---
title: Integrieren und Verwalten von Sicherheitsvorgängen und Microsoft Graph-Sicherheit
description: Verbessern von Bedrohungsschutz, -erkennung und -reaktion mit Microsoft Graph-Sicherheit und Azure Logic Apps
services: logic-apps
ms.suite: integration
author: ecfan
ms.author: preetikr
ms.reviewer: v-ching, estfan, logicappspm
ms.topic: article
ms.date: 02/21/2020
tags: connectors
ms.openlocfilehash: a83cd68df2f1d722517d6239bf6959075860d0b8
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94888537"
---
# <a name="improve-threat-protection-by-integrating-security-operations-with-microsoft-graph-security--azure-logic-apps"></a>Verbessern des Bedrohungsschutzes durch Integrieren von Sicherheitsvorgängen mit der Sicherheits-API von Microsoft Graph und Azure Logic Apps

Mit [Azure Logic Apps](../logic-apps/logic-apps-overview.md) und dem [Sicherheits-API-Connector von Microsoft Graph](/graph/security-concept-overview) können Sie die Art und Weise verbessern, in der Ihre App Bedrohungen erkennt, sich schützt und reagiert, indem Sie automatisierte Workflows für die Integration von Microsoft-Sicherheitsprodukten, Diensten und Partnern erstellen. Sie können z.B. [Azure Security Center-Playbooks](../security-center/workflow-automation.md) erstellen, die Entitäten der Sicherheits-API von Microsoft Graph, z.B. Warnungen, überwachen und verwalten. Unter anderem werden folgende Szenarien vom Sicherheits-API-Connector von Microsoft Graph unterstützt:

* Abrufen von Warnungen basierend auf Abfragen oder nach Warnungs-ID. Sie können z.B. eine Liste abrufen, die Warnungen mit hohem Schweregrad enthält.

* Aktualisieren von Warnungen. Beispielsweise können Sie Warnungszuweisungen aktualisieren, Kommentare zu Warnungen hinzufügen oder Warnungen markieren.

* Überwachen, wenn Warnungen erstellt oder geändert werden, durch Erstellen von [Warnungsabonnements (Webhooks)](/graph/api/resources/webhooks).

* Verwalten Ihrer Warnungsabonnements. Sie können z.B. aktive Abonnements abrufen, die Ablaufzeit für ein Abonnement erweitern oder Abonnements löschen.

Der Workflow Ihrer Logik-App kann Aktionen verwenden, die Antworten vom Sicherheits-API-Connector von Microsoft Graph erhalten, und diese Ausgabe weiteren Aktionen in Ihrem Workflow zur Verfügung stellen. Sie können die Ausgabe der Aktionen des Sicherheits-API-Connectors von Microsoft Graph auch von anderen Aktionen in Ihrem Workflow verwenden lassen. Wenn Sie z.B. Warnungen mit hohem Schweregrad über den Sicherheits-API-Connector von Microsoft Graph erhalten haben, können Sie diese Warnungen in einer E-Mail-Nachricht mithilfe des Outlook-Connectors senden. 

