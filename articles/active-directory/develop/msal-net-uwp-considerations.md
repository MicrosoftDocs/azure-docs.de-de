---
title: Überlegungen zur Universellen Windows-Plattform (Microsoft-Authentifizierungsbibliothek für .NET) | Azure
description: Erfahren Sie mehr über bestimmte Überlegungen zur Verwendung der Universellen Windows-Plattform mit der Microsoft-Authentifizierungsbibliothek für .NET (MSAL.NET).
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
ms.openlocfilehash: 83fb999b0cf66cfd8d96e82d23ed43626352a8aa
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544137"
---
# <a name="universal-windows-platform-specific-considerations-with-msalnet"></a>Spezifische Überlegungen zur Universellen Windows-Plattform mit MSAL.NET
Unter Xamarin iOS gibt es verschiedene Überlegungen, die Sie bei Verwendung von MSAL.NET berücksichtigen sollten.

## <a name="the-usecorporatenetwork-property"></a>UseCorporateNetwork-Eigenschaft
In der WinRT-Plattform hat `PublicClientApplication` die folgende boolesche Eigenschaft ``UseCorporateNetwork``. Durch diese Eigenschaft profitieren Win8.1- und UWP-Anwendungen von der integrierten Windows-Authentifizierung (und daher vom einmaligen Anmelden des im Betriebssystem angemeldeten Benutzers), falls dieser mit einem Konto in einem verbundenen Azure AD-Mandanten angemeldet ist. Diese nutzt Webauthentifizierungsbroker (Web Authentication Broker, WAB). 

> [!IMPORTANT]
> Wenn diese Eigenschaft auf TRUE festgelegt ist, wird davon ausgegangen, dass der Anwendungsentwickler die integrierte Windows-Authentifizierung (IWA) in der Anwendung aktiviert hat. Hier:
> - Aktivieren Sie in ``Package.appxmanifest`` für Ihre UWP-Anwendung auf der Registerkarte **Funktionen** die folgenden Funktionen:
>   - Unternehmensauthentifizierung
>   - Private Netzwerke (Client und Server)
>   - Freigegebenes Benutzerzertifikat

Die integrierte Windows-Authentifizierung ist standardmäßig nicht aktiviert, da für Anwendungen, welche die Funktion „Unternehmensauthentifizierung“ oder „Freigegebene Benutzerzertifikate“ anfordern, eine höhere Überprüfungsebene erforderlich ist, um im Windows Store akzeptiert zu werden und gegebenenfalls nicht alle Entwickler die Überprüfung auf der höheren Ebene durchführen möchten. 

Die zugrunde liegende Implementierung auf die Universelle Windows-Plattform (WAB) funktioniert nicht ordnungsgemäß in Enterprise-Szenarios, für die der bedingte Zugriff aktiviert wurde. Der Grund dafür ist, dass der Benutzer versucht, sich mit Windows Hello anzumelden, und ihm vorgeschlagen wird, ein Zertifikat auszuwählen, dieses Zertifikat für die PIN allerdings nicht gefunden wird, oder der Benutzer es auswählt, dann aber nicht zur Eingabe der PIN aufgefordert wird. Eine Möglichkeit zur Umgehung dieses Problems besteht darin, eine alternative Methode zu verwenden (Benutzername/Kennwort + Telefonauthentifizierung), jedoch hat sich diese Vorgehensweise nicht bewährt. 

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in den folgenden Beispielen:

Beispiel | Plattform | BESCHREIBUNG 
|------ | -------- | -----------|
|[active-directory-dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | Eine msal.net verwendende Clientanwendung der Universellen Windows-Plattform für den Zugriff auf Microsoft Graph für eine Benutzerauthentifizierung mit dem Azure AD v2.0-Endpunkt <br>![Topologie](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Eine einfache Xamarin Forms-App, mit der die Verwendung von MSAL zum Authentifizieren von MSA und Azure AD über den AAD v2.0-Endpunkt und der Zugriff auf Microsoft Graph mit dem sich daraus ergebenden Token veranschaulicht wird <br>![Topologie](media/msal-net-uwp-considerations/topology-xamarin-native.png)|