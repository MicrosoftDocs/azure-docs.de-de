---
title: 'Verwenden der Video Indexer-APIs zum Anpassen eines Sprachmodells: Azure'
titlesuffix: Azure Media Services
description: In diesem Artikel wird gezeigt, wie ein Sprachmodell mit den Video Indexer-APIs angepasst werden kann.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 4ef5354a94ae707df8dd1f2767efe04dfbacd7ad
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65799589"
---
# <a name="customize-a-language-model-with-the-video-indexer-apis"></a>Anpassen eines Sprachmodells mit den Video Indexer-APIs

Mit Video Indexer können Sie benutzerdefinierte Sprachmodelle erstellen, um die Spracherkennung durch Hochladen von Anpassungstexten anzupassen, insbesondere von Texten aus dem Bereich, an dessen Vokabular sich die Engine anpassen soll. Sobald Sie Ihr Modell trainiert haben, werden neue Wörter, die im Anpassungstext vorkommen, erkannt. 

Eine detaillierte Übersicht und bewährte Methoden für benutzerdefinierte Sprachmodelle finden Sie unter [Anpassen eines Sprachmodells mit Video Indexer](customize-language-model-overview.md).

Sie können die Video Indexer-APIs verwenden, um benutzerdefinierte Sprachmodelle in Ihrem Konto zu erstellen und zu bearbeiten, wie in diesem Thema beschrieben. Sie können auch die Website verwenden, wie unter [Anpassen des Sprachmodells mit der Video Indexer-Website](customize-language-model-with-api.md) beschrieben wird.

## <a name="create-a-language-model"></a>Erstellen eines Sprachmodells

Der folgende Befehl erstellt ein neues benutzerdefiniertes Sprachmodell im angegebenen Konto. Sie können Dateien für das Sprachmodell in diesem Aufruf hochladen. Alternativ können Sie das Sprachmodell hier erstellen und Dateien für das Modell später durch Aktualisieren des Sprachmodells hochladen.

> [!NOTE]
> Sie müssen das Modell mit seinen aktivierten Dateien weiterhin trainieren, damit das Modell den Inhalt seiner Dateien erlernen kann. Anleitungen zum Trainieren einer Sprache finden Sie im nächsten Abschnitt.

### <a name="request-url"></a>Anfrage-URL

Dies ist eine POST-Anforderung.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?name={name}&accessToken={accessToken}
```

Unten sehen Sie die Anforderung in Curl.

```curl
curl -v -X POST "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language?accessToken={accessToken}&modelName={modelName}&language={language}"

--data-ascii "{body}" 
```

[Erforderliche Parameter anzeigen und mit dem Video Indexer-Entwicklerportal ausprobieren](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Person-Model?).

### <a name="request-parameters"></a>Anforderungsparameter

|**Name**|**Typ**|**Erforderlich**|**Beschreibung**|
|---|---|---|---|
|location|Zeichenfolge|Ja|Die Azure-Region, an die der Aufruf weitergeleitet werden soll. Weitere Informationen finden Sie unter [Azure-Regionen und Video Indexer](regions.md).|
|accountId|Zeichenfolge|Ja|Global eindeutiger Bezeichner für das Konto|
|accessToken|Zeichenfolge|Ja|Zugriffstoken (der Bereich muss [Kontozugriffstoken](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) sein) zum Authentifizieren des Aufrufs. Zugriffstoken laufen nach einer Stunde ab.|
|modelName|Zeichenfolge|Ja|Der Name für das Sprachmodell.|
|language|Zeichenfolge|Ja|Die Sprache des Sprachmodells. <br/>Der Parameter **language** muss mit der Sprache im BCP-47-Format als „language tag-region“ (z.B. „en-US“) angegeben werden. Unterstützte Sprachen sind Englisch (en-US), Deutsch (de-DE), Spanisch (es-SP), Arabisch (ar-EG), Französisch (fr-FR), Hindi (hi-HI), Italienisch (it-IT), Japanisch (ja-JP), Portugiesisch (pt-BR), Russisch (ru-RU) und Chinesisch (zh-CN).  |

### <a name="request-body"></a>Anforderungstext

Um Dateien hochzuladen, die dem Sprachmodell hinzugefügt werden sollen, müssen Sie Dateien im Textkörper mithilfe von Formulardaten hochladen und zusätzlich Werte für die oben genannten erforderlichen Parameter angeben. Dies kann auf zwei Arten erreicht werden: 

1. Der Schlüssel ist der Dateiname und der Wert die TXT-Datei.
2. Der Schlüssel ist der Dateiname und der Wert eine URL zur TXT-Datei.

### <a name="response"></a>response

Die Antwort stellt Metadaten zum neu erstellten Sprachmodell zusammen mit Metadaten zu jeder der Dateien des Modells im Format der JSON-Beispielausgabe bereit.

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}

```

