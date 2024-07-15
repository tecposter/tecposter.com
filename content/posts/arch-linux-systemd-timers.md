---
date: 2024-07-13T13:27:54.266693677Z
title: Arch Linux systemd timers
---

Timers have built-in support for calendar time events, monotonic time
events, and can be run asynchronously.

Timers are systemd unit files with a suffix of `.timer`{.verbatim}. For
each `.timer`{.verbatim} file, a matching `.service`{.verbatim} file
exists (e.g. `foo.timer`{.verbatim} and `foo.service`{.verbatim}). The
`.timer`{.verbatim} file activates and controls the
`.service`{.verbatim} file.

# As a cron replacement

Timers can be used as an alternative to cron. Here is going to present
an example to show how to create a daily running job.

First create a service unit under the user\'s control.

``` shell
systemctl edit --user --force --full xxx-git-update.service
```

-   `--user`{.verbatim}, Talk to the service manager of the calling
    user, rather than the service manager of the system.
-   `--force`{.verbatim}, create all of the specified units which do not
    already exist.
-   `--full`{.verbatim}, copy the original units instead of creating
    drop-in files.

After executing the command above, an editor, could be vim, will be
poped up. Then type in the service unit content according. Here is a
simple example showing below.

``` shell
[Unit]
Description=xxx git update

[Service]
Type=simple
ExecStart=/home/xxx/space/bin/xxx-git-update.sh
```

Save and quit the editor, the new service unit file will be saved under
`~/.config/systemd/user/`{.verbatim}. At this point, we can start the
new created service and check the running result by the following
commands.

``` shell
❯ systemctl start --user xxx-git-update.service

❯ systemctl status --user xxx-git-update.service
○ xxx-git-update.service - xxx git update
     Loaded: loaded (/home/xxx/.config/systemd/user/xxx-git-update.service; static)
     Active: inactive (dead) since Sun 2022-07-17 18:04:52 CST; 10s ago
   Duration: 2.731s
TriggeredBy: ● xxx-git-update.timer
    Process: 126107 ExecStart=/home/xxx/space/bin/xxx-git-update.sh (code=exited, status=0/SUCCESS)
   Main PID: 126107 (code=exited, status=0/SUCCESS)
        CPU: 120ms

Jul 17 18:04:51 arch xxx-git-update.sh[126107]: + [[ -n  M xx/xxxxx-xx-x-x-x-x-x.org ]]
Jul 17 18:04:51 arch xxx-git-update.sh[126107]: + git add .
Jul 17 18:04:51 arch xxx-git-update.sh[126138]: ++ date
Jul 17 18:04:51 arch xxx-git-update.sh[126107]: + git commit -m 'auto update - Sun Jul 17 06:04:51 PM CST 2022'
Jul 17 18:04:51 arch xxx-git-update.sh[126139]: [master aa3292a] auto update - Sun Jul 17 06:04:51 PM CST 2022
Jul 17 18:04:51 arch xxx-git-update.sh[126139]:  1 file changed, 30 insertions(+)
Jul 17 18:04:51 arch xxx-git-update.sh[126107]: + git push
Jul 17 18:04:52 arch xxx-git-update.sh[126141]: remote: Powered by GITEE.COM [GNK-6.3]
Jul 17 18:04:52 arch xxx-git-update.sh[126141]: To gitxx.com:xxx/xxx.git
Jul 17 18:04:52 arch xxx-git-update.sh[126141]:    c2da5c9..aa3292a  master -> master
```

Create a corresponding timer unit with the similar command as creating
service unit.

``` shell
❯ systemctl edit --user --force --full xxx-git-update.timer
```

And here is a example of timer that starts daily (at 00:00:00 everyday).

``` shell
[Unit]
Description=Daily run xxx git update

[Timer]
OnCalendar=daily
Persistent=true
```

At last start the timer and check its status.

``` shell
❯ systemctl start --user xxx-git-update.timer

❯ systemctl status --user xxx-git-update.timer
● xxx-git-update.timer - Daily run xxx git update
     Loaded: loaded (/home/xxx/.config/systemd/user/xxx-git-update.timer; static)
     Active: active (waiting) since Sun 2022-07-17 18:01:43 CST; 11s ago
      Until: Sun 2022-07-17 18:01:43 CST; 11s ago
    Trigger: Mon 2022-07-18 00:00:00 CST; 5h 58min left
   Triggers: ● xxx-git-update.service

Jul 17 18:01:43 arch systemd[687]: Started Daily run xxx git update.
```

# Ref

-   <https://wiki.archlinux.org/title/Systemd/Timers>
-   <https://opensource.com/article/20/7/systemd-timers>
-   <https://linuxconfig.org/how-to-schedule-tasks-with-systemd-timers-in-linux>
