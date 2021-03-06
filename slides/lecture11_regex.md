# String Manipulation

---

## String Manipulation

When importing, cleaning or manipulating data, character strings are reasonably common.

R includes a range of string-manipulation utilities, including an implementation of **regular expressions**.

---

## `nchar`

The call `nchar(x)` finds the length of a string x.

    !r
    > nchar("Markov chain Monte Carlo")
    [1] 24

---

## `paste`

`paste` concatenates several strings, returning the result in one long string.

    !r
    > paste("Markov", "chain", "Monte", "Carlo")
    [1] "Markov chain Monte Carlo"
    > paste("Markov", "chain", "Monte", "Carlo", sep="-")
    [1] "Markov-chain-Monte-Carlo"
    > paste("Markov", "chain", "Monte", "Carlo", sep=".")
    [1] "Markov.chain.Monte.Carlo"

The optional argument sep can be used to put something other than a space between the pieces being spliced together.

---

## `sprintf`

The call `sprintf` assembles a string from parts in a formatted manner.

    !r
    > i <- 8
    > s <- sprintf("the square of %d is %d",i,i^2)
    > s
    [1] "the square of 8 is 64"

There are a variety of format options:

    !r
    > sprintf("%.3f", pi)
    [1] "3.142"
    > sprintf("%1.0f", pi)
    [1] "3"
    > sprintf("%-10f", pi)
    [1] "3.141593  "
    > sprintf("%E", pi)
    [1] "3.141593E+00"
    > sprintf("%+f", pi)
    [1] "+3.141593"

---

## `substr`

The call `substr(x,start,stop)` returns the substring in the given character position range `start`:`stop` in the given string `x`.

    !r
    > substr("biostatistics", 4, 7)
    [1] "stat"

It can also be used to substitute characters:

    !r
    > x <- "The Titans could win this weekend."
    > substr(x, 12, 16) <- "won't"
    > x
    [1] "The Titans won't win this weekend."

---

## `strsplit`

The call `strsplit` splits a string into a list of substrings based on another string (a delimiter):

    !r
    > strsplit("6-16-2011",split="-")
    [[1]]
    [1] "6"    "16"   "2011"
    > strsplit("Markov chain Monte Carlo", " ")
    [[1]]
    [1] "Markov" "chain"  "Monte"  "Carlo"

---

## What are Regular Expressions?

### Regular expressions are a way to describe patterns in text.

This is widely used in statistical computation for:

* Finding things
* Replacing things
* Extracting data

Regular expressions express patterns in character values which can then be used to extract parts of strings or to modify those strings.

---

## Regular expressions

![email regex](images/email-regexp.png)


---

## Regular Expressions

There are 3 main regular expression standards:

* POSIX Basic Regular Expressions
* POSIX Extended Regular Expressions (R uses this by default)
* Perl-based Regular Expressions (R has support for this as well)

R supports ERE by default, but it also has support for Perl-based RE (`perl=TRUE`).

A good reference: http://en.wikipedia.org/wiki/Regular_expression#Syntax

## Presenter Notes

1 There is a caveat when using regular expressions in R, but that will appear later on in this talk.
1 If you use regular expressions in a language and find that it doesn't behave as you expect, it may be because the language doesn't support certain features.

---

## The simplest regular expression

The function `grep` can be used to find matches in a character vector. It returns the indices of matching strings:

    !r
    > grep("test", c("foo", "bar", "test"))
    [1] 3
    > grep("test", c("foo", "bar", "test"), value=TRUE)
    [1] "test"

* "test" is a perfectly valid regular expression
* Works just like CTRL+F in your browser

## Presenter Notes

How do you describe more complex patterns?


---

## Metacharacters

How do we find more complex patterns?

Characters with special meaning in regular expressions are called **metacharacters**.

