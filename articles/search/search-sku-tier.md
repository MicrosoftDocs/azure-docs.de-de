---
title: Auswählen eines Tarifs
titleSuffix: Azure Cognitive Search
description: 'Azure Cognitive Search kann in folgenden Dienstebenen bereitgestellt werden: „Free“, „Basic“ und „Standard“, wobei „Standard“ mit verschiedenen Ressourcenkonfigurationen und Kapazitäten verfügbar ist.'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: 3f2cbd7afe206866ae4d5b7c0925c8f3be9ab785
ms.sourcegitcommit: 65a4f2a297639811426a4f27c918ac8b10750d81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96558809"
---
# <a name="choose-a-pricing-tier-for-azure-cognitive-search"></a>Auswählen eines Tarifs für die kognitive Azure-Suche

Beim [Erstellen eines Suchdiensts](search-create-service-portal.md) wählen Sie einen Tarif, der für die Lebensdauer des Diensts festgelegt ist. Der ausgewählte Tarif bestimmt:

+ Anzahl der Indizes und anderer Objekte (maximale Grenzwerte)
+ Größe und Geschwindigkeit von Partitionen (physischer Speicher)
+ Die abrechenbare Rate, eine festgelegte monatliche Gebühr, aber auch inkrementelle Kosten, wenn Sie Partitionen oder Replikate hinzufügen

