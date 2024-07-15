---
date: 2024-07-13T13:32:13.266694019Z
title: Emacs
---

# Dired

-   `C-x d`{.verbatim} dired
-   `C-x 4 d`{.verbatim} dired-other-window
-   `C-x 5 d`{.verbatim} dired-other-frame
-   `C-x C-d`{.verbatim} list-directory
-   `C-x C-f`{.verbatim} find-file
-   `C-x 4 C-f`{.verbatim} find-file-other-window
-   `C-x 5 C-f`{.verbatim} find-file-other-frame

wildcard

``` shell
C-x d ~/**/*/*.el RET
```

Deleteing files with Dired

-   `d`{.verbatim} Flag this file for deletion
    (dired-flag-file-deletion).
-   `u`{.verbatim} Remove the deletion flag (dired-unmark).
-   `DEL`{.verbatim} Move point to previous line and remove the deletion
    flag on that line (dired-unmark-backward).
-   `x`{.verbatim} Delete files flagged for deletion
    (dired-do-flagged-delete).

``` shell
C-x d # open dired
d # flag files for deletion
u # remove the deletion flag
x # delete flagged files
yes # confirm
no # cacel
C-g # quit
```

Visiting Files in Dired

-   `RET`{.verbatim} Visit the file described on the current line, like
    typing C-x C-f and supplying that file name (dired-find-file)
-   `SHIFT+RET`{.verbatim} uses another window to display the file's
    buffer (dired-find-file-other-window)
-   `g o`{.verbatim} View the file described on the current line, with
    View mode (dired-view-file)
-   `g O`{.verbatim} dired-find-file-other-window
-   `^`{.verbatim} Visit the parent directory of the current directory
    (dired-up-directory). This is equivalent to moving to the line for
    .. and typing f there.

Mark

-   `m`{.verbatim} dired-mark
-   `u`{.verbatim} Remove any mark on this line (dired-unmark)
-   `U`{.verbatim} Remove all marks from all the files in this Dired
    buffer (dired-unmark-all-marks).

Operating on **marked** Files

-   `C`{.verbatim} Copy the specified files (dired-do-copy)
-   `D`{.verbatim} Delete the specified files (dired-do-delete). This is
    like the shell command rm.
-   `R`{.verbatim} Rename the specified files (dired-do-rename)
-   `H`{.verbatim} Make hard links to the specified files
    (dired-do-hardlink).This is like the shell command ln.
-   `S`{.verbatim} Make symbolic links to the specified files
    (dired-do-symlink). This is like 'ln -s'
-   `M`{.verbatim} Change the mode (also called permission bits) of the
    specified files (dired-do-chmod).
-   `G`{.verbatim} Change the group of the specified files to newgroup
    (dired-do-chgrp).
-   `O`{.verbatim} Change the mode (also called permission bits) of the
    specified files (dired-do-chmod).

read more:
<https://www.gnu.org/software/emacs/manual/html_node/emacs/Operating-on-Files.html>

Subdirectories in Dired

-   `I`{.verbatim} Insert the contents of a subdirectory later in the
    buffer. (dired-maybe-insert-subdir)
-   `r`{.verbatim} update the subdirectory's contents
    (dired-do-redisplay)
-   `g $`{.verbatim} Hide or show the subdirectory that point is in, and
    move point to the next subdirectory (dired-hide-subdir).
-   `M-$`{.verbatim} Hide all subdirectories in this Dired buffer,
    leaving only their header lines (dired-hide-all).
-   (dired-hide-details-mode)

# Bindings

-   help
    -   `C-h b`{.verbatim} describe-bindings
    -   `C-h C-h`{.verbatim}, view a list of all help features
-   `C-x 0`{.verbatim} delete-window
-   `C-x 1`{.verbatim} delete-other-windows
-   `C-n`{.verbatim} completion-at-point
-   `C-g`{.verbatim} evil-normal-state
-   `C-;`{.verbatim} embark-ack
    -   `E`{.verbatim} embark-export
-   Vertico (Vertical completion UI)
    -   `C-j`{.verbatim} vertico-next
    -   `C-k`{.verbatim} vertico-previous
    -   `C-f`{.verbatim} vertico-exit
    -   `C-<return>`{.verbatim} vertico-exit-input
    -   `TAB`{.verbatim} vertico-insert
