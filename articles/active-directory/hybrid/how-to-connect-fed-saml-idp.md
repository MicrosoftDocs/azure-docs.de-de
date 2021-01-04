---
title: 'Azure AD Connect: Verwenden eines SAML 2.0-Identitätsanbieters für einmaliges Anmelden – Azure'
description: In diesem Dokument wird beschrieben, wie ein mit SAML 2.0 kompatibler Identitätsanbieter (IdP, Identity Provider) für einmaliges Anmelden verwendet wird.
services: active-directory
author: billmath
manager: daveba
ms.custom: it-pro
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 20088ccc79abe978eb5b3f1c98e958f6c26bcb86
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861798"
---
#  <a name="use-a-saml-20-identity-provider-idp-for-single-sign-on"></a>Verwenden eines SAML 2.0-Identitätsanbieters (IdP, Identity Provider) für einmaliges Anmelden

Dieses Dokument enthält Informationen zur Verwendung des mit dem SP-Lite-Profil kompatiblen SAML 2.0-Identitätsanbieters als bevorzugter Sicherheitstokendienst (STS) bzw. Identitätsanbieter. Dieses Szenario ist hilfreich, wenn Sie bereits lokal über einen Speicher für das Benutzerverzeichnis und Kennwort verfügen, auf den mithilfe von SAML 2.0 zugegriffen werden kann. Dieses vorhandene Benutzerverzeichnis kann für die Anmeldung bei Microsoft 365 und anderen mit Azure AD gesicherten Ressourcen verwendet werden. Das SAML 2.0-SP-Lite-Profil basiert auf dem häufig verwendeten Verbund-Identitätsstandard Security Assertion Markup Language (SAML), um das einmalige Anmelden zu ermöglichen und ein Framework für den Austausch von Attributen bereitzustellen.

>[!NOTE]
>Eine Liste der Drittanbieter-IdPs, die für die Verwendung mit Azure AD getestet wurden, finden Sie in der [Azure AD-Verbund – Kompatibilitätsliste](how-to-connect-fed-compatibility.md).

Microsoft unterstützt diese Anmeldung als Integration von Microsoft-Clouddiensten, z. B. Microsoft 365, mit Ihrem ordnungsgemäß konfigurierten, auf dem SAML 2.0-Profil basierten IdP. Es handelt sich bei SAML 2.0-Identitätsanbieter um Drittanbieterprodukte, und Microsoft bietet deshalb keinen Support für die Bereitstellung, Konfiguration und Problembehandlung sowie keine bewährten Methoden dafür. Sobald die Integration mit dem SAML 2.0-Identitätsanbieter ordnungsgemäß konfiguriert ist, kann dieser für die Konfiguration mithilfe des Microsoft-Verbindungsuntersuchungstools verwendet werden, wie weiter unten ausführlicher beschrieben wird. Weitere Informationen zu Ihrem auf dem SAML 2.0 SP-Lite-Profil basierten Identitätsanbieter erhalten Sie bei der Organisation, die ihn bereitgestellt hat.

> [!IMPORTANT]
> In diesem Anmeldeszenario mit SAML 2.0-Identitätsanbietern ist nur eine begrenzte Anzahl von Clients verfügbar, einschließlich:
> 
> - Webbasierte Clients wie Outlook Web Access und SharePoint Online
> - E-Mail-Clients, die die Standardauthentifizierung sowie eine unterstützte Exchange-Zugriffsmethode wie IMAP, POP, Aktive Sync, MAPI usw. verwenden (der Endpunkt des erweiterten Clientprotokolls muss bereitgestellt werden), einschließlich:
>     - Microsoft Outlook 2010/Outlook 2013/Outlook 2016, Apple iPhone (verschiedene iOS-Versionen)
>     - Verschiedene Google Android-Geräte
>     - Windows Phone 7, Windows Phone 7.8 und Windows Phone 8.0
>     - E-Mail-Client für Windows 8 und Windows 8.1
>     - E-Mail-Client für Windows 10

Alle anderen Clients sind nicht in diesem Anmeldeszenario mit Ihrem SAML 2.0-Identitätsanbieter verfügbar. Beispielsweise kann sich der Lync 2010-Desktopclient nicht beim Dienst mit Ihrem SAML 2.0-Identitätsanbieter anmelden, der für einmaliges Anmelden konfiguriert ist.

## <a name="azure-ad-saml-20-protocol-requirements"></a>Azure AD-SAML 2.0-Protokollanforderungen
Dieses Dokument enthält detaillierte Anforderungen an die Protokoll- und Benachrichtigungsformatierung, die Ihr SAML 2.0-Identitätsanbieter implementieren muss, um einen Verbund mit Azure AD zu bilden, damit die Anmeldung bei mehr als einem Microsoft-Clouddienst (wie Microsoft 365) aktiviert werden kann. Die vertrauende SAML 2.0-Seite (SP-STS) für einen Microsoft-Clouddienst, die in diesem Szenario verwendet wird, ist Azure AD.

