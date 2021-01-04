---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: mimart
ms.openlocfilehash: d43b879057001d62ea72bd2e011ad52957d47470
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95990883"
---
## <a name="sample-templates"></a>Beispielvorlagen
Beispielvorlagen für die Benutzeroberflächenanpassung finden Sie hier:

```bash
git clone https://github.com/Azure-Samples/Azure-AD-B2C-page-templates
```

Dieses Projekt enthält die folgenden Vorlagen:
- [Ozeanblau](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/ocean_blue)
- [Schiefergrau](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/slate_gray)

So verwenden Sie das Beispiel

1. Klonen Sie das Repository auf Ihrem lokalen Computer. Wählen Sie den Vorlagenordner `/ocean_blue` oder `/slate_gray` aus.
1. Laden Sie alle Dateien aus dem Vorlagenordner und dem Ordner `/assets` in den Blob-Speicher hoch, wie in den vorherigen Abschnitten beschrieben.
1. Öffnen Sie als Nächstes jede `\*.html`-Datei im Stammverzeichnis von `/ocean_blue` bzw. `/slate_gray`, und ersetzen Sie alle Vorkommen relativer URLs durch die URLs in den CSS-, Bild- und Schriftartdateien, die Sie in Schritt 2 hochgeladen haben. Beispiel:
    ```html
    <link href="./css/assets.css" rel="stylesheet" type="text/css" />
    ```

    To
    ```html
    <link href="https://your-storage-account.blob.core.windows.net/your-container/css/assets.css" rel="stylesheet" type="text/css" />
    ```
1. Speichern Sie die `\*.html`-Dateien, und laden Sie sie in den Blob-Speicher hoch.
1. Ändern Sie nun die Richtlinie, die auf Ihre HTML-Datei verweist, wie zuvor erwähnt.
1. Falls Schriftarten, Bilder oder CSS-Dateien fehlen, überprüfen Sie die Verweise in der Erweiterungsrichtlinie und die HTML-Dateien (\*.html).
