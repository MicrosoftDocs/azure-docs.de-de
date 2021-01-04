---
title: 'Azure Active Directory: Zertifikatbasierte Authentifizierung'
description: Erfahren Sie, wie Sie die zertifikatbasierte Authentifizierung in Ihrer Umgebung konfigurieren.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 3ba84bb3ee38981217e72f8372a836b03647083d
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861339"
---
# <a name="get-started-with-certificate-based-authentication-in-azure-active-directory"></a>Erste Schritte mit zertifikatbasierter Authentifizierung in Azure Active Directory

Bei Verwendung der zertifikatbasierten Authentifizierung können Sie von Azure Active Directory mit einem Clientzertifikat auf einem Windows-, Android- oder iOS-Gerät authentifiziert werden, wenn Sie Ihr Exchange Online-Konto mit folgenden Anwendungen oder Clients verbinden:

- Mobile Microsoft-Anwendungen wie Microsoft Outlook und Microsoft Word
- Exchange ActiveSync-Clients (EAS)

Wenn Sie dieses Feature konfigurieren, ist es bei bestimmten E-Mail- und Microsoft Office-Anwendungen auf Ihrem mobilen Gerät nicht länger erforderlich, einen Benutzernamen und ein Kennwort einzugeben.

Dieses Thema:

- Informiert Sie über die Schritte zum Konfigurieren und Nutzen zertifikatbasierter Authentifizierung für Benutzer von Mandanten in Office 365 Enterprise-, Business-, Education- und US Government-Plänen. Dieses Feature ist als Vorversion in Office 365 China-, US Government Defense- und US Government Federal-Plänen verfügbar.
- Setzt voraus, dass Sie bereits über eine [Public Key-Infrastruktur (PKI)](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831740(v=ws.11)) verfügen und [AD FS](../hybrid/how-to-connect-fed-whatis.md) konfiguriert haben.

## <a name="requirements"></a>Requirements (Anforderungen)

Damit Sie eine zertifikatbasierte Authentifizierung konfigurieren können, muss Folgendes zutreffen:

- Die zertifikatbasierte Authentifizierung (Certificate-Based Authentication, CBA) wird nur für Verbundumgebungen für Browseranwendungen, native Clients mit moderner Authentifizierung (ADAL) oder MSAL-Bibliotheken unterstützt. Die einzige Ausnahme ist das EAS-Protokoll (Exchange ActiveSync) für Exchange Online (EXO), das sowohl für Verbund- als auch verwaltete Konten verwendet werden kann.
- Die Stammzertifizierungsstelle und alle Zwischenzertifizierungsstellen müssen in Azure Active Directory konfiguriert sein.
- Jede Zertifizierungsstelle muss über eine Zertifikatsperrliste verfügen, auf die über eine Internet-URL verwiesen werden kann.
- Sie müssen mindestens eine Zertifizierungsstelle in Azure Active Directory konfiguriert haben. Die entsprechenden Schritte finden Sie im Abschnitt [Konfigurieren Sie die Zertifizierungsstellen](#step-2-configure-the-certificate-authorities).
- Bei Exchange ActiveSync-Clients muss das Clientzertifikat über die routingfähige E-Mail-Adresse des Benutzers in Exchange Online verfügen (entweder als Prinzipalname oder als Wert des RFC822-Namens im Feld „Alternativer Antragstellername“). Azure Active Directory ordnet den RFC822-Wert dem Attribut für die Proxyadresse innerhalb des Verzeichnisses zu.
- Ihr Clientgerät benötigt Zugriff auf mindestens eine Zertifizierungsstelle, die Clientzertifikate ausstellt.
- Ein Clientzertifikat für die Clientauthentifizierung muss für Ihren Client ausgestellt sein.

>[!IMPORTANT]
>Eine Zertifikatsperrliste darf maximal 20 MB groß sein, damit sie erfolgreich von Azure Active Directory heruntergeladen und zwischengespeichert werden kann, und der Download der Zertifikatsperrliste darf maximal zehn Sekunden dauern.  Wenn Azure Active Directory eine Zertifikatsperrliste nicht herunterladen kann, sind zertifikatbasierte Authentifizierungen mit Zertifikaten, die von der entsprechenden Zertifizierungsstelle ausgestellt wurden, nicht erfolgreich. Zur Einhaltung der Größenbeschränkungen für CRL-Dateien empfiehlt es sich, eine angemessene Zertifikatlebensdauer zu verwenden und abgelaufene Zertifikate zu bereinigen.

## <a name="step-1-select-your-device-platform"></a>Schritt 1: Auswählen Ihrer Geräteplattform

Als ersten Schritt müssen Sie für Ihre Geräteplattform Folgendes überprüfen:

- Die Unterstützung mobiler Office-Anwendungen
- Die spezifischen Implementierungsanforderungen

Die entsprechenden Informationen sind für die folgenden Geräteplattformen vorhanden:

- [Android](active-directory-certificate-based-authentication-android.md)
- [iOS](active-directory-certificate-based-authentication-ios.md)

## <a name="step-2-configure-the-certificate-authorities"></a>Schritt 2: Konfigurieren der Zertifizierungsstellen

Laden Sie zum Konfigurieren Ihrer Zertifizierungsstellen in Azure Active Directory für jede Zertifizierungsstelle Folgendes hoch:

* Den öffentlichen Teil des Zertifikats im Format *CER*
* Die Internet-URLs der Zertifikatsperrlisten

So sieht das Schema für eine Zertifizierungsstelle aus:

```csharp
    class TrustedCAsForPasswordlessAuth
    {
       CertificateAuthorityInformation[] certificateAuthorities;
    }

    class CertificateAuthorityInformation

    {
        CertAuthorityType authorityType;
        X509Certificate trustedCertificate;
        string crlDistributionPoint;
        string deltaCrlDistributionPoint;
        string trustedIssuer;
        string trustedIssuerSKI;
    }

    enum CertAuthorityType
    {
        RootAuthority = 0,
        IntermediateAuthority = 1
    }
```

Für die Konfiguration können Sie [Azure Active Directory PowerShell, Version 2](/powershell/azure/active-directory/install-adv2) verwenden:

1. Starten Sie Windows PowerShell mit Administratorrechten.
2. Installieren Sie das Azure AD-Modul Version [2.0.0.33](https://www.powershellgallery.com/packages/AzureAD/2.0.0.33) oder höher.

```powershell
    Install-Module -Name AzureAD –RequiredVersion 2.0.0.33
```

Im ersten Konfigurationsschritt müssen Sie eine Verbindung mit Ihrem Mandanten herstellen. Sobald eine Verbindung mit Ihrem Mandanten hergestellt ist, können Sie die in Ihrem Verzeichnis definierten vertrauenswürdigen Zertifizierungsstellen überprüfen, hinzufügen, löschen und ändern.

### <a name="connect"></a>Verbinden

Stellen Sie eine Verbindung mit Ihrem Mandanten mit dem [Connect-AzureAD](/powershell/module/azuread/connect-azuread)-Cmdlet her:

```azurepowershell
    Connect-AzureAD
```

### <a name="retrieve"></a>Gerätehandle

Rufen Sie die in Ihrem Verzeichnis definierten vertrauenswürdigen Zertifizierungsstellen mit dem [Get-AzureADTrustedCertificateAuthority](/powershell/module/azuread/get-azureadtrustedcertificateauthority)-Cmdlet ab.

```azurepowershell
    Get-AzureADTrustedCertificateAuthority
```

### <a name="add"></a>Hinzufügen

Um eine vertrauenswürdige Zertifizierungsstelle mit dem Cmdlet [New-AzureADTrustedCertificateAuthority](/powershell/module/azuread/new-azureadtrustedcertificateauthority) zu erstellen, legen Sie das Attribut **crlDistributionPoint** auf den richtigen Wert fest:

```azurepowershell
    $cert=Get-Content -Encoding byte "[LOCATION OF THE CER FILE]"
    $new_ca=New-Object -TypeName Microsoft.Open.AzureAD.Model.CertificateAuthorityInformation
    $new_ca.AuthorityType=0
    $new_ca.TrustedCertificate=$cert
    $new_ca.crlDistributionPoint="<CRL Distribution URL>"
    New-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $new_ca
```

### <a name="remove"></a>Remove (Entfernen)

Entfernen Sie eine vertrauenswürdige Zertifizierungsstelle mit dem [Remove-AzureADTrustedCertificateAuthority](/powershell/module/azuread/remove-azureadtrustedcertificateauthority)-Cmdlet:

```azurepowershell
    $c=Get-AzureADTrustedCertificateAuthority
    Remove-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[2]
```

### <a name="modify"></a>Ändern

Ändern Sie eine vertrauenswürdige Zertifizierungsstelle mit dem [Set-AzureADTrustedCertificateAuthority](/powershell/module/azuread/set-azureadtrustedcertificateauthority)-Cmdlet:

```azurepowershell
    $c=Get-AzureADTrustedCertificateAuthority
    $c[0].AuthorityType=1
    Set-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[0]
```

## <a name="step-3-configure-revocation"></a>Schritt 3: Konfigurieren der Sperrung

Zum Sperren eines Clientzertifikats ruft Azure Active Directory die Zertifikatsperrliste von den URLs ab, die als Teil der Informationen zur Zertifizierungsstelle hochgeladen wurden, und platziert diese Liste im Zwischenspeicher. Anhand des Zeitstempels der letzten Veröffentlichung (Eigenschaft **Effective Date** ) in der Zertifikatsperrliste wird sichergestellt, dass die Liste noch gültig ist. Der Verweis auf die Zertifikatsperrliste erfolgt regelmäßig, um den Zugriff auf Zertifikate zu sperren, die in dieser Liste enthalten sind.

Wenn eine schnellere Sperrung erforderlich ist (beispielsweise beim Verlust eines Geräts), kann der Autorisierungstoken des Benutzers für ungültig erklärt werden. Um das Autorisierungstoken für ungültig zu erklären, legen Sie das Feld **StsRefreshTokenValidFrom** für diesen Benutzer über Windows PowerShell fest. Das Feld **StsRefreshTokenValidFrom** muss für jeden Benutzer aktualisiert werden, für den der Zugriff gesperrt werden soll.

Um sicherzustellen, dass die Sperrung aufrechterhalten wird, muss die Eigenschaft **Effective Date** der Zertifikatsperrliste auf ein Datum festgelegt werden, das nach dem Wert von **StsRefreshTokenValidFrom** liegt. Stellen Sie außerdem sicher, dass das betroffene Zertifikat in der Zertifikatsperrliste enthalten ist.

Die unten aufgeführten Schritte zeigen, wie Sie das Autorisierungstoken aktualisieren und für ungültig erklären, indem Sie das Feld **StsRefreshTokenValidFrom** festlegen.

**So konfigurieren Sie die Sperrung:**

1. Verbinden Sie sich mit Administratoranmeldeinformationen mit dem MSOL-Dienst:

```powershell
        $msolcred = get-credential
        connect-msolservice -credential $msolcred
```

2. Rufen Sie den aktuellen StsRefreshTokensValidFrom-Wert für einen Benutzer ab:

```powershell
        $user = Get-MsolUser -UserPrincipalName test@yourdomain.com`
        $user.StsRefreshTokensValidFrom
```

3. Legen Sie einen neuen StsRefreshTokensValidFrom-Wert für den Benutzer fest, der mit dem aktuellen Zeitstempel übereinstimmt:

```powershell
        Set-MsolUser -UserPrincipalName test@yourdomain.com -StsRefreshTokensValidFrom ("03/05/2016")
```

Das festgelegte Datum muss in der Zukunft liegen. Wenn das Datum nicht in der Zukunft liegt, wird die **StsRefreshTokensValidFrom** -Eigenschaft nicht festgelegt. Wenn das Datum in der Zukunft liegt, wird **StsRefreshTokensValidFrom** auf die aktuelle Uhrzeit festgelegt (nicht das Datum, das mit dem Befehl „Set-MsolUser“ angegeben ist).

## <a name="step-4-test-your-configuration"></a>Schritt 4: Testen Ihrer Konfiguration

### <a name="testing-your-certificate"></a>Testen Ihres Zertifikats

Als ersten Konfigurationstest sollten Sie versuchen, sich bei [OWA](https://outlook.office365.com) oder [SharePoint Online](https://microsoft.sharepoint.com) mithilfe Ihres **Gerätebrowsers** anzumelden.

Wenn die Anmeldung erfolgreich ist, wissen Sie:

- Das Benutzerzertifikat wurde auf Ihrem Testgerät bereitgestellt.
- AD FS ist richtig konfiguriert.

### <a name="testing-office-mobile-applications"></a>Testen mobiler Office-Anwendungen

**So testen Sie die zertifikatbasierte Authentifizierung für Ihre mobile Office-Anwendung:**

1. Installieren Sie auf Ihrem Testgerät eine mobile Office-Anwendung (z. B. OneDrive).
3. Starten Sie die Anwendung.
4. Geben Sie Ihren Benutzernamen ein, und wählen Sie das Benutzerzertifikat aus, das verwendet werden soll.

Sie sollten erfolgreich angemeldet werden.

### <a name="testing-exchange-activesync-client-applications"></a>Testen von Exchange ActiveSync-Clientanwendungen

Um über die zertifikatbasierte Authentifizierung auf Exchange ActiveSync (EAS) zuzugreifen, muss ein EAS-Profil mit dem Clientzertifikat für die Anwendung verfügbar sein.

Das EAS-Profil muss folgende Informationen enthalten:

- Das Benutzerzertifikat, das für die Authentifizierung verwendet werden soll.

- Der EAS-Endpunkt (z.B. outlook.office365.com)

Sie können ein EAS-Profil über eine MDM-Lösung (Mobile Device Management, mobile Geräteverwaltung) wie Intune konfigurieren und auf dem Gerät platzieren oder das Zertifikat manuell im EAS-Profil auf dem Gerät platzieren.

### <a name="testing-eas-client-applications-on-android"></a>Testen von EAS-Clientanwendungen auf Android

**So testen Sie die Zertifikatauthentifizierung:**

1. Konfigurieren Sie ein EAS-Profil in der Anwendung, das die im vorigen Abschnitt beschriebenen Anforderungen erfüllt.
2. Öffnen Sie die Anwendung, und stellen Sie sicher, dass die E-Mail synchronisiert wird.

## <a name="next-steps"></a>Nächste Schritte

[Weitere Informationen zur zertifikatbasierten Authentifizierung auf Android-Geräten](active-directory-certificate-based-authentication-android.md)

[Weitere Informationen zur zertifikatbasierten Authentifizierung auf iOS-Geräten](active-directory-certificate-based-authentication-ios.md)
