<div align="center">
  <img src="assets/rojo-logo.png" alt="Rojo" height="217" />
</div>

<div>&nbsp;</div>

<div align="center">
	<a href="https://travis-ci.org/LPGhatguy/rojo">
		<img src="https://api.travis-ci.org/LPGhatguy/rojo.svg?branch=master" alt="Travis-CI Build Status" />
	</a>
  <img src="https://img.shields.io/badge/server_version-0.4.0-brightgreen.svg" alt="Current server version" />
  <img src="https://img.shields.io/badge/plugin_version-0.4.0-brightgreen.svg" alt="Current plugin version" />
</div>

<hr />

**Rojo** is a flexible multi-tool designed for creating robust Roblox projects.

It's designed for power users who want to use the **best tools available** for building games, libraries, and plugins.

## Features
Rojo lets you:

* Work on scripts from the filesystem, in your favorite editor
* Version your place, library, or plugin using Git or another VCS
* Sync JSON-format models from the filesystem into your game

Later this year, Rojo will be able to:

* Sync `rbxmx` models between the filesystem and Roblox Studio
* Package projects into `rbxmx` files from the command line

## Installation
Rojo has two components:

* The server, a binary written in Rust
* The plugin, a Roblox Studio plugin written in Lua

It's important that the plugin and server are compatible. The plugin will show errors in the Roblox Studio Output window if there is a version mismatch.

To install the server, either:

* If you have Rust installed, use `cargo install rojo`
* Or, download a pre-built Windows binary from [the GitHub releases page](https://github.com/LPGhatguy/rojo/releases)

To install the plugin, either:

* Install the plugin from the [Roblox plugin page](https://www.roblox.com/library/1211549683/Rojo).
  * This gives you less control over what version you install -- you will always have the latest version.
* Or, download the latest release from [the GitHub releases section](https://github.com/LPGhatguy/rojo/releases) and install it into your Roblox plugins folder
  * You can open this folder by clicking the "Plugins Folder" button from the Plugins toolbar in Roblox Studio

## Server Usage
For more help, use `rojo help`.

### New Project
Create a new folder, then use `rojo init` inside that folder to initialize an empty project:

```sh
mkdir my-new-project
cd my-new-project

rojo init
```

Rojo will create an empty project file named `rojo.json` in the directory.

The default project file is:

```json
{
  "name": "my-new-project",
  "servePort": 8000,
  "partitions": {}
}
```

### Start Dev Server
To start the Rojo dev server, use:

```sh
rojo serve
```

The tool will tell you whether it found an existing project. You should then be able to connect and use the project from within Roblox Studio!

### Migrating an Existing Roblox Project
[Bi-directional script syncing](https://github.com/LPGhatguy/rojo/issues/5) is on the roadmap for Rojo this year, but isn't implemented.

In the mean-time, manually migrating scripts is probably the best route forward.

### Syncing into Roblox
In order to sync code into Roblox, you'll need to add one or more *partitions* to your configuration. A partition tells Rojo how to map directories on your filesystem to Roblox objects.

Each entry in the `partitions` map has a unique name, a filesystem path, and the full name of the Roblox object to sync into.

For example, if you want to map your `src` directory to an object named `My Cool Game` in `ReplicatedStorage`, you could use this configuration:

```json
{
  "name": "rojo",
  "servePort": 8000,
  "partitions": {
    "game": {
      "path": "src",
      "target": "ReplicatedStorage.My Cool Game"
    }
  }
}
```

The `path` parameter is relative to the project file.

The `target` parameter is a path to a Roblox object to link the partition to, starting at `game`. If any objects don't exist along the way, Rojo will try to create them.

**Any objects in a partition may be wiped away by Rojo after syncing! If this is not desired, use multiple, smaller partitions.**

Run `rojo serve` in the directory containing this project, then press the "Sync In" or "Toggle Polling" buttons in the Roblox Studio plugin to sync into your game.

### Sync Details
The structure of files and diectories on the filesystem are preserved when syncing into game.

Creation of Roblox instances follows a simple set of rules. The first rule that matches the file name is chosen:

| File Name      | Instance Type  | Notes                                     |
| -------------- | -------------- | ----------------------------------------- |
| `*.server.lua` | `Script`       | `Source` will contain the file's contents |
| `*.client.lua` | `LocalScript`  | `Source` will contain the file's contents |
| `*.lua`        | `ModuleScript` | `Source` will contain the file's contents |
| `*.model.json` | *Varies*       | See the example below                     |
| `*`            | `StringValue`  | `Value` will contain the file's contents  |

Any directories on the filesystem will turn into `Folder` objects.

Any directory containing one of these files will instead be a `ModuleScript`, `Script`, `LocalScript` containing the directory's contents:

* `init.lua`
* `init.server.lua`
* `init.client.lua`

For example, this file tree:

* my-game
	* init.client.lua
	* foo.lua

Will turn into these instances in Roblox:

* `my-game` (`LocalScript` with source from `my-game/init.client.lua`)
	* `foo` (`ModuleScript` with source from `my-game/foo.lua`)

`*.model.json` files are a way to represent simple Roblox instances on the filesystem until `rbxmx` and `rbxlx` support is implemented in Rojo.

This feature is intended for small instances, like `RemoteEvent` or `*Value` objects.

JSON Model files are strict, with every property being required. They look like this:

```json
{
  "Name": "hello",
  "ClassName": "Model",
  "Children": [
    {
      "Name": "Some Part",
      "ClassName": "Part",
      "Children": [],
      "Properties": {}
    },
    {
      "Name": "Some StringValue",
      "ClassName": "StringValue",
      "Children": [],
      "Properties": {
        "Value": {
          "Type": "String",
          "Value": "Hello, world!"
        }
      }
    }
  ],
  "Properties": {}
}
```

## Inspiration
There are lots of other tools that sync scripts into Roblox or provide other tools for working with Roblox places.

Here are a few, if you're looking for alternatives or supplements to Rojo:

* [Studio Bridge by Vocksel](https://github.com/vocksel/studio-bridge)
* [RbxRefresh by Osyris](https://github.com/osyrisrblx/RbxRefresh)
* [RbxSync by evaera](https://github.com/evaera/RbxSync)
* [CodeSync](https://github.com/MemoryPenguin/CodeSync) and [rbx-exteditor](https://github.com/MemoryPenguin/rbx-exteditor) by [MemoryPenguin](https://github.com/MemoryPenguin)
* [rbxmk by Anaminus](https://github.com/anaminus/rbxmk)

I also have a couple tools that Rojo intends to replace:

* [rbxfs](https://github.com/LPGhatguy/rbxfs), which has been deprecated by Rojo
* [rbxpacker](https://github.com/LPGhatguy/rbxpacker), which is still useful

## Contributing
Pull requests are welcome!

All pull requests are run against a test suite on Travis CI. That test suite should always pass!

## License
Rojo is available under the terms of the MIT license. See [LICENSE.md](LICENSE.md) for details.