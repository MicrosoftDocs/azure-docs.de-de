---
title: Auswählen einer SKU oder eines Tarifs für den Azure Search-Dienst – Azure Search
description: 'Azure Search kann in folgenden SKUs bereitgestellt werden: „Free“, „Basic“ und „Standard“, wobei „Standard“ mit verschiedenen Ressourcenkonfigurationen und Kapazitäten verfügbar ist.'
services: search
author: HeidiSteen
manager: cgronlun
tags: azure-portal
ms.service: search
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 00422209302bbcc2139be4f6b490f0bb2816c051
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65539290"
---
# <a name="choose-a-pricing-tier-for-azure-search"></a>Auswählen eines Tarifs für Azure Search

Beim Erstellen eines Azure Search-Diensts erfolgt das [Erstellen einer Ressource](search-create-service-portal.md) basierend auf einem Tarif oder einer SKU, der/die für die Lebensdauer des Diensts festgelegt ist. Folgende Tarife sind verfügbar: „Free“, „Basic“, „Standard“ und „Speicheroptimiert“. „Standard“ und „Speicheroptimiert“ werden mit verschiedenen Konfigurationen und Kapazitäten bereitgestellt.

Die meisten Kunden starten mit dem Tarif „Free“, um den Dienst vorab zu testen. Später kann für Entwicklungs- und Produktionsbereitstellungen ein Upgrade auf einen höheren Tarif vorgenommen werden. Im Tarif „Free“ können alle Schnellstarts und Tutorials durchgeführt werden, auch jene für die ressourcenintensive kognitive Suche.

> [!NOTE]
> Microsoft bietet derzeit die Tarife vom Typ „Speicheroptimiert“ als günstigere Vorschauversionen zum Testen und Experimentieren an, um Feedback zu sammeln. Die endgültigen Preise werden später bekannt gegeben, wenn diese Tarife allgemein verfügbar sind. Wir raten davon ab, diese Tarife für Produktionsanwendungen zu verwenden.

Tarife geben die Merkmale der Hardware wieder, die den Dienst hostet (anstelle von Funktionen), und unterscheiden sich durch:

+ Anzahl der Indizes, die erstellt werden können
+ Größe und Geschwindigkeit von Partitionen (physischer Speicher)

Obwohl alle Tarife, einschließlich des Tarifs „Free“, in der Regel Featureparität bieten, können größere Workloads höhere Tarife erforderlich machen. Beispielsweise enthält die [KI-Indizierung mit Cognitive Services](cognitive-search-concept-intro.md) Qualifikationen mit langer Laufzeit, die bei einem kostenlosen Dienst zu einem Timeout führen, sofern es sich nicht um ein kleines Dataset handelt.

> [!NOTE] 
> Eine Ausnahme bei der Featureparität bilden die [Indexer](search-indexer-overview.md), die in „S3 HD“ nicht verfügbar sind.
>

Innerhalb eines Tarifs können Sie [Replikat- und Partitionsressourcen anpassen](search-capacity-planning.md), um hoch- oder herunterzuskalieren. Sie können mit jeweils ein oder zwei Replikaten und Partitionen beginnen und Ihre Rechenleistung dann für eine hohe Indizierungsworkload vorübergehend erhöhen. Die Möglichkeit, Ressourcenniveaus innerhalb eines Tarifs zu optimieren, sorgt für mehr Flexibilität, verkompliziert aber auch etwas Ihre Analyse. Möglicherweise müssen Sie experimentieren, um herauszufinden, ob ein niedrigerer Tarif mit mehr Ressourcen/Replikaten einen besseren Wert und höhere Leistung bietet als ein höherer Tarif mit weniger Ressourcen. Weitere Informationen dazu, wann und warum Sie Kapazität anpassen sollten, finden Sie unter [Überlegungen zur Leistung und Optimierung von Azure Search](search-performance-optimization.md).

## <a name="tiers-for-azure-search"></a>Tarife für Azure Search

