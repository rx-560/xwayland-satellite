## FL Studio / Wine dialog workaround

This fork is based on current xwayland-satellite `main` and contains a workaround for FL Studio dialogs under Wine.

Two separate issues were involved:

- Fractional-scale popup shivering was fixed upstream by rounding popup sizes up when reconfiguring them.
- FL Studio's settings windows were being classified by xwayland-satellite as an `xdg_popup` because it is a fixed-size, undecorated, transient X11 `_NET_WM_WINDOW_TYPE_DIALOG`.

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


## Updating to a new upstream xwayland-satellite version

This fork is built from the `fl-dialog-toplevel` branch and installed as a separate binary:

```text
~/.local/bin/xwayland-satellite-fl
```

Because of that, updating a system-installed xwayland-satellite package will not update the binary used by the FL Studio launcher.

To update this fork:

1. Pull the latest changes:
   ```bash
   cd ~/src/xwayland-satellite
   git switch fl-dialog-toplevel
   git pull --ff-only
   ```
2. Rebuild it using the same configuration:
   ```bash
   CARGO_TARGET_DIR=target-fl \
   cargo build --release --features fontconfig,systemd
   ```
3. Replace the installed custom binary:
   ```bash
   cp target-fl/release/xwayland-satellite \
    ~/.local/bin/xwayland-satellite-fl
   ```
4. Restart FL Studio so the launcher starts the new xwayland-satellite build.

If the fork is rebased onto a newer upstream xwayland-satellite release, the custom dialog handling may need to be updated if upstream changes the same window-role code.
