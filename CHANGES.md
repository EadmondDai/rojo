# Rojo Change Log

## Current Master
* Plugin now automatically selects `HttpService` if it determines that HTTP isn't enabled ([#58](https://github.com/LPGhatguy/rojo/pull/58))

## 0.4.2 (April 4, 2018)
* Fixed final case of duplicated instance insertion, caused by reconciled instances not being inserted into `RouteMap`.
	* The reconciler is still not a perfect solution, especially if script instances get moved around without being destroyed. I don't think this can be fixed before a big refactor.

## 0.4.1 (April 1, 2018)
* Merged plugin repository into main Rojo repository for easier tracking.
* Improved `RouteMap` object tracking; this should fix some cases of duplicated instances being synced into the tree.

## 0.4.0 (March 27, 2018)
* Protocol version 1, which shifts more responsibility onto the server
	* This is a **major breaking** change!
	* The server now has a content of 'filter plugins', which transform data at various stages in the pipeline
	* The server now exposes Roblox instance objects instead of file contents, which lines up with how `rojo pack` will work, and paves the way for more robust syncing.
* Added `*.model.json` files, which let you embed small Roblox objects into your Rojo tree.
* Improved error messages in some cases ([#46](https://github.com/LPGhatguy/rojo/issues/46))

## 0.3.2 (December 20, 2017)
* Fixed `rojo serve` failing to correctly construct an absolute root path when passed as an argument
* Fixed intense CPU usage when running `rojo serve`

## 0.3.1 (December 14, 2017)
* Improved error reporting when invalid JSON is found in a `rojo.json` project
	* These messages are passed on from Serde

## 0.3.0 (December 12, 2017)
* Factored out the plugin into a separate repository
* Fixed server when using a file as a partition
	* Previously, trailing slashes were put on the end of a partition even if the read request was an empty string. This broke file reading on Windows when a partition pointed to a file instead of a directory!
* Started running automatic tests on Travis CI (#9)

## 0.2.3 (December 4, 2017)
* Plugin only release
* Tightened `init` file rules to only match script files
	* Previously, Rojo would sometimes pick up the wrong file when syncing

## 0.2.2 (December 1, 2017)
* Plugin only release
* Fixed broken reconciliation behavior with `init` files

## 0.2.1 (December 1, 2017)
* Plugin only release
* Changes default port to 8000

## 0.2.0 (December 1, 2017)
* Support for `init.lua` like rbxfs and rbxpacker
* More robust syncing with a new reconciler

## 0.1.0 (November 29, 2017)
* Initial release, functionally very similar to [rbxfs](https://github.com/LPGhatguy/rbxfs)