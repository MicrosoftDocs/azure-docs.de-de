---
title: Erste Schritte zum Bereitstellen von Inhalten nach Bedarf mithilfe von REST | Microsoft Docs
description: Dieses Lernprogramm führt Sie durch die Schritte zum Implementieren einer Anwendung zur Übermittlung von Inhalten nach Bedarf mit Azure Media Services mithilfe der REST-API.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 88194b59-e479-43ac-b179-af4f295e3780
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 76eae5fa049ed1fbf7195277613867aca63c1082
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64867636"
---
# <a name="get-started-with-delivering-content-on-demand-using-rest"></a>Erste Schritte zum Bereitstellen von Inhalten nach Bedarf mithilfe von REST  

> [!NOTE]
> Media Services v2 werden derzeit keine neuen Features oder Funktionen hinzugefügt. <br/>Sehen Sie sich die neuste Version – [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/) – an. Lesen Sie außerdem die [Hinweise zur Migration von v2 zu v3](../latest/migrate-from-v2-to-v3.md).

Dieser Schnellstart führt Sie durch die Schritte zum Implementieren einer Anwendung zur Video-on-Demand (VoD)-Inhaltsübermittlung mit Azure Media Services (AMS)-REST-APIs.

Das Lernprogramm stellt den Media Services-Arbeitsfluss und die gängigsten Programmierobjekte und Aufgaben für die Media Services-Entwicklung vor. Nach Abschluss des Tutorials sind Sie in der Lage, eine einfache Mediendatei zu streamen oder progressiv herunterzuladen, die Sie hochgeladen, codiert und heruntergeladen haben.

Die folgende Abbildung zeigt einige der am häufigsten verwendeten Objekte beim Entwickeln von VoD-Anwendungen mit dem Media Services OData-Modell.

Klicken Sie auf Bild, um es in voller Größe anzeigen.  

<a href="./media/media-services-rest-get-started/media-services-overview-object-model.png" target="_blank"><img src="./media/media-services-rest-get-started/media-services-overview-object-model-small.png"></a> 

## <a name="prerequisites"></a>Voraussetzungen
Für den Einstieg in die Entwicklung mit Media Services mithilfe von REST-APIs gelten die folgenden Voraussetzungen:

