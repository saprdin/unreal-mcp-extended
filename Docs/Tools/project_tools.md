# Unreal MCP Project Tools

This document provides detailed information about the project-level tools available in the Unreal MCP integration.

## Overview

Project tools manage project-wide settings and configuration, such as input mappings.

## Project Tools

### create_input_mapping

Create an input mapping (legacy input system) for the project.

**Parameters:**
- `action_name` (string) - Name of the input action
- `key` (string) - Key to bind (`SpaceBar`, `LeftMouseButton`, `W`, `Gamepad_FaceButton_Bottom`, etc.)
- `input_type` (string, optional) - Type of input mapping: `Action` or `Axis` (default: `Action`)

**Returns:**
- Operation result

**Example:**
```json
{
  "command": "create_input_mapping",
  "params": {
    "action_name": "Jump",
    "key": "SpaceBar",
    "input_type": "Action"
  }
}
```

After creating a mapping, you can react to it in a Blueprint with `add_blueprint_input_action_node` (see [Node Tools](node_tools.md)).

## Error Handling

All command responses include a `success` or `status` field indicating whether the operation succeeded, and a `message` field with details in case of failure.

```json
{
  "success": false,
  "message": "Failed to connect to Unreal Engine"
}
```
