---
title: Neuerungen in der kognitiven Azure-Suche
description: Ankündigungen neuer und erweiterter Features, einschließlich der Umbenennung des Diensts von Azure Search in kognitive Azure-Suche.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 02/09/2021
ms.custom: references_regions
ms.openlocfilehash: 13cb22c178be29af71b57d0f50fdbd0e95718069
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100104602"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Neuerungen in der kognitiven Azure-Suche

Informieren Sie sich über die Neuerungen im Dienst. Legen Sie ein Lesezeichen für diese Seite an, um über den Dienst auf dem Laufenden zu bleiben. Sehen Sie sich die [Liste der Previewfunktionen](search-api-preview.md) an, um die Funktionen in der öffentlichen Vorschauphase anzuzeigen.

## <a name="february-2021"></a>Februar 2021

|Funktion&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  BESCHREIBUNG | Verfügbarkeit  |
|------------------------------|---------------|---------------|
| [Zurücksetzen von Dokumenten (Vorschau)](search-howto-run-reset-indexers.md) |  Erneute Verarbeitung von einzeln ausgewählten Suchdokumenten in Indexerworkloads. | [Search-REST-API 2020-06-30-Preview](/rest/api/searchservice/index-preview) |
| [Verfügbarkeitszonen](search-performance-optimization.md#availability-zones)| Für Suchdienste mit zwei oder mehr Replikaten in bestimmten Regionen (siehe [diesen Artikel](search-performance-optimization.md#availability-zones)) wird Resilienz erzielt, indem Replikate an zwei oder mehr getrennten physischen Standorten vorgehalten werden.  | Die Verfügbarkeit richtet sich nach der Region und dem Datum der Erstellung des Suchdiensts. Ausführliche Informationen finden Sie im Dokument zur Leistungsoptimierung. |

## <a name="january-2021"></a>Januar 2021

|Funktion&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  BESCHREIBUNG | Verfügbarkeit  |
|------------------------------|-------------|---------------|
| [Solution Accelerator für Azure Cognitive Search und QnA Maker](https://github.com/Azure-Samples/search-qna-maker-accelerator) | Pullen von Fragen und Antworten aus dem Dokument sowie zum Vorschlagen der Antworten mit der höchsten Relevanz. Eine Livedemo-App finden Sie unter [https://aka.ms/qnaWithAzureSearchDemo](https://aka.ms/qnaWithAzureSearchDemo).  | Open-Source-Projekt (keine SLA) |

## <a name="2020-archive"></a>2020: Archiv

| Month (Monat) | Funktion | BESCHREIBUNG |
|-------|---------|-------------|
| November | [Verschlüsselung mit kundenseitig verwalteten Schlüsseln (erweitert)](search-security-manage-encryption-keys.md) | Erweitert die kundenseitig verwaltete Verschlüsselung für alle Ressourcen, die von einem Suchdienst erstellt und verwaltet werden. Allgemein verfügbar.|
| September | [Visual Studio Code-Erweiterung für Azure Cognitive Search](search-get-started-vs-code.md) | Hinzufügung von Arbeitsbereich, Navigationselementen, IntelliSense und Vorlagen für die Erstellung von Indizes, Indexern, Datenquellen und Skillsets. | Public Preview |
| September | [Verwaltete Dienstidentität (Indexer)](search-howto-managed-identities-data-sources.md) | Allgemein verfügbar.  |
| September | [Ausgehende Anforderungen über eine Private Link-Instanz](search-indexer-howto-access-private.md) | Allgemein verfügbar.  |
| September | [Verwaltungs-REST-API (2020-08-01)](/rest/api/searchmanagement/management-api-versions) | Allgemein verfügbar. |
| September | [Verwaltungs-REST-API (2020-08-01-Preview)](/rest/api/searchmanagement/management-api-versions) | Fügt eine gemeinsam genutzte Private Link-Ressource für Azure Functions und Azure SQL für MySQL-Datenbanken hinzu. |
| September | [Management .NET SDK 4.0](/dotnet/api/overview/azure/search/management) |  Azure SDK-Update für das Management SDK mit der REST-API-Zielversion 2020-08-01. Allgemein verfügbar.|
| August | [Doppelte Verschlüsselung](search-security-overview.md#encryption) | Allgemein verfügbar für alle Suchdienste, die nach dem 1. August 2020 in den folgenden Regionen erstellt wurden: USA, Westen 2; USA, Osten; USA, Süden-Mitte; US Gov Virginia; US Gov Arizona. |
| Juli | [Azure.Search.Documents-Clientbibliothek](/dotnet/api/overview/azure/search.documents-readme) | Azure SDK für .NET, allgemein verfügbar. |
| Juli | [azure.search.documents-Clientbibliothek](/python/api/overview/azure/search-documents-readme)  | Azure SDK für Python, allgemein verfügbar. |
| Juli | [@azure/search-documents-Clientbibliothek](/javascript/api/overview/azure/search-documents-readme)  | Azure SDK für JavaScript, allgemein verfügbar. |
| June | [Wissensspeicher](knowledge-store-concept-intro.md) | Allgemein verfügbar. |
| June | [REST-API 2020-06-30 suchen](/rest/api/searchservice/) | Allgemein verfügbar. |
| June | [Search-REST-API 2020-06-30-Preview](/rest/api/searchservice/) | Hinzufügung von „Skillset zurücksetzen“ für die selektive erneute Verarbeitung von Skills und inkrementelle Anreicherung. |
| June | [Okapi BM25-Relevanzalgorithmus](index-ranking-similarity.md) | Allgemein verfügbar. |
| June |  **executionEnvironment** (für Suchdienste mit Azure Private Link) | Allgemein verfügbar. |
| June | [AML-Skill (Vorschau)](cognitive-search-aml-skill.md) | Kognitiver Skill, um die KI-Anreicherung mit einem benutzerdefinierten AML-Modell (Azure Machine Learning) zu erweitern. |
| May | [Debugsitzungen (Vorschau)](cognitive-search-debug-session.md) | Skillset-Debugger im Portal.  |
| May | [IP-Regeln für eingehende Firewallunterstützung](service-configure-firewall.md) | Allgemein verfügbar.  |
| May | [Azure Private Link für einen privaten Suchendpunkt](service-create-private-endpoint.md) | Allgemein verfügbar.  |
| May | [Verwaltete Dienstidentität (Indexer) (Vorschau)](search-howto-managed-identities-data-sources.md) | Verbinden von Azure-Datenquellen mit einer verwalteten Identität.  |
| May | [sessionId-Abfrageparameter](index-similarity-and-scoring.md), [scoringStatistics=global parameter](index-similarity-and-scoring.md#scoring-statistics)  | Globale Suchstatistik, nützlich für [Machine Learning-Modelle (LearnToRank) für Suchrelevanz](https://github.com/Azure-Samples/search-ranking-tutorial).  |
| May | [Erweiterung der featuresMode-Relevanzbewertung (Vorschau)](index-similarity-and-scoring.md#featuresMode-param)  |   |
|März  | [Natives vorläufiges Löschen von Blobs (Vorschau)](search-howto-index-changed-deleted-blobs.md) | Löschen von Suchdokumenten, wenn das Quellblob im Blobspeicher vorläufig gelöscht wird. |
|März  | [Verwaltungs-REST-API (2020-03-13)](/rest/api/searchmanagement/management-api-versions) | Allgemein verfügbar. |
|Februar | [Qualifikation „PII-Erkennung“ (Vorschau)](cognitive-search-skill-pii-detection.md)  | Kognitiver Skill, mit dem persönliche Informationen extrahiert und maskiert werden. |
|Februar | [Qualifikation “Benutzerdefinierte Entitätssuche“ (Vorschau)](cognitive-search-skill-custom-entity-lookup.md) | Kognitiver Skill zum Finden von Wörtern und Ausdrücken in einer Liste und zum Bezeichnen aller Dokumente mit übereinstimmenden Entitäten.  |
|January | [Verschlüsselung mit kundenseitig verwalteten Schlüsseln](search-security-manage-encryption-keys.md) | Allgemein verfügbar  |
|January | [IP-Regeln für eingehende Firewallunterstützung(Vorschau)](service-configure-firewall.md) | Neue **IpRule**- und **NetworkRuleSet**-Eigenschaften in [CreateOrUpdate-API](/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service).  |
|January | [Erstellen eines privaten Endpunkts (Vorschau)](service-create-private-endpoint.md) | Einrichten einer Private Link-Instanz für sichere Verbindungen mit Ihrem Suchdienst. Im Rahmen der Lösung verfügt diese Previewfunktion über eine Abhängigkeit von [Azure Private Link](../private-link/private-link-overview.md) und [Azure Virtual Network](../virtual-network/virtual-networks-overview.md). |

## <a name="2019-archive"></a>2019: Archiv

| Month (Monat) | Funktion | BESCHREIBUNG |
|-------|---------|-------------|
|Dezember | [Erstellen einer Demo-App (Vorschau)](search-create-app-portal.md) | Ein Assistent, mit dem eine herunterladbare HTML-Datei mit (schreibgeschütztem) Abfragezugriff auf einen Index generiert wird. Dies ist als Überprüfungs- und Testtool konzipiert und soll nicht als Abkürzung zur Erstellung einer vollständigen Client-App dienen.|
|November | [Inkrementelle Anreicherung (Vorschau)](cognitive-search-incremental-indexing-conceptual.md) | Zwischenspeichern der Verarbeitung von Skillsets zur zukünftigen Verwendung.  |
|November | [Kognitiver Skill „Dokumentextrahierung“ (Vorschau)](cognitive-search-skill-document-extraction.md) | Kognitiver Skill zum Extrahieren des Inhalts einer Datei aus einem Skillset.|
|November | [Skill „Textübersetzungs-API“](cognitive-search-skill-text-translation.md) | Kognitiver Skill für die Nutzung während der Indizierung, mit dem Text evaluiert und übersetzt wird. Allgemein verfügbar.|
|November | [Power BI-Vorlagen](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) | Vorlage zum Visualisieren von Inhalt im Wissensspeicher |
|November | [Azure Data Lake Storage Gen2 (Vorschauversion)](search-howto-index-azure-data-lake-storage.md), [Cosmos DB-Gremlin-API (Vorschauversion)](search-howto-index-cosmosdb.md) und [Cosmos DB-Cassandra-API (Vorschauversion)](search-howto-index-cosmosdb.md) | Neue Indexerdatenquellen, die sich in der öffentlichen Vorschauphase befinden. |
|Juli | [Unterstützung für die Azure Government-Cloud](../azure-government/compare-azure-government-global-azure.md#azure-cognitive-search) | Allgemein verfügbar.|

<a name="new-service-name"></a>

## <a name="new-service-name"></a>Neuer Dienstname

Azure Search wurde im Oktober 2019 in **Azure Cognitive Search** umbenannt, um die erweiterte (optionale) Verwendung von kognitiven Skills und KI-Verarbeitung bei wichtigen Vorgängen widerzuspiegeln. API-Versionen, NuGet-Pakete, Namespaces und Endpunkte bleiben unverändert. Neue und bereits vorhandene Suchlösungen sind von der Änderung des Dienstnamens nicht betroffen.

## <a name="service-updates"></a>Dienstupdates

[Ankündigungen von Dienstupdates](https://azure.microsoft.com/updates/?product=search&status=all) für die kognitive Azure-Suche finden Sie auf der Azure-Website.