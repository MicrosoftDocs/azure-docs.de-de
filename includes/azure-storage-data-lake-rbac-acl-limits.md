---
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/29/2020
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 97b47b48a183951002d04cd58b08d5e2b9051eda
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017673"
---
| Mechanismus | `Scope` |Einschränkungen | Unterstützte Berechtigungsebene |
|---|---|---|---|
| Azure RBAC | Speicherkonten, Container. <br>Ressourcenübergreifende Azure-Rollenzuweisungen auf Abonnement- oder Ressourcengruppenebene. | 2\.000 Azure-Rollenzuweisungen in einem Abonnement | Azure-Rollen (integriert oder benutzerdefiniert) |
| ACL| Verzeichnis, Datei |32 ACL-Einträge (im Grunde 28 ACL-Einträge) pro Datei und pro Verzeichnis. Für Zugriffs- und Standard-ACLs gilt jeweils ein eigener Zugriffsgrenzwert von 32 ACLs. |ACL-Berechtigung|
