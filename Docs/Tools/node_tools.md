# Unreal MCP Node Tools

This document provides detailed information about the Blueprint node tools available in the Unreal MCP integration.

## Overview

Node tools allow you to build and edit Blueprint event graphs programmatically: adding event/function/flow-control nodes, managing variables, connecting and disconnecting pins, and inspecting an existing Blueprint's structure with `get_blueprint_info`.

A typical workflow:

1. `get_blueprint_info` to inspect what already exists
2. Add nodes (`add_blueprint_event_node`, `add_blueprint_function_node`, ...) — each returns a node ID (GUID)
3. `connect_blueprint_nodes` to wire the pins together
4. `compile_blueprint` (see [Blueprint Tools](blueprint_tools.md)) and check for errors

## Inspection

### get_blueprint_info

Read a Blueprint's structure: parent class, variables, components, and event-graph nodes. Use this to inspect or analyze an existing Blueprint (e.g. to find issues, or to get node IDs for other tools).

**Parameters:**
- `blueprint_name` (string) - Name of the target Blueprint (must be under `/Game/Blueprints`)

**Returns:**
- `parent_class`, `variables` (with defaults), `components`, and `nodes` arrays (with pin literals)

**Example:**
```json
{
  "command": "get_blueprint_info",
  "params": {
    "blueprint_name": "BP_MovingCube"
  }
}
```

### find_blueprint_nodes

Find nodes in a Blueprint's event graph.

**Parameters:**
- `blueprint_name` (string) - Name of the target Blueprint
- `node_type` (string, optional) - Type of node to find (`Event`, `Function`, `Variable`, etc.)
- `event_type` (string, optional) - Specific event type to find (`BeginPlay`, `Tick`, etc.)

**Returns:**
- Array of found node IDs

**Example:**
```json
{
  "command": "find_blueprint_nodes",
  "params": {
    "blueprint_name": "BP_MovingCube",
    "node_type": "Event",
    "event_type": "Tick"
  }
}
```

## Adding Nodes

All node-adding tools accept an optional `node_position` (`[X, Y]` in the graph, default `[0, 0]`) and return the new node's ID.

### add_blueprint_event_node

Add an event node to a Blueprint's event graph.

**Parameters:**
- `blueprint_name` (string) - Name of the target Blueprint
- `event_name` (string) - Name of the event. Use the `Receive` prefix for standard events: `ReceiveBeginPlay`, `ReceiveTick`, etc.
- `node_position` (array, optional) - [X, Y] position in the graph

**Example:**
```json
{
  "command": "add_blueprint_event_node",
  "params": {
    "blueprint_name": "BP_MovingCube",
    "event_name": "ReceiveBeginPlay"
  }
}
```

### add_blueprint_input_action_node

Add an input action event node to a Blueprint's event graph. The action should exist in the project's input settings (see `create_input_mapping` in [Project Tools](project_tools.md)).

**Parameters:**
- `blueprint_name` (string) - Name of the target Blueprint
- `action_name` (string) - Name of the input action to respond to
- `node_position` (array, optional) - [X, Y] position in the graph

**Example:**
```json
{
  "command": "add_blueprint_input_action_node",
  "params": {
    "blueprint_name": "BP_PlayerPawn",
    "action_name": "Jump"
  }
}
```

### add_blueprint_function_node

Add a function call node to a Blueprint's event graph.

**Parameters:**
- `blueprint_name` (string) - Name of the target Blueprint
- `target` (string) - Target object for the function (a component name, or `self`)
- `function_name` (string) - Name of the function to call
- `params` (object, optional) - Parameter values to set on the function node's input pins
- `node_position` (array, optional) - [X, Y] position in the graph

**Example:**
```json
{
  "command": "add_blueprint_function_node",
  "params": {
    "blueprint_name": "BP_MovingCube",
    "target": "self",
    "function_name": "SetActorLocation",
    "params": { "NewLocation": [0, 0, 100] }
  }
}
```

### add_blueprint_branch_node

Add a Branch (if/else) node for conditional logic.

**Parameters:**
- `blueprint_name` (string) - Name of the target Blueprint
- `node_position` (array, optional) - [X, Y] position in the graph

**Returns:**
- Node ID and a `pins` list. Pins: `execute` (exec in), `Condition` (bool in), `then` (exec out if true), `else` (exec out if false)

**Example:**
```json
{
  "command": "add_blueprint_branch_node",
  "params": {
    "blueprint_name": "BP_MovingCube"
  }
}
```

### add_blueprint_sequence_node

Add a Sequence node, which fires multiple exec outputs in order (`then_0`, `then_1`, ...).

**Parameters:**
- `blueprint_name` (string) - Name of the target Blueprint
- `node_position` (array, optional) - [X, Y] position in the graph

**Example:**
```json
{
  "command": "add_blueprint_sequence_node",
  "params": {
    "blueprint_name": "BP_MovingCube"
  }
}
```

### add_blueprint_cast_node

Add a 'Cast To \<class\>' node to the event graph.

**Parameters:**
- `blueprint_name` (string) - Name of the target Blueprint
- `target_class` (string) - Class to cast to (e.g. `Pawn`, `Character`, `BP_MyActor_C`)
- `node_position` (array, optional) - [X, Y] position in the graph

**Example:**
```json
{
  "command": "add_blueprint_cast_node",
  "params": {
    "blueprint_name": "BP_MovingCube",
    "target_class": "Character"
  }
}
```

### add_blueprint_get_self_component_reference

Add a node that gets a reference to a component owned by the current Blueprint — similar to dragging a component in from the Components panel.

**Parameters:**
- `blueprint_name` (string) - Name of the target Blueprint
- `component_name` (string) - Name of the component to get a reference to
- `node_position` (array, optional) - [X, Y] position in the graph

