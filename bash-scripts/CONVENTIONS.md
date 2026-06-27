# When writing bash scripts, you MUST follow these principles

- Code should be easy to read and understand.

- Keep the code as simple as possible. Avoid unnecessary complexity.

- Use meaningful names for variables, functions, etc. Names should reveal intent.

- Functions should be small and do one thing well. They should not exceed a few lines.

- Function names should describe the action being performed.

- Prefer fewer arguments in functions. Ideally, aim for no more than two or three.

- Only use comments when necessary, as they can become outdated.\
  Instead, strive to make the code self-explanatory.

- When comments are used, they should add useful information that is not\
  readily apparent from the code itself.

- Properly handle errors and exceptions to ensure the software's robustness.

- Consider security implications of the code.\
  Implement security best practices to protect against vulnerabilities and attacks.

- Executables must start with `#!/usr/bin/env bash` and minimal flags.

- Use set to set shell options so that calling your script as `bash script_name`\
  does not break its functionality.

- Shell should only be used for small utilities or simple wrapper scripts.

- If you are writing a script that is more than 100 lines long, or that uses\
  non-straightforward control flow logic, suggest a rewrite in a more\
  structured language now.

- Executables should have a .sh extension or no extension.

- Libraries must have a .sh extension and should not be executable.

- SUID and SGID are forbidden on shell scripts.

- All error messages should go to STDERR.

- Start each file with a description of its contents.

- Every file must have a top-level comment including a brief overview of its contents.

- A copyright notice and author information are optional.

- Any function that is not both obvious and short must have a function header comment.\
  Any function in a library must have a function header comment regardless of\
  length or complexity.

- It should be possible for someone else to learn how to use your program or\
  to use a function in your library by reading the comments (and self-help,\
  if provided) without reading the code.

- All function header comments should describe the intended API behaviour.

- Description of a function includes\
  `Globals: List of global variables used and modified`\
  `Arguments: Arguments taken`,`Outputs: Output to STDOUT or STDERR`\
  `Returns: Returned values other than the default exit status of last command`.

- Comment tricky, non-obvious, interesting or important parts of your code.

- Indent 2 spaces. No tabs.

- Maximum line length is 80 characters.

- If you have to write literal strings that are longer than 80 characters,\
  this should be done with a here document or an embedded newline if possible.

- Pipelines should be split one per line if they don’t all fit on one line.\
  If a pipeline all fits on one line, it should be on one line.

- Put `; then` and `; do` on the same line as the `if`, `for`, or `while`.

- `else` should be on its own line and closing statements (`fi` and `done`) should be\
  on their own line vertically aligned with the opening statement.

- Although it is possible to omit `in "$@"` in for loops we recommend consistently\
  including it for clarity.

- In case statements, indent alternatives by 2 spaces.\
  One-line alternatives need a space after the close parenthesis of the pattern\
  and before the `;;`\
  Long or multi-command alternatives should be split over \
  multiple lines with the pattern, actions, \
  and `;;` on separate lines.

- In order of precedence: Stay consistent with what you find;\
  quote your variables; prefer `"${var}"` over `"$var"`.

- Use `"$@"` unless you have a specific reason to use `"$*"`, such as simply\
  appending the arguments to a string in a message or log.

- Use `$(command)` instead of backticks.

- `[[…]]` is preferred over `[ … ]`, test and `/usr/bin/[`.

- Use quotes rather than filler characters where possible.

- Use an explicit path when doing wildcard expansion of filenames.

- `eval` should be avoided.

- Bash arrays should be used to store lists of elements, to avoid quoting\
  complications. This particularly applies to argument lists.

- Given the choice between invoking a shell builtin and invoking a separate process,\
  choose the builtin.

- Always check return values and give informative return values.

- A function called `main` is required for scripts long enough to contain\
  at least one other function.

- Put all functions together in the file just below constants.\
  Don’t hide executable code between functions.\
  Doing so makes the code difficult to follow and results in nasty surprises \
  when debugging.

- If you’ve got functions, put them all together near the top of the file.\
  Only includes, set statements and setting constants may be done\
  before declaring functions.

- Declare function-specific variables with local. Remember that bash uses dynamic scoping i.e., local variables can be \
  accessed by called functions with the function's body.

- File names should be lowercase, with underscores to separate words if desired.

- Constants and anything exported to the environment should be capitalized,\
  separated with underscores, and declared at the top of the file.

- For the sake of clarity readonly or export is recommended\
  vs. the equivalent declare commands.

- Aliases should be avoided in scripts. Use functions instead.

- Always use `(( … ))` or `$(( … ))` rather than `let` or `$[ … ]` or `expr`.

- Use process substitution or the readarray builtin (bash4+) \
  in preference to piping to while.\
  Pipes create a subshell, so any variables modified within a pipeline\
  do not propagate to the parent shell.

- Function names should be lower-case, with underscores to separate words.\
  Separate libraries with `::`.\
  Parentheses are required after the function name.\
  The keyword function is optional, but must be used consistently throughout a project.

- Avoid modifying the values of the following bash variables in a script:\
  `BASH_VERSION`,`BASH_VERSINFO`,`EUID`, `UID`, `PPID`, `PID`, `PGID`, `SID`,\
  `SHLVL`, `RANDOM` ,`LINENO`, `FUNCNAME`, `BASH_SOURCE`, `BASH_LINENO`, `OSTYPE`,\
  `MACHTYPE`, `HOSTTYPE`,`HOSTNAME`,`LINES`, `COLUMNS`,`GROUPS`,`HOME`,`OPTARG`\
  ,`OPTIND`,`REPLY`, `SECONDS`,`TERM`, `DEBUG`.

- It's worth noting that Bash variables starting with an underscore \
  (e.g., `_`, `__`, etc.) are generally reserved for internal use and should not\
  be modified.

- When you have a function `main` in the script, wrap the call to `main` as below:

  ```bash
   if [[ "${BASH_SOURCE[0]}" == "${0}" ]];then
     main "$@"
    fi
  ```

- Every script must have a debug mode that can be enabled from the command line. It can be as simple as `set -x`.

- For a script that uses complex logic, it is a good practice to provide a dry-run \
  mode with the option enabled from the command line.

- Complex scripts must provide a verbose mode enabled using -v and --verbose from the command line. When verbose mode \
  is enabled, the script must provide detailed information about its execution, including any errors or warnings.

- When sourcing a file (say `include.sh`) for a script that may be executed from anywhere, use the following template:

  ```bash
  SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
  source "$SCRIPT_DIR/include.sh"
  ```

- A script's usage must also include examples.
