---
title: Investigate devices in the OT sensor or on-premises management console device map
description: Learn how to use the a device map on an OT sensor or an on-premises management console, which provides a graphical representation of devices and the connections between them.
ms.date: 01/25/2023
ms.topic: how-to
---

# Investigate devices on a device map

OT device maps provide a graphic representation of the network devices detected by the OT network sensor and the connections between them.

Use a device map to retrieve, analyze, and manage device information, either all at once or by network segment, such as specific interest groups or Purdue layers. If you're working in an air-gapped environment with an on-premises management console, use a *zone map* to view devices across all connected OT sensors in a specific zone.

## Prerequisites

To perform the procedures in this article, make sure that you have:

- An OT network sensor [installed](ot-deploy/install-software-ot-sensor.md), [activated, and configured](ot-deploy/activate-deploy-sensor.md), with network traffic ingested

- To view devices across multiple sensors in a zone, an on-premises management console [installed](ot-deploy/install-software-on-premises-management-console.md), [activated, and configured](ot-deploy/activate-deploy-management.md), with multiple sensors [connected](ot-deploy/connect-sensors-to-management.md) and [assigned to sites and zones](ot-deploy/sites-and-zones-on-premises.md).

- Access to your OT sensor or on-premises management console. Users with the **Viewer** role can view data on the map. To import or export data or edit the map view, you'll need access as a **Security Analyst** or **Admin** user. For more information, see [On-premises users and roles for OT monitoring with Defender for IoT](roles-on-premises.md).

## View devices on OT sensor device map

