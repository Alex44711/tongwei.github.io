[BASH learning](https://wangdoc.com/bash/expansion.html)
# Notes
## Globbing
* **~user** to user's main directory
* **?** means any single char
    ```console
    # a.txt and b.txt files
    $ ls ?.txt
    a.txt b.txt
    ```
* **[...]** [ab] could match **a** or **b**
* **?[!a]** means second char is not **a**
* **{...}** means globbing each string in braces
    ```console
    $ echo {0..8..2}
    0 2 4 6 8
    $ echo {a..c}{1..3}
    a1 a2 a3 b1 b2 b3 c1 c2 c3
    ```
* braces is not file name globbing, it's used to extends all values in braces
* **shopt** could change the behavior of BASH

## Quotes and Escaping
* **Single quote** used to keep the literal meaning of string, all special char will be normal string between single quote.
    ```console
    # wrong
    $ echo 'it\'s'
    # correct
    $ echo $'it\'s'
    # better
    $ echo "it's"
    it's
    ```
* Double quote has three special chars: **\$**, **`**, and **\\**. It will be extended by Bash
* **`** could be used to execute sub-command
* Double quote keep the original format of command.
    ```console
    # Single line output
    $ echo $(cal)
    一月 2020 日 一 二 三 四 五 六 1 2 3 ... 31

    # Original Format output
    $ echo "$(cal)"
          一月 2020
    日 一 二 三 四 五 六
              1  2  3  4
     5  6  7  8  9 10 11
    12 13 14 15 16 17 18
    19 20 21 22 23 24 25
    26 27 28 29 30 31
    ```
* Here document
  * multi-lines input
  * variable will be replaced, doulbe/single will become to normal string
  ```console
    $ foo='hello world'
    $ cat << _example_
    $foo
    "$foo"
    '$foo'
    _example_
    
    hello world
    "hello world"
    'hello world'

    # if no need populate variable
    $ foo='hello world'
    $ cat << _example_
    $foo
    "$foo"
    '$foo'
    _example_

    hello world
    "hello world"
    'hello world'

    # working as
    $ command << token
    string
    token
    # equals
    $ echo string | command  
  ```

## Bash Variables
* Usage of **{}**
  ```console
  $ a=foo
  $ echo $a_file
  $ echo ${a}_file
  foo_file
  ```
* If the value is a variable, **!** could be used
  ```console
  $ myvar=USER
  $ echo ${!myvar}
  alex
  ```
* **export** is able to export variable to child bash session, update child bash variable will not update father variable
  ```console
  $ export foo=bar
  $ bash
  $ echo $foo
  bar
  $ foo=baz
  $ exit
  $ echo $foo
  bar
  ```
* Special variables
  ```console
  # Previous command exit status code
  $ echo $?
  1
  # Current shell process ID
  $ echo $$
  # Last parameters of previous command
  $ echo $USER
  alex
  $ echo $_
  alex
  # Previous backend async cpmmand processID
  $ firefox &
  [1] 11064
  $ echo $!
  11064
  # Current bash name
  $ echo $0
  /bin/bash
  # Current Shell startup parameter
  $ echo $-
  himBHs
  ```
* Default value of variables  
  `${varname:-word}`  
  If variable **varname** exist and not empty, return it's value or return **word**  
  `${varname:=word}`  
  Same as first one, one more thing is set up the value of variable  
  `${varname:+word}`  
  If variable name exist and not empty, then execute **word** command. Used to test if a variable exist  
  `${varname:?message}`  
  If **varname** exist and not empty, return it's value or print out **varname: message** and stop bash running. Used to prevent variable to run with define.
* **declare** commands  
  -a：declare array variable  
  -f：output all func defination  
  -F：output all func name  
  -i：declare int variable  
  -l：declare variable as lowcase  
  -p：check variable info  
  -r：declare read-only variable  
  -u：declare variable as uppercase  
  -x：export as environment variables  
* **let** command
  ```console
  $ let "v1 = 1" "v2 = v1++"
  $ echo $v1,$v2
  2,1
  ```

## Operation String
