---
title: 'Verwenden der Video Indexer-API zum Anpassen eines Personenmodells: Azure'
titlesuffix: Azure Media Services
description: In diesem Artikel wird gezeigt, wie ein Personenmodell mit der Video Indexer-API angepasst werden kann.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 6c4980536eddd0226fac422ae17ddb717e34630d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65799467"
---
# <a name="customize-a-person-model-with-the-video-indexer-api"></a>Anpassen eines Personenmodells mit der Video Indexer-API

Video Indexer unterstützt Gesichtserkennung und Erkennung bekannter Personen für Videoinhalte. Die Funktion zur Erkennung von Prominenten umfasst ungefähr eine Million Gesichter, die auf häufig angeforderten Datenquellen wie IMDB, Wikipedia und den wichtigsten LinkedIn-Influencern basieren. Gesichter, die von der Funktion zur Erkennung von Prominenten nicht erkannt werden, werden erfasst, bleiben aber unbenannt. Nachdem Sie Ihr Video in Video Indexer hochgeladen und die Ergebnisse zurückerhalten haben, können Sie die Gesichter benennen, die zwar erfasst, aber nicht erkannt wurden. Sobald Sie ein Gesicht mit einem Namen versehen haben, werden Gesicht und Name dem Personenmodell Ihres Kontos hinzugefügt. Video Indexer erkennt dieses Gesicht dann in Ihren zukünftigen und früheren Videos.

Sie können die Video Indexer-API verwenden, um Gesichter zu bearbeiten, die in einem Video erkannt wurden. Dies wird in diesem Artikel beschrieben. Sie können auch die Video Indexer-Website verwenden, wie unter [Anpassen des Personenmodells mit der Video Indexer-Website](customize-person-model-with-api.md) beschrieben wird.

## <a name="managing-multiple-person-models"></a>Verwalten mehrerer Personenmodelle 

Video Indexer unterstützt mehrere Personenmodelle pro Konto. Dieses Feature ist derzeit nur über die Video Indexer-APIs verfügbar.

Wenn Ihr Konto verschiedenen Anwendungsszenarien entspricht, sollten Sie möglicherweise mehrere Personenmodelle pro Konto erstellen. Wenn sich Ihre Inhalte z. B. mit dem Thema „Sport“ befassen, können Sie dann für jede Sportart (Football, Basketball, Fußball, usw.) ein separates Personenmodell erstellen. 

Sobald ein Modell erstellt wurde, können Sie es verwenden, indem Sie die Modell-ID eines bestimmten Personenmodells beim Hochladen/Indizieren oder erneuten Indizieren eines Videos angeben. Beim Trainieren eines neuen Gesichts für ein Video wird das bestimmte benutzerdefinierte Modell, dem das Video zugeordnet war, aktualisiert.

Jedes Konto hat ein Limit von 50 Personenmodellen. Wenn Sie keine Unterstützung für Mehrpersonenmodelle benötigen, weisen Sie Ihrem Video beim Hochladen/Indizieren oder erneuten Indizieren keine Personenmodell-ID zu. In diesem Fall verwendet Video Indexer das standardmäßige benutzerdefinierte Personenmodell in Ihrem Konto.

## <a name="create-a-new-person-model"></a>Erstellen eines neuen Personenmodells

Erstellen Sie ein neues Personenmodell im angegebenen Konto. 

### <a name="request-url"></a>Anfrage-URL

