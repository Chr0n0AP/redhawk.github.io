---
title: "Working with Events"
weight: 80
---

In addition to using message event Properties and message Ports, the REDHAWK library enables developers to interface with Event Channels to send and receive non-REDHAWK structured messages using the CORBA Any object. The library provides both Publisher and Subscriber interfaces for sending and receiving data. The libraries make use of existing marshaling and unmarshaling support for simple data types (i.e., int, float, string, etc.), REDHAWK Core Framework event messages, and defined structured messages used by your Component. For custom structured data, it is the developer’s responsibility to implement the marshaling and unmarshaling methods into and out of a CORBA Any or serialize the data structure into string type that can be marshaled. This API is considered an advanced topic to support custom behavior using CORBA’s Event Channels.

### Publisher Support

To publish data to an Event Channel, the Publisher role provides the following method:

  - `push` - Accepts data to forward to an Event Channel. For C++, structured data types require the overload of "operator<<=". For Python and Java, structured data should be marshaled into the CORBA Any object.

#### C++ Example of Publisher

##### Component.h
```c++
struct MsgData {
   int id;
   std::string data;
};

redhawk::events::ManagerPtr   ecm;
redhawk::events::PublisherPtr pub;
```

##### `Component.cpp`

```c++
Component::constructor {

   // get access to the Event Channel Manager
   ecm = redhawk::events::GetManager( this );

   // request a Publisher object for an Event Channel
   if ( ecm ) {
       pub = ecm->Publisher("test1");
   }
}

// required to marshall data into the CORBA::Any
void operator<<=( CORBA::Any &any, const MsgData &msg ) {
   // marshall MsgData to Any
};

Component::serviceFunction {

  // create a structured message
  MsgData  my_msg = generateMsg();

  if ( pub ) {
    pub->push( my_msg);
  }
  // or simple text based messages
  if ( pub )  pub->push( "simple message to send" );
}
```

#### Python Example of Publisher

##### Component.py
```py
from omniORB import CORBA, any
from ossie.events import Manager

class MsgData(object):
  def __init__(self):
      self.id = 0;
      self.data = ""

  # convert message to a dictionary
  def encode(self):
      return { 'id', : self.id, 'data' : self.data }

class Component:

  def constructor(self):
      # get access to the Event Channel Manager
      self.ecm = Manager.GetManager( self );
      if self.ecm:
         # request a Publisher object for an Event Channel
         self.pub = self.ecm.Publisher("test1");

  def process(self):
      msgdata = self.generateMsgData()
      if self.pub:
          self.pub.push( msgdata.encode() )
       // or simple text based messages
       if self.pub : self.pub.push( "simple text message");
```

#### Java Example of Publisher

##### Component.java

```java
// add required imports
import org.omg.CORBA.ORB;
import org.ossie.events.Manager;
import org.ossie.events.Publisher;

// add members to your component's class
private org.ossie.events.Manager   ecm;
private org.ossie.events.Publisher pub;

 public class MsgData {
     public int  id;
     public String data;
 }


 public org.omg.CORBA.TypeCode getType () {
     return  org.omg.CORBA.ORB.init().create_interface_tc("IDL:MsgData/MsgData:1.0", "MsgData");
 }

 public void constructor() {
     try {
      	this.ecm = Manager.GetManager( this );
        try {
              if (this.pub == null ) {
                this.pub = ecm.Publisher("test1");
        }
        catch( org.ossie.events.Manager.RegistrationFailed e) {
              //  handle registration error
        }
        catch( org.ossie.events.Manager.RegistrationExists e) {
              // handle registration error
        }
      }
      catch( org.ossie.events.Manager.OperationFailed e) {
            logger.error( e.getMessage());
      }
  }

 public void encodeMsg(Any a, MsgData msg)
 {
   // perform encoding of msg to  CORBA any,
 }

 protected int serviceFunction() {
      MsgData msg = generateMsg();
      if  ( this.pub != null ) {
          try {
             this.pub.push( encodeMsg( msg )  );
          }
          catch( Exception ex ) {
              // handle exception
          }
      }
}
```

### Subscriber Support

The Subscriber role provides two modes (polling vs callback), for receiving data from an Event Channel. Both methods require the developer to unmarshal data from a CORBA Any. For C++, structured data types require the overload of "operator>>=". For Python and Java, structured data should be unmarshaled from the CORBA Any object.

  - `getData` - (polling) Grab a message from the Event Channel. If no messages are available then return -1. For Python, a CORBA Any is returned or None for no messages available.

  - `callback` - Provide a callback to the Subscriber object. As data arrives from the Event Channel, this callback is notified.

#### Example of C++ Subscriber Role Using `getData` Method

##### Component.h Polling Example

```c++
redhawk::events::ManagerPtr    ecm;
redhawk::events::SubscriberPtr sub;

struct MsgData {
   int id;
   std::string data;
};
```

##### Component.cpp Polling Example

```c++
// required to unmarshall data from the CORBA::Any
bool operator>>=( const CORBA::Any &any, const MsgData *&msg ) {
   //unmarshall Any into MsgData
};

Component::constructor {
   // get access to the Event Channel Manager
   ecm = redhawk::events::GetManager( this );

   // request a Subscriber object for an Event Channel
   if ( ecm ) {
      sub = ecm->Subscriber("test1");
   }
}

Component::serviceFunction {
 MsgData msgin;
	 if ( sub && sub->getData( msgin ) == 0 ) {
  RH_NL_INFO("mylogger", "Received msg =" << msgin.id);
 }
}
```

#### Example of C++ Subscriber Role Using `callback` Method

