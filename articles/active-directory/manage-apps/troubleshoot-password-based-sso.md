---
title: Behandeln von Problemen beim kennwortbasierten einmaligen Anmelden bei Azure Active Directory
description: Hier erfahren Sie, wie Sie Probleme mit einer Azure AD-App beheben, die für kennwortbasiertes einmaliges Anmelden konfiguriert wurde.
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: d122cedbad41d6984614a0edccb2fd98269710f2
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96618075"
---
# <a name="troubleshoot-password-based-single-sign-on-in-azure-ad"></a>Behandeln von Problemen beim kennwortbasierten einmaligen Anmelden in Azure AD

Wenn Sie kennwortbasiertes einmaliges Anmelden (Single Sign-on, SSO) in „Meine Apps“ verwenden möchten, muss die Browsererweiterung installiert sein. Die Erweiterung wird automatisch heruntergeladen, wenn Sie eine App auswählen, die für kennwortbasiertes SSO konfiguriert ist. Informationen zur Verwendung von „Meine Apps“ aus der Sicht eines Endbenutzers finden Sie unter [Hilfe zum Portal „Meine Apps“](../user-help/my-apps-portal-end-user-access.md).

## <a name="my-apps-browser-extension-not-installed"></a>Browsererweiterung für „Meine Apps“ nicht installiert
Vergewissern Sie sich, dass die Browsererweiterung installiert wurde. Weitere Informationen finden Sie unter [Planen einer Bereitstellung von „Meine Apps“ in Azure Active Directory](access-panel-deployment-plan.md). 

## <a name="single-sign-on-not-configured"></a>Einmaliges Anmelden nicht konfiguriert
Vergewissern Sie sich, dass das kennwortbasierte einmalige Anmelden konfiguriert wurde. Weitere Informationen finden Sie unter [Konfigurieren von kennwortbasiertem einmaligem Anmelden](configure-password-single-sign-on-non-gallery-applications.md).

## <a name="users-not-assigned"></a>Benutzer nicht zugewiesen
Vergewissern Sie sich, dass der Benutzer der App zugewiesen wurde. Weitere Informationen finden Sie unter [Zuweisen eines Benutzers oder einer Gruppe zu einer App](assign-user-or-group-access-portal.md).

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>Die Anmeldeinformationen wurden angegeben, werden aber von der Erweiterung nicht gesendet

Dieses Problem tritt normalerweise auf, wenn der Anwendungsanbieter vor Kurzem die Anmeldungsseite geändert hat, um ein Feld hinzuzufügen, einen Bezeichner zum Erkennen der Felder für Benutzername und Kennwort geändert hat oder die Funktionsweise der Anmeldung für die Anwendung geändert hat. In vielen Fällen kann Microsoft jedoch mit dem Anwendungshersteller zusammenarbeiten, um diese Probleme schnell zu beheben.

Microsoft verfügt zwar über Technologie, mit der automatisch erkannt werden kann, wenn es für Integrationen zu Fehlern kommt. Unter Umständen ist es aber nicht möglich, die Probleme sofort zu finden, oder die Lösung des Problems dauert einige Zeit. Falls eine dieser Integrationen nicht richtig funktioniert, können Sie eine Supportanfrage erstellen, damit der Fehler so schnell wie möglich behoben werden kann.

**Falls Sie Kontakt zum Hersteller der Anwendung haben**, können Sie ihn an Microsoft verweisen, damit wir mit ihm zusammenarbeiten können, um die Anwendung in Azure Active Directory zu integrieren. Verweisen Sie den Hersteller als Einstieg an [Ihre Anwendung im Azure Active Directory-Anwendungskatalog auflisten](../develop/v2-howto-app-gallery-listing.md).

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>Die Anmeldeinformationen wurden angegeben und gesendet, die Seite gibt jedoch an, dass die Anmeldeinformationen falsch sind

Probieren Sie es zunächst mit den folgenden Schritten, um dieses Problem zu beheben:

- Der Benutzer soll unter Verwendung der für ihn gespeicherten Anmeldeinformationen zunächst versuchen, **sich direkt bei der Anwendungswebsite anzumelden**.

  * Wenn die Anmeldung funktioniert, soll der Benutzer auf der **Anwendungskachel** im Abschnitt **Apps** von [Meine Apps](https://myapps.microsoft.com/) auf die Schaltfläche **Anmeldeinformationen aktualisieren** klicken, um die Anmeldeinformationen auf den Benutzernamen und das Kennwort zu aktualisieren, die zuletzt funktioniert haben.

  * Wenn Sie oder ein anderer Administrator die Anmeldeinformationen für diesen Benutzer zugewiesen werden, suchen Sie die Anwendungszuordnung des Benutzers oder der Gruppe, indem Sie zur Registerkarte **Users & Groups** der Anwendung navigieren, die Zuordnung auswählen und dann auf die Schaltfläche **Anmeldeinformationen aktualisieren** klicken.

- Wenn sich der Benutzer seine eigenen Anmeldeinformationen zugewiesen hat, soll er **sich vergewissern, dass sein Kennwort in der Anwendung nicht abgelaufen ist**. Sollte dies der Fall sein, soll der Benutzer **das abgelaufene Kennwort aktualisieren**, indem er sich direkt bei der Anwendung anmeldet.

  * Nach Aktualisierung des Kennworts in der Anwendung bitten Sie den Benutzer, auf der **Anwendungskachel** im Abschnitt **Apps** von [Meine Apps](https://myapps.microsoft.com/) auf die Schaltfläche **Anmeldeinformationen aktualisieren** zu klicken, um die Anmeldeinformationen auf den Benutzernamen und das Kennwort zu aktualisieren, die zuletzt bekanntermaßen funktioniert haben.

  * Wenn Sie oder ein anderer Administrator die Anmeldeinformationen für diesen Benutzer zugewiesen werden, suchen Sie die Anwendungszuordnung des Benutzers oder der Gruppe, indem Sie zur Registerkarte **Users & Groups** der Anwendung navigieren, die Zuordnung auswählen und dann auf die Schaltfläche **Anmeldeinformationen aktualisieren** klicken.

- Vergewissern Sie sich, dass die Browsererweiterung für „Meine Apps“ ausgeführt wird und im Browser Ihres Benutzers aktiviert wurde.

- Vergewissern Sie sich, dass sich Ihre Benutzer nicht über „Meine Apps“ bei der Anwendung anzumelden versuchen, während sie sich im **Modus „Inkognito“, „InPrivate“ oder „Privat“** befinden. Die Erweiterung für „Meine Apps“ wird in diesen Modi nicht unterstützt.

Sollten die obigen Vorschläge nicht funktionieren, wurde unter Umständen auf Anwendungsseite eine Änderung vorgenommen, durch die die Anwendungsintegration in Azure AD vorübergehend unterbrochen wurde. Dies kann beispielsweise vorkommen, wenn der Hersteller der Anwendung ein Skript auf seiner Seite einfügt, das ein anderes Verhalten für die manuelle bzw. automatische Eingabe aufweist, wodurch die automatische Integration (z.B. unsere) unterbrochen wird. In vielen Fällen kann Microsoft jedoch mit dem Anwendungshersteller zusammenarbeiten, um diese Probleme schnell zu beheben.

Microsoft verfügt zwar über Technologie, mit der automatisch erkannt werden kann, wenn es für Anwendungsintegrationen zu Fehlern kommt. Unter Umständen ist es aber nicht möglich, die Probleme sofort zu finden, oder die Lösung des Problems dauert einige Zeit. Wenn eine Integration nicht richtig funktioniert, können Sie eine Supportanfrage öffnen, damit das Problem so schnell wie möglich behoben werden kann. 

Falls Sie darüber hinaus **Kontakt zum Hersteller der Anwendung haben,** **verweisen Sie ihn an uns**, damit wir mit ihm zusammenarbeiten können, um die Anwendung in Azure Active Directory zu integrieren. Verweisen Sie den Hersteller als Einstieg an [Ihre Anwendung im Azure Active Directory-Anwendungskatalog auflisten](../develop/v2-howto-app-gallery-listing.md).

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>Überprüfen Sie, ob die Anmeldeseite der Anwendung vor Kurzem geändert wurde oder ein zusätzliches Feld erfordert.

Wenn die Anmeldeseite der Anwendung erheblich geändert wurde, kann dies in einigen Fällen zur Unterbrechung unserer Integration führen. Dies kann beispielsweise vorkommen, wenn ein Anwendungshersteller ein Anmeldefeld, ein Captcha oder Multi-Factor Authentication hinzufügt. In vielen Fällen kann Microsoft jedoch mit dem Anwendungshersteller zusammenarbeiten, um diese Probleme schnell zu beheben.

Microsoft verfügt zwar über Technologie, mit der automatisch erkannt werden kann, wenn es für Anwendungsintegrationen zu Fehlern kommt. Unter Umständen ist es aber nicht möglich, die Probleme sofort zu finden, oder die Lösung des Problems dauert einige Zeit. Wenn eine Integration nicht richtig funktioniert, können Sie eine Supportanfrage öffnen, damit das Problem so schnell wie möglich behoben werden kann. 

Falls Sie darüber hinaus **Kontakt zum Hersteller der Anwendung haben,** **verweisen Sie ihn an uns**, damit wir mit ihm zusammenarbeiten können, um die Anwendung in Azure Active Directory zu integrieren. Verweisen Sie den Hersteller als Einstieg an [Ihre Anwendung im Azure Active Directory-Anwendungskatalog auflisten](../develop/v2-howto-app-gallery-listing.md).

## <a name="capture-sign-in-fields-for-an-app"></a>Erfassen von Anmeldefeldern für eine Anwendung

Das Erfassen von Anmeldefeldern wird nur für HTML-fähige Anmeldeseiten unterstützt. Für nicht standardmäßige Anmeldeseiten, z.B. Seiten, die Adobe Flash oder andere nicht-HTML-fähige Technologien verwenden, wird dieses Feature nicht unterstützt.

Es gibt zwei Möglichkeiten, Anmeldefelder für Ihre benutzerdefinierten Apps zu erfassen:

- Die **automatische Erfassung von Anmeldefeldern** funktioniert bei den meisten HTML-fähigen Anmeldeseiten, *wenn diese bekannte DIV-IDs* für die Felder von Benutzernamen und Kennwörtern verwenden. Der HTML-Code auf der Seite wird nach DIV-IDs durchsucht, die bestimmte Kriterien erfüllen. Diese Metadaten werden so gespeichert, dass sie später für die App wiedergegeben werden können.

- Die **manuelle Erfassung von Anmeldefeldern** wird verwendet, wenn der App-Anbieter *die Anmeldeeingabefelder nicht mit Bezeichnungen versieht*. Die manuelle Erfassung wird auch verwendet, wenn der Anbieter *mehrere Felder rendert, die nicht automatisch erkannt werden können*. Azure Active Directory (Azure AD) kann Daten für alle Felder auf der Anmeldeseite speichern, wenn Sie Azure AD mitteilen, wo sich diese Felder auf der Seite befinden.

Allgemein gilt: Falls die automatische Erfassung von Anmeldefeldern nicht funktioniert, versuchen Sie es mit der manuellen Option.

### <a name="automatically-capture-sign-in-fields-for-an-app"></a>Automatische Erfassung von Anmeldefeldern für eine App

Führen Sie die folgenden Schritte aus, um das kennwortbasierte SSO mit der automatischen Erfassung von Anmeldefeldern zu konfigurieren:
1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/). Melden Sie sich als Globaler Administrator oder Co-Administrator an.
2. Klicken Sie im Navigationsbereich auf der linken Seite auf **Alle Dienste** um die Azure AD-Erweiterung zu öffnen.
3. Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und klicken Sie auf **Azure Active Directory**.
4. Klicken Sie im Azure AD-Navigationsbereich auf **Unternehmensanwendungen**.
5. Klicken Sie auf **Alle Anwendungen**, um eine Liste mit Ihren Apps anzuzeigen.
   > [!NOTE]
   > Wenn Ihre App nicht angezeigt wird, verwenden Sie das **Filter**-Steuerelement über der Liste **Alle Anwendungen**. Legen Sie für die Option **Anzeigen** „Alle Anwendungen“ fest.
6. Wählen Sie die App aus, die Sie für SSO konfigurieren möchten.
7. Warten Sie, bis die App geladen ist, und klicken Sie anschließend im Navigationsbereich auf der linken Seite auf **Einmaliges Anmelden**.
8. Wählen Sie den Modus **Kennwortbasiertes Anmelden** aus.
9. Geben Sie die **Anmelde-URL** ein. Dabei handelt es sich um die URL der Seite, auf der die Benutzer ihren Benutzernamen und ihr Kennwort für die Anmeldung eingeben. *Stellen Sie sicher, dass die Anmeldefelder auf der Seite für die URL, die Sie bereitstellen, angezeigt werden*.
10. Wählen Sie **Speichern** aus.
    Die Seite wird automatisch nach Eingabefeldern für Benutzernamen und Kennwort durchsucht. Sie können jetzt Kennwörter mit Azure AD über die Browsererweiterung für „Meine Apps“ auf sichere Weise an diese App übertragen.

### <a name="manually-capture-sign-in-fields-for-an-app"></a>Manuelle Erfassung von Anmeldefeldern für eine App

Wenn Sie Anmeldefelder manuell erfassen möchten, muss die Browsererweiterung für „Meine Apps“ installiert sein. Darüber hinaus kann Ihr Browser nicht im *inPrivate*-, *Inkognito*- oder *privaten* Modus ausgeführt werden.

Führen Sie die folgenden Schritte aus, um das kennwortbasierte SSO für eine App unter Verwendung der manuellen Erfassung von Anmeldefeldern zu konfigurieren:
1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/). Melden Sie sich als Globaler Administrator oder Co-Administrator an.
2. Klicken Sie im Navigationsbereich auf der linken Seite auf **Alle Dienste** um die Azure AD-Erweiterung zu öffnen.
3. Geben Sie im Filtersuchfeld **Azure Active Directory** ein, und klicken Sie auf **Azure Active Directory**.
4. Klicken Sie im Azure AD-Navigationsbereich auf **Unternehmensanwendungen**.
5. Klicken Sie auf **Alle Anwendungen**, um eine Liste mit Ihren Apps anzuzeigen.
   > [!NOTE] 
   > Wenn Ihre App nicht angezeigt wird, verwenden Sie das **Filter**-Steuerelement über der Liste **Alle Anwendungen**. Legen Sie für die Option **Anzeigen** „Alle Anwendungen“ fest.
