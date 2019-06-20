---
title: Hochladen einer VHD-Datei in Azure DevTest Labs mithilfe des Microsoft Azure Storage-Explorers | Microsoft-Dokumentation
description: Hochladen einer VHD-Datei in das Speicherkonto eines Labs mithilfe des Microsoft Azure Storage-Explorers
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 3c187d104334fe75ec9e0ce41a3fdc14b508dfb2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60623352"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-microsoft-azure-storage-explorer"></a>Hochladen einer VHD-Datei in das Speicherkonto eines Labs mithilfe des Microsoft Azure Storage-Explorers

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

In Azure DevTest Labs können VHD-Dateien verwendet werden, um benutzerdefinierte Images zu erstellen. Diese können wiederum verwendet werden, um virtuelle Computer bereitzustellen. Dieser Artikel veranschaulicht, wie Sie den [Microsoft Azure Storage-Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) verwenden, um eine VHD-Datei in das Speicherkonto eines Labs hochzuladen. Nachdem Sie die VHD-Datei hochgeladen haben, finden Sie im Abschnitt [Nächste Schritte](#next-steps) einige Artikel, die veranschaulichen, wie aus der hochgeladenen VHD-Datei ein benutzerdefiniertes Image erstellt wird. Weitere Informationen zu Datenträgern und VHDs in Azure finden Sie unter [Einführung in verwaltete Datenträger](../virtual-machines/linux/managed-disks-overview.md).

## <a name="step-by-step-instructions"></a>Schrittweise Anweisungen

Die folgenden Schritte führen Sie durch das Hochladen einer VHD-Datei in DevTest Labs mithilfe des [Microsoft Azure Storage-Explorers](../vs-azure-tools-storage-manage-with-storage-explorer.md).

1. [Laden Sie die neueste Version des Microsoft Azure Storage-Explorers herunter, und installieren Sie sie](https://www.storageexplorer.com).

1. Rufen Sie den Namen des Labspeicherkontos im Azure-Portal ab:

    1. Melden Sie sich beim [Azure-Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) an.
    
    1. Wählen Sie **Alle Dienste** und dann in der Liste die Option **DevTest Labs**.
    
    1. Wählen Sie in der Liste der Labs das gewünschte Lab aus.  
    
    1. Wählen Sie auf dem Blatt des Labs **Konfiguration**aus. 
    
    1. Wählen Sie auf dem Blatt **Konfiguration** für das Lab die Option **Benutzerdefinierte Images (VHDs)** aus.
    
    1. Wählen Sie auf dem Blatt **Benutzerdefinierte Images** die Option **+ Hinzufügen** aus. 
    
    1. Wählen Sie auf dem Blatt **Benutzerdefinierte Images** die Option **VHD** aus.
    
    1. Wählen Sie auf dem Blatt **VHD** die Option **Hochladen einer VHD mithilfe von PowerShell** aus.
    
        ![Hochladen der VHD mithilfe von PowerShell][0]
    
    1. Auf dem Blatt **Image mithilfe von PowerShell hochladen** wird ein Aufruf des Cmdlets **Add-AzureVhd** angezeigt. Der erste Parameter (*Ziel*) enthält den Namen des Speicherkontos für das Lab im folgenden Format:
    
        `https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/...`

    1. Notieren Sie sich das Speicherkonto, da Sie es in späteren Schritten benötigen.
    
1. Stellen Sie über den Storage-Explorer eine Verbindung mit einem Azure-Abonnementkonto her.

    > [!TIP] 
    > 
    > Der Storage-Explorer unterstützt verschiedene Verbindungsoptionen. Dieser Abschnitt veranschaulicht die Herstellung einer Verbindung mit einem Speicherkonto, das mit Ihrem Azure-Abonnement verknüpft ist. Weitere vom Storage-Explorer unterstützte Verbindungsoptionen finden Sie im Artikel [Erste Schritte mit dem Storage-Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md).
 
    1. Öffnen Sie den Storage-Explorer.
    
    1. Wählen Sie im Storage-Explorer die Option **Azure-Kontoeinstellungen** aus. 
    
        ![Azure-Kontoeinstellungen][1]
    
    1. Im linken Bereich werden die Microsoft-Konten angezeigt, bei denen Sie angemeldet sind. Wählen Sie zum Herstellen einer Verbindung mit einem anderen Konto die Option **Konto hinzufügen**aus, und führen Sie die Schritte in den Dialogfeldern durch, um sich mit einem Microsoft-Konto anzumelden, das mindestens einem aktiven Azure-Abonnement zugeordnet ist.
    
        ![Konto hinzufügen][2]
    
    1. Nach erfolgreicher Anmeldung mit einem Microsoft-Konto werden im linken Bereich die Azure-Abonnements angezeigt, die mit dem Konto verknüpft sind. Wählen Sie die Azure-Abonnements aus, die Sie verwenden möchten, und klicken Sie dann auf **Übernehmen**. (Durch Auswählen von **Alle Abonnements** können Sie alle bzw. keine Azure-Abonnements in der Liste auswählen.)
    
        ![Azure-Abonnements auswählen][3]
    
    1. Im linken Bereich werden die Speicherkonten angezeigt, die mit den ausgewählten Azure-Abonnements verknüpft sind.
    
        ![Ausgewählte Azure-Abonnements][4]

1. Suchen Sie das Speicherkonto des Labs:

    1. Suchen Sie im linken Fensterbereich des Storage-Explorers den Knoten für das Azure-Abonnement, zu dem das Lab gehört, und erweitern Sie den Knoten.
    
    1. Erweitern Sie unter dem Knoten des Abonnements den Eintrag **Speicherkonten**.

    1. Erweitern Sie den Speicherkontoknoten des Labs, um die Knoten für **Blobcontainer**, **Dateifreigaben**, **Warteschlangen** und **Tabellen** anzuzeigen.
    
    1. Erweitern Sie den Knoten **Blobcontainer**.
    
    1. Wählen Sie den Blobcontainer für Uploads aus, um den Inhalt im rechten Fensterbereich anzuzeigen.
        
        ![Verzeichnis für Uploads][5]

1. Laden Sie die VHD-Datei mithilfe des Storage-Explorers hoch:

    1. Im rechten Bereich des Storage-Explorers sollte eine Liste der Blobs im Blobcontainer für **Uploads** des Labspeicherkontos angezeigt werden. Wählen Sie auf der Symbolleiste des Blob-Editors die Option **Upload** aus. 
        
        ![Schaltfläche zum Hochladen][6]
    
    1. Wählen Sie im Dropdownmenü **Upload** den Eintrag **Dateien hochladen...** aus.
    
    1. Wählen Sie im Dialogfeld **Dateien hochladen** die Schaltfläche mit den Auslassungspunkten aus.
        
        ![Datei auswählen][8]  

    1. Navigieren Sie im Dialogfeld **Dateien zum Hochladen auswählen** zu der gewünschten VHD-Datei, wählen Sie sie aus, und klicken Sie dann auf **Öffnen**.
    
    1. Ändern Sie im Dialogfeld **Dateien hochladen** den **Blobtyp** zu **Seitenblob**.
    
    1. Wählen Sie die Option **Hochladen**.

        ![Datei auswählen][9]  
    
    1. Der Storage-Explorer-Bereich **Aktivitätsprotokoll** zeigt den Uploadstatus an (zusammen mit Links zum Abbrechen des Uploads). Der Vorgang zum Hochladen kann abhängig von der Größe der VHD-Datei und der Geschwindigkeit Ihrer Verbindung lange dauern. 

        ![Uploadstatus der Datei][10]  

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen eines benutzerdefinierten Images in Azure DevTest Labs aus einer VHD-Datei mit dem Azure-Portal](devtest-lab-create-template.md)
- [Erstellen eines benutzerdefinierten Azure DevTest Labs-Images aus einer VHD-Datei mithilfe von PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)

[0]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-image-using-psh.png
[1]: ./media/devtest-lab-upload-vhd-using-storage-explorer/settings-icon.png
[2]: ./media/devtest-lab-upload-vhd-using-storage-explorer/add-account-link.png
[3]: ./media/devtest-lab-upload-vhd-using-storage-explorer/subscriptions-list.png
[4]: ./media/devtest-lab-upload-vhd-using-storage-explorer/storage-accounts-list.png
[5]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-dir.png
[6]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-button.png
[7]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-files.png
[8]: ./media/devtest-lab-upload-vhd-using-storage-explorer/select-file.png
[9]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-file.png
[10]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-status.png
