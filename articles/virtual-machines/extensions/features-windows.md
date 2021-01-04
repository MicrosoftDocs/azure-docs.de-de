---
title: Azure-VM-Erweiterungen und Features für Windows
description: Sie erhalten einen Überblick über die Erweiterungen für virtuelle Azure-Computer, gruppiert nach den bereitgestellten oder verbesserten Funktionen.
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 999d63ee-890e-432e-9391-25b3fc6cde28
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: akjosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 565f98126cea8cc03874bb4f83ecdc2c65f8d5fb
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016453"
---
# <a name="virtual-machine-extensions-and-features-for-windows"></a>Erweiterungen und Features für virtuelle Computer für Windows

Erweiterungen für virtuelle Azure-Computer sind kleine Anwendungen, die Konfigurations- und Automatisierungsaufgaben auf virtuellen Azure-Computern nach der Bereitstellung ermöglichen. Wenn z.B. Software auf einem virtuellen Computer (virtual machine, VM) installiert werden muss, Virenschutz oder die Ausführung eines Skripts erforderlich ist, kann eine VM-Erweiterung verwendet werden. Azure-VM-Erweiterungen können mithilfe der Azure-Befehlszeilenschnittstelle, PowerShell, Azure Resource Manager-Vorlagen und dem Azure-Portal ausgeführt werden. Erweiterungen können mit einer neuen Bereitstellung für virtuelle Computer gebündelt oder für ein bestehendes System ausgeführt werden.

Dieser Artikel enthält eine Übersicht der VM-Erweiterungen, erläutert Voraussetzungen für die Verwendung von Azure-VM-Erweiterungen und bietet Hilfestellung beim Erkennen, Verwalten und Entfernen von VM-Erweiterungen. Dieser Artikel enthält verallgemeinerte Informationen, da eine Vielzahl von VM-Erweiterungen verfügbar ist, die alle eine potenziell eigene Konfiguration aufweisen. Erweiterungsspezifische Details finden Sie in der für die jeweilige Erweiterung spezifischen Dokumentation.

 

## <a name="use-cases-and-samples"></a>Anwendungsfälle und Beispiele

Es sind verschiedene Azure VM-Erweiterungen für jeweils spezifische Anwendungsfälle verfügbar. Beispiele hierfür sind:

- Anwenden von gewünschten Statuskonfigurationen mit PowerShell auf eine VM mithilfe der DSC-Erweiterung für Windows. Weitere Informationen finden Sie unter [Azure Desired State configuration extension](dsc-overview.md) (Azure-Erweiterung für die gewünschte Statuskonfiguration).
- Konfigurieren der Überwachung einer VM mit der Log Analytics-Agent-VM-Erweiterung. Weitere Informationen finden Sie unter [Verbinden von virtuellen Azure-Computern mit Azure Monitor-Protokollen](../../azure-monitor/learn/quick-collect-azurevm.md).
- Konfigurieren eines virtuellen Azure-Computers mit Chef. Weitere Informationen finden Sie unter [Automatisieren der Bereitstellung virtueller Azure-Computer mit Chef](/azure/developer/chef/windows-vm-configure).
- Konfigurieren der Überwachung Ihrer Azure-Infrastruktur mit der Datadog-Erweiterung. Weitere Informationen finden Sie im [Datadog-Blog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).


Über prozessspezifische Erweiterungen hinaus ist sowohl für virtuelle Windows- als auch für virtuelle Linux-Computer eine benutzerdefinierte Skripterweiterung verfügbar. Die benutzerdefinierte Skripterweiterung für Windows ermöglicht die Ausführung beliebiger PowerShell-Skripts auf VMs. Benutzerdefinierte Skripts sind beim Entwerfen von Azure-Bereitstellungen nützlich, die Konfiguration über das Maß hinaus erfordern, das mithilfe von Azure-Tools erreicht werden kann. Weitere Informationen finden Sie unter [Benutzerdefinierte Skripterweiterung für Windows-VMs](custom-script-windows.md).

## <a name="prerequisites"></a>Voraussetzungen

Der Azure-Windows-Agent muss installiert sein, damit die Erweiterung auf der VM ausgeführt werden kann. Einige individuelle Erweiterungen haben Voraussetzungen, z.B. den Zugriff auf Ressourcen oder Abhängigkeiten.

