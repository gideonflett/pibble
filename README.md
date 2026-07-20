<h1 align=center>
    pibble
</h1>

| Clock |
|---|
| ![Clock](https://raw.githubusercontent.com/kianblakley/pibble/assets/assets/clock.png) |

| App drawer |
|---|
| ![App drawer](https://raw.githubusercontent.com/kianblakley/pibble/assets/assets/appdrawer.png) |

| Wallpaper selector |
|---|
| ![Wallpaper selector](https://raw.githubusercontent.com/kianblakley/pibble/assets/assets/wallpaperselector.png) |

| Clipboard history |
|---|
| ![Clipboard history](https://raw.githubusercontent.com/kianblakley/pibble/assets/assets/clipboardhistory.png) |

| Flyouts |
|---|
| ![Flyouts](https://raw.githubusercontent.com/kianblakley/pibble/assets/assets/flyouts.png) |

| Settings |
|---|
| ![Settings](https://raw.githubusercontent.com/kianblakley/pibble/assets/assets/settings.png) |

| Power button |
|---|
| ![Power button](https://raw.githubusercontent.com/kianblakley/pibble/assets/assets/powerbutton.png) |

## Installation

**1. Install dependencies**

Necessary:

| Dependency | Use |
|---|---|
| [Quickshell](https://github.com/quickshell-mirror/quickshell) | Runs the shell |
| A Wayland compositor, e.g. [Niri](https://github.com/YaLTeR/niri), [Hyprland](https://github.com/hyprwm/Hyprland) | Hosts the shell |

Optional (required for full feature set):

| Dependency | Use |
|---|---|
| [cliphist](https://github.com/sentriz/cliphist) | Clipboard history |
| [ImageMagick](https://github.com/ImageMagick/ImageMagick) | Wallpaper/clip thumbnails |
| [awww](https://codeberg.org/LGFae/awww) | Sets the wallpaper (can use any program via settings) |
| [matugen](https://github.com/InioX/matugen) | Wallpaper-derived color theme |


**2. Clone**

```sh
git clone https://github.com/kianblakley/pibble.git
cd pibble
```

**3. Start the daemon**

```sh
./pibble
```

**4. Toggle the launcher**

```sh
./pibble toggle
```

## Core Keybindings

| Key | Action |
|---|---|
| `Tab` | Navigate pages |
| `Ctrl+P` (or swipe down) | Reveal power button |
| `Ctrl+S` | Open settings |
| `Escape` | Close the launcher |

## Plugins

Every subdirectory of `~/.config/pibble/plugins/` that contains a `page.qml` is loaded as an extra launcher page. Plugin pages join the Tab cycle and appear in Settings → Pages as a chip (click to enable/disable, drag to reorder), exactly like the built-in pages. The leftmost enabled chip is the page the launcher opens on.

> [!CAUTION]
> Plugins are ordinary QML: they can import Quickshell and spawn processes with your user's permissions. Install only plugins you trust.

The root item of `page.qml` is instantiated once at daemon startup, resized to the full launcher area, and shown while its page is active.

Required:

| Property | Meaning |
|---|---|
| `property string pluginId` | Unique page id. Must not be `clock`, `apps`, `walls`, `clips`, or `settings`, or collide with another plugin — rejected plugins surface as a notification. |

Optional, read by the launcher:

| Member | Meaning |
|---|---|
| `property string title` | Display name (defaults to the pluginId) |
| `function nav(dx, dy)` | Called for arrow keys / scroll wheel while the page is active (`dx`/`dy` are -1, 0, or 1) |
| `function activate()` | Called for the launch keybind (Enter) |
| `property string searchText` | If declared, receives what the user types while the page is active; if absent, typing jumps to the app search like on the clock page |

Injected by the launcher after load — declare it to receive the shell API:

| Member | Meaning |
|---|---|
| `property var shell` | Object with `accent`, `fg`, `muted`, `surface` (colors), `fontFamily`, `fontScale`, `activePane`, `launcherShown`, `close()`, `animMs(ms)` |

Escape, Tab/cycle, settings, and power keybinds stay with the launcher. A minimal plugin:

```qml
// ~/.config/pibble/plugins/hello/page.qml
import QtQuick

Item {
    readonly property string pluginId: "hello"
    property var shell: null

    Text {
        anchors.centerIn: parent
        text: "hello from a plugin"
        color: shell ? shell.fg : "white"
        font { family: shell ? shell.fontFamily : ""; pixelSize: 24 }
    }
}
```

## Namespaces

Each window has a layer-shell namespace which can be used to apply background effects in your compositor's configuration file.

| Namespace | Window |
|---|---|
| `pibble-launcher` | Main launcher |
| `pibble-notifications` | Notification flyout |
| `pibble-volume` | Volume OSD |



