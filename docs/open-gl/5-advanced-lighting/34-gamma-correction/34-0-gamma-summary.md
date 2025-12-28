As soon as we compute the final pixel colours of the scene, we display them on a monitor. These used to be through cathode-ray-tube (CRT) monitors. These monitors had the physical property that twice the input voltage did not result in twice the amount of brightness. Doubling the input voltage resulted in a brightness equal to an exponential relationship of 2.2, known as the gamma of a monitor.

This happens to closely match how human beings measure brightness as brightness is also displayed with a similar inverse power relationship.

![[34-0-brightness.png]]

The top line reflects the correct brightness scale to the human eye (0.2 is double of 0.1). However, the physical brightness of light is reflected in the bottom scale. Doubling the brightness returns the correct physical brightness, but since our eyes perceive brightness differently (more susceptible changes to bright colours), it looks weird.

Because the human eye prefers to see brightness colours according to the top scale, monitors will use a power relationship for displaying output colours so that the original physical brightness colours are mapped to the non-linear brightness colours in the top scale.

This results in an issue when rendering graphics, the colour and brightness options we configure are based on what we perceive from the monitor, and thus all the options are actually non-linear brightness/colour options.

![[34-0-gamma.png]]

- The dotted line represents colour/light values in linear space
- The solid line represents the colour space that monitors display
- Take the example of (0.5, 0.0, 0.0) - a semi-dark red light
- Doubling this colour in linear space results in (1.0, 0.0, 0.0) - red
- However, the original colour gets displayed on the monitor as (0.218, 0, 0)
- Once we double the dark-red light in linear space, it actually becomes 4.5 times as bright on the monitor!

> CRTs and monitors correct the displayed light due to how we perceive light.

We assumed we were working in linear space, but we've been working in the monitor's output space, so all colours and lighting variables we configured weren't physically correct. Thus, we often set lighting values brighter than they should be (since the monitors darken them).

![[34-0-example.png]]

With gamma correction, the colour values work more nicely together and darker areas show more details.