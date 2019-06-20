---
title: Probleme beim Konfigurieren des einmaligen Anmeldens per Kennwort für eine nicht im Katalog enthaltene Anwendung | Microsoft-Dokumentation
description: Informationen zu den häufig auftretenden Problemen beim Konfigurieren des einmaligen Anmeldens per Kennwort für benutzerdefinierte, nicht im Katalog enthaltene Anwendungen, die nicht im Azure AD-Anwendungskatalog aufgeführt sind
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: f8787008b396c2dd8ce1c006a40fee1e32e8100d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60442063"
---
# <a name="problem-configuring-password-single-sign-on-for-a-non-gallery-application"></a>Probleme beim Konfigurieren des einmaligen Anmeldens per Kennwort für eine nicht im Katalog enthaltene Anwendung

In diesem Artikel finden Sie Informationen zu den häufig auftretenden Problemen beim Konfigurieren des **einmaligen Anmeldens per Kennwort** für eine nicht im Katalog enthaltene Anwendung.

## <a name="how-to-capture-sign-in-fields-for-an-application"></a>So erfassen Sie Anmeldefelder für eine Anwendung

Das Erfassen von Anmeldefeldern wird nur für HTML-fähige Anmeldeseiten unterstützt. **Nicht unterstützt werden nicht standardmäßige Anmeldeseiten**, z.B. Seiten, bei denen Flash verwendet wird, oder Seiten mit anderen nicht-HTML-fähigen Technologien.

Es gibt zwei Möglichkeiten, wie Sie Anmeldefelder für benutzerdefinierte Anwendungen erfassen können:

-   Automatische Erfassung von Anmeldefeldern

-   Manuelle Erfassung von Anmeldefeldern

Die **automatische Erfassung von Anmeldefeldern** funktioniert bei den meisten HTML-fähigen Anmeldeseiten, wenn diese **bekannte DIV-IDs für die Eingabefelder von Benutzernamen und Kennwörtern** verwenden. Hierbei wird HTML-Scraping auf der Seite durchgeführt, um DIV-IDs zu suchen, die bestimmte Kriterien erfüllen. Anschließend werden die Metadaten für diese Anwendung gespeichert, damit später Kennwörter wiedergegeben werden können.

Die **manuelle Erfassung von Anmeldefeldern** kann verwendet werden, falls es bei der Anwendung **keine Herstellerbezeichnungen** für die Eingabefelder gibt, die für die Anmeldung verwendet werden. Die manuelle Erfassung von Anmeldefeldern kann auch verwendet werden, wenn der **Hersteller mehrere Felder rendert**, die nicht automatisch erfasst werden können. Azure AD kann Daten für alle Felder auf der Anmeldeseite speichern. Hierfür müssen Sie uns lediglich mitteilen, wo sich diese Felder auf der Seite befinden.

Allgemein gilt: **Falls die automatische Erfassung von Anmeldefeldern nicht funktioniert, versuchen Sie es mit der manuellen Option.**

### <a name="how-to-automatically-capture-sign-in-fields-for-an-application"></a>So erfassen Sie Anmeldefelder für eine Anwendung automatisch

Führen Sie die folgenden Schritte aus, um das **einmalige Anmelden per Kennwort** für eine Anwendung mit der **automatischen Erfassung von Anmeldefeldern** zu konfigurieren:

1. Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** oder **Co-Administrator** an.

2. Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3. Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.

4. Klicken Sie im linken Azure Active Directory-Navigationsmenü auf **Unternehmensanwendungen**.

5. Klicken Sie auf **Alle Anwendungen**, um eine Liste aller Anwendungen anzuzeigen.

   * Wenn die gewünschte Anwendung nicht angezeigt wird, verwenden Sie das Steuerelement **Filter** oberhalb der Liste **Alle Anwendungen**, und legen Sie die Option **Anzeigen** auf **Alle Anwendungen** fest.

