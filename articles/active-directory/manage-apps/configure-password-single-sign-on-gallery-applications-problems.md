---
title: Probleme beim Konfigurieren des einmaligen Anmeldens per Kennwort für eine Azure AD-Kataloganwendung | Microsoft-Dokumentation
description: Informationen zu den häufig auftretenden Problemen beim Konfigurieren des einmaligen Anmeldens per Kennwort für Anwendungen, die bereits im Azure AD-Anwendungskatalog aufgeführt sind
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: a35ef95074099499186eae0fadd37f1995d8e725
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190293"
---
# <a name="problem-configuring-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Probleme beim Konfigurieren des einmaligen Anmeldens per Kennwort für eine Azure AD-Kataloganwendung

In diesem Artikel finden Sie Informationen zu den häufig auftretenden Problemen beim Konfigurieren des **einmaligen Anmeldens per Kennwort** für eine Azure AD-Kataloganwendung.

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>Die Anmeldeinformationen wurden angegeben, werden aber von der Erweiterung nicht gesendet

Dieses Problem tritt normalerweise auf, wenn der Anwendungsanbieter vor Kurzem die Anmeldungsseite geändert hat, um ein Feld hinzuzufügen, einen Bezeichner zum Erkennen der Felder für Benutzername und Kennwort geändert hat oder die Funktionsweise der Anmeldung für die Anwendung geändert hat. In vielen Fällen kann Microsoft jedoch mit dem Anwendungshersteller zusammenarbeiten, um diese Probleme schnell zu beheben.

Microsoft verfügt zwar über Technologie, mit der automatisch erkannt werden kann, wenn es für Integrationen zu Fehlern kommt. Unter Umständen ist es aber nicht möglich, die Probleme sofort zu finden, oder die Lösung des Problems dauert einige Zeit. Falls eine dieser Integrationen nicht richtig funktioniert, können Sie eine Supportanfrage erstellen, damit der Fehler so schnell wie möglich behoben werden kann.

**Falls Sie Kontakt zum Hersteller der Anwendung haben**, können Sie ihn an Microsoft verweisen, damit wir mit ihm zusammenarbeiten können, um die Anwendung in Azure Active Directory zu integrieren. Verweisen Sie den Hersteller als Einstieg an [Ihre Anwendung im Azure Active Directory-Anwendungskatalog auflisten](../develop/howto-app-gallery-listing.md).

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>Die Anmeldeinformationen wurden angegeben und gesendet, die Seite gibt jedoch an, dass die Anmeldeinformationen falsch sind

Probieren Sie es zunächst mit den folgenden Schritten, um dieses Problem zu beheben:

