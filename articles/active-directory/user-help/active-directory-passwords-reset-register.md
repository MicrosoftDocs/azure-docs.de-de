---
title: Registrieren für die Self-Service-Kennwortzurücksetzung – Azure Active Directory | Microsoft-Dokumentation
description: Registrieren von Authentifizierungsdaten für die Self-Service-Kennwortzurücksetzung von Azure AD
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 01/11/2018
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: f113c42ff45811f31eb77a92873fb187dd51ea6b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60530895"
---
# <a name="register-for-self-service-password-reset"></a>Registrieren für die Self-Service-Kennwortzurücksetzung

> [!IMPORTANT]
> Haben Sie Probleme bei der Anmeldung? Dann lesen Sie unter [Zurücksetzen des Kennworts eines Geschäfts-, Schul- oder Unikontos](active-directory-passwords-update-your-own-password.md) weiter.

Endbenutzer können ihr Kennwort mithilfe der Self-Service-Kennwortzurücksetzung (Self-Service Password Reset, SSPR) von Azure Active Directory (Azure AD) selbstständig zurücksetzen oder ihr Konto entsperren. Um diese Funktion verwenden zu können, müssen Sie zunächst Ihre Authentifizierungsmethoden registrieren oder die vordefinierten Authentifizierungsmethoden bestätigen, die Ihr Administrator angegeben hat.

## <a name="register-or-confirm-authentication-data-with-sspr"></a>Registrieren oder Bestätigen von Authentifizierungsdaten für die Self-Service-Kennwortzurücksetzung

