---
title: Codieren eines Azure-Medienobjekts mit Media Encoder Standard | Microsoft Docs
description: Erfahren Sie, wie Sie Medieninhalte in Media Services mithilfe von Media Encoder Standard codieren können. Die Codebeispiele basieren auf der REST-API.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 2a7273c6-8a22-4f82-9bfe-4509ff32d4a4
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 8db9e60e9ce99eaf2621821825620966b8b8b4ae
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60640077"
---
# <a name="how-to-encode-an-asset-by-using-media-encoder-standard"></a>Codieren eines Medienobjekts mit Media Encoder Standard
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
> * [REST](media-services-rest-encode-asset.md)
> * [Portal](media-services-portal-encode.md)
>
>

## <a name="overview"></a>Übersicht

Um digitale Videos über das Internet zu übermitteln, müssen Sie die Medien komprimieren. Digitale Videodateien sind umfangreich und möglicherweise zu groß, um sie über das Internet zu übermitteln oder auf den Geräten Ihrer Kunden ordnungsgemäß wiederzugeben. Bei der Codierung werden Video- und Audiodaten komprimiert, damit Ihre Kunden Ihre Medien anzeigen können.

Die Codierung ist einer der häufigsten Verarbeitungsvorgänge in Azure Media Services. Sie erstellen Codierungsaufträge, um Mediendateien von einer Codierung in eine andere zu konvertieren. Zum Codieren können Sie den in Media Services integrierten Encoder (Media Encoder Standard) verwenden. Sie können auch einen Encoder von einem Media Services-Partner verwenden. Drittanbieter-Encoder sind über den Azure Marketplace erhältlich. Sie können Details zu Codierungsaufgaben angeben, indem Sie für Ihren Encoder vordefinierte Zeichenfolgen oder vordefinierte Konfigurationsdateien verwenden. Welche Arten von Voreinstellungen verfügbar sind, erfahren Sie unter [Aufgabenvoreinstellungen für Media Encoder Standard](https://msdn.microsoft.com/library/mt269960).

Je nach Art der Verarbeitung, die Sie durchführen möchten, können einem Auftrag eine oder mehrere Aufgaben zugeordnet sein. Sie haben zwei Möglichkeiten, um über die REST-API Aufträge und die zugehörigen Aufgaben zu erstellen:

* Aufgaben können inline über Navigationseigenschaft „Tasks“ in Auftragsentitäten definiert werden.
* Verwenden Sie OData-Batchverarbeitung.

Es wird empfohlen, Ihre Quelldateien immer in einen Satz von MP4-Dateien mit adaptiver Bitrate zu codieren und anschließend mithilfe der [dynamischen Paketerstellung](media-services-dynamic-packaging-overview.md) in das gewünschte Format zu konvertieren.

Wenn Ihr Ausgabemedienobjekt speicherverschlüsselt ist, müssen Sie die Übermittlungsrichtlinien für Medienobjekte konfigurieren. Weitere Informationen finden Sie unter [Konfigurieren von Übermittlungsrichtlinien für Medienobjekte](media-services-rest-configure-asset-delivery-policy.md).

## <a name="considerations"></a>Überlegungen

Wenn Sie in Media Services auf Entitäten zugreifen, müssen Sie bestimmte Headerfelder und Werte in Ihren HTTP-Anforderungen festlegen. Weitere Informationen finden Sie unter [Installation für die Entwicklung mit der Media Services-REST-API](media-services-rest-how-to-use.md).

Bevor Sie mit dem Verweisen auf Medienprozessoren beginnen, stellen Sie sicher, dass Sie über die richtige Medienprozessor-ID verfügen. Weitere Informationen finden Sie unter [Abrufen von Medienprozessoren](media-services-rest-get-media-processor.md).

## <a name="connect-to-media-services"></a>Verbinden mit Mediendiensten

Informationen zum Herstellen einer Verbindung mit der AMS-API finden Sie unter [Zugreifen auf die Azure Media Services-API per Azure AD-Authentifizierung](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="create-a-job-with-a-single-encoding-task"></a>Erstellen eines Auftrags mit einer einzelnen Codierungsaufgabe

> [!NOTE]
> Beim Verwenden der Media Services REST-API gelten die folgenden Überlegungen:
>
> Wenn Sie in Media Services auf Entitäten zugreifen, müssen Sie bestimmte Headerfelder und Werte in Ihren HTTP-Anforderungen festlegen. Weitere Informationen finden Sie unter [Installation für die Entwicklung mit der Media Services-REST-API](media-services-rest-how-to-use.md).
>
> Wenn Sie JSON verwenden und angeben, dass das Schlüsselwort **__metadata** in der Anforderung verwendet werden soll (z.B. für Verweise auf ein verknüpftes Objekt), müssen Sie den **Accept**-Header auf das [ausführliche JSON-Format](https://www.odata.org/documentation/odata-version-3-0/json-verbose-format/) festlegen: Accept: application/json;odata=verbose.
>
>

Das folgende Beispiel zeigt, wie Sie einen Auftrag mit einer Aufgabe erstellen und bereitstellen, die für die Codierung eines Videos mit einer bestimmten Auflösung und Qualität konfiguriert wurde. Bei der Codierung mit Media Encoder Standard können Sie die [hier](https://msdn.microsoft.com/library/mt269960)angegebenen Voreinstellungen für die Aufgabenkonfiguration verwenden.

Anforderung:

    POST https://media.windows.net/API/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
        Authorization: Bearer <ENCODED JWT TOKEN> 
        x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
        Host: media.windows.net

    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "Adaptive Streaming", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",  "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}

Antwort:

    HTTP/1.1 201 Created

    . . .

### <a name="set-the-output-assets-name"></a>Festlegen des Namens des Ausgabemedienobjekts
Im folgenden Beispiel wird gezeigt, wie das AssetName-Attribut festgelegt wird:

    { "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"}

## <a name="considerations"></a>Überlegungen
* TaskBody-Eigenschaften müssen literale XML-Elemente verwenden, um die Anzahl der vom Task verwendeten Eingabe- oder Ausgabemedienobjekte zu definieren. Der Artikel „Aufgabe“ enthält die XML-Schemadefinition für den XML-Code.
* In der "TaskBody"-Definition muss jeder interne Wert für `<inputAsset>` und `<outputAsset>` als "JobInputAsset(value)" oder "JobOutputAsset(value)" festgelegt werden.
* Eine Aufgabe kann mehrere Ausgabemedienobjekte besitzen. Ein JobOutputAsset(x)-Objekt kann nur ein Mal als Ausgabe einer Aufgabe in einem Auftrag verwendet werden.
* Sie können JobInputAsset oder JobOutputAsset als Eingabemedienobjekt einer Aufgabe angeben.
* Aufgaben dürfen keine Schleife bilden.
* Der Value-Parameter, den Sie an JobInputAsset oder JobOutputAsset übergeben, stellt den Indexwert für ein Medienobjekt dar. Die tatsächlichen Medienobjekte werden in den Navigationseigenschaften „InputMediaAssets“ und „OutputMediaAssets“ für die Auftragsentitätsdefinition definiert.
* Da Media Services als Grundlage OData v3 verwendet, wird auf die einzelnen Medienobjekte in den InputMediaAssets- und OutputMediaAssets-Navigationseigenschaftensammlungen durch das Name/Wert-Paar „__metadata: uri“ verwiesen.
* InputMediaAssets ist mindestens einem Medienobjekt zugeordnet, das Sie in Media Services erstellt haben. OutputMediaAssets werden vom System erstellt. Sie verweisen nicht auf ein vorhandenes Medienobjekt.
* OutputMediaAssets können mithilfe des assetName-Attributs benannt werden. Wenn dieses Attribut nicht vorhanden ist, wird als Name von „OutputMediaAsset“ der interne Textwert des `<outputAsset>`-Elements mit dem Wert des Auftragsnamens oder der Auftrags-ID (falls die „Name“-Eigenschaft nicht definiert ist) als Suffix verwendet. Wenn Sie für assetName z.B. den Wert „Sample“ festlegen, wird die Name-Eigenschaft von OutputMediaAsset auf „Sample“ festgelegt. Wenn Sie jedoch keinen Wert für assetName festgelegt haben, der Auftragsname jedoch auf „NewJob“ festgelegt wurde, wird OutputMediaAsset der Name „JobOutputAsset(Wert)_NewJob“ zugewiesen.

## <a name="create-a-job-with-chained-tasks"></a>Erstellen eines Auftrags mit verketteten Aufgaben
In zahlreichen Anwendungsszenarien möchten Entwickler eine Reihe von Verarbeitungsaufgaben erstellen. In Media Services können Sie eine Reihe verketteter Aufgaben erstellen. In jeder Aufgabe können verschiedene Verarbeitungsschritte ausgeführt und unterschiedliche Medienprozessoren verwendet werden. Innerhalb der verketteten Aufgaben kann ein Medienobjekt von einer Aufgabe an eine andere übergeben werden, sodass eine lineare Aufgabensequenz auf das Medienobjekt angewendet wird. In einem Auftrag ausgeführte Aufgaben müssen jedoch nicht sequenziell sein. Beim Erstellen einer verketteten Aufgabe werden die verketteten **ITask**-Objekte in einem einzelnen **IJob-Objekt** erstellt.

> [!NOTE]
> Die Aufgabenanzahl ist derzeit auf 30 Aufgaben pro Auftrag begrenzt. Wenn Sie mehr als 30 Aufgaben verketten müssen, erstellen Sie mehrere Aufträge, um die Aufgaben zu verteilen.
>
>

    POST https://media.windows.net/api/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

    {  
       "Name":"NewTestJob",
       "InputMediaAssets":[  
          {  
             "__metadata":{  
                "uri":"https://testrest.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A910ffdc1-2e25-4b17-8a42-61ffd4b8914c')"
             }
          }
       ],
       "Tasks":[  
          {  
             "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
          },
          {  
             "Configuration":"H264 Smooth Streaming 720p",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-16\"?><taskBody><inputAsset>JobOutputAsset(0)</inputAsset><outputAsset>JobOutputAsset(1)</outputAsset></taskBody>"
          }
       ]
    }


### <a name="considerations"></a>Überlegungen
So aktivieren Sie die Aufgabenverkettung:

* Ein Auftrag muss mindestens zwei Aufgaben aufweisen.
* Es muss mindestens eine Aufgabe vorhanden sein, deren Eingabe die Ausgabe einer anderen Aufgabe im Auftrag ist.

## <a name="use-odata-batch-processing"></a>Verwenden von OData-Batchverarbeitung
Das folgende Beispiel zeigt die Verwendung von OData-Batchverarbeitung zum Erstellen eines Auftrags und von Aufgaben. Weitere Informationen zur Batchverarbeitung finden Sie unter [Open Data Protocol (OData) Batch Processing](https://www.odata.org/documentation/odata-version-3-0/batch-processing/)(in englischer Sprache).

    POST https://media.windows.net/api/$batch HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Content-Type: multipart/mixed; boundary=batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
    Accept: multipart/mixed
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
    Host: media.windows.net


    --batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
    Content-Type: multipart/mixed; boundary=changeset_122fb0a4-cd80-4958-820f-346309967e4d

    --changeset_122fb0a4-cd80-4958-820f-346309967e4d
    Content-Type: application/http
    Content-Transfer-Encoding: binary

    POST https://media.windows.net/api/Jobs HTTP/1.1
    Content-ID: 1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

    {"Name" : "NewTestJob", "InputMediaAssets@odata.bind":["https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A2a22445d-1500-80c6-4b34-f1e5190d33c6')"]}

    --changeset_122fb0a4-cd80-4958-820f-346309967e4d
    Content-Type: application/http
    Content-Transfer-Encoding: binary

    POST https://media.windows.net/api/$1/Tasks HTTP/1.1
    Content-ID: 2
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

    {  
       "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
       "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
       "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"Custom output name\">JobOutputAsset(0)</outputAsset></taskBody>"
    }

    --changeset_122fb0a4-cd80-4958-820f-346309967e4d--
    --batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e--



## <a name="create-a-job-by-using-a-jobtemplate"></a>Erstellen eines Auftrags mithilfe einer JobTemplate
Bei der Verarbeitung mehrerer Medienobjekte unter Verwendung eines gemeinsamen Satzes von Aufgaben verwenden Sie eine JobTemplate zum Festlegen der Standardaufgabenvorgaben oder der Aufgabenreihenfolge.

Das folgende Beispiel zeigt, wie eine JobTemplate mit einer inline definierten TaskTemplate erstellt wird. Die TaskTemplate verwendet Media Encoder Standard als MediaProcessor zum Codieren der Medienobjektdatei. Es können jedoch auch andere MediaProcessors verwendet werden.

    POST https://media.windows.net/API/JobTemplates HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net


    {"Name" : "NewJobTemplate25", "JobTemplateBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><jobTemplate><taskBody taskTemplateId=\"nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789\"><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody></jobTemplate>", "TaskTemplates" : [{"Id" : "nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789", "Configuration" : "H264 Smooth Streaming 720p", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56", "Name" : "SampleTaskTemplate2", "NumberofInputAssets" : 1, "NumberofOutputAssets" : 1}] }


> [!NOTE]
> Im Gegensatz zu anderen Media Services-Entitäten müssen Sie eine neue GUID für jede TaskTemplate definieren und diese in der TaskTemplateId- und ID-Eigenschaft im Anforderungstext platzieren. Das Inhaltsidentifikationsschema muss das unter "Identifizieren von Azure Media Services-Entitäten" beschriebene Schema befolgen. Darüber hinaus können JobTemplates nicht aktualisiert werden. Stattdessen müssen Sie eine neue Vorlage mit Ihren aktualisierten Änderungen erstellen.
>
>

Im Erfolgsfall wird die folgende Antwort zurückgegeben:

    HTTP/1.1 201 Created

    . . .


Das folgende Beispiel zeigt, wie Sie einen Auftrag erstellen, der auf eine JobTemplate-ID verweist:

    POST https://media.windows.net/API/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net


    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A3f1fe4a2-68f5-4190-9557-cd45beccef92')"}}], "TemplateId" : "nb:jtid:UUID:15e6e5e6-ac85-084e-9dc2-db3645fbf0aa"}


Im Erfolgsfall wird die folgende Antwort zurückgegeben:

    HTTP/1.1 201 Created

    . . .


## <a name="advanced-encoding-features-to-explore"></a>Erweiterte Codierungsfeatures
* [Generieren von Miniaturansichten](media-services-dotnet-generate-thumbnail-with-mes.md)
* [Generieren von Miniaturansichten während der Codierung](media-services-dotnet-generate-thumbnail-with-mes.md#example-of-generating-a-thumbnail-while-encoding)
* [Zuschneiden von Videos während der Codierung](media-services-crop-video.md)
* [Anpassen von Codierungsvoreinstellungen](media-services-custom-mes-presets-with-dotnet.md)
* [Überlagern eines Videos mit einem Bild oder Versehen eines Videos mit einem Wasserzeichen](media-services-advanced-encoding-with-mes.md#overlay)

## <a name="media-services-learning-paths"></a>Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Nächste Schritte
Sie sind nun in der Lage, einen Auftrag zur Codierung von Medienobjekten zu erstellen und können mit dem Thema [Prüfen des Auftragsfortschritts mit Media Services](media-services-rest-check-job-progress.md) fortfahren.

## <a name="see-also"></a>Weitere Informationen
[Abrufen von Medienprozessoren](media-services-rest-get-media-processor.md)
