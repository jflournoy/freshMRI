freshMRI: README.md
==========

There are some truly excellent neuroimaging tools that are widely available and easy to use. Many neuroimagers are not (yet) computer scientists, however, and installing different software packages can be a bit of a challenge. This guide is intended to make it a little bit easier to set up a Mac for neuroimaging data analysis.

The guide is broken up into several parts:
+ README.md (freshMRI_000): introduction, getting to the command line
+ freshMRI_001.md: system tools
+ freshMRI_002.md: scientific python libraries
+ freshMRI_003.md: neuroimaging software

----------
  If you are a whiz-bang programmer, you can probably skip the rest of this part (unless you want to check it out and offer some helpful suggestions!), but if not familiar with using the command line, this is a good time to get that way. For the completely CLI-naive, here's a nice quick and dirty intro to the CLI from [Lifehacker](http://lifehacker.com/5633909/who-needs-a-mouse-learn-to-use-the-command-line-for-almost-anything). To go a little more in depth, FSL hosts a [Basic Unix Tutorial](http://fsl.fmrib.ox.ac.uk/fslcourse/unix_intro/) that gets advanced pretty quickly. Here are some of the bare bones:

There are plenty of alternatives to Terminal.app, the command line interface (CLI) that comes standard with OS X, but unless you have a strong alternate preference already, this one is a great application.
![/Applications/Utilities/Terminal](https://github.com/wem3/freshMRI/raw/master/images/Terminal_Finder.png "Navigate to /Applications/Utilities/Terminal in Finder")

You can change the appearance of the Terminal window in Preferences-> Settings (I like 'Homebrew' or 'Pro'). It's good idea to keep the Terminal app in your dock or somewhere else that's easy to access, as we'll be using it a lot.

In your Terminal window, there's a prompt on the left-most side of the screen that says something like:
```bash
RickyMcMRI's-MacBook-Pro:~ricky$
```
(the specifics aren't important).

Often times, code blocks use a '$' or '>' to indicate the prompt, but if you try to copy/paste
```bash
$ echo 'freshMRI is totally blowing my mind'
```
into the terminal, you'll get something like
```bash
-bash: $: command not found
```
Instead, try
```bash
echo 'freshMRI is totally blowing my mind'
```
Most of the rest of the instructions in this tutorial will be paste-able directly into your command window. Whenever you're ready, head on to freshMRI_001.md.
