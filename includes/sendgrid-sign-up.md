---
author: georgewallace
ms.service: multiple
ms.topic: include
ms.date: 11/25/2018
ms.author: gwallace
ms.openlocfilehash: 3590b8fdb67b25e0d4389e814aefcc4d18b1193c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015423"
---
Kunden von Azure können monatlich 25.000 kostenlose E-Mails freischalten. Mit diesen 25.000 kostenlosen E-Mails pro Monat erhalten Sie Zugriff auf erweiterte Berichterstellung und Analysen sowie auf [sämtliche APIs][all APIs] (Web, SMTP, Ereignis, Analyse usw.). Informationen zu den zusätzlichen Diensten von SendGrid finden Sie auf der [Seite mit den SendGrid-Lösungen][SendGrid Solutions].

### <a name="to-sign-up-for-a-sendgrid-account"></a>So registrieren Sie ein SendGrid-Konto
1. Melden Sie sich beim [Azure-Portal][Azure portal] an.
2. Wählen Sie im Menü des Azure-Portals oder auf der Startseite die Option **Ressource erstellen** aus.

    ![Screenshot des Menüs des Azure-Portals mit ausgewählter Option „Ressource erstellen“.][command-bar-new]
3. Suchen Sie nach **SendGrid**, und wählen Sie diese Option aus.

    ![Screenshot des Marketplace-Bildschirms im Azure-Portal, der „SendGr“ im Suchfeld anzeigt. „SendGrid“ist in den Suchergebnissen ausgewählt.][sendgrid-store]
4. Füllen Sie das Registrierungsformular aus, und wählen Sie **Erstellen**.

    ![Screenshot des Dialogfelds „Neues SendGrid-Konto erstellen“, in dem die Felder „Name“, „Kennwort“, „Abonnement“ und „Ressourcengruppe“ ausgefüllt sind.][sendgrid-create]
5. Geben Sie unter **Name** einen Namen ein, um Ihren SendGrid-Dienst in Ihren Azure-Einstellungen zu identifizieren. Namen müssen zwischen 1 und 100 Zeichen lang sein und dürfen nur alphanumerische Zeichen, Gedankenstriche, Punkte und Unterstriche enthalten. Der Name muss in der Liste der abonnierten Azure Store-Artikel eindeutig sein.
6. Geben Sie Ihr **Kennwort** ein, und bestätigen Sie es.
7. Wählen Sie Ihr **Abonnement** aus.
8. Erstellen Sie eine neue **Ressourcengruppe**, oder verwenden Sie eine bereits vorhandene.
9. Wählen Sie im Abschnitt **Tarif** den SendGrid-Tarif aus, für den Sie sich registrieren möchten.

    ![Screenshot des Dialogfelds „Neues SendGrid-Konto erstellen“ mit geöffnetem Abschnitt „Tarif auswählen“ und ausgewähltem Tarif „Free“.][sendgrid-pricing]
10. Geben Sie einen **Promotioncode** ein (sofern vorhanden).
11. Geben Sie Ihre **Kontaktinformationen** ein.
12. Lesen Sie sich die rechtlichen **Bedingungen** durch, und akzeptieren Sie sie.
13. Im Anschluss an die Kaufbestätigung erscheint ein Popup mit dem Hinweis, dass die **Bereitstellung erfolgreich** war, und Ihr Konto wird aufgeführt.

    ![Screenshot der Seite „SendGrid-Konten“, auf der das neue Konto „ContosoSendGrid“ angezeigt wird.][all-resources]

    Nachdem Sie den Kauf abgeschlossen und zum Initiieren der E-Mail-Überprüfung auf die Schaltfläche **Verwalten** geklickt haben, erhalten Sie eine E-Mail von SendGrid, in der Sie gebeten werden, Ihr Konto zu bestätigen. Sollten Sie diese E-Mail nicht erhalten oder Ihr Konto nicht bestätigen können, lesen Sie sich unsere häufig gestellten Fragen durch.

    ![Screenshot der Kontoseite „ContosoSendGrid“ mit hervorgehobener Schaltfläche „Verwalten“.][manage]

    **Solange das Konto noch nicht bestätigt wurde, können pro Tag nur maximal 100 E-Mails versendet werden.**

    Wenn Sie Ihren Abonnementplan ändern oder die SendGrid-Kontakteinstellungen anzeigen möchten, klicken Sie auf den Namen des SendGrid-Diensts, um das SendGrid-Marketplace-Dashboard zu öffnen.

    ![Screenshot, der zeigt, dass die Seite „Einstellungen“ für das Konto „ContosoSendGrid“ geöffnet wird, indem auf der Kontoseite „ContosoSendGrid“ die Option „Alle Einstellungen“ ausgewählt wird.][settings]

    Wenn Sie eine E-Mail mit SendGrid senden möchten, müssen Sie Ihren API-Schlüssel angeben.

