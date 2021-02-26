---
title: 'Tutorial: Verwenden der REST-APIs'
description: In diesem Tutorial wird beschrieben, wie Sie mit den Azure Purview-REST-APIs auf die Inhalte Ihres Katalogs zugreifen.
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/03/2020
ms.openlocfilehash: bfb808c634ba946e1a4825d7828db6df8963352c
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98951242"
---
# <a name="tutorial-use-the-rest-apis"></a>Tutorial: Verwenden der REST-APIs

In diesem Tutorial erfahren Sie, wie Sie die Azure Purview-REST-APIs verwenden. Jeder Benutzer, der Daten an einen Azure Purview-Katalog übermitteln, den Katalog im Rahmen eines automatisierten Prozesses aufnehmen oder seine eigene Benutzerumgebung im Katalog erstellen möchte, kann hierfür die REST-APIs verwenden.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Erstellen eines Dienstprinzipals (Anwendung)
> * Konfigurieren des Katalogs, sodass er dem Dienstprinzipal (Anwendung) vertraut
> * Anzeigen der Dokumentation zu den REST-APIs
> * Sammeln der erforderlichen Werte für die Verwendung der REST-APIs
> * Verwenden des Postman-Clients zum Aufrufen der REST-APIs
> * Generieren eines .NET-Clients zum Aufrufen der REST-APIs

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

* Für die ersten Schritte benötigen Sie ein vorhandenes Azure Purview-Konto. Wenn Sie keinen Katalog besitzen, finden Sie weitere Informationen in der [Schnellstartanleitung zum Erstellen eines Azure Purview-Kontos](create-catalog-portal.md).

* Falls Sie dem Katalog Daten hinzufügen müssen, lesen Sie das [Tutorial zum Ausführen des Starter Kits und zum Überprüfen von Daten](tutorial-scan-data.md).

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

## <a name="create-a-service-principal-application"></a>Erstellen eines Dienstprinzipals (Anwendung)

Damit ein REST-API-Client auf den Katalog zugreifen kann, muss der Client über einen Dienstprinzipal (Anwendung) und eine Identität verfügen, die der Katalog erkennt und der er konfigurationsgemäß vertraut. Bei REST-API-Aufrufen an den Katalog wird die Identität des Dienstprinzipals verwendet.

Bei Kunden, die vorhandene Dienstprinzipale (Anwendungs-IDs) verwendet haben, gab es eine hohe Fehlerrate. Daher empfiehlt es sich, einen neuen Dienstprinzipal zum Aufrufen von APIs zu erstellen.

So erstellen Sie einen neuen Dienstprinzipal:

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach **Azure Active Directory**, und wählen Sie den Dienst aus.
1. Wählen Sie auf der Seite **Azure Active Directory** im linken Bereich die Option **App-Registrierungen** aus.
1. Wählen Sie **Neue Registrierung** aus.
1. Gehen Sie auf der Seite **Registrieren einer Anwendung** folgendermaßen vor:
    1. Geben Sie einen **Namen** für die Anwendung (Dienstprinzipalname) ein.
    1. Wählen Sie **Nur Konten in diesem Organisationsverzeichnis (nur _&lt;Name Ihres Mandanten&gt;_  – einzelner Mandant)** aus.
    1. Wählen Sie unter **Umleitungs-URI (optional)** die Option **Web** aus, und geben Sie einen Wert ein. Dieser Wert muss kein gültiger URI sein.
    1. Wählen Sie **Registrieren**.
1. Kopieren Sie auf der Seite des neuen Dienstprinzipals die Werte unter **Anzeigename** und **Anwendungs-ID (Client)** , und speichern Sie sie zur späteren Verwendung.

   Die Anwendungs-ID ist der Wert `client_id` im Beispielcode.

Zur Verwendung des Dienstprinzipals (Anwendung) müssen Sie sein Kennwort abrufen. Gehen Sie dabei folgendermaßen vor:

1. Suchen Sie im Azure-Portal nach **Azure Active Directory**, und wählen Sie den Dienst aus. Wählen Sie anschließend im linken Bereich die Option **App-Registrierungen** aus.
1. Wählen Sie in der Liste Ihren Dienstprinzipal (Anwendung) aus.
1. Wählen Sie im linken Bereich die Option **Zertifikate und Geheimnisse** aus.
1. Wählen Sie **Neuer geheimer Clientschlüssel**.
1. Geben Sie auf der Seite **Geheimen Clientschlüssel hinzufügen** unter **Beschreibung** eine Beschreibung ein, und wählen Sie unter **Gültig bis** eine Ablaufzeit und anschließend **Hinzufügen** aus.

   Auf der Seite **Geheime Clientschlüssel** ist die Zeichenfolge in der Spalte **Wert** des neuen Geheimnisses Ihr Kennwort. Speichern Sie diesen Wert.

   :::image type="content" source="./media/tutorial-using-rest-apis/client-secret.png" alt-text="Screenshot: Geheimer Clientschlüssel":::

## <a name="configure-your-catalog-to-trust-the-service-principal-application"></a>Konfigurieren des Katalogs, sodass er dem Dienstprinzipal (Anwendung) vertraut

So konfigurieren Sie Azure Purview, sodass der Dienst dem neuen Dienstprinzipal vertraut:

1. Navigieren Sie zu Ihrem Purview-Konto.

1. Wählen Sie auf der Seite **Purview account** (Purview-Konto) die Registerkarte **Zugriffssteuerung (IAM)** aus.

1. Klicken Sie auf **+ Hinzufügen**.

1. Wählen Sie **Rollenzuweisung hinzufügen** aus.

1. Geben Sie als Rollentyp **Datenkurator für Purview** ein.

    > [!Note]
    > Weitere Informationen zu Azure Purview-Datenkatalogberechtigungen finden Sie unter [Rollenbasierte Zugriffssteuerung auf der Datenebene von Azure Purview](catalog-permissions.md). Wenn Sie beispielsweise die Berechtigung zum Auslösen von Überprüfungen benötigen, muss als Rollentyp **Datenquellenadministrator für Purview** festgelegt werden.

1. Behalten Sie unter **Zugriff zuweisen zu** den Standardwert **Benutzer, Gruppe oder Dienstprinzipal** bei.

1. Geben Sie unter **Auswählen** den Namen des zuvor erstellten Dienstprinzipals ein, den Sie zuweisen möchten, und klicken Sie anschließend im Ergebnisbereich auf den entsprechenden Namen.

1. Klicken Sie im Menü „Einstellungen“ auf **Speichern**

Sie haben den Dienstprinzipal nun als Anwendungsadministrator konfiguriert, sodass er Inhalte an den Katalog senden kann.

## <a name="view-the-rest-apis-documentation"></a>Anzeigen der Dokumentation zu den REST-APIs

