---
title: "Hash Calculator"
fullTitle: "Hash Calculator"
name: "Mikhail Ignatovich"
date: 2019-07-17
weight: 4
draft: false
---

<b>KolmoLD is a way to calculate heavy algorithms by Webassembly technology for a short time!</b>

KolmoLD formulas are algorithms that can transform data objects. The Hash Calculator defines a formula that applies the Secure Hash Algorithm to a data
object and produces a SHA256. The formula itself has been defined in an executable data object, and two arguments:
data object and algorithm name can be passed in as arguments. This example uses the “Hello World” as a data object and "SHA256" for algorithm name. The execution of the algorithm happens client-side, and once the required data objects have been downloaded, they can be re-used easily.

<div class="interactive-canvas">
<textarea class="manifest-text" id="sample_1_manifest_text" cols="100" rows="35">
{
"MIME": "application/octet-stream",
"size": 32,
"doi": {
	"SHA256": "SHA256"
},
"formulas": 
[
	{
		"actor": {
			"wasm": {
				"doi": {
					"SHA256": "A09405100166D2F25CB246A14E09AF1FC290A0051E6E07F7BFF8C7D632945188"
				}
			}
		},
		"parameters": {
			"Input": {
				"doi": {
					"SHA256": "D2A84F4B8B650937EC8F73CD8BE2C74ADD5A911BA64DF27458ED8229DA804A26"
				}
			},
			"AlgName": {
				"raw": "SHA256"
			}
		}
	}
]
}
</textarea>
<br>
<span class="run-button-placeholder"></span>
<span class="render-button-placeholder"></span>
<span class="manifest-data-placeholder" id="sample_1_manifest_data"></span>
</div>


<b>actor.wasm (Webassembly for calculating SHA256, SHA512, MD5)</b>
    DOI=A09405100166D2F25CB246A14E09AF1FC290A0051E6E07F7BFF8C7D632945188

<b>Input (Text "Hello World")</b>
    DOI=D2A84F4B8B650937EC8F73CD8BE2C74ADD5A911BA64DF27458ED8229DA804A26

<b>AlgName</b>
    Raw value "SHA256"