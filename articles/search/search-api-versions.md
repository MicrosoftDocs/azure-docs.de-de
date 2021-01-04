---
title: API-Versionen
titleSuffix: Azure Cognitive Search
description: Versionsrichtlinie für REST-APIs für die kognitive Azure-Suche und die Clientbibliothek im .NET SDK.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/05/2020
ms.openlocfilehash: feca2e83646264b28e42125b3fdebf41266c06bd
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422721"
---
# <a name="api-versions-in-azure-cognitive-search"></a>API-Versionen in der kognitiven Azure-Suche

Für die kognitive Azure-Suche sind regelmäßig Featureupdates verfügbar. Manchmal, aber nicht immer, erfordern diese Updates eine neue Version der API, um die Abwärtskompatibilität zu gewährleisten. Die Veröffentlichung einer neuen Version ermöglicht Ihnen, zu steuern, wann und wie Sie Suchdienstupdates in Ihren Code integrieren.

In der Regel versucht das Team für die kognitive Azure-Suche, neue Versionen nur im Bedarfsfall zu veröffentlichen, da es einen gewissen Aufwand erfordern kann, wenn Sie Ihren Code aktualisieren, um eine neue API-Version zu verwenden. Eine neue Version ist nur dann erforderlich, wenn sich ein Aspekt der API in einer Weise geändert hat, die die Abwärtskompatibilität beeinträchtigt. Ursache für solche Änderungen können Fehlerbehebungen bei vorhandenen Funktionen sein oder neue Funktionen, die vorhandene API-Oberflächenbereiche ändern.

