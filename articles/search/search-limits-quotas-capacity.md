---
title: Dienstgrenzwerte für Ebenen und SKUs
titleSuffix: Azure Cognitive Search
description: Grenzwerte für den Dienst, die bei der Kapazitätsplanung verwendet werden, sowie Höchstwerte für Anforderungen und Antworten für die kognitive Azure-Suche.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 994ed74750d159dfdb83259e9fe921f870ec2241
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509366"
---
# <a name="service-limits-in-azure-cognitive-search"></a>Dienstgrenzwerte in der kognitiven Azure-Suche

Die Grenzwerte für Speicher, Workloads und Mengen von Indizes und anderen Objekten hängen davon ab, ob die [Bereitstellung von Azure Cognitive Search](search-create-service-portal.md) im Tarif **Free**, **Basic**, **Standard** oder **Storage Optimized** erfolgt.

+ **Free** ist ein gemeinsamer mehrinstanzfähiger Dienst, der Teil Ihres Azure-Abonnements ist. 

+ **Basic** bietet dedizierte Computeressourcen für Produktionsworkloads mit geringerem Umfang, teilen sich jedoch bestimmte Netzwerkinfrastruktur mit anderen Mandanten.

+ **Standard** wird auf dedizierten Computern ausgeführt. Sie bieten höhere Speicher- und Verarbeitungskapazität auf jeder Ebene. Standard ist in vier Ebenen verfügbar: S1, S2, S3 und S3 HD. S3 High Density (S3 HD) ist für [Mehrinstanzenfähigkeit](search-modeling-multitenant-saas-applications.md) und große Mengen von kleinen Indizes (3.000 Indizes pro Dienst) konzipiert. S3 HD bietet kein [Indexerfeature](search-indexer-overview.md), und die Datenerfassung muss APIs nutzen, die Daten aus der Quelle an den Index übermitteln. 

+ **Storage Optimized** wird auf dedizierten Computern mit mehr Gesamtspeicher, Speicherbandbreite und Arbeitsspeicher als **Standard** ausgeführt. Dieser Tarif ist für große Indizes konzipiert, die nur langsam geändert werden. „Storage Optimized“ gibt es auf zwei Ebenen: L1 und L2.

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
| Maximale Anzahl von Elementen in allen komplexen Sammlungen pro Dokument&nbsp;<sup>2</sup> |3000 |3000 |3000 |3000 |3000 |3000 |3000 |3000 |
| Maximale Tiefe der komplexen Felder |10 |10 |10 |10 |10 |10 |10 |10 |
| Maximale Anzahl von [Vorschlägen](/rest/api/searchservice/suggesters) pro Index |1 |1 |1 |1 |1 |1 |1 |1 |
| Maximale Anzahl von [Bewertungsprofilen](/rest/api/searchservice/add-scoring-profiles-to-a-search-index) pro Index |100 |100 |100 |100 |100 |100 |100 |100 |
| Maximale Anzahl von Funktionen pro Profil |8 |8 |8 |8 |8 |8 |8 |8 |

<sup>1</sup> Basic-Dienste, die vor Dezember 2017 erstellt wurden, haben niedrigere Grenzwerte (5 statt 15) für Indizes. Der Basic-Tarif ist die einzige SKU mit einem unteren Grenzwert von 100 Feldern pro Index.

