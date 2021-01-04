---
title: 'Verschieben von Blob Storage-Daten mit Azure Storage-Explorer: Team Data Science-Prozess'
description: Erfahren Sie, wie Sie mit dem Azure Storage-Explorer Daten aus Azure Blob Storage hoch- und herunterladen können.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c5750132280d69c946ce180c51e4de6507549fcb
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93318174"
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>Verschieben von Daten in und aus Azure Blob Storage mithilfe des Azure Storage-Explorers
Der Azure Storage-Explorer ist ein kostenloses Tool von Microsoft, das Ihnen das Arbeiten mit Azure Storage-Daten unter Windows, macOS und Linux ermöglicht. Dieses Thema beschreibt Verwendung des Tools zum Hoch- und Herunterladen von Daten aus Azure Blob Storage. Das Tool kann von der Seite [Microsoft Azure Storage-Explorer](https://storageexplorer.com/)heruntergeladen werden.

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Wenn Sie einen virtuellen Computer verwenden, der mit den von den [virtuellen Data Science-Computern in Azure](../data-science-virtual-machine/overview.md) bereitgestellten Skripts eingerichtet wurde, ist der Azure Storage-Explorer bereits auf dem virtuellen Computer installiert.
> 
> [!NOTE]
> Eine umfassende Einführung in Azure Blob Storage finden Sie unter [Grundlagen zu Azure Blob Storage](../../storage/blobs/storage-quickstart-blobs-dotnet.md) und [Azure-Blobdienst](/rest/api/storageservices/Blob-Service-Concepts).   
> 
> 

## <a name="prerequisites"></a>Voraussetzungen
In diesem Dokument wird davon ausgegangen, dass Sie über ein Azure-Abonnement, ein Speicherkonto und den zugehörigen Speicherschlüssel für dieses Konto verfügen. Um Daten hoch- und herunterladen zu können, müssen Sie den Namen Ihres Azure Storage-Kontos und den Kontoschlüssel kennen. 

* Informationen zum Einrichten eines Azure-Abonnements finden Sie unter [Kostenlose Testversion](https://azure.microsoft.com/pricing/free-trial/).
* Anleitungen zum Erstellen eines Speicherkontos und zum Abrufen von Konto- und Schlüsselinformationen finden Sie unter [Erstellen eines Azure-Speicherkontos](../../storage/common/storage-account-create.md). Notieren Sie sich den Zugriffsschlüssel für Ihr Speicherkonto, da Sie diesen Schlüssel benötigen, um über das Tool Azure Storage-Explorer eine Verbindung mit dem Konto herzustellen.
* Der Azure Storage-Explorer kann von der Seite [Microsoft Azure Storage-Explorer](https://storageexplorer.com/) heruntergeladen werden. Übernehmen Sie während der Installation die Standardwerte.

<a id="explorer"></a>

## <a name="use-azure-storage-explorer"></a>Verwenden von Azure Storage-Explorer
Die folgenden Schritte beschreiben das Hoch- und Herunterladen von Daten mithilfe des Azure Storage-Explorers. 

1. Starten Sie den Microsoft Azure Storage-Explorer.
2. Um den Assistenten **Bei Ihrem Konto anmelden...** zu öffnen, wählen Sie das Symbol **Azure-Kontoeinstellungen** und dann die Option **Konto hinzufügen** aus, und geben Sie Ihre Anmeldeinformationen ein. 
![Hinzufügen eines Azure Storage-Kontos](./media/move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3. Wählen Sie zum Öffnen des Assistenten **Verbindung mit Azure Storage herstellen** das Symbol **Verbindung mit Azure Storage herstellen** aus. ![Klicken auf „Verbindung mit Azure Storage herstellen“](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. Geben Sie den Zugriffsschlüssel Ihres Azure Storage-Kontos in den Assistenten **Verbindung mit Azure Storage herstellen** ein, und klicken Sie anschließend auf **Weiter**. ![Eingeben des Zugriffsschlüssels des Azure Storage-Kontos](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. Geben Sie im Feld **Kontoname** den Namen des Speicherkontos ein, und wählen Sie **Weiter** aus. ![Externen Speicher anfügen](./media/move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. Das hinzugefügte Speicherkonto sollte nun angezeigt werden. Um in einem Speicherkonto einen Blobcontainer zu erstellen, klicken Sie mit der rechten Maustaste auf den Knoten **Blobcontaine** r in diesem Konto, wählen Sie die Option **Blobcontainer erstellen** aus, und geben Sie einen Namen ein.
7. Um Daten in einen Container hochzuladen, wählen Sie den Zielcontainer aus, und klicken Sie auf die Schaltfläche **Hochladen**.
![Speicherkonten](./media/move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. Klicken Sie rechts neben dem Feld **Dateien** auf die Schaltfläche **...** , wählen Sie im Dateisystem eine oder mehrere Dateien zum Hochladen aus, und klicken Sie auf **Hochladen** , um mit dem Hochladen der Dateien zu beginnen.![Dateien hochladen](./media/move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
9. Um Daten herunterzuladen, wählen Sie das Blob im entsprechenden Container aus und klicken auf **Herunterladen**. ![Dateien herunterladen](./media/move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)