---
title: Festlegen von Azure-Rollen für den Azure-Administratorzugriff
titleSuffix: Azure Cognitive Search
description: Rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC) im Azure-Portal zum Steuern und Delegieren von administrativen Aufgaben für die Verwaltung von Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: ee122727100ec0abad0dfe93b9e5f1be0276cb8e
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92519498"
---
# <a name="set-azure-roles-for-administrative-access-to-azure-cognitive-search"></a>Festlegen von Azure-Rollen für den Administratorzugriff auf Azure Cognitive Search

In Azure wird ein [globales Modell für die rollenbasierte Autorisierung](../role-based-access-control/role-assignments-portal.md) für alle Dienste bereitgestellt, die über das Portal oder mit Resource Manager-APIs verwaltet werden. Die Rollen „Besitzer“, „Mitwirkender“ und „Leser“ bestimmen die Ebene der *Dienstverwaltung* für Active Directory-Benutzer, -Gruppen und -Dienstprinzipale, die einer Rolle jeweils zugewiesen sind. 

> [!Note]
> Zum Sichern von Inhalten im Dienst gibt es keine rollenbasierte Zugriffssteuerung in Azure. Sie müssen entweder einen Administrator-API-Schlüssel oder einen Abfrage-API-Schlüssel für authentifizierte Anforderungen an den Dienst selbst verwenden. Für den identitätsbasierten Zugriff über Suchergebnisse können Sie Sicherheitsfilter erstellen, um die Ergebnisse anhand der Identität einzugrenzen. Auf diese Weise werden Dokumente entfernt, auf die der Anforderer keinen Zugriff haben sollte. Weitere Informationen finden Sie unter [Sicherheitsfilter](search-security-trimming-for-azure-search.md).

## <a name="management-tasks-by-role"></a>Verwaltungsaufgaben nach Rolle

Für die kognitive Azure-Suche sind Rollen mit Berechtigungsebenen zugeordnet, die die folgenden Verwaltungsaufgaben unterstützen:

| Role | Aufgabe |
| --- | --- |
| Besitzer |Erstellen oder löschen Sie den Dienst bzw. jedes Objekt im Dienst, einschließlich API-Schlüsseln, Indizes, Indexern sowie Datenquellen und Zeitplänen für Indexer.<p>Zeigen Sie den Dienststatus an, einschließlich Anzahl und Speichergröße.<p>Hinzufügen oder Löschen von Rollenmitgliedschaften (Nur ein Besitzer kann die Rollenmitgliedschaften verwalten.)<p>Abonnementadministratoren und Dienstbesitzer sind automatisch Mitglieder der Besitzerrolle. |
| Mitwirkender | Die gleiche Zugriffsebene wie „Besitzer“, mit Ausnahme der Azure-Rollenverwaltung. So kann z.B. ein Mitwirkender Objekte erstellen oder löschen bzw. [API-Schlüssel](search-security-api-keys.md) anzeigen und neu generieren, aber nicht die Rollenmitgliedschaften ändern.<br><br>[Mitwirkender von Suchdienst](../role-based-access-control/built-in-roles.md#search-service-contributor) entspricht der allgemeinen integrierten Rolle „Mitwirkender“. |
| Leser |Mit dieser Rolle können Sie grundlegende Informationen zum Dienst anzeigen, z. B. Dienstendpunkt, Abonnement, Ressourcengruppe, Region, Tarif und Kapazität. Sie können auch auf der Registerkarte „Überwachung“ Dienstmetriken wie z. B. die durchschnittliche Anzahl von Abfragen pro Sekunde anzeigen. Mitglieder dieser Rolle können keine Informationen zu Index, Indexer, Datenquelle oder Skillset anzeigen. Dies schließt auch Nutzungsdaten für diese Objekte ein, beispielsweise die Anzahl von vorhandenen Indizes im Dienst. |

Rollen erteilen keine Zugriffsrechte für den Dienstendpunkt. Suchdienstoperationen, wie z. B. die Indexverwaltung, Auffüllung des Indexes und Abfragen von Suchdaten werden durch die API-Schlüssel und nicht durch Rollen gesteuert. Weitere Informationen finden Sie unter [Verwalten von API-Schlüsseln](search-security-api-keys.md).

## <a name="permissions-table"></a>Berechtigungstabelle

Die folgende Tabelle enthält eine Zusammenfassung der in der kognitiven Azure-Suche zulässigen Vorgänge sowie Informationen dazu, welcher Schlüssel für den jeweiligen Vorgang erforderlich ist.

Azure RBAC-Berechtigungen gelten für Portalvorgänge und die Dienstverwaltung (Erstellen, Löschen oder Ändern eines Diensts oder der zugehörigen API-Schlüssel). API-Schlüssel werden erstellt, wenn der Dienst bereits vorhanden ist, und gelten für Inhaltsvorgänge im Dienst. Darüber hinaus interagiert ein Azure RBAC-Besitzer oder -Mitwirkender bei inhaltsbezogenen Vorgängen im Portal (z. B. beim Erstellen oder Löschen von Objekten) über einen impliziten Administrator-API-Schlüssel mit dem Dienst.

| Vorgang | Gesteuert von |
|-----------|-------------------------|
| Erstellen von Diensten | Azure RBAC-Berechtigungen: „Besitzer“ oder „Mitwirkender“ |
| Skalieren von Diensten | Azure RBAC-Berechtigungen: „Besitzer“ oder „Mitwirkender“|
| Löschen von Diensten | Azure RBAC-Berechtigungen: „Besitzer“ oder „Mitwirkender“ |
| Verwalten von Administrator- oder Abfrageschlüsseln | Azure RBAC-Berechtigungen: „Besitzer“ oder „Mitwirkender“|
| Anzeigen von Dienstinformationen im Portal oder einer Verwaltungs-API | Azure RBAC-Berechtigungen: „Besitzer“, „Mitwirkender“ oder „Leser“  |
| Anzeigen von Objektinformationen und Metriken im Portal oder einer Verwaltungs-API | Azure RBAC-Berechtigungen: „Besitzer“ oder „Mitwirkender“ |
| Erstellen/Ändern/Löschen von Objekten für den Dienst: <br>Indizes und Komponententeile (einschließlich Analysedefinitionen, Bewertungsprofile und CORS-Optionen), Indexer, Datenquellen, Synonyme und Vorschläge | Administratorschlüssel bei Verwendung einer API, „Azure RBAC-Besitzer“ oder „Azure RBAC-Mitwirkender“ bei Verwendung des Portals |
| Abfragen von Indizes | Administrator- oder Abfrageschlüssel bei Verwendung einer API, „Azure RBAC-Besitzer“ oder „Azure RBAC-Mitwirkender“ bei Verwendung des Portals |
| Abfragen von Systeminformationen zu Objekten, beispielsweise zum Zurückgeben von Statistiken, Zählungen und Objektlisten | Administratorschlüssel bei Verwendung einer API, „Azure RBAC-Besitzer“ oder „Azure RBAC-Mitwirkender“ bei Verwendung des Portals |

## <a name="next-steps"></a>Nächste Schritte

+ [Verwalten mit PowerShell](search-manage-powershell.md) 
+ [Leistung und Optimierung in der kognitiven Azure-Suche](search-performance-optimization.md)
+ [Was ist die rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)?](../role-based-access-control/overview.md)