Sie sollten sicherstellen, dass die ausgehenden Nachrichten Ihres SAML 2.0-Identitätsanbieters den bereitgestellten Beispielablaufverfolgungen so ähnlich wie möglich sind. Verwenden Sie auch wenn möglich die bestimmten Attributwerte aus den bereitgestellten Azure AD-Metadaten. Wenn Sie mit den Ausgabenachrichten zufrieden sind, können Sie mit der Microsoft-Verbindungsuntersuchung wie unten beschrieben Tests durchführen.

Die Azure AD-Metadaten können unter dieser URL heruntergeladen werden: [https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml](https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml).
Für Kunden in China, die die für China bestimmte Instanz von Microsoft 365 nutzen, sollte der folgende Verbundendpunkt verwendet werden: [https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml](https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml).

## <a name="saml-protocol-requirements"></a>SAML-Protokollanforderungen
In diesem Abschnitt wird beschrieben, wie die Anforderungs- und Anwortbenachrichtigungspaare zusammengefügt werden, damit Sie Ihre Benachrichtigungen richtig formatieren können.

Azure AD kann für die Arbeit mit Identitätsanbietern konfiguriert werden, die dasselbe SAML 2.0-SP Lite-Profil mit einigen bestimmten Anforderungen verwenden, so wie nachstehend aufgeführt. Mithilfe der Anforderungs- und Antwortbeispielbenachrichtigungen von SAML, zusammen mit automatischen und manuellen Tests, können Sie Interoperabilität mit Azure AD erreichen.

## <a name="signature-block-requirements"></a>Signaturblockanforderungen
In der SAML-Antwortnachricht enthält der Signaturknoten Informationen über die digitale Signatur für die Benachrichtigung selbst. Der Signaturblock besitzt folgende Anforderungen:

1. Der Assertionsknoten selbst muss signiert sein
2.  Der RSA-sha1-Algorithmus muss als DigestMethod verwendet werden. Andere Algorithmen für digitale Signaturen werden nicht akzeptiert.
   `<ds:DigestMethod Algorithm="https://www.w3.org/2000/09/xmldsig#sha1"/>`
3.  Sie können auch das XML-Dokument signieren. 
4.  Der Transformationsalgorithmus muss den Werten im folgenden Beispiel entsprechen: `<ds:Transform Algorithm="https://www.w3.org/2000/09/xmldsig#enveloped-signature"/>
       <ds:Transform Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#"/>`
9.  Der SignatureMethod-Algorithmus müssen mit folgendem Beispiel übereinstimmen: `<ds:SignatureMethod Algorithm="https://www.w3.org/2000/09/xmldsig#rsa-sha1"/>`

## <a name="supported-bindings"></a>Unterstützte Bindungen
Bindungen sind die transportbezogenen Kommunikationsparameter, die erforderlich sind. Die folgenden Anforderungen gelten für die Bindungen

1. HTTPS ist der erforderliche Transport.
2.  Azure AD erfordert HTTP POST für die Tokenübermittlung während der Anmeldung.
3.  Azure AD verwendet HTTP POST für die Authentifizierungsanforderung an den Identitätsanbieter und REDIRECT für die Abmeldenachricht an den Identitätsanbieter.

## <a name="required-attributes"></a>Erforderliche Attribute
Diese Tabelle zeigt die Anforderungen für bestimmte Attribute in der SAML 2.0-Benachrichtigung.
 
|attribute|BESCHREIBUNG|
| ----- | ----- |
|NameID|Der Wert dieser Assertion muss mit der ImmutableID des Azure AD-Benutzers identisch sein. Er kann bis zu 64 alphanumerische Zeichen lang sein. Alle sicheren Nicht-HTML-Zeichen müssen codiert werden, z.B. wird ein „+“-Zeichen als „.2B“ angezeigt.|
|IDPEmail|Der Benutzerprinzipalname (User Principal Name, UPN) ist in der SAML-Antwort als Element mit dem Namen „IDPEmail“ aufgeführt. Dieser ist der UPN des Benutzers in Azure AD/Microsoft 365. Der UPN ist im E-Mail-Adressformat. Der UPN-Wert in Windows Microsoft 365 (Azure Active Directory).|
|Issuer (Aussteller)|Dies muss ein URI des Identitätsanbieters sein. Der Aussteller aus der Beispielbenachrichtigung sollte nicht wiederverwendet werden. Wenn Sie mehrere Domänen der obersten Ebene in Ihrem Azure AD-Mandanten verfügen, muss der Aussteller mit der pro Domäne konfigurierten URI-Einstellung übereinstimmen.|

>[!IMPORTANT]
>Azure AD unterstützt aktuell den folgenden NameID-Format-URI für SAML 2.0:urn:oasis:names:tc:SAML:2.0:nameid-format:persistent.

