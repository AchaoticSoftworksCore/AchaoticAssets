# Euphoria UI Library

Welcome to **Euphoria UI Library**, a clean and minimal Roblox UI framework built for creating sleek, feature-rich interfaces with ease. Whether you're building tabs, modules, dropdowns, sliders, checkboxes, or more — Euphoria gives you a powerful, modular, and visually polished foundation with smooth animations, search support, resize/drag functionality, and full mobile compatibility.

> Made by **riq** and **enrithy**

---

## Features

* **Modular Design:** Organize UI elements into tabs and expandable modules for clean, structured layouts.
* **Customizable Components:** Dropdowns, sliders, checkboxes, textboxes, and buttons — all with animated transitions.
* **Global Search Bar:** Built-in header search that filters all elements across every tab in real time.
* **In-Game Notifications:** Inform users with beautiful, stacked, animated notifications with a progress bar.
* **Responsive UI:** Auto-scales and adapts layout for PC and mobile devices.
* **Resize & Drag:** Users can freely drag and resize the window.
* **Toggle Visibility:** Press `Insert` to hide/show the UI with a smooth animation.
* **Keybind Support:** Every module supports an assignable keybind to toggle it independently.

---

## Installation & Usage

Load the library with:

```lua
local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/AchaoticSoftworksCore/AchaoticAssets/refs/heads/main/UiLibrarys/Euphoria.luau"))()

local Window = Library:init("My Hub")
```

---

## Table of Contents

