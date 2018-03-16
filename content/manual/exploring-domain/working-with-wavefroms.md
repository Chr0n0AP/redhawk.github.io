---
title: "Working with Waveforms on a Running Domain"
weight: 30
---

If you have a running Domain Manager and Device Manager, you may create and work with [waveforms]({{< relref "manual/waveforms/_index.md" >}}).  You can launch the waveform on the domain, launch additional components into the running waveform, stop the running waveform, and release the waveform from the domain.

### Launching a Waveform

To launch a waveform:

1.  Right-click the domain and from the domain context menu, select **Launch Waveform…**:

    ##### Domain Context Menu
    ![Domain context menu](../images/DomainContextMenu.png)

    The **Launch Waveform** wizard is displayed:

    ##### Launch Waveform Wizard
    ![Launch Waveform wizard](../images/REDHAWK_Launch_Waveform_p1.png)

2.  On the **Select a Waveform** page of the **Launch Waveform** wizard, perform the following procedure:

    1.  Select the waveform to launch.

    2.  Select the **Start the waveform after launching** checkbox to start the waveform and all of its contained components immediately after launch.

    3.  Click **Next**.

    The **Assign Initial Properties** page is displayed.

    ##### Assign Initial Properties
    ![Assign Initial Properties page](../images/assignproperties.png)

3.  On the **Assign Initial Properties** page, set the properties of the components within the waveform. Any property modified here is specific to this waveform and does not impact the component‘s execution in other environments. As the properties are changed from their default values, the now non-default values appear in bold as shown below:

    ##### Application Properties: Non-Default
    ![Application Properties turn bold when non-default values are set](../images/REDHAWK_Launch_Waveform_p2b.png)


    When you are finished assigning properties, click **Next**. The **Assign Components to Devices** page is displayed:

    ##### Assign Components to Devices
    ![Assign Components to Devices page](../images/assigncomps.png)

4.  The **Assign Components to Devices** page enables you to specify what executable device on which each of the components launches. If the device setting is **Auto**, REDHAWK determines the executable device based on any allocation properties and dependencies set on the components and devices.

5.  To launch the waveform, click **Finish**.

    {{% notice tip %}}
In the REDHAWK Explorer View, the Domain Manager now displays the launched waveform within the **Waveforms** folder.
    {{% /notice %}}

    {{% notice note %}}
If you did not select the **Start the waveform after launching** checkbox, the waveform has not been started. To start the waveform, in the **REDHAWK Explorer View**, right-click the waveform from the domain’s waveforms folder and select **Start**.
    {{% /notice %}}

    Once the waveform is started, the **REDHAWK Explorer View** indicates that the waveform and components within the waveform are in the started state by displaying **STARTED** next to the waveform’s instance and the instance of each component:

    ##### Started Waveform
    ![Started Waveform](../images/startedwave.png)

### Launching Additional Components into a Running Waveform

Once a waveform has been started, additional components may be launched into the running waveform. These additional components run on the local machine and not on the domain.

1.  In the **REDHAWK Explorer View**, right-click the waveform and select **Open With > Chalkboard**:

    ##### Opening a Running Waveform in the Chalkboard
    ![Opening a running Waveform in the Chalkboard](../images/runninglaunch.png)

    This displays the running waveform in the Chalkboard:

    ##### Running Waveform in the Chalkboard
    ![Running Waveform in the Chalkboard](../images/runningchalk.png)

2.  From the Palette, add additional components to the waveform.

    {{% notice note %}}
Standard runtime actions (**Plot**, **Start**, **Stop**, **Terminate**, and **Connect**) are available on the newly added components. These components are added only to the currently running instance of the waveform and are launched on the local machine, **NOT** in the domain.
    {{% /notice %}}

### Stopping a Waveform

To stop a running waveform but keep it on the domain, in the **REDHAWK Explorer View**, right-click the running waveform and select **Stop** from the context menu:

##### Stopping a Waveform
![Stopping a Waveform](../images/stopwave.png)

### Releasing a Waveform

To stop a running waveform and release it from the domain, in the **REDHAWK Explorer View**, right-click the running waveform and select **Release** from the context menu:

##### Releasing a Waveform
![Releasing a Waveform](../images/releasewave.png)

{{% notice tip %}}
It is not necessary to select **Stop** prior to releasing the waveform. The IDE stops the waveform before releasing it.
{{% /notice %}}
