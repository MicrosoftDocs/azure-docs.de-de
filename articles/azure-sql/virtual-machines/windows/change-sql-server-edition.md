---
title: Direkte Änderung der SQL Server-Edition
description: Hier erfahren Sie, wie Sie die Edition Ihres virtuellen SQL Server-Computers in Azure ändern, um durch das Herabstufen Kosten zu reduzieren oder durch Upgrades weitere Features zu aktivieren.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/14/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: b6ea6f947b2cd8d7d9c38a0e5e440ce5fd273bb7
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97358825"
---
# <a name="in-place-change-of-sql-server-edition-on-azure-vm"></a>Direkte Änderung der SQL Server-Edition auf der Azure-VM
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In diesem Artikel wird beschrieben, wie Sie die Edition von SQL Server auf einem virtuellen Windows-Computer in Azure ändern. 

Die Edition von SQL Server richtet sich nach dem Product Key und wird während des Installationsvorgangs mithilfe der Installationsmedien angegeben. Die Edition bestimmt, welche [Features](/sql/sql-server/editions-and-components-of-sql-server-2017) im SQL Server-Produkt verfügbar sind. Sie können die SQL Server-Edition mit den Installationsmedien ändern und entweder ein Downgrade durchführen, um die Kosten zu senken, oder ein Upgrade durchführen, um weitere Features zu aktivieren.

Nachdem die Edition von SQL Server intern in den SQL Server-VM geändert wurde, müssen Sie die Eigenschaft „Edition“ von SQL Server im Azure-Portal zu Abrechnungszwecken aktualisieren. 

## <a name="prerequisites"></a>Voraussetzungen

Für eine direkte Änderung der Edition von SQL Server benötigen Sie Folgendes: 

