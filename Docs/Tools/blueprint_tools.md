# Unreal MCP Blueprint Tools

This document provides detailed information about the Blueprint tools available in the Unreal MCP integration.

## Overview

Blueprint tools allow you to create and configure Blueprint assets: new Blueprint classes, components, properties, physics settings, function graphs, and materials. To spawn instances of a Blueprint into the level, see `spawn_blueprint_actor` in [Editor Tools](editor_tools.md); to edit a Blueprint's event graph, see [Node Tools](node_tools.md).

## Blueprint Tools

### create_blueprint

Create a new Blueprint class.

**Parameters:**
- `name` (string) - Name of the new Blueprint class
- `parent_class` (string) - Parent class for the Blueprint (e.g. `Actor`, `Pawn`)

**Returns:**
- Information about the created Blueprint, including success status and path. Created Blueprints are auto-saved to disk.

**Example:**
```json
{
  "command": "create_blueprint",
  "params": {
    "name": "BP_MovingCube",
    "parent_class": "Actor"
  }
}
```

### add_blueprint_function

Create a new (empty) function graph in a Blueprint. Nodes can then be added to it with the node tools.

**Parameters:**
- `blueprint_name` (string) - Name of the target Blueprint
- `function_name` (string) - Name for the new function

**Returns:**
- Operation result

**Example:**
```json
{
  "command": "add_blueprint_function",
  "params": {
    "blueprint_name": "BP_MovingCube",
    "function_name": "ResetPosition"
  }
}
```

### add_component_to_blueprint

Add a component to a Blueprint.

**Parameters:**
- `blueprint_name` (string) - Name of the target Blueprint
- `component_type` (string) - Type of component to add (use the component class name without the `U` prefix, e.g. `StaticMeshComponent`)
- `component_name` (string) - Name for the new component
- `location` (array, optional) - [X, Y, Z] coordinates for the component's position (default: [0, 0, 0])
- `rotation` (array, optional) - [Pitch, Yaw, Roll] values for the component's rotation (default: [0, 0, 0])
- `scale` (array, optional) - [X, Y, Z] values for the component's scale (default: [1, 1, 1])
- `component_properties` (object, optional) - Additional properties to set on the component

**Returns:**
- Information about the added component

**Example:**
```json
{
  "command": "add_component_to_blueprint",
  "params": {
    "blueprint_name": "BP_MovingCube",
    "component_type": "StaticMeshComponent",
    "component_name": "CubeMesh",
    "scale": [2.0, 2.0, 2.0]
  }
}
```

### set_static_mesh_properties

Set the mesh asset on a StaticMeshComponent.

**Parameters:**
- `blueprint_name` (string) - Name of the target Blueprint
- `component_name` (string) - Name of the StaticMeshComponent
- `static_mesh` (string, optional) - Path to the static mesh asset (default: `/Engine/BasicShapes/Cube.Cube`)

**Returns:**
- Operation result

**Example:**
```json
{
  "command": "set_static_mesh_properties",
  "params": {
    "blueprint_name": "BP_MovingCube",
    "component_name": "CubeMesh",
    "static_mesh": "/Engine/BasicShapes/Sphere.Sphere"
  }
}
```

### set_component_property

Set a property on a component in a Blueprint.

**Parameters:**
- `blueprint_name` (string) - Name of the target Blueprint
- `component_name` (string) - Name of the component
- `property_name` (string) - Name of the property to set
- `property_value` - Value to set the property to

**Returns:**
- Operation result

**Example:**
```json
{
  "command": "set_component_property",
  "params": {
    "blueprint_name": "BP_MovingCube",
    "component_name": "CubeMesh",
    "property_name": "bVisible",
    "property_value": true
  }
}
```

### set_physics_properties

Set physics properties on a component.

**Parameters:**
- `blueprint_name` (string) - Name of the target Blueprint
- `component_name` (string) - Name of the component
- `simulate_physics` (boolean, optional) - Whether to simulate physics (default: true)
- `gravity_enabled` (boolean, optional) - Whether gravity affects the component (default: true)
- `mass` (float, optional) - Mass in kg (default: 1.0)
- `linear_damping` (float, optional) - Linear damping (default: 0.01)
- `angular_damping` (float, optional) - Angular damping (default: 0.0)

**Returns:**
- Operation result

**Example:**
```json
{
  "command": "set_physics_properties",
  "params": {
    "blueprint_name": "BP_MovingCube",
    "component_name": "CubeMesh",
    "simulate_physics": true,
    "mass": 10.0
  }
}
```

### compile_blueprint

Compile a Blueprint. Returns real compile errors, warnings, and messages — use this after editing a graph to verify it is valid.

**Parameters:**
- `blueprint_name` (string) - Name of the target Blueprint

**Returns:**
- Compile result including any errors/warnings/messages

**Example:**
```json
{
  "command": "compile_blueprint",
  "params": {
    "blueprint_name": "BP_MovingCube"
  }
}
```

### set_blueprint_property

Set a property on a Blueprint's class default object (CDO).

**Parameters:**
- `blueprint_name` (string) - Name of the target Blueprint
- `property_name` (string) - Name of the property to set
- `property_value` - Value to set the property to

**Returns:**
- Operation result

**Example:**
```json
{
  "command": "set_blueprint_property",
  "params": {
    "blueprint_name": "BP_PlayerPawn",
    "property_name": "bUseControllerRotationYaw",
    "property_value": true
  }
}
```

## Material Tools

### create_material

Create a new Material asset, optionally with a base color.

**Parameters:**
- `name` (string) - Name of the material asset
- `path` (string, optional) - Content folder to create it in (default: `/Game/Materials/`)
- `base_color` (array, optional) - [R, G, B] floats in 0..1 for the base color

**Returns:**
- Information about the created material

**Example:**
```json
{
  "command": "create_material",
  "params": {
    "name": "M_Red",
    "base_color": [1.0, 0.0, 0.0]
  }
}
```

### create_material_instance

Create a Material Instance from a parent material and set parameter overrides. A practical way to mass-produce color/value variants without creating new materials.

**Parameters:**
- `name` (string) - Name for the new instance asset
- `parent_material` (string) - Path to the parent material (e.g. `/Game/Materials/M_Base.M_Base`)
- `path` (string, optional) - Content folder (default: `/Game/Materials/`)
- `scalar_params` (object, optional) - Scalar parameter overrides, e.g. `{"Roughness": 0.5}`
- `vector_params` (object, optional) - Vector parameter overrides, e.g. `{"Color": [1, 0, 0, 1]}`

**Returns:**
- Information about the created material instance

**Example:**
```json
{
  "command": "create_material_instance",
  "params": {
    "name": "MI_Blue",
    "parent_material": "/Game/Materials/M_Base.M_Base",
    "vector_params": { "Color": [0.0, 0.0, 1.0, 1.0] }
  }
}
```

## Disabled Tools

- **`set_pawn_properties`** - Present in the source but currently disabled (the `@mcp.tool()` decorator is commented out). Use `set_blueprint_property` (or `set_component_property`) to set the individual pawn properties instead.

## Error Handling

All command responses include a `success` or `status` field indicating whether the operation succeeded, and a `message` field with details in case of failure.

```json
{
  "success": false,
  "message": "Blueprint 'BP_MovingCube' not found"
}
```

## Implementation Notes

- Blueprint names are resolved under `/Game/Blueprints` by default.
- Created Blueprints are automatically saved to disk.
- Always call `compile_blueprint` after structural changes and check its result for errors.
