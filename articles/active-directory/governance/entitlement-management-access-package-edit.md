---
title: Bearbeiten und Verwalten eines vorhandenen Zugriffspakets in der Azure AD-Berechtigungsverwaltung (Vorschauversion) – Azure Active Directory
description: Erfahren Sie, wie Sie ein vorhandenes Zugriffspaket in der Azure Active Directory-Berechtigungsverwaltung (Vorschauversion) bearbeiten und verwalten.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/16/2019
ms.author: rolyon
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce51f4df50de50cef06bba699ca7c6f76bc5d166
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65833247"
---
# <a name="edit-and-manage-an-existing-access-package-in-azure-ad-entitlement-management-preview"></a>Bearbeiten und Verwalten eines vorhandenen Zugriffspakets in der Azure AD-Berechtigungsverwaltung (Vorschauversion)

> [!IMPORTANT]
> Die Berechtigungsverwaltung von Azure Active Directory (Azure AD) befindet sich derzeit in der öffentlichen Vorschau.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Mit einem Zugriffspaket können Sie Ressourcen und Richtlinien einmalig so einrichten, dass der Zugriff für die gesamte Lebensdauer des Zugriffspakets automatisch verwaltet wird. Als Zugriffspaket-Manager können Sie die Ressourcen innerhalb eines Zugriffspakets jederzeit ändern, ohne dass Sie Benutzern den Zugriff auf die neuen Ressourcen bereitstellen oder den Zugriff auf die bisherigen Ressourcen entfernen müssen. Auch Richtlinien können jederzeit aktualisiert werden. Jedoch gelten die Änderungen dann nur für neue Zugriffe.

In diesem Artikel wird beschrieben, wie Sie vorhandene Zugriffspakete bearbeiten und verwalten.

## <a name="add-resource-roles"></a>Hinzufügen von Ressourcenrollen

Eine Ressourcenrolle ist eine Sammlung von Berechtigungen, die einer Ressource zugeordnet sind. Damit Benutzer Ressourcen anfordern können, müssen Sie dem Zugriffspaket Ressourcenrollen hinzufügen. Ressourcenrollen können für Gruppen, Anwendungen und SharePoint-Websites hinzugefügt werden.

**Erforderliche Rolle**: Benutzeradministrator, Katalogbesitzer oder Zugriffspaket-Manager

1. Klicken Sie im Azure-Portal auf **Azure Active Directory** und dann auf **Identity Governance**.

1. Klicken Sie im Menü auf der linken Seite auf **Zugriffspakete**, und öffnen Sie das Zugriffspaket.

1. Klicken Sie im Menü auf der linken Seite auf **Ressourcenrollen**.

1. Klicken Sie auf **Ressourcenrollen hinzufügen**, um die Seite „Hinzufügen von Ressourcenrollen zu einem Zugriffspaket“ zu öffnen.

    ![Zugriffspaket: Ressourcenrollen hinzufügen](./media/entitlement-management-access-package-edit/resource-roles-add.png)

1. Je nachdem, ob Sie eine Gruppe, Anwendung oder SharePoint-Website hinzufügen möchten, führen Sie die entsprechenden Schritte in einem der folgenden Abschnitte zum Hinzufügen von Ressourcenrollen aus.

### <a name="add-a-group-resource-role"></a>Hinzufügen einer Ressourcenrolle für eine Gruppe

Mit der Azure AD-Berechtigungsverwaltung können Benutzer automatisch einer Gruppe hinzugefügt werden, wenn sie einem Zugriffspaket zugewiesen werden. 

- Ist eine Gruppe Teil eines Zugriffspakets und wird ein Benutzer diesem Zugriffspaket zugewiesen, wird der Benutzer automatisch dieser Gruppe hinzugefügt (sofern noch nicht vorhanden).
- Läuft die Zuweisung des Benutzers zu einem Zugriffspaket ab, wird er aus der Gruppe entfernt, sofern er keinem anderen Zugriffspaket zugewiesen ist, dem diese Gruppe angehört.

