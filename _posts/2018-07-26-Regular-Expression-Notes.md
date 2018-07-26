---
layout: single
title:  "Regular Expression Notes"
header:
  overlay_color: "#000"
  overlay_filter: "0.1"
  overlay_image: /
  cta_label: "Learn RE step by step"
  cta_url: "https://www.gnu.org/software/sed/manual/html_node/Regular-Expressions.html"
  caption: "Photo credit: [**_Jiayong Mo_**](https://alphagarden.github.io)"
classes: wide
date:   2018-07-26 10:19:01 -0600

excerpt: "A personal notes for Regular Expression learning"
categories: regexp 
---

#### Basic Regular Expression.(BRE)

*This following content is about [sed][sed-url] (Stream editor) and how to use regular expression syntax.*

*Regular expression matcher is greedy, matches are attempted from left to right, and if two or more matches are possible starting at the same character,  it selects the longest*

#### `*`

Matches a sequence of zero or more instances of matches for the preceding regular expression, which must be:

* an ordinary character.
* a special character preceded by \ 
* a grouped regular expression `\(regexp\)`
* a bracket expression. `\{\}`

#### `\+`

As `*`, but matches one or more. 

#### `\?`

As *, but only matches exactly zero or one. 

#### `\{i\}`

As *, but matches exactly `i` sequences ( *i*  is a decimal integer; for portability, keep it between 0 and 255 inclusive).

#### `\{i,j}\`

Matches between *i* and *j*, inclusive, sequences.

#### `\{i,}\`

Mathces more than or equal to i sequences.

#### `\(regexp\)`

Groups the inner regexp as a whole.

* Apply postfix operators, like `\(abcd\)*` will search zero or more whole sequences of 'abcd'.
* Use back references.

#### `.`

Matches any character, including newline. 

#### `^`

matches the null string at beginning of the pattern space, but ^ will acts as a special character only at the beginning of the regular expression or subexpression (that is, after`\(` or `\|`).  

#### `####  #### 

It is the same as `^`, but refers to end of pattern space. `$` also acts as a special character only at the end of the regular expression or subexpression(that is, before `\)` or `\|`).

#### `[list]`

Matches any single character in *list*. 

The characters `$`, `*`, `.`, `[`, and `\` are normally not special within list 

#### `[^list]` 

Matches any single character not in the *list*.

#### regexp1`\|` regexp2

Matches either `regexp1` or `regexp2`.

#### regexp1regexp2

Matches the concatenation of regexp1 and regexp2

#### `\digit` (back reference)

 Matches the digit-th `\(...\)` parenthesized subexpression in the regular expression. 

#### \n

Matches the new line characters.

#### \char

Matches char，where char is one of  `$`, `*`, `.`, `[`, `\`, or `^` .

#### Example

`abcdef`

Matches `abcdef`. 

`a*b`

Matches zero or more ‘a’s followed by a single ‘b’. For example, ‘b’ or ‘aaaaab’. 

`a\?b`

Matches `b` or `ab`. 

`a\+b\+`

Matches one or more ‘a’s followed by one or more ‘b’s: ‘ab’ is the shortest possible match, but other examples are ‘aaaab’ or ‘abbbbb’ or ‘aaaaaabbbbbbb’. 

`.*`

`.\+`

These two both match all the characters in a string; however, the first matches every string (including the empty string), while the second matches only strings containing at least one character. 

`^main.*(.*)`

This matches a string starting with ‘main’, followed by an opening and closing parenthesis. The `n`, `(’ and ‘)` need not be adjacent. 

`^#`

This matches a string beginning with ‘#’. 

`\\$`

This matches a string ending with a single backslash. The regexp contains two backslashes for escaping. 

`\$`

Instead, this matches a string consisting of a single dollar sign, because it is escaped. 

`[a-zA-Z0-9]`

In the C locale, this matches any ASCII letters or digits. 

`[^ tab]\+`

(Here tab stands for a single tab character.) This matches a string of one or more characters, none of which is a space or a tab. Usually this means a word. 

`^\(.*\)\n\1$`

This matches a string consisting of two equal substrings separated by a newline. 

`.\{9\}A$`

This matches nine characters followed by an ‘A’. 

`^.\{15\}A`

This matches the start of a string that contains 16 characters, the last of which is an ‘A’.

#### Extended Regular Expression(ERE)

In GNU `sed`, the only difference between *basic* and *extended* regular expressions is in the behavior of a new  special characters : `?`, `+` , parentheses`()`, brace `{}`, and `|`.

* For `BRE` syntax, these characters do not have special meaning unless prefixed prefixed backslash`\`
* For`ERE` syntax, these characters are special unless they are prefixed with backslash`\`

|Desired Pattern|BRE|ERE|
|---|---|---|
|literal '+' (plus sign)|* `$ echo 'a+b=c' > foo` `$ 'sed -n '/a+b/p' foo` `a+b=c`|`$ echo 'a+b=c' > foo` ` $ sed -E -n '/a\+b/p' foo ` `a+b=c `|
|one or more 'a' characters followed by 'b' (plus sign as special meta-character)|`$ echo aab > foo` ` $ sed -n '/a\+b/p'` ` foo aab`|`$ echo aab > foo` ` $ sed -E -n '/a+b/p' foo`  `aab`|



[sed-url]:(https://www.gnu.org/software/sed/manual/sed.html)	"SED"