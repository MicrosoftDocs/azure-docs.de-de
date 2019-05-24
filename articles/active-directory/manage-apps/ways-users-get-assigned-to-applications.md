---
title: Zuweisen von Benutzern zu Anwendungen | Microsoft-Dokumentation
description: Informationen zum Zuweisen von Benutzern zu einer Anwendung in Ihrem Mandanten
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: b818fe1d8b6bbc9d2d8c5b460b4d71dccdd39366
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65825988"
---
# <a name="how-to-assign-users-to-applications"></a>Zuweisen von Benutzern zu Anwendungen

In diesem Artikel erhalten Sie Informationen zum Zuweisen von Benutzern zu einer Anwendung in Ihrem Mandanten.

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>Wie werden Benutzer einer Anwendung in Azure AD zugewiesen?

Damit Benutzer Zugriff auf eine Anwendung haben, müssen sie dieser zunächst zugewiesen werden. Die Zuweisung kann durch einen Administrator, einen Vertreter des Unternehmens oder manchmal auch durch den Benutzer selbst erfolgen. Nachfolgend sind die verschiedenen Möglichkeiten aufgeführt, wie Benutzer Anwendungen zugewiesen werden können:

1.  Ein Administrator [weist einen Benutzer](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) direkt der Anwendung zu.

2.  Ein Administrator [weist eine Gruppe](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal), der der Benutzer als Mitglied angehört, der Anwendung zu. Dies kann die folgenden Gruppen umfassen:

    * Eine Gruppe, die lokal synchronisiert wurde

    * Eine in der Cloud erstellte statische Sicherheitsgruppe

    * Eine in der Cloud erstellte [dynamische Sicherheitsgruppe](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal)

    * Eine in der Cloud erstellte Office 365-Gruppe

    * Die Gruppe [Alle Benutzer](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-dedicated-groups)

3.  Ein Administrator aktiviert den [Self-Service-Anwendungszugriff](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access), damit ein Benutzer eine Anwendung mithilfe der Funktion **App hinzufügen** des [Anwendungszugriffsbereichs](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **ohne Genehmigung des Unternehmens** hinzufügen kann.

4.  Ein Administrator aktiviert den [Self-Service-Anwendungszugriff](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access), damit ein Benutzer eine Anwendung mithilfe der Funktion **App hinzufügen** des [Anwendungszugriffsbereichs](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) hinzufügen kann, jedoch nur **mit vorheriger Genehmigung durch festgelegte genehmigende Personen des Unternehmens**.

5.  Ein Administrator aktiviert die [Self-Service-Gruppenverwaltung](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management), damit ein Benutzer einer Gruppe, der eine Anwendung zugewiesen ist, **ohne Genehmigung des Unternehmens** beitreten kann.

6.  Ein Administrator aktiviert die [Self-Service-Gruppenverwaltung](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management), damit ein Benutzer einer Gruppe, der eine Anwendung zugewiesen ist, beitreten kann, jedoch nur **mit vorheriger Genehmigung durch festgelegte genehmigende Personen des Unternehmens**.

7.  Ein Administrator weist einem Benutzer direkt eine Lizenz für eine Erstanbieteranwendung zu, z.B. für [Microsoft Office 365](https://products.office.com/).

8.  Ein Administrator weist einer Gruppe, der der Benutzer als Mitglied angehört, eine Lizenz für eine Erstanbieteranwendung zu, z.B. für [Microsoft Office 365](https://products.office.com/).

9.  Ein [Administrator gibt seine Zustimmung für eine Anwendung](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview), sodass diese von allen Benutzern verwendet werden kann. Ein Benutzer meldet sich dann bei der Anwendung an.

10. Ein Benutzer [gibt selbst seine Zustimmung zu einer Anwendung](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview), indem er sich bei der Anwendung anmeldet.

## <a name="next-steps"></a>Nächste Schritte
[Verwalten von Anwendungen mit Azure Active Directory](what-is-application-management.md)
