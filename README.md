
# README

For this assignment, I made two programs using GNU Coreutils 9.7. I successfully compiled GNU Coreutils 9.7 on macOS using configure and make. The assignment referenced build_on_mac.sh, but that script was not included in the source links. I verified the build by running both modified programs and confirming their output.

**ls.c:** I changed the default format in `decode_switches()` so that running `ls` without options displays a long-format listing, including permissions, ownership, file sizes, and dates.

**whoami.c:** I also changed the username output to display `You are: ` before the username.

Building/ Testing

I built Coreutils 9.7 on macOS using the terminal and the macOS 26.5 SDK.

I tested my modifications with: - `./build/src/ls` and - `./build/src/whoami`

Both programs worked, and the `ls` command displayed a proper detailed listing, and `whoami` displayed `You are: a3`.

Challenges and Reflection

One big challenge was getting Coreutils to compile on my Mac. I encountered an SDK linker error and a missing `parse-datetime.c` file. After troubleshooting these issues, I was able to complete the build. This assignment helped me understand how changes to C source code can affect the command-line programs, as well as how to use Git to track my modifications.


The AI Challenge: Codebase Architecture & Patch Diff Analysis

Codebase Exploration with AI

For this assignment, I used ChatGPT to help me understand the GNU Coreutils 9.7 codebase, as locate the sections I needed to modify. Since the files contain a lot of C code, AI helped me identify the formatting logic in ls.c and the username retrieval code in whoami.c. I also used AI to understand how these functions work before making my changes.

Implementing the C Changes

In ls.c, I modified the decode_switches() function so that ls displays the long format by default (without the -1). This means it automatically shows file permissions, ownership, sizes, and dates. In whoami.c, I changed the output so that it prints You are:  followed by the current username. I successfully compiled and tested both programs on my Mac. The results showed that both modifications worked.

AI Patch Audit 

I used ChatGPT to help review my changes and used git diff to compare my modified files with the original Coreutils source code.

Changes in ls.c:
-: ls_mode == LS_LS ? (stdout_isatty ()
-many_per_line : one_per_line)
-: ls_mode == LS_LS ? long_format

This modification changes the default display format without removing the program’s ability to process command-line options.
The changes I made in whoami.c:
-puts (pw->pw_name);
-printf ("You are: %s\n", pw->pw_name);
This will change only how the username is displaye!

Understanding Command-Line Flags and Default Initialization

The ls.c program uses getopt_long() to process command-line options such as -l and -1. When one option is provided, the program uses that option to determine how the files should be displayed. I also changed the default to long_format, so that the detailed listing will appear automatically. Additionally, the explicit formatting options can still override the default.

The whoami.c program uses geteuid() to retrieve the effective user ID of the person running the program. After, it then getpwuid() to look up the username associated with that ID. The username is stored in pw->pw_name. In order to fix this, I modified the output statement to include You are:  before the username, that way it will work without changing how the program gets the information.

GNU C Coding Standards Audit:

After reviewing the changes, I found that the modifications were small, and focused on the requirements. The whoami.c modification follows the existing GNU C formatting style. The ls.c modification works correctly, although its indentation could be improved to better match the original GNU C style. The changes do not require modifying the existing POSIX user lookup functions or removing command-line option handling.

Challenges and Reflection
One of the biggest challenges I experiencing was compiling GNU Coreutils on my Mac. I also dealt with a SDK linker error and a missing parse-datetime.c file. I used ChatGPT to help troubleshoot the errors in order to find solutions for my code since I wasn't feeling too good about it. I also learned that making changes to a large code requires understanding how different parts of the program work together. The chatgpt had a hard time making the code without splitting it up part by part. I also learned how to use Git to compare source-code changes and how important it is to test the actual program instead of assuming the code works. Using the AI was helpful in order to understanding errors and finding relevant sections of code, but I still needed to check the results myself to make sure the modifications worked correctly.
