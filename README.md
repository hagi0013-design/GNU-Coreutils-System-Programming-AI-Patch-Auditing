READMD 

Problem Statement

The goal of this assignment was to make changes to two programs in GNU Coreutils 9.7, which is a collection of command-line tools. I needed to modify `ls.c` so that the `ls` command shows a detailed listing by default. I also needed to modify `whoami.c` so that it displays `You are:` before the username.

To complete this assignment, I had to look through the C source code, find the sections that controlled the output, make the changes, rebuild the programs, and test them to make sure they worked.

Solution Overview

I used ChatGPT to help me understand the Coreutils code and find the parts I needed to change. Since the source files contain a lot of code, I needed to focus on the specific functions instead of trying to understand everything at once. For `ls.c`, I changed the default format so that it displays a detailed listing without needing to type `-l`. In `whoami.c`, I changed the output statement so that it prints `You are:` as well as the username.

Both programs worked when I tested them. The `ls` command displayed a detailed listing, and `whoami` displayed `You are: a3`.

I first 
-Opened the GNU Coreutils 9.7 source code.
-Found the formatting code in `ls.c`.
-Changed the default format to `long_format`.
-Found the username output statement in `whoami.c`.
-Changed the output message.
-Tested the programs to check the results.
-Used Git to compare my changes with the original code.

## 2. C System Mechanics & Diff Analysis

The main change in `ls.c` was inside the `decode_switches()` function. The function helps to determine which options the user selected as well as and how the files should be displayed.

Some of the important variables and values I learned about were:

- `format`: Determines how the files are displayed.
- `format_opt`: Keeps track of whether the user selected a specific format.
- `ls_mode`: Identifies which mode the program is running in.
- `long_format`: Selects the detailed listing format.
- `many_per_line` and `one_per_line`: Control other ways of displaying filenames.

**Original code:**

##format = (0 <= format_opt ? format_opt
          : ls_mode == LS_LS ? (stdout_isatty ()
                                ? many_per_line : one_per_line)
          : ls_mode == LS_MULTI_COL ? many_per_line
          : /* ls_mode == LS_LONG_FORMAT */ long_format);
```

**New code**

##format = (0 <= format_opt ? format_opt
          : ls_mode == LS_LS ? long_format
          : ls_mode == LS_MULTI_COL ? many_per_line
          : /* ls_mode == LS_LONG_FORMAT */ long_format);
```

The main difference is that I replaced the original terminal-based format selection with `long_format` for the regular `ls` mode. This means that when I run `ls` without any options, it displays more information about the files, including permissions, ownership, file size, and modification time. I also learned that the program checks `format_opt` first. This is important because it means the user can still select a different format using command-line options. For example, `ls -1` can still display one filename per line, and `ls -C` can still display filenames in columns.


### 2.2 Changes in `whoami.c`

The `whoami` program gets information about the current effective user and displays the username. The function `geteuid()` gets the effective user ID. The function `getpwuid()` uses that ID to look up the user's information. The username is then available through `pw->pw_name`.

The original output statement was:

```c
puts (pw->pw_name);
```

I changed it to:

```c
printf ("You are: %s\n", pw->pw_name);
```
My modified program displayed:

```text
You are: a3
```

This change only affects how the username is displayed. I did not change the functions that retrieve the user's information.

### 2.3 Unified Git Diff Patch

I used Git to compare my modified code with the original code. The following shows the main changes I made.


```diff
diff --git a/src/ls.c b/src/ls.c
--- a/src/ls.c
+++ b/src/ls.c
@@
-  format = (0 <= format_opt ? format_opt
-            : ls_mode == LS_LS ? (stdout_isatty ()
-                                  ? many_per_line : one_per_line)
-            : ls_mode == LS_MULTI_COL ? many_per_line
-            : /* ls_mode == LS_LONG_FORMAT */ long_format);
+  format = (0 <= format_opt ? format_opt
+            : ls_mode == LS_LS ? long_format
+            : ls_mode == LS_MULTI_COL ? many_per_line
+            : /* ls_mode == LS_LONG_FORMAT */ long_format);

diff --git a/src/whoami.c b/src/whoami.c
--- a/src/whoami.c
+++ b/src/whoami.c
@@
-  puts (pw->pw_name);
+  printf ("You are: %s\n", pw->pw_name);
```

To get the actual diff from my repository, I can run:

