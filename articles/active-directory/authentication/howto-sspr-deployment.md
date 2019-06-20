---
title: Bereitstellungshandbuch zur Self-Service-Kennwortzurücksetzung – Azure Active Directory
description: Tipps für ein erfolgreiches Rollout der Self-Service-Kennwortzurücksetzung von Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c254ef3a71e95b33df2a779c728d47fff3c3759
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65190375"
---
# <a name="how-to-successfully-roll-out-self-service-password-reset"></a>Erfolgreicher Rollout der Self-Service-Kennwortzurücksetzung

Um einen reibungslosen Rollout der Funktion für die Self-Service-Kennwortzurücksetzung (SSPR) von Azure Active Directory (Azure AD) sicherzustellen, führen die meisten Kunden die folgenden Schritte aus:

> [!VIDEO https://www.youtube.com/embed/OZn5btP6ZXw]

1. Führen Sie mit einem kleinen Teilbereich Ihrer Organisation einen Pilotversuch durch.
   * Informationen zum Pilotversuch finden Sie unter [Tutorial: Ausführen eines Rollouts der Azure AD-Self-Service-Kennwortzurücksetzung für eine Pilotgruppe](tutorial-sspr-pilot.md).
1. Schulen Sie Ihren Helpdesk.
   * Wie wird das Helpdeskpersonal Ihren Benutzern helfen?
   * Werden Sie von Benutzern die ausschließliche Verwendung von SSPR verlangen und nicht zulassen, dass Ihr Helpdesk Benutzer unterstützt?
   * Haben Sie dem Helpdeskpersonal die URLs für Registrierung und Zurücksetzung zur Verfügung gestellt?
      * Registrierung: https://aka.ms/ssprsetup
      * Zurücksetzung: https://aka.ms/sspr

   > [!WARNING]
   > Die Verwendung des Kontrollkästchens „Benutzer muss Kennwort bei der nächsten Anmeldung ändern“ in lokalen Active Directory-Verwaltungstools wie „Active Directory-Benutzer und -Computer“ oder dem Active Directory-Verwaltungscenter wird nicht unterstützt. Beim Ändern eines Kennworts in der lokalen Umgebung darf diese Option nicht aktiviert werden. 

1. Schulen Sie Ihre Benutzer.
   * Die folgenden Abschnitte dieses Dokuments behandeln Beispielkommunikation, Kennwortportale, Erzwingung der Registrierung und das Eintragen von Authentifizierungsdaten.
   * Die Azure Active Directory-Produktgruppe hat einen [Plan für die schrittweise Bereitstellung](https://aka.ms/SSPRDeploymentPlan) entwickelt, den Organisationen zusammen mit der Dokumentation auf dieser Website dazu verwenden können, einen Geschäftsvorgang zu erstellen und die Bereitstellung der Self-Service-Kennwortzurücksetzung zu planen.
1. Aktivieren Sie die Self-Service-Kennwortzurücksetzung für Ihre gesamte Organisation.
   * Wenn Sie bereit sind, aktivieren Sie die Kennwortzurücksetzung für alle Benutzer, indem Sie den Schalter **Self-Service-Kennwortzurücksetzung aktiviert** auf **Alle** festlegen.

## <a name="sample-communication"></a>Beispielkommunikation

Viele Kunden sind der Meinung, dass eine E-Mail-Kampagne mit einfachen Anweisungen der einfachste Weg ist, um Benutzer über die Nutzung von SSPR zu informieren. [Wir haben einfache E-Mails und sonstige Begleitmaterialien erstellt, die Sie beim Rollout als Vorlagen verwenden können](https://www.microsoft.com/download/details.aspx?id=56768):

* **In Kürze verfügbar:** Diese E-Mail-Vorlage kann in den Wochen bzw. Tagen vor dem Rollout verwendet werden, um Benutzer darüber zu informieren, dass sie in Kürze aktiv werden müssen.
* **Jetzt verfügbar:** Diese E-Mail-Vorlage kann am Tag der Programmeinführung verwendet werden, um Benutzer zum Registrieren und Bestätigen ihrer Authentifizierungsdaten zu bewegen. Wenn Benutzer sich jetzt registrieren, ist SSPR bei Bedarf für sie verfügbar.
* **Erinnerung an Registrierung:** Diese E-Mail-Vorlage kann einige Tage bzw. Wochen nach der Bereitstellung verwendet werden, um Benutzer an das Registrieren und Bestätigen ihrer Authentifizierungsdaten zu erinnern.
* **SSPR-Poster:** Poster, die Sie anpassen und in Ihrer Organisation in den Tagen und Wochen vor und nach Ihrem Rollout aufhängen können.
* **SSPR-Tischkarten:** Tischkarten, die Sie in der Kantine, in Konferenzräumen oder auf Schreibtischen aufstellen können, um Ihre Benutzer zu animieren, die Registrierung abzuschließen.
* **SSPR-Aufkleber:** Aufklebervorlagen, die Sie anpassen, drucken und auf Laptops, Monitore, Tastaturen oder Mobiltelefone kleben können, um den Zugriff auf SSPR zu beschreiben.

![SSPR-E-Mail-Beispiele für die Bereitstellung für Benutzer][Email]

## <a name="create-your-own-password-portal"></a>Erstellen eines eigenen Kennwortportals

Viele Kunden entscheiden sich für das Hosten einer Webseite und das Erstellen eines DNS-Stammeintrags, beispielsweise https://passwords.contoso.com. Sie füllen diese Seite mit Links zu den folgenden Informationen auf:

* [Portal für die Azure AD-Kennwortzurücksetzung: https://aka.ms/sspr](https://aka.ms/sspr)
* [Registrierungsportal für die Azure AD-Kennwortzurücksetzung: https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)
* [Portal für die Azure AD-Kennwortänderung: https://account.activedirectory.windowsazure.com/ChangePassword.aspx](https://account.activedirectory.windowsazure.com/ChangePassword.aspx)
* Weitere organisationsspezifische Informationen

Sie können in allen E-Mails oder auf Flyern eine einprägsame Marken-URL angeben, unter der Benutzer sämtliche Informationen zur Nutzung der Dienste finden. Wir haben eine [Beispielseite für die Kennwortzurücksetzung](https://github.com/ajamess/password-reset-page) erstellt, die Sie verwenden und entsprechend den Anforderungen Ihrer Organisation anpassen können.

## <a name="use-enforced-registration"></a>Verwenden der erzwungenen Registrierung

Wenn Sie möchten, dass sich Ihre Benutzer für die Kennwortzurücksetzung registrieren, können Sie die Registrierung bei der Anmeldung über Azure AD erzwingen. Sie können diese Option im Bereich **Kennwortzurücksetzung** Ihres Verzeichnisses aktivieren, indem Sie auf der Registerkarte **Registrierung** die Option **Registrierung von Benutzern bei der Anmeldung verlangen?** aktivieren.

Administratoren können auch festlegen, dass sich Benutzer nach einem bestimmten Zeitraum erneut registrieren müssen. Sie können **Anzahl der Tage, bevor Benutzer aufgefordert werden, ihre Authentifizierungsinformationen erneut zu bestätigen** auf 0 bis 730 Tage festlegen.

Nachdem Sie diese Option aktiviert haben, sehen Benutzer bei der Anmeldung eine Meldung mit dem Hinweis, dass ihr Administrator die Bestätigung ihrer Authentifizierungsinformationen verlangt.

## <a name="populate-authentication-data"></a>Auffüllen von Authentifizierungsdaten

Sie sollten erwägen, [einige Authentifizierungsdaten für Ihre Benutzer vorab auszufüllen](howto-sspr-authenticationdata.md). Auf diese Weise müssen sich Benutzer erst dann für die Kennwortzurücksetzung registrieren, wenn sie SSPR verwenden können. Solange Benutzer die Authentifizierungsdaten angeben, die der von Ihnen definierten Richtlinie für die Kennwortzurücksetzung entsprechen, können sie ihre Kennwörter zurücksetzen.

## <a name="disable-self-service-password-reset"></a>Deaktivieren der Self-Service-Kennwortzurücksetzung

Wenn Ihre Organisation entscheidet, die Self-Service-Kennwortzurücksetzung zu deaktivieren, dann ist dies ganz einfach. Öffnen Sie Ihren Azure AD-Mandanten, und navigieren Sie zu **Kennwortzurücksetzung** > **Eigenschaften**, und wählen Sie unter **Self-Service-Kennwortzurücksetzung aktiviert** die Option **Keine**. Benutzer behalten weiterhin ihre registrierten Authentifizierungsmethoden für die zukünftige Verwendung bei.

## <a name="next-steps"></a>Nächste Schritte

* [Zurücksetzen oder Ändern des Kennworts](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registrieren für die Self-Service-Kennwortzurücksetzung](../user-help/active-directory-passwords-reset-register.md)
* Lesen Sie außerdem, wie Sie die [zusammengeführte Registrierung für Azure Multi-Factor Authentication und die Self-Service-Kennwortzurücksetzung in Azure AD aktivieren](concept-registration-mfa-sspr-converged.md).
* [Lizenzanforderungen für Azure AD-Self-Service-Kennwortzurücksetzung](concept-sspr-licensing.md)
* [Bereitstellen der Kennwortzurücksetzung ohne erforderliche Endbenutzerregistrierung](howto-sspr-authenticationdata.md)
* [Kennwortrichtlinien und -einschränkungen in Azure Active Directory](concept-sspr-policy.md)
* [Übersicht über die Kennwortrückschreibung](howto-sspr-writeback.md)
* [Berichterstellungsoptionen für die Kennwortverwaltung von Azure AD](howto-sspr-reporting.md)
* [Welche Optionen sind für SSPR verfügbar, und was bedeuten sie?](concept-sspr-howitworks.md)
* [Anscheinend ist ein Fehler aufgetreten. Wie behebe ich Probleme mit SSPR?](active-directory-passwords-troubleshoot.md)
* [Ich habe eine Frage, die nicht an einer anderen Stelle abgedeckt wurde.](active-directory-passwords-faq.md)

[Email]: ./media/howto-sspr-deployment/sspr-emailtemplates.png "Passen Sie diese E-Mail-Vorlagen den Anforderungen Ihrer Organisation entsprechend an."
