---
title: 'Dienstgrenzwerte für Ebenen und SKUs: Azure Search'
description: Grenzwerte für den Dienst, die bei der Kapazitätsplanung verwendet werden, sowie Höchstwerte für Anforderungen und Antworten für Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 419c533aabd67637efa64777387c491dd890596e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65024638"
---
# <a name="service-limits-in-azure-search"></a>Grenzwerte für den Azure Search-Dienst
Die Grenzwerte für Speicher, Workloads und Mengen von Indizes, Dokumenten und anderen Objekten hängen davon ab, ob die [Bereitstellung von Azure Search](search-create-service-portal.md) im Tarif **Free**, **Basic**, **Standard** oder **Storage Optimized** erfolgt.

+ **Free** ist ein gemeinsamer mehrinstanzfähiger Dienst, der Teil Ihres Azure-Abonnements ist.

+ **Basic** bietet spezifische Computerressourcen für Produktionsworkloads mit geringerem Umfang.

+ **Standard** wird auf dedizierten Computern ausgeführt. Sie bieten höhere Speicher- und Verarbeitungskapazität auf jeder Ebene. Standard ist in vier Ebenen verfügbar: S1, S2, S3 und S3 HD.

+ **Storage Optimized** wird auf dedizierten Computern mit mehr Gesamtspeicher, Speicherbandbreite und Arbeitsspeicher als **Standard** ausgeführt. „Storage Optimized“ gibt es auf zwei Ebenen: L1 und L2

> [!NOTE]
> Die Tarife vom Typ „Storage Optimized“ sind derzeit als günstigere Vorschauversion zum Testen und Experimentieren erhältlich, um Feedback zu sammeln. Die endgültigen Preise werden später bekannt gegeben, wenn diese Tarife allgemein verfügbar sind. Wir raten davon ab, diese Tarife für Produktionsanwendungen zu verwenden.

  S3 High Density (S3 HD) ist für bestimmte Workloads konzipiert: [Mehrinstanzenfähigkeit](search-modeling-multitenant-saas-applications.md) und große Mengen von kleinen Indizes (eine Million Dokumente pro Index, 3000 Indizes pro Dienst). Bei diesem Tarif ist das [Indexerfeature](search-indexer-overview.md) nicht verfügbar. Bei S3 HD muss der Push-Ansatz für die Datenerfassung verwendet werden, wobei Daten mithilfe von API-Aufrufen per Push von der Quelle an den Index übertragen werden. 

> [!NOTE]
> Ein Dienst wird für einen bestimmten Tarif bereitgestellt. Das Wechseln von Tarifen, um die Kapazität zu erhöhen, umfasst die Bereitstellung eines neuen Diensts (es gibt kein direktes Upgrade). Weitere Informationen finden Sie unter [Auswählen einer SKU oder eines Tarifs](search-sku-tier.md). Weitere Informationen zum Anpassen der Kapazität in einem Dienst, den Sie bereits bereitgestellt haben, finden Sie unter [Skalieren von Ressourcenebenen für Abfrage und Indizierung von Workloads in Azure Search](search-capacity-planning.md).
>

## <a name="subscription-limits"></a>Grenzwerte für Abonnements
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="storage-limits"></a>Speichergrenzwerte
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

<a name="index-limits"></a>

## <a name="index-limits"></a>Indexgrenzwerte