- Der Benutzer soll unter Verwendung der für ihn gespeicherten Anmeldeinformationen zunächst versuchen, **sich direkt bei der Anwendungswebsite anzumelden**.

  * Wenn die Anmeldung funktioniert, soll der Benutzer auf der **Anwendungskachel** im Abschnitt **Apps** des Bereichs [Anwendungszugriff](https://myapps.microsoft.com/) auf die Schaltfläche **Anmeldeinformationen aktualisieren** klicken, um die Anmeldeinformationen auf den Benutzernamen und das Kennwort zu aktualisieren, die zuletzt funktioniert haben.

  * Wenn Sie oder ein anderer Administrator die Anmeldeinformationen für diesen Benutzer zugewiesen werden, suchen Sie die Anwendungszuordnung des Benutzers oder der Gruppe, indem Sie zur Registerkarte **Users & Groups** der Anwendung navigieren, die Zuordnung auswählen und dann auf die Schaltfläche **Anmeldeinformationen aktualisieren** klicken.

- Wenn sich der Benutzer seine eigenen Anmeldeinformationen zugewiesen hat, soll er **sich vergewissern, dass sein Kennwort in der Anwendung nicht abgelaufen ist**. Sollte dies der Fall sein, soll der Benutzer **das abgelaufene Kennwort aktualisieren**, indem er sich direkt bei der Anwendung anmeldet.

  * Nachdem das Kennwort in der Anwendung aktualisiert wurde, bitten Sie den Benutzer, in der **Anwendungskachel** im Abschnitt **Apps** des Bereichs [Anwendungszugriff](https://myapps.microsoft.com/) auf die Schaltfläche **Anmeldeinformationen aktualisieren** zu klicken, um die Anmeldeinformationen auf den Benutzernamen und das Kennwort zu aktualisieren, die zuletzt bekanntermaßen funktioniert haben.

  * Wenn Sie oder ein anderer Administrator die Anmeldeinformationen für diesen Benutzer zugewiesen werden, suchen Sie die Anwendungszuordnung des Benutzers oder der Gruppe, indem Sie zur Registerkarte **Users & Groups** der Anwendung navigieren, die Zuordnung auswählen und dann auf die Schaltfläche **Anmeldeinformationen aktualisieren** klicken.

- Der Benutzer soll die Browsererweiterung für den Zugriffsbereich aktualisieren, indem er die Schritte im Abschnitt [Installieren der Browsererweiterung für den Zugriffsbereich](#how-to-install-the-access-panel-browser-extension) ausführt.

- Stellen Sie sicher, dass die Browsererweiterung für den Zugriffsbereich ausgeführt wird und im Browser des Benutzers aktiviert ist.

- Stellen Sie sicher, dass sich Ihre Benutzer nicht über den Zugriffsbereich bei der Anwendung anmelden möchten, während sie sich in den Modi **inkognito, InPrivate oder privat** befinden. Die Erweiterung für den Zugriffsbereich wird in diesen Modi nicht unterstützt.

Sollten die obigen Vorschläge nicht funktionieren, wurde unter Umständen auf Anwendungsseite eine Änderung vorgenommen, durch die die Anwendungsintegration in Azure AD vorübergehend unterbrochen wurde. Dies kann beispielsweise vorkommen, wenn der Hersteller der Anwendung ein Skript auf seiner Seite einfügt, das ein anderes Verhalten für die manuelle bzw. automatische Eingabe aufweist, wodurch die automatische Integration (z.B. unsere) unterbrochen wird. In vielen Fällen kann Microsoft jedoch mit dem Anwendungshersteller zusammenarbeiten, um diese Probleme schnell zu beheben.

Microsoft verfügt zwar über Technologie, mit der automatisch erkannt werden kann, wenn es für Anwendungsintegrationen zu Fehlern kommt. Unter Umständen ist es aber nicht möglich, die Probleme sofort zu finden, oder die Lösung des Problems dauert einige Zeit. Wenn eine Integration nicht richtig funktioniert, können Sie eine Supportanfrage öffnen, damit das Problem so schnell wie möglich behoben werden kann. 

Falls Sie darüber hinaus **Kontakt zum Hersteller der Anwendung haben,** **verweisen Sie ihn an uns**, damit wir mit ihm zusammenarbeiten können, um die Anwendung in Azure Active Directory zu integrieren. Verweisen Sie den Hersteller als Einstieg an [Ihre Anwendung im Azure Active Directory-Anwendungskatalog auflisten](../develop/howto-app-gallery-listing.md).

## <a name="the-extension-works-in-chrome-and-firefox-but-not-in-internet-explorer"></a>Die Erweiterung funktioniert in Chrome und Firefox, jedoch nicht in Internet Explorer.

Es gibt zwei Hauptursachen für dieses Problem:

- Abhängig von den in Internet Explorer aktivierten Sicherheitseinstellungen, d. h., wenn die Website nicht Teil einer **Vertrauenswürdigen Zone** ist, wird die Ausführung unseres Skripts für die Anwendung in einigen Fällen blockiert.

  *  Weisen Sie den Benutzer zum Beheben dieses Problems an, **die Website der Anwendung** der Liste **Vertrauenswürdige Sites** in den **Internet Explorer-Sicherheitseinstellungen** hinzuzufügen. Für eine detaillierte Anleitung können Sie den Benutzer auf den Artikel [Wie füge ich eine Website zur Liste vertrauenswürdiger Sites hinzu](https://answers.microsoft.com/en-us/ie/forum/ie9-windows_7/how-do-i-add-a-site-to-my-trusted-sites-list/98cc77c8-b364-e011-8dfc-68b599b31bf5) verweisen.

- In seltenen Fällen kann die Internet Explorer-Sicherheitsüberprüfung dazu führen, dass die Seite langsamer als die Ausführung unseres Skripts geladen wird.

  * Diese Situation kann leider je nach Browserversion, Geschwindigkeit des Computers oder der besuchten Website variieren. In diesem Fall wird empfohlen, sich an den Support zu wenden, damit wir die Integration für die Anwendung wiederherstellen können.

Falls Sie darüber hinaus **Kontakt zum Hersteller der Anwendung haben,** **verweisen Sie ihn an uns**, damit wir mit ihm zusammenarbeiten können, um die Anwendung in Azure Active Directory zu integrieren. Verweisen Sie den Hersteller als Einstieg an [Ihre Anwendung im Azure Active Directory-Anwendungskatalog auflisten](../develop/howto-app-gallery-listing.md).

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>Überprüfen Sie, ob die Anmeldeseite der Anwendung vor Kurzem geändert wurde oder ein zusätzliches Feld erfordert.

Wenn die Anmeldeseite der Anwendung erheblich geändert wurde, kann dies in einigen Fällen zur Unterbrechung unserer Integration führen. Dies kann beispielsweise vorkommen, wenn ein Anwendungshersteller ein Anmeldefeld, ein Captcha oder Multi-Factor Authentication hinzufügt. In vielen Fällen kann Microsoft jedoch mit dem Anwendungshersteller zusammenarbeiten, um diese Probleme schnell zu beheben.

Microsoft verfügt zwar über Technologie, mit der automatisch erkannt werden kann, wenn es für Anwendungsintegrationen zu Fehlern kommt. Unter Umständen ist es aber nicht möglich, die Probleme sofort zu finden, oder die Lösung des Problems dauert einige Zeit. Wenn eine Integration nicht richtig funktioniert, können Sie eine Supportanfrage öffnen, damit das Problem so schnell wie möglich behoben werden kann. 

Falls Sie darüber hinaus **Kontakt zum Hersteller der Anwendung haben,** **verweisen Sie ihn an uns**, damit wir mit ihm zusammenarbeiten können, um die Anwendung in Azure Active Directory zu integrieren. Verweisen Sie den Hersteller als Einstieg an [Ihre Anwendung im Azure Active Directory-Anwendungskatalog auflisten](../develop/howto-app-gallery-listing.md).

## <a name="how-to-install-the-access-panel-browser-extension"></a>Installieren der Browsererweiterung für den Zugriffsbereich

Führen Sie zum Installieren der Browsererweiterung für den Zugriffsbereich die folgenden Schritte aus:

1.  Öffnen Sie in einem der unterstützten Browser den [Zugriffsbereich](https://myapps.microsoft.com), und melden Sie sich in Azure AD als **Benutzer** an.

2.  Klicken Sie im Zugriffsbereich auf eine **für kennwortbasiertes SSO konfigurierte Anwendung**.

3.  Wählen Sie in der Aufforderung zum Installieren der Software **Jetzt installieren** aus.

4.  Sie werden basierend auf Ihrem Browser zum Downloadlink weitergeleitet. **Fügen** Sie die Erweiterung Ihrem Browser hinzu.

5.  Wenn Sie im Browser zur Auswahl aufgefordert werden, wählen Sie die Option zum **Aktivieren** oder **Zulassen** der Erweiterung aus.

6.  Führen Sie nach der Installation einen **Neustart** Ihrer Browsersitzung durch.

7.  Melden Sie sich beim Zugriffsbereich an, und überprüfen Sie, ob Sie die für kennwortbasiertes SSO konfigurierten Anwendungen **starten** können.

Sie können die Erweiterung für Chrome und Firefox auch direkt über die folgenden Links herunterladen:

-   [Zugriffsbereichserweiterung für Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Zugriffsbereichserweiterung für Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="next-steps"></a>Nächste Schritte
[Bereitstellen von einmaligem Anmelden bei Ihren Apps mit dem Anwendungsproxy](application-proxy-configure-single-sign-on-with-kcd.md)

