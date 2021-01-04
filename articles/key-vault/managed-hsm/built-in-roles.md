---
title: 'Integrierte Rollen der lokalen RBAC für verwaltete HSMs: Azure Key Vault | Microsoft-Dokumentation'
description: Übersicht über integrierte Rollen für verwaltete HSMs, die Benutzern, Dienstprinzipalen, Gruppen und verwalteten Identitäten zugewiesen werden können
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: a5ecd8f13a3255a565168f62f358a6a38e3cbab4
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445216"
---
# <a name="managed-hsm-local-rbac-built-in-roles"></a>Integrierte Rollen der lokalen RBAC für verwaltete HSMs

Die lokale RBAC für verwaltete HSMs verfügt über mehrere integrierte Rollen, die Sie Benutzern, Dienstprinzipalen, Gruppen und verwalteten Identitäten zuweisen können. Damit ein Prinzipal einen Vorgang ausführen kann, müssen Sie ihm eine Rolle zuweisen, die ihn zur Ausführung der entsprechenden Vorgänge berechtigt. Mit allen diesen Rollen und Vorgängen können nur Berechtigungen für Vorgänge auf der Datenebene verwaltet werden. Wenn Sie Berechtigungen auf der Steuerungsebene für die verwaltete HSM-Ressource verwalten möchten (etwa zum Erstellen eines neuen verwalteten HSM oder zum Aktualisieren, Verschieben oder Löschen eines bereits vorhandenen HSM) müssen Sie die [rollenbasierte Zugriffssteuerung von Azure (Azure Role-Based Access Control, Azure RBAC)](../../role-based-access-control/overview.md) verwenden.

## <a name="built-in-roles"></a>Integrierte Rollen

|Rollenname|BESCHREIBUNG|id|
|---|---|---|
|Managed HSM Administrator (Administrator für verwaltete HSMs)| Gewährt Vollzugriff auf alle Datenaktionen.|a290e904-7015-4bba-90c8-60543313cdb4|
|Managed HSM Crypto Officer (Kryptoverantwortlicher für verwaltete HSMs)| Gewährt Vollzugriff auf alle Schlüsselverwaltungsvorgänge und kryptografischen Schlüsselvorgänge.|515eb02d-2335-4d2d-92f2-b1cbdf9c3778|
|Managed HSM Crypto User (Kryptografiebenutzer für verwaltete HSMs)|Gewährt Zugriff zum Erstellen und Verwenden von Schlüsseln für kryptografische Vorgänge. Kann Schlüssel nicht endgültig löschen.|21dbd100-6940-42c2-9190-5d6cb909625b|
|Managed HSM Policy Administrator (Richtlinienadministrator für verwaltete HSMs)| Erteilt die Berechtigung zum Erstellen und Löschen von Rollenzuweisungen.|4bd23610-cdcf-4971-bdee-bdc562cc28e4|
|Managed HSM Crypto Auditor (Kryptografieprüfer für verwaltete HSMs)|Erteilt die Berechtigung zum Lesen (aber nicht zum Verwenden) von Schlüsseln.|2c18b078-7c48-4d3a-af88-5a3a1b3f82b3|
|Managed HSM Crypto Service Encryption (Kryptografiedienstverschlüsselung für verwaltete HSMs)| Erteilt die Berechtigung zum Verwenden eines Schlüssels für die Dienstverschlüsselung. |33413926-3206-4cdd-b39a-83574fe37a17|
|Managed HSM Backup (Sicherung verwalteter HSMs)| Erteilt die Berechtigung zum Ausführen einer HSM-Sicherung für einen einzelnen Schlüssel oder einer vollständigen HSM-Sicherung. |7b127d3c-77bd-4e3e-bbe0-dbb8971fa7f8|

## <a name="permitted-operations"></a>Erlaubte Vorgänge
> [!NOTE]  
> - Ein „X“ gibt an, dass eine Rolle zum Ausführen der Datenaktion berechtigt ist. Eine leere Zelle bedeutet, dass die Rolle nicht zum Ausführen der Datenaktion berechtigt ist.
> - Alle Datenaktionsnamen haben das Präfix „Microsoft.KeyVault/managedHsm“. Dieses wurde in den folgenden Tabellen zur besseren Übersichtlichkeit weggelassen.
> - Alle Rollennamen haben das Suffix „für verwaltete HSMs“. Dieses wurde in der folgenden Tabelle zur besseren Übersichtlichkeit weggelassen.

|Datenaktion | Administrator | Kryptoverantwortlicher | Kryptografiebenutzer | Richtlinien-Administrator | Kryptografiedienstverschlüsselung | Backup | Kryptografieprüfer|
|---|---|---|---|---|---|---|---|
|**Verwaltung der Sicherheitsdomäne**|
/securitydomain/download/action|<center>X</center>||||||
/securitydomain/upload/action|<center>X</center>||||||
/securitydomain/upload/read|<center>X</center>||||||
/securitydomain/transferkey/read|<center>X</center>||||||
|**Schlüsselverwaltung**|
|/keys/read/action|<center>X</center>|<center>X</center>|<center>X</center>||<center>X</center>||<center>X</center>|
|/keys/write/action|<center>X</center>|<center>X</center>|<center>X</center>||||
|/keys/create|<center>X</center>|<center>X</center>|<center>X</center>||||
|/keys/delete|<center>X</center>|<center>X</center>|||||
|/keys/deletedKeys/read/action|<center>X</center>|<center>X</center>|||||
|/keys/deletedKeys/recover/action|<center>X</center>|<center>X</center>|||||
|/keys/deletedKeys/delete|<center>X</center>|<center>X</center>|||||<center>X</center>|
|/keys/backup/action|<center>X</center>|<center>X</center>|<center>X</center>|||<center>X</center>|
|/keys/restore/action|<center>X</center>|<center>X</center>|||||
|/keys/export/action|<center>X</center>|<center>X</center>|||||
|/keys/import/action|<center>X</center>|<center>X</center>|||||
|**Kryptografische Schlüsselvorgänge**|
|/keys/encrypt/action|<center>X</center>|<center>X</center>|<center>X</center>||||
|/keys/decrypt/action|<center>X</center>|<center>X</center>|<center>X</center>||||
|/keys/wrap/action|<center>X</center>|<center>X</center>|<center>X</center>||<center>X</center>||
|/keys/unwrap/action|<center>X</center>|<center>X</center>|<center>X</center>||<center>X</center>||
|/keys/sign/action|<center>X</center>|<center>X</center>|<center>X</center>||||
|/keys/verify/action|<center>X</center>|<center>X</center>|<center>X</center>||||
|**Rollenverwaltung**|
|/roleAssignments/delete/action|<center>X</center>|||<center>X</center>|||
|/roleAssignments/read/action|<center>X</center>|||<center>X</center>|||
|/roleAssignments/write/action|<center>X</center>|||<center>X</center>|||
|/roleDefinitions/read/action|<center>X</center>|||<center>X</center>|||
|**Verwaltung der Sicherung/Wiederherstellung**|
|/backup/start/action|<center>X</center>|||||<center>X</center>|
|/backup/status/action|<center>X</center>|||||<center>X</center>|
|/restore/start/action|<center>X</center>||||||
|/restore/status/action|<center>X</center>||||||
||||||||

## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich eine Übersicht über die [rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)](../../role-based-access-control/overview.md) an.
- Sehen Sie sich ein Tutorial zur [Rollenverwaltung für verwaltetes HSM](role-management.md) an.
