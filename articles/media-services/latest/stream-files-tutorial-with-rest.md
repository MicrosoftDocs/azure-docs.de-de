---
title: Codieren einer Remotedatei und Streamen mithilfe von Media Services
description: Führen Sie die Schritte in diesem Tutorial aus, um eine Datei anhand einer URL zu codieren und Ihre Inhalte mit Azure Media Services über REST zu streamen.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.custom: mvc
ms.date: 10/12/2020
ms.author: inhenkel
ms.openlocfilehash: 21f7203af267f53d37e26390ea73c896ea9db76e
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98953987"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---rest"></a>Tutorial: Codieren einer Remotedatei anhand einer URL und Streamen des Videos über REST

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services ermöglicht das Codieren Ihrer Mediendateien in Formate, die mit einer Vielzahl von Browsern und Geräten wiedergegeben werden können. So können Sie Ihre Inhalte beispielsweise im HLS-Format von Apple oder im MPEG DASH-Format streamen. Vor dem Streamen sollten Sie Ihre digitale Mediendatei in hoher Qualität codieren. Anleitungen zur Codierung finden Sie unter [Codierungskonzept](encoding-concept.md).

In diesem Tutorial wird gezeigt, wie Sie eine Datei anhand einer URL codieren und das Video mit Azure Media Services über REST streamen können. 

![Video abspielen](./media/stream-files-tutorial-with-api/final-video.png)

Dieses Tutorial veranschaulicht folgende Vorgehensweisen:    

> [!div class="checklist"]
> * Erstellen eines Media Services-Kontos
> * Zugreifen auf die Media Services-API
> * Herunterladen von Postman-Dateien
> * Konfigurieren von Postman
> * Senden von Anforderungen mit Postman
> * Testen der Streaming-URL
> * Bereinigen von Ressourcen

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

- [Erstellen Sie ein Media Services-Konto.](./create-account-howto.md)

    Merken Sie sich die Werte, die Sie für den Namen der Ressourcengruppe und des Media Services-Kontos verwendet haben.