There are eleven metacharacters:

    [ \^$.|?*+()

Non-metacharacters are called *literals*.

## Presenter Notes

We add "meta", which means "about", because we're describing information about other characters.


---

## Wildcard Metacharacter

Dot (.) is a *wildcard* that matches any character:

    !r
    > grep("foo.bar", c("fooxbar", "foo bar", "foobar", "foo12bar"))
    [1] 1 2

Here, "foo.bar" is the regular expression. If we want to match two spaces:

    !r
    > grep("foo..bar", c("fooxbar", "foo bar", "foobar", "foo12bar"))
    [1] 4

Or, more generally:

    !r
    > grep("foo.+bar", c("fooxbar", "foo bar", "foobar", "foo12bar"))
    [1] 1 2 4
    > grep("bar+", c("bar", "barr", "barrrrrrrr", "ba"))
    [1] 1 2 3

The + metacharacter means: *one or more times*

---

## Escaping Metacharacters

In order to search for a character that is a metacharacter, it must be *escaped* using a backslash:

    !r
    > grep("\\.", c("fooxbar", "foo bar", "foobar", "foo.bar"))
    [1] 4

Why are there two backslashes?

---

## Quantifiers

The + metacharacter is a *quantifier*, metacharacters that describe how many times the preceding element is to be repeated.

To match zero or more times, we use the asterix:

    !r
    > grep("foo.*bar", c("fooxbar", "foo bar", "foobar", "foo12bar"))
    [1] 1 2 3 4
    > grep("bar*", c("bar", "barr", "barrrr", "ba"))
    [1] 1 2 3 4

Both "foobar" and "ba" match now, because we're matching zero or more times instead of one or more times.


---

## Quantifiers

The question mark searches for exactly zero or one occurrence of the preceding character:

    !r
    > grep("abc?def", c("abdef", "abcdef", "abccdef"))
    [1] 1 2

If we would like to match exactly *n* times, we enclose the number in curly braces:

    !r
    > grep("10{6}", c("1000", "1000000"))
    [1] 2

Even more generally, to match at least *n* times, but not more than *m* times:

    > grep("10{2,4}1", c("101", "1001", "10001", "100001", "1000001"))
    [1] 2 3 4

## Presenter Notes

n or m may be blank!


---

## Anchors

Quantifiers can return unexpected results on their own:

    > grep("10{3}", c("100", "1000", "10000"))
    [1] 2 3

The third string matches because this regular expression is "floating". It matches *anywhere* in the string.

**Anchors** can help to localize the regular expression search.


---

## Anchors

Anchors specify string *positions*, rather than strings themselves.

A dollar sign (`$`) metacharacter anchors the regular expression to the end of the string.

    !r
    > grep("10{3}$", c("100", "1000", "10000"))
    [1] 2

---

## Anchors

The caret (`^`) "anchors" the regular expression to the beginning of the string.

    !r
    > grep("10{3}", c("1000", "abc1000"))
    [1] 1 2
    > grep("^10{3}", c("1000", "abc1000"))
    [1] 1

Anchors can be combined to provide more control over localizing the regular expression:

    > grep("^10{3}$", c("abc1000", "1000", "10000"))
    [1] 2

---

## Anchor Symbols

The anchor symbols include:

* `^` : beginning of the string
* `$` : end of the string
* `\<` : beginning of a word
* `\>` : end of a word
* `\b` : edge of a word
* `\B` : NOT at an edge of a word

---

## Email regex

![email regex describe](images/email-regexp-labeled.png)

## Presenter Notes

1 Text-version: `^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,4}$`

---


## Example: Extracting Variable Names

In our HAART dataset, perhaps we would like to extract the variable names that begin with "init". Using `grep`, this is easy:

    !r
    > grep('^init',names(haart),value=TRUE)
    [1] "init.reg"  "init.date"

This expression can be used directly to index the variables themselves:

    !r
    > head(haart[,grep('^init',names(haart),value=TRUE)])
         init.reg init.date
    1 3TC,AZT,EFV    7/1/03
    2 3TC,AZT,EFV  11/23/04
    3 3TC,AZT,EFV   4/30/03
    4 3TC,AZT,NVP   3/25/06
    5 3TC,D4T,EFV    9/1/04
    6 3TC,AZT,NVP   12/2/03

---

## Character Classes

A **character class** matches anything inside a set of square brackets for one character position once and only once.

Specify the characters in square brackets:

    !r
    > grep("ab[cdef]", c("abc", "abd", "abe", "abf", "abg"))
    [1] 1 2 3 4

Similarly, the dash (-) metacharacter can be used to specify a range of characters:

    !r
    > grep("ab[c-f]", c("abc", "abd", "abe", "abf", "abg"))
    [1] 1 2 3 4
    > grep("ab[c-fC-F]", c("abc", "abC", "abf", "abF", "abg"))
    [1] 1 2 3 4
    > grep("ab[c-fC-F123]", c("abc", "abF", "ab1", "ab2"))
    [1] 1 2 3 4

The order of the characters inside a character class does not matter.

Typing a caret after the opening square bracket will negate the character class.

---

## Example: Finding Files

A common use of regular expressions is to find text corresponding to particular filenames. Here is one that looks for JPEG files, which are typically given a `.jpg` extension:

    ^[a-zA-Z]+\\.jpg$

* caret anchors to the start of a line
* character class looks for one or more letters, any case
* escaped period looks for the separator
* dollar sign anchors to the end of a line

---

## Metacharacters and Character Classes

Some metacharacters mean different things depending on *context*. For example, inside of brackets, most metacharacters lose their powers.

Only `^- \]` are special inside character classes.

    !r
    > grep("[.+*]", c(".", "+", "*", "x"))
    [1] 1 2 3
    > grep("[a-c-]", c("a", "b", "c", "-"))
    [1] 1 2 3 4

* To include a literal ] place it *first* in the list
* To include a literal ^ place it *anywhere but first*
* To include a literal - place it *first or last*

