---
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: include
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 95acb028867caf6f497dd99ad3082efcaab09c7b
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93325298"
---
Das Eingangsskript empfängt an einen bereitgestellten Webdienst übermittelte Daten und übergibt sie an das Modell. Anschließend nimmt es die vom Modell zurückgegebene Antwort entgegen und gibt diese an den Client zurück. *Das Skript ist auf Ihr Modell zugeschnitten*. In ihm muss die Struktur der vom Modell erwarteten und zurückgegebenen Daten bekannt sein.

Sie müssen die folgenden beiden Schritte in Ihrem Eingabeskript ausführen:

1. Laden des Modells (mit einer Funktion namens `init()`)
1. Ausführen des Modells für Eingabedaten (mit einer Funktion namens `run()`)

Wir gehen diese Schritte nun ausführlich durch.

### <a name="writing-init"></a>Schreiben von init() 

#### <a name="loading-a-registered-model"></a>Laden eines registrierten Modells

Die registrierten Modelle werden unter einem Pfad gespeichert, auf den von einer Umgebungsvariablen namens `AZUREML_MODEL_DIR` verwiesen wird. Weitere Informationen zur genauen Verzeichnisstruktur finden Sie unter [Suchen nach Modelldateien im Eingabeskript](../articles/machine-learning/how-to-deploy-advanced-entry-script.md#load-registered-models).

#### <a name="loading-a-local-model"></a>Laden eines lokalen Modells

Wenn Sie sich gegen die Registrierung Ihres Modells entschieden haben und das Modell als Teil Ihres Quellverzeichnisses übergeben haben, können Sie es wie lokal einlesen, indem Sie den Pfad zum Modell relativ zu Ihrem Bewertungsskript übergeben. Beispiel: Wenn Sie ein Verzeichnis mit folgender Struktur haben:

```bash

- source_dir
    - score.py
    - models
        - model1.onnx

```

können Sie Ihre Modelle mit dem folgenden Python-Code laden:

```python
import os

model = open(os.path.join('.', 'models', 'model1.onnx'))
```

### <a name="writing-run"></a>Schreiben von run()

`run()` wird jedes Mal ausgeführt, wenn das Modell eine Bewertungsanforderung empfängt, und erwartet, dass der Anforderungstext ein JSON-Dokument mit der folgenden Struktur ist:

```json
{
    "data": <model-specific-data-structure>
}

```

Die Eingabe in `run()` ist eine Python-Zeichenfolge, die alles enthält, was auf den Schlüssel „data“ folgt.

Das folgende Beispiel veranschaulicht, wie ein registriertes SciKit-learn-Modell geladen und mit numpy-Daten bewertet wird:

```python
import json
import numpy as np
import os
from sklearn.externals import joblib


def init():
    global model
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(data):
    try:
        data = np.array(json.loads(data))
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

Weiterführende Beispiele, einschließlich automatischer Swagger-Schemagenerierung und binärer Daten (d. h. Bilder), finden Sie im [Artikel zum Erstellen von erweiterten Eingabeskripts](../articles/machine-learning/how-to-deploy-advanced-entry-script.md).