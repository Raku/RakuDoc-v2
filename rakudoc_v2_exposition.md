
# RakuDoc Exposition

	Additional notes and examples for the RakuDoc specification


<div id="VERSION&nbsp;&nbsp;&nbsp;&nbsp;2.20.5"></div><div id="VERSION"></div>

## VERSION&nbsp;&nbsp;&nbsp;&nbsp;2.20.5

<div id="Documenting code vs documenting software"></div><div id="Documenting_code_vs_documenting_software"></div>

## Documenting code vs documenting software
<span class="para" id="542276f"></span>*[This section elaborates [the corresponding entry in the RakuDoc specification](rakudoc:rakudoc_v2_specification.rakudoc)]*   
RakuDoc is a markup language with simple instructions for simple tasks and more complex structures to suit larger projects. There is a clear distinction between documentation intended to help maintain and develop the software, and the visual presentation of information needed by a newcomer to understand how to use software.  
Consider the two ways in which documentation is used...  

<div id="Case 1: You are writing internal documentation to aid software design"></div><div id="Case_1:_You_are_writing_internal_documentation_to_aid_software_design"></div>

### Case 1: You are writing internal documentation to aid software design

```
    unit class Beginners;

    #| a variable to hold number of people
    has $.participants;

    #| data that is initially provided by default, but will be overwritten
    has $.new-participant = $=finish;

    ... # code

    =finish
    default data string for a new participant
```
<span class="para" id="8cba4e6"></span>A RakuDoc compliant editor or Integrated Design Environment will pick up the text following `#|` and put it into a pop-up menu whenever you select (*e.g.* hover over) a use of `$.participants`.   
<span class="para" id="705ce7e"></span>The `=finish` statement marks the last piece of code. Everything after it is treated as a string and placed in the `$=finish` variable.   

<div id="Case 2: You are writing external documentation to accompany some software"></div><div id="Case_2:_You_are_writing_external_documentation_to_accompany_some_software"></div>

### Case 2: You are writing external documentation to accompany some software
Consider the following short description:  

```
    =begin rakudoc
    =TITLE Tutorial about Flavorizing
    =SUBTITLE
    A short tutorial on Flavorizing your quarks

    =head Starting out

    In the section you will learn about how to add flavors to quarks produced
    by the I<Imagiton>.
    It goes without saying that these techniques should B<NOT> be carried out
    without a precocious child nearby; they will strain the imagination of
    an ossified adult.

    =head2 What is a flavour

    It is well-known (see the L<Wikipedia article|https://en.wikipedia.org/wiki/Flavour_(particle_physics)>
    that quarks N<a quark is a constituent of a hadron> come in six flavors, for example:
    =item Up
    =item Bottom
    =item Charm

    Z< Lots more text >
    =end rakudoc
```
<span class="para" id="e000f7a"></span>To indicate that all of the markup above is not Raku code, it is enclosed in a block labelled *rakudoc*. This is an example of the **delimited** form of the **rakudoc** block (so named because it is specified between `=begin` and `=end` directives).   
<span class="para" id="2421eba"></span>The `=TITLE`, `=SUBTITLE`, and `=SYNOPSIS` are *semantic blocks* containing standard information that can be searched by other tools. For all other purposes, they can be considered to the same as `=head` *blocks*.   
<span class="para" id="b1671fd"></span>A `=head` block begins a top-level heading. The `=head2` is a second level heading. These are examples of the **abbreviated** form of RakuDoc blocks. Any non-empty text lines following a heading specifier are collectively treated as the text of the heading.   
<span class="para" id="34c6d89"></span>The `=item` components are abbreviated blocks containing text entries that constitute the items of a list. There is no need to start or stop a list explicitly; the renderer will place each consecutive `=item` in a single list. The first non-`=item` paragraph or block will end the list.   
<span class="para" id="de0bd63"></span>Within the text there are several examples of inline markup instructions, which have the form **__Upper Unicode character__** ** &lt; ** ** > **. In the example above, we used: `I&lt;...>`, `B&lt;...>`, `N&lt;...>`, `L&lt;...>`, and `Z&lt;...>`. These are for Important, Basis, Note (a footnote), Link, and Zero (a comment) markup.   

<div id="Text-oriented RakuDoc"></div><div id="Text-oriented_RakuDoc"></div>

## Text-oriented RakuDoc

<div id="Block scope"></div><div id="Block_scope"></div>

### Block scope
<div id="Sections"></div>

#### Sections
<span class="para" id="542276f"></span>*[This section elaborates [the corresponding entry in the RakuDoc specification](rakudoc:rakudoc_v2_specification.rakudoc)]*   
<span class="para" id="9096f9d"></span>Providing metadata to a `=begin section` block definition is **not** the same as declaring metadata using a `=config` directive. Metadata in a config is provided to all matching blocks in the current block scope. Metadata in the `=begin section` declaration is only applied to the section itself, not to the blocks it contains.   
For example, suppose the following RakuDoc text was included in a source file  

```
    =begin section :delta(v2.3.2-, "removed due to conflict with chancellor's role")
    =head2 role Fool

    This role is especially important in authoritarian kingdoms to remind kings of their humanity.

    =end section
```
which might render as:  
> <span style="color: white; background-color: #d64161;"> for 2.3.2- removed due to conflict with chancellor's role</span>


<div id="role Fool"></div><div id="role_Fool"></div>

### role Fool
This role is especially important in authoritarian kingdoms to remind kings of their humanity.

<span class="para" id="09a2743"></span>If a user indicates to a renderer that they wish to view documentation valid in (for example) `v3.1.1` then, because `v3.1.1` is after `v2.3.2`, the section above – including the nested `=head2` block and the following plain paragraph – would not be rendered.   
<span class="para" id="4c9622c"></span>If however, a user indicates they wish to view documentation for `v2`, the renderer would render the section above, including an indication the section will be removed after `v2.3.2`.   

<div id="Counts and counters"></div><div id="Counts_and_counters"></div>

### Counts and counters

<div id="Each counter has a ***restart status***"></div><div id="Each_counter_has_a_restart_status"></div>

#### Each counter has a ***restart status***
<span class="para" id="d1d9051"></span>*[This section elaborates [the corresponding entry in the RakuDoc specification](Counts and counters)]*   
<span class="para" id="d75152c"></span>A `:restart` is useful to restart a specific counter that would otherwise continue. For example, to cause a specially prefixed series of tables to reset after a heading:   

```
1. Concept
    Table 1.1. List of requirements
    Table 1.2. List of resources
    Table 1.3. List of constraints

2. Implementation
    Table 2.1. Implementation schedule
    Table 2.2. Resource suppliers
    Table 2.3. Costings vs actual expenditure
```
To manually reset the second series of tables, you could specify them like so:  

```
=counter table1 :prefix<head1>

=numhead Concept
    =for numtable :caption< List of requirements >
    =for numtable :caption< List of resources >
    =for numtable :caption< List of constraints >

=numhead Implementation
    =counter table :restart
    =for numtable :caption< Implementation schedule >
    =for numtable :caption< Resource suppliers >
    =for numtable :caption< Costings vs actual expenditure >
```
<span class="para" id="ad1765a"></span>Note that, because a counter’s restart status persists until the next time it generates an enumeration, that `:restart` could be placed ***anywhere*** before the specific table whose numbering is to be restarted. For example, it could instead be specified immediately after the final table under the first heading:   