Dies ist eine POST-Anforderung.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?name={name}&accessToken={accessToken}
```

Unten sehen Sie die Anforderung in Curl.

```curl
curl -v -X POST "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?name={name}&accessToken={accessToken}"
```

[Erforderliche Parameter anzeigen und mit dem Video Indexer-Entwicklerportal ausprobieren](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Person-Model?).

### <a name="request-parameters"></a>Anforderungsparameter 

|**Name**|**Typ**|**Erforderlich**|**Beschreibung**|
|---|---|---|---|
|location|Zeichenfolge|Ja|Die Azure-Region, an die der Aufruf weitergeleitet werden soll. Weitere Informationen finden Sie unter [Azure-Regionen und Video Indexer](regions.md).|
|accountId|Zeichenfolge|Ja|Global eindeutiger Bezeichner für das Konto|
|name|Zeichenfolge|Ja|Name für das Personenmodell|
|accessToken|Zeichenfolge|Ja|Zugriffstoken (der Bereich muss [Kontozugriffstoken](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) sein) zum Authentifizieren des Aufrufs. Zugriffstoken laufen nach einer Stunde ab.|

### <a name="request-body"></a>Anforderungstext

Es ist kein weiterer Anforderungstext für diesen Aufruf erforderlich.

### <a name="response"></a>response

Die Antwort stellt den Namen und die generierte Modell-ID des Personenmodells bereit, das Sie gerade im Format des folgenden Beispiels erstellt haben.

```json
{
    "id": "227654b4-912c-4b92-ba4f-641d488e3720",
    "name": "Example Person Model"
}
```

Sie sollten dann den Wert **id** für den Parameter **personModelId** verwenden, wenn [ein zu indizierendes Video hochgeladen](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) oder [ein Video neu indiziert](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) wird.

## <a name="delete-a-person-model"></a>Löschen eines Personenmodells

Löscht ein benutzerdefiniertes Personenmodell aus dem angegebenen Konto. 

Sobald das Personenmodell erfolgreich gelöscht wurde, bleibt der Index Ihrer aktuellen Videos, die das gelöschte Modell verwendet haben, unverändert, bis Sie sie neu indizieren. Beim erneuten Indizieren werden die Gesichter, die im gelöschten Modell benannt wurden, von Video Indexer in Ihren aktuellen Videos, die mit diesem Modell indiziert wurden, nicht erkannt. Diese Gesichter werden jedoch weiterhin ermittelt. Ihre aktuellen Videos, die mit dem gelöschten Modell indiziert wurden, verwenden nun das Standardpersonenmodell Ihres Kontos. Wenn Gesichter aus dem gelöschten Modell auch im Standardmodell Ihres Kontos benannt sind, werden diese Gesichter in den Videos weiterhin erkannt.

### <a name="request-url"></a>Anfrage-URL

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels/{id}?accessToken={accessToken}
```

Unten sehen Sie die Anforderung in Curl.
```curl
curl -v -X DELETE "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels/{id}?accessToken={accessToken}"
```

[Erforderliche Parameter anzeigen und mit dem Video Indexer-Entwicklerportal ausprobieren](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Person-Model?).

### <a name="request-parameters"></a>Anforderungsparameter

|**Name**|**Typ**|**Erforderlich**|**Beschreibung**|
|---|---|---|---|
|location|Zeichenfolge|Ja|Die Azure-Region, an die der Aufruf weitergeleitet werden soll. Weitere Informationen finden Sie unter [Azure-Regionen und Video Indexer](regions.md).|
|accountId|Zeichenfolge|Ja|Global eindeutiger Bezeichner für das Konto|
|id|Zeichenfolge|Ja|Die Personenmodell-ID (beim Erstellen des Personenmodells generiert)|
|accessToken|Zeichenfolge|Ja|Zugriffstoken (der Bereich muss [Kontozugriffstoken](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) sein) zum Authentifizieren des Aufrufs. Zugriffstoken laufen nach einer Stunde ab.|

### <a name="request-body"></a>Anforderungstext

Es ist kein weiterer Anforderungstext für diesen Aufruf erforderlich.

### <a name="response"></a>response

Es wird kein Inhalt zurückgegeben, wenn das Personenmodell erfolgreich gelöscht wurde.

## <a name="get-all-person-models"></a>Abrufen aller Personenmodelle

Rufen Sie alle Personenmodelle im angegebenen Konto ab. 

### <a name="request-call"></a>Anforderungsaufruf

Dies ist eine GET-Anforderung.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?accessToken={accessToken}
```

Unten sehen Sie die Anforderung in Curl.

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?accessToken={accessToken}"
```

[Erforderliche Parameter anzeigen und mit dem Video Indexer-Entwicklerportal ausprobieren](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Person-Models?).

### <a name="request-parameters"></a>Anforderungsparameter

|**Name**|**Typ**|**Erforderlich**|**Beschreibung**|
|---|---|---|---|
|location|Zeichenfolge|Ja|Die Azure-Region, an die der Aufruf weitergeleitet werden soll. Weitere Informationen finden Sie unter [Azure-Regionen und Video Indexer](regions.md).|
|accountId|Zeichenfolge|Ja|Global eindeutiger Bezeichner für das Konto|
|accessToken|Zeichenfolge|Ja|Zugriffstoken (der Bereich muss [Kontozugriffstoken](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) sein) zum Authentifizieren des Aufrufs. Zugriffstoken laufen nach einer Stunde ab.|