In der folgenden Tabelle sind die verfügbaren Tarife aufgeführt. Weitere Informationen zu den verschiedenen Tarifen finden Sie auf der Seite [Azure Search – Preise](https://azure.microsoft.com/pricing/details/search/), im Artikel [Grenzwerte für den Azure Search-Dienst](search-limits-quotas-capacity.md) und auf der Portalseite beim Bereitstellen eines Diensts.

|Tarif | Capacity |
|-----|-------------|
|Kostenlos | Für andere Abonnenten freigegeben. Nicht skalierbar. Auf drei Indizes und 50 MB Speicherplatz begrenzt. |
|Basic | Dedizierte Computingressourcen für Produktionsworkloads mit geringerem Umfang. Eine 2-GB-Partition und bis zu drei Replikate. |
|Standard 1 (S1) | Ab „S1“ werden dedizierte Computer mit höherer Speicher- und Verarbeitungskapazität auf jeder Ebene verwendet. Für „S1“ beträgt die Partitionsgröße 25 GB/Partition (mit maximal 300 GB pro Dienst). |
|Standard 2 (S2) | Ähnlich wie „S1“, jedoch mit 100 GB/Partition (und maximal 1,2 TB pro Dienst). |
|Standard 3 (S3) | 200 GB/Partition (mit maximal 2,4 TB pro Dienst). |
|Standard 3 High Density (S3 HD) | High-density ist ein *Hostingmodus* für S3. Die zugrunde liegende Hardware ist für eine große Anzahl kleinerer Indizes optimiert und für Szenarios mit Mehrinstanzenfähigkeit konzipiert. Die Gebühr pro Einheit unterscheidet sich zwischen „S3 HD“ und „S3“ nicht. Die Hardware ist bei „S3 HD“ jedoch für schnelle Dateilesevorgänge bei einer großen Anzahl kleinerer Indizes optimiert.|
|Speicheroptimiert 1 (L1) | 1 TB/Partition (mit maximal 12 TB pro Dienst). |
|Speicheroptimiert 2 (L2) | 2 TB/Partition (mit maximal 24 TB pro Dienst). |

> [!NOTE] 
> Die speicheroptimierten Tarife bieten eine höhere Speicherkapazität zu einem niedrigeren Preis pro TB als die Standard-Tarife. Der größte Nachteil ist die höhere Wartezeit. Diese müssen Sie bei Ihren spezifischen Anwendungsanforderungen berücksichtigen.  Weitere Informationen zu den Leistungsaspekten dieses Tarifs finden Sie unter [Überlegungen zur Leistung und Optimierung von Azure Search](search-performance-optimization.md).
>

## <a name="how-billing-works"></a>Funktionsweise der Abrechnung

Bei Azure Search können auf drei Arten Kosten anfallen, dabei wird auch zwischen festen und variablen Komponenten unterschieden. In diesem Abschnitt werden die drei Abrechnungskomponenten „Basiskosten für den Dienst“, „Gebühren für ausgehende Daten“ und „Um KI erweiterte Indizierung“ beschrieben.

### <a name="core-service-costs-fixed-and-variable"></a>Basiskosten für den Dienst (fest und variabel)

Für den Dienst selbst fällt die Mindestgebühr für die erste Sucheinheit an (1 Replikat x 1 Partition). Dieser Betrag gilt für die Lebensdauer des Diensts, da der Dienst nicht mit weniger als dieser Konfiguration ausgeführt werden kann.

Über diese Mindestanforderung hinaus können Sie Replikate und Partitionen eigenständig hinzufügen. Beispielsweise können Sie nur Replikate oder nur Partitionen hinzufügen. Inkrementelle Kapazitätserweiterungen durch Replikate und Partitionen bilden die variable Kostenkomponente.

Die Abrechnung basiert auf einer [Formel (Replikate x Partitionen x Preis)](#search-units). Der abgerechnete Preis hängt von Ihrem ausgewählten Tarif ab.

Im folgenden Screenshot wird der Preis pro Einheit für die Tarife „Free“, „Basic“ und „S1“ dargestellt. (Die Tarife „S2“, „S3“, „L1“ und „L2“ werden nicht dargestellt.) Für „Basic“ werden durchschnittlich monatliche Kosten in Höhe des unter *price-1* angegebenen Werts an. Für „Standard“ werden durchschnittlich monatliche Kosten in Höhe des unter *price-2* angegebenen Werts fällig. Die Kosten pro Einheit steigen mit jedem Tarif an, da sich beim nächsthöheren Tarif jeweils die Rechenleistung und Speicherkapazität erhöhen. Die Preise für Azure Search sind auf der Seite [Azure Search – Preise](https://azure.microsoft.com/pricing/details/search/) verfügbar.

![Preise pro Einheit](./media/search-sku-tier/per-unit-pricing.png "Preise pro Einheit")

Wenn Sie Ihre Kosten für eine Suchlösung abschätzen möchten, bedenken Sie, dass sich Preis und Kapazität nicht linear verhalten. (Bei einer Verdopplung der Kapazität fallen mehr als doppelt so hohe Kosten an.) Ein Beispiel zur Funktionsweise der Formel finden Sie unter [Zuordnen von Replikaten und Partitionen](search-capacity-planning.md#how-to-allocate-replicas-and-partitions).

#### <a name="billing-based-on-search-units"></a>Auf Sucheinheiten basierende Abrechnung

Bei Azure Search-Vorgängen ist das wichtigste Abrechnungskonzept die *Sucheinheit* (Search Unit, SU). Da Azure Search bei der Indizierung und bei Abfragen sowohl von Replikaten als auch von Partitionen abhängig ist, ist es nicht sinnvoll, entweder nur nach dem einen oder nur nach dem anderen abzurechnen. Stattdessen basiert die Abrechnung auf einer Kombination beider.

Die SU ist das Produkt aus den von einem Dienst verwendeten *Replikaten* und *Partitionen*: **(R x P = SU)** .

Jeder Dienst beginnt mit mindestens einer SU (einem Replikat multipliziert mit einer Partition). Die maximale Anzahl von SUs für einen Dienst beträgt 36. Dieser Höchstwert kann auf verschiedene Weise erreicht werden: beispielsweise mit 6 Partitionen x 6 Replikaten oder mit 3 Partitionen x 12 Replikaten. Üblicherweise werden weniger als die Gesamtkapazität an SUs verwendet (3 Replikate x 3 Partitionen werden beispielsweise als 9 SUs abgerechnet). Gültige Kombinationen finden Sie in der Tabelle [Partitions- und Replikatskombinationen](search-capacity-planning.md#chart).

Der Preis wird stündlich pro SU abgerechnet. Dabei steigt der Preis mit jedem höheren Tarif an. Höhere Tarife enthalten größere und schnellere Partitionen, was zu einem insgesamt höheren Stundensatz für den jeweiligen Tarif führt. Die Preise für die einzelnen Tarife finden Sie auf der Seite [Azure Search – Preise](https://azure.microsoft.com/pricing/details/search/).

Die meisten Kunden schalten nur einen Teil der Gesamtkapazität online und halten den Rest in Reserve. Die Anzahl der Partitionen und Replikate, die Sie online schalten, bestimmt (mit der SU-Formel berechnet) den stündlichen Preis.

### <a name="data-egress-charges-during-indexing"></a>Gebühren für ausgehende Daten während der Indizierung

Die Verwendung von [Azure Search-Indexern](search-indexer-overview.md) kann je nach Standort der Dienste Auswirkungen auf die Kosten haben. Sie können die Gebühren für ausgehende Daten vollständig vermeiden, wenn Sie den Azure Search-Dienst in derselben Region wie Ihre Daten erstellen. Die folgenden Informationen stammen von der Seite [Preisübersicht Bandbreite](https://azure.microsoft.com/pricing/details/bandwidth/):

+ Microsoft berechnet keine Gebühren für in beliebige Azure-Dienste eingehende Daten oder für ausgehende Daten aus Azure Search.

+ Bei Lösungen mit mehreren Diensten fallen keine Gebühren für Datenübertragungen an, wenn sich alle Dienste in derselben Region befinden.

Für ausgehende Daten fallen Gebühren an, wenn sich die Dienste in verschiedenen Regionen befinden. Diese Gebühren sind nicht Teil Ihrer eigentlichen Azure Search-Rechnung. Sie werden hier jedoch erwähnt, weil die Kosten für eine Datenübertragung per Pull aus unterschiedlichen Regionen mithilfe von Daten oder um KI erweiterte Indexer in Ihrer Gesamtrechnung aufgeführt werden.

### <a name="ai-enriched-indexing-with-cognitive-services"></a>Um KI erweiterte Indizierung mit Cognitive Services

Für die [KI-Indizierung mit Cognitive Services](cognitive-search-concept-intro.md) sollten Sie eine abrechenbare Cognitive Services-Ressource in derselben Region wie Azure Search im S0-Tarif für die nutzungsbasierte Bezahlung der Verarbeitung anfügen. Durch das Anfügen von Cognitive Services fallen keine festen Kosten an. Sie bezahlen nur für die benötigte Verarbeitung.

Für die Extraktion von Bildern während der Dokumententschlüsselung fällt eine Azure Search-Gebühr an. Die Abrechnung erfolgt gemäß der Anzahl von Bildern, die Sie aus den Dokumenten extrahieren. Das Extrahieren von Text ist derzeit kostenlos.

Andere Erweiterungen, wie die Verarbeitung natürlicher Sprache, basieren auf [integrierten kognitiven Fähigkeiten](cognitive-search-predefined-skills.md) und werden im Rahmen einer Cognitive Services-Ressource abgerechnet. Dabei wird die gleiche Gebühr abgerechnet, als ob Sie die Aufgabe direkt mithilfe von Cognitive Services ausgeführt hätten. Weitere Informationen finden Sie unter [Anfügen einer Cognitive Services-Ressource an eine Qualifikationsgruppe](cognitive-search-attach-cognitive-services.md).

<a name="search-units"></a>

#### <a name="billing-for-image-extraction-in-cognitive-search"></a>Abrechnung für das Extrahieren von Bildern bei der kognitiven Suche

Wenn Sie Bilder aus Dateien in einer Indizierungspipeline für die kognitive Suche extrahieren, wird dieser Vorgang in Ihrer Azure Search-Rechnung abgerechnet. In einer [Indexerkonfiguration](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-parameters) löst der Parameter **imageAction** die Bildextraktion aus. Ist **imageAction** auf „None“ festgelegt (Standardeinstellung), fallen keine Gebühren für die Bildextraktion an.

Preisänderungen bleiben vorbehalten. Alle Preisänderungen werden auf der Seite [Azure Search – Preise](https://azure.microsoft.com/pricing/details/search/) dokumentiert.

#### <a name="billing-for-built-in-skills-in-cognitive-search"></a>Abrechnung für integrierte Qualifikationen bei der kognitiven Suche

Beim Einrichten einer Anreicherungspipeline basieren alle in der Pipeline verwendeten [integrierten Qualifikationen](cognitive-search-predefined-skills.md) auf Machine Learning-Modellen. Diese Modelle werden von Cognitive Services bereitgestellt. Die Verwendung dieser Modelle während der Indizierung wird mit der gleichen Gebühr abgerechnet, als ob Sie die Ressource direkt angefordert hätten.

Angenommen, Sie haben eine Pipeline, die optische Zeichenerkennung (Optical Character Recognition, OCR) für gescannte JPEG-Dateien verwendet. Dabei wird der resultierende Text per Push in einen Azure Search-Index für Freiformsuchabfragen übertragen. Ihre Indizierungspipeline würde einen Indexer mit der [Qualifikation „OCR“](cognitive-search-skill-ocr.md) enthalten, und diese Qualifikation wäre [einer Cognitive Services-Ressource zugeordnet](cognitive-search-attach-cognitive-services.md). Wenn Sie den Indexer ausführen, werden die Gebühren für die OCR-Ausführung auf Ihrer Rechnung der Cognitive Services-Ressourcen aufgeführt.

## <a name="tips-for-reducing-costs"></a>Tipps zur Reduzierung von Kosten

Sie können den Dienst zur Senkung der Kosten nicht herunterfahren. Dedizierte Ressourcen werden für eine exklusive Nutzung während der gesamten Lebensdauer des Diensts ständig betrieben. Die einzige Möglichkeit zur Kostenreduzierung besteht darin, Replikate und Partitionen auf ein Niveau zu senken, das immer noch eine akzeptable Leistung und die [Einhaltung der Vereinbarung zum Servicelevel (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/) bietet.

Beispielsweise können Sie einen Tarif mit einem niedrigeren Stundensatz auswählen. Stundensätze von S1 sind niedriger als die von S2 oder S3. Angenommen, Sie stellen einen Dienst am unteren Ende Ihrer voraussichtlichen Auslastungen bereit. Wenn dieser Dienst Ihre Anforderungen nicht mehr erfüllt, können Sie einen zweiten Dienst in einem höheren Tarif erstellen, Ihre Indizes für den zweiten Dienst neu erstellen und dann den ersten Dienst löschen.

Wenn Sie eine Kapazitätsplanung für lokale Server durchgeführt haben, wissen Sie, dass das Zukaufen eines höheren Tarifs üblich ist, um voraussichtliches Wachstum kompensieren zu können. Mit einem Clouddienst können Sie bei den Kosteneinsparungen aggressiver vorgehen, da Sie nicht an einen bestimmten Kauf gebunden sind. Reicht der aktuelle Dienst nicht aus, können Sie jederzeit in einen Dienst mit höherem Tarif wechseln.

### <a name="capacity"></a>Capacity

In Azure Search wird Kapazität in *Replikate* und *Partitionen* strukturiert.

+ Replikate sind Instanzen des Suchdiensts. Jedes Replikat hostet die Kopie eines Index mit Lastenausgleich. Beispielsweise verfügt ein Dienst mit sechs Replikaten über sechs Kopien jedes in den Dienst geladenen Index.

+ Partitionen speichern Indizes und teilen automatisch durchsuchbare Daten. Zwei Partitionen teilen den Index in zwei Teile, drei Partitionen in drei Teile und so weiter. Im Hinblick auf die Kapazität ist die *Partitionsgröße* das primäre Unterscheidungsmerkmal für Tarife.

> [!NOTE]
> Alle Tarife vom Typ „Standard“ und „Speicheroptimiert“ unterstützen [flexible Partitions- und Replikatskombinationen](search-capacity-planning.md#chart), sodass Sie durch Ändern des Verhältnisses [Ihr System in Hinblick auf Geschwindigkeit oder Speicher optimieren](search-performance-optimization.md) können. Der Tarif „Basic“ bietet bis zu drei Replikate für Hochverfügbarkeit, enthält jedoch nur eine Partition. Die Tarife vom Typ „Free“ bieten keine dedizierten Ressourcen: Computeressourcen werden von mehreren Abonnenten gemeinsam genutzt.

### <a name="more-about-service-limits"></a>Weitere Informationen zu Dienstgrenzwerten

Dienste hosten Ressourcen wie z. B. Indizes und Indexer. Für jeden Tarif gelten [Dienstgrenzwerte](search-limits-quotas-capacity.md) für die Anzahl von Ressourcen, die Sie erstellen können. Daher ist die maximale Anzahl der Indizes (und anderer Objekte) das zweite Unterscheidungsmerkmal für Tarife. Wenn Sie die einzelnen Optionen im Portal durchgehen, beachten Sie die Grenzwerte für die Anzahl der Indizes. Andere Ressourcen, z. B. Indexer, Datenquellen und Qualifikationsgruppen, sind an Indexgrenzwerte gebunden.

## <a name="consumption-patterns"></a>Verbrauchsmuster

Die meisten Kunden beginnen mit dem Tarif „Free“, den sie unbegrenzt beibehalten, und wählen dann einen der Tarife vom Typ „Standard“ oder „Speicheroptimiert“ für umfangreiche Entwicklungs- oder Produktionsworkloads aus.

![Azure Search-Tarife](./media/search-sku-tier/tiers.png "Azure Search-Tarife")

Im unteren und oberen Bereich sind die Tarife vom Typ „Basic“ und „S3 HD“ für wichtige, aber untypische Verbrauchsmuster verfügbar. „Basic“ eignet sich für kleine Produktionsworkloads. Dieser Tarif bietet SLAs, dedizierte Ressourcen und Hochverfügbarkeit, jedoch mit einer maximalen Größe von 2 GB einen moderaten Speicher. Er wurde für Kunden entwickelt, die die verfügbare Kapazität konstant unterschreiten. Am oberen Ende ist „S3 HD“ für Workloads vorgesehen, die typisch sind für unabhängige Softwarehersteller, Partner, [mehrinstanzenfähige Lösungen](search-modeling-multitenant-saas-applications.md) oder jede Konfiguration, die eine große Anzahl kleiner Indizes aufruft. Die Entscheidung für „Basic“ oder für „S3 HD“ ist oft eindeutig und wird schnell getroffen. Wenn Sie noch unsicher sind, finden Sie weitere Entscheidungshilfen auf der Seite [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) oder beim [Azure-Support](https://azure.microsoft.com/support/options/).

Die häufiger verwendeten Tarife vom Typ „Standard“ („S1“ bis „S3“) bieten zunehmende Kapazitätsstufen. Dabei liegen die Wendepunkte bei Partitionsgröße und maximaler Anzahl von Indizes, Indexern und entsprechenden Ressourcen:

|  | S1 | S2 | S3 |  |  |  |  |
|--|----|----|----|--|--|--|--|
| Partitionsgröße|  25 GB | 100 GB | 200 GB |  |  |  |  |
| Index- und Indexergrenzwerte| 50 | 200 | 200 |  |  |  |  |

„S1“ eignet sich für Kunden, die dedizierte Ressourcen und mehrere Partitionen benötigen. Dieser Tarif bietet eine Partitionsgröße von 25 GB und bis zu zwölf Partitionen sowie einen Grenzwert von 300 GB pro Dienst, wenn Sie Partitionen über Replikate maximieren. (Weitere ausgewogene Zuordnungen finden Sie unter [Zuordnen von Partitionen und Replikaten](search-capacity-planning.md#chart).)

Portal- und Preisseiten legen den Schwerpunkt auf Partitionsgröße und Speicher, aber für jeden Tarif nehmen die Computefunktionen (Datenträgerkapazität, Geschwindigkeit, CPUs) üblicherweise linear zum Preis zu. Ein Replikat im Tarif „S2“ ist schneller als in „S1“ und langsamer als in „S3“. Tarife vom Typ „S3“ durchbrechen das lineare Computepreismuster mit überproportional schnelleren E/A-Vorgängen. Wenn Sie erwarten, dass E/A-Vorgänge in Ihrem Fall den Engpass bilden werden, denken Sie daran, dass „S3“ wesentlich mehr IOPS bietet als niedrigere Tarife.

Die Tarife „S3“ und „S3 HD“ werden durch eine Infrastruktur mit identischer, sehr hoher Kapazität unterstützt, deren maximale Grenzwerte auf unterschiedliche Weise erreicht werden. „S3“ wurde für eine kleinere Anzahl sehr großer Indizes konzipiert, daher ist der maximale Grenzwert ressourcengebunden (2,4 TB pro Dienst). S3 HD ist für eine große Zahl sehr kleiner Indizes ausgelegt. Bei 1.000 Indizes erreicht S3 HD seine Grenzwerte in Form von Indexeinschränkungen. Wenn Sie als Kunde von „S3 HD“ mehr als 1.000 Indizes benötigen, erhalten Sie beim Microsoft-Support Informationen zur entsprechenden Vorgehensweise.

> [!NOTE]
> Bislang mussten Dokumentgrenzwerte berücksichtigt werden, doch dies gilt für neue Dienste nicht mehr. Weitere Informationen zu Bedingungen, für die immer noch Dokumentgrenzwerte gelten, finden Sie unter [Dokumentgrenzwerte](search-limits-quotas-capacity.md#document-limits).
>

Die Tarife vom Typ „Speicheroptimiert („L1“ und „L2“) sind ideal für Anwendungen mit hohen Datenanforderungen und relativ geringer Benutzeranzahl, bei denen die Minimierung der Abfragewartezeit nicht an erster Stelle steht.  

|  | L1 | L2 |  |  |  |  |  |
|--|----|----|--|--|--|--|--|
| Partitionsgröße|  1 TB | 2 TB |  |  |  |  |  |
| Index- und Indexergrenzwerte| 10 | 10 |  |  |  |  |  |

In „L2“ steht insgesamt die doppelte Speicherkapazität von „L1“ zur Verfügung.  Orientieren Sie sich bei der Wahl Ihres Tarifs an der maximalen Datenmenge, die für Ihren Index voraussichtlich erforderlich sein wird. Die Partitionen werden im Tarif „L1“ in Schritten von je 1 TB bis zu einer Maximalgröße von 12 TB hochskaliert. Die Partitionsgröße in „L2“ wird pro Partition in Schritten von 2 TB bis maximal 24 TB erhöht.

## <a name="evaluating-capacity"></a>Abschätzen der Kapazität

Die Kosten, die für das Ausführen eines Diensts anfallen, hängen direkt mit der Kapazität zusammen. Jeder Tarif enthält Grenzwerte auf zwei Ebenen: Speicher und Ressourcen. Der zuerst erreichte Grenzwert gilt – daher sollten Sie sich über beide Aspekte Gedanken machen.

In der Regel diktieren Geschäftsanforderungen die erforderliche Anzahl der Indizes. Für ein großes Repository von Dokumenten benötigen Sie beispielsweise einen globalen Index. Oder Sie benötigen möglicherweise je nach Region, Anwendung oder Geschäftsnische mehrere Indizes.

Um die Größe eines Indexes zu bestimmen, müssen Sie [einen erstellen](search-create-index-portal.md). Die Daten in Azure Search entsprechen in erster Linie der Struktur eines [invertierten Index](https://en.wikipedia.org/wiki/Inverted_index), der über andere Eigenschaften als die Quelldaten verfügt. Bei einem invertierten Index werden Größe und Komplexität vom Inhalt bestimmt, nicht notwendigerweise von der Menge der Daten, die Sie eingeben. Eine große Datenquelle mit hoher Redundanz könnte einen kleineren Index ergeben als ein kleineres Dataset mit stark variierendem Inhalt. Daher ist es kaum möglich, die Indexgröße aus der Größe des ursprünglichen Datasets abzuleiten.

> [!NOTE] 
> Auch wenn sich das Schätzen der künftig benötigten Indizes und des erforderlichen Speichers wie bloßes Mutmaßen anfühlt, lohnt sich der Aufwand. Stellt sich die Kapazität eines Tarifs als zu gering heraus, müssen Sie einen neuen Dienst in einem höheren Tarif bereitstellen und anschließend die [Indizes neu laden](search-howto-reindex.md). Für einen Dienst kann kein direktes SKU-Upgrade durchgeführt werden.
>

### <a name="step-1-develop-rough-estimates-by-using-the-free-tier"></a>Schritt 1: Entwickeln grober Schätzungen mit dem Tarif „Free“

Eine Möglichkeit zum Abschätzen der Kapazität besteht darin, mit dem Tarif „Free“ zu beginnen. Bedenken Sie, dass der Dienst vom Typ „Free“ bis zu drei Indizes, 50 MB Speicherplatz und 2 Minuten Indizierungszeit bietet. Es kann schwierig sein, mit diesen Einschränkungen eine voraussichtliche Indexgröße zu schätzen. Versuchen Sie es dennoch auf folgende Weise:

+ [Erstellen Sie einen kostenlosen Dienst](search-create-service-portal.md).
+ Bereiten Sie eine kleines, repräsentatives Dataset vor (z. B. 5.000 Dokumente und eine Stichprobengröße von 10 %).
+ [Erstellen Sie einen anfänglichen Index](search-create-index-portal.md), und notieren Sie sich dessen Größe im Portal (z. B. 30 MB).

Ist das Beispiel repräsentativ und umfasst es zehn Prozent der gesamten Datenquelle, dann werden aus einem Index von 30 MB ca. 300 MB, wenn alle Dokumente indiziert werden. Auf Grundlage dieses vorläufigen Werts könnten Sie diese Menge auf ein Budget für zwei Indizes (Entwicklung und Produktion) verdoppeln. Dies ergäbe einen Speicherbedarf von insgesamt 600 MB. Da diese Anforderung mühelos vom Tarif „Basic“ erfüllt wird, könnten Sie damit beginnen.

### <a name="step-2-develop-refined-estimates-by-using-a-billable-tier"></a>Schritt 2: Entwickeln präziser Schätzungen mit einem kostenpflichtigen Tarif

Einige Kunden beginnen lieber mit dedizierten Ressourcen, die für höhere Sampling- und Verarbeitungszeiten geeignet sind, und entwickeln anschließend während der Entwicklung realistische Schätzungen bezüglich Indexmenge, Größe und Abfragevolumen. Zunächst wird ein Dienst basierend auf einer Schätzung bereitgestellt. Mit fortschreitender Dauer des Entwicklungsprojekts wissen die Teams in der Regel, ob der derzeitige Dienst für die voraussichtlichen Produktionsworkloads über- oder unterdimensioniert ist.

1. [Überprüfen Sie die Dienstgrenzwerte in jedem Tarif](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#index-limits), um festzustellen, ob niedrigere Tarife die Anzahl der benötigten Indizes unterstützen können. In den Tarifen „Basic“, „S1“ und „S2“ betragen die Grenzwerte für Indizes 15, 50 bzw. 200. Der Tarif „Speicheroptimiert“ ist auf zehn Indizes beschränkt, da er für eine geringe Anzahl sehr großer Indizes ausgelegt ist.

1. [Erstellen Sie einen Dienst unter einem abzurechnenden Tarif](search-create-service-portal.md):

    + Wenn Sie am Anfang des Lernprozesses stehen, beginnen Sie mit einem niedrigen Tarif, wie z. B. „Basic“ oder „S1“.
    + Sind umfangreiche Indizierungs- und Abfragelasten vorgesehen, starten Sie mit einem höheren Tarif, wie „S2“ oder sogar „S3“.
    + Beginnen Sie mit einem Tarif vom Typ „Speicheroptimiert“ („L1“ oder „L2“), wenn Sie sehr viele Daten indizieren möchten und die Abfragelast relativ gering ist (etwa im Fall einer internen Geschäftsanwendung).

1. [Erstellen Sie einen anfänglichen Index](search-create-index-portal.md), um zu bestimmen, wie Quelldaten in einen Index übersetzt werden. Dies ist die einzige Möglichkeit, die Größe des Indexes zu schätzen.

1. [Überwachen Sie Speicher, Dienstgrenzwerte, Abfragevolumen und Latenz](search-monitor-usage.md) im Portal. Im Portal werden die Abfragen pro Sekunde, gedrosselte Abfragen und die Wartezeit bei Suchvorgängen angezeigt. Diese Werte können Ihnen dabei helfen, den richtigen Tarif auszuwählen. Sie können auch eine umfassende Überwachung von Werten, wie z. B. eine Analyse der Durchklickrate, durch Aktivieren der Option [Durchsuchen der Datenverkehrsanalyse](search-traffic-analytics.md) konfigurieren.

Anzahl und Größe von Indizes sind für die Analyse gleichermaßen wichtig. Die maximalen Grenzwerte werden durch vollständige Auslastung des Speichers (Partitionen) oder über die maximalen Grenzwerte von Ressourcen (Indizes, Indexer usw.) erreicht, je nachdem, was zuerst eintritt. Im Portal können Sie beides verfolgen, da die aktuelle Verwendung und die maximalen Grenzwerte nebeneinander auf der Seite „Übersicht“ angezeigt werden.

> [!NOTE]
> Der Speicherbedarf kann zunehmen, wenn Dokumente irrelevante Daten enthalten. Im Idealfall enthalten Dokumente nur die Daten, die Sie für die Suche benötigen. Binäre Daten sind nicht durchsuchbar und sollten separat gespeichert werden (beispielsweise in einer Azure-Tabelle oder in Azure Blob Storage). In diesem Fall sollte dem Index ein Feld mit einem URL-Verweis auf die externen Daten hinzugefügt werden. Die maximale Größe eines einzelnen Dokuments beträgt 16 MB (oder weniger, wenn Sie im Rahmen einer einzelnen Anforderung mehrere Dokumente gleichzeitig hochladen). Weitere Informationen finden Sie unter [Grenzwerte für den Azure Search-Dienst](search-limits-quotas-capacity.md).
>

**Überlegungen zum Abfragevolumen**

„Abfragen pro Sekunde“ (Queries per Second, QPS) ist eine wichtige Metrik bei der Leistungsoptimierung, spielt aber im Allgemeinen bei der Tarifauswahl nur dann eine Rolle, wenn Sie zu Beginn ein hohes Abfragevolumen erwarten.

Die Tarife vom Typ „Standard“ bieten ein ausgewogenes Verhältnis von Replikaten und Partitionen. Durch Hinzufügen von Replikaten für den Lastenausgleich oder durch Hinzufügen von Partitionen für eine parallele Verarbeitung können Sie kürzere Abfrageverarbeitungszeiten erzielen. Nach der Bereitstellung des Diensts können Sie die Leistung optimieren.

Ist von Anfang an ein hohes durchgängiges Abfragevolumen zu erwarten, sollten Sie höhere Tarife vom Typ „Standard“ in Verbindung mit einer leistungsfähigeren Hardware in Erwägung ziehen. Bleiben diese Abfragevolumen aus, können Sie Partitionen und Replikate offline schalten oder auch zu einem Dienst mit niedrigerem Tarif wechseln. Weitere Informationen zum Berechnen des Abfragedurchsatzes finden Sie unter [Überlegungen zur Leistung und Optimierung von Azure Search](search-performance-optimization.md).

Die Tarife vom Typ „Speicheroptimiert“ eigenen sich für Workloads mit großen Datenmengen und unterstützen insgesamt mehr verfügbaren Indexspeicher bei niedrigeren Anforderungen an die Abfragewartezeit. Dabei sollten Sie weiterhin zusätzliche Replikate für den Lastenausgleich und zusätzliche Partitionen für die parallele Verarbeitung verwenden. Nach der Bereitstellung des Diensts können Sie die Leistung optimieren.

**Vereinbarungen zum Servicelevel**

Die Funktionen des Tarifs „Free“ und der Vorschauversion bieten keine [Vereinbarungen zum Servicelevel (Service Level Agreements, SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Für alle abrechenbaren Tarife gelten SLAs, wenn Sie genügend Redundanz für Ihren Dienst bereitstellen. Für Abfrage-SLAs (Lesezugriff) benötigen Sie zwei oder mehr Replikate. Für Abfrage- und Indizierungs-SLAs (Lese-/Schreibzugriff) sind drei oder mehr Replikate erforderlich. Die Anzahl der Partitionen hat keine Auswirkungen auf die SLAs.

## <a name="tips-for-tier-evaluation"></a>Tipps zur Tarifbewertung

+ Informieren Sie sich darüber, wie Sie effiziente Indizes erstellen und welche Aktualisierungsmethoden die geringsten Auswirkungen haben. Informieren Sie sich auch über Abfrageaktivitäten, indem Sie die Option [Durchsuchen der Datenverkehrsanalyse](search-traffic-analytics.md) verwenden.

+ Erstellen Sie Metriken zu Abfragen, und sammeln Sie Daten zu Verwendungsmustern (Abfragen während der Geschäftszeiten, Indizierung außerhalb der Spitzenzeiten). Treffen Sie anhand dieser Daten Entscheidungen zur Dienstbereitstellung. Da ein stündliches oder tägliches Intervall ungeeignet ist, können Sie Partitionen und Ressourcen dynamisch so anpassen, dass geplante Änderungen von Abfragevolumen ermöglicht werden. Auch ungeplante, aber anhaltende Änderungen können berücksichtigt werden, wenn Ebenen beständig genug sind, um die Durchführung von Aktionen zu garantieren.

+ Beachten Sie, dass der einzige Nachteil einer unterdimensionierten Bereitstellung darin besteht, dass Sie ggf. einen Dienst entfernen müssen, wenn die tatsächlichen Anforderungen Ihre Schätzungen überschreiten. Um Dienstunterbrechungen zu vermeiden, sollten Sie einen neuen Dienst im gleichen Abonnement in einem höheren Tarif erstellen und parallel ausführen, bis alle Apps und Anforderungen auf den neuen Endpunkt abzielen.

## <a name="next-steps"></a>Nächste Schritte

Beginnen Sie mit einem Tarif vom Typ „Free“, und erstellen Sie einen anfänglichen Index mit einer Teilmenge Ihrer Daten, um deren Eigenschaften zu verstehen. Die Datenstruktur in Azure Search entspricht einem invertierten Index. Größe und Komplexität eines invertierten Index werden vom Inhalt bestimmt. Denken Sie daran, dass hoch redundanter Inhalt eher zu einem kleineren Index tendiert als sehr unregelmäßiger Inhalt. Daher werden die Speicheranforderungen für den Index durch die Inhaltseigenschaften und nicht durch die Größe des Datasets bestimmt.

Stellen Sie nach einer ersten Schätzung der Indexgröße [einen abrechenbaren Dienst](search-create-service-portal.md) in einem der folgenden Tarife bereit, die in diesem Artikel beschrieben werden: „Basic“, „Standard“ oder „Speicheroptimiert“. Lockern Sie künstliche Einschränkungen im Zusammenhang mit der Dimensionierung von Daten, und [erstellen Sie den Index neu](search-howto-reindex.md), um alle Daten einzuschließen, die durchsuchbar sein sollen.

[Ordnen Sie Partitionen und Replikate zu](search-capacity-planning.md), wie es erforderlich ist, um die gewünschte Leistung und Skalierung zu erzielen.

Wenn Sie mit der Leistung und Kapazität zufrieden sind, dann sind Sie fertig. Erstellen Sie andernfalls erneut einen Suchdienst in einem anderen Tarif, der Ihren Anforderungen eher entspricht.

> [!NOTE]
> Wenn Sie Fragen haben, posten Sie in [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search), oder wenden Sie sich an den [Azure-Support](https://azure.microsoft.com/support/options/).
