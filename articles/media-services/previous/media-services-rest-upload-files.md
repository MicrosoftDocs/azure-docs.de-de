---
title: Hochladen von Dateien in ein Azure Media Services-Konto mithilfe von REST | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Medieninhalte in Media Services nutzen können, indem Sie Medienobjekte mithilfe von REST erstellen und hochladen.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 49863bec4cbd367b6b309ef5a79e7287cb53ee5b
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93042966"
---
# <a name="upload-files-into-a-media-services-account-using-rest"></a>Hochladen von Dateien in ein Media Services-Konto mit REST

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> * [Portal](media-services-portal-upload-files.md)
> 

In Media Services laden Sie Ihre digitalen Dateien in ein Medienobjekt hoch. Die Entität [Asset](/rest/api/media/operations/asset) kann Videos, Audiodateien, Bilder, Miniaturansichtssammlungen, Texttitel und Untertiteldateien (und die Metadaten zu diesen Dateien) enthalten.  Nachdem die Dateien in das Medienobjekt hochgeladen wurden, werden Ihre Inhalte zur weiteren Verarbeitung und zum Streaming sicher in der Cloud gespeichert. 

In diesem Tutorial erfahren Sie, wie Sie eine Datei hochladen und andere damit verbundene Vorgänge durchführen:

> [!div class="checklist"]
> * Einrichten von Postman für alle Uploadvorgänge
> * Verbinden mit Mediendiensten 
> * Erstellen einer Zugriffsrichtlinie mit Schreibberechtigung
> * Erstellen eines Medienobjekts
> * Erstellen eines SAS-Locators und der Upload-URL
> * Hochladen einer Datei in den Blobspeicher mithilfe der Upload-URL
> * Erstellen von Metadaten in dem Medienobjekt für die hochgeladene Mediendatei

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) erstellen, bevor Sie beginnen.
- [Erstellen Sie ein Azure Media Services-Konto mithilfe des Azure-Portals](media-services-portal-create-account.md).
- Lesen Sie den Artikel [Zugreifen auf die Azure Media Services-API per Azure AD-Authentifizierung – Übersicht](media-services-use-aad-auth-to-access-ams-api.md).
- Weitere Informationen finden Sie im Artikel [Verwenden der Azure AD-Authentifizierung zum Zugreifen auf die Media Services-API mit REST](./media-services-rest-connect-with-aad.md).
- Konfigurieren Sie **Postman** gemäß der Beschreibung in [Konfigurieren von Postman für Media Services-REST-API-Aufrufe](media-rest-apis-with-postman.md).

## <a name="considerations"></a>Überlegungen

Die folgenden Überlegungen gelten für die Verwendung der Media Services-REST-API:
 
* Wenn Sie mithilfe der Media Services-REST-API auf Entitäten zugreifen, müssen Sie bestimmte Headerfelder und Werte in Ihren HTTP-Anforderungen festlegen. Weitere Informationen finden Sie unter [Installation für die Entwicklung mit der Media Services-REST-API](media-services-rest-how-to-use.md). <br/>Die in diesem Tutorial verwendete Postman-Sammlung sorgt dafür, dass alle notwendigen Header festgelegt werden.
* Media Services verwendet beim Erstellen von URLs für den Streaminginhalt den Wert der IAssetFile.Name-Eigenschaft (z. B. http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters). Aus diesem Grund ist die Prozentkodierung nicht zulässig. Der Wert der **Name** -Eigenschaft darf keines der folgenden [für die Prozentcodierung reservierten Zeichen](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters) enthalten: ! * '();:@&=+$,/?%#[]". Darüber hinaus wird für die Dateinamenerweiterung nur ein Punkt (.) unterstützt.
* Die Länge des Namens darf 260 Zeichen nicht überschreiten.
* Bei der Verarbeitung in Media Services werden nur Dateien bis zu einer bestimmten Größe unterstützt. Ausführliche Informationen zur Dateigrößenbeschränkung finden Sie in [diesem Artikel](media-services-quotas-and-limitations.md).

