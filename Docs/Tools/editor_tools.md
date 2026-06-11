# Unreal MCP Editor Tools

This document provides detailed information about the editor tools available in the Unreal MCP integration.

## Overview

Editor tools cover everything that happens inside the running Unreal Editor: managing actors in the level, browsing and importing assets, controlling Play-In-Editor (PIE) sessions, and debugging through the output log, console commands, or arbitrary editor Python.

## Actor Tools

### get_actors_in_level

Get a list of all actors in the current level.

**Parameters:** None

**Returns:**
- Array of actor objects, each with name, class/type, and transform information

**Example:**
```json
{
  "command": "get_actors_in_level",
  "params": {}
}
```

### find_actors_by_name

Find actors in the current level by name pattern.

**Parameters:**
- `pattern` (string) - Name pattern to search for

**Returns:**
- Array of matching actor names

**Example:**
```json
{
  "command": "find_actors_by_name",
  "params": {
    "pattern": "Cube"
  }
}
```

### spawn_actor

Create a new actor in the current level.

**Parameters:**
- `name` (string) - The name to give the new actor (must be unique)
- `type` (string) - The type of actor to create (e.g. `StaticMeshActor`, `PointLight`, `CameraActor`)
- `location` (array, optional) - The [X, Y, Z] world location to spawn at (default: [0, 0, 0])
- `rotation` (array, optional) - The [Pitch, Yaw, Roll] rotation in degrees (default: [0, 0, 0])

**Returns:**
- The created actor's properties

**Example:**
```json
{
  "command": "spawn_actor",
  "params": {
    "name": "MyCube",
    "type": "StaticMeshActor",
    "location": [0, 0, 100],
    "rotation": [0, 45, 0]
  }
}
```

### delete_actor

Delete an actor by name.

**Parameters:**
- `name` (string) - Name of the actor to delete

**Returns:**
- Operation result

**Example:**
```json
{
  "command": "delete_actor",
  "params": {
    "name": "MyCube"
  }
}
```

### duplicate_actor

Duplicate a level actor.

**Parameters:**
- `name` (string) - Name of the actor to duplicate
- `offset` (array, optional) - [X, Y, Z] world offset for the copy (default: [100, 100, 0])

**Returns:**
- Properties of the duplicated actor

**Example:**
```json
{
  "command": "duplicate_actor",
  "params": {
    "name": "MyCube",
    "offset": [200, 0, 0]
  }
}
```

### set_actor_transform

Set the transform (location, rotation, scale) of an actor. Only the parameters you pass are changed.

**Parameters:**
- `name` (string) - Name of the actor
- `location` (array, optional) - [X, Y, Z] world location
- `rotation` (array, optional) - [Pitch, Yaw, Roll] rotation in degrees
- `scale` (array, optional) - [X, Y, Z] scale

**Returns:**
- Updated actor properties

**Example:**
```json
{
  "command": "set_actor_transform",
  "params": {
    "name": "MyCube",
    "location": [100, 200, 50],
    "scale": [2, 2, 2]
  }
}
```

### get_actor_properties

Get all properties of an actor.

**Parameters:**
- `name` (string) - Name of the actor

**Returns:**
- Actor properties (name, class, transform, components, ...)

**Example:**
```json
{
  "command": "get_actor_properties",
  "params": {
    "name": "MyCube"
  }
}
```

### set_actor_property

Set a single property on an actor.

**Parameters:**
- `name` (string) - Name of the actor
- `property_name` (string) - Name of the property to set
- `property_value` - Value to set the property to

**Returns:**
- Operation result

**Example:**
```json
{
  "command": "set_actor_property",
  "params": {
    "name": "MyLight",
    "property_name": "bHidden",
    "property_value": true
  }
}
```

### set_actor_mesh

Assign a static mesh to a level actor's StaticMeshComponent. Useful for making empty `StaticMeshActor`s visible.

**Parameters:**
- `name` (string) - Name of the actor in the level
- `mesh_path` (string) - Asset path, e.g. `/Engine/BasicShapes/Cube.Cube`

**Returns:**
- Operation result

**Example:**
```json
{
  "command": "set_actor_mesh",
  "params": {
    "name": "MyCube",
    "mesh_path": "/Engine/BasicShapes/Sphere.Sphere"
  }
}
```

### set_actor_material

Assign a material to a level actor's mesh component.

**Parameters:**
- `name` (string) - Name of the actor in the level
- `material_path` (string) - Asset path, e.g. `/Game/Materials/M_Red.M_Red`
- `slot` (integer, optional) - Material slot index (default: 0)

**Returns:**
- Operation result

**Example:**
```json
{
  "command": "set_actor_material",
  "params": {
    "name": "MyCube",
    "material_path": "/Game/Materials/M_Red.M_Red"
  }
}
```

### spawn_blueprint_actor

Spawn an actor from a Blueprint class.

**Parameters:**
- `blueprint_name` (string) - Name of the Blueprint to spawn from
- `actor_name` (string) - Name to give the spawned actor
- `location` (array, optional) - The [X, Y, Z] world location to spawn at (default: [0, 0, 0])
- `rotation` (array, optional) - The [Pitch, Yaw, Roll] rotation in degrees (default: [0, 0, 0])

