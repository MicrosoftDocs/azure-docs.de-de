---
title: Die Erweiterung für den SQL Server-IaaS-Agent
description: In diesem Artikel erfahren Sie, wie die SQL Server-IaaS-Agent-Erweiterung bei der Automatisierung bestimmter SQL Server-Verwaltungsaufgaben auf Azure-VMs hilft. Hierzu gehören Features wie automatisierte Sicherungen und Patches, Azure Key Vault-Integration, Lizenzierungsverwaltung, Speicherkonfiguration und zentrale Verwaltung aller SQL Server-VM-Instanzen.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.subservice: management
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: e8268630b2c108dc95ded059ce41866a14fadd0e
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359250"
---
# <a name="automate-management-with-the-sql-server-iaas-agent-extension"></a>Automatisieren der Verwaltung mit der SQL Server-IaaS-Agent-Erweiterung
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


Die Erweiterung für den SQL Server-IaaS-Agent (SqlIaasExtension) kann auf virtuellen Azure-Computern ausgeführt werden, um Verwaltungsaufgaben zu automatisieren. 

Dieser Artikel bietet eine Übersicht über die Erweiterung. Informationen zum Installieren der SQL Server-IaaS-Erweiterung für SQL Server auf Azure-VMs finden Sie in den Artikeln zur [automatischen Installation](sql-agent-extension-automatic-registration-all-vms.md) und zur [Installation auf einzelnen VMs](sql-agent-extension-manually-register-single-vm.md) bzw. zur [Masseninstallation auf VMs](sql-agent-extension-manually-register-vms-bulk.md). 

## <a name="overview"></a>Übersicht

Die Erweiterung für den SQL Server-IaaS-Agent bietet für SQL Server auf Azure-VMs zahlreiche Vorteile: 

