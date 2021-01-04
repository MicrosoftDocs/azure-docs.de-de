---
title: Azure Analysis Services-Datenbanksicherung und -wiederherstellung | Microsoft-Dokumentation
description: In diesem Artikel wird das Sichern und Wiederherstellen von Modellmetadaten und Daten aus einer Azure Analysis Services-Datenbank beschrieben.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: af1850f77c1d13c761bfc2a143074b5067b349b4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013002"
---
# <a name="analysis-services-database-backup-and-restore"></a>Sichern und Wiederherstellen von Analysis Services-Datenbanken

Das Sichern von Datenbanken für tabellarische Modelle in Azure Analysis Services ähnelt dem Sichern von lokalem Analysis Services. Der Hauptunterschied besteht im Speicherort der Sicherungsdateien. Sicherungsdateien müssen in einem Container in einem [Azure Storage-Konto](../storage/common/storage-account-create.md) gespeichert werden. Sie können ein bereits vorhandenes Speicherkonto und einen vorhandenen Container verwenden, oder beim Konfigurieren der Speichereinstellungen für den Server können ein Speicherkonto und ein Container erstellt werden.

> [!NOTE]
> Wenn Sie ein Speicherkonto erstellen, wird unter Umständen auch ein neuer abrechenbarer Dienst erstellt. Weitere Informationen finden Sie unter [Preise für Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).
> 
> 

