---
title: Azure Active Directory Identity Protection | Microsoft Docs
description: Erfahren Sie, wie Sie mit Azure AD Identity Protection für Angreifer die Möglichkeit einschränken können, eine kompromittierte Identität oder ein Gerät auszunutzen, und wie Sie eine Identität oder ein Gerät schützen, das zuvor vermutlich oder mit Sicherheit kompromittiert war.
services: active-directory
keywords: Azure Active Directory Identity Protection, Cloud App Discovery, Verwalten von Anwendungen, Sicherheit, Risiko, Risikostufe, Sicherheitsrisiko, Sicherheitsrichtlinie
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2019
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d9b0c80522a988447b2753953c0c51b058fdd43
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "66015214"
---
# <a name="what-is-azure-active-directory-identity-protection"></a>Was ist Azure Active Directory Identity Protection?

Mit Azure Active Directory Identity Protection können Organisationen automatische Antworten auf erkannte verdächtige Aktionen im Zusammenhang mit Benutzeridentitäten konfigurieren.

## <a name="get-started"></a>Erste Schritte

Microsoft sichert cloudbasierte Identitäten bereits seit über zehn Jahren. Mit Azure Active Directory Identity Protection können Sie in Ihrer Umgebung die gleichen Schutzsysteme einsetzen, die von Microsoft zum Sichern von Identitäten verwendet werden.

Die allermeisten Sicherheitsverletzungen kommen vor, wenn Angreifer Zugriff auf eine Umgebung erhalten, indem sie die Identität eines Benutzers stehlen. Angreifer sind im Laufe der Zeit immer besser darin geworden, Drittanbieter-Sicherheitsverletzungen auszunutzen und ausgefeilte Phishingangriffe zu starten. Sobald sich ein Angreifer Zugriff auf ein mit geringen Rechten ausgestattetes Benutzerkonto verschafft hat, ist es für ihn relativ einfach, per Seitwärtsbewegung Zugriff auf wichtige Unternehmensressourcen zu erlangen.

Daher müssen Sie:

- Alle Identitäten unabhängig von deren Berechtigungsstufe schützen

- Proaktiv verhindern, dass gefährdete Identitäten missbraucht werden

Die Ermittlung von kompromittierten Identitäten ist keine einfache Aufgabe. Azure Active Directory verwendet adaptive Machine Learning-Algorithmen und -Heuristiken zum Erkennen von Anomalien und verdächtigen Vorfällen, die auf potenziell gefährdete Identitäten hinweisen. Mit diesen Daten generiert Identity Protection Berichte und Warnungen, damit Sie die erkannten Probleme auswerten und entsprechende Aktionen zur Lösung oder Korrektur durchführen können.

