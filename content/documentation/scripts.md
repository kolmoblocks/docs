---
title: "The Scripts"
fullTitle: "The Scripts"
name: "Mikhail Ignatovich"
date: 2018-08-06
external_link: ""
weight: 2
draft: false
---

This Java Script is part of the project and it allows to retrieve data from The KolmoLD Network by [Manifest](/documentation/manifest). These scripts are working in a web browser environment. It contains the following objects:

- engine (cache, network, server)
- wasm
- manifest
- formula

*The engine* object is the entry point of the scripts. The life cycle of *engine* is equal to a parent application. Object *engine* contains other objects to implement network communications and caching. Creating *engine* require mandatory options that must define network settings. Listed below code is show how to create *engine*. Address of dedicated server in options is the address of any peer node of [The KolmoLD Network](/documentation/network_design).

```JavaScript
// Mandatory options to create engine
let options = {
    Network: {
        DedicatedServer: "http://127.0.0.1:8080"
    }
};

// Creating engine with options
let engine = new Engine(options)
```

Class *wasm* is a wrapper to WebAssebbly actor. It allows executing WebAssembly algorithms according to [the specification](/documentation/wasm). Classes *manifest* and *formula* are represents entities of [the manifest](/documentation/manifest).

To retrieve data from The KolmonetLD Network by manifest use *eval_manifest* method. Listed below code is show how to do it right.

```JavaScript
// Create manifest
let manifest = new Manifest(JSON.parse("\"doi\":{\"SHA256\":\"6F59B23AA0D0F7BEE8AEF463F1527FB025217095707B72D25F25C8AE62E58F6\" }\""))

// Retrieve data by manifest
let result = engine.eval_manifest(manifest)

// Check operation result
if ("ok" === result.status)
{
    // Size of the retrieved data in bytes
    let size = result.data["size"]

    // MIME of the retrieved data if defined in the manifest
    let MIME = result.data["MIME"]

    // Retrieved data itself
    let data_from_kolmold = result.data
}
else
{
    // Something wrong happened
    alert(result.status)
}
```
Here is the [link](https://github.com/kolmoblocks/kolmonet/tree/master/scripts) to the git with scripts.