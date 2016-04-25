---
date: 2014-12-25T15:30:00Z
tags: ["Technology", "Home Automation", "IoT"]
title: Wink vs. SmartThings
---

During my holiday shopping adventures, I obtained a Wink hub at a 50% discount and decided to write a comparison of Wink to SmartThings (based on my personal experience with each device).  If you don't follow home automation, [Wink](http://www.wink.com/) is a Quirky product that is being heavily marketed in partnership with retail stores, such as Home Depot and Target.  [SmartThings](http://www.smartthings.com/) offers a similar product, but was [purchased by Samsung](http://blog.smartthings.com/news/smartthings-updates/smartthings-samsung-open-platform/) earlier this year. Both devices offer a home automation hub that can control all of your ZigBee and Z-wave devices, but there are a few fundamental differences.

## Cost

The first point of comparison is cost. The Wink Hub is available for 50 USD and at the moment, that's half the price of the SmartThings Hub. Additionally, Wink has teamed with GE to sell an incredibly inexpensive wirelessly connected LED bulb called the [GE Link](http://gelinkbulbs.com/). These bulbs are designed to work with the Wink system right out of the box and at 15 USD per bulb, this is a bargain compared to other connected bulbs. Based solely on price, Wink takes the lead by offering an affordable entry to home automation.

## Ease of Setup

The setup is where things became interesting. The Wink Hub connects to your network via 2.4GHz Wi-Fi, but it does **not** work with 5GHz Wi-Fi networks. Unfortunately, my home network runs on 5GHz. To compensate for this, I set up a 2.4GHz wireless network specifically for the Wink Hub and attempted to configure the hub using the Wink App installed on my phone. I originally had some problems because my phone was connected to the 5GHz network and the Wink App seems to default to the SSID of the wireless network the phone is using to configure the hub. During setup, the Wink App connects your smartphone to the Wink Hub over an ad-hoc wireless network to configure the hub and then reconnects to your home network. I spent about an hour staring at the blinking LED before I switched my phone over to the 2.4GHz network and eventually got everything configured. If I were using 2.4GHz wireless or a bridged wireless network, I probably wouldn't have had any issues.

The main point of my frustration was figuring out what the different LED colors on the Wink Hub mean. The Wink Hub has a single LED that changes color depending on the hub's state, but it does not ship with documentation that describes these states. Fortunately, [this information](http://www.wink.com/help/faq/#hub) is available online.

As for the SmartThings hub, it doesn't support Wi-Fi. Instead, it has an Ethernet jack, which I plugged into my router and had configured within minutes. For me, the SmartThings Hub was much easier to setup.  It's also smaller than the Wink hub, so I can keep it stashed with my other networking equipment.

## Using Smart Devices

Most of the interaction with your connected devices will be done through a smartphone application. Therefore, I think it's really important to have a polished interface. In this regard, I think that the Wink App is a bit easier to use than the SmartThings App. The Wink App features "Robots", which are similar to the "SmartApps" found in the SmartThings App. Robots and SmartApps allow you to apply conditional logic to a single device or a group of devices, such as "If I leave the house, turn off all the lights." However, when manually controlling a device, such as a connected light switch, the Wink system had a noticeable amount of latency in issuing commands. The SmartThings App is more responsive to manual commands, which I think is important from a user perspective. In a non-connected household, you can instantly turn off the light by flipping a switch, so it's discomforting to flip a smart switch and see all the connected lights turn off asynchronously due to a perceptible delay.

I had very few issues with controlling devices using Wink or SmartThings, but one exception was pairing the [Quirky+GE Spotter Multipurpose Sensor](http://www.wink.com/products/quirkyge-spotter-multipurpose-sensor/) with the Wink App. The pairing process involves giving the Spotter power (via battery or plug-in adapter) and holding your smartphone against the Spotter's light sensor as it communicates with the device by flashing the screen. I could never get the Spotter to work and ended up returning the device out of frustration. The entire process seemed kludgy to me.

On the other hand, using the GE Link bulbs was a pleasant experience. The Wink Hub supports the GE Link bulbs out of the box and I was able to set up geofence triggers and schedule the lights to wake me up in the morning. Although it isn't officially supported, I was also able to pair the bulbs with the SmartThings Hub by requesting a ZigBee firmware update from SmartThings support.

## Summary

Overall, I prefer SmartThings to Wink. The ease of setup, the customer support, and the active community are promising. Both systems tie into [IFTTT](https://ifttt.com/), which greatly extends the usefulness of your "smart" devices. The Internet of Things is still in its infancy and therefore, I think the future will rely heavily on industry partnerships. As of right now, I think SmartThings has a larger community supporting it, but Wink has a strong partnership with GE and retail stores, so it will be interesting to see how the market develops.
