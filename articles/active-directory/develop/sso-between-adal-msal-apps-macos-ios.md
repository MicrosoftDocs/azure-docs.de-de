---
title: Einmaliges Anmelden (SSO) zwischen ADAL- und MSAL-Apps (iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Erfahren Sie, wie Sie einmaliges Anmelden zwischen ADAL- und MSAL-Apps realisieren
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 396e9cfeace8791a59dec4a9c9c7203212f57304
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584246"
---
# <a name="how-to-sso-between-adal-and-msal-apps-on-macos-and-ios"></a>Vorgehensweise: SSO zwischen ADAL- und MSAL-Apps auf macOS und iOS

Die Microsoft Authentication Library (MSAL) für iOS kann den SSO-Zustand mit [ADAL Objective-C](https://github.com/AzureAD/azure-activedirectory-library-for-objc) zwischen Anwendungen teilen. Sie können Ihre Apps in Ihrem eigenen Tempo zu MSAL migrieren, sodass Ihre Benutzer weiterhin von App-übergreifendem SSO profitieren – sogar bei einer Kombination aus ADAL- und MSAL-basierten Apps.

Wenn Sie eine Anleitung zum Einrichten von SSO zwischen Apps mit dem MSAL SDK suchen, lesen Sie [SSO zwischen mehreren Apps im Hintergrund](single-sign-on-macos-ios.md#silent-sso-between-apps). Dieser Artikel konzentriert sich auf SSO zwischen ADAL und MSAL.

Die Besonderheiten beim Implementieren von SSO hängen von der ADAL-Version ab, die Sie verwenden.

## <a name="adal-27x"></a>ADAL 2.7.x

Dieser Abschnitt zeigt die SSO-Unterschiede zwischen MSAL und ADAL 2.7.x

### <a name="cache-format"></a>Cacheformat

ADAL 2.7.x kann das MSAL-Cacheformat lesen. Für App-übergreifendes SSO mit der Version ADAL 2.7.x müssen Sie nichts Weiteres tun. Machen Sie sich jedoch der Unterschiede bei den Kontobezeichnern bewusst, die diese beiden Bibliotheken unterstützen.

### <a name="account-identifier-differences"></a>Unterschiede bei Kontobezeichnern

MSAL und ADAL verwenden unterschiedliche Kontobezeichner. ADAL verwendet UPN als primären Kontobezeichner. MSAL verwendet einen nicht darstellbaren Kontobezeichner, der auf einer Objekt-ID und einer Mandanten-ID für AAD-Konten sowie auf einen `sub`-Anspruch für andere Arten von Konten basiert.

Wenn Sie ein `MSALAccount`-Objekt im MSAL-Ergebnis erhalten, enthält es einen Kontobezeichner in der Eigenschaft `identifier`. Die Anwendung sollte diesen Bezeichner für nachfolgende Anforderungen im Hintergrund verwenden.

Zusätzlich zu `identifier` enthält das Objekt `MSALAccount` einen darstellbaren Bezeichner namens `username`, der es nach `userId` in ADAL verschiebt. `username` gilt nicht als eindeutiger Bezeichner und kann sich jederzeit ändern, daher sollte er nur für Abwärtskompatibilitätsszenarios mit ADAL verwendet werden. MSAL unterstützt Cacheabfragen entweder mit `username` oder `identifier`, wobei die Abfrage mit `identifier` empfohlen wird.

Die folgende Tabelle fasst die Unterschiede bei Kontobezeichnern zwischen ADAL und MSAL zusammen:

| Kontobezeichner                | MSAL                                                         | ADAL 2.7.x      | Ältere ADAL-Versionen (vor ADAL 2.7.x) |
| --------------------------------- | ------------------------------------------------------------ | --------------- | ------------------------------ |
| darstellbarer Bezeichner            | `username`                                                   | `userId`        | `userId`                       |
| eindeutiger nicht darstellbarer Bezeichner | `identifier`                                                 | `homeAccountId` | Nicht zutreffend                            |
| Konto-ID nicht bekannt               | Abfrage aller Konten über die `allAccounts:`-API in `MSALPublicClientApplication` | –             | Nicht zutreffend                            |

Dies ist die `MSALAccount`-Schnittstelle mit den Bezeichnern:

```objc
@protocol MSALAccount <NSObject>

/*!
 Displayable user identifier. Can be used for UI and backward compatibility with ADAL.
 */
@property (readonly, nullable) NSString *username;

/*!
 Unique identifier for the account.
 Save this for account lookups from cache at a later point.
 */
@property (readonly, nullable) NSString *identifier;

/*!
 Host part of the authority string used for authentication based on the issuer identifier.
 */
@property (readonly, nonnull) NSString *environment;

/*!
 ID token claims for the account.
 Can be used to read additional information about the account, e.g. name
 Will only be returned if there has been an id token issued for the client Id for the account's source tenant.
 */
@property (readonly, nullable) NSDictionary<NSString *, NSString *> *accountClaims;

@end
```

### <a name="sso-from-msal-to-adal"></a>SSO von MSAL zu ADAL

Wenn Sie eine MSAL-App und eine ADAL-App haben, und sich der Benutzer zuerst in der MSAL-basierten App anmeldet, können Sie SSO in der ADAL-App erhalten, indem Sie den `username` aus dem `MSALAccount`-Objekt speichern und ihn als `userId` an Ihre ADAL-basierte App übergeben. ADAL kann dann die Kontoinformationen im Hintergrund mit der `acquireTokenSilentWithResource:clientId:redirectUri:userId:completionBlock:`-API finden.

### <a name="sso-from-adal-to-msal"></a>SSO von ADAL zu MSAL

Wenn Sie eine MSAL-App und eine ADAL-App haben und sich der Benutzer zuerst bei der ADAL-basierten App anmeldet, können Sie die ADAL-Benutzer-ID für die Suche nach Konten in MSAL verwenden. Dies gilt auch bei der Migration von ADAL zu MSAL.

#### <a name="adals-homeaccountid"></a>homeAccountId von ADAL

ADAL 2.7.x gibt über diese Eigenschaft die `homeAccountId`-Eigenschaft im Objekt `ADUserInformation` im Ergebnis zurück:

```objc
/*! Unique AAD account identifier across tenants based on user's home OID/home tenantId. */
@property (readonly) NSString *homeAccountId;
```

`homeAccountId` in ADAL entspricht `identifier` in MSAL. Sie können diesen Bezeichner speichern, um ihn in MSAL für die Suche nach Konten mit der `accountForIdentifier:error:`-API zu verwenden.

#### <a name="adals-userid"></a>`userId` von ADAL

Wenn `homeAccountId` nicht verfügbar ist, oder Sie nur den darstellbaren Bezeichner haben, können Sie mit `userId` von ADAL das Konto in MSAL suchen.

Suchen Sie in MSAL zunächst ein Konto mit `username` oder `identifier`. Verwenden Sie immer `identifier` für die Abfrage, falls diese Klasse verfügbar ist, und verwenden Sie `username` nur als Fallback. Wenn ein Konto gefunden wird, verwenden Sie das Konto in den `acquireTokenSilent`-Aufrufen.

Objective-C:

```objc
NSString *msalIdentifier = @"previously.saved.msal.account.id";
MSALAccount *account = nil;
    
if (msalIdentifier)
{
    // If you have MSAL account id returned either from MSAL as identifier or ADAL as homeAccountId, use it
    account = [application accountForIdentifier:@"my.account.id.here" error:nil];
}
else
{
    // Fallback to ADAL userId for migration
    account = [application accountForUsername:@"adal.user.id" error:nil];
}
    
if (!account)
{
  // Account not found.
  return;
}

MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:account];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift:

```swift
        
let msalIdentifier: String?
var account: MSALAccount
        
do {
  if let msalIdentifier = msalIdentifier {
    account = try application.account(forIdentifier: msalIdentifier)
  }
  else {
    account = try application.account(forUsername: "adal.user.id") 
  }
             
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: account)          
  application.acquireTokenSilent(with: silentParameters) {
    (result: MSALResult?, error: Error?) in
    // handle result
  }  
} catch let error as NSError {
  // handle error or account not found
}
```



MSAL-unterstützte APIs bei der Kontosuche:

```objc
/*!
 Returns account for the given account identifier (received from an account object returned in a previous acquireToken call)
 
 @param  error      The error that occurred trying to get the accounts, if any, if you're
                    not interested in the specific error pass in nil.
 */
- (nullable MSALAccount *)accountForIdentifier:(nonnull NSString *)identifier
                                         error:(NSError * _Nullable __autoreleasing * _Nullable)error;
    
/*!
Returns account for for the given username (received from an account object returned in a previous acquireToken call or ADAL)
    
@param  username    The displayable value in UserPrincipleName(UPN) format
@param  error       The error that occurred trying to get the accounts, if any, if you're
                    not interested in the specific error pass in nil.
*/
- (MSALAccount *)accountForUsername:(NSString *)username
                              error:(NSError * __autoreleasing *)error;
```

## <a name="adal-2x-266"></a>ADAL 2.x bis 2.6.6

Dieser Abschnitt behandelt die SSO-Unterschiede zwischen MSAL und ADAL 2.x bis 2.6.6.

Ältere ADAL-Versionen unterstützen standardmäßig das MSAL-Cacheformat nicht. MSAL kann das ältere ADAL-Cacheformat lesen ohne erneut nach Benutzeranmeldeinformationen zu fragen, um eine reibungslose Migration von ADAL zu MSAL zu gewährleisten.

Da `homeAccountId` in älteren ADAL-Versionen nicht verfügbar ist, müssen Sie Konten mit dem `username` suchen:

```objc
/*!
 Returns account for for the given username (received from an account object returned in a previous acquireToken call or ADAL)

 @param  username    The displayable value in UserPrincipleName(UPN) format
 @param  error       The error that occurred trying to get the accounts, if any.  If you're not interested in the specific error pass in nil.
 */
- (MSALAccount *)accountForUsername:(NSString *)username
                              error:(NSError * __autoreleasing *)error;
```

Beispiel:

Objective-C:


```objc
MSALAccount *account = [application accountForUsername:@"adal.user.id" error:nil];;
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:account];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift:

```swift
do {
  let account = try application.account(forUsername: "adal.user.id")          
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: account)
  application.acquireTokenSilent(with: silentParameters) { 
    (result: MSALResult?, error: Error?) in
    // handle result
  }   
} catch let error as NSError { 
  // handle error or account not found
}
```



Alternativ können Sie auch alle Konten lesen, die auch die Kontoinformationen von ADAL lesen:

Objective-C:

```objc
NSArray *accounts = [application allAccounts:nil];
    
if ([accounts count] == 0)
{
  // No account found.
  return; 
}
if ([accounts count] > 1)
{
  // You might want to display an account picker to user in actual application
  // For this sample we assume there's only ever one account in cache
  return;
}
    ``
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:accounts[0]];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift:

```swift
      
do {
  let accounts = try application.allAccounts()
  if accounts.count == 0 {
    // No account found.
    return
  }
  if accounts.count > 1 {
    // You might want to display an account picker to user in actual application
    // For this sample we assume there's only ever one account in cache
    return
  }
  
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: accounts[0])
  application.acquireTokenSilent(with: silentParameters) {
    (result: MSALResult?, error: Error?) in
    // handle result or error  
  }  
} catch let error as NSError { 
  // handle error
}
```



## <a name="next-steps"></a>Nächste Schritte

Hier erfahren Sie mehr zu [Authentifizierungsfluss und Anwendungsszenarios](authentication-flows-app-scenarios.md)
