---
title: Konfigurieren von Identitätsanbietern (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Erfahren Sie, wie Sie mit MSAL für iOS und macOS verschiedene autoritative Stellen wie B2C, Sovereign Clouds und Gastbenutzer nutzen können.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: d8a176fff0da932d0fafd40b9ab895b635acc5f6
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96169442"
---
# <a name="how-to-configure-msal-for-ios-and-macos-to-use-different-identity-providers"></a>Gewusst wie: Konfigurieren von MSAL für iOS und macOS für die Verwendung verschiedener Identitätsanbieter

Dieser Artikel zeigt Ihnen, wie Sie Ihre Microsoft-Authentifizierungsbibliothek-App für iOS und macOS (MSAL) für verschiedene autoritative Stellen wie Azure Active Directory (Azure AD), Business-to-Consumer (B2C), Sovereign Clouds und Gastbenutzer konfigurieren.  In diesem Artikel können Sie sich im Allgemeinen eine autoritative Stelle als Identitätsanbieter vorstellen.

## <a name="default-authority-configuration"></a>Standardkonfiguration der autoritativen Stellen

`MSALPublicClientApplication` ist mit einer Standard-URL der autoritativen Stelle von `https://login.microsoftonline.com/common` konfiguriert, die für die meisten Azure Active Directory-Szenarios (AAD) geeignet ist. Wenn Sie keine fortgeschrittenen Szenarios wie nationale Clouds oder die Arbeit mit B2C implementieren, müssen Sie diese nicht ändern.

> [!NOTE]
> Eine moderne Authentifizierung mit Active Directory-Verbunddienste als Identitätsanbieter (ADFS) wird nicht unterstützt (Weitere Informationen finden Sie unter [ADFS für Entwickler](/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios)). ADFS wird vom Verbund unterstützt.

## <a name="change-the-default-authority"></a>Änderung der autoritativen Standardstelle

In einigen Szenarios, wie z. B. Business-to-Consumer (B2C), müssen Sie möglicherweise die autoritative Standardstelle ändern.

### <a name="b2c"></a>B2C

Die [Microsoft Authentication Library (MSAL)](reference-v2-libraries.md) benötigt eine andere Konfiguration der autoritativen Stelle, um mit B2C zu arbeiten. MSAL erkennt ein URL-Format der autoritativen Stelle selbst als B2C. Das erkannte B2C-Format der autoritativen Stelle ist `https://<host>/tfp/<tenant>/<policy>`, zum Beispiel `https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy`. Sie können aber auch alle anderen unterstützten B2C-URLs der autoritativen Stelle verwenden, indem Sie die autoritative Stelle explizit als B2C-Stelle bezeichnen.

Um ein beliebiges URL-Format für B2C zu unterstützen, kann `MSALB2CAuthority` wie folgt mit einer beliebigen URL festgelegt werden:

Objective-C
```objc
NSURL *authorityURL = [NSURL URLWithString:@"arbitrary URL"];
MSALB2CAuthority *b2cAuthority = [[MSALB2CAuthority alloc] initWithURL:authorityURL
                                                                     error:&b2cAuthorityError];
```
Swift
```swift
guard let authorityURL = URL(string: "arbitrary URL") else {
    // Handle error
    return
}
let b2cAuthority = try MSALB2CAuthority(url: authorityURL)
```

Alle autoritativen B2C-Stellen, die nicht das B2C-Standardformat verwenden, müssen als bekannte autoritative Stellen gekennzeichnet werden.

Fügen Sie jede einzelne autoritative B2C-Stelle der Liste der bekannten autoritativen Stellen hinzu, auch wenn sich diese nur in der Richtlinie unterscheiden.

Objective-C
```objc
MSALPublicClientApplicationConfig *b2cApplicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:b2cAuthority];
b2cApplicationConfig.knownAuthorities = @[b2cAuthority];
```
Swift
```swift
let b2cApplicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: b2cAuthority)
b2cApplicationConfig.knownAuthorities = [b2cAuthority]
```

Wenn Ihre App eine neue Richtlinie anfordert, muss die URL der autoritativen Stelle geändert werden, da die URL für jede Richtlinie unterschiedlich ist. 

Um eine B2C-Anwendung zu konfigurieren, legen Sie wie folgt `@property MSALAuthority *authority` mit einer Instanz von `MSALB2CAuthority` in `MSALPublicClientApplicationConfig` fest, bevor Sie `MSALPublicClientApplication` erstellen:

Objective-C
```ObjC
    // Create B2C authority URL
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy"];
    
    MSALB2CAuthority *b2cAuthority = [[MSALB2CAuthority alloc] initWithURL:authorityURL
                                                                     error:&b2cAuthorityError];
    if (!b2cAuthority)
    {
        // Handle error
        return;
    }
    
    // Create MSALPublicClientApplication configuration
    MSALPublicClientApplicationConfig *b2cApplicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                                   initWithClientId:@"your-client-id"
                                                                   redirectUri:@"your-redirect-uri"
                                                                   authority:b2cAuthority];

    // Initialize MSALPublicClientApplication
    MSALPublicClientApplication *b2cApplication =
    [[MSALPublicClientApplication alloc] initWithConfiguration:b2cApplicationConfig error:&error];
    
    if (!b2cApplication)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    // Create B2C authority URL
    guard let authorityURL = URL(string: "https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy") else {
        // Handle error
        return
    }
    let b2cAuthority = try MSALB2CAuthority(url: authorityURL)

    // Create MSALPublicClientApplication configuration
    let b2cApplicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: b2cAuthority)

    // Initialize MSALPublicClientApplication
    let b2cApplication = try MSALPublicClientApplication(configuration: b2cApplicationConfig)
} catch {
    // Handle error
}
```

### <a name="sovereign-clouds"></a>Sovereign Clouds

Wenn Ihre App in einer Sovereign Cloud läuft, müssen Sie möglicherweise die URL der autoritativen Stelle in der `MSALPublicClientApplication` ändern. Das folgende Beispiel legt die URL der autoritativen Stelle für die Arbeit mit der deutschen AAD-Cloud fest:

Objective-C
```objc
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.de/common"];
    MSALAuthority *sovereignAuthority = [MSALAuthority authorityWithURL:authorityURL error:&authorityError];
    
    if (!sovereignAuthority)
    {
        // Handle error
        return;
    }
    
    MSALPublicClientApplicationConfig *applicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:sovereignAuthority];
    
    
    MSALPublicClientApplication *sovereignApplication = [[MSALPublicClientApplication alloc] initWithConfiguration:applicationConfig error:&error];
    
    
    if (!sovereignApplication)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    guard let authorityURL = URL(string: "https://login.microsoftonline.de/common") else {
        //Handle error
        return
    }
    let sovereignAuthority = try MSALAuthority(url: authorityURL)
            
    let applicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: sovereignAuthority)
            
    let sovereignApplication = try MSALPublicClientApplication(configuration: applicationConfig)
} catch {
    // Handle error
}
```

Möglicherweise sollten Sie verschiedene Bereiche an jede Sovereign Cloud übergeben. Welche Bereiche gesendet werden sollen, hängt von der Ressource ab, die Sie verwenden. Sie können beispielsweise `"https://graph.microsoft.com/user.read"` in der weltweiten Cloud und `"https://graph.microsoft.de/user.read"` in der deutschen Cloud verwenden.

### <a name="signing-a-user-into-a-specific-tenant"></a>Signieren eines Benutzers mit einem bestimmten Mandanten

Wenn die URL der autoritativen Stelle auf `"login.microsoftonline.com/common"` festgelegt ist, wird der Benutzer bei seinem Basismandanten angemeldet. Einige Apps müssen den Benutzer jedoch möglicherweise mit einem anderen Mandanten signieren, und andere Apps funktionieren nur mit einem einzigen Mandanten.

Um den Benutzer mit einem bestimmten Mandanten zu signieren, konfigurieren Sie `MSALPublicClientApplication` mit einer bestimmten autoritativen Stelle. Beispiel:

`https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4`

Im Folgenden wird gezeigt, wie Sie einen Benutzer mit einem bestimmten Mandanten signieren können:

Objective-C
```objc
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4"];
    MSALAADAuthority *tenantedAuthority = [[MSALAADAuthority alloc] initWithURL:authorityURL error:&authorityError];
    
    if (!tenantedAuthority)
    {
        // Handle error
        return;
    }
    
    MSALPublicClientApplicationConfig *applicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:tenantedAuthority];
    
    MSALPublicClientApplication *application =
    [[MSALPublicClientApplication alloc] initWithConfiguration:applicationConfig error:&error];
    
    if (!application)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    guard let authorityURL = URL(string: "https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4") else {
        //Handle error
        return
    }    
    let tenantedAuthority = try MSALAADAuthority(url: authorityURL)
            
    let applicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: tenantedAuthority)
            
    let application = try MSALPublicClientApplication(configuration: applicationConfig)
} catch {
    // Handle error
}
```

## <a name="supported-authorities"></a>Unterstützte autoritative Stellen

### <a name="msalauthority"></a>MSALAuthority

Die `MSALAuthority`-Klasse ist die unbenannte Basisklasse für die MSAL-Klassen der autoritativen Stellen. Versuchen Sie nicht, eine Instanz davon mit `alloc` oder `new` zu erstellen. Stattdessen erstellen Sie entweder direkt eine ihrer Unterklassen (`MSALAADAuthority`, `MSALB2CAuthority`), oder Sie verwenden die Factory-Methode `authorityWithURL:error:`, um Unterklassen mit einer URL der autoritativen Stelle zu erstellen.

Verwenden Sie die `url`-Eigenschaft, um eine normalisierte URL der autoritativen Stelle zu erhalten. Zusätzliche Parameter und Pfadkomponenten oder Fragmente, die nicht Teil der autoritativen Stelle sind, werden nicht in der zurückgegebenen normalisierten URL der autoritativen Stelle angezeigt.

Im Folgenden finden Sie Unterklassen von `MSALAuthority`, die Sie je nach der zu verwendenden autoritativen Stelle instanziieren können.

### <a name="msalaadauthority"></a>MSALAADAuthority

`MSALAADAuthority` ist eine autoritative AAD-Stelle. Die URL der autoritativen Stelle sollte im folgenden Format vorliegen, wobei `<port>` optional ist: `https://<host>:<port>/<tenant>`

### <a name="msalb2cauthority"></a>MSALB2CAuthority

`MSALB2CAuthority` ist eine autoritative B2C-Stelle. Standardmäßig sollte die B2C-URL der autoritativen Stelle im folgenden Format vorliegen, wobei `<port>` optional ist: `https://<host>:<port>/tfp/<tenant>/<policy>`. MSAL unterstützt aber auch andere beliebige B2C-Formate der autoritativen Stellen.

## <a name="next-steps"></a>Nächste Schritte

Hier erfahren Sie mehr zu [Authentifizierungsfluss und Anwendungsszenarios](authentication-flows-app-scenarios.md)