**Example:**
```json
{
  "command": "add_blueprint_get_self_component_reference",
  "params": {
    "blueprint_name": "BP_MovingCube",
    "component_name": "CubeMesh"
  }
}
```

### add_blueprint_self_reference

Add a 'Get Self' node that returns a reference to this actor.

**Parameters:**
- `blueprint_name` (string) - Name of the target Blueprint
- `node_position` (array, optional) - [X, Y] position in the graph

**Example:**
```json
{
  "command": "add_blueprint_self_reference",
  "params": {
    "blueprint_name": "BP_MovingCube"
  }
}
```

## Variables

### add_blueprint_variable

Add a member variable to a Blueprint.

**Parameters:**
- `blueprint_name` (string) - Name of the target Blueprint
- `variable_name` (string) - Name of the variable
- `variable_type` (string) - Type of the variable (`Boolean`, `Integer`, `Float`, `Vector`, etc.)
- `is_exposed` (boolean, optional) - Whether to expose the variable to the editor (default: false)

**Example:**
```json
{
  "command": "add_blueprint_variable",
  "params": {
    "blueprint_name": "BP_MovingCube",
    "variable_name": "Speed",
    "variable_type": "Float",
    "is_exposed": true
  }
}
```

### add_blueprint_variable_get_node

Add a 'Get Variable' node (reads a Blueprint variable) to the event graph. The output pin is named after the variable.

**Parameters:**
- `blueprint_name` (string) - Name of the target Blueprint
- `variable_name` (string) - Name of an existing variable to read
- `node_position` (array, optional) - [X, Y] position in the graph

**Example:**
```json
{
  "command": "add_blueprint_variable_get_node",
  "params": {
    "blueprint_name": "BP_MovingCube",
    "variable_name": "Speed"
  }
}
```

### add_blueprint_variable_set_node

Add a 'Set Variable' node (writes a Blueprint variable) to the event graph. Pins: `execute`/`then` (exec), plus a value input pin named after the variable.

**Parameters:**
- `blueprint_name` (string) - Name of the target Blueprint
- `variable_name` (string) - Name of an existing variable to write
- `node_position` (array, optional) - [X, Y] position in the graph

**Example:**
```json
{
  "command": "add_blueprint_variable_set_node",
  "params": {
    "blueprint_name": "BP_MovingCube",
    "variable_name": "Speed"
  }
}
```

### delete_variable

Delete a member variable from a Blueprint.

**Parameters:**
- `blueprint_name` (string) - Name of the target Blueprint
- `variable_name` (string) - Name of the variable to delete

**Example:**
```json
{
  "command": "delete_variable",
  "params": {
    "blueprint_name": "BP_MovingCube",
    "variable_name": "Speed"
  }
}
```

## Connections & Layout

### connect_blueprint_nodes

Connect two nodes in a Blueprint's event graph.

**Parameters:**
- `blueprint_name` (string) - Name of the target Blueprint
- `source_node_id` (string) - ID of the source node
- `source_pin` (string) - Name of the output pin on the source node
- `target_node_id` (string) - ID of the target node
- `target_pin` (string) - Name of the input pin on the target node

**Example:**
```json
{
  "command": "connect_blueprint_nodes",
  "params": {
    "blueprint_name": "BP_MovingCube",
    "source_node_id": "ABC123...",
    "source_pin": "then",
    "target_node_id": "DEF456...",
    "target_pin": "execute"
  }
}
```

### disconnect_blueprint_nodes

Break a specific link between two node pins (the mirror of `connect_blueprint_nodes`).

**Parameters:**
- `blueprint_name` (string) - Name of the target Blueprint
- `source_node_id` (string) - ID of the source node
- `source_pin` (string) - Name of the output pin on the source node
- `target_node_id` (string) - ID of the target node
- `target_pin` (string) - Name of the input pin on the target node

**Example:**
```json
{
  "command": "disconnect_blueprint_nodes",
  "params": {
    "blueprint_name": "BP_MovingCube",
    "source_node_id": "ABC123...",
    "source_pin": "then",
    "target_node_id": "DEF456...",
    "target_pin": "execute"
  }
}
```

### set_node_position

Move an existing node to a new [X, Y] position (tidy up the graph layout).

**Parameters:**
- `blueprint_name` (string) - Name of the target Blueprint
- `node_id` (string) - GUID of the node to move
- `node_position` (array) - [X, Y] target position

**Example:**
```json
{
  "command": "set_node_position",
  "params": {
    "blueprint_name": "BP_MovingCube",
    "node_id": "ABC123...",
    "node_position": [400, 200]
  }
}
```

### delete_node

Delete a node from a Blueprint graph by its GUID (get IDs via `get_blueprint_info` or `find_blueprint_nodes`).

**Parameters:**
- `blueprint_name` (string) - Name of the target Blueprint
- `node_id` (string) - GUID of the node to delete

**Example:**
```json
{
  "command": "delete_node",
  "params": {
    "blueprint_name": "BP_MovingCube",
    "node_id": "ABC123..."
  }
}
```

## Error Handling

All command responses include a `success` or `status` field indicating whether the operation succeeded, and a `message` field with details in case of failure.

```json
{
  "success": false,
  "message": "Node not found: ABC123..."
}
```

## Type Reference

### Common Event Names

- `ReceiveBeginPlay` - Begin Play
- `ReceiveTick` - Tick
- `ReceiveEndPlay` - End Play
- `ReceiveActorBeginOverlap` / `ReceiveActorEndOverlap` - Overlap events
- `ReceiveHit` - Collision hit

### Variable Types

- `Boolean`
- `Integer`
- `Float`
- `String`
- `Name`
- `Vector`
- `Rotator`
- `Transform`
- Object/class names for reference types
