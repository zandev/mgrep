# Notes

As many of us, I recurrently need to extract multi line blocks of text from, let say, logs files. Depending of the structure of the block of text, a simple egrep "regexp" -Bn -An could be sufficient, or not. When it comes to match more complex regular expression over several lines of text, all traditional unix tools fails (well, I fail miserably) miserably to offer a simple solution.

The idea here is to provide a simple tool for matching complex regular expression over multiple lines of text.

## CLI usage ideas :

    mgrep -b "Warning" -e "ignored"

Will match a block of text where the first line contain the word "Warning" and the last line the word "ignored".
The idea here is that such an expressions will be auto-anchored like this:

    mgrep -b "^.*Warning.*$" -e "^.*ignored.*$"

One could give a block body constraint:

    mgrep -b "Warning" -i "Unknow (media|mime) type" -e "ignored"

Will match the given text block only if one of it's inners lines match against "^.\*Unknow (media|mime) type.\*$"

Parts of the auto-anchoring could be dissabled by anchoring manually:

    mgrep -b "^Warning" -e "ignored\.$"

Will be auto-anchored like this:

    mgrep -b "^Warning.*$" -e "^.*ignored\.$"

Consecutive lines could be matched by providing several time the same switch:

    mgrep -b "Warning" -b "^File" -e "ignored"

Will match a text block where the first line match "^.\*Warning.\*$", the second "^File.\*$" and the last "^.\*ignored.\*$"

The last idea is to provide a simple scripting language:

    Warning
    ^File
    *
    +MIME type
    *(^Unknow media)|(Bad file type)
    !Configuring NAT
    +
    ignored\.$

Here, 

__?__, __+__, __*__ and __!__ in front of the line have the same meaning as in regular expressions, but they match against new lines.

So, the following script match a text block which follow thoses rules:

The first line match

    ^.*Warning.*$

The second

    ^File.*$

Followed by zero or many lines

Followed by one or many lines matching

    ^MIME type.*$

Followed by zero or many lines matching

    (^Unknow media.*$)|(^.*Bad file type.*$)

Followed by any line not matching

    ^.*Configuring NAT.*$

Followed by one or many lines

Finally, the last line in the pattern should match

    ^.*ignored\.$

That's it, let's back to work!
