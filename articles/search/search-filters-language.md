---
title: Sprachfilter für eine mehrsprachige Inhalte in einem Suchindex – Azure Search
description: Filterkriterien zur Unterstützung der mehrsprachigen Suche, wobei der Gültigkeitsbereich der Suchabfrage auf sprachspezifische Felder festgelegt wird.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 10/23/2017
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 695fdfba1573ff97b05f8e8b50a05bef9dbf48de
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61289614"
---
# <a name="how-to-filter-by-language-in-azure-search"></a>Filtern nach Sprache in Azure Search 

In einer mehrsprachigen Suchanwendung ist es wichtig, nach Ergebnissen in der Sprache des Benutzers suchen und entsprechende Ergebnisse abrufen zu können. In Azure Search kann hierzu beispielsweise eine Reihe von Feldern für die Speicherung von Zeichenfolgen in einer bestimmten Sprache erstellt und die Volltextsuche dann bei der Abfrage auf diese Felder beschränkt werden.

Mithilfe von Abfrageparametern in der Anforderung wird der Gültigkeitsbereich der Suchabfrage festgelegt, und Ergebnisse aus Feldern, deren Inhalt nicht der gewünschten Sucherfahrung entspricht, werden ignoriert.

| Parameter | Zweck |
|-----------|--------------|
| **searchFields** | Grenzt die Volltextsuche auf die Liste der angegebenen Felder ein. |
| **$select** | Kürzt die Antwort, sodass nur die von Ihnen angegebenen Felder berücksichtigt werden. Standardmäßig werden alle abrufbaren Felder zurückgegeben. Mit dem Parameter **$select** können Sie auswählen, welche Felder berücksichtigt werden sollen. |

Der Erfolg dieser Methode hängt von der Integrität der Feldinhalte ab. Azure Search übersetzt keine Zeichenfolgen und führt auch keine Spracherkennung durch. Sie müssen selbst dafür sorgen, dass die Felder die erwarteten Zeichenfolgen enthalten.

## <a name="define-fields-for-content-in-different-languages"></a>Definieren von Feldern für Inhalt in verschiedenen Sprachen

In Azure Search sind Abfragen auf einen einzelnen Index ausgerichtet. Entwickler, die sprachspezifische Zeichenfolgen in einer einzelnen Suchumgebung bereitstellen möchten, definieren üblicherweise dedizierte Felder, um die Werte zu speichern – also ein Feld für englische Zeichenfolgen, ein Feld für Französisch und so weiter. 

In unseren Beispielen (etwa im [Immobilienbeispiel](search-get-started-portal.md) weiter unten) sind Ihnen wahrscheinlich bereits Felddefinitionen wie auf dem folgenden Screenshot aufgefallen. Dieses Beispiel zeigt die Sprachanalysezuweisungen für die Felder in diesem Index. Aus Leistungsgründen empfiehlt es sich, Felder mit Textinhalt bei der Volltextsuche mit einer Analysekomponente zu kombinieren, die die linguistischen Regeln der Zielsprache behandelt.

  ![](./media/search-filters-language/lang-fields.png)

> [!Note]
> Codebeispiele zur Veranschaulichung von Felddefinitionen mit Sprachanalysen finden Sie unter [Definieren eines Index (.NET)](https://docs.microsoft.com/azure/search/search-create-index-dotnet) sowie unter [Definieren eines Index (REST)](search-create-index-rest-api.md).

## <a name="build-and-load-an-index"></a>Erstellen und Laden eines Index

Ein (wahrscheinlich naheliegender) Zwischenschritt vor der Erstellung einer Abfrage ist das [Erstellen und Auffüllen des Index](https://docs.microsoft.com/azure/search/search-create-index-dotnet). Er wird daher nur der Vollständigkeit halber erwähnt. Die Verfügbarkeit des Index kann unter anderem durch Überprüfen der Indexliste im [Portal](https://portal.azure.com) ermittelt werden.

## <a name="constrain-the-query-and-trim-results"></a>Einschränken der Abfrage und Ausschließen von Ergebnissen

Abfrageparameter dienen dazu, die Suche auf bestimmte Felder einzugrenzen und anschließend die Ergebnisse von Feldern auszuschließen, die für Ihr Szenario nicht relevant sind. Wenn Sie die Suche also beispielsweise auf Felder mit französischen Zeichenfolgen einschränken möchten, richten Sie die Abfrage mithilfe von **searchFields** auf Felder aus, die Zeichenfolgen in dieser Sprache enthalten. 

Standardmäßig werden bei einer Suche alle als abrufbar markierten Felder zurückgegeben. Es empfiehlt sich daher, Felder auszuschließen, die für die angestrebte sprachspezifische Sucherfahrung nicht geeignet sind. Wenn Sie die Suche auf ein Feld mit französischen Zeichenfolgen eingeschränkt haben, möchten Sie wahrscheinlich Felder mit englischen Zeichenfolgen aus Ihren Ergebnissen ausschließen. Mithilfe des Abfrageparameters **$select** können Sie steuern, welche Felder an die aufrufende Anwendung zurückgegeben werden sollen.

```csharp
parameters =
    new SearchParameters()
    {
        searchFields = "description_fr" 
        Select = new[] { "description_fr"  }
    };
```
> [!Note]
> Die Abfrage enthält zwar kein $filter-Argument, der Anwendungsfall ist jedoch eng mit Filterkonzepten verwandt, weshalb wir ihn als Filterszenario präsentieren.

## <a name="see-also"></a>Weitere Informationen

+ [Filter in Azure Search](search-filters.md)
+ [Sprachanalysen](https://docs.microsoft.com/rest/api/searchservice/language-support)
+ [Funktionsweise der Volltextsuche in Azure Search](search-lucene-query-architecture.md)
+ [Search Documents (Azure Search Service REST API)](https://docs.microsoft.com/rest/api/searchservice/search-documents) (Suchen nach Dokumenten (Azure Search Service-REST-API))