```
=numhead Concept
    =for numtable :caption< List of requirements >
    =for numtable :caption< List of resources >
    =for numtable :caption< List of constraints >
    =counter table :restart

=numhead Implementation
    =for numtable :caption< Implementation schedule >
    =for numtable :caption< Resource suppliers >
    =for numtable :caption< Costings vs actual expenditure >
```
<span class="para" id="6d802bf"></span>The `:!restart` variant is useful to allow “list-like” numbered blocks, such as `numitem` or `numdefn` to preserve their enumeration sequences despite intervening blocks that would normally restart those sequences. For example, to allow an aside to be placed in the middle of a numbered list of items, without breaking the numbered sequence:   

```
My nefarious plan is:

    1. Kidnap every being who knows the secret recipe for Nuka-Cola

    2. Demand a ransom of ONE MILLION DOLLARS

    (Note: need to work out how to get away cleanly from the exchange location)

    3. Profit!
```
You could achieve this with something like:  

```
=para My nefarious plan is:

=item Kidnap every being who knows the secret recipe for Nuka-Cola

=item Demand a ransom of ONE MILLION DOLLARS

=para (Note: need to work out how to get away cleanly from the exchange location)

=counter item :!restart
=item  Profit!
```
<span class="para" id="8b469e2"></span>The `:restart(INTEGER)` form of the metaoption is useful for specialized numbering schemes, such as starting from zero instead of `1`:

```
=counter head1 :restart(0)
```
...or to enumerate blocks in a non-monotonically increasing sequence for some reason:  

```
The values that can be ORed together in the bitset are:

    =counter item :restart(1)
    =numitem Read flag

    =counter item :restart(2)
    =numitem Write flag

    =counter item :restart(4)
    =numitem Execute flag

    =counter item :restart(8)
    =numitem Self-destruct flag (be careful with this one!)
```

<div id="A note about using ``:counter`` and ``:restart-after``/``restart-except-after`` together"></div><div id="A_note_about_using_:counter_and_:restart-after/restart-except-after_together"></div>

#### A note about using `:counter` and `:restart-after`/`restart-except-after` together
<span class="para" id="d0bb440"></span>It should be remembered, whereas a `=config NAME` directive changes the behaviour for the block named `NAME`, a `=counter NAME` directive changes the behaviour for the counter named ***`NAME`***, **not** for the block named `NAME`.   
Usually this distinction does not matter because, by default, blocks use the counter with their own name. But it can lead to seemingly non-obvious behaviours when blocks and counters with the same name are both configured at the same time.  
For example, consider the following:  

```
=config  table2 :counter< head2 >
=counter table2 :restart-after< table1 >
```
<span class="para" id="b865493"></span>After this configuration, one might expect `table2` blocks to restart at `1` after each `table1` block. But they do not. Instead, they restart after each `head1` block.   
<span class="para" id="612814e"></span>This is because the `table2` block is now using the counter named ***`head2`***, and that counter (by default) is restarted after every `head1` block. Or, to put it another way, it is true that the ***`table2`*** counter will be restarted after every `table1` block, but that will not matter because the `table2` block is no longer using the ***`table2`*** counter at all.   
<span class="para" id="f2a7d57"></span>This kind of apparent violation of expectation usually indicates design confusion on the part of the document’s author. For example, the author seems to indicate that they want each `table2` block to be numbered as if it were a `head2` *(Intent A)*, but they also want the numbering for `table2` blocks to be restarted after every `table1` block *(Intent B)*.   
<span class="para" id="3b44d45"></span>However, restarting after every `table1` block is *not* how the numbering of `head2` blocks works, so if that did happen then each `table2` block would ***not*** be being numbered as if it were a `head2` block. So *Intent B* intrinsically contradicts *Intent A*.   
<span class="para" id="173581c"></span>Fortunately, this kind of logical contradiction does not blow up the RakuDoc parser (or the entire universe!) because `=config` and `=counter` configure different elements (*i.e.* blocks and counters respectively), even when those elements happen to share the same name.   
<div id="Tables"></div>

### Tables

<div id="Visual description of simple tables"></div><div id="Visual_description_of_simple_tables"></div>

#### Visual description of simple tables

<div id="Valid tables"></div><div id="Valid_tables"></div>

##### Valid tables
<span class="para" id="542276f"></span>*[This section elaborates [the corresponding entry in the RakuDoc specification](rakudoc:rakudoc_v2_specification.rakudoc)]*   
Following are examples of valid tables...  

```
=begin table
    The Shoveller   Eddie Stevens     King Arthur's singing shovel
    Blue Raja       Geoffrey Smith    Master of cutlery
    Mr Furious      Roy Orson         Ticking time bomb of fury
    The Bowler      Carol Pinnsler    Haunted bowling ball
=end table
```

```
=table
    Constants           1
    Variables           10
    Subroutines         33
    Everything else     57
```

```
=for table
    mouse    | mice
    horse    | horses
    elephant | elephants
```

```
=table
    Animal | Legs |    Eats
    =======================
    Zebra  +   4  + Cookies
    Human  +   2  +   Pizza
    Shark  +   0  +    Fish
```

```
=table
        Superhero     | Secret          |
                      | Identity        | Superpower
        ==============|=================|================================
        The Shoveller | Eddie Stevens   | King Arthur's singing shovel
```

```
=begin table

                        Secret
        Superhero       Identity          Superpower
        =============   ===============   ===================
        The Shoveller   Eddie Stevens     King Arthur's
                                          singing shovel

        Blue Raja       Geoffrey Smith    Master of cutlery

        Mr Furious      Roy Orson         Ticking time bomb
                                          of fury

        The Bowler      Carol Pinnsler    Haunted bowling ball

=end table
```

```
=table
    X | O |
   ---+---+---
      | X | O
   ---+---+---
      |   | X
```

```
=table
    X   O
   ===========
        X   O
   ===========
            X
```

```
=begin table

foo
bar

=end table
```

<div id="Invalid tables"></div><div id="Invalid_tables"></div>

##### Invalid tables
Following are examples of invalid tables, and they should trigger an unhandled exception during parsing.  
&nbsp;&nbsp;• Using two or more different column separators in the same row is not allowed:  

```
=begin table
r0c0  +  r0c1  |  r0c3
=end table
```
&nbsp;&nbsp;• Using both visual and whitespace column separators in the same table is not allowed:  

```
=begin table
r0c0 +  r0c1 | r0c3
r1c0    r0c1   r0c3
=end table
```
&nbsp;&nbsp;• Specifying consecutive interior row separators without an intervening row is not allowed:  

```
=begin table
r0c0 |  r0c1
============
============
r1c0 |  r1c1
=end table
```

<div id="Unexpected tables"></div><div id="Unexpected_tables"></div>

##### Unexpected tables
Following are examples of valid tables that are probably intended to be two columns. However, the columns in these examples are not aligned well, so each will parse as a single-column table.  
&nbsp;&nbsp;• Unaligned columns with WS column separators:  

```
=begin table
r0c0    r0c1
    r1c0 r0c1
=end table
```
&nbsp;&nbsp;• Unaligned columns with visual column separators:  

```
=begin table
r0c0  |  r0c1
 r1c0 |r0c1
=end table
```

<div id="Procedural description of tables"></div><div id="Procedural_description_of_tables"></div>

#### Procedural description of tables
<span class="para" id="542276f"></span>*[This section elaborates [the corresponding entry in the RakuDoc specification](rakudoc:rakudoc_v2_specification.rakudoc)]*   
<span class="para" id="dab2760"></span>The fundamental idea is that a procedural `=table` sets up a semi-infinite 2D grid of cells, all of which are initially empty. The grid also tracks the fill position (*$POS*: the next empty cell to be filled), and the fill direction (*$DIR*: the direction to move *$POS* after filling; either *across* or *down*).   
Let’s see how it works...  
The following RakuDoc specification:  

