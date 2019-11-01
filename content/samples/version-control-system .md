---
title: "Version Control System"
fullTitle: "Version Control System"
name: "Mikhail Ignatovich"
date: 2019-10-01
weight: 7
draft: false
---

# Version Control System

<b>KolmoLD is a way to build version control systems!</b>

Using KolmoLD technology as a storage subsystem of the version control system allows reducing the amount of storing data. It could be possible by implementing type-specific program modules for optimal calculating differences between file versions and for reproducing original files from the calculated difference on the client-side.
KolmoLD has the type-specific program module for docx, xlsx and pptx Microsoft Office 2007-2013 XML files. It based on the [delta encoding algorithm](https://en.wikipedia.org/wiki/Delta_encoding) but applied in a more optimal way. It makes possible to reduce the size of the calculated difference <b>up to 40 times</b>.

![alt text](/images/diffs table.jpg)

Listed below example with pptx file.

<div class="interactive-canvas">
<textarea class="manifest-text" id="sample_1_manifest_text" cols="100" rows="5">{ "doi": { "SHA256": "05C62DEDE874781224CD154D29CBE87FEA2D2BE3A193DD155E6ED7E0820BE6CB" } }</textarea>
<br>
<span class="run-button-placeholder"></span>
<span class="render-button-placeholder"></span>
<span class="manifest-data-placeholder" id="sample_1_manifest_data"></span>
</div>

First version of file is 2BB3DE761030BF744FF7389CCFD492D6A1418BC57B88E600C311D6360EDC9360, second version is 05C62DEDE874781224CD154D29CBE87FEA2D2BE3A193DD155E6ED7E0820BE6CB. The difference between versions calculated by KolmoLD 35B4F7203128D900B0D76875F1D5E1F8A56E9E312F2A7DC0BD5A7C114D11750E has size 20,184 bytes. If you calculate the difference between these versions by the standard way it will be 126,253 bytes. Calculated by KolmoLD difference is <b>6.25 times smaller</b>.