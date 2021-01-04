---
title: Konvertieren von JSON und XML mithilfe von Liquid-Vorlagen
description: Transformieren von JSON und XML mithilfe von Liquid-Vorlagen als Zuordnungen in Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, daviburg, logicappspm
ms.topic: article
ms.date: 07/31/2020
ms.openlocfilehash: 0362c9ed4f736474dbd49e1bfaf1373e0f48acd6
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94992708"
---
# <a name="transform-json-and-xml-using-liquid-templates-as-maps-in-azure-logic-apps"></a>Transformieren von JSON und XML mithilfe von Liquid-Vorlagen als Zuordnungen in Azure Logic Apps

Wenn Sie grundlegende JSON-Transformationen in Ihren Logik-Apps durchführen möchten, können Sie native [Datenvorgänge](../logic-apps/logic-apps-perform-data-operations.md) wie **JSON erstellen** oder **JSON analysieren** verwenden. Für erweiterte und komplexe JSON-in-JSON-Transformationen, die Elemente wie Iterationen, Ablaufsteuerungen und Variablen aufweisen, erstellen und verwenden Sie Vorlagen, die diese Transformationen mithilfe der Open Source-Vorlagensprache [Liquid](https://shopify.github.io/liquid/) beschreiben. Sie können auch [andere Transformationen ausführen](#other-transformations), z. B. JSON in Text, XML in JSON und XML in Text.

Bevor Sie eine Liquid-Transformation in Ihrer Logik-App ausführen können, müssen Sie zunächst eine Liquid-Vorlage erstellen, die die gewünschte Zuordnung definiert. Dann [laden Sie die Vorlage als Zuordnung hoch](../logic-apps/logic-apps-enterprise-integration-maps.md) in Ihr [Integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md). Wenn Sie die Aktion **Liquid – Von JSON in JSON transformieren** zu Ihrer Logik-App hinzufügen, können Sie dann die Liquid-Vorlage als Zuordnung für die zu verwendende Aktion auswählen.

In diesem Artikel wird gezeigt, wie Sie die folgenden Aufgaben ausführen:

* Erstellen einer Liquid-Vorlage.
* Hinzufügen der Vorlage zu Ihrem Integrationskonto.
* Hinzufügen der Liquid-Transformationsaktion zu Ihrer Logik-App.
* Auswählen der Vorlage als die Zuordnung, die Sie verwenden möchten.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Falls Sie kein Abonnement besitzen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/).

