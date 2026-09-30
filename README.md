FL Studio / Wine dialog workaround
This fork changes _NET_WM_WINDOW_TYPE_DIALOG windows to be represented as Wayland toplevels instead of xdg popups.
This fixes FL Studio dialogs under niri + xwayland-satellite that otherwise exhibit:
- window-edge shivering with fractional scaling
- windows flinging across the screen while dragging
- loss of mouse interaction after attempting to drag
Tested with FL Studio 20 under Wine on niri with a 1.25× scaled output.
This is currently a broad workaround and may affect applications that rely on fixed undecorated dialogs being treated as popups.
