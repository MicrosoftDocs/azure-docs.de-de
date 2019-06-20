---
title: Überprüfen des Zugriffs auf Gruppen oder Anwendungen mit Zugriffsüberprüfungen – Azure Active Directory | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie den Zugriff von Gruppenmitgliedern oder Anwendungen mit Azure Active Directory-Zugriffsüberprüfungen prüfen können.
services: active-directory
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/21/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6cd5bbba681acaa0c32e681f7cb4809142fe11f9
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66113238"
---
# <a name="review-access-to-groups-or-applications-in-azure-ad-access-reviews"></a>Überprüfen des Zugriffs auf Gruppen oder Anwendungen mit Azure AD-Zugriffsüberprüfungen

Azure Active Directory (Azure AD) erleichtert Unternehmen das Verwalten des Zugriffs auf Gruppen und Anwendungen in Azure AD und anderen Microsoft-Onlinediensten durch die Funktion „Zugriffsüberprüfungen“.

In diesem Artikel wird beschrieben, wie ein Reviewer eine Zugriffsüberprüfung für Gruppenmitglieder oder Benutzer mit Zugriffsberechtigungen für eine Anwendung durchführt.

## <a name="prerequisites"></a>Voraussetzungen

- Azure AD Premium P2

Weitere Informationen finden Sie unter [Welche Benutzer benötigen Lizenzen?](access-reviews-overview.md#which-users-must-have-licenses)

## <a name="open-the-access-review"></a>Öffnen der Zugriffsüberprüfung

Bei einer Zugriffsüberprüfung müssen Sie zuerst die Zugriffsüberprüfung suchen und öffnen.

1. Achten Sie auf eine E-Mail von Microsoft, in der Sie zur Überprüfung des Zugriffs aufgefordert werden. Nachfolgend sehen Sie eine Beispiel-E-Mail, die zur Überprüfen des Zugriffs auf eine Gruppe auffordert.

    ![E-Mail zur Zugriffsüberprüfung](./media/perform-access-review/access-review-email.png)

1. Klicken Sie auf den Link **Start review** (Jetzt überprüfen), um die Zugriffsüberprüfung zu öffnen.

Wenn Sie keine E-Mail haben, können Sie Ihre ausstehenden Zugriffsüberprüfungen mit folgenden Schritten anzeigen.

1. Melden Sie sich auf [https://myapps.microsoft.com](https://myapps.microsoft.com) im MyApps-Portal an.

    ![MyApps-Portal](./media/perform-access-review/myapps-access-panel.png)

1. Klicken Sie oben rechts auf der Seite auf das Benutzersymbol, neben dem Ihr Name und Ihre Standardorganisation angezeigt werden. Werden mehrere Organisationen aufgelistet, wählen Sie die Organisation aus, die eine Zugriffsüberprüfung angefordert hat.

1. Klicken Sie auf die Kachel **Zugriffsüberprüfungen**, um eine Liste der ausstehenden Zugriffsüberprüfungen anzuzeigen.

    Wenn die Kachel nicht angezeigt wird, sind für diese Organisation keine Zugriffsüberprüfungen auszuführen. Daher ist zurzeit keine Aktion erforderlich.

    ![Liste mit den Zugriffsüberprüfungen](./media/perform-access-review/access-reviews-list.png)

1. Klicken Sie bei der Zugriffsüberprüfung, die Sie durchführen möchten, auf den Link **Überprüfung starten**.

## <a name="perform-the-access-review"></a>Durchführen der Zugriffsüberprüfung

Wenn Sie die Zugriffsüberprüfung öffnen, sehen Sie die Namen der Benutzer, die überprüft werden müssen.

Wenn Sie aufgefordert werden, Ihren eigenen Zugriff zu überprüfen, sieht die Seite anders aus. Weitere Informationen finden Sie unter [Überprüfen des eigenen Zugriffs auf Gruppen oder Anwendungen mit Azure AD-Zugriffsüberprüfungen](review-your-access.md).

![Durchführen der Zugriffsüberprüfung](./media/perform-access-review/perform-access-review.png)

Es gibt zwei Möglichkeiten, Zugriff zu gewähren oder zu verweigern:

- Sie können einem oder mehreren Benutzern den Zugriff gewähren oder verweigern.
- Sie können die Systemempfehlungen annehmen, was die einfachste und schnellste Möglichkeit ist.

### <a name="approve-or-deny-access-for-one-or-more-users"></a>Gewähren oder Verweigern des Zugriffs eines oder mehrerer Benutzer

1. Sehen Sie sich die Liste der Benutzer an, um zu entscheiden, ob deren weiterer Zugriff gewährt oder verweigert werden soll.

1. Um einem einzelnen Benutzer den Zugriff zu gewähren oder zu verweigern, klicken Sie auf die entsprechende Zeile, um ein Fenster zu öffnen, in dem Sie die auszuführende Aktion angeben können. Um mehreren Benutzern den Zugriff zu gewähren oder zu verweigern, müssen Sie die einzelnen Benutzernamen mit einem Häkchen versehen (aktivieren) und dann auf die Schaltfläche **X Benutzer überprüfen** klicken, um ein Fenster zu öffnen, in dem Sie die auszuführende Aktion angeben können.

1. Klicken Sie auf **Approve** (Gewähren) oder **Deny** (Verweigern). Wenn Sie sich nicht sicher sind, können Sie auch auf **Don‘t know** (Nicht bekannt) klicken. In diesem Fall behält der Benutzer zwar seinen Zugriff, allerdings wird die Auswahl in den Überwachungsprotokollen aufgezeichnet.

    ![Durchführen der Zugriffsüberprüfung](./media/perform-access-review/approve-deny.png)

1. Geben Sie bei Bedarf im Feld **Grund** einen Grund ein.

    Der Administrator der Zugriffsüberprüfung kann möglicherweise verlangen, dass Sie eine Begründung für den weiteren Zugriffsbedarf oder die Gruppenmitgliedschaft angeben, bevor der Zugriff gewährt wird.

1. Wenn Sie eine entsprechende Maßnahme angegeben haben, klicken Sie auf **Save** (Speichern).

    Wenn Sie Ihre Entscheidung ändern möchten, klicken Sie auf die Zeile, und ändern Sie diese. Sie können z.B. den Zugriff für einen Benutzer gewähren, der erst nicht gewährt wurde, oder den Zugriff für einen Benutzer verweigern, dem dieser zunächst gewährt wurde. Bis zum Abschluss der Zugriffsüberprüfung können Sie Ihre Entscheidung jederzeit ändern.

    Wenn es mehr als einen Reviewer gibt, wird die letzte abgegebene Antwort übernommen. Stellen Sie sich z.B. vor, dass ein Administrator zwei Reviewer auswählt, Alice und Bob. Alice öffnet die Zugriffsüberprüfung als Erste und gewährt den Zugriff. Vor dem Abschluss der Prüfung öffnen Bob die Zugriffsüberprüfung und verweigert den Zugriff. Die als letztes abgegebene Antwort, die Verweigerung, wird übernommen.

    > [!NOTE]
    > Wenn der Zugriff für einen Benutzer verweigert wird, wird dieser nicht sofort entfernt. Alle betreffenden Benutzer werden nach Ende der Überprüfung (oder wenn ein Administrator die Überprüfung beendet) entfernt.

### <a name="approve-or-deny-access-based-on-recommendations"></a>Gewähren und Verweigern von Zugriff auf Grundlage von Empfehlungen

Wir geben Ihnen Empfehlungen, die Sie mit einem einzigen Klick annehmen können, um die Zugriffsüberprüfung so einfach und schnell wie möglich zu gestalten. Diese Empfehlungen werden auf Grundlage der Anmeldeaktivität des Benutzers generiert.

1. Klicken Sie in der blauen Leiste am unteren Rand der Seite auf **Empfehlungen akzeptieren**.

    ![Empfehlungen akzeptieren](./media/perform-access-review/accept-recommendations.png)

    Eine Zusammenfassung der Empfehlungen wird angezeigt.

    ![Zusammenfassung der Empfehlungen](./media/perform-access-review/accept-recommendations-summary.png)

1. Klicken Sie auf **OK**, um die Empfehlungen zu akzeptieren.

## <a name="next-steps"></a>Nächste Schritte

- [Abschließen einer Zugriffsüberprüfung von Gruppen oder Anwendungen](complete-access-review.md)
