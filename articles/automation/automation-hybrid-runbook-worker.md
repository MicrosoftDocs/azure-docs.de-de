---
title: 'Azure Automation: Übersicht über Hybrid Runbook Worker'
description: Dieser Artikel enthält eine Übersicht über Hybrid Runbook Worker, mit denen Sie Runbooks auf Computern in Ihrem lokalen Rechenzentrum oder beim Cloudanbieter ausführen können.
services: automation
ms.subservice: process-automation
ms.date: 01/22/2021
ms.topic: conceptual
ms.openlocfilehash: 7cf18b6b677daaf97d425c86a0cad91b3abcb225
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896951"
---
# <a name="hybrid-runbook-worker-overview"></a>Übersicht über Hybrid Runbook Worker

Runbooks in Azure Automation haben möglicherweise keinen Zugriff auf Ressourcen in anderen Clouds oder in Ihrer lokalen Umgebung, da sie auf der Azure-Cloudplattform ausgeführt werden. Sie können Runbooks mit dem Azure Automation-Feature „Hybrid Runbook Worker“ direkt auf dem Computer, der die Rolle hostet, und für Ressourcen in der Umgebung ausführen, um diese lokalen Ressourcen zu verwalten. Runbooks werden in Azure Automation gespeichert und verwaltet und an einzelne oder mehrere zugewiesene Computer übermittelt.

## <a name="runbook-worker-types"></a>Runbook Worker-Typen

Es gibt zwei Arten von Runbook Workern – System und Benutzer. In der folgenden Tabelle sind die Unterschiede zwischen diesen beschrieben.

|type | Beschreibung |
|-----|-------------|
|**System** |Unterstützt einen Satz ausgeblendeter Runbooks, die von der Updateverwaltungsfunktion verwendet werden, die für die Installation von benutzerdefinierten Updates auf Windows- und Linux-Computern vorgesehen sind.<br> Diese Art von Hybrid Runbook Worker ist kein Mitglied einer Hybrid Runbook Worker-Gruppe und führt daher keine Runbooks aus, die auf eine Runbook Worker-Gruppe abzielen. |
|**Benutzer** |Unterstützt benutzerdefinierte Runbooks, die direkt auf dem Windows- und Linux-Computer ausgeführt werden sollen, die Mitglieder einer oder mehrerer Runbook Worker-Gruppen sind. |

Ein Hybrid Runbook Worker kann unter dem Betriebssystem Windows oder Linux ausgeführt werden, und diese Rolle basiert darauf, dass der [Log Analytics-Agent](../azure-monitor/platform/log-analytics-agent.md) an einen [Log Analytics-Arbeitsbereich](../azure-monitor/platform/design-logs-deployment.md) von Azure Monitor Bericht erstattet. Der Arbeitsbereich kann nicht nur den Computer für das unterstützte Betriebssystem überwachen, sondern auch die Komponenten herunterladen, die für die Installation des Hybrid Runbook Workers benötigt werden.

Wenn die Azure Automation-[Updateverwaltung](./update-management/overview.md) aktiviert ist, werden alle mit dem Log Analytics-Arbeitsbereich verbundenen Computer automatisch als System-Hybrid Runbook Worker konfiguriert. Informationen zur Konfiguration als Windows Hybrid Runbook Worker-Benutzer finden Sie unter [Bereitstellen eines Windows Hybrid Runbook Worker](automation-windows-hrw-install.md) und für Linux unter [Bereitstellen eines Linux Hybrid Runbook Worker](automation-linux-hrw-install.md).

## <a name="how-does-it-work"></a>Wie funktioniert dies?

![Übersicht über Hybrid Runbook Worker](media/automation-hybrid-runbook-worker/automation.png)

Jeder Benutzer-Hybrid Runbook Worker ist Mitglied einer Hybrid Runbook Worker-Gruppe, die Sie beim Installieren des Workers angeben. Eine Gruppe kann einen einzelnen Worker umfassen, aber für Hochverfügbarkeit können Sie mehrere Worker in eine Gruppe aufnehmen. Jeder Computer kann eine Hybrid Runbook Worker-Berichterstellung an ein Automation-Konto hosten. Sie können den Hybrid Worker nicht bei mehreren Automation-Konten registrieren. Ein Hybrid Worker kann nur auf Aufträge eines einzelnen Automation-Kontos lauschen. Computer, auf denen der von der Updateverwaltung verwaltete System-Hybrid Runbook Worker gehostet wird, können einer Hybrid Runbook Worker-Gruppe hinzugefügt werden. Sie müssen aber für die Updateverwaltung und die Mitgliedschaft in der Hybrid Runbook Worker-Gruppe dasselbe Automation-Konto verwenden.

