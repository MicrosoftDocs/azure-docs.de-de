---
title: Bewährte Methoden für Azure RBAC
description: Bewährte Methoden für die Verwendung der rollenbasierten Zugriffssteuerung von Azure (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: conceptual
ms.workload: identity
ms.date: 12/16/2020
ms.author: rolyon
ms.openlocfilehash: 1444ee3643222ceebc67298bdb580955b1432104
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526414"
---
# <a name="best-practices-for-azure-rbac"></a>Bewährte Methoden für Azure RBAC

In diesem Artikel werden einige bewährte Methoden für die Verwendung der rollenbasierten Zugriffssteuerung in Azure (Azure RBAC) beschrieben. Diese bewährten Methoden leiten sich aus unseren Erfahrungen mit Azure RBAC und den Erfahrungen von Kunden wie Ihnen ab.

## <a name="only-grant-the-access-users-need"></a>Nur den für Benutzer erforderlichen Zugriff gewähren

Mithilfe von Azure RBAC können Sie Aufgaben in Ihrem Team verteilen und Benutzern nur den Zugriff gewähren, den sie zur Ausführung ihrer Aufgaben benötigen. Anstatt allen uneingeschränkte Berechtigungen in Ihrem Azure-Abonnement oder Ihren Ressourcen zu gewähren, können Sie die Berechtigungen auf bestimmte Aktionen in einem bestimmten Bereich beschränken.

Bei der Planung Ihrer Strategie für die Zugriffssteuerung hat es sich bewährt, Benutzern die geringsten Rechte zum Ausführen ihrer Aufgaben zu erteilen. Vermeiden Sie es, umfangreichere Rollen in umfassenderen Bereichen zuzuweisen, auch wenn dies anfänglich bequemer erscheint. Wenn Sie benutzerdefinierte Rollen erstellen, schließen Sie nur die Berechtigungen ein, die Benutzer benötigen. Durch das Einschränken von Rollen und Bereichen begrenzen Sie die Ressourcen, die gefährdet sind, wenn der Sicherheitsprinzipal kompromittiert wird.

Das folgende Diagramm zeigt ein vorgeschlagenes Muster für die Verwendung von Azure RBAC.

![Azure RBAC und der Ansatz der geringsten Rechte](./media/best-practices/rbac-least-privilege.png)

Weitere Informationen zum Hinzufügen von Rollenzuweisungen finden Sie unter [Hinzufügen oder Entfernen von Rollenzuweisungen mithilfe von Azure RBAC und dem Azure-Portal](role-assignments-portal.md).

## <a name="limit-the-number-of-subscription-owners"></a>Begrenzen der Anzahl von Abonnementbesitzern

Sie sollten höchstens 3 Abonnementbesitzer haben, um die Möglichkeit einer Sicherheitsverletzung durch einen kompromittierten Besitzer zu verringern. Diese Empfehlung lässt sich im Azure Security Center überwachen. Weitere Identitäts- und Zugriffsempfehlungen im Security Center finden Sie unter [Sicherheitsempfehlungen – ein Referenzhandbuch](../security-center/recommendations-reference.md).

## <a name="use-azure-ad-privileged-identity-management"></a>Verwenden von Azure AD Privileged Identity Management

Um privilegierte Konten vor bösartigen Cyberangriffen zu schützen, können Sie mit Azure Active Directory Privileged Identity Management (PIM) die Dauer der Aktivierung von Berechtigungen verkürzen und sich mittels Berichten und Warnungen einen besseren Überblick über deren Verwendung verschaffen. PIM hilft Ihnen beim Schutz privilegierter Konten, indem es privilegierten Just-In-Time-Zugriff auf Azure AD- und Azure-Ressourcen gewährt. Der Zugriff kann zeitgebunden sein, sodass Berechtigungen nach dessen Ablauf automatisch entzogen werden. 

Weitere Informationen finden Sie unter [Was ist Azure AD Privileged Identity Management?](../active-directory/privileged-identity-management/pim-configure.md).

## <a name="assign-roles-to-groups-not-users"></a>Zuweisen von Rollen zu Gruppen,nicht zu Benutzern

Damit Rollenzuweisungen besser verwaltbar sind, sollten Sie Benutzern keine Rollen direkt zuweisen. Weisen Sie Rollen stattdessen Gruppen zu. Das Zuweisen von Rollen zu Gruppen anstelle von Benutzern trägt auch dazu bei, die Anzahl von Rollenzuweisungen zu minimieren, die auf [2.000 Rollenzuweisungen pro Abonnement](troubleshooting.md#azure-role-assignments-limit) beschränkt sind. 

## <a name="next-steps"></a>Nächste Schritte

- [Behandeln von Problemen bei Azure RBAC](troubleshooting.md)
