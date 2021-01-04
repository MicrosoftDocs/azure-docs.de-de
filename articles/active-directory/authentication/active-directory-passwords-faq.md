---
title: Häufig gestellte Fragen zur Self-Service-Kennwortzurücksetzung – Azure Active Directory
description: Häufig gestellte Fragen zur Self-Service-Kennwortzurücksetzung in Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/20/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0874f6aeb74b028e92e10d972757cc5971735e39
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96744311"
---
# <a name="self-service-password-reset-frequently-asked-questions"></a>Häufig gestellte Fragen zur Self-Service-Kennwortzurücksetzung

Im Folgenden sind einige häufig gestellte Fragen (FAQ) rund um die Self-Service-Kennwortzurücksetzung aufgeführt.

Wenn Sie eine allgemeine Frage zu Azure Active Directory (Azure AD) und zur Self-Service-Kennwortzurücksetzung (Self-Service Password Reset, SSPR) haben, die hier nicht beantwortet wird, können Sie die Community auf der [Microsoft-Frageseite „Q&A“ für Azure Active Directory](/answers/topics/azure-active-directory.html) um Unterstützung bitten. Zu den Mitgliedern der Community gehören Techniker, Produktmanager MVPs und andere IT-Experten.

Diese FAQ sind in folgende Abschnitte unterteilt:

* [Fragen zum Registrieren für die Kennwortzurücksetzung](#password-reset-registration)
* [Fragen zum Zurücksetzen des Kennworts](#password-reset)
* [Fragen zum Ändern des Kennworts](#password-change)
* [Fragen zu Kennwortverwaltungsberichten](#password-management-reports)
* [Fragen zur Kennwortrückschreibung](#password-writeback)

## <a name="password-reset-registration"></a>Registrieren für die Kennwortzurücksetzung

* **F:  Können meine Benutzer ihre eigenen Daten zur Kennwortzurücksetzung registrieren?**

  > **A:** Ja. Wenn die Kennwortzurücksetzung aktiviert ist und die Benutzer lizenziert sind, können diese das Kennwortregistrierungsportal (https://aka.ms/ssprsetup) öffnen und ihre Authentifizierungsdaten registrieren. Benutzer können sich auch über den Zugriffsbereich (https://myapps.microsoft.com) registrieren. Bei der Registrierung über den Zugriffsbereich müssen die Benutzer ihr Profilbild auswählen. Anschließend wählen sie **Profil** und dann die Option **Für das Zurücksetzen des Kennworts registrieren** aus.
  >
  > Wenn Sie die [kombinierte Registrierung](concept-registration-mfa-sspr-combined.md) aktivieren, können sich Benutzer gleichzeitig für SSPR und Azure AD Multi-Factor Authentication registrieren.
* **F:  Wenn ich Kennwortzurücksetzung für eine Gruppe aktiviere und mich anschließend entscheide, sie für alle zu aktivieren, müssen sich meine Benutzer dann erneut registrieren?**

  > **A:** Nein. Benutzer mit aufgefüllten Authentifizierungsdaten müssen sich nicht erneut registrieren.
  >
  >
* **F:  Kann ich im Namen meiner Benutzer Daten zur Kennwortzurücksetzung definieren?**

  > **A:** Ja, Sie können dies mit Azure AD Connect, PowerShell, dem [Azure-Portal](https://portal.azure.com) oder dem [Microsoft 365 Admin Center](https://admin.microsoft.com) durchführen. Weitere Informationen finden Sie unter [Von der Azure AD Self-Service-Kennwortzurücksetzung verwendete Daten](howto-sspr-authenticationdata.md).
  >
  >
* **F:  Kann ich Daten für Sicherheitsfragen vom lokalen Standort aus synchronisieren?**

  > **A:** Nein, dies ist zurzeit noch nicht möglich.
  >
  >
* **F:  Können meine Benutzer Daten so registrieren, dass anderen Benutzern diese Daten nicht angezeigt werden?**

  > **A:** Ja. Wenn Benutzer Daten über das Registrierungsportal für die Kennwortzurücksetzung registrieren, werden die Daten in privaten Authentifizierungsfeldern gespeichert, die nur für globale Administratoren und den Benutzer selbst sichtbar sind.
  >
  >
* **F:  Müssen meine Benutzer registriert werden, damit sie die Kennwortzurücksetzung verwenden können?**

  > **A:** Nein. Wenn Sie genügend Authentifizierungsinformationen in ihrem Auftrag definieren, müssen Benutzer sich nicht registrieren. Das Zurücksetzen des Kennworts funktioniert, solange Sie in den entsprechenden Feldern im Verzeichnis ordnungsgemäß formatierte Daten gespeichert haben.
  >
  >
* **F:  Kann ich die Felder „Telefon für Authentifizierung“, „E-Mail-Adresse zur Authentifizierung“ oder „Alternatives Telefon für Authentifizierung“ im Namen meiner Benutzer synchronisieren oder festlegen?**

  > **A:** Die Felder, die von einem globalen Administrator festgelegt werden können, sind im Artikel mit den [Datenanforderungen von SSPR](howto-sspr-authenticationdata.md) definiert.
  >
  >
* **F:  Wie bestimmt das Registrierungsportal, welche Optionen meinen Benutzern angezeigt werden sollen?**

  > **A:** Das Registrierungsportal für die Kennwortzurücksetzung zeigt nur die Optionen an, die Sie für Ihre Benutzer aktiviert haben. Diese Optionen finden Sie im Abschnitt **Richtlinie zum Zurücksetzen des Benutzerkennworts** auf der Registerkarte **Konfigurieren** Ihres Verzeichnisses. Wenn Sie also z. B. die Sicherheitsfragen nicht aktivieren, können Benutzer sich für diese Option nicht registrieren.
  >
  >
* **F:  Wann gilt ein Benutzer als registriert?**

  > **A:** Ein Benutzer gilt als für SSPR registriert, wenn er mindestens die **Anzahl von Methoden, die zum Zurücksetzen eines Kennworts erforderlich sind**, registriert hat, die Sie im [Azure-Portal](https://portal.azure.com) festgelegt haben.
  >
  >

## <a name="password-reset"></a>Zurücksetzen von Kennwörtern

* **F:  Verhindern Sie, dass Benutzer in kurzer Zeit mehrfach versuchen, ein Kennwort zurückzusetzen?**

  > **A:** Ja, es wurden Sicherheitsfeatures in die Kennwortzurücksetzung integriert, um Schutz vor Missbrauch zu bieten. 
  >
  > Benutzer dürfen innerhalb von 24 Stunden nur fünfmal versuchen, das Kennwort zurückzusetzen, bevor sie für 24 Stunden gesperrt werden. 
  >
  > Benutzer können innerhalb einer Stunde nur fünfmal versuchen, eine Telefonnummer zu überprüfen, eine SMS zu senden oder Sicherheitsfragen und -antworten zu überprüfen, bevor sie für 24 Stunden gesperrt werden. 
  >
  > Benutzer können eine E-Mail maximal 10 Mal innerhalb von 10 Minuten versenden, bevor sie für 24 Stunden gesperrt werden.
  >
  > Die Zähler werden zurückgesetzt, sobald ein Benutzer sein Passwort zurücksetzt.
  >
  >
* **F:  Wie lange muss ich warten, bis eine E-Mail, eine SMS oder ein Anruf von der Kennwortzurücksetzung eintrifft?**

  > **A:** E-Mails, SMS-Nachrichten und Anrufe sollten in weniger als einer Minute eingehen. Im Normalfall dauert es zwischen 5 und 20 Sekunden.
  > Wenn Sie in diesem Zeitraum die Benachrichtigung nicht erhalten:
  > * Überprüfen Sie Ihren Ordner mit Junk-E-Mails.
  > * Überprüfen Sie, ob es sich bei der kontaktierten Nummer oder E-Mail-Adresse um die erwartete Nummer bzw. E-Mail-Adresse handelt.
  > * Überprüfen Sie, ob die Authentifizierungsdaten im Verzeichnis richtig formatiert sind, z.B. „+1 4255551234“ oder *user\@contoso.com*. 
* **F:  Welche Sprachen werden von der Kennwortzurücksetzung unterstützt?**

  > **A:** Die Benutzeroberfläche für die Kennwortzurücksetzung, SMS-Nachrichten und Sprachanrufe wurden in dieselben Sprachen lokalisiert, die in Microsoft 365 unterstützt werden.
  >
  >
* **F:  Welche Teile der Oberfläche zur Kennwortzurücksetzung werden angepasst, wenn ich die organisationsspezifischen Brandingelemente auf der Registerkarte „Konfigurieren“ für mein Verzeichnis festlege?**

  > **A:** Das Portal für die Kennwortzurücksetzung zeigt das Logo Ihrer Organisation und erlaubt Ihnen zudem, den Link „Wenden Sie sich an Ihren Administrator“ so zu konfigurieren, dass er auf eine benutzerdefinierte E-Mail-Adresse oder eine URL verweist. E-Mails, die von der Kennwortzurücksetzung gesendet werden, enthalten das Logo, die Farben sowie den Namen Ihrer Organisation im Nachrichtentext der E-Mail. Zudem werden sie über die Einstellungen für diesen bestimmten Namen angepasst.
  >
  >
* **F:  Wie informiere ich meine Benutzer, wo sie ihre Kennwörter zurücksetzen können?**

  > **A:** Nutzen Sie die Vorschläge in unserem [Artikel zur SSPR-Bereitstellung](howto-sspr-deployment.md#plan-communications).
  >
  >
* **F:  Kann ich diese Seite von einem mobilen Gerät aus verwenden?**

  > **A:** Ja, diese Seite funktioniert auf mobilen Geräten.
  >
  >
* **F:  Wird das Entsperren lokaler Active Directory-Konten unterstützt, wenn Benutzer ihre Kennwörter zurücksetzen?**

  > **A:** Ja. Wenn ein Benutzer sein Kennwort zurücksetzt und das Kennwortrückschreiben mit Azure AD Connect bereitgestellt wurde, wird das Konto des Benutzers automatisch entsperrt, wenn das Kennwort zurückgesetzt wird.
  >
  >
* **F:  Wie kann ich die Kennwortzurücksetzung direkt in die Anmeldeoberfläche auf den Desktops meiner Benutzer integrieren?**

  > **A:** Wenn Sie ein Azure AD Premium-Kunde sind, können Sie Microsoft Identity Manager ohne zusätzliche Kosten installieren und die Lösung zur lokalen Kennwortzurücksetzung bereitstellen.
  >
  >
* **F:  Kann ich für verschiedene Gebietsschemas unterschiedliche Sicherheitsfragen festlegen?**

  > **A:** Nein, dies ist zurzeit noch nicht möglich.
  >
  >
* **F:  Wie viele Fragen kann ich für die Authentifizierungsoption mit Sicherheitsfragen konfigurieren?**

  > **A:** Sie können bis zu 20 benutzerdefinierte Sicherheitsfragen im [Azure-Portal](https://portal.azure.com) konfigurieren.
  >
  >
* **F:  Wie lang können die Sicherheitsfragen sein?**

  > **A:** Sicherheitsfragen können zwischen 3 und 200 Zeichen umfassen.
  >
  >
* **F:  Wie lang können die Antworten auf Sicherheitsfragen sein?**

  > **A:** Antworten können 3 bis 40 Zeichen lang sein.
  >
  >
* **F:  Werden doppelte Antworten auf Sicherheitsfragen abgelehnt?**

  > **A:** Ja, doppelte Antworten auf Sicherheitsfragen werden abgelehnt.
  >
  >
* **F:  Kann ein Benutzer sich für dieselbe Sicherheitsfrage mehrmals registrieren?**

  > **A:** Nein. Sobald ein Benutzer sich für eine bestimmte Frage registriert, kann er sich kein zweites Mal für dieselbe Frage registrieren.
  >
  >
* **F:  Ist es möglich, eine Mindestanzahl von Sicherheitsfragen für die Registrierung und Zurücksetzung festzulegen?**

  > **A:** Ja, es kann ein Grenzwert für die Registrierung und ein anderer für die Zurücksetzung festgelegt werden. Für die Registrierung können drei bis fünf Sicherheitsfragen erforderlich sein. Für die Zurücksetzung können drei bis fünf Fragen erforderlich sein.
  >
  >
* **F:  Ich konfigurierte meine Richtlinie so, dass Benutzer zum Zurücksetzen Sicherheitsfragen verwenden müssen, aber die Azure-Administratoren scheinen anders konfiguriert zu sein.**

  > **A:** Dies entspricht dem erwarteten Verhalten. Microsoft erzwingt eine starke standardmäßige Zwei-Tor-Kennwortzurücksetzungs-Richtlinie für jede Azure-Administratorrolle. Damit wird verhindert, dass Administratoren Sicherheitsfragen verwenden. Weitere Informationen zu dieser Richtlinie finden Sie im Artikel [Kennwortrichtlinien und -Einschränkungen in Azure Active Directory](concept-sspr-policy.md).
  >
  >
* **F:  Wie werden die Sicherheitsfragen während der Zurücksetzung ausgewählt, wenn ein Benutzer mehr als die maximale Anzahl von Fragen registriert, die für die Zurücksetzung erforderlich sind?**

  > **A:** Aus der Gesamtzahl von Fragen, für die sich ein Benutzer registriert hat, werden *N* Sicherheitsfragen nach dem Zufallsprinzip ausgewählt, wobei *N* für die **Anzahl der für die Zurücksetzung erforderlichen Fragen** steht. Wenn ein Benutzer z. B. fünf Sicherheitsfragen registriert hat, aber nur drei für das Zurücksetzen eines Kennworts erforderlich sind, werden drei der fünf Fragen zufällig ausgewählt und beim Zurücksetzen angezeigt. Wenn der Benutzer die Fragen falsch beantwortet, wird der Auswahlvorgang erneut durchgeführt, um eine wiederholte Fragestellung zu verhindern.
  >
  >
* **F:  Wie lange ist der Einmalpasscode per E-Mail und SMS gültig?**

  > **A:** Die Sitzungsdauer für die Kennwortzurücksetzung beträgt 15 Minuten. Ab dem Beginn des Vorgangs zur Kennwortzurücksetzung hat der Benutzer 15 Minuten Zeit, das Kennwort zurückzusetzen. Der Einmalpasscode per E-Mail oder SMS ist während der Sitzung der Kennwortzurücksetzung 5 Minuten lang gültig.
  >
  >
* **F:  Kann ich das Zurücksetzen von Kennwörtern durch Benutzer blockieren?**

  > **A:** Ja, wenn Sie eine Gruppe verwenden, um SSPR zu aktivieren, können Sie einen einzelnen Benutzer aus der Gruppe entfernen, die Benutzern erlaubt, ihr Kennwort zurückzusetzen. Wenn der Benutzer ein globaler Administrator ist, behält er die Möglichkeit, sein Kennwort zurückzusetzen, und dies kann nicht deaktiviert werden.
  >
  >

## <a name="password-change"></a>Kennwortänderung

* **F:  Wo können meine Benutzer ihre Kennwörter ändern?**

  > **A:** Benutzer können ihre Kennwörter überall dort ändern, wo ihr Profilbild oder -symbol angezeigt wird (beispielsweise im [Office 365](https://portal.office.com)-Portal oder im [Zugriffsbereich](https://myapps.microsoft.com) in der rechten oberen Ecke). Benutzer können ihre Kennwörter über die [Profilseite des Zugriffsbereichs](https://account.activedirectory.windowsazure.com/r#/profile) ändern. Außerdem werden Benutzer unter Umständen auf der Azure AD-Anmeldeseite zum Ändern ihres Kennworts aufgefordert, falls ihr Kennwort abgelaufen ist. Und schließlich können Benutzer direkt zum [Kennwortänderungsportal von Azure AD](https://account.activedirectory.windowsazure.com/ChangePassword.aspx) navigieren, wenn sie ihr Kennwort ändern möchten.
  >
  >
* **F:  Können meine Benutzer im Office-Portal benachrichtigt werden, wenn ihr lokales Kennwort abläuft?**

  > **A:** Ja, das ist möglich, wenn Sie die Active Directory-Verbunddienste (AD FS) verwenden. Wenn Sie AD FS verwenden, befolgen Sie die Anweisungen im Artikel [Senden von Kennwortrichtlinienansprüchen mit AD FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-to-send-password-expiry-claims?f=255&MSPPError=-2147217396). Wenn Sie die Kennworthashsynchronisierung verwenden, ist dies derzeit nicht möglich. Da wir keine Kennwortrichtlinien aus lokalen Verzeichnissen synchronisieren, können wir auch keine Ablaufbenachrichtigungen in Cloudumgebungen veröffentlichen. In beiden Fällen können Sie [Benutzer, deren Kennwörter demnächst ablaufen, auch über PowerShell benachrichtigen](https://social.technet.microsoft.com/wiki/contents/articles/23313.notify-active-directory-users-about-password-expiry-using-powershell.aspx).
  >
  >
* **F:  Kann ich das Ändern von Kennwörtern durch Benutzer blockieren?**

  > **A:** Für reine Cloudbenutzer können Kennwortänderungen nicht blockiert werden. Für lokale Benutzer können Sie die Option **Benutzer kann Kennwort nicht ändern** festlegen. Die ausgewählten Benutzer können ihr Kennwort nicht ändern.
  >
  >

## <a name="password-management-reports"></a>Berichte zur Kennwortverwaltung

* **F:  Wie lange dauert es, bis Daten in den Berichten zur Kennwortverwaltung angezeigt werden?**

  > **A:** Daten sollten innerhalb von 5 bis 10 Minuten in den Berichten zur Kennwortverwaltung angezeigt werden. In manchen Fällen kann es bis zu einer Stunde dauern.
  >
  >
* **F:  Wie kann ich die Berichte zur Kennwortverwaltung filtern?**

  > **A:** Wählen Sie die kleine Lupe ganz rechts neben den Spaltenbeschriftungen oben im Bericht aus, um Berichte zur Kennwortverwaltung zu filtern. Wenn Sie umfangreichere Filterfunktionen benötigen, können Sie den Bericht in Excel exportieren und eine Pivottabelle erstellen.
  >
  >
* **F: Wie hoch ist die maximale Anzahl der Ereignisse, die in den Kennwortverwaltungsberichten gespeichert werden?**

  > **A:** In den Kennwortverwaltungsberichten werden bis zu 75.000 Kennwortzurücksetzungsereignisse oder Ereignisse im Zusammenhang mit der Registrierung für die Kennwortzurücksetzung für einen Zeitraum von bis zu 30 Tagen gespeichert. Eine Erweiterung dieser Anzahl der Ereignisse ist in Arbeit.
  >
  >
* **F:  Wie weit reichen die Berichte zur Kennwortverwaltung zurück?**

  > **A:** Die Berichte zur Kennwortverwaltung zeigen Vorgänge der letzten 30 Tage an. Wenn Sie diese Daten archivieren möchten, können Sie die Berichte in regelmäßigen Abständen herunterladen und an einem anderen Speicherort speichern.
  >
  >
* **F:  Gibt es eine maximale Anzahl von Zeilen, die in den Berichten zur Kennwortverwaltung angezeigt werden können?**

  > **A:** Ja. Jeder der Berichte zur Kennwortverwaltung kann maximal 75.000 Zeilen umfassen, unabhängig davon, ob sie auf der Benutzeroberfläche angezeigt oder heruntergeladen werden.
  >
  >
* **F:  Gibt es eine API, um auf die Daten der Kennwortzurücksetzung oder des Registrierens für die Kennwortzurücksetzung zuzugreifen?**

  > **A:** Ja. Informationen dazu, wie Sie auf die Berichtsdaten zur Kennwortzurücksetzung zugreifen können, finden Sie in der [Referenz zur Azure Log Analytics-REST-API](/rest/api/loganalytics/).
  >
  >

## <a name="password-writeback"></a>Kennwortrückschreiben

* **F:  Wie funktioniert das Kennwortrückschreiben im Detail?**

  > **A:** Im Artikel [Funktionsweise des Kennwortrückschreibens](./tutorial-enable-sspr-writeback.md) wird erläutert, was geschieht, wenn Sie das Kennwortrückschreiben aktivieren, und wie Daten durch das System wieder in Ihre lokale Umgebung übertragen werden.
  >
  >
* **F:  Wie lange dauert es, bis das Kennwortrückschreiben funktioniert? Gibt es eine Synchronisierungsverzögerung wie bei der Kennworthashsynchronisierung?**

  > **A:** Das Kennwortrückschreiben erfolgt sofort. Sie ist eine synchrone Pipeline, die grundlegend anders funktioniert als die Kennworthashsynchronisierung. Durch das Kennwortrückschreiben erhalten Benutzer in Echtzeit Feedback über den Erfolg der Kennwortzurücksetzung oder -änderung. Die durchschnittliche Zeit für das erfolgreiche Rückschreiben eines Kennworts liegt bei unter 500 ms.
  >
  >
* **F:  Welchen Einfluss hat es auf mein Cloudkonto und meinen Cloudzugriff, wenn mein lokales Konto deaktiviert ist?**

  > **A:** Wenn Ihre lokale ID deaktiviert ist, werden auch Ihre Cloud-ID und Ihr Cloudzugriff beim nächsten Synchronisierungsintervall über Azure AAD Connect deaktiviert. Standardmäßig erfolgt diese Synchronisierung alle 30 Minuten.
  >
  >
* **F:  Wenn mein lokales Konto von lokalen Active Directory-Kennwortrichtlinien eingeschränkt wird, beachtet SSPR diese Richtlinien bei meiner Kennwortänderung?**

  > **A:** Ja. SSPR beachtet und befolgt die lokale Active Directory-Kennwortrichtlinie. Diese Richtlinie umfasst die typische Active Directory-Domänenkennwortrichtlinie sowie alle definierten Kennwortrichtlinien mit feiner Abstufung, die auf einen Benutzer ausgerichtet sind.
  >
  >
* **F:  Für welche Arten von Konten funktioniert das Kennwortrückschreiben?**

  > **A:** Das Kennwortrückschreiben kann für Benutzerkonten verwendet werden, die aus dem lokalen Active Directory mit Azure AD synchronisiert werden. Dies schließt Verbundbenutzer, Benutzer mit Kennworthashsynchronisierung und Benutzer mit Pass-Through-Authentifizierung ein.
  >
  >
* **F:  Werden die Kennwortrichtlinien meiner Domäne durch die Kennwortrückschreibung durchgesetzt?**

  > **A:** Ja. Die Kennwortrückschreibung setzt das Kennwortalter, den Verlauf, die Komplexität, Filter und alle anderen Einschränkungen durch, die Sie in Ihrer lokalen Domäne für Kennwörter festlegen können.
  >
  >
* **F:  Ist die Kennwortrückschreibung sicher?  Wie kann ich sicher sein, dass ich nicht gehackt werde?**

  > **A:** Ja, das Kennwortrückschreiben ist sicher. Weitere Informationen zu den mehrere Sicherheitsstufen, die durch den Dienst zum Kennwortrückschreiben implementiert werden, finden Sie im Abschnitt [Sicherheit für das Kennwortrückschreiben](concept-sspr-writeback.md#password-writeback-security) im Artikel [Übersicht über die Kennwortrückschreibung](./tutorial-enable-sspr-writeback.md).
  >
  >

## <a name="next-steps"></a>Nächste Schritte

* [Erfolgreiches Rollout der Self-Service-Kennwortzurücksetzung](howto-sspr-deployment.md)
* [Zurücksetzen oder Ändern des Kennworts](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registrieren für die Self-Service-Kennwortzurücksetzung](../user-help/active-directory-passwords-reset-register.md)
* [Lizenzanforderungen für Azure AD-Self-Service-Kennwortzurücksetzung](concept-sspr-licensing.md)
* [Bereitstellen der Kennwortzurücksetzung ohne erforderliche Endbenutzerregistrierung](howto-sspr-authenticationdata.md)
* [Authentifizierungsmethoden](concept-sspr-howitworks.md#authentication-methods)
* [Kennwortrichtlinien und -einschränkungen in Azure Active Directory](concept-sspr-policy.md)
* [Übersicht über die Kennwortrückschreibung](./tutorial-enable-sspr-writeback.md)
* [Berichterstellungsoptionen für die Kennwortverwaltung von Azure AD](howto-sspr-reporting.md)
* [Welche Optionen sind für SSPR verfügbar, und was bedeuten sie?](concept-sspr-howitworks.md)
* [Anscheinend ist ein Fehler aufgetreten. Wie behebe ich Probleme mit SSPR?](./troubleshoot-sspr.md)