* Ein Azure-Konto. Ausführliche Informationen finden Sie unter [Einen Monat kostenlos testen](https://azure.microsoft.com/pricing/free-trial/).
* Media Services-Konto. Informationen zum Erstellen eines Media Services-Kontos finden Sie unter [Gewusst wie: Erstellen eines Media Services Kontos](media-services-portal-create-account.md).
* Kenntnisse darüber, wie Sie mit der Media Services-REST-API Code entwickeln. Weitere Informationen finden Sie unter [Media Services-REST-API – Übersicht](media-services-rest-how-to-use.md).
* Eine Anwendung Ihrer Wahl, die HTTP-Anforderungen und -Antworten senden kann. In diesem Lernprogramm wird [Fiddler](https://www.telerik.com/download/fiddler)verwendet.

Die folgenden Aufgaben werden in diesem Schnellstart beschrieben.

1. Starten von Streamingendpunkten (mithilfe des Azure-Portals)
2. Herstellen einer Verbindung mit dem Media Services-Konto mit der REST-API
3. Erstellen eines neuen Medienobjekts und Hochladen einer Videodatei mit der REST-API
4. Codieren der Quelldatei in einen Satz von MP4-Dateien mit adaptiver Bitrate mit der REST-API
5. Veröffentlichen des Medienobjekts und Abrufen von URLs für Streaming und progressiven Download mit der REST-API
6. Wiedergeben Ihrer Inhalte

>[!NOTE]
>Es gilt ein Grenzwert von 1.000.000 Richtlinien für verschiedene AMS-Richtlinien (z.B. für die Locator-Richtlinie oder für ContentKeyAuthorizationPolicy). Wenn Sie immer die gleichen Tage/Zugriffsberechtigungen verwenden, z. B. Richtlinien für Locator, die für einen längeren Zeitraum vorgesehen sind (Richtlinien ohne Upload), verwenden Sie dieselbe Richtlinien-ID. Weitere Informationen dazu finden Sie in [diesem Artikel](media-services-dotnet-manage-entities.md#limit-access-policies).

Weitere Informationen zu AMS-REST-Entitäten, die in diesem Artikel verwendet werden, finden Sie unter [Azure Media Services REST-API-Referenz](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference). Siehe auch [Azure Media Services-Konzepte](media-services-concepts.md).

>[!NOTE]
>Wenn Sie in Media Services auf Entitäten zugreifen, müssen Sie bestimmte Headerfelder und Werte in Ihren HTTP-Anforderungen festlegen. Weitere Informationen finden Sie unter [Installation für die Entwicklung mit der Media Services-REST-API](media-services-rest-how-to-use.md).

## <a name="start-streaming-endpoints-using-the-azure-portal"></a>Starten von Streamingendpunkten mithilfe des Azure-Portals

Wenn Sie mit Azure Media Services arbeiten, besteht eines der häufigsten Szenarien darin, Videos per Adaptive Bitrate Streaming zu übermitteln. Media Services bietet dynamische Paketerstellung für die Just-in-Time-Übermittlung von Daten vom Typ „MP4-codierte Inhalte mit adaptiver Bitrate“ in Streamingformaten, die von Media Services unterstützt werden (MPEG DASH, HLS, Smooth Streaming), ohne dass Sie jeweils vorab verpackte Versionen dieser Streamingformate speichern müssen.

>[!NOTE]
>Beim Erstellen Ihres AMS-Kontos wird dem Konto ein **Standard**-Streamingendpunkt mit dem Status **Beendet** hinzugefügt. Um mit dem Streamen der Inhalte zu beginnen und die dynamische Paketerstellung und dynamische Verschlüsselung zu nutzen, muss der Streamingendpunkt, von dem Sie Inhalte streamen möchten, den Status **Wird ausgeführt** aufweisen.

Führen Sie folgende Schritte aus, um den Streamingendpunkt zu starten:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/)an.
2. Klicken Sie im Fenster „Einstellungen“ auf „Streamingendpunkte“.
3. Klicken Sie auf den standardmäßigen Streamingendpunkt.

    Das Fenster DEFAULT STREAMING ENDPOINT DETAILS (DETAILS ZUM STANDARD-STREAMINGENDPUNKT) wird angezeigt.

4. Klicken Sie auf das Symbol „Start“.
5. Klicken Sie auf die Schaltfläche „Speichern“, um die Änderungen zu speichern.

## <a id="connect"></a>Herstellen einer Verbindung mit dem Media Services-Konto mit der REST-API

Informationen zum Herstellen einer Verbindung mit der AMS-API finden Sie unter [Zugreifen auf die Azure Media Services-API per Azure AD-Authentifizierung](media-services-use-aad-auth-to-access-ams-api.md). 

## <a id="upload"></a>Erstellen eines neuen Medienobjekts und Hochladen einer Videodatei mit der REST-API

In Media Services laden Sie Ihre digitalen Dateien in ein Medienobjekt hoch. Die Entität **Asset** kann Videos, Audiodateien, Bilder, Miniaturansichtssammlungen, Texttitel und Untertiteldateien (und die Metadaten zu diesen Dateien) enthalten.  Nachdem die Dateien in das Medienobjekt hochgeladen wurden, werden Ihre Inhalte zur weiteren Verarbeitung und zum Streaming sicher in der Cloud gespeichert.

Zu den Werten, die Sie festlegen müssen, gehören Optionen für die Erstellung von Medienobjekten. Die Eigenschaft **Options** ist ein Aufzählungswert, der die Verschlüsselungsoptionen beschreibt, mit denen ein Medienobjekt erstellt werden kann. Gültig sind einzelne Werte aus der folgenden Liste. Es ist jedoch nicht zulässig, Werte aus dieser Liste zu kombinieren:

* **None** = **0**: Es wird keine Verschlüsselung verwendet. Bei Verwendung dieser Option sind Ihre Inhalte während der Übertragung oder im Ruhezustand im Speicher nicht geschützt.
    Wenn Sie planen, eine MP4-Datei über progressives Herunterladen zu übermitteln, verwenden Sie diese Option.
* **StorageEncrypted** = **1**: Verschlüsselt Ihre Inhalte lokal mithilfe von AES-256-Bit-Verschlüsselung und lädt sie dann in Azure Storage hoch, wo sie verschlüsselt im Ruhezustand gespeichert werden. Medienobjekte, die durch Storage Encryption geschützt sind, werden automatisch entschlüsselt, vor der Codierung in einem verschlüsselten Dateisystem platziert und optional vor dem Hochladen als neues Ausgabemedienobjekt erneut verschlüsselt. Der primäre Anwendungsfall für Storage Encryption ist, wenn Sie Ihre qualitativ hochwertigen Eingabemediendateien mit starker Verschlüsselung beim Speichern im Ruhezustand auf dem Datenträger sichern möchten.
* **CommonEncryptionProtected** = **2**: Verwenden Sie diese Option, wenn Sie Inhalte hochladen, die bereits verschlüsselt wurden und durch allgemeine Verschlüsselung oder PlayReady-DRM geschützt sind (z.B. mit PlayReady-DRM geschütztem Smooth Streaming).
* **EnvelopeEncryptionProtected** = **4**: Verwenden Sie diese Option, wenn Sie mit AES verschlüsseltes HLS hochladen. Die Dateien müssen von Transform Manager codiert und verschlüsselt worden sein.

### <a name="create-an-asset"></a>Erstellen eines Medienobjekts
Ein Medienobjekt ist ein Container für mehrere Typen oder Gruppen von Objekten in Media Services. Dazu gehören Videos, Audiodateien, Bilder, Miniaturansichtsammlungen, Texttitel und Untertiteldateien. In der REST-API muss beim Erstellen eines Medienobjekts eine POST-Anforderung an Media Services gesendet werden. Dabei müssen alle Eigenschaftsinformationen zum Medienobjekt im Anforderungstext enthalten sein.

Im folgenden Beispiel wird veranschaulicht, wie Sie ein Medienobjekt erstellen.

**HTTP-Anforderung**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Assets HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 45

    {"Name":"BigBuckBunny.mp4", "Options":"0"}


**HTTP-Antwort**

Im Erfolgsfall wird Folgendes zurückgegeben:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 452
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
    request-id: e98be122-ae09-473a-8072-0ccd234a0657
    x-ms-request-id: e98be122-ae09-473a-8072-0ccd234a0657
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:06:40 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
       "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "State":0,
       "Created":"2015-01-18T22:06:40.6010903Z",
       "LastModified":"2015-01-18T22:06:40.6010903Z",
       "AlternateId":null,
       "Name":"BigBuckBunny2.mp4",
       "Options":0,
       "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StorageAccountName":"storagetestaccount001"
    }

### <a name="create-an-assetfile"></a>Erstellen einer AssetFile
Die [AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) -Entität stellt eine Video- oder Audiodatei dar, die in einem Blob-Container gespeichert ist. Eine Medienobjektdatei ist immer mit einem Medienobjekt verknüpft, wobei ein Medienobjekt eine oder mehrere AssetFiles enthalten kann. Der Media Services Encoder-Task kann nicht ausgeführt werden, wenn ein Medienobjektdatei-Objekt keiner digitalen Datei in einem Blobcontainer zugeordnet ist.

Nachdem Sie Ihre digitale Mediendatei in einen Blobcontainer hochgeladen haben, verwenden Sie die **MERGE** -HTTP-Anforderung, um die AssetFile anhand von Informationen über Ihre Mediendatei zu aktualisieren (wie später in diesem Thema beschrieben).

**HTTP-Anforderung**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Files HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 164

    {  
       "IsEncrypted":"false",
       "IsPrimary":"false",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }


**HTTP-Antwort**

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 535
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5')
    Server: Microsoft-IIS/8.5
    request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    x-ms-request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 00:34:07 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Files/@Element",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "Name":"BigBuckBunny.mp4",
       "ContentFileSize":"0",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "EncryptionVersion":null,
       "EncryptionScheme":null,
       "IsEncrypted":false,
       "EncryptionKeyId":null,
       "InitializationVector":null,
       "IsPrimary":false,
       "LastModified":"2015-01-19T00:34:08.1934137Z",
       "Created":"2015-01-19T00:34:08.1934137Z",
       "MimeType":"video/mp4",
       "ContentChecksum":null
    }


