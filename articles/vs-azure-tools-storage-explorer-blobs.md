---
title: Verwalten von Azure Blob Storage-Ressourcen mit dem Storage-Explorer | Microsoft-Dokumentation
description: Verwalten Sie Azure Blob Storage-Ressourcen mit Storage-Explorer. Erstellen Sie einen Blobcontainer, zeigen Sie Blobcontainerinhalte an, löschen oder kopieren Sie einen Blobcontainer usw.
services: storage
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: ''
ms.assetid: 2f09e545-ec94-4d89-b96c-14783cc9d7a9
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/21/2019
ms.author: cawa
ms.openlocfilehash: 48f00d964a87790b8d8c9b1d8eceaed26d15199e
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95531966"
---
# <a name="manage-azure-blob-storage-resources-with-storage-explorer"></a>Verwalten von Azure Blob Storage-Ressourcen mit dem Storage-Explorer

## <a name="overview"></a>Übersicht

[Azure Blob Storage](./storage/blobs/storage-quickstart-blobs-dotnet.md) ist ein Dienst zur Speicherung großer Mengen unstrukturierter Daten, beispielsweise Text- oder Binärdaten, auf die von überall auf der Welt über HTTP oder HTTPS zugegriffen werden kann.
Sie können Blob Storage verwenden, um Daten öffentlich auf der ganzen Welt zur Verfügung zu stellen oder um Anwendungsdaten privat zu speichern. In diesem Artikel wird beschrieben, wie Sie den Storage-Explorer für Blobcontainer und Blobs verwenden.

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie die in diesem Artikel aufgeführten Schritte ausführen können, benötigen Sie Folgendes:

* [Herunterladen und Installieren des Storage-Explorers](https://www.storageexplorer.com)
* [Herstellen der Verbindung mit einem Azure Storage-Konto oder -Dienst](vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service)

## <a name="create-a-blob-container"></a>Erstellen eines Blobcontainers

Alle Blobs müssen sich in einem Blobcontainer befinden. Ein Blobcontainer ist einfach eine logische Gruppierung von Blobs. Ein Konto kann eine unbegrenzte Anzahl von Containern enthalten, und in jedem Container kann eine unbegrenzte Anzahl von Blobs gespeichert werden.

Die folgenden Schritte veranschaulichen, wie Sie einen Blobcontainer im Storage-Explorer erstellen.

1. Öffnen Sie den Storage-Explorer.
2. Erweitern Sie im linken Bereich das Speicherkonto, in dem Sie den Blobcontainer erstellen möchten.
3. Klicken Sie mit der rechten Maustaste auf **Blobcontainer**, und wählen Sie im Kontextmenü die Option **Blobcontainer erstellen** aus.

   ![Kontextmenü „Blobcontainer erstellen“][0]
4. Unter dem Ordner **Blobcontainer** wird ein Textfeld angezeigt. Geben Sie den Namen für den Blobcontainer ein. Informationen zu Regeln und Einschränkungen für die Benennung von Blobcontainern finden Sie unter [Erstellen eines Containers](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container).

   ![Textfeld „Blobcontainer erstellen“][1]
5. Drücken Sie die **EINGABETASTE**, wenn Sie mit dem Erstellen des Blobcontainers fertig sind, oder drücken Sie **ESC**, um den Vorgang abzubrechen. Nach der erfolgreichen Erstellung des Blobcontainers wird er im Ordner **Blobcontainer** für das ausgewählte Speicherkonto angezeigt.

   ![Blobcontainer wurde erstellt][2]

## <a name="view-a-blob-containers-contents"></a>Anzeigen des Inhalts eines Blobcontainers

Blobcontainer enthalten Blobs und Ordner (die ebenfalls Blobs enthalten können).

Die folgenden Schritte veranschaulichen, wie Sie den Inhalt eines Blobcontainers im Storage-Explorer erstellen:

1. Öffnen Sie den Storage-Explorer.
2. Erweitern Sie im linken Bereich das Speicherkonto, das den gewünschten Blobcontainer enthält.
3. Erweitern Sie die Option **Blobcontainer** des Speicherkontos.
4. Klicken Sie mit der rechten Maustaste auf den Blobcontainer, den Sie anzeigen möchten, und wählen Sie im Kontextmenü die Option **Blobcontainer-Editor öffnen**.
   Sie können auch auf den Blobcontainer doppelklicken, den Sie anzeigen möchten.

   ![Kontextmenü „Blobcontainer-Editor öffnen“][19]
5. Im Hauptfenster wird der Inhalt des Blobcontainers angezeigt.

   ![Blobcontainer-Editor][3]

## <a name="delete-a-blob-container"></a>Löschen eines Blobcontainers

Blobcontainer können nach Bedarf einfach erstellt und gelöscht werden. (Informationen zum Löschen einzelner Blobs finden Sie im Abschnitt [Verwalten von Blobs in einem Blobcontainer](#managing-blobs-in-a-blob-container).)

Die folgenden Schritte veranschaulichen, wie Sie einen Blobcontainer im Storage-Explorer löschen.

1. Öffnen Sie den Storage-Explorer.
2. Erweitern Sie im linken Bereich das Speicherkonto, das den gewünschten Blobcontainer enthält.
3. Erweitern Sie die Option **Blobcontainer** des Speicherkontos.
4. Klicken Sie mit der rechten Maustaste auf den Blobcontainer, den Sie löschen möchten, und wählen Sie im Kontextmenü die Option **Löschen**.
   Sie können auch die **ENTF-TASTE** drücken, um den derzeit ausgewählten Blobcontainer zu löschen.

   ![Kontextmenü „Delete blob Container“ (Blobcontainer löschen)][4]
5. Wählen Sie im Bestätigungsdialogfeld die Option **Ja** .

   ![Bestätigung „Delete blob Container“ (Blobcontainer löschen)][5]

## <a name="copy-a-blob-container"></a>Kopieren eines Blobcontainers

Mit dem Storage-Explorer können Sie einen Blobcontainer in die Zwischenablage kopieren und diesen Blobcontainer dann in ein anderes Speicherkonto einfügen. (Informationen zum Kopieren einzelner Blobs finden Sie im Abschnitt [Verwalten von Blobs in einem Blobcontainer](#managing-blobs-in-a-blob-container).)

Die folgenden Schritte veranschaulichen, wie Sie einen Blobcontainer aus einem Speicherkonto in ein anderes kopieren.

1. Öffnen Sie den Storage-Explorer.
2. Erweitern Sie im linken Bereich das Speicherkonto, das den gewünschten Blobcontainer enthält.
3. Erweitern Sie die Option **Blobcontainer** des Speicherkontos.
4. Klicken Sie mit der rechten Maustaste auf den Blobcontainer, den Sie kopieren möchten, und wählen Sie im Kontextmenü die Option **Copy Blob Container**(Blobcontainer kopieren).

   ![Kontextmenü „Blobcontainer kopieren“][6]
5. Klicken Sie mit der rechten Maustaste auf das gewünschte Zielspeicherkonto, in das Sie den Blobcontainer einfügen möchten, und wählen Sie im Kontextmenü die Option **Paste Blob Container**(Blobcontainer einfügen).

   ![Kontextmenü „Blobcontainer einfügen“][7]

## <a name="get-the-sas-for-a-blob-container"></a>Abrufen der SAS für einen Blobcontainer

[Shared Access Signatures (SAS)](./storage/common/storage-sas-overview.md) ermöglichen den delegierten Zugriff auf Ressourcen in Ihrem Speicherkonto.
Sie haben die Möglichkeit, einem Client für einen bestimmten Zeitraum spezielle eingeschränkte Berechtigungen für Objekte in Ihrem Speicherkonto zu erteilen, ohne Ihre Konto-Zugriffsschlüssel weitergeben zu müssen.

Die folgenden Schritte veranschaulichen, wie Sie eine SAS für einen Blobcontainer erstellen:

1. Öffnen Sie den Storage-Explorer.
2. Erweitern Sie im linken Bereich das Speicherkonto mit dem Blobcontainer, für den Sie die SAS abrufen möchten.
3. Erweitern Sie die Option **Blobcontainer** des Speicherkontos.
4. Klicken Sie mit der rechten Maustaste auf den gewünschten Blobcontainer, und wählen Sie im Kontextmenü die Option **Get Shared Access Signature**(SAS abrufen).

   ![Kontextmenü „SAS abrufen“][8]
5. Geben Sie im Dialogfeld **Shared Access Signature** die Richtlinie, das Start- und Ablaufdatum, die Zeitzone und die Zugriffsebenen für die Ressource an.

   ![SAS abrufen – Optionen][9]
6. Wählen Sie **Erstellen**, wenn Sie das Angeben der SAS-Optionen abgeschlossen haben.
7. In einem zweiten Dialogfeld **Shared Access Signature** wird dann der Blobcontainer zusammen mit der URL und den Abfragezeichenfolgen angezeigt, die Sie zum Zugreifen auf die Speicherressource verwenden können.
   Wählen Sie neben der URL, die Sie in die Zwischenablage kopieren möchten, die Option **Kopieren** .

   ![SAS-URLs kopieren][10]
8. Wählen Sie abschließend die Option **Schließen**.

## <a name="manage-access-policies-for-a-blob-container"></a>Verwalten von Zugriffsrichtlinien für einen Blobcontainer

Die folgenden Schritte veranschaulichen, wie Sie Zugriffsrichtlinien für einen Blobcontainer verwalten (hinzufügen und entfernen):

1. Öffnen Sie den Storage-Explorer.
2. Erweitern Sie im linken Bereich das Speicherkonto mit dem Blobcontainer, dessen Zugriffsrichtlinien Sie verwalten möchten.
3. Erweitern Sie die Option **Blobcontainer** des Speicherkontos.
4. Wählen Sie den gewünschten Blobcontainer, und wählen Sie im Kontextmenü die Option **Manage Access Policies**(Zugriffsrichtlinien verwalten).

   ![Kontextmenü „Manage Access Policies“ (Zugriffsrichtlinien verwalten)][11]
5. Im Dialogfeld **Zugriffsrichtlinien** werden alle Zugriffsrichtlinien aufgeführt, die für den ausgewählten Blobcontainer bereits erstellt wurden.

   ![Zugriffsrichtlinie – Optionen][12]
6. Führen Sie diese Schritte je nach der Verwaltungsaufgabe der Zugriffsrichtlinie aus:

   * **Hinzufügen einer neuen Zugriffsrichtlinie:** Wählen Sie die Option **Hinzufügen** aus. Nach der Erstellung wird die neu hinzugefügte Zugriffsrichtlinie (mit Standardeinstellungen) im Dialogfeld **Zugriffsrichtlinien** angezeigt.
   * **Bearbeiten einer Zugriffsrichtlinie:** Nehmen Sie die gewünschten Bearbeitungen vor, und wählen Sie die Option **Speichern** aus.
   * **Entfernen einer Zugriffsrichtlinie:** Wählen Sie neben der Zugriffsrichtlinie, die Sie entfernen möchten, die Option **Entfernen** aus.

## <a name="set-the-public-access-level-for-a-blob-container"></a>Festlegen der öffentlichen Zugriffsebene für einen Blobcontainer

Standardmäßig wird für jeden Blobcontainer „Kein öffentlicher Zugriff“ festgelegt.

Die folgenden Schritte veranschaulichen, wie Sie eine öffentliche Zugriffsebene für einen Blobcontainer angeben.

1. Öffnen Sie den Storage-Explorer.
2. Erweitern Sie im linken Bereich das Speicherkonto mit dem Blobcontainer, dessen Zugriffsrichtlinien Sie verwalten möchten.
3. Erweitern Sie die Option **Blobcontainer** des Speicherkontos.
4. Wählen Sie den gewünschten Blobcontainer, und wählen Sie im Kontextmenü die Option **Set Public Access Level**(Öffentliche Zugriffsebene festlegen).

   ![Kontextmenü „Set Public Access Level“ (Öffentliche Zugriffsebene festlegen)][13]
5. Geben Sie im Dialogfeld **Set Container Public Access Level** (Öffentliche Zugriffsebene für Container festlegen) die gewünschte Zugriffsebene an.

   ![Set Public Access Level (Öffentliche Zugriffsebene festlegen) – Optionen][14]
6. Wählen Sie **Übernehmen**.

## <a name="managing-blobs-in-a-blob-container"></a>Verwalten von Blobs in einem Blobcontainer

Nach dem Erstellen eines Blobcontainers können Sie ein Blob in den Blobcontainer hochladen, ein Blob auf den lokalen Computer herunterladen, ein Blob auf dem lokalen Computer öffnen und vieles mehr.

Die folgenden Schritte veranschaulichen, wie Sie die Blobs (und Ordner) in einem Blobcontainer verwalten.

1. Öffnen Sie den Storage-Explorer.
2. Erweitern Sie im linken Bereich das Speicherkonto, das den gewünschten Blobcontainer enthält.
3. Erweitern Sie die Option **Blobcontainer** des Speicherkontos.
4. Doppelklicken Sie auf den Blobcontainer, den Sie anzeigen möchten.
5. Im Hauptfenster wird der Inhalt des Blobcontainers angezeigt.

   ![Blobcontainer anzeigen][3]
6. Im Hauptfenster wird der Inhalt des Blobcontainers angezeigt.
7. Führen Sie die folgenden Schritte für die gewünschte Aufgabe aus:

   * **Hochladen von Dateien in einen Blobcontainer**

     1. Wählen Sie auf der Symbolleiste des Hauptbereichs die Option **Hochladen** und dann im Dropdownmenü die Option **Dateien hochladen** aus.

        ![Menü „Dateien hochladen“][15]
     2. Wählen Sie im Dialogfeld **Dateien hochladen** rechts neben dem Feld **Dateien** die Schaltfläche mit den Auslassungspunkten (**…**) aus, um die hochzuladenden Dateien auszuwählen.

        ![Dateien hochladen – Optionen][16]
     3. Geben Sie als Typ **Blob-Typ** an. Weitere Informationen finden Sie unter [Erstellen eines Containers](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container).
     4. Optional können Sie einen Zielordner angeben, in den die ausgewählten Dateien hochgeladen werden. Falls der Zielordner noch nicht vorhanden ist, wird er erstellt.
     5. Wählen Sie die Option **Hochladen**.
   * **Hochladen eines Ordners in einen Blobcontainer**

     1. Wählen Sie auf der Symbolleiste des Hauptbereichs die Option **Hochladen** und dann im Dropdownmenü die Option **Uploadordner** aus.

        ![Menü „Upload folder“ (Ordner hochladen)][17]
     2. Klicken Sie im Dialogfeld **Uploadordner** rechts vom Textfeld **Ordner** auf die Schaltfläche mit den Auslassungszeichen (**…**), um den Ordner auszuwählen, dessen Inhalt Sie hochladen möchten.

        ![Upload folder (Ordner hochladen) – Optionen][18]
     3. Geben Sie als Typ **Blob-Typ** an. Weitere Informationen finden Sie unter [Erstellen eines Containers](storage/blobs/storage-quickstart-blobs-dotnet.md#create-a-container).
     4. Optional können Sie einen Zielordner angeben, in den der Inhalt des ausgewählten Ordners hochgeladen wird. Falls der Zielordner noch nicht vorhanden ist, wird er erstellt.
     5. Wählen Sie die Option **Hochladen**.
   * **Herunterladen eines Blobs auf den lokalen Computer**

     1. Wählen Sie das Blob aus, das Sie herunterladen möchten.
     2. Wählen Sie in der Symbolleiste des Hauptbereichs die Option **Herunterladen**.
     3. Geben Sie im Dialogfeld **Specify where to save the downloaded blob** (Downloadspeicherort für Blob angeben) den Speicherort, an den das Blob heruntergeladen werden soll, und den Namen dafür an.  
     4. Wählen Sie **Speichern** aus.
   * **Öffnen eines Blobs auf dem lokalen Computer**

     1. Wählen Sie das Blob aus, das Sie öffnen möchten.
     2. Wählen Sie in der Symbolleiste des Hauptbereichs die Option **Öffnen**.
     3. Das Blob wird heruntergeladen und mit der Anwendung geöffnet, die dem zugrunde liegenden Dateityp des Blobs zugeordnet ist.
   * **Kopieren eines Blobs in die Zwischenablage**

     1. Wählen Sie das Blob aus, das Sie kopieren möchten.
     2. Wählen Sie in der Symbolleiste des Hauptbereichs die Option **Kopieren**.
     3. Navigieren Sie im linken Bereich zu einem anderen Blobcontainer, und doppelklicken Sie darauf, um ihn im Hauptbereich anzuzeigen.
     4. Wählen Sie in der Symbolleiste des Hauptbereichs die Option **Einfügen**, um eine Kopie des Blobs zu erstellen.
   * **Löschen eines Blobs**

     1. Wählen Sie das Blob aus, das Sie löschen möchten.
     2. Wählen Sie in der Symbolleiste des Hauptbereichs die Option **Löschen**.
     3. Wählen Sie im Bestätigungsdialogfeld die Option **Ja** .

## <a name="next-steps"></a>Nächste Schritte

* Sehen Sie sich die [neuesten Versionsanmerkungen und Videos zum Storage-Explorer](https://www.storageexplorer.com) an.
* Informieren Sie sich, wie Sie [Anwendungen mit Azure-Blobs, -Tabellen, -Warteschlangen und -Dateien erstellen](https://azure.microsoft.com/documentation/services/storage/).

[0]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-create-context-menu.png
[1]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create.png
[2]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create-done.png
[3]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-editor.png
[4]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-context-menu.png
[5]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-confirmation.png
[6]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-copy-context-menu.png
[7]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-paste-context-menu.png
[8]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-context-menu.png
[9]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-options.png
[10]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-urls.png
[11]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-context-menu.png
[12]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-options.png
[13]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-context-menu.png
[14]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-options.png
[15]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-menu.png
[16]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-options.png
[17]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-menu.png
[18]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-options.png
[19]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-open-editor-context-menu.png