## <a name="sample-saml-request-and-response-messages"></a>SAML-Anforderungs- und Antwortbeispielbenachrichtigungen
Ein Anforderungs- und Antwortbenachrichtigungspaar wird für den Nachrichtenaustausch bei der Anmeldung angezeigt.
Dies ist eine Beispielanforderungsnachricht, die von Azure AD an einen SAML 2.0-Beispielidentitätsanbieter gesendet wird. Der SAML 2.0-Beispielidentitätsanbieter ist mit AD FS zur Verwendung des SAML-P-Protokolls konfiguriert. Interoperabilitätstests wurden auch mit anderen SAML 2.0-Identitätsanbietern ausgeführt.

```xml
    <samlp:AuthnRequest 
        xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol" 
        xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion" 
        ID="_7171b0b2-19f2-4ba2-8f94-24b5e56b7f1e" 
        IssueInstant="2014-01-30T16:18:35Z" 
        Version="2.0" 
        AssertionConsumerServiceIndex="0" >
            <saml:Issuer>urn:federation:MicrosoftOnline</saml:Issuer>
            <samlp:NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
    </samlp:AuthnRequest>
```

Dies ist eine Beispielantwortnachricht, die von dem mit SAML 2.0 kompatiblen Beispielidentitätsanbieter an Azure AD/Microsoft 365 gesendet wird.

```xml
    <samlp:Response ID="_592c022f-e85e-4d23-b55b-9141c95cd2a5" Version="2.0" IssueInstant="2014-01-31T15:36:31.357Z" Destination="https://login.microsoftonline.com/login.srf" Consent="urn:oasis:names:tc:SAML:2.0:consent:unspecified" InResponseTo="_049917a6-1183-42fd-a190-1d2cbaf9b144" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">http://WS2012R2-0.contoso.com/adfs/services/trust</Issuer>
    <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
    </samlp:Status>
    <Assertion ID="_7e3c1bcd-f180-4f78-83e1-7680920793aa" IssueInstant="2014-01-31T15:36:31.279Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>http://WS2012R2-0.contoso.com/adfs/services/trust</Issuer>
    <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      <ds:SignedInfo>
        <ds:CanonicalizationMethod Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#" />
        <ds:SignatureMethod Algorithm="https://www.w3.org/2000/09/xmldsig#rsa-sha1" />
        <ds:Reference URI="#_7e3c1bcd-f180-4f78-83e1-7680920793aa">
          <ds:Transforms>
            <ds:Transform Algorithm="https://www.w3.org/2000/09/xmldsig#enveloped-signature" />
            <ds:Transform Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#" />
          </ds:Transforms>
          <ds:DigestMethod Algorithm="https://www.w3.org/2000/09/xmldsig#sha1" />
          <ds:DigestValue>CBn/5YqbheaJP425c0pHva9PhNY=</ds:DigestValue>
        </ds:Reference>
      </ds:SignedInfo>
      <ds:SignatureValue>TciWMyHW2ZODrh/2xrvp5ggmcHBFEd9vrp6DYXp+hZWJzmXMmzwmwS8KNRJKy8H7XqBsdELA1Msqi8I3TmWdnoIRfM/ZAyUppo8suMu6Zw+boE32hoQRnX9EWN/f0vH6zA/YKTzrjca6JQ8gAV1ErwvRWDpyMcwdYCiWALv9ScbkAcebOE1s1JctZ5RBXggdZWrYi72X+I4i6WgyZcIGai/rZ4v2otoWAEHS0y1yh1qT7NDPpl/McDaTGkNU6C+8VfjD78DrUXEcAfKvPgKlKrOMZnD1lCGsViimGY+LSuIdY45MLmyaa5UT4KWph6dA==</ds:SignatureValue>
      <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
        <ds:X509Data>
          <ds:X509Certificate>MIIC7jCCAdagAwIBAgIQRrjsbFPaXIlOG3GTv50fkjANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyhBREZTIFNpZ25pbmcgLSBXUzIwMTJSMi0wLnN3aW5mb3JtZXIuY29tMB4XDTE0MDEyMDE1MTY0MFoXDTE1MDEyMDE1MTY0MFowMzExMC8GA1UEAxMoQURGUyBTaWduaW5nIC0gV1MyMDEyUjItMC5zd2luZm9ybWVyLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKe+rLVmXy1QwCwZwqgbbp1/+3ZWxd9T/jV0hpLIIWr+LCOHqq8n8beJvlivgLmDJo8f+EITnAxWcsJUvVai/35AhHCUq9tc9sqMp5PWtabAEMb2AU72/QlX/72D2/NbGQq1BWYbqUpgpCZ2nSgvlWDHlCiUo//UGsvfox01kjTFlmqQInsJVfRxF5AcCAwEAATANBgkqhkiG9w0BAQsFAAOCAQEAi8c6C4zaTEc7aQiUgvnGQgCbMZbhUXXLGRpjvFLKaQzkwa9eq7WLJibcSNyGXBa/SfT5wJgsm3TPKgSehGAOTirhcqHheZyvBObAScY7GOT+u9pVYp6raFrc7ez3c+CGHeV/tNvy1hJNs12FYH4X+ZCNFIT9tprieR25NCdi5SWUbPZL0tVzJsHc1y92b2M2FxqRDohxQgJvyJOpcg2mSBzZZIkvDg7gfPSUXHVS1MQs0RHSbwq/XdQocUUhl9/e/YWCbNNxlM84BxFsBUok1dH/gzBySx+Fc8zYi7cOq9yaBT3RLT6cGmFGVYZJW4FyhPZOCLVNsLlnPQcX3dDg9A==</ds:X509Certificate>
        </ds:X509Data>
      </KeyInfo>
    </ds:Signature>
    <Subject>
      <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">ABCDEG1234567890</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="_049917a6-1183-42fd-a190-1d2cbaf9b144" NotOnOrAfter="2014-01-31T15:41:31.357Z" Recipient="https://login.microsoftonline.com/login.srf" />
      </SubjectConfirmation>
    </Subject>
    <Conditions NotBefore="2014-01-31T15:36:31.263Z" NotOnOrAfter="2014-01-31T16:36:31.263Z">
      <AudienceRestriction>
        <Audience>urn:federation:MicrosoftOnline</Audience>
      </AudienceRestriction>
    </Conditions>
    <AttributeStatement>
      <Attribute Name="IDPEmail">
        <AttributeValue>administrator@contoso.com</AttributeValue>
      </Attribute>
    </AttributeStatement>
    <AuthnStatement AuthnInstant="2014-01-31T15:36:30.200Z" SessionIndex="_7e3c1bcd-f180-4f78-83e1-7680920793aa">
      <AuthnContext>
        <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</AuthnContextClassRef>
      </AuthnContext>
    </AuthnStatement>
    </Assertion>
    </samlp:Response>
```

