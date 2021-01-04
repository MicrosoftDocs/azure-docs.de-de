---
title: Verwenden eines TLS-/SSL-Zertifikats im Code
description: Erfahren Sie, wie Sie Clientzertifikate in Ihrem Code verwenden. Authentifizieren Sie sich bei Remoteressourcen mit einem Clientzertifikat, oder führen Sie Kryptografieaufgaben mit diesen aus.
ms.topic: article
ms.date: 09/22/2020
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: b4e184f827875ebebd40ab976ef63e77ee702d49
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93126038"
---
# <a name="use-a-tlsssl-certificate-in-your-code-in-azure-app-service"></a>Verwenden eines TLS-/SSL-Zertifikats in Ihrem Code in Azure App Service

Sie können in Ihrem Anwendungscode auf die [öffentlichen oder privaten Zertifikate zugreifen, die Sie App Service hinzufügen](configure-ssl-certificate.md). Der App-Code kann als Client fungieren und auf einen externen Dienst mit erforderlicher Zertifikatauthentifizierung zugreifen oder ggf. kryptografische Aufgaben ausführen. Diese Schrittanleitung zeigt, wie Sie öffentliche oder private Zertifikate in Ihrem Anwendungscode verwenden.

Bei der Verwendung von Zertifikaten in Ihrem Code wird die TLS-Funktionalität in App Service genutzt. Dazu muss Ihre App im Tarif **Basic** oder höher ausgeführt werden. Befindet sich Ihre App in der Ebene **Free** oder **Shared** , können Sie [die Zertifikatsdatei in Ihr App-Repository aufnehmen](#load-certificate-from-file).

Wenn Sie Ihre TLS-/SSL-Zertifikate über App Service verwalten, können Sie die Zertifikate und Ihren Anwendungscode separat verwalten und Ihre sensiblen Daten schützen.

## <a name="prerequisites"></a>Voraussetzungen

Im Rahmen dieser Schrittanleitung müssen Sie folgende Schritte durchführen:

- [Erstellen einer App Service-App](./index.yml)
- [Hinzufügen eines Zertifikats zur App](configure-ssl-certificate.md)

## <a name="find-the-thumbprint"></a>Ermitteln des Fingerabdrucks

Wählen Sie im <a href="https://portal.azure.com" target="_blank">Azure-Portal</a> im linken Menü **App Services** >  **\<app-name>** aus.

Wählen Sie im linken Navigationsbereich Ihrer App **TLS-/SSL-Einstellungen** und dann **Private Schlüsselzertifikate (PFX)** oder **Öffentliche Schlüsselzertifikate (CER)** aus.

Ermitteln Sie das Zertifikat, das Sie verwenden möchten, und kopieren Sie den Fingerabdruck des Zertifikats.

![Kopieren des Zertifikatfingerabdrucks](./media/configure-ssl-certificate/create-free-cert-finished.png)

## <a name="make-the-certificate-accessible"></a>Sicherstellen, dass auf das Zertifikat zugegriffen werden kann

Fügen Sie zum Zugreifen auf ein Zertifikat in Ihrem App-Code der App-Einstellung `WEBSITE_LOAD_CERTIFICATES` den Fingerabdruck hinzu, indem Sie den folgenden Befehl in <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a> ausführen:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

Damit auf alle Zertifikate zugegriffen werden kann, legen Sie den Wert auf `*` fest.

## <a name="load-certificate-in-windows-apps"></a>Laden eines Zertifikats in Windows-Apps

Die App-Einstellung `WEBSITE_LOAD_CERTIFICATES` macht die angegebenen Zertifikate für Ihre unter Windows gehostete App im Windows-Zertifikatspeicher in [Current User\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) zugänglich.

In C#-Code greifen Sie auf das Zertifikat anhand des Zertifikatfingerabdrucks zu. Der folgende Code lädt ein Zertifikat mit dem Fingerabdruck `E661583E8FABEF4C0BEF694CBC41C28FB81CD870`.

```csharp
using System;
using System.Linq;
using System.Security.Cryptography.X509Certificates;

string certThumbprint = "E661583E8FABEF4C0BEF694CBC41C28FB81CD870";
bool validOnly = false;

using (X509Store certStore = new X509Store(StoreName.My, StoreLocation.CurrentUser))
{
  certStore.Open(OpenFlags.ReadOnly);

  X509Certificate2Collection certCollection = certStore.Certificates.Find(
                              X509FindType.FindByThumbprint,
                              // Replace below with your certificate's thumbprint
                              certThumbprint,
                              validOnly);
  // Get the first cert with the thumbprint
  X509Certificate2 cert = certCollection.OfType<X509Certificate>().FirstOrDefault();

  if (cert is null)
      throw new Exception($"Certificate with thumbprint {certThumbprint} was not found");

  // Use certificate
  Console.WriteLine(cert.FriendlyName);
  
  // Consider to call Dispose() on the certificate after it's being used, avaliable in .NET 4.6 and later
}
```

In Java-Code greifen Sie über den Speicher „Windows-MY“ unter Verwendung des Felds für den Antragstellernamen auf das Zertifikat zu (siehe [Öffentliches Schlüsselzertifikat](https://en.wikipedia.org/wiki/Public_key_certificate)). Der folgende Code zeigt, wie Sie ein Zertifikat für einen privaten Schlüssel laden:

```java
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.bind.annotation.RequestMapping;
import java.security.KeyStore;
import java.security.cert.Certificate;
import java.security.PrivateKey;

...
KeyStore ks = KeyStore.getInstance("Windows-MY");
ks.load(null, null); 
Certificate cert = ks.getCertificate("<subject-cn>");
PrivateKey privKey = (PrivateKey) ks.getKey("<subject-cn>", ("<password>").toCharArray());

// Use the certificate and key
...
```

Informationen zu Sprachen, die keine Unterstützung oder nur unzureichende Unterstützung für den Windows-Zertifikatspeicher bieten, finden Sie unter [Laden des Zertifikats aus einer Datei](#load-certificate-from-file).

## <a name="load-certificate-from-file"></a>Laden des Zertifikats aus einer Datei

Wenn Sie eine manuell hochgeladene Zertifikatsdatei laden müssen, sollte der Upload eher über [FTPS](deploy-ftp.md) statt beispielsweise über [Git](deploy-local-git.md) erfolgen. Halten Sie vertrauliche Daten wie beispielsweise ein privates Zertifikat aus der Quellcodeverwaltung heraus.

> [!NOTE]
> ASP.NET und ASP.NET Core unter Windows müssen auf den Zertifikatspeicher auch dann zugreifen, wenn Sie ein Zertifikat aus einer Datei laden. Laden Sie zum Laden einer Zertifikatsdatei in eine Windows .NET-App das aktuelle Benutzerprofil mit dem folgenden Befehl in <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>:
>
> ```azurecli-interactive
> az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
> ```
>
> Bei der Verwendung von Zertifikaten in Ihrem Code wird die TLS-Funktionalität in App Service genutzt. Dazu muss Ihre App im Tarif **Basic** oder höher ausgeführt werden.

Im folgenden C#-Beispiel wird ein öffentliches Zertifikat aus einem relativen Pfad in die App geladen:

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("~/<relative-path-to-cert-file>");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Informationen zum Laden eines TLS-/SSL-Zertifikats aus einer Datei in Node.js, PHP, Python, Java oder Ruby finden Sie in der Dokumentation für die jeweilige Sprache oder Webplattform.

## <a name="load-certificate-in-linuxwindows-containers"></a>Laden eines Zertifikats in Linux-/Windows-Containern

Die App-Einstellung `WEBSITE_LOAD_CERTIFICATES` macht die angegebenen Zertifikate für Ihre Windows- oder Linux-Container-Apps (einschließlich integrierter Linux-Container) als Dateien zugänglich. Die Dateien befinden sich in den folgenden Verzeichnissen:

| Containerplattform | Öffentliche Zertifikate | Private Zertifikate |
| - | - | - |
| Windows-Container | `C:\appservice\certificates\public` | `C:\appservice\certificates\private` |
| Linux-Container | `/var/ssl/certs` | `/var/ssl/private` |

Die Zertifikatsdateinamen sind die Zertifikatfingerabdrücke. 

> [!NOTE]
> Von App Service werden die Zertifikatpfade in Windows-Container als Umgebungsvariablen (`WEBSITE_PRIVATE_CERTS_PATH`, `WEBSITE_INTERMEDIATE_CERTS_PATH`, `WEBSITE_PUBLIC_CERTS_PATH` und `WEBSITE_ROOT_CERTS_PATH`) eingefügt. Für den Fall, dass sich die Zertifikatpfade später ändern, ist es besser, auf den Zertifikatpfad mit den Umgebungsvariablen zu verweisen, anstatt einen hartcodierten Zertifikatpfad zu verwenden.
>

Darüber hinaus laden [Windows Server Core-Container](configure-custom-container.md#supported-parent-images) die Zertifikate automatisch in den Zertifikatspeicher (in **LocalMachine\My** ). Verwenden Sie zum Laden der Zertifikate das gleiche Muster wie beim [Laden eines Zertifikats in Windows-Apps](#load-certificate-in-windows-apps). Verwenden Sie für Windows Nano-basierte Container die oben angegebenen Dateipfade, um [das Zertifikat direkt aus der Datei zu laden](#load-certificate-from-file).

Der folgende C#-Code zeigt, wie ein öffentliches Zertifikat in eine Linux-App geladen wird.

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("/var/ssl/certs/<thumbprint>.der");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Informationen zum Laden eines TLS-/SSL-Zertifikats aus einer Datei in Node.js, PHP, Python, Java oder Ruby finden Sie in der Dokumentation für die jeweilige Sprache oder Webplattform.

## <a name="more-resources"></a>Weitere Ressourcen

* [Schützen eines benutzerdefinierten DNS-Namens mit einer TLS-/SSL-Bindung in Azure App Service](configure-ssl-bindings.md)
* [Erzwingen von HTTPS](configure-ssl-bindings.md#enforce-https)
* [Erzwingen von TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [Häufig gestellte Fragen: App Service-Zertifikate](./faq-configuration-and-management.md)