---
title: Verbessern der Erkennungsgenauigkeit in Azure Active Directory Identity Protection (aktualisiert) | Microsoft-Dokumentation
description: Verbessern der Erkennungsgenauigkeit in Azure Active Directory Identity Protection (aktualisiert).
services: active-directory
keywords: Azure Active Directory Identity Protection, Cloud App Discovery, Verwalten von Anwendungen, Sicherheit, Risiko, Risikostufe, Sicherheitsrisiko, Sicherheitsrichtlinie
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7724d69a9294b420ca061d5ad26ad64826372203
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60453291"
---
# <a name="how-to-improve-the-detection-accuracy"></a>Anleitung: Verbessern der Erkennungsgenauigkeit 

Identity Protection stellt Feedbackmechanismen bereit, mit denen Sie Azure AD Feedback zu den Risikoerkennungen in Ihrer Umgebung geben können. Um dieses Feedback bereitzustellen, können Sie den Status des erkannten risikobehafteten Benutzers oder des Anmeldeereignisses bestätigen. Anhand dieses Feedbacks nimmt Microsoft Aktionen hinsichtlich der aktuellen Risikoerkennungen vor und verbessert die Genauigkeit zukünftiger Erkennungen. 


## <a name="what-is-detection"></a>Was ist Erkennung?

Erkennung ist der Vorgang, mit dem verdächtige Aktivitäten in Verbindung mit Ihren Benutzerkonten identifiziert werden. Die verdächtigen Aktivitäten, die Azure AD erkennen kann, werden als [Risikoereignis](../reports-monitoring/concept-risk-events.md) bezeichnet. Der Erkennungsvorgang basiert auf adaptiven Machine Learning-Algorithmen und -Heuristiken, um Risikoereignisse für Benutzer zu erkennen.

Anhand der Ergebnisse der Erkennung wird bestimmt, ob Benutzer und Anmeldungen risikobehaftet sind. 


## <a name="how-can-i-improve-the-detection-accuracy"></a>Wie kann ich die Erkennungsgenauigkeit verbessern?

Da es sich bei der Erkennung um einen automatisierten Vorgang handelt, ist es möglich, dass Azure AD falsch-positive Ergebnisse meldet. Sie können die Erkennungsgenauigkeit verbessern, indem Sie Feedback für Azure AD in Bezug auf die Erkennungsergebnisse bereitstellen.

Es gibt drei Möglichkeiten zur Verbesserung der Erkennungsgenauigkeit: Bestätigen gefährdeter Anmeldungen, Bestätigen sicherer Anmeldungen und Verwerfen von Benutzerrisiken. Dazu können Sie die folgenden Berichte verwenden:

- **Bericht „Riskante Anmeldungen“** : Im Bericht zu riskanten Anmeldungen können Sie bestätigen, ob die Anmeldungen sicher oder gefährdet sind.

- **Bericht „Riskante Benutzer“** : Im Bericht zu riskanten Benutzern können Sie das Benutzerrisiko verwerfen. 

Ihr Feedback wird von Azure AD verarbeitet, um die Genauigkeit der Erkennungsergebnisse zu verbessern. In der Regel geben Sie Feedback im Rahmen einer Untersuchung zu Benutzer- oder Anmelderisiken. Weitere Informationen finden Sie unter [Untersuchen riskanter Benutzer und Anmeldungen](howto-investigate-risky-users-signins.md).


## <a name="confirm-compromised"></a>Gefährdung bestätigen

Wenn Sie ein Anmeldeereignis als gefährdet bestätigen, signalisieren Sie Azure AD, dass die Anmeldung durch den Besitzer der Identität nicht autorisiert war. Bei Auswahl von „Gefährdung bestätigen“ passiert in Azure AD Folgendes:

- Das Benutzerrisiko des betroffenen Benutzers wird auf „Hoch“ eingestuft.

- Machine Learning wird hinsichtlich der Erkennung von Risikoereignissen optimiert.
 
- Zum Schutz Ihrer Organisation werden zusätzliche Maßnahmen ergriffen.



So bestätigen Sie eine gefährdete Anmeldung

- **Bericht „Riskante Anmeldungen“** : Mit dieser Option können Sie für ein oder mehrere Anmeldeereignisse eine risikobehaftete Anmeldung bestätigen.

    ![Benutzerrisiko verwerfen](./media/howto-improve-detection-accuracy/07.png)

