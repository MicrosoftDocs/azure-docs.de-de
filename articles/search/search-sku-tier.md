---
title: Auswählen eines Tarifs
titleSuffix: Azure Cognitive Search
description: 'Hier erfahren Sie etwas über die Tarife (oder SKUs) für Azure Cognitive Search. Ein Suchdienst kann auf diesen Ebenen bereitgestellt werden: „Free“, „Basic“ und „Standard“. „Standard“ steht in verschiedenen Ressourcenkonfigurationen und Kapazitäten zur Verfügung.'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2021
ms.custom: contperf-fy21q2
ms.openlocfilehash: df218c2a4c066343ef571c6f24554ecaa806b639
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99987881"
---
# <a name="choose-a-pricing-tier-for-azure-cognitive-search"></a>Auswählen eines Tarifs für die kognitive Azure-Suche

Beim [Erstellen eines Suchdiensts](search-create-service-portal.md) müssen Sie auch einen Tarif (oder eine SKU) auswählen, der für die Lebensdauer des Diensts festgelegt wird. Die Preise (bzw. die geschätzten monatlichen Kosten für die Ausführung des Diensts) werden im Portal auf der Seite **Tarif auswählen** angezeigt, wenn Sie den Dienst erstellen. Falls Sie die Bereitstellung stattdessen mit PowerShell oder der Azure CLI durchführen, wird der Tarif mit dem Parameter **`-Sku`** angegeben. In diesem Fall sollten Sie sich unter [Azure Cognitive Search – Preise](https://azure.microsoft.com/pricing/details/search/) über die geschätzten Kosten informieren.

Der ausgewählte Tarif bestimmt:

+ Maximale Anzahl von Indizes und anderen Objekten, die im Dienst zulässig sind
+ Größe und Geschwindigkeit von Partitionen (physischer Speicher)
+ Die abrechenbare Rate als eine festgelegte monatliche Gebühr, aber auch als inkrementelle Kosten, wenn Sie Kapazität hinzufügen

In einigen Fällen bestimmt der von Ihnen gewählte Tarif die Verfügbarkeit von [Premium-Features](#premium-features).

> [!NOTE]
> Suchen Sie nach Informationen zu „Azure-SKUs“? Beginnen Sie mit [Azure-Preise](https://azure.microsoft.com/pricing/), und scrollen Sie dann nach unten, um die Links zu den Preisseiten für die einzelnen Dienste anzuzeigen.

## <a name="tier-descriptions"></a>Tarifbeschreibungen

Folgende Tarife sind verfügbar: **Free**, **Basic**, **Standard** und **Speicheroptimiert**. „Standard“ und „Speicheroptimiert“ werden mit verschiedenen Konfigurationen und Kapazitäten bereitgestellt. Im folgenden Screenshot aus dem Azure-Portal sind die verfügbaren Tarife ohne Preise aufgeführt (die Preise finden Sie im Portal und auf der [Seite mit der Preisübersicht](https://azure.microsoft.com/pricing/details/search/)). 

:::image type="content" source="media/search-sku-tier/tiers.png" alt-text="Diagramm des Tarifs" border="true":::

Mit **Free** wird ein eingeschränkter Suchdienst für kleinere Projekte wie das Ausführen von Tutorials und Codebeispielen erstellt. Intern werden Systemressourcen von mehreren Abonnenten gemeinsam genutzt. Es ist nicht möglich, einen kostenlosen Dienst zu skalieren oder signifikante Workloads auszuführen.

**Basic** und **Standard** sind die am häufigsten verwendeten abzurechnenden Tarife, wobei **Standard** die Standardeinstellung ist, weil sie mehr Flexibilität bei der Skalierung für Workloads bietet. Wenn Sie dedizierte Ressourcen nutzen, können Sie größere Projekte bereitstellen, die Leistung optimieren und die Kapazität steigern.

Einige Tarife sind für bestimmte Verwendungszwecke konzipiert. **Standard 3 High Density (S3 HD)** ist beispielsweise ein *Hostingmodus* für S3. Die zugrunde liegende Hardware ist für eine große Anzahl kleinerer Indizes optimiert und für Szenarien mit Mehrinstanzenfähigkeit konzipiert. Die Gebühr pro Einheit unterscheidet sich zwischen „S3 HD“ und „S3“ nicht. Die Hardware ist bei „S3 HD“ jedoch für schnelle Dateilesevorgänge bei einer großen Anzahl kleinerer Indizes optimiert.

**Datenspeicheroptimierte** Tarife bieten eine höhere Speicherkapazität zu einem niedrigeren Preis pro TB als die Standard-Tarife. Der größte Nachteil ist die höhere Wartezeit. Diese müssen Sie bei Ihren spezifischen Anwendungsanforderungen berücksichtigen. Weitere Informationen zu den Leistungsaspekten dieses Tarifs finden Sie unter [Überlegungen zur Leistung und Optimierung von Azure Search](search-performance-optimization.md).

Weitere Informationen zu den verschiedenen Tarifen finden Sie auf der [Seite mit der Preisübersicht](https://azure.microsoft.com/pricing/details/search/), im Artikel [Grenzwerte für den Dienst für die kognitive Azure-Suche](search-limits-quotas-capacity.md) und auf der Portalseite beim Bereitstellen eines Diensts.

<a name="premium-features"></a>

## <a name="feature-availability-by-tier"></a>Funktionsverfügbarkeit pro Tarif

Die meisten Funktionen sind in allen Tarifen (einschließlich des Free-Tarifs) verfügbar. In einigen Fällen wirkt sich der von Ihnen ausgewählte Tarif auf die Fähigkeit aus, eine Funktion zu implementieren. In der folgenden Tabelle werden die Funktionseinschränkungen im Zusammenhang mit der Dienstebene (dem Tarif) beschrieben.

| Funktion | Einschränkungen |
|---------|-------------|
| [Indexer](search-indexer-overview.md) | Indexer sind auf S3 HD nicht verfügbar.  |
| [KI-Anreicherung](search-security-manage-encryption-keys.md) | Wird im Free-Tarif ausgeführt, aber nicht empfohlen. |
| [Verwaltete oder vertrauenswürdige Identitäten für den ausgehenden Zugriff (Indexer)](search-howto-managed-identities-data-sources.md) | Im Free-Tarif nicht verfügbar.|
| [Von Kunden verwaltete Verschlüsselungsschlüssel](search-security-manage-encryption-keys.md) | Im Free-Tarif nicht verfügbar. |
| [IP-Firewallzugriff](service-configure-firewall.md) | Im Free-Tarif nicht verfügbar. |
| [Privater Endpunkt (Integration mit Azure Private Link)](service-create-private-endpoint.md) | Für eingehende Verbindungen bei einem Suchdienst, nicht im Free-Tarif verfügbar. Für ausgehende Verbindungen von Indexern zu anderen Azure-Ressourcen, nicht verfügbar im Tarif „Free“ oder „S3 HD“. Für Indexer, die Skillsets verwenden, nicht verfügbar im Tarif „Free“, „Basic“, „S1“ oder „S3 HD“| 
| [Verfügbarkeitszonen](search-performance-optimization.md) | In den Tarifen „Free“ und „Basic“ nicht verfügbar. |

Ressourcenintensive Funktionen funktionieren möglicherweise nicht gut, wenn Sie ihnen nicht genügend Kapazität zuweisen. Beispielsweise umfasst die [KI-Anreicherung](cognitive-search-concept-intro.md) Skills mit langer Laufzeit, die bei einem kostenlosen Dienst zu einem Timeout führen, sofern es sich nicht um ein kleines Dataset handelt.

## <a name="upper-limits"></a>Obergrenzen

Tarife bestimmen den maximalen Speicherplatz des Diensts selbst sowie die maximale Anzahl von Indizes, Indexern, Datenquellen, Skillsets und Synonymzuordnungen, die Sie erstellen können. Eine vollständige Unterbrechung aller Grenzwerte finden Sie unter [Diensteinschränkungen in Azure Cognitive Search](search-limits-quotas-capacity.md). 

## <a name="partition-size-and-speed"></a>Partitionsgröße und Geschwindigkeit

Der Tarifpreis enthält Details zum partitionsweisen Speicher, der von 2 GB für „Basic“ bis zu 2 TB für datenspeicheroptimierte Tarife (L2) reicht. Andere Hardwaremerkmale, wie z. B. die Geschwindigkeit von Vorgängen, die Latenz und die Übertragungsraten, werden nicht veröffentlicht, aber die für bestimmte Lösungsarchitekturen vorgesehenen Tarife basieren auf Hardware mit den Features zur Unterstützung dieser Szenarien. Weitere Informationen zu Partitionen finden Sie unter [Schätzen und Verwalten der Kapazität](search-capacity-planning.md) und [Skalieren zur Verbesserung der Leistung](search-performance-optimization.md).

## <a name="billing-rates"></a>Abrechnungsraten

Bei Tarifen gibt es unterschiedliche Abrechnungsraten, mit höheren Raten für Tarife, die auf teurerer Hardware ausgeführt werden oder teurere Features bereitstellen. Die Abrechnungsrate pro Ebene finden Sie auf den [Seiten zu Azure-Preisen](https://azure.microsoft.com/pricing/details/search/) für Azure Cognitive Search.

Nachdem Sie einen Dienst erstellt haben, wird die Abrechnungsrate sowohl zu *Fixkosten* für die Ausführung des Diensts rund um die Uhr als auch zu *inkrementellen Kosten*, wenn Sie mehr Kapazität hinzufügen möchten.

Suchdiensten werden Computerressourcen in Form von *Partitionen* (für Speicher) und *Replikaten* (Instanzen der Abfrage-Engine) zugeordnet. Anfänglich wird ein Dienst mit jeweils einer der beiden Ressourcen erstellt, und die Abrechnungsrate ist in beiden Ressourcen enthalten. Wenn Sie jedoch die Kapazität skalieren, werden die Kosten in Inkrementen der abrechenbaren Rate erhöht oder gesenkt.

Dies wird im folgenden Beispiel veranschaulicht. Nehmen Sie eine hypothetische Abrechnungsrate von 100 € pro Monat an. Wenn Sie für den Suchdienst seine Anfangskapazität von einer Partition und einem Replikat beibehalten, ist 100 € der Betrag, den Sie am Monatsende erwartungsgemäß bezahlen werden. Wenn Sie aber zwei weitere Replikate hinzufügen, um Hochverfügbarkeit zu erreichen, erhöht sich die monatliche Rechnung auf 300 € (100 € für das erste Paar aus Replikat und Partition, gefolgt von 200 € für die zwei Replikate).

Dieses Preismodell basiert auf dem Konzept einer Anwendung der Abrechnungsrate auf die Anzahl der *Sucheinheiten* (Search Units, SUs), die von einem Suchdienst verwendet werden. Alle Dienste werden zunächst in einer einzigen SU bereitgestellt. Sie können aber die Anzahl der SUs erhöhen, indem Sie entweder Partitionen oder Replikate zur Verarbeitung größerer Workloads hinzufügen. Weitere Informationen finden Sie unter [Schätzen der Kosten für einen Suchdienst](search-sku-manage-costs.md).

## <a name="next-steps"></a>Nächste Schritte

Die beste Möglichkeit zur Auswahl eines Tarifs ist, mit dem kostengünstigsten Tarif zu beginnen und sich dann aufgrund von Erfahrungen und anhand von Tests zu entscheiden, ob Sie den Dienst beibehalten oder einen neuen Dienst zu einem höheren Tarif erstellen möchten. Für die nächsten Schritte empfehlen wir, dass Sie einen Suchdienst auf einer Ebene erstellen, die die von Ihnen vorgeschlagenen Testebene aufnehmen kann. Lesen Sie dann den folgenden Leitfaden mit Empfehlungen zum Schätzen von Kosten und Kapazität.

+ [Erstellen eines Suchdiensts](search-create-service-portal.md)
+ [Schätzen der Kosten](search-sku-manage-costs.md)
+ [Schätzen der Kapazität](search-sku-manage-costs.md)