### <a name="azure-vm-agent"></a>Azure-VM-Agent

Der Azure VM-Agent verwaltet Interaktionen zwischen einem virtuellen Azure-Computer und dem Azure Fabric Controller. Der VM-Agent ist für viele funktionale Aspekte in Bezug auf die Bereitstellung und Verwaltung virtueller Azure-Computer verantwortlich. Dies umfasst auch das Ausführen von VM-Erweiterungen. Der Azure-VM-Agent ist in Azure Marketplace-Images vorinstalliert und kann manuell auf unterstützten Betriebssystemen installiert werden. Der Azure-VM-Agent für Windows wird auch als Windows-Gast-Agent bezeichnet.

Informationen zu unterstützten Betriebssystemen und Installationshinweise finden Sie unter [Informationen zum Agent und zu Erweiterungen für virtuelle Computer](agent-windows.md).

#### <a name="supported-agent-versions"></a>Unterstützte Agent-Versionen

Es gibt Mindestversionen des Agents, um die bestmöglichen Ergebnisse zu erzielen. [hier finden Sie weitere Informationen](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)

#### <a name="supported-oses"></a>Unterstützte Betriebssysteme

Der Windows-Gast-Agent wird auf mehreren Betriebssystemen ausgeführt. Das Erweiterungsframework begrenzt jedoch die Anzahl der Betriebssysteme, die von Erweiterungen unterstützt werden. [hier finden Sie weitere Informationen](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems
)

Manche Erweiterung werden nicht auf allen Betriebssystemen unterstützt. In diesem Fall wird der Fehler *Error Code 51, 'Unsupported OS'* (Fehlercode 51, „Nicht unterstütztes Betriebssystem“) zurückgegeben. Überprüfen Sie die Dokumentation zu Erweiterungen auf Informationen zu Unterstützungsmöglichkeiten.

#### <a name="network-access"></a>Netzwerkzugriff

Erweiterungspakete werden aus dem Azure Storage-Erweiterungsrepository heruntergeladen, und Uploads des Erweiterungsstatus werden in Azure Storage gepostet. Wenn Sie [unterstützte](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) Versionen der Agents verwenden, müssen Sie keinen Zugriff auf Azure Storage in der VM-Region zulassen, da Sie über den Agent die Kommunikation mit Agents an den Azure-Fabric Controller umleiten können (HostGAPlugin-Feature über den privilegierten Kanal der privaten IP-Adresse [168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md)). Wenn Sie eine nicht unterstützte Version des Agents verwenden, müssen Sie in dieser Region den von der VM ausgehenden Zugriff auf Azure Storage zulassen.

> [!IMPORTANT]
> Wenn Sie den Zugriff auf *168.63.129.16* mit der Gastfirewall oder einem Proxy blockiert haben, treten bei den Erweiterungen unabhängig von den gerade beschriebenen Szenarien Fehler auf. Die Ports 80, 443 und 32526 sind erforderlich.

