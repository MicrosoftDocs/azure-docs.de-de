---
title: Verwalten von Azure Data Lake Storage Gen1-Ressourcen in Azure Storage-Explorer
description: Es wird beschrieben, wie Sie auf Ihre Azure Data Lake Storage Gen1-Daten und Ressourcen in Azure Storage-Explorer zugreifen und diese verwalten.
Keywords: Azure Data Lake Store, Azure Storage-Explorer
services: Data Lake Store
documentationcenter: ''
author: jejiang
manager: DJ
editor: Jenny Jiang
ms.assetid: ''
ms.service: data-lake-store
ms.custom: Azure Data Lake Store
ms.devlang: dotnet
ms.topic: article
ms.date: 02/05/2018
ms.author: jejiang
ms.openlocfilehash: 18ef1f182611a9c8fbf24cd08026633f5449bbe9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60613536"
---
# <a name="manage-azure-data-lake-storage-gen1-resources-by-using-storage-explorer"></a>Verwalten von Azure Data Lake Storage Gen1-Ressourcen mit Storage-Explorer

[Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) ist ein Dienst zum Speichern großer Mengen von unstrukturierten Daten, z.B. als Text- oder Binärdaten. Sie können per HTTP oder HTTPS von jedem Ort aus auf die Daten zugreifen. Mit Data Lake Storage Gen1 in Azure Storage-Explorer können Sie auf Data Lake Storage Gen1-Daten und -Ressourcen sowie andere Azure-Entitäten wie Blobs und Warteschlangen zugreifen und diese verwalten. Nun können Sie mit demselben Tool Ihre verschiedenen Azure-Entitäten an einem Ort verwalten.

Ein weiterer Vorteil besteht darin, dass Sie keine Abonnementberechtigung zum Verwalten von Data Lake Storage Gen1-Daten benötigen. In Storage-Explorer können Sie den Data Lake Storage Gen1-Pfad an den Knoten **Local and Attached** (Lokal und angefügt) anfügen, sofern Ihnen die Berechtigung gewährt wird.

## <a name="prerequisites"></a>Voraussetzungen
Damit Sie die in diesem Artikel aufgeführten Schritte ausführen können, benötigen Sie Folgendes:

*   Ein Azure-Abonnement. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial).
*   Ein Data Lake Storage Gen1-Konto. Eine Anleitung zur Erstellung finden Sie unter [Erste Schritte mit Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

## <a name="install-storage-explorer"></a>Installieren von Storage-Explorer

Installieren Sie die neuesten Azure Storage-Explorer-Komponenten von der [Produktwebseite](https://azure.microsoft.com/features/storage-explorer/). Die Installation unterstützt Windows-, Linux- und Macintosh-Versionen.

## <a name="connect-to-an-azure-subscription"></a>Herstellen einer Verbindung mit einem Azure-Abonnement

1. Wähen Sie in Storage-Explorer links das Plug-In-Symbol.
       
   ![Plug-In-Symbol](./media/data-lake-store-in-storage-explorer/plug-in-icon.png)
 
2. Wählen Sie **Azure-Konto hinzufügen** und dann **Anmelden**.

   ![Dialogfeld „Verbindung mit Azure-Speicher herstellen“](./media/data-lake-store-in-storage-explorer/connect-to-azure-subscription.png)

2. Geben Sie im Dialogfeld **Bei Ihrem Konto anmelden** Ihre Azure-Anmeldeinformationen ein.

    ![Dialogfeld für Azure-Anmeldung](./media/data-lake-store-in-storage-explorer/sign-in.png)

3. Wählen Sie Ihr Abonnement in der Liste aus, und wählen Sie anschließend **Übernehmen**.

    ![Abonnementinformationen und Schaltfläche „Übernehmen“](./media/data-lake-store-in-storage-explorer/apply-subscription.png)

    Der Bereich **EXPLORER** wird aktualisiert, und die Konten im ausgewählten Abonnement werden angezeigt.

    ![Kontoliste](./media/data-lake-store-in-storage-explorer/account-list.png)

Sie haben die Data Lake Storage Gen1-Instanz nun mit Ihrem Azure-Abonnement verbunden.

## <a name="connect-to-data-lake-storage-gen1"></a>Herstellen der Verbindung mit Data Lake Storage Gen1
Sie können auf Ressourcen zugreifen, die in Ihrem Abonnement nicht vorhanden sind, wenn Sie den URI für die Ressourcen erhalten. Anschließend können Sie eine Verbindung mit Data Lake Storage Gen1 herstellen, indem Sie nach der Anmeldung den URI verwenden.
1. Öffnen Sie den Storage-Explorer.
2. Erweitern Sie im linken Bereich die Option **Local and Attached** (Lokal und angefügt).
3. Klicken Sie mit der rechten Maustaste auf **Data Lake Store**, und wählen Sie die Option **Connect to Data Lake Store** (Verbindung mit Data Lake Store herstellen).

      ![Option „Connect to Data Lake Store“ (Verbindung mit Data Lake Store herstellen) im Kontextmenü](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach.png)

4. Geben Sie den URI ein. Das Tool greift auf den URL-Speicherort zu, den Sie gerade eingegeben haben.

      ![Dialogfeld „Connect to Data Lake Store“ (Verbindung mit Data Lake Store herstellen) mit dem Textfeld für die URI-Eingabe](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

      ![Ergebnis der Verbindungsherstellung mit Data Lake Storage Gen1](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-attach-finish.png)

## <a name="view-a-data-lake-storage-gen1-accounts-contents"></a>Anzeigen des Inhalts eines Data Lake Storage Gen1-Kontos
Zu den Ressourcen eines Data Lake Storage Gen1-Kontos gehören Ordner und Dateien.

Die folgenden Schritte veranschaulichen, wie Sie den Inhalt eines Data Lake Storage Gen1-Kontos in Storage-Explorer anzeigen:

1. Öffnen Sie den Storage-Explorer.
2. Erweitern Sie im linken Bereich das Abonnement mit dem Data Lake Storage Gen1-Konto, das Sie anzeigen möchten.
3. Erweitern Sie **Data Lake Store**.
4. Klicken Sie mit der rechten Maustaste auf den Data Lake Storage Gen1-Kontoknoten, den Sie anzeigen möchten, und wählen Sie **Öffnen**. Sie können auch auf das Data Lake Storage Gen1-Konto doppelklicken, um es zu öffnen. 
   
   Im Hauptfenster wird der Inhalt des Data Lake Storage Gen1-Kontos angezeigt.

   ![Hauptfenster mit einer Ordnerliste](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-toolbar-mainpane.png) 

## <a name="manage-resources-in-data-lake-storage-gen1"></a>Verwalten von Ressourcen in Data Lake Storage Gen1

Sie können Data Lake Storage Gen1-Ressourcen verwalten, indem Sie die folgenden Vorgänge durchführen:
*   Durchsuchen Sie die Data Lake Storage Gen1-Ressourcen mehrerer Data Lake Storage Gen1-Konten.  
*   Verwenden Sie eine Verbindungszeichenfolge, um eine direkte Verbindung mit Data Lake Storage Gen1 herzustellen und die Daten zu verwalten. 
*   Zeigen Sie Data Lake Storage Gen1-Ressourcen, die von anderen Benutzern per ACL freigegeben wurden, unter **Local and Attached** (Lokal und angefügt) an.
*   Führen Sie CRUD-Vorgänge für Dateien und Ordner durch: Unterstützung von rekursiven Ordnern und Mehrfachauswahl von Dateien. 
*   Sie können Ordner per Drag & Drop verschieben und hinzufügen, um schnell auf die letzten Speicherorte zuzugreifen. Dieser Vorgang ähnelt der Verwendung des Datei-Explorers auf dem Desktop. 
*   Kopieren und öffnen Sie einen Data Lake Storage Gen1-Hyperlink in Storage-Explorer mit nur einem Klick. 
*   Zeigen Sie das Aktivitätsprotokoll im unteren rechten Bereich an, um den Aktivitätsstatus einzublenden.
*   Zeigen Sie die Ordnerstatistiken und Dateieigenschaften an.

## <a name="manage-resources-in-azure-storage-explorer"></a>Verwalten von Ressourcen in Azure Storage-Explorer
Wenn Sie ein Data Lake Storage Gen1-Konto erstellt haben, haben Sie folgende Möglichkeiten:

* Laden Sie Ordner und Dateien hoch und herunter, und öffnen Sie Ressourcen auf Ihrem lokalen Computer.
* Führen Sie das Anheften an den **Schnellzugriff** durch, erstellen Sie einen neuen Ordner, kopieren Sie eine URL, und wählen Sie alles aus.
* Sie können kopieren und einfügen, umbenennen, löschen, Ordnerstatistiken abrufen und aktualisieren.

Im Folgenden wird veranschaulicht, wie Sie Ressourcen in einem Data Lake Storage Gen1-Konto verwalten. Führen Sie die Schritte für die Aufgabe aus, die Sie durchführen möchten.

### <a name="upload-files"></a>Hochladen von Dateien

1. Wählen Sie in der Symbolleiste des Hauptbereichs die Option **Hochladen** und im Dropdownmenü dann die Option **Dateien hochladen**.

   ![Menüelement „Dateien hochladen“](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-files-menu.png) 

2. Wählen Sie im Dialogfeld **Select files to upload** (Dateien zum Hochladen auswählen) die Dateien aus, die Sie hochladen möchten.

   ![Dialogfeld zum Hochladen von Dateien](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-files-dialog.png)

3. Wählen Sie **Öffnen**, um den Upload zu starten.

### <a name="upload-a-folder"></a>Hochladen eines Ordners

1. Wählen Sie in der Symbolleiste des Hauptbereichs die Option **Hochladen** und dann im Dropdownmenü die Option **Upload Folder** (Ordner hochladen).

   ![Menüelement „Upload folder“ (Ordner hochladen)](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-menu.png) 
     
2. Wählen Sie im Dialogfeld **Select folder to upload** (Ordner für Upload auswählen) einen Ordner aus, den Sie hochladen möchten. Klicken Sie anschließend auf **Ordner auswählen**.

   ![Dialogfeld zum Hochladen von Ordnern](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-dialog.png)      

   Der Upload wird gestartet.

   ![Dialogfeld mit aktivem Uploadvorgang](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-drag.png) 

> [!NOTE]
> Sie können die Ordner und Dateien direkt auf einen lokalen Computer ziehen, um den Uploadvorgang zu starten. 
       
### <a name="download-folders-or-files-to-your-local-computer"></a>Herunterladen von Ordnern oder Dateien auf Ihren lokalen Computer

1. Wählen Sie die Ordner oder Dateien aus, die Sie herunterladen möchten.
2. Wählen Sie in der Symbolleiste des Hauptbereichs die Option **Herunterladen**.
3. Geben Sie im Dialogfeld **Select a folder to save the downloaded files into** (Ordner zum Speichern heruntergeladener Dateien auswählen) den Speicherort und den Namen an.
4. Wählen Sie **Speichern** aus.

### <a name="open-a-folder-or-file-from-your-local-computer"></a>Öffnen eines Ordners oder einer Datei von Ihrem lokalen Computer

1. Wählen Sie den Ordner oder die Datei aus, der bzw. die geöffnet werden soll.
2. Wählen Sie in der Symbolleiste des Hauptbereichs die Option **Öffnen**. Oder klicken Sie mit der rechten Maustaste auf den ausgewählten Ordner bzw. die Datei, und wählen Sie im Kontextmenü dann die Option **Öffnen**.

Die Datei wird heruntergeladen und mit der Anwendung geöffnet, die dem zugrunde liegenden Dateityp zugeordnet ist. Ein Ordner wird im Hauptbereich geöffnet.

![Geöffnete Datei](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-open.png) 

### <a name="copy-folders-or-files-to-the-clipboard"></a>Kopieren von Ordnern oder Dateien in die Zwischenablage

1. Wählen Sie die Ordner oder Dateien aus, die Sie kopieren möchten.
2. Wählen Sie in der Symbolleiste des Hauptbereichs die Option **Kopieren**. Oder klicken Sie mit der rechten Maustaste auf die ausgewählten Ordner oder Dateien, und wählen Sie im Kontextmenü dann die Option **Kopieren**.
3. Navigieren Sie im linken Bereich zu einem anderen Data Lake Storage Gen1-Konto, und doppelklicken Sie darauf, um es im Hauptbereich anzuzeigen.
4. Wählen Sie in der Symbolleiste des Hauptbereichs die Option **Einfügen**, um eine Kopie zu erstellen. Oder wählen Sie im Kontextmenü des Ziels die Option **Einfügen**.

![Auswahl zum Kopieren eines Ordners](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-copy-paste.png)

> [!NOTE]
> Kopier- bzw. Einfügevorgänge über mehrere Speichertypen hinweg werden nicht unterstützt. Sie können Data Lake Storage Gen1-Ordner oder -Dateien kopieren und in ein anderes Data Lake Storage Gen1-Konto einfügen. Es ist aber *nicht* möglich, Data Lake Storage Gen1-Ordner oder -Dateien zu kopieren und in Azure-Blobspeicher einzufügen (oder umgekehrt).
> 
> Der Kopier- bzw. Einfügevorgang funktioniert, indem die Ordner oder Dateien auf den lokalen Computer heruntergeladen und dann auf das Ziel hochgeladen werden. Mit dem Tool wird die Aktion *nicht* auf dem Back-End durchgeführt. Der Kopier- bzw. Einfügevorgang für große Dateien ist langsam. Das Kopieren und Verschieben von Dateien mit hoher Leistung wird derzeit optimiert.

### <a name="delete-folders-or-files"></a>Löschen von Ordnern oder Dateien

1. Wählen Sie die Ordner oder Dateien aus, die Sie löschen möchten.
2. Wählen Sie in der Symbolleiste des Hauptbereichs die Option **Löschen**. Oder klicken Sie mit der rechten Maustaste auf die ausgewählten Ordner oder Dateien, und wählen Sie im Kontextmenü dann die Option **Löschen**.
3. Wählen Sie im Bestätigungsdialogfeld die Option **Ja**.

![Auswahl für das Löschen eines Ordners](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-delete.png)

### <a name="pin-to-quick-access"></a>Anheften an Schnellzugriff

1. Wählen Sie den Ordner aus, den Sie anheften möchten.
2. Wählen Sie in der Symbolleiste im Hauptbereich die Option **An Schnellzugriff anheften**.

   Im linken Bereich wird der ausgewählte Ordner dem Knoten **Schnellzugriff** hinzugefügt.

   ![Auswahl für das Anheften eines Ordners an „Schnellzugriff“](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-quick-access.png)

Nachdem Sie einen Ordner an den Knoten **Schnellzugriff** angeheftet haben, können Sie leicht auf Ressourcen zugreifen.

### <a name="use-deep-links"></a>Verwenden von Deep-Links
Wenn Sie über eine URL verfügen, können Sie sie im Datei-Explorer oder Browser in den Adresspfad eingeben. „Storage Explorer.exe“ wird automatisch ausgeführt, um auf den URL-Speicherort zuzugreifen.

![Deep-Link im Datei-Explorer](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-deep-link.png)


## <a name="next-steps"></a>Nächste Schritte
* Sehen Sie sich die [neuesten Versionsanmerkungen und Videos zum Storage-Explorer](https://www.storageexplorer.com) an.
* Erfahren Sie, wie Sie [Azure Cosmos DB in Azure Storage-Explorer verwalten](https://docs.microsoft.com/azure/cosmos-db/storage-explorer).
* [Erste Schritte mit dem Storage-Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).
* [Erste Schritte mit Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview)
* Sehen Sie sich ein [YouTube-Video zur Verwendung von Azure Cosmos DB in Azure Storage-Explorer](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be) an.
