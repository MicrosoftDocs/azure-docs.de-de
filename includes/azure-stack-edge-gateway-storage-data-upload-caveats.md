---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: dc18efe03cbc8a3f657ae4afc781941e8e76611c
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96465673"
---
Die folgenden Einschränkungen gelten für Daten, die in Azure verschoben werden.

- Es wird empfohlen, dass nicht mehr als ein Gerät in den gleichen Container schreibt.
- Wenn Sie bereits über ein Azure-Objekt (z.B. Blob oder Datei) in der Cloud verfügen, das den gleichen Namen hat wie das Objekt, das kopiert wird, überschreibt das Gerät die Datei in der Cloud.
- Eine unter Freigabeordnern erstellte leere Verzeichnishierarchie (ohne jegliche Dateien) wird nicht in die Blobcontainer hochgeladen.
- Sie können die Daten mithilfe von Drag & Drop mit dem Datei-Explorer oder über die Befehlszeile kopieren. Wenn die Gesamtgröße der kopierten Dateien über 10GB liegt, sollten Sie ein Programm zum Massenkopieren wie z.B. Robocopy oder rsync verwenden. Die Tools zum Massenkopieren wiederholen den Kopiervorgang bei vorübergehenden Fehlern und bieten zusätzliche Resilienz. Wenn Sie Blob Storage über REST verwenden, können Sie AzCopy oder den Azure Storage-Explorer verwenden.
- Wenn die dem Azure-Speichercontainer zugeordnete Freigabe Blobs hochlädt, die nicht mit dem Typ der zum Zeitpunkt der Erstellung für die Freigabe definierten Blobs übereinstimmen, werden solche Blobs nicht aktualisiert. Sie erstellen z.B. eine Freigabe für Blockblobs auf dem Gerät. Ordnen Sie die Freigabe einem vorhandenen Cloudcontainer mit Seitenblobs zu. Aktualisieren Sie diese Freigabe zum Herunterladen der Dateien. Ändern Sie einige der aktualisierten Dateien, die in der Cloud bereits als Seitenblobs gespeichert sind. Es treten Uploadfehler auf.
- Das Umbenennen einer Datei, die in den Freigaben erstellt wurde, wird nicht unterstützt.
- Durch das Löschen einer Datei aus einer Freigabe wird der Eintrag im Speicherkonto nicht gelöscht.
- Wenn Sie Daten mithilfe von rsync kopieren, wird die Option `rsync -a` nicht unterstützt.