Wenn Sie ein Runbook auf einen Hybrid Runbook Worker starten, geben Sie die Gruppe an, in der das Runbook ausgeführt werden soll. Jeder Worker in der Gruppe ruft Azure Automation ab, um festzustellen, ob Aufträge verfügbar sind. Wenn ein Auftrag verfügbar ist, übernimmt ihn der erste verfügbare Worker. Die Verarbeitungszeit der Auftragswarteschlange hängt vom Hybrid Worker-Hardwareprofil und der Auslastung ab. Sie können keinen bestimmten Worker angeben. Ein Hybrid Worker arbeitet an einem Abrufmechanismus (alle 30 Sekunden) und folgt der Reihenfolge „wer zuerst kommt, wird zuerst bedient“ (first-come, first-serve). Je nachdem, wann ein Auftrag gepusht wurde, wird der Auftrag von dem Hybrid Worker übernommen, der den Automation-Dienst anpingt. Ein einzelner Hybrid Worker kann generell vier Aufträge pro Ping abrufen (d. h. alle 30 Sekunden). Wenn Ihre Rate von Pushaufträgen höher als vier in 30 Sekunden ist, besteht eine hohe Wahrscheinlichkeit, dass ein anderer Hybrid Worker in der Hybrid Runbook Worker-Gruppe den Auftrag übernommen hat.