Weitere Informationen zu Microsoft Graph-Sicherheit finden Sie in der [Übersicht über die Sicherheits-API von Microsoft Graph](/graph/security-concept-overview). Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen unter [Was ist Azure Logic Apps?](../logic-apps/logic-apps-overview.md). Informationen zu Power Automate oder Power Apps finden Sie bei Bedarf in der [Übersicht über Power Automate](https://flow.microsoft.com/) bzw. in der [Übersicht über Power Apps](https://powerapps.microsoft.com/).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/). 

* Um den Sicherheits-API-Connector von Microsoft Graph verwenden zu können, benötigen Sie die *explizit gegebene* Zustimmung des Mandantenadministrators für Azure Active Directory (AD), die Teil der [Authentifizierungsanforderungen der Sicherheits-API in Microsoft Graph](/graph/security-authorization) ist. Diese Zustimmung erfordert die Anwendungs-ID des Sicherheits-API-Connectors von Microsoft Graph und den Namen, die Sie auch im [Azure-Portal](https://portal.azure.com) finden:

  | Eigenschaft | Wert |
  |----------|-------|
  | **Anwendungsname** | `MicrosoftGraphSecurityConnector` |
  | **Anwendungs-ID** | `c4829704-0edc-4c3d-a347-7c4a67586f3c` |
  |||

  Um die Zustimmung für den Connector zu gewähren, kann Ihr Mandantenadministrator für Azure AD einen dieser Schritte ausführen:

  * [Erteilen der Zustimmung des Mandantenadministrators für Azure AD-Anwendungen](../active-directory/develop/v2-permissions-and-consent.md).

  * Während der ersten Ausführung Ihrer Logik-App kann Ihre App die Zustimmung von Ihrem Azure AD-Mandantenadministrator über die [Einwilligungserfahrung für Anwendungen](../active-directory/develop/application-consent-experience.md) anfordern.
   
* Grundlegende Kenntnisse über die [Erstellung von Logik-Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Die Logik-App, mit der Sie auf die Entitäten Ihrer Sicherheits-API von Microsoft Graph wie z.B. Warnungen zugreifen möchten. Um einen Microsoft Graph-Sicherheitstrigger zu verwenden, benötigen Sie eine leere Logik-App. Um eine Microsoft Graph-Sicherheitsaktion zu verwenden, benötigen Sie eine Logik-App, die mit dem entsprechenden Trigger für Ihr Szenario beginnt.

## <a name="connect-to-microsoft-graph-security"></a>Herstellen einer Verbindung mit der Sicherheits-API von Microsoft Graph 

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, und öffnen Sie Ihre Logik-App im Logik-App-Designer, sofern sie nicht bereits geöffnet ist.

1. Fügen Sie für leere Logik-Apps die Trigger und alle weiteren gewünschten Aktionen hinzu, bevor Sie eine Aktion der Sicherheits-API von Microsoft Graph hinzufügen.

   Oder

   Wählen Sie für vorhandene Logik-Apps im letzten Schritt zum Hinzufügen einer Aktion der Sicherheits-API von Microsoft Graph die Option **Neuer Schritt** aus.

   Oder

   Wenn Sie zwischen Schritten eine Aktion einfügen möchten, bewegen Sie den Mauszeiger über den Pfeil zwischen den Schritten. Wählen Sie das daraufhin angezeigte Pluszeichen (+) und dann **Aktion hinzufügen** aus.

1. Geben Sie in das Suchfeld „microsoft graph security“ als Filter ein. Wählen Sie in der Liste mit den Aktionen die gewünschte Aktion aus.

1. Melden Sie sich mit Ihren Anmeldeinformationen für die Sicherheits-API von Microsoft Graph.

1. Geben Sie die erforderlichen Informationen für die ausgewählte Aktion ein, und fahren Sie mit dem Erstellen Ihres Logik-App-Workflows fort.

## <a name="add-triggers"></a>Trigger hinzufügen

In Azure Logic Apps muss jede Logik-App mit einem [Trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) beginnen, der ausgelöst wird, wenn ein bestimmtes Ereignis eintritt oder eine bestimmte Bedingung erfüllt wird. Bei jeder Auslösung des Triggers erstellt das Logic Apps-Modul eine Logik-App-Instanz und startet die Ausführung des Workflows Ihrer App.

> [!NOTE] 
> Beim Auslösen eines Triggers verarbeitet der Trigger alle neuen Warnungen. Werden keine Warnungen empfangen, wird die Triggerausführung übersprungen. Der nächste Triggerabruf erfolgt basierend auf dem in den Triggereigenschaften angegebenen Wiederholungsintervall.

Dieses Beispiel zeigt, wie Sie einen Logik-App-Workflow starten können, wenn neue Warnungen an Ihre App gesendet werden.

1.  Erstellen Sie im Azure-Portal oder in Visual Studio eine leere Logik-App, die den Logik-App-Designer öffnet. In diesem Beispiel wird das Azure-Portal verwendet.

1.  Geben Sie im Designer in das Suchfeld „microsoft graph security“ als Filter ein. Wählen Sie in der Triggerliste den folgenden Trigger aus: **Bei allen neuen Warnungen**

1.  Stellen Sie im Trigger Informationen zu den Warnungen bereit, die Sie überwachen möchten. Weitere Eigenschaften stehen zur Verfügung, wenn Sie die Liste **Neuen Parameter hinzufügen** öffnen und einen Parameter auswählen, um diese Eigenschaft dem Trigger hinzuzufügen.

   | Eigenschaft | Eigenschaft (JSON) | Erforderlich | type | BESCHREIBUNG |
   |----------|-----------------|----------|------|-------------|
   | **Intervall** | `interval` | Ja | Integer | Eine positive ganze Zahl, die beschreibt, wie oft der Workflow basierend auf der Häufigkeit ausgeführt wird. Zulässige Mindest- und Maximalintervalle: <p><p>– Monat: 1–16 Monate <br>– Tag: 1–500 Tage <br>– Stunde: 1–12.000 Stunden <br>– Minute: 1–72.000 Minuten <br>- Sekunde: 1–9.999.999 Sekunden <p>Wenn das Intervall also beispielsweise auf „6“ und die Häufigkeit auf „Month“ festgelegt ist, erfolgt die Wiederholung alle sechs Monate. |
   | **Frequency** | `frequency` | Ja | String | Die Zeiteinheit für die Wiederholung: **Sekunde**, **Minute**, **Stunde**, **Tag**, **Woche** oder **Monat** |
   | **Zeitzone** | `timeZone` | Nein | String | Nur relevant, wenn Sie eine Startzeit angeben, da dieser Trigger keine [UTC-Abweichung](https://en.wikipedia.org/wiki/UTC_offset) akzeptiert. Wählen Sie die anzuwendende Zeitzone aus. |
   | **Startzeit** | `startTime` | Nein | String | Geben Sie Startdatum und -uhrzeit im folgenden Format an: <p><p>JJJJ-MM-TTThh:mm:ss (bei Auswahl einer Zeitzone) <p>Oder <p>JJJJ-MM-TTThh:mm:ssZ (wenn keine Zeitzone ausgewählt wird) <p>Für den 18. September 2017 um 14:00 Uhr würden Sie also „2017-09-18T14:00:00“ angeben und eine Zeitzone (z. B. „Pacific Standard Time“) auswählen. Alternativ können Sie „2017-09-18T14:00:00Z“ ohne Zeitzone angeben. <p>**Hinweis:** Diese Startzeit kann maximal 49 Jahre in der Zukunft liegen und muss dem [ISO 8601-Format für Datums-/Uhrzeitangaben](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) entsprechen und im [UTC-Datums-/Zeitformat](https://en.wikipedia.org/wiki/Coordinated_Universal_Time) angegeben werden, jedoch ohne [UTC-Abweichung](https://en.wikipedia.org/wiki/UTC_offset). Wenn Sie keine Zeitzone auswählen, müssen Sie den Buchstaben „Z“ ohne Leerzeichen anhängen. „Z“ bezieht sich auf die entsprechende [nautische Zeit](https://en.wikipedia.org/wiki/Nautical_time). <p>Bei einfachen Zeitpläne ist die Startzeit das erste Vorkommen. Bei komplexeren Zeitplänen wird der Trigger nicht vor der Startzeit ausgelöst. [*Wie kann ich Startdatum und -uhrzeit verwenden?*](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   ||||||

1.  Wenn Sie fertig sind, wählen Sie auf der Symbolleiste des Designers die Option **Speichern** aus.

1.  Fahren Sie nun damit fort, der Logik-App weitere Aktionen für die Aufgaben hinzuzufügen, die anhand der Triggerergebnisse durchgeführt werden sollen.

## <a name="add-actions"></a>Hinzufügen von Aktionen

Hier finden Sie ausführlichere Informationen zur Verwendung der verschiedenen verfügbaren Aktionen mit dem Sicherheits-API-Connector von Microsoft Graph.

### <a name="manage-alerts"></a>Warnungen verwalten

Um zu filtern, sortieren, oder die neuesten Ergebnisse zu erhalten, geben Sie *nur* die [von Microsoft Graph unterstützten ODATA-Abfrageparameter](/graph/query-parameters) ein. *Geben Sie nicht* die vollständige Basis-URL oder die HTTP-Aktion an, z.B. den `https://graph.microsoft.com/v1.0/security/alerts`-, `GET`- oder `PATCH`-Vorgang. Dieses spezifische Beispiel zeigt die Parameter für eine **Warnungen abrufen**-Aktion, wenn Sie eine Liste mit Warnungen mit hohem Schweregrad wünschen:

`Filter alerts value as Severity eq 'high'`

Weitere Informationen zu den Abfragen, die Sie mit diesem Connector verwenden können, finden Sie unter [Auflisten von Warnungen](/graph/api/alert-list). Um erweiterte Funktionen mit diesem Connector zu erstellen, informieren Sie sich über die [Schemaeigenschaftenwarnungen](/graph/api/resources/alert), die der Connector unterstützt.

| Aktion | BESCHREIBUNG |
|--------|-------------|
| **Warnungen abrufen** | Rufen Sie auf Basis von [Warnungseigenschaften](/graph/api/resources/alert) gefilterte Warnungen ab, z. B. `Provider eq 'Azure Security Center' or 'Palo Alto Networks'`. | 
| **Warnung nach ID abrufen** | Rufen Sie eine bestimmte Warnung basierend auf der Warnungs-ID ab. | 
| **Benachrichtigung aktualisieren** | Aktualisieren Sie eine bestimmte Warnung basierend auf der Warnungs-ID. Um sicherzustellen, dass Sie die erforderlichen und bearbeitbaren Eigenschaften in der Anforderung übergeben, informieren Sie sich über die [bearbeitbaren Eigenschaften für Warnungen](/graph/api/alert-update). Um z.B. eine Warnung Sicherheitsanalysten zur Untersuchung zuzuweisen, können Sie die Eigenschaft **Zugewiesen an** der Warnung aktualisieren. |
|||

### <a name="manage-alert-subscriptions"></a>Verwalten von Warnungsabonnements

Microsoft Graph unterstützt [*Abonnements*](/graph/api/resources/subscription) oder [*Webhooks*](/graph/api/resources/webhooks). Stellen Sie zum Abrufen, Aktualisieren oder Löschen von Abonnements die [von Microsoft Graph unterstützten ODATA-Abfrageparameter](/graph/query-parameters) für das Microsoft Graph-Entitätskonstrukt bereit, und beziehen Sie `security/alerts` gefolgt von der ODATA-Abfrage mit ein. *Beziehen Sie nicht* die Basis-URL mit ein, z.B. `https://graph.microsoft.com/v1.0`. Verwenden Sie stattdessen das Format in diesem Beispiel:

`security/alerts?$filter=status eq 'NewAlert'`

| Aktion | BESCHREIBUNG |
|--------|-------------|
| **Erstellen von Abonnements** | [Erstellen Sie ein Abonnement](/graph/api/subscription-post-subscriptions), das Sie über Änderungen informiert. Sie können dieses Abonnement nach den bestimmten Warnungstypen filtern, die Sie wünschen. Beispielsweise können Sie ein Abonnement erstellen, das Sie bei Warnungen mit hohem Schweregrad benachrichtigt. |
| **Aktive Abonnements abrufen** | [Rufen Sie nicht abgelaufene Abonnements ab](/graph/api/subscription-list). | 
| **Abonnement aktualisieren** | [Aktualisieren Sie ein Abonnement](/graph/api/subscription-update) durch die Bereitstellung der Abonnement-ID. Um z.B. Ihr Abonnement zu erweitern, können Sie die `expirationDateTime`-Eigenschaft des Abonnements aktualisieren. | 
| **Abonnement löschen** | [Löschen Sie ein Abonnement](/graph/api/subscription-delete) durch die Bereitstellung der Abonnement-ID. | 
||| 

### <a name="manage-threat-intelligence-indicators"></a>Verwalten von Threat Intelligence-Indikatoren

Um zu filtern, sortieren, oder die neuesten Ergebnisse zu erhalten, geben Sie *nur* die [von Microsoft Graph unterstützten ODATA-Abfrageparameter](/graph/query-parameters) ein. *Geben Sie nicht* die vollständige Basis-URL oder die HTTP-Aktion an, z.B. den `https://graph.microsoft.com/beta/security/tiIndicators`-, `GET`- oder `PATCH`-Vorgang. Dieses spezifische Beispiel zeigt die Parameter für eine **Get tiIndicators**-Aktion, wenn Sie eine Liste mit dem Bedrohungstyp `DDoS` benötigen:

`Filter threat intelligence indicator value as threatType eq 'DDoS'`

Weitere Informationen zu den Abfragen, die Sie mit diesem Connector verwenden können, finden Sie unter [„Optionale Abfrageparameter“ in der Referenzdokumentation zu Microsoft Graph Security-Threat Intelligence-Indikatoren](/graph/api/tiindicators-list). Um erweiterte Funktionen mit diesem Connector zu erstellen, informieren Sie sich über die [Threat Intelligence-Indikatoren für Schemaeigenschaften](/graph/api/resources/tiindicator), die der Connector unterstützt.

| Aktion | BESCHREIBUNG |
|--------|-------------|
| **Abrufen von Threat Intelligence-Indikatoren** | Rufen Sie auf Basis von [tiIndicators-Eigenschaften](/graph/api/resources/tiindicator) gefilterte Threat Intelligence-Indikatoren ab, z. B. `threatType eq 'MaliciousUrl' or 'DDoS'` |
| **Abrufen von Threat Intelligence-Indikatoren nach ID** | Rufen Sie einen bestimmten Threat Intelligence-Indikator basierend auf der tiIndicator-ID ab. | 
| **Erstellen von Threat Intelligence-Indikatoren** | Erstellen Sie einen neuen Threat Intelligence-Indikator, indem Sie Daten an die tiIndicator-Sammlung übermitteln. Um sicherzustellen, dass Sie die erforderlichen Eigenschaften in der Anforderung übergeben, informieren Sie sich über die [erforderlichen Eigenschaften zum Erstellen von Threat Intelligence-Indikatoren](/graph/api/tiindicators-post). |
| **Übermitteln mehrerer Threat Intelligence-Indikatoren** | Erstellen Sie mehrere neue Threat Intelligence-Indikatoren, indem Sie eine tiIndicator-Sammlung veröffentlichen. Um sicherzustellen, dass Sie in der Anforderung die erforderlichen Eigenschaften übergeben, informieren Sie sich über die [erforderlichen Eigenschaften zum Übermitteln mehrerer Threat Intelligence-Indikatoren](/graph/api/tiindicator-submittiindicators). |
| **Aktualisieren von Threat Intelligence-Indikatoren** | Aktualisieren Sie einen bestimmten Threat Intelligence-Indikator basierend auf der tiIndicator-ID. Um sicherzustellen, dass Sie in der Anforderung die erforderlichen und bearbeitbaren Eigenschaften übergeben, informieren Sie sich über die [bearbeitbaren Eigenschaften für Threat Intelligence-Indikatoren](/graph/api/tiindicator-update). Um z. B. die anzuwendende Aktion, wenn der Indikator innerhalb des Sicherheitstools targetProduct abgeglichen wird, zu aktualisieren, können Sie die Eigenschaft **Aktion** des Threat Intelligence-Indikators aktualisieren. |
| **Aktualisieren mehrerer Threat Intelligence-Indikatoren** | Aktualisieren Sie mehrere Threat Intelligence-Indikatoren. Um sicherzustellen, dass Sie in der Anforderung die erforderlichen Eigenschaften übergeben, informieren Sie sich über die [erforderlichen Eigenschaften zum Aktualisieren mehrerer Threat Intelligence-Indikatoren](/graph/api/tiindicator-updatetiindicators). |
| **Löschen von Threat Intelligence-Indikatoren nach ID** | Löschen Sie einen bestimmten Threat Intelligence-Indikator basierend auf der tiIndicator-ID. |
| **Löschen mehrerer Threat Intelligence-Indikatoren nach ID** | Löschen Sie mehrere Threat Intelligence-Indikatoren anhand ihrer IDs. Um sicherzustellen, dass Sie in der Anforderung die erforderlichen Eigenschaften übergeben, informieren Sie sich über die [erforderlichen Eigenschaften zum Löschen mehrerer Threat Intelligence-Indikatoren nach ID](/graph/api/tiindicator-deletetiindicators). |
| **Löschen mehrerer Threat Intelligence-Indikatoren nach externer ID** | Löschen Sie mehrere Threat Intelligence-Indikatoren anhand der externen IDs. Um sicherzustellen, dass Sie in der Anforderung die erforderlichen Eigenschaften übergeben, informieren Sie sich über die [erforderlichen Eigenschaften zum Löschen mehrerer Threat Intelligence-Indikatoren nach externer ID](/graph/api/tiindicator-deletetiindicatorsbyexternalid). |
|||

## <a name="connector-reference"></a>Connector-Referenz

Technische Details zu Triggern, Aktionen und Beschränkungen aus der OpenAPI-Beschreibung (ehemals Swagger) des Connectors finden Sie auf der [Referenzseite](/connectors/microsoftgraphsecurity/) des Connectors.

## <a name="next-steps"></a>Nächste Schritte

Informationen zu anderen [Logic Apps-Connectors](../connectors/apis-list.md)
