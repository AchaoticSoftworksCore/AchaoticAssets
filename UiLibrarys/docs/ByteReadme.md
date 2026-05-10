# Byte UI Library

Welcome to **Byte UI Library**, a minimal and performance-focused Roblox UI framework built around a module-centric design. Byte organizes everything into tabs, modules, and per-module settings — keeping interfaces clean and compact. It features automatic config persistence, responsive scaling for PC and mobile, a Safe Mode disconnect button, and smooth exponential animations throughout.

---

## Features

* **Module-Centric Layout:** Every setting lives inside a collapsible module row, keeping the UI compact and organized.
* **Automatic Config Persistence:** All flag values are automatically saved to and loaded from a local JSON file — no manual save calls needed.
* **Per-Element Flags:** Every interactive element is tied to a unique flag that persists across sessions.
* **Two-Column Layout:** Modules can be placed in a left or right column per tab for structured organization.
* **Safe Mode:** A built-in disconnect button that cleanly destroys the UI and severs all connections.
* **Responsive Scaling:** Auto-scales to screen size for both PC and mobile devices.
* **Mobile Support:** A floating toggle button is automatically shown on mobile platforms.
* **Insert Key Toggle:** PC users can hide/show the UI with the `Insert` key.

---

## Installation & Usage

Load the library with:

```lua
local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/AchaoticSoftworksCore/AchaoticAssets/refs/heads/main/UiLibrarys/Byte.luau"))()

Library.name = "MyScript"
Library.parent = game:GetService("CoreGui")

local Tabs = Library:create()
```

---

## Table of Contents

