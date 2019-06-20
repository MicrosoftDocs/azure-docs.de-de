---
title: Verwenden von benannten Werten in Azure API Management-Richtlinien
description: Erfahren Sie, wie benannte Werte in Azure API Management-Richtlinien verwendet werden.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2018
ms.author: apimpm
ms.openlocfilehash: 9e1b1953520c5502668fbbae70a37a140253b035
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241693"
---
# <a name="how-to-use-named-values-in-azure-api-management-policies"></a>Verwenden von benannten Werten in Azure API Management-Richtlinien
API Management-Richtlinien sind eine leistungsfähige Funktion des Systems, mit der das Azure-Portal das Verhalten der API über eine Konfiguration ändern kann. Richtlinien sind eine Sammlung von Anweisungen, die sequenziell bei Anfragen oder Antworten einer API ausgeführt werden. Richtlinienanweisungen können mithilfe von literalen Textwerten, Richtlinienausdrücken und benannten Werten erstellt werden. 

Jede API Management-Dienstinstanz weist eine Eigenschaftensammlung von Schlüssel-Wert-Paaren auf, die als benannte Werte bezeichnet werden und die für die gesamte Dienstinstanz gelten. Diese benannten Werte können zum Verwalten konstanter Zeichenfolgenwerte für alle API-Konfigurationen und -Richtlinien verwendet werden. Jede Eigenschaft kann über folgende Attribute verfügen:

| Attribut | Type | BESCHREIBUNG |
| --- | --- | --- |
| `Display name` |Zeichenfolge |Alphanumerische Zeichenfolge zum Verweis auf die Eigenschaft in den Richtlinien |
| `Value`        |Zeichenfolge |Der Wert der Eigenschaft. Er darf nicht leer sein oder nur aus Leerzeichen bestehen. |
| `Secret`       |boolean|Bestimmt, ob der Wert ein geheimer Schlüssel ist und ob er verschlüsselt werden sollte.|
| `Tags`         |Array von Zeichenfolgen |Optionale Tags, die zum Filtern der Eigenschaftenliste verwendet werden können, wenn sie bereitgestellt werden. |

![Benannte Werte](./media/api-management-howto-properties/named-values.png)

Eigenschaftswerte können Literalzeichenfolgen und [Richtlinienausdrücke](/azure/api-management/api-management-policy-expressions)enthalten. Der Wert von `ExpressionProperty` ist beispielsweise ein Richtlinienausdruck, der eine Zeichenfolge zurückgibt, die das aktuelle Datum und die Uhrzeit enthält. Die Eigenschaft `ContosoHeaderValue` ist als geheimer Schlüssel markiert, sodass ihr Wert nicht angezeigt wird.

| NAME | Wert | `Secret` | `Tags` |
| --- | --- | --- | --- |
| ContosoHeader |TrackingId |False |Contoso |
| ContosoHeaderValue |•••••••••••••••••••••• |True |Contoso |
| ExpressionProperty |@(DateTime.Now.ToString()) |False | |

## <a name="to-add-and-edit-a-property"></a>So fügen Sie eine Eigenschaft hinzu und bearbeiten sie

![Hinzufügen einer Eigenschaft](./media/api-management-howto-properties/add-property.png)

1. Wählen Sie unter **API MANAGEMENT** die Option **APIs** aus.
2. Klicken Sie auf **Benannte Werte**.
3. Klicken Sie auf **+ Hinzufügen**.

   „Name“ und „Wert“ sind erforderliche Werte. Wenn dieser Eigenschaftswert ein Geheimnis ist, aktivieren Sie das Kontrollkästchen „Dies ist ein Geheimnis.“. Geben Sie einen oder mehrere optionale Tags ein, um Ihre benannten Werte besser organisieren zu können, und klicken Sie auf „Speichern“.
4. Klicken Sie auf **Create**.

Sobald die Eigenschaft erstellt wurde, können Sie sie bearbeiten, indem Sie darauf klicken. Wenn Sie den Namen der Eigenschaft ändern, werden alle Richtlinien, die auf diese Eigenschaft verweisen, automatisch aktualisiert, sodass sie den neuen Namen verwenden.

Informationen zum Bearbeiten einer Eigenschaft mithilfe der REST-API finden Sie unter [Bearbeiten einer Eigenschaft mit der REST-API](/rest/api/apimanagement/2019-01-01/property?patch).

## <a name="to-delete-a-property"></a>So löschen Sie eine Eigenschaft

Klicken Sie zum Löschen einer Eigenschaft neben der zu löschenden Eigenschaft auf **Löschen** .