* Grundlegende Kenntnisse über die [Erstellung von Logik-Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Ein [Integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

* Grundlegende Kenntnisse über die [Liquid-Vorlagensprache](https://shopify.github.io/liquid/)

  > [!NOTE]
  > Die Aktion **Liquid – Von JSON in JSON transformieren** folgt der [DotLiquid-Implementierung für Liquid](https://github.com/dotliquid/dotliquid), die sich in bestimmten Fällen von der [Shopify-Implementierung für Liquid](https://shopify.github.io/liquid) unterscheidet. Weitere Informationen finden Sie unter [Überlegungen zur Liquid-Vorlage](#liquid-template-considerations).

## <a name="create-the-template"></a>Erstellen der Vorlage

1. Erstellen Sie die Liquid-Vorlage, die Sie als Zuordnung für die JSON-Transformation verwenden. Sie können ein beliebiges Bearbeitungstool verwenden.

   Erstellen Sie für dieses Beispiel die Liquid-Vorlage, wie in diesem Schritt beschrieben:

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

1. Speichern Sie die Vorlage mithilfe der `.liquid`-Erweiterung. In diesem Beispiel wird `SimpleJsonToJsonTemplate.liquid` verwendet.

## <a name="upload-the-template"></a>Hochladen der Vorlage

1. Melden Sie sich mit den Anmeldeinformationen Ihres Azure-Kontos beim [Azure-Portal](https://portal.azure.com) an.

1. Geben Sie im Azure-Portal in das Suchfeld `integration accounts` ein, und wählen Sie **Integrationskonten** aus.

   ![„Integrationskonten“ suchen](./media/logic-apps-enterprise-integration-liquid-transform/find-integration-accounts.png)

1. Suchen Sie Ihr Integrationskonto, und wählen Sie es aus.

   ![Auswählen des Integrationskontos](./media/logic-apps-enterprise-integration-liquid-transform/select-integration-account.png)

1. Wählen Sie im Bereich **Übersicht** unter **Komponenten** die Option **Zuordnungen** aus.

    ![Kachel „Zuordnungen“ auswählen](./media/logic-apps-enterprise-integration-liquid-transform/select-maps-tile.png)

1. Wählen Sie im Bereich **Zuordnungen** den Befehl **Hinzufügen** aus, und geben Sie die folgenden Details für Ihre Zuordnung an:

   | Eigenschaft | Wert | BESCHREIBUNG |
   |----------|-------|-------------|
   | **Name** | `JsonToJsonTemplate` | Der Name für Ihre Zuordnung, der in diesem Beispiel „JsonToJsonTemplate“ lautet. |
   | **Zuordnungstyp** | **Liquid** | Der Typ Ihrer Zuordnung. Bei JSON-zu-JSON-Transformationen müssen Sie **liquid** auswählen. |
   | **Map** | `SimpleJsonToJsonTemplate.liquid` | Eine vorhandene Liquid-Vorlagen- oder -Zuordnungsdatei, die für die Transformation verwendet wird. In diesem Beispiel heißt die Datei „SimpleJsonToJsonTemplate.liquid“. Sie können die Dateiauswahl verwenden, um diese Datei zu suchen. Informationen zu Größenbeschränkungen für Karten finden Sie unter [Grenzwerte und Konfiguration](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits). |
   |||

   ![Hinzufügen der Liquid-Vorlage](./media/logic-apps-enterprise-integration-liquid-transform/add-liquid-template.png)

## <a name="add-the-liquid-transformation-action"></a>Hinzufügen der Liquid-Transformationsaktion

1. Führen Sie die folgenden Schritte aus, um über das Azure-Portal [eine Ressourcengruppe zu erstellen](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Fügen Sie Ihrer Logik-App im Logik-App-Designer den [Anforderungstrigger](../connectors/connectors-native-reqres.md#add-request) hinzu.

1. Wählen Sie unter dem Trigger die Option **Neuer Schritt** aus. Geben Sie `liquid` als Filter in das Suchfeld ein, und wählen Sie die folgende Aktion aus: **Liquid – Von JSON in JSON transformieren**

   ![Suchen und Auswählen von Liquid-Aktionen](./media/logic-apps-enterprise-integration-liquid-transform/search-action-liquid.png)

1. Öffnen Sie die Liste **Zuordnung**, und wählen Sie Ihre Liquid-Vorlage aus, die in diesem Beispiel „JsonToJsonTemplate“ heißt.

   ![Auswählen einer Zuordnung](./media/logic-apps-enterprise-integration-liquid-transform/select-map.png)

   Wenn die Zuordnungsliste keine Einträge enthält, ist Ihre Logik-App höchstwahrscheinlich nicht mit Ihrem Integrationskonto verknüpft. 
   Führen Sie die folgenden Schritte aus, um Ihre Logik-App mit dem Integrationskonto zu verknüpfen, das die Liquid-Vorlage oder -Zuordnung enthält:

   1. Wählen Sie im Menü Ihrer Logik-App die Option **Workfloweinstellungen** aus.

   1. Wählen Sie in der Liste **Wählen Sie ein Integrationskonto aus** Ihr Integrationskonto aus, und wählen Sie dann **Speichern** aus.

      ![Verknüpfen der Logik-App mit dem Integrationskonto](./media/logic-apps-enterprise-integration-liquid-transform/link-integration-account.png)

1. Fügen Sie dieser Aktion nun die Eigenschaft **Inhalt** hinzu. Öffnen Sie die Liste **Neuen Parameter hinzufügen**, und wählen Sie **Inhalt** aus.

   ![Eigenschaft „Inhalt“ zu der Aktion hinzufügen](./media/logic-apps-enterprise-integration-liquid-transform/add-content-property-to-action.png)

1. Um den Wert der Eigenschaft **Inhalt** festzulegen, klicken Sie in das Feld **Inhalt**, damit die dynamische Inhaltsliste angezeigt wird. Wählen Sie das Token **Body** aus, das die Textinhaltsausgabe des Triggers darstellt.

   ![„Body“-Token für den Eigenschaftswert von „Inhalt“ auswählen](./media/logic-apps-enterprise-integration-liquid-transform/select-body.png)

   Wenn Sie fertig sind, sieht die Aktion wie das folgende Beispiel aus:

   ![Abgeschlossene Aktion „Von JSON in JSON transformieren“](./media/logic-apps-enterprise-integration-liquid-transform/finished-transform-action.png)

## <a name="test-your-logic-app"></a>Testen Ihrer Logik-App

Stellen Sie mittels [Postman](https://www.getpostman.com/postman) oder einem ähnlichen Tool JSON-Eingaben für Ihre Logik-App bereit. Die transformierte JSON-Ausgabe von der Logik-App sieht wie im folgenden Beispiel dargestellt aus:

![Beispielausgabe](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontojson.png)

<a name="template-considerations"></a>

## <a name="liquid-template-considerations"></a>Überlegungen zu Liquid-Vorlagen

* Liquid-Vorlagen folgen den [Dateigrößenlimits für Zuordnungen](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits) in Azure Logic Apps.

* Die Aktion **Liquid – Von JSON in JSON transformieren** folgt der [DotLiquid-Implementierung für Liquid](https://github.com/dotliquid/dotliquid). Diese Implementierung ist eine Portierung in das .NET Framework aus der [Shopify-Implementierung für Liquid](https://shopify.github.io/liquid/) und unterscheidet sich in [bestimmten Fällen](https://github.com/dotliquid/dotliquid/issues).

  Die bekannten Unterschiede sind wie folgt:

  * Die Aktion **Liquid – Von JSON in JSON transformieren** gibt nativ eine Zeichenfolge aus, die JSON, XML, HTML usw. enthalten kann. Die Liquid-Aktion gibt lediglich an, dass die erwartete Textausgabe aus der Liquid-Vorlage eine JSON-Zeichenfolge ist. Die Aktion weist ihre Logik-App an, Eingaben als JSON-Objekt zu analysieren, und wendet einen Wrapper an, damit Liquid die JSON-Struktur interpretieren kann. Nach der Transformation weist die Aktion Ihre Logik-App an, die Textausgabe von Liquid zurück in JSON zu analysieren.

    DotLiquid versteht JSON nicht nativ. Stellen Sie daher sicher, dass Sie den umgekehrten Schrägstrich (`\`) und alle anderen reservierten JSON-Zeichen mit Escapezeichen versehen.

  * Wenn Ihre Vorlage [Liquid-Filter](https://shopify.github.io/liquid/basics/introduction/#filters) verwendet, stellen Sie sicher, dass Sie die [DotLiquid- und C#-Benennungskonventionen](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers#filter-and-output-casing) einhalten, die *normale Groß-/Kleinschreibung (wie in Sätzen)* verwenden. Stellen Sie bei allen Liquid-Transformationen sicher, dass Filternamen in Ihrer Vorlage ebenfalls normale Groß-/Kleinschreibung verwenden. Andernfalls funktionieren die Filter nicht.

    Wenn Sie z. B. den `replace`-Filter verwenden, verwenden Sie `Replace`, nicht `replace`. Dieselbe Regel gilt, wenn Sie Beispiele bei [DotLiquid Online](http://dotliquidmarkup.org/try-online) ausprobieren. Weitere Informationen finden Sie unter [Shopify Liquid-Filter](https://shopify.dev/docs/themes/liquid/reference/filters) und [DotLiquid Liquid-Filter](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Developers#create-your-own-filters). Die Shopify-Spezifikation umfasst Beispiele für jeden Filter, sodass Sie zu Vergleichszwecken diese Beispiele unter [DotLiquid – Online testen](http://dotliquidmarkup.org/try-online) ausprobieren können.

  * Der `json`-Filter aus den Shopify-Erweiterungsfiltern ist zurzeit [in DotLiquid nicht implementiert](https://github.com/dotliquid/dotliquid/issues/384). In der Regel können Sie diesen Filter verwenden, um die Textausgabe für die JSON-Zeichenfolgenanalyse vorzubereiten, doch stattdessen müssen Sie den `Replace`-Filter verwenden.

  * Der `Replace`-Standardfilter in der [DotLiquid-Implementierung](https://github.com/dotliquid/dotliquid/blob/b6a7d992bf47e7d7dcec36fb402f2e0d70819388/src/DotLiquid/StandardFilters.cs#L425) verwendet den [Abgleich regulärer Ausdrücke (RegEx)](/dotnet/standard/base-types/regular-expression-language-quick-reference), während die [Shopify-Implementierung](https://shopify.github.io/liquid/filters/replace/) einen [einfachen Zeichenfolgenabgleich](https://github.com/Shopify/liquid/issues/202) verwendet. Beide Implementierungen scheinen identisch zu funktionieren, bis Sie ein für reguläre Ausdrücke reserviertes Zeichen oder ein Escapezeichen im match-Parameter verwenden.

    Um z. B. den für reguläre Ausdrücke reservierten umgekehrten Schrägstrich (`\`) mit Escapezeichen zu versehen, verwenden Sie `| Replace: '\\', '\\'`, nicht `| Replace: '\', '\\'`. In diesen Beispielen wird gezeigt, wie sich der `Replace`-Filter anders verhält, wenn Sie versuchen, den umgekehrten Schrägstrich mit Escapezeichen zu versehen. Während diese Version erfolgreich funktioniert:

    `{ "SampleText": "{{ 'The quick brown fox "jumped" over the sleeping dog\\' | Replace: '\\', '\\' | Replace: '"', '\"'}}"}`

    Mit diesem Ergebnis:

    `{ "SampleText": "The quick brown fox \"jumped\" over the sleeping dog\\\\"}`

    Schlägt diese Version fehl:

    `{ "SampleText": "{{ 'The quick brown fox "jumped" over the sleeping dog\\' | Replace: '\', '\\' | Replace: '"', '\"'}}"}`

    Mit diesem Fehler:

    `{ "SampleText": "Liquid error: parsing "\" - Illegal \ at end of pattern."}`

    Weitere Informationen finden Sie unter [Replace-Standardfilter verwendet Musterabgleich mit regulären Ausdrücken (RegEx)](https://github.com/dotliquid/dotliquid/issues/385).

  * Der `Sort`-Filter in der [DotLiquid-Implementierung](https://github.com/dotliquid/dotliquid/blob/b6a7d992bf47e7d7dcec36fb402f2e0d70819388/src/DotLiquid/StandardFilters.cs#L326) sortiert Elemente in einem Array oder einer Sammlung nach Eigenschaft, jedoch mit den folgenden Unterschieden:<p>

    * Befolgt das [sort_natural-Verhalten von Shopify](https://shopify.github.io/liquid/filters/sort_natural/), nicht das [Sortierverhalten (sort) von Shopify](https://shopify.github.io/liquid/filters/sort/).

    * Sortiert nur in alphanumerischen Zeichenfolgenreihenfolge. Weitere Informationen finden Sie unter [numerische Sortierung](https://github.com/Shopify/liquid/issues/980).

    * Verwendet eine Reihenfolge *ohne Beachtung von Groß-/Kleinschreibung*, keine Reihenfolge unter Beachtung von Groß-/Kleinschreibung. Weitere Informationen finden Sie unter [Sort-Filter hält nicht das Groß-/Kleinschreibungsverhalten der Shopify-Spezifikation ein]( https://github.com/dotliquid/dotliquid/issues/393).

<a name="other-transformations"></a>

## <a name="other-transformations-using-liquid"></a>Andere Transformationen, die Liquid verwenden

Liquid ist nicht beschränkt auf nur JSON-Transformationen. Sie können Liquid auch verwenden, um andere Transformationen auszuführen, z. B.:

* [JSON in Text](#json-text)
* [XML in JSON](#xml-json)
* [XML in Text](#xml-text)

<a name="json-text"></a>

### <a name="transform-json-to-text"></a>Transformieren von JSON in Text

Dies ist die Liquid-Vorlage, die für dieses Beispiel verwendet wird:

```json
{{content.firstName | Append: ' ' | Append: content.lastName}}
```

Nachfolgend finden Sie die Beispieleingaben und -ausgaben:

![Beispielausgabe JSON in Text](./media/logic-apps-enterprise-integration-liquid-transform/example-output-jsontotext.png)

<a name="xml-json"></a>

### <a name="transform-xml-to-json"></a>Transformieren von XML in JSON

Dies ist die Liquid-Vorlage, die für dieses Beispiel verwendet wird:

``` json
[{% JSONArrayFor item in content -%}
      {{item}}
  {% endJSONArrayFor -%}]
```

Die `JSONArrayFor`-Schleife ist ein benutzerdefinierter Schleifenmechanismus für XML-Eingaben, damit sie JSON-Nutzlasten erstellen können, die ein nachgestelltes Komma vermeiden. Außerdem verwendet die `where`-Bedingung für diesen benutzerdefinierten Schleifenmechanismus den Namen des XML-Elements für Vergleiche, anstatt den Wert des Elements zu verwenden, wie andere Liquid-Filter. Weitere Informationen finden Sie unter [Deep Dive on set-body Policy - Collections of Things](https://azure.microsoft.com/blog/deep-dive-on-set-body-policy).

Nachfolgend finden Sie die Beispieleingaben und -ausgaben:

![Beispielausgabe XML in JSON](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltojson.png)

<a name="xml-text"></a>

### <a name="transform-xml-to-text"></a>Transformieren von XML in Text

Dies ist die Liquid-Vorlage, die für dieses Beispiel verwendet wird:

``` json
{{content.firstName | Append: ' ' | Append: content.lastName}}
```

Nachfolgend finden Sie die Beispieleingaben und -ausgaben:

![Beispielausgabe XML in Text](./media/logic-apps-enterprise-integration-liquid-transform/example-output-xmltotext.png)

## <a name="next-steps"></a>Nächste Schritte

* [Shopify Liquid-Sprachen und -Beispiele](https://shopify.github.io/liquid/basics/introduction/)
* [DotLiquid](http://dotliquidmarkup.org/)
* [DotLiquid – Online testen](http://dotliquidmarkup.org/try-online)
* [DotLiquid GitHub](https://github.com/dotliquid/dotliquid)
* [DotLiquid GitHub-Probleme](https://github.com/dotliquid/dotliquid/issues/)
* [Weitere Informationen zu Zuordnungen](../logic-apps/logic-apps-enterprise-integration-maps.md)
