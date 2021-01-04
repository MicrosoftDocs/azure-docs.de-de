---
title: Hinzufügen von Sprachanalysetools zu Zeichenfolgenfeldern
titleSuffix: Azure Cognitive Search
description: Mehrsprachige lexikalische Analyse für nicht englischsprachige Abfragen und Indizes in Azure Cognitive Search.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/05/2020
ms.openlocfilehash: e763dbd15ea443ad3c8f6295b37999c748db7e6c
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422330"
---
# <a name="add-language-analyzers-to-string-fields-in-an-azure-cognitive-search-index"></a>Hinzufügen von Sprachanalysetools zu Zeichenfolgenfeldern in einem Azure Cognitive Search-Index

Ein *Sprachanalysetool* ist eine bestimmte Art einer [Textanalyse](search-analyzers.md), die eine lexikalische Analyse mithilfe der linguistischen Regeln der Zielsprache durchführt. Jedes durchsuchbare Feld verfügt über die Eigenschaft **analyzer**. Wenn Ihr Inhalt aus übersetzten Zeichenfolgen besteht, wie z. B. separaten Feldern für englischen und chinesischen Text, können Sie für jedes Feld Sprachanalysetools angeben, um auf die umfangreichen linguistischen Funktionen dieser Sprachanalysetools zuzugreifen.

## <a name="when-to-use-a-language-analyzer"></a>Anwendungsfälle für Sprachanalysetools

Sie sollten ein Sprachanalysetool in Erwägung ziehen, wenn der Wert der Textanalyse durch Sensitivität für die Wort- oder Satzstruktur steigt. Ein häufiges Beispiel ist die Zuordnung unregelmäßiger Verbformen („bringt“ und „brachte“) oder Pluralformen („Sphinx“ und „Sphingen“). Ohne linguistische Sensitivität werden diese Zeichen nur nach ihren physischen Merkmalen analysiert, wodurch die Verbindung nicht erkannt wird. Da große Textabschnitte mit höherer Wahrscheinlichkeit solche Inhalte aufweisen, sind Felder, die Beschreibungen, Rezensionen oder Zusammenfassungen enthalten, gute Kandidaten für ein Sprachanalysetool.

