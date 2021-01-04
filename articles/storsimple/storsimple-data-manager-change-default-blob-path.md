---
title: Ändern eines Blobpfads von der Standardeinstellung
description: Hier erfahren Sie, wie Sie eine Azure-Funktion einrichten, die einen Standardpfad für eine Blobdatei umbenennt, und anschließend Blobs an einen anderen Speicherort verschieben.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 6af095c7abdb9aa61e57d543ff2ab2f9192dadc8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011437"
---
# <a name="change-a-blob-path-from-the-default-path"></a>Ändern eines Blobpfads von der Standardeinstellung

Wenn der StorSimple Data Manager-Dienst die Daten transformiert, werden die transformierten Blobs standardmäßig in einem Speichercontainer erstellt, wie bei der Erstellung des Zielrepositorys angegeben. Wenn Blobs dort empfangen werden, empfiehlt es sich, diese Blobs an einen anderen Speicherort zu verschieben. Dieser Artikel beschreibt, wie Sie eine Azure-Funktion einrichten, die einen Standardpfad für eine Blobdatei umbenennt und anschließend Blobs an einen anderen Speicherort verschiebt.

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie sicher, dass Sie über eine ordnungsgemäß konfigurierte Auftragsdefinition in Ihrem StorSimple Data Manager-Dienst verfügen.

## <a name="create-an-azure-function"></a>Erstellen einer Azure-Funktion

Führen Sie folgenden Schritte aus, um eine Azure-Funktion zu erstellen:

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/).

2. Klicken Sie auf **+ Ressource erstellen**. Geben Sie im Feld **Suchen** den Text **Funktionen-App** ein, und drücken Sie die **EINGABETASTE**. Wählen Sie in der angezeigten Liste der Apps **Funktions-App** aus, und klicken Sie darauf.

    ![Geben Sie im Suchfeld „Funktionen-App“ ein.](./media/storsimple-data-manager-change-default-blob-path/search-function-app.png)

3. Klicken Sie auf **Erstellen**.

    ![Die Schaltfläche „Erstellen“ im Fenster „Funktionen-App“](./media/storsimple-data-manager-change-default-blob-path/create-function-app.png)

4. Führen Sie auf dem Konfigurationsblatt **Funktionen-App** die folgenden Schritte aus:

    1. Geben Sie einen eindeutigen **App-Namen** an.
    2. Wählen Sie in der Dropdownliste die Option **Abonnement** aus. Bei diesem Abonnement sollte es sich um das Ihrem StorSimple Data Manager-Dienst zugeordnete handeln.
    3. Wählen Sie die Option **Neu erstellen** für die Ressourcengruppe aus.
    4. Wählen Sie in der Dropdownliste **Hostingplan** die Option **Verbrauchsplan** aus.
    5. Geben Sie einen Speicherort an, an dem die Funktion ausgeführt wird. Es sollte sich um dieselbe Region handeln, in der sich der StorSimple Data Manager-Dienst und das Speicherkonto, das der Auftragsdefinition zugeordnet ist, befinden.
    6. Wählen Sie ein vorhandenes Speicherkonto aus, oder erstellen Sie ein neues. Ein Speicherkonto wird intern für die Funktion verwendet.

        ![Neue Funktionen-App-Konfigurationsdaten eingeben](./media/storsimple-data-manager-change-default-blob-path/function-app-parameters.png)

    7. Klicken Sie auf **Erstellen**. Die Funktionen-App wird erstellt.
     
        ![Erstellte Funktionen-App](./media/storsimple-data-manager-change-default-blob-path/function-app-created.png)

5. Wählen Sie **Funktionen** aus, und klicken Sie auf **+ Neue Funktion**.

    ![Klicken auf „+ Neue Funktion“](./media/storsimple-data-manager-change-default-blob-path/create-new-function.png)

6. Wählen Sie **C#** als Sprache aus. Wählen Sie im Raster der Vorlagenkacheln die Option **C#** auf der Kachel **QueueTrigger-CSharp** aus.

