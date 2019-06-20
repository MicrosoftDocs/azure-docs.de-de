---
title: Ausführen von PowerShell-Skripts in einer Windows-VM unter Azure
description: In diesem Thema wird beschrieben, wie PowerShell-Skripts auf einem virtuellen Azure Windows-Computer mithilfe von „Befehl ausführen“ ausgeführt werden
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/26/2019
ms.topic: article
manager: carmonm
ms.openlocfilehash: 23973445992ceaeb0cd3bc0589665f2fac5b64e5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64575343"
---
# <a name="run-powershell-scripts-in-your-windows-vm-with-run-command"></a>Ausführen von PowerShell-Skripts in Ihrer Windows-VM mit „Befehl ausführen“

Der Befehl „Run“ verwendet den VM-Agent, um PowerShell-Skripts innerhalb einer Azure Windows-VM auszuführen. Diese Skripts können für die allgemeine Verwaltung von Computern oder Anwendungen sowie für die schnelle Diagnose und Korrektur von Zugriffs- und Netzwerkproblemen in VMs verwendet werden und allgemein, um die VM wieder in einen guten Zustand zu bringen.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="benefits"></a>Vorteile

Es können mehrere Optionen für den Zugriff auf Ihre virtuellen Computer verwendet werden. „Befehl ausführen“ kann Skripts mithilfe des VM-Agents remote auf Ihren virtuellen Computern ausführen. „Befehl ausführen“ kann für Windows-VMs über das Azure-Portal, die [REST-API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand) oder [PowerShell](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) verwendet werden.

Diese Möglichkeit ist in allen Szenarien nützlich, in denen Sie ein Skript auf einem virtuellen Computer ausführen möchten, und stellt eine der wenigen Möglichkeiten zur Problembehandlung und Wartung von virtuellen Computern dar, deren RDP- oder SSH-Port aufgrund einer nicht ordnungsgemäßen Netzwerk- oder Administratorkonfiguration nicht geöffnet ist.

## <a name="restrictions"></a>Einschränkungen

Die Verwendung von „Befehl ausführen“ unterliegt den folgenden Einschränkungen:

* Die Ausgabe ist auf die letzten 4096 Bytes beschränkt
* Der Mindestzeitraum für die Ausführung eines Skripts liegt bei etwa 20 Sekunden
* Skripts werden unter Windows als „System“ ausgeführt
* Es kann zu jeder Zeit jeweils nur ein Skript ausgeführt werden
* Skripts, die Informationen anfordern (interaktiver Modus), werden nicht unterstützt.
* Die Ausführung von Skripts kann nicht abgebrochen werden
* Der Maximalzeitraum beim Ausführen von Skripts ist auf 90 Minuten beschränkt, danach tritt ein Timeout auf
* Um die Ergebnisse des Skripts zurückzugeben, ist eine ausgehende Konnektivität des virtuellen Computers erforderlich.

