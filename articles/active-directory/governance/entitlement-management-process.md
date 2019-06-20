---
title: Anforderungsprozess und E-Mail-Benachrichtigungen in der Azure AD-Berechtigungsverwaltung (Vorschauversion) – Azure Active Directory
description: Hier finden Sie Informationen zum Anforderungsprozess für ein Zugriffspaket in der Azure Active Directory-Berechtigungsverwaltung (Vorschauversion) und darüber, wann entsprechende E-Mail-Benachrichtigungen gesendet werden.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/26/2019
ms.author: rolyon
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ab18c8f165fc30636cd05091be1181743f9972d
ms.sourcegitcommit: 8a681ba0aaba07965a2adba84a8407282b5762b2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/29/2019
ms.locfileid: "64873643"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management-preview"></a>Anforderungsprozess und E-Mail-Benachrichtigungen in der Azure AD-Berechtigungsverwaltung (Vorschauversion)

> [!IMPORTANT]
> Die Berechtigungsverwaltung von Azure Active Directory (Azure AD) befindet sich derzeit in der öffentlichen Vorschau.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Wenn ein Benutzer eine Anforderung für ein Zugriffspaket sendet, wird ein Prozess gestartet, um diese Anforderung zu übermitteln. Die Azure AD-Berechtigungsverwaltung sendet auch E-Mail-Benachrichtigungen an genehmigende Personen und Anforderer, wenn wichtige Ereignisse während des Prozesses auftreten.

In diesem Artikel finden Sie eine Beschreibung des Anforderungsprozesses und der E-Mail-Benachrichtigungen, die gesendet werden.

## <a name="request-process"></a>Anforderungsprozess

Ein Benutzer, der Zugriff auf ein Zugriffspaket benötigt, kann eine Zugriffsanforderung senden. Abhängig von der Konfiguration der Richtlinie muss die Anforderung möglicherweise genehmigt werden. Wenn eine Anforderung genehmigt wird, startet ein Prozess, um dem Benutzer Zugriff auf jede Ressource im Zugriffspaket zuzuweisen. Das folgende Diagramm zeigt eine Übersicht über den Prozess und die unterschiedlichen Zustände.

![Diagramm des Genehmigungsprozesses](./media/entitlement-management-process/request-process.png)

| Zustand | BESCHREIBUNG |
| --- | --- |
| Gesendet | Ein Benutzer sendet eine Anforderung. |
| Ausstehende Genehmigung | Wenn die Richtlinie für ein Zugriffspaket eine Genehmigung erfordert, ändert sich der Status der Anforderung in „Genehmigung ausstehend“. |
| Abgelaufen | Wenn keine der genehmigenden Personen die Anforderung innerhalb des Zeitlimits für die Genehmigungsanforderung überprüft, läuft die Anforderung ab. In dem Fall muss der Benutzer seine Anforderung erneut senden. |
| Verweigert | Eine genehmigende Person lehnt eine Anforderung ab. |
| Genehmigt | Eine genehmigende Person genehmigt eine Anforderung. |
| Übermitteln | Dem Benutzer wurde **noch kein** Zugriff auf alle Ressourcen im Zugriffspaket zugewiesen. Wenn es sich um einen externen Benutzer handelt, hat der Benutzer noch nicht auf das Ressourcenverzeichnis zugegriffen und die Berechtigungsaufforderung akzeptiert. |
| Geliefert | Dem Benutzer wurde Zugriff auf alle Ressourcen im Zugriffspaket zugewiesen. |
| Zugriff verlängern | Wenn in der Richtlinie Verlängerungen zulässig sind, hat der Benutzer die Zuweisung verlängert. |
| Zugriff abgelaufen | Der Benutzerzugriff auf das Zugriffspaket ist abgelaufen. Um wieder Zugriff zu erhalten, muss der Benutzer eine Anforderung senden. |

## <a name="email-notifications"></a>E-Mail-Benachrichtigungen

Wenn Sie eine genehmigende Person sind, werden Ihnen E-Mail-Benachrichtigungen gesendet, wenn Sie eine Zugriffsanforderung genehmigen müssen und wenn eine Zugriffsanforderung abgeschlossen wurde. Wenn Sie ein Anforderer sind, werden Ihnen E-Mail-Benachrichtigungen gesendet, die den Status Ihrer Anforderung angeben. Im folgenden Diagramm ist dargestellt, in welchen Fällen diese E-Mail-Benachrichtigungen gesendet werden.

![Berechtigungsverwaltungsprozess für E-Mails](./media/entitlement-management-process/email-notifications.png)

Die folgende Tabelle enthält weitere Details zu den einzelnen E-Mail-Benachrichtigungen.