## <a name="train-a-language-model"></a>Trainieren eines Sprachmodells

Der folgende Befehl trainiert ein benutzerdefiniertes Sprachmodell im angegebenen Konto mit dem Inhalt der Dateien, die in das Sprachmodell hochgeladen und in ihm aktiviert wurden. 

> [!NOTE]
> Zunächst müssen Sie das Sprachmodell erstellen und seine Dateien hochladen. Sie können Dateien entweder beim Erstellen des Sprachmodells oder durch Aktualisieren des Sprachmodells hochladen. 

### <a name="request-url"></a>Anfrage-URL

Dies ist eine PUT-Anforderung.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Train?accessToken={accessToken}
```

Unten sehen Sie die Anforderung in Curl.

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Train?accessToken={accessToken}"
```
 
[Erforderliche Parameter anzeigen und mit dem Video Indexer-Entwicklerportal ausprobieren](https://api-portal.videoindexer.ai/docs/services/operations/operations/Train-Language-Model?&pattern=train).

### <a name="request-parameters"></a>Anforderungsparameter

|**Name**|**Typ**|**Erforderlich**|**Beschreibung**|
|---|---|---|---|
|location|Zeichenfolge|Ja|Die Azure-Region, an die der Aufruf weitergeleitet werden soll. Weitere Informationen finden Sie unter [Azure-Regionen und Video Indexer](regions.md).|
|accountID|Zeichenfolge|Ja|Global eindeutiger Bezeichner für das Konto|
|modelId|Zeichenfolge|Ja|Die Sprachmodell-ID (wird beim Erstellen des Sprachmodells generiert).|
|accessToken|Zeichenfolge|Ja|Zugriffstoken (der Bereich muss [Kontozugriffstoken](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) sein) zum Authentifizieren des Aufrufs. Zugriffstoken laufen nach einer Stunde ab.|

### <a name="request-body"></a>Anforderungstext

Es ist kein weiterer Anforderungstext für diesen Aufruf erforderlich.

### <a name="response"></a>response

Die Antwort stellt Metadaten zum neu trainierten Sprachmodell zusammen mit Metadaten zu jeder der Dateien des Modells im Format der JSON-Beispielausgabe bereit.

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": false,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```

Sie sollten dann den Wert **id** des Sprachmodells für den Parameter **linguisticModelId** verwenden, wenn [Sie ein zu indizierendes Video hochladen](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?), und für den Parameter **languageModelId**, wenn Sie [ein Video erneut indizieren](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?).

## <a name="delete-a-language-model"></a>Löschen eines Sprachmodells

Der folgende Befehl löscht ein benutzerdefiniertes Sprachmodell aus dem angegebenen Konto. Jedes Video, das das gelöschte Sprachmodell verwendet hat, behält den gleichen Index bei, bis Sie das Video erneut indizieren. Wenn Sie das Video erneut indizieren, können Sie dem Video ein neues Sprachmodell zuweisen. Andernfalls verwendet Video Indexer das Standardmodell, um das Video erneut zu indizieren.

### <a name="request-url"></a>Anfrage-URL

Dies ist eine DELETE-Anforderung.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}
```

Unten sehen Sie die Anforderung in Curl.

```curl
curl -v -X DELETE "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}"
```
 
[Erforderliche Parameter anzeigen und mit dem Video Indexer-Entwicklerportal ausprobieren](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model?&pattern=delete).

### <a name="request-parameters"></a>Anforderungsparameter 

|**Name**|**Typ**|**Erforderlich**|**Beschreibung**|
|---|---|---|---|
|location|Zeichenfolge|Ja|Die Azure-Region, an die der Aufruf weitergeleitet werden soll. Weitere Informationen finden Sie unter [Azure-Regionen und Video Indexer](regions.md).|
|accountID|Zeichenfolge|Ja|Global eindeutiger Bezeichner für das Konto|
|modelId|Zeichenfolge|Ja|Die Sprachmodell-ID (wird beim Erstellen des Sprachmodells generiert).|
|accessToken|Zeichenfolge|Ja|Zugriffstoken (der Bereich muss [Kontozugriffstoken](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) sein) zum Authentifizieren des Aufrufs. Zugriffstoken laufen nach einer Stunde ab.|

### <a name="request-body"></a>Anforderungstext

Es ist kein weiterer Anforderungstext für diesen Aufruf erforderlich.

### <a name="response"></a>response

Es wird kein Inhalt zurückgegeben, wenn das Sprachmodell erfolgreich gelöscht wurde.

## <a name="update-a-language-model"></a>Aktualisieren eines Sprachmodells

Der folgende Befehl aktualisiert ein benutzerdefiniertes Sprachmodell im angegebenen Konto.

> [!NOTE]
> Sie müssen das Sprachmodell bereits erstellt haben. Mit diesem Aufruf können Sie alle Dateien im Modell aktivieren oder deaktivieren, den Namen des Sprachmodells aktualisieren und Dateien hochladen, die dem Sprachmodell hinzugefügt werden sollen.

### <a name="request-url"></a>Anfrage-URL

Dies ist eine PUT-Anforderung.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}[&modelName][&enable]
```

