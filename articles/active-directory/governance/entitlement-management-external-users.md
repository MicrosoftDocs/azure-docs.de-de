---
title: 'Steuern des Zugriffs für externe Benutzer in der Azure AD-Berechtigungsverwaltung : Azure Active Directory'
description: Enthält Informationen zu den Einstellungen, die Sie angeben können, um den Zugriff für externe Benutzer in der Azure Active Directory-Berechtigungsverwaltung zu steuern.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0fae7a62f062478c5fee45d172b88d0132f3a8f8
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96546130"
---
# <a name="govern-access-for-external-users-in-azure-ad-entitlement-management"></a>Steuern des Zugriffs für externe Benutzer in der Azure AD-Berechtigungsverwaltung

Für die Azure AD-Berechtigungsverwaltung wird [Azure AD Business-to-Business (B2B)](../external-identities/what-is-b2b.md) genutzt, um mit Personen außerhalb Ihrer Organisation in einem anderen Verzeichnis zusammenzuarbeiten. Externe Benutzer verwenden Azure AD B2B für die Authentifizierung gegenüber ihrem Basisverzeichnis, aber sie werden in Ihrem Verzeichnis dargestellt. Die Darstellung in Ihrem Verzeichnis ermöglicht dem Benutzer den Zugriff auf Ihre Ressourcen.

In diesem Artikel werden die Einstellungen beschrieben, die Sie angeben können, um den Zugriff für externe Benutzer zu steuern.

## <a name="how-entitlement-management-can-help"></a>Vorteile der Berechtigungsverwaltung

Wenn Sie [Azure AD B2B](../external-identities/what-is-b2b.md)-Einladungen verwenden, müssen Sie bereits die E-Mail-Adressen der externen Gastbenutzer kennen, die Sie in das Ressourcenverzeichnis aufnehmen möchten, um mit ihnen zusammenzuarbeiten. Dies lässt sich leicht durchführen, wenn Sie an einem kleineren oder kurzfristigen Projekt arbeiten und bereits alle Teilnehmer kennen. Es ist jedoch schwieriger, wenn Sie mit vielen Benutzern arbeiten möchten oder wenn die Teilnehmer im Lauf der Zeit wechseln.  Angenommen, Sie arbeiten mit einer anderen Organisation zusammen und haben einen Ansprechpartner in dieser Organisation, im Laufe der Zeit benötigen jedoch weitere Benutzer in dieser Organisation ebenfalls Zugriff.

Mit der Berechtigungsverwaltung können Sie eine Richtlinie definieren, die Benutzern aus von Ihnen angegebenen Organisationen das selbstständige Anfordern eines Zugriffspakets ermöglicht. Sie können ein Ablaufdatum für den Zugriff angeben und festlegen, ob eine Genehmigung erforderlich ist. Wenn eine Genehmigung erforderlich ist, können Sie auch einen oder mehrere Benutzer der externen Organisation für Ihr Verzeichnis einladen und als genehmigende Personen festlegen, da diese wahrscheinlich am besten wissen, welche externen Benutzer in ihrer eigenen Organisation Zugriff benötigen. Nachdem Sie das Zugriffspaket konfiguriert haben, können Sie der Kontaktperson (Projektsponsor) in der externen Organisation den Link zum Zugriffspaket senden. Diese Kontaktperson kann den Link für andere Benutzer in der externen Organisation freigeben, und diese können mit diesem Link das Zugriffspaket anfordern. Benutzer in dieser Organisation, die bereits in Ihr Verzeichnis eingeladen wurden, können diesen Link ebenfalls verwenden.

Wenn eine Anforderung genehmigt wurde, gewährt die Berechtigungsverwaltung dem Benutzer den erforderlichen Zugriff. Dies kann das Einladen des Benutzers umfassen, falls er sich noch nicht in Ihrem Verzeichnis befindet. Azure AD erstellt automatisch ein B2B-Gastkonto für den Benutzer. Beachten Sie, dass ein Administrator möglicherweise durch das Festlegen einer [B2B-Zulassungs- oder -Verweigerungsliste](../external-identities/allow-deny-list.md), die Einladungen an andere Organisationen zulässt oder blockiert, festgelegt hat, welche Organisationen für die Zusammenarbeit zugelassen werden.  Wenn die Zulassungs- oder Verweigerungsliste den Benutzer nicht zulässt, wird er nicht eingeladen.

