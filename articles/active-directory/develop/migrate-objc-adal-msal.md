---
title: Leitfaden für die Migration von ADAL zu MSAL (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Erfahren Sie mehr über die Unterschiede zwischen MSAL für iOS/macOS und der Azure AD-Authentifizierungsbibliothek für Objective-C (ADAL.ObjC) sowie über die Migration zu MSAL für iOS/macOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: fb66d8a4bf97a6f8a10534c9c4459123ad6a2654
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97107918"
---
# <a name="migrate-applications-to-msal-for-ios-and-macos"></a>Migrieren von Anwendungen zu MSAL für iOS und macOS

Die Azure Active Directory-Authentifizierungsbibliothek ([ADAL Objective-C](https://github.com/AzureAD/azure-activedirectory-library-for-objc)) wurde erstellt, um mit Azure Active Directory-Konten über den v1.0-Endpunkt arbeiten zu können.

Die Microsoft Authentication Library für iOS und macOS (MSAL) wurde mit dem Ziel erstellt, mit allen Microsoft-Identitäten wie Azure AD-Konten, persönlichen Microsoft-Konten und Azure AD B2C-Konten über Microsoft Identity Platform (genauer: über den Azure AD v2.0-Endpunkt) arbeiten zu können.

Microsoft Identity Platform weist einige wesentliche Unterschiede im Vergleich zu Azure Active Directory v1.0 auf. In diesem Artikel werden diese Unterschiede erläutert. Zusätzlich werden Hinweise zum Migrieren einer App von ADAL zu MSAL bereitgestellt.

## <a name="adal-and-msal-app-capability-differences"></a>Funktionale Unterschiede zwischen ADAL- und MSAL-Apps

### <a name="who-can-sign-in"></a>Wer kann sich anmelden?

* ADAL unterstützt nur Geschäfts-, Schul- und Unikonten (auch als Azure AD-Konten bekannt).
* MSAL unterstützt persönliche Microsoft-Konten (MSA-Konten), für die Hotmail.com, Outlook.com oder Live.com genutzt wird.
* MSAL unterstützt Geschäfts-, Schul- und Unikonten sowie Azure AD B2C-Konten.

### <a name="standards-compliance"></a>Einhaltung von Standards

* Der Microsoft Identity Platform-Endpunkt ist mit den Standards OAuth 2.0 und OpenID Connect konform.

### <a name="incremental-and-dynamic-consent"></a>Inkrementelle und dynamische Zustimmung

* Für den Azure Active Directory v1.0-Endpunkt müssen alle Berechtigungen während der Anwendungsregistrierung im Voraus deklariert werden. Dies bedeutet, dass diese Berechtigungen statisch sind.
* Microsoft Identity Platform ermöglicht das dynamische Anfordern von Berechtigungen. Apps dürfen nur bei Bedarf Berechtigungen anfordern.

Weitere Informationen zu den Unterschieden zwischen Azure Active Directory v1.0 und Microsoft Identity Platform finden Sie unter [Gründe für eine Aktualisierung auf Microsoft Identity Platform (v2.0)](../azuread-dev/azure-ad-endpoint-comparison.md).

## <a name="adal-and-msal-library-differences"></a>Unterschiede zwischen der ADAL- und MSAL-Bibliothek

Die öffentliche MSAL-API spiegelt einige wesentliche Unterschiede zwischen Azure AD v1.0 und Microsoft Identity Platform wider.

### <a name="msalpublicclientapplication-instead-of-adauthenticationcontext"></a>Verwendung von MSALPublicClientApplication statt ADAuthenticationContext

`ADAuthenticationContext` ist das erste Objekt, das eine ADAL-App erstellt. Es stellt eine ADAL-Instanz dar, die durch eine Instanziierung erzeugt wurde. Apps erstellen eine neue Instanz von `ADAuthenticationContext` für jede Kombination aus Azure Active Directory-Cloud und -Mandant (dieser entspricht der autoritativen Stelle). Mit nur einem `ADAuthenticationContext`-Objekt können Token für mehrere öffentliche Clientanwendungen abgerufen werden.

In MSAL findet die Hauptinteraktion über ein `MSALPublicClientApplication`-Objekt statt, das nach dem [öffentlichen OAuth 2.0-Client](https://tools.ietf.org/html/rfc6749#section-2.1) modelliert wird. Mit einer Instanz von `MSALPublicClientApplication` kann mit mehreren AAD-Clouds und -Mandanten interagiert werden, ohne dass eine neue Instanz für jede autoritative Stelle erstellt werden muss. Für die meisten Apps ist eine `MSALPublicClientApplication`-Instanz ausreichend.

### <a name="scopes-instead-of-resources"></a>Verwendung von Bereichen statt Ressourcen

In ADAL musste eine App einen *Ressourcenbezeichner* wie `https://graph.microsoft.com` bereitstellen, um Token vom Azure Active Directory v1.0-Endpunkt abzurufen. Für eine Ressource können mehrere Bereiche oder OAuth 2.0-Berechtigungen im unterstützten App-Manifest definiert werden. Dadurch konnten Client-Apps Token von dieser Ressource für eine bestimmte Gruppe von Bereichen anfordern, die bei der App-Registrierung vorab definiert wurden.

In MSAL stellen Apps für eine Anforderung nicht einen einzelnen Ressourcenbezeichner, sondern mehrere Bereiche bereit. Ein Bereich ist ein Ressourcenbezeichner, auf den ein Berechtigungsname in der Form Ressource/Berechtigung folgt. Zum Beispiel, `https://graph.microsoft.com/user.read`

Bereiche können in MSAL auf zwei Arten bereitgestellt werden:

* Durch eine Liste aller Berechtigungen, die für die App erforderlich sind. Beispiel: 

    `@[@"https://graph.microsoft.com/directory.read", @"https://graph.microsoft.com/directory.write"]`

    In diesem Fall fordert die App die Berechtigungen `directory.read` und `directory.write` an. Der Benutzer wird aufgefordert, in die Erteilung dieser Berechtigungen einzuwilligen, falls er diesen Schritt für die App noch nicht ausgeführt hat. Die Anwendung erhält möglicherweise auch zusätzliche Berechtigungen, in die der Benutzer zuvor eingewilligt hat. Der Benutzer wird nur im Fall von neuen oder noch nicht erteilten Berechtigungen zu einer Einwilligung aufgefordert.

* Durch den Bereich `/.default`.

Dies ist der integrierte Bereich für jede Anwendung. Er bezieht sich auf die statische Liste der Berechtigungen, die bei der Anwendungsregistrierung konfiguriert wurden. Das Verhalten ähnelt dem von `resource`. Dies kann bei der Migration hilfreich sein, um sicherzustellen, dass ähnliche Bereiche verwendet werden und eine vergleichbare Servicequalität für Benutzer aufrechterhalten wird.

Fügen Sie dem Ressourcenbezeichner `/.default` an, um den Bereich `/.default` zu verwenden. Beispiel: `https://graph.microsoft.com/.default`. Wenn die Adresse Ihrer Ressource mit einem Schrägstrich (`/`) endet, sollten Sie dennoch `/.default` einschließlich des führenden Schrägstrichs anfügen. Das Ergebnis ist dann ein Bereich mit zwei Schrägstrichen (`//`).

Weitere Informationen zur Verwendung des Bereichs „/.default“ finden Sie [hier](./v2-permissions-and-consent.md#the-default-scope).

### <a name="supporting-different-webview-types--browsers"></a>Unterstützung unterschiedlicher WebView-Typen und -Browser

ADAL unterstützt nur UIWebView/WKWebView für iOS und WebView für macOS. MSAL für iOS unterstützt mehr Optionen zum Anzeigen von Webinhalten bei der Anforderung eines Autorisierungscodes. `UIWebView` wird nicht mehr unterstützt. Dadurch können die Servicequalität für Benutzer gesteigert und die Sicherheit erhöht werden.

Standardmäßig wird für MSAL unter iOS die Webkomponente [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc) verwendet, die Apple zur Authentifizierung auf Geräten mit iOS 12 und höher empfiehlt. Mit ihr werden die Vorteile des einmaligen Anmeldens (SSO) nutzbar, da Cookies von Apps und vom Safari-Browser gemeinsam genutzt werden.

Sie können je nach App-Anforderung und der gewünschten Servicequalität für Benutzer eine andere Webkomponente verwenden. Weitere Optionen finden Sie unter [Unterstützte Webansichtstypen](customize-webviews.md).

Bei der Migration von ADAL zu MSAL bietet `WKWebView` für Benutzer eine Servicequalität, die der von ADAL unter iOS und macOS am nächsten kommt. Wir empfehlen Ihnen, nach Möglichkeit zu `ASWebAuthenticationSession` unter iOS zu migrieren. Für macOS empfehlen wir die Verwendung von `WKWebView`.

### <a name="account-management-api-differences"></a>Unterschiede bei der API zur Kontoverwaltung

Wenn Sie die ADAL-Methoden `acquireToken()` oder `acquireTokenSilent()` aufrufen, erhalten Sie ein `ADUserInformation`-Objekt mit einer Liste der Ansprüche des `id_token`, das das zu authentifizierende Konto darstellt. Außerdem gibt `ADUserInformation` auf der Grundlage des `upn`-Anspruchs eine `userId` zurück. Nach dem ersten interaktiven Tokenabruf erwartet ADAL, dass Entwickler eine `userId` für alle automatischen Aufrufe angeben.

ADAL bietet keine API zum Abrufen bekannter Benutzeridentitäten. Stattdessen ist die App für das Speichern und Verwalten dieser Konten zuständig.

MSAL bietet eine Reihe von APIs zum Auflisten aller Konten, die MSAL bekannt sind, wobei ein Abruf eines Tokens nicht notwendig ist.

Ebenso wie ADAL gibt MSAL Kontoinformationen zurück, die eine Liste der Ansprüche des `id_token` enthalten. Dieses ist Teil des `MSALAccount`-Objekts, das im `MSALResult`-Objekt enthalten ist.

MSAL bietet eine Reihe von APIs zum Entfernen von Konten. Die entfernten Konten sind nicht mehr für die App zugänglich. Nachdem ein Konto entfernt wurde, wird der Benutzer bei späteren Aufrufen für Tokenabrufe interaktiv aufgefordert, ein solches Token abzurufen. Es werden nur Konten aus Clientanwendungen entfernt, von denen dieser Vorgang gestartet wird. Die Konten anderer Apps, die auf dem Gerät oder über den Systembrowser ausgeführt werden, werden nicht entfernt. Dadurch wird sichergestellt, dass der Benutzer auch nach der Abmeldung von einer einzelnen App SSO verwenden kann.

MSAL gibt außerdem einen Kontobezeichner zurück, der verwendet werden kann, um später ein Token automatisch anzufordern. Der Kontobezeichner (auf den über die `identifier`-Eigenschaft im `MSALAccount`-Objekt zugegriffen werden kann) kann jedoch nicht angezeigt werden. Auch das Format ist unbekannt. Darüber hinaus sollten Sie auf eine Auswertung oder Analyse des Bezeichners verzichten.

### <a name="migrating-the-account-cache"></a>Migrieren des Kontocaches

Bei der Migration von ADAL speichern Apps normalerweise die `userId` von ADAL, die nicht über den `identifier`-Wert verfügt, der für MSAL erforderlich ist. Zur Migration kann eine App einmalig ein MSAL-Konto mithilfe der Benutzer-ID von ADAL mit der folgenden API abfragen:

`- (nullable MSALAccount *)accountForUsername:(nonnull NSString *)username error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

Diese API liest sowohl den MSAL- als auch den ADAL-Cache, um das Konto anhand der ADAL-Benutzer-ID (UPN) zu finden.

Wenn das Konto gefunden wird, sollte der Entwickler dieses zum automatischen Abrufen des Tokens verwenden. Durch den ersten automatischen Tokenabruf wird für das Konto ein Upgrade durchgeführt. Das MSAL-Ergebnis enthält einen MSAL-kompatiblen Kontobezeichner (`identifier`), der vom Entwickler verwendet werden kann. Danach sollte für Kontosuchvorgänge nur `identifier` in Kombination mit der folgenden API verwendet werden:

`- (nullable MSALAccount *)accountForIdentifier:(nonnull NSString *)identifier error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

Die `userId` von ADAL kann theoretisch für alle MSAL-Vorgänge weiterhin verwendet werden. Da aber die `userId` auf dem UPN basiert, gelten für sie mehrere Einschränkungen, die zu einer schlechten Servicequalität für Benutzer führen. Wenn sich beispielsweise der UPN ändert, muss sich der Benutzer erneut anmelden. Es wird empfohlen, für alle Apps den nicht anzeigbaren Konto-`identifier`-Wert für alle Vorgänge zu verwenden.

Weitere Informationen finden Sie unter [Cachezustandsmigration](sso-between-adal-msal-apps-macos-ios.md).

### <a name="token-acquisition-changes"></a>Änderungen am Tokenabruf

In MSAL wurden einige Änderungen an Aufrufen vorgenommen, die für den Tokenabruf verwendet werden:

* Ebenso wie in ADAL führt `acquireTokenSilent` immer zu einer automatischen Anforderung.
* Anders als in ADAL wird durch `acquireToken` immer eine Benutzeroberfläche über die Webansicht oder über die Microsoft Authenticator-App angezeigt. Abhängig vom SSO-Zustand in der Webansicht oder in Microsoft Authenticator wird der Benutzer möglicherweise aufgefordert, seine Anmeldeinformationen einzugeben.
* In ADAL führt `acquireToken` in Kombination mit `AD_PROMPT_AUTO` zunächst zu einem automatischen Tokenabruf. Die Benutzeroberfläche wird nur angezeigt, wenn die automatische Anforderung fehlschlägt. In MSAL kann diese Logik umgesetzt werden, indem zuerst `acquireTokenSilent` aufgerufen wird. `acquireToken` wird anschließend nur dann aufgerufen, wenn der automatische Abruf fehlschlägt. Dadurch können Entwickler das Verhalten für Benutzer anpassen, bevor ein interaktiver Tokenabruf gestartet wird.

### <a name="error-handling-differences"></a>Unterschiede in der Fehlerbehandlung

MSAL unterscheidet deutlicher zwischen Fehlern, die von Ihrer App behandelt werden können, und solchen, die den Eingriff eines Benutzers erfordern. Der Entwickler muss daher eine begrenzte Anzahl von Fehlern behandeln:

* `MSALErrorInteractionRequired`: Der Benutzer muss eine interaktive Anforderung ausführen. Dies kann verschiedene Ursachen haben: Beispielsweise kann es vorkommen, dass eine Authentifizierungssitzung abgelaufen ist, eine Richtlinie für bedingten Zugriff geändert wurde, ein Aktualisierungstoken abgelaufen ist oder widerrufen wurde oder sich keine gültigen Token im Cache befinden.
* `MSALErrorServerDeclinedScopes`: Die Anforderung wurde nicht vollständig abgeschlossen, und einigen Bereichen wurde kein Zugriff gewährt. Dies kann dadurch verursacht werden, dass ein Benutzer seine Einwilligung in ein oder mehrere Bereiche verweigert.

Die Behandlung aller anderen Fehler in der [`MSALError`-Liste](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128) ist optional. Sie können die Informationen in diesen Fehlern verwenden, um die Servicequalität für Benutzer zu verbessern.

Weitere Informationen zur MSAL-Fehlerbehandlung finden Sie unter [Behandeln von Fehlern und Ausnahmen in MSAL](msal-error-handling-ios.md).

### <a name="broker-support"></a>Brokerunterstützung

MSAL unterstützt ab Version 0.3.0 die Brokerauthentifizierung mit der Microsoft Authenticator-App. Microsoft Authenticator unterstützt außerdem Szenarios mit bedingtem Zugriff. Ein Beispiel für derartige Szenarios ist die Nutzung von Gerätekonformitätsrichtlinien, durch die der Benutzer aufgefordert wird, das Gerät über Intune zu registrieren oder eine Registrierung mit AAD durchzuführen, um ein Token abzurufen. Ein weiteres Beispiel sind Richtlinien für bedingten Zugriff in Kombination mit der mobilen Anwendungsverwaltung (MAM). Für diese Richtlinien ist ein Konformitätsnachweis erforderlich, damit eine App ein Token abrufen kann.

So aktivieren Sie einen Broker für Ihre Anwendung:

1. Registrieren Sie ein brokerkompatibles Umleitungs-URI-Format für die Anwendung. Das brokerkompatible Umleitungs-URI-Format lautet `msauth.<app.bundle.id>://auth`. Ersetzen Sie `<app.bundle.id>` durch die Bundle-ID Ihrer Anwendung. Wenn Sie von ADAL migrieren und Ihre Anwendung bereits einen Broker nutzen kann, sind keine weiteren Schritte erforderlich. Der vorherige Umleitungs-URI ist vollständig mit MSAL kompatibel, sodass Sie mit Schritt 3 fortfahren können.

2. Fügen Sie das Umleitungs-URI-Schema Ihrer Anwendungsdatei „info.plist“ hinzu. Für den standardmäßigen MSAL-Umleitungs-URI lautet das Format `msauth.<app.bundle.id>`. Beispiel:

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

3. Fügen Sie der Datei „info.plist“ Ihrer App unter „LSApplicationQueriesSchemes“ folgende Schemas hinzu:

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

4. Fügen Sie der Datei „AppDelegate.m“ für die Verarbeitung von Rückrufen Folgendes hinzu: Objective-C:
    
    ```objc
    - (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options`
    {
        return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
    }
    ```
    
    Swift:
    
    ```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }
    ```

### <a name="business-to-business-b2b"></a>B2B (Business-to-Business)

In ADAL erstellen Sie eigenständige Instanzen von `ADAuthenticationContext` für jeden Mandanten, für den die App Token anfordert. Dies ist in MSAL nicht mehr erforderlich. In MSAL können Sie eine einzelne Instanz von `MSALPublicClientApplication` erstellen und für jede AAD-Cloud und -Organisation verwenden, indem Sie eine andere autoritative Stelle für acquireToken- und acquireTokenSilent-Aufrufe angeben.

## <a name="sso-in-partnership-with-other-sdks"></a>SSO im Zusammenspiel mit anderen SDKs

Mithilfe der folgenden SDKs ist es möglich, einen einheitlichen Cache zu verwenden und so SSO für MSAL unter iOS zu ermöglichen:

- ADAL Objective-C 2.7.x und höher
- MSAL.NET für Xamarin 2.4.x und höher
- ADAL.NET für Xamarin 4.4.x und höher

SSO wird durch die iOS-Schlüsselbundfreigabe ermöglicht und kann nur für Apps verwendet werden, die über dasselbe Apple Developer-Konto veröffentlicht wurden.

Die Nutzung von SSO mithilfe der iOS-Schlüsselbundfreigabe ist die einzige Möglichkeit, den SSO-Vorgang automatisch auszuführen.

Unter macOS ist mit MSAL auch SSO für andere MSAL-Anwendungen, die auf iOS und macOS basieren, sowie für Anwendungen auf Grundlage von ADAL Objective-C möglich.

MSAL unter iOS unterstützt noch zwei andere Arten von SSO:

* SSO über den Webbrowser. MSAL für iOS unterstützt `ASWebAuthenticationSession`. Damit wird SSO über Cookies bereitgestellt, die von anderen Apps auf dem Gerät und insbesondere dem Safari-Browser gemeinsam verwendet werden.
* SSO über einen Authentifizierungsbroker. Auf einem iOS-Gerät wird Microsoft Authenticator als Authentifizierungsbroker verwendet. Dieser kann Richtlinien für den bedingten Zugriff umsetzen, die z. B. ein konformes Gerät fordern. Außerdem stellt dieser Authentifizierungsbroker SSO für registrierte Geräte bereit. MSAL SDKS ab Version 0.3.0 unterstützen standardmäßig einen Broker.

## <a name="intune-mam-sdk"></a>Intune MAM SDK

Das [Intune MAM SDK](/intune/app-sdk-get-started) unterstützt MSAL für iOS ab Version [11.1.2](https://github.com/msintuneappsdk/ms-intune-app-sdk-ios/releases/tag/11.1.2).

## <a name="msal-and-adal-in-the-same-app"></a>MSAL und ADAL innerhalb derselben App

ADAL-Versionen ab Version 2.7.0 können nicht mit MSAL in derselben Anwendung verwendet werden. Der Hauptgrund liegt im gemeinsamen Code der zusammen genutzten Submodule. Wenn Sie Ihrer Anwendung sowohl das ADAL- als auch das MSAL-Framework hinzufügen, gibt es zwei Instanzen derselben Klasse, da Objective-C keine Namespaces unterstützt. Es gibt keine Garantie dafür, welche Klasse zur Laufzeit ausgewählt wird. Wenn beide SDKs dieselbe Version der in Konflikt stehenden Klasse verwenden, funktioniert die App möglicherweise weiterhin. Wenn es sich jedoch um eine andere Version handelt, kann es zu unerwarteten App-Abstürzen kommen, die schwer zu diagnostizieren sind.

Das Ausführen von ADAL und MSAL in derselben Produktionsanwendung wird nicht unterstützt. Wenn Sie jedoch nur Tests durchführen und Benutzer von ADAL Objective-C zu MSAL für iOS und macOS migrieren, können Sie [ADAL Objective-C 2.6.10](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases/tag/2.6.10) weiterhin verwenden. Dies ist die einzige Version, die mit MSAL in derselben Anwendung verwendet werden kann. Für diese ADAL-Version werden keine neuen Featureupdates bereitgestellt. Sie sollten sie daher nur zu Migrations- und Testzwecken verwenden. Langfristig sollten Sie für Ihre App auf die gemeinsame Verwendung von ADAL und MSAL verzichten.

Die Nutzung von ADAL und MSAL innerhalb derselben App wird nicht unterstützt.
Die gemeinsame Verwendung von ADAL und MSAL in unterschiedlichen Anwendungen wird hingegen vollständig unterstützt.

## <a name="practical-migration-steps"></a>Praktische Migrationsschritte

### <a name="app-registration-migration"></a>App-Registrierung und -Migration

Sie müssen Ihre vorhandene AAD-Anwendung nicht anpassen, um zu MSAL zu wechseln und AAD-Konten zu aktivieren. Wenn Ihre ADAL-basierte Anwendung die Brokerauthentifizierung jedoch nicht unterstützt, müssen Sie einen neuen Umleitungs-URI für die Anwendung registrieren, bevor Sie zu MSAL wechseln können.

Der Umleitungs-URI sollte das folgende Format aufweisen: `msauth.<app.bundle.id>://auth`. Ersetzen Sie `<app.bundle.id>` durch die Bundle-ID Ihrer Anwendung. Geben Sie den Umleitungs-URI im [Azure-Portal](https://aka.ms/MobileAppReg) an.

Wenn für iOS die zertifikatbasierte Authentifizierung unterstützt werden soll, muss ein zusätzlicher Umleitungs-URI in Ihrer Anwendung im Azure-Portal im folgenden Format registriert werden: `msauth://code/<broker-redirect-uri-in-url-encoded-form>`. Zum Beispiel, `msauth://code/msauth.com.microsoft.mybundleId%3A%2F%2Fauth`

Empfohlen wird, dass alle Apps beide Umleitungs-URIs registrieren.

Wenn die inkrementelle Einwilligung unterstützt werden soll, müssen Sie die APIs und Berechtigungen, auf die Ihre App gemäß der Konfiguration zugreifen kann, in Ihrer App-Registrierung auf der Registerkarte **API-Berechtigungen** auswählen.

Wenn Sie von ADAL migrieren und sowohl AAD- als auch MSA-Konten unterstützen möchten, müssen Sie die vorhandene Anwendungsregistrierung aktualisieren, damit beides unterstützt wird. Nicht empfohlen wird, eine vorhandene Produktions-App so zu aktualisieren, dass sowohl AAD als auch MSA gleichzeitig unterstützt werden. Erstellen Sie stattdessen eine weitere Client-ID, die sowohl AAD als auch MSA für Tests unterstützt. Überprüfen Sie anschließend, ob alle Szenarios funktionieren, und aktualisieren Sie erst danach die vorhandene App.

### <a name="add-msal-to-your-app"></a>Hinzufügen von MSAL zu Ihrer App

Sie können Ihrer App das MSAL SDK mithilfe Ihres bevorzugten Paketverwaltungstools hinzufügen. Detaillierte Anweisungen finden Sie [hier](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Installation).

### <a name="update-your-apps-infoplist-file"></a>Aktualisieren der App-Datei „info.plist“

Fügen Sie nur im Fall von iOS der Datei „info.plist“ das Umleitungs-URI-Schema Ihrer Anwendung hinzu. Bei Apps, die mit einem ADAL-Broker kompatibel sind, sollte dieses bereits vorhanden sein. Das standardmäßige Umleitungs-URI-Schema für MSAL weist das folgende Format auf: `msauth.<app.bundle.id>`.  

```xml
<key>CFBundleURLSchemes</key>
<array>
    <string>msauth.<app.bundle.id></string>
</array>
```

Fügen Sie der Datei „info.plist“ Ihrer App unter `LSApplicationQueriesSchemes` die folgenden Schemas hinzu.

```xml
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
     <string>msauthv3</string>
</array>
```

### <a name="update-your-appdelegate-code"></a>Aktualisieren des AppDelegate-Codes

Fügen Sie nur im Fall von iOS der Datei „AppDelegate.m“ Folgendes hinzu:

Objective-C:

```objc
- (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options`
{
    return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

Swift:

```swift
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
    return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}
```

**Bei Verwendung von Xcode 11** müssen Sie den MSAL-Rückruf stattdessen in der Datei `SceneDelegate` anordnen.
Wenn Sie zur Erzielung von Kompatibilität mit älteren iOS-Versionen sowohl „UISceneDelegate“ als auch „UIApplicationDelegate“ unterstützen, muss der MSAL-Rückruf in beide Dateien eingefügt werden.

Objective-C:

```objc
 - (void)scene:(UIScene *)scene openURLContexts:(NSSet<UIOpenURLContext *> *)URLContexts
 {
     UIOpenURLContext *context = URLContexts.anyObject;
     NSURL *url = context.URL;
     NSString *sourceApplication = context.options.sourceApplication;
     
     [MSALPublicClientApplication handleMSALResponse:url sourceApplication:sourceApplication];
 }
```

Swift:

```swift
func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
        
        guard let urlContext = URLContexts.first else {
            return
        }
        
        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication
        
        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
```

Dadurch kann MSAL Antworten von der Broker- und der Webkomponente verarbeiten.
Dies war in ADAL nicht erforderlich, da für die App-Delegatmethoden automatisch ein Swizzling ausgeführt wurde. Das manuelle Hinzufügen ist weniger fehleranfällig und bietet der Anwendung mehr Kontrolle.

### <a name="enable-token-caching"></a>Aktivieren der Zwischenspeicherung von Token

MSAL speichert die Token Ihrer App standardmäßig in der iOS- oder macOS-Keychain zwischen. 

So aktivieren Sie die Zwischenspeicherung von Token:
1. Stellen Sie sicher, dass Ihre Anwendung ordnungsgemäß signiert ist.
2. Rufen Sie Ihr Xcode-Projekt auf, und klicken Sie auf „Settings“ (Einstellungen) und dann auf die Registerkarte **Capabilities** (Funktionen) > **Enable Keychain Sharing** (Keychain-Freigabe aktivieren).
3. Klicken Sie auf **+** , und geben Sie den folgenden Eintrag für **Keychain Groups** (Schlüsselbundgruppen) an: 3.a Geben Sie für iOS `com.microsoft.adalcache` ein. 3.b Geben Sie für macOS `com.microsoft.identity.universalstorage` ein.

### <a name="create-msalpublicclientapplication-and-switch-to-its-acquiretoken-and-acquiretokesilent-calls"></a>Erstellen von MSALPublicClientApplication und Wechseln zu den zugehörigen acquireToken- und acquireTokeSilent-Aufrufen

Sie können `MSALPublicClientApplication` mit folgendem Code erstellen:

Objective-C:

```objc
NSError *error = nil;
MSALPublicClientApplicationConfig *configuration = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];
    
MSALPublicClientApplication *application =
[[MSALPublicClientApplication alloc] initWithConfiguration:configuration
                                                     error:&error];
```

Swift:

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application
            
} catch let error as NSError {
  // handle error here
}
```

Rufen Sie anschließend die API zur Kontoverwaltung auf, um zu ermitteln, ob sich Konten im Cache befinden:

Objective-C:

```objc
NSString *accountIdentifier = nil /*previously saved MSAL account identifier */;
NSError *error = nil;
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:&error];
```

Swift:

```swift
// definitions that need to be initialized
let application: MSALPublicClientApplication!
let accountIdentifier: String! /*previously saved MSAL account identifier */

do {
  let account = try application.account(forIdentifier: accountIdentifier)
  // continue with account usage
} catch let error as NSError {
  // handle error here
}
```



Alternative können Sie auch alle Konten lesen:

Objective-C:

```objc
NSError *error = nil;
NSArray<MSALAccount *> *accounts = [application allAccounts:&error];
```

Swift:

```swift
let application: MSALPublicClientApplication!
do {
  let accounts = try application.allAccounts()
  // continue with account usage
} catch let error as NSError {
  // handle error here
}
```



Wenn ein Konto gefunden wird, rufen Sie die MSAL-`acquireTokenSilent`-API auf:

Objective-C:

```objc
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"<your-resource-here>/.default"] account:account];
    
[application acquireTokenSilentWithParameters:silentParameters
                              completionBlock:^(MSALResult *result, NSError *error)
{
    if (result)
    {
        NSString *accessToken = result.accessToken;
        // Use your token
    }
    else
    {
        // Check the error
        if ([error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
        {
            // Interactive auth will be required
        }
            
        // Other errors may require trying again later, or reporting authentication problems to the user
    }
}];
```

Swift:

```swift
let application: MSALPublicClientApplication!
let account: MSALAccount!
        
let silentParameters = MSALSilentTokenParameters(scopes: ["<your-resource-here>/.default"], 
                                                 account: account)
application.acquireTokenSilent(with: silentParameters) {
  (result: MSALResult?, error: Error?) in
  if let accessToken = result?.accessToken {
     // use accessToken
  }
  else {
    // Check the error
    guard let error = error else {
      assert(true, "callback should contain a valid result or error")
      return
    }
    
    let nsError = error as NSError
    if (nsError.domain == MSALErrorDomain
        && nsError.code == MSALError.interactionRequired.rawValue) {
      // Interactive auth will be required
    }
                
    // Other errors may require trying again later, or reporting authentication problems to the user
  }
}
```



## <a name="next-steps"></a>Nächste Schritte

Hier erfahren Sie mehr zu [Authentifizierungsfluss und Anwendungsszenarios](authentication-flows-app-scenarios.md)