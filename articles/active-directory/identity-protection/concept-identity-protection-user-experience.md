---
title: Benutzererfahrungen mit Azure AD Identity Protection
description: Benutzeroberfläche von Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d4897b9187caab50be4db75bbc0af03e3d35aa4
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94835984"
---
# <a name="user-experiences-with-azure-ad-identity-protection"></a>Benutzererfahrungen mit Azure AD Identity Protection

Azure Active Directory Identity Protection bietet folgende Möglichkeiten:

* Festlegen, dass sich Benutzer für Azure AD Multi-Factor Authentication (MFA) registrieren müssen
* Automatisieren der Behandlung riskanter Anmeldungen und kompromittierter Benutzer

Alle Identitätsschutzrichtlinien haben Auswirkungen auf den Benutzeranmeldevorgang. Indem Sie Benutzern das Registrieren für und Verwenden von Tools wie Azure AD MFA und die Self-Service-Kennwortzurücksetzung erlauben, können die Auswirkungen verringert werden. Diese Tools bieten Benutzern in Kombination mit der Auswahl geeigneter Richtlinien bei Bedarf eine Option zur Eigenwartung.

## <a name="multi-factor-authentication-registration"></a>Registrierung für die mehrstufige Authentifizierung

Indem Sie die Identity Protection-Richtlinie, die eine Registrierung mit mehrstufiger Authentifizierung erfordert, für alle Benutzer aktivieren, wird sichergestellt, dass diese in Zukunft die Möglichkeit zur Eigenwartung mit Azure AD MFA haben. Wenn Sie diese Richtlinie konfigurieren, können sich Ihre Benutzer in den darauffolgenden 14 Tagen freiwillig registrieren. Nach Ablauf dieses Zeitraums wird die Registrierung erzwungen. Die Benutzeroberfläche wird unten beschrieben. Weitere Informationen finden Sie in der Dokumentation für Endbenutzer im Artikel [Übersicht über die zweistufige Überprüfung und Ihr Geschäfts-, Schul- oder Unikonto](../user-help/multi-factor-authentication-end-user-first-time.md).

### <a name="registration-interrupt"></a>Registrierungsunterbrechung

1. Bei der Anmeldung bei einer beliebigen in Azure AD integrierten Anwendung wird der Benutzer davon in Kenntnis gesetzt, dass das Konto für die mehrstufige Authentifizierung eingerichtet werden muss. Diese Richtlinie wird auch in Windows 10 Out-of-Box-Experience für neue Benutzer mit einem neuen Gerät ausgelöst.
   
    ![Weitere Informationen erforderlich](./media/concept-identity-protection-user-experience/identity-protection-experience-more-info-mfa.png)

1. Führen Sie die begleiteten Schritte zum Registrieren bei Azure AD Multi-Factor Authentication aus, und schließen Sie Ihre Anmeldung ab.

## <a name="risky-sign-in-remediation"></a>Behandlung riskanter Anmeldungen

Wenn ein Administrator eine Richtlinie für Anmelderisiken konfiguriert hat, werden die betroffenen Benutzer benachrichtigt, wenn sie versuchen, sich anzumelden und die Risikostufe der Richtlinie ausgelöst wird. 

### <a name="risky-sign-in-self-remediation"></a>Eigenwartung riskanter Anmeldungen

1. Der Benutzer wird informiert, dass im Zusammenhang mit seiner Anmeldung eine Unregelmäßigkeit erkannt wurde (beispielsweise eine Anmeldung an einem neuen Standort, mit einem neuen Gerät oder mit einer neuen App).
   
    ![Eingabeaufforderung bei ungewöhnlicher Aktion](./media/concept-identity-protection-user-experience/120.png)

1. Der Benutzer muss seine Identität nachweisen, indem er Azure AD MFA mit einer der zuvor registrierten Methoden ausführt. 

### <a name="risky-sign-in-administrator-unblock"></a>Aufhebung der Blockierung bei riskanten Anmeldungen durch Administrator

Administratoren können eine Benutzer bei der Anmeldung je nach Risikostufe blockieren. Zur Aufhebung der Blockierung muss sich der Endbenutzer an die IT-Mitarbeiter der Organisation wenden oder sich an einem bekannten Standort oder mit einem bekannten Gerät anmelden. In diesem Fall ist keine Eigenwartung mittels mehrstufiger Authentifizierung möglich.

![Blockiert durch Anmelderisikorichtlinie](./media/concept-identity-protection-user-experience/200.png)

Die IT-Mitarbeiter können die Anweisungen im Abschnitt [Aufheben der Blockierung von Benutzern](howto-identity-protection-remediate-unblock.md#unblocking-based-on-sign-in-risk) befolgen, um Benutzern die Anmeldung wieder zu ermöglichen.

## <a name="risky-user-remediation"></a>Behandlung bei riskanten Benutzern

Wenn eine Benutzerrisikorichtlinie konfiguriert wurde, müssen Benutzer, die der Benutzerrisiko-Wahrscheinlichkeitsstufe entsprechen (und somit als gefährdet betrachtet werden), das Wiederherstellungsverfahren für kompromittierte Benutzer durchlaufen, um sich wieder anmelden zu können. 

### <a name="risky-user-self-remediation"></a>Eigenwartung bei riskanten Benutzern

1. Der Benutzer wird darüber informiert, dass die Sicherheit des Kontos aufgrund von verdächtigen Aktivitäten oder kompromittierten Anmeldeinformationen gefährdet ist.
   
    ![Wiederherstellung](./media/concept-identity-protection-user-experience/101.png)

1. Der Benutzer muss seine Identität nachweisen, indem er Azure AD MFA mit einer der zuvor registrierten Methoden ausführt. 
1. Am Ende des Vorgangs muss der Benutzer sein Kennwort mit der Self-Service-Kennwortzurücksetzung ändern, da unter Umständen eine andere Person Zugriff auf sein Konto hatte.

## <a name="risky-sign-in-administrator-unblock"></a>Aufhebung der Blockierung bei riskanten Anmeldungen durch Administrator

Administratoren können eine Benutzer bei der Anmeldung je nach Risikostufe blockieren. Zur Aufhebung der Blockierung muss sich der Endbenutzer an die IT-Mitarbeiter der Organisation wenden. In diesem Fall ist keine Eigenwartung mittels mehrstufiger Authentifizierung und Self-Service-Kennwortzurücksetzung möglich.

![Blockiert durch Benutzerrisikorichtlinie](./media/concept-identity-protection-user-experience/104.png)

Die IT-Mitarbeiter können die Anweisungen im Abschnitt [Aufheben der Blockierung von Benutzern](howto-identity-protection-remediate-unblock.md#unblocking-based-on-user-risk) befolgen, um Benutzern die Anmeldung wieder zu ermöglichen.

## <a name="see-also"></a>Weitere Informationen

- [Beheben von Risiken und Aufheben der Blockierung von Benutzern](howto-identity-protection-remediate-unblock.md)

- [Azure Active Directory Identity Protection](./overview-identity-protection.md)