> [!NOTE]
> Wenn sich das Speicherkonto in einer anderen Region befindet, konfigurieren Sie die Firewalleinstellungen für das Speicherkonto so, dass der Zugriff über **ausgewählte Netzwerke** zulässig ist. Geben Sie im **Adressbereich** der Firewall den IP-Adressbereich für die Region an, in der sich der Analysis Services-Server befindet. Die Konfiguration der Firewalleinstellungen für das Speicherkonto wird unterstützt, um den Zugriff von allen Netzwerken zuzulassen. Allerdings wird die Auswahl bestimmter Netzwerke und die Angabe eines IP-Adressbereichs bevorzugt. Weitere Informationen finden Sie unter [Häufig gestellte Fragen zur Netzwerkkonnektivität von Analysis Services](analysis-services-network-faq.md#backup-and-restore).

Sicherungen werden mit der Erweiterung ABF gespeichert. Für tabellarische In-Memory-Modelle werden sowohl die Modelldaten als auch die Metadaten gespeichert. Für tabellarische DirectQuery-Modelle werden nur die Modellmetadaten gespeichert. Sicherungen können in Abhängigkeit von den gewählten Optionen komprimiert und verschlüsselt werden.


## <a name="configure-storage-settings"></a>Konfigurieren von Speichereinstellungen
Bevor Sie eine Sicherung durchführen, müssen Sie die Speichereinstellungen für den Server konfigurieren.


### <a name="to-configure-storage-settings"></a>So konfigurieren Sie Speichereinstellungen
1.  Klicken Sie im Azure-Portal auf **Einstellungen** > **Sicherung**.

    ![Sicherungen in den Einstellungen](./media/analysis-services-backup/aas-backup-backups.png)

2.  Klicken Sie auf **Aktiviert** und dann auf **Speichereinstellungen**.

    ![Aktivieren](./media/analysis-services-backup/aas-backup-enable.png)

3. Wählen Sie Ihr Speicherkonto aus, oder erstellen Sie ein neues.

4. Wählen Sie einen Container aus, oder erstellen Sie einen neuen.

    ![Container auswählen](./media/analysis-services-backup/aas-backup-container.png)

5. Speichern Sie die Sicherungseinstellungen.

    ![Sicherungseinstellungen speichern](./media/analysis-services-backup/aas-backup-save.png)

## <a name="backup"></a>Backup

### <a name="to-backup-by-using-ssms"></a>So führen Sie eine Sicherung mithilfe von SSMS durch

1. Klicken Sie in SSMS mit der rechten Maustaste auf eine Datenbank > **Sichern**.

2. Klicken Sie unter **Datenbank sichern** > **Sicherungsdatei** auf **Durchsuchen**.

3. Überprüfen Sie im Dialogfeld **Datei speichern unter** den Ordnerpfad, und geben Sie einen Namen für die Sicherungsdatei ein. 

4. Wählen Sie im Dialogfeld **Datenbank sichern** Optionen aus.

    **Überschreiben von Dateien zulassen**: Wählen Sie diese Option aus, um Sicherungsdateien mit identischem Namen zu überschreiben. Wenn diese Option nicht ausgewählt ist, darf die Datei, die Sie speichern, nicht denselben Namen wie eine Datei haben, die an diesem Speicherort bereits vorhanden ist.

    **Komprimierung anwenden**: Wählen Sie diese Option aus, um die Sicherungsdatei zu komprimieren. Mit komprimierten Sicherungsdateien sparen Sie Speicherplatz, allerdings wird die CPU-Auslastung geringfügig erhöht. 

    **Sicherungsdatei verschlüsseln**: Wählen Sie diese Option aus, um die Sicherungsdatei zu verschlüsseln. Für diese Option muss vom Benutzer ein Kennwort angegeben werden, um die Sicherungsdatei zu sichern. Mit dem Kennwort wird verhindert, dass die Sicherungsdaten außer bei einem Wiederherstellungsvorgang gelesen werden. Wenn Sie Sicherungen verschlüsseln, speichern Sie das Kennwort an einem sicheren Ort.

5. Klicken Sie auf **OK**, um die Sicherungsdatei zu erstellen und zu speichern.


### <a name="powershell"></a>PowerShell
Verwenden Sie das Cmdlet [Backup-ASDatabase](/powershell/module/sqlserver/backup-asdatabase).

## <a name="restore"></a>Restore
Bei der Wiederherstellung muss sich die Sicherungsdatei im Speicherkonto befinden, das Sie für den Server konfiguriert haben. Wenn Sie eine Sicherungsdatei aus einem lokalen Speicherort in Ihr Speicherkonto verschieben müssen, verwenden Sie den [Microsoft Azure Storage-Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) oder das Befehlszeilenprogramm [AzCopy](../storage/common/storage-use-azcopy-v10.md). 



> [!NOTE]
> Wenn Sie mithilfe eines lokalen Servers eine Wiederherstellung durchführen, müssen Sie alle Domänenbenutzer aus den Rollen des Modells entfernen und sie als Azure Active Directory-Benutzer den Rollen wieder hinzufügen.
> 
> 

### <a name="to-restore-by-using-ssms"></a>So führen Sie eine Wiederherstellung mithilfe von SSMS durch

1. Klicken Sie in SSMS mit der rechten Maustaste auf eine Datenbank > **Wiederherstellen**.

2. Klicken Sie im Dialogfeld **Datenbank sichern** unter **Sicherungsdatei** auf **Durchsuchen**.

3. Wählen Sie im Dialogfeld **Datenbankdateien suchen** die Datei aus, die Sie wiederherstellen möchten.

4. Wählen Sie die Datenbank unter **Datenbank wiederherstellen** aus.

5. Geben Sie Optionen an. Die Sicherheitsoptionen müssen mit den Sicherungsoptionen übereinstimmen, die Sie beim Sichern verwendet haben.


### <a name="powershell"></a>PowerShell

Verwenden Sie das Cmdlet [Restore-ASDatabase](/powershell/module/sqlserver/restore-asdatabase).


## <a name="related-information"></a>Verwandte Informationen

[Azure-Speicherkonten](../storage/common/storage-account-create.md)  
[Hochverfügbarkeit](analysis-services-bcdr.md)      
[Häufig gestellte Fragen zu Netzwerkkonnektivität von Analysis Services](analysis-services-network-faq.md)