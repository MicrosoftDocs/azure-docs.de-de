---
title: Bekannte Browserprobleme (Microsoft Authentication Library für JavaScript) | Azure
description: Erfahren Sie mehr über bekannte Probleme, die bei Verwendung der Microsoft Authentication Library für JavaScript (MSAL.js) in Verbindung mit den Browsern Internet Explorer und Microsoft Edge auftreten können.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c57ed956ec50c8bac26720a27894c07353928336
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65874086"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-with-msaljs"></a>Bekannte Probleme in den Browsern Internet Explorer und Microsoft Edge bei Verwendung von MSAL.js

## <a name="issues-due-to-security-zones"></a>Probleme aufgrund von Sicherheitszonen
Es wurden mehrere Probleme bei der Authentifizierung in IE und Microsoft Edge gemeldet (nach dem Update von *Microsoft Edge auf die Browserversion 40.15063.0.0*). Wir verfolgen diese Fälle und haben das Microsoft Edge-Team informiert. Während das Microsoft Edge-Team an einer Lösung arbeitet, finden Sie hier eine Beschreibung der häufig auftretenden Probleme und mögliche Problemumgehungen.

### <a name="cause"></a>Ursache
Die meisten Probleme haben folgende Ursache. Der Sitzungsspeicher und lokale Speicher im Microsoft Edge-Browser sind durch Sicherheitszonen partitioniert. In dieser spezifischen Version von Microsoft Edge werden der Sitzungsspeicher und der lokale Speicher gelöscht, wenn die Anwendung zonenübergreifend umgeleitet wird. Insbesondere der Sitzungsspeicher wird bei der normalen Browsernavigation gelöscht, und sowohl der Sitzungs- als auch der lokale Speicher werden im InPrivate-Modus des Browsers gelöscht. MSAL.js speichert einen bestimmten Zustand im Sitzungsspeicher und nutzt diesen Zustand während der Authentifizierungsflows als Prüfkriterium. Wenn der Sitzungsspeicher gelöscht wird, gehen die Zustandsinformationen verloren, was zu einem Authentifizierungsfehler führt.

### <a name="issues"></a>Probleme

- **Authentifizierung: Endlosschleifen bei der Umleitung und neu geladene Seiten**: Wenn Benutzer sich in Microsoft Edge bei der Anwendung anmelden, werden sie von der AAD-Anmeldeseite zurückgeleitet. Dabei entsteht eine endlose Umleitungsschleife, die dazu führt, dass die Seite ständig neu geladen wird. Dieses Problem tritt in der Regel zusammen mit einem `invalid_state`-Fehler im Sitzungsspeicher auf.

- **Endlosschleifen beim Tokenabruf und AADSTS50058-Fehler**: Wenn eine in Microsoft Edge ausgeführte Anwendung versucht, ein Token für eine Ressource abzurufen, kann die Anwendung in einer Endlosschleife hängen bleiben, die durch den Tokenabruf verursacht wird. Zusätzlich registriert die Netzwerküberwachung folgenden AAD-Fehler:

    `Error :login_required; Error description:AADSTS50058: A silent sign-in request was sent but no user is signed in. The cookies used to represent the user's session were not sent in the request to Azure AD. This can happen if the user is using Internet Explorer or Edge, and the web app sending the silent sign-in request is in different IE security zone than the Azure AD endpoint (login.microsoftonline.com)`

