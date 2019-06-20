---
title: Überwachen von B2B-Nachrichten mit Azure Monitor-Protokollen – Azure Logic Apps | Microsoft-Dokumentation
description: Überwachen Sie AS2-, X12- und EDIFACT-Nachrichten für Integrationskonten und Azure Logic Apps, und richten Sie Diagnoseprotokollierung mit Azure Monitor-Protokollen ein.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/23/2018
ms.openlocfilehash: 12799a308157c3c0e19de1f82c0fe3df44fad37e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62106299"
---
# <a name="monitor-b2b-messages-with-azure-monitor-logs-in-azure-logic-apps"></a>Überwachen von B2B-Nachrichten mit Azure Monitor-Protokollen in Azure Logic Apps

Nachdem Sie die B2B-Kommunikation zwischen Handelspartnern in Ihrem Integrationskonto eingerichtet haben, können diese Partner untereinander Nachrichten austauschen. Um zu überprüfen, ob die Kommunikation erwartungsgemäß funktioniert, können Sie über [Azure Monitor-Protokolle](../log-analytics/log-analytics-overview.md) AS2-, X12- und EDIFACT-Nachrichten überwachen und die Diagnoseprotokollierung für Ihr Integrationskonto einrichten. Dieser Dienst überwacht Ihre Cloud- und Ihre lokale Umgebung, unterstützt Sie dabei, deren Verfügbarkeit und Leistung zu gewährleisten, und erfasst Laufzeitdetails und Ereignisse für ein ausführlicheres Debugging. Sie können diese Daten auch mit anderen Diensten verwenden, z.B. Azure Storage und Azure Event Hubs.

> [!NOTE]
> Diese Seite enthält möglicherweise Verweise auf Microsoft Operations Management Suite (OMS), die [im Januar 2019 eingestellt wird](../azure-monitor/platform/oms-portal-transition.md). Soweit möglich, werden diese Schritte aber durch Azure Log Analytics ersetzt. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Voraussetzungen

