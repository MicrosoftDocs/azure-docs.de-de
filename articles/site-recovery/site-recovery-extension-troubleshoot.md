---
title: Behandeln von Problemen mit Azure Site Recovery-Agents | Microsoft-Dokumentation
description: Dieser Artikel bietet Informationen zu Symptomen, Ursachen und Lösungen von Azure Site Recovery-Agent-Fehlern.
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: 5ea701682c03370cea46f9126ecf78427a776371
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61280670"
---
# <a name="troubleshoot-issues-with-the-azure-site-recovery-agent"></a>Behandeln von Problemen mit dem Azure Site Recovery-Agent

Dieser Artikel enthält Schritte für die Problembehandlung, mit denen Sie Azure Site Recovery-Fehler mit dem VM-Agent und der Erweiterung beheben können.


## <a name="azure-site-recovery-extension-time-out"></a>Timeout der Azure Site Recovery-Erweiterung  

Fehlermeldung: „Timeout bei der Taskausführung beim Warten auf den Start des Erweiterungsvorgangs.“<br>
Fehlercode: 151076

 Azure Site Recovery installiert eine Erweiterung auf dem virtuellen Computer als Teil des Aktivierungsschutzes. Jede der folgenden Bedingungen kann verhindern, dass der Schutzvorgang ausgelöst wird und der Auftrag nicht ausgeführt wird. Führen Sie die folgenden Problembehandlungsschritte aus, und wiederholen Sie dann den Vorgang:

**Ursache 1: [Der Agent ist auf der VM installiert, reagiert aber nicht (für Windows-VMs)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**     
**Ursache 2: [Der auf der VM installierte Agent ist veraltet (für Linux-VMs)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Ursache 3: [Die Site Recovery-Erweiterung kann nicht aktualisiert oder geladen werden.](#the-site-recovery-extension-fails-to-update-or-load)**  

Fehlermeldung: „Der vorherige Site Recovery-Erweiterungsvorgang nimmt mehr Zeit in Anspruch als erwartet.“<br>
Fehlercode: 150066<br>

**Ursache 1: [Der Agent ist auf der VM installiert, reagiert aber nicht (für Windows-VMs)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**     
**Ursache 2: [Der auf der VM installierte Agent ist veraltet (für Linux-VMs)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Ursache 3: [Der Status der Site Recovery-Erweiterung ist nicht korrekt.](#the-site-recovery-extension-fails-to-update-or-load)**  

## <a name="protection-fails-because-the-vm-agent-is-unresponsive"></a>Fehler beim Schutzvorgang, weil der VM-Agent nicht reagiert

Fehlermeldung: „Timeout bei der Taskausführung beim Warten auf den Start des Erweiterungsvorgangs.“<br>
Fehlercode: 151099<br>

Dieser Fehler kann auftreten, wenn der Azure-Gast-Agent auf dem virtuellen Computer nicht im Bereitschaftszustand befindet.
Sie können den Status des Azure-Gast-Agents im [Azure-Portal](https://portal.azure.com/) überprüfen. Wechseln Sie zum virtuellen Computer, den Sie schützen möchten, und überprüfen Sie den Status unter „VM > Einstellungen > Eigenschaften > Agent-Status“. Meistens ist der Status des Agents nach dem Neustart des virtuellen Computers „Bereit“. Wenn jedoch ein Neustart nicht möglich ist, oder das Problem weiterhin besteht, führen Sie die folgenden Schritte zur Problembehebung aus.

**Ursache 1: [Der Agent ist auf der VM installiert, reagiert aber nicht (für Windows-VMs)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**     
**Ursache 2: [Der auf der VM installierte Agent ist veraltet (für Linux-VMs)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  


Fehlermeldung: „Timeout bei der Taskausführung beim Warten auf den Start des Erweiterungsvorgangs.“<br>
Fehlercode: 151095<br>

Dieser Fehler tritt auf, wenn auf dem Linux-Computer eine alte Agent-Version installiert ist. Führen Sie folgenden Schritt aus, um das Problem zu beheben.<br>
  **Ursache 1: [Der auf der VM installierte Agent ist veraltet (für Linux-VMs)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
## <a name="causes-and-solutions"></a>Ursachen und Lösungen

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Der Agent ist auf dem virtuellen Computer installiert, reagiert aber nicht (bei virtuellen Windows-Computern)

#### <a name="solution"></a>Lösung
Der VM-Agent wurde möglicherweise beschädigt, oder der Dienst wurde angehalten. Durch Neuinstallation des VM-Agents erhalten Sie die neueste Version. Dadurch wird auch die Kommunikation mit dem Dienst neu gestartet.

1. Ermitteln Sie, ob der „Windows-Azure-Gast-Agent-Dienst“ in den VM-Diensten ausgeführt wird (services.msc). Starten Sie den „Windows-Azure-Gast-Agent-Dienst“ neu.    
2. Wenn der Windows-Azure-Gast-Agent-Dienst in den Diensten nicht angezeigt wird, wechseln Sie in der Systemsteuerung zu **Programme und Funktionen**, um zu ermitteln, ob der Windows-Gast-Agent-Dienst installiert ist.
4. Wenn der Windows-Azure-Gast-Agent unter **Programme und Funktionen** angezeigt wird, deinstallieren Sie ihn.
5. Laden Sie die [aktuelle Version der Agent-MSI-Datei](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) herunter, und installieren Sie sie. Zum Durchführen der Installation benötigen Sie Administratorberechtigungen.
6. Überprüfen Sie, ob der Windows-Azure-Gast-Agent-Dienst in den Diensten angezeigt wird.
7. Starten Sie den Schutzauftrag neu.

Überprüfen Sie auch, ob [Microsoft .NET 4.5](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) auf dem virtuellen Computer installiert ist. .NET 4.5 ist für die Kommunikation des VM-Agents mit dem Dienst erforderlich.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Der auf dem virtuellen Computer installierte Agent ist veraltet (bei virtuellen Linux-Computern).

#### <a name="solution"></a>Lösung
Die meisten Fehler im Zusammenhang mit Agents oder Erweiterungen bei virtuellen Linux-Computern werden durch Probleme verursacht, die einen veralteten VM-Agent betreffen. Befolgen Sie diese allgemeinen Richtlinien, um dieses Problem zu beheben:

1. Folgen Sie den Anweisungen unter [Aktualisieren des Linux-VM-Agents ](../virtual-machines/linux/update-agent.md).

   > [!NOTE]
   > Wir *empfehlen dringend*, den Agent ausschließlich über ein Verteilungsrepository zu aktualisieren. Wir raten davon ab, den Agent-Code direkt von GitHub herunterzuladen und die Aktualisierung durchzuführen. Falls der aktuelle Agent für Ihre Distribution nicht verfügbar ist, können Sie sich an den zuständigen Support wenden, um Informationen zur Installation zu erhalten. Eine Prüfung auf den aktuellen Agent können Sie auf der Seite für den [Windows Azure-Linux-Agent](https://github.com/Azure/WALinuxAgent/releases) im GitHub-Repository durchführen.

2. Stellen Sie mit dem folgenden Befehl sicher, dass der Azure-Agent auf dem virtuellen Computer ausgeführt wird: `ps -e`

   Wenn der Prozess nicht ausgeführt wird, starten Sie ihn mit den folgenden Befehlen neu:

   * Für Ubuntu: `service walinuxagent start`
   * Für andere Distributionen: `service waagent start`

3. [Konfigurieren Sie den Agent für den automatischen Neustart](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Aktivieren Sie den Schutz für den virtuellen Computer.



### <a name="the-site-recovery-extension-fails-to-update-or-load"></a>Die Site Recovery-Erweiterung kann nicht aktualisiert oder geladen werden.
Wenn der Status der Erweiterungen „Leer“, „NotReady“ oder „Im Übergang“ ist.

#### <a name="solution"></a>Lösung

Deinstallieren Sie die Erweiterung, und wiederholen Sie den Vorgang.

So deinstallieren Sie die Erweiterung:

1. Wechseln Sie im [Azure-Portal](https://portal.azure.com/) zu dem virtuellen Computer, auf dem der Sicherungsfehler auftritt.
2. Wählen Sie **Settings**aus.
3. Wählen Sie **Erweiterungen**.
4. Wählen Sie **Site Recovery-Erweiterung**.
5. Wählen Sie **Deinstallieren**.

Wenn die VMSnapshot-Erweiterung im Azure-Portal nicht angezeigt wird, [aktualisieren Sie den Azure-Linux-Agent](../virtual-machines/linux/update-agent.md) und führen Sie dann den Schutzauftrag aus. 

Diese Schritte bewirken, dass die Erweiterung während der nächsten Ausführung des Schutzauftrags neu installiert wird.


