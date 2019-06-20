---
title: 'Verschieben von Blob Storage-Daten mit Azure Storage-Explorer: Team Data Science-Prozess'
description: Verschieben von Daten in und aus Azure Blob Storage mithilfe des Azure Storage-Explorers
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 8ec4289c641b3f140950575ac2f8016f894f2c9c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61430750"
---
# <a name="move-data-to-and-from-azure-blob-storage-using-azure-storage-explorer"></a>Verschieben von Daten in und aus Azure Blob Storage mithilfe des Azure Storage-Explorers
Der Azure Storage-Explorer ist ein kostenloses Tool von Microsoft, das Ihnen das Arbeiten mit Azure Storage-Daten unter Windows, macOS und Linux ermöglicht. Dieses Thema beschreibt Verwendung des Tools zum Hoch- und Herunterladen von Daten aus Azure Blob Storage. Das Tool kann von der Seite [Microsoft Azure Storage-Explorer](https://storageexplorer.com/)heruntergeladen werden.

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

> [!NOTE]
> Wenn Sie einen virtuellen Computer verwenden, der mit den von den [virtuellen Data Science-Computern in Azure](virtual-machines.md) bereitgestellten Skripts eingerichtet wurde, ist der Azure Storage-Explorer bereits auf dem virtuellen Computer installiert.
> 
> [!NOTE]
> Eine umfassende Einführung in Azure Blob Storage finden Sie unter [Grundlagen zu Azure Blob Storage](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) und [Azure-Blobdienst](https://msdn.microsoft.com/library/azure/dd179376.aspx).   
> 
> 

## <a name="prerequisites"></a>Voraussetzungen
In diesem Dokument wird davon ausgegangen, dass Sie über ein Azure-Abonnement, ein Speicherkonto und den zugehörigen Speicherschlüssel für dieses Konto verfügen. Bevor Sie Daten hoch- und herunterladen können, müssen Sie den Namen Ihres Azure-Speicherkontos und den Kontoschlüssel kennen. 

* Informationen zum Einrichten eines Azure-Abonnements finden Sie unter [Kostenlose Testversion](https://azure.microsoft.com/pricing/free-trial/).
* Anleitungen zum Erstellen eines Speicherkontos und zum Abrufen von Konto- und Schlüsselinformationen finden Sie unter [Informationen zu Azure-Speicherkonten](../../storage/common/storage-create-storage-account.md). Notieren Sie sich den Zugriffsschlüssel für Ihr Speicherkonto, da Sie diesen Schlüssel benötigen, um über das Tool Azure Storage-Explorer eine Verbindung mit dem Konto herzustellen.
* Der Azure Storage-Explorer kann von der Seite [Microsoft Azure Storage-Explorer](https://storageexplorer.com/) heruntergeladen werden. Übernehmen Sie während der Installation die Standardwerte.

<a id="explorer"></a>

## <a name="use-azure-storage-explorer"></a>Verwenden von Azure Storage-Explorer
Die folgenden Schritte beschreiben das Hoch- und Herunterladen von Daten mithilfe des Azure Storage-Explorers. 

1. Starten Sie den Microsoft Azure Storage-Explorer.
2. Um den Assistenten **Bei Ihrem Konto anmelden...** zu öffnen, wählen Sie das Symbol **Azure-Kontoeinstellungen** und dann die Option **Konto hinzufügen** aus, und geben Sie Ihre Anmeldeinformationen ein. ![Azure Storage-Konto hinzufügen](./media/move-data-to-azure-blob-using-azure-storage-explorer/add-an-azure-store-account.png)
3. Um den Assistenten **Verbindung mit Azure-Speicher herstellen** zu öffnen, wählen Sie das Symbol **Verbindung mit Azure-Speicher herstellen** aus. ![Klicken auf „Connect to Azure storage“ (Mit Azure Storage verbinden)](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-1.png)
4. Geben Sie den Zugriffsschlüssel Ihres Azure-Speicherkontos in den Assistenten **Verbindung mit Azure-Speicher herstellen** ein, und klicken Sie dann auf **Weiter**. ![Eingeben des Zugriffsschlüssels für das Azure Storage-Konto](./media/move-data-to-azure-blob-using-azure-storage-explorer/connect-to-azure-storage-2.png)
5. Geben Sie im Feld **Kontoname** den Namen des Speicherkontos ein, und wählen Sie **Weiter** aus. ![Externen Speicher anfügen](./media/move-data-to-azure-blob-using-azure-storage-explorer/attach-external-storage.png)
6. Das hinzugefügte Speicherkonto sollte jetzt aufgeführt werden. Um in einem Speicherkonto einen Blobcontainer zu erstellen, klicken Sie mit der rechten Maustaste auf den Knoten **Blobcontaine**r in diesem Konto, wählen Sie die Option **Blobcontainer erstellen** aus, und geben Sie einen Namen ein.
7. Um Daten in einen Container hochzuladen, wählen Sie den Zielcontainer aus, und klicken Sie auf die Schaltfläche **Hochladen**.![Speicherkonten](./media/move-data-to-azure-blob-using-azure-storage-explorer/storage-accounts.png)
8. Klicken Sie rechts neben dem Feld **Dateien** auf die Schaltfläche **...** , wählen Sie im Dateisystem eine oder mehrere Dateien zum Hochladen aus, und klicken Sie auf **Hochladen**, um mit dem Hochladen der Dateien zu beginnen.![Dateien hochladen](./media/move-data-to-azure-blob-using-azure-storage-explorer/upload-files-to-blob.png)
9. Um Daten herunterzuladen, wählen Sie das Blob im entsprechenden Container aus und klicken auf **Herunterladen**. ![Dateien herunterladen](./media/move-data-to-azure-blob-using-azure-storage-explorer/download-files-from-blob.png)