* Eine Logik-App, für die die Diagnoseprotokollierung eingerichtet ist. Informieren Sie sich über das [Erstellen einer Logik-App](quickstart-create-first-logic-app-workflow.md) und das [Einrichten der Protokollierung für diese Logik-App](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Wenn Sie die obigen Anforderungen erfüllen, benötigen Sie auch einen Log Analytics-Arbeitsbereich, in dem Sie die Überwachung und Nachverfolgung von B2B-Kommunikation über Azure Monitor-Protokolle nutzen. Falls Sie keinen Log Analytics-Arbeitsbereich besitzen, lesen Sie die Informationen zum [Erstellen eines Log Analytics-Arbeitsbereichs](../azure-monitor/learn/quick-create-workspace.md).

* Ein mit Ihrer Logik-App verknüpftes Integrationskonto. Informationen zum Erstellen eines Integrationskontos mit Logik-App-Verknüpfung finden Sie [hier](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).

## <a name="turn-on-diagnostics-logging"></a>Aktivieren der Diagnoseprotokollierung

Die Protokollierung kann entweder direkt über Ihr Integrationskonto oder [über den Azure Monitor-Dienst](#azure-monitor-service) aktiviert werden. Azure Monitor bietet eine grundlegende Überwachung mit Daten der Infrastrukturebene. Weitere Informationen zu Azure Monitor finden Sie [hier](../azure-monitor/overview.md).

### <a name="turn-on-logging-from-integration-account"></a>Aktivieren der Protokollierung vom Integrationskonto

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach Ihrem Integrationskonto, und wählen Sie es aus. Wählen Sie unter **Überwachung** die Option **Diagnoseeinstellungen** aus.

   ![Suchen und Auswählen des eigenen Integrationskontos, Auswählen von „Diagnoseeinstellungen“](media/logic-apps-monitor-b2b-message/find-integration-account.png)

1. Suchen Sie nun Ihr Integrationskonto, und wählen Sie es aus. Wählen Sie in Filterlisten die Werte für Ihr Integrationskonto aus.
Wählen Sie nach Abschluss **Add diagnostic setting** (Diagnoseeinstellung hinzufügen) aus.

   | Eigenschaft | Wert | BESCHREIBUNG | 
   |----------|-------|-------------|
   | **Abonnement** | <*Name des Azure-Abonnements*> | Das mit Ihrem Integrationskonto verknüpfte Azure-Abonnement | 
   | **Ressourcengruppe** | <*Name der Azure-Ressourcengruppe*> | Die Azure-Ressourcengruppe für Ihr Integrationskonto | 
   | **Ressourcentyp** | **Integrationskonten** | Der Typ der Azure-Ressource, in der Sie die Protokollierung aktivieren möchten | 
   | **Ressource** | <*integration-account-name*> | Der Name Ihrer Azure-Ressource, in der Sie die Protokollierung aktivieren möchten | 
   ||||  

   Beispiel:

   ![Einrichten der Diagnose für Ihr Integrationskonto](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

1. Geben Sie einen Namen für die neue Diagnoseeinstellung an, und wählen Sie Ihren Log Analytics-Arbeitsbereich und die zu protokollierenden Daten aus.

   1. Wählen Sie **An Log Analytics senden** aus. 

   1. Wählen Sie unter **Log Analytics** die Option **Konfigurieren** aus. 

   1. Wählen Sie unter **OMS-Arbeitsbereiche** den Log Analytics-Arbeitsbereich aus, den Sie für die Protokollierung verwenden möchten. 

      > [!NOTE]
      > OMS-Arbeitsbereiche werden derzeit durch Log Analytics-Arbeitsbereiche ersetzt. 

   1. Wählen Sie unter **Protokoll** die Kategorie **IntegrationAccountTrackingEvents** aus, und wählen Sie dann **Speichern** aus.

   Beispiel: 

   ![Einrichten von Azure Monitor-Protokollen zum Senden von Diagnosedaten an ein Protokoll](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

1. Nun können Sie das [Nachverfolgen von B2B-Nachrichten in Azure Monitor Protokollen](../logic-apps/logic-apps-track-b2b-messages-omsportal.md) einrichten.

<a name="azure-monitor-service"></a>

### <a name="turn-on-logging-through-azure-monitor"></a>Aktivieren der Protokollierung über Azure Monitor

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) im Azure-Hauptmenü **Überwachen** aus. Wählen Sie unter **Einstellungen** die Option **Diagnoseeinstellungen** aus. 

   ![Wählen Sie „Überwachen“ > „Diagnoseeinstellungen“ > Ihr Integrationskonto aus.](media/logic-apps-monitor-b2b-message/monitor-diagnostics-settings.png)

1. Suchen Sie nun Ihr Integrationskonto, und wählen Sie es aus. Wählen Sie in Filterlisten die Werte für Ihr Integrationskonto aus.
Wählen Sie nach Abschluss **Add diagnostic setting** (Diagnoseeinstellung hinzufügen) aus.

   | Eigenschaft | Wert | BESCHREIBUNG | 
   |----------|-------|-------------|
   | **Abonnement** | <*Name des Azure-Abonnements*> | Das mit Ihrem Integrationskonto verknüpfte Azure-Abonnement | 
   | **Ressourcengruppe** | <*Name der Azure-Ressourcengruppe*> | Die Azure-Ressourcengruppe für Ihr Integrationskonto | 
   | **Ressourcentyp** | **Integrationskonten** | Der Typ der Azure-Ressource, in der Sie die Protokollierung aktivieren möchten | 
   | **Ressource** | <*integration-account-name*> | Der Name Ihrer Azure-Ressource, in der Sie die Protokollierung aktivieren möchten | 
   ||||  

   Beispiel:

   ![Einrichten der Diagnose für Ihr Integrationskonto](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

1. Geben Sie einen Namen für die neue Diagnoseeinstellung an, und wählen Sie Ihren Log Analytics-Arbeitsbereich und die zu protokollierenden Daten aus.

   1. Wählen Sie **An Log Analytics senden** aus. 

   1. Wählen Sie unter **Log Analytics** die Option **Konfigurieren** aus. 

   1. Wählen Sie unter **OMS-Arbeitsbereiche** den Log Analytics-Arbeitsbereich aus, den Sie für die Protokollierung verwenden möchten. 

      > [!NOTE]
      > OMS-Arbeitsbereiche werden derzeit durch Log Analytics-Arbeitsbereiche ersetzt. 

   1. Wählen Sie unter **Protokoll** die Kategorie **IntegrationAccountTrackingEvents** aus, und wählen Sie dann **Speichern** aus.

   Beispiel: 

   ![Einrichten von Azure Monitor-Protokollen zum Senden von Diagnosedaten an ein Protokoll](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

1. Nun können Sie das [Nachverfolgen von B2B-Nachrichten in Azure Monitor Protokollen](../logic-apps/logic-apps-track-b2b-messages-omsportal.md) einrichten.

## <a name="use-diagnostic-data-with-other-services"></a>Verwenden von Diagnosedaten mit anderen Diensten

In Verbindung mit Azure Monitor-Protokollen können Sie die Nutzung der Diagnosedaten Ihrer Logik-App um andere Azure-Dienste erweitern, z.B.: 

* [Archivieren von Azure-Diagnoseprotokollen in Azure Storage](../azure-monitor/platform/archive-diagnostic-logs.md)
* [Streamen von Azure-Diagnoseprotokollen an Azure Event Hubs](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md) 

Durch die Verwendung von Telemetriedaten und Analysen anderer Dienste (beispielsweise [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) und [Power BI](../azure-monitor/platform/powerbi.md)) können Sie dann eine Überwachung in Echtzeit implementieren. Beispiel:

* [Streamen von Daten von Event Hubs zu Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Analysieren von Streamingdaten mit Stream Analytics und Erstellen eines Dashboards für die Echtzeitanalyse in Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Stellen Sie basierend auf den einzurichtenden Optionen sicher, dass Sie zuerst [ein Azure Storage-Konto](../storage/common/storage-create-storage-account.md) oder [einen Azure Event Hub](../event-hubs/event-hubs-create.md) erstellen. Sie können dann die Ziele auswählen, an die Sie Diagnosedaten senden möchten.
Werte für die Beibehaltungsdauer gelten nur, wenn Sie sich für die Verwendung eines Speicherkontos entscheiden.

![Senden von Daten an das Azure-Speicherkonto oder einen Event Hub](./media/logic-apps-monitor-b2b-message/diagnostics-storage-event-hub-log-analytics.png)

## <a name="supported-tracking-schemas"></a>Unterstützte Nachverfolgungsschemas

Azure unterstützt die folgenden Nachverfolgungsschematypen. Mit Ausnahme des Typs „Benutzerdefiniert“ besitzen alle ein festes Schema.

* [AS2-Nachverfolgungsschema](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12-Nachverfolgungsschema](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Benutzerdefinierte Nachverfolgungsschemas](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Nächste Schritte

* [Nachverfolgen von B2B-Nachrichten in Azure Monitor-Protokollen](../logic-apps/logic-apps-track-b2b-messages-omsportal.md "Nachverfolgen von B2B-Nachrichten in Azure Monitor-Protokollen")
* [Weitere Informationen zum Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Informationen zum Enterprise Integration Pack")

