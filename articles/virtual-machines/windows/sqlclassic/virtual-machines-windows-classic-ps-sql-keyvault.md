---
title: Integrieren von Key Vault in SQL Server auf Windows-VMs in Azure (klassisch)| Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Konfiguration der SQL Server-Verschlüsselung zur Verwendung mit dem Azure-Schlüsseltresor automatisieren. In diesem Thema wird beschrieben, wie Sie die Azure Key Vault-Integration mit virtuellen SQL Server-Computern zur Erstellung im klassischen Bereitstellungsmodell verwenden.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: ab8d41a7-1971-4032-ab71-eb435c455dc1
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/17/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: e20e2a094e1fd88dfc2a25b586dc6c894f92b418
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62108462"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-classic"></a>Konfigurieren der Azure Key Vault-Integration für SQL Server auf Azure Virtual Machines (klassisch)
> [!div class="op_single_selector"]
> * [Ressourcen-Manager](../sql/virtual-machines-windows-ps-sql-keyvault.md)
> * [Klassisch](../classic/ps-sql-keyvault.md)
> 
> 

## <a name="overview"></a>Übersicht
Es gibt mehrere SQL Server-Verschlüsselungsfeatures, z.B. [Transparent Data Encryption (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [Column Level Encryption (CLE)](https://msdn.microsoft.com/library/ms173744.aspx) und [Sicherungsverschlüsselung](https://msdn.microsoft.com/library/dn449489.aspx). Bei diesen Arten der Verschlüsselung müssen Sie die kryptografischen Schlüssel verwalten und speichern, die Sie für die Verschlüsselung verwenden. Der Azure-Schlüsseltresor-Dienst (Azure Key Vault, AKV) ist dafür ausgelegt, die Sicherheit und Verwaltung dieser Schlüssel an einem sicheren und hoch verfügbaren Speicherort zu verbessern. Mit dem [SQL Server-Connector](https://www.microsoft.com/download/details.aspx?id=45344) kann SQL Server diese Schlüssel aus Azure Key Vault verwenden.

> [!IMPORTANT] 
> Azure verfügt über zwei verschiedene Bereitstellungsmodelle für das Erstellen und Verwenden von Ressourcen: [Resource Manager-Bereitstellungen und klassische Bereitstellungen](../../../azure-resource-manager/resource-manager-deployment-model.md). Dieser Artikel befasst sich mit der Verwendung des klassischen Bereitstellungsmodells. Microsoft empfiehlt für die meisten neuen Bereitstellungen die Verwendung des Ressourcen-Manager-Modells.

Wenn Sie SQL Server mit lokalen Computern ausführen, können Sie die [Schritte zum Zugreifen auf den Azure-Schlüsseltresor von Ihrem lokalen SQL Server-Computer ausführen](https://msdn.microsoft.com/library/dn198405.aspx). Sie können für SQL Server auf virtuellen Azure-Computern aber Zeit sparen, indem Sie die Funktion für die *Azure-Schlüsseltresor-Integration* verwenden. Mit einigen Azure PowerShell-Cmdlets zum Aktivieren dieser Funktion können Sie die Konfiguration automatisieren, die ein virtueller SQL-Computer zum Zugreifen auf Ihren Schlüsseltresor benötigt.

Wenn diese Funktion aktiviert ist, wird der SQL Server-Connector automatisch aktiviert und der Anbieter für erweiterbare Schlüsselverwaltung für den Zugriff auf den Azure-Schlüsseltresor konfiguriert. Außerdem werden die Anmeldeinformationen erstellt, um den Zugriff auf Ihren Tresor zu ermöglichen. Wenn Sie sich die Schritte in der oben erwähnten Dokumentation für die Ausführung auf lokalen Computern angesehen haben, haben Sie erfahren, dass die Schritte 2 und 3 mit dieser Funktion automatisiert werden. Der einzige Schritt, den Sie noch manuell ausführen müssen, ist die Erstellung des Schlüsseltresors und der Schlüssel. Ab diesem Punkt ist das gesamte Setup des virtuellen SQL-Computers automatisiert. Nachdem die Funktion dieses Setup abgeschlossen hat, können Sie die T-SQL-Anweisungen ausführen, um mit der Verschlüsselung Ihrer Datenbanken oder Backups zu beginnen, wie Sie dies normalerweise auch tun.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="configure-akv-integration"></a>Konfigurieren der Integration des Azure-Schlüsseltresors
Verwenden Sie PowerShell zum Konfigurieren der Azure-Schlüsseltresor-Integration. Die folgenden Abschnitte enthalten eine Übersicht über die erforderlichen Parameter sowie ein PowerShell-Beispielskript.

### <a name="install-the-sql-server-iaas-extension"></a>Installieren der Erweiterung für SQL Server-IaaS
[Installieren Sie zunächst die Erweiterung für SQL Server-IaaS.](../classic/sql-server-agent-extension.md)

### <a name="understand-the-input-parameters"></a>Grundlegendes zu den Eingabeparametern
In der folgenden Tabelle sind die Parameter aufgeführt, die zum Ausführen des PowerShell-Skripts im nächsten Abschnitt erforderlich sind.

| Parameter | BESCHREIBUNG | Beispiel |
| --- | --- | --- |
| **$akvURL** |**Schlüsseltresor-URL** |"https:\//contosokeyvault.vault.azure.net/" |
| **$spName** |**Dienstprinzipalname** |"fde2b411-33d5-4e11-af04eb07b669ccf2" |
| **$spSecret** |**Geheimer Schlüssel des Dienstprinzipals** |"9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM=" |
| **$credName** |**Name der Anmeldeinformationen**: Die Azure-Schlüsseltresor-Integration erstellt Anmeldeinformationen in SQL Server, damit die virtuelle Maschine Zugriff auf den Schlüsseltresor hat. Wählen Sie einen Namen für diese Anmeldeinformation. |"mycred1" |
| **$vmName** |**Name des virtuellen Computers**: Der Name eines bereits erstellten SQL-Computers. |"myvmname" |
| **$serviceName** |**Dienstname**: Der Clouddienstname, der dem virtuellen SQL-Computer zugeordnet ist. |"mycloudservicename" |

### <a name="enable-akv-integration-with-powershell"></a>Aktivieren der Azure-Schlüsseltresor-Integration mit PowerShell
Mit dem **New-AzureVMSqlServerKeyVaultCredentialConfig** -Cmdlet wird ein Konfigurationsobjekt für die Funktion „Azure-Schlüsseltresor-Integration“ erstellt. Mit **Set-AzureVMSqlServerExtension** wird diese Integration mit dem Parameter **KeyVaultCredentialSettings** konfiguriert. Die folgenden Schritte zeigen, wie Sie diese Befehle verwenden.

1. Konfigurieren Sie in Azure PowerShell zuerst die Eingabeparameter mit Ihren speziellen Werten, wie dies in den vorherigen Abschnitten dieses Themas beschrieben ist. Das folgende Skript ist ein Beispiel.
   
        $akvURL = "https:\//contosokeyvault.vault.azure.net/"
        $spName = "fde2b411-33d5-4e11-af04eb07b669ccf2"
        $spSecret = "9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM="
        $credName = "mycred1"
        $vmName = "myvmname"
        $serviceName = "mycloudservicename"
2. Verwenden Sie anschließend das folgende Skript, um die Azure-Schlüsseltresor-Integration zu konfigurieren.
   
        $secureakv =  $spSecret | ConvertTo-SecureString -AsPlainText -Force
        $akvs = New-AzureVMSqlServerKeyVaultCredentialConfig -Enable -CredentialName $credname -AzureKeyVaultUrl $akvURL -ServicePrincipalName $spName -ServicePrincipalSecret $secureakv
        Get-AzureVM -ServiceName $serviceName -Name $vmName | Set-AzureVMSqlServerExtension -KeyVaultCredentialSettings $akvs | Update-AzureVM

Über die SQL-IaaS-Agent-Erweiterung wird der virtuelle SQL-Computer mit dieser neuen Konfiguration aktualisiert.

[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]

