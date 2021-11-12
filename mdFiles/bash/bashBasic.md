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
* With **Shebang** line, bash file could execute with **./script.sh** directly.  
`#!/bin/bash`  
* If don't want to define full directory. Put **~/bin** into $PATH. Then just need to enter script file name to execute bash.
  ```console
  export PATH=$PAHT:~/bin
  $ source ~/.bashrc
  $ script.sh
  ```
* **#!/usr/bin/env NAME**: let Shell find the first matched **NAME** in env variables. If you don't know a specific directory, it's useful.
* Script parameters
  * **$1 ~ $9**: script's first to nine parameters
  * **$#**: total number of parameters
  * **$@**: All parameters, space interval
  * **$**: All parameters, interval **\$IFS**. Default is space.
* If script parameters more than 9, then the 10th parameter could be referred by **${10}**.
* For example:
  * ```command -o foo bar```
  * -o is $1, foo is $2, bar is $3
* ```console
  #!/bin/bash
  # script.sh
  echo "All parameters：" $@
  echo "Parameter numbers：" $#
  echo '$0 = ' $0
  echo '$1 = ' $1
  echo '$2 = ' $2
  echo '$3 = ' $3

  #Result
  $ ./script.sh a b c
  All parameters：a b c
  Parameter numbers：3
  $0 =  script.sh
  $1 =  a
  $2 =  b
  $3 =  c
  ```

* Simple way for **source**  
  `$. .bashrc`
## Read command
* **Read** command get the input from user.
  ```console
  echo -n "Input text > "
  read text
  echo "Your input: $text"
  ```
* Print each line of file
  ```console
  #!/bin/bash

  filename='/etc/hosts'

  while read myline
  do
    echo "$myline"
  done < $filename
  ```
* **-a** parameter take the user input into an array.
* **-n** parameter only take substring of input.
* **-e** enable auto complete in file directory.

## Condition Statement
* **test** command used to test if condition is true.
* Shell provide and (-a), or (-o), no(!) to connect test condition. Priority ! > -a > -o
  ```console
  cd /bin
  if test -e ./notFile -o -e ./bash
  then
      echo 'At least one file!'
  else
      echo 'Both not exist!'
  fi
  ```
* one last statement in if statement works  
  `$ if false; true; then echo 'hello world'; fi`  
  `hello world`
* **Test** Command
  ```console
  #1
  [ expression ]
  #2
  test expression
  #3 Support regular expression
  [[ expression ]]
  ```
* **[]** is a simple way of **test** command. Note: Need space before and after brackets
* **test** internal **<, >**, it has to quote by quotation or use backward slash.
* **test** internal using parentheses, it has to qupte by quotation or escape
  ```console
  if ((3 > 2)); then
    echo "true"
  fi
  true
  ```
* **case** could use all wildcard
  ```console
  a): match a
  a|b): match a or b
  [[:alpha:]])：match single char。
  ???)：match three chars word。
  *.txt)：match file ending with .txt。
  *)：match any input
  ```
* Add **;;&** in the end of conditation statement. Will not exit **case**. Keep going next conditon.

## Loop statement
* while condition  
  ```console
  while condition; do
    commands
  done
  ```
* while codition part could execute any number of commands, but the result only depends on the result of last command.
* Until condition
  If the conditio does not satisfy, keep execute the commands
* for...in Loop
  ```console
  for variable in list
  do
    commands
  done
  # If also support grammer
  for (( i=0; i<5; i++ )); do
    echo $i
  done
  ```
* **breah** and **continue** can be used to jump out loop.
* Select Command
  ```console
  select name
  [in list]
  do
    commands
  done
  ```
* Example for **select**
  ```console
  select brand in Samsung Sony iphone   symphony Walton
  do
    echo "You have chosen $brand"
  done
  ```
