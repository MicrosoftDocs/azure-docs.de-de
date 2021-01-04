---
title: UWP-Überlegungen (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Erfahren Sie mehr über Überlegungen zur Verwendung der Universellen Windows-Plattform (UWP) mit der Microsoft-Authentifizierungsbibliothek für .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: c71c55adbf79af780169380269b5081322f94baf
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93083438"
---
# <a name="considerations-for-using-universal-windows-platform-with-msalnet"></a>Überlegungen zur Verwendung der Universellen Windows-Plattform mit MSAL.NET
Entwickler von Anwendungen, die die UWP (Universelle Windows-Plattform) mit MSAL.NET verwenden, sollten sich mit den Konzepten befassen, die in diesem Artikel vorgestellt werden.

## <a name="the-usecorporatenetwork-property"></a>UseCorporateNetwork-Eigenschaft
Auf der WinRT-Plattform (Windows Runtime) hat `PublicClientApplication` die boolesche Eigenschaft `UseCorporateNetwork`. Diese Eigenschaft ermöglicht es Windows 8.1-Anwendungen und UWP-Anwendungen, von der integrierten Windows-Authentifizierung (IWA) zu profitieren, wenn der Benutzer bei einem Konto angemeldet ist, das über einen Azure Active Directory-Verbundmandanten (Azure AD) verfügt. Benutzer, die beim Betriebssystem angemeldet sind, können auch Einmaliges Anmelden (SSO) verwenden. Wenn Sie die `UseCorporateNetwork`-Eigenschaft festlegen, verwendet MSAL.NET einen Webauthentifizierungsbroker (WAB).

> [!IMPORTANT]
> Wenn die `UseCorporateNetwork`-Eigenschaft auf „true“ festgelegt ist, wird davon ausgegangen, dass der Anwendungsentwickler IWA in der Anwendung aktiviert hat. So aktivieren Sie IWA:
> - Aktivieren Sie im `Package.appxmanifest` für Ihre UWP-Anwendung auf der Registerkarte **Funktionen** die folgenden Funktionen:
>   - **Unternehmensauthentifizierung**
>   - **Private Netzwerke (Client und Server)**
>   - **Freigegebenes Benutzerzertifikat**

IWA ist standardmäßig nicht aktiviert, da Microsoft Store ein hohes Maß an Überprüfung erfordert, bevor Anwendungen akzeptiert werden, die auf Funktionen der Unternehmensauthentifizierung oder freigegebene Benutzerzertifikate zugreifen. Nicht alle Entwickler möchten dieses Maß an Überprüfung durchführen.

Auf der UWP-Plattform funktioniert die zugrunde liegende WAB-Implementierung in Unternehmensszenarien, für die bedingter Zugriff aktiviert ist, nicht ordnungsgemäß. Benutzer können Symptome dieses Problems bemerken, wenn sie versuchen, sich mithilfe von Windows Hello anzumelden. Wenn der Benutzer aufgefordert wird, ein Zertifikat auszuwählen:

- Das Zertifikat für die PIN wurde nicht gefunden.
- Nachdem der Benutzer ein Zertifikat ausgewählt hat, wird er nicht zur Eingabe der PIN aufgefordert.

Sie können versuchen, dieses Problem zu vermeiden, indem Sie eine alternative Methode wie Benutzername/Kennwort- und Telefonauthentifizierung verwenden, aber das Benutzererlebnis ist nicht gut.

## <a name="troubleshooting"></a>Problembehandlung

Einige Kunden haben in bestimmten Unternehmensumgebungen, von denen sie wissen, dass eine Internetverbindung besteht und dass die Verbindung mit einem öffentlichen Netzwerk funktioniert, von folgendem Anmeldefehler berichtet.

```Text
We can't connect to the service you need right now. Check your network connection or try this again later.
```

Sie können dieses Problem vermeiden, indem Sie sicherstellen, dass WAB (die zugrunde liegende Windows-Komponente) ein privates Netzwerk zulässt. Dies können Sie erreichen, indem Sie einen Registrierungsschlüssel festlegen:

```Text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\authhost.exe\EnablePrivateNetwork = 00000001
```

Weitere Informationen finden Sie unter [Webauthentifizierungsbroker: Fiddler](/windows/uwp/security/web-authentication-broker#fiddler).

## <a name="next-steps"></a>Nächste Schritte
Die folgenden Beispiele enthalten weitere Informationen.

Beispiel | Plattform | BESCHREIBUNG 
|------ | -------- | -----------|
|[active-directory-dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | Eine UWP-Clientanwendung, die MSAL.NET verwendet. Sie greift für einen Benutzer, der sich über einen Azure AD 2.0-Endpunkt authentifiziert, auf Microsoft Graph zu. <br>![Topologie](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[active-directory-xamarin-native-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Eine Xamarin Forms-App, die die Verwendung von MSAL zeigt, um persönliche Microsoft-Konten und Azure AD über Microsoft Identity Platform zu authentifizieren. Ferner wird der Zugriff auf Microsoft Graph und das resultierende Token gezeigt. <br>![Diagramm, das die Verwendung von MSAL zeigt, um persönliche Microsoft-Konten und Azure AD über Microsoft Identity Platform zu authentifizieren.](media/msal-net-uwp-considerations/topology-xamarin-native.png)|
