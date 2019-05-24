---
author: erikre
ms.service: multiple
ms.topic: include
ms.date: 11/25/2018
ms.author: erikre
ms.openlocfilehash: 96c4da8465a87fee4c00bfc6177515c94910704a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121376"
---
Kunden von Azure können monatlich 25.000 kostenlose E-Mails freischalten. Mit diesen 25.000 kostenlosen E-Mails pro Monat erhalten Sie Zugriff auf erweiterte Berichterstellung und Analysen sowie auf [sämtliche APIs][all APIs] (Web, SMTP, Ereignis, Analyse usw.). Informationen zu den zusätzlichen Diensten von SendGrid finden Sie auf der [Seite mit den SendGrid-Lösungen][SendGrid Solutions].

### <a name="to-sign-up-for-a-sendgrid-account"></a>So registrieren Sie ein SendGrid-Konto
1. Melden Sie sich beim [Azure-Portal][Azure portal] an.
2. Wählen Sie im Menü auf der linken Seite **Ressource erstellen** aus.

    ![command-bar-new][command-bar-new]
3. Klicken Sie auf **Add-Ons** und anschließend auf **E-Mail-Zustellung über SendGrid**.

    ![sendgrid-store][sendgrid-store]
4. Füllen Sie das Registrierungsformular aus, und wählen Sie **Erstellen**.

    ![sendgrid-create][sendgrid-create]
5. Geben Sie unter **Name** einen Namen ein, um Ihren SendGrid-Dienst in Ihren Azure-Einstellungen zu identifizieren. Namen müssen zwischen 1 und 100 Zeichen lang sein und dürfen nur alphanumerische Zeichen, Gedankenstriche, Punkte und Unterstriche enthalten. Der Name muss in der Liste der abonnierten Azure Store-Artikel eindeutig sein.
6. Geben Sie Ihr **Kennwort** ein, und bestätigen Sie es.
7. Wählen Sie Ihr **Abonnement** aus.
8. Erstellen Sie eine neue **Ressourcengruppe**, oder verwenden Sie eine bereits vorhandene.
9. Wählen Sie im Abschnitt **Tarif** den SendGrid-Tarif aus, für den Sie sich registrieren möchten.

    ![sendgrid-pricing][sendgrid-pricing]
10. Geben Sie einen **Promotioncode** ein (sofern vorhanden).
11. Geben Sie Ihre **Kontaktinformationen** ein.
12. Lesen Sie sich die rechtlichen **Bedingungen** durch, und akzeptieren Sie sie.
13. Im Anschluss an die Kaufbestätigung erscheint ein Popup mit dem Hinweis, dass die **Bereitstellung erfolgreich** war, und Ihr Konto wird im Abschnitt **Alle Ressourcen** aufgeführt.

    ![Alle Ressourcen][all-resources]

    Nachdem Sie den Kauf abgeschlossen und zum Initiieren der E-Mail-Überprüfung auf die Schaltfläche **Verwalten** geklickt haben, erhalten Sie eine E-Mail von SendGrid, in der Sie gebeten werden, Ihr Konto zu bestätigen. Sollten Sie diese E-Mail nicht erhalten oder Ihr Konto nicht bestätigen können, lesen Sie sich die häufig gestellten Fragen durch.

    ![manage][manage]

    **Solange das Konto noch nicht bestätigt wurde, können pro Tag nur maximal 100 E-Mails versendet werden.**

    Wenn Sie Ihren Abonnementplan ändern oder die SendGrid-Kontakteinstellungen anzeigen möchten, klicken Sie auf den Namen des SendGrid-Diensts, um das SendGrid-Marketplace-Dashboard zu öffnen.

    ![settings][settings]

    Wenn Sie eine E-Mail mit SendGrid senden möchten, müssen Sie Ihren API-Schlüssel angeben.

### <a name="to-find-your-sendgrid-api-key"></a>So finden Sie Ihren API-Schlüssel für SendGrid
1. Klicken Sie auf **Manage**.

    ![manage][manage]
2. Wählen Sie auf Ihrem SendGrid-Dashboard die Option **Settings** (Einstellungen) und anschließend im Menü auf der linken Seite die Option **API Keys** (API-Schlüssel) aus.

    ![api-keys][api-keys]

3. Klicken Sie auf **API-Schlüssel erstellen**.

    ![general-api-key][general-api-key]
4. Geben Sie mindestens einen Wert für **Name of this key** (Name dieses Schlüssels) an, gewähren Sie Vollzugriff für **Mail Send** (Senden von E-Mails), und wählen Sie **Save** (Speichern).

    ![access][access]
5. Ihre API wird daraufhin einmal angezeigt. Bewahren Sie sie an einem sicheren Ort auf.

### <a name="to-find-your-sendgrid-credentials"></a>So finden Sie Ihre SendGrid-Anmeldeinformationen
1. Klicken Sie auf das Schlüsselsymbol, um zu Ihrem **Benutzernamen** zu gelangen.

    ![key][key]
2. Als Kennwort wird das Kennwort verwendet, das Sie bei der Einrichtung gewählt haben. Wenn Sie Änderungen vornehmen möchten, können Sie auf **Kennwort ändern** oder auf **Kennwort zurücksetzen** klicken.

Die Einstellungen für die Zustellung von E-Mails können durch Klicken auf die Schaltfläche **Verwalten** verwaltet werden. Daraufhin werden Sie zum SendGrid-Dashboard umgeleitet.

![manage][manage]

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
