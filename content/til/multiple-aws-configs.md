---
title: Multiple AWS config files
date: 2022-08-11
summary: >
  This is a brief post on how I fully moved from `vim` to `nvim`.
tags:
 - cli
 - aws
 - direnv
---

# tl;dr

Use direnv and [environment variables to configure the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-envvars.html) to ensure you are interacting with the right AWS account.

```zsh
mkdir -p $HOME/code/cnd && cd $_

echo 'export AWS_CONFIG_FILE="$HOME/.aws/cnd_config"' > .envrc
echo 'export AWS_SHARED_CREDENTIALS_FILE="$HOME/.aws/cnd_credentials"' >> .envrc

direnv allow

aws s3 ls # confirm that this works!
```

## Problem

It's very common for me to have to do work for multiple clients on the same computer. This can be difficult to manage because different organizations use different credentials. This problem is solved in code by using things like `asdf`, `nvm`,
`rbenv` and `pipenv`. But how do we manage this for tools like the AWS CLI?

## Proposal

The AWS CLI references a `~/.aws/config` (and optionally a `~/.aws/credentials`) file. What if we found a way to point
the AWS CLI to a specific file?

Fortunately, [the AWS CLI uses two environment variables](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-envvars.html) to determine where to find these files:

- `AWS_CONFIG_FILE`, which defaults to `$HOME/.aws/config`
- `AWS_SHARED_CREDENTIALS_FILE`, which defaults to `$HOME/.aws/credentials`

One thing I've been leveraging in my workflow is using direnv to set environment variables on a file-system hierachical basis.

In other words:

- when I'm in `$HOME/code/work`, I have custom, base environment variables set in `$HOME/code/work/.envrc`
- when I'm in `$HOME/code/me`, I have custom, base environment variables set specific to my personal work
- and so on

What if I did something similar to **emulate** starting a session on a per-environment basis?

## Solution

First, I created prefixed files in my `$HOME/.aws` directory with the extension of my volunteer organization (Center for New Data -- or cnd for short). This means both `$HOME/.aws/cnd_config` and `$HOME/.aws/cnd_credentials` exist.

Next, I created `$HOME/code/cnd/.envrc` where I set both `AWS_CONFIG_FILE` and `AWS_SHARED_CREDENTIALS_FILE`.

```bash
# example $HOME/code/cnd/.envrc file

export AWS_CONFIG_FILE="$HOME/.aws/cnd_config"
export AWS_SHARED_CREDENTIALS_FILE="$HOME/.aws/cnd_credentials"
```

**Side note:** Could I have set this at the `$HOME/code/cnd/.aws/config` and `$HOME/code/cnd/.aws/credentials`? Sure! I decided to leave them in `$HOME/.aws` because that's the default place to look and frankly I have that directory memorized haha.

# Closing thoughts

I think this works pretty nice! I'm able to swap in and out of my directories and know that I'm interacting with the right AWS account.

One thing I miss about working in Windows is using WSL to have multiple Linux instances completely separate, so that I can be TOTALLY sure. This solution works for those with a single computer 😎
