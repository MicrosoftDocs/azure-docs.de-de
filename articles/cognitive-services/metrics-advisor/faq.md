---
title: 'Metrics Advisor: Häufig gestellte Fragen'
titleSuffix: Azure Cognitive Services
description: Häufig gestellte Fragen zum Metrics Advisor-Dienst
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: mbullwin
ms.openlocfilehash: 0c4c296cb1454ed89eef102732533589b1c8ca0d
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420958"
---
# <a name="metrics-advisor-frequently-asked-questions"></a>Metrics Advisor: Häufig gestellte Fragen

### <a name="what-is-the-cost-of-my-instance"></a>Welche Kosten fallen für meine Instanz an?

Für die Verwendung Ihrer Instanz in der Vorschau fallen derzeit keine Kosten an.

### <a name="why-cant-i-create-the-resource-the-pricing-tier-is-unavailable-and-it-says-you-have-already-created-1-s0-for-this-subscription"></a>Warum kann ich die Ressource nicht erstellen? Der Tarif ist nicht verfügbar, und „You have already created 1 S0 for this subscription“ (Sie haben bereits 1 S0 für dieses Abonnement erstellt) wird angezeigt.

:::image type="content" source="media/pricing.png" alt-text="Meldung, wenn eine F0-Ressource bereits vorhanden ist":::

In der öffentlichen Vorschau kann nur eine Instanz von Metrics Advisor pro Region unter einem Abonnement erstellt werden.

Wenn Sie bereits über eine Instanz verfügen, die mit demselben Abonnement in derselben Region erstellt wurde, können Sie eine andere Region oder ein anderes Abonnement zum Erstellen einer neuen Instanz verwenden. Oder löschen Sie eine vorhandene Instanz, um eine neue Instanz zu erstellen.

Wenn Sie die vorhandene Instanz bereits gelöscht haben, der Fehler jedoch weiterhin angezeigt wird, warten Sie nach dem Löschen der Ressource ca. 20 Minuten, bevor Sie eine neue Instanz erstellen.

## <a name="basic-concepts"></a>Grundlegende Konzepte

### <a name="what-is-multi-dimensional-time-series-data"></a>Was sind mehrdimensionale Zeitreihendaten?

