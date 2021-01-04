---
title: Azure-VM-Erweiterungen und Features für Linux
description: Erfahren Sie, welche Erweiterungen für virtuelle Azure-Computer unter Linux verfügbar sind, gruppiert nach den bereitgestellten oder verbesserten Funktionen.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 52f5d0ec-8f75-49e7-9e15-88d46b420e63
ms.service: virtual-machines-linux
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: akjosh
ms.openlocfilehash: 129897d3288a900803efbfba8abf86c276077fa8
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966070"
---
# <a name="virtual-machine-extensions-and-features-for-linux"></a>Informationen zu Erweiterungen und Features für virtuelle Computer für Linux

Erweiterungen für virtuelle Azure-Computer sind kleine Anwendungen, die Konfigurations- und Automatisierungsaufgaben auf virtuellen Azure-Computern nach der Bereitstellung ermöglichen. Wenn z.B. Software auf einem virtuellen Computer (virtual machine, VM) installiert werden muss, Virenschutz oder die Ausführung eines Skripts erforderlich ist, kann eine VM-Erweiterung verwendet werden. Azure-VM-Erweiterungen können mithilfe der Azure-Befehlszeilenschnittstelle, PowerShell, Azure Resource Manager-Vorlagen und dem Azure-Portal ausgeführt werden. Erweiterungen können mit einer neuen Bereitstellung für virtuelle Computer gebündelt oder für ein bestehendes System ausgeführt werden.

Dieser Artikel enthält eine Übersicht der VM-Erweiterungen, erläutert Voraussetzungen für die Verwendung von Azure-VM-Erweiterungen und bietet Hilfestellung beim Erkennen, Verwalten und Entfernen von VM-Erweiterungen. Dieser Artikel enthält verallgemeinerte Informationen, da eine Vielzahl von VM-Erweiterungen verfügbar ist, die alle eine potenziell eigene Konfiguration aufweisen. Erweiterungsspezifische Details finden Sie in der für die jeweilige Erweiterung spezifischen Dokumentation.

## <a name="use-cases-and-samples"></a>Anwendungsfälle und Beispiele

Es sind verschiedene Azure VM-Erweiterungen für jeweils spezifische Anwendungsfälle verfügbar. Beispiele hierfür sind:

- Anwenden von gewünschten Statuskonfigurationen mit PowerShell auf eine VM mithilfe der DSC-Erweiterung für Linux. Weitere Informationen finden Sie unter [Azure Desired State configuration extension](https://github.com/Azure/azure-linux-extensions/tree/master/DSC) (Azure-Erweiterung für die gewünschte Statuskonfiguration).
- Konfigurieren der Überwachung einer VM mit der VM-Erweiterung „Microsoft Monitoring Agent“. Weitere Informationen finden Sie unter [Überwachen einer Linux-VM](../linux/tutorial-monitor.md).
- Konfigurieren der Überwachung Ihrer Azure-Infrastruktur mit der Chef- oder Datadog-Erweiterung. Weitere Informationen finden Sie in der [Chef-Dokumentation](https://docs.chef.io/azure_portal.html) oder im [Datadog-Blog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).

Über prozessspezifische Erweiterungen hinaus ist sowohl für virtuelle Windows- als auch für virtuelle Linux-Computer eine benutzerdefinierte Skripterweiterung verfügbar. Die benutzerdefinierte Skripterweiterung für Linux ermöglicht die Ausführung beliebiger Bash-Skripts auf virtuellen Computern. Benutzerdefinierte Skripts sind beim Entwerfen von Azure-Bereitstellungen nützlich, die Konfiguration über das Maß hinaus erfordern, das mithilfe von Azure-Tools erreicht werden kann. Weitere Informationen finden Sie unter [Benutzerdefinierte Skripterweiterung für Linux-VMs](custom-script-linux.md).

## <a name="prerequisites"></a>Voraussetzungen

Der Azure Linux-Agent muss installiert sein, damit die Erweiterung auf der VM ausgeführt werden kann. Einige individuelle Erweiterungen haben Voraussetzungen, z.B. den Zugriff auf Ressourcen oder Abhängigkeiten.

### <a name="azure-vm-agent"></a>Azure-VM-Agent

Der Azure VM-Agent verwaltet Interaktionen zwischen einem virtuellen Azure-Computer und dem Azure Fabric Controller. Der VM-Agent ist für viele funktionale Aspekte in Bezug auf die Bereitstellung und Verwaltung virtueller Azure-Computer verantwortlich. Dies umfasst auch das Ausführen von VM-Erweiterungen. Der Azure-VM-Agent ist in Azure Marketplace-Images vorinstalliert und kann manuell auf unterstützten Betriebssystemen installiert werden. Der Azure-VM-Agent für Linux wird als Linux-Agent bezeichnet.

Informationen zu unterstützten Betriebssystemen und Installationshinweise finden Sie unter [Informationen zum Agent und zu Erweiterungen für virtuelle Computer](agent-linux.md).

#### <a name="supported-agent-versions"></a>Unterstützte Agent-Versionen

Es gibt Mindestversionen des Agents, um die bestmöglichen Ergebnisse zu erzielen. [hier finden Sie weitere Informationen](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)

#### <a name="supported-oses"></a>Unterstützte Betriebssysteme

Der Linux-Agent wird auf mehreren Betriebssystemen ausgeführt. Das Erweiterungsframework begrenzt jedoch die Anzahl der Betriebssysteme, die von Erweiterungen unterstützt werden. [hier finden Sie weitere Informationen](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems
)

Manche Erweiterung werden nicht auf allen Betriebssystemen unterstützt. In diesem Fall wird der Fehler *Error Code 51, 'Unsupported OS'* (Fehlercode 51, „Nicht unterstütztes Betriebssystem“) zurückgegeben. Überprüfen Sie die Dokumentation zu Erweiterungen auf Informationen zu Unterstützungsmöglichkeiten.

#### <a name="network-access"></a>Netzwerkzugriff

Erweiterungspakete werden aus dem Azure Storage-Erweiterungsrepository heruntergeladen, und Uploads des Erweiterungsstatus werden in Azure Storage gepostet. Wenn Sie unterstütze Versionen der Agents verwenden (siehe [Minimum version support for virtual machine agents in Azure (Unterstütze Mindestversion für VM-Agents in Azure)](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)), müssen Sie den Zugriff auf Azure Storage in der VM-Region nicht zulassen. Sie können den Agent verwenden, um die Kommunikation mit Agents an den Azure Fabric Controller umzuleiten. Wenn Sie eine nicht unterstützte Version des Agents verwenden, müssen Sie in dieser Region den von der VM ausgehenden Zugriff auf Azure Storage zulassen.

> [!IMPORTANT]
> Wenn Sie den Zugriff auf *168.63.129.16* mit der Gastfirewall blockiert haben, schlagen die Erweiterungen unabhängig von den gerade beschriebenen Szenarios fehl.

Agents können nur zum Herunterladen von Erweiterungspaketen und für Statusberichte verwendet werden. Wenn z.B. bei der Installation einer Erweiterung ein Skript aus GitHub heruntergeladen werden muss (Custom Script) oder Zugriff auf Azure Storage (Azure Backup) notwendig ist, müssen zusätzliche Firewallports/Netzwerksicherheitsgruppen-Ports geöffnet werden. Verschiedene Erweiterungen haben verschiedene Voraussetzungen, da es sich bei ihnen um eigenständige Anwendungen handelt. Für Erweiterungen, die Zugriff auf Azure Storage benötigen, können Sie den Zugriff über Azure-NSG-Diensttags für [Storage](../../virtual-network/network-security-groups-overview.md#service-tags) gewähren.

Für Anfragen zum Umleiten von Datenverkehr verfügt der Linux-Agent über Proxyserverunterstützung. Diese Proxyserverunterstützung gilt jedoch nicht für Erweiterungen. Sie müssen jede einzelne Erweiterung für die Arbeit mit einem Proxy konfigurieren.

## <a name="discover-vm-extensions"></a>Ermitteln von VM-Erweiterungen

Für die Verwendung mit virtuellen Azure-Computern stehen viele verschiedene VM-Erweiterungen zur Verfügung. Um eine vollständige Liste anzuzeigen, verwenden Sie [az vm extension image list](/cli/azure/vm/extension/image#az-vm-extension-image-list). Im folgenden Beispiel werden alle verfügbaren Erweiterungen am Standort *westus* (USA, Westen) aufgelistet:

```azurecli
az vm extension image list --location westus --output table
```

## <a name="run-vm-extensions"></a>Ausführen von VM-Erweiterungen

Azure-VM-Erweiterungen können auf vorhandenen VMs ausgeführt werden, was nützlich ist, um Konfigurationsänderungen vorzunehmen oder die Konnektivität für eine bereits bereitgestellte VM wiederherzustellen. VM-Erweiterungen können darüber hinaus mit der Bereitstellung von Azure Resource Manager-Vorlagen gekoppelt werden. Durch die Verwendung von Erweiterungen mit Resource Manager-Vorlagen können virtuelle Azure-Computer bereitgestellt und konfiguriert werden, ohne dass Eingriffe nach der Bereitstellung erforderlich werden.

Die folgenden Methoden können verwendet werden, um eine Erweiterung für eine vorhandene VM auszuführen.

### <a name="azure-cli"></a>Azure CLI

Azure VM-Erweiterungen können mit dem Befehl [az vm extension set](/cli/azure/vm/extension#az-vm-extension-set) für einen vorhandenen virtuellen Computer ausgeführt werden. Im folgenden Beispiel wird die Erweiterung für benutzerdefinierte Skripts für einen virtuellen Computer mit dem Namen *myVM* in der Ressourcengruppe *myResourceGroup* ausgeführt. Ersetzen Sie den Ressourcengruppennamen, den VM-Namen und das auszuführende Skript (https:\//raw.githubusercontent.com/me/project/hello.sh) im Beispiel durch Ihre eigenen Informationen. 

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://raw.githubusercontent.com/me/project/hello.sh"],"commandToExecute": "./hello.sh"}'
```

Wenn die Erweiterung ordnungsgemäß ausgeführt wird, ähnelt die Ausgabe dem folgenden Beispiel:

```bash
info:    Executing command vm extension set
+ Looking up the VM "myVM"
+ Installing extension "CustomScript", VM: "mvVM"
info:    vm extension set command OK
```

### <a name="azure-portal"></a>Azure-Portal

VM-Erweiterungen können mithilfe des Azure-Portals auf eine vorhandene VM angewendet werden. Wählen Sie im Azure-Portal **Erweiterungen** aus, und klicken Sie dann auf **Hinzufügen**. Wählen Sie die Erweiterung aus, die Sie aus der Liste verfügbarer Erweiterungen erhalten, und befolgen Sie die Anweisungen im Assistenten.

Das folgende Bild zeigt die Installation der benutzerdefinierten Linux-Skripterweiterung aus dem Azure-Portal:

![Installieren benutzerdefinierter Skripterweiterung](./media/features-linux/installscriptextensionlinux.png)

### <a name="azure-resource-manager-templates"></a>Azure-Ressourcen-Manager-Vorlagen

VM-Erweiterungen können einer Azure Resource Manager-Vorlage hinzugefügt und mit der Bereitstellung der Vorlage ausgeführt werden. Wenn Sie eine Erweiterung mithilfe einer Vorlage bereitstellen, können Sie vollständig konfigurierte Azure-Bereitstellungen erstellen. Beispielsweise stammt der folgende JSON-Code aus einer Resource Manager-Vorlage, die einen Satz von VMs mit Lastenausgleich und Azure SQL-Datenbank bereitstellt und dann auf jeder VM eine .NET Core-Anwendung installiert. Die VM-Erweiterung erledigt die Softwareinstallation.

Weitere Informationen finden Sie in der vollständigen [Resource Manager-Vorlage](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
    }
}
```

Weitere Informationen zum Erstellen von Resource Manager-Vorlagen finden Sie unter [Erstellen von Azure Resource Manager-Vorlagen](../windows/template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>Schützen der Daten von VM-Erweiterungen

Beim Ausführen einer VM-Erweiterung ist es möglicherweise erforderlich, vertrauliche Informationen wie Anmeldeinformationen, Namen von Speicherkonten und Zugriffsschlüssel von Speicherkonten mit aufzunehmen. Viele VM-Erweiterungen beinhalten eine geschützte Konfiguration, die Daten verschlüsselt und sie ausschließlich innerhalb des virtuellen Zielcomputers entschlüsselt. Jede Erweiterung weist ein spezifisches Schema für die geschützte Konfiguration auf, und jede wird in der erweiterungsspezifischen Dokumentation ausführlich erläutert.

Das folgende Beispiel zeigt eine Instanz der benutzerdefinierten Skripterweiterung für Linux. Der auszuführende Befehl enthält Anmeldeinformationen. In diesem Beispiel wird der auszuführende Befehl nicht verschlüsselt:

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ],
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
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
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

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

#### <a name="agent-updates"></a>Updates für Agents

Der Linux-VM-Agent enthält *Bereitstellungs-Agent-Code* und *Code für die Behandlung von Erweiterungen* in einem Paket, die nicht voneinander getrennt werden können. Sie können den *Bereitstellungs-Agent* deaktivieren, wenn Sie die Bereitstellung in Azure mit cloud-init durchführen möchten. Informationen dazu finden Sie unter [Verwenden von cloud-init](../linux/using-cloud-init.md).

Unterstützte Versionen der Agents können automatische Updates verwenden. Der einzige Code, der aktualisiert werden kann, ist die *Erweiterung zur Behandlung von Code*, nicht der Bereitstellungscode. Der *Bereitstellungs-Agent-Code* wird einmalig ausgeführt.

Der *Code für die Behandlung von Erweiterungen* ist für die Kommunikation mit dem Azure-Fabric und das Behandeln von Vorgängen für VM-Erweiterungen verantwortlich, z.B. Installationen, Statusberichte, Aktualisierungen und Entfernen einzelner Erweiterungen. Updates enthalten Sicherheitsfixes, Fehlerbehebungen und Verbesserungen für den *Code für die Behandlung von Erweiterungen*.

Beim Installieren des Agents wird ein übergeordneter Daemon erstellt. Dieses übergeordnete Element erzeugt dann einen untergeordneten Prozess, mit dem Erweiterungen behandelt werden. Wenn ein Update für den Agent verfügbar ist, wird es heruntergeladen und das übergeordnete Element beendet den untergeordneten Prozess, aktualisiert diesen und startet ihn dann neu. Falls ein Problem mit dem Update vorliegt, führt der übergeordnete Prozess ein Rollback auf die Vorgängerversion des untergeordneten Prozesses durch.

Der übergeordnete Prozess kann nicht automatisch aktualisiert werden. Der übergeordnete Prozess kann nur durch ein Update des Distributionspakets aktualisiert werden.

Um herauszufinden, welche Version ausgeführt wird, überprüfen Sie `waagent` wie folgt:

```bash
waagent --version
```

Die Ausgabe sieht in etwa wie das folgende Beispiel aus:

```bash
WALinuxAgent-2.2.17 running on ubuntu 16.04
Python: 3.5.2
Goal state agent: 2.2.18
```

In der vorhergehenden Beispielausgabe ist die Version des übergeordneten Prozesses oder des bereitgestellten Pakets *WALinuxAgent-2.2.17*.

Die Zielversion des Agents ist die automatisch aktualisierte Version.

Es wird dringend empfohlen, die automatische Aktualisierung für den Agent immer zu aktivieren: [AutoUpdate.Enabled=y](./update-linux-agent.md). Ist diese Funktion nicht aktiviert, müssen Sie den Agent manuell aktualisieren und erhalten keine Fehlerbehebungen und Sicherheitsupdates.

#### <a name="extension-updates"></a>Updates für Erweiterungen

Wenn ein Update für eine Erweiterung verfügbar ist, lädt der Linux-Agent dieses herunter und aktualisiert die Erweiterung. Automatische Updates für Erweiterungen sind entweder *kleinere Updates* oder *Hotifxupdates*. Sie können beim Bereitstellen der Erweiterung entscheiden, ob Sie *kleinere* Updates für Erweiterungen abonnieren wollen oder nicht. Im folgenden Beispiel sehen Sie, wie man Nebenversionen in einer Resource Manager-Vorlage automatisch mit *autoUpgradeMinorVersion": true,'* aktualisiert:

```json
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
        ]
    },
```

Sie sollten in Ihren Bereitstellungen von Erweiterungen immer automatische Updates auswählen, um die neuesten Fehlerbehebungen für Nebenversionen zu erhalten. Das Abonnement für Hotfixupdates, die Sicherheitsfixes oder Hauptfehlerbehebungen enthalten, kann nicht gekündigt werden.

### <a name="how-to-identify-extension-updates"></a>So identifizieren Sie Updates für Erweiterungen

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Erkennen, ob die Erweiterung auf „autoUpgradeMinorVersion“ auf einer VM festgelegt ist

Sie können am VM-Modell erkennen, ob die Erweiterung mit „autoUpgradeMinorVersion“ bereitgestellt wurde. Um dies zu überprüfen, verwenden Sie [az vm show](/cli/azure/vm#az-vm-show), und geben Sie die Ressourcengruppe und den VM-Namen wie folgt an:

```azurecli
az vm show --resource-group myResourceGroup --name myVM
```

Die Ausgabe des folgenden Beispiels zeigt, dass *autoUpgradeMinorVersion* auf *TRUE* festgelegt ist:

```json
  "resources": [
    {
      "autoUpgradeMinorVersion": true,
      "forceUpdateTag": null,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM/extensions/CustomScriptExtension",
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Erkennen, wann „autoUpgradeMinorVersion“ aufgetreten ist

Um zu sehen, wann die Erweiterung aktualisiert wurde, überprüfen Sie die Protokolldatei des Agents auf der VM unter */var/log/waagent.log*.

Im folgenden Beispiel war auf dem virtuellen Computer *Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9025* installiert. Ein Hotfix auf *Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027* war verfügbar:

```bash
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Expected handler state: enabled
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Decide which version to use
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Use version: 2.3.9027
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Current handler state is: NotInstalled
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Download extension package
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Unpack extension package
INFO Event: name=Microsoft.OSTCExtensions.LinuxDiagnostic, op=Download, message=Download succeeded
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Initialize extension directory
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Update settings file: 0.settings
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9025] Disable extension.
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9025] Launch command:diagnostic.py -disable
...
INFO Event: name=Microsoft.OSTCExtensions.LinuxDiagnostic, op=Disable, message=Launch command succeeded: diagnostic.py -disable
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Update extension.
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Launch command:diagnostic.py -update
2017/08/14 20:21:57 LinuxAzureDiagnostic started to handle.
```

## <a name="agent-permissions"></a>Berechtigungen für Agents

Damit er seine Aufgaben ausführen kann, muss der Agent als *root* ausgeführt werden.

## <a name="troubleshoot-vm-extensions"></a>Problembehandlung bei VM-Erweiterungen

Jede VM-Erweiterung kann für die Erweiterung spezifische Schritte zur Problembehandlung aufweisen. Wenn Sie beispielsweise die benutzerdefinierte Skripterweiterung verwenden, finden Sie Details zur Skriptausführung lokal auf der VM, auf der die Erweiterung ausgeführt wurde. Alle erweiterungsspezifischen Schritte zur Problembehandlung sind ausführlich in der Dokumentation zur Erweiterung erläutert.

Die folgenden Schritte zur Problembehandlung gelten für alle VM-Erweiterungen.

1. Um das Protokoll des Linux-Agents zu überprüfen, sollten Sie die Aktivität bei der Bereitstellung der Erweiterung in */var/log/waagent.log* untersuchen.

2. Überprüfen Sie die Protokolle der eigentlichen Erweiterung in */var/log/azure/\<extensionName>* , um weitere Informationen zu erhalten.

3. Lesen Sie die Abschnitte zur Problembehandlung in der Dokumentation zu Erweiterungen für Fehlercodes, bekannten Problemen usw.

3. Sehen Sie sich die Systemprotokolle an. Überprüfen Sie, ob es andere Vorgänge gab, die möglicherweise die Erweiterung beeinträchtigt haben, z.B. eine lange Installation einer anderen Anwendung, für die exklusiver Zugriff auf den Paket-Manager notwendig war.

### <a name="common-reasons-for-extension-failures"></a>Häufige Ursachen für Fehler bei der Erweiterung

1. Erweiterungen sollen innerhalb von 20 Minuten ausgeführt werden (mit Ausnahme von benutzerdefinierten Erweiterungen, Chef und DSC, die 90 Minuten Zeit haben). Wenn Ihre Bereitstellung diese Zeit überschreitet, wird sie als Timeout markiert. Dies kann an VMs mit unzureichenden Ressourcen liegen, wenn andere VM-Konfigurationen/Starttasks hohe Mengen Ressourcen verbrauchen, während die Erweiterung bereitstellt.

2. Mindestvoraussetzungen nicht erfüllt. Einige Erweiterungen verfügen über VM-SKUs, z.B. HPC-Images. Erweiterungen können bestimmte Voraussetzungen für den Netzwerkzugriff erfordern, z.B. die Kommunikation mit Azure Storage oder öffentlichen Diensten. Andere Beispiele sind u.a. der Zugriff auf Paket-Repositorys, nahezu vollständig belegter Festplattenspeicher oder Sicherheitseinschränkungen.

3. Exklusiver Paket-Manager-Zugriff. In einigen Fällen können eine lange ausgeführte VM-Konfiguration und die Installation der Erweiterung in Konflikt stehen, wenn beide exklusiven Zugriff auf den Paket-Manager benötigen.

### <a name="view-extension-status"></a>Anzeigen des Erweiterungsstatus

Wenn eine VM-Erweiterung für eine VM ausgeführt wurde, können Sie mit [az vm get-instance-view](/cli/azure/vm#az-vm-get-instance-view) wie folgt zum Erweiterungsstatus zurückkehren:

```azurecli
az vm get-instance-view \
    --resource-group rgName \
    --name myVM \
    --query "instanceView.extensions"
```

Die Ausgabe sieht in etwa wie das folgende Beispiel aus:

```bash
  {
    "name": "customScript",
    "statuses": [
      {
        "code": "ProvisioningState/failed/0",
        "displayStatus": "Provisioning failed",
        "level": "Error",
        "message": "Enable failed: failed to execute command: command terminated with exit status=127\n[stdout]\n\n[stderr]\n/bin/sh: 1: ech: not found\n",
        "time": null
      }
    ],
    "substatuses": null,
    "type": "Microsoft.Azure.Extensions.customScript",
    "typeHandlerVersion": "2.0.6"
  }
```

Der Ausführungsstatus von Erweiterungen findet sich ebenfalls im Azure-Portal. Um den Status einer Erweiterung anzuzeigen, klicken Sie auf die VM und wählen Sie **Erweiterungen** sowie die gewünschte Erweiterung aus.

### <a name="rerun-a-vm-extension"></a>Erneutes Ausführen einer VM-Erweiterung

In manchen Fällen kann die erneute Ausführung einer VM-Erweiterung erforderlich sein. Sie können eine Erweiterung erneut ausführen, indem Sie sie entfernen und die Erweiterung dann mit einer Ausführungsmethode Ihrer Wahl erneut ausführen. Verwenden Sie zum Entfernen einer Erweiterung [az vm extension delete](/cli/azure/vm/extension#az-vm-extension-delete) wie folgt:

```azurecli
az vm extension delete \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name customScript
```

Sie können eine Erweiterung auch im Azure-Portal wie folgt entfernen:

1. Wählen Sie einen virtuellen Computer aus.
2. Wählen Sie **Erweiterungen** aus.
3. Wählen Sie die gewünschte Erweiterung aus.
4. Wählen Sie **Deinstallieren** aus.

## <a name="common-vm-extension-reference"></a>Allgemeine VM-Erweiterungsreferenz

| Name der Erweiterung | BESCHREIBUNG | Weitere Informationen |
| --- | --- | --- |
| Benutzerdefinierte Skripterweiterung für Linux |Ausführen von Skripts für virtuelle Azure-Computer |[Benutzerdefinierte Skripterweiterung für Linux](custom-script-linux.md) |
| Erweiterungen für den Zugriff auf virtuelle Computer |Wiedererlangen des Zugriffs auf einen virtuellen Azure-Computer |[Erweiterungen für den Zugriff auf virtuelle Computer](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
| Azure-Diagnoseerweiterung |Verwalten der Azure-Diagnose |[Azure-Diagnoseerweiterung](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Erweiterung für den Zugriff auf virtuelle Azure-Computer |Verwalten von Benutzern und Anmeldeinformationen |[Erweiterungen für den Zugriff auf virtuelle Computer für Linux](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu VM-Erweiterungen finden Sie unter [Azure virtual machine extensions and features overview (Übersicht über Azure-VM-Erweiterungen und Features)](overview.md).