##### Component.h Callback Example
```c++
redhawk::events::ManagerPtr    ecm;
redhawk::events::SubscriberPtr sub;

struct MsgData {
   int id;
   std::string data;
};

void  my_msg_cb( const CORBA::Any &data );
```

##### Component.cpp Callback Example
```c++
// required to unmarshall data from the CORBA::Any
bool operator>>=( const CORBA::Any &any, const MsgData *&msg ) {
   //unmarshall Any into MsgData
};

void Component::my_msg_cb( const CORBA::Any &data ) {
 // structure msg
 MsgData msg;
 if ( data >>= msg ) {
   LOG_INFO( Component, "Received message " << msg.id );
 }
}

Component::constructor {
   // get access to the Event Channel Manager
   ecm = redhawk::events::GetManager( this );

   // request a Subscriber object for an Event Channel
   if ( ecm ) {
      sub = ecm->Subscriber("test1");
      sub->setDataArrivedListener( this , &Component::my_msg_cb );
   }
}
```

#### Example of Python Subscriber Role Using `getData` Method

##### Component.py Polling Example
```py
from ossie.events import Manager
from omniORB import CORBA, any

class MsgData(object):
    def __init__(self):
        self.id = 0
        self.data = ""

    def constructor(self):
        # get access to the Event Channel Manager
        self.ecm = Manager.GetManager( self )
        if self.ecm:
            # request a Subscriber object for an Event Channel
            self.sub = self.ecm.Subscriber("test1")

    def decodeMsg(self, raw ):
        # unpack message back into
        mdict = any.from_any(raw)
        ret = MsgData()
        ret.id = mdict['id']
        ret.data = mdict['data']
        return ret

    def process(self):
        if self.sub:
            raw = self.sub.getData()
            if raw:
                msgin = self.decodeMsg(raw)
                self._log.info("Received message  = " +str(msgin.id))
```

#### Example of Python Subscriber Role Using `callback` Method

##### Component.py Callback Example
```py
# Callback snippet example, define in Component.py
from ossie.events import Manager
from omniORB import CORBA, any

class MsgData(object):
    def __init__(self):
        self.id = 0
        self.data = ""


    def msg_cb(self, data):
        if data:
            # unpack message back into
            mdict = any.from_any(data)
            msg = MsgData()
            msg.id = mdict['id']
            msg.data = mdict['data']
            # do something with msg

    def constructor(self):
        # get access to the Event Channel Manager
        self.ecm = Manager.GetManager( self );
        if self.ecm:
            # request a Subscriber object for an Event Channel
            self.sub = self.ecm.Subscriber("test1")
            self.sub.setDataArrivedCB( self.msg_cb )
```

#### Example of Java Subscriber Role Using `getData` Method

##### Component.java Polling Example
```java
  // add required imports
  import org.omg.CORBA.Any;
  import org.omg.CORBA.ORB;
  import org.ossie.events.Manager;
  import org.ossie.events.Subscriber;

  // add members to your component's class
  private org.ossie.events.Manager    ecm;
  private org.ossie.events.Subscriber sub;

   public class MsgData {
       public int  id;
       public String data;
   }

   public org.omg.CORBA.TypeCode getType () {
       return  org.omg.CORBA.ORB.init().create_interface_tc("IDL:MsgData/MsgData:1.0", "MsgData");
   }

   public void constructor() {
     try {
     	  this.ecm = Manager.GetManager( this );
       	try {
            if ( this.sub == null ) {
               this.sub  = ecm.Subscriber("test1");
            }
        } catch( org.ossie.events.Manager.RegistrationFailed e) {
             // handle exceptions
        } catch( org.ossie.events.Manager.RegistrationExists e) {
             // handle exceptions
        }
     } catch( org.ossie.events.Manager.OperationFailed e) {
            // handle exceptions
     }
   }

   public void decodeMsg( org.omg.CORBA.Any any, MsgData msg ) {
       // decode message from CORBA.Any;
   }

   protected int serviceFunction() {
     if  ( this.sub != null ) {
      	if ( this.sub.getData( any ) == 0 ) {
            MsgData msg = new MsgData();
            decodeMsg( any, msg );
       	    logger.info("Received message = " + msg.id);
        }
     }

    }
```

#### Example of Java Subscriber Role Using `callback` Method

##### Component.java Callback Example
```java
// add required imports
import org.omg.CORBA.Any;
import org.omg.CORBA.ORB;
import org.ossie.events.Manager;
import org.ossie.events.Subscriber;
import org.ossie.events.Subscriber.DataArrivedListener;

// add members to your component's class
private org.ossie.events.Manager    ecm;
private org.ossie.events.Subscriber sub;

public class MsgData {
   public int  id;
   public String data;
}

public class MsgArrived  implements DataArrivedListener {
  public Component parent=null;

  public MsgArrived ( Component inParent ) {
       parent = inParent;
  }

  public void processData( final Any data ) {
      // decode message from CORBA.Any;
       // do something with the message
  }
}

public org.omg.CORBA.TypeCode getType () {
    return  org.omg.CORBA.ORB.init().create_interface_tc("IDL:MsgData/MsgData:1.0", "MsgData");
}

public void constructor() {
    try {
     	  this.ecm = Manager.GetManager( this );
       	try {
            if ( this.sub == null ) {
               this.sub  = ecm.Subscriber("test1");
               this.my_ch = new MsgArrived(this);
               this.sub.setDataArrivedListener(this.my_cb );
            }
         } catch( org.ossie.events.Manager.RegistrationFailed e) {
               // handle exceptions
         } catch( org.ossie.events.Manager.RegistrationExists e) {
               // handle exceptions
         }
     } catch( org.ossie.events.Manager.OperationFailed e) {
            // handle exceptions
     }
   }
```