```
=begin table :caption<Experimental data>
    =row :header
        =for cell :row-span(2)
        Date
        =for cell :column-span(3)
        Samples
        =for cell :row-span(2)
        Mean
    =row :header
        =cell I<Sample 1>
        =cell I<Sample 2>
        =cell I<Sample 3>
    =row
    =column
        =cell 2023-03-08
        =cell 2023-04-14
        =cell 2023-06-23
    =column
        =cell 0.4
        =cell 0.8
        =cell 0.2
    =column
        =cell 0.1
        =cell 0.6
        =cell 0.9
    =column
        =cell 0.3
        =cell 0.5
        =cell 0.0
    =column
        =cell 0.26667
        =cell 0.63333
        =cell 0.36667
    =row
        =for cell :label
        Mean:
        =cell 0.46667
        =cell 0.53333
        =cell 0.26667
        =cell 0.42222
=end table
 code
produces the following table:
in table :!toc :caption<Experimental data>
=row :header
    =for cell :row-span(2)
    Date
    =for cell :column-span(3)
    Samples
    =for cell :row-span(2)
    Mean
=row :header
    =cell I<Sample 1>
    =cell I<Sample 2>
    =cell I<Sample 3>
=row
=column
    =cell 2023-03-08
    =cell 2023-04-14
    =cell 2023-06-23
=column
    =cell 0.4
    =cell 0.8
    =cell 0.2
=column
    =cell 0.1
    =cell 0.6
    =cell 0.9
=column
    =cell 0.3
    =cell 0.5
    =cell 0.0
=column
    =cell 0.26667
    =cell 0.63333
    =cell 0.36667
=row
    =for cell :label
    Mean:
    =cell 0.46667
    =cell 0.53333
    =cell 0.26667
    =cell 0.42222
=end table

Let’s examine how that specification creates that layout, step by step.
Note that, in the following diagram, the various symbols have these meanings:
=begin code :lang<text>
    ┌───┐
    │   │ : An empty table cell
    └───┘
    ┌───┐
    │ → │ : The empty cell at $POS; $DIR is currently "across"
    └───┘
    ┌───┐
    │ ↓ │ : The empty cell at $POS; $DIR is currently "down"
    └───┘
    ┌───┐
    │+++│ : The most recently filled cell
    └───┘
    ┌───┐
    │###│ : A previously filled cell
    └───┘
```
And now the step-by-step explanation:  

