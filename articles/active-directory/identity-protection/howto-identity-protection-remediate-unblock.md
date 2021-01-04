---
title: Behandeln von Risiken und Aufheben der Blockierung von Benutzern in Azure AD Identity Protection
description: Lernen Sie die Optionen zum Schließen von aktiven Risikoerkennungen kennen.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 10/06/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6dd918aef85deefc23771413c3eb7b92f1189d39
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94835797"
---
# <a name="remediate-risks-and-unblock-users"></a>Behandeln von Risiken und Aufheben der Blockierung von Benutzern

Nach Abschluss Ihrer [Untersuchung](howto-identity-protection-investigate-risk.md) möchten Sie vielleicht Maßnahmen ergreifen, um das Risiko zu beheben oder die Blockierung von Benutzern aufzuheben. Organisationen haben auch die Möglichkeit, die automatische Behandlung mithilfe ihrer [Risikorichtlinien](howto-identity-protection-configure-risk-policies.md) zu aktivieren. Organisationen sollten versuchen, alle angezeigten Risikoerkennungen innerhalb eines Zeitraums zu schließen, der für Ihre Organisation akzeptabel ist. Microsoft empfiehlt, Ereignisse so schnell wie möglich zu schließen, da Zeit im Umgang mit Risiken eine Rolle spielt.

## <a name="remediation"></a>Wiederherstellung

Alle aktiven Risikoerkennungen fließen in die Berechnung eines Werts ein, der als „Benutzerrisikostufe“ bezeichnet wird. Die Benutzerrisikostufe ist ein Indikator (niedrig, mittel, hoch) für die Wahrscheinlichkeit, dass ein Konto kompromittiert wurde. Als Administrator sollten Sie dafür sorgen, dass alle Risikoerkennungen geschlossen werden, damit die betroffenen Benutzer nicht mehr gefährdet sind.

Einige Risikoerkennungen können von Identity Protection als „Geschlossen (System)“ gekennzeichnet werden, da die Ereignisse nicht mehr als riskant eingestuft werden.

Administratoren stehen die folgenden Optionen zum Beheben von Risiken zur Verfügung:

- Eigenwartung mit Risikorichtlinie
- Manuelles Zurücksetzen des Kennworts
- Benutzerrisiko verwerfen
- Einzelne Risikoerkennungen manuell schließen

### <a name="self-remediation-with-risk-policy"></a>Eigenwartung mit Risikorichtlinie

Wenn Sie Benutzern die Eigenwartung mit Azure AD Multi-Factor Authentication (MFA) und Self-Service-Kennwortzurücksetzung (SSPR) in Ihren Risikorichtlinien erlauben, können sie ihre bei Erkennung eines Risikos erfolgte Blockierung selbst aufheben. Diese Erkennungen werden dann als geschlossen betrachtet. Benutzer müssen sich zuvor für Azure AD MFA und SSPR registriert haben, um diese Aktionen zu verwenden, wenn Risiken erkannt werden.

Einige Erkennungen lösen möglicherweise keine Risiken in der Stufe aus, in der eine Eigenwartung durch den Benutzer erforderlich ist, aber Administratoren sollten diese Erkennungen trotzdem auswerten. Administratoren können feststellen, dass zusätzliche Maßnahmen erforderlich sind, wie das [Blockieren des Zugriffs von bestimmten Orten](../conditional-access/howto-conditional-access-policy-location.md) oder das Reduzieren des akzeptablen Risikos in ihren Richtlinien.

### <a name="manual-password-reset"></a>Manuelles Zurücksetzen des Kennworts

Wenn keine Kennwortzurücksetzung durch eine Benutzerrisikorichtlinie angefordert werden kann, können Administratoren alle Risikoerkennungen für einen Benutzer durch eine manuelle Kennwortzurücksetzung schließen.

Administratoren erhalten beim Zurücksetzen eines Kennworts für Ihre Benutzer zwei Optionen:

- **Temporäres Kennwort generieren**: Durch das Generieren eines temporären Kennworts können Sie eine Identität umgehend in einen sicheren Status zurückversetzen. Diese Methode erfordert, mit den betroffenen Benutzern Kontakt aufzunehmen, da diese das temporäre Kennwort kennen müssen. Da das Kennwort temporär ist, wird der Benutzer bei der nächsten Anmeldung aufgefordert, ein neues Kennwort festzulegen.

- **Benutzer zum Zurücksetzen des Kennworts auffordern**: Wenn der Benutzer zum Zurücksetzen von Kennwörtern aufgefordert wird, ist eine Self-Service-Wiederherstellung ohne Kontakt mit dem Helpdesk oder einem Administrator möglich. Diese Methode gilt nur für Benutzer, die für Azure AD MFA und SSPR registriert sind. Für Benutzer, die nicht registriert sind, ist diese Option nicht verfügbar.

### <a name="dismiss-user-risk"></a>Benutzerrisiko verwerfen

Wenn eine Kennwortzurücksetzung für Sie keine Option ist, da der Benutzer z.B. gelöscht wurde, können Sie die Benutzerrisikoerkennungen verwerfen.