Da der Zugriff des Benutzers nicht unbefristet sein soll, geben Sie in der Richtlinie ein Ablaufdatum, z.B. 180 Tage, an. Nach 180 Tagen wird der gesamte per Zugriffspaket gewährte Zugriff durch die Berechtigungsverwaltung entfernt, sofern er nicht verlängert wird. Wenn der Benutzer, der über die Berechtigungsverwaltung eingeladen wurde, nach Ende der Zuweisung über keine weiteren Zuweisungen von Zugriffspaketen verfügt, wird die Anmeldung über sein Gastkonto standardmäßig 30 Tage lang gesperrt, und anschließend wird das Konto entfernt. Auf diese Weise lassen sich unnötige Konten verhindern. Wie in den folgenden Abschnitten beschrieben, können diese Einstellungen konfiguriert werden.

## <a name="how-access-works-for-external-users"></a>Funktionsweise des Zugriffs für externe Benutzer

Im folgenden Diagramm und den folgenden Schritten erhalten Sie einen Überblick darüber, wie externen Benutzern der Zugriff auf ein Zugriffspaket gewährt wird.

![Diagramm: Lebenszyklus externer Benutzer](./media/entitlement-management-external-users/external-users-lifecycle.png)

1. Sie [fügen eine verbundene Organisation](entitlement-management-organization.md) für das Azure AD-Verzeichnis oder die Domäne hinzu, mit dem bzw. der Sie zusammenarbeiten möchten.

