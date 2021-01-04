---
title: Entwickeln mit v3-APIs
titleSuffix: Azure Media Services
description: Erfahren Sie mehr über Regeln, die für Entitäten und APIs gelten, wenn Sie mit Media Services v3 entwickeln.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 10/23/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 416fb9fc4ce0622a710f2c119942edc4986ddd06
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790575"
---
# <a name="develop-with-media-services-v3-apis"></a>Entwickeln mit Media Services v3-APIs

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Als Entwickler können Sie die [REST-API](/rest/api/media/) von Media Services oder Clientbibliotheken verwenden, die es Ihnen ermöglichen, mit der REST-API auf einfache Weise zu interagieren, um benutzerdefinierte Medienworkflows zu erstellen, zu verwalten und zu pflegen. Die [Media Services v3](https://aka.ms/ams-v3-rest-sdk)-API basiert auf der OpenAPI-Spezifikation (ehemals Swagger).

In diesem Artikel werden Regeln erläutert, die für Entitäten und APIs gelten, wenn Sie mit Media Services v3 entwickeln.

## <a name="accessing-the-azure-media-services-api"></a>Zugreifen auf die Azure Media Services-API

Sie müssen zuerst authentifiziert werden, um berechtigt zu sein, auf Media Services-Ressourcen und die Media Services-API zuzugreifen. Für Media Services wird die [Azure Active Directory-basierte](../../active-directory/fundamentals/active-directory-whatis.md) Authentifizierung unterstützt. Zwei häufige Authentifizierungsoptionen sind:
 
* **Dienstprinzipalauthentifizierung** : Wird zur Authentifizierung eines Dienstes verwendet (z. B. Web-Apps, Funktions-Apps, Logik-Apps, API und Microservices). Bei Anwendungen, die diese Authentifizierungsmethode normalerweise nutzen, handelt es sich um Apps, mit denen Daemondienste, Dienste der mittleren Ebene oder geplante Aufträge ausgeführt werden. Beispielsweise sollte es für Web-Apps immer einen Dienst der mittleren Ebene geben, der sich mit einem Dienstprinzipal mit Media Services verbindet.
* **Benutzerauthentifizierung** : Dient zum Authentifizieren einer Person, die die App für die Interaktion mit Media Services-Ressourcen verwendet. Die interaktive App sollte den Benutzer zuerst zur Eingabe seiner Anmeldeinformationen auffordern. Ein Beispiel hierfür ist eine Verwaltungskonsolen-App, die von autorisierten Benutzern zum Überwachen von Codierungsaufträgen oder Livestreaming verwendet wird.

Die Media Services-API erfordert, dass der Benutzer oder die App, die die REST-API-Anforderungen sendet, Zugriff auf die Media Services-Kontoressource hat und eine Rolle **Mitwirkender** oder **Besitzer** verwendet. Auf die API kann mit der Rolle **Leser** zugegriffen werden, es sind aber nur Vorgänge vom Typ **Get** und **List** verfügbar. Weitere Informationen finden Sie unter [Rollenbasierte Zugriffssteuerung von Azure (Azure RBAC) für Media Services-Konten](rbac-overview.md).

Anstatt ein Dienstprinzipal zu erstellen, sollten Sie die Verwendung verwalteter Identitäten für Azure-Ressourcen in Betracht ziehen, um über den Azure Resource Manager auf die Media Services-API zuzugreifen. Weitere Informationen zu verwalteten Identitäten für Azure-Ressourcen finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-ad-service-principal"></a>Azure AD-Dienstprinzipal

Die Azure AD-App und der Dienstprinzipal sollten sich im selben Mandanten befinden. Nachdem Sie die App erstellt haben, können Sie für die App den Zugriff auf das Media Services-Konto über die Rolle **Mitwirkender** oder **Besitzer** gewähren.

Wenn Sie sich nicht sicher sind, ob Sie über Berechtigungen zum Erstellen einer Azure AD-App verfügen, lesen Sie [Erforderliche Berechtigungen](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

In der folgenden Abbildung stellen die Zahlen den Fluss der Anforderungen in chronologischer Reihenfolge dar:

![Authentifizierung von Apps der mittleren Ebene mit AAD über eine Web-API](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. Eine App der mittleren Ebene fordert ein Azure AD-Zugriffstoken an, das die folgenden Parameter enthält:  

   * Azure AD-Mandantenendpunkt.
   * Media Services-Ressourcen-URI.
   * Ressourcen-URI für REST Media Services
   * Werte der Azure AD-App: Client-ID und Clientgeheimnis

   Informationen zum Abrufen aller erforderlichen Werte finden Sie in [Zugriff auf Azure Media Services API](./access-api-howto.md).

2. Das Azure AD-Zugriffstoken wird an die mittlere Ebene gesendet.
4. Die mittlere Ebene sendet eine Anforderung mit dem Azure AD-Token an die Azure Media-REST-API.
5. Die mittlere Ebene erhält die Daten von den Media Services zurück.

### <a name="samples"></a>Beispiele

Sehen Sie sich die folgenden Beispiele an, die zeigen, wie Sie sich mit dem Azure AD-Dienstprinzipal verbinden können:

* [Herstellen einer Verbindung mit REST](media-rest-apis-with-postman.md)  
* [Herstellen einer Verbindung mit Java](configure-connect-java-howto.md)
* [Herstellen einer Verbindung mit .NET](configure-connect-dotnet-howto.md)
* [Herstellen einer Verbindung mit Node.js](configure-connect-nodejs-howto.md)
* [Herstellen einer Verbindung mit Python](configure-connect-python-howto.md)

## <a name="naming-conventions"></a>Benennungskonventionen

Azure Media Services-v3-Ressourcennamen (beispielsweise Objekte, Aufträge und Transformationen) unterliegen den Namenseinschränkungen von Azure Resource Manager. Ressourcennamen sind gemäß den Vorgaben von Azure Resource Manager immer eindeutig. Daher können Sie beliebige Zeichenfolgen für eindeutige Bezeichner (beispielsweise GUIDs) als Ressourcennamen verwenden.

Media Services-Ressourcennamen dürfen Folgendes nicht enthalten: „<“, „>“, „%“, „&“, „:“, „&#92;“, „?“, „/“, „*“, „+“, „.“, einzelne Anführungszeichen oder Steuerzeichen. Alle anderen Zeichen sind zulässig. Ein Ressourcenname darf maximal 260 Zeichen lang sein.

Weitere Informationen zu Azure Resource Manager-Benennungen finden Sie in den [Benennungsanforderungen](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) und in den [Benennungskonventionen](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).

### <a name="names-of-filesblobs-within-an-asset"></a>Namen von Dateien/Blobs in einer Ressource

Die Namen von Dateien/Blobs innerhalb einer Ressource müssen die [Anforderungen an Blobnamen](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) und die [Anforderungen an NTFS-Namen](/windows/win32/fileio/naming-a-file) erfüllen. Diese Anforderungen bestehen, da die Dateien zur Verarbeitung aus Blob Storage auf einen lokalen NTFS-Datenträger kopiert werden können.

## <a name="long-running-operations"></a>Zeitintensive Vorgänge

Die in den [swagger-Dateien](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) von Azure Media Services mit `x-ms-long-running-operation` gekennzeichneten Vorgänge sind zeitintensive Vorgänge. 

Weitere Informationen zum Verfolgen asynchroner Azure-Vorgänge finden Sie unter [Asynchrone Vorgänge](../../azure-resource-manager/management/async-operations.md).

Media Services verfügt über die folgenden zeitintensiven Vorgänge:

* [Erstellen von Liveereignissen](/rest/api/media/liveevents/create)
* [Aktualisieren von Liveereignissen](/rest/api/media/liveevents/update)
* [Löschen von Liveereignissen](/rest/api/media/liveevents/delete)
* [Starten von Liveereignissen](/rest/api/media/liveevents/start)
* [Beenden von Liveereignissen](/rest/api/media/liveevents/stop)

  Verwenden Sie den Parameter `removeOutputsOnStop`, um beim Beenden des Ereignisses alle zugehörigen Liveausgaben zu löschen.  
* [Zurücksetzen eines Liveereignisses](/rest/api/media/liveevents/reset)
* [Erstellen einer Liveausgabe](/rest/api/media/liveevents/create)
* [Löschen einer Liveausgabe](/rest/api/media/liveevents/delete)
* [Erstellen eines Streamingendpunkts](/rest/api/media/streamingendpoints/create)
* [Aktualisieren eines Streamingendpunkts](/rest/api/media/streamingendpoints/update)
* [Löschen eines Streamingendpunkts](/rest/api/media/streamingendpoints/delete)
* [Starten eines Streamingendpunkts](/rest/api/media/streamingendpoints/start)
* [Beenden eines Streamingendpunkts](/rest/api/media/streamingendpoints/stop)
* [Skalieren eines Streamingendpunkts](/rest/api/media/streamingendpoints/scale)

Nach erfolgreicher Übermittlung eines zeitintensiven Vorgangs erhalten Sie eine Bestätigung vom Typ „201 – Erstellt“ und müssen anhand der zurückgegebenen Vorgangs-ID abfragen, ob der Vorgang abgeschlossen ist.

Der Artikel [Nachverfolgen asynchroner Vorgänge in Azure](../../azure-resource-manager/management/async-operations.md) enthält ausführliche Informationen zur Nachverfolgung des Status asynchroner Azure-Vorgänge anhand von Werten aus der zurückgegebenen Antwort.

Für jedes Liveereignis bzw. für jede zugehörige Liveausgabe wird jeweils nur ein einzelner zeitintensiver Vorgang unterstützt. Wurde ein zeitintensiver Vorgang gestartet, muss er erst abgeschlossen werden, bevor der nächste zeitintensive Vorgang für das gleiche Liveereignis oder für eine der zugehörigen Liveausgaben gestartet wird. Bei Liveereignissen mit mehreren Liveausgaben müssen Sie warten, bis ein zeitintensiver Vorgang für eine Liveausgabe abgeschlossen wurde, bevor Sie einen zeitintensiven Vorgang für eine weitere Liveausgabe auslösen.

## <a name="sdks"></a>SDKs

> [!NOTE]
> Für die Azure Media Services v3 SDKs wird keine Threadsicherheit garantiert. Wenn Sie eine Multithread-App entwickeln, sollten Sie zum Schutz des Clients Ihre eigene Threadsynchronisierungslogik hinzufügen oder für jeden Thread ein neues AzureMediaServicesClient-Objekt verwenden. Achten Sie außerdem auf Multithreadingprobleme, die durch optionale Objekte entstehen können, die von Ihrem Code für den Client bereitgestellt werden (beispielsweise eine HttpClient-Instanz in .NET).

|SDK|Verweis|
|---|---|
|[.NET SDK](https://aka.ms/ams-v3-dotnet-sdk)|[.NET-Referenz](/dotnet/api/overview/azure/mediaservices/management)|
|[Java SDK](https://aka.ms/ams-v3-java-sdk)|[Java-Referenz](/java/api/overview/azure/mediaservices/management)|
|[Python SDK](https://aka.ms/ams-v3-python-sdk)|[Python-Referenz](/python/api/overview/azure/mediaservices/management)|
|[Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk) |[Node.js-Referenz](/javascript/api/overview/azure/mediaservices/management)| 
|[Go SDK](https://aka.ms/ams-v3-go-sdk) |[Go-Referenz](https://aka.ms/ams-v3-go-ref)|
|[Ruby SDK](https://aka.ms/ams-v3-ruby-sdk)||

### <a name="see-also"></a>Weitere Informationen

- [EventGrid .NET SDK mit Media Services-Ereignissen](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Definitionen von Media Services-Ereignissen](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)

## <a name="azure-media-services-explorer"></a>Azure Media Services Explorer

[Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) ist ein Tool für Windows-Kunden, die sich über Media Services informieren möchten. Bei AMSE handelt es sich um eine WinForms-/C#-Anwendung zum Hochladen, Herunterladen, Codieren und Streamen von VoD- und Liveinhalten mit Media Services. Das AMSE-Tool richtet sich an Kunden, die Media Services ohne Programmieraufwand testen möchten. Der AMSE-Code wird als Ressource für Kunden bereitgestellt, die mit Media Services entwickeln möchten.

AMSE ist ein Open-Source-Projekt mit communitybasiertem Support. (Probleme können hier gemeldet werden: https://github.com/Azure/Azure-Media-Services-Explorer/issues) Für dieses Projekt gelten die Microsoft-Verhaltensregeln für Open Source ([Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/)). Weitere Informationen finden Sie in den [häufig gestellten Fragen zum Verhaltenskodex](https://opensource.microsoft.com/codeofconduct/faq/). Sie können sich auch an opencode@microsoft.com wenden, wenn Sie weitere Fragen oder Anmerkungen haben.

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Filterung, Sortierung und Paginierung von Media Services-Entitäten

Informationen finden Sie unter [Filterung, Sortierung, Paginierung von Azure Media Services-Entitäten](entities-overview.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Fragen stellen, Feedback geben, Updates abrufen

Im Artikel [Azure Media Services-Community](media-services-community.md) finden Sie verschiedene Möglichkeiten, Fragen zu stellen, Feedback zu geben und Updates zu Media Services zu bekommen.

## <a name="see-also"></a>Weitere Informationen

Informationen zum Abrufen aller erforderlichen Werte finden Sie in [Zugriff auf Azure Media Services API](./access-api-howto.md).

## <a name="next-steps"></a>Nächste Schritte

* [Herstellen einer Verbindung mit Media Services mit Java](configure-connect-java-howto.md)
* [Herstellen einer Verbindung mit Media Services mit .NET](configure-connect-dotnet-howto.md)
* [Herstellen einer Verbindung mit Media Services mit Node.js](configure-connect-nodejs-howto.md)
* [Herstellen einer Verbindung mit Media Services mit Python](configure-connect-python-howto.md)