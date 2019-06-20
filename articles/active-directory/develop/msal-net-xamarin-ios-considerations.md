---
title: Überlegungen zu Xamarin iOS (Microsoft-Authentifizierungsbibliothek für .NET) | Azure
description: Erfahren Sie mehr über spezielle Überlegungen zur Verwendung von Xamarin iOS mit der Microsoft-Authentifizierungsbibliothek für .NET (MSAL.NET).
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
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf236bff2300129ec97d3b8946c4c2a2748bca77
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65602130"
---
# <a name="xamarin-ios-specific-considerations-with-msalnet"></a>Spezielle Überlegungen für Xamarin iOS mit MSAL.NET
Unter Xamarin iOS gibt es verschiedene Überlegungen, die Sie bei Verwendung von MSAL.NET berücksichtigen müssen.

- [Bekannte Probleme mit iOS 12 und der Authentifizierung](#known-issues-with-ios-12-and-authentication)
- [Überschreiben und Implementieren der `OpenUrl`-Funktion im `AppDelegate`](#implement-openurl)
- [Aktivieren von Keychaingruppen](#enable-keychain-groups)
- [Aktivieren von Tokencachefreigaben](#enable-token-cache-sharing-across-ios-applications)
- [Aktivieren des Keychainzugriffs](#enable-keychain-access)

## <a name="known-issues-with-ios-12-and-authentication"></a>Bekannte Probleme mit iOS 12 und der Authentifizierung
Microsoft hat eine [Sicherheitsempfehlung](https://github.com/aspnet/AspNetCore/issues/4647) mit Informationen zur Inkompatibilität zwischen iOS 12 und einigen Arten der Authentifizierung herausgegeben. Diese Inkompatibilität führt zu Problemen mit der Anmeldung über soziale Medien sowie mit WSFed- und OIDC-Anmeldungen. Die Empfehlung enthält auch Informationen dazu, was Entwickler tun können, um derzeitige Sicherheitseinschränkungen zu entfernen, die von ASP.NET hinzugefügt werden, sodass ihre Anwendungen mit iOS 12 kompatibel werden.  

Beim Entwickeln von MSAL.NET-Anwendungen für Xamarin iOS stellen Sie möglicherweise eine Endlosschleife fest, wenn Sie versuchen, sich über iOS 12 bei Websites anzumelden (ähnlich diesem [ADAL-Problem](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329)). 

Sie stellen möglicherweise auch ein Problem mit der ASP.NET Core-OICD-Authentifizierung bei iOS 12 Safari fest, wie in diesem [WebKit-Problem](https://bugs.webkit.org/show_bug.cgi?id=188165) beschrieben.

## <a name="implement-openurl"></a>Implementieren von OpenUrl

Zuerst müssen Sie die `OpenUrl` -Methode der `FormsApplicationDelegate`-Klasse überschreiben und `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs` aufrufen.

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

Sie müssen auch ein URL-Schema definieren, Berechtigungen anfordern, mit denen Ihre App eine andere App aufrufen kann, die Umleitungs-URL in einer bestimmten Form festlegen und diese Umleitungs-URL im [Azure-Portal](https://portal.azure.com) registrieren.

## <a name="enable-keychain-groups"></a>Aktivieren von Keychaingruppen

Damit der Tokencache und die `AcquireTokenSilentAsync`-Methode funktionieren, müssen mehrere Schritte ausgeführt werden:
1. Aktivieren Sie den Keychainzugriff in Ihrer *`* Entitlements.plist*-Datei, und geben Sie die **Keychaingruppen** in Ihrem Paketbezeichner an.
2. Wählen Sie die *`*Entitlements.plist*`* -Datei im Feld **Benutzerdefinierte Berechtigungen** in der Ansicht **Bundlesignierung** des Fensters mit iOS-Projektoptionen aus.
3. Stellen Sie beim Signieren von Zertifikaten sicher, dass Xcode die gleiche Apple-ID verwendet.

## <a name="enable-token-cache-sharing-across-ios-applications"></a>Aktivieren der Tokencachefreigabe über iOS-Anwendungen hinweg

Ab MSAL 2.x können Sie eine Keychainsicherheitsgruppe angeben, die zum Beibehalten des Tokencaches über mehrere Anwendungen hinweg verwendet werden soll. Auf diese Weise können Sie den Tokencache für verschiedene Anwendungen freigeben, die über die gleiche Keychainsicherheitsgruppe verfügen. Dazu gehören mit [ADAL.NET](https://aka.ms/adal-net) entwickelte Anwendungen, MSAL.NET Xamarin.iOS-Anwendungen und native iOS-Anwendungen, die mit [ADAL.objc](https://github.com/AzureAD/azure-activedirectory-library-for-objc) oder [MSAL.objc](https://github.com/AzureAD/microsoft-authentication-library-for-objc) entwickelt wurden.

Die Freigabe des Tokencaches ermöglicht das einmalige Anmelden (Single Sign-On, SSO) bei allen Anwendungen, die die gleiche Keychainsicherheitsgruppe verwenden.

Um das einmalige Anmelden zu aktivieren, müssen Sie die `PublicClientApplication.iOSKeychainSecurityGroup`-Eigenschaft in allen Anwendungen auf den gleichen Wert festlegen.

Hier ein Beispiel mit MSAL v3.x:
```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.msalrocks")
     .Build();
```

Hier ein Beispiel mit MSAL v2.7.x:

```csharp
PublicClientApplication.iOSKeychainSecurityGroup = "com.microsoft.msalrocks";
```

> [!NOTE]
> Die `KeychainSecurityGroup`-Eigenschaft ist veraltet. In MSAL 2.x waren Entwickler gezwungen, beim Verwenden der `KeychainSecurityGroup`-Eigenschaft das TeamId-Präfix einzuschließen. 
> 
> Ab MSAL 2.7.x löst die MSAL das TeamId-Präfix zur Laufzeit auf, wenn die `iOSKeychainSecurityGroup`-Eigenschaft verwendet wird. Wenn diese Eigenschaft verwendet wird, darf der Wert das TeamId-Präfix nicht enthalten. 
> 
> Verwenden Sie die neue `iOSKeychainSecurityGroup`-Eigenschaft, die keine Angabe der TeamId durch die Entwickler erfordert. Die `KeychainSecurityGroup`-Eigenschaft ist jetzt veraltet. 

## <a name="enable-keychain-access"></a>Aktivieren des Keychainzugriffs

In MSAL 2.x und ADAL 4.x wird die TeamId für den Zugriff auf die Keychain verwendet. Dadurch können die Authentifizierungsbibliotheken SSO-Zugriff auf Anwendungen desselben Herausgebers bereitstellen. 

Was ist das [TeamIdentifierPrefix](/xamarin/ios/deploy-test/provisioning/entitlements?tabs=vsmac) (TeamId)? Es handelt sich um einen eindeutigen Bezeichner (unternehmens- oder personenbezogen) im App Store. Die AppId ist eindeutig für eine App. Wenn Sie über mehrere Apps verfügen, ist die TeamId für alle Apps gleich, die AppId ist jedoch unterschiedlich. Der Keychainzugriffsgruppe wird vom System automatisch eine TeamId für jede Gruppe vorangestellt. Auf diese Weise erzwingt das Betriebssystem, dass Apps desselben Herausgebers auf die freigegebene Keychain zugreifen können. 

Wenn Sie beim Initialisieren der `PublicClientApplication` eine `MsalClientException` mit der Meldung `TeamId returned null from the iOS keychain...` erhalten, müssen Sie in der iOS Xamarin-App folgendermaßen vorgehen:

1. Wechseln Sie in VS auf der Registerkarte „Debuggen“ zu „nameOfMyApp.iOS“ -> „Eigenschaften“.
2. Wechseln Sie dann zu „iOS-Bundlesignierung“. 
3. Klicken Sie unter „Benutzerdefinierte Berechtigungen“ auf die drei Auslassungspunkte (...), und wählen Sie die Datei „Entitlements.plist“ Ihrer App aus.
4. In der csproj-Datei der iOS-App sollte jetzt diese Zeile enthalten sein: `<CodesignEntitlements>Entitlements.plist</CodesignEntitlements>`.
5. **Erstellen** Sie das Projekt neu.

Dieser Vorgang erfolgt *zusätzlich* zum Aktivieren des Keychainzugriffs in der `Entitlements.plist`-Datei. Verwenden Sie dabei entweder die unten stehende Zugriffsgruppe oder Ihre eigene:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
</plist>
```

## <a name="next-steps"></a>Nächste Schritte

Detailliertere Informationen finden Sie im Abschnitt [iOS Specific Considerations](https://github.com/azure-samples/active-directory-xamarin-native-v2#ios-specific-considerations) der readme.md-Datei des folgenden Beispiels:

Beispiel | Plattform | BESCHREIBUNG 
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Eine einfache Xamarin Forms-App, die die Verwendung der MSAL zum Authentifizieren von MSA und Azure AD über den AAD V2.0-Endpunkt und den Zugriff auf Microsoft Graph mit dem resultierenden Token veranschaulicht. <br>![Topologie](media/msal-net-xamarin-ios-considerations/topology.png)