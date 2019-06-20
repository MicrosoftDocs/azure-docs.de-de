---
title: Konfigurieren der Richtlinie zum Anmelderisiko in Azure Active Directory Identity Protection | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie die Azure AD Identity Protection-Richtlinie zum Anmelderisiko konfigurieren.
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
ms.date: 03/14/2019
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe9e0a4d481ef7b802c50fdc347872e389fa8ef7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60294668"
---
# <a name="how-to-configure-the-sign-in-risk-policy"></a>Anleitung: Konfigurieren der Richtlinie zum Anmelderisiko

Azure Active Directory erkennt [Risikoereignistypen](../reports-monitoring/concept-risk-events.md#risk-event-types) in Echtzeit und offline. Alle Risikoereignisse, die bei der Anmeldung eines Benutzers erkannt wurden, tragen zu einem logischen Konzept bei, das als „riskante Anmeldung“ bezeichnet wird. Eine risikobehaftete Anmeldung ist ein Hinweis auf einen Anmeldeversuch, der nicht vom rechtmäßigen Besitzer eines Benutzerkontos durchgeführt wurde.


## <a name="what-is-the-sign-in-risk-policy"></a>Was ist die Richtlinie zum Anmelderisiko?

Azure AD analysiert jede Anmeldung eines Benutzers. Das Ziel der Analyse besteht darin, verdächtige Aktionen zu erkennen, die mit der Anmeldung verbunden sind. Wird die Anmeldung beispielsweise mit einer anonymen IP-Adresse durchgeführt, oder wird die Anmeldung von einem unbekannten Ort aus initiiert? In Azure AD werden die verdächtigen Aktionen, die vom System erkannt werden können, auch als Risikoereignisse bezeichnet. Basierend auf den Risikoereignissen, die während einer Anmeldung erkannt wurden, berechnet Azure AD einen Wert. Der Wert steht für die Wahrscheinlichkeit (gering, mittel, hoch), dass die Anmeldung nicht vom befugten Benutzer durchgeführt wird. Die Wahrscheinlichkeit wird als **Risikostufe der Anmeldung** bezeichnet.

Die Richtlinie zum Anmelderisiko ist eine automatisierte Antwort, die Sie für eine bestimmte Risikostufe der Anmeldung konfigurieren können. In Ihrer Antwort können Sie den Zugriff auf Ihre Ressourcen blockieren oder die Durchführung einer mehrstufigen Authentifizierung (Multi-Factor Authentication, MFA) zur Bedingung machen, bevor der Zugriff gewährt wird.

   
## <a name="how-do-i-access-the-sign-in-risk-policy"></a>Wie greife ich auf die Richtlinie zum Anmelderisiko zu?
   
Die Richtlinie zum Anmelderisiko befindet sich auf der [Azure AD Identity Protection-Seite](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy) im Abschnitt **Konfigurieren**.
   
![Richtlinie zum Anmelderisiko](./media/howto-sign-in-risk-policy/1014.png "Richtlinie zum Anmelderisiko")


## <a name="policy-settings"></a>Richtlinieneinstellungen

Sie müssen Folgendes festlegen, wenn Sie die Richtlinie zum Anmelderisiko konfigurieren:

- Die Benutzer und Gruppen, für die die Richtlinie gilt:

    ![Benutzer und Gruppen](./media/howto-sign-in-risk-policy/11.png)

- Die Risikostufe der Anmeldung, die die Richtlinie auslöst:

    ![Risikostufe für die Anmeldung](./media/howto-sign-in-risk-policy/12.png)

- Die Art des Zugriffs, die erzwungen werden soll, wenn Ihre Risikostufe der Anmeldung erfüllt wurde:  

    ![Access](./media/howto-sign-in-risk-policy/13.png)

- Zustand Ihrer Richtlinie:

    ![Erzwingen der Richtlinie](./media/howto-sign-in-risk-policy/14.png)


Das Dialogfeld „Richtlinienkonfiguration“ enthält eine Option zum Schätzen der Auswirkungen, die sich aus der Neukonfiguration ergeben.

![Voraussichtliche Auswirkung](./media/howto-sign-in-risk-policy/15.png)

## <a name="what-you-should-know"></a>Wichtige Informationen

Sie können eine Sicherheitsrichtlinie für das Anmelderisiko so konfigurieren, dass MFA erzwungen wird:

![Anfordern von MFA](./media/howto-sign-in-risk-policy/16.png)

Aus Sicherheitsgründen gilt diese Einstellung aber nur für Benutzer, die bereits für MFA registriert wurden. Identity Protection blockiert Benutzer mit einer MFA-Anforderung, falls sie noch nicht für MFA registriert sind.

Sie sollten wie folgt vorgehen, wenn Sie MFA für riskante Anmeldungen erzwingen möchten:

1. Aktivieren Sie die [Multi-Factor Authentication-Registrierungsrichtlinie](howto-mfa-policy.md) für die entsprechenden Benutzer.

2. Fordern Sie die betroffenen Benutzer auf, sich bei einer nicht riskanten Sitzung anzumelden, um eine MFA-Registrierung durchzuführen.

Mit diesen Schritten wird sichergestellt, dass die Multi-Factor Authentication für eine risikobehaftete Anmeldung erzwungen wird.

Für die Richtlinie zum Anmelderisiko gilt Folgendes:

- Sie wird auf den gesamten Browserdatenverkehr und alle Anmeldungen angewendet, für den bzw. die eine moderne Authentifizierung genutzt wird.

- Sie wird nicht auf Anwendungen angewendet, für die ältere Sicherheitsprotokolle verwendet werden, indem der WS-Trust-Endpunkt beim Verbund-IDP, z. B. ADFS, deaktiviert wird.


Eine Übersicht über die zugehörige Benutzeroberfläche finden Sie unter:

* [Wiederherstellung risikobehafteter Anmeldungen](flows.md#risky-sign-in-recovery)
* [Risikobehaftete Anmeldung blockiert](flows.md#risky-sign-in-blocked)  
* [Anmeldeverfahren von Azure AD Identity Protection](flows.md)  

## <a name="best-practices"></a>Bewährte Methoden

Wenn Sie den Schwellenwert **Hoch** auswählen, wird die Richtlinie weniger häufig ausgelöst, und die Auswirkungen für Benutzer verringern sich.  

Hierbei werden aber Anmeldungen mit dem Status **Niedrig** und **Mittel**, die als risikobehaftet gekennzeichnet sind, aus der Richtlinie ausgeschlossen. Für einen Angreifer wird das Ausnutzen einer kompromittierten Identität so ggf. nicht blockiert.

Gehen Sie beim Festlegen der Richtlinie wie folgt vor:

- Schließen Sie die Benutzer aus, die nicht über eine mehrstufige Authentifizierung verfügen bzw. für die dies nicht möglich ist.

- Schließen Sie die Benutzer nach Gebietsschemas aus, für die die Aktivierung der Richtlinie keinen praktischen Nutzen hat (z. B. kein Zugriff auf den Helpdesk).

- Schließen Sie die Benutzer aus, die voraussichtlich viele falsch positive Ergebnisse generieren (Entwickler, Sicherheitsanalysten).

- Verwenden Sie während des anfänglichen Rollouts der Richtlinie (oder wenn Sie die Auswirkungen für Endbenutzer verringern möchten) den Schwellenwert **Hoch**.

- Verwenden Sie den Schwellenwert **Niedrig**, falls für Ihre Organisation eine höhere Sicherheitsebene erforderlich ist. Wenn Sie für den Schwellenwert **Niedrig** auswählen, erhöht sich der Aufwand für die Benutzer bei der Anmeldung, aber die Sicherheit wird erhöht.

Die empfohlene Standardvorgehensweise für die meisten Organisationen ist die Konfiguration einer Regel für den Schwellenwert **Mittel** , um eine gute Balance zwischen Benutzerfreundlichkeit und Sicherheit zu erzielen.






## <a name="next-steps"></a>Nächste Schritte

Eine Übersicht über Azure AD Identity Protection finden Sie [in diesem Artikel](overview.md).
