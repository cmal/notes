1. GitHub Global Config
====================
1.1 User Name
-------------
::

    $ git config --global user.name "cmal"
    # Sets the default name for git to use when you commit

1.2 Email
---------
::
    $ git config --global user.email "zyzy5730@163.com"
    # Sets the default email for git to use when you commit
1.3 Password Caching
--------------------
::
    $ git config --global credential.helper cache
    # Set git to use the credential memory cache
::
    $ git config --global credential.helper 'cache --timeout=86400'
    # Set the cache to timeout after 24 hour (setting is in seconds)

2. Generating SSH Keys
======================
2.1 Check for SSH Keys
----------------------
::
    $ cd ~/.ssh
    # Checks to see if there is a directory named ".ssh" in your user directory

    $ ls
    # Lists all the subdirectories in the current directory
    config  id_rsa  id_rsa.pub  known_hosts

    $ mkdir key_backup
    # Makes a subdirectory called "key_backup" in the current directory

    $ cp id_rsa* key_backup
    # Copies the id_rsa keypair into key_backup

    $ rm id_rsa*
    # Deletes the id_rsa keypair
    
2.2 Generate a new SSH key
--------------------------
::
    $ ssh-keygen -t rsa -C "zyzy5730@163.com"
    # Creates a new ssh key using the provided email
    Generating public/private rsa key pair.
    Enter file in which to save the key (/home/cmal/.ssh/id_rsa):

2.3 Add your SSH key to GitHub
------------------------------
::
    $ sudo apt-get install xclip
    # Downloads and installs xclip

    $ xclip -sel clip < ~/.ssh/id_rsa.pub
    # Copies the contents of the id_rsa.pub file to your clipboard
    
Open GitHub.com and paste onto that.