### <a name="creating-the-accesspolicy-with-write-permission"></a>Erstellen der AccessPolicy mit Schreibberechtigung
Bevor Sie Dateien in den Blobspeicher hochladen, legen Sie die Zugriffsrichtlinienberechtigungen für das Schreiben in ein Medienobjekt fest. Senden Sie dazu eine HTTP POST-Anforderung an die AccessPolicies-Entitätenmenge. Definieren Sie bei der Erstellung einen DurationInMinutes-Wert, da Sie andernfalls eine Antwort mit einer Meldung „500 Interner Serverfehler“ empfangen. Weitere Informationen zu "AccessPolicies" finden Sie unter [AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy).

Im folgenden Beispiel wird veranschaulicht, wie eine AccessPolicy erstellt wird:

**HTTP-Anforderung**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 74

    {"Name":"NewUploadPolicy", "DurationInMinutes":"440", "Permissions":"2"}

**HTTP-Antwort**

Im Erfolgsfall wird die folgende Antwort zurückgegeben:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 312
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae')
    Server: Microsoft-IIS/8.5
    request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
    x-ms-request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:18:06 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#AccessPolicies/@Element",
       "Id":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "Created":"2015-01-18T22:18:06.6370575Z",
       "LastModified":"2015-01-18T22:18:06.6370575Z",
       "Name":"NewUploadPolicy",
       "DurationInMinutes":440.0,
       "Permissions":2
    }

### <a name="get-the-upload-url"></a>Abrufen der Upload-URL

