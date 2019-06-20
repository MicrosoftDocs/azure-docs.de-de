---
title: Übersicht über das Azure AD .NET-Protokoll | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie HTTP-Nachrichten zum Autorisieren des Zugriffs auf Webanwendungen und Web-APIs in Ihrem Mandanten mit Azure AD verwenden.
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/22/2019
ms.author: priyamo
ms.openlocfilehash: b6dd4cd55755ae2c92afd327ad72ffe6966b9a07
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178264"
---
## <a name="register-your-application-with-your-ad-tenant"></a>Registrieren der Anwendung beim AD-Mandanten
Zuerst müssen Sie Ihre Anwendung beim Azure Active Directory-Mandanten (Azure AD) registrieren. Hierbei erhalten Sie eine Anwendungs-ID für die Anwendung, und die Aktivierung für den Empfang von Token wird durchgeführt.

* Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
* Wählen Sie Ihren Azure AD-Mandanten aus, indem Sie auf der Seite oben rechts auf Ihr Konto klicken. Klicken Sie anschließend auf das Navigationselement **Verzeichnis wechseln**, und wählen Sie dann den entsprechenden Mandanten aus. 
  * Überspringen Sie diesen Schritt, wenn Ihr Konto nur einen Azure AD-Mandanten enthält oder wenn Sie bereits den entsprechenden Azure AD-Mandanten ausgewählt haben.
* Klicken Sie im linken Navigationsbereich auf **Azure Active Directory**.
* Klicken Sie auf **App-Registrierungen** und dann auf **Neue Registrierung**.
* Folgen Sie der Anleitung, und erstellen Sie eine neue Anwendung. Es spielt bei diesem Tutorial keine Rolle, ob es sich um eine Webanwendung oder eine öffentliche Clientanwendung (Mobilgerät und Desktop) handelt. Falls Sie jedoch spezielle Beispiele für Webanwendungen bzw. öffentliche Clientanwendungen wünschen, können Sie sich unsere [Schnellstarts](../articles/active-directory/develop/v1-overview.md) ansehen.
  * **Name** ist der Anwendungsname und beschreibt Ihre Anwendung für Endbenutzer.
  * Wählen Sie unter **Unterstützte Kontotypen** **Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten** aus.
  * Stellen Sie den **Umleitungs-URI** bereit. Bei Webanwendungen ist dies die Basis-URL Ihrer App, unter der sich Benutzer anmelden können.  Beispiel: `http://localhost:12345`. Bei öffentlichen Clients (Mobilgerät und Desktop) verwendet Azure AD den URI zum Zurückgeben von Tokenantworten. Geben Sie einen für Ihre Anwendung spezifischen Wert ein.  Beispiel: `http://MyFirstAADApp`.
    <!--TODO: add once App ID URI is configurable: The **App ID URI** is a unique identifier for your application. The convention is to use `https://<tenant-domain>/<app-name>`, e.g. `https://contoso.onmicrosoft.com/my-first-aad-app`-->  
* Nach Abschluss der Registrierung weist Azure AD Ihrer Anwendung eine eindeutige Client-ID (die **Anwendungs-ID**) zu. Diesen Wert benötigen Sie in den nächsten Abschnitten. Daher sollten Sie ihn von der Anwendungsseite kopieren.
* Um die Anwendung im Azure-Portal zu suchen, klicken Sie auf **App Registrierungen** und dann auf **Alle Anwendungen anzeigen**.
