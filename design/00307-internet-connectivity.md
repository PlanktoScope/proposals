# Proposal: Make internet connectivity easier

Author(s): Ethan Li (@ethanjli)

Last updated: 2023-12-06

Discussion at: <https://github.com/PlanktoScope/PlanktoScope/issues/307>

## Abstract

This proposal analyzes the trade-offs between different possible approaches to make it simpler and easier for PlanktoScope operators to add internet connectivity to their PlanktoScopes, and recommends an optimal set of approaches for doing so.

[TODO: actually recommend a set of approaches]

## Background

Currently, our recommended way for users to access the PlanktoScope software is by connecting to the PlanktoScope's embedded Raspberry Pi computer from a client device (a computer, tablet, or phone) over a direct Wi-Fi connection (on the `wlan0` network interface) or Ethernet connection (on the `eth0` network interface).
If a user wants to maintain internet connectivity on their client device (e.g. to be able to look up information at the same time, or to be on a Slack chat or a video call for remote assistance, or to use Google Docs, etc.) while they are connected to the PlanktoScope, they have three possible strategies:

1. Keep their client device connected directly to the PlanktoScope using one of the client device's network interface, and connect their client device to the internet using an additional network interface (e.g. another Ethernet adapter or another Wi-Fi module) on the client device.

2. Keep their client device connected directly to the PlanktoScope using one of the PlanktoScope's network interfaces, and connect their PlanktoScope to the internet using another network interface (i.e. Ethernet for internet access and Wi-Fi hotspot for client devices, or Wi-Fi for internet access and Ethernet for a client device), and rely on the PlanktoScope's default configuration to share its own internet access to any directly-connected client devices.

3. Connect their PlanktoScope and client device to a local network with the appropriate firewall settings and with internet access, and then access the PlanktoScope from the client device via mDNS over that local network.

We would like to make it easier for users to connect their PlanktoScopes to the internet (which is enabled by strategies 2 and 3); internet connectivity would enable PlanktoScopes to receive software updates, to upload data seamlessly (to EcoTaxa or elsewhere), and to be accessible for remote assistance and remote operation.
However, the current complexity of these strategies makes it harder for users to actually achieve internet connectivity while simultaneously maintaining access to the PlanktoScope for client devices:

1. Not all client devices have multiple network interfaces. For example, many laptops would require an additional Wi-Fi adapter to be able to connect to two Wi-Fi networks (the PlanktoScope's local hotspot, plus a Wi-Fi network with internet connectivity); and I don't know if mobile devices would be able to use an additional Wi-Fi adapter. [TODO: test this out]

2. It is not always simple or possible to run the PlanktoScope in an operating environment where a client device can connect by Ethernet to a PlanktoScope which has internet access via Wi-Fi, or a PlanktoScope can get internet access via Ethernet (in order to allow one or more client devices to connect to the PlanktoScope via Wi-Fi).
   For example, most laptops don't include Ethernet interfaces, and many places don't provide Ethernet ports for internet access.

3. Users typically don't have control (or prior knowledge) of whether a local network has the appropriate settings (e.g. firewall rules) for a PlanktoScope on that network to be accessible to other devices on that network.
   Even worse, if a user has configured their PlanktoScope to connect to a Wi-Fi network and discovered that they can't access their PlanktoScope over that Wi-Fi network (e.g. due to firewall rules or a captive portal), either they must find a way to connect their client device to their PlanktoScope over Ethernet or they musteither re-flash the PlanktoScope's SD card, as there is no other way to reset the PlanktoScope's Wi-Fi settings.

These issues degrade the usability of the PlanktoScope software.

Additionally, in strategies 2 and 3, if the local network for internet access has a captive portal or some special authorization process (e.g. network device registration) before it provides network access or internet connectivity, then we will need to provide a software interface for the user to access that captive portal through a web browser running on the PlanktoScope.
This can be done with [Browsh](https://www.brow.sh/), which has Firefox as a dependency; but including Firefox would significantly increase the size of our SD card images, and dealing with captive portals makes our setup process more complicated.

## Proposal

This proposal analyzes several options for addressing the issues highlighted in the "Background" section, and recommends the following changes:

1. [TODO: select the relevant changes]

### Option A: include an Ethernet cable and Ethernet-to-USB adapter in kits

A direct Ethernet connection between the PlanktoScope and the client device is the most reliable way to connect to a PlanktoScope, both for troubleshooting in abnormal situations and for standard operations in normal situations.
This is because the Ethernet connection's reliability and usability isn't affected by factors in the local Wi-Fi environment, such as how crowded the local area is with other Wi-Fi networks or other PlanktoScopes, or what Wi-Fi regulatory domain the PlanktoScope is in, or what firewall and other security configurations are on existing Wi-Fi networks which the PlanktoScope is trying to connect to.
Such factors have caused problems in the past with connecting to PlanktoScopes over Wi-Fi.
Additionally, an Ethernet connection is the highest-bandwidth way to transfer data from the PlanktoScope's Raspberry Pi across a network, making it the most efficient option for PlanktoScopes which may need to move raw datasets either to a local computer or to the cloud.
Finally, an Ethernet connection provides users a way to fix any messed-up Wi-Fi configurations on a PlanktoScope.

For these reasons, it may be a good idea to include an Ethernet cable (and an Ethernet-to-USB adapter for client devices) as part of the official PlanktoScope kit.
An Ethernet-to-USB adapter would also be a necessary part of the kit because most client devices have no Ethernet port.

#### Implications for strategy 1

The user could use the provided Ethernet cable and Ethernet-to-USB adapter to connect their client device to the PlanktoScope while the client device is connected to the internet via Wi-Fi.
Or the user could use the provided Ethernet cable and Ethernet-to-USB adapter to connect their client device to the internet while the client device is connected to a PlanktoScope via Wi-Fi.

[TODO: test if this works on Android]

#### Implications for strategy 2

This option gives PlanktoScope operators two ways to connect their client device to the PlanktoScope and simultaneously give their PlanktoScope internet access, only using components provided in the standard PlanktoScope kit:

1. Connect the PlanktoScope to the internet by Wi-Fi (using the PlanktoScope's `wlan0` interface in non-hotspot mode); and connect the client device to the PlanktoScope by Ethernet, using the provided Ethernet cable and (if needed) the provided Ethernet-to-USB adapter.
2. Connect the PlanktoScope to the internet by Ethernet, using the provided Ethernet cable; and connect the client device to the PlanktoScope by Wi-Fi (using the PlanktoScope's `wlan0` interface in hotspot mode).

#### Implications for strategy 3

If the user configures the PlanktoScope to connect to a Wi-Fi network which doesn't allow their client device to access the PlanktoScope over mDNS, the kit's Ethernet cable and Ethernet-to-USB cable gives them a way to fix their PlanktoScope's Wi-Fi settings without having to re-flash their Planktoscope's SD card.

#### Additional considerations

Compatibility:

- This option would not require any changes to the PlanktoScope software.
- The user has some flexibility to choose their strategy depending on what their operating environment is compatible with: if they can't connect any device to the internet by Ethernet, they can use strategy 1 by connecting both the client device and the PlanktoScope to the internet by Wi-Fi, and then connecting the client device to the PlanktoScope by Ethernet.
  On the other hand, if they can't connect any device to the internet by Wi-Fi, then they can use strategy 2 by connecting the PlanktoScope to the internet by Ethernet, and then connecting the client device to the PlanktoScope by the PlanktoScope's Wi-Fi hotspot.

Limitations:

- Without additional equipment (e.g. an Ethernet switch or additional Ethernet cables and additional Ethernet-to-USB adapters), only one client device can be connected to the PlanktoScope by Ethernet cable.

User-facing complexities:

- The user will still need to choose between strategy 1, strategy 2, and strategy 3.
  This choice will depend on (and can be guided by) their operating environment and the capabilities of their client device, but it's a bit complicated to describe.

- For strategy 2, the user will need to decide whether to connect the PlanktoScope to the internet by Wi-Fi (in which case they'll need to connect their client device to the PlanktoScope by Ethernet) or by Ethernet (in which case they'll need to connect their client device to the PlanktoScope by the PlanktoScope's Wi-Fi hotspot).
  This choice will depend on (and can be guided by) their operating environment and the capabilities of their client device, but it's a bit complicated to describe.

Technical risks/complexities and unresolved questions:

- We don't know whether Ethernet-to-USB adapters are compatible with mobile phones and tablet computers, or whether they would allow a PlanktoScope's Ethernet port to take advantage of a phone's USB tethering functionality. [TODO: test this]
  Thus, this option might not benefit users who use a phone or tablet as their client device for operating the PlanktoScope, and who cannot connect their PlanktoScope to the internet via an Ethernet connection.

Kit production:

- FairScope will need to procure Ethernet-to-USB adapters.
  Some client devices only have USB-C ports, while older client devices may only have USB-A ports.
  Thus, for maximum compatibility with client devices, the kit should probably include an Ethernet-to-USB-C adapter and a USB-C-to-USB-A adapter.
- FairScope will need to procure Ethernet cables.
  Different operators may prefer different Ethernet cable lengths.
  If FairScope lets customers specify their preferred length, this would add complexity to FairScope's processes.
  Alternatively, FairScope could choose a default cable length (e.g. 1 meter) which would be reasonable for connecting to the PlanktoScope from a local laptop.
- DIY kit producers will need to provide substitutions for Ethernet cables and Ethernet-to-USB adapters if they can't procure the official ones sold by FairScope.
  This issue does not directly impact FairScope, and it may not be a major issue given that Ethernet cables and Ethernet-to-USB adapters are commodity products.

User upgrade path:

- Users of "legacy PlanktoScopes" (which includes both DIY users and FairScope customers) will need to purchase an Ethernet cable (and maybe also an Ethernet-to-USB adapter) if they want to have an easier experience with network connectivity.
  They will face the same potential issues as DIY kit producers, though if FairScope resells these parts, it could simplify the upgrade path for customers.
- Users who don't upgrade will miss out on the benefits of having an Ethernet cable and Ethernet-to-USB adapter as part of their PlanktoScope kit, and nothing will change for them; they will just use their PlanktoScopes the same way as before.
- In other words, the Ethernet cable and Ethernet-to-USB adapter can be treated as a strongly recommended (and included by default) - but still optional - add-on to the PlanktoScope.

### Option B: add software support for an optional USB Wi-Fi dongle, and include a dongle in kits

Wi-Fi is the most generally compatible way to establish network connections, both for internet connectivity and for client device connectivity.
This is because almost all network devices have a Wi-Fi interface - phones, tablets, laptops, and modern desktop computers.

For this reason, the simplest option is to make the PlanktoScope's Raspberry Pi able to host a Wi-Fi hotspot while simultaneously being connected to the internet via an existing Wi-Fi network.
One simple way to accomplish this is to add a USB Wi-Fi dongle to the PlanktoScope kit, and plug it into the PlanktoScope's Raspberry Pi; then the Raspberry Pi would have its internal Wi-Fi module and an external Wi-Fi module plugged into it.
There is a [list](https://github.com/morrownr/USB-WiFi/blob/main/home/USB_WiFi_Adapters_that_are_supported_with_Linux_in-kernel_drivers.md) of USB Wi-Fi dongles which work out-of-the-box on Linux, with no need to install drivers beyond what is already provided with the Raspberry Pi OS.
Of the listed adapters, the [Panda PAU03 N150 adapter](https://www.amazon.com/Panda-Ultra-150Mbps-Wireless-Adapter/dp/B00762YNMG) is the smallest option; note that it only works with the 2.4 GHz frequency band.

#### Implications for strategy 1

Instead of plugging the USB Wi-Fi dongle into the PlanktoScope, the user could plug the USB Wi-Fi dongle into their client device.
This would allow the user to simultaneously connect their laptop to the PlanktoScope's Wi-Fi hotspot and to a Wi-Fi network with internet access.

[TODO: test if this works on Android]

#### Implications for strategy 2

When the USB Wi-Fi dongle is plugged into the PlanktoScope's Raspberry Pi, the internal Wi-Fi module will show up in the OS as the `wlan0` interface, while the USB Wi-Fi dongle would show up as `wlan1`.
It may make sense for the default OS configuration to use `wlan0` (the Raspberry Pi's embedded Wi-Fi module) for connecting to the internet, and to use `wlan1` (the USB Wi-Fi dongle) for hosting the Wi-Fi hotspot.
This way, the Raspberry Pi has a stable MAC address for connecting to Wi-Fi networks which require MAC address registration as part of their firewall settings.
Additionally, this allows us to take advantage of the 5 GHz band when `wlan0` connects to Wi-Fi networks, while `wlan1` only makes a Wi-Fi hotspot on the 2.4 GHz band (which is a limitation we have anyways for compatibility with Wi-Fi regulations across different countries); this matches well with how the Raspberry Pi's internal Wi-Fi adapter supports both 2.4 GHz and 5 GHz, while the Panda PAU03 adapter only supports 2.4 GHz.

Then the PlanktoScope's Node-RED dashboard panel for connecting to Wi-Fi would be used to select which network the PlanktoScope connects to on `wlan0`.

#### Implications for strategy 3

If the user configures the PlanktoScope to have `wlan0` connect to a Wi-Fi network which doesn't allow their client device to access the PlanktoScope over mDNS, the Wi-Fi hotspot over `wlan1` gives them a way to fix their PlanktoScope's Wi-Fi settings on `wlan0` without having to re-flash their Planktoscope's SD card.

#### Additional considerations

Compatibility:

- The PlanktoScope OS will need to be reconfigured to make use of two Wi-Fi interfaces, but it will still need to support PlanktoScopes which lack the USB Wi-Fi dongle.
  We can maintain compatibility by having the default behavior be that, when no USB Wi-Fi dongle is plugged in, the PlanktoScope will connect to a Wi-Fi network with `wlan0` if such a connection is configured and the PlanktoScope has internet access over that connection (represented by being able to ping `google.com`); and otherwise it will fall back to making a Wi-Fi hotspot on `wlan0`.
  This is the current behavior of the PlanktoScope's autohotspot service.
  Then, plugging in the USB Wi-Fi dongle activates the new behavior where `wlan0` is only used for connecting to a Wi-Fi network, and `wlan1` is only used for making a Wi-Fi hotspot.

- The Panda PAU03 adapter only works with the 2.4 GHz frequency band.
  Modern wireless access points and routers which use 5 GHz tend to be dual-band (i.e. they also support 2.4 GHz) for backwards-compatibility, so we should be fine.

- The Panda PAU03 adapter is small, but it might not be small enough for the user to keep it plugged in to the Raspberry Pi's USB port while having the USB/Ethernet port cover attached to the PlanktoScope's case.
  Addressing this would require changing the mechanical design of the PlanktoScope case to add more space between the Raspberry Pi's USB ports and the case's USB/Ethernet port cover.
  [TODO: check Panda PAU03 fit in the v2.6 PlanktoScope case]

- [TODO: check Panda PAU03 fit in the v2.1 PlanktoScope case] The Panda PAU03 adapter is small, but it might not be small enough to fit into the tiny USB port holes in the v2.1 PlanktoScope case.
  However, this is just a design flaw with the v2.1 PlanktoScope case, and an easy workaround is to remove the acrylic plate which covers the USB and Ethernet ports.

- If the user wants to try strategy 1 by plugging the USB Wi-Fi dongle into their client device, a variety of device-specific compatibility problems are possible (including missing drivers on macOS and/or Windows), and all of them would be difficult to troubleshoot.
  We probably don't have the resources to help users diagnose and troubleshoot such compatibility problems, so strategy 1 might turn out to not be a realistic strategy for this option.

- Besides the Panda PAU03 adapter, there are also other small USB Wi-Fi dongles available which don't have out-of-the-box driver support on Linux.
  We could try to maintain compatibility with such dongles by adding drivers for them, but it will add lots of complexity to our distro setup scripts due to the types of changes needed - so it may not be worth it.

Limitations:

- If the user refuses to buy a USB Wi-Fi dongle, we still have no way for them to fix a messed-up Wi-Fi configuration without asking them to connect to their PlanktoScope over Ethernet or re-flash their SD card.

User-facing complexities:

- If the USB Wi-FI dongle is plugged into the PlanktoScope's Raspberry Pi by default, this pushes users to default to strategy 2 (and to try seeing if strategy 3 works on their internet-connected Wi-Fi network), which simplifies our setup instructions.
  Then strategy 1 is only used as a last-resort backup for troubleshooting if strategies 2 and 3 aren't working.

Technical risks/complexities and unresolved questions:

- [TODO: build a proof-of-concept] We will need to make sure we can implement the appropriate behaviors when the USB Wi-Fi dongle is plugged in or unplugged.

Kit production:

- FairScope will need to procure appropriate USB Wi-Fi dongles.
  Some client devices (especially newer ones) only have USB-C ports, while USB Wi-Fi dongles are only sold with USB-A ports.
  Thus, if we want to provide maximum compatibility with client devices in strategy 1, the kit should probably also include a USB-A-to-USB-C adapter.
  However, this might not be worth it if we don't really want to support strategy 1.

- DIY kit producers will need to find an appropriate substitution for the USB Wi-Fi dongle if they can't procure the official ones sold by FairScope.
  This issue does not directly impact FairScope, but it may be an issue for DIY kit producers who can't find a small USB Wi-Fi dongle which has out-of-the-box Linux compatibility.

User upgrade path:

- Users of "legacy PlanktoScopes" (which includes both DIY users and FairScope customers) will need to purchase an appropriate USB Wi-Fi dongle if they want to have an easier experience with network connectivity.
  They will face the same potential issues as DIY kit producers, though if FairScope resells these USB Wi-Fi dongles, it could simplify the upgrade path for customers.

- Users who don't upgrade will miss out on the benefits of having an additional Wi-Fi module in their PlanktoScope, and nothing will change for them; they will just use their PlanktoScopes the same way as before.

- In other words, the USB Wi-Fi dongle can be treated as a strongly recommended (and included by default) - but still optional - add-on to the PlanktoScope.

### Option C: add OS support for phone/laptop USB tethering

(talk about simplicity by avoiding university firewall stuff vs. risks of unintentionally high mobile data usage due to data transfer)

[TODO: test out this option]

### Option D: reconfigure the OS for AP-STA mode

In theory, it is possible to reconfigure the operating system to simultaneously run the `wlan0` interface (for the Raspberry Pi 4's integrated Wi-Fi module) as a client connected to a Wi-Fi network with internet access and as an access point providing a Wi-Fi hotspot.
This is called [AP-STA mode](https://docs.raspap.com/ap-sta/), and instructions for such a configuration can be found at https://raspberrypi.stackexchange.com/questions/89803/access-point-as-wifi-router-repeater-optional-with-bridge/ .
This would provide the same benefits as Option B, but without the need to add an actual USB Wi-Fi dongle to PlanktoScope kits - and thus avoid questions of how to maintain software backwards-compatibility with older PlanktoScopes lacking the USB Wi-Fi dongle.

However, in practice the performance of AP-STA mode has been [reported](https://forums.raspberrypi.com/viewtopic.php?t=289180) to degrade significantly with traffic, and reliability/stability issues have also been [reported](https://www.reddit.com/r/RaspAP/comments/10vdhg3/apsta_mode_vs_second_usb_adapter/).
Because the PlanktoScope's internet connection may be used to transmit lots of data (e.g. for uploading datasets, downloading software updates, and viewing the camera preview MJPEG stream), and the PlanktoScope's Wi-Fi hotspot is used to transmit lots of data (notably the camera preview MJPEG stream), AP-STA mode would introduce an unacceptable level of risk for performance/reliability issues for our project.

### Option E: no change

[TODO]

### Synthesis

[TODO]

## Rationale

[A discussion of alternate approaches and the trade offs, advantages, and
disadvantages of the specified approach.]

[discuss network security implications of the options, about being able to turn off wifi hotspot mode to lock down access]

- In order to allow users to reduce the attack surface of their PlanktoScopes, we will need to add a setting to allow users to change the behavior of their PlanktoScope so that it can be set to never host a Wi-Fi hotspot, even when it can't find an existing Wi-Fi network with internet access.
  Such a setting could be used regardless of whether one or two Wi-Fi modules are on the PlanktoScope.

## Compatibility

[A discussion of any impacts of the change on backwards-compatibility.]

## Implementation

[A description of what actions need to be taken in order to implement the
proposal, who will do them, and when.
This should include a discussion of how the work fits into the PlanktoScope
project's release cycle.]

## Open issues (if applicable)

[A discussion of issues relating to this proposal for which the author does not
know the solution. This section may be removed if there are no such issues.]
