---
title: Hinzufügen eines vorhandenen Azure-Abonnements zu Ihrem Mandanten – Azure Active Directory | Microsoft-Dokumentation
description: Anleitungen zum Hinzufügen eines vorhandenen Azure-Abonnements zu Ihrem Azure Active Directory-Mandanten.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: d2889af6000e77fba7a91392c0adb227588b5306
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66430800"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Zuordnen oder Hinzufügen eines Azure-Abonnements zu Ihrem Azure Active Directory-Mandanten

Azure-Abonnements haben eine Vertrauensstellung mit Azure Active Directory (Azure AD). Das bedeutet, sie vertrauen Azure AD beim Authentifizieren von Benutzern, Diensten und Geräten. Mehrere Abonnements können demselben Azure AD-Verzeichnis vertrauen, aber ein Abonnement vertraut immer nur einem einzigen Verzeichnis.

Wenn Ihr Abonnement abläuft, verlieren Sie auch den Zugriff auf alle Ressourcen, die dem Abonnement zugwiesen sind. Das Azure AD-Verzeichnis bleibt jedoch in Azure, sodass Sie das Verzeichnis mit einem anderen Azure-Abonnement zuweisen und verwalten können.

Alle Ihre Benutzer verfügen über ein einzelnes *Basisverzeichnis* für die Authentifizierung. Ihre Benutzer können aber auch Gäste in anderen Verzeichnissen sein. Ihnen werden sowohl das Basis- als auch das Gastverzeichnis für jeden Benutzer in Azure AD angezeigt.

> [!Important]
> Wenn Sie ein Abonnement einem anderen Verzeichnis zuordnen, verlieren Benutzer, denen Rollen mit der [rollenbasierten Zugriffssteuerung (RBAC)](../../role-based-access-control/role-assignments-portal.md) zugewiesen wurden, ihren Zugriff. Klassische Abonnementadministratoren (Dienstadministrator und Co-Admin) verlieren ebenfalls ihren Zugriff.
> 
> Darüber hinaus führt das Verschieben Ihres Azure Kubernetes Service-Clusters (AKS-Clusters) in ein anderes Abonnement oder das Verschieben des Abonnements, das den Cluster besitzt, in einen neuen Mandanten dazu, dass der Cluster seine Funktionalität aufgrund des Verlusts von Rollenzuweisungen und Dienstprinzipalrechten verliert. Weitere Informationen zu AKS finden Sie unter [Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/).

## <a name="before-you-begin"></a>Voraussetzungen

Bevor Sie Ihr Abonnement zuweisen oder hinzufügen können, müssen Sie die folgenden Aufgaben ausführen:

1. Überprüfen Sie die folgende Liste von Änderungen und deren möglichen Folgen für Sie:

    - Benutzer, deren Rollen mithilfe von RBAC zugewiesen wurden, verlieren den Zugriff.
    - Dienstadministrator und Co-Admins verlieren den Zugriff.
    - Wenn Sie über Schlüsseltresore verfügen, kann darauf nicht mehr zugegriffen werden, und Sie müssen sie nach der Zuweisung korrigieren.
    - Wenn Sie über verwaltete Identitäten für Ressourcen wie Virtual Machines oder Logic Apps verfügen, müssen Sie diese nach der Zuordnung erneut aktivieren oder erstellen.
    - Wenn Sie eine Azure Stack-Instanz konfiguriert haben, müssen Sie diese nach der Zuweisung erneut registrieren.