Unten sehen Sie die Anforderung in Curl.

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}?modelName={string}&enable={string}"

--data-ascii "{body}" 
```
 
[Erforderliche Parameter anzeigen und mit dem Video Indexer-Entwicklerportal ausprobieren](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model?&pattern=update).

### <a name="request-parameters"></a>Anforderungsparameter 

|**Name**|**Typ**|**Erforderlich**|**Beschreibung**|
|---|---|---|---|
|location|Zeichenfolge|Ja|Die Azure-Region, an die der Aufruf weitergeleitet werden soll. Weitere Informationen finden Sie unter [Azure-Regionen und Video Indexer](regions.md).|
|accountID|Zeichenfolge|Ja|Global eindeutiger Bezeichner für das Konto|
|modelId|Zeichenfolge|Ja|Die Sprachmodell-ID (wird beim Erstellen des Sprachmodells generiert).|
|accessToken|Zeichenfolge|Ja|Zugriffstoken (der Bereich muss [Kontozugriffstoken](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) sein) zum Authentifizieren des Aufrufs. Zugriffstoken laufen nach einer Stunde ab.|
|modelName|Zeichenfolge|Nein|Der neue Name, den Sie dem Modell geben können.|
|enable|boolean|Nein|Wählen Sie aus, ob alle Dateien in diesem Modell aktiviert (TRUE) oder deaktiviert (FALSE) sind.|

### <a name="request-body"></a>Anforderungstext

Um Dateien hochzuladen, die dem Sprachmodell hinzugefügt werden sollen, müssen Sie Dateien im Textkörper mithilfe von Formulardaten hochladen und zusätzlich Werte für die oben genannten erforderlichen Parameter angeben. Dies kann auf zwei Arten erreicht werden: 

1. Der Schlüssel ist der Dateiname und der Wert die TXT-Datei.
2. Der Schlüssel ist der Dateiname und der Wert eine URL zur TXT-Datei.

### <a name="response"></a>response

Die Antwort stellt Metadaten zum neu trainierten Sprachmodell zusammen mit Metadaten zu jeder der Dateien des Modells im Format der JSON-Beispielausgabe bereit.

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```
Sie können die **id** der Dateien verwenden, die hier zurückgegeben wird, um den Inhalt der Datei herunterzuladen.

## <a name="update-a-file-from-a-language-model"></a>Aktualisieren einer Datei aus einem Sprachmodell

Der folgende Befehl ermöglicht es Ihnen, den Namen und den Zustand **enable** einer Datei in einem benutzerdefinierten Sprachmodell im angegebenen Konto zu aktualisieren.

### <a name="request-url"></a>Anfrage-URL

Dies ist eine PUT-Anforderung.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}[&fileName][&enable]
```

Unten sehen Sie die Anforderung in Curl.

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}?fileName={string}&enable={string}"
```
 