```
    =begin table                   ┌───┬───┬───┬───┬───┬───┬┈┈
                                   │ → │   │   │   │   │   │
    (Create a new table grid       ├───┼───┼───┼───┼───┼───┼┈┈
     Default $POS is [0,0]         │   │   │   │   │   │   │
     Default $DIR is across)       ├───┼───┼───┼───┼───┼───┼┈┈
                                   │   │   │   │   │   │   │
                                   ├───┼───┼───┼───┼───┼───┼┈┈
                                   │   │   │   │   │   │   │
                                   ├───┼───┼───┼───┼───┼───┼┈┈
                                   │   │   │   │   │   │   │
                                   ├───┼───┼───┼───┼───┼───┼┈┈
                                   │   │   │   │   │   │   │
                                   ├───┼───┼───┼───┼───┼───┼┈┈
                                   ┊   ┊   ┊   ┊   ┊   ┊   ┊


    =row :header                   ┌───┬───┬───┬───┬───┬───┬┈┈
                                   │ → │   │   │   │   │   │
    (Previous action was not =cell ├───┼───┼───┼───┼───┼───┼┈┈
     So set $DIR to "across"       │   │   │   │   │   │   │
     and don't change $POS)        ├───┼───┼───┼───┼───┼───┼┈┈
                                   │   │   │   │   │   │   │
                                   ├───┼───┼───┼───┼───┼───┼┈┈
                                   │   │   │   │   │   │   │
                                   ├───┼───┼───┼───┼───┼───┼┈┈
                                   │   │   │   │   │   │   │
                                   ├───┼───┼───┼───┼───┼───┼┈┈
                                   │   │   │   │   │   │   │
                                   ├───┼───┼───┼───┼───┼───┼┈┈
                                   ┊   ┊   ┊   ┊   ┊   ┊   ┊



    =for cell :row-span(2)         ┌───┬───┬───┬───┬───┬───┬┈┈
    Date                           │+++│ → │   │   │   │   │
                                   │+++│───┼───┼───┼───┼───┼┈┈
    (Fill in 1x2 cells             │+++│   │   │   │   │   │
     starting from $POS            ├───┼───┼───┼───┼───┼───┼┈┈
     Then move $POS to             │   │   │   │   │   │   │
     first empty cell              ├───┼───┼───┼───┼───┼───┼┈┈
     in $DIR direction)            │   │   │   │   │   │   │
                                   ├───┼───┼───┼───┼───┼───┼┈┈
                                   │   │   │   │   │   │   │
                                   ├───┼───┼───┼───┼───┼───┼┈┈
                                   │   │   │   │   │   │   │
                                   ├───┼───┼───┼───┼───┼───┼┈┈
                                   ┊   ┊   ┊   ┊   ┊   ┊   ┊



    =for cell :column-span(3)      ┌───┬───────────┬───┬───┬┈┈
    Samples                        │###│+++++++++++│ → │   │
                                   │###├───┬───┬───┼───┼───┼┈┈
    (Fill in 3x1 cells             │###│   │   │   │   │   │
     starting from $POS            ├───┼───┼───┼───┼───┼───┼┈┈
     Then move $POS to             │   │   │   │   │   │   │
     first empty cell              ├───┼───┼───┼───┼───┼───┼┈┈
     in $DIR direction)            │   │   │   │   │   │   │
                                   ├───┼───┼───┼───┼───┼───┼┈┈
                                   │   │   │   │   │   │   │
                                   ├───┼───┼───┼───┼───┼───┼┈┈
                                   │   │   │   │   │   │   │
                                   ├───┼───┼───┼───┼───┼───┼┈┈
                                   ┊   ┊   ┊   ┊   ┊   ┊   ┊


    =for cell :row-span(2)         ┌───┬───────────┬───┬───┬┈┈
    Mean                           │###│###########│+++│ → │
                                   │###├───┬───┬───┤+++│───┼┈┈
    (Fill in 1x2 cells             │###│   │   │   │+++│   │
     starting from $POS            ├───┼───┼───┼───┼───┼───┼┈┈
     Then move $POS to             │   │   │   │   │   │   │
     first empty cell              ├───┼───┼───┼───┼───┼───┼┈┈
     in $DIR direction)            │   │   │   │   │   │   │
                                   ├───┼───┼───┼───┼───┼───┼┈┈
                                   │   │   │   │   │   │   │
                                   ├───┼───┼───┼───┼───┼───┼┈┈
                                   │   │   │   │   │   │   │
                                   ├───┼───┼───┼───┼───┼───┼┈┈
                                   ┊   ┊   ┊   ┊   ┊   ┊   ┊


    =row :header                   ┌───┬───────────┬───┬───┬┈┈
                                   │###│###########│###│   │
    (Find the next row at or       │###├───┬───┬───┤###│───┼┈┈
     below the current $POS        │###│ → │   │   │###│   │
     that has an empty cell        ├───┼───┼───┼───┼───┼───┼┈┈
     to the left of $POS, then     │   │   │   │   │   │   │
     move $POS to the leftmost     ├───┼───┼───┼───┼───┼───┼┈┈
     cell on that row, and set     │   │   │   │   │   │   │
     $DIR to "across")             ├───┼───┼───┼───┼───┼───┼┈┈
                                   │   │   │   │   │   │   │
                                   ├───┼───┼───┼───┼───┼───┼┈┈
                                   │   │   │   │   │   │   │
                                   ├───┼───┼───┼───┼───┼───┼┈┈
                                   ┊   ┊   ┊   ┊   ┊   ┊   ┊


    =cell Sample 1                 ┌───┬───────────┬───┬───┬┈┈
                                   │###│###########│###│   │
    (Fill in 1x1 cell at $POS      │###├───┬───┬───┤###│───┼┈┈
     then move $POS to             │###│+++│ → │   │###│   │
     the first empty cell          ├───┼───┼───┼───┼───┼───┼┈┈
     in $DIR direction)            │   │   │   │   │   │   │
                                   ├───┼───┼───┼───┼───┼───┼┈┈
                                   │   │   │   │   │   │   │
                                   ├───┼───┼───┼───┼───┼───┼┈┈
                                   │   │   │   │   │   │   │
                                   ├───┼───┼───┼───┼───┼───┼┈┈
                                   │   │   │   │   │   │   │
                                   ├───┼───┼───┼───┼───┼───┼┈┈
                                   ┊   ┊   ┊   ┊   ┊   ┊   ┊


    =cell Sample 2                 ┌───┬───────────┬───┬───┬┈┈
                                   │###│###########│###│   │
    (Fill in 1x1 cell at $POS      │###├───┬───┬───┤###│───┼┈┈
     then move $POS to             │###│###│+++│ → │###│   │
     the first empty cell          ├───┼───┼───┼───┼───┼───┼┈┈
     in $DIR direction)            │   │   │   │   │   │   │
                                   ├───┼───┼───┼───┼───┼───┼┈┈
                                   │   │   │   │   │   │   │
                                   ├───┼───┼───┼───┼───┼───┼┈┈
                                   │   │   │   │   │   │   │
                                   ├───┼───┼───┼───┼───┼───┼┈┈
                                   │   │   │   │   │   │   │
                                   ├───┼───┼───┼───┼───┼───┼┈┈
                                   ┊   ┊   ┊   ┊   ┊   ┊   ┊


    =cell Sample 3                 ┌───┬───────────┬───┬───┬┈┈
                                   │###│###########│###│   │
    (Fill in 1x1 cell at $POS      │###├───┬───┬───┤###│───┼┈┈
     then move $POS to             │###│###│###│+++│###│ → │
     the first empty cell          ├───┼───┼───┼───┼───┼───┼┈┈
     in $DIR direction)            │   │   │   │   │   │   │
                                   ├───┼───┼───┼───┼───┼───┼┈┈
                                   │   │   │   │   │   │   │
                                   ├───┼───┼───┼───┼───┼───┼┈┈
                                   │   │   │   │   │   │   │
                                   ├───┼───┼───┼───┼───┼───┼┈┈
                                   │   │   │   │   │   │   │
                                   ├───┼───┼───┼───┼───┼───┼┈┈
                                   ┊   ┊   ┊   ┊   ┊   ┊   ┊


    =row                           ┌───┬───────────┬───┬───┬┈┈
                                   │###│###########│###│   │
    (Move $POS down to the         │###├───┬───┬───┤###│───┼┈┈
     next row with an empty cell   │###│###│###│###│###│   │
     that's to the left of $POS    ├───┼───┼───┼───┼───┼───┼┈┈
     and to left-most empty cell   │ → │   │   │   │   │   │
     on that row)                  ├───┼───┼───┼───┼───┼───┼┈┈
                                   │   │   │   │   │   │   │
                                   ├───┼───┼───┼───┼───┼───┼┈┈
                                   │   │   │   │   │   │   │
                                   ├───┼───┼───┼───┼───┼───┼┈┈
                                   │   │   │   │   │   │   │
                                   ├───┼───┼───┼───┼───┼───┼┈┈
                                   ┊   ┊   ┊   ┊   ┊   ┊   ┊


    =column                        ┌───┬───────────┬───┬───┬┈┈
                                   │###│###########│###│   │
    (Previous action was not a     │###├───┬───┬───┤###│───┼┈┈
     =cell so set $DIR to "down",  │###│###│###│###│###│   │
     with no other effect)         ├───┼───┼───┼───┼───┼───┼┈┈
                                   │ ↓ │   │   │   │   │   │
                                   ├───┼───┼───┼───┼───┼───┼┈┈
                                   │   │   │   │   │   │   │
                                   ├───┼───┼───┼───┼───┼───┼┈┈
                                   │   │   │   │   │   │   │
                                   ├───┼───┼───┼───┼───┼───┼┈┈
                                   │   │   │   │   │   │   │
                                   ├───┼───┼───┼───┼───┼───┼┈┈
                                   ┊   ┊   ┊   ┊   ┊   ┊   ┊


    =cell 2023-03-08               ┌───┬───────────┬───┬───┬┈┈
    =cell 2023-04-14               │###│###########│###│   │
    =cell 2023-06-23               │###├───┬───┬───┤###│───┼┈┈
                                   │###│###│###│###│###│   │
    (Each =cell block              ├───┼───┼───┼───┼───┼───┼┈┈
     fills in a 1x1 cell           │+++│   │   │   │   │   │
     at $POS, then moves           ├───┼───┼───┼───┼───┼───┼┈┈
     $POS to the first empty       │+++│   │   │   │   │   │
     cell in $DIR direction)       ├───┼───┼───┼───┼───┼───┼┈┈
                                   │+++│   │   │   │   │   │
                                   ├───┼───┼───┼───┼───┼───┼┈┈
                                   │ ↓ │   │   │   │   │   │
                                   ├───┼───┼───┼───┼───┼───┼┈┈
                                   ┊   ┊   ┊   ┊   ┊   ┊   ┊



    =column                        ┌───┬───────────┬───┬───┬┈┈
                                   │###│###########│###│   │
    (Previous action was           │###├───┬───┬───┤###│───┼┈┈
     a =cell, so move $POS         │###│###│###│###│###│   │
     to the uppermost empty        ├───┼───┼───┼───┼───┼───┼┈┈
     cell in the first non-full    │###│ ↓ │   │   │   │   │
     column to the right,          ├───┼───┼───┼───┼───┼───┼┈┈
     and set $DIR to "down")       │###│   │   │   │   │   │
                                   ├───┼───┼───┼───┼───┼───┼┈┈
                                   │###│   │   │   │   │   │
                                   ├───┼───┼───┼───┼───┼───┼┈┈
                                   │   │   │   │   │   │   │
                                   ├───┼───┼───┼───┼───┼───┼┈┈
                                   ┊   ┊   ┊   ┊   ┊   ┊   ┊


    =cell 0.4                      ┌───┬───────────┬───┬───┬┈┈
    =cell 0.8                      │###│###########│###│   │
    =cell 0.2                      │###├───┬───┬───┤###│───┼┈┈
                                   │###│###│###│###│###│   │
    (Each =cell block              ├───┼───┼───┼───┼───┼───┼┈┈
     fills in a 1x1 cell           │###│+++│   │   │   │   │
     at $POS, then moves           ├───┼───┼───┼───┼───┼───┼┈┈
     $POS to the first empty       │###│+++│   │   │   │   │
     cell in $DIR direction)       ├───┼───┼───┼───┼───┼───┼┈┈
                                   │###│+++│   │   │   │   │
                                   ├───┼───┼───┼───┼───┼───┼┈┈
                                   │   │ ↓ │   │   │   │   │
                                   ├───┼───┼───┼───┼───┼───┼┈┈
                                   ┊   ┊   ┊   ┊   ┊   ┊   ┊



    =column                        ┌───┬───────────┬───┬───┬┈┈
    =cell 0.1                      │###│###########│###│   │
    =cell 0.6                      │###├───┬───┬───┤###│───┼┈┈
    =cell 0.9                      │###│###│###│###│###│   │
    =column                        ├───┼───┼───┼───┼───┼───┼┈┈
    =cell 0.3                      │###│###│+++│+++│+++│   │
    =cell 0.5                      ├───┼───┼───┼───┼───┼───┼┈┈
    =cell 0.0                      │###│###│+++│+++│+++│   │
    =column                        ├───┼───┼───┼───┼───┼───┼┈┈
    =cell 0.26667                  │###│###│+++│+++│+++│   │
    =cell 0.63333                  ├───┼───┼───┼───┼───┼───┼┈┈
    =cell 0.36667                  │   │   │   │   │ ↓ │   │
                                   ├───┼───┼───┼───┼───┼───┼┈┈
    (Rinse and repeat)             ┊   ┊   ┊   ┊   ┊   ┊   ┊


    =row                           ┌───┬───────────┬───┬───┬┈┈
                                   │###│###########│###│   │
    ($POS row is entirely empty    │###├───┬───┬───┤###│───┼┈┈
     so it's the first row with    │###│###│###│###│###│   │
     an empty cell to the left     ├───┼───┼───┼───┼───┼───┼┈┈
     of $POS, so stay on current   │###│###│###│###│###│   │
     row and move $POS to the      ├───┼───┼───┼───┼───┼───┼┈┈
     left-most empty cell in row;  │###│###│###│###│###│   │
     change $DIR to "across")      ├───┼───┼───┼───┼───┼───┼┈┈
                                   │###│###│###│###│###│   │
                                   ├───┼───┼───┼───┼───┼───┼┈┈
                                   │ → │   │   │   │   │   │
                                   ├───┼───┼───┼───┼───┼───┼┈┈
                                   ┊   ┊   ┊   ┊   ┊   ┊   ┊


    =for cell :label               ┌───┬───────────┬───┬───┬┈┈
    Mean:                          │###│###########│###│   │
    =cell 0.46667                  │###├───┬───┬───┤###│───┼┈┈
    =cell 0.53333                  │###│###│###│###│###│   │
    =cell 0.26667                  ├───┼───┼───┼───┼───┼───┼┈┈
    =cell 0.42222                  │###│###│###│###│###│   │
                                   ├───┼───┼───┼───┼───┼───┼┈┈
    (Fill in 1x1 cells,            │###│###│###│###│###│   │
     moving $POS each time         ├───┼───┼───┼───┼───┼───┼┈┈
     in the direction              │###│###│###│###│###│   │
     specified by $DIR)            ├───┼───┼───┼───┼───┼───┼┈┈
                                   │+++│+++│+++│+++│+++│ → │
                                   ├───┼───┼───┼───┼───┼───┼┈┈
                                   ┊   ┊   ┊   ┊   ┊   ┊   ┊


    =end table                     ┌───┬───────────┬───┐
                                   │###│###########│###│
    (Terminate grid-filling        │###├───┬───┬───┤###│
     then trim the table to        │###│###│###│###│###│
     the bounding box around       ├───┼───┼───┼───┼───┤
     all filled cells)             │###│###│###│###│###│
                                   ├───┼───┼───┼───┼───┤
                                   │###│###│###│###│###│
                                   ├───┼───┼───┼───┼───┤
                                   │###│###│###│###│###│
                                   ├───┼───┼───┼───┼───┤
                                   │###│###│###│###│###│
                                   └───┴───┴───┴───┴───┘
```
<div id="Formulae"></div>

