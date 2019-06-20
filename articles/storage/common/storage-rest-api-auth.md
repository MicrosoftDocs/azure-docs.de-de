---
title: Aufrufen der REST-API-Vorgänge von Azure Storage-Diensten (einschließlich Authentifizierung) | Microsoft-Dokumentation
description: Aufrufen der REST-API-Vorgänge von Azure Storage-Diensten (einschließlich Authentifizierung)
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 38a120747734cbe4af8804a3e7596fc11a2c2eb3
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306660"
---
# <a name="using-the-azure-storage-rest-api"></a>Verwenden der Azure Storage-REST-API

In diesem Artikel wird veranschaulicht, wie Sie die REST-APIs des Blob Storage-Diensts verwenden und sich für das Aufrufen des Diensts authentifizieren. Er wurde aus der Sicht eines Entwicklers geschrieben, der nichts über REST und das Durchführen eines REST-Aufrufs weiß. Wir sehen uns die Referenzdokumentation für einen REST-Aufruf an und finden heraus, wie die Übersetzung in den eigentlichen REST-Aufruf durchgeführt wird. Es wird beispielsweise folgende Frage beantwortet: Wo gehören die Felder hin? Nachdem Sie sich über das Einrichten eines REST-Aufrufs informiert haben, können Sie dieses Wissen nutzen, um andere REST-APIs des Storage-Diensts zu verwenden.

## <a name="prerequisites"></a>Voraussetzungen 

Die Anwendung listet die Container im Blobspeicher für ein Speicherkonto auf. Sie benötigen Folgendes, um den Code in diesem Artikel ausprobieren zu können: 