### <a name="to-find-your-sendgrid-api-key"></a>So finden Sie Ihren API-Schlüssel für SendGrid
1. Klicken Sie auf **Manage**.

    ![Screenshot der Kontoseite „ContosoSendGrid“ mit hervorgehobener Schaltfläche „Verwalten“.][manage]
2. Wählen Sie auf Ihrem SendGrid-Dashboard die Option **Settings** (Einstellungen) und anschließend im Menü auf der linken Seite die Option **API Keys** (API-Schlüssel) aus.

    ![Screenshot des SendGrid-Dashboards mit geöffnetem Dropdownmenü „Einstellungen“ und ausgewählten API-Schlüsseln.][api-keys]

3. Klicken Sie auf **API-Schlüssel erstellen**.

    ![Screenshot des Bildschirms „API-Schlüssel“ mit ausgewählter Schaltfläche „API-Schlüssel erstellen“.][general-api-key]
4. Geben Sie mindestens einen Wert für **Name of this key** (Name dieses Schlüssels) an, gewähren Sie Vollzugriff für **Mail Send** (Senden von E-Mails), und wählen Sie **Save** (Speichern).

    ![Screenshot des Bildschirms „Neuen allgemeinen API-Schlüssel hinzufügen“, auf dem „E-Mail senden“ auf „Vollzugriff“ und „Geplante Sendevorgänge“ auf „Kein Zugriff“ festgelegt ist und die Schaltfläche „Speichern“ hervorgehoben ist.][access]
5. Ihre API wird daraufhin einmal angezeigt. Bewahren Sie sie an einem sicheren Ort auf.

### <a name="to-find-your-sendgrid-credentials"></a>So finden Sie Ihre SendGrid-Anmeldeinformationen
1. Klicken Sie auf das Schlüsselsymbol, um zu Ihrem **Benutzernamen** zu gelangen.

    ![Screenshot: Kontoseite „ContosoSendGrid“ mit hervorgehobenem Schlüsselsymbol][key]
2. Als Kennwort wird das Kennwort verwendet, das Sie bei der Einrichtung gewählt haben. Wenn Sie Änderungen vornehmen möchten, können Sie auf **Kennwort ändern** oder auf **Kennwort zurücksetzen** klicken.

Die Einstellungen für die Zustellung von E-Mails können durch Klicken auf die Schaltfläche **Verwalten** verwaltet werden. Daraufhin werden Sie zum SendGrid-Dashboard umgeleitet.

![Screenshot der Kontoseite „ContosoSendGrid“ mit hervorgehobener Schaltfläche „Verwalten“.][manage]

Weitere Informationen zum Senden von E-Mails über SendGrid finden Sie in der [Übersicht über die E-Mail-API][Email API Overview].

<!--images-->

[command-bar-new]: ./media/sendgrid-sign-up/new-addon.png
[sendgrid-store]: ./media/sendgrid-sign-up/sendgrid-store.png
[sendgrid-create]: ./media/sendgrid-sign-up/sendgrid-create.png
[sendgrid-pricing]: ./media/sendgrid-sign-up/sendgrid-pricing.png
[all-resources]: ./media/sendgrid-sign-up/all-resources.png
[manage]: ./media/sendgrid-sign-up/manage.png
[settings]: ./media/sendgrid-sign-up/settings.png
[api-keys]: ./media/sendgrid-sign-up/api-keys.png
[general-api-key]: ./media/sendgrid-sign-up/general-api-key.png
[access]: ./media/sendgrid-sign-up/access.png
[key]: ./media/sendgrid-sign-up/key.png

<!--Links-->

[SendGrid Solutions]: https://sendgrid.com/solutions
[Azure portal]: https://portal.azure.com
[SendGrid Getting Started]: http://sendgrid.com/docs
[SendGrid Provisioning Process]: https://support.sendgrid.com/hc/articles/200181628-Why-is-my-account-being-provisioned-
[all APIs]: https://sendgrid.com/docs/API_Reference/index.html
[Email API Overview]: https://sendgrid.com/docs/API_Reference/Web_API_v3/Mail/index.html
