---
title: Clientanwendungen (Microsoft Authentication Library) | Azure
description: Erfahren Sie mehr über öffentliche und vertrauliche Clientanwendungen in der Microsoft Authentication Library (MSAL).
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
ms.date: 04/25/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e0300ec0ef4ee67b06acb85514ae898bbd0a830
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544293"
---
# <a name="public-client-and-confidential-client-applications"></a>Öffentliche und vertrauliche Clientanwendungen
Die Microsoft Authentication Library (MSAL) definiert zwei Arten von Clients: öffentliche und vertrauliche Clients. Diese beiden Clienttypen unterscheiden sich in ihrer Fähigkeit, sich auf sichere Weise bei einem Autorisierungsserver zu authentifizieren und die Vertraulichkeit ihrer Clientanmeldeinformationen zu gewährleisten.  Die Azure AD-Authentifizierungsbibliothek (Azure AD Authentication Library, ADAL) dagegen funktioniert nach dem Konzept des Authentifizierungskontexts (hierbei handelt es sich um eine Verbindung mit Azure AD).

- **Vertrauliche Clientanwendungen** sind Anwendungen, die auf Servern ausgeführt werden (Web-Apps, Web-API-Anwendungen oder sogar Daemon-/Dienstanwendungen). Sie gelten als schwer zugänglich und geben keine Anwendungsgeheimnisse preis. Vertrauliche Clients können Geheimnisse speichern, die zur Konfigurationszeit eingerichtet wurden. Jede Instanz eines Clients verfügt über eine eigene Konfiguration (einschließlich Client-ID und geheimem Clientschlüssel). Endbenutzer können diese Werte nur schwer extrahieren. Eine Web-App ist die häufigste Form eines vertraulichen Clients. Die Client-ID wird über den Webbrowser verfügbar gemacht, aber der geheime Schlüssel wird nur im Rückkanal übergeben und niemals direkt verfügbar gemacht.

    Vertrauliche Client-Apps: <BR>
    ![Web-App](media/msal-client-applications/web-app.png) ![Web-API](media/msal-client-applications/web-api.png) ![Daemon/Dienst](media/msal-client-applications/daemon-service.png)

- **Öffentliche Clientanwendungen** sind Anwendungen, die auf Geräten, Desktopcomputern oder in einem Webbrowser ausgeführt werden. Sie sind in Bezug auf eine sichere Speicherung von Anwendungsgeheimnissen nicht vertrauenswürdig und können daher nur im Namen eines Benutzers auf Web-APIs zugreifen (sie unterstützen nur öffentliche Clientflows). Öffentliche Clients können keine Geheimnisse speichern, die zur Konfigurationszeit eingerichtet wurden, und verfügen daher nicht über einen geheimen Clientschlüssel.

    Öffentliche Clientanwendungen: <BR>
    ![Desktop-App](media/msal-client-applications/desktop-app.png) ![API ohne Browser](media/msal-client-applications/browserless-app.png) ![Mobile App](media/msal-client-applications/mobile-app.png)

> [!NOTE]
> In MSAL.js gibt es keine Trennung zwischen öffentlichen und vertraulichen Client-Apps.  MSAL.js stellt Client-Apps als Benutzer-Agent-basierte Apps da: öffentliche Clients, bei denen der Clientcode in einem Benutzer-Agent wie beispielsweise einem Webbrowser ausgeführt wird.  Diese Clients speichern keine Geheimnisse, da der Browserkontext offen zugänglich ist.

## <a name="comparing-the-client-types"></a>Vergleich der Clienttypen
Bei öffentlichen und vertraulichen Clientanwendungen gibt es sowohl Gemeinsamkeiten als auch Unterschiede:

- Beide Anwendungsarten verwalten einen Benutzertokencache und können ein Token im Hintergrund abrufen (in Fällen, in denen sich das Token bereits im Tokencache befindet). Vertrauliche Clientanwendungen verfügen auch über einen App-Tokencache für Token, die für die App selbst sind.
- Beide verwalten Benutzerkonten und können die Konten aus dem Benutzertokencache abrufen, ein Konto anhand seines Bezeichners abrufen oder ein Konto entfernen.
- Öffentliche Clientanwendungen verfügen über vier Möglichkeiten zum Abrufen von Token (vier Authentifizierungsflows), vertrauliche Clientanwendungen besitzen drei solcher Möglichkeiten (sowie eine Methode zum Berechnen der URL des Autorisierungsendpunkts eines Identitätsanbieters). Weitere Informationen finden Sie unter „Szenarien“ und „Abrufen von Token“.

Wenn Sie ADAL bereits verwendet haben, werden Sie feststellen, dass im Gegensatz zum ADAL-Authentifizierungskontext die Client-ID (auch als Anwendung- oder App-ID bezeichnet) in MSAL einmal bei der Erstellung der Anwendung übergeben wird und beim Abrufen eines Tokens nicht mehr wiederholt werden muss. Dies gilt sowohl für öffentliche als auch für vertrauliche Clientanwendungen. Geheime Clientschlüssel werden auch an Konstruktoren von öffentlichen Clientanwendungen übergeben: Dies sind die Geheimnisse, die sie für den Identitätsanbieter freigeben.

## <a name="next-steps"></a>Nächste Schritte
Sie erhalten Informationen zu folgenden Themen:
- [Optionen für die Clientanwendungskonfiguration](msal-client-application-configuration.md)
- [Instanziieren von Clientanwendungen mithilfe von MSAL.NET](msal-net-initializing-client-applications.md)
- [Instanziieren von Clientanwendungen mithilfe von MSAL.js](msal-js-initializing-client-applications.md)
