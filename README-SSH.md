# Git, Github, and SSH

Follow the original [Github](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent) article.

In case you need to setup multiple SSH keys (for example if you are working on computer's work), try the following:

## Setup multiple SSH keys

### Generate SSH keys

Open your terminal / CMD PROMPT and type the following command:

```
ssh-keygen -t ed25519 -C "your_email@example.com"
```

The command will ask ssh to generate a key for you. After running the command, you will see the following feedback:

```
Generating public/private rsa key pair.
```

The next line you will see would be:

```
Enter file in which to save the key (/Users/YOU/.ssh/id_ed25519): 
```

Here, you can specify a directory and filename for the ssh key that will be generated. 
The default is `/Users/YOU/.ssh/id_ed25519`. It will be saved in `/Users/YOU/.ssh/` with the file name `id_ed25519`.
It will generate two files. `id_ed25519` and `id_ed25519.pub`. 
The `id_ed25519.pub` contains your public key which you will use, you can give this to your team leader or to other people that you want. 
The `id_ed25519` is the private key, so __don't give this key to anyone__.

In this case, since we are going to generate two ssh keys, we don't want to keep the default file name, set it to whatever name you want by giving it `/Users/YOU/.ssh/file_name`. 
I named mine `id_ed25519_personal`.

The next line you will see would be:

```
Enter passphrase (empty for no passphrase): 
```

If you type a passphrase here, you will have to remember that and type the same passphrase again everytime you use this key.
I'll leave it up to you to decide. For me, I did not add any passphrase so I simply pressed enter. 
The next line would ask you to retype the passphrase again, of course if you left it empty then just press enter.

With all that you should have gotten something that looks like this:

```console
foo@bar:~$ ssh-keygen -t ed25519 -C "your_email@example.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/YOU/.ssh/id_ed25519): /Users/YOU/.ssh/id_ed25519_personal
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /Users/YOU/.ssh/id_ed25519_personal.
Your public key has been saved in /Users/YOU/.ssh/id_ed25519_personal.pub.
The key fingerprint is:
0b:0a:9f:58:2d:c8:a3:87:f7:44:17:f6:2c:d8:b7:3a your_email@example.com
The key's randomart image is:
+--[ RSA 4096]----+
|                 |
|                 |
|      o          |
| . . = +         |
|  = = * S        |
| o B = + o       |
|o + =   o        |
| o o  E.         |
|    . ..         |
+-----------------+
```

That's your first key. Now run `cd ~/.ssh` and then run `ls`. `ls` would list all files in the directory. 
You should see the keys that have been generated.

```
id_ed25519_personal			id_ed25519_personal.pub
```

Now try to generate another one by following the same procedure again. I named my second key `id_ed25519_work`. Once you're done you should have the following files (respective to the filename you gave it):

```
id_ed25519_personal			id_ed25519_personal.pub			id_ed25519_work			id_ed25519_work.pub
```

### Adding ssh keys to github accounts (or whatever you use)

First, I'll add my `id_ed25519_personal` ssh key.

1. Go to your github account then go to `settings -> SSH and GPG keys`. Click on `New SSH key` button.
2. Go to your terminal again and run this command: `cat ~/.ssh/<yourfilename>.pub` replacing `<yourfilename>` with whatever file name you gave it, in this case mine is `id_ed25519_personal.pub`.
After running the commands above, you'll see something like this:
    ```
    ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCha2J5mW3i3BgtZ25TUdKE2Xo92989tQ== your_email@example.com
    ```
3. Copy paste that to the key on your `new ssh key form` then give it whatever title you like. I gave mine `macbook`.
4. Done! Now you have added your first ssh key to your github account, now add the other one to another github account or to the same github account giving it a different title so you can differentiate between the two.

### Configure SSH

1. On your terminal / CMD PROMPT, run this command: `touch ~/.ssh/config` this will create a file with the file name of `config` on the `~/.ssh` folder. Now Go to that folder and open that file with your text editor of choice.
2. Copy & paste the following on it:
    ```
    Host gh_work
        HostName github.com
        IdentityFile ~/.ssh/id_ed25519_personal
    ```
    The `Host` is how you would referrence this credentials on your terminal. The `HostName` is whatever platform you use, in this case `github.com`. The `IdentityFile` is the `ssh key` to be used for this credential.
3. Now add another one. By the end you should have something like these:

```
Host gh_work
    HostName github.com
    IdentityFile ~/.ssh/id_ed25519_work

Host gh_personal
    HostName github.com
    IdentityFile ~/.ssh/id_ed25519_personal
```

# Testing connection

On your terminal / CMD PROMPT, run `ssh -T git@<myHost>` replacing `myHost` with the host you wrote on the `config` file. Mine is `ssh -T git@gh_personal`.

After running that command, you should see something like this: `Hi <username>! You've successfully authenticated, but GitHub does not provide shell access.`. Now you know it's working. Go ahead and test the other key you added.

Note: you might need to restart the ssh-agent.

# Cloning repository using a specific ssh key

For this example I will clone this repository: https://github.com/aprilmintacpineda/chat-with-people-backend. To test if yours keys work, you should your own test repository.

Run `git clone git@<myHost>:YOUR_GITHUB_USERNAME/REPOSITORY_NAME.git`. Replacing myHost with the `Host` you want that was specified on your `config`. Ones it's done, you can run `git remote -v` and you'll see something like this:

```
origin	git@gh_personal:YOUR_GITHUB_USERNAME/REPOSITORY_NAME.git (fetch)
origin	git@gh_personal:YOUR_GITHUB_USERNAME/REPOSITORY_NAME.git (push)
```

You see now that it would use my `gh_personal` keys everytime. Now make changes and commit and push the changes. If all worked well you should get no errors.

Note that if you already have an existing clone in your machine, you can do this:

```
git remote add origin git@<myHost>:YOUR_GITHUB_USERNAME/REPOSITORY_NAME.git
```

then simply push to that remote.

Now you're all set, you can test the other key too!

# Resources

- https://help.github.com/articles/connecting-to-github-with-ssh/
- https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/
- https://gist.github.com/aprilmintacpineda/f101bf5fd34f1e6664497cf4b9b9345f
