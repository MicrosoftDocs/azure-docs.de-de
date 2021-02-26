---
title: Verwenden von Webbrowsern (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Erfahren Sie mehr über die besonderen Überlegungen zur Verwendung von Xamarin Android mit der Microsoft-Authentifizierungsbibliothek für .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 23ba50a6eca1e398b9d459153b84719909f2ecac
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99583738"
---
# <a name="using-web-browsers-msalnet"></a>Verwenden von Webbrowsern (MSAL.NET)

Webbrowser sind eine Voraussetzung für die interaktive Authentifizierung. MSAL.NET unterstützt standardmäßig den [Systemwebbrowser](#system-web-browser-on-xamarinios-xamarinandroid) unter Xamarin.iOS und Xamarin.Android. Je nach Ihren Anforderungen (UX, Einmaliges Anmelden, Sicherheit) [können Sie jedoch auch den eingebetteten Webbrowser](#enable-embedded-webviews-on-ios-and-android) in [Xamarin.iOS](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinios)-Apps und [Xamarin.Android](#detecting-the-presence-of-custom-tabs-on-xamarinandroid)-Apps aktivieren. Sie können den gewünschten Webbrowser auch [dynamisch auswählen](#detecting-the-presence-of-custom-tabs-on-xamarinandroid), und zwar danach, ob Chrome oder ein Browser, der benutzerdefinierte Chrome-Registerkarten in Android unterstützt, vorhanden ist. MSAL.NET unterstützt nur den Systembrowser in .NET Core-Desktopanwendungen.

## <a name="web-browsers-in-msalnet"></a>Webbrowser in MSAL.NET

### <a name="interaction-happens-in-a-web-browser"></a>Interaktion erfolgt im Webbrowser

Es ist wichtig zu wissen, dass der Inhalt des Dialogfelds beim interaktiven Abrufen eines Tokens nicht von der Bibliothek, sondern vom Sicherheitstokendienst (Security Token Service, STS) bereitgestellt wird. Der Authentifizierungsendpunkt sendet bestimmten HTML- und JavaScript-Code zurück, über den die Interaktion gesteuert wird. Dieser Code wird in einem Webbrowser oder Websteuerelement gerendert. Die Verarbeitung der HTML-Interaktion durch STS hat viele Vorteile:

- Das Kennwort (sofern angegeben) wird grundsätzlich weder von der Anwendung noch von der Authentifizierungsbibliothek gespeichert.
- Umleitungen zu anderen Identitätsanbietern werden unterstützt (z. B. Anmeldung mit einem Geschäfts-, Schul- oder Unikonto oder einem persönlichen Konto über MSAL bzw. Anmeldung mit einem Social Media-Konto über Azure AD B2C).
- Der STS kann den bedingten Zugriff steuern, beispielsweise indem der Benutzer während der Authentifizierungsphase die [mehrstufige Authentifizierung (Multi-Factor Authentication, MFA)](../authentication/concept-mfa-howitworks.md) durchführen muss (Eingabe einer Windows Hello-PIN, Telefonanruf oder Verwendung einer Authentifizierungs-App auf dem Smartphone). Falls die erforderliche mehrstufige Authentifizierung noch nicht eingerichtet ist, kann der Benutzer sie direkt im selben Dialogfeld einrichten.  Der Benutzer gibt seine Mobiltelefonnummer ein und wird durch die Schritte zur Installation einer Authentifizierungs-App und zum Scannen eines QR-Codes geführt, um sein Konto hinzuzufügen. Die servergesteuerte Interaktion ist ausgesprochen benutzerfreundlich!
- Bei Ablauf des Kennworts kann der Benutzer sein Kennwort im selben Dialogfeld ändern (zusätzliche Felder für die Eingabe des alten und neuen Kennworts werden angezeigt).
- Ermöglicht das Branding des Mandanten oder der Anwendung (Images) durch den Azure AD-Mandantenadministrator/-Anwendungsbesitzer.
- Der Benutzer kann seine Einwilligung geben, dass die Anwendung unmittelbar nach der Authentifizierung im Namen des Benutzers Zugriff auf Ressourcen/Geltungsbereiche erhält.

### <a name="embedded-vs-system-web-ui"></a>Eingebettete Webbenutzeroberfläche im Vergleich zur System-Webbenutzeroberfläche

MSAL.NET ist eine Bibliothek mit mehreren Frameworks und verfügt über frameworkspezifischen Code zum Hosten eines Browsers in einem Benutzeroberflächen-Steuerelement (in .NET Classic wird z. B. WinForms verwendet, in Xamarin werden native mobile Steuerelemente verwendet und so weiter). Dieses Steuerelement wird als `embedded`-Webbenutzeroberfläche bezeichnet. Alternativ kann MSAL.NET auch den Standardsystembrowser des Betriebssystems starten.

Im Allgemeinen wird empfohlen, den Plattformstandard zu verwenden, und das ist in der Regel der Systembrowser. Der Systembrowser kann die Benutzer besser speichern, die sich zuvor angemeldet haben. Sie ändern dieses Verhalten mit `WithUseEmbeddedWebView(bool)`.

### <a name="at-a-glance"></a>Auf einen Blick

| Framework        | Eingebettet | System | Standard |
| ------------- |-------------| -----| ----- |
| .NET Classic     | Ja | Ja^ | Eingebettet |
| .NET Core     | Nein | Ja^ | System |
| .NET Standard | Nein | Ja^ | System |
| UWP | Ja | Nein | Eingebettet |
| Xamarin.Android | Ja | Ja  | System |
| Xamarin.iOS | Ja | Ja  | System |
| Xamarin.Mac| Ja | Nein | Eingebettet |

^ Erfordert den Umleitungs-URI „http://localhost“

## <a name="system-web-browser-on-xamarinios-xamarinandroid"></a>Systemwebbrowser unter Xamarin.iOS und Xamarin.Android

MSAL.NET unterstützt standardmäßig den Systemwebbrowser unter Xamarin.iOS, Xamarin.Android und .NET Core. Für alle Plattformen, die eine Benutzeroberfläche (d. h. nicht .NET Core) bereitstellen, stellt die Bibliothek, in die ein Webbrowser-Steuerelement eingebettet ist, ein Dialogfeld bereit. MSAL.NET verwendet darüber hinaus eine eingebettete Webansicht für die .NET-Desktopplattform und einen WAB für die UWP-Plattform. Standardmäßig verwendet MSAL.NET jedoch den **Systemwebbrowser** für Xamarin.iOS- und Xamarin.Android-Anwendungen. Unter iOS wählt MSAL.NET sogar die Webansicht aus, die abhängig von der Betriebssystemversion (iOS12, iOS11 und früher) verwendet werden soll.

Die Nutzung des Systembrowsers hat den entscheidenden Vorteil, dass der SSO-Zustand mit anderen Anwendungen und mit Webanwendungen geteilt wird, ohne dass ein Broker (Unternehmensportal/Authentifikator) erforderlich ist. Der Systembrowser wurde standardmäßig in MSAL.NET für die Xamarin.iOS- und Xamarin.Android-Plattformen verwendet, weil der Systemwebbrowser auf diesen Plattformen den ganzen Bildschirm belegt und somit die Benutzererfahrung verbessert. Die Systemwebansicht ist von einem Dialogfeld nicht zu unterscheiden. Unter iOS muss der Benutzer jedoch ggf. seine Einwilligung geben, dass der Browser einen Rückruf an die Anwendung senden darf, was zeitraubend sein kann.

## <a name="system-browser-experience-on-net-core"></a>Systembrowser in .NET Core

In .NET Core startet MSAL.NET den Systembrowser als separaten Prozess. MSAL.NET kann diesen Browser nicht steuern, aber nachdem der Benutzer die Authentifizierung abgeschlossen hat, wird die Webseite so umgeleitet, dass MSAL.NET den URI abfangen kann.

Sie können auch für .NET Classic geschriebene Apps für die Verwendung dieses Browsers konfigurieren, indem Sie Folgendes angeben:

```csharp
await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
```

MSAL.NET kann nicht erkennen, ob der Benutzer die Webseite verlässt oder einfach den Browser schließt. Für Apps, die diese Technik verwenden, empfiehlt es sich, ein Timeout (über `CancellationToken`) zu definieren. Wir empfehlen ein Timeout von mindestens einigen Minuten, um Fälle zu berücksichtigen, in denen der Benutzer aufgefordert wird, das Kennwort zu ändern oder die mehrstufige Authentifizierung durchzuführen.

### <a name="how-to-use-the-default-os-browser"></a>So verwenden Sie den Standardbrowser des Betriebssystems

MSAL.NET muss `http://localhost:port` überwachen und den Code abfangen, den AAD sendet, wenn der Benutzer die Authentifizierung durchgeführt hat. (Ausführliche Informationen finden Sie unter [Autorisierungscode](v2-oauth2-auth-code-flow.md).)

Gehen Sie wie folgt vor, um den Systembrowser zu aktivieren:

1. Konfigurieren Sie `http://localhost` während der App-Registrierung als Umleitungs-URI (wird derzeit von B2C nicht unterstützt).
2. Geben Sie beim Erstellen Ihrer PublicClientApplication den folgenden Umleitungs-URI an:

```csharp
IPublicClientApplication pca = PublicClientApplicationBuilder
                            .Create("<CLIENT_ID>")
                             // or use a known port if you wish "http://localhost:1234"
                            .WithRedirectUri("http://localhost")  
                            .Build();
```

> [!Note]
> Wenn Sie `http://localhost` konfigurieren, sucht MSAL.NET intern nach einem zufälligen offenen Port und verwendet diesen.

### <a name="linux-and-mac"></a>Linux und MAC

Unter Linux öffnet MSAL.NET den Standardbrowser des Betriebssystems mit dem Tool „xdg-open“. Führen Sie das Tool zur Problembehandlung über ein Terminal aus, z. B. `xdg-open "https://www.bing.com"`. Auf dem Mac wird der Browser durch Aufrufen von `open <url>` geöffnet.

### <a name="customizing-the-experience"></a>Anpassen der Benutzeroberfläche

> [!NOTE]
> Eine Anpassung ist in MSAL.NET 4.1.0 oder höher möglich.

MSAL.NET kann mit einer HTTP-Nachricht antworten, wenn ein Token empfangen wird oder ein Fehler auftritt. Sie können eine HTML-Nachricht anzeigen oder zu einer URL Ihrer Wahl umleiten:

```csharp
var options = new SystemWebViewOptions() 
{
    HtmlMessageError = "<p> An error occured: {0}. Details {1}</p>",
    BrowserRedirectSuccess = new Uri("https://www.microsoft.com");
}

await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
         .WithSystemWebViewOptions(options)
         .ExecuteAsync();
```

### <a name="opening-a-specific-browser-experimental"></a>Öffnen eines bestimmten Browsers (experimentell)

Sie können das Öffnen des Browsers für MSAL.NET anpassen. Statt beispielsweise den jeweiligen Standardbrowser zu verwenden, können Sie das Öffnen eines bestimmten Browsers erzwingen:

```csharp
var options = new SystemWebViewOptions() 
{
    OpenBrowserAsync = SystemWebViewOptions.OpenWithEdgeBrowserAsync
}
```

### <a name="uwp-doesnt-use-the-system-webview"></a>UWP verwendet keine Systemwebansicht

Bei Desktopanwendungen führt der Start einer Systemwebansicht jedoch zu einer nicht optimalen Benutzererfahrung, da der Browser ggf. über anderen bereits geöffneten Registerkarten geöffnet wird. Nach der Authentifizierung wird eine weitere Seite angezeigt, in der der Benutzer aufgefordert wird, dieses Fenster zu schließen. Wenn der Benutzer nicht aufpasst, kann der gesamte Prozess geschlossen werden (einschließlich anderer Registerkarten, die nichts mit der Authentifizierung zu tun haben). Die Nutzung des Systembrowsers auf dem Desktop kann auch das Öffnen und Lauschen an lokalen Ports erfordern, wozu u. U. erweiterte Berechtigungen für die Anwendung benötigt werden. Bei Entwicklern, Benutzern oder Administratoren kann dies jedoch auf gewisse Bedenken stoßen.

## <a name="enable-embedded-webviews-on-ios-and-android"></a>Aktivieren der eingebetteten Webansichten unter iOS und Android

Sie haben auch die Möglichkeit, eingebettete Webansichten in Xamarin.iOS- und Xamarin.Android-Apps zu aktivieren. Ab der Vorschau von MSAL.NET 2.0.0 unterstützt MSAL.NET auch die Option für **eingebettete** Webansichten. Für ADAL.NET sind eingebettete Webansichten die einzige unterstützte Option.

Als Entwickler, der MSAL.NET gezielt für Xamarin verwendet, haben Sie die Wahl zwischen eingebetteten Webansichten oder Systembrowsern. Die Entscheidung hängt von der gewünschten Benutzererfahrung und etwaigen Sicherheitsbedenken ab.

Derzeit unterstützt MSAL.NET noch keine Android- und iOS-Broker. Deshalb kann der Systembrowser weiterhin die bessere Option sein, um einmaliges Anmelden (SSO) bereitzustellen. Die Unterstützung von Brokern in Verbindung mit dem eingebetteten Webbrowser befindet sich noch im MSAL.NET-Backlog.

### <a name="differences-between-embedded-webview-and-system-browser"></a>Unterschiede zwischen eingebetteter Webansicht und Systembrowser
Es gibt einige visuelle Unterschiede zwischen der eingebetteten Webansicht und dem Systembrowser in MSAL.NET.

**Interaktive Anmeldung mit MSAL.NET über die eingebettete Webansicht:**

![Eingebettet](media/msal-net-web-browsers/embedded-webview.png)

**Interaktive Anmeldung mit MSAL.NET über den Systembrowser:**

![Systembrowser](media/msal-net-web-browsers/system-browser.png)

### <a name="developer-options"></a>Entwickleroptionen

Wenn Sie als Entwickler MSAL.NET verwenden, haben Sie mehrere Optionen, um das interaktive Dialogfeld aus dem STS anzuzeigen:

- **Systembrowser.** Der Systembrowser wird standardmäßig in der Bibliothek festgelegt. Wenn Sie Android verwenden, finden Sie unter [Systembrowser](msal-net-system-browser-android-considerations.md) Informationen darüber, unter welchen Browsern die Authentifizierung unterstützt wird. Wenn Sie den Systembrowser in Android verwenden, wird auf dem Gerät die Nutzung eines Browsers empfohlen, der benutzerdefinierte Chrome-Registerkarten unterstützt.  Andernfalls kann ein Authentifizierungsfehler auftreten.
- **Eingebettete Webansicht.** Wenn Sie nur die eingebettete Webansicht in MSAL.NET verwenden möchten, bietet Ihnen der `AcquireTokenInteractively`-Parametergenerator eine `WithUseEmbeddedWebView()`-Methode.

    iOS

    ```csharp
    AuthenticationResult authResult;
    authResult = app.AcquireTokenInteractively(scopes)
                    .WithUseEmbeddedWebView(useEmbeddedWebview)
                    .ExecuteAsync();
    ```

    Android:

    ```csharp
    authResult = app.AcquireTokenInteractively(scopes)
                .WithParentActivityOrWindow(activity)
                .WithUseEmbeddedWebView(useEmbeddedWebview)
                .ExecuteAsync();
    ```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinios"></a>Auswählen zwischen eingebettetem Webbrowser oder Systembrowser in Xamarin.iOS

In Ihrer iOS-App können Sie in `AppDelegate.cs` das `ParentWindow` auf `null` initialisieren. Diese Option steht in iOS nicht zur Verfügung.

```csharp
App.ParentWindow = null; // no UI parent on iOS
```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid"></a>Auswählen zwischen eingebettetem Webbrowser oder Systembrowser in Xamarin.Android

In Ihrer Android-App können Sie in `MainActivity.cs` die übergeordnete Aktivität festlegen, sodass im Authentifizierungsergebnis darauf zurückverwiesen wird:

```csharp
 App.ParentWindow = this;
```

Der Code in `MainPage.xaml.cs`:

```csharp
authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(true)
                      .ExecuteAsync();
```

#### <a name="detecting-the-presence-of-custom-tabs-on-xamarinandroid"></a>Erkennen des Vorhandenseins benutzerdefinierter Registerkarten in Xamarin.Android

Angenommen, Sie möchten für Apps, die im Browser ausgeführt werden, SSO über den Systemwebbrowser aktivieren. Gleichzeitig haben Sie jedoch Bedenken bezüglich der Benutzererfahrung auf Android-Geräten, die über keinen Browser mit Unterstützung benutzerdefinierter Registerkarten verfügen. In diesem Fall können Sie durch Aufrufen der `IsSystemWebViewAvailable()`-Methode in `IPublicClientApplication` zwischen beiden Optionen wählen. Diese Methode gibt `true` zurück, wenn der PackageManager benutzerdefinierte Registerkarten erkennt, und `false`, wenn diese auf dem Gerät nicht erkannt werden.

Abhängig von dem durch diese Methode zurückgegebenen Wert und Ihren Anforderungen können Sie eine Entscheidung treffen:

- Sie können eine benutzerdefinierte Fehlermeldung an den Benutzer zurückgeben. Beispiel: "Installieren Sie Chrome, um die Authentifizierung fortzusetzen" – ODER –
- Sie können auf die Option für eingebettete Webansichten zurückgreifen und die Benutzeroberfläche als eingebettete Webansicht starten.

Im folgenden Code ist die Option für eingebettete Webansichten dargestellt:

```csharp
bool useSystemBrowser = app.IsSystemWebviewAvailable();

authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(!useSystemBrowser)
                      .ExecuteAsync();
```

#### <a name="net-core-doesnt-support-interactive-authentication-with-an-embedded-browser"></a>.NET Core unterstützt keine interaktive Authentifizierung mit einem eingebetteten Browser

Bei .NET Core ist der interaktive Tokenabruf nur über den Systemwebbrowser und nicht über eingebettete Webansichten verfügbar. Tatsächlich wird von .NET Core noch keine Benutzeroberfläche bereitgestellt.
Wenn Sie das Surfen mit dem Systemwebbrowser anpassen möchten, können Sie die [IWithCustomUI](scenario-desktop-acquire-token.md#withcustomwebui)-Schnittstelle implementieren und sogar ihren eigenen Browser bereitstellen.
