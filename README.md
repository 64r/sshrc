<h2>Installation</h2>

<h3>Ubuntu (12.04 and 14.04 only):</h3>
    
    $ sudo add-apt-repository ppa:russell-s-stewart/ppa
    $ sudo apt-get update
    $ sudo apt-get install sshrc


<h3>Everything else:</h3>
    $ wget https://raw.githubusercontent.com/Russell91/sshrc/master/sshrc
    $ chmod +x sshrc
    $ sudo mv sshrc /usr/local/bin #or anywhere else on your PATH

<h2>Usage</h2>

sshrc works just like ssh, but it also sources ~/.sshrc after logging in remotely.

    $ echo "echo welcome" > ~/.sshrc
    $ sshrc me@myserver
    welcome

    $ echo "alias ..='cd ..'" > ~/.sshrc
    $ sshrc me@myserver
    $ type ..
    .. is aliased to `cd ..'

You can use this to set environment variables, define functions, and run post-login commands.

Your most import configuration files (e.g. vim, inputrc) may not be bash scripts. Put them in ~/.sshrc.d and sshrc will copy them to a (guaranteed) unique folder in the /tmp directory after login. You can find them on the sever at $SSHHOME/.sshrc.d

Once the config files are on the server, you can usually tell programs to load their configuration from the $SSHHOME/.sshrc.d directory by setting the right environment variables. For example, vim uses the VIM environment variable.

    $ mkdir -p ~/.sshrc.d
    $ echo ':imap <special> jk <Esc>' > ~/.sshrc.d/vimrc
    $ echo 'VIM=$SSHHOME/.sshrc.d' > ~/.sshrc
    $ sshrc me@myserver
    $ vim # jk -> normal mode will work
    
Putting too much data in ~/.sshrc.d will slow down your login times. If the folder contents are > 1MB, the server may start blocking your sshrc attempts.

If you use tmux frequently, you can make sshrc work there as well.

    $ echo 'SHELL=$SSHHOME/bashsshrc
      alias tmuxrussell="tmux -S /tmp/russelltmuxserver"
      alias foo="echo I work with tmux, too"' > ~/.sshrc
    $ sshrc me@myserver
    $ tmuxrussell
    $ foo
    I work with tmux, too

After the SHELL variable is set, any new tmux server will load your configurations. The -S option will start a separate tmux server at /tmp/russelltmuxserver. You can still use vanilla tmux, but don't start the first shell on the standard tmux server (as long as `tmux ls` is nonempty you're okay) without running `unset SHELL`. Otherwise your server's coinhabitants will get your configurations when they log into the tmux server you created.
