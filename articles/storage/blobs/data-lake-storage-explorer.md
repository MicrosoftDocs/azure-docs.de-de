---
title: Verwenden von Azure Storage-Explorer mit Azure Data Lake Storage Gen2
description: Verwenden Sie Azure Storage-Explorer, um Verzeichnisse, Dateien und Zugriffssteuerungslisten (ACLs) in Speicherkonten zu verwalten, für die der hierarchische Namespace aktiviert ist.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 07/16/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: e46bb87788de27916860720284087643db7153dc
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95913401"
---
# <a name="use-azure-storage-explorer-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Verwenden von Azure Storage-Explorer zum Verwalten von Verzeichnissen, Dateien und Zugriffssteuerungslisten in Azure Data Lake Storage Gen2

In diesem Artikel erfahren Sie, wie Sie [Azure Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/) zum Erstellen und Verwalten von Verzeichnissen, Dateien und Berechtigungen in Speicherkonten verwenden, für die der hierarchische Namespace aktiviert ist.

## <a name="prerequisites"></a>Voraussetzungen

> [!div class="checklist"]
> * Ein Azure-Abonnement. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).
> * Ein Speicherkonto, für das der hierarchische Namespace aktiviert ist. Befolgen Sie [diese Anleitung](../common/storage-account-create.md) für die Erstellung.
> * Azure Storage-Explorer muss auf dem lokalen Computer installiert sein. Informationen zum Installieren von Azure Storage-Explorer für Windows, Macintosh oder Linux finden Sie unter [Azure Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/).

## <a name="sign-in-to-storage-explorer"></a>Anmelden bei Storage-Explorer

Nach dem erstmaligen Starten von Storage-Explorer wird das Fenster **Microsoft Azure Storage-Explorer – Verbinden** angezeigt. Obwohl der Storage-Explorer mehrere Möglichkeiten zur Verbindung mit Speicherkonten bietet, wird derzeit nur eine Möglichkeit zur Verwaltung von ACLs unterstützt.

|Aufgabe|Zweck|
|---|---|
|Hinzufügen eines Azure-Kontos | Führt die Umleitung auf die Anmeldeseite Ihrer Organisation durch, um Sie für Azure zu authentifizieren. Dies ist derzeit die einzige unterstützte Authentifizierungsmethode, wenn Sie ACLs verwalten und festlegen möchten.|
|Verwenden einer Verbindungszeichenfolge oder eines Shared Access Signature-URI | Kann verwendet werden, um mit einem SAS-Token oder einer freigegebenen Verbindungszeichenfolge direkt auf einen Container oder ein Speicherkonto zuzugreifen. |
|Verwenden eines Speicherkontonamens und -schlüssels| Verwenden Sie den Speicherkontonamen und -schlüssel Ihres Speicherkontos, um eine Verbindung mit Azure Storage herzustellen.|

Wählen Sie **Azure-Konto hinzufügen**, und klicken Sie auf **Anmelden...** . Befolgen Sie die Anweisungen auf dem Bildschirm, um sich an Ihrem Azure-Konto anzumelden.

![Screenshot: Microsoft Azure Storage-Explorer mit hervorgehobener Option „Azure-Konto hinzufügen“ und der Schaltfläche „Anmelden“](media/storage-quickstart-blobs-storage-explorer/connect.png)