[Erforderliche Parameter anzeigen und mit dem Video Indexer-Entwicklerportal ausprobieren](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model-file?&pattern=update).

### <a name="request-parameters"></a>Anforderungsparameter 

|**Name**|**Typ**|**Erforderlich**|**Beschreibung**|
|---|---|---|---|
|location|Zeichenfolge|Ja|Die Azure-Region, an die der Aufruf weitergeleitet werden soll. Weitere Informationen finden Sie unter [Azure-Regionen und Video Indexer](regions.md).|
|accountId|Zeichenfolge|Ja|Global eindeutiger Bezeichner für das Konto|
|modelId|Zeichenfolge|Ja|ID des Sprachmodells, das die Datei enthält (wird generiert, wenn das Sprachmodell erstellt wird).|
|fileId|Zeichenfolge|Ja|ID der Datei, die aktualisiert wird (wird generiert, wenn die Datei bei der Erstellung oder Aktualisierung des Sprachmodells hochgeladen wird).|
|accessToken|Zeichenfolge|Ja|Zugriffstoken (der Bereich muss [Kontozugriffstoken](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) sein) zum Authentifizieren des Aufrufs. Zugriffstoken laufen nach einer Stunde ab.|
|fileName|Zeichenfolge|Nein|Der Name, in den der Dateiname aktualisiert werden soll.|
|enable|boolean|Nein|Aktualisieren Sie, ob diese Datei im Sprachmodell aktiviert (TRUE) oder deaktiviert (FALSE) ist.|

### <a name="request-body"></a>Anforderungstext

Es ist kein weiterer Anforderungstext für diesen Aufruf erforderlich.

### <a name="response"></a>response

Die Antwort stellt Metadaten im Format der unten gezeigten JSON-Beispielausgabe für die Datei bereit, die Sie aktualisiert haben.

```json
{
  "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
  "name": "RenamedFile",
  "enable": false,
  "creator": "John Doe",
  "creationTime": "2018-04-27T20:10:10.5233333"
}
```
Sie können die **id** der Datei verwenden, die hier zurückgegeben wird, um den Inhalt der Datei herunterzuladen.

## <a name="get-a-specific-language-model"></a>Abrufen eines bestimmten Sprachmodells

Der folgende Befehl gibt Informationen zum angegebenen Sprachmodell im angegebenen Konto zurück, z.B. die Sprache und die Dateien, die sich im Sprachmodell befinden. 

### <a name="request-url"></a>Anfrage-URL

Dies ist eine GET-Anforderung.
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}
```

Unten sehen Sie die Anforderung in Curl.

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}"
```
 
[Erforderliche Parameter anzeigen und mit dem Video Indexer-Entwicklerportal ausprobieren](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model?&pattern=get).

### <a name="request-parameters-and-request-body"></a>Anforderungsparameter und Anforderungstext

|**Name**|**Typ**|**Erforderlich**|**Beschreibung**|
|---|---|---|---|
|location|Zeichenfolge|Ja|Die Azure-Region, an die der Aufruf weitergeleitet werden soll. Weitere Informationen finden Sie unter [Azure-Regionen und Video Indexer](regions.md).|
|accountID|Zeichenfolge|Ja|Global eindeutiger Bezeichner für das Konto|
|modelId|Zeichenfolge|Ja|Die Sprachmodell-ID (wird beim Erstellen des Sprachmodells generiert).|
|accessToken|Zeichenfolge|Ja|Zugriffstoken (der Bereich muss [Kontozugriffstoken](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) sein) zum Authentifizieren des Aufrufs. Zugriffstoken laufen nach einer Stunde ab.|

### <a name="request-body"></a>Anforderungstext

Es ist kein weiterer Anforderungstext für diesen Aufruf erforderlich.

### <a name="response"></a>response

Die Antwort stellt Metadaten zum angegebenen Sprachmodell zusammen mit Metadaten zu jeder der Dateien des Modells im Format der unten gezeigten JSON-Beispielausgabe bereit.

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}
```

Sie können die **id** der Datei verwenden, die hier zurückgegeben wird, um den Inhalt der Datei herunterzuladen.

## <a name="get-all-the-language-models"></a>Abrufen aller Sprachmodelle

Der folgende Befehl gibt alle benutzerdefinierten Sprachmodelle im angegebenen Konto in einer Liste zurück.

### <a name="request-url"></a>Anfrage-URL

Dies ist eine GET-Anforderung.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language?accessToken={accessToken}
```

