---
title: "Hello World Sample"
fullTitle: "Hello World"
name: "Mikhail Ignatovich"
date: 2019-07-17
weight: 1
draft: false
---

<b>KolmoLD is a way to get data generated from others!</b>

In the KolmoLD framework, content is generated. The *formula* for generating this content is defined in a *manifest*. Data chunks can also be defined and referenced by these formulas. Manifests and formulas are best explained by a "Hello World" example. We will create the "Hello World" string by concatenating two strings together: "Hello " and "World". These two strings are plain text data objects. The formula that will concatenate them together is an executable type of data object. It is identified by its custom MIME type: application/wasm. The final manifest that will generate the complete string defines the relation between the plain text data objects and the concatenation data object. In other words, it concatenates the two strings together and outputs "Hello World."

<div class="interactive-canvas">
    <textarea class="manifest-text" id="sample_1_manifest_text" cols="100" rows="5">{ "doi": { "SHA256" : "A591A6D40BF420404A011733CFB7B190D62C65BF0BCDA32B57B277D9AD9F146E" } }</textarea>
    <br>
    <span class="run-button-placeholder"></span>
    <span class="render-button-placeholder"></span>
    <span class="manifest-data-placeholder" id="sample_1_manifest_data"></span>
</div>

<b>Result data</b>
DOI=A591A6D40BF420404A011733CFB7B190D62C65BF0BCDA32B57B277D9AD9F146E

<b>actor.wasm (Webassembly for concatination data)</b>
    DOI=0F23021FFDC8F3BB3302C1763F52D10F55E16A2B74CADD3EE3BD7081D6D7B89C
    
<b>arg1 (Text file with "Hello ")</b>
    DOI=A4CFC596D52C95B51AB28B4DEF04F83DA35CD3A4C6DB1B6001D4D0FA41809221
    
<b>arg2 (Text file with "World")</b>
    DOI=AA1DB5C660D3D1F3F4F9361B9848694300929BE94B74C84452A87420C59E5DF9