Sie sollten Sprachanalysetools außerdem in Erwägung ziehen, wenn die Inhalte aus Zeichenfolgen in nicht westlichen Sprachen bestehen. Die [Standardanalyse](search-analyzers.md#default-analyzer) ist zwar nicht sprachsensitiv, das Konzept der Verwendung von Leerzeichen und Sonderzeichen (Bindestriche und Schrägstriche) zum Trennen von Zeichenfolgen lässt sich tendenziell aber eher auf westliche Sprachen als auch nicht westliche Sprachen anwenden. 

Beispielsweise ist im Chinesischen, Japanischen, Koreanischen (CJK) und anderen asiatischen Sprachen ein Leerzeichen nicht zwangsläufig ein Trennzeichen für Wörter. Sehen Sie sich die folgende japanische Zeichenfolge an. Da sie keine Leerzeichen aufweist, wird die gesamte Zeichenfolge von einem nicht sprachsensitiven Analysetool wahrscheinlich als ein Token analysiert, während es sich bei dieser Zeichenfolge tatsächlich um einen Satz handelt.

```
これは私たちの銀河系の中ではもっとも重く明るいクラスの球状星団です。
(This is the heaviest and brightest group of spherical stars in our galaxy.)
```

Für das Beispiel oben müsste eine erfolgreiche Abfrage das vollständige Token oder einen Teil des Tokens mit einem Suffix-Platzhalterzeichen enthalten, was zu einem unnatürlichen und eingeschränkten Suchverhalten führt.

Ein besseres Verhalten ergibt sich bei der Suche nach einzelnen Wörtern: 明るい (hell), 私たちの (unsere), 銀河系 (Galaxie). Durch die Verwendung eines der japanischen Analysetools, die in Cognitive Search zur Verfügung stehen, lässt sich dieses Verhalten leichter aufbrechen, da diese Analysetools besser dafür ausgerüstet sind, den Textabschnitt in aussagekräftige Wörter in der Zielsprache aufzuteilen.

## <a name="comparing-lucene-and-microsoft-analyzers"></a>Vergleich der Analysetools von Lucene und Microsoft

Azure Cognitive Search unterstützt 35 Sprachanalysetools auf Basis von Lucene und 50 Sprachanalysetools, die von Microsoft-Technologien zur Verarbeitung natürlicher Sprache unterstützt werden, die in Office und Bing zum Einsatz kommen.

Einige Entwickler bevorzugen möglicherweise die vertrautere, einfachere Open Source-Lösung von Lucene. Lucene-Sprachanalysetools sind schneller, während Microsoft-Sprachanalysetools erweiterte Funktionen bieten, wie z.B. Lemmatisierung, Wortzerlegung (in Sprachen wie Deutsch, Dänisch, Niederländisch, Schwedisch, Norwegisch, Estnisch, Finnisch, Ungarisch und Slowakisch) und Entitätserkennung (URLs, E-Mails, Datumsangaben und Zahlen). Vergleichen Sie nach Möglichkeit die Analyseprogramme von Microsoft und Lucene, um die für Ihre Anforderungen passendere Lösung zu ermitteln. 

Die Indizierung mit Analyseprogrammen von Microsoft dauert je nach Sprache durchschnittlich zwei bis drei Mal länger als mit entsprechenden Analyseprogrammen von Lucene. Die Suchleistung sollte bei durchschnittlich großen Abfragen nicht wesentlich eingeschränkt sein. 

### <a name="english-analyzers"></a>Analysetools für Englisch

Standardmäßig wird das Lucene-Standardanalysetool verwendet, das für Englisch gut funktioniert, vielleicht aber nicht ganz so gut wie das Analysetool für Englisch von Lucene oder das Analysetool für Englisch von Microsoft. 
 
+ Das Analysetool für Englisch von Lucene ist beispielsweise eine Erweiterung des Standardanalysetools. Es entfernt Possessivformen (nachgestelltes -s) bei Wörtern, wendet gemäß dem Wortstammerkennungsalgorithmus von Porter die Wortstammerkennung an und entfernt englische Stoppwörter.  

+ Das Analysetool für Englisch von Microsoft führt die Lemmatisierung anstelle der Wortstammerkennung durch. Dadurch können gebeugte und unregelmäßige Wortformen viel besser verarbeitet werden, was zu relevanteren Suchergebnissen führt. 

## <a name="configuring-analyzers"></a>Konfigurieren von Analysetools

Sprachanalysen werden in der vorliegenden Form verwendet. Sie können für die Eigenschaft **analyzer** aller Felder in der Indexdefinition den Namen eines Analyseprogramms festlegen, das die Sprache und den Linguistikstapel (Microsoft oder Lucene) angibt. Bei der Indizierung dieses Felds und der Suche danach wird das gleiche Analyseprogramm verwendet. Beispiel: Sie können im selben Index separate Felder für englische, französische und spanische Hotelbeschreibungen verwenden.

> [!NOTE]
> Es ist nicht möglich, für ein Feld zur Indizierungszeit eine andere Sprachanalyse als zur Abfragezeit zu verwenden. Diese Funktion ist für [benutzerdefinierte Analysen](index-add-custom-analyzers.md) reserviert. Wenn Sie daher versuchen, die Eigenschaften **searchAnalyzer** oder **indexAnalyzer** auf den Namen einer Sprachanalyse festzulegen, gibt die REST-API eine Fehlerantwort zurück. Sie müssen stattdessen die **analyzer**-Eigenschaft verwenden.

Verwenden Sie den Abfrageparameter **searchFields**, um anzugeben, für welches sprachspezifische Feld in den Abfragen gesucht werden soll. Beispiele für Abfragen mit der Eigenschaft „analyzer“ finden Sie unter [Search Documents (Azure Search Service REST API)](/rest/api/searchservice/search-documents) (Durchsuchen von Dokumenten (REST-API des Azure Search-Diensts)). 

Weitere Informationen zu Indexeigenschaften finden Sie unter [Erstellen eines Index &#40;REST-API für die kognitive Azure-Suche&#41;](/rest/api/searchservice/create-index). Weitere Informationen zur Analyse in der kognitiven Azure-Suche finden Sie unter [Analysetools in der kognitiven Azure-Suche](./search-analyzers.md).

<a name="language-analyzer-list"></a>

## <a name="language-analyzer-list"></a>Liste der Sprachanalysen 
 Es folgt die Liste der unterstützten Sprachen sowie die Namen der Lucene- und Microsoft-Analyseprogramme.  

|Sprache|Name des Microsoft-Analysetools|Name des Lucene-Analysetools|  
|--------------|-----------------------------|--------------------------|  
|Arabisch|ar.microsoft|ar.lucene|  
|Armenisch||hy.lucene|  
|Bengalisch|bn.microsoft||  
|Baskisch||eu.lucene|  
|Bulgarisch|bg.microsoft|bg.lucene|  
|Katalanisch|ca.microsoft|ca.lucene|  
|Chinesisch (vereinfacht)|zh-Hans.microsoft|zh-Hans.lucene|  
|Chinesisch (traditionell)|zh-Hant.microsoft|zh-Hant.lucene|  
|Kroatisch|hr.microsoft||  
|Tschechisch|cs.microsoft|cs.lucene|  
|Dänisch|da.microsoft|da.lucene|  
|Niederländisch|nl.microsoft|nl.lucene|  
|Englisch|en.microsoft|en.lucene|  
|Estnisch|et.microsoft||  
|Finnisch|fi.microsoft|fi.lucene|  
|Französisch|fr.microsoft|fr.lucene|  
|Galizisch||gl.lucene|  
|Deutsch|de.microsoft|de.lucene|  
|Griechisch|el.microsoft|el.lucene|  
|Gujarati|gu.microsoft||  
|Hebräisch|he.microsoft||  
|Hindi|hi.microsoft|hi.lucene|  
|Ungarisch|hu.microsoft|hu.lucene|  
|Isländisch|is.microsoft||  
|Indonesisch (Bahasa)|id.microsoft|id.lucene|  
|Irisch||ga.lucene|  
|Italienisch|it.microsoft|it.lucene|  
|Japanisch|ja.microsoft|ja.lucene|  
|Kannada|kn.microsoft||  
|Koreanisch|ko.microsoft|ko.lucene|  
|Lettisch|lv.microsoft|lv.lucene|  
|Litauisch|lt.microsoft||  
|Malayalam|ml.microsoft||  
|Malaiisch (Lateinisch)|ms.microsoft||  
|Marathi|mr.microsoft||  
|Norwegisch|nb.microsoft|no.lucene|  
|Persisch||fa.lucene|  
|Polnisch|pl.microsoft|pl.lucene|  
|Portugiesisch (Brasilien)|pt-Br.microsoft|pt-Br.lucene|  
|Portugiesisch (Portugal)|pt-Pt.microsoft|pt-Pt.lucene|  
|Pandschabi|pa.microsoft||  
|Rumänisch|ro.microsoft|ro.lucene|  
|Russisch|ru.microsoft|ru.lucene|  
|Serbisch (Kyrillisch)|sr-cyrillic.microsoft||  
|Serbisch (Lateinisch)|sr-latin.microsoft||  
|Slowakisch|sk.microsoft||  
|Slowenisch|sl.microsoft||  
|Spanisch|es.microsoft|es.lucene|  
|Schwedisch|sv.microsoft|sv.lucene|  
|Tamilisch|ta.microsoft||  
|Telugu|te.microsoft||  
|Thailändisch|th.microsoft|th.lucene|  
|Türkisch|tr.microsoft|tr.lucene|  
|Ukrainisch|uk.microsoft||  
|Urdu|ur.microsoft||  
|Vietnamesisch|vi.microsoft||  

 Alle Analysetools mit **Lucene** im Namen werden von den [Sprachanalysetools von Apache Lucene](https://lucene.apache.org/core/6_6_1/core/overview-summary.html ) unterstützt.

## <a name="see-also"></a>Weitere Informationen  

+ [Erstellen eines Index &#40;REST-API für die kognitive Azure-Suche&#41;](/rest/api/searchservice/create-index)  

+ [LexicalAnalyzerName-Klasse](/dotnet/api/azure.search.documents.indexes.models.lexicalanalyzername)