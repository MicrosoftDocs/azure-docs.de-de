---
title: Überlegungen zu Xamarin.Android (Microsoft-Authentifizierungsbibliothek für .NET) | Azure
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
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c57feb33967732481d78e0ddaba5e90f4f82f327
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65544441"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>Besondere Überlegungen zu Xamarin.Android mit MSAL.NET
In diesem Artikel erfahren Sie mehr über die besonderen Überlegungen zur Verwendung des Systembrowsers von Xamarin.Android mit der Microsoft-Authentifizierungsbibliothek für .NET (MSAL.NET).

Ab MSAL.NET 2.4.0-preview unterstützt MSAL.NET andere Browser als Google Chrome. Für die Authentifizierung muss Google Chrome auch nicht mehr auf dem Android-Gerät installiert sein.

Es wird empfohlen, Browser zu verwenden, die benutzerdefinierte Registerkarten unterstützen. Dazu gehören unter anderem Folgende:

| Browser, die benutzerdefinierte Registerkarten unterstützen | Paketname |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | com.microsoft.emmx|
|Firefox | org.mozilla.firefox|
|Ecosia | com.ecosia.android|
|Kiwi | com.kiwibrowser.browser|
|Brave | com.brave.browser|

Neben den Browsern, die benutzerdefinierte Registerkarten unterstützen, wurden auch einige weitere Browser ohne Unterstützung benutzerdefinierter Registerkarten getestet, die ebenfalls für die Authentifizierung funktionieren: Opera, Opera Mini, InBrowser und Maxthon. Weitere Informationen entnehmen Sie der [Tabelle der Testergebnisse](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Android-system-browser#devices-and-browsers-tested).

## <a name="known-issues"></a>Bekannte Probleme

- Wenn der Benutzer keinen Browser auf dem Gerät aktiviert hat, löst MSAL.NET eine `AndroidActivityNotFound`-Ausnahme aus. 
  - **Lösung**: Informieren Sie den Benutzer darüber, dass ein Browser auf dem Gerät aktiviert sein sollte (vorzugsweise mit Unterstützung für benutzerdefinierte Registerkarten).

- Wenn die Authentifizierung fehlschlägt (z. B. wenn die Authentifizierung mit DuckDuckGo gestartet wird), gibt MSAL.NET `AuthenticationCanceled MsalClientException` zurück. 
  - **Ursache:** Auf dem Gerät wurde kein Browser aktiviert, der benutzerdefinierte Registerkarten unterstützt. Die Authentifizierung wurde mit einem alternativen Browser gestartet, der die Authentifizierung nicht durchführen konnte. 
  - **Lösung**: Informieren Sie den Benutzer darüber, dass ein Browser auf dem Gerät installiert sein sollte (vorzugsweise mit Unterstützung für benutzerdefinierte Registerkarten).

## <a name="devices-and-browsers-tested"></a>Getestete Geräte und Browser
In der folgenden Tabelle werden die Geräte und Browser aufgeführt, die getestet wurden.

| | Browser&ast;     |  Ergebnis  | 
| ------------- |:-------------:|:-----:|
| Huawei/One+ | Chrome&ast; | Pass|
| Huawei/One+ | Edge&ast; | Pass|
| Huawei/One+ | Firefox&ast; | Pass|
| Huawei/One+ | Brave&ast; | Pass|
| One+ | Ecosia&ast; | Pass|
| One+ | Kiwi&ast; | Pass|
| Huawei/One+ | Opera | Pass|
| Huawei | OperaMini | Pass|
| Huawei/One+ | InBrowser | Pass|
| One+ | Maxthon | Pass|
| Huawei/One+ | DuckDuckGo | Benutzerauthentifizierung abgebrochen|
| Huawei/One+ | UC Browser | Benutzerauthentifizierung abgebrochen|
| One+ | Dolphin | Benutzerauthentifizierung abgebrochen|
| One+ | CM Browser | Benutzerauthentifizierung abgebrochen|
| Huawei/One+ | Keiner installiert | AndroidActivityNotFound-Ausnahme|

&ast; Unterstützt benutzerdefinierte Registerkarten

## <a name="next-steps"></a>Nächste Schritte
Codeausschnitte und zusätzliche Informationen zur Verwendung des Systembrowsers mit Xamarin.Android finden Sie in dieser [Führungslinie](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid).  