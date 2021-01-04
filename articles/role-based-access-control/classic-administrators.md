---
title: Verwaltung von Azure-Abonnements im klassischem Bereitstellungsmodell
description: Informationen zum Hinzufügen oder Ändern der Azure-Rollen „Co-Administrator“ und „Dienstadministrator“ sowie zum Anzeigen des Kontoadministrators.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/30/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 4c758dca9f6020fd948ad8c3a8cdf014306e8ae7
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96343995"
---
# <a name="azure-classic-subscription-administrators"></a>Verwaltung von Azure-Abonnements im klassischem Bereitstellungsmodell

Microsoft empfiehlt das Verwalten des Zugriffs auf Azure-Ressourcen mithilfe der rollenbasierten Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC). Wenn Sie jedoch immer noch das klassische Bereitstellungsmodell verwenden, müssen Sie eine klassische Rolle des Typs „Abonnementadministrator“ verwenden: Dienstadministrator und Co-Administrator. Weitere Informationen finden Sie unter [Azure Resource Manager-Bereitstellung im Vergleich zur klassischen Bereitstellung](../azure-resource-manager/management/deployment-models.md).

In diesem Artikel wird das Hinzufügen oder Ändern der Azure-Rollen „Co-Administrator“ und „Dienstadministrator“ sowie das Anzeigen des Kontoadministrators beschrieben.

## <a name="add-a-co-administrator"></a>Hinzufügen eines Co-Administrators

> [!TIP]
> Sie müssen lediglich einen Co-Administrator hinzufügen, wenn der Benutzer klassische Azure-Bereitstellungen verwalten soll, indem Sie das [PowerShell-Modul „Azure Service Management“](/powershell/module/servicemanagement/azure.service) verwenden. Wenn der Benutzer nur das Azure-Portal zum Verwalten der klassischen Ressourcen verwenden, müssen Sie den klassischen Administrator für den Benutzer nicht hinzufügen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) als Dienstadministrator oder Co-Administrator an.

1. Öffnen Sie [Abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), und wählen Sie ein Abonnement aus.

    Co-Administratoren können nur auf Abonnementebene zugewiesen werden.

1. Klicken Sie auf **Zugriffssteuerung (IAM)** .

1. Klicken Sie auf die Registerkarte **Klassische Administratoren**.

    ![Screenshot des Öffnens von „Klassische Administratoren“](./media/classic-administrators/classic-administrators.png)

1. Klicken Sie auf **Hinzufügen** > **Co-Administrator hinzufügen**, um den Bereich „Co-Administratoren hinzufügen“ zu öffnen.

    Wenn die Schaltfläche „Co-Administrator hinzufügen“ deaktiviert ist, fehlen Ihnen Berechtigungen.

1. Wählen Sie den Benutzer aus, den Sie hinzufügen möchten, und klicken Sie auf **Hinzufügen**.

    ![Screenshot, auf dem der Co-Administrator hinzugefügt wird](./media/classic-administrators/add-coadmin.png)

## <a name="add-a-guest-user-as-a-co-administrator"></a>Hinzufügen eines Gastbenutzers als Co-Administrator

