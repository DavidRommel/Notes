# Learning Regular Expressions

### Table of Contents
* [1. Get Started with Regular Expressions](#1-get-started-with-regular-expressions)
    * [Expression Flags](#expression-flags)
    * [findall()](#findall)
    * [search()](#search)
    * [match()](#match)
* [2. Characters](#2-characters)
    * [Literal Characters](#literal-characters)
    * [Metacharacters](#metacharacters)
    * [Wildcard metacharacter](#wildcard-metacharacter)
    * [Escaping Metacharacters](#escaping-metacharacters)
    * [Other Special Characters](#other-special-characters)
    * [Challenge: Characters](#challenge-characters)
* [3. Character Sets](#3-character-sets)
    * [Define a character set](#define-a-character-set)
    * [Character ranges](#character-ranges)
    * [Negative character sets](#negative-character-sets)
    * [Metacharacters inside characters sets](#metacharacters-inside-characters-sets)
    * [Shorthand character sets](#shorthand-character-sets)
    * [Challenge: Character sets](#challenge-character-sets)
* [4. Repetition](#4-repetition)
    * [Repetition metacharacters](#repetition-metacharacters)
    * [Quantified repetition](#quantified-repetition)
    * [Greedy expressions](#greedy-expressions)
    * [Lazy expressions](#lazy-expressions)
    * [Challenge: Repetition](#challenge-repetition)
* [5. Grouping and Alternation](#5-grouping-and-alternation)
    * [Grouping metacharacters](#grouping-metacharacters)
    * [Alternation metacharacters](#alternation-metacharacters)
    * [Efficiency when using alternation](#efficiency-when-using-alternation)
    * [Challenge: Grouping and alternation](#challenge-grouping-and-alternation)
* [6. Anchors](#6-anchors)
    * [Start and end anchors](#start-and-end-anchors)
    * [Line breaks and multiline mode](#line-breaks-and-multiline-mode)
    * [Word boundaries](#word-boundaries)
    * [Challenge: Anchors](#challenge-anchors)
* [7. Capturing Groups and Backreferences](#7-capturing-groups-and-backreferences)
    * [Captures and backreferences](#captures-and-backreferences)
    * [Backreferences to optional expressions](#backreferences-to-optional-expressions)
    * [Find and replace using backreferences](#find-and-replace-using-backreferences)
    * [Non-capturing group expressions](#non-capturing-group-expressions)
    * [Challenge: Backreferences](#challenge-backreferences)
* [8. Lookaround Assertions](#8-lookaround-assertions)
    * [Positive lookahead assertions](#positive-lookahead-assertions)
    * [Negative lookahead assertions](#negative-lookahead-assertions)

```python
import numpy as np
import pandas as pd
import re
```


```python
with open('data/emerson_self-reliance.txt') as file:
    emerson = file.read()

with open('data/shakespeare_sonnet_18.txt') as file:
    shakespeare = file.read()

with open('data/us_presidents.csv') as file:
    presidents = file.read()
```

## 1. Get Started with Regular Expressions

### Expression Flags

Indicates different modes that we are using with our regular expressions.

* `re.A` (re.ASCII) Perform ASCII-only matching instead of full Unicode matching
* `re.I` (re.IGNORECASE) Perform case-insensitive matching
* `re.M` (re.MULTILINE) This flag is used with metacharacter caret and dollar.  When this flag is specified, the metacharacter ^ matches the pattern at beginning of the string and each newline’s beginning (\n).  And the metacharacter $ matches pattern at the end of the string and the end of each new line (\n)
* `re.S` (re.DOTALL) Make the DOT (.) special character match any character at all, including a newline. Without this flag, DOT(.) will match anything except a newline
* `re.X` (re.VERBOSE) Allow comment in the regex. This flag is useful to make regex more readable by allowing comments in the regex.
* `re.L` (re.LOCALE) Perform case-insensitive matching dependent on the current locale. Use only with bytes patterns


```python
text = 'This is a test'

# the r in front of the string stands for "raw string"
# it tells the Python interpreter to treat backslashes as literal characters rather than as part of an escape sequence
# regular expression patterns use backslashes frequently
pattern = r'TEST'
re.findall(pattern, text)
```




    []




```python
# Perform case-insensitive matching
re.findall(pattern, text, flags = re.I)
```




    ['test']




```python
# To specify more than one flag, use the | operator to connect them.
re.findall(pattern, text, flags = re.I | re.M | re.X)
```




    ['test']



### findall()
* is the equivalent of **setting** the **Global** flag
* will return **all** occurrences of the pattern


```python
text = 'This is a test string that has two occurances of test'
pattern = r'test'
re.findall(pattern, text)
```




    ['test', 'test']



### search()
* is equivalent to **disabling** the **Global** flag
* will return the **first** occurrence only
    * string is read from left to right 


```python
match = re.search(pattern, text)

print(match.group()) # Returns the actual string that was matched
print(match.span()) # Returns a tuple containing both the (start, end)
print(match.start()) # Returns the starting index of the match within the original string
print(match.end()) # Returns the ending index of the match.
```

    test
    (10, 14)
    10
    14


### match()
* only checks the beginning of a string
* the same functions as in the `search()` function can be used to parse the components of the match object


```python
text = 'This is some text'
pattern = r'This'
print(re.match(pattern, text))
```

    <re.Match object; span=(0, 4), match='This'>



```python
# searching for patterns not at the beginning of a string will return None
pattern = r'is'
print(re.match(pattern, text))
```

    None


## 2. Characters

### Literal Characters
* regular expressions are case sensitive by default
* spaces are treated as characters
    * `car` does not equal `c a r`


```python
text = 'carnival'
pattern = r'carn'

# this will find the 'carn' in 'carnival' but not return the whole word
re.findall(pattern, text)
```




    ['carn']



### Metacharacters
* a character with special meaning
    * can have more than one meaning, need to understand the context to know how they perform in a certain expressiopn
* \ . * + - { } [ ] ^ $ | ? ( ) : ! =


```python
text = 'pizzazz'
# online tools like regexr.com will color code the different parts of this function
# this can be helpful when building complex regular expressions
pattern = r'zz .* .+ [abc] $ ? (abc) \d{2,3}'
```

### Wildcard metacharacter
* `.` any character except newline
* not a literal period
    * `r'9.00'` will match 9.00, 9500, 9-00 and not just the period like may be intended
    * A good regular expression should match the text you want to target and only that text, nothing more.


```python
pattern = r'h.t'
print(re.search(pattern, 'hat'))
print(re.search(pattern, 'hot'))
print(re.search(pattern, 'hit'))
print(re.search(pattern, 'heat')) # the wildcard is only one single character
```

    <re.Match object; span=(0, 3), match='hat'>
    <re.Match object; span=(0, 3), match='hot'>
    <re.Match object; span=(0, 3), match='hit'>
    None



```python
text = 'silver sliver slider'
pattern = r's...er'
re.findall(pattern, text)
```




    ['silver', 'sliver', 'slider']



### Escaping Metacharacters
* `\` Escape the next character
    * treat the metacharacter that follows as a literal character
    * escape a backslash by preceeding it with a backslash `\\`


```python
pattern = r'9\.00' # treats the period as a literal character instead of a wildcard
text = '9.00 9500 9-00'
re.findall(pattern, text)
```




    ['9.00']



### Other Special Characters
* a space is a character
* tab `\t`
* tine returns
    * can vary based on the environment (Windows, Mac, Linux)
        * line return `\r`
        * new line `\n`
        * `\r\n`


```python
text = 'before tab\tafter tab'
pattern = r'\t.....'
print(text)
re.search(pattern, text)
```

    before tab	after tab
    <re.Match object; span=(10, 16), match='\tafter'>




```python
text = 'one\ntwo'
pattern = r'\n...'
print(text)
re.search(pattern, text)
```

    one
    two
    <re.Match object; span=(3, 7), match='\ntwo'>



### Challenge: Characters


```python
pattern = r'self'
len(re.findall(pattern, emerson, flags = re.I))
```



    64




```python
words = ['himself', 'herself', 'itself', 'myself', 'yourself', 'thyself']
for w in words:
    print('{} : {}'.format(w, len(re.findall(w, emerson, flags = re.I))))
```

    himself : 20
    herself : 0
    itself : 12
    myself : 6
    yourself : 7
    thyself : 1



```python
text = 'please, palace, parade'
pattern = r'p..a.e'
re.findall(pattern, text)
```




    ['please', 'palace', 'parade']




```python
pattern = r't..ch'
words = pd.Series(re.findall(pattern, emerson, flags = re.I))
words.value_counts()
```




    teach    8
    t, ch    2
    ttach    1
    to Ch    1
    touch    1
    tarch    1
    Name: count, dtype: int64



## 3. Character Sets

### Define a character set
* Will match any one of several characters
    * But only **one** character
* Can consist of characters, numbers, punctuation
* `[` Begin a character set
* `]` End a character set


```python
text = 'gray grey'
pattern = r'gr[ea]y'
re.findall(pattern, text)
```




    ['gray', 'grey']




```python
# even though 'a' is the second character in the set it still returns the first word 'gray' in the text string
re.search(pattern, text)
```




    <re.Match object; span=(0, 4), match='gray'>




```python
text = 'great'
pattern = r'gr[ea]t' # only matches a single character, not multiple characters
re.findall(pattern, text)
```




    []



### Character ranges
* `-` specifies a range of characters
    * only a metacharacter **inside** of a character set
* includes all characters between two characters
    * assumes characters have some sort of order between them
    * `[0-9]`, `[A-Za-z]`
    * not as intuitive with symbols
* Character sets only match a **single** character
    * `[50-99]` will **not** match all numbers between 50 and 99
        * net effect of this is the same as `[0-9]`
        * a character set including 5, 0-9, and 9


```python
text = '123-456-7890'
pattern = r'[0-9][0-9][0-9]-[0-9][0-9][0-9]-[0-9][0-9][0-9][0-9]'
re.findall(pattern, text)
```




    ['123-456-7890']



### Negative character sets
* `^` negate a character set
    * as the **first** character inside a character set
* character set is **not** one of several characters
    * `[^aeiou]` matches any one consonant (non-vowel)


```python
text = 'seek sees seem seen see'
pattern = r'see[^mn]'

# does not match 'see' because it doesn't have four characters
# the negative character set is still looking for a character
# if there was a space or punctuation mark after 'see' it would be returned since that counts as a character
re.findall(pattern, text)
```




    ['seek', 'sees']



### Metacharacters inside characters sets
* Most metacharacters inside character sets are already escaped
* `h[a.]t` would treat the period as a literal character and not as a wildcard
    * **hat** and **h.t** would match
* Exceptions:
    * `]-^\`
    * need to use `\` to escape these characters
* Python will give warning of 'possible nested set' if `[` isn't escaped as well


```python
text = 'var[3] var[4]'
pattern = r'var[\[(][0-9][\])]'
re.findall(pattern, text)
```




    ['var[3]', 'var[4]']




```python
text = r'file01 file-1 file\1 file_1'
pattern = r'file[0\-\\_]1'
re.findall(pattern, text)
```




    ['file01', 'file-1', 'file\\1', 'file_1']



### Shorthand character sets
* `\d` Digit, equivalent to `[0-9]`
* `\w` Word character, equivalent to `[a-zA-Z0-9_]`
    * An underscore is considered a word character
    * A hyphen is not a word character
        * use `[\w\-]` to include the hyphen
* `\s` Whitespace, equivalent to `[ \t\r\n]`

Negative versions are the capital of each letter
* `\D` Not digit, equivalent to `[^0-9]`
* `\W` Not word character, equivalent to `[^a-zA-Z0-9_]`
* `\S` Not whitespace, equivalent to `[^ \t\r\n]`

`[^\d\s]` is not the same as `[\D\S]`
* `[^\d\s]` NOT digit OR space character
* `[\D\S]` EITHER NOT digit OR NOT space character


```python
text = 'ABC 123 1_A'
pattern = r'\w\w\w'
re.findall(pattern, text)
```




    ['ABC', '123', '1_A']




```python
text = '1234 5678 abc'

# will find any character which is not a digit or a space
pattern = r'[^\d\s]'
re.findall(pattern, text)
```




    ['a', 'b', 'c']




```python
# will find any character which is either not a digit or not a space
# \D matches anything that is not a digit (which includes letters, symbols, and spaces)
# \S matches anything that is not a whitespace (which includes letters, symbols, and digits
# if either of those are true (which will always be the case) then the character will be returned
pattern = r'[\D\S]'
re.findall(pattern, text)
```




    ['1', '2', '3', '4', ' ', '5', '6', '7', '8', ' ', 'a', 'b', 'c']



### Challenge: Character sets


```python
pattern = r'live[sd]'
re.findall(pattern, emerson)
```




    ['lives', 'lives', 'lives', 'lives', 'lived', 'lived', 'lives']




```python
pattern = r'virtue[^s]' # this will include an additional character other than 's' after the word
re.findall(pattern, emerson)
```




    ['virtue ',
     'virtue ',
     'virtue ',
     'virtue ',
     'virtue.',
     'virtue ',
     'virtue?',
     'virtue,',
     'virtue.',
     'virtue ',
     'virtue ',
     'virtue,',
     'virtue,',
     'virtue.']




```python
pattern = r'\d\.'
re.findall(pattern, emerson)
```




    ['1.', '2.', '3.', '4.']




```python
pattern = r'c\w\w\w\w\w\w\w\w\w\w\w\w\w\w\w'
re.findall(pattern, emerson)
```




    ['circumnavigation']



## 4. Repetition

### Repetition metacharacters
* `*` preceding item, zero or more times
* `+` preceding item, one or more times
    * most common
    * frequently paired with the wildcard metacharacter
        * `.+` matches any string of characters except a line return
    * can be combined with a shorthand character set `\d+`
* `?` preceding item, zero or one time


```python
s = pd.Series(['Good morning', 'Good day.', 'Good evening.', 'Another word'], name='words')
pattern = r'Good .+'

# one method for using regular expressions in a Series object
s.str.findall(pattern)
```




    0     [Good morning]
    1        [Good day.]
    2    [Good evening.]
    3                 []
    Name: words, dtype: object




```python
# another method for using regular expressions in a Series object
s.apply(lambda x: re.findall(pattern, str(x)))
```




    0     [Good morning]
    1        [Good day.]
    2    [Good evening.]
    3                 []
    Name: words, dtype: object




```python
text = 'jumped apple walked house played talked river called started smile'
pattern = r'\s[a-z]+ed\s'

# 'jumped' does not have a space before it so it is exluded from the results
# the space between 'played' and 'talked' was already used (consumed) as the ending of the ' played ' match
# it is no longer available to serve as the starting space for ' talked '
# the same is true for ' started ' since ' called ' preceeds it
re.findall(pattern, text)
```




    [' walked ', ' played ', ' called ']




```python
text = 'apple apples applesssssss'
pattern = r'apples*'

# s* matches the character 's' zero or more times
# s+ would not match 'apple' since it is searching for at least one s
# s? would only match 'apple' or 'apples'
re.findall(pattern, text)
```




    ['apple', 'apples', 'applesssssss']




```python
text = '0 01 012 0123 01234 012345 0123456'
pattern = r'\d\d\d\d*'
re.findall(pattern, text)
```




    ['012', '0123', '01234', '012345', '0123456']




```python
# this may be more clear than using the * in the previous example
pattern = r'\d\d\d+'
re.findall(pattern, text)
```




    ['012', '0123', '01234', '012345', '0123456']




```python
text = 'color colour'
# this pattern will match both spellings
pattern = r'colou?r'
re.findall(pattern, text)
```




    ['color', 'colour']



### Quantified repetition
* Used specify the number of times something is repeated
* `{` start quantified repetition of preceding item
* `}` end quantified repetition of preceding item
* `{min, max}` you specifiy the minimum and maximum number of times the character can be repeated
    * `min` can be zero
    * `max` is optional
    * `\d{4,8}` matches numbers with four to eight digits
    * `\d{4}` matches numbers with exactly four digits (min is max)
    * `\d{4,}` mtches numbers with four or more digits (max is infinite)


```python
text = ['(555) 123-4567', '+1 212-555-0199', '555-987-6543', '011 44 20 7946 0958', '555-246-8135', '+61 2 9876 5432', '555-369-1470', '555.802.9314', '555-481-5162', '555-730-2941']
df = pd.DataFrame({'phone_numbers' : text})
pattern = r'\d{3}-\d{3}-\d{4}'

# contains() string method can be used to search for regular expressions in data frame columns
df['is_invalid'] = ~df['phone_numbers'].str.contains(pattern, na = False)
df[df['is_invalid'] == True]
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>phone_numbers</th>
      <th>is_invalid</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>(555) 123-4567</td>
      <td>True</td>
    </tr>
    <tr>
      <th>3</th>
      <td>011 44 20 7946 0958</td>
      <td>True</td>
    </tr>
    <tr>
      <th>5</th>
      <td>+61 2 9876 5432</td>
      <td>True</td>
    </tr>
    <tr>
      <th>7</th>
      <td>555.802.9314</td>
      <td>True</td>
    </tr>
  </tbody>
</table>



```python
text = '''1. a
2. ab
3. abc
4. abcd
5. abcde
6. abcdef'''

pattern = r'\d\. \w{3,5}+\s'
re.findall(pattern, text)
```




    ['3. abc\n', '4. abcd\n', '5. abcde\n']



### Greedy expressions
* standard repetiton quantifiers are greedy
    * expression tries to match the longest possible string


```python
text = 'filename.jpg, filename2.jpg'
pattern = r'.+\.jpg'

# returns the whole string instead of just the individual filenames like intended
re.findall(pattern, text)
```




    ['filename.jpg, filename2.jpg']




```python
text = 'page 266'
pattern = r'.*[0-9]+'
# regular expression engine first checks the .* wildcard expression until it is no longer valid
# this returns the whole string
# engine then backtracks character by character checking the [0-9]+ part of the expression
# if it gave up a character would it match
re.findall(pattern, text)
```




    ['page 266']



### Lazy expressions
* opposite of greedy expressions
* `?` make preceding quantifier lazy
    * match as little as possile before giving control to the next expression part
    * still defers to the overall match


```python
text = 'page 266'
pattern = r'.*?[0-9]+'

# goes letter by letter when evaluating .* and asks if the second part would return a match
# continually asks "can I give up yet"
re.findall(pattern, text)
```




    ['page 266']




```python
pattern = r'.*?[0-9]+?'

# the sixes are returned because .* can return 0 or more characters
# then the bare minimum number of digits are returned
re.findall(pattern, text)
```




    ['page 2', '6', '6']




```python
text = '01_FY_07_report_99.xls'
# default greedy behavior
pattern = r'\d+\w+\d+'
re.findall(pattern, text)
```




    ['01_FY_07_report_99']




```python
# lazy behavior
# takes the bare minimum number of word characters until a digit is found
pattern = r'\d+\w+?\d+'
re.findall(pattern, text)
```




    ['01_FY_07']



### Challenge: Repetition


```python
pattern = r'\w*self'
result = pd.Series(re.findall(pattern, emerson))
result.value_counts()
```




    himself     20
    self        15
    itself      12
    yourself     7
    myself       6
    thyself      1
    Name: count, dtype: int64




```python
pattern = r'T\w{11}'
re.findall(pattern, emerson)
```




    ['Tranquillity']




```python
pattern = r'".+?"'
# this will not return quoted text that covers multiple lines
# grouping and alternation will solve that
re.findall(pattern, emerson)
```




    ['"Ne te quaesiveris extra."',
     '"But these impulses may be from below, not from above."',
     '"They do not seem to me to be such; but if I am the devil\'s child, I will live then from the devil."',
     '"Go love thy infant; love thy wood-chopper; be good-natured and modest; have that grace; and never varnish your hard, uncharitable ambition with this incredible tenderness for black folk a thousand miles off. Thy love afar is spite at home."',
     '"the foolish face of praise,"',
     '"the height of Rome;"',
     '"I think,"',
     '"I am,"',
     '"Come out unto us."',
     '"What we love that we have, but by desire we bereave ourselves of the love."',
     '"studying a profession,"',
     '"His hidden meaning lies in our endeavors; Our valors are our best gods."',
     '"To the persevering mortal,"',
     '"the blessed Immortals are swift."',
     '"Let not God speak to us, lest we die. Speak thou, speak any man with us, and we will obey."',
     '"It must be somehow that you stole the light from us."',
     '"without abolishing our arms, magazines, commissaries and carriages, until, in imitation of the Roman custom, the soldier should receive his supply of corn, grind it in his hand-mill and bake his bread himself."',
     '"Thy lot or portion of life,"',
     '"is seeking after thee; therefore be at rest from seeking after if."']



## 5. Grouping and Alternation

* `(` start grouped expression
* `)` end grouped expression

### Grouping metacharacters


```python
# if there is one or more capture groups in your pattern
# re.findall() returns only the text inside those groups
# rather than the entire match.
text = 'independent dependent'
pattern = r'(in)?dependent'
re.findall(pattern, text)
```




    ['in', '']




```python
# In Python you need to use a non-capturing group by adding ?: inside the parentheses
pattern = r'(?:in)?dependent'
re.findall(pattern, text)
```




    ['independent', 'dependent']




```python
text = '555-666-57890'
pattern = r'(\d{3})-(\d{3}-\d{4})'

# When your regular expression contains multiple sets of parentheses, re.findall() returns a list of tuples
results = re.findall(pattern, text)
print(results)
# use standard Python indexing to access the data
# [0][0] first tuple in list, first item in tuple
print(results[0][0])
print(results[0][1])
```

    [('555', '666-5789')]
    555
    666-5789



```python
# this could be useful if you wanted to reformat all of the phone numbers matching a particular pattern
pattern = r'(\d{3})-(\d{3})-(\d{4})'
results = re.findall(pattern, text)
print('({}) {}-{}'.format(results[0][0], results[0][1], results[0][2]))
```

    (555) 666-5789


### Alternation metacharacters
* `|` match previous or next expression
    * essentiallly an `OR` operator
        * either match expression on the left **or** match expression on the right
    * leftmost expression gets precedence


```python
text = 'apple orange'
pattern = r'apple|orange'
re.findall(pattern, text)
```




    ['apple', 'orange']




```python
text = 'apple juice apple sauce'
# 'apple juice' or 'apple sauce'
pattern = r'apple (?:juice|sauce)'
re.findall(pattern, text)
```




    ['apple juice', 'apple sauce']




```python
text = 'apple juice apple sauce'
# 'apple juice' or 'sauce'
pattern = r'apple juice|sauce'
re.findall(pattern, text)
```




    ['apple juice', 'sauce']




```python
text = 'February is often misspelled as Febuary'
pattern = r'Feb(?:ru|u)ary'
# it would be more efficient to use r'Febr?uary' for the pattern
re.findall(pattern, text)
```




    ['February', 'Febuary']




```python
# you can also use alternation with repetition
text = 'AABBAACC CCCCBBBB AA BBCCDDEE'
# will match any combination of AA, BB, or CC repeated four times
pattern = r'(?:AA|BB|CC){4}'
re.findall(pattern, text)
```




    ['AABBAACC', 'CCCCBBBB']



### Efficiency when using alternation
* have to give some thought to the way that the regular expression engine is going to make its choices
* doesn't have a concept of what would be a better match
* is eager and doesn't look for other matches later in the string
* put the simplest (most efficient) expression first


```python
# regular expressions are eager to return a result to you
# reads left-to-right and will return the first match it finds
text = 'peanutbutter'
pattern = r'(peanut|peanutbutter)' # returns 'peanut' even though 'peanutbutter' would have matched more
re.findall(pattern, text)
```




    ['peanut']




```python
# regular expressions are greedy by default
pattern = r'peanut(?:butter)?'
re.findall(pattern, text)
```




    ['peanutbutter']




```python
# adding an additional ? will make it lazy
pattern = r'peanut(?:butter)??'
re.findall(pattern, text)
```




    ['peanut']




```python
text = 'abcdefghijklmnopqrstuvwxyz'
pattern = r'(xyz|abc)'
# evaluates the text string character by character and looks for a match
#    does 'a' match the first character in 'xyz' - NO
#    does 'a' match the first character in 'abc' - YES
#       does 'b' match the second character in 'abc' - YES
#          does 'c' match the second character in 'abc' - YES
#          match found!
#
# if at any point a match was not found it would backtrack and continue searching the string for a match
#
# it doesn't just scan to the end of the text looking for 'xyz' before considering 'abc'
# the text may be a multi-page document instead of just a single line
re.search(pattern, text).group()
```




    'abc'



### Challenge: Grouping and alternation


```python
# Match 'myself', 'yourself', 'thyself', but not 'himself', 'herself', or 'itself'
pattern = r'(?:my|your|thy)self'

from collections import Counter # useful for counting values in lists
Counter(re.findall(pattern, emerson, flags = re.I)).items()
```




    dict_items([('thyself', 1), ('yourself', 7), ('myself', 6)])




```python
# match 'good', 'goodness', and 'goods'
pattern = r'good(?:ness|s)?'
results = Counter(re.findall(pattern, emerson, flags = re.I))
df = pd.DataFrame({'keys' : results.keys(), 'values' : results.values()})
df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>keys</th>
      <th>values</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>good</td>
      <td>19</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Good</td>
      <td>2</td>
    </tr>
    <tr>
      <th>2</th>
      <td>goodness</td>
      <td>3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>goods</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>




```python
results['good']
```




    19




```python
# the top 2 words with the most occurrences
results.most_common(2)
```




    [('good', 19), ('goodness', 3)]




```python
# iterate over key-value pairs
for item, count in results.items():
    print('{}: {}'.format(item, count))
```

    good: 19
    Good: 2
    goodness: 3
    goods: 1



```python
# convert the items() dict_items object into a list to use indexing
results = list(results.items())
print('{}: {}'.format(results[0][0], results[0][1]))
```

    good: 19



```python
# Match 'do' or 'does' followed by 'no', 'not', or 'nothing'

# put the most complicated alternatives first so that the engine doesn't just use `no`
# first 'nothing' is checked, then 'not', and finally 'no'
# if no(?:t|thing)? was used it would just return the 'do no'
pattern = r'[Dd]o(?:es)? (?:nothing|not|no)'

results = Counter(re.findall(pattern, emerson))

for item, count in results.items():
    print('{}: {}'.format(item, count))
```

    does not: 16
    Do not: 3
    do not: 14
    do no: 1
    do nothing: 1


## 6. Anchors

### Start and end anchors
* expressions which specify a starting or an ending point that needs to be considered when searching for a pattern
* reference a position, not an actual character
* `^` start of string/line
* `$` end of string/line
* `\A` start of string, never end of line
    * *are newer and may not be supported by older engines*
* `\Z` end of string, never end of line
    * *are newer and may not be supported by older engines*


```python
text = 'this is a string of text'
# search only the start of string
pattern = r'^string'
re.findall(pattern, text)
```




    []




```python
# search only the start of string
pattern = r'^this'
re.findall(pattern, text)
```




    ['this']




```python
# search only the end of string
# note the $ character is placed at the end of the pattern, not the beginning
pattern = r'text$'
re.findall(pattern, text)
```




    ['text']




```python
# from the beginning to the end it must match the pattern
pattern = r'^this$'
print(re.findall(pattern, text))

text = 'this'
print(re.findall(pattern, text))
```

    []
    ['this']



```python
text = '''this
text has the word
this on multiple lines'''
pattern = r'^this'
re.findall(pattern, text)
```




    ['this']




```python
# you need to use the MULTILINE flag to return the occurrences of 'this' at the beginning of other lines
re.findall(pattern, text, flags = re.MULTILINE)
```




    ['this', 'this']




```python
# (?m) tells Python to treat the pattern as multiline
pattern = r'(?m)^this'
re.findall(pattern, text)
```




    ['this', 'this']




```python
# simple validation of email addresses
text = 'someone@nowhere.com-junk'

# this returns a match even though the email address has invalid characters at the end
pattern = r'\w+@\w+\.[a-z]{3}'
print(re.findall(pattern, text))

# adding the ^ and $ characters forces validation of the entire string
pattern = r'^\w+@\w+\.[a-z]{3}$'
print(re.findall(pattern, text))
```

    ['someone@nowhere.com']
    []


### Line breaks and multiline mode
* there are two different modes that we can look at regular expressions in
    * **single-line mode**
        * default
        * `^` and `$` do **not** match at line breaks
        * `\A` and `\Z` do **not** match at line breaks
    * **multi-line mode**
        * `^` and `$` **do** match at line breaks
        * `\A` and `\Z` do **not** match at line breaks


```python
text = '''milk
apple juice
sweet peas
yogurt
sweet corn
applesauce
milkshake
sweet potatoes
'''
pattern = r'[a-z ]+'
re.findall(pattern, text)
```




    ['milk',
     'apple juice',
     'sweet peas',
     'yogurt',
     'sweet corn',
     'applesauce',
     'milkshake',
     'sweet potatoes']




```python
# by default doesn't match any of the subsequent lines
pattern = r'^[a-z ]+'
re.findall(pattern, text)
```




    ['milk']




```python
re.findall(pattern, text, re.MULTILINE)
```




    ['milk',
     'apple juice',
     'sweet peas',
     'yogurt',
     'sweet corn',
     'applesauce',
     'milkshake',
     'sweet potatoes']




```python
# adding a $ at the end works as well since each line matches the expression start to finish
pattern = r'^[a-z ]+$'
re.findall(pattern, text, re.MULTILINE)
```




    ['milk',
     'apple juice',
     'sweet peas',
     'yogurt',
     'sweet corn',
     'applesauce',
     'milkshake',
     'sweet potatoes']




```python
# now referencing the beginning and ending of the entire string
pattern = r'\A[a-z ]+\Z'
re.findall(pattern, text, re.MULTILINE)
```




    []



### Word boundaries
* references a position, not an actual character
* any time there is a shift between a word character and a non-word character there is a boundary
    * word characters: `[A-Za-z0-9_]`
* `\b` word boundary (start/end of word)
* `\B` not a word boundary


```python
text = 'The cat is in the category'

# does not return the occurrence of 'cat' from 'category'
pattern = r'\bcat\b'
re.findall(pattern, text)
```




    ['cat']




```python
# returns the `cat` from category as well
pattern = r'cat'
re.findall(pattern, text)
```




    ['cat', 'cat']




```python
text = 'abc_123 top-notch'
pattern = r'\b\w+\b'

# the dash character is not a word character
re.findall(pattern, text)
```




    ['abc_123', 'top', 'notch']




```python
text = 'New York'
pattern = r'\bNew\b \bYork\b'
re.findall(pattern, text)
```




    ['New York']




```python
text = 'this is a test'
# returns word characters that do not have a word boundary on either side of them
pattern = r'\B\w+\B'
re.findall(pattern, text)
```




    ['hi', 'es']




```python
text = 'Shall I compare thee to a summer\'s day?'

# matches only the 'e' at the end of 'compare' and 'thee'
pattern = r'e\b'
re.findall(pattern, text)
```




    ['e', 'e']




```python
# matches only the stand-alone 'a' not the once in the middle of words
pattern = r'\ba\b'
re.findall(pattern, text)
```




    ['a']



### Challenge: Anchors


```python
# how many paragraphs start with the letter 'I'
```


```python
pattern = r'^I\b'
Counter(re.findall(pattern, emerson, flags = re.MULTILINE))
```




    Counter({'I': 4})




```python
# how many paragraphs end with a question mark
```


```python
pattern = r'\?$'
Counter(re.findall(pattern, emerson, flags = re.MULTILINE))
```




    Counter({'?': 1})




```python
# match all words with exactly 15 letters, including hyphenated words
```


```python
pattern = r'\b[\w\-]{15}\b'
re.findall(pattern, emerson)
```




    ['classifications', 'million-colored', 'thousand-cloven']



## 7. Capturing Groups and Backreferences

### Captures and backreferences
* Whenever you group an expression the engine captures it by default
    * Matched data in parentheses is stored fro later use
* To use the captured text you need to use backreferences
    * `\1` Backreference to first capture
    * `\2` Backreference to second capture
    * `\3` Backreference to third capture
* Most regular expression engines support `\1` through `\9`
    * some support through `\99`
* Cannot be used inside character classes


```python
text = 'ab:cd:ef:ef:cd:ab'
pattern = r'(ab):(cd):(ef):\3:\2:\1'

# finditer() is useful when using groups
for match in re.finditer(pattern, text):
    print(match.group(0)) # print matched string    
    for i in match.groups(): # print the groups captured
        print(i)
```

    ab:cd:ef:ef:cd:ab
    ab
    cd
    ef



```python
# not using the backreference would return matches even when the tags are a mismatch
pattern = r'<(i|em)>.+?<\/(i|em)>'

# '<i>text 3</em>' returned even though it is a mismatch
for match in re.finditer(pattern, text):
    print(match.group(0))    
```


```python
# text 3, because it uses a combination of 'i' and 'em' tags will not match now
# the backreference is a reference to the literal text that was captured NOT to the expression that captured it
text = r'<i>text 1</i> <em>text 2</em> <i>text 3</em>'

# The ? tells the engine to match as little as possible before moving to the next part of the pattern
# otherwise, if the string had multiple </i> tags (since 'i' is captured) it would match to the last one
# This entire string would be matched '<i>text 1</i> <em>text 2</em> <i>text 3</i>' with default behavior
pattern = r'<(i|em)>.+?<\/\1>'

for match in re.finditer(pattern, text):
    print(match.group(0))  
```

    <i>text 1</i>
    <em>text 2</em>



```python
# search for duplicated words
text = '''Paris in the 
the spring.'''
pattern = r'\b(\w+)\b\s+\1'

for match in re.finditer(pattern, text):
    print('\'{}\''.format(match.group(0)))
```

    'the 
    the'


### Backreferences to optional expressions


```python
text = 'B AB'
pattern = r'(A?)B' # Group containing an Optional Character
# ensures the group always returns a string, even if it's empty

for match in re.finditer(pattern, text):
    print('Match: {}'.format(match.group(0)))
    for i in match.groups():
        print('\tgroup: {}'.format(i))
```

    Match: B
    	group: 
    Match: AB
    	group: A



```python
pattern = r'(A)?B' # Optional Group
# use if you want to know if the pattern was actually there

for match in re.finditer(pattern, text):
    print('Match: {}'.format(match.group(0)))
    for i in match.groups():
        print('\tgroup: {}'.format(i))
```

    Match: B
    	group: None
    Match: AB
    	group: A



```python
# the zero-width character can be used as a backreference
text = '''typical & political
atypical & apolitical'''
pattern = r'(a?)typical & \1political'

for match in re.finditer(pattern, text):
    print('Match: {}'.format(match.group(0)))
    for i in match.groups():
        print('\tgroup: {}'.format(i))
```

    Match: typical & political
    	group: 
    Match: atypical & apolitical
    	group: a



```python
# an optional group of characters is only captured if it matches
text = 'willing and unwilling'
pattern = r'(un)?willing'

for match in re.finditer(pattern, text):
    print('Match: {}'.format(match.group(0)))
    for i in match.groups():
        print('\tgroup: {}'.format(i))
```

    Match: willing
    	group: None
    Match: unwilling
    	group: un



```python
# an optional group that did not match has no backreference
text = '''unwilling & unable
willing & able'''
pattern = r'(un)?willing & \1able' 

# does not match 'willing & able' since no group was captured
for match in re.finditer(pattern, text):
    print('Match: {}'.format(match.group(0)))
    for i in match.groups():
        print('\tgroup: {}'.format(i))
```

    Match: unwilling & unable
    	group: un



```python
# Solution: capture the optional group
# does not match 'willing & able' since no group was captured
pattern = r'((un)?)willing & \1able'

for match in re.finditer(pattern, text):
    print('Match: {}'.format(match.group(0)))
    for i in match.groups():
        print('\tgroup: {}'.format(i))
```

    Match: unwilling & unable
    	group: un
    	group: un
    Match: willing & able
    	group: 
    	group: None


### Find and replace using backreferences


```python
text = 'I love hot coffee.'
pattern = r'(love) hot'

re.sub(pattern, '*\\1*', text)
```




    'I *love* coffee.'




```python
text = '''Stephen King
Margaret Atwood
Douglas Adams'''
# pattern = r'(\b\w+?\b)\s+(\b\w+?\b)' # this pattern works without having to set the flag
pattern = r'^(.+) (.+)$'
print(re.sub(pattern, r'\2, \1', text, flags = re.MULTILINE))
```

    King, Stephen
    Atwood, Margaret
    Adams, Douglas


### Non-capturing group expressions
* can prevent automatic capturing of a group expression
* useful for groups which will not be used by backreferences
* `?:` disable capturing for this group


```python
text = '''I like pizza.
I love coffee.'''
# both groups are captured by default
pattern = r'I (like|love) (.+)\.'
for match in re.finditer(pattern, text):
    print('Match: {}'.format(match.group(0)))
    for i in match.groups():
        print('\tgroup: {}'.format(i))
```

    Match: I like pizza.
    	group: like
    	group: pizza
    Match: I love coffee.
    	group: love
    	group: coffee



```python
# capturing of the first group is disabled
pattern = r'I (?:like|love) (.+)\.'
for match in re.finditer(pattern, text):
    print('Match: {}'.format(match.group(0)))
    for i in match.groups():
        print('\tgroup: {}'.format(i))
```

    Match: I like pizza.
    	group: pizza
    Match: I love coffee.
    	group: coffee


### Challenge: Backreferences


```python
# Manually parse a csv file
# For each president output in the following syntax 'start-end: full name'

# need the {0,4} for the current president, that does not have an end date
# pattern = r'^(?:\d+),([\w\s.]+),(\d+),(\d+)' # my attempt
pattern = r'\d+,(.+),(\d{4}),(\d{0,4}),.+,.+,https:.+$' # solution
for match in re.finditer(pattern, presidents, flags = re.MULTILINE):
    print('{}-{}: {}'.format(match.group(2), match.group(3), match.group(1)))
```

    1789-1797: George Washington
    1797-1801: John Adams
    1801-1809: Thomas Jefferson
    1809-1817: James Madison
    1817-1825: James Monroe
    1825-1829: John Q. Adams
    1829-1837: Andrew Jackson
    1837-1841: Martin Van Buren
    1841-1841: William H. Harrison
    1841-1845: John Tyler
    1845-1849: James K. Polk
    1849-1850: Zachary Taylor
    1850-1853: Millard Fillmore
    1853-1857: Franklin Pierce
    1857-1861: James Buchanan
    1861-1865: Abraham Lincoln
    1865-1869: Andrew Johnson
    1869-1877: Ulysses S. Grant
    1877-1881: Rutherford B. Hayes
    1881-1881: James A. Garfield
    1881-1885: Chester A. Arthur
    1885-1889: Grover Cleveland
    1889-1893: Benjamin Harrison
    1893-1897: Grover Cleveland
    1897-1901: William McKinley
    1901-1909: Theodore Roosevelt
    1909-1913: William H. Taft
    1913-1921: Woodrow Wilson
    1921-1923: Warren G. Harding
    1923-1929: Calvin Coolidge
    1929-1933: Herbert Hoover
    1933-1945: Franklin D. Roosevelt
    1945-1953: Harry S. Truman
    1953-1961: Dwight D. Eisenhower
    1961-1963: John F. Kennedy
    1963-1969: Lyndon B. Johnson
    1969-1974: Richard Nixon
    1974-1977: Gerald Ford
    1977-1981: Jimmy Carter
    1981-1989: Ronald Reagan
    1989-1993: George H. W. Bush
    1993-2001: Bill Clinton
    2001-2009: George W. Bush
    2009-2017: Barack Obama
    2017-2021: Donald J. Trump
    2021-: Joe Biden


## 8. Lookaround Assertions

### Positive lookahead assertions
* Returns true if a grouped expression is ahead of the current position
    * only return True/False, not matched or captured text
* Defines an additional condition that must be met
* Group
    * is not included in the match
    * is not captured
* `?=` group is a positive lookahead assertion
    * `?` modifies the group
    * `=` says to make the group into a look ahead assertion
        * you are asserting it should be equal to something
* lookahead assertions examine the same characters as the expression


```python
# matches '2025' in both dates
text = '2025-01-01, 01-01-2025'
pattern = r'\d{4}'

for match in re.finditer(pattern, text):
    print('Match: {}'.format(match.group(0)))
    for i in match.groups():
        print('\tgroup: {}'.format(i))
```

    Match: 2025
    Match: 2025



```python
# matches '2025' only when date matches the format '####-##-##'
# group is not captured
# lookahead assertions are a handy way to say find this, but when you do also make sure this is true

# Leading Lookahead
# not as efficient
# validates the entire date pattern twice (once in the lookahead, once when the engine moves forward)
# at every single character in your string, the engine stops and tries to look ahead to match a full 10-character date
pattern = r'(?=\d{4}-\d{2}-\d{2})\d{4}' 

# Trailing Lookahead
# more efficient; only performs the complex lookahead check after it finds 4 digits
pattern2 = r'\d{4}(?=-\d{2}-\d{2})' 

for match in re.finditer(pattern, text):
    print('Match: {}'.format(match.group(0)))
    for i in match.groups():
        print('\tgroup: {}'.format(i))
```

    Match: 2025



```python
# find all the words followed by punctuation, but not including the punctuation itself
pattern = r'\b[A-Za-z\']+\b(?=[,;:.?!])'
re.findall(pattern, shakespeare)
```




    ['day',
     'temperate',
     'May',
     'date',
     'shines',
     'dimmed',
     'declines',
     'chance',
     'untrimmed',
     'fade',
     "ow'st",
     'shade',
     "grow'st",
     'breathe',
     'see',
     'this',
     'thee']




```python
# match any word that is used again later in the text
pattern = r'(\b\w+\b)(?=.*\1)'
re.findall(pattern, shakespeare)
```




    ['a', 'more', 's', 'a', 'is', 'fair', 's', 'in', 'can', 'this']



### Negative lookahead assertions
* True if a grouped expression is not ahead of the current position
* `?!` group is a negative lookahead assertion


```python
# match words that do not start with 're'
text = 'bread red one'
pattern = r'\b(?!re)\w+\b'
re.findall(pattern, text)
```




    ['bread', 'one']




```python
# match words that do not end in 'er' -- this does NOT work
# lookaheads only check what is directly in front of the engine's current position

# the regular expression engine is greedy and looks for any possible way to make the pattern true
# initially the regular expression engine gobbles up the entire word 'butter'
# the engine looks at what follows 'butter' 
#    since the word is over, it sees a space or the end of the string. 
#    a space is not "er", so the lookahead is satisfied.
# lookbehind assertions are required to prevent this from happening
text = 'bread butter'
pattern = r'\b\w+(?!er)\b'
re.findall(pattern, text)
```




    ['bread', 'butter']




```python
# match all occurrences of 'green' except when it is followed by 'grass'
text = 'The green frog chased the green bug in the green grass.'
pattern = r'\bgreen\b(?! grass)'
re.sub(pattern, 'BLUE', text)
```




    'The BLUE frog chased the BLUE bug in the green grass.'




```python
# match last occurrence of 'green'
pattern = r'\bgreen\b(?!.*green)' # as long as green doesn't happen somewhere later
pattern2 = r'(\bgreen\b)(?!.*\1)' # could also capture green and use a backreference
re.sub(pattern, 'BLUE', text)
```




    'The green frog chased the green bug in the BLUE grass.'




```python
# Do not matche lines that begin with a code comment indicator '#'
pattern = r'^(?!\s*#).+$'
text = '''# line one
# line two
line three
line four'''
re.findall(pattern, text, flags = re.MULTILINE)
```




    ['line three', 'line four']



### Lookbehind assertions
* True if a grouped expresion is (or is not) behind the current position
* Similar to lookahead assertions
* Group is not included in the match or captured
* `?<=` group is a positive lookbehind assertion
* `?<!` group is a negative lookbehind assertion


```python
# matches 'ball' in 'baseball' but not 'football'
text = 'I like baseball and football.'
pattern = r'(?<=base)ball'
re.findall(pattern, text)
```




    ['ball']




```python
# matches 'ball' in 'football' but not 'baseball'
pattern = r'(?<!base)ball'
re.findall(pattern, text)
```




    ['ball']




```python
# match all words after 'green'
text = 'The green frog chased the green bug in the green grass.'
pattern = r'(?<=\bgreen\s)\b\w+'
re.findall(pattern, text)
```




    ['frog', 'bug', 'grass']




```python
# match words that do not end in 'er'
# this works as intended in contrast to the lookahead assertion which did not
text = 'bread butter'
pattern = r'\b\w+(?<!er)\b'
re.findall(pattern, text)
```




    ['bread']




```python
text = '''John Smith
Mr. Smith
Ms. Smith
Mrs. Smith
Mr. John Smith'''
pattern = r'(?<=(?:Mr|Ms)\. )Smith' # this works because 'Mr' and 'Ms' are the same number of characters
pattern2 = r'(?<=(?:Mr|Ms|Mrs)\. )Smith' # this produces an error: look-behind requires fixed-width pattern
re.findall(pattern, text)
```




    ['Smith', 'Smith']



### Multiple lookaround assertions
* multiple assertions impact performance
    * multiple layers of regular expressions to check
    * use anchors, especially at the start, to reduce searching
    * put the simplest/fastest assertions first


```python
# matches words starting with 'sea'
# asserts 6 or more characters
# asserts not 'seashore'
pattern = r'\b(?=\w{6,})sea(?!shore)\w+\b'
text = 'seat, season, seagull, seaside, seashore'
re.findall(pattern, text)
```




    ['season', 'seagull', 'seaside']




```python
# find film titles with three or more words where every word is capitalized
text = '''Mad Max Fury Road
John Wick
Mission Impossible Fallout
Top Gun Maverick
Edge of Tomorrow'''

# match will be everything in the title from beginning to end
# assert word boundary, some number of characters, and a space at least twice
# assert not that there are NOT some characters then a space and a lowercase letter
pattern = r'^(?=(?:\b\w+ ){2,})(?!.+\s[a-z]).+$'
re.findall(pattern, text, flags = re.MULTILINE)
```




    ['Mad Max Fury Road', 'Mission Impossible Fallout', 'Top Gun Maverick']




```python
text = 'this is some text with \'single\' and "double" quotes.'
# Match using bookends
# use both a lookahead and lookbehind assertion to identify characters that surround content
pattern = r'(?<=["\']).+?(?=["\'])' # match everything inside quotes
re.findall(pattern, text)
```




    ['single', ' and ', 'double']




```python
pattern = r'(?<=["\'])[^"\']+(?=["\'])'
re.findall(pattern, text)
```




    ['single', ' and ', 'double']




```python
text = 'camelCase'
# matches the zero-width position before a camel-case letter
pattern = r'(?<=[a-z])(?=[A-Z])'
re.sub(pattern, ' ', text)
```




    'camel Case'




```python
# password validation
# password must be 10 or more characters ^.{10,}$
# password must include an uppercase letter (?=.*[A-Z])
# password must include a lowercase letter (?=.*[a-z])
# password must include a number (?=.*\d)
# password must include a symbol (?=.*[!@#$%^&*])
text = '''PbsBox9moM
!5MsLHXO6i
LYRRLSFQVC
fszumyvjte
7211484587'''
pattern = r'^(?=.*[A-Z])(?=.*[a-z])(?=.*\d)(?=.*[!@#$%^&*]).{10,}$'
re.findall(pattern, text, flags = re.MULTILINE)
```




    ['!5MsLHXO6i']




```python
# add commas to numbers
#    identifies groups of 1 to 3 digits that are followed by exactly one or more sets of three digits
#    provided those digits aren't followed by more digits
text = '1000000000' # more than 4 decimal places will add a comma
pattern = r'(\d{1,3})(?=(\d{3})+(?!\d))'
re.sub(pattern, r'\1,', text)
```




    '1,000,000,000'



### Challenge: Lookaround assertions


```python
# Match the line for every U.S. President
pattern = r'^\d{1,2},.+,\d{4},(?:\d{4})?,.+,.+,.+$'
re.findall(pattern, presidents, flags = re.MULTILINE)
```




    ['1,George Washington,1789,1797,Independent,Virginia,https://en.wikipedia.org/wiki/George_Washington',
     '2,John Adams,1797,1801,Federalist,Massachusetts,https://en.wikipedia.org/wiki/John_Adams',
     '3,Thomas Jefferson,1801,1809,Democratic-Republican,Virginia,https://en.wikipedia.org/wiki/Thomas_Jefferson',
     '4,James Madison,1809,1817,Democratic-Republican,Virginia,https://en.wikipedia.org/wiki/James_Madison',
     '5,James Monroe,1817,1825,Democratic-Republican,Virginia,https://en.wikipedia.org/wiki/James_Monroe',
     '6,John Q. Adams,1825,1829,Democratic-Republican/National Republican,Massachusetts,https://en.wikipedia.org/wiki/John_Quincy_Adams',
     '7,Andrew Jackson,1829,1837,Democratic,Tennessee,https://en.wikipedia.org/wiki/Andrew_Jackson',
     '8,Martin Van Buren,1837,1841,Democratic,New York,https://en.wikipedia.org/wiki/Martin_Van_Buren',
     '9,William H. Harrison,1841,1841,Whig,Ohio,https://en.wikipedia.org/wiki/William_Henry_Harrison',
     '10,John Tyler,1841,1845,Whig,Virginia,https://en.wikipedia.org/wiki/John_Tyler',
     '11,James K. Polk,1845,1849,Democratic,Tennessee,https://en.wikipedia.org/wiki/James_K._Polk',
     '12,Zachary Taylor,1849,1850,Whig,Louisiana,https://en.wikipedia.org/wiki/Zachary_Taylor',
     '13,Millard Fillmore,1850,1853,Whig,New York,https://en.wikipedia.org/wiki/Millard_Fillmore',
     '14,Franklin Pierce,1853,1857,Democratic,New Hampshire,https://en.wikipedia.org/wiki/Franklin_Pierce',
     '15,James Buchanan,1857,1861,Democratic,Pennsylvania,https://en.wikipedia.org/wiki/James_Buchanan',
     '16,Abraham Lincoln,1861,1865,Republican/National Union,Illinois,https://en.wikipedia.org/wiki/Abraham_Lincoln',
     '17,Andrew Johnson,1865,1869,Democratic/National Union,Tennessee,https://en.wikipedia.org/wiki/Andrew_Johnson',
     '18,Ulysses S. Grant,1869,1877,Republican,Ohio,https://en.wikipedia.org/wiki/Ulysses_S._Grant',
     '19,Rutherford B. Hayes,1877,1881,Republican,Ohio,https://en.wikipedia.org/wiki/Rutherford_B._Hayes',
     '20,James A. Garfield,1881,1881,Republican,Ohio,https://en.wikipedia.org/wiki/James_A._Garfield',
     '21,Chester A. Arthur,1881,1885,Republican,New York,https://en.wikipedia.org/wiki/Chester_A._Arthur',
     '22,Grover Cleveland,1885,1889,Democratic,New York,https://en.wikipedia.org/wiki/Grover_Cleveland',
     '23,Benjamin Harrison,1889,1893,Republican,Indiana,https://en.wikipedia.org/wiki/Benjamin_Harrison',
     '24,Grover Cleveland,1893,1897,Democratic,New York,https://en.wikipedia.org/wiki/Grover_Cleveland',
     '25,William McKinley,1897,1901,Republican,Ohio,https://en.wikipedia.org/wiki/William_McKinley',
     '26,Theodore Roosevelt,1901,1909,Republican,New York,https://en.wikipedia.org/wiki/Theodore_Roosevelt',
     '27,William H. Taft,1909,1913,Republican,Ohio,https://en.wikipedia.org/wiki/William_Howard_Taft',
     '28,Woodrow Wilson,1913,1921,Democratic,New Jersey,https://en.wikipedia.org/wiki/Woodrow_Wilson',
     '29,Warren G. Harding,1921,1923,Republican,Ohio,https://en.wikipedia.org/wiki/Warren_G._Harding',
     '30,Calvin Coolidge,1923,1929,Republican,Massachusetts,https://en.wikipedia.org/wiki/Calvin_Coolidge',
     '31,Herbert Hoover,1929,1933,Republican,Iowa,https://en.wikipedia.org/wiki/Herbert_Hoover',
     '32,Franklin D. Roosevelt,1933,1945,Democratic,New York,https://en.wikipedia.org/wiki/Franklin_D._Roosevelt',
     '33,Harry S. Truman,1945,1953,Democratic,Missouri,https://en.wikipedia.org/wiki/Harry_S._Truman',
     '34,Dwight D. Eisenhower,1953,1961,Republican,Texas,https://en.wikipedia.org/wiki/Dwight_D._Eisenhower',
     '35,John F. Kennedy,1961,1963,Democratic,Massachusetts,https://en.wikipedia.org/wiki/John_F._Kennedy',
     '36,Lyndon B. Johnson,1963,1969,Democratic,Texas,https://en.wikipedia.org/wiki/Lyndon_B._Johnson',
     '37,Richard Nixon,1969,1974,Republican,California,https://en.wikipedia.org/wiki/Richard_Nixon',
     '38,Gerald Ford,1974,1977,Republican,Michigan,https://en.wikipedia.org/wiki/Gerald_Ford',
     '39,Jimmy Carter,1977,1981,Democratic,Georgia,https://en.wikipedia.org/wiki/Jimmy_Carter',
     '40,Ronald Reagan,1981,1989,Republican,California,https://en.wikipedia.org/wiki/Ronald_Reagan',
     '41,George H. W. Bush,1989,1993,Republican,Texas,https://en.wikipedia.org/wiki/George_H._W._Bush',
     '42,Bill Clinton,1993,2001,Democratic,Arkansas,https://en.wikipedia.org/wiki/Bill_Clinton',
     '43,George W. Bush,2001,2009,Republican,Texas,https://en.wikipedia.org/wiki/George_W._Bush',
     '44,Barack Obama,2009,2017,Democratic,Illinois,https://en.wikipedia.org/wiki/Barack_Obama',
     '45,Donald J. Trump,2017,2021,Republican,New York,https://en.wikipedia.org/wiki/Donald_Trump',
     '46,Joe Biden,2021,,Democratic,Delaware,https://en.wikipedia.org/wiki/Joe_Biden']




```python
# Presidents whose home state was not Virginia or Massachusetts
pattern = r'^\d{1,2},.+,\d{4},(?:\d{4})?,.+,(?!Virginia|Massachusetts).+,.+$'
re.findall(pattern, presidents, flags = re.MULTILINE)
```




    ['7,Andrew Jackson,1829,1837,Democratic,Tennessee,https://en.wikipedia.org/wiki/Andrew_Jackson',
     '8,Martin Van Buren,1837,1841,Democratic,New York,https://en.wikipedia.org/wiki/Martin_Van_Buren',
     '9,William H. Harrison,1841,1841,Whig,Ohio,https://en.wikipedia.org/wiki/William_Henry_Harrison',
     '11,James K. Polk,1845,1849,Democratic,Tennessee,https://en.wikipedia.org/wiki/James_K._Polk',
     '12,Zachary Taylor,1849,1850,Whig,Louisiana,https://en.wikipedia.org/wiki/Zachary_Taylor',
     '13,Millard Fillmore,1850,1853,Whig,New York,https://en.wikipedia.org/wiki/Millard_Fillmore',
     '14,Franklin Pierce,1853,1857,Democratic,New Hampshire,https://en.wikipedia.org/wiki/Franklin_Pierce',
     '15,James Buchanan,1857,1861,Democratic,Pennsylvania,https://en.wikipedia.org/wiki/James_Buchanan',
     '16,Abraham Lincoln,1861,1865,Republican/National Union,Illinois,https://en.wikipedia.org/wiki/Abraham_Lincoln',
     '17,Andrew Johnson,1865,1869,Democratic/National Union,Tennessee,https://en.wikipedia.org/wiki/Andrew_Johnson',
     '18,Ulysses S. Grant,1869,1877,Republican,Ohio,https://en.wikipedia.org/wiki/Ulysses_S._Grant',
     '19,Rutherford B. Hayes,1877,1881,Republican,Ohio,https://en.wikipedia.org/wiki/Rutherford_B._Hayes',
     '20,James A. Garfield,1881,1881,Republican,Ohio,https://en.wikipedia.org/wiki/James_A._Garfield',
     '21,Chester A. Arthur,1881,1885,Republican,New York,https://en.wikipedia.org/wiki/Chester_A._Arthur',
     '22,Grover Cleveland,1885,1889,Democratic,New York,https://en.wikipedia.org/wiki/Grover_Cleveland',
     '23,Benjamin Harrison,1889,1893,Republican,Indiana,https://en.wikipedia.org/wiki/Benjamin_Harrison',
     '24,Grover Cleveland,1893,1897,Democratic,New York,https://en.wikipedia.org/wiki/Grover_Cleveland',
     '25,William McKinley,1897,1901,Republican,Ohio,https://en.wikipedia.org/wiki/William_McKinley',
     '26,Theodore Roosevelt,1901,1909,Republican,New York,https://en.wikipedia.org/wiki/Theodore_Roosevelt',
     '27,William H. Taft,1909,1913,Republican,Ohio,https://en.wikipedia.org/wiki/William_Howard_Taft',
     '28,Woodrow Wilson,1913,1921,Democratic,New Jersey,https://en.wikipedia.org/wiki/Woodrow_Wilson',
     '29,Warren G. Harding,1921,1923,Republican,Ohio,https://en.wikipedia.org/wiki/Warren_G._Harding',
     '31,Herbert Hoover,1929,1933,Republican,Iowa,https://en.wikipedia.org/wiki/Herbert_Hoover',
     '32,Franklin D. Roosevelt,1933,1945,Democratic,New York,https://en.wikipedia.org/wiki/Franklin_D._Roosevelt',
     '33,Harry S. Truman,1945,1953,Democratic,Missouri,https://en.wikipedia.org/wiki/Harry_S._Truman',
     '34,Dwight D. Eisenhower,1953,1961,Republican,Texas,https://en.wikipedia.org/wiki/Dwight_D._Eisenhower',
     '36,Lyndon B. Johnson,1963,1969,Democratic,Texas,https://en.wikipedia.org/wiki/Lyndon_B._Johnson',
     '37,Richard Nixon,1969,1974,Republican,California,https://en.wikipedia.org/wiki/Richard_Nixon',
     '38,Gerald Ford,1974,1977,Republican,Michigan,https://en.wikipedia.org/wiki/Gerald_Ford',
     '39,Jimmy Carter,1977,1981,Democratic,Georgia,https://en.wikipedia.org/wiki/Jimmy_Carter',
     '40,Ronald Reagan,1981,1989,Republican,California,https://en.wikipedia.org/wiki/Ronald_Reagan',
     '41,George H. W. Bush,1989,1993,Republican,Texas,https://en.wikipedia.org/wiki/George_H._W._Bush',
     '42,Bill Clinton,1993,2001,Democratic,Arkansas,https://en.wikipedia.org/wiki/Bill_Clinton',
     '43,George W. Bush,2001,2009,Republican,Texas,https://en.wikipedia.org/wiki/George_W._Bush',
     '44,Barack Obama,2009,2017,Democratic,Illinois,https://en.wikipedia.org/wiki/Barack_Obama',
     '45,Donald J. Trump,2017,2021,Republican,New York,https://en.wikipedia.org/wiki/Donald_Trump',
     '46,Joe Biden,2021,,Democratic,Delaware,https://en.wikipedia.org/wiki/Joe_Biden']




```python
# Presidents whose last name is longer than 7 characters
```


```python
pattern = r'^\d{1,2},[A-Za-z.\s]+?\s[A-Za-z\s]{8,},\d{4},(?:\d{4})?,.+,.+,.+$'

# could also be written as an assertion
# from here to the comma this should be true
pattern2 = r'^\d{1,2},(?=[A-Za-z.\s]+?\s[A-Za-z\s]{8,},).+,\d{4},(?:\d{4})?,.+,.+,.+$'
re.findall(pattern, presidents, flags = re.MULTILINE)
```




    ['1,George Washington,1789,1797,Independent,Virginia,https://en.wikipedia.org/wiki/George_Washington',
     '3,Thomas Jefferson,1801,1809,Democratic-Republican,Virginia,https://en.wikipedia.org/wiki/Thomas_Jefferson',
     '8,Martin Van Buren,1837,1841,Democratic,New York,https://en.wikipedia.org/wiki/Martin_Van_Buren',
     '9,William H. Harrison,1841,1841,Whig,Ohio,https://en.wikipedia.org/wiki/William_Henry_Harrison',
     '13,Millard Fillmore,1850,1853,Whig,New York,https://en.wikipedia.org/wiki/Millard_Fillmore',
     '15,James Buchanan,1857,1861,Democratic,Pennsylvania,https://en.wikipedia.org/wiki/James_Buchanan',
     '20,James A. Garfield,1881,1881,Republican,Ohio,https://en.wikipedia.org/wiki/James_A._Garfield',
     '22,Grover Cleveland,1885,1889,Democratic,New York,https://en.wikipedia.org/wiki/Grover_Cleveland',
     '23,Benjamin Harrison,1889,1893,Republican,Indiana,https://en.wikipedia.org/wiki/Benjamin_Harrison',
     '24,Grover Cleveland,1893,1897,Democratic,New York,https://en.wikipedia.org/wiki/Grover_Cleveland',
     '25,William McKinley,1897,1901,Republican,Ohio,https://en.wikipedia.org/wiki/William_McKinley',
     '26,Theodore Roosevelt,1901,1909,Republican,New York,https://en.wikipedia.org/wiki/Theodore_Roosevelt',
     '30,Calvin Coolidge,1923,1929,Republican,Massachusetts,https://en.wikipedia.org/wiki/Calvin_Coolidge',
     '32,Franklin D. Roosevelt,1933,1945,Democratic,New York,https://en.wikipedia.org/wiki/Franklin_D._Roosevelt',
     '34,Dwight D. Eisenhower,1953,1961,Republican,Texas,https://en.wikipedia.org/wiki/Dwight_D._Eisenhower']




```python
# Presidents whose name does not include a middle initial
pattern = r'^\d{1,2},[A-Za-z\s]+,\d{4},(?:\d{4})?,.+,.+,.+$'
pattern2 = r'^\d{1,2},[^.]+,\d{4},(?:\d{4})?,.+,.+,.+$' # alternative
pattern3 = r'^\d{1,2},(?=[^.]+,).+,\d{4},(?:\d{4})?,.+,.+,.+$' # written as an assertion
re.findall(pattern, presidents, flags = re.MULTILINE)
```




    ['1,George Washington,1789,1797,Independent,Virginia,https://en.wikipedia.org/wiki/George_Washington',
     '2,John Adams,1797,1801,Federalist,Massachusetts,https://en.wikipedia.org/wiki/John_Adams',
     '3,Thomas Jefferson,1801,1809,Democratic-Republican,Virginia,https://en.wikipedia.org/wiki/Thomas_Jefferson',
     '4,James Madison,1809,1817,Democratic-Republican,Virginia,https://en.wikipedia.org/wiki/James_Madison',
     '5,James Monroe,1817,1825,Democratic-Republican,Virginia,https://en.wikipedia.org/wiki/James_Monroe',
     '7,Andrew Jackson,1829,1837,Democratic,Tennessee,https://en.wikipedia.org/wiki/Andrew_Jackson',
     '8,Martin Van Buren,1837,1841,Democratic,New York,https://en.wikipedia.org/wiki/Martin_Van_Buren',
     '10,John Tyler,1841,1845,Whig,Virginia,https://en.wikipedia.org/wiki/John_Tyler',
     '12,Zachary Taylor,1849,1850,Whig,Louisiana,https://en.wikipedia.org/wiki/Zachary_Taylor',
     '13,Millard Fillmore,1850,1853,Whig,New York,https://en.wikipedia.org/wiki/Millard_Fillmore',
     '14,Franklin Pierce,1853,1857,Democratic,New Hampshire,https://en.wikipedia.org/wiki/Franklin_Pierce',
     '15,James Buchanan,1857,1861,Democratic,Pennsylvania,https://en.wikipedia.org/wiki/James_Buchanan',
     '16,Abraham Lincoln,1861,1865,Republican/National Union,Illinois,https://en.wikipedia.org/wiki/Abraham_Lincoln',
     '17,Andrew Johnson,1865,1869,Democratic/National Union,Tennessee,https://en.wikipedia.org/wiki/Andrew_Johnson',
     '22,Grover Cleveland,1885,1889,Democratic,New York,https://en.wikipedia.org/wiki/Grover_Cleveland',
     '23,Benjamin Harrison,1889,1893,Republican,Indiana,https://en.wikipedia.org/wiki/Benjamin_Harrison',
     '24,Grover Cleveland,1893,1897,Democratic,New York,https://en.wikipedia.org/wiki/Grover_Cleveland',
     '25,William McKinley,1897,1901,Republican,Ohio,https://en.wikipedia.org/wiki/William_McKinley',
     '26,Theodore Roosevelt,1901,1909,Republican,New York,https://en.wikipedia.org/wiki/Theodore_Roosevelt',
     '28,Woodrow Wilson,1913,1921,Democratic,New Jersey,https://en.wikipedia.org/wiki/Woodrow_Wilson',
     '30,Calvin Coolidge,1923,1929,Republican,Massachusetts,https://en.wikipedia.org/wiki/Calvin_Coolidge',
     '31,Herbert Hoover,1929,1933,Republican,Iowa,https://en.wikipedia.org/wiki/Herbert_Hoover',
     '37,Richard Nixon,1969,1974,Republican,California,https://en.wikipedia.org/wiki/Richard_Nixon',
     '38,Gerald Ford,1974,1977,Republican,Michigan,https://en.wikipedia.org/wiki/Gerald_Ford',
     '39,Jimmy Carter,1977,1981,Democratic,Georgia,https://en.wikipedia.org/wiki/Jimmy_Carter',
     '40,Ronald Reagan,1981,1989,Republican,California,https://en.wikipedia.org/wiki/Ronald_Reagan',
     '42,Bill Clinton,1993,2001,Democratic,Arkansas,https://en.wikipedia.org/wiki/Bill_Clinton',
     '44,Barack Obama,2009,2017,Democratic,Illinois,https://en.wikipedia.org/wiki/Barack_Obama',
     '46,Joe Biden,2021,,Democratic,Delaware,https://en.wikipedia.org/wiki/Joe_Biden']




```python
# Presidents whose term in office began on or after 1900
pattern = r'^\d{1,2},.+,(?:19|20)\d{2},(?:\d{4})?,.+,.+,.+$'
re.findall(pattern, presidents, flags = re.MULTILINE)
```




    ['26,Theodore Roosevelt,1901,1909,Republican,New York,https://en.wikipedia.org/wiki/Theodore_Roosevelt',
     '27,William H. Taft,1909,1913,Republican,Ohio,https://en.wikipedia.org/wiki/William_Howard_Taft',
     '28,Woodrow Wilson,1913,1921,Democratic,New Jersey,https://en.wikipedia.org/wiki/Woodrow_Wilson',
     '29,Warren G. Harding,1921,1923,Republican,Ohio,https://en.wikipedia.org/wiki/Warren_G._Harding',
     '30,Calvin Coolidge,1923,1929,Republican,Massachusetts,https://en.wikipedia.org/wiki/Calvin_Coolidge',
     '31,Herbert Hoover,1929,1933,Republican,Iowa,https://en.wikipedia.org/wiki/Herbert_Hoover',
     '32,Franklin D. Roosevelt,1933,1945,Democratic,New York,https://en.wikipedia.org/wiki/Franklin_D._Roosevelt',
     '33,Harry S. Truman,1945,1953,Democratic,Missouri,https://en.wikipedia.org/wiki/Harry_S._Truman',
     '34,Dwight D. Eisenhower,1953,1961,Republican,Texas,https://en.wikipedia.org/wiki/Dwight_D._Eisenhower',
     '35,John F. Kennedy,1961,1963,Democratic,Massachusetts,https://en.wikipedia.org/wiki/John_F._Kennedy',
     '36,Lyndon B. Johnson,1963,1969,Democratic,Texas,https://en.wikipedia.org/wiki/Lyndon_B._Johnson',
     '37,Richard Nixon,1969,1974,Republican,California,https://en.wikipedia.org/wiki/Richard_Nixon',
     '38,Gerald Ford,1974,1977,Republican,Michigan,https://en.wikipedia.org/wiki/Gerald_Ford',
     '39,Jimmy Carter,1977,1981,Democratic,Georgia,https://en.wikipedia.org/wiki/Jimmy_Carter',
     '40,Ronald Reagan,1981,1989,Republican,California,https://en.wikipedia.org/wiki/Ronald_Reagan',
     '41,George H. W. Bush,1989,1993,Republican,Texas,https://en.wikipedia.org/wiki/George_H._W._Bush',
     '42,Bill Clinton,1993,2001,Democratic,Arkansas,https://en.wikipedia.org/wiki/Bill_Clinton',
     '43,George W. Bush,2001,2009,Republican,Texas,https://en.wikipedia.org/wiki/George_W._Bush',
     '44,Barack Obama,2009,2017,Democratic,Illinois,https://en.wikipedia.org/wiki/Barack_Obama',
     '45,Donald J. Trump,2017,2021,Republican,New York,https://en.wikipedia.org/wiki/Donald_Trump',
     '46,Joe Biden,2021,,Democratic,Delaware,https://en.wikipedia.org/wiki/Joe_Biden']




```python
pattern = r'^\d{1,2},(?=[^.]+,)(?=[A-Za-z.\s]+?\s[A-Za-z\s]{8,},).+,(?:19|20)\d{2},(?:\d{4})?,.+,(?!Virginia|Massachusetts).+,.+$'
re.findall(pattern, presidents, flags = re.MULTILINE)
```




    ['26,Theodore Roosevelt,1901,1909,Republican,New York,https://en.wikipedia.org/wiki/Theodore_Roosevelt']