7. Gehen Sie unter **Queue Trigger** (Warteschlangentrigger) folgendermaßen vor:

    1. Geben Sie einen **Namen** für Ihre Funktion ein.
    2. Geben Sie im Feld **Warteschlangenname** den Namen für die Auftragsdefinition zur Datentransformation ein.
    3. Klicken Sie unter **Speicherkontoverbindung** auf **Neu**. Wählen Sie in der Liste der Speicherkonten das Konto aus, das Ihrer Auftragsdefinition zugeordnet ist. Notieren Sie sich den Verbindungsnamen (markiert). Der Name wird später in der Azure-Funktion benötigt.

        ![Erstellen Sie eine neue C#-Funktion.](./media/storsimple-data-manager-change-default-blob-path/new-function-parameters.png)

    4. Klicken Sie auf **Erstellen**. Die **Funktion** wird erstellt.

     
10. Führen Sie im Fenster der Funktion die _CSX_-Datei aus.

    ![Erstellen einer neuen C#-Funktion 2](./media/storsimple-data-manager-change-default-blob-path/new-function-run-csx.png)
    
    Führen Sie die folgenden Schritte aus:

    1. Fügen Sie folgenden Code ein:

        ```
        using System;
        using System.Configuration;
        using Microsoft.WindowsAzure.Storage.Blob;
        using Microsoft.WindowsAzure.Storage.Queue;
        using Microsoft.WindowsAzure.Storage;
        using System.Collections.Generic;
        using System.Linq;

        public static void Run(QueueItem myQueueItem, TraceWriter log)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["STORAGE_CONNECTIONNAME"]);

            string storageAccUriEndswith = "windows.net/";
            string uri = myQueueItem.TargetLocation.Replace("%20", " ");
            log.Info($"Blob Uri: {uri}");

            // Remove storage account uri string
            uri = uri.Substring(uri.IndexOf(storageAccUriEndswith) + storageAccUriEndswith.Length);

            string containerName = uri.Substring(0, uri.IndexOf("/")); 

            // Remove container name string
            uri = uri.Substring(containerName.Length + 1);

            // Current blob path
            string blobName = uri; 

            string volumeName = uri.Substring(containerName.Length + 1);
            volumeName = uri.Substring(0, uri.IndexOf("/"));

            // Remove volume name string
            uri = uri.Substring(volumeName.Length + 1);

            string newContainerName = uri.Substring(0, uri.IndexOf("/")).ToLower();
            string newBlobName = uri.Substring(newContainerName.Length + 1);

            log.Info($"Container name: {containerName}");
            log.Info($"Volume name: {volumeName}");
            log.Info($"New container name: {newContainerName}");

            log.Info($"Blob name: {blobName}");
            log.Info($"New blob name: {newBlobName}");

            // Create the blob client.
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

            // Container reference
            CloudBlobContainer container = blobClient.GetContainerReference(containerName);
            CloudBlobContainer newContainer = blobClient.GetContainerReference(newContainerName);
            newContainer.CreateIfNotExists();

            if(!container.Exists())
            {
                log.Info($"Container - {containerName} not exists");
                return;
            }

            if(!newContainer.Exists())
            {
                log.Info($"Container - {newContainerName} not exists");
                return;
            }

            CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
            if (!blob.Exists())
            {
                // Skip to copy the blob to new container, if source blob doesn't exist
                log.Info($"The specified blob does not exist.");
                log.Info($"Blob Uri: {blob.Uri}");
                return;
            }

            CloudBlockBlob blobCopy = newContainer.GetBlockBlobReference(newBlobName);
            if (!blobCopy.Exists())
            {
                blobCopy.StartCopy(blob);
                // Delete old blob, after copy to new container
                blob.DeleteIfExists();
                log.Info($"Blob file path renamed completed successfully");
            }
            else
            {
                log.Info($"Blob file path renamed already done");
                // Delete old blob, if already exists.
                blob.DeleteIfExists();
            }
        }

        public class QueueItem
        {
            public string SourceLocation {get;set;}
            public long SizeInBytes {get;set;}
            public string Status {get;set;}
            public string JobID {get;set;}
            public string TargetLocation {get; set;}
        }

        ```

    2. Ersetzen Sie **STORAGE_CONNECTIONNAME** in Zeile 11 durch Ihre Speicherkontoverbindung (siehe Schritt 7c).

        ![Kopieren des Speicherverbindungsnamens](./media/storsimple-data-manager-change-default-blob-path/new-function-storage-connection-name.png)

    3. **Speichern** Sie die Funktion.

        ![Funktion speichern](./media/storsimple-data-manager-change-default-blob-path/save-function.png)

12. Um die Funktion zu vervollständigen, fügen Sie über die folgenden Schritte eine weitere Datei hinzu:

    1. Klicken Sie auf **Dateien anzeigen**.

       ![Link „Dateien anzeigen“](./media/storsimple-data-manager-change-default-blob-path/view-files.png)

    2. Klicken Sie auf **+ Hinzufügen**.
        
        ![Hinzufügen einer neuen Funktion/einer Datei](./media/storsimple-data-manager-change-default-blob-path/new-function-add-file.png)
    
    3. Geben Sie **project.json** ein, und drücken Sie dann die **EINGABETASTE**. Fügen Sie folgenden Code in die Datei **project.json** ein:

        ```
        {
        "frameworks": {
            "net46":{
            "dependencies": {
                "windowsazure.storage": "8.1.1"
            }
            }
        }
        }

        ```

    
    4. Klicken Sie auf **Speichern**.

        ![Neues Funktionsprojekt (JSON)](./media/storsimple-data-manager-change-default-blob-path/new-function-project-json.png)

Sie haben eine Azure-Funktion erstellt. Diese Funktion wird jedes Mal ausgelöst, wenn vom Datentransformationsauftrag ein neues Blob generiert wird.

## <a name="next-steps"></a>Nächste Schritte

[Use StorSimple Data Manager UI to transform your data (Verwenden der StorSimple Data Manager-UI zum Transformieren von Daten)](storsimple-data-manager-ui.md)
