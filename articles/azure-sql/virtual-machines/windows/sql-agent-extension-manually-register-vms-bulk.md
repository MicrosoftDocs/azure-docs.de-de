---
title: Registrieren mehrerer SQL Server-VMs mit der SQL-IaaS-Agent-Erweiterung in Azure
description: Durch eine Massenregistrierung von SQL Server-VMs mit der SQL-IaaS-Agent-Erweiterung können Sie die Verwaltbarkeit verbessern.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 558daede55f6563155d3f54e97d77c0a3ca4de59
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97357227"
---
# <a name="register-multiple-sql-vms-in-azure-with-the-sql-iaas-agent-extension"></a>Registrieren mehrerer SQL Server-VMs mit der SQL-IaaS-Agent-Erweiterung in Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In diesem Artikel wird beschrieben, wie Sie mithilfe des Azure PowerShell-Cmdlets `Register-SqlVMs` Ihre virtuellen SQL Server-Computer (Virtual Machines, VMs) mit der [SQL-IaaS-Agent-Erweiterung](sql-server-iaas-agent-extension-automate-management.md) in einem Massenvorgang registrieren. 


In diesem Artikel erfahren Sie, wie Sie SQL Server-VMs in einem Massenvorgang manuell registrieren. Alternativ können Sie [alle SQL Server-VMs automatisch](sql-agent-extension-automatic-registration-all-vms.md) oder [einzelne SQL Server-VMs manuell](sql-agent-extension-manually-register-single-vm.md) registrieren. 

## <a name="overview"></a>Übersicht