## <a name="set-up-postman"></a>Einrichten von Postman

Schritte zum Einrichten von Postman für dieses Tutorial finden Sie unter [Konfigurieren von Postman](media-rest-apis-with-postman.md).

## <a name="connect-to-media-services"></a>Verbinden mit Mediendiensten

1. Fügen Sie Ihrer Umgebung Verbindungswerte hinzu. 

    Einige Variablen, die Teil der **MediaServices** - [Umgebung](postman-environment.md) sind, müssen manuell festgelegt werden, bevor Sie mit der Ausführung von Vorgängen beginnen können, die in der [Sammlung](postman-collection.md) definiert sind.

    Informationen zum Abrufen von Werten für die ersten fünf Variablen finden Sie unter [Zugriff auf die Azure Media Services-API mit der Azure AD-Authentifizierung](media-services-use-aad-auth-to-access-ams-api.md). 

    ![Screenshot, der das Zahnradsymbol rechts oben und die ersten fünf Variablen auf der Registerkarte „Verwaltungsumgebungen“ ausgewählt zeigt.](./media/media-services-rest-upload-files/postman-import-env.png)
2. Geben Sie den Wert für die Umgebungsvariable **MediaFileName** an.

    Geben Sie den Dateinamen des Mediums an, das Sie hochladen möchten. In diesem Beispiel werden wir „BigBuckBunny.mp4“ hochladen. 
3. Überprüfen Sie die Datei **AzureMediaServices.postman_environment.json**. Sie werden sehen, dass fast alle Vorgänge in der Sammlung ein „Testskript“ ausführen. Die Skripts übernehmen einige von der Antwort zurückgegebene Werte und legen entsprechende Umgebungsvariablen fest.

    Beispielsweise erhält der erste Vorgang ein Zugriffstoken und legt es auf die Umgebungsvariable **AccessToken** fest, die in allen anderen Vorgängen verwendet wird.

    ```    
    "listen": "test",
    "script": {
        "type": "text/javascript",
        "exec": [
            "var json = JSON.parse(responseBody);",
            "postman.setEnvironmentVariable(\"AccessToken\", json.access_token);"
        ]
    }
    ```
4. Klicken Sie auf der linken Seite des Fensters **Postman** auf **1. Get AAD Auth token** -> **Get Azure AD Token for Service Principal** (AAD Authentifizierungstoken abrufen – Azure AD-Token für Dienstprinzipal abrufen).

    Der URL-Teil wird mit der Umgebungsvariablen **AzureADSTSEndpoint** gefüllt (früher im Tutorial legen Sie die Werte von Umgebungsvariablen fest, die die Sammlung unterstützen).

    ![Screenshot, der „1. AAD-Authentifizierungstoken für den Dienstprinzipal auswählen“ im Postman-Fenster und die Schaltfläche „Senden“ ausgewählt zeigt.](./media/media-services-rest-upload-files/postment-get-token.png)

5. Klicken Sie auf **Senden**.

    Sie können die Antwort sehen, die „access_token“ enthält. Das „Testskript“ nimmt diesen Wert und legt die Umgebungsvariable **AccessToken** fest (wie oben beschrieben). Wenn Sie Ihre Umgebungsvariablen untersuchen, werden Sie feststellen, dass diese Variable nun den Wert des Zugriffstoken (Bearertoken) enthält, das in den restlichen Vorgängen verwendet wird. 

    Wenn das Token abläuft, durchlaufen Sie den Schritt zum Abrufen des Azure AD-Tokens für den Dienstprinzipal erneut. 

## <a name="create-an-access-policy-with-write-permission"></a>Erstellen einer Zugriffsrichtlinie mit Schreibberechtigung

### <a name="overview"></a>Übersicht 

