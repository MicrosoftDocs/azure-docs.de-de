---
title: Erstellen eines Trainingsdatasets für ein benutzerdefiniertes Modell – Formularerkennung
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie sicherstellen, dass Ihr Trainingsdataset für das Trainieren eines Formularerkennungsmodells optimiert ist.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: pafarley
ms.openlocfilehash: ee57ccb82e771ee8ab93b09e476a94df32278069
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99585108"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>Erstellen eines Trainingsdatasets für ein benutzerdefiniertes Modell

Wenn Sie das benutzerdefinierte Modell zur Formularerkennung verwenden, stellen Sie dem Vorgang [Trainieren eines benutzerdefinierten Modells](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync) Ihre eigenen Trainingsdaten bereit, sodass das Modell mit Ihren branchenspezifischen Formularen trainiert werden kann. Folgen Sie diesem Leitfaden, um zu erfahren, wie Daten für ein effektives Training des Modells gesammelt und vorbereitet werden können.

Sie benötigen mindestens fünf ausgefüllte Formulare desselben Typs.

Wenn Sie manuell bezeichnete Trainingsdaten verwenden möchten, müssen Sie mit mindestens fünf ausgefüllten Formularen desselben Typs beginnen. Sie können trotzdem nicht bezeichnete Formulare zusätzlich zum erforderlichen Dataset verwenden.

## <a name="custom-model-input-requirements"></a>Eingabeanforderungen für benutzerdefinierte Modelle

Stellen Sie zunächst sicher, dass Ihr Trainingsdataset den Eingabeanforderungen für die Formularerkennung entspricht.

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="training-data-tips"></a>Tipps zu Trainingsdaten

Befolgen Sie diese zusätzlichen Tipps, um Ihr Dataset für das Training weiter zu optimieren.

* Verwenden Sie nach Möglichkeit textbasierte PDF-Dokumente anstelle von bildbasierten Dokumenten. Gescannte PDF-Dateien werden als Bilder behandelt.
* Verwenden Sie als ausgefüllte Formulare Exemplare, in denen alle Felder ausgefüllt sind.
* Verwenden Sie Formulare mit verschiedenen Werten in jedem Feld.
* Wenn Ihre Formularbilder eine mäßige Qualität aufweisen, verwenden Sie ein größeres Dataset (beispielsweise 10–15 Bilder).

## <a name="upload-your-training-data"></a>Hochladen Ihrer Trainingsdaten

Wenn Sie den Satz von Formulardokumenten, die Sie für das Training verwenden möchten, zusammengestellt haben, müssen Sie ihn in einen Azure Blob Storage-Container hochladen. Wenn Sie nicht wissen, wie Sie ein Azure Storage-Konto mit einem Container erstellen, folgen Sie den Anweisungen im [Azure Storage-Schnellstart für das Azure-Portal](../../storage/blobs/storage-quickstart-blobs-portal.md). Verwenden Sie die Standardleistungsstufe.

Wenn Sie manuell bezeichnete Daten verwenden möchten, müssen Sie auch die Dateien *.labels.json* und *.ocr.json* hochladen, die den Trainingsdokumenten entsprechen. Sie können das [Tool für die Beschriftung von Beispielen](./quickstarts/label-tool.md) (oder Ihre eigene Benutzeroberfläche) verwenden, um diese Dateien zu generieren.

### <a name="organize-your-data-in-subfolders-optional"></a>Organisieren Ihrer Daten in Unterordnern (optional)

Standardmäßig werden von der [Train Custom Model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync)-API nur Formulardokumente verwendet, die sich im Stammverzeichnis Ihres Speichercontainers befinden. Sie können jedoch mit Daten in Unterordnern trainieren, wenn Sie dies im API-Aufruf angeben. Normalerweise hat der Text des [Train Custom Model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync)-Aufrufs das folgende Format, wobei `<SAS URL>` die SAS-URL (Shared Access Signature) des Containers ist:

```json
{
  "source":"<SAS URL>"
}
```

Wenn Sie dem Anforderungstext den folgenden Inhalt hinzufügen, wird die API mit Dokumenten trainiert, die sich in Unterordnern befinden. Das Feld `"prefix"` ist optional und schränkt das Trainingsdataset auf Dateien ein, deren Pfad mit der angegebenen Zeichenfolge beginnt. So bewirkt beispielsweise der Wert `"Test"`, dass die API nur Dateien oder Ordner berücksichtigt, die mit dem Wort „Test“ beginnen.

```json
{
  "source": "<SAS URL>",
  "sourceFilter": {
    "prefix": "<prefix string>",
    "includeSubFolders": true
  },
  "useLabelFile": false
}
```

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun gelernt haben, wie Sie ein Trainingsdataset erstellen, folgen Sie den Anweisungen in einem Schnellstart, um ein benutzerdefiniertes Formularerkennungsmodell zu trainieren und es mit Ihren Formularen zu verwenden.

* [Schnellstart: Trainieren eines Modells zur Formularerkennung und Extrahieren von Formulardaten unter Verwendung der Clientbibliothek oder REST-API](./quickstarts/client-library.md)
* [Trainieren eines Formularerkennungsmodells mit Beschriftungen mithilfe des Tools für die Beschriftung von Beispielen](./quickstarts/label-tool.md)

## <a name="see-also"></a>Weitere Informationen

* [Was ist die Formularerkennung?](./overview.md)