Sie können eine beliebige Office 365-Gruppe oder Azure AD-Sicherheitsgruppe auswählen.  Administratoren können einem Katalog jede beliebige Gruppe hinzufügen. Das gleiche gilt für Katalogbesitzer, wenn sie Besitzer der Gruppe sind. Beachten Sie beim Auswählen einer Gruppe die folgenden Azure AD-Einschränkungen:

- Wird ein Benutzer (einschließlich eines Gasts) einer Gruppe als Mitglied hinzugefügt, sind alle anderen Mitglieder dieser Gruppe für ihn sichtbar.
- In Azure AD kann die Mitgliedschaft einer Gruppe, die über Windows Server Active Directory mit Azure AD Connect synchronisiert wurde, nicht geändert werden.  
- Die Mitgliedschaft von dynamischen Gruppen kann nicht durch Hinzufügen oder Entfernen eines Mitglieds aktualisiert werden. Daher eignen sich dynamische Gruppenmitgliedschaften nicht für die Azure AD-Berechtigungsverwaltung.

1. Klicken Sie auf der Seite **Hinzufügen von Ressourcenrollen zu einem Zugriffspaket** auf **Gruppen**, um den Bereich „Gruppen auswählen“ zu öffnen.

1. Wählen Sie die Gruppen aus, die Sie in das Zugriffspaket einschließen möchten.

    ![Zugriffspaket: Ressourcenrollen hinzufügen – Gruppen auswählen](./media/entitlement-management-access-package-edit/group-select.png)

1. Klicken Sie auf **Auswählen**.

1. Wählen Sie in der Liste **Rolle** die Option **Besitzer** oder **Mitglied** aus.

    In der Regel wird die Rolle „Mitglied“ ausgewählt. Mit der Rolle „Besitzer“ ist es Benutzern erlaubt, andere Mitglieder oder Besitzer hinzuzufügen oder zu entfernen.

    ![Zugriffspaket: Ressourcenrolle für eine Gruppe hinzufügen](./media/entitlement-management-access-package-edit/group-role.png)

1. Klicken Sie auf **Hinzufügen**.

    Alle Benutzer mit einer bestehenden Zuweisung zu dem Zugriffspaket werden durch das Hinzufügen der Gruppe automatisch Mitglied dieser Gruppe.

### <a name="add-an-application-resource-role"></a>Hinzufügen einer Ressourcenrolle für eine Anwendung

Wird ein Benutzer einem Zugriffspaket zugewiesen, kann ihm mit Azure AD automatisch der Zugriff auf eine Azure AD-Unternehmensanwendung zugewiesen werden, einschließlich SaaS-Anwendungen und Verbundanwendungen Ihrer Organisation mit Azure AD. Für Anwendungen, die in Azure AD über die einmalige Verbundanmeldung integriert sind, stellt Azure AD für Benutzer, die diesen Anwendungen zugewiesen sind, Verbundtoken aus.

Anwendungen können über mehrere Rollen verfügen. Wenn Sie einem Zugriffspaket eine Anwendung hinzufügen, die mehr als eine Rolle besitzt, müssen Sie für diese Benutzer die entsprechende Rolle angeben.  Als Entwickler von Anwendungen finden Sie die Vorgehensweise zum Bereitstellen dieser Rollen für Ihre Anwendungen im Artikel [Konfigurieren von im SAML-Token ausgestellten Rollenansprüchen für Unternehmensanwendungen](../develop/active-directory-enterprise-app-role-management.md).

Sobald eine Anwendungsrolle Teil eines Zugriffspakets ist, gilt Folgendes:

- Wird ein Benutzer dem Zugriffspaket zugewiesen, wird er der Anwendungsrolle hinzugefügt (sofern noch nicht vorhanden).
- Läuft die Zuweisung des Benutzers zu einem Zugriffspaket ab, wird dessen Zugriff aus der Anwendung entfernt, sofern er keinem anderen Zugriffspaket zugewiesen ist, dem diese Anwendungsrolle angehört.

Beachten Sie Folgendes, wenn Sie eine Anwendung auswählen:

- Anwendungen können auch Gruppen besitzen, die ihren Rollen zugewiesen sind.  Sie können wählen, ob Sie einem Zugriffspaket anstelle einer Anwendungsrolle eine Gruppe hinzufügen möchten. In diesem Fall wird die Anwendung dem Benutzer jedoch nicht als Teil des Zugriffspakets im Portal „Mein Zugriff“ angezeigt.

1. Klicken Sie auf der Seite **Hinzufügen von Ressourcenrollen zu einem Zugriffspaket** auf **Anwendungen**, um den Bereich „Anwendungen auswählen“ zu öffnen.

1. Wählen Sie die Anwendungen aus, die Sie in das Zugriffspaket einschließen möchten.

    ![Zugriffspaket: Ressourcenrollen hinzufügen – Anwendungen auswählen](./media/entitlement-management-access-package-edit/application-select.png)

1. Klicken Sie auf **Auswählen**.

1. Wählen Sie in der Liste **Rolle** eine Anwendungsrolle aus.

    ![Zugriffspaket: Ressourcenrolle für eine Anwendung hinzufügen](./media/entitlement-management-access-package-edit/application-role.png)

1. Klicken Sie auf **Hinzufügen**.

    Alle Benutzer mit einer bestehenden Zuweisung zu dem Zugriffspaket erhalten durch das Hinzufügen der Anwendung automatisch Zugriff auf diese Anwendung.

### <a name="add-a-sharepoint-site-resource-role"></a>Hinzufügen einer Ressourcenrolle für eine SharePoint-Website

Mit Azure AD kann Benutzern, die einem Zugriffspaket zugewiesen sind, automatisch Zugriff auf eine Website oder Websitesammlung von SharePoint Online zugewiesen werden.

1. Klicken Sie auf der Seite **Hinzufügen von Ressourcenrollen zu einem Zugriffspaket** auf **SharePoint-Websites**, um den Bereich „SharePoint Online-Websites auswählen“ zu öffnen.

1. Wählen Sie die SharePoint Online-Websites aus, die Sie in das Zugriffspaket einschließen möchten.

    ![Zugriffspaket: Ressourcenrollen hinzufügen – SharePoint Online-Websites auswählen](./media/entitlement-management-access-package-edit/sharepoint-site-select.png)

1. Klicken Sie auf **Auswählen**.

1. Wählen Sie in der Liste **Rolle** eine Rolle für die SharePoint Online-Website aus.

    ![Zugriffspaket: Ressourcenrolle für eine SharePoint Online-Website hinzufügen](./media/entitlement-management-access-package-edit/sharepoint-site-role.png)

1. Klicken Sie auf **Hinzufügen**.

    Alle Benutzer mit einer bestehenden Zuweisung zu dem Zugriffspaket erhalten durch das Hinzufügen der SharePoint Online-Website automatisch Zugriff auf diese Website.

## <a name="remove-resource-roles"></a>Entfernen von Ressourcenrollen

**Erforderliche Rolle**: Benutzeradministrator, Katalogbesitzer oder Zugriffspaket-Manager

1. Klicken Sie im Azure-Portal auf **Azure Active Directory** und dann auf **Identity Governance**.

1. Klicken Sie im Menü auf der linken Seite auf **Zugriffspakete**, und öffnen Sie das Zugriffspaket.

1. Klicken Sie im Menü auf der linken Seite auf **Ressourcenrollen**.

1. Suchen Sie in der Liste der Ressourcenrollen nach der Ressourcenrolle, die Sie entfernen möchten.

