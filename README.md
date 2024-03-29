# SSH and GPG with [gitpod](https://gitpod.io/)

## Get Your `.gitconfig`

1. Your Git Config is located at `~/.gitconfig`
2. Encode the File with base64:

```bash
cat .gitconfig | base64 -w 0
```

3. [Create a variable](https://gitpod.io/variables) on gitpod.

   - Enter **Name** `GITCONFIG`.
   - Enter output of previous command as **Value**.

4. Add following task to your `.gitpod.yml`:

```yml
tasks:
  - before: >
      [[ ! -z $GITCONFIG  ]] &&
      echo $GITCONFIG | base64 -d > ~/.gitconfig &&
      chmod 644 ~/.gitconfig
```

## Make commits GPG-signed

1. To sign commit using GPG you must first add GPG keys to Github and/or Gitlab.

   - [For Github](https://docs.github.com/en/authentication/managing-commit-signature-verification)
   - [For Gitlab](https://docs.gitlab.com/ee/user/project/repository/gpg_signed_commits/)

2. Your GPG Keys are stored in `~/.gnupg` folder. Encode this folder with base64.

```bash
tar -czf - ~/.gnupg | base64 -w 0
```

3. [Create a variable](https://gitpod.io/variables) on gitpod.
   - Enter **Name** `GNUPG`.
   - Enter output of previous command in **Value**.
4. Add following task to your `.gitpod.yml`:

```yml
tasks:
  - before: >
      [[ ! -z $GNUPG  ]] &&
      cd ~ &&
      rm -rf .gnupg &&
      echo $GNUPG | base64 -d | tar --no-same-owner -xzf -
```

## SSH Github and Gitlab from gitpod

1. Add SSH Keys to Github and/or Gitlab
   - [For Github](https://docs.github.com/en/authentication/connecting-to-github-with-ssh)
   - [For Gitlab](https://docs.gitlab.com/ee/ssh/)
2. Get your SSH Public Key from `id_rsa.pub`
   - [Create a variable](https://gitpod.io/variables) on gitpod.
   - Enter **Name** `SSH_PUBLIC_KEY`.
   - Paste the contents of file in **Value**.
3. Get your SSH Public Key from `id_rsa`

   - [Create a variable](https://gitpod.io/variables) on gitpod.
   - Enter **Name** `SSH_PRIVATE_KEY`.
   - Encode the File `id_rsa` with base64.

   ```bash
   cat id_rsa | base64 -w 0
   ```

   - Paste the output of previous command in **Value**.

4. Add Following tasks to your `.gitpod.yml`:

```yml
tasks:
  - before: >
      mkdir -p ~/.ssh &&
      [[ ! -z $SSH_PUBLIC_KEY  ]] &&
      echo $SSH_PUBLIC_KEY > ~/.ssh/id_rsa.pub &&
      chmod 644 ~/.ssh/id_rsa.pub &&
      [[ ! -z $SSH_PRIVATE_KEY  ]] &&
      echo $SSH_PRIVATE_KEY | base64 -d > ~/.ssh/id_rsa &&
      chmod 600 ~/.ssh/id_rsa
```