- **Ein bei der Anmeldung für die Authentifizierung verwendetes Popupfenster wird nicht geschlossen oder bleibt hängen**: Wenn sich ein Benutzer in Microsoft Edge oder IE (InPrivate) über ein Popupfenster authentifiziert, wird das Popupfenster nach der Eingabe der Anmeldeinformationen und der Anmeldung nicht geschlossen, wenn sich die Navigation über mehrere Domänen in unterschiedlichen Sicherheitszonen erstreckt. Dies liegt daran, dass MSAL.js das Handle für das Popupfenster verliert.  

    Im Folgenden die Links zu den Fehlern in der Problemverfolgung zu Microsoft Edge:  
    - [Fehler 13861050](https://developer.microsoft.com/en-us/microsoft-edge/platform/issues/13861050/)
    - [Fehler 13861663](https://developer.microsoft.com/en-us/microsoft-edge/platform/issues/13861663/)

### <a name="update-fix-available-in-msaljs-023"></a>Update: Fix in MSAL.js 0.2.3 verfügbar
In [MSAL.js 0.2.3](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases) wurden Fixes für die Umleitungsschleife bei der Authentifizierung veröffentlicht. Aktivieren Sie das Flag `storeAuthStateInCookie` in der Konfiguration von MSAL.js, um diesen Fix zu nutzen. Dieses Flag ist standardmäßig auf „false“ festgelegt.

Wenn das Flag `storeAuthStateInCookie` aktiviert ist, nutzt MSAL.js die Browsercookies, um den Anforderungsstatus zu speichern, der für die Überprüfung der Authentifizierungsflows erforderlich ist.

> [!NOTE]
> Für den msal-angular- und msal-angularjs-Wrapper ist dieser Fix noch nicht verfügbar. Das Problem mit den Popupfenstern wird durch diesen Fix nicht behoben.

Nutzen Sie die unten beschriebenen Problemumgehungen.

#### <a name="other-workarounds"></a>Weitere Problemumgehungen
Vergewissern Sie sich, dass das Problem nur in der spezifischen Version des Microsoft Edge-Browsers auftritt und dass die anderen Browser einwandfrei funktionieren, bevor Sie diese Problemumgehungen anwenden.  
1. Zunächst stellen sicher, dass die Anwendungsdomäne und alle anderen an der Umleitung des Authentifizierungsflows beteiligten Websites in den Sicherheitseinstellungen des Browsers als vertrauenswürdige Sites hinzugefügt wurden, sodass sie derselben Sicherheitszone angehören.
Gehen Sie dazu folgendermaßen vor:
    - Öffnen Sie **Internet Explorer**, und klicken Sie in der oberen rechten Ecke auf **Einstellungen** (Zahnradsymbol).
    - Wählen Sie **Internetoptionen** aus.
    - Wählen Sie die Registerkarte **Sicherheit** aus.
    - Klicken Sie unter der Option **Vertrauenswürdige Sites** auf die Schaltfläche **Sites**, und fügen Sie im daraufhin geöffneten Dialogfeld die URLs hinzu.

2. Wie bereits erwähnt, wird während der normalen Navigation nur der Sitzungsspeicher gelöscht. Daher können Sie MSAL.js stattdessen für die Verwendung des lokalen Speichers konfigurieren. Dazu können Sie während der Initialisierung von MSAL den Konfigurationsparameter `cacheLocation` verwenden.

Beim InPrivate-Browsen wird das Problem dadurch allerdings nicht behoben, da sowohl der Sitzungsspeicher als auch der lokale Speicher gelöscht werden.

## <a name="issues-due-to-popup-blockers"></a>Probleme durch Popupblocker

In manchen Fällen werden Popupfenster in Internet Explorer oder Microsoft Edge blockiert, beispielsweise, wenn bei der Multi-Factor Authentication ein zweites Popupfenster eingeblendet wird. Sie werden im Browser darüber informiert, dass Sie das Popupfenster einmalig oder dauerhaft zulassen können. Wenn Sie das Popupfenster zulassen möchten, wird es automatisch geöffnet, und der Browser gibt ein `null`-Handle dafür zurück. Dies führt dazu, dass die Bibliothek kein Handle für das Fenster hat, sodass es keine Möglichkeit gibt, das Popupfenster zu schließen. In Chrome tritt dieses Problem nicht auf, wenn sie aufgefordert werden, Popupfenster zuzulassen, da der Browser nicht automatisch ein Popupfenster öffnet.

Zur **Problemumgehung** müssen Entwickler Popupfenster in IE und Microsoft Edge zulassen, bevor sie ihre App verwenden. So wird das Problem vermieden.

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie mehr über die [Verwendung von MSAL.js in Internet Explorer](msal-js-use-ie-browser.md).
