---
title: Verwenden von Brokern mit Xamarin iOS und Android | Azure
titleSuffix: Microsoft identity platform
description: Erfahren Sie, wie Xamarin iOS-Anwendungen eingerichtet werden, die Microsoft Authenticator und die Microsoft Authentication Library for .NET (MSAL.NET) verwenden können. Erfahren Sie außerdem, wie Sie von der Azure AD Authentication Library for .NET (ADAL.NET) zu Microsoft Authentication Library for .NET (MSAL.NET) migrieren.
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/08/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 7fa13a328a55b0e9eaa546e70bf0711f4f011cf1
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96173429"
---
# <a name="use-microsoft-authenticator-or-intune-company-portal-on-xamarin-applications"></a>Verwenden von Microsoft Authenticator oder des Intune-Unternehmensportals für Xamarin-Anwendungen

Unter Android und iOS ermöglichen Broker wie Microsoft Authenticator und das Android-spezifische Microsoft Intune-Unternehmensportal Folgendes:

- **Einmaliges Anmelden (Single Sign-On, SSO)** : Die Benutzer müssen sich nicht bei jeder Anwendung anmelden.
- **Geräteidentifikation**: Der Broker greift auf das Gerätezertifikat zu. Dieses Zertifikat wird auf dem Gerät erstellt, wenn es dem Arbeitsplatz hinzugefügt wird.
- **Überprüfung der Anwendungsidentifikation**: Wenn eine Anwendung den Broker aufruft, übergibt sie ihre Umleitungs-URL. Die URL wird dann vom Broker überprüft.

Verwenden Sie zum Aktivieren einer dieser Funktionen den `WithBroker()`-Parameter, wenn Sie die `PublicClientApplicationBuilder.CreateApplication`-Methode aufrufen. Der `.WithBroker()`-Parameter ist standardmäßig auf „true“ festgelegt.

Das Einrichten der Brokerauthentifizierung in der Microsoft-Authentifizierungsbibliothek für .NET (MSAL.NET) variiert je nach Plattform:

* [iOS-Anwendungen](#brokered-authentication-for-ios)
* [Android-Anwendungen](#brokered-authentication-for-android)

## <a name="brokered-authentication-for-ios"></a>Brokerauthentifizierung für iOS

Führen Sie die folgenden Schritte aus, um Ihre Xamarin.iOS-App für die Kommunikation mit der [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458)-App zu aktivieren. Für iOS 13 sollten Sie [Überlegungen zur Verwendung von Xamarin iOS mit MSAL.NET](./msal-net-xamarin-ios-considerations.md) lesen.

### <a name="step-1-enable-broker-support"></a>Schritt 1: Aktivieren der Brokerunterstützung

Sie müssen Brokerunterstützung für einzelne Instanzen von `PublicClientApplication` aktivieren. Die Unterstützung ist standardmäßig deaktiviert. Wenn Sie `PublicClientApplication` über `PublicClientApplicationBuilder`erstellen, verwenden Sie den `WithBroker()`-Parameter, wie im folgenden Beispiel gezeigt. Der `WithBroker()`-Parameter ist standardmäßig auf „true“ festgelegt.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>Schritt 2: Aktivieren des Keychainzugriffs

Um den Keychainzugriff zu aktivieren, müssen Sie über eine Keychain-Zugriffsgruppe für Ihre Anwendung verfügen. Sie können die `WithIosKeychainSecurityGroup()`-API verwenden, um die Keychainzugriffsgruppe festzulegen, wenn Sie die Anwendung erstellen:

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Weitere Informationen finden Sie unter [Aktivieren des Keychainzugriffs](msal-net-xamarin-ios-considerations.md#enable-keychain-access).

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Schritt 3: Aktualisieren von AppDelegate zum Verarbeiten des Rückrufs

Wenn MSAL.NET den Broker aufruft, führt der Broker einen Rückruf Ihrer Anwendung über die `OpenUrl`-Methode der `AppDelegate`-Klasse aus. Da MSAL auf die Antwort vom Broker wartet, muss Ihre Anwendung kooperieren, damit der Rückruf von MSAL.NET erfolgen kann. Um diese Kooperation zu ermöglichen, müssen Sie die Datei *AppDelegate.cs* zur Außerkraftsetzung der folgenden Methode aktualisieren.

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

Diese Methode wird jedes Mal aufgerufen, wenn die Anwendung gestartet wird. Sie wird verwendet, um die Antwort vom Broker zu verarbeiten und den von MSAL.NET eingeleiteten Authentifizierungsprozess abzuschließen.

### <a name="step-4-set-uiviewcontroller"></a>Schritt 4: Festlegen von „UIViewController()“

Sie müssen in der Datei *AppDelegate.cs* ein Objektfenster festlegen. Normalerweise müssen Sie das Objektfenster bei Xamarin iOS nicht festlegen, aber Sie benötigen ein Objektfenster, um zu senden und Antworten von einem Broker zu empfangen.

So richten Sie das Objektfenster ein:

1. Legen Sie in der Datei *AppDelegate.cs* `App.RootViewController` auf einen neuen `UIViewController()` fest. Mit dieser Zuweisung wird sichergestellt, dass der Aufruf an den Broker `UIViewController`enthält. Wenn diese Einstellung falsch zugewiesen wird, wird möglicherweise der folgende Fehler angezeigt:

      `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`

1. Verwenden Sie im `AcquireTokenInteractive`-Aufruf `.WithParentActivityOrWindow(App.RootViewController)`, und übergeben Sie dann den Verweis auf das Objektfenster, das Sie verwenden möchten.

    In *App.cs*:

    ```csharp
       public static object RootViewController { get; set; }
    ```

    In *AppDelegate.cs*:

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

### <a name="step-5-register-a-url-scheme"></a>Schritt 5: Registrieren eines URL-Schemas

MSAL.NET verwendet URLs, um den Broker aufzurufen und die Brokerantwort dann an Ihre App zurückzugeben. Um den Roundtrip abzuschließen, registrieren Sie in der Datei *Info.plist* ein URL-Schema für Ihre App.

Der `CFBundleURLSchemes`-Name muss das Präfix `msauth.` enthalten. Stellen Sie dem Präfix `CFBundleURLName` nach.

Im URL-Schema bezeichnet `BundleId` eindeutig die App `$"msauth.(BundleId)"`. Wenn `BundleId` also `com.yourcompany.xforms` ist, lautet das URL-Schema `msauth.com.yourcompany.xforms`.

> [!NOTE]
> Dieses URL-Schema wird Teil des Umleitungs-URI, der Ihre App eindeutig identifiziert, wenn sie die Antwort vom Broker empfängt.

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

### <a name="step-6-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>Schritt 6: Hinzufügen der Broker-ID im Abschnitt LSApplicationQueriesSchemes

MSAL verwendet `–canOpenURL:`, um zu überprüfen, ob der Broker auf dem Gerät installiert ist. Unter iOS 9 wurden die Schemas, die von einer Anwendung abgefragt werden können, von Apple fest vorgegeben.

Fügen Sie dem Abschnitt `LSApplicationQueriesSchemes` der *Info.plist*-Datei `msauthv2` hinzu, wie im folgenden Beispiel zu sehen:

```XML
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
    </array>
```

### <a name="step-7-add-a-redirect-uri-to-your-app-registration"></a>Schritt 7: Hinzufügen eines Umleitungs-URI zur App-Registrierung

Wenn Sie den Broker verwenden, gilt für den Umleitungs-URI eine zusätzliche Anforderung. Der Umleitungs-URI _muss_ das folgende Format aufweisen:

```csharp
$"msauth.{BundleId}://auth"
```

Hier sehen Sie ein Beispiel:

```csharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth";
```

Beachten Sie, dass der Umleitungs-URI mit dem `CFBundleURLSchemes`-Namen übereinstimmt, den Sie in der Datei *Info.plist* hinzugefügt haben.

Fügen Sie den Umleitungs-URI der Registrierung der App im [Azure-Portal](https://portal.azure.com) hinzu. Um einen ordnungsgemäß formatierten Umleitungs-URI zu generieren, verwenden Sie **App-Registrierungen** im Azure-Portal, um den Brokerumleitungs-URI aus der Bündel-ID zu generieren.

**So generieren Sie den Umleitungs-URI:**

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie unter **Azure Active Directory** > **App-Registrierungen** Ihre registrierte App aus.
1. Wählen Sie **Authentifizierung** > **Plattform hinzufügen** > **IOS/macOS** aus.
1. Geben Sie Ihre Bündel-ID ein, und wählen Sie dann **Konfigurieren** aus.

    Kopieren Sie den generierten Umleitungs-URI, der im Textfeld **Umleitungs-URI** angezeigt wird, um ihn in Ihren Code einzubeziehen:

    :::image type="content" source="media/msal-net-use-brokers-with-xamarin-apps/portal-01-ios-platform-settings.png" alt-text="iOS-Plattformeinstellungen mit generiertem Umleitungs-URI im Azure-Portal":::
1. Wählen Sie **Fertig** aus, um die Generierung des Umleitungs-URI abzuschließen.

## <a name="brokered-authentication-for-android"></a>Brokerauthentifizierung für Android

### <a name="step-1-enable-broker-support"></a>Schritt 1: Aktivieren der Brokerunterstützung

Die Brokerunterstützung wird pro `PublicClientApplication` aktiviert. Standardmäßig ist es deaktiviert. Verwenden Sie den (standardmäßig auf „true“ festgelegten) Parameter `WithBroker()`, wenn Sie die `IPublicClientApplication` über den `PublicClientApplicationBuilder` erstellen.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid) // See step #4
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Schritt 2: Aktualisieren von AppDelegate zum Verarbeiten des Rückrufs

Wenn MSAL.NET den Broker aufruft, führt der Broker wiederum einen Rückruf Ihrer Anwendung mit der `OnActivityResult()`-Methode aus. Da MSAL auf die Antwort vom Broker wartet, muss Ihre Anwendung das Ergebnis an MSAL.NET weiterleiten.

Leiten Sie das Ergebnis an die `SetAuthenticationContinuationEventArgs(int requestCode, Result resultCode, Intent data)`-Methode weiter, indem Sie die `OnActivityResult()`-Methode wie hier gezeigt überschreiben:

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
   base.OnActivityResult(requestCode, resultCode, data);
   AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

Diese Methode wird jedes Mal aufgerufen, wenn die Brokeranwendung gestartet wird. Sie wird verwendet, um die Antwort vom Broker zu verarbeiten und den von MSAL.NET gestarteten Authentifizierungsvorgang abzuschließen.

### <a name="step-3-set-an-activity"></a>Schritt 3: Festlegen einer Aktivität

Um die Brokerauthentifizierung zu aktivieren, legen Sie eine Aktivität fest, damit MSAL senden und die Antwort vom Broker empfangen kann. Stellen Sie zu diesem Zweck die Aktivität (normalerweise die `MainActivity`) für das übergeordnete Objekt `WithParentActivityOrWindow(object parent)` bereit.

Beispielsweise im Aufruf von `AcquireTokenInteractive()`:

```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow((Activity)context))
             .ExecuteAsync();
```

### <a name="step-4-add-a-redirect-uri-to-your-app-registration"></a>Schritt 4: Hinzufügen eines Umleitungs-URI zur App-Registrierung

MSAL verwendet URLs, um den Broker aufzurufen und die Brokerantwort dann an Ihre App zurückzugeben. Um diesen Roundtrip abzuschließen, müssen Sie einen **Umleitungs-URI** für Ihre App mithilfe des [Azure-Portals](https://portal.azure.com) registrieren.

Das Format des Umleitungs-URI für Ihre Anwendung hängt von dem Zertifikat ab, das zum Signieren des APK verwendet wird. Beispiel:

```
msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=
```

Der letzte Teil des URI (`hgbUYHVBYUTvuvT&Y6tr554365466=`) ist die Base64-Codierungsversion der Signatur, mit der das APK signiert ist. Wenn Sie Ihre App in Visual Studio entwickeln und Ihren Code debuggen, ohne das APK mit einem bestimmten Zertifikat zu signieren, signiert Visual Studio das APK für Sie zu Debuggingzwecken. Wenn Visual Studio das APK auf diese Weise für Sie signiert, erhält es eine eindeutige Signatur für den Computer, auf dem es erstellt wurde. Daher müssen Sie jedes Mal, wenn Sie Ihre App auf einem anderen Computer erstellen, den Umleitungs-URI im Anwendungscode und in der Anwendungsregistrierung im Azure-Portal aktualisieren, damit die App mit MSAL authentifiziert werden kann.

Beim Debuggen kann eine MSAL-Ausnahme (oder Protokollmeldung) auftreten, die besagt, dass der angegebene Umleitungs-URI falsch ist. **Die Ausnahme- oder Protokollmeldung zeigt außerdem an, welchen Umleitungs-URI Sie verwenden sollten** mit dem aktuellen Computer, auf dem Sie debuggen. Sie können den bereitgestellten Umleitungs-URI verwenden, um die Entwicklung Ihrer App fortzusetzen, solange Sie den Umleitungs-URI im Code aktualisieren und den bereitgestellten Umleitungs-URI der App-Registrierung im Azure-Portal hinzufügen.

Wenn Sie bereit sind, den Code abzuschließen, aktualisieren Sie den Umleitungs-URI im Code und in der Anwendungsregistrierung im Azure-Portal, um die Signatur des Zertifikats zu verwenden, mit dem Sie das APK signieren.

In der Praxis bedeutet dies, dass Sie erwägen sollten, einen Umleitungs-URI für jedes Entwicklungsteammitglied *sowie* einen Umleitungs-URI für die zur Produktion signierte Version des APK hinzuzufügen.

Sie können die Signatur auf ähnliche Weise wie MSAL selbst berechnen:

```csharp
   private string GetRedirectUriForBroker()
   {
      string packageName = Application.Context.PackageName;
      string signatureDigest = this.GetCurrentSignatureForPackage(packageName);
      if (!string.IsNullOrEmpty(signatureDigest))
      {
            return string.Format(CultureInfo.InvariantCulture, "{0}://{1}/{2}", RedirectUriScheme,
               packageName.ToLowerInvariant(), signatureDigest);
      }

      return string.Empty;
   }

   private string GetCurrentSignatureForPackage(string packageName)
   {
            PackageInfo info = Application.Context.PackageManager.GetPackageInfo(packageName,
               PackageInfoFlags.Signatures);
            if (info != null && info.Signatures != null && info.Signatures.Count > 0)
            {
               // First available signature. Applications can be signed with multiple signatures.
               // The order of Signatures is not guaranteed.
               Signature signature = info.Signatures[0];
               MessageDigest md = MessageDigest.GetInstance("SHA");
               md.Update(signature.ToByteArray());
               return Convert.ToBase64String(md.Digest(), Base64FormattingOptions.None);
               // Server side needs to register all other tags. ADAL will
               // send one of them.
            }
   }
```

Sie haben zudem die Möglichkeit, die Signatur für das Paket mithilfe des **Schlüsselverwaltungstools** mit den folgenden Befehlen zu erwerben:

* Windows:
    ```console
    keytool.exe -list -v -keystore "%LocalAppData%\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android
    ````
* macOS:
    ```console
    keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
    ````

### <a name="step-5-optional-fall-back-to-the-system-browser"></a>Schritt 5 (optional): Fallback auf den Systembrowser

Wenn MSAL für die Verwendung des Brokers konfiguriert ist, der Broker jedoch nicht installiert ist, führt MSAL ein Fallback auf die Verwendung einer Webansicht (eines Browsers) durch. MSAL versucht, sich mit dem standardmäßigen Systembrowser auf dem Gerät zu authentifizieren. Dies gelingt nicht, da der Umleitungs-URI für den Broker konfiguriert ist und der Systembrowser nicht weiß, wie er damit zurück zu MSAL navigieren kann. Um den Fehler zu vermeiden, können Sie einen *Absichtsfilter* mit dem Brokerumleitungs-URI konfigurieren, den Sie in Schritt 4 verwendet haben.

Ändern Sie das Manifest Ihrer Anwendung, um den Absichtsfilter hinzuzufügen:

```xml
<!-- NOTE the SLASH (required) that prefixes the signature value in the path attribute.
     The signature value is the Base64-encoded signature discussed above. -->
<intent-filter>
      <data android:scheme="msauth"
                    android:host="Package Name"
                    android:path="/Package Signature"/>
```

Wenn Ihr Umleitungs-URI z. B. `msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=` lautet, sollte das Manifest wie der folgende XML-Ausschnitt aussehen.

Der Schrägstrich (`/`) vor der Signatur im `android:path`-Wert ist **erforderlich**.

```xml
<!-- NOTE the SLASH (required) that prefixes the signature value in the path attribute.
     The signature value is the Base64-encoded signature discussed above. -->
<intent-filter>
      <data android:scheme="msauth"
                    android:host="com.microsoft.xforms.testApp"
                    android:path="/hgbUYHVBYUTvuvT&Y6tr554365466="/>
```

Alternativ können Sie MSAL so konfigurieren, dass MSAL ein Fallback auf den eingebetteten Browser durchführt, der nicht von einem Umleitungs-URI abhängig ist:

```csharp
.WithUseEmbeddedWebUi(true)
```

## <a name="troubleshooting-tips-for-android-brokered-authentication"></a>Tipps zur Problembehandlung für die Android-Brokerauthentifizierung

Hier finden Sie einige Tipps zum Vermeiden von Problemen bei der Implementierung der Brokerauthentifizierung unter Android:

- **Umleitungs-URI**: Fügen Sie Ihrer Anwendungsregistrierung im [Azure-Portal](https://portal.azure.com/) einen Umleitungs-URI hinzu. Ein fehlender oder falscher Umleitungs-URI ist ein Problem, auf das Entwickler häufiger treffen.
- **Brokerversion**: Installieren Sie die mindestens erforderliche Version der Broker-Apps. Beide Apps können für die Brokerauthentifizierung unter Android verwendet werden.
  - [Intune-Unternehmensportal](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal) (Version 5.0.4689.0 oder höher)
  - [Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator) (Version 6.2001.0140 oder höher).
- **Broker-Rangfolge**: MSAL kommuniziert mit dem *ersten installierten Broker* auf dem Gerät, wenn mehrere Broker installiert sind.

    Beispiel: Wenn Sie Microsoft Authenticator zuerst und dann das Intune-Unternehmensportal installieren, wird die Brokerauthentifizierung *nur* auf dem Microsoft Authenticator erfolgen.
- **Protokolle**: Wenn ein Problem mit der Brokerauthentifizierung auftritt, können Sie mithilfe der Brokerprotokolle die Ursache diagnostizieren.
  - Anzeigen der Microsoft Authenticator-Protokolle:

    1. Wählen Sie oben rechts in der App die Menüschaltfläche aus.
    1. Wählen Sie **Hilfe** > **Protokolle senden** > **Protokolle anzeigen** aus.
    1. Wählen Sie **Alles kopieren** aus, um die Brokerprotokolle in die Zwischenablage des Geräts zu kopieren.

    Um diese Protokolle zum Debuggen zu nutzen, senden Sie sie am besten per E-Mail an sich selbst und zeigen sie auf Ihrem Entwicklungscomputer an. Möglicherweise ist es einfacher, wenn Sie die Protokolle auf Ihrem Computer analysieren anstatt auf dem Gerät selbst. Sie können auch einen Test-Editor unter Android verwenden, um die Protokolle als Textdatei zu speichern, und dann die Datei per USB-Kabel auf einen Computer kopieren.

  - Anzeigen der Intune-Unternehmensportal-Protokolle:

    1. Wählen Sie oben links in der App die Menüschaltfläche aus.
    1. Wählen Sie **Einstellungen** > **Diagnosedaten** aus.
    1. Wählen Sie **Protokolle kopieren** aus, um die Brokerprotokolle auf die SD-Karte des Geräts zu kopieren.
    1. Stellen Sie mit einem USB-Kabel eine Verbindung zwischen dem Gerät und einem Computer her, um die Protokolle auf dem Entwicklungscomputer anzuzeigen.

    Sobald Sie über die Protokolle verfügen, können Sie sie über die Korrelations-ID nach Ihren Authentifizierungsversuchen durchsuchen. Die Korrelations-ID wird jeder Authentifizierungsanforderung angefügt. Um Fehler zu finden, die vom Authentifizierungsendpunkt der Microsoft Identity Platform zurückgegeben werden, suchen Sie nach `AADSTS`.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über [Überlegungen zur Verwendung der Universellen Windows-Plattform mit MSAL.NET](msal-net-uwp-considerations.md).
