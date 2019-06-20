---
title: 'Gewusst wie: Prüfen des Auftragsfortschritts mit der REST-API | Microsoft Docs'
description: Erfahren Sie, wie Sie den Auftragsfortschritt verfolgen.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: a1a1f956-c035-448a-af9c-5ac15fcce9dd
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 378a90b712af031f9d89758939afb993cee5aafd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64869505"
---
# <a name="how-to-check-job-progress"></a>Vorgehensweise: Prüfen des Auftragsfortschritts

> [!NOTE]
> Media Services v2 werden derzeit keine neuen Features oder Funktionen hinzugefügt. <br/>Sehen Sie sich die neuste Version – [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/) – an. Lesen Sie außerdem die [Hinweise zur Migration von v2 zu v3](../latest/migrate-from-v2-to-v3.md).

Beim Ausführen von Aufträgen ist es nützlich, deren Fortschritt verfolgen zu können. Sie können den Auftragsstatus mithilfe der State-Eigenschaft des Auftrags feststellen. Weitere Informationen zur State-Eigenschaft finden Sie unter [Eigenschaften von Auftragsentitäten](https://docs.microsoft.com/rest/api/media/operations/job#job_entity_properties).

## <a name="connect-to-media-services"></a>Verbinden mit Mediendiensten

Informationen zum Herstellen einer Verbindung mit der AMS-API finden Sie unter [Zugreifen auf die Azure Media Services-API per Azure AD-Authentifizierung](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="check-job-progress"></a>Prüfen des Auftragsstatus

Anforderung:

    GET https://media.windows.net/api/Jobs()?$filter=Id%20eq%20'nb%3Ajid%3AUUID%3Af3c43f94-327f-2347-90bb-3bf79f8559f1'&$top=1 HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    Host: media.windows.net

Antwort:

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 450
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: d9b83c57-e26c-4d10-a20b-2be634c4b6a8
    request-id: 91d2be35-20ed-4e1c-a147-e82cd000c193
    x-ms-request-id: 91d2be35-20ed-4e1c-a147-e82cd000c193
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains

    {"odata.metadata":"https://media.windows.net/api/$metadata#Jobs","value":[{"Id":"nb:jid:UUID:f3c43f94-327f-2347-90bb-3bf79f8559f1","Name":"Encoding BigBuckBunny into to H264 Adaptive Bitrate MP4 Set 720p","Created":"2015-02-11T01:46:08.897","LastModified":"2015-02-11T01:46:08.897","EndTime":null,"Priority":0,"RunningDuration":0.0,"StartTime":"2015-02-11T01:46:16.58","State":2,"TemplateId":null,"JobNotificationSubscriptions":[]}]} 


## <a name="media-services-learning-paths"></a>Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Weitere Informationen

[Übersicht über die Media Services Operations-REST-API](media-services-rest-how-to-use.md)