**Returns:**
- The spawned actor's properties

**Example:**
```json
{
  "command": "spawn_blueprint_actor",
  "params": {
    "blueprint_name": "BP_MovingCube",
    "actor_name": "MovingCube1",
    "location": [0, 0, 100]
  }
}
```

## Asset Tools

### list_assets

List/search assets in the content browser via the Asset Registry.

**Parameters:**
- `path` (string, optional) - Content path to search under (default: `/Game`, recursive)
- `class_name` (string, optional) - Asset class filter (e.g. `StaticMesh`, `Material`, `Blueprint`)
- `limit` (integer, optional) - Max number of assets to return (default: 100)

**Returns:**
- List of matching assets

**Example:**
```json
{
  "command": "list_assets",
  "params": {
    "path": "/Game/Blueprints",
    "class_name": "Blueprint"
  }
}
```

### import_asset

Import an external file (FBX, PNG, WAV, ...) as a content asset.

**Parameters:**
- `source_file` (string) - Absolute path on disk, e.g. `C:/assets/rock.fbx`
- `destination_path` (string, optional) - Content folder to import into (default: `/Game/Imported`)

**Returns:**
- Imported asset information

**Example:**
```json
{
  "command": "import_asset",
  "params": {
    "source_file": "C:/assets/rock.fbx",
    "destination_path": "/Game/Props"
  }
}
```

### open_asset_editor

Open an asset in its editor window on the user's screen (e.g. show them a Blueprint you just created).

**Parameters:**
- `asset_path` (string) - Asset path, e.g. `/Game/Blueprints/BP_MovingCube`

**Returns:**
- Operation result

**Example:**
```json
{
  "command": "open_asset_editor",
  "params": {
    "asset_path": "/Game/Blueprints/BP_MovingCube"
  }
}
```

### save_all

Save all dirty (unsaved) packages — level and content assets.

**Parameters:** None

**Returns:**
- Operation result

**Example:**
```json
{
  "command": "save_all",
  "params": {}
}
```

## Play-In-Editor (PIE) Tools

### start_pie

Start a Play-In-Editor session. After starting, poll actors (`get_actor_properties`) or read `get_output_log` to observe runtime behavior. Combined with `stop_pie`, this enables AI self-testing loops.

**Parameters:** None

**Example:**
```json
{
  "command": "start_pie",
  "params": {}
}
```

### stop_pie

Stop the running Play-In-Editor session.

**Parameters:** None

**Example:**
```json
{
  "command": "stop_pie",
  "params": {}
}
```

## Debugging Tools

### get_output_log

Read recent lines from the Unreal Editor output log (errors, warnings, Print String output). Use this to debug — e.g. after start/stop play or a compile, read what the engine reported.

**Parameters:**
- `max_lines` (integer, optional) - How many of the most recent lines to return (default: 100)
- `filter` (string, optional) - Substring filter (e.g. `Error`, `LogBlueprint`, `MyDebug`)

**Returns:**
- Matching log lines

**Example:**
```json
{
  "command": "get_output_log",
  "params": {
    "max_lines": 50,
    "filter": "Error"
  }
}
```

### execute_python

Execute a Python script inside the Unreal Editor (the `unreal` module is available). This is the universal escape hatch: anything the editor's Python API can do, this can do.

**Parameters:**
- `script` (string) - Python source code (multi-line OK). Keep under ~7KB. Use `print()` / `unreal.log()` — output is returned in `output`.

**Returns:**
- Script output

**Example:**
```json
{
  "command": "execute_python",
  "params": {
    "script": "import unreal\nprint(unreal.SystemLibrary.get_engine_version())"
  }
}
```

### exec_console_command

Run an Unreal console command (e.g. `stat fps`, `r.ScreenPercentage 50`, `show collision`). Command output (if any) goes to the engine log — read it with `get_output_log`.

**Parameters:**
- `command` (string) - The console command to run

**Example:**
```json
{
  "command": "exec_console_command",
  "params": {
    "command": "stat fps"
  }
}
```

## Disabled / Removed Tools

- **`focus_viewport`** - Present in the source but currently disabled (the `@mcp.tool()` decorator is commented out) because it is buggy. Use `execute_python` with the editor's viewport API if you need viewport control.
- **`take_screenshot`** - Removed from the toolset.

## Error Handling

All command responses include a `success` or `status` field indicating whether the operation succeeded, and a `message` field with details in case of failure.

```json
{
  "success": false,
  "message": "Failed to connect to Unreal Engine"
}
```

## Troubleshooting

- **"Failed to connect to Unreal Engine"**: Make sure the Unreal Editor is running with the UnrealMCP plugin enabled (TCP port 55557).
- **Actor not found**: Verify that the actor name is correct and the actor exists in the current level (`get_actors_in_level`).
- **Invalid parameters**: Location/rotation/scale arrays must contain exactly 3 numeric values.