>[!NOTE]
>Es gilt ein Grenzwert von 1.000.000 Richtlinien für verschiedene AMS-Richtlinien (z.B. für die Locator-Richtlinie oder für ContentKeyAuthorizationPolicy). Wenn Sie immer die gleichen Tage/Zugriffsberechtigungen verwenden, z.B. Richtlinien für Locator, die für einen längeren Zeitraum vorgesehen sind (Richtlinien ohne Upload), sollten Sie dieselbe Richtlinien-ID verwenden. Weitere Informationen dazu finden Sie in [diesem Artikel](media-services-dotnet-manage-entities.md#limit-access-policies).

Bevor Sie Dateien in den Blobspeicher hochladen, legen Sie die Zugriffsrichtlinienberechtigungen für das Schreiben in ein Medienobjekt fest. Senden Sie dazu eine HTTP POST-Anforderung an die AccessPolicies-Entitätenmenge. Definieren Sie bei der Erstellung einen DurationInMinutes-Wert, da Sie andernfalls eine Antwort mit einer Meldung „500 Interner Serverfehler“ empfangen. Weitere Informationen zu "AccessPolicies" finden Sie unter [AccessPolicy](/rest/api/media/operations/accesspolicy).

### <a name="create-an-access-policy"></a>Erstellen einer Zugriffsrichtlinie

1. Wählen Sie **AccessPolicy** -> **Create AccessPolicy for Upload** (AccessPolicy – AccessPolicy für Upload erstellen) aus.
2. Klicken Sie auf **Senden**.

    ![Screenshot, der „Zugriffsrichtlinie – Zugriffsrichtlinie für Upload erstellen“ im linken Menübereich und die Schaltfläche „Senden“ ausgewählt zeigt.](./media/media-services-rest-upload-files/postman-access-policy.png)

    Das „Testskript“ ruft die AccessPolicy-ID ab und legt die entsprechende Umgebungsvariable fest.

## <a name="create-an-asset"></a>Erstellen eines Medienobjekts

### <a name="overview"></a>Übersicht

Ein [Medienobjekt](/rest/api/media/operations/asset) ist ein Container für mehrere Typen oder Gruppen von Objekten in Media Services. Dazu gehören Videos, Audiodateien, Bilder, Miniaturansichtsammlungen, Texttitel und Untertiteldateien. In der REST-API muss beim Erstellen eines Medienobjekts eine POST-Anforderung an Media Services gesendet werden. Dabei müssen alle Eigenschaftsinformationen zum Medienobjekt im Anforderungstext enthalten sein.

Eine der Eigenschaften, die Sie beim Erstellen eines Medienobjekts hinzufügen können, ist **Options**. Sie können eine der folgenden Verschlüsselungsoptionen angeben: **Keine** (Standardwert, es wird keine Verschlüsselung verwendet), **StorageEncrypted** (für Inhalte, die mit clientseitiger Speicherverschlüsselung vorverschlüsselt wurden), **CommonEncryptionProtected** oder **EnvelopeEncryptionProtected**. Wenn Sie über ein verschlüsseltes Medienobjekt verfügen, müssen Sie eine Übermittlungsrichtlinie konfigurieren. Weitere Informationen finden Sie unter [Konfigurieren von Übermittlungsrichtlinien für Medienobjekte](media-services-rest-configure-asset-delivery-policy.md).

Wenn Ihr Medienobjekt verschlüsselt ist, müssen Sie ein **ContentKey** -Element erstellen und mit Ihrem Medienobjekt verknüpfen, wie im folgenden Artikel beschrieben: [So erstellen Sie einen ContentKey](media-services-rest-create-contentkey.md). Nachdem Sie die Dateien in das Medienobjekt hochgeladen haben, müssen Sie die Verschlüsselungseigenschaften für die **AssetFile** -Entität anhand der Werte aktualisieren, die Sie während der **Asset** -Verschlüsselung erhalten haben. Verwenden Sie dazu die **MERGE** -HTTP-Anforderung. 

In diesem Beispiel erstellen wir ein unverschlüsseltes Medienobjekt. 

### <a name="create-an-asset"></a>Erstellen eines Medienobjekts

1. Wählen Sie **Assets** -> **Create Asset** (Medienobjekte – Medienobjekt erstellen) aus.
2. Klicken Sie auf **Senden**.

    ![Screenshot, der „Medienobjekt – Medienobjekt erstellen“ im Menü „Sammlungen“ und die Schaltfläche „Senden“ ausgewählt zeigt.](./media/media-services-rest-upload-files/postman-create-asset.png)

    Das „Testskript“ ruft die Medienobjekt-ID ab und legt die entsprechende Umgebungsvariable fest.

## <a name="create-a-sas-locator-and-create-the-upload-url"></a>Erstellen eines SAS-Locators und der Upload-URL

### <a name="overview"></a>Übersicht

Nachdem Sie AccessPolicy und Locator konfiguriert haben, können Sie die eigentliche Datei mithilfe der Azure Storage-REST-APIs in einen Azure Blob Storage-Container hochladen. Sie müssen die Dateien als Blockblobs hochladen. Seitenblobs werden von Azure Media Services nicht unterstützt.  

Weitere Informationen zum Arbeiten mit Azure Storage-Blobs finden Sie unter [REST-API für den Blobdienst](/rest/api/storageservices/blob-service-rest-api).

Um die eigentliche Upload-URL zu empfangen, erstellen Sie einen SAS-Locator (siehe unten). Ein Locator definiert die Startzeit und den Typ des Verbindungsendpunkts für Clients, die auf Dateien in einem Medienobjekt zugreifen möchten. Sie können mehrere Locator-Entitäten für ein bestimmtes AccessPolicy-/ Asset-Paar erstellen, um unterschiedliche Clientanforderungen und -voraussetzungen zu verarbeiten. Jeder dieser Locators verwendet den „StartTime“-Wert plus den „DurationInMinutes“-Wert des „AccessPolicy“-Objekts, um zu bestimmen, für welchen Zeitraum eine URL verwendet werden kann. Weitere Informationen finden Sie unter [Locator](/rest/api/media/operations/locator).

Eine SAS-URL weist das folgende Format auf:

`{https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}`

### <a name="considerations"></a>Überlegungen

Folgende Überlegungen sollten berücksichtigt werden:

* Einem bestimmten Medienobjekt können jeweils nicht mehr als fünf eindeutige Locators zugeordnet sein. Weitere Informationen finden Sie unter "Locator".
* Wenn Sie Ihre Dateien sofort hochladen müssen, sollten Sie Ihren StartTime-Wert auf fünf Minuten vor der aktuellen Uhrzeit festlegen. Dies ist erforderlich, weil ggf. eine Uhrzeitabweichung zwischen dem Clientcomputer und Media Services vorliegen kann. Darüber hinaus muss der StartTime-Wert das folgende DateTime-Format aufweisen: YYYY-MM-DDTHH:mm:ssZ (Beispiel: „2014-05-23T17:53:50Z“).    
* Gegebenenfalls tritt eine Verzögerung von 30 bis 40 Sekunden zwischen dem Erstellen eines Locators und seiner Verfügbarkeit auf.

### <a name="create-a-sas-locator"></a>Erstellen eines SAS-Locators

1. Wählen Sie **Locator** -> **Create SAS Locator** (Locator – SAS-Locator erstellen) aus.
2. Klicken Sie auf **Senden**.

    Das Testskript erstellt die „Upload-URL“ auf der Grundlage des von Ihnen angegebenen Mediendateinamens und der SAS-Locator-Informationen und legt die entsprechende Umgebungsvariable fest.

    ![Screenshot, der „Locator – SAS-Locator erstellen“ im Menü „Sammlungen“ und die Schaltfläche „Senden“ ausgewählt zeigt.](./media/media-services-rest-upload-files/postman-create-sas-locator.png)

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>Hochladen einer Datei in den Blobspeicher mithilfe der Upload-URL

### <a name="overview"></a>Übersicht

Nachdem Sie nun über die Upload-URL verfügen, müssen Sie mithilfe der Azure Blob-APIs direkt Code schreiben, um Ihre Datei in den SAS-Container hochzuladen. Weitere Informationen finden Sie in den folgenden Artikeln:

- [Verwenden der Azure Storage-REST-API](../../storage/common/storage-rest-api-auth.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [PUT Blob](/rest/api/storageservices/put-blob)
- [Hochladen von Blobs in Blobspeicher](/previous-versions/azure/storage/storage-use-azcopy#upload-blobs-to-blob-storage)

### <a name="upload-a-file-with-postman"></a>Hochladen einer Datei mit Postman

Als Beispiel laden wir eine kleine MP4-Datei mit Postman hoch. Möglicherweise gibt es eine Begrenzung der Dateigröße beim Hochladen von Binärdateien über Postman.

Die Anforderung zum Hochladen ist nicht Teil der **AzureMedia** -Sammlung. 

Erstellen Sie eine neue Anforderung, und richten Sie sie ein:
1. Drücken Sie **+** , um eine neue Anforderungsregisterkarte zu erstellen.
2. Wählen Sie die **PUT** -Operation aus, und fügen Sie **{{UploadURL}}** in die URL ein.
2. Lassen Sie die Registerkarte **Authorization** (Autorisierung) unverändert (legen Sie nicht das **Bearertoken** fest).
3. Geben Sie auf der Registerkarte **Header** Folgendes an: **Key** : „x-ms-blob-type“ und **Value** : „BlockBlob“.
2. Klicken Sie auf der Registerkarte **Body** (Textkörper) auf **binary** (Binär).
4. Wählen Sie die Datei mit dem Namen aus, den Sie in der Umgebungsvariablen **MediaFileName** angegeben haben.
5. Klicken Sie auf **Senden**.

    ![Screenshot, der die ausgewählte Registerkarte „(Upload-URL)“ zeigt.](./media/media-services-rest-upload-files/postman-upload-file.png)

##  <a name="create-a-metadata-in-the-asset"></a>Erstellen von Metadaten im Medienobjekt

Nachdem die Datei hochgeladen wurde, müssen Sie für die Mediendatei, die Sie in den Blobspeicher Ihres Medienobjekts hochgeladen haben, Metadaten im Medienobjekt erstellen.

1. Wählen Sie **AssetFiles** -> **CreateFileInfos** aus.
2. Klicken Sie auf **Senden**.

    ![Hochladen einer Datei](./media/media-services-rest-upload-files/postman-create-file-info.png)

Die Datei sollte mit festgelegten Metadaten hochgeladen werden.

## <a name="validate"></a>Überprüfen

Um zu überprüfen, ob die Datei erfolgreich hochgeladen wurde, sollten Sie [AssetFile](/rest/api/media/operations/assetfile) abfragen und **ContentFileSize** (oder andere Details) mit dem vergleichen, was Sie im neuen Medienobjekt erwarten. 

Beispielsweise stellt die folgende **GET** -Operation Dateidaten für Ihre Medienobjektdatei (im Falle der Datei „BigBuckBunny.mp4“) bereit. Die Abfrage verwendet die zuvor von Ihnen festgelegten [Umgebungsvariablen](postman-environment.md).

`{{RESTAPIEndpoint}}/Assets('{{LastAssetId}}')/Files`

Die Antwort enthält Größe, Name und andere Informationen.

```console
"Id": "nb:cid:UUID:69e72ede-2886-4f2a-8d36-80a59da09913",
"Name": "BigBuckBunny.mp4",
"ContentFileSize": "3186542",
"ParentAssetId": "nb:cid:UUID:0b8f3b04-72fb-4f38-8e7b-d7dd78888938",
```
  
## <a name="next-steps"></a>Nächste Schritte

Sie können nun Ihre hochgeladenen Medienobjekte codieren. Weitere Informationen finden Sie unter [Codieren von Medienobjekten](media-services-portal-encode.md).

Sie können auch mithilfe von Azure Functions einen Codierungsauftrag auslösen, wenn eine Datei im konfigurierten Container eingeht. Weitere Informationen finden Sie in [diesem Beispiel](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ).