Mit dem Cmdlet `Register-SqlVMs` können alle virtuellen Computer in einer bestimmten Liste von Abonnements, Ressourcengruppen oder einer Liste mit bestimmten virtuellen Computern registriert werden. Mit dem Cmdlet werden die virtuellen Computer im [Verwaltungsmodus „Lightweight“](sql-server-iaas-agent-extension-automate-management.md#management-modes) registriert, und dann werden [ein Bericht und eine Protokolldatei](#output-description) generiert. 

Der Registrierungsprozess birgt keine Risiken, führt nicht zu Ausfallzeiten und startet den SQL Server-Dienst oder den virtuellen Computer nicht neu. 

## <a name="prerequisites"></a>Voraussetzungen

Um Ihre SQL Server-VM mit der Erweiterung registrieren zu können, benötigen Sie Folgendes: 

- Ein [Azure-Abonnement](https://azure.microsoft.com/free/), das [beim Anbieter **Microsoft.SqlVirtualMachine** registriert](sql-agent-extension-manually-register-single-vm.md#register-subscription-with-rp) wurde und nicht registrierte SQL Server-VMs enthält. 
- Die zum Registrieren der virtuellen Computer verwendeten Clientanmeldeinformationen sind in einer der folgenden Azure-Rollen vorhanden: **Mitwirkender von virtuellen Computern**, **Mitwirkender** oder **Besitzer**. 
- Die aktuelle Version von [Azure PowerShell (mindestens Version 5.0)](/powershell/azure/new-azureps-module-az). 


## <a name="get-started"></a>Erste Schritte

Bevor Sie fortfahren, müssen Sie zuerst eine lokale Kopie des Skripts erstellen, die Kopie als PowerShell-Modul importieren und eine Verbindung mit Azure herstellen. 

### <a name="create-the-script"></a>Erstellen Sie das Skript.

Zum Erstellen des Skripts kopieren Sie das [vollständige Skript](#full-script) am Ende dieses Artikels und speichern Sie es lokal als `RegisterSqlVMs.psm1`. 

### <a name="import-the-script"></a>Importieren des Skripts

Nachdem das Skript erstellt wurde, können Sie es als Modul in das PowerShell-Terminal importieren. 

Öffnen Sie ein PowerShell-Verwaltungsterminal und navigieren Sie zum Speicherort der `RegisterSqlVMs.psm1`-Datei. Führen Sie dann das folgende PowerShell-Cmdlet aus, um das Skript als Modul zu importieren: 

```powershell-interactive
Import-Module .\RegisterSqlVMs.psm1
```

### <a name="connect-to-azure"></a>Herstellen einer Verbindung mit Azure

Verwenden Sie das folgende PowerShell-Cmdlet, um eine Verbindung zu Azure herzustellen:

```powershell-interactive
Connect-AzAccount
```


## <a name="all-vms-in-a-list-of-subscriptions"></a>Alle VMs in einer Liste der Abonnements 

Verwenden Sie das folgende Cmdlet, um alle SQL Server-VMs in einer Liste der Abonnements zu registrieren:

```powershell-interactive
Register-SqlVMs -SubscriptionList SubscriptionId1,SubscriptionId2
```

Beispielausgabe: 

```
Number of subscriptions registration failed for 
because you do not have access or credentials are wrong: 1
Total VMs Found: 10
VMs Already registered: 1
Number of VMs registered successfully: 4
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 3
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-a-single-subscription"></a>Alle VMs in einem einzelnen Abonnement

Verwenden Sie das folgende Cmdlet, um alle SQL Server-VMs in einem einzelnen Abonnement zu registrieren: 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1
```

Beispielausgabe:

```
Total VMs Found: 10
VMs Already registered: 1
Number of VMs registered successfully: 5
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the  guest agent on VM is not running: 2
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-multiple-resource-groups"></a>Alle VMs in mehreren Ressourcengruppen

Verwenden Sie das folgende Cmdlet, um alle SQL Server-VMs in mehreren Ressourcengruppen in einem einzelnen Abonnement zu registrieren:

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupList ResourceGroup1,ResourceGroup2
```

Beispielausgabe:

```
Total VMs Found: 4
VMs Already registered: 1
Number of VMs registered successfully: 1
Number of VMs failed to register due to error: 1
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-a-resource-group"></a>Alle VMs in einer Ressourcengruppe

Verwenden Sie das folgende Cmdlet, um alle SQL Server-VMs in einer einzelnen Ressourcengruppe zu registrieren: 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1
```

Beispielausgabe:

```
Total VMs Found: 4
VMs Already registered: 1
Number of VMs registered successfully: 1
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="specific-vms-in-a-single-resource-group"></a>Bestimmte VMs in einer einzelnen Ressourcengruppe

Verwenden Sie das folgende Cmdlet, um bestimmte SQL Server-VMs in einer einzelnen Ressourcengruppe zu registrieren:

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1 -VmList VM1,VM2,VM3
```

Beispielausgabe:

```
Total VMs Found: 3
VMs Already registered: 0
Number of VMs registered successfully: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 1
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="a-specific-vm"></a>Eine bestimmte VM

Verwenden Sie das folgende Cmdlet, um eine bestimmte SQL Server-VM zu registrieren: 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1 -Name VM1
```

Beispielausgabe:

```
Total VMs Found: 1
VMs Already registered: 0
Number of VMs registered successfully: 1

Please find the detailed report in  file RegisterSqlVMScriptReport1571314821.txt
```


## <a name="output-description"></a>Ausgabebeschreibung

Bei jeder Verwendung des `Register-SqlVMs`-Cmdlets werden ein Bericht und eine Protokolldatei generiert. 

### <a name="report"></a>Bericht

Der Bericht wird als `.txt`-Datei mit dem Namen `RegisterSqlVMScriptReport<Timestamp>.txt` erstellt, wobei der Zeitstempel dem Zeitpunkt entspricht, an dem das Cmdlet gestartet wurde. Im Bericht werden die folgenden Details aufgelistet:

| **Ausgabewert** | **Beschreibung** |
| :--------------  | :-------------- | 
| Anzahl der Abonnements, für die die Registrierung fehlgeschlagen ist, weil Sie über keine Zugriffsrechte verfügen oder die Anmeldeinformationen falsch sind | Stellt die Anzahl und die Liste der Abonnements zur Verfügung, bei denen mit der angegebenen Authentifizierung Probleme aufgetreten sind. Die ausführliche Fehlermeldung finden Sie in der Protokolldatei, indem Sie nach der Abonnement-ID suchen. | 
| Anzahl der Abonnements, die nicht geprüft werden konnten, weil sie nicht beim Ressourcenanbieter registriert sind | Dieser Abschnitt enthält die Anzahl und die Liste der Abonnements, die nicht für die SQL-IaaS-Agent-Erweiterung registriert wurden. |
| Gesamtzahl der gefundenen VMs | Die Anzahl der virtuellen Computer, die im Bereich der an das Cmdlet übergegebenen Parameter gefunden wurden. | 
| Bereits registrierte VMs | Die Anzahl der virtuellen Computer, die übersprungen wurden, weil sie bereits mit der Erweiterung registriert wurden. |
| Anzahl der erfolgreich registrierten VMs | Die Anzahl der virtuellen Computer, die nach der Ausführung des Cmdlets erfolgreich registriert wurden. Listet die registrierten virtuellen Computer im Format `SubscriptionID, Resource Group, Virtual Machine` auf. | 
| Anzahl der VMs, die aufgrund eines Fehlers nicht registriert werden konnten | Anzahl der virtuellen Computer, die aufgrund eines Fehlers nicht registriert werden konnten. Die Fehlerdetails finden Sie in der Protokolldatei. | 
| Anzahl der VMs, die übersprungen wurden, weil die VM oder der Gast-Agent in der VM nicht ausgeführt wird | Anzahl und Liste der virtuellen Computer, die nicht registriert werden konnten, weil der virtuelle Computer oder der Gast-Agent auf dem virtuellen Computer nicht ausgeführt wird. Diese können erneut geprüft werden, nachdem der virtuelle Computer oder der Gast-Agent gestartet wurde. Details finden Sie in der Protokolldatei. |
| Anzahl der virtuellen Computer, die übersprungen wurden, weil auf ihnen nicht SQL Server unter Windows ausgeführt wird | Anzahl der virtuellen Computer, die übersprungen wurden, weil auf ihnen nicht SQL Server ausgeführt wird oder weil sie keine Windows-VM sind. Die virtuellen Computer werden im Format `SubscriptionID, Resource Group, Virtual Machine` aufgelistet. | 
| &nbsp; | &nbsp; |

### <a name="log"></a>Log 

Fehler werden in der Protokolldatei `VMsNotRegisteredDueToError<Timestamp>.log` protokolliert, wobei der Zeitstempel der Zeitpunkt ist, zu dem das Skript gestartet wurde. Wenn der Fehler auf Abonnementebene liegt, enthält das Protokoll die kommagetrennte Abonnement-ID und die Fehlermeldung. Wenn der Fehler bei der Registrierung des virtuellen Computers aufgetreten ist, enthält das Protokoll die Abonnement-ID, den Ressourcengruppennamen, den VM-Namen, den Fehlercode und die Fehlermeldung mit Kommas als Trennzeichen. 

## <a name="remarks"></a>Bemerkungen

Wenn Sie SQL Server-VMs mit der Erweiterung mithilfe des bereitgestellten Skripts registrieren, müssen Sie Folgendes beachten:

- Für die Registrierung mit der Erweiterung muss ein Gast-Agent auf der SQL Server-VM ausgeführt werden. Windows Server 2008-Images verfügen über keinen Gast-Agent, sodass diese virtuellen Computer fehlschlagen und manuell im [NoAgent-Verwaltungsmodus](sql-server-iaas-agent-extension-automate-management.md#management-modes) registriert werden müssen.
- Es ist eine integrierte Wiederholungslogik vorhanden, um offensichtliche Fehler zu überwinden. Wenn die virtuelle Maschine erfolgreich registriert wurde, ist dies ein schneller Vorgang. Wenn die Registrierung jedoch fehlschlägt, wird jeder virtuelle Computer erneut geprüft.  Daher sollten Sie eine ausreichende Zeit für die Durchführung des Registrierungsprozesses zulassen. Die tatsächlich erforderliche Zeit hängt jedoch vom Typ und von der Anzahl der Fehler ab. 

## <a name="full-script"></a>Vollständiges Skript

Das vollständige Skript auf GitHub finden Sie unter [Massenregistrierung von SQL Server-VMs mit Azure PowerShell](https://github.com/Azure/azure-docs-powershell-samples/blob/master/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1). 

Kopieren Sie das vollständige Skript und speichern Sie es als `RegisterSqLVMs.psm1`.

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1 "Bulk register SQL Server virtual machines")]

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln: 

* [Übersicht über SQL Server auf einem virtuellen Windows-Computer](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Häufig gestellte Fragen zu SQL Server auf virtuellen Windows-Computern](frequently-asked-questions-faq.md)
* [Preisinformationen für SQL Server auf virtuellen Windows-Computern](pricing-guidance.md)
* [SQL Server auf Windows-VMs – Versionshinweise](../../database/doc-changes-updates-release-notes.md)
