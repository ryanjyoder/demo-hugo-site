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

parts:
  content:
    plugin: dump
    stage: [config.toml,archetypes, content, data, layouts, static, themes]

  webserver:
    source: https://github.com/gohugoio/hugo.git
    source-type: git
    plugin: go
    go-importpath: github.com/gohugoio/hugo


apps:
  webserver:
    command: hugo server -s $SNAP --bind 0.0.0.0
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

The second section builds hugo.
```yaml
apps:
  webserver:
    command: hugo server -s $SNAP --bind 0.0.0.0 --disableLiveReload
    daemon: simple
    plugs: [network, network-bind]
```
Note: You can make changes to how hugo runs here. Port changes for example can be make here. If you have too many pages to hold in memory, considering adding `--renderToDisk --cacheDir $SNAP_DATA/public` to the command.
