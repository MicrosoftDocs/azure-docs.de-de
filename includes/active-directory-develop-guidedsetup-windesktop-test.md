---
title: Includedatei
description: Includedatei
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: a11b291ab89dc9f8159e00e1f2304706f041068e
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66120171"
---
## <a name="test-your-code"></a>Testen Ihres Codes

Drücken Sie **F5**, um das Projekt in Visual Studio auszuführen. Ihre Anwendung **MainWindow** wird wie im Folgenden dargestellt angezeigt:

![Testen Ihrer Anwendung](./media/active-directory-develop-guidedsetup-windesktop-test/samplescreenshot.png)

Wenn Sie die Anwendung zum ersten Mal ausführen und auf die Schaltfläche **Microsoft Graph-API aufrufen** klicken, werden Sie zur Anmeldung aufgefordert. Verwenden Sie zum Testen ein Azure Active Directory-Konto (Geschäfts-, Schul- oder Unikonto) oder ein Microsoft-Konto (live.com, outlook.com).

![Anmelden bei der Anwendung](./media/active-directory-develop-guidedsetup-windesktop-test/signinscreenshot.png)

### <a name="provide-consent-for-application-access"></a>Zustimmen zum Anwendungszugriff

Bei der ersten Anmeldung bei Ihrer Anwendung werden Sie zudem aufgefordert, Ihre Zustimmung zu geben, dass die Anwendung auf Ihr Profil zugreifen und Sie anmelden darf, wie hier gezeigt:

![Zustimmen zum Anwendungszugriff](./media/active-directory-develop-guidedsetup-windesktop-test/consentscreen.png)

### <a name="view-application-results"></a>Anzeigen von Anwendungsergebnissen

Nachdem Sie sich angemeldet haben, sollten Sie die Benutzerprofilinformationen sehen, die vom Aufruf der Microsoft Graph-API zurückgegeben werden. Die Ergebnisse werden im Feld **Ergebnisse des API-Aufrufs** angezeigt. Grundlegende Informationen über das Token, das durch den Aufruf von `AcquireTokenInteractive` oder `AcquireTokenSilent` abgerufen wurde, sollten im Feld **Tokeninformationen** angezeigt werden. Die Ergebnisse umfassen die folgenden Eigenschaften:

|Eigenschaft  |Format  |BESCHREIBUNG |
|---------|---------|---------|

|**Username** |<span>user@domain.com</span> |Der Benutzername, der zur Identifizierung des Benutzers verwendet wird.| |**Token Expires** |DateTime |Der Zeitpunkt, zu dem das Token abläuft. MSAL verlängert die Ablauffrist, indem das Token bei Bedarf verlängert wird.|


<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Weitere Informationen zu Bereichen und delegierten Berechtigungen

Die Microsoft Graph-API benötigt den Bereich *user.read*, um das Benutzerprofil zu lesen. Dieser Bereich wird standardmäßig jeder Anwendung automatisch hinzugefügt, die im Anwendungsregistrierungsportal registriert wird. Andere Microsoft Graph-APIs sowie benutzerdefinierte APIs für Ihren Back-End-Server erfordern unter Umständen zusätzliche Bereiche. Die Microsoft Graph-API benötigt den Bereich *Calendars.Read*, um die Kalender des Benutzers aufzuführen.

Um auf die Kalender des Benutzers im Kontext einer Anwendung zugreifen zu können, müssen Sie den Informationen für die Anwendungsregistrierung die delegierte Berechtigung *Calendars.Read* hinzufügen. Fügen Sie dann dem Aufruf von `acquireTokenSilent` den Bereich *Calendars.Read* hinzu.

>[!NOTE]
>Wenn Sie die Anzahl der Bereiche erhöhen, werden Benutzer ggf. zu weiteren Genehmigungen aufgefordert.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