Unten sehen Sie die Anforderung in Curl.

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language?accessToken={accessToken}"
```
 
[Erforderliche Parameter anzeigen und mit dem Video Indexer-Entwicklerportal ausprobieren](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Models?&pattern=get).

### <a name="request-parameters"></a>Anforderungsparameter

|**Name**|**Typ**|**Erforderlich**|**Beschreibung**|
|---|---|---|---|
|location|Zeichenfolge|Ja|Die Azure-Region, an die der Aufruf weitergeleitet werden soll. Weitere Informationen finden Sie unter [Azure-Regionen und Video Indexer](regions.md).|
|accountID|Zeichenfolge|Ja|Global eindeutiger Bezeichner für das Konto|
|accessToken|Zeichenfolge|Ja|Zugriffstoken (der Bereich muss [Kontozugriffstoken](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) sein) zum Authentifizieren des Aufrufs. Zugriffstoken laufen nach einer Stunde ab.|

### <a name="request-body"></a>Anforderungstext

Es ist kein weiterer Anforderungstext für diesen Aufruf erforderlich.

### <a name="response"></a>response

Die Antwort enthält eine Liste mit allen Sprachmodellen in Ihrem Konto und deren Metadaten und Dateien im Format der unten gezeigten JSON-Beispielausgabe.

```json
[
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
        "name": "TestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000000",
        "files": [
        {
            "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
            "name": "hellofile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.6733333"
        },
        {
            "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
            "name": "worldfile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.86"
        }
        ]
    },
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a892",
        "name": "AnotherTestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000001",
        "files": []
    }
]
```

## <a name="delete-a-file-from-a-language-model"></a>Löschen einer Datei aus einem Sprachmodell

Der folgende Befehl löscht die angegebene Datei aus dem angegebenen Sprachmodell im angegebenen Konto. 

### <a name="request-url"></a>Anfrage-URL

Dies ist eine DELETE-Anforderung.
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}
```

Unten sehen Sie die Anforderung in Curl.

```curl
curl -v -X DELETE "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}"
```
 
[Erforderliche Parameter anzeigen und mit dem Video Indexer-Entwicklerportal ausprobieren](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model-File?&pattern=delete).

### <a name="request-parameters"></a>Anforderungsparameter 

|**Name**|**Typ**|**Erforderlich**|**Beschreibung**|
|---|---|---|---|
|location|Zeichenfolge|Ja|Die Azure-Region, an die der Aufruf weitergeleitet werden soll. Weitere Informationen finden Sie unter [Azure-Regionen und Video Indexer](regions.md).|
|accountID|Zeichenfolge|Ja|Global eindeutiger Bezeichner für das Konto|
|modelId|Zeichenfolge|Ja|ID des Sprachmodells, das die Datei enthält (wird generiert, wenn das Sprachmodell erstellt wird).|
|fileId|Zeichenfolge|Ja|ID der Datei, die aktualisiert wird (wird generiert, wenn die Datei bei der Erstellung oder Aktualisierung des Sprachmodells hochgeladen wird).|
|accessToken|Zeichenfolge|Ja|Zugriffstoken (der Bereich muss [Kontozugriffstoken](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) sein) zum Authentifizieren des Aufrufs. Zugriffstoken laufen nach einer Stunde ab.|

### <a name="request-body"></a>Anforderungstext

Es ist kein weiterer Anforderungstext für diesen Aufruf erforderlich.

### <a name="response"></a>response

Es wird kein Inhalt zurückgegeben, wenn die Datei erfolgreich aus dem Sprachmodell gelöscht wurde.

## <a name="get-metadata-on-a-file-from-a-language-model"></a>Abrufen von Metadaten für eine Datei aus einem Sprachmodell

Gibt die Inhalte und Metadaten für die angegebene Datei aus dem ausgewählten Sprachmodell in Ihrem Konto zurück.

### <a name="request-url"></a>Anfrage-URL

