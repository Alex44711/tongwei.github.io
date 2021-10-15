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
* Length of String  
  **${varname}**
* Sub string  
  Grammer: `${varname:offset:length}`
* Search and replace
  ```console
  $ testPath=/home/cc/book/long.file.name
  # Shortest match
  $ echo ${testPath#/*/}
  cc/book/long.file.name
  # Longest match
  $ echo ${testPath##/*/}
  long.file.name
  # delete file directory part, only keep file name
  $ echo ${path##*/}
  long.file.name
  ```
* Replace grammer
  ${variable/#pattern/string}
  ```console
  $ test=JPG.JPG
  $ echo ${foo/#JPG/jpg}
  jpg.JPG
  ```
* Tail match of string
  ```console
  $ path=/home/cam/book/long.file.name
  $ echo ${path%.*}
  /home/cam/book/long.file
  $ echo ${path%%.*}
  /home/cam/book/long
  # Replace with tail match
  ${variable/%pattern/string}
  ```
* Any place matching  
  `${variable/pattern/string}`
  `${variable//pattern/string}`  
  All are longest mathching, first one only replace first match, second will replace all matched pattern
* Replace ":" to new line
  ```console
  $ echo -e ${PATH//:/'\n'}
  /usr/local/sbin
  /usr/local/bin
  /usr/sbin
  ...
  ```
* Change lower/upper case
  ```console
  # Convert to Upper case
  ${varname^^}
  # Convert to Lower case
  ${varname,,}
  ```

## Arithmetic Operation
* ((...)) run integer arithmetic operation grammer 
  ```console
  $ ((foo = 5 + 5))
  $ echo $foo
  10
  $ echo $((2 + 2))
  4
  ```
* If using string in **$((...))**, bash will treat it as a variable name. If no same name variable, the value of that is empty. And empty value will be **0**
  ```console
  $ echo $(( "hello" + 2))
  2
  $ echo $(( "hello" * 2))
  0
  $ foo=hello
  $ hello=3
  $ echo $(( foo + 2 ))
  5
  ```
* **$((...))** support bit operation, example:  
  **<<**  
  **>>**  
  ...  
* **$((...))** support logical operation, example:  
  **!**  
  **<=**  
  ...
* **$((...))** support assignment operation, example:  
  `parameter += value --- parameter = parameter + value`   
  ...
* Evaluation operation ","
  ```console
  $ echo $((foo = 1 + 2, 3 * 4))
  12
  $ echo $foo
  3
  ```

## Line Operation
* Cursor movement
  * **Ctrl + a: move to line start**
  * **Ctrl + b: move to line start one char**
  * **Ctrl + e: move to line end**
  * **Ctrl + f: move to line end one char**
  * **Alt + f: move to the end of current word**
  * **Alt + b: move to the start of current word**
* Edit command
  * **Ctrl + d: delete char of cursor (delete)**
  * **Ctrl + w: delete the word before cursor**
  * **Ctrl + t: char switch before and after cursor**
  * **Alt + t: word switch before and after cursor**
  * **Alt + l: word lowcase after cursor**
  * **Alt + u: word uppercase after cursor**
  * **Ctrl + k: cut after cursor string**
  * **Ctrl + u: cut before cursor string**
  * **Ctrl + y: paste string in cursor**
  * **Alt + d: cur after cursor word**
  * **Alt + Backspace: cut before cursor word**
* Auto complete
* Operation history  
  `$ echo $HISTFILE`  
  `/home/me/.bash_history`  
  ```console
  $ !e
  $ echo Goodbye
  Goodbye
  ```
  **!e** means find the operation history, latest one start with **e** and execute.
* History command
  ```console
  $ export HISTTIMEFORMAT='%F %T  '
  $ history
  1  2013-06-09 10:40:12   cat /etc/issue
  2  2013-06-09 10:40:12   clear
  ```
  Above code, **%F** means **%Y - %m - %d**, **%T** means **%H : %M : %S**.  
  As long as **HISTTIMEFORMAT** is set up, will save timestamp in **.bash_history** file.
* Set history operation number  
  `$ export HISTSIZE=10000`  
* If **HISTSIZE=0** write into **~/.bashrc** file, then will not keep the use's operation history. If write into **/etc/profile**, the whole system will not keep operation history.
* Set which command will not be write into history  
  `export HISTIGNORE='pwd:ls:exit'`  
* `$ !8` to execute the command line number 8
* Related commands
  * **!$**: represent the last parameter of last command
  * **!***: represent all parameters of last command
  * **Ctrl + [**: represent ESC
  > **Alt + .**: insert the last word of previous  command.

## Directory Stack
* **cd -**
  * return to previous directory
* **pushd, popd**  
  Used to remember multiple layer directories
  * `$ pushd dirname`  

  First time use **pushd** command, put the current directory into stack, then put the directory that you need to go into stack (top).  
* When **pod** command does not have any parameters, will pop the record at the stack top, then enter the new directory
  ```console
  $ pwd
  /home/user
  $ pushd ~/learning
  ~/learning /home/user
  $ pushd ~/general
  ~/general ~/learning /home/user
  $ popd
  # Enter ~/learning directory
  $ popd
  # Enter /home/user directory
  $ popd
  # directory not change, current stack is empty
  ```
* **-n** parameter  
  **-n** parameter not only pop top record, not change directory.  
* Int parameter  
  Will not change directory
  ```console
  # Count from top, move the third one to top
  $ pushd +3
  # Delete the one count from top
  $ popd +3
  # Count from bottom of stack
  $ popd -3
  # pod +0 delete first directory, pod +1 delete second directory
  ```
* Directory parameter  
  **pushd** can accept a directory as parameter, then put this dir to the top of stack, then enter this directory.  
* **dirs** command can show the content of current directory. Usually to check result of **pushd** and **popd**
  ```console
  -c : clean stack
  -l : no tilde, print full directory
  -p : each line for each item in stack
  -v : each line for each item, show location number
  +N : Nth item from stack top
  -N : Nth item from stack bottom
  ```

## Get started with scripts