### Formulae
<span class="para" id="542276f"></span>*[This section elaborates [the corresponding entry in the RakuDoc specification](rakudoc:rakudoc_v2_specification.rakudoc)]*   
<span class="para" id="7dfa942"></span>Implementors of renderers are ***not*** required to render the instructions (*e.g.* the LaTeX code) in a formula; it is acceptable for a renderer to render the contents of a `=formula` or `F&lt;>` in some other way than as a fully realized mathematical equation.   
For example, the following two formulae:  

```
We will use the identity: F<\sum \frac{1}{n^{2}} = \frac{\pi^{2}}{6}>

...where the value of pi can be inferred from Euler’s Identity:

=formula e^{i\pi}+1=0
```
...may either be rendered “accurately”:  
> <span class="para" id="04e2d41"></span>We will use the identity: `\sum \frac{1}{n^{2}} = \frac{\pi^{2}}{6}`   
...where the value of pi can be inferred from Euler’s Identity:  
<div id="Formula"></div>

## 
e^{i\pi}+1=0

... or may be converted to a suitable Unicode approximation:  
> We will use the identity: ∑ 1/n² = 𝜋²/6  
...where the value of pi can be inferred from Euler’s Identity:  
<span class="para" id="fb0dc17"></span>e<sup>iπ</sup> + 1 = 0

... or may even left as raw LaTeX (perhaps with some kind of visual marker indicating that it is unprocessed):  
> <span class="para" id="5e4272e"></span>We will use the identity: *__\sum \frac{1}{n^{2}} = \frac{\pi^{2}}{6}__*   
... where the value of pi can be inferred from Euler’s Identity:  
<span class="para" id="7b875a5"></span>*__e^{i\pi}+1=0__*

<div id="Citations"></div>

### Citations

<div id="Citation blocks"></div><div id="Citation_blocks"></div>

#### Citation blocks
<span class="para" id="542276f"></span>*[This section elaborates [the corresponding entry in the RakuDoc specification](rakudoc:rakudoc_v2_specification.rakudoc)]*   
<span class="para" id="9c0f445"></span>The full citation dataset for each individual reference work is specified in a `citation` block, as structured textual data in any of the following notations: CSL-JSON, CSL-YAML, CSL-Raku, RIS, MODS, BiBLaTeX, BibTeX, BibTeXML, or Medline/PubMed (XML or NBIB). All of these formats are widely used citation-data-interchange standards, except for CSL-Raku, which is just CSL-JSON/CSL-YAML data transposed into the native Raku syntax for a list of hashes.   
For example:  

```
=comment  Citation source data specified in CSL-Raku
=begin citation
    {
        note            => "Fields can be specified as key => value pairs"
        id              => "rakudoc-evo-chapter",
        type            => "chapter",
        title           => "The Evolution of RakuDoc",
        author          => [ { family => "Hainsworth", given => "Richard"   },
                             { family => "Mattijsen",  given => "Elizabeth" },
                             { family => "Conway",     given => "Damian"    },
                           ],
        editor          => [ { family => "Smith", given => "Jane" } ],
        container-title => "Modern Markup Systems",
        publisher       => "Butterfly Press",
        publisher-place => "Amsterdam",
        page            => "42-65",
        issued          => { date-parts => [[2024, 5, 12]] },
        ISBN            => "978-3-16-148410-0",
    },
    {
                   :note< Fields can also be specified as adverbial pairs >
                     :id< json-vs-xml-meta >
                   :type< article-journal >
                  :title< A Performance Analysis of JSON vs XML in Academic Metadata >
                 :author[ { family => "Doe", given => "John", ORCID => "0000-0001-2345-6789" } ]
        :container-title< Journal of Digital Humanities >
                 :volume( 12 )
                  :issue( 3 )
                   :page< 101-115 >
                    :DO 10.1000/jdh.2024.03.01 
                 :issued{ date-parts => [[2024]] }
    }
=end citation
```

