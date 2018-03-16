---
title: "Dynamic Connections"
weight: 40
---

Unless a component is in the process of being terminated, it is valid to retrieve a port reference at any other point in the component’s life cycle. Anyone may call `getPort()` on the component at any time. In the case of a uses port, anyone may call `connectPort()` or `disconnectPort()` at any time. In the case of a provides port, anyone may cast to that port reference and start making calls on it. It is the task of the component developer to make sure that the component handles changes like this smoothly. The base classes and code generators provided with REDHAWK handle the vast majority of the issues arising from this change, especially when the provides port implements one of the REDHAWK standard interfaces.

This dynamic connection behavior provides huge benefits to an application developer. For example, if one wanted to inspect the data being passed from one component to the next, a temporary provides-side implementation can be created and a new connection established. The standard behavior of a uses port is to send the same data to all its existing connections. This means of dynamic connecting is essential for REDHAWK’s plotting mechanism.
