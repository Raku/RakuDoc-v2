
# RakuDoc Version 2

	This is the first revision of the RakuDoc markup language since the language was formulated to replace POD. POD was the documentation language for Perl 5.

<div id="Overview"></div>

## Overview
This repository contains the most recent version of the RakuDoc v2 specification, and some files that are intended to aid in determining compliance with the specification.  
The specification is complete, but there may be minor changes as flaws are detected.  
The old specification was in a single file that combined the specification with explanation and tutorial elements. The old specification - valid to v2.20.4 - now resides in a sub-directory. The new specification is in two files - an exposition and a specification.  
There are ideas about a version 3, which in particular,will need to be revised to ensure that LToR writing systems are correctly accommodated.  
Any change must be backwards compatible.  
<div id="Renderers"></div>

## Renderers
It is intended that there should be multiple renderers from RakuDoc into other output formats, and based on other languages.  
<span class="para" id="072e9a5"></span>A renderer is considered compliant if it can render into a chosen output format the file [Rakudociem-ipsum.rakudoc](compliance-files/rakudociem-ipsum.rakudoc). `rakudociem-ipsum.rakudoc` contains RakuDoc errors and the specification gives some recommendations about errors. So a compliant renderer should generate the expected warnings, though the way the warnings are handled is not defined. Some of the links in `rakudociem-ipsum.rakudoc` are to files in the `compliance-files/` directory.   
<span class="para" id="0bca3e4"></span>Currently, [RakuAST::RakuDoc::Render](https://github.com/finanalyst/rakuast-rakudoc-render) is the most complete renderer, and the distribution contains renderers to Text, HTML and Markdown.   
These are browser clickable links to&nbsp;&nbsp;• the [HTML version of the exposition file](https://htmlpreview.github.io/?https://github.com/Raku/RakuDoc-GAMMA/blob/main/rakudoc_v2_exposition.html)  in this repo.  
&nbsp;&nbsp;• the [HTML version of the specification file](https://htmlpreview.github.io/?https://github.com/Raku/RakuDoc-GAMMA/blob/main/rakudoc_v2_specification.html)  in this repo.  




----

----

Rendered from ./README.rakudoc/README at 10:41 UTC on 2026-06-10

Source last modified at 10:39 UTC on 2026-06-10

