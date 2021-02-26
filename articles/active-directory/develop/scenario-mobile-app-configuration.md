---
title: Konfigurieren mobiler Apps, die Web-APIs aufrufen | Azure
titleSuffix: Microsoft identity platform
description: Erfahren Sie, wie Sie den Code Ihrer mobilen App für den Aufruf einer Web-API konfigurieren
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/16/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 1187c768a54dd04d25b6de0e6785ebb81a7dfc24
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584430"
---
# <a name="configure-a-mobile-app-that-calls-web-apis"></a>Konfigurieren einer mobilen App, die Web-APIs aufruft

Nachdem Sie Ihre Anwendung erstellt haben, erfahren Sie, wie Sie den Code mithilfe der App-Registrierungsparameter konfigurieren. Mobile Anwendungen halten im Hinblick auf ihre Einpassung in ihr Erstellungsframework eine Reihe von Schwierigkeiten bereit.

## <a name="find-msal-support-for-mobile-apps"></a>MSAL-Unterstützung für mobile Apps

Die folgenden MSAL-Typen (Microsoft Authentication Library) unterstützen mobile Apps.

MSAL | BESCHREIBUNG
------------ | ----------
![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Zum Entwickeln portierbarer Anwendungen verwendet. MSAL.NET unterstützt beim Entwickeln von mobilen Anwendungen die folgenden Plattformen: Universelle Windows-Plattform (UWP), Xamarin.iOS und Xamarin.Android.
![MSAL.iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL.iOS | Zum Entwickeln nativer iOS-Anwendungen mit Objective-C oder Swift verwendet.
![MSAL.Android](media/sample-v2-code/logo_android.png) <br/> MSAL.Android | Zum Entwickeln nativer Android-Anwendungen in Java für Android verwendet.

## <a name="instantiate-the-application"></a>Instanziierung der Anwendung

### <a name="android"></a>Android

Mobile Anwendungen verwenden die `PublicClientApplication`-Klasse. Hier sehen Sie, wie sie instanziiert wird:

```Java
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);
```

### <a name="ios"></a>iOS

Mobile Anwendungen unter iOS müssen die `MSALPublicClientApplication`-Klasse instanziieren. Verwenden Sie zum Instanziieren der Klasse den folgenden Code.

```objc
NSError *msalError = nil;

MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

[Zusätzliche MSALPublicClientApplicationConfig-Eigenschaften](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplicationConfig.html#/Configuration%20options) können die Standardautorität überschreiben, einen Umleitungs-URI angeben oder das Verhalten der MSAL-Tokenzwischenspeicherung ändern.

### <a name="xamarin-or-uwp"></a>Xamarin oder UWP

In den folgenden Abschnitten wird erläutert, wie Sie die Anwendung für Xamarin.iOS-, Xamarin.Android- und UWP-Apps instanziieren.

#### <a name="instantiate-the-application"></a>Instanziierung der Anwendung

In Xamarin oder UWP ist das einfachste Verfahren zum Instanziieren die Verwendung des folgenden Codes. In diesem Code ist `ClientId` die GUID der registrierten App.

```csharp
var app = PublicClientApplicationBuilder.Create(clientId)
                                        .Build();
```

Mithilfe zusätzlicher `With<Parameter>`-Methoden werden das übergeordnete Element der Benutzeroberfläche festgelegt, die Standardautorität außer Kraft gesetzt, ein Clientname und eine Version für die Telemetrie sowie ein Umleitungs-URI und die zu verwendende HTTP-Factory angegeben. Die HTTP-Factory kann beispielsweise für das Behandeln von Proxys und das Angeben von Telemetrie und Protokollierung verwendet werden.

In den folgenden Abschnitten finden Sie weitere Informationen zum Instanziieren der Anwendung.

##### <a name="specify-the-parent-ui-window-or-activity"></a>Angeben des übergeordneten Elements der Benutzeroberfläche, des übergeordneten Fensters oder der übergeordneten Aktivität

Unter Android müssen Sie vor der interaktiven Authentifizierung die übergeordnete Aktivität übergeben. Wenn Sie unter iOS einen Broker verwenden, müssen Sie `ViewController` übergeben. Auf dieselbe Weise möchten Sie möglicherweise auf der UWP das übergeordnete Fenster übergeben. Sie übergeben es beim Abrufen des Tokens. Sie können aber beim Erstellen der App außerdem einen Rückruf als Delegat festlegen, der `UIParent` zurückgibt.

```csharp
IPublicClientApplication application = PublicClientApplicationBuilder.Create(clientId)
  .ParentActivityOrWindowFunc(() => parentUi)
  .Build();
```

Unter Android empfehlen wir, [`CurrentActivityPlugin`](https://github.com/jamesmontemagno/CurrentActivityPlugin) zu verwenden. Der resultierende Code des `PublicClientApplication`-Generators sieht wie in diesem Beispiel aus:

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

##### <a name="find-more-app-building-parameters"></a>Suchen weiterer Parameter für die App-Erstellung

Eine Liste aller Methoden, die in `PublicClientApplicationBuilder` verfügbar sind, finden Sie in der [Methodenliste](/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods).

Eine Beschreibung aller Optionen, die in `PublicClientApplicationOptions` verfügbar gemacht werden, finden Sie in der [Referenzdokumentation](/dotnet/api/microsoft.identity.client.publicclientapplicationoptions).

## <a name="tasks-for-xamarin-ios"></a>Aufgaben für Xamarin iOS

Wenn Sie MSAL.NET für Xamarin iOS verwenden, führen Sie die folgenden Aufgaben aus.

* [Überschreiben und Implementieren der `OpenUrl`-Funktion in `AppDelegate`](msal-net-xamarin-ios-considerations.md#implement-openurl)
* [Aktivieren von Keychaingruppen](msal-net-xamarin-ios-considerations.md#enable-keychain-access)
* [Aktivieren von Tokencachefreigaben](msal-net-xamarin-ios-considerations.md#enable-token-cache-sharing-across-ios-applications)
* [Aktivieren des Keychainzugriffs](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

Weitere Informationen finden Sie unter [Überlegungen zu Xamarin iOS](msal-net-xamarin-ios-considerations.md).

## <a name="tasks-for-msal-for-ios-and-macos"></a>Aufgaben für MSAL für iOS und macOS

Diese Aufgaben sind erforderlich, wenn Sie MSAL für iOS und macOS verwenden:

* [Implementieren Sie den `openURL`-Rückruf](#brokered-authentication-for-msal-for-ios-and-macos).
* [Aktivieren Sie Keychainzugriffsgruppen](howto-v2-keychain-objc.md).
* [Passen Sie Browser und WebViews an](customize-webviews.md).

## <a name="tasks-for-xamarinandroid"></a>Aufgaben für Xamarin.Android

Wenn Sie Xamarin.Android verwenden, führen Sie die folgenden Aufgaben aus:

- [Sicherstellen, dass die Steuerung nach dem Ende des interaktiven Teils des Authentifizierungsablaufs wieder an MSAL übergeben wird](msal-net-xamarin-android-considerations.md#ensure-that-control-returns-to-msal)
- [Aktualisieren des Android-Manifests](msal-net-xamarin-android-considerations.md#update-the-android-manifest)
- [Verwenden der eingebetteten Webansicht (optional)](msal-net-xamarin-android-considerations.md#use-the-embedded-web-view-optional)
- [Problembehandlung nach Bedarf](msal-net-xamarin-android-considerations.md#troubleshooting)

Weitere Informationen finden Sie unter [Überlegungen zu Xamarin.Android](msal-net-xamarin-android-considerations.md).

Überlegungen zu den Browsern unter Android finden Sie unter [Spezifische Überlegungen zu Xamarin.Android mit MSAL.NET](msal-net-system-browser-android-considerations.md).

#### <a name="tasks-for-uwp"></a>Aufgaben für UWP

Auf der UWP können Sie Unternehmensnetzwerke verwenden. In den folgenden Abschnitten werden die Aufgaben erläutert, die Sie im Unternehmensszenario ausführen sollten.

Weitere Informationen finden Sie unter [Für UWP spezifische Überlegungen mit MSAL.NET](msal-net-uwp-considerations.md).

## <a name="configure-the-application-to-use-the-broker"></a>Konfigurieren der Anwendung für die Verwendung des Brokers

Unter Android und iOS ermöglichen Broker Folgendes:

- **Einmaliges Anmelden (Single Sign-On, SSO)** : Sie können SSO für Geräte verwenden, die bei Azure Active Directory (Azure AD) registriert sind. Wenn Sie SSO verwenden, müssen sich Ihre Benutzer nicht bei jeder Anwendung anmelden.
- **Geräteidentifikation**: Diese Einstellung ermöglicht bedingte Zugriffsrichtlinien im Zusammenhang mit Azure AD Geräten. Der Authentifizierungsprozess verwendet das Gerätezertifikat, das beim Hinzufügen des Geräts zum Arbeitsplatz erstellt wurde.
- **Überprüfung der Anwendungsidentifikation**: Wenn eine Anwendung den Broker aufruft, übergibt sie ihre Umleitungs-URL. Diese wird dann vom Broker überprüft.

### <a name="enable-the-broker-on-xamarin"></a>Aktivieren des Brokers in Xamarin

Um den Broker in Xamarin zu aktivieren, verwenden Sie den `WithBroker()`-Parameter, wenn Sie die `PublicClientApplicationBuilder.CreateApplication`-Methode aufrufen. Standardmäßig ist `.WithBroker()` auf „true“ festgelegt.

Um die Brokerauthentifizierung für Xamarin.iOS zu aktivieren, führen Sie die Schritte im [Xamarin.iOS-Abschnitt](#enable-brokered-authentication-for-xamarin-ios) in diesem Artikel aus.

### <a name="enable-the-broker-for-msal-for-android"></a>Aktivieren des Brokers für MSAL für Android

Informationen zum Aktivieren eines Brokers unter Android finden Sie unter [Brokerauthentifizierung in Android](msal-android-single-sign-on.md).

### <a name="enable-the-broker-for-msal-for-ios-and-macos"></a>Aktivieren des Brokers für MSAL für iOS und macOS

Brokerauthentifizierung ist standardmäßig für Azure AD-Szenarien in MSAL für iOS und macOS aktiviert.

In den folgenden Abschnitten finden Sie Anweisungen zum Konfigurieren Ihrer Anwendung für die Unterstützung von Brokerauthentifizierung für MSAL für Xamarin.iOS oder MSAL für iOS und macOS. Einige der Schritte in den beiden Anweisungslisten unterscheiden sich.

### <a name="enable-brokered-authentication-for-xamarin-ios"></a>Aktivieren von Brokerauthentifizierung für Xamarin iOS

Führen Sie die Schritte in diesem Abschnitt aus, um Ihre Xamarin.iOS-App für die Kommunikation mit der [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458)-App zu aktivieren.

#### <a name="step-1-enable-broker-support"></a>Schritt 1: Aktivieren der Brokerunterstützung

Die Brokerunterstützung ist standardmäßig deaktiviert. Sie aktivieren Sie für eine einzelne `PublicClientApplication`-Klasse. Verwenden Sie den `WithBroker()`-Parameter, wenn Sie die `PublicClientApplication`-Klasse mithilfe von `PublicClientApplicationBuilder` erstellen. Der `WithBroker()`-Parameter ist standardmäßig auf „true“ festgelegt.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

#### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Schritt 2: Aktualisieren von AppDelegate zum Verarbeiten des Rückrufs

Wenn MSAL.NET den Broker aufruft, führt der Broker wiederum einen Rückruf an Ihre Anwendung aus. Der Rückruf erfolgt mithilfe der `AppDelegate.OpenUrl`-Methode. Da MSAL auf die Antwort vom Broker wartet, muss Ihre Anwendung kooperieren, damit der Rückruf von MSAL.NET erfolgen kann. Sie richten dieses Verhalten durch Aktualisieren der Datei `AppDelegate.cs` zum Überschreiben der Methode ein, wie aus dem folgenden Code zu ersehen.

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url,
                             string sourceApplication,
                             NSObject annotation)
{
 if (AuthenticationContinuationHelper.IsBrokerResponse(sourceApplication))
 {
  AuthenticationContinuationHelper.SetBrokerContinuationEventArgs(url);
  return true;
 }
 else if (!AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url))
 {
  return false;
 }
 return true;
}
```

Diese Methode wird jedes Mal aufgerufen, wenn die Anwendung gestartet wird. Sie bietet die Gelegenheit, die Antwort vom Broker zu verarbeiten und den von MSAL.NET eingeleiteten Authentifizierungsprozess abzuschließen.

#### <a name="step-3-set-a-uiviewcontroller"></a>Schritt 3: Festlegen von „UIViewController()“

Für Xamarin iOS brauchen Sie normalerweise kein Objektfenster festzulegen. In diesem Fall sollten Sie es jedoch festlegen, sodass Sie an einen Broker senden und Antworten von ihm empfangen können. Um ein Objektfenster festzulegen, legen Sie in `AppDelegate.cs` einen `ViewController` fest.

Führen Sie die folgenden Schritte aus, um das Objektfenster festzulegen:

1. Legen Sie in der Datei `AppDelegate.cs` das `App.RootViewController`-Objekt auf einen neuen `UIViewController()` fest. Mit dieser Einstellung wird sichergestellt, dass der aufzurufende Broker `UIViewController`enthält. Wenn der Controller nicht ordnungsgemäß festgelegt wird, erhalten Sie möglicherweise folgende Fehlermeldung:

    `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker."`

1. Verwenden Sie für den `AcquireTokenInteractive`-Aufruf `.WithParentActivityOrWindow(App.RootViewController)`. Übergeben Sie den Verweis an das Objektfenster, das Sie verwenden möchten. Hier sehen Sie ein Beispiel:

    In `App.cs`:
    ```csharp
       public static object RootViewController { get; set; }
    ```
    In `AppDelegate.cs`:
    ```csharp
       LoadApplication(new App());
       App.RootViewController = new UIViewController();
    ```
    Im `AcquireToken`-Aufruf:
    ```csharp
    result = await app.AcquireTokenInteractive(scopes)
                 .WithParentActivityOrWindow(App.RootViewController)
                 .ExecuteAsync();
    ```

#### <a name="step-4-register-a-url-scheme"></a>Schritt 4: Registrieren eines URL-Schemas

MSAL.NET verwendet URLs, um den Broker aufzurufen und die Brokerantwort dann an Ihre App zurückzugeben. Um die Rundreise abzuschließen, registrieren Sie in der Datei `Info.plist` das URL-Schema Ihrer App.

Führen Sie die folgenden Schritte aus, um das URL-Schema Ihrer App zu registrieren:

1. Stellen Sie `CFBundleURLSchemes` `msauth` voran.
1. Fügen Sie `CFBundleURLName` am Ende hinzu. Folgen Sie diesem Muster:

   `$"msauth.(BundleId)"`

   Hier identifiziert `BundleId` Ihr Gerät eindeutig. Wenn `BundleId` z. B. `yourcompany.xforms` ist, lautet Ihr URL-Schema `msauth.com.yourcompany.xforms`.

  
      Dieses URL-Schema wird Teil des Umleitungs-URI, der Ihre App eindeutig identifiziert, wenn er die Antwort des Brokers empfängt.

   ```XML
    <key>CFBundleURLTypes</key>
       <array>
         <dict>
           <key>CFBundleTypeRole</key>
           <string>Editor</string>
           <key>CFBundleURLName</key>
           <string>com.yourcompany.xforms</string>
           <key>CFBundleURLSchemes</key>
           <array>
             <string>msauth.com.yourcompany.xforms</string>
           </array>
         </dict>
       </array>
   ```

#### <a name="step-5-add-to-the-lsapplicationqueriesschemes-section"></a>Schritt 5: Hinzufügen zum Abschnitt LSApplicationQueriesSchemes

MSAL verwendet `–canOpenURL:`, um zu überprüfen, ob der Broker auf dem Gerät installiert ist. Unter iOS 9 wurden die Schemas, die von einer Anwendung abgefragt werden können, von Apple fest vorgegeben.

Fügen Sie dem Abschnitt `LSApplicationQueriesSchemes` der `Info.plist`-Datei `msauthv2` hinzu, wie im folgenden Codebeispiel zu sehen:

```XML
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="brokered-authentication-for-msal-for-ios-and-macos"></a>Brokerauthentifizierung für MSAL für iOS und macOS

Brokerauthentifizierung ist für Azure AD-Szenarien standardmäßig aktiviert.

#### <a name="step-1-update-appdelegate-to-handle-the-callback"></a>Schritt 1: Aktualisieren von AppDelegate zum Verarbeiten des Rückrufs

Wenn MSAL für iOS und macOS den Broker aufruft, führt der Broker einen Rückruf Ihrer Anwendung mithilfe der `openURL`-Methode aus. Da MSAL auf die Antwort vom Broker wartet, muss Ihre Anwendung kooperieren, um MSAL zurückzurufen. Sie richten diese Funktionalität durch Aktualisieren der Datei `AppDelegate.m` zum Überschreiben der Methode ein, wie aus den folgenden Codebeispielen zu ersehen.

```objc
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
    return [MSALPublicClientApplication handleMSALResponse:url
                                         sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {

        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }

        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

Wenn Sie `UISceneDelegate` unter iOS 13 oder höher eingeführt haben, platzieren Sie den MSAL-Rückruf stattdessen in den `scene:openURLContexts:` von `UISceneDelegate`. MSAL `handleMSALResponse:sourceApplication:` darf für jede URL nur ein Mal aufgerufen werden.

Weitere Informationen finden Sie in der [Apple-Dokumentation](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc).

#### <a name="step-2-register-a-url-scheme"></a>Schritt 2: Registrieren eines URL-Schemas

MSAL für iOS und macOS verwendet URLs, um den Broker aufzurufen und die Brokerantwort dann an Ihre App zurückzugeben. Um den Roundtrip abzuschließen, registrieren Sie in der Datei `Info.plist` ein URL-Schema für Ihre App.

So registrieren Sie ein Schema für Ihre App:

1. Stellen Sie dem benutzerdefinierten URL-Schema `msauth` als Präfix voran.

1. Fügen Sie Ihren Bundlebezeichner am Ende Ihres Schemas hinzu. Folgen Sie diesem Muster:

   `$"msauth.(BundleId)"`

   Hier identifiziert `BundleId` Ihr Gerät eindeutig. Wenn `BundleId` z. B. `yourcompany.xforms` ist, lautet Ihr URL-Schema `msauth.com.yourcompany.xforms`.

    Dieses URL-Schema wird Teil des Umleitungs-URI, der Ihre App eindeutig identifiziert, wenn er die Antwort des Brokers empfängt. Achten Sie darauf, dass der Umleitungs-URI im Format `msauth.(BundleId)://auth` für Ihre Anwendung im [Azure-Portal](https://portal.azure.com) registriert ist.

   ```XML
   <key>CFBundleURLTypes</key>
   <array>
       <dict>
           <key>CFBundleURLSchemes</key>
           <array>
               <string>msauth.[BUNDLE_ID]</string>
           </array>
       </dict>
   </array>
   ```

#### <a name="step-3-add-lsapplicationqueriesschemes"></a>Schritt 3: Hinzufügen von LSApplicationQueriesSchemes

Fügen Sie `LSApplicationQueriesSchemes` hinzu, um Aufrufe an die Microsoft Authenticator-App zuzulassen, wenn sie installiert ist.

> [!NOTE]
> Das `msauthv3`-Schema wird benötigt, wenn Ihre App mithilfe von Xcode 11 und höher kompiliert wird.

Hier sehen Sie ein Beispiel für das Hinzufügen von `LSApplicationQueriesSchemes`:

```XML
<key>LSApplicationQueriesSchemes</key>
<array>
  <string>msauthv2</string>
  <string>msauthv3</string>
</array>
```

### <a name="brokered-authentication-for-xamarinandroid"></a>Brokerauthentifizierung für Xamarin.Android

Informationen zum Aktivieren eines Brokers unter Android finden Sie unter [Brokerauthentifizierung in Xamarin.Android](msal-net-use-brokers-with-xamarin-apps.md#brokered-authentication-for-android).

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem nächsten Artikel in diesem Szenario fort: [Abrufen eines Tokens](scenario-mobile-acquire-token.md).