> [!IMPORTANT]
> Wenn von Richtlinien auf die Eigenschaft verwiesen wird, können Sie sie erst erfolgreich löschen, nachdem Sie die Eigenschaft aus allen Richtlinien entfernt haben, die sie verwenden.
> 
> 

Informationen zum Löschen einer Eigenschaft mithilfe der REST-API finden Sie unter [Löschen einer Eigenschaft mit der REST-API](/rest/api/apimanagement/2019-01-01/property/delete).

## <a name="to-search-and-filter-named-values"></a>So suchen und filtern Sie benannte Werte:

Die Registerkarte **Benannte Werte** umfasst Such- und Filterfunktionen, die Ihnen beim Verwalten Ihrer benannten Werte helfen. Geben Sie zum Filtern der Eigenschaftenliste nach einem Eigenschaftennamen einen Suchbegriff in das Textfeld **Eigenschaft suchen** ein. Wenn Sie alle benannten Werte anzeigen möchten, löschen Sie den Inhalt des Textfelds **Eigenschaft suchen**, und drücken Sie die Eingabetaste.

Geben Sie zum Filtern der Eigenschaftenliste nach Tagwerten ein oder mehrere Tags in das Textfeld **Nach Tags filtern** ein. Wenn Sie alle benannten Werte anzeigen möchten, löschen Sie den Inhalt des Textfelds **Nach Tags filtern**, und drücken Sie die Eingabetaste.

## <a name="to-use-a-property"></a>So verwenden Sie eine Eigenschaft

Um eine Eigenschaft in einer Richtlinie zu verwenden, setzen Sie den Namen der Eigenschaft in ein doppeltes Paar geschweifter Klammern (z.B. `{{ContosoHeader}}`), wie im folgenden Beispiel gezeigt:

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

In diesem Beispiel dient `ContosoHeader` als Name eines Headers in einer `set-header`-Richtlinie und `ContosoHeaderValue` als Wert dieses Headers. Wenn diese Richtlinie in einer Anforderung oder Antwort an das API Management-Gateway ausgewertet wird, werden `{{ContosoHeader}}` und `{{ContosoHeaderValue}}` durch ihre jeweiligen Eigenschaftswerte ersetzt.

Benannte Werte können als vollständige Attribut- oder Elementwerte verwendet werden, wie im vorherigen Beispiel gezeigt, sie können aber auch in einen literalen Textausdruck eingefügt oder mit einem Teil davon kombiniert werden, wie im folgenden Beispiel gezeigt wird: `<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

Benannte Werte können auch Richtlinienausdrücke enthalten. Im folgenden Beispiel wird `ExpressionProperty` verwendet.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

Wenn diese Richtlinie ausgewertet wird, wird `{{ExpressionProperty}}` durch ihren Wert ersetzt: `@(DateTime.Now.ToString())`. Da der Wert ein Richtlinienausdruck ist, wird der Ausdruck ausgewertet, und die Ausführung der Richtlinie wird fortgesetzt.

Sie können dies im Entwicklerportal durch Aufrufen eines Vorgangs testen, der eine Richtlinie mit entsprechenden benannten Werten aufweist. Im folgenden Beispiel wird ein Vorgang mit den beiden vorherigen `set-header`-Beispielrichtlinien mit benannten Werten aufgerufen. Beachten Sie, dass die Antwort zwei benutzerdefinierte Header enthält, die mithilfe von Richtlinien mit benannten Werten konfiguriert wurden.

![Entwicklerportal][api-management-send-results]

Beim Betrachten der [Verfolgung mit dem API-Inspektor](api-management-howto-api-inspector.md) für einen Aufruf, der die beiden vorherigen Beispielrichtlinien mit benannten Werten enthält, sehen Sie beide `set-header`-Richtlinien mit den eingefügten Eigenschaftswerten und die Auswertung des Richtlinienausdrucks für die Eigenschaft, die den Richtlinienausdruck enthält.

![Verfolgung mit dem API-Inspektor][api-management-api-inspector-trace]

Eigenschaftswerte können zwar Richtlinienausdrücke enthalten, Eigenschaftswerte können jedoch keine anderen benannten Werte enthalten. Wenn Text, der einen Eigenschaftsverweis enthält, als Eigenschaftswert verwendet wird, z. B. `Property value text {{MyProperty}}`, wird dieser Eigenschaftsverweis nicht ersetzt, und er wird als Bestandteil des Eigenschaftswerts eingebunden.

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zum Arbeiten mit Richtlinien
  * [Richtlinien in Azure API Management](api-management-howto-policies.md)
  * [Richtlinienreferenz](/azure/api-management/api-management-policies)
  * [Richtlinienausdrücke](/azure/api-management/api-management-policy-expressions)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png