- **Detailansicht des Berichts „Riskante Anmeldungen“** : Mit dieser Option können Sie für das im Bericht „Riskante Anmeldungen“ ausgewählte Anmeldeereignis ein gefährdetes Konto bestätigen. 

    ![Benutzerrisiko verwerfen](./media/howto-improve-detection-accuracy/04.png)


 
## <a name="confirm-safe"></a>Sicherheit bestätigen


Wenn Sie ein Anmeldeereignis als sicher bestätigen, signalisieren Sie Azure AD, dass die Anmeldung durch den Besitzer der Identität **autorisiert** war. Bei Auswahl von „Sicherheit bestätigen“ passiert in Azure AD Folgendes:

- Der Benutzerrisikobeitrag der ausgewählten Anmeldungen wird zurückgesetzt.

- Die zugrunde liegenden Risikoereignisse werden geschlossen.

- Machine Learning wird hinsichtlich der Erkennung von Risikoereignissen optimiert.

- Zum Schutz Ihrer Organisation werden zusätzliche Maßnahmen ergriffen.
 

So bestätigen Sie eine sichere Anmeldung

- **Bericht „Riskante Anmeldungen“** : Mit dieser Option können Sie für ein oder mehrere Anmeldeereignisse eine sichere Anmeldung bestätigen.

    ![Benutzerrisiko verwerfen](./media/howto-improve-detection-accuracy/08.png)

- **Detailansicht des Berichts „Riskante Anmeldungen“** : Mit dieser Option können Sie für das im Bericht „Riskante Anmeldungen“ ausgewählte Anmeldeereignis eine sichere Anmeldung bestätigen. 

    ![Benutzerrisiko verwerfen](./media/howto-improve-detection-accuracy/05.png)




## <a name="dismiss-user-risk"></a>Benutzerrisiko verwerfen

Wenn Sie bereits Aktionen zur Problembehebung für einen riskanten Benutzer vorgenommen haben oder glauben, dass der Benutzer fälschlicherweise als riskant gekennzeichnet wurde, können Sie ein Benutzerrisiko verwerfen. Durch das Verwerfen eines Benutzerrisikos wird für den Benutzer ein nicht-riskanter Zustand wiederhergestellt. Für den ausgewählten Benutzer werden alle risikobehafteten Anmeldungen und Risikoereignisse in der Vergangenheit verworfen.


So verwerfen Sie das gemeldete Benutzerrisiko

- **Bericht „Riskante Benutzer“** : Mit dieser Option können Sie das Benutzerrisiko für einen oder mehrere ausgewählte Benutzer verwerfen.

    ![Benutzerrisiko verwerfen](./media/howto-improve-detection-accuracy/02.png)

- **Detailansicht**: Mit dieser Option können Sie das Benutzerrisiko für den im Bericht „Riskante Benutzer“ ausgewählten Benutzer verwerfen. 

    ![Benutzerrisiko verwerfen](./media/howto-improve-detection-accuracy/01.png)


**Wichtige Informationen:**

- Diese Aktion kann nicht rückgängig gemacht werden.

- Es kann einige Minuten dauern, bis diese Aktion abgeschlossen ist. Daher sollten Sie Ihre Anforderung nicht erneut senden.

- Diese Aktion können Sie nur ausführen, wenn AD die Anmeldeinformationen des Benutzers verwaltet. 



## <a name="best-practices"></a>Bewährte Methoden

Das Verwerfen eines Benutzerrisikos stellt eine Möglichkeit dar, die Sperrung des Benutzers aufzuheben, wenn dieser durch die Richtlinie zum Benutzerrisiko gesperrt wurde und den Zustand nicht durch Zurücksetzenlassen des Kennworts und/oder aufgrund der aktivierten mehrstufigen Authentifizierung (MFA) selbst beheben kann. In diesem Fall empfiehlt es sich sicherzustellen, dass der Benutzer sich anschließend für ein Zurücksetzen des Kennworts und MFA registriert, damit er alle zukünftigen Risikoereignisse selbst beheben kann.


## <a name="next-steps"></a>Nächste Schritte

Eine Übersicht über Azure AD Identity Protection finden Sie [in diesem Artikel](overview-v2.md).