1. Klicken Sie auf die Auslassungspunkte ( **...** ) und dann auf **Ressourcenrolle entfernen**.

    Allen Benutzern mit einer bestehenden Zuweisung zu dem Zugriffspaket wird durch das Entfernen der Ressourcenrolle automatisch der Zugriff darauf widerrufen.

## <a name="add-a-new-policy"></a>Hinzufügen einer neuen Richtlinie

Wenn Sie angeben möchten, wer ein Zugriffspaket anfordern kann, müssen Sie eine Richtlinie erstellen. Sie können für ein einzelnes Zugriffspaket mehrere Richtlinien erstellen, wenn Sie unterschiedlichen Benutzergruppen Zuweisungen mit unterschiedlichen Genehmigungs- und Ablaufeinstellungen erteilen möchten. Mit einer einzelnen Richtlinie können einem Zugriffspaket nicht gleichzeitig interne und externe Benutzer zugewiesen werden. Erstellen Sie hierfür in einem Zugriffspaket zwei separate Richtlinien für interne bzw. externe Benutzer. Gelten für einen Benutzer mehrere Richtlinien, wird er beim Anfordern dazu aufgefordert, die Richtlinie auszuwählen, der er zugewiesen werden möchte.

Im folgenden Diagramm wird der allgemeine Prozess beim Erstellen einer Richtlinie für ein vorhandenes Zugriffspaket veranschaulicht:

![Prozess für das Erstellen einer Richtlinie](./media/entitlement-management-access-package-edit/policy-process.png)

**Erforderliche Rolle**: Benutzeradministrator, Katalogbesitzer oder Zugriffspaket-Manager

1. Klicken Sie im Azure-Portal auf **Azure Active Directory** und dann auf **Identity Governance**.

1. Klicken Sie im Menü auf der linken Seite auf **Zugriffspakete**, und öffnen Sie das Zugriffspaket.

1. Klicken Sie auf **Richtlinien** und dann auf **Richtlinie hinzufügen**.

1. Geben Sie einen Namen und eine Beschreibung für die Richtlinie ein.

    ![Erstellen der Richtlinie mit Name und Beschreibung](./media/entitlement-management-access-package-edit/policy-name-description.png)

1. Je nachdem, welche Auswahl Sie unter **Benutzer, die Zugriff anfordern können** getroffen haben, führen Sie die entsprechenden Schritte in den folgenden Abschnitten zur jeweiligen Richtlinie aus.

[!INCLUDE [Entitlement management policy](../../../includes/active-directory-entitlement-management-policy.md)]

## <a name="edit-an-existing-policy"></a>Bearbeiten einer vorhandenen Richtlinie

Sie können eine Richtlinie jederzeit bearbeiten. Wenn Sie das Ablaufdatum für eine Richtlinie ändern, ändert sich das Ablaufdatum für Anforderungen mit ausstehender Genehmigung oder genehmigte Anforderungen nicht.

**Erforderliche Rolle**: Benutzeradministrator, Katalogbesitzer oder Zugriffspaket-Manager

1. Klicken Sie im Azure-Portal auf **Azure Active Directory** und dann auf **Identity Governance**.

1. Klicken Sie im Menü auf der linken Seite auf **Zugriffspakete**, und öffnen Sie das Zugriffspaket.

1. Klicken Sie auf **Richtlinien** und anschließend auf die Richtline, die Sie bearbeiten möchten.

    Der Bereich **Richtliniendetails** wird am unteren Rand der Seite geöffnet.

    ![Zugriffspaket: Bereich „Richtliniendetails“](./media/entitlement-management-access-package-edit/policy-details.png)

1. Klicken Sie auf **Bearbeiten**, um die Richtlinie zu bearbeiten.

    ![Zugriffspaket: Richtlinie bearbeiten](./media/entitlement-management-access-package-edit/policy-edit.png)

1. Klicken Sie abschließend auf **Aktualisieren**.

## <a name="directly-assign-a-user"></a>Direktes Zuweisen eines Benutzers