1. Sign into your OT sensor and select **Device map**. All devices detected by the OT sensor are displayed by default according to [Purdue layer](best-practices/understand-network-architecture.md#sensor-placement-in-your-networking-architecture).

    On the OT sensor's device map:

    - Devices with currently active alerts are highlighted in red
    - Starred devices are those that had been marked as important
    - Devices with no alerts are shown in black, or grey in the zoomed-in, connections view

1. Zoom in and select a specific device to view the connections between it and other devices, highlighted in blue.

    When zoomed in, each device shows the following details:

    - The device's host name, IP address, and subnet address, if relevant.
    - The number of currently active alerts on the device.
    - The device type, represented by a various icons.
    - The number of devices grouped in a subnet in an IT network, if relevant. This number of devices is shown in a black circle.
    - If the device is newly detected or unauthorized.

1. Right-click a specific device and select **View properties** to drill down further to a [device details page](how-to-investigate-sensor-detections-in-a-device-inventory.md#view-device-details). <!--validate this step-->

### Modify the OT sensor map display

Use any of the following map tools to modify the data shown and how it's displayed:

|Name  |Description  |
|---------|---------|
|**Refresh map**     | Select to refresh the map with updated data. <!--how often does this refresh automatically?-->        |
| **Notifications** | Select to view [device notifications](#manage-device-notifications-from-an-ot-sensor-device-map). |
|**Search by IP / MAC**     | Filter the map to display only devices connected to a specific IP or MAC address.       |
|**Multicast/broadcast**     | Select to edit the filter that shows or hides multicast and broadcast devices.    By default, multicast and broadcast traffic is hidden.      |
|**Add filter**  (Last seen)   | Select to filter devices displayed by those shown in a specific time period, from the last five minutes to the last seven days.       |
|**Reset filters**     |   Select to reset the *Last seen* filter.      |
|**Highlight**     | Select to highlight the devices in a specific [device group](device-inventory.md#built-in-device-groups). Highlighted devices are shown on the map in blue. <br><br>Use the **Search groups** box to search for device groups to highlight, or expand your group options, and then select the group you want to highlight.       |
|**Filter**     |  Select to filter the map to show only the devices in a specific [device group](device-inventory.md#built-in-device-groups). <br><br>Use the **Search groups** box to search for device groups, or expand your group options, and then select the group you want to filter by.        |
| **Zoom** <br>:::image type="icon" source="media/how-to-work-with-maps/zoom-in-icon-v2.png" border="false"::: / :::image type="icon" source="media/how-to-work-with-maps/zoom-out-icon-v2.png"  border="false"::: | Zoom in on the map to view the connections between each device, either using the mouse or the **+**/**-** buttons on the right of the map. |
| **Fit to screen** <br>:::image type="icon" source="media/how-to-work-with-maps/fit-to-screen-icon.png" border="false":::    |  Zooms out to fit all devices on the screen      |
|**Fit to selection**<br>:::image type="icon" source="media/how-to-work-with-maps/fit-to-selection-icon.png" border="false":::     |  Zooms out enough to fit all selected devices on the screen      |
|**IT/OT Presentation Options** <br> :::image type="icon" source="media/how-to-work-with-maps/collapse-view-icon.png" border="false":::    |Select **Disable Display IT Networks Groups** to prevent the ability to [collapse subnets](#view-it-subnets-from-an-ot-sensor-device-map) in the map. This option is selected on by default.        |
|**Layout options** <br>:::image type="icon" source="media/how-to-work-with-maps/layouts-icon-v2.png" border="false":::    |  Select one of the following: <br>- **Pin layout**. Select to save device locations if you've dragged them to new places on the map. <br />- **Layout by connection**. Select to view devices organized by their connections. <br />- **Layout by Purdue**. Select to view devices organized by their Purdue layers.        |

To see device device details, select a device and expand the device details pane on the right. In a device details pane:

- Select **Activity Report** to jump to the device's [data mining report](how-to-create-data-mining-queries.md)
- Select **Event Timeline** to jump to the device's [event timeline](how-to-track-sensor-activity.md)
- Select **Device Details** to jump to a full [device details page](how-to-investigate-sensor-detections-in-a-device-inventory.md#view-device-details).


### View IT subnets from an OT sensor device map
<!--cant' validate this procedure-->

By default, IT devices are automatically aggregated by [subnet](how-to-control-what-traffic-is-monitored.md#define-ics-or-iot-and-segregated-subnets), so that the map focuses on OT and ICS networks.

**To expand an IT subnet**:

1. Sign into your OT sensor and select **Device map**.
1. Right-click the icon on the map that represents a specific IT network and select **Expand Network**. 
1. In the confirmation box that appears, select **OK**.

**To collapse an IT subnet:**

1. Sign into your OT sensor and select **Device map**. 
1. Select one or more expanded subnets and then select **Collapse All**.


## Create a custom device group from an OT sensor device map

In addition to OT sensor's [built-in device groups](device-inventory.md#built-in-device-groups), create new custom groups as needed to use when highlighting or filtering devices on the map.

1. Either select **+ Create Custom Group** in the toolbar, or right-click a device in the map and then select **Add to custom group**.

1. In the **Add custom group** pane:

    - In the **Name** field, enter a meaningful name for your group, with up to 30 characters. 
    - From the **Copy from groups** menu, select any groups you want to copy devices from.
    - From the **Devices** menu, select any extra devices to add to your group.

## Import / export device data from an OT sensor device map

Use one of the following options to import and export device data:

- **Import Devices**. Select to import devices from a pre-configured .CSV file.
- **Export Devices**. Select to export all currently displayed devices, with full details, to a .CSV file.<!--is this correct?-->
- **Export Device Summary**. Select to export a high level summary of all currently displayed devices to a .CSV file. 


## Edit devices from the OT sensor device map

1. Sign into an OT sensor and select **Device map**. 

1. Right-click a device to open the device options menu, and then select any of the following options:

    |Name  |Description  |
    |---------|---------|
    |**Authorize/Unauthorize**     |    Changes the device's [authorization status](device-inventory.md#authorized-and-unauthorized-devices).     |
    |**Mark as Important / Non-Important**     |    Changes the device's [importance](device-inventory.md#important-ot-devices) status, highlighting business critical servers on the map with a star and elsewhere, including OT sensor reports and the Azure device inventory.     |
    |**Show Alerts** / **Show Events**     |  Opens the **Alerts** or **Event Timeline** tab on the device's details page.   |
    |  **Activity Report**   | Generates an activity report for the device for the selected timespan.        |
    | **Simulate Attack Vectors**    |   Generates an [attack vector simulation](how-to-create-attack-vector-reports.md) for the selected device.      |
    | **Add to custom group**    | Creates a new [custom group](#create-a-custom-device-group-from-an-ot-sensor-device-map) with the selected device.        |
    |  **Delete**   |Deletes the device from the inventory.     |

## Merge devices from the OT sensor device map

You may want to merge devices if the OT sensor detected multiple network entities associated with a unique device, such as a PLC with four network cards, or a single laptop with both WiFi and a physical network card.

You can only merge [authorized devices](device-inventory.md#authorized-and-unauthorized-devices). 

> [!IMPORTANT]
> You can't undo a device merge. If you mistakenly merged two devices, delete the devices and then wait for the sensor to rediscover both.
> 

**To merge multiple devices**:

1. Sign into your OT sensor and select **Device map**.

1. Select one or more authorized devices and then right-click and select **Merge**. 

1. In the **Set merge device attributes** dialog, select the device name you want to use for the merged device.

1. Select **Save** to save your changes.

It can take up to two minutes complete the merge. Merge events are listed in the OT sensor's event timeline.


## Manage device notifications from an OT sensor device map

As opposed to alerts, which provide details about changes in your traffic that might present a threat to your network, device notifications on an OT sensor device map provide details about network activity that might require your attention, but are not threats. 

For example, you might receive a notification about an inactive device that needs to be reconnected, or removed if it's no longer part of the network.

**To view and handle device notifications**:

1. Sign into the OT sensor and select **Device map** > **Notifications**.

1. In the **Discovery Notifications** pane on the right, filter notifications as needed by time range, device, subnet, or operating systems.

1. Do one of the following:

    - Accept or dismiss each notification, one at a time.
    - Select **Select All** to show which notifications can be handled together.Clear selections for specific notifications, and then accept or dismiss any remaining selected notifications together.

    When you handle multiple notifications together, you may still have remaining notifications that need to be handled manually, such as for new IP addresses or no subnets detected.

> [!NOTE]
> For example, you may want to handle multiple notifications together if:
>
> - IT upgraded the OS across multiple network servers and you want to learn all of the new server versions.
> - A group of devices is no longer active, and you want to instruct the OT sensor to remove the devices from the OT sensor.

The following table lists available responses for each notification, and when we recommend using each one:

| Type | Description | Available responses |
|--|--|--|
| **New IP detected** | A new IP address is associated with the device. This may occur in the following scenarios: <br><br>- A new or additional IP address was associated with a device already detected, with an existing MAC address.<br><br> - A new IP address was detected for a device that's using a NetBIOS name. <br /><br /> - An IP address was detected as the management interface for a device associated with a MAC address. <br /><br /> - A new IP address was detected for a device that's using a virtual IP address. | - **Set Additional IP to Device**: Merge the devices <br />- **Replace Existing IP**: Replaces any existing IP address with the new address <br /> - **Dismiss**: Remove the notification. |
| **Inactive devices** | Traffic wasn't detected on a device for more than 60 days. | - **Delete**: Delete any devices that aren't part of your network anymore.<br />- **Dismiss**:  Remove the notification if the device is still part of your network. You may want to reconnect the device if it's been disconnected by accident.|
| **New OT devices** | A subnet includes an OT device that's not defined in an ICS subnet. <br><br>This may occur when a device is detected that can be defined as an ICS subnet. We recommend defining such devices as ICS subnets to differentiate between OT and IT devices on the map. | - **Set as ICS Subnet**: Define the device as an ICS subnet. <br>- **Dismiss**: Remove the notification if the device isn't part of the subnet. |
| **No subnets configured** | No subnets are currently configured in your network. <br /><br /> We recommend configuring subnets for the ability to differentiate between OT and IT devices on the map. | - **Open Subnets Configuration** and [configure subnets](how-to-control-what-traffic-is-monitored.md#define-ics-or-iot-and-segregated-subnets). <br />- **Dismiss**: Remove the notification. |
| **Operating system changes** | One or more new operating systems have been associated with the device. | - Select the name of the new OS that you want to associate with the device.<br /> - **Dismiss**:  Remove the notification. |
| **New subnets** | New subnets were discovered. |-  **Learn**: Automatically add the subnet.<br />- **Open Subnet Configuration**: Add all missing subnet information.<br />- **Dismiss**<br />Remove the notification. |
| **Device type changes** | A new device type has been associated with the device. | - **Set as {…}**: Associate the new type with the device.<br />- **Dismiss**: Remove the notification. |


## View a device map for a specific zone

If you're working with an on-premises management console with sites and zones configured, device maps are also available for each zone.

On the on-premises management console, zone maps show all network elements related to a selected zone, including OT sensors, detected devices, and more.

**To view a zone map**:

1. Sign into an on-premises management console and select **Site Management** > **View Zone Map** for the zone you want to view. For example: <!--fix image-->

    :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-region-to-default-business-unit-v2.png" alt-text="Default region to default business unit.":::

1. Use any of the following map tools to change your map display:

    |Name  |Description  |
    |---------|---------|
    |**Save current arrangement**  <br>:::image type="icon" source="media/how-to-work-with-maps/save-zone-map.png" border="false":::   | Saves any changes you've made in the map display.        |
    |**Hide multicast/broadcast addresses**<br>:::image type="icon" source="media/how-to-work-with-maps/hide-multi-cast-zone-map.png" border="false":::         |  Selected by default. Select to show multicast and broadcast devices on the map.  |
    |**Present Purdue lines**  <br>:::image type="icon" source="media/how-to-work-with-maps/present-purdue-zone-map.png" border="false":::   | Selected by default. Select to hide Purdue lines on the map. |
    |**Relayout**  <br>:::image type="icon" source="media/how-to-work-with-maps/relayout-zone-map.png" border="false":::   | Select to reorganize the layout by Purdue lines or by zone.        |
    |**Scale to fit screen** <br> :::image type="icon" source="media/how-to-work-with-maps/scale-zone-map.png" border="false":::   | Zooms in or out on the map so that the entire map fits on the screen.        |
    | **Search by IP / MAC** | Select a specific IP or MAC address to highlight the device on the map. |
    | **Change to a different zone map** <br>:::image type="icon" source="media/how-to-work-with-maps/change-zone-map.png" border="false"::: | Select to open the **Change Zone Map** dialog, where you can select a different zone map to view. |
    | **Zoom** <br>:::image type="icon" source="media/how-to-work-with-maps/zoom-in-icon-v2.png" border="false"::: / :::image type="icon" source="media/how-to-work-with-maps/zoom-out-icon-v2.png"  border="false"::: | Zoom in on the map to view the connections between each device, either using the mouse or the **+**/**-** buttons on the right of the map. |

1. Zoom in to view more details per devices, such as to view the number of devices grouped in a subnet, or to expand a subnet.

1. Right-click a device and select **View properties** to open a **Device Properties** dialog, with more details about the device.

1. Right-click a device shown in red and select **View alerts** to jump to the **Alerts page**, with alerts filtered only for the selected device.


## Next steps

For more information, see [Investigate sensor detections in a Device Inventory](how-to-investigate-sensor-detections-in-a-device-inventory.md).


