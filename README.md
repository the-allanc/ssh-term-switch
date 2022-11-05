ssh-term-switch
===============

Script to help change Tilix profile when SSH'ing.

I liked [this script](https://github.com/fboender/sshbg) which intelligently determines whether you're starting a new SSH session, and to which host, and to colour the background based on the hostname.

And I liked [this guide](https://deeb.me/20190116/change-profiles-automatically-in-tilix-when-connecting-to-ssh-hosts) to alias the `ssh` command to help trigger a Tilix profile.

And I liked [this script](https://stackoverflow.com/a/30540928) which shows a convoluted way to get the current background color of a terminal.

So I combined them - you can use this script to help automatically set a Tilix profile, and use the standard SSH config file to help set the name. It can also set the background color too for other terminals, and try to restore the original color once it's complete.

## Installation and usage

Requirements:

* Python v3.x+
* Tilix (optionally)

Clone this repo:

    git clone git@github.com:the-allanc/ssh-term-switch.git
    cd ssh-tilix-profile-switch

Copy the `ssh-term-switch` script to some dir in your PATH, for example:

    sudo cp ssh-term-switch /usr/local/bin/

Enable the `LocalCommand` configuration setting in your SSH config. You can do
this on a host-by-host basis, or with wildcards. To enable it for all hosts,
make your SSH config look like this:

    $ cat ~/.ssh/config
    PermitLocalCommand yes
    Host *
        LocalCommand ssh-tilix-profile-switch "%n"

This will tell the script (which will inform Tilix) that you have connected to the hostname given.

You can also pass an "alias" argument to the command, which makes it easier to group
a set of hosts under a single profile. For example:

    $ cat ~/.ssh/config
    PermitLocalCommand yes
    Host *.production
        LocalCommand ssh-tilix-profile-switch "%n" "--alias=prod"
    Host devbox devbox2
        LocalCommand ssh-tilix-profile-switch "%n"
    Host docker*
        LocalCommand ssh-tilix-profile-switch "%n" "--alias=docker"
    Host *
        LocalCommand ssh-tilix-profile-switch "%n" "--alias=default"

Then when setting up the automatic profile switching in Tilix (as described [here](https://deeb.me/20190116/change-profiles-automatically-in-tilix-when-connecting-to-ssh-hosts)), you can use the aliases instead of the hostname (in the case of the example, you can use "prod", "docker" or "default").

## Setting background color for other terminals

You can also use the script to set the background color for other terminals - while this feature works for other terminals, it doesn't work currently with Tilix (as of v1.9.1).

To choose a background color to use, you can pass the RGB value to set the background to like this:

    $ cat ~/.ssh/config
    PermitLocalCommand yes
    Host *
        LocalCommand ssh-tilix-profile-switch "%n" "--remotebg=#225588"

Once the SSH connection is finished, the script will try to restore the background color to what it previously (if it can detect it), but you can override the color to restore the terminal to like this:

    $ cat ~/.ssh/config
    PermitLocalCommand yes
    Host *
        LocalCommand ssh-tilix-profile-switch "%n" "--remotebg=#2266aa" "--localbg=#222222"

The command will still set the hostname to allow Tilix to do profile switching, so defining something like this will still work for both Tilix and other terminals:

    $ cat ~/.ssh/config
    PermitLocalCommand yes
    Host *
        LocalCommand ssh-tilix-profile-switch "%n" "--alias=prod" "--remotebg=#2266aa" "--localbg=#222222"

## Remarks, weirdness and bugs.

* The hostname is the one you specify on the commandline, **NOT** necessarily
  the real remote hostname.
* Manually chained SSH (`ssh machine_a -> ssh machine_b`) will not work.
  Automatically chained SSH (through `ProxyCommand`) *will* work.
* Changing colors will not necessarily work for all terminals - such as Byobu.

## License

MIT license. See `LICENSE` file.
