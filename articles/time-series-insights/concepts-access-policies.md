---
title: Konfigurieren der Sicherheit zum Gewähren von Datenzugriff – Azure Time Series Insights | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie in der Azure Time Series Insights-Umgebung Sicherheit und Berechtigungen konfigurieren und Datenzugriffsrichtlinien verwalten.
ms.service: time-series-insights
services: time-series-insights
author: shipra1mishra
ms.author: shmishr
manager: dviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/02/2020
ms.custom: seodec18
ms.openlocfilehash: 84b973dfa016b069b18fda47a4336fe952f73b3c
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780857"
---
# <a name="grant-data-access-to-an-environment"></a>Gewähren von Datenzugriff für eine Umgebung

In diesem Artikel werden die beiden Arten von Zugriffsrichtlinien für Azure Time Series Insights thematisiert.

## <a name="sign-in-to-azure-time-series-insights"></a>Anmelden bei Azure Time Series Insights

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Suchen Sie Ihre Azure Time Series Insights-Umgebung, indem Sie `Time Series Insights environments` in das Feld **Suche** eingeben. Wählen Sie in den Suchergebnissen den Eintrag `Time Series Insights environments` aus.
1. Wählen Sie Ihre Azure Time Series Insights-Umgebung in der Liste aus.

## <a name="grant-data-access"></a>Gewähren des Datenzugriffs

Führen Sie die folgenden Schritte aus, um einem Benutzerprinzipal Datenzugriff zu gewähren.

