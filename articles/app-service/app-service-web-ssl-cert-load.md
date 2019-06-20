---
title: Verwenden eines SSL-Zertifikats in Anwendungscode – Azure App Service | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Clientzertifikate verwenden, um eine Verbindung mit Remoteressourcen herzustellen, die diese erfordern.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: ead1892062912840c9931ae60d11c90975ad26ac
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475100"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>Verwenden eines SSL-Zertifikats in Ihrem Anwendungscode in Azure App Service

Diese Schrittanleitung zeigt, wie Sie öffentliche oder private Zertifikate in Ihrem Anwendungscode verwenden. Ein Beispiel eines Anwendungsfalls ist der Zugriff Ihrer App auf einen externen Dienst, der eine Zertifikatauthentifizierung erfordert.

Bei der Verwendung von Zertifikaten in Ihrem Code wird die SSL-Funktionalität in App Service genutzt. Dazu muss Ihre App im Tarif **Basic** oder höher ausgeführt werden. Alternativ dazu können Sie [die Zertifikatdatei in Ihr App-Repository einschließen](#load-certificate-from-file), dies ist jedoch keine empfohlene Vorgehensweise für private Zertifikate.

Wenn Sie Ihre SSL-Zertifikate über App Service verwalten, können Sie die Zertifikate und Ihren Anwendungscode separat verwalten und Ihre sensiblen Daten schützen.

## <a name="upload-a-private-certificate"></a>Hochladen eines privaten Zertifikats

Bevor Sie ein privates Zertifikat hochladen, stellen Sie sicher, dass es [alle Anforderungen erfüllt](app-service-web-tutorial-custom-ssl.md#prepare-a-private-certificate). Ausgenommen ist die Anforderung, dass das Zertifikat für die Serverauthentifizierung konfiguriert sein muss.

Wenn Sie bereit zum Hochladen sind, führen Sie den folgenden Befehl in der <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a> aus.

```azurecli-interactive
az webapp config ssl upload --name <app-name> --resource-group <resource-group-name> --certificate-file <path-to-PFX-file> --certificate-password <PFX-password> --query thumbprint
```

Kopieren Sie den Zertifikatfingerabdruck, und [stellen Sie sicher, dass auf das Zertifikat zugegriffen werden kann](#make-the-certificate-accessible).

## <a name="upload-a-public-certificate"></a>Hochladen eines öffentlichen Zertifikats

Öffentliche Zertifikate werden im Format *.cer*-Format unterstützt. Um öffentliches Zertifikat hochzuladen, öffnen Sie das <a href="https://portal.azure.com" target="_blank">Azure-Portal</a>, und navigieren Sie zu Ihrer App.

Klicken Sie im linken Navigationsbereich Ihrer App auf **SSL-Einstellungen** > **Öffentliche Zertifikate (.cer)**  > **Öffentliches Zertifikat hochladen**.

Geben Sie unter **Name** einen Namen für das Zertifikat ein. Wählen Sie unter **CER-Zertifikatdatei** Ihre CER-Datei aus.

Klicken Sie auf **Hochladen**.

![Hochladen eines öffentlichen Zertifikats](./media/app-service-web-ssl-cert-load/private-cert-upload.png)

Sobald das Zertifikat hochgeladen wurde, kopieren Sie den Zertifikatfingerabdruck, und [stellen Sie sicher, dass auf das Zertifikat zugegriffen werden kann](#make-the-certificate-accessible).

## <a name="import-an-app-service-certificate"></a>Importieren eines App Service-Zertifikats

Informationen hierzu finden Sie unter [Kaufen und Konfigurieren eines SSL-Zertifikats für Azure App Service](web-sites-purchase-ssl-web-site.md).

Sobald das Zertifikat importiert wurde, kopieren Sie den Zertifikatfingerabdruck, und [stellen Sie sicher, dass auf das Zertifikat zugegriffen werden kann](#make-the-certificate-accessible).

## <a name="make-the-certificate-accessible"></a>Sicherstellen, dass auf das Zertifikat zugegriffen werden kann

Um ein hochgeladenes oder importiertes Zertifikat in Ihrem App-Code zu verwenden, müssen Sie mit der App-Einstellung `WEBSITE_LOAD_CERTIFICATES` dafür sorgen, dass auf das Zertifikat zugegriffen werden kann. Führen Sie dazu den folgenden Befehl in der <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a> aus:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Damit auf alle Zertifikate zugegriffen werden kann, legen Sie den Wert auf `*` fest.

> [!NOTE]
> Diese Einstellung platziert die angegebenen Zertifikate für die meisten Tarife im Speicher [Current User\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores). Im Tarif **Isolated** allerdings (d.h., die App wird in einer [App Service-Umgebung](environment/intro.md) ausgeführt) wird das Zertifikat im Speicher [Local Machine\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) platziert.
>

![Konfigurieren von App-Einstellungen](./media/app-service-web-ssl-cert-load/configure-app-setting.png)

Klicken Sie abschließend auf **Speichern**.

Die konfigurierten Zertifikate können jetzt von Ihrem Code verwendet werden.

## <a name="load-the-certificate-in-code"></a>Laden des Zertifikats im Code

Nachdem Ihr Zertifikat verfügbar ist, greifen Sie in C#-Code über den Fingerabdruck des Zertifikats auf dieses zu. Der folgende Code lädt ein Zertifikat mit dem Fingerabdruck `E661583E8FABEF4C0BEF694CBC41C28FB81CD870`.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
X509Store certStore = new X509Store(StoreName.My, StoreLocation.CurrentUser);
certStore.Open(OpenFlags.ReadOnly);
X509Certificate2Collection certCollection = certStore.Certificates.Find(
                            X509FindType.FindByThumbprint,
                            // Replace below with your certificate's thumbprint
                            "E661583E8FABEF4C0BEF694CBC41C28FB81CD870",
                            false);
// Get the first cert with the thumbprint
if (certCollection.Count > 0)
{
    X509Certificate2 cert = certCollection[0];
    // Use certificate
    Console.WriteLine(cert.FriendlyName);
}
certStore.Close();
...
```

<a name="file"></a>
## <a name="load-certificate-from-file"></a>Laden des Zertifikats aus einer Datei

Wenn Sie die Zertifikatdatei aus Ihrem Anwendungsverzeichnis laden müssen, sollte der Upload besser über [FTPS](deploy-ftp.md) anstatt z.B. über [Git](deploy-local-git.md) erfolgen. Halten Sie vertrauliche Daten wie beispielsweise ein privates Zertifikat aus der Quellcodeverwaltung heraus.

Auch wenn Sie die Datei direkt in Ihrem .NET-Code laden, überprüft die Bibliothek dennoch, ob das aktuelle Benutzerprofil geladen ist. Um das aktuelle Benutzerprofil zu laden, legen Sie die App-Einstellung `WEBSITE_LOAD_USER_PROFILE` mit dem folgenden Befehl in der <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a> fest.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
```

Sobald diese Einstellung festgelegt ist, lädt das folgende C#-Beispiel ein Zertifikat namens `mycert.pfx` aus dem `certs`-Verzeichnis des Repositorys Ihrer App.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
// Replace the parameter with "~/<relative-path-to-cert-file>".
string certPath = Server.MapPath("~/certs/mycert.pfx");

X509Certificate2 cert = GetCertificate(certPath, signatureBlob.Thumbprint);
...
```
