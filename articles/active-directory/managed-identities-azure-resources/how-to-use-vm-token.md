---
title: Verwenden von verwalteten Identitäten auf einem virtuellen Computer zum Abrufen eines Zugriffstokens – Azure AD
description: Schrittweise Anweisungen und Beispiele zur Verwendung verwalteter Identitäten für Azure-Ressourcen auf einem virtuellen Computer zum Abrufen eines OAuth-Zugriffstokens.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/03/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: bed64df921326ad4d219f934f7a7bc6860bfc7d8
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861900"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-to-acquire-an-access-token"></a>Verwenden von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Azure-Computer zum Abrufen eines Zugriffstokens 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  

Verwaltete Identitäten für Azure-Ressourcen stellen für Azure-Dienste eine automatisch verwaltete Identität in Azure Active Directory bereit. Sie können diese Identität für die Authentifizierung bei jedem Dienst verwenden, der die Azure AD-Authentifizierung unterstützt. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein. 

Dieser Artikel enthält verschiedene Code- und Skriptbeispiele für den Tokenabruf sowie eine Anleitung zu wichtigen Themen, z.B. zur Behandlung bei Tokenablauf und HTTP-Fehlern. 

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Wenn Sie die Azure PowerShell-Beispiele in diesem Artikel verwenden möchten, müssen Sie die neueste Version von [Azure PowerShell](/powershell/azure/install-az-ps) installieren.


> [!IMPORTANT]
> - Bei allen Beispielcodes/-skripts in diesem Artikel wird vorausgesetzt, dass der Client auf einem virtuellen Computer mit verwalteten Identitäten für Azure-Ressourcen ausgeführt wird. Verwenden Sie die Funktion „Verbinden“ für virtuelle Computer im Azure-Portal zum Herstellen einer Remoteverbindung mit Ihrem virtuellen Computer. Weitere Informationen zur Aktivierung von verwalteten Identitäten für Azure-Ressourcen auf einer VM finden Sie unter [Konfigurieren von verwalteten Identitäten für Azure-Ressourcen auf einer VM über das Azure-Portal](qs-configure-portal-windows-vm.md) oder in einem der verwandten Artikel (PowerShell, CLI, Vorlage oder Azure SDK). 

> [!IMPORTANT]
> - Die Sicherheitsgrenze für verwaltete Identitäten für Azure-Ressourcen wird durch die Ressource vorgegeben, auf der sie verwendet werden. Der gesamte Code und alle Skripts, die auf einem virtuellen Computer ausgeführt werden, können Token für die darin verfügbaren verwalteten Identitäten anfordern und abrufen. 

## <a name="overview"></a>Übersicht

