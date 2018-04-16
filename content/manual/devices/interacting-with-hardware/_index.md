---
title: "Using Devices to Interact with Hardware"
weight: 10
---

REDHAWK devices can be used to interact with hardware receivers and digitizers, for example, a data acquisition board or a USRP. To have a REDHAWK component use a REDHAWK device, you can establish a [*usesdevice* relationship]({{< relref "manual/runtime-environment/applications.md#usesdevice-relationship" >}}) between a component and a device. The *usesdevice* relationship requires the component to only use that particular type of device . The REDHAWK distribution includes three devices that can be used out-of-the-box: Realtek RTL2832-based DVB dongles with Osmocom’s `rtl-sdr` library, Ettus’s USRP, and an FM RDS simulator with the `libRfSimulators` library.

The following example explains how to use the `FmRdsSimulator` REDHAWK device to receive and process a generated FM signal, and provide the audio and a plot of the signal.

{{% notice note %}}
The `RTL2832U` REDHAWK device can be substituted for the `FmRdsSimulator` REDHAWK device to receive a live FM signal.
{{% /notice %}}

1.  Open the Python Sandbox and import the `frontend` module.

    ```py
    >>> from ossie.utils import sb
    >>> import frontend
    ```

2.  Launch the `FmRdsSimulator` device, the `AmFmPmBasebandDemod` component, the `fastfilter` component, the `ArbitraryRateResampler` component, the `SoundSink` Sandbox component, and set up the REDHAWK IDE plotter.

    {{% notice note %}}
To plot data, the REDHAWK IDE must be installed and the path to the Eclipse directory of the installed IDE must be specified in the Sandbox. This can be done through the `IDELocation()` function.
    {{% /notice %}}

    ```py
    >>> sim = sb.launch("rh.FmRdsSimulator")
    >>> demod=sb.launch("rh.AmFmPmBasebandDemod")
    >>> filter=sb.launch("rh.fastfilter")
    >>> resample=sb.launch("rh.ArbitraryRateResampler")
    >>> agc=sb.launch("rh.agc")
    >>> sink=sb.SoundSink()
    >>> sb.IDELocation("/path/to/ide/eclipse/directory")
    >>> plot=sb.Plot()
    ```

3.  Connect devices and components.

    ```py
    >>> sim.connect(demod)
    >>> demod.connect(filter, usesPortName="fm_dataFloat_out")
    >>> filter.connect(resample)
    >>> resample.connect(agc)
    >>> agc.connect(sink)
    >>> agc.connect(plot)
    ```

4.  Configure and start all components and devices in the Sandbox.

    ```py
    >>> sim.addAWGN=False
    >>> demod.freqDeviation=15000.0
    >>> filter.filterProps.freq1=16000.0
    >>> filter.filterProps.Ripple=0.5
    >>> filter.filterProps.Type="lowpass"
    >>> resample.outputRate=32000.0
    >>> sb.start()
    ```

5.  Create a tuner allocation structure and allocate the device.

    ```py
    >>> alloc = frontend.createTunerAllocation("RX_DIGITIZER", allocation_id="testing", center_frequency=100.1e6, sample_rate=256e3,sample_rate_tolerance=20.0)
    >>> sim.allocateCapacity(alloc)
    ```

    {{% notice note %}}
Set the `center_frequency` property in the allocation structure to the radio station desired. Specifying a `bandwidth` of 0.0 indicates that any bandwidth is acceptable given that the other parameters are met.
    {{% /notice %}}

Devices may be used within the Sandbox or within a domain, in which case devices are deployed by a Device Manager at startup. The lifecycles of the devices used within the Sandbox follow the same lifecycle as the scripting environment; when the scripting environment is shutdown, the devices are shutdown. The lifecycles of devices deployed by a Device Manager follow the lifecycle of the Device Manager; when a Device Manager is started, the devices associated with that Device Manager are launched, and when the Device Manager is shutdown, the associated devices are released.

The configuration of a Device Manager’s Devices is controlled through an XML file called a Device Configuration Descriptor (DCD) file. Any one DCD file associated with a Device Manager instance is often referred to as a node.

When a node is deployed, the devices associated with the node become available to the domain. Applications can contain *usesdevice* relationships. In other words, elements in the SAD XML file can declare that the application requires the use of a particular type of device. When the application is deployed by the domain, the domain searches through the deployed devices for any one device that can satisfy the declared dependency. Any device that satisfies an application dependency may become busy and unavailable for use by other applications. When the application is released, the device is brought back into the pool of available devices.

Given the radio nature of REDHAWK, the interaction between the REDHAWK environment and RF devices has been standardized through a common API, known as FrontEnd Interfaces (FEI).

If you have RF FrontEnd hardware that you want to model in REDHAWK, you can use the FEI module to facilitate this process. The FEI module contains interfaces designed to standardize the interaction (allocation, operation, and development) of tuner devices within the REDHAWK Core Framework (between applications and radio hardware). This standard breaks the tie between the application and the hardware and provides more flexibility.

The FEI module defines a number of interfaces to enable users to interact with several different generic [types of tuners]({{< relref "manual/appendices/fei.md#types-of-tuners" >}}), including:

  - Receiver (RX) tuner
  - Receiver Digitizer (RX_DIGITIZER) tuner
  - Channelizer (CHANNELIZER) tuner
  - Digital Down Converter (DDC) tuner
  - Receiver Digitizer Channelizer (RX_DIGITIZER_CHANNELIZER) tuner
  - Transmitter (TX) tuner
  - Receiver With Scanning Capability (RX_SCANNER_DIGITIZER) tuner

Before you can interact with the tuners, you must create a REDHAWK device that is FEI compliant. The following procedure explains how to make a REDHAWK device that is compliant with FEI version 2.4.
