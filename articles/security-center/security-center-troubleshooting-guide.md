---
title: Azure Security Center – Handbuch zur Problembehandlung | Microsoft Docs
description: In diesem Dokument wird die Problembehandlung in Azure Security Center beschrieben.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 44462de6-2cc5-4672-b1d3-dbb4749a28cd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/20/2019
ms.author: rkarlin
ms.openlocfilehash: 63275db36bdb64985625c3789d558bd09e2d47bc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60912028"
---
# <a name="azure-security-center-troubleshooting-guide"></a>Azure Security Center – Handbuch zur Problembehandlung
Dieses Handbuch ist für IT-Experten, Informationssicherheitsanalysten und Cloudadministratoren konzipiert, in deren Organisation Azure Security Center verwendet wird und die Security Center-Probleme lösen müssen.

>[!NOTE]
>Ab Anfang Juni 2017 verwendet Security Center den Microsoft Monitoring Agent zum Sammeln und Speichern von Daten. Weitere Informationen finden Sie unter [Azure Security Center – Plattformmigration](security-center-platform-migration.md). Die Informationen in diesem Artikel stellen Security Center-Funktionen nach dem Umstieg auf den Microsoft Monitoring Agent vor.
>

## <a name="troubleshooting-guide"></a>Handbuch zur Problembehandlung
In diesem Handbuch wird beschrieben, wie Sie Probleme mit Security Center beheben. Der Großteil der Problembehandlung in Security Center erfolgt, indem zunächst die Einträge im [Überwachungsprotokoll](../azure-monitor/platform/activity-logs-overview.md) für die fehlerhafte Komponente geprüft werden. Über Überwachungsprotokolle können Sie Folgendes ermitteln:

* Welche Vorgänge durchgeführt werden
* Wer den Vorgang initiiert hat
* Wann der Vorgang durchgeführt wurde
* Status des Vorgangs
* Werte anderer Eigenschaften, mit denen Sie den Vorgang eventuell untersuchen können

Das Überwachungsprotokoll enthält alle Schreibvorgänge (PUT, POST, DELETE), die für Ihre Ressourcen durchgeführt werden, aber keine Lesevorgänge (GET).

## <a name="microsoft-monitoring-agent"></a>Microsoft Monitoring Agent
Security Center verwendet Microsoft Monitoring Agent, um sicherheitsrelevante Daten von Ihren virtuellen Azure-Computern zu sammeln. Dieser Agent wird auch vom Azure Monitor-Dienst verwendet. Nachdem die Datensammlung aktiviert und der Agent auf dem Zielcomputer richtig installiert wurde, sollte dieser Prozess ausgeführt werden:

* HealthService.exe

Wenn Sie die Dienstverwaltungskonsole („services.msc“) öffnen, sehen Sie auch den Microsoft Monitoring Agent als ausgeführten Dienst, wie unten dargestellt:

![Dienste](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig5.png)

Wenn Sie erfahren möchten, welche Version des Agents Sie verwenden, öffnen Sie den **Task-Manager**, und suchen Sie auf der Registerkarte **Prozesse** den **Microsoft Monitoring Agent-Dienst**. Klicken Sie mit der rechten Maustaste darauf, und klicken Sie dann auf **Eigenschaften**. Auf der Registerkarte **Details** wird die Dateiversion wie unten dargestellt angezeigt:

![Datei](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig6.png)


## <a name="microsoft-monitoring-agent-installation-scenarios"></a>Installationsszenarien für den Microsoft Monitoring Agent
Es gibt zwei Installationsszenarien, die bei der Installation des Microsoft Monitoring Agents auf Ihrem Computer zu unterschiedlichen Ergebnissen führen können. Folgende Szenarien werden unterstützt:

* **Der Agent wird automatisch von Security Center installiert:** In diesem Szenario können Sie die Warnungen an beiden Orten einsehen – im Security Center und in der Protokollsuche. Sie erhalten E-Mail-Benachrichtigungen an die E-Mail-Adresse, die in der Sicherheitsrichtlinie für das Abonnement, zu der die Ressource gehört, konfiguriert wurde.
zu erstellen und zu verwalten.
* **Der Agent wird manuell auf einem virtuellen Computer in Azure installiert:** Wenn Sie in diesem Szenario Agents verwenden, die vor Februar 2017 manuell heruntergeladen und installiert wurden, können Sie die Warnungen nur dann im Security Center-Portal anzeigen, wenn Sie das Abonnement filtern, zu dem der Arbeitsbereich gehört. Wenn Sie das Abonnement herausfiltern, zu dem die Ressource gehört, können Sie keine Warnungen anzeigen. Sie erhalten E-Mail-Benachrichtigungen an die E-Mail-Adresse, die in der Sicherheitsrichtlinie für das Abonnement, zu der der Arbeitsbereich gehört, konfiguriert wurde.

