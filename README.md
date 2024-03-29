LaTeX support for texnansi encoding (LY1)
===========================================

Installing
==========

Ordinarily, you don't have to do anything, since this package is likely
already present in your TeX distribution (TeX Live, MiKTeX).

But if you're on your own, then just recursively copy the directories
here to your texmf-local (or some) tree, preserving structure.  Then add
the map files *8y.map to your dvips setup and/or update the
configuration of other DVI drivers correspondingly.  You will have to
adjust URW vs. Adobe names as needed.  (In TeX Live, this is already
done in dvips35.map, etc., in conjunction with updmap, and the *8y.map
files here are not used.)

Usage
=====

The font encoding `LY1` corresponds to the `texnansi` encoding
originally used in the Y&Y TeX system.  There is also an option to
support the `ansinew` encoding that is the default encoding in
Microsoft Windows.

The main file is `ly1enc.def`. This contains the declaration of
the encoding. To use `texnansi` encoded fonts within LaTeX, 
typically you just need to add the line


	\usepackage[LY1]{fontenc}

to your document preamble.

An alternative to the above line is to use the `texnansi` package

	\usepackage{texnansi}

This will call the fontenc package, as above, to set up the LY1
encoding but then make further changes to the uppercase and
lowercase tables.  Note that LaTeX does not need these changes to
correctly uppercase text as its \MakeUppercase and \MakeLowercase
commands only require the uppercase and lower case codes of the
letters a-z.

So for most purposes it is better to use the call to fontenc
rather than use the `texnansi' package.  Then, if you pass the
file to a site using T1 (Cork) encoded fonts the line can more
easily be changed to

	\usepackage[T1]{fontenc}

The texnansi package has a package option `ansinew` which
modifies the LY1 encoding to refer to that MS Windows
encoding.  However you should only do this if your TeX system
can not reencode fonts to support T1 or LY1.  The `ansinew`
treatment of the left quote ` as a grave accent makes it
rather unsuitable for TeX use.  It also lacks several useful
glyphs.  However if you need to, you can do:

	\usepackage[ansinew]{texnansi}

The other possible reason for using the `texnansi` package
relates to the use of the lowercase table in the hyphenation
algorithm.  This is described below, but it is unfortunately
of necessity rather technical, and you may want to stop
reading at that point.

LY1 matches OT1 well enough that you can usually use it in that role
also.  You can `\input` texnansi if you want this to work exactly even
in plain TeX.

Having declared the LY1 encoding to LaTeX, LaTeX will need to
know the fonts that are available in this encoding.  This
distribution includes TeX metrics, font definition files and
map files for the common "Standard PostScript" fonts.

For each encoding LaTeX needs to know an `error font` which is
its fall back in the case that the font substitution system
can find nothing better to do.  ly1enc.def specifies that the
Error Font for LY1 is Adobe Times-Roman.  Any PostScript
interpreter must be able to provide Times-Roman, so it seems a
good choice as the error font.  Yet you may wish to change
this for any reason; just edit the lines

    \DeclareErrorFont{LY1}{ptm}{m}{n}{10}
    \DeclareFontSubstitution{LY1}{ptm}{m}{n}

at the top of ly1enc.def to refer to some other font set.  For example
if you have Y&Y TeX's "Lucida Bright" distribution you could change the
two occurrences of `{ptm}` to `{hlh}` to make LaTeX fall back on Lucida
Bright if all else fails.  Please follow the LPPL and do not
capriciously alter ly1enc.def in other ways.

For more information on LY1 encoding and see Y&Y's WWW page:

        <http://www.tug.org/yandy/usely1.htm>
        

A Note on Lowercase Codes and Hyphenation Tables
================================================

The lowercase table is used by TeX's hyphenation algorithm, so
in principle one would need to change the lowercase table to
match the encoding used.  However, it should not be changed
mid paragraph as all words, whatever font encoding they are
set in, are lowercased using the table current at the *end* of
the paragraph before being checked for hyphenation.  Because
of these limitations in TeX's hyphenation algorithm, LaTeX
does not support changing the lowercase table mid document, so
if the `texnansi` package is used then the `texnansi`
lowercase table will be used for all fonts (whether or not LY1
encoded) and conversely if it is not used, the T1 table will
be used, even for LY1 encoded fonts.  The reason why this
problem is not as serious as it may at first appear is that
the majority of letters (as opposed to ligatures, and symbols)
that occur in LY1 occur in the same place in T1.

The only letters that might possibly appear in a hyphenation
table that are in different positions in T1 and LY1 are:

```
\oe  (low position OT1 high new)
\ss  (low position OT1 high new)
\v{s}
\v{z}
\l
\i   OT1  (although *composites* such as \'\i are in T1 position
           so this will only affect languages (Turkish?) that use
           an unaccented \i)
\"{y}
```

It should be noted that for those symbols that occur twice in
LY1, the "high" copy is used if it coincides with the position
used in T1 (as in the case of \ae, \o) otherwise the "low"
copy will be used which will coincide with the OT1 position
(\oe, \ss).  (Unless the setup for ansinew is enabled in which
case the high position is always used.)

Fortunately most hyphenation files for Western European
languages (I checked French, German and Danish) duplicate all
the hyphenation patterns using these letters so that the
tables work with both T1 and OT1 (and hence LY1) encoded
fonts.  However if you are using a language that makes use of
say, \ss, but the hyphenation patterns just refer to \ss as
^^ff then you will need to copy the hyphenation file and edit
all occurrences of ^^ff to refer to the new position; either
^^19 (for texnansi) or ^^df (for ansinew).

The remaining letters that are in different positions, such as
s caron, may be dealt with similarly, although they are mainly
used in Eastern European languages.  The LY1 encoding does not
have as many glyphs for Eastern European languages as T1, so
for those languages most likely this encoding is not suitable.
(Of course TeX can produce all the accented letters required,
but the hyphenation algorithm can not work with such
constructed composites it can only work with composite letters
that actually occur in the font.)

It would be a good thing if hyphenation tables did not refer
to any explicit character positions using the ^^ab notation,
but rather always accessed characters by control sequences
such as \ss.  This would allow the remapping to be done via
inputting a file such as ly1enc.def rather than by editing the
hyphenation file.  However currently most of the publicly
available hyphenation tables unfortunately do "hard wire" the
T1 character codes in this way.


Copyright 1997 David Carlisle  
(Updated Feb. 2001 Walter Schmidt)  
(Updated Jun. 2010 Karl Berry)  
(Updated Oct. 2010 Karl Berry -- Times small caps faked as intended.)  
(Updated Jan. 2011 Karl Berry  
 -- missing map entries for ptmrr8ye and ptmr8yn.  
 -- irrelevant {phvl*,psyr,pzdr}.tfm removed.  
 -- relevant ptm{b,r}c8y.vf included, and corresponding ptm{b,r}c8y.tfm.  
 Thanks to Bruno Voisin for much research and debugging.  
)  
(Updated June 2022 David Carlisle )

All the files in this directory may be redistributed and/or modified
under the terms of the LaTeX Project Public License 
http://www.latex-project.org/lppl/
either version 1 of the License, or (at your option) any later version.
