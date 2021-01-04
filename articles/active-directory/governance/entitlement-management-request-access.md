---
title: Anfordern eines Zugriffspakets – Azure AD-Berechtigungsverwaltung
description: Erfahren Sie, wie Sie das Portal „Mein Zugriff“ verwenden, um Zugriff auf ein Zugriffspaket in der Azure Active Directory-Berechtigungsverwaltung anzufordern.
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
ms.openlocfilehash: 726507fa6ea0651b23d46424bda669a2d8ad41e0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996643"
---
# <a name="request-access-to-an-access-package-in-azure-ad-entitlement-management"></a>Anfordern des Zugriffs auf ein Zugriffspaket in der Azure AD-Berechtigungsverwaltung

In der Azure AD-Berechtigungsverwaltung ermöglicht ein Zugriffspaket eine einmalige Einrichtung von Ressourcen und Richtlinien, die den Zugriff während der gesamten Lebensdauer des Zugriffspakets automatisch verwaltet. 

Ein Zugriffspaket-Manager kann Richtlinien konfigurieren, die eine Genehmigung erfordern, damit Benutzer Zugriff auf Zugriffspakete erhalten. Ein Benutzer, der Zugriff auf ein Zugriffspaket benötigt, kann eine Anforderung stellen, um Zugriff zu erhalten. In diesem Artikel wird beschrieben, wie Sie eine Zugriffsanforderung senden.

## <a name="sign-in-to-the-my-access-portal"></a>Anmelden beim Portal „Mein Zugriff“

Als Erstes müssen Sie sich beim Portal „Mein Zugriff“ anmelden. Dort können Sie Zugriff auf ein Zugriffspaket anfordern.

**Erforderliche Rolle:** Anforderer

1. Suchen Sie nach einer E-Mail oder Nachricht des Projektmanagers oder Geschäftsleiters, mit dem Sie arbeiten. Die E-Mail sollte einen Link zu dem Zugriffspaket enthalten, auf das Sie Zugriff benötigen. Der Link beginnt mit `myaccess`, enthält einen Verzeichnishinweis und endet mit einer Zugriffspaket-ID.  (Bei US Government kann die Domäne stattdessen `https://myaccess.microsoft.us` lauten.)
 
    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. Öffnen Sie den Link.

1. Melden Sie sich beim Portal „Mein Zugriff“ an.

    Achten Sie darauf, dass Sie Ihr Organisationskonto (Geschäfts-, Schul- oder Unikonto) verwenden. Wenden Sie sich im Zweifelsfall an Ihren Projektmanager oder Geschäftsleiter.

## <a name="request-an-access-package"></a>Anfordern eines Zugriffspakets

Nachdem Sie das Zugriffspaket im Portal „Mein Zugriff“ gefunden haben, können Sie eine Anforderung senden.

**Erforderliche Rolle:** Anforderer

1. Suchen Sie in der Liste nach dem Zugriffspaket.  Falls erforderlich, können Sie eine Suchzeichenfolge eingeben und dann den Filter **Name**, **Katalog** oder **Ressourcen** auswählen.

    ![Portal „Mein Zugriff“ – Suche nach Ressourcen](./media/entitlement-management-request-access/my-access-resource-search.png)

1. Klicken Sie auf das Häkchen, um das Zugriffspaket auszuwählen.

1. Klicken Sie auf **Zugriff anfordern**, um den Bereich „Zugriff anfordern“ zu öffnen.

    ![Portal „Mein Zugriff“ – Zugriffspakete](./media/entitlement-management-request-access/my-access-request-access-button.png)

1. Wenn das Feld **Geschäftliche Begründung** angezeigt wird, geben Sie eine Begründung für den benötigten Zugriff ein.

1. Wenn **Für bestimmten Zeitraum anfordern?** aktiviert ist, wählen Sie **Ja** oder **Nein** aus.

1. Geben Sie gegebenenfalls das Startdatum und das Enddatum ein.

    ![Portal „Mein Zugriff“ – Zugriff anfordern](./media/entitlement-management-shared/my-access-request-access.png)

1. Klicken Sie abschließend auf **Senden**, um Ihre Anforderung zu senden.

1. Klicken Sie auf **Anforderungsverlauf**, um eine Liste Ihrer Anforderungen und den Status anzuzeigen.

    Wenn das Zugriffspaket genehmigt werden muss, weist die Anforderung jetzt den Status „Genehmigung steht aus“ auf.

### <a name="select-a-policy"></a>Auswählen einer Richtlinie