>[!NOTE]
> Um das Verhalten im zweiten Szenario zu vermeiden, müssen Sie die neueste Version des Agents herunterladen.
>

## Integritätsprobleme des Überwachungs-Agents <a name="mon-agent"></a>
Mit der **Zustandsüberwachung** wird der Grund dafür definiert, warum Security Center keine erfolgreiche Überwachung von VMs und Computern durchführen kann, die für die automatische Bereitstellung initialisiert wurden. In der folgenden Tabelle sind die Werte, Beschreibungen und Lösungsschritte der **Zustandsüberwachung** enthalten.

| Zustandsüberwachung | BESCHREIBUNG | Lösungsschritte |
|---|---|---|
| Agent-Installation ausstehend | Die Microsoft Monitoring Agent-Installation ist noch nicht abgeschlossen.  Die Installation kann einige Stunden dauern. | Warten Sie, bis die automatische Installation abgeschlossen wurde. |
| Energiezustand „Aus“ | Die VM wurde beendet.  Der Microsoft Monitoring Agent kann nur auf einer VM installiert werden, die ausgeführt wird. | Starten Sie den virtuellen Computer neu. |
| Azure-VM-Agent fehlt oder ist ungültig | Der Microsoft Monitoring Agent wurde noch nicht installiert.  Damit die Erweiterung von Security Center installiert werden kann, ist ein gültiger Azure-VM-Agent erforderlich. | Führen Sie für den Azure-VM-Agent auf der VM die Installation, Neuinstallation oder ein Upgrade durch. |
| VM ist nicht zur Installation bereit  | Der Microsoft Monitoring Agent ist noch nicht installiert, da die VM nicht für die Installation bereit ist. Die VM ist aufgrund eines Problems mit dem VM-Agent oder der VM-Bereitstellung noch nicht für die Installation bereit. | Überprüfen Sie den Status der VM. Kehren Sie im Portal zu **Virtuelle Computer** zurück, und wählen Sie die VM aus, um Statusinformationen zu erhalten. |
|Fehler bei der Installation – allgemeiner Fehler | Der Microsoft Monitoring Agent wurde installiert, aber es ist ein Fehler aufgetreten. | [Führen Sie die Installation der Erweiterung manuell durch](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension), oder deinstallieren Sie die Erweiterung, damit Security Center versucht, die Installation erneut zu starten. |
| Fehler bei der Installation – lokaler Agent ist bereits installiert | Bei der Microsoft Monitoring Agent-Installation ist ein Fehler aufgetreten. Von Security Center wurde ein lokaler Agent (Log Analytics oder System Center Operations Manager) identifiziert, der auf dem virtuellen Computer bereits installiert ist. Zur Vermeidung einer Multi-Homing-Konfiguration, wenn die VM an zwei separate Arbeitsbereiche meldet, wurde die Microsoft Monitoring Agent-Installation beendet. | Es gibt hierfür zwei Lösungsmöglichkeiten: [manuelle Installation der Erweiterung](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension) und Herstellung einer Verbindung mit Ihrem gewünschten Arbeitsbereich. Oder Sie legen Ihren gewünschten Arbeitsbereich als Standardarbeitsbereich fest und aktivieren die automatische Bereitstellung des Agents.  Siehe [Aktivieren der automatischen Bereitstellung](security-center-enable-data-collection.md). |
| Agent kann keine Verbindung mit dem Arbeitsbereich herstellen | Der Microsoft Monitoring Agent wurde installiert, aber es ist ein Fehler im Zusammenhang mit der Netzwerkkonnektivität aufgetreten.  Stellen Sie sicher, dass Internetzugriff besteht oder dass für den Agent ein gültiger HTTP-Proxy konfiguriert wurde. | Siehe die Netzwerkanforderungen für den Überwachungs-Agent. |
| Verbindung des Agents mit fehlendem oder unbekanntem Arbeitsbereich | Security Center hat erkannt, dass der auf der VM installierte Microsoft Monitoring Agent mit einem Arbeitsbereich verbunden ist, für den er keine Zugriffsberechtigung besitzt. | Dies kann in zwei Fällen vorkommen. Der Arbeitsbereich wurde gelöscht und ist nicht mehr vorhanden. Installieren Sie den Agent mit dem richtigen Arbeitsbereich neu, oder deinstallieren Sie den Agent, und lassen Sie für Security Center die Durchführung der automatischen Installation für die Bereitstellung zu. Im zweiten Fall ist der Arbeitsbereich Teil eines Abonnements, für das Security Center keine Berechtigungen besitzt. Für Security Center sind Abonnements erforderlich, um für die Microsoft-Sicherheitsressourcenanbieter den Zugriff zu ermöglichen. Registrieren Sie das Abonnement beim Microsoft-Sicherheitsressourcenanbieter, um dies zu aktivieren. Verwenden Sie hierfür die API, PowerShell oder das Portal, oder filtern Sie im Dashboard **Übersicht** von Security Center einfach nach dem Abonnement. Weitere Informationen finden Sie unter [Ressourcenanbieter und -typen](../azure-resource-manager/resource-manager-supported-services.md#azure-portal). |
| Agent reagiert nicht oder ID fehlt | Security Center kann die von der VM gescannten Sicherheitsdaten nicht abrufen, obwohl der Agent installiert ist. | Der Agent meldet keine Daten, einschließlich Heartbeat. Unter Umständen ist der Agent beschädigt, oder der Datenverkehr ist aus einem bestimmten Grund blockiert. Es kann auch sein, dass der Agent zwar Daten meldet, aber eine Azure-Ressourcen-ID fehlt, sodass die Daten nicht der Azure-VM zugeordnet werden können. Informationen zur Problembehandlung für Linux finden Sie unter [Troubleshooting Guide for Log Analytics Agent for Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md#im-not-seeing-any-linux-data-in-the-oms-portal) (Leitfaden zur Problembehandlung für den Log Analytics-Agent für Linux). Informationen zur Problembehandlung für Windows finden Sie unter [Troubleshooting Windows Virtual Machines](https://github.com/MicrosoftDocs/azure-docs/blob/8c53ac4371d482eda3d85819a4fb8dac09996a89/articles/log-analytics/log-analytics-azure-vm-extension.md#troubleshooting-windows-virtual-machines) (Problembehandlung für virtuelle Windows-Computer). |
| Agent nicht installiert | Die Datensammlung ist deaktiviert. | Aktivieren Sie die Datensammlung in der Sicherheitsrichtlinie, oder installieren Sie den Microsoft Monitoring Agent manuell. |


## Beheben von Problemen mit den Netzwerkanforderungen für den Überwachungs-Agent <a name="mon-network-req"></a>
Damit Agents eine Verbindung mit Security Center herstellen und sich bei diesem registrieren können, müssen sie Zugriff auf Netzwerkressourcen, einschließlich der Portnummern und Domänen-URLs, haben.

- Für Proxy-Server müssen Sie sicherstellen, dass die entsprechenden Proxy-Serverressourcen in Agenteinstellungen konfiguriert sind. Weitere Informationen zum [Ändern der Proxyeinstellungen](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) finden Sie in diesem Artikel.
- Wenn eine Firewall den Zugriff auf das Internet einschränkt, müssen Sie die Firewall so konfigurieren, dass Log Analytics Zugriff erhält. In den Agenteinstellungen ist keine Aktion erforderlich.

Die folgende Tabelle zeigt die für die Kommunikation erforderlichen Ressourcen.

| Agent-Ressource | Ports | Umgehung der HTTPS-Überprüfung |
|---|---|---|
| *.ods.opinsights.azure.com | 443 | Ja |
| *.oms.opinsights.azure.com | 443 | Ja |
| *.blob.core.windows.net | 443 | Ja |
| *.azure-automation.net | 443 | Ja |

Wenn mit dem Agent Onboardingprobleme auftreten, lesen Sie den Artikel [How to troubleshoot Operations Management Suite onboarding issues](https://support.microsoft.com/en-us/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues) (Behandeln von Onboardingproblemen mit Operations Management Suite).


## <a name="troubleshooting-endpoint-protection-not-working-properly"></a>Schwierigkeiten bei der Endpoint Protection-Problembehandlung

Der Gast-Agent ist der übergeordnete Prozess aller Vorgänge der [Microsoft Antimalware](../security/azure-security-antimalware.md)-Erweiterung. Wenn beim Prozess für den Gast-Agent ein Fehler auftritt, schlägt die Microsoft Antimalware, die als untergeordneter Prozess des Gast-Agents ausgeführt wird, unter Umständen auch fehl.  In diesen Fällen ist es ratsam, die folgenden Optionen zu überprüfen:

- Wenn die Ziel-VM ein benutzerdefiniertes Image ist und der Ersteller der VM den Gast-Agent nicht installiert hat.
- Wenn das Ziel keine Windows-VM ist, sondern eine Linux-VM, tritt bei der Installation der Windows-Version der Antimalware-Erweiterung auf einer Linux-VM ein Fehler auf. Der Linux-Gast-Agent verfügt über bestimmte Anforderungen in Bezug auf die Betriebssystemversion und die erforderlichen Pakete, und wenn diese Anforderungen nicht erfüllt werden, funktioniert der VM-Agent auch dafür nicht.
- Wenn die VM mit einer alten Version des Gast-Agent erstellt wurde. In diesem Fall sollten Sie beachten, dass einige alte Agents nicht selbst automatisch das Update auf die neuere Version durchführen können, weil dieses Problem dadurch verursacht werden kann. Verwenden Sie immer die aktuelle Version des Gast-Agents, wenn Sie Ihre eigenen Images erstellen.
- Für einige Verwaltungssoftwareanwendungen von Drittanbietern wird der Gast-Agent ggf. deaktiviert oder der Zugriff auf bestimmte Dateispeicherorte blockiert. Stellen Sie sicher, dass der Agent in der Ausschlussliste enthalten ist, wenn auf Ihrer VM Software von Drittanbietern installiert ist.
- Bestimmte Firewalleinstellungen oder Netzwerksicherheitsgruppen (NSGs) können bewirken, dass der Netzwerkdatenverkehr zum und vom Gast-Agent blockiert wird.
- Es kann sein, dass bestimmte Zugriffssteuerungslisten (Access Control Lists, ACLs) den Datenträgerzugriff verhindern.
- Durch einen Mangel an Speicherplatz kann verhindert werden, dass der Gast-Agent richtig funktioniert.

Die Microsoft Antimalware-Benutzeroberfläche ist standardmäßig deaktiviert. Unter [Enabling Microsoft Antimalware User Interface on ARM VMs Post Deployment](https://blogs.msdn.microsoft.com/azuresecurity/2016/03/09/enabling-microsoft-antimalware-user-interface-post-deployment/) (Aktivieren der Microsoft Antimalware-Benutzeroberfläche auf ARM-VMs nach der Bereitstellung) finden Sie weitere Informationen dazu, wie Sie sie bei Bedarf aktivieren.

## <a name="troubleshooting-problems-loading-the-dashboard"></a>Behandlung von Problemen beim Laden des Dashboards

Wenn beim Laden des Security Center-Dashboards Probleme auftreten, stellen Sie sicher, dass der Benutzer, der das Security Center-Abonnement registriert (d.h. der erste Benutzer, der Security Center mit dem Abonnement geöffnet hat), und der Benutzer, der die Datensammlung aktivieren möchte, entweder *Besitzer* oder *Mitwirkender* des Abonnements sind. Ab diesem Zeitpunkt können auch Benutzer mit der *Leser*-Berechtigung für das Abonnement die Richtlinie unter „dashboard/alerts/recommendation/policy“ anzeigen.

## <a name="contacting-microsoft-support"></a>Kontaktaufnahme mit dem Microsoft-Support
Einige Probleme können mit den Richtlinien in diesem Artikel identifiziert werden, während andere im öffentlichen Security Center- [Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSecurityCenter)dokumentiert sind. Falls Sie weitere Hilfe zur Problembehandlung benötigen, können Sie wie unten gezeigt über das **Azure-Portal** eine neue Supportanfrage erstellen:

![Microsoft-Support](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig2.png)


## <a name="see-also"></a>Weitere Informationen
In diesem Dokument haben Sie erfahren, wie Sie Sicherheitsrichtlinien in Azure Security Center konfigurieren können. Weitere Informationen zu Azure Security Center finden Sie in den folgenden Quellen:

* [Planungs- und Betriebshandbuch für Azure Security Center](security-center-planning-and-operations-guide.md) : Erfahren Sie, wie Sie Entwurfsüberlegungen zur Einführung von Azure Security Center planen und umsetzen können.
* [Überwachen der Sicherheitsintegrität in Azure Security Center](security-center-monitoring.md) : Erfahren Sie, wie Sie die Integrität Ihrer Azure-Ressourcen überwachen.
* [Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center](security-center-managing-and-responding-alerts.md) : Erfahren Sie, wie Sie Sicherheitswarnungen verwalten und darauf reagieren.
* [Überwachen von Partnerlösungen mit Azure Security Center](security-center-partner-solutions.md) : Erfahren Sie, wie der Integritätsstatus Ihrer Partnerlösungen überwacht wird.
* [Azure Security Center – Häufig gestellte Fragen](security-center-faq.md) : Hier finden Sie häufig gestellte Fragen zur Verwendung des Diensts.
* [Azure Security Blog](https://blogs.msdn.com/b/azuresecurity/) (Blog zur Azure-Sicherheit): Hier finden Sie Blogbeiträge zur Azure-Sicherheit und -Compliance.