Eine Clientanwendung kann ein [App-exklusives Zugriffstoken](../develop/developer-glossary.md#access-token) der verwalteten Identitäten für Azure-Ressourcen für den Zugriff auf eine bestimmte Ressource anfordern. Das Token [basiert auf dem Dienstprinzipal der verwalteten Identitäten für Azure-Ressourcen](overview.md#managed-identity-types). Daher muss sich der Client nicht selbst registrieren, um ein Zugriffstoken unter seinem eigenen Dienstprinzipal abzurufen. Das Token ist geeignet für die Nutzung als Bearertoken in [Dienst-zu-Dienst-Aufrufen, für die Clientanmeldeinformationen benötigt werden](../develop/v2-oauth2-client-creds-grant-flow.md).

| Link | BESCHREIBUNG |
| -------------- | -------------------- |
| [Abrufen eines Tokens über HTTP](#get-a-token-using-http) | Protokolldetails zum Tokenendpunkt für verwaltete Identitäten für Azure-Ressourcen |
| [Abrufen eines Tokens mit der Microsoft.Azure.Services.AppAuthentication-Bibliothek für .NET](#get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net) | Beispiel für die Verwendung der Microsoft.Azure.Services.AppAuthentication-Bibliothek auf einem .NET-Client
| [Abrufen eines Tokens über C#](#get-a-token-using-c) | Beispiel für die Verwendung des REST-Endpunkts für verwaltete Identitäten für Azure-Ressourcen über einen C#-Client |
| [Abrufen eines Tokens mit Java](#get-a-token-using-java) | Beispiel für die Verwendung von verwalteten Identitäten für den REST-Endpunkt von Azure-Ressourcen über einen Java-Client |
| [Abrufen eines Tokens über Go](#get-a-token-using-go) | Beispiel für die Verwendung des REST-Endpunkts für verwaltete Identitäten für Azure-Ressourcen über einen Go-Client |
| [Abrufen eines Tokens über Azure PowerShell](#get-a-token-using-azure-powershell) | Beispiel für die Verwendung des REST-Endpunkts für verwaltete Identitäten für Azure-Ressourcen über einen PowerShell-Client |
| [Abrufen eines Tokens über cURL](#get-a-token-using-curl) | Beispiel für die Verwendung des REST-Endpunkts für verwaltete Identitäten für Azure-Ressourcen über einen Bash/cURL-Client |
| Behandlung der Token-Zwischenspeicherung | Anleitung zur Behandlung abgelaufener Zugriffstoken |
| [Fehlerbehandlung](#error-handling) | Anleitung zur Behandlung von HTTP-Fehlern, die vom Tokenendpunkt für verwaltete Identitäten für Azure-Ressourcen zurückgegeben werden |
| [Ressourcen-IDs für Azure-Dienste](#resource-ids-for-azure-services) | Abrufen von Ressourcen-IDs für unterstützte Azure-Dienste |

## <a name="get-a-token-using-http"></a>Abrufen eines Tokens über HTTP 

Die grundlegende Schnittstelle zum Abrufen eines Zugriffstokens basiert auf REST, sodass sie für alle auf dem virtuellen Computer ausgeführten Clientanwendungen, die HTTP-REST-Aufrufe ausführen können, zur Verfügung steht. Dies ähnelt dem Azure AD-Programmiermodell, aber der Client verwendet einen Endpunkt auf dem virtuellen Computer (und keinen Azure AD-Endpunkt).

Beispielanforderung mit dem Azure-IMDS-Endpunkt (Instance Metadata Service) *(empfohlen)* :

```
GET 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/' HTTP/1.1 Metadata: true
```

| Element | BESCHREIBUNG |
| ------- | ----------- |
| `GET` | Das HTTP-Verb, mit dem angegeben wird, dass Sie Daten vom Endpunkt abrufen möchten. In diesem Fall ist dies ein OAuth-Zugriffstoken. | 
| `http://169.254.169.254/metadata/identity/oauth2/token` | Der Endpunkt für verwaltete Identitäten für Azure-Ressourcen für den Instanzmetadatendienst. |
| `api-version`  | Ein Abfragezeichenfolgenparameter, mit dem die API-Version für den IMDS-Endpunkt angegeben wird. Verwenden Sie API-Version `2018-02-01` oder höher. |
| `resource` | Ein Abfragezeichenfolgenparameter, der den App-ID-URI der Zielressource angibt. Er wird auch im Anspruch `aud` (audience) des ausgestellten Tokens angezeigt. In diesem Beispiel wird ein Token für den Zugriff auf Azure Resource Manager angefordert, das über den App-ID-URI `https://management.azure.com/` verfügt. |
| `Metadata` | Ein HTTP-Anforderungsheader-Feld, das für verwaltete Identitäten für Azure-Ressourcen als Maßnahme gegen SSRF-Angriffe (Server Side Request Forgery) erforderlich ist. Dieser Wert muss auf „true“ (in Kleinbuchstaben) festgelegt werden. |
| `object_id` | (Optional) Ein Abfragezeichenfolgen-Parameter, der den object_id-Wert der verwalteten Identität angibt, für die das Token gelten soll. Erforderlich, wenn Ihr virtueller Computer über mehrere vom Benutzer zugewiesene verwaltete Identitäten verfügt.|
| `client_id` | (Optional) Ein Abfragezeichenfolgen-Parameter, der den client_id-Wert der verwalteten Identität angibt, für die das Token gelten soll. Erforderlich, wenn Ihr virtueller Computer über mehrere vom Benutzer zugewiesene verwaltete Identitäten verfügt.|
| `mi_res_id` | (Optional) Ein Abfragezeichenfolgenparameter, der das Element „mi_res_id“ (Azure-Ressourcen-ID) der verwalteten Identität angibt, für die das Token gelten soll. Erforderlich, wenn Ihr virtueller Computer über mehrere vom Benutzer zugewiesene verwaltete Identitäten verfügt. |

Beispielanforderung mit dem VM-Erweiterungsendpunkt für verwaltete Identitäten für Azure-Ressourcen *(Veraltung geplant für Januar 2019)* :

```http
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Element | BESCHREIBUNG |
| ------- | ----------- |
| `GET` | Das HTTP-Verb, mit dem angegeben wird, dass Sie Daten vom Endpunkt abrufen möchten. In diesem Fall ist dies ein OAuth-Zugriffstoken. | 
| `http://localhost:50342/oauth2/token` | Der Endpunkt für verwaltete Identitäten für Azure-Ressourcen, wobei 50342 der Standardport und konfigurierbar ist. |
| `resource` | Ein Abfragezeichenfolgenparameter, der den App-ID-URI der Zielressource angibt. Er wird auch im Anspruch `aud` (audience) des ausgestellten Tokens angezeigt. In diesem Beispiel wird ein Token für den Zugriff auf Azure Resource Manager angefordert, das über den App-ID-URI `https://management.azure.com/` verfügt. |
| `Metadata` | Ein HTTP-Anforderungsheader-Feld, das für verwaltete Identitäten für Azure-Ressourcen als Maßnahme gegen SSRF-Angriffe (Server Side Request Forgery) erforderlich ist. Dieser Wert muss auf „true“ (in Kleinbuchstaben) festgelegt werden.|
| `object_id` | (Optional) Ein Abfragezeichenfolgen-Parameter, der den object_id-Wert der verwalteten Identität angibt, für die das Token gelten soll. Erforderlich, wenn Ihr virtueller Computer über mehrere vom Benutzer zugewiesene verwaltete Identitäten verfügt.|
| `client_id` | (Optional) Ein Abfragezeichenfolgen-Parameter, der den client_id-Wert der verwalteten Identität angibt, für die das Token gelten soll. Erforderlich, wenn Ihr virtueller Computer über mehrere vom Benutzer zugewiesene verwaltete Identitäten verfügt.|

Beispiel für eine Antwort:

```json
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| Element | BESCHREIBUNG |
| ------- | ----------- |
| `access_token` | Das angeforderte Zugriffstoken. Beim Aufrufen einer geschützten REST-API wird das Token als „Bearertoken“ in das `Authorization`-Anforderungsheader-Feld eingebettet, damit der Aufrufer von der API authentifiziert werden kann. | 
| `refresh_token` | Wird von verwalteten Identitäten für Azure-Ressourcen nicht verwendet. |
| `expires_in` | Der Zeitraum in Sekunden, wie lange das Zugriffstoken ab dem Zeitpunkt der Ausstellung gültig ist, bevor es abläuft. Der Ausstellungszeitpunkt ist im Anspruch `iat` des Tokens zu finden. |
| `expires_on` | Der Zeitpunkt, zu dem das Zugriffstoken abläuft. Das Datum wird als Anzahl von Sekunden ab „1970-01-01T0:0:0Z UTC“ (entspricht dem Anspruch `exp` des Tokens) dargestellt. |
| `not_before` | Der Zeitpunkt, ab dem das Zugriffstoken wirksam ist und akzeptiert werden kann. Das Datum wird als Anzahl von Sekunden ab „1970-01-01T0:0:0Z UTC“ (entspricht dem Anspruch `nbf` des Tokens) dargestellt. |
| `resource` | Die Ressource, für die das Zugriffstoken angefordert wurde (Übereinstimmung mit dem Abfragezeichenfolgenparameter `resource` der Anforderung). |
| `token_type` | Der Typ des Tokens. In diesem Fall ein „Bearerzugriffstoken“, sodass die Ressource Zugriff auf den Bearer dieses Tokens gewähren kann. |

## <a name="get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net"></a>Abrufen eines Tokens mit der Microsoft.Azure.Services.AppAuthentication-Bibliothek für .NET

Bei .NET-Anwendungen und -Funktionen stellt die einfachste Methode für die Arbeit mit verwalteten Identitäten für Azure-Ressourcen das Microsoft.Azure.Services.AppAuthentication-Paket dar. Mithilfe dieser Bibliothek können Sie zudem Ihren Code lokal auf dem Entwicklungscomputer testen. Hierzu verwenden Sie Ihr Benutzerkonto aus Visual Studio, aus der [Azure CLI](/cli/azure) oder der integrierten Active Directory-Authentifizierung. Weitere Informationen zu Optionen für die lokale Entwicklung mit dieser Bibliothek finden Sie in der [Microsoft.Azure.Services.AppAuthentication-Referenz](../../key-vault/general/service-to-service-authentication.md). In diesem Abschnitt werden die ersten Schritte mit der Bibliothek in Ihrem Code erläutert.

1. Fügen Sie Ihrer Anwendung einen Verweis auf die NuGet-Pakete [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) und [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) hinzu.

2.  Fügen Sie Ihrer Anwendung den folgenden Code hinzu:

    ```csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;
    // ...
    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");
    // OR
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
    ```
    
Weitere Informationen zu Microsoft.Azure.Services.AppAuthentication und den zugehörigen Vorgängen finden Sie in der [Microsoft.Azure.Services.AppAuthentication-Referenz](../../key-vault/general/service-to-service-authentication.md) und im [.NET-Beispiel zu App Service und KeyVault mit verwalteten Identitäten für Azure-Ressourcen](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

## <a name="get-a-token-using-c"></a>Abrufen eines Tokens über C#

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net;
using System.Web.Script.Serialization; 

// Build request to acquire managed identities for Azure resources token
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/");
request.Headers["Metadata"] = "true";
request.Method = "GET";

try
{
    // Call /token endpoint
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();

    // Pipe response Stream to a StreamReader, and extract access token
    StreamReader streamResponse = new StreamReader(response.GetResponseStream()); 
    string stringResponse = streamResponse.ReadToEnd();
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    string accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

```

## <a name="get-a-token-using-java"></a>Abrufen eines Tokens mit Java

Verwenden Sie diese [JSON-Bibliothek](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core/2.9.4), um ein Token mithilfe von Java abzurufen.

```Java
import java.io.*;
import java.net.*;
import com.fasterxml.jackson.core.*;
 
class GetMSIToken {
    public static void main(String[] args) throws Exception {
 
        URL msiEndpoint = new URL("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/");
        HttpURLConnection con = (HttpURLConnection) msiEndpoint.openConnection();
        con.setRequestMethod("GET");
        con.setRequestProperty("Metadata", "true");
 
        if (con.getResponseCode()!=200) {
            throw new Exception("Error calling managed identity token endpoint.");
        }
 
        InputStream responseStream = con.getInputStream();
 
        JsonFactory factory = new JsonFactory();
        JsonParser parser = factory.createParser(responseStream);
 
        while(!parser.isClosed()){
            JsonToken jsonToken = parser.nextToken();
 
            if(JsonToken.FIELD_NAME.equals(jsonToken)){
                String fieldName = parser.getCurrentName();
                jsonToken = parser.nextToken();
 
                if("access_token".equals(fieldName)){
                    String accesstoken = parser.getValueAsString();
                    System.out.println("Access Token: " + accesstoken.substring(0,5)+ "..." + accesstoken.substring(accesstoken.length()-5));
                    return;
                }
            }
        }
    }
}
```

## <a name="get-a-token-using-go"></a>Abrufen eines Tokens über Go

```
package main

import (
  "fmt"
  "io/ioutil"
  "net/http"
  "net/url"
  "encoding/json"
)

type responseJson struct {
  AccessToken string `json:"access_token"`
  RefreshToken string `json:"refresh_token"`
  ExpiresIn string `json:"expires_in"`
  ExpiresOn string `json:"expires_on"`
  NotBefore string `json:"not_before"`
  Resource string `json:"resource"`
  TokenType string `json:"token_type"`
}

func main() {
    
    // Create HTTP request for a managed services for Azure resources token to access Azure Resource Manager
    var msi_endpoint *url.URL
    msi_endpoint, err := url.Parse("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01")
    if err != nil {
      fmt.Println("Error creating URL: ", err)
      return 
    }
    msi_parameters := url.Values{}
    msi_parameters.Add("resource", "https://management.azure.com/")
    msi_endpoint.RawQuery = msi_parameters.Encode()
    req, err := http.NewRequest("GET", msi_endpoint.String(), nil)
    if err != nil {
      fmt.Println("Error creating HTTP request: ", err)
      return 
    }
    req.Header.Add("Metadata", "true")

    // Call managed services for Azure resources token endpoint
    client := &http.Client{}
    resp, err := client.Do(req) 
    if err != nil{
      fmt.Println("Error calling token endpoint: ", err)
      return
    }

    // Pull out response body
    responseBytes,err := ioutil.ReadAll(resp.Body)
    defer resp.Body.Close()
    if err != nil {
      fmt.Println("Error reading response body : ", err)
      return
    }

    // Unmarshall response body into struct
    var r responseJson
    err = json.Unmarshal(responseBytes, &r)
    if err != nil {
      fmt.Println("Error unmarshalling the response:", err)
      return
    }

    // Print HTTP response and marshalled response body elements to console
    fmt.Println("Response status:", resp.Status)
    fmt.Println("access_token: ", r.AccessToken)
    fmt.Println("refresh_token: ", r.RefreshToken)
    fmt.Println("expires_in: ", r.ExpiresIn)
    fmt.Println("expires_on: ", r.ExpiresOn)
    fmt.Println("not_before: ", r.NotBefore)
    fmt.Println("resource: ", r.Resource)
    fmt.Println("token_type: ", r.TokenType)
}
```

## <a name="get-a-token-using-azure-powershell"></a>Abrufen eines Tokens über Azure PowerShell

Im folgenden Beispiel wird veranschaulicht, wie Sie den REST-Endpunkt für verwaltete Identitäten für Azure-Ressourcen über einen PowerShell-Client für Folgendes verwenden:

1. Abrufen eines Zugriffstokens
2. Verwenden des Zugriffstokens, um eine Azure Resource Manager-REST-API aufzurufen und Informationen zum virtuellen Computer abzurufen Achten Sie darauf, dass Sie `<SUBSCRIPTION-ID>`, `<RESOURCE-GROUP>` und `<VM-NAME>` jeweils durch Ihre Abonnement-ID, den Namen der Ressourcengruppe und den Namen des virtuellen Computers ersetzen.

```azurepowershell
Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Headers @{Metadata="true"}
```

Beispiel zum Extrahieren des Zugriffstokens aus der Antwort:
```azurepowershell
# Get an access token for managed identities for Azure resources
$response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' `
                              -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token
echo "The managed identities for Azure resources access token is $access_token"

# Use the access token to get resource information for the VM
$vmInfoRest = (Invoke-WebRequest -Uri 'https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Compute/virtualMachines/<VM-NAME>?api-version=2017-12-01' -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $access_token"}).content
echo "JSON returned from call to get VM info:"
echo $vmInfoRest

```

## <a name="get-a-token-using-curl"></a>Abrufen eines Tokens über cURL

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s
```


Beispiel zum Extrahieren des Zugriffstokens aus der Antwort:

```bash
response=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The managed identities for Azure resources access token is $access_token
```

## <a name="token-caching"></a>Zwischenspeichern von Tokens

Zwar speichert das verwendete Subsystem für verwaltete Identitäten für Azure-Ressourcen (IMDS/VM-Erweiterung für verwaltete Identitäten für Azure-Ressourcen) Tokens zwischen, es wird jedoch empfohlen, auch die Zwischenspeicherung von Token in Ihrem Code zu implementieren. Daher sollten Sie Vorbereitungen für Szenarien treffen, bei denen die Ressource angibt, dass das Token abgelaufen ist. 

Aufrufe an Azure AD über das Netzwerk erfolgen nur in folgenden Fällen:
- Cachefehler aufgrund eines fehlenden Tokens im Cache des Subsystems für verwaltete Identitäten für Azure-Ressourcen
- Abgelaufenes zwischengespeichertes Token

## <a name="error-handling"></a>Fehlerbehandlung

Der Endpunkt für verwaltete Identitäten für Azure-Ressourcen signalisiert Fehler über das Statuscodefeld des Nachrichtenheaders der HTTP-Antwort als 4xx- oder 5xx-Fehler:

| Statuscode | Fehlerursache | Fehlerbehandlung |
| ----------- | ------------ | ------------- |
| 404 – Nicht gefunden. | Der IMDS-Endpunkt wird gerade aktualisiert. | Wiederholungsversuch mit exponentiellem Backoff. Siehe Anleitung unten. |
| 429: Zu viele Anforderungen. |  IMDS-Drosselungsgrenzwert erreicht. | Wiederholungsversuch mit exponentiellem Backoff. Siehe Anleitung unten. |
| 4xx – Fehler in der Anforderung. | Mindestens einer der Anforderungsparameter war falsch. | Wiederholen Sie den Vorgang nicht.  Überprüfen Sie den Fehler, um weitere Informationen zu erhalten.  4xx-Fehler sind Fehler während der Entwurfszeit.|
| 5xx – Vorübergehender Fehler vom Dienst. | Das Subsystem für verwaltete Identitäten für Azure-Ressourcen oder Azure Active Directory hat einen vorübergehenden Fehler zurückgegeben. | Nach mindestens 1 Sekunde können Sie den Vorgang wiederholen.  Wenn Sie den Vorgang zu schnell oder zu häufig wiederholen, gibt IMDS und/oder Azure AD möglicherweise einen Fehler zum Ratenlimit (429) zurück.|
| timeout | Der IMDS-Endpunkt wird gerade aktualisiert. | Wiederholungsversuch mit exponentiellem Backoff. Siehe Anleitung unten. |

Wenn ein Fehler auftritt, enthält der entsprechende HTTP-Antworttext JSON-Code mit den Fehlerdetails:

| Element | BESCHREIBUNG |
| ------- | ----------- |
| error   | Fehler-ID |
| error_description | Ausführliche Beschreibung des Fehlers. **Fehlerbeschreibungen können sich jederzeit ändern. Schreiben Sie keinen Code, der sich basierend auf Werten in der Fehlerbeschreibung brancht.**|

### <a name="http-response-reference"></a>Referenz für HTTP-Antworten

In diesem Abschnitt sind die möglichen Fehlerantworten aufgeführt. Der Status „200 OK“ ist eine erfolgreiche Antwort, und das Zugriffstoken ist im JSON-Antworttext im Element „access_token“ enthalten.

| Statuscode | Fehler | Fehlerbeschreibung | Lösung |
| ----------- | ----- | ----------------- | -------- |
| 400 – Ungültige Anforderung | invalid_resource | AADSTS50001: Die Anwendung *\<URI\>* wurde im Mandanten *\<TENANT-ID\>* nicht gefunden. Dies kann auftreten, wenn die Anwendung nicht vom Administrator des Mandanten installiert wurde oder wenn sie von den Benutzern des Mandanten keine Zustimmung erhalten hat. Unter Umständen haben Sie Ihre Authentifizierung an den falschen Mandanten gesendet. | (Nur Linux) |
| 400 – Ungültige Anforderung | bad_request_102 | Erforderlicher Metadatenheader nicht angegeben | Entweder fehlt der `Metadata`-Anforderungsheader in Ihrer Anforderung, oder er ist falsch formatiert. Der Wert muss als `true` (in Kleinbuchstaben) angegeben werden. Ein Beispiel finden Sie im vorherigen REST-Abschnitt unter „Beispiel für eine Anforderung“.|
| 401 – Nicht autorisiert | unknown_source | Unbekannte Quelle *\<URI\>* | Stellen Sie sicher, dass Ihr HTTP GET-Anforderungs-URI richtig formatiert ist. Der Teil `scheme:host/resource-path` muss als `http://localhost:50342/oauth2/token` angegeben werden. Ein Beispiel finden Sie im vorherigen REST-Abschnitt unter „Beispiel für eine Anforderung“.|
|           | invalid_request | In der Anforderung fehlt ein erforderlicher Parameter, ist ein ungültiger Parameter enthalten oder ist ein Parameter mehrfach vorhanden, oder die Anforderung ist auf andere Weise fehlerhaft. |  |
|           | unauthorized_client | Der Client ist zum Anfordern eines Zugriffstokens mit dieser Methode nicht autorisiert. | Ursache ist eine Anforderung, die keinen lokalen Loopback zum Aufrufen der Erweiterung verwendet hat, oder ein virtueller Computer, auf dem keine verwalteten Identitäten für Azure-Ressourcen ordnungsgemäß konfiguriert sind. Hilfe zur Konfiguration des virtuellen Computers finden Sie unter [Konfigurieren von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Computer über das Azure-Portal](qs-configure-portal-windows-vm.md). |
|           | access_denied | Der Ressourcenbesitzer oder Autorisierungsserver hat die Anforderung verweigert. |  |
|           | unsupported_response_type | Der Autorisierungsserver unterstützt das Abrufen eines Zugriffstokens mit dieser Methode nicht. |  |
|           | invalid_scope | Der angeforderte Bereich ist ungültig, unbekannt oder falsch formatiert. |  |
| 500 Interner Serverfehler | unknown | Beim Abrufen des Tokens aus Active Directory ist ein Fehler aufgetreten. Einzelheiten finden Sie in den Protokollen unter *\<file path\>* . | Stellen Sie sicher, dass verwaltete Identitäten für Azure-Ressourcen auf dem virtuellen Computer aktiviert wurden. Hilfe zur Konfiguration des virtuellen Computers finden Sie unter [Konfigurieren von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Computer über das Azure-Portal](qs-configure-portal-windows-vm.md).<br><br>Überprüfen Sie zudem, ob Ihr HTTP GET-Anforderungs-URI richtig formatiert ist. Dies gilt vor allem für den Ressourcen-URI, der in der Abfragezeichenfolge angegeben ist. Unter „Beispiel für eine Anforderung“ im vorherigen REST-Abschnitt finden Sie ein Beispiel, und unter [Azure-Dienste, die die Azure AD-Authentifizierung unterstützen](./services-support-managed-identities.md) finden Sie eine Liste mit Diensten und den dazugehörigen Ressourcen-IDs.

## <a name="retry-guidance"></a>Informationen zur Wiederholung 

Wenn Sie einen Fehlercode vom Typ 404, 429 oder 5xx erhalten, sollten Sie den Vorgang wiederholen. (Weitere Informationen finden Sie weiter oben unter [Fehlerbehandlung](#error-handling).)

Drosselungsgrenzwerte gelten für die Anzahl von Aufrufen, die an den IMDS-Endpunkt gerichtet werden. Wird der Drosselungsschwellenwert überschritten, schränkt der IMDS-Endpunkt alle weiteren Anforderungen ein, während die Drosselung aktiv ist. Während dieser Zeit gibt der IMDS-Endpunkt den HTTP-Statuscode 429 (zu viele Anforderungen) zurück, und die Anforderungen sind nicht erfolgreich. 

Empfohlene Wiederholungsstrategie: 

| **Wiederholungsstrategie** | **Einstellungen** | **Werte** | **So funktioniert's** |
| --- | --- | --- | --- |
|ExponentialBackoff |Anzahl der Wiederholungen<br />Min. Backoff<br />Max. Backoff<br />Delta-Backoff<br />Erster schneller Wiederholungsversuch |5<br />0 Sek.<br />60 Sekunden<br />2 Sek<br />false |Versuch 1 – Verzögerung 0 Sek.<br />Versuch 2 – Verzögerung ca. 2 Sek.<br />Versuch 3 – Verzögerung ca. 6 Sek.<br />Versuch 4 – Verzögerung ca. 14 Sek.<br />Versuch 5 – Verzögerung ca. 30 Sek. |

## <a name="resource-ids-for-azure-services"></a>Ressourcen-IDs für Azure-Dienste

Eine Liste mit Ressourcen, die Azure AD unterstützen und mit verwalteten Identitäten für Azure-Ressourcen getestet wurden, und die jeweiligen zugehörigen Ressourcen-IDs finden Sie unter [Azure-Dienste, die die Azure AD-Authentifizierung unterstützen](./services-support-managed-identities.md).


## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Aktivieren von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Azure-Computer finden Sie unter [Konfigurieren von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Computer über das Azure-Portal](qs-configure-portal-windows-vm.md).
