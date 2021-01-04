---
title: Abrufen eines Tokens von Azure AD zum Autorisieren von Anforderungen von einer Clientanwendung
titleSuffix: Azure Storage
description: Verwenden Sie Azure Active Directory zum Authentifizieren aus einer Clientanwendung, zum Abrufen eines OAuth 2.0-Tokens und zum Autorisieren von Anforderungen an den Azure-Blob- und -Warteschlangenspeicher.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/07/2020
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 6d6a152096ce4e16849542c26d1c7a675a972b89
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96779072"
---
# <a name="acquire-a-token-from-azure-ad-for-authorizing-requests-from-a-client-application"></a>Abrufen eines Tokens von Azure AD zum Autorisieren von Anforderungen von einer Clientanwendung

Ein wesentlicher Vorteil bei der Verwendung von Azure Active Directory (Azure AD) mit dem Azure-Blob- und -Warteschlangenspeicher besteht darin, dass Ihre Anmeldeinformationen nicht mehr im Code gespeichert werden müssen. Stattdessen können Sie ein OAuth 2.0-Zugriffstoken von der Microsoft Identity Platform anfordern. Azure AD übernimmt die Authentifizierung des Sicherheitsprinzipals (Benutzer, Gruppe oder Dienstprinzipal), der die Anwendung ausführt. Wenn die Authentifizierung erfolgreich ist, gibt Azure AD das Zugriffstoken an die Anwendung zurück, und die Anwendung kann dann das Zugriffstoken zum Autorisieren von Anforderungen an den Azure-Blob- oder -Warteschlangenspeicher verwenden.

In diesem Artikel erfahren Sie anhand einer Beispielanwendung, die zum Herunterladen verfügbar ist, wie Sie Ihre native Anwendung oder Webanwendung für die Authentifizierung mit Microsoft Identity Platform konfigurieren können. In der Beispielanwendung wird .NET verwendet. Der Ansatz ist bei anderen Sprachen aber ähnlich. Weitere Informationen zu Microsoft Identity Platform finden Sie unter [Übersicht über Microsoft Identity Platform](../../active-directory/develop/v2-overview.md).