In einigen Fällen möchten Sie bestimmte Benutzer möglicherweise einem Zugriffspaket direkt zuweisen, damit diese das Zugriffspaket nicht extra anfordern müssen. Damit dies möglich ist, muss das Zugriffspaket eine Richtlinie enthalten, die direkte Zuweisungen eines Administrator erlaubt.

**Erforderliche Rolle**: Benutzeradministrator, Katalogbesitzer oder Zugriffspaket-Manager

1. Klicken Sie im Azure-Portal auf **Azure Active Directory** und dann auf **Identity Governance**.

1. Klicken Sie im Menü auf der linken Seite auf **Zugriffspakete**, und öffnen Sie das Zugriffspaket.

1. Klicken Sie im Menü auf der linken Seite auf **Zuweisungen**.

1. Klicken Sie auf **Neue Zuweisung**, um die Seite „Hinzufügen von Benutzern zu einem Zugriffspaket“ zu öffnen.

    ![Zuweisungen: Benutzer einem Zugriffspaket hinzufügen](./media/entitlement-management-access-package-edit/assignments-add-user.png)

1. Klicken Sie auf **Benutzer hinzufügen**, um die Benutzer auszuwählen, die Sie dem Zugriffspaket zuweisen möchten.

1. Wählen Sie in der Liste **Richtlinie auswählen** eine Richtlinie mit der Einstellung [Keine (nur direkte Zuweisungen eines Administrators)](#policy-none-administrator-direct-assignments-only) aus.

    Enthält das Zugriffspaket keine Richtlinien dieses Typs, können Sie diese hinzufügen. Klicken Sie dazu auf **Neue Richtlinie erstellen**.

1. Legen Sie Datum und Uhrzeit fest, wann die Zuweisung der ausgewählten Benutzer beginnen und enden soll. Wird kein Enddatum angegeben, gelten die Ablaufeinstellungen der Richtlinie.

1. Geben Sie zur Datenbankpflege optional eine Begründung für Ihre direkte Zuweisung an.

1. Klicken Sie auf **Hinzufügen**, um die ausgewählten Benutzer dem Zugriffspaket direkt zuzuweisen.

    Klicken Sie nach kurzer Zeit auf **Aktualisieren**, um die Benutzer in der Liste „Zuweisungen“ anzuzeigen.

## <a name="view-who-has-an-assignment"></a>Anzeigen von Benutzern mit Zuweisung

**Erforderliche Rolle**: Benutzeradministrator, Katalogbesitzer oder Zugriffspaket-Manager

1. Klicken Sie im Azure-Portal auf **Azure Active Directory** und dann auf **Identity Governance**.

1. Klicken Sie im Menü auf der linken Seite auf **Zugriffspakete**, und öffnen Sie das Zugriffspaket.

1. Klicken Sie auf **Zuweisungen**, um eine Liste der aktiven Zuweisungen anzuzeigen.

1. Klicken Sie auf eine bestimmte Zuweisung, wenn Sie zusätzliche Details anzeigen möchten.

1. Wenn Sie die Liste der Zuweisungen mit nicht ordnungsgemäß bereitgestellten Ressourcenrollen anzeigen möchten, klicken Sie auf den Filterstatus, und wählen Sie **Übermitteln** aus.

    Zusätzliche Details zu Übermittlungsfehlern werden auf der Seite **Anforderungen** unter der Anforderung des jeweiligen Benutzers angezeigt.

1. Klicken Sie zum Anzeigen von abgelaufenen Zuweisungen auf den Filterstatus, und wählen Sie **Abgelaufen** aus.

1. Wenn Sie eine CSV-Datei aus der gefilterten Liste herunterladen möchten, klicken Sie auf **Herunterladen**.

## <a name="view-requests"></a>Anzeigen von Anforderungen

**Erforderliche Rolle**: Benutzeradministrator, Katalogbesitzer oder Zugriffspaket-Manager

1. Klicken Sie im Azure-Portal auf **Azure Active Directory** und dann auf **Identity Governance**.

1. Klicken Sie im Menü auf der linken Seite auf **Zugriffspakete**, und öffnen Sie das Zugriffspaket.

1. Klicken Sie auf **Anforderungen**.

1. Klicken Sie auf eine bestimmte Anforderung, wenn Sie zusätzliche Details anzeigen möchten.

## <a name="view-a-requests-delivery-errors"></a>Anzeigen der Fehler bei der Übermittlung einer Anforderung

**Erforderliche Rolle**: Benutzeradministrator, Katalogbesitzer oder Zugriffspaket-Manager

1. Klicken Sie im Azure-Portal auf **Azure Active Directory** und dann auf **Identity Governance**.

1. Klicken Sie im Menü auf der linken Seite auf **Zugriffspakete**, und öffnen Sie das Zugriffspaket.

1. Klicken Sie auf **Anforderungen**.

1. Wählen Sie die Anforderung aus, die angezeigt werden soll.

    Sind bei der Übermittlung der Anforderung Fehler aufgetreten, lautet der Status der Anforderung **Nicht übermittelt** und der Unterstatus **Teilweise übermittelt**.

    Sind Übermittlungsfehler aufgetreten, wird deren Anzahl im Detailbereich der Anforderung angezeigt.

1. Klicken Sie auf die Anzahl, um alle Fehler bei der Übermittlung der Anforderung anzuzeigen.

## <a name="cancel-a-pending-request"></a>Abbrechen einer ausstehenden Anforderung

Es können nur ausstehende Anforderungen abgebrochen werden, die noch nicht übermittelt wurden.

**Erforderliche Rolle**: Benutzeradministrator, Katalogbesitzer oder Zugriffspaket-Manager

1. Klicken Sie im Azure-Portal auf **Azure Active Directory** und dann auf **Identity Governance**.

1. Klicken Sie im Menü auf der linken Seite auf **Zugriffspakete**, und öffnen Sie das Zugriffspaket.

1. Klicken Sie auf **Anforderungen**.

1. Wählen Sie die Anforderung aus, die Sie abbrechen möchten.

1. Klicken Sie im Detailbereich der Anforderung auf **Anforderung abbrechen**.

## <a name="copy-my-access-portal-link"></a>Kopieren des Links zum Portal „Mein Zugriff“

Die meisten Benutzer in Ihrem Verzeichnis können sich beim Portal „Mein Zugriff“ anmelden, wo automatisch eine Liste mit den Zugriffspaketen, die sie anfordern können, angezeigt wird. Handelt es sich bei den Benutzern jedoch um externe Geschäftspartner, die sich noch nicht in Ihrem Verzeichnis befinden, müssen Sie diesen einen Link zur Anforderung eines Zugriffspakets senden. Ist das Zugriffspaket für externe Benutzer aktiviert und enthält es eine Richtlinie für das Verzeichnis des externen Benutzers, kann er über den Link zum Portal „Mein Zugriff“ das Zugriffspaket anfordern.

**Erforderliche Rolle**: Benutzeradministrator, Katalogbesitzer oder Zugriffspaket-Manager

1. Klicken Sie im Azure-Portal auf **Azure Active Directory** und dann auf **Identity Governance**.

1. Klicken Sie im Menü auf der linken Seite auf **Zugriffspakete**, und öffnen Sie das Zugriffspaket.

1. Kopieren Sie auf der Seite „Übersicht“ den **Link zum Portal „Mein Zugriff“** .

    ![Zugriffspaket (Übersicht): Link zum Portal „Mein Zugriff“](./media/entitlement-management-shared/my-access-portal-link.png)

1. Senden Sie den Link per E-Mail an Ihren externen Geschäftspartner. Dieser kann den Link für seine Benutzer zur Anforderung des Zugriffspakets freigeben.

## <a name="change-the-hidden-setting"></a>Ändern der Einstellung „Ausgeblendet“

Zugriffspakete sind standardmäßig sichtbar. Dies bedeutet, dass einem Benutzer ein Zugriffspaket im Portal „Mein Zugriff“ automatisch aufgeführt wird, wenn ihm das Anfordern des Zugriffspakets in einer Richtlinie erlaubt wird.

**Erforderliche Rolle**: Benutzeradministrator, Katalogbesitzer oder Zugriffspaket-Manager

1. Klicken Sie im Azure-Portal auf **Azure Active Directory** und dann auf **Identity Governance**.

1. Klicken Sie im Menü auf der linken Seite auf **Zugriffspakete**, und öffnen Sie das Zugriffspaket.

1. Klicken Sie auf der Seite „Übersicht“ auf **Bearbeiten**.

1. Legen Sie die Einstellung **Ausgeblendet** fest:

    Bei **Nein** wird das Zugriffspaket im Portal „Mein Zugriff“ des Benutzers aufgeführt.

    Bei **Ja** wird das Zugriffspaket im Portal „Mein Zugriff“ des Benutzers nicht aufgeführt. In diesem Fall kann der Benutzer das Zugriffspaket nur über den direkten Link auf das Zugriffspaket im **Portal „Mein Zugriff“** anzeigen.

## <a name="delete"></a>Löschen

Es können nur Zugriffspakete ohne aktive Benutzerzuweisungen gelöscht werden.

**Erforderliche Rolle**: Benutzeradministrator, Katalogbesitzer oder Zugriffspaket-Manager

1. Klicken Sie im Azure-Portal auf **Azure Active Directory** und dann auf **Identity Governance**.

1. Klicken Sie im Menü auf der linken Seite auf **Zugriffspakete**, und öffnen Sie das Zugriffspaket.

1. Klicken Sie im Menü auf der linken Seite auf **Zuweisungen**, und entfernen Sie für alle Benutzer den Zugriff.

1. Klicken Sie im Menü auf der linken Seite auf **Übersicht** und dann auf **Löschen**.

1. Klicken Sie im angezeigten Meldungsfeld auf **Ja**.

## <a name="when-are-changes-applied"></a>Übernahme der Änderungen

In der Azure AD-Berechtigungsverwaltung werden Massenänderungen an Zuweisungen und Ressourcen in Zugriffspaketen mehrmals täglich verarbeitet. Wenn Sie also eine Zuweisung vornehmen oder die Ressourcenrollen Ihres Zugriffspakets ändern, kann es bis zu 24 Stunden dauern, bis diese Änderungen in Azure AD übernommen werden. Hinzu kommt die Zeit für die Weitergabe der Änderungen an andere Microsoft Online Services oder verbundene SaaS-Anwendungen. Betrifft Ihre Änderung nur einige wenige Objekte, wird sie wahrscheinlich innerhalb von ein paar Minuten in Azure AD übernommen. Anschließend wird diese Änderung von anderen Azure AD-Komponenten erkannt, und die SaaS-Anwendungen werden aktualisiert. Betrifft die Änderung hingegen Tausende von Objekten, dauert die Übernahme länger. Enthält Ihr Zugriffspaket beispielsweise zwei Anwendungen und 100 Benutzerzuweisungen, und Sie möchten dem Zugriffspaket eine Rolle für eine SharePoint-Website hinzufügen, kann es möglicherweise eine Weile dauern, bis alle Benutzer Teil dieser SharePoint-Rolle sind. Sie können den Fortschritt über das Azure AD-Überwachungsprotokoll, das Azure AD-Bereitstellungsprotokoll und die Überwachungsprotokolle der SharePoint-Website überwachen.

## <a name="next-steps"></a>Nächste Schritte

- [Anforderungsprozess und E-Mail-Benachrichtigungen](entitlement-management-process.md)
