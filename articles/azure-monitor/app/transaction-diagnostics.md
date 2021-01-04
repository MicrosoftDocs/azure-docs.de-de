---
title: Azure Application Insights-Transaktionsdiagnose | Microsoft-Dokumentation
description: Application Insights-End-to-End-Transaktionsdiagnose
ms.topic: conceptual
ms.date: 01/19/2018
ms.reviewer: sdash
ms.openlocfilehash: 7623b7131e6344a67c468d0436884ebfef9b0058
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96746096"
---
# <a name="unified-cross-component-transaction-diagnostics"></a>Einheitliche komponentenübergreifende Transaktionsdiagnose

Die einheitliche Diagnoseoberfläche setzt die serverseitige Telemetrie übergreifend von allen Ihren mithilfe von Application Insights überwachten Komponenten automatisch in einer einzelnen Ansicht in Beziehung. Es spielt keine Rolle, wenn Sie über mehrere Ressourcen mit separaten Instrumentierungsschlüsseln verfügen. Application Insights erkennt die zugrundeliegende Beziehung und ermöglicht Ihnen die komfortable Diagnose der Anwendungskomponente, Abhängigkeit oder Ausnahme, die zur langsamen Ausführung oder einem Fehler bei einer Transaktion geführt hat.

## <a name="what-is-a-component"></a>Was ist eine Komponente?

Komponenten sind unabhängig bereitstellbare Teile Ihrer verteilten Anwendung/Microserviceanwendung. Entwickler und Betriebsteams verfügen über Sichtbarkeit oder Zugriff für die von diesen Anwendungskomponenten generierte Telemetrie auf Codeebene.

* Komponenten unterscheiden sich von „beobachteten“ externen Abhängigkeiten, wie SQL, EventHub usw., auf die Ihr Team/Ihre Organisation möglicherweise keinen Zugriff hat (Code oder Telemetrie).
* Komponenten können in einer beliebigen Anzahl von Server-/Rollen-/Containerinstanzen ausgeführt werden.
* Komponenten können separate Application Insights-Instrumentierungsschlüssel (sogar in verschiedenen Abonnements) oder verschiedene Rollen aufweisen, die alle an einen einzelnen Application Insights-Instrumentierungsschlüssel berichten. Auf der neuen Benutzeroberfläche werden Details übergreifend über alle Komponenten angezeigt, unabhängig von deren Einrichtung.

