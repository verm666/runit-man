## Description

Simple [runit](http://smarden.org/runit/ "runit home page") web management tool with internationalization support.

Server will run by **runit-man** script. Take a note that **runit-man** must have privileges like **runsvdir** process ones.

## Screenshot

![Screenshot](https://github.com/Undev/runit-man/raw/master/runit-man-screenshot.gif "Screenshot")

## Installation

Usually You should install both **runit-man** and **thin** gems to run this tool fine.

```bash
gem install runit-man thin
```

Pragmatic approach is to setup runit-man as runit service like this:

```bash
runit-man -p 14500 -r
```

This command installs runit-man as runit service (using default folders `/etc/sv/` and `/etc/service/`).

Look at {file:INSTALL.md} for details.

### rackup configuration

Take a note that runit-man gem also provides config.ru rackup configuration file.
It's useful for running under unicorn/rainbows etc. `runit-man --rackup=command` option does `cd config.ru directory && set environment && exec command`.

## Customization

This tool can provide additional information or actions through it's Web page.

### View names and content of files that related to concrete service

For each known runit service this tool looks for `./runit-man/files-to-view/` folder.
Every symlink here will be shown as link to view target file content.

### Show links that related to concrete service

For each known runit service this tool looks for `./runit-man/urls-to-view/` folder.
Every file ended with .url will be shown as link to view target location (location should be written as content of this file).

### Show buttons that send signals to concrete service

For each known runit service this tool looks for `./runit-man/allowed-signals/` folder.
Each one-letter-named file declares that signal button should be shown in Web UI.

Signal letters listed below in REST API section.

## REST API

### Get state

You can read current state of services in [JSON format](http://www.json.org/ "JSON home page") using
`GET /services.json`

### Management

You can manage your services using
`POST /<service name>/<command>`

Supported commands: *up*, *down*, *restart*, *switch_up* (activates service), *switch_down* (deactivates service).

You can also send any signal to service using
`POST /<service name>/signal/<signal>`

Supported signals and their's meaning:

* t: TERM
* k: KILL
* i: INT
* 1: USR1
* 2: USR2
* a: ALARM
* q: QUIT
* x: EXIT
* p: PAUSE
* c: CONT
* h: HUP
* o: ONCE

### Read logs

#### svlogd

You can read tail of service log using
`GET /<service name>/log/<count of tailing lines>.txt`

Note that to use this feature You must do logging using 

```bash
exec svlogd <options> $LOG_DIRECTORY_LOCATION
```

#### logger
Use logger like:

```bash
exec logger  -i -t "runit-man" -p local1.info
```

and use option `-l "logger:/var/log/"` where base logs directory shown after period.

