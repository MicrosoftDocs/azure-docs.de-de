---
title: include file
description: include file
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: c8453a2ec00a2fca107f85a23a8af1e6313a70b6
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178252"
---
### <a name="sql-servers"></a>SQL-Server

|  |  |
|---------|---------|
| [Überwachen bei nicht vorhandenem Azure Active Directory-Administrator](../articles/governance/policy/samples/audit-no-aad-admin.md) | Lassen Sie eine Überwachung zu, wenn dem SQL-Server kein Azure Active Directory-Administrator zugewiesen ist. |
| [Überwachen von Einstellungen für die Bedrohungserkennung auf Serverebene](../articles/governance/policy/samples/audit-sql-server-threat-detection-setting.md) | Überwacht, ob die Richtlinien der Sicherheitswarnungen für SQL-Datenbanken nicht auf den angegebenen Status festgelegt sind. Sie geben einen Wert an, der angibt, ob die Bedrohungserkennung aktiviert oder deaktiviert ist.  |
| [Überwachen der SQL Server-Überwachungseinstellungen](../articles/governance/policy/samples/sql-server-audit.md) | Überwacht SQL Server abhängig davon, ob die Überwachungseinstellungen aktiviert sind. |
| [Überwachen von Einstellungen der Überwachung auf SQL-Server-Ebene](../articles/governance/policy/samples/audit-sql-server-audit-setting.md) | Überwacht, ob die SQL-Server-Überwachungseinstellungen nicht mit einer angegebenen Einstellung übereinstimmen. Sie geben einen Wert an, der angibt, ob Überwachungseinstellungen aktiviert oder deaktiviert sein sollen. |
| [Verlangen der SQL Server-Version 12.0](../articles/governance/policy/samples/require-sql-12.md) | Erfordert die Verwendung der SQL Server-Version 12.0.  |