> [!NOTE]
> * **Fehlen die Links von verknüpften Elementen?** Die gesamte verknüpfte Telemetrie findet sich in den Abschnitten [oben](#cross-component-transaction-chart) und [unten](#all-telemetry-with-this-operation-id) auf der linken Seite. 

## <a name="transaction-diagnostics-experience"></a>Oberfläche zur Transaktionsdiagnose
Diese Ansicht ist in vier Hauptteile gegliedert: eine Ergebnisliste, ein komponentenübergreifendes Transaktionsdiagramm, eine Liste der Zeitabfolge für die gesamte Telemetrie für diesen Vorgang und den Detailbereich für ein bestimmtes auf der linken Seite ausgewähltes Telemetrieelement.

![Hauptbestandteile](media/transaction-diagnostics/4partsCrossComponent.png)

## <a name="cross-component-transaction-chart"></a>Komponentenübergreifendes Transaktionsdiagramm

Dieses Diagramm stellt eine komponentenübergreifende Zeitachse mit horizontalen Balken für die Dauer von Anforderungen und Abhängigkeiten bereit. Alle gesammelten Ausnahmen werden ebenfalls auf der Zeitachse markiert.

* Die oberste Zeile dieses Diagramms stellt den Einstiegspunkt dar, die eingehende Anforderung für die erste in dieser Transaktion aufgerufene Komponente. Die Dauer stellt hier die insgesamt für den Abschluss der Transaktion in Anspruch genommene Zeit dar.
* Alle Aufrufe von externen Abhängigkeiten sind einfache, nicht zuklappbare Zeilen mit Symbolen, die den Typ der Abhängigkeit darstellen.
* Aufrufe an andere Komponenten sind in zuklappbaren Zeilen aufgeführt. Jede Zeile entspricht einem bestimmten für die Komponenten aufgerufenen Vorgang.
* Standardmäßig wird die von Ihnen ausgewählte Anforderung, Abhängigkeit oder Ausnahme rechts angezeigt.
* Wählen Sie eine beliebige Zeile aus, um ihre [Details auf der rechten Seite](#details-of-the-selected-telemetry) anzuzeigen. 

> [!NOTE]
> Aufrufe an andere Komponenten weisen zwei Zeilen auf : Eine Zeile stellt den ausgehenden Aufruf (Abhängigkeit) von der aufrufenden Komponente dar, die andere Zeile entspricht der eingehenden Anforderung bei der aufgerufenen Komponente. Das führende Symbol und die unterschiedliche Gestaltung der Balken für die Dauer erleichtern die Unterscheidung zwischen ihnen.

## <a name="all-telemetry-with-this-operation-id"></a>Die gesamte Telemetrie mit dieser Vorgangs-ID

Dieser Abschnitt zeigt eine flache Listenansicht in einer zeitlichen Abfolge der gesamten Telemetrie für diese Transaktion. Er zeigt auch die benutzerdefinierten Ereignisse und Ablaufverfolgungen, die im Transaktionsdiagramm nicht angezeigt werden. Sie können diese Liste hinsichtlich der von einer bestimmten Komponente/eines Aufrufs generierten Telemetrie filtern. Sie können in dieser Liste beliebige Telemetrieelemente auswählen, um die entsprechenden [Details auf der rechten Seite](#details-of-the-selected-telemetry) anzuzeigen.

![Zeitliche Abfolge der gesamten Telemetrie](media/transaction-diagnostics/allTelemetryDrawerOpened.png)

## <a name="details-of-the-selected-telemetry"></a>Details zur ausgewählten Telemetrie

Dieser reduzierbare Bereich zeigt die Details jedes ausgewählte Elements aus dem Transaktionsdiagramm oder der Liste. Mit "Alle anzeigen" wird eine Liste der erfassten Standardattribute angezeigt. Eventuelle benutzerdefinierte Attribute werden separat unterhalb des Standardsatzes aufgeführt. Klicken Sie auf die „...“ unten dem Stapelüberwachungsfenster, um eine Option zum Kopieren der Ablaufverfolgung zu erhalten. Klicken Sie auf „Profiler-Überwachungen öffnen“ oder „Debugmomentaufnahme öffnen“, um die Diagnose auf Codeebene in den entsprechenden Detailbereichen anzuzeigen.

![Ausnahmedetail](media/transaction-diagnostics/exceptiondetail.png)

## <a name="search-results"></a>Suchergebnisse

Dieser reduzierbare Bereich zeigt die anderen Ergebnisse an, die den Filterkriterien entsprechen. Klicken Sie auf ein beliebiges Ergebnis, um die entsprechenden Details in den drei oben aufgeführten Abschnitten zu aktualisieren. Wir versuchen, Stichproben zu finden, die höchstwahrscheinlich die Details aller Komponenten enthalten, auch wenn die Stichprobenentnahme in irgendeiner dieser Komponenten wirksam sind. Diese sind als „vorgeschlagene“ Beispiele dargestellt.

![Suchergebnisse](media/transaction-diagnostics/searchResults.png)

## <a name="profiler-and-snapshot-debugger"></a>Profiler und Momentaufnahmedebugger

Der [Application Insights Profiler](./profiler.md) oder der [Momentaufnahmedebugger](snapshot-debugger.md) unterstützen die Diagnose von Leistungs- und Ausfallproblemen auf Codeebene. Dank dieser Benutzeroberfläche können Sie Profilerüberwachungen oder Momentaufnahmen von beliebigen Komponenten mit nur einem Klick anzeigen.

Setzen Sie sich bei Problemen mit dem Profiler unter **serviceprofilerhelp\@microsoft.com** mit uns in Verbindung.

Setzen Sie sich bei Problemen mit dem Momentaufnahmedebugger unter **snapshothelp\@microsoft.com** mit uns in Verbindung.

![Profilerintegration](media/transaction-diagnostics/profilerTraces.png)

## <a name="faq"></a>Häufig gestellte Fragen

*Es wird eine einzelne Komponente im Diagramm angezeigt, und die anderen werden nur als externe Abhängigkeiten ohne Details zu den Vorgängen innerhalb der Komponenten dargestellt.*

Mögliche Ursachen:

* Sind die anderen Komponenten mit Application Insights instrumentiert?
* Verwenden sie das neueste stabile Application Insights-SDK?
* Wenn es sich bei diesen Komponenten um separate Application Insights-Ressourcen handelt, haben Sie den erforderlichen [Zugriff](resources-roles-access-control.md)? Wenn Sie Zugriff haben und die Komponenten mit den neuesten Application Insights-SDKs instrumentiert sind, informieren Sie uns über den Feedbackkanal in der oberen rechten Ecke.

*Ich sehe doppelte Zeilen für die Abhängigkeiten. Entspricht dies dem erwarteten Verhalten?*

Zurzeit zeigen wir den ausgehenden Abhängigkeitsaufruf separat von der eingehenden Anforderung an. Normalerweise sehen die beiden Aufrufe identisch aus, lediglich der Wert für die Dauer unterscheidet sich aufgrund des Netzwerk-Roundtrips. Das führende Symbol und die unterschiedliche Gestaltung der Balken für die Dauer erleichtern die Unterscheidung zwischen ihnen. Finden Sie diese Darstellung der Daten verwirrend? Senden Sie uns Ihr Feedback!

*Treten Uhrabweichungen über verschiedene Komponenteninstanzen hinweg auf?*

Die Zeitachsen im Transaktionsdiagramm gleichen Uhrabweichungen aus. Sie können die genauen Zeitstempel im Detailbereich oder mithilfe von Analytics anzeigen.

*Warum fehlen auf der neuen Benutzeroberfläche die meisten der zugehörigen Elementabfragen?*

Dies ist beabsichtigt. Alle zugehörigen Elemente über alle Komponenten hinweg stehen bereits auf der linken Seite zur Verfügung (oberer und unterer Abschnitt). Die neue Benutzeroberfläche weist zwei zugehörige Elemente auf, die von der linken Seite nicht abgedeckt werden: die gesamte Telemetrie fünf Minuten vor und nach dem Ereignis und die Benutzerzeitachse.