Nach Abschluss des Verbindungsvorgangs wird Azure Storage-Explorer geladen, und die Registerkarte **Explorer** wird angezeigt. So erhalten Sie einen Einblick in Ihre gesamten Azure Storage-Konten und in den lokalen Speicher, der über den [Azurite-Speicheremulator](../common/storage-use-azurite.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [Cosmos DB](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)-Konten oder [Azure Stack](/azure-stack/user/azure-stack-storage-connect-se?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)-Umgebungen konfiguriert wurde.

![Fenster „Microsoft Azure Storage-Explorer – Verbinden“](media/storage-quickstart-blobs-storage-explorer/mainpage.png)

## <a name="create-a-container"></a>Erstellen eines Containers

Ein Container enthält Verzeichnisse und Dateien. Erweitern Sie das Speicherkonto, das Sie im vorherigen Schritt erstellt haben, um einen Container zu erstellen. Wählen Sie **BLOB-Container**, klicken Sie mit der rechten Maustaste, und wählen Sie **BLOB-Container erstellen**. Geben Sie den Namen für den Container ein. Eine Liste mit den Regeln und Einschränkungen für die Benennung von Containern finden Sie unter [Erstellen eines Containers](storage-quickstart-blobs-dotnet.md#create-a-container). Drücken Sie nach Abschluss des Vorgangs die **EINGABETASTE**, um den Container zu erstellen. Nach der erfolgreichen Erstellung des Containers wird dieser im Ordner **Blobcontainer** für das ausgewählte Speicherkonto angezeigt.

![Microsoft Azure Storage-Explorer – Erstellen eines Containers](media/data-lake-storage-explorer/creating-a-filesystem.png)

## <a name="create-a-directory"></a>Erstellen eines Verzeichnisses

Wählen Sie den Container aus, den Sie im vorherigen Schritt erstellt haben, um ein Verzeichnis zu erstellen. Wählen Sie auf dem Menüband „Container“ die Schaltfläche **Neuer Ordner** aus. Geben Sie den Namen für das Verzeichnis ein. Drücken Sie nach Abschluss des Vorgangs die **EINGABETASTE**, um das Verzeichnis zu erstellen. Nachdem das Verzeichnis erstellt wurde, wird es im Editor-Fenster angezeigt.

![Microsoft Azure Storage-Explorer – Erstellen eines Verzeichnisses](media/data-lake-storage-explorer/creating-a-directory.png)

## <a name="upload-blobs-to-the-directory"></a>Hochladen von Blobs in das Verzeichnis

Wählen Sie auf dem Menüband „Verzeichnis“ die Schaltfläche **Hochladen** aus. Bei diesem Vorgang besteht die Option, einen Ordner oder eine Datei hochzuladen.

Wählen Sie die Dateien oder den Ordner für den Upload aus.

![Microsoft Azure Storage-Explorer – Blob hochladen](media/data-lake-storage-explorer/upload-file.png)

Wenn Sie **OK** wählen, werden die ausgewählten Dateien in die Warteschlange für den Upload eingereiht, und jede Datei wird hochgeladen. Nach Abschluss des Uploads werden die Ergebnisse im Fenster **Aktivitäten** angezeigt.

## <a name="view-blobs-in-a-directory"></a>Anzeigen von Blobs in einem Verzeichnis

Wählen Sie in der Anwendung **Azure Storage-Explorer** ein Verzeichnis unter einem Speicherkonto aus. Im Hauptbereich wird eine Liste mit den Blobs des ausgewählten Verzeichnisses angezeigt.

![Microsoft Azure Storage-Explorer – Blobs eines Verzeichnisses auflisten](media/data-lake-storage-explorer/list-files.png)

## <a name="download-blobs"></a>Herunterladen von Blobs

Wählen Sie auf dem Menüband bei ausgewählter Datei die Option **Herunterladen** aus, um mit **Azure Storage-Explorer** Dateien herunterzuladen. Ein Dialogfeld für Dateien wird geöffnet, und Sie können einen Dateinamen eingeben. Wählen Sie **Speichern** aus, um mit dem Herunterladen einer Datei in den lokalen Speicherort zu beginnen.

## <a name="managing-access"></a>Verwalten des Zugriffs

Sie können Berechtigungen im Stammverzeichnis Ihres Containers festlegen. Hierzu müssen Sie mit Ihrem individuellen Konto mit entsprechenden Rechten beim Azure Storage-Explorer angemeldet sein (anstatt über eine Verbindungszeichenfolge). Klicken Sie mit der rechten Maustaste auf Ihren Container, und wählen Sie **Berechtigungen verwalten** aus, um das Dialogfeld **Berechtigung verwalten** zu öffnen.

![Microsoft Azure Storage-Explorer – Verzeichniszugriff verwalten](media/storage-quickstart-blobs-storage-Explorer/manageperms.png)

Das Dialogfeld **Berechtigung verwalten** ermöglicht es Ihnen, Berechtigungen für den Besitzer und die Besitzergruppe zu verwalten. Sie können auch neue Benutzer und Gruppen zur Zugriffssteuerungsliste hinzufügen, für die Sie dann die Berechtigungen verwalten können.

Um einen neuen Benutzer oder eine Gruppe zur Zugriffssteuerungsliste hinzuzufügen, wählen Sie das Feld **Benutzer oder Gruppe hinzufügen** aus.

Geben Sie den entsprechenden Azure Active Directory (AAD)-Eintrag ein, den Sie zur Liste hinzufügen möchten, und wählen Sie dann **Hinzufügen**.

Der Benutzer oder die Gruppe erscheint nun im Feld **Benutzer und Gruppen:** , sodass Sie mit der Verwaltung ihrer Berechtigungen beginnen können.

> [!NOTE]
> Es ist eine Best Practice und es wird empfohlen, eine Sicherheitsgruppe in AAD zu erstellen und die Berechtigungen für die Gruppe und nicht für einzelne Benutzer zu verwalten. Einzelheiten zu dieser Empfehlung sowie zu anderen Best Practices finden Sie unter [Best Practices für Data Lake Storage Gen2](data-lake-storage-best-practices.md).

Es gibt zwei Kategorien von Berechtigungen, die Sie zuweisen können: Zugriffs-ACLs und Standard-ACLs.

* **Zugriff**: Zugriffs-ACLs steuern den Zugriff auf ein Objekt. Dateien und Verzeichnisse verfügen jeweils über Zugriffs-ACLs.

* **Standard:** Eine Vorlage von ACLs, die einem Verzeichnis zugeordnet ist, das die Zugriffs-ACLs für alle untergeordneten Elemente bestimmt, die unter diesem Verzeichnis erstellt werden. Dateien verfügen über keine Standard-ACLs.

Innerhalb dieser beiden Kategorien gibt es drei Berechtigungen, die Sie dann Dateien oder Verzeichnissen zuweisen können: **Lesen**, **Schreiben** und **Ausführen**.

>[!NOTE]
> Wenn Sie hier eine Auswahl treffen, werden keine Berechtigungen für ein aktuell vorhandenes Element im Verzeichnis festgelegt. Sie müssen jedes einzelne Element aufrufen und die Berechtigungen manuell festlegen, wenn die Datei bereits existiert.

Sie können Berechtigungen für einzelne Verzeichnisse sowie einzelne Dateien verwalten, was Ihnen eine differenzierte Zugriffssteuerung ermöglicht. Der Prozess zur Verwaltung von Berechtigungen für Verzeichnisse und Dateien ist derselbe wie oben beschrieben. Klicken Sie mit der rechten Maustaste auf die Datei oder das Verzeichnis, für das Sie die Berechtigungen verwalten möchten, und folgen Sie dem gleichen Prozess.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über Zugriffssteuerungslisten in Data Lake Storage Gen2.

> [!div class="nextstepaction"]
> [Zugriffssteuerung in Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md)