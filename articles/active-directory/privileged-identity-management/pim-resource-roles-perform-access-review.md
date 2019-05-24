---
title: Überprüfen des Zugriffs auf Azure-Ressourcenrollen in PIM – Azure Active Directory | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie den Zugriff auf Azure-Ressourcenrollen in Azure AD Privileged Identity Management (PIM) überprüfen.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ede412292caf682c3078002d23e1956bd68d1378
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65602208"
---
# <a name="review-access-to-azure-resource-roles-in-pim"></a>Überprüfen des Zugriffs auf Azure-Ressourcen-Rollen in PIM
Azure Active Directory (Azure AD) Privileged Identity Management (PIM) vereinfacht die Art und Weise, in der Unternehmen den privilegierten Zugriff auf Ressourcen in Azure verwalten. 

Wenn Ihnen eine Administratorrolle zugewiesen wurde, werden Sie vom Administrator für privilegierte Rollen Ihrer Organisation möglicherweise gebeten, regelmäßig zu bestätigen, dass Sie diese Rolle für Ihre Aufgaben benötigen. Sie erhalten möglicherweise eine E-Mail mit einem Link, oder Sie können direkt zum [Azure-Portal](https://portal.azure.com)wechseln. Führen Sie die Schritte in diesem Artikel aus, um die Ihnen zugewiesenen Rollen selbst zu überprüfen.

Wenn Sie Administrator für privilegierte Rollen sind und sich für die Zugriffsüberprüfungen interessieren, erhalten Sie weitere Informationen unter [Starten einer Zugriffsüberprüfung](pim-resource-roles-start-access-review.md).

## <a name="add-the-privileged-identity-management-application"></a>Hinzufügen der Anwendung Privileged Identity Management
Sie können die Anwendung Azure Active Directory PIM (Azure AD) im [Azure-Portal](https://portal.azure.com/) verwenden, um die Überprüfung durchzuführen. Wenn die Anwendung in Ihrem Portal nicht vorhanden ist, führen Sie folgende Schritte aus, um zu beginnen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Wählen Sie in der oberen rechten Ecke des Azure-Portals Ihren Benutzernamen, und wählen Sie das Verzeichnis aus, in dem Sie arbeiten möchten.
3. Wählen Sie **Alle Dienste** aus, und verwenden Sie das Textfeld **Filter**, um nach *Azure AD Privileged Identity Management* zu suchen.
4. Aktivieren Sie **An Dashboard anheften**, und wählen Sie dann **Erstellen** aus. Die PIM-Anwendung wird geöffnet.

## <a name="approve-or-deny-access"></a>Genehmigen oder Verweigern des Zugriffs
Wenn Sie Zugriff genehmigen oder verweigern, teilen Sie dem Prüfer lediglich mit, ob Sie diese Rolle weiterhin verwenden. Wählen Sie **Genehmigen** aus, wenn Sie in der Rolle bleiben möchten, oder **Verweigern**, wenn Sie den Zugriff nicht mehr benötigen. Ihr Status ändert sich nur, wenn der Prüfer die Ergebnisse anwendet.

Gehen Sie wie folgt vor, um die Zugriffsüberprüfung zu finden und abzuschließen:
1. Navigieren Sie zur Azure AD PIM-Anwendung.
2. Wählen Sie das Blatt **Zugriff überprüfen** aus.

   ![Screenshot der PIM-Anwendung mit ausgewähltem Blatt „Zugriff überprüfen“](media/pim-resource-roles-perform-access-review/rbac-access-review-complete.png)

3. Wählen Sie die Überprüfung aus, die Sie abschließen möchten. 
4. Wählen Sie entweder **Genehmigen** oder **Verweigern** aus. Möglicherweise müssen Sie im Feld **Grund angeben** einen Grund für Ihre Entscheidung eingeben.

   ![Screenshot der Seite „Details überprüfen“](media/pim-resource-roles-perform-access-review/rbac-access-review-choice.png)

## <a name="next-steps"></a>Nächste Schritte

- [Durchführen einer Zugriffsüberprüfung für Azure AD-Rollen in PIM](pim-how-to-perform-security-review.md)
