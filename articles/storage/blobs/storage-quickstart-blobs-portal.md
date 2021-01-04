---
title: 'Schnellstart: Erstellen eines Blobs mithilfe des Azure-Portals'
titleSuffix: Azure Storage
description: In dieser Schnellstartanleitung verwenden Sie das Azure-Portal im Objektspeicher (Blob). Anschließend verwenden Sie das Azure-Portal, um ein Blob in Azure Storage hochzuladen, ein Blob herunterzuladen und die Blobs in einem Container aufzulisten.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 10/19/2020
ms.author: tamram
ms.openlocfilehash: edadcc4025913052e048ea94d47cac253e4bcd1a
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95523330"
---
# <a name="quickstart-upload-download-and-list-blobs-with-the-azure-portal"></a>Schnellstart: Hochladen, Herunterladen und Auflisten von Blobs mit dem Azure-Portal

In dieser Schnellstartanleitung erfahren Sie, wie Sie über das [Azure-Portal](https://portal.azure.com/) einen Container in Azure Storage erstellen und Blockblobs in diesen Container hochladen bzw. daraus herunterladen.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

## <a name="create-a-container"></a>Erstellen eines Containers

Führen Sie die folgenden Schritte aus, um einen Container über das Azure-Portal zu erstellen:

1. Navigieren Sie im Azure-Portal zu Ihrem neuen Speicherkonto.
2. Scrollen Sie im linken Menü für das Speicherkonto zum Abschnitt **Blob-Dienst**, und wählen Sie **Container** aus.
3. Wählen Sie die Schaltfläche **+ Container**.
4. Geben Sie einen Namen für den neuen Container ein. Der Containername muss klein geschrieben werden, mit einem Buchstaben oder einer Zahl beginnen und darf nur Buchstaben, Zahlen und Bindestriche (-) enthalten. Weitere Informationen zu Container- und Blobnamen finden Sie unter [Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Benennen von Containern, Blobs und Metadaten und Verweisen auf diese).
5. Legen Sie die öffentliche Zugriffsebene für den Container fest. Die Standardebene ist **Private (no anonymous access)** (Privat (kein anonymer Zugriff)).
6. Wählen Sie **OK** aus, um den Container zu erstellen.

    :::image type="content" source="media/storage-quickstart-blobs-portal/create-container.png" alt-text="Screenshot: Erstellen eines Containers über das Azure-Portal":::

## <a name="upload-a-block-blob"></a>Hochladen eines Blockblobs

Blockblobs bestehen aus Datenblöcken, die zusammen ein Blob bilden. In den meisten Blob Storage-Szenarien werden Blockblobs verwendet. Blockblobs eignen sich perfekt zum Speichern von Text und Binärdaten in der Cloud. Hierzu zählen beispielsweise Dateien, Bilder und Videos. In dieser Schnellstartanleitung erfahren Sie, wie Sie Blockblobs verwenden.

Gehen Sie wie folgt vor, um über das Azure-Portal ein Blockblob in Ihren neuen Container hochzuladen:

1. Navigieren Sie im Azure-Portal zu dem Container, den Sie im vorherigen Abschnitt erstellt haben.
1. Wählen Sie den Container aus, um eine Liste mit den darin enthaltenen Blobs anzuzeigen. Da dieser Container neu ist, enthält er noch keine Blobs.
1. Wählen Sie die Schaltfläche **Hochladen** aus, um das Uploadblatt zu öffnen, und navigieren Sie in Ihrem lokalen Dateisystem zu einer Datei, um sie als Blockblob hochzuladen. Im Abschnitt **Erweitert** können optional weitere Einstellungen für den Uploadvorgang konfiguriert werden.

    :::image type="content" source="media/storage-quickstart-blobs-portal/upload-blob.png" alt-text="Screenshot: Hochladen eines Blobs von Ihrem lokalen Laufwerk über das Azure-Portal":::

1. Wählen Sie die Schaltfläche **Hochladen** aus, um das Blob hochzuladen.
1. Laden Sie auf diese Weise beliebig viele Blobs hoch. Wie Sie sehen, werden die neuen Blobs nun innerhalb des Containers aufgeführt.

## <a name="download-a-block-blob"></a>Herunterladen eines Blockblobs

Sie können ein Blockblob herunterladen, um es im Browser anzuzeigen oder in Ihrem lokalen Dateisystem zu speichern. Gehen Sie zum Herunterladen eines Blockblobs wie folgt vor:

1. Navigieren Sie zu der Liste mit den Blobs, die Sie im vorherigen Abschnitt hochgeladen haben.
1. Klicken Sie mit der rechten Maustaste auf den Blob, den Sie herunterladen möchten, und wählen Sie **Herunterladen** aus.

    :::image type="content" source="media/storage-quickstart-blobs-portal/download-blob.png" alt-text="Screenshot: Herunterladen eines Blobs über das Azure-Portal":::

## <a name="delete-a-block-blob"></a>Löschen eines Blockblobs

Führen Sie zum Löschen von Blobs über das Azure-Portal die folgenden Schritte aus:

1. Navigieren Sie im Azure-Portal zum Container.
1. Zeigen Sie die Liste der Blobs im Container an.
1. Verwenden Sie das Kontrollkästchen, um mindestens ein Blob in der Liste auszuwählen.
1. Wählen Sie die Schaltfläche **Löschen** aus, um die ausgewählten Blobs zu löschen.
1. Bestätigen Sie im Dialogfeld den Löschvorgang, und geben Sie an, ob Blobmomentaufnahmen ebenfalls gelöscht werden sollen.

:::image type="content" source="media/storage-quickstart-blobs-portal/delete-blobs.png" alt-text="Screenshot: Löschen von Blobs über das Azure-Portal":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie alle Ressourcen entfernen möchten, die Sie im Rahmen dieser Schnellstartanleitung erstellt haben, löschen Sie einfach den Container. Die Blobs im Container werden ebenfalls gelöscht.

So löschen Sie den Container:

1. Navigieren Sie im Azure-Portal zu der Liste mit den Containern in Ihrem Speicherkonto.
1. Wählen Sie den zu löschenden Container aus.
1. Wählen Sie die Schaltfläche **Mehr** ( **...** ) und dann **Löschen**.
1. Bestätigen Sie, dass Sie den Container löschen möchten.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie erfahren, wie Sie mit dem Azure-Portal einen Container erstellen und ein Blob hochladen. Informationen zum Verwenden von Blobspeicher über eine Web-App finden Sie im folgenden Tutorial, das zeigt, wie Sie Bilder in ein Speicherkonto hochladen:

> [!div class="nextstepaction"]
> [Tutorial: Hochladen von Bilddaten in die Cloud mit Azure Storage](storage-upload-process-images.md)