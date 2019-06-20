---
title: Überprüfen des Zugriffs auf Azure AD-Rollen in PIM – Azure Active Directory | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie den Zugriff auf Azure AD-Rollen in Azure AD Privileged Identity Management (PIM) überprüfen.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/21/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3850af026ea13e1920aea65e18358ebb04ef0d25
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65140968"
---
# <a name="review-access-to-azure-ad-roles-in-pim"></a>Überprüfen des Zugriffs auf Azure AD-Rollen in PIM

Azure Active Directory (AD) Privileged Identity Management (PIM) vereinfacht die Art und Weise, in der Unternehmen den privilegierten Zugriff auf Ressourcen in Azure AD und anderen Microsoft-Onlinediensten wie Office 365 oder Microsoft Intune verwalten.  

Wenn Ihnen eine Administratorrolle zugewiesen wurde, werden Sie vom Administrator für privilegierte Rollen Ihrer Organisation möglicherweise gebeten, regelmäßig zu bestätigen, dass Sie diese Rolle für Ihre Aufgaben benötigen. Sie erhalten möglicherweise eine E-Mail mit einem Link, oder Sie können direkt zum [Azure-Portal](https://portal.azure.com)wechseln. Führen Sie die Schritte in diesem Artikel aus, um die Ihnen zugewiesenen Rollen selbst zu überprüfen.

Wenn Sie Administrator für privilegierte Rollen oder globaler Administrator sind und sich für Zugriffsüberprüfungen interessieren, erhalten Sie weitere Informationen unter [Starten einer Zugriffsüberprüfung](pim-how-to-start-security-review.md).

## <a name="add-the-privileged-identity-management-application"></a>Hinzufügen der Anwendung Privileged Identity Management
Sie können die Anwendung Azure AD Privileged Identity Management (PIM) im [Azure-Portal](https://portal.azure.com/) verwenden, um die Überprüfung durchzuführen.  Wenn sich die Anwendung Azure AD Privileged Identity Management nicht in Ihrem Portal befindet, führen Sie die folgenden Schritte aus, um zu beginnen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Wählen Sie in der oberen rechten Ecke des Azure-Portals Ihren Benutzernamen, und wählen Sie das Verzeichnis aus, in dem Sie arbeiten möchten.
3. Wählen Sie **Alle Dienste** aus, und verwenden Sie das Textfeld „Filter“, um nach **Azure AD Privileged Identity Management** zu suchen.
4. Aktivieren Sie das Kontrollkästchen **An Dashboard anheften**, und klicken Sie dann auf **Erstellen**. Die Anwendung Privileged Identity Management wird geöffnet.

## <a name="approve-or-deny-access"></a>Genehmigen oder Verweigern des Zugriffs
Wenn Sie Zugriff genehmigen oder verweigern, teilen Sie dem Prüfer lediglich mit, ob Sie diese Rolle weiterhin verwenden. Wählen Sie **Genehmigen** aus, wenn Sie in der Rolle bleiben möchten, oder **Verweigern**, wenn Sie den Zugriff nicht mehr benötigen. Ihr Status ändert sich nicht sofort, sondern erst, wenn der Prüfer die Ergebnisse angewendet hat.
Gehen Sie wie folgt vor, um die Zugriffsüberprüfung zu finden und abzuschließen:

1. Wählen Sie in der PIM-Anwendung **Privilegierten Zugriff überprüfen**aus. Wenn Zugriffsüberprüfungen ausstehen, werden sie auf dem Blatt mit den Azure AD-Zugriffsüberprüfungen angezeigt.
2. Wählen Sie die Überprüfung aus, die Sie abschließen möchten.
3. Wenn Sie die Überprüfung nicht erstellt haben, werden Sie als der einzige Benutzer in der Überprüfung angezeigt. Wählen Sie das Kontrollkästchen neben Ihrem Namen.
4. Wählen Sie entweder **Genehmigen** oder **Verweigern** aus. Möglicherweise müssen Sie einen Grund für Ihre Entscheidung im Textfeld **Grund angeben** eingeben.  
5. Schließen Sie das Blatt **Azure AD-Rollen überprüfen** .

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Nächste Schritte

- [Ausführen einer Zugriffsüberprüfung für Azure-Ressourcenrollen in PIM](pim-resource-roles-perform-access-review.md)
