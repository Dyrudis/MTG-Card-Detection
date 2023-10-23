# MTG Card Detection
 
## Description
 
#### This project consists in the detection of a Magic: The Gathering card in a picture and give its name, id and whatever is needed as an  output.

- We first use `Scryfall` to download the json file containing all the cards and their informations:  
  - Get to `https://scryfall.com/docs/api/bulk-data` and download the first element : `Oracle Cards`.

  - Put the file in the `data` folder of this project.

  - Rename the file into `cards raw.json`.

- We then use the `cards raw processing.ipynb` file to reduce it to what we need for a faster processing.  

This needs to be done each time a new set is released to keep the cards database up to date.

