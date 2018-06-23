---
layout: single
title:  "Docker Notes"
header:
  overlay_color: "#000"
  overlay_filter: "0.1"
  overlay_image: /
  cta_label: "Learn stuff step by step"
  cta_url: "https://github.com/AlphaGarden"
  caption: "Photo credit: [**_Jiayong Mo_**](https://alphagarden.github.io)"
classes: wide
date:   2018-06-23 16:26:01 -0600

excerpt: "A personal notes for Docker learning"
categories: Docker
---


``` bash
docker run -ti bash encryption xxxx
```
Use this to run a command in a new container. It suits the situation where you do not have a container running, and you want to create one, start it and then run a process on it.
``` bash
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
```
This is for when you want to run a command in an existing container. This is better if you already have a container running and want to change it or obtain something from it.
``` bash
docker exec
```
