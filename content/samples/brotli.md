---
title: "Brotli Sample"
fullTitle: "Brotli"
name: "Mikhail Ignatovich"
date: 2019-07-17
weight: 2
draft: false
---

<b>KolmoLD is a way to get the support of new data formats!</b>

KolmoLD manifests provides a way to distribute new data compression algorithms along the same channel as the compressed data. This means that a new codec that has not been installed as a browser add-on or an application upgrade is accessible client-side. Once the manifest defining the codec has been downloaded, it can be stored and re-used on all files that has been compressed with it.

In this example, Brotli has been defined as a KolmoLD formula, and a manifest has been provided that will decompress a data object with this formula. The data object has already been compressed prior to this example.

The data object in question is a JavaScript file.

<div class="interactive-canvas">
<textarea class="manifest-text" id="sample_1_manifest_text" cols="100" rows="5">{ "doi" :{ "SHA256" : "6F59B23AA0D0F7BEE8AEF463F1527FB025217095707B72D25F25C8AE62EA58F6" } }</textarea>
<br>
<span class="run-button-placeholder"></span>
<span class="render-button-placeholder"></span>
<span class="manifest-data-placeholder" id="sample_1_manifest_data"></span>
</div>

<b>Result data</b>
    DOI=6F59B23AA0D0F7BEE8AEF463F1527FB025217095707B72D25F25C8AE62EA58F6

<b>actor.wasm (Webassembly for decompressing Brotli files)</b>
    DOI=740F4421EBC579AFE1F4FB0F57C15E698B7BB525EA19E22B96634D49BB2147EE
    
<b>arg1 (Compressed by Brotli JavaScript file)</b>
    DOI=ABCF598718AEAC83B850A6BF0773F3B8C3F8E43F33A05F4A571CFF5355DE58AE