---
title: 'Azure Automation-Updateverwaltung: Übersicht'
description: Dieser Artikel bietet eine Übersicht über die Updateverwaltungsfunktion, die Updates für Ihre Windows- und Linux-Computer implementiert.
services: automation
ms.subservice: update-management
ms.date: 01/22/2021
ms.topic: conceptual
ms.openlocfilehash: 6e312d354a25113a764bca5e9492909d22af9873
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "100007736"
---
# <a name="update-management-overview"></a>Übersicht über die Updateverwaltung

Mithilfe der Updateverwaltung in Azure Automation können Sie Betriebssystemupdates für Ihre Windows- und Linux-VMs in Azure, in lokalen Umgebungen und in anderen Cloudumgebungen verwalten. Sie können den Status der verfügbaren Updates auf allen Agent-Computern schnell auswerten und die Installation der für den Server erforderlichen Updates initiieren.

> [!NOTE]
> Auf einem Computer, der mit der Updateverwaltung konfiguriert ist, können Sie keine benutzerdefinierten Skripts aus Azure Automation ausführen. Auf diesem Computer kann nur das von Microsoft signierte Updateskript ausgeführt werden.

> [!NOTE]
> Zum aktuellen Zeitpunkt wird das Aktivieren der Updateverwaltung direkt von einem Arc-fähigen Server aus nicht unterstützt. Informationen zu den Anforderungen und zum Aktivieren für Ihren Server finden Sie unter [Aktivieren der Updateverwaltung aus Ihrem Automation-Konto](../../automation/update-management/enable-from-automation-account.md).

Informationen zum automatischen Herunterladen und Installieren verfügbarer Patches vom Typ *Kritisch* und *Sicherheit* auf Ihren virtuellen Azure-Computer finden Sie unter [Automatische VM-Gastpatches](../../virtual-machines/windows/automatic-vm-guest-patching.md) für Windows-VMs.

Machen Sie sich mit den Informationen in den folgenden Abschnitten vertraut, bevor Sie die Updateverwaltung bereitstellen und die Verwaltung auf Ihren Computern ermöglichen.  

## <a name="about-update-management"></a>Informationen zur Updateverwaltung

Computer, die von der Updateverwaltung verwaltet werden, sind bei der Durchführung der Bewertung und Bereitstellung von Updates auf Folgendes angewiesen:

* [Log Analytics-Agent](../../azure-monitor/platform/log-analytics-agent.md) für Windows oder Linux
* PowerShell Desired State Configuration (DSC) für Linux
* Automation Hybrid Runbook Worker (wird automatisch installiert, wenn Sie die Updateverwaltung auf dem Computer aktivieren)
* Microsoft Update oder [Windows Server Update Services](/windows-server/administration/windows-server-update-services/get-started/windows-server-update-services-wsus) (WSUS) für Windows-Computer
* Privates oder öffentliches Updaterepository für Linux-Computer

Das folgende Diagramm veranschaulicht, wie die Updateverwaltung alle verbundenen Windows Server- und Linux-Server eines Arbeitsbereichs bewertet und Sicherheitsupdates darauf anwendet:

![Updateverwaltungs-Workflow](./media/overview/update-mgmt-updateworkflow.png)

Die Updateverwaltung kann für das systeminterne Bereitstellen von Computern in mehreren Abonnements im selben Mandanten verwendet werden.

Nachdem ein Paket veröffentlicht wurde, dauert es zwei bis drei Stunden, bis der Patch für Linux-Computer für die Bewertung angezeigt wird. Bei Windows-Computern dauert es 12 bis 15 Stunden, bis der Patch nach der Veröffentlichung für die Bewertung angezeigt wird. Wenn ein Computer einen Scanvorgang abgeschlossen hat, um die Konformität für das Update zu überprüfen, leitet der Agent die Informationen gesammelt an Azure Monitor-Protokolle weiter. Auf einem Windows-Computer wird der Konformitätsscan standardmäßig alle 12 Stunden ausgeführt. Für einen Linux-Computer wird der Konformitätsscan standardmäßig jede Stunde durchgeführt. Wenn der Log Analytics-Agent neu gestartet wird, wird ein Konformitätsscan innerhalb von 15 Minuten gestartet.

Darüber hinaus wird der Update-Konformitätsscan innerhalb von 15 Minuten nach dem Neustart des Log Analytics-Agents sowie vor und nach der Updateinstallation gestartet.

