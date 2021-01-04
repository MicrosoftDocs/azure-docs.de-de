---
title: 'Tutorial: Ausführen eines TensorFlow-Modells in Python – Custom Vision Service'
titleSuffix: Azure Cognitive Services
description: Ausführen eines TensorFlow-Modells in Python Dieser Artikel gilt nur für Modelle, die aus Bildklassifizierungsprojekten in den Maschinelles Sehen-Dienst exportiert wurden.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 11/23/2020
ms.author: pafarley
ms.custom: devx-track-python
ms.openlocfilehash: a47475ad55c5e6262dc8ba1a384d89b9721fd2e9
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95736424"
---
# <a name="tutorial-run-tensorflow-model-in-python"></a>Tutorial: Ausführen eines TensorFlow-Modells in Python

Nachdem Sie Ihr [TensorFlow-Modell](./export-your-model.md) aus Custom Vision Service exportiert haben, hilft Ihnen dieser Schnellstart dabei, das Modell lokal zu verwenden, um Bilder zu klassifizieren.

> [!NOTE]
> Dieses Tutorial betrifft nur Modelle, die aus Bildklassifizierungsprojekten exportiert wurden.

## <a name="prerequisites"></a>Voraussetzungen

Zum Verwenden des Tutorials müssen Sie die folgenden Schritte ausführen:

- Installieren Sie entweder Python 2.7+ oder Python 3.5+.
- Installieren Sie pip.

Anschließend müssen folgende Pakete installiert werden:

```bash
pip install tensorflow
pip install pillow
pip install numpy
pip install opencv-python
```

## <a name="load-your-model-and-tags"></a>Laden Ihrer Modelle und Tags

Die heruntergeladene ZIP-Datei enthält eine Datei namens _model.pb_ und eine Datei namens _labels.txt_. Diese Dateien stellen das trainierte Modell und die Klassifizierungsbezeichnungen dar. Laden Sie als ersten Schritt das Modell in Ihr Projekt. Fügen Sie den folgenden Code einem neuen Python-Skript hinzu:

```Python
import tensorflow as tf
import os

graph_def = tf.compat.v1.GraphDef()
labels = []

# These are set to the default names from exported models, update as needed.
filename = "model.pb"
labels_filename = "labels.txt"

# Import the TF graph
with tf.io.gfile.GFile(filename, 'rb') as f:
    graph_def.ParseFromString(f.read())
    tf.import_graph_def(graph_def, name='')

# Create a list of labels.
with open(labels_filename, 'rt') as lf:
    for l in lf:
        labels.append(l.strip())
```

## <a name="prepare-an-image-for-prediction"></a>Vorbereiten eines Bilds auf die Vorhersage

Für das Bild müssen ein paar Schritte ausgeführt werden, um es für die Vorhersage vorzubereiten. In diesen Schritten wird die Bildmanipulation imitiert, die während des Trainings ausgeführt wird:

### <a name="open-the-file-and-create-an-image-in-the-bgr-color-space"></a>Öffnen der Datei und Erstellen eines Bilds im BGR-Farbraum

```Python
from PIL import Image
import numpy as np
import cv2

# Load from a file
imageFile = "<path to your image file>"
image = Image.open(imageFile)

# Update orientation based on EXIF tags, if the file has orientation info.
image = update_orientation(image)

# Convert to OpenCV format
image = convert_to_opencv(image)
```

### <a name="handle-images-with-a-dimension-1600"></a>Verarbeiten von Bildern mit einer Dimension > 1600

```Python
# If the image has either w or h greater than 1600 we resize it down respecting
# aspect ratio such that the largest dimension is 1600
image = resize_down_to_1600_max_dim(image)
```

### <a name="crop-the-largest-center-square"></a>Zuschneiden des größten Quadrats in der Mitte

```Python
# We next get the largest center square
h, w = image.shape[:2]
min_dim = min(w,h)
max_square_image = crop_center(image, min_dim, min_dim)
```

### <a name="resize-down-to-256x256"></a>Verkleinern auf 256x256

```Python
# Resize that square down to 256x256
augmented_image = resize_to_256_square(max_square_image)
```

### <a name="crop-the-center-for-the-specific-input-size-for-the-model"></a>Zuschneiden des mittleren Bereichs auf die genaue Eingabegröße für das Modell