- Ein [Azure-Abonnement](https://azure.microsoft.com/free/).
- Eine mit der [SQL-IaaS-Agent-Erweiterung](sql-agent-extension-manually-register-single-vm.md) registrierte [SQL Server-VM unter Windows](./create-sql-vm-portal.md).
- Setupmedien mit der **gewünschten Edition** von SQL Server. Kunden mit [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) können Ihre Installationsmedien im [Microsoft Business Center](https://www.microsoft.com/Licensing/servicecenter/default.aspx) abrufen. Kunden ohne Software Assurance können die Setupmedien in einem SQL Server-VM-Image aus Azure Marketplace mit der gewünschten Edition verwenden (in der Regel unter `C:\SQLServerFull` gespeichert). 


## <a name="upgrade-an-edition"></a>Upgrade einer Edition

> [!WARNING]
> Durch ein Upgrade der SQL Server-Edition werden der Dienst für SQL Server sowie alle zugeordneten Dienste, z. B. Analysis Services und R Services, neu gestartet. 

Für ein Upgrade der SQL Server-Edition beschaffen Sie sich die SQL Server-Setupmedien für die gewünschte Edition, und führen Sie dann die folgenden Schritte aus:

1. Rufen Sie in den SQL Server-Installationsmedien „Setup.exe“ auf. 
1. Navigieren Sie zu **Wartung**, und wählen Sie die Option **Editionsupgrade** aus. 

   ![Auswahl für das Upgrade der Edition von SQL Server](./media/change-sql-server-edition/edition-upgrade.png)

1. Klicken Sie auf **Weiter**, bis Sie zur Seite **Die Edition kann jetzt aktualisiert werden** gelangen, und wählen Sie dann **Update** aus. Das Setupfenster reagiert möglicherweise einige Minuten nicht mehr, während die Änderung wirksam wird. Auf der Seite **Abgeschlossen** wird bestätigt, dass das Upgrade Ihrer Edition beendet wurde. 

Nach dem Upgrade der SQL Server-Edition ändern Sie die Eigenschaft „Edition“ der SQL Server-VM im Azure-Portal. Dadurch werden die mit dieser VM verbundenen Metadaten und Abrechnungen aktualisiert.

## <a name="downgrade-an-edition"></a>Downgrade einer Edition

Für ein Downgrade der SQL Server-Edition müssen Sie SQL Server vollständig deinstallieren und dann mithilfe der Setupmedien für die gewünschte Edition neu installieren. 

> [!WARNING]
> Das Deinstallieren von SQL Server kann zu zusätzlichen Ausfallzeiten führen. 

Führen Sie für ein Downgrade der SQL Server-Edition die folgenden Schritte aus:

1. Sichern Sie alle Datenbanken, einschließlich der Systemdatenbanken. 
1. Verschieben Sie die Systemdatenbanken (Master, Modell und MSDB) an einen neuen Speicherort. 
1. Führen Sie eine vollständige Deinstallation von SQL Server und aller zugeordneten Dienste durch. 
1. Starten Sie den virtuellen Computer neu. 
1. Installieren Sie SQL Server mithilfe der Medien mit der gewünschten SQL Server-Edition.
1. Installieren Sie die neuesten Service Packs und kumulativen Updates.  
1. Ersetzen Sie die neuen Systemdatenbanken, die während der Installation erstellt wurden, durch die Systemdatenbanken, die Sie zuvor an einen anderen Speicherort verschoben hatten. 

Nach dem Downgrade der SQL Server-Edition ändern Sie die Eigenschaft „Edition“ der SQL Server-VM im Azure-Portal. Dadurch werden die mit dieser VM verbundenen Metadaten und Abrechnungen aktualisiert.

## <a name="change-edition-in-portal"></a>Ändern der Edition im Portal 

Nachdem Sie die Edition von SQL Server mithilfe der Installationsmedien geändert und Ihre SQL Server-VM mit der [SQL-IaaS-Agent-Erweiterung](sql-agent-extension-manually-register-single-vm.md) registriert haben, können Sie die Eigenschaft „Edition“ der SQL Server-VM im Azure-Portal zu Abrechnungszwecken ändern. Gehen Sie dazu folgendermaßen vor: 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. 
1. Navigieren Sie zu Ihrer SQL Server-VM-Ressource. 
1. Wählen Sie **Konfigurieren** unter **Einstellungen** aus. Wählen Sie dann in der Dropdownliste unter **Edition** die gewünschte Edition von SQL Server aus. 

   ![Ändern von Editionsmetadaten](./media/change-sql-server-edition/edition-change-in-portal.png)

1. Nehmen Sie die Warnung zur Kenntnis, dass Sie zuerst die SQL Server-Edition ändern müssen und dass die Eigenschaft „Edition“ der SQL Server-Edition entsprechen muss. 
1. Wählen Sie **Übernehmen** aus, um die Änderungen der Editionsmetadaten anzuwenden. 


## <a name="remarks"></a>Bemerkungen

- Die Eigenschaft „Edition“ der SQL Server-VM muss mit der Edition der SQL Server-Instanz übereinstimmen, die für alle SQL Server-VMs installiert ist, einschließlich der Lizenztypen „Nutzungsbasierte Bezahlung“ und „Bring Your Own License (BYOL)“.
- Wenn Sie Ihre SQL Server-VM-Ressource verwerfen, kehren Sie wieder zur hartcodierten Editionseinstellung des Images zurück.
- Die Möglichkeit, die Edition zu ändern, ist ein Feature der SQL-IaaS-Agent-Erweiterung. Beim Bereitstellen eines Azure Marketplace-Images über das Azure-Portal wird eine SQL Server-VM mit der SQL-IaaS-Agent-Erweiterung automatisch registriert. Kunden, die SQL Server selbst installieren, müssen [ihre SQL Server-VM jedoch manuell registrieren](sql-agent-extension-manually-register-single-vm.md).
- Das Hinzufügen einer SQL Server-VM zu einer Verfügbarkeitsgruppe erfordert die Neuerstellung der VM. Alle einer Verfügbarkeitsgruppe hinzugefügten VMs werden auf die Standardedition zurückgesetzt, und die Edition muss erneut geändert werden.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln: 

* [Übersicht über SQL Server auf einem virtuellen Windows-Computer](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Häufig gestellte Fragen zu SQL Server auf virtuellen Windows-Computern](frequently-asked-questions-faq.md)
* [Preisinformationen für SQL Server auf virtuellen Windows-Computern](pricing-guidance.md)
* [SQL Server auf Windows-VMs – Versionshinweise](doc-changes-updates-release-notes.md)