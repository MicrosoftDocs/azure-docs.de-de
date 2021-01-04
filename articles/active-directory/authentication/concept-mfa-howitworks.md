---
title: Azure AD Multi-Factor Authentication – Übersicht
description: Hier erfahren Sie, wie Azure AD Multi-Factor Authentication zum Schutz des Zugriffs auf Daten und Anwendungen beiträgt und gleichzeitig ein einfaches Anmeldeverfahren für Benutzer bietet.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1cc5227cb4b5de02cba65a60dd469da93d3767e5
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96744056"
---
# <a name="how-it-works-azure-ad-multi-factor-authentication"></a>So funktioniert's: Azure AD Multi-Factor Authentication

Multi-Factor Authentication (mehrstufige Authentifizierung) ist ein Prozess, bei dem Benutzer während des Anmeldevorgangs zur Durchführung eines weiteren Identifizierungsverfahrens aufgefordert werden, z. B. per Eingabe eines Codes auf dem Smartphone oder per Fingerabdruckscan.

Wenn Sie zum Authentifizieren von Benutzern nur ein Kennwort nutzen, kann dies einen Angriffsvektor darstellen und mit Unsicherheit verbunden sein. Falls das Kennwort nicht sicher ist oder offengelegt wurde, können Sie nicht sicher sein, ob es wirklich der Benutzer ist, der sich mit dem Benutzernamen und dem Kennwort anmeldet, oder ein Angreifer. Wenn Sie ein zweites Authentifizierungsverfahren erzwingen, wird die Sicherheit erhöht, weil dieses zusätzliche Verfahren von einem Angreifer nicht ohne Weiteres nachvollzogen bzw. dupliziert werden kann.

![Abbildung zum Konzept der unterschiedlichen Arten von mehrstufiger Authentifizierung](./media/concept-mfa-howitworks/methods.png)

Für Azure AD Multi-Factor Authentication sind mindestens zwei der folgenden Authentifizierungsverfahren obligatorisch:

* Eine dem Benutzer bekannte Information (meist ein Kennwort).
* Ein im Besitz des Benutzers befindliches Objekt, z. B. ein vertrauenswürdiges Gerät, das nicht ohne Weiteres dupliziert werden kann (Telefon oder Hardwareschlüssel).
* Ein biometrisches Merkmal des Benutzers (Fingerabdruck- oder Gesichtsscan).

Um das Onboarding zu vereinfachen, können sich Benutzer mit nur einem Schritt sowohl für die Self-Service-Kennwortzurücksetzung als auch für Azure AD Multi-Factor Authentication registrieren. Administratoren können definieren, welche Verfahren für die sekundäre Authentifizierung genutzt werden können. Azure AD Multi-Factor Authentication kann auch erzwungen werden, wenn Benutzer eine Self-Service-Kennwortzurücksetzung durchführen, um diesen Prozess noch sicherer zu machen.

![Verwendete Authentifizierungsmethoden auf dem Anmeldebildschirm](media/concept-authentication-methods/overview-login.png)

Azure AD Multi-Factor Authentication trägt zum Schutz des Zugriffs auf Daten und Anwendungen bei und ist gleichzeitig für Benutzer unkompliziert. Indem eine zweite Form der Authentifizierung verlangt wird, bietet das Verfahren zusätzliche Sicherheit und eine zuverlässige Authentifizierung über verschiedene einfache [Authentifizierungsmethoden](concept-authentication-methods.md). Benutzer können auf der Grundlage von Konfigurationsentscheidungen, die ein Administrator trifft, zur MFA aufgefordert werden oder auch nicht.

Ihre Anwendungen oder Dienste erfordern keine Änderungen, um Azure AD Multi-Factor Authentication verwenden zu können. Die Überprüfungsaufforderungen sind Teil der Azure AD-Anmeldung, bei der die MFA-Abfrage ggf. automatisch angefordert und verarbeitet wird.

## <a name="available-verification-methods"></a>Verfügbare Überprüfungsmethoden

Wenn ein Benutzer sich bei einer Anwendung oder einem Dienst anmeldet und eine MFA-Aufforderung erhält, kann er eine der registrierten Formen der zusätzlichen Überprüfung wählen. Ein Administrator könnte die Registrierung dieser Azure AD Multi-Factor Authentication-Überprüfungsmethoden verlangen, oder der Benutzer kann Überprüfungsmethoden unter [Eigenes Profil](https://myprofile.microsoft.com) bearbeiten oder hinzufügen.

Die folgenden zusätzlichen Formen der Überprüfung können bei Azure AD Multi-Factor Authentication verwendet werden:

* Microsoft Authenticator-App
* OATH-Hardwaretoken
* sms
* Anruf

## <a name="how-to-enable-and-use-azure-ad-multi-factor-authentication"></a>Aktivieren und Verwenden von Azure AD Multi-Factor Authentication

Für Benutzer und Gruppen kann Azure AD Multi-Factor Authentication aktiviert werden, damit sie während der Anmeldung zur weiteren Überprüfung aufgefordert werden. Für alle Azure AD-Mandanten sind [Sicherheitsstandards](../fundamentals/concept-fundamentals-security-defaults.md) verfügbar, damit die Microsoft Authenticator-App schnell von allen Benutzern verwendet werden kann.

Für die genauere Steuerung können [Richtlinien für den bedingten Zugriff](../conditional-access/overview.md) verwendet werden, um Ereignisse oder Anwendungen zu definieren, die MFA erfordern. Diese Richtlinien können reguläre Anmeldungsereignisse zulassen, wenn sich der Benutzer im Unternehmensnetzwerk befindet oder ein registriertes Gerät verwendet, jedoch zusätzliche Überprüfungsfaktoren anfordern, wenn der Benutzer remote arbeitet oder ein persönliches Gerät verwendet.

![Übersichtsdiagramm: Funktionsweise des bedingten Zugriffs zum Schutz des Anmeldevorgangs](media/tutorial-enable-azure-mfa/conditional-access-overview.png)

## <a name="next-steps"></a>Nächste Schritte

Informationen zur Lizenzierung finden Sie unter [Features und Lizenzen für Azure AD Multi-Factor Authentication](concept-mfa-licensing.md).

Wenn Sie MFA in Aktion erleben möchten, aktivieren Sie im folgenden Tutorial Azure AD Multi-Factor Authentication für eine Reihe von Testbenutzern:

> [!div class="nextstepaction"]
> [Aktivieren von Azure AD Multi-Factor Authentication](./tutorial-enable-azure-mfa.md)