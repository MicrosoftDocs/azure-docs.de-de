---
title: Webbrowser in der Microsoft-Authentifizierungsbibliothek für .NET | Azure
description: Erfahren Sie mehr über die besonderen Überlegungen zur Verwendung von Xamarin Android mit der Microsoft-Authentifizierungsbibliothek für .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/06/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f7f91e6ab1fb12132068b839e66fafd3ab1bc73
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65543957"
---
# <a name="using-web-browsers-in-msalnet"></a>Verwenden von Webbrowsern in MSAL.NET
Webbrowser sind eine Voraussetzung für die interaktive Authentifizierung. Der [Systemwebbrowser](#system-web-browser-on-xamarinios-and-xamarinandroid) unter Xamarin.iOS und [Xamarin.Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/system-browser) wird standardmäßig von MSAL.NET unterstützt. Je nach Ihren Anforderungen (UX, Einmaliges Anmelden, Sicherheit) [können Sie jedoch auch den eingebetteten Webbrowser](#enable-embedded-webviews) in [Xamarin.iOS](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinios)-Apps und [Xamarin.Android](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid)-Apps aktivieren. Sie können den gewünschten Webbrowser auch [dynamisch auswählen](#detecting-the-presence-of-custom-tabs-on-xamarinandroid), und zwar danach, ob Chrome oder ein Browser, der benutzerdefinierte Chrome-Registerkarten in Android unterstützt, vorhanden ist.

## <a name="web-browsers-in-msalnet"></a>Webbrowser in MSAL.NET

Es ist wichtig zu wissen, dass der Inhalt des Dialogfelds beim interaktiven Tokenabruf nicht von der Bibliothek, sondern vom Sicherheitstokendienst (Security Token Service, STS) bereitgestellt wird. Der Authentifizierungsendpunkt sendet bestimmten HTML- und JavaScript-Code zurück, über den die Interaktion gesteuert wird. Dieser Code wird in einem Webbrowser oder Websteuerelement gerendert. Die Verarbeitung der HTML-Interaktion durch STS hat viele Vorteile:

- Das Kennwort (sofern angegeben) wird grundsätzlich weder von der Anwendung noch von der Authentifizierungsbibliothek gespeichert.
- Umleitungen zu anderen Identitätsanbietern werden unterstützt (z. B. Anmeldung mit einem Geschäfts-, Schul- oder Unikonto oder einem persönlichen Konto über MSAL bzw. Anmeldung mit einem Social Media-Konto über Azure AD B2C).
- Der STS kann den bedingten Zugriff steuern, indem während der Authentifizierungsphase z. B. die mehrstufige Authentifizierung vom Benutzer angefordert wird (Eingabe einer Windows Hello-PIN, Anruf per Telefon oder Verwendung einer Authentifizierungs-App auf dem Smartphone). Falls die erforderliche mehrstufige Authentifizierung noch nicht eingerichtet ist, kann sie vom Benutzer nach Bedarf im selben Dialogfeld eingerichtet werden.  Der Benutzer gibt seine Mobiltelefonnummer ein und wird durch die Schritte zur Installation einer Authentifizierungs-App und zum Scannen eines QR-Codes geführt, um sein Konto hinzuzufügen. Die servergesteuerte Interaktion ist ausgesprochen benutzerfreundlich!
- Bei Ablauf des Kennworts kann der Benutzer sein Kennwort im selben Dialogfeld ändern (zusätzliche Felder für die Eingabe des alten und neuen Kennworts werden angezeigt).
- Ermöglicht das Branding des Mandanten oder der Anwendung (Images) durch den Azure AD-Mandantenadministrator/-Anwendungsbesitzer.
- Der Benutzer kann seine Einwilligung geben, dass die Anwendung unmittelbar nach der Authentifizierung im Namen des Benutzers Zugriff auf Ressourcen/Geltungsbereiche erhält.

## <a name="system-web-browser-on-xamarinios-and-xamarinandroid"></a>Systemwebbrowser unter Xamarin.iOS und Xamarin.Android

Der Systemwebbrowser unter Xamarin.iOS und Xamarin.Android wird standardmäßig von MSAL.NET unterstützt. Für alle Plattformen, die eine Benutzeroberfläche (d. h. nicht .NET Core) bereitstellen, stellt die Bibliothek, in die ein Webbrowser-Steuerelement eingebettet ist, ein Dialogfeld bereit. MSAL.NET verwendet darüber hinaus eine eingebettete Webansicht für die .NET-Desktopplattform und einen WAB für die UWP-Plattform. Standardmäßig verwendet MSAL.NET jedoch den **Systemwebbrowser** für Xamarin.iOS- und Xamarin.Android-Anwendungen. Unter iOS wählt MSAL.NET sogar die Webansicht aus, die abhängig von der Betriebssystemversion (iOS12, iOS11 und früher) verwendet werden soll.

Die Nutzung des Systembrowsers hat den entscheidenden Vorteil, dass der SSO-Zustand mit anderen Anwendungen und mit Webanwendungen geteilt wird, ohne dass ein Broker (Unternehmensportal/Authentifikator) erforderlich ist. Der Systembrowser wurde standardmäßig in MSAL.NET für Xamarin.iOS- und Xamarin.Android-Plattformen verwendet, da der Systemwebbrowser unter diesen Plattformen den ganzen Bildschirm belegt und somit die Benutzererfahrung verbessert. Die Systemwebansicht ist von einem Dialogfeld nicht zu unterscheiden. Unter iOS muss der Benutzer jedoch ggf. seine Einwilligung geben, dass der Browser einen Rückruf an die Anwendung senden darf, was zeitraubend sein kann.

### <a name="uwp-does-not-use-the-system-webview"></a>UWP verwendet keine Systemwebansicht

Bei Desktopanwendungen führt der Start einer Systemwebansicht jedoch zu einer nicht optimalen Benutzererfahrung, da der Browser ggf. über anderen bereits geöffneten Registerkarten geöffnet wird. Nach der Authentifizierung wird eine weitere Seite angezeigt, in der der Benutzer aufgefordert wird, dieses Fenster zu schließen. Hier muss der Benutzer genau aufpassen, um nicht den ganzen Prozess zu schließen (darunter auch andere Registerkarten, die nichts mit der Authentifizierung zu tun haben). Die Nutzung des Systembrowsers auf dem Desktop kann auch das Öffnen und Lauschen an lokalen Ports erfordern, wozu u. U. erweiterte Berechtigungen für die Anwendung benötigt werden. Bei Entwicklern, Benutzern oder Administratoren kann dies jedoch auf gewisse Bedenken stoßen.

## <a name="enable-embedded-webviews"></a>Aktivieren eingebetteter Webansichten 
Sie haben auch die Möglichkeit, eingebettete Webansichten in Xamarin.iOS- und Xamarin.Android-Apps zu aktivieren. Ab der Vorschau von MSAL.NET 2.0.0 unterstützt MSAL.NET auch die Option für **eingebettete** Webansichten. Für ADAL.NET sind eingebettete Webansichten die einzige unterstützte Option.

Als Entwickler, der MSAL.NET gezielt für Xamarin verwendet, haben Sie die Wahl zwischen eingebetteten Webansichten oder Systembrowsern. Die Entscheidung hängt von der gewünschten Benutzererfahrung und etwaigen Sicherheitsbedenken ab.

Derzeit unterstützt MSAL.NET noch keine Android- und iOS-Broker. Deshalb kann der Systembrowser weiterhin die bessere Option sein, wenn Sie Einmaliges Anmelden (SSO) bereitstellen müssen. Die Unterstützung von Brokern in Verbindung mit dem eingebetteten Webbrowser befindet sich noch im MSAL.NET-Backlog.

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

In Ihrer Android-App in `MainActivity.cs` können Sie die übergeordnete Aktivität festlegen, damit in den Authentifizierungsergebnissen darauf zurückverwiesen wird:

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

- Sie können eine benutzerdefinierte Fehlermeldung an den Benutzer zurückgeben. Beispiel:  "Installieren Sie Chrome, um die Authentifizierung fortzusetzen" – ODER –
- Sie können auf die Option für eingebettete Webansichten zurückgreifen und die Benutzeroberfläche als eingebettete Webansicht starten.

Im folgenden Code ist die Option für eingebettete Webansichten dargestellt:

```csharp
bool useSystemBrowser = app.IsSystemWebviewAvailable();

authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(!useSystemBrowser)
                      .ExecuteAsync();
```

## <a name="net-core-does-not-support-interactive-authentication-out-of-the-box"></a>.NET Core unterstützt keine vorkonfigurierte, interaktive Authentifizierung

Für .NET Core steht der interaktive Tokenabruf nicht zur Verfügung. Tatsächlich wird von .NET Core noch keine Benutzeroberfläche bereitgestellt. Wenn Sie die interaktive Anmeldung für eine .NET Core-Anwendung bereitstellen möchten, können Sie dem Benutzer über die Anwendung einen Code und eine URL für die interaktive Anmeldung anzeigen lassen (siehe [Gerätecodeflow](msal-authentication-flows.md#device-code)).

Alternativ können Sie die [IWithCustomUI](scenario-desktop-acquire-token.md#withcustomwebui)-Schnittstelle implementieren und einen eigenen Browser bereitstellen.