Um die eigentliche Upload-URL zu empfangen, erstellen Sie einen SAS-Locator. Ein Locator definiert die Startzeit und den Typ des Verbindungsendpunkts für Clients, die auf Dateien in einem Medienobjekt zugreifen möchten. Sie können mehrere Locator-Entitäten für ein bestimmtes AccessPolicy-/ Asset-Paar erstellen, um unterschiedliche Clientanforderungen und -voraussetzungen zu verarbeiten. Jeder dieser Locators verwendet den „StartTime“-Wert plus den „DurationInMinutes“-Wert des „AccessPolicy“-Objekts, um zu bestimmen, für welchen Zeitraum eine URL verwendet werden kann. Weitere Informationen finden Sie unter [Locator](https://docs.microsoft.com/rest/api/media/operations/locator).

Eine SAS-URL weist das folgende Format auf:

    {https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

Folgende Überlegungen sollten berücksichtigt werden:

* Einem bestimmten Medienobjekt können jeweils nicht mehr als fünf eindeutige Locators zugeordnet sein. 
* Wenn Sie Ihre Dateien sofort hochladen müssen, sollten Sie Ihren StartTime-Wert auf fünf Minuten vor der aktuellen Uhrzeit festlegen. Dies ist erforderlich, weil ggf. eine Uhrzeitabweichung zwischen dem Clientcomputer und Media Services vorliegen kann. Darüber hinaus muss der StartTime-Wert das folgende DateTime-Format aufweisen: YYYY-MM-DDTHH:mm:ssZ (Beispiel: „2014-05-23T17:53:50Z“).    
* Gegebenenfalls tritt eine Verzögerung von 30 bis 40 Sekunden zwischen dem Erstellen eines Locators und seiner Verfügbarkeit auf. Dieses Problem gilt sowohl für [SAS-URLs](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) als auch für Ursprungslocators.

Das folgende Beispiel zeigt, wie Sie einen SAS URL-Locator gemäß der Type-Eigenschaft im Anforderungstext (1 für einen SAS-Locator und 2 für einen On-Demand-Ursprungslocator) erstellen können. Die zurückgegebene **Path** -Eigenschaft enthält die URL, die Sie für den Upload der Datei verwenden müssen.

**HTTP-Anforderung**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Locators HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 178

    {  
       "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StartTime":"2015-02-18T16:45:53",
       "Type":1
    }


**HTTP-Antwort**

Im Erfolgsfall wird die folgende Antwort zurückgegeben:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 949
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54')
    Server: Microsoft-IIS/8.5
    request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
    x-ms-request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 03:01:29 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Locators/@Element",
       "Id":"nb:lid:UUID:af57bdd8-6751-4e84-b403-f3c140444b54",
       "ExpirationDateTime":"2015-02-19T00:05:53",
       "Type":1,
       "Path":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
       "BaseUri":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247",
       "ContentAccessComponent":"?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
       "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StartTime":"2015-02-18T16:45:53",
       "Name":null
    }

### <a name="upload-a-file-into-a-blob-storage-container"></a>Hochladen einer Datei in einen Blobspeichercontainer
Nachdem Sie AccessPolicy und Locator konfiguriert haben, können Sie die eigentliche Datei mithilfe der Azure Storage-REST-APIs in einen Azure Blob Storage-Container hochladen. Sie müssen die Dateien als Blockblobs hochladen. Seitenblobs werden von Azure Media Services nicht unterstützt.  

> [!NOTE]
> Sie müssen den Dateinamen der Uploaddatei in den **Path** -Wert des Locators einfügen, den Sie im vorherigen Abschnitt empfangen haben. Beispiel: `https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4?`.
>
>

Weitere Informationen zum Arbeiten mit Azure Storage-Blobs finden Sie unter [REST-API für den Blobdienst](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API).

### <a name="update-the-assetfile"></a>Aktualisieren der AssetFile
Nachdem Sie Ihre Datei nun hochgeladen haben, sollten Sie die FileAsset-Größe und andere Informationen aktualisieren. Beispiel:

    MERGE https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5') HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net

    {  
       "ContentFileSize":"1186540",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }


**HTTP-Antwort**

Im Erfolgsfall wird Folgendes zurückgegeben:

    HTTP/1.1 204 No Content
    ...

## <a name="delete-the-locator-and-accesspolicy"></a>Löschen von AccessPolicy und Locator
**HTTP-Anforderung**

    DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net


**HTTP-Antwort**

Im Erfolgsfall wird Folgendes zurückgegeben:

    HTTP/1.1 204 No Content
    ...

**HTTP-Anforderung**

    DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net

**HTTP-Antwort**

Im Erfolgsfall wird Folgendes zurückgegeben:

    HTTP/1.1 204 No Content
    ...

## <a id="encode"></a>Codieren der Quelldatei in einen Satz von MP4-Dateien mit adaptiver Bitrate

Nach dem Erfassen der Medienobjekte in Media Services können die Medien u. a. codiert, transcodiert/multiplexiert und mit einem Wasserzeichen versehen werden, bevor sie an die Clients übermittelt werden. Diese Aktivitäten werden geplant und für mehrere Hintergrundrolleninstanzen ausgeführt, um hohe Leistung und Verfügbarkeit zu gewährleisten. Diese Aktivitäten werden als Aufträge bezeichnet, und jeder Auftrag besteht aus atomaren Tasks, welche die eigentliche Arbeit für die Medienobjektdatei leisten (weitere Informationen finden Sie in den Beschreibungen zu [Job](https://docs.microsoft.com/rest/api/media/operations/job) und [Task](https://docs.microsoft.com/rest/api/media/operations/task)).

Wie bereits erwähnt, besteht beim Arbeiten mit Azure Media Services eines der häufigsten Szenarios darin, Streaming mit adaptiver Bitrate an Ihre Clients zu übermitteln. Media Services kann eine Reihe von MP4-Dateien mit adaptiver Bitrate dynamisch in eines der folgenden Formate verpacken: HTTP Live Streaming (HLS), Smooth Streaming, MPEG-DASH.

Der folgende Abschnitt veranschaulicht die Erstellung eines Auftrags, der eine Codierungsaufgabe enthält. Die Aufgabe gibt an, dass die Zwischendatei mit **Media Encoder Standard**in einen MP4-Dateisatz mit adaptiver Bitrate transcodiert werden soll. Außerdem wird in diesem Abschnitt erläutert, wie Sie den Fortschritt der Auftragsverarbeitung überwachen. Nachdem Sie den Auftrag abgeschlossen haben, können Sie Locators erstellen, die für den Zugriff auf Ihre Medienobjekte erforderlich sind.

### <a name="get-a-media-processor"></a>Abrufen eines Medienprozessors
Der Medienprozessor in Media Services ist für bestimmte Verarbeitungsaufgaben wie z. B. Codierung, Formatumwandlungen, Verschlüsselung oder Entschlüsselung von Medieninhalten zuständig. Für den in diesem Tutorial beschriebenen Codierungstask verwenden wir Media Encoder Standard.

Durch den folgenden Code wird die Encoder-ID angefordert.

**HTTP-Anforderung**

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net


**HTTP-Antwort**

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 273
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
    x-ms-request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 07:54:09 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "Description":"Media Encoder Standard",
             "Name":"Media Encoder Standard",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }

### <a name="create-a-job"></a>Erstellen eines Auftrags
Je nach Art der Verarbeitung, die Sie durchführen möchten, können einem Auftrag eine oder mehrere Aufgaben zugeordnet sein. Sie haben zwei Möglichkeiten, um über die REST-API Aufträge und die zugehörigen Aufgaben zu erstellen: Aufgaben können inline über die Aufgabennavigationseigenschaft in Auftragsentitäten oder über die OData-Stapelverarbeitung definiert werden. Das Media Services SDK verwendet die Batchverarbeitung. Zur einfacheren Lesbarkeit der Codebeispiele in diesem Artikel werden die Tasks jedoch inline definiert. Weitere Informationen zur Batchverarbeitung finden Sie unter [Open Data Protocol (OData) Batch Processing](https://www.odata.org/documentation/odata-version-3-0/batch-processing/)(in englischer Sprache).

Das folgende Beispiel zeigt, wie Sie einen Auftrag mit einer Aufgabe erstellen und bereitstellen, die für die Codierung eines Videos mit einer bestimmten Auflösung und Qualität konfiguriert wurde. Der folgende Abschnitt dieser Dokumentation enthält eine Liste sämtlicher [Aufgabenvoreinstellungen](https://msdn.microsoft.com/library/mt269960) , die vom Media Encoder-Standardprozessor unterstützt werden.  

**HTTP-Anforderung**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 482

    {  
       "Name":"NewTestJob",
       "InputMediaAssets":[  
          {  
             "__metadata":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')"
             }
          }
       ],
       "Tasks":[  
          {  
             "Configuration":"Adaptive Streaming",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset>
                <outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
          }
       ]
    }

**HTTP-Antwort**

Im Erfolgsfall wird die folgende Antwort zurückgegeben:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 1215
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')
    Server: Microsoft-IIS/8.5
    request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
    x-ms-request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 08:04:35 GMT

    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
             "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Job"
          },
          "Tasks":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Tasks"
             }
          },
          "OutputMediaAssets":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets"
             }
          },
          "InputMediaAssets":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')/InputMediaAssets"
             }
          },
          "Id":"nb:jid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
          "Name":"NewTestJob",
          "Created":"2015-01-19T08:04:34.3287057Z",
          "LastModified":"2015-01-19T08:04:34.3287057Z",
          "EndTime":null,
          "Priority":0,
          "RunningDuration":0,
          "StartTime":null,
          "State":0,
          "TemplateId":null,
          "JobNotificationSubscriptions":{  
             "__metadata":{  
                "type":"Collection(Microsoft.Cloud.Media.Vod.Rest.Data.Models.JobNotificationSubscription)"
             },
             "results":[  

             ]
          }
       }
    }


