# MTG Card Detection

## Description

This project consists in the detection of a Magic: The Gathering card in a picture and give its name, id and whatever is needed as an  output

## How to update the dataset

- We first use `Scryfall` to download the json file containing all the cards and their informations:  
  - Get to `https://scryfall.com/docs/api/bulk-data` and download the first element : `Oracle Cards`.

  - Put the file in the `data` folder of this project.

  - Rename the file into `cards raw.json`.

- We then use the `cards raw processing.ipynb` file to reduce it to what we need for a faster processing.  

This needs to be done each time a new set is released to keep the cards database up to date.

## Tesseract download for OCR

In order to execute the `card detection.ipynb` file, you need to install `Tesseract` on your computer.

- Download the `Tesseract` OCR engine from `https://github.com/UB-Mannheim/tesseract/wiki` and follow the instructions to install it.

- Update the path to the `tesseract.exe` file path in the `card detection.ipynb` file.

## Explanation

### Approach

Our first intuition was to create a CNN to classify the cards, but since there are nearly 50,000 different cards, we decided to go for another approach.

We therefore chose to use text detection, then to search among the existing cards to find the corresponding card. This solution is divided into several steps:

1. Transformation of the perspective of the image to obtain an image of the card facing
2. Text detection
3. Search for the corresponding card

### Perspective transformation

To transform the perspective of the image, we used the `warpPerspective` method of the OpenCV library. This method takes as parameters the original image, the coordinates of the 4 corners of the card and the size of the output image. The coordinates of the 4 corners of the card are obtained using the `findContours` method of the OpenCV library. Here is an overview of the perspective transformation:

![Before transformation](https://i.imgur.com/DPeg0D9.png)
![After transformation](https://i.imgur.com/SDQivDl.png)

### Text detection

For text detection, we used the Tesseract library. This library allows you to detect text in an image and transcribe it. We used the `image_to_string` method of the Tesseract library to get the text of the card. Here is an overview of the text detection for the previous card:

```plain
ombre de hyene

enchantement  aura

enchanter  creature
la creature enchantee gagne 11 et
a initiative

armure totemique si la creature
enchantee devait etre detruite a la place
retirezlut toutes ses blessures et detruisez
cette aura
```

As we can see, the detection is not perfect, which is why we will use a similarity search method to find the corresponding card.

### Search for the corresponding card

For the search of the corresponding card, we needed a dataset of the names and description of all the cards. We found it on Scryfall and we modified it to obtain a JSON file with the following fields for all existing cards:

- `id`: the id of the card
- `name`: the name of the card
- `text`: the description of the card
- `uri`: the url of the card on Scryfall

Then, we created a TF-IDF (with the sklearn library) on the texts of the cards. Then we used the `cosine_similarity` method of the scikit-learn library to find the most similar cards. Here is an example of the result:

```plain
1: Ombre de hyène - https://scryfall.com/card/2x2/13/fr/ombre-de-hy%C3%A8ne?utm_source=api
2: Ombre de drakôn - https://scryfall.com/card/roe/63/fr/ombre-de-drak%C3%B4n?utm_source=api
3: Ombre de mammouth - https://scryfall.com/card/roe/37/fr/ombre-de-mammouth?utm_source=api
4: Ombre de sanglier - https://scryfall.com/card/pc2/58/fr/ombre-de-sanglier?utm_source=api
5: Rise of the Witch-king - https://scryfall.com/card/ltr/221/fr/rise-of-the-witch-king?utm_source=api
```