* Exmaple for **select** and **case**
  ```console
  echo "Which Operating System do you like? "
  select os in Ubuntu LinuxMint Windows8  Windows10 WindowsXP
  do
    case $os in
      "Ubuntu"|"LinuxMint")
        echo "I also use $os."
      ;;
      "Windows8" | "Windows10" |  "WindowsXP")
        echo "Why don't you try Linux?"
      ;;
      *)
        echo "Invalid entry."
        break
      ;;
    esac
  done
  ```

## Bash Function
* If function and alias has same name, alias will be executed first.
  ```console
  # Function defination
  fn() {
    # code
  }

  function fn() {
    # code
  }

  # Date example
  today() {
    echo -n "Today's date is: "
    date +"%A, %B %-d, %Y"
  }
  - before d means remove unnecessary 0
  https://www.runoob.com/linux/linux-comm-date.html
  ```
* Delete a function  
  `unset -f functionName`
* Check all defined function  
  * all function: `declare -f`
  * without body: `declare -F`
  * single function: `declare -f functionName`
* Parameter variables
  ```console
  function alice {
    echo "alice: $@"
    echo "$0: $1 $2 $3 $4"
    echo "$# arguments"

  }

  alice in wonderland
  ```
* date example
  ```console
  function log_msg {
    echo "[`date '+ %F %T'` ]: $@"
  }
  ```
* variables inside of function belongs to global variables.
  * **local** can be used to define local var inside of function

## Array
* Create Array
  ```console
  #1
  ARRAY[INDEX]=value
  #2
  ARRAY=(value1 value2 ... valueN)
  #3
  array=([2]=c [0]=a [1]=b)
  ```
* Read Array
  ```console
  #1
  echo ${array[i]}
  #2 All items
  foo=(a b c d e f)
  echo ${foo[@]}
  #3 Traverse
  for i in "${name[@]}"; do
    echo $i
  done
  #4 Copy an array
  hobbies=( "${activities[@]}" )
  ```
* Length of Array
  ```console
  #1
  ${#array[*]}
  ${#array[@]}
  #2
  a[100]=foo
  $ echo ${#a[*]}
  1
  $ echo ${a[100]}
  3
  ```
* Get Array index
  **\${!array[@]}** and **\${!array[*]}**
  ```console
  #1
  $ arr=([5]=a [9]=b [23]=c)
  $ echo ${!arr[@]}
  5 9 23

  #2 Get subArray
  $ food=( apples bananas cucumbers dates eggs fajitas grapes )
  $ echo ${food[@]:1:1}
  bananas
  $ echo ${food[@]:1:3}
  bananas cucumbers dates
  ```
* Append Array member
  ```console
  $ foo=(a b c)
  $ echo ${foo[@]}
  a b c
  $ foo+=(d e f)
  $ echo ${foo[@]}
  a b c d e f
  ```
* Delete Array member
  ```console
  $ foo=(a b c d e f)
  $ echo ${foo[@]}
  a b c d e f

  $ unset foo[2]
  $ echo ${foo[@]}
  a b d e f
  # It just was hidden, not deleted

  # unset ArrayName could clean whole array
  ```
## **Set** and **shopt** command
* **set -u** if encounter no-exist variables, scirpt return error
* **set -x** print out the command use to echo some messages
* **set -e** scipt stops when any error happens
* When expected command return non-zero, could turn off **set +e** then turn if on **set -e**
* **set -eo pipefail** as long as one child command failed, whole pipe command failed

## Script debug
* Below scirpt: if **cd** command failed, **rm** will be a problem
  ```console
  dir_name=/path/not/exist
  cd $dir_name
  rm *

  # If should be like this, judge if $dir_name exist, then execute following commands
  [[ -d $dir_name ]] && cd $dir_name && rm *

  # Dry run
  [[ -d $dir_name ]] && cd $dir_name && echo rm *
  ```