```Python
# Get the input size of the model
with tf.compat.v1.Session() as sess:
    input_tensor_shape = sess.graph.get_tensor_by_name('Placeholder:0').shape.as_list()
network_input_size = input_tensor_shape[1]

# Crop the center for the specified network_input_Size
augmented_image = crop_center(augmented_image, network_input_size, network_input_size)

```

### <a name="add-helper-functions"></a>Hinzufügen von Hilfsfunktionen

In den obenstehenden Schritten werden die folgenden Hilfsfunktionen verwendet:

```Python
def convert_to_opencv(image):
    # RGB -> BGR conversion is performed as well.
    image = image.convert('RGB')
    r,g,b = np.array(image).T
    opencv_image = np.array([b,g,r]).transpose()
    return opencv_image

def crop_center(img,cropx,cropy):
    h, w = img.shape[:2]
    startx = w//2-(cropx//2)
    starty = h//2-(cropy//2)
    return img[starty:starty+cropy, startx:startx+cropx]

def resize_down_to_1600_max_dim(image):
    h, w = image.shape[:2]
    if (h < 1600 and w < 1600):
        return image

    new_size = (1600 * w // h, 1600) if (h > w) else (1600, 1600 * h // w)
    return cv2.resize(image, new_size, interpolation = cv2.INTER_LINEAR)

def resize_to_256_square(image):
    h, w = image.shape[:2]
    return cv2.resize(image, (256, 256), interpolation = cv2.INTER_LINEAR)

def update_orientation(image):
    exif_orientation_tag = 0x0112
    if hasattr(image, '_getexif'):
        exif = image._getexif()
        if (exif != None and exif_orientation_tag in exif):
            orientation = exif.get(exif_orientation_tag, 1)
            # orientation is 1 based, shift to zero based and flip/transpose based on 0-based values
            orientation -= 1
            if orientation >= 4:
                image = image.transpose(Image.TRANSPOSE)
            if orientation == 2 or orientation == 3 or orientation == 6 or orientation == 7:
                image = image.transpose(Image.FLIP_TOP_BOTTOM)
            if orientation == 1 or orientation == 2 or orientation == 5 or orientation == 6:
                image = image.transpose(Image.FLIP_LEFT_RIGHT)
    return image
```

## <a name="classify-an-image"></a>Klassifizieren eines Bilds

Sobald das Bild als Tensor vorbereitet ist, kann es über das Modell für eine Vorhersage gesendet werden:

```Python

# These names are part of the model and cannot be changed.
output_layer = 'loss:0'
input_node = 'Placeholder:0'

with tf.compat.v1.Session() as sess:
    try:
        prob_tensor = sess.graph.get_tensor_by_name(output_layer)
        predictions = sess.run(prob_tensor, {input_node: [augmented_image] })
    except KeyError:
        print ("Couldn't find classification output layer: " + output_layer + ".")
        print ("Verify this a model exported from an Object Detection project.")
        exit(-1)
```

## <a name="display-the-results"></a>Anzeigen der Ergebnisse

Die Ergebnisse der Ausführung des Bildtensors über das Modell muss dann wieder den Bezeichnungen zugeordnet werden.

```Python
    # Print the highest probability label
    highest_probability_index = np.argmax(predictions)
    print('Classified as: ' + labels[highest_probability_index])
    print()

    # Or you can print out all of the results mapping labels to probabilities.
    label_index = 0
    for p in predictions:
        truncated_probablity = np.float64(np.round(p,8))
        print (labels[label_index], truncated_probablity)
        label_index += 1
```

## <a name="next-steps"></a>Nächste Schritte

Als Nächstes erfahren Sie, wie Sie Ihr Modell in einer mobilen Anwendung umschließen:
* [Use exported Tensorflow model in an Android application (Verwenden des exportierten Tensorflow-Modells in einer Android-Anwendung)](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)
* [Use exported CoreML model in a Swift iOS application (Verwenden des exportierten CoreML-Modells in einer Swift iOS-Anwendung)](https://go.microsoft.com/fwlink/?linkid=857726)
* [Use exported CoreML model in an iOS application with Xamarin (Verwenden des exportierten CoreML-Modells in einer iOS-Anwendung mit Xamarin)](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)