## <a name="configure-your-saml-20-compliant-identity-provider"></a>Konfigurieren Ihres mit SAML 2.0 kompatiblen Identitätsanbieters
Dieser Abschnitt enthält Richtlinien zum Konfigurieren Ihres SAML 2.0-Identitätsanbieters für einen Verbund mit Azure AD für die Aktivierung des einmaligen Anmeldens für den Zugriff auf einen oder mehrere Microsoft-Clouddienste (z. B. Microsoft 365) mithilfe des SAML 2.0-Protokolls. Die vertrauende SAML 2.0-Seite für einen Microsoft-Clouddienst, die in diesem Szenario verwendet wird, ist Azure AD.

## <a name="add-azure-ad-metadata"></a>Hinzufügen von Azure AD-Metadaten
Ihr SAML 2.0-Identitätsanbieter muss Informationen zur vertrauenden Azure AD-Seite befolgen. Azure AD veröffentlicht Metadaten unter https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml.

Es wird empfohlen, immer die aktuellen Azure AD-Metadaten zu importieren, wenn Sie Ihren SAML 2.0-Identitätsanbieter konfigurieren.

>[!NOTE]
>Azure AD liest keine Metadaten vom Identitätsanbieter.

## <a name="add-azure-ad-as-a-relying-party"></a>Hinzufügen von Azure AD als vertrauende Seite
Sie müssen die Kommunikation zwischen Ihrem SAML 2.0-Identitätsanbieter und Azure AD aktivieren. Diese Konfiguration ist abhängig von Ihrem spezifischen Identitätsanbieter. Informationen hierzu entnehmen Sie bitte der Dokumentation. Sie würden die ID der vertrauenden Seite in der Regel auf den gleichen Wert wie die EntityID aus den Azure AD-Metadaten festlegen.

>[!NOTE]
>Stellen Sie sicher, dass die Uhr auf dem Server Ihres SAML 2.0-Identitätsanbieters mit einer genauen Zeitquelle synchronisiert ist. Eine ungenaue Zeitangabe kann zu Fehlern bei Verbundanmeldungen führen.

## <a name="install-windows-powershell-for-sign-on-with-saml-20-identity-provider"></a>Installieren von Windows PowerShell für einmaliges Anmelden mit dem SAML 2.0-Identitätsanbieter
Nachdem Sie Ihren SAML 2.0-Identitätsanbieter für die Verwendung mit Azure AD-Anmeldung konfiguriert haben, besteht der nächste Schritt aus dem Herunterladen und Installieren des Azure Active Directory-Moduls für Windows PowerShell. Nach Abschluss der Installation verwenden Sie diese Cmdlets zum Konfigurieren Ihrer Azure AD-Domänen als Verbunddomänen.