* Installieren Sie [Visual Studio 2019](https://www.visualstudio.com/visual-studio-homepage-vs.aspx) mit der folgenden Workload:
    - Azure-Entwicklung

* Ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

* Ein allgemeines Speicherkonto. Falls Sie noch kein Speicherkonto haben, lesen Sie [Erstellen eines Speicherkontos](storage-quickstart-create-account.md).

* Mit dem Beispiel in diesem Artikel wird veranschaulicht, wie Sie die Container in einem Speicherkonto auflisten. Fügen Sie zum Anzeigen der Ausgabe dem Blobspeicher im Speicherkonto einige Container hinzu, bevor Sie beginnen.

## <a name="download-the-sample-application"></a>Herunterladen der Beispielanwendung

Die Beispielanwendung ist eine Konsolenanwendung in C#.

Verwenden Sie [Git](https://git-scm.com/), um eine Kopie der Anwendung in Ihre Entwicklungsumgebung herunterzuladen. 

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-rest-api-with-auth.git
```

Mit diesem Befehl wird das Repository in Ihren lokalen Git-Ordner geklont. Suchen Sie zum Öffnen der Visual Studio-Projektmappe nach dem Ordner „storage-dotnet-rest-api-with-auth“, und öffnen Sie ihn. Doppelklicken Sie anschließend auf „StorageRestApiAuth.sln“. 

## <a name="what-is-rest"></a>Was ist REST?

REST steht für *Representational State Transfer*. Eine ausführliche Definition finden Sie bei [Wikipedia](https://en.wikipedia.org/wiki/Representational_state_transfer).

REST ist im Wesentlichen eine Architektur, die Sie nutzen können, wenn Sie APIs aufrufen oder APIs für das Aufrufen zur Verfügung stellen. Sie ist unabhängig von den Geschehnissen auf beiden Seiten und von der weiteren Software, die beim Senden oder Empfangen der REST-Aufrufe verwendet wird. Sie können eine Anwendung schreiben, die auf einem Mac-, Windows- oder Linux-Computer, Android-Smartphone oder -Tablet, iPhone, iPod oder einer Website ausgeführt wird, und für alle Plattformen die gleiche REST-API verwenden. Daten können in ein- oder ausgehender Richtung übergeben werden, wenn die REST-API aufgerufen wird. Für die REST-API spielt es keine Rolle, von welcher Plattform aus sie aufgerufen wird. Wichtig sind die in der Anforderung übergebenen Informationen und die in der Antwort bereitgestellten Daten.

Es ist nützlich, wenn Sie sich mit REST auskennen. Das Azure-Produktteam veröffentlicht ständig neue Features. In vielen Fällen sind die neuen Features über die REST-Schnittstelle zugänglich. In einigen Fällen werden die Features jedoch noch nicht in **allen** Speicherclientbibliotheken oder auf der Benutzeroberfläche (z.B. Azure-Portal) angezeigt. Wenn Sie immer die aktuellsten und besten Funktionen nutzen möchten, ist das Erlernen von REST eine Voraussetzung dafür. Falls Sie Ihre eigene Bibliothek schreiben möchten, um mit Azure Storage zu interagieren, oder wenn Sie per Programmiersprache ohne SDK oder Speicherclientbibliothek auf Azure Storage zugreifen möchten, können Sie die REST-API verwenden.

## <a name="about-the-sample-application"></a>Infos zur Beispielanwendung

Die Beispielanwendung listet die Container in einem Speicherkonto auf. Wenn Sie verstanden haben, wie die Informationen in der REST-API-Dokumentation mit Ihrem tatsächlichen Code korrelieren, sind andere REST-Aufrufe einfacher nachzuvollziehen. 

Wenn Sie sich die [Blob-Dienst-REST-API](/rest/api/storageservices/Blob-Service-REST-API) ansehen, werden alle Vorgänge angezeigt, die Sie für Blobspeicher durchführen können. Die Speicherclientbibliotheken dienen als Wrapper für die REST-APIs. Sie erleichtern Ihnen das Zugreifen auf den Speicher, ohne dass die REST-APIs direkt verwendet werden müssen. Wie oben erwähnt, kann es vorkommen, dass Sie anstelle einer Speicherclientbibliothek die REST-API nutzen möchten.

## <a name="rest-api-reference-list-containers-api"></a>REST-API-Referenz: API zum Auflisten von Containern

Sehen Sie sich die Seite in der REST-API-Referenz für den [ListContainers](/rest/api/storageservices/List-Containers2)-Vorgang an. Anhand dieser Informationen können Sie nachvollziehen, woher einige der Felder in Anforderung und Antwort stammen.

**Anforderungsmethode**: GET. Dieses Verb ist die HTTP-Methode, die Sie als Eigenschaft des Anforderungsobjekts angeben. Andere Werte für dieses Verb sind HEAD, PUT und DELETE – je nach aufgerufener API.

**Anforderungs-URI**: https://myaccount.blob.core.windows.net/?comp=list. Dieser URI wird aus dem Blob Storage-Kontoendpunkt `http://myaccount.blob.core.windows.net` und der Ressourcenzeichenfolge `/?comp=list` erstellt.

[URI-Parameter](/rest/api/storageservices/List-Containers2#uri-parameters): Es sind zusätzliche Abfrageparameter vorhanden, die Sie beim Aufrufen von ListContainers verwenden können. Zwei dieser Parameter sind *timeout* für den Aufruf (in Sekunden) und *prefix* für die Filterung.

Ein weiterer hilfreicher Parameter ist *maxresults:* . Wenn mehr Container als unter diesem Wert angegeben verfügbar sind, enthält der Antworttext ein *NextMarker*-Element, mit dem der nächste Container angegeben wird, der bei der nächsten Anforderung zurückgegeben wird. Zur Verwendung dieses Features geben Sie den Wert von *NextMarker* als Parameter *marker* im URI an, wenn Sie die nächste Anforderung senden. Bei Verwendung dieses Features ist dies analog zum Blättern durch die Ergebnisse. 

Falls Sie zusätzliche Parameter verwenden möchten, können Sie sie an die Ressourcenzeichenfolge mit dem Wert anfügen. Beispiel:

```
/?comp=list&timeout=60&maxresults=100
```

[Anforderungsheader](/rest/api/storageservices/List-Containers2#request-headers) **:** In diesem Abschnitt werden die erforderlichen und optionalen Anforderungsheader aufgeführt. Drei Header sind erforderlich: ein *Authorization*-Header, *x-ms-date* (enthält die UTC-Zeit für die Anforderung) und *x-ms-version* (gibt die Version der zu verwendenden REST-API an). Das Einfügen von *x-ms-client-request-id* in die Header ist optional. Sie können den Wert für dieses Feld beliebig festlegen. Er wird in die Speicheranalyseprotokolle geschrieben, wenn die Protokollierung aktiviert ist.

[Anforderungstext](/rest/api/storageservices/List-Containers2#request-body) **:** Für ListContainers ist kein Anforderungstext vorhanden. Der Anforderungstext wird für alle PUT-Vorgänge verwendet, wenn Blobs hochgeladen werden. Außerdem wird das SetContainerAccessPolicy-Element verwendet, mit dem Sie eine XML-Liste mit gespeicherten Zugriffsrichtlinien senden können, die angewendet werden sollen. Gespeicherte Zugriffsrichtlinien werden im Artikel [Verwenden von Shared Access Signatures (SAS)](storage-dotnet-shared-access-signature-part-1.md) beschrieben.

[Antwortstatuscode](/rest/api/storageservices/List-Containers2#status-code) **:** Enthält Informationen zu allen Statuscodes, die Sie kennen müssen. In diesem Beispiel bedeutet der HTTP-Statuscode 200, dass alles in Ordnung ist. Eine vollständige Liste mit HTTP-Statuscodes finden Sie unter [Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) (Statuscodedefinitionen). Informationen zu spezifischen Fehlercodes der Storage-REST-APIs finden Sie unter [Bekannte REST API-Fehlercodes](/rest/api/storageservices/common-rest-api-error-codes).

[Antwortheader](/rest/api/storageservices/List-Containers2#response-headers) **:** Hierzu gehören *Content Type*, *x-ms-request-id* (die übergebene Anforderungs-ID), *x-ms-version* (gibt die Version des verwendeten Blob-Diensts an) und *Date* (UTC-Zeitpunkt der Anforderung).

[Antworttext](/rest/api/storageservices/List-Containers2#response-body): Dieses Feld ist eine XML-Struktur, über die die angeforderten Daten bereitgestellt werden. In diesem Beispiel umfasst die Antwort eine Liste mit Containern und ihren Eigenschaften.

## <a name="creating-the-rest-request"></a>Erstellen der REST-Anforderung

Zunächst einige kurze Anmerkungen: Verwenden Sie bei der Ausführung in der Produktion aus Sicherheitsgründen nicht HTTP, sondern immer HTTPS. In dieser Übung empfiehlt sich die Nutzung von HTTP, damit Sie die Anforderungs- und Antwortdaten anzeigen können. Zum Anzeigen der Anforderungs- und Antwortinformationen in den eigentlichen REST-Aufrufen können Sie [Fiddler](https://www.telerik.com/fiddler) oder eine ähnliche Anwendung herunterladen. In der Visual Studio-Projektmappe sind der Name und der Schlüssel des Speicherkontos in der Klasse hartcodiert. Die ListContainersAsyncREST-Methode übergibt den Speicherkontonamen und -schlüssel an die Methoden, die zum Erstellen der verschiedenen Komponenten einer REST-Anforderung verwendet werden. In einer echten Anwendung befinden sich der Speicherkontoname und -schlüssel in einer Konfigurationsdatei oder in Umgebungsvariablen oder werden aus einem Azure Key Vault abgerufen.

In unserem Beispielprojekt befindet sich der Code zum Erstellen des Autorisierungsheaders in einer separaten Klasse. Die Idee dahinter ist, dass Sie die gesamte Klasse Ihrer eigenen Projektmappe hinzufügen und unverändert verwenden können. Der Code des Autorisierungsheaders funktioniert für die meisten REST-API-Aufrufe für Azure Storage.

Navigieren Sie zum Erstellen der Anforderung, bei der es sich um ein HttpRequestMessage-Objekt handelt, zu ListContainersAsyncREST in „Program.cs“. Die Schritte zum Erstellen der Anforderung sind: 

* Erstellen Sie den URI, der zum Aufrufen des Diensts verwendet werden soll. 
* Erstellen Sie das HttpRequestMessage-Objekt, und legen Sie die Nutzlast fest. Die Nutzlast ist für ListContainersAsyncREST null, weil wir nichts übergeben.
* Fügen Sie die Anforderungsheader für „x-ms-date“ und „x-ms-version“ hinzu.
* Rufen Sie den Autorisierungsheader ab, und fügen Sie ihn hinzu.

Sie benötigen diese grundlegenden Informationen: 

*  Für ListContainers wird `GET` als **method** (Methode) verwendet. Dieser Wert wird beim Instanziieren der Anforderung festgelegt. 
*  Die **resource** (Ressource) ist der Abfrageteil des URI, mit dem angegeben wird, welche API aufgerufen wird. Der Wert lautet `/?comp=list`. Wie bereits erwähnt, befindet sich die Ressource auf der Seite der Referenzdokumentation, die die Informationen zur [ListContainers-API](/rest/api/storageservices/List-Containers2) enthält.
*  Der URI wird erstellt, indem der Blob-Dienst-Endpunkt für dieses Speicherkonto erstellt und die Ressource verkettet wird. Der Wert für **request URI** (Anforderungs-URI) ist dann `http://contosorest.blob.core.windows.net/?comp=list`.
*  Für ListContainers ist **requestBody** null, und es sind keine zusätzlichen **Header** vorhanden.

Unterschiedliche APIs können über andere zu übergebende Parameter verfügen, z.B. *ifMatch*. Ein Beispiel für die Verwendung von ifMatch ist das Aufrufen von PutBlob. In diesem Fall legen Sie ifMatch auf ein ETag fest. Das Blob wird dann nur aktualisiert, wenn das von Ihnen angegebene ETag mit dem aktuellen ETag des Blobs übereinstimmt. Wenn eine andere Person das Blob seit dem Abruf des ETags aktualisiert hat, werden diese Änderungen nicht überschrieben. 

Legen Sie zuerst `uri` und `payload` fest. 

```csharp
// Construct the URI. This will look like this:
//   https://myaccount.blob.core.windows.net/resource
String uri = string.Format("http://{0}.blob.core.windows.net?comp=list", storageAccountName);

// Set this to whatever payload you desire. Ours is null because 
//   we're not passing anything in.
Byte[] requestPayload = null;
```

Instanziieren Sie als Nächstes die Anforderung, indem Sie die Methode auf `GET` festlegen und den URI angeben.

```csharp 
//Instantiate the request message with a null payload.
using (var httpRequestMessage = new HttpRequestMessage(HttpMethod.Get, uri)
{ Content = (requestPayload == null) ? null : new ByteArrayContent(requestPayload) })
{
```

Fügen Sie die Anforderungsheader für „x-ms-date“ und „x-ms-version“ hinzu. An dieser Stelle im Code fügen Sie auch alle zusätzlichen Anforderungsheader hinzu, die für den Aufruf erforderlich sind. In diesem Beispiel sind keine zusätzlichen Header vorhanden. Ein Beispiel für eine API, von der zusätzliche Header übergeben werden, ist SetContainerACL. Für Blobspeicher wird ein Header mit dem Namen „x-ms-blob-public-access“ und der Wert für die Zugriffsebene hinzugefügt.

```csharp
    // Add the request headers for x-ms-date and x-ms-version.
    DateTime now = DateTime.UtcNow;
    httpRequestMessage.Headers.Add("x-ms-date", now.ToString("R", CultureInfo.InvariantCulture));
    httpRequestMessage.Headers.Add("x-ms-version", "2017-07-29");
    // If you need any additional headers, add them here before creating
    //   the authorization header. 
```

Rufen Sie die Methode auf, mit der der Autorisierungsheader erstellt wird, und fügen Sie sie den Anforderungsheadern hinzu. Das Erstellen des Autorisierungsheaders wird später in diesem Artikel beschrieben. Der Methodenname lautet GetAuthorizationHeader, der in diesem Codeausschnitt zu sehen ist:

```csharp
    // Get the authorization header and add it.
    httpRequestMessage.Headers.Authorization = AzureStorageAuthenticationHelper.GetAuthorizationHeader(
        storageAccountName, storageAccountKey, now, httpRequestMessage);
```

An diesem Punkt enthält `httpRequestMessage` die vollständige REST-Anforderung mit den Autorisierungsheadern. 

## <a name="call-the-rest-api-with-the-request"></a>Aufrufen der REST-API mit der Anforderung

Da Sie jetzt über die Anforderung verfügen, können Sie SendAsync zum Senden der REST-Anforderung aufrufen. SendAsync ruft die API auf und erhält die Antwort zurück. Sehen Sie sich den StatusCode (200 ist „OK“) der Antwort an, und analysieren Sie anschließend die Antwort. In diesem Fall erhalten Sie eine XML-Liste mit Containern. Wir sehen uns nun den Code zum Aufrufen der GetRESTRequest-Methode für das Erstellen der Anforderung, Ausführen der Anforderung und anschließende Prüfen der Antwort auf die Liste mit den Containern an.

```csharp 
    // Send the request.
    using (HttpResponseMessage httpResponseMessage = 
      await new HttpClient().SendAsync(httpRequestMessage, cancellationToken))
    {
        // If successful (status code = 200), 
        //   parse the XML response for the container names.
        if (httpResponseMessage.StatusCode == HttpStatusCode.OK)
        {
            String xmlString = await httpResponseMessage.Content.ReadAsStringAsync();
            XElement x = XElement.Parse(xmlString);
            foreach (XElement container in x.Element("Containers").Elements("Container"))
            {
                Console.WriteLine("Container name = {0}", container.Element("Name").Value);
            }
        }
    }
}
```

Wenn Sie beim Aufrufen von SendAsync beispielsweise einen Network Sniffer wie [Fiddler](https://www.telerik.com/fiddler) ausführen, können Sie die Informationen zur Anforderung und zur Antwort anzeigen. Wir sehen uns dies nun genauer an. Der Name des Speicherkontos lautet *contosorest*.

**Anforderung:**

```
GET /?comp=list HTTP/1.1
```

**Anforderungsheader:**

```
x-ms-date: Thu, 16 Nov 2017 23:34:04 GMT
x-ms-version: 2014-02-14
Authorization: SharedKey contosorest:1dVlYJWWJAOSHTCPGiwdX1rOS8B4fenYP/VrU0LfzQk=
Host: contosorest.blob.core.windows.net
Connection: Keep-Alive
```

**Nach der Ausführung zurückgegebener Statuscode bzw. Antwortheader:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 3e889876-001e-0039-6a3a-5f4396000000
x-ms-version: 2017-07-29
Date: Fri, 17 Nov 2017 00:23:42 GMT
Content-Length: 1511
```

**Antworttext (XML):** Für ListContainers wird hier die Liste der Container mit den zugehörigen Eigenschaften angezeigt.

```xml  
<?xml version="1.0" encoding="utf-8"?>
<EnumerationResults 
  ServiceEndpoint="http://contosorest.blob.core.windows.net/">
  <Containers>
    <Container>
      <Name>container-1</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:39:48 GMT</Last-Modified>
        <Etag>"0x8D46CBD5A7C301D"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-2</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:40:50 GMT</Last-Modified>
        <Etag>"0x8D46CBD7F49E9BD"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-3</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:41:10 GMT</Last-Modified>
        <Etag>"0x8D46CBD8B243D68"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-4</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:41:25 GMT</Last-Modified>
        <Etag>"0x8D46CBD93FED46F"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
        </Properties>
      </Container>
      <Container>
        <Name>container-5</Name>
        <Properties>
          <Last-Modified>Thu, 16 Mar 2017 22:41:39 GMT</Last-Modified>
          <Etag>"0x8D46CBD9C762815"</Etag>
          <LeaseStatus>unlocked</LeaseStatus>
          <LeaseState>available</LeaseState>
        </Properties>
      </Container>
  </Containers>
  <NextMarker />
</EnumerationResults>
```

Nachdem Sie nun mit dem Erstellen der Anforderung, dem Aufrufen des Diensts und dem Analysieren der Ergebnisse vertraut sind, geht es mit der Erstellung des Autorisierungsheaders weiter. Das Erstellen des Headers ist kompliziert, aber es gibt auch eine gute Nachricht: Nachdem der Code funktioniert, kann er für alle REST-APIs des Storage-Diensts verwendet werden.

## <a name="creating-the-authorization-header"></a>Erstellen des Autorisierungsheaders

> [!TIP]
> Azure Storage unterstützt jetzt die Integration von Azure Active Directory (Azure AD) für Blobs und Warteschlangen. Azure AD bietet eine erheblich einfachere Benutzeroberfläche für das Autorisieren einer Anforderung an Azure Storage. Weitere Informationen zur Verwendung von Azure AD zum Autorisieren von REST-Vorgängen finden Sie unter [Authentifizieren mit Azure Active Directory](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory). Eine Übersicht über die Azure AD-Integration in Azure Storage finden Sie unter [Authentifizieren des Zugriffs auf Azure Storage mit Azure Active Directory](storage-auth-aad.md).

Es gibt einen Artikel, in dem konzeptuell (ohne Code) beschrieben wird, wie Sie die [Authentifizierung für Azure Storage-Dienste](/rest/api/storageservices/Authorization-for-the-Azure-Storage-Services) durchführen.
Wir fassen hier die wesentlichen Punkte des Artikels zusammen und zeigen den Code.

Verwenden Sie zuerst eine Authentifizierung mit gemeinsam verwendetem Schlüssel. Der Autorisierungsheader hat das folgende Format:

```  
Authorization="SharedKey <storage account name>:<signature>"  
```

Das Signaturfeld ist ein HMAC (Hash-based Message Authentication Code), der aus der Anforderung erstellt und mit dem SHA256-Algorithmus berechnet und anschließend per Base64-Codierung codiert wird. So weit alles klar? (Bleiben Sie dran: Der Ausdruck *canonicalized* (vereinheitlicht) ist noch gar nicht gefallen.)

In diesem Codeausschnitt wird das Format der Signaturzeichenfolge für den gemeinsam verwendeten Schlüssel veranschaulicht:

```csharp  
StringToSign = VERB + "\n" +  
               Content-Encoding + "\n" +  
               Content-Language + "\n" +  
               Content-Length + "\n" +  
               Content-MD5 + "\n" +  
               Content-Type + "\n" +  
               Date + "\n" +  
               If-Modified-Since + "\n" +  
               If-Match + "\n" +  
               If-None-Match + "\n" +  
               If-Unmodified-Since + "\n" +  
               Range + "\n" +  
               CanonicalizedHeaders +  
               CanonicalizedResource;  
```

Die meisten dieser Felder werden nur selten verwendet. Für Blobspeicher geben Sie „VERB“, „md5“, „content length“, „Canonicalized Headers“ und „Canonicalized Resource“ an. Sie können die anderen Felder leer lassen (mit Angabe von `\n`, damit klar ist, dass sie leer sind).

Was sind CanonicalizedHeaders und CanonicalizedResource? Gute Frage. Was bedeutet „canonicalized“ eigentlich? In Microsoft Word wird dieser Begriff gar nicht als Wort erkannt. [Wikipedia sagt über Kanonisierung](https://en.wikipedia.org/wiki/Canonicalization) Folgendes: *In computer science, canonicalization (sometimes standardization or normalization) is a process for converting data that has more than one possible representation into a "standard", "normal", or canonical form.* (In der Computerwissenschaft ist „Canonicalization“ (Deutsch: Vereinheitlichung, auch als Standardisierung oder Normalisierung bezeichnet) ein Prozess zum Konvertieren von Daten, für die mehr als eine Art der Darstellung möglich ist, in eine „Standardform“, „Normalform“ oder „kanonische Form“.) Vereinfacht ausgedrückt heißt dies, dass die Elemente einer Liste (bei vereinheitlichten Headern also beispielsweise Header) in ein erforderliches Format standardisiert werden. Microsoft hat sich quasi für ein Format entschieden, und Sie müssen es einhalten.

Wir beginnen mit diesen beiden vereinheitlichten Feldern, da sie erforderlich sind, um den Autorisierungsheader zu erstellen.

**Vereinheitlichte Header**

Rufen Sie zum Erstellen dieses Werts die Header ab, die mit „x-ms-“ beginnen, und sortieren Sie sie. Formatieren Sie sie anschließend in eine Reihe von `[key:value\n]`-Instanzen, die zu einer Zeichenfolge verkettet werden. In diesem Beispiel sehen die vereinheitlichten Header wie folgt aus: 

```
x-ms-date:Fri, 17 Nov 2017 00:44:48 GMT\nx-ms-version:2017-07-29\n
```

Hier ist der Code angegeben, der zum Erstellen dieser Ausgabe verwendet wird:

```csharp 
private static string GetCanonicalizedHeaders(HttpRequestMessage httpRequestMessage)
{
    var headers = from kvp in httpRequestMessage.Headers
        where kvp.Key.StartsWith("x-ms-", StringComparison.OrdinalIgnoreCase)
        orderby kvp.Key
        select new { Key = kvp.Key.ToLowerInvariant(), kvp.Value };

    StringBuilder headersBuilder = new StringBuilder();

    // Create the string in the right format; this is what makes the headers "canonicalized" --
    //   it means put in a standard format. https://en.wikipedia.org/wiki/Canonicalization
    foreach (var kvp in headers)
    {
        headersBuilder.Append(kvp.Key);
        char separator = ':';

        // Get the value for each header, strip out \r\n if found, then append it with the key.
        foreach (string headerValue in kvp.Value)
        {
            string trimmedValue = headerValue.TrimStart().Replace("\r\n", string.Empty);
            headersBuilder.Append(separator).Append(trimmedValue);

            // Set this to a comma; this will only be used
            // if there are multiple values for one of the headers.
            separator = ',';
        }

        headersBuilder.Append("\n");
    }

    return headersBuilder.ToString();
}
```

**Vereinheitlichte Ressource**

Dieser Teil der Signaturzeichenfolge steht für das Speicherkonto, auf das die Anforderung abzielt. Beachten Sie, dass der Anforderungs-URI `<http://contosorest.blob.core.windows.net/?comp=list>` lautet und den tatsächlichen Kontonamen enthält (hier `contosorest`). In diesem Beispiel wird Folgendes zurückgegeben:

```
/contosorest/\ncomp:list
```

Wenn Sie Abfrageparameter verwenden, sind diese ebenfalls in diesem Beispiel enthalten. Hier ist der Code angegeben, mit dem auch zusätzliche Abfrageparameter und Abfrageparameter mit mehreren Werten verarbeitet werden. Denken Sie daran, dass Sie diesen Code für alle REST-APIs erstellen. Sie möchten alle Möglichkeiten berücksichtigen, selbst wenn die ListContainers-Methode nicht alle benötigt.

```csharp 
private static string GetCanonicalizedResource(Uri address, string storageAccountName)
{
    // The absolute path will be "/" because for we're getting a list of containers.
    StringBuilder sb = new StringBuilder("/").Append(storageAccountName).Append(address.AbsolutePath);

    // Address.Query is the resource, such as "?comp=list".
    // This ends up with a NameValueCollection with 1 entry having key=comp, value=list.
    // It will have more entries if you have more query parameters.
    NameValueCollection values = HttpUtility.ParseQueryString(address.Query);

    foreach (var item in values.AllKeys.OrderBy(k => k))
    {
        sb.Append('\n').Append(item).Append(':').Append(values[item]);
    }

    return sb.ToString();
}
```

Nachdem die vereinheitlichten Zeichenfolgen nun festgelegt wurden, sehen wir uns an, wie der eigentliche Autorisierungsheader erstellt wird. Zunächst erstellen Sie eine Zeichenfolge mit der Nachrichtensignatur im Format StringToSign, das weiter oben in diesem Artikel beschrieben wurde. Dieses Konzept ist einfacher zu erklären, indem Kommentare im Code verwendet werden. Dies ist also die endgültige Methode, mit der der Autorisierungsheader zurückgegeben wird:

```csharp
internal static AuthenticationHeaderValue GetAuthorizationHeader(
    string storageAccountName, string storageAccountKey, DateTime now,
    HttpRequestMessage httpRequestMessage, string ifMatch = "", string md5 = "")
{
    // This is the raw representation of the message signature.
    HttpMethod method = httpRequestMessage.Method;
    String MessageSignature = String.Format("{0}\n\n\n{1}\n{5}\n\n\n\n{2}\n\n\n\n{3}{4}",
                method.ToString(),
                (method == HttpMethod.Get || method == HttpMethod.Head) ? String.Empty
                  : httpRequestMessage.Content.Headers.ContentLength.ToString(),
                ifMatch,
                GetCanonicalizedHeaders(httpRequestMessage),
                GetCanonicalizedResource(httpRequestMessage.RequestUri, storageAccountName),
                md5);

    // Now turn it into a byte array.
    byte[] SignatureBytes = Encoding.UTF8.GetBytes(MessageSignature);

    // Create the HMACSHA256 version of the storage key.
    HMACSHA256 SHA256 = new HMACSHA256(Convert.FromBase64String(storageAccountKey));

    // Compute the hash of the SignatureBytes and convert it to a base64 string.
    string signature = Convert.ToBase64String(SHA256.ComputeHash(SignatureBytes));

    // This is the actual header that will be added to the list of request headers.
    AuthenticationHeaderValue authHV = new AuthenticationHeaderValue("SharedKey",
        storageAccountName + ":" + Convert.ToBase64String(SHA256.ComputeHash(SignatureBytes)));
    return authHV;
}
```

Wenn Sie diesen Code ausführen, sieht die resultierende MessageSignature wie folgt aus:

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 01:07:37 GMT\nx-ms-version:2017-07-29\n/contosorest/\ncomp:list
```

Hier ist der endgültige Wert für AuthorizationHeader angegeben:

```
SharedKey contosorest:Ms5sfwkA8nqTRw7Uury4MPHqM6Rj2nfgbYNvUKOa67w=
```

Der AuthorizationHeader ist der letzte Header, der vor dem Posten der Antwort in Anforderungsheadern angeordnet wird.

Dies sind alle Informationen, die zum Zusammenstellen einer Klasse erforderlich sind, mit der Sie eine Anforderung zum Aufrufen der REST-APIs von Storage-Diensten verwenden können.

## <a name="how-about-another-example"></a>Wie wäre es mit einem weiteren Beispiel? 

Wir sehen uns nun an, wie Sie den Code zum Aufrufen von ListBlobs für den Container *container-1* ändern. Dieser Code ist nahezu identisch mit dem Code zum Auflisten von Containern. Die einzigen Unterschiede sind der URI und die Vorgehensweise beim Analysieren der Antwort. 

Wenn Sie sich die Referenzdokumentation für [ListBlobs](/rest/api/storageservices/List-Blobs) ansehen, sehen Sie, dass die Methode *GET* ist und der RequestURI wie folgt lautet:

```
https://myaccount.blob.core.windows.net/container-1?restype=container&comp=list
```

Ändern Sie unter ListContainersAsyncREST den Code, mit dem der URI auf die API für ListBlobs festgelegt wird. Der Containername lautet **container-1**.

```csharp
String uri = 
    string.Format("http://{0}.blob.core.windows.net/container-1?restype=container&comp=list",
      storageAccountName);

```

Ändern Sie anschließend den Code an der Stelle, an der die Antwort verarbeitet wird, damit nicht nach Containern gesucht wird, sondern nach Blobs.

```csharp
foreach (XElement container in x.Element("Blobs").Elements("Blob"))
{
    Console.WriteLine("Blob name = {0}", container.Element("Name").Value);
}
```

Wenn Sie dieses Beispiel ausführen, erhalten Sie beispielsweise folgende Ergebnisse:

**Vereinheitlichte Header:**

```
x-ms-date:Fri, 17 Nov 2017 05:16:48 GMT\nx-ms-version:2017-07-29\n
```

**Vereinheitlichte Ressource:**

```
/contosorest/container-1\ncomp:list\nrestype:container
```

**MessageSignature:**

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 05:16:48 GMT
  \nx-ms-version:2017-07-29\n/contosorest/container-1\ncomp:list\nrestype:container
```

**AuthorizationHeader:**

```
SharedKey contosorest:uzvWZN1WUIv2LYC6e3En10/7EIQJ5X9KtFQqrZkxi6s=
```

Die folgenden Werte stammen aus [Fiddler](https://www.telerik.com/fiddler):

**Anforderung:**

```
GET http://contosorest.blob.core.windows.net/container-1?restype=container&comp=list HTTP/1.1
```

**Anforderungsheader:**

```
x-ms-date: Fri, 17 Nov 2017 05:16:48 GMT
x-ms-version: 2017-07-29
Authorization: SharedKey contosorest:uzvWZN1WUIv2LYC6e3En10/7EIQJ5X9KtFQqrZkxi6s=
Host: contosorest.blob.core.windows.net
Connection: Keep-Alive
```

**Nach der Ausführung zurückgegebener Statuscode bzw. Antwortheader:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 7e9316da-001e-0037-4063-5faf9d000000
x-ms-version: 2017-07-29
Date: Fri, 17 Nov 2017 05:20:21 GMT
Content-Length: 1135
```

**Antworttext (XML):** In dieser XML-Antwort wird die Liste der Blobs und der zugehörigen Eigenschaften angezeigt. 

```xml
<?xml version="1.0" encoding="utf-8"?>
<EnumerationResults 
    ServiceEndpoint="http://contosorest.blob.core.windows.net/" ContainerName="container-1">
    <Blobs>
        <Blob>
            <Name>DogInCatTree.png</Name>
            <Properties><Last-Modified>Fri, 17 Nov 2017 01:41:14 GMT</Last-Modified>
            <Etag>0x8D52D5C4A4C96B0</Etag>
            <Content-Length>419416</Content-Length>
            <Content-Type>image/png</Content-Type>
            <Content-Encoding />
            <Content-Language />
            <Content-MD5 />
            <Cache-Control />
            <Content-Disposition />
            <BlobType>BlockBlob</BlobType>
            <LeaseStatus>unlocked</LeaseStatus>
            <LeaseState>available</LeaseState>
            <ServerEncrypted>true</ServerEncrypted>
            </Properties>
        </Blob>
        <Blob>
            <Name>GuyEyeingOreos.png</Name>
            <Properties>
                <Last-Modified>Fri, 17 Nov 2017 01:41:14 GMT</Last-Modified>
                <Etag>0x8D52D5C4A25A6F6</Etag>
                <Content-Length>167464</Content-Length>
                <Content-Type>image/png</Content-Type>
                <Content-Encoding />
                <Content-Language />
                <Content-MD5 />
                <Cache-Control />
                <Content-Disposition />
                <BlobType>BlockBlob</BlobType>
                <LeaseStatus>unlocked</LeaseStatus>
                <LeaseState>available</LeaseState>
                <ServerEncrypted>true</ServerEncrypted>
            </Properties>
            </Blob>
        </Blobs>
    <NextMarker />
</EnumerationResults>
```

## <a name="summary"></a>Zusammenfassung

In diesem Artikel haben Sie gelernt, wie eine Anforderung an die Blobspeicher-REST-API gesendet wird. Mit der Anforderung können Sie eine Liste der Container oder eine Liste der Blobs in einem Container abrufen. Sie haben erfahren, wie Sie die Autorisierungssignatur für den REST-API-Aufruf erstellen und diese in der REST-Anforderung verwenden. Zuletzt wurde gezeigt, wie Sie die Antwort untersuchen.

## <a name="next-steps"></a>Nächste Schritte

* [REST-API des Blob-Diensts](/rest/api/storageservices/blob-service-rest-api)
* [REST-API des Dateidiensts](/rest/api/storageservices/file-service-rest-api)
* [REST-API des Warteschlangendiensts](/rest/api/storageservices/queue-service-rest-api)