* [Overview](#overview)
* [Core Structure](#core-structure)
* [UI Elements](#ui-elements)
  * [Tabs](#tabs)
  * [Modules](#modules)
  * [Dropdown](#dropdown)
  * [Slider](#slider)
  * [Checkbox](#checkbox)
  * [Divider](#divider)
  * [Textbox](#textbox)
  * [Button](#button)
* [Notifications](#notifications)
* [Custom Example](#custom-example)
* [Best Practices](#best-practices)

---

## Overview

Euphoria is structured around a central `Library` object. Calling `:init()` creates and renders the main window, after which you build your UI by creating tabs and populating them with modules and elements. Modules are expandable containers that support their own nested elements, making it easy to group related settings together under a single toggle.

---

## Core Structure

| Function | Description | Parameters | Returns |
| --- | --- | --- | --- |
| `Library:init(title)` | Creates and renders the main UI window | `title: string?` | `Library` |
| `Library:create_tab(name, icon)` | Creates a new navigation tab | `name: string, icon: string?` | `Tab` |
| `Library:notify(index)` | Displays an animated notification | `{title, content, duration?, notify_type}` | None |
| `Library:get_device()` | Detects and stores the device type | None | None |
| `Library:get_screen_scale()` | Calculates and stores the UI scale | None | None |
| `Library:mobile_ui_scale()` | Applies mobile-specific text/icon scaling | None | None |
| `Library:mobile_controls()` | Adds touch hitboxes to small buttons | None | None |

**Internal state fields:**

| Field | Type | Description |
| --- | --- | --- |
| `Library.__current_tab` | `Tab?` | The currently active tab |
| `Library.__tabs` | `{ Tab }` | All registered tabs |
| `Library.__all_elements` | `{ {name, frame} }` | All searchable elements |
| `Library.__minimized` | `boolean` | Whether the UI is hidden |
| `Library.__device` | `string?` | `"pc"`, `"mobile"`, or `"unknown"` |

---

## UI Elements

### Tabs

Tabs are the top-level navigation containers displayed in the left sidebar.

```lua
local tab = Window:create_tab(name, icon)
```

* **name:** `string` — Tab label
* **icon:** `string?` — Optional Roblox asset ID for a tab icon

Returns: `Tab`

**Example:**

```lua
local MainTab = Window:create_tab("Main", "rbxassetid://10734888000")
local SettingsTab = Window:create_tab("Settings")
```

---

### Modules

Modules are expandable rows that act as containers for nested elements. They include a toggle switch, an optional keybind, and can hold sliders, checkboxes, textboxes, buttons, dropdowns, and dividers.

```lua
local module = tab:create_module({
    title = "Module Title",
    default = false,
    callback = function(state) end
})
```

| Field | Type | Description |
| --- | --- | --- |
| `title` | `string` | Label displayed on the module row |
| `default` | `boolean?` | Initial toggle state (default: `false`) |
| `callback` | `(state: boolean) -> ()` | Called when the module is toggled |

Returns: `Module`

Keybinds can be assigned by clicking the keyboard icon on the module row at runtime.

**Example:**

```lua
local SpeedModule = MainTab:create_module({
    title = "Speed Hack",
    default = false,
    callback = function(state)
        game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = state and 50 or 16
    end
})
```

---

### Dropdown

Displays a list of selectable options. Supports both single and multi-selection, with a built-in search bar.

**On a Tab:**

```lua
local dropdown = tab:create_dropdown({
    title = "Dropdown Title",
    options = {"Option1", "Option2", "Option3"},
    default = "Option1",
    multi_selection = false,
    callback = function(value) end
})
```

**On a Module:**

```lua
local dropdown = module:create_dropdown({
    title = "Dropdown Title",
    options = {"Option1", "Option2"},
    default = {"Option1"},
    multi_selection = true,
    callback = function(value) end
})
```

| Field | Type | Description |
| --- | --- | --- |
| `title` | `string` | Label for the dropdown |
| `options` | `{ string }` | List of selectable options |
| `default` | `string \| { string }?` | Pre-selected option(s) |
| `multi_selection` | `boolean?` | Allow multiple selections (default: `false`) |
| `callback` | `(value: string \| { string }) -> ()` | Called when selection changes |

**Extra Functions:**

* `dropdown:Set(value)` — Programmatically set the selected value(s).

Returns: `{ Set: (value) -> () }`

---

### Slider

A draggable numeric slider for selecting values within a range.

**On a Tab:**

```lua
local slider = tab:create_slider({
    title = "Slider Title",
    minimum = 0,
    maximum = 100,
    default = 50,
    rounding = 0,
    callback = function(value) end
})
```

**On a Module:**

```lua
local slider = module:create_slider({
    title = "Slider Title",
    minimum = 0,
    maximum = 100,
    default = 50,
    rounding = 5,
    callback = function(value) end
})
```

| Field | Type | Description |
| --- | --- | --- |
| `title` | `string` | Label for the slider |
| `minimum` | `number?` | Minimum value (default: `0`) |
| `maximum` | `number?` | Maximum value (default: `100`) |
| `default` | `number?` | Starting value (default: `minimum`) |
| `rounding` | `number?` | Snap interval — `0` for none (default: `0`) |
| `callback` | `(value: number) -> ()` | Called on value change |

**Extra Functions:**

* `slider:Set(value)` — Programmatically set the slider value.

Returns: `{ Set: (value) -> () }`

---

### Checkbox

A simple boolean toggle displayed as a checkmark box.

**On a Tab:**

```lua
tab:create_checkbox({
    title = "Checkbox Title",
    default = false,
    callback = function(state) end
})
```

**On a Module:**

```lua
module:create_checkbox({
    title = "Checkbox Title",
    default = false,
    callback = function(state) end
})
```

| Field | Type | Description |
| --- | --- | --- |
| `title` | `string` | Label for the checkbox |
| `default` | `boolean?` | Initial state (default: `false`) |
| `callback` | `(state: boolean) -> ()` | Called when toggled |

Returns: None

---

### Divider

A visual separator used to group elements. Accepts an optional label.

**On a Tab:**

```lua
tab:create_divider("Section Name")
```

**On a Module:**

```lua
module:create_divider("Sub-Section")
```

| Parameter | Type | Description |
| --- | --- | --- |
| `text` | `string?` | Optional section label |

Returns: None

---

### Textbox

A text input field. The callback fires when the user presses Enter.

**On a Tab:**

```lua
local textbox = tab:create_textbox({
    title = "Textbox Title",
    placeholder = "Enter text...",
    callback = function(text) end
})
```

**On a Module:**

```lua
local textbox = module:create_textbox({
    title = "Textbox Title",
    placeholder = "Enter text...",
    callback = function(text) end
})
```

| Field | Type | Description |
| --- | --- | --- |
| `title` | `string` | Label for the textbox |
| `placeholder` | `string?` | Placeholder hint text |
| `callback` | `(text: string) -> ()` | Called when Enter is pressed |

**Extra Functions:**

* `textbox:Set(text)` — Programmatically set the textbox content.

Returns: `{ Set: (text) -> () }`

---

### Button

A clickable button row with a ripple feedback animation.

**On a Tab:**

```lua
tab:create_button({
    title = "Button Title",
    callback = function() end
})
```

**On a Module:**

```lua
module:create_button({
    title = "Button Title",
    callback = function() end
})
```

| Field | Type | Description |
| --- | --- | --- |
| `title` | `string` | Label displayed on the button |
| `callback` | `() -> ()` | Called when clicked |

Returns: None

---

## Notifications

Display stacked animated notifications with a progress bar. Up to 5 can be active at once; additional ones queue automatically.

```lua
Library:notify({
    title = "Notification Title",
    content = "Your message here.",
    duration = 3,
    notify_type = "normal"
})
```

| Field | Type | Description |
| --- | --- | --- |
| `title` | `string` | Notification heading |
| `content` | `string` | Body message |
| `duration` | `number?` | Display time in seconds (default: `3`) |
| `notify_type` | `string` | Icon style: `"normal"`, `"warn"`, or `"error"` |

---

## Custom Example

```lua
local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/AchaoticSoftworksCore/AchaoticAssets/refs/heads/main/UiLibrarys/Euphoria.luau"))()

local Window = Library:init("My Script")

-- Tabs
local CombatTab = Window:create_tab("Combat", "rbxassetid://10734888000")
local UtilityTab = Window:create_tab("Utility", "rbxassetid://10709818996")

-- =====================
-- COMBAT TAB
-- =====================

CombatTab:create_divider("Aimbot")

local Aimbot = CombatTab:create_module({
    title = "Aimbot",
    default = false,
    callback = function(state)
        Library:notify({
            title = "Aimbot",
            content = state and "Enabled!" or "Disabled!",
            duration = 3,
            notify_type = state and "normal" or "warn"
        })
    end
})

Aimbot:create_dropdown({
    title = "Target Part",
    options = {"Head", "Torso", "Random"},
    default = "Head",
    multi_selection = false,
    callback = function(value)
        print("Targeting:", value)
    end
})

Aimbot:create_slider({
    title = "FOV",
    minimum = 10,
    maximum = 500,
    default = 100,
    rounding = 10,
    callback = function(value)
        print("FOV:", value)
    end
})

Aimbot:create_checkbox({
    title = "Visible Only",
    default = true,
    callback = function(state)
        print("Visible only:", state)
    end
})

CombatTab:create_divider("Misc")

CombatTab:create_checkbox({
    title = "Auto Parry",
    default = false,
    callback = function(state)
        print("Auto Parry:", state)
    end
})

-- =====================
-- UTILITY TAB
-- =====================

UtilityTab:create_divider("Movement")

local SpeedHack = UtilityTab:create_module({
    title = "Speed Hack",
    default = false,
    callback = function(state)
        local hum = game.Players.LocalPlayer.Character.Humanoid
        hum.WalkSpeed = state and 50 or 16
    end
})

SpeedHack:create_slider({
    title = "Walk Speed",
    minimum = 16,
    maximum = 300,
    default = 50,
    rounding = 1,
    callback = function(value)
        game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = value
    end
})

SpeedHack:create_divider("Options")

SpeedHack:create_checkbox({
    title = "Reset on Death",
    default = true,
    callback = function(state)
        print("Reset on death:", state)
    end
})

UtilityTab:create_divider("Tools")

local TargetBox = UtilityTab:create_textbox({
    title = "Target Player",
    placeholder = "Enter username...",
    callback = function(text)
        print("Target set to:", text)
    end
})

UtilityTab:create_button({
    title = "Rejoin Server",
    callback = function()
        game:GetService("TeleportService"):Teleport(game.PlaceId)
    end
})

UtilityTab:create_button({
    title = "Test Notification",
    callback = function()
        Library:notify({
            title = "Hello!",
            content = "Euphoria UI is working.",
            duration = 4,
            notify_type = "normal"
        })
    end
})
```

---

## Best Practices

* **Organize Logically:** Group related settings into modules and use dividers to separate categories within a tab.
* **Use Defaults:** Always provide sensible `default` values so the UI feels ready to use immediately.
* **Handle All States:** Make sure callbacks handle both `true` and `false` (or all dropdown values) to avoid nil errors.
* **Keybinds:** Remind users they can right-click module keybind buttons at runtime — document it in your script's description.
* **Notify on Change:** Use `Library:notify()` to give users feedback on important toggle or action events.
* **Insert Key:** The UI is toggled with the `Insert` key — let users know in your script's header or a startup notification.