Wenn Sie auf **Alle Ereignisse verwerfen** klicken, werden alle Ereignisse geschlossen, und der betroffene Benutzer ist nicht mehr gefährdet. Da diese Methode jedoch keine Auswirkungen auf das vorhandene Kennwort hat, bringt sie die zugehörige Identität nicht wieder in einen sicheren Zustand. 

### <a name="close-individual-risk-detections-manually"></a>Einzelne Risikoerkennungen manuell schließen

Sie können einzelne Risikoerkennungen manuell schließen. Durch das manuelle Schließen von Risikoerkennungen können Sie die Benutzerrisikostufe herabsetzen. Risikoerkennungen werden in der Regel als Reaktion auf eine entsprechende Untersuchung manuell geschlossen. Dies ist beispielsweise der Fall, wenn ein Gespräch mit dem Benutzer ergibt, dass keine aktive Risikoerkennung mehr erforderlich ist. 
 
Beim manuellen Schließen von Risikoerkennungen können Sie die folgenden Aktionen ausführen, um den Status einer Risikoerkennung zu ändern:

- Benutzergefährdung bestätigen
- Benutzerrisiko verwerfen
- Anmeldung als sicher bestätigen
- Anmeldung als gefährdet bestätigen

## <a name="unblocking-users"></a>Benutzer werden entsperrt

Ein Administrator kann eine Anmeldung basierend auf seiner Risikorichtlinie oder seinen Untersuchungen blockieren. Eine Blockierung kann auf der Grundlage eines Anmelde- oder Benutzerrisikos erfolgen.

### <a name="unblocking-based-on-user-risk"></a>Aufheben einer auf Benutzerrisiken basierenden Blockierung

Zum Aufheben einer Blockierung eines Kontos, das aufgrund eines Benutzerrisikos blockiert wurde, können Administratoren die folgenden Optionen verwenden:

1. **Zurücksetzen des Kennworts:** Sie können das Kennwort des Benutzers zurücksetzen.
1. **Benutzerrisiko verwerfen**: Die Benutzerrisikorichtlinie blockiert einen Benutzer, wenn die konfigurierte Benutzerrisikostufe für die Blockierung des Zugriffs erreicht wurde. Sie können die Risikostufe eines Benutzers verringern, indem Sie das Benutzerrisiko verwerfen oder gemeldete Risikoerkennungen manuell schließen.
1. **Benutzer von der Richtlinie ausschließen**: Wenn Sie vermuten, dass die aktuelle Konfiguration Ihrer Anmelderichtlinie Probleme für bestimmte Benutzer verursacht, können Sie diese Benutzer von der Richtlinie ausschließen. Weitere Informationen finden Sie im Abschnitt „Ausschlüsse“ im Artikel [ Konfigurieren und Aktivieren von Risikorichtlinien](howto-identity-protection-configure-risk-policies.md#exclusions).
1. **Deaktivieren der Richtlinie:** Wenn Sie vermuten, dass Ihre Richtlinienkonfiguration Probleme für alle Benutzer verursacht, können Sie die Richtlinie deaktivieren. Weitere Informationen finden Sie im Artikel [ Konfigurieren und Aktivieren von Risikorichtlinien](howto-identity-protection-configure-risk-policies.md).

### <a name="unblocking-based-on-sign-in-risk"></a>Aufheben einer auf Anmelderisiken basierenden Blockierung

Zum Aufheben einer Blockierung eines Kontos, das basierend auf Anmelderisiken blockiert wurde, können Administratoren die folgenden Optionen verwenden:

1. **Anmelden über einen bekannten Ort oder ein bekanntes Gerät:** Die Blockierung einer verdächtigen Anmeldung ist häufig auf Anmeldeversuche über einen unbekannten Ort oder ein unbekanntes Gerät zurückzuführen. Um zu ermitteln, ob dies die Ursache für die Blockierung ist, muss sich der betroffene Benutzer lediglich über einen bekannten Ort oder über ein bekanntes Gerät anmelden.
1. **Benutzer von der Richtlinie ausschließen**: Wenn Sie vermuten, dass die aktuelle Konfiguration Ihrer Anmelderichtlinie Probleme für bestimmte Benutzer verursacht, können Sie diese Benutzer von der Richtlinie ausschließen. Weitere Informationen finden Sie im Abschnitt „Ausschlüsse“ im Artikel [ Konfigurieren und Aktivieren von Risikorichtlinien](howto-identity-protection-configure-risk-policies.md#exclusions).
1. **Deaktivieren der Richtlinie:** Wenn Sie vermuten, dass Ihre Richtlinienkonfiguration Probleme für alle Benutzer verursacht, können Sie die Richtlinie deaktivieren. Weitere Informationen finden Sie im Artikel [ Konfigurieren und Aktivieren von Risikorichtlinien](howto-identity-protection-configure-risk-policies.md).

## <a name="powershell-preview"></a>Vorschauversion von PowerShell

Mithilfe des PowerShell SDK-Vorschaumoduls von Microsoft Graph können Organisationen das Risiko unter Verwendung von PowerShell verwalten. Die Vorschaumodule und den Beispielcode finden Sie im [GitHub-Repository für Azure AD](https://github.com/AzureAD/IdentityProtectionTools).

## <a name="next-steps"></a>Nächste Schritte

Eine Übersicht über Azure AD Identity Protection finden Sie [in diesem Artikel](overview-identity-protection.md).