1. Wählen Sie **Datenzugriffsrichtlinien** und dann **+ Hinzufügen** aus.

    [![Auswählen und Hinzufügen einer Datenzugriffsrichtlinie](media/data-access/data-access-select-add-button.png)](media/data-access/data-access-select-add-button.png#lightbox)

1. Wählen Sie **Benutzer auswählen** aus. Suchen Sie den Benutzernamen oder die E-Mail-Adresse des Benutzers, den Sie hinzufügen möchten. Wählen Sie **Auswählen** aus, um die Auswahl zu bestätigen.

    [![Auswählen eines hinzuzufügenden Benutzers](media/data-access/data-access-select-user-to-confirm.png)](media/data-access/data-access-select-user-to-confirm.png#lightbox)

1. Wählen Sie **Rolle auswählen** aus. Wählen Sie die geeignete Zugriffsrolle für den Benutzer aus:

    * Wählen Sie **Mitwirkender** aus, wenn Sie dem Benutzer das Ändern von Verweisdaten und das Freigeben gespeicherter Abfragen und Perspektiven für andere Benutzer der Umgebung erlauben möchten.

    * Wählen Sie andernfalls **Leser** aus, um dem Benutzer das Abfragen von Daten und das Speichern persönlicher (nicht freigegebener) Abfragen in der Umgebung zu ermöglichen.

   Wählen Sie **OK** aus, um die Rollenauswahl zu bestätigen.

    [![Bestätigen der ausgewählten Rolle](media/data-access/data-access-select-a-role.png)](media/data-access/data-access-select-a-role.png#lightbox)

1. Wählen Sie auf der Seite **Benutzerrolle auswählen** die Option **OK** aus.

    [![Auswählen von „OK“ auf der Seite „Benutzerrolle auswählen“](media/data-access/data-access-confirm-user-and-role.png)](media/data-access/data-access-confirm-user-and-role.png#lightbox)

1. Vergewissern Sie sich, dass auf der Seite **Datenzugriffsrichtlinien** die Benutzer und die Rollen der einzelnen Benutzer aufgeführt werden.

    [![Überprüfen, ob Benutzer und Rollen richtig sind](media/data-access/data-access-verify-and-confirm-assignments.png)](media/data-access/data-access-verify-and-confirm-assignments.png#lightbox)

## <a name="provide-guest-access-from-another-azure-ad-tenant"></a>Bereitstellen von Gastzugriff über einen anderen Azure AD-Mandanten

Die Rolle `Guest` ist keine Verwaltungsrolle. Es ist ein Begriff, der ein Konto bezeichnet, das von einem Mandanten zu einem anderem Mandanten eingeladen wird. Nachdem das Gastkonto in das Mandantenverzeichnis eingeladen wurde, kann darauf dieselbe Zugriffssteuerung angewendet werden wie auf jedes andere Konto. Sie können Verwaltungszugriff auf eine Azure Time Series Insights-Umgebung gewähren, indem Sie das Blatt „Zugriffssteuerung (IAM)“ verwenden. Oder Sie können den Zugriff auf die Daten in der Umgebung über das Blatt „Datenzugriffsrichtlinien“ gewähren. Weitere Informationen zum Mandantengastzugriff in Azure Active Directory (Azure AD) finden Sie unter [Hinzufügen von Azure Active Directory B2B-Zusammenarbeitsbenutzern über das Azure-Portal](../active-directory/external-identities/add-users-administrator.md).

Führen Sie die folgenden Schritte aus, um einem Azure AD-Benutzer über einen anderen Mandanten Gastzugriff auf die Azure Time Series Insights-Umgebung zu gewähren.

1. Navigieren Sie zum Azure-Portal, klicken Sie auf **Azure Active Directory**, scrollen Sie zur Registerkarte **Übersicht**, und wählen Sie **Gastbenutzer** aus.

    [![Auswählen von Datenzugriffsrichtlinien und „+ Einladen“](media/data-access/data-access-invite-another-aad-tenant.png)](media/data-access/data-access-invite-another-aad-tenant.png#lightbox)

1. Geben Sie die E-Mail-Adresse des Benutzers ein, den Sie einladen möchten. Diese E-Mail-Adresse muss Azure AD zugeordnet sein. Sie können der Einladung eine persönliche Nachricht hinzufügen.

    [![Eingeben der E-Mail-Adresse, um den ausgewählten Benutzer zu suchen](media/data-access/data-access-invite-guest-by-email.png)](media/data-access/data-access-invite-guest-by-email.png#lightbox)

1. Suchen Sie nach der Bestätigungsmeldung, die am Bildschirm angezeigt wird. Sie können auch auf **Benachrichtigungen** klicken, um zu überprüfen, ob der Gastbenutzer hinzugefügt wurde.

    [![Suchen nach der Bestätigungsblase, die angezeigt wird](media/data-access/data-access-confirmation-bubble.png)](media/data-access/data-access-confirmation-bubble.png#lightbox)

1. Wechseln Sie zurück zur Time Series Insights-Umgebung, um den neu erstellten Gastbenutzer hinzuzufügen. Klicken Sie auf **Datenzugriffsrichtlinien** wie unter **Gewähren des Datenzugriffs** beschrieben. **Wählen Sie einen Benutzer aus.** Suchen Sie nach der E-Mail-Adresse des Gastbenutzers, den Sie eingeladen haben, um diesen hinzuzufügen. Wählen Sie dann **Auswählen** aus, um die Auswahl zu bestätigen.

    [![Auswählen des Benutzers und Bestätigen der Auswahl](media/data-access/data-access-select-invited-person-confirmation.png)](media/data-access/data-access-select-invited-person-confirmation.png#lightbox)

1. Wählen Sie **Rolle auswählen** aus. Wählen Sie eine geeignete Zugriffsrolle für den Gastbenutzer aus:

    * Wählen Sie **Mitwirkender** aus, wenn Sie dem Benutzer das Ändern von Verweisdaten und das Freigeben gespeicherter Abfragen und Perspektiven für andere Benutzer der Umgebung erlauben möchten.

    * Wählen Sie andernfalls **Leser** aus, um dem Benutzer das Abfragen von Daten und das Speichern persönlicher (nicht freigegebener) Abfragen in der Umgebung zu ermöglichen.

   Wählen Sie **OK** aus, um die Rollenauswahl zu bestätigen.

    [![Bestätigen der Rollenauswahl](media/data-access/data-access-select-ok-and-confirm.png)](media/data-access/data-access-select-ok-and-confirm.png#lightbox)

1. Wählen Sie auf der Seite **Benutzerrolle auswählen** die Option **OK** aus.

1. Vergewissern Sie sich, dass auf der Seite **Datenzugriffsrichtlinien** die Gastbenutzer mit denen ihnen zugewiesenen Rollen aufgeführt werden.

    [![Überprüfen, ob Benutzer und Rollen ordnungsgemäß zugewiesen sind](media/data-access/data-access-confirm-invited-users-and-roles.png)](media/data-access/data-access-confirm-invited-users-and-roles.png#lightbox)

1. Nun erhält der Gastbenutzer eine Einladungs-E-Mail an die oben angegebene E-Mail-Adresse. Der Gastbenutzer wählt **Los geht's** aus, um die Einladung anzunehmen und eine Verbindung mit der Azure-Cloud herzustellen.

    [![Gast wählt „Los geht's“ aus, um die Einladung anzunehmen](media/data-access/data-access-email-invitation.png)](media/data-access/data-access-email-invitation.png#lightbox)

1. Nachdem **Los geht's** ausgewählt wurde, wird dem Gastbenutzer ein Berechtigungsfeld angezeigt, das der Organisation des Administrators zugeordnet ist. Nachdem die Berechtigung durch die Auswahl von **Akzeptieren** erteilt wurde, wird er angemeldet.

    [![Gast überprüft Berechtigungen und akzeptiert die Einladung](media/data-access/data-access-grant-permission-sign-in.png)](media/data-access/data-access-grant-permission-sign-in.png#lightbox)

1. Der Administrator [teilt die Umgebungs-URL](time-series-insights-parameterized-urls.md) mit seinem Gast.

1. Nachdem sich der Gastbenutzer bei der E-Mail-Adresse angemeldet hat, mit der Sie ihn eingeladen haben, und dieser die Einladung annimmt, wird er an das Azure-Portal weitergeleitet.

1. Der Gast kann nun mithilfe der vom Administrator bereitgestellten Umgebungs-URL auf die freigegebene Umgebung zugreifen. Er kann diese URL für den sofortigen Zugriff in seinen Webbrowser eingeben.

1. Der Mandant des Administrators wird dem Gastbenutzer angezeigt, nachdem Sie sein Profilsymbol in der oberen rechten Ecke des Time Series-Explorers ausgewählt haben.

    [![Avatarauswahl auf insights.azure.com](media/data-access/data-access-select-tenant-and-instance.png)](media/data-access/data-access-select-tenant-and-instance.png#lightbox)

    Nachdem der Gastbenutzer den Mandanten des Administrators ausgewählt hat, kann er die freigegebene Azure Time Series Insights-Umgebung auswählen.

    Er verfügt nun über alle Funktionen, die mit der Rolle verbunden sind, die Sie ihm in **Schritt 5** zugewiesen haben.

    [![Gastbenutzer wählt Ihren Azure-Mandanten aus der Dropdownliste aus](media/data-access/data-access-all-capabilities.png)](media/data-access/data-access-all-capabilities.png#lightbox)

## <a name="next-steps"></a>Nächste Schritte

* Lesen Sie [Authentifizierung und Autorisierung](time-series-insights-authentication-and-authorization.md), um sich über die Schritte für die Azure Active Directory App-Registrierung zu informieren.

* Anzeigen [Ihrer Umgebung im Azure Time Series Insights-Explorer](./concepts-ux-panels.md).
