#bash #automation 
```bash
#!/bin/bash

# Define options
options="Pokemon\nStardew Valley"

# Use dmenu to choose
choice=$(echo -e "$options" | dmenu -p "Choose your lo-fi music:")

# URLs for Pokémon and Stardew Valley lo-fi playlists
pokemon_url="https://youtu.be/6CjpgFOOtuI"  # Replace with actual URL
stardew_url="https://youtu.be/eEM7ORqC3HY"  # Replace with actual URL

# Play selected music
case "$choice" in
    "Pokemon")
        mpv "$pokemon_url"
        ;;
    "Stardew Valley")
        mpv "$stardew_url"
        ;;
    *)
        echo "No valid option selected."
        ;;
esac


```
