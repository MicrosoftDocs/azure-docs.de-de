---
title: Verwalten von Media Services-Entitäten mit REST | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Media Services-Entitäten mit der REST-API verwalten.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 95262a32-0f2a-4286-b9e2-1a1ca6399b5b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: ffbf30f2bfdf0a175513a8d2b9182b35c39f6aae
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60761708"
---
# <a name="managing-media-services-entities-with-rest"></a>Verwalten von Media Services-Entitäten mit REST  

> [!div class="op_single_selector"]
> * [REST](media-services-rest-manage-entities.md)
> * [.NET](media-services-dotnet-manage-entities.md)
> 
> 

Microsoft Azure Media Services ist ein REST-basierter Dienst auf Grundlage von OData v3. Sie können Entitäten genauso wie bei anderen OData-Diensten hinzufügen, abfragen, aktualisieren und löschen. Auf Ausnahmen wird ggf. hingewiesen. Weitere Informationen zu OData finden Sie in der [Dokumentation zu Open Data Protocol](https://www.odata.org/documentation/).

Dieses Thema veranschaulicht das Verwalten von Azure Media Services-Entitäten mit REST.

>[!NOTE]
> Ab dem 1. April 2017 werden alle Auftragsdatensätze in Ihrem Konto, die älter als 90 Tage sind, sowie alle zugehörigen Aufgabendatensätze automatisch gelöscht, selbst wenn die Gesamtanzahl von Datensätzen unterhalb des maximalen Kontingents liegt. Beispielsweise wird jeder Auftragsdatensatz in Ihrem Konto, der vor dem 31. Dezember 2016 erstellt wurde, am 1. April 2017 automatisch gelöscht. Wenn Sie die Auftrags-/Aufgabeninformationen archivieren müssen, können Sie dazu den in diesem Thema beschriebenen Code verwenden.

## <a name="considerations"></a>Überlegungen  

Wenn Sie in Media Services auf Entitäten zugreifen, müssen Sie bestimmte Headerfelder und Werte in Ihren HTTP-Anforderungen festlegen. Weitere Informationen finden Sie unter [Installation für die Entwicklung mit der Media Services-REST-API](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Verbinden mit Mediendiensten

Informationen zum Herstellen einer Verbindung mit der AMS-API finden Sie unter [Zugreifen auf die Azure Media Services-API per Azure AD-Authentifizierung](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="adding-entities"></a>Hinzufügen von Entitäten
Jede Entität in Media Services wird über eine POST-HTTP-Anforderung einer Entitätenmenge hinzugefügt, z. B. Medienobjekten.

Im folgenden Beispiel wird veranschaulicht, wie eine AccessPolicy erstellt wird:

    POST https://media.windows.net/API/AccessPolicies HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net
    Content-Length: 74
    Expect: 100-continue

    {"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}

## <a name="querying-entities"></a>Abfragen von Entitäten
Das Abfragen und Auflisten von Entitäten ist unkompliziert und umfasst nur die HTTP-Anforderung GET und optional OData-Vorgänge.
Im folgenden Beispiel wird eine Liste aller MediaProcessor-Entitäten abgerufen.

    GET https://media.windows.net/API/MediaProcessors HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

Sie können auch eine bestimmte Entität oder alle Entitätenmengen im Zusammenhang mit einer bestimmten Entität abrufen, wie in den folgenden Beispielen:

    GET https://media.windows.net/API/JobTemplates('nb:jtid:UUID:e81192f5-576f-b247-b781-70a790c20e7c') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

    GET https://media.windows.net/API/JobTemplates('nb:jtid:UUID:e81192f5-576f-b247-b781-70a790c20e7c')/TaskTemplates HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

Im folgenden Beispiel wird nur die State-Eigenschaft aller Aufträge zurückgegeben.

    GET https://media.windows.net/API/Jobs?$select=State HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

Das folgende Beispiel gibt alle JobTemplates mit dem Namen „SampleTemplate“ zurück.

    GET https://media.windows.net/API/JobTemplates?$filter=startswith(Name,%20'SampleTemplate') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

> [!NOTE]
> Der $expand-Vorgang wird in Media Services nicht unterstützt, ebenso wie die in den LINQ-Informationen (WCF Data Services) beschriebenen LINQ-Methoden.
> 
> 

## <a name="enumerating-through-large-collections-of-entities"></a>Auflisten von großen Auflistungen von Entitäten
Beim Abfragen von Entitäten gibt es ein Limit von 1.000 Entitäten, die gleichzeitig zurückgegeben werden können, da die öffentliche REST-Version 2 Abfrageergebnisse auf 1.000 Ergebnisse begrenzt. Verwenden Sie **skip** und **top**, um die große Sammlung von Entitäten aufzuzählen. 

Das folgende Beispiel zeigt, wie Sie **skip** und **top** zum Überspringen der ersten 2000 Aufträge und Abrufen der nächsten 1000 Aufträge verwenden.  

    GET https://media.windows.net/api/Jobs()?$skip=2000&$top=1000 HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: media.windows.net

## <a name="updating-entities"></a>Aktualisieren von Entitäten
Abhängig vom Typ und dem Status der Entität können Sie Eigenschaften für diese Entität über die HTTP-Anforderungen PATCH, PUT oder MERGE aktualisieren. Weitere Informationen zu diesen Vorgängen finden Sie unter [PATCH, PUT, MERGE](https://msdn.microsoft.com/library/dd541276.aspx).

Im folgenden Codebeispiel wird veranschaulicht, wie die Name-Eigenschaft für eine Asset-Entität aktualisiert wird.

    MERGE https://media.windows.net/API/Assets('nb:cid:UUID:80782407-3f87-4e60-a43e-5e4454232f60') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: media.windows.net
    Content-Length: 21
    Expect: 100-continue

    {"Name" : "NewName" }

## <a name="deleting-entities"></a>Löschen von Entitäten
Entitäten können in Media Services mithilfe der HTTP-Anforderung DELETE gelöscht werden. Abhängig von der Entität kann die Reihenfolge, in der Sie Entitäten löschen, wichtig sein. Für Entitäten wie Medienobjekte müssen Sie z. B. alle Locators widerrufen (oder löschen), die auf ein bestimmtes Medienobjekt verweisen, bevor das Medienobjekt gelöscht wird.

Im folgenden Beispiel wird das Löschen eines Locator veranschaulicht, mit dem eine Datei in den BLOB-Speicher hochgeladen wurde.

    DELETE https://media.windows.net/API/Locators('nb:lid:UUID:76dcc8e8-4230-463d-97b0-ce25c41b5c8d') HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: media.windows.net
    Content-Length: 0

## <a name="media-services-learning-paths"></a>Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

