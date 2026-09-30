## FL Studio / Wine dialog workaround

This fork is based on current xwayland-satellite `main` and contains a workaround for FL Studio dialogs under Wine.

Two separate issues were involved:

- Fractional-scale popup shivering was fixed upstream by rounding popup sizes up when reconfiguring them.
- FL Studio's Export/Rendering dialog was being classified by xwayland-satellite as an `xdg_popup` because it is a fixed-size, undecorated, transient X11 `_NET_WM_WINDOW_TYPE_DIALOG`.

FL Studio expects that dialog to behave like a normal movable window. When represented as an `xdg_popup`, dragging it caused large position jumps and could leave mouse interaction broken.

This fork changes the X11 `DIALOG` role handling so those windows are created as Wayland toplevels instead.

With FL Studio under Wine + niri, this results in:

- no fractional-scaling shiver
- normal window dragging
- no window flinging across the screen
- no loss of mouse interaction after dragging
- the dialog appearing as a normal niri-managed window

### Caveat

The custom patch currently affects all X11 `_NET_WM_WINDOW_TYPE_DIALOG` windows, not only FL Studio. It is therefore a broad workaround rather than an FL-specific fix.