Wenn Sie einen Gastbenutzer als Co-Administrator hinzufügen möchten, führen Sie dieselben Schritte wie im obigen Abschnitt [Hinzufügen eines Co-Administrators](#add-a-co-administrator) aus. Der Gastbenutzer muss die folgenden Kriterien erfüllen:

- Der Gastbenutzer muss in Ihrem Verzeichnis vorhanden sein. Dies bedeutet, dass der Benutzer eine Einladung zu Ihrem Verzeichnis erhalten und diese akzeptiert haben muss.

Weitere Informationen zum Hinzufügen eines Gastbenutzers zu Ihrem Verzeichnis finden Sie unter [Hinzufügen von Azure Active Directory B2B-Zusammenarbeitsbenutzern über das Azure-Portal](../active-directory/external-identities/add-users-administrator.md).

### <a name="differences-for-guest-users"></a>Unterschiede für Gastbenutzer

Bei Gastbenutzern mit der Rolle „Co-Administrator“ gibt es im Vergleich zu Mitgliedsbenutzern mit der Rolle „Co-Administrator“ unter Umständen gewisse Unterschiede. Nehmen Sie das folgende Szenario als Beispiel:

- Benutzer A verfügt über ein Azure AD-Konto (Geschäfts-, Schul- oder Unikonto) und ist Dienstadministrator für ein Azure-Abonnement.
- Benutzer B verfügt über ein Microsoft-Konto.
- Benutzer A weist Benutzer B die Rolle „Co-Administrator“ zu.
- Benutzer B kann fast alle Aktionen ausführen, aber keine Anwendungen registrieren oder Benutzer im Azure AD-Verzeichnis suchen.

Man würde erwarten, dass Benutzer B alles verwalten kann. Der Grund für den Unterschied ist, dass das Microsoft-Konto dem Abonnement nicht als Mitgliedsbenutzer, sondern als Gastbenutzer hinzugefügt wird. Gastbenutzer haben im Vergleich zu Mitgliedsbenutzern andere Standardberechtigungen in Azure AD. So können Mitgliedsbenutzer im Gegensatz zu Gastbenutzern beispielsweise andere Benutzer in Azure AD lesen. Mitgliedsbenutzer können neue Dienstprinzipale in Azure AD registrieren, Gastbenutzer nicht.

Wenn ein Gastbenutzer in der Lage sein muss, diese Aufgaben auszuführen, können ihm die spezifischen Azure AD-Rollen zugewiesen werden, die er benötigt. Im vorherigen Szenario könnten Sie ihm also die Rolle [Verzeichnis lesen](../active-directory/roles/permissions-reference.md#directory-readers) zum Lesen anderer Benutzer und die Rolle [Anwendungsentwickler](../active-directory/roles/permissions-reference.md#application-developer) zum Erstellen von Dienstprinzipalen zuweisen. Weitere Informationen zu Mitglieds- und Gastbenutzern sowie zu deren Berechtigungen finden Sie unter [Welche Standardbenutzerberechtigungen gibt es in Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md). Weitere Informationen zum Gewähren des Zugriffs für Gastbenutzer finden Sie unter [Hinzufügen oder Entfernen von Rollenzuweisungen für externe Gastbenutzer mithilfe von Azure RBAC und dem Azure-Portal](role-assignments-external-users.md).

Beachten Sie, dass sich die [integrierten Azure-Rollen](../role-based-access-control/built-in-roles.md) von den [Azure AD-Rollen](../active-directory/roles/permissions-reference.md) unterscheiden. Die integrierten Rollen ermöglichen keinerlei Azure AD-Zugriff. Weitere Informationen finden Sie unter [Grundlegendes zu den verschiedenen Rollen](../role-based-access-control/rbac-and-directory-admin-roles.md).

Eine vergleichende Betrachtung zu Mitglieds- und Gastbenutzern finden Sie unter [Welche Standardbenutzerberechtigungen gibt es in Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md).

## <a name="remove-a-co-administrator"></a>Entfernen eines Co-Administrators

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) als Dienstadministrator oder Co-Administrator an.

1. Öffnen Sie [Abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), und wählen Sie ein Abonnement aus.

1. Klicken Sie auf **Zugriffssteuerung (IAM)** .

1. Klicken Sie auf die Registerkarte **Klassische Administratoren**.

1. Fügen Sie ein Häkchen neben dem Co-Administrator hinzu, den Sie entfernen möchten.

1. Klicken Sie auf **Entfernen**.

1. Klicken Sie im angezeigten Meldungsfeld auf **Ja**.

    ![Screenshot, auf dem der Co-Administrator entfernt wird](./media/classic-administrators/remove-coadmin.png)

## <a name="change-the-service-administrator"></a>Ändern des Dienstadministrators

Nur der Kontoadministrator kann den Dienstadministrator für ein Abonnement ändern. Wenn Sie sich für ein Azure-Abonnement registrieren, ist der Dienstadministrator standardmäßig mit dem Kontoadministrator identisch. Der Benutzer mit der Rolle „Kontoadministrator“ kann nicht auf das Azure-Portal zugreifen. Der Benutzer mit der Rolle „Dienstadministrator“ hat Vollzugriff auf das Azure-Portal. Wenn Kontoadministrator und Dienstadministrator identisch sind und Sie den Dienstadministrator in einen anderen Benutzer ändern, verliert der Kontoadministrator den Zugriff auf das Azure-Portal. Der Kontoadministrator kann aber immer das Kontocenter verwenden, um den Dienstadministrator wieder auf sich selbst festzulegen.

Befolgen Sie diese Schritte, um den Dienstadministrator im Azure-Portal zu ändern.

1. Stellen Sie sicher, dass Ihr Szenario unterstützt wird, indem Sie die [Einschränkungen beim Ändern des Dienstadministrators](#limitations-for-changing-the-service-administrator) überprüfen.

1. Melden Sie sich als Kontoadministrator beim [Azure-Portal](https://portal.azure.com) an.

1. Öffnen Sie **Cost Management + Billing**, und wählen Sie ein Abonnement aus.

1. Klicken Sie im linken Navigationsbereich auf **Eigenschaften**.

1. Klicken Sie auf **Dienstadministrator**.

    ![Screenshot der Abonnementeigenschaften im Azure-Portal](./media/classic-administrators/service-admin.png)

1. Geben Sie auf der Seite **Dienstadministrator bearbeiten** die E-Mail-Adresse des neuen Dienstadministrators ein.

    ![Screenshot der Seite „Dienstadministrator bearbeiten“](./media/classic-administrators/service-admin-edit.png)

1. Klicken Sie auf **OK** , um die Änderung zu speichern.

### <a name="limitations-for-changing-the-service-administrator"></a>Einschränkungen beim Ändern des Dienstadministrators

Pro Azure-Abonnement kann es nur einen Dienstadministrator geben. Der Dienstadministrator lässt sich unterschiedlich ändern, je nachdem, ob der Kontoadministrator ein Microsoft-Konto hat oder ob es sich um ein Azure AD-Konto (Geschäfts-, Schul-oder Unikonto) handelt.

| Konto für Kontoadministratoren | Kann den Dienstadministrator in ein anderes Microsoft-Konto ändern? | Kann den Dienstadministrator im gleichen Verzeichnis in ein Azure AD-Konto ändern? | Kann den Dienstadministrator in einem anderen Verzeichnis in ein Azure AD-Konto ändern? |
| --- | --- | --- | --- |
| Microsoft-Konto | Ja | Nein | Nein |
| Azure AD-Konto | Ja | Ja | Nein |

Wenn der Kontoadministrator ein Azure AD-Konto ist, können Sie den Dienstadministrator in ein Azure AD-Konto in demselben Verzeichnis ändern, aber nicht in einem anderen Verzeichnis. Beispielsweise kann abby@contoso.com den Dienstadministrator in bob@contoso.com ändern, aber nicht den Dienstadministrator in john@notcontoso.com, es sei denn, john@notcontoso.com ist im Verzeichnis contoso.com vorhanden.

Weitere Informationen zu Microsoft-Konten und Azure AD-Konten finden Sie unter [Was ist Azure Active Directory?](../active-directory/fundamentals/active-directory-whatis.md).

## <a name="view-the-account-administrator"></a>Anzeigen des Kontoadministrators

Der Kontoadministrator ist der Benutzer, der sich ursprünglich für das Azure-Abonnement registriert hat, und der (in Bezug auf die Abrechnung) verantwortliche Besitzer des Abonnements. Informationen zum Ändern des Kontoadministrators für ein Abonnement finden Sie unter [Übertragen des Besitzes eines Azure-Abonnements auf ein anderes Konto](../cost-management-billing/manage/billing-subscription-transfer.md).

Gehen Sie folgendermaßen vor, um den Kontoadministrator anzuzeigen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Öffnen Sie **Cost Management + Billing**, und wählen Sie ein Abonnement aus.

1. Klicken Sie im linken Navigationsbereich auf **Eigenschaften**.

    Der Kontoadministrator des Abonnements wird im Feld **Kontoadministrator** angezeigt.

    ![Screenshot des Kontoadministrators](./media/classic-administrators/account-admin.png)

## <a name="next-steps"></a>Nächste Schritte

* [Grundlegendes zu den verschiedenen Rollen](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Hinzufügen oder Entfernen von Rollenzuweisungen mithilfe von Azure RBAC und dem Azure-Portal](../role-based-access-control/role-assignments-portal.md)
* [Hinzufügen oder Ändern von Azure-Abonnementadministratoren](../cost-management-billing/manage/add-change-subscription-administrator.md)