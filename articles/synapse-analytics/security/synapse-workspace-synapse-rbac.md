---
title: Rollenbasierte Zugriffssteuerung in Synapse
description: In diesem Artikel wird die rollenbasierte Zugriffssteuerung in Azure Synapse Analytics erläutert.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/1/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 9e96d6decba679c7a4764a77f1e9720000faf78c
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100105146"
---
# <a name="what-is-synapse-role-based-access-control-rbac"></a>Was ist die rollenbasierte Zugriffssteuerung (RBAC) in Synapse?

Synapse RBAC erweitert die Funktionen von [Azure RBAC](../../role-based-access-control/overview.md) auf Synapse-Arbeitsbereiche und deren Inhalt. 

Azure RBAC dient zum Verwalten der Benutzer, die den Synapse-Arbeitsbereich und die zugehörigen SQL-Pools, Apache Spark-Pools und Integration Runtimes erstellen, aktualisieren oder löschen können.

Synapse RBAC dient zum Verwalten der folgenden Zugriffsberechtigungen:
- Veröffentlichen von Codeartefakten und Auflisten von veröffentlichten Codeartefakten bzw. Zugreifen auf diese 
- Ausführen von Code in Apache Spark-Pools und Integration Runtimes
- Zugreifen auf verknüpfte Dienste (Daten), die durch Anmeldeinformationen geschützt sind 
- Überwachen oder Abbrechen von Auftragsausführungen, Überprüfen von Auftragsausgaben und Ausführungsprotokollen  

>[!Note]
>Synapse RBAC wird zwar verwendet, um den Zugriff auf veröffentlichte SQL-Skripts zu verwalten, bietet jedoch nur eine eingeschränkte Zugriffssteuerung für serverlose SQL-Pools und wird _nicht_ zum Steuern des Zugriffs auf dedizierte SQL-Pools verwendet.  Der Zugriff auf SQL-Pools wird hauptsächlich mithilfe der SQL-Sicherheit gesteuert.

## <a name="what-can-i-do-with-synapse-rbac"></a>Wofür kann ich Synapse RBAC verwenden?

Hier sind einige Beispiele für Genehmigungen in Synapse RBAC:
  - Veröffentlichen von Änderungen an Apache Spark-Notebooks und -Aufträgen im Livedienst (Benutzer)
  - Ausführen und Abbrechen von Notebooks und Spark-Aufträgen für einen bestimmten Apache Spark-Pool (Benutzer)
  - Verwenden bestimmter Anmeldeinformationen zum Ausführen von Pipelines, die durch die Systemidentität des Arbeitsbereichs geschützt werden, und Zugreifen auf Daten in verknüpften Diensten, die mit Anmeldeinformationen geschützt sind (Benutzer) 
  - Verwalten, Überwachen und Abbrechen von Auftragsausführung in bestimmten Spark-Pools (Administratoren)    

## <a name="how-synapse-rbac-works"></a>Funktionsweise von Synapse RBAC
Wie Azure RBAC funktioniert auch Synapse RBAC über das Erstellen von Rollenzuweisungen. Eine Rollenzuweisung umfasst drei Elemente: einen Sicherheitsprinzipal, eine Rollendefinition und einen Bereich.  

### <a name="security-principals"></a>Sicherheitsprinzipale

Ein _Sicherheitsprinzipal_ ist ein Benutzer, eine Gruppe, ein Dienstprinzipal oder eine verwaltete Identität.

### <a name="roles"></a>Rollen
 
Eine _Rolle_ ist eine Sammlung von Berechtigungen oder Aktionen, die für bestimmte Ressourcen- oder Artefakttypen ausgeführt werden können.

Synapse bietet integrierte Rollen, die Sammlungen von Aktionen für die Anforderungen unterschiedlicher Personas definieren:
- Administratoren können Vollzugriff erhalten, um einen Arbeitsbereich erstellen und konfigurieren zu können. 
- Entwickler können SQL-Skripts, Notebooks, Pipelines und Datenflüsse erstellen, aktualisieren und debuggen, aber diesen Code nicht für Computeressourcen/-daten in der Produktion veröffentlichen oder ausführen.
- Operatoren können den Systemstatus und die Anwendungsausführung überwachen und verwalten und Protokolle überprüfen, ohne Zugriff auf den Code oder die Ausgaben der Ausführung zu benötigen.
- Das Sicherheitspersonal kann Endpunkte verwalten und konfigurieren, ohne auf Code, Computeressourcen oder Daten zugreifen zu müssen.