Agents können nur zum Herunterladen von Erweiterungspaketen und für Statusberichte verwendet werden. Wenn z.B. bei der Installation einer Erweiterung ein Skript aus GitHub heruntergeladen werden muss (Custom Script) oder Zugriff auf Azure Storage (Azure Backup) notwendig ist, müssen zusätzliche Firewallports/Netzwerksicherheitsgruppen-Ports geöffnet werden. Verschiedene Erweiterungen haben verschiedene Voraussetzungen, da es sich bei ihnen um eigenständige Anwendungen handelt. Für Erweiterungen, die Zugriff auf Azure Storage oder Azure Active Directory benötigen, können Sie den Zugriff über [Azure-NSG-Diensttags](../../virtual-network/network-security-groups-overview.md#service-tags) für Storage bzw. AzureActiveDirectory gewähren.

Der Windows-Gast-Agent unterstützt nicht das Umleiten von Datenverkehrsanforderungen des Agents über einen Proxyserver. Das bedeutet, dass der Windows-Gast-Agent Ihren benutzerdefinierten Proxy (sofern vorhanden) für den Zugriff auf Ressourcen im Internet oder auf dem Host über die IP-Adresse 168.63.129.16 verwendet.

## <a name="discover-vm-extensions"></a>Ermitteln von VM-Erweiterungen

Für die Verwendung mit virtuellen Azure-Computern stehen viele verschiedene VM-Erweiterungen zur Verfügung. Eine vollständige Liste finden Sie unter [Get-AzVMExtensionImage](/powershell/module/az.compute/get-azvmextensionimage). Im folgenden Beispiel werden alle verfügbaren Erweiterungen am Standort *WestUS* aufgelistet:

```powershell
Get-AzVmImagePublisher -Location "WestUS" | `
Get-AzVMExtensionImageType | `
Get-AzVMExtensionImage | Select Type, Version
```

## <a name="run-vm-extensions"></a>Ausführen von VM-Erweiterungen

Azure-VM-Erweiterungen können auf vorhandenen VMs ausgeführt werden, was nützlich ist, um Konfigurationsänderungen vorzunehmen oder die Konnektivität für eine bereits bereitgestellte VM wiederherzustellen. VM-Erweiterungen können darüber hinaus mit der Bereitstellung von Azure Resource Manager-Vorlagen gekoppelt werden. Durch die Verwendung von Erweiterungen mit Resource Manager-Vorlagen können virtuelle Azure-Computer bereitgestellt und konfiguriert werden, ohne dass Eingriffe nach der Bereitstellung erforderlich werden.

Die folgenden Methoden können verwendet werden, um eine Erweiterung für eine vorhandene VM auszuführen.

### <a name="powershell"></a>PowerShell

Mehrere PowerShell-Befehle können zum Ausführen einzelner Erweiterungen verwendet werden. Verwenden Sie zum Anzeigen einer Liste [Get-Command](/powershell/module/microsoft.powershell.core/get-command), und filtern Sie nach *Extension*:

```powershell
Get-Command Set-Az*Extension* -Module Az.Compute
```

Dies erzeugt eine Ausgabe ähnlich der Folgenden:

```powershell
CommandType     Name                                          Version    Source
-----------     ----                                          -------    ------
Cmdlet          Set-AzVMAccessExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMADDomainExtension                     4.5.0      Az.Compute
Cmdlet          Set-AzVMAEMExtension                          4.5.0      Az.Compute
Cmdlet          Set-AzVMBackupExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMBginfoExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMChefExtension                         4.5.0      Az.Compute
Cmdlet          Set-AzVMCustomScriptExtension                 4.5.0      Az.Compute
Cmdlet          Set-AzVMDiagnosticsExtension                  4.5.0      Az.Compute
Cmdlet          Set-AzVMDiskEncryptionExtension               4.5.0      Az.Compute
Cmdlet          Set-AzVMDscExtension                          4.5.0      Az.Compute
Cmdlet          Set-AzVMExtension                             4.5.0      Az.Compute
Cmdlet          Set-AzVMSqlServerExtension                    4.5.0      Az.Compute
Cmdlet          Set-AzVmssDiskEncryptionExtension             4.5.0      Az.Compute
```

Im folgenden Beispiel wird mit der benutzerdefinierten Skripterweiterung ein Skript von einem GitHub-Repository auf den virtuellen Zielcomputer heruntergeladen und dann das Skript ausgeführt. Weitere Informationen zum Verwenden der benutzerdefinierten Skripterweiterung finden Sie unter [Windows-VM – benutzerdefinierte Skripterweiterungen mit Azure Resource Manager-Vorlagen](custom-script-windows.md).

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" -Name "myCustomScript" `
    -FileUri "https://raw.githubusercontent.com/neilpeterson/nepeters-azure-templates/master/windows-custom-script-simple/support-scripts/Create-File.ps1" `
    -Run "Create-File.ps1" -Location "West US"
```

Im folgenden Beispiel wird die VM-Zugriffserweiterung verwendet, um das Administratorkennwort eines virtuellen Windows-Computers auf ein vorübergehendes zurückzusetzen. Weitere Informationen über die VM-Zugriffserweiterung finden Sie unter [Zurücksetzen des Remotedesktopdiensts oder seines Anmeldekennworts in einer Windows-VM](../troubleshooting/reset-rdp.md). Sobald Sie dies durchgeführt haben, sollten Sie bei der ersten Anmeldung das Kennwort zurücksetzen:

```powershell
$cred=Get-Credential

Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myVMAccess" `
    -Location WestUS -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

Mit dem `Set-AzVMExtension`-Befehl kann jede beliebige VM-Erweiterung gestartet werden. Weitere Informationen finden Sie in der [Referenz zu Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension).


### <a name="azure-portal"></a>Azure-Portal

VM-Erweiterungen können mithilfe des Azure-Portals auf eine vorhandene VM angewendet werden. Wählen Sie im Azure-Portal **Erweiterungen** aus, und klicken Sie dann auf **Hinzufügen**. Wählen Sie die Erweiterung aus, die Sie aus der Liste verfügbarer Erweiterungen erhalten, und befolgen Sie die Anweisungen im Assistenten.

Im folgenden Beispiel wird die Installation der Microsoft Antimalware-Erweiterung aus dem Azure-Portal gezeigt:

![Installieren der Antimalware-Erweiterung](./media/features-windows/installantimalwareextension.png)

### <a name="azure-resource-manager-templates"></a>Azure-Ressourcen-Manager-Vorlagen

VM-Erweiterungen können einer Azure Resource Manager-Vorlage hinzugefügt und mit der Bereitstellung der Vorlage ausgeführt werden. Wenn Sie eine Erweiterung mithilfe einer Vorlage bereitstellen, können Sie vollständig konfigurierte Azure-Bereitstellungen erstellen. Beispielsweise stammt der folgende JSON-Code aus einer Resource Manager-Vorlage, die VMs mit Lastenausgleich und einer Azure SQL-Datenbank bereitstellt und dann auf jeder VM eine .NET Core-Anwendung installiert. Die VM-Erweiterung erledigt die Softwareinstallation.

Weitere Informationen finden Sie in der vollständigen [Resource Manager-Vorlage](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

Weitere Informationen zum Erstellen von Resource Manager-Vorlagen finden Sie unter [Erstellen von Azure Resource Manager-Vorlagen mit Erweiterungen für virtuelle Windows-Computer](../windows/template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>Schützen der Daten von VM-Erweiterungen

Beim Ausführen einer VM-Erweiterung ist es möglicherweise erforderlich, vertrauliche Informationen wie Anmeldeinformationen, Namen von Speicherkonten und Zugriffsschlüssel von Speicherkonten mit aufzunehmen. Viele VM-Erweiterungen beinhalten eine geschützte Konfiguration, die Daten verschlüsselt und sie ausschließlich innerhalb des virtuellen Zielcomputers entschlüsselt. Jede Erweiterung weist ein spezifisches Schema für die geschützte Konfiguration auf, und jede wird in der erweiterungsspezifischen Dokumentation ausführlich erläutert.

Das folgende Beispiel zeigt eine Instanz der benutzerdefinierten Skripterweiterung für Windows. Der auszuführende Befehl enthält Anmeldeinformationen. In diesem Beispiel wird der auszuführende Befehl nicht verschlüsselt:

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ],
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

Durch Verschieben der Eigenschaft **CommandToExecute** in die **protected**-Konfiguration wird die Ausführungszeichenfolge, wie im folgenden Beispiel gezeigt, geschützt:

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

Auf einer Azure-IaaS-VM, die Erweiterungen verwendet, werden möglicherweise Zertifikate des Antragstellers **_Windows Azure CRP Certificate Generator_** angezeigt. Auf einer klassischen RDFE-VM tragen diese Zertifikate den Antragstellernamen **_Windows Azure Service Management for Extensions_**.

Diese Zertifikate sichern die Kommunikation zwischen dem virtuellen Computer und seinem Host während der Übertragung geschützter Einstellungen (Kennwort, andere Anmeldeinformationen), die von Erweiterungen verwendet werden. Die Zertifikate werden von Azure Fabric Controller generiert und an den VM-Agent übermittelt. Wenn Sie den virtuellen Computer jeden Tag beenden und starten, wird möglicherweise ein neues Zertifikat von Fabric Controller erstellt. Das Zertifikat wird im persönlichen Zertifikatspeicher des Computers gespeichert. Diese Zertifikate können gelöscht werden. Der VM-Agent erstellt Zertifikate bei Bedarf neu.

### <a name="how-do-agents-and-extensions-get-updated"></a>Wie werden Agents und Erweiterungen aktualisiert?

Agents und Erweiterungen haben den gleichen Updatemechanismus. Einige Updates erfordern keine zusätzlichen Firewallregeln.

Wenn ein Update verfügbar ist, wird es nur auf der VM installiert, auf der es eine Änderung der Erweiterungen oder andere Änderungen des VM-Modells gab, z.B.:

- Datenträger
- Erweiterungen
- Container der Startdiagnose
- Geheimnisse des Gastbetriebssystems
- Größe des virtuellen Computers
- Netzwerkprofil

Herausgeber stellen Updates in verschiedenen Regionen zu verschiedenen Zeiten zur Verfügung, d.h., möglicherweise haben Ihre VMs in verschiedenen Regionen unterschiedliche Versionen.

#### <a name="listing-extensions-deployed-to-a-vm"></a>Auflisten von Erweiterungen, die einer VM bereitgestellt wurden

```powershell
$vm = Get-AzVM -ResourceGroupName "myResourceGroup" -VMName "myVM"
$vm.Extensions | select Publisher, VirtualMachineExtensionType, TypeHandlerVersion
```

```powershell
Publisher             VirtualMachineExtensionType          TypeHandlerVersion
---------             ---------------------------          ------------------
Microsoft.Compute     CustomScriptExtension                1.9
```

#### <a name="agent-updates"></a>Updates für Agents

Der Windows-Gast-Agent enthält nur *Code für die Behandlung von Erweiterungen*. Der *Code für die Windows-Bereitstellung* ist hiervon getrennt. Sie können den Windows-Gast-Agent deinstallieren. Sie können die automatischen Updates für den Windows-Gast-Agent nicht deaktivieren.

Der *Code für die Behandlung von Erweiterungen* ist für die Kommunikation mit dem Azure-Fabric und das Behandeln von Vorgängen für VM-Erweiterungen verantwortlich, z.B. Installationen, Statusberichte, Aktualisierungen und Entfernen einzelner Erweiterungen. Updates enthalten Sicherheitsfixes, Fehlerbehebungen und Verbesserungen für den *Code für die Behandlung von Erweiterungen*.

Informationen dazu, welche Version Sie ausführen, finden Sie unter [Detecting installed Windows Guest Agent (Identifizieren des installierten Windows-Gast-Agents)](agent-windows.md#detect-the-vm-agent).

#### <a name="extension-updates"></a>Updates für Erweiterungen

Wenn ein Update für eine Erweiterung verfügbar ist, lädt der Windows-Gast-Agent dieses herunter und aktualisiert die Erweiterung. Automatische Updates für Erweiterungen sind entweder *kleinere Updates* oder *Hotifxupdates*. Sie können beim Bereitstellen der Erweiterung entscheiden, ob Sie *kleinere* Updates für Erweiterungen abonnieren wollen oder nicht. Im folgenden Beispiel sehen Sie, wie man Nebenversionen in einer Resource Manager-Vorlage automatisch mit *autoUpgradeMinorVersion": true,'* aktualisiert:

```json
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
```

Sie sollten in Ihren Bereitstellungen von Erweiterungen immer automatische Updates auswählen, um die neuesten Fehlerbehebungen für Nebenversionen zu erhalten. Das Abonnement für Hotfixupdates, die Sicherheitsfixes oder Hauptfehlerbehebungen enthalten, kann nicht gekündigt werden.

### <a name="how-to-identify-extension-updates"></a>So identifizieren Sie Updates für Erweiterungen

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Erkennen, ob die Erweiterung auf „autoUpgradeMinorVersion“ auf einer VM festgelegt ist

Sie können am VM-Modell erkennen, ob die Erweiterung mit „autoUpgradeMinorVersion“ bereitgestellt wurde. Um dies zu überprüfen, verwenden Sie [Get-AzVm](/powershell/module/az.compute/get-azvm), und geben Sie die Ressourcengruppe und den VM-Namen wie folgt an:

```powerShell
 $vm = Get-AzVm -ResourceGroupName "myResourceGroup" -VMName "myVM"
 $vm.Extensions
```

Die Ausgabe des folgenden Beispiels zeigt, dass *autoUpgradeMinorVersion* auf *TRUE* festgelegt ist:

```powershell
ForceUpdateTag              :
Publisher                   : Microsoft.Compute
VirtualMachineExtensionType : CustomScriptExtension
TypeHandlerVersion          : 1.9
AutoUpgradeMinorVersion     : True
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Erkennen, wann „autoUpgradeMinorVersion“ aufgetreten ist

Um zu sehen, wann die Erweiterung aktualisiert wurde, überprüfen Sie die Protokolldatei des Agents auf der VM unter *C:\WindowsAzure\Logs\WaAppAgent.log*.

Im folgenden Beispiel war *Microsoft.Compute.CustomScriptExtension 1.8* auf der VM installiert. Ein Hotfix für Version *1.9* war verfügbar:

```powershell
[INFO]  Getting plugin locations for plugin 'Microsoft.Compute.CustomScriptExtension'. Current Version: '1.8', Requested Version: '1.9'
[INFO]  Auto-Upgrade mode. Highest public version for plugin 'Microsoft.Compute.CustomScriptExtension' with requested version: '1.9', is: '1.9'
```

## <a name="agent-permissions"></a>Berechtigungen für Agents

Um seine Aufgaben auszuführen, muss der Agent als *Lokales System* ausgeführt werden.

## <a name="troubleshoot-vm-extensions"></a>Problembehandlung bei VM-Erweiterungen

Jede VM-Erweiterung kann für die Erweiterung spezifische Schritte zur Problembehandlung aufweisen. Wenn Sie beispielsweise die benutzerdefinierte Skripterweiterung verwenden, finden Sie Details zur Skriptausführung lokal auf der VM, auf der die Erweiterung ausgeführt wurde. Alle erweiterungsspezifischen Schritte zur Problembehandlung sind ausführlich in der Dokumentation zur Erweiterung erläutert.

Die folgenden Schritte zur Problembehandlung gelten für alle VM-Erweiterungen.

1. Um die Protokolldatei des Windows-Gast-Agents zu überprüfen, sollten Sie die Aktivität bei der Bereitstellung der Erweiterung in *C:\WindowsAzure\Logs\WaAppAgent.log* näher betrachten.

2. Überprüfen Sie die tatsächlichen Erweiterungsprotokolle unter *C:\WindowsAzure\Logs\Plugins\\<extensionName>* , um weitere Einzelheiten zu erhalten.

3. Lesen Sie die Abschnitte zur Problembehandlung in der Dokumentation zu Erweiterungen für Fehlercodes, bekannte Probleme, etc.

4. Sehen Sie sich die Systemprotokolle an. Überprüfen Sie, ob es andere Vorgänge gab, die möglicherweise die Erweiterung beeinträchtigt haben, z.B. eine lange Installation einer anderen Anwendung, für die exklusiver Zugriff auf den Paket-Manager notwendig war.

### <a name="common-reasons-for-extension-failures"></a>Häufige Ursachen für Fehler bei der Erweiterung

1. Erweiterungen sollen innerhalb von 20 Minuten ausgeführt werden (mit Ausnahme von benutzerdefinierten Erweiterungen, Chef und DSC, die 90 Minuten Zeit haben). Wenn Ihre Bereitstellung diese Zeit überschreitet, wird sie als Timeout markiert. Dies kann an VMs mit unzureichenden Ressourcen liegen, wenn andere VM-Konfigurationen/Starttasks hohe Mengen Ressourcen verbrauchen, während die Erweiterung bereitstellt.

2. Mindestvoraussetzungen nicht erfüllt. Einige Erweiterungen verfügen über VM-SKUs, z.B. HPC-Images. Erweiterungen können bestimmte Voraussetzungen für den Netzwerkzugriff erfordern, z.B. die Kommunikation mit Azure Storage oder öffentlichen Diensten. Andere Beispiele sind u.a. der Zugriff auf Paket-Repositorys, nahezu vollständig belegter Festplattenspeicher oder Sicherheitseinschränkungen.

3. Exklusiver Paket-Manager-Zugriff. In einigen Fällen können eine lange ausgeführte VM-Konfiguration und die Installation der Erweiterung in Konflikt stehen, wenn beide exklusiven Zugriff auf den Paket-Manager benötigen.

### <a name="view-extension-status"></a>Anzeigen des Erweiterungsstatus

Wenn eine VM-Erweiterung für einen virtuellen Computer ausgeführt wurde, können Sie mit [Get-AzVM](/powershell/module/az.compute/get-azvm) zum Erweiterungsstatus zurückkehren. *Substatuses[0]* zeigt, dass die Bereitstellung der Erweiterung erfolgreich war. Das bedeutet, dass Sie der VM erfolgreich bereitgestellt wurde. Wenn die Ausführung der Erweiterung innerhalb der VM fehlgeschlagen ist, ist der Status *Substatuses[1]* .

```powershell
Get-AzVM -ResourceGroupName "myResourceGroup" -VMName "myVM" -Status
```

Die Ausgabe sieht in etwa wie das folgende Beispiel aus:

```powershell
Extensions[0]           :
  Name                  : CustomScriptExtension
  Type                  : Microsoft.Compute.CustomScriptExtension
  TypeHandlerVersion    : 1.9
  Substatuses[0]        :
    Code                : ComponentStatus/StdOut/succeeded
    Level               : Info
    DisplayStatus       : Provisioning succeeded
    Message             : Windows PowerShell \nCopyright (C) Microsoft Corporation. All rights reserved.\n
  Substatuses[1]        :
    Code                : ComponentStatus/StdErr/succeeded
    Level               : Info
    DisplayStatus       : Provisioning succeeded
    Message             : The argument 'cseTest%20Scriptparam1.ps1' to the -File parameter does not exist. Provide the path to an existing '.ps1' file as an argument to the

-File parameter.
  Statuses[0]           :
    Code                : ProvisioningState/failed/-196608
    Level               : Error
    DisplayStatus       : Provisioning failed
    Message             : Finished executing command
```

Der Ausführungsstatus von Erweiterungen findet sich ebenfalls im Azure-Portal. Um den Status einer Erweiterung anzuzeigen, klicken Sie auf die VM und wählen Sie **Erweiterungen** sowie die gewünschte Erweiterung aus.

### <a name="rerun-vm-extensions"></a>Erneutes Ausführen von VM-Erweiterungen

In manchen Fällen kann die erneute Ausführung einer VM-Erweiterung erforderlich sein. Sie können eine Erweiterung erneut ausführen, indem Sie sie entfernen und die Erweiterung dann mit einer Ausführungsmethode Ihrer Wahl erneut ausführen. Verwenden Sie zum Entfernen einer Erweiterung [Remove-AzVMExtension](/powershell/module/az.compute/remove-azvmextension) wie folgt:

```powershell
Remove-AzVMExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myExtensionName"
```

Sie können eine Erweiterung auch im Azure-Portal wie folgt entfernen:

1. Wählen Sie einen virtuellen Computer aus.
2. Wählen Sie **Erweiterungen** aus.
3. Wählen Sie die gewünschte Erweiterung aus.
4. Wählen Sie **Deinstallieren** aus.

## <a name="common-vm-extensions-reference"></a>Allgemeine VM-Erweiterungsreferenz
| Name der Erweiterung | BESCHREIBUNG | Weitere Informationen |
| --- | --- | --- |
| CustomScript-Erweiterung für Windows |Ausführen von Skripts für virtuelle Azure-Computer |[Benutzerdefinierte Skripterweiterung für Windows](custom-script-windows.md) |
| DSC-Erweiterung für Windows |PowerShell-DSC-Erweiterung (Desired State Configuration, Konfigurieren des gewünschten Zustands) |[DSC-Erweiterung für Windows](dsc-overview.md) |
| Azure-Diagnoseerweiterung |Verwalten der Azure-Diagnose |[Azure-Diagnoseerweiterung](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Erweiterung für den Zugriff auf virtuelle Azure-Computer |Verwalten von Benutzern und Anmeldeinformationen |[Erweiterung für den Zugriff auf virtuelle Computer für Linux](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu VM-Erweiterungen finden Sie unter [Azure virtual machine extensions and features overview (Übersicht über Azure-VM-Erweiterungen und Features)](overview.md).