Das Azure Active Directory-Modul für Windows PowerShell ist ein Download zum Verwalten Ihrer Organisationsdaten in Azure AD. Dieses Modul installiert eine Reihe von Cmdlets in Windows PowerShell; Sie führen diese Cmdlets zum Einrichten des einmaligen Anmeldens für den Zugriff auf Azure AD aus, und somit auch auf alle Cloud-Dienste, die Sie abonniert haben. Anleitungen zum Herunterladen und Installieren der Cmdlets finden Sie unter [/previous-versions/azure/jj151815(v=azure.100)](/previous-versions/azure/jj151815(v=azure.100)).

## <a name="set-up-a-trust-between-your-saml-identity-provider-and-azure-ad"></a>Einrichten einer Vertrauensstellung zwischen Ihrem SAML-Identitätsanbieter und Azure AD
Bevor ein Verbund für eine Azure AD-Domäne konfiguriert wird, muss zuerst eine benutzerdefinierte Domäne konfiguriert werden. Sie können keinen Verbund für die Standarddomäne erstellen, die von Microsoft bereitgestellt wird. Die Standarddomäne von Microsoft endet mit „onmicrosoft.com“.
Sie führen eine Reihe von Cmdlets in der Windows PowerShell-Befehlszeilenschnittstelle aus oder konvertieren Domänen für einmaliges Anmelden bzw. fügen diese hinzu.

Jede Azure Active Directory-Domäne, für die Sie einen Verbund mithilfe Ihres SAML 2.0-Identitätsanbieters erstellen möchten, muss entweder als eine Domäne für einmaliges Anmelden hinzugefügt oder als Domäne für einmaliges Anmelden von einer Standarddomäne konvertiert werden. Durch das Hinzufügen oder Konvertieren einer Domäne wird eine Vertrauensstellung zwischen Ihrem SAML 2.0-Identitätsanbieter und Azure AD eingerichtet.

Das folgende Verfahren führt Sie durch das Konvertieren einer vorhandenen Standarddomäne zu einer Verbunddomäne mithilfe von SAML 2.0 SP-Lite. 

>[!NOTE]
>Nach Ausführung dieses Schritts kommt es in Ihrer Domäne möglicherweise zu einem Ausfall, der Benutzer bis zu zwei Stunden lang betreffen kann.

## <a name="configuring-a-domain-in-your-azure-ad-directory-for-federation"></a>Konfigurieren einer Domäne in Azure AD Directory für den Verbund


1. Stellen Sie eine Verbindung mit Ihrem Azure AD-Verzeichnis als Mandantenadministrator her:

    ```powershell
    Connect-MsolService
    ```
    
