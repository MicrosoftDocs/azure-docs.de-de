---
title: Verwenden eines Ressourcendashboards für die Zugriffsüberprüfung in PIM – Azure Active Directory | Microsoft-Dokumentation
description: Dieser Artikel beschreibt, wie Sie ein Ressourcendashboard verwenden, um eine Zugriffsüberprüfung in Azure AD Privileged Identity Management (PIM) durchzuführen.
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
ms.openlocfilehash: e5404d1b85821458aedef64b72ae635ea49aa1ff
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65602453"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review-in-pim"></a>Verwenden eines Ressourcendashboards für die Zugriffsüberprüfung in PIM

Sie können ein Ressourcendashboard verwenden, um eine Zugriffsüberprüfung in Azure Active Directory (Azure AD) Privileged Identity Management (PIM) durchzuführen. Das Dashboard mit der Administratoransicht besteht aus drei Hauptkomponenten:

- eine grafische Darstellung der Ressourcenrollenaktivierungen
- zwei Diagramme, die die Verteilung von Rollenzuweisungen nach Zuweisungstyp darstellen
- ein Datenbereich, der sich auf neue Rollenzuweisungen bezieht

![Screenshot der Dashboardadministratoransicht mit Diagrammen](media/pim-resource-roles-overview-dashboards/rbac-overview-top.png)

![Screenshot der Dashboardadministratoransicht mit Datenlisten](media/pim-resource-roles-overview-dashboards/role-settings.png)

Die grafische Darstellung der Ressourcenrollenaktivierungen deckt die letzten sieben Tage ab. Diese Daten beziehen sich auf die ausgewählte Ressource und geben Aufschluss über Aktivierungen für die gängigsten Rollen (Besitzer, Mitwirkender, Benutzerzugriffsadministrator) sowie für alle Rollen zusammen.

Rechts neben dem Aktivierungsdiagramm befinden sich zwei Diagramme mit der Verteilung von Rollenzuweisungen nach Zuweisungstyp (für Benutzer und Gruppen). Wenn Sie auf ein Segment des Diagramms klicken, ändert sich der Wert in einen Prozentsatz (oder umgekehrt).

Unterhalb der Diagramme werden die Anzahl von Benutzern und Gruppen mit neuen Rollenzuweisungen in den letzten 30 Tagen sowie eine (in absteigender Reihenfolge) nach Gesamtzuweisungen sortierte Liste mit Rollen angezeigt.

## <a name="next-steps"></a>Nächste Schritte

- [Starten einer Zugriffsüberprüfung für Azure-Ressourcenrollen in PIM](pim-resource-roles-start-access-review.md) 