1. Melden Sie sich mit einem Konto an, das:
    - Über die Rollenzuweisung [Besitzer](../../role-based-access-control/built-in-roles.md#owner) für das Abonnement verfügt. Weitere Informationen zum Zuweisen der Rolle „Besitzer“ finden Sie unter [Verwalten des Zugriffs auf Azure-Ressourcen mit RBAC und dem Azure-Portal](../../role-based-access-control/role-assignments-portal.md).
    - Sowohl im aktuellen Verzeichnis, das dem Abonnement zugewiesen ist, als auch im neuen Verzeichnis, dem Sie das Abonnement in Zukunft zuweisen möchten, existiert. Weitere Informationen zum Zugreifen auf ein anderes Verzeichnis finden Sie unter [Wie fügen Azure Active Directory-Administratoren Benutzer für die B2B-Zusammenarbeit hinzu?](../b2b/add-users-administrator.md).

1. Achten Sie darauf, dass Sie kein Azure-Clouddienstanbieter-Abonnement (MS-AZR-0145P, MS-AZR-0146P, MS-AZR-0146P, MS-AZR-159P), kein internes Microsoft-Abonnement (MS-AZR-0015P) und kein Microsoft Imagine-Abonnement (MS-AZR-0144P) verwenden.
    
## <a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>So weisen Sie Ihrem Azure AD-Verzeichnis ein vorhandenes Abonnement zu

1. Melden Sie sich an, und wählen Sie im [Azure-Portal auf der Seite „Abonnements“](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) ein Abonnement aus.

2. Wählen Sie **Verzeichnis ändern** aus.

    ![Seite „Abonnements“ mit hervorgehobener Option „Verzeichnis ändern“](media/active-directory-how-subscriptions-associated-directory/change-directory-button.png)

3. Überprüfen Sie alle angezeigten Warnungen, und wählen Sie dann **Ändern** aus.

    ![Ändern der Verzeichnisseite und Anzeigen des zu ändernden Verzeichnisses](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    Das Verzeichnis wird für das Abonnement geändert, und eine Erfolgsmeldung wird angezeigt.

    ![Nachricht über die erfolgreiche Änderung des Verzeichnisses](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)
4. Rufen Sie mithilfe des **Verzeichnisumschalters** Ihr neues Verzeichnis auf. Es kann mehrere Stunden dauern, bis alles ordnungsgemäß angezeigt wird. Wenn es anscheinend zu lange dauert, stellen Sie sicher, dass der **Globale Abonnementfilter** für das verschobene Abonnement nicht einfach nur ausgeblendet ist.

    ![Seite mit dem Verzeichnisumschalter mit Beispielinformationen](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

Das Ändern des Abonnementverzeichnisses erfolgt auf Dienstebene, sodass sich der Vorgang nicht auf die Abonnementabrechnung auswirkt. Der Kontoadministrator kann den Dienstadministrator weiterhin über das [Kontocenter](https://account.azure.com/subscriptions) ändern. Um das ursprüngliche Verzeichnis zu löschen, müssen Sie die Abonnementabrechnung einem neuen Kontoadministrator übertragen. Weitere Informationen zum Übertragen der Abrechnung finden Sie unter [Übertragen des Besitzes eines Azure-Abonnements auf ein anderes Konto](../../billing/billing-subscription-transfer.md).

## <a name="post-association-steps"></a>Schritte nach der Zuordnung
Nachdem Sie ein Abonnement einem anderen Verzeichnis zugeordnet haben, sind eventuell zusätzliche Schritte erforderlich, die Sie ausführen müssen, damit alle Vorgänge fortgesetzt werden können.

1. Wenn Sie über Schlüsseltresore verfügen, müssen Sie die Mandanten-ID des Schlüsseltresors ändern. Weitere Informationen finden Sie unter [Ändern der Mandanten-ID des Schlüsseltresors nach einer Abonnementverschiebung](../../key-vault/key-vault-subscription-move-fix.md).

2. Wenn Sie vom System zugewiesene verwaltete Identitäten für Ressourcen verwendet haben, müssen Sie diese erneut aktivieren. Wenn Sie vom Benutzer zugewiesene verwaltete Identitäten verwendet haben, müssen Sie diese erneut erstellen. Nach dem erneuten Aktivieren oder Erstellen der verwalteten Identitäten müssen Sie die Berechtigungen, die diesen Identitäten zugewiesen sind, erneut einrichten. Weitere Informationen finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../managed-identities-azure-resources/overview.md).

3. Wenn Sie mit diesem Abonnement eine Azure Stack-Instanz registriert haben, müssen Sie diese erneut registrieren. Weitere Informationen finden Sie unter [Registrieren von Azure Stack in Azure](/azure-stack/operator/azure-stack-registration).



## <a name="next-steps"></a>Nächste Schritte

- Wie Sie einen neuen Mandanten erstellen, erfahren Sie unter [Zugreifen auf Azure Active Directory zum Erstellen eines neuen Mandanten](active-directory-access-create-new-tenant.md).

- Informationen dazu, wie der Zugriff auf Ressourcen in Microsoft Azure gesteuert wird, finden Sie unter [Grundlegendes zum Zugriff auf Ressourcen in Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)

- Weitere Informationen zum Zuweisen von Rollen in Azure AD finden Sie unter [Zuweisen von Verzeichnisrollen zu Benutzern in Azure Active Directory](active-directory-users-assign-role-azure-portal.md).
