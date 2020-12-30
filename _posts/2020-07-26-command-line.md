---
layout: post
title: Learn Command Line (codecademy)
date: 2020-07-26
categories: [codecademy]
---

This post is a note for what I have learned from [codecademy - Learn the Command Line](https://www.codecademy.com/learn/learn-the-command-line).

# Command Line

### Navigation the file system

`ls -alt` lists all contents, including hidden files and directories, in long format, ordered by the date and time they were last modified

### echo

```
$ echo "Hello"
Hello
```

1. *standard input*, abbreviated as `stdin`, is information inputted into the terminal through the keyboard or input device.
2. *standard output*, abbreviated as `stdout`, is the information outputted after a process is run.
3. *standard error*, abbreviated as `stderr`, is an error message outputted by a failed process.

#### standard output

```
$ cat oceans.txt > continents.txt
```

`>` takes the standard output of the command on the left, and redirects it to the file on the right

```
$ cat glaciers.txt >> rivers.txt
```

`>>` takes the standard output of the command on the left and *appends* (adds) it to the file on the right

#### standard input

```
$ cat < lakes.txt
```

`<` takes the standard input from the file on the right and inputs it into the program on the left

#### pipe

```
$ cat volcanoes.txt | wc | cat > islands.txt 
$ cat volcanoes.txt | wc  > islands.txt # also work
```

`|` is a “pipe”. The `|` takes the standard output of the command on the left, and *pipes* it as standard input to the command on the right

the output of `cat volcanoes.txt` is the standard input of `wc`. in turn, the `wc` command outputs the number of lines, words, and characters in `volcanoes.txt`

```
$ sort deserts.txt | uniq > uniq-deserts.txt # all lines are alphabetized and made unique
```

### grep: find file with specific content

```
$ grep -i Mount mountains.txt 
Hindu Kush Mountains
Henduan Mountains
Andes mountains
Atlas Mountains
Mount Kilimanjaro 
Scandinavian mountains
Appalachian mountains
Rocky mountains
Transantarctic Mountains
```

`grep` stands for “global regular expression print”
`grep -i` enables the command to be case insensitive

#### search all files under directory

```
$ grep -R Arctic /home/ccuser/workspace/geography 
/home/ccuser/workspace/geography/oceans.txt:Arctic Ocean
/home/ccuser/workspace/geography/deserts.txt:Arctic Desert
/home/ccuser/workspace/geography/continents.txt:Arctic Ocean
/home/ccuser/workspace/geography/uniq-deserts.txt:Arctic Desert

$ grep -Rl Arctic /home/ccuser/workspace/geography 
/home/ccuser/workspace/geography/oceans.txt
/home/ccuser/workspace/geography/deserts.txt
/home/ccuser/workspace/geography/continents.txt
/home/ccuser/workspace/geography/uniq-deserts.txt
```

`grep -R` searches all files in a directory and outputs filenames and lines containing matched results
`-R` stands for “recursive”

`grep -Rl` searches all files in a directory and outputs only filenames with matched results
`l` stands for “files with matches”

### sed: find and replace

```
$ cat forests.txt 
The Amazon snowforest
The Congo snowforest
Valdivian Temperate snowforest
Daintree snowforest
Southeast Asian snowforest snowforest
Tongrass National forest
Sinharaja Forest Reserve
Pacific Temperate snowforest snowforest

$ sed 's/snow/rain/' forests.txt 
The Amazon rainforest
The Congo rainforest
Valdivian Temperate rainforest
Daintree rainforest
Southeast Asian rainforest snowforest
Tongrass National forest
Sinharaja Forest Reserve
Pacific Temperate rainforest snowforest

$ sed 's/snow/rain/g' forests.txt 
The Amazon rainforest
The Congo rainforest
Valdivian Temperate rainforest
Daintree rainforest
Southeast Asian rainforest rainforest
Tongrass National forest
Sinharaja Forest Reserve
Pacific Temperate rainforest rainforest
```

`sed` stands for “stream editor”. It accepts standard input and modifies it based on an *expression*, before displaying it as output data the expression `'s/snow/rain/g'`:

- `s`: stands for “substitution”. it is *always* used when using `sed` for substitution.
- `snow`: the search string, the text to find.
- `rain`: the replacement string, the text to add in place.
- `g` expression, meaning “global”.

### Environment

```
$ source ~/.bash_profile
```

activates the changes in ~/.bash_profile for the current session
On Linux style shells, this is `~/.bashrc` and on OSX, this is `~/.bash_profile`

#### .bash_profile

```bash
# ~/.bash_profile
alias pd="pwd"
export USER="Jane Doe"
export PS1=">> "
```

set up the alias of `pwd` command and export USER

```
$ source ~/.bash_profile
>> pd # will print out the working directory
>> echo $USER
```

`export PS1=">> "` sets the command prompt variable and exports the variable. 
Here we change the default command prompt from $ to >>

#### $PATH

```
$ echo $PATH
/home/ccuser/.gem/ruby/2.0.0/bin:/usr/local/sbin:/usr/local/bin:/usr/bin:/usr/sbin:/sbin:/bin
```

`PATH` is an environment variable that stores a list of directories separated by a colon
Each directory contains scripts for the command line to execute

#### env

```
$ env
HOSTNAME=8915dfaa7c90
GEM_HOME=/home/ccuser/.gem/ruby/2.3.0
TERM=xterm
EXPIRES_AT=1594653431
NLTK_DATA=/home/ccuser/.nltk_data
PATH=/home/ccuser/.bin:/home/ccuser/node_modules/.bin:/home/ccuser/.gem/ruby/2.3.0/bin:/home/ccuser/.composer/vendor/bin:/home/ccuser/.bin:/home/ccuser/node_modules/.bin:/home/ccuser/.gem/ruby/2.3.0/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
CODEX_RUNNER_PATH=/var/codecademy/codex/runners
PWD=/home/ccuser/workspace/music
MPLBACKEND=agg
SESSION_ID=1530f080-11e8-4c0b-b982-713483f64647
LANG=en_US.UTF-8
TZ=Etc/UTC
PS1=$ 
HOME=/home/ccuser
SHLVL=2
PYTHONPATH=/var/codecademy/runner_contexts/python:
_=/usr/bin/env
```

The `env` command stands for “environment”, and returns a list of the environment variables for the current user

### bash Scripting

it is good practice to place commonly used scripts in the `~/bin/` directory

```
$ chmod +x script.sh
```

have the “execute” permission to allow them to be run

```bash
# ~/.bash_profile
PATH=~/bin:$PATH
```

ensure that scripts in `~/bin/` are available, you must add this directory to your `PATH` within your configuration file

```bash
# script.sh
#!/bin/bash
```

The beginning of your script file should start with `#!/bin/bash` on its own line. This tells the computer which type of interpreter to use for the script.

#### Variables

```bash
greeting="Hello" # variables
echo $greeting # access variables with $
```
#### Conditionals
```bash
#!/bin/bash
first_greeting="Nice to meet you!"
later_greeting="How are you?"
greeting_occasion=1
if [ $greeting_occasion -lt 1 ]
then
  echo $first_greeting
else
  echo $later_greeting
fi
```

 `-lt` stands for “less than”

- Equal: `-eq` or `==`
- Not equal: `-ne` or `!=`
- Less than or equal: `-le`
- Less than: `-lt`
- Greater than or equal: `-ge`
- Greater than: `-gt`
- Is null: `-z`

#### loops

##### for loop

```bash
files=/some/directory/*

for file in $files
do
  echo $file
done
```

##### while loop

```bash
while [ $index -lt 5 ]
do
  echo $index
  index=$((index + 1))
done
```

##### until loop

```bash
until [ $index -eq 5 ]
do
  echo $index
  index=$((index + 1))
done
```

#### inputs

```bash
echo "Guess a number"
read number
echo "You guessed $number"
```

##### access external data

```
saycolors red green blue
```

These arguments are entered after the script name and are separated by spaces
Within the script, these are accessed using `$1`, `$2`, etc, where `$1` is the first argument (here, “red”)

```bash
for color in "$@"
do
  echo $color
done
```

If your script needs to accept an indefinite number of input arguments, you can iterate over them using the `"$@"`