- **Featurevorteile:** Die Erweiterung macht verschiedene Automatisierungsfeatures wie Portalverwaltung, flexible Lizenzierung, automatisierte Sicherungen und Patches und andere verfügbar. Weitere Informationen finden Sie unter [Featurevorteile](#feature-benefits) weiter unten in diesem Artikel. 

- **Compliance**: Mit der Erweiterung können Sie auch einfacher Ihrer Verpflichtung gemäß den Nutzungsbedingungen zum Produkt nachkommen, Microsoft zu benachrichtigen, wenn der Azure-Hybridvorteil aktiviert wurde. Auf diese Weise können Sie darauf verzichten, für jede Ressource ein Lizenzregistrierungsformular zu verwalten.  

- **Free:** Die Erweiterung ist in allen drei Verwaltbarkeitsmodi vollständig kostenlos. Es fallen keine zusätzlichen Kosten für die Erweiterung oder für das Ändern von Verwaltungsmodi an. 

- **Vereinfachte Lizenzverwaltung**: Die Erweiterung vereinfacht die SQL Server-Lizenzverwaltung. Außerdem können Sie über das [Azure-Portal](manage-sql-vm-portal.md), die Azure-Befehlszeilenschnittstelle oder PowerShell schnell SQL Server-VMs mit aktiviertem Azure-Hybridvorteil identifizieren: 

   # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

   ```azurecli-interactive
   $vms = az sql vm list | ConvertFrom-Json
   $vms | Where-Object {$_.sqlServerLicenseType -eq "AHUB"}
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   Get-AzSqlVM | Where-Object {$_.LicenseType -eq 'AHUB'}
   ```

   ---


> [!IMPORTANT]
> Die SQL-IaaS-Agent-Erweiterung sammelt Daten ausschließlich, um Kunden zu ermöglichen, bei der Verwendung von SQL Server in Azure Virtual Machines optionale Vorteile zu nutzen. Microsoft verwendet diese Daten ohne vorherige Zustimmung des Kunden nicht für Lizenzierungsüberprüfungen. Weitere Informationen finden Sie unter [Ergänzende Datenschutzbestimmungen zu SQL Server](/sql/sql-server/sql-server-privacy#non-personal-data).


## <a name="feature-benefits"></a>Featurevorteile 

Die SQL Server-IaaS-Agent-Erweiterung macht eine Reihe von Featurevorteilen für die Verwaltung Ihrer SQL Server-VMs verfügbar. 

Diese Vorteile werden in der folgenden Tabelle genauer erläutert: 


| Funktion | Beschreibung |
| --- | --- |
| **Portalverwaltung** | Ermöglicht die [Verwaltung im Portal](manage-sql-vm-portal.md), sodass Sie alle Ihre SQL Server-VMs an einem Ort anzeigen und SQL-spezifische Features direkt über das Portal aktivieren und deaktivieren können. 
| **Automatisierte Sicherung** |Automatisiert die Planung von Sicherungen für alle Datenbanken entweder der Standardinstanz oder einer [ordnungsgemäß installierten](frequently-asked-questions-faq.md#administration) benannten Instanz von SQL Server in der VM. Weitere Informationen finden Sie unter [Automatisierte Sicherung für SQL Server auf virtuellen Azure-Computern (Resource Manager)](automated-backup-sql-2014.md). |
| **Automatisiertes Patchen** |Mit diesem Feature können Sie ein Wartungsfenster konfigurieren, in dem wichtige Windows- und SQL Server-Sicherheitsupdates für die VM ausgeführt werden. Auf diese Weise werden Updates für Ihre Workload während der Spitzenzeiten vermieden. Weitere Informationen finden Sie unter [Automatisiertes Patchen für SQL Server auf virtuellen Azure-Computern (Resource Manager)](automated-patching.md). |
| **Azure Key Vault-Integration** |Mit diesem Dienst können Sie Azure Key Vault auf Ihrem virtuellen SQL Server-Computer automatisch installieren und konfigurieren. Weitere Informationen finden Sie unter [Konfigurieren der Azure Key Vault-Integration für SQL Server auf virtuellen Azure-Computern (Resource Manager)](azure-key-vault-integration-configure.md). |
| **Flexible Lizenzierung** | Durch einen [nahtlosen Wechsel](licensing-model-azure-hybrid-benefit-ahb-change.md) zwischen den Lizenzmodellen „Bring-Your-Own-License“ (BYOL, auch als „Azure-Hybridvorteil“ bezeichnet) und „Nutzungsbasierte Bezahlung“ können Sie Kosten sparen. | 
| **Flexible Version/Edition** | Wenn Sie die [Version](change-sql-server-version.md) oder [Edition](change-sql-server-edition.md) von SQL Server ändern möchten, können Sie einfach die Metadaten im Azure-Portal aktualisieren, ohne die gesamte SQL Server-VM erneut bereitstellen zu müssen.  | 


## <a name="management-modes"></a>Verwaltungsmodi

Sie können Ihre SQL-IaaS-Erweiterung in drei Verwaltungsmodi registrieren: 

- Im Modus **Lightweight** werden Erweiterungsbinärdateien auf den virtuellen Computer kopiert. Der Agent wird jedoch nicht installiert, und der SQL Server-Dienst wird auch nicht neu gestartet. Der Modus „Lightweight“ unterstützt nur das Ändern des Lizenztyps und der Edition von SQL Server und bietet eine eingeschränkte Portalverwaltung. Verwenden Sie diese Option für SQL Server-VMs mit mehreren Instanzen oder für den Einsatz mit einer Failoverclusterinstanz (FCI). Der Lightweight-Modus ist der Standardverwaltungsmodus bei Verwendung des Features [Automatische Registrierung](sql-agent-extension-automatic-registration-all-vms.md) und wenn bei der manuellen Registrierung kein Verwaltungstyp angegeben wurde. Die Verwendung des Modus „Lightweight“ hat keine Auswirkungen auf den Arbeitsspeicher oder die CPU. Außerdem fallen keine Kosten an. Es wird empfohlen, die SQL Server-VM zunächst im Modus „Lightweight“ zu registrieren und dann während eines Fenster für die geplante Wartung ein Upgrade auf den Modus „Vollständig“ auszuführen. 

- Im Modus **Vollständig** wird der SQL-IaaS-Agent auf dem virtuellen Computer installiert, um alle Funktionen bereitzustellen. Dafür sind jedoch ein Neustart des SQL Server-Diensts sowie Systemadministratorberechtigungen erforderlich. Verwenden Sie diesen Modus zum Verwalten einer SQL Server-VM mit einer einzelnen Instanz. Für den Modus „Vollständig“ werden zwei Windows-Dienste installiert, die minimale Auswirkungen auf den Arbeitsspeicher und die CPU haben. Diese Dienste können über den Task-Manager überwacht werden. Mit der Verwendung des Verwaltbarkeitsmodus „Vollständig“ sind keine Kosten verbunden. 

- Der Modus **NoAgent** ist speziell für Installationen von SQL Server 2008 und SQL Server 2008 R2 unter Windows Server 2008 vorgesehen. Es gibt keine Auswirkung auf den Arbeitsspeicher oder die CPU, wenn der Modus „NoAgent“ verwendet wird. Es fallen keine Kosten für die Verwendung des Verwaltbarkeitsmodus „NoAgent“ an, der SQL Server-Dienst wird nicht neu gestartet, und es wird kein Agent auf dem virtuellen Computer installiert. 

Über Azure PowerShell können Sie den aktuellen Modus des SQL Server-IaaS-Agents anzeigen: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

## <a name="installation"></a>Installation

Registrieren Sie Ihre SQL Server-VM mit der SQL Server-IaaS-Agent-Erweiterung, um die _Ressource_ **Virtueller SQL-Computer** in Ihrem Abonnement zu erstellen. Dabei handelt es sich um eine _andere_ Ressource als die Ressource für Ihren virtuellen Computer. Wenn Sie die Registrierung der Erweiterung für Ihre SQL Server-VM aufheben, wird die _Ressource_ **Virtueller SQL-Computer** entfernt, während der tatsächliche virtuelle Computer jedoch erhalten bleibt.

Beim Bereitstellen eines Azure Marketplace-Images einer SQL Server-VM über das Azure-Portal wird die SQL Server-VM mit der Erweiterung automatisch registriert. Wenn Sie jedoch SQL Server auf einer Azure-VM selbst installieren oder eine Azure-VM von einer benutzerdefinierten VHD bereitstellen, müssen Sie Ihre SQL Server-VM mit der SQL-IaaS-Agent-Erweiterung registrieren, um die Featurevorteile nutzen zu können. 

Wenn Sie die Erweiterung im Modus „Lightweight“ registrieren, werden die Binärdateien kopiert, der Agent wird jedoch nicht auf dem virtuellen Computer installiert. Der Agent wird auf dem virtuellen Computer installiert, wenn die Erweiterung auf den Verwaltungsmodus „Vollständig“ aktualisiert wird. 

Es gibt drei Möglichkeiten, VMs mit der Erweiterung zu registrieren: 
- [Automatisch für alle aktuellen und zukünftigen VMs in einem Abonnement](sql-agent-extension-automatic-registration-all-vms.md)
- [Manuell für eine einzelne VM](sql-agent-extension-manually-register-single-vm.md)
- [Manuell für mehrere VMs in einem Massenvorgang](sql-agent-extension-manually-register-vms-bulk.md)

### <a name="named-instance-support"></a>Unterstützung für benannte Instanzen

Die SQL Server-IaaS-Agent-Erweiterung funktioniert mit einer benannten Instanz von SQL Server, wenn es sich dabei um die einzige SQL Server-Instanz handelt, die auf der VM verfügbar ist. Die Erweiterung kann nicht auf VMs installiert werden, auf denen sich mehrere SQL Server-Instanzen befinden. 

Wenn Sie eine benannte Instanz von SQL Server verwenden möchten, stellen Sie eine Azure-VM bereit, installieren Sie eine einzelne benannte SQL Server-Instanz darauf, und registrieren Sie die SQL Server-VM dann mit der [SQL-IaaS-Erweiterung](sql-agent-extension-manually-register-single-vm.md).

Falls Sie stattdessen eine benannte Instanz mit einem SQL Server-Image aus dem Azure Marketplace verwenden möchten, führen Sie die folgenden Schritte aus: 

   1. Stellen Sie eine SQL Server-VM vom Azure Marketplace bereit. 
   1. [Heben Sie die Registrierung der SQL-IaaS-Agent-Erweiterung für die SQL Server-VM auf](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension). 
   1. Deinstallieren Sie SQL Server in der SQL Server-VM vollständig.
   1. Installieren Sie SQL Server mit einer benannten Instanz in der SQL Server-VM. 
   1. [Registrieren Sie die VM mit der SQL-IaaS-Agent-Erweiterung](sql-agent-extension-manually-register-single-vm.md#register-with-extension). 

## <a name="verify-status-of-extension"></a>Überprüfen des Status der Erweiterung

Sie können den Status der Erweiterung über das Azure-Portal oder mit Azure PowerShell überprüfen. 

### <a name="azure-portal"></a>Azure-Portal

Vergewissern Sie sich im Azure-Portal, dass die Erweiterung installiert ist. 

Wählen Sie im Bereich der VM die Option **Alle Einstellungen** und dann **Erweiterungen** aus. Die Erweiterung **SqlIaasExtension** sollte aufgeführt sein.

![Status der SQL Server-IaaS-Agent-Erweiterung im Azure-Portal](./media/sql-server-iaas-agent-extension-automate-management/azure-rm-sql-server-iaas-agent-portal.png)


### <a name="azure-powershell"></a>Azure PowerShell

Sie können auch das Azure PowerShell-Cmdlet **Get-AzVMSqlServerExtension** verwenden:

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

Der Befehl oben überprüft, ob der Agent installiert ist, und stellt allgemeine Statusinformationen bereit. Mit den folgenden Befehlen können Sie spezifische Statusinformationen zu automatischen Sicherungen und Patches abrufen:

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```


## <a name="limitations"></a>Einschränkungen

Die SQL-IaaS-Agent-Erweiterung unterstützt nur Folgendes: 

- Über den Azure Resource Manager bereitgestellte SQL Server-VMs. SQL Server-VMs, die mit dem klassischen Modell bereitgestellt wurden, werden nicht unterstützt. 
- In der öffentlichen Cloud oder Azure Government-Cloud bereitgestellte SQL Server-VMs. Bereitstellungen in anderen privaten Clouds oder Government Clouds werden nicht unterstützt. 


## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen 

**Sollte ich meine SQL Server-VM registrieren, die über ein SQL Server-Image im Azure Marketplace bereitgestellt wird?**

Nein. Microsoft registriert VMs, die über die SQL Server-Images im Azure Marketplace bereitgestellt werden, automatisch. Die Registrierung der VM mit der Erweiterung ist nur erforderlich, wenn die VM *nicht* über die SQL Server-Images in Azure Marketplace bereitgestellt wurde und SQL Server selbst installiert wurde.

**Ist die SQL-IaaS-Agent-Erweiterung für alle Kunden verfügbar?** 

Ja. Kunden sollten Ihre SQL Server-VMs mit der Erweiterung registrieren, wenn sie kein SQL Server-Image aus Azure Marketplace verwendet, sondern stattdessen SQL Server selbst installiert oder ihre benutzerdefinierte VHD verwendet haben. VMs im Besitz aller Abonnementtypen (direkt, Enterprise Agreement und Cloud Solution Provider) können mit der SQL-IaaS-Agent-Erweiterung registriert werden.

**Wie lautet der Standardverwaltungsmodus bei der Registrierung mit der SQL-IaaS-Agent-Erweiterung?**

Bei der Registrierung der VM mit der SQL-IaaS-Agent-Erweiterung lautet der Standardverwaltungsmodus *Lightweight*. Wenn die Eigenschaft für die Verwaltung von SQL Server bei der Registrierung mit der Erweiterung nicht festgelegt wurde, wird der Modus „Lightweight“ festgelegt, und der SQL Server-Dienst wird nicht neu gestartet. Es wird empfohlen, die VM mit der SQL-IaaS-Agent-Erweiterung zunächst im Modus „Lightweight“ zu registrieren und dann während eines Wartungsfensters auf den Modus „Vollständig“ zu aktualisieren. Der Standardverwaltungsmodus ist auch dann „Lightweight“, wenn das Feature zur [automatischen Registrierung](sql-agent-extension-automatic-registration-all-vms.md) verwendet wird.

**Was sind die Voraussetzungen für die Registrierung der VM mit der SQL-IaaS-Agent-Erweiterung?**

Als Voraussetzung für die Registrierung mit der SQL-IaaS-Agent-Erweiterung muss lediglich SQL Server auf der VM installiert sein. Beachten Sie, dass bei einer Installation der SQL-IaaS-Agent-Erweiterung im Modus „Vollständig“ ein Neustart des SQL Server-Diensts erfolgt. Es wird daher empfohlen, dies während eines Wartungsfensters durchzuführen.

**Wird bei der Registrierung mit der SQL-IaaS-Agent-Erweiterung ein Agent auf meiner VM installiert?**

Ja, bei der Registrierung mit der SQL-IaaS-Agent-Erweiterung im Verwaltbarkeitsmodus „Vollständig“ wird auf der VM ein Agent installiert. Bei einer Registrierung in den Modi „Lightweight“ oder „NoAgent“ ist dies nicht der Fall. 

Bei der Registrierung mit der SQL-IaaS-Agent-Erweiterung im Modus „Lightweight“ werden nur die *Binärdateien* der SQL-IaaS-Agent-Erweiterung auf die VM kopiert, der Agent wird jedoch nicht installiert. Diese Binärdateien werden dann für die Installation des Agents verwendet, wenn der Verwaltungsmodus in „Vollständig“ geändert wird.


**Wird bei der Registrierung mit der SQL-IaaS-Agent-Erweiterung der SQL Server-Dienst auf meiner VM neu gestartet?**

Das hängt von dem während der Registrierung angegebenen Modus ab. Wenn als Modus „Lightweight“ oder „NoAgent“ angegeben wurde, wird der SQL Server-Dienst nicht neu gestartet. Wird als Verwaltungsmodus jedoch „Vollständig“ angegeben, erfolgt ein Neustart des SQL Server-Diensts. Beim Feature für die automatische Registrierung werden Ihre SQL Server-VMs im Modus „Lightweight“ registriert, es sei denn, die Windows Server-Version ist 2008. In diesem Fall wird die SQL Server-VM im Modus „NoAgent“ registriert. 

**Worin besteht der Unterschied zwischen den Verwaltungsmodi „Lightweight“ und „NoAgent“ bei der Registrierung mit der SQL-IaaS-Agent-Erweiterung?** 

Der Verwaltungsmodus „NoAgent“ ist der einzige verfügbare Modus für SQL Server 2008 und SQL Server 2008 R2 unter Windows Server 2008. Für alle anderen Versionen von Windows Server sind die beiden Verwaltbarkeitsmodi „Lightweight“ und „Vollständig“ verfügbar. 

Für den Modus „NoAgent“ müssen die Eigenschaften für die SQL Server-Version und -Edition vom Kunden festgelegt werden. Der Modus „Lightweight“ fragt die VM ab, um die Version und Edition der SQL Server-Instanz zu ermitteln.

**Kann ich die SQL-IaaS-Agent-Erweiterung ohne Angabe des SQL Server-Lizenztyps registrieren?**

Nein. Der SQL Server-Lizenztyp ist bei der Registrierung der VM mit der SQL-IaaS-Agent-Erweiterung keine optionale Eigenschaft. Sie müssen in allen Verwaltbarkeitsmodi („NoAgent“, „Lightweight“ und „Vollständig“) den SQL Server-Lizenztyp „Nutzungsbasierte Bezahlung“ oder „Azure-Hybridvorteil“ festlegen, wenn Sie die Registrierung der VM mit der SQL-IaaS-Agent-Erweiterung vornehmen. Wenn eine der kostenlosen Versionen von SQL Server (z. B. die Developer oder Evaluation Edition) installiert ist, müssen Sie bei der Registrierung die Lizenzierung mit nutzungsbasierter Bezahlung angeben. Der Azure-Hybridvorteil ist nur für kostenpflichtige Versionen von SQL Server verfügbar, z. B. für die Editionen Enterprise und Standard.

**Kann ich die SQL Server-IaaS-Erweiterung vom Modus „NoAgent“ auf „Vollständig“ upgraden?**

Nein. Das Upgrade des Verwaltbarkeitsmodus auf „Vollständig“ oder „Lightweight“ steht für den Modus „NoAgent“ nicht zur Verfügung. Dies ist eine technische Einschränkung von Windows Server 2008. Sie müssen für das Betriebssystem zunächst ein Upgrade auf Windows Server 2008 R2 oder höher ausführen. Danach können Sie ein Upgrade auf den Verwaltungsmodus „Vollständig“ ausführen. 

**Kann ich die SQL Server-IaaS-Erweiterung vom Modus „Lightweight“ auf „Vollständig“ upgraden?**

Ja. Das Upgrade des Verwaltbarkeitsmodus von „Lightweight“ auf „Vollständig“ wird über Azure PowerShell und das Azure-Portal unterstützt. Dadurch wird ein Neustart des SQL Server-Diensts auslöst.

**Kann ich eine Herabstufung der SQL Server-IaaS-Erweiterung vom Modus „Vollständig“ auf die Verwaltungsmodi „NoAgent“ oder „Lightweight“ durchführen?**

Nein. Eine Herabstufung des Verwaltungsmodus für die SQL Server-IaaS-Erweiterung wird nicht unterstützt. Der Verwaltbarkeitsmodus kann vom Modus „Vollständig“ nicht auf „Lightweight“ oder „NoAgent“ herabgestuft werden und auch nicht vom Modus „Lightweight“ auf den Modus „NoAgent“. 

Um den Modus „Vollständig“ in einen anderen Verwaltbarkeitsmodus zu ändern, müssen Sie die [Registrierung der SQL-IaaS-Agent-Erweiterung für die SQL Server-VM aufheben](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension), indem Sie die _Ressource_ „Virtueller SQL-Computer“ löschen und die SQL Server-VM mit der SQL-IaaS-Agent-Erweiterung in einem anderen Verwaltungsmodus erneut registrieren.

**Kann ich die Registrierung der VM mit der SQL-IaaS-Agent-Erweiterung im Azure-Portal vornehmen?**

Nein. Die Registrierung der VM mit der SQL-IaaS-Agent-Erweiterung steht im Azure-Portal nicht zur Verfügung. Die Registrierung mit der SQL-IaaS-Agent-Erweiterung wird nur über Azure CLI oder Azure PowerShell unterstützt. 

**Kann ich eine VM mit der SQL-IaaS-Agent-Erweiterung registrieren, bevor SQL Server installiert wird?**

Nein. Die VM muss mindestens eine SQL Server-Instanz (Datenbank-Engine) aufweisen, damit sie mit der SQL-IaaS-Agent-Erweiterung registriert werden kann. Wenn auf der VM keine SQL Server-Instanz vorhanden ist, weist die neue Ressource Microsoft.SqlVirtualMachine einen Fehlerzustand auf.

**Kann ich eine VM mit der SQL-IaaS-Agent-Erweiterung registrieren, wenn mehrere SQL Server-Instanzen vorhanden sind?**

Ja. Die SQL-IaaS-Agent-Erweiterung registriert nur eine SQL Server-Instanz (Datenbank-Engine). Wenn mehrere Instanzen vorhanden sind, registriert die SQL-IaaS-Agent-Erweiterung die SQL Server-Standardinstanz. Gibt es keine Standardinstanz, wird nur die Registrierung im Lightweight-Modus unterstützt. Für ein Upgrade vom Verwaltbarkeitsmodus „Lightweight“ auf „Vollständig“ sollte entweder die SQL Server-Standardinstanz vorhanden sein, oder die VM sollte nur eine benannte SQL Server-Instanz aufweisen.

**Kann ich eine SQL Server-Failoverclusterinstanz mit der SQL-IaaS-Agent-Erweiterung registrieren?**

Ja. SQL Server-Failoverclusterinstanzen auf einer Azure-VM können mit der SQL-IaaS-Agent-Erweiterung im Modus „Lightweight“ registriert werden. Ein Upgrade von SQL Server-Failoverclusterinstanzen auf den Verwaltungsmodus „Vollständig“ ist jedoch nicht möglich.

**Kann ich meine VM mit der SQL-IaaS-Agent-Erweiterung registrieren, wenn eine Always On-Verfügbarkeitsgruppe konfiguriert wurde?**

Ja. Es gibt keine Einschränkungen beim Registrieren einer SQL Server-Instanz auf einer Azure-VM mit der SQL-IaaS-Agent-Erweiterung, wenn eine Konfiguration einer Always On-Verfügbarkeitsgruppe besteht.

**Welche Kosten fallen für das Registrieren der VM mit der SQL-IaaS-Agent-Erweiterung oder beim Upgraden auf den Verwaltbarkeitsmodus „Vollständig“ an?**

Keine Für das Registrieren der VM mit der SQL-IaaS-Agent-Erweiterung oder für das Verwenden eines der drei Verwaltbarkeitsmodi fallen keine Gebühren an. Das Verwalten Ihrer SQL Server-VM mit der Erweiterung ist vollständig kostenlos. 

**Welche Auswirkungen hat ein Verwenden der verschiedenen Verwaltbarkeitsmodi auf die Leistung?**

Es gibt keine Auswirkungen, wenn der Verwaltbarkeitsmodus *NoAgent* oder *Lightweight* verwendet wird. Es gibt minimale Auswirkungen, wenn der Verwaltbarkeitsmodus *Vollständig* von zwei Diensten verwendet wird, die in das Betriebssystem installiert wurden. Diese können über den Task-Manager überwacht und in der integrierten Konsole „Dienste“ in Windows angezeigt werden. 

Die beiden Dienstnamen lauten:
- `SqlIaaSExtensionQuery` (Anzeigename – `Microsoft SQL Server IaaS Query Service`)
- `SQLIaaSExtension` (Anzeigename – `Microsoft SQL Server IaaS Agent`)

**Wie entferne ich die Erweiterung?**

Sie entfernen die Erweiterung, indem Sie die [Registrierung der SQL-IaaS-Agent-Erweiterung für die SQL Server-VM aufheben](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension). 

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Installieren der SQL Server-IaaS-Erweiterung für SQL Server auf Azure-VMs finden Sie in den Artikeln zur [automatischen Installation](sql-agent-extension-automatic-registration-all-vms.md) und zur [Installation auf einzelnen VMs](sql-agent-extension-manually-register-single-vm.md) bzw. zur [Masseninstallation auf VMs](sql-agent-extension-manually-register-vms-bulk.md).

Ausführlichere Informationen zur Verwendung von SQL Server auf virtuellen Azure-Computern finden Sie unter [Was ist SQL Server auf virtuellen Azure-Computern?](sql-server-on-azure-vm-iaas-what-is-overview.md).
