---
tags:
  - bash
---
## Screenshots
![[Pasted image 20241023141724.png]]
![[Pasted image 20241023141821.png]]
## Code
```bash
#!/bin/bash

# Define your input CSV file and image
csv_file="members.csv"
input_image="member.png"
output_folder="members_cards"
barcode_folder="barcodes"
temp_folder="temp"
font_path="$HOME/.fonts/AnekLatin_Expanded-ExtraBold.ttf"

# Create output folder if it doesn't exist
mkdir -p "$output_folder"
mkdir -p "$temp_folder"
mkdir -p "$barcode_folder"

# Define the size of the image (in inches) and DPI (resolution)
image_width=1769
image_height=1116

# Position for the top-left text (0.2 inches from left and top)
top_left_x=$(echo "$image_width / 12" | bc)
top_left_y=$(echo "$image_width / 12" | bc)

# Position for the bottom-left text (1.69 inches from left and 0.08 inches from bottom)
bottom_left_x=$(echo "$image_width / 2" | bc)
bottom_left_y=$(echo "$image_height * 0.92" | bc)

# Read CSV file and process each row
while IFS=, read -r mem_number surname first_name middle_name
do
    # Output image file name (you can customize it)
    read first second  <<< "$first_name"
    output_image="$output_folder/$surname $first.png"
    if [ "$second" == "" ]; then
        if [ "$middle_name" == "N/A" ]; then
            filltext="$first\n$surname"
        else
            filltext="$first $middle_name\n$surname"
        fi
    else
        if [ "$middle_name" == "N/A" ]; then
            filltext="$first\n$second\n$surname"
        else
            filltext="$first\n$second $middle_name\n$surname"
        fi
    fi

    # Use ImageMagick to add text at the specified positions
    magick "$input_image" \
        -font "$font_path" -pointsize 72 -fill white \
        -draw "text $top_left_x,$top_left_y '$filltext'" \
        "$temp_folder/$mem_number.png"

    zint --output="$barcode_folder/$mem_number.png" \
        --barcode=20 \
        --data="$mem_number" \
        --fg=FFFFFF \
        --bg=FFFFFF00

    # Rotate the barcode image by 90 degrees and overlay it onto the base image
    magick "$temp_folder/$mem_number.png" \
        \( "$barcode_folder/$mem_number.png" -rotate -90 -resize 100x \) \
        -geometry "+$(echo "$image_width * 0.9 + 32" | bc)+$(echo "$image_height / 2 + 120" | bc)" \
        -composite "$output_image"

    echo "Created $output_image"
done < "$csv_file"


```

```bash
#!/bin/bash

# Define your input CSV file and image
csv_file="officers.csv"
input_image="officer.png"
output_folder="officers_cards"
font_path="$HOME/.fonts/AnekLatin_Expanded-ExtraBold.ttf"
sub_path="$HOME/.fonts/Aileron-Regular.otf"
temp_folder="temp"
barcode_folder="barcodes"

# Create output folder if it doesn't exist
mkdir -p "$output_folder"
mkdir -p "$temp_folder"
mkdir -p "$barcode_folder"

# Define the size of the image (in inches) and DPI (resolution)
image_width=1769
image_height=1116

# Position for the top-left text (0.2 inches from left and top)
top_left_x=$(echo "$image_width / 12" | bc)
top_left_y=$(echo "$image_width / 12" | bc)

# Position for the bottom-left text (1.69 inches from left and 0.08 inches from bottom)
bottom_left_x=$(echo "$image_width / 2 + 8" | bc)
bottom_left_y=$(echo "$image_height * 0.9 - 12" | bc)

# Read CSV file and process each row
while IFS=, read -r mem_number surname first_name middle_name position division
do
    # Output image file name (you can customize it)
    read first second  <<< "$first_name"
    output_image="$output_folder/$surname $first.png"
    if [ "$second" == "" ]; then
        if [ "$middle_name" == "N/A" ]; then
            nametext="$first\n$surname"
        else
            nametext="$first $middle_name\n$surname"
        fi
    else
        if [ "$middle_name" == "N/A" ]; then
            nametext="$first\n$second\n$surname"
        else
            nametext="$first\n$second $middle_name\n$surname"
        fi
    fi

    if [ "$position" == "PRESIDENT" ]; then
        positiontext="President"
    elif [ "$position" == "REGULAR OFFICER" ]; then
        positiontext="$division Officer"
    else
        positiontext="$position $division"
    fi

    # Use ImageMagick to add text at the specified positions
    magick "$input_image" \
        -font "$font_path" -pointsize 72 \
        -draw "text $top_left_x,$top_left_y '$nametext'" \
        -font "$sub_path" -pointsize 32 -fill "#BA181B" \
        -draw "text $bottom_left_x,$bottom_left_y '$positiontext'" \
        "$temp_folder/$mem_number.png"

    zint --output="$barcode_folder/$mem_number.png" \
        --barcode=20 \
        --data="$mem_number" \
        --fg=BA181B \
        --bg=FFFFFF00

    # Rotate the barcode image by 90 degrees and overlay it onto the base image
    magick "$temp_folder/$mem_number.png" \
        \( "$barcode_folder/$mem_number.png" -rotate -90 -resize 100x \) \
        -geometry "+$(echo "$image_width * 0.9 + 32" | bc)+$(echo "$image_height / 2 + 120" | bc)" \
        -composite "$output_image"

    echo "Created $output_image"
done < "$csv_file"

```