[Hier](./synapse-workspace-synapse-rbac-roles.md) finden Sie weitere Informationen zu den integrierten Synapse-Rollen. 

### <a name="scopes"></a>Bereiche

Mit einem _Bereich_ werden die Ressourcen oder Artefakte definiert, für die der Zugriff gilt.  Synapse unterstützt hierarchische Bereiche.  Berechtigungen, die auf einem Bereich höherer Ebene erteilt wurden, werden auf Objekte auf niedrigerer Ebene vererbt.  In Synapse RBAC ist der Bereich auf der obersten Ebene der Arbeitsbereich.  Durch das Zuweisen einer Rolle auf Arbeitsbereichsebene werden die zugehörigen Berechtigungen allen entsprechenden Objekten im Arbeitsbereich erteilt.  

Folgende Bereiche werden derzeit in einem Arbeitsbereich unterstützt: Apache Spark-Pool, Integration Runtime, verknüpfter Dienst und Anmeldeinformationen. 

Der Zugriff auf Codeartefakte wird auf Arbeitsbereichsebene gewährt.  Das Gewähren von Zugriff auf Sammlungen von Artefakten innerhalb eines Arbeitsbereichs wird in einem späteren Release unterstützt.

## <a name="resolving-role-assignments-to-determine-permissions"></a>Auflösen von Rollenzuweisungen zum Bestimmen der Berechtigungen

Eine Rollenzuweisung gewährt dem Prinzipal die Berechtigungen, die für die Rolle im angegebenen Bereich definiert sind.

Synapse RBAC basiert wie Azure RBAC auf einem additiven Modell. Einem einzelnen Prinzipal können mehrere Rollen in unterschiedlichen Bereichen zugewiesen werden. Beim Berechnen der Berechtigungen eines Sicherheitsprinzipals berücksichtigt das System alle Rollen, die dem Prinzipal und den Gruppen zugewiesen sind, die den Prinzipal direkt oder indirekt einschließen.  Außerdem wird der Bereich jeder Zuweisung berücksichtigt, um die geltenden Berechtigungen zu bestimmen.  

## <a name="enforcing-assigned-permissions"></a>Erzwingen zugewiesener Berechtigungen

In Synapse Studio sind möglicherweise bestimmte Schaltflächen oder Optionen abgeblendet, wenn Sie nicht über die erforderlichen Berechtigungen verfügen, oder es wird ein Berechtigungsfehler zurückgegeben. 

Wenn eine Schaltfläche oder Option deaktiviert ist, können Sie mit dem Mauszeiger darauf zeigen, um eine QuickInfo mit der erforderlichen Berechtigung anzuzeigen.  Bitten Sie einen Synapse-Administrator, Ihnen eine Rolle zuzuweisen, die die erforderliche Berechtigung umfasst. Sie finden die Rollen, die bestimmte Aktionen ermöglichen, [hier](./synapse-workspace-synapse-rbac-roles.md).

## <a name="who-can-assign-synapse-rbac-roles"></a>Wer kann Synapse RBAC-Rollen zuweisen?

Nur ein Synapse-Administrator kann Synapse RBAC-Rollen zuweisen.  Ein Synapse-Administrator auf Arbeitsbereichsebene kann Zugriff in jedem Bereich gewähren.  Ein Synapse-Administrator in einem Bereich auf niedrigerer Ebene kann nur Zugriff für diesen Bereich gewähren. 

Beim Erstellen eines neuen Arbeitsbereichs erhält der Ersteller automatisch die Rolle „Synapse-Administrator“ für diesen Arbeitsbereich.   

## <a name="where-do-i-manage-synapse-rbac"></a>Wo wird Synapse RBAC verwaltet?

Synapse RBAC wird in Synapse Studio mithilfe der Zugriffssteuerungstools im Verwaltungshub verwaltet. 

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über die integrierten [Synapse RBAC-Rollen](./synapse-workspace-synapse-rbac-roles.md).

Erfahren Sie über das [Überprüfen von Synapse RBAC-Rollenzuweisungen](./how-to-review-synapse-rbac-role-assignments.md) für einen Arbeitsbereich.

Erfahren Sie, [wer Synapse RBAC-Rollen zuweisen darf](./how-to-manage-synapse-rbac-role-assignments.md).