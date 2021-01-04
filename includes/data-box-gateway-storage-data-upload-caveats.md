---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 385ebffb4780543b532c81b89f1847f5c84cd0ac
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96581029"
---
Die folgenden Einschränkungen gelten für Daten, die in Azure verschoben werden.

- Es wird empfohlen, dass nicht mehr als ein Gerät in den gleichen Container schreibt.
- Wenn Sie bereits über ein Azure-Objekt (z. B. Blob oder Datei) in der Cloud verfügen, das den gleichen Namen hat wie das Objekt, das kopiert wird, überschreibt das Gerät die Datei in der Cloud.
- Eine unter Freigabeordnern erstellte leere Verzeichnishierarchie (ohne jegliche Dateien) wird nicht in die Blobcontainer hochgeladen.
- Sie können die Daten mithilfe von Drag & Drop mit dem Datei-Explorer oder über die Befehlszeile kopieren. Wenn die Gesamtgröße der kopierten Dateien über 10 GB liegt, sollten Sie ein Programm zum Massenkopieren wie z.B. `Robocopy` oder `rsync` verwenden. Die Tools zum Massenkopieren wiederholen den Kopiervorgang bei vorübergehenden Fehlern und bieten zusätzliche Resilienz.
- Wenn die dem Azure-Speichercontainer zugeordnete Freigabe Blobs hochlädt, die nicht mit dem Typ der zum Zeitpunkt der Erstellung für die Freigabe definierten Blobs übereinstimmen, werden solche Blobs nicht aktualisiert. Wenn Sie z. B. eine Blockblobfreigabe auf dem Gerät erstellen, die Freigabe einem vorhandenen Cloudcontainer zuordnen, der über Seitenblobs verfügt, diese Freigabe zum Herunterladen der Dateien aktualisieren und dann einige der aktualisierten Dateien ändern, die bereits als Seitenblobs in der Cloud gespeichert sind, werden Fehlermeldungen bezüglich des Hochladens angezeigt.
- Das Umbenennen einer Datei, die in den Freigaben erstellt wurde, wird nicht unterstützt.
- Durch das Löschen einer Datei aus einer Freigabe wird der Eintrag im Speicherkonto nicht gelöscht.
- Wenn Sie Daten mithilfe von `rsync` kopieren, wird die Option `rsync -a` nicht unterstützt.