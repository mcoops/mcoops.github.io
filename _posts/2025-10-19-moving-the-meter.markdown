---
# layout: post
title: "Version 2... we moved"
date: 2025-10-19 00:00:00 +1000
categories: IoT
---

We moved... Devastating as stuff like the water meter didn't meet the priority
in the new house. Until now!

Same rules apply as before:

- No batteries
- Reuse what we already have

The only difference this time around is that the new water meter is really far
away, doesn't flood, and the meter itself is closer to the lid.

<a href="/assets/images/waterMove/sensor.jpeg">
  ![Image](/assets/images/waterMove/sensor.jpeg){: width="450" }
</a>

Same idea though - a piece of aluminium just to hold the sensor in place
but not obstruct 'the man' from reading the meter.

The last is important as now we cannot use the existing sensor as it's simply
too big.

## Induction sensor

Previously we had a large PNP sensor, which had a large detection range but
that simply won't fit at our new house.

Luckily I had also ordered another sensor at the time which is smaller and
came highly recommended from AliExpress - ABILKEEN. The one I bought
specifically is no longer available, but the brand still is. For example:
[AliExpress](https://www.aliexpress.com/item/1005008293144678.html?spm=a2g0o.productlist.main.1.47821AJa1AJaLU&algo_pvid=b7e023d4-2bf4-4887-9b6c-559037a49209&pdp_ext_f=%7B%22order%22%3A%22-1%22%2C%22eval%22%3A%221%22%2C%22fromPage%22%3A%22search%22%7D&utparam-url=scene%3Asearch%7Cquery_from%3A%7Cx_object_id%3A1005008293144678%7C_p_origin_prod%3A)

Just note, I did order a PNP NO (Normally Open) but received a NC (Normally
Closed). Not to worry, just means that there's always a constant voltage for
the signal, instead of only receiving a voltage when the sensor is active.
We just need to account for that.

Typically a NC sensor is associated with more power usage, but comparing it to
the larger one we used to use, it's similar anyway.

The run is also a lot longer than previous, from about 6 meters to over 25
meters. Due to the distance, voltage drop is a factor, but will depend heavily
on the cable quality you use. A 3-core cable from [AliExpress](https://www.aliexpress.com/item/4000567876582.html?spm=a2g0o.order_list.order_list_main.30.699a180235hXQX)
I only lose ~1V so we get a constant read of 11V - good enough for what we're
doing.

## Shelly Uni

Doesn't change from our previous [setup](2024-07-18-monitoring-water-usage.markdown).

The only thing I have changed is instead of using an automotive fuse holder, we
switched it to one I have spare from Christmas lights [Hexfuse2](https://www.hansonelectronics.com.au/product/hexfuse2/).
It's essentially the same as before, just not as dodgy. The negative is common
and the positive line is what's run through the fuse.

I like it better as it just better in our housing, and even has a light
which activates when the fuse blows.

The fuse is mainly here to protect the power supply from the sensor out the
front of the house in case it shorts or an animal chews it.

My wiring looks like:

<a href="/assets/images/waterMove/shellyWiring.jpg">
  ![Image](/assets/images/waterMove/shellyWiring.jpg){: width="300"}
</a>

The UNI really doesn't have to be behind the fuse, I've done it just as it's
easier wiring and they're cheap.

## Shelly Setup

Again nothing changes from the previous [setup](2024-07-18-monitoring-water-usage.markdown).

EXCEPT, the sensor is always on, so we need to invert the logic in Home
Assistant, so our automation now looks like the follow but we trigger when the
voltage drops below about half:

```yaml
alias: generate watermeter pulse
description: ""
trigger:
  - platform: numeric_state
    entity_id:
      - sensor.watermeter_adc
    below: 6
condition: []
action:
  - service: input_boolean.toggle
    metadata: {}
    data: {}
    target:
      entity_id: input_boolean.watermeter_pulse
  - delay:
      hours: 0
      minutes: 0
      seconds: 0
      milliseconds: 100
  - service: input_boolean.toggle
    metadata: {}
    data: {}
    target:
      entity_id: input_boolean.watermeter_pulse
mode: single
```

That's it, we're back in action.

<a href="/assets/images/waterMove/newGraph.png">
  ![Image](/assets/images/waterMove/newGraph.png){: width="450" }
</a>