Bei jeder Auftragsanforderung sind einige wichtige Punkte zu beachten:

* TaskBody-Eigenschaften MÜSSEN literale XML-Elemente verwenden, um die Anzahl der vom Task verwendeten Eingabe- oder Ausgabemedienobjekte zu definieren. Der Artikel „Aufgabe“ enthält die XML-Schemadefinition für den XML-Code.
* In der "TaskBody"-Definition muss jeder interne Wert für `<inputAsset>` und `<outputAsset>` als "JobInputAsset(value)" oder "JobOutputAsset(value)" festgelegt werden.
* Eine Aufgabe kann mehrere Ausgabemedienobjekte besitzen. Ein JobOutputAsset(x)-Objekt kann nur ein Mal als Ausgabe einer Aufgabe in einem Auftrag verwendet werden.
* Sie können JobInputAsset oder JobOutputAsset als Eingabemedienobjekt einer Aufgabe angeben.
* Aufgaben dürfen keine Schleife bilden.
* Der Value-Parameter, den Sie an JobInputAsset oder JobOutputAsset übergeben, stellt den Indexwert für ein Medienobjekt dar. Die tatsächlichen Medienobjekte werden in den Navigationseigenschaften InputMediaAssets und OutputMediaAssets für die Auftragsentitätsdefinition definiert.

