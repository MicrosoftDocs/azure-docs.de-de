---
author: baanders
description: Includedatei für den Schritt „Zugriffsberechtigungen“ bei der Einrichtung von Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: a905bb3b4effb0381facfbfaa37c8ea412b81287
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552206"
---
In Azure Digital Twins wird [Azure Active Directory (Azure AD)](../articles/active-directory/fundamentals/active-directory-whatis.md) für die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) verwendet. Dies bedeutet, dass einem Benutzer eine Rolle mit entsprechenden Berechtigungen zugewiesen werden muss, bevor er Aufrufe auf Datenebene an Ihre Azure Digital Twins-Instanz richten kann.

Bei Azure Digital Twins ist dies die Rolle _**Azure Digital Twins Data Owner**_ (Azure Digital Twins-Datenbesitzer). Weitere Informationen zu Rollen und Sicherheit finden Sie unter [ *-Konzepte: Sicherheit für Azure Digital Twins-Lösungen*](../articles/digital-twins/concepts-security.md).

In diesem Abschnitt erfahren Sie, wie Sie eine Rollenzuweisung für einen Benutzer in Ihrer Azure Digital Twins-Instanz erstellen, indem Sie die E-Mail-Adresse dieses Benutzers im Azure AD-Mandanten in Ihrem Azure-Abonnement verwenden. Je nach Ihrer Rolle in Ihrer Organisation können Sie diese Berechtigung für sich selbst oder für eine andere Person einrichten, die die Azure Digital Twins-Instanz verwaltet.

### <a name="assign-the-role"></a>Zuweisen der Rolle

Um einem Benutzer Berechtigungen zum Verwalten einer Azure Digital Twins-Instanz zu gewähren, müssen Sie ihm die Rolle _**Azure Digital Twins Data Owner**_ (Azure Digital Twins-Datenbesitzer) innerhalb der Instanz zuweisen.

> [!NOTE]
> Beachten Sie, dass sich diese Rolle von der Azure AD-Rolle *Besitzer* unterscheidet, die ebenfalls im Bereich der Azure Digital Twins-Instanz zugewiesen werden kann. Es handelt sich hierbei um zwei unterschiedliche Verwaltungsrollen. Die Azure AD-Rolle *Besitzer* gewährt keinen Zugriff auf Features der Datenebene, während dieser Zugriff mit *Azure Digital Twins Data Owner* (Azure Digital Twins-Datenbesitzer) gewährt wird.