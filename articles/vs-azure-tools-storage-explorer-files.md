---
title: Verwenden des Speicher-Explorers mit Azure File Storage | Microsoft-Dokumentation
description: Hier wird beschrieben, wie Sie den Storage-Explorer für die Verwendung von Dateifreigaben und Dateien nutzen.
services: storage
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: ''
ms.assetid: ''
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/09/2017
ms.author: cawa
ms.openlocfilehash: 84f6473c25a5be11eeda7cd2b311d93a7226a78c
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96488390"
---
# <a name="using-storage-explorer-with-azure-file-storage"></a>Verwenden des Storage-Explorers mit Azure File Storage

Azure File Storage ist ein Dienst, bei dem Dateifreigaben in der Cloud unter Verwendung des standardmäßigen SMB-Protokolls (Server Message Block) bereitgestellt werden können. Sowohl SMB 2.1 als auch SMB 3.0 werden unterstützt. Mit Azure File Storage können Sie Legacyanwendungen, für die Dateifreigaben benötigt werden, schnell und ohne teures Umschreiben zu Azure migrieren. Sie können File Storage verwenden, um Daten weltweit öffentlich zur Verfügung zu stellen oder um Anwendungsdaten privat zu speichern. In diesem Artikel wird beschrieben, wie Sie den Storage-Explorer für Dateifreigaben und Dateien verwenden.

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie die in diesem Artikel aufgeführten Schritte ausführen können, benötigen Sie Folgendes:

- [Herunterladen und Installieren des Storage-Explorers](https://www.storageexplorer.com/)

- [Herstellen der Verbindung mit einem Azure Storage-Konto oder -Dienst](./vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service)

## <a name="create-a-file-share"></a>Erstellen einer Dateifreigabe

Alle Dateien müssen sich auf einer Dateifreigabe befinden. Hierbei handelt es sich lediglich um eine logische Gruppierung von Dateien. Ein Konto kann eine unbegrenzte Anzahl von Dateifreigaben enthalten, und auf jeder Freigabe kann eine unbegrenzte Anzahl von Dateien gespeichert werden.

Die folgenden Schritte veranschaulichen, wie Sie eine Dateifreigabe im Storage-Explorer erstellen.

1. Öffnen Sie den Storage-Explorer.

1. Erweitern Sie im linken Bereich das Speicherkonto, in dem Sie die Dateifreigabe erstellen möchten.

1. Klicken Sie mit der rechten Maustaste auf **Dateifreigaben**, und wählen Sie im Kontextmenü die Option **Dateifreigabe erstellen**.

    ![Erstellen einer Dateifreigabe](media/vs-azure-tools-storage-explorer-files/image1.png)

1. Unter dem Ordner **Dateifreigaben** wird ein Textfeld angezeigt. Geben Sie den Namen für Ihre Dateifreigabe ein. Eine Liste mit den Regeln und Einschränkungen für die Benennung von Dateifreigaben finden Sie unter [Erstellen eines Containers](./storage/blobs/storage-quickstart-blobs-dotnet.md).

    ![Benennen der Freigabe](media/vs-azure-tools-storage-explorer-files/image2.png)

1. Drücken Sie die **EINGABETASTE**, wenn Sie mit dem Erstellen der Dateifreigabe fertig sind, oder drücken Sie **ESC**, um den Vorgang abzubrechen. Nach der erfolgreichen Erstellung der Dateifreigabe wird sie im Ordner **Dateifreigaben** für das ausgewählte Speicherkonto angezeigt.

    ![Neue Freigabe](media/vs-azure-tools-storage-explorer-files/image3.png)

## <a name="view-a-file-shares-contents"></a>Anzeigen des Inhalts einer Dateifreigabe

Dateifreigaben enthalten Dateien und Ordner (die ebenfalls Dateien enthalten können).

Die folgenden Schritte veranschaulichen, wie Sie den Inhalt einer Dateifreigabe im Storage-Explorer anzeigen:

1. Öffnen Sie den Storage-Explorer.

1. Erweitern Sie im linken Bereich das Speicherkonto, das die gewünschte Dateifreigabe enthält.

1. Erweitern Sie die **Dateifreigaben** des Speicherkontos.

1. Klicken Sie mit der rechten Maustaste auf die Dateifreigabe, die Sie anzeigen möchten, und wählen Sie im Kontextmenü die Option **Öffnen**. Sie können auch auf die Dateifreigabe doppelklicken, die Sie anzeigen möchten.

    ![Öffnen der Freigabe](media/vs-azure-tools-storage-explorer-files/image4.png)

1. Im Hauptbereich wird der Inhalt der Dateifreigabe angezeigt.
    
    ![Screenshot: Hauptbereich für eine Dateifreigabe im Storage-Explorer mit dem Inhalt der Freigabe](media/vs-azure-tools-storage-explorer-files/image5.png)

## <a name="delete-a-file-share"></a>Löschen einer Dateifreigabe

Dateifreigaben können nach Bedarf leicht erstellt und gelöscht werden. (Informationen zum Löschen von einzelnen Dateien finden Sie im Abschnitt [Verwalten von Dateien auf einer Dateifreigabe](./vs-azure-tools-storage-explorer-blobs.md#managing-blobs-in-a-blob-container).)

Die folgenden Schritte veranschaulichen, wie Sie eine Dateifreigabe im Storage-Explorer löschen:

1. Öffnen Sie den Storage-Explorer.

1. Erweitern Sie im linken Bereich das Speicherkonto, das die gewünschte Dateifreigabe enthält.

1. Erweitern Sie die **Dateifreigaben** des Speicherkontos.

1. Klicken Sie mit der rechten Maustaste auf die Dateifreigabe, die Sie löschen möchten, und wählen Sie im Kontextmenü die Option **Löschen**. Sie können auch die **ENTF-TASTE** drücken, um die derzeit ausgewählte Dateifreigabe zu löschen.

    ![Löschen](media/vs-azure-tools-storage-explorer-files/image6.png)

1. Wählen Sie im Bestätigungsdialogfeld die Option **Ja** .
    
    ![Bestätigungsdialogfeld](media/vs-azure-tools-storage-explorer-files/image7.png)

## <a name="copy-a-file-share"></a>Kopieren einer Dateifreigabe

Mit dem Storage-Explorer können Sie eine Dateifreigabe in die Zwischenablage kopieren und die Dateifreigabe dann in ein anderes Speicherkonto einfügen. (Informationen zum Kopieren von einzelnen Dateien finden Sie im Abschnitt [Verwalten von Dateien auf einer Dateifreigabe](./vs-azure-tools-storage-explorer-blobs.md#managing-blobs-in-a-blob-container).)

Die folgenden Schritte veranschaulichen, wie Sie eine Dateifreigabe aus einem Speicherkonto in ein anderes kopieren.

1. Öffnen Sie den Storage-Explorer.

1. Erweitern Sie im linken Bereich das Speicherkonto, das die zu kopierende Dateifreigabe enthält.

1. Erweitern Sie die **Dateifreigaben** des Speicherkontos.

1. Klicken Sie mit der rechten Maustaste auf die Dateifreigabe, die Sie kopieren möchten, und wählen Sie im Kontextmenü die Option **Copy File Share** (Dateifreigabe kopieren).

    ![Kopieren der Dateifreigabe](media/vs-azure-tools-storage-explorer-files/image8.png)

1. Klicken Sie mit der rechten Maustaste auf das gewünschte Zielspeicherkonto, in das Sie die Dateifreigabe einfügen möchten, und wählen Sie im Kontextmenü die Option **Paste File Share** (Dateifreigabe einfügen).

    ![Einfügen einer Dateifreigabe](media/vs-azure-tools-storage-explorer-files/image9.png)

## <a name="get-the-sas-for-a-file-share"></a>Abrufen der SAS für eine Dateifreigabe

[Shared Access Signatures (SAS)](./storage/common/storage-sas-overview.md) ermöglichen den delegierten Zugriff auf Ressourcen in Ihrem Speicherkonto. Sie haben die Möglichkeit, einem Client für einen bestimmten Zeitraum spezielle eingeschränkte Berechtigungen für Objekte in Ihrem Speicherkonto zu erteilen, ohne Ihre Konto-Zugriffsschlüssel weitergeben zu müssen.

Die folgenden Schritte veranschaulichen, wie Sie eine SAS für eine Dateifreigabe erstellen:

1. Öffnen Sie den Storage-Explorer.

1. Erweitern Sie im linken Bereich das Speicherkonto mit der Dateifreigabe, für die Sie die SAS abrufen möchten.

1. Erweitern Sie die **Dateifreigaben** des Speicherkontos.

1. Klicken Sie mit der rechten Maustaste auf die gewünschte Dateifreigabe, und wählen Sie im Kontextmenü die Option **Get Shared Access Signature** (SAS abrufen).

    ![Abrufen der Shared Access Signature](media/vs-azure-tools-storage-explorer-files/image10.png)

1. Geben Sie im Dialogfeld **Shared Access Signature** die Richtlinie, das Start- und Ablaufdatum, die Zeitzone und die Zugriffsebenen für die Ressource an.

    ![Dialogfeld „SAS“](media/vs-azure-tools-storage-explorer-files/image11.png)

1. Wählen Sie **Erstellen**, wenn Sie das Angeben der SAS-Optionen abgeschlossen haben.

1. In einem zweiten Dialogfeld **Shared Access Signature** wird dann die Dateifreigabe zusammen mit der URL und den Abfragezeichenfolgen angezeigt, die Sie zum Zugreifen auf die Speicherressource verwenden können. Wählen Sie neben der URL, die Sie in die Zwischenablage kopieren möchten, die Option **Kopieren** .
    
    ![Zweites Dialogfeld „SAS“](media/vs-azure-tools-storage-explorer-files/image12.png)

1. Wählen Sie abschließend die Option **Schließen**.

## <a name="manage-access-policies-for-a-file-share"></a>Verwalten von Zugriffsrichtlinien für eine Dateifreigabe

Die folgenden Schritte veranschaulichen, wie Sie Zugriffsrichtlinien für eine Dateifreigabe verwalten (hinzufügen und entfernen): Die Zugriffsrichtlinien werden zum Erstellen von SAS-URLs verwendet, mit denen Benutzer während eines festgelegten Zeitraums auf die Storage-Dateiressource zugreifen können.

1. Öffnen Sie den Storage-Explorer.

1. Erweitern Sie im linken Bereich das Speicherkonto mit der Dateifreigabe, deren Zugriffsrichtlinien Sie verwalten möchten.

1. Erweitern Sie die **Dateifreigaben** des Speicherkontos.

1. Wählen Sie die gewünschte Dateifreigabe, und wählen Sie im Kontextmenü die Option **Manage Access Policies** (Zugriffsrichtlinien verwalten).

    ![Kontextmenü „Manage Access Policies“ (Zugriffsrichtlinien verwalten)](media/vs-azure-tools-storage-explorer-files/image13.png)

1. Im Dialogfeld **Zugriffsrichtlinien** werden alle Zugriffsrichtlinien aufgeführt, die für die ausgewählte Dateifreigabe bereits erstellt wurden.
    
    ![Zugriffsrichtlinien](media/vs-azure-tools-storage-explorer-files/image14.png)

1. Führen Sie diese Schritte je nach der Verwaltungsaufgabe der Zugriffsrichtlinie aus:
    
    - **Hinzufügen einer neuen Zugriffsrichtlinie:** Wählen Sie die Option **Hinzufügen** aus. Nach der Erstellung wird die neu hinzugefügte Zugriffsrichtlinie (mit Standardeinstellungen) im Dialogfeld **Zugriffsrichtlinien** angezeigt.

    - **Bearbeiten einer Zugriffsrichtlinie:** Nehmen Sie die gewünschten Bearbeitungen vor, und wählen Sie anschließend die Option **Speichern**.

    - **Entfernen einer Zugriffsrichtlinie:** Wählen Sie neben der Zugriffsrichtlinie, die Sie entfernen möchten, die Option **Entfernen** aus.

1. Erstellen Sie eine neue SAS-URL, indem Sie zuvor erstellte Zugriffsrichtlinie verwenden:
    
    ![Abrufen der SAS](media/vs-azure-tools-storage-explorer-files/image15.png)
    
    ![SAS-Name und -Eigenschaften](media/vs-azure-tools-storage-explorer-files/image16.png)

## <a name="managing-files-in-a-file-share"></a>Verwalten von Dateien auf einer Dateifreigabe

Nachdem Sie eine Dateifreigabe erstellt haben, können Sie eine Datei auf diese Dateifreigabe hochladen, eine Datei auf Ihren lokalen Computer herunterladen, eine Datei auf Ihrem lokalen Computer öffnen und vieles mehr.

Die folgenden Schritte veranschaulichen, wie Sie die Dateien (und Ordner) auf einer Dateifreigabe verwalten.

1.  Öffnen Sie den Storage-Explorer.

1.  Erweitern Sie im linken Bereich das Speicherkonto, das die zu verwaltende Dateifreigabe enthält.

1.  Erweitern Sie die **Dateifreigaben** des Speicherkontos.

1.  Doppelklicken Sie auf die Dateifreigabe, die Sie anzeigen möchten.

1.  Im Hauptbereich wird der Inhalt der Dateifreigabe angezeigt.

    ![Screenshot: Hauptbereich für die Dateifreigabe „myazurefileshare“ im Storage-Explorer mit dem Inhalt der Freigabe, in dem der erste Ordner ausgewählt ist](media/vs-azure-tools-storage-explorer-files/image17.png)

1.  Im Hauptbereich wird der Inhalt der Dateifreigabe angezeigt.

1.  Führen Sie die folgenden Schritte für die gewünschte Aufgabe aus:

    - **Hochladen von Dateien auf eine Dateifreigabe**

        a.  Wählen Sie auf der Symbolleiste des Hauptbereichs die Option **Hochladen** und dann im Dropdownmenü die Option **Dateien hochladen** aus.

        ![Hochladen von Dateien](media/vs-azure-tools-storage-explorer-files/image18.png)
        
        b. Wählen Sie im Dialogfeld **Dateien hochladen** rechts neben dem Feld **Dateien** die Schaltfläche mit den Auslassungspunkten ( **…** ) aus, um die hochzuladenden Dateien auszuwählen.

        ![Hinzufügen von Dateien](media/vs-azure-tools-storage-explorer-files/image19.png)

        c. Wählen Sie die Option **Hochladen**.

    - **Hochladen eines Ordners auf eine Dateifreigabe**
        
        a. Wählen Sie auf der Symbolleiste des Hauptbereichs die Option **Hochladen** und dann im Dropdownmenü die Option **Uploadordner** aus.

        ![Menü „Upload folder“ (Ordner hochladen)](media/vs-azure-tools-storage-explorer-files/image20.png)

        b. Klicken Sie im Dialogfeld **Uploadordner** rechts vom Textfeld **Ordner** auf die Schaltfläche mit den Auslassungszeichen ( **…** ), um den Ordner auszuwählen, dessen Inhalt Sie hochladen möchten.

        c. Optional können Sie einen Zielordner angeben, in den der Inhalt des ausgewählten Ordners hochgeladen wird. Falls der Zielordner noch nicht vorhanden ist, wird er erstellt.

        d. Wählen Sie die Option **Hochladen**.

    - **Herunterladen einer Datei auf den lokalen Computer**
        
        a. Wählen Sie die Datei aus, die Sie herunterladen möchten.
        
        b. Wählen Sie in der Symbolleiste des Hauptbereichs die Option **Herunterladen**.
        
        c. Geben Sie im Dialogfeld **Specify where to save the downloaded file** (Downloadspeicherort für Datei angeben) den Speicherort, an den die Datei heruntergeladen werden soll, und ihren Namen an.

        d. Wählen Sie **Speichern** aus.

    - **Öffnen einer Datei auf dem lokalen Computer**
        
        a.  Wählen Sie die Datei aus, die Sie öffnen möchten.
        
        b.  Wählen Sie in der Symbolleiste des Hauptbereichs die Option **Öffnen**.
        
        c.  Die Datei wird heruntergeladen und mit der Anwendung geöffnet, die dem zugrunde liegenden Dateityp der Datei zugeordnet ist.

    - **Kopieren einer Datei in die Zwischenablage**

        a. Wählen Sie die Datei aus, die Sie kopieren möchten.

        b. Wählen Sie in der Symbolleiste des Hauptbereichs die Option **Kopieren**.

        c. Navigieren Sie im linken Bereich zu einer anderen Dateifreigabe, und doppelklicken Sie darauf, um sie im Hauptbereich anzuzeigen.

        d. Wählen Sie in der Symbolleiste des Hauptbereichs die Option **Einfügen**, um eine Kopie der Datei zu erstellen.

    - **Löschen von Dateien**

        a. Wählen Sie die Datei aus, die Sie löschen möchten.

        b. Wählen Sie in der Symbolleiste des Hauptbereichs die Option **Löschen**.

        c. Wählen Sie im Bestätigungsdialogfeld die Option **Ja** .

## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich die [neuesten Versionsanmerkungen und Videos zum Storage-Explorer](https://www.storageexplorer.com/) an.

- Informieren Sie sich, wie Sie [Anwendungen mit Azure-Blobs, -Tabellen, -Warteschlangen und -Dateien erstellen](https://azure.microsoft.com/documentation/services/storage/).