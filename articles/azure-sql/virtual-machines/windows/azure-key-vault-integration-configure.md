---
title: Integrieren von Key Vault in SQL Server auf Windows-VMs in Azure (Resource Manager) | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Konfiguration der SQL Server-Verschlüsselung zur Verwendung mit dem Azure-Schlüsseltresor automatisieren. In diesem Thema wird beschrieben, wie Sie die Azure Key Vault-Integration für SQL-VMs verwenden, die mit dem Resource Manager erstellt wurden.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: cd66dfb1-0e9b-4fb0-a471-9deaf4ab4ab8
ms.service: virtual-machines-sql
ms.subservice: security
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/30/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: a6955b7fc4948faaea6db426545f8cc3d1eece35
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359896"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-vms-resource-manager"></a>Konfigurieren der Azure Key Vault-Integration für SQL Server auf Azure-VMs (Resource Manager)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Es gibt mehrere SQL Server-Verschlüsselungsfeatures, z.B. [Transparent Data Encryption (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption), [Column Level Encryption (CLE)](/sql/t-sql/functions/cryptographic-functions-transact-sql) und [Sicherungsverschlüsselung](/sql/relational-databases/backup-restore/backup-encryption). Bei diesen Arten der Verschlüsselung müssen Sie die kryptografischen Schlüssel verwalten und speichern, die Sie für die Verschlüsselung verwenden. Der Azure Key Vault-Dienst ist darauf ausgelegt, die Sicherheit und Verwaltung dieser Schlüssel an einem sicheren und hoch verfügbaren Speicherort zu verbessern. Mit dem [SQL Server-Connector](https://www.microsoft.com/download/details.aspx?id=45344) kann SQL Server diese Schlüssel aus Azure Key Vault verwenden.

Wenn Sie SQL Server lokal ausführen, können Sie [Schritte zum Zugriff auf Azure Key Vault von Ihrer lokalen SQL Server-Instanz](/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server) ausführen. Für SQL Server auf Azure-VMs können Sie aber Zeit sparen, indem Sie das Feature für die *Azure Key Vault-Integration* nutzen.

Wenn diese Funktion aktiviert ist, wird der SQL Server-Connector automatisch aktiviert und der Anbieter für erweiterbare Schlüsselverwaltung für den Zugriff auf den Azure-Schlüsseltresor konfiguriert. Außerdem werden die Anmeldeinformationen erstellt, um den Zugriff auf Ihren Tresor zu ermöglichen. Wenn Sie sich die Schritte in der oben erwähnten Dokumentation für die Ausführung auf lokalen Computern angesehen haben, haben Sie erfahren, dass die Schritte 2 und 3 mit dieser Funktion automatisiert werden. Der einzige Schritt, den Sie noch manuell ausführen müssen, ist die Erstellung des Schlüsseltresors und der Schlüssel. Ab diesem Punkt ist das gesamte Setup Ihrer SQL Server-VM automatisiert. Nachdem das Setup über das Feature abgeschlossen wurde, können Sie Transact-SQL-Anweisungen ausführen, um wie gewohnt mit der Verschlüsselung Ihrer Datenbanken oder Sicherungen zu beginnen.

[!INCLUDE [Prepare for Key Vault integration](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

  >[!NOTE]
  > Version 1.0.4.0 des Anbieters für die erweiterte Schlüsselverwaltung wird auf der SQL Server-VM über die [SQL- IaaS-Erweiterung](./sql-server-iaas-agent-extension-automate-management.md) installiert. Ein Upgrade der SQL-IaaS-Erweiterung bewirkt nicht, dass die Anbieterversion aktualisiert wird. Dies bedeutet, dass Sie bei Bedarf ein manuelles Upgrade der Anbieterversion für die erweiterbare Schlüsselverwaltung durchführen sollten (z. B. bei der Migration zu SQL Managed Instance).


## <a name="enabling-and-configuring-key-vault-integration"></a>Aktivieren und Konfigurieren der Azure Key Vault-Integration
Sie können die Azure Key Vault-Integration während der Bereitstellung aktivieren oder für vorhandene VMs konfigurieren.

### <a name="new-vms"></a>Neue VMs
Wenn Sie mithilfe von Resource Manager eine neuen SQL-VM bereitstellen, bietet das Azure-Portal eine Möglichkeit zum Aktivieren der Azure Key Vault-Integration. Das Azure Key Vault-Feature ist nur für die Enterprise-, Developer- und Evaluation-Edition von SQL Server verfügbar.

![SQL – Azure Key Vault-Integration](./media/azure-key-vault-integration-configure/azure-sql-arm-akv.png)

Eine ausführliche exemplarische Vorgehensweise zur Bereitstellung finden Sie im Artikel zum [Bereitstellen einer SQL-VM im Azure-Portal](create-sql-vm-portal.md).

### <a name="existing-vms"></a>Vorhandene VMs

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Für vorhandene SQL-VMs öffnen Sie die Ihre [SQL-VM-Ressource](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) und wählen dann in den **Einstellungen** die Option **Sicherheit** aus. Wählen Sie **Aktivieren** aus, um die Azure Key Vault-Integration zu ermöglichen. 

![Key Vault-Integration für vorhandene SQL-VMs](./media/azure-key-vault-integration-configure/azure-sql-rm-akv-existing-vms.png)

Wenn Sie fertig sind, klicken Sie unten auf der Seite **Sicherheit** auf die Schaltfläche **Übernehmen**, um Ihre Änderungen zu speichern.

> [!NOTE]
> Der hier erstellte Anmeldeinformationsname wird später einem SQL-Anmeldenamen zugeordnet. Dadurch wird dem SQL-Anmeldenamen der Zugriff auf den Schlüsseltresor ermöglicht. 


> [!NOTE]
> Sie können die Key Vault-Integration auch mithilfe einer Vorlage konfigurieren. Weitere Informationen finden Sie in der [Azure-Schnellstartvorlage für die Azure Key Vault-Integration](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update).


[!INCLUDE [Key Vault integration next steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]