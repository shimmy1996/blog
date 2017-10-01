+++
title = "Get emacs To Work With fcitx"
lastmod = 2017-09-30T23:17:04-05:00
tags = ["emacs", "fcitx"]
categories = ["emacs"]
draft = true
date = 2017-09-25
slug = "get-emacs-to-work-with-fcitx"
+++

## Enabling fcitx in emacs {#enabling-fcitx-in-emacs}


### fcitx-diagnose {#fcitx-diagnose}


### locale settings {#locale-settings}

export zh<sub>cn.utf</sub>-8
set lc<sub>ctype</sub>=zh<sub>cn.utf</sub>-8


### fcitx plugin in emacs {#fcitx-plugin-in-emacs}


### disabling ctrl+space hot key {#disabling-ctrl-space-hot-key}

replaced with os+space and ctrl+os


## could be better {#could-be-better}

do not want set lc-ctype globally
run emacs as daemon


### create new entry in .config/systemd/user/emacs.service {#create-new-entry-in-dot-config-systemd-user-emacs-dot-service}


### add environment variable {#add-environment-variable}


### problem: does not load init file {#problem-does-not-load-init-file}


### setting up alias of emacsclient -nc in .zshrc {#setting-up-alias-of-emacsclient-nc-in-dot-zshrc}

alias e="emacsclient -nc"