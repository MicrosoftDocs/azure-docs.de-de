---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 08/27/2020
ms.author: msmbaldwin
ms.openlocfilehash: 8c8c9563c954e3d1a84ea2b9f411187d5fb9f226
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96028118"
---
Key Vault unterstützt bis zu 1.024 Zugriffsrichtlinieneinträge, wobei jeder Eintrag einen eindeutigen Satz von Berechtigungen für einen bestimmten Sicherheitsprinzipal erteilt. Aufgrund dieser Einschränkung empfiehlt es sich, Zugriffsrichtlinien möglichst Gruppen von Benutzern anstelle von einzelnen Benutzern zuzuweisen. Das Verwenden von Gruppen vereinfacht die Verwaltung von Berechtigungen für mehrere Personen in Ihrer Organisation deutlich. Weitere Informationen finden Sie unter [Verwalten des Zugriffs auf Apps und Ressourcen mithilfe von Azure Active Directory-Gruppen](../articles/active-directory/fundamentals/active-directory-manage-groups.md).

Ausführliche Informationen zur Key Vault-Zugriffssteuerung finden Sie unter [Azure Key Vault – Sicherheit: Identitäts- und Zugriffsverwaltung](../articles/key-vault/general/overview-security.md#identity-and-access-management).