---

## POSIX Character Classes

POSIX Extended Regular Expressions define a set of character classes that denote certain common ranges.

* [:alnum:] : any alphanumeric character 0 to 9 or A to Z or a to z.
* [:word:] : alphanumeric characters plus `_`
* [:alpha:] : any alpha character A to Z or a to z.
* [:digit:] : only the digits 0 to 9
* [:blank:] : space and tab characters only.
* [:graph:] : exclude whitespace (SPACE, TAB).
* [:lower:] : any alpha character a to z.
* [:print:] : any visible characters and the space character
* [:punct:] : any punctuation characters.
* [:upper:] : any alpha character A to Z.

---

## POSIX Character Classes

For example,

    !r
    > grep("[[:alpha:]]", c("----x----","--------","5456655"))
    [1] 1
    > grep("[[:blank:]]", c("foo","bar","foo bar","foobar"))
    [1] 3
    > grep("^[[:alpha:].]+@[[:alpha:].]+$",
        c("chris.fonnesbeck@vanderbilt.edu", "@fonnesbeck", "fonnesbeck_chris@yahoo.com"))
    [1] 1


---

## Perl Character Classes

* `\w` : match any character in the range 0-9, A-Z or a-z and `_`
* `\W` : match any character NOT in the range 0-9, A-Z and a-z or `_`
* `\d` : match any character in the range 0-9
* `\D` : match any character NOT in the range 0-9
* `\s` : match any whitespace characters (space, tab, etc.)
* `\S` : match any character NOT whitespace
* `\b` : word boundaries, match any character(s) at the beginning and/or end of a word
* `\B` : not word boundary, match any character(s) NOT at the beginning and/or end of a word

---

## `regexpr` and `gregexpr`

Sometimes we want more details regarding the matches than is provided by `grep`. `regexpr` and `gregexpr` pinpoint and possibly extract those parts of a string that were matched by a regular expression.

The output from these functions is a vector of starting positions of the regular expressions which were found.

    !r
    > tst = c('one x7 two b1','three c5 four b9',
    +         'five six seven','a8 eight nine')
    > wh = regexpr('[a-z][0-9]',tst)
    > wh
    [1]  5  7 -1  1
    attr(,"match.length")
    [1]  2  2 -1  2

The `match.length` attribute is associated with the vector of starting positions to provide information about exactly which characters were involved in the match.

* `regexpr` only provides information about the first match in its input strings
* `gregexpr` returns information about *all* matches found

## Presenter Notes

if no match occurred, a value of -1 is returned.

---

## `regexpr` and `gregexpr`

`gregexpr` returns a list.

    !r
    > (wh1 = gregexpr('[a-z][0-9]',tst))
    [[1]]
    [1]  5 12
    attr(,"match.length")
    [1] 2 2
    attr(,"useBytes")
    [1] TRUE

    [[2]]
    [1]  7 15
    attr(,"match.length")
    [1] 2 2
    attr(,"useBytes")
    [1] TRUE

    [[3]]
    [1] -1
    attr(,"match.length")
    [1] -1
    attr(,"useBytes")
    [1] TRUE

    [[4]]
    [1] 1
    attr(,"match.length")
    [1] 2
    attr(,"useBytes")
    [1] TRUE

---

## `regexpr` and `gregexpr`

