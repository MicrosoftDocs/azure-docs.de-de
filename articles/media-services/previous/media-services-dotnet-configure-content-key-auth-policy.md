---
title: Konfigurieren einer Autorisierungsrichtlinie für Inhaltsschlüssel mit dem Media Services .NET-SDK | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit dem Media Services .NET-SDK eine Autorisierungsrichtlinie für einen Inhaltsschlüssel konfigurieren.
services: media-services
documentationcenter: ''
author: mingfeiy
manager: femila
editor: ''
ms.assetid: 1a0aedda-5b87-4436-8193-09fc2f14310c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako;mingfeiy
ms.openlocfilehash: 744887a3b23518a5b970a3fda94bf990806bd2d8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61230269"
---
# <a name="dynamic-encryption-configure-a-content-key-authorization-policy"></a>Dynamische Verschlüsselung: Konfigurieren einer Autorisierungsrichtlinie für Inhaltsschlüssel
[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Übersicht
 Mit Azure Media Services können Sie MPEG-DASH-, Smooth Streaming- und HTTP Live Streaming-Datenströme (HLS) übermitteln, die mit AES (Advanced Encryption Standard) mit 128-Bit-Verschlüsselungsschlüsseln oder [PlayReady-DRM (Digital Rights Management)](https://www.microsoft.com/playready/overview/) geschützt sind. Media Services ermöglicht Ihnen auch, mit Widevine DRM verschlüsselte DASH-Streams zu übermitteln. PlayReady und Widevine sind gemäß der Common Encryption-Spezifikation (ISO/IEC 23001-7 CENC) verschlüsselt.

Media Services bietet auch einen Schlüssel-/Lizenzübermittlungsdienst, von dem Clients AES-Schlüssel oder PlayReady/Widevine-Lizenzen zur Wiedergabe von verschlüsselten Inhalten abrufen können.

Wenn ein Medienobjekt durch Media Services verschlüsselt werden soll, müssen Sie dem Medienobjekt einen Verschlüsselungsschlüssel (CommonEncryption oder EnvelopeEncryption) zuordnen. Weitere Informationen finden Sie unter [Erstellen von Inhaltsschlüsseln mit .NET](media-services-dotnet-create-contentkey.md). Außerdem müssen Sie Autorisierungsrichtlinien für den Schlüssel konfigurieren (wie in diesem Artikel beschrieben).

Wenn ein Player einen Stream anfordert, verwendet Media Services den angegebenen Schlüssel, um den Inhalt mit AES- oder DRM-Verschlüsselung dynamisch zu verschlüsseln. Um den Stream zu entschlüsseln, fordert der Player den Schlüssel vom Schlüsselübermittlungsdienst an. Um zu ermitteln, ob der Benutzer berechtigt ist, den Schlüssel zu erhalten, wertet der Dienst die Autorisierungsrichtlinien aus, die Sie für den Schlüssel angegeben haben.

Media Services unterstützt mehrere Möglichkeiten zur Authentifizierung von Benutzern, die Schlüssel anfordern. Die Autorisierungsrichtlinie für Inhaltsschlüssel kann eine oder mehrere Autorisierungseinschränkungen aufweisen. Die Optionen lauten „Open“ oder „Token“. Eine durch Token eingeschränkte Richtlinie gilt nur zusammen mit einem Token, das von einem Sicherheitstokendienst (Security Token Service, STS) ausgestellt wurde. Media Services unterstützt Token im Simple Web Token-Format ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) und im JSON Web Token-Format ([JWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3)).

Media Services stellt keinen Sicherheitstokendienst (STS) bereit. Sie können einen benutzerdefinierten Sicherheitstokendienst erstellen oder Azure Access Control Service zum Ausstellen von Token verwenden. Der STS muss für die Erstellung eines mit dem angegebenen Schlüssel signierten Tokens und die Ausgabe von Ansprüchen konfiguriert sein, die Sie in der Konfiguration der Token-Einschränkung angegeben haben (wie in diesem Artikel beschrieben). Der Schlüsselübermittlungsdienst von Media Services gibt den Verschlüsselungsschlüssel an den Client zurück, wenn das Token gültig ist und die Ansprüche im Token mit den für den Inhaltsschlüssel konfigurierten Ansprüchen übereinstimmen.

Weitere Informationen finden Sie in den folgenden Artikeln:

- [JWT-Tokenauthentifizierung](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
- [Integrate Azure Media Services OWIN MVC-based app with Azure Active Directory and restrict content key delivery based on JWT claims](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/) (Integrieren einer Azure Media Services-OWIN MVC-basierten App in Azure Active Directory und Einschränken der Übermittlung von Inhaltsschlüsseln auf Grundlage von JWT-Ansprüchen)

### <a name="some-considerations-apply"></a>Zu berücksichtigende Aspekte
* Beim Erstellen Ihres Media Services-Kontos wird dem Konto ein Standard-Streamingendpunkt mit dem Status „Beendet“ hinzugefügt. Um mit dem Streamen der Inhalte zu beginnen und die dynamische Paketerstellung und dynamische Verschlüsselung zu nutzen, muss der Streamingendpunkt den Status „Wird ausgeführt“ aufweisen. 
* Ihr Medienobjekt muss einen Satz von MP4-Dateien bzw. Smooth Streaming-Dateien mit adaptiver Bitrate enthalten. Weitere Informationen finden Sie unter [Codieren von Medienobjekten](media-services-encode-asset.md).
* Zum Hochladen und Codieren Ihrer Medienobjekte verwenden Sie die Option „AssetCreationOptions.StorageEncrypted“.
* Wenn Sie mehrere Inhaltsschlüssel verwenden möchten, die dieselbe Richtlinienkonfiguration erfordern, wird empfohlen, eine einzelne Autorisierungsrichtlinie zu erstellen und für mehrere Inhaltsschlüssel wiederzuverwenden.
* „ContentKeyAuthorizationPolicy“ und die zugehörigen Objekte (Richtlinienoptionen und Einschränkungen) werden vom Schlüsselübermittlungsdienst 15 Minuten lang zwischengespeichert. Sie können eine „ContentKeyAuthorizationPolicy“ erstellen und angeben, dass eine Tokeneinschränkung verwendet werden soll. Dann können Sie die Richtlinie testen und hinsichtlich der Open-Einschränkung aktualisieren. Dieser Vorgang dauert ungefähr 15 Minuten, bevor die Richtlinie zur offenen Version der Richtlinie wechselt.
* Wenn Sie die Übermittlungsrichtlinie eines Medienobjekts hinzufügen oder aktualisieren, müssen Sie alle vorhandenen Locators löschen und einen neuen Locator erstellen.
* Progressive Downloads können derzeit nicht verschlüsselt werden.
* Ein Media Services-Streamingendpunkt legt den Wert des CORS-Headers „Access-Control-Allow-Origin“ in der Preflightantwort als Platzhalterzeichen „\*“ fest. Dieser Wert funktioniert mit den meisten Playern gut, z.B Azure Media Player, Roku und JWPlayer sowie weitere. Einige Player, die „dash.js“ nutzen, funktionieren jedoch nicht, da „XMLHttpRequest“ in der „dash.js“ mit dem Anmeldeinformationsmodus auf „include“ gesetzt ist und der Platzhalter „\*“ als Wert von „Access-Control-Allow-Origin“ nicht zugelassen wird. Als Problemumgehung für diese Einschränkung in „dash.js“ ist Folgendes möglich: Wenn Sie Ihren Client von einer einzigen Domäne aus hosten, kann Media Services diese Domäne im Preflightantwortheader angeben. Wenn Sie Hilfe benötigen, öffnen Sie ein Supportticket im Azure-Portal.

## <a name="aes-128-dynamic-encryption"></a>Dynamische AES-128-Verschlüsselung
### <a name="open-restriction"></a>Open-Einschränkung
Bei Verwendung einer Open-Einschränkung übermittelt das System den Schlüssel an jeden, der einen Schlüssel anfordert. Diese Einschränkung kann zu Testzwecken nützlich sein.

Im folgenden Beispiel wird eine Open-Autorisierungsrichtlinie erstellt und dem Inhaltsschlüssel hinzugefügt:
```csharp
    static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
    {
        // Create ContentKeyAuthorizationPolicy with Open restrictions
        // and create authorization policy
        IContentKeyAuthorizationPolicy policy = _context.
        ContentKeyAuthorizationPolicies.
        CreateAsync("Open Authorization Policy").Result;
        
        List<ContentKeyAuthorizationPolicyRestriction> restrictions =
            new List<ContentKeyAuthorizationPolicyRestriction>();

        ContentKeyAuthorizationPolicyRestriction restriction =
            new ContentKeyAuthorizationPolicyRestriction
            {
                Name = "HLS Open Authorization Policy",
                KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
                Requirements = null // no requirements needed for HLS
            };

        restrictions.Add(restriction);

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create(
            "policy", 
            ContentKeyDeliveryType.BaselineHttp, 
            restrictions, 
            "");

        policy.Options.Add(policyOption);

        // Add ContentKeyAuthorizationPolicy to ContentKey
        contentKey.AuthorizationPolicyId = policy.Id;
        IContentKey updatedKey = contentKey.UpdateAsync().Result;
        Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);
    }
```

### <a name="token-restriction"></a>Token-Einschränkung
In diesem Abschnitt wird beschrieben, wie eine Autorisierungsrichtlinie für Inhaltsschlüssel erstellt und dem Inhaltsschlüssel zugeordnet wird. Die Autorisierungsrichtlinie beschreibt, welche Autorisierungsanforderungen der Benutzer erfüllen muss, um festzustellen, ob der Benutzer zum Erhalt des Schlüssels autorisiert ist. Beispielsweise wird überprüft, ob die Verifizierungsschlüsselliste den Schlüssel enthält, mit dem das Token signiert wurde.

Zur Konfiguration der Einschränkungsoption „Token“ benötigen Sie XML-Code, um die Autorisierungsanforderungen des Tokens in XML zu beschreiben. Die XML für die Konfiguration der Token-Einschränkung muss folgendem XML-Schema entsprechen:
```csharp
#### Token restriction schema
    <?xml version="1.0" encoding="utf-8"?>
    <xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" xmlns:xs="https://www.w3.org/2001/XMLSchema">
      <xs:complexType name="TokenClaim">
        <xs:sequence>
          <xs:element name="ClaimType" nillable="true" type="xs:string" />
          <xs:element minOccurs="0" name="ClaimValue" nillable="true" type="xs:string" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="TokenClaim" nillable="true" type="tns:TokenClaim" />
      <xs:complexType name="TokenRestrictionTemplate">
        <xs:sequence>
          <xs:element minOccurs="0" name="AlternateVerificationKeys" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
          <xs:element name="Audience" nillable="true" type="xs:anyURI" />
          <xs:element name="Issuer" nillable="true" type="xs:anyURI" />
          <xs:element name="PrimaryVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
          <xs:element minOccurs="0" name="RequiredClaims" nillable="true" type="tns:ArrayOfTokenClaim" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="TokenRestrictionTemplate" nillable="true" type="tns:TokenRestrictionTemplate" />
      <xs:complexType name="ArrayOfTokenVerificationKey">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfTokenVerificationKey" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
      <xs:complexType name="TokenVerificationKey">
        <xs:sequence />
      </xs:complexType>
      <xs:element name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
      <xs:complexType name="ArrayOfTokenClaim">
        <xs:sequence>
          <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenClaim" nillable="true" type="tns:TokenClaim" />
        </xs:sequence>
      </xs:complexType>
      <xs:element name="ArrayOfTokenClaim" nillable="true" type="tns:ArrayOfTokenClaim" />
      <xs:complexType name="SymmetricVerificationKey">
        <xs:complexContent mixed="false">
          <xs:extension base="tns:TokenVerificationKey">
            <xs:sequence>
              <xs:element name="KeyValue" nillable="true" type="xs:base64Binary" />
            </xs:sequence>
          </xs:extension>
        </xs:complexContent>
      </xs:complexType>
      <xs:element name="SymmetricVerificationKey" nillable="true" type="tns:SymmetricVerificationKey" />
    </xs:schema>
```
Bei der Konfiguration der Richtlinie mit Tokeneinschränkung müssen die Parameter für den primären Verifizierungsschlüssel (primary verification key), den Aussteller (issuer) und die Zielgruppe (audience) angegeben werden. Der primäre Verifizierungsschlüssel enthält den Schlüssel, mit dem das Token signiert wurde. Der Aussteller ist der Sicherheitstokendienst, der das Token ausstellt. Audience (manchmal auch Scope) beschreibt den Verwendungszweck des Tokens oder die Ressource, auf die durch das Token Zugriff gewährt wird. Der Schlüsselübermittlungsdienst von Media Services überprüft, ob die Werte im Token mit den Werten in der Vorlage übereinstimmen.

Bei Verwendung des Media Services SDK für .NET können Sie die TokenRestrictionTemplate-Klasse zum Generieren des Einschränkungstokens verwenden.
Im folgenden Beispiel wird eine Autorisierungsrichtlinie mit einer Token-Einschränkung erstellt. In diesem Beispiel muss der Client ein Token vorlegen, das einen Signaturschlüssel (VerificationKey), einen Tokenaussteller und erforderliche Ansprüche enthält.
```csharp
    public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
    {
        string tokenTemplateString = GenerateTokenRequirements();

        IContentKeyAuthorizationPolicy policy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("HLS token restricted authorization policy").Result;

        List<ContentKeyAuthorizationPolicyRestriction> restrictions =
                new List<ContentKeyAuthorizationPolicyRestriction>();

        ContentKeyAuthorizationPolicyRestriction restriction =
                new ContentKeyAuthorizationPolicyRestriction
                {
                    Name = "Token Authorization Policy",
                    KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                    Requirements = tokenTemplateString
                };

        restrictions.Add(restriction);

        //You could have multiple options 
        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create(
                "Token option for HLS",
                ContentKeyDeliveryType.BaselineHttp,
                restrictions,
                null  // no key delivery data is needed for HLS
                );

        policy.Options.Add(policyOption);

        // Add ContentKeyAuthorizationPolicy to ContentKey
        contentKey.AuthorizationPolicyId = policy.Id;
        IContentKey updatedKey = contentKey.UpdateAsync().Result;
        Console.WriteLine("Adding Key to Asset: Key ID is " + updatedKey.Id);

        return tokenTemplateString;
    }

    static private string GenerateTokenRequirements()
    {
        TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);

        template.PrimaryVerificationKey = new SymmetricVerificationKey();
        template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();

        template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

        return TokenRestrictionTemplateSerializer.Serialize(template);
    }
```
#### <a name="test-token"></a>Testtoken
Gehen Sie wie folgt vor, um ein Testtoken abzurufen, das auf der Tokeneinschränkung basiert, die für die Schlüsselautorisierungsrichtlinie verwendet wurde:
```csharp
    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate =
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    // Note, you need to pass the key id Guid because we specified 
    // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
    Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);

    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
    Console.WriteLine();
```

## <a name="playready-dynamic-encryption"></a>Dynamische PlayReady-Verschlüsselung
Mithilfe von Media Services können Sie die Rechte und Einschränkungen konfigurieren, die durch die PlayReady-DRM-Laufzeit erzwungen werden sollen, wenn ein Benutzer versucht, geschützte Inhalte wiederzugeben. 

Wenn Sie Inhalte mit PlayReady schützen, müssen Sie in Ihrer Autorisierungsrichtlinie u.a. eine XML-Zeichenfolge zur Definition der [PlayReady-Lizenzvorlage](media-services-playready-license-template-overview.md) angeben. Die PlayReadyLicenseResponseTemplate-Klasse und die PlayReadyLicenseTemplate-Klasse im Media Services SDK für .NET unterstützen Sie bei der Definition der PlayReady-Lizenzvorlage.

Informationen zum Verschlüsseln Ihrer Inhalte mit PlayReady und Widevine finden Sie unter [Verwenden von dynamischer allgemeiner Verschlüsselung mit PlayReady und/oder Widevine](media-services-protect-with-playready-widevine.md).

### <a name="open-restriction"></a>Open-Einschränkung
Bei Verwendung einer Open-Einschränkung übermittelt das System den Schlüssel an jeden, der einen Schlüssel anfordert. Diese Einschränkung kann zu Testzwecken nützlich sein.

Im folgenden Beispiel wird eine Open-Autorisierungsrichtlinie erstellt und dem Inhaltsschlüssel hinzugefügt:

```csharp
    static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
    {

        // Create ContentKeyAuthorizationPolicy with Open restrictions 
        // and create authorization policy          

        List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
        {
            new ContentKeyAuthorizationPolicyRestriction 
            { 
                Name = "Open", 
                KeyRestrictionType = (int)ContentKeyRestrictionType.Open, 
                Requirements = null
            }
        };

        // Configure PlayReady license template.
        string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create("",
                ContentKeyDeliveryType.PlayReadyLicense,
                    restrictions, newLicenseTemplate);

        IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                    ContentKeyAuthorizationPolicies.
                    CreateAsync("Deliver Common Content Key with no restrictions").
                    Result;


        contentKeyAuthorizationPolicy.Options.Add(policyOption);

        // Associate the content key authorization policy with the content key.
        contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
        contentKey = contentKey.UpdateAsync().Result;
    }
```

### <a name="token-restriction"></a>Token-Einschränkung
Zur Konfiguration der Einschränkungsoption „Token“ benötigen Sie XML-Code, um die Autorisierungsanforderungen des Tokens in XML zu beschreiben. Der XML-Code für die Konfiguration der Tokeneinschränkung muss dem im Abschnitt „Schema für die Tokeneinschränkung“ beschriebenen XML-Schema entsprechen.

```csharp
    public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
    {
        string tokenTemplateString = GenerateTokenRequirements();

        IContentKeyAuthorizationPolicy policy = _context.
                                ContentKeyAuthorizationPolicies.
                                CreateAsync("HLS token restricted authorization policy").Result;

        List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
        {
            new ContentKeyAuthorizationPolicyRestriction 
            { 
                Name = "Token Authorization Policy", 
                KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
                Requirements = tokenTemplateString, 
            }
        };

        // Configure PlayReady license template.
        string newLicenseTemplate = ConfigurePlayReadyLicenseTemplate();

        IContentKeyAuthorizationPolicyOption policyOption =
            _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
                ContentKeyDeliveryType.PlayReadyLicense,
                    restrictions, newLicenseTemplate);

        IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
                    ContentKeyAuthorizationPolicies.
                    CreateAsync("Deliver Common Content Key with no restrictions").
                    Result;

        policy.Options.Add(policyOption);

        // Add ContentKeyAuthorizationPolicy to ContentKey
        contentKeyAuthorizationPolicy.Options.Add(policyOption);

        // Associate the content key authorization policy with the content key
        contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
        contentKey = contentKey.UpdateAsync().Result;

        return tokenTemplateString;
    }

    static private string GenerateTokenRequirements()
    {

        TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);

        template.PrimaryVerificationKey = new SymmetricVerificationKey();
        template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
            template.Audience = _sampleAudience.ToString();
            template.Issuer = _sampleIssuer.ToString();


        template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);

        return TokenRestrictionTemplateSerializer.Serialize(template);
    } 

    static private string ConfigurePlayReadyLicenseTemplate()
    {
        // The following code configures PlayReady License Template using .NET classes
        // and returns the XML string.

        //The PlayReadyLicenseResponseTemplate class represents the template for the response sent back to the end user. 
        //It contains a field for a custom data string between the license server and the application 
        //(may be useful for custom app logic) as well as a list of one or more license templates.
        PlayReadyLicenseResponseTemplate responseTemplate = new PlayReadyLicenseResponseTemplate();

        // The PlayReadyLicenseTemplate class represents a license template for creating PlayReady licenses
        // to be returned to the end users. 
        //It contains the data on the content key in the license and any rights or restrictions to be 
        //enforced by the PlayReady DRM runtime when using the content key.
        PlayReadyLicenseTemplate licenseTemplate = new PlayReadyLicenseTemplate();
        //Configure whether the license is persistent (saved in persistent storage on the client) 
        //or non-persistent (only held in memory while the player is using the license).  
        licenseTemplate.LicenseType = PlayReadyLicenseType.Nonpersistent;

        // AllowTestDevices controls whether test devices can use the license or not.  
        // If true, the MinimumSecurityLevel property of the license
        // is set to 150.  If false (the default), the MinimumSecurityLevel property of the license is set to 2000.
        licenseTemplate.AllowTestDevices = true;


        // You can also configure the Play Right in the PlayReady license by using the PlayReadyPlayRight class. 
        // It grants the user the ability to play back the content subject to the zero or more restrictions 
        // configured in the license and on the PlayRight itself (for playback specific policy). 
        // Much of the policy on the PlayRight has to do with output restrictions 
        // which control the types of outputs that the content can be played over and 
        // any restrictions that must be put in place when using a given output.
        // For example, if the DigitalVideoOnlyContentRestriction is enabled, 
        //then the DRM runtime will only allow the video to be displayed over digital outputs 
        //(analog video outputs won’t be allowed to pass the content).

        //IMPORTANT: These types of restrictions can be very powerful but can also affect the consumer experience. 
        // If the output protections are configured too restrictive, 
        // the content might be unplayable on some clients. For more information, see the PlayReady Compliance Rules document.

        // For example:
        //licenseTemplate.PlayRight.AgcAndColorStripeRestriction = new AgcAndColorStripeRestriction(1);

        responseTemplate.LicenseTemplates.Add(licenseTemplate);

        return MediaServicesLicenseTemplateSerializer.Serialize(responseTemplate);
    }
```

Um ein Testtoken abzurufen, das auf der Tokeneinschränkung basiert, die für die Schlüsselautorisierungsrichtlinie verwendet wurde, lesen Sie den Abschnitt [Testtoken](#test-token). 

## <a id="types"></a>Beim Definieren von ContentKeyAuthorizationPolicy verwendete Typen
### <a id="ContentKeyRestrictionType"></a>ContentKeyRestrictionType

```csharp
    public enum ContentKeyRestrictionType
    {
        Open = 0,
        TokenRestricted = 1,
        IPRestricted = 2,
    }
```

### <a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType

```csharp 
    public enum ContentKeyDeliveryType
    {
      None = 0,
      PlayReadyLicense = 1,
      BaselineHttp = 2,
      Widevine = 3
    }
```

### <a id="TokenType"></a>TokenType

```csharp
    public enum TokenType
    {
        Undefined = 0,
        SWT = 1,
        JWT = 2,
    }
```

## <a name="media-services-learning-paths"></a>Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie nun eine Autorisierungsrichtlinie für den Inhaltsschlüssel konfiguriert haben, finden Sie weitere Informationen unter [Konfigurieren einer Übermittlungsrichtlinie für Medienobjekte](media-services-dotnet-configure-asset-delivery-policy.md).

