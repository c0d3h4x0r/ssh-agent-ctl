# ssh-agent-ctl
`bash` script wrapper around Linux `ssh-agent` to easily enable auto-starting a single instance per user.

You can easily add this script as a `systemd` user service, to a login/profile script, or to your shell's rc script.

# Support

ZERO SUPPORT, WARRANTIES, OR GUARANTEES OF ANY KIND (other than the applicable GPL license) ARE PROVIDED BY THE DEVELOPER.

You use this software entirely at your own risk, and the issue tracker is intentionally closed. If you want anything about this software changed or fixed, create your own fork and fix it yourself. You are welcome to send a pull request if you want to be a particularly good Samaritan, but I make no guarantees that I'll ever merge it or even comment on it.

# Prerequisites

1. A Linux system (with desktop environment or not).
1. `bash` already installed, working, and set as your default/preferred shell.
1. `ssh-agent` already installed and working.
1. A burning desire to have `ssh-agent` automatically start a single instance per-user the first time each user logs into the system.

# Installation

1. Open a shell as `root`, depending upon your system type:
    - (for non-`systemd`-based Linux): `sudo -i`
    - (for `systemd`-based Linux): `sudo machinectl shell root@`
1. `cd /usr/local/src`
1. `git clone https://github.com/c0d3h4x0r/ssh-agent-ctl.git`
1. `cd ssh-agent-ctl`
1. `cp ./usr/local/sbin/ssh-agent-ctl /usr/local/sbin/`
1. `cp ./usr/lib/systemd/user/ssh-agent.service /usr/lib/systemd/user/`
1. `cp ./etc/profile.d/ssh-agent.sh /etc/profile.d/ssh-agent.sh`
1. `chown root:root /usr/local/sbin/ssh-agent-ctl`
1. `chmod ugo+rx /usr/local/sbin/ssh-agent-ctl`
1. Manually add the following lines to the bottom of `/etc/bash.bashrc`:

    ```
    ssh-agent-ctl start-bg /etc/bash.bashrc
    eval "$(ssh-agent-ctl envsrc)"
    ssh-agent-ctl addkeys /etc/bash.bashrc
    ```
1. (only for `systemd`-based Linux): `cp ./usr/lib/systemd/user/ssh-agent.service /usr/lib/systemd/user/`
1. (only for `systemd`-based Linux): `chown root:root /usr/lib/systemd/user/ssh-agent.service`
1. (only for `systemd`-based Linux): `systemctl --user daemon-reload`
1. (only for `systemd`-based Linux): `systemctl --user enable --now ssh-agent.service`
1. Manually place any private keys you wish to have `ssh-agent-ctl` auto-add to the agent (via `ssh-add`) into a new personal `~/.ssh/private_keys` folder.
1. `reboot` to restart Linux.

# Usage

However you login to the system (via text console or desktop environment), `ssh-agent-ctl` will be called and will auto-start a single instance of `ssh-agent` for your local user account.

The first time you open a `bash` shell, `ssh-agent-ctl` will be called, will detect it is running inside a terminal with `stdin` attached, and will then auto-add any private keys sitting in `~/.ssh/private_keys`, which (for any key with a passphrase) stop and ask you to type in the passphrase. Once you've done this, your instance of `ssh-agent` will remain running (and will hold the added keys resident in memory) until the last logged-in instance of your user account logs out of the system.