We can use `substring` to extract the characters corresponding to the expression match:

    !r
    > res <- substring(tst, wh, wh + attr(wh,'match.length') - 1)
    > res
    [1] "x7" "c5" ""   "a8"

Or, in the case of `gregexpr`:

    !r
    res1 <- list()
    for(i in 1:length(wh1))
        res1[[i]] <- substring(tst[i], wh1[[i]],
            wh1[[i]] + attr(wh1[[i]],'match.length')-1)

    > res1
    [[1]]
    [1] "x7" "b1"

    [[2]]
    [1] "c5" "b9"

    [[3]]
    [1] ""

    [[4]]
    [1] "a8"

---

## `regexpr` and `gregexpr`

Another possibility for processing the output is to use `mapply`.

This involves turning the `substring` call into a function:

    !r
    > getexpr <- function(str, greg) substring(str, greg,
    +                        greg + attr(greg,'match.length') - 1)

Now `mapply` can be called with the two vectors of interest:

    !r
    > mapply(getexpr,tst,wh1)
    $`one x7 two b1`
    [1] "x7" "b1"

    $`three c5 four b9`
    [1] "c5" "b9"

    $`five six seven`
    [1] ""

    $`a8 eight nine`
    [1] "a8"


---

## Email Regular Expression

![email regex describe](images/email-regexp.png)


---

## Email Regular Expression

![Email regexp 2](images/email-regexp-2.png)

## Presenter Notes

Complete with Titans colors!
`^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,4}$`

---

## Full Email Validation Regexp

`(?:(?:\r\n)?[ \t])*(?:(?:(?:[^()<>@,;:\\".\[\] \000-\031]+(?:(?:(?:\r\n)?[ \t] )+|\Z|(?=[\["()<>@,;:\\".\[\]]))|"(?:[^\"\r\\]|\\.|(?:(?:\r\n)?[ \t]))*"(?:(?: \r\n)?[ \t])*)(?:\.(?:(?:\r\n)?[ \t])*(?:[^()<>@,;:\\".\[\] \000-\031]+(?:(?:( ?:\r\n)?[ \t])+|\Z|(?=[\["()<>@,;:\\".\[\]]))|"(?:[^\"\r\\]|\\.|(?:(?:\r\n)?[ \t]))*"(?:(?:\r\n)?[ \t])*))*@(?:(?:\r\n)?[ \t])*(?:[^()<>@,;:\\".\[\] \000-\0 31]+(?:(?:(?:\r\n)?[ \t])+|\Z|(?=[\["()<>@,;:\\".\[\]]))|\[([^\[\]\r\\]|\\.)*\ ](?:(?:\r\n)?[ \t])*)(?:\.(?:(?:\r\n)?[ \t])*(?:[^()<>@,;:\\".\[\] \000-\031]+ (?:(?:(?:\r\n)?[ \t])+|\Z|(?=[\["()<>@,;:\\".\[\]]))|\[([^\[\]\r\\]|\\.)*\](?: (?:\r\n)?[ \t])*))*|(?:[^()<>@,;:\\".\[\] \000-\031]+(?:(?:(?:\r\n)?[ \t])+|\Z |(?=[\["()<>@,;:\\".\[\]]))|"(?:[^\"\r\\]|\\.|(?:(?:\r\n)?[ \t]))*"(?:(?:\r\n) ?[ \t])*)*\<(?:(?:\r\n)?[ \t])*(?:@(?:[^()<>@,;:\\".\[\] \000-\031]+(?:(?:(?:\ r\n)?[ \t])+|\Z|(?=[\["()<>@,;:\\".\[\]]))|\[([^\[\]\r\\]|\\.)*\](?:(?:\r\n)?[1`

---


