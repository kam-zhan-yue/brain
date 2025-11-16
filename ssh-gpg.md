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