| # | E-Mail-Betreff | Sendeszenario | Gesendet an |
| --- | --- | --- | --- |
| 1 | Aktion erforderlich: Zugriffsanforderung von *[Anforderer]* für *[Zugriffspaket]* bis *[Datum]* überprüfen | Wenn ein Anforderer eine Anforderung für ein Zugriffspaket sendet | Alle genehmigenden Personen |
| 2 | Aktion erforderlich: Zugriffsanforderung von *[Anforderer]* für *[Zugriffspaket]* bis *[Datum]* überprüfen | X Tage vor dem Zeitlimit für die Genehmigungsanforderung | Alle genehmigenden Personen |
| 3 | Statusbenachrichtigung: Die Zugriffsanforderung von *[Anforderer]* für *[Zugriffspaket]* ist abgelaufen | Wenn die genehmigenden Personen eine Zugriffsanforderung nicht innerhalb der vorgesehenen Frist genehmigen oder ablehnen | Anforderer |
| 4 | Statusbenachrichtigung: Zugriffsanforderung von *[Anforderer]* für *[Zugriffspaket]* abgeschlossen | Wenn die erste genehmigende Person eine Zugriffsanforderung genehmigt oder ablehnt | Alle genehmigenden Personen |
| 5 | Ihnen wurde der Zugriff auf *[Zugriffspaket]* verweigert | Wenn einem Anforderer der Zugriff auf das Zugriffspaket verweigert wurde | Anforderer |
| 6 | Sie haben jetzt Zugriff auf *[Zugriffspaket]*  | Wenn einem Anforderer Zugriff auf alle Ressourcen im Zugriffspaket erteilt wurde | Anforderer |
| 7 | Ihr Zugriff auf *[Zugriffspaket]* läuft in X Tag(en) ab | X Tage vor dem Ablauf des Zugriffs des Anforderers auf das Zugriffspaket | Anforderer |
| 8 | Ihr Zugriff auf *[Zugriffspaket]* ist abgelaufen | Wenn der Zugriff des Anforderers auf ein Zugriffspaket abläuft | Anforderer |

### <a name="review-access-request-emails"></a>E-Mails zur Überprüfung von Zugriffsanforderungen

Wenn ein Anforderer eine Zugriffsanforderung für ein Zugriffspaket sendet, die so konfiguriert ist, dass eine Genehmigung erforderlich ist, erhalten alle in der Richtlinie festgelegten genehmigenden Personen eine E-Mail-Benachrichtigung mit Details der Anforderung. Die Details umfassen den Namen des Anforderers, die Organisation, das Start- und Enddatum für den Zugriff, (falls angegeben) eine geschäftlichen Begründung, wann die Anforderung gesendet wurde und wann die Anforderung abläuft. Die E-Mail enthält einen Link, über den die genehmigenden Personen die Zugriffsanforderung genehmigen oder ablehnen können. Nachfolgend sehen Sie ein Beispiel einer E-Mail-Benachrichtigung, die an eine genehmigende Person gesendet wird, wenn ein Anforderer eine Zugriffsanforderung sendet.

![E-Mail zur Überprüfung einer Zugriffsanforderung](./media/entitlement-management-shared/email-approve-request.png)

### <a name="approved-or-denied-emails"></a>E-Mails über Genehmigung oder Ablehnung

Anforderer werden benachrichtigt, wenn ihre Zugriffsanforderung genehmigt wurde und für den Zugriff verfügbar ist oder wenn die Zugriffsanforderung abgelehnt wurde. Wenn eine genehmigende Person eine von einem Anforderer gesendete Zugriffsanforderung überprüft, kann sie die Zugriffsanforderung genehmigen oder ablehnen. Die genehmigende Person muss eine geschäftliche Begründung der Entscheidung hinzufügen.

Wenn eine Zugriffsanforderung genehmigt wird, startet die Berechtigungsverwaltung den Prozess, durch den dem Anforderer Zugriff auf die einzelnen Ressourcen im Zugriffspaket gewährt wird. Nachdem dem Anforderer der Zugriff auf alle Ressourcen im Zugriffspaket gewährt wurde, wird eine E-Mail-Benachrichtigung an den Anforderer gesendet, um ihn darüber zu informieren, dass seine Zugriffsanforderung genehmigt wurde und er jetzt auf das Zugriffspaket zugreifen kann. Nachfolgend sehen Sie ein Beispiel einer E-Mail-Benachrichtigung, die an einen Anforderer gesendet wird, wenn ihm Zugriff auf ein Zugriffspaket gewährt wurde.

Wenn eine Zugriffsanforderung abgelehnt wird, erhält der Anforderer eine E-Mail-Benachrichtigung. Nachfolgend sehen Sie ein Beispiel einer E-Mail-Benachrichtigung, die an einen Anforderer gesendet wird, wenn die Zugriffsanforderung abgelehnt wurde.

### <a name="expired-access-request-emails"></a>E-Mails über abgelaufene Zugriffsanforderungen

Anforderer werden benachrichtigt, wenn ihre Zugriffsanforderung abgelaufen ist. Wenn ein Anforderer eine Zugriffsanforderung sendet, wird für die Anforderung eine Anforderungsdauer festgelegt, nach der die Anforderung abläuft. Wenn keine der genehmigenden Personen eine Genehmigungs-/Ablehnungsentscheidung übermittelt, verbleibt die Anforderung im Status „Genehmigung ausstehend“. Wenn die Anforderung die konfigurierte Ablaufdauer erreicht, läuft die Anforderung ab und kann von den genehmigenden Personen nicht mehr genehmigt oder abgelehnt werden. In diesem Fall ändert sich der Status der Anforderung in „Abgelaufen“. Eine abgelaufene Anforderung kann nicht mehr genehmigt oder abgelehnt werden. Es wird eine E-Mail-Benachrichtigung an den Anforderer gesendet, die ihn darüber informiert, dass seine Zugriffsanforderung abgelaufen ist und er die Zugriffsanforderung erneut senden muss. Nachfolgend sehen Sie ein Beispiel einer E-Mail-Benachrichtigung, die an einen Anforderer gesendet wird, wenn die Zugriffsanforderung abgelaufen ist.

![E-Mail über abgelaufene Zugriffsanforderung](./media/entitlement-management-process/email-expired-access-request.png)

## <a name="next-steps"></a>Nächste Schritte

- [Anfordern des Zugriffs auf ein Zugriffspaket](entitlement-management-request-access.md)
- [Genehmigen oder Ablehnen von Zugriffsanforderungen](entitlement-management-request-approve.md)
