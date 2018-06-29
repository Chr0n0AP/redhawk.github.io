---
title: "Working with Components, Devices, and Services"
weight: 20
---

The Sandbox contains the following commands for working with components, devices, and services:

  - The `show()` Command
  - The `catalog()` Command
  - The `api()` Method
  - The `launch()` Command

The `show()` command displays running components, connections between components, and the `SDRROOT`:

```py
>>> sb.show()
```

The `catalog()` command displays which components, devices, and services are available in `SDRROOT`. To determine what types are displayed, use the `objType` argument (by default `objType="components"`) as shown below:

```py
>>> sb.catalog()
>>> sb.catalog(objType="devices")
>>> sb.catalog(objType="services")
```
An alternative to `catalog()` is `browse()`. The function `browse()` provides formatted human-readable text that describes the items that are installed on the system rather than the computer-friendly list that `catalog()` provides.

The `api()` method displays the ports and properties for a running component:

```py
>>> comp.api()
Component [FloatToShort]:
Provides (Input) Ports ==============
Port Name       Port Interface
---------       --------------
float_in        IDL:BULKIO/dataFloat:1.0

Uses (Output) Ports ==============
Port Name       Port Interface
---------       --------------
short_out       IDL:BULKIO/dataShort:1.0

Properties ==============
Property Name   (Data Type)     [Default Value] Current Value
-------------   -----------     --------------- -------------
max_value       (float/SF/32f)  1.0             1.0
min_value       (float/SF/32f)  -1.0            -1.0
```

The `launch()` command launches components, devices, and services. The first argument identifies the object to launch. It may be either a path to an SPD file or, for objects installed to the current `SDRROOT`, the name of the object as given in the SPD. The path can be absolute or relative and does not need to reside in `SDRROOT`.

The following example demonstrates how to launch a device from the current working directory using the path:

```py
>>> my_dev = sb.launch("./MyDevice.spd.xml")
```

The following example demonstrates how to launch a device named `SigGen` from the current working directory using the name:

```py
>>> my_dev = sb.launch("rh.SigGen")
```

A component's properties, either normal or with the `commandline` attribute, may be overridden at launch time by passing a dictionary of property IDs and values to the keyword argument `properties`. These values override the default values listed in the PRF file.

```py
siggen = sb.launch("rh.SigGen", properties={"sample_rate":100e3,
                                            "frequency":22e3,
                                            "shape":"square"})
```

By default, the Sandbox launches the first component implementation with an entry point that exists on the file system. A particular implementation may be specified by passing the implementation ID to the `impl` argument:

```py
>>> siggen = sb.launch("rh.SigGen", impl="cpp")
```

The Sandbox includes limited support for attaching a debugger to a component process. The debugger console opens in a new XTerm window to allow continued interaction on the Sandbox console.

In the case of C++, to launch a component and attach `gdb` to the process, enter the following command:

```py
>>> my_comp = sb.launch("./MyComponent.spd.xml", debugger="gdb")
```

 The component and `gdb` are run in separate processes. Exiting gdb closes the window, but the component continues to function.

 The debugger argument also supports `jdb` (Java), `pdb` (Python), and `valgrind` (Valgrind, a tool used for diagnostics such as memory leak detection).

 To provide arguments to the supported debuggers, the debugger needs to be instantiated outside the scope of the launch function. For example, to perform a full leak check using Valgrind, use the following argument:

 ```py
 >>> from ossie.utils.sandbox.debugger import GDB, JDB, PDB, Valgrind
 >>> vg_option = {'--leak-check':'full'}
 >>> vg = Valgrind(**vg_option)
 >>> my_comp = sb.launch("./MyComponent.spd.xml", debugger=vg)
 ```

 If incorrect arguments are passed, the component fails to deploy. Note that the Python debugger does not take arguments.

#### Properties

In addition to the standard REDHAWK `query()` and `configure()` functions, the Sandbox presents a simplified interface to properties for components and devices. Properties can be accessed as attributes on the component object:

```py
>>> my_comp.string_prop = "Hello World!"
>>> my_comp.long_prop
1
```

Property names are taken from the component PRF file, with any characters that are invalid for Python identifiers replaced by an underscore.

The current value of properties with a mode of “readonly” or “readwrite” may be inspected. Properties with a mode of “readwrite” or “writeonly” can be assigned a new value.

