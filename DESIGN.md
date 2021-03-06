# Rojo Design - Protocol Version 1
This is a super rough draft that I'm trying to use to lay out some of my thoughts.

## API

### POST `/read`
Accepts a `Vec<Route>` of items to read.

Returns `Vec<Option<RbxInstance>>`, in the same order as the request.

### POST `/write`
Accepts a `Vec<{ Route, RbxInstance }>` of items to write.

I imagine that the `Name` attribute of the top-level `RbxInstance` would be ignored in favor of the route name?

## CLI
The `rojo serve` command uses three major components:
* A Virtual Filesystem (VFS), which exposes the filesystem as `VfsItem` objects
* A VFS watcher, which tracks changes to the filesystem and logs them
* An HTTP API, which exposes an interface to the Roblox Studio plugin

### Transform Plugins
Transform plugins (or filter plugins?) can interject in three places:
* Transform a `VfsItem` that's being read into an `RbxInstance` in the VFS
* Transform an `RbxInstance` that's being written into a `VfsItem` in the VFS
* Transform a file change into paths that need to be updated in the VFS watcher

The plan is to have several built-in plugins that can be rearranged/configured in project settings:

* Base plugin
	* Transforms all unhandled files to/from StringValue objects
* Script plugin
	* Transforms `*.lua` files to their appropriate file types
* JSON/rbxmx/rbxlx model plugin
* External binary plugin
	* User passes a binary name (like `moonc`) that modifies file contents

## Roblox Studio Plugin
With the protocol version 1 change, the Roblox Studio plugin got a lot simpler. Notably, the plugin doesn't need to be aware of anything about the filesystem's semantics, which is super handy.

## Bi-directional syncing
Quenty laid out a good way to handle bi-directional syncing.

When receiving a change from the plugin:
1. Hash the new contents of the file, store it in a map from routes to hashes
2. Write the new file contents to the filesystem
3. Later down the line, receive a change event from the filesystem watcher
4. When receiving a change, if the item is in the hash map, read it and hash those contents
5. If the hash matches the last noted hash, discard the change, else continue as normal