`\t])*)(?:\.(?:(?:\r\n)?[ \t])*(?:[^()<>@,;:\\".\[\] \000-\031]+(?:(?:(?:\r\n) ?[ \t])+|\Z|(?=[\["()<>@,;:\\".\[\]]))|\[([^\[\]\r\\]|\\.)*\](?:(?:\r\n)?[ \t] )*))*(?:,@(?:(?:\r\n)?[ \t])*(?:[^()<>@,;:\\".\[\] \000-\031]+(?:(?:(?:\r\n)?[ \t])+|\Z|(?=[\["()<>@,;:\\".\[\]]))|\[([^\[\]\r\\]|\\.)*\](?:(?:\r\n)?[ \t])*)(?:\.(?:(?:\r\n)?[ \t])*(?:[^()<>@,;:\\".\[\] \000-\031]+(?:(?:(?:\r\n)?[ \t] )+|\Z|(?=[\["()<>@,;:\\".\[\]]))|\[([^\[\]\r\\]|\\.)*\](?:(?:\r\n)?[ \t])*))*) *:(?:(?:\r\n)?[ \t])*)?(?:[^()<>@,;:\\".\[\] \000-\031]+(?:(?:(?:\r\n)?[ \t])+|\Z|(?=[\["()<>@,;:\\".\[\]]))|"(?:[^\"\r\\]|\\.|(?:(?:\r\n)?[ \t]))*"(?:(?:\r \n)?[ \t])*)(?:\.(?:(?:\r\n)?[ \t])*(?:[^()<>@,;:\\".\[\] \000-\031]+(?:(?:(?: \r\n)?[ \t])+|\Z|(?=[\["()<>@,;:\\".\[\]]))|"(?:[^\"\r\\]|\\.|(?:(?:\r\n)?[ \t ]))*"(?:(?:\r\n)?[ \t])*))*@(?:(?:\r\n)?[ \t])*(?:[^()<>@,;:\\".\[\] \000-\031 ]+(?:(?:(?:\r\n)?[ \t])+|\Z|(?=[\["()<>@,;:\\".\[\]]))|\[([^\[\]\r\\]|\\.)*\]( ?:(?:\r\n)?[ \t])*)(?:\.(?:(?:\r\n)?[ \t])*(?:[^()<>@,;:\\".\[\] \000-\031]+(?`

---

`:(?:(?:\r\n)?[ \t])+|\Z|(?=[\["()<>@,;:\\".\[\]]))|\[([^\[\]\r\\]|\\.)*\](?:(? :\r\n)?[ \t])*))*\>(?:(?:\r\n)?[ \t])*)|(?:[^()<>@,;:\\".\[\] \000-\031]+(?:(? :(?:\r\n)?[ \t])+|\Z|(?=[\["()<>@,;:\\".\[\]]))|"(?:[^\"\r\\]|\\.|(?:(?:\r\n)? [ \t]))*"(?:(?:\r\n)?[ \t])*)*:(?:(?:\r\n)?[ \t])*(?:(?:(?:[^()<>@,;:\\".\[\] \000-\031]+(?:(?:(?:\r\n)?[ \t])+|\Z|(?=[\["()<>@,;:\\".\[\]]))|"(?:[^\"\r\\]| \\.|(?:(?:\r\n)?[ \t]))*"(?:(?:\r\n)?[ \t])*)(?:\.(?:(?:\r\n)?[ \t])*(?:[^()<> @,;:\\".\[\] \000-\031]+(?:(?:(?:\r\n)?[ \t])+|\Z|(?=[\["()<>@,;:\\".\[\]]))|" (?:[^\"\r\\]|\\.|(?:(?:\r\n)?[ \t]))*"(?:(?:\r\n)?[ \t])*))*@(?:(?:\r\n)?[ \t] )*(?:[^()<>@,;:\\".\[\] \000-\031]+(?:(?:(?:\r\n)?[ \t])+|\Z|(?=[\["()<>@,;:\\ ".\[\]]))|\[([^\[\]\r\\]|\\.)*\](?:(?:\r\n)?[ \t])*)(?:\.(?:(?:\r\n)?[ \t])*(? :[^()<>@,;:\\".\[\] \000-\031]+(?:(?:(?:\r\n)?[ \t])+|\Z|(?=[\["()<>@,;:\\".\[ \]]))|\[([^\[\]\r\\]|\\.)*\](?:(?:\r\n)?[ \t])*))*|(?:[^()<>@,;:\\".\[\] \000- \031]+(?:(?:(?:\r\n)?[\t])+|\Z|(?=[\["()<>@,;:\\".\[\]]))|"(?:[^\"\r\\]|\\.|(?:(?:\r\n)?[ \t]))*"(?:(?:\r\n)?[ \t])*)*\<(?:(?:\r\n)?[ \t])*(?:@(?:[^()<>@,; :\\".\[\]`

---