Laden Sie zum Anzeigen der API-Swagger-Dokumentation die Datei [PurviewCatalogAPISwagger.zip](https://github.com/Azure/Purview-Samples/raw/master/rest-api/PurviewCatalogAPISwagger.zip) herunter, extrahieren Sie die Dateien, und öffnen Sie „index.html“.

Weitere Informationen zur erweiterten Such-/Vorschlags-API von Azure Purview finden Sie in der Dokumentation zu REST-API-Suchfiltern. Der von AutoRest generierte Client unterstützt derzeit keine angepassten Suchparameter. Definieren Sie zur Umgehung dieses Problem anhand der Suchfilterdokumentation Filterklassen im Code als API-Aufrufparameter. Das Dokument „index.html“ enthält Beispiele zu diesen APIs.

## <a name="collect-the-necessary-values-to-use-the-rest-apis"></a>Sammeln der erforderlichen Werte für die Verwendung der REST-APIs

Suchen und speichern Sie die folgenden Werte:

* Mandanten-ID:
  * Suchen Sie im [Azure-Portal](https://portal.azure.com) nach **Azure Active Directory**, und wählen Sie den Dienst aus.
  * Wählen Sie im linken Bereich im Abschnitt **Verwalten** die Option **Eigenschaften** aus, suchen Sie die **Mandanten-ID**, und wählen Sie dann das Symbol **In Zwischenablage kopieren** aus, um den Wert zu speichern.
* Atlas-Endpunkt:
  * Suchen Sie im Azure-Portal auf der [Seite mit den Azure Purview-Konten](https://aka.ms/purviewportal) in der Liste Ihr Azure Purview-Konto, und wählen Sie es aus.
  * Wählen Sie **Eigenschaften** aus, suchen Sie den **Atlas-Endpunkt**, und wählen Sie dann das Symbol **In Zwischenablage kopieren** aus, um den Wert zu speichern. Entfernen Sie den Teil *https://* der Zeichenfolge, wenn Sie sie später verwenden.
* Kontoname:
  * Extrahieren Sie den Namen Ihres Katalogs aus der Atlas-Endpunktzeichenfolge. Beispiel: Ist der Atlas-Endpunkt `https://ThisIsMyCatalog.catalog.purview.azure.com`, lautet der Kontoname `ThisIsMyCatalog`.

## <a name="use-the-postman-client-to-call-the-rest-apis"></a>Verwenden des Postman-Clients zum Aufrufen der REST-APIs

1. Installieren Sie den [Postman-Client](https://www.getpostman.com/).
1. Wählen Sie im Client die Option **Import** (Importieren) aus, und verwenden Sie [Test.postman_collection.json](https://raw.githubusercontent.com/Azure/Purview-Samples/master/rest-api/Test.postman_collection.json).
1. Wählen Sie **Collections** (Sammlungen) und anschließend **Test** (Testen) aus.
1. Wählen Sie **Get Token** (Token abrufen) aus:
    1. Ersetzen Sie in der URL neben POST *&lt;your-tenant-id&gt;* durch die Mandanten-ID, die Sie im vorherigen Abschnitt kopiert haben.
    1. Wählen Sie die Registerkarte **Body** (Text) aus, und ersetzen Sie die Platzhalter im Pfad und Text aus dem vorherigen Schritt.

       Nach dem Auswählen von **Send** (Senden), enthält der Antworttext eine JSON-Struktur, einschließlich des Namens *access_token* und eines mit Anführungszeichen versehenen Zeichenfolgenwerts. 
    1. Kopieren Sie den Bearertokenwert (ohne Anführungszeichen), um ihn im nächsten Schritt zu verwenden.

1. Wählen Sie **/v2/types/typedefs** aus:
    1. Ersetzen Sie den Platzhalter im Pfad durch den Wert des Atlas-Endpunkts. Dieser Wert kann abgerufen werden, indem Sie im Ibiza-Portal zur Kataloginstanz navigieren und auf „Übersicht“ klicken. 

       Das Bearertoken wird im ersten Schritt festgelegt. (Alternativ können Sie es manuell auf der Registerkarte „Autorisierung“ kopieren.)

    1. Wählen Sie **Send** (Senden) aus, um die Antwort zu erhalten.

## <a name="generate-a-net-client-to-call-the-rest-apis"></a>Generieren eines .NET-Clients zum Aufrufen der REST-APIs

### <a name="install-autorest"></a>Installieren von AutoRest



1. [Installieren Sie Node.js.](https://github.com/Azure/autorest/blob/v2/docs/installing-autorest.md)
1. Öffnen Sie PowerShell, und führen Sie den folgenden Befehl aus:

   ```powershell
   npm install -g autorest@V2
   ```

### <a name="download-rest-api-specszip-and-create-the-client"></a>Herunterladen von „rest-api-specs.zip“ und Erstellen des Clients

1. Laden Sie [rest-api-specs.zip](https://github.com/Azure/Purview-Samples/raw/master/rest-api/rest-api-specs.zip) herunter, und extrahieren Sie die Dateien.
1. Führen Sie in PowerShell den folgenden Befehl im extrahierten Ordner „rest-api-specs“ aus:

   ```powershell
   autorest --input-file=data-plane/preview/purviewcatalog.json --csharp --output-folder=PurviewCatalogClient/CSharp --namespace=PurviewCatalog --add-credentials
   ```

   Die Ausgabe dieses Prozesses erstellt den Ordner „PurviewCatalogClient“ und den Unterordner „CSharp“ im Ordner „rest-api-specs“.

### <a name="create-a-sample-net-console-application"></a>Erstellen einer .NET-Konsolenbeispielanwendung

1. Öffnen Sie Visual Studio 2019. Diese Anweisungen wurden mit der kostenlosen Community Edition getestet.
1. Erstellen Sie auf der Seite **Neues Projekt erstellen** ein Projekt vom Typ **Konsolen-App (.NET Core)** in C#.
1. Kopieren Sie den bereitgestellten [Beispielcode](#sample-code-for-the-console-application), und fügen Sie ihn ein.
1. Ersetzen Sie *accountName*, *servicePrincipalId*, *servicePrincipalKey* und *tenantId* durch die zuvor gesammelten Werte.
1. Verwenden Sie den **Projektmappen-Explorer**, um im Visual Studio-Projekt einen Ordner mit dem Namen **Generiert** hinzuzufügen.
1. Kopieren Sie den zuvor erstellten Ordner „rest-api-specs\PurviewCatalogClient\CSharp“ in den Ordner „\Generiert“. Verwenden Sie für den Kopiervorgang den Datei-Explorer oder die Eingabeaufforderung. Dadurch wird in Visual Studio das automatische Hinzufügen der Dateien zum Projekt ausgelöst.
1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie dann **NuGet-Pakete verwalten** aus.
1. Wählen Sie die Registerkarte **Durchsuchen** aus. Suchen Sie nach **Microsoft.Rest.ClientRuntime**, und wählen Sie das Paket aus.
1. Stellen Sie sicher, dass die Version mindestens 2.3.21 lautet, und wählen Sie dann **Installieren** aus.
1. Erstellen Sie die Anwendung, und führen Sie sie aus.

Der Beispielcode gibt die Anzahl von Typdefinitionen (Typedefs) im Katalog zurück und zeigt, wie Rollenzuweisungen behandelt werden sollen. Ausführliche Informationen finden Sie im Beispielcode unter `DoRoleAssignmentOperations()`. Weitere Informationen zum Projekt finden Sie unter [Projekteinrichtung](https://github.com/Azure/autorest/blob/v2/docs/client/proj-setup.md).

### <a name="sample-code-for-the-console-application"></a>Beispielcode für die Konsolenanwendung

```csharp
using Microsoft.Rest;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;

namespace PurviewCatalogSdkTest
{
    class Program
    {
        private static string accountName = "{account-name}";
        private static string servicePrincipalId = "{service-principal-id}";
        private static string servicePrincipalKey = "{service-principal-key}";
        private static string tenantId = "{tenant-id}";

        static void Main(string[] args)
        {
            Console.WriteLine("Azure Purview client");

            // You need to change the api path below (e.g. /api) based on what you're trying to call
            string baseUri = string.Format("https://{0}.catalog.purview.azure.com/api", accountName);

            // Get token and set auth
            var svcClientCreds = new TokenCredentials(getToken(), "Bearer");
            var client = new PurviewCatalog.PurviewCatalogServiceRESTAPIDocument(svcClientCreds);
            client.BaseUri = new System.Uri(baseUri);

            // /v2/types/typedefs
            var task = client.TypesREST.GetAllTypeDefsWithHttpMessagesAsync();
            Console.WriteLine("\nURI:\n" + task.Result.Request.RequestUri);
            Console.WriteLine("\nStatus Code:\n" + task.Result.Response.StatusCode);
            Console.WriteLine("\nResult:\n" + JsonConvert.SerializeObject(task.Result.Body, Formatting.Indented));
        }

        // Replace client_id and client_secret with application ID and password value from service principal
        private static string getToken()
        {
            var values = new Dictionary<string, string>

            // The "resource" could be "https://purview.azure.net" or "73c2949e-da2d-457a-9607-fcc665198967"
            {
                { "grant_type", "client_credentials" },
                { "client_id", servicePrincipalId },
                { "client_secret", servicePrincipalKey },
                { "resource", "73c2949e-da2d-457a-9607-fcc665198967" }
            };

            string authUrl = string.Format("https://login.windows.net/{0}/oauth2/token", tenantId);
            var content = new FormUrlEncodedContent(values);

            HttpClient authClient = new HttpClient();
            var bearerResult = authClient.PostAsync(authUrl, content);
            bearerResult.Wait();
            var resultContent = bearerResult.Result.Content.ReadAsStringAsync();
            resultContent.Wait();
            var bearerToken = JObject.Parse(resultContent.Result)["access_token"].ToString();
            var svcClientCreds = new TokenCredentials(bearerToken, "Bearer");

            return bearerToken;
        }
    }
}
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Datenquellen verwalten](manage-data-sources.md)
