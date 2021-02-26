---
title: 'Media Services: Apple FairPlay-Lizenzunterstützung'
description: Dieses Thema enthält eine Übersicht über die Anforderungen und die Konfiguration der Apple FairPlay-Lizenz.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 5a00ba8000f33e8e50b1a5d01f40a68a7fe2442d
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98956190"
---
# <a name="apple-fairplay-license-requirements-and-configuration"></a>Anforderungen und Konfiguration der Apple FairPlay-Lizenz

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services ermöglicht Ihnen das Verschlüsseln von HLS-Inhalten mit **Apple FairPlay** (AES-128 CBC). Media Services bietet darüber hinaus einen Dienst für die Bereitstellung von FairPlay-Lizenzen. Wenn der Player versucht, Ihre durch FairPlay geschützten Inhalte wiederzugeben, wird eine Anforderung zum Erwerben einer Lizenz an den Lizenzbereitstellungsdienst gesendet. Wenn der Lizenzdienst die Anforderung genehmigt, wird die Lizenz ausgegeben. Diese wird an den Client gesendet und zum Entschlüsseln und Wiedergeben des angegebenen Inhalts verwendet.

Media Services bietet außerdem APIs, mit denen Sie Ihre FairPlay-Lizenzen konfigurieren können. Dieses Thema behandelt die FairPlay-Lizenzanforderungen und zeigt, wie Sie eine **FairPlay**-Lizenz mithilfe von Media Services-APIs konfigurieren können. 

## <a name="requirements"></a>Requirements (Anforderungen)

Die folgenden Angaben sind erforderlich, wenn Sie Ihre HLS-Inhalte mit **Apple FairPlay** verschlüsseln und Media Services verwenden, um FairPlay-Lizenzen bereitzustellen:

* Registrieren Sie sich für das [Apple Developer Program](https://developer.apple.com/)(Apple-Entwicklerprogramm).
* Apple setzt voraus, dass der Inhaltsbesitzer über das [Bereitstellungspaket](https://developer.apple.com/contact/fps/)verfügt. Geben Sie an, dass Sie bereits KSM (Key Security Module) mit Media Services implementiert haben, und dass Sie das endgültige FPS-Paket anfordern. Das endgültige FPS-Paket enthält Anweisungen zum Generieren des Zertifikats und zum Abrufen des geheimen Anwendungsschlüssels (ASK). Sie verwenden ASK, um FairPlay zu konfigurieren.
* Folgendes muss seitens der Media Services-Schlüssel-/Lizenzbereitstellung festgelegt werden:

    * **App Cert (AC)** : Dies ist eine PFX-Datei, die den privaten Schlüssel enthält. Sie erstellen diese Datei und verschlüsseln sie mit einem Kennwort. Die PFX-Datei muss im Base64-Format vorliegen.

        Die folgenden Schritte beschreiben, wie eine PFX-Zertifikatdatei für FairPlay generiert wird:

        1. Installieren von OpenSSL von https://slproweb.com/products/Win32OpenSSL.html.

            Wechseln Sie zum Ordner, in dem das FairPlay-Zertifikat und andere von Apple übermittelte Dateien enthalten sind.
        2. Führen Sie den folgenden Befehl an der Befehlszeile aus: Dadurch wird die CER-Datei in eine PEM-Datei konvertiert.

            „C:\OpenSSL-Win32\bin\openssl.exe“ x509 -inform der -in FairPlay.cer -out FairPlay-out.pem
        3. Führen Sie den folgenden Befehl an der Befehlszeile aus: Dadurch wird die PEM-Datei in eine PFX-Datei mit dem privaten Schlüssel konvertiert. Das Kennwort für die PFX-Datei wird dann von OpenSSL angefordert.

            „C:\OpenSSL-Win32\bin\openssl.exe“ pkcs12 -export -out FairPlay-out.pfx -inkey privatekey.pem -in FairPlay-out.pem -passin file:privatekey-pem-pass.txt
            
    * **App Cert-Kennwort**: Das Kennwort zum Erstellen der PFX-Datei.
    * **ASK**: Dieser Schlüssel wird bei der Generierung der Zertifizierung über das Apple Developer-Portal empfangen. Jedes Entwicklungsteam erhält einen eindeutigen ASK. Speichern Sie eine Kopie des ASK an einem sicheren Ort. Sie müssen den ASK später als FairPlayAsk für Media Services konfigurieren.
    
* Folgendes muss seitens des FPS-Clients festgelegt werden:

  * **App Cert (AC)** : CER-/DER-Datei mit dem öffentlichen Schlüssel, den das Betriebssystem zur Verschlüsselung bestimmter Nutzlasten verwendet. Media Services muss den Schlüssel kennen, da er vom Player benötigt wird. Der Schlüsselbereitstellungsdienst entschlüsselt den Schlüssel mithilfe des entsprechenden privaten Schlüssels.

* Um einen über FairPlay verschlüsselten Stream wiederzugeben, rufen Sie zuerst den echten ASK ab, und generieren Sie dann ein echtes Zertifikat. Dieser Prozess erstellt alle drei Teile:

  * DER-Datei
  * PFX-Datei
  * Kennwort für die PFX-Datei
  
> [!NOTE]
> Azure Media Services überprüft das Ablaufdatum des Zertifikats während der Paketerstellung und der Schlüsselübermittlung nicht. Nach Ablauf des Zertifikats ist es weiterhin funktionsfähig.

## <a name="fairplay-and-player-apps"></a>FairPlay und Player-Apps

Wenn Ihr Inhalt mit **Apple FairPlay** verschlüsselt ist, werden die einzelnen Audio- und Videosamples mit dem **AES-128 CBC**-Modus verschlüsselt. **FairPlay Streaming** (FPS) ist in die Gerätebetriebssysteme integriert und wird von iOS und Apple TV nativ unterstützt. Safari unter OS X ermöglicht FPS durch Unterstützung der EME-Schnittstelle (Encrypted Media Extensions).

Azure Media Player unterstützt zudem die FairPlay-Wiedergabe. Weitere Informationen finden Sie in der [Azure Media Player-Dokumentation](https://amp.azure.net/libs/amp/latest/docs/index.html).

Sie können Ihre eigenen Player-Apps mit dem iOS SDK entwickeln. Damit FairPlay-Inhalte wiedergegeben werden können, müssen Sie das Lizenzaustauschprotokoll implementieren. Dieses Protokoll wird nicht von Apple angegeben. Es ist jeder App freigestellt, wie sie Anforderungen zur Schlüsselübermittlung sendet. Der Media Services-FairPlay-Schlüsselübermittlungsdienst erwartet das SPC als POST-Nachricht mit Verschlüsselung vom Typ „www-form-url“ im folgenden Format:

```
spc=<Base64 encoded SPC>
```

## <a name="fairplay-configuration-net-example"></a>FairPlay-Konfiguration – .NET-Beispiel

FairPlay-Lizenzen können mithilfe von Media Services-APIs konfiguriert werden. Wenn der Player versucht, Ihre durch FairPlay geschützten Inhalte wiederzugeben, wird eine Anforderung zum Erwerben einer Lizenz an den Lizenzbereitstellungsdienst gesendet. Wenn der Lizenzdienst die Anforderung genehmigt, stellt der Dienst die Lizenz aus. Sie wird an den Client gesendet und dient zum Entschlüsseln und Wiedergeben des angegebenen Inhalts.

> [!NOTE]
> Üblicherweise sollten Sie FairPlay-Richtlinienoptionen nur einmal konfigurieren, da Sie nur einen Satz aus Zertifikat und ASK haben.

Im folgenden Beispiel wird das [Media Services .NET SDK](/dotnet/api/microsoft.azure.management.media.models?view=azure-dotnet) für die Lizenzkonfiguration verwendet.

```csharp
private static ContentKeyPolicyFairPlayConfiguration ConfigureFairPlayPolicyOptions()
{

    string askHex = "";
    string FairPlayPfxPassword = "";

    var appCert = new X509Certificate2("FairPlayPfxPath", FairPlayPfxPassword, X509KeyStorageFlags.Exportable);

    byte[] askBytes = Enumerable
        .Range(0, askHex.Length)
        .Where(x => x % 2 == 0)
        .Select(x => Convert.ToByte(askHex.Substring(x, 2), 16))
        .ToArray();

    ContentKeyPolicyFairPlayConfiguration fairPlayConfiguration =
    new ContentKeyPolicyFairPlayConfiguration
    {
        Ask = askBytes,
        FairPlayPfx =
                Convert.ToBase64String(appCert.Export(X509ContentType.Pfx, FairPlayPfxPassword)),
        FairPlayPfxPassword = FairPlayPfxPassword,
        RentalAndLeaseKeyType =
                ContentKeyPolicyFairPlayRentalAndLeaseKeyType
                .PersistentUnlimited,
        RentalDuration = 2249 // in seconds
    };

    return fairPlayConfiguration;
}
```

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über das [Schützen mit DRM](protect-with-drm.md).