`\000-\031]+(?:(?:(?:\r\n)?[ \t])+|\Z|(?=[\["()<>@,;:\\".\[\]]))|\[([ ^\[\]\r\\]|\\.)*\](?:(?:\r\n)?[ \t])*)(?:\.(?:(?:\r\n)?[ \t])*(?:[^()<>@,;:\\" .\[\]\000-\031]+(?:(?:(?:\r\n)?[ \t])+|\Z|(?=[\["()<>@,;:\\".\[\]]))|\[([^\[\ ]\r\\]|\\.)*\](?:(?:\r\n)?[ \t])*))*(?:,@(?:(?:\r\n)?[ \t])*(?:[^()<>@,;:\\".\ [\] \000-\031]+(?:(?:(?:\r\n)?[ \t])+|\Z|(?=[\["()<>@,;:\\".\[\]]))|\[([^\[\]\ r\\]|\\.)*\](?:(?:\r\n)?[ \t])*)(?:\.(?:(?:\r\n)?[ \t])*(?:[^()<>@,;:\\".\[\] \000-\031]+(?:(?:(?:\r\n)?[ \t])+|\Z|(?=[\["()<>@,;:\\".\[\]]))|\[([^\[\]\r\\] |\\.)*\](?:(?:\r\n)?[ \t])*))*)*:(?:(?:\r\n)?[ \t])*)?(?:[^()<>@,;:\\".\[\] \0 00-\031]+(?:(?:(?:\r\n)?[ \t])+|\Z|(?=[\["()<>@,;:\\".\[\]]))|"(?:[^\"\r\\]|\\ .|(?:(?:\r\n)?[ \t]))*"(?:(?:\r\n)?[ \t])*)(?:\.(?:(?:\r\n)?[ \t])*(?:[^()<>@, ;:\\".\[\] \000-\031]+(?:(?:(?:\r\n)?[ \t])+|\Z|(?=[\["()<>@,;:\\".\[\]]))|"(? :[^\"\r\\]|\\.|(?:(?:\r\n)?[ \t]))*"(?:(?:\r\n)?[ \t])*))*@(?:(?:\r\n)?[ \t])*`

---


`(?:[^()<>@,;:\\".\[\] \000-\031]+(?:(?:(?:\r\n)?[ \t])+|\Z|(?=[\["()<>@,;:\\". \[\]]))|\[([^\[\]\r\\]|\\.)*\](?:(?:\r\n)?[ \t])*)(?:\.(?:(?:\r\n)?[ \t])*(?:[ ^()<>@,;:\\".\[\] \000-\031]+(?:(?:(?:\r\n)?[ \t])+|\Z|(?=[\["()<>@,;:\\".\[\] ]))|\[([^\[\]\r\\]|\\.)*\](?:(?:\r\n)?[ \t])*))*\>(?:(?:\r\n)?[ \t])*)(?:,\s*( ?:(?:[^()<>@,;:\\".\[\] \000-\031]+(?:(?:(?:\r\n)?[ \t])+|\Z|(?=[\["()<>@,;:\\ ".\[\]]))|"(?:[^\"\r\\]|\\.|(?:(?:\r\n)?[ \t]))*"(?:(?:\r\n)?[ \t])*)(?:\.(?:( ?:\r\n)?[ \t])*(?:[^()<>@,;:\\".\[\] \000-\031]+(?:(?:(?:\r\n)?[ \t])+|\Z|(?=[ \["()<>@,;:\\".\[\]]))|"(?:[^\"\r\\]|\\.|(?:(?:\r\n)?[ \t]))*"(?:(?:\r\n)?[ \t ])*))*@(?:(?:\r\n)?[ \t])*(?:[^()<>@,;:\\".\[\] \000-\031]+(?:(?:(?:\r\n)?[ \t ])+|\Z|(?=[\["()<>@,;:\\".\[\]]))|\[([^\[\]\r\\]|\\.)*\](?:(?:\r\n)?[ \t])*)(?:\.(?:(?:\r\n)?[ \t])*(?:[^()<>@,;:\\".\[\] \000-\031]+(?:(?:(?:\r\n)?[ \t])+| \Z|(?=[\["()<>@,;:\\".\[\]]))|\[([^\[\]\r\\]|\\.)*\](?:(?:\r\n)?[ \t])*))*|(?:`

---

