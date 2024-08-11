---
date: 2024-07-13T13:24:19.900026738Z
title: Arch Linux Clean the System
---
In this article, we\'re going to learn how to

-   Clean package cache
-   Remove unused packages (orphans)
-   Clean the cache in our /home directory
-   Remove old config files
-   Remove duplicates, empty files, empty directories and broken
    symlinks
-   Find the largest files and directories
-   Clean Systemd journal

NOTE that we may need to [backup our linux
system](https://averagelinuxuser.com/backup-and-restore-your-linux-system-with-rsync/)
before the cleaning work.

# Clean package cache

Pacman stores all the downloaded packages in
`/var/cache/pacman/pkg/`{.verbatim} and it does not remove the old or
uninstalled versions automatically.

We can use `du`{.verbatim} to check the size of the package cache before
removing

``` shell
❯ du -sh /var/cache/pacman/pkg
23G     /var/cache/pacman/pkg
```

Remove cached packages that are not currently installed

``` shell
sudo pacman -Sc
```

Remove all the packages from the cache, including the installed ones

``` shell
sudo pacman -Scc
```

# Remove unused packages (orphans)

Find the unused packages (orphans)

``` shell
❯ sudo pacman -Qtdq
js78
```

Remove them

``` shell
❯ sudo pacman -Rns $(pacman -Qtdq)
checking dependencies...

Packages (1) js78-78.15.0-4

Total Removed Size:  62.31 MiB

:: Do you want to remove these packages? [Y/n]
```

# Clean the cache in `/home`{.verbatim} directory

First we can check the size of the cache within `/home`{.verbatim}
directory

``` shell
❯ du -sh ~/.cache
1.2G    /home/zjh/.cache
```

Then remove all the cache files

``` shell
rm -rf ~/.cache/*
```

# Remove old config files

Arch Linux stores the configuration files in `~/.config/`{.verbatim},
sometimes, may also put some old files in `~/.local/share/`{.verbatim}.

We can check the folders in there and remove the ones we don\'t need.

# Remove duplicates, empty files, empty directories and broken symlinks

We can use `rmlint`{.verbatim} to remove duplicates, empty files, empty
directories and broken symlinks

``` shell
sudo pacman -S rmlint
```

Execute `rmlint`{.verbatim} with specifying the directory we want to
check

``` shell
❯ rmlint ~
...
==> Note: Please use the saved script below for removal, not the above output.
==> In total 30320 files, whereof 4185 are duplicates in 2629 groups.
==> This equals 0.63 GB of duplicates which could be removed.
==> 519 other suspicious item(s) found, which may vary in size.
==> Scanning took in total 3.391s.


Wrote a sh file to: /home/xxx/rmlint.sh
Wrote a json file to: /home/xxx/rmlint.json

```

After executing the command above, `rmlint`{.verbatim} will generate
`rmlint.sh`{.verbatim} and `rmlint.json`{.verbatim}. We can open the
files to check which files, directories or broken symlinks will be
removed.

And then execute `rmlint.sh`{.verbatim} to perform deletion.

``` shell
sh -c rmlint.sh
```

# Find the largest files and directories

We can use `ncdu`{.verbatim} to find the largest files and directories

``` shell
sudo pacman -S ncdu
```

# Clean Systemd journal

Systemd storers logs in `/var/log/journal/`{.verbatim}.

We can keep the latest logs by limiting the size

``` shell
sudo journalctl --vacuum-size=500M # only keep 500mb of the latest logs
```

or by limiting the time

``` shell
sudo journalctl --vacuum-time=4weeks
```

We can also let the system to automatically clean the logs by setting
`SystemMaxUse`{.verbatim} in `/etc/systemd/journald.conf`{.verbatim}

``` shell
SystemMaxUse=500M
```

# Ref

-   <https://averagelinuxuser.com/clean-arch-linux/>
-   <https://linuxhint.com/remove_package_dependencies_pacman_arch_linux/>
