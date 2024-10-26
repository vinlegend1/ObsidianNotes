---
title: Custom Dmenu Scripts
date: 2024-02-28T20:12:28+08:00
tags:
  - dmenu
  - bash
  - automation
author: vinceroo
---

## Dmenu script to load other binaries

**note** that the directory used was `~/.local/pbin`, you need to add it to your `$PATH`

```R
#!/bin/bash

# Directory containing executables
directory="$HOME/.local/pbin/"

# Get list of executable files in the directory
options=$(find "$directory" -type f -executable -printf "%f\n")

# Use dmenu to select an option
selected_option=$(echo "$options" | dmenu -p "Select an executable:")

# If an option is selected, execute the corresponding binary
if [ -n "$selected_option" ]; then
    selected_path="$directory$selected_option"
    "$selected_path"
fi
```

## Check Battery Health (not dmenu)
original command to check battery health and general info (for BAT0, if you only have one battery)
```
upower -i /org/freedesktop/UPower/devices/battery_BAT0
```

This script finds the crucial information (capacity, and the maximum energy the battery can hold) and pipes it to `notify-send` to send a notification.
```
#!/bin/bash

output=$(your_command_here) # Replace 'your_command_here' with the command that generates the output

capacity=$(echo "$output" | grep -oP 'capacity:\s+\K\d+\.\d+%')
energy_full=$(echo "$output" | grep -oP 'energy-full:\s+\K\d+\.\d+\s+Wh')

notify-send "Battery Information" "Capacity: $capacity\nEnergy Full: $energy_full"
```