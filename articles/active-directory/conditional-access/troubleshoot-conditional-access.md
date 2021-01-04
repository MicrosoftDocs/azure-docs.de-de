---
title: Beheben von Anmeldeproblemen bei bedingtem Zugriff – Azure Active Directory
description: In diesem Artikel wird beschrieben, was zu tun ist, wenn Ihre Richtlinien für den bedingten Zugriff zu unerwarteten Ergebnissen führen.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12f722977329bd5d79d4d0e410a29c730faf00c5
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/17/2020
ms.locfileid: "92145098"
---
# <a name="troubleshooting-sign-in-problems-with-conditional-access"></a>Beheben von Anmeldeproblemen bei bedingtem Zugriff

Anhand der Informationen in diesem Artikel und mithilfe von Fehlermeldungen und dem Azure AD-Anmeldeprotokoll können Sie Probleme bei unerwarteten Anmeldeergebnissen im Zusammenhang mit dem bedingten Zugriff beheben.

## <a name="select-all-consequences"></a>Auswählen von Konsequenzen vom Typ „Alle“

Das Framework für bedingten Zugriff bietet Ihnen mehr Flexibilität bei der Konfiguration. Mehr Flexibilität bedeutet jedoch auch, dass Sie jede Konfigurationsrichtlinie vor dem Veröffentlichen sorgfältig prüfen sollten, um unerwünschte Ergebnisse zu vermeiden. Achten Sie in diesem Fall besonders auf Zuweisungen, die sich auf komplette Sätze auswirken, z.B. **alle Benutzer/Gruppen/Cloud-Apps**.

Organisationen sollten die folgenden Konfigurationen vermeiden:

**Für alle Benutzer, alle Cloud-Apps:**

- **Zugriff blockieren:** Diese Konfiguration blockiert Ihre gesamte Organisation.
- **Markieren des Geräts als kompatibel erforderlich:** Für Benutzer, die ihre Geräte noch nicht registriert haben, blockiert diese Richtlinie den gesamten Zugriff, einschließlich des Zugriffs auf das Intune-Portal. Wenn Sie ein Administrator ohne registriertes Gerät sind, verhindert diese Richtlinie auch, dass Sie in das Azure-Portal zurückkehren und die Richtlinie ändern können.
- **Require Hybrid Azure AD domain joined device** (In Azure AD Hybrid-Domäne eingebundenes Gerät erforderlich): Diese Richtlinie blockiert potenziell den Zugriff für alle Benutzer in Ihrer Organisation, wenn sie nicht über in Azure AD Hybrid eingebundene Geräte verfügen.
- **App-Schutzrichtlinie erforderlich** : Diese Richtlinie zum Blockieren des Zugriffs kann potenziell auch den Zugriff für alle Benutzer in Ihrer Organisation blockieren, wenn Sie nicht über eine Intune-Richtlinie verfügen. Wenn Sie als Administrator nicht über eine Clientanwendung mit einer Intune-App-Schutzrichtlinie verfügen, verhindert diese Richtlinie, dass Sie wieder in Portale wie Intune und Azure gelangen.

**Für alle Benutzer, alle Cloud-Apps, alle Geräteplattformen:**

- **Zugriff blockieren:** Diese Konfiguration blockiert Ihre gesamte Organisation.

## <a name="conditional-access-sign-in-interrupt"></a>Unterbrechung der Anmeldung bei bedingtem Zugriff

Die erste Möglichkeit besteht darin, die angezeigte Fehlermeldung zu überprüfen. Bei Problemen mit der Anmeldung über einen Webbrowser enthält die eigentliche Fehlerseite ausführliche Informationen. Diese Informationen können lediglich das Problem beschreiben und eine Lösung vorschlagen.

![Anmeldefehler – kompatibles Gerät erforderlich](./media/troubleshoot-conditional-access/image1.png)

Die Meldung für den obigen Fehler besagt, dass der Zugriff auf die Anwendung nur von Geräten oder Clientanwendungen erfolgen kann, welche die Richtlinie für die Verwaltung mobiler Geräte im Unternehmen erfüllen. In diesem Fall erfüllen die Anwendung und das Gerät diese Richtlinie nicht.

## <a name="azure-ad-sign-in-events"></a>Azure AD-Anmeldeereignisse

Die zweite Methode zum Abrufen detaillierter Informationen zu der Anmeldeunterbrechung besteht darin, die Azure AD-Anmeldeereignisse zu überprüfen, um festzustellen, welche Richtlinie(n) für den bedingten Zugriff angewendet wurde(n) und aus welchem Grund.

Weitere Informationen zu diesem Problem erhalten Sie, wenn Sie auf der ersten Fehlerseite auf **Weitere Informationen** klicken. Durch das Klicken auf **Weitere Informationen** werden Informationen zur Problembehandlung angezeigt, die beim Durchsuchen der Azure AD-Anmeldeereignisse für ein bestimmtes Fehlerereignis, das dem Benutzer angezeigt wurde, oder beim Öffnen eines Supportfalls bei Microsoft hilfreich sein können.

![Weitere Informationen zu einer unterbrochenen Anmeldung im Webbrowser bei bedingtem Zugriff](./media/troubleshoot-conditional-access/image2.png)

Gehen Sie wie folgt vor, um herauszufinden, welche Richtlinie(n) für den bedingten Zugriff angewendet wurde(n) und aus welchem Grund.

