---
title: How to install Xdebug on Homebrew
layout: post
date: 2014-02-17 16:55:16
categories: xdebug homebrew
---


Installing Xdebug via homebrew on maverick only works if you installed PHP via homebrew.  If you didn’t, I recommend following the installation instructions for [josegonzalez/homebrew-php](https://github.com/Homebrew/homebrew-php). Once you’ve done that, you can find your xdebug configuration at _/usr/local/etc/php/5.4/conf.d/ext-xdebug.ini_.  This configuration should look something like…

```
[xdebug]
zend_extension="/usr/local/Cellar/php54-xdebug/2.2.3/xdebug.so"
xdebug.default_enable=1
xdebug.remote_enable=on
xdebug.remote_handler=dbgp
xdebug.idekey=INSERT_YOUR_KEY
xdebug.remote_autostart=on
```

You’ll need to configure your favorite code editor with the idekey to get things running.