* [Overview](#overview)
* [Core Structure](#core-structure)
* [Config System](#config-system)
* [UI Elements](#ui-elements)
  * [Tabs](#tabs)
  * [Modules](#modules)
  * [Toggle](#toggle)
  * [Slider](#slider)
  * [Dropdown](#dropdown)
* [Safe Mode](#safe-mode)
* [Custom Example](#custom-example)
* [Best Practices](#best-practices)

---

## Overview

Byte's hierarchy is:

```
Library:create()
  └── TabsController
        └── TabsController.create_tab(text, image)
              └── ModuleController
                    └── ModuleController.create_module({...}, callback)
                          └── SettingsController
                                ├── SettingsController.create_toggle({...})
                                ├── SettingsController.create_slider({...})
                                └── SettingsController.create_dropdown({...}, callback)
```

All interactive elements are tied to a **flag** string. Flags are stored in `Library.flags` and automatically persisted to `Nurysium/configs/{GameId}.json` on every change. When the library loads, it reads this file and restores all previous values automatically.

---

## Core Structure

| Field / Function | Type | Description |
| --- | --- | --- |
| `Library.name` | `string` | Name of the ScreenGui instance |
| `Library.parent` | `Instance` | Parent for the ScreenGui (usually `CoreGui`) |
| `Library.flags` | `table` | All current flag values, keyed by flag string |
| `Library.open` | `boolean` | Whether the UI is currently visible |
| `Library.disconnected` | `boolean` | Whether the UI has been safely disconnected |
| `Library.mobile` | `boolean` | `true` on iOS/Android platforms |
| `Library.scale` | `number` | Current UI scale value |
| `Library:create()` | function | Builds and renders the UI, returns `TabsController` |
| `Library:get_screen_scale()` | function | Recalculates and updates `Library.scale` |

---

## Config System

Byte automatically handles saving and loading. You do not need to call save manually — every flag update triggers a save internally.

Configs are stored at:
```
Nurysium/configs/{GameId}.json
```

On load, all saved flag values are restored automatically, so sliders, toggles, and dropdowns reflect the user's last session without any extra code.

**Manual access** (if needed):

```lua
-- Read a flag value at any time
print(Library.flags["my_flag"])
```

The `ConfigsController` is internal; direct calls are not necessary in normal usage.

---

## UI Elements

### Tabs

Tabs are the top-level navigation entries displayed in the left sidebar. Each tab has its own independent left and right module columns.

```lua
local myTab = Tabs.create_tab(text, image)
```

| Parameter | Type | Description |
| --- | --- | --- |
| `text` | `string` | Label shown on the tab button |
| `image` | `string` | Roblox asset ID for the tab icon |

Returns: `ModuleController`

**Example:**

```lua
local CombatTab = Tabs.create_tab("Combat", "rbxassetid://10734888000")
local UtilityTab = Tabs.create_tab("Utility", "rbxassetid://10709818996")
```

> The first tab created is **not** auto-selected — clicking it triggers selection. Design your layout with this in mind.

---

### Modules

Modules are the primary containers within a tab. Each module is a clickable row with a title that acts as its own toggle. When enabled, the module's background highlights and its `callback` fires with `true`. Modules also contain nested settings elements (toggles, sliders, dropdowns).

```lua
local Settings = ModuleController.create_module({
    text = "Module Title",
    flag = "unique_flag",
    side = "left"
}, function(value)
    -- fires when module is clicked/toggled
end)
```

| Field | Type | Description |
| --- | --- | --- |
| `text` | `string` | Label displayed on the module row |
| `flag` | `string` | Unique key used for config persistence |
| `side` | `string` | Column placement: `"left"` or `"right"` |
| `callback` | `(value: boolean) -> ()` | Called with the current toggle state on each click |

Returns: `SettingsController`

> Note the `.` call syntax — the settings table acts as `self`. Always use `ModuleController.create_module({...}, callback)`, not `:`.

**Example:**

```lua
local SpeedSettings = CombatTab.create_module({
    text = "Speed Hack",
    flag = "speed_hack",
    side = "left"
}, function(enabled)
    local hum = game.Players.LocalPlayer.Character.Humanoid
    hum.WalkSpeed = enabled and 80 or 16
end)
```

---

### Toggle

A compact boolean toggle inside a module's settings panel. Displays a label and a small checkbox-style indicator.

```lua
Settings.create_toggle({
    title = "Toggle Label",
    flag = "unique_toggle_flag"
})
```

| Field | Type | Description |
| --- | --- | --- |
| `title` | `string` | Label shown next to the toggle |
| `flag` | `string` | Unique key used for config persistence |

Returns: None

The toggle value is accessible at `Library.flags["unique_toggle_flag"]` (`true`/`false`).

> Note the `.` call syntax — the settings table acts as `self`.

**Example:**

```lua
Settings.create_toggle({
    title = "Visible Only",
    flag = "visible_only"
})

-- Read the value elsewhere:
if Library.flags["visible_only"] then
    -- only target visible players
end
```

---

### Slider

A horizontal drag slider that produces an integer value from `0` to `100`.

```lua
Settings.create_slider({
    title = "Slider Label",
    flag = "unique_slider_flag",
    value = 50
})
```

| Field | Type | Description |
| --- | --- | --- |
| `title` | `string` | Label shown below the slider track |
| `flag` | `string` | Unique key used for config persistence |
| `value` | `number` | Default value (0–100 integer) |

Returns: None

The slider fires no callback directly — read `Library.flags["flag"]` wherever you need the value, or poll it from a loop/heartbeat. The value is always an integer between `0` and `100`.

> Note the `.` call syntax — the settings table acts as `self`.

**Example:**

```lua
Settings.create_slider({
    title = "FOV",
    flag = "aimbot_fov",
    value = 30
})

-- Usage elsewhere:
game.Workspace.CurrentCamera.FieldOfView = Library.flags["aimbot_fov"]
```

---

### Dropdown

A scrollable list of options inside a module's settings panel. Only one option can be selected at a time. The callback fires immediately when the user selects an option.

```lua
Settings.create_dropdown({
    title = "Dropdown Label",
    flag = "unique_dropdown_flag",
    mods = {"Option1", "Option2", "Option3"},
    default_flag = "Option1"
}, function(value)
    print("Selected:", value)
end)
```

| Field | Type | Description |
| --- | --- | --- |
| `title` | `string` | Label shown above the dropdown list |
| `flag` | `string` | Unique key used for config persistence |
| `mods` | `{ string }` | List of selectable option strings |
| `default_flag` | `string?` | Pre-selected option on first load |
| `callback` | `(value: string) -> ()` | Called when the user selects an option |

Returns: None

> Note the `.` call syntax — the settings table acts as `self`.

**Example:**

```lua
Settings.create_dropdown({
    title = "Target Part",
    flag = "target_part",
    mods = {"Head", "Torso", "HumanoidRootPart"},
    default_flag = "Head"
}, function(value)
    print("Now targeting:", value)
end)
```

---

## Safe Mode

Byte includes a built-in **Safe Mode** button displayed below the tab list. Hovering reveals a disconnect icon; clicking it triggers a 2-second animated sequence that then:

1. Closes and hides the UI with an animation
2. Destroys the `ScreenGui` instance
3. Disconnects all internal connections via `Connections:abadone()`
4. Sets `Library.disconnected = true` and clears `Library.flags`

This is useful for quickly cleaning up the UI without restarting the game. After Safe Mode activates, the library is fully inert — no further calls should be made.

---

## Custom Example

```lua
local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/AchaoticSoftworksCore/AchaoticAssets/refs/heads/main/UiLibrarys/Byte.luau"))()

Library.name = "MyHub"
Library.parent = game:GetService("CoreGui")

local Tabs = Library:create()

-- =====================
-- TABS
-- =====================

local CombatTab = Tabs.create_tab("Combat", "rbxassetid://10734888000")
local UtilityTab = Tabs.create_tab("Utility", "rbxassetid://10709818996")

-- =====================
-- COMBAT TAB
-- =====================

local AimbotSettings = CombatTab.create_module({
    text = "Aimbot",
    flag = "aimbot_enabled",
    side = "left"
}, function(enabled)
    print("Aimbot:", enabled)
end)

AimbotSettings.create_dropdown({
    title = "Target Part",
    flag = "aimbot_part",
    mods = {"Head", "Torso", "HumanoidRootPart"},
    default_flag = "Head"
}, function(value)
    print("Target set to:", value)
end)

AimbotSettings.create_slider({
    title = "FOV",
    flag = "aimbot_fov",
    value = 30
})

AimbotSettings.create_toggle({
    title = "Visible Only",
    flag = "aimbot_visible_only"
})

local SilentAimSettings = CombatTab.create_module({
    text = "Silent Aim",
    flag = "silent_aim",
    side = "right"
}, function(enabled)
    print("Silent Aim:", enabled)
end)

SilentAimSettings.create_dropdown({
    title = "Hit Part",
    flag = "silent_hit_part",
    mods = {"Head", "Torso", "Random"},
    default_flag = "Head"
}, function(value)
    print("Silent aim targeting:", value)
end)

SilentAimSettings.create_toggle({
    title = "Team Check",
    flag = "silent_team_check"
})

-- =====================
-- UTILITY TAB
-- =====================

local SpeedSettings = UtilityTab.create_module({
    text = "Speed Hack",
    flag = "speed_enabled",
    side = "left"
}, function(enabled)
    local hum = game.Players.LocalPlayer.Character and
        game.Players.LocalPlayer.Character:FindFirstChildOfClass("Humanoid")

    if hum then
        hum.WalkSpeed = enabled and Library.flags["speed_value"] or 16
    end
end)

SpeedSettings.create_slider({
    title = "Walk Speed",
    flag = "speed_value",
    value = 50
})

SpeedSettings.create_toggle({
    title = "Reset on Death",
    flag = "speed_reset_death"
})

local MiscSettings = UtilityTab.create_module({
    text = "Misc",
    flag = "misc_enabled",
    side = "right"
}, function(enabled)
    print("Misc module:", enabled)
end)

MiscSettings.create_dropdown({
    title = "Noclip Mode",
    flag = "noclip_mode",
    mods = {"Off", "Full", "Partial"},
    default_flag = "Off"
}, function(value)
    print("Noclip:", value)
end)

MiscSettings.create_toggle({
    title = "Infinite Jump",
    flag = "infinite_jump"
})

-- Read flags anywhere:
game:GetService("RunService").Heartbeat:Connect(function()
    if Library.flags["infinite_jump"] then
        -- handle infinite jump
    end
end)
```

---

## Best Practices

* **Unique Flags:** Every element across all modules must have a unique flag string — duplicate flags will silently overwrite each other's saved values.
* **Use `.` not `:` for elements:** `create_module`, `create_toggle`, `create_slider`, and `create_dropdown` all use the settings table as `self` — always call them with `.` and pass the config table as the first argument.
* **Poll Sliders:** Sliders don't fire a callback — read `Library.flags["flag"]` from a `RunService.Heartbeat` or inside your module callback when you need the live value.
* **Check `disconnected`:** If your script runs loops or heartbeat connections, guard them with `if Library.disconnected then return end` so they stop cleanly after Safe Mode.
* **Side Balance:** Spread modules evenly between `"left"` and `"right"` columns to keep the window visually balanced.
* **Insert Key:** Let users know they can press `Insert` to toggle the UI — or show it in a startup print. Mobile users get a floating button automatically.