1. Melden Sie sich als globaler Administrator, Sicherheitsadministrator oder globaler Leser im **Azure-Portal** an.
1. Navigieren Sie zu **Azure Active Directory** > **Anmeldungen**.
1. Suchen Sie nach dem Ereignis für die zu überprüfende Anmeldung. Filtern Sie unnötige Informationen heraus, indem Sie Filter und Spalten hinzufügen oder entfernen.
   1. Fügen Sie Filter hinzu, um den Bereich einzugrenzen:
      1. **Korrelations-ID** zum Untersuchen eines bestimmten Ereignisses.
      1. **Bedingter Zugriff** zum Anzeigen von Richtlinienfehlern und Richtlinienkonformität. Legen Sie den Filter so fest, dass nur Fehler angezeigt werden, um die Ergebnisse einzugrenzen.
      1. **Benutzername** zum Anzeigen von Informationen zu bestimmten Benutzern.
      1. **Datum** zur Festlegung des fraglichen Zeitraums.

   ![Auswählen des Filters für den bedingten Zugriff im Anmeldeprotokoll](./media/troubleshoot-conditional-access/image3.png)

1. Wenn Sie das Anmeldeereignis, das dem Anmeldefehler des Benutzers entspricht, gefunden haben, wählen Sie die Registerkarte **Bedingter Zugriff** aus. Auf der Registerkarte „Bedingter Zugriff“ wird die spezielle Richtlinie (ggf. auch mehrere Richtlinien) angezeigt, die zur Unterbrechung der Anmeldung geführt hat.
   1. Die Informationen auf der Registerkarte **Problembehandlung und Support** geben möglicherweise eindeutig Auskunft darüber, warum bei der Anmeldung ein Fehler aufgetreten ist (z. B. ein Gerät, das die Konformitätsanforderungen nicht erfüllt).
   1. Zur weiteren Untersuchung führen Sie einen Drilldown in die Konfiguration der Richtlinien durch, indem Sie auf den **Richtliniennamen** klicken. Durch Klicken auf den **Richtliniennamen** wird für die ausgewählte Richtlinie die Benutzeroberfläche für die Richtlinienkonfiguration angezeigt. Hier können Sie die Richtlinie überprüfen und bearbeiten.
   1. Der **Clientbenutzer** und die **Gerätedetails** , die für die Bewertung der Richtlinie für bedingten Zugriff verwendet wurden, sind auch auf den Registerkarten **Grundlegende Infos** , **Standort** , **Geräteinformationen** , **Authentifizierungsdetails** und **Weitere Details** des Anmeldeereignisses verfügbar.

### <a name="policy-details"></a>Richtliniendetails

Wenn Sie in einem Anmeldeereignis auf die drei Punkte rechts neben der Richtlinie klicken, werden die Richtliniendetails angezeigt. So erhalten Administratoren zusätzliche Informationen zum Grund für die erfolgreiche oder nicht erfolgreiche Anwendung einer Richtlinie.

   ![Registerkarte „Bedingter Zugriff“ des Anmeldeereignisses](./media/troubleshoot-conditional-access/image5.png)

   ![Richtliniendetails (Vorschau)](./media/troubleshoot-conditional-access/policy-details.png)

Auf der linken Seite werden die bei der Anmeldung erfassten Details angezeigt, auf der rechte Seite Informationen darüber, ob diese Details den Anforderungen der angewendeten Richtlinien für bedingten Zugriff entsprechen. Richtlinien für bedingten Zugriff gelten nur, wenn alle Bedingungen erfüllt oder nicht konfiguriert sind.

Wenn die im Ereignis angezeigten Informationen nicht ausreichen, um die Anmeldeergebnisse zu verstehen oder die Richtlinie anzupassen, um die gewünschten Ergebnisse zu erhalten, kann ein Supportfall geöffnet werden. Navigieren Sie zur Registerkarte **Problembehandlung und Support** des Anmeldeereignisses, und wählen Sie **Neue Supportanfrage erstellen** aus.

![Registerkarte „Problembehandlung und Support“ des Anmeldeereignisses](./media/troubleshoot-conditional-access/image6.png)

Geben Sie bei der Übermittlung des Vorfalls die Anforderungs-ID sowie Uhrzeit und Datum des Anmeldeereignisses in den Details an. Diese Informationen ermöglichen es dem Microsoft-Support, das betreffende Ereignis zu finden.

### <a name="conditional-access-error-codes"></a>Feldercodes bei bedingtem Zugriff

| Anmeldefehlercode | Fehlerzeichenfolge |
| --- | --- |
| 53000 | DeviceNotCompliant |
| 53001 | DeviceNotDomainJoined |
| 53002 | ApplicationUsedIsNotAnApprovedApp |
| 53003 | BlockedByConditionalAccess |
| 53004 | ProofUpBlockedDueToRisk |

## <a name="what-to-do-if-you-are-locked-out-of-the-azure-portal"></a>Was ist zu tun, wenn Ihr Zugriff auf das Azure-Portal gesperrt ist?

Wenn Ihr Zugriff auf das Azure-Portal aufgrund einer falschen Einstellung in einer Richtlinie für bedingten Zugriff gesperrt wurde, gehen Sie folgendermaßen vor:

- Überprüfen Sie, ob weitere Administratoren in Ihrer Organisation vorhanden sind, die noch nicht gesperrt sind. Ein Administrator mit Zugriff auf das Azure-Portal kann die Richtlinie deaktivieren, die Ihre Anmeldung verhindert. 
- Wenn keiner der Administratoren in Ihrer Organisation die Richtlinie aktualisieren kann, übermitteln Sie eine Supportanfrage. Der Microsoft-Support kann Richtlinien für bedingten Zugriff, die den Zugriff verhindern, überprüfen und nach Bestätigung aktualisieren.

## <a name="next-steps"></a>Nächste Schritte

- [Berichte zu Anmeldeaktivitäten im Azure Active Directory-Portal](../reports-monitoring/concept-sign-ins.md)
- [Beheben von Problemen beim bedingten Zugriff mit dem Was-wäre-wenn-Tool](troubleshoot-conditional-access-what-if.md)
