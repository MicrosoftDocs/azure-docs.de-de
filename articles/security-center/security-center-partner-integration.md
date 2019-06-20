---
title: Integrieren von Sicherheitslösungen in Azure Security Center | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Partner in Azure Security Center integriert werden, um die allgemeine Sicherheit Ihrer Azure-Ressourcen zu verbessern.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/20/2019
ms.author: rkarlin
ms.openlocfilehash: cfe1137f0b0b155a06ebb5ce54bfd83859bdfa01
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64569811"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Integrieren von Sicherheitslösungen in Azure Security Center
Dieses Dokument unterstützt Sie bei der Verwaltung von bereits mit Azure Security Center verbundenen Sicherheitslösungen sowie beim Hinzufügen neuer Lösungen.

> [!NOTE]
> Eine Teilmenge der Sicherheitslösungen läuft am 31. Juli 2019 aus. Weitere Informationen und alternative Dienste finden Sie unter [Auslaufen von Security Center-Funktionen (Juli 2019)](security-center-features-retirement-july2019.md#menu_solutions).

## <a name="integrated-azure-security-solutions"></a>Integrierte Azure-Sicherheitslösungen
Mit Security Center können Sie ganz einfach integrierte Sicherheitslösungen in Azure aktivieren. Dies hat unter anderem folgende Vorteile:

- **Vereinfachte Bereitstellung:** Security Center bietet eine optimierte Bereitstellung integrierter Partnerlösungen. Für Lösungen wie Antischadsoftware und Sicherheitsrisikobewertungen kann Security Center den erforderlichen Agent auf Ihren virtuellen Computern bereitstellen, und für Firewallgeräte kann sich Security Center um den Großteil der erforderlichen Netzwerkkonfiguration kümmern.
- **Integrierte Erkennungen:** Sicherheitsereignisse von Partnerlösungen werden automatisch gesammelt, aggregiert und im Rahmen der Security Center-Warnungen und -Vorfälle angezeigt. Diese Ereignisse werden mit Erkennungen anderer Quellen zusammengeführt, um erweiterte Bedrohungserkennungsfunktionen bereitzustellen.
- **Einheitliche Integritätsüberwachung und -verwaltung:** Mithilfe integrierter Integritätsereignisse können Kunden alle Partnerlösungen auf einen Blick überwachen. Es ist eine grundlegende Verwaltung verfügbar – mit einfachem Zugriff auf die erweiterte Konfiguration über die Partnerlösung.

Integrierte Sicherheitslösungen umfassen derzeit Folgendes:

- Web Application Firewall ([Barracuda](https://www.barracuda.com/products/webapplicationfirewall), [F5](https://support.f5.com/kb/en-us/products/big-ip_asm/manuals/product/bigip-ve-web-application-firewall-microsoft-azure-12-0-0.html), [Imperva](https://www.imperva.com/Products/WebApplicationFirewall-WAF), [Fortinet](https://www.fortinet.com/products.html) und [Azure Application Gateway](https://azure.microsoft.com/blog/azure-web-application-firewall-waf-generally-available/))
- Firewall der nächsten Generation ([Check Point](https://www.checkpoint.com/products/vsec-microsoft-azure/), [Barracuda](https://campus.barracuda.com/product/nextgenfirewallf/article/NGF/AzureDeployment/), [Fortinet](https://docs.fortinet.com/d/fortigate-fortios-handbook-the-complete-guide-to-fortios-5.2), [Cisco](https://www.cisco.com/c/en/us/td/docs/security/firepower/quick_start/azure/ftdv-azure-qsg.html) und [Palo Alto Networks](https://www.paloaltonetworks.com/products))
- Sicherheitsrisikobewertung ([Qualys](https://www.qualys.com/public-clouds/microsoft-azure/) und [Rapid7](https://www.rapid7.com/products/insightvm/))

> [!NOTE]
> Security Center installiert Microsoft Monitoring Agent nicht auf virtuellen Partnergeräten, da die meisten Sicherheitsanbieter die Ausführung externer Agents auf ihrem Gerät verbieten.
>
>

## <a name="how-security-solutions-are-integrated"></a>Informationen zur Integration von Sicherheitslösungen
Über Security Center bereitgestellte Azure-Sicherheitslösungen werden automatisch verbunden. Sie können auch eine Verbindung mit anderen Sicherheitsdatenquellen herstellen. Hierzu zählen beispielsweise folgende:

- Azure AD Identity Protection
- Lokal oder in anderen Clouds ausgeführte Computer
- Sicherheitslösung mit CEF-Unterstützung (Common Event Format)
- Microsoft Advanced Threat Analytics

![Integration von Partnerlösungen](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Verwalten integrierter Azure-Sicherheitslösungen und anderer Datenquellen

1. Melden Sie sich beim [Azure-Portal](https://azure.microsoft.com/features/azure-portal/) an.

2. Wählen Sie im **Microsoft Azure-Menü** die Option **Security Center**. **Security Center – Übersicht** wird geöffnet.

3. Klicken Sie im Menü von Security Center auf **Sicherheitslösungen**.

   ![Übersicht über Security Center](./media/security-center-partner-integration/overview.png)

Unter **Sicherheitslösungen** können Sie Informationen zur Integrität integrierter Azure-Sicherheitslösungen anzeigen und grundlegende Verwaltungsaufgaben durchführen. Sie können auch eine Verbindung mit anderen Arten von Sicherheitsdatenquellen herstellen – beispielsweise mit Azure Active Directory Identity Protection-Warnungen und Firewallprotokollen im CEF-Format (Common Event Format).

### <a name="connected-solutions"></a>Verbundene Lösungen

Der Abschnitt **Verbunde Lösungen** enthält Sicherheitslösungen, die derzeit mit Security Center verbunden sind, sowie Informationen zum Integritätsstatus der einzelnen Lösungen.  

![Verbundene Lösungen](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

Eine Partnerlösung kann den folgenden Status aufweisen:

* Fehlerfrei (grün): Es liegt kein Integritätsproblem vor.
* Fehlerhaft (rot): Es liegt ein Integritätsproblem vor, das sofort untersucht werden muss.
* Integritätsprobleme (orange): Die Lösung hat das Melden der Integrität beendet.
* Nicht gemeldet (grau): Die Lösung hat noch nichts berichtet. Der Status einer Lösung kann unter Umständen noch nicht gemeldet worden sein, wenn die Verbindung kürzlich hergestellt wurde und die Bereitstellung noch nicht abgeschlossen ist, oder es sind keine Integritätsdaten verfügbar.

> [!NOTE]
> Wenn keine Integritätsstatusdaten verfügbar sind, zeigt Security Center das Datum und die Uhrzeit des letzten empfangenen Ereignisses an, um anzugeben, ob die Lösung Berichte übermittelt. Wenn keine Integritätsdaten verfügbar sind und innerhalb der letzten 14 Tage keine Warnungen empfangen wurden, gibt Security Center an, dass die Lösung fehlerhaft ist oder nicht berichtet.
>
>

1. Wählen Sie **ANSICHT**, um zusätzliche Informationen und Optionen anzuzeigen, darunter:

   - **Lösungskonsole**. Öffnet die Verwaltungsoberfläche für diese Lösung.
   - **Virtuellen Computer verknüpfen**. Öffnet das Blatt „Anwendungen verknüpfen“. Hier können Sie Ressourcen mit der Partnerlösung verknüpfen.
   - **Lösung löschen**.
   - **Konfigurieren**.

   ![Details der Partnerlösung](./media/security-center-partner-solutions/partner-solutions-detail.png)

### <a name="discovered-solutions"></a>Ermittelte Lösungen

Security Center erkennt Sicherheitslösungen automatisch, die in Azure ausgeführt werden, aber nicht mit Security Center verbunden sind, und zeigt die Lösungen im Abschnitt **Ermittelte Lösungen** an. Dazu gehören sowohl Azure-Lösungen wie [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) als auch Partnerlösungen.

> [!NOTE]
> Für das ermittelte Lösungsfeature ist der Standardtarif von Security Center ist auf Abonnementebene erforderlich. Weitere Informationen zu den Tarifen von Security finden Sie unter [Preise](security-center-pricing.md).
>
>

Wählen Sie unterhalb einer Lösung die Option **VERBINDEN**, um die Integration in Security Center durchzuführen und über Sicherheitswarnungen benachrichtigt zu werden.

![Ermittelte Lösungen](./media/security-center-partner-integration/security-center-partner-integration-fig5.png)

Security Center erkennt auch im Abonnement bereitgestellte Lösungen, die die Weiterleitung von CEF-Protokollen (Common Event Format) ermöglichen. Informieren Sie sich über das [Verbinden einer Sicherheitslösung](quick-security-solutions.md) mit Security Center, für die CEF-Protokolle verwendet werden.

### <a name="add-data-sources"></a>Hinzufügen von Datenquellen

Der Abschnitt **Datenquellen hinzufügen** enthält weitere verfügbare Datenquellen, mit denen eine Verbindung hergestellt werden kann. Klicken Sie **HINZUFÜGEN**, um eine Anleitung zum Hinzufügen von Daten aus einer dieser Quellen anzuzeigen.

![Datenquellen](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)

### <a name="connect-external-solutions"></a>Herstellen einer Verbindung mit externen Lösungen

Zusätzlich zum Sammeln von Sicherheitsdaten von Ihren Computern können Sie Sicherheitsdaten von vielen anderen Sicherheitslösungen integrieren, z.B. für Lösungen mit Unterstützung des Common Event Format (CEF). CEF ist ein branchenübliches Standardformat, das in Verbindung mit Syslog-Nachrichten von vielen Sicherheitsanbietern verwendet wird, um die Ereignisintegration für unterschiedliche Plattformen zu ermöglichen.

In dieser Schnellstartanleitung wird Folgendes beschrieben:
- Verbinden einer Sicherheitslösung mit Security Center über CEF-Protokolle
- Überprüfen der Verbindung mit der Sicherheitslösung

#### <a name="prerequisites"></a>Voraussetzungen
Für den Einstieg in Security Center benötigen Sie ein Microsoft Azure-Abonnement. Wenn Sie nicht über ein Abonnement verfügen, können Sie sich für ein [kostenloses Testkonto](https://azure.microsoft.com/free/) registrieren.

Zum Durcharbeiten dieser Schnellstartanleitung müssen Sie den Tarif „Standard“ von Security Center verwenden. Sie können Security Center Standard kostenlos testen. Unter [Schnellstarthandbuch zu Azure Security Center](security-center-get-started.md) wird Schritt für Schritt beschrieben, wie Sie das Upgrade auf den Tarif „Standard“ durchführen. Weitere Informationen finden Sie auf der [Preisseite](https://azure.microsoft.com/pricing/details/security-center/).

Außerdem benötigen Sie einen [Linux-Computer](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-linux) mit Syslog-Dienst, der bereits mit Ihrer Security Center-Instanz verbunden ist.

#### <a name="connect-solution-using-cef"></a>Herstellen einer Verbindung für die Lösung per CEF

1. Melden Sie sich beim [Azure-Portal](https://azure.microsoft.com/features/azure-portal/) an.
2. Wählen Sie im Menü **Microsoft Azure** die Option **Security Center**. **Security Center – Übersicht** wird geöffnet.

    ![Auswählen von Security Center](./media/quick-security-solutions/quick-security-solutions-fig1.png)  

3. Klicken Sie im Hauptmenü von Security Center auf **Sicherheitslösungen**.
4. Klicken Sie auf der Seite „Sicherheitslösungen“ im Bereich **Datenquellen hinzufügen (3)** unter **Common Event Format** auf **Hinzufügen**.

    ![Hinzufügen einer Datenquelle](./media/quick-security-solutions/quick-security-solutions-fig2.png)

5. Erweitern Sie auf der Seite „Common Event Format-Protokolle“ den zweiten Schritt **Syslog-Weiterleitung für das Senden der erforderlichen Protokolle an den Agent über UDP-Port 25226 konfigurieren**, und befolgen Sie die unten angegebene Anleitung auf Ihrem Linux-Computer:

    ![Konfigurieren von Syslog](./media/quick-security-solutions/quick-security-solutions-fig3.png)

6. Erweitern Sie den dritten Schritt **Agent-Konfigurationsdatei auf dem Agent-Computer platzieren**, und befolgen Sie die unten angegebene Anleitung auf Ihrem Linux-Computer:

    ![Agent-Konfiguration](./media/quick-security-solutions/quick-security-solutions-fig4.png)

7. Erweitern Sie den vierten Schritt **Syslog-Daemon und Agent neu starten**, und befolgen Sie die unten angegebene Anleitung auf Ihrem Linux-Computer:

    ![Neustarten von Syslog](./media/quick-security-solutions/quick-security-solutions-fig5.png)


#### <a name="validate-the-connection"></a>Überprüfen der Verbindung

Bevor Sie mit den unten angegebenen Schritten fortfahren, müssen Sie warten, bis das Syslog mit dem Melden an Security Center beginnt. Dies kann einige Zeit dauern und variiert je nach Größe der Umgebung.

1.  Klicken Sie im linken Bereich des Security Center-Dashboards auf **Suchen**.
2.  Wählen Sie den Arbeitsbereich aus, mit dem das Syslog (Linux-Computer) verbunden ist.
3.  Geben Sie *CommonSecurityLog* ein, und klicken Sie auf die Schaltfläche **Suchen**.

Im folgenden Beispiel werden die Ergebnisse dieser Schritte angezeigt: ![CommonSecurityLog](./media/quick-security-solutions/common-sec-log.png)

#### <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Andere Schnellstartanleitungen und Tutorials in dieser Sammlung bauen auf dieser Schnellstartanleitung auf. Wenn Sie planen, mit den nachfolgenden Schnellstartanleitungen und Tutorials fortzufahren, sollten Sie weiter den Tarif „Standard“ nutzen und die automatische Bereitstellung aktiviert lassen. Gehen Sie wie folgt vor, falls Sie nicht fortfahren oder zum Free-Tarif zurückkehren möchten:

1. Kehren Sie zum Hauptmenü von Security Center zurück, und wählen Sie die Option **Sicherheitsrichtlinie**.
2. Wählen Sie das Abonnement oder die Richtlinie aus, für das bzw. die Sie zu „Free“ zurückwechseln möchten. Der Bereich **Sicherheitsrichtlinie** wird geöffnet.
3. Wählen Sie unter **RICHTLINIENKOMPONENTEN** die Option **Tarif**.
4. Wählen Sie **Free**, um für das Abonnement vom Tarif „Standard“ zu „Free“ zu wechseln.
5. Wählen Sie **Speichern** aus.

Gehen Sie wie folgt vor, um die automatische Bereitstellung zu deaktivieren:

1. Kehren Sie zum Hauptmenü von Security Center zurück, und wählen Sie die Option **Sicherheitsrichtlinie**.
2. Wählen Sie das Abonnement aus, für das Sie die automatische Bereitstellung deaktivieren möchten.
3. Wählen Sie im Bereich **Sicherheitsrichtlinie – Datensammlung** unter **Onboarding** die Option **Aus**, um die automatische Bereitstellung zu deaktivieren.
4. Wählen Sie **Speichern** aus.

>[!NOTE]
> Wenn Sie die automatische Bereitstellung deaktivieren, wird Microsoft Monitoring Agent nicht von virtuellen Azure-Computern entfernt, auf denen der Agent bereitgestellt wurde. Wenn Sie die automatische Bereitstellung deaktivieren, schränkt dies die Sicherheitsüberwachung für Ihre Ressourcen ein.
>

## <a name="exporting-data-to-a-siem"></a>Exportieren von Daten in ein SIEM-Tool

Von Azure Security Center erzeugte verarbeitete Ereignisse werden im [Aktivitätsprotokoll](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) von Azure veröffentlicht, einem der über Azure Monitor verfügbaren Protokolltypen. Azure Monitor bietet eine konsolidierte Pipeline zum Routing beliebiger Überwachungsdaten zu einem SIEM-Tool. Dies erfolgt durch Streaming der Daten an einen Event Hub, von wo aus sie dann in ein Partnertool hereingezogen werden können.

Diese Pipe verwendet die [einzelne Azure-Überwachungspipeline](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) für den Zugriff auf die Überwachungsdaten aus Ihrer Azure-Umgebung. So können Sie mühelos SIEMs und Überwachungstools zum Verarbeiten der Daten einrichten.

In den nächsten Abschnitten wird beschrieben, wie Sie Daten zum Streamen an einen Event Hub konfigurieren können. Die Schritte setzen voraus, dass Sie bereits Azure Security Center in Ihrem Azure-Abonnement konfiguriert haben.

Allgemeine Übersicht

![Allgemeine Übersicht](media/security-center-export-data-to-siem/overview.png)

### <a name="what-is-the-azure-security-data-exposed-to-siem"></a>Was sind die für SIEM verfügbar gemachten Azure-Sicherheitsdaten?

In dieser Version stellen wir die [Sicherheitswarnungen](../security-center/security-center-managing-and-responding-alerts.md) zur Verfügung. In zukünftigen Versionen wird das Dataset mit Empfehlungen zur Sicherheit erweitert.

### <a name="how-to-setup-the-pipeline"></a>Einrichten der Pipeline

#### <a name="create-an-event-hub"></a>Erstellen eines Ereignis-Hubs

Bevor Sie beginnen, müssen Sie [einen Event Hubs-Namespace erstellen](../event-hubs/event-hubs-create.md). Dieser Namespace und Event Hub sind das Ziel für sämtliche Ihrer Überwachungsdaten.

#### <a name="stream-the-azure-activity-log-to-event-hubs"></a>Streamen des Azure-Aktivitätsprotokolls an Event Hubs

Bitte lesen Sie den folgenden Artikel: [Streamen des Azure-Aktivitätsprotokolls an Event Hubs](../azure-monitor/platform/activity-logs-stream-event-hubs.md).

#### <a name="install-a-partner-siem-connector"></a>Installieren eines Partner-SIEM-Connectors 

Die Weiterleitung Ihrer Überwachungsdaten an einen Event Hub mit Azure Monitor ermöglicht eine einfache Integration in SIEM- und Überwachungstools von Partnern.

Unter dem folgenden Link finden Sie die Liste [unterstützter SIEMs](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#what-can-i-do-with-the-monitoring-data-being-sent-to-my-event-hub).

### <a name="example-for-querying-data"></a>Beispiel zum Abfragen von Daten 

Hier sind einige Splunk-Abfragen, mit denen Sie Warnungsdaten erhalten können:

| **Beschreibung der Abfrage** | **Abfrage** |
|----|----|
| Alle Warnungen| index=main Microsoft.Security/locations/alerts|
| Zusammenfassen der Anzahl der Vorgänge nach deren Namen| index=main sourcetype="amal:security" \| table operationName \| stats count by operationName|
| Abrufen von Informationen zu Warnungen: Zeit, Name, Status, ID und Abonnement | index=main Microsoft.Security/locations/alerts \| table \_time, properties.eventName, State, properties.operationId, am_subscriptionId |


## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie Partnerlösungen in Security Center integrieren. Weitere Informationen zu Security Center finden Sie in den folgenden Artikeln:

* [Überwachen der Sicherheitsintegrität in Azure Security Center](security-center-monitoring.md): Hier erfahren Sie, wie Sie die Integrität Ihrer Azure-Ressourcen überwachen.
* [Überwachen von Partnerlösungen mit Azure Security Center](security-center-partner-solutions.md): Hier erfahren Sie, wie Sie den Integritätsstatus Ihrer Partnerlösungen überwachen.
* [Azure Security Center – häufig gestellte Fragen](security-center-faq.md): Hier finden Sie häufig gestellte Fragen zur Verwendung von Security Center.
* [Azure Security-Blog](https://blogs.msdn.com/b/azuresecurity/): Hier finden Sie Blogbeiträge zur Sicherheit und Compliance von Azure.
