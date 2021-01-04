---
title: REST-API für die Medienobjektkonvertierung
description: Beschreibt, wie Sie ein Medienobjekt über die REST-API konvertieren.
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 889a70005f1cbabaad525147b4661ea04886138a
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445607"
---
# <a name="use-the-model-conversion-rest-api"></a>Verwenden der REST-API für die Modellkonvertierung

Der [Modellkonvertierungsdienst](model-conversion.md) wird über eine [REST-API](https://en.wikipedia.org/wiki/Representational_state_transfer) gesteuert. In diesem Artikel werden die Details der Konvertierungsdienst-API beschrieben.

## <a name="regions"></a>Regions

Informationen zu den Basis-URLs, an die Anforderungen gesendet werden, finden Sie in der [Liste der verfügbaren Regionen](../../reference/regions.md).

## <a name="common-headers"></a>Allgemeine Header

### <a name="common-request-headers"></a>Allgemeine Anforderungsheader

Diese Header müssen für alle Anforderungen angegeben werden:

- Der **Authorization** -Header muss den Wert „Bearer [ *TOKEN* ]“ aufweisen, wobei [ *TOKEN* ] ein [Dienstzugriffstoken](../tokens.md) ist.

### <a name="common-response-headers"></a>Allgemeine Antwortheader

Alle Antworten enthalten die folgenden Header:

- Der **MS-CV** -Header enthält eine eindeutige Zeichenfolge, die verwendet werden kann, um den Aufruf innerhalb des Diensts nachzuverfolgen.

## <a name="endpoints"></a>Endpunkte

Der Konvertierungsdienst stellt drei REST-API-Endpunkte für Folgendes bereit:

- Starten der Modellkonvertierung mithilfe eines Speicherkontos, das mit Ihrem Azure Remote Rendering-Konto verknüpft ist. 
- Starten der Modellkonvertierung mithilfe der bereitgestellten *Shared Access Signatures (SAS)* .
- Abfragen des Konvertierungsstatus

### <a name="start-conversion-using-a-linked-storage-account"></a>Starten der Konvertierung mithilfe eines verknüpften Speicherkontos
Ihr Azure Remote Rendering-Konto muss Zugriff auf das angegebene Speicherkonto besitzen, indem Sie die Schritte zum [Verknüpfen von Speicherkonten](../create-an-account.md#link-storage-accounts) ausführen.

| Endpunkt | Methode |
|-----------|:-----------|
| /v1/accounts/ **accountID** /conversions/create | POST |

Gibt die ID der aktuell ausgeführten Konvertierung in einem JSON-Dokument als Wrapper zurück. Der Feldname lautet „conversionId“.

#### <a name="request-body"></a>Anforderungstext

> [!NOTE]
> Alles unter `input.folderPath` wird abgerufen, um die Konvertierung in Azure durchzuführen. Wenn `input.folderPath` nicht angegeben ist, wird der gesamte Inhalt des Containers abgerufen. Alle Blobs und Ordner, die abgerufen werden, müssen [gültige Windows-Dateinamen](/windows/win32/fileio/naming-a-file#naming-conventions) besitzen.

```json
{
    "input":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container containing your input asset data>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the input blob container>",
        "inputAssetPath" : "<path to the model in the input blob container relative to the folderPath (or container root if no folderPath is specified)>"
    },
    "output":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container where the converted asset will be copied to>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the output blob container. Will contain the asset and log files>",
        "outputAssetFileName": "<optional: can be omitted or empty. The filename of the converted asset. If provided the filename needs to end in .arrAsset>"
    }
}
```
### <a name="start-conversion-using-provided-shared-access-signatures"></a>Starten der Konvertierung mithilfe der bereitgestellten Shared Access Signatures (SAS)
Wenn Ihr ARR-Konto nicht mit Ihrem Speicherkonto verknüpft ist, können Sie mithilfe dieser REST-Schnittstelle Zugriff über *Shared Access Signatures (SAS)* bereitstellen.

| Endpunkt | Methode |
|-----------|:-----------|
| /v1/accounts/ **accountID** /conversions/createWithSharedAccessSignature | POST |

Gibt die ID der aktuell ausgeführten Konvertierung in einem JSON-Dokument als Wrapper zurück. Der Feldname lautet `conversionId`.

#### <a name="request-body"></a>Anforderungstext

Der Anforderungstext ist derselbe wie im oben aufgeführten REST-Befehl „create“, aber die Eingabe und die Ausgabe enthält jeweils *SAS-Token (Shared Access Signature)* . Diese Token ermöglichen den Zugriff auf das Speicherkonto zum Lesen der Eingabe und zum Schreiben des Konvertierungsergebnisses.

> [!NOTE]
> Diese SAS-URI-Token sind die Abfragezeichenfolgen und nicht der vollständige URI. 

> [!NOTE]
> Alles unter `input.folderPath` wird abgerufen, um die Konvertierung in Azure durchzuführen. Wenn `input.folderPath` nicht angegeben ist, wird der gesamte Inhalt des Containers abgerufen. Alle Blobs und Ordner, die abgerufen werden, müssen [gültige Windows-Dateinamen](/windows/win32/fileio/naming-a-file#naming-conventions) besitzen.

```json
{
    "input":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container containing your input asset data>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the input blob container>",
        "inputAssetPath" : "<path to the model in the input blob container relative to the folderPath (or container root if no folderPath is specified)>",
        "containerReadListSas" : "<a container SAS token which gives read and list access to the given input blob container>"
    },
    "output":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container where the converted asset will be copied to>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the output blob container. Will contain the asset and log files>",
        "outputAssetFileName": "<optional: can be omitted or empty. The filename of the converted asset. If provided the filename needs to end in .arrAsset>",
        "containerWriteSas" : "<a container SAS token which gives write access to the given output blob container>"
    }
}
```

### <a name="poll-conversion-status"></a>Abrufen des Konvertierungsstatus
Der Status einer aktuell ausgeführten Konvertierung, die mit einem der oben genannten REST-Aufrufe gestartet wurde, kann mithilfe der folgenden Schnittstelle abgefragt werden:


| Endpunkt | Methode |
|-----------|:-----------|
| /v1/accounts/ **accountID** /conversions/ **conversionId** | GET |

Gibt ein JSON-Dokument mit einem Feld „status“ zurück, das die folgenden Werte aufweisen kann:

- "Created"
- „Running“ (wird ausgeführt)
- „Success“
- „Failure“ (Fehler)

Wenn der Status „Failure“ lautet, wird ein zusätzliches Feld „error“ mit einem Unterfeld „Message“ angezeigt, das Fehlerinformationen enthält. Zusätzliche Protokolle werden in Ihren Ausgabecontainer hochgeladen.

## <a name="list-conversions"></a>Konvertierungen auflisten

Verwenden Sie diese Schnittstelle, um eine Liste aller Konvertierungen für ein Konto abzurufen:

| Endpunkt | Methode |
|-----------|:-----------|
| /v1/accounts/ **accountID** /conversions?skiptoken= **skipToken** | GET |

| Parameter | Erforderlich |
|-----------|:-----------|
| accountID | Ja |
| skiptoken | Nein |

Gibt ein JSON-Dokument zurück, das ein Array der Konvertierungen und deren Details enthält. Diese Abfrage gibt jeweils maximal 50 Konvertierungen zurück. Wenn mehr Konvertierungen abgerufen werden müssen, enthält die Antwort eine **nextLink** -Eigenschaft, die das skipToken enthält, das zum Abrufen des nächsten Resultsets abgefragt werden kann.

## <a name="next-steps"></a>Nächste Schritte

- [Verwenden von Azure Blob Storage für die Modellkonvertierung](blob-storage.md)
- [Modellkonvertierung](model-conversion.md)