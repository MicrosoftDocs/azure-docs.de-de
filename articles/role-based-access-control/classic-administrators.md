---
title: Verwaltung von Azure-Abonnements im klassischem Bereitstellungsmodell | Microsoft-Dokumentation
description: Informationen zum Hinzufügen oder Ändern der Azure-Rollen „Co-Administrator“ und „Dienstadministrator“ sowie zum Anzeigen des Kontoadministrators.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/19/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 9c3bd2480853f5c4134cd560c20a6007b044e138
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64924650"
---
# <a name="azure-classic-subscription-administrators"></a>Verwaltung von Azure-Abonnements im klassischem Bereitstellungsmodell

Microsoft empfiehlt das Verwalten des Zugriffs auf Azure-Ressourcen mithilfe der rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC). Wenn Sie jedoch immer noch das klassische Bereitstellungsmodell verwenden, müssen Sie eine klassische Rolle des Typs „Abonnementadministrator“ verwenden: Dienstadministrator und Co-Administrator. Weitere Informationen finden Sie unter [Azure Resource Manager-Bereitstellung im Vergleich zur klassischen Bereitstellung](../azure-resource-manager/resource-manager-deployment-model.md).

In diesem Artikel wird das Hinzufügen oder Ändern der Azure-Rollen „Co-Administrator“ und „Dienstadministrator“ sowie das Anzeigen des Kontoadministrators beschrieben.

## <a name="add-a-co-administrator"></a>Hinzufügen eines Co-Administrators

