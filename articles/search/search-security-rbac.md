---
title: Festlegen von RBAC-Rollen für den Azure-Administratorzugriff im Portal – Azure Search
description: Rollenbasierte Verwaltungsfunktionen (RBAC) im Azure-Portal zum Steuern und Delegieren von administrativen Aufgaben für die Verwaltung von Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 34def35eba1e5c1645e6e1f9a505704d153ac716
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61282365"
---
# <a name="set-rbac-roles-for-administrative-access"></a>Festlegen von RBAC-Rollen für den Administratorzugriff

In Azure wird ein [globales Modell für die rollenbasierte Autorisierung](../role-based-access-control/role-assignments-portal.md) für alle Dienste bereitgestellt, die über das Portal oder mit Resource Manager-APIs verwaltet werden. Die Rollen „Besitzer“, „Mitwirkender“ und „Leser“ bestimmen die Ebene der *Dienstverwaltung* für Active Directory-Benutzer, -Gruppen und -Dienstprinzipale, die einer Rolle jeweils zugewiesen sind. 

> [!Note]
> Es gibt keine rollenbasierte Zugriffssteuerung zum Schutz der Teile eines Indexes oder einer Teilmenge von Dokumenten. Für den identitätsbasierten Zugriff über Suchergebnisse können Sie Sicherheitsfilter erstellen, um die Ergebnisse anhand der Identität einzugrenzen. Auf diese Weise werden Dokumente entfernt, auf die der Anforderer keinen Zugriff haben sollte. Weitere Informationen finden Sie unter [Sicherheitsfilter](search-security-trimming-for-azure-search.md) und [Sichern mit Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="management-tasks-by-role"></a>Verwaltungsaufgaben nach Rolle

Für Azure Search sind Rollen mit Berechtigungsebenen zugeordnet, die die folgenden Verwaltungsaufgaben unterstützen:

| Rolle | Aufgabe |
| --- | --- |
| Owner (Besitzer) |Erstellen oder löschen Sie den Dienst bzw. jedes Objekt im Dienst, einschließlich API-Schlüsseln, Indizes, Indexern sowie Datenquellen und Zeitplänen für Indexer.<p>Zeigen Sie den Dienststatus an, einschließlich Anzahl und Speichergröße.<p>Hinzufügen oder Löschen von Rollenmitgliedschaften (Nur ein Besitzer kann die Rollenmitgliedschaften verwalten.)<p>Abonnementadministratoren und Dienstbesitzer sind automatisch Mitglieder der Besitzerrolle. |
| Mitwirkender |Die gleiche Ebene wie „Besitzer“, mit Ausnahme der RBAC-Rollenverwaltung. So kann z.B. ein Mitwirkender Objekte erstellen oder löschen bzw. [API-Schlüssel](search-security-api-keys.md) anzeigen und neu generieren, aber nicht die Rollenmitgliedschaften ändern. |
| [Mitwirkender von Suchdienst](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor) | Entspricht der Rolle „Mitwirkender“. |
| Leser |Anzeigen von Dienstzusammenfassungen und Metriken. Mitglieder dieser Rolle können Index, Indexer, Datenquelle oder Schlüsselinformationen nicht anzeigen.  |

Rollen erteilen keine Zugriffsrechte für den Dienstendpunkt. Suchdienstoperationen, wie z. B. die Indexverwaltung, Auffüllung des Indexes und Abfragen von Suchdaten werden durch die API-Schlüssel und nicht durch Rollen gesteuert. Weitere Informationen finden Sie unter [Verwalten von API-Schlüsseln](search-security-api-keys.md).

## <a name="see-also"></a>Weitere Informationen

+ [Verwalten mit PowerShell](search-manage-powershell.md) 
+ [Überlegungen zur Leistung und Optimierung von Azure Search](search-performance-optimization.md)
+ [Erste Schritte mit der rollenbasierten Zugriffssteuerung im Azure-Portal](../role-based-access-control/overview.md)