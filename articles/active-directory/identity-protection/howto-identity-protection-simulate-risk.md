---
title: Simulieren von Risikoerkennungen in Azure AD Identity Protection
description: Erfahren Sie, wie Sie Risikoerkennungen in Identity Protection simulieren.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7eafeaf59757fcda978fa89b4bc2f9882b769e48
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94835899"
---
# <a name="simulating-risk-detections-in-identity-protection"></a>Simulieren von Risikoerkennungen in Identity Protection

Administratoren möchten möglicherweise Risiken in Ihrer Umgebung simulieren, um die folgenden Aufgaben zu erledigen:

- Daten in der Identity Protection-Umgebung durch Simulieren von Risikoerkennungen und Sicherheitsrisiken aufzufüllen.
- Richtlinien für den risikobasierten bedingten Zugriff einzurichten und die Auswirkungen dieser Richtlinien zu testen.

Dieser Artikel enthält die Schritte zum Simulieren der folgenden Risikoerkennungstypen:

- Anonyme IP-Adresse (leicht)
- Ungewöhnliche Anmeldeeigenschaften (mittel)
- Ungewöhnlicher Ortswechsel (schwer)

Andere Risikoerkennungen können nicht auf sichere Weise simuliert werden.

Im Artikel [Was bedeutet Risiko?](concept-identity-protection-risks.md) finden Sie weitere Informationen zu den einzelnen Risikoerkennungen.

## <a name="anonymous-ip-address"></a>Anonyme IP-Adresse

Für die folgenden Schritte müssen folgende Voraussetzungen erfüllt sein:

