---
title: "Type-Specific Compression"
fullTitle: "Type-Specific Compression"
name: "Mikhail Ignatovich"
date: 2019-07-17
weight: 5
draft: false
---

<b>KolmoLD is a way to get the best compression ratio for a specific type of data!</b>

General-purpose lossless compression algorithms have served web developers well, but with the advent of dictionary coders compression algorithms can be tweaked to achieve optimal compression ratios for a specific context. Keeping the context in mind is possible with KolmoLD. Let’s say the first version of the dictionary required for this compression algorithm (e.g. zstandard) is trained on a set of data files. Optimal compression ratios are achieved at the beginning, but then the format of the data files change. It is now necessary to create a new dictionary.

Making the change in a KolmoLD manifest has little impact on the functionality, and deployment is straightforward. The new dictionary is linked in the manifest as a data object. When the end-user downloads the new manifest, the system recognizes that the data object containing the decompression formula remains unchanged. The only file that needs to be downloaded again is the new dictionary.

<div class="interactive-canvas">
<textarea class="manifest-text" id="sample_1_manifest_text" cols="100" rows="5">{ "doi": { "SHA256": "409895A3AAB6D7AA458B46550CF1AA0094FBCB5107A62BC4BAB6B11D073A6268" } }</textarea>
<br>
<span class="run-button-placeholder"></span>
<span class="render-button-placeholder"></span>
<span class="manifest-data-placeholder" id="sample_1_manifest_data"></span>
</div>