To view the properties that are available for a given component, along with their types and current and default values, use the `api()` function.

Simple properties with numeric types can be assigned from any Python numeric type:

```py
>>> my_comp.float_prop = 3
>>> my_comp.long_prop = 1.0e3
```

The value is range checked and coerced into the desired type before being configured on the component:

```py
>>> my_comp.ushort_prop = -1
ossie.utils.type_helpers.OutOfRangeException: ...
```

Floating point values are truncated, not rounded, during conversion to integer types:

```py
>>> my_comp.long_value = 1.5
>>> my_comp.long_value
1
```

A simple property with a complex value type can be assigned from a Python complex or two-item sequence. The numeric conversion of the real and imaginary components is identical to that of single numeric values.

```py
>>> my_comp.complex_prop = 1.0+2.5j
>>> my_comp.complex_prop = (1, 2)
```

Complex properties support assignment from single numeric values; the imaginary component is assumed to be 0.

```py
>>> my_comp.complex_prop = 1
>>> my_comp.complex_prop
1+0j
```

Properties that have enumerated values in the component’s PRF support assignment using the enumerated name as a Python string:

```py
>>> siggen.shape = "triangle"
```

Struct properties can be set with a Python dictionary:

```py
>>> my_comp.struct_prop = {"item_string": "value",
...                        "item_long": 100}
```

The dictionary keys are the IDs of the simple properties that make up the struct. Each value is converted to the appropriate type following the same rules as simple properties. Any struct members that are not in the dictionary retain their current values.

Individual struct members may be set directly, using the simple property name:

```py
>>> my_comp.struct_prop.item_string = "new value"
```

{{% notice note %}}
Properties have a mode (readwrite, readonly, writeonly), and for compatibility reasons, the mode is a member of the Python Struct property container and cannot change. If the Struct property has a member called “mode”, requesting the member “mode” from the property will return its access mode rather than the content of the property member. Access the value of any element of a property with a reserved word as its name as follows:
```py
>>> my_comp.struct_prop["mode"] = "Hello World!"
```
{{% /notice %}}

{{% notice note %}}
Setting a struct member as an attribute uses the simple property’s name, while setting the member via a dictionary uses the simple property’s id.
{{% /notice %}}

Both simple and struct sequence properties may be manipulated as lists. Sequence properties support the common Python list operations, such as slicing and in-place modifiers:

```py
>>> my_comp.long_seq = [1, 2, 3, 4]
>>> my_comp.long_seq[2:]
[3, 4]
>>> my_comp.long_seq.append(5)
>>> my_comp.long_seq
[1, 2, 3, 4, 5]
>>> my_comp.long_seq[2:4] = [6]
>>> my_comp.long_seq
[1, 2, 6, 5]
```

The items of simple sequences follow the same conversion rules as the corresponding simple property:

```py
>>> my_comp.long_seq = [1.5, 2.5, 3.5]
>>> my_comp.long_seq
[1, 2, 3]
```

Each item in a struct sequence works identically to a single struct property:

```py
>>> my_comp.struct_seq
[{'a':'first', 'b':1}, {'a':'second', 'b':2}]
>>> my_comp.struct_seq[0].a = "new value"
>>> my_comp.struct_seq[1] = {"a":"third", "b":3}
>>> my_comp.struct_seq
[{'a':'new value', 'b':1}, {'a':'third', 'b':3}]
```

The Sandbox generates a low-level CORBA `configure()` call each time a property value is set. However, Sandbox components also support setting multiple property values at once using a Python dictionary:

```py
>>> my_comp.configure({"long_prop":1, "string_prop":"new value"})
```

The keys may be either the property names or ids. The values are converted in the same manner as setting the individual property directly.

#### Property Listener

 It is possible to asynchronously listen to changes in properties such that it is not necessary to poll the component to see the state of a particular property. This is done through property change listeners. To implement this listener, create a property change listener and register it with the component. Note that the listener can be a local object or an eventchannel

 ```py
 >>> def property_change_callback(self, event_id, registration_id, resource_id, properties, timestamp):
         print event_id, registration_id, resource_id, properties, timestamp
 >>> listener = sb.PropertyChangeListener(changeCallbacks={'prop_1':callback_fn})
 >>> comp.registerPropertyListener(listener, ['prop_1'], 0.5)  # check the property every 0.5 seconds
 ```