6. Wählen Sie die Anwendung aus, für die Sie das einmalige Anmelden konfigurieren möchten.

7. Nachdem die Anwendung geladen wurde, klicken Sie im linken Navigationsmenü der Anwendung auf **Einmaliges Anmelden**.

8. Wählen Sie den Modus **Kennwortbasiertes Anmelden** aus.

9. Geben Sie die **Anmelde-URL**. Dies ist die URL, an der Benutzer ihren Benutzernamen und ihr Kennwort für die Anmeldung eingeben. **Stellen Sie sicher, dass die Anmeldefelder unter der angegebenen URL sichtbar sind**.

10. Klicken Sie auf die Schaltfläche **Save** .

11. Diese URL wird anschließend automatisch auf ein Eingabefeld für einen Benutzernamen und ein Kennwort untersucht, und Sie können Azure AD verwenden, um auf sichere Weise Kennwörter an diese Anwendung zu übertragen, indem Sie die Zugriffsbereich-Browsererweiterung verwenden.

## <a name="how-to-manually-capture-sign-in-fields-for-an-application"></a>So erfassen Sie Anmeldefelder für eine Anwendung manuell

Wenn Sie Anmeldefelder manuell erfassen möchten, muss die Browsererweiterung für den Zugriffsbereich installiert sein. Diese **darf nicht InPrivate, inkognito oder privat ausgeführt werden**. Um die Browsererweiterung zu installieren, führen Sie die Schritte im Abschnitt [Installieren der Browsererweiterung für den Zugriffsbereich](#i-cannot-manually-detect-sign-in-fields-for-my-application) aus.

Führen Sie die folgenden Schritte aus, um das **einmalige Anmelden per Kennwort** für eine Anwendung mit der **manuellen Erfassung von Anmeldefeldern** zu konfigurieren:

1. Öffnen Sie das [**Azure-Portal**](https://portal.azure.com/), und melden Sie sich als **Globaler Administrator** oder **Co-Administrator** an.

2. Öffnen Sie die **Azure Active Directory-Erweiterung**, indem Sie oben im Hauptnavigationsmenü auf der linken Seite auf **Alle Dienste** klicken.

3. Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und wählen Sie das Element **Azure Active Directory** aus.

4. Klicken Sie im linken Azure Active Directory-Navigationsmenü auf **Unternehmensanwendungen**.

5. Klicken Sie auf **Alle Anwendungen**, um eine Liste aller Anwendungen anzuzeigen.

   * Wenn die gewünschte Anwendung nicht angezeigt wird, verwenden Sie das Steuerelement **Filter** oberhalb der Liste **Alle Anwendungen**, und legen Sie die Option **Anzeigen** auf **Alle Anwendungen** fest.

6. Wählen Sie die Anwendung aus, für die Sie das einmalige Anmelden konfigurieren möchten.

7. Nachdem die Anwendung geladen wurde, klicken Sie im linken Navigationsmenü der Anwendung auf **Einmaliges Anmelden**.

8. Wählen Sie den Modus **Kennwortbasiertes Anmelden** aus.

9. Geben Sie die **Anmelde-URL**. Dies ist die URL, an der Benutzer ihren Benutzernamen und ihr Kennwort für die Anmeldung eingeben. **Stellen Sie sicher, dass die Anmeldefelder unter der angegebenen URL sichtbar sind**.

10. Klicken Sie auf die Schaltfläche **Save** .

11. Diese URL wird anschließend automatisch auf ein Eingabefeld für einen Benutzernamen und ein Kennwort untersucht, und Sie können Azure AD verwenden, um auf sichere Weise Kennwörter an diese Anwendung zu übertragen, indem Sie die Zugriffsbereich-Browsererweiterung verwenden. Falls hierbei ein Fehler auftritt, können Sie mit Schritt 12 fortfahren, um **den Anmeldemodus für die Verwendung der manuellen Erkennung von Anmeldefeldern zu ändern**.

12. Klicken Sie auf **&lt;AppName&gt;-Einstellungen für einmaliges Anmelden über ein Kennwort konfigurieren**.

13. Wählen Sie die Konfigurationsoption **Anmeldefelder manuell erkennen** aus.

14. Klicken Sie auf **OK**.

15. Klicken Sie auf **Speichern**.

16. Befolgen Sie die Anweisungen auf dem Bildschirm, um den Zugriffsbereich zu verwenden.

## <a name="i-see-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>Die Fehlermeldung „Wir konnten unter dieser URL keine Anmeldefelder finden“ wird angezeigt.

Dieser Fehler wird angezeigt, wenn die automatische Erkennung von Anmeldefeldern fehlschlägt. Um dieses Problem zu beheben, verwenden Sie die manuelle Erkennung von Anmeldefeldern, indem Sie die Schritte im Abschnitt [So erfassen Sie Anmeldefelder für eine Anwendung manuell](#how-to-manually-capture-sign-in-fields-for-an-application) ausführen.

## <a name="i-see-an-unable-to-save-single-sign-on-configuration-error"></a>Die Fehlermeldung „Die Konfiguration für einmaliges Anmelden kann nicht gespeichert werden“ wird angezeigt.

In seltenen Fällen schlägt die Aktualisierung der Konfiguration für einmaliges Anmelden fehl. Versuchen Sie, diesen Fehler zu beheben, indem Sie die Konfiguration für einmaliges Anmelden erneut speichern.

Falls das Problem weiterhin besteht, erstellen Sie eine Supportanfrage, und geben Sie die Informationen an, die in den Abschnitten [Anzeigen von Details einer Portalbenachrichtigung](#i-cannot-manually-detect-sign-in-fields-for-my-application) und [Anfordern von Unterstützung durch das Senden von Benachrichtigungsdetails an einen Supporttechniker](#how-to-get-help-by-sending-notification-details-to-a-support-engineer) erfasst wurden.

## <a name="i-cannot-manually-detect-sign-in-fields-for-my-application"></a>Die Anmeldefelder für meine Anwendung werden nicht manuell erkannt

Sollte die manuelle Erkennung nicht funktionieren, können Sie einige der folgenden Verhaltensweisen feststellen:

-   Die manuelle Erfassung schien zu funktionieren, aber die erfassten Felder waren nicht korrekt.

-   Während der Erfassung werden nicht die richtigen Felder hervorgehoben.

-   Die Erfassung leitet mich wie erwartet auf die Anmeldeseite der Anwendung, doch dann geschieht nichts.

-   Die manuelle Erfassung scheint zu funktionieren, aber es erfolgt keine einmalige Anmeldung, wenn die Benutzer über den Zugriffsbereich zur Anwendung navigieren.

Überprüfen Sie Folgendes, wenn eines der genannten Probleme auftritt:

-   Stellen Sie sicher, dass die neueste Version der Browsererweiterung für den Zugriffsbereich **installiert** und **aktiviert** ist, indem Sie die Schritte im Abschnitt [Installieren der Browsererweiterung für den Zugriffsbereich](#how-to-install-the-access-panel-browser-extension) ausführen.

-   Vergewissern Sie sich, dass Sie die Erfassung nicht starten, wenn der Browser **inkognito, InPrivate oder im privaten Modus** ausgeführt wird. Die Erweiterung für den Zugriffsbereich wird in diesen Modi nicht unterstützt.

-   Stellen Sie sicher, dass sich Ihre Benutzer nicht über den Zugriffsbereich bei der Anwendung anmelden möchten, während sie sich in den Modi **inkognito, InPrivate oder privat** befinden. Die Erweiterung für den Zugriffsbereich wird in diesen Modi nicht unterstützt.

-   Versuchen Sie, die manuelle Erfassung erneut durchzuführen, und vergewissern Sie sich dabei, dass sich die roten Marker über den richtigen Feldern befinden.

-   Wenn die manuelle Erfassung oder die Anmeldeseite nicht mehr reagiert (Fall 3 oben), versuchen Sie, die manuelle Erfassung erneut durchzuführen. Drücken Sie dieses Mal nach Abschluss des Vorgangs jedoch die Taste **F12**, um die Entwicklerkonsole des Browsers zu öffnen. Öffnen Sie die **Konsole**, und geben Sie **window.location="&lt;Anmelde-URL, die Sie beim Konfigurieren der App angegeben haben&gt;"** ein. Drücken Sie dann die **EINGABETASTE**. So erzwingen Sie eine Seitenumleitung, die den Erfassungsprozess beendet und die erfassten Felder speichert.

Wenn keiner dieser Ansätze funktioniert, fordern Sie Unterstützung beim Support an. Erstellen Sie eine Supportanfrage mit den Details Ihrer Lösungsversuche. Geben Sie außerdem die Informationen an, die in den Abschnitten [Anzeigen von Details einer Portalbenachrichtigung](#i-cannot-manually-detect-sign-in-fields-for-my-application) und [Erhalten von Unterstützung durch Senden von Benachrichtigungsdetails an einen Supporttechniker](#how-to-get-help-by-sending-notification-details-to-a-support-engineer) erfasst wurden (sofern zutreffend).

## <a name="how-to-install-the-access-panel-browser-extension"></a>Installieren der Browsererweiterung für den Zugriffsbereich

Führen Sie zum Installieren der Browsererweiterung für den Zugriffsbereich die folgenden Schritte aus:

1.  Öffnen Sie in einem der unterstützten Browser den [Zugriffsbereich](https://myapps.microsoft.com), und melden Sie sich in Azure AD als **Benutzer** an.

2.  Klicken Sie im Zugriffsbereich auf eine **für kennwortbasiertes SSO konfigurierte Anwendung**.

3.  Wählen Sie in der Aufforderung zum Installieren der Software **Jetzt installieren** aus.

4.  Sie werden basierend auf Ihrem Browser zum Downloadlink weitergeleitet. **Fügen** Sie die Erweiterung Ihrem Browser hinzu.

5.  Wenn Sie im Browser zur Auswahl aufgefordert werden, wählen Sie die Option zum **Aktivieren** oder **Zulassen** der Erweiterung aus.

6.  Führen Sie nach der Installation einen **Neustart** der Browsersitzung durch.

7.  Melden Sie sich beim Zugriffsbereich an, und überprüfen Sie, ob Sie die für kennwortbasiertes SSO konfigurierten Anwendungen **starten** können.

Sie können die Erweiterung für Chrome und Firefox auch direkt über die folgenden Links herunterladen:

-   [Zugriffsbereichserweiterung für Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Zugriffsbereichserweiterung für Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="how-to-see-the-details-of-a-portal-notification"></a>Anzeigen von Details einer Portalbenachrichtigung

Sie können die Details von Portalbenachrichtigungen anzeigen, indem Sie folgende Schritte ausführen:

1. Klicken Sie in der rechten oberen Ecke des Azure-Portals auf das Symbol **Benachrichtigungen** (Glockensymbol).

2. Wählen Sie eine Benachrichtigung mit einem **Fehlerstatus** aus (mit einem roten (!) neben dem Namen).

   >[HINWEIS] Sie können auf keine Benachrichtigungen klicken, die den Status **Erfolgreich** oder **In Bearbeitung** aufweisen.
   >
   >

3. Der Bereich **Benachrichtigungsdetails** wird geöffnet.

4. Diese Informationen helfen Ihnen, das Problem besser zu verstehen.

5. Wenn Sie weitere Unterstützung benötigen, können Sie diese Informationen auch einem Supporttechniker oder der Produktgruppe mitteilen, um Hilfe bei Ihrem Problem zu erhalten.

6. Klicken Sie auf das **Symbol** **Kopieren** rechts neben dem Textfeld **Fehler kopieren**, um alle Benachrichtigungsdetails zu kopieren und einem Support- oder Produktgruppentechniker mitzuteilen.

## <a name="how-to-get-help-by-sending-notification-details-to-a-support-engineer"></a>Erhalten von Unterstützung durch Senden von Benachrichtigungsdetails an einen Supporttechniker

Es ist sehr wichtig, dass Sie dem Supporttechniker **alle unten aufgelisteten Details** mitteilen, wenn Sie Hilfe benötigen, damit dieser schnell helfen kann. Sie können entweder **einen Screenshot erstellen**, oder Sie klicken auf das Symbol **Fehler kopieren** rechts neben dem Textfeld **Fehler kopieren**.

## <a name="notification-details-explained"></a>Erläuterung der Benachrichtigungsdetails

Im Folgenden wird die Bedeutung der einzelnen Benachrichtigungselemente näher erläutert, und es werden Beispiele genannt.

### <a name="essential-notification-items"></a>Grundlegende Benachrichtigungselemente

-   **Titel:** der beschreibende Titel der Benachrichtigung

    -   Beispiel: **Anwendungsproxyeinstellungen**

-   **Beschreibung:** Beschreibung des Ergebnisses des Vorgangs

    -   Beispiel: **Die eingegebene interne URL wird bereits von einer anderen Anwendung verwendet**

-   **Benachrichtigungs-ID:** die eindeutige ID der Benachrichtigung

    -   Beispiel: **clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068**

-   **Clientanforderungs-ID:** die spezifische Anforderungs-ID, die vom Browser erstellt wurde

    -   Beispiel: **302fd775-3329-4670-a9f3-bea37004f0bc**

-   **Zeitstempel (UTC):** der Zeitstempel beim Auftreten der Benachrichtigung in UTC

    -   Beispiel: **2017-03-23T19:50:43.7583681Z**

-   **Interne Transaktions-ID:** die interne ID, über die wir den Fehler in unseren Systemen suchen können

    -   Beispiel: **71a2f329-ca29-402f-aa72-bc00a7aca603**

-   **UPN:** der Benutzer, der den Vorgang durchgeführt hat

    -   Beispiel: **tperkins\@f128.info**

-   **Mandanten-ID:** die eindeutige ID des Mandanten, dem der Benutzer angehört, der den Vorgang durchgeführt hat

    -   Beispiel: **7918d4b5-0442-4a97-be2d-36f9f9962ece**

-   **Benutzerobjekt-ID:** die eindeutige ID des Benutzers, der den Vorgang durchgeführt hat

    -   Beispiel: **17f84be4-51f8-483a-b533-383791227a99**

### <a name="detailed-notification-items"></a>Detaillierte Benachrichtigungselemente

-   **Anzeigename:** **(kann leer sein)** ein detaillierterer Anzeigename für den Fehler

    -   Beispiel: **Anwendungsproxyeinstellungen**

-   **Status:** der spezifische Status der Benachrichtigung

    -   Beispiel: **Fehler**

-   **Objekt-ID**: **(kann leer sein)** die Objekt-ID, für die der Vorgang durchgeführt wurde

    -   Beispiel: **8e08161d-f2fd-40ad-a34a-a9632d6bb599**

-   **Details:** detaillierte Beschreibung des Ergebnisses des Vorgangs

    -   Beispiel: **Interne URL '<https://bing.com/>' ist ungültig, da sie bereits verwendet wird**

-   **Fehler kopieren**: Klicken Sie auf das **Kopiersymbol** rechts neben dem Textfeld **Fehler kopieren**, um alle Benachrichtigungsdetails zu kopieren und einem Support- oder Produktgruppentechniker mitzuteilen.

    -   Beispiel: ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>Nächste Schritte
[Bereitstellen von einmaligem Anmelden bei Ihren Apps mit dem Anwendungsproxy](application-proxy-configure-single-sign-on-with-kcd.md)

