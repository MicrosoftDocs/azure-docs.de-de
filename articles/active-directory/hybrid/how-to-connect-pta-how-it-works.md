---
title: 'Azure AD Connect: Passthrough-Authentifizierung – Funktionsweise | Microsoft-Dokumentation'
description: In diesem Artikel wird die Funktionsweise der Passthrough-Authentifizierung mit Azure Active Directory beschrieben.
services: active-directory
keywords: Passthrough-Authentifizierung mit Azure AD Connect, Active Directory installieren, erforderliche Komponenten für Azure AD, SSO, einmaliges Anmelden
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/19/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe92f761ac0b16da7c3cc3c69c1fa4b00f4e7579
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996592"
---
# <a name="azure-active-directory-pass-through-authentication-technical-deep-dive"></a>Passthrough-Authentifizierung mit Azure Active Directory: Technische Einzelheiten
Dieser Artikel enthält eine Übersicht über die Funktionsweise der Passthrough-Authentifizierung mit Azure Active Directory (Azure AD). Ausführliche technische und sicherheitsbezogene Informationen finden Sie im Artikel [Azure Active Directory-Passthrough-Authentifizierung – ausführliche Informationen zur Sicherheit](how-to-connect-pta-security-deep-dive.md).

## <a name="how-does-azure-active-directory-pass-through-authentication-work"></a>Funktionsweise der Passthrough-Authentifizierung mit Azure Active Directory

>[!NOTE]
>Damit die Passthrough-Authentifizierung funktioniert, müssen Benutzer über eine lokale Active Directory-Instanz mithilfe von Azure AD Connect in Azure AD bereitgestellt werden. Die Passthrough-Authentifizierung gilt nicht für Benutzer, die auf die Cloud beschränkt sind.

Wenn ein Benutzer versucht, sich bei einer durch Azure AD gesicherten Anwendung anzumelden und die Passthrough-Authentifizierung im Mandanten aktiviert ist, geschieht Folgendes:

1. Der Benutzer versucht, auf eine Anwendung zuzugreifen (z.B. [Outlook-Web-App](https://outlook.office365.com/owa/)).
2. Wenn der Benutzer nicht bereits angemeldet ist, wird der Benutzer zur Azure AD-Seite **Benutzeranmeldung** umgeleitet.
3. Der Benutzer gibt auf der Azure AD-Anmeldeseite seinen Benutzernamen ein und wählt anschließend die Schaltfläche **Weiter** aus.
4. Der Benutzer gibt auf der Azure AD-Anmeldeseite sein Kennwort ein und wählt anschließend die Schaltfläche **Anmelden** aus.
5. Nach dem Empfang der Anmeldeanforderung speichert Azure AD den Benutzernamen und das (mit dem öffentlichen Schlüssel der Authentifizierungs-Agents verschlüsselte) Kennwort in einer Warteschlange.
6. Ein lokaler Authentifizierungs-Agent ruft den Benutzernamen und das verschlüsselte Kennwort aus der Warteschlange ab. Beachten Sie, dass der Agent die Warteschlange nicht häufig auf Anforderungen überprüft, sondern Anforderungen über eine eingerichtete dauerhafte Verbindung abruft.
7. Der Agent entschlüsselt das Kennwort mithilfe des privaten Schlüssels.
8. Der Agent überprüft dann mithilfe von Standard-Windows-APIs den Benutzernamen und das Kennwort in Active Directory (ein ähnlicher Mechanismus wie bei den Active Directory-Verbunddiensten (AD FS)). Beim Benutzernamen kann es sich entweder um den lokalen Standardbenutzernamen (in der Regel `userPrincipalName`) handeln oder um ein anderes (als `Alternate ID` bezeichnetes) Attribut, das in Azure AD Connect konfiguriert ist.
9. Der lokale Active Directory-Domänencontroller (DC) wertet die Anforderung aus und gibt die entsprechende Antwort (Erfolg, Fehler, Kennwort abgelaufen oder Benutzer gesperrt) an den Agenten zurück.
10. Der Authentifizierungs-Agent gibt diese Antwort wiederum an Azure AD zurück.
11. Azure AD wertet die Antwort aus und gibt eine entsprechende Antwort an den Benutzer zurück. Beispiel: Azure AD meldet den Benutzer sofort an oder verlangt Azure AD Multi-Factor Authentication.
12. Wenn die Anmeldung erfolgreich ist, kann der Benutzer auf die Anwendung zugreifen.

Das folgende Diagramm veranschaulicht die dafür notwendigen Schritte und Komponenten:

![Passthrough-Authentifizierung](./media/how-to-connect-pta-how-it-works/pta2.png)

## <a name="next-steps"></a>Nächste Schritte
- [Aktuelle Einschränkungen:](how-to-connect-pta-current-limitations.md) Informationen zu den unterstützten und nicht unterstützten Szenarien
- [Schnellstart:](how-to-connect-pta-quick-start.md) Aktivieren und Ausführen der Passthrough-Authentifizierung von Azure AD
- [Migrieren von AD FS zur Passthrough-Authentifizierung](https://aka.ms/adfstoPTADP): Ein detaillierter Leitfaden zur Migration von AD FS (oder anderen Verbundtechnologien) zur Passthrough-Authentifizierung
- [Smart Lockout:](../authentication/howto-password-smart-lockout.md) Konfigurieren der Smart Lockout-Funktion für Ihren Mandanten, um Benutzerkonten zu schützen
- [Häufig gestellte Fragen:](how-to-connect-pta-faq.md) Antworten auf häufig gestellte Fragen
- [Problembehandlung:](tshoot-connect-pass-through-authentication.md) Informationen zum Beheben von allgemeinen Problemen, die bei der Passthrough-Authentifizierung auftreten können
- [Ausführliche Informationen zur Sicherheit:](how-to-connect-pta-security-deep-dive.md) Technische Informationen zur Passthrough-Authentifizierung
- [Nahtloses SSO mit Azure AD](how-to-connect-sso.md): Informationen zu dieser Ergänzungsfunktion
- [UserVoice:](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) Anfordern neuer Features über das Azure Active Directory-Forum