| Resource | Kostenlos | Basic&nbsp;<sup>1</sup>  | S1 | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| -------- | ---- | ------------------- | --- | --- | --- | --- | --- | --- |
| Maximale Anzahl von Indizes |3 |5 oder 15 |50 |200 |200 |1000 pro Partition oder 3000 pro Dienst |10 |10 |
| Maximale Anzahl der einfachen Felder pro Index |1000 |100 |1000 |1000 |1000 |1000 |1000 |1000 |
| Maximale Anzahl der komplexen Sammlungsfelder pro Index |40 |40 |40 |40 |40 |40 |40 |40 |
| Maximale Anzahl der Elemente in allen komplexen Sammlungen pro Dokument |3000 |3000 |3000 |3000 |3000 |3000 |3000 |3000 |
| Maximale Tiefe der komplexen Felder |10 |10 |10 |10 |10 |10 |10 |10 |
| Maximale Anzahl von [Vorschlägen](https://docs.microsoft.com/rest/api/searchservice/suggesters) pro Index |1 |1 |1 |1 |1 |1 |1 |1 |
| Maximale Anzahl von [Bewertungsprofilen](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) pro Index |100 |100 |100 |100 |100 |100 |100 |100 |
| Maximale Anzahl von Funktionen pro Profil |8 |8 |8 |8 |8 |8 |8 |8 |

<sup>1</sup> Basic-Dienste, die vor Dezember 2017 erstellt wurden, haben niedrigere Grenzwerte (5 statt 15) für Indizes. Der Basic-Tarif ist die einzige SKU mit einem unteren Grenzwert von 100 Feldern pro Index.

<a name="document-limits"></a>

## <a name="document-limits"></a>Dokumentgrenzwerte 

Ab Oktober 2018 gelten für neue Dienste, die in einem kostenpflichtigen Tarif (Basic, S1, S2, S3, S3 HD) in einer beliebigen Region erstellt werden, keine Dokumentgrenzwerte mehr. In den meisten Regionen können zwar bereits seit November/Dezember 2017 beliebig viele Dokumente verwendet werden, es gab jedoch noch fünf Regionen, in denen weiterhin Dokumentgrenzwerte galten. Je nachdem, wann und wo Sie einen Suchdienst erstellt haben, verwenden Sie möglicherweise einen Dienst, für den noch Dokumentgrenzwerte gelten.

Überprüfen Sie auf der Kachel „Nutzung“ auf der Übersichtsseite Ihres Diensts, ob Dokumentgrenzwerte für Ihren Dienst gelten. Die Dokumentanzahl ist entweder unbegrenzt, oder es gilt ein Grenzwert basierend auf dem Tarif dafür.

  ![Kachel „Nutzung“](media/search-limits-quotas-capacity/portal-usage-tile.png)

### <a name="regions-previously-having-document-limits"></a>Regionen, für die noch Dokumentgrenzwerte galten

Sollte im Portal ein Dokumentgrenzwert angegeben sein, wurde Ihr Dienst entweder vor Ende 2017 oder in einem Rechenzentrum erstellt, in dem Azure Search-Dienste in Clustern mit geringerer Kapazität gehostet werden:

+ Australien (Osten)
+ Asien, Osten
+ Indien, Mitte
+ Japan, Westen
+ USA, Westen-Mitte

Für Dienste, die Dokumentgrenzwerten unterliegen, gelten die folgenden Obergrenzen:

|  Kostenlos | Basic | S1 | S2 | S3 | S3&nbsp;HD |
|-------|-------|----|----|----|-------|
|  10\.000 |1&nbsp;Millionen |15 Millionen pro Partition oder 180 Millionen pro Dienst |60 Millionen pro Partition oder 720 Millionen pro Dienst |120 Millionen pro Partition oder 1,4 Milliarden pro Dienst |1 Millionen pro Index oder 200 Millionen pro Partition |

Sollten für den Dienst Einschränkungen gelten, die Sie behindern, erstellen Sie einen neuen Dienst, und veröffentlichen Sie anschließend sämtliche Inhalte erneut für diesen Dienst. Es gibt keinen Mechanismus, mit dem Sie Ihren Dienst nahtlos im Hintergrund auf neuer Hardware bereitstellen können.

> [!Note] 
> Bei S3 High Density-Diensten, die nach Ende 2017 erstellt wurden, wurde das Limit „200 Millionen Dokumente pro Partition“ entfernt, das Limit „1 Million Dokumente pro Index“ bleibt jedoch bestehen.


### <a name="document-size-limits-per-api-call"></a>Dokumentgrößenbeschränkungen pro API-Aufruf

Die maximale Dokumentgröße beim Aufrufen einer Index-API beträgt etwa 16 Megabytes.

Die Dokumentgröße ist tatsächlich eine Begrenzung der Größe des Anforderungstexts der Index-API. Da Sie einen Batch von mehreren Dokumenten auf einmal an die Index-API übergeben können, hängt die realistische maximale Größe davon ab, wie viele Dokumente im Batch vorhanden sind. Für einen Batch mit einem einzelnen Dokument beträgt die maximale Dokumentgröße 16 MB von JSON.

Um die Dokumentgröße niedrig zu halten, achten Sie darauf, nicht abfragbare Daten von der Anforderung auszuschließen. Bilder und andere binäre Daten können nicht direkt abgefragt werden und sollten nicht im Index gespeichert werden. Um nicht abfragbare Daten in Suchergebnisse zu integrieren, definieren Sie ein nicht durchsuchbares Feld, in dem ein URL-Verweis auf die Ressource gespeichert wird.

## <a name="indexer-limits"></a>Indexergrenzwerte

Es gibt eine maximale Ausführungsdauer, um den Dienst als Ganzes ausgewogen und stabil zu gestalten, aber größere Datensätze benötigen möglicherweise mehr Indizierungszeit, als das Maximum zulässt. Wenn ein Indizierungsauftrag nicht innerhalb der maximal zulässigen Zeit abgeschlossen werden kann, versuchen Sie, den Auftrag nach einem Zeitplan auszuführen. Der Planer verfolgt den Indizierungsstatus. Wenn ein geplanter Indizierungsauftrag aus irgendeinem Grund unterbrochen wird, kann der Indexer den Auftrag bei der nächsten geplanten Ausführung an der Stelle fortsetzen, an der er unterbrochen wurde.


| Resource | Free&nbsp;<sup>1</sup> | Basic&nbsp;<sup>2</sup>| S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>3</sup>|L1 |L2 |
| -------- | ----------------- | ----------------- | --- | --- | --- | --- | --- | --- |
| Maximale Anzahl von Indexern |3 |5 oder 15|50 |200 |200 |– |10 |10 |
| Maximale Datenquellen |3 |5 oder 15 |50 |200 |200 |– |10 |10 |
| Maximale Qualifikationsgruppen <sup>4</sup> |3 |5 oder 15 |50 |200 |200 |– |10 |10 |
| Maximale Indizierungslast pro Aufruf |10\.000 Dokumente |Nur durch maximale Dokumentanzahl beschränkt |Nur durch maximale Dokumentanzahl beschränkt |Nur durch maximale Dokumentanzahl beschränkt |Nur durch maximale Dokumentanzahl beschränkt |– |Keine Begrenzung |Keine Begrenzung |
| Minimaler Zeitplan | 5 Minuten |5 Minuten |5 Minuten |5 Minuten |5 Minuten |5 Minuten |5 Minuten | 5 Minuten |
| Maximale Ausführungsdauer <sup>5</sup> | 1–3 Minuten |24 Stunden |24 Stunden |24 Stunden |24 Stunden |–  |24 Stunden |24 Stunden |
| Maximale Ausführungsdauer für Qualifikationsgruppen der kognitiven Suche oder für die BLOB-Indizierung bei Bildanalysen <sup>5</sup> | 3 bis 10 Minuten |2 Stunden |2 Stunden |2 Stunden |2 Stunden |–  |2 Stunden |2 Stunden |
| Blobindexer: maximale Blobgröße, MB |16 |16 |128 |256 |256 |–  |256 |256 |
| Blobindexer: maximale Anzahl der Zeichen des aus einem Blob extrahierten Inhalts |32\.000 |64\.000 |4&nbsp;Millionen |4&nbsp;Millionen |4&nbsp;Millionen |– |4&nbsp;Millionen |4&nbsp;Millionen |

<sup>1</sup> Die maximale Indexerausführungszeit bei Diensten im Free-Tarif beträgt drei Minuten für Blobquellen und eine Minute für alle anderen Datenquellen. Für eine KI-Indizierung, die Aufrufe in Cognitive Services ausführt, gilt bei den kostenlosen Diensten ein Limit von 20 kostenlosen Transaktionen pro Tag. Dabei ist eine Transaktion als ein Dokument definiert, das die Anreicherungspipeline erfolgreich durchläuft.

<sup>2</sup> Basic-Dienste, die vor Dezember 2017 erstellt wurden, haben niedrigere Grenzwerte (5 statt 15) für Indexer, Datenquellen und Qualifikationsgruppen.

<sup>3</sup> S3 HD-Dienste beinhalten keine Indexerunterstützung.

<sup>4</sup> Maximal 30 Fähigkeiten pro Qualifikationsgruppe.

<sup>5</sup> Workloads der kognitiven Suche und Bildanalysen in der Azure-BLOB-Indizierung weisen eine kürzere Ausführungsdauer auf als die normale Textindizierung. Bildanalysen und die Verarbeitung natürlicher Sprache sind rechenintensive Vorgänge, die unverhältnismäßig große Mengen an verfügbarer Verarbeitungskapazität verbrauchen. Die Ausführungsdauer wurde reduziert, damit andere Aufträge in der Warteschlange ausgeführt werden können.  

## <a name="queries-per-second-qps"></a>Abfragen pro Sekunde (QPS)

QPS-Schätzungen müssen unabhängig von jedem Kunde erstellt werden. Indexgröße und Komplexität, Abfragegröße und Komplexität sowie der Umfang des Datenverkehrs sind Hauptentscheidungskriterium für den QPS-Wert. Es gibt keine Möglichkeit, sinnvolle Schätzungen abzugeben, wenn diese Faktoren unbekannt sind.

Schätzungen sind besser vorhersagbar, wenn sie für Dienste berechnet werden, die auf dedizierten Ressourcen ausgeführt werden (Basic- und Standard-Tarife). Sie können den QPS-Wert genauer schätzen, da Sie die Kontrolle über mehr Parameter haben. Anleitungen zur Herangehensweise für Schätzungen finden Sie unter [Leistung und Optimierung von Azure Search](search-performance-optimization.md).

Für die Tarife vom Typ „Storage Optimized“ sollten Sie einen geringeren Abfragedurchsatz und eine höhere Latenz erwarten als für die Tarife vom Typ „Standard“.  Die Methodik zum Schätzen der zu erwartenden Abfrageleistung entspricht der der Standard-Tarife.

## <a name="data-limits-cognitive-search"></a>Datengrenzwerte (kognitive Suche)

Für eine [Pipeline für die kognitive Suche](cognitive-search-concept-intro.md), die Aufrufe zur [Entitätserkennung](cognitive-search-skill-entity-recognition.md), [Schlüsselbegriffserkennung](cognitive-search-skill-keyphrases.md), [Standpunktanalyse](cognitive-search-skill-sentiment.md) und [Sprachenerkennung](cognitive-search-skill-language-detection.md) an eine Textanalyseressource sendet, gelten Datengrenzwerte. Die maximale Größe eines Datensatzes ist 50.000 Zeichen, gemessen durch `String.Length`. Wenn Sie Ihre Daten teilen müssen, bevor Sie sie an das Stimmungsanalysetool senden, verwenden Sie den [Skill „Text teilen“](cognitive-search-skill-textsplit.md).

## <a name="api-request-limits"></a>API-Anforderungsgrenzwerte
* Maximal 16 MB pro Anforderung <sup>1</sup>
* Maximale URL-Länge von 8 KB
* Maximal 1000 Dokumente pro Batch von Hochlade-, Zusammenführungs- oder Löschvorgängen für Indizes
* Maximal 32 Felder in $orderby-Klausel
* Maximale Suchbegriffgröße ist 32.766 Byte (32 KB minus 2 Bytes) von UTF-8-codiertem Text

<sup>1</sup> In Azure Search darf der Inhalt einer Anforderung nicht größer als 16 MB sein. Dies beschränkt möglicherweise den Inhalt einzelner Felder oder Sammlungen, für die ansonsten keine theoretischen Beschränkungen gelten. (Weitere Informationen zur Feldzusammensetzung und den Beschränkungen finden Sie unter [Supported data types](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) (Unterstützte Datentypen).)

## <a name="api-response-limits"></a>API-Antwortengrenzwerte
* Maximale Rückgabe von 1000 Dokumenten pro Seite mit Suchergebnissen
* Maximale Rückgabe von 100 Vorschlägen pro Anforderung der Vorschlags-API

## <a name="api-key-limits"></a>API-Schlüsselgrenzwerte
API-Schlüssel werden für die Dienstauthentifizierung verwendet. Es gibt zwei Arten. Administratorschlüssel werden im Anforderungsheader angegeben und gewähren vollständigen Lese-und Schreibzugriff für den Dienst. Abfrageschlüssel sind schreibgeschützt, die in der URL angegeben und normalerweise an Clientanwendungen verteilt werden.

* Maximal 2 Administratorschlüssel pro Dienst
* Maximal 50 Abfrageschlüssel pro Dienst
