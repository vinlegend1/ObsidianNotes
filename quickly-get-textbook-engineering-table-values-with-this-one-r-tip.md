+++
title = 'Quickly Get Textbook Engineering Table Values With This One R Tip'
date = 2024-03-04T09:59:41+08:00
draft = false
+++

## Step 1: Grab the PDF copy of the table you're using
- Grab it and if it's directly from the book, you can extract the specific pages `pdfsplit` and `pdfunite`
- or use a site that extracts the specific pages you need
- or use a GUI application like [pdfarranger](https://github.com/pdfarranger/pdfarranger)

## Step 2: Convert PDF to CSV
- go to a site that converts pdf to csv, any will do
- after getting the csv, edit it in a spreadsheet program and rename the header values as needed

## Step 3: Read CSV in R and approx()
- to do linear interpolation and import that table in csv

- an example of this using the Tables for Transient Heat transfer
```R
table <- read.csv("Table for Transient Heat Transfer.csv")
Bia = h * D / (2*k)
c_1 <- approx(table$bi, table$c_1_cylinder, xout=Bia)$y
zeta_1 <- approx(table$bi, table$zeta_cylinder, xout=Bia)$y
```

the first parameter is x, in this case `table$bi`.
second is y
third is explicitly stated to be `xout` which is a variable that essentially says:

$$f(xout = ?) = ?$$

you need to add a `$y` at the end to access only the "regressor" value or the y value
the output of the `approx()` function is a vector with x and y components

lastly, store the value of the approximation in a variable
