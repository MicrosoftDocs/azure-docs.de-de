---
title: Informationen dazu, wie Benutzer Apps in Azure Active Directory zugewiesen werden
description: Hier erfahren Sie, wie Benutzer einer App zugewiesen werden, die Azure Active Directory für die Identitätsverwaltung verwendet.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: kenwith
ms.openlocfilehash: b9786f9d9da363f15bd2f59390d5dddf86bc1bf9
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658849"
---
# <a name="understand-how-users-are-assigned-to-apps-in-azure-active-directory"></a>Informationen dazu, wie Benutzer Apps in Azure Active Directory zugewiesen werden
In diesem Artikel erhalten Sie Informationen zum Zuweisen von Benutzern zu einer Anwendung in Ihrem Mandanten.

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>Wie werden Benutzer einer Anwendung in Azure AD zugewiesen?
Damit Benutzer Zugriff auf eine Anwendung haben, müssen sie dieser zunächst zugewiesen werden. Die Zuweisung kann durch einen Administrator, einen Vertreter des Unternehmens oder manchmal auch durch den Benutzer selbst erfolgen. Nachfolgend sind die verschiedenen Möglichkeiten aufgeführt, wie Benutzer Anwendungen zugewiesen werden können:

*  Ein Administrator [weist einen Benutzer](./assign-user-or-group-access-portal.md) direkt der Anwendung zu.
*  Ein Administrator [weist eine Gruppe](./assign-user-or-group-access-portal.md), der der Benutzer als Mitglied angehört, der Anwendung zu. Dies kann die folgenden Gruppen umfassen:
    * Eine Gruppe, die lokal synchronisiert wurde
    * Eine in der Cloud erstellte statische Sicherheitsgruppe
    * Eine in der Cloud erstellte [dynamische Sicherheitsgruppe](../enterprise-users/groups-dynamic-membership.md)
    * Eine in der Cloud erstellte Microsoft 365-Gruppe
    * Die Gruppe [Alle Benutzer](../fundamentals/active-directory-groups-create-azure-portal.md)
*  Ein Administrator aktiviert den [Self-Service-Anwendungszugriff](./manage-self-service-access.md), damit ein Benutzer eine Anwendung mithilfe der Funktion **App hinzufügen** in [Meine Apps](../user-help/my-apps-portal-end-user-access.md) **ohne Genehmigung des Unternehmens** hinzufügen kann.
*  Ein Administrator aktiviert den [Self-Service-Anwendungszugriff](./manage-self-service-access.md), damit ein Benutzer eine Anwendung mithilfe der Funktion **App hinzufügen** in [Meine Apps](../user-help/my-apps-portal-end-user-access.md) hinzufügen kann, jedoch nur **mit vorheriger Genehmigung durch festgelegte genehmigende Personen des Unternehmens**.
*  Ein Administrator aktiviert die [Self-Service-Gruppenverwaltung](../enterprise-users/groups-self-service-management.md), damit ein Benutzer einer Gruppe, der eine Anwendung zugewiesen ist, **ohne Genehmigung des Unternehmens** beitreten kann.
*  Ein Administrator aktiviert die [Self-Service-Gruppenverwaltung](../enterprise-users/groups-self-service-management.md), damit ein Benutzer einer Gruppe, der eine Anwendung zugewiesen ist, beitreten kann, jedoch nur **mit vorheriger Genehmigung durch festgelegte genehmigende Personen des Unternehmens**.
*  Ein Administrator weist einem Benutzer direkt eine Lizenz für eine Erstanbieteranwendung zu, z. B. für [Microsoft 365](https://products.office.com/).
*  Ein Administrator weist einer Gruppe, der der Benutzer als Mitglied angehört, eine Lizenz für eine Erstanbieteranwendung zu, z. B. für [Microsoft 365](https://products.office.com/).
*  Ein [Administrator gibt seine Zustimmung für eine Anwendung](../develop/howto-convert-app-to-be-multi-tenant.md), sodass diese von allen Benutzern verwendet werden kann. Ein Benutzer meldet sich dann bei der Anwendung an.
* Ein Benutzer [gibt selbst seine Zustimmung zu einer Anwendung](../develop/howto-convert-app-to-be-multi-tenant.md), indem er sich bei der Anwendung anmeldet.

## <a name="next-steps"></a>Nächste Schritte
* [Schnellstartserie zur Anwendungsverwaltung](view-applications-portal.md)
* [Worum handelt es sich bei der Anwendungsverwaltung?](what-is-application-management.md)
* [Worum handelt es sich beim einmaligen Anmelden?](what-is-single-sign-on.md)