---
title: Entwickeln mit v3-APIs – Azure | Microsoft-Dokumentation
description: In diesem Artikel werden Regeln erläutert, die für Entitäten und APIs gelten, wenn Sie mit Media Services v3 entwickeln.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/02/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: ca4e343ea4774bbe4ff992ad671575b150b3c045
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65890967"
---
# <a name="developing-with-media-services-v3-apis"></a>Entwickeln mit Media Services v3-APIs

Als Entwickler können Sie die [REST-API](https://aka.ms/ams-v3-rest-ref) von Media Services oder Clientbibliotheken verwenden, die es Ihnen ermöglichen, mit der REST-API auf einfache Weise zu interagieren, um benutzerdefinierte Medienworkflows zu erstellen, zu verwalten und zu pflegen. Die [Media Services v3](https://aka.ms/ams-v3-rest-sdk)-API basiert auf der OpenAPI-Spezifikation (ehemals Swagger).

In diesem Artikel werden Regeln erläutert, die für Entitäten und APIs gelten, wenn Sie mit Media Services v3 entwickeln.

## <a name="accessing-the-azure-media-services-api"></a>Zugreifen auf die Azure Media Services-API

Sie müssen zuerst authentifiziert werden, um berechtigt zu sein, auf Media Services-Ressourcen und die Media Services-API zuzugreifen. Für Media Services wird die [Azure Active Directory-basierte](../../active-directory/fundamentals/active-directory-whatis.md) Authentifizierung unterstützt. Zwei häufige Authentifizierungsoptionen sind:
 
* **Dienstprinzipalauthentifizierung** – wird zur Authentifizierung eines Dienstes verwendet (z.B. Web-Apps, Funktions-Apps, Logik-Apps, API und Microservices). Bei Anwendungen, die diese Authentifizierungsmethode normalerweise nutzen, handelt es sich um Apps, mit denen Daemondienste, Dienste der mittleren Ebene oder geplante Aufträge ausgeführt werden. Beispielsweise sollte es für Web-Apps immer einen Dienst der mittleren Ebene geben, der sich mit einem Dienstprinzipal mit Media Services verbindet.
* **Benutzerauthentifizierung** – wird zur Authentifizierung einer Person verwendet, die die App zum Interagieren mit Media Services-Ressourcen verwendet. Die interaktive Anwendung sollte den Benutzer zuerst zur Eingabe seiner Anmeldeinformationen auffordern. Ein Beispiel hierfür ist eine Verwaltungskonsolen-App, die von autorisierten Benutzern zum Überwachen von Codierungsaufträgen oder Livestreaming verwendet wird.

Die Media Services-API erfordert, dass der Benutzer oder die Anwendung, die die REST-API-Anforderungen sendet, Zugriff auf die Media Services-Kontoressource hat und eine Rolle **Mitwirkender** oder **Besitzer** verwendet. Auf die API kann mit der Rolle **Leser** zugegriffen werden, es sind aber nur **Get**- oder **List** -Operationen verfügbar. Weitere Informationen finden Sie unter [Rollenbasierte Zugriffssteuerung für Media Services-Konten](rbac-overview.md).

Anstatt ein Dienstprinzipal zu erstellen, sollten Sie die Verwendung verwalteter Identitäten für Azure-Ressourcen in Betracht ziehen, um über den Azure Resource Manager auf die Media Services-API zuzugreifen. Weitere Informationen zu verwalteten Identitäten für Azure-Ressourcen finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-ad-service-principal"></a>Azure AD-Dienstprinzipal 

Wenn Sie eine Azure AD-Anwendung und ein Dienstprinzipal erstellen, muss sich die Anwendung in ihrem eigenen Mandanten befinden. Nachdem Sie die Anwendung erstellt haben, können Sie für die App den Zugriff auf das Media Services-Konto über die Rolle **Mitwirkender** oder **Besitzer** gewähren. 

Wenn Sie sich nicht sicher sind, ob Sie über Berechtigungen zum Erstellen einer Azure AD-Anwendung verfügen, lesen Sie bitte [Erforderliche Berechtigungen](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

In der folgenden Abbildung stellen die Zahlen den Fluss der Anforderungen in chronologischer Reihenfolge dar:

![Apps der mittleren Ebene](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. Eine App der mittleren Ebene fordert ein Azure AD-Zugriffstoken an, das die folgenden Parameter enthält:  

   * Azure AD-Mandantenendpunkt.
   * Media Services-Ressourcen-URI.
   * Ressourcen-URI für REST Media Services
   * Werte der Azure AD-Anwendung: Client-ID und geheimer Clientschlüssel.
   
   Informationen zum Abrufen aller erforderlichen Werte finden Sie in [Zugriff auf Azure Media Services API mit Azure CLI](access-api-cli-how-to.md)

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

Weitere Informationen zur Benennung in Azure Resource Manager finden Sie unter: [Namensanforderungen](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) und [Namenskonvention](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="long-running-operations"></a>Zeitintensive Vorgänge

Die in den [swagger-Dateien](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) von Azure Media Services mit `x-ms-long-running-operation` gekennzeichneten Vorgänge sind zeitintensive Vorgänge. 

Weitere Informationen zum Verfolgen asynchroner Azure-Vorgänge finden Sie unter [Asynchrone Vorgänge](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation).

Media Services verfügt über die folgenden zeitintensiven Vorgänge:

* Create LiveEvent
* Update LiveEvent
* Delete LiveEvent
* Start LiveEvent
* Stop LiveEvent
* Reset LiveEvent
* Create LiveOutput
* Delete LiveOutput
* Create StreamingEndpoint
* Update StreamingEndpoint
* Delete StreamingEndpoint
* Start StreamingEndpoint
* Stop StreamingEndpoint
* Scale StreamingEndpoint


## <a name="sdks"></a>SDKs

> [!NOTE]
> Für die Azure Media Services v3 SDKs wird keine Threadsicherheit garantiert. Wenn Sie eine Multithreadanwendung entwickeln, sollten Sie zum Schutz des Clients Ihre eigene Threadsynchronisierungslogik hinzufügen oder für jeden Thread ein neues AzureMediaServicesClient-Objekt verwenden. Achten Sie außerdem auf Multithreadingprobleme, die durch optionale Objekte entstehen können, die von Ihrem Code für den Client bereitgestellt werden (beispielsweise eine HttpClient-Instanz in .NET).

|SDK|Verweis|
|---|---|
|[.NET SDK](https://aka.ms/ams-v3-dotnet-sdk)|[.NET-Referenz](https://aka.ms/ams-v3-dotnet-ref)|
|[Java SDK](https://aka.ms/ams-v3-java-sdk)|[Java-Referenz](https://aka.ms/ams-v3-java-ref)|
|[Python SDK](https://aka.ms/ams-v3-python-sdk)|[Python-Referenz](https://aka.ms/ams-v3-python-ref)|
|[Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk) |[Node.js-Referenz](https://aka.ms/ams-v3-nodejs-ref)| 
|[Go SDK](https://aka.ms/ams-v3-go-sdk) |[Go-Referenz](https://aka.ms/ams-v3-go-ref)|
|[Ruby SDK](https://aka.ms/ams-v3-ruby-sdk)||

### <a name="see-also"></a>Weitere Informationen

- [EventGrid .NET SDK mit Media Services-Ereignissen](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Definitionen von Media Services-Ereignissen](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)

## <a name="azure-media-services-explorer"></a>Azure Media Services Explorer

[Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) ist ein Tool für Windows-Kunden, die sich über Media Services informieren möchten. Bei AMSE handelt es sich um eine WinForms-/C#-Anwendung zum Hochladen, Herunterladen, Codieren und Streamen von VoD- und Liveinhalten mit Media Services. Das AMSE-Tool richtet sich an Kunden, die Media Services ohne Programmieraufwand testen möchten. Der AMSE-Code wird als Ressource für Kunden bereitgestellt, die mit Media Services entwickeln möchten.

AMSE ist ein Open-Source-Projekt mit communitybasiertem Support. (Probleme können hier gemeldet werden: https://github.com/Azure/Azure-Media-Services-Explorer/issues) Für dieses Projekt wird der [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/) (Verhaltenskodex für Microsoft Open Source) beachtet. Weitere Informationen finden Sie in den [häufig gestellten Fragen zum Verhaltenskodex](https://opensource.microsoft.com/codeofconduct/faq/). Sie können sich auch an opencode@microsoft.com wenden, wenn Sie weitere Fragen oder Anmerkungen haben.

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Filterung, Sortierung und Paginierung von Media Services-Entitäten

Informationen finden Sie unter [Filterung, Sortierung, Paginierung von Azure Media Services-Entitäten](entities-overview.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Fragen stellen, Feedback geben, Updates abrufen

Im Artikel [Azure Media Services-Community](media-services-community.md) finden Sie verschiedene Möglichkeiten, Fragen zu stellen, Feedback zu geben und Updates zu Media Services zu bekommen.

## <a name="see-also"></a>Weitere Informationen

[Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>Nächste Schritte

* [Herstellen einer Verbindung mit Media Services mit Java](configure-connect-java-howto.md)
* [Herstellen einer Verbindung mit Media Services mit .NET](configure-connect-dotnet-howto.md)
* [Herstellen einer Verbindung mit Media Services mit Node.js](configure-connect-nodejs-howto.md)
* [Herstellen einer Verbindung mit Media Services mit Python](configure-connect-python-howto.md)