> [!TIP]
> Sie müssen lediglich einen Co-Administrator hinzufügen, wenn der Benutzer klassische Azure-Bereitstellungen verwalten soll, indem Sie das [PowerShell-Modul „Azure Service Management“](https://docs.microsoft.com/powershell/module/servicemanagement/azure) verwenden. Wenn der Benutzer nur das Azure-Portal zum Verwalten der klassischen Ressourcen verwenden, müssen Sie den klassischen Administrator für den Benutzer nicht hinzufügen.

1. Melden Sie sich als beim [Azure-Portal](https://portal.azure.com) als Dienstadministrator an.

1. Öffnen Sie [Abonnement](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), und wählen Sie ein Abonnement aus.

    Co-Administratoren können nur auf Abonnementebene zugewiesen werden.

1. Klicken Sie auf **Zugriffssteuerung (IAM)** .

1. Klicken Sie auf die Registerkarte **Klassische Administratoren**.

    ![Screenshot des Öffnens von „Klassische Administratoren“](./media/classic-administrators/classic-administrators.png)

1. Klicken Sie auf **Hinzufügen** > **Co-Administrator hinzufügen**, um den Bereich „Co-Administratoren hinzufügen“ zu öffnen.

    Wenn die Schaltfläche „Co-Administrator hinzufügen“ deaktiviert ist, fehlen Ihnen Berechtigungen.

1. Wählen Sie den Benutzer aus, den Sie hinzufügen möchten, und klicken Sie auf **Hinzufügen**.

    ![Screenshot, auf dem der Co-Administrator hinzugefügt wird](./media/classic-administrators/add-coadmin.png)

### <a name="adding-a-guest-user-as-a-co-administrator"></a>Hinzufügen eines Gastbenutzers als Co-Administrator

Bei [Gastbenutzern](../active-directory/b2b/b2b-quickstart-add-guest-users-portal.md) mit der Rolle „Co-Administrator“ gibt es im Vergleich zu Mitgliedsbenutzern mit der Rolle „Co-Administrator“ unter Umständen gewisse Unterschiede. Stellen Sie sich folgendes Szenario vor:

- Benutzer A verfügt über ein Geschäfts-, Schul- oder Unikonto für Azure AD und ist Dienstadministrator für ein Azure-Abonnement.
- Benutzer B verfügt über ein Microsoft-Konto.
- Benutzer A weist Benutzer B die Rolle „Co-Administrator“ zu.
- Benutzer B kann fast alle Aktionen ausführen, aber keine Anwendungen registrieren oder Benutzer im Azure AD-Verzeichnis suchen.

Man würde erwarten, dass Benutzer B alles verwalten kann. Der Grund für den Unterschied ist, dass das Microsoft-Konto dem Abonnement nicht als Mitgliedsbenutzer, sondern als Gastbenutzer hinzugefügt wird. Gastbenutzer haben im Vergleich zu Mitgliedsbenutzern andere Standardberechtigungen in Azure AD. So können Mitgliedsbenutzer im Gegensatz zu Gastbenutzern beispielsweise andere Benutzer in Azure AD lesen. Mitgliedsbenutzer können neue Dienstprinzipale in Azure AD registrieren, Gastbenutzer nicht. Wenn ein Gastbenutzer in der Lage sein muss, diese Aufgaben auszuführen, können ihm die spezifischen Azure AD-Administratorrollen zugewiesen werden, die der Gastbenutzer benötigt. Im vorherigen Szenario könnten Sie ihm also die Rolle [Verzeichnis lesen](../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) zum Lesen anderer Benutzer und die Rolle [Anwendungsentwickler](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-developer) zum Erstellen von Dienstprinzipalen zuweisen. Weitere Informationen zu Mitglieds- und Gastbenutzern sowie zu deren Berechtigungen finden Sie unter [Welche Standardbenutzerberechtigungen gibt es in Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md).

Beachten Sie, dass sich die [integrierten Rollen für Azure-Ressourcen](../role-based-access-control/built-in-roles.md) von den [Azure AD-Administratorrollen](../active-directory/users-groups-roles/directory-assign-admin-roles.md) unterscheiden. Die integrierten Rollen ermöglichen keinerlei Azure AD-Zugriff. Weitere Informationen finden Sie unter [Grundlegendes zu den verschiedenen Rollen](../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="remove-a-co-administrator"></a>Entfernen eines Co-Administrators

1. Melden Sie sich als beim [Azure-Portal](https://portal.azure.com) als Dienstadministrator an.

1. Öffnen Sie [Abonnement](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), und wählen Sie ein Abonnement aus.

1. Klicken Sie auf **Zugriffssteuerung (IAM)** .

1. Klicken Sie auf die Registerkarte **Klassische Administratoren**.

1. Fügen Sie ein Häkchen neben dem Co-Administrator hinzu, den Sie entfernen möchten.

1. Klicken Sie auf **Entfernen**.

1. Klicken Sie im angezeigten Meldungsfeld auf **Ja**.

    ![Screenshot, auf dem der Co-Administrator entfernt wird](./media/classic-administrators/remove-coadmin.png)

## <a name="change-the-service-administrator"></a>Ändern des Dienstadministrators

Nur der Kontoadministrator kann den Dienstadministrator für ein Abonnement ändern. Wenn Sie sich für ein Azure-Abonnement registrieren, ist der Dienstadministrator standardmäßig mit dem Kontoadministrator identisch. Der Benutzer mit der Rolle „Kontoadministrator“ kann nicht auf das Azure-Portal zugreifen. Der Benutzer mit der Rolle „Dienstadministrator“ hat Vollzugriff auf das Azure-Portal. Wenn Kontoadministrator und Dienstadministrator identisch sind und Sie den Dienstadministrator in einen anderen Benutzer ändern, verliert der Kontoadministrator den Zugriff auf das Azure-Portal. Der Kontoadministrator kann aber immer das Kontocenter verwenden, um den Dienstadministrator wieder auf sich selbst festzulegen.

Es gibt zwei Möglichkeiten zum Ändern des Dienstadministrators. Sie können diese Änderung im **Azure-Portal** oder **Kontocenter** vornehmen.

### <a name="azure-portal"></a>Azure-Portal

1. Stellen Sie sicher, dass Ihr Szenario unterstützt wird, indem Sie die Einschränkungen beim Ändern von Dienstadministratoren überprüfen.

1. Melden Sie sich als Kontoadministrator beim [Azure-Portal](https://portal.azure.com) an.

1. Öffnen Sie [Abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), und wählen Sie ein Abonnement aus.

1. Klicken Sie auf **Eigenschaften**.

    ![Screenshot des Kontoadministrators](./media/classic-administrators/account-admin.png)

1. Klicken Sie oben auf **Dienstadministrator** um den Bereich „Dienstadministrator“ zu öffnen.

    Wenn die Schaltfläche „Dienstadministrator“ deaktiviert ist, fehlen Ihnen Berechtigungen. Nur der Benutzer mit der Rolle „Kontoadministrator“ kann den Dienstadministrator ändern.

1. Wählen Sie einen neuen Dienstadministrator aus, und klicken Sie dann auf **Speichern**.

### <a name="account-center"></a>Kontocenter

1. Stellen Sie sicher, dass Ihr Szenario unterstützt wird, indem Sie die Einschränkungen beim Ändern von Dienstadministratoren überprüfen.

1. Melden Sie sich beim [Kontocenter](https://account.windowsazure.com/subscriptions) als Kontoadministrator an.

1. Klicken Sie auf ein Abonnement.

1. Klicken Sie auf der rechten Seite auf **Abonnementdetails bearbeiten**.

    ![Screenshot mit der Schaltfläche „Abonnementdetails bearbeiten“ im Kontocenter](./media/classic-administrators/editsub.png)

1. Geben Sie im Feld **DIENSTADMINISTRATOR** die E-Mail-Adresse des neuen Dienstadministrators ein.

    ![Screenshot mit dem Feld zum Ändern der E-Mail-Adresse des Dienstadministrators](./media/classic-administrators/change-service-admin.png)

1. Klicken Sie auf das Häkchen, um die Änderung zu speichern.

### <a name="limitations-for-changing-the-service-administrator"></a>Einschränkungen beim Ändern des Dienstadministrators

Jedes Abonnement ist einem Azure AD-Verzeichnis zugeordnet. Sie finden das Verzeichnis, dem das Abonnement zugeordnet ist, indem Sie im Azure-Portal zu **Abonnements** navigieren und ein Abonnement auswählen, um das Verzeichnis anzuzeigen.

Wenn Sie mit einem Geschäfts-, Schul- oder Unikonto angemeldet sind, können Sie andere Konten in Ihrer Organisation als Dienstadministrator hinzufügen. Beispielsweise kann abby@contoso.com das Konto bob@contoso.com als Dienstadministrator hinzufügen, aber nicht john@notcontoso.com, es sei denn, john@notcontoso.com ist im Verzeichnis „contoso.com“ enthalten. Mit einem Geschäfts-, Schul- oder Unikonto angemeldete Benutzer können Benutzer mit Microsoft-Konten weiterhin als Dienstadministrator hinzufügen.

  | Anmeldemethode | Microsoft-Kontobenutzer als Dienstadministrator hinzufügen? | Geschäfts-, Schul- oder Unikonto in der gleichen Organisation als Dienstadministrator hinzufügen? | Geschäfts-, Schul- oder Unikonto in einer anderen Organisation als Dienstadministrator hinzufügen? |
  | --- | --- | --- | --- |
  |  Microsoft-Konto |Ja |Nein |Nein |
  |  Geschäfts- oder Schulkonto |Ja |Ja |Nein |

## <a name="view-the-account-administrator"></a>Anzeigen des Kontoadministrators

Der Kontoadministrator ist der Benutzer, der sich ursprünglich für das Azure-Abonnement registriert hat, und der (in Bezug auf die Abrechnung) verantwortliche Besitzer des Abonnements. Informationen zum Ändern des Kontoadministrators für ein Abonnement finden Sie unter [Übertragen des Besitzes eines Azure-Abonnements auf ein anderes Konto](../billing/billing-subscription-transfer.md).

Gehen Sie folgendermaßen vor, um den Kontoadministrator anzuzeigen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Öffnen Sie [Abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), und wählen Sie ein Abonnement aus.

1. Klicken Sie auf **Eigenschaften**.

    Der Kontoadministrator des Abonnements wird im Feld **Kontoadministrator** angezeigt.

    ![Screenshot des Kontoadministrators](./media/classic-administrators/account-admin.png)

## <a name="next-steps"></a>Nächste Schritte

* [Grundlegendes zu den verschiedenen Rollen in Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Verwalten des Zugriffs auf Azure-Ressourcen mit RBAC und dem Azure-Portal](../role-based-access-control/role-assignments-portal.md)
* [Hinzufügen oder Ändern von Azure-Abonnementadministratoren](../billing/billing-add-change-azure-subscription-administrator.md)