Ein Hybrid Runbook Worker weist viele der [Ressourceneinschränkungen](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) einer [Azure-Sandbox](automation-runbook-execution.md#runbook-execution-environment) hinsichtlich Datenträgerplatz, Arbeitsspeicher oder Netzwerksockets nicht auf. Die Grenzwerte eines Hybrid Worker werden nur durch die eigenen Ressourcen des Workers bestimmt und sind nicht durch die Zeitbeschränkung durch die [gleichmäßige Verteilung](automation-runbook-execution.md#fair-share) eingeschränkt, die für Azure-Sandboxes gilt.

Um die Verteilung von Runbooks auf Hybrid Runbook Worker zu steuern, und wann oder wie die Aufträge ausgelöst werden, können Sie den Hybrid Worker für verschiedene Hybrid Runbook Worker-Gruppen innerhalb Ihres Automation-Kontos registrieren. Richten Sie die Aufträge auf die jeweilige Gruppe oder die Gruppen aus, um Ihre Ausführungsanordnung zu unterstützen.

## <a name="hybrid-runbook-worker-installation"></a>Installation von Hybrid Runbook Workern

Der Vorgang zum Installieren eines Benutzer-Hybrid Runbook Workers ist vom Betriebssystem abhängig. In der folgenden Tabelle sind die Bereitstellungstypen definiert.

|Betriebssystem  |Bereitstellungstypen  |
|---------|---------|
|Windows     | [Automatisiert](automation-windows-hrw-install.md#automated-deployment)<br>[Manuell](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Manuell](automation-linux-hrw-install.md#install-a-linux-hybrid-runbook-worker)        |

Die empfohlene Installationsmethode für einen Windows-Computer besteht darin, mithilfe eines Azure Automation-Runbooks den Konfigurationsprozess vollständig zu automatisieren. Falls dies nicht möglich ist, können Sie die Rolle Schritt für Schritt manuell installieren und konfigurieren. Bei Linux-Computern führen Sie zur Installation des Agents auf dem Computer ein Python-Skript aus.

## <a name="network-planning"></a><a name="network-planning"></a>Netzwerkplanung

Überprüfen Sie die [Azure Automation-Netzwerkkonfiguration](automation-network-configuration.md#network-planning-for-hybrid-runbook-worker), um ausführliche Informationen zu den Ports, URLs und anderen Netzwerkdetails zu erhalten, die für Hybrid Runbook Worker erforderlich sind.

### <a name="proxy-server-use"></a>Verwenden von Proxyservern

Wenn Sie einen Proxyserver für die Kommunikation zwischen Azure Automation und den Computern verwenden, auf denen der Log Analytics-Agent ausgeführt wird, müssen Sie sicherstellen, dass auf die entsprechenden Ressourcen zugegriffen werden kann. Das Zeitlimit für Anforderungen vom Hybrid Runbook Worker und von den Automation-Diensten beträgt 30 Sekunden. Nach drei Versuchen tritt bei einer Anforderung ein Fehler auf.

### <a name="firewall-use"></a>Verwenden von Firewalls

Wenn Sie eine Firewall verwenden, um den Zugriff auf das Internet einzuschränken, müssen Sie die Firewall so konfigurieren, dass der Zugriff möglich ist. Wenn Sie das Log Analytics-Gateway als Proxy verwenden, achten Sie darauf, dass es für Hybrid Runbook Worker konfiguriert ist. Weitere Informationen finden Sie unter [Konfigurieren des Log Analytics-Gateways für Automation Hybrid Runbook Worker](../azure-monitor/platform/gateway.md).

### <a name="service-tags"></a>Diensttags

Azure Automation unterstützt Diensttags für virtuelle Azure-Netzwerke, die mit dem Diensttag [GuestAndHybridManagement](../virtual-network/service-tags-overview.md) beginnen. Sie können Diensttags verwenden, um Netzwerkzugriffssteuerungen in [Netzwerksicherheitsgruppen](../virtual-network/network-security-groups-overview.md#security-rules) oder in der [Azure Firewall](../firewall/service-tags.md) zu definieren. Diensttags können anstelle von spezifischen IP-Adressen verwendet werden, wenn Sie Sicherheitsregeln erstellen. Indem Sie den Diensttagnamen **GuestAndHybridManagement** im entsprechenden Quell- oder Zielfeld einer Regel angeben, können Sie den Datenverkehr für den Automation-Dienst zulassen oder verweigern. Dieses Diensttag weist keine Unterstützung für das Zulassen einer präziseren Steuerung durch das Einschränken von IP-Adressbereichen auf eine bestimmte Region auf.

Über das Diensttag für den Azure Automation-Dienst werden nur IP-Adressen für die folgenden Szenarien bereitgestellt:

* Auslösen von Webhooks aus Ihrem virtuellen Netzwerk
* Zulassen der Kommunikation mit dem Automation-Dienst für Hybrid Runbook Workers oder State Configuration-Agents in Ihrem VNET

>[!NOTE]
>Das Diensttag **GuestAndHybridManagement** verfügt derzeit nicht über Unterstützung für die Ausführung von Runbookaufträgen in einer Azure-Sandbox, sondern nur direkt auf einem Hybrid Runbook Worker.

## <a name="support-for-impact-level-5-il5"></a>Unterstützungs für Auswirkungsstufe 5 (Impact Level, IL5)

Azure Automation Hybrid Runbook Worker kann in Azure Government verwendet werden, um Auswirkungsstufe 5-Workloads in einer der zwei folgenden Konfigurationen zu unterstützen:

* [Isolierter virtueller Computer](../azure-government/documentation-government-impact-level-5.md#isolated-virtual-machines). Bei der Bereitstellung wird der gesamte physische Host für diesen Computer verwendet, wodurch die Isolationsstufe bereitgestellt wird, die für die Unterstützung von IL5-Workloads erforderlich ist.

* [Azure Dedicated Host](../azure-government/documentation-government-impact-level-5.md#azure-dedicated-host), der physische Server bereitstellt, die einen oder mehrere virtuelle Computer hosten können und ausschließlich für ein Azure-Abonnement reserviert sind.

>[!NOTE]
>Die Computeisolation durch die Hybrid Runbook Worker-Rolle ist für Azure Commercial- und US Government-Clouds verfügbar.

### <a name="update-management-addresses-for-hybrid-runbook-worker"></a>Updateverwaltung-Adressen für Hybrid Runbook Worker

Zusätzlich zu den Standardadressen und -ports, die für Hybrid Runbook Worker erforderlich sind, verfügt Updateverwaltung über weitere Anforderungen an die Netzwerkkonfiguration, die im Abschnitt [Netzwerkplanung](./update-management/overview.md#ports) beschrieben werden.

## <a name="azure-automation-state-configuration-on-a-hybrid-runbook-worker"></a>Azure Automation State Configuration auf einem Hybrid Runbook Worker

Sie können [Azure Automation State Configuration](automation-dsc-overview.md) auf einem Hybrid Runbook Worker ausführen. Um die Konfiguration von Servern zu verwalten, die den Hybrid Runbook Worker unterstützen, müssen Sie die Server als DSC-Knoten hinzufügen. Weitere Informationen finden Sie unter [Aktivieren von Computern zur Verwaltung durch Azure Automation State Configuration](automation-dsc-onboarding.md).

## <a name="runbook-worker-limits"></a>Grenzwerte für Runbook Worker

Die maximale Anzahl von Hybrid Worker-Gruppen pro Automation-Konto beträgt 4000 und gilt sowohl für System- als auch für Benutzer-Hybrid Worker. Wenn Sie mehr als 4.000 Computer verwalten müssen, empfiehlt es sich, ein weiteres Automation-Konto zu erstellen.

## <a name="runbooks-on-a-hybrid-runbook-worker"></a>Runbooks auf einem Hybrid Runbook Worker

Unter Umständen verfügen Sie über Runbooks, die Ressourcen auf dem lokalen Computer verwalten oder für Ressourcen in der lokalen Umgebung ausgeführt werden, in der ein Benutzer-Hybrid Runbook Worker bereitgestellt wurde. In diesem Fall können Sie sich entscheiden, Ihre Runbooks auf dem Hybrid Worker statt in einem Automation-Konto auszuführen. Runbooks, die auf einem Hybrid Runbook Worker ausgeführt werden, sind strukturell identisch mit denen, die Sie im Automation-Konto ausführen. Weitere Informationen finden Sie unter [Ausführen von Runbooks auf einem Hybrid Runbook Worker](automation-hrw-run-runbooks.md).

### <a name="hybrid-runbook-worker-jobs"></a>Hybrid-Runbook-Worker-Aufträge

Hybrid Runbook Worker-Aufträge werden unter Windows unter dem lokalen Konto **System** und unter Linux unter dem Konto [nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux) ausgeführt. Azure Automation behandelt Aufträge in Hybrid Runbook Workern anders als Aufträge, die in Azure-Sandboxes ausgeführt werden. Weitere Informationen finden Sie unter [Ausführungsumgebung für Runbooks](automation-runbook-execution.md#runbook-execution-environment).

Wenn der Hostcomputer des Hybrid Runbook Workers neu gestartet wird, werden alle ausgeführten Runbookaufträge von vorn oder – im Fall von PowerShell-Workflow-Runbooks – beim letzten Prüfpunkt neu gestartet. Nachdem ein Runbookauftrag mehr als dreimal neu gestartet wurde, wird er angehalten.

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Runbook-Berechtigungen für einen Hybrid Runbook Worker

Da sie auf Nicht-Azure-Ressourcen zugreifen, können Runbooks, die auf einem Benutzer-Hybrid Runbook Worker laufen, nicht den Authentifizierungsmechanismus verwenden, der normalerweise von Runbooks zur Authentifizierung bei Azure-Ressourcen verwendet wird. Ein Runbook stellt entweder eine eigene Authentifizierung für lokale Ressourcen bereit oder konfiguriert die Authentifizierung mithilfe von [verwalteten Identitäten für Azure-Ressourcen](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager). Alternativ hierzu können Sie auch ein ausführendes Konto angeben, um einen Benutzerkontext für alle Runbooks bereitzustellen.

## <a name="view-system-hybrid-runbook-workers"></a>Anzeigen von System-Hybrid Runbook Workern

Nachdem die Updateverwaltungsfunktion auf Windows- oder Linux-Computern aktiviert wurde, können Sie mithilfe der Liste der Hybrid Runbook Worker-Systemgruppe im Azure-Portal einen Bestand erstellen. Sie können im Portal bis zu 2.000 Worker anzeigen, indem Sie im linken Bereich für das ausgewählte Automation-Konto unter **Hybrid worker groups** (Hybrid Worker-Gruppen) die Registerkarte **System hybrid worker groups** (Hybrid Worker-Systemgruppen) auswählen.

:::image type="content" source="./media/automation-hybrid-runbook-worker/system-hybrid-workers-page.png" alt-text="Seite „System hybrid worker groups“ (Hybrid Worker-Systemgruppen) für ein Automation-Konto" border="false" lightbox="./media/automation-hybrid-runbook-worker/system-hybrid-workers-page.png":::

Wenn Sie über mehr als 2.000 Hybrid Worker verfügen und eine vollständige Liste abrufen möchten, können Sie das folgende PowerShell-Skript ausführen:

```powershell
"Get-AzSubscription -SubscriptionName "<subscriptionName>" | Set-AzContext
$workersList = (Get-AzAutomationHybridWorkerGroup -ResourceGroupName "<resourceGroupName>" -AutomationAccountName "<automationAccountName>").Runbookworker
$workersList | export-csv -Path "<Path>\output.csv" -NoClobber -NoTypeInformation"
```

## <a name="next-steps"></a>Nächste Schritte

* Um zu erfahren, wie Sie Ihre Runbooks für die Automatisierung von Prozessen in Ihrem lokalen Rechenzentrum oder in einer anderen Cloudumgebung konfigurieren, lesen Sie sich [Ausführen von Runbooks auf einem Hybrid Runbook Worker](automation-hrw-run-runbooks.md) durch.

* Informationen zum Behandeln von Problemen mit Ihren Hybrid Runbook Workern finden Sie unter [Problembehandlung von Hybrid Runbook Workern](troubleshoot/hybrid-runbook-worker.md#general).
