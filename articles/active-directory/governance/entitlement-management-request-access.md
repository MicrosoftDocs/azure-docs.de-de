---
title: Anfordern des Zugriffs auf ein Zugriffspaket in der Azure AD-Berechtigungsverwaltung (Vorschauversion) – Azure Active Directory
description: Erfahren Sie, wie Sie das Portal „Mein Zugriff“ verwenden, um Zugriff auf ein Zugriffspaket in der Azure Active Directory-Berechtigungsverwaltung (Vorschauversion) anzufordern.
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
ms.date: 04/19/2019
ms.author: rolyon
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39a50240b4360c5b4adcd6020c2b80b0f06315f7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64540671"
---
# <a name="request-access-to-an-access-package-in-azure-ad-entitlement-management-preview"></a>Anfordern des Zugriffs auf ein Zugriffspaket in der Azure AD-Berechtigungsverwaltung (Vorschauversion)

> [!IMPORTANT]
> Die Berechtigungsverwaltung von Azure Active Directory (Azure AD) befindet sich derzeit in der öffentlichen Vorschau.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="sign-in-to-the-my-access-portal"></a>Anmelden beim Portal „Mein Zugriff“

Als Erstes müssen Sie sich beim Portal „Mein Zugriff“ anmelden. Dort können Sie Zugriff auf ein Zugriffspaket anfordern.

**Erforderliche Rolle:** Anforderer

1. Suchen Sie nach einer E-Mail oder Nachricht des Projektmanagers oder Geschäftsleiters, mit dem Sie arbeiten. Die E-Mail sollte einen Link zu dem Zugriffspaket enthalten, auf das Sie Zugriff benötigen. Der Link beginnt mit der folgenden Zeichenfolge:

    `https://myaccess.microsoft.com`

1. Öffnen Sie den Link.

1. Melden Sie sich beim Portal „Mein Zugriff“ an.

    Achten Sie darauf, dass Sie Ihr Organisationskonto verwenden. Wenden Sie sich im Zweifelsfall an Ihren Projektmanager oder Geschäftsleiter.

## <a name="request-an-access-package"></a>Anfordern eines Zugriffspakets

Nachdem Sie das Zugriffspaket im Portal „Mein Zugriff“ gefunden haben, können Sie eine Anforderung senden.

**Erforderliche Rolle:** Anforderer

1. Klicken Sie auf das Häkchen, um das Zugriffspaket auszuwählen.

    ![Portal „Mein Zugriff“ – Zugriffspakete](./media/entitlement-management-shared/my-access-access-packages.png)

1. Klicken Sie auf **Zugriff anfordern**, um den Bereich „Zugriff anfordern“ zu öffnen.

1. Wenn das Feld **Geschäftliche Begründung** angezeigt wird, geben Sie eine Begründung für den benötigten Zugriff ein.

1. Wenn **Für bestimmten Zeitraum anfordern?** aktiviert ist, wählen Sie **Ja** oder **Nein** aus.

1. Geben Sie gegebenenfalls das Startdatum und das Enddatum ein.

    ![Portal „Mein Zugriff“ – Zugriff anfordern](./media/entitlement-management-shared/my-access-request-access.png)

1. Klicken Sie abschließend auf **Senden**, um Ihre Anforderung zu senden.

1. Klicken Sie auf **Anforderungsverlauf**, um eine Liste Ihrer Anforderungen und den Status anzuzeigen.

    Wenn das Zugriffspaket genehmigt werden muss, weist die Anforderung jetzt den Status „Genehmigung steht aus“ auf.

## <a name="cancel-a-request"></a>Abbrechen einer Anforderung

Wenn Sie eine Zugriffsanforderung senden und die Anforderung weiterhin den Status **Genehmigung steht aus** aufweist, können Sie die Anforderung abbrechen.

**Erforderliche Rolle:** Anforderer

1. Klicken Sie im Portal „Mein Zugriff“ auf der linken Seite auf **Anforderungsverlauf**, um eine Liste Ihrer Anforderungen und den Status anzuzeigen.

1. Klicken Sie auf den Link **Anzeigen** für die Anforderung, die Sie abbrechen möchten.

1. Wenn die Anforderung weiterhin den Status **Genehmigung steht aus** aufweist, können Sie auf **Anforderung abbrechen** klicken, um die Anforderung abzubrechen.

    ![Portal „Mein Zugriff“ – Anforderung abbrechen](./media/entitlement-management-request-access/my-access-cancel-request.png)

1. Klicken Sie auf **Anforderungsverlauf**, um zu bestätigen, dass die Anforderung abgebrochen wurde.

## <a name="select-a-policy"></a>Auswählen einer Richtlinie

Wenn Sie Zugriff auf ein Zugriffspaket anfordern, für das mehrere Richtlinien gelten, werden Sie möglicherweise aufgefordert, eine Richtlinie auszuwählen. Beispielsweise kann ein Zugriffspaketmanager ein Zugriffspaket mit zwei Richtlinien für zwei Gruppen interner Mitarbeiter konfigurieren. Die erste Richtlinie könnte einen Zeitraum von 60 Tagen für den Zugriff zulassen und eine Genehmigung erfordern. Die zweite Richtlinie könnte den Zugriff 2 Tage lang zulassen und keine Genehmigung erfordern. Bei diesem Szenario müssen Sie die Richtlinie auswählen, die Sie verwenden möchten.

**Erforderliche Rolle:** Anforderer

## <a name="next-steps"></a>Nächste Schritte

- [Genehmigen oder Ablehnen von Zugriffsanforderungen](entitlement-management-request-approve.md)
- [Anforderungsprozess und E-Mail-Benachrichtigungen](entitlement-management-process.md)