> [!NOTE]
> Da Media Services als Grundlage OData v3 verwendet, wird auf die einzelnen Medienobjekte in den Navigationseigenschaftenauflistungen InputMediaAssets und OutputMediaAssets durch das Name-Wert-Paar „__metadata: uri“ verwiesen.
>
>

* InputMediaAssets ist mindestens einem Medienobjekt zugeordnet, das Sie in Media Services erstellt haben. OutputMediaAssets werden vom System erstellt. Sie verweisen nicht auf ein vorhandenes Medienobjekt.
* OutputMediaAssets können mithilfe des assetName-Attributs benannt werden. Wenn dieses Attribut nicht vorhanden ist, wird als Name von „OutputMediaAsset“ der interne Textwert des `<outputAsset>`-Elements mit dem Wert des Auftragsnamens oder der Auftrags-ID (falls die „Name“-Eigenschaft nicht definiert ist) als Suffix verwendet. Wenn Sie für assetName z. B. den Wert „Sample“ festlegen, wird die Name-Eigenschaft von OutputMediaAsset auf „Sample“ festgelegt. Wenn Sie jedoch keinen Wert für assetName festgelegt haben, der Auftragsname jedoch auf NewJob festgelegt wurde, wird OutputMediaAsset der Name JobOutputAsset(Wert)_NewJob zugewiesen.

    Im folgenden Beispiel wird gezeigt, wie das AssetName-Attribut festgelegt wird:

        "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"
* So aktivieren Sie die Aufgabenverkettung:

  * Ein Auftrag muss mindestens zwei Tasks aufweisen.
  * Es muss mindestens eine Aufgabe vorhanden sein, deren Eingabe die Ausgabe einer anderen Aufgabe im Auftrag ist.

Weitere Informationen finden Sie unter [Erstellen eines Codierungsauftrags mit der Media Services REST-API](media-services-rest-encode-asset.md).

### <a name="monitor-processing-progress"></a>Überwachen des Verarbeitungsstatus
Sie können den Auftragsstatus mithilfe der State-Eigenschaft abrufen, wie im folgenden Beispiel veranschaulicht:

**HTTP-Anforderung**

    GET https://wamsbayclus001rest-hs.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/State HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net
    Content-Length: 0


**HTTP-Antwort**

Im Erfolgsfall wird die folgende Antwort zurückgegeben:

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 17
    Content-Type: application/json;odata=verbose;charset=utf-8
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 01324d81-18e2-4493-a3e5-c6186209f0c8
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Sun, 13 May 2012 05:16:53 GMT

    {"d":{"State":2}}


### <a name="cancel-a-job"></a>Abbrechen eines Auftrags
Media Services ermöglicht das Abbrechen aktuell ausgeführter Aufträge über die CancelJob-Funktion. Dieser Aufruf gibt den Fehlercode 400 zurück, wenn Sie versuchen, einen Auftrag abzubrechen, während sein Status „Abgebrochen“, „Wird abgebrochen“, „Fehler“ oder „Abgeschlossen“ lautet.

Das folgende Beispiel zeigt, wie Sie CancelJob aufrufen.

**HTTP-Anforderung**

    GET https://wamsbayclus001rest-hs.net/API/CancelJob?jobid='nb%3ajid%3aUUID%3a71d2dd33-efdf-ec43-8ea1-136a110bd42c' HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.2
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net


Bei Erfolg wird ein Antwortcode 204 ohne Meldungstext zurückgegeben.

> [!NOTE]
> Sie müssen die Auftrags-ID URL-codieren (normalerweise nb:jid:UUID:Wert), wenn Sie sie als Parameter an CancelJob übergeben.
>
>

### <a name="get-the-output-asset"></a>Abrufen des Ausgabemedienobjekts
Der folgende Code zeigt, wie Sie die ID des Ausgabemedienobjekts anfordern.

**HTTP-Anforderung**

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets() HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net


**HTTP-Antwort**

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 445
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
    x-ms-request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 08:28:13 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets",
       "value":[  
          {  
             "Id":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
             "State":0,
             "Created":"2015-01-19T07:52:15.603",
             "LastModified":"2015-01-19T07:52:15.603",
             "AlternateId":null,
             "Name":"Multibitrate MP4s",
             "Options":0,
             "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c",
             "StorageAccountName":"storagetestaccount001"
          }
       ]
    }

