---
title: Xamarin Android-Codekonfiguration und Problembehandlung (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Erfahren Sie mehr über Überlegungen zur Verwendung von Xamarin Android mit der Microsoft-Authentifizierungsbibliothek für .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 320d48535c4792a4d610888c6a7030568ccf16bc
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995181"
---
# <a name="configuration-requirements-and-troubleshooting-tips-for-xamarin-android-with-msalnet"></a>Konfigurationsanforderungen und Tipps zur Problembehandlung für Xamarin Android mit MSAL.NET

Es gibt mehrere Konfigurationsänderungen, die Sie in Ihrem Code vornehmen müssen, wenn Sie Xamarin Android mit der Microsoft-Authentifizierungsbibliothek für .NET (MSAL.NET) verwenden. In den folgenden Abschnitten werden die erforderlichen Änderungen beschrieben, gefolgt vom Abschnitt [Problembehandlung](#troubleshooting), der Ihnen helfen soll, einige der häufigsten Probleme zu vermeiden.

## <a name="set-the-parent-activity"></a>Festlegen der übergeordneten Aktivität

Legen Sie in Xamarin Android die übergeordnete Aktivität fest, damit das Token nach der Interaktion Folgendes zurückgibt:

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

In MSAL.NET 4.2 und höher können Sie diese Funktionalität auch auf der Ebene [PublicClientApplication][PublicClientApplication] festlegen. Verwenden Sie zu diesem Zweck einen Rückruf:

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

Wenn Sie [CurrentActivityPlugin](https://github.com/jamesmontemagno/CurrentActivityPlugin) verwenden, sollte Ihr Builder-Code [`PublicClientApplication`][PublicClientApplication]diesem Codeausschnitt ähneln:

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

## <a name="ensure-that-control-returns-to-msal"></a>Sicherstellen, dass die Steuerung wieder an MSAL übergeben wird

Wenn der interaktive Teil des Authentifizierungsablaufs endet, wird die Steuerung an MSAL zurückgegeben, indem die folgende Methode außer Kraft gesetzt wird: [`Activity`][Activity].[`OnActivityResult()`][OnActivityResult] -Methode.

Rufen Sie in Ihrer Außerkraftsetzung die MSAL.NET-Methode `AuthenticationContinuationHelper`.`SetAuthenticationContinuationEventArgs()` auf, damit die Steuerung am Ende des interaktiven Teils des Authentifizierungsablaufs zurückgegeben an MSAL wird.

```csharp
protected override void OnActivityResult(int requestCode,
                                         Result resultCode,
                                         Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);

    // Return control to MSAL
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode,
                                                                            resultCode,
                                                                            data);
}
```

## <a name="update-the-android-manifest"></a>Aktualisieren des Android-Manifests

Die *AndroidManifest.xml*-Datei sollte die folgenden Werte enthalten:

```XML
  <!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
  <activity
        android:name="microsoft.identity.client.BrowserTabActivity">
     <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="msauth"
                android:host="Enter_the_Package_Name"
                android:path="/Enter_the_Signature_Hash" />
     </intent-filter>
  </activity>
```

Ersetzen Sie den Wert `android:host=` durch den Paketnamen, den Sie im Azure-Portal registriert haben. Ersetzen Sie den Wert `android:path=` durch den Schlüsselhash, den Sie im Azure-Portal registriert haben. Der Signaturhash darf *nicht* URL-codiert sein. Der Signaturhash muss mit einem führenden Schrägstrich (`/`) beginnen.

Erstellen Sie alternativ [die Aktivität in Code](/xamarin/android/platform/android-manifest#the-basics), statt *AndroidManifest.xml* manuell zu bearbeiten. Um die Aktivität im Code zu erstellen, erstellen Sie zuerst eine Klasse, die das `Activity`-Attribut und das `IntentFilter`-Attribut enthält.

Hier sehen Sie ein Beispiel für eine Klasse, die die Werte der XML-Datei darstellt:

```csharp
  [Activity]
  [IntentFilter(new[] { Intent.ActionView },
        Categories = new[] { Intent.CategoryBrowsable, Intent.CategoryDefault },
        DataHost = "auth",
        DataScheme = "msal{client_id}")]
  public class MsalActivity : BrowserTabActivity
  {
  }
```

### <a name="xamarinforms-43x-manifest"></a>Xamarin.Forms 4.3.x-Manifest

Xamarin.Forms 4.3.x generiert Code, der das `package`-Attribut in *AndroidManifest.xml* auf `com.companyname.{appName}` festlegt. Wenn Sie `DataScheme` als `msal{client_id}` verwenden, kann es sinnvoll sein, den Wert zu ändern, damit er mit dem Namen des `MainActivity.cs`-Namespace übereinstimmt.

## <a name="use-the-embedded-web-view-optional"></a>Verwenden der eingebetteten Webansicht (optional)

Standardmäßig verwendet MSAL.NET den Systemwebbrowser. Dieser Browser ermöglicht Ihnen, bei Verwendung von Webanwendungen und anderen Apps Einmaliges Anmelden (SSO) zu nutzen. In seltenen Fällen kann es sinnvoll sein, dass Ihr System eine eingebettete Webansicht verwendet.

Dieses Codebeispiel zeigt das Einrichten einer eingebetteten Webansicht:

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

Weitere Informationen finden Sie unter [Verwenden von Webbrowsern für MSAL.NET](msal-net-web-browsers.md) und [Überlegungen zu Systembrowsern für Xamarin Android](msal-net-system-browser-android-considerations.md).

## <a name="troubleshooting"></a>Problembehandlung

### <a name="general-tips"></a>Allgemeine Tipps

- Aktualisieren Sie das vorhandene MSAL.NET-NuGet-Paket auf die [neueste Version von MSAL.NET](https://www.nuget.org/packages/Microsoft.Identity.Client/).
- Überprüfen Sie, ob Xamarin.Forms auf der neuesten Version basiert.
- Überprüfen Sie, ob Xamarin.Android.Support.v4 auf der neuesten Version basiert.
- Stellen Sie sicher, dass für alle Xamarin.Android.Support-Pakete die neueste Version festgelegt wird.
- Bereinigen Sie die Anwendung, oder erstellen Sie sie neu.
- Versuchen Sie in Visual Studio, die maximale Anzahl von parallelen Projektbuilds auf **1** festzulegen. Wählen Sie zu diesem Zweck **Optionen** > **Projekte und Projektmappen** > **Erstellen und Ausführen** > **Maximale Anzahl paralleler Projektbuilds** aus.
- Wenn Sie Builds an der Befehlszeile erstellen und in Ihrem Befehl `/m` verwenden, versuchen Sie, dieses Element aus dem Befehl zu entfernen.

### <a name="error-the-name-authenticationcontinuationhelper-doesnt-exist-in-the-current-context"></a>Error: Der Name „AuthenticationContinuationHelper“ ist im aktuellen Kontext nicht vorhanden.

Wenn ein Fehler darauf hinweist, dass `AuthenticationContinuationHelper` im aktuellen Kontext nicht vorhanden ist, hat Visual Studio die Datei *Android.csproj\** möglicherweise falsch aktualisiert. Manchmal enthält der Dateipfad im Element `<HintPath>` fälschlicherweise `netstandard13` statt `monoandroid90`.

Dieses Beispiel enthält einen ordnungsgemäßen Dateipfad:

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in dem Beispiel einer [mobilen Xamarin-Anwendung, die Microsoft Identity Platform verwendet](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations). In der folgenden Tabelle sind die relevanten Informationen aus der Infodatei zusammengefasst.

| Beispiel | Plattform | BESCHREIBUNG |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin.iOS, Android, UWP | Eine einfache Xamarin Forms-App, die die Verwendung von MSAL zeigt, um persönliche Microsoft-Konten und Azure AD über den Azure AD 2.0-Endpunkt zu authentifizieren. Die App zeigt außerdem den Zugriff auf Microsoft Graph und das resultierende Token. <br>![Diagramm des Authentifizierungsablaufs](media/msal-net-xamarin-android-considerations/topology.png) |

<!-- REF LINKS -->
[PublicClientApplication]: /dotnet/api/microsoft.identity.client.publicclientapplication
[OnActivityResult]: /dotnet/api/android.app.activity.onactivityresult
[Activity]: /dotnet/api/android.app.activity
