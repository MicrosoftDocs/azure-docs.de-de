---
title: Übersicht über die Media Services Operations-REST-API | Microsoft-Dokumentation
description: Übersicht über die Media Services-REST-API
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: a5f1c5e7-ec52-4e26-9a44-d9ea699f68d9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako;johndeu
ms.openlocfilehash: fbdd9325f50e1bcb271b7ca47b9ccd3361d0d27e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64687060"
---
# <a name="media-services-operations-rest-api-overview"></a>Übersicht über die Media Services Operations-REST-API 

> [!NOTE]
> Media Services v2 werden derzeit keine neuen Features oder Funktionen hinzugefügt. <br/>Sehen Sie sich die neuste Version – [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/) – an. Lesen Sie außerdem die [Hinweise zur Migration von v2 zu v3](../latest/migrate-from-v2-to-v3.md).

Die **Media Services Operations-REST-API** wird zum Erstellen von Aufträgen, Medienobjekten, Livekanälen und anderen Ressourcen in einem Media Services-Konto verwendet. Weitere Informationen finden Sie in der [Referenz zur Media Services Operations-REST-API](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference).

Media Services stellt eine REST-API zur Verfügung, die sowohl das JSON- als auch das XML-Format für „atom+pub“ akzeptiert. Die Media Services REST-API erfordert spezifische HTTP-Header, die jeder Client beim Herstellen der Verbindung mit Media Services senden muss, sowie eine Reihe optionaler Header. In den folgenden Abschnitten werden die Header und HTTP-Verben beschrieben, die Sie verwenden können, um Anforderungen zu erstellen und Antworten von Media Services zu empfangen.

Die Authentifizierung für die Media Services-REST-API erfolgt über die Azure Active Directory-Authentifizierung, die im Artikel [Verwenden der Azure AD-Authentifizierung zum Zugreifen auf die Azure Media Services-API per REST](media-services-rest-connect-with-aad.md) beschrieben wird.

## <a name="considerations"></a>Überlegungen

Berücksichtigen Sie Folgendes, wenn Sie REST verwenden:

* Beim Abfragen von Entitäten gibt es ein Limit von 1.000 Entitäten, die gleichzeitig zurückgegeben werden können, da die öffentliche REST-Version 2 Abfrageergebnisse auf 1.000 Ergebnisse begrenzt. Sie müssen **Skip** und **Take** (.NET) bzw. **top** (REST) wie in [diesem .NET-Beispiel](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) und diesem [REST-API-Beispiel](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities) beschrieben verwenden. 
* Wenn Sie JSON verwenden und angeben, dass das Schlüsselwort **__metadata** in der Anforderung verwendet werden soll (z. B. für Verweise auf ein verknüpftes Objekt), MÜSSEN Sie den **Accept**-Header auf das [ausführliche JSON-Format](https://www.odata.org/documentation/odata-version-3-0/json-verbose-format/) festlegen (siehe folgendes Beispiel). OData versteht die **__metadata**-Eigenschaft in der Anforderung nur, wenn Sie das ausführliche Format verwenden.  
  
        POST https://media.windows.net/API/Jobs HTTP/1.1
        Content-Type: application/json;odata=verbose
        Accept: application/json;odata=verbose
        DataServiceVersion: 3.0
        MaxDataServiceVersion: 3.0
        x-ms-version: 2.17
        Authorization: Bearer <ENCODED JWT TOKEN> 
        Host: media.windows.net
  
        {
            "Name" : "NewTestJob", 
            "InputMediaAssets" : 
                [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aba5356eb-30ff-4dc6-9e5a-41e4223540e7')"}}]
        . . . 

## <a name="standard-http-request-headers-supported-by-media-services"></a>Von Media Services unterstützte standardmäßige HTTP-Anforderungsheader
Für jeden Media Services-Aufruf müssen Sie eine Reihe obligatorischer Header in Ihre Anforderung einschließen. Darüber hinaus stehen verschiedene optionale Header zur Auswahl. In der folgenden Tabelle sind die erforderlichen Header aufgeführt:

| Header | Type | Wert |
| --- | --- | --- |
| Autorisierung |Bearer |Das Bearer-Token ist der einzig zulässige Autorisierungsmechanismus. Der Wert muss außerdem das von Azure Active Directory bereitgestellte Zugriffstoken enthalten. |
| x-ms-version |Decimal |2.17 (oder die neueste Version)|
| DataServiceVersion |Decimal |3.0 |
| MaxDataServiceVersion |Decimal |3.0 |

> [!NOTE]
> Da Media Services OData verwendet, um seine REST-APIs verfügbar zu machen, sollten der DataServiceVersion- Header und der MaxDataServiceVersion-Header in alle Anforderungen eingeschlossen werden. Falls dies nicht geschieht, geht Media Services aktuell davon aus, dass der DataServiceVersion-Wert 3.0 verwendet wird.
> 
> 

Im Folgenden finden Sie eine Reihe optionaler Header:

| Header | Type | Wert |
| --- | --- | --- |
| Date |RFC 1123-Datum |Zeitstempel der Anforderung |
| Accept |Content-Typ |Der angeforderte Inhaltstyp für die Antwort, z. B.:<p> -application/json;odata=verbose<p> - application/atom+xml<p> Antworten können unterschiedliche Inhaltstypen aufweisen, z. B. einen Blobabruf, bei dem eine erfolgreiche Antwort den Blobdatenstrom als Nutzlast enthält. |
| Accept-Encoding |Gzip, deflate |GZIP- und DEFLATE-Codierung, falls zutreffend. Hinweis: Bei großen Ressourcen kann Media Services diesen Header ignorieren und unkomprimierte Daten zurückgeben. |
| Accept-Language |„en“, „es“ usw. |Gibt die bevorzugte Sprache für die Antwort an. |
| Accept-Charset |Charset-Typ, z. B. „UTF-8“ |Der Standardwert ist UTF-8. |
| X-HTTP-Method |HTTP-Methode |Ermöglicht Clients oder Firewalls, die keine HTTP-Methoden wie PUT oder DELETE unterstützen, die Verwendung dieser Methoden über einen getunnelten GET-Aufruf. |
| Content-Typ |Content-Typ |Der Inhaltstyp des Anforderungstexts in PUT- oder POST-Anforderungen. |
| client-request-id |string |Ein vom Aufrufer definierter Wert, der die angegebene Anforderung identifiziert. Falls angegeben, wird dieser Wert in die Antwortnachricht eingeschlossen, um die Anforderung zuzuordnen. <p><p>**Wichtig**<p>Werte müssen auf 2.096 Bytes (2 KB) begrenzt sein. |

## <a name="standard-http-response-headers-supported-by-media-services"></a>Von Media Services unterstützte standardmäßige HTTP-Antwortheader
Im Folgenden lernen Sie einen Satz von Headern kennen, die je nach der angeforderten Ressource und beabsichtigten Aktion zurückgegeben werden können.

| Header | Type | Wert |
| --- | --- | --- |
| request-id |string |Ein eindeutiger, vom Dienst generierter Bezeichner für den aktuellen Vorgang. |
| client-request-id |string |Ein Bezeichner, der vom Aufrufer in der ursprünglichen Anforderung angegeben wird, sofern vorhanden. |
| Date |RFC 1123-Datum |Datum/Uhrzeit, zu dem die Anforderung verarbeitet wurde. |
| Content-Typ |Variabel |Der Inhaltstyp des Antworttexts. |
| Content-Encoding |Variabel |GZIP oder DEFLATE, je nachdem, welche Codierung geeignet ist. |

## <a name="standard-http-verbs-supported-by-media-services"></a>Von Media Services unterstützte standardmäßige HTTP-Verben
Im Folgenden finden eine vollständige Liste der HTTP-Verben, die für HTTP-Anforderungen verwendet werden können:

| Verb | BESCHREIBUNG |
| --- | --- |
| GET |Gibt den aktuellen Wert eines Objekts zurück. |
| POST |Erstellt ein Objekt auf Grundlage der bereitgestellten Daten oder sendet einen Befehl. |
| PUT |Ersetzt ein Objekt oder erstellt ein benanntes Objekt (falls zutreffend). |
| DELETE |Löscht ein Objekt. |
| MERGE |Aktualisiert ein vorhandenes Objekt anhand von Änderungen der benannten Eigenschaft. |
| HEAD |Gibt die Metadaten eines Objekts für eine GET-Antwort zurück. |

## <a name="discover-and-browse-the-media-services-entity-model"></a>Ermitteln und Durchsuchen des Media Services-Entitätsmodells
Um Media Services-Entitäten schnell aufzufinden, können Sie den $metadata-Vorgang verwenden. Dadurch können Sie alle gültigen Entitätstypen, Entitätseigenschaften, Zuordnungen, Funktionen, Aktionen usw. abrufen. Durch Hinzufügen des $metadata-Vorgangs am Ende des Endpunkts der Media Services REST-API können Sie auf diesen Ermittlungsdienst zugreifen.

 /api/$metadata.

Sie sollten "?api-version=2.x" an das Ende des URIs anhängen, wenn Sie die Metadaten in einem Browser anzeigen möchten. Andernfalls sollte der x-ms-version-Header nicht in die Anforderung eingeschlossen werden.

## <a name="authenticate-with-media-services-rest-using-azure-active-directory"></a>Authentifizieren mit Media Services REST mithilfe von Azure Active Directory

Die Authentifizierung bei der REST-API wird über Azure Active Directory (AD) durchgeführt.
Weitere Informationen darüber, wie Sie die erforderlichen Authentifizierungsdetails für Ihr Media Services-Konto vom Azure-Portal erhalten, finden Sie unter [Zugreifen auf die Azure Media Services-API per Azure AD-Authentifizierung](media-services-use-aad-auth-to-access-ams-api.md).

Weitere Informationen zum Schreiben von Code, der über die Azure AD-Authentifizierung eine Verbindung zur REST-API herstellt, finden Sie im Artikel [Verwenden der Azure AD-Authentifizierung zum Zugreifen auf die Azure Media Services-API per REST](media-services-rest-connect-with-aad.md).

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zur Verwendung der Azure AD-Authentifizierung mit der Media Services REST-API finden Sie unter [Verwenden der Azure AD-Authentifizierung zum Zugreifen auf die Azure Media Services-API per REST](media-services-rest-connect-with-aad.md).

## <a name="media-services-learning-paths"></a>Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

