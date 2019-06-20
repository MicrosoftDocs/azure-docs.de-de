---
title: Genehmigen oder Ablehnen von Zugriffsanforderungen in der Azure AD-Berechtigungsverwaltung (Vorschauversion) – Azure Active Directory
description: Erfahren Sie, wie Sie das Portal „Mein Zugriff“ verwenden, um Anforderungen für ein Zugriffspaket in der Azure Active Directory-Berechtigungsverwaltung (Vorschauversion) zu genehmigen oder abzulehnen.
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
ms.date: 04/18/2019
ms.author: rolyon
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b2d07638f6c6f153ee3640273fbee5e56df0ab2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64540667"
---
# <a name="approve-or-deny-access-requests-in-azure-ad-entitlement-management-preview"></a>Genehmigen oder Ablehnen von Zugriffsanforderungen in der Azure AD-Berechtigungsverwaltung (Vorschauversion)

> [!IMPORTANT]
> Die Berechtigungsverwaltung von Azure Active Directory (Azure AD) befindet sich derzeit in der öffentlichen Vorschau.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Mit der Azure AD-Berechtigungsverwaltung können Sie Richtlinien konfigurieren, die eine Genehmigung für Zugriffspakete erfordern, und einen oder mehrere genehmigende Personen auswählen. In diesem Artikel wird beschrieben, wie ausgewählte genehmigende Personen Anforderungen für Zugriffspakete genehmigen oder ablehnen können.

## <a name="open-request"></a>Öffnen der Anforderung

Der erste Schritt beim Genehmigen oder Ablehnen von Zugriffsanforderungen besteht darin, die Zugriffsanforderung zu suchen und zu öffnen, deren Genehmigung aussteht. Es gibt zwei Möglichkeiten zum Öffnen der Zugriffsanforderung.

**Erforderliche Rolle:** Genehmigende Person

1. Suchen Sie nach einer E-Mail von Microsoft Azure, in der Sie zum Genehmigen oder Ablehnen einer Anforderung aufgefordert werden. Hier finden Sie eine Beispiel-E-Mail:

    ![E-Mail für die Genehmigung der Anforderung zum Zugreifen auf ein Paket](./media/entitlement-management-shared/email-approve-request.png)

1. Klicken Sie auf den Link **Anforderung genehmigen oder ablehnen**, um die Zugriffsanforderung zu öffnen.

1. Melden Sie sich beim Portal „Mein Zugriff“ an.

Wenn Sie nicht über die E-Mail verfügen, können Sie die Zugriffsanforderungen, die Sie noch genehmigen müssen, durch Ausführen der folgenden Schritte ermitteln.

1. Melden Sie sich unter [https://myaccess.microsoft.com](https://myaccess.microsoft.com) beim Portal „Mein Zugriff“ an.

1. Klicken Sie im linken Menü auf **Genehmigungen**, um eine Liste der Zugriffsanforderungen anzuzeigen, deren Genehmigung aussteht.

1. Auf der Registerkarte **Ausstehend** können Sie die Anforderung finden.

## <a name="approve-or-deny-request"></a>Genehmigen oder Ablehnen einer Anforderung

Nachdem Sie eine Zugriffsanforderung geöffnet haben, deren Genehmigung aussteht, können Sie Details anzeigen, die Ihnen helfen, eine Entscheidung über die Genehmigung oder Ablehnung zu treffen.

**Erforderliche Rolle:** Genehmigende Person

1. Klicken Sie auf den Link **Anzeigen**, um den Bereich „Zugriffsanforderung“ zu öffnen.

1. Klicken Sie auf **Details**, um Details zur Zugriffsanforderung anzuzeigen.

    Die Details umfassen den Namen des Benutzers, die Organisation, das Start- und Enddatum für den Zugriff, (falls angegeben) eine geschäftlichen Begründung, wann die Anforderung gesendet wurde und wann die Anforderung abläuft.

1. Klicken Sie auf **Approve** (Gewähren) oder **Deny** (Verweigern).

1. Geben Sie bei Bedarf einen Grund ein.

    ![Portal „Mein Zugriff“ – Zugriffsanforderung](./media/entitlement-management-shared/my-access-approve-request.png)

1. Klicken Sie auf **Senden**, um Ihre Entscheidung zu übermitteln.

    Wenn eine Richtlinie mit mehreren genehmigenden Personen konfiguriert ist, muss nur eine genehmigende Person eine Entscheidung über die ausstehende Genehmigung treffen. Nachdem eine genehmigende Person ihre Entscheidung über die Zugriffsanforderung übermittelt hat, wird die Anforderung abgeschlossen. Sie kann dann nicht mehr von den anderen genehmigenden Personen genehmigt oder abgelehnt werden. Die anderen genehmigenden Personen können die Entscheidung über die Anforderung und den Entscheidungsträger in ihrem Portal „Mein Zugriff“ anzeigen. Derzeit wird nur eine einstufige Genehmigung unterstützt.

    Wenn keine der festgelegten genehmigenden Personen die Zugriffsanforderung genehmigen oder ablehnen kann, läuft die Anforderung nach der festgelegten Anforderungsdauer ab. Der Benutzer wird benachrichtigt, dass seine Zugriffsanforderung abgelaufen ist und er die Zugriffsanforderung erneut senden muss.

## <a name="next-steps"></a>Nächste Schritte

- [Anfordern des Zugriffs auf ein Zugriffspaket](entitlement-management-request-access.md)
- [Anforderungsprozess und E-Mail-Benachrichtigungen](entitlement-management-process.md)
