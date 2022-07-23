# Regex Tutorial

To see revisions please see the original gist.
The original gist is [here.](https://gist.github.com/Tonyavall/fabb4a74fc417442174bce27dde973ec)

## Summary

A regular expression, or regex, is a specific pattern to be searched in text or a body of text. Regex consists of 'tokens', or characters that have a certain meaning that will match to a single text character or a sequence of characters based on the meaning, or functionality. This guide will be in the context of JavaScript and the bodies of texts will be referred to as strings.

## Table of Contents

- [Anchors](#anchors)
- [Quantifiers](#quantifiers)
- [Grouping Constructs](#grouping-constructs)
- [Bracket Expressions](#bracket-expressions)
- [Character Classes](#character-classes)
- [The OR Operator](#the-or-operator)
- [Flags](#flags)
- [Character Escapes](#character-escapes)

## Regex Components

### Anchors
Anchors are regex tokens that don't match to any characters, but instead specifies a position in the string. For example, anchors ^ (caret) and $ (dollar) specify the beginning of the string and end of the string, respectively.

Lets find out what this means:

    We will use the regex method .test(), which will return true if the tested string contains the regular expression. 
    Here is the syntax.
        RegExpObject.test(string)

    Suppose we are testing for whether a name contains the letter 'n'.

    Here we're saying find any instance of the letter n.
    /n/.test('Brian') // Returns true

    Now we're saying find the letter n in the BEGINNING of the string.
    /^n/.test('Brian') // Returns false
    /^n/.test('Natalie') // Returns true

    Finally, we're saying find the letter n in the END of the string.
    /n$/.test('Brian') // Returns true

Anchors can have very useful implications when we touch on more complex regular expressions.

### Quantifiers
In short, quantifiers count. They are regex tokens that specify how many instances of an expression to match to.
Please visit and consider this [table](https://docs.microsoft.com/en-us/dotnet/standard/base-types/quantifiers-in-regular-expressions) to open in a seperate tab.

First ignore the 'Lazy Quantifier' column and only consider the first and last columns on the first table, since quantifiers are naturally greedy.

Now that you have this table side by side, let's take a deep dive.

    The token \d represents all numbers 0-9.
    So when we have the JavaScript expression /\d/ we are doing 2 things:
        1. Declaring a new /Regex/ object.
        2. Saying, hey, find a single character that is any number from 0 to 9.
    
    /\d\d/.test('21') // Returns true
    /\d\d/.test('2') // Returns false, because we specified to look for 2 characters. 
    
    What if we wanted to look for more? This is where quantifiers come in.

    Referencing the table we know * matches 0 OR more times.
    This means that it will match the expression specified as many times as it can or none at all.

    /\d*/.test(' ') // returns true -> our 0 instance
    /\d*/.test('2') // returns true -> our instance of 1
    /\d*/.test('241923671895612038103') // returns true -> our instance of MORE

    The quantifier + (plus) will match 1 OR more times. It will not have an instance of 0 like the * quantifier.

    /\d+/.test(' ') // returns false 
    /\d+/.test('2') // returns true -> our instance of 1
    /\d+/.test('241923671895612038103') // returns true -> our instance of MORE
    
    The quantifier ? will match 0 or 1 time.
    This can be useful if you want the token before ? to be optional.

    Here we are putting a ? quantifier after the literal character 's', therefore it makes the 's' optional. 
    /https?/.test('http') // returns true
    /https?/.test('https') // returns true

    If we wanted to make the 'p' optional as well we could...
    /http?s?/.test('http') // returns true

    What if we wanted to find an expression a specific number (n) of times? This is where {n} comes in.

    {n} will match EXACTLY n number of times.
    {n,} will match ATLEAST n number of times.
    {n,m} will match FROM n TO m number of times.

    /\d{5}/.test('12345y') // returns true
    /\d{6}/.test('12345y') // returns false

    /\d{5,}/.test('1234') // returns false

    /\d{4,5}/.test('1234') // returns true
    /\d{4,5}/.test('123') // returns false

And that's it!

The final concept to remember about quantifiers is that quantifiers are greedy by default. What does this mean?

Greedy = Will match as MANY occurences of the pattern as possible

Lazy = Will match the LEAST occurences of the pattern as possible

To make a quantifier lazy, simply attach a ? after it.
### Grouping Constructs
Grouping constructs is actually a simple concept. To group constructs or a set of tokens, we simply encase it in paranthesis. 

For example:

    Suppose we have a phone number,
    916-253-8512 

    To match this expression we would do something like this...
    \d{3}-\d{3}-\d{4}

    Now let's group each part of the number.
    (\d{3})-(\d{3})-(\d{4})
       1       2       3

    The group numbers are as labeled above AND is how they are referred to.

    ENTIRE STRING = $0
    Group 1 = $1
    Group 2 = $2
    Group 3 = $3

    In JavaScript, using the method .replace,
    we can now reorder the phone number or any set of characters that matches the expression.

    '916-253-8512'.replace(/(\d{3})-(\d{3})-(\d{4})/g,'$3-$2-$1')
                                    ^Regex               ^Order

    Are you able to guess what this will return?
    It will return '8512-253-916'.
And that's it for capturing and manipulating groups!

Documentation on .replace can be found [here.](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/replace)
### Bracket Expressions
Brackets are essentially a set of optional tokens to match a single character. It's useful if you want to only match characters A-Z, a-z, or both.

Before we take a deeper dive, it's important to mention that all characters in a bracket expression are interpreted literally.

For example, period (.), is the token to match any one character, but in a bracket it is interpreted as a literal period to match a period character. 

    To escape this, we simply use a backslash \ like so, \.

There are 4 exceptions to this. First, the carat (^), next is the hyphen (-), then bracket (]), and finally, the backslash \

The hyphen essentially functions as a NOT operator where as the hyphen expresses a range of characters. If the hyphen is placed in the beginning of the bracket expression, it WILL be interpreted literally.

Let's take a look:

    Suppose we want to match a specific set of optional characters.
        [abc] will match -> a, OR b, OR c
        [abc]{3} will match exactly 3 characters ->
            all 3 being either a, OR b, OR c

    Similarly we can also define a range of characters using a hyphen token (-).
        [a-z] will match all LOWERCASE letters in the alphabet.
        [a-z]{3} will match the same as above, but with a sequence of exactly 3 characters.

        [a-z]+ will match ALL lowercase letters found.
        [^a-z]+ will match all UPPERCASE letters found.

        Remember, caret (^), works as a NOT operator and will 
        match everything except the specified tokens in the bracket.
### Character Classes
Character classes are related, but not limited to bracket expressions. Consider this [table](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions/Character_Classes) of character class types.

For this guide, we will only cover the most common ones:

    \d matches any digit 0-9
    /\d/.test('H3llo') // returns true. Will match '3'

    \w matches A-Z, a-z, and 0-9
    /\w+/.test('H3llo') // returns true. Will match all characters seperately.

    \s matches a single white space character
    /\s/.test('MynameisJohnDoe') // returns false
    /\s/.test( 'MynameisJohnDoe ') // returns true

    . matches any single character except line terminators
    /.+/.test('1fgNa0123A') // returns true

    * matches 0 or more characters
    /.*/.test('00fnakp 12380 faf mowpafhwi a31023andl ') // returns true

    The combination .* will grab anything and everything within the string.

That's it! There are more character classes to learn about in the provided table,
however, the ones we've covered are the most commonly used ones.
### The OR Operator
The OR operator, or also known as the alternation operator, works exactly like a bracket expression, except we utilize capturing groups. 

For instance: 
    
    Gr[ae]y will match both gray and grey because it will match a OR e.

    We can apply the same logic to the OR operator.

    Gr(a|e)y will match the same exact thing.

    /The (cat|dog) went for a walk/ // will match both
    'The cat went for a walk' and
    'The dog went for a walk'

    How is this usefull? Suppose we try to match emails.
    Emails can either be gmail, yahoo, outlook, etc and can have
    different top level domains such as .com, .org, etc.

    A simplified regex, as a real email regex validator is quite long,
    would look something like this.

    / \w+@(gmail|yahoo|outlook).(com|org|net) /g

Notice something different? That's right! This time we used / /g <- a flag.
### Flags
As you may have guessed, flags are optional, but can be useful in many cases.
For this example we will use the method [.match.](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/match)

There are only 6 flags in javascript: 

    The two most important are...
    
        const string = 'I love pizza. Love olives. LovE them all!'

    1. g - Makes the search look for ALL matches
        string.match(/love/g)   //  returns love, Love, LovE  

    2. i - Makes the search case sensitive
        string.match(/Love/i)   //  returns Love, skips over love

    They are often used together...
        string.match(/LovE/ig) // returns LovE

    Other flags include...
    3. m - [Multiline Mode](https://javascript.info/regexp-multiline-mode)    
    4. s - Allows a dot . to match newline character \n
    5. u - Enables full unicode support.
    6. y - Allows for search at the exact position in the text


Last, but not least are character escapes that we've briefly mentioned.
### Character Escapes
Character escapes enables us to output characters that would otherwise be
impossible. This includes escaping special characters to literal and vise versa.

From a previous example we mentioned...

    A question mark (?) immediately following a character means match zero or one instance of this character. 
    What if we wanted to use a question mark literally?

    We can simply, escape it.
        const string = 'Who took my icecream?'
        string.match(/icecream\?/)   //  returns 'icecream?'

This applies to many special characters in regular expressions and vice versa.
It essentially flips the meaning of the character allowing us to utilize functionalities
that otherwise would be impossible.

That's it! We've now covered all the basics of Regex based on JavaScript.

Please let me know if any parts of this article seems confusing, misleading,
or wrong and I will edit it ASAP. Thank you!

## Author

Created by Tony Vallescas,

CS Student. Aspiring Software Engineer

Github: [Tonyavall](https://github.com/Tonyavall)