- Sie benötigen den [Tor-Browser](https://www.torproject.org/projects/torbrowser.html.en), um anonyme IP-Adressen zu simulieren. Möglicherweise müssen Sie einen virtuellen Computer verwenden, wenn Ihre Organisation die Verwendung des Tor-Browsers einschränkt.
- Sie benötigen ein Testkonto, das noch nicht für Azure AD Multi-Factor Authentication registriert ist.

**Führen Sie die folgende Schritte aus, um eine Anmeldung über eine anonyme IP-Adresse zu simulieren,** :

1. Navigieren Sie im [Tor-Browser](https://www.torproject.org/projects/torbrowser.html.en) zu [https://myapps.microsoft.com](https://myapps.microsoft.com).   
2. Geben Sie die Anmeldeinformationen des Kontos ein, das im Bericht **Anmeldungen von anonymen IP-Adressen** enthalten sein soll.

Die Anmeldung wird innerhalb von fünf bis zehn Minuten auf dem Identity Protection Dashboard angezeigt. 

## <a name="unfamiliar-sign-in-properties"></a>Ungewöhnliche Anmeldeeigenschaften

Um unbekannte Standorte zu simulieren, müssen Sie sich von einem Ort und mit einem Gerät anmelden, bei dem das Testkonto noch nicht angemeldet war.

Das unten stehende Verfahren verwendet folgende neu erstellte Elemente:

- Eine VPN-Verbindung zum Simulieren eines neuen Standorts
- Einen virtuellen Computer zum Simulieren eines neuen Geräts

Für das folgende Verfahren müssen Sie ein Benutzerkonto verwenden, das folgende Voraussetzungen erfüllt:

- Anmeldeverlauf von mindestens 30 Tagen
- Azure AD Multi-Factor Authentication aktiviert.

**Gehen Sie folgendermaßen vor, um eine Anmeldung von einem unbekannten Ort zu simulieren**:

1. Wenn Sie sich mit dem Testkonto anmelden, verursachen Sie einen Fehler bei der Multi-Factor Authentication (MFA)-Abfrage.
2. Navigieren Sie in Ihrem neuen VPN zu [https://myapps.microsoft.com](https://myapps.microsoft.com), und geben Sie die Anmeldeinformationen Ihres Testkontos ein.

Die Anmeldung wird innerhalb von fünf bis zehn Minuten auf dem Identity Protection Dashboard angezeigt.

## <a name="atypical-travel"></a>Ungewöhnlicher Ortswechsel

Das Simulieren des ungewöhnlichen Ortswechsels ist schwierig, da der Algorithmus einen Machine Learning-Ansatz nutzt, um falsch positive Ergebnisse auszusieben, z.B. der ungewöhnliche Ortswechsel vertrauter Geräte oder Anmeldungen über VPNs, die von anderen Benutzern im Verzeichnis verwendet werden. Außerdem sind für den Algorithmus ein Anmeldeverlauf von 14 Tagen und zehn Anmeldungen des Benutzers erforderlich, bevor mit dem Generieren von Risikoerkennungen begonnen wird. Aufgrund der komplexen Machine Learning-Modelle und der oben genannten Regeln besteht die Möglichkeit, dass die nachfolgenden Schritte nicht zu einer Risikoerkennung führen. Sie können diese Schritte für mehrere Azure AD-Konten replizieren, um diese Erkennung zu simulieren.

**Führen Sie die folgenden Schritte aus, um eine Risikoerkennung vom Typ „Ungewöhnlicher Ortswechsel“ zu simulieren**:

1. Navigieren Sie in Ihrem Standardbrowser zu [https://myapps.microsoft.com](https://myapps.microsoft.com).  
2. Geben Sie die Anmeldeinformationen des Kontos ein, für das eine Risikoerkennung vom Typ „Ungewöhnlicher Ortswechsel“ generiert werden soll.
3. Ändern Sie Ihren Benutzer-Agent. Sie können den Benutzer-Agent in Microsoft Edge von Entwicklertools (F12) ändern.
4. Ändern Sie Ihre IP-Adresse. Sie können die IP-Adresse ändern, indem Sie ein VPN oder ein Tor-Add-On verwenden oder unter Azure einen neuen virtuellen Computer in einem anderen Rechenzentrum erstellen.
5. Melden Sie sich unter [https://myapps.microsoft.com](https://myapps.microsoft.com) mit den gleichen Anmeldeinformationen wie vorher und innerhalb von wenigen Minuten nach der vorherigen Anmeldung an.

Die Anmeldung wird innerhalb von zwei bis vier Stunden auf dem Identity Protection Dashboard angezeigt.

## <a name="testing-risk-policies"></a>Testen von Risikorichtlinien

Dieser Abschnitt erläutert die Schritte zum Testen der Benutzer- und Anmelderisikorichtlinien, die Sie im Artikel [ Konfigurieren und Aktivieren von Risikorichtlinien](howto-identity-protection-configure-risk-policies.md) erstellt haben.

### <a name="user-risk-policy"></a>Richtlinie zum Benutzerrisiko

Um eine Benutzerrisiko-Sicherheitsrichtlinie zu testen, führen Sie die folgenden Schritte aus:

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).
1. Navigieren Sie zu **Azure Active Directory** > **Sicherheit** > **Übersicht**.
1. Wählen Sie **Benutzerrisikorichtlinie konfigurieren** aus.
   1. Unter **Zuweisungen**
      1. **Benutzer**: Wählen Sie **Alle Benutzer** oder **Einzelne Benutzer und Gruppen auswählen** aus, wenn Sie den Rollout einschränken.
         1. Optional können Sie Benutzer aus der Richtlinie ausschließen.
      1. **Bedingungen** - **Benutzerrisiko**: Die Empfehlung von Microsoft lautet, diese Option auf **Hoch** festzulegen.
   1. Unter **Steuerelemente**
      1. **Zugriff**: Die Empfehlung von Microsoft lautet, den **Zugriff zuzulassen** und eine **Kennwortänderung anzufordern**.
   1. **Erzwingen der Richtlinie** - **Aus**
   1. **Speichern**: Diese Aktion kehrt zur Seite **Übersicht** zurück.
1. Erhöhen Sie das Benutzerrisiko eines Testkontos, indem Sie beispielsweise eine der Risikoerkennungen mehrmals simulieren.
1. Warten Sie einige Minuten, und vergewissern Sie sich dann, dass die Risikostufe für den Benutzer angehoben wurde. Wenn dies nicht der Fall ist, simulieren Sie weitere Risikoerkennungen für den Benutzer.
1. Kehren Sie zu ihrer Risikorichtlinie zurück, und aktivieren Sie **Richtlinie erzwingen**, und **speichern** Sie Ihre Richtlinienänderung.
1. Sie können den auf dem Benutzerrisiko basierenden bedingten Zugriff jetzt testen, indem Sie sich mit einem Benutzer anmelden, für den eine erhöhte Risikostufe gilt.

### <a name="sign-in-risk-security-policy"></a>Anmelderisiko-Sicherheitsrichtlinie

Führen Sie die folgenden Schritte aus, um eine Anmelderisiko-Sicherheitsrichtlinie zu testen:

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).
1. Navigieren Sie zu **Azure Active Directory** > **Sicherheit** > **Übersicht**.
1. Wählen Sie **Anmelderisikorichtlinie konfigurieren** aus.
   1. Unter **Zuweisungen**
      1. **Benutzer**: Wählen Sie **Alle Benutzer** oder **Einzelne Benutzer und Gruppen auswählen** aus, wenn Sie den Rollout einschränken.
         1. Optional können Sie Benutzer aus der Richtlinie ausschließen.
      1. **Bedingungen** - **Anmelderisiko**: Die Empfehlung von Microsoft lautet, diese Option auf **Mittel und höher** festzulegen.
   1. Unter **Steuerelemente**
      1. **Zugriff**: Die Empfehlung von Microsoft lautet, den **Zugriff zuzulassen** und eine **mehrstufige Authentifizierung anzufordern**.
   1. **Richtlinie erzwingen** - **Ein**
   1. **Speichern**: Diese Aktion kehrt zur Seite **Übersicht** zurück.
1. Sie können den auf dem Anmelderisiko basierenden bedingten Zugriff jetzt testen, indem Sie sich mithilfe einer potenziell risikobehafteten Sitzung anmelden (z. B. durch Verwendung des Tor-Browsers). 

## <a name="next-steps"></a>Nächste Schritte

- [Was bedeutet Risiko?](concept-identity-protection-risks.md)

- [Vorgehensweise: Konfigurieren und Aktivieren von Risikorichtlinien](howto-identity-protection-configure-risk-policies.md)

- [Azure Active Directory Identity Protection](overview-identity-protection.md)
