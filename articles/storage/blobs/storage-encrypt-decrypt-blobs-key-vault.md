---
title: 'Tutorial: Verschlüsseln und Entschlüsseln von Blobs mit Azure Key Vault'
titleSuffix: Azure Storage
description: Hier wird erläutert, wie Sie ein Blob mithilfe der clientseitigen Verschlüsselung mit Azure Key Vault verschlüsseln und entschlüsseln.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: ddc9dbf77c04ea95e5b873c45de4c0df109514c7
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95544444"
---
# <a name="tutorial---encrypt-and-decrypt-blobs-using-azure-key-vault"></a>Tutorial: Verschlüsseln und Entschlüsseln von Blobs mit Azure Key Vault

In diesem Lernprogramm erfahren Sie, wie Sie die clientseitige Speicherverschlüsselung mit Azure Key Vault verwenden. Sie werden durch die Schritte zum Verschlüsseln und Entschlüsseln eines Blobs in einer Konsolenanwendung mit dieser Technologie geführt.

**Geschätzter Zeitaufwand**: 20 Minuten

Eine Übersicht über Azure Key Vault finden Sie unter [Was ist Azure Key Vault?](../../key-vault/general/overview.md).

Übersichtsinformationen zur clientseitigen Verschlüsselung für Azure Storage finden Sie unter [Clientseitige Verschlüsselung und Azure Key Vault für Microsoft Azure Storage](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

* Ein Azure-Speicherkonto
* Visual Studio 2013 oder höher
* Azure PowerShell

## <a name="overview-of-client-side-encryption"></a>Übersicht über die clientseitige Verschlüsselung

Eine Übersicht über die clientseitige Verschlüsselung für Azure Storage finden Sie unter [Clientseitige Verschlüsselung und Azure Key Vault für Microsoft Azure Storage](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Hier eine kurze Beschreibung zur Funktionsweise der clientseitigen Verschlüsselung:

1. Das Azure Storage-Client-SDK generiert einen Inhaltsverschlüsselungsschlüssel (Content Encryption Key, CEK), bei dem es sich um einen einmalig verwendeten symmetrischen Schlüssel handelt.
2. Die Kundendaten werden mit diesem CEK verschlüsselt.
3. Der CEK wird dann mit dem Schlüsselverschlüsselungsschlüssel (Key Encryption Key, KEK) umschlossen (verschlüsselt). Der KEK wird anhand eines Schlüsselbezeichners identifiziert und kann ein asymmetrisches Schlüsselpaar oder ein symmetrischer Schlüssel sein. Er kann lokal verwaltet oder in Azure Key Vault gespeichert werden. Der Storage-Client hat selbst niemals Zugriff auf den KEK. Er ruft lediglich den Algorithmus für das Umschließen des Schlüssels aus, der vom Schlüsseltresor bereitgestellt wird. Kunden können bei Bedarf benutzerdefinierte Anbieter für das Umschließen von Schlüsseln bzw. das Aufheben dieses Zustands verwenden.
4. Die verschlüsselten Daten werden dann in den Azure Storage-Dienst hochgeladen.

## <a name="set-up-your-azure-key-vault"></a>Einrichten des Azure-Schlüsseltresors

Zum Fortsetzen dieses Tutorials müssen Sie die folgenden Schritte ausführen, die beschrieben sind im Tutorial [Schnellstart: Festlegen eines Geheimnisses und Abrufen des Geheimnisses aus Azure Key Vault mithilfe einer .NET-Web-App](../../key-vault/secrets/quick-create-net.md).

* Erstellen eines Schlüsseltresors
* Hinzufügen eines Schlüssels oder geheimen Schlüssels zum Schlüsseltresor
* Registrieren Sie eine Anwendung bei Azure Active Directory.
* Autorisieren der Anwendung zum Verwenden des Schlüssels oder geheimen Schlüssels

Notieren Sie ClientID und ClientSecret. Diese Angaben werden generiert, wenn Sie eine Anwendung mit Azure Active Directory registrieren.

Erstellen Sie beide Schlüssel im Schlüsseltresor. Im weiteren Verlauf des Tutorials setzen wir voraus, dass Sie die folgenden Namen verwendet haben: ContosoKeyVault und TestRSAKey1.

## <a name="create-a-console-application-with-packages-and-appsettings"></a>Erstellen einer Konsolenanwendung mit Paketen und AppSettings

Erstellen Sie in Visual Studio eine neue Konsolenanwendung.

Fügen Sie die erforderlichen NuGet-Pakete in der Paket-Manager-Konsole hinzu.

```powershell
Install-Package Microsoft.Azure.ConfigurationManager
Install-Package Microsoft.Azure.Storage.Common
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

Install-Package Microsoft.Azure.KeyVault
Install-Package Microsoft.Azure.KeyVault.Extensions
```

Fügen Sie der Datei „App.Config“ AppSettings hinzu.

```xml
<appSettings>
    <add key="accountName" value="myaccount"/>
    <add key="accountKey" value="theaccountkey"/>
    <add key="clientId" value="theclientid"/>
    <add key="clientSecret" value="theclientsecret"/>
    <add key="container" value="stuff"/>
</appSettings>
```

Fügen Sie die folgenden `using`-Anweisungen hinzu, und stellen Sie sicher, dass Sie dem Projekt einen Verweis auf „System.Configuration“ hinzufügen.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Configuration;
using Microsoft.Azure;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Auth;
using Microsoft.Azure.Storage.Blob;
using Microsoft.Azure.KeyVault;
using System.Threading;
using System.IO;
```

## <a name="add-a-method-to-get-a-token-to-your-console-application"></a>Hinzufügen einer Methode zum Abrufen eines Tokens für die Konsolenanwendung

Die folgende Methode wird von Schlüsseltresorklassen verwendet, für die eine Authentifizierung des Zugriffs auf den Schlüsseltresor erforderlich ist.

```csharp
private async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);
    ClientCredential clientCred = new ClientCredential(
        CloudConfigurationManager.GetSetting("clientId"),
        CloudConfigurationManager.GetSetting("clientSecret"));
    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)
        throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

