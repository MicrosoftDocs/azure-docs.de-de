---
title: Best Practices für den bedingten Zugriff in Azure Active Directory | Microsoft-Dokumentation
description: Erfahren Sie, was Sie wissen sollten und was Sie beim Konfigurieren von Richtlinien für den bedingten Zugriff vermeiden sollten.
services: active-directory
keywords: bedingter Zugriff auf Apps, bedingter Zugriff mit Azure AD, sicherer Zugriff auf Unternehmensressourcen, Richtlinien für bedingten Zugriff
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/25/2019
ms.author: joflore
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0fd18cdf5ef326e5836332d70aec89a101f249af
ms.sourcegitcommit: f9448a4d87226362a02b14d88290ad6b1aea9d82
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/07/2019
ms.locfileid: "66807752"
---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Best Practices für den bedingten Zugriff in Azure Active Directory

Mit dem [bedingten Zugriff von Azure Active Directory (Azure AD)](../active-directory-conditional-access-azure-portal.md) können Sie den Zugriff von autorisierten Benutzern auf Ihre Cloud-Apps steuern. Dieser Artikel bietet Folgendes:

- Wichtige Informationen 
- Aktionen, die Sie beim Konfigurieren von Richtlinien für den bedingten Zugriff vermeiden sollten 

Für diesen Artikel wird davon ausgegangen, dass Sie mit den Konzepten und Begriffen vertraut sind, die in [ Was ist bedingter Zugriff in Azure Active Directory?](../active-directory-conditional-access-azure-portal.md) beschrieben werden.



## <a name="whats-required-to-make-a-policy-work"></a>Was ist erforderlich, damit eine Richtlinie funktioniert?

Wenn Sie eine neue Richtlinie erstellen, werden keine Benutzer, Gruppen, Apps oder Zugriffssteuerungen ausgewählt.

![Cloud-Apps](./media/best-practices/02.png)


Damit Ihre Richtlinie funktioniert, müssen Sie Folgendes konfigurieren:


| Was           | Vorgehensweise                                  | Warum |
| :--            | :--                                  | :-- |
| **Cloud-Apps** |Wählen Sie mindestens eine App aus.  | Ziel einer Richtlinie für den bedingten Zugriff ist es, Ihnen die Steuerung des Zugriffs autorisierter Benutzer auf Cloud-Apps zu ermöglichen.|
| **Benutzer und Gruppen** | Wählen Sie mindestens einen Benutzer oder eine Gruppe aus, der bzw. die dazu autorisiert ist, auf die von Ihnen ausgewählten Cloud-Apps zuzugreifen. | Eine Richtlinie für den bedingten Zugriff, der keine Benutzer und Gruppen zugewiesen sind, wird niemals angewendet. |
| **Steuerelemente** | Wählen Sie mindestens eine Zugriffssteuerung aus. | Ihr Richtlinienprozessor muss wissen, was zu tun ist, wenn die Bedingungen erfüllt sind. |




## <a name="what-you-should-know"></a>Wichtige Informationen



### <a name="how-are-conditional-access-policies-applied"></a>Wie werden Richtlinien für bedingten Zugriff angewendet?

Beim Zugriff auf eine Cloud-App können mehrere Richtlinien für bedingten Zugriff gelten. In diesem Fall müssen alle geltenden Richtlinien erfüllt werden. Wenn also beispielsweise eine Richtlinie die Verwendung der MFA und die zweite Richtlinie ein konformes Gerät erfordert, müssen Sie die MFA durchlaufen und ein konformes Gerät verwenden. 

Alle Richtlinien werden in zwei Phasen erzwungen:

- In der **ersten** Phase werden alle Richtlinien ausgewertet und alle nicht erfüllten Zugriffssteuerungen gesammelt. 

- In der **zweiten** Phase werden Sie aufgefordert, die nicht erfüllten Anforderungen zu erfüllen. Sollte eine der Richtlinien den Zugriff blockieren, werden Sie blockiert und nicht zur Erfüllung weiterer Richtlinienanforderungen aufgefordert. Falls Sie durch keine der Richtlinien blockiert wurden, werden Sie aufgefordert, weitere Richtlinienanforderungen zu erfüllen. Dabei gilt die folgende Reihenfolge:

    ![Reihenfolge](./media/best-practices/06.png)
    
    Danach folgen externe MFA-Anbieter und die Nutzungsbedingungen.



### <a name="how-are-assignments-evaluated"></a>Wie werden Zuweisungen ausgewertet?

