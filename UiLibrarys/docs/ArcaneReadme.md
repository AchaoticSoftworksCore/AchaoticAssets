# Arcane UI Library

Welcome to **Arcane UI Library**, a feature-rich and highly structured Roblox UI framework built for professional script hubs. Arcane features a multi-level layout system (Window → Pages → SubPages → Sections → Elements), a full theme engine, built-in config management, colorpickers, keybinds with multiple modes, a live settings page, and smooth fade/tween animations throughout.

---

## Features

* **Deep Hierarchy:** Window → Pages → SubPages → Sections → Elements, keeping everything organized at scale.
* **Full Theme Engine:** Six theme keys that propagate live to every element via `Library:ChangeTheme()`.
* **Config System:** Save, load, delete, and refresh JSON configs with a single call. A built-in Settings page handles all of this automatically.
* **Sub-Elements:** Toggles and Labels can embed inline Colorpickers and Keybinds directly on their row.
* **Keybind Modes:** Toggle, Hold, and Always — each with its own interaction model.
* **HSV Colorpicker:** A full hue + saturation/value palette picker that opens as a floating window.
* **Resizable & Draggable Window:** Users can drag anywhere and resize from the bottom-right corner.
* **Notifications:** Lightweight stacked notifications with optional icons and configurable duration.
* **Auto-Unload:** Re-executing the script cleanly destroys the previous instance first.

---

## Installation & Usage

```lua
local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/AchaoticSoftworksCore/AchaoticAssets/refs/heads/main/UiLibrarys/Arcane.luau"))()

local Window = Library:Window({
    Name = "My Script",
    SubTitle = "for Game Name",
    ExpiresIn = "30d"
})
```

> **Note:** Change `Library.Folders.Directory` before calling `Window` if you want configs saved under a custom folder name.

---

## Table of Contents

* [Overview](#overview)
* [Core Structure](#core-structure)
* [Theme System](#theme-system)
* [Config System](#config-system)
* [UI Hierarchy](#ui-hierarchy)
  * [Window](#window)
  * [Page](#page)
  * [Category](#category)
  * [SubPage](#subpage)
  * [Section](#section)
* [Elements](#elements)
  * [Toggle](#toggle)
  * [Button](#button)
  * [Slider](#slider)
  * [Dropdown](#dropdown)
  * [Label](#label)
  * [Textbox](#textbox)
* [Sub-Elements](#sub-elements)
  * [Colorpicker](#colorpicker)
  * [Keybind](#keybind)
* [Notifications](#notifications)
* [Built-In Settings Page](#built-in-settings-page)
* [Custom Example](#custom-example)
* [Best Practices](#best-practices)

---

## Overview

Arcane's full hierarchy:

```
Library:Window()
  └── Window:Page()
        ├── Window:Category()          -- sidebar label above page buttons
        └── Page:SubPage()
              └── SubPage:Section()
                    ├── Section:Toggle()
                    │     ├── Toggle:Colorpicker()
                    │     └── Toggle:Keybind()
                    ├── Section:Button()
                    ├── Section:Slider()
                    ├── Section:Dropdown()
                    ├── Section:Label()
                    │     ├── Label:Colorpicker()
                    │     └── Label:Keybind()
                    └── Section:Textbox()
```

All interactive elements are tied to a **Flag** string stored in `Library.Flags`. Flags are serialized to and from JSON by the config system.

---

## Core Structure

| Field / Function | Type | Description |
| --- | --- | --- |
| `Library.Flags` | `table` | All current flag values keyed by flag string |
| `Library.Theme` | `table` | Current theme color values |
| `Library.MenuKeybind` | `string` | Key to toggle window visibility (default: `"Enum.KeyCode.Z"`) |
| `Library.Tween.Time` | `number` | Default tween duration (default: `0.4`) |
| `Library.Tween.Style` | `Enum.EasingStyle` | Default easing style (default: `Quint`) |
| `Library.Tween.Direction` | `Enum.EasingDirection` | Default easing direction (default: `Out`) |
| `Library.FadeSpeed` | `number` | Speed of fade-in/out animations (default: `0.2`) |
| `Library.Folders.Directory` | `string` | Root folder name for saved files |
| `Library.Folders.Configs` | `string` | Config subfolder path |
| `Library:Unload()` | function | Destroys the UI and disconnects all connections |
| `Library:Notification(Name, Duration, Icon)` | function | Shows a notification |
| `Library:GetConfig()` | function | Returns current flags as a JSON string |
| `Library:LoadConfig(Config)` | function | Applies a JSON config string to all flags |
| `Library:DeleteConfig(Config)` | function | Deletes a config file from disk |
| `Library:RefreshConfigsList(Dropdown)` | function | Updates a Dropdown element with all saved config filenames |
| `Library:ChangeTheme(Key, Color)` | function | Changes a theme key and propagates to all themed elements |
| `Library:ToRich(Text, Color)` | function | Wraps text in a rich-text font color tag |
| `Library:CreateSettingsPage(Window)` | function | Adds the built-in Settings page to a window |

---

## Theme System

Arcane uses six named theme keys that apply to UI elements at creation time and update live via `Library:ChangeTheme()`.

| Key | Default Color | Used On |
| --- | --- | --- |
| `Background` | `RGB(16, 18, 18)` | Window background, notification background |
| `Inline` | `RGB(21, 24, 24)` | Section backgrounds, title bar, content area |
| `Element` | `RGB(30, 34, 34)` | Buttons, slider tracks, textboxes, keybind buttons |
| `Accent` | `RGB(255, 255, 255)` | Toggle indicator fill, slider fill, active dropdown options |
| `Border` | `RGB(30, 34, 34)` | Primary `UIStroke` borders |
| `Border 2` | `RGB(56, 62, 62)` | Thicker element borders (toggles, buttons, textboxes) |

**Changing a theme color at runtime:**

```lua
Library:ChangeTheme("Accent", Color3.fromRGB(100, 180, 255))
```

The built-in Settings page includes live colorpickers for every theme key automatically.

---

## Config System

Configs are JSON files saved to `Library.Folders.Configs`. Every flag (toggle state, slider value, dropdown selection, keybind, colorpicker) is included.

```lua
-- Save current state to a file
writefile(Library.Folders.Configs .. "/MyConfig.json", Library:GetConfig())

-- Load from a file
Library:LoadConfig(readfile(Library.Folders.Configs .. "/MyConfig.json"))

-- Delete a config
Library:DeleteConfig("MyConfig.json")

-- Refresh a dropdown element with all saved configs
Library:RefreshConfigsList(MyDropdownElement)
```

All of the above is handled automatically by the built-in Settings page (see [Built-In Settings Page](#built-in-settings-page)).

---

## UI Hierarchy

### Window

The main window frame. Draggable anywhere, resizable from the bottom-right corner.

```lua
local Window = Library:Window({
    Name = "My Script",
    SubTitle = "for Game Name",
    ExpiresIn = "30d"
})
```

| Field | Type | Description |
| --- | --- | --- |
| `Name` | `string` | Script name displayed in the title box |
| `SubTitle` | `string` | Subtitle shown below the name (e.g. game name) |
| `ExpiresIn` | `string` | Subscription expiry shown in the bottom bar |

The window also displays a live **session duration** timer in the bottom bar.

**Methods:**

| Method | Description |
| --- | --- |
| `Window:SetOpen(Bool)` | Fades the window in or out |
| `Window:SetCenter()` | Snaps the anchor back to top-left after dragging |

The window is toggled with `Library.MenuKeybind` (default: `Z`). Change it with:

```lua
Library.MenuKeybind = tostring(Enum.KeyCode.RightShift)
```

---

### Page

Pages appear as buttons in the left sidebar. The first page created is auto-activated.

```lua
local CombatPage = Window:Page({
    Name = "Combat",
    Icon = "136879043989014"
})
```

| Field | Type | Description |
| --- | --- | --- |
| `Name` | `string` | Page label shown in sidebar |
| `Icon` | `string` | Roblox asset ID for the sidebar icon |

**Methods:**

| Method | Description |
| --- | --- |
| `Page:Turn(Bool)` | Activates or deactivates the page |

---

### Category

A small dimmed label displayed in the sidebar to group pages. Called on the **Window**, not a page.

```lua
Window:Category("Combat")
Window:Page({Name = "Aimbot", Icon = "..."})
Window:Page({Name = "ESP", Icon = "..."})
```

---

### SubPage

Tab-style navigation within a page. The first SubPage is auto-activated. Each SubPage has a configurable number of columns (default: 2).

```lua
local MainSubPage = CombatPage:SubPage({
    Name = "Main",
    Columns = 2
})
```

| Field | Type | Description |
| --- | --- | --- |
| `Name` | `string` | SubPage tab label |
| `Columns` | `number?` | Number of side-by-side columns (default: `2`) |

**Methods:**

| Method | Description |
| --- | --- |
| `SubPage:Turn(Bool)` | Activates or deactivates the sub-page |

---

### Section

A boxed container that holds elements, placed in a specific column of a SubPage.

```lua
local AimbotSection = MainSubPage:Section({
    Name = "Aimbot",
    Icon = "97491613646216",
    Side = 1
})
```

| Field | Type | Description |
| --- | --- | --- |
| `Name` | `string` | Section title |
| `Icon` | `string` | Roblox asset ID for the section icon |
| `Side` | `number` | Column index to place the section in (`1` = left, `2` = right) |

---

## Elements

All elements accept both `PascalCase` and `lowercase` field names (e.g. `Name` or `name`, `Flag` or `flag`). Omitting `Flag` auto-generates a unique one.

---

### Toggle

A boolean checkbox-style toggle.

```lua
local MyToggle = AimbotSection:Toggle({
    Name = "Enable Aimbot",
    Flag = "aimbot_enabled",
    Default = false,
    Callback = function(Value)
        print("Aimbot:", Value)
    end
})
```

| Field | Type | Description |
| --- | --- | --- |
| `Name` | `string` | Label text |
| `Flag` | `string?` | Config persistence key |
| `Default` | `boolean?` | Initial state (default: `false`) |
| `Callback` | `(Value: boolean) -> ()` | Called on state change |

**Methods:**

| Method | Description |
| --- | --- |
| `Toggle:Get()` | Returns current `boolean` value |
| `Toggle:Set(Value)` | Sets toggle state and fires callback |
| `Toggle:SetVisibility(Bool)` | Shows or hides the element |
| `Toggle:Colorpicker(Data)` | Adds an inline colorpicker (see [Colorpicker](#colorpicker)) |
| `Toggle:Keybind(Data)` | Adds an inline keybind (see [Keybind](#keybind)) |

---

### Button

A clickable button with a flash animation on press.

```lua
local MyButton = AimbotSection:Button({
    Name = "Teleport to Spawn",
    Callback = function()
        -- action
    end
})
```

| Field | Type | Description |
| --- | --- | --- |
| `Name` | `string` | Button label |
| `Callback` | `() -> ()` | Called when clicked |

**Methods:**

| Method | Description |
| --- | --- |
| `Button:Press()` | Programmatically triggers the button with animation |
| `Button:SetVisibility(Bool)` | Shows or hides the element |

---

### Slider

A draggable numeric slider with decimal rounding and a suffix label.

```lua
local MySlider = AimbotSection:Slider({
    Name = "FOV",
    Flag = "aimbot_fov",
    Min = 10,
    Max = 500,
    Default = 100,
    Decimals = 1,
    Suffix = "px",
    Callback = function(Value)
        print("FOV:", Value)
    end
})
```

| Field | Type | Description |
| --- | --- | --- |
| `Name` | `string` | Label text |
| `Flag` | `string?` | Config persistence key |
| `Min` | `number?` | Minimum value (default: `0`) |
| `Max` | `number?` | Maximum value (default: `100`) |
| `Default` | `number?` | Starting value (default: `0`) |
| `Decimals` | `number?` | Rounding precision — `1` = integer, `0.1` = 1 decimal (default: `1`) |
| `Suffix` | `string?` | Text appended after the value display (e.g. `"px"`, `"s"`) |
| `Callback` | `(Value: number) -> ()` | Called on value change |

**Methods:**

| Method | Description |
| --- | --- |
| `Slider:Get()` | Returns current `number` value |
| `Slider:Set(Value)` | Sets slider value (clamped and rounded automatically) |
| `Slider:SetVisibility(Bool)` | Shows or hides the element |

---

### Dropdown

A floating option list supporting single or multi-selection. Options can be added, removed, and refreshed at runtime.

```lua
local MyDropdown = AimbotSection:Dropdown({
    Name = "Target Part",
    Flag = "aimbot_part",
    Items = {"Head", "Torso", "HumanoidRootPart"},
    Default = "Head",
    Multi = false,
    Callback = function(Value)
        print("Selected:", Value)
    end
})
```

| Field | Type | Description |
| --- | --- | --- |
| `Name` | `string` | Label text |
| `Flag` | `string?` | Config persistence key |
| `Items` | `{ string }` | Initial list of options |
| `Default` | `string \| { string }?` | Pre-selected option(s) |
| `Multi` | `boolean?` | Allow multiple selections (default: `false`) |
| `Callback` | `(Value: string \| { string }) -> ()` | Called when selection changes |

**Methods:**

| Method | Description |
| --- | --- |
| `Dropdown:Get()` | Returns current value (`string` or `{ string }` if multi) |
| `Dropdown:Set(Option)` | Sets selected option(s) programmatically |
| `Dropdown:Add(Option)` | Adds a new option at runtime |
| `Dropdown:Remove(Option)` | Removes an option by name |
| `Dropdown:Refresh(List)` | Replaces all options with a new `{ string }` list |
| `Dropdown:SetOpen(Bool)` | Opens or closes the option list |
| `Dropdown:SetVisibility(Bool)` | Shows or hides the element |

---

### Label

A read-only text row. Supports inline Colorpicker and Keybind sub-elements on the right side.

```lua
local MyLabel = AimbotSection:Label("Status: Active")
MyLabel:SetText("Status: Idle")
```

| Parameter | Type | Description |
| --- | --- | --- |
| `Name` | `string` | Text to display |

**Methods:**

| Method | Description |
| --- | --- |
| `Label:SetText(Text)` | Updates the displayed text |
| `Label:SetVisibility(Bool)` | Shows or hides the element |
| `Label:Colorpicker(Data)` | Adds an inline colorpicker |
| `Label:Keybind(Data)` | Adds an inline keybind |

---

### Textbox

A text input field. Can be set to fire only on Enter press (`Finished = true`) or on every character change (default).

```lua
local MyTextbox = AimbotSection:Textbox({
    Flag = "target_name",
    Default = "",
    Placeholder = "Enter target...",
    Numeric = false,
    Finished = true,
    Callback = function(Value)
        print("Input:", Value)
    end
})
```

| Field | Type | Description |
| --- | --- | --- |
| `Flag` | `string?` | Config persistence key |
| `Default` | `string?` | Initial text value |
| `Placeholder` | `string?` | Placeholder hint text |
| `Numeric` | `boolean?` | Rejects non-numeric input when `true` (default: `false`) |
| `Finished` | `boolean?` | Only fires callback on Enter press (default: `false`, fires on every character) |
| `Callback` | `(Value: string) -> ()` | Called on input |

**Methods:**

| Method | Description |
| --- | --- |
| `Textbox:Get()` | Returns current `string` value |
| `Textbox:Set(Value)` | Sets the textbox content programmatically |
| `Textbox:SetVisibility(Bool)` | Shows or hides the element |

---

## Sub-Elements

Sub-elements attach inline to **Toggle** or **Label** rows on their right side. Multiple can be chained.

---

### Colorpicker

A floating HSV colorpicker with a hue slider and saturation/value palette. Left-clicking the color swatch opens/closes it.

```lua
local MyColorpicker = MyToggle:Colorpicker({
    Flag = "esp_color",
    Default = Color3.fromRGB(255, 0, 0),
    Callback = function(Color)
        print("Color:", Color)
    end
})
```

```lua
-- Also available on Label:
local LabelColor = MyLabel:Colorpicker({
    Flag = "label_color",
    Default = Color3.fromRGB(255, 255, 255),
    Callback = function(Color) end
})
```

| Field | Type | Description |
| --- | --- | --- |
| `Flag` | `string?` | Config persistence key |
| `Default` | `Color3?` | Initial color |
| `Callback` | `(Color: Color3) -> ()` | Called when color changes |

**Methods:**

| Method | Description |
| --- | --- |
| `Colorpicker:Get()` | Returns current `Color3` |
| `Colorpicker:Set(Color, Alpha?)` | Sets color — accepts `Color3`, hex string `"#RRGGBB"`, or `{R, G, B, A}` table |
| `Colorpicker:SetOpen(Bool)` | Opens or closes the picker window |
| `Colorpicker:SetVisibility(Bool)` | Shows or hides the swatch button |
| `Colorpicker:Update()` | Refreshes the display and fires the callback |

The saved flag value is `{ Color = Color3, HexValue = "RRGGBB" }`.

---

### Keybind

An assignable hotkey with three activation modes. Left-click to assign a key; right-click to open the mode picker.

```lua
local MyKeybind = MyToggle:Keybind({
    Flag = "aimbot_key",
    Default = Enum.KeyCode.E,
    Mode = "Toggle",
    Callback = function(Toggled)
        print("Aimbot active:", Toggled)
    end
})
```

```lua
-- Also available on Label:
local LabelKeybind = MyLabel:Keybind({
    Flag = "speed_key",
    Default = Enum.KeyCode.LeftShift,
    Mode = "Hold",
    Callback = function(Toggled) end
})
```

| Field | Type | Description |
| --- | --- | --- |
| `Flag` | `string?` | Config persistence key |
| `Default` | `Enum.KeyCode` | Initial key assignment |
| `Mode` | `string?` | Activation mode: `"Toggle"`, `"Hold"`, or `"Always"` (default: `"Toggle"`) |
| `Callback` | `(Toggled: boolean) -> ()` | Called when the keybind activates or deactivates |

**Modes:**

| Mode | Behavior |
| --- | --- |
| `Toggle` | Flips `Toggled` on each key press |
| `Hold` | `Toggled = true` while key is held, `false` on release |
| `Always` | `Toggled` is always `true` whenever key is pressed |

**Methods:**

| Method | Description |
| --- | --- |
| `Keybind:Get()` | Returns `Key, Mode, Toggled` |
| `Keybind:Set(Key)` | Accepts `KeyCode` enum, mode string (`"Toggle"`/`"Hold"`/`"Always"`), or `{Key, Mode}` table |
| `Keybind:SetMode(Mode)` | Changes the activation mode |
| `Keybind:SetOpen(Bool)` | Opens or closes the mode picker |
| `Keybind:Press(Bool?)` | Programmatically fires the keybind |

The saved flag value is `{ Key = "Enum.KeyCode.E", Mode = "Toggle", Toggled = false }`.

---

## Notifications

Lightweight floating notifications that stack and auto-dismiss. An optional icon asset ID can be provided.

```lua
Library:Notification("Aimbot enabled", 3, "10734888000")
--                    Name             Duration  Icon (optional)
```

| Parameter | Type | Description |
| --- | --- | --- |
| `Name` | `string` | Message text |
| `Duration` | `number` | Display time in seconds |
| `Icon` | `string?` | Optional Roblox asset ID for a left-side icon |

---

## Built-In Settings Page

Call `Library:CreateSettingsPage(Window)` after creating your pages to add a fully automatic **Settings** page with three sub-pages:

```lua
Library:CreateSettingsPage(Window)
```

**Configs sub-page** — create, save, load, delete, and refresh configs with a built-in Dropdown + Textbox interface.

**Theming sub-page** — live colorpickers for every theme key (`Background`, `Inline`, `Element`, `Accent`, `Border`, `Border 2`).

**Settings sub-page:**
- `Unload` button — cleanly destroys the UI
- `Menu Keybind` — reassign the window toggle key
- `Tween Speed` — adjust animation duration
- `Tween Style` — choose from 11 easing styles
- `Tween Direction` — `In`, `Out`, or `InOut`

---

## Custom Example

```lua
local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/AchaoticSoftworksCore/AchaoticAssets/refs/heads/main/UiLibrarys/Arcane.luau"))()

Library.Folders.Directory = "MyScript"
Library.Folders.Configs = "MyScript/Configs"

local Window = Library:Window({
    Name = "My Script",
    SubTitle = "for Blade Ball",
    ExpiresIn = "Lifetime"
})

Library:CreateSettingsPage(Window)

-- Sidebar categories
Window:Category("Combat")

local CombatPage = Window:Page({Name = "Combat", Icon = "136879043989014"})
local UtilityPage = Window:Page({Name = "Utility", Icon = "10709818996"})

-- =====================
-- COMBAT PAGE
-- =====================

local MainSub = CombatPage:SubPage({Name = "Main", Columns = 2})

local AimbotSection = MainSub:Section({Name = "Aimbot", Icon = "97491613646216", Side = 1})

local AimbotToggle = AimbotSection:Toggle({
    Name = "Enable Aimbot",
    Flag = "aimbot_on",
    Default = false,
    Callback = function(Value)
        print("Aimbot:", Value)
    end
})

AimbotToggle:Keybind({
    Flag = "aimbot_key",
    Default = Enum.KeyCode.E,
    Mode = "Toggle",
    Callback = function(Toggled)
        -- synced with the toggle automatically
    end
})

AimbotSection:Dropdown({
    Name = "Target Part",
    Flag = "aimbot_part",
    Items = {"Head", "Torso", "HumanoidRootPart"},
    Default = "Head",
    Multi = false,
    Callback = function(Value)
        print("Targeting:", Value)
    end
})

AimbotSection:Slider({
    Name = "FOV",
    Flag = "aimbot_fov",
    Min = 10,
    Max = 500,
    Default = 100,
    Decimals = 1,
    Suffix = "px",
    Callback = function(Value)
        print("FOV:", Value)
    end
})

AimbotSection:Toggle({
    Name = "Visible Only",
    Flag = "aimbot_visible",
    Default = true,
    Callback = function(Value) end
})

local ESPSection = MainSub:Section({Name = "ESP", Icon = "97491613646216", Side = 2})

local ESPToggle = ESPSection:Toggle({
    Name = "Enable ESP",
    Flag = "esp_on",
    Default = false,
    Callback = function(Value)
        print("ESP:", Value)
    end
})

ESPToggle:Colorpicker({
    Flag = "esp_color",
    Default = Color3.fromRGB(255, 0, 0),
    Callback = function(Color)
        print("ESP Color:", Color)
    end
})

ESPSection:Toggle({
    Name = "Show Names",
    Flag = "esp_names",
    Default = true,
    Callback = function(Value) end
})

ESPSection:Slider({
    Name = "Max Distance",
    Flag = "esp_dist",
    Min = 50,
    Max = 2000,
    Default = 1000,
    Decimals = 1,
    Suffix = "st",
    Callback = function(Value) end
})

-- =====================
-- UTILITY PAGE
-- =====================

Window:Category("Utility")

local UtilitySub = UtilityPage:SubPage({Name = "Movement", Columns = 2})

local SpeedSection = UtilitySub:Section({Name = "Speed", Icon = "10709818996", Side = 1})

local SpeedToggle = SpeedSection:Toggle({
    Name = "Speed Hack",
    Flag = "speed_on",
    Default = false,
    Callback = function(Value)
        local hum = game.Players.LocalPlayer.Character.Humanoid
        hum.WalkSpeed = Value and Library.Flags["speed_val"] or 16
    end
})

SpeedToggle:Keybind({
    Flag = "speed_key",
    Default = Enum.KeyCode.LeftShift,
    Mode = "Hold",
    Callback = function(Toggled)
        local hum = game.Players.LocalPlayer.Character.Humanoid
        hum.WalkSpeed = Toggled and Library.Flags["speed_val"] or 16
    end
})

SpeedSection:Slider({
    Name = "Speed",
    Flag = "speed_val",
    Min = 16,
    Max = 300,
    Default = 80,
    Decimals = 1,
    Callback = function(Value) end
})

local MiscSection = UtilitySub:Section({Name = "Misc", Icon = "10709818996", Side = 2})

MiscSection:Textbox({
    Flag = "target_player",
    Placeholder = "Enter username...",
    Finished = true,
    Callback = function(Value)
        print("Target:", Value)
    end
})

MiscSection:Button({
    Name = "Rejoin Server",
    Callback = function()
        game:GetService("TeleportService"):Teleport(game.PlaceId)
    end
})

MiscSection:Label("Session info will appear here.")

-- Notification on load
Library:Notification("Script loaded!", 3, "10734888000")
```

---

## Best Practices

* **Change folder names:** Replace `Library.Folders.Directory` before calling `Window` so configs don't collide with other scripts using the same library.
* **Unique flags:** Every element needs a unique `Flag` string. Duplicates silently overwrite each other in the config.
* **Use `Decimals` correctly:** `Decimals = 1` gives integers (rounds to nearest 1). `Decimals = 0.1` gives one decimal place. Think of it as the snap interval, not decimal places.
* **`Finished = true` on Textboxes:** If the callback does heavy work (network calls, player lookups), always use `Finished = true` so it only fires on Enter rather than every keystroke.
* **Sub-elements order:** Colorpickers and Keybinds appear right-to-left in the order they're created — add the Keybind first if you want it furthest right.
* **`Library:CreateSettingsPage` last:** Call it after all your pages so it appears at the bottom of the sidebar.
* **Guard `Library.Flags`:** If reading a flag value in a loop (e.g. Heartbeat), check `if Library and Library.Flags["my_flag"] then` — after `:Unload()`, `Library` is set to `nil`.