## <a name="access-azure-storage-and-key-vault-in-your-program"></a>Zugreifen auf Azure Storage und Key Vault in Ihrem Programm

Fügen Sie in der „Main()“-Methode den folgenden Code hinzu:

```csharp
// This is standard code to interact with Blob storage.
StorageCredentials creds = new StorageCredentials(
    CloudConfigurationManager.GetSetting("accountName"),
    CloudConfigurationManager.GetSetting("accountKey")
);
CloudStorageAccount account = new CloudStorageAccount(creds, useHttps: true);
CloudBlobClient client = account.CreateCloudBlobClient();
CloudBlobContainer contain = client.GetContainerReference(CloudConfigurationManager.GetSetting("container"));
contain.CreateIfNotExists();

// The Resolver object is used to interact with Key Vault for Azure Storage.
// This is where the GetToken method from above is used.
KeyVaultKeyResolver cloudResolver = new KeyVaultKeyResolver(GetToken);
```

> [!NOTE]
> Schlüsseltresor-Objektmodelle
> 
> Es ist wichtig zu verstehen, dass eigentlich zwei Schlüsseltresor-Objektmodelle vorhanden sind: eins basiert auf der REST-API (KeyVault-Namespace), und das andere ist eine Erweiterung für die clientseitige Verschlüsselung.
> 
> Der Schlüsseltresor-Client interagiert mit der REST-API und erkennt JSON-Webschlüssel und geheime Schlüssel für die beiden Arten von Elementen, die im Schlüsseltresor enthalten sind.
> 
> Die Schlüsseltresor-Erweiterungen sind Klassen, die scheinbar speziell für die clientseitige Verschlüsselung in Azure Storage erstellt wurden. Sie enthalten eine Schnittstelle für Schlüssel (IKey) und Klassen, die auf dem Konzept eines "Key Resolver" basieren. Es gibt zwei Implementierungen von IKey, die Sie kennen müssen: RSAKey und SymmetricKey. Sie kollidieren mit den Elementen, die im Schlüsseltresor enthalten sind, aber an diesem Punkt sind es unabhängige Klassen (Schlüssel und geheimer Schlüssel, die vom Schlüsseltresor-Client abgerufen werden, implementieren IKey also nicht).
> 
> 

## <a name="encrypt-blob-and-upload"></a>Verschlüsseln des Blobs und Hochladen

Fügen Sie den folgenden Code hinzu, um ein BLOB zu verschlüsseln und in Ihr Azure-Speicherkonto hochzuladen. Die **ResolveKeyAsync**-Methode, die verwendet wird, gibt einen IKey zurück.

```csharp
// Retrieve the key that you created previously.
// The IKey that is returned here is an RsaKey.
var rsa = cloudResolver.ResolveKeyAsync(
            "https://contosokeyvault.vault.azure.net/keys/TestRSAKey1", 
            CancellationToken.None).GetAwaiter().GetResult();

// Now you simply use the RSA key to encrypt by setting it in the BlobEncryptionPolicy.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(rsa, null);
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

// Reference a block blob.
CloudBlockBlob blob = contain.GetBlockBlobReference("MyFile.txt");

// Upload using the UploadFromStream method.
using (var stream = System.IO.File.OpenRead(@"C:\Temp\MyFile.txt"))
    blob.UploadFromStream(stream, stream.Length, null, options, null);
```

> [!NOTE]
> Wenn Sie sich den BlobEncryptionPolicy-Konstruktor ansehen, wird Ihnen auffallen, dass dafür ein Schlüssel und/oder ein Konfliktlöser (Resolver) verwendet werden kann. Denken Sie daran, dass Sie an diesem Punkt keinen Konfliktlöser für die Verschlüsselung verwenden können, weil dafür derzeit kein Standardschlüssel unterstützt wird.

