---
title: Interagieren mit Berichten mithilfe der JavaScript-API | Microsoft Docs
description: Mithilfe der Power BI-JavaScript-API können Sie mühelos Power BI-Berichte in Ihre Anwendungen einbetten.
services: power-bi-workspace-collections
ms.service: power-bi-workspace-collections
author: rkarlin
ms.author: rkarlin
ms.topic: conceptual
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: 252296af8b2065ae22bed8b421d4d00718b78287
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64705513"
---
# <a name="interact-with-power-bi-reports-using-the-javascript-api"></a>Interagieren mit Power BI-Berichten mithilfe der JavaScript-API

Mithilfe der Power BI-JavaScript-API können Sie mühelos Power BI-Berichte in Ihre Anwendungen einbetten. Mit der API können die Anwendungen programmgesteuert mit anderen Berichtselementen wie Seiten und Filtern interagieren. Diese Interaktivität macht Power BI-Berichte zu einem integrierten Bestandteil Ihrer Anwendung.

> [!IMPORTANT]
> Power BI-Arbeitsbereichssammlungen sind veraltet und nur noch bis Juni 2018 (oder bis zum Termin in Ihrem Vertrag) verfügbar. Es wird empfohlen, die Migration zu Power BI Embedded zu planen, um Unterbrechungen für Ihre Anwendung zu vermeiden. Informationen zur Migration Ihrer Daten zu Power BI Embedded finden Sie unter [Migrieren von Inhalten aus Power BI-Arbeitsbereichssammlungen zu Power BI Embedded](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

Zum Einbetten eines Power BI-Berichts in Ihre Anwendung verwenden Sie einen IFrame, der als Teil der Anwendung gehostet wird. Der IFrame fungiert als Grenze zwischen Ihrer Anwendung und dem Bericht, wie in der folgenden Abbildung zu sehen:

![IFrame für Power BI-Arbeitsbereichssammlung ohne JavaScript-API](media/interact-with-reports/iframe-without-javacript.png)

Der IFrame erleichtert den Einbettungsprozess erheblich, ohne die JavaScript-API können der Bericht und die Anwendung jedoch nicht miteinander interagieren. Durch diese mangelnde Interaktion kann der Eindruck entstehen, dass der Bericht eigentlich kein Teil der Anwendung ist. Der Bericht und die Anwendung müssen wie in der folgenden Abbildung gezeigt in beiden Richtungen miteinander kommunizieren:

![IFrame für Power BI-Arbeitsbereichssammlung mit JavaScript-API](media/interact-with-reports/iframe-with-javascript.png)

Die Power BI-JavaScript-API ermöglicht Ihnen das Schreiben von Code, der die IFrame-Grenze sicher passieren kann. Dadurch kann die Anwendung programmgesteuert eine Aktion in einem Bericht ausführen und auf Ereignisse von Aktionen lauschen, die Benutzer innerhalb des Berichts durchführen.

## <a name="what-can-you-do-with-the-power-bi-javascript-api"></a>Welche Möglichkeiten bietet Ihnen die Power BI-JavaScript-API?

Mit der JavaScript-API können Sie Berichte verwalten, zu Seiten in einem Bericht navigieren, einen Bericht filtern und Einbettungsereignisse behandeln. Das folgende Diagramm zeigt die Struktur der API.

![Power BI-JavaScript-API – Diagramm](media/interact-with-reports/javascript-api-diagram.png)

### <a name="manage-reports"></a>Verwalten von Berichten
Mit der JavaScript-API können Sie das Verhalten auf der Berichts- und Seitenebene verwalten:

* Sicheres Einbetten eines bestimmten Power BI-Berichts in die Anwendung (testen Sie die [Demoanwendung zum Einbetten](https://azure-samples.github.io/powerbi-angular-client/#/scenario1)
  * Festlegen des Zugriffstokens
* Konfigurieren des Berichts
  * Aktivieren und Deaktivieren der Filter- und Seitennavigationsbereiche (testen Sie die [Demoanwendung zum Aktualisieren von Einstellungen](https://azure-samples.github.io/powerbi-angular-client/#/scenario6)
  * Festlegen von Standardeinstellungen für Seiten und Filter (testen Sie die [Demo zu Standardeinstellungen](https://azure-samples.github.io/powerbi-angular-client/#/scenario5)
* Aktivieren und Deaktivieren des Vollbildmodus

[Weitere Informationen zum Einbetten eines Berichts](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embedding-Basics)

### <a name="navigate-to-pages-in-a-report"></a>Navigieren zu Seiten in einem Bericht
Mit der JavaScript-API können Sie alle Seiten in einem Bericht ermitteln und die aktuelle Seite festlegen. Testen Sie die [Demoanwendung zur Navigation](https://azure-samples.github.io/powerbi-angular-client/#/scenario3).

[Weitere Informationen zur Seitennavigation](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Page-Navigation)

### <a name="filter-a-report"></a>Filtern eines Berichts
Die JavaScript-API bietet einfache und erweiterte Filterfunktionen für eingebettete Berichte und Berichtsseiten. Testen Sie die [Demoanwendung zum Filtern](https://azure-samples.github.io/powerbi-angular-client/#/scenario4), und sehen Sie sich einige einführende Codebeispiele an.

#### <a name="basic-filters"></a>Einfache Filter
Ein einfacher Filter wird auf eine Spalte oder Hierarchieebene angewendet und enthält eine Liste von Werten, die ein- oder ausgeschlossen werden sollen.

```typescript
const basicFilter: pbi.models.IBasicFilter = {
  $schema: "https://powerbi.com/product/schema#basic",
  target: {
    table: "Store",
    column: "Count"
  },
  operator: "In",
  values: [1,2,3,4]
}
```

#### <a name="advanced-filters"></a>Erweiterte Filter
Erweiterte Filter verwenden den logischen Operator AND oder OR und akzeptieren eine oder zwei Bedingungen, die jeweils über einen eigenen Operator und Wert verfügen. Folgende Bedingungen werden unterstützt:

* Keine
* LessThan
* LessThanOrEqual
* GreaterThan
* GreaterThanOrEqual
* Contains
* DoesNotContain
* StartsWith
* DoesNotStartWith
* Is
* IsNot
* IsBlank
* IsNotBlank

```typescript
const advancedFilter: pbi.models.IAdvancedFilter = {
  $schema: "https://powerbi.com/product/schema#advanced",
  target: {
    table: "Store",
    column: "Name"
  },
  logicalOperator: "Or",
  conditions: [
    {
      operator: "Contains",
      value: "Wash"
    },
    {
      operator: "Contains",
      value: "Park"
    }
  ]
}
```

[Weitere Informationen zum Filtern](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Filters)

### <a name="handling-events"></a>Behandeln von Ereignissen

Ihre Anwendung kann nicht nur Informationen in den IFrame senden, sondern auch Informationen zu den folgenden Ereignissen aus dem IFrame empfangen:

* Einbetten
  * loaded
  * error
* Berichte
  * pageChanged
  * dataSelected (demnächst verfügbar)

[Weitere Informationen zur Behandlung von Ereignissen](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Handling-Events)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Power BI-JavaScript-API finden Sie unter folgenden Links:

* [JavaScript-API-Wiki](https://github.com/Microsoft/PowerBI-JavaScript/wiki)
* [Objektmodellreferenz](https://microsoft.github.io/powerbi-models/modules/_models_.html)
* [Livedemo](https://microsoft.github.io/PowerBI-JavaScript/demo/)