Eine Übersicht über den Datenfluss für OAuth 2.0-Codeberechtigungen finden Sie unter [Autorisieren des Zugriffs auf Azure Active Directory-Webanwendungen mit dem Flow zum Erteilen des OAuth 2.0-Codes](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

## <a name="about-the-sample-application"></a>Infos zur Beispielanwendung

Anhand der Beispielanwendung können Sie in einer End-to-End-Umgebung sehen, wie eine Webanwendung für die Authentifizierung mit Azure AD in einer lokalen Entwicklungsumgebung konfiguriert wird. Zum Anzeigen und Ausführen der Beispielanwendung klonen Sie diese zunächst, oder laden Sie diese aus [GitHub](https://github.com/Azure-Samples/storage-dotnet-azure-ad-msal) herunter. Führen Sie anschließend die im Artikel beschriebenen Schritte durch, um eine Azure-App-Registrierung zu konfigurieren und die Anwendung für Ihre Umgebung zu aktualisieren.

## <a name="assign-a-role-to-an-azure-ad-security-principal"></a>Zuweisen einer Rolle zu einem Azure AD-Sicherheitsprinzipal

Um einen Sicherheitsprinzipal aus Ihrer Azure Storage-Anwendung authentifizieren zu können, müssen Sie zuerst die Einstellungen der rollenbasierten Zugriffskontrolle in Azure (Azure Role-Based Access Control, RBAC) für diesen Sicherheitsprinzipal konfigurieren. Azure Storage definiert integrierte Rollen, die Berechtigungen für Container und Warteschlangen beinhalten. Wird die Azure-Rolle einem Sicherheitsprinzipal zugewiesen, wird diesem Sicherheitsprinzipal Zugriff auf die Ressource gewährt. Weitere Informationen finden Sie unter [Verwalten von Zugriffsrechten für Blob- und Warteschlangendaten mit Azure RBAC](./storage-auth-aad-rbac-portal.md).

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Registrieren der Anwendung bei einem Azure AD-Mandanten

Der erste Schritt bei der Verwendung von Azure AD zum Autorisieren des Zugriffs auf Speicherressourcen ist die Registrierung Ihrer Clientanwendung mit einem Azure AD-Mandanten im [Azure-Portal](https://portal.azure.com). Wenn Sie Ihre Clientanwendung registrieren, liefern Sie Azure AD Informationen zu Ihrer Anwendung. Azure AD stellt dann eine Client-ID (auch als *Anwendungs-ID* bezeichnet) bereit, mit der Sie Ihre Anwendung zur Laufzeit Azure AD zuordnen. Weitere Informationen zur Client-ID finden Sie unter [Anwendungs- und Dienstprinzipalobjekte in Azure Active Directory (Azure AD)](../../active-directory/develop/app-objects-and-service-principals.md). Um Ihre Azure Storage-Anwendung zu registrieren, führen Sie die in der folgenden Anleitung gezeigten Schritte aus [Schnellstart: Registrieren einer Anwendung bei der Microsoft Identity Platform](../../active-directory/develop/quickstart-configure-app-access-web-apis.md). 

Die folgende Abbildung zeigt die allgemeinen Einstellungen zum Registrieren einer Webanwendung. In diesem Beispiel ist der Umleitungs-URI zum Testen der Beispielanwendung in der Entwicklungsumgebung auf `http://localhost:5000/signin-oidc` festgelegt. Diese Einstellung können Sie später unter der Einstellung **Authentifizierung** für Ihre registrierte Anwendung im Azure-Portal ändern:

:::image type="content" source="media/storage-auth-aad-app/app-registration.png" alt-text="Screenshot, der das Registrieren Ihrer Speicheranwendung bei Azure AD zeigt":::

> [!NOTE]
> Wenn Sie Ihre Anwendung als native Anwendung registrieren, können Sie jeden gültigen URI als **Umleitungs-URI** angeben. Bei nativen Anwendungen muss dieser Wert keine echte URL sein. Bei Webanwendungen muss der Umleitungs-URI ein gültiger URI sein, da er die URL definiert, für die Token bereitgestellt werden.

Nachdem Sie Ihre Anwendung registriert haben, wird die Anwendungs-ID (oder Client-ID) unter **Einstellungen** angezeigt:

:::image type="content" source="media/storage-auth-aad-app/app-registration-client-id.png" alt-text="Screenshot der Client-ID":::

Ausführlichere Informationen zum Registrieren einer Anwendung bei Azure AD finden Sie unter [Integrieren von Anwendungen in Azure Active Directory](../../active-directory/develop/quickstart-register-app.md).

### <a name="grant-your-registered-app-permissions-to-azure-storage"></a>Erteilen von Berechtigungen für die registrierte App für Azure Storage

Erteilen Sie als Nächstes Ihrer Anwendung Berechtigungen zum Aufrufen von Azure Storage-APIs. Dieser Schritt ermöglicht Ihrer Anwendung, Anforderungen an Azure Storage mit Azure AD zu autorisieren.

1. Wählen Sie auf der Seite **API-Berechtigungen** für Ihre registrierte Anwendung **Berechtigung hinzufügen** aus.
1. Wählen Sie auf der Registerkarte **Microsoft-APIs** die Option **Azure Storage** aus.
1. Im Bereich **API-Berechtigungen anfordern** unter **Welche Art von Berechtigungen sind für Ihre Anwendung erforderlich?** wird als verfügbarer Berechtigungstyp **Delegierte Berechtigungen** angezeigt. Diese Option ist standardmäßig für Sie aktiviert.
1. Aktivieren Sie unter **Berechtigungen** das Kontrollkästchen neben **user_impersonation**, und klicken Sie auf die Schaltfläche **Berechtigungen hinzufügen**.

    :::image type="content" source="media/storage-auth-aad-app/registered-app-permissions-1.png" alt-text="Screenshot mit Berechtigungen für die Speicher-API":::

1. Erteilen Sie als Nächstes die Administratoreinwilligung für diese Berechtigungen, indem Sie auf **Grant admin consent for Default Directory** (Administratoreinwilligung für Standardverzeichnis gewähren) klicken.

Im Bereich **API-Berechtigungen** wird jetzt angezeigt, dass Ihre registrierte Azure AD-Anwendung Zugriff auf die Microsoft Graph- und auf die Azure Storage-API hat und dass die Einwilligung für das Standardverzeichnis erteilt wurde. Berechtigungen für Microsoft Graph werden automatisch gewährt, wenn Sie Ihre App zum ersten Mal bei Azure AD registrieren.

:::image type="content" source="media/storage-auth-aad-app/registered-app-permissions-2.png" alt-text="Screenshot mit API-Berechtigungen für die registrierte Anwendung":::

### <a name="create-a-client-secret"></a>Erstellen eines Clientgeheimnisses

Die Anwendung benötigt zum Beweis ihrer Identität einen geheimen Clientschlüssel, wenn sie ein Token anfordert. Führen Sie folgende Schritte aus, um den geheimen Clientschlüssel hinzuzufügen:

1. Navigieren Sie im Azure-Portal zu Ihrer App-Registrierung.
1. Klicken Sie auf die Einstellung **Certificates & secrets** (Zertifikate & Geheime Schlüssel).
1. Klicken Sie unter **Client secrets** (Geheime Clientschlüssel) auf **New client secret** (Neuer geheimer Clientschlüssel), um einen neuen geheimen Schlüssel zu erstellen.
1. Geben Sie eine Beschreibung für den geheimen Schlüssel an, und wählen Sie das gewünschte Ablaufintervall aus.
1. Kopieren Sie den Wert des neuen geheimen Schlüssels sofort, und legen Sie die Kopie an einem sicheren Speicherort ab. Der vollständige Wert wird Ihnen nur einmal angezeigt.

    ![Screenshot: geheimer Clientschlüssel](media/storage-auth-aad-app/client-secret.png)

### <a name="enable-implicit-grant-flow"></a>Aktivieren des Flows zur impliziten Genehmigung

Konfigurieren Sie als Nächstes den Flow zur impliziten Genehmigung für Ihre Anwendung. Folgen Sie diesen Schritten:

1. Navigieren Sie im Azure-Portal zu Ihrer App-Registrierung.
1. Wählen Sie im Abschnitt **Manage** (Verwalten) die Einstellung **Authentication** (Authentifizierung) aus.
1. Aktivieren Sie im Abschnitt **Implicit grant** (Implizite Genehmigung) das Kontrollkästchen zum Aktivieren von ID-Token, wie in der folgenden Abbildung gezeigt:

    :::image type="content" source="media/storage-auth-aad-app/enable-implicit-grant-flow.png" alt-text="Screenshot: Aktivieren der Einstellungen für den Flow zur impliziten Genehmigung":::

## <a name="client-libraries-for-token-acquisition"></a>Clientbibliotheken für die Tokenbeschaffung

Sobald Sie Ihre Anwendung registriert und ihr Berechtigungen für den Zugriff auf Daten im Azure-Blob- oder -Warteschlangenspeicher erteilt haben, können Sie Ihrer Anwendung Code zum Authentifizieren eines Sicherheitsprinzipals und zum Beschaffen eines OAuth 2.0-Tokens hinzufügen. Zum Authentifizieren und Abrufen des Tokens können Sie entweder eine der [Authentifizierungsbibliotheken von Microsoft Identity Platform](../../active-directory/develop/reference-v2-libraries.md) oder eine andere Open-Source-Bibliothek verwenden, die OpenID Connect 1.0 unterstützt. Ihre Anwendung kann dann mit dem Zugriffstoken eine Anforderung an den Azure-Blob- oder -Warteschlangenspeicher autorisieren.

Eine Liste der Szenarios, für die das Abrufen von Token unterstützt wird, finden Sie im Abschnitt zu [Authentifizierungsflows](../../active-directory/develop/msal-authentication-flows.md) in der Dokumentation [Microsoft Authentication Library (MSAL)](../../active-directory/develop/msal-overview.md).

## <a name="well-known-values-for-authentication-with-azure-ad"></a>Bekannte Werte für die Authentifizierung mit Azure AD

Um einen Sicherheitsprinzipal bei Azure AD zu authentifizieren, müssen Sie einige bekannte Werte in den Code aufnehmen.

### <a name="azure-ad-authority"></a>Azure AD-Autorität

Für die öffentliche Microsoft-Cloud lautet die Azure AD-Autorität wie unten angegeben. Dabei steht *tenant-id* für Ihre Active Directory-Mandanten-ID (oder Verzeichnis-ID):

`https://login.microsoftonline.com/<tenant-id>/`

Die Mandanten-ID identifiziert den zur Authentifizierung zu verwendenden Azure AD-Mandanten. Er wird auch als Verzeichnis-ID bezeichnet. Navigieren Sie zur Seite **Übersicht** Ihrer App-Registrierung im Azure-Portal, und kopieren Sie den Wert von dort, um die Mandanten-ID abzurufen.

### <a name="azure-storage-resource-id"></a>Azure Storage-Ressourcen-ID

[!INCLUDE [storage-resource-id-include](../../../includes/storage-resource-id-include.md)]

## <a name="net-code-example-create-a-block-blob"></a>Codebeispiel für .NET: Erstellen eines Blockblobs

Das Codebeispiel veranschaulicht, wie Sie ein Zugriffstoken aus Azure AD abrufen. Mit dem Zugriffstoken wird der angegebene Benutzer authentifiziert und anschließend eine Anforderung zum Erstellen eines Blockblobs autorisiert. Damit dieses Beispiel funktioniert, führen Sie zunächst die in den vorherigen Abschnitten beschriebenen Schritte aus.

Sie benötigen die folgenden Werte aus der App-Registrierung, um das Token anzufordern:

- Den Namen Ihrer Azure AD-Domäne. Diesen Wert können Sie über die **Übersicht** Ihrer Azure Active Directory-Instanz abrufen.
- Die ID des Mandanten (oder des Verzeichnisses). Diesen Wert können Sie über die **Übersicht** Ihrer App-Registrierung abrufen.
- Die Client-ID (oder Anwendungs-ID). Diesen Wert können Sie über die **Übersicht** Ihrer App-Registrierung abrufen.
- Der Clientumleitungs-URI. Diesen Wert können Sie über die Einstellungen zur **Authentication** (Authentifizierung) für Ihre App-Registrierung abrufen.
- Den Wert des geheimen Clientschlüssels. Diesen Wert können Sie aus dem Speicherort abrufen, an dem Sie den Wert zuvor abgelegt haben.

### <a name="create-a-storage-account-and-container"></a>Erstellen eines Speicherkontos und Containers

Erstellen Sie mit demselben Abonnement, das Sie auch für Azure Active Directory verwenden, ein Speicherkonto, um das Codebeispiel auszuführen. Erstellen Sie anschließend einen Container innerhalb dieses Speicherkontos. Der Beispielcode wird in diesem Container einen Blockblob erstellen.

Weisen Sie anschließend die Rolle **Storage Blob Data Contributor** (Mitwirkender an Speicherblobdaten) explizit dem Benutzerkonto zu, unter dem Sie den Beispielcode ausführen werden. Informationen zum Zuweisen dieser Rolle im Azure-Portal finden Sie unter [Zuweisen einer Azure-Rolle für den Zugriff auf Blob- und Warteschlangendaten über das Azure-Portal](storage-auth-aad-rbac-portal.md).

> [!NOTE]
> Wenn Sie ein Azure Storage-Konto erstellen, erhalten Sie nicht automatisch Berechtigungen für den Zugriff auf Daten über Azure AD. Sie müssen sich selbst explizit eine Azure-Rolle für Azure Storage zuweisen. Sie können sie auf Ebene Ihres Abonnements, einer Ressourcengruppe, eines Speicherkontos oder eines Containers oder einer Warteschlange zuordnen.
>
> Bevor Sie sich eine Rolle für den Datenzugriff zuweisen, können Sie bereits über das Azure-Portal auf Daten in Ihrem Speicherkonto zugreifen, da das Azure-Portal auch den Kontoschlüssel für den Datenzugriff nutzen kann. Weitere Informationen finden Sie unter [Auswählen der Autorisierung des Zugriffs auf Blobdaten im Azure-Portal](../blobs/authorize-data-operations-portal.md).

### <a name="create-a-web-application-that-authorizes-access-to-blob-storage-with-azure-ad"></a>Erstellen einer Webanwendung, die Zugriff auf Blob Storage über Azure AD gewährt

Wenn Ihre Anwendung auf Azure Storage zugreift, dann im Namen des Benutzers. Der Zugriff auf Blob- oder Warteschlangenressourcen erfolgt also unter Verwendung der Berechtigungen des angemeldeten Benutzers. Sie benötigen zum Testen dieses Codebeispiels eine Webanwendung, die den Benutzer auffordert, sich mit einer Azure AD-Identität anzumelden. Sie können Ihre eigene oder die von Microsoft bereitgestellte Beispielanwendung verwenden.

Eine vollständige Beispielwebanwendung, die ein Token abruft und zum Erstellen eines Blobs in Azure Storage verwendet, steht unter [GitHub](https://aka.ms/aadstorage) zur Verfügung. Durch Überprüfen und Ausführen des vollständigen Beispiels können Sie die Codebeispiele möglicherweise besser verstehen. Informationen zum Ausführen des vollständigen Beispiels finden Sie im Abschnitt [View and run the completed sample (Anzeigen und Ausführen des vollständigen Beispiels)](#view-and-run-the-completed-sample).

#### <a name="add-references-and-using-statements"></a>Hinzufügen von Verweisen und using-Anweisungen  

Installieren Sie von Visual Studio aus die Azure Storage-Clientbibliothek. Wählen Sie im Menü **Extras** den Eintrag **NuGet-Paket-Manager** und danach **Paket-Manager-Konsole**. Geben Sie im Konsolenfenster die folgenden Befehle ein, um die erforderlichen Pakete aus der Azure Storage-Clientbibliothek für .NET zu installieren:

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

```console
Install-Package Azure.Storage.Blobs
Install-Package Microsoft.Identity.Web -Version 0.4.0-preview
```

Fügen Sie anschließend der Datei „HomeController.cs“ die folgenden using-Anweisungen hinzu:

```csharp
using Microsoft.Identity.Web; //MSAL library for getting the access token
using Azure.Storage.Blobs;
```

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

```console
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.Identity.Web -Version 0.4.0-preview //or a later version
```

Fügen Sie anschließend der Datei „HomeController.cs“ die folgenden using-Anweisungen hinzu:

```csharp
using Microsoft.Identity.Client; //MSAL library for getting the access token
using Microsoft.Azure.Storage.Auth;
using Microsoft.Azure.Storage.Blob;
```

---

#### <a name="create-a-block-blob"></a>Erstellen eines Blockblobs

Fügen Sie den folgenden Codeausschnitt hinzu, um einen Blockblob zu erstellen. Denken Sie daran, die Werte in eckigen Klammern durch Ihre eigenen Werte zu ersetzen:

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

```csharp
private static async Task<string> CreateBlob(TokenAcquisitionTokenCredential tokenCredential)
{
    Uri blobUri = new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt");
    BlobClient blobClient = new BlobClient(blobUri, tokenCredential);

    string blobContents = "Blob created by Azure AD authenticated user.";
    byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

    using (MemoryStream stream = new MemoryStream(byteArray))
    {
        await blobClient.UploadAsync(stream);
    }
    return "Blob successfully created";
}
```

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

```csharp
private static async Task<string> CreateBlob(string accessToken)
{
    // Create a blob on behalf of the user.
    TokenCredential tokenCredential = new TokenCredential(accessToken);
    StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

    // Replace the URL below with the URL to your blob.
    Uri blobUri = new Uri("https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt");
    CloudBlockBlob blob = new CloudBlockBlob(blobUri, storageCredentials);
    await blob.UploadTextAsync("Blob created by Azure AD authenticated user.");
    return "Blob successfully created";
}
```

---

> [!NOTE]
> Um Blob- und Warteschlangenvorgänge mit einem OAuth 2.0-Token zu autorisieren, müssen Sie HTTPS verwenden.

Im obigen Beispiel wickelt die .NET-Clientbibliothek die Autorisierung der Anforderung für die Erstellung des Blockblobs ab. Die Autorisierung der Anforderung wird auch von Azure Storage-Clientbibliotheken für andere Sprachen für Sie abgewickelt. Wenn Sie jedoch über die REST-API einen Azure Storage-Vorgang mit einem OAuth-Token aufrufen, müssen Sie den **Autorisierungsheader** mithilfe des OAuth-Tokens erstellen.

Wenn Sie Blob- und Warteschlangendienstvorgänge mit OAuth-Zugriffstoken aufrufen möchten, übergeben Sie das Zugriffstoken im **Autorisierungsheader** unter Verwendung des Schemas **Bearer**, und geben Sie mindestens die Dienstversion 2017-11-09 an, wie im folgenden Beispiel zu sehen:

```https
GET /container/file.txt HTTP/1.1
Host: mystorageaccount.blob.core.windows.net
x-ms-version: 2017-11-09
Authorization: Bearer eyJ0eXAiOnJKV1...Xd6j
```

#### <a name="get-an-access-token-from-azure-ad"></a>Abrufen eines Zugriffstokens aus Azure AD

Fügen Sie als Nächstes eine Methode hinzu, die im Namen des Benutzers ein Token von Azure AD anfordert. Diese Methode definiert den Bereich, für den Berechtigungen erteilt werden sollen. Weitere Informationen zu Berechtigungen und Bereichen finden Sie unter [Berechtigungen und Zustimmung im Microsoft Identity Platform-Endpunkt](../../active-directory/develop/v2-permissions-and-consent.md).

Verwenden Sie die Ressourcen-ID, um den Bereich zu erstellen, für den das Token abgerufen werden soll. Im Beispiel wird der Bereich mit der Ressourcen-ID und dem integrierten `user_impersonation`-Bereich erstellt, der angibt, dass das Token im Namen des Benutzers angefordert wird.

Beachten Sie, dass Sie dem Benutzer möglicherweise eine Benutzeroberfläche zur Verfügung stellen müssen, die ihm ermöglicht, die Zustimmung zur Anforderung des Tokens in seinem Namen zu erteilen:

```csharp
[AuthorizeForScopes(Scopes = new string[] { "https://storage.azure.com/user_impersonation" })]
public async Task<IActionResult> Blob()
{
    string message = await CreateBlob(new TokenAcquisitionTokenCredential(_tokenAcquisition));
    ViewData["Message"] = message;
    return View();
}
```

Einwilligung ist der Prozess, mit dem ein Benutzer einer Anwendung die Autorisierung für den Zugriff auf geschützte Ressourcen in seinem Auftrag gewährt. Microsoft Identity Platform unterstützt die inkrementelle Zustimmung. Das heißt, ein Sicherheitsprinzipal kann zunächst einen Mindestsatz von Berechtigungen anfordern und im Laufe der Zeit und nach Bedarf Berechtigungen hinzufügen. Wenn Ihr Code ein Zugriffstoken anfordert, geben Sie den Umfang der Berechtigungen an, die Ihre Anwendung benötigt. Weitere Informationen zur inkrementellen Zustimmung finden Sie unter [Inkrementelle und dynamische Zustimmung](../../active-directory/azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent).

## <a name="view-and-run-the-completed-sample"></a>Anzeigen und Ausführen des vollständigen Beispiels

Zum Ausführen der Beispielanwendung klonen Sie diese zunächst, oder laden Sie diese aus [GitHub](https://github.com/Azure-Samples/storage-dotnet-azure-ad-msal) herunter. Aktualisieren Sie die Anwendung anschließend wie in den folgenden Abschnitten beschrieben.

### <a name="provide-values-in-the-settings-file"></a>Bereitstellen von Werten in der Einstellungsdatei

Aktualisieren Sie die Datei *appsettings.json* wie folgt mit Ihren eigenen Werten:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "<azure-ad-domain-name>.onmicrosoft.com",
    "TenantId": "<tenant-id>",
    "ClientId": "<client-id>",
    "ClientSecret": "<client-secret>"
    "ClientCertificates": [
    ],
    "CallbackPath": "/signin-oidc"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="update-the-storage-account-and-container-name"></a>Aktualisieren des Speicherkonto- und Containernamens

Aktualisieren Sie in der Datei *HomeController.cs* den URI, der auf den Blockblob verweist, damit dieser den Namen Ihres Speicherkontos und Containers verwendet, wobei Sie die Werte in spitzen Klammern durch Ihre eigenen Werte ersetzen:

```html
https://<storage-account>.blob.core.windows.net/<container>/Blob1.txt
```

## <a name="next-steps"></a>Nächste Schritte

- [Microsoft Identity Platform](../../active-directory/develop/index.yml)
- [Verwalten von Rechten für den Zugriff auf Speicherdaten mithilfe der Azure RBAC](./storage-auth-aad-rbac-portal.md)
- [Authentifizieren des Zugriffs auf Blobs und Warteschlangen mit Azure Active Directory und verwalteten Identitäten für Azure-Ressourcen](storage-auth-aad-msi.md)