```
=comment  This citation data is in CSL-JSON format
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
=comment  This citation data is CSL-YAML
=for citation
    - id:    starlight2024
      type:  article-journal
      title: "Asynchronous Concurrency: A Journey Through Raku Supplies"
      author:
          - family: Starlight
            given:  Orion
      container-title: "The Reactive Programmer's Quarterly"
      issued:
          date-parts: [[2024, 11]]
      volume: 12
      issue:  4
      page:   45-60
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
=comment  This citation is in BibTeXML format
=begin citation
    <bibtex:file xmlns:bibtex="http://bibtexml.sf.net/">
      <bibtex:entry id="mercurio2025">
        <bibtex:book>
          <bibtex:author>Silvia Mercurio</bibtex:author>
          <bibtex:title>The Morphological Beauty of Raku Grammars</bibtex:title>
          <bibtex:publisher>Butterfly Press</bibtex:publisher>
          <bibtex:year>2025</bibtex:year>
          <bibtex:month>March</bibtex:month>
          <bibtex:isbn>978-1-2345-6789-0</bibtex:isbn>
        </bibtex:book>
      </bibtex:entry>
    </bibtex:file>
=end citation
```

```
=comment  This is RIS data
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

```
=comment  This is MODS
=begin citation
    <mods xmlns="http://www.loc.gov/mods/v3" ID="Vasiliev26">
        <titleInfo>
            <title>Functional Purity in a Multi-Paradigm World</title>
        </titleInfo>
        <name type="personal">
            <namePart type="given">Elena</namePart>
            <namePart type="family">Vasiliev</namePart>
            <role>
                <roleTerm type="text">author</roleTerm>
            </role>
        </name>
        <originInfo>
            <publisher>Deep Space Coding Collective</publisher>
            <dateIssued>2026</dateIssued>
        </originInfo>
        <typeOfResource>text</typeOfResource>
        <genre>technical report</genre>
    </mods>
=end citation
```

```
=comment This citation is in PubMed/Medline NBIB format
=citation
    PMID- 39876543
    STAT- Publisher
    DA  - 2025/08/15
    TI  - The Semantic Elegance of Raku Grammars.
    BTI - The Semantic Elegance of Raku Grammars
    AU  - Mercurio S
    LA  - eng
    PT  - Book
    PB  - Butterfly Press
    SN  - 978-1-2345-9876-0
```

```
=comment This citation is in PubMed/Medline XML format
=citation
   <PubmedArticleSet>
     <PubmedArticle>
       <MedlineCitation Status="Publisher">
         <PMID Version="1">98765432</PMID>
         <Article PubModel="Print">
           <Journal>
             <ISSN IssnType="Print">1234-567X&lt;/ISSN>
             <JournalIssue>
               <Volume>14</Volume>
               <Issue>1</Issue>
               <PubDate>
                 <Year>2026</Year>
                 <Month>Jan</Month>
                 <Day>30</Day>
               </PubDate>
             </JournalIssue>
             <Title>International Journal of Perl and Raku</Title>
           </Journal>
           <ArticleTitle>Concurrent Processing in RakuDoc: A Performance Analysis.</ArticleTitle>
           <Pagination>
             <MedlinePgn>102-115</MedlinePgn>
           </Pagination>
           <AuthorList CompleteYN="Y">
             <Author ValidYN="Y">
               <LastName>Generic</LastName>
               <ForeName>A&lt;/ForeName>
             </Author>
             <Author ValidYN="Y">
               <LastName>Researcher</LastName>
               <ForeName>B</ForeName>
             </Author>
           </AuthorList>
           <Language>eng</Language>
           <PublicationTypeList>
             <PublicationType>Journal Article</PublicationType>
           </PublicationTypeList>
         </Article>
       </MedlineCitation>
     </PubmedArticle>
   </PubmedArticleSet>
```

<div id="Implied code and indentation"></div><div id="Implied_code_and_indentation"></div>

### Implied code and indentation
<span class="para" id="542276f"></span>*[This section elaborates [the corresponding entry in the RakuDoc specification](rakudoc:rakudoc_v2_specification.rakudoc)]*   
<span class="para" id="3d19298"></span>Each block creates a virtual margin (at the column before its leading `=`) and that virtual margin extends ***only*** to the end of the block (*i.e.* **not** to the start of the next named block).   
<span class="para" id="2e9bfca"></span>Care must be taken with *abbreviated* and *extended* blocks where the block ends at the next blank line.   

```
=begin rakudoc
This is an ordinary paragraph

    While this is not an ordinary paragraph;
    it is a code block.

    =head Mumble mumble

    Surprisingly, this is a code block
        (with fancy indentation too)

This is just an ordinary paragraph.

=end rakudoc
```
<span class="para" id="0682e98"></span>The *Surprisingly...* paragraph is a code block because each block only sets the virtual margin within that block, and the `=head` block terminates at the blank line immediately after the `=head` line, and before the apparent code block begins. At which point, the virtual margin reverts to the previous virtual margin set by the surrounding `=begin rakudoc`...`=end rakudoc` block.   
The current virtual margin terminates at the end of the block whose opener set it, and not at the start of the next block that sets a virtual margin. In other words, the virtual margin is a local feature of each individual block, not a collective feature of the entire document, and therefore a block’s virtual margin always ends at the end of the block itself (and reverts at that point to the virtual margin of the surrounding block, if any).  
Which means:  

```
=begin rakudoc
This is an ordinary paragraph

    While this is not
    This is a code block

    =head Mumble mumble

    This IS also a code block
       because the preceding =head's virtual margin ended
       at the end of the preceding =head block, which was
       at the preceding blank line. So the virtual margin for
       this implicit block is the virtual margin of the surrounding
       C<=begin rakudoc ... =end rakudoc> block. This block is indented
       relative to that C<=rakudoc block>'s virtual margin, so it's
       a code block.

This is a para block
    (with fancy indentation too, because the indentation of first line alone
     determines whether an implicit block is a C<=para> or a C<=code> block,
     and all subsequent lines, regardless of their indentation, become part
     of that block ... until the end of the implicit block, which occurs at
     the next blank line or the next explicit RakuDoc block or directive.)

=end rakudoc
```
<span class="para" id="6172220"></span>And if, instead, you wanted the block straight after the `=head` to be an implicit paragraph block (*i.e.* not an implicit code block), you'd write it:   

```
=begin rakudoc
This is an ordinary paragraph

    While this is not
    This is a code block

    =begin section

    =head Mumble mumble

    This is a para block, NOT a code block
       because the virtual margin for this implicit block
       is the virtual margin of the surrounding
       =begin section ... =end section block.
       The first line of this implicit block is NOT indented
       relative to that =section block's virtual margin,
       so it's a para block.

    =end section

This is a para block too...because its first line is not indented
   relative to the virtual margin of the current =begin rakudoc ... =end rakudoc block.

