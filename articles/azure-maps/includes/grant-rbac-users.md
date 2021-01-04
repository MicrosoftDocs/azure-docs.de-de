---
title: Gewähren von rollenbasiertem Zugriff für Benutzer
titleSuffix: Azure Maps
description: Verwenden der rollenbasierten Zugriffssteuerung, um Benutzern Autorisierung für Azure Maps zu gewähren
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/17/2020
ms.topic: include
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 19806fe24d0ff3b87ebe61b45ac302947c734fa0
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895567"
---
## <a name="grant-role-based-access-for-users-to-azure-maps"></a>Gewähren von rollenbasierter Zugriffssteuerung für Benutzer in Azure Maps

Sie gewähren die *rollenbasierte Zugriffssteuerung in Azure (Azure RBAC)* , indem Sie mindestens einer Azure Maps-Rollendefinition eine Azure AD-Gruppe oder Sicherheitsprinzipale zuweisen. Informationen über die für Azure Maps verfügbaren Rollendefinitionen für die rollenbasierte Zugriffssteuerung von Azure finden Sie unter **Zugriffssteuerung (IAM)** . Wählen Sie **Rollen** aus, und suchen Sie dann nach Rollen, die mit *Azure Maps* beginnen.

* Informationen zur effizienten Verwaltung des Zugriffs einer großen Menge von Benutzern auf Azure Maps finden Sie unter [Azure AD-Gruppen](../../active-directory/fundamentals/active-directory-manage-groups.md).
* Damit Benutzer sich bei der Anwendung authentifizieren können, müssen die Benutzer in Azure AD erstellt werden. Siehe [Hinzufügen oder Löschen von Benutzern mithilfe von Azure AD](../../active-directory/fundamentals/add-users-azure-active-directory.md).

Weitere Informationen zum effektiven Verwalten eines Verzeichnisses für Benutzer finden Sie unter [Azure AD](../../active-directory/fundamentals/index.yml).

1. Navigieren Sie zu Ihrem **Azure Maps-Konto** . Wählen Sie **Zugriffssteuerung (IAM)**  > **Rollenzuweisungen** aus.

    ![Gewähren von Zugriff mithilfe von Azure RBAC](../media/how-to-manage-authentication/how-to-grant-rbac.png)

2. Wählen Sie auf der Registerkarte **Rollenzuweisungen** unter **Rolle** eine integrierte Azure Maps-Rollendefinition aus, z. B. **Azure Maps-Datenleser** oder **Azure Maps-Datenmitwirkender** . Wählen Sie unter **Zugriff zuweisen zu** die Option **Azure AD-Benutzer, -Gruppe oder -Dienstprinzipal** aus. Wählen Sie den Prinzipal nach Namen aus. Klicken Sie dann auf **Speichern** .

   * Lesen Sie die Details zum [Hinzufügen oder Entfernen von Rollenzuweisungen](../../role-based-access-control/role-assignments-portal.md).

> [!WARNING]
> Integrierte Azure Maps-Rollendefinitionen bieten einen sehr umfangreichen Autorisierungszugriff auf viele Azure Maps-REST-APIs. Informationen, wie Sie APIs für Benutzer auf ein Mindestmaß beschränken, finden Sie unter [Erstellen einer benutzerdefinierten Rollendefinition und Zuweisen von Benutzern](../../role-based-access-control/custom-roles.md) zur benutzerdefinierten Rollendefinition. Dadurch werden Benutzer in die Lage versetzt, die geringsten Berechtigungen zu erhalten, die für die Anwendung erforderlich sind.