1. Sie erstellen in Ihrem Verzeichnis ein Zugriffspaket, das die Richtlinie [Für nicht in Ihrem Verzeichnis befindliche Benutzer](entitlement-management-access-package-create.md#for-users-not-in-your-directory) enthält.

1. Sie senden einen [Link für das Portal „Mein Zugriff“](entitlement-management-access-package-settings.md) an Ihre Kontaktperson in der externen Organisation, den diese zur Anforderung des Zugriffspakets an die Benutzer weitergeben kann.

1. Ein externer Benutzer (in diesem Beispiel **Requestor A**) verwendet den Link für das Portal „Mein Zugriff“, um [Zugriff auf das Zugriffspaket anzufordern](entitlement-management-request-access.md). Wie sich der Benutzer anmeldet, hängt vom Authentifizierungstyp des Verzeichnisses oder der Domäne ab, der bzw. die in der verbundenen Organisation definiert ist.

1. Eine genehmigende Person [genehmigt die Anforderung](entitlement-management-request-approve.md) (oder sie wird automatisch genehmigt).

1. Für die Anforderung wird in den [Übermittlungsstatus](entitlement-management-process.md) gewechselt.

1. Per B2B-Einladungsprozess wird in Ihrem Verzeichnis ein Gastbenutzerkonto erstellt (in diesem Beispiel **Requestor A (Guest)** ). Wenn eine [Zulassungs- oder Verweigerungsliste](../external-identities/allow-deny-list.md) definiert ist, wird die Listeneinstellung angewendet.

1. Dem Gastbenutzer wird der Zugriff auf alle Ressourcen des Zugriffspakets zugewiesen. Es kann einige Zeit dauern, bis Änderungen an Azure AD und anderen Microsoft Online Services oder verbundenen SaaS-Anwendungen vorgenommen werden. Weitere Informationen finden Sie im Abschnitt zur [Anwendung von Änderungen](entitlement-management-access-package-resources.md#when-changes-are-applied).

1. Der externe Benutzer erhält eine E-Mail mit dem Hinweis, dass der Zugriff [bereitgestellt](entitlement-management-process.md) wurde.

1. Für den Zugriff auf die Ressourcen kann der externe Benutzer entweder in der E-Mail auf den Link klicken oder versuchen, direkt auf die Verzeichnisressourcen zuzugreifen, um den Einladungsprozess abzuschließen.

1. Abhängig von den Richtlinieneinstellungen läuft die Zuweisung des Zugriffspakets für den externen Benutzer nach einer bestimmten Zeit ab, sodass der Zugriff für ihn nicht mehr möglich ist.

1. Je nach Lebenszyklus der Einstellungen des externen Benutzers gilt Folgendes: Wenn der externe Benutzer nicht mehr über Zuweisungen von Zugriffspaketen verfügt, wird seine Anmeldung blockiert und das Gastbenutzerkonto aus Ihrem Verzeichnis entfernt.

## <a name="settings-for-external-users"></a>Einstellungen für externe Benutzer

Um zu gewährleisten, dass Benutzer außerhalb Ihrer Organisation Zugriffspakete anfordern und Zugriff auf die Ressourcen in diesen Zugriffspaketen erhalten können, müssen Sie sicherstellen, dass einige Einstellungen richtig konfiguriert sind.

### <a name="enable-catalog-for-external-users"></a>Aktivieren des Katalogs für externe Benutzer

- Wenn Sie einen [neuen Katalog](entitlement-management-catalog-create.md) erstellen, wird es standardmäßig ermöglicht, dass externe Benutzer Zugriffspakete aus dem Katalog anfordern können. Stellen Sie sicher, dass **Für externe Benutzer aktiviert ist** auf **Ja** festgelegt ist.

    ![Bearbeiten von Katalogeinstellungen](./media/entitlement-management-shared/catalog-edit.png)

### <a name="configure-your-azure-ad-b2b-external-collaboration-settings"></a>Konfigurieren der Einstellungen für externe Azure AD B2B-Zusammenarbeit

- Wenn Sie Gastbenutzern erlauben, andere Gastbenutzer in Ihr Verzeichnis einzuladen, können Gastbenutzereinladungen auch außerhalb der Berechtigungsverwaltung erfolgen. Es wird empfohlen, dass die Option **Gäste können einladen** auf **Nein** gesetzt wird, sodass nur ordnungsgemäß gesteuerte Einladungen möglich sind.
- Wenn Sie die B2B-Zulassungsliste verwenden, müssen Sie sicherstellen, dass alle Domänen, mit denen Sie über die Berechtigungsverwaltung zusammenarbeiten möchten, zur Liste hinzugefügt werden. Wenn Sie stattdessen die B2B-Verweigerungliste verwenden, müssen Sie sicherstellen, dass alle Domänen, mit denen Sie zusammenarbeiten möchten, nicht zur Liste hinzugefügt werden.
- Wenn Sie eine Richtlinie für die Berechtigungsverwaltung für **alle Benutzer** (alle verbundenen Organisationen und alle neuen externen Benutzer) erstellen und ein Benutzer nicht zu einer verbundenen Organisation in Ihrem Verzeichnis gehört, wird automatisch eine verbundene Organisation für ihn erstellt, wenn er das Paket anfordert. Alle Einstellungen Ihrer B2B-Zulassungs- oder Verweigerungsliste haben Vorrang. Achten Sie daher darauf, die Domänen, die Sie in diese Richtlinie einbeziehen möchten, in Ihre Zulassungsliste aufzunehmen, wenn Sie eine verwenden, bzw. sie von Ihrer Verweigerungsliste auszuschließen, wenn Sie mit einer solchen Liste arbeiten.
- Wenn Sie eine Richtlinie zur Berechtigungsverwaltung erstellen möchten, die **alle Benutzer** umfasst (alle verbundenen Organisationen + alle neuen externen Benutzer), müssen Sie zunächst die Authentifizierung mit Einmalkennung per E-Mail für Ihr Verzeichnis aktivieren. Weitere Informationen finden Sie unter [Authentifizierung mit Einmalkennung per E-Mail (Vorschauversion)](../external-identities/one-time-passcode.md).
- Weitere Informationen über Einstellungen für externe Azure AD B2B-Zusammenarbeit finden Sie unter [Aktivieren der externen B2B-Zusammenarbeit und Steuern, wer Gäste einladen kann](../external-identities/delegate-invitations.md).

    ![Einstellungen für externe Azure AD-Zusammenarbeit](./media/entitlement-management-external-users/collaboration-settings.png)

### <a name="review-your-conditional-access-policies"></a>Überprüfen Ihrer Richtlinien für bedingten Zugriff

- Stellen Sie sicher, dass Gastbenutzer von allen Richtlinien für bedingten Zugriff ausgeschlossen werden, die neue Gastbenutzer nicht erfüllen können, da sie dadurch daran gehindert werden, sich bei Ihrem Verzeichnis anzumelden. Beispielsweise verfügen Gastbenutzer wahrscheinlich nicht über ein registriertes Gerät, befinden sich nicht an einem bekannten Standort und möchten sich nicht für die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) registrieren. Deshalb wird die Verwendung der der Berechtigungsverwaltung für Gastbenutzer blockiert, wenn diese Anforderungen in einer Richtlinie für bedingten Zugriff verwendet werden. Weitere Informationen finden Sie unter [Was sind Bedingungen beim bedingten Zugriff in Azure Active Directory?](../conditional-access/concept-conditional-access-conditions.md).

    ![Einstellungen zum Ausschluss in Azure AD-Richtlinien für bedingten Zugriff](./media/entitlement-management-external-users/conditional-access-exclude.png)

### <a name="review-your-sharepoint-online-external-sharing-settings"></a>Überprüfen Ihrer Einstellung der externen SharePoint Online-Freigabe

- Wenn Sie SharePoint Online-Websites in Ihre Zugriffspakete für externe Benutzer aufnehmen möchten, stellen Sie sicher, dass Ihre Einstellung für die externe Freigabe auf Organisationsebene auf **Jeder** (Benutzer müssen sich nicht anmelden) oder **Neue und bestehende Gäste** (Gastbenutzer müssen sich anmelden oder einen Prüfcode angeben) festgelegt ist. Weitere Informationen finden Sie unter [Aktivieren oder Deaktivieren der externen Freigabe](/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting).

- Wenn Sie die externe Freigabe außerhalb der Berechtigungsverwaltung einschränken möchten, können Sie die Einstellung für die externe Freigabe auf **Vorhandene Gäste** festlegen. Dann können nur neue Benutzer, die über die Berechtigungsverwaltung eingeladen werden, auf diese Websites zugreifen. Weitere Informationen finden Sie unter [Aktivieren oder Deaktivieren der externen Freigabe](/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting).

- Stellen Sie sicher, dass die Einstellungen auf Websiteebene den Gastzugriff ermöglichen (es müssen dieselben Optionen ausgewählt sein, wie zuvor aufgeführt). Weitere Informationen finden Sie unter [Aktivieren oder Deaktivieren der externen Freigabe für eine Website](/sharepoint/change-external-sharing-site).

### <a name="review-your-microsoft-365-group-sharing-settings"></a>Überprüfen der Freigabeeinstellungen der Microsoft 365-Gruppe

- Wenn Sie Microsoft 365-Gruppen in Ihre Zugriffspakete für externe Benutzer aufnehmen möchten, stellen Sie sicher, dass **Benutzer dürfen neue Gastbenutzer zur Organisation hinzufügen** auf **Ein** festgelegt ist, damit Gastbenutzer zugreifen können. Weitere Informationen finden Sie unter [Verwalten des Gastzugriffs auf Microsoft 365-Gruppen](/Microsoft 365/admin/create-groups/manage-guest-access-in-groups?view=Microsoft 365-worldwide#manage-groups-guest-access).

- Wenn Sie möchten, dass externe Benutzer auf die SharePoint Online-Website und die der Microsoft 365-Gruppe zugeordneten Ressourcen zugreifen können, müssen Sie sicherstellen, dass die externe SharePoint Online-Freigabe aktiviert ist. Weitere Informationen finden Sie unter [Aktivieren oder Deaktivieren der externen Freigabe](/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting).

- Informationen zum Einrichten einer Gastrichtlinie für Microsoft 365-Gruppen auf Verzeichnisebene in PowerShell finden Sie in [Beispiel: Konfigurieren einer Gastrichtlinie für Gruppen auf Verzeichnisebene](../enterprise-users/groups-settings-cmdlets.md#example-configure-guest-policy-for-groups-at-the-directory-level).

### <a name="review-your-teams-sharing-settings"></a>Überprüfen der Freigabeeinstellungen für Microsoft Teams

- Wenn Sie Microsoft Teams in Ihre Zugriffspakete für externe Benutzer aufnehmen möchten, stellen Sie sicher, dass **Gastzugriff in Microsoft Teams zulassen** auf **Ein** gesetzt ist, damit Gastbenutzer zugreifen können. Weitere Informationen finden Sie unter [Konfigurieren des Gastzugriffs im Microsoft Teams Admin Center](/microsoftteams/set-up-guests#configure-guest-access-in-the-teams-admin-center).

## <a name="manage-the-lifecycle-of-external-users"></a>Verwalten des Lebenszyklus von externen Benutzern

Sie können auswählen, was passiert, wenn ein externer Benutzer, der per Genehmigung der Anforderung eines Zugriffspakets eingeladen wurde, nicht mehr über Zuweisungen von Zugriffspaketen verfügt. Dies kann passieren, wenn der Benutzer alle Zuweisungen von Zugriffspaketen aufgibt oder seine letzte Zuweisung eines Zugriffspakets abläuft. Wenn ein externer Benutzer nicht mehr über Zuweisungen von Zugriffspaketen verfügt, wird für ihn die Anmeldung bei Ihrem Verzeichnis standardmäßig blockiert. Nach 30 Tagen wird das Gastbenutzerkonto aus Ihrem Verzeichnis entfernt.

**Erforderliche Rolle:** Globaler Administrator oder Benutzeradministrator

1. Klicken Sie im Azure-Portal auf **Azure Active Directory** und dann auf **Identity Governance**.

1. Klicken Sie im linken Menü im Abschnitt **Berechtigungsverwaltung** auf **Einstellungen**.

1. Klicken Sie auf **Bearbeiten**.

    ![Einstellungen für die Verwaltung des Lebenszyklus von externen Benutzern](./media/entitlement-management-external-users/settings-external-users.png)

1. Wählen Sie im Abschnitt **Lebenszyklus von externen Benutzern verwalten** die jeweiligen Einstellungen für externe Benutzer aus.

1. Wenn ein externer Benutzer seine letzte Zuweisung von Zugriffspaketen verliert und Sie die Anmeldung beim Verzeichnis blockieren möchten, müssen Sie die Option **Anmelden von externen Benutzern bei diesem Verzeichnis blockieren** auf **Ja** festlegen.

    > [!NOTE]
    > Wenn die Anmeldung eines Benutzers bei diesem Verzeichnis blockiert ist, kann der Benutzer das Zugriffspaket nicht erneut anfordern und auch keinen zusätzlichen Zugriff in diesem Verzeichnis beantragen. Konfigurieren Sie keine Blockierung der Anmeldung, wenn die Benutzer später den Zugriff auf weitere Zugriffspakete anfordern müssen.

1. Wenn ein externer Benutzer seine letzte Zuweisung zu Zugriffspaketen verliert und Sie sein Gastbenutzerkonto aus diesem Verzeichnis entfernen möchten, müssen Sie **Externen Benutzer entfernen** auf **Ja** festlegen.

    > [!NOTE]
    > Im Rahmen der Berechtigungsverwaltung werden nur Konten entfernt, für die eine Einladung per Berechtigungsverwaltung erfolgt ist. Beachten Sie außerdem Folgendes: Für einen Benutzer wird auch dann die Anmeldung blockiert und die Entfernung aus dem Verzeichnis durchgeführt, wenn dieser Benutzer zu Ressourcen im Verzeichnis hinzugefügt wurde, die nicht den Zuweisungen von Zugriffspaketen unterliegen. Wenn der Gastbenutzer bereits in diesem Verzeichnis vorhanden war, bevor die Zuweisungen von Zugriffspaketen durchgeführt wurden, wird er nicht entfernt. Falls der Gast aber per Zuweisung eines Zugriffspakets eingeladen und anschließend auch einer OneDrive for Business- oder SharePoint Online-Website zugewiesen wurde, wird er trotzdem entfernt.

1. Wenn Sie das Gastbenutzerkonto in diesem Verzeichnis entfernen möchten, können Sie die Anzahl von Tagen festlegen, die ablaufen sollen, bevor die Entfernung durchgeführt wird. Falls Sie das Gastbenutzerkonto entfernen möchten, sobald die letzte Zuweisung von Zugriffspaketen abgelaufen ist, legen Sie **Anzahl von Tagen, bevor ein externer Benutzer aus diesem Verzeichnis entfernt wird** auf **0** fest.

1. Klicken Sie auf **Speichern**.

## <a name="next-steps"></a>Nächste Schritte

- [Hinzufügen einer verbundenen Organisation](entitlement-management-organization.md)
- [Für Benutzer, die sich nicht in Ihrem Verzeichnis befinden](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
- [Problembehandlung](entitlement-management-troubleshoot.md)