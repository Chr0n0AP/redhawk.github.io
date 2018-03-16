---
title: "Logging"
weight: 130
---

REDHAWK provides a logging capability for use by all resources (components, devices, and services). By default, the REDHAWK Framework and its support libraries have been developed to take advantage of the underlying language’s implementation: log4cxx, log4j, and log4py for C++, Java, and Python, respectively. All three libraries follow the basic premise of the log4j capabilities. A root logger definition is the parent of all loggers and configuration of the loggers is controlled through a Java style properties file. Each REDHAWK resource creates a named logger as a child of the root logger. (The name is based on the underlying resource’s name.) This chapter explains how to configure and use the logging capability. For a detailed description of log4j and its capabilities, refer to the log4j website (<http://logging.apache.org/log4j/1.2/>).

All REDHAWK-generated code for resources provides an instantiation of a logging object for use by the resource. For Java and Python implementations, the underlying logging implementation of the language is used. For C++, there is a set of macro definitions that enable a resource to use the underlying logging implementation associated with the REDHAWK Core Framework library.

{{% children depth="999" %}}