Weitere Informationen finden Sie in der Definition für [Multi-dimensional metric](glossary.md#multi-dimensional-metric) (Mehrdimensionale Metrik) im Glossar.

### <a name="how-much-data-is-needed-for-metrics-advisor-to-start-anomaly-detection"></a>Wie viele Daten sind erforderlich, damit der Metrics Advisor die Anomalieerkennung startet?

Mindestens ein Datenpunkt kann eine Anomalieerkennung auslösen. Dies bringt jedoch nicht die beste Genauigkeit mit sich. Der Dienst geht von einem Fenster vorheriger Datenpunkte aus und verwendet den Wert, den Sie während der Datenfeederstellung als Regel zum Füllen von Lücken angegeben haben.

Es wird empfohlen, dass Sie vor dem Zeitstempel, für den Sie die Ermittlung durchführen möchten, über einige Daten verfügen.
Ausgehend von der Granularität der Daten variiert die empfohlene Datenmenge wie unten beschrieben.

| Granularität | Empfohlene Datenmenge für die Erkennung |
| ----------- | ------------------------------------- |
| Weniger als 5 Minuten | 4 Tage an Daten |
| 5 Minuten bis 1 Tag | 28 Tage an Daten |
| Mehr als 1 Tag, bis zu 31 Tage | 4 Jahre an Daten |
| Mehr als 31 Tage | 48 Jahre an Daten |

### <a name="why-metrics-advisor-doesnt-detect-anomalies-from-historical-data"></a>Warum erkennt Metrics Advisor keine Anomalien in Verlaufsdaten?

Metrics Advisor dient zum Erkennen von Livestreamingdaten. Es gibt eine Einschränkung der maximalen Länge der Verlaufsdaten, die der Dienst rückwirkend prüft und für die er eine Anomalieerkennung ausführt. Dies bedeutet, dass nur Datenpunkte nach einem bestimmten frühesten Zeitstempel Anomalieerkennungsergebnisse aufweisen. Der früheste Zeitstempel hängt von der Granularität der Daten ab.

Ausgehend von der Granularität der Daten weisen die Längen der Verlaufsdaten die Anomalieerkennungsergebnisse wie folgt auf.

| Granularität | Maximale Länge der Verlaufsdaten für die Anomalieerkennung |
| ----------- | ------------------------------------- |
| Weniger als 5 Minuten | Onboardingzeit – 13 Stunden |
| 5 Minuten bis weniger als 1 Stunde | Onboardingzeit – 4 Tage  |
| 1 Stunde bis weniger als 1 Tag | Onboardingzeit – 14 Tage  |
| 1 Tag | Onboardingzeit – 28 Tage  |
| Mehr als 1 Tag, weniger als 31 Tage | Onboardingzeit – 2 Jahre  |
| Mehr als 31 Tage | Onboardingzeit – 24 Jahre   |

### <a name="more-concepts-and-technical-terms"></a>Weitere Konzepte und technische Begriffe

Weitere Informationen finden Sie auch im [Glossar](glossary.md).

###  <a name="how-do-i-write-a-valid-query-for-ingesting-my-data"></a>Wie schreibe ich eine gültige Abfrage zum Erfassen meiner Daten?  

Damit die Daten vom Metrics Advisor erfasst werden können, müssen Sie eine Abfrage erstellen, die die Dimensionen der Daten zu einem einzelnen Zeitstempel zurückgibt. Der Metrics Advisor führt diese Abfrage mehrmals aus, um die Daten von jedem Zeitstempel abzurufen. 

Beachten Sie, dass die Abfrage zu einem bestimmten Zeitstempel höchstens einen Datensatz für jede Dimensionskombination zurückgeben sollte. Alle zurückgegebenen Datensätze müssen denselben Zeitstempel aufweisen. Es dürfen keine doppelten Datensätze von der Abfrage zurückgegeben werden.

Angenommen, Sie erstellen die folgende Abfrage für eine tägliche Metrik: 
 
`select timestamp, city, category, revenue from sampledata where Timestamp >= @StartTime and Timestamp < dateadd(DAY, 1, @StartTime)`

Achten Sie darauf, dass Sie die richtige Granularität für Ihre Zeitreihe verwenden. Für eine stündliche Metrik verwenden Sie Folgendes: 

`select timestamp, city, category, revenue from sampledata where Timestamp >= @StartTime and Timestamp < dateadd(hour, 1, @StartTime)`

Beachten Sie, dass diese Abfragen nur Daten zu einem einzigen Zeitstempel zurückgeben und alle Dimensionskombinationen enthalten, die vom Metrics Advisor erfasst werden. 

:::image type="content" source="media/query-result.png" alt-text="Ein Abfrageergebnis mit einem Zeitstempel" lightbox="media/query-result.png":::


### <a name="how-do-i-detect-spikes--dips-as-anomalies"></a>Wie erkenne ich Spitzen und Einbrüche als Anomalien?

Wenn Sie harte Schwellenwerte vordefiniert haben, können Sie „Hard threshold“ (Harter Schwellenwert) unter [anomaly detection configurations](how-tos/configure-metrics.md#anomaly-detection-methods) (Anomalieerkennungskonfiguration) manuell festlegen.
Wenn keine Schwellenwerte vorhanden sind, können Sie die intelligente Erkennung verwenden, die von KI unterstützt wird. Ausführliche Informationen finden Sie unter [Optimieren der Erkennungskonfiguration](how-tos/configure-metrics.md#tune-the-detecting-configuration).

### <a name="how-do-i-detect-inconformity-with-regular-seasonal-patterns-as-anomalies"></a>Wie erkenne ich eine fehlende Übereinstimmung mit regulären (saisonalen) Mustern als Anomalien?

Die intelligente Erkennung ist in der Lage, das Muster Ihrer Daten einschließlich saisonaler Muster zu lernen. Anschließend werden die Datenpunkte, die den regulären Mustern nicht entsprechen, als Anomalien erkannt. Ausführliche Informationen finden Sie unter [Optimieren der Erkennungskonfiguration](how-tos/configure-metrics.md#tune-the-detecting-configuration).

### <a name="how-do-i-detect-flat-lines-as-anomalies"></a>Wie erkenne ich fehlende Schwankungen als Anomalien?

Wenn Ihre Daten normalerweise recht instabil sind und stark schwanken und Sie benachrichtigt werden möchten, wenn die Daten zu stabil sind oder sogar überhaupt keine Schwankungen mehr aufweisen, kann „Change Threshold“ (Änderungsschwellenwert) so konfiguriert werden, dass solche Datenpunkte erkannt werden, wenn die Änderung zu gering ist.
Ausführliche Informationen finden Sie unter [Anomalieerkennungskonfiguration](how-tos/configure-metrics.md#anomaly-detection-methods).

### <a name="how-to-set-up-email-settings-and-enable-alerting-by-email"></a>Wie richte ich E-Mail-Einstellungen ein und aktiviere Warnungen per E-Mail?

1.  Ein Benutzer mit den Berechtigungen „Abonnementadministrator“ oder „Ressourcengruppenadministrator“ muss zu der Metrics Advisor-Ressource navigieren, die im Azure-Portal erstellt wurde, und die Registerkarte **Zugriffssteuerung (IAM)** auswählen. 
2.  Wählen Sie **Rollenzuweisung hinzufügen** aus.
3.  Wählen Sie die Rolle **Cognitive Services Metrics Advisor-Administrator** und dann Ihr Konto aus, wie in der Abbildung unten dargestellt.
4.  Klicken Sie auf die Schaltfläche **Speichern**, dann sind Sie erfolgreich als Administrator der Metrics Advisor-Ressource hinzugefügt worden. Beachten Sie, dass alle oben genannten Aktionen vom Abonnementadministrator oder Ressourcengruppenadministrator durchgeführt werden müssen. 

:::image type="content" source="media/access-control.png" alt-text="Menüseite für die Zugriffssteuerung (IAM) mit ausgewählter Option zum Hinzufügen einer Rollenzuweisung, gefolgt von einem Feld zum Zuweisen des Zugriffs zum ausgewählten Benutzer, der mit der Zugriffsrolle „Cognitive Services Metrics Advisor-Administrator“ angezeigt wird, gefolgt von der Schaltfläche „Speichern“ der Benutzeroberfläche, die ausgewählt ist, um die Schritte der Suche nach einem Benutzer und zum Hinzufügen einer bestimmten Ebene von Zugriffsberechtigungen zu veranschaulichen." lightbox="media/access-control.png":::


5.  Es kann bis zu einer Minute dauern, bis die Berechtigungen weitergegeben werden. Wählen Sie dann Ihren Metrics Advisor-Arbeitsbereich und anschließend die Option **E-Mail-Einstellung** im linken Navigationsbereich aus. Füllen Sie die erforderlichen Felder aus, insbesondere die SMTP-bezogenen Informationen. 
6.  Wählen Sie **Speichern** aus, dann sind Sie mit der E-Mail-Konfiguration fertig. Sie können neue Hooks erstellen und metrische Anomalien abonnieren, um Warnungen in Quasi-Echtzeit zu erhalten. 

## <a name="advanced-concepts"></a>Erweiterte Konzepte

### <a name="how-does-metric-advisor-build-an-incident-tree-for-multi-dimensional-metrics"></a>Wie erstellt Metrics Advisor eine Incidentstruktur für mehrdimensionale Metriken?

Eine Metrik kann in mehrere Zeitreihen nach Dimensionen aufgeteilt werden. Beispielsweise wird die Metrik `Response latency` für alle Dienste überwacht, die im Besitz des Teams sind. Die `Service`-Kategorie könnte als Dimension verwendet werden, um die Metrik zu erweitern, sodass `Response latency` durch `Service1`, `Service2` usw. aufgeteilt wird. Jeder Dienst kann auf verschiedenen Computern in mehreren Rechenzentren bereitgestellt werden, damit die Metrik weiter durch `Machine` und `Data center` aufgeteilt werden kann.

|Dienst| Rechenzentrum| Machine  | 
|----|------|----------------   |
| S1 |  DC1 |   M1 |
| S1 |  DC1 |   M2 |
| S1 |  DC2 |   M3 |
| S1 |  DC2 |   M4 |
| S2 |  DC1 |   M1 |
| S2 |  DC1 |   M2 |
| S2 |  DC2 |   M5 |
| S2 |  DC2 |   M6 |
| ...|      |      |

Ausgehend von der Gesamtsumme `Response latency` können wir Detailinformationen der Metrik nach `Service`, `Data center` und `Machine` anzeigen. Möglicherweise ist es jedoch sinnvoller, dass die Dienstbesitzer den Pfad `Service` -> `Data center` -> `Machine` oder Infrastrukturtechniker den Pfad `Data Center` -> `Machine` -> `Service` benutzen. Dies hängt alles von den individuellen Geschäftsanforderungen des Benutzers ab. 

Im Metrics Advisor können Benutzer jeden Pfad angeben, für den sie von einem Knoten der hierarchischen Topologie aus einen Drilldown oder ein Rollup ausführen möchten. Genauer gesagt handelt es sich bei der hierarchischen Topologie um ein gerichtetes azyklisches Diagramm und nicht um eine Baumstruktur. Es gibt eine vollständige hierarchische Topologie, die aus allen möglichen Dimensionskombinationen besteht, wie im folgenden Beispiel: 

:::image type="content" source="media/dimension-combinations-view.png" alt-text="hierarchisches Topologiediagramm, das aus mehreren miteinander verbundenen Knoten und Kanten mit mehreren Dimensionen besteht, die mit S, DC und M bezeichnet sind, mit entsprechenden Zahlen von 1 bis 6." lightbox="media/dimension-combinations-view.png":::

Wenn die Dimension `Service` theoretisch `Ls` verschiedene Werte, die Dimension `Data center` `Ldc` verschiedene Werte und die Dimension `Machine` `Lm` verschiedene Werte aufweist, dann könnte es in der hierarchischen Topologie `(Ls + 1) * (Ldc + 1) * (Lm + 1)` Dimensionskombinationen geben. 

In der Regel sind jedoch nicht alle Dimensionskombinationen gültig, was die Komplexität erheblich reduzieren kann. Wenn Benutzer die Metrik selbst aggregieren, wird die Anzahl der Dimensionen derzeit nicht beschränkt. Wenn Sie die von Metrics Advisor bereitgestellte Rollup-Funktion verwenden müssen, sollten nicht mehr als sechs Dimensionen vorhanden sein. Allerdings wird die Anzahl der von Dimensionen für eine Metrik erweiterten Zeitreihen auf weniger als 10.000 beschränkt.

Das Tool **Incidentstruktur** auf der Diagnoseseite zeigt nur Knoten an, bei denen eine Anomalie erkannt wurde, und nicht die gesamte Topologie. Dies soll Ihnen helfen, das Augenmerk auf das aktuelle Problem zu richten. Außerdem werden möglicherweise nicht alle Anomalien innerhalb der Metrik angezeigt. Stattdessen werden die obersten Anomalien auf der Grundlage des Beitrags angezeigt. So lassen sich die Auswirkungen, der Umfang und der Verbreitungsweg der anormalen Daten schnell herausfinden. Dadurch wird die Anzahl der zu untersuchenden Anomalien erheblich verringert und Benutzern geholfen, ihre zentralen Probleme zu erkennen und zu finden. 
 
Wenn beispielsweise eine Anomalie bei `Service = S2 | Data Center = DC2 | Machine = M5` auftritt, wirkt sich die Abweichung der Anomalie auf den übergeordneten Knoten `Service= S2` aus, der die Anomalie ebenfalls erkannt hat. Die Anomalie wirkt sich jedoch nicht auf das gesamte Rechenzentrum bei `DC2` und alle Dienste bei `M5` aus. Die Incidentstruktur wäre wie im unteren Screenshot aufgebaut, die oberste Anomalie ist bei `Service = S2` erfasst, und die Ursache könnte über zwei Pfade analysiert werden, die beide zu `Service = S2 | Data Center = DC2 | Machine = M5` führen.

 :::image type="content" source="media/root-cause-paths.png" alt-text="Fünf markierte Knoten mit zwei verschiedenen Pfaden, die durch Kanten mit einem gemeinsamen Knoten mit der Bezeichnung S2 verbunden sind. Die obere Anomalie wird bei Service = S2 erfasst, und die Ursache kann anhand der beiden Pfade analysiert werden, die beide zu Service = S2 | Data Center = DC2 | Machine = M5" lightbox="media/root-cause-paths.png"::: führen.

## <a name="next-steps"></a>Nächste Schritte
- [Übersicht über Metrics Advisor](overview.md)
- [Verwenden des Webportals](quickstarts/web-portal.md)