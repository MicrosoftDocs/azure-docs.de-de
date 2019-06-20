---
title: Ausführen von Shellskripts in einer Linux-VM unter Azure
description: In diesem Thema wird beschrieben, wie Skripts auf einem virtuellen Azure Linux-Computer mithilfe von „Befehl ausführen“ ausgeführt werden
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/26/2019
ms.topic: article
manager: carmonm
ms.openlocfilehash: fe0547c1ddb89b8d9ab3e876e83bbdea17a58743
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64570599"
---
# <a name="run-shell-scripts-in-your-linux-vm-with-run-command"></a>Ausführen von Shellskripts in einer Linux-VM mit „Befehl ausführen“

„Befehl ausführen“ verwendet den VM-Agent, um Shellskripts innerhalb einer Azure Linux-VM auszuführen. Diese Skripts können für die allgemeine Verwaltung von Computern oder Anwendungen sowie für die schnelle Diagnose und Korrektur von Zugriffs- und Netzwerkproblemen in VMs verwendet werden und allgemein, um die VM wieder in einen guten Zustand zu bringen.

## <a name="benefits"></a>Vorteile

Es können mehrere Optionen für den Zugriff auf Ihre virtuellen Computer verwendet werden. „Befehl ausführen“ kann Skripts mithilfe des VM-Agents remote auf Ihren virtuellen Computern ausführen. „Befehl ausführen“ kann für virtuelle Linux-Computer über das Azure-Portal, die [REST-API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand) oder die [Azure CLI](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) verwendet werden.

Diese Möglichkeit ist in allen Szenarien nützlich, in denen Sie ein Skript auf einem virtuellen Computer ausführen möchten, und stellt eine der wenigen Möglichkeiten zur Problembehandlung und Wartung von virtuellen Computern dar, deren RDP- oder SSH-Port aufgrund einer nicht ordnungsgemäßen Netzwerk- oder Administratorkonfiguration nicht geöffnet ist.

## <a name="restrictions"></a>Einschränkungen

Im Anschluss finden eine Liste der Einschränkungen, denen die Verwendung von „Befehl ausführen“ unterliegt.

* Die Ausgabe ist auf die letzten 4096 Bytes beschränkt
* Der Mindestzeitraum für die Ausführung eines Skripts liegt bei etwa 20 Sekunden
* Skripts werden unter Linux standardmäßig mit erhöhten Benutzerberechtigungen ausgeführt
* Es kann zu jeder Zeit jeweils nur ein Skript ausgeführt werden
* Skripts, die Informationen anfordern (interaktiver Modus), werden nicht unterstützt.
* Die Ausführung von Skripts kann nicht abgebrochen werden
* Der Maximalzeitraum beim Ausführen von Skripts ist auf 90 Minuten beschränkt, danach tritt ein Timeout auf
* Um die Ergebnisse des Skripts zurückzugeben, ist eine ausgehende Konnektivität des virtuellen Computers erforderlich.

> [!NOTE]
> Der Ausführungsbefehl muss über Port 443 eine Verbindung mit öffentlichen Azure-IP-Adressen herstellen können, damit er richtig funktioniert. Wenn die Erweiterung keinen Zugriff auf diese Endpunkte hat, werden die Skripts möglicherweise erfolgreich ausgeführt, geben aber keine Ergebnisse zurück. Wenn Sie Datenverkehr auf dem virtuellen Computer blockieren, können Sie [Diensttags](../../virtual-network/security-overview.md#service-tags) verwenden, um Datenverkehr mit öffentlichen Azure-IP-Adressen über das `AzureCloud`-Tag zulassen.

## <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

Im folgenden Beispiel wird der Befehl [az vm run-command](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) verwendet, um ein Shellskript auf einer Azure Linux-VM auszuführen.

```azurecli-interactive
az vm run-command invoke -g myResourceGroup -n myVm --command-id RunShellScript --scripts "sudo apt-get update && sudo apt-get install -y nginx"
```

> [!NOTE]
> Um Befehle als ein anderer Benutzer auszuführen, können Sie `sudo -u` verwenden, um ein anderes Benutzerkonto für eine Aktion anzugeben.

## <a name="azure-portal"></a>Azure-Portal

Navigieren Sie in [Azure](https://portal.azure.com) zu einer VM, und wählen Sie unter **VORGÄNGE** **Befehl ausführen** aus. Es wird eine Liste mit den für die Ausführung auf der VM verfügbaren Befehlen angezeigt.

![Ausführen-Befehlsliste](./media/run-command/run-command-list.png)

Wählen Sie einen auszuführenden Befehl aus. Einige der Befehle weisen möglicherweise optionale oder erforderliche Eingabeparameter auf. Bei diesen Befehlen werden Ihnen die Parameter in Form von Textfeldern angezeigt, für die Sie die Eingabewerte bereitstellen müssen. Für jeden Befehl können Sie das ausgeführte Skript anzeigen, indem Sie **Skript anzeigen** aufklappen. **RunShellScript** unterscheidet sich insofern von den anderen Befehlen, als es Ihnen die Angabe eines eigenen, benutzerdefinierten Skripts ermöglicht.

> [!NOTE]
> Die integrierten Befehle können nicht bearbeitet werden.

Nachdem Sie den Befehl ausgewählt haben, klicken Sie auf **Ausführen**, um das Skript auszuführen. Das Skript wird ausgeführt und gibt nach dem Abschluss die Ausgabe und eventuelle Fehler im Ausgabefenster zurück. Der folgende Screenshot zeigt eine Beispielausgabe für die Ausführung des **ifconfig**-Befehls.

![Skriptausgabe von „Befehl ausführen“](./media/run-command/run-command-script-output.png)

## <a name="available-commands"></a>Verfügbare Befehle

Diese Tabelle enthält die Liste der für virtuelle Linux-Computer verfügbaren Befehle. Der Befehl **RunShellScript** kann für die Ausführung jedes beliebigen benutzerdefinierten Skripts verwendet werden.

|**Name**|**Beschreibung**|
|---|---|
|**RunShellScript**|Führt ein Linux-Shellskript aus.|
|**ifconfig**| Ruft die Konfiguration aller Netzwerkschnittstellen ab.|

## <a name="limiting-access-to-run-command"></a>Einschränken des Zugriffs auf „Befehl ausführen“

Das Auflisten der ausführbaren Befehle oder das Anzeigen der Details zu einem Befehl erfordert die Berechtigung `Microsoft.Compute/locations/runCommands/read` auf Abonnementebene, über die die integrierte Rolle [Leser](../../role-based-access-control/built-in-roles.md#reader) und höhere Rollen verfügen.

Für das Ausführen eines Befehls ist die Berechtigung `Microsoft.Compute/virtualMachines/runCommand/action` auf Abonnementebene erforderlich, über die die Rolle [Mitwirkender für virtuelle Computer](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) und höhere Rollen verfügen.

Um „Befehl ausführen“ zu verwenden, können Sie eine der [integrierten](../../role-based-access-control/built-in-roles.md) Rollen verwenden oder eine [benutzerdefinierte](../../role-based-access-control/custom-roles.md) Rolle erstellen.

## <a name="next-steps"></a>Nächste Schritte

Informationen über andere Möglichkeiten zur Remoteausführung von Skripts und Befehlen finden Sie unter [Ausführen von Skripts in Ihrer Linux-VM](run-scripts-in-vm.md).