### <a name="request-body"></a>Anforderungstext

Es ist kein weiterer Anforderungstext für diesen Aufruf erforderlich.

### <a name="response"></a>response

Die Antwort enthält eine Liste aller Personenmodelle in Ihrem Konto (einschließlich des Standardpersonenmodells in dem angegebenen Konto) sowie deren Namen und IDs im Format des folgenden Beispiels.

```json
[
    {
        "id": "59f9c326-b141-4515-abe7-7d822518571f",
        "name": "Default"
    }, 
    {
        "id": "9ef2632d-310a-4510-92e1-cc70ae0230d4",
        "name": "Test"
    }
]
```

Sie können auswählen, welches Modell Sie für ein Video verwenden möchten, indem Sie den Wert **id** des Personenmodells für den Parameter **personModelId** verwenden, wenn Sie [ein zu indizierendes Video hochladen](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) oder [ein Video neu indizieren](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="update-a-face"></a>Gesicht aktualisieren

Mit diesem Befehl können Sie ein Gesicht in Ihrem Video mit einem Namen aktualisieren, indem Sie die ID des Videos und die ID des Gesichts verwenden. Dadurch wird das Personenmodell aktualisiert, dem das Video beim Hochladen/Indizieren oder erneuten Indizieren zugeordnet wurde. Wenn kein Personenmodell zugewiesen wurde, wird das Standardpersonenmodell des Kontos aktualisiert. 

Sobald dies der Fall ist, werden die Vorkommen desselben Gesichts in Ihren anderen aktuellen Videos erkannt, die dasselbe Personenmodell verwenden. Die Erkennung des Gesichts in Ihren anderen aktuellen Videos kann einige Zeit in Anspruch nehmen, da es sich um einen Batchprozess handelt.

Sie können ein Gesicht, das von Video Indexer als Prominenten erkannt wurde, mit einem neuen Namen aktualisieren. Der neue Name, den Sie vergeben, hat Vorrang vor der integrierten Erkennung von Prominenten.

### <a name="request-call"></a>Anforderungsaufruf

Dies ist eine POST-Anforderung.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Videos/{videoId}/Index/Faces/{faceId}?accessToken={accessToken}&newName={newName}
```

Unten sehen Sie die Anforderung in Curl.

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Videos/{videoId}/Index/Faces/{faceId}?accessToken={accessToken}&newName={newName}"
```

[Erforderliche Parameter anzeigen und mit dem Video Indexer-Entwicklerportal ausprobieren](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Video-Face?).

### <a name="request-parameters"></a>Anforderungsparameter

|**Name**|**Typ**|**Erforderlich**|**Beschreibung**|
|---|---|---|---|
|location|Zeichenfolge|Ja|Die Azure-Region, an die der Aufruf weitergeleitet werden soll. Weitere Informationen finden Sie unter [Azure-Regionen und Video Indexer](regions.md).|
|accountId|Zeichenfolge|Ja|Global eindeutiger Bezeichner für das Konto|
|videoId|Zeichenfolge|Ja|ID für das Video, in dem das Gesicht zu sehen ist, das Sie aktualisieren möchten. Dies wird erstellt, wenn das Video hochgeladen und indiziert wurde.|
|faceId|integer|Ja|Die ID für das zu aktualisierende Gesicht. Sie können die „faceId“ aus dem Videoindex abrufen.|
|accessToken|Zeichenfolge|Ja|Zugriffstoken (der Bereich muss [Kontozugriffstoken](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) sein) zum Authentifizieren des Aufrufs. Zugriffstoken laufen nach einer Stunde ab.|
|name|Zeichenfolge|Ja|Neuer Name, mit dem das Gesicht aktualisiert wird.|

Namen sind für Personenmodelle eindeutig. Wenn Sie zwei verschiedenen Gesichtern in demselben Personenmodell denselben Parameterwert für **Name** zuweisen, betrachtet Video Indexer die Gesichter als dieselbe Person und führt sie zusammen, nachdem Sie Ihr Video neu indizieren. 

### <a name="request-body"></a>Anforderungstext

Es ist kein weiterer Anforderungstext für diesen Aufruf erforderlich.

### <a name="response"></a>response

Es gibt keinen zurückgegebenen Inhalt, wenn das Gesicht erfolgreich aktualisiert wurde.

## <a name="next-steps"></a>Nächste Schritte

[Anpassen eines Personenmodells mit der Video Indexer-Website](customize-person-model-with-website.md)