=end rakudoc
```
<div id="Metadata"></div>

## Metadata

<div id="Instructions with side effects"></div><div id="Instructions_with_side_effects"></div>

### Instructions with side effects

<div id="Display and metadata"></div><div id="Display_and_metadata"></div>

#### Display and metadata
<span class="para" id="542276f"></span>*[This section elaborates [the corresponding entry in the RakuDoc specification](rakudoc:rakudoc_v2_specification.rakudoc)]*   
Markup codes may contain only display text or may contain a mix of display and meta information. This section clarifies these groups.  
<span class="para" id="a9aa84f"></span>If a markup code may contain both display text and meta information, the character `|` is used to delimit the two components. In order to use `|` inside a display text, it must be escaped (`\|`) or specified as an entity (`E&lt;VERTICAL LINE>`).   

<div id="Display only codes"></div><div id="Display_only_codes"></div>

##### Display only codes
<span class="para" id="15e2443"></span>The following codes may *only* contain display text:   

```
B&lt;  DISPLAY-TEXT  >
C&lt;  DISPLAY-TEXT  >
H&lt;  DISPLAY-TEXT  >
I&lt;  DISPLAY-TEXT  >
J&lt;  DISPLAY-TEXT  >
K&lt;  DISPLAY-TEXT  >
N&lt;  DISPLAY-TEXT  >
O&lt;  DISPLAY-TEXT  >
R&lt;  DISPLAY-TEXT  >
S&lt;  DISPLAY-TEXT  >
T&lt;  DISPLAY-TEXT  >
U&lt;  DISPLAY-TEXT  >
V&lt;  DISPLAY-TEXT  >
W&lt;  DISPLAY-TEXT  >
```

<div id="Metadata and (optional) display text"></div><div id="Metadata_and_(optional)_display_text"></div>

##### Metadata and (optional) display text
The following codes may (and, in some cases, must) contain both a display text and some kind of metadata.  

```
A&lt;  DISPLAY-TEXT   |  METADATA=ALIAS-NAME            >
D&lt;  DISPLAY-TEXT   |  METADATA=SYNONYMS              >
Δ&lt;  DISPLAY-TEXT   |  METADATA=VERSION-ETC           >
E&lt;  DISPLAY-TEXT   |  METADATA=HTML/UNICODE-ENTITIES >
F&lt;  DISPLAY-TEXT   |  METADATA=LATEX-FORM            >
L&lt;  DISPLAY-TEXT   |  METADATA=TARGET-URI            >
M&lt;  DISPLAY-TEXT   |  METADATA=WHATEVER              >
P&lt;  DISPLAY-TEXT   |  METADATA=REPLACEMENT-URI       >
X&lt;  DISPLAY-TEXT   |  METADATA=INDEX-ENTRY           >
```
<span class="para" id="0c67979"></span>The markup codes `A`, `E`, `F`, `L`, and `P` may be specified **without** a display text, in which case the specification permits the renderer to provide an automatic rendering if the metadata component fails to produce a renderable result. If an explicit display text is provided (*i.e.* to the left of a `|`), that text overrides the default alternate rendering.   

<div id="Metadata only"></div><div id="Metadata_only"></div>

##### Metadata only

```
Q&lt;  METADATA=CITATION REFERENCES >
Z&lt;  METADATA=COMMENT >
```
<span class="para" id="4b66443"></span>The quotation code `Q&lt;> ` has a semicolon list akin to a metadata list, which is replaced in the rendered text with properly styled citations.   
<span class="para" id="8a3aa09"></span>The comment code `Z&lt;> ` is intended not to have a display text so the contents of a comment can be considered as pure metadata.   

<div id="Summary of RakuDoc syntax"></div><div id="Summary_of_RakuDoc_syntax"></div>

## Summary of RakuDoc syntax

<div id="Directives"></div>
<div>Directives</div> | **Directive** | **Specifies** |
| :---: | :---: |
 | `=alias` | Define a RakuDoc macro |
 | `=begin` | Start of an explicitly terminated block |
 | `=column` | Start a new column in a procedural table |
 | `=config` | Block-scoped modifications to a block or markup instruction |
 | `=counter` | Block-scoped reconfiguration of a counter |
 | `=document` | Change the default for document level options |
 | `=end` | Explicit termination of a `=begin` block |
 | `=finish` | No ambient blocks after this point |
 | `=for` | Start of an implicitly (blank-line) terminated block |
 | `=place` | Place into the document some text loaded from, or generated by, a URI |
 | `=row` | Start a new row in a procedural table |

<div id="Blocks"></div>
<div>Blocks</div> | **Block typename** | **Specifies** |
| :---: | :---: |
 | `=cell` | Contents of a table cell, only valid in a procedural table context |
 | `=citation` | Specify a list of citation datasets in one of several standard citation markup languages |
 | `=code` | Verbatim pre-formatted sample source code |
 | `=input` | Pre-formatted sample input |
 | `=output` | Pre-formatted sample output |
 | `=comment` | Content to be ignored by all renderers |
 | `=head` | First-level heading |
 | `=headN` | Nth-level heading |
 | `=defn` | Definition of a term |
 | `=item` | First-level list item |
 | `=itemN` | Nth-level list item |
 | `=nested` | Nest block contents within the current context |
 | `=para` | Ordinary paragraph |
 | `=rakudoc` | No "ambient" blocks inside |
 | `=section` | Defines a lexical scope within the document |
 | `=pod` | Legacy equivalent to `=rakudoc` (use is deprecated) |
 | `=table` | Visual or procedural table |
 | `=formula` | Render content as a LaTeX formula |
 | `=data` | Raku data section |
 | `=NAMED` | Semantic blocks (`=SYNOPSIS`, `=TITLE`, *etc.*) |
 | `=CustomName` | User-defined block (must have mixed-case name) |

<div id="Metadata_options"></div>
<div>Metadata options</div> | **Metadata option** | **Blocks applied to** | **Description** |
| :---: | :---: | :---: |
 | `:allow` | `=code` | Allow specific mark-up codes in otherwise verbatim blocks |
 | &nbsp; | `=input` | &nbsp; |
 | &nbsp; | `=output` | &nbsp; |
 | &nbsp; | `=table` | &nbsp; |
 | `:id` | all blocks | Specify the anchor for a block |
 | `:toc` | `=para` | Include content or caption in the Table of Contents |
 | &nbsp; | `=nested` | &nbsp; |
 | &nbsp; | `=code` | &nbsp; |
 | &nbsp; | `=input` | &nbsp; |
 | &nbsp; | `=output` | &nbsp; |
 | &nbsp; | `=table` | &nbsp; |
 | &nbsp; | `=formula` | &nbsp; |
 | &nbsp; | `=place` | &nbsp; |
 | `:!toc` | SEMANTIC block | Do not include content or caption in the Table of Contents |
 | &nbsp; | Custom block | &nbsp; |
 | &nbsp; | `=headN` | &nbsp; |
 | &nbsp; | `=numheadN` | &nbsp; |
 | `:headlevel` | All blocks | Override the automatic level of the block type within a Table of Contents |
 | &nbsp; | (but typically for para-ish blocks) | &nbsp; |
 | `:caption` | SEMANTIC block | Caption to be associated with a block in the Table of Contents |
 | &nbsp; | Custom block | &nbsp; |
 | &nbsp; | `=nested` | &nbsp; |
 | &nbsp; | `=code` | &nbsp; |
 | &nbsp; | `=input` | &nbsp; |
 | &nbsp; | `=output` | &nbsp; |
 | &nbsp; | `=table` | &nbsp; |
 | &nbsp; | `=formula` | &nbsp; |
 | &nbsp; | `=place` | &nbsp; |
 | `:hidden` | SEMANTIC block | Remove block from rendered text and from Table of Contents (use `P&lt;semantic: ...>` to inject elsewhere) |
 | `:header` | `=table` (procedural semantics only) | Specify rows, columns, or cells to be rendered as column headers in a table |
 | &nbsp; | `=cell` | &nbsp; |
 | &nbsp; | `=row` directive | &nbsp; |
 | &nbsp; | `=column` directive | &nbsp; |
 | `:label` | `=table` (procedural semantics only) | Specify rows, columns, or cells to be rendered as labels (row headers) in a table |
 | &nbsp; | `=cell` | &nbsp; |
 | &nbsp; | `=row` directive | &nbsp; |
 | &nbsp; | `=column` directive | &nbsp; |
 | `:align<ALIGNMENTS>` | `=table` (procedural semantics only) | Specify how cell contents are to be vertically and/or horizontally aligned |
 | &nbsp; | `=cell` | &nbsp; |
 | &nbsp; | `=row` directive | &nbsp; |
 | &nbsp; | `=column` directive | &nbsp; |
 | `:row-span(HEIGHT)` | `=cell` | Specify how many rows a single cell should span in a procedural table |
 | `:column-span(WIDTH)` | `=cell` | Specify how many columns a single cell should span in a procedural table |
 | `:span(WIDTH, HEIGHT)` | `=cell` | A shorthand for `:column-span(WIDTH) :row-span(HEIGHT)` |
 | `:delta` | `=table` | Developer information associated with a block |
 | &nbsp; | `=formula` | &nbsp; |
 | &nbsp; | `=nested` | &nbsp; |
 | &nbsp; | `=code` | &nbsp; |
 | &nbsp; | `=input` | &nbsp; |
 | &nbsp; | `=output` | &nbsp; |
 | &nbsp; | `=headN` | &nbsp; |
 | &nbsp; | `=numheadN` | &nbsp; |
 | &nbsp; | SEMANTIC block | &nbsp; |
 | &nbsp; | Custom block | &nbsp; |
 | `:bullet<CHAR>` | `=item` | Replace the default bullet with one or more characters, or entities specified with `E&lt; >` |
 | &nbsp; | `=itemN` | &nbsp; |

<div id="Markup_instructions"></div>
<div>Markup instructions</div> | **Markup instruction** | **Specifies** |
| :---: | :---: |
 | `A&lt;...|...>` | Alias to be replaced by contents of the specified `=alias` directive (`A&lt;alt text | aliasname>`) |
 | `B&lt;...>` | Basis/focus of sentence (typically rendered bold) |
 | `C&lt;...>` | Code (typically rendered fixed-width) |
 | `D&lt;...>` | Definition inline (`D&lt;term being defined | synonym1; synonym2>`) |
 | `Δ&lt;...|...;...>` | Delta note (`Δ&lt;visible text | version; notification text>`) |
 | `E&lt;...|...;...`> | Entity (HTML or Unicode) description (`E&lt;entity1; entity2; multi,glyph;...`>) |
 | `F&lt;...|...>` | Inline content for a formula (`F&lt;alt text | LaTeX notation>`) |
 | `G&lt;...>` | *(This markup code is not yet defined, but is reserved for future use)* |
 | `H&lt;...>` | High text (typically rendered superscript) |
 | `I&lt;...>` | Important content (typically rendered in italics) |
 | `J&lt;...>` | Junior text (typically rendered subscript) |
 | `K&lt;...>` | Keyboard input (typically rendered fixed-width) |
 | `L&lt;...|...>` | Link (`L&lt;display text | destination URI>`) |
 | `M&lt;...|..,..;...>` | Markup extra (`M&lt;display text | functionality; param,sub-type;...>`) |
 | `N&lt;...>` | Note (not rendered inline, but made visible in some way: footnote, sidenote, pop-up, *etc.*)) |
 | `O&lt;...>` | Struck-out content |
 | `P&lt;...|...>` | Placement link (`P&lt; alt text | source URI >`) |
 | `Q&lt;...>` | Marker citing one or more bibliographic datasets via identifiers with possible text annotations (`Q&lt; ID1 ; ID2, text; etc. >`) |
 | `R&lt;...>` | Replaceable component or metasyntax |
 | `S&lt;...>` | Space characters to be preserved |
 | `T&lt;...>` | Terminal output (typically rendered fixed-width) |
 | `U&lt;...>` | Unusual content (typically rendered with underlining) |
 | `V&lt;...>` | Verbatim (internal markup instructions ignored) |
 | `W&lt;...>` | Weighty content (typically rendered with small caps) |
 | `X&lt;...|..,..;...>` | Index entry (`X&lt; display text | entry,subentry ; ...>`) |
 | `Y&lt;...>` | *(This markup code is not yet defined, but is reserved for future use)* |
 | `Z&lt;...>` | Zero-width comment (contents never rendered) |

<div id=":form_components"></div>
<div>:form components</div> | **Field** | **Meaning** | **`:lc` or informal shortcut** |
| :---: | :---: | :---: |
 | `%A` | enumeration value as an ASCII capital alpha | `%a` |
 | `%B` or `%ব` | enumeration value in Bengali numerals *(if supported)* | `%b` *(no effect)* |
 | `%C` | caption *(if any)* | `%c` |
 | `%D` | block’s data | `%d` |
 | `%H` or `%ह` | enumeration value in Hindu numerals *(if supported)* | `%h` *(no effect)* |
 | `%J` or `%業` | enumeration value in Japanese numerals *(if supported)* | `%j` or `%並` |
 | `%N` | enumeration value as a cardinal number | `%n` |
 | `%R` | enumeration value in Roman numerals *(if supported)* | `%r` |
 | `%T` | the name of the block’s `num`-less type in Titlecase | `%t` |
 | `%Z` or `%大` | enumeration value in Chinese numerals *(if supported)* | `%z` or `%小` |
 | `%%` | a literal `%` *(only required if literal `%` is followed by a letter)* |  |
 | `%:` | a literal `:` *(only required if literal `:` is followed by a letter)* |  |  |

<div id="Options_for_form_fields"></div>
<div>Options for form fields</div> | **Field option** | **Meaning** |
| :---: | :---: |
 | `:ord` | use ordinal instead of cardinal number *(if available in requested language)* |
 | `:lc` | use lowercase or informal notation *(if any)* |
 | `:uc` | use uppercase or formal notation *(if any)* |
 | `:tc` | use titlecase *(uppercase first character only)* |
 | `:tclc` | use titlecase for first character, lowercase for the rest |
 | `:sc` | use titlecase for first character of each sentence, preserve ALLCAPS words, lowercase for the rest |
 | `:pc` | use titlecase for first character of each word, preserve ALLCAPS words, lowercase for the rest |
 | `:lang<xx>` | use numbering and/or ordinal system for ISO 639-1 language **__xx__** *(if supported)* |  |

<div id="Options_related_to_citations"></div>
<div>Options related to citations</div> | **Block or directive** | **Option** | **Meaning** |
| :---: | :---: | :---: |
 | `=citation` | `:load<URL>` | Import citation data from the specified URL |
 | `=citation` | `:category<CatName(s)>` | Include the block’s citation data in the specified category or categories |
 | `=document` | `:citation-style<name or URL>` | When rendering, format all citation lists using the CSL style format specified by the name or by the URL |
 | `=document` | `:citation-locale<tag>` | When rendering, format all citation lists using the CSL locale conventions selected by the specified IETF BCP 47 language tag |
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

----

Rendered from ./rakudoc_v2_exposition.rakudoc/rakudoc_v2_exposition at 10:38 UTC on 2026-07-05

Source last modified at 10:26 UTC on 2026-06-10