Die gleiche Regel gilt bei SDK-Updates. Das SDK für die kognitive Azure-Suche folgt den Regeln der [semantischen Versionierung](https://semver.org/), d.h., dass seine Version aus drei Teilen besteht: Haupt-, Neben- und Buildnummer (z.B. 1.1.0). Eine neue Hauptversion des SDK wird nur dann veröffentlicht, wenn Änderungen die Abwärtskompatibilität beeinträchtigen. Bei Funktionsupdates ohne Beeinträchtigung der Abwärtskompatibilität wird die Nebenversion, bei Fehlerbehebungen nur die Buildversion heraufgesetzt.

> [!Important]
> Die Azure-SDKs für .NET, Java, Python und JavaScript führen neue Clientbibliotheken für Azure Cognitive Search ein. Zurzeit unterstützt keine der Azure-SDK-Bibliotheken die neuesten REST-APIs für die Suche (2020-06-30) oder die Verwaltung (2020-03-13) vollständig, dies ändert sich jedoch im Laufe der Zeit. Sie können diese Seite oder die [Neuerungen](whats-new.md) in regelmäßigen Abständen überprüfen, um Ankündigungen zu funktionalen Erweiterungen zu erhalten.

<a name="unsupported-versions"></a>

## <a name="unsupported-versions"></a>Nicht unterstützte Versionen

Führen Sie bis zum 15. Oktober 2020 ein Upgrade der vorhandenen Suchlösungen auf die aktuelle Version der REST-API aus. Zu diesem Zeitpunkt werden die folgenden Versionen der Azure Cognitive Search-REST-API und deren Support eingestellt:

+ **2015-02-28**
+ **2015-02-28-Preview**
+ **2014-07-31-Preview**
+ **2014-10-20-Preview**

Darüber hinaus werden Versionen des Azure Cognitive Search .NET SDK vor [**3.0.0-rc**](https://www.nuget.org/packages/Microsoft.Azure.Search/3.0.0-rc), ebenfalls eingestellt, da sie auf eine dieser REST-API-Versionen ausgerichtet sind. 

Nach diesem Datum können Anwendungen, die eine der veralteten REST-API- oder SDK-Versionen verwenden, nicht mehr verwendet werden und müssen aktualisiert werden. Wie bei jeder Änderung dieser Art kündigen wir diese 12 Monate im Voraus an, sodass Sie ausreichend Zeit haben, sich darauf einzustellen.

Für die Weiterverwendung von Azure Cognitive Search muss vorhandener Code, der auf die [REST-API](search-api-migration.md) ausgerichtet ist, bis zum 15. Oktober 2020 zur [REST-API-Version 2020-06-30](/rest/api/searchservice/) oder zu einem neueren SDK migriert werden.  Wenn Sie Fragen zur Aktualisierung auf die neueste Version haben, senden Sie bitte bis zum 15. Mai 2020 eine E-Mail an azuresearch_contact@microsoft.com, um sicherzustellen, dass genügend Zeit für die Aktualisierung Ihres Codes bleibt.

## <a name="rest-apis"></a>REST-APIs

Eine Instanz des Azure Cognitive Search-Diensts unterstützt mehrere REST-API-Versionen, einschließlich der neuesten. Sie können auch ältere Versionen weiterhin verwenden, aber es wird empfohlen, den Code zur neuesten Version zu [migrieren](search-api-migration.md). Wenn Sie die REST-API verwenden, müssen Sie die API-Version bei jeder Anforderung über den api-version-Parameter angeben. Wenn Sie das .NET SDK verwenden, legt die Version des verwendeten SDK die Version der REST-API fest. Wenn Sie ein älteres SDK verwenden, können Sie diesen Code weiterhin ohne Änderungen ausführen, selbst wenn der Dienst aktualisiert wird, um eine neuere Version der API zu unterstützen.

Die folgende Tabelle zeigt die Versionsgeschichte der aktuellen und zuvor veröffentlichter Versionen der REST-API für den Suchdienst. Die Dokumentation wird nur für die aktuellste stabile Version und die Vorschauversionen veröffentlicht.

### <a name="search-service-apis"></a>Suchdienst-APIs

Erstellen und verwalten Sie Inhalte in einem Suchdienst.

| Version&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   | Status | Beschreibung |
|-------------------------|--------|------------------------------|
| [Search 2020-06-30](/rest/api/searchservice/index)| Stable | Das neueste stabile Release der Such-REST-APIs mit Verbesserungen bei der Relevanzbewertung und der allgemeinen Verfügbarkeit des Wissensspeichers.|
| [Search 2020-06-30-Preview](/rest/api/searchservice/index-preview)| Vorschau | Mit der stabilen Version verknüpfte Vorschauversion. Umfasst mehrere [Previewfunktionen](search-api-preview.md). |
| Search 2019-05-06 | Stable  | Fügt [komplexe Typen](search-howto-complex-data-types.md) hinzu. |
| Search 2019-05-06-Preview | Vorschau | Mit der stabilen Version verknüpfte Vorschauversion. |
| Search 2017-11-11 | Stable | Fügt Skillsets und [KI-Anreicherung](cognitive-search-concept-intro.md) hinzu. |
| Search 2017-11-11-Preview | Vorschau | Mit der stabilen Version verknüpfte Vorschauversion. |
| Search 2016-09-01 |Stable | Fügt [Indexer](search-indexer-overview.md) hinzu. |
| Search 2016-09-01-Preview | Vorschau | Mit der stabilen Version verknüpfte Vorschauversion.|
| Search 2015-02-28 | Nicht mehr unterstützt ab dem 10. 10. 2020   | Erstes allgemein verfügbares Release.  |
| Search 2015-02-28-Preview | Nicht mehr unterstützt ab dem 10. 10. 2020  | Mit der stabilen Version verknüpfte Vorschauversion. |
| Search 2014-10-20-Preview | Nicht mehr unterstützt ab dem 10. 10. 2020 | Zweite öffentliche Vorschauversion. |
| Search 2014-07-31-Preview | Nicht mehr unterstützt ab dem 10. 10. 2020  | Erste öffentliche Vorschauversion. |

### <a name="management-rest-apis"></a>REST-APIs für die Verwaltung

Erstellen und verwalten Sie einen Suchdienst, und verwalten Sie API-Schlüssel.

| Version&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   | Status | Beschreibung |
|-------------------------|--------|------------------------------|
| [Management 2020-08-01](/rest/api/searchmanagement/) | Stable | Neuestes stabiles Release der Verwaltungs-REST-APIs. Es fügt allgemein verfügbare Unterstützung für freigegebene Private Link-Ressourcen für alle Ressourcen hinzu, auf die extern zugegriffen wird, mit Ausnahme der in der Vorschauversion genannten Ressourcen. |
| [Management 2020-08-01-Preview](/rest/api/searchmanagement/index-preview) | Vorschau  | Derzeit als Vorschauversion: fügt Unterstützung für freigegebene Private Link-Ressourcen für Azure Functions und Azure Database for MySQL hinzu. |
| Management 2020-03-13  | Stable | Fügt einen [privaten Endpunkt](service-create-private-endpoint.md) über Private Link sowie [Netzwerk-IP-Regeln](service-configure-firewall.md) für neue Dienste hinzu. Weitere Informationen finden Sie in der [Swagger-Spezifikation](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/resource-manager/Microsoft.Search/stable/2020-08-01). |
| Management 2019-10-01-Preview | Vorschau  | In diese Liste wurden keine Previewfunktionen aufgenommen. Diese Vorschauversion ist funktionell äquivalent zu 2020-03-13. Weitere Informationen finden Sie in der [Swagger-Spezifikation](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/resource-manager/Microsoft.Search/preview/2019-10-01-preview). |
| Management 2015-08-19  | Stable | Die erste allgemein verfügbare Version der Management-REST-APIs. Bietet Dienstbereitstellung, Hochskalierung und API-Schlüsselverwaltung. Weitere Informationen finden Sie in der [Swagger-Spezifikation](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/resource-manager/Microsoft.Search/stable/2015-08-19). |
| Management 2015-08-19-Preview  | Vorschau | Die erste Vorschauversion der Management-REST-APIs. Weitere Informationen finden Sie in der [Swagger-Spezifikation](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/search/resource-manager/Microsoft.Search/stable/2015-08-19). |

## <a name="azure-sdk-for-net"></a>Azure SDK für .NET

In der folgenden Tabelle finden Sie Links zu neueren SDK-Versionen. 

| SDK-Version | Status | BESCHREIBUNG |
|-------------|--------|------------------------------|
| [Azure.Search.Documents 11](/dotnet/api/overview/azure/search.documents-readme) | Stable | Neue Clientbibliothek aus dem Azure .NET SDK, veröffentlicht im Juli 2020. Ist für die REST-API für die Suche (Version 2020-06-30) vorgesehen, unterstützt jedoch noch keine Geofilter. |
| [Microsoft.Azure.Search 10](https://www.nuget.org/packages/Microsoft.Azure.Search/) | Stable | Veröffentlichung: Mai 2019 Gilt für die Such-REST-API, api-version=2019-05-06.|
| [Microsoft.Azure.Management.Search 4.0.0](/dotnet/api/overview/azure/search/management) | Stable | Gilt für die Verwaltungs-REST-API-Version 2020-08-01.  |
| Microsoft.Azure.Management.Search 3.0.0 | Stable | Gilt für die Management REST-API, api-version=2015-08-19.  |

## <a name="azure-sdk-for-java"></a>Azure SDK für Java

| SDK-Version | Status | BESCHREIBUNG  |
|-------------|--------|------------------------------|
| [Java azure-search-documents 11](https://newreleases.io/project/github/Azure/azure-sdk-for-java/release/azure-search-documents_11.1.0) | Stable | Neue Clientbibliothek aus dem Azure .NET SDK, veröffentlicht im Juli 2020. Gilt für die Such-REST-API, api-version=2019-05-06. |
| [Java Management Client 1.35.0](/java/api/overview/azure/search/management) | Stable | Gilt für die Management REST-API, api-version=2015-08-19. |

## <a name="azure-sdk-for-javascript"></a>Azure SDK für JavaScript

| SDK-Version | Status | BESCHREIBUNG  |
|-------------|--------|------------------------------|
| [JavaScript azure-search 11.0](https://azure.github.io/azure-sdk-for-node/azure-search/latest/) | Stable | Neue Clientbibliothek aus dem Azure .NET SDK, veröffentlicht im Juli 2020. Gilt für die Such-REST-API, api-version=2016-09-01. |
| [JavaScript azure-arm-search](https://azure.github.io/azure-sdk-for-node/azure-arm-search/latest/) | Stable | Gilt für die Management REST-API, api-version=2015-08-19. |

## <a name="azure-sdk-for-python"></a>Azure SDK für Python

| SDK-Version | Status | BESCHREIBUNG  |
|-------------|--------|------------------------------|
| [Python azure-search-documents 11.0](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-search-documents/11.0.0/index.html) | Stable | Neue Clientbibliothek aus dem Azure .NET SDK, veröffentlicht im Juli 2020. Gilt für die Such-REST-API, api-version=2019-05-06. |
| [Python azure-mgmt-search 1.0](/python/api/overview/azure/search) | Stable | Gilt für die Management REST-API, api-version=2015-08-19. |