> [!NOTE]
> Der Ausführungsbefehl muss über Port 443 eine Verbindung mit öffentlichen Azure-IP-Adressen herstellen können, damit er richtig funktioniert. Wenn die Erweiterung keinen Zugriff auf diese Endpunkte hat, werden die Skripts möglicherweise erfolgreich ausgeführt, geben aber keine Ergebnisse zurück. Wenn Sie Datenverkehr auf dem virtuellen Computer blockieren, können Sie [Diensttags](../../virtual-network/security-overview.md#service-tags) verwenden, um Datenverkehr mit öffentlichen Azure-IP-Adressen über das `AzureCloud`-Tag zulassen.

## <a name="run-a-command"></a>Ausführen eines Befehls

Navigieren Sie in [Azure](https://portal.azure.com) zu einer VM, und wählen Sie unter **VORGÄNGE** **Befehl ausführen** aus. Es wird eine Liste mit den für die Ausführung auf der VM verfügbaren Befehlen angezeigt.

![Ausführen-Befehlsliste](./media/run-command/run-command-list.png)

Wählen Sie einen auszuführenden Befehl aus. Einige der Befehle weisen möglicherweise optionale oder erforderliche Eingabeparameter auf. Bei diesen Befehlen werden Ihnen die Parameter in Form von Textfeldern angezeigt, für die Sie die Eingabewerte bereitstellen müssen. Für jeden Befehl können Sie das ausgeführte Skript anzeigen, indem Sie **Skript anzeigen** aufklappen. **RunPowerShellScript** unterscheidet sich insofern von den anderen Befehlen, als es Ihnen die Angabe eines eigenen, benutzerdefinierten Skripts ermöglicht.

> [!NOTE]
> Die integrierten Befehle können nicht bearbeitet werden.

Nachdem Sie den Befehl ausgewählt haben, klicken Sie auf **Ausführen**, um das Skript auszuführen. Das Skript wird ausgeführt und gibt nach dem Abschluss die Ausgabe und eventuelle Fehler im Ausgabefenster zurück. Der folgende Screenshot zeigt eine Beispielausgabe für die Ausführung des **RDPSettings**-Befehls.

![Skriptausgabe von „Befehl ausführen“](./media/run-command/run-command-script-output.png)

## <a name="commands"></a>Befehle

Diese Tabelle enthält die Liste der für virtuelle Windows-Computer verfügbaren Befehle. Der Befehl **RunPowerShellScript** kann für die Ausführung jedes beliebigen benutzerdefinierten Skripts verwendet werden.

|**Name**|**Beschreibung**|
|---|---|
|**RunPowerShellScript**|Führt ein PowerShell-Skript aus|
|**EnableRemotePS**|Konfiguriert den Computer für die Aktivierung von remote-PowerShell.|
|**EnableAdminAccount**|Überprüft, ob das lokale Administratorkonto deaktiviert ist, und aktiviert es, wenn das der Fall ist.|
|**IPConfig**| Zeigt detaillierte Informationen für die IP-Adresse, die Subnetzmaske und das Standardgateway für jeden an TCP/IP gebundenen Adapter an.|
|**RDPSettings**|Überprüft Registrierungseinstellungen und Domänen-Richtlinieneinstellungen. Schlägt Richtlinienaktionen vor, wenn der Computer Teil einer Domäne ist, oder ändert die Einstellungen in Standardwerte.|
|**ResetRDPCert**|Entfernt das SSL-Zertifikat, das an den RDP-Listener gebunden ist, und stellt die Standardwerte für die Sicherheit des RDP-Listeners wieder her. Verwenden Sie dieses Skript, wenn irgendwelche Probleme in Verbindung mit dem Zertifikat auftreten.|
|**SetRDPPort**|Legt die standardmäßige oder vom Benutzer angegebene Portnummer für Remote Desktop-Verbindungen fest. Aktiviert die Firewallregel für eingehenden Zugriff auf den Port.|

## <a name="powershell"></a>PowerShell

Im folgenden Beispiel wird das Cmdlet [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) zum Ausführen eines PowerShell-Skripts in einem virtuellen Azure-Computer verwendet. Für das Cmdlet gilt, dass das im Parameter `-ScriptPath` referenzierte Skript am Ausführungsort des Cmdlets lokal sein muss.


```azurepowershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Einschränken des Zugriffs auf „Befehl ausführen“

Das Auflisten der ausführbaren Befehle oder das Anzeigen der Details zu einem Befehl erfordert die Berechtigung `Microsoft.Compute/locations/runCommands/read` auf Abonnementebene, über die die integrierte Rolle [Leser](../../role-based-access-control/built-in-roles.md#reader) und höhere Rollen verfügen.

Für das Ausführen eines Befehls ist die Berechtigung `Microsoft.Compute/virtualMachines/runCommand/action` auf Abonnementebene erforderlich, über die die Rolle [Mitwirkender für virtuelle Computer](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) und höhere Rollen verfügen.

Um „Befehl ausführen“ zu verwenden, können Sie eine der [integrierten](../../role-based-access-control/built-in-roles.md) Rollen verwenden oder eine [benutzerdefinierte](../../role-based-access-control/custom-roles.md) Rolle erstellen.

## <a name="next-steps"></a>Nächste Schritte

Informationen über andere Möglichkeiten zur Remoteausführung von Skripts und Befehlen finden Sie unter [Ausführen von Skripts in Ihrer Windows-VM](run-scripts-in-vm.md).