Azure Active Directory Identity Protection ist viel mehr als nur ein Tool für die Überwachung und Berichterstellung. Zum Schutz der Identitäten Ihrer Organisation können Sie risikobasierte Richtlinien konfigurieren, die automatisch auf erkannte Probleme reagieren, wenn eine angegebene Risikostufe erreicht wurde. Mit diesen Richtlinien in Verbindung mit anderen Kontrollelementen für den bedingten Zugriff von Azure Active Directory und [Enterprise Mobility + Security](https://docs.microsoft.com/enterprise-mobility-security/) (EMS) können adaptive Korrekturaktionen entweder blockiert oder initiiert werden. Hierzu gehören Kennwortzurücksetzungen und die Durchsetzung der Multi-Factor Authentication.

### <a name="identity-protection-capabilities"></a>Funktionen von Identity Protection

**Erkennen von Sicherheitsrisiken und gefährdeten Konten:**  

- Bereitstellen von benutzerdefinierten Empfehlungen zur Verbesserung der allgemeinen Sicherheit, indem Sicherheitsrisiken aufgedeckt werden
- Berechnen der Risikostufen für die Anmeldung
- Berechnen von Benutzerrisikostufen

**Untersuchen von Risikoereignissen:**

- Senden von Benachrichtigungen für Risikoereignisse
- Untersuchen von Risikoereignissen mit relevanten und kontextbezogenen Informationen
- Bereitstellen grundlegender Workflows zum Nachverfolgen von Untersuchungen
- Bereitstellen des einfachen Zugriffs auf Korrekturaktionen, z. B. Kennwortzurücksetzung

**Risikobasierte Richtlinien für bedingten Zugriff:**

- Richtlinie für Abwehrmaßnahmen bei risikobehafteten Anmeldungen, indem Anmeldungen blockiert oder die Multi-Factor Authentication erzwungen wird
- Richtlinie zum Blockieren oder Schützen risikobehafteter Benutzerkonten
- Richtlinie zur Durchsetzung der Registrierung von Benutzern für die Multi-Factor Authentication

## <a name="identity-protection-roles"></a>Identity Protection-Rollen

Sie können für einen Lastenausgleich bei Managementaktivitäten zu Ihrer Implementierung von Identity Protection verschiedene Rollen zuweisen. Azure AD Identity Protection unterstützt drei Verzeichnisrollen:

| Rolle | Möglich | Nicht möglich |
| :-- | --- | --- |
| Globaler Administrator | Vollzugriff auf Identity Protection, Integrieren von Identity Protection| |
| Sicherheitsadministrator | Vollzugriff auf Identity Protection | Integrieren von Identity Protection, Zurücksetzen von Kennwörtern für einen Benutzer |
| Sicherheitsleseberechtigter | Schreibgeschützter Zugriff auf Identity Protection | Integrieren von Identity Protection, Bereinigen von Benutzern, Konfigurieren von Richtlinien, Zurücksetzen von Kennwörtern |

Weitere Informationen finden Sie unter [Zuweisen von Administratorrollen in Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).

## <a name="detection"></a>Erkennung

### <a name="vulnerabilities"></a>Sicherheitsrisiken

Azure Active Directory Identity Protection analysiert Ihre Konfiguration und erkennt Sicherheitsrisiken, die sich auf die Identitäten Ihrer Benutzers auswirken können. Weitere Details finden Sie unter [Von Azure Active Directory Identity Protection erkannte Sicherheitsrisiken](vulnerabilities.md).

### <a name="risk-events"></a>Risikoereignisse

Azure Active Directory verwendet adaptive Machine Learning-Algorithmen und -Heuristiken, um verdächtige Aktivitäten im Zusammenhang mit den Identitäten Ihrer Benutzer zu erkennen. Das System erstellt für jede erkannte verdächtige Aktivität einen Datensatz. Diese Datensätze werden auch als Risikoereignisse bezeichnet.  
Weitere Details finden Sie unter [Azure Active Directory-Risikoereignisse](../active-directory-identity-protection-risk-events.md).

## <a name="investigation"></a>Untersuchung

Ihre Navigation durch Identity Protection beginnt normalerweise mit dem Identity Protection-Dashboard.

![Wiederherstellung](./media/overview/1000.png "Wiederherstellung")

Über das Dashboard haben Sie Zugriff auf folgende Komponenten:

- Berichte, z.B. **Benutzer mit Risikokennzeichnung**, **Risikoereignisse** und **Sicherheitsrisiken**
- Einstellungen, z.B. die Konfiguration von **Sicherheitsrichtlinien**, **Benachrichtigungen** und **Registrierung für mehrstufige Authentifizierung**

Hier beginnen Sie in der Regel mit dem Untersuchungsprozess. Dies ist der Prozess der Überprüfung von Aktivitäten, Protokollen und anderen relevanten Informationen zu einem Risikoereignis, um zu entscheiden, ob Schritte zur Fehlerbeseitigung oder Lösung erforderlich sind. Außerdem ermitteln Sie, ob und wie die Identität kompromittiert und wie die kompromittierte Identität verwendet wurde.

Sie können Ihre Untersuchungsaktivitäten mit den [Benachrichtigungen](notifications.md) verknüpfen, die Azure Active Directory Protection per E-Mail sendet.

## <a name="policies"></a>Richtlinien

Um automatische Antworten zu implementieren, bietet Ihnen Azure Active Directory Identity Protection drei Richtlinien:

- [Multi-Factor Authentication-Registrierungsrichtlinie](howto-mfa-policy.md)
- [Richtlinie zum Benutzerrisiko](howto-user-risk-policy.md)
- [Richtlinie zum Anmelderisiko](howto-sign-in-risk-policy.md)

## <a name="license-requirements"></a>Lizenzanforderungen

[!INCLUDE [Active Directory P2 license](../../../includes/active-directory-p2-license.md)]

## <a name="next-steps"></a>Nächste Schritte

- [Channel 9: Show zu Azure AD und Identität: Identity Protection Preview](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview) (Channel 9: Azure AD- und Identity-Show: Vorschau von Identity Protection)
- [Aktivieren von Azure Active Directory Identity Protection](enable.md)
