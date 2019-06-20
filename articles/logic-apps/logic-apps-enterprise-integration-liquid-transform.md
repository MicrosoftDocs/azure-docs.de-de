---
title: Konvertieren von JSON-Daten mit Liquid-Transformationen – Azure Logic Apps | Microsoft Docs
description: Erstellen von Transformationen oder Zuordnungen für erweiterte JSON-Transformationen mithilfe von Logic Apps und einer Liquid-Vorlage
services: logic-apps
ms.service: logic-apps
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, LADocs
ms.suite: integration
ms.topic: article
ms.date: 08/16/2018
ms.openlocfilehash: 5472a8ce2670a34174d6d39f0d90faca8a7002ad
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61467532"
---
# <a name="perform-advanced-json-transformations-with-liquid-templates-in-azure-logic-apps"></a>Ausführen erweiterter JSON-Transformationen mit Liquid-Vorlagen in Azure Logic Apps

Sie können grundlegende JSON-Transformationen in Ihren Logik-Apps über native Datenvorgangsaktionen wie **JSON erstellen** oder **JSON analysieren** durchführen. Sie können Vorlagen oder Zuordnungen mit [Liquid](https://shopify.github.io/liquid/) erstellen, einer Open Source-Vorlagensprache für flexible Web-Apps, um erweiterte JSON-Transformationen durchzuführen. Eine Liquid-Vorlagen definiert, wie JSON-Ausgaben umgewandelt werden. Dazu unterstützt Liquid komplexere JSON-Transformationen wie Iterationen, Ablaufsteuerungen, Variablen usw. unterstützen kann. 

Bevor Sie eine Liquid-Transformation in Ihrer Logik-App ausführen können, müssen Sie daher zunächst eine JSON-zu-JSON-Zuordnung mit einer Liquid-Vorlage definieren und die Zuordnung in Ihrem Integrationskonto speichern. Dieser Artikel zeigt, wie diese Liquid-Vorlage bzw. -Zuordnung erstellt und verwendet wird. 

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Falls Sie über kein Abonnement verfügen, können Sie [mit einem kostenlosen Azure-Konto beginnen](https://azure.microsoft.com/free/). Oder [registrieren Sie sich für ein Abonnement mit nutzungsbasierter Bezahlung](https://azure.microsoft.com/pricing/purchase-options/).

* Grundlegende Kenntnisse über die [Erstellung von Logik-Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Ein Basic-[Integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* Grundlegende Kenntnisse über die [Liquid-Vorlagensprache](https://shopify.github.io/liquid/).

## <a name="create-liquid-template-or-map-for-your-integration-account"></a>Erstellen einer Liquid-Vorlage oder -Zuordnung für Ihr Integrationskonto

1. Erstellen Sie für dieses Beispiel die Liquid-Beispielvorlage, die in diesem Schritt beschrieben wird. In Ihrer Liquid-Vorlage können Sie [Liquid-Filter](https://shopify.github.io/liquid/basics/introduction/#filters) einsetzen, die [DotLiquid](https://dotliquidmarkup.org/)- und C#-Benennungskonventionen verwenden. 

   > [!NOTE]
   > Stellen Sie sicher, dass die Filternamen die *normale Groß-/Kleinschreibung* in Ihrer Vorlage verwenden. Andernfalls funktionieren die Filter nicht.

   ```json
   {%- assign deviceList = content.devices | Split: ', ' -%}
   
   {
      "fullName": "{{content.firstName | Append: ' ' | Append: content.lastName}}",
      "firstNameUpperCase": "{{content.firstName | Upcase}}",
      "phoneAreaCode": "{{content.phone | Slice: 1, 3}}",
      "devices" : [
         {%- for device in deviceList -%}
            {%- if forloop.Last == true -%}
            "{{device}}"
            {%- else -%}
            "{{device}}",
            {%- endif -%}
        {%- endfor -%}
        ]
   }
   ```

2. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie im Azure-Hauptmenü die Option **Alle Ressourcen** aus. Suchen Sie im Suchfeld nach Ihrem Integrationskonto, und wählen Sie es aus.

   ![Auswählen des Integrationskontos](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

3.  Wählen Sie unter **Komponenten** die Option **Zuordnungen** aus.

    ![Auswählen von Zuordnungen](./media/logic-apps-enterprise-integration-liquid-transform/add-maps.png)

4. Wählen Sie **Hinzufügen** aus, und geben Sie dann die folgenden Details für die Zuordnung an:

   | Eigenschaft | Wert | BESCHREIBUNG | 
   |----------|-------|-------------|
   | **Name** | JsonToJsonTemplate | Der Name für Ihre Zuordnung, der in diesem Beispiel „JsonToJsonTemplate“ lautet. | 
   | **Zuordnungstyp** | **Liquid** | Der Typ Ihrer Zuordnung. Bei JSON-zu-JSON-Transformationen müssen Sie **liquid** auswählen. | 
   | **Map** | "SimpleJsonToJsonTemplate.liquid" | Eine vorhandene Liquid-Vorlagen- oder -Zuordnungsdatei, die für die Transformation verwendet wird. In diesem Beispiel heißt die Datei „SimpleJsonToJsonTemplate.liquid“. Sie können die Dateiauswahl verwenden, um diese Datei zu suchen. |
   ||| 

   ![Hinzufügen der Liquid-Vorlage](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)
    
## <a name="add-the-liquid-action-for-json-transformation"></a>Hinzufügen der Liquid-Aktion für die JSON-Transformation

1. Führen Sie die folgenden Schritte aus, um über das Azure-Portal [eine Ressourcengruppe zu erstellen](../logic-apps/quickstart-create-first-logic-app-workflow.md).

2. Fügen Sie Ihrer Logik-App im Logik-App-Designer den [Anforderungstrigger](../connectors/connectors-native-reqres.md#use-the-http-request-trigger) hinzu.

3. Wählen Sie unter dem Trigger die Option **Neuer Schritt** aus. 
   Geben Sie in das Suchfeld „Liquid“ als Filter ein, und wählen Sie die folgende Aktion aus: **Liquid – Von JSON in JSON transformieren**

   ![Suchen und Auswählen von Liquid-Aktionen](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

4. Klicken Sie in das Feld **Inhalt**, damit die dynamische Inhaltsliste angezeigt wird, und wählen Sie das **Textkörpertoken** aus.
  
   ![Auswählen eines Textkörpers](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)
 
5. Wählen Sie in der Liste **Zuordnung** Ihre Liquid-Vorlage aus, die in diesem Beispiel „JsonToJsonTemplate“ heißt.

   ![Auswählen einer Zuordnung](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Wenn die Zuordnungsliste keine Einträge enthält, ist Ihre Logik-App höchstwahrscheinlich nicht mit Ihrem Integrationskonto verknüpft. 
   Führen Sie die folgenden Schritte aus, um Ihre Logik-App mit dem Integrationskonto zu verknüpfen, das die Liquid-Vorlage oder -Zuordnung enthält:

   1. Wählen Sie im Menü Ihrer Logik-App die Option **Workfloweinstellungen** aus.

   2. Wählen Sie in der Liste **Wählen Sie ein Integrationskonto aus** Ihr Integrationskonto aus, und wählen Sie dann **Speichern** aus.

      ![Verknüpfen der Logik-App mit dem Integrationskonto](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

## <a name="test-your-logic-app"></a>Testen Ihrer Logik-App

Senden Sie die JSON-Eingabe über [Postman](https://www.getpostman.com/postman) oder ein ähnliches Tool an Ihre Logik-App. Die transformierte JSON-Ausgabe von der Logik-App sieht wie im folgenden Beispiel dargestellt aus:
  
![Beispielausgabe](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

## <a name="more-liquid-action-examples"></a>Weitere Beispiele für Liquid-Aktionen
Liquid ist nicht beschränkt auf JSON-Transformationen. Hier finden Sie andere verfügbare Transformationsaktionen, die Liquid verwenden.

* Transformieren von JSON in Text
  
  Dies ist die Liquid-Vorlage, die für dieses Beispiel verwendet wurde:
   
   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```
   Nachfolgend finden Sie Beispieleingaben und -ausgaben:
  
   ![Beispielausgabe JSON in Text](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

* Transformieren von XML in JSON
  
  Dies ist die Liquid-Vorlage, die für dieses Beispiel verwendet wurde:
   
   ``` json
   [{% JSONArrayFor item in content -%}
        {{item}}
    {% endJSONArrayFor -%}]
   ```
   Nachfolgend finden Sie Beispieleingaben und -ausgaben:

   ![Beispielausgabe XML in JSON](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

* Transformieren von XML in Text
  
  Dies ist die Liquid-Vorlage, die für dieses Beispiel verwendet wurde:

   ``` json
   {{content.firstName | Append: ' ' | Append: content.lastName}}
   ```

   Nachfolgend finden Sie Beispieleingaben und -ausgaben:

   ![Beispielausgabe XML in Text](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>Nächste Schritte

* [Weitere Informationen zum Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Informationen zum Enterprise Integration Pack")  
* [Weitere Informationen zu Zuordnungen](../logic-apps/logic-apps-enterprise-integration-maps.md "Informationen zu Zuordnungen für die Unternehmensintegration")  

