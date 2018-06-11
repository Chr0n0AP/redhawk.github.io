---
title: "Example Sandbox Interaction"
weight: 40
---

The code below provides an example of component interaction in the Sandbox:

```py
>>> my_comp = sb.launch("<component name>")
>>> my_comp
<local component '<component name>_1' at 0x<hex address>>
>>> my_comp.api()
Component [example]:
Provides (Input) Ports ==============
Port Name       Port Interface
---------       --------------
input_s IDL:BULKIO/dataShort:1.0


Uses (Output) Ports ==============
Port Name       Port Interface
---------       --------------
output_s        IDL:BULKIO/dataShort:1.0


Properties ==============
Property Name   (Data Type)     [Default Value] Current Value
-------------   -----------     --------------- -------------
my_float        (float/SF/32f)  [None]                  None
my_string       (string)        [None]                  None
some_shorts     (ShortSeq)      [None]                  None
>>> my_comp.my_float
>>> my_comp.my_float = 5.0
>>> my_comp.my_float
5.0
>>> my_comp.api()
Component [example]:
Provides (Input) Ports ==============
Port Name       Port Interface
---------       --------------
input_s IDL:BULKIO/dataShort:1.0


Uses (Output) Ports ==============
Port Name       Port Interface
---------       --------------
output_s        IDL:BULKIO/dataShort:1.0


Properties ==============
Property Name   (Data Type)     [Default Value] Current Value
-------------   -----------     --------------- -------------
my_float        (float/SF/32f)  [None]                  5.0
my_string       (string)        [None]                  None
some_shorts     (ShortSeq)      [None]                  None
```

#### Connecting Components

Connecting components is done by invoking a `connect()` function on the uses-side (output-side) component with the provides-side (input-side) component as the argument of the call.


```py
>>> another_comp = sb.launch("repeater")
>>> my_comp.connect(another_comp)
True
```

{{% notice note %}}
If the connections are ambiguous (multiple uses ports or multiple provides ports have matching types), an error occurs. To resolve the ambiguity, `usesPortName` and/or `providesPortName` must be specified as arguments to the function. For example, the following call specifies `providesPortName` as an argument.
{{% /notice %}}

```py
>>> my_comp.connect(another_comp, providesPortName="float_in_1")
```

#### Connection Manager

A REDHAWK DomainManager contains a Connection Manager that provides systemic benefits for the management of connections between endpoints that can come and go. The underlying endpoints are specialized data strutures and CORBA references required to complete the connection, which complicates the creation of endpoints. The Python Sandbox already contains Pythonic representations of these Domain objects, which reduce the need to retrieve the CORBA references. The Python Sandbox contains helpers that use these representations for the creation of endpoints.

```py
>>> from ossie.utils import rhconnection
>>> dom = redhawk.attach()
>>> app = dom.createApplication('/waveforms/my_app/my_app.sad.xml')
>>> dev = dom.devices[0]
>>> uses = rhconnection.makeEndPoint(app, 'out_portname')
>>> prov = rhconnection.makeEndPoint(dev, 'in_portname')
>>> dom.getConnectionMgr().connect(uses,prov,'user_id','connection_id')
```

#### Sending and Receiving Data

Multiple [helpers]({{< relref "manual/sandbox/python/sources-and-sinks.md" >}}) are available in the Sandbox that can be connected to running components and devices.

#### Setting Component Log Levels

The log level of the component may be set using the `execparams` argument in the component constructor.

```py
>> myComponent = sb.launch("<component name>", execparams={"DEBUG_LEVEL":1})
```