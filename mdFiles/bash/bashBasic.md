[BASH learning](https://wangdoc.com/bash/expansion.html)
# Notes
## globbing
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
    ```
    ```consolex1
    $ echo {a..c}{1..3}
    a1 a2 a3 b1 b2 b3 c1 c2 c3
* braces is not file name globbing, it's used to extends all values in braces
* **shopt** could change the behavior of BASH
## Quotes and Escaping