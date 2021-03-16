---
title: Quickly download a Github asset from the latest release
date: 2021-03-16
summary: >
  Learn how to quickly download a Github asset from a repository tagged latest.
tags:
  - git
  - github
  - cli
  - bash
---

# tl;dr

```bash
download_url=$(
  curl -sL https://api.github.com/repos/<owner>/<repo>/releases/<tag> \
  | jq -r '.assets[] | select(.name | contains("unique identifier")) | .browser_download_url'
)

curl -sOL "$download_url"
```

# Motivation

I recently was working on automating setting up a new machine, which meant that I needed to figure out which tools needed to be ported over. For each tool I found, I wanted to write a simple bash script to install the tool. Some tools were a simple `apt-get` and others required a more manual install.

I found a few tools I installed that were `.deb` assets in a Github release for a given repo (e.g. [`sharkdp/bat`](https://github.com/sharkdp/bat/)). Some tools version their compiled assets by adding a version number to the file name, which means that programmatically getting the latest released version of the tool requires a few steps. Thus, this effort was started.

# Github API

One neat thing about Github repos is that you can go to https://api.github.com/repos/<owner>/<repo>/releases/<tag> to find out more information about a release, in a JSON response.

In our example of [`sharkdp/bat`](https://github.com/sharkdp/bat), the following URL would give us information about the latest release:

```
https://api.github.com/repos/sharkdp/bat/releases/latest
```

At the time of writing, the response looked like:

```json
{
  "url": "https://api.github.com/repos/sharkdp/bat/releases/39023558",
  "assets_url": "https://api.github.com/repos/sharkdp/bat/releases/39023558/assets",
  "upload_url": "https://uploads.github.com/repos/sharkdp/bat/releases/39023558/assets{?name,label}",
  "html_url": "https://github.com/sharkdp/bat/releases/tag/v0.18.0",
  "id": 39023558,
  "author": {
		"redacted-for": "saving-space-in-this-blog-post"
  },
  "node_id": "MDc6UmVsZWFzZTM5MDIzNTU4",
  "tag_name": "v0.18.0",
  "target_commitish": "master",
  "name": "v0.18.0",
  "draft": false,
  "prerelease": false,
  "created_at": "2021-02-28T22:04:49Z",
  "published_at": "2021-02-28T22:25:07Z",
  "assets": [
    {
      "url": "https://api.github.com/repos/sharkdp/bat/releases/assets/32755515",
      "id": 32755515,
      "node_id": "MDEyOlJlbGVhc2VBc3NldDMyNzU1NTE1",
      "name": "bat-musl_0.18.0_amd64.deb",
      "label": "",
      "uploader": {
				"redacted-for": "saving-space-in-this-blog-post"
      },
      "content_type": "application/octet-stream",
      "state": "uploaded",
      "size": 2108024,
      "download_count": 444,
      "created_at": "2021-02-28T22:32:33Z",
      "updated_at": "2021-02-28T22:32:34Z",
      "browser_download_url": "https://github.com/sharkdp/bat/releases/download/v0.18.0/bat-musl_0.18.0_amd64.deb"
    },
    {
      "url": "https://api.github.com/repos/sharkdp/bat/releases/assets/32755569",
      "id": 32755569,
      "node_id": "MDEyOlJlbGVhc2VBc3NldDMyNzU1NTY5",
      "name": "bat-musl_0.18.0_i686.deb",
      "label": "",
      "uploader": {},
      "content_type": "application/gzip",
      "state": "uploaded",
      "size": 2433507,
      "download_count": 45,
      "created_at": "2021-02-28T22:34:23Z",
      "updated_at": "2021-02-28T22:34:23Z",
      "browser_download_url": "https://github.com/sharkdp/bat/releases/download/v0.18.0/bat-v0.18.0-aarch64-unknown-linux-gnu.tar.gz"
    },
    {
      "url": "https://api.github.com/repos/sharkdp/bat/releases/assets/32755580",
      "id": 32755580,
      "node_id": "MDEyOlJlbGVhc2VBc3NldDMyNzU1NTgw",
      "name": "bat-v0.18.0-arm-unknown-linux-gnueabihf.tar.gz",
      "label": "",
      "uploader": {},
      "content_type": "application/gzip",
      "state": "uploaded",
      "size": 2507785,
      "download_count": 25,
      "created_at": "2021-02-28T22:34:54Z",
      "updated_at": "2021-02-28T22:34:55Z",
      "browser_download_url": "https://github.com/sharkdp/bat/releases/download/v0.18.0/bat-v0.18.0-arm-unknown-linux-gnueabihf.tar.gz"
    },
    {
      "url": "https://api.github.com/repos/sharkdp/bat/releases/assets/32755699",
      "id": 32755699,
      "node_id": "MDEyOlJlbGVhc2VBc3NldDMyNzU1Njk5",
      "name": "bat-v0.18.0-i686-pc-windows-msvc.zip",
      "label": "",
      "uploader": {},
      "content_type": "application/zip",
      "state": "uploaded",
      "size": 2534190,
      "download_count": 18,
      "created_at": "2021-02-28T22:38:47Z",
      "updated_at": "2021-02-28T22:38:48Z",
      "browser_download_url": "https://github.com/sharkdp/bat/releases/download/v0.18.0/bat-v0.18.0-i686-pc-windows-msvc.zip"
    },
    {
      "url": "https://api.github.com/repos/sharkdp/bat/releases/assets/32755531",
      "id": 32755531,
      "node_id": "MDEyOlJlbGVhc2VBc3NldDMyNzU1NTMx",
      "name": "bat-v0.18.0-i686-unknown-linux-gnu.tar.gz",
      "label": "",
      "uploader": {},
      "content_type": "application/gzip",
      "state": "uploaded",
      "size": 2632101,
      "download_count": 22,
      "created_at": "2021-02-28T22:33:22Z",
      "updated_at": "2021-02-28T22:33:23Z",
      "browser_download_url": "https://github.com/sharkdp/bat/releases/download/v0.18.0/bat-v0.18.0-i686-unknown-linux-gnu.tar.gz"
    },
    {
      "url": "https://api.github.com/repos/sharkdp/bat/releases/assets/32755568",
      "id": 32755568,
      "node_id": "MDEyOlJlbGVhc2VBc3NldDMyNzU1NTY4",
      "name": "bat-v0.18.0-i686-unknown-linux-musl.tar.gz",
      "label": "",
      "uploader": {},
      "content_type": "application/gzip",
      "state": "uploaded",
      "size": 2636932,
      "download_count": 25,
      "created_at": "2021-02-28T22:34:38Z",
      "updated_at": "2021-02-28T22:34:38Z",
      "browser_download_url": "https://github.com/sharkdp/bat/releases/download/v0.18.0/bat-v0.18.0-i686-unknown-linux-musl.tar.gz"
    },
    {
      "url": "https://api.github.com/repos/sharkdp/bat/releases/assets/32755587",
      "id": 32755587,
      "node_id": "MDEyOlJlbGVhc2VBc3NldDMyNzU1NTg3",
      "name": "bat-v0.18.0-x86_64-apple-darwin.tar.gz",
      "label": "",
      "uploader": {},
      "content_type": "application/gzip",
      "state": "uploaded",
      "size": 2444324,
      "download_count": 138,
      "created_at": "2021-02-28T22:35:11Z",
      "updated_at": "2021-02-28T22:35:12Z",
      "browser_download_url": "https://github.com/sharkdp/bat/releases/download/v0.18.0/bat-v0.18.0-x86_64-apple-darwin.tar.gz"
    },
    {
      "url": "https://api.github.com/repos/sharkdp/bat/releases/assets/32755704",
      "id": 32755704,
      "node_id": "MDEyOlJlbGVhc2VBc3NldDMyNzU1NzA0",
      "name": "bat-v0.18.0-x86_64-pc-windows-gnu.zip",
      "label": "",
      "uploader": {},
      "content_type": "application/zip",
      "state": "uploaded",
      "size": 2760469,
      "download_count": 34,
      "created_at": "2021-02-28T22:39:19Z",
      "updated_at": "2021-02-28T22:39:20Z",
      "browser_download_url": "https://github.com/sharkdp/bat/releases/download/v0.18.0/bat-v0.18.0-x86_64-pc-windows-gnu.zip"
    },
    {
      "url": "https://api.github.com/repos/sharkdp/bat/releases/assets/32755581",
      "id": 32755581,
      "node_id": "MDEyOlJlbGVhc2VBc3NldDMyNzU1NTgx",
      "name": "bat-v0.18.0-x86_64-pc-windows-msvc.zip",
      "label": "",
      "uploader": {},
      "content_type": "application/zip",
      "state": "uploaded",
      "size": 2694485,
      "download_count": 1786,
      "created_at": "2021-02-28T22:35:00Z",
      "updated_at": "2021-02-28T22:35:01Z",
      "browser_download_url": "https://github.com/sharkdp/bat/releases/download/v0.18.0/bat-v0.18.0-x86_64-pc-windows-msvc.zip"
    },
    {
      "url": "https://api.github.com/repos/sharkdp/bat/releases/assets/32755519",
      "id": 32755519,
      "node_id": "MDEyOlJlbGVhc2VBc3NldDMyNzU1NTE5",
      "name": "bat-v0.18.0-x86_64-unknown-linux-gnu.tar.gz",
      "label": "",
      "uploader": {},
      "content_type": "application/gzip",
      "state": "uploaded",
      "size": 2580440,
      "download_count": 860,
      "created_at": "2021-02-28T22:32:51Z",
      "updated_at": "2021-02-28T22:32:51Z",
      "browser_download_url": "https://github.com/sharkdp/bat/releases/download/v0.18.0/bat-v0.18.0-x86_64-unknown-linux-gnu.tar.gz"
    },
    {
      "url": "https://api.github.com/repos/sharkdp/bat/releases/assets/32755514",
      "id": 32755514,
      "node_id": "MDEyOlJlbGVhc2VBc3NldDMyNzU1NTE0",
      "name": "bat-v0.18.0-x86_64-unknown-linux-musl.tar.gz",
      "label": "",
      "uploader": {},
      "content_type": "application/gzip",
      "state": "uploaded",
      "size": 2602252,
      "download_count": 333,
      "created_at": "2021-02-28T22:32:33Z",
      "updated_at": "2021-02-28T22:32:34Z",
      "browser_download_url": "https://github.com/sharkdp/bat/releases/download/v0.18.0/bat-v0.18.0-x86_64-unknown-linux-musl.tar.gz"
    },
    {
      "url": "https://api.github.com/repos/sharkdp/bat/releases/assets/32755520",
      "id": 32755520,
      "node_id": "MDEyOlJlbGVhc2VBc3NldDMyNzU1NTIw",
      "name": "bat_0.18.0_amd64.deb",
      "label": "",
      "uploader": {},
      "content_type": "application/octet-stream",
      "state": "uploaded",
      "size": 2078288,
      "download_count": 1503,
      "created_at": "2021-02-28T22:32:51Z",
      "updated_at": "2021-02-28T22:32:51Z",
      "browser_download_url": "https://github.com/sharkdp/bat/releases/download/v0.18.0/bat_0.18.0_amd64.deb"
    },
    {
      "url": "https://api.github.com/repos/sharkdp/bat/releases/assets/32755553",
      "id": 32755553,
      "node_id": "MDEyOlJlbGVhc2VBc3NldDMyNzU1NTUz",
      "name": "bat_0.18.0_arm64.deb",
      "label": "",
      "uploader": {},
      "content_type": "application/octet-stream",
      "state": "uploaded",
      "size": 1884996,
      "download_count": 93,
      "created_at": "2021-02-28T22:34:23Z",
      "updated_at": "2021-02-28T22:34:23Z",
      "browser_download_url": "https://github.com/sharkdp/bat/releases/download/v0.18.0/bat_0.18.0_arm64.deb"
    },
    {
      "url": "https://api.github.com/repos/sharkdp/bat/releases/assets/32755579",
      "id": 32755579,
      "node_id": "MDEyOlJlbGVhc2VBc3NldDMyNzU1NTc5",
      "name": "bat_0.18.0_armhf.deb",
      "label": "",
      "uploader": {},
      "content_type": "application/octet-stream",
      "state": "uploaded",
      "size": 1922892,
      "download_count": 106,
      "created_at": "2021-02-28T22:34:54Z",
      "updated_at": "2021-02-28T22:34:55Z",
      "browser_download_url": "https://github.com/sharkdp/bat/releases/download/v0.18.0/bat_0.18.0_armhf.deb"
    },
    {
      "url": "https://api.github.com/repos/sharkdp/bat/releases/assets/32755530",
      "id": 32755530,
      "node_id": "MDEyOlJlbGVhc2VBc3NldDMyNzU1NTMw",
      "name": "bat_0.18.0_i686.deb",
      "label": "",
      "uploader": {},
      "content_type": "application/octet-stream",
      "state": "uploaded",
      "size": 2140228,
      "download_count": 26,
      "created_at": "2021-02-28T22:33:22Z",
      "updated_at": "2021-02-28T22:33:22Z",
      "browser_download_url": "https://github.com/sharkdp/bat/releases/download/v0.18.0/bat_0.18.0_i686.deb"
    }
  ],
  "tarball_url": "https://api.github.com/repos/sharkdp/bat/tarball/v0.18.0",
  "zipball_url": "https://api.github.com/repos/sharkdp/bat/zipball/v0.18.0",
  "body": "redacted-to-save-space"
}
```

# Programatically downloading an asset

In order to programmatically parse outwhich asset I needed, I used [`jq`](https://stedolan.github.io/jq/) to parse the json response, and find the latest `amd64.deb` file.

Since there is a `bat-musl_<version>_amd64.deb` and a `bat_<version>_amd64.deb`, I needed to write the following `jq` expression:

```bash
jq -r '.assets[] | select(.name | (contains("bat_") and contains("amd64.deb"))) | .browser_download_url'
```

Breaking this expression down:

  - `.assets[]` lets us operate on the `assets` value (an array of objects).
  - `.select` allows us to filter `assets`. We use the `.name` attribute, and if it contains both `bat_` (not `bat-musl`) and `amd64.deb`.
  - `.browser_download_url` is the output value of the assets we selected (which should just be one).

Once we have the download url, we simply do a curl to download the file. Combining this all together:

```bash
# This sets the output of jq to the $download_url bash variable.

download_url=$(
  curl -sL https://api.github.com/repos/sharkdp/bat/releases/latest \
  | jq -r '.assets[] | select(.name | (contains("bat_") and contains("amd64.deb"))) | .browser_download_url'
)

curl -sOL "$download_url"
```

One thing not mentioned here is that in order to programmatically install this, I would either (a) need to know the filename or (b) set the filename. Since this was a one time thing (on a new computer), I figured option b would be the easiest to get me there.

So I modified the curl command which downloads the `.deb`:

```bash
curl -sL "$download_url" bat-latest.deb

# Install the package
sudo dpkg -i bat-latest.deb

# Delete installer
rm bat-latest.deb
```
