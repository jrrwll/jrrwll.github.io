
---
title: "osascript"
---

## osascript

```shell
osascript /example/path/to/AppleScript.scpt

osascript -e 'tell application "System Events" to keystroke "q" using {command down,control down}'

alias ejectall='osascript -e 'tell application "Finder" to eject (every disk whose ejectable is true)''
```

**lock screen via cli on mac**

```shell
pmset displaysleepnow

/System/Library/CoreServices/ScreenSaverEngine.app/Contents/MacOS/ScreenSaverEngine

# control command Q
osascript -e 'tell application "System Events" to keystroke "q" using {command down,control down}'
# sleep Finder
osascript -e 'tell application "Finder" to sleep'
```

## gui

```ruby
# display a dialog box
display dialog "Hello World!" with title "Hello"

# mute the system volume
set volume 0

# send a standard quit signal to a GUI application
quit app "APPLICATIONNAME"

# change the desktop wallpaper
tell application "Finder" to set desktop picture to POSIX file "/path/to/picture.jpg"
```

## finder

```ruby
# open a new Finder window
tell app "Finder" to make new Finder window

tell application "Finder" to sleep

# Ejecting All Mounted Volumes, Drives, and Disk Images
tell application "Finder" to eject (every disk whose ejectable is true)

tell application "Finder" to set desktop picture to POSIX file "/path/to/picture.jpg"
```
