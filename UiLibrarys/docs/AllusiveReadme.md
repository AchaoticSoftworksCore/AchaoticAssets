# Allusive UI Library

Welcome to **Allusive UI Library**, a sleek and highly customizable Roblox UI library designed for creating intuitive, feature-rich user interfaces with ease and style. Whether you're building tabs, modules, dropdowns, sliders, checkboxes, or more, Allusive UI gives you a powerful, modular, and visually stunning framework to elevate your Roblox projects to the stars.

---

## Features

* **Modular Design:** Organize UI elements into tabs and modules for clean, structured interfaces.
* **Customizable Components:** Dropdowns, sliders, checkboxes, textboxes, and more — all with extensive customization options.
* **Config Management:** Easily save and load user preferences.
* **In-Game Notifications:** Inform players with beautiful, animated notifications.
* **Responsive UI:** Automatically scales based on device and screen size.
* **Flexible Callbacks:** Respond to user input with customizable functions.
* **Space-Age Aesthetic:** Clean, modern UI with cosmic vibes.

---

## Installation & Usage

Load the library with:

```lua
local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/AchaoticSoftworksCore/AchaoticAssets/refs/heads/main/UiLibrarys/Allusive.luau"))()
local main = Library.new()

main:load() -- Initialize the UI
```

---

## Table of Contents

