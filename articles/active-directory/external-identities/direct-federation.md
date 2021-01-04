---
title: Direkter Verbund mit einem Identitätsanbieter für B2B – Azure AD
description: Herstellen eines direkten Verbunds mit einem SAML- oder WS-Verbund-Identitätsanbieter, damit sich Gäste bei Ihren Azure AD-Apps anmelden können
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/24/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5beae56a5d38c4620481c27c3f42c52602984e6b
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96860625"
---
# <a name="direct-federation-with-ad-fs-and-third-party-providers-for-guest-users-preview"></a>Direkter Verbund mit AD FS und Drittanbietern für Gastbenutzer (Preview)

> [!NOTE]
>  Ein direkter Verbund ist eine Public Previewfunktion von Azure Active Directory. Weitere Informationen zu Vorschauversionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

In diesem Artikel wird beschrieben, wie Sie einen direkten Verbund mit einer anderen Organisation für die B2B-Zusammenarbeit einrichten. Sie können einen direkten Verbund mit jeder Organisation einrichten, deren Identitätsanbieter das SAML 2.0- oder WS-Verbund-Protokoll unterstützt.
Wenn Sie einen direkten Verbund mit dem Identitätsanbieter eines Partners einrichten, können neue Gastbenutzer aus dieser Domäne ihr eigenes, vom Identitätsanbieter verwaltetes Organisationskonto verwenden, um sich bei Ihrem Azure AD-Mandanten anzumelden und mit Ihnen zusammenzuarbeiten. Es ist nicht erforderlich, dass der Gastbenutzer ein separates Azure AD-Konto erstellt.
> [!NOTE]
> Direkte Verbundgastbenutzer müssen sich über einen Link anmelden, der den Mandantenkontext enthält (z. B. `https://myapps.microsoft.com/?tenantid=<tenant id>` oder `https://portal.azure.com/<tenant id>` bzw. `https://myapps.microsoft.com/\<verified domain>.onmicrosoft.com` bei einer überprüften Domäne). Direkte Links zu Anwendungen und Ressourcen funktionieren ebenfalls, sofern sie den Mandantenkontext enthalten. Direkte Verbundgastbenutzer können sich derzeit nicht über allgemeine Endpunkte, die keinen Mandantenkontext aufweisen, anmelden. Beispielsweise führt die Verwendung von `https://myapps.microsoft.com`, `https://portal.azure.com` oder `https://teams.microsoft.com` zu einem Fehler.
 
## <a name="when-is-a-guest-user-authenticated-with-direct-federation"></a>Wann wird ein Gastbenutzer mit direktem Verbund authentifiziert?
Nachdem Sie den direkten Verbund mit einer Organisation eingerichtet haben, werden alle neuen Gastbenutzer, die Sie einladen, mithilfe des direkten Verbunds authentifiziert. Es ist wichtig zu beachten, dass durch das Einrichten des direkten Verbunds nicht die Authentifizierungsmethode für Gastbenutzer geändert wird, die bereits eine Einladung von Ihnen eingelöst haben. Im Folgenden finden Sie einige Beispiele:
 - Wenn Gastbenutzer bereits Einladungen von Ihnen eingelöst haben und Sie anschließend direkten Verbund mit deren Organisation einrichten, verwenden diese Gastbenutzer weiterhin dieselbe Authentifizierungsmethode, die sie vor dem Einrichten des direkten Verbunds verwendet haben.
 - Wenn Sie den direkten Verbund mit einer Partnerorganisation einrichten, Gastbenutzer einladen und die Partnerorganisation dann später zu Azure AD wechselt, verwenden die Gastbenutzer, die bereits Einladungen eingelöst haben, weiterhin den direkten Verbund, solange die direkte Verbundrichtlinie in Ihrem Mandanten vorhanden ist.
 - Wenn Sie den direkten Verbund mit einer Partnerorganisation löschen, können sich alle Gastbenutzer, die zurzeit den direkt Verbund verwenden, nicht mehr anmelden.