-   corfu (completion-in-region)
    -   `C-j`{.verbatim} corfu-next
    -   `C-n`{.verbatim} corfu-next
    -   `C-k`{.verbatim} corfu-previous
    -   `C-p`{.verbatim} corfu-previous
-   consult
    -   `C-s`{.verbatim} consult-line
    -   `C-M-s`{.verbatim} consult-repgrep
    -   `C-x b`{.verbatim} consult-buffer
    -   `C-M-l`{.verbatim} consult-imenu
    -   `C-r`{.verbatim} consult-history
    -   `C-r`{.verbatim} consult-history (minibuffer-local-map)
-   org
    -   `C-c C-v t`{.verbatim} org-babel-tangle
    -   `C-c o l`{.verbatim} org-toggle-link-display
    -   `C-k`{.verbatim} outline-backward-same-level
    -   `C-j`{.verbatim} outline-forward-same-level
-   org-roam
    -   `C-c n l`{.verbatim} org-roam-buffer-toggle
    -   `C-c n f`{.verbatim} org-roam-node-find
    -   `C-c n i`{.verbatim} org-roam-node-insert
-   projectil
    -   `C-c p`{.verbatim} projectile-command-map
        -   `C-c p p`{.verbatim} projectile-switch-project
        -   `C-c p f`{.verbatim} projectile-find-file
    -   `C-S-s`{.verbatim} projectile-ripgrep
-   eglot
    -   `C-t`{.verbatim} xref-pop-marker-stack
    -   `C-h .`{.verbatim} eldoc-doc-buffer
    -   `K`{.verbatim} eldoc-doc-buffer
    -   `g`{.verbatim} Prefix Command
        -   `g d`{.verbatim} xref-find-definitions
        -   `g r`{.verbatim} xref-find-references

# LaTex

Install `texlive-most`{.verbatim}

``` shell
sudo pamman -S texlive-most
```

## Bindings

-   `C-c C-s C-l`{.verbatim} org-latex-preview

## Examples

Display inline `$E=mc^2$`{.verbatim}: $E=mc^2$

    #+BEGIN_LaTeX latex
    \begin{aligned}
    T_{1}(n) & = 2T_{1}(\frac{n}{2}) + c_{1}n \\
    & = 2[2T_{1}(\frac{n}{2^{2}}) + c_{1}\frac{n}{2}] + c_{1}n \\
    & = 2^{2}T_{1}(\frac{n}{2^{2}}) + 2c_{1}n \\
    & ...\\
    & = 2^{L}T_{1}(\frac{n}{2^{L}}) + Lc_{1}n \\
    n & = 2^{L} \\
    L & = log_{2}n \\
    => T_{1}(n) & = nT_{1}(1) + c_{1}.n.log_{2}{n} \\
    T_{1}(n) & = \Omega(nlog_{2}{n}) \\
    \end{aligned}
    #+END_LaTex latex

```latex
\begin{aligned}
T_{1}(n) & = 2T_{1}(\frac{n}{2}) + c_{1}n \\
& = 2[2T_{1}(\frac{n}{2^{2}}) + c_{1}\frac{n}{2}] + c_{1}n \\
& = 2^{2}T_{1}(\frac{n}{2^{2}}) + 2c_{1}n \\
& ...\\
& = 2^{L}T_{1}(\frac{n}{2^{L}}) + Lc_{1}n \\
n & = 2^{L} \\
L & = log_{2}n \\
=> T_{1}(n) & = nT_{1}(1) + c_{1}.n.log_{2}{n} \\
T_{1}(n) & = \Omega(nlog_{2}{n}) \\
\end{aligned}
```
latex

## greek

  ---------- --------------------- ---------- --------------------- ---------- --------------------- ---------- ---------------------
  $\alpha$   `\alpha`{.verbatim}   $\beta$    `\beta`{.verbatim}    $\gamma$   `\gamma`{.verbatim}   $\delta$   `\delta`{.verbatim}
  $\theta$   `\theta`{.verbatim}   $\Theta$   `\Theta`{.verbatim}   $\omega$   `\omega`{.verbatim}   $\Omega$   `\Omega`{.verbatim}
  ---------- --------------------- ---------- --------------------- ---------- --------------------- ---------- ---------------------

ref

