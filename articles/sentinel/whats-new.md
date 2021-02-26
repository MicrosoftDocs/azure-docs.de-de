---
title: Neuerungen in Azure Sentinel
description: In diesem Artikel werden neue Azure Sentinel-Features der letzten Monate beschrieben.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 02/04/2021
ms.openlocfilehash: 9136947767bffb7bea800cdd2a735794baf8f329
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "100007362"
---
# <a name="whats-new-in-azure-sentinel"></a>Neuerungen in Azure Sentinel

In diesem Artikel werden die neuesten Features für Azure Sentinel sowie neue Features in verwandten Diensten aufgeführt, die Azure Sentinel noch benutzerfreundlicher machen.

Informationen zu älteren bereitgestellten Features finden Sie in unseren [Tech Community-Blogs](https://techcommunity.microsoft.com/t5/azure-sentinel/bg-p/AzureSentinelBlog/label-name/What's%20New).

Entsprechend gekennzeichnete Features sind derzeit als VORSCHAUVERSION verfügbar. In den [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) finden Sie weitere rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden oder anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.


> [!TIP]
> Die Microsoft-Teams für die Bedrohungssuche steuern Abfragen, Playbooks, Arbeitsmappen und Notebooks zur [Azure Sentinel-Community](https://github.com/Azure/Azure-Sentinel) bei, darunter auch spezifische [Hunting-Abfragen](https://github.com/Azure/Azure-Sentinel), die Ihre Teams anpassen und verwenden können. 
>
> Und auch Sie selbst können sich beteiligen! Werden Sie Teil der [GitHub-Community für Azure Sentinel-Bedrohungsspezialisten](https://github.com/Azure/Azure-Sentinel/wiki).
> 

## <a name="january-2021"></a>Januar 2021

- [Analyseregel-Assistent: Verbesserte Abfragebearbeitungsfunktionen (öffentliche Vorschau)](#analytics-rule-wizard-improved-query-editing-experience-public-preview)
- [PowerShell-Modul „Az.SecurityInsights“ (Public Preview)](#azsecurityinsights-powershell-module-public-preview)
- [SQL-Datenbank-Connector](#sql-database-connector)
- [Verbesserte Incidentkommentare](#improved-incident-comments)
- [Dedizierte Log Analytics-Cluster](#dedicated-log-analytics-clusters)
- [Verwaltete Identitäten für Logik-Apps](#logic-apps-managed-identities)
- [Verbesserte Regeloptimierung mit den Vorschaudiagrammen für Analyseregeln](#improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview)


### <a name="analytics-rule-wizard-improved-query-editing-experience-public-preview"></a>Analyseregel-Assistent: Verbesserte Abfragebearbeitungsfunktionen (öffentliche Vorschau)

Der Azure Sentinel-Assistent für geplante Analyseregeln bietet nun die folgenden Verbesserungen für das Erstellen und Bearbeiten von Abfragen:

-   Ein erweiterbares Bearbeitungsfenster mit mehr Platz zum Anzeigen Ihrer Abfrage auf dem Bildschirm
-   Hervorhebung von Schlüsselwörtern im Abfragecode
-   Erweiterte Unterstützung für AutoVervollständigen
-   Validierung von Echtzeitabfragen Fehler in Abfragen werden nun auf der Scrollleiste als roter Block und im Registerkartennamen **Regellogik festlegen** als roter Punkt angezeigt. Außerdem kann eine Abfrage mit Fehlern nicht gespeichert werden.

Weitere Informationen finden Sie im [Tutorial: Standardmäßig verfügbare Erkennung von Bedrohungen](tutorial-detect-threats-built-in.md).
### <a name="azsecurityinsights-powershell-module-public-preview"></a>PowerShell-Modul „Az.SecurityInsights“ (Public Preview)

Azure Sentinel unterstützt jetzt das neue PowerShell-Modul [Az.SecurityInsights](https://www.powershellgallery.com/packages/Az.SecurityInsights/).

Das Modul **Az.SecurityInsights** unterstützt gängige Azure Sentinel-Anwendungsfälle wie etwa die Interaktion mit Incidents, um Dinge wie Status, Schweregrad oder Besitzer zu ändern, sowie das Hinzufügen von Kommentaren und Bezeichnungen zu Incidents und das Erstellen von Lesezeichen.

Wir empfehlen zwar die Verwendung von ARM-Vorlagen ([Azure Resource Manager](/azure/azure-resource-manager/templates/)) für Ihre CI/CD-Pipeline, das Modul **Az.SecurityInsights** ist jedoch bei Aufgaben nach der Bereitstellung hilfreich und für die SOC-Automatisierung vorgesehen.  Ihre SOC-Automatisierung kann beispielsweise Schritte zum Konfigurieren von Datenconnectors, zum Erstellen von Analyseregeln oder zum Hinzufügen von Automatisierungsaktionen zu Analyseregeln enthalten.

Weitere Informationen, einschließlich einer vollständigen Liste und einer Beschreibung der verfügbaren Cmdlets, sowie Parameterbeschreibungen und Beispiele finden Sie in der [PowerShell-Dokumentation zu „Az.SecurityInsights“](/powershell/module/az.securityinsights/).

### <a name="sql-database-connector"></a>SQL-Datenbank-Connector

Azure Sentinel bietet jetzt einen Azure SQL-Datenbank-Connector, mit dem Sie die Überwachungs- und Diagnoseprotokolle Ihrer Datenbanken an Azure Sentinel streamen und kontinuierlich die Aktivitäten in allen Ihren Instanzen überwachen können.

Azure SQL ist eine vollständig verwaltete PaaS-Datenbank-Engine (Platform-as-a-Service), bei der die meisten Funktionen für die Datenbankverwaltung ohne Benutzereingriff erfolgen, z. B. Upgrades, Patches, Sicherungen und Überwachung.

Weitere Informationen finden Sie unter [Verbinden von Diagnose- und Überwachungsprotokollen von Azure SQL-Datenbank](connect-azure-sql-logs.md).

### <a name="improved-incident-comments"></a>Verbesserte Incidentkommentare

Analysten verwenden Incidentkommentare, um bei Incidents zusammenzuarbeiten und Prozesse und Schritte manuell oder im Rahmen eines Playbooks zu dokumentieren. 

Dank unserer verbesserten Kommentierung können Sie nun Ihre Kommentare formatieren und vorhandene Kommentare bearbeiten oder löschen.

Weitere Informationen finden Sie unter [Automatisches Erstellen von Incidents aus Microsoft-Sicherheitswarnungen](create-incidents-from-alerts.md).
### <a name="dedicated-log-analytics-clusters"></a>Dedizierte Log Analytics-Cluster

Azure Sentinel unterstützt jetzt dedizierte Log Analytics-Cluster als Bereitstellungsoption. Die Verwendung eines dedizierten Clusters empfiehlt sich in folgenden Fällen:

- **Erfassung von mehr als 1 TB pro Tag** in Ihrem Azure Sentinel-Arbeitsbereich
- **Mehrere Azure Sentinel-Arbeitsbereiche** in Ihrer Azure-Registrierung

Dedizierte Cluster ermöglichen die Verwendung von Features wie kundenseitig verwalteten Schlüsseln, Lockbox, Mehrfachverschlüsselung und schnelleren arbeitsbereichsübergreifenden Abfragen, wenn sich mehrere Arbeitsbereiche im gleichen Cluster befinden.

Weitere Informationen finden Sie unter [Dedizierte Azure Monitor-Protokollcluster](https://docs.microsoft.com/azure/azure-monitor/log-query/logs-dedicated-clusters).

### <a name="logic-apps-managed-identities"></a>Verwaltete Identitäten für Logik-Apps

Azure Sentinel unterstützt jetzt verwaltete Identitäten für den Logic Apps-Connector für Azure Sentinel. Dadurch können Sie einem bestimmten Playbook direkt Berechtigungen für die Verwendung in Azure Sentinel erteilen, anstatt zusätzliche Identitäten zu erstellen.

- **Ohne verwaltete Identität** benötigt der Logic Apps-Connector eine separate Identität mit einer Azure Sentinel-RBAC-Rolle, damit er in Azure Sentinel verwendet werden kann. Bei der separaten Identität kann es sich um einen Azure AD-Benutzer oder um einen Dienstprinzipal (beispielsweise eine bei Azure AD registrierte Anwendung) handeln.

- **Wenn Sie die Unterstützung verwalteter Identitäten in Ihrer Logik-App aktivieren**, wird die Logik-App bei Azure AD registriert und eine Objekt-ID bereitgestellt. Verwenden Sie die Objekt-ID in Azure Sentinel, um die Logik-App mit einer Azure RBAC-Rolle in Ihrem Azure Sentinel-Arbeitsbereich zuzuweisen. 

Weitere Informationen finden Sie unter

- [Authentifizieren des Zugriffs auf Azure-Ressourcen mithilfe verwalteter Identitäten in Azure Logic Apps](/azure/logic-apps/create-managed-service-identity)
- [Dokumentation zum Logic Apps-Connector für Azure Sentinel](/connectors/azuresentinel) 

### <a name="improved-rule-tuning-with-the-analytics-rule-preview-graphs-public-preview"></a>Verbesserte Regeloptimierung mit den Vorschaudiagrammen für Analyseregeln (Public Preview)

Azure Sentinel ermöglicht nun eine bessere Optimierung Ihrer Analyseregeln, um deren Genauigkeit zu erhöhen und Stördatenverkehr zu verringern.

Nachdem Sie eine Analyseregel auf der Registerkarte **Regellogik festlegen** bearbeitet haben, steht auf der rechten Seite der Bereich **Ergebnissimulation** zur Verfügung. 

Wählen Sie **Mit aktuellen Daten testen** aus. Daraufhin wird von Azure Sentinel eine Simulation der letzten 50 Ausführungen Ihrer Analyseregel durchgeführt. Im generierten Diagramm wird die durchschnittliche Anzahl von Warnungen angezeigt, die durch die Regel generiert worden wären (basierend auf den ausgewerteten Ereignisrohdaten). 

Weitere Informationen finden Sie unter [Definieren der Regelabfragelogik und Konfigurieren von Einstellungen](tutorial-detect-threats-custom.md#define-the-rule-query-logic-and-configure-settings).

## <a name="december-2020"></a>Dezember 2020

- [80 neue integrierte Hunting-Abfragen](#80-new-built-in-hunting-queries)
- [Verbesserungen für den Log Analytics-Agent](#log-analytics-agent-improvements)

### <a name="80-new-built-in-hunting-queries"></a>80 neue integrierte Hunting-Abfragen
 
Mit den integrierten Hunting-Abfragen von Azure Sentinel können SOC-Analysten Lücken in der aktuellen Erkennungsabdeckung verringern und neue Hunting-Spuren initiieren.

Dieses Update für Azure Sentinel umfasst neue Hunting-Abfragen mit Abdeckung der MITRE ATT&CK-Frameworkmatrix:

- **Sammlung**
- **Command-and-Control**
- **Zugriff auf Anmeldeinformationen**
- **Ermittlung**
- **Ausführung**
- **Exfiltration**
- **Auswirkung**
- **Erstzugriff**
- **Persistenz**
- **Rechteausweitung**

Die zusätzlichen Hunting-Abfragen unterstützen Sie bei der Ermittlung verdächtiger Aktivitäten in Ihrer Umgebung. Sie geben zwar unter Umständen auch legitime Aktivitäten und potenziell schädliche Aktivitäten zurück, können aber eine gute Orientierungshilfe bei der Bedrohungssuche sein. 

Wenn Sie nach der Ausführung dieser Abfragen mit den Ergebnissen zufrieden sind, können Sie sie ggf. in Analyseregeln konvertieren oder vorhandenen oder neuen Incidents Hunting-Ergebnisse hinzufügen.

Alle hinzugefügten Abfragen sind über die Azure Sentinel-Hunting-Seite verfügbar. Weitere Informationen finden Sie unter [Suchen nach Bedrohungen mit Azure Sentinel](hunting.md).

### <a name="log-analytics-agent-improvements"></a>Verbesserungen für den Log Analytics-Agent

Azure Sentinel-Benutzer profitieren von folgenden Verbesserungen für den Log Analytics-Agent:

- **Unterstützung weiterer Betriebssysteme**, einschließlich CentOS 8, RedHat 8 und SuSE Linux 15
- **Unterstützung von Python 3** (zusätzlich zu Python 2)

Azure Sentinel verwendet den Log Analytics-Agent, um Ereignisse wie Windows-Sicherheitsereignisse, Syslog-Ereignisse, CEF-Protokolle und Ähnliches an Ihren Arbeitsbereich zu senden.

> [!NOTE]
> Der Log Analytics-Agent wird manchmal auch als OMS-Agent oder als Microsoft Monitoring Agent (MMA) bezeichnet. 
> 

Weitere Informationen finden Sie in der [Log Analytics-Dokumentation](/azure/azure-monitor/platform/log-analytics-agent) sowie in den [Versionshinweisen zum Log Analytics-Agent](https://github.com/microsoft/OMS-Agent-for-Linux/releases).
## <a name="november-2020"></a>November 2020

- [Überwachen Ihrer Logic Apps-Playbooks in Azure Sentinel](#monitor-your-logic-apps-playbooks-in-azure-sentinel)
- [Microsoft 365 Defender-Connector (Public Preview)](#microsoft-365-defender-connector-public-preview)
### <a name="monitor-your-logic-apps-playbooks-in-azure-sentinel"></a>Überwachen Ihrer Logic Apps-Playbooks in Azure Sentinel

Azure Sentinel ist nun in [Azure Logic Apps](/azure/logic-apps/) integriert. Dieser Clouddienst unterstützt Sie beim Planen, Automatisieren und Orchestrieren von Aufgaben, Geschäftsprozessen und Workflows.

Verwenden Sie eine Azure-Logik-App in Azure Sentinel als Playbook, das bei der Erstellung eines Incidents oder bei der Selektierung und Bearbeitung von Incidents automatisch aufgerufen werden kann. 

Wir haben eine [Azure-Arbeitsmappe](/azure/azure-monitor/platform/workbooks-overview) namens **Überwachung der Playbookintegrität** hinzugefügt, um Einblicke in die Integrität, Leistung und Nutzung Ihrer Playbooks zu geben. Dazu zählen auch Playbooks, die Sie mit Azure Logic Apps hinzufügen. 

Verwenden Sie die Arbeitsmappe **Überwachung der Playbookintegrität**, um die Integrität Ihrer Playbooks zu überwachen oder anhand der Menge der erfolgreichen oder nicht erfolgreichen Ausführungen nach Anomalien zu suchen. 

Die Arbeitsmappe **Überwachung der Playbookintegrität** ist ab sofort im Vorlagenkatalog von Azure Sentinel verfügbar:

:::image type="content" source="media/whats-new/playbook-monitoring-workbook.gif" alt-text="Arbeitsmappe zur Überwachung der Integrität von Beispielplaybooks":::

Weitere Informationen finden Sie unter

- [Logic Apps-Dokumentation](/azure/logic-apps/monitor-logic-apps-log-analytics#set-up-azure-monitor-logs)

- [Azure Monitor-Dokumentation](/azure/azure-monitor/platform/activity-log#send-to-log-analytics-workspace)

### <a name="microsoft-365-defender-connector-public-preview"></a>Microsoft 365 Defender-Connector (Public Preview)
 
Mithilfe des Microsoft 365 Defender-Connectors für Azure Sentinel können Sie erweiterte Hunting-Protokolle (eine Art von Ereignisrohdaten) aus Microsoft 365 Defender an Azure Sentinel streamen. 

Dank der Integration von [Microsoft Defender for Endpoint (MDATP)](/windows/security/threat-protection/) in den Schutzschirm von [Microsoft 365 Defender](/microsoft-365/security/mtp/microsoft-threat-protection) können Sie jetzt Ihre erweiterten Hunting-Ereignisse aus Microsoft Defender for Endpoint mithilfe des Microsoft 365 Defender-Connectors erfassen und sie direkt an neue, zu diesem Zweck erstellte Tabellen in Ihrem Azure Sentinel-Arbeitsbereich streamen. 

Da die Azure Sentinel-Tabellen auf dem gleichen Schema basieren, das auch im Microsoft 365 Defender-Portal verwendet wird, haben Sie uneingeschränkten Zugriff auf sämtliche erweiterte Hunting-Protokolle. 

Weitere Informationen finden Sie unter [Verknüpfen von Daten aus Microsoft 365 Defender mit Azure Sentinel](connect-microsoft-365-defender.md).

> [!NOTE]
> Microsoft 365 Defender wurde bisher als Microsoft Threat Protection oder MTP bezeichnet. Microsoft Defender for Endpoint wurde bisher als Microsoft Defender Advanced Threat Protection oder MDATP bezeichnet.
> 

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
>[Schnellstart: Ausführen des Onboardings für Azure Sentinel](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[Einblick in Warnungen](quickstart-get-visibility.md)
