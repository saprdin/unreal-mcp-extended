# Unreal MCP UMG Tools

This document provides detailed information about the UMG (Unreal Motion Graphics) tools available in the Unreal MCP integration.

## Overview

UMG tools allow you to create and manipulate Widget Blueprints for game UI: creating widget blueprints, adding text blocks, buttons, images, and progress bars, binding events and properties, and inspecting or editing an existing widget tree.

Widget Blueprints are created under `/Game/UI` by default; editing tools (`get_widget_info`, `set_widget_text`, `delete_widget`, ...) resolve widget names under `/Game/Widgets`.

## UMG Tools

### create_umg_widget_blueprint

Create a new UMG Widget Blueprint.

**Parameters:**
- `widget_name` (string) - Name of the widget blueprint to create
- `parent_class` (string, optional) - Parent class for the widget (default: `UserWidget`)
- `path` (string, optional) - Content browser path where the widget should be created (default: `/Game/UI`)

**Returns:**
- Success status and widget path

**Example:**
```json
{
  "command": "create_umg_widget_blueprint",
  "params": {
    "widget_name": "WBP_MainMenu"
  }
}
```

### add_text_block_to_widget

Add a Text Block widget to a Widget Blueprint.

**Parameters:**
- `widget_name` (string) - Name of the target Widget Blueprint
- `text_block_name` (string) - Name to give the new Text Block
- `text` (string, optional) - Initial text content
- `position` (array, optional) - [X, Y] position in the canvas panel (default: [0, 0])
- `size` (array, optional) - [Width, Height] of the text block (default: [200, 50])
- `font_size` (integer, optional) - Font size in points (default: 12)
- `color` (array, optional) - [R, G, B, A] color values 0.0–1.0 (default: white)

**Example:**
```json
{
  "command": "add_text_block_to_widget",
  "params": {
    "widget_name": "WBP_MainMenu",
    "text_block_name": "TitleText",
    "text": "My Game",
    "position": [100, 50],
    "font_size": 32
  }
}
```

### add_button_to_widget

Add a Button widget to a Widget Blueprint.

**Parameters:**
- `widget_name` (string) - Name of the target Widget Blueprint
- `button_name` (string) - Name to give the new Button
- `text` (string, optional) - Text to display on the button
- `position` (array, optional) - [X, Y] position in the canvas panel (default: [0, 0])
- `size` (array, optional) - [Width, Height] of the button (default: [200, 50])
- `font_size` (integer, optional) - Font size for button text (default: 12)
- `color` (array, optional) - [R, G, B, A] text color values 0.0–1.0 (default: white)
- `background_color` (array, optional) - [R, G, B, A] button background color values 0.0–1.0 (default: dark gray)

**Example:**
```json
{
  "command": "add_button_to_widget",
  "params": {
    "widget_name": "WBP_MainMenu",
    "button_name": "StartButton",
    "text": "Start Game",
    "position": [100, 200]
  }
}
```

### add_image_to_widget

Add an Image widget, optionally with a texture.

**Parameters:**
- `widget_name` (string) - Name of the target Widget Blueprint
- `image_name` (string) - Name to give the new Image
- `texture_path` (string, optional) - Texture asset path (e.g. `/Game/Widgets/Images/A.A`)
- `position` (array, optional) - [X, Y] position in the canvas panel
- `size` (array, optional) - [Width, Height] of the image

**Example:**
```json
{
  "command": "add_image_to_widget",
  "params": {
    "widget_name": "WBP_MainMenu",
    "image_name": "Logo",
    "texture_path": "/Game/UI/Textures/T_Logo.T_Logo",
    "position": [50, 20]
  }
}
```

### add_progress_bar_to_widget

Add a ProgressBar (health bar, loading bar, etc.).

**Parameters:**
- `widget_name` (string) - Name of the target Widget Blueprint
- `bar_name` (string) - Name to give the new ProgressBar
- `percent` (float, optional) - Fill amount 0.0–1.0 (default: 1.0)
- `fill_color` (array, optional) - [R, G, B, A?] fill color
- `position` (array, optional) - [X, Y] position in the canvas panel
- `size` (array, optional) - [Width, Height] of the bar