-   <https://wiki.archlinux.org/title/TeX_Live#Installation>
-   <https://stackoverflow.com/questions/17239273/org-mode-buffer-latex-syntax-highlighting>

# Table

-   `C-c |`{.verbatim} convert selected region to table

ref

-   <https://orgmode.org/worg/org-tutorials/tables.html>

# How-to

## How to create a directory?

<https://stackoverflow.com/questions/155507/how-do-i-create-a-directory-from-within-emacs>

`M-x make-directory`{.verbatim}

``` shell
M-x make-directory RET dir/to/create RET
```

`M-! mkdir`{.verbatim} call shell command `mkdir`{.verbatim}

``` shell
M-! mkdir -p dir/parent{1,2}/node RET
```

`Dired`{.verbatim} mode, press `+`{.verbatim}

``` shell
+
```

`C-x C-f - find-file`{.verbatim}

``` shell
C-x C-f dir/to/create/with-file
M-x make-directory RET RET # Create directory 'dir/to/create' if not exists
```

## How to quickly copy/move files

-   <https://emacs.stackexchange.com/questions/5603/how-to-quickly-copy-move-file-in-emacs-dired>
-   <http://pragmaticemacs.com/emacs/copy-and-paste-files-with-dired-ranger/>

## How to open files externally in Emacs dired mode?

<https://stackoverflow.com/questions/6845005/how-can-i-open-files-externally-in-emacs-dired-mode>
Yes. Use `!`{.verbatim} while in dired to run a shell command on a file.

``` shell
:!
```

# BookMarks

<https://www.emacswiki.org/emacs/BookMarks>

-   'C-x r m' -- set a bookmark at the current location (e.g. in a file)
-   'C-x r b' -- jump to a bookmark
-   'C-x r l' -- list your bookmarks
-   'M-x bookmark-delete' -- delete a bookmark by name

# EmacsClient

-   <https://www.emacswiki.org/emacs/EmacsClient>
-   <https://www.youtube.com/watch?v=ZjCRxAMPdNc>

``` shell
emacsclient --create-frame --alternate-editor=""
```

The emacsclient program will connect to a running instance of Emacs if
it exists. -c tells Emacs to open the file in a new frame, which is
optional. Specifying -a="" tells emacsclient to start an instance of
emacs if it cannot find one already running.

# Ref

-   tutorial
    -   [A Guided Tour of
        Emacs](https://www.gnu.org/software/emacs/tour/)
    -   [Emacs From Scratch -
        YouTube](https://youtube.com/playlist?list=PLEoMzSkcN8oPH1au7H6B7bBJ4ZO7BXjSZ)
-   [Editing Source Code](id:0144EE42-58F2-4EEF-B2E3-217D2E37307C)
-   [How to copy and paste in
    minibuffer?](id:849F8798-5CF5-48B0-93A7-3F5F7654D531)
-   [System Crafters Live! - Replacing Ivy and Counsel with Vertico and
    Consult](https://www.youtube.com/watch?v=UtqE-lR2HCA)
-   [How can I use Emacs and iTerm for the same
    keybinding?](https://emacs.stackexchange.com/questions/56128/how-can-i-use-emacs-and-iterm-for-the-same-keybinding)
-   [Dynamic
    Abbreviations](https://www.emacswiki.org/emacs/DynamicAbbreviations)
-   [Awesome Emacs](https://github.com/emacs-tw/awesome-emacs)
-   [Replacing Ivy and Counsel with Vertico and
    Consult](https://www.youtube.com/watch?v=UtqE-lR2HCA)
-   [Emacs Episode 80 - Vertico, Marginalia, Consult, and
    Embark](https://www.youtube.com/watch?v=5ffb2at2d7w&t=1s)
-   [minad/vertico](https://github.com/minad/vertico)
-   [minad/consult](https://github.com/minad/consult)
-   <https://github.com/daviwil/emacs-from-scratch/blob/master/init.el>
-   packages
    -   <https://github.com/raxod502/straight.el>
-   org babel to support rust
    -   ob-rust
    -   Babel languages Support:
        <https://orgmode.org/worg/org-contrib/babel/languages/index.html>
    -   <https://github.com/brotzeit/rustic>
    -   <https://www.reddit.com/r/emacs/comments/988lig/obrust_org_babel_support_for_rust/>