- Installieren Sie den REST-Client von [Postman](https://www.getpostman.com/), um die REST-APIs auszuführen, die in einigen der AMS REST-Tutorials gezeigt werden. 

    Wir verwenden **Postman**, aber jedes REST-Tool wäre geeignet. Weitere Alternativen sind: **Visual Studio Code** mit dem REST-Plug-In oder **Telerik Fiddler**. 

## <a name="download-postman-files"></a>Herunterladen von Postman-Dateien

Klonen Sie ein GitHub-Repository mit der Postman-Sammlung und den Umgebungsdateien.

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-rest-postman.git
 ```

## <a name="access-api"></a>Zugriffs-API

Ausführliche Informationen finden Sie unter [Abrufen von Anmeldeinformationen für den Zugriff auf die Media Services-API](access-api-howto.md).

## <a name="configure-postman"></a>Konfigurieren von Postman

### <a name="configure-the-environment"></a>Konfigurieren der Umgebung 

1. Öffnen Sie die App **Postman**.
2. Wählen Sie auf der rechten Seite des Bildschirms die Option **Manage environment** (Umgebung verwalten) aus.

    ![Verwalten der Umgebung](./media/develop-with-postman/postman-import-env.png)
4. Klicken Sie im Dialogfeld **Manage environment** (Umgebung verwalten) auf **Import**.
2. Navigieren Sie zur Datei `Azure Media Service v3 Environment.postman_environment.json`, die beim Klonen von `https://github.com/Azure-Samples/media-services-v3-rest-postman.git` heruntergeladen wurde.
6. Die Umgebung **Azure Media Service v3 Environment** wird hinzugefügt.

    > [!Note]
    > Aktualisieren Sie die Zugriffsvariablen mit Werten aus dem Abschnitt **Zugreifen auf die Media Services-API**.

7. Doppelklicken Sie auf die ausgewählte Datei, und geben Sie Werte ein, die Sie durch Ausführen der Schritte für den [Zugriff auf die API](#access-api) erhalten.
8. Schließen Sie das Dialogfeld.
9. Wählen Sie in der Dropdownliste die Umgebung **Azure Media Service v3 Environment** aus.

    ![Auswählen der Umgebung](./media/develop-with-postman/choose-env.png)
   
### <a name="configure-the-collection"></a>Konfigurieren der Sammlung

1. Klicken Sie auf **Import**, um die Sammlungsdatei zu importieren.
1. Navigieren Sie zur Datei `Media Services v3.postman_collection.json`, die beim Klonen von `https://github.com/Azure-Samples/media-services-v3-rest-postman.git` heruntergeladen wurde.
3. Wählen Sie die Datei **Media Services v3.postman_collection.json** aus.

    ![Importieren einer Datei](./media/develop-with-postman/postman-import-collection.png)

## <a name="send-requests-using-postman"></a>Senden von Anforderungen mit Postman

In diesem Abschnitt senden wir relevante Anforderungen für die Codierung und Erstellung von URLs, damit Sie Ihre Datei streamen können. Folgende Anforderungen werden gesendet:

1. Abrufen des Azure AD-Tokens für die Dienstprinzipalauthentifizierung
1. Starten eines Streamingendpunkts
2. Erstellen eines Ausgabemedienobjekts
3. Erstellen einer Transformation
4. Erstellen eines Auftrags
5. Erstellen eines Streaminglocators
6. Auflisten der Pfade des Streaminglocators

> [!Note]
>  In diesem Tutorial wird davon ausgegangen, dass Sie alle Ressourcen mit eindeutigen Namen erstellen.  

### <a name="get-azure-ad-token"></a>Abrufen des Azure AD-Tokens 

1. Wählen Sie im linken Fenster der Postman-App „Step 1: Get AAD Auth token“ (Schritt 1: AAD-Authentifizierungstoken abrufen).
2. Klicken Sie anschließend auf „Get Azure AD Token for Service Principal Authentication“ (Azure AD-Token für Dienstprinzipalauthentifizierung abrufen).
3. Klicken Sie auf **Senden**.

    Der folgende Vorgang vom Typ **POST** wird gesendet:

    ```
    https://login.microsoftonline.com/:aadTenantDomain/oauth2/token
    ```

4. Die zurückgegebene Antwort enthält das Token und legt die Umgebungsvariable „AccessToken“ auf den Tokenwert fest. Klicken Sie zum Anzeigen des Codes, der „AccessToken“ festlegt, auf die Registerkarte **Tests**. 

    ![Abrufen des AAD-Tokens](./media/develop-with-postman/postman-get-aad-auth-token.png)


### <a name="start-a-streaming-endpoint"></a>Starten eines Streamingendpunkts

Um das Streamen zu ermöglichen, muss zunächst der [Streamingendpunkt](./streaming-endpoint-concept.md) gestartet werden, von dem aus Sie das Video streamen möchten.

> [!NOTE]
> Es werden nur ausgeführte Streamingendpunkte abgerechnet.

1. Wählen Sie im linken Fenster der Postman-App „Streaming and Live“ (Streaming und live) aus.
2. Wählen Sie anschließend „Start StreamingEndpoint“ (Streamingendpunkt starten) aus.
3. Klicken Sie auf **Senden**.

    * Der folgende Vorgang vom Typ **POST** wird gesendet:

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaservices/:accountName/streamingEndpoints/:streamingEndpointName/start?api-version={{api-version}}
        ```
    * War die Anforderung erfolgreich, wird `Status: 202 Accepted` zurückgegeben.

        Dieser Status bedeutet, dass die Anforderung akzeptiert, aber noch nicht verarbeitet wurde. Der Status des Vorgangs kann auf der Grundlage des Werts im `Azure-AsyncOperation`-Antwortheader abgefragt werden.

        Der folgende GET-Vorgang gibt beispielsweise den Status Ihres Vorgangs zurück:
        
        `https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/<resourceGroupName>/providers/Microsoft.Media/mediaservices/<accountName>/streamingendpointoperations/1be71957-4edc-4f3c-a29d-5c2777136a2e?api-version=2018-07-01`

        Der Artikel [Nachverfolgen asynchroner Vorgänge in Azure](../../azure-resource-manager/management/async-operations.md) enthält ausführliche Informationen zur Nachverfolgung des Status asynchroner Azure-Vorgänge anhand von Werten aus der zurückgegebenen Antwort.

### <a name="create-an-output-asset"></a>Erstellen eines Ausgabemedienobjekts

Das [Ausgabeobjekt](/rest/api/media/assets) speichert das Ergebnis Ihres Codierungsauftrags. 

1. Wählen Sie im linken Fenster der Postman-App „Assets“ (Medienobjekte) aus.
2. Klicken Sie anschließend auf „Create or update an Asset“ (Medienobjekt erstellen oder aktualisieren).
3. Klicken Sie auf **Senden**.

    * Der folgende Vorgang vom Typ **PUT** wird gesendet:

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/assets/:assetName?api-version={{api-version}}
        ```
    * Der Vorgang hat den folgenden Text:

        ```json
        {
        "properties": {
            "description": "My Asset",
            "alternateId" : "some GUID",
            "storageAccountName": "<replace from environment file>",
            "container": "<supply any valid container name of your choosing>"
         }
        }
        ```

> [!NOTE]
> Ersetzen Sie unbedingt die Namen für Speicherkonto und Container durch die Namen aus der Umgebungsdatei, oder geben Sie eigene Namen an.
>
> Stellen Sie beim Ausführen der im weiteren Verlauf dieses Artikels beschriebenen Schritte sicher, dass Sie gültige Parameter im Anforderungstext angeben.

### <a name="create-a-transform"></a>Erstellen einer Transformation

Bei der Codierung oder Verarbeitung von Inhalten in Media Services besteht ein allgemeines Muster darin, die Codierungseinstellungen als eine Anleitung einzurichten. Anschließend übermitteln Sie einen **Auftrag**, um diese Anleitung auf ein Video anzuwenden. Durch die Übermittlung von neuen Aufträgen für jedes neue Video wenden Sie diese Anleitung auf alle Videos in Ihrer Bibliothek an. Eine Anleitung wird in Media Services als eine **Transformation** aufgerufen. Weitere Informationen finden Sie unter [Transformationen und Aufträge](./transforms-jobs-concept.md). Das in diesem Tutorial beschriebene Beispiel definiert eine Anleitung, die das Video codiert, damit es auf eine Vielzahl von iOS- und Android-Geräte gestreamt werden kann. 

Beim Erstellen einer neuen [Transformations](/rest/api/media/transforms)instanz müssen Sie angeben, was als Ausgabe generiert werden soll. Der erforderliche Parameter ist ein Objekt vom Typ **TransformOutput**. Jedes **TransformOutput**-Objekt enthält eine **Voreinstellung**. Die **Voreinstellung** beschreibt die schrittweisen Anweisungen von Video- und/oder Audioverarbeitungsvorgängen, die verwendet werden sollen, um das gewünschte **TransformOutput**-Objekt zu generieren. Das in diesem Artikel beschriebene Beispiel verwendet eine integrierte Voreinstellung namens **AdaptiveStreaming**. Die Voreinstellung codiert das Eingabevideo in eine automatisch generierte Bitratenkette (Paare aus Bitrate und Auflösung) auf Basis der Eingabeauflösung und -bitrate und generiert ISO MP4-Dateien mit H.264-Video und AAC-Audio entsprechend jedem Paar aus Bitrate und Auflösung. Weitere Informationen zu dieser Voreinstellung finden Sie unter [Automatische Erstellen einer Bitratenkette](autogen-bitrate-ladder.md).

Sie können ein integriertes EncoderNamedPreset-Objekt oder benutzerdefinierte Voreinstellungen verwenden. 

> [!Note]
> Beim Erstellen einer [Transformation](/rest/api/media/transforms) sollten Sie zunächst mit der Methode **Get** überprüfen, ob bereits eine Transformation vorhanden ist. In diesem Tutorial wird davon ausgegangen, dass Sie die Transformation mit einem eindeutigen Namen erstellen.

1. Klicken Sie im linken Fenster der Postman-App auf „Encoding and Analysis“ (Codierung und Analyse).
2. Klicken Sie anschließend auf „Create Transform“ (Transformation erstellen).
3. Klicken Sie auf **Senden**.

    * Der folgende Vorgang vom Typ **PUT** wird gesendet:

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName?api-version={{api-version}}
        ```
    * Der Vorgang hat den folgenden Text:

        ```json
        {
            "properties": {
                "description": "Standard Transform using an Adaptive Streaming encoding preset from the library of built-in Standard Encoder presets",
                "outputs": [
                    {
                    "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                    "preset": {
                        "@odata.type": "#Microsoft.Media.BuiltInStandardEncoderPreset",
                        "presetName": "AdaptiveStreaming"
                    }
                    }
                ]
            }
        }
        ```

### <a name="create-a-job"></a>Erstellen eines Auftrags

Ein [Auftrag](/rest/api/media/jobs) ist die eigentliche Anforderung an Azure Media Services, um die erstellte **Transformation** auf ein bestimmtes Eingabevideo oder auf einen Audioinhalt anzuwenden. Der **Auftrag** gibt Informationen wie den Speicherort des Eingabevideos und den Speicherort für die Ausgabe an.

In diesem Beispiel basiert die Eingabe des Auftrags auf einer HTTPS-URL (https:\//nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/).

1. Klicken Sie im linken Fenster der Postman-App auf „Encoding and Analysis“ (Codierung und Analyse).
2. Klicken Sie anschließend auf „Create or Update Job“ (Auftrag erstellen oder aktualisieren).
3. Klicken Sie auf **Senden**.

    * Der folgende Vorgang vom Typ **PUT** wird gesendet:

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
        ```
    * Der Vorgang hat den folgenden Text:

        ```json
        {
        "properties": {
            "input": {
            "@odata.type": "#Microsoft.Media.JobInputHttp",
            "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
            "files": [
                    "Ignite-short.mp4"
                ]
            },
            "outputs": [
            {
                "@odata.type": "#Microsoft.Media.JobOutputAsset",
                "assetName": "testAsset1"
            }
            ]
        }
        }
        ```

Der Abschluss des Auftrags nimmt einige Zeit in Anspruch. Wenn er erfolgt ist, möchten Sie benachrichtigt werden. Zum Anzeigen des Auftragsfortschritts empfehlen wir Event Grid. Event Grid ist für hohe Verfügbarkeit, konsistente Leistung und dynamische Skalierung konzipiert. Mit Event Grid können Ihre Apps Ereignisse aus praktisch allen Azure-Diensten sowie aus benutzerdefinierten Quellen überwachen und darauf reagieren. Eine einfache, HTTP-basierte, reaktive Ereignisverarbeitung hilft Ihnen mit intelligentem Filtern und Routing von Ereignissen dabei, effiziente Lösungen zu erstellen.  Weitere Informationen finden Sie unter [Routing von Ereignissen an einen benutzerdefinierten Webendpunkt](job-state-events-cli-how-to.md).

Der **Auftrag** durchläuft in der Regel die folgenden Zustände: **Geplant**, **In Warteschlange**, **Wird verarbeitet**, **Abgeschlossen** (Endzustand). Wenn für den Auftrag ein Fehler aufgetreten ist, erhalten Sie den Zustand **Fehler**. Wenn der Auftrag aktuell abgebrochen wird, erhalten Sie **Abbrechen** und **Abgebrochen**, wenn dies geschehen ist.

#### <a name="job-error-codes"></a>Auftragsfehlercodes

Weitere Informationen finden Sie unter [Fehlercodes](/rest/api/media/jobs/get#joberrorcode).

### <a name="create-a-streaming-locator"></a>Erstellen Sie eines Streaminglocators

Nach Abschluss des Codierungsauftrags muss das Video im **Ausgabemedienobjekt** für die Wiedergabe durch Clients verfügbar gemacht werden. Sie können dies in zwei Schritten bewerkstelligen: Erstellen Sie zunächst einen [StreamingLocator](/rest/api/media/streaminglocators) und dann die Streaming-URLs, die Clients verwenden können. 

Der Vorgang zum Erstellen eines Streaminglocators wird als „Veröffentlichen“ bezeichnet. Standardmäßig ist der Streaminglocator sofort nach dem Vornehmen der API-Aufrufe gültig und bleibt es auch, bis er gelöscht wird (es sei denn, Sie konfigurieren die optionalen Start- und Endzeiten). 

Beim Erstellen eines [StreamingLocator](/rest/api/media/streaminglocators) müssen Sie den gewünschten **StreamingPolicyName** angeben. In diesem Beispiel streamen Sie unverschlüsselte Inhalte. Daher wird die vordefinierte Richtlinie für unverschlüsseltes Streaming (Predefined_ClearStreamingOnly) verwendet.

> [!IMPORTANT]
> Wenn Sie eine benutzerdefinierte [Streamingrichtlinie](/rest/api/media/streamingpolicies) verwenden, sollten Sie eine begrenzte Sammlung solcher Richtlinien für Ihr Media Services-Konto erstellen und diese für Ihre StreamingLocators wiederverwenden, wenn dieselben Verschlüsselungsoptionen und Protokolle benötigt werden. 

Ihr Media Services-Konto weist ein Kontingent für die Anzahl von **Streamingrichtlinieneinträgen** auf. Sie sollten nicht für jeden Streaminglocator eine neue **Streamingrichtlinie** erstellen.

1. Wählen Sie im linken Fenster der Postman-App „Streaming Policies and Locators“ (Streamingrichtlinien und Locators) aus.
2. Wählen Sie dann schließend „Create a Streaming Locator (clear)“ (Streaminglocator erstellen (löschen)) aus.
3. Klicken Sie auf **Senden**.

    * Der folgende Vorgang vom Typ **PUT** wird gesendet:

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/streamingLocators/:streamingLocatorName?api-version={{api-version}}
        ```
    * Der Vorgang hat den folgenden Text:

        ```json
        {
          "properties": {
            "streamingPolicyName": "Predefined_ClearStreamingOnly",
            "assetName": "testAsset1",
            "contentKeys": [],
            "filters": []
         }
        }
        ```

### <a name="list-paths-and-build-streaming-urls"></a>Auflisten der Pfade und Erstellen der Streaming-URLs

#### <a name="list-paths"></a>Auflisten der Pfade

Nachdem der [Streaminglocator](/rest/api/media/streaminglocators) erstellt wurde, können Sie die Streaming-URLs abrufen.

1. Klicken Sie im linken Fenster der Postman-App auf „Streaming Policies“ (Streamingrichtlinien).
2. Klicken Sie anschließend auf „List Paths“ (Pfade auflisten).
3. Klicken Sie auf **Senden**.

    * Der folgende Vorgang vom Typ **POST** wird gesendet:

        ```
        https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/streamingLocators/:streamingLocatorName/listPaths?api-version={{api-version}}
        ```
        
    * Der Vorgang weist keinen Text auf:
        
4. Notieren Sie sich einen der Pfade, den Sie für das Streaming verwenden möchten. Sie benötigen ihn im nächsten Abschnitt. In vorliegenden Fall wurden folgende Pfade zurückgegeben:
    
    ```
    "streamingPaths": [
        {
            "streamingProtocol": "Hls",
            "encryptionScheme": "NoEncryption",
            "paths": [
                "/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest(format=m3u8-aapl)"
            ]
        },
        {
            "streamingProtocol": "Dash",
            "encryptionScheme": "NoEncryption",
            "paths": [
                "/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest(format=mpd-time-csf)"
            ]
        },
        {
            "streamingProtocol": "SmoothStreaming",
            "encryptionScheme": "NoEncryption",
            "paths": [
                "/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest"
            ]
        }
    ]
    ```

#### <a name="build-the-streaming-urls"></a>Erstellen der Streaming-URLs

In diesem Abschnitt wird eine HLS-Streaming-URL erstellt. URLs setzen sich aus folgenden Werten zusammen:

1. Das Protokoll für die Datenübertragung (in diesem Fall: „https“).

    > [!NOTE]
    > Wenn ein Player auf einer HTTPS-Website gehostet wird, stellen Sie sicher, die URL mit „https“ zu aktualisieren.

2. Der StreamingEndpoint-Hostname (in diesem Fall: „amsaccount-usw22.streaming.media.azure.net“).

    Zum Abrufen des Hostnamens können Sie den folgenden GET-Vorgang verwenden:
    
    ```
    https://management.azure.com/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaservices/:accountName/streamingEndpoints/default?api-version={{api-version}}
    ```
    Legen Sie die Parameter `resourceGroupName` und `accountName` unbedingt so fest, dass Sie den Werten in der Umgebungsdatei entsprechen. 
    
3. Ein Pfad, den Sie im vorherigen Abschnitt (Auflisten der Pfade) abgerufen haben.  

Dadurch ergibt sich die folgende HLS-URL:

```
https://amsaccount-usw22.streaming.media.azure.net/cdb80234-1d94-42a9-b056-0eefa78e5c63/Ignite-short.ism/manifest(format=m3u8-aapl)
```

## <a name="test-the-streaming-url"></a>Testen der Streaming-URL


> [!NOTE]
> Stellen Sie sicher, dass der **Streamingendpunkt**, von dem aus Sie die Inhalte streamen möchten, ausgeführt wird.

Um den Stream zu testen, wird in diesem Artikel Azure Media Player verwendet. 

1. Öffnen Sie einen Webbrowser, und navigieren Sie zu [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/).
2. Fügen Sie im Feld **URL:** die URL ein, die Sie erstellt haben. 
3. Drücken Sie **Player aktualisieren**.

Azure Media Player kann zum Testen verwendet werden, sollte jedoch nicht in einer Produktionsumgebung zum Einsatz kommen. 

## <a name="clean-up-resources-in-your-media-services-account"></a>Bereinigen von Ressourcen in Ihrem Media Services-Konto

Im Allgemeinen sollten Sie alles mit Ausnahme der Objekte bereinigen, die Sie wiederverwenden möchten. (In der Regel werden Sie **Transformationen** wiederverwenden und **Streaminglocators** usw. dauerhaft speichern). Wenn Sie Ihr Konto nach dem Experimentieren bereinigen möchten, sollten Sie die Ressourcen löschen, bei denen Sie nicht beabsichtigen, sie wiederzuverwenden.  

Wählen Sie zum Löschen einer Ressource den Löschvorgang unter der zu löschenden Ressource aus.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie keine Ressourcen in Ihrer Ressourcengruppe mehr benötigen, einschließlich der Media Services und Speicherkonten, die Sie für dieses Tutorial erstellt haben, löschen Sie die zuvor erstellte Ressourcengruppe.  

Führen Sie den folgenden CLI-Befehl aus:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="ask-questions-give-feedback-get-updates"></a>Fragen stellen, Feedback geben, Updates abrufen

Im Artikel [Azure Media Services-Community](media-services-community.md) finden Sie verschiedene Möglichkeiten, Fragen zu stellen, Feedback zu geben und Updates zu Media Services zu bekommen.

## <a name="next-steps"></a>Nächste Schritte

Da Sie nun wissen, wie Sie Ihr Video hochladen, codieren und streamen können, lesen Sie den folgenden Artikel: 

> [!div class="nextstepaction"]
> [Analysieren von Videos](analyze-videos-tutorial-with-api.md)
