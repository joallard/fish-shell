.. _language:

The fish language
*****************

This document is a comprehensive overview of fish's scripting language.

For interactive features see :ref:`Interactive use <interactive>`.

Syntax overview
---------------

Shells like fish are used by giving them commands. Every fish command follows the same basic syntax. A command is executed by writing the name of the command followed by any arguments. For example::

    echo hello world

This calls the :ref:`echo <cmd-echo>` command. ``echo`` writes its arguments to the screen. In the example above, the output is ``hello world``. Everything in fish is done with commands. There are commands for repeating other commands, commands for assigning variables, commands for treating a group of commands as a single command, etc. All of these commands follow the same basic syntax.

To learn more about the ``echo`` command, read its manual page by typing ``man echo``. ``man`` is a command for displaying a manual page on a given topic. It takes the name of the manual page to display as an argument. There are manual pages for almost every command. There are also manual pages for many other things, such as system libraries and important files.

Every program on your computer can be used as a command in fish. If the program file is located in one of the PATH_ directories, you can just type the name of the program to use it. Otherwise the whole filename, including the directory (like ``/home/me/code/checkers/checkers`` or ``../checkers``) is required.

Here is a list of some useful commands:

- :ref:`cd <cmd-cd>`: Change the current directory
- ``ls``: List files and directories
- ``man``: Display a manual page
- ``mv``: Move (rename) files
- ``cp``: Copy files
- :ref:`open <cmd-open>`: Open files with the default application associated with each filetype
- ``less``: Display the contents of files

Commands and arguments are separated by the space character ``' '``. Every command ends with either a newline (by pressing the return key) or a semicolon ``;``. Multiple commands can be written on the same line by separating them with semicolons.

A switch is a very common special type of argument. Switches almost always start with one or more hyphens ``-`` and alter the way a command operates. For example, the ``ls`` command usually lists the names of all files and directories in the current working directory. By using the ``-l`` switch, the behavior of ``ls`` is changed to not only display the filename, but also the size, permissions, owner, and modification time of each file.

Switches differ between commands and are usually documented on a command's manual page. There are some switches, however, that are common to most commands. For example, ``--help`` will usually display a help text, ``--version`` will usually display the command version, and ``-i`` will often turn on interactive prompting before taking action.

.. _terminology:

Terminology
-----------

Here we define some of the terms used on this page and throughout the rest of the fish documentation:

- **Argument**: A parameter given to a command.

- **Builtin**: A command that is implemented by the shell. Builtins are so closely tied to the operation of the shell that it is impossible to implement them as external commands.

- **Command**: A program that the shell can run, or more specifically an external program that the shell runs in another process.

- **Function**: A block of commands that can be called as if they were a single command. By using functions, it is possible to string together multiple simple commands into one more advanced command.

- **Job**: A running pipeline or command.

- **Pipeline**: A set of commands strung together so that the output of one command is the input of the next command.

- **Redirection**: An operation that changes one of the input or output streams associated with a job.

- **Switch** or **Option**: A special kind of argument that alters the behavior of a command. A switch almost always begins with one or two hyphens.

.. _quotes:

Quotes
------

Sometimes features like `parameter expansion <#expand>`_ and `character escapes <#escapes>`_ get in the way. When that happens, you can use quotes, either single (``'``) or double (``"``). Between single quotes, fish performs no expansions. Between double quotes, fish only performs :ref:`variable expansion <expand-variable>`. No other kind of expansion (including :ref:`brace expansion <expand-brace>` or parameter expansion) is performed, and escape sequences (for example, ``\n``) are ignored. Within quotes, whitespace is not used to separate arguments, allowing quoted arguments to contain spaces.

The only meaningful escape sequences in single quotes are ``\'``, which escapes a single quote and ``\\``, which escapes the backslash symbol. The only meaningful escapes in double quotes are ``\"``, which escapes a double quote, ``\$``, which escapes a dollar character, ``\`` followed by a newline, which deletes the backslash and the newline, and ``\\``, which escapes the backslash symbol.

Single quotes have no special meaning within double quotes and vice versa.

Example::

    rm "cumbersome filename.txt"

removes the file ``cumbersome filename.txt``, while

::

    rm cumbersome filename.txt

removes two files, ``cumbersome`` and ``filename.txt``.

Another example::

    grep 'enabled)$' foo.txt

searches for lines ending in ``enabled)`` in ``foo.txt`` (the ``$`` is special to ``grep``: it matches the end of the line).

.. _escapes:

Escaping Characters
-------------------

Some characters cannot be written directly on the command line. For these characters, so-called escape sequences are provided. These are:

- ``\a`` represents the alert character.
- ``\e`` represents the escape character.
- ``\f`` represents the form feed character.
- ``\n`` represents a newline character.
- ``\r`` represents the carriage return character.
- ``\t`` represents the tab character.
- ``\v`` represents the vertical tab character.
- ``\xHH``, where ``HH`` is a hexadecimal number, represents the ASCII character with the specified value. For example, ``\x9`` is the tab character.
- ``\XHH``, where ``HH`` is a hexadecimal number, represents a byte of data with the specified value. If you are using a multibyte encoding, this can be used to enter invalid strings. Only use this if you know what you are doing.
- ``\ooo``, where ``ooo`` is an octal number, represents the ASCII character with the specified value. For example, ``\011`` is the tab character.
- ``\uXXXX``, where ``XXXX`` is a hexadecimal number, represents the 16-bit Unicode character with the specified value. For example, ``\u9`` is the tab character.
- ``\UXXXXXXXX``, where ``XXXXXXXX`` is a hexadecimal number, represents the 32-bit Unicode character with the specified value. For example, ``\U9`` is the tab character.
- ``\cX``, where ``X`` is a letter of the alphabet, represents the control sequence generated by pressing the control key and the specified letter. For example, ``\ci`` is the tab character

Some characters have special meaning to the shell. For example, an apostrophe ``'`` disables expansion (see :ref:`Quotes<quotes>`). To tell the shell to treat these characters literally, escape them with a backslash. For example, the command::

    echo \'hello world\'

outputs ``'hello world'`` (including the apostrophes), while the command::

    echo 'hello world'

outputs ``hello world`` (without the apostrophes). In the former case the shell treats the apostrophes as literal ``'`` characters, while in the latter case it treats them as special expansion modifiers.

The special characters and their escape sequences are:

- :code:`\ ` (backslash space) escapes the space character. This keeps the shell from splitting arguments on the escaped space.
- ``\$`` escapes the dollar character.
- ``\\`` escapes the backslash character.
- ``\*`` escapes the star character.
- ``\?`` escapes the question mark character (this is not necessary if the ``qmark-noglob`` :ref:`feature flag<featureflags>` is enabled).
- ``\~`` escapes the tilde character.
- ``\#`` escapes the hash character.
- ``\(`` escapes the left parenthesis character.
- ``\)`` escapes the right parenthesis character.
- ``\{`` escapes the left curly bracket character.
- ``\}`` escapes the right curly bracket character.
- ``\[`` escapes the left bracket character.
- ``\]`` escapes the right bracket character.
- ``\<`` escapes the less than character.
- ``\>`` escapes the more than character.
- ``\^`` escapes the circumflex character.
- ``\&`` escapes the ampersand character.
- ``\|`` escapes the vertical bar character.
- ``\;`` escapes the semicolon character.
- ``\"`` escapes the quote character.
- ``\'`` escapes the apostrophe character.

.. _redirects:

Input/Output Redirection
-----------------------------

Most programs use three input/output (I/O) streams:

- Standard input (stdin) for reading. Defaults to reading from the keyboard.
- Standard output (stdout) for writing output. Defaults to writing to the screen.
- Standard error (stderr) for writing errors and warnings. Defaults to writing to the screen.

Each stream has a number called the file descriptor (FD): 0 for stdin, 1 for stdout, and 2 for stderr.

