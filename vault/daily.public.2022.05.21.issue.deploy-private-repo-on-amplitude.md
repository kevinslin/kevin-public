---
id: 6a6zlabtuxiralfjc44ybzm
title: Deploy Private Repo on Amplitude
desc: ''
updated: 1653150489725
created: 1653147732793
---

## Summary

Using AWS Amplify to deploy a multi-repo [dendron](https://wiki.dendron.so) wiki (what you are currently reading) with a mix of public and private repositories. 

## Context

You can configure Amplify for **one** repo. If you wish to programatically pull in additional **private git** repositories, you'll need a custom setup.

## Solution
1. Create a custom [deploy key](https://docs.github.com/en/developers/overview/managing-deploy-keys) for the private repo in github
    - generate the key
    ```sh
    ssh-keygen -f deploy_key -N ""
    ```
1. Encode the deploy key as a base64 encoded env variable for amplitude
    ```sh
    cat deploy_key | base64 | tr -d \\n 
    ```
    - add this as a hosting [environment variable](https://docs.aws.amazon.com/amplify/latest/userguide/environment-variables.html#amplify-console-environment-variables) (eg. DEPLOY_KEY)
1. Modify the `amplify.yml` file to make use of the deploy key
    - there's 2 key steps
        - adding deploy key to `ssh-agent`
            - WARNING: this implementation will print the `$DEPLOY_KEY` to `stdout`
        - disabling `StrictHostKeyChecking`
            - NOTE: amplify does not have a `$HOME/.ssh` folder by default so you'll need to create one as part of the deployment process
    - relevant excerpt below
    ```yml
    - ...
    - eval "$(ssh-agent -s)"
    - ssh-add <(echo "$DEPLOY_KEY" | base64 -d)

    - echo "disable strict host key check"
    - mkdir ~/.ssh
    - touch ~/.ssh/config

    - 'echo -e "Host *\n\tStrictHostKeyChecking no\n\n" > ~/.ssh/config'
    - ...
    ```
    - full build file [here](https://github.com/kevinslin/kevin-public-garden/blob/main/amplify.yml)

Now you should be able to use git to clone the private repo.

## Gotchas

- strict host key checking will cause amplitude to fail when pulling from amplitude
- `$HOME/.ssh` folder **does not exit** in regular amplify container
- gitlab doesn't support `ed25519` encryption [^gitlab]
- when trying to repro on mac, the following will fail because the piped file descriptor is deemed to have insufficient permissions...
    ```sh
    ssh-add <(echo "$DEPLOY_KEY" | base64 -d)
    # results in
    ssh-agent + Permissions 0660 for '/dev/fd/11' are too open
    ```
    - workaround is to use `echo "${SSH_PRIVATE_KEY}" | ssh-add -` [^gitlab]

## Troubleshooting

### Permission Denied
```
git@github.com: Permission denied (publickey).\r
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.
```

In my case, this was because no valid deploy key was there

### Host key verification failed
```
Host key verification failed.\r
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.
```

In my case, this was because of `StrictHostKeyChecking`

## Alternatives 

### Tried

### Not Tried
- [Using a github token instead of ssh](https://www.repost.aws/questions/QUUJ9KbqisTlaQEdIMATTBrw/amplify-build-error-could-not-read-from-remote-repository)
- Building a custom container image with keys embedded

## Lookup

### Internet
- [Fetching Private Submodules in AWS Amplify Build](https://stackoverflow.com/questions/56219052/fetching-private-submodules-in-aws-amplify-build)
- [Add Amplify deploy key to multiple github repositories](https://repost.aws/questions/QUMK_kIt1kSJKazyhV8u1vSA)
- [Gitlab issue](https://gitlab.com/gitlab-examples/ssh-private-key/-/issues/1) ^kqz0w4afj5zo

[^gitlab]: [[#^kqz0w4afj5zo]]

### Dendron
- Internal Investigation
    - [[Private Gh Repo|dendron://kevin-private/daily.journal.2022.05.20.private-gh-repo]]
- Service Providers
    - [[Deploy Keys|dendron://notes/s.github.ref.deploy-keys]]
    - [[Env|dendron://notes/aws.s.amplify-hosting.ref.env]]
- SSH
    - [[Ssh Config|dendron://notes/pkg.ssh.config.ssh-config]]
    - [[Ssh Agent|dendron://notes/cli.ssh-agent]]