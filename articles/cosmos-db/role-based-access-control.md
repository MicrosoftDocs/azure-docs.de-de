---
title: Rollenbasierte Zugriffssteuerung in Azure Cosmos DB mit Azure Active Directory-Integration
description: Erfahren Sie, wie Azure Cosmos DB Datenbankschutz mithilfe der Active Directory-Integration (RBAC) bereitstellt.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 971d2ec96906a3309963495dd1af5d293a71f265
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66243517"
---
# <a name="role-based-access-control-in-azure-cosmos-db"></a>Rollenbasierte Zugriffssteuerung in Azure Cosmos DB

Azure Cosmos DB bietet für gängige Verwaltungsszenarien eine integrierte rollenbasierte Zugriffssteuerung (RBAC). Eine Person mit einem Profil in Azure Active Directory kann diese RBAC-Rollen Benutzern, Gruppen, Dienstprinzipalen oder verwalteten Identitäten zuweisen, um den Zugriff auf Ressourcen und Vorgänge für Azure Cosmos DB-Ressourcen zu gewähren oder zu verweigern. Rollenzuweisungen sind auf den Zugriff auf Steuerungsebene beschränkt, was den Zugriff auf Konten, Datenbanken, Container und Angebote (Durchsatz) für Azure Cosmos umfasst.

## <a name="built-in-roles"></a>Integrierte Rollen

Die folgenden vordefinierten Rollen werden von Azure Cosmos DB unterstützt:

|**Integrierte Rolle**  |**Beschreibung**  |
|---------|---------|
|[DocumentDB-Kontomitwirkender](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)   | Kann Azure Cosmos DB-Konten verwalten.  |
|[Cosmos DB-Kontoleser](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)  | Kann Azure Cosmos DB-Kontodaten lesen.        |
|[Cosmos-Sicherungsoperator](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)     |  Kann eine Wiederherstellungsanforderung für eine Azure Cosmos-Datenbank oder einen Container übermitteln.       |
|[Cosmos DB-Operator](../role-based-access-control/built-in-roles.md#cosmos-db-operator)  | Kann Azure Cosmos-Konten, -Datenbanken und -Container bereitstellen, aber nicht auf die Schlüssel zugreifen, die für den Zugriff auf die Daten erforderlich sind.         |

> [!IMPORTANT]
> RBAC-Unterstützung in Azure Cosmos DB gilt nur für Vorgänge auf Steuerungsebene. Vorgänge auf Datenebene werden mithilfe von Hauptschlüsseln oder Ressourcentoken abgesichert. Weitere Informationen finden Sie unter [Sicherer Zugriff auf Daten in Azure Cosmos DB](secure-access-to-data.md).

## <a name="identity-and-access-management-iam"></a>Identitäts- und Zugriffsverwaltung (IAM)

Der Bereich **Zugriffssteuerung (IAM)**  im Azure-Portal dient zum Konfigurieren der rollenbasierten Zugriffssteuerung für Azure Cosmos-Ressourcen. Die Rollen werden Benutzern, Gruppen, Dienstprinzipalen und verwalteten Identitäten in Active Directory zugewiesen. Sie können für Einzelbenutzer und Gruppen integrierte oder benutzerdefinierte Rollen verwenden. Der folgende Screenshot zeigt die Active Directory-Integration (RBAC) mithilfe der Zugriffssteuerung (IAM) im Azure-Portal:

![Zugriffssteuerung (IAM) im Azure-Portal: Veranschaulichung der Datenbanksicherheit](./media/role-based-access-control/database-security-identity-access-management-rbac.png)

## <a name="custom-roles"></a>Benutzerdefinierte Rollen

Zusätzlich zu den integrierten Rollen können Benutzer in Azure auch [benutzerdefinierte Rollen](../role-based-access-control/custom-roles.md) erstellen und diese Rollen Dienstprinzipalen in allen Abonnements innerhalb ihres Active Directory-Mandanten zuweisen. Benutzerdefinierte Rollen bieten Benutzern eine Möglichkeit, RBAC-Rollendefinitionen mit einem benutzerdefinierten Satz von Ressourcenanbietervorgängen zu erstellen. Um zu erfahren, welche Vorgänge für die Erstellung benutzerdefinierter Rollen für Azure Cosmos DB verfügbar sind, siehe [Vorgänge für Azure Resource Manager-Ressourcenanbieter](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb).

## <a name="next-steps"></a>Nächste Schritte

- [Was ist die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) für Azure-Ressourcen?](../role-based-access-control/overview.md)
- [Benutzerdefinierte Rollen für Azure-Ressourcen](../role-based-access-control/custom-roles.md)
- [Vorgänge für Azure Resource Manager-Ressourcenanbieter](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)