Wenn Sie Zugriff auf ein Zugriffspaket anfordern, für das mehrere Richtlinien gelten, werden Sie möglicherweise aufgefordert, eine Richtlinie auszuwählen. Beispielsweise kann ein Zugriffspaketmanager ein Zugriffspaket mit zwei Richtlinien für zwei Gruppen interner Mitarbeiter konfigurieren. Die erste Richtlinie könnte einen Zeitraum von 60 Tagen für den Zugriff zulassen und eine Genehmigung erfordern. Die zweite Richtlinie könnte den Zugriff 2 Tage lang zulassen und keine Genehmigung erfordern. Bei diesem Szenario müssen Sie die Richtlinie auswählen, die Sie verwenden möchten.

![Portal „Mein Zugriff“ – Zugriff anfordern – mehrere Richtlinien](./media/entitlement-management-request-access/my-access-multiple-policies.png)

### <a name="fill-out-requestor-information"></a>Angeben der Informationen zum Anforderer

Möglicherweise fordern Sie Zugriff auf ein Zugriffspaket an, das eine geschäftliche Begründung und zusätzliche Informationen zum Anforderer erfordert, bevor Ihnen Zugriff auf das Zugriffspaket gewährt wird. Geben Sie alle Informationen zum Anforderer ein, die für den Zugriff auf das Zugriffspaket erforderlich sind.

![Portal „Mein Zugriff“ – Zugriff anfordern – Informationen zum Anforderer angeben](./media/entitlement-management-request-access/my-access-requestor-information.png)

## <a name="resubmit-a-request"></a>Erneutes Übermitteln einer Anforderung

Wenn Sie den Zugriff auf ein Zugriffspaket anfordern, kann Ihre Anforderung abgelehnt werden, oder Ihre Anforderung kann ablaufen, wenn die genehmigenden Personen nicht rechtzeitig antworten. Wenn Sie Zugriff benötigen, können Sie es nochmal versuchen und Ihre Anforderung nochmal übermitteln. Im Folgenden wird erläutert, wie Sie eine Zugriffsanforderung nochmal übermitteln:

**Erforderliche Rolle:** Anforderer

1. Melden Sie sich beim Portal **Mein Zugriff** an.

1. Klicken Sie auf **Anforderungsverlauf** im linken Navigationsmenü.

1. Suchen Sie das Zugriffspaket, für das Sie eine Anforderung nochmal übermitteln.

1. Klicken Sie auf das Häkchen, um das Zugriffspaket auszuwählen.

1. Klicken Sie rechts vom ausgewählten Zugriffspaket auf den blauen Link **View** (Ansicht).
    
    ![Auswählen des Zugriffspakets und des Links „View“ (Ansicht)](./media/entitlement-management-request-access/resubmit-request-select-request-and-view.png)

    Ein Bereich wird rechts mit dem Anforderungsverlauf für das Zugriffspaket geöffnet.
    
    ![Klicken auf die Schaltfläche „Erneut übermitteln“](./media/entitlement-management-request-access/resubmit-request-select-resubmit.png)

1. Klicken Sie unten im Bereich auf die Schaltfläche **Erneut übermitteln**.

## <a name="cancel-a-request"></a>Abbrechen einer Anforderung

Wenn Sie eine Zugriffsanforderung senden und die Anforderung weiterhin den Status **Genehmigung steht aus** aufweist, können Sie die Anforderung abbrechen.

**Erforderliche Rolle:** Anforderer

1. Klicken Sie im Portal „Mein Zugriff“ auf der linken Seite auf **Anforderungsverlauf**, um eine Liste Ihrer Anforderungen und den Status anzuzeigen.

1. Klicken Sie auf den Link **Anzeigen** für die Anforderung, die Sie abbrechen möchten.

1. Wenn die Anforderung weiterhin den Status **Genehmigung steht aus** aufweist, können Sie auf **Anforderung abbrechen** klicken, um die Anforderung abzubrechen.

    ![Portal „Mein Zugriff“ – Anforderung abbrechen](./media/entitlement-management-request-access/my-access-cancel-request.png)

1. Klicken Sie auf **Anforderungsverlauf**, um zu bestätigen, dass die Anforderung abgebrochen wurde.

## <a name="next-steps"></a>Nächste Schritte

- [Genehmigen oder Ablehnen von Zugriffsanforderungen](entitlement-management-request-approve.md)
- [Anforderungsprozess und E-Mail-Benachrichtigungen](entitlement-management-process.md)