Die Updateverwaltung meldet basierend auf der für die Synchronisierung konfigurierten Quelle, wie aktuell der Computer ist. Wenn der Windows-Computer für das Senden von Meldungen an [Windows Server Update Services](/windows-server/administration/windows-server-update-services/get-started/windows-server-update-services-wsus) (WSUS) konfiguriert ist, können sich die Ergebnisse von den angezeigten Microsoft Update-Ergebnissen unterscheiden. Dies hängt davon ab, wann WSUS zuletzt mit Microsoft Update synchronisiert wurde. Dasselbe gilt für Linux-Computer, die für Meldungen an ein lokales Repository konfiguriert sind (anstatt an ein öffentliches Repository).

> [!NOTE]
> Damit Meldungen an den Dienst ordnungsgemäß erfolgen können, erfordert die Updateverwaltung, dass bestimmte URLs und Ports aktiviert werden. Weitere Informationen zu diesen Anforderungen finden Sie unter [Netzwerkkonfiguration](../automation-hybrid-runbook-worker.md#network-planning).

Sie können Softwareupdates auf Computern bereitstellen und installieren, für die die Updates erforderlich sind, indem Sie einen geplante Bereitstellung erstellen. Updates, die als optional klassifiziert sind, sind im Bereitstellungsumfang von Windows-Computern nicht enthalten. Nur erforderliche Updates sind im Bereitstellungsumfang enthalten.

Die geplante Bereitstellung definiert, welche Zielcomputer die anwendbaren Updates erhalten. Hierzu werden entweder explizit bestimmte Computer angegeben, oder es wird eine [Computergruppe](../../azure-monitor/platform/computer-groups.md) ausgewählt, die auf Protokollsuchvorgängen einer bestimmten Gruppe von Computern basiert (oder auf einer [Azure-Abfrage](query-logs.md), die virtuelle Azure-Computer auf der Grundlage bestimmter Kriterien dynamisch auswählt). Diese Gruppen unterscheiden sich von der [Bereichskonfiguration](../../azure-monitor/insights/solution-targeting.md), mit der die Zielcomputer gesteuert werden, die die Konfiguration erhalten sollen, um die Updateverwaltung zu aktivieren. Dies verhindert, dass sie die Updatekonformität prüfen und melden und genehmigte erforderliche Updates installieren.

Beim Definieren einer Bereitstellung geben Sie außerdem einen Zeitplan an, um einen Zeitraum zu genehmigen und festzulegen, in dem Updates installiert werden dürfen. Dieser Zeitraum wird das Wartungsfenster bezeichnet. 20 Minuten des Wartungsfensters sind für Neustarts reserviert (sofern ein Neustart erforderlich ist und Sie die entsprechende Neustartoption ausgewählt haben). Wenn das Patchen länger dauert als erwartet und im Wartungsfenster weniger als 20 Minuten verbleiben, wird kein Neustart durchgeführt.

Updates werden mit Runbooks in Azure Automation installiert. Sie können diese Runbooks nicht anzeigen, und für diese Runbooks ist keine Konfiguration erforderlich. Bei der Erstellung einer Updatebereitstellung wird ein Zeitplan erstellt, nach dem für die einbezogenen Computer zur angegebenen Zeit ein Masterrunbook für das Update gestartet wird. Das Masterrunbook startet ein untergeordnetes Runbook für jeden Agent, um die erforderlichen Updates zu installieren.

Wenn die Datums- bzw. Uhrzeitangabe der Updatebereitstellung erreicht ist, führen die Zielcomputer die Bereitstellung parallel aus. Vor der Installation wird ein Scan ausgeführt, um sicherzustellen, dass die Updates weiterhin erforderlich sind. Für WSUS-Clientcomputer tritt ein Fehler bei der Updatebereitstellung auf, wenn die Updates in WSUS nicht genehmigt wurden.

Die Registrierung eines Computers für die Updateverwaltung in mehreren Log Analytics-Arbeitsbereichen (auch als „Multi-Homing“ bezeichnet) wird nicht unterstützt.

## <a name="clients"></a>Clients

### <a name="supported-operating-systems"></a>Unterstützte Betriebssysteme

Die folgende Tabelle enthält die unterstützten Betriebssysteme für Updatebewertungen und -patching. Das Patchen erfordert einen System Hybrid Runbook Worker, der automatisch installiert wird, wenn Sie den virtuellen Computer oder Server für die Verwaltung durch Updateverwaltung aktivieren. Informationen zu den Systemanforderungen für Hybrid Runbook Worker finden Sie unter [Bereitstellen eines Windows Hybrid Runbook Workers](../automation-windows-hrw-install.md) und [Bereitstellen eines Linux Hybrid Runbook Workers](../automation-linux-hrw-install.md).

> [!NOTE]
> Die Updatebewertung von Linux-Computern wird nur in bestimmten Regionen unterstützt, die in der [Zuordnungstabelle](../how-to/region-mappings.md#supported-mappings) für Automation-Konto und Log Analytics-Arbeitsbereich aufgeführt sind.

|Betriebssystem  |Notizen  |
|---------|---------|
|Windows Server 2019 (Datacenter/Standard mit Server Core)<br><br>Windows Server 2016 (Datacenter/Standard ohne Server Core)<br><br>Windows Server 2012 R2 (Datacenter/Standard)<br><br>Windows Server 2012 | |
|Windows Server 2008 R2 (RTM und SP1 Standard)| Die Updateverwaltung unterstützt Bewertungen und Patching für dieses Betriebssystem. Der [Hybrid Runbook Worker](../automation-windows-hrw-install.md) wird für Windows Server 2008 R2 unterstützt. |
|CentOS 6 und 7 (x64)      | Linux-Agents erfordern Zugriff auf ein Updaterepository. Für klassifizierungsbasiertes Patchen muss `yum` Sicherheitsdaten zurückgeben, über die CentOS in den RTM-Releases nicht verfügt. Weitere Informationen zu klassifizierungsbasiertem Patching unter CentOS finden Sie unter [Lösung für die Updateverwaltung in Azure](view-update-assessments.md#linux).          |
|Red Hat Enterprise 6 und 7 (x64)     | Linux-Agents erfordern Zugriff auf ein Updaterepository.        |
|SUSE Linux Enterprise Server 12 (x64)     | Linux-Agents erfordern Zugriff auf ein Updaterepository.        |
|Ubuntu 14.04 LTS, 16.04 LTS und 18.04 LTS (x64)      |Linux-Agents erfordern Zugriff auf ein Updaterepository.         |

> [!NOTE]
> VM-Skalierungsgruppen von Azure können über die Updateverwaltung verwaltet werden. Die Updateverwaltung arbeitet nicht mit dem Basisimage, sondern mit den Instanzen selbst. Die Updates müssen inkrementell geplant werden, damit nicht alle VM-Instanzen auf einmal aktualisiert werden. Sie können Knoten für VM-Skalierungsgruppen hinzufügen. Eine entsprechende Anleitung finden Sie unter [Hinzufügen eines Nicht-Azure-Computers zu Änderungsnachverfolgung und Bestand](../automation-tutorial-installed-software.md#add-a-non-azure-machine-to-change-tracking-and-inventory).

### <a name="unsupported-operating-systems"></a>Nicht unterstützte Betriebssysteme

In der folgenden Tabelle werden Betriebssysteme aufgelistet, die von der Updateverwaltung nicht unterstützt werden:

|Betriebssystem  |Notizen  |
|---------|---------|
|Windows-Client     | Clientbetriebssysteme (z.B. Windows 7 und Windows 10) werden nicht unterstützt.<br> Für Azure Windows Virtual Desktop (WVD) ist die empfohlene Methode<br> zum Verwalten von Updates [Microsoft Endpoint Configuration Manager](../../virtual-desktop/configure-automatic-updates.md) für die Patchverwaltung von Windows 10-Clientcomputern. |
|Windows Server 2016 Nano Server     | Wird nicht unterstützt.       |
|Azure Kubernetes Service-Knoten | Wird nicht unterstützt. Verwenden Sie den unter [Anwenden von Sicherheits- und Kernelupdates auf Linux-Knoten in Azure Kubernetes Service (AKS)](../../aks/node-updates-kured.md) beschriebenen Patchprozess.|

### <a name="system-requirements"></a>Systemanforderungen

Der folgende Abschnitt enthält Informationen zu betriebssystemspezifischen Anforderungen. Weitere Informationen finden Sie unter [Netzwerkplanung](#ports). Informationen zu den Anforderungen für TLS 1.2 finden Sie unter [Erzwingen von TLS 1.2 für Azure Automation](../automation-managing-data.md#tls-12-enforcement-for-azure-automation).

#### <a name="windows"></a>Windows

Softwareanforderungen:

- .NET Framework 4.6 oder höher ist erforderlich. ([.NET Framework herunterladen](/dotnet/framework/install/guide-for-developers).)
- Windows PowerShell 5.1 ist erforderlich. ([Windows Management Framework 5.1 herunterladen](https://www.microsoft.com/download/details.aspx?id=54616).)

Windows-Agents müssen für die Kommunikation mit einem WSUS-Server konfiguriert sein oder erfordern Zugriff auf Microsoft Update. Für Hybridcomputer empfehlen wir, den Log Analytics-Agent für Windows zu installieren, indem Sie zuerst Ihren Computer mit [Azure Arc-fähigen Servern](../../azure-arc/servers/overview.md) verbinden und dann Azure Policy verwenden, um die integrierte Richtlinie [Log Analytics-Agent für Windows Azure Arc-Computer bereitstellen](../../governance/policy/samples/built-in-policies.md#monitoring) zuzuweisen. Wenn Sie alternativ die Überwachung der Computer mit Azure Monitor für VMs planen, verwenden Sie stattdessen die Methode [Azure Monitor für VMs aktivieren](../../governance/policy/samples/built-in-initiatives.md#monitoring).

Sie können die Updateverwaltung zusammen mit Microsoft Endpoint Configuration Manager verwenden. Weitere Informationen zu Integrationsszenarien finden Sie unter [Integrieren der Updateverwaltung in Windows Endpoint Configuration Manager](mecmintegration.md). Der [Log Analytics-Agent für Windows](../../azure-monitor/platform/agent-windows.md) ist für Windows-Server erforderlich, die von Sites in Ihrer Configuration Manager-Umgebung verwaltet werden.

Über Azure Marketplace bereitgestellte virtuelle Windows-Computer sind standardmäßig so konfiguriert, dass sie automatisch Updates vom Windows Update-Dienst erhalten. Dieses Verhalten ändert sich nicht, wenn Sie Ihrem Arbeitsbereich virtuelle Windows-Computer hinzufügen. Wenn Sie Updates mithilfe der Updateverwaltung nicht aktiv verwalten, gilt das Standardverhalten (Updates werden automatisch angewendet).

> [!NOTE]
> Sie können die Gruppenrichtlinie so ändern, dass Computerneustarts nur vom Benutzer durchgeführt werden können, nicht aber vom System. Verwaltete Computer bleiben ggf. hängen, wenn die Updateverwaltung nicht berechtigt ist, den Computer ohne manuelle Benutzerinteraktion neu zu starten. Weitere Informationen finden Sie unter [Konfigurieren der Gruppenrichtlinieneinstellungen für automatische Updates](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates).

#### <a name="linux"></a>Linux

Softwareanforderungen:

- Der Computer benötigt Zugriff auf ein Updaterepository, entweder privat oder öffentlich.
- Für die Interaktion mit der Updateverwaltung ist TLS 1.1 oder TLS 1.2 erforderlich.
- Python 2.x-Installation

> [!NOTE]
> Die Updatebewertung von Linux-Computern wird nur in bestimmten Regionen unterstützt. Informationen finden Sie in der [Zuordnungstabelle](../how-to/region-mappings.md#supported-mappings) für Automation-Konto und Log Analytics-Arbeitsbereich.

Für Hybridcomputer empfehlen wir, den Log Analytics-Agent für Linux zu installieren, indem Sie zuerst Ihren Computer mit [Servern mit Azure Arc-Unterstützung](../../azure-arc/servers/overview.md) verbinden und dann Azure Policy verwenden, um die integrierte Richtlinie [Log Analytics-Agent für Linux-Azure Arc-Computer bereitstellen](../../governance/policy/samples/built-in-policies.md#monitoring) zuzuweisen. Wenn Sie alternativ die Überwachung der Computer mit Azure Monitor für VMs planen, verwenden Sie stattdessen die Methode [Azure Monitor für VMs aktivieren](../../governance/policy/samples/built-in-initiatives.md#monitoring).

Virtuelle Computer, die auf der Grundlage der über Azure Marketplace erhältlichen On-Demand-RHEL-Images (Red Hat Enterprise Linux) erstellt werden, werden für den Zugriff auf die in Azure bereitgestellte [Red Hat-Updateinfrastruktur (RHUI)](../../virtual-machines/workloads/redhat/redhat-rhui.md) registriert. Alle anderen Linux-Distributionen müssen über das jeweilige Onlinedateirepository der Distributionen mithilfe der von der jeweiligen Distribution unterstützten Methoden aktualisiert werden.

## <a name="permissions"></a>Berechtigungen

Zum Erstellen und Verwalten von Updatebereitstellungen benötigen Sie bestimmte Berechtigungen. Weitere Informationen zu diesen Berechtigungen finden Sie unter [Rollenbasierter Zugriff: Updateverwaltung](../automation-role-based-access-control.md#update-management-permissions).

## <a name="update-management-components"></a>Updateverwaltungskomponenten

Die Updateverwaltung verwendet die in diesem Abschnitt beschriebenen Ressourcen. Diese Ressourcen werden automatisch Ihrem Automation-Konto hinzugefügt, wenn Sie die Updateverwaltung aktivieren.

### <a name="hybrid-runbook-worker-groups"></a>Hybrid-Runbook-Workergruppen

Nachdem Sie die Updateverwaltung aktiviert haben, werden alle direkt mit dem Log Analytics-Arbeitsbereich verbundenen Windows-Computer automatisch als System Hybrid Runbook Worker konfiguriert, um die Runbooks zu unterstützen, die die Updateverwaltung unterstützen.

Jeder von der Updateverwaltung verwaltete Windows-Computer wird im Bereich „Hybrid Worker-Gruppen“ als Hybrid Worker-Systemgruppe für das Automation-Konto aufgeführt. Die Gruppen verwenden die Benennungskonvention `Hostname FQDN_GUID`. Es ist nicht möglich, diese Gruppen mit Runbooks in Ihrem Konto zu erreichen. Entsprechende Versuche sind nicht erfolgreich. Diese Gruppen sind nur für die ausschließliche Unterstützung der Updateverwaltung bestimmt. Weitere Informationen zur Anzeige der Liste von Windows-Computern, die als Hybrid Runbook Worker konfiguriert sind, finden Sie unter [Anzeigen von Hybrid Runbook Workern](../automation-hybrid-runbook-worker.md#view-system-hybrid-runbook-workers).

Sie können die Windows-Computer einer System Hybrid Runbook Worker-Gruppe in Ihrem Automation-Konto hinzufügen, um Automation-Runbooks zu unterstützen, wenn Sie für die Updateverwaltung und die Mitgliedschaft in der Hybrid Runbook Worker-Gruppe dasselbe Konto verwenden. Diese Funktionalität wurde in Version 7.2.12024.0 des Hybrid Runbook Worker hinzugefügt.

### <a name="management-packs"></a>Management Packs

Wenn Ihre Operations Manager-Verwaltungsgruppe [mit einem Log Analytics-Arbeitsbereich verbunden](../../azure-monitor/platform/om-agents.md) ist, werden in Operations Manager die folgenden Management Packs installiert. Diese Management Packs werden auch für die Updateverwaltung auf direkt verbundenen Windows-Computern installiert. Für diese Management Packs fällt kein Konfigurations- oder Verwaltungsaufwand an.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Update Deployment MP

> [!NOTE]
> Wenn Sie über eine Operations Manager 1807- oder 2019-Verwaltungsgruppe verfügen, die mit einem Log Analytics-Arbeitsbereich verbunden ist, und in der Verwaltungsgruppe Agents für die Erfassung von Protokolldaten konfiguriert sind, müssen Sie den Parameter `IsAutoRegistrationEnabled` außer Kraft setzen und in der Regel **Microsoft.IntelligencePacks.AzureAutomation.HybridAgent.Init** auf „True“ festlegen.

Weitere Informationen zu Updates der Management Packs finden Sie unter [Herstellen einer Verbindung zwischen Operations Manager und Azure Monitor-Protokollen](../../azure-monitor/platform/om-agents.md).

> [!NOTE]
> Damit die Updateverwaltung Computer mit dem Log Analytics-Agent vollständig verwalten kann, müssen Sie auf den Log Analytics-Agent für Windows oder den Log Analytics-Agent für Linux aktualisieren. Informationen zum Aktualisieren des Agents finden Sie unter [Aktualisieren eines Operations Manager-Agents](/system-center/scom/deploy-upgrade-agents). In Umgebungen, in denen Operations Manager verwendet wird, muss mindestens System Center Operations Manager 2012 R2 UR14 ausgeführt werden.

## <a name="data-collection"></a>Datensammlung

### <a name="supported-sources"></a>Unterstützte Quellen

In der folgenden Tabelle sind die verbundenen Quellen beschrieben, die von der Updateverwaltung unterstützt werden:

| Verbundene Quelle | Unterstützt | BESCHREIBUNG |
| --- | --- | --- |
| Windows-Agents |Ja |Die Updateverwaltung sammelt Informationen zu Systemupdates von Windows-Agents und startet dann die Installation von erforderlichen Updates. |
| Linux-Agents |Ja |Die Updateverwaltung sammelt Informationen zu Systemupdates von Linux-Agents und startet dann die Installation von erforderlichen Updates für unterstützte Distributionen. |
| Operations Manager-Verwaltungsgruppe |Ja |Die Updateverwaltung sammelt Informationen zu Systemupdates von Agents in einer verbundenen Verwaltungsgruppe.<br/><br/>Es ist keine direkte Verbindung zwischen dem Operations Manager-Agent und Azure Monitor-Protokollen erforderlich. Daten werden von der Verwaltungsgruppe an den Log Analytics-Arbeitsbereich weitergeleitet. |

### <a name="collection-frequency"></a>Sammlungshäufigkeit

Die Updateverwaltung überprüft verwaltete Computer auf Daten mithilfe der folgenden Regeln. Es kann zwischen 30 Minuten und 6 Stunden dauern, bis im Dashboard aktualisierte Daten von verwalteten Computern angezeigt werden.

* Für jeden Windows-Computer: Die Updateverwaltung führt zweimal am Tag eine Überprüfung jedes Computers durch.

* Für jeden Linux-Computer: Die Updateverwaltung führt jede Stunde eine Überprüfung durch.

Die durchschnittliche Datennutzung von Azure Monitor-Protokollen für einen Computer mit Updateverwaltung beträgt etwa 25 MB pro Monat. Dieser Wert ist lediglich eine Schätzung und kann sich abhängig von Ihrer Umgebung ändern. Es empfiehlt sich, die Umgebung zu überwachen, um die exakte Nutzung nachzuverfolgen. Weitere Informationen zum Analysieren des Datenverbrauchs mit Azure Monitor-Protokollen finden Sie unter [Verwalten von Verbrauch und Kosten](../../azure-monitor/platform/manage-cost-storage.md).

## <a name="network-planning"></a><a name="ports"></a>Netzwerkplanung

Überprüfen Sie die [Netzwerkkonfiguration von Azure Automation](../automation-network-configuration.md#hybrid-runbook-worker-and-state-configuration), um ausführliche Informationen zu den Ports, URLs und anderen Netzwerkdetails zu erhalten, die für die Updateverwaltung erforderlich sind.

Bei Windows-Computern müssen Sie auch Datenverkehr zu allen Endpunkten zulassen, die für Windows Update erforderlich sind. Sie finden eine aktualisierte Liste der erforderlichen Endpunkte unter [Probleme im Zusammenhang mit HTTP/Proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy). Wenn Sie über einen lokalen [Windows Update-Server](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment) verfügen, müssen Sie auch Datenverkehr zu dem in Ihrem [WSUS-Schlüssel](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) angegebenen Server zulassen.

Informationen zu den erforderlichen Endpunkten für Red Hat Linux-Computer finden Sie unter [IP-Adressen der Server für die RHUI-Inhaltsübermittlung](../../virtual-machines/workloads/redhat/redhat-rhui.md#the-ips-for-the-rhui-content-delivery-servers). Informationen zu anderen Linux-Distributionen finden Sie in der jeweiligen Dokumentation des Anbieters.

Weitere Informationen zu Ports, die für den Hybrid Runbook Worker erforderlich sind, finden Sie unter [Updateverwaltungsadressen für Hybrid Runbook Worker](../automation-hybrid-runbook-worker.md#update-management-addresses-for-hybrid-runbook-worker).

Wenn Computer im Netzwerk aufgrund von IT-Sicherheitsrichtlinien keine Internetverbindung herstellen können, können Sie ein [Log Analytics-Gateway](../../azure-monitor/platform/gateway.md) einrichten und den Computer so konfigurieren, dass er die Verbindung mit Azure Automation und Azure Monitor über das Gateway herstellt.

## <a name="update-classifications"></a>Updateklassifizierungen

In der folgenden Tabelle sind die Klassifizierungen definiert, die von der Updateverwaltung für Windows-Updates unterstützt werden.

|Klassifizierung  |BESCHREIBUNG  |
|---------|---------|
|Kritische Updates     | Ein Update für ein bestimmtes Problem, mit dem ein entscheidender, nicht sicherheitsrelevanter Fehler behoben wird.        |
|Sicherheitsupdates     | Ein Update für ein produktspezifisches, sicherheitsrelevantes Problem.        |
|Updaterollups     | Eine kumulative Gruppe von Hotfixes, die zur Vereinfachung der Bereitstellung gebündelt sind.        |
|Feature Packs     | Neue Produktfeatures, die nicht im Rahmen eines Produktreleases verteilt werden.        |
|Service Packs     | Eine kumulative Gruppe von Hotfixes, die auf eine Anwendung angewendet werden.        |
|Definitionsupdates     | Ein Update für virenbehaftete oder andere Definitionsdateien.        |
|Tools     | Ein Hilfsprogramm oder Feature, mit dem mindestens eine Aufgabe ausgeführt werden kann.        |
|Aktualisierungen     | Ein Update für eine Anwendung oder Datei, die zurzeit installiert ist.        |

In der nächsten Tabelle sind die für Linux-Updates unterstützten Klassifizierungen definiert.

|Klassifizierung  |BESCHREIBUNG  |
|---------|---------|
|Kritische Updates und Sicherheitsupdates     | Updates für ein spezielles oder produktspezifisches, sicherheitsrelevantes Problem.         |
|Andere Updates     | Alle anderen Updates, die nicht kritisch sind oder bei denen es sich nicht um Sicherheitsupdates handelt.        |

>[!NOTE]
>Die Updateklassifizierung für Linux-Computer ist nur verfügbar, wenn sie in den unterstützten öffentlichen Azure-Cloudregionen verwendet wird. Es gibt keine Klassifizierung von Linux-Updates bei Verwendung der Updateverwaltung in den folgenden Regionen mit nationalen Clouds:
>
>* Azure US Government
>* 21Vianet in China
>
> Anstatt klassifiziert zu werden, werden Updates unter der Kategorie **Andere Updates** gemeldet.
>
> Die Updateverwaltung verwendet Daten, die von den unterstützten Verteilungen veröffentlicht werden, insbesondere in deren veröffentlichten [OVAL](https://oval.mitre.org/)-Dateien (Open Vulnerability and Assessment Language). Da der Internetzugriff aus diesen nationalen Clouds eingeschränkt ist, kann die Updateverwaltung nicht auf diese Dateien zugreifen.

Für Linux kann die Updateverwaltung bei der Anzeige von Bewertungsdaten dank der Datenanreicherung in der Cloud zwischen kritischen Updates und Sicherheitsupdates in der Cloud unter der Klassifizierung **Sicherheit** und **Sonstiges** unterscheiden. Für das Patchen verwendet die Updateverwaltung Klassifizierungsdaten, die auf dem Computer verfügbar sind. Im Gegensatz zu anderen Distributionen verfügt CentOS in der RTM-Version standardmäßig nicht über diese Informationen. Wenn Sie CentOS-Computer so konfiguriert haben, dass für den folgenden Befehl Sicherheitsdaten zurückgegeben werden, kann die Updateverwaltung basierend auf Klassifizierungen Patchvorgänge ausführen.

```bash
sudo yum -q --security check-update
```

Aktuell steht keine unterstützte Methode zur Verfügung, mit der unter CentOS eine native Verfügbarkeit von Klassifizierungsdaten implementiert werden kann. Für Kunden, die diese Funktion möglicherweise selbst implementiert haben, wird derzeit nur eingeschränkter Support bereitgestellt.

Sie müssen das Plug-In „yum-security“ installieren, um Updates in Version 6 von Red Hat Enterprise zu klassifizieren. Unter Red Hat Enterprise Linux 7 ist das Plug-In bereits Teil von yum selbst, und Sie müssen also nichts installieren. Weitere Informationen finden Sie im folgenden [Knowledge-Artikel](https://access.redhat.com/solutions/10021) zu Red Hat.

Wenn Sie die Ausführung eines Updates auf einem Linux-Computer planen, um z. B. nur Updates zu installieren, die der Klassifizierung **Sicherheit** entsprechen, unterscheiden sich die installierten Updates von den Updates, die dieser Klassifizierung entsprechen, oder sie sind eine Teilmenge davon. Wenn eine Bewertung der ausstehenden Betriebssystemupdates für Ihren Linux-Computer durchgeführt wird, werden [OVAL](https://oval.mitre.org/)-Dateien (Open Vulnerability and Assessment Language), die vom Linux-Distributionsanbieter bereitgestellt werden, von der Updateverwaltung für die Klassifizierung verwendet.

Die Kategorisierung erfolgt für Linux-Updates in **Sicherheit** oder **Sonstiges** und basiert auf den OVAL-Dateien, die Updates enthalten, die Sicherheitsprobleme oder Sicherheitsrisiken beheben. Wenn aber der Zeitplan für Updates ausgeführt wird, wird er auf dem Linux-Computer ausgeführt, wobei er den geeigneten Paket-Manager wie YUM, APT oder ZYPPER verwendet, um diese zu installieren. Der Paket-Manager für die Linux-Distribution verfügt möglicherweise über einen anderen Mechanismus zum Klassifizieren von Updates, sodass sich die Ergebnisse von denen, die die Updateverwaltung über die OVAL-Dateien erzielt hat, unterscheiden können. Informationen dazu, wie Sie den Computer manuell überprüfen und verstehen, welche Updates vom Paket-Manager als sicherheitsrelevant eingestuft werden, finden Sie unter [Problembehandlung für die Bereitstellung von Linux-Updates](../troubleshoot/update-management.md#updates-linux-installed-different).

## <a name="integrate-update-management-with-configuration-manager"></a>Integrieren der Updateverwaltung in Configuration Manager

Kunden, die in Microsoft Endpoint Configuration Manager investiert haben, um PCs, Server und mobile Geräte zu verwalten, profitieren auch von der Leistungsstärke und dem Funktionsumfang von Configuration Manager bei der Verwaltung von Softwareupdates. Informationen, wie Sie die Updateverwaltung in Configuration Manager integrieren, finden Sie unter [Integrieren der Updateverwaltung in Windows Endpoint Configuration Manager](mecmintegration.md).

## <a name="third-party-updates-on-windows"></a>Drittanbieterupdates unter Windows

In der Updateverwaltung wird das lokal konfigurierte Updaterepository verwendet, um unterstützte Windows-Systeme zu aktualisieren, entweder WSUS oder Windows Update. Mit Tools wie [System Center Updates Publisher](/configmgr/sum/tools/updates-publisher) können Sie benutzerdefinierte Updates mit WSUS importieren und veröffentlichen. Dadurch kann die Updateverwaltung Computer, auf denen Configuration Manager als Updaterepository verwendet wird, mit Software von Drittanbietern aktualisieren. Informationen zum Konfigurieren von Updates Publisher finden Sie unter [Installieren von Updates Publisher](/configmgr/sum/tools/install-updates-publisher).

## <a name="enable-update-management"></a>Aktivieren der Updateverwaltung

Hier finden Sie die Möglichkeiten, wie Sie die Updateverwaltung aktivieren und zu verwaltende Computer auswählen können:

- Verwendung einer [Azure Resource Manager-Vorlage](enable-from-template.md) für die Bereitstellung der Updateverwaltung in einem neuen oder vorhandenen Automation-Konto und Azure Monitor Log Analytics-Arbeitsbereich in Ihrem Abonnement. Sie konfiguriert nicht den Bereich der Computer, die verwaltet werden sollen. Dies erfolgt als gesonderter Schritt nach der Verwendung der Vorlage.

- Über Ihr [Automation-Konto](enable-from-automation-account.md) für einen oder mehrere Azure- und Nicht-Azure-Computer, einschließlich Server mit Arc-Unterstützung

- Verwenden Sie die [Runbook](enable-from-runbook.md)-Methode **enable-automationsolution**.

- Für eine [ausgewählte Azure-VM](enable-from-vm.md) über die Seite **Virtuelle Computer** im Azure-Portal. Dieses Szenario steht für virtuelle Computer unter Linux oder Windows zur Verfügung.

- Für [mehrere virtuelle Azure-Computer](enable-from-portal.md), indem Sie sie auf der Seite **Virtuelle Computer** im Azure-Portal auswählen.

> [!NOTE]
> Die Updateverwaltung erfordert, dass ein Log Analytics Arbeitsbereich mit Ihrem Automation-Konto verknüpft wird. Eine aktuelle Liste der unterstützten Regionen finden Sie unter [Arbeitsbereichzuordnungen in Azure](../how-to/region-mappings.md). Die Regionszuordnungen haben keine Auswirkung auf die Möglichkeit, virtuelle Computer in einer anderen Region als der Ihres Automation-Kontos zu verwalten.

## <a name="next-steps"></a>Nächste Schritte

* Ausführliche Informationen zur Arbeit mit der Updateverwaltung finden Sie unter [Verwalten von Updates für Ihre VMs](manage-updates-for-vm.md).

* Lesen Sie häufig gestellte Fragen zur Updateverwaltung unter [Häufig gestellte Fragen zu Azure Automation](../automation-faq.md).
