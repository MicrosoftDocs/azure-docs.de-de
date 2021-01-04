---
title: Berichte der Self-Service-Kennwortzurücksetzung – Azure Active Directory
description: Berichterstellung zu Ereignissen der Self-Service-Kennwortzurücksetzung von Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/01/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a764538ebc73927b1d274b2538e123ec90ce60e
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96741557"
---
# <a name="reporting-options-for-azure-ad-password-management"></a>Berichterstellungsoptionen für die Kennwortverwaltung von Azure AD

Nach einer Bereitstellung möchten viele Organisationen wissen, wie oder ob Self-Service-Kennwortzurücksetzung (Self-Service Password Reset, SSPR) tatsächlich verwendet wird. Über die Berichtsfunktion, die von Azure Active Directory (Azure AD) bereitgestellt wird, können Sie Fragen beantworten, indem Sie vordefinierte Berichte verwenden. Wenn Sie eine ordnungsgemäße Lizenz haben, können Sie auch benutzerdefinierte Abfragen erstellen.

![Berichterstellung zu SSPR mithilfe der Überwachungsprotokolle in Azure AD][Reporting]

Die folgenden Fragen können mit den Berichten beantwortet werden, die im [Azure-Portal](https://portal.azure.com/) vorhanden sind:

> [!NOTE]
> Sie müssen [ein globaler Administrator](../roles/permissions-reference.md) sein, und Sie müssen zustimmen, dass diese Daten im Auftrag Ihres Unternehmens erfasst werden. Um zuzustimmen, müssen Sie die Registerkarte **Berichterstellung** oder die Überwachungsprotokolle mindestens ein Mal öffnen. Bis dahin werden keine Daten für Ihre Organisation erfasst.
>

* Wie viele Personen haben sich für die Kennwortzurücksetzung registriert?
* Wer hat sich für das Zurücksetzen von Kennwörtern registriert?
* Welche Daten registrieren die Benutzer?
* Wie viele Personen haben ihre Kennwörter in den letzten sieben Tagen zurückgesetzt?
* Welche Methoden werden von Benutzern und Administratoren am häufigsten zum Zurücksetzen von Kennwörtern verwendet?
* Welche Probleme treten häufig für Benutzer oder Administratoren bei dem Versuch auf, das Kennwort zurückzusetzen?
* Welche Administratoren setzen häufig ihre eigenen Kennwörter zurück?
* Gibt es verdächtige Aktivitäten beim Zurücksetzen des Kennworts?

## <a name="how-to-view-password-management-reports-in-the-azure-portal"></a>Anzeigen von Kennwortverwaltungsberichten im Azure-Portal

Die Art und Weise, wie Aktivitäten zur Kennwortzurücksetzung und zur Registrierung für die Kennwortzurücksetzung angezeigt werden, wurde verbessert. Führen Sie die folgenden Schritte aus, um nach den Ereignissen zur Kennwortzurücksetzung und zur Registrierung für die Kennwortzurücksetzung zu suchen:

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).
2. Wählen Sie **Alle Dienste** im linken Bereich aus.
3. Suchen Sie in der Liste mit den Diensten nach **Azure Active Directory**, und wählen Sie diesen Eintrag aus.
4. Wählen Sie im Abschnitt „Verwalten“ die Option **Benutzer** aus.
5. Wählen Sie auf dem Blatt **Benutzer** die Option **Überwachungsprotokolle** aus. Daraufhin werden sämtliche Überwachungsereignisse angezeigt, die für alle Benutzer in Ihrem Verzeichnis aufgetreten sind. Diese Ansicht kann gefiltert werden, um alle kennwortbezogenen Ereignisse anzuzeigen.
6. Wählen Sie am oberen Rand des Bereichs im Menü **Filter** die Dropdownliste **Dienst** aus, und ändern Sie den Diensttyp in **Self-Service-Kennwortverwaltung**.
7. Grenzen Sie die Liste optional weiter ein, indem Sie die spezielle **Aktivität** auswählen, für die Sie sich interessieren.

### <a name="combined-registration"></a>Kombinierte Registrierung

Wenn Sie die [kombinierte Registrierung](./concept-registration-mfa-sspr-combined.md) aktiviert haben, finden Sie Informationen zur Benutzeraktivität in den Überwachungsprotokollen unter **Sicherheit** > **Authentifizierungsmethoden**.

