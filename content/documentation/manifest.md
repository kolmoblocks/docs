---
title: "Manifest"
fullTitle: "Manifest"
name: "Mikhail Ignatovich"
date: 2018-07-19
external_link: ""
draft: false
---

Manifest describe attributes and ways to reproduce resulting data.
One of presentation form of The Manifest is json.

Manifest may contain:

- [Properties](#properties)
- [Data Object Identifiers (DOI)](#doi)
- [Formulas](#formulas)
- Raw Value

**Example 1 (manifest with one formula):**
```json
{
	"MIME": "text/plain; charset=utf-8",
	"size": 202707,
	"doi": {
		"SHA256": "6F59B23AA0D0F7BEE8AEF463F1527FB025217095707B72D25F25C8AE62EA58F6"
	},
	"formulas": [
		{
			"actor": {
				"wasm": {
					"doi": {
						"SHA256": "740F4421EBC579AFE1F4FB0F57C15E698B7BB525EA19E22B96634D49BB2147EE"
					}
				}
			},
			"parameters": {
				"arg1": {
					"doi": {
						"SHA256": "ABCF598718AEAC83B850A6BF0773F3B8C3F8E43F33A05F4A571CFF5355DE58AE"
					}
				}
			}
		}
	]
}
```

**Example 2 (manifest with raw value):**
```json
{
	"raw" : "Hello World"
}
```
---
## Properties

Manifest may contain properties such as size and MIME. Size property contains the size of the resulting data in bytes. MIME property contains the type of the resulting data. All of these properties are optional. Usually, the values of the properties used to check resulting data.

**Example (specifying size property):**
```json
{
    "size" : 8080
}
```

**Example (specifying MIME property):**
```json
{
    "MIME" : "text/plain; charset=utf-8"
}
```

---
## DOI

Data Object Identifier (DOI) is a set of pair values contains the name of the hashing algorithm and value of the hash. This information is mandatory for all manifest except manifests are containing the Raw Value. At this time, Kolmonet support only SHA256 algorithm. Value of the hash is the result of applying a hash algorithm to resulting data.

**Example (specifying DOI):**
```json

{
    "doi" : {
        "SHA256": "ABCF598718AEAC83B850A6BF0773F3B8C3F8E43F33A05F4A571CFF5355DE58AE"
    }
}
```

---
## Formulas

Each Manifest may contain formulas. 

**Pseudo code example for formulas:**
```json
{
    "formulas": 
    [
            "Formula 1",        
            "Formula 2",

            "...",

            "Formula N"
    ]
}
```

At this time, Kolmonet support formulas contains actor and parameters. Supported type of actors is WebAssembly only. Two types of WebEssembly instantiation supported:

- WebAssembly without imports (pure WebAssembly)
- WebAssembly with JavaScript glue code (Emscripten generated)

Every entity in the formula is The Manifest. Below are examples for all types of supported formulas. Parameter's count and its names in the formula are defined [WebAssembly's interface](/documentation/wasm).

**Pseudo code example for pure WebAssembly formula:**
```json
{
    "actor": {
        "wasm": {
            "Manifest for WebAssembly"
        }
    },
    "parameters": {
        "arg1" : {
            "Manifest for Argument 1"
        },
        "arg2" : {
            "Manifest for Argument 2"
        },

        "...",

        "argM" : {
            "Manifest for Argument M"
        },
    }
}
```

**Pseudo code example for WebAssembly with JavaScript glue code formula:**
```json
{
    "actor": {
        "wasm": {
            "Manifest for WebAssembly"
        },
        "jsglue":{
            "Manifest for JavaScript glue code"
        }
    },
    "parameters": {
        "arg1": {
            "Manifest for Argument 1"
        },
        "arg2": {
            "Manifest for Argument 2"
        },

        "...",
        
        "argM": {
            "Manifest for Argument M"
        },
    }
}
```

