---
title: 'Azure Active Directory-Authentifizierung: Übersicht'
description: Enthält eine Beschreibung der unterschiedlichen Authentifizierungsmethoden und Sicherheitsfeatures für Benutzeranmeldungen mit Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: overview
ms.date: 07/13/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: sahenry, michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: f85b78480e7258780f8dd396431edeb968189e4c
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96741251"
---
# <a name="what-is-azure-active-directory-authentication"></a>Was ist die Azure Active Directory-Authentifizierung?

Eine der Hauptfunktionen einer Identitätsplattform ist die Verifizierung bzw. *Authentifizierung* von Anmeldeinformationen, wenn ein Benutzer sich an einem Gerät, einer Anwendung oder einem Dienst anmeldet. In Azure Active Directory (Azure AD) geht es bei der Authentifizierung um mehr als nur die Verifizierung eines Benutzernamens und des zugehörigen Kennworts. Die Azure AD-Authentifizierung umfasst die folgenden Komponenten, um die Sicherheit zu verbessern und den Supportaufwand für den Helpdesk zu reduzieren:

* Self-Service-Kennwortzurücksetzung
* Azure AD Multi-Factor Authentication
* Hybridintegration zum Zurückschreiben von Kennwortänderungen in die lokale Umgebung
* Hybridintegration zum Erzwingen von Kennwortschutzrichtlinien für eine lokale Umgebung
* Kennwortlose Authentifizierung

## <a name="improve-the-end-user-experience"></a>Verbessern der Endbenutzererfahrung

Azure AD trägt zum Schutz der Identität eines Benutzers und zur Vereinfachung der Umgebung für die Anmeldung bei. Mit Features wie der Self-Service-Kennwortzurücksetzung können Benutzer ihre Kennwörter aktualisieren oder ändern, indem Sie auf einem beliebigen Gerät den Webbrowser verwenden. Dieses Feature ist besonders nützlich, wenn der Benutzer sein Kennwort vergessen hat oder sein Konto gesperrt ist. Der Benutzer kann die Sperrung so selbst beseitigen und weiterarbeiten, ohne auf Support durch den Helpdesk oder den Administrator warten zu müssen.

Bei Azure AD Multi-Factor Authentication können Benutzer während der Anmeldung ein zusätzliches Authentifizierungsverfahren wählen, z. B. einen Telefonanruf oder eine Benachrichtigung per mobiler App. Hierdurch wird die Beschränkung auf ein einzelnes festes Verfahren für die sekundäre Authentifizierung, z. B.per Hardwaretoken, aufgehoben. Wenn für den Benutzer ein bestimmtes Authentifizierungsverfahren gerade nicht verfügbar ist, kann er ein anderes Verfahren auswählen und sofort weiterarbeiten.

![Verwendete Authentifizierungsmethoden auf dem Anmeldebildschirm](media/concept-authentication-methods/overview-login.png)

Bei der kennwortlosen Authentifizierung muss der Benutzer kein sicheres Kennwort mehr erstellen und im Kopf behalten. Funktionen wie Windows Hello for Business oder FIDO2-Sicherheitsschlüssel ermöglichen Benutzern die Anmeldung an einem Gerät oder einer Anwendung ohne Kennwort. Auf diese Weise wird die Komplexität verringert, die mit der Verwaltung von Kennwörtern für mehrere Umgebungen verbunden ist.

## <a name="self-service-password-reset"></a>Self-Service-Kennwortzurücksetzung

Mit der Self-Service-Kennwortzurücksetzung können Benutzer ihr Kennwort ohne Beteiligung eines Administrators oder des Helpdesks ändern oder zurücksetzen. Wenn das Konto eines Benutzers gesperrt ist oder dieser sein Kennwort vergessen hat, kann er die Schritte zum Entsperren ausführen und anschließend weiterarbeiten. Dies führt zu weniger Anrufen beim Helpdesk und Produktivitätsverlusten, wenn sich ein Benutzer nicht an seinem Gerät oder einer Anwendung anmelden kann.

Die Self-Service-Kennwortzurücksetzung funktioniert in den folgenden Szenarien:

* **Kennwortänderung**: Hierbei kennt ein Benutzer sein Kennwort und möchte es ändern.
* **Kennwortzurücksetzung**: Hierbei kann sich ein Benutzer nicht anmelden, weil er beispielsweise das Kennwort vergessen hat, sodass es zurückgesetzt werden muss.
* **Kontoentsperrung**: Hierbei kann sich ein Benutzer nicht anmelden, weil sein Konto gesperrt ist, und das Konto soll entsperrt werden.

Wenn ein Benutzer sein Kennwort per Self-Service-Kennwortzurücksetzung aktualisiert oder zurücksetzt, kann das Kennwort auch zurück in eine lokale Active Directory-Umgebung geschrieben werden. Mit dem Kennwortrückschreiben wird sichergestellt, dass ein Benutzer seine aktualisierten Anmeldeinformationen sofort für lokale Geräte und Anwendungen verwenden kann.

## <a name="azure-ad-multi-factor-authentication"></a>Azure AD Multi-Factor Authentication

Multi-Factor Authentication (mehrstufige Authentifizierung) ist ein Prozess, bei dem Benutzer während des Anmeldevorgangs zur Durchführung eines weiteren Identifizierungsverfahrens aufgefordert werden, z. B. per Eingabe eines Codes auf dem Smartphone oder per Fingerabdruckscan.

