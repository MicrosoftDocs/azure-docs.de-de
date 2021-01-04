---
title: Beheben von Problemen bei der Anmeldung für ein Azure-Abonnement
description: Hilft bei der Behebung von Problemen, die das Anmelden beim Azure-Portal oder Azure-Kontocenter verhindern.
services: cost-management-billing
author: v-miegge
manager: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 08/20/2020
ms.author: v-miegge
ms.openlocfilehash: 2e9b14fa264f3286134913e3c279c4400ce5bcc3
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132328"
---
# <a name="troubleshoot-azure-subscription-sign-in-issues"></a>Beheben von Problemen bei der Anmeldung für ein Azure-Abonnement

Dieser Leitfaden enthält hilfreiche Informationen zur Behebung von Problemen, die das Anmelden beim Azure-Portal oder Azure-Kontocenter verhindern.

> [!NOTE]
> Wenn Sie Probleme bei der Registrierung für ein neues Azure-Konto haben, helfen Ihnen die Informationen unter [Beheben von Problemen beim Registrieren eines neuen Kontos im Azure-Portal oder im Azure-Kontocenter](./troubleshoot-azure-sign-up.md) weiter.

## <a name="page-hangs-in-the-loading-status"></a>Seite hängt im Ladestatus

Wenn Ihr Internetbrowser hängt, können Sie nacheinander die folgenden Schritte ausprobieren, bis Sie auf das Azure-Portal zugreifen können.

- Aktualisieren Sie die Seite.
- Verwenden Sie einen anderen Internetbrowser.
- Verwenden Sie den privaten Modus in Ihrem Browser:

   - **Microsoft Edge:** Öffnen Sie **Einstellungen** (drei Punkte neben Ihrem Profilbild), und wählen Sie die Option **Neues InPrivate-Fenster** aus. Navigieren Sie dann zum [Azure-Portal](https://portal.azure.com/) oder [Azure-Kontocenter](https://account.azure.com/Subscriptions), und melden Sie sich an. 
   - **Chrome:** Wählen Sie den Modus **Inkognito** aus.
   - **Safari:** Wählen Sie **Datei** und dann **Neues privates Fenster** aus.

- Löschen Sie den Cache und die Internetcookies:

   - **Microsoft Edge:** Öffnen Sie **Einstellungen**, und wählen Sie **Datenschutz und Dienste** aus. Führen Sie die Schritte unter **Browserdaten löschen** aus. Vergewissern Sie sich, dass die Kontrollkästchen für **Browserverlauf**, **Downloadverlauf** und **Zwischengespeicherte Bilder und Dateien** aktiviert sind, und wählen Sie dann die Option **Löschen** aus.
   - **Chrome:** Wählen Sie **Einstellungen** und unter **Datenschutz und Sicherheit** dann **Browserdaten löschen** aus.

## <a name="you-are-automatically-signed-in-as-a-different-user"></a>Sie werden automatisch als ein anderer Benutzer angemeldet

Dieses Problem kann auftreten, wenn Sie mehrere Benutzerkonten in einem Internetbrowser verwenden.

Um dieses Problem zu lösen, probieren Sie eine der folgenden Methoden aus:

- Löschen Sie Cache und Internetcookies.

   - **Microsoft Edge:** Öffnen Sie **Einstellungen**, und wählen Sie **Datenschutz und Dienste** aus. Führen Sie die Schritte unter **Browserdaten löschen** aus. Vergewissern Sie sich, dass die Kontrollkästchen für **Browserverlauf**, **Downloadverlauf**, **Cookies** und **Zwischengespeicherte Bilder und Dateien** aktiviert sind, und wählen Sie dann die Option **Löschen** aus.
   - **Chrome:** Wählen Sie **Einstellungen** und unter **Datenschutz und Sicherheit** dann **Browserdaten löschen** aus.
- Setzen Sie Ihre Browsereinstellungen auf die Standardwerte zurück.
- Verwenden Sie den privaten Modus in Ihrem Browser. 
   - **Microsoft Edge:** Öffnen Sie **Einstellungen** (drei Punkte neben Ihrem Profilbild), und wählen Sie die Option **Neues InPrivate-Fenster** aus. Navigieren Sie dann zum [Azure-Portal](https://portal.azure.com/) oder [Azure-Kontocenter](https://account.azure.com/Subscriptions), und melden Sie sich an. 
   - **Chrome:** Wählen Sie den Modus **Inkognito** aus.
   - **Safari:** Wählen Sie **Datei** und dann **Neues privates Fenster** aus.

## <a name="i-can-sign-in-but-i-see-the-error-no-subscriptions-found"></a>Ich kann mich zwar anmelden, aber der Fehler „Keine Abonnements gefunden“ wird angezeigt

Dieses Problem tritt auf, wenn Sie das falsche Verzeichnis ausgewählt haben oder Ihr Konto nicht über ausreichende Berechtigungen verfügt.

**Szenario 1:** Sie erhalten die Fehlermeldung bei der Anmeldung beim [Azure-Portal](https://portal.azure.com/)

So beheben Sie dieses Problem:

- Vergewissern Sie sich, dass das richtige Azure-Verzeichnis ausgewählt ist, indem Sie oben rechts Ihr Konto auswählen.
- Wenn das richtige Azure-Verzeichnis ausgewählt ist und die Fehlermeldung trotzdem noch ausgegeben wird, [sollten Sie Ihr Konto als Besitzer hinzufügen lassen](./add-change-subscription-administrator.md).

**Szenario 2:** Sie erhalten die Fehlermeldung bei der Anmeldung beim [Azure-Kontocenter](https://account.windowsazure.com/Subscriptions)

Überprüfen Sie, ob das Konto, das Sie verwendet haben, das des Kontoadministrators ist. Gehen Sie folgendermaßen vor, um zu überprüfen, wer der Kontoadministrator ist:

1.  Melden Sie sich bei der [Ansicht „Abonnements“ im Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) an.
1.  Wählen Sie das zu überprüfende Abonnement und dann **Einstellungen** aus.
1.  Wählen Sie **Eigenschaften** aus. Der Kontoadministrator des Abonnements wird im Feld **Kontoadministrator** angezeigt.

## <a name="additional-help-resources"></a>Zusätzliche Hilferessourcen

Weitere Artikel für die Problembehandlung bei Azure-Abrechnung und -Abonnements

- [Karte abgelehnt](./troubleshoot-declined-card.md)
- [Probleme bei der Registrierung für ein Abonnement](./troubleshoot-azure-sign-up.md)
- [Keine Abonnements gefunden](./no-subscriptions-found.md)
- [Enterprise-Kostenansicht deaktiviert](./enterprise-mgmt-grp-troubleshoot-cost-view.md)
- [Dokumentation zur Azure-Abrechnung](../index.yml)

## <a name="contact-us-for-help"></a>Setzen Sie sich mit uns in Verbindung, um Hilfe zu erhalten.

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).