Dies ist eine GET-Anforderung.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?accessToken={accessToken}
```

Unten sehen Sie die Anforderung in Curl.
```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}"
```
 
[Erforderliche Parameter anzeigen und mit dem Video Indexer-Entwicklerportal ausprobieren](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model-File-Data?&pattern=get%20language%20model).

### <a name="request-parameters"></a>Anforderungsparameter 

|**Name**|**Typ**|**Erforderlich**|**Beschreibung**|
|---|---|---|---|
|location|Zeichenfolge|Ja|Die Azure-Region, an die der Aufruf weitergeleitet werden soll. Weitere Informationen finden Sie unter [Azure-Regionen und Video Indexer](regions.md).|
|accountID|Zeichenfolge|Ja|Global eindeutiger Bezeichner für das Konto|
|modelId|Zeichenfolge|Ja|Die ID des Sprachmodells, das die Datei enthält (wird generiert, wenn das Sprachmodell erstellt wird).|
|fileId|Zeichenfolge|Ja|Die ID der Datei, die aktualisiert wird (wird generiert, wenn die Datei bei der Erstellung oder Aktualisierung des Sprachmodells hochgeladen wird).|
|accessToken|Zeichenfolge|Ja|Zugriffstoken (der Bereich muss [Kontozugriffstoken](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) sein) zum Authentifizieren des Aufrufs. Zugriffstoken laufen nach einer Stunde ab.|

### <a name="request-body"></a>Anforderungstext

Es ist kein weiterer Anforderungstext für diesen Aufruf erforderlich.

### <a name="response"></a>response

Die Antwort enthält die Inhalte und Metadaten der Datei im JSON-Format, ähnlich wie in diesem Beispiel:

```json
{
    "content": "hello\r\nworld",
    "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
    "name": "Hello",
    "enable": true,
    "creator": "John Doe",
    "creationTime": "2018-04-27T20:10:10.5233333"
}
```

> [!NOTE]
> Der Inhalt dieser Beispieldatei sind die Wörter „hello“ und „world“ in zwei separaten Zeilen.

## <a name="download-a-file-from-a-language-model"></a>Herunterladen einer Datei aus einem Sprachmodell

Der folgende Befehl lädt eine Textdatei mit dem Inhalt der angegebenen Datei aus dem angegebenen Sprachmodell im angegebenen Konto herunter. Diese Textdatei sollte mit dem Inhalt der Textdatei übereinstimmen, die ursprünglich hochgeladen wurde.

### <a name="request-url"></a>Anfrage-URL
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}/download?accessToken={accessToken}
```

Unten sehen Sie die Anforderung in Curl.

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}/download?accessToken={accessToken}"
```
 
[Erforderliche Parameter anzeigen und mit dem Video Indexer-Entwicklerportal ausprobieren](https://api-portal.videoindexer.ai/docs/services/operations/operations/Download-Language-Model-File-Content?).

### <a name="request-parameters"></a>Anforderungsparameter 

|**Name**|**Typ**|**Erforderlich**|**Beschreibung**|
|---|---|---|---|
|location|Zeichenfolge|Ja|Die Azure-Region, an die der Aufruf weitergeleitet werden soll. Weitere Informationen finden Sie unter [Azure-Regionen und Video Indexer](regions.md).|
|accountID|Zeichenfolge|Ja|Global eindeutiger Bezeichner für das Konto|
|modelId|Zeichenfolge|Ja|ID des Sprachmodells, das die Datei enthält (wird generiert, wenn das Sprachmodell erstellt wird).|
|fileId|Zeichenfolge|Ja|ID der Datei, die aktualisiert wird (wird generiert, wenn die Datei bei der Erstellung oder Aktualisierung des Sprachmodells hochgeladen wird).|
|accessToken|Zeichenfolge|Ja|Zugriffstoken (der Bereich muss [Kontozugriffstoken](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) sein) zum Authentifizieren des Aufrufs. Zugriffstoken laufen nach einer Stunde ab.|

### <a name="request-body"></a>Anforderungstext 

Es ist kein weiterer Anforderungstext für diesen Aufruf erforderlich.

### <a name="response"></a>response

Die Antwort ist der Download einer Textdatei mit dem Inhalt der Datei im JSON-Format. 

## <a name="next-steps"></a>Nächste Schritte

[Anpassen des Sprachmodells mit der Website](customize-language-model-with-website.md)