The destination of a stream can be changed using something called *redirection*. For example, ``echo hello > output.txt``, redirects the standard output of the ``echo`` command to a text file.

- To read standard input from a file, use ``<SOURCE_FILE``.
- To write standard output to a file, use ``>DESTINATION``.
- To write standard error to a file, use ``2>DESTINATION``. [#]_
- To append standard output to a file, use ``>>DESTINATION_FILE``.
- To append standard error to a file, use ``2>>DESTINATION_FILE``.
- To not overwrite ("clobber") an existing file, use ``>?DESTINATION`` or ``2>?DESTINATION``. This is known as the "noclobber" redirection.

``DESTINATION`` can be one of the following:

- A filename. The output will be written to the specified file. Often ``>/dev/null`` to silence output by writing it to the special "sinkhole" file.
- An ampersand (``&``) followed by the number of another file descriptor like ``&2`` for standard error. The output will be written to the destination descriptor.
- An ampersand followed by a minus sign (``&-``). The file descriptor will be closed.

As a convenience, the redirection ``&>`` can be used to direct both stdout and stderr to the same destination. For example, ``echo hello &> all_output.txt`` redirects both stdout and stderr to the file ``all_output.txt``. This is equivalent to ``echo hello > all_output.txt 2>&1``.

Any arbitrary file descriptor can used in a redirection by prefixing the redirection with the FD number.

- To redirect the input of descriptor N, use ``N<DESTINATION``.
- To redirect the output of descriptor N, use ``N>DESTINATION``.
- To append the output of descriptor N to a file, use ``N>>DESTINATION_FILE``.

For example, ``echo hello 2> output.stderr`` writes the standard error (file descriptor 2) to ``output.stderr``.

It is an error to redirect a builtin, function, or block to a file descriptor above 2. However this is supported for external commands.

.. [#] Previous versions of fish also allowed specifying this as ``^DESTINATION``, but that made another character special so it was deprecated and will be removed in the future. See :ref:`feature flags<featureflags>`.

.. _pipes:

Piping
------

Another way to redirect streams is a *pipe*. A pipe connects streams with each other. Usually the standard output of one command is connected with the standard input of another. This is done by separating commands with the pipe character ``|``. For example::

    cat foo.txt | head

The command ``cat foo.txt`` sends the contents of ``foo.txt`` to stdout. This output is provided as input for the ``head`` program, which prints the first 10 lines of its input.

It is possible to pipe a different output file descriptor by prepending its FD number and the output redirect symbol to the pipe. For example::

    make fish 2>| less

will attempt to build ``fish``, and any errors will be shown using the ``less`` pager. [#]_

As a convenience, the pipe ``&|`` redirects both stdout and stderr to the same process. Note that this is different from bash, which uses ``|&``.

.. [#] A "pager" here is a program that takes output and "paginates" it. ``less`` doesn't just do pages, it allows arbitrary scrolling (even back!).

.. _syntax-job-control:

Job control
-----------

When you start a job in fish, fish itself will pause, and give control of the terminal to the program just started. Sometimes, you want to continue using the commandline, and have the job run in the background. To create a background job, append an \& (ampersand) to your command. This will tell fish to run the job in the background. Background jobs are very useful when running programs that have a graphical user interface.

Example::

  emacs &


will start the emacs text editor in the background. :ref:`fg <cmd-fg>` can be used to bring it into the foreground again when needed.

Most programs allow you to suspend the program's execution and return control to fish by pressing :kbd:`Control`\ +\ :kbd:`Z` (also referred to as ``^Z``). Once back at the fish commandline, you can start other programs and do anything you want. If you then want you can go back to the suspended command by using the :ref:`fg <cmd-fg>` (foreground) command.

If you instead want to put a suspended job into the background, use the :ref:`bg <cmd-bg>` command.

To get a listing of all currently started jobs, use the :ref:`jobs <cmd-jobs>` command.
These listed jobs can be removed with the :ref:`disown <cmd-disown>` command.

Note that functions cannot be started in the background. Functions that are stopped and then restarted in the background using the :ref:`bg <cmd-bg>` command will not execute correctly.

.. _syntax-function:

Functions
---------

Functions are programs written in the fish syntax. They group together various commands and their arguments using a single name.

For example, here's a simple function to list directories::

  function ll
      ls -l $argv
  end

The first line tells fish to define a function by the name of ``ll``, so it can be used by simply writing ``ll`` on the commandline. The second line tells fish that the command ``ls -l $argv`` should be called when ``ll`` is invoked. :ref:`$argv <variables-argv>` is a :ref:`list variable <variables-lists>`, which always contains all arguments sent to the function. In the example above, these are simply passed on to the ``ls`` command. The ``end`` on the third line ends the definition.

Calling this as ``ll /tmp/`` will end up running ``ls -l /tmp/``, which will list the contents of /tmp.

This is a kind of function known as a :ref:`wrapper <syntax-function-wrappers>` or "alias".

Fish's prompt is also defined in a function, called :ref:`fish_prompt <cmd-fish_prompt>`. It is run when the prompt is about to be displayed and its output forms the prompt::

  function fish_prompt
      # A simple prompt. Displays the current directory
      # (which fish stores in the $PWD variable)
      # and then a user symbol - a '►' for a normal user and a '#' for root.
      set -l user_char '►'
      if fish_is_root_user
          set user_char '#'
      end

      echo (set_color yellow)$PWD (set_color purple)$user_char
  end

To edit a function, you can use :ref:`funced <cmd-funced>`, and to save a function :ref:`funcsave <cmd-funcsave>`. This will store it in a function file that fish will :ref:`autoload <syntax-function-autoloading>` when needed.

The :ref:`functions <cmd-functions>` builtin can show a function's current definition (and :ref:`type <cmd-type>` will also do if given a function).

For more information on functions, see the documentation for the :ref:`function <cmd-function>` builtin.

.. _syntax-function-wrappers:

Defining aliases
^^^^^^^^^^^^^^^^

One of the most common uses for functions is to slightly alter the behavior of an already existing command. For example, one might want to redefine the ``ls`` command to display colors. The switch for turning on colors on GNU systems is ``--color=auto``. An alias, or wrapper, around ``ls`` might look like this::

  function ls
      command ls --color=auto $argv
  end

There are a few important things that need to be noted about aliases:

- Always take care to add the :ref:`$argv <variables-argv>` variable to the list of parameters to the wrapped command. This makes sure that if the user specifies any additional parameters to the function, they are passed on to the underlying command.

- If the alias has the same name as the aliased command, you need to prefix the call to the program with ``command`` to tell fish that the function should not call itself, but rather a command with the same name. If you forget to do so, the function would call itself until the end of time. Usually fish is smart enough to figure this out and will refrain from doing so (which is hopefully in your interest).

- Autoloading isn't applicable to aliases. Since, by definition, the function is created at the time the alias command is executed. You cannot autoload aliases.

To easily create a function of this form, you can use the :ref:`alias <cmd-alias>` command. Unlike other shells, this just makes functions - fish has no separate concept of an "alias", we just use the word for a function wrapper like this.

For an alternative, try :ref:`abbreviations <abbreviations>`. These are words that are expanded while you type, instead of being actual functions inside the shell.

.. _syntax-function-autoloading:

Autoloading functions
^^^^^^^^^^^^^^^^^^^^^

Functions can be defined on the commandline or in a configuration file, but they can also be automatically loaded. This has some advantages:

- An autoloaded function becomes available automatically to all running shells.
- If the function definition is changed, all running shells will automatically reload the altered version, after a while.
- Startup time and memory usage is improved, etc.

When fish needs to load a function, it searches through any directories in the :ref:`list variable <variables-lists>` ``$fish_function_path`` for a file with a name consisting of the name of the function plus the suffix ``.fish`` and loads the first it finds.

By default ``$fish_function_path`` contains the following:

- A directory for end-users to keep their own functions, usually ``~/.config/fish/functions`` (controlled by the ``XDG_CONFIG_HOME`` environment variable).
- A directory for systems administrators to install functions for all users on the system, usually ``/etc/fish/functions`` (really ``$__fish_sysconfdir/functions``).
- Directories for other software to put their own functions. These are in the directories in the ``XDG_DATA_DIRS`` environment variable, in a subdirectory called ``fish/vendor_functions.d``. The default is usually ``/usr/share/fish/vendor_functions.d`` and ``/usr/local/share/fish/vendor_functions.d``.
- The functions shipped with fish, usually installed in ``/usr/share/fish/functions`` (really ``$__fish_data_dir/functions``).

If you are unsure, your functions probably belong in ``~/.config/fish/functions``.

Autoloading also won't work for `event handlers <#event>`_, since fish cannot know that a function is supposed to be executed when an event occurs when it hasn't yet loaded the function. See the `event handlers <#event>`_ section for more information.

If you are developing another program and want to install fish functions for it, install them to the "vendor" functions directory. As this path varies from system to system, you can use ``pkgconfig`` to discover it with the output of ``pkg-config --variable functionsdir fish``. Your installation system should support a custom path to override the pkgconfig path, as other distributors may need to alter it easily.

Comments
--------

Anything after a ``#`` until the end of the line is a comment. That means it's purely for the reader's benefit, fish ignores it.

This is useful to explain what and why you are doing something::

  function ls
      # The function is called ls,
      # so we have to explicitly call `command ls` to avoid calling ourselves.
      command ls --color=auto $argv
  end

There are no multiline comments. If you want to make a comment span multiple lines, simply start each line with a ``#``.

Comments can also appear after a line like so::

  set -gx EDITOR emacs # I don't like vim.

.. _syntax-conditional:

Conditions
----------

Fish has some builtins that let you execute commands only if a specific criterion is met: :ref:`if <cmd-if>`, :ref:`switch <cmd-switch>`, :ref:`and <cmd-and>` and :ref:`or <cmd-or>`, and also the familiar :ref:`&&/|| <tut-combiners>` syntax.

The :ref:`switch <cmd-switch>` command is used to execute one of possibly many blocks of commands depending on the value of a string. See the documentation for :ref:`switch <cmd-switch>` for more information.

The other conditionals use the :ref:`exit status <variables-status>` of a command to decide if a command or a block of commands should be executed.

Unlike programming languages you might know, :ref:`if <cmd-if>` doesn't take a *condition*, it takes a *command*. If that command returned a successful :ref:`exit status <variables-status>` (that's 0), the ``if`` branch is taken, otherwise the :ref:`else <cmd-else>` branch.

Some examples::

  # Just see if the file contains the string "fish" anywhere.
  # This executes the `grep` command, which searches for a string,
  # and if it finds it returns a status of 0.
  # The `-q` switch stops it from printing any matches.
  if grep -q fish myanimals
      echo "You have fish!"
  else
      echo "You don't have fish!"
  end

  # $XDG_CONFIG_HOME is a standard place to store configuration.
  # If it's not set applications should use ~/.config.
  set -q XDG_CONFIG_HOME; and set -l configdir $XDG_CONFIG_HOME
  or set -l configdir ~/.config

For more, see the documentation for the builtins or the :ref:`Conditionals <tut-conditionals>` section of the tutorial.

Loops and blocks
----------------

Like most programming language, fish also has the familiar :ref:`while <cmd-while>` and :ref:`for <cmd-for>` loops.

``while`` works like a repeated :ref:`if <cmd-if>`::

  while true
      echo Still running
      sleep 1
  end

will print "Still running" once a second. You can abort it with ctrl-c.

``for`` loops work like in other shells, which is more like python's for-loops than e.g. C's::

  for file in *
      echo file: $file
  end

will print each file in the current directory. The part after the ``in`` is just a list of arguments, so you can use any :ref:`expansions <expand>` there::

  set moreanimals bird fox
  for animal in {cat,}fish dog $moreanimals
     echo I like the $animal
  end

If you need a list of numbers, you can use the ``seq`` command to create one::

  for i in (seq 1 5)
      echo $i
  end

:ref:`break <cmd-break>` is available to break out of a loop, and :ref:`continue <cmd-continue>` to jump to the next iteration.

:ref:`Input and output redirections <redirects>` (including :ref:`pipes <pipes>`) can also be applied to loops::

  while read -l line
      echo line: $line
  end < file

In addition there's a :ref:`begin <cmd-begin>` block that just groups commands together so you can redirect to a block or use a new :ref:`variable scope <variables-scope>` without any repetition::

  begin
     set -l foo bar # this variable will only be available in this block!
  end

.. _expand:

Parameter expansion
-------------------

When fish is given a commandline, it expands the parameters before sending them to the command. There are multiple different kinds of expansions:

- :ref:`Wildcards <expand-wildcard>`, to create filenames from patterns
- :ref:`Variable expansion <expand-variable>`, to use the value of a variable
- :ref:`Command substitution <expand-command-substitution>`, to use the output of another command
- :ref:`Brace expansion <expand-brace>`, to write lists with common pre- or suffixes in a shorter way
- :ref:`Tilde expansion <expand-home>`, to turn the ``~`` at the beginning of paths into the path to the home directory

Parameter expansion is limited to 524288 items. There is a limit to how many arguments the operating system allows for any command, and 524288 is far above it. This is a measure to stop the shell from hanging doing useless computation.

.. _expand-wildcard:

Wildcards ("Globbing")
^^^^^^^^^^^^^^^^^^^^^^

When a parameter includes an :ref:`unquoted <quotes>` ``*`` star (or "asterisk") or a ``?`` question mark, fish uses it as a wildcard to match files.

- ``*`` matches any number of characters (including zero) in a file name, not including ``/``.

- ``**`` matches any number of characters (including zero), and also descends into subdirectories. If ``**`` is a segment by itself, that segment may match zero times, for compatibility with other shells.

- ``?`` can match any single character except ``/``. This is deprecated and can be disabled via the ``qmark-noglob`` :ref:`feature flag<featureflags>`, so ``?`` will just be an ordinary character.

Other shells, such as zsh, have a much richer glob syntax, like ``**(.)`` to only match regular files. Fish does not. Instead of reinventing the whell, use programs like ``find`` to look for files. For example::

    function ff --description 'Like ** but only returns plain files.'
        # This also ignores .git directories.
        find . \( -name .git -type d -prune \) -o -type f | \
            sed -n -e '/^\.\/\.git$/n' -e 's/^\.\///p'
    end

You would then use it in place of ``**`` like this, ``my_prog (ff)``, to pass only regular files in or below $PWD to ``my_prog``. [#]_

Wildcard matches are sorted case insensitively. When sorting matches containing numbers, they are naturally sorted, so that the strings '1' '5' and '12' would be sorted like 1, 5, 12.

Hidden files (where the name begins with a dot) are not considered when wildcarding unless the wildcard string has a dot in that place.

Examples:

- ``a*`` matches any files beginning with an 'a' in the current directory.

- ``???`` matches any file in the current directory whose name is exactly three characters long.

- ``**`` matches any files and directories in the current directory and all of its subdirectories.

- ``~/.*`` matches all hidden files (also known as "dotfiles") and directories in your home directory.

For most commands, if any wildcard fails to expand, the command is not executed, :ref:`$status <variables-status>` is set to nonzero, and a warning is printed. This behavior is like what bash does with ``shopt -s failglob``. There are exactly 4 exceptions, namely :ref:`set <cmd-set>`, overriding variables in :ref:`overrides <variables-override>`, :ref:`count <cmd-count>` and :ref:`for <cmd-for>`. Their globs will instead expand to zero arguments (so the command won't see them at all), like with ``shopt -s nullglob`` in bash.

Examples::

    # List the .foo files, or warns if there aren't any.
    ls *.foo

    # List the .foo files, if any.
    set foos *.foo
    if count $foos >/dev/null
        ls $foos
    end

.. [#] Technically, unix allows filenames with newlines, and this splits the ``find`` output on newlines. If you want to avoid that, use find's ``-print0`` option and :ref:`string split0<cmd-string-split0>`.

.. _expand-variable:

Variable expansion
^^^^^^^^^^^^^^^^^^

One of the most important expansions in fish is the "variable expansion". This is the replacing of a dollar sign (``$``) followed by a variable name with the _value_ of that variable. For more on shell variables, read the :ref:`Shell variables <variables>` section.

In the simplest case, this is just something like::

    echo $HOME

which will replace ``$HOME`` with the home directory of the current user, and pass it to :ref:`echo <cmd-echo>`, which will then print it.

Sometimes a variable has no value because it is undefined or empty, and it expands to nothing::


    echo $nonexistentvariable
    # Prints no output.

To separate a variable name from text you can encase the variable within double-quotes or braces::

    echo The plural of $WORD is "$WORD"s
    # Prints "The plural of cat is cats" when $WORD is set to cat.
    echo The plural of $WORD is {$WORD}s
    # ditto

Note that without the quotes or braces, fish will try to expand a variable called ``$WORDs``, which may not exist.

The latter syntax ``{$WORD}`` is a special case of :ref:`brace expansion <expand-brace>`.

If $WORD here is undefined or an empty list, the "s" is not printed. However, it is printed if $WORD is the empty string (like after ``set WORD ""``).

Unlike all the other expansions, variable expansion also happens in double quoted strings. Inside double quotes (``"these"``), variables will always expand to exactly one argument. If they are empty or undefined, it will result in an empty string. If they have one element, they'll expand to that element. If they have more than that, the elements will be joined with spaces, unless the variable is a :ref:`path variable <variables-path>` - in that case it will use a colon (`:`) instead [#]_.

Outside of double quotes, variables will expand to as many arguments as they have elements. That means an empty list will expand to nothing, a variable with one element will expand to that element, and a variable with multiple elements will expand to each of those elements separately.

If a variable expands to nothing, it will cancel out any other strings attached to it. See the :ref:`cartesian product <cartesian-product>` section for more information.

The ``$`` symbol can also be used multiple times, as a kind of "dereference" operator (the ``*`` in C or C++), like in the following code::

    set foo a b c
    set a 10; set b 20; set c 30
    for i in (seq (count $$foo))
        echo $$foo[$i]
    end

    # Output is:
    # 10
    # 20
    # 30

``$$foo[$i]`` is "the value of the variable named by ``$foo[$i]``.

When using this feature together with list brackets, the brackets will be used from the inside out. ``$$foo[5]`` will use the fifth element of ``$foo`` as a variable name, instead of giving the fifth element of all the variables $foo refers to. That would instead be expressed as ``$$foo[1][5]`` (take the first element of ``$foo``, use it as a variable name, then give the fifth element of that).

.. [#] Unlike bash or zsh, which will join with the first character of $IFS (which usually is space).

.. _expand-command-substitution:

Command substitution
^^^^^^^^^^^^^^^^^^^^

The output of a command (or an entire :ref:`pipeline <pipes>`) can be used as the arguments to another command.

When you write a command in parenthesis like ``outercommand (innercommand)``, the ``innercommand`` will be executed first. Its output will be taken and each line given as a separate argument to ``outercommand``, which will then be executed. [#]_

If the output is piped to :ref:`string split or string split0 <cmd-string-split>` as the last step, those splits are used as they appear instead of splitting lines.

The exit status of the last run command substitution is available in the `status <#variables-status>`_ variable if the substitution happens in the context of a :ref:`set <cmd-set>` command (so ``if set -l (something)`` checks if ``something`` returned true).

Only part of the output can be used, see :ref:`index range expansion <expand-index-range>` for details.

Fish has a default limit of 100 MiB on the data it will read in a command sustitution. If that limit is reached the command (all of it, not just the command substitution - the outer command won't be executed at all) fails and ``$status`` is set to 122. This is so command substitutions can't cause the system to go out of memory, because typically your operating system has a much lower limit, so reading more than that would be useless and harmful. This limit can be adjusted with the ``fish_read_limit`` variable (`0` meaning no limit). This limit also affects the :ref:`read <cmd-read>` command.

Examples::

    # Outputs 'image.png'.
    echo (basename image.jpg .jpg).png

    # Convert all JPEG files in the current directory to the
    # PNG format using the 'convert' program.
    for i in *.jpg; convert $i (basename $i .jpg).png; end

    # Set the ``data`` variable to the contents of 'data.txt'
    # without splitting it into a list.
    begin; set -l IFS; set data (cat data.txt); end

    # Set ``$data`` to the contents of data, splitting on NUL-bytes.
    set data (cat data | string split0)


Sometimes you want to pass the output of a command to another command that only accepts files. If it's just one file, you can usually just pass it via a pipe, like::

    grep fish myanimallist1 | wc -l

but if you need multiple or the command doesn't read from standard input, "process substitution" is useful. Other shells [#]_ allow this via ``foo <(bar) <(baz)``, and fish uses the :ref:`psub <cmd-psub>` command::

    # Compare just the lines containing "fish" in two files:
    diff -u (grep fish myanimallist1 | psub) (grep fish myanimallist2 | psub)

This creates a temporary file, stores the output of the command in that file and prints the filename, so it is given to the outer command.

.. [#] Setting ``$IFS`` to empty will disable line splitting. This is deprecated, use :ref:`string split <cmd-string-split>` instead.
.. [#] Bash and Zsh at least, though it is a POSIX extension

.. _expand-brace:

Brace expansion
^^^^^^^^^^^^^^^

Curly braces can be used to write comma-separated lists. They will be expanded with each element becoming a new parameter, with the surrounding string attached. This is useful to save on typing, and to separate a variable name from surrounding text.

Examples::

  > echo input.{c,h,txt}
  input.c input.h input.txt

  # Move all files with the suffix '.c' or '.h' to the subdirectory src.
  > mv *.{c,h} src/

  # Make a copy of `file` at `file.bak`.
  > cp file{,.bak}

  > set -l dogs hot cool cute "good "
  > echo {$dogs}dog
  hotdog cooldog cutedog good dog

If there is no "," or variable expansion between the curly braces, they will not be expanded::

    # This {} isn't special
    > echo foo-{}
    foo-{}
    # This passes "HEAD@{2}" to git
    > git reset --hard HEAD@{2}
    > echo {{a,b}}
    {a} {b} # because the inner brace pair is expanded, but the outer isn't.

If after expansion there is nothing between the braces, the argument will be removed (see :ref:`the cartesian product section <cartesian-product>`)::

    > echo foo-{$undefinedvar}
    # Output is an empty line, just like a bare `echo`.

If there is nothing between a brace and a comma or two commas, it's interpreted as an empty element::

    > echo {,,/usr}/bin
    /bin /bin /usr/bin

To use a "," as an element, :ref:`quote <quotes>` or :ref:`escape <escapes>` it.

.. _cartesian-product:

Combining lists (Cartesian Product)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

When lists are expanded with other parts attached, they are expanded with these parts still attached. Even if two lists are attached to each other, they are expanded in all combinations. This is referred to as the `cartesian product` (like in mathematics), and works basically like :ref:`brace expansion <expand-brace>`.

Examples::

    # Brace expansion is the most familiar:
    # All elements in the brace combine with the parts outside of the braces
    >_ echo {good,bad}" apples"
    good apples bad apples

    # The same thing happens with variable expansion.
    >_ set -l a x y z
    >_ set -l b 1 2 3

    # $a is {x,y,z}, $b is {1,2,3},
    # so this is `echo {x,y,z}{1,2,3}`
    >_ echo $a$b
    x1 y1 z1 x2 y2 z2 x3 y3 z3

    # Same thing if something is between the lists
    >_ echo $a"-"$b
    x-1 y-1 z-1 x-2 y-2 z-2 x-3 y-3 z-3

    # Or a brace expansion and a variable
    >_ echo {x,y,z}$b
    x1 y1 z1 x2 y2 z2 x3 y3 z3

    # A combined brace-variable expansion
    >_ echo {$b}word
    1word 2word 3word

    # Special case: If $c has no elements, this expands to nothing
    >_ echo {$c}word
    # Output is an empty line

Sometimes this may be unwanted, especially that tokens can disappear after expansion. In those cases, you should double-quote variables - ``echo "$c"word``.

This also happens after :ref:`command substitution <expand-command-substitution>`. To avoid tokens disappearing there, make the inner command return a trailing newline, or store the output in a variable and double-quote it.

E.g.

::

    >_ set b 1 2 3
    >_ echo (echo x)$b
    x1 x2 x3
    >_ echo (printf '%s' '')banana
    # the printf prints nothing, so this is nothing times "banana",
    # which is nothing.
    >_ echo (printf '%s\n' '')banana
    # the printf prints a newline,
    # so the command substitution expands to an empty string,
    # so this is `''banana`
    banana

This can be quite useful. For example, if you want to go through all the files in all the directories in $PATH, use::

    for file in $PATH/*

Because :ref:`$PATH <path>` is a list, this expands to all the files in all the directories in it. And if there are no directories in $PATH, the right answer here is to expand to no files.

.. _expand-index-range:

Index range expansion
^^^^^^^^^^^^^^^^^^^^^

Sometimes it's necessary to access only some of the elements of a list, or some of the lines a command substitution outputs. Both are possible in fish by writing a set of indices in brackets, like::

  $var[2]
  # or
  $var[1..3]

In index brackets, fish understands ranges written like ``a..b`` ('a' and 'b' being indices). They are expanded into a sequence of indices from a to b (so ``a a+1 a+2 ... b``), going up if b is larger and going down if a is larger. Negative indices can also be used - they are taken from the end of the list, so ``-1`` is the last element, and ``-2`` the one before it. If an index doesn't exist the range is clamped to the next possible index.

If a list has 5 elements the indices go from 1 to 5, so a range of ``2..16`` will only go from element 2 to element 5.

If the end is negative the range always goes up, so ``2..-2`` will go from element 2 to 4, and ``2..-16`` won't go anywhere because there is no way to go from the second element to one that doesn't exist, while going up.
If the start is negative the range always goes down, so ``-2..1`` will go from element 4 to 1, and ``-16..2`` won't go anywhere because there is no way to go from the second element to one that doesn't exist, while going down.

A missing starting index in a range defaults to 1. This is allowed if the range is the first index expression of the sequence. Similarly, a missing ending index, defaulting to -1 is allowed for the last index range in the sequence.

Multiple ranges are also possible, separated with a space.

Some examples::


    echo (seq 10)[1 2 3]
    # Prints: 1 2 3

    # Limit the command substitution output
    echo (seq 10)[2..5]
    # Uses elements from 2 to 5
    # Output is: 2 3 4 5

    echo (seq 10)[7..]
    # Prints: 7 8 9 10

    # Use overlapping ranges:
    echo (seq 10)[2..5 1..3]
    # Takes elements from 2 to 5 and then elements from 1 to 3
    # Output is: 2 3 4 5 1 2 3

    # Reverse output
    echo (seq 10)[-1..1]
    # Uses elements from the last output line to
    # the first one in reverse direction
    # Output is: 10 9 8 7 6 5 4 3 2 1

    # The command substitution has only one line,
    # so these will result in empty output:
    echo (echo one)[2..-1]
    echo (echo one)[-3..1]

The same works when setting or expanding variables::


    # Reverse path variable
    set PATH $PATH[-1..1]
    # or
    set PATH[-1..1] $PATH

    # Use only n last items of the PATH
    set n -3
    echo $PATH[$n..-1]

Variables can be used as indices for expansion of variables, like so::

    set index 2
    set letters a b c d
    echo $letters[$index] # returns 'b'

However using variables as indices for command substitution is currently not supported, so::

    echo (seq 5)[$index] # This won't work

    set sequence (seq 5) # It needs to be written on two lines like this.
    echo $sequence[$index] # returns '2'

When using indirect variable expansion with multiple ``$`` (``$$name``), you have to give all indices up to the variable you want to slice::

    > set -l list 1 2 3 4 5
    > set -l name list
    > echo $$name[1]
    1 2 3 4 5
    > echo $$name[1..-1][1..3] # or $$name[1][1..3], since $name only has one element.
    1 2 3

.. _expand-home:

Home directory expansion
^^^^^^^^^^^^^^^^^^^^^^^^

The ``~`` (tilde) character at the beginning of a parameter, followed by a username, is expanded into the home directory of the specified user. A lone ``~``, or a ``~`` followed by a slash, is expanded into the home directory of the process owner::

  ls ~/Music # lists my music directory

  echo ~root # prints root's home directory, probably "/root"


.. _combine:

Combining different expansions
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

All of the above expansions can be combined. If several expansions result in more than one parameter, all possible combinations are created.

When combining multiple parameter expansions, expansions are performed in the following order:

- Command substitutions
- Variable expansions
- Bracket expansion
- Wildcard expansion

Expansions are performed from right to left, nested bracket expansions are performed from the inside and out.

Example:

If the current directory contains the files 'foo' and 'bar', the command ``echo a(ls){1,2,3}`` will output ``abar1 abar2 abar3 afoo1 afoo2 afoo3``.

.. _variables:

Shell variables
---------------

Variables are a way to save data and pass it around. They can be used just by the shell, or they can be ":ref:`exported <variables-export>`", so that a copy of the variable is available to any external command the shell starts. An exported variable is referred to as an "environment variable".

To set a variable value, use the :ref:`set <cmd-set>` command. A variable name can not be empty and can contain only letters, digits, and underscores. It may begin and end with any of those characters.

Example:

To set the variable ``smurf_color`` to the value ``blue``, use the command ``set smurf_color blue``.

After a variable has been set, you can use the value of a variable in the shell through :ref:`variable expansion <expand-variable>`.

Example::

    set smurf_color blue
    echo Smurfs are usually $smurf_color
    set pants_color red
    echo Papa smurf, who is $smurf_color, wears $pants_color pants

So you set a variable with ``set``, and use it with a ``$`` and the name.

.. _variables-scope:

Variable scope
^^^^^^^^^^^^^^

There are three kinds of variables in fish: universal, global and local variables.

- Universal variables are shared between all fish sessions a user is running on one computer.
- Global variables are specific to the current fish session, and will never be erased unless explicitly requested by using ``set -e``.
- Local variables are specific to the current fish session, and associated with a specific block of commands, and automatically erased when a specific block goes out of scope. A block of commands is a series of commands that begins with one of the commands ``for``, ``while`` , ``if``, ``function``, ``begin`` or ``switch``, and ends with the command ``end``.

Variables can be explicitly set to be universal with the ``-U`` or ``--universal`` switch, global with the ``-g`` or ``--global`` switch, or local with the ``-l`` or ``--local`` switch.  The scoping rules when creating or updating a variable are:

- When a scope is explicitly given, it will be used. If a variable of the same name exists in a different scope, that variable will not be changed.

- When no scope is given, but a variable of that name exists, the variable of the smallest scope will be modified. The scope will not be changed.

- As a special case, when no scope is given and no variable has been defined the variable will belong to the scope of the currently executing *function*. Note that this is different from the ``--local`` flag, which would make the variable local to the current *block*.

There can be many variables with the same name, but different scopes. When you :ref:`use a variable <expand-variable>`, the smallest scoped variable of that name will be used. If a local variable exists, it will be used instead of the global or universal variable of the same name.


Example:

There are a few possible uses for different scopes.

Typically inside funcions you should use local scope::

    function something
        set -l file /path/to/my/file
        if not test -e "$file"
            set file /path/to/my/otherfile
        end
    end

If you want to set something in config.fish, or set something in a function and have it available for the rest of the session, global scope is a good choice::

    # Don't shorten the working directory in the prompt
    set -g fish_prompt_pwd_dir_length 0

    # Set my preferred cursor style:
    function setcursors
       set -g fish_cursor_default block
       set -g fish_cursor_insert line
       set -g fish_cursor_visual underscore
    end

    # Set my language (also :ref:`exported <variables-export>`):
    set -gx LANG de_DE.UTF-8

If you want to set some personal customization, universal variables are nice::

     # Typically you'd run this interactively, fish takes care of keeping it.
     set -U fish_color_autosuggestion 555

The following code will not output anything::

    begin
        # This is a nice local scope where all variables will die
        set -l pirate 'There be treasure in them thar hills'
        set captain Space, the final frontier
    end

    echo $pirate
    # This will not output anything, since the pirate was local
    echo $captain
    # This will output the good Captain's speech since $captain had function-scope.

.. _variables-override:

Overriding variables for a single command
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If you want to override a variable for a single command, you can use "var=val" statements before the command::

  # Call git status on another directory
  # (can also be done via `git -C somerepo status`)
  GIT_DIR=somerepo git status

Note that, unlike other shells, fish will first set the variable and then perform other expansions on the line, so::

  set foo banana
  foo=gagaga echo $foo # prints gagaga, while in other shells it might print "banana"

Multiple elements can be given in a :ref:`brace expansion<expand-brace>`::

  # Call bash with a reasonable default path.
  PATH={/usr,}/{s,}bin bash

Or with a :ref:`glob <expand-wildcard>`::

  # Run vlc on all mp3 files in the current directory
  # If no file exists it will still be run with no arguments
  mp3s=*.mp3 vlc $mp3s

Unlike other shells, this does *not* inhibit any lookup (aliases or similar). Calling a command after setting a variable override will result in the exact same command being run.

This syntax is supported since fish 3.1.

.. _variables-universal:

More on universal variables
^^^^^^^^^^^^^^^^^^^^^^^^^^^

Universal variables are variables that are shared between all the user's fish sessions on the computer. Fish stores many of its configuration options as universal variables. This means that in order to change fish settings, all you have to do is change the variable value once, and it will be automatically updated for all sessions, and preserved across computer reboots and login/logout.

To see universal variables in action, start two fish sessions side by side, and issue the following command in one of them ``set fish_color_cwd blue``. Since ``fish_color_cwd`` is a universal variable, the color of the current working directory listing in the prompt will instantly change to blue on both terminals.

`Universal variables <#variables-universal>`_ are stored in the file ``.config/fish/fish_variables``. Do not edit this file directly, as your edits may be overwritten. Edit the variables through fish scripts or by using fish interactively instead.

Do not append to universal variables in :ref:`config.fish <initialization>`, because these variables will then get longer with each new shell instance. Instead, simply set them once at the command line.


.. _variables-functions:

Variable scope for functions
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

When calling a function, all current local variables temporarily disappear. This shadowing of the local scope is needed since the variable namespace would become cluttered, making it very easy to accidentally overwrite variables from another function.

For example::

    function shiver
        set phrase 'Shiver me timbers'
    end

    function avast
        set --local phrase 'Avast, mateys'
        # Calling the shiver function here can not
        # change any variables in the local scope
        shiver
        echo $phrase
    end
    avast

    # Outputs "Avast, mateys"



.. _variables-export:

Exporting variables
^^^^^^^^^^^^^^^^^^^

Variables in fish can be "exported", so they will be inherited by any commands started by fish. In particular, this is necessary for variables used to configure external commands like $LESS or $GOPATH, but also for variables that contain general system settings like $PATH or $LANGUAGE. If an external command needs to know a variable, it needs to be exported.

This also applies to fish - when it starts up, it receives environment variables from its parent (usually the terminal). These typically include system configuration like :ref:`$PATH <PATH>` and :ref:`locale variables <variables-locale>`.

Variables can be explicitly set to be exported with the ``-x`` or ``--export`` switch, or not exported with the ``-u`` or ``--unexport`` switch.  The exporting rules when setting a variable are identical to the scoping rules for variables:

- If a variable is explicitly set to either be exported or not exported, that setting will be honored.

- If a variable is not explicitly set to be exported or not exported, but has been previously defined, the previous exporting rule for the variable is kept.

- Otherwise, by default, the variable will not be exported.

- If a variable has local scope and is exported, any function called receives a *copy* of it, so any changes it makes to the variable disappear once the function returns.

- Global variables are accessible to functions whether they are exported or not.

As a naming convention, exported variables are in uppercase and unexported variables are in lowercase.

For example::

    set -gx ANDROID_HOME ~/.android # /opt/android-sdk
    set -gx CDPATH . ~ (test -e ~/Videos; and echo ~/Videos)
    set -gx EDITOR emacs -nw
    set -gx GOPATH ~/dev/go
    set -gx GTK2_RC_FILES "$XDG_CONFIG_HOME/gtk-2.0/gtkrc"
    set -gx LESSHISTFILE "-"

Note: Exporting is not a :ref:`scope <variables-scope>`, but an additional state. It typically makes sense to make exported variables global as well, but local-exported variables can be useful if you need something more specific than :ref:`Overrides <variables-override>`. They are *copied* to functions so the function can't alter them outside, and still available to commands.

.. _variables-lists:

Lists
^^^^^

Fish can store a list (or an "array" if you wish) of multiple strings inside of a variable::

   > set mylist first second third
   > printf '%s\n' $mylist # prints each element on its own line
   first
   second
   third

To access one element of a list, use the index of the element inside of square brackets, like this::

   echo $PATH[3]

Note that list indices start at 1 in fish, not 0 like in other languages. This is because it requires less subtracting of 1 and many common Unix tools like ``seq`` work better with it (``seq 5`` prints 1 to 5, not 0 to 5). An invalid index is silently ignored resulting in no value (not even an empty string, just no argument at all).

If you don't use any brackets, all the elements of the list will be passed to the command as separate items. This means you can iterate over a list with ``for``::

    for i in $PATH
        echo $i is in the path
    end

This goes over every directory in $PATH separately and prints a line saying it is in the path.

To create a variable ``smurf``, containing the items ``blue`` and ``small``, simply write::

    set smurf blue small

It is also possible to set or erase individual elements of a list::

    # Set smurf to be a list with the elements 'blue' and 'small'
    set smurf blue small

    # Change the second element of smurf to 'evil'
    set smurf[2] evil

    # Erase the first element
    set -e smurf[1]

    # Output 'evil'
    echo $smurf


If you specify a negative index when expanding or assigning to a list variable, the index will be taken from the *end* of the list. For example, the index -1 is the last element of the list::

    > set fruit apple orange banana
    > echo $fruit[-1]
    banana

    > echo $fruit[-2..-1]
    orange
    banana

    > echo $fruit[-1..1] # reverses the list
    banana
    orange
    apple

As you see, you can use a range of indices, see :ref:`index range expansion <expand-index-range>` for details.

All lists are one-dimensional and can't contain other lists, although it is possible to fake nested lists using dereferencing - see :ref:`variable expansion <expand-variable>`.

When a list is exported as an environment variable, it is either space or colon delimited, depending on whether it is a :ref:`path variable <variables-path>`::

    > set -x smurf blue small
    > set -x smurf_PATH forest mushroom
    > env | grep smurf
    smurf=blue small
    smurf_PATH=forest:mushroom

Fish automatically creates lists from all environment variables whose name ends in PATH (like $PATH, $CDPATH or $MANPATH), by splitting them on colons. Other variables are not automatically split.

Lists can be inspected with the :ref:`count <cmd-count>` or the :ref:`contains <cmd-contains>` commands::

    count $smurf
    # 2

    contains blue $smurf
    # key found, exits with status 0

    > contains -i blue $smurf
    1

A nice thing about lists is that they are passed to commands one element as one argument, so once you've set your list, you can just pass it::

  set -l grep_args -r "my string"
  grep $grep_args . # will run the same as `grep -r "my string"` .

Unlike other shells, fish does not do "word splitting" - elements in a list stay as they are, even if they contain spaces or tabs.

.. _variables-argv:

Argument Handling
^^^^^^^^^^^^^^^^^

An important list is ``$argv``, which contains the arguments to a function or script. For example::

  function myfunction
      echo $argv[1]
      echo $argv[3]
  end

This function takes whatever arguments it gets and prints the first and third::

  > myfunction first second third
  first
  third

  > myfunction apple cucumber banana
  apple
  banana

Commandline tools often get various options and flags and positional arguments, and $argv would contain all of these.

A more robust approach to argument handling is :ref:`argparse <cmd-argparse>`, which checks the defined options and puts them into various variables, leaving only the positional arguments in $argv. Here's a simple example::

  function mybetterfunction
      argparse h/help s/second -- $argv
      or return # exit if argparse failed because it found an option it didn't recognize - it will print an error

      # If -h or --help is given, we print a little help text and return
      if set -q _flag_help
          echo "mybetterfunction [-h|--help] [-s|--second] [ARGUMENTS...]"
          return 0
      end

      # If -s or --second is given, we print the second argument,
      # not the first and third.
      if set -q _flag_second
          echo $argv[2]
      else
          echo $argv[1]
          echo $argv[3]
      end
  end

The options will be *removed* from $argv, so $argv[2] is the second *positional* argument now::

  > mybetterfunction first -s second third
  second

.. _variables-path:

PATH variables
^^^^^^^^^^^^^^

Path variables are a special kind of variable used to support colon-delimited path lists including PATH, CDPATH, MANPATH, PYTHONPATH, etc. All variables that end in "PATH" (case-sensitive) become PATH variables.

PATH variables act as normal lists, except they are implicitly joined and split on colons.

::

    set MYPATH 1 2 3
    echo "$MYPATH"
    # 1:2:3
    set MYPATH "$MYPATH:4:5"
    echo $MYPATH
    # 1 2 3 4 5
    echo "$MYPATH"
    # 1:2:3:4:5

Variables can be marked or unmarked as PATH variables via the ``--path`` and ``--unpath`` options to ``set``.

.. _variables-special:

Special variables
^^^^^^^^^^^^^^^^^

You can change the settings of fish by changing the values of certain variables.

.. _PATH:

- ``PATH``, a list of directories in which to search for commands

- ``CDPATH``, a list of directories in which the :ref:`cd <cmd-cd>` builtin looks for a new directory.

- The locale variables ``LANG``, ``LC_ALL``, ``LC_COLLATE``, ``LC_CTYPE``, ``LC_MESSAGES``, ``LC_MONETARY``, ``LC_NUMERIC`` and ``LC_TIME`` set the language option for the shell and subprograms. See the section `Locale variables <#variables-locale>`_ for more information.

- A number of variable starting with the prefixes ``fish_color`` and ``fish_pager_color``. See `Variables for changing highlighting colors <#variables-color>`__ for more information.

- ``fish_ambiguous_width`` controls the computed width of ambiguous-width characters. This should be set to 1 if your terminal renders these characters as single-width (typical), or 2 if double-width.

- ``fish_emoji_width`` controls whether fish assumes emoji render as 2 cells or 1 cell wide. This is necessary because the correct value changed from 1 to 2 in Unicode 9, and some terminals may not be aware. Set this if you see graphical glitching related to emoji (or other "special" characters). It should usually be auto-detected.

- ``FISH_DEBUG`` and ``FISH_DEBUG_OUTPUT`` control what debug output fish generates and where it puts it, analogous to the ``--debug`` and ``--debug-output`` options. These have to be set on startup, via e.g. ``FISH_DEBUG='reader*' FISH_DEBUG_OUTPUT=/tmp/fishlog fish``.

- ``fish_escape_delay_ms`` sets how long fish waits for another key after seeing an escape, to distinguish pressing the escape key from the start of an escape sequence. The default is 30ms. Increasing it increases the latency but allows pressing escape instead of alt for alt+character bindings. For more information, see :ref:`the chapter in the bind documentation <cmd-bind-escape>`.

- ``fish_greeting``, the greeting message printed on startup. This is printed by a function of the same name that can be overridden for more complicated changes (see :ref:`funced <cmd-funced>`

- ``fish_handle_reflow``, determines whether fish should try to repaint the commandline when the terminal resizes. In terminals that reflow text this should be disabled. Set it to 1 to enable, anything else to disable.

- ``fish_history``, the current history session name. If set, all subsequent commands within an
  interactive fish session will be logged to a separate file identified by the value of the
  variable. If unset, the default session name "fish" is used. If set to an
  empty string, history is not saved to disk (but is still available within the interactive
  session).

- ``fish_trace``, if set and not empty, will cause fish to print commands before they execute, similar to ``set -x`` in bash. The trace is printed to the path given by the :ref:`--debug-output <cmd-fish>` option to fish (stderr by default).

- ``fish_user_paths``, a list of directories that are prepended to ``PATH``. This can be a universal variable.

- ``umask``, the current file creation mask. The preferred way to change the umask variable is through the :ref:`umask <cmd-umask>` function. An attempt to set umask to an invalid value will always fail.

- ``BROWSER``, your preferred web browser. If this variable is set, fish will use the specified browser instead of the system default browser to display the fish documentation.

Fish also provides additional information through the values of certain environment variables. Most of these variables are read-only and their value can't be changed with ``set``.

- ``_``, the name of the currently running command (though this is deprecated, and the use of ``status current-command`` is preferred).

- ``argv``, a list of arguments to the shell or function. ``argv`` is only defined when inside a function call, or if fish was invoked with a list of arguments, like ``fish myscript.fish foo bar``. This variable can be changed.

- ``CMD_DURATION``, the runtime of the last command in milliseconds.

- ``COLUMNS`` and ``LINES``, the current size of the terminal in height and width. These values are only used by fish if the operating system does not report the size of the terminal. Both variables must be set in that case otherwise a default of 80x24 will be used. They are updated when the window size changes.

- ``fish_kill_signal``, the signal that terminated the last foreground job, or 0 if the job exited normally.

- ``fish_pid``, the process ID (PID) of the shell.

- ``history``, a list containing the last commands that were entered.

- ``HOME``, the user's home directory. This variable can be changed.

- ``hostname``, the machine's hostname.

- ``IFS``, the internal field separator that is used for word splitting with the :ref:`read <cmd-read>` builtin. Setting this to the empty string will also disable line splitting in `command substitution <#expand-command-substitution>`_. This variable can be changed.

- ``last_pid``, the process ID (PID) of the last background process.

- ``PWD``, the current working directory.

- ``pipestatus``, a list of exit statuses of all processes that made up the last executed pipe.

- ``SHLVL``, the level of nesting of shells. Fish increments this in interactive shells, otherwise it simply passes it along.

- ``status``, the `exit status <#variables-status>`_ of the last foreground job to exit. If the job was terminated through a signal, the exit status will be 128 plus the signal number.

- ``status_generation``, the "generation" count of ``$status``. This will be incremented only when the previous command produced an explicit status. (For example, background jobs will not increment this).

- ``USER``, the current username. This variable can be changed.

- ``version``, the version of the currently running fish (also available as ``FISH_VERSION`` for backward compatibility).

As a convention, an uppercase name is usually used for exported variables, while lowercase variables are not exported. (``CMD_DURATION`` is an exception for historical reasons). This rule is not enforced by fish, but it is good coding practice to use casing to distinguish between exported and unexported variables.

Fish also uses some variables internally, their name usually starting with ``__fish``. These are internal and should not typically be modified directly.

.. _variables-status:

The status variable
^^^^^^^^^^^^^^^^^^^

Whenever a process exits, an exit status is returned to the program that started it (usually the shell). This exit status is an integer number, which tells the calling application how the execution of the command went. In general, a zero exit status means that the command executed without problem, but a non-zero exit status means there was some form of problem.

Fish stores the exit status of the last process in the last job to exit in the ``status`` variable.

If fish encounters a problem while executing a command, the status variable may also be set to a specific value:

- 0 is generally the exit status of fish commands if they successfully performed the requested operation.

- 1 is generally the exit status of fish commands if they failed to perform the requested operation.

- 121 is generally the exit status of fish commands if they were supplied with invalid arguments.

- 123 means that the command was not executed because the command name contained invalid characters.

- 124 means that the command was not executed because none of the wildcards in the command produced any matches.

- 125 means that while an executable with the specified name was located, the operating system could not actually execute the command.

- 126 means that while a file with the specified name was located, it was not executable.

- 127 means that no function, builtin or command with the given name could be located.

If a process exits through a signal, the exit status will be 128 plus the number of the signal.


.. _variables-color:

Syntax highlighting variables
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The colors used by fish for syntax highlighting can be configured by changing the values of a various variables. The value of these variables can be one of the colors accepted by the :ref:`set_color <cmd-set_color>` command. The ``--bold`` or ``-b`` switches accepted by ``set_color`` are also accepted.


Example: to make errors highlighted and red, use::

    set fish_color_error red --bold


The following variables are available to change the highlighting colors in fish:

==========================================                 =====================================================================
Variable                                                   Meaning
==========================================                 =====================================================================
``fish_color_normal``                                      default color
``fish_color_command``                                     commands like echo
``fish_color_keyword``                                     keywords like if - this falls back on command color if unset
``fish_color_quote``                                       quoted text like "abc"
``fish_color_redirection``                                 IO redirections like >/dev/null
``fish_color_end``                                         process separators like ';' and '&'
``fish_color_error``                                       syntax errors
``fish_color_param``                                       ordinary command parameters
``fish_color_comment``                                     comments like '# important'
``fish_color_selection``                                   selected text in vi visual mode
``fish_color_operator``                                    parameter expansion operators like '*' and '~'
``fish_color_escape``                                      character escapes like '\n' and '\x70'
``fish_color_autosuggestion``                              autosuggestions (the proposed rest of a command)
``fish_color_cwd``                                         the current working directory in the default prompt
``fish_color_user``                                        the username in the default prompt
``fish_color_host``                                        the hostname in the default prompt
``fish_color_host_remote``                                 the hostname in the default prompt for remote sessions (like ssh)
``fish_color_cancel``                                      the '^C' indicator on a canceled command
``fish_color_search_match``                                history search matches and selected pager items (background only)
==========================================                 =====================================================================

.. _variables-color-pager:

Pager color variables
^^^^^^^^^^^^^^^^^^^^^^^

fish will sometimes present a list of choices in a table, called the pager.

Example: to set the background of each pager row, use::

    set fish_pager_color_background --background=white

To have black text on alternating white and gray backgrounds::

    set fish_pager_color_prefix black
    set fish_pager_color_completion black
    set fish_pager_color_description black
    set fish_pager_color_background --background=white
    set fish_pager_color_secondary_background --background=brwhite

Variables affecting the pager colors:

==========================================                 ===========================================================
Variable                                                   Meaning
==========================================                 ===========================================================
``fish_pager_color_progress``                              the progress bar at the bottom left corner
``fish_pager_color_background``                            the background color of a line
``fish_pager_color_prefix``                                the prefix string, i.e. the string that is to be completed
``fish_pager_color_completion``                            the completion itself, i.e. the proposed rest of the string
``fish_pager_color_description``                           the completion description
``fish_pager_color_selected_background``                   background of the selected completion
``fish_pager_color_selected_prefix``                       prefix of the selected completion
``fish_pager_color_selected_completion``                   suffix of the selected completion
``fish_pager_color_selected_description``                  description of the selected completion
``fish_pager_color_secondary_background``                  background of every second unselected completion
``fish_pager_color_secondary_prefix``                      prefix of every second unselected completion
``fish_pager_color_secondary_completion``                  suffix of every second unselected completion
``fish_pager_color_secondary_description``                 description of every second unselected completion
==========================================                 ===========================================================

.. _variables-locale:

Locale variables
^^^^^^^^^^^^^^^^

The "locale" of a program is its set of language and regional settings. In UNIX, there are a few separate variables to control separate things - ``LC_CTYPE`` defines the text encoding while ``LC_TIME`` defines the time format.

The locale variables are: ``LANG``, ``LC_ALL``, ``LC_COLLATE``, ``LC_CTYPE``, ``LC_MESSAGES``,  ``LC_MONETARY``, ``LC_NUMERIC`` and ``LC_TIME``. These variables work as follows: ``LC_ALL`` forces all the aspects of the locale to the specified value. If ``LC_ALL`` is set, all other locale variables will be ignored (this is typically not recommended!). The other ``LC_`` variables set the specified aspect of the locale information. ``LANG`` is a fallback value, it will be used if none of the ``LC_`` variables are specified.

The most common way to set the locale to use a command like ``set -gx LANG en_GB.utf8``, which sets the current locale to be the English language, as used in Great Britain, using the UTF-8 character set. That way any program that requires one setting differently can easily override just that and doesn't have to resort to LC_ALL. For a list of available locales on your system, try ``locale -a``.


.. _builtin-overview:

Builtin commands
----------------

Fish includes a number of commands in the shell directly. We call these "builtins". These include:

- Builtins that manipulate the shell state - :ref:`cd <cmd-cd>` changes directory, :ref:`set <cmd-set>` sets variables
- Builtins for dealing with data, like :ref:`string <cmd-string>` for strings and :ref:`math <cmd-math>` for numbers, :ref:`count <cmd-count>` for counting lines or arguments
- :ref:`status <cmd-status>` for asking about the shell's status
- :ref:`printf <cmd-printf>` and :ref:`echo <cmd-echo>` for creating output
- :ref:`test <cmd-test>` for checking conditions
- :ref:`argparse <cmd-argparse>` for parsing function arguments
- :ref:`source <cmd-source>` to read a script in the current shell (so changes to variables stay) and :ref:`eval <cmd-eval>` to execute a string as script
- :ref:`random <cmd-random>` to get random numbers or pick a random element from a list

For a list of all builtins, use ``builtin -n``.

For a list of all builtins, functions and commands shipped with fish, see the :ref:`list of commands <Commands>`. The documentation is also available by using the ``--help`` switch.

.. _identifiers:

Shell variable and function names
---------------------------------

The names given to variables and functions (so called "identifiers") have to follow certain rules:

- A variable name cannot be empty. It can contain only letters, digits, and underscores. It may begin and end with any of those characters.

- A function name cannot be empty. It may not begin with a hyphen ("-") and may not contain a slash ("/"). All other characters, including a space, are valid.

- A bind mode name (e.g., ``bind -m abc ...``) must be a valid variable name.

Other things have other restrictions. For instance what is allowed for file names depends on your system, but at the very least they cannot contain a "/" (because that is the path separator) or NULL byte (because that is how UNIX ends strings).
