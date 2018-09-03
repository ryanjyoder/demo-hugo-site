---
title: "Snapping Hugo - Step by step"
date: 2018-02-13T13:56:12-05:00
showDate: true
draft: false
thumbnail: images/snapdir.png
tags: ["poem","code"]
---

Building a hugo site as snpa is very simple. Copy and paste the snap directory of this repo in to the same directory as your hugo site. https://github.com/ryanjyoder/demo-hugo-site/
<!--more-->
### The snapcraft.yaml
Here's the entire `snapcraft.yaml`. We'll go over each section of the file below.
```yaml
name: demo-hugo-site
version: 0.1
summary: Demo Hugo Site - Snap a hugo site
description: |
 Build a hugo site and snap it with a webserver.

grade: stable
confinement: strict
environment:
    GO111MODULE: 'on'

parts:
  content:
    plugin: dump
    stage: [config.toml,archetypes, content, data, layouts, static, themes]
    after: [hugo]

  hugo:
    plugin: nil
    override-build: |
      echo "\nStarting override-build:"
      url=""
      arch=`echo $SNAPCRAFT_ARCH_TRIPLET | awk -F- '{print $1}'` 
      case "$arch" in
      x86_64) 
      url=https://github.com/gohugoio/hugo/releases/download/v0.48/hugo_0.48_Linux-64bit.tar.gz
      ;;
      arm)
      url=https://github.com/gohugoio/hugo/releases/download/v0.48/hugo_0.48_Linux-ARM.tar.gz
      ;;
      aarch64)
      url=https://github.com/gohugoio/hugo/releases/download/v0.48/hugo_0.48_Linux-ARM64.tar.gz
      ;;
      i368)
      url=https://github.com/gohugoio/hugo/releases/download/v0.48/hugo_0.48_Linux-32bit.tar.gz
      ;;
      *)
      echo SNAP_ARCH unknow type $SNAP_ARCH
      env
      exit 1
      esac
      curl $url -L --output hugo.tar.gz
      tar -xvzf hugo.tar.gz
      install -d $SNAPCRAFT_PART_INSTALL/bin
      cp -av hugo $SNAPCRAFT_PART_INSTALL/bin/


apps:
  hugo:
    command: hugo server -s $SNAP --bind 0.0.0.0 --disableLiveReload 
    daemon: simple
    plugs: [network, network-bind]
```
  
  <br>
### The Content
The first section of the snapcraft.yaml file copies the site content into the snap.

```yaml
  content:
    plugin: dump
    stage: [config.toml,archetypes, content, data, layouts, static, themes]
```
Note: if you are not using `toml` for your config file, you will need to change it here. You'll replace `config.toml` with `config.yaml` or `config.json`


<br>
### Hugo

The second section downloads hugo. The section is a bit verbose, because we have to select the correct tar based on the architecture.
```yaml
  hugo:
    plugin: nil
    override-build: |
      echo "\nStarting override-build:"
      url=""
      arch=`echo $SNAPCRAFT_ARCH_TRIPLET | awk -F- '{print $1}'` 
      case "$arch" in
      x86_64) 
      url=https://github.com/gohugoio/hugo/releases/download/v0.48/hugo_0.48_Linux-64bit.tar.gz
      ;;
      arm)
      url=https://github.com/gohugoio/hugo/releases/download/v0.48/hugo_0.48_Linux-ARM.tar.gz
      ;;
      aarch64)
      url=https://github.com/gohugoio/hugo/releases/download/v0.48/hugo_0.48_Linux-ARM64.tar.gz
      ;;
      i368)
      url=https://github.com/gohugoio/hugo/releases/download/v0.48/hugo_0.48_Linux-32bit.tar.gz
      ;;
      *)
      echo SNAP_ARCH unknow type $SNAP_ARCH
      env
      exit 1
      esac
      curl $url -L --output hugo.tar.gz
      tar -xvzf hugo.tar.gz
      install -d $SNAPCRAFT_PART_INSTALL/bin
      cp -av hugo $SNAPCRAFT_PART_INSTALL/bin/

```
Note: You can make changes to how hugo runs here. Port changes for example can be make here. If you have too many pages to hold in memory, considering adding `--renderToDisk --cacheDir $SNAP_DATA/public` to the command.
