---
title: Behandeln von Problemen mit der Azure-VM-Erweiterung während der Notfallwiederherstellung mit Azure Site Recovery
description: Behandeln von Problemen mit der Azure-VM-Erweiterung während der Notfallwiederherstellung mit Azure Site Recovery
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.openlocfilehash: c1915d108bf9465d3e5b8d6a55053b583ee4f580
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009703"
---
# <a name="troubleshoot-azure-vm-extension-issues"></a>Behandeln von Problemen mit der Azure-VM-Erweiterung

Dieser Artikel enthält Schritte für die Problembehandlung, mit denen Sie Azure Site Recovery-Fehler mit dem VM-Agent und der Erweiterung beheben können.

## <a name="low-system-resources"></a>Geringe Systemressourcen

Dieses Problem tritt auf, wenn das System über wenig verfügbaren Arbeitsspeicher verfügt und nicht in der Lage ist, Arbeitsspeicher für die Installation des Mobilitätsdiensts zuzuweisen. Stellen Sie sicher, dass genügend Arbeitsspeicher verfügbar ist, damit die Installation fortgesetzt und erfolgreich abgeschlossen werden kann.

## <a name="azure-site-recovery-extension-time-out"></a>Timeout der Azure Site Recovery-Erweiterung  

Fehlermeldung: „Timeout bei der Taskausführung beim Warten auf den Start des Erweiterungsvorgangs.“<br>
Fehlercode: 151076

 Azure Site Recovery hat eine Erweiterung auf dem virtuellen Computer als Teil eines Aktivierungsschutzauftrags installiert. Jede der folgenden Bedingungen kann verhindern, dass der Schutzvorgang ausgelöst wird, und bewirken, dass der Auftrag nicht ausgeführt wird. Führen Sie die folgenden Problembehandlungsschritte aus, und wiederholen Sie dann den Vorgang:

- [Der Agent ist auf der VM installiert, reagiert aber nicht (für Windows-VMs)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [Der auf der VM installierte Agent ist veraltet (für Linux-VMs)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
- [Die Site Recovery-Erweiterung kann nicht aktualisiert oder geladen werden](#the-site-recovery-extension-fails-to-update-or-load)

Fehlermeldung: „Der vorherige Site Recovery-Erweiterungsvorgang nimmt mehr Zeit in Anspruch als erwartet.“<br>
Fehlercode: 150066

- [Der Agent ist auf der VM installiert, reagiert aber nicht (für Windows-VMs)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [Der auf der VM installierte Agent ist veraltet (für Linux-VMs)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
- [Der Status der Site Recovery-Erweiterung ist nicht korrekt](#the-site-recovery-extension-fails-to-update-or-load)

## <a name="protection-fails-because-the-vm-agent-is-unresponsive"></a>Fehler beim Schutzvorgang, weil der VM-Agent nicht reagiert

Fehlermeldung: „Timeout bei der Taskausführung beim Warten auf den Start des Erweiterungsvorgangs.“<br>
Fehlercode: 151099

Dieser Fehler kann auftreten, wenn der Azure-Gast-Agent auf dem virtuellen Computer nicht im Bereitschaftszustand befindet.

Sie können den Status des Azure-Gast-Agents im [Azure-Portal](https://portal.azure.com/) überprüfen. Wechseln Sie zum virtuellen Computer, den Sie schützen möchten, und überprüfen Sie den Status unter **VM** > **Einstellungen** > **Eigenschaften** > **Agent-Status**. Meistens ist der Status des Agents nach dem Neustart des virtuellen Computers „Bereit“. Wenn jedoch ein Neustart nicht möglich ist, oder das Problem weiterhin besteht, führen Sie die folgenden Schritte zur Problembehebung aus:

- [Der Agent ist auf der VM installiert, reagiert aber nicht (für Windows-VMs)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [Der auf der VM installierte Agent ist veraltet (für Linux-VMs)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)


Fehlermeldung: „Timeout bei der Taskausführung beim Warten auf den Start des Erweiterungsvorgangs.“<br>
Fehlercode: 151095

Dieser Fehler tritt auf, wenn auf dem Linux-Computer eine veraltete Agent-Version installiert ist. Führen Sie folgenden Schritt aus, um das Problem zu beheben:

- [Der auf der VM installierte Agent ist veraltet (für Linux-VMs)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)  

## <a name="causes-and-solutions"></a>Ursachen und Lösungen

### <a name="the-agent-is-installed-in-the-vm-but-its-unresponsive-for-windows-vms"></a><a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Der Agent ist auf dem virtuellen Computer installiert, reagiert aber nicht (bei virtuellen Windows-Computern)

#### <a name="solution"></a>Lösung
Der VM-Agent wurde möglicherweise beschädigt, oder der Dienst wurde angehalten. Durch Neuinstallation des VM-Agents erhalten Sie die neueste Version. Dadurch wird auch die Kommunikation mit dem Dienst neu gestartet.

1. Ermitteln Sie, ob der Microsoft Azure-Gast-Agent-Dienst in den VM-Diensten ausgeführt wird (services.msc). Starten Sie den Windows-Azure-Gast-Agent-Dienst neu.    
1. Wenn der Windows-Azure-Gast-Agent-Dienst in den Diensten nicht angezeigt wird, öffnen Sie die Systemsteuerung. Wechseln Sie zu **Programme und Features**, um festzustellen, ob der Windows-Gast-Agent-Dienst installiert ist.
1. Wenn der Microsoft Azure-Gast-Agent unter **Programme und Funktionen** angezeigt wird, deinstallieren Sie ihn.
1. Laden Sie die [aktuelle Version der Agent-MSI-Datei](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) herunter, und installieren Sie sie. Zum Durchführen der Installation benötigen Sie Administratorberechtigungen.
1. Überprüfen Sie, ob der Windows-Azure-Gast-Agent-Dienst in den Diensten angezeigt wird.
1. Starten Sie den Schutzauftrag neu.

Überprüfen Sie auch, ob [Microsoft .NET 4.5](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) auf dem virtuellen Computer installiert ist. Sie benötigen .NET 4.5 für die Kommunikation des VM-Agent mit dem Dienst.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Der auf dem virtuellen Computer installierte Agent ist veraltet (bei virtuellen Linux-Computern).

#### <a name="solution"></a>Lösung
Die meisten Fehler im Zusammenhang mit Agents oder Erweiterungen bei virtuellen Linux-Computern werden durch Probleme verursacht, die einen veralteten VM-Agent betreffen. Befolgen Sie diese allgemeinen Richtlinien, um dieses Problem zu beheben:

1. Folgen Sie den Anweisungen unter [Aktualisieren des Linux-VM-Agents ](../virtual-machines/extensions/update-linux-agent.md).

   > [!NOTE]
   > Wir *empfehlen dringend*, den Agent ausschließlich über ein Verteilungsrepository zu aktualisieren. Wir raten davon ab, den Agent-Code direkt von GitHub herunterzuladen und die Aktualisierung durchzuführen. Falls der aktuelle Agent für Ihre Distribution nicht verfügbar ist, können Sie sich an den zuständigen Support wenden, um Informationen zur Installation zu erhalten. Eine Prüfung auf den aktuellen Agent können Sie auf der Seite für den [Windows Azure-Linux-Agent](https://github.com/Azure/WALinuxAgent/releases) im GitHub-Repository durchführen.

1. Stellen Sie mit dem folgenden Befehl sicher, dass der Azure-Agent auf dem virtuellen Computer ausgeführt wird: `ps -e`

   Wenn der Prozess nicht ausgeführt wird, starten Sie ihn mit den folgenden Befehlen neu:

   - Für Ubuntu: `service walinuxagent start`
   - Für andere Distributionen: `service waagent start`

1. [Konfigurieren Sie den Agent für den automatischen Neustart](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
1. Aktivieren Sie den Schutz für den virtuellen Computer.

### <a name="the-site-recovery-extension-fails-to-update-or-load"></a>Die Site Recovery-Erweiterung kann nicht aktualisiert oder geladen werden.

Der Status der Erweiterungen ist „Leer“, „NotReady“ oder „Im Übergang“.

#### <a name="solution"></a>Lösung

Deinstallieren Sie die Erweiterung, und wiederholen Sie den Vorgang.

So deinstallieren Sie die Erweiterung:

1. Wechseln Sie im [Azure-Portal](https://portal.azure.com/) zu dem virtuellen Computer, auf dem der Sicherungsfehler auftritt.
1. Wählen Sie **Settings** aus.
1. Wählen Sie **Erweiterungen**.
1. Wählen Sie **Site Recovery-Erweiterung**.
1. Wählen Sie **Deinstallieren** aus.

Wenn die VMSnapshot-Erweiterung im Azure-Portal nicht angezeigt wird, [aktualisieren Sie den Azure-Linux-Agent](../virtual-machines/extensions/update-linux-agent.md). Führen Sie anschließend den Schutzauftrag aus.

Diese Schritte bewirken, dass die Erweiterung während der nächsten Ausführung des Schutzauftrags neu installiert wird.