1. Öffnen Sie auf Ihrem Gerät den Webbrowser, und rufen Sie die [Registrierungsseite für die Kennwortzurücksetzung](https://aka.ms/ssprsetup) auf.
2. Geben Sie Ihren vom Administrator bereitgestellten Benutzernamen und das dazugehörige Kennwort ein.
3. Abhängig von der Konfiguration durch Ihr IT-Team können einige der folgenden Optionen konfiguriert und überprüft werden. Wenn der Administrator von Ihnen zur Verwendung Ihrer Informationen autorisiert wurde, kann er einige der Informationen für Sie auffüllen.
    * **Bürotelefon**: Diese Option kann nur vom Administrator festgelegt werden.
    * **Authentifizierungstelefon**: Legen Sie diese Option auf eine andere Telefonnummer fest, unter der Sie erreichbar sind. Ein Beispiel wäre ein Mobiltelefon, das Textnachrichten oder Anrufe empfangen kann.
    * **E-Mail für Authentifizierung**: Legen Sie diese Option auf eine alternative E-Mail-Adresse fest, auf die Sie zugreifen können, ohne das zurückzusetzende Kennwort zu verwenden.
    * **Sicherheitsfragen**: Diese Liste mit Fragen wurde vom Administrator zur Beantwortung freigegeben. Fragen und Antworten dürfen nicht mehrmals verwendet werden.
4. Geben Sie die vom Administrator angeforderten Informationen an, und überprüfen Sie sie. Sollten mehrere Optionen verfügbar sein, empfiehlt es sich, mehrere Methoden zu registrieren. Dadurch sind Sie flexibel, falls eine der Methoden nicht verfügbar ist – beispielsweise, wenn Sie unterwegs und nicht über Ihr Bürotelefon erreichbar sind.

    ![Registrieren von Authentifizierungsmethoden und Klicken auf „Fertig stellen“][Register]

5. Klicken Sie auf **Fertig stellen**. SSPR steht Ihnen nun bei Bedarf zur Verfügung.

Für **Authentifizierungstelefon** oder **E-Mail für Authentifizierung** eingegebene Daten werden im globalen Verzeichnis nicht angezeigt. Diese Daten sind nur für Sie selbst und für Ihre Administratoren sichtbar. Die Antworten auf Ihre Sicherheitsfragen sind nur für Sie selbst sichtbar.

Unter Umständen werden Sie von den Administratoren nach einer Weile zur Bestätigung Ihrer Authentifizierungsmethoden aufgefordert, um sicherzustellen, dass die registrierten Methoden immer noch angemessen sind.

## <a name="common-problems-and-their-solutions"></a>Häufige Probleme und Lösungen

 Hier finden Sie einige häufige Fehler und die passenden Lösungen:

| Fehlerbeschreibung| Welcher Fehler wird angezeigt?| Lösung |
| --- | --- | --- |
| Nach der Eingabe meiner Benutzer-ID wird mir die Seite „Wenden Sie sich an Ihren Administrator“ angezeigt. | Wenden Sie sich an Ihren Administrator. <br> <br> Es wurde ermittelt, dass das Kennwort Ihres Benutzerkontos nicht von Microsoft verwaltet wird. Ihr Kennwort kann daher nicht automatisch zurückgesetzt werden. <br> <br> Weitere Unterstützung erhalten Sie vom IT-Team. | Diese Meldung wird angezeigt, weil Ihr IT-Team Ihr Kennwort in Ihrer lokalen Umgebung verwaltet und das Zurücksetzen des Kennworts über den Link **Zugriff auf Ihr Konto nicht möglich** nicht gestattet ist. <br> <br> Wenn Sie Ihr Kennwort zurücksetzen möchten, wenden Sie sich direkt an das IT-Team, um Unterstützung zu erhalten. Teilen Sie dem Team mit, dass Sie Ihr Kennwort zurücksetzen möchten, damit es dieses Feature für Sie aktivieren kann.|
| Nach dem Eingeben meiner Benutzer-ID erhalte ich die Fehlermeldung „Ihr Konto ist für ein Zurücksetzen des Kennworts nicht aktiviert“. | Ihr Konto ist nicht für die Kennwortzurücksetzung aktiviert. <br> <br> Ihr IT-Team hat Ihr Konto nicht zur Verwendung mit diesem Dienst eingerichtet. <br> <br> Wenn Sie möchten, können wir einen Administrator in Ihrer Organisation kontaktieren, um Ihr Kennwort für Sie zurückzusetzen. | Diese Meldung wird angezeigt, weil Ihr IT-Team die Kennwortzurücksetzung über den Link **Zugriff auf Ihr Konto nicht möglich** für Ihre Organisation nicht aktiviert oder Ihnen keine Berechtigung für die Nutzung dieses Features erteilt hat. <br> <br> Klicken Sie zum Zurücksetzen Ihres Kennworts auf den Link zur **Kontaktaufnahme mit einem Administrator**. Daraufhin wird eine E-Mail an das IT-Team Ihres Unternehmens gesendet. Die E-Mail informiert das Team darüber, dass Sie Ihr Kennwort zurücksetzen möchten, damit es dieses Feature für Sie aktivieren kann. |
| Nach dem Eingeben meiner Benutzer-ID erhalte ich die Fehlermeldung „Das Konto konnte nicht überprüft werden“. | Das Konto konnte nicht überprüft werden. <br> <br> Wenn Sie möchten, können wir einen Administrator in Ihrer Organisation kontaktieren, um Ihr Kennwort für Sie zurückzusetzen. | Diese Meldung wird angezeigt, weil die Kennwortzurücksetzung für Sie zwar aktiviert ist, Sie sich aber nicht für die Nutzung dieses Diensts registriert haben. Wenn Sie sich für die Kennwortzurücksetzung registrieren möchten, rufen Sie die [Registrierungsseite für die Kennwortzurücksetzung](https://aka.ms/ssprsetup) auf, sobald Sie wieder auf Ihr Konto zugreifen können. <br> <br> Klicken Sie zum Zurücksetzen Ihres Kennworts auf den Link zur **Kontaktaufnahme mit einem Administrator**, um eine E-Mail an das IT-Team Ihres Unternehmens zu senden. |

## <a name="next-steps"></a>Nächste Schritte

* [Ändern Ihres Kennworts mithilfe der Self-Service-Kennwortzurücksetzung](active-directory-passwords-update-your-own-password.md)
* [Registrierungsseite für die Kennwortzurücksetzung](https://aka.ms/ssprsetup)
* [Kennwortzurücksetzungsportal](https://passwordreset.microsoftonline.com/)
* Falls Sie sich nicht bei Ihrem Microsoft-Konto anmelden können, klicken Sie [hier](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).

[Register]: ./media/active-directory-passwords-reset-register/register-2-methods.png "Registrierungsseite für die Kennwortzurücksetzung mit registrierten Methoden und der Schaltfläche „Fertig stellen“"

