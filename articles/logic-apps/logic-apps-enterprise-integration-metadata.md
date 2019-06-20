---
title: Verwalten von Artefaktmetadaten in Integrationskonten – Azure Logic Apps| Microsoft-Dokumentation
description: Hinzufügen oder Abrufen von Artefaktmetadaten aus Integrationskonten in Azure Logic Apps mit Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.date: 01/17/2019
ms.openlocfilehash: 5ebdf45bec4e7cfceb75354af40c7a21c22c6eef
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60846200"
---
# <a name="manage-artifact-metadata-in-integration-accounts-with-azure-logic-apps-and-enterprise-integration-pack"></a>Hinzufügen oder Abrufen von Artefaktmetadaten in Integrationskonten in Azure Logic Apps und Enterprise Integration Pack

Sie können benutzerdefinierte Metadaten für Artefakte in Integrationskonten definieren und diese Metadaten während der Laufzeit der Logik-App abrufen. Beispielsweise können Sie Metadaten für Artefakte wie Partner, Vereinbarungen, Schemas und Zuordnungen angeben. Alle davon speichern Metadaten mit Schlüssel-Wert-Paaren. 

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Falls Sie kein Abonnement besitzen, können Sie sich <a href="https://azure.microsoft.com/free/" target="_blank">für ein kostenloses Azure-Konto registrieren</a>.

* Ein grundlegendes [Integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) mit den Artefakten, denen Sie Metadaten hinzufügen möchten. Beispiel: 

  * [Partner](logic-apps-enterprise-integration-partners.md)
  * [Vereinbarung](logic-apps-enterprise-integration-agreements.md)
  * [Schema](logic-apps-enterprise-integration-schemas.md)
  * [Map](logic-apps-enterprise-integration-maps.md)