## <a name="decrypt-blob-and-download"></a>Entschlüsseln des Blobs und Herunterladen

Eigentlich findet Entschlüsselung statt, wenn die Verwendung der Resolver-Klassen sinnvoll ist. Die ID des Schlüssels, der für die Verschlüsselung verwendet wird, wird dem BLOB in seinen Metadaten zugeordnet. Es gibt für Sie also keinen Grund, den Schlüssel abzurufen und sich die Zuordnung zwischen Schlüssel und BLOB zu merken. Sie müssen lediglich sicherstellen, dass der Schlüssel im Schlüsseltresor erhalten bleibt.   

Der private Schlüssel eines RSA-Schlüssels verbleibt im Schlüsseltresor. Damit die Entschlüsselung durchgeführt werden kann, wird der verschlüsselte Schlüssel aus den BLOB-Metadaten, der den CEK enthält, zur Entschlüsselung an den Schlüsseltresor gesendet.

Fügen Sie Folgendes hinzu, um das Blob zu entschlüsseln, das Sie gerade hochgeladen haben.

```csharp
// In this case, we will not pass a key and only pass the resolver because
// this policy will only be used for downloading / decrypting.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(null, cloudResolver);
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

using (var np = File.Open(@"C:\data\MyFileDecrypted.txt", FileMode.Create))
    blob.DownloadToStream(np, null, options, null);
```

> [!NOTE]
> Es sind einige andere Arten von Konfliktlösern (Resolvers) vorhanden, um die Schlüsselverwaltung zu vereinfachen, z.B. AggregateKeyResolver und CachingKeyResolver.

## <a name="use-key-vault-secrets"></a>Verwenden von geheimen Schlüsseltresor-Schlüsseln

Für die Verwendung eines geheimen Schlüssels mit clientseitiger Verschlüsselung wird die SymmetricKey-Klasse genutzt, da ein geheimer Schlüssel im Wesentlichen ein symmetrischer Schlüssel ist. Wie oben erwähnt, wird ein geheimer Schlüssel im Schlüsseltresor nicht exakt einem "SymmetricKey" zugeordnet. Hierbei sind einige Dinge zu beachten:

* Der Schlüssel in einem "SymmetricKey" muss eine feste Länge haben: 128, 192, 256, 384 oder 512 Bit.
* Der Schlüssel in einem "SymmetricKey" sollte über eine Base64-Codierung verfügen.
* Ein geheimer Schlüsseltresor-Schlüssel, der als "SymmetricKey" verwendet wird, muss im Schlüsseltresor den Inhaltstyp "application/octet-stream" aufweisen.

Hier ist ein Beispiel in PowerShell angegeben, bei dem im Schlüsseltresor ein geheimer Schlüssel erstellt wird, der als "SymmetricKey" verwendet werden kann.
Bitte beachten Sie, dass der hartcodierte Wert „$key“ nur der Veranschaulichung dient. In Ihrem eigenen Code sollten Sie diesen Schlüssel generieren.

```powershell
// Here we are making a 128-bit key so we have 16 characters.
//     The characters are in the ASCII range of UTF8 so they are
//    each 1 byte. 16 x 8 = 128.
$key = "qwertyuiopasdfgh"
$b = [System.Text.Encoding]::UTF8.GetBytes($key)
$enc = [System.Convert]::ToBase64String($b)
$secretvalue = ConvertTo-SecureString $enc -AsPlainText -Force

// Substitute the VaultName and Name in this command.
$secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'TestSecret2' -SecretValue $secretvalue -ContentType "application/octet-stream"
```

In Ihrer Konsolenanwendung können Sie den gleichen Aufruf wie vorher verwenden, um diesen geheimen Schlüssel als "SymmetricKey" abzurufen.

```csharp
SymmetricKey sec = (SymmetricKey) cloudResolver.ResolveKeyAsync(
    "https://contosokeyvault.vault.azure.net/secrets/TestSecret2/",
    CancellationToken.None).GetAwaiter().GetResult();
```

Das ist alles. Viel Spaß!

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Verwendung von Microsoft Azure Storage mit C# finden Sie unter [Microsoft Azure Storage Client Library for .NET](/previous-versions/azure/dn261237(v=azure.100)) (Microsoft Azure Storage-Clientbibliothek für .NET).

Weitere Informationen zur Blob-REST-API finden Sie unter [REST-API des Blob-Diensts](/rest/api/storageservices/Blob-Service-REST-API).

Aktuelle Informationen zu Microsoft Azure Storage finden Sie im [Microsoft Azure Storage Team Blog](/archive/blogs/windowsazurestorage/) (in englischer Sprache).