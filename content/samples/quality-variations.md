---
title: "Quality Variations"
fullTitle: "Quality Variations"
name: "Mikhail Ignatovich"
date: 2019-07-17
weight: 6
draft: false
---

<b>KolmoLD is a way to get media data with quality variations!</b>

This use case is about a dividing image to blocks and calculating the resulting image on the client side according to traffic condition. For this example, the image was divided by frequencies after discrete fast Fourier transformation to blocks. Each block contains data with range of frequency. Low frequencies describe the main details of the image. Higher frequencies describe the precise details of the image. To get image data from these blocks you need to apply reverse FFT to them. More blocks mean the best quality.


Block 1 (low frequency)

<div class="interactive-canvas">
<textarea class="manifest-text" id="block1" cols="100" rows="3">{ "doi": { "SHA256": "387126C669906546BF6B70CF3A1A8236EF446C8898FF3A22A810A99F45B59029" } }
</textarea>
<br>
<span class="run-button-placeholder"></span>
</div>

<br>Block 2 (middle frequency)

<div class="interactive-canvas">
<textarea class="manifest-text" id="block2" cols="100" rows="3" value="s">{"doi":{"SHA256":"57A3988068E5305D7392FEE11AC144B3BA410F1CFFC09F5861E998457AEB1E7A"}}</textarea>
<br>
<span class="run-button-placeholder"></span>
</div>

<br>Block 3 (high frequency)

<div class="interactive-canvas">
<textarea class="manifest-text" id="block3" cols="100" rows="3">{ "doi": { "SHA256": "2568990365C2E8E19BBCEABA97FAA92CB0FF4FBE5203FFF8A3E90AD19A38FD65" } }</textarea>
<br>
<span class="run-button-placeholder"></span>
</div>

<br>Result image (Block 1)

<div class="interactive-canvas">
<textarea class="manifest-text" id="image_block1" cols="100" rows="3">{ "doi": { "SHA256": "D3498F585189527D77B895061F063075CAC57B2924FD30B1EF484413B5CF8406" } }</textarea>
<br>
<span class="run-button-placeholder"></span>
<span class="render-button-placeholder"></span>
<span class="manifest-data-placeholder" id="image_block1_dw"></span>
</div>

<br>Result image (Block 1 + Block 2)

<div class="interactive-canvas">
<textarea class="manifest-text" id="image_block1block2" cols="100" rows="3">{ "doi": { "SHA256": "744AEF1BF83A2FAF41202E3839CAB9656DF6C8A5582286D6E4F0E67A11C078AB" } }</textarea>
<br>
<span class="run-button-placeholder"></span>
<span class="render-button-placeholder"></span>
<span class="manifest-data-placeholder" id="image_block1block2_dw"></span>
</div>

<br>Result image (Block 1 + Block 2 + Block 3)

<div class="interactive-canvas">
<textarea class="manifest-text" id="image_block1block2block3" cols="100" rows="3">{ "doi": { "SHA256": "C7ACD3820E98ECDA2DDD9753680C1E6EAA51C4BC2D5A13B41F372ED0D1A11ACE" } }</textarea>
<br>
<span class="run-button-placeholder"></span>
<span class="render-button-placeholder"></span>
<span class="manifest-data-placeholder" id="image_block1block2block3_dw"></span>
</div>