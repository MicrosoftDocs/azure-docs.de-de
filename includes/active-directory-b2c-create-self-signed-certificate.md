---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/27/2021
ms.author: mimart
ms.openlocfilehash: 3458a4f3ba337d4c470c21fa5d89b28f5f2701a3
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98916918"
---
Wenn Sie noch nicht über ein Zertifikat verfügen, können Sie für dieses Tutorial ein selbstsigniertes Zertifikat verwenden. Ein selbst signiertes Zertifikat ist ein Sicherheitszertifikat, das nicht von einer Zertifizierungsstelle signiert wurde. Es bietet nicht alle Sicherheitsgarantien eines von einer Zertifizierungsstelle signierten Zertifikats. 

# <a name="windows"></a>[Windows](#tab/windows)

Verwenden Sie unter Windows das PowerShell-Cmdlet [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate), um ein Zertifikat zu generieren.

1. Führen Sie diesen PowerShell-Befehl aus, um ein selbstsigniertes Zertifikat zu generieren. Ändern Sie das Argument `-Subject` entsprechend Ihrer Anwendung und des Azure AD B2C-Mandantennamens. Sie können auch das `-NotAfter`-Datum anpassen, um einen anderen Ablaufzeitpunkt für das Zertifikat anzugeben.

    ```PowerShell
    New-SelfSignedCertificate `
        -KeyExportPolicy Exportable `
        -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
        -KeyAlgorithm RSA `
        -KeyLength 2048 `
        -KeyUsage DigitalSignature `
        -NotAfter (Get-Date).AddMonths(12) `
        -CertStoreLocation "Cert:\CurrentUser\My"
    ```

1. Öffnen Sie **Benutzerzertifikate verwalten** > **Aktueller Benutzer** > **Persönliche** > **Zertifikate** > *yourappname.yourtenant.onmicrosoft.com*.
1. Wählen Sie das Zertifikat und dann **Aktion** > **Alle Aufgaben** > **Exportieren** aus.
1. Wählen Sie **Ja** > **Weiter** > **Ja, privaten Schlüssel exportieren** > **Weiter** aus.
1. Übernehmen Sie die Standardeinstellungen für **Format der zu exportierenden Datei** aus.
1. Geben Sie ein Kennwort für das Zertifikat an.

Damit das Kennwort für die PFX-Datei in Azure AD B2C akzeptiert wird, muss es statt mit „AES256-SHA256“ mit der Option „TripleDES-SHA1“ im Exporthilfsprogramm des Windows-Zertifikatspeichers verschlüsselt werden.

# <a name="macos"></a>[macOS](#tab/macos)

Verwenden Sie unter macOS den [Zertifikatsassistenten](https://support.apple.com/guide/keychain-access/aside/glosa3ed0609/11.0/mac/11.0) in Keychain Access, um ein Zertifikat zu generieren.

1. Führen Sie die Anweisungen unter [Erstellen von selbstsignierten Zertifikaten in Keychain Access unter Mac OS](https://support.apple.com/guide/keychain-access/kyca8916/mac) aus.
1. Wählen Sie in der Keychain Access-App auf Ihrem Mac das Zertifikat aus, das Sie erstellt haben.
1. Wählen Sie „Datei“ > „Elemente exportieren“ aus.
1. Wählen Sie einen Dateinamen aus, um das Zertifikat zu speichern. Beispielsweise **self-signed-certificate.p12**. 
1. Wählen Sie als **Dateiformat** die Option **Privater Informationsaustausch (.p12)** aus.
1. Wählen Sie **Speichern** aus.
1. Geben Sie ein **Kennwort** ein, und **überprüfen** Sie es.
1. Ändern Sie die Dateinamenerweiterung in `.pfx`. Beispielsweise **self-signed-certificate.pfx**. 

---
