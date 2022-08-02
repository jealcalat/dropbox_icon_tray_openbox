# Icon tray and status for dropbox in polybar with Openbox

I managed to show the Dropbox icon status in Polybar.

Follow this steps:
1. Install the dropbox client for your linux distro (in arch linux and arch-based distros, `yay -S dropbox`). For Openbox WM add this line in the `autostart` script: 

```zsh
(sleep 15s && ~/.dropbox-dist/dropboxd) &
```

2. Download the `dropbox.py` script from the [`installer-linux`](https://www.dropbox.com/download?dl=packages/dropbox.py).
3. Download [this](https://github.com/polybar/polybar/issues/131#issuecomment-256913524) shell script and add an alias to the `dropbox.py` script. To make it work on non-interactive shells, expand the aliases. For `zsh` use setopt aliases`. The shell script should look like this:

```zsh
#!/bin/sh
setopt aliases
alias dropboxPy='~/Dropbox/dropbox.py'
# change this line using the alias
STATUS="$(echo `dropboxPy status` | awk '{print $1;}')"
STATUS_COMPLETE="$(echo `dropboxPy status` | awk '{print $1" "$2" "$3" "$4;}')"
C_status=#fbf1c7
DROPBOX_ICON=""
if [[ $STATUS == *"Dropbox"* ]]; then
  C_status=#ec644b
elif [[ $STATUS == *"Starting"* ]]; then
  #statements
  ICON=
elif [[ $STATUS == *"Syncing"* ]]; then
  DROPBOX_ICON=
else
  I=1
fi
echo "%{F$C_status}$DROPBOX_ICON $ICON $STATUS_COMPLETE"
```

4. Create a module in the `modules.ini` of your Polybar. Mine is

```inputrc
[module/dropbox]
type = custom/script
exec = zsh ~/.config/openbox/polybar/dropboxStatus.sh
format-underline = ${color.foreground}
;#458588
click-left = python ~/Dropbox/dropbox.py start
interval=2
format-padding = 2
```

5. Finally, add the module to the `config.ini` of the Polybar wherever you want. For example, on a `[bar/bottom]` on the left 


```inputrc
modules-left = dropbox spotify previous playpause next
```

![Dropbox Up To Date](./img/dpx.png)