## <a id="publish_get_urls"></a>Veröffentlichen des Medienobjekts und Abrufen von URLs für Streaming und progressiven Download mit der REST-API

Um ein Medienobjekt zu streamen oder herunterzuladen, müssen Sie es zunächst durch Erstellen eines Locators "veröffentlichen". Locators ermöglichen den Zugriff auf Dateien im Medienobjekt. Media Services unterstützt zwei Arten von Locatorobjekten: OnDemandOrigin-Locator zum Streamen von Medien (z. B. MPEG DASH, HLS oder Smooth Streaming) und Access Signature-Locator (SAS) zum Herunterladen von Mediendateien. 

Nachdem Sie die Locator erstellt haben, können Sie die URLs erstellen, mit denen Sie die Dateien streamen oder herunterladen möchten.

>[!NOTE]
>Beim Erstellen Ihres AMS-Kontos wird dem Konto ein **Standard**-Streamingendpunkt mit dem Status **Beendet** hinzugefügt. Um mit dem Streamen der Inhalte zu beginnen und die dynamische Paketerstellung und dynamische Verschlüsselung zu nutzen, muss der Streamingendpunkt, von dem Sie Inhalte streamen möchten, den Status **Wird ausgeführt** aufweisen.

Eine URL für Smooth Streaming hat das folgende Format:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Eine Streaming-URL für HLS hat das folgende Format:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Eine Streaming-URL für MPEG DASH hat das folgende Format:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

Eine SAS-URL zum Herunterladen von Dateien hat das folgende Format:

    {blob container name}/{asset name}/{file name}/{SAS signature}

Dieser Abschnitt zeigt, wie Medienobjekte mithilfe der folgenden Tasks „veröffentlicht“ werden.  

* Erstellen der AccessPolicy mit Leseberechtigung
* Erstellen eines SAS-URLs zum Herunterladen von Inhalten
* Erstellen einer Ursprungs-URL für das Streamen von Inhalten

### <a name="creating-the-accesspolicy-with-read-permission"></a>Erstellen der AccessPolicy mit Leseberechtigung
Vor dem Herunterladen oder Streamen von Medieninhalten definieren Sie zunächst eine AccessPolicy mit Leseberechtigungen und erstellen die entsprechende Locator-Entität, die den Typ des Übermittlungsmechanismus angibt, den Sie für Clients aktivieren möchten. Weitere Informationen zu den verfügbaren Eigenschaften finden Sie unter [AccessPolicy-Entitätseigenschaften](https://docs.microsoft.com/rest/api/media/operations/accesspolicy#accesspolicy_properties).

Das folgende Beispiel zeigt, wie die AccessPolicy für Leseberechtigungen eines bestimmten Medienobjekts angegeben wird.

    POST https://wamsbayclus001rest-hs.net/API/AccessPolicies HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net
    Content-Length: 74
    Expect: 100-continue

    {"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}

Im Erfolgsfall wird ein 201 Erfolgscode zurückgegeben, der die erstellte AccessPolicy-Entität beschreibt. Anschließend verwenden Sie die AccessPolicy-ID zusammen mit der ID des Medienobjekts, das die zu übermittelnde Datei (z.B. ein Ausgabemedienobjekt) enthält, um die Locator-Entität zu erstellen.

> [!NOTE]
> Dieser grundlegende Workflow entspricht dem Hochladen einer Datei bei der Erfassung eines Medienobjekts (wie oben in diesem Thema beschrieben). Wie beim Hochladen von Dateien gilt Folgendes: Wenn Sie (oder die Clients) sofortigen Zugriff auf Ihre Dateien benötigen, legen Sie den StartTime-Wert auf fünf Minuten vor der aktuellen Zeit fest. Dies ist erforderlich, weil ggf. eine Uhrzeitabweichung zwischen dem Clientcomputer und Media Services vorliegen kann. Der StartTime-Wert muss das folgende DateTime-Format aufweisen: YYYY-MM-DDTHH:mm:ssZ (Beispiel: „2014-05-23T17:53:50Z“).
>
>

### <a name="creating-a-sas-url-for-downloading-content"></a>Erstellen eines SAS-URLs zum Herunterladen von Inhalten
Der folgende Code zeigt, wie Sie eine URL abrufen, die zum Herunterladen einer zuvor erstellten und hochgeladenen Mediendatei verwendet werden kann. Für die AccessPolicy wurden Leseberechtigungen festgelegt, und der Locator-Pfad verweist auf eine SAS-URL zum Herunterladen von Inhalten.

    POST https://wamsbayclus001rest-hs.net/API/Locators HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net
    Content-Length: 182
    Expect: 100-continue

    {"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c", "StartTime" : "2014-05-17T16:45:53", "Type":1}

Im Erfolgsfall wird die folgende Antwort zurückgegeben:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 1150
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 8cfd8556-3064-416a-97f2-67d9e35f0911
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Mon, 14 May 2012 21:41:32 GMT

    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
             "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
          },
          "AccessPolicy":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')/AccessPolicy"
             }
          },
          "Asset":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Asset"
             }
          },
          "Id":"nb:lid:UUID:8e5a821d-2194-4d00-8884-adf979856874",
          "ExpirationDateTime":"\/Date(1337049393000)\/",
          "Type":1,
          "Path":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c?st=2012-05-14T21%3A36%3A33Z&se=2012-05-15T02%3A36%3A33Z&sr=c&si=8e5a821d-2194-4d00-8884-adf979856874&sig=y75dViDpC5V8WutrXM%2B%2FGpR3uOtqmlISiNlHU1YUBOg%3D",
          "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
          "AssetId":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
          "StartTime":"\/Date(1337031393000)\/"
       }
    }