* Eine Logik-App, die mit dem Integrationskonto und den Artefaktmetadaten verknüpft ist, die Sie verwenden möchten. Wenn Ihre Logik-App noch nicht verknüpft ist, lesen Sie den Abschnitt zum [Verknüpfen von Logik-Apps mit Integrationskonten](logic-apps-enterprise-integration-create-integration-account.md#link-account). 

  Wenn Sie noch nicht über eine Logik-App verfügen, lesen Sie den Artikel zum [Erstellen von Logik-Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md). 
  Fügen Sie die Auslöser und Aktionen hinzu, mit denen Sie Artefaktmetadaten verwalten möchten. Oder fügen Sie Ihrer Logik-App testweise einen Auslöser wie **Request** oder **HTTP** hinzu.

## <a name="add-metadata-to-artifacts"></a>Hinzufügen von Metadaten zu Artefakten

1. Melden Sie sich mit den Anmeldeinformationen Ihres Azure-Kontos beim <a href="https://portal.azure.com" target="_blank">Azure-Portal</a> an. Suchen und öffnen Sie Ihr Integrationskonto.

1. Wählen Sie das Artefakt, dem Sie die Metadaten hinzufügen möchten, und wählen Sie **Bearbeiten**. Geben Sie Metadatendetails für das Artefakt ein. Beispiel:

   ![Eingeben von Metadaten](media/logic-apps-enterprise-integration-metadata/add-partner-metadata.png)

1. Wählen Sie **OK** aus, wenn Sie fertig sind.

1. Wenn Sie diese Metadaten in der JSON-Definition (JavaScript Object Notation) für das Integrationskonto anzeigen möchten, wählen Sie **Als JSON bearbeiten**, um den JSON-Editor zu öffnen: 

   ![JSON für Partnermetadaten](media/logic-apps-enterprise-integration-metadata/partner-metadata.png)

## <a name="get-artifact-metadata"></a>Abrufen von Artefaktmetadaten

1. Öffnen Sie im Azure-Portal die Logik-App, die mit dem gewünschten Integrationskonto verknüpft ist. 

1. Wenn Sie im Logik-App-Designer den Schritt zum Abrufen von Metadaten unter dem Auslöser oder die letzte Aktion im Workflow hinzufügen, wählen Sie **Neuer Schritt** > **Aktion hinzufügen**. 

1. Geben Sie im Suchfeld „Integrationskonto“ ein. Wählen Sie unter dem Suchfeld **Alle** aus. Wählen Sie in der Liste mit den Aktionen diese Aktion aus: **Artefaktsuche für Integrationskonto – Integrationskonto**

   ![Auswählen von „Artefaktsuche für Integrationskonto“](media/logic-apps-enterprise-integration-metadata/integration-account-artifact-lookup.png)

1. Geben Sie diese Informationen für das gesuchte Artefakt ein:

   | Eigenschaft | Erforderlich | Value | BESCHREIBUNG | 
   |----------|---------|-------|-------------| 
   | **Artefakttyp** | Ja | **Schema**, **Zuordnung**, **Partner**, **Vereinbarung** oder ein benutzerdefinierter Typ | Der Typ des gewünschten Artefakts | 
   | **Artefaktname** | Ja | <*artifact-name*> | Der Name des gewünschten Artefakts | 
   ||| 

   So rufen Sie beispielsweise die Metadaten für ein Handelspartnerartefakt ab:

   ![Auswählen des Artefakttyps und Eingeben des Namens des Artefakts](media/logic-apps-enterprise-integration-metadata/artifact-lookup-information.png)

1. Fügen Sie die gewünschte Aktion für die Bearbeitung der Metadaten hinzu. Beispiel:

   1. Wählen Sie unter der Aktion **Artefaktsuche für Integrationskonto** die Option **Nächster Schritt**, und wählen Sie **Aktion hinzufügen**. 

   1. Geben Sie im Suchfeld „http“ ein. Wählen Sie im Suchfeld **Integrierte Funktionen**, und wählen Sie diese Aktion: **HTTP – HTTP**

      ![Hinzufügen einer HTTP-Aktion](media/logic-apps-enterprise-integration-metadata/http-action.png)

   1. Geben Sie die Informationen für die Artefaktmetadaten ein, die Sie verwalten möchten. 

      Beispiel: Sie möchten die `routingUrl`-Metadaten abrufen, die Sie zuvor hinzugefügt haben. Diese Eigenschaftswerte könnten Sie angeben: 

      | Eigenschaft | Erforderlich | Value | BESCHREIBUNG | 
      |----------|----------|-------|-------------| 
      | **Methode** | Ja | <*operation-to-run*> | Der HTTP-Vorgang, der auf das Artefakt ausgeführt werden soll. Diese HTTP-Aktion verwendet z.B. die **GET**-Methode. | 
      | **URI** | Ja | <*metadata-location*> | Um auf den `routingUrl`-Metadatenwert des abgerufenen Artefakts zuzugreifen, können Sie einen Ausdruck verwenden. Beispiel: <p>`@{outputs('Integration_Account_Artifact_Lookup')['properties']['metadata']['routingUrl']}` | 
      | **Header** | Nein | <*header-values*> | Alle Headerausgaben des Auslösers, die Sie in die HTTP-Aktion übergeben möchten. Um den `headers`-Eigenschaftswert des Auslöser zu übergeben, können Sie beispielsweise einen Ausdruck verwenden. Beispiel: <p>`@triggeroutputs()['headers']` | 
      | **Text** | Nein | <*body-content*> | Alle anderen Inhalte, die Sie über die `body`-Eigenschaft der HTTP-Aktion übergeben möchten. In diesem Beispiel werden die `properties`-Werte in die HTTP-Aktion übergeben: <p>1. Klicken Sie in die Eigenschaft **Body**, damit die dynamische Inhaltsliste angezeigt wird. Wenn keine Eigenschaften angezeigt werden, wählen Sie **Mehr anzeigen**. <br>2. Wählen Sie aus der dynamischen Inhaltsliste unter **Artefaktsuche für Integrationskonto** die Option **Eigenschaften**. | 
      |||| 

      Beispiel:

      ![Festlegen von Werten und Ausdrücken für HTTP-Aktion](media/logic-apps-enterprise-integration-metadata/add-http-action-values.png)

   1. Zeigen Sie zum Prüfen der Informationen, die Sie für die HTTP-Aktion angeben haben, die JSON-Definition Ihrer Logik-App an. Wählen Sie in der Symbolleiste des Logik-App-Designers die **Codeansicht**, damit die JSON-Definition der App angezeigt wird. Beispiel:

      ![JSON-Definition der Logik-App](media/logic-apps-enterprise-integration-metadata/finished-logic-app-definition.png)

      Wenn Sie wieder zum Logik-App-Designer wechseln, werden alle bisher verwendeten Ausdrücke als aufgelöst angezeigt. Beispiel:

      ![Aufgelöste Ausdrücke im Logik-App-Designer](media/logic-apps-enterprise-integration-metadata/resolved-expressions.png)

## <a name="next-steps"></a>Nächste Schritte

* [Weitere Informationen zu Vereinbarungen](logic-apps-enterprise-integration-agreements.md)