```bash
git diff -- src/ls.c src/whoami.c
```

This command shows the lines I changed, removed, or added. It helped me check that I was making the changes in the correct places.

## 3. Pros & Cons of the Solution

### Pros

- The `ls` command shows more information about files without needing the `-l` option.
- The `whoami` command displays a clearer message that identifies the current user.
- The changes were small and did not require rewriting the entire programs.
- The existing command-line options in `ls` can still be used.
- I gained more experience working with C code, compiling programs, and using Git.

### Cons

- Changing the default `ls` output could cause problems for scripts that expect the normal compact listing.
- People who are used to the original `ls` behavior might find the change confusing.
- Changing `whoami` to include `You are:` could affect scripts that expect only the username as output.
- Building GNU Coreutils can be difficult because it has many files and dependencies.

Overall, the changes worked for the assignment, but I learned that changing a command's default behavior can affect other users and programs that depend on it.

## 4. AI Audit Reflection

Using AI to Explore the Code

I used ChatGPT to help me understand the GNU Coreutils 9.7 code and find the sections I needed to modify. Since the files contain thousands of lines, it was difficult to start.
ChatGPT helped me locate the formatting logic in `ls.c` and the username output code in `whoami.c`. It also explained what the functions and variables did, which helped me understand why the changes were needed.

### 4.2 Prompts I Used

Some of the prompts I used during the assignment included:

- "Help me find the parts of GNU Coreutils 9.7 that I need to modify in `ls.c` and `whoami.c`."
- "Explain how the `decode_switches()` function works in `ls.c`."
- "Explain how `format_opt`, `ls_mode`, and `long_format` affect the output."
- "Help me change the `whoami` output to display `You are:` before the username."
- "Help me troubleshoot the SDK linker error and missing `parse-datetime.c` file when compiling Coreutils on my Mac."

These prompts helped me understand the code, work through problems, and review what I had changed.

Understanding Command-Line Options and Variables

One thing I learned was that the variables in `ls.c` have different purposes. The `format_opt` variable keeps track of an explicitly selected format, while `format` determines the format the program actually uses. At first, I did not fully understand how the default format was selected. ChatGPT helped me see that the program checks whether the user selected an option before choosing the default. 

I also learned that `whoami.c` separates retrieving user information from displaying it. The functions get the user's identity, while the output statement controls what appears on the screen. Because of this, I only needed to change the output statement to add the message.


I also used ChatGPT to help review the changes and used `git diff` to compare my modified files with the original source code. I checked that the `ls.c` change selected the detailed format by default and that the `whoami.c` change displayed the username with the new message. I also wanted to make sure I had not removed the existing command-line option handling or changed more code than necessary.
One thing I learned is that looking at a diff is not the same as testing the program. The diff shows what changed, but I still needed to rebuild and run the programs to make sure they worked.


I tried to keep my changes simple and consistent with the existing code. The change in `whoami.c` was straightforward because I only changed the output statement. The change in `ls.c` helped keep the existing conditional structure but changed the default format.

After making the changes, I rebuilt Coreutils and tested both programs. Both worked in my testing. The `ls` command showed a detailed listing, and `whoami` displayed `You are: a3`.

Testing the actual programs was important because I did not want to assume that the code worked just because it looked correct.

##Challenges and Reflection

One of the biggest challenges I had was getting GNU Coreutils to compile on my Mac. I ran into an SDK linker error as well a missing `parse-datetime.c` file. These problems made the process frustrating because I had to figure out whether the issue was with my code or the build setup.

I used ChatGPT to help me understand the errors and troubleshoot possible solutions. Working with such a large codebase was also difficult, so I had to look at the code in smaller sections instead of trying to understand everything at once.
This assignment helped me learn how small changes in C source code can affect how a command-line program works. I also learned how to use Git to compare my changes with the original code and why it is important to test the actual program after rebuilding it.


Overall, this assignment gave me more experience working with C programming and a real software project. I changed `ls.c` so that the command displays a detailed listing by default, and I changed `whoami.c` so that it prints `You are:` before the username.
Both programs worked when I tested them. Even though I had trouble compiling Coreutils on my Mac, I learned more about troubleshooting build errors, understanding existing code, and using Git to review changes.

The biggest thing I learned was that modifying a program involves more than changing a line of code. I also need to understand how the code works, rebuild the program, and test the results to make sure everything works as expected.
