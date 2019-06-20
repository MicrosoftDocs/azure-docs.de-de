---
title: Abschließen einer Zugriffsüberprüfung von Azure-Ressourcenrollen in PIM – Azure Active Directory | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine Zugriffsüberprüfung von Azure-Ressourcenrollen in Azure AD Privileged Identity Management (PIM) abschließen.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: aee8ac3c2638ede559f8a1f9c51f2d6e62604998
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65602300"
---
# <a name="complete-an-access-review-of-azure-resource-roles-in-pim"></a>Abschließen einer Zugriffsüberprüfung von Azure-Ressourcenrollen in PIM
Nachdem eine [Zugriffsüberprüfung gestartet wurde](pim-resource-roles-start-access-review.md), können Administratoren für privilegierte Rollen den privilegierten Zugriff überprüfen. Azure Active Directory (Azure AD) Privileged Identity Management (PIM) sendet automatisch eine E-Mail, in der die Benutzer aufgefordert werden, ihren Zugriff zu überprüfen. Wenn ein Benutzer diese E-Mail nicht erhalten hat, können Sie ihm die Anweisungen unter [Ausführen einer Zugriffsüberprüfung](pim-resource-roles-perform-access-review.md) zusenden.

Wenn der Zeitraum für die Zugriffsüberprüfung abgelaufen ist oder alle Benutzer die Selbstüberprüfung abgeschlossen haben, führen Sie die Schritte in diesem Artikel aus, um die Überprüfung zu verwalten und die Ergebnisse anzuzeigen.

## <a name="manage-access-reviews"></a>Verwalten von Zugriffsüberprüfungen
1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/). Wählen Sie dann im Dashboard die Anwendung **Azure-Ressourcen** aus.

2. Wählen Sie Ihre Ressource aus.

3. Wählen Sie auf dem Dashboard den Abschnitt **Zugriffsüberprüfungen** aus.
![Zugriffsüberprüfungen](media/pim-resource-roles-complete-access-review/rbac-access-review-home-list.png)

4. Wählen Sie die Zugriffsüberprüfung aus, die Sie verwalten möchten.

Auf dem Detailblatt der Zugriffsüberprüfung stehen eine Reihe von Optionen zum Verwalten dieser Überprüfung zur Verfügung. Die folgenden Optionen sind verfügbar:

![Optionen zum Verwalten einer Überprüfung](media/pim-resource-roles-complete-access-review/rbac-access-review-menu.png)

### <a name="stop"></a>Beenden
Alle Zugriffsüberprüfungen weisen ein Enddatum auf, Sie können jedoch die Schaltfläche **Beenden** verwenden, um sie zu einem frühen Zeitpunkt abzuschließen. Alle Benutzer, die ihre Überprüfung bis zu diesem Zeitpunkt nicht abgeschlossen haben, können sie nicht mehr abschließen, nachdem Sie die Überprüfung beendet haben. Eine Überprüfung kann nicht neu gestartet werden, nachdem sie beendet wurde.

### <a name="reset"></a>Reset
Sie können eine Zugriffsüberprüfung zurücksetzen, um alle dafür getroffenen Entscheidungen zu entfernen. Nachdem Sie eine Zugriffsüberprüfung zurückgesetzt haben, werden alle Benutzer als „Nicht überprüft“ gekennzeichnet. 

### <a name="apply"></a>Anwenden
Nachdem eine Zugriffsüberprüfung abgeschlossen wurde, verwenden Sie die Schaltfläche **Übernehmen**, um das Ergebnis der Überprüfung zu implementieren. Wenn bei der Überprüfung der Zugriff eines Benutzers verweigert wurde, wird mit diesem Schritt seine Rollenzuweisung entfernt.  

### <a name="delete"></a>Löschen
Wenn Sie an der Überprüfung nicht weiter interessiert sind, löschen Sie sie. Über die Schaltfläche **Löschen** entfernen Sie die Überprüfung aus der PIM-Anwendung.

## <a name="results"></a>Ergebnisse
Auf der Registerkarte **Ergebnisse** können Sie eine Liste mit Ihren Ergebnissen der Überprüfung anzeigen und herunterladen. 
![Registerkarte „Ergebnisse“](media/pim-resource-roles-complete-access-review/rbac-access-review-results.png)

## <a name="reviewers"></a>Prüfer
Zeigen Sie Prüfer für Ihre vorhandene Zugriffsüberprüfung an, und fügen Sie sie hinzu. Erinnern Sie Prüfer daran, ihre Überprüfungen durchzuführen.
![Prüfer hinzufügen](media/pim-resource-roles-complete-access-review/rbac-access-review-reviewers.png)

## <a name="next-steps"></a>Nächste Schritte

- [Starten einer Zugriffsüberprüfung für Azure-Ressourcenrollen in PIM](pim-resource-roles-start-access-review.md)
- [Ausführen einer Zugriffsüberprüfung für Azure-Ressourcenrollen in PIM](pim-resource-roles-perform-access-review.md)