Außerdem gelten für einige [Premium-Features](#premium-features) Tarifanforderungen.

## <a name="tier-descriptions"></a>Tarifbeschreibungen

Folgende Tarife sind verfügbar: **Free**, **Basic**, **Standard** und **Speicheroptimiert**. „Standard“ und „Speicheroptimiert“ werden mit verschiedenen Konfigurationen und Kapazitäten bereitgestellt.

Im folgenden Screenshot aus dem Azure-Portal sind die verfügbaren Tarife ohne Preise aufgeführt (die Preise finden Sie im Portal und auf der [Seite mit der Preisübersicht](https://azure.microsoft.com/pricing/details/search/)). 

![Tarife der kognitiven Azure-Suche](media/search-sku-tier/tiers.png "Tarife der kognitiven Azure-Suche")

Mit **Free** wird ein eingeschränkter Suchdienst für kleinere Projekte wie das Ausführen von Tutorials und Codebeispielen erstellt. Intern werden Replikate und Partitionen von mehreren Abonnenten gemeinsam genutzt. Es ist nicht möglich, einen kostenlosen Dienst zu skalieren oder signifikante Workloads auszuführen.

**Basic** und **Standard** sind die am häufigsten verwendeten abrechenbaren Tarife, wobei **Standard** der vorgegebene Tarif ist. Wenn Sie dedizierte Ressourcen nutzen, können Sie größere Projekte bereitstellen, die Leistung optimieren und die Kapazität steigern.

Einige Tarife sind für bestimmte Verwendungszwecke optimiert. **Standard 3 High Density (S3 HD)** ist beispielsweise ein *Hostingmodus* für S3. Die zugrunde liegende Hardware ist für eine große Anzahl kleinerer Indizes optimiert und für Szenarien mit Mehrinstanzenfähigkeit konzipiert. Die Gebühr pro Einheit unterscheidet sich zwischen „S3 HD“ und „S3“ nicht. Die Hardware ist bei „S3 HD“ jedoch für schnelle Dateilesevorgänge bei einer großen Anzahl kleinerer Indizes optimiert.

**Datenspeicheroptimierte** Tarife bieten eine höhere Speicherkapazität zu einem niedrigeren Preis pro TB als die Standard-Tarife. Der größte Nachteil ist die höhere Wartezeit. Diese müssen Sie bei Ihren spezifischen Anwendungsanforderungen berücksichtigen. Weitere Informationen zu den Leistungsaspekten dieses Tarifs finden Sie unter [Überlegungen zur Leistung und Optimierung von Azure Search](search-performance-optimization.md).

Weitere Informationen zu den verschiedenen Tarifen finden Sie auf der [Seite mit der Preisübersicht](https://azure.microsoft.com/pricing/details/search/), im Artikel [Grenzwerte für den Dienst für die kognitive Azure-Suche](search-limits-quotas-capacity.md) und auf der Portalseite beim Bereitstellen eines Diensts.

<a name="premium-features"></a>

## <a name="feature-availability-by-tier"></a>Funktionsverfügbarkeit pro Tarif

In der folgenden Tabelle werden die tarifbezogenen Featureeinschränkungen beschrieben.

| Funktion | Einschränkungen |
|---------|-------------|
| [Indexer](search-indexer-overview.md) | Indexer sind auf S3 HD nicht verfügbar.  |
| [KI-Anreicherung](search-security-manage-encryption-keys.md) | Wird im Free-Tarif ausgeführt, aber nicht empfohlen. |
| [Von Kunden verwaltete Verschlüsselungsschlüssel](search-security-manage-encryption-keys.md) | Im Free-Tarif nicht verfügbar. |
| [IP-Firewallzugriff](service-configure-firewall.md) | Im Free-Tarif nicht verfügbar. |
| [Privater Endpunkt (Integration mit Azure Private Link)](service-create-private-endpoint.md) | Für eingehende Verbindungen bei einem Suchdienst, nicht im Free-Tarif verfügbar. Für ausgehende Verbindungen von Indexern zu anderen Azure-Ressourcen, nicht verfügbar im Tarif „Free“ oder „S3 HD“. Für Indexer, die Skillsets verwenden, nicht verfügbar im Tarif „Free“, „Basic“, „S1“ oder „S3 HD“|

Die meisten Features sind in jedem Tarif verfügbar, auch im Free-Tarif, aber ressourcenintensive Features funktionieren möglicherweise nicht gut, wenn ihnen keine ausreichende Kapazität zugewiesen wurde. Beispielsweise umfasst die [KI-Anreicherung](cognitive-search-concept-intro.md) Skills mit langer Laufzeit, die bei einem kostenlosen Dienst zu einem Timeout führen, sofern es sich nicht um ein kleines Dataset handelt.

## <a name="billable-events"></a>Abrechenbare Ereignisse

Für eine auf der kognitiven Azure-Suche basierende Lösung können folgende Kosten anfallen:

+ [Kosten für den Dienst](#service-costs) selbst, der rund um die Uhr mit einer minimalen Konfiguration (eine Partition und ein Replikat) zum Basistarif ausgeführt wird.

+ Hinzufügen von Kapazität (Replikate oder Partitionen), wobei die Kosten in Inkrementen der abrechenbaren Rate steigen

+ Bandbreitengebühren (ausgehende Datenübertragung)

+ Für bestimmte Funktionen oder Features erforderliche Add-On-Dienste:

  + KI-Anreicherung (erfordert [Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/))
  + Wissensspeicher (erfordert [Azure Storage](https://azure.microsoft.com/pricing/details/storage/))
  + Inkrementelle Anreicherung (erfordert [Azure Storage](https://azure.microsoft.com/pricing/details/storage/), gilt für KI-Anreicherung)
  + Kundenseitig verwaltete Schlüssel und doppelte Verschlüsselung (erfordert [Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/))
  + Private Endpunkte für ein Zugriffsmodell ohne Internet (erfordert [Azure Private Link](https://azure.microsoft.com/pricing/details/private-link/))

### <a name="service-costs"></a>Dienstkosten

Im Unterschied zu virtuellen Computern oder anderen Ressourcen, die angehalten werden können, um Gebühren zu vermeiden, ist ein Dienst für die kognitive Azure-Suche immer auf der Hardware verfügbar, die für Ihre ausschließliche Nutzung bestimmt ist. Daher ist die Erstellung eines Diensts ein abrechenbares Ereignis, das mit dem Erstellen des Diensts beginnt und mit dem Löschen des Diensts endet. 

Die Mindestgebühr fällt zum abrechenbaren Preis für die erste Sucheinheit (ein Replikat × eine Partition) an. Dieser Betrag gilt für die Lebensdauer des Diensts, da der Dienst nicht mit weniger als dieser Konfiguration ausgeführt werden kann. Über dieses Minimum hinaus können Sie Replikate und Partitionen unabhängig voneinander hinzufügen. Inkrementelle Kapazitätserweiterungen durch Replikate und Partitionen erhöhen die Abrechnung basierend auf der folgenden Formel: [(Replikate × Partitionen × Preis)](#search-units). Der abgerechnete Preis hängt dabei von Ihrem ausgewählten Tarif ab.

Wenn Sie Ihre Kosten für eine Suchlösung abschätzen möchten, bedenken Sie, dass sich Preis und Kapazität nicht linear verhalten. (Bei einer Verdopplung der Kapazität fallen mehr als doppelt so hohe Kosten an.) Ein Beispiel zur Funktionsweise der Formel finden Sie unter [Zuordnen von Replikaten und Partitionen](search-capacity-planning.md#how-to-allocate-replicas-and-partitions).

### <a name="bandwidth-charges"></a>Bandbreitengebühren

Die Verwendung von [Indexern](search-indexer-overview.md) kann je nach Standort der Dienste Auswirkungen auf die Kosten haben. Sie können die Gebühren für ausgehende Daten vollständig vermeiden, wenn Sie den Dienst für die kognitive Azure-Suche in derselben Region wie Ihre Daten erstellen. Die folgenden Informationen stammen von der Seite [Preisübersicht Bandbreite](https://azure.microsoft.com/pricing/details/bandwidth/):

+ Microsoft berechnet keine Gebühren für in beliebige Azure-Dienste eingehende Daten oder für ausgehende Daten aus der kognitiven Azure-Suche.
+ Bei Lösungen mit mehreren Diensten fallen keine Gebühren für Datenübertragungen an, wenn sich alle Dienste in derselben Region befinden.

Für ausgehende Daten fallen Gebühren an, wenn sich die Dienste in verschiedenen Regionen befinden. Diese Gebühren sind nicht Teil Ihrer eigentlichen Rechnung für die kognitive Azure-Suche. Sie werden hier jedoch erwähnt, weil die Kosten für eine Datenübertragung per Pull aus unterschiedlichen Regionen mithilfe von Daten oder um KI erweiterte Indexer in Ihrer Gesamtrechnung aufgeführt werden.

### <a name="ai-enrichment-with-cognitive-services"></a>KI-Anreicherung mit Cognitive Services

Für die [KI-Anreicherung](cognitive-search-concept-intro.md) sollten Sie eine [abrechenbare Azure Cognitive Services-Ressource](cognitive-search-attach-cognitive-services.md) in derselben Region wie die kognitive Azure-Suche im S0-Tarif für die nutzungsbasierte Bezahlung der Verarbeitung anfügen. Durch das Anfügen von Cognitive Services fallen keine festen Kosten an. Sie bezahlen nur für die benötigte Verarbeitung.

| Vorgang | Auswirkungen auf die Abrechnung |
|-----------|----------------|
| Dokumententschlüsselung, Textextraktion | Kostenlos |
| Dokumententschlüsselung, Bildextraktion | Die Abrechnung erfolgt entsprechend der Anzahl von Bildern, die aus den Dokumenten extrahiert werden. In einer [Indexerkonfiguration](/rest/api/searchservice/create-indexer#indexer-parameters) löst der Parameter **imageAction** die Bildextraktion aus. Ist **imageAction** auf „None“ festgelegt (Standardeinstellung), fallen keine Gebühren für die Bildextraktion an. Die Preise für die Bildextraktion sind auf der Seite mit [Preisdetails](https://azure.microsoft.com/pricing/details/search/) für die kognitive Azure-Suche dokumentiert.|
| [Integrierte kognitive Qualifikationen](cognitive-search-predefined-skills.md) | Wird zum gleichen Preis abgerechnet, als ob Sie die Aufgabe direkt mithilfe von Cognitive Services ausgeführt hätten. |
| Benutzerdefinierte Qualifikationen | Bei einer benutzerdefinierten Qualifikation handelt es sich um die von Ihnen bereitgestellte Funktionalität. Die Kosten für die Verwendung einer benutzerdefinierten Qualifikation hängen ganz davon ab, ob mit dem benutzerdefinierten Code andere gebührenpflichtige Dienste aufgerufen werden. |

Mit dem Feature [Inkrementelle Anreicherung (Vorschau)](cognitive-search-incremental-indexing-conceptual.md) können Sie einen Cache bereitstellen, durch den der Indexer beim Ausführen nur der kognitiven Fähigkeiten, die beim zukünftigen Ändern Ihres Skillsets notwendig sind, effizienter arbeiten kann. Das spart Zeit und Geld.

<a name="search-units"></a>

## <a name="billing-formula-r-x-p--su"></a>Formel für die Abrechnung (R × P = SU)

Bei Vorgängen der kognitiven Azure-Suche ist das wichtigste Abrechnungskonzept die *Sucheinheit* (Search Unit, SU). Da die kognitive Azure-Suche bei der Indizierung und bei Abfragen sowohl von Replikaten als auch von Partitionen abhängig ist, ist es nicht sinnvoll, entweder nur nach dem einen oder nur nach dem anderen abzurechnen. Stattdessen basiert die Abrechnung auf einer Kombination beider.

Die SU ist das Produkt aus den von einem Dienst verwendeten *Replikaten* und *Partitionen*: **(R x P = SU)** .

Jeder Dienst beginnt mit mindestens einer SU (einem Replikat multipliziert mit einer Partition). Die maximale Anzahl von SUs für einen Dienst beträgt 36. Dieser Höchstwert kann auf verschiedene Weise erreicht werden: beispielsweise mit 6 Partitionen x 6 Replikaten oder mit 3 Partitionen x 12 Replikaten. Üblicherweise werden weniger als die Gesamtkapazität an SUs verwendet (3 Replikate x 3 Partitionen werden beispielsweise als 9 SUs abgerechnet). Gültige Kombinationen finden Sie in der Tabelle [Partitions- und Replikatskombinationen](search-capacity-planning.md#chart).

Der Preis wird stündlich pro SU abgerechnet. Dabei steigt der Preis mit jedem höheren Tarif an. Höhere Tarife enthalten größere und schnellere Partitionen, was zu einem insgesamt höheren Stundensatz für den jeweiligen Tarif führt. Die Preise für die einzelnen Tarife finden Sie auf der Seite [Azure Search – Preise](https://azure.microsoft.com/pricing/details/search/).

Die meisten Kunden schalten nur einen Teil der Gesamtkapazität online und halten den Rest in Reserve. Die Anzahl der Partitionen und Replikate, die Sie online schalten, bestimmt (mit der SU-Formel berechnet) den stündlichen Preis.

## <a name="how-to-manage-costs"></a>Verwalten von Kosten

Die folgenden Vorschläge können Ihnen helfen, die Kosten zu senken oder effektiver zu verwalten:

+ Erstellen Sie alle Ressourcen in derselben Region oder in möglichst wenigen Regionen, um Bandbreitengebühren zu minimieren oder auszuschließen.

+ Führen Sie alle Dienste in einer Ressourcengruppe zusammen, z.B. kognitive Azure-Suche, Cognitive Services und alle anderen in Ihrer Lösung verwendeten Azure-Dienste. Navigieren Sie im Azure-Portal zu der Ressourcengruppe, und verwenden Sie die Befehle für **Kostenverwaltung**, um Einblicke in die tatsächlichen und projizierten Ausgaben zu erhalten.

+ Sie können Azure App Service für Ihre Front-End-Anwendung verwenden, sodass Anforderungen und Antworten innerhalb der Grenzen des Rechenzentrums bleiben.

+ Führen Sie die Hochskalierung für ressourcenintensive Vorgänge wie die Indizierung durch, und passen Sie die Skalierung anschließend für reguläre Abfrageworkloads wieder an. Beginnen Sie mit der Mindestkonfiguration für die kognitive Azure-Suche (eine SU bestehend aus einer Partition und einem Replikat), und überwachen Sie dann die Benutzeraktivität, um Verwendungsmuster zu identifizieren, die auf die Notwendigkeit einer höheren Kapazität hindeuten. Wenn ein vorhersagbares Muster vorliegt, können Sie möglicherweise die Skalierung mit der Aktivität synchronisieren (um dies zu automatisieren, müssen Sie Code schreiben).

Integrierte Tools und Features im Zusammenhang mit Ausgaben finden Sie außerdem unter [Abrechnung und Kostenverwaltung](../cost-management-billing/cost-management-billing-overview.md).

Das temporäre Herunterfahren eines Suchdiensts ist nicht möglich. Dedizierte Ressourcen werden für eine exklusive Nutzung während der gesamten Lebensdauer des Diensts ständig betrieben. Das Löschen eines Diensts ist endgültig und löscht auch die zugehörigen Daten.

Im Hinblick auf den Dienst selbst besteht die einzige Möglichkeit zur Kostenreduzierung darin, Replikate und Partitionen auf ein Niveau zu reduzieren, das immer noch eine akzeptable Leistung und [SLA-Konformität](https://azure.microsoft.com/support/legal/sla/search/v1_0/) bietet, oder einen Dienst mit einem niedrigeren Tarif zu erstellen. (Die Stundensätze von S1 sind niedriger als die von S2 oder S3.) Angenommen, Sie stellen einen Dienst am unteren Ende Ihrer voraussichtlichen Auslastungen bereit. Wenn dieser Dienst Ihre Anforderungen nicht mehr erfüllt, können Sie einen zweiten Dienst in einem höheren Tarif erstellen, Ihre Indizes für den zweiten Dienst neu erstellen und dann den ersten Dienst löschen.

## <a name="how-to-evaluate-capacity-requirements"></a>Auswerten von Kapazitätsanforderungen

In der kognitiven Azure-Suche wird Kapazität in *Replikate* und *Partitionen* strukturiert.

+ Replikate sind Instanzen des Suchdiensts. Jedes Replikat hostet die Kopie eines Index mit Lastenausgleich. Beispielsweise verfügt ein Dienst mit sechs Replikaten über sechs Kopien jedes in den Dienst geladenen Index.

+ Partitionen speichern Indizes und teilen automatisch durchsuchbare Daten. Zwei Partitionen teilen den Index in zwei Teile, drei Partitionen in drei Teile und so weiter. Im Hinblick auf die Kapazität ist die *Partitionsgröße* das primäre Unterscheidungsmerkmal für Tarife.

> [!NOTE]
> Alle Tarife vom Typ „Standard“ und „Speicheroptimiert“ unterstützen [flexible Partitions- und Replikatskombinationen](search-capacity-planning.md#chart), sodass Sie durch Ändern des Verhältnisses [Ihr System in Hinblick auf Geschwindigkeit oder Speicher optimieren](search-performance-optimization.md) können. Der Tarif „Basic“ bietet bis zu drei Replikate für Hochverfügbarkeit, enthält jedoch nur eine Partition. Die Tarife vom Typ „Free“ bieten keine dedizierten Ressourcen: Computeressourcen werden von mehreren Abonnenten gemeinsam genutzt.

### <a name="evaluating-capacity"></a>Abschätzen der Kapazität

Zwischen der Kapazität und den Kosten für die Ausführung des Diensts besteht ein direkter Zusammenhang. Tarife sind mit Grenzwerten in zwei Ebenen verbunden: Speicher und Inhalt (z. B. Anzahl der Indizes). Der zuerst erreichte Grenzwert gilt – daher sollten Sie sich über beide Aspekte Gedanken machen.

In der Regel diktieren Geschäftsanforderungen die erforderliche Anzahl der Indizes. Für ein großes Repository von Dokumenten benötigen Sie beispielsweise einen globalen Index. Oder Sie benötigen möglicherweise je nach Region, Anwendung oder Geschäftsnische mehrere Indizes.

Um die Größe eines Indexes zu bestimmen, müssen Sie [einen erstellen](search-what-is-an-index.md). Die Größe basiert auf den importierten Daten und der Indexkonfiguration, also beispielsweise, ob Sie Vorschlagsfunktionen, die Filterung und Sortierung aktivieren.

Bei der Volltextsuche entspricht die primäre Datenstruktur der Struktur eines [invertierten Indexes](https://en.wikipedia.org/wiki/Inverted_index), die über andere Eigenschaften als die Quelldaten verfügt. Bei einem invertierten Index werden Größe und Komplexität vom Inhalt bestimmt, nicht notwendigerweise von der Menge der Daten, die Sie eingeben. Eine große Datenquelle mit hoher Redundanz könnte einen kleineren Index ergeben als ein kleineres Dataset mit stark variierendem Inhalt. Daher ist es kaum möglich, die Indexgröße aus der Größe des ursprünglichen Datasets abzuleiten.

> [!NOTE] 
> Auch wenn sich das Schätzen der künftig benötigten Indizes und des erforderlichen Speichers wie bloßes Mutmaßen anfühlt, lohnt sich der Aufwand. Stellt sich die Kapazität eines Tarifs als zu gering heraus, müssen Sie einen neuen Dienst in einem höheren Tarif bereitstellen und anschließend die [Indizes neu laden](search-howto-reindex.md). Für einen Dienst kann kein direktes Upgrade von einem Tarif auf einen anderen durchgeführt werden.
>

### <a name="estimate-with-the-free-tier"></a>Schätzung mit dem Free-Tarif

Eine Möglichkeit zum Abschätzen der Kapazität besteht darin, mit dem Tarif „Free“ zu beginnen. Bedenken Sie, dass der Dienst vom Typ „Free“ bis zu drei Indizes, 50 MB Speicherplatz und 2 Minuten Indizierungszeit bietet. Es kann schwierig sein, mit diesen Einschränkungen eine voraussichtliche Indexgröße zu schätzen. Sie können es dennoch wie folgt versuchen:

+ [Erstellen Sie einen kostenlosen Dienst](search-create-service-portal.md).
+ Bereiten Sie ein kleines repräsentatives Dataset vor.
+ [Erstellen Sie einen anfänglichen Index im Portal](search-get-started-portal.md), und notieren Sie sich die Größe. Funktionen und Attribute haben Auswirkungen auf den Speicher. Durch das Hinzufügen von Vorschlagsfunktionen (Search-as-you-Type-Abfragen) erhöhen sich beispielsweise die Speicheranforderungen. Sie können mehrere Versionen eines Index unter Verwendung desselben Datasets erstellen und dabei verschiedene Attribute für die einzelnen Felder verwenden, um zu ermitteln, wie sich die Speicheranforderungen ändern. Weitere Informationen finden Sie [im Abschnitt „Hinweise zum Speicher“ unter „Erstellen eines einfachen Index“](search-what-is-an-index.md#index-size).

Mit einer groben Schätzung können Sie die betreffende Größe für zwei Indizes verdoppeln (Entwicklung und Produktion) und dann Ihren Tarif entsprechend auswählen.

### <a name="estimate-with-a-billable-tier"></a>Schätzung mit einem abrechenbaren Tarif

Dedizierte Ressourcen ermöglichen längere Sampling- und Verarbeitungszeiten und eignen sich besser für realistische Schätzungen bezüglich Indexmenge, Größe und Abfragevolumen während der Entwicklung. Einige Kunden beginnen direkt mit einem abrechenbaren Tarif und werten ihn im Lauf des Entwicklungsprojekts erneut aus.

1. [Überprüfen Sie die Dienstgrenzwerte in jedem Tarif](./search-limits-quotas-capacity.md#index-limits), um festzustellen, ob niedrigere Tarife die Anzahl der benötigten Indizes unterstützen können. In den Tarifen „Basic“, „S1“ und „S2“ betragen die Grenzwerte für Indizes 15, 50 bzw. 200. Der Tarif „Speicheroptimiert“ ist auf zehn Indizes beschränkt, da er für eine geringe Anzahl sehr großer Indizes ausgelegt ist.

1. [Erstellen Sie einen Dienst unter einem abzurechnenden Tarif](search-create-service-portal.md):

    + Beginnen Sie mit einem niedrigen Tarif, z. B. „Basic“ oder „S1“, wenn Sie sich über die projizierte Last nicht sicher sind.
    + Sind umfangreiche Indizierungs- und Abfragelasten vorgesehen, starten Sie mit einem höheren Tarif, wie „S2“ oder sogar „S3“.
    + Beginnen Sie mit einem Tarif vom Typ „Speicheroptimiert“ („L1“ oder „L2“), wenn Sie sehr viele Daten indizieren möchten und die Abfragelast relativ gering ist (etwa im Fall einer internen Geschäftsanwendung).

1. [Erstellen Sie einen anfänglichen Index](search-what-is-an-index.md), um zu bestimmen, wie Quelldaten in einen Index übersetzt werden. Dies ist die einzige Möglichkeit, die Größe des Indexes zu schätzen.

1. [Überwachen Sie Speicher, Dienstgrenzwerte, Abfragevolumen und Latenz](search-monitor-usage.md) im Portal. Im Portal werden die Abfragen pro Sekunde, gedrosselte Abfragen und die Wartezeit bei Suchvorgängen angezeigt. Diese Werte können Ihnen dabei helfen, den richtigen Tarif auszuwählen. 

Anzahl und Größe von Indizes sind für die Analyse gleichermaßen wichtig. Die maximalen Grenzwerte werden durch vollständige Auslastung des Speichers (Partitionen) oder über die maximalen Grenzwerte von Ressourcen (Indizes, Indexer usw.) erreicht, je nachdem, was zuerst eintritt. Im Portal können Sie beides verfolgen, da die aktuelle Verwendung und die maximalen Grenzwerte nebeneinander auf der Seite „Übersicht“ angezeigt werden.

> [!NOTE]
> Der Speicherbedarf kann zunehmen, wenn Dokumente irrelevante Daten enthalten. Im Idealfall enthalten Dokumente nur die Daten, die Sie für die Suche benötigen. Binäre Daten sind nicht durchsuchbar und sollten separat gespeichert werden (beispielsweise in einer Azure-Tabelle oder in Azure Blob Storage). In diesem Fall sollte dem Index ein Feld mit einem URL-Verweis auf die externen Daten hinzugefügt werden. Die maximale Größe eines einzelnen Dokuments beträgt 16 MB (oder weniger, wenn Sie im Rahmen einer einzelnen Anforderung mehrere Dokumente gleichzeitig hochladen). Weitere Informationen finden Sie unter [Grenzwerte für den Dienst für die kognitive Azure-Suche](search-limits-quotas-capacity.md).
>

**Überlegungen zum Abfragevolumen**

„Abfragen pro Sekunde“ (Queries per Second, QPS) ist eine wichtige Metrik bei der Leistungsoptimierung, spielt aber im Allgemeinen bei der Tarifauswahl nur dann eine Rolle, wenn Sie zu Beginn ein hohes Abfragevolumen erwarten.

Die Tarife vom Typ „Standard“ bieten ein ausgewogenes Verhältnis von Replikaten und Partitionen. Durch Hinzufügen von Replikaten für den Lastenausgleich oder durch Hinzufügen von Partitionen für eine parallele Verarbeitung können Sie kürzere Abfrageverarbeitungszeiten erzielen. Nach der Bereitstellung des Diensts können Sie die Leistung optimieren.

Ist von Anfang an ein hohes durchgängiges Abfragevolumen zu erwarten, sollten Sie höhere Tarife vom Typ „Standard“ in Verbindung mit einer leistungsfähigeren Hardware in Erwägung ziehen. Bleiben diese Abfragevolumen aus, können Sie Partitionen und Replikate offline schalten oder auch zu einem Dienst mit niedrigerem Tarif wechseln. Weitere Informationen zum Berechnen des Abfragedurchsatzes finden Sie unter [Überlegungen zur Leistung und Optimierung der kognitiven Azure-Suche](search-performance-optimization.md).

Die Tarife vom Typ „Speicheroptimiert“ eigenen sich für Workloads mit großen Datenmengen und unterstützen insgesamt mehr verfügbaren Indexspeicher bei niedrigeren Anforderungen an die Abfragewartezeit. Dabei sollten Sie weiterhin zusätzliche Replikate für den Lastenausgleich und zusätzliche Partitionen für die parallele Verarbeitung verwenden. Nach der Bereitstellung des Diensts können Sie die Leistung optimieren.

**Vereinbarungen zum Servicelevel**

Die Funktionen des Tarifs „Free“ und der Vorschauversion bieten keine [Vereinbarungen zum Servicelevel (Service Level Agreements, SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Für alle abrechenbaren Tarife gelten SLAs, wenn Sie genügend Redundanz für Ihren Dienst bereitstellen. Für Abfrage-SLAs (Lesezugriff) benötigen Sie zwei oder mehr Replikate. Für Abfrage- und Indizierungs-SLAs (Lese-/Schreibzugriff) sind drei oder mehr Replikate erforderlich. Die Anzahl der Partitionen hat keine Auswirkungen auf die SLAs.

## <a name="tips-for-tier-evaluation"></a>Tipps zur Tarifbewertung

+ Erstellen Sie Metriken zu Abfragen, und sammeln Sie Daten zu Verwendungsmustern (Abfragen während der Geschäftszeiten, Indizierung außerhalb der Spitzenzeiten). Treffen Sie anhand dieser Daten Entscheidungen zur Dienstbereitstellung. Da ein stündliches oder tägliches Intervall ungeeignet ist, können Sie Partitionen und Ressourcen dynamisch so anpassen, dass geplante Änderungen von Abfragevolumen ermöglicht werden. Auch ungeplante, aber anhaltende Änderungen können berücksichtigt werden, wenn Ebenen beständig genug sind, um die Durchführung von Aktionen zu garantieren.

+ Beachten Sie, dass der einzige Nachteil einer unterdimensionierten Bereitstellung darin besteht, dass Sie ggf. einen Dienst entfernen müssen, wenn die tatsächlichen Anforderungen Ihre Schätzungen überschreiten. Um Dienstunterbrechungen zu vermeiden, sollten Sie einen neuen Dienst in einem höheren Tarif erstellen und parallel ausführen, bis alle Apps und Anforderungen den neuen Endpunkt zum Ziel haben.

## <a name="next-steps"></a>Nächste Schritte

Beginnen Sie mit einem Tarif vom Typ „Free“, und erstellen Sie einen anfänglichen Index mit einer Teilmenge Ihrer Daten, um deren Eigenschaften zu verstehen. Die Datenstruktur in der kognitiven Azure-Suche entspricht einem invertierten Index. Größe und Komplexität eines invertierten Index werden vom Inhalt bestimmt. Denken Sie daran, dass hoch redundanter Inhalt eher zu einem kleineren Index tendiert als sehr unregelmäßiger Inhalt. Daher werden die Speicheranforderungen für den Index durch die Inhaltseigenschaften und nicht durch die Größe des Datasets bestimmt.

Stellen Sie nach einer ersten Schätzung der Indexgröße [einen abrechenbaren Dienst](search-create-service-portal.md) in einem der folgenden Tarife bereit, die in diesem Artikel beschrieben werden: „Basic“, „Standard“ oder „Speicheroptimiert“. Lockern Sie künstliche Einschränkungen im Zusammenhang mit der Dimensionierung von Daten, und [erstellen Sie den Index neu](search-howto-reindex.md), um alle Daten einzuschließen, die durchsuchbar sein sollen.

[Ordnen Sie Partitionen und Replikate zu](search-capacity-planning.md), wie es erforderlich ist, um die gewünschte Leistung und Skalierung zu erzielen.

Wenn Sie mit der Leistung und Kapazität zufrieden sind, dann sind Sie fertig. Erstellen Sie andernfalls erneut einen Suchdienst in einem anderen Tarif, der Ihren Anforderungen eher entspricht.

> [!NOTE]
> Wenn Sie Fragen haben, posten Sie in [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search), oder wenden Sie sich an den [Azure-Support](https://azure.microsoft.com/support/options/).