`[^()<>@,;:\\".\[\] \000-\031]+(?:(?:(?:\r\n)?[ \t])+|\Z|(?=[\["()<>@,;:\\".\[\ ]]))|"(?:[^\"\r\\]|\\.|(?:(?:\r\n)?[ \t]))*"(?:(?:\r\n)?[ \t])*)*\<(?:(?:\r\n) ?[ \t])*(?:@(?:[^()<>@,;:\\".\[\] \000-\031]+(?:(?:(?:\r\n)?[ \t])+|\Z|(?=[\[" ()<>@,;:\\".\[\]]))|\[([^\[\]\r\\]|\\.)*\](?:(?:\r\n)?[ \t])*)(?:\.(?:(?:\r\n) ?[ \t])*(?:[^()<>@,;:\\".\[\] \000-\031]+(?:(?:(?:\r\n)?[ \t])+|\Z|(?=[\["()<> @,;:\\".\[\]]))|\[([^\[\]\r\\]|\\.)*\](?:(?:\r\n)?[ \t])*))*(?:,@(?:(?:\r\n)?[ \t])*(?:[^()<>@,;:\\".\[\] \000-\031]+(?:(?:(?:\r\n)?[ \t])+|\Z|(?=[\["()<>@, ;:\\".\[\]]))|\[([^\[\]\r\\]|\\.)*\](?:(?:\r\n)?[ \t])*)(?:\.(?:(?:\r\n)?[ \t] )*(?:[^()<>@,;:\\".\[\] \000-\031]+(?:(?:(?:\r\n)?[ \t])+|\Z|(?=[\["()<>@,;:\\ ".\[\]]))|\[([^\[\]\r\\]|\\.)*\](?:(?:\r\n)?[ \t])*))*)*:(?:(?:\r\n)?[ \t])*)?(?:[^()<>@,;:\\".\[\] \000-\031]+(?:(?:(?:\r\n)?[ \t])+|\Z|(?=[\["()<>@,;:\\". \[\]]))|"(?:[^\"\r\\]|\\.|(?:(?:\r\n)?[ \t]))*"(?:(?:\r\n)?[ \t])*)(?:\.(?:(?: \r\n)?[`

---

`\t])*(?:[^()<>@,;:\\".\[\] \000-\031]+(?:(?:(?:\r\n)?[ \t])+|\Z|(?=[\[ "()<>@,;:\\".\[\]]))|"(?:[^\"\r\\]|\\.|(?:(?:\r\n)?[ \t]))*"(?:(?:\r\n)?[ \t]) *))*@(?:(?:\r\n)?[ \t])*(?:[^()<>@,;:\\".\[\] \000-\031]+(?:(?:(?:\r\n)?[ \t]) +|\Z|(?=[\["()<>@,;:\\".\[\]]))|\[([^\[\]\r\\]|\\.)*\](?:(?:\r\n)?[ \t])*)(?:\ .(?:(?:\r\n)?[ \t])*(?:[^()<>@,;:\\".\[\] \000-\031]+(?:(?:(?:\r\n)?[ \t])+|\Z |(?=[\["()<>@,;:\\".\[\]]))|\[([^\[\]\r\\]|\\.)*\](?:(?:\r\n)?[ \t])*))*\>(?:( ?:\r\n)?[ \t])*))*)?;\s*)`

**6343 characters!**

---

## Substituting Text

Search-and-replace operations are carried out using `sub` and `gsub`.

* `sub` replaces only the first occurrence of a pattern
* `gsub` replaces all occurrences

Example: replace lower-case with upper-case:

    !r
    > text <- c("arm","leg","head", "foot","hand", "hindleg", "elbow")
    > gsub("h","H",text)
    [1] "arm" "leg" "Head" "foot" "Hand" "Hindleg" "elbow"
    > sub("o","O",text)
    [1] "arm" "leg" "head" "fOot" "hand" "hindleg" "elbOw"

More generally, to replace the first character of every string with upper-case 'O' we use the wildcard with an anchor:

    !r
    gsub("^.","O",text)
    [1] "Orm" "Oeg" "Oead" "Ooot" "Oand" "Oindleg" "Olbow"

---

## Svetlana's Tutorial

Svetlana Eden wrote an excellent tutorial (2007): ***Introduction to String Matching and Modification in R Using Regular Expressions***

### http://bit.ly/svetlana_regexp

<script type="text/x-mathjax-config">
  MathJax.Hub.Config({
    tex2jax: {
      displayMath: [ ['$$','$$'], ["\\[","\\]"] ],
      inlineMath: [ ['$','$'], ["\\(","\\)"] ],
      processEscapes: true
    }
  });
</script>
<script type="text/javascript"
    src="../MathJax/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>