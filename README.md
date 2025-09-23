
# RakuDoc Version 2

	This is the first revision of the RakuDoc markup language since the language was formulated to replace POD. POD was the documentation language for Perl 5.

----

## Table of Contents

<a href="#Overview">Overview</a>   
<a href="#Renderers">Renderers</a>   


<div id="Overview"></div>

## Overview
<span class="para" id="ff9e307"></span>This repository contains the most recent version of the RakuDoc v2 specification document, and some files that are intended to aid in determining compliance with the specification.   
<span class="para" id="0d212d6"></span>The specification is complete, but there are ideas about how to extend some parts of it.   
<span class="para" id="9408c03"></span>In particular, version 3 will need to be revised to ensure that LToR writing systems are correctly accommodated.   
<span class="para" id="003a863"></span>Any change must be backwards compatible.   
<div id="Renderers"></div>

## Renderers
<span class="para" id="aa831e5"></span>It is intended that there should be multiple renderers from RakuDoc into other output formats, and based on other languages.   
<span class="para" id="072e9a5"></span>A renderer is considered compliant if it can render into a chosen output format the file [Rakudociem-ipsum.rakudoc](compliance-files/rakudociem-ipsum.rakudoc). `rakudociem-ipsum.rakudoc` contains RakuDoc errors and the specification gives some recommendations about errors. So a compliant renderer should generate the expected warnings, though the way the warnings are handled is not defined. Some of the links in `rakudociem-ipsum.rakudoc` are to files in the `compliance-files/` directory.   
<span class="para" id="0bca3e4"></span>Currently, [RakuAST::RakuDoc::Render](https://github.com/finanalyst/rakuast-rakudoc-render) is the most complete renderer, and the distribution contains renderers to Text, HTML and Markdown.   
<span class="para" id="d4e3b6c"></span>This is a browser clickable link to the [HTML file](https://htmlpreview.github.io/?https://github.com/Raku/RakuDoc-GAMMA/blob/main/rakudoc_v2.html) in this repo.



----

----

Rendered from ./README.rakudoc/README at 09:37 UTC on 2025-09-23

Source last modified at 09:36 UTC on 2025-09-23