Alle Zuweisungen sind logisch per **UND**-Operator verbunden. Wenn Sie mehr als eine Zuweisung konfiguriert haben, müssen die Bedingungen aller Zuweisungen erfüllt sein, damit eine Richtlinie ausgelöst wird.  

Beim Konfigurieren einer Standortbedingung, die für alle Verbindungen von außerhalb des Organisationsnetzwerks gilt, gehen Sie folgendermaßen vor:

- Schließen Sie **Alle Standorte** ein.
- Schließen Sie **Alle vertrauenswürdigen IPs** aus.


### <a name="what-to-do-if-you-are-locked-out-of-the-azure-ad-admin-portal"></a>Was ist zu tun, wenn Ihr Zugriff auf das Azure AD-Verwaltungsportal gesperrt ist?

Wenn Ihr Zugriff auf das Azure AD-Portal aufgrund einer falschen Einstellung in einer Richtlinie für den bedingten Zugriff gesperrt wurde, gehen Sie folgendermaßen vor:

- Überprüfen Sie, ob weitere Administratoren in Ihrer Organisation vorhanden sind, die noch nicht gesperrt sind. Ein Administrator mit Zugriff auf das Azure-Portal kann die Richtlinie deaktivieren, die Ihre Anmeldung sperrt. 

- Wenn keiner der Administratoren in Ihrer Organisation die Richtlinie aktualisieren kann, müssen Sie eine Supportanfrage übermitteln. Der Microsoft-Support kann Richtlinien für den bedingten Zugriff, die den Zugriff verhindern, überprüfen und aktualisieren.


### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>Was passiert, wenn Sie im klassischen Azure-Portal und im Azure-Portal Richtlinien konfiguriert haben?  

Beide Richtlinien werden von Azure Active Directory erzwungen, und ein Benutzer erhält nur dann Zugriff, wenn alle Anforderungen erfüllt sind.

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>Was passiert, wenn Sie im Intune Silverlight-Portal und im Azure-Portal über Richtlinien verfügen?

Beide Richtlinien werden von Azure Active Directory erzwungen, und ein Benutzer erhält nur dann Zugriff, wenn alle Anforderungen erfüllt sind.

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>Was passiert, wenn ich mehrere Richtlinien für denselben Benutzer konfiguriert habe?  

Bei jeder Anmeldung werden von Azure Active Directory alle Richtlinien ausgewertet, und es wird sichergestellt, dass alle Anforderungen erfüllt sind, bevor dem Benutzer der Zugriff gewährt wird. Ein Blockieren des Zugriffs sticht alle übrigen Konfigurationseinstellungen aus. 


### <a name="does-conditional-access-work-with-exchange-activesync"></a>Funktioniert der bedingte Zugriff mit Exchange ActiveSync?

Ja, Sie können Exchange ActiveSync mit einigen [Einschränkungen](https://docs.microsoft.com/azure/active-directory/conditional-access/conditional-access-for-exo-and-spo) in einer Richtlinie für bedingten Zugriff verwenden. 

### <a name="how-should-you-configure-conditional-access-with-office-365-apps"></a>Wie sollten Sie den bedingten Zugriff für Office 365-Apps konfigurieren?

Da Office 365-Apps miteinander verbunden sind, empfiehlt es sich, häufig verwendete Apps beim Erstellen von Richtlinien gemeinsam zuzuweisen.

Zu den gängigen miteinander verbundenen Anwendungen gehören Microsoft Flow, Microsoft Planner, Microsoft Teams, Office 365 Exchange Online, Office 365 SharePoint Online und Office 365 Yammer.

Bei Richtlinien, die Benutzerinteraktionen erfordern, wie z. B. die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA), ist dies wichtig, wenn der Zugriff am Anfang einer Sitzung oder Aufgabe gesteuert wird. Wenn Sie dies nicht tun, können Benutzer einige Aufgaben in einer App nicht ausführen. Wenn beispielsweise auf nicht verwalteten Geräten die mehrstufige Authentifizierung für den Zugriff auf SharePoint, aber nicht für den Zugriff auf die E-Mail-App erforderlich ist, können Benutzer, die in ihrer E-Mail-App arbeiten, keine SharePoint-Dateien an eine Nachricht anfügen. Weitere Informationen finden Sie im Artikel [Was sind Dienstabhängigkeiten beim bedingten Azure Active Directory-Zugriff?](service-dependencies.md)





## <a name="what-you-should-avoid-doing"></a>Das sollten Sie vermeiden