Die zurückgegebene **Path** -Eigenschaft enthält die SAS-URL.

> [!NOTE]
> Wenn Sie speicherverschlüsselten Inhalt herunterladen, müssen Sie ihn vor dem Rendern manuell entschlüsseln. Alternativ können Sie den MediaProcessor für die Speicherverschlüsselung in einer Verarbeitungsaufgabe verwenden, um verarbeitete Dateien unverschlüsselt an ein OutputAsset auszugeben und dann von diesem Medienobjekt herunterzuladen. Weitere Informationen zur Verarbeitung finden Sie unter „Erstellen und Verschlüsseln von Aufträgen mit der Media Services-REST-API“. Nachdem die SAS URL-Locators erstellt wurden, können sie nicht mehr aktualisiert werden. Beispielsweise kann derselbe Locator nicht mit einem aktualisierten StartTime-Wert wiederverwendet werden. Dies liegt an der Art, wie SAS-URLs erstellt werden. Wenn ein Locator abgelaufen ist und Sie auf ein Medienobjekt zugreifen möchten, um es herunterzuladen, müssen Sie einen neuen Locator mit einem neuen StartTime-Wert erstellen.
>
>

### <a name="download-files"></a>Herunterladen von Dateien
Nachdem Sie AccessPolicy und Locator konfiguriert haben, können Sie Dateien mithilfe der Azure Storage-REST-APIs herunterladen.  

> [!NOTE]
> Sie müssen den Dateinamen der Downloaddatei in den **Path** -Wert des Locators einfügen, den Sie im vorherigen Abschnitt empfangen haben. Zum Beispiel, https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4? . . .

Weitere Informationen zum Arbeiten mit Azure Storage-Blobs finden Sie unter [REST-API für den Blobdienst](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API).

Als Ergebnis des zuvor ausgeführten Codierungsauftrags (Codierung in einen MP4-Satz mit adaptiver Bitrate) verfügen Sie über mehrere MP4-Dateien, die Sie progressiv herunterladen können. Beispiel:    

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

### <a name="creating-a-streaming-url-for-streaming-content"></a>Erstellen einer Streaming-URL für das Streamen von Inhalten
Der folgende Code zeigt, wie Sie einen Streaming-URL-Locator erstellen:

    POST https://wamsbayclus001rest-hs/API/Locators HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs
    Content-Length: 182
    Expect: 100-continue

    {"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3", "StartTime" : "2014-05-17T16:45:53",, "Type":2}

Im Erfolgsfall wird die folgende Antwort zurückgegeben:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 981
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 2eac4158-fc78-4fa2-81ee-c9f582dc385f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Mon, 14 May 2012 21:41:39 GMT

    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
             "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
          },
          "AccessPolicy":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/AccessPolicy"
             }
          },
          "Asset":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/Asset"
             }
          },
          "Id":"nb:lid:UUID:52034bf6-dfae-4d83-aad3-3bd87dcb1a5d",
          "ExpirationDateTime":"\/Date(1337049395000)\/",
          "Type":2,
          "Path":"http://wamsbayclus001rest-hs.net/52034bf6-dfae-4d83-aad3-3bd87dcb1a5d/",
          "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
          "AssetId":"nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3",
          "StartTime":"\/Date(1337031395000)\/"
       }
    }

Um eine Smooth Streaming-Ursprungs-URL in einen Streaming Media Player zu streamen, müssen Sie die Path-Eigenschaft mit dem Namen der Smooth Streaming-Manifestdatei gefolgt von „/ manifest“ anfügen.

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

Zum Streamen von HLS fügen Sie (format=m3u8-aapl) nach „/manifest“ an.

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

Zum Streamen von MPEG DASH fügen Sie (format=mpd-time-csf) nach „/manifest“ an.

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)


## <a id="play"></a>Wiedergeben Ihrer Inhalte
Verwenden Sie zum Streamen von Videos [Azure Media Services Player](https://amsplayer.azurewebsites.net/azuremediaplayer.html).

Fügen Sie zum Testen des progressiven Downloads eine URL in einen Browser ein (z. B. Internet Explorer, Chrome, Safari).

## <a name="next-steps-media-services-learning-paths"></a>Nächste Schritte: Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
