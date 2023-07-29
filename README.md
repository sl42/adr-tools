ADR Tools
=========

A command-line tool for working with a log of [Architecture Decision Records][ADRs] (ADRs).

[![Build Status](https://travis-ci.org/npryce/adr-tools.svg?branch=master)](https://travis-ci.org/npryce/adr-tools)

Quick Start
-----------

[Install ADR Tools](INSTALL.md).

Use the `adr` command to manage ADRs.  Try running `adr help`.

ADRs are stored in a subdirectory of your project as Markdown files. 
The default directory is `doc/adr`, but you can specify the directory
when you initialise the ADR log.

1. Create an ADR directory in the root of your project:

        adr init doc/architecture/decisions

    This will create a directory named `doc/architecture/decisions` 
    containing the first ADR, which records that you are using ADRs
    to record architectural decisions and links to 
    [Michael Nygard's article on the subject][ADRs].

2. Create Architecture Decision Records

        adr new Implement as Unix shell scripts

    This will create a new, numbered ADR file and open it in your
    editor of choice (as specified by the VISUAL or EDITOR environment
    variable).

    To create a new ADR that supercedes a previous one (ADR 9, for example),
    use the -s option.

        adr new -s 9 Use Rust for performance-critical functionality

    This will create a new ADR file that is flagged as superceding
    ADR 9, and changes the status of ADR 9 to indicate that it is
    superceded by the new ADR.  It then opens the new ADR in your
    editor of choice.
    
3. For further information, use the built in help:

        adr help


See the [tests](tests/) for detailed examples.

The decisions for this tool are recorded as [architecture decision records in the project repository](doc/adr/). 

[ADRs]: http://thinkrelevance.com/blog/2011/11/15/documenting-architecture-decisions

# Some Bash Commands To Generate A Complete HTML Tree 

The following commands here are a copy & paste solution to generate a 
navigable set of HTML files out of the markdown files (in case no appropriate
markdown viewer is available. To achieve that the following tools are 
necessary:

- '`dot`'     (https://graphviz.org)
- '`pandoc`'  (https://pandoc.org)
- a lua filter to convert markdown links into HTML links 
  (see https://stackoverflow.com/questions/40993488/convert-markdown-links-to-html-with-pandoc, 
  store this script within '`/c/apps/pandoc/md-links-to-html.lua`' for example)

Under windows the following parameters have to be set within a bash instance
(all required binaries are supposed to be located within '`c:\apps`'):

```
PATH=$PATH:/c/apps/adr-tools/src:/c/apps/pandoc:/c/apps/graphviz/bin
export EDITOR=notepad++.exe
export PAGER='less -I -n '
export DECISION_DIR=`pwd`/architecture/decisions
```
Then the following commands will generate a complete HMTL ADR document tree
including a SVG graph overview and a table of contents HTML page. 
Please mind that you have to go to the directory in which the 
'`architecture`' directory exists for which the documentation shall be 
created:

```
find $DECISION_DIR/. -iname "*.orig" -ls -delete
adr generate toc                   > $DECISION_DIR/toc.md
adr generate graph | dot.exe -Tsvg > $DECISION_DIR/graph.svg
for curr_file in `find $DECISION_DIR/. -iname '*.md'` ; do pandoc.exe --toc --lua-filter=/c/apps/pandoc/md-links-to-html.lua -f markdown+autolink_bare_uris -t html "Scurr_file" -o "$DECISION_DIR/$(basename Scurr_file '.md').html" ; done
```

