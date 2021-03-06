# Python primer IV, Data Science I, Fall 2020. 

## Topics to cover

- working with regular expressions
- various `re` functions (`re.search`, `re.finditer`, `re.findall`)
- counting and storing matches

## Useful materials
- Haddock and Dunn chapters and 10, bit of 11 
- updated document that has corrections for python3 where necessary (PythonLesson2_Chapter9.pdf, PythonLesson3_Chapter10.docx)
- regular expression cheat sheet (python-regular-expressions-cheat-sheet-1.pdf)
- [regular expression tester](https://regex101.com).
<p>&nbsp;</p>


## Introduction to regular expressions
We have already used some Unix (e.g., `grep`, `sed`, `tr`, `awk`) and Python (`str.count()`) tools that allow pattern recognition as a basis for completing some task or file manipulation. However, we were using these tools mostly to search for fixed patterns. While working with biological data, and really any other form of big data, we will encounter many problems that will require the ability to match flexible patterns. 

Because pattern matching is a common task for working with big data, there is a standard set of tools in Python (and other languages) for dealing with them: regular expressions. Regular expressions are statements in code designed to match text with certain characteristics (ranging from exact matches to extremely fuzzy ones), are extremely useful in biology and across data science in general. This week we will start to learn about how to match specific and increasingly flexible patterns in data, as well as how to track, store, and manipulate the portions of strings that we wish to extract. Moving forward, you are likely to use regular expressions in many if not most of the code you write in Python.

We will start working with regular expressions using the `re` module. As hinted previously with `sys`, libraries will play a major role in enabling your python code. We will cover that in more detail in a few weeks. For now, lets cover the basics of matching regular expressions with `re`.

To use functionality in the `re` library in your code, you will add a line near the top of your scripts that uses the `import` function. 

    import re

## `re.search()`

In general, `re` requires you to specify a pattern to match, followed by a string to match the pattern in. There are numerous functions built into `re`, but lets start here with `re.search`, which returns an object with information about the match and true/false based on whether the match occurs in the string or not. The general idea is as follows:

    re.search(pattern, string)

We can search a specified string, using an `if` statement as an example of how to control our code based on the presence or absence of matches:

    Seq = "ATCGGGGCCTAGAAT"
    if re.search("TAG", Seq):
        print("Stop codon (TAG) found.\n")


The `^` character can be used to anchor the pattern at the beginning of the string, and the `$` anchors the pattern at the end
    
    Seq = "ATCGGGGCCTAGAAT"
    if re.search("^A", Seq):
        print("Seq begins with A.\n")

    Seq = "ATCGGGGCCTAGAAT"
    if re.search("T$", Seq):
        print("Seq ends with T.\n")

We can use **alternation** (pattern A OR pattern B) to increase flexibility of  expressions.

    Seq = "ATCGGGGCCTAGAAT"
        if re.search("CC(C|T)", Seq):
        print("Seq has 'CCC' or 'CCT'.\n")


`re.search()` also stores matches in an object. If parentheses are used to group matches, the object can have multiple groups.

    Seq = 'ATCGGGGGGATCGGGATC'
    re.search('ATC', Seq)
    #returns: <re.Match object; span=(0, 3), match='ATC'>

Matches can be extracted as below, although we will cover capturing and storing matches in more detail below.

    Seq = 'ATCGGGGGGATCGGGATC'
    re.search('ATC', Seq).group() #returns: 'ATC'

Note that above, `re.search` only stores one ATC, even though there are 3 in Seq. Thats because `re.search` only makes one match. `re.findall` and `re.finditerate` find, track, and store multiple matches. More on these below.

## Basic syntax for regular expression matching

Syntax for regular expressions is mostly consistent across many languages, including Unix, Perl, and Python. The regular expression cheat sheet posted with the course materials will be helpful here; I suggest keeping that or similar cheat sheet close by. The table below illustrates some of the most commonly used expressions

<p>&nbsp;</p>

| Expression | Meaning |
|---------- | ---------- |
|\d  | single digit character |
|\d+  | One or more digits |
|\d*  | 0 or more digits |
|\w  | word character |
|\W | non word character |
|\s | whitespace character |
|\S | non-whitespace character |
|*  | anything|
|.| any non-whitespace character|
|^ | beginning of string anchor |
|$ | end of string anchor  |
|\d{3,}| 3 or more consecutive digits|
|[a-z| any lower case letter|
|[A-Z]| any upper case letter|
|[^ATCG]| any character other than A, T, C, G|
|\\$| matches "$", special characters 'escaped' with `\`|
|\\@| matches "@", special characters 'escaped' with `\`|
<p>&nbsp;</p>

## A note on special characters

Special characters require a `\` escape in regular expressions. Thus, matching '&' in a string of text requires an expresssion such as "\&". In addition, `\` is used to indicate certain special characters such as line endings, tabs, and other characters listed above. Examples below.
<p>&nbsp;</p>

| Expression | Translation |
|---------- | ---------- |
|\\' |	single quote 	|
|\\* |	* 	|
|\\+ |	+ 	|
|\\? |	? 	|
|\n |	New Line 	|
|\r |	Carriage Return |	
| \t |	Tab 	|
| \b |	Backspace |	
| \f |	Form Feed |	

.^$*+?()[{\|

Example usage.

    X = "COD * ? + \n"
    if re.search("\+", X):  
        print("Matched")
    if re.search("\*", X):  
        print("Matched")   
    if re.search("\$", X):  
        print("Matched")
    if re.search("\?", X):  
        print("Matched")
### `re.finditer)`

`re.finditer()` can process multiple matches and returns a list of match objects which can be further processed in a loop.

    Seq = "CGCTCNTAGATGCGCRATGACTGCAYTGC" 

    matches = re.finditer(r"[^ATGC]", Seq) 
    for m in matches: 
        base = m.group() 
        pos  = m.start() 
        print(base + str(pos))

### `re.findall()`

`re.findall` can process multiple matches and returns the text of the matches themselves as strings. Thus, rather than returning a list of match objects, this returns a list of strings. Useful for capturing or counting matches.

In each below use of `re.findall` each match is stored in a list. We will talk more about capturing matches below.

    ID = "CO_BC_13292929 0 1 2 0 2 1 1 "
    re.findall("[A-Z]+",ID) # returns['CO', 'BC']
    re.findall("\d{3,}",ID) # returns['13292929']
    re.findall("\s\d",ID) # returns [' 0', ' 1', ' 2', ' 0', ' 2', ' 1', ' 1']

## Matching repetitive patterns. 

To extract AT repeated **2 or more**  times:

    SSR = 'ATATATGGGCGATATATATCCATATC'
    re.findall("(AT){2,}", SSR)

To extract ATC repeated **3 or more**  times:

    SSR = 'ATCATCATCATCATGGGCGATATATATCCATATC'
    re.findall("(ATC){3,}", SSR)

Or if we wanted to extract regions of DNA that only have T and A for 5 consecutive bases:
    
    SSR = 'ATAAAATCATCATATTTATGGGCGATATATATCCATATC'
    re.findall("[AT]{5,}", SSR)



## Counting the number of matches in a string

There are multiple ways to do this. One kind of straight forward way uses `re.findall`, which will return strings of each match, in a list if there is more than one.

    Sent = 'the dog jumped over the moon, which circled the Earth'
    Result = re.findall("the", Sent) 
    print(Result) # will be a list ['the', 'the', 'the']

The length of the list generated from `re.findall` gives you the number of matches.

    len(Result)

Or more efficiently, the line below will do:

    NumMatch = len(re.findall("the", Sent)) 

Another example, lets say we want to know how many 'AT' repeats occur in a DNA sequence

    Seq = "CTGCATTATATCGTACGAAATTATACGCGCGATATATATATATATAT"
    len(re.findall('AT', Seq)) ## returns 13

Below, an expression is used to match regions with only A and/or T for 4 or more consecutive bases. The results of `refindall` are then used in a for loop to print the length of each AT rich match.

    Seq = "CTGCATTATACGAATTATACGCGCGATATAATACATATAT"
    ATlist = re.findall("[AT]{4,}", Seq)
    for Rep in ATlist:
        print(Rep, len(Rep)) 

## Storing matches

There are numerous ways to do capture and store matches. This can be an incredibly powerful way to extract information from large files of text, especially when using flexible regular expressions. 

Parentheses are used to define parts of expression matches to store.
    
    X = 'the dog jumped over the moon'
    re.search('(dog)\s(jumped)', X).group(1) # returns dog
    re.search('(dog)\s(jumped)', X).group(2) # returns jumped

Another way of looking at this with more flexible expression:

    Name = "Loxia curvirostra" 
    m = re.search("(\w+) (\w+)", Name) 
    print(m.group(1)) # prints 'Loxia'
    re.search("(\w+) (\w+)", Name).group(2) # returns 'curvirostra'


## Splitting strings using a regular expression: `re.split`

We are accustomed to splitting strings based on delimiters such as ',', '\s', and '\t', but in reality we can split strings with any expression we like using regular expressions and `re.split`

    Seq = "ATGGGNCTGANNTAAAGNNNNGTCCCCNNNTTTTTTT"
    MotifList = re.split("N{2,}", Seq) # splitting by '2 or more Ns
    print(MotifList)

## `re.sub`
`re.sub` can be used to replace a string that matches a regular expression. It takes  a regular expression pattern in the first argument, a new string in the second argument, and the string to be processed in the third argument.

    Seq = 'CO_MT_134545 0   1 0   2 1'
    result = re.sub('CO_',  '',    Seq) # Deletes CO_
    result = re.sub(r'\s+', ' ',   Seq) # Delete extra whitespaces 

    Let = 'abcdefghi'
    result = re.sub('abc(def)ghi', r'\1', Let) # Replace string with a part of itself

## Simple example demo

Below is a script that performs a few simple substitutions on the yeast_genome.gff file we worked with earlier in the semester. Providing this here as an example workflow, or as a basis for playing with regular expression actions.

    import sys
    import re

    IN = open(sys.argv[1], 'r')
    OUT = open("CHR1_5_ygenome.txt", 'w')

    for Line in IN:
	    Line = Line.strip('\n')
	    RLine = re.sub('chr','CHR', Line)
	print(RLine)
	
	if re.search('CHRI|CHRV', RLine):
		OUT.write(RLine + "\n")