* Parameters used to debug
  * $LINENO wil return line number
  * $FUNCNAME 
    ```console
    #!/bin/bash

    function func1()
    {
      echo "func1: FUNCNAME0 is ${FUNCNAME[0]}"
      echo "func1: FUNCNAME1 is ${FUNCNAME[1]}"
      echo "func1: FUNCNAME2 is ${FUNCNAME[2]}"
      func2
    }

    function func2()
    {
      echo "func2: FUNCNAME0 is ${FUNCNAME[0]}"
      echo "func2: FUNCNAME1 is ${FUNCNAME[1]}"
      echo "func2: FUNCNAME2 is ${FUNCNAME[2]}"
    }

    func1

    # Result
    $ ./test.sh
    func1: FUNCNAME0 is func1
    func1: FUNCNAME1 is main
    func1: FUNCNAME2 is
    func2: FUNCNAME0 is func2
    func2: FUNCNAME1 is func1
    func2: FUNCNAME2 is main
    ```
* $BASH_SOURCE return an array includes script invoke stack
* $BASH_LINENO return an array includes invoke line number

## **mktemp** command, **trap** command
* Safety create temp file
  * **/tmp** folder is read/writeable by everyone 
* temp file should follow rules:
  * check if file already exist
  * make sure file createLogin session will run a whole system env **startup**/etc/profile success all user's global configuration **scripts**/etc/profile.d
   * have to has access limitation
  * creatan unpredictable file name
  * when exist script, delete temp file
* **mktemp** will create a temp file, only user has limitation
* **trap** command coudl reponse system signal

## Bash startup environment
* Login session will run a whole system env startup
  * **/etc/profile** all user's global configuration scripts
  * **/etc/profile.d** all .sh script in this folder
  * **~/.bash_profile** user's personal config script, it exist, then it's an end
  * **~/.bash_login** if above one not exist, then execute this one
  * **~/.profile** if above two not exist, then try to execute this one
* bash login command force execute login session
* Non-login session
  * Enter system, manual create bash session like  
    * `bash` in command line
    * Following scripts will be executed:
      * **/etc/bash.bashrc** for all users
      * **~/.bashrc** only for current user
    * Execute script will not call `~/.bashrc`
* **~/.bash_logout** will be called when session exit
* **Startup options** -- `$ bash -n scriptname`
  * **-n** not execute script, just check grammer
  * **-v** print command before output the script result
  * **-x** print command before execute it
* **Bash** allow user define their own shortcut
  * Global keyboard binding file is **/etc/inputrc**, user can create their own binding file **.inputrc** file. If user define this file, need to add this line below, make sure global binding will not be missed.  
  `$include /etc/inputrc`
  * Example:
    * **"\C-t":"pwd\n"** means bind **Ctrl+t** with **pwd** command

## Command reminder
* Environment variables PS1
* Customized command reminder:
  * Updated PS1 need to put into .bashrc
  * If need to see the updated reminder, just execute:
    * `$ source ~/.bashrc`
* Reminder defination:
    ```console
    \a：Bell ringing
    \d：For example: "Mon May 26"
    \h：Hostname
    \H：Full desk name
    \j：Current Shell session command number
    \l：Current terminal device name
    \n：
    \r：A new line
    \s：Shell name
    \t：24 hours:minutes:seconds
    \T：12 hours current time
    \@：12 hours AM/PM
    \A：24 hours hours:minutes
    \u：current username
    \v：Shell version number
    \V：Shell edition and release version
    \w：Current work directory
    \W：Current folder name
    \!：Current command in history commands
    \#：Current shell session command numbers
    \$：User $ sign root # sign
    \[：Start sign
    \]：End sign
    ```
* For example:
  * **[\u@\h \W]\$** means **[user@host ~]$**
* Color
  * Bash allow customized reminder color
* **PS1='\[\033[0;31m\]<\u@\h \W>\$\[\033[00m\]'** Reminder will be red, following text is normal text
* Background color could be customized
* Sys env PS2, PS3, PS4
  * PS2: new line reminder default **>**
  * PS3: select command reminder
  * PS4: default **+**, for bash parameter **-x**