In jedem dieser Szenarien können Sie die Authentifizierungsmethode eines Gastbenutzers aktualisieren, indem Sie das Gastbenutzerkonto aus Ihrem Verzeichnis löschen und den Benutzer erneut einladen.

Der direkte Verbund ist an Domänennamespaces gebunden, z. B. „contoso.com“ und „fabrikam.com“. Wenn Sie eine direkte Verbundkonfiguration mit AD FS oder einem Drittanbieter-Identitätsanbieter einrichten, ordnen Organisationen diesen Identitätsanbietern einen oder mehrere Domänennamespaces zu. 

## <a name="end-user-experience"></a>Endbenutzererfahrung 
Bei direktem Verbund melden sich Gastbenutzer mit ihren eigenen Organisationskonten bei Ihrem Azure AD-Mandanten an. Wenn sie auf freigegebene Ressourcen zugreifen und zur Anmeldung aufgefordert werden, werden direkte Verbundbenutzer zu ihrem Identitätsanbieter umgeleitet. Nach erfolgreicher Anmeldung werden sie an Azure AD zurückgeleitet, um auf Ressourcen zuzugreifen. Die Aktualisierungstoken von direkten Verbundbenutzern sind 12 Stunden lang gültig, wobei es sich um die [Standarddauer für Pass-Through-Aktualisierungstoken](../develop/active-directory-configurable-token-lifetimes.md#exceptions) in Azure AD handelt. Wenn der Verbundidentitätsanbieter einmaliges Anmelden (SSO) aktiviert hat, erfährt der Benutzer auch SSO, und es wird nach der ersten Authentifizierung keine Anmeldeaufforderung mehr angezeigt.

## <a name="limitations"></a>Einschränkungen

### <a name="dns-verified-domains-in-azure-ad"></a>DNS-verifizierte Domänen in Azure AD
Die Domäne, mit der Sie einen Verbund einrichten möchten, darf in Azure AD ***nicht** _ DNS-verifiziert werden. Es ist zulässig, einen direkten Verbund mit nicht verwalteten (E-Mail-verifizierten oder „viralen“) Azure AD-Mandanten einzurichten, da sie nicht DNS-verifiziert sind.

### <a name="authentication-url"></a>Authentifizierungs-URL
Der direkte Verbund ist nur für Richtlinien zulässig, bei denen die Domäne der Authentifizierungs-URL mit der Zieldomäne übereinstimmt, oder wenn es sich bei der Authentifizierungs-URL um einen dieser zulässigen Identitätsanbieter handelt (diese Liste kann geändert werden):

-   accounts.google.com
-   pingidentity.com
-   login.pingone.com
-   okta.com
-   oktapreview.com
-   okta-emea.com
-   my.salesforce.com
-   federation.exostar.com
-   federation.exostartest.com

Wenn Sie beispielsweise den direkten Verbund für _*fabrikam.com** einrichten, besteht die Authentifizierungs-URL `https://fabrikam.com/adfs` die Überprüfung. Ein Host in derselben Domäne wird ebenfalls bestehen, z. B. `https://sts.fabrikam.com/adfs`. Die Authentifizierungs-URL `https://fabrikamconglomerate.com/adfs` oder `https://fabrikam.com.uk/adfs` für dieselbe Domäne wird jedoch nicht bestehen.

### <a name="signing-certificate-renewal"></a>Signaturzertifikatverlängerung
Wenn Sie die Metadaten-URL in den Identitätsanbietereinstellungen angeben, verlängert Azure AD das Signaturzertifikat automatisch, wenn es abläuft. Wenn das Zertifikat jedoch aus irgendeinem Grund vor der Ablaufzeit rotiert wird, oder wenn Sie keine Metadaten-URL bereitstellen, kann Azure AD es nicht verlängern. In diesem Fall müssen Sie das Signaturzertifikat manuell aktualisieren.

### <a name="limit-on-federation-relationships"></a>Limit für Verbundbeziehungen
Derzeit werden maximal 1.000 Verbundbeziehungen unterstützt. Dieses Limit umfasst sowohl [interne Verbünde](/powershell/module/msonline/set-msoldomainfederationsettings) als auch direkte Verbünde.

### <a name="limit-on-multiple-domains"></a>Limit bei mehreren Domänen
Der direkte Verbund mit mehreren Domänen desselben Mandanten wird derzeit nicht unterstützt.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen
### <a name="can-i-set-up-direct-federation-with-a-domain-for-which-an-unmanaged-email-verified-tenant-exists"></a>Kann ich einen direkten Verbund mit einer Domäne einrichten, für die ein nicht verwalteter (per E-Mail verifizierten) Mandant vorhanden ist? 
Ja. Wenn die Domäne nicht verifiziert wurde und der Mandant keine [Übernahme durch den Administrator](../enterprise-users/domains-admin-takeover.md) erfahren hat, können Sie einen direkten Verbund mit dieser Domäne einrichten. Nicht verwaltete oder per E-Mail verifizierte Mandanten werden erstellt, wenn ein Benutzer eine B2B-Einladung einlöst oder eine Self-Service-Anmeldung für Azure AD über eine Domain durchführt, die derzeit nicht existiert. Sie können den direkten Verbund mit diesen Domänen einrichten. Wenn Sie versuchen, den direkten Verbund mit einer DNS-verifizierten Domäne einzurichten, entweder im Azure-Portal oder über die PowerShell, wird eine Fehlermeldung angezeigt.
### <a name="if-direct-federation-and-email-one-time-passcode-authentication-are-both-enabled-which-method-takes-precedence"></a>Welche Methode hat Vorrang, wenn Authentifizierung mittels direktem Verbund und mit E-Mail-Einmalkennung aktiviert ist?
Wenn direkter Verbund mit einer Partnerorganisation eingerichtet ist, hat dieser Vorrang vor der Authentifizierung per E-Mail-Einmalkennung für neue Gastbenutzer aus dieser Organisation. Wenn ein Gastbenutzer eine Einladung mit Authentifizierung mittels Einmalkennung eingelöst hat, bevor Sie den direkten Verbund einrichten, wird er die Authentifizierung mit Einmalkennung weiterhin verwenden. 
### <a name="does-direct-federation-address-sign-in-issues-due-to-a-partially-synced-tenancy"></a>Behandelt der direkte Verbund Anmeldeprobleme aufgrund eines teilweise synchronisierten Mandanten?
Nein, in diesem Szenario sollte die Funktion [E-Mail-Einmalkennung](one-time-passcode.md) verwendet werden. Ein „teilweise synchronisierter Mandant“ bezieht sich auf einen Azure AD-Partnermandanten, bei dem lokale Benutzeridentitäten nicht vollständig mit der Cloud synchronisiert sind. Ein Gast, dessen Identität noch nicht in der Cloud vorhanden ist, der aber versucht, Ihre B2B-Einladung einzulösen, kann sich dann nicht anmelden. Die Einmalkennungsfunktion gestattet diesem Gast die Anmeldung. Die direkte Verbundfunktion behandelt Szenarien, in denen der Gast über ein eigenes vom Identitätsanbieter verwaltetes Organisationskonto verfügt, die Organisation aber über gar keine Azure AD-Präsenz verfügt.

## <a name="step-1-configure-the-partner-organizations-identity-provider"></a>Schritt 1: Konfigurieren Sie den Identitätsanbieter der Partnerorganisation
Zunächst muss Ihre Partnerorganisation ihren Identitätsanbieter mit den erforderlichen Ansprüchen und Vertrauenspersonen vertraut machen. 

> [!NOTE]
> Wir verwenden als Beispiel Active Directory-Verbunddienste (AD FS), um zu veranschaulichen, wie ein Identitätsanbieter für den direkten Verbund konfiguriert wird. Beispiele zum Konfigurieren von AD FS als SAML 2.0- oder WS-Verbund-Identitätsanbieter zur Vorbereitung des direkten Verbunds finden Sie unter [Konfigurieren des direkten Verbunds mit AD FS](direct-federation-adfs.md).

### <a name="saml-20-configuration"></a>SAML 2.0-Konfiguration

Azure AD B2B kann so konfiguriert werden, dass es einen Verbund mit Identitätsanbietern bildet, die das SAML-Protokoll mit den unten aufgeführten spezifischen Anforderungen verwenden. Weitere Informationen zum Einrichten einer Vertrauensstellung zwischen Ihrem SAML-Identitätsanbieter und Azure AD finden Sie unter [Verwenden eines SAML 2.0-Identitätsanbieters für das einmalige Anmelden](../hybrid/how-to-connect-fed-saml-idp.md).  

> [!NOTE]
> Die Zieldomäne für den direkten Verbund darf nicht in Azure AD DNS-verifiziert sein. Die Domäne der Authentifizierungs-URL muss mit der Zieldomäne oder der Domäne eines zulässigen Identitätsanbieters übereinstimmen. Weitere Details finden Sie im Abschnitt [Einschränkungen](#limitations). 

#### <a name="required-saml-20-attributes-and-claims"></a>Erforderliche SAML 2.0-Attribute und -Ansprüche
In den folgenden Tabellen sind die Anforderungen für bestimmte Attribute und Ansprüche aufgeführt, die beim Drittanbieter-Identitätsanbieter konfiguriert werden müssen. Die folgenden Attribute müssen in der SAML 2.0-Antwort vom Identitätsanbieter empfangen werden, um einen direkten Verbund einzurichten. Diese Attribute können durch Verlinkung mit der XML-Datei des Online-Sicherheitstokendiensts oder durch manuelle Eingabe konfiguriert werden.

Erforderliche Attribute für die SAML 2.0-Antwort des Identitätsanbieters:

|attribute  |Wert  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Zielgruppe     |`urn:federation:MicrosoftOnline`         |
|Issuer (Aussteller)     |Der Aussteller-URI des Partneridentitätsanbieters, z. B. `http://www.example.com/exk10l6w90DHM0yi...`         |


Erforderliche Ansprüche für das vom Identitätsanbieter ausgegebene SAML 2.0-Token:

|attribute  |Wert  |
|---------|---------|
|NameID-Format     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

### <a name="ws-fed-configuration"></a>WS-Verbund-Konfiguration 
Azure AD B2B kann so konfiguriert werden, dass es einen Verbund mit Identitätsanbietern bildet, die das WS-Verbund-Protokoll mit einigen unten aufgeführten spezifischen Anforderungen verwenden. Derzeit sind die beiden WS-Verbund-Anbieter auf Kompatibilität mit Azure AD getestet, einschließlich AD FS und Shibboleth. Weitere Informationen zum Aufbau einer Vertrauensstellung der vertrauenden Seite zwischen einem WS-Verbund-kompatiblen Anbieter mit Azure AD finden Sie in dem „Artikel zur STS-Integration unter Verwendung von WS-Protokollen“, der in den [Dokumenten zur Kompatibilität von Azure AD-Identitätsanbietern](https://www.microsoft.com/download/details.aspx?id=56843) verfügbar ist.

> [!NOTE]
> Die Zieldomäne für den direkten Verbund darf nicht in Azure AD DNS-verifiziert sein. Die Domäne der Authentifizierungs-URL muss mit der Zieldomäne oder der Domäne eines zulässigen Identitätsanbieters übereinstimmen. Weitere Details finden Sie im Abschnitt [Einschränkungen](#limitations). 

#### <a name="required-ws-fed-attributes-and-claims"></a>Erforderliche WS-Verbund-Attribute und -Ansprüche

In den folgenden Tabellen sind die Anforderungen für bestimmte Attribute und Ansprüche aufgeführt, die beim Drittanbieter-WF-Verbund-Identitätsanbieter konfiguriert werden müssen. Zum Einrichten eines direkten Verbunds müssen die folgenden Attribute in der WS-Verbund-Nachricht vom Identitätsanbieter empfangen werden. Diese Attribute können durch Verlinkung mit der XML-Datei des Online-Sicherheitstokendiensts oder durch manuelle Eingabe konfiguriert werden.

Erforderliche Attribute in der WS-Verbund-Nachricht vom Identitätsanbieter:
 
|attribute  |Wert  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|Zielgruppe     |`urn:federation:MicrosoftOnline`         |
|Issuer (Aussteller)     |Der Aussteller-URI des Partneridentitätsanbieters, z. B. `http://www.example.com/exk10l6w90DHM0yi...`         |

Erforderliche Ansprüche für das vom Identitätsanbieter ausgegebene WS-Verbund-Token:

|attribute  |Wert  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

## <a name="step-2-configure-direct-federation-in-azure-ad"></a>Schritt 2: Direkten Verbund in Azure AD konfigurieren 
Als Nächstes konfigurieren Sie den direkten Verbund mit dem in Schritt 1 in Azure AD konfigurierten Identitätsanbieter. Sie können das Azure AD-Portal oder PowerShell verwenden. Es kann 5–10 Minuten dauern, bis die direkte Verbundrichtlinie wirksam wird. Versuchen Sie während dieser Zeit nicht, eine Einladung für die direkte Verbunddomäne einzulösen. Die folgenden Attribute sind erforderlich:
- Aussteller-URI des Partneridentitätsanbieters
- Passiver Authentifizierungsendpunkt des Partneridentitätsanbieters (nur HTTPS wird unterstützt)
- Zertifikat

### <a name="to-configure-direct-federation-in-the-azure-ad-portal"></a>So konfigurieren Sie direkten Verbund im Azure AD-Portal

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/). Wählen Sie im linken Bereich **Azure Active Directory** aus. 
2. Wählen Sie **Externe Identitäten** > **Alle Identitätsanbieter** aus.
3. Wählen Sie dann **Neuer SAML-/WS-Fed-IdP** aus.

    ![Screenshot der Schaltfläche zum Hinzufügen eines neuen SAML- oder WS-Verbund-Identitätsanbieters](media/direct-federation/new-saml-wsfed-idp.png)

4. Wählen Sie auf der Seite **Neuer SAML-/WS-Fed-IdP** unter **Identitätsanbieterprotokoll** die Option **SAML-** oder **WS-FED** aus.

    ![Screenshot der Schaltfläche „Analysieren“ auf der SAML- oder WS-Fed IdP-Seite](media/direct-federation/new-saml-wsfed-idp-parse.png)

5. Geben Sie den Domänennamen Ihrer Partnerorganisation ein, der der Zieldomänenname für den direkten Verbund ist.
6. Sie können eine Metadatendatei hochladen, um Metadatendetails aufzufüllen. Wenn Sie sich entschließen, Metadaten manuell einzugeben, geben Sie die folgenden Informationen ein:
   - Domänenname des Partneridentitätsanbieters
   - Entitäts-ID des Partneridentitätsanbieters
   - Passiver Requestorendpunkt des Partneridentitätsanbieters
   - Zertifikat
   > [!NOTE]
   > Metadaten-URL ist optional, wird aber dringend empfohlen. Wenn Sie die Metadaten-URL angeben, kann Azure AD das Signaturzertifikat automatisch verlängern, wenn es abläuft. Wenn das Zertifikat aus irgendeinem Grund vor der Ablaufzeit rotiert wird, oder wenn Sie keine Metadaten-URL bereitstellen, kann Azure AD es nicht verlängern. In diesem Fall müssen Sie das Signaturzertifikat manuell aktualisieren.

7. Wählen Sie **Speichern** aus. 

### <a name="to-configure-direct-federation-in-azure-ad-using-powershell"></a>So konfigurieren Sie direkten Verbund in Azure AD mit der PowerShell

1. Installieren Sie die neueste Version des Azure AD PowerShell für Graph-Moduls ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)). (Wenn Sie ausführliche Schritte benötigen, enthält der Schnellstart zum Hinzufügen eines Gastbenutzers den Abschnitt [Installieren des neuesten AzureADPreview-Moduls](b2b-quickstart-invite-powershell.md#install-the-latest-azureadpreview-module).) 
2. Führen Sie den folgenden Befehl aus: 
   ```powershell
   Connect-AzureAD
   ```
1. Melden Sie sich an der Anmeldeaufforderung mit dem verwalteten globalen Administratorkonto an. 
2. Führen Sie die folgenden Befehle aus, und ersetzen Sie dabei die Werte aus der Verbundmetadatendatei. Für AD FS-Server und Okta lautet die Verbunddatei „federationmetadata.xml“, z. B.: `https://sts.totheclouddemo.com/federationmetadata/2007-06/federationmetadata.xml`. 

   ```powershell
   $federationSettings = New-Object Microsoft.Open.AzureAD.Model.DomainFederationSettings
   $federationSettings.PassiveLogOnUri ="https://sts.totheclouddemo.com/adfs/ls/"
   $federationSettings.LogOffUri = $federationSettings.PassiveLogOnUri
   $federationSettings.IssuerUri = "http://sts.totheclouddemo.com/adfs/services/trust"
   $federationSettings.MetadataExchangeUri="https://sts.totheclouddemo.com/adfs/services/trust/mex"
   $federationSettings.SigningCertificate= <Replace with X509 signing cert’s public key>
   $federationSettings.PreferredAuthenticationProtocol="WsFed" OR "Samlp"
   $domainName = <Replace with domain name>
   New-AzureADExternalDomainFederation -ExternalDomainName $domainName  -FederationSettings $federationSettings
   ```

## <a name="step-3-test-direct-federation-in-azure-ad"></a>Schritt 3: Direkten Verbund in Azure AD testen
Testen Sie nun Ihre Einrichtung des direkten Verbunds, indem Sie einen neuen B2B-Gastbenutzer einladen. Detailinformationen finden Sie unter [Hinzufügen von Azure AD B2B-Zusammenarbeitsbenutzern im Azure-Portal](add-users-administrator.md).
 
## <a name="how-do-i-edit-a-direct-federation-relationship"></a>Wie bearbeite eine direkte Verbundbeziehung?

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/). Wählen Sie im linken Bereich **Azure Active Directory** aus. 
2. Wählen Sie **Externe Identitäten** aus.
3. Wählen Sie **Alle Identitätsanbieter** aus.
4. Wählen Sie unter **SAML-/WS-Fed-IdP** den Anbieter aus.
5. Aktualisieren Sie die Werte im Bereich mit den Identitätsanbieterdetails.
6. Wählen Sie **Speichern** aus.


## <a name="how-do-i-remove-direct-federation"></a>Wie entferne ich einen direkten Verbund?
Sie können Ihre direkte Verbundeinrichtung entfernen. Wenn Sie dies tun, können sich Verbundgastbenutzer, die ihre Einladungen bereits eingelöst haben, nicht mehr anmelden. Sie können ihnen aber erneut Zugriff auf Ihre Ressourcen gewähren, indem Sie sie aus dem Verzeichnis löschen und erneut einladen. So entfernen Sie den direkten Verbund mit einem Identitätsanbieter im Azure AD-Portal

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/). Wählen Sie im linken Bereich **Azure Active Directory** aus. 
2. Wählen Sie **Externe Identitäten** aus.
3. Wählen Sie **Alle Identitätsanbieter** aus.
4. Wählen Sie den Identitätsanbieter und dann **Löschen** aus. 
5. Wählen Sie **Ja** aus, um den Löschvorgang zu bestätigen. 

So entfernen Sie den direkten Verbund mit einem Identitätsanbieter mit der PowerShell
1. Installieren Sie die neueste Version des Azure AD PowerShell für Graph-Moduls ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Führen Sie den folgenden Befehl aus: 
   ```powershell
   Connect-AzureAD
   ```
3. Melden Sie sich an der Anmeldeaufforderung mit dem verwalteten globalen Administratorkonto an. 
4. Geben Sie den folgenden Befehl ein:
   ```powershell
   Remove-AzureADExternalDomainFederation -ExternalDomainName  $domainName
   ```

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die [Umgebung zum Einlösen von Einladungen](redemption-experience.md), wenn sich externe Benutzer mit verschiedenen Identitätsanbietern anmelden.
