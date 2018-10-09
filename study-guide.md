# Mastering Regular Expressions

## Introducing Regular Expressions

### What Are Regular Expressions?

+ "Pattern-matching" language
+ Generalize what to look for, then match any characters that fit that description
+ Commonly seen in:
    + Web forms
    + Find-and-replace features
    + Parsing logs or any kind of text

### Tools

+ Where do we write regex?
    + In programming languages (Perl, Java, .NET, etc.; most support some kind of regex)
    + With text processing applications (`sed`, `awk`, etc.)
    + In other applications (InDesign)
+ How can we test what we write?
    + Online regex testing websites:
        + [RegExr](https://regexr.com/)
        + [Regex101](https://regex101.com/)
        + [Regex Pal](https://www.regexpal.com/)


### The Regular Expressions Engine

+ Matches the regex given to the left-most match in the provided text
+ There is much more than a single engine
+ Most engines are based on one of two algorithms:
    + Nondeterministic Finite Automaton
        + The regex expression can reference back to earlier in the expression
            + `\d{1,3}`: The `{1,3}` references back to the `\d`
        + Seen in:
            + Perl
            + Python
            + `sed`
    + Deterministic Finite Automaton
        + The regex expression _cannot_ reference itself later in the expression
            + `\d{1,3}` would fail, since the `{1,3}` references the `\d` before it 

### Standards

+ IEEE POSIX standards:
    + SRE: Simple Regular Expressions; deprecated for BRE
    + BRE: Basic Regular Expressions
    + ERE: Extended Regular Expressions
    + ERE builds off BRE:
        + Added repetition and alternation characters (`+`, `?`, `|`)
        + In BRE, `{ }` and `( )` metacharacters must be written as `\{ \}` and `\( \)`
    + Uses `[:digit:]` "bracket expressions" versus `\d` character classes
+ Perl Compatible Regular Expressions
    + Inspiration for Java, .NET, Python regex
    + Supports some Python regex implementations
    + Perl does not use all PCRE standards
+ Examples with `grep`:
    + Match IP addresses in `access-log` file with PCRE (`-P`):

        $ grep -P '\d{1,3}\.\d{1,3}\.\d{1,3}\.\d{1,3}' access-log

    + Match IP address in `access-log` file with ERE (`-E`):

        $ grep -E '[[:digit:]]{1,3}\.[[:digit:]]{1,3}\.[[:digit:]]{1,3}\.[[:digit:]]{1,3}' access-log

## Basic Regular Expressions

### Literal Pattern Matching

+ Regex isn't all complicated token and metacharacters
+ Literal, plain text can be part of the expression
+ Be conscious of using a metacharacter when you want a literal character

### Matching Characters and Words

+ `\w`: Match "word" characters (A-Z, a-z, 0-9)
+ `\W`: Match non-word characters (anything that is not A-Z, a-z, 0-9)
+ Encase characters in square brackets to provide a range or list:
    + `[a-z]`: Match any letter between `a` and `z`, lowercase only
    + `[C-K]`: Match any letter between `C` and `K`, uppercase only
    + `[ACK]`: Match either `A`, `C`, or `K`

### Matching Digits

+ `\d`: Match any digit, 0-9
+ `\D`: Match anything that is not a digit (0-9)
+ Encase in square brackets to provide a range or list:
    + `[0-9]`: Match any digit between `0` and `9`
    + `[4-7]`: Match any digit between `'4` and `7` (`4`, `5`, `6`, `7`)
    + `[347]`: Match either `3`, `4`, or `7`

### Matching Whitespace

+ `\s`: Match any whitespace characters (tab, space, newline, carriage return)
+ `\S`: Match any non-whitespare characters
+ `\t`: Match any tabs
+ `\n`: Match any newlines
+ `\r`: Match any carriage returns

## More Regular Expressions

### Location

+ `^`: Match start of line
+ `$`: Match end of line
+ `^\s+$`: Match blank line

### Boundaries

+ `\b`: Does not match anything, but marks a boundary; at the start of an expression it ensures the previous token is not a word character (`\w`), while at the end of an expression, it ensures the next token is not a word character
+ `\B`: Does not match anything, but marks a boundary; at the end of an expression it ensures the previous token is not a non-word character (`\W`), while at the end of an expression, it ensures the next token is not a non-word character
+ `\< ... \>`: Some older programs (such as Vim) use `\<` and `\>` to mark boundaries; these work as boundaries against both word and non-word characters (`\w` and `\W`)

### Alternation

+ `|`: "Or"; match one of the provided subexpressions; there can be more than two subexpressions
    + `(NJ|PA)`: Match `NJ` or `PA`

### Repetition

+ `?`: Make preceding token optional; can be an individual character or subexpression contained in a group
+ `+`: Repeat preceding token or subexpression one or more times
+ `*`: Repeat preceding token or subexpression zero or more times

### Possessive Quantifiers

+ `.`: Wildcard; match any single character
+ When using repetition (`+`, `*`) alongside the `.` metacharacter, the regex engine will try to match as much as possible
    + "Greedy" or "possessive"
+ Use `?` after the quantifier to force the engine to match as few matches as possible
    + "Ungreedy" or "lazy"

## Classes, Groups, and Backreferences


### More Character Classes

+ `[...]`: Anything encased in square brackets is a character class
    + Commonly used for ranges `[a-zA-Z0-9]`
+ `[^...]`: Negated character class; anything encased in square brackets with a caret (`^`) cannot be matched
+ XML, XPath, .NET, and JGSoft regex only:
    + Subtract from ranges: `[A-Z-[N]]` (matched `A-M`, `O-Z`; does not match `N`)

### Backreferences

+ `( ... )`: Create a capturing group
    + Capturing groups are named in their numerical order
+ `\#`: Reference a capturing group, where `#` is the number of the group
+ In `(H2) ... \1`, `(H2)` matches `H2`, while `\1` also matches `H2`

### Named Groups

+ Give backreferences names
+ Python:
    + Set named group with:
        + `(?P<ID> ... )`
    + Reference named group with:
        + `(?P=ID)`
+ .NET:
    + Set named group with:
        + `(?<tag> ... )`
        + `(?'tag' ... )`
    + Reference named group with:
        + `\k<tag>`
        + `\k'tag'`
+ PCRE:
    + Set named group with:
        + `(?P<ID> ... )`
        + `(?<tag> ... )`
        + `(?'tag' ... )`
    + Reference named group with:
        + `(?P=ID)`
        + `\k<tag>`
        + `\k'tag'`
        + `\k{tag}`
        + `\g{tag}`

### Non-Capturing Groups

+ `(?: ... )`: Create a non-capturing group
+ Non-capturing groups do not count when numbering capturing groups

## Lookarounds

### Lookaheads

+ Allows us to reference an expression and use it as a boundary
+ `(?= ... )`: A positive lookahead; the regex engine ensures this match exists as a boundary following the expression but does not capturing it
+ `(?! ... )`: A negative lookahead; the regex engine ensures this match does _not_ exist as a boundary following the expression

### Lookbehinds

+ Allows us to reference an expression and use it as a boundary
+ `(?<= ... )`: A positive lookbehind; the regex engine ensures this match exists as a boundary _before_ the expression but does not capture it
+ `(?<! ... )`: A negative lookbehind; the regex engine ensures this match does _not_ exist as a boundary _before_ the expression

## Conditionals

### if Confitionals

+ Allows us to reference matched text to determine how the regular expression works
    + "If the expressions matches "a", do this; if it does not, do this other thing"
+ `(?(if)then|else)`: If the expression matches the text referenced in `if`, match the expression in `then`; if it does not match the expression in `else`.

### Named Conditionals

+ Allows us to use named backreferences when using conditionals
+ When referencing that backreference in the `if` state, only use the name of the reference, do not call the reference as normal (i.e., do not use `\g{named_ref}`)
+ Otherwise works as a normal conditional
