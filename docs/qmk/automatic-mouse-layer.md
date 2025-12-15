[See documentation here](https://docs.qmk.fm/features/pointing_device#pointing-device-auto-mouse)

The AML will automatically activate as soon as the pointing device is active, and deactivate the target layer after a set time.

Additionally, if any key that is defined as a mouse key is pressed, then the layer will be held as long as the key is pressed and the timer will be reset on key release. When a non-mouse key is pressed, then the layer is deactivated early (with some exceptions).


```c
// in config.h:
#define POINTING_DEVICE_AUTO_MOUSE_ENABLE
// only required if not setting mouse layer elsewhere
#define AUTO_MOUSE_DEFAULT_LAYER <index of your mouse layer>

// in keymap.c:
void pointing_device_init_user(void) {
    set_auto_mouse_layer(<mouse_layer>); // only required if AUTO_MOUSE_DEFAULT_LAYER is not set to index of <mouse_layer>
    set_auto_mouse_enable(true);         // always required before the auto mouse feature will work
}
```

Because the auto mouse feature can be disabled/enabled during runtime and starts as disabled by default, it must be enabled by calling `set_auto_mouse_layer(true);` somewhere in firmware before the feature will work.