Wenn Sie zum Authentifizieren von Benutzern nur ein Kennwort nutzen, kann dies einen Angriffsvektor darstellen und mit Unsicherheit verbunden sein. Falls das Kennwort nicht sicher ist oder offengelegt wurde, können Sie nicht sicher sein, ob es wirklich der Benutzer ist, der sich mit dem Benutzernamen und dem Kennwort anmeldet, oder ein Angreifer. Wenn Sie ein zweites Authentifizierungsverfahren erzwingen, wird die Sicherheit erhöht, weil dieses zusätzliche Verfahren von einem Angreifer nicht ohne Weiteres nachvollzogen bzw. dupliziert werden kann.

![Abbildung zum Konzept der unterschiedlichen Arten von mehrstufiger Authentifizierung](./media/concept-mfa-howitworks/methods.png)

Für Azure AD Multi-Factor Authentication sind mindestens zwei der folgenden Authentifizierungsverfahren obligatorisch:

* Eine dem Benutzer bekannte Information (meist ein Kennwort).
* Ein im Besitz des Benutzers befindliches Objekt, z. B. ein vertrauenswürdiges Gerät, das nicht ohne Weiteres dupliziert werden kann (Telefon oder Hardwareschlüssel).
* Ein biometrisches Merkmal des Benutzers (Fingerabdruck- oder Gesichtsscan).

Um das Onboarding zu vereinfachen, können sich Benutzer mit nur einem Schritt sowohl für die Self-Service-Kennwortzurücksetzung als auch für Azure AD Multi-Factor Authentication registrieren. Administratoren können definieren, welche Verfahren für die sekundäre Authentifizierung genutzt werden können. Azure AD Multi-Factor Authentication kann auch erzwungen werden, wenn Benutzer eine Self-Service-Kennwortzurücksetzung durchführen, um diesen Prozess noch sicherer zu machen.

## <a name="password-protection"></a>Kennwortschutz

Standardmäßig werden unsichere Kennwörter, z. B. *Kennwort1*, von Azure AD blockiert. Eine globale Ausschlussliste mit bekannten unsicheren Kennwörtern wird automatisch aktualisiert und angewendet. Wenn ein Azure AD-Benutzer versucht, eines dieser unsicheren Kennwörter zu verwenden, erhält er eine Benachrichtigung, dass ein sichereres Kennwort gewählt werden muss.

Zur Erhöhung der Sicherheit können Sie benutzerdefinierte Richtlinien für den Kennwortschutz definieren. Für diese Richtlinien können Filter verwendet werden, um alle Varianten eines Kennworts zu blockieren, die beispielsweise einen Namen wie *Contoso* oder einen Ort wie *London* enthalten.

Zum Erzielen von Hybridsicherheit können Sie den Azure AD-Kennwortschutz in eine lokale Active Directory-Umgebung integrieren. Eine in der lokalen Umgebung installierte Komponente erhält die globale Ausschlussliste für Kennwörter und die benutzerdefinierten Richtlinien zum Kennwortschutz von Azure AD, und die Domänencontroller nutzen diese Informationen zum Schützen der Vorgänge für die Kennwortänderung. Mit diesem Hybridansatz wird sichergestellt, dass die Verwendung sicherer Kennwörter immer erzwungen wird – unabhängig davon, wie oder wo ein Benutzer seine Anmeldeinformationen ändert.

## <a name="passwordless-authentication"></a>Kennwortlose Authentifizierung

Das letztendliche Ziel für viele Umgebungen besteht darin, die Nutzung von Kennwörtern für Anmeldungen zu beenden. Features wie der Azure-Kennwortschutz oder Azure AD Multi-Factor Authentication tragen zwar zur Erhöhung der Sicherheit bei. Die Anmeldung per Benutzername und Kennwort bleibt aber eine unsichere Form der Authentifizierung, die missbraucht werden oder Ziel von Brute-Force-Angriffen werden kann.

![Abwägung von Sicherheit und Komfort: Auf dem Weg zur kennwortlosen Authentifizierung](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

Wenn Sie sich mit einem kennwortlosen Verfahren anmelden, werden die Anmeldeinformationen per Biometrielösung mit Windows Hello for Business oder FIDO2-Sicherheitsschlüssel bereitgestellt. Diese Authentifizierungsmethoden können von Angreifern nicht ohne Weiteres dupliziert werden.

Azure AD verfügt über Verfahren für die native Authentifizierung mit kennwortlosen Methoden, um die Anmeldung für Benutzer zu vereinfachen und das Angriffsrisiko zu verringern.

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Einstieg finden Sie im Tutorial zur [Self-Service-Kennwortzurücksetzung (SSPR)][tutorial-sspr] und im Tutorial zu [Azure AD Multi-Factor Authentication][tutorial-azure-mfa].

Weitere Informationen zur Self-Service-Kennwortzurücksetzung finden Sie unter [So funktioniert's: Self-Service-Kennwortzurücksetzung in Azure AD][concept-sspr].

Weitere Informationen zu den Konzepten von Multi-Factor Authentication finden Sie unter [So funktioniert's: Azure AD Multi-Factor Authentication][concept-mfa].

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
