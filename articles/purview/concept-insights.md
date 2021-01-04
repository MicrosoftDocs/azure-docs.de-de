---
title: Grundlegendes zu Erkenntnisse-Berichten in Azure Purview
description: In diesem Artikel werden die Erkenntnisse in Azure Purview erläutert.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 12/02/2020
ms.openlocfilehash: d841fa336b2702a02f3215f97a6403217986d7e0
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96550859"
---
# <a name="understand-insights-in-azure-purview"></a>Grundlegendes zu Erkenntnissen in Azure Purview

Dieser Artikel bietet eine Übersicht über das Erkenntnisse-Feature in Azure Purview.

Erkenntnisse zählen zu den tragenden Säulen von Purview. Diese Funktion bietet Kunden eine einzelne Ansicht in ihrem Katalog und soll darüber hinaus Datenquellenadministratoren, Geschäftsbenutzern, Data Stewards, Data Officers und Sicherheitsadministratoren spezifische Erkenntnisse vermitteln. Zurzeit bietet Purview die folgenden Erkenntnisse-Berichte, die Kunden in der öffentlichen Vorschau zur Verfügung gestellt werden.

## <a name="asset-insights"></a>Ressourcen-Erkenntnisse

Dieser Bericht zeigt Ihre Datenumgebung aus der Vogelperspektive und unterteilt sie beispielsweise nach Dimensionen wie Quelltyp, Klassifizierung und Dateigröße. Dieser Bericht ist für unterschiedliche Benutzertypen bestimmt, die das Purview-Konto verwalten und Überprüfungen durchführen könnten, oder Geschäftsbenutzer, die vielleicht wissen möchten, wie viele Ressourcen mit einer bestimmten Klassifizierung in der Datenumgebung Ihrer Organisation vorhanden sind. 

Der Bericht bietet umfassende Erkenntnisse mittels Diagrammen und KPIs und geht später ausführlich auf bestimmte Anomalien wie z. B. falsch platzierte Dateien ein. Außerdem unterstützt der Bericht auch eine komfortable End-to-End-Benutzeroberfläche, in der Kunden die Anzahl von Ressourcen mit einer bestimmten Klassifizierung anzeigen, Informationen nach Quelltypen und obersten Ordnern aufschlüsseln und auch die Liste der Ressourcen zur weiteren Untersuchung anzeigen können.

## <a name="scan-insights"></a>Scan-Erkenntnisse

Der Bericht ermöglicht Administratoren, die allgemeine Integrität der Scans nachzuvollziehen – wie viele erfolgreich waren, bei wie vielen Fehler aufgetreten sind, wie viele abgebrochen wurden. Dieser Bericht liefert ein Statusupdate für Scans, die innerhalb der letzten sieben Tage oder letzten 30 Tage im Purview-Konto ausgeführt wurden.

Außerdem können Administratoren anhand des Berichts eingehend untersuchen, bei welchen Scans bei welchen spezifischen Quelltypen Fehler aufgetreten sind. Um den Benutzern weitere Untersuchungen zu ermöglichen, unterstützt der Bericht sie bei der Navigation in der Scanverlaufseite in der Benutzeroberfläche „Quellen“.

## <a name="glossary-insights"></a>Glossarerkenntnisse

Dieser Bericht liefert Geschäftsbenutzern und Data Stewards einen Statusbericht zum Glossar. Benutzer können diesen Bericht anzeigen, um die Verteilung von Glossarbegriffen nach Status zu verstehen, und zu erfahren, wie viele Glossarbegriffe Ressourcen angefügt sind, und wie viele noch nicht. Geschäftsbenutzer können sich auch über die Vollständigkeit ihrer Glossarbegriffe informieren. 

Dieser Bericht fasst die wichtigsten Elemente zusammen, auf die sich ein Geschäftsbenutzer oder Data Steward konzentrieren muss, um ein umfassendes und brauchbares Glossar für seine Organisation zu erstellen. Benutzer können auch von der „Glossarerkenntnisse“-Umgebung zur „Glossar“-Umgebung navigieren, um Änderungen an einem bestimmten Glossarbegriff vorzunehmen.

## <a name="classification-insights"></a>Klassifizierungs-Erkenntnisse

Dieser Bericht enthält Details zum Speicherort klassifizierter Daten, zu den während eines Scans gefundenen Klassifizierungen und einen Drilldown zu den klassifizierten Dateien selbst. Er ermöglicht Sicherheitsadministratoren, die in der Datenumgebung Ihrer Organisation gefundenen Arten von Informationen zu verstehen. 

In Azure Purview ähneln Klassifizierungen Betrefftags und dienen zum Kennzeichnen und Identifizieren von Inhalten eines bestimmten Typs in Ihrer Datenumgebung.

Identifizieren Sie anhand des Berichts „Klassifizierungs-Erkenntnisse“ Inhalte mit bestimmten Klassifizierungen, und verstehen Sie erforderliche Aktionen wie z. B. das Erhöhen der Sicherheit der Repositorys oder das Verschieben von Inhalten an einen sichereren Ort.

Weitere Informationen finden Sie unter [Klassifizierungs-Erkenntnisse zu Ihren Daten von Azure Purview](classification-insights.md).

## <a name="sensitivity-labeling-insights"></a>Vertraulichkeitsbezeichnungs-Erkenntnisse

Dieser Bericht enthält Details zu den Vertraulichkeitsbezeichnungen, die während eines Scans gefunden werden, sowie einen Drilldown zu den bezeichneten Dateien selbst. Er ermöglicht Sicherheitsadministratoren, die Sicherheit der in der Datenumgebung Ihrer Organisation gefundenen Informationen sicherzustellen. 

In Azure Purview werden Vertraulichkeitsbezeichnungen verwendet, um die Klassifizierungstypkategorien sowie die Gruppensicherheitsrichtlinien zu ermitteln, die Sie auf die einzelnen Kategorien anwenden möchten.

Verwenden Sie den Bericht „Vertraulichkeitsbezeichnungs-Erkenntnisse“, um die in ihren Inhalten gefundenen Vertraulichkeitsbezeichnungen zu identifizieren und erforderliche Aktionen wie das Verwalten des Zugriffs auf bestimmte Repositorys oder Dateien zu verstehen.

Weitere Informationen finden Sie unter [Vertraulichkeitsbezeichnungs-Erkenntnisse zu Ihren Daten in Azure Purview](sensitivity-insights.md).

## <a name="file-extension-insights"></a>Dateierweiterungs-Erkenntnisse

Dieser Bericht enthält Details zu den während eines Scans gefundenen Dateierweiterungen oder Dateitypen sowie einen Drilldown zu den Dateien selbst. 

Verwenden Sie den Bericht „Dateierweiterungs-Erkenntnisse“, um zu verstehen, wie viele Dateien jedes Typs Sie besitzen, wo sich diese Dateien befinden und ob sie auf vertrauliche Daten gescannt werden können.

Weitere Informationen finden Sie unter [Dateierweiterungs-Erkenntnisse zu Ihren Daten von Azure Purview](file-extension-insights.md).

## <a name="next-steps"></a>Nächste Schritte

* [Glossarerkenntnisse](glossary-insights.md)
* [Scan-Erkenntnisse](scan-insights.md)
* [Klassifizierungs-Erkenntnisse](./classification-insights.md)