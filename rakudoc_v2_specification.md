
# RakuDoc

	Raku markup for documenting Raku software to aid development and use.


<div id="VERSION&nbsp;&nbsp;&nbsp;&nbsp;2.20.7"></div><div id="VERSION"></div>

## VERSION&nbsp;&nbsp;&nbsp;&nbsp;2.20.7
<div id="Introduction"></div>

## Introduction
<span class="para" id="96251f6"></span>RakuDoc has a variety of **components** that allow for rich structured documents, provide markup hints to renderers without assuming an output format, extract information from compiled programs, provide information to programs, and enable custom developer extensions.   
<span class="para" id="d83cb2d"></span>This document describes a revision of RakuDoc modifying the original speculation [S26](https://design.raku.org/S26.html) that was implemented as Raku was developed. RakuDoc is intended to be backwards compatible with the POD6 markup language based on S26.   
RakuDoc is extensible and renderers are free to provide extra functionality beyond the minimum set of instructions described here.  
<span class="para" id="ace4162"></span>The markup language is called **RakuDoc** with the *CamelCase* spelling in order to highlight both the *Raku* and *Doc* parts for a newcomer. Some authors may prefer **Rakudoc** with *Titlecase* spelling, which is considered a possible but non-canonical variant.   
<span class="para" id="15ca6e5"></span>The file extension for a file containing RakuDoc source is **.rakudoc**.   

<div id="A note about indentation"></div><div id="A_note_about_indentation"></div>

### A note about indentation
<span class="para" id="0e1045f"></span>As it will become apparent, whitespace and indentation plays an important role in RakuDoc. There are [quite a few horizontal whitespace characters](https://unicode-explorer.com/articles/space-characters); no equivalence is made between them by Rakudo. For example, tabs and spaces are treated as two separate horizontal whitespace characters, with no assumption that a single tab is equivalent to four (or two or eight) spaces.   
<span class="para" id="e67573c"></span>Consequently, mixing spaces and tabs may **appear** to create the same visual margin in some editor, but they may be **treated** as different margins when parsing RakuDoc.   
<span class="para" id="13f379e"></span>It is recommended that RakuDoc authors choose a single whitespace character (*e.g.* only tabs or only spaces) for margin alignment.   

<div id="Two use cases for RakuDoc"></div><div id="Two_use_cases_for_RakuDoc"></div>

## Two use cases for RakuDoc
<span class="para" id="5dfed40"></span>RakuDoc can be thought of as having components that are closely connected to a program or module that is being documented (<span style="font-weight: 600; font-style: italic">code-oriented RakuDoc</span>), and components that can be used for text-oriented documents, such as the source for a webpage or a book (<span style="font-weight: 600; font-style: italic">text-oriented RakuDoc</span>).   
Code-oriented RakuDoc is expected to be "consumed" by users in an editor or Integrated Design Environment (IDE), while text-oriented RakuDoc is consumed in some rendered version, such as an HTML web page, a command-line manpage, or a chapter in an e-book.  
<span class="para" id="5fa67ea"></span>When RakuDoc components are viewed in an editor or IDE to provide information about variables, methods, roles, classes and the like, the information could be made available in a pop-up whenever a documented term is selected in some way (*e.g.* hovering a mouse over it). In the editor context, RakuDoc blocks and markup should then be treated as a comment to the code. Editors are not expected to render the RakuDoc other than to show the RakuDoc components verbatim, but may do so if it seems expedient.   
When RakuDoc components are viewed in a rendered version (for example, as HTML), components related to the executable aspects of the program should be ignored.  
<span class="para" id="5c97c8c"></span>Within the body of a program there may be several sections of code, which is called the <span style="font-weight: 600; font-style: italic">ambient context</span>, interleaved between sections of RakuDoc.   
<span class="para" id="f6b7756"></span>Sections of code that are intended to be examples within the documentation (*i.e.* code that is not actual interleaved executable code) can be specified in `=code` blocks, which are treated as integral parts of the RakuDoc, not as source code in ambient context.   
<span class="para" id="406919e"></span>Consider a source file containing the definition of a class. When the file is edited in an IDE (*e.g.* the class is being developed or maintained) the code-oriented RakuDoc is useful to help the developer understand the internal structure and function of specific elements within the code.   
Furthermore, when the class is imported into another Raku program the IDE will be able to access the information in declarator blocks attached to specific terms, without needing the source code to be available.  
<span class="para" id="891a68a"></span>However, by including text-oriented RakuDoc in the same file, end-user documentation can be provided for the class within the same source file. By passing the source through a renderer, a documentation file (*e.g.* a README.md file for a github repo) can be generated.   
<span class="para" id="1941957"></span>This document describes the minimum version of RakuDoc that a renderer or editor must recognize, along with some expected rendering behaviours for text-oriented renderers. *“Expected behaviours”* means that a renderer should approximate the behaviour as far as possible given the limitations of the output format, and that the approximation should be a reasonable interpretation of the standard described here.   
<span class="para" id="a3cf768"></span>The RakuDoc design assumes certain types of customisability, such as the ability to define new blocks or markup instructions. In order to access blocks or functionality not described in this document, the file containing such RakuDoc instructions should contain a `use` statement that loads a module that provides the information needed to render the extensions. The information provided by this module may be renderer-specific.   
<span class="para" id="6c07cad"></span>[Further exposition on this topic is available. ](rakudoc:rakudoc_v2_exposition.rakudoc)   
<div id="Components"></div>

## Components
RakuDoc has four main types of components, which are distinguished by their scope and by effect they have on other components.  

<span style="font-weight: 600; font-style: italic">Directives</span>

&nbsp;&nbsp;<span style="background-color: lightgrey;"><span class="para" id="8ac3365"></span>Directives define how blocks work. Directives have a similar syntax to regular blocks, but they actually operate on other blocks. New directives cannot be defined. Directives specify **behaviours** rather than content.</span>

<span style="font-weight: 600; font-style: italic">Blocks</span>

&nbsp;&nbsp;<span style="background-color: lightgrey;"><span class="para" id="d5bdc09"></span>Blocks define complete text components, such as a new paragraph or a code sample or a table. New kinds of blocks can be defined. Blocks contain **actual content** which is to be directly rendered in some way.</span>

<span style="font-weight: 600; font-style: italic">Markup instructions</span>

&nbsp;&nbsp;<span style="background-color: lightgrey;">Markup instructions typically define inline items embedded within a block. Some markup instructions only affect the visual rendering of their contents. Others may have side-effects (such as including items into an index or glossary). New markup instructions can be defined.</span>

<span style="font-weight: 600; font-style: italic">Metadata options</span>

&nbsp;&nbsp;<span style="background-color: lightgrey;">Options provide information to a block. They may produce side-effects or alter the way a block should be rendered. A document writer can associate any metadata option with any block or directive. A renderer is only required to comply with those listed in this document.</span>


<div id="Directive syntax"></div><div id="Directive_syntax"></div>

### Directive syntax
<span class="para" id="1f841b8"></span>These are the syntax forms for each directive. The `=` of each directive must be the first non-whitespace character on a given line.   

<div id="Block and document delimiters"></div><div id="Block_and_document_delimiters"></div>

#### Block and document delimiters
&nbsp;&nbsp;• **=begin** specifies the start of a delimited block  
&nbsp;&nbsp;• **=end** specifies the end of a delimited block  
&nbsp;&nbsp;• **=for** specifies the start of an extended block  
&nbsp;&nbsp;• **=finish** specifies the end of all ambient code; everything thereafter is a string  

<div id="Procedural table constructors"></div><div id="Procedural_table_constructors"></div>

#### Procedural table constructors
&nbsp;&nbsp;• **=column** specifies the start of a column, but only inside a `table` block  
&nbsp;&nbsp;• **=row** specifies the start of a row, but only inside a `table` block  

<div id="Alias declaration"></div><div id="Alias_declaration"></div>

#### Alias declaration
<span class="para" id="8bf4a6b"></span>The **=alias** directive specifies a text substitution available in subsequent `A&lt;...>` markup instructions. The general syntax for an alias directive is:   

```
=alias ALIAS_NAME Text for substitution
=                 Optional extra text
```

<div id="Lexical block and markup configuration"></div><div id="Lexical_block_and_markup_configuration"></div>

#### Lexical block and markup configuration
<span class="para" id="e40a686"></span>The **=config** directive specifies default options to be applied to specific types of blocks or markup instructions within the remainder of the current block-scope. These default options apply from immediately after the `=config` directive up to the end of the innermost surrounding block.   
The general syntax for configuration directives is:  

```
=config BLOCK_TYPE  :CONFIG<OPTIONS>
=                   :OPTIONAL&lt;EXTRA CONFIG OPTIONS>
```
<span class="para" id="ff16c3f"></span>If the block-type is single uppercase character **__X__**, the `=config` directive applies the specified configuration options to the **__X__** markup instruction. For example:   

```
=config C :allow<B I>
```
<span class="para" id="e75b0bd"></span>This configures the `C&lt;>` markup instruction to recognize nested `B&lt;>` and `I&lt;>` markup instructions (both of which would otherwise be treated as verbatim within a `C&lt;>` instruction).   
<span class="para" id="98353ac"></span>To avoid ambiguities [Naming rules](Naming rules) are applied to blocks and markup instructions.   
<span class="para" id="031358d"></span>Note that since the `=config` directive applies to *block types*, `=config item ...` and `=config numitem ...` are equivalent. More information about *block types* can be found in the section on [Block type, level and enumeration](Block type, level and enumeration).   

<div id="Global document configuration"></div><div id="Global_document_configuration"></div>

#### Global document configuration
<span class="para" id="1a4ae5e"></span>The **=document** directive specifies new default values for options applicable to the whole document. These values are applied when the final rendering is completed.   
The syntax is:  

```
=document :DOCUMENT<OPTIONS>
=         :OPTIONAL&lt;EXTRA DOCUMENT OPTIONS>
```
<span class="para" id="90951c8"></span>A renderer may provide custom options not specified here. These should have mixed-case names, see [Naming rules](Naming rules).   

<div id="Content insertion"></div><div id="Content_insertion"></div>

#### Content insertion
<span class="para" id="066eee2"></span>The **=place** directive specifies a source of information that is to be placed in the current block. The general syntax of **=place** is   

```
=place URL :OPTIONS
=          :MORE<OPTIONS> :IF&lt;NECESSARY>
```

<div id="Lexical counter configuration"></div><div id="Lexical_counter_configuration"></div>

#### Lexical counter configuration
<span class="para" id="859c893"></span>The **=counter** directive reconfigures a named counter within the current lexical scope. The general syntax of **=counter** is   

```
=counter COUNTER_NAME :OPTIONS
=                     :MORE<OPTIONS> :IF&lt;NECESSARY>
```

<div id="Block syntax"></div><div id="Block_syntax"></div>

### Block syntax
Even though visually similar in some contexts, a block is not a directive...nor vice versa.  
Blocks may be specified in three ways...  

<div id="Delimited form"></div><div id="Delimited_form"></div>

#### Delimited form
<span class="para" id="43fde80"></span>A <span style="font-weight: 600; font-style: italic">delimited block</span> starts with the directive `=begin` as the first non-whitespace on a line, followed by a valid Raku identifier indicating the name of the block, followed optionally by metadata (see [#Metadata syntax](Metadata syntax) below), followed by content lines. A delimited block is only terminated by an `=end` directive followed by the same block name. A delimited block that is not terminated by an `=end same-block-name` instruction throws an error.
<span class="para" id="41af1d0"></span>For example, to specify a `para` block in delimited form:   

```
=begin para :meta<data>
This text is the content of the block.

The preceding blank line is also a part of the block,
as are these two non-blank lines.
=end para
```
The general syntax is:  

```
=begin BLOCK_TYPE  :OPTIONAL&lt;CONFIG INFO>
=                  :OPTIONAL&lt;EXTRA CONFIG INFO>
BLOCK CONTENTS
=end BLOCK_TYPE
```

<div id="Extended form"></div><div id="Extended_form"></div>

#### Extended form
<span class="para" id="057cd93"></span>An <span style="font-weight: 600; font-style: italic">extended block</span> begins with the directive `=for` as the first non-whitespace on a line, followed by the name of the block, followed by optional metadata. The next non-blank lines after the metadata specify the content of the block, which is terminated either by the first entirely blank line after the content or by the first line that begins with a RakuDoc directive or block. For example, to specify a `para` block in extended format:   

```
=for para :meta<data>
This text is the content of the block.

This text is NOT content of the block.
After a blank line, a new block is started.
```
The general syntax is:  

```
=for BLOCK_TYPE  :OPTIONAL&lt;CONFIG INFO>
=                :OPTIONAL&lt;EXTRA CONFIG INFO>
BLOCK DATA
```

<div id="Abbreviated form"></div><div id="Abbreviated_form"></div>

#### Abbreviated form
<span class="para" id="7a05e9e"></span>In an <span style="font-weight: 600; font-style: italic">abbreviated block</span>, the name of the block immediately follows an `=` sign, which must be the first non-whitespace character on the line. Everything after the block name (up to the first blank line or the start of the next directive or block) is considered the content of the block. For example, to specify a `para` block in abbreviated form:   

```
=para This text is the content of the block.

This text is NOT content of the block.
After a blank line, a new block is started.
```
Note that abbreviated blocks cannot be specified with metadata. Any apparent metadata elements placed after an abbreviated block name are instead considered to be part of the block contents.  
<span class="para" id="3d19de1"></span>Where an extended or abbreviated block uses a blank line as the block terminator, the blank line is not included as part of the content of the block. All subsequent whitespace, whether horizontal or vertical (spaces, tabs, new lines), is ignored. Note that horizontal and vertical whitespace can be included inside a delimited block, and may be significant (*e.g.* in [#Verbatim blocks](Verbatim blocks)).   
Naturally, the contents of blocks may contain embedded RakuDoc markup codes, but not all blocks may contain other blocks. These rules are:  
&nbsp;&nbsp;• Abbreviated- and extended-form blocks can *never* contain any other kind of block.  
&nbsp;&nbsp;• The delimited form of the following <span style="font-weight: 600; font-style: italic">atomic blocks</span> do ***not*** contain any other kind of block (*i.e.* they treat their contents as pure data, even if those contents happen to resemble a block definition): `citation`, `code`, `comment`, `data`, `formula`, `head`, `input`, `output`, [custom blocks](User-defined blocks)  
&nbsp;&nbsp;• The delimited form of the following <span style="font-weight: 600; font-style: italic">paragraph-level blocks</span> may contain only atomic blocks or [implicit paragraphs](Ordinary paragraphs): `defn`, `item`, `para`  
&nbsp;&nbsp;• The delimited form of the following <span style="font-weight: 600; font-style: italic">container blocks</span> may contain *any* other kind of block (including other container blocks): `cell`, `nested`, `pod`, `rakudoc`, `section`, `SEMANTIC`  
&nbsp;&nbsp;• The delimited form of the `table` block may directly contain only `cell` and/or `comment` blocks.  
&nbsp;&nbsp;&nbsp;&nbsp;▹ *Note, however, that `cell` blocks may themselves contain any other kind of block, which means that `table` blocks may **indirectly** contain any other kind of block.*  
&nbsp;&nbsp;&nbsp;&nbsp;▹ *A `table` may also contain `=row` and `=column` directives, which are not blocks.*  

<div id="Markup instruction syntax"></div><div id="Markup_instruction_syntax"></div>

### Markup instruction syntax
The general syntax of a markup instruction is  

```
 <instruction><opening marker><content> | <meta list><closing marker>
```
&nbsp;&nbsp;• **__&lt;instruction>__**  
&nbsp;&nbsp;• **__&lt;opening marker>__**  
&nbsp;&nbsp;• **__&lt;content>__**  
&nbsp;&nbsp;• **__&lt;meta list>__**  
&nbsp;&nbsp;• **__&lt;closing marker>__**  
For example:  

```
This is U&lt;unusual> text, and this is I&lt;important>.
And this is a L&lt;link to the Raku Programming Language|https://raku.org>.
```
Which would be rendered as:  
> <span class="para" id="869d304"></span>For example: This is __unusual__ text, and this is *important*. And this is a [link to the Raku Programming Language](https://raku.org).


<div id="Metadata syntax"></div><div id="Metadata_syntax"></div>

### Metadata syntax
Metadata is specified using a Raku option pair, where the value portion is always parsed using Raku semantics. The following table contains some typical examples.  

<div id="Table"></div>
| **Value is...** | **Specify with...** |
| :----:  | :----: |
| True  | `:key` |
 | False  | `:!key` |
 | Literal  | `:key<answer>` |
 | List  | `:key("foo", 42)` |
 | Hash  | `:key{ :note('Rosebud, they said'), :emphasize }` |

<span class="para" id="1996bfc"></span>Numerical values can be specified in any Raku-compatible format (42, 42.0, 42e0, 0x2a, 0d42, 0o52, 0b101010). Strings can either be specified using single or double quotes, or angle brackets (`'foo'`, `"bar"`, ` <baz> `). Note that if a string with whitespace is specified in angle brackets, it is in fact a list of strings: ` <foo bar baz> ` is the same as ` ('foo','bar','baz') `.   
Within the context of the parser of the Raku Programming Language, it is also possible to refer to any compile-time value inside the meta-data.  
<span class="para" id="fdfdf76"></span>Requiring Raku semantics for the value component of a metadata option means that for compliance to this specification, RakuDoc markup is prohibited within a metadata option. For example, ` :bullet( E&lt;BALLOT BOX WITH X> ) ` is prohibited. However, Raku has a syntax for Unicode characters, so ` :bullet«\c[BALLOT BOX WITH CHECK]» ` is possible. Note the use of ` «...» ` delimiters, which stringify their contents with interpolation, ensuring the `\c[...]` interpolator is correctly converted.   
A renderer may provide a mechanism for allowing RakuDoc within a metadata option but the value part must still conform to Raku semantics, for example,  

```
:caption('This is a heading with I<important markup> in it')
```
The outer single quotes ensures that the parser passes a plain string to the renderer, which may then post-process the string.  
<span class="para" id="942b5ab"></span>The metadata of an extended block or abbreviated block may extend beyond the first line declaring the block. Each subsequent line must start with an `=` in the first virtual column, meaning that it must vertically align with the `=` of the RakuDoc block declaration, and it must be followed by at least one horizontal whitespace character.   
For example:  

```
=for head1 :a-first-line-key<firstvalue> :another-first-line-key<xyz>
=          :a-second-line-key(42)
= :a-third-line-key<third>
Content for the heading block
```
<div id="Directives"></div>

## Directives
<span class="para" id="eb9a59b"></span>Directives are similar in form to abbreviated *block* instructions, but they do not have the extended or delimited forms. If a directive name is preceded by a `=begin`, `=for` or `=end`, then an error will be thrown.   
<span class="para" id="902789d"></span>Directives cannot be enumerated. If a directive is prefixed by `num`, then a warning will be generated and the prefix ignored.   
<div id="Aliases"></div>

### Aliases
<span class="para" id="7944898"></span>The `=alias` directive provides a way to define block-scoped synonyms for longer RakuDoc sequences, (meta)object declarators from the code, or even entire chunks of ambient source. These synonyms can then be inserted into subsequent RakuDoc using the [`A&lt;> formatting code`](Alias placements).   
<span class="para" id="d5e76c0"></span>An `=alias` is scoped from immediately after its declaration up to the end of the innermost surrounding RakuDoc block.   
The alias directive takes two arguments. The first is an identifier (which is usually specified in uppercase, though this is not mandatory). The second argument consists of one or more lines of replacement text.  
<span class="para" id="6a0840b"></span>Each `=alias` directive creates a block-scoped RakuDoc macro that can be invoked during document generation by placing the identifier (*i.e.* the first argument of the alias) in an `A&lt;>` formatting code. This formatting code is then replaced by the text returned by the new macro.   
<span class="para" id="5eea4da"></span>The replacement text returned by the alias macro begins at the first non-whitespace character after the alias’s identifier, and continues to the end of the line. You can extend the replacement text over multiple lines by starting the following line(s) with an `=` (at the same level of indentation as the `=alias` directive itself) followed by at least one whitespace. Each additional line of replacement text uses the original line’s (virtual) left margin, as specified by the indentation of the replacement text on the `=alias` line.   
For example:  

```
=alias PROGNAME    Earl Irradiatem Evermore
=alias VENDOR      4D Kingdoms
=alias TERMS_URLS  =item L<http://www.4dk.com/eie>
=                  =item L<http://www.4dk.co.uk/eie.io/>
=                  =item L<http://www.fordecay.ch/canttouchthis>

The use of A<PROGNAME> is subject to the terms and conditions
laid out by A<VENDOR>, as specified at:

A<TERMS_URLS>
```
This would produce:  
> <span class="para" id="44101f5"></span>The use of Earl Irradiatem Evermore is subject to the terms and conditions laid out by 4D Kingdoms, as specified at:   
<span class="para" id="2f4d9c2"></span>&nbsp;&nbsp;• [http://www.4dk.com/eie](http://www.4dk.com/eie)  
&nbsp;&nbsp;• [http://www.4dk.co.uk/eie.io/](http://www.4dk.co.uk/eie.io/)  
&nbsp;&nbsp;• [http://www.fordecay.ch/canttouchthis](http://www.fordecay.ch/canttouchthis)

The advantage of using aliases is, obviously, that the same alias can be reused in multiple places in the documentation. Then, if the replacement text ever has to be changed, it need only be modified in a single place:  

```
=alias PROGNAME    Count Krunchem Constantly
=alias VENDOR      Last Chance Receivers Intl
=alias TERMS_URLS  L<http://www.c11.com/generic_conditions>
```
<span class="para" id="f8caefd"></span>Alias placement codes may also specify a default display text, before the alias name and separated from it by a `|`. When a display is specified, it will be used if the requested alias cannot be found (and an *"unknown alias"* warning will be issued in that case):   

```
The use of A&lt;this program | SOFTWARE> is subject to the terms and conditions
laid out by A&lt;our company | COMPANY>, as specified at:

    A&lt;(Please visit our website) | OURTERMS>
```
produces ...  
> <span class="para" id="4345386"></span>The use of this program  is subject to the terms and conditions laid out by our company , as specified at: (Please visit our website)

Furthermore, since none of the aliases were specified in this document, error messages will be produced by the renderer (perhaps at the end of the rendered page).  

<div id="Block specifiers"></div><div id="Block_specifiers"></div>

### Block specifiers
<span class="para" id="99b393a"></span>The `=begin`, `=end`, and `=for` directives all specify types of blocks. They have already been illustrated in multiple examples and will not be described further here.   

<div id="Block configuration"></div><div id="Block_configuration"></div>

### Block configuration
<span class="para" id="685c4c2"></span>All block forms can be associated with metadata options. Typically the metadata are specified for a block using the **extended** or **delimited** forms.   
<span class="para" id="5cd5745"></span>If the same option needs to be added to multiple blocks, this can be done using a `config` directive, and then the **abbreviated** block form can be used in the [same block scope](Block scope).   
<span class="para" id="d5b95f0"></span>For example, suppose we want all `=code` blocks to be labelled with a `:delta` within some section of a document ([More information on developer notes](Developer or delta notes)). Rather than add a `:delta` modifier to every individual `=code`, we could configure every =code block to be automatically given the same developer note, like so:   

```
=config code :delta(v2.3.2+, 'oFun enhancements') 
=code method droll { say 'what a cool operator' }
=code method drool { say 'what a slob' }
=code method drill { say 'keep up' }
```
<span class="para" id="030b617"></span>Within the same block scope, *e.g.* between `=begin section` and `=end section` instructions, one or more `=config` directives can be specified to define the default behaviour of specific block types. Successive `=config` directives within the same scope are cumulative in effect. Because `=config` directives specify *default* behaviours, whenever a particular kind of metadata is explicitly specified for an instance of that block type, that option overrides the defaults set by any active `=config` blocks. For example:   

```
=config item1 :bullet« \c[Earth Globe Europe-Africa] » 
=config item2 :bullet« \c[Hand with Index and Middle Fingers Crossed] » 
=comment  In the following list, =item1 and =item2 will now have non-standard bullets

The major sources of sustainable energy are:
    =item1 geothermal
    =item1 fusion
    =item2 (eventually)

=begin section
    =config item1 :toc :headlevel(2)
    =comment  The following items still have non-standard bullets but now they will
              also be added to the table of contents as if they were =head2 blocks

    Important items are:
        =item1 small scale fusion
        =item1 room temperature superconductors

=end section

=comment  The following list items will NOT be added to the Table of Contents
          because the section containing that particular =config has now ended.
          They will still have the non-standard Earth bullets though,
          because those two =config directives are still in scope.

    =item1 matter-antimatter annihilation
    =item1 zero-point energy
```
<span class="para" id="9e58f96"></span>There is more discussion of `=config` in the context of [Formatting codes](Formatting codes).   

<div id="Document management"></div><div id="Document_management"></div>

### Document management
<span class="para" id="1db1bb5"></span>The `=document` directive allows the author to override the values of document-level options. It is required that all document-level options have suitable default values, some of which are specified in this document. Any defaults that are not specified here are left to the discretion of the developer of each renderer.   
<span class="para" id="0e3df60"></span>Unlike the `=config` and other directives, the `=document` directive is **not** lexically scoped. Hence, one or more `=document` directives may be specified anywhere in a RakuDoc document and their effects are cumulative. That is, the options passed to each successive `=document` directive are accumulated and concatenated to determine the final set of options. Hence, the two directives:   

```
=document :!auto-toc  :auto-index    :citation-style<acm>

=document :auto-toc   :!auto-index   :citation-locale<en-US>
```
... are equivalent to the single directive:  

```
=document :!auto-toc :auto-index :citation-locale<en-US> :auto-toc :!auto-index :citation-style<acm>
```
... which is equivalent to:  

```
=document  :citation-locale< en-US> :auto-toc  :!auto-index  :citation-style<acm>
```
<span class="para" id="722a56f"></span>... because the `:auto-toc` and `:auto-index` options in later `=document` directives override those in earlier directives (which is the usual RakuDoc behaviour).   
The following document level options are specified:  

<div id="Document_level_options"></div>
<div>Document level options</div> | **Option name** | **Default** | **Description** |
| :---: | :---: | :---: |
 | `:error` | True | If True, warnings for any error conditions are rendered at the end of the document. |
 | `:auto-toc` | False unless overridden by the presence of `=head` or semantic blocks. | Whether to automatically generate a Table of Contents, or mandate no automatic rendering of a Table of Contents, more in [Table of Contents, Index, and Citations](Table of Contents, Index, and Citations). |
 | `:auto-index` | False unless overridden by the presence of `X&lt; ... >` markup. | Whether to automatically generate an Index, or mandate no automatic rendering of an Index, more in [Table of Contents, Index, and Citations](Table of Contents, Index, and Citations). |
 | `:auto-citations` | False unless overridden by the presence of `Q&lt; ... >` markup. | Whether to automatically generate a Citations list, or mandate no automatic rendering of a Citations list, more in [Table of Contents, Index, and Citations](Table of Contents, Index, and Citations). |
 | `:lang` | en | The language of the content of the document. |
 | `:citation-style` | ieee | The style used to format citations. More detail can be found in [Citations](Citations). |
 | `:citation-locale` | en-GB | The locale used to format citation dates and numbers. More detail can be found in [Citations](Citations). |

<div id="Document termination"></div><div id="Document_termination"></div>

### Document termination
<span class="para" id="6664020"></span>The `=finish` directive indicates the end of all ambient contexts within the document. This means that the parser will treat all the remaining text in the file as a string.   
<span class="para" id="10e3cf2"></span>Any text after the `=finish` block can be accessed as a string within the program (*i.e.* the ambient code) via the `$=finish` variable. This can be used to provide data to a program, such as a test.   

```
use JSON::Fast;
my %h = from-json( $=finish );
say %h.raku;
# more lines of code

=finish
{ "key1": "a string value", "key2": "another value" }
```
This will generate the following output:  

```
{:key1("a string value"), :key2("another value")}
```

<div id="Table constructors"></div><div id="Table_constructors"></div>

### Table constructors
<span class="para" id="94efdd1"></span>The `=row` and `=column` instructions are directives, not blocks, and they are described in more detail in the section on the [procedural `=table` block](Procedural description of tables).   

<div id="Placement from another (external) source"></div><div id="Placement_from_another_(external)_source"></div>

### Placement from another (external) source
<span class="para" id="c06dff7"></span>The `=place` instruction is a directive that tells the renderer to obtain text, information, or an object from another source.   
The rendered version of the data placed in the text is considered to form its own block scope.  
<span class="para" id="9999e22"></span>An in-line version of `=place` is `P&lt;...>`, which is described in more detail in the section on markup codes.   
<span class="para" id="522f890"></span>The schema of the URL specified in a `=place` directive specifies where to look for the external content:   

<div id="Table_0"></div>
| **Schema** | **Where to look for the resource** |
| :----:  | :----: |
| `http(s):` | Look on the web  |
 | `file:`    | Look on the local filesystem  |
 | `rakudoc:` | Look in the usual places for Raku documentation |
 | `man:`     | Look via a local man(1)  |
 | `defn:`    | Look in the current document  |

Meanwhile, the kind of content being placed is inferred from the final extension of the URL. For example:  

<div id="Table_0"></div>
| **Extension** | **How to treat the contents** |
| :----:  | :----: |
| `.txt`     | Render the contents as plaintext |
 | `.rakudoc` | Render the contents as RakuDoc  |
 | `.html`    | Render the contents as XHTML  |
 | `.md`      | Render the contents as Markdown  |
 | `.json`    | Render the contents as JSON  |
 | `.jpg`     | Render the contents as an image  |
 | `.mp4`     | Render the contents as a video  |

Renderers are not required to render anything other than plaintext and RakuDoc, but may also support other formats if they wish.  
In the case where a URL does not end in a recognized extension:  

```
=place https://example.org/landingpage

=place rakudoc:App::Rak

=place file:/usr/share/legal/std_disclaimer
```
...then the type of content (and hence rendering) may be inferred from the schema:  

<div id="Table_0"></div>
| **Schema** | **Inferred content type if no final extension** |
| :----:  | :----: |
| `http(s):` | XHTML  |
 | `file:`    | plaintext  |
 | `rakudoc:` | RakuDoc  |

<span class="para" id="3e74b83"></span>A **=place** directive may be accompanied by metadata options to ensure that a reference can be made in the Table of Contents, or to provide an ALT text. For example:   

```
=place https://raku.org/img/camelia-logo.png  :!toc :caption<Raku’s mascot> :alt<A cheerful butterfly>
```
will produce ...  

<div id="Raku’s mascot"></div><div id="Raku’s_mascot"></div>

## Raku’s mascot
![Raku’s mascot](https://raku.org/img/camelia-logo.png)

<span class="para" id="e7b548c"></span>Use `=place ... :toc :headlevel(3)` to treat the `:caption` text as a `=head3`, and include it in the Table of Contents as a `=head3` caption.   
> <span class="para" id="e1a6fcd"></span>Note that when any of the `toc:`, `index:`, or `citation:` schemas is used, the automatic rendering of a full Tables of Contents, Index, or Citations list will be cancelled. See [Table of Contents, Index, and Citations](Table of Contents, Index, and Citations) for more detail.


<div id="Enumeration management"></div><div id="Enumeration_management"></div>

### Enumeration management
<span class="para" id="39408d5"></span>The `=counter` instruction is a directive that affects the behaviour of the counters used by numbered blocks.   
<span class="para" id="d1ae218"></span>Although each *counter* is named in the same way as a *blocktype*, the two are separate entities.   
<span class="para" id="b088728"></span>The directive is followed by the name of a counter *(**not** the name of a block)* and one or more of:   
&nbsp;&nbsp;• `:restart`  
&nbsp;&nbsp;• `:!restart`  
&nbsp;&nbsp;• `:restart( INTEGER )`  
&nbsp;&nbsp;• `:restart-after< BLOCK LIST >`  
&nbsp;&nbsp;• `:restart-except-after< BLOCK LIST >`  
&nbsp;&nbsp;• `:prefix< BLOCK >`  
<span class="para" id="3ac8347"></span>These options may not be used in a `=config` statement, nor may `=config` options appear in a `=counter` directive. In case of error, a warning message will be generated and the option will be ignored.   
<span class="para" id="702c09a"></span>The directive is described in more detail in the [section on enumerated blocks](Enumerated blocks)   

<div id="Code-oriented RakuDoc"></div><div id="Code-oriented_RakuDoc"></div>

## Code-oriented RakuDoc
The following RakuDoc components are primarily intended for use when editing Raku code within an editor. Standalone renderers may ignore them.  

<div id="Declarator blocks"></div><div id="Declarator_blocks"></div>

### Declarator blocks
Declarator blocks differ from other blocks in that they do not have a specific type. Instead, they are attached to a particular element of the ambient source code.  
<span class="para" id="53a35a0"></span>Declarator blocks are introduced by a special comment: either `#|` or `#=`, which must be immediately followed by either a space or an opening bracket character, such as a curly brace `{`, `(` or `«`. If followed by a space, the block is terminated by the end of line; if followed by one or more opening bracket characters, the block may extend over multiple lines and is terminated by the matching sequence of closing bracket characters.   
RakuDoc markup instructions may be used inside declarator blocks.  
<span class="para" id="03f55ae"></span>Blocks starting with `#|` are attached to the code after them, and blocks starting with `#=` are attached to the code before them.   
Since declarator blocks are attached to source code, they can be used to document classes, roles, subroutines and in general any statement or block.  
<span class="para" id="3c67ae1"></span>The `WHY` method can be used on these classes, roles, subroutines, *etc.* to return the attached RakuDoc value.   
For example:  

```
#| Base class for magicians
class Magician {
  has Int $.level;
  has Str @.spells;
}


#| Fight mechanics
sub duel(  #= Magicians only, no mortals.
  Magician $a,  #= first magician
  Magician $b,  #= second magician
) {
}

say Magician.WHY; # OUTPUT: «Base class for magicians␤»
say &duel.WHY.leading; # OUTPUT: «Fight mechanics␤»
say &duel.WHY.trailing; # OUTPUT: «Magicians only, no mortals.␤»
say &duel.signature.params[0].WHY;  # OUTPUT: «first magician␤»
say &duel.signature.params[1].WHY;  # OUTPUT: «second magician␤»
```
These declarations can extend to several lines. For example:  

```
#|( This is an example of stringification:
    * Numbers turn into strings
    * Regexes operate on said strings
    * C<with> topicalizes and places result into $_
)
sub search-in-seq
#=« Uses
    * topic
    * decont operator
»
  ( Int $end, Int $number ) {
    with (^$end).grep( /^$number/ ) {
        .say for $_<>;
    }
}
```
A useful idiom is to place trailing declarator blocks on the parameters of the MAIN sub. These comments will be picked up automatically by USAGE.  

```
sub MAIN(
    Str $prefix,   #= use this prefix on output lines
    Int :$verbose, #= how many lines of output per result (0 = no output)
)
```

<div id="Data blocks"></div><div id="Data_blocks"></div>

### Data blocks
<span class="para" id="ecac283"></span>`=data` blocks are used to specify named and/or ordered chunks of data for the ambient code. They may appear anywhere within a source file, and as many times as required.   
<span class="para" id="7885f90"></span>The corresponding variable, `$=data` holds an object that does both the Associative and Positional roles.   
<span class="para" id="ea23029"></span>Each `=data` block can be given a `:key` option, to name it. The contents of any `=data` block with a key are accessible (as a single string) via the Associative aspect of `$=data` object. For example:   

```
=begin data :key<Virtues>
Laziness
Impatience
Hubris
=end data

say 'The three virtues are:';
say $=data<Virtues>;
```
<span class="para" id="28be9f3"></span>The contents of any `=data` block that does not have a `:key` are accessible (as a single string) via the Positional aspect of `$=data`. Unkeyed `=data` blocks are stored in the same order they appear in the file. For example:   

```
say 'The second anti-virtue is: ', $=data[1];

=data Industry
=data Patience
=data Humility
```
<span class="para" id="5e85766"></span>Note that, as the preceding example illustrates, because RakuDoc is a compile-time phenomenon, it is possible to specify `=data` blocks *after* the point in the source where their contents will be used (provided they’re not being used in a `BEGIN`, of course).   
<span class="para" id="a205651"></span>When `$=data` itself is stringified, it returns the concatenation of all the unkeyed `=data` blocks the parser has seen.   
<span class="para" id="0644df2"></span>`=data` blocks are never rendered by the text-oriented RakuDoc renderers.   

<div id="Text-oriented RakuDoc"></div><div id="Text-oriented_RakuDoc"></div>

## Text-oriented RakuDoc
The RakuDoc instructions in this section are primarily intended for text-oriented rendering. However, text-oriented and code-oriented RakuDoc can be freely intermixed with Raku code in a single file; this may help to create code that is well-documented.  
When a Raku compiler parses a file, line contents are expected to be Raku code (ambient text). When a RakuDoc block is finished, the next line is assumed to revert to ambient text.  

```
#start of file
my $text; # ambient code

=head this is some text
this line continues the heading

#this line is now ambient code and so must be specified as a comment
```
<span class="para" id="704d561"></span>A `rakudoc` block reverses this assumption.   

```
#start of file
my $text; # ambient code
=begin rakudoc

=head this is some text
this line continues the heading

this line is not ambient code and so
it will be treated as an ordinary paragraph
=end rakudoc
# this line is now ambient code
```
<span class="para" id="4b1ebc1"></span>Files that are intended to contain text-oriented documentation alone should be entirely enclosed in a `rakudoc` block.   
<span class="para" id="16040a0"></span>However, files whose filename ends in a `.rakudoc` extension are always presumed to contain only text-oriented documentation, and any renderer must always treat the file’s contents as being enclosed in an implicit `=begin rakudoc`...`=end rakudoc`, unless those contents are already explicitly enclosed in a `rakudoc` block.   
<span class="para" id="43031f1"></span>Historically the name `pod` was used for this functionality. For compatibility it will still be possible to use the name `pod` instead of `rakudoc` in the foreseeable future. Note however that the Raku Programming Language may assign slightly different semantics to `rakudoc` at some time in the future.   

<div id="Block type, level and enumeration"></div><div id="Block_type,_level_and_enumeration"></div>

### Block type, level and enumeration
<span class="para" id="9ff32a7"></span>A **block type** consists of a **block base** name, such as `head`, and a **block level**, which is an integer starting from `1`. (A block type consisting of a block base without an integer implies the block level is `1`). The following are all valid *block types*: `head`, `head2`, `formula3`, `table2`, `para`.   
<span class="para" id="712c475"></span>Any block type can be associated with an enumeration by prefixing the *block name* with **num**, *e.g.* `numhead`, `numtable`, `numitem`, `numcode`, `numformula`.   
<span class="para" id="c4bf853"></span>The effects of enumeration and level on `head`, `defn`, and `item` are illustrated in the sections on those block types because they have some special properties by default, namely:   
&nbsp;&nbsp;• Sequences of `=item` and of `=defn` are grouped together into lists automatically.  
&nbsp;&nbsp;• The `=head` / `=numhead` blocks are automatically included into the main *Table of Contents*.  
&nbsp;&nbsp;&nbsp;&nbsp;▹ Semantic blocks are treated as `=head` blocks.  
<span class="para" id="9730fcb"></span>The block bases `rakudoc`, `section`, `nested`, `citation`, and `pod` are considered to be <span style="font-weight: 600; font-style: italic">container blocks</span>, which act on their contents but do not have any separate rendered contents. Consequently, even if the prefix *num* and a *level* are attached to these bases, they have no effect.   
<span class="para" id="f301457"></span>More detail about how enumerations can be customized for all block types can be found in [Enumerated blocks](Enumerated blocks).   
<div id="Headings"></div>

### Headings
<span class="para" id="b8e4444"></span>Headings can be defined using `=headN`, where **__N__** is greater than zero (*e.g.* `=head1`, `=head2`, `=head3`, *etc.*). `=head` is an alias for `=head1`.

```
=head A top-level heading

=head2 A second-level heading

=head3 A third-level heading
```

<div id="Numbered headings"></div><div id="Numbered_headings"></div>

#### Numbered headings
<span class="para" id="91af772"></span>You can specify that a heading is numbered using the `=numhead` block (more detail can be found in [Enumerated blocks](Enumerated blocks)). For example:   

```
=numhead1 The Problem

=numhead1 The Solution

    =numhead2 Analysis

        =head3 Overview

        =head3 Details

    =numhead2 Design

=numhead1 The Implementation
```
which would produce:  

```
1. The Problem

2. The Solution

    2.1. Analysis

        Overview

        Details

    2.2. Design

3. The Implementation
```
<span class="para" id="c765975"></span>A document has an *inherent numbering* for every heading, but only the `=numheadN` block makes the numeration visible in the heading and in the Table of Contents.
Note that, even though renderers are not required to distinctly render more than the first four levels of heading, they are required to correctly honour arbitrarily nested numberings. That is:  

```
=numhead6 The Rescue of the Kobayashi Maru
```
should produce something like:  

```
2.3.8.6.1.9. The Rescue of the Kobayashi Maru
```

<div id="Block scope"></div><div id="Block_scope"></div>

### Block scope
<span class="para" id="89bb862"></span>A block scope is important for the `=config` and `=alias` directives. In addition, some renderers may generate secondary pages from primary source files, for example, gathering together the same method names in multiple classes. It is desirable for all the relevant blocks to be gathered together.   
<div id="Sections"></div>

#### Sections
<span class="para" id="9bf05e1"></span>The `section` block will typically only be used in the delimited form so that the document writer can define an explicit block scope. The `=begin section` declaration starts a new block scope, and the `=end section` returns the scope to the previous one. Unlike an `=end rakudoc`, an `=end section` does not always change the scope back to ambient code. It only does so when its corresponding `=begin section` changed the scope *from* ambient code. In contrast, an `=end rakudoc` always reverts to ambient code.   
Sections may be embedded within other sections.  
<span class="para" id="3b77fc4"></span>When a `=begin section` is not present, a renderer may apply the following heuristic to determine the block scope (assuming **__X__**, **__Y__**, and **__Z__** are digits such that `0 < X < Y < Z `):   
&nbsp;&nbsp;• A `=headY` declaration starts a new block scope.  
&nbsp;&nbsp;• The next `=headY` declaration ends the block scope of the previous `=headY`  
&nbsp;&nbsp;• A `=headZ` declaration following a `=headY` is within the block scope of the preceding `=headY` declaration, and does not end it.  
&nbsp;&nbsp;• A `=headX` declaration ends the scope of any preceding `=headY` and `=headZ` declarations.  
<span class="para" id="366c0c9"></span>Note that a `=begin section` declaration overrides the heuristic, meaning that multiple `=headX` declarations can be placed within the same block scope.   
<span class="para" id="737e6b3"></span>A renderer is not expected to render blocks differently when they are embedded in a `section` block.   
<span class="para" id="9096f9d"></span>Providing metadata to a `=begin section` block definition is **not** the same as declaring metadata using a `=config` directive. Metadata in a config is provided to all matching blocks in the current block scope. Metadata in the `=begin section` declaration is only applied to the section itself, not to the blocks it contains.
<span class="para" id="6c07cad"></span>[Further exposition on this topic is available. ](rakudoc:rakudoc_v2_exposition.rakudoc)   

<div id="Document scope"></div><div id="Document_scope"></div>

#### Document scope
<span class="para" id="135a19b"></span>A RakuDoc document starts with the first RakuDoc block or directive supplied by the parser to the renderer from a file, either as an AST or `$=pod` structure, and ends with the last such directive or block. Once the document has ended, the document level options are evaluated and applied to render the document and create its output. Note that a RakuDoc document may be embedded within a Raku module or program using one or more `rakudoc` blocks.   
<span class="para" id="f972249"></span>A `=begin rakudoc` declaration (or equivalently a `=begin pod`) has the effect that the lines following it are consider RakuDoc text or blocks, and are not treated by the parser as Raku code.   
<span class="para" id="12a8120"></span>An `=end rakudoc` (or `=end pod`) will signal a return to the ambient code.   
<span class="para" id="a3de1b3"></span>A `rakudoc` block does **not** start a new block scope and continues the previous document scope.   
<span class="para" id="bb134f3"></span>`rakudoc` blocks may be nested, but do not create additional scopes. For example, if a RakuDoc source containing its own (possibly implied) `=begin rakudoc` statement is included in another RakuDoc source by means of a `=place` statement, then the embedded `rakudoc` block does not create a new block scope. This means that any `=config` statements in an embedded RakuDoc source will apply to the document or block scope in which the source is embedded.   
<span class="para" id="d26d99c"></span>Contrast this to a `section` block, which **does** create a new block scope.   
<span class="para" id="4ca76ac"></span>The metadata that affect an entire document are tabulated [in the syntax section](DocumentOptions). These options may changed at any point in a RakuDoc document with a `=document` directive. The option values are evaluated at the end of the document scope, so the last directives are the most significant.   

<div id="Enumerated blocks"></div><div id="Enumerated_blocks"></div>

### Enumerated blocks
<span class="para" id="d6127fa"></span>RakuDoc allows for the customisable numbering of any block, not just headings and items. Moreover, the enumeration, which is generated automatically by the renderer, can be referred to later in the text using the `A&lt; ... >` markup code. The automatic enumeration is controlled using a construct that is largely independent of blocks: *counters*.   
<span class="para" id="094622c"></span>The *block level* integer at the end of the name of a block type (*e.g.* `head1`, `table2`, `formula3`, *etc.*) is used to characterize numbering hierarchies, so that **subordinate** blocks are typically numbered with reference to their **superordinate** predecessors. For example, an instance of `=numhead2` is *subordinate* to the last instance of `=numhead` (which is equivalent to `=numhead1`).   
<span class="para" id="1787fda"></span>The *block type* may optionally be prefixed by the letters `num`, which signals that the block should be rendered with an enumeration. Every block *has* an enumeration, but only those blocks prefixed with `num` actually include that number in their rendering.   
<span class="para" id="2b4037c"></span>Note that **directives** (such as `=for`, `=begin`, `=end`, `=config`, `=row`, `=column`, `=alias`, *etc.*) are **not** blocks, and so cannot be prefixed by `num`.   
<span class="para" id="374e8df"></span>The (possibly hierarchical) enumeration value for each block is provided by the counter to which the block is currently *subscribed*. By default, each block automatically subscribes to the counter with the same base name as itself. So, for example, enumeration values for `head`, `head1`, `numhead` and `numhead1` blocks are supplied by the single counter named ***`head1`***.   
<span class="para" id="a40d76d"></span>Every block, whether built-in or user-defined, automatically subscribes to a counter, even if the block is never explicitly specified in a document. Counters autovivify (with an initial individual value of `1`) the first time they are referenced, either when a block subscribed to them is used in a document, or when the counter name itself is first used as the argument of some metaoption.   
<span class="para" id="e2503fa"></span>The counter to which a block is subscribed can be changed using the `:counter` metaoption, either applied to an single instance of a block:   

```
=for head1 :counter<SomeOtherCounter>»
This Is A Specially Numbered Heading
```
or configured for all subsequent blocks in the same scope:  

```
=config head1 :counter<SomeOtherCounter>
```
<span class="para" id="7d2dea4"></span>Two or more blocks may subscribe to the same counter, in which case every instance of any of those blocks causes the counter to increment (even instances without the `num` prefix). For example, to have every table and every formula use a single shared enumeration, you could either have the `formula` block subscribe to the ***`table`*** counter (as the `table` block already does by default):   

```
=config formula1  :counter< table1 >
```
...or else have both block types subscribe to the same user-defined counter (which would be autogenerated as soon as it is first used):  

```
=config table1    :counter< TablesAndFormulae >
=config formula1  :counter< TablesAndFormulae >
```
<span class="para" id="ec23d56"></span>Note that the `:counter` option can also be used to create a unique numbering for only part of a document, by taking advantage of the lexical scoping of the `=config` directive. For example, to cause tables and formulae within a single subheading to be numbered separately (without affecting subsequent table/formula numbering later in the document):   

```
=head3 Aside: Detailed derivation of the master equation
=begin section
    =config table    :counter< AsideNumbering >
    =config formula  :counter< AsideNumbering >
    ...
=end section

=head3 Table/formula numbering continues normally from here
```

<div id="Enumeration aliases for blocks"></div><div id="Enumeration_aliases_for_blocks"></div>

#### Enumeration aliases for blocks
<span class="para" id="be0f004"></span>It is also possible to capture the specific number with which a given block was rendered, by specifying that block with the `:numalias<TAG>` metaoption. If this option is applied to a specific block then, when the block is rendered, a new alias is automatically created. If that alias is later referred to (using the `A&lt;TAG>` notation), the enumeration value for the originating numbered block is substituted into the rendered document at that point. For example:   

```
=for numtable :caption<Complete list of ingredients>  :numalias<IngredientsTable>

=comment  And later...

First gather all the ingredients listed in A<IngredientsTable>.
```
This might be rendered something like...  

```
Table 7. Complete list of ingredients

# And later...

=para  First gather all the ingredients listed in Table 7.
```
<span class="para" id="3d7e6d4"></span>By default, the format used for the substituted enumeration text will be `%T %N` (using the enumeration format string [described elsewhere](Formatting of blocks)). However, the author may specify a different rendering for the alias, using an extended variant of the option: `:numalias< FORMAT | TAG >`, where `FORMAT` is a desired alternative format string. For example:   

```
=for numitem  :numalias< Article %N | HQ >  :form<Article %N. %D>
Headquarters location.

=comment Text of document with many other numitems

=para  The deadline is noon at the location of the main office, see A<HQ>.
```
This would be rendered as something like  

```
Article 10. Headquarters location

# Lots of text

The deadline is noon at the location of the main office, see Article 10.
```
<span class="para" id="77917cb"></span>Since all block options may be preconfigured using the `=config` directive, it is also possible to create a parameterized `:numalias` as follows:   

```
=config numitem  :numalias< Article %N. | * >  :form<Article %N. %D>
```
<span class="para" id="ceb31e3"></span>In this case, the `*` is replaced by the tag specified in each subsequent `:numalias` option applied to individual `numitem` blocks. For example, with the previous `=config` the following uses of `:numalias`:   

```
=for numitem  :numalias< HQ  >
=for numitem  :numalias< CCN >
=for numitem  :numalias< WTF >
```
...are equivalent to:  

```
=for numitem :numalias< Article %N. | HQ  >
=for numitem :numalias< Article %N. | CCN >
=for numitem :numalias< Article %N. | WTF >
```
<span class="para" id="49a489e"></span>The precedence rule for options is that options specified directly on a block take priority over preconfigured options specified by a `=config` directive. Consequently, a `:numalias` with an explicit format will replace any preconfigured `:numalias` supplied by a preceding `=config` directive. For example:   

```
=config numitem  :numalias< Article %N. | * >  :form<Article %N. %D>

=for numitem  :numalias< HQ  >
=for numitem  :numalias< CCN >
=for numitem  :numalias< The WTF Exception (%N) | WTF >
```
...are equivalent to:  

```
=for numitem :numalias< Article %N. | HQ  >
=for numitem :numalias< Article %N. | CCN >
=for numitem :numalias< The WTF Exception (%N) | WTF >
```
<span class="para" id="fd040ce"></span>Moreover, if the explicit format is empty (*i.e.* `:numalias<|TAG>`) then the default format (*i.e.* `%T %N`) will be reinstated for that one particular block. Hence:   

```
=for numitem  :numalias< | WTF >
```
...is equivalent to:  

```
=for numitem :numalias< %T %N | WTF >
```

<div id="Counts and counters"></div><div id="Counts_and_counters"></div>

### Counts and counters
The counters to which blocks can subscribe are independent constructs that are created and maintained for each RakuDoc document by every compliant renderer. Counters are entirely separate from the blocks they service, though the two constructs do interact in several ways that will be described hereafter.  
<span class="para" id="628e5cf"></span>Counters have a range of features useful for generating enumerations: an *individual value*, a *hierarchical prefix*, a *restart status*, and various possible *restart triggers*.   
<span class="para" id="342853d"></span>All these features have sensible defaults whenever a counter is auto-created. However, all of them may also be explicitly reconfigured within a given lexical scope using the `=counter` directive.   

<div id="Each counter has an ***individual value***"></div><div id="Each_counter_has_an_individual_value"></div>

#### Each counter has an ***individual value***
<span class="para" id="66cedd9"></span>A counter’s *individual value* (a.k.a. its *count*) is a global scalar integer variable that is external to the counter itself, and which tracks the counter’s current enumeration value. The individual value of every counter starts (and restarts) at 1, and is incremented every time some block requests the next value from the counter.   
<span class="para" id="a160f99"></span>Because a counter’s individual value is not itself lexically scoped, it does not revert to its earlier value at the end of a `section`. It is the single value that a counter manages, not a lexical property of the counter itself. Diagrammatically:   

```
    Block  (has lexically scoped properties: counter, form, allow, etc.)
      ┆
[subscribes to]
      ┆
      V
   Counter (has lexically scoped properties: prefix, restart status, restart trigger)
      ┆
  [manages]
      ┆
      V
    Count  (a single global integer variable)
```

<div id="Each counter has a ***hierarchical prefix***"></div><div id="Each_counter_has_a_hierarchical_prefix"></div>

#### Each counter has a ***hierarchical prefix***
<span class="para" id="0d81f79"></span>A counter’s *hierarchical prefix* is the name of some other counter which recursively supplies it with a hierarchical enumeration, such as is required by subordinate blocks such as `head2`, `table3`, *etc.*   
<span class="para" id="26f84f5"></span>Therefore, when a counter is asked to supply its current or next value to a block being rendered, the value returned is a list of integers consisting of the original counter’s own *individual value*, preceded by the current value of some other counter whose name is specified by the original counter’s hierarchical prefix.   
<span class="para" id="d0e9a7c"></span>By default, a given counter’s hierarchical prefix is its immediate *superordinate counter*. That is, the counter ***table3*** has a default prefix of ***table2***, and the ***table2*** counter’s default prefix is ***table1***. Top-level counters, such as ***table1*** have no default hierarchical prefix.   
<span class="para" id="91bde2d"></span>So, for example, the complete hierarchical enumeration supplied by the ***table3*** counter is: `(t1, t2, t3)`, where `tN` is the current individual value of the ***tableN*** counter. Mathematically speaking:   

```
hier-enum( CounterN )  →  hier-enum( Counter(N-1) ), indiv-count( CounterN )
hier-enum( Counter1 )  →                             indiv-count( Counter1 )
```
<span class="para" id="f790561"></span>You can change the hierarchical prefix value for a given counter using the `=counter` directive. For example, you might prefer that all top-level tables (which normally have no hierarchical prefix) should be prefixed by the current value of the ***head1*** counter, to produce output like:   

```
1. Concept
    Table 1.1. List of requirements
    Table 1.2. List of resources
    Table 1.3. List of constraints

2. Implementation
    Table 2.4. Implementation schedule
    Table 2.5. Resource suppliers
    Table 2.6. Costings vs actual expenditure
```
<span class="para" id="d51e572"></span>To ensure that every numbered table uses the current ***head1*** value as a prefix in this way, you would configure the ***table*** counter as follows:   

```
=counter table1 :prefix<head1>
```
<span class="para" id="d5b35c5"></span>(To have the individual counters of the tables automatically restart after each heading, see the `:restart-after` metaoption, which is described later.)   
<span class="para" id="1cb94bc"></span>Note that the `=counter` directive is lexically scoped (*i.e.* it is effectively a `=config` for counters instead of blocks), so the preceding directive would need to be placed at the start of the document.   
<span class="para" id="abf25a1"></span>You can also explicitly *remove* any prefix from a counter, by specifying an “empty” prefix for it. For example, to change the usual `t1.t2.t3.` enumeration of the ***table3*** counter to just `t3.`, you could specify:   

```
=counter table3 :prefix<>
```
<span class="para" id="dc33938"></span>Note that this directive would also indirectly affect the ***table4***, ***table5***, *etc.* counters, by “cutting off” their default hierarchical prefixes at ***table3***. This is considered a feature, as it ensures consistency of representation across counter hierarchies.   

<div id="Each counter has a ***restart status***"></div><div id="Each_counter_has_a_restart_status"></div>

#### Each counter has a ***restart status***
<span class="para" id="98e2104"></span>A counter normally continues to increment its individual value every time any subscribing block requests the “next” value from the counter. However, this monotonic progression can be changed by altering the counter’s *restart status*.   
<span class="para" id="0a57210"></span>Normally, a counter’s restart status has the value `Nil`, indicating no special restart behaviour. In that case the counter defaults to simply incrementing its individual value to obtain its next value.   
<span class="para" id="9fd8d02"></span>But you can configure a counter’s restart status to ensure that its next value will be something other than a simple increment, using the `=counter` directive’s `:restart` option.   
<span class="para" id="8038ae3"></span>If the value passed in the `:restart` option is true: `=counter head1 :restart` then the counter’s restart status immediately becomes true and the counter will no longer increment its individual value when its next value is requested (*i.e.* when its next subscribing block is rendered). Instead the counter will restart its global count from `1` and return that as its next value.   
<span class="para" id="2e2c5a3"></span>If the value passed in the `:restart` option is false: `=counter item1 :!restart` then the counter’s restart status immediately becomes false and the counter will thereafter increment its individual value as usual when its next value is requested, even if an automatic *restart trigger* is subsequently encountered (see the following section).   
<span class="para" id="ba298e3"></span>If the value passed in the `:restart` option is an integer, such as: `=counter head1 :restart(42)` then the counter’s restart status becomes that integer and the counter will thereafter restart its individual value at the specified integer when a next value is requested, even if an automatic *restart trigger* is subsequently encountered.   
<span class="para" id="5d18775"></span>Once a counter has used its restart status to determine what its next enumeration value should be, that restart status is always reset to `Nil`. In other words, the `:restart` option tells the counter how to calculate its next (and *only* its next) value.   

<div id="Each counter has an ***automatic restart trigger***"></div><div id="Each_counter_has_an_automatic_restart_trigger"></div>

#### Each counter has an ***automatic restart trigger***
<span class="para" id="6883290"></span>By default, most counters are flagged for subsequent automatic restarting (from `1`) whenever a block with the same base name but some superordinate level is encountered in the document. However, there is also a mechanism that permits other non-standard *automatic restart triggers* to be specified on a given counter.   
<span class="para" id="2c2e62d"></span>Each custom trigger consists of a set of one or more basenames of specific ***trigger blocks*** that, when they are rendered, will automatically set the *restart status* of a counter to true, so that the counter will automatically restart whenever its next value is subsequently requested.   
<span class="para" id="ff2a071"></span>To specify that a particular counter should be automatically restarted after one or more blocks are rendered, configure that counter using the `:restart-after` metaoption, passing the names of the restart-triggering blocks as the metaoption’s argument. For example, to have the numbering of every table and formula restart after each `head1`, `head2`, or `head3` block:   

```
=counter table    :restart-after< head1 head2 head3 >
=counter formula  :restart-after< head1 head2 head3 >
```
<span class="para" id="070d61a"></span>Or to have the numbering of items restart only after each `input` block (*i.e.* instead of after *any* non-`item` block):   

```
=counter item1   :restart-after< input >
=counter item2   :restart-after< input >
=counter item3   :restart-after< input >
=counter item4   :restart-after< input >
```
<span class="para" id="919126a"></span>Note that, as the first example above indicates, having to specify *all* the “superordinate” trigger blocks for each automatic counter restart is rather tedious, so we define that specifying a particular trigger blockname in a `:restart-after< blocknameN >` implies *“...restart after `blocknameN` or any ***superordinate*** block of the same base name.”*
In other words, the previous table-and-formula example (though still perfectly valid) could be simplified to:  

```
=counter table    :restart-after< head3 >
=counter formula  :restart-after< head3 >
```
<span class="para" id="06c0356"></span>which means *“Automatically restart the counter after any `head3` or superordinate `head` block”*.   
<span class="para" id="9a5abec"></span>It is also possible to specify automatic restart behaviours “negatively”. That is, to specify that a particular counter restarts after ***any*** block, except the one or more that are explicitly excluded from the trigger. For example, having previously changed the reset behaviours of the counters ***`item1`*** through ***`item4`***, we could reinstate their default *“restart after any non-`item`”* behaviours, like so:   

```
=counter item1  :restart-except-after< item1 item2 item3 item4 >
=counter item2  :restart-except-after<       item2 item3 item4 >
=counter item3  :restart-except-after<             item3 item4 >
=counter item4  :restart-except-after<                   item4 >
```
Or we coulld construct special counters for a set of user-defined item-like blocks, as follows:  

```
=counter MyItem        :restart-except-after< MyItem MySubItem MySubSubItem >
=counter MySubItem     :restart-except-after<        MySubItem MySubSubItem >
=counter MySubSubItem  :restart-except-after<                  MySubSubItem >
```
<span class="para" id="fa95cc6"></span>In other words, the presence of an *excluded blockname* (such as `BlockName`) in a `:restart-except-after` implies that the counter being configured is to be automatically restarted after every block ***except*** `BlockName`. Multiple excluded blocknames can be specified in the same `:restart-except-after`, but specifying both a `:restart-after` and a `:restart-except-after` option on the same counter is redundant, because the `:restart-except-after` already implies *“restart after everything else”*, so any blocks explicitly listed in the `:restart-after` are already covered by that *“...everything else”*.
<span class="para" id="5141aa8"></span>Therefore, where both `:restart-after` and `:restart-except-after` options are specified in a single `=counter` directive, the `:restart-except-after` trigger is installed and the `:restart-after` trigger is ignored (preferably with the renderer issuing a warning).   
<span class="para" id="fac7328"></span>Note that having to specify hierarchical subordinate excluded subitems all the way down is also tedious, so we define that any excluded blockname in a `:restart-except-after` implies *“...restart after any block except this block or any ***subordinate*** block of the same base name.”*   
Hence the previous example of reinstating normal item restarting behaviour could also be simplified to:  

```
=counter item1  :restart-except-after< item1 >
=counter item2  :restart-except-after< item2 >
=counter item3  :restart-except-after< item3 >
=counter item4  :restart-except-after< item4 >
```
<span class="para" id="eb13db0"></span>Every counter’s automatic restart trigger is rechecked immediately after each block in a document is rendered, and if a match is found then the counter’s restart status is changed from `Nil` to `True`, which means that the counter will be restarted next time it is asked for a value. However, if the counter’s restart status is already non-`Nil` (because it has already had an explicit `:restart` option applied to it) then that pre-existing non-`Nil` restart status is left unchanged by the trigger.   
<span class="para" id="7ccd720"></span>The effect of this is that any explicit `:restart` always overrides the effect of any automatic trigger, regardless of whether the `:restart` precedes or follows the triggering.   
<span class="para" id="a519c6b"></span>[Further exposition on this topic is available. ](rakudoc_v2_exposition.md)   

<div id="Formatting of blocks"></div><div id="Formatting_of_blocks"></div>

#### Formatting of blocks
<span class="para" id="b1d763a"></span>The `:form` metaoption allows the author to modify the way in which enumerated blocks are rendered. It can be used to alter the position where the enumeration value appears, and to add extra text around the various components of a block.   
<span class="para" id="456ec43"></span>All para-ish blocks have a default format of `:form<%N. %D>` (*e.g.* ***1. Block data here***). All head-ish blocks have a default format of `:form<%T %A. %C>` (*e.g.* ***Table 1. Caption here***), except for `=numhead`, which has a default of `:form<%N. %D>` (*e.g.* ***1. Heading here***).   
<span class="para" id="82db5a1"></span>The value associated with a `:form` option is similar in concept to a `sprintf` or `strftime` format string. That is, it specifies a template into which various values related to the block are inserted, to produce a final rendering of the block. The `:form` option differs from `sprintf` and `strftime` in that it uses a more verbose (but far more readable) specification syntax.   
<span class="para" id="3d468e8"></span>Placeholders for components to be formatted into the format template are known as *“fields”*, and are specified by a `%` followed by a single letter character (*i.e.* a single codepoint that matches `/<:Letter>/`. This may also be followed (immediately and without any intervening whitespace) by one or more options in the usual `:OPTION` or `:OPTION&lt;VALUE>` format. Any other character sequences between fields are rendered verbatim.   
<span class="para" id="eac86a5"></span>Renderers are not required to implement the `:form` metaoption, but if they do then a conformant renderer must support at least the following field types:   

<div id="Field_types"></div>
<div>Field types</div> | Field | Insert next enumeration value as... |
| :---: | :---: |
 | `%N` | the corresponding Indo-Arabic numerals: *1, 2, 3, *etc.** |
 | `%A` | the corresponding ASCII capital alpha: *A, B, C ... Z, AA, AB, AC, *etc.** |
 | `%T` | the name of the block type in Titlecase without the leading `num` or trailing level number: *Head, Item, Table, Formula, Defn, *etc.** |
 | `%C` | the caption of the block (if any) |
 | `%D` | the data contained in the block (*i.e.* its contents) |
 | `%%` | a literal `%` |
 | `%:` | a literal `:` |
So, for example, the following enumerated elements:  

```
=numhead1 PHYSICS
=numhead2 Newtonian
=numhead2 Relativistic
=begin numformula :caption<Mass-Energy Equivalence>
E = MC²
=end numformula
```
...would normally be rendered something like:  

```
1. PHYSICS

1.1. Newtonian

1.2. Relativistic

            E = MC²

Formula 1. Mass-Energy Equivalence
```
But the following pre-configurations:  

```
=config numhead1   :form<TOPIC %A - %D>
=config numhead2   :form<%D [subtopic %A%N]>
=config numformula :form<Equation %N: %C>
```
...would cause the document to be rendered differently:  

```
TOPIC A - PHYSICS

Newtonian [subtopic A1]

Relativistic [subtopic A2]

            E = MC²

Equation 1: Mass-Energy Equivalence
```

<div id="Formatting multi-level enumerations"></div><div id="Formatting_multi-level_enumerations"></div>

##### Formatting multi-level enumerations
<span class="para" id="7ddd1e9"></span>Notice that in the `=config numhead2` example in the previous section the `:form` option specifies ***two*** enumeration fields: `%A` and `%N`. This is because enumerated headings are hierarchical in their numbering. For example, the sequence of numbered headings:   

```
=numhead1 The Plan
=numhead2 Plan design criteria
=numhead2 Plan design constraints
=numhead3 Time
=numhead3 Space
=numhead3 Energy
=numhead4 Available energy
=numhead4 Energy costs
```
...would be rendered something like:  

```
1. The Plan
    1.1. Plan design criteria
    1.2. Plan design constraints
        1.2.1. Time
        1.2.2. Space
        1.2.3. Energy
            1.2.3.1. Available energy
            1.2.3.2. Energy costs
```
<span class="para" id="1b3dbf7"></span>So a `:form` for a second-level heading:   

```
=config numhead2 :form<%D [subtopic %A%N]>
```
...requires two fields, because it expects to interpolate two enumeration values: the enumeration value of the current first-level heading, plus its own second-level enumeration value.  
<span class="para" id="45830cc"></span>In that example, the first field (`%A`) would be replaced by the current `numhead1` enumeration value, and the second field (`%N`) would be replaced by the current `numhead2` value.   
<span class="para" id="a033428"></span>In general, a `:form` applied to a `numhead`*X* block may specify up to *X* enumeration fields, *X-1* of which would be replaced (left-to-right) by the *X-1* current enumeration values of the *X-1* superordinate headings. The *X*-th field would, of course, be replaced by the block’s own enumeration value. For example:   

```
=config numhead4 :form<%N(%A) %N.%N: %D>
```
<span class="para" id="567c460"></span>...would be formatted by replacing the first `%N` with the current `numhead1` enumeration value, then the single `%A` with the current `numhead2` enumeration value, then the second `%N` with the current `numhead3` enumeration value, then the third `%N` with the current `numhead4` enumeration value.   
<span class="para" id="1320832"></span>If any of the superordinate headings is not enumerated (*e.g.* `head3` instead of `numhead3`), then the corresponding enumeration field would be replaced by an empty string instead of a number.   
<span class="para" id="515f627"></span>If a `:form` option specifies ***fewer*** enumeration fields than the corresponding heading level:   

```
=config numhead4 :form<%N.%N: %D>
```
<span class="para" id="e070a55"></span>...then only the final *X* most-subordinate enumeration values are inserted into the corresponding format. That is, the previous example would be rendered:   

```
1. The Plan
    1.1. Plan design criteria
    1.2. Plan design constraints
        1.2.1. Time
        1.2.2. Space
        1.2.3. Energy
            3.1. Available energy
            3.2. Energy costs
```
<span class="para" id="8d383b4"></span>That is, because the `:form` for `numhead4` specifies only two enumeration fields, only the two most-subordinate enumeration values (those for `numhead3` and `numhead4`) are interpolated.   
<span class="para" id="7b66bc0"></span>The same rules and behaviour apply to `numitem1`/`numitem2`/`numitem3` blocks and to every other block that may be given a hierarchical enumeration.   

<div id="Formatting options"></div><div id="Formatting_options"></div>

#### Formatting options

<div id="Field ordinality"></div><div id="Field_ordinality"></div>

##### Field ordinality
<span class="para" id="b0d69ac"></span>The `%N` field normally interpolates the current enumeration value as a cardinal number (*1, 2, 3, *etc.**), but the `:ord` option requests the field to interpolate an ordinal number instead (*1st, 2nd, 3rd, *etc.*th*). For example:   

```
=config numitem1  :form<%N:ord Activity: %D>
=config numitem2  :form<%N:ord step: %D>

=numitem1 Deploy
=numitem2   Plan
=numitem2   Prepare
=numitem2   Execute
=numitem1 Review
=numitem2   Observe
=numitem2   Discuss
```
...would be rendered:  

```
1st Activity: Deploy
    1st step: Plan
    2nd step: Prepare
    3rd step: Execute

2nd Activity: Review
    1st step: Observe
    2nd step: Discuss
```
<span class="para" id="7b3c159"></span>To change the underlying language of the ordinal system (*e.g.* 1er, 2e, 3e *etc.* or 第一, 第二, 第三, *etc.*) see [the `:lang` option](Field_language).   

<div id="Field case"></div><div id="Field_case"></div>

##### Field case
<span class="para" id="b50fb29"></span>Some enumeration formats (*e.g.* alphabetic characters or Roman numerals) have uppercase and lowercase variants or close analogies thereto (*e.g.* “Business” vs “ordinary” notation in Chinese and Japanese numbers). Other fields may benefit from other casing strategies (*e.g.* Sentencecase, or ProperCase).   
To select which case-variant a field uses, append one of the following options:  

<div id="Form_field_case_options"></div>
<div>Form field case options</div> | Option | How the contents of the field are rendered |
| :---: | :---: |
 | `:uc` | EVERYTHING IN ***U****PPER****C****ASE* OR AS “FORMAL/BUSINESS” NUMERALS |
 | `:lc` | everything in ***l****ower****c****ase* or as “ordinary/everyday” numerals |
 | `:tc` | First character in ***t****itle****c****ase* but NO other change of case |
 | `:tclc` | First character in ***t****itle****c****ase*, everything else in ***l****ower****c****ase* |
 | `:sc` | ***S****entence****c****ase*: First character of each sentence in Titlecase, ABBREVS entirely in uppercase, everything else in lowercase |
 | `:pc` | ***P****roper****c***ase: First Character Of Every Word In Titlecase, ABBREVS Entirely In Uppercase, Everything Else In Lowercase |
<span class="para" id="99caac7"></span>For example, to specify that particular case(s) should be used for `%A` alphabetic field:   

```
=config numitem1  :form<%A:uc. %D>
=config numitem2  :form<%A:uc(%A:lc) %D>

=numitem1 Task sequence
=numitem2   Plan
=numitem2   Prepare
=numitem2   Execute
```
...would be rendered:  

```
A. Task sequence
    A(a) Plan
    A(b) Prepare
    A(c) Execute
```
These options also specify how ordinal numbers are rendered:  

```
=config numitem1  :form<%N:ord:uc ACTIVITY: %D>
=config numitem2  :form<%N:ord:lc step: %D>

=numitem1 DEPLOY
=numitem2   Plan
=numitem2   Prepare
=numitem2   Execute
=numitem1 REVIEW
=numitem2   Observe
=numitem2   Discuss
```
...would be rendered:  

```
1ST ACTIVITY: DEPLOY
    1st step: Plan
    2nd step: Prepare
    3rd step: Execute

2ND ACTIVITY: REVIEW
    1st step: Observe
    2nd step: Discuss
```
<span class="para" id="5d7536a"></span>As a convenient shortcut for common cases, every defined field of the form `%X` must also provide a corresponding field of the form `%x`, which is exactly equivalent to `%X:lc`.   
<span class="para" id="7cbad03"></span>Note, however, that the uppercase version (`%X`) is ***not*** automatically equivalent to `%X:uc`. That is, a renderer is free to specify, for each field type, whether the default rendering is uppercase or lowercase (if that is even relevant to the field). To override the renderer’s default casing and insist on an uppercase/formal rendering, the user must explicitly specify `%X:uc`
<span class="para" id="bb77f87"></span>Note that the `:uc`, `:lc`, `:tc`, and `:tclc` options all behave exactly like their Raku counterparts, whereas `:sc` and `:pc` will attempt to behave in a somewhat more “literary” fashion. Specifically, these last two both understand that words originally in ALLCAPS usually need to stay capitalized. If you think that you need `:tclc` on a caption or data field, you probably want `:sc` instead, because it understands slightly more about natural language formatting. For example, the caption:   

```
=for numtable :caption<list of allowed ASCII characters. Namely, those i like.> :form<%N. %C:tclc>
```
...would be rendered:  

```
Table 1. List of allowed ascii characters. Namely, those i like.
```
...whereas:  

```
=for numtable :caption<list of allowed ASCII characters. Namely, those i like.> :form<%N. %C:sc>
```
...would be rendered:  

```
Table 2. List of allowed ASCII characters. Namely, those I like.
```
<span class="para" id="f8eedd4"></span>Note too that it is an error to specify two or more of `:lc`, `:uc`, `:tc`, `:tclc`, `:sc`, or `:pc` on the same field, either explicitly (*e.g.* `%N:uc:lc`) or implicitly (*e.g.* `%n:tc`). In such cases, the case of the field specifier itself determines which case is actually used (*e.g.* `%N:uc:lc` means `%N:uc`; `%n:tc` means `%n:lc`). Renderers should issue a warning when they encounter these kinds of self-contradictory field specifications.   
<span class="para" id="fcee67a"></span>Specific casing may also be applied to non-enumerator fields such as `%T`, `%C`, or `%D`. For example:   

```
=config numhead1   :form<TOPIC %A - %D:uc>
=config numformula :form<[%T:lc %N: %C:sc]>

=numhead1 Physics

=begin numformula :caption<Mass-Energy Equivalence>
E = mc²
=end numformula
```
This would override the intrinsic mixed-casing of the block data and captions to produce something like:  

```
TOPIC A - PHYSICS

            E = mc²

[formula 1: Mass-energy equivalence]
```
In order to avoid undue complexity and ambiguity, explicit embedded RakuDoc components in the data or caption associated with a block will disable any case handling.  
For example:  

```
=config numformula :form<[%T:lc %N: %C:sc]>

=begin numformula :caption<B<Mass-Energy> Equivalence>
E = mc²
=end numformula
```
This would override the sentence-casing of the caption (only) to produce something like:  

```
            E = mc²
[formula 1: Mass-Energy Equivalence]
```

<div id="Field language"></div><div id="Field_language"></div>

##### Field language
<span class="para" id="eb80723"></span>The `:lang` option specifies that a field should use the conventions of a specific language when inserting a numeric or ordinal value. The language to be used is specified using the language’s [ISO 639-1 two-character language code](https://en.wikipedia.org/wiki/ISO_639-1).   
<span class="para" id="4b405e4"></span>For example to specify that, instead of using regular Indo-Arabic numerals, the items of a first-level enumerated list should use Roman (*i.e.* ***La****tin*) numerals:   

```
=config numitem1 :form<%N:lang<la>. %D>
```
<span class="para" id="ee9b308"></span>...or that second-level list items should use French ordinals (*1er*, *2e*, *3e*, *etc.*):   

```
=config numitem2 :form<N:ord:lang<fr>. %D>
```
<span class="para" id="38e0d9b"></span>Renderers are encouraged to support at least the following languages: English (`:lang<en>`, which is the default and represents the [Indo-Arabic numeral system](https://en.wikipedia.org/wiki/Hindu%E2%80%93Arabic_numeral_system)) Roman numerals (`:lang<la>`), Mandarin Chinese (`:lang<zh>`), Hindi (`:lang<hi>`), Bengali (`:lang<bn>`), and Japanese (`:lang<ja>`). Collectively these six languages cover the primary number systems used by a majority the world’s population.   
<span class="para" id="72fa139"></span>Note, however, that renderers are not **required** to support any languages other than the default `:lang<en>`. If an unsupported language is requested, renderers may always fall back on `:lang<en>`.   
<span class="para" id="41cd188"></span>If a renderer supports Roman numerals (*I, II, III, IV, *etc.**) via `%N:lang<la>`, then it must also provide the equivalent field `%R`. If it also supports lowercase Roman numerals via `%N:lang<la>:lc` (*i, ii, iii, iv, *etc.**), then it must also provide the equivalent field `%r`.   
<span class="para" id="fdafdc5"></span>Likewise, if a renderer supports Mandarin Chinese numbers via `%N:lang<zh>` (which should generate formal/business 大寫 numerals), then it must also provide the equivalent fields `%Z` and `%大`. If it also supports `%N:lang<zh>:lc` (which should produce ordinary 小寫 numerals), then it must also provide the equivalent fields `%z` and `%小`.   
<span class="para" id="97ba240"></span>Generated Chinese numbers should probably use traditional glyphs, rather than simplified glyphs, for maximum comprehensibility outside mainland China *(though the expert opinions of native speakers regarding this choice should certainly be sought where possible)*.   
<span class="para" id="1d65a82"></span>If a renderer supports Japanese numerals, via `%N:lang<ja>`, then it must also provide the equivalent fields `%J` and `%業` (formal/business numbers) as well as `%j` and `%並` (everyday numbers). Note that `%業` and `%並` are still tentative, and perhaps not the most obvious choices. Unfortunately the most obvious choices – `%大` and `%小` – are already taken by `%Z` and `%z` (as Chinese arguably had the prior claim to them). Feedback on these names and suggestions for improvements – especially from native speakers of Japanese – would be greatly appreciated.   
<span class="para" id="03dfff8"></span>If a renderer supports Hindi numerals, via `%N:lang<hi>`, then it must also provide the equivalent fields `%H` and `%ह`. If a renderer supports Bengali numerals, via `%N:lang<bn>`, then it must also provide the equivalent fields `%B` and `%ব`.   
<span class="para" id="a43ef02"></span>Only these six languages (English, Mandarin Chinese, Hindi, Bengali, Japanese, and Latin) will ever have dedicated single-character field specifiers. If a renderer supports the number systems of any other languages (*e.g.* `%N:lang<fr>`, `%N:lang<th>`, or `%N:lang<eo>`), the renderer is ***not*** permitted to reserve any `%X` field for them.   
<span class="para" id="6098bea"></span>Note that a renderer is permitted to support `%N:lang<xx>`, without supporting the corresponding `%N:lang<xx>:ord`, or vice versa (though implementers are strongly encouraged to implement both cardinal and ordinal modifiers – where relevant – for any language they support).

<div id="Formatting aliases"></div><div id="Formatting_aliases"></div>

#### Formatting aliases
<span class="para" id="dd4a6dc"></span>A `numBlockname` may have an associated `:numalias< TAG >` option. Later, in accordance with the rules described above for the [Alias directive ](), the enumeration can substituted into text using `A&lt;TAG>`.   
<span class="para" id="955164f"></span>By default, the format used for the substituted enumeration text will be ` %T %N `, using the enumeration format string described above.   
<span class="para" id="24566be"></span>The author may specify the rendering of the alias using the option `:numalias< FORMAT | TAG >`, where **__FORMAT__** is a format string. For example:   

```
=for numitem :numalias<Article %N | HQ > :form<Article %N. %D>
Headquarters location.
=comment Text of document with many numitems
=para The deadline is noon at the location of the main office, see A&lt;HQ>.
```
This would be rendered as something like  

```
Article 10. Headquarters location
# Lots of text
The deadline is noon at the location of the main office, see Article 10.
```
<span class="para" id="1e98cf1"></span>Since all options may be set using the `=config` directive, it is possible to set *:numalias* as follows:   

```
=config numitem :numalias< Article %N. | * > :form<Article %N. %D>
```
<span class="para" id="958c795"></span>In this case, the `*` is replaced by the actual **__TAG__** specified in the `:numalias` option for the block. For example:   

```
=for numitem :numalias<HQ>
```
<span class="para" id="c56f7c1"></span>The precedence rule for options is that options specified by a block take priority over options specified by a `=config` directive.   
<span class="para" id="97a571a"></span>Consequently, `:numalias<|TAG>` will override any declaration given by a `=config` directive, and the default value of `%T %N.` will be used to render any `A&lt;TAG>` reference.

<div id="Ordinary paragraphs"></div><div id="Ordinary_paragraphs"></div>

### Ordinary paragraphs
An ordinary paragraph consists of text that is to be formatted into a document at the current level of nesting, with whitespace squeezed, lines filled, and any special inline markup applied.  
Ordinary paragraphs consist of one or more consecutive lines of text, each of which starts with a non-whitespace character. The paragraph is terminated by the first blank line or directive.  
For example:  

```
=head This is a heading block

This is an ordinary paragraph.
Its text  will   be     squeezed     and
short lines filled. It is terminated by
the first blank line.

This is another ordinary paragraph.
Its     text    will  also be squeezed and
short lines filled. It is terminated by
the trailing directive on the next line.

    =head2 This is another heading block

    This is yet another ordinary paragraph,
    at the first virtual column set by the
    previous directive
```
<span class="para" id="a6afb99"></span>Ordinary paragraphs do not require an explicit block specifier or delimiters (within a `rakudoc` block).   
<span class="para" id="6efd6dd"></span>Alternatively, there is also an explicit `=para` block specifier that can be used to explicitly mark a paragraph, which would be necessary outside a `rakudoc` block.   

```
=para
This is an ordinary paragraph.
Its text  will   be     squeezed     and
short lines filled.
```
This is rendered as:  
> This is an ordinary paragraph. Its text will be squeezed and short lines filled.

<span class="para" id="cc1497f"></span>In addition, the longer `=begin para` and `=end para` form can be used. For example:   

```
=begin para
This is an ordinary paragraph.
Its text  will   be     squeezed     and
short lines filled.

This is still part of the same paragraph,
which continues until an...
=end para
```
<span class="para" id="88b7be4"></span>As demonstrated by the previous example, within a delimited `=begin para` and `=end para` block, any blank lines are preserved.   

<div id="Nesting or indenting a block"></div><div id="Nesting_or_indenting_a_block"></div>

### Nesting or indenting a block
<span class="para" id="fd4e43e"></span>RakuDoc provides a `=nested` block that marks all its contents as being nested:   

```
=begin nested
S<We are all of us in the gutter,
but some of us are looking at the stars!>
    =begin nested
    – Oscar Wilde
    =end nested
=end nested
```
...which would produce:  
> <span class="para" id="27f68e9"></span>We are all of us in the gutter,<br>but some of us are looking at the stars!   
> – Oscar Wilde

<span class="para" id="7b997a1"></span>A `nested` block is like a `section` block in that it defines a scope and can contain any other kind of block, including implicit paragraph and code blocks. Note that the relative physical indentation of the `nested` blocks in the document plays no role in determining the ultimate indentation of their contents in the final rendering.   

<div id="Verbatim blocks"></div><div id="Verbatim_blocks"></div>

### Verbatim blocks
<span class="para" id="515f112"></span>Normally a writer does not want to consider the way text flows on a page. They want the end of each line to be treated the same as a space, and for extra spaces to be eliminated. However, the exact placing of whitespace sometimes *is* important, especially for code.   

<div id="Code blocks"></div><div id="Code_blocks"></div>

#### Code blocks
<span class="para" id="65d44ac"></span>Code blocks are used to specify pre-formatted text (typically source code), which should be rendered without rejustification, without whitespace squeezing, and by default **without** recognizing any inline markup instructions. Code blocks also have an implicit nesting associated with them. Typically these blocks are used to show examples of code, markup, data formats, or other textual specifications. They are usually rendered using a fixed-width font.   
A code block may be implicitly specified as one or more lines of text, each of which starts with a whitespace character at the block’s virtual left margin. The implicit code block is then terminated by a blank line or an explicit directive. For example:  

```
This ordinary paragraph introduces a code block:

    $this = 1 * code('block');
    $which.is_specified(:by<indenting>);
```
<span class="para" id="643ab20"></span>Implicit code blocks may be used [elsewhere with some caveats](Implied code and indentation).   
<span class="para" id="a254526"></span>There is also an explicit `=code` block (which can be specified within most other block types, not just `=rakudoc`, `=item`, *etc.*):   

```
The C&lt;loud_update()> subroutine adds feedback:

=begin code

sub loud_update ($who, $status) {
    say "$who -> $status";

    silent_update($who, $status);
}

=end code
```
<span class="para" id="3ece161"></span>As the previous example demonstrates, within an explicit `=code` block the code does not have to be indented; it can start at the (virtual) left margin. Furthermore, lines that start with whitespace characters after that margin have subsequent whitespace preserved exactly (in addition to the implicit nesting of the code). Explicit `=code` blocks may also contain empty lines.   

<div id="Preprocessing and postprocessing of code"></div><div id="Preprocessing_and_postprocessing_of_code"></div>

###### Preprocessing and postprocessing of code
The code in a document is almost always related to a specific programming language, by default Raku. But examples of Ruby, C, RakuDoc, or other languages may also appear in the Raku documentation sources.  
A renderer may apply language specific syntax highlighting to the contents of a code block according to the following rules:  
&nbsp;&nbsp;• `=code` blocks with no explicit or preconfigured `:lang` option default to `:lang<raku>`.  
&nbsp;&nbsp;• `=code` blocks can be marked as not being in any specific language by using `:!lang`.  
&nbsp;&nbsp;• Renderers must not syntax-highlight any code block whose `:lang` value is `False`, that is `:!lang`.  
&nbsp;&nbsp;• Renderers must not syntax-highlight any code block whose `:syntax-highlighting` value is `False`; by default `:syntax-highlighting`  is True.  
&nbsp;&nbsp;• Renderers may syntax-highlight any other code block (including `:lang<text>`), but are not required to do so.  
<span class="para" id="c06b8c2"></span>By default, a renderer will not change the contents of a code block, but see the caveat when [`:allow` is used](Markup within verbatim blocks).   

<div id="I/O blocks"></div><div id="I/O_blocks"></div>

#### I/O blocks
<span class="para" id="6274d96"></span>RakuDoc provides blocks for specifying the input and output of programs. These are similar to `code` blocks in that they preserve whitespace and are rendered distinctly from regular text paragraphs.   
<span class="para" id="0a57092"></span>The `=input` block is used to specify pre-formatted keyboard input, which should be rendered without re-justification or squeezing of whitespace.   
<span class="para" id="2cc2dd6"></span>The `=output` block is used to specify pre-formatted terminal or file output, which should also be rendered without re-justification or whitespace-squeezing.   
Although they are rendered with verbatim whitespace, like a code block, input and output blocks differ from code blocks in that they do recognize any inline markup instructions within their text.  
For example:  

```
=begin output
Name:    Baracus, B.A.
Rank:    Sgt
Serial:  1PTDF007

Do you want additional personnel details? K<y>

Height:  180cm/5'11"
Weight:  104kg/230lb
Age:     49

Print? K<n>
=end output
```
<span class="para" id="f69c967"></span>In this example, the two embedded `K&lt;...>` sequences would be recognized as inline markup and rendered appropriately (*i.e.* as keyboard input).   

<div id="Markup within verbatim blocks"></div><div id="Markup_within_verbatim_blocks"></div>

#### Markup within verbatim blocks
<span class="para" id="9c7fe5b"></span>Although `=code` blocks automatically disregard all markup instructions, occasionally you may still need to specify some markup within a code block. For example, you may wish to emphasize a particular keyword in an example (using a `B&lt;>` code). Or you may want to indicate that part of the example is metasyntactic (using the `R&lt;>` code). Or you might need to insert a non-ASCII character (using the `E&lt;>` code).   
<span class="para" id="f44ed95"></span>You can specify a list of [display only](Display only codes) markup instructions (`B`, `C`, `H`, `I`, `J`, `K`, `N`, `O`, `R`, `S`, `T`, `U`, `V`) that should still be recognized within a code block using the `:allow` option. The value of the `:allow` option must be a list of the (single-letter) names of one or more markup instructions. Those codes will then remain active inside the code block. For example:   

```
=begin code :allow< B R > :lang<RakuDoc>
sub demo {
    B<say> 'Hello R&lt;name>';
    I&lt;note> 'The I format is not recognized';
}
=end code
```
This would be rendered:  

```
sub demo {
    say 'Hello name';
    I&lt;note> 'The I format is not recognized';
}
```
<span class="para" id="16799a1"></span>It should be noted that both `:allow` and `:lang` (if `:syntax-highlighting` is True) will affect the rendering of the content of the block. This is likely to cause conflicts in some cases. The renderer is free to choose how to resolve such conflicts, *e.g.* by disregarding the `:allow` metadata.   
<div id="Lists"></div>

### Lists
<span class="para" id="3c51523"></span>Lists in RakuDoc are specified as a series of contiguous `=item` blocks. No special "container" directives or other list delimiters are required to enclose the entire list.   
<span class="para" id="2a194d7"></span>Note that `=item` is just an abbreviation for `=item1`.   

<div id="Unordered lists"></div><div id="Unordered_lists"></div>

#### Unordered lists
Lists in RakuDoc are by default unordered. For example:  

```
The three suspects are:

=item  Happy
=item  Sleepy
=item  Grumpy
```
This produces:  
> The three suspects are:&nbsp;&nbsp;• Happy  
&nbsp;&nbsp;• Sleepy  
&nbsp;&nbsp;• Grumpy  


<span class="para" id="42ce697"></span>By default, a compliant renderer will provide a bullet for each item. RakuDoc also allows for custom bullets as [discussed below](Bullets and bullet strategy).   

<div id="Multi-level lists"></div><div id="Multi-level_lists"></div>

#### Multi-level lists
<span class="para" id="423577d"></span>Lists may be multi-level, with items at each level specified using the `=item1`, `=item2`, `=item3`, *etc.* blocks. (The indentation depends on the rendering engine and does not need to be included in the RakuDoc markup.) Up to four levels are normally differentiated.   
For example:  

```
=item1  Animal
=item2     Vertebrate
=item3         Mammals
=item4             Primates
=item2     Invertebrate

=item1  Phase
=item2  Solid
=item3  Crystalline
=item3  Amorphous
=item2  Liquid
=item2  Gas
```
This would produce:  
> &nbsp;&nbsp;• Animal  
&nbsp;&nbsp;&nbsp;&nbsp;▹ Vertebrate  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;‣ Mammals  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;⁃ Primates  
&nbsp;&nbsp;&nbsp;&nbsp;▹ Invertebrate  
&nbsp;&nbsp;• Phase  
&nbsp;&nbsp;&nbsp;&nbsp;▹ Solid  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;‣ Crystalline  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;‣ Amorphous  
&nbsp;&nbsp;&nbsp;&nbsp;▹ Liquid  
&nbsp;&nbsp;&nbsp;&nbsp;▹ Gas  


<span class="para" id="b58b908"></span>Note, however, that `item` blocks within the same list are not *physically* nested. That is, subordinate items should not be specified inside superordinate items:   

```
=comment THE WRONG WAY...
=begin item1          ───────────────┐
The choices are:                     │
=item2 Liberty          ─── Level 2  ├─── Level 1
=item2 Death            ─── Level 2  │
=item2 Beer             ─── Level 2  │
=end item1            ───────────────┘

=comment THE CORRECT WAY...
=begin item1          ───────────────┐
The choices are:                     ├─── Level 1
=end item1            ───────────────┘
=item2 Liberty        ─────────────────── Level 2
=item2 Death          ─────────────────── Level 2
=item2 Beer           ─────────────────── Level 2
```
Renderers are not required to parse nested list items, nor to produce a coherent rendering if they are able to parse them.  

<div id="Multi-paragraph lists"></div><div id="Multi-paragraph_lists"></div>

#### Multi-paragraph lists
<span class="para" id="21f4639"></span>Using the delimited form of the `=item` block (`=begin item` and `=end item`), we can specify items that contain multiple paragraphs.   
For example:  

```
Let’s consider two common proverbs:

=begin item
I<The rain in Spain falls mainly on the plain.>

This is a common myth and an unconscionable slur on the Spanish people,
the majority of whom are extremely attractive.
=end item

=begin item
I<The early bird gets the worm.>

In deciding whether to become an early riser, it is worth considering
whether you would actually enjoy annelids for breakfast.
=end item

As you can see, folk wisdom is often of dubious value.
```
This renders as:  
> Let’s consider two common proverbs:  
&nbsp;&nbsp;• *The rain in Spain falls mainly on the plain.*  
&nbsp;&nbsp;• *The early bird gets the worm.*  
As you can see, folk wisdom is often of dubious value.


<div id="Bullets and bullet strategy"></div><div id="Bullets_and_bullet_strategy"></div>

#### Bullets and bullet strategy
<span class="para" id="c656828"></span>When rendered, each `=itemN` will be preceded by a bullet and each level may have a different bullet. A compliant renderer will define a *default* bullet for at least the first four levels within a nested list, with the bulleting strategy for other levels being up to the renderer.   
<span class="para" id="3c21ec3"></span>The default bullet can be overridden with a custom bullet for any `=itemN`. For example:

```
The project originally consisted of five phases, of which
two are already complete and two have been abandoned:

=for item :bullet« \c[BALLOT BOX WITH CHECK] »
Investigate existing solutions

=for item :bullet« \c[BALLOT BOX WITH CHECK] »
Define a minimal initial feature set

=for item :bullet« \c[BALLOT BOX] »
Implement this minimal set of features

=for item :bullet« \c[BALLOT BOX WITH X] »
Secure 100 million in venture capital

=for item :bullet« \c[BALLOT BOX WITH X] »
Abscond to the Bahamas with the cash
```
... would produce something like a list with checkboxes, thus:  
> The project originally consisted of five phases, of which two are already complete and two have been abandoned:&nbsp;&nbsp;☑ Investigate existing solutions  
&nbsp;&nbsp;☑ Define a minimal initial feature set  
&nbsp;&nbsp;☐ Implement this minimal set of features  
&nbsp;&nbsp;☒ Secure 100 million in venture capital  
&nbsp;&nbsp;☒ Abscond to the Bahamas with the cash  


However, more interesting bullets are possible, including multiple characters. For example:  

```
=for item :bullet« \c[Heavy Check Mark] »
Investigate existing solutions

=for item :bullet« \c[Heavy Check Mark] »
Define a minimal initial feature set

=for item :bullet« \c[Anticlockwise Downwards And Upwards Open Circle Arrows] »
Implement this minimal set of features

=for item :bullet« \c[no entry sign] »
Secure 100 million in venture capital

=for item :bullet« \c[no entry sign, palm tree] »
Abscond to the Bahamas with the cash
```
... to produce  
> &nbsp;&nbsp;✔ Investigate existing solutions  
&nbsp;&nbsp;✔ Define a minimal initial feature set  
&nbsp;&nbsp;🔄 Implement this minimal set of features  
&nbsp;&nbsp;🚫 Secure 100 million in venture capital  
&nbsp;&nbsp;🚫🌴 Abscond to the Bahamas with the cash  


<span class="para" id="eb15a8d"></span>By using the `=config` directive, it is also possible to change the default bullets for the duration of a section. For example,   

```
=begin section
=config item1 :bullet« \c[Earth Globe Europe-Africa] » 
=config item2 :bullet« \c[Hand with Index and Middle Fingers Crossed] » 

The major sources of sustainable energy are:
    =item1 wind
    =item1 hydroelectric
    =item1 solar
    =item1 geothermal
    =item1 fusion
    =item2 (eventually)
=end section
```
...which would produce:  
> The major sources of sustainable energy are:&nbsp;&nbsp;🌍 wind  
&nbsp;&nbsp;🌍 hydroelectric  
&nbsp;&nbsp;🌍 solar  
&nbsp;&nbsp;🌍 geothermal  
&nbsp;&nbsp;🌍 fusion  
&nbsp;&nbsp;&nbsp;&nbsp;🤞 (eventually)

<span class="para" id="c9fd21e"></span>In the event that a custom bullet cannot be rendered (*e.g.* a specified Unicode glyph is unrecognized or unrenderable in the target format), a compliant renderer will fall back to its default bullet and generate an error message.   

<div id="Ordered lists"></div><div id="Ordered_lists"></div>

#### Ordered lists
<span class="para" id="f162f11"></span>A `=numitemN` expresses the *inherent numeration* of a list:

```
=numitem1 Visito
=numitem2 Veni
=numitem2 Vidi
=numitem2 Vici
```
This would produce something like:  
> 1. Visito  
1.1. Veni  
1.2. Vidi  
1.3. Vici

<span class="para" id="8441664"></span>The numbering scheme is at the discretion of the renderer. A renderer might, for example, provide a scheme similar to the examples here, and also provide an enhancement, such as an `:html-ordered` metaoption that might leverage the `<ul type="A">` markup.   
<span class="para" id="71385f8"></span>The numbering of successive `=numitem1` list items increments automatically, but is reset to `1` whenever any other kind of non-ambient RakuDoc block appears between two `=numitem1` blocks. For example:   

```
The options are:

=numitem1 Liberty
=numitem1 Death
=numitem1 Beer

The tools are:

=numitem1 Revolution
=numitem1 Deep-fried peanut butter sandwich
=numitem1 Keg
```
This would produce:  
> The options are:  
1. Liberty  
2. Death  
3. Beer  
The tools are:  
1. Revolution  
2. Deep-fried peanut butter sandwich  
3. Keg

<span class="para" id="c3efead"></span>The numbering of nested items (`=numitem2`, `=numitem3`, *etc.*) resets to 1 whenever the numbering of a superordinate item changes (either resets or increments).   
<span class="para" id="6009dc4"></span>To prevent a `=numitemN` from resetting after a non-item block, you can modify the block’s counter using the `:!restart` option:

```
=numitem1 Retreat to remote Himalayan monastery

=numitem1 Learn the hidden mysteries of space and time

I&lt;????>

=counter numitem1 :!restart
=numitem1 Prophet!
```
This produces:  
> 1. Retreat to remote Himalayan monastery  
2. Learn the hidden mysteries of space and time  
<span class="para" id="c652b23"></span>*????*   
3. Prophet!

<span class="para" id="2de7e62"></span>Normally, if two `=numitemN` blocks are separated by some other kind of block (for example, a `=para`, `=code`, or `=table` block), then the numbering of the second `=numitemN` block resets to 1.   
<span class="para" id="41d65c5"></span>However, if the *`numitemN`* counter is modified with a `:!restart` option (either before or after the intervening block) the numbering of the second `numitemN` block does not reset, but increments instead (*i.e.* as if there had been no intervening non-numbered block).   
<span class="para" id="65b8d57"></span>The `:!restart` option has no effect on the counters of any superordinate or subordinate `=numitem` blocks that are currently active in the same scope. That is: a `=counter numitemN :!restart` causes the counter of every active `=numitemZ` block (where **__Z__** > **__N__**) to reset to 1, and the counter of every active `=numitemA` block (where **__A__** &lt; **__N__**) stays the same.   
<span class="para" id="e177a50"></span>A `=numitem` is the same as a `=numitem1`, by analogy with `=item` and `=head`.   
<span class="para" id="14c1d6d"></span>The underlying paradigm is that every sequence of list instructions (that is: every sequence of `=itemN` and/or `=numitemN` blocks) has a default counter that provides an *inherent numeration*. The `=numitemN` instructions express the numeration when rendered, while the `=itemN` instructions do not.
<span class="para" id="a1e4aaa"></span>When a sequence of list instructions is terminated by another sort of block then, by default, a new list is created with its own inherent numeration. By specifying `:!restart` before the **next** list instruction, the previous *inherent numeration* is preserved and continued.   

<div id="Definition lists"></div><div id="Definition_lists"></div>

#### Definition lists
<span class="para" id="7158320"></span>Lists that define terms or commands can be specified using the `=defn` block, which is equivalent to HTML `<DL>` lists.   
<span class="para" id="b0ea8ef"></span>A definition contains two parts: a term and a defining text. The term is the contents of the first line of the `defn` block (*i.e.* the text immediately after an abbreviated `=defn`, or the first line after a `=for defn` or `=begin defn`). The defining text is the remaining lines within the scope of the `defn` block. A renderer is expected to distinguish between the term and the defining text. For this reason, the term part is rendered verbatim, including any attempted markup codes.   
For example:  

```
=defn Admiration
Our polite recognition of another’s resemblance to ourselves.

=defn Misfortune
The kind of fortune that never misses.
```
...will be rendered as:  
> 
<span style="font-weight: 600; font-style: italic">Admiration</span>

&nbsp;&nbsp;<span style="background-color: lightgrey;">Our polite recognition of another’s resemblance to ourselves.</span>

<span style="font-weight: 600; font-style: italic">Misfortune</span>

&nbsp;&nbsp;<span style="background-color: lightgrey;">The kind of fortune that never misses.</span>



<span class="para" id="891b1ee"></span>A renderer is expected to retain the information generated by a definition list, and the defining text may be targeted by a [link markup instruction](Links).   

<div id="Numbered definitions"></div><div id="Numbered_definitions"></div>

##### Numbered definitions
<span class="para" id="8859ad3"></span>Definitions can be numbered in the same way as `=item` lists. That is a `=numdefn` instruction will express the *inherent numeration* of the definition list when it is rendered.   
<span class="para" id="328752f"></span>As with ordinary lists, if the counter of a `=numdefn` block is configured with a `:!restart` metadata option, then the preceding definition list’s inherent numeration is preserved and continued.   
For example:  

```
=numdefn Aardvark
A lexicographically pre-eminent animal

=numdefn Beaver
An architecturally pre-eminent animal

Et cetera, et cetera...

=numdefn Yak
A grammatically pre-eminent animal

=numdefn Zorilla
A lexicographically penultimate animal
```
...will yield something like:  
> 
<span style="font-weight: 600; font-style: italic">1.Aardvark</span>

&nbsp;&nbsp;<span style="background-color: lightgrey;">A lexicographically pre-eminent animal</span>

<span style="font-weight: 600; font-style: italic">2.Beaver</span>

&nbsp;&nbsp;<span style="background-color: lightgrey;">An architecturally pre-eminent animal</span>

Et cetera, et cetera...
<span style="font-weight: 600; font-style: italic">1.Yak</span>

&nbsp;&nbsp;<span style="background-color: lightgrey;">A grammatically pre-eminent animal</span>

<span style="font-weight: 600; font-style: italic">2.Zorilla</span>

&nbsp;&nbsp;<span style="background-color: lightgrey;">A lexicographically penultimate animal</span>



<span class="para" id="a25db81"></span>In order to continue a list, an explicit `:!restart` must be specified for the block’s counter, such as:   

```
=numdefn Aardvark
A lexicographically pre-eminent animal

=numdefn Beaver
An architecturally pre-eminent animal

Et cetera, et cetera...

=counter numdefn :!restart
=numdefn Yak
A grammatically pre-eminent animal

=numdefn Zorilla
A lexicographically penultimate animal
```
...will yield something like:  
> 
<span style="font-weight: 600; font-style: italic">1.Aardvark</span>

&nbsp;&nbsp;<span style="background-color: lightgrey;">A lexicographically pre-eminent animal</span>

<span style="font-weight: 600; font-style: italic">2.Beaver</span>

&nbsp;&nbsp;<span style="background-color: lightgrey;">An architecturally pre-eminent animal</span>

Et cetera, et cetera...
<span style="font-weight: 600; font-style: italic">3.Yak</span>

&nbsp;&nbsp;<span style="background-color: lightgrey;">A grammatically pre-eminent animal</span>

<span style="font-weight: 600; font-style: italic">4.Zorilla</span>

&nbsp;&nbsp;<span style="background-color: lightgrey;">A lexicographically penultimate animal</span>



<span class="para" id="36f76e6"></span>Suppose a writer needs every numbered definition to form a single monotonic sequence within some block scope (*e.g.* for every definition within a document section, or every definition throughout the entire document). This would seem to require that every `=numdefn` be preceded by a `=counter numdefn :!restart` option. Or the writer could simply pre-configure the auto-restart behaviour for every `=numdefn` within the scope so that the block’s counter never auto-restarts (*i.e.* restarts after no block whatsoever), as follows:   

```
=counter numdefn :restart-after<>
```
<div id="Tables"></div>

### Tables
<span class="para" id="87fc07d"></span>Tables can be specified in RakuDoc using a `=table` block using either a *visual* definition, or a *procedural* definition. The *visual* semantics allow for a simple table to be quickly specified, but the simplicity imposes a number of constraints. The *procedural* semantics overcome those constraints by describing the contents of rows, columns, and cells.   
<span class="para" id="0c53cbd"></span>A table may be given an associated description or title using the `:caption` option and may be included in the [Table of Contents](Table of Contents, Index, and Citations).   
<span class="para" id="4d3aa82"></span>A *procedural* table may only be specified in the *delimited* form. As will become clear below, *procedural semantics* are assumed if the next RakuDoc instruction after a `=begin table` is one of `=row`, `=column`, or `=cell`.   

<div id="Visual description of simple tables"></div><div id="Visual_description_of_simple_tables"></div>

#### Visual description of simple tables
<span class="para" id="b788d02"></span>In a visual table specification, columns are separated by two or more consecutive whitespace characters (*e.g.* double-space or twin-tabs), or by a vertical line (`|`) or a border intersection (`+`), either of which must be separated from any content by at least one whitespace character. Note that only one kind of column separator (*i.e.* only spaced or only `|`/`+`) is allowed in a single line, but different lines are allowed to use different visible column separator types. Using a mixture of visible and non-visible column separator types in a single table is an error.   
<span class="para" id="0798ca2"></span>Rows can be specified in one of two ways: either one row per line, with no separators; or multiple lines per row with explicit horizontal separators – whitespace, intersections (`+`), or horizontal lines (`-`, `=`, `_`) – between *every* row. Either style can also have an explicitly separated header row at the top. If rows are using the two-whitespace-character separator, the row cells should be carefully aligned to ensure the table is interpreted as the user intended.   
<span class="para" id="44af1a1"></span>Each individual table cell is separately formatted, as if it were a nested `=para`. Note that table rows are expected to all have the same number of cells.   
This means you can create tables compactly, line-by-line:  

```
=table
    The Shoveller   Eddie Stevens     King Arthur’s singing shovel
    Blue Raja       Geoffrey Smith    Master of cutlery
    Mr Furious      Roy Orson         Ticking time bomb of fury
    The Bowler      Carol Pinnsler    Haunted bowling ball
```
...or line-by-line with multi-line headers:  

```
=table
    Superhero     | Secret          |
                  | Identity        | Superpower
    ==============|=================|================================
    The Shoveller | Eddie Stevens   | King Arthur’s singing shovel
    Blue Raja     | Geoffrey Smith  | Master of cutlery
    Mr Furious    | Roy Orson       | Ticking time bomb of fury
    The Bowler    | Carol Pinnsler  | Haunted bowling ball
```
<span class="para" id="196247e"></span>...or with multi-line headers *and* multi-line data:   

```
=begin table :caption('The Other Guys')

                    Secret
    Superhero       Identity          Superpower
    =============   ===============   ===================
    The Shoveller   Eddie Stevens     King Arthur’s
                                      singing shovel

    Blue Raja       Geoffrey Smith    Master of cutlery

    Mr Furious      Roy Orson         Ticking time bomb
                                      of fury

    The Bowler      Carol Pinnsler    Haunted bowling ball

=end table
```
<span class="para" id="9adb7eb"></span>Visual-style tables can only have a single header row (namely: everything before the first `=====` line). That single header may, however, span one or more initial lines of the visual table specification, as some of the preceding examples illustrate. However, regardless of how many lines a visual-style header spans, it is always treated as a single header row. Individual renderers are free to choose how they represent visual header contents that have line breaks in them: they may treat them as a single string to be reflowed and wrapped, or they may elect to preserve the original line breaks within each header cell. The only requirement is that the entire header of a visual-style table must be rendered as a single row of cells. If two or more header rows are required, use a *procedural table* instead.   
<span class="para" id="6c07cad"></span>[Further exposition on this topic is available. ](rakudoc:rakudoc_v2_exposition.rakudoc)   

<div id="Procedural description of tables"></div><div id="Procedural_description_of_tables"></div>

#### Procedural description of tables
<span class="para" id="af35f45"></span>A visual table is useful for most purposes, but a *procedural* description is needed if one or more of the following are required:   
&nbsp;&nbsp;• row labels,  
&nbsp;&nbsp;• vertical or horizontal alignments of individual cells, of complete rows or columns, or of the entire table,  
&nbsp;&nbsp;• cells spanning multiple rows and/or columns,  
&nbsp;&nbsp;• cells that include other RakuDoc blocks, such as a nested sub-table or a code block.  
<span class="para" id="6fa4e44"></span>Table semantics are *procedural* if the `=table` declarator is immediately followed by `=cell`, `=row`, or `=column`, otherwise *visual* semantics are assumed.   
<span class="para" id="0c63296"></span>Only `=cell`, `=row`, `=column`, or `=comment` declarations are permitted within the immediate block scope of a procedural table. Note that both `=cell` and `=comment` introduce their own block scopes, so other RakuDoc blocks (including nested tables) may be included within their block scopes.   
<span class="para" id="a8d0eb9"></span>The fundamental idea is that a procedural `=table` sets up a semi-infinite 2D grid of cells, all of which are initially empty. The grid also tracks the fill position (*$POS*: the next empty cell to be filled), and the fill direction (*$DIR*: the direction to move *$POS* after filling; either *across* or *down*). Initially, *$POS* is set to the top left-most cell of the table, and *$DIR* is set to *across*.   
<span class="para" id="dca209e"></span>Subsequent `=cell` blocks start filling in that grid, with any intervening `=row` and `=column` directives adjusting *$POS* and *$DIR*.   
<span class="para" id="9fdbe48"></span>`=cell` blocks can be specified as spanning multiple rows and/or columns, using the `:row-span(WIDTH)`, `:column-span(HEIGHT)`, or `:span(WIDTH, HEIGHT)` annotations, in which case the block fills more than one grid cell (and merges them into a single logical cell), with the top-left corner of the fill starting at *$POS*.
<span class="para" id="c842fb2"></span>A `=cell` block has the following effects:   
&nbsp;&nbsp;• The specified contents are considered to fill in the cell(s) at *$POS* (even if the contents are actually null/empty)  
&nbsp;&nbsp;• *$POS* is moved to the next unfilled cell in the direction specified by *$DIR* (*i.e.* either across or down)  
<span class="para" id="4f77bbc"></span>A `=row` directive has the following effects:   
&nbsp;&nbsp;• *$DIR* is set to *across*  
&nbsp;&nbsp;• If the previous action was not a `=cell` (*i.e.* it was a `=table` , `=row`, or `=column`) then the `=row` directive has no other effects (*i.e.* the subsequent effect is skipped)  
&nbsp;&nbsp;• Otherwise, starting at the row specified by *`$POS`*, find the uppermost row *R* that is ***at-or-below*** the row specified by *`$POS`*, and where row *R* has at least one empty cell in a column strictly to the left of the column specified by *`$POS`*, then move *`$POS`* left-and-down, to the ***left-most*** empty cell in row *R*.  
<span class="para" id="5700336"></span>A `=column` directive has the following effects:   
&nbsp;&nbsp;• *$DIR* is set to *down*  
&nbsp;&nbsp;• If the previous action was not a `=cell` (*i.e.* it was a `=table` , `=row`, or `=column`) then the `=column` directive has no other effects (*i.e.* the subsequent effect is skipped)  
&nbsp;&nbsp;• Otherwise, starting at the column specified by *`$POS`*, find the left-most column *C* that is ***at-or-to-the-right-of*** the column specified by *`$POS`*, and where column *C* has at least one empty cell in a row strictly above the row specified by *`$POS`*, then move *`$POS`* up-and-right, to the ***upper-most*** empty cell in column *C*  
<span class="para" id="a4bcdee"></span>In other words, `=row` searches the south-west quadrant from *`$POS`* to find the most north-westerly empty cell ... and moves *`$POS`* to that cell. And `=column` searches the north-east quadrant from *`$POS`* to find the most north-westerly empty cell ... and moves *`$POS`* to that cell.   
<span class="para" id="f39140c"></span>In addition, `=table` and `=cell` blocks and `=row` and `=column` directives can be annotated with any of the following metadata, which subsequently act as defaults for any `=cell` in their scope:   
&nbsp;&nbsp;• `:align< ALIGNMENTS >` : changes how contents are aligned in a cell **__ALIGNMENTS__** may be any one of: `left`, `centre`, `center`, `right`, and/or any one of: `top`, `middle`, `bottom`.  
&nbsp;&nbsp;• `:header` : specifies that the cell(s) should be considered column headers  
&nbsp;&nbsp;• `:label` : specifies that the cell(s) should be considered row labels (*i.e.* horizontal headers)  

For example:  

```
=begin table :caption('The Other Guys')
    =row :header
        =cell Superhero
        =cell Secret Identity
        =cell Superpower
    =row
        =cell The Shoveller
        =cell Eddie Stevens
        =cell King Arthur’s singing shovel
    =row
        =cell Blue Raja
        =cell Geoffrey Smith
        =cell Master of cutlery
    =row
        =cell The Bowler
        =cell Carol Pinnsler
        =cell Haunted bowling ball
=end table
```
Or, if you prefer to specify the data in columns:  

```
=begin table :caption('The Other Guys')
    =row :header
        =cell Superhero
        =cell Secret Identity
        =cell Superpower
    =row
    =column
        =cell The Shoveller
        =cell Blue Raja
        =cell The Bowler
    =column
        =cell Eddie Stevens
        =cell Geoffrey Smith
        =cell Carol Pinnsler
    =column
        =cell King Arthur’s singing shovel
        =cell Master of cutlery
        =cell Haunted bowling ball
=end table
```
Either of which would produce:  
> 
<div id="The_Other_Guys"></div>
<div>The Other Guys</div> | **Superhero** | **Secret Identity** | **Superpower** |
| :---: | :---: | :---: |
 | The Shoveller | Eddie Stevens | King Arthur’s singing shovel |
 | Blue Raja | Geoffrey Smith | Master of cutlery |
 | The Bowler | Carol Pinnsler | Haunted bowling ball |

<span class="para" id="6c07cad"></span>[Further exposition on this topic is available. ](rakudoc:rakudoc_v2_exposition.rakudoc)   
<div id="Formulae"></div>

### Formulae
<span class="para" id="19aad76"></span>RakuDoc provides a built-in block `=formula` and an inline markup code `F&lt;>` to contain text that will be rendered as formulae.   
<span class="para" id="4a5a721"></span>There are several markup languages for expressing mathematical expressions, so the RakuDoc specification *recommends* the currently most widespread markup syntax for `=formula` and `F&lt;>`, namely the [LaTeX mathematical notation](https://en.wikibooks.org/wiki/LaTeX/Mathematics), including the extensions specified in the [AMSMath package](https://en.wikipedia.org/wiki/AMS-LaTeX)   
<span class="para" id="e688e93"></span>This does not preclude the development of custom blocks to support other mathematical markup languages, *e.g.* a `=MathML` custom block to support another syntax.   
<span class="para" id="d4022fd"></span>Implementors of renderers are ***not*** required to render the contents of these instructions; it is acceptable for a renderer to render the contents of a `=formula` or `F&lt;>` in some other way than as a fully realized mathematical equation.   
<span class="para" id="730db2e"></span>Both `=formula` and `F&lt;>` may take explicit ALT texts, so that the author can decide how their formula should be rendered in cases where the renderer cannot handle the specification.   
<span class="para" id="33b6c58"></span>That is, the `=formula` block accepts an `:alt` metaoption, and the syntax for the `F&lt;>` instruction is either `F&lt; FORMULA >` or `F&lt; ALT | FORMULA >`.
Using the ALT text options, the document author can supply a suitable explicit alternative rendering to renderers that cannot handle the full formula syntax:  

```
We will use the identity: F&lt;∑ 1/n² = 𝜋²/6 | \sum \frac{1}{n^{2}} = \frac{\pi^{2}}{6}>

...where the value of pi can be inferred from Euler’s Identity:

=for formula  :alt< eH&lt;iπ> + 1 = 0 >
e^{i\pi}+1=0
```
An ALT text allows an author to rely on getting an “accurate” representation under those renderers that fully support formulas, and to be able to fall back on a range of reasonable alternatives under those renderers that don’t:  

<div id="Fallback_options"></div>
<div>Fallback options</div> | **Fall back on** | **Example** |
| :---: | :---: |
 | Plaintext | F&lt;  Euler’s Identity | e^{i\pi}+1=0 > |
 | RakuDoc | F&lt;  eH&lt;iπ> + 1 = 0 | e^{i\pi}+1=0 > |
 | Unicode | F&lt;  eⁱᐢ + 1 = 0 | e^{i\pi}+1=0 > |
 | Image | F&lt;  P&lt;file:EulerID.jpg> | e^{i\pi}+1=0 > |
 | Link | F&lt;  L&lt;https://en.wikipedia.org/wiki/Euler%27s_identity> | e^{i\pi}+1=0 > |
<span class="para" id="43685d1"></span>It is also possible to **stack** the fallback options. For example, one could try for an “accurate” rendering of a formula, but fall back on the placement of a pre-rendered image, or (failing to find that) fall back again to a RakuDoc representation with an outgoing link:   

```
We will use: F<
                 P<
                     L< eH<iπ> + 1 = 0
                      | https://en.wikipedia.org/wiki/Euler%27s_identity
                      >
                  >
              | e^{i\pi}+1=0
              >
```
Implementors are free to delay – or reject – implementing LaTeX-based formulas, and implementations are considered compliant with the specification, so long as the ALT texts are rendered.  
<span class="para" id="6c07cad"></span>[Further exposition on this topic is available. ](rakudoc:rakudoc_v2_exposition.rakudoc)   

<div id="RakuDoc comments"></div><div id="RakuDoc_comments"></div>

### RakuDoc comments
RakuDoc comments are components that both RakuDoc renderers and ambient-code compilers ignore.  
<span class="para" id="32628bb"></span>Comments are useful for *meta-*documentation (documenting the documentation). Single-line comments can use an abbreviated `=comment` block:   

```
=comment Add more here about the algorithm
```
<span class="para" id="9316d14"></span>For multi-line comments, use a delimited `comment` block:   

```
=begin comment
This comment is
multi-line.
=end comment
```

<div id="Semantic blocks"></div><div id="Semantic_blocks"></div>

### Semantic blocks
All uppercase block typenames are reserved for specifying standard documentation, publishing components, source constructs, or meta-information. The following must be recognized:  

```
=NAME
=AUTHOR
=VERSION
=TITLE
=SUBTITLE
=LICENSE
=LICENCE
=SYNOPSIS
```
<span class="para" id="03ed663"></span>The block name of a semantic block is to be rendered as if it were `=head BLOCKNAME`, and the contents of the block are then to be rendered as one or more regular paragraphs. Consequently, if a semantic block has multiple lines, then its contents must be enclosed using the ***delimited*** form.   
A semantic block may be defined, but omitted from the text in the place it is written. This is because sections about Authors, or Licensing, are sometimes required at the end, or at the beginning, or in the meta section of an HTML document.  
A renderer is expected to gather the contents of a semantic block for it to be accessible by an external program.  
<span class="para" id="a8c9673"></span>If the metadata option `:hidden` is associated with a semantic block (by using a `=config` directive, or as a direct metadata option on the *extended* or *delimited* forms) then the renderer will not render the semantic block at the position it is located in the RakuDoc document. It will be placed in a data structure by the renderer and the contents can then be rendered at a place defined by a [Placement instruction](Placement links).   
<span class="para" id="2c65111"></span>The block name of a rendered semantic block will be placed in the [*Table of Contents*](Table of Contents, Index, and Citations) table in the position that it is rendered, unless the `:!toc` and or `:caption` options are set. A `:hidden` metadata option will also remove the block’s entry from the ToC. But if it is placed by a `P&lt;semantic:>` instruction, the block entry will be placed at the appropriate point in the ToC.   
<div id="Citations"></div>

### Citations
<span class="para" id="6be136c"></span>RakuDoc provides a mechanism for citing external reference sources in the usual way: with a compact marker in the text and the full information for the source material listed elsewhere. Citations in RakuDoc are marked and listed in an appearance-neutral syntax that allows them to be rendered consistently in any standard citation style (*e.g.* ACM, APA, Chicago, IEEE, Harvard, MLA, *etc.*).   

<div id="Citation indicators"></div><div id="Citation_indicators"></div>

#### Citation indicators
<span class="para" id="d3c83f7"></span>Individual citations of reference sources in the text are indicated with a `Q&lt;>` formatting code. The value contained within each such formatting code must be a unique *citation identifier* associated with some cited reference work within the document (see below). For example:   

```
=para
We have now explored Q&lt;rakudoc-evo-chapter> at some length
whether it would be better to use JSON or XML Q&lt; JsonVsXMLMeta >
to specify citation data or whether it might be better to design
a bespoke DSL Q«10.1109/MODELS50736.2021.00031» instead.
```
<span class="para" id="4accce7"></span>A single `Q&lt;>` can contain multiple identifiers, which must be separated by semicolons. For example:   

```
=para
The design of Raku commenced Q&lt;Apocalypse1; P6&Parrot; ConwayKeynote2010> in late 2000...
```
<span class="para" id="aa8b19e"></span>Depending on the chosen bibliographic style in which the document is eventually rendered, multi-identifier `Q&lt;>` codes may be rendered separately:   
> The design of Raku commenced [7] [9] [10] in late 2000...

...or aggregated into a single bibliographic marker:  
> The design of Raku commenced [7,9-10] in late 2000...

<span class="para" id="09f3e86"></span>Each identifier can also optionally be followed by comma and an annotation, which may be used to refine the context of a given citation by specifying particular sections, chapters, pages, figures, tables, timestamps, *etc.* within the work being cited. For example:   

```
=para
We have now explored Q<rakudoc-evo-chapter, pp. 32-47> at some length
whether it would be better to use JSON or XML Q<JsonVsXMLMeta, table 8b>
to specify citation data or whether it might be better to design
a bespoke DSL Q<10.1109/MODELS50736.2021.00031, sect. III> instead.

=para
The design of Raku commenced Q<Apocalypse1; P6&Parrot, ch. 1; ConwayKeynote2010, ts=00:03:27>
in late 2000...
```
Note that whether or not these annotations are included as part of the corresponding bibliographic markers, or are silently ignored, is determined entirely by the bibliographic style that is selected when the document is rendered.  

<div id="Citation blocks"></div><div id="Citation_blocks"></div>

#### Citation blocks
<span class="para" id="9c0f445"></span>The full citation dataset for each individual reference work is specified in a `citation` block, as structured textual data in any of the following notations: CSL-JSON, CSL-YAML, CSL-Raku, RIS, MODS, BiBLaTeX, BibTeX, BibTeXML, or Medline/PubMed (XML or NBIB). All of these formats are widely used citation-data-interchange standards, except for CSL-Raku, which is just CSL-JSON/CSL-YAML data transposed into the native Raku syntax for a list of hashes.   
<span class="para" id="db35941"></span>Each `citation` block may contain multiple entries (*i.e.* aggregated citation data for multiple reference works), but the data for every entry within a single block must be represented in the same data format (*e.g.* all in CSL-JSON, or all in BibTeX, *etc.*) A document may, however, contain multiple `citation` blocks, each of which may specify their contents in different data formats.   
For example:  

```
=comment  This citation data in in CSL-JSON format
=begin citation
    [
        {
                   "id": "mercurio2025",
                 "type": "book",
                "title": "The Morphological Beauty of Raku Grammars",
               "author": [ {"family": "Mercurio", "given": "Silvia"} ],
               "issued": {"date-parts": [[2025, 3, 14]]},
            "publisher": "Butterfly Press",
                 "ISBN": "978-1-2345-6789-0"
        }
    ]
=end citation
```

```
=comment  This citation is in BibLaTeX format
=citation  @article{zenraku2024,
               author = {Wall, Larry},
               title = {The Zen of Raku},
               journaltitle = {Journal of Camel Studies},
               date = {2024-05-12},
               volume = {42},
               number = {3},
               pages = {101-115},
               url = {https://example.org/raku},
               urldate = {2026-01-23}
           }
```

```
=comment  This in RIS data
=begin citation
    TY  - JOUR
    ID  - thorne2023
    AU  - Thorne, Alistair
    TI  - Optimization Strategies for MoarVM Bytecode
    JO  - Journal of Virtual Machine Design
    PY  - 2023/08/21/
    VL  - 9
    IS  - 2
    SP  - 210
    EP  - 235
    SN  - 1234-5678
    ER  -
=end citation
```
<span class="para" id="603405f"></span>Note that, for implementation (and perhaps source-highlighting) purposes, the actual species of notation for data contained in each `citation` block can be automatically inferred by examining just the first few non-blank characters in the block’s content (*the “sniff” test*), according to the following table:   

<div id="Table_0"></div>
<div>Table</div> | First char(s) | Inferred format | Because character represents the start of a... |
| :---: | :---: | :---: |
 | `[` | CSL-JSON | JSON array of objects |
 | `-` | CSL-YAML | YAML list of mappings |
 | `(` | CSL-Raku | Raku-native parenthesized list of hashes |
 | `{` | CSL-Raku | Raku-native unparenthesized list of hashes |
 | `@` | BibLaTeX | sequence of standard BibLaTeX or legacy BibTeX entries |
 | `<bibtex` | BibTeXML | sequence of XML-encoded BibTeX entries. |
 | `<mods` | MODS | sequence of XML-encoded MODS entries. |
 | `<PubmedArticleSet` | PubMed/Medline XML | sequence of XML-encoded PubMed/Medline entries. |
 | `PMID-` | PubMed/Medline NBIB | sequence of modern PubMed NBIB KEY-VALUE datasets |
 | `UI  -` | Medline NBIB | sequence of legacy Medline NBIB KEY-VALUE datasets |
 | `TY  -` | RIS | sequence of RIS-format KEY-VALUE datasets |
<span class="para" id="17018c0"></span>Citation data can also be loaded from external sources via a URL, by using the `:load` metaoption:   

```
=for citation  :load< file:/users/jan/.citelists/working/citations.json >

=for citation  :load< https:example.com/markup_design_references.bib >
```
In this variant, the block must not contain any extra data (and renderers should report an error if it does).  
The suffix of the file determines the inferred format of the imported data:  

<div id="Table_0"></div>
<div>Table</div> | **Suffix** | **Inferred format** |
| :---: | :---: |
 | `.bib` | BibLaTeX (including legacy BibTeX) |
 | `.json` | CSL-JSON |
 | `.mods` | MODS |
 | `.nbib` | Medline/PubMed NBIB |
 | `.raku` | CSL-Raku |
 | `.ris ` | RIS |
 | `.xml` | MODS or PubMed XML or BibTeXML (distinguished via “sniff” test) |
 | `.yaml` | CSL-YAML |
<span class="para" id="d6f797b"></span>A document can include any number of `citation` blocks, in any order, located anywhere in the document. Some common approaches might be:   
&nbsp;&nbsp;• One block per citation, with each citation placed immediately before or after the point where the source is referred to via a `Q&lt;>` code within the text.  
&nbsp;&nbsp;• One citation block at the end of each chapter, section, or heading, containing data for all citations made in that unit.  
&nbsp;&nbsp;• A single citation block at the end of the entire document, with all the citations aggregated together.  

<div id="Citation identifiers"></div><div id="Citation_identifiers"></div>

#### Citation identifiers
<span class="para" id="9d77e14"></span>Every dataset included in any `citation` block must contain a *citation identifier*: a text string consisting of any sequence of one or more Unicode characters. Each citation identifier must be unique across all citation datasets within the document.   
<span class="para" id="87e1a43"></span>This identifier may be specified in the `id` field (of any of the CSL notations), the `ID` attribute (of MODS), the mandatory initial ID string (of BibLaTeX, BibTeX, and BibTeXML), the `PMID` or `UI` value (of PubMed/Medline), or the `ID` tag (an unofficial extra tag commonly used with RIS data).   
If a dataset cannot furnish a suitable identifier, but its citation data includes a ISO-26324 Digital Object Identifier, then the DOI may be used instead.  
<span class="para" id="ee263d2"></span>Each citation identifier is used to associate a `Q&lt;>` marker with its corresponding citation data in some `citation` block. For example:   

```
Most programming languages use everyday words for their keywords,
but some have taken that notion much further, basing their entire
syntax on (subsets) of natural languages Q&lt;Perligata> or on conlangs
Q&lt;tlhInganHol::yIghun>.

=begin citation
    [
      {
        "id":              "tlhInganHol::yIghun",
        "type":            "software",
        "title":           "Lingua::tlhInganHol::yIghun: Perl code in the original Klingon",
        "author":          [{ "family": "Conway", "given": "Damian" }],
        "issued":          { "date-parts": [[2009, 6, 1 ]] },
        "abstract":        "Perl module that allows programs to written Klingon syntax and vocabulary.",
        "publisher":       "MetaCPAN",
        "version":         "20090601",
        "URL":             "https://metacpan.org/pod/Lingua::tlhInganHol::yIghun",
        "accessed":        { "date-parts": [[2025, 1, 25 ]] }
      },
      {
        "id":              "Perligata",
        "type":            "paper-conference",
        "title":           "Lingua::Romana::Perligata -- Perl for the XXI-imum Century",
        "author":          [{ "family": "Conway", "given": "Damian" }],
        "event-title":     "Proceedings of the Perl Conference 4.0",
        "event-place":     "Monterey, CA, USA",
        "publisher":       "O’Reilly and Associates",
        "publisher-place": "CA, USA",
        "issued":          { "date-parts": [[2000, 7]] },
        "page":            "1-15",
        "ISBN":            "0-596-00013-8"
      }
    ]
=end citation
```
<span class="para" id="5867f6c"></span>When used within a `Q&lt;>`, a citation identifier cannot contain the characters `'|'`, `';'`, `','`, or `'\'`, unless they are escaped with a leading `'\'`. Likewise, it cannot contain the `Q`’s own delimiters, unless they are either balanced or escaped.   
<span class="para" id="6c07cad"></span>[Further exposition on this topic is available. ](rakudoc:rakudoc_v2_exposition.rakudoc)   

<div id="Positioning bibliographic lists"></div><div id="Positioning_bibliographic_lists"></div>

#### Positioning bibliographic lists
<span class="para" id="abbabcf"></span>The eventual bibliographic list that a renderer generates from the data may be placed anywhere within a document, by using a `=place` directive with the `citation:` scheme. For example:   

```
=head References
=place citation:*
```
<span class="para" id="a4df75a"></span>This places a custom ***list-of-citations*** that is specially produced by the renderer, using every dataset from ***all*** of the document’s `=citation` blocks, and applying a user-selected citation style – specified either within the document via a `:citation-style` option (see below), or a style externally selected for the renderer in some way (*e.g.* via a command-line argument or environment variable).   
<span class="para" id="c4df41d"></span>To have the renderer include only particular citations, you can specify something more limited than *“whatever”* (*i.e.* `*`) in the placement:   

```
=head References
=comment Only list sources actually cited in at least one Q&lt;>
=place citation:cited

=head Further Reading
=comment Only list sources not cited in any Q&lt;>
=place citation:uncited
```
<span class="para" id="ddf5821"></span>If a RakuDoc document does not contain any `=place citation:` directives, but includes one or more `Q&lt;>` codes, then the renderer should behave as if there had been a single `=place citation:cited` at the very end of the document.   

<div id="Categorizing citation blocks and selecting bibliographic lists"></div><div id="Categorizing_citation_blocks_and_selecting_bibliographic_lists"></div>

#### Categorizing citation blocks and selecting bibliographic lists
<span class="para" id="368db1a"></span>Individual `citation` blocks can also be declared to belong in one or more user-defined categories (whose names must – as usual for user-defined components – contain at least one uppercase and one lowercase letter).   
<span class="para" id="fa067b9"></span>These categories can then be used to select particular citation data for a given listing. Multiple `citation` blocks can be specified in the same category, and a given `citation` block can be specified as belonging to multiple categories. For example:   

```
=for citation  :category<SyntaxResearch>

=for citation  :category<OtherImpls>

=for citation  :category<SyntaxResearch Semantics>

=for citation  :category<Semantics ImplIssues>

=comment And then...

=head1 Bibliography

=numhead2 Syntactic design
=place citation:SyntaxResearch

=numhead2 Semantic constraints
=place citation:Semantics

=numhead2 Implementation
=place citation:ImplIssues,OtherImpls
```

<div id="Rendering citations"></div><div id="Rendering_citations"></div>

#### Rendering citations
<span class="para" id="46ee182"></span>When a `Q&lt;>` marker or `=place citation:` list is to be rendered, the renderer must determine the *citation style* in which the markers and the citations list are to be formatted (*i.e.* ACM, APA, Chicago, IEEE, Harvard, *etc.*)   
<span class="para" id="aa44606"></span>The required styling information may be hard-coded directly into a document, by specifying the `:citation-style` option in the [document scope](Document scope). The `:citation-style` value may be specified either as a full URL for the corresponding CSL style file:   

```
=document :citation-style< https://raw.githubusercontent.com/citation-style-language/styles/refs/heads/master/apa-numeric-superscript-brackets.csl >
```
...or as just the standard name of the CSL style (without its filepath prefix or extension suffix):  

```
=document :citation-style< chicago-author-date >
```
<span class="para" id="dc71a87"></span>In the absence of an explicit specification within a RakuDoc document, the default citation style is [ IEEE ](https://raw.githubusercontent.com/citation-style-language/styles/master/ieee.csl). That is, each document begins with an implicit `=document :citation-style<ieee>`.   
<span class="para" id="6d08e4e"></span>A CSL locale is also required and may be hard-wired into a RakuDoc document, using the document level `:citation-locale` option, which takes an [ IETF BCP 47 language tag ](https://en.wikipedia.org/wiki/IETF_language_tag) specifying the desired locale:   

```
=document :citation-locale< de-CH >
```
<span class="para" id="2873902"></span>The default locale for a RakuDoc source is [ en-GB ](https://raw.githubusercontent.com/citation-style-language/locales/master/locales-en-GB.xml). That is, each document begins with an implicit `=document :citation-locale< en-GB >`.   
> <span class="para" id="e5505ab"></span>Note: the [ en-US locale ](https://raw.githubusercontent.com/citation-style-language/locales/master/locales-en-US.xml) was not chosen as the default because it overpunctuates abbreviations and because it uses the month-day-year date sequence.

<span class="para" id="5ef63a6"></span>The specification order of the original `citation` data within a RakuDoc document is entirely unrelated to its final rendering; the order is always determined by the specified citation style and locale.   
When rendering to output formats that support intradocument linkages (such as HTML), renderers may wish to link each rendered bibliographic marker to the corresponding bibliographic reference in the rendered bibliographic list, and perhaps back-link the reference to the marker as well. This approach is encouraged, but not required.  

<div id="Missing citations"></div><div id="Missing_citations"></div>

#### Missing citations
<span class="para" id="f5a1aa3"></span>If any `Q&lt;>` contains a citation identifier that is not present anywhere in the document’s citation data, and if the author has explicitly requested a listing for that non-existent citation dataset (*e.g.* by specifying a `=place citation:*` or `=place citation:cited`), then the missing dataset must be listed in such a way that it is clearly marked as absent/unknown.   
<span class="para" id="97e7ce5"></span>The missing listing must also still be associated with a suitable marker generated for the orphaned `Q&lt;>` code when the citations are listed. For example, if a `Q&lt;Wall42>` or `Q&lt;SapWho99>` appears in the text, but there are no datasets in any `citation` block that contain those particular citation identifiers, then the markers must still be rendered appropriately in the text, as something like:   
> <span class="para" id="3802d5b"></span>As discussed in Wall’s early treatise on language design**<sup>[7,8]</sup>**, the apparent design tension between *manipulexity* and *whipuptitude* may only be an artefact of our fixation with linguistic minimalism.

...and the missing reference sources might be rendered like so (in IEEE style):  
> <span class="para" id="9f6f207"></span>[7] R. Unknown, “NO SUCH CITATION ID: Wall42”, *Did you misspell Wall42?*, 0.

> <span class="para" id="c77a560"></span>[8] R. Unknown, “NO SUCH CITATION ID: SapWho99”, *Did you misspell SapWho99?*, 1.

...or like so (MLA style):  
> <span class="para" id="4a90642"></span>Unknown, Reference. 0. "NO SUCH CITATION ID: Wall42." *Did you misspell Wall42?*

> <span class="para" id="43439d9"></span>Unknown, Reference. 1. "NO SUCH CITATION ID: SapWho99." *Did you misspell SapWho99?*

That is, a RakuDoc renderer must autogenerate a pseudo-entry for each missing citation dataset, with the issue dates numbered sequentially from 1, as if the absent citation data had been:  

```
=begin citation
    [
      {
        "id":              "Wall42",
        "type":            "article",
        "title":           "NO SUCH CITATION ID: Wall42",
        "author":          [{ "family": "Unknown", "given": "Reference" }],
        "container-title": "Did you misspell Wall42?",
        "issued":          { "date-parts": [[0]] }
      },
      {
        "id":              "SapWho99",
        "type":            "article",
        "title":           "NO SUCH CITATION ID: SapWho99",
        "author":          [{ "family": "Unknown", "given": "Reference" }],
        "container-title": "Did you misspell SapWho99?",
        "issued":          { "date-parts": [[1]] }
      },
    ]
=end citation
```

<div id="User-defined blocks"></div><div id="User-defined_blocks"></div>

### User-defined blocks
<span class="para" id="18209fb"></span>When a block name contains both an uppercase and a lowercase character, as specified [in the naming rules below](Naming rules), it is interpreted as a developer-defined block. The naming rules are to ensure that the names of built-in blocks, semantic blocks, and custom blocks never collide.   
The way in which a renderer styles the contents of a custom block depends on the renderer, and each renderer may specify a procedure for using user-supplied templates or code.  
<span class="para" id="bfc7fc2"></span>If a renderer does not recognize a custom block, then the renderer should replace that block with the contents of the block’s ` :alt<...> ` option (if provided). If the document author did not include an ` :alt<...> ` option, then the renderer should treat the block name as the contents of a `=head` block, which can be overridden as described in [Table of Contents](Table of Contents, Index, and Citations) and should render the contents of the unrecognized custom block verbatim, like a `=code` block. Any unrecognized custom block (with or without an ` :alt<...> ` option) should generate a warning message unless the custom block is passed the ` :!warn ` option.   
<span class="para" id="07e5248"></span>Multi-paragraph contents should be enclosed using the *delimited* form of the block.   

<div id="Naming rules"></div><div id="Naming_rules"></div>

### Naming rules
In order to avoid conflicts between built-in blocks, semantic blocks, custom blocks, and markup instructions, the following rules must be followed:  
&nbsp;&nbsp;• Built-in blocks (*e.g.* `head`, `item`, `table`)  
&nbsp;&nbsp;&nbsp;&nbsp;▹ names must be all lowercase and at least two characters long  
&nbsp;&nbsp;&nbsp;&nbsp;▹ renderers may choose how to render built-in blocks, but must respect all the defined metadata options specified here, and may respect additional metadata options  
&nbsp;&nbsp;• Semantic blocks (*e.g.* `AUTHOR`, `TITLE`)  
&nbsp;&nbsp;&nbsp;&nbsp;▹ names must be all uppercase and at least two characters long  
&nbsp;&nbsp;&nbsp;&nbsp;▹ renderers should treat these as if they are `head1` with a caption consisting of the semantic block name  
&nbsp;&nbsp;&nbsp;&nbsp;▹ the contents of the block should be treated as a paragraph.  
&nbsp;&nbsp;• Custom blocks (*e.g.* `MyNewBlock`)  
&nbsp;&nbsp;&nbsp;&nbsp;▹ names must contain at least one uppercase, and at least one lowercase character  
&nbsp;&nbsp;&nbsp;&nbsp;▹ renderers will define how users may specify metadata options and syntax  
&nbsp;&nbsp;&nbsp;&nbsp;▹ renderers will define how the contents of the block are interpreted and rendered  
&nbsp;&nbsp;• Custom markup codes (*e.g.* `Æ`, `Ø`, `ß`)  
&nbsp;&nbsp;&nbsp;&nbsp;▹ names must consist of exactly one uppercase Unicode character (*i.e.* with the *Upper* property)  
&nbsp;&nbsp;&nbsp;&nbsp;▹ names may not be a character in the ASCII range (these are reserved for current and future built-in markup codes)  
&nbsp;&nbsp;&nbsp;&nbsp;▹ the built-in markup instruction – [`M&lt;>`](Markup extras) – is also provided for custom markup  
&nbsp;&nbsp;• Custom options  
&nbsp;&nbsp;&nbsp;&nbsp;▹ Renderers may provide additional options for all blocks and for the document, and it is strongly recommended that such option names consist of at least one uppercase and at least one lower case character to distinguish them from options specified in this document.  

<div id="Implied code and indentation"></div><div id="Implied_code_and_indentation"></div>

### Implied code and indentation
<span class="para" id="357e9d2"></span>An example of implied code was given in the [section on Code blocks](Code blocks). Implied code can be used in other blocks.   
<span class="para" id="8eaed0e"></span>Each block creates a virtual margin (at the column before its leading `=`) and that virtual margin extends ***only*** to the end of the block (*i.e.* **not** to the start of the next named block). Thus:   

```
┊=begin rakudoc
┊This is an implicit =para block
┊
┊   This is implicit =code block
┊
   ┊=begin nested
   ┊This is an implicit =para block
   ┊
   ┊   This is implicit =code block
   ┊
      ┊=begin nested
      ┊This is an implicit =para block
      ┊
      ┊   This is implicit =code block
      ┊
      ┊=end nested
   ┊
              ┊=for nested
              ┊This is an implicit =para block
              ┊over multiple lines
              ┊ending at this final non-blank line
   ┊
   ┊           This is implicit =code block (NOT a =para)
   ┊
   ┊=end nested
┊
┊This is an implicit =para block
┊
┊    This is implicit =code block
┊
┊=end rakudoc
```
Which means that, in the following example:  

```
=begin rakudoc
    =for Podcast
        this is not code

    this is code
=end rakudoc
```
<span class="para" id="25aef13"></span>...the final line is an implicit `code` block, because it is indented from the virtual margin of its containing `=begin rakudoc`/`=end rakudoc` block.   
Whereas in:  

```
=begin rakudoc
=for Podcast
    this is not code

this is not code either
=end rakudoc
```
<span class="para" id="fabb621"></span>...the final content is an implicit `para` block, because it starts at the virtual margin of its containing `=begin rakudoc`/`=end rakudoc` block.   
<span class="para" id="7a3ef09"></span>Indentation from the current virtual margin only implies a para or code block in blocks that **don’t** already explicitly specify otherwise. Hence, all the following *“container blocks”* should honour the *indented means `code`* and *on-margin means `para`* shortcuts: `=cell`, `=defn`, `=item`, `=itemN`, `=nested`, `=rakudoc`, `=section`, `=pod`.   
<span class="para" id="4c64a29"></span>***None*** of the following blocks should infer `para` or `code` from indentation: `=para`, `=code`, `=input`, `=output`, `=comment`, `=table`, `=formula`, `=data`, `=head`, `=headN`, `=SEMANTIC`.
<span class="para" id="2453144"></span>That’s because each block in this second group specifies explicitly what type of information their contents are supposed to be, so there’s no need to infer anything. In particular, `=para` and `=code` are specifically provided to *circumvent* inference from indentation.   
<span class="para" id="d82af9b"></span>A `=config code` directive will also apply to any implicit code block in the block scope.   
Finally, user-defined custom blocks should always pass their contents verbatim to whatever appropriate user-defined handler is in scope, and it’s up to that handler to infer the meaning of any indentation.  
<span class="para" id="6c07cad"></span>[Further exposition on this topic is available. ](rakudoc:rakudoc_v2_exposition.rakudoc)   
<div id="Metadata"></div>

## Metadata
A powerful feature of RakuDoc is the ability to associate metadata with a block. Metadata can be used to change the way a block is rendered, or to allow a renderer to access data in the cloud, or receive data from a microservice.  
Some metadata options are defined for certain blocks and a renderer must produce the behaviour described in this section (or in the section for the relevant block), to the extent possible for a given output format.  
A renderer may ignore metadata options not specified in this document, or define other metadata options that it will recognize.  
<span class="para" id="6c07cad"></span>[Further exposition on this topic is available. ](rakudoc:rakudoc_v2_exposition.rakudoc)   
<div id="Anchors"></div>

### Anchors
<span class="para" id="829f00f"></span>Whenever a `=head` block such as:   

```
=head Table of Contents, Index, and Citations
```
<span class="para" id="0632dfe"></span>...is rendered, it is also associated with an anchor, so that when a link of the form `L&lt;link to an internal heading|#Table of Contents, Index, and Citations>` is rendered, the renderer will place a link to the correct heading in the output format. The anchor will also be used in the Table of Contents.   
<span class="para" id="531d6d0"></span>Note that the text in the `L&lt;...>` before the `|` is the display text, and the tag after the `|` is the content of the target `=head` block.   
In order for a link to work correctly, the anchor must be unique. The renderer is free to chose the algorithm it uses for generating the internal anchor. This is because different outputs, such as Markdown and HTML, have different formats for anchors.  
<span class="para" id="a658b2a"></span>An author may want to shorten an internal link to a title, and also to place an anchor on every block. For this purpose, the `:id<LABEL>` metadata option can be attached to any block. For example:

```
=for head :id<ToCIaC>
Table of Contents, Index, and Citations

...and later we can add: L&lt;link to an internal heading|#ToCIaC>
```
<span class="para" id="3b487d4"></span>The renderer is expected to create a link with that name to the start of the block. It is up to the author of the document to ensure that all `:id` links are unique.   
<span class="para" id="32650dc"></span>A renderer may mangle the link name (*e.g.* the contents of a header) to create an anchor ID internally because different output formats may place restrictions on the characters that can be used in a link. However, a document author may not rely on a renderer’s ID generation algorithm.   
<span class="para" id="276cec7"></span>Internal links (links to anchors within the same document) must be specified by a leading `#` followed by either:&nbsp;&nbsp;• the target block’s explicit `:id<LABEL>`, or  
&nbsp;&nbsp;• the exact contents of the corresponding `=head` block, or  
&nbsp;&nbsp;• the exact contents of a block’s explicit `:caption<TEXT>`.  

<span class="para" id="fd649c8"></span>Internal links cannot be specified by a `#` followed by:   
&nbsp;&nbsp;• a `=head` block’s implicit *autogenerated-from-content* ID, or  
&nbsp;&nbsp;• a block’s implicit *autogenerated-from-caption* ID.  
<span class="para" id="727e6c6"></span>That is, the in-document link target cannot be the mangled contents of a `=head` block or a `:caption<...>` metatag; it can only be the original contents.   
For example:  

```
=for head :id<h123>
A sample heading

=for table :id<t456> :caption<Example table>
A  B  C
1  2  3

This is a L&lt;valid link (to an explicit block ID) | #h123 >
This is a L&lt;valid link (to an explicit block ID) | #t456 >

This is a L&lt;valid link (to a heading) | #A sample heading >
This is a L&lt;valid link (to a caption) | #Example table   >

This is L&lt;NOT a valid link | #A_sample_heading >
This is L&lt;NOT a valid link | #Example_table   >
```

<div id="Table of Contents, Index, and Citations"></div><div id="Table_of_Contents,_Index,_and_Citations"></div>

### Table of Contents, Index, and Citations
<span class="para" id="a5d0cea"></span>When a renderer parses a file containing RakuDoc, information is collected about headings (from `=headN` and semantic block statements), as well as `X&lt;>` and `Q&lt;>` markup codes.
<span class="para" id="b52a62e"></span>This information, if it exists, is expected to be rendered in an automatic *Table of Contents*, an *Index*, or a *Citations list*, and placed in a manner determined by the renderer (*e.g.* as a sidebar Table of Contents, or as a separate Index or Bibliography at the end of the main text).   
<span class="para" id="cdbe04c"></span>The information may also be filtered and placed by the author using [a `P&lt;>` markup instruction](Placement links).   
<span class="para" id="5b62278"></span>To avoid duplication, if an author uses a `=place` statement to filter information that would usually be automatically rendered, the automated rendering is cancelled. In this way, the author can circumvent the automated rendering of information and take explicit control of their positioning and content.   
<span class="para" id="15e0543"></span>Additionally, some authors may not want any *Table of Contents*, *Index*, or *Citations list*. The automated generation of these structures can be prevented via the appropriate document-level meta option:   

```
=document :!auto-toc :!auto-index
```
<span class="para" id="e365a89"></span>Renderers will typically add default captions or titles for automatically generated content. These can also be changed using the various *:auto-...* meta options:   

```
=document :auto-toc<Contents> :auto-index<Glossary> :auto-citations<List of References>
```
<span class="para" id="57bdac6"></span>Setting `:toc` on a block will cause the complete contents of that block to be included in the Table of Contents. Including the entire block contents is usually not desirable, so the `:caption<...> ` meta option can be used to provide a more appropriate text for the Table of Contents.   
<span class="para" id="44efe2e"></span>Setting `:!toc` will prevent a block’s information from being included in the Table of Contents.   
<span class="para" id="4b6399d"></span>When a built-in block, such as `table`, needs to be entered into the *Table of Contents* and styled like a `=head2`, then use `=table2` instead of `table`.   
<span class="para" id="db20b88"></span>Custom Tables of Contents can be created to group together tables, formulas, code examples, *etc.*. This is described in [Custom tables of contents](Custom tables of contents). Note that placing information gathered for custom Tables of Content will **not** cancel the automated generation of the default *head* Table of Contents.   

<div id="Custom tables of contents"></div><div id="Custom_tables_of_contents"></div>

#### Custom tables of contents
<span class="para" id="5267be8"></span>The implicit heading level of a block in the Table of Contents (TOC) is always equal to its *block level*. Hence `=head1` is implicitly level-1, while `=formula3` is implicitly level-3.   
<span class="para" id="33e5329"></span>The default TOC is created from `=head` and semantic blocks (*e.g.* `=SYNOPSIS`), which are defined to have the `:toc` option set to **True**. The semantic blocks `=TITLE` and `=SUBTITLE` are not typically included in a Table of Contents, and so are defined to have the `:!toc` option.   
<span class="para" id="093d1d3"></span>Other blocks may be added to the default Table of Contents. To add a single block (or an entire block type) to the default TOC (*a.k.a.* the `head` TOC), configure it with an explicit `:toc` option. For example, if you want all first-level formulas to be listed in the TOC, preconfigure `=formula1` with `:toc`:   

```
=config formula1 :toc
```
<span class="para" id="3e53412"></span>Then use `=formula1` or `=formula` to specify each formula.   
<span class="para" id="22b1f87"></span>On the other hand, if you want only one particular table included as a third-level TOC entry, specify the `:toc` option on just that one block:   

```
=for table3 :caption<XOR logic> :toc
    |   | 0 | 1 |
    | 0 | 0 | 1 |
    | 1 | 1 | 0 |
```
<span class="para" id="1066e3f"></span>You can also specify that a particular block or block type belongs to one or more other TOCs, by specifying the TOC name(s) as an optional argument to `:toc`. For example:   

```
=comment All =formula3 and =Chart2 blocks are (only) included in “Diagrams” TOC...
=config formula3 :toc< Diagrams >
=config Chart2   :toc< Diagrams >

=comment This specific table is included in the default (head) TOC, as well as in the Diagrams and table TOCs...
=begin table :toc< head Diagrams table >
| NW | N | NE |
|  W | * | E  |
| SW | S | SE |
=end table
```
<span class="para" id="3b4cdd8"></span>To insert these other TOCs into a document, use a `=place` directive at the location where each custom table of contents is required. If you do not want your other TOCs to themselves be listed in the main TOC, add the `:!toc` option to the placements. For example:   

```
=place toc:Diagrams :!toc :caption<List of Diagrams>
```
<span class="para" id="52f2cf4"></span>More detail about constraining the TOC levels can be found in the section on [placements]().   

<div id="Developer or delta notes"></div><div id="Developer_or_delta_notes"></div>

### Developer or delta notes
When a set of source files is related to a project with versions (such as the Raku language), documentation may get out of date, or may only apply to features of a future release.  
<span class="para" id="f360ab7"></span>A delta note can be attached to a block using the `:delta` metadata option, or specified inline with the ` Δ&lt; ... > ` markup instruction. Both the metadata option and the markup instruction have two arguments: the first is the version specification, and the second is an optional text note.   
> <span class="para" id="0f07a9c"></span>**Note:** This is an example of a built-in markup code that uses a Unicode Upper character. The mnemonic is *Delta*, which is commonly used to indicate an incremental component.

The version specification is summarized as follows:  

<div id="Table_0"></div>
| **Syntax** | **Meaning** |
| :----:  | :----: |
| '*'  | Any version |
 | v1.2.3  | specific version |
 | v1.2.3+  | specific version or later |
 | v1.2.3-  | specific version or earlier |
 | v1.2.3..v2.3.4  | inclusive range of versions |
 | v1.2.3^..^v2.3.4  | exclusive range of versions |
 | v1.2.3..^v2.3.4 or v1.2.3^..v2.3.4 | semi-inclusive range of versions |

<span class="para" id="8c8ce84"></span>The version specification (*e.g.* v1.2.3) shown here follows the rules of [semantic versioning](https://semver.org), which is the preferred form for Raku documentation, but is not mandatory. Note that the `..` and `^` characters create Raku ranges.   
With no extra information about which version should be displayed, a renderer should have a way to include both the string and the version specification.  
<span class="para" id="db325e4"></span>Alternatively, if there is information about the version to be shown (*e.g.* from a command-line argument), a renderer should only show blocks that correspond to the version information. A block that does not have an explicit `:delta` is treated as if it has `:delta<*>`.   
<span class="para" id="77d7c5a"></span>An example is given in the description of the [`section` block ](Sections)   

<div id="Alternative rendering"></div><div id="Alternative_rendering"></div>

### Alternative rendering
<span class="para" id="3c4ad55"></span>If the metadata option `:alt` associated with a block has a string value, and an error is encountered when rendering a block, then the value of `:alt` is rendered in place of the block.   

<div id="Markup instructions"></div><div id="Markup_instructions"></div>

## Markup instructions
Markup instructions provide a way to add inline markup to a piece of text.  
All RakuDoc markup instructions consist of a single capital letter followed immediately by a set of single or double angle brackets; Unicode double angle brackets may be used.  
Markup instructions may nest other markup instructions.  
<span class="para" id="e503c33"></span>Some markup instructions only affect their contents and do not have any metadata associated with them. These are sometimes called <span style="font-weight: 600; font-style: italic">formatting codes</span>. Other markup instructions have side effects.   

<div id="Formatting codes"></div><div id="Formatting_codes"></div>

### Formatting codes
<span class="para" id="8f9b82e"></span>RakuDoc characterizes formatting codes semantically: markup tags indicate the purpose/function/status/meaning of the marked-up text and renderers may choose any appropriate visual representation to reflect the indicated significance. Thus the ` B&lt;> ` formatting code does **not** indicate that the text must be *Bold*; it indicates that the text is highly significant: the *Basis* of a sentence. Although, when this highly significant *basis* text is rendered, it might very appropriately be rendered in a bold font.   
The following markup codes should be made available in all renderers.  

<div id="Table_0"></div>
 | **Inline markup** | **Instruction** | **Mnemonic** | **HTML equivalent** | **Markdown equivalent** |
| :---: | :---: | :---: | :---: | :---: |
 | Basis | `B<text>` | *“B for Basis”* | `<strong>text</strong>` | `**text**` |
 | Important | `I<text>` | *“I for Important”* | `<em>text</em>` | `*text*` |
 | Unusual | `U<text>` | *“U for Unusual”* | `<ins>text</ins>` | `<ins>text</ins>` |
 | Weighty | `W<text>` | *“W for Weighty”* | `&lt;span style="font-variant: small-caps;">text&lt;/span>` | `&lt;span style="font-variant: small-caps;">text&lt;/span>` |
 | Struck out | `O<text>` | *“O for Out”* | `<del>text</del>` | `~~text~~` |
 | Superscript | `H<text>` | *“H for High text”* | `<sup>text</sup>` | `<sup>text</sup>` |
 | Subscript | `J<text>` | *“J for Junior text”* | `<sub>text</sub>` | `<sub>text</sub>` |
 | Code | `C<text>` | *“C for Code”* | `<code>text</code>` | ``text`` |
The table contains suggested HTML and Markdown equivalents, however a renderer is free to choose other representations.  
<span class="para" id="1836173"></span>This syntax does not provide a mechanism to associate metadata options with formatting codes explicitly. It is, however, possible to do this via a `=config` directive. For example, ` C&lt;> ` markup by default does not allow for embedded format codes to be rendered, but an author might want ` B&lt;> ` to be allowed. This can be achieved with:   

```
=config C :allow<B>
```
<span class="para" id="3a96d40"></span>This mechanism is explained further in the [syntax section](Directive syntax).   

<div id="Instructions with side effects"></div><div id="Instructions_with_side_effects"></div>

### Instructions with side effects
The following markup instructions, including all customisable instructions, will typically have associated metadata.  
<div id="Links"></div>

#### Links
<span class="para" id="0f34fc8"></span>To create a link, enclose the target specification and any inline text in `L&lt; >`. RakuDoc links are more general than the conventional HTML links.   
<span class="para" id="cb0ef31"></span>The general syntax is ` L&lt; LABEL | TARGET > `
where the optional label is text that is rendered and the target may include a schema. If the label is omitted, then the target is used as the label. Whitespace on either side of the bar is not significant.  
<span class="para" id="4b793b6"></span>When the bar is present with whitespace as the label, then the renderer *must* supply a suitable text for the label. It is recommended that the renderer retrieves the description supplied by the URI target itself. Descriptions are common with modern internet protocols, such as HTTPS, but may not be available with older or poorly configured URIs. In such a case, the renderer may fallback to a text version of the URI itself. This can be summarised as follows:   

<div id="Table_0"></div>
<div>Table</div> | **Link specificiation** | **Displayed text** |
| :---: | :---: |
 | `L&lt; https://example.com >` | The URI itself |
 | `L&lt; some text | https://example.com>` | The text before the `|` |
 | `L&lt; | https://example.com>` | The renderer is free to find a suitable display text, but must in all cases display **something**. The most *"suitable display text"* would be the title supplied by the URI target. |
<span class="para" id="8ddfa8d"></span>When the schema in the URI is missing, then the `https://` schema is implied, which means that if the website url is missing as well, the link is to the same host as the document.   
<span class="para" id="89ce013"></span>The exact name of the resource to which a link is pointing must be used by the renderer. So `L&lt; For reference | README.md >` or `L&lt; text reference | README >`   
<span class="para" id="059e44c"></span>must link to `README.md` and to `README`, with the renderer making no assumption about the file format.   
<span class="para" id="904ae04"></span>However, a collection of RakuDoc source documents may be intended as the base for multiple output formats, such as ` .html `, ` .md `, or ` .pdf `, and so links within the collection may not be able to provide fixed extensions within links. Instead, a group of interlinked RakuDoc documents may need to delegate the selection of an appropriate file format (and extension, if one is needed) to the renderer itself. This is accomplished explicitly using the ` .* ` pseudo extension. For example:   

```
L< dealing with the filesystem | type/IO.Path.* >
```
or for a heading within another resource:  

```
L< getting a directory listing | type/IO.Path.*#routine_dir >
```
<span class="para" id="7bb83d2"></span>When a `.*` (“whatever”) extension is encountered by the renderer, if the output format is Markdown, these links would be rendered with `.md` extensions replacing the `.*` pseudo-extensions:   

```
[dealing with the filesystem](type/IO.Path.md)
[getting a directory listing](type/IO.Path.md#routine_dir)
```
<span class="para" id="1e559d2"></span>Or, if the output format is HTML, these links would be rendered with `.html` extensions instead:   

```
<a href="type/IO.Path.html">             dealing with the filesystem </a>
<a href="type/IO.Path.html#routine_dir"> getting a directory listing </a>
```
Several schemas are possible:  
&nbsp;&nbsp;• `https://` or `http://` The renderer may style the label and cause a jump in a networked environment, as is normal for HTML documents.  
&nbsp;&nbsp;• `rakudoc:` A reference to a RakuDoc source, which might be in a Raku module or a RakuDoc file.  
&nbsp;&nbsp;• `mailto:` An email address. Typically, activating this type of link invokes a mailer.  
&nbsp;&nbsp;• `man:` A link to the system manpages.  
&nbsp;&nbsp;• `isbn:` and `issn:` The International Standard Book Number or International Standard Serial Number for a publication.  
&nbsp;&nbsp;• `defn:` A link to a [block-form definition](Definition lists) or an [inline definition](Inline definitions) of the specified term within the current document.  
<span class="para" id="068f204"></span>To refer to a specific section within a webpage, manpage, or RakuDoc document, add the name of that section after the main link, separated by a `#`. To refer to a section of the current document, omit the external address (*i.e.* start the target specifier with a `#`).   
A renderer is expected to render a link with a schema as follows:  
&nbsp;&nbsp;• At a minimum, a link like `L&lt;rakudoc:ModuleName>` is simply rendered as text, to something like: “the ModuleName documentation”, and `L&lt;man:appname>` to something like: “The appname manpage”. And neither of them attempt to add any kind of actual operational link.  
&nbsp;&nbsp;• At the next level of sophistication, a renderer encountering either link scheme may attempt to shell out a call to `raku --doc ModuleName` or `man appname`, and present the results in a pop-up.  
&nbsp;&nbsp;• If the renderer is in a networked environment, the renderer is expected to provide a set of user selectable options that provide links to such documentation, for example, a set of HTML links in a pop-down box. A renderer might also allow users to preconfigure their preferred website(s) for these schemes, perhaps offering `rakudoc:` links to `https://raku.land/` or `man:` links to `https://www.kernel.org/doc/man-pages/` as defaults.  
Renderers are not expected to handle all schemas beyond the minimum above. Renderers offering more sophisticated styling or operational links should provide ways to configure schemas, whilst offering reasonable defaults.  
<div id="Examples"></div>

##### Examples

```
I&lt;ACM Transactions on Programming Languages and Systems>
is a registered serial publication (L&lt;issn:0164-0925>)

This module implements the standard Unix L&lt;man:find(1)> facilities.

Please forward bug reports to L&lt;mailto:abyss@example.org>

This module needs the L&lt;LAME library|http://www.mp3dev.org/mp3/>.

You could also write the code L&lt;in gibberish|RakuDoc:Acme::Anguish>

Also see: L&lt;man:bash(1)#Compound Commands>,

=defn lexiphania
An unfortunate proclivity for employing grandiloquisms (for example,
words such as "proclivity", "grandiloquism", and indeed "lexiphania").

=defn glossoligation
Restraint of the tongue (voluntary or otherwise)

To treat his chronic L&lt;defn:lexiphania> the doctor prescribed
an immediate L&lt;defn:glossoligation> or, if that proved ineffective,
a complete cephalectomy.
```

<div id="Placement links"></div><div id="Placement_links"></div>

#### Placement links
<span class="para" id="93e0c9f"></span>A second kind of link — the `P&lt;>` or **placement link** — works in the opposite direction. Instead of directing focus out to another document, it allows you to assimilate the contents of another document into your own.   
<span class="para" id="4067530"></span>In other words, the `P&lt;>` markup instruction takes a URI and (where possible) inserts the contents of the corresponding document inline in place of the code itself.   
<span class="para" id="5b7afa0"></span>A URI meets the regex pattern `\w+:\S+`, where the word characters before `:` are called the *schema*. There may not be any explicit whitespace characters in a URI.   
<span class="para" id="28b9187"></span>`P&lt;>` markup is handy for breaking out standard elements of your documentation set into reusable components that can then be incorporated directly into multiple documents. For example:   

```
=COPYRIGHT
P<file:/shared/docs/std_copyright.rakudoc>

=DISCLAIMER
P<http://www.MegaGigaTeraPetaCorp.com/std/disclaimer.txt>
```
might produce:  
> <span class="para" id="9db8543"></span>**Copyright**   
> This document is copyright (c) MegaGigaTeraPetaCorp, 2006. All rights reserved.

<span class="para" id="a22a367"></span>**Disclaimer**   
> ABSOLUTELY NO WARRANTY IS IMPLIED. NOT EVEN OF ANY KIND. WE HAVE SOLD YOU THIS SOFTWARE WITH NO HINT OF A SUGGESTION THAT IT IS EITHER USEFUL OR USABLE. AS FOR GUARANTEES OF CORRECTNESS...DON’T MAKE US LAUGH! AT SOME TIME IN THE FUTURE WE MIGHT DEIGN TO SELL YOU UPGRADES THAT PURPORT TO ADDRESS SOME OF THE APPLICATION’S MANY DEFICIENCIES, BUT NO PROMISES THERE EITHER. WE HAVE MORE LAWYERS ON STAFF THAN YOU HAVE TOTAL EMPLOYEES, SO DON’T EVEN *THINK* ABOUT SUING US. HAVE A NICE DAY.

<span class="para" id="159184b"></span>The `P&lt;>` markup code can also be specified with an optional display text, which will be rendered if the renderer cannot find or access the external data source for the placement link. For example:   

```
=COPYRIGHT
P&lt;The document is copyright. | file:/shared/docs/std_copyright.rakudoc>

=DISCLAIMER
P&lt;NO WARRANTY. NONE. NIL. NADA. | http://www.MegaGigaTeraPetaCorp.com/std/disclaimer.txt>
```
If the filesystem or internet is inaccessible, this might produce:  
> <span class="para" id="9db8543"></span>**Copyright**   
> This document is copyright.

<span class="para" id="a22a367"></span>**Disclaimer**   
> NO WARRANTY. NONE. NIL. NADA.

If a renderer cannot find or access the external data source for a placement link, and the placement link does not have an alternative display text, it must issue a warning and render the URI directly in some form, possibly as an outwards link. For example:  
> <span class="para" id="9db8543"></span>**Copyright**   
> <span class="para" id="e12480b"></span>See: `file:/shared/docs/std_copyright.rakudoc`

<span class="para" id="a22a367"></span>**Disclaimer**   
> <span class="para" id="6360a37"></span>See: [http://www.MegaGigaTeraPetaCorp.com/std/disclaimer.txt](http://www.MegaGigaTeraPetaCorp.com/std/disclaimer.txt)

<span class="para" id="4ca7b68"></span>You can use any of the following URI forms (see [#Links](Links)) in a placement link:   
&nbsp;&nbsp;• `http:` and `https:`  
&nbsp;&nbsp;• `file:`  
&nbsp;&nbsp;• `toc:`  
&nbsp;&nbsp;• `index:`  
&nbsp;&nbsp;• `semantic:`  
&nbsp;&nbsp;• `citations:`  

<div id="TOC placements"></div><div id="TOC_placements"></div>

##### TOC placements
<span class="para" id="689f38a"></span>The `toc:` form is a special pseudo-scheme that inserts a table of contents (TOC) in place of the `P&lt;>` code. After the colon, there must be a string of one or more non-blank characters, called the specification.   
<span class="para" id="62e3118"></span>There is at least one set of TOC data collected by the renderer from the `=head` and semantic blocks, but as described above, custom TOC names can be created.   
<span class="para" id="fd47bbb"></span>The specification is used to select the TOC data set and to constrain how much data is rendered. When the specification is a `*`, a digit, or a range, then the default or *head* TOC is implied. For example, to place a table of contents listing only first- and second-level headings:   

```
P<toc:1..2 >
```
To place a table of contents that lists the first four levels of headings:  

```
P<toc:1..4 >
```
<span class="para" id="a32f4e1"></span>In order to include the whole default (or `head`) table of contents, use `toc:*`.   
<span class="para" id="255c55f"></span>When *toc:* is followed by the name of TOC and optionally a *comma*, then `*`, number, or range, the level restrictions are applied to the named TOC.   
Valid specs would be:  
&nbsp;&nbsp;• `*` (all levels of the default ToC)  
&nbsp;&nbsp;• `1..4` (levels 1-4 of the default ToC)  
&nbsp;&nbsp;• `Diagrams` (meaning all levels of the Diagrams ToC)  
&nbsp;&nbsp;• `Diagrams,*` (all levels of the Diagrams ToC)  
&nbsp;&nbsp;• `table,1..4` (levels 1..4 of the table ToC)  
Invalid specs:  
&nbsp;&nbsp;• `formula,` (missing levels specifier)  
&nbsp;&nbsp;• `,*` (missing TOC name)  
<span class="para" id="1c8e4f4"></span>A document may have as many `P&lt;toc:...>` placements as necessary.

<div id="Index placements"></div><div id="Index_placements"></div>

##### Index placements
<span class="para" id="1ae5f50"></span>The `index:*` form is a special pseudo-scheme that inserts an index table in place of the `P&lt;>` code.   

<div id="Semantic block placements"></div><div id="Semantic_block_placements"></div>

##### Semantic block placements
<span class="para" id="cdae455"></span>The `semantic:NAME` form places the **__NAME__** semantic block at the position of the `P&lt;>` instruction.

<div id="Alias placement"></div><div id="Alias_placement"></div>

#### Alias placement
<span class="para" id="8c24fb9"></span>A variation on the placement instruction is the `A&lt;>` instruction, which is replaced by the contents of the named alias or object specified within its delimiters. For example:   

```
=alias PROGNAME    Earl Irradiatem Eventually
=alias VENDOR      4D Kingdoms
=alias TERMS_URL   L<http://www.4dk.com/eie>

The use of A<PROGNAME> is subject to the terms and conditions
laid out by A<VENDOR>, as specified at A<TERMS_URL>.
```
<span class="para" id="fde09eb"></span>Any Raku object available after the CHECK phase, such as an object that starts with a sigil, is available within an alias placement. Unless the object is already a string type, it is converted to a string during document-generation by implicitly calling `.raku` on it.   
<span class="para" id="240b57c"></span>So, for example, a document can refer to its own filename (as `A&lt;$?FILE>`), or to the subroutine inside which the specific RakuDoc is nested (as `A&lt;&?ROUTINE>`), or to the current class (as `A&lt;$?CLASS>`). Similarly, the value of any program constants defined with sigils can be easily reproduced in documentation:   

```
# Actual code...
constant Num $GROWTH_RATE = 1.6;

=begin rakudoc
=head4 Standard Growth Rate

The standard growth rate is assumed to be A<$GROWTH_RATE>.
=end rakudoc
```
<span class="para" id="3706750"></span>Non-mutating method calls on these objects are also allowed, so a document can reproduce the surrounding subroutine’s signature (`A&lt;&?ROUTINE.signature>`) or the type of a constant (`A&lt;$GROWTH_RATE.WHAT>`).   
<span class="para" id="bf27aa3"></span>See [Aliases](Alias blocks) for further details of the aliasing macro mechanism.   

<div id="Inline definitions"></div><div id="Inline_definitions"></div>

#### Inline definitions
<span class="para" id="4b7d6ad"></span>A `D&lt;>` formatting code marks a piece of text as being the *“inline definition”* of a term. That is: it’s like a `=defn` block, but it’s not a list item; it’s just part of the regular text. Well-written documentation often includes sentences/paragraphs that introduce a new term, and define it. Typically we want to visually distinguish those newly defined terms, and to be able to link back to the paragraph where they’re defined.   
For example:  

```
There ensued a terrible moment of D&lt;coyotus interruptus>: a brief
suspension of the effects of gravity, accompanied by a sudden
to-the-camera realization of imminent downwards acceleration.
```
<span class="para" id="5f66f6d"></span>The contents of the `D&lt;>` represent the term being defined, the location of the `D&lt;>` implies a link target location, and the paragraph containing the `D&lt;>` suggests a suitable pop-up definition of the term (if a renderer supports such).   
<span class="para" id="5355e5f"></span>The definition term in a `D&lt;>` is rendered distinctly (typically in ***bold italics***), and the `D&lt;>` sets up a link target to that term, and possibly a pop-up for the corresponding `L&lt;>` link to display the entire paragraph in which the `D&lt;>` appeared. For this reason, the term is rendered verbatim, including any attempted markup codes.   
<span class="para" id="076657c"></span>In other words, when rendered to HTML, a `D&lt;term being defined>` would be translated to something like:   

```
...a <dfn id="term being defined">term being defined</dfn> would be...
```
<span class="para" id="7efeb4a"></span>And a `L&lt;defn:term being defined>` link would be translated to something like:   

```
...a <a href="#term being defined">term being defined</a> link would be...
```
A definition may be given synonyms, which are specified after a vertical bar and separated by semicolons:  

```
A D&lt;formatting code|formatting codes;formatters> provides a way
to add inline markup to a piece of text.
```

<div id="Space-preserving text"></div><div id="Space-preserving_text"></div>

#### Space-preserving text
<span class="para" id="0881007"></span>Any text enclosed in an `S&lt;>` code is formatted normally, except that every whitespace character in it — including any newline — is preserved. These characters are also treated as being non-breaking (except for the newlines, of course). For example:   

```
The emergency signal is: S<
dot dot dot   dash dash dash   dot dot dot>.
```
would be formatted like so:  

```
The emergency signal is:
dot dot dot   dash dash dash    dot dot dot.
```
rather than:  

```
The emergency signal is: dot dot dot dash dash dash dot dot dot.
```
<div id="Comments"></div>

#### Comments
A comment is text that is never rendered.  
<span class="para" id="d2bda65"></span>To create a comment enclose it in `Z&lt; >`:   

```
Raku is awesome Z<Of course it is!>
```
This would be rendered as:  
> <span class="para" id="8adddf7"></span>Raku is awesome

<div id="Notes"></div>

#### Notes
Notes are ancillary information that may be rendered as footnotes or sidenotes or pop-up notes, depending on the renderer and environment.  
<span class="para" id="25fd8eb"></span>To create a note, enclose it in `N&lt; >`   

```
Raku is multi-paradigmatic N<Supporting Procedural, Object Oriented, and Functional programming>
```
Which produces:  
> <span class="para" id="a5ed67d"></span>Raku is multi-paradigmatic <a id="Supporting_Procedural,_Object_Oriented,_and_Functional_programming" href="#fn_target_Supporting_Procedural,_Object_Oriented,_and_Functional_programming"><sup>[ 1 ]</sup></a>


<div id="Keyboard input"></div><div id="Keyboard_input"></div>

#### Keyboard input
<span class="para" id="b3ad94a"></span>To flag text as keyboard input enclose it in `K&lt; >`   

```
=output Enter your name: K&lt;John Doe>
```
Which is rendered like so:  
> 
```
  --- output --- 
Enter your name: John Doe
```

<div id="Replaceable"></div>

#### Replaceable
<span class="para" id="58bcb3d"></span>The `R&lt;>` markup instruction specifies that the contained text is a **replaceable item**, a placeholder, or a metasyntactic variable. It is used to indicate a component of a syntax or specification that should eventually be replaced by an actual value. For example:   

```
The basic C&lt;ln> command is: C&lt;ln> R&lt;source_file> R&lt;target_file>
```
This is rendered like so:  
> <span class="para" id="decb3ea"></span>The basic `ln` command is: `ln` **__source_file__** **__target_file__**


<div id="Terminal output"></div><div id="Terminal_output"></div>

#### Terminal output
<span class="para" id="c39cdc5"></span>To flag text as terminal output enclose it in `T&lt; >`   

```
=input T&lt;Enter your name:> John Doe
```
Which would be rendered:  
> 
```
  --- input --- 
Enter your name: John Doe
```


<div id="Unicode and HTML references"></div><div id="Unicode_and_HTML_references"></div>

#### Unicode and HTML references
<span class="para" id="d95b004"></span>Unicode codepoint names or numbers may be included in a RakuDoc document by enclosing them in `E&lt; >`. When `E&lt; >` encloses a number, it is treated as the Unicode value for the code point using binary, octal, decimal, or hexadecimal numbers specified in the Raku notations for explicitly based numbers. Numbers without an explicit base, as per Raku convention, are decimal.   
For example, each of the following:  

```
Raku makes considerable use of the E<171> and E<187> characters.

Raku makes considerable use of the E<0b10101011> and E<0b10111011> characters.

Raku makes considerable use of the E<0o253> and E<0o273> characters.

Raku makes considerable use of the E<0d171> and E<0d187> characters.

Raku makes considerable use of the E<0xAB> and E<0xBB> characters.
```
will yield the same rendering:  
> Raku makes considerable use of the « and » characters.

<span class="para" id="b7af1da"></span>It is also possible to specify Unicode codepoint **names**. Multiple codepoints separated by `,` will be considered as a single grapheme. So:   

```
E<LEFT-POINTING DOUBLE ANGLE QUOTATION MARK>
E<REGIONAL INDICATOR SYMBOL LETTER U, REGIONAL INDICATOR SYMBOL LETTER A> Ukraine
E<RIGHT-POINTING DOUBLE ANGLE QUOTATION MARK>
```
will be rendered as:  
> <span class="para" id="fc97552"></span>« 🇺🇦 Ukraine »

<span class="para" id="4845e71"></span>Since emojis are graphemes that can be described by Unicode, `E&lt; >` will also generate emojis. For example:   

```
E<WHITE SMILING FACE> Smiling face
```
will generate:  
> <span class="para" id="23ee874"></span>☺ Smiling face

Alternatively, HTML5 character references may be used. For example:  

```
Raku makes considerable use of the E<laquo> and E<raquo> characters.
```
also yields: Raku makes considerable use of the « and » characters.  
<span class="para" id="4d2f428"></span>The `E&lt;>` markup may be used for a list of characters separated by `;`   

```
Raku code often contains the E<0xff62;0xff63> bracketing characters to avoid using quotes.
```
This would yield: Raku code often contains the ｢｣ bracketing characters to avoid using quotes.  
<span class="para" id="e6a222d"></span>The `E&lt;>` code can also be specified with an alternate display text (before the entity specification and separated from it by a `|`). When an alternate text is specified, it is used if the specified entity cannot be represented by the renderer (*e.g.* if the renderer only supports ASCII).   
For example:  

```
Raku makes considerable use of the E&lt;left- |laquo> and E&lt;right-double-angle |raquo> characters.

Raku code often contains the E&lt;left- and right-corner |0xff62; 0xff63> bracketing characters
to avoid using quotes.
```
An ASCII-only renderer would then render those like so:  
> Raku makes considerable use of the left- and right-double-angle characters.  
Raku code often contains the left- and right-corner bracketing characters to avoid using quotes.


<div id="Verbatim text"></div><div id="Verbatim_text"></div>

#### Verbatim text
<span class="para" id="2719a1e"></span>The `V&lt;>` markup instruction treats its entire contents as being **verbatim**, disregarding every apparent markup instruction within it. For example:   

```
The B<V< V<> >> markup instruction disarms other codes
such as V< I<>, C<>, B<>, and M<> >.
```
<span class="para" id="9e7d677"></span>Note, however that the `V&lt;>` code only changes the way its contents are parsed, *not* the way they are rendered. That is, the contents are still wrapped and formatted like plain text, and the effects of any markup instructions surrounding the `V&lt;>` code are still applied to its contents. For example the previous example is rendered as:   
> <span class="para" id="faf6b25"></span>The ** V&lt;> ** markup instruction disarms other codes such as  I&lt;>, C&lt;>, B&lt;>, and M&lt;> .

<span class="para" id="2064eda"></span>Note that `C&lt;>` also by default keeps its contents verbatim; the difference between `C&lt;>` and `V&lt;>` is that `C&lt;>` always renders its contents to show that they are code (*e.g.* in a fixed-width font or a distinctive style), whilst `V&lt;>` uses the default text presentation for the current block.   
<span class="para" id="bace9b9"></span>The default behaviors of both `V&lt;>` and `C&lt;>` can be changed using a `=config` directive. For example:   

```
=config C :allow< B I U >
=config V :allow< N L X >
```

<div id="Indexing terms"></div><div id="Indexing_terms"></div>

#### Indexing terms
<span class="para" id="e5a9ab9"></span>Anything enclosed in an `X&lt;>` code is an **index entry**. The contents of the code are both formatted into the document and used as the (case-insensitive) index entry:   

```
An X&lt;array> is an ordered list of scalars indexed by number,
starting with 0. A X&lt;hash> is an unordered collection of scalar
values indexed by their associated string key.
```
You can specify an index entry in which the indexed text and the index entry are different, by separating the two with a vertical bar:  

```
An X&lt;array|arrays> is an ordered list of scalars indexed by number,
starting with 0. A X&lt;hash|hashes> is an unordered collection of
scalar values indexed by their associated string key.
```
In the two-part form, the presentation text comes before the bar, and the actual index entry comes after the bar and is case-sensitive.  
You can specify hierarchical index entries by separating indexing levels with commas:  

```
An X&lt;array|arrays, definition of> is an ordered list of scalars
indexed by number, starting with 0. A X&lt;hash|hashes, definition of>
is an unordered collection of scalar values indexed by their
associated string key.
```
You can specify two or more entries for a single indexed text, by separating the entries with semicolons:  

```
A X&lt;hash|hashes, definition of; associative arrays>
is an unordered collection of scalar values indexed by their
associated string key.
```
The indexed text can be empty, creating a "zero-width" index entry:  

```
X&lt;|puns, deliberate>This is called the "Orcish Maneuver"
because you "OR" the "cache".
```

<div id="Markup extras"></div><div id="Markup_extras"></div>

#### Markup extras
<span class="para" id="38278ff"></span>In order to allow for renderers to add customisable markup code, whilst at the same time abiding by the [naming rules](Naming rules), the markup instruction `M&lt; visible text | list-of-strings >` is defined as a *built-in* markup instruction.
<span class="para" id="25c16e2"></span>The **__visible text__** is shown in the text, and may be blank.   
<span class="para" id="fdb2fa0"></span>The **__list-of-strings__** is specified in the same way as the `X&lt;>` markup instruction for [indexing](Indexing entries). The semantics of the string is defined by the renderer. If a renderer does not recognize the list of strings, it is expected that the visible text will be rendered and marked in some way (such as with a border, or highlighted), and at least the first string will be made available in the same way as the `D&lt;>` markup instruction. It is also recommended that the first string is used by the renderer to identify the functionality being provided.   
<span class="para" id="d53d8c9"></span>For example, suppose a RakuDoc document is used to create part of a website in which a button is to be styled that accesses the API of a payment service (*e.g.* 'PayMe'). A developer can then create some functionality according to the rules of a RakuDoc renderer, and present it to the service point. In an HTML document, this might be coded as a ` <button class="PayMeApp" data-token="vendor-id" data-price="$0.99"> ` with a specific class and structure. The RakuDoc instruction might then be:   

```
To sample this marvellous libation M&lt;order now by PayMeApp|PayMeApp; vendor-id; $0.99>
```
An HTML renderer would the convert this to, for example,  

```
<p>To sample this marvellous libation
    <button class="PayMeApp" data-token="vendor-id" data-price="$0.99">
    order now by PayMeApp
    </button>
</p>
```

<div id="Implementor considerations"></div><div id="Implementor_considerations"></div>

## Implementor considerations
<span class="para" id="6759afa"></span>RakuDoc is intended to be a markup language for text oriented documentation, which will be rendered into output formats such as *HTML* or *epub*. It is expected that there may be several modules that will render RakuDoc.   
In order for a renderer to be compliant with the specification, it must:  
&nbsp;&nbsp;• render in some way all the built-in RakuDoc blocks and markup codes  
&nbsp;&nbsp;&nbsp;&nbsp;▹ where fallbacks have been specified, only the minimum fallback need be rendered  
&nbsp;&nbsp;• recognize and apply all the directives  
&nbsp;&nbsp;• provide some mechanism for a user to add custom blocks and markup codes  
&nbsp;&nbsp;• signal rendering errors in some way, unless such warnings have been explicitly silenced  
&nbsp;&nbsp;&nbsp;&nbsp;▹ rendering errors include unknown block/code types, as well as the use of unimplemented built-ins  
&nbsp;&nbsp;&nbsp;&nbsp;▹ the following signalling mechanisms are acceptable approaches to fulfil this requirement:  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;‣ writing error messages to STDERR  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;‣ writing error messages to a log file  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;‣ appending error messages to the end of the rendered output in a distinctive style that indicates they are errors  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;‣ rendering unhandled blocks or codes inline (in a distinctive style) and providing the associated error message in a pop-up that activates on hover or mouse-click  
<div id="Licence"></div>

## Licence
Artistic-2.0



<div id="Credits"></div>

## Credits
&nbsp;&nbsp;• Damian Conway (@thoughtstream)  
&nbsp;&nbsp;• Richard Hainsworth (@finanalyst)  
&nbsp;&nbsp;• Elizabeth Mattijen (@lizmat)  
&nbsp;&nbsp;• Aliaksandr Zahatski (@zag)  




----

## Footnotes
1<a id=".<fnTarget>" href="#Supporting_Procedural,_Object_Oriented,_and_Functional_programming"> |^| </a>Supporting Procedural, Object Oriented, and Functional programming



----

----

Rendered from ./rakudoc_v2_specification.rakudoc/rakudoc_v2_specification at 10:38 UTC on 2026-07-05

Source last modified at 10:35 UTC on 2026-07-05



----

----

## WARNINGS

1: Unknown or as yet undeclared alias ｢ SOFTWARE｣ in block ｢document｣ with heading ｢Aliases｣ over-riden by ｢this program ｣

2: Unknown or as yet undeclared alias ｢ COMPANY｣ in block ｢document｣ with heading ｢Aliases｣ over-riden by ｢our company ｣

3: Unknown or as yet undeclared alias ｢ OURTERMS｣ in block ｢document｣ with heading ｢Aliases｣ over-riden by ｢(Please visit our website) ｣