## <a name="description-of-the-report-columns-in-the-azure-portal"></a>Beschreibung der Berichtsspalten im Azure-Portal

In der folgende Liste werden alle Berichtsspalten im Detail beschrieben, die es im Azure-Portal gibt:

* **Benutzer:** Der Benutzer, der versucht hat, sich für die Kennwortzurücksetzung zu registrieren
* **Rolle**: Die Rolle des Benutzers im Verzeichnis
* **Datum und Uhrzeit**: Datum und Uhrzeit des Versuchs
* **Registrierte Daten**: Die Authentifizierungsdaten, die vom Benutzer während der Registrierung für die Kennwortzurücksetzung bereitgestellt wurden

## <a name="description-of-the-report-values-in-the-azure-portal"></a>Beschreibung der Berichtswerte im Azure-Portal

In der folgenden Tabelle sind die verschiedenen Werte beschrieben, die Sie für jede Spalte im Azure-Portal festlegen können:

| Column | Zulässige Werte und deren Bedeutung |
| --- | --- |
| Registrierte Daten |**Alternative E-Mail-Adresse**: Der Benutzer hat eine alternative E-Mail-Adresse oder eine Authentifizierungs-E-Mail zur Authentifizierung verwendet.<p><p>**Bürotelefon:** Der Benutzer hat eine Bürotelefonnummer zur Authentifizierung verwendet.<p>**Mobiltelefon**: Der Benutzer hat ein Mobiltelefon oder Authentifizierungstelefon zur Authentifizierung verwendet.<p>**Sicherheitsfragen**: Der Benutzer hat Sicherheitsfragen zur Authentifizierung verwendet.<p>**Eine beliebige Kombination der vorherigen Methoden, z.B. alternative E-Mail-Adresse und Mobiltelefon**: Tritt auf, wenn eine Richtlinie für die zweistufige Überprüfung angegeben ist, und zeigt, welche beiden Methoden der Benutzer zur Authentifizierung seiner Anforderung zum Zurücksetzen des Kennworts verwendet hat. |

## <a name="self-service-password-management-activity-types"></a>Aktivitätstypen für die Self-Service-Kennwortverwaltung

In der Überwachungsereigniskategorie **Self-Service-Kennwortverwaltung** werden folgende Aktivitätstypen angezeigt:

* [Von Self-Service-Kennwortzurücksetzung ausgeschlossen](#activity-type-blocked-from-self-service-password-reset): Gibt an, dass ein Benutzer in den letzten 24 Stunden mindestens fünfmal versucht hat, ein Kennwort zurückzusetzen, ein bestimmtes Gate zu verwenden oder eine Telefonnummer zu bestätigen.
* [Kennwort ändern (Self-Service)](#activity-type-change-password-self-service): Gibt an, dass ein Benutzer eine freiwillige oder (wegen Ablauf) erzwungene Kennwortänderung vorgenommen hat.
* [Kennwort zurücksetzen (durch Administrator)](#activity-type-reset-password-by-admin): Gibt an, dass ein Administrator im Auftrag eines Benutzers ein Kennwort über das Azure-Portal zurückgesetzt hat.
* [Kennwort zurücksetzen (Self-Service)](#activity-type-reset-password-self-service): Gibt an, dass ein Benutzer sein Kennwort über das [Portal für die Azure AD-Kennwortzurücksetzung](https://passwordreset.microsoftonline.com) erfolgreich zurückgesetzt hat.
* [Aktivitätsstatus der Self-Service-Kennwortzurücksetzung](#activity-type-self-serve-password-reset-flow-activity-progress): Gibt die einzelnen Schritte an, die ein Benutzer im Rahmen der Kennwortzurücksetzung durchläuft, beispielsweise Durchlaufen eines bestimmten Authentifizierungsgates für die Kennwortzurücksetzung.
* [Benutzerkonto entsperren (Self-Service)](#activity-type-unlock-a-user-account-self-service): Gibt an, dass ein Benutzer sein Active Directory-Konto über das [Azure AD-Portal für die Kennwortzurücksetzung](https://passwordreset.microsoftonline.com) erfolgreich entsperrt hat, indem er das Active Directory-Feature zur Kontoentsperrung ohne Zurücksetzen verwendet hat.
* [Für Self-Service-Kennwortzurücksetzung registrierter Benutzer](#activity-type-user-registered-for-self-service-password-reset): Gibt an, dass ein Benutzer alle erforderlichen Informationen registriert hat, um sein Kennwort gemäß der aktuellen Mandantenrichtlinie für die Kennwortzurücksetzung zurückzusetzen.

### <a name="activity-type-blocked-from-self-service-password-reset"></a>Aktivitätstyp: Blocked from self-service password reset (Von Self-Service-Kennwortzurücksetzung ausgeschlossen)

In der folgenden Liste werden die Details dieser Aktivität erläutert:

* **Beschreibung der Aktivität**: Gibt an, dass ein Benutzer in den letzten 24 Stunden mindestens fünfmal versucht hat, ein Kennwort zurückzusetzen, ein bestimmtes Gate zu verwenden oder eine Telefonnummer zu bestätigen.
* **Akteur der Aktivität**: Der Benutzer, der daran gehindert wurde, weitere Zurücksetzungsvorgänge auszuführen. Der Benutzer kann ein Endbenutzer oder ein Administrator sein.
* **Ziel der Aktivität**: Der Benutzer, der daran gehindert wurde, weitere Zurücksetzungsvorgänge auszuführen. Der Benutzer kann ein Endbenutzer oder ein Administrator sein.
* **Aktivitätsstatus**:
  * _Erfolg_: Gibt an, dass ein Benutzer daran gehindert wurde, in den nächsten 24 Stunden weitere Zurücksetzungsvorgänge auszuführen, weitere Authentifizierungsmethoden zu verwenden oder weitere Telefonnummern zu bestätigen.
* **Fehlerursache für den Aktivitätsstatus**: Nicht zutreffend

### <a name="activity-type-change-password-self-service"></a>Aktivitätstyp: Change password (self-service) (Kennwort ändern (Self-Service))

In der folgenden Liste werden die Details dieser Aktivität erläutert:

* **Beschreibung der Aktivität**: Gibt an, dass ein Benutzer eine freiwillige oder (wegen Ablauf) erzwungene Kennwortänderung vorgenommen hat.
* **Akteur der Aktivität**: Der Benutzer, der sein Kennwort geändert hat Der Benutzer kann ein Endbenutzer oder ein Administrator sein.
* **Ziel der Aktivität**: Der Benutzer, der sein Kennwort geändert hat Der Benutzer kann ein Endbenutzer oder ein Administrator sein.
* **Aktivitätsstatus**:
  * _Erfolg_: Gibt an, dass ein Benutzer sein Kennwort erfolgreich geändert hat.
  * _Fehler_: Gibt an, dass ein Benutzer sein Kennwort nicht ändern konnte. Sie können die Zeile auswählen, um die Kategorie **Activity Status Reason** (Grund für den Aktivitätsstatus) anzuzeigen und mehr darüber zu erfahren, warum der Fehler aufgetreten ist.
* **Fehlerursache für den Aktivitätsstatus**:
  * _FuzzyPolicyViolationInvalidPassword_: Der Benutzer hat ein Kennwort angegeben, das automatisch blockiert wurde, weil die Microsoft-Erkennung für unzulässige Kennwörter das Kennwort als zu allgemein oder als besonders schwach eingestuft hat.

### <a name="activity-type-reset-password-by-admin"></a>Aktivitätstyp: Reset password (by admin) (Kennwort zurücksetzen (durch Administrator))

In der folgenden Liste werden die Details dieser Aktivität erläutert:

* **Beschreibung der Aktivität**: Gibt an, dass ein Administrator im Auftrag eines Benutzers ein Kennwort über das Azure-Portal zurückgesetzt hat.
* **Akteur der Aktivität**: Der Administrator, der das Kennwort im Auftrag eines anderen Endbenutzers oder Administrators zurückgesetzt hat Dabei muss es sich um einen Kennwortadministrator, Benutzeradministrator oder Helpdeskadministrator handeln.
* **Ziel der Aktivität**: Der Benutzer, dessen Kennwort zurückgesetzt wurde. Der Benutzer kann ein Endbenutzer oder ein anderer Administrator sein.
* **Aktivitätsstatus**:
  * _Erfolg_: Gibt an, dass ein Administrator das Kennwort eines Benutzers erfolgreich zurückgesetzt hat.
  * _Fehler_: Gibt an, dass ein Administrator das Kennwort eines Benutzers nicht ändern konnte. Sie können die Zeile auswählen, um die Kategorie **Activity Status Reason** (Grund für den Aktivitätsstatus) anzuzeigen und mehr darüber zu erfahren, warum der Fehler aufgetreten ist.

### <a name="activity-type-reset-password-self-service"></a>Aktivitätstyp: Reset password (self-service) (Kennwort zurücksetzen (Self-Service))

In der folgenden Liste werden die Details dieser Aktivität erläutert:

* **Beschreibung der Aktivität**: Gibt an, dass ein Benutzer sein Kennwort über das [Portal für die Azure AD-Kennwortzurücksetzung](https://passwordreset.microsoftonline.com) erfolgreich zurückgesetzt hat.
* **Akteur der Aktivität**: Der Benutzer, der sein Kennwort zurückgesetzt hat. Der Benutzer kann ein Endbenutzer oder ein Administrator sein.
* **Ziel der Aktivität**: Der Benutzer, der sein Kennwort zurückgesetzt hat. Der Benutzer kann ein Endbenutzer oder ein Administrator sein.
* **Aktivitätsstatus**:
  * _Erfolg_: Gibt an, dass ein Benutzer sein eigenes Kennwort erfolgreich zurückgesetzt hat.
  * _Fehler_: Gibt an, dass ein Benutzer sein eigenes Kennwort nicht zurücksetzen konnte. Sie können die Zeile auswählen, um die Kategorie **Activity Status Reason** (Grund für den Aktivitätsstatus) anzuzeigen und mehr darüber zu erfahren, warum der Fehler aufgetreten ist.
* **Fehlerursache für den Aktivitätsstatus**:
  * _FuzzyPolicyViolationInvalidPassword_: Der Administrator hat ein Kennwort angegeben, das automatisch blockiert wurde, weil die Microsoft-Erkennung für unzulässige Kennwörter das Kennwort als zu allgemein oder als besonders schwach eingestuft hat.

### <a name="activity-type-self-serve-password-reset-flow-activity-progress"></a>Aktivitätstyp: Aktivitätsstatus der Self-Service-Kennwortzurücksetzung

In der folgenden Liste werden die Details dieser Aktivität erläutert:

* **Beschreibung der Aktivität**: Gibt die einzelnen Schritte an, die ein Benutzer im Rahmen der Kennwortzurücksetzung durchläuft, beispielsweise Durchlaufen eines bestimmten Authentifizierungsgates für die Kennwortzurücksetzung.
* **Akteur der Aktivität**: Der Benutzer, der einen Teil der Kennwortzurücksetzung ausgeführt hat. Der Benutzer kann ein Endbenutzer oder ein Administrator sein.
* **Ziel der Aktivität**: Der Benutzer, der einen Teil der Kennwortzurücksetzung ausgeführt hat. Der Benutzer kann ein Endbenutzer oder ein Administrator sein.
* **Aktivitätsstatus**:
  * _Erfolg_: Gibt an, dass ein Benutzer einen bestimmten Schritt der Kennwortzurücksetzung erfolgreich ausgeführt hat.
  * _Fehler_: Gibt an, dass ein bestimmter Schritt der Kennwortzurücksetzung nicht erfolgreich war. Sie können die Zeile auswählen, um die Kategorie **Activity Status Reason** (Grund für den Aktivitätsstatus) anzuzeigen und mehr darüber zu erfahren, warum der Fehler aufgetreten ist.
* **Gründe für den Aktivitätsstatus**:   In der folgenden Tabelle sind [alle zulässigen Gründe für den Status einer Zurücksetzungsaktivität](#description-of-the-report-columns-in-the-azure-portal) aufgeführt.

### <a name="activity-type-unlock-a-user-account-self-service"></a>Aktivitätstyp: Benutzerkonto entsperren (Self-Service)

In der folgenden Liste werden die Details dieser Aktivität erläutert:

* **Beschreibung der Aktivität**: Gibt an, dass ein Benutzer sein Active Directory-Konto über das [Azure AD-Portal für die Kennwortzurücksetzung](https://passwordreset.microsoftonline.com) erfolgreich entsperrt hat, indem er das Active Directory-Feature zur Kontoentsperrung ohne Zurücksetzen verwendet hat.
* **Akteur der Aktivität**: Der Benutzer, der sein Konto entsperrt hat, ohne das Kennwort zurückzusetzen. Der Benutzer kann ein Endbenutzer oder ein Administrator sein.
* **Ziel der Aktivität**: Der Benutzer, der sein Konto entsperrt hat, ohne das Kennwort zurückzusetzen. Der Benutzer kann ein Endbenutzer oder ein Administrator sein.
* **Zulässige Aktivitätsstatus**:
  * _Erfolg_: Gibt an, dass ein Benutzer sein eigenes Konto erfolgreich entsperrt hat.
  * _Fehler_: Gibt an, dass ein Benutzer sein Konto nicht entsperren konnte. Sie können die Zeile auswählen, um die Kategorie **Activity Status Reason** (Grund für den Aktivitätsstatus) anzuzeigen und mehr darüber zu erfahren, warum der Fehler aufgetreten ist.

### <a name="activity-type-user-registered-for-self-service-password-reset"></a>Aktivitätstyp: User registered for self-service password reset (Für Self-Service-Kennwortzurücksetzung registrierter Benutzer)

In der folgenden Liste werden die Details dieser Aktivität erläutert:

* **Beschreibung der Aktivität**: Gibt an, dass ein Benutzer alle erforderlichen Informationen registriert hat, um sein Kennwort gemäß der aktuellen Mandantenrichtlinie für die Kennwortzurücksetzung zurückzusetzen. 
* **Akteur der Aktivität**: Der Benutzer, der sich für die Kennwortzurücksetzung registriert hat. Der Benutzer kann ein Endbenutzer oder ein Administrator sein.
* **Ziel der Aktivität**: Der Benutzer, der sich für die Kennwortzurücksetzung registriert hat. Der Benutzer kann ein Endbenutzer oder ein Administrator sein.
* **Zulässige Aktivitätsstatus**:
  * _Erfolg_: Gibt an, dass sich ein Benutzer erfolgreich in Übereinstimmung mit der aktuellen Richtlinie für eine Kennwortzurücksetzung registriert hat. 
  * _Fehler_: Gibt an, dass sich ein Benutzer nicht für die Kennwortzurücksetzung registrieren konnte. Sie können die Zeile auswählen, um die Kategorie **Activity Status Reason** (Grund für den Aktivitätsstatus) anzuzeigen und mehr darüber zu erfahren, warum der Fehler aufgetreten ist.

     >[!NOTE]
     >Fehler bedeutet nicht, dass ein Benutzer sein eigenes Kennwort nicht zurücksetzen kann, sondern bedeutet, dass der Benutzer den Registrierungsvorgang nicht abgeschlossen hat. Wenn das Konto des Benutzers unbestätigte Daten enthält, die korrekt sind, beispielsweise eine noch nicht bestätigte Telefonnummer, können diese weiterhin zum Zurücksetzen des Kennworts verwendet werden, auch wenn die Telefonnummer noch nicht bestätigt wurde.

## <a name="next-steps"></a>Nächste Schritte

* [Nutzungs- und Insights-Berichte für SSPR und MFA](howto-authentication-methods-usage-insights.md)
* [Erfolgreiches Rollout der Self-Service-Kennwortzurücksetzung](howto-sspr-deployment.md)
* [Ich habe mein Azure AD-Kennwort vergessen. Was nun?](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registrieren für die Self-Service-Kennwortzurücksetzung](../user-help/active-directory-passwords-reset-register.md)
* [Lizenzanforderungen für Azure AD-Self-Service-Kennwortzurücksetzung](concept-sspr-licensing.md)
* [Bereitstellen der Kennwortzurücksetzung ohne erforderliche Endbenutzerregistrierung](howto-sspr-authenticationdata.md)
* [Authentifizierungsmethoden](concept-sspr-howitworks.md#authentication-methods)
* [Kennwortrichtlinien und -einschränkungen in Azure Active Directory](concept-sspr-policy.md)
* [Übersicht über die Kennwortrückschreibung](./tutorial-enable-sspr-writeback.md)
* [Welche Optionen sind für SSPR verfügbar, und was bedeuten sie?](concept-sspr-howitworks.md)
* [Anscheinend ist ein Fehler aufgetreten. Wie behebe ich Probleme mit SSPR?](./troubleshoot-sspr.md)
* [Ich habe eine Frage, die nicht an einer anderen Stelle abgedeckt wurde.](active-directory-passwords-faq.md)

[Reporting]: ./media/howto-sspr-reporting/sspr-reporting.png "Beispiel für Protokolle zur Überwachung der SSPR-Aktivität in Azure AD"