2. Konfigurieren Sie Ihre gewünschte Microsoft 365-Domäne zur Verwendung eines Verbunds mit SAML 2.0:

    ```powershell
    $dom = "contoso.com" 
    $BrandName - "Sample SAML 2.0 IDP" 
    $LogOnUrl = "https://WS2012R2-0.contoso.com/passiveLogon" 
    $LogOffUrl = "https://WS2012R2-0.contoso.com/passiveLogOff" 
    $ecpUrl = "https://WS2012R2-0.contoso.com/PAOS" 
    $MyURI = "urn:uri:MySamlp2IDP" 
    $MySigningCert = "MIIC7jCCAdagAwIBAgIQRrjsbFPaXIlOG3GTv50fkjANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyh BREZTIFNpZ25pbmcgLSBXUzIwMTJSMi0wLnN3aW5mb3JtZXIuY29tMB4XDTE0MDEyMDE1MTY0MFoXDT E1MDEyMDE1MTY0MFowMzExMC8GA1UEAxMoQURGUyBTaWduaW5nIC0gV1MyMDEyUjItMC5zd2luZm9yb WVyLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKe+rLVmXy1QwCwZwqgbbp1/kupQ VcjKuKLitVDbssFyqbDTjP7WRjlVMWAHBI3kgNT7oE362Gf2WMJFf1b0HcrsgLin7daRXpq4Qi6OA57 sW1YFMj3sqyuTP0eZV3S4+ZbDVob6amsZIdIwxaLP9Zfywg2bLsGnVldB0+XKedZwDbCLCVg+3ZWxd9 T/jV0hpLIIWr+LCOHqq8n8beJvlivgLmDJo8f+EITnAxWcsJUvVai/35AhHCUq9tc9sqMp5PWtabAEM b2AU72/QlX/72D2/NbGQq1BWYbqUpgpCZ2nSgvlWDHlCiUo//UGsvfox01kjTFlmqQInsJVfRxF5AcC AwEAATANBgkqhkiG9w0BAQsFAAOCAQEAi8c6C4zaTEc7aQiUgvnGQgCbMZbhUXXLGRpjvFLKaQzkwa9 eq7WLJibcSNyGXBa/SfT5wJgsm3TPKgSehGAOTirhcqHheZyvBObAScY7GOT+u9pVYp6raFrc7ez3c+ CGHeV/tNvy1hJNs12FYH4X+ZCNFIT9tprieR25NCdi5SWUbPZL0tVzJsHc1y92b2M2FxqRDohxQgJvy JOpcg2mSBzZZIkvDg7gfPSUXHVS1MQs0RHSbwq/XdQocUUhl9/e/YWCbNNxlM84BxFsBUok1dH/gzBy Sx+Fc8zYi7cOq9yaBT3RLT6cGmFGVYZJW4FyhPZOCLVNsLlnPQcX3dDg9A==" 
    $uri = "http://WS2012R2-0.contoso.com/adfs/services/trust" 
    $Protocol = "SAMLP" 
    Set-MsolDomainAuthentication `
        -DomainName $dom `
        -FederationBrandName $BrandName `
        -Authentication Federated `
        -PassiveLogOnUri $LogOnUrl `
        -ActiveLogOnUri $ecpUrl `
        -SigningCertificate $MySigningCert `
        -IssuerUri $MyURI `
        -LogOffUri $LogOffUrl `
        -PreferredAuthenticationProtocol $Protocol
    ``` 

3.  Sie können die base64-codierte Zeichenfolge des Signaturzertifikats aus Ihrer IDP-Metadatendatei abrufen. Ein Beispiel für diesen Speicherort wurde bereitgestellt, kann jedoch abhängig von Ihrer Implementierung variieren.

    ```xml
    <IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
        <KeyDescriptor use="signing">
          <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
             <X509Data>
                 <X509Certificate> MIIC5jCCAc6gAwIBAgIQLnaxUPzay6ZJsC8HVv/QfTANBgkqhkiG9w0BAQsFADAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwHhcNMTMxMTA0MTgxMzMyWhcNMTQxMTA0MTgxMzMyWjAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQCwMdVLTr5YTSRp+ccbSpuuFeXMfABD9mVCi2wtkRwC30TIyPdORz642MkurdxdPCWjwgJ0HW6TvXwcO9afH3OC5V//wEGDoNcI8PV4enCzTYFe/h//w51uqyv48Fbb3lEXs+aVl8155OAj2sO9IX64OJWKey82GQWK3g7LfhWWpp17j5bKpSd9DBH5pvrV+Q1ESU3mx71TEOvikHGCZYitEPywNeVMLRKrevdWI3FAhFjcCSO6nWDiMqCqiTDYOURXIcHVYTSof1YotkJ4tG6mP5Kpjzd4VQvnR7Pjb47nhIYG6iZ3mR1F85Ns9+hBWukQWNN2hcD/uGdPXhpdMVpBAgMBAAEwDQYJKoZIhvcNAQELBQADggEBAK7h7jF7wPzhZ1dPl4e+XMAr8I7TNbhgEU3+oxKyW/IioQbvZVw1mYVCbGq9Rsw4KE06eSMybqHln3w5EeBbLS0MEkApqHY+p68iRpguqa+W7UHKXXQVgPMCpqxMFKonX6VlSQOR64FgpBme2uG+LJ8reTgypEKspQIN0WvtPWmiq4zAwBp08hAacgv868c0MM4WbOYU0rzMIR6Q+ceGVRImlCwZ5b7XKp4mJZ9hlaRjeuyVrDuzBkzROSurX1OXoci08yJvhbtiBJLf3uPOJHrhjKRwIt2TnzS9ElgFZlJiDIA26Athe73n43CT0af2IG6yC7e6sK4L3NEXJrwwUZk=</X509Certificate>
              </X509Data>
            </KeyInfo>
        </KeyDescriptor>
    </IDPSSODescriptor>
    ``` 

Weitere Informationen zu „Set-MsolDomainAuthentication“ finden Sie unter: [/previous-versions/azure/dn194112(v=azure.100)](/previous-versions/azure/dn194112(v=azure.100)).

>[!NOTE]
>Sie müssen `$ecpUrl = "https://WS2012R2-0.contoso.com/PAOS"` nur verwenden, wenn Sie eine ECP-Erweiterung für Ihren Identitätsanbieter einrichten. Exchange Online-Clients mit Ausnahme von Outlook Web Application (OWA) basieren auf einem auf POST basierenden aktiven Endpunkt. Wenn Ihr SAML 2.0-STS einen aktiven Endpunkt implementiert, der der ECP-Implementierung von Shibboleth von einem aktiven Endpunkt ähnelt, können diese Rich Clients möglicherweise mit dem Exchange Online-Dienst interagieren.

Nachdem der Verbund konfiguriert wurde, können Sie zurück zu „ohne Verbund“ (oder „verwaltet“) wechseln. Diese Änderungen benötigen jedoch bis zu zwei Stunden und erfordern die Zuweisung neuer zufälliger Kennwörter für die cloudbasierte Anmeldung für jeden Benutzer. Das Zurückwechseln zu „verwaltet“ ist in einigen Szenarios möglicherweise erforderlich, um einen Fehler in Ihren Einstellungen zurückzusetzen. Weitere Informationen zur Domänenkonvertierung finden Sie unter: [/previous-versions/azure/dn194122(v=azure.100)](/previous-versions/azure/dn194122(v=azure.100)).

## <a name="provision-user-principals-to-azure-ad--microsoft-365"></a>Bereitstellen von Benutzerprinzipalen für Azure AD/Microsoft 365
Bevor Sie Ihre Benutzer mit Microsoft 365 authentifizieren können, müssen Sie Azure AD mit Benutzerprinzipalen bereitstellen, die mit der Assertion im SAML 2.0-Anspruch übereinstimmen. Wenn Azure AD diese Benutzerprinzipale im Voraus nicht bekannt sind, können sie nicht für die Verbundanmeldung verwendet werden. Azure AD Connect oder Windows PowerShell können zum Bereitstellen von Benutzerprinzipalen verwendet werden.

Azure AD Connect kann zum Bereitstellen von Prinzipalen für Ihre Domänen in Ihrem Azure AD-Verzeichnis aus dem lokalen Active Directory verwendet werden. Ausführlichere Informationen finden Sie unter [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](whatis-hybrid-identity.md).

Windows PowerShell kann auch zum automatischen Hinzufügen neuer Benutzer zu Azure AD und zum Synchronisieren von Änderungen aus dem lokalen Verzeichnis verwendet werden. Um Windows PowerShell-Cmdlets verwenden zu können, müssen Sie die [Azure Active Directory-Module](/powershell/azure/active-directory/install-adv2) herunterladen.

Diese Prozedur zeigt, wie Azure AD ein einzelner Benutzer hinzugefügt wird.


1. Stellen Sie eine Verbindung mit Ihrem Azure AD-Verzeichnis als Mandantenadministrator her: Connect-MsolService.
2. Erstellen Sie einen neuen Benutzerprinzipal:

    ```powershell
    New-MsolUser `
      -UserPrincipalName elwoodf1@contoso.com `
      -ImmutableId ABCDEFG1234567890 `
      -DisplayName "Elwood Folk" `
      -FirstName Elwood `
      -LastName Folk `
      -AlternateEmailAddresses "Elwood.Folk@contoso.com" `
      -LicenseAssignment "samlp2test:ENTERPRISEPACK" `
      -UsageLocation "US" 
    ```

Weitere Informationen zum Check-Out „New-MsolUser“ finden Sie unter [/previous-versions/azure/dn194096(v=azure.100)](/previous-versions/azure/dn194096(v=azure.100)).

>[!NOTE]
>Der Wert „UserPrinciplName“ muss mit dem Wert übereinstimmen, den Sie für „IDPEmail“ in Ihrem SAML 2.0-Anspruch senden, und der Wert „ImmutableID“ muss mit dem Wert übereinstimmen, der in Ihrer „NameID“-Assertion gesendet wurde.

## <a name="verify-single-sign-on-with-your-saml-20-idp"></a>Überprüfen des einmaligen Anmeldens mit Ihrem SAML 2.0-IdP
Bevor Sie als Administrator das einmalige Anmelden (auch als Identitätsverbund bekannt) überprüfen und verwalten, überprüfen Sie die Informationen, und führen Sie die Schritte in den folgenden Artikeln aus, um das einmalige Anmelden mit Ihrem auf SAML 2.0 SP-Lite basierenden Identitätsanbieter einzurichten.


1.  Sie haben die Azure AD-SAML 2.0-Protokollanforderungen überprüft.
2.  Sie haben Ihren SAML 2.0-Identitätsanbieter konfiguriert.
3.  Installieren Sie Windows PowerShell für einmaliges Anmelden mit dem SAML 2.0-Identitätsanbieter.
4.  Richten Sie eine Vertrauensstellung zwischen Ihrem SAML 2.0-Identitätsanbieter und Azure AD ein.
5.  Stellen Sie einen bekannten Testbenutzerprinzipal für Azure Active Directory (Microsoft 365) entweder über Windows PowerShell oder Azure AD Connect bereit.
6.  Konfigurieren Sie mithilfe von [Azure AD Connect](whatis-hybrid-identity.md) die Verzeichnissynchronisierung.

Nach dem Einrichten des einmaligen Anmeldens mit Ihrem auf SAML 2.0 SP-Lite basierenden Identitätsanbieter müssen Sie überprüfen, ob dieser ordnungsgemäß arbeitet.

>[!NOTE]
>Wenn Sie eine Domäne konvertiert haben, statt eine hinzuzufügen, kann die Einrichtung des einmaligen Anmeldens bis zu 24 Stunden dauern.
Bevor Sie das einmalige Anmelden überprüfen, müssen Sie die Einrichtung der Active Directory-Synchronisierung abgeschlossen, Ihre Verzeichnisse synchronisiert und Ihre synchronisierten Benutzer aktiviert haben.

### <a name="use-the-tool-to-verify-that-single-sign-on-has-been-set-up-correctly"></a>Verwenden Sie das Tool, um sicherzustellen, dass das einmalige Anmelden ordnungsgemäß eingerichtet wurde.
Um sicherzustellen, dass das einmalige Anmelden ordnungsgemäß eingerichtet wurde, können Sie die folgenden Schritte ausführen, um zu bestätigen, dass Sie sich beim Clouddienst mit Ihren Unternehmensanmeldeinformationen anmelden können.

Microsoft hat ein Tool bereitgestellt, das Sie verwenden können, um Ihre auf SAML 2.0 basierenden Identitätsanbieter zu testen. Bevor Sie das Testtool ausführen, müssen Sie Azure AD-Mandanten für den Verbund mit Ihrem Identitätsanbieter konfiguriert haben.

>[!NOTE]
>Die Verbindungsuntersuchung erfordert Internet Explorer 10 oder höher.



1. Laden Sie die Verbindungsuntersuchung unter [https://testconnectivity.microsoft.com/?tabid=Client](https://testconnectivity.microsoft.com/?tabid=Client) herunter.
2.  Klicken Sie auf „Jetzt installieren“, um mit dem Download und der Installation des Tools zu beginnen.
3.  Wählen Sie „I can’t set up federation with Office 365, Azure, or other services that use Azure Active Directory“ (Ich kann keinen Verbund mit Office 365, Azure oder anderen Diensten einrichten, die Azure Active Directory verwenden) aus.
4.  Nachdem das Tool heruntergeladen wurde und ausgeführt wird, sehen Sie das Fenster „Verbindungsdiagnose“. Das Tool führt Sie ausführlich durch das Testen Ihrer Verbundverbindung.
5.  Die Verbindungsuntersuchung öffnet Ihren SAML 2.0-Identitätsanbieter für Ihre Anmeldung. Geben Sie die Anmeldeinformationen für den zu überprüfenden Benutzerprinzipal ein: ![Screenshot des Anmeldefensters für den SAML 2.0-IDP.](./media/how-to-connect-fed-saml-idp/saml1.png)
6.  Geben Sie im Fenster der Anmeldung für den Verbundtest einen Kontonamen und ein zugehöriges Kennwort für den Azure AD-Mandanten ein, für den mit Ihrem SAML 2.0-Identitätsanbieter ein Verbund erstellt werden soll. Das Tool versucht, sich mithilfe dieser Anmeldeinformationen anzumelden. Als Ausgabe werden ausführliche Ergebnisse von Tests, die während des Anmeldeversuchs durchgeführt wurden, bereitgestellt.
![SAML](./media/how-to-connect-fed-saml-idp/saml2.png)
7. Dieses Fenster zeigt ein Fehlerergebnis des Tests an. Durch Klicken auf „Überprüfen“ werden ausführliche Informationen über die Ergebnisse der Tests, die ausgeführt wurden, angezeigt. Sie können auch die Ergebnisse auf einem Datenträger speichern, um sie freizugeben.
 
>[!NOTE]
>Die Verbindungsuntersuchung testet auch den aktiven Verbund mithilfe der auf WS* basierten und ECP/PAOS-Protokolle. Wenn Sie diese nicht verwenden, können Sie den folgenden Fehler ignorieren: Der aktive Anmeldevorgang unter Verwendung des aktiven Verbundendpunkts Ihres Identitätsanbieters wird getestet.

### <a name="manually-verify-that-single-sign-on-has-been-set-up-correctly"></a>Manuelle Bestätigung, dass einmaliges Anmelden ordnungsgemäß eingerichtet wurde
Manuelle Überprüfung bietet zusätzliche Schritte, die Sie vornehmen können, um sicherzustellen, dass Ihr SAML 2.0-Identitätsanbieter in vielen Szenarios korrekt ausgeführt wird.
Um zu überprüfen, dass einmaliges Anmelden ordnungsgemäß eingerichtet wurde, führen Sie die folgenden Schritte aus:


1. Melden Sie sich auf einem mit einer Domäne verbundenen Computer bei Ihrem Clouddienst an. Nutzen Sie dafür den Anmeldenamen, den Sie für Ihre Unternehmensanmeldeinformationen verwenden.
2.  Klicken Sie in das Kennwortfeld. Wenn das einmalige Anmelden eingerichtet ist, wird das Kennwortfeld schattiert, und es wird die folgende Meldung angezeigt: „Sie müssen sich jetzt bei &lt;Ihrem Unternehmen&gt; anmelden.“
3.  Klicken Sie auf den Link zum Anmelden bei &lt;Ihr Unternehmen&gt;. Wenn Sie sich anmelden können, wurde das einmalige Anmelden eingerichtet.

## <a name="next-steps"></a>Nächste Schritte


- [Verwaltung der Active Directory-Verbunddienste und Anpassung mit Azure AD Connect](how-to-connect-fed-management.md)
- [Azure AD-Verbund – Kompatibilitätsliste](how-to-connect-fed-compatibility.md)
- [Azure AD Connect – Benutzerdefinierte Installation](how-to-connect-install-custom.md)
