---
title: Integrieren von Sicherheitsvorgängen mit der Sicherheits-API von Microsoft Graph – Azure Logic Apps
description: Verbessern von Bedrohungsschutz-, Erkennungs- und Reaktionsfunktionen Ihrer App durch die Verwaltung von Sicherheitsvorgängen mit der Sicherheits-API von Microsoft Graph und Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: preetikr
ms.author: preetikr
ms.reviewer: klam, estfan, LADocs
ms.topic: article
ms.date: 01/30/2019
tags: connectors
ms.openlocfilehash: 24963a35bc3e54b2d140bf4ed1d169b213bd9b2a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60448046"
---
# <a name="improve-threat-protection-by-integrating-security-operations-with-microsoft-graph-security--azure-logic-apps"></a>Verbessern des Bedrohungsschutzes durch Integrieren von Sicherheitsvorgängen mit der Sicherheits-API von Microsoft Graph und Azure Logic Apps

Mit [Azure Logic Apps](../logic-apps/logic-apps-overview.md) und dem [Sicherheits-API-Connector von Microsoft Graph](https://docs.microsoft.com/graph/security-concept-overview) können Sie die Art und Weise verbessern, in der Ihre App Bedrohungen erkennt, sich schützt und reagiert, indem Sie automatisierte Workflows für die Integration von Microsoft-Sicherheitsprodukten, Diensten und Partnern erstellen. Sie können z.B. [Azure Security Center-Playbooks](../security-center/security-center-playbooks.md) erstellen, die Entitäten der Sicherheits-API von Microsoft Graph, z.B. Warnungen, überwachen und verwalten. Unter anderem werden folgende Szenarien vom Sicherheits-API-Connector von Microsoft Graph unterstützt:

* Abrufen von Warnungen basierend auf Abfragen oder nach Warnungs-ID. Sie können z.B. eine Liste abrufen, die Warnungen mit hohem Schweregrad enthält.
* Aktualisieren von Warnungen. Beispielsweise können Sie Warnungszuweisungen aktualisieren, Kommentare zu Warnungen hinzufügen oder Warnungen markieren.
* Überwachen, wenn Warnungen erstellt oder geändert werden, durch Erstellen von [Warnungsabonnements (Webhooks)](https://docs.microsoft.com/graph/api/resources/webhooks).
* Verwalten Ihrer Warnungsabonnements. Sie können z.B. aktive Abonnements abrufen, die Ablaufzeit für ein Abonnement erweitern oder Abonnements löschen.

Der Workflow Ihrer Logik-App kann Aktionen verwenden, die Antworten vom Sicherheits-API-Connector von Microsoft Graph erhalten, und diese Ausgabe weiteren Aktionen in Ihrem Workflow zur Verfügung stellen. Sie können die Ausgabe der Aktionen des Sicherheits-API-Connectors von Microsoft Graph auch von anderen Aktionen in Ihrem Workflow verwenden lassen. Wenn Sie z.B. Warnungen mit hohem Schweregrad über den Sicherheits-API-Connector von Microsoft Graph erhalten haben, können Sie diese Warnungen in einer E-Mail-Nachricht mithilfe des Outlook-Connectors senden. 

Weitere Informationen zu Microsoft Graph-Sicherheit finden Sie in der [Übersicht über die Sicherheits-API von Microsoft Graph](https://aka.ms/graphsecuritydocs). Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen unter [Was ist Azure Logic Apps?](../logic-apps/logic-apps-overview.md). Wenn Sie sich für Microsoft Flow oder PowerApps interessieren, lesen Sie [Weniger arbeiten, mehr erledigen](https://flow.microsoft.com/) oder [Apps für Ihren Geschäftserfolg](https://powerapps.microsoft.com/).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/). 

* Um den Sicherheits-API-Connector von Microsoft Graph verwenden zu können, benötigen Sie die *explizit gegebene* Zustimmung des Mandantenadministrators für Azure Active Directory (AD), die Teil der [Authentifizierungsanforderungen der Sicherheits-API in Microsoft Graph](https://aka.ms/graphsecurityauth) ist. Diese Zustimmung erfordert die Anwendungs-ID des Sicherheits-API-Connectors von Microsoft Graph und den Namen, die Sie auch im [Azure-Portal](https://portal.azure.com) finden:

   | Eigenschaft | Wert |
   |----------|-------|
   | **Anwendungsname** | `MicrosoftGraphSecurityConnector` |
   | **Anwendungs-ID** | `c4829704-0edc-4c3d-a347-7c4a67586f3c` |
   |||

   Um die Zustimmung für den Connector zu gewähren, kann Ihr Mandantenadministrator für Azure AD einen dieser Schritte ausführen:

   * [Erteilen der Zustimmung des Mandantenadministrators für Azure AD-Anwendungen](../active-directory/develop/v2-permissions-and-consent.md).

   * Während der ersten Ausführung Ihrer Logik-App kann Ihre App die Zustimmung von Ihrem Azure AD-Mandantenadministrator über die [Einwilligungserfahrung für Anwendungen](../active-directory/develop/application-consent-experience.md) anfordern.
   
* Grundlegende Kenntnisse über die [Erstellung von Logik-Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Die Logik-App, mit der Sie auf die Entitäten Ihrer Sicherheits-API von Microsoft Graph wie z.B. Warnungen zugreifen möchten. Dieser Connector verfügt derzeit nicht über Trigger. Um also eine Aktion der Sicherheits-API von Microsoft Graph zu verwenden, starten Sie Ihre Logik-App mit einem Trigger, z.B. dem **Wiederholungstrigger**.

## <a name="connect-to-microsoft-graph-security"></a>Herstellen einer Verbindung mit der Sicherheits-API von Microsoft Graph 

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, und öffnen Sie Ihre Logik-App im Logik-App-Designer, sofern sie nicht bereits geöffnet ist.

1. Fügen Sie für leere Logik-Apps die Trigger und alle weiteren gewünschten Aktionen hinzu, bevor Sie eine Aktion der Sicherheits-API von Microsoft Graph hinzufügen.

   Oder

   Wählen Sie für vorhandene Logik-Apps im letzten Schritt zum Hinzufügen einer Aktion der Sicherheits-API von Microsoft Graph die Option **Neuer Schritt** aus.

   Oder

   Wenn Sie zwischen Schritten eine Aktion einfügen möchten, bewegen Sie den Mauszeiger über den Pfeil zwischen den Schritten. 
   Wählen Sie das daraufhin angezeigte Pluszeichen (+) und dann **Aktion hinzufügen** aus.

1. Geben Sie in das Suchfeld „microsoft graph security“ als Filter ein. Wählen Sie in der Liste mit den Aktionen die gewünschte Aktion aus.

1. Melden Sie sich mit Ihren Anmeldeinformationen für die Sicherheits-API von Microsoft Graph.

1. Geben Sie die erforderlichen Informationen für die ausgewählte Aktion ein, und fahren Sie mit dem Erstellen Ihres Logik-App-Workflows fort.

## <a name="add-actions"></a>Hinzufügen von Aktionen

Hier finden Sie ausführlichere Informationen zur Verwendung der verschiedenen verfügbaren Aktionen mit dem Sicherheits-API-Connector von Microsoft Graph.

### <a name="manage-alerts"></a>Warnungen verwalten

Um zu filtern, sortieren, oder die neuesten Ergebnisse zu erhalten, geben Sie *nur* die [von Microsoft Graph unterstützten ODATA-Abfrageparameter](https://docs.microsoft.com/graph/query-parameters) ein. *Geben Sie nicht* die vollständige Basis-URL oder die HTTP-Aktion an, z.B. den `https://graph.microsoft.com/v1.0/security/alerts`-, `GET`- oder `PATCH`-Vorgang. Dieses spezifische Beispiel zeigt die Parameter für eine **Warnungen abrufen**-Aktion, wenn Sie eine Liste mit Warnungen mit hohem Schweregrad wünschen:

`Filter alerts value as Severity eq 'high'`

Weitere Informationen zu den Abfragen, die Sie mit diesem Connector verwenden können, finden Sie unter [Auflisten von Warnungen](https://docs.microsoft.com/graph/api/alert-list). Um erweiterte Funktionen mit diesem Connector zu erstellen, informieren Sie sich über die [Schemaeigenschaftenwarnungen](https://docs.microsoft.com/graph/api/resources/alert), die der Connector unterstützt.

| Aktion | BESCHREIBUNG |
|--------|-------------|
| **Warnungen abrufen** | Rufen Sie auf Basis einer oder mehrerer [Warnungseigenschaften](https://docs.microsoft.com/graph/api/resources/alert) gefilterte Warnungen ab, z.B.: <p>`Provider eq 'Azure Security Center' or 'Palo Alto Networks'` | 
| **Warnung nach ID abrufen** | Rufen Sie eine bestimmte Warnung basierend auf der Warnungs-ID ab. | 
| **Benachrichtigung aktualisieren** | Aktualisieren Sie eine bestimmte Warnung basierend auf der Warnungs-ID. <p>Um sicherzustellen, dass Sie die erforderlichen und bearbeitbaren Eigenschaften in der Anforderung übergeben, informieren Sie sich über die [bearbeitbaren Eigenschaften für Warnungen](https://docs.microsoft.com/graph/api/alert-update). Um z.B. eine Warnung Sicherheitsanalysten zur Untersuchung zuzuweisen, können Sie die Eigenschaft **Zugewiesen an** der Warnung aktualisieren. |
|||

### <a name="manage-alert-subscriptions"></a>Verwalten von Warnungsabonnements

Microsoft Graph unterstützt [ *Abonnements*](https://docs.microsoft.com/graph/api/resources/subscription) oder [ *Webhooks*](https://docs.microsoft.com/graph/api/resources/webhooks). Stellen Sie zum Abrufen, Aktualisieren oder Löschen von Abonnements die [von Microsoft Graph unterstützten ODATA-Abfrageparameter](https://docs.microsoft.com/graph/query-parameters) für das Microsoft Graph-Entitätskonstrukt bereit, und beziehen Sie `security/alerts` gefolgt von der ODATA-Abfrage mit ein. 
*Beziehen Sie nicht* die Basis-URL mit ein, z.B. `https://graph.microsoft.com/v1.0`. Verwenden Sie stattdessen das Format in diesem Beispiel:

`security/alerts?$filter=status eq 'New'`

| Aktion | BESCHREIBUNG |
|--------|-------------|
| **Erstellen von Abonnements** | [Erstellen Sie ein Abonnement](https://docs.microsoft.com/graph/api/subscription-post-subscriptions), das Sie über Änderungen informiert. Sie können dieses Abonnement nach den bestimmten Warnungstypen filtern, die Sie wünschen. Beispielsweise können Sie ein Abonnement erstellen, das Sie bei Warnungen mit hohem Schweregrad benachrichtigt. |
| **Aktive Abonnements abrufen** | [Rufen Sie nicht abgelaufene Abonnements ab](https://docs.microsoft.com/graph/api/subscription-list). | 
| **Abonnement aktualisieren** | [Aktualisieren Sie ein Abonnement](https://docs.microsoft.com/graph/api/subscription-update) durch die Bereitstellung der Abonnement-ID. Um z.B. Ihr Abonnement zu erweitern, können Sie die `expirationDateTime`-Eigenschaft des Abonnements aktualisieren. | 
| **Abonnement löschen** | [Löschen Sie ein Abonnement](https://docs.microsoft.com/graph/api/subscription-delete) durch die Bereitstellung der Abonnement-ID. | 
||| 

## <a name="connector-reference"></a>Connector-Referenz

Technische Details zu Triggern, Aktionen und Beschränkungen aus der OpenAPI-Beschreibung (ehemals Swagger) des Connectors finden Sie auf der [Referenzseite](https://aka.ms/graphsecurityconnectorreference) des Connectors.

## <a name="get-support"></a>Support

Sollten Sie Fragen haben, besuchen Sie das [Azure Logic Apps-Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
Wenn Sie Features vorschlagen oder für Vorschläge abstimmen möchten, besuchen Sie die [Website für Logic Apps-Benutzerfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nächste Schritte

Informationen zu anderen [Logic Apps-Connectors](../connectors/apis-list.md)