<sup>2</sup> Es ist eine Obergrenze für Elemente vorhanden, da eine hohe Anzahl von diesen Elementen den für Ihren Index erforderlichen Speicherplatz erheblich erhöht. Ein Element einer komplexen Sammlung ist als Member dieser Sammlung definiert. Angenommen, es gibt ein [Hotel-Dokument mit einer komplexen Sammlung „Rooms“](search-howto-complex-data-types.md#indexing-complex-types), dann wird jedes Zimmer in der Sammlung „Rooms“ als ein Element angesehen. Bei einer Indizierung kann die Indizierungs-Engine maximal 3000 Elemente sicher im gesamten Dokument verarbeiten. [Dieser Grenzwert](search-api-migration.md#upgrade-to-2019-05-06) wurde in `api-version=2019-05-06` eingeführt und gilt nur für komplexe Sammlungen, nicht für Zeichenfolgensammlungen oder komplexe Felder.

<a name="document-limits"></a>

## <a name="document-limits"></a>Dokumentgrenzwerte 

Seit Oktober 2018 gelten für neue Dienste, die in einem kostenpflichtigen Tarif (Basic, S1, S2, S3, S3 HD) in einer beliebigen Region erstellt werden, keine Grenzwerte für die Anzahl von Dokumenten mehr. Ältere Dienste, die vor Oktober 2018 erstellt wurden, unterliegen möglicherweise weiterhin den Grenzwerten für die Dokumentanzahl.

Um zu ermitteln, ob Ihr Dienst über Dokumentgrenzwerte verfügt, verwenden Sie die [GET Service Statistics-REST-API](/rest/api/searchservice/get-service-statistics). Dokumentgrenzwerte werden in der Antwort angezeigt, wobei `null` für keine Grenzwerte steht.

> [!NOTE]
> Obwohl der Dienst selbst keine Dokumentgrenzwerte festlegt, gibt es einen Shardgrenzwert von etwa 24 Milliarden Dokumenten pro Index für Basic-, S1-, S2 und S3-Suchdienste. Für S3 HD liegt der Shardgrenzwert bei 2 Milliarden Dokumenten pro Index. Jedes Element einer komplexen Sammlung zählt für den Shardgrenzwert als separates Dokument.

### <a name="document-size-limits-per-api-call"></a>Dokumentgrößenbeschränkungen pro API-Aufruf

Die maximale Dokumentgröße beim Aufrufen einer Index-API beträgt etwa 16 Megabytes.

Die Dokumentgröße ist tatsächlich eine Begrenzung der Größe des Anforderungstexts der Index-API. Da Sie einen Batch von mehreren Dokumenten auf einmal an die Index-API übergeben können, hängt die realistische maximale Größe davon ab, wie viele Dokumente im Batch vorhanden sind. Für einen Batch mit einem einzelnen Dokument beträgt die maximale Dokumentgröße 16 MB von JSON.

Denken Sie beim Schätzen der Dokumentgröße daran, nur die Felder zu berücksichtigen, die von einem Suchdienst verwendet werden können. Sämtliche Binär- oder Bilddaten in den Quelldokumenten sollten aus der Berechnungen ausgeschlossen werden.  

## <a name="indexer-limits"></a>Indexergrenzwerte

Es gibt eine maximale Ausführungsdauer, um den Dienst als Ganzes ausgewogen und stabil zu gestalten, aber größere Datensätze benötigen möglicherweise mehr Indizierungszeit, als das Maximum zulässt. Wenn ein Indizierungsauftrag nicht innerhalb der maximal zulässigen Zeit abgeschlossen werden kann, versuchen Sie, den Auftrag nach einem Zeitplan auszuführen. Der Planer verfolgt den Indizierungsstatus. Wenn ein geplanter Indizierungsauftrag aus irgendeinem Grund unterbrochen wird, kann der Indexer den Auftrag bei der nächsten geplanten Ausführung an der Stelle fortsetzen, an der er unterbrochen wurde.


| Resource | Free&nbsp;<sup>1</sup> | Basic&nbsp;<sup>2</sup>| S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>3</sup>|L1 |L2 |
| -------- | ----------------- | ----------------- | --- | --- | --- | --- | --- | --- |
| Maximale Anzahl von Indexern |3 |5 oder 15|50 |200 |200 |– |10 |10 |
| Maximale Datenquellen |3 |5 oder 15 |50 |200 |200 |– |10 |10 |
| Maximale Qualifikationsgruppen <sup>4</sup> |3 |5 oder 15 |50 |200 |200 |– |10 |10 |
| Maximale Indizierungslast pro Aufruf |10.000 Dokumente |Nur durch maximale Dokumentanzahl beschränkt |Nur durch maximale Dokumentanzahl beschränkt |Nur durch maximale Dokumentanzahl beschränkt |Nur durch maximale Dokumentanzahl beschränkt |– |Keine Begrenzung |Keine Begrenzung |
| Minimaler Zeitplan | 5 Minuten |5 Minuten |5 Minuten |5 Minuten |5 Minuten |5 Minuten |5 Minuten | 5 Minuten |
| Maximale Ausführungszeit| 1–3 Minuten |24 Stunden |24 Stunden |24 Stunden |24 Stunden |–  |24 Stunden |24 Stunden |
| Maximale Laufzeitdauer für Indexer mit einem Skillset <sup>5</sup> | 3 bis 10 Minuten |2 Stunden |2 Stunden |2 Stunden |2 Stunden |–  |2 Stunden |2 Stunden |
| Blobindexer: maximale Blobgröße, MB |16 |16 |128 |256 |256 |–  |256 |256 |
| Blobindexer: maximale Anzahl der Zeichen des aus einem Blob extrahierten Inhalts |32.000 |64.000 |4&nbsp;Millionen |8&nbsp;Mio. |16&nbsp;Mio. |– |4&nbsp;Millionen |4&nbsp;Millionen |

<sup>1</sup> Die maximale Indexerausführungszeit bei Diensten im Free-Tarif beträgt drei Minuten für Blobquellen und eine Minute für alle anderen Datenquellen. Für eine KI-Indizierung, die Aufrufe in Cognitive Services ausführt, gilt bei den kostenlosen Diensten ein Limit von 20 kostenlosen Transaktionen pro Tag. Dabei ist eine Transaktion als ein Dokument definiert, das die Anreicherungspipeline erfolgreich durchläuft.

<sup>2</sup> Basic-Dienste, die vor Dezember 2017 erstellt wurden, haben niedrigere Grenzwerte (5 statt 15) für Indexer, Datenquellen und Qualifikationsgruppen.

<sup>3</sup> S3 HD-Dienste beinhalten keine Indexerunterstützung.

<sup>4</sup> Maximal 30 Fähigkeiten pro Qualifikationsgruppe.

<sup>5</sup> Die KI-Anreicherung und die Bildanalyse sind rechenintensive Vorgänge, die unverhältnismäßig große Mengen der verfügbaren Verarbeitungskapazität verbrauchen. Die Laufzeit für diese Workloads wurde verkürzt, damit andere Aufträge in der Warteschlange bessere Chancen haben, ausgeführt zu werden.

> [!NOTE]
> Wie unter [Indexgrenzwerte](#index-limits) beschrieben, erzwingen Indexer die Obergrenze von 3.000 Elementen auch für alle komplexen Sammlungen pro Dokument – ab der neuesten allgemein verfügbaren API-Version, die komplexe Typen (`2019-05-06`) unterstützt. Dies bedeutet, dass dieser Grenzwert für Sie nicht gilt, wenn Sie Ihren Indexer mit einer früheren API-Version erstellt haben. Zur Sicherstellung der maximalen Kompatibilität wird ein Indexer, der mit einer früheren API-Version erstellt und dann mit API-Version `2019-05-06` oder höher aktualisiert wurde, trotzdem von der Begrenzung **ausgenommen**. Kunden sollten sich dieser negativen Auswirkungen, die wie oben erwähnt mit der Verwendung sehr komplexer Sammlungen verbunden sind, bewusst sein. Wir empfehlen Ihnen dringend, für die Erstellung aller neuen Indexer die neueste allgemein verfügbare API-Version zu nutzen.

## <a name="shared-private-link-resource-limits"></a>Beschränkungen für freigegebene Private Link-Ressourcen

Indexer haben [über private Endpunkte](search-indexer-howto-access-private.md), die über die [API für freigegebene Private Link-Ressourcen](/rest/api/searchmanagement/sharedprivatelinkresources) verwaltet werden, Zugriff auf Azure-Ressourcen. In diesem Abschnitt werden die Grenzwerte für diese Funktion beschrieben.

| Resource | Kostenlos | Basic | S1 | S2 | S3 | S3 HD | L1 | L2
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Unterstützung für den Indexer des privaten Endpunkts | Nein | Ja | Ja | Ja | Ja | Nein | Ja | Ja |
| Unterstützung für private Endpunkte für Indexer mit einem Skillset<sup>1</sup> | Nein | Nein | Nein | Ja | Ja | Nein | Ja | Ja |
| Maximale Anzahl privater Endpunkte | – | 10 oder 30 | 100 | 400 | 400 | – | 20 | 20 |
| Maximale Anzahl unterschiedlicher Ressourcentypen<sup>2</sup> | N/V | 4 | 7 | 15 | 15 | N/V | 4 | 4 |

<sup>1</sup> Die KI-Anreicherung und die Bildanalyse sind rechenintensive Vorgänge, die unverhältnismäßig große Mengen der verfügbaren Verarbeitungskapazität verbrauchen. Aus diesem Grund sind private Verbindungen in niedrigeren Tarifen deaktiviert, um negative Auswirkungen auf die Leistung und Stabilität des Suchdiensts selbst zu vermeiden.

<sup>2</sup> Die Anzahl der unterschiedlichen Ressourcentypen wird als die Anzahl der eindeutigen `groupId`-Werte berechnet, die in allen freigegebenen Private Link-Ressourcen für einen bestimmten Dienst verwendet werden. Der Status der jeweiligen Ressource spielt dabei keine Rolle.

## <a name="synonym-limits"></a>Synonymlimits

Die maximale Anzahl der Synonymzuordnungen variiert je nach Tarif. Jede Regel kann bis zu 20 Erweiterungen aufweisen. Als Erweiterungen werden gleichwertige Begriffe bezeichnet. Beispielsweise würde für „Katze“, die Zuordnung von „Kätzchen“, „Kater“ und „Felis“ (die Gattung der Katzen) als drei Erweiterungen gezählt werden.

| Resource | Kostenlos | Basic | S1 | S2 | S3 | S3-HD |L1 | L2 |
| -------- | -----|------ |----|----|----|-------|---|----|
| Maximale Synonymzuordnungen |3 |3|5 |10 |20 |20 | 10 | 10 |
| Maximale Anzahl von Regeln pro Zuordnung |5.000 |20000|20000 |20000 |20000 |20000 | 20000 | 20000  |

## <a name="data-limits-ai-enrichment"></a>Datengrenzwerte (KI-Anreicherung)

Für eine [KI-Anreicherungspipeline](cognitive-search-concept-intro.md), die Aufrufe zur [Entitätserkennung](cognitive-search-skill-entity-recognition.md), [Schlüsselbegriffserkennung](cognitive-search-skill-keyphrases.md), [Stimmungsanalyse](cognitive-search-skill-sentiment.md), [Sprachenerkennung](cognitive-search-skill-language-detection.md) und [Erkennung personenbezogener Daten](cognitive-search-skill-pii-detection.md) an eine Textanalyseressource sendet, gelten Datengrenzwerte. Die maximale Größe eines Datensatzes beträgt 50.000 Zeichen (gemessen durch [`String.Length`](/dotnet/api/system.string.length)). Wenn Sie Ihre Daten teilen müssen, bevor Sie sie an das Stimmungsanalysetool senden, verwenden Sie den [Skill „Text teilen“](cognitive-search-skill-textsplit.md).

## <a name="throttling-limits"></a>Drosselungslimits

API-Anforderungen werden gedrosselt, wenn das System sich der Spitzenkapazität nähert. Die Drosselung verhält sich für verschiedene APIs unterschiedlich. Abfrage-APIs (Suchen/Vorschlagen/AutoVervollständigen) und Indizierungs-APIs drosseln dynamisch basierend auf der Last des Diensts. Index-APIs und Dienstvorgangs-APIs verfügen über statische Grenzwerte für Anforderungsraten. 

Statische Grenzwerte für Anforderungsraten für Indexvorgänge:

+ Indizes auflisten (GET /indexes): 3 pro Sekunde pro Sucheinheit
+ Index abrufen (GET /indexes/myindex): 10 pro Sekunde pro Sucheinheit
+ Index erstellen (POST /indexes): 12 pro Minute pro Sucheinheit
+ Index erstellen oder aktualisieren (PUT /indexes/myindex): 6 pro Sekunde pro Sucheinheit
+ Index löschen (DELETE /indexes/myindex): 12 pro Minute pro Sucheinheit 

Statische Grenzwerte für Anforderungsraten für Vorgänge in Bezug auf einen Dienst:

+ Dienststatistiken (GET /servicestats): 4 pro Sekunde pro Sucheinheit

## <a name="api-request-limits"></a>API-Anforderungsgrenzwerte
* Maximal 16 MB pro Anforderung <sup>1</sup>
* Maximale URL-Länge von 8 KB
* Maximal 1000 Dokumente pro Batch von Hochlade-, Zusammenführungs- oder Löschvorgängen für Indizes
* Maximal 32 Felder in $orderby-Klausel
* Maximale Suchbegriffgröße ist 32.766 Byte (32 KB minus 2 Bytes) von UTF-8-codiertem Text

<sup>1</sup> In der kognitiven Azure-Suche darf der Inhalt einer Anforderung nicht größer als 16 MB sein. Dies beschränkt möglicherweise den Inhalt einzelner Felder oder Sammlungen, für die keine anderen theoretischen Beschränkungen gelten. (Weitere Informationen zur Feldzusammensetzung und den Beschränkungen finden Sie unter [Unterstützte Datentypen](/rest/api/searchservice/supported-data-types).)

## <a name="api-response-limits"></a>API-Antwortengrenzwerte
* Maximale Rückgabe von 1000 Dokumenten pro Seite mit Suchergebnissen
* Maximale Rückgabe von 100 Vorschlägen pro Anforderung der Vorschlags-API

## <a name="api-key-limits"></a>API-Schlüsselgrenzwerte
API-Schlüssel werden für die Dienstauthentifizierung verwendet. Es gibt zwei Arten. Administratorschlüssel werden im Anforderungsheader angegeben und gewähren vollständigen Lese-und Schreibzugriff für den Dienst. Abfrageschlüssel sind schreibgeschützt, die in der URL angegeben und normalerweise an Clientanwendungen verteilt werden.

* Maximal 2 Administratorschlüssel pro Dienst
* Maximal 50 Abfrageschlüssel pro Dienst
