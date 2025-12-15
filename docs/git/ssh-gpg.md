Git uses SSH and GPG to verify identities when pushing and signing commits.

## SSH
Using SSH allows you to build an encrypted connection with a Github repository. When you clone a Github repository, it sets a remote url.

```shell
git remote -v
```

This might show one of two things:
```shell
https://github.com/kam-zhan-yue/.config.git
git@github.comm:kam-zhan-yue/.config.git
```

- If it is HTTPS -> Git will ask for a token
- If it is SSH -> Git will use your SSH key

When using HTTPS, if you don't have a token (`gh auth login`), then it might ask for a username and password (deprecated).
When using the SSH key, the passphrase is needed unless cached in `ssh-agent`.

> TLDR: `gh auth login` with HTTPS only works when the repository is configured with HTTPS. Likewise with SSH.

Additionally, SSH is annoying because you have to input your password everytime.

You can change the remote URL with 
```shell
git rmeote set-url origin https://github.com/kam-zhan-yue/.config.git
```

## GPG
Using GPG allows you to sign commits. To generate a GPG key, the machine needs to install GnuPG and a GPG key needs to be created.

This GPG key is then added to the account and can be attached for signing. Since I use multiple machines and want to have different keys on each, I use a GPG key in the .gitconfig.local which is not tracked by git and set per machine :)

### COMMON ERRORS
I ran into an issue where running `git commit -m -S` gave me an unhelpful error.
```shell
❯ git commit -m "test"
error: gpg failed to sign the data
fatal: failed to write commit object
```

However, `jj` revealed more detailed logs.
```shell
➜ jj git push
Internal error: Unexpected error from backend
Caused by:
1: Could not write object of type commit
2: Signing error
3: GPG failed with exit status: 2:
gpg: signing failed: Screen or window too small
gpg: signing failed: Screen or window too small
```

Upon investigation, [it was due to the password entry daemon](https://github.com/kovidgoyal/kitty/issues/6018#issuecomment-2495642902). We can fix it by doing
```shell
gpgconf --kill gpg-agent
gpgconf --launch gpg-agent
```