Das Framework für bedingten Zugriff bietet Ihnen mehr Flexibilität bei der Konfiguration. Mehr Flexibilität bedeutet jedoch auch, dass Sie jede Konfigurationsrichtlinie vor dem Veröffentlichen sorgfältig prüfen sollten, um unerwünschte Ergebnisse zu vermeiden. Achten Sie in diesem Fall besonders auf Zuweisungen, die sich auf komplette Sätze auswirken, z.B. **alle Benutzer/Gruppen/Cloud-Apps**.

Vermeiden Sie in Ihrer Umgebung die folgenden Konfigurationen:


**Für alle Benutzer, alle Cloud-Apps:**

- **Zugriff blockieren:** Diese Konfiguration blockiert Ihre gesamte Organisation, was in keinem Fall wünschenswert ist.

- **Erfordert kompatibles Gerät:** Für Benutzer, die ihre Geräte noch nicht registriert haben, blockiert diese Richtlinie den gesamten Zugriff, einschließlich des Zugriffs auf das Intune-Portal. Wenn Sie ein Administrator ohne registriertes Gerät sind, verhindert diese Richtlinie auch, dass Sie in das Azure-Portal zurückkehren und die Richtlinie ändern können.

- **Erfordert Domänenbeitritt:** Diese Richtlinie blockiert potenziell den Zugriff für alle Benutzer in Ihrer Organisation, wenn Sie noch nicht über in die Domäne eingebundene Geräte verfügen.

- **App-Schutzrichtlinie erforderlich**: Diese Richtlinie zum Blockieren des Zugriffs kann potenziell auch den Zugriff für alle Benutzer in Ihrer Organisation blockieren, wenn Sie nicht über eine Intune-Richtlinie verfügen. Wenn Sie als Administrator nicht über eine Clientanwendung mit einer Intune-App-Schutzrichtlinie verfügen, verhindert diese Richtlinie, dass Sie wieder in Portale wie Intune und Azure gelangen.

**Für alle Benutzer, alle Cloud-Apps, alle Geräteplattformen:**

- **Zugriff blockieren:** Diese Konfiguration blockiert Ihre gesamte Organisation, was in keinem Fall wünschenswert ist.


## <a name="how-should-you-deploy-a-new-policy"></a>Wie stellen Sie eine neue Richtlinie bereit?

Im ersten Schritt sollten Sie Ihre Richtlinie mit dem [Was-wäre-wenn-Tool](what-if-tool.md) bewerten.

Wenn neue Richtlinien für Ihre Umgebung bereit sind, stellen Sie sie phasenweise bereit:

1. Wenden Sie die Richtlinie auf eine kleine Gruppe von Benutzern an, und überprüfen Sie, ob sie sich erwartungsgemäß verhält. 

2.  Wenn Sie eine Richtlinie auf einen größeren Benutzerkreis erweitern, schließen Sie dabei weiterhin alle Administratoren von der Richtlinie aus, um zu gewährleisten, dass sie weiterhin Zugriff haben und die Richtlinie bei Bedarf aktualisieren können.

3. Wenden Sie eine Richtlinie nur dann auf alle Benutzer an, wenn dies erforderlich ist. 

Es wird empfohlen, ein Benutzerkonto zu erstellen, für das Folgendes gilt:

- Nur zur Richtlinienverwaltung gedacht 
- Von allen Richtlinien ausgeschlossen


## <a name="policy-migration"></a>Richtlinienmigration

Aus den folgenden Gründen sollten Sie eine Migration der Richtlinien in Erwägung ziehen, die Sie nicht im Azure-Portal erstellt haben:

- Sie können jetzt Szenarien berücksichtigen, die bisher nicht behandelt werden konnten.

- Sie können die Anzahl der zu verwaltenden Richtlinien reduzieren, indem Sie diese zusammenführen.   

- Sie können alle bedingten Zugriffsrichtlinien an einem zentralen Ort verwalten.

- Das klassische Azure-Portal wurde eingestellt.   


Weitere Informationen finden Sie unter [Migrieren klassischer Richtlinien in das Azure-Portal](policy-migration.md).


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln:

- Unter [Schnellstart: Anfordern der mehrstufigen Authentifizierung (Multi-Factor Authentication, MFA) für bestimmte Apps über den bedingten Zugriff von Azure Active Directory](app-based-mfa.md) erfahren Sie, wie Sie eine Richtlinie für bedingten Zugriff konfigurieren.
- Unter [Anleitung: Planen der Bereitstellung von bedingtem Zugriff in Azure Active Directory](plan-conditional-access.md) erfahren Sie, wie Sie Ihre Richtlinien für bedingten Zugriff planen.