**Example:**
```json
{
  "command": "add_progress_bar_to_widget",
  "params": {
    "widget_name": "WBP_HUD",
    "bar_name": "HealthBar",
    "percent": 0.75,
    "fill_color": [1.0, 0.0, 0.0, 1.0]
  }
}
```

### bind_widget_event

Bind an event on a widget component to a function.

**Parameters:**
- `widget_name` (string) - Name of the target Widget Blueprint
- `widget_component_name` (string) - Name of the widget component (button, etc.)
- `event_name` (string) - Name of the event to bind (`OnClicked`, etc.)
- `function_name` (string, optional) - Name of the function to create/bind to (defaults to `{widget_component_name}_{event_name}`)

**Example:**
```json
{
  "command": "bind_widget_event",
  "params": {
    "widget_name": "WBP_MainMenu",
    "widget_component_name": "StartButton",
    "event_name": "OnClicked"
  }
}
```

### set_text_block_binding

Set up a property binding for a Text Block widget, so the text updates from a property.

**Parameters:**
- `widget_name` (string) - Name of the target Widget Blueprint
- `text_block_name` (string) - Name of the Text Block to bind
- `binding_property` (string) - Name of the property to bind to
- `binding_type` (string, optional) - Type of binding (default: `Text`)

**Example:**
```json
{
  "command": "set_text_block_binding",
  "params": {
    "widget_name": "WBP_HUD",
    "text_block_name": "ScoreText",
    "binding_property": "CurrentScore"
  }
}
```

### add_widget_to_viewport

Add a Widget Blueprint instance to the viewport.

**Parameters:**
- `widget_name` (string) - Name of the Widget Blueprint to add
- `z_order` (integer, optional) - Z-order for the widget; higher numbers appear on top (default: 0)

**Example:**
```json
{
  "command": "add_widget_to_viewport",
  "params": {
    "widget_name": "WBP_HUD",
    "z_order": 5
  }
}
```

### get_widget_info

Read a Widget Blueprint's widget tree: every widget's name, class, text content (TextBlock/EditableTextBox), and canvas position. Use this to collect/audit UI strings or inspect an existing widget.

**Parameters:**
- `widget_name` (string) - Name of the Widget Blueprint (under `/Game/Widgets`)

**Returns:**
- `widgets` array: `[{name, class, text?, position?}, ...]`

**Example:**
```json
{
  "command": "get_widget_info",
  "params": {
    "widget_name": "WBP_MainMenu"
  }
}
```

### set_widget_text

Change the text of an existing TextBlock/EditableTextBox in a Widget Blueprint (e.g. fix a typo found via `get_widget_info`).

**Parameters:**
- `widget_name` (string) - Name of the Widget Blueprint (under `/Game/Widgets`)
- `component_name` (string) - Name of the text widget inside it
- `text` (string) - New text content

**Example:**
```json
{
  "command": "set_widget_text",
  "params": {
    "widget_name": "WBP_MainMenu",
    "component_name": "TitleText",
    "text": "My Awesome Game"
  }
}
```

### delete_widget

Remove a widget (text, button, image, ...) from a Widget Blueprint.

**Parameters:**
- `widget_name` (string) - Name of the Widget Blueprint (under `/Game/Widgets`)
- `component_name` (string) - Name of the widget to remove

**Example:**
```json
{
  "command": "delete_widget",
  "params": {
    "widget_name": "WBP_MainMenu",
    "component_name": "OldButton"
  }
}
```

## Error Handling

All command responses include a `success` or `status` field indicating whether the operation succeeded, and a `message` field with details in case of failure.

```json
{
  "success": false,
  "message": "Widget Blueprint 'WBP_MainMenu' not found"
}
```

## Implementation Notes

- Widget Blueprints are created through the proper `UWidgetBlueprintFactory`, so they behave like hand-created ones.
- New widgets are added to the root Canvas Panel.
- To wire button clicks to logic, use `bind_widget_event` and then add nodes to the generated function with the [Node Tools](node_tools.md).
