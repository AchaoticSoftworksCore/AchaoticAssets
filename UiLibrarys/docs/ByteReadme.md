# Byte UI Library

> A sleek, minimal Roblox exploit UI library with smooth animations, config persistence, and mobile support.

---

## Table of Contents

- [Getting Started](#getting-started)
- [Library Setup](#library-setup)
- [Creating a Window](#creating-a-window)
- [Tabs](#tabs)
- [Modules](#modules)
- [Settings (Elements)](#settings-elements)
  - [Toggle](#toggle)
  - [Slider](#slider)
  - [Dropdown](#dropdown)
- [Config System](#config-system)
- [Keybinds & Mobile](#keybinds--mobile)
- [Safe Mode / Disconnect](#safe-mode--disconnect)
- [Full Example](#full-example)

---

## Getting Started

Load the library using your executor's HTTP functions:

```lua
local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/AchaoticSoftworksCore/AchaoticAssets/refs/heads/main/UiLibrarys/Byte.luau"))()
```

---

## Library Setup

Before creating any UI, initialize the library by calling `:create()` with the required options:

```lua
Library:create()
```

**Required fields on `Library` before calling `:create()`:**

| Field    | Type     | Description                          |
|----------|----------|--------------------------------------|
| `name`   | `string` | The name of the ScreenGui instance   |
| `parent` | `Instance` | Where the GUI is parented (e.g. `game.CoreGui`) |

```lua
Library.name = "MyScript"
Library.parent = game.CoreGui

local UI = Library:create()
```

> `:create()` returns a `TabsController` used to build the rest of your UI.

---

## Creating a Window

The window is created automatically when you call `Library:create()`. It produces a centered `640x355` frame with a tab panel on the left and dual section columns on the right.

---

## Tabs

Tabs appear in the left sidebar. Each tab has a label and an icon.

```lua
local Tab = UI.create_tab("Combat", "rbxassetid://YOUR_ICON_ID")
```

| Parameter | Type     | Description              |
|-----------|----------|--------------------------|
| `text`    | `string` | Label shown on the tab   |
| `image`   | `string` | Asset ID for the icon    |

`create_tab` returns a `ModuleController`.

---

## Modules

Modules are collapsible containers inside a tab. Clicking a module header toggles it open/closed and fires a callback.

```lua
local Module = Tab:create_module(function(enabled)
    -- fires whenever the module is toggled
    print("Module is now:", enabled)
end)
```

**Required fields on `Tab` before calling `:create_module()`:**

| Field   | Type     | Description                                |
|---------|----------|--------------------------------------------|
| `text`  | `string` | Module header label                        |
| `flag`  | `string` | Unique key used for config saving/loading  |
| `side`  | `string` | `"left"` or `"right"` — which column it appears in |

```lua
Tab.text = "Aimbot"
Tab.flag = "aimbot_enabled"
Tab.side = "left"

local Module = Tab:create_module(function(enabled)
    -- your logic here
end)
```

`create_module` returns a `SettingsController`.

---

## Settings (Elements)

Settings are elements placed **inside** a module. Access them through the returned `SettingsController`.

---

### Toggle

A simple on/off switch.

```lua
Module.title = "Silent Aim"
Module.flag  = "silent_aim"

Module:create_toggle()
```

| Field   | Type     | Description                               |
|---------|----------|-------------------------------------------|
| `title` | `string` | Label shown next to the toggle            |
| `flag`  | `string` | Unique config key for this toggle's state |

The toggle value is automatically saved to and loaded from the config file.

---

### Slider

A draggable 0–100 value slider.

```lua
Module.title = "FOV"
Module.flag  = "fov_value"
Module.value = 50  -- default value (0–100)

Module:create_slider()
```

| Field   | Type     | Description                            |
|---------|----------|----------------------------------------|
| `title` | `string` | Label displayed below the slider       |
| `flag`  | `string` | Unique config key                      |
| `value` | `number` | Default value between `0` and `100`    |

The current value is stored in `Library.flags[flag]` as a number.

---

### Dropdown

A scrollable list of selectable options.

```lua
Module.title        = "Hit Part"
Module.flag         = "hit_part"
Module.mods         = { "Head", "Torso", "HumanoidRootPart" }
Module.default_flag = "Head"

Module:create_dropdown(function(selected)
    print("Selected:", selected)
end)
```

| Field          | Type       | Description                                  |
|----------------|------------|----------------------------------------------|
| `title`        | `string`   | Label shown above the dropdown               |
| `flag`         | `string`   | Unique config key                            |
| `mods`         | `table`    | Array of string options                      |
| `default_flag` | `string`   | Default selected value (optional)            |
| `callback`     | `function` | Fires with the selected value on each change |

---

## Config System

Configs are saved automatically per-game using `game.GameId` as the filename. They are stored at:

```
Nurysium/configs/<GameId>.json
```

All flags (toggles, sliders, dropdowns) are persisted across sessions without any extra setup — values are written on every interaction and loaded on startup.

You can access the current flag state at any time via:

```lua
Library.flags["your_flag_key"]
```

---

## Keybinds & Mobile

| Action        | Input             |
|---------------|-------------------|
| Toggle UI     | `Insert` key      |
| Toggle UI (Mobile) | Tap the floating button |

The UI auto-scales based on screen resolution. On mobile, a small floating button appears in the bottom-left corner to show/hide the window.

---

## Safe Mode / Disconnect

A **Safe Mode** button is docked below the tab panel. Clicking it:

1. Plays a disconnect animation
2. Closes and destroys the UI after 2 seconds
3. Disconnects all internal connections
4. Clears all flags from memory

This is useful for quickly hiding the UI and cleaning up all hooks when needed.

---

## Full Example

```lua
local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/AchaoticSoftworksCore/AchaoticAssets/refs/heads/main/UiLibrarys/Byte.luau"))()

Library.name   = "MyScript"
Library.parent = game.CoreGui

local UI = Library:create()

-- Tab
local CombatTab = UI.create_tab("Combat", "rbxassetid://134992015790041")

-- Module (left column)
CombatTab.text = "Aimbot"
CombatTab.flag = "aimbot_module"
CombatTab.side = "left"

local AimbotModule = CombatTab:create_module(function(enabled)
    -- enable/disable aimbot logic
end)

-- Toggle inside the module
AimbotModule.title = "Silent Aim"
AimbotModule.flag  = "silent_aim"
AimbotModule:create_toggle()

-- Slider inside the module
AimbotModule.title = "FOV"
AimbotModule.flag  = "aimbot_fov"
AimbotModule.value = 60
AimbotModule:create_slider()

-- Dropdown inside the module
AimbotModule.title        = "Hit Part"
AimbotModule.flag         = "hit_part"
AimbotModule.mods         = { "Head", "Torso", "HumanoidRootPart" }
AimbotModule.default_flag = "Head"
AimbotModule:create_dropdown(function(selected)
    print("Targeting:", selected)
end)
```

---

## Notes

- Every `flag` key must be **unique** across your entire script to avoid state conflicts.
- `side` must be either `"left"` or `"right"` — modules are split into two columns per tab.
- Slider values are always integers between `0` and `100`.
- The library uses `cloneref` internally — make sure your executor supports it.