6. Wählen Sie die App aus, die Sie für SSO konfigurieren möchten.
7. Warten Sie, bis die App geladen ist, und klicken Sie anschließend im Navigationsbereich auf der linken Seite auf **Einmaliges Anmelden**.
8. Wählen Sie den Modus **Kennwortbasiertes Anmelden** aus.
9. Geben Sie die **Anmelde-URL** ein. Dabei handelt es sich um die Seite, auf der die Benutzer ihren Benutzernamen und ihr Kennwort für die Anmeldung eingeben. *Stellen Sie sicher, dass die Anmeldefelder auf der Seite für die URL, die Sie bereitstellen, angezeigt werden*.
10. Wählen Sie ***&lt;appname&gt;*-Einstellungen für einmaliges Anmelden über ein Kennwort konfigurieren** aus.
11. Wählen Sie **Anmeldefelder manuell erkennen** aus.
14. Klicken Sie auf **OK**.
15. Wählen Sie **Speichern** aus.
16. Folgen Sie den Anleitungen zur Verwendung von „Meine Apps“.


## <a name="troubleshoot-problems"></a>Behandeln von Problemen

### <a name="i-get-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>Die Fehlermeldung „Wir konnten unter dieser URL keine Anmeldefelder finden“ wird angezeigt

Diese Fehlermeldung wird angezeigt, wenn die automatische Erkennung von Anmeldefeldern fehlschlägt. Verwenden Sie die manuelle Erkennung von Anmeldefeldern, um das Problem zu beheben. Weitere Informationen finden Sie im Abschnitt [Manuelle Erfassung von Anmeldefeldern für eine Anwendung](#manually-capture-sign-in-fields-for-an-app) dieses Artikels.

### <a name="i-get-an-unable-to-save-single-sign-on-configuration-error"></a>Die Fehlermeldung „Die Konfiguration für einmaliges Anmelden kann nicht gespeichert werden“ wird angezeigt

In seltenen Fällen tritt beim Aktualisieren der SSO-Konfiguration ein Fehler auf. Versuchen Sie, dieses Problem zu beheben, indem Sie die Konfiguration erneut speichern.

Wenn der Fehler weiterhin auftritt, öffnen Sie eine Supportanfrage. Fügen Sie die in den Abschnitten [Anzeigen der Protalbenachrichtigungsdetails](#view-portal-notification-details) und [Senden von Benachrichtigungsdetails an einen Supporttechniker, um Hilfe zu erhalten](#send-notification-details-to-a-support-engineer-to-get-help) dieses Artikels beschriebenen Informationen hinzu.

### <a name="i-cant-manually-detect-sign-in-fields-for-my-app"></a>Die Anmeldefelder für meine App werden nicht manuell erkannt

Möglicherweise beobachten Sie das folgende Verhalten, wenn die manuelle Erkennung nicht funktioniert:
- Die manuelle Erfassung schien zu funktionieren, aber die erfassten Felder waren nicht korrekt.
- Während der Ausführung der Erfassung werden nicht die richtigen Felder hervorgehoben.
- Die Erfassung leitet Sie wie erwartet auf die Anmeldeseite der App, doch dann geschieht nichts.
- Die manuelle Erfassung scheint zu funktionieren, aber es erfolgt keine einmalige Anmeldung, wenn Benutzer über „Meine Apps“ zur App navigieren.

Wenn bei Ihnen eines dieser Probleme auftritt, gehen Sie folgendermaßen vor:
- Stellen Sie sicher, dass Sie die neueste Version der Browsererweiterung für „Meine Apps“ *installiert und aktiviert* haben.
- Vergewissern Sie sich, dass der Browser während der Erfassung nicht im *Inkognito*-, im *InPrivate*- oder im *privaten* Modus ausgeführt wird. Die Erweiterung für „Meine Apps“ wird in diesen Modi nicht unterstützt.
- Stellen Sie sicher, dass sich Ihre Benutzer nicht über „Meine Apps“ bei der App anzumelden versuchen, während sie sich im Modus *Inkognito*, *InPrivate* oder *Privat* befinden.
- Versuchen Sie erneut die manuelle Erfassung. Stellen Sie sicher, dass sich die roten Marker über den richtigen Feldern befinden.
- Wenn die manuelle Erfassung oder die Anmeldeseite nicht mehr reagiert, versuchen Sie, die manuelle Erfassung erneut durchzuführen. Drücken Sie dieses Mal nach Abschluss des Vorgangs jedoch die Taste F12, um die Entwicklerkonsole des Browsers zu öffnen. Wählen Sie die Registerkarte **Konsole** aus. Geben Sie **window.location=" *&lt;die Anmelde-URL, die Sie beim Konfigurieren der App angegeben haben&gt;* "** ein. Drücken Sie dann die EINGABETASTE. So erzwingen Sie eine Seitenumleitung, die den Erfassungsprozess beendet und die erfassten Felder speichert.

### <a name="i-cant-add-another-user-to-my-password-based-sso-app"></a>Ich kann meiner kennwortbasierten SSO-App keinen weiteren Benutzer hinzufügen.

Kennwortbasierte SSO-Apps sind auf 48 Benutzer beschränkt. Daher gilt pro App eine Beschränkung auf 48 Schlüssel für Benutzername-Kennwort-Paare.
Wenn Sie zusätzliche Benutzer hinzufügen möchten, haben Sie folgende Möglichkeiten:
-   Hinzufügen einer zusätzlichen Instanz der App
-   Entfernen von Benutzern, die die App nicht mehr verwenden

## <a name="request-support"></a>Anfordern von Support 
Wenn Sie beim Einrichten von SSO und Zuweisen von Benutzern eine Fehlermeldung erhalten, öffnen Sie ein Supportticket. Fügen Sie so viele der folgenden Informationen wie möglich hinzu:

-   Fehlerkorrelations-ID
-   UPN (E-Mail-Adresse des Benutzers)
-   Mandanten-ID
-   Browsertyp
-   Zeitzone und Zeitpunkt/Zeitraum des Fehlers
-   Fiddler-Ablaufverfolgungen

### <a name="view-portal-notification-details"></a>Anzeigen der Protalbenachrichtigungsdetails

Gehen Sie folgendermaßen vor, um die Protalbenachrichtigungsdetails anzuzeigen:
1. Wählen Sie im Azure-Portal in der rechten oberen Ecke das Symbol **Benachrichtigungen** (Glockensymbol) aus.
2. Wählen Sie eine Benachrichtigung mit dem Status *Fehler* aus. (Diese sind mit einem roten „!“ versehen.)
   > [!NOTE]
   > Sie können keine Benachrichtigungen mit dem Status *Erfolgreich* oder *In Bearbeitung* auswählen.
3. Der Bereich **Benachrichtigungsdetails** wird geöffnet. Lesen Sie die Informationen zu diesem Problem.
5. Wenn Sie weitere Unterstützung benötigen, teilen Sie die Informationen einem Supporttechniker oder der Produktgruppe mit. Wählen Sie das Symbol **Kopieren** rechts neben dem Feld **Fehler kopieren** aus, um die Details der Benachrichtigung zu kopieren, die Sie weiterleiten möchten.

### <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>Senden von Benachrichtigungsdetails an einen Supporttechniker, um Hilfe zu erhalten

Es ist wichtig, dass Sie *alle* in diesem Abschnitt aufgeführten Details an den Support weiterleiten, damit Ihnen schnell geholfen werden kann. Zum Aufzeichnen können Sie einen Screenshot erstellen oder **Fehler kopieren** auswählen.

In den folgenden Informationen wird erläutert, was die einzelnen Elemente der Benachrichtigung bedeuten, und es werden Beispiele bereitgestellt.

#### <a name="essential-notification-items"></a>Grundlegende Benachrichtigungselemente

- **Titel**: der beschreibende Titel der Benachrichtigung.

   Beispiel: *Anwendungsproxyeinstellungen*

- **Beschreibung**: Ergebnis des Vorgangs.

   Beispiel: *Die eingegebene interne URL wird bereits von einer anderen Anwendung verwendet.*

- **Benachrichtigungs-ID**: die eindeutige ID der Benachrichtigung.

    Beispiel: *clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068*

- **Clientanforderungs-ID**: die spezifische Anforderungs-ID, die vom Browser erstellt wurde.

    Beispiel: *302fd775-3329-4670-a9f3-bea37004f0bc*

- **Zeitstempel (UTC)**: der Zeitstempel beim Auftreten der Benachrichtigung in UTC.

    Beispiel: *2017-03-23T19:50:43.7583681Z*

- **Interne Transaktions-ID**: die interne ID, über die wir den Fehler in unseren Systemen suchen können.

    Beispiel: **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **UPN**: Der Benutzer, der den Vorgang ausgeführt hat.

    Beispiel: *tperkins\@f128.info*

- **Mandanten-ID**: die eindeutige ID des Mandanten, dem der Benutzer angehört, der den Vorgang ausgeführt hat.

    Beispiel: *7918d4b5-0442-4a97-be2d-36f9f9962ece*

- **Benutzerobjekt-ID**: Die eindeutige ID des Benutzers, der den Vorgang ausgeführt hat.

    Beispiel: *17f84be4-51f8-483a-b533-383791227a99*

#### <a name="detailed-notification-items"></a>Detaillierte Benachrichtigungselemente

- **Anzeigename**: (kann leer sein) ein detaillierterer Anzeigename für den Fehler.

    Beispiel: *Anwendungsproxyeinstellungen*

- **Status**: der spezifische Status der Benachrichtigung.

    Beispiel: *Fehler*

- **Objekt-ID**: (kann leer sein) die Objekt-ID, für die der Vorgang ausgeführt wurde.

   Beispiel: *8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **Details**: detaillierte Beschreibung des Ergebnisses des Vorgangs.

    Beispiel: *Interne URL „<https://bing.com/>“ ist ungültig, da sie bereits verwendet wird.*

- **Fehler kopieren**: Ermöglicht Ihnen, das Symbol **Kopieren** rechts neben dem Textfeld **Fehler kopieren** auszuwählen, um die Details der Benachrichtigung zu kopieren, die Sie an den Support weiterleiten möchten.

    Beispiel: ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```


## <a name="next-steps"></a>Nächste Schritte
* [Schnellstartserie zur Anwendungsverwaltung](view-applications-portal.md)
* [Planen einer Bereitstellung von „Meine Apps“](access-panel-deployment-plan.md)
