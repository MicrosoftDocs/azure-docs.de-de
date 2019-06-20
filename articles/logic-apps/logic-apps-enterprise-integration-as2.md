---
title: AS2-Nachrichten für die B2B-Unternehmensintegration – Azure Logic Apps
description: Austauschen von AS2-Nachrichten in Azure Logic Apps mit Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 04/22/2019
ms.openlocfilehash: b494f6524e5105a95bc8a24a6fa2521abcca3f7b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64729402"
---
# <a name="exchange-as2-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Austauschen von AS2-Nachrichten für die B2B-Unternehmensintegration in Azure Logic Apps mit Enterprise Integration Pack

Um mit AS2-Nachrichten in Azure Logic Apps zu arbeiten, können Sie den AS2-Connector verwenden, der Auslöser und Aktionen für die Verwaltung der AS2-Kommunikation bereitstellt. Um z. B. Sicherheit und Zuverlässigkeit bei der Übertragung von Nachrichten zu gewährleisten, können Sie die folgenden Aktionen verwenden:

* [**Als AS2-Nachricht codieren**-Aktion](#encode) zum Bereitstellen von Verschlüsselung, digitaler Signatur und Bestätigungen durch Benachrichtigungen über den Nachrichtenstatus (MDN), die beim Unterstützen der Nichtabstreitbarkeit helfen. Diese Aktion wendet z. B. AS2/HTTP-Header an und führt die folgenden Aufgaben aus, wenn sie konfiguriert sind:

  * Signieren ausgehender Nachrichten
  * Verschlüsseln ausgehender Nachrichten
  * Komprimieren der Nachricht
  * Dateinamen im MIME-Header übertragen

* [**AS2-Nachricht decodieren**-Aktion](#decode) zum Bereitstellen von Entschlüsselung, digitaler Signatur und Bestätigungen durch Benachrichtigungen über den Nachrichtenstatus (MDN). Diese Aktion führt z. B. die folgenden Aufgaben aus: 

  * Verarbeiten von AS2/HTTP-Headern
  * Abstimmen empfangener MDNs mit der ursprünglichen ausgehenden Nachricht
  * Aktualisieren und Korrelieren von Datensätzen in der Nichtabstreitbarkeits-Datenbank
  * Schreiben von Datensätzen für AS2-Statusberichte
  * Base64-codierte Ausgabe von Nutzlastinhalten
  * Ermitteln, ob MDNs erforderlich sind Ermittlung auf Grundlage der AS2-Vereinbarung, ob MDNs synchron oder asynchron sein müssen
  * Generieren einer synchronen oder asynchronen MDN auf Grundlage der AS2-Vereinbarung
  * Festlegen der Korrelationstoken und Eigenschaften für MDNs

  Diese Aktion führt auch die folgenden Aufgaben aus, wenn sie konfiguriert ist:

  * Überprüfen der Signatur
  * Entschlüsseln der Nachrichten
  * Dekomprimieren der Nachrichten 
  * Suchen nach und Unterbinden von doppelten Nachrichten-IDs

In diesem Artikel wird gezeigt, wie Sie die AS2-Aktionen für die Codierung und Decodierung zu einer bestehenden Logik-App hinzufügen können.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie noch kein Azure-Abonnement haben, [melden Sie sich für ein kostenloses Azure-Konto an](https://azure.microsoft.com/free/).

* Die Logik-App, von der aus Sie auf den AS2-Connector zugreifen möchten, und einen Trigger, der den Workflow Ihre Logik-App startet. Der AS2-Connector stellt nur Aktionen und keine Trigger bereit. Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen unter [Was ist Azure Logic Apps?](../logic-apps/logic-apps-overview.md) und [Schnellstart: Erstellen Ihres ersten automatisierten Workflows mit Azure Logic Apps – Azure-Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Ein [Integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), das Ihrem Azure-Abonnement zugeordnet und mit der Logik-App verknüpft ist, in der Sie den AS2-Connector verwenden möchten. Sowohl Ihre Logik-App als auch Ihr Integrationskonto müssen an demselben Standort oder in derselben Azure-Region vorhanden sein.

* Mindestens zwei [Parteien](../logic-apps/logic-apps-enterprise-integration-partners.md), die Sie bereits in Ihrem Integrationskonto unter Verwendung des AS2-Identitätsqualifizierers definiert haben.

* Bevor Sie den AS2-Connector verwenden können, müssen Sie eine AS2-[Vereinbarung](../logic-apps/logic-apps-enterprise-integration-agreements.md) zwischen den Parteien erstellen und diese Vereinbarung in Ihrem Integrationskonto speichern.

* Wenn Sie [Azure Key Vault](../key-vault/key-vault-overview.md) für die Zertifikatsverwaltung verwenden, überprüfen Sie, ob Ihre Tresorschlüssel die Vorgänge **Verschlüsseln** und **Entschlüsseln** zulassen. Andernfalls treten beim Codieren und Decodieren Fehler auf.

  Wechseln Sie im Azure-Portal zu Ihrem Schlüsseltresor, sehen Sie sich die **zulässigen Vorgänge** für den Tresorschlüssel an und bestätigen Sie, dass die Vorgänge **Verschlüsseln** und **Entschlüsseln** ausgewählt sind.

  ![Überprüfen der Tresorschlüsselvorgänge](media/logic-apps-enterprise-integration-as2/vault-key-permitted-operations.png)

<a name="encode"></a>

## <a name="encode-as2-messages"></a>Codieren von AS2-Nachrichten

1. Öffnen Sie, falls noch nicht geschehen, Ihre Logik-App über das [Azure-Portal](https://portal.azure.com) im Designer für Logik-Apps.

1. Fügen Sie im Designer eine neue Aktion zu Ihrer Logik-App hinzu. 

1. Wählen Sie unter **Aktion auswählen** und unter dem Suchfeld **Alle** aus. Geben Sie im Suchfeld „as2 codieren“ ein, und wählen Sie diese Aktion aus: **Als AS2-Nachricht codieren**.

   ![Wählen Sie „Als AS2-Nachricht codieren“ aus.](./media/logic-apps-enterprise-integration-as2/select-as2-encode.png)

1. Wenn keine bestehende Verbindung mit Ihrem Integrationskonto vorhanden ist, werden Sie aufgefordert, diese Verbindung jetzt zu erstellen. Benennen Sie die Verbindung, wählen Sie das Integrationskonto aus, mit dem Sie eine Verbindung herstellen möchten, und wählen Sie anschließend **Erstellen** aus.

   ![Integrationskontoverbindung erstellen](./media/logic-apps-enterprise-integration-as2/as2-create-connection.png)  
 
1. Geben Sie jetzt Informationen zu diesen Eigenschaften an:

   | Eigenschaft | BESCHREIBUNG |
   |----------|-------------|
   | **AS2-From** | Der Bezeichner für den Absender der Nachricht, wie in Ihrer AS2-Vereinbarung angegeben. |
   | **AS2-To** | Der Bezeichner für den Empfänger der Nachricht, wie in Ihrer AS2-Vereinbarung angegeben. |
   | **body** | Die Nachrichtennutzlast. |
   |||

   Beispiel:

   ![Eigenschaften für die Nachrichtencodierung](./media/logic-apps-enterprise-integration-as2/as2-message-encoding-details.png)

<a name="decode"></a>

## <a name="decode-as2-messages"></a>Decodieren von AS2-Nachrichten

1. Öffnen Sie, falls noch nicht geschehen, Ihre Logik-App über das [Azure-Portal](https://portal.azure.com) im Designer für Logik-Apps.

1. Fügen Sie im Designer eine neue Aktion zu Ihrer Logik-App hinzu. 

1. Wählen Sie unter **Aktion auswählen** und unter dem Suchfeld **Alle** aus. Geben Sie im Suchfeld „as2 decodieren“ ein, und wählen Sie diese Aktion aus: **Decodieren von AS2-Nachrichten**

   ![Wählen Sie „AS2-Nachricht decodieren“ aus.](media/logic-apps-enterprise-integration-as2/select-as2-decode.png)

1. Wenn keine bestehende Verbindung mit Ihrem Integrationskonto vorhanden ist, werden Sie aufgefordert, diese Verbindung jetzt zu erstellen. Benennen Sie die Verbindung, wählen Sie das Integrationskonto aus, mit dem Sie eine Verbindung herstellen möchten, und wählen Sie anschließend **Erstellen** aus.

   ![Integrationskontoverbindung erstellen](./media/logic-apps-enterprise-integration-as2/as2-create-connection.png)  

1. Wählen Sie für **body** und **Headers** diese Werte aus den vorherigen Trigger- oder Aktionsausgaben aus.

   Angenommen, Ihre Logik-App empfängt Nachrichten über einen Anforderungstrigger. Sie können die Ausgaben von diesem Trigger auswählen.

   ![Wählen Sie bei den Ausgaben der Anforderung den Text und die Header aus.](media/logic-apps-enterprise-integration-as2/as2-message-decoding-details.png) 

## <a name="sample"></a>Beispiel

Wenn Sie eine uneingeschränkt funktionsfähige Logik-App und ein AS2-Beispielszenario bereitstellen möchten, sehen Sie sich [Azure Logic Apps - AS2 Send Receive](https://azure.microsoft.com/documentation/templates/201-logic-app-as2-send-receive/) (Azure Logic Apps – AS2: Senden/Empfangen) an.

## <a name="connector-reference"></a>Connector-Referenz

Technische Details wie Trigger, Aktionen und Limits, wie sie in der OpenAPI-Datei (ehemals Swagger) des Connectors beschrieben werden, finden Sie auf der [Referenzseite des Connectors](/connectors/as2/).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)
