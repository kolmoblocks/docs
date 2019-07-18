---
title: "Data Transformation"
fullTitle: "Data Transformation"
name: "Mikhail Ignatovich"
date: 2019-07-17
weight: 3
draft: false
---

<b>KolmoLD is a way to describe data as a transformation for another data!</b>

A formula that transforms input data into something new can easily be implemented in the KolmoLD
framework. The transformation formula is defined as an executable data object. In this example, it transforms
plain text into upper case letters. A manifest defines the data objects that need to be transformed and outputs the results.

<div class="interactive-canvas">
<textarea class="manifest-text" id="sample_1_manifest_text" cols="100" rows="3">{ "doi": { "SHA256": "E41B63C496ACD3978DFA624F513AC6B88832E07083BC99B029E2961AE547CB41" } }</textarea>
<br>
<span class="run-button-placeholder"></span>
<span class="render-button-placeholder"></span>
<span class="manifest-data-placeholder" id="sample_1_manifest_data"></span>
</div>

<b>Result data</b>
    DOI=E41B63C496ACD3978DFA624F513AC6B88832E07083BC99B029E2961AE547CB41

<b>actor.wasm (Webassembly for upper case)</b>
    DOI=8CF015555D8A4A3F077DD00960BFDF10260D51C0F298DC44A45AE8A7075FED35
    

<b>arg1 (Text file with "big brother")</b>
    DOI=6E459A96A65560FD0E4DA8E5FF58E7CED59BD685F068F6DBF23AEE158F2E091C
    