* [Overview](#-overview)
* [Core Structure](#-core-structure)
* [UI Elements](#-ui-elements)

  * [Tabs](#tabs)
  * [Modules](#modules)
  * [Dropdown](#dropdown)
  * [Slider](#slider)
  * [Checkbox](#checkbox)
  * [Divider](#divider)
  * [Paragraph](#paragraph)
  * [Textbox](#textbox)
  * [Feature](#feature)
* [Notifications](#notifications)
* [Custom Example](#custom-example)
* [Best Practices](#best-practices)
* [License](#license)

---

## Overview

The Allusive UI Library simplifies complex UI creation with a modular system organized into tabs and modules. It supports various interactive components and persistent user preferences through an easy-to-use configuration system.

---

## Core Structure

| Function                                   | Description                                | Parameters                       | Returns       |
| ------------------------------------------ | ------------------------------------------ | -------------------------------- | ------------- |
| `Library.new()`                            | Creates a new UI instance                  | None                             | Main instance |
| `Library:load()`                           | Loads the UI and config                    | None                             | None          |
| `Library:get_screen_scale()`               | Returns UI scale based on screen size      | None                             | Number        |
| `Library:get_device()`                     | Detects the device type (PC, Mobile, etc.) | None                             | String        |
| `Library:SendNotification(settings)`       | Displays a notification                    | `{title, text, duration}`        | None          |
| `Library:removed(action)`                  | Registers a callback on UI destruction     | `function`                       | None          |
| `Library:flag_type(flag, type)`            | Sets a config flag type                    | `flag, type`                     | None          |
| `Library:remove_table_value(table, value)` | Removes a value from a table               | `table, value`                   | None          |
| `Library:create_ui()`                      | Initializes the UI framework               | None                             | None          |
| `Library:UIVisiblity()`                    | Toggles UI visibility                      | None                             | None          |
| `Library:change_visiblity(state)`          | Sets UI visibility explicitly              | `boolean`                        | None          |
| `Library:update_tabs(tab)`                 | Updates tab appearance                     | `TextButton`                     | None          |
| `Library:update_sections(left, right)`     | Updates module sections                    | `ScrollingFrame, ScrollingFrame` | None          |

---

## Config Management

| Function                    | Description              | Parameters          | Returns     |
| --------------------------- | ------------------------ | ------------------- | ----------- |
| `Config:save(file, config)` | Saves configuration data | `file_name, config` | None        |
| `Config:load(file, config)` | Loads configuration data | `file_name, config` | Config data |

---

## UI Elements

### Tabs

Organize your modules into tabs.

```lua
local tab = main:create_tab(title, icon)
```

* **title:** string — Tab name
* **icon:** string (asset ID) — Tab icon

Returns: `TabManager`

### Modules

Create containers for UI elements inside tabs.

```lua
local module = tab:create_module({
  title = "Module Title",
  flag = "Unique_Flag",
  description = "Description",
  section = "left" or "right",
  callback = function(value) end
})
```

Returns: `ModuleManager`

### Dropdown

Select one or more options from a list.

```lua
local dropdown = module:create_dropdown({
  title = "Dropdown Title",
  flag = "Dropdown_Flag",
  options = {"Option1", "Option2"},
  multi_dropdown = true/false,
  maximum_options = number,
  callback = function(value) end
})
```

**Extra Functions:**

* `dropdown:update(option)` — Update selected option.
* `dropdown:unfold_settings()` — Toggle dropdown visibility.
* `dropdown:New(value)` — Add a new option.

Returns: `DropdownManager`

### Slider

Select numeric values in a range.

```lua
local slider = module:create_slider({
  title = "Slider Title",
  flag = "Slider_Flag",
  maximum_value = 100,
  minimum_value = 0,
  value = 50,
  round_number = true/false,
  callback = function(value) end
})
```

**Extra Functions:**

* `slider:set_percentage(percentage)` — Set slider by percentage.
* `slider:update()` — Refresh slider display.
* `slider:input()` — Process user input.

Returns: `SliderManager`

### Checkbox

Toggle boolean settings.

```lua
module:create_checkbox({
  title = "Checkbox Title",
  flag = "Checkbox_Flag",
  callback = function(value) end
})
```

Returns: None

### Divider

Visual separator between elements.

```lua
module:create_divider({})
```

Returns: None

### Paragraph

Display static text or info.

```lua
module:create_paragraph({
  title = "Paragraph Title",
  text = "Paragraph content here."
})
```

Returns: None

### Textbox

Input custom text.

```lua
local textbox = module:create_textbox({
  title = "Textbox Title",
  placeholder = "Enter text...",
  flag = "Textbox_Flag",
  callback = function(text) end
})
```

**Extra Functions:**

* `textbox:update_text(text)` — Update textbox content.

Returns: `TextboxManager`

### Feature

Custom feature with flexible functionality.

```lua
module:create_feature(settings)
```

Settings depend on implementation.

---

## Notifications

Display in-game notifications.

```lua
Library.SendNotification({
  title = "Notification Title",
  text = "Message goes here",
  duration = seconds
})
```

---

## Custom Example

```lua
local Library = loadstring(game:HttpGet("https://raw.githubusercontent.com/AchaoticSoftworksCore/AchaoticAssets/refs/heads/main/UiLibrarys/Allusive.luau"))()
local main = Library.new()

-- Tabs
local combat_tab = main:create_tab("Combat", "rbxassetid://76499042599127")
local utility_tab = main:create_tab("Utility", "rbxassetid://126017907477623")

-- Combat Module
local aimbot = combat_tab:create_module({
  title = "Aimbot",
  flag = "Aimbot",
  description = "Locks onto enemies",
  section = "left",
  callback = function(value)
    print("Aimbot enabled:", value)
    Library.SendNotification({title = "Aimbot", text = value and "Enabled!" or "Disabled!", duration = 3})
  end
})

aimbot:create_dropdown({
  title = "Target Part",
  flag = "Target_Part",
  options = {"Head", "Torso", "Random"},
  multi_dropdown = false,
  maximum_options = 1,
  callback = function(value) print("Targeting:", value) end
})

aimbot:create_slider({
  title = "Aimbot Sensitivity",
  flag = "Aimbot_Sensitivity",
  maximum_value = 100,
  minimum_value = 10,
  value = 50,
  round_number = true,
  callback = function(value) print("Sensitivity:", value) end
})

aimbot:create_checkbox({
  title = "Silent Aim",
  flag = "Silent_Aim",
  callback = function(value) print("Silent Aim:", value) end
})

-- Utility Module
local speedhack = utility_tab:create_module({
  title = "Speed Hack",
  flag = "Speed_Hack",
  description = "Increases player speed",
  section = "right",
  callback = function(value) print("Speed Hack:", value) end
})

speedhack:create_paragraph({
  title = "Info",
  text = "Use responsibly to avoid detection."
})

speedhack:create_textbox({
  title = "Speed Value",
  placeholder = "Enter speed...",
  flag = "Speed_Value",
  callback = function(text) print("Speed set to:", text) end
})

main:load()
```

---

## Best Practices

* **Organize:** Group related settings logically into modules and tabs.
* **Unique Flags:** Use unique flags for config saving.
* **Test Callbacks:** Handle all input edge cases.
* **Responsive UI:** Use `Library:get_screen_scale()` for adaptable UI.
* **Notify Users:** Use notifications for feedback.

---

## 📜 License

This project is licensed under the MIT License. See the LICENSE file for details.
