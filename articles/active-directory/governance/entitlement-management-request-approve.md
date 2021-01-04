---
title: Genehmigen oder Ablehnen von Zugriffsanforderungen – Azure AD-Berechtigungsverwaltung
description: Erfahren Sie, wie Sie das Portal „Mein Zugriff“ verwenden, um Anforderungen für ein Zugriffspaket in der Azure Active Directory-Berechtigungsverwaltung zu genehmigen oder abzulehnen.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfbd5e6f190e0600c31aa17e9b1a75f08e213df4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95973574"
---
# <a name="approve-or-deny-access-requests-in-azure-ad-entitlement-management"></a>Genehmigen oder Ablehnen von Zugriffsanforderungen in der Azure AD-Berechtigungsverwaltung

Mit der Azure AD-Berechtigungsverwaltung können Sie Richtlinien konfigurieren, die eine Genehmigung für Zugriffspakete erfordern, und einen oder mehrere genehmigende Personen auswählen. In diesem Artikel wird beschrieben, wie ausgewählte genehmigende Personen Anforderungen für Zugriffspakete genehmigen oder ablehnen können.

## <a name="open-request"></a>Öffnen der Anforderung

Der erste Schritt beim Genehmigen oder Ablehnen von Zugriffsanforderungen besteht darin, die Zugriffsanforderung zu suchen und zu öffnen, deren Genehmigung aussteht. Es gibt zwei Möglichkeiten zum Öffnen der Zugriffsanforderung.

**Erforderliche Rolle:** Genehmigende Person

1. Suchen Sie nach einer E-Mail von Microsoft Azure, in der Sie zum Genehmigen oder Ablehnen einer Anforderung aufgefordert werden. Hier finden Sie eine Beispiel-E-Mail:

    ![E-Mail für die Genehmigung der Anforderung zum Zugreifen auf ein Paket](./media/entitlement-management-shared/approver-request-email.png)

1. Klicken Sie auf den Link **Anforderung genehmigen oder ablehnen**, um die Zugriffsanforderung zu öffnen.

1. Melden Sie sich beim Portal „Mein Zugriff“ an.

Wenn Sie nicht über die E-Mail verfügen, können Sie die Zugriffsanforderungen, die Sie noch genehmigen müssen, durch Ausführen der folgenden Schritte ermitteln.

1. Melden Sie sich unter [https://myaccess.microsoft.com](https://myaccess.microsoft.com) beim Portal „Mein Zugriff“ an.  (Für die US-Regierung lautet die Domäne im Link für das Portal „Mein Zugriff“ `myaccess.microsoft.us`.)

1. Klicken Sie im linken Menü auf **Genehmigungen**, um eine Liste der Zugriffsanforderungen anzuzeigen, deren Genehmigung aussteht.

1. Auf der Registerkarte **Ausstehend** können Sie die Anforderung finden.

## <a name="view-requestors-answers-to-questions-preview"></a>Anzeigen der Antworten von Anforderern auf Fragen (Vorschau)

1. Navigieren Sie in „Mein Zugriff“ zur Registerkarte **Genehmigungen**.

1. Wechseln Sie zur Anforderung, die Sie genehmigen möchten, und klicken Sie auf **Details**. Sie können auch auf **Genehmigen** oder **Ablehnen** klicken, wenn Sie bereit sind, eine Entscheidung zu treffen.

1. Klicken Sie auf **Anforderungsdetails**.

     ![Portal „Mein Zugriff“ –Zugriffsanforderung – Auf „Anforderungsdetails“ klicken](./media/entitlement-management-request-approve/requestor-information-request-details.png)

1. Die vom Anforderer bereitgestellten Informationen befinden sich am unteren Rand des Panels.

     ![Screenshot mit den Details für die Anforderung](./media/entitlement-management-request-approve/requestor-information-requestor-answers.png)

1. Basierend auf den Informationen, die der Anforderer bereitgestellt hat, können Sie die Anforderung genehmigen oder ablehnen. Anleitungen hierzu finden Sie in den Schritten unter „Genehmigen oder Ablehnen einer Anforderung“.

## <a name="approve-or-deny-request"></a>Genehmigen oder Ablehnen einer Anforderung

Nachdem Sie eine Zugriffsanforderung geöffnet haben, deren Genehmigung aussteht, können Sie Details anzeigen, die Ihnen helfen, eine Entscheidung über die Genehmigung oder Ablehnung zu treffen.

**Erforderliche Rolle:** Genehmigende Person

1. Klicken Sie auf den Link **Anzeigen**, um den Bereich „Zugriffsanforderung“ zu öffnen.

1. Klicken Sie auf **Details**, um Details zur Zugriffsanforderung anzuzeigen.

    Die Details umfassen den Namen des Benutzers, die Organisation, das Start- und Enddatum für den Zugriff, (falls angegeben) eine geschäftlichen Begründung, wann die Anforderung gesendet wurde und wann die Anforderung abläuft.

1. Klicken Sie auf **Approve** (Gewähren) oder **Deny** (Verweigern).

1. Geben Sie bei Bedarf einen Grund ein.

    ![Screenshot: Seite zum Akzeptieren oder Ablehnen der Anforderung](./media/entitlement-management-request-approve/my-access-approve-request.png)

1. Klicken Sie auf **Senden**, um Ihre Entscheidung zu übermitteln.

    Wenn eine Richtlinie mit mehreren genehmigenden Personen konfiguriert ist, muss nur eine genehmigende Person eine Entscheidung über die ausstehende Genehmigung treffen. Nachdem eine genehmigende Person ihre Entscheidung über die Zugriffsanforderung übermittelt hat, wird die Anforderung abgeschlossen. Sie kann dann nicht mehr von den anderen genehmigenden Personen genehmigt oder abgelehnt werden. Die anderen genehmigenden Personen können die Entscheidung über die Anforderung und den Entscheidungsträger in ihrem Portal „Mein Zugriff“ anzeigen. Derzeit wird nur eine einstufige Genehmigung unterstützt.

    Wenn keine der festgelegten genehmigenden Personen die Zugriffsanforderung genehmigen oder ablehnen kann, läuft die Anforderung nach der festgelegten Anforderungsdauer ab. Der Benutzer wird benachrichtigt, dass seine Zugriffsanforderung abgelaufen ist und er die Zugriffsanforderung erneut senden muss.

## <a name="next-steps"></a>Nächste Schritte

- [Anfordern des Zugriffs auf ein Zugriffspaket](entitlement-management-request-access.md)
- [Anforderungsprozess und E-Mail-Benachrichtigungen](entitlement-management-process.md)
