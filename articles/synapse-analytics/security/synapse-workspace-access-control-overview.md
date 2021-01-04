---
title: 'Übersicht: Zugriffssteuerung für einen Synapse-Arbeitsbereich'
description: In diesem Artikel werden die Mechanismen beschrieben, die zum Steuern des Zugriffs auf einen Synapse-Arbeitsbereich und die darin enthaltenen Ressourcen und Codeartefakte verwendet werden.
services: synapse-analytics
author: billgib
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 12/03/2020
ms.author: billgib
ms.reviewer: jrasnick
ms.openlocfilehash: 36c4ce34d6b0c4df119d5531cda725605cfd0bc1
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96523717"
---
# <a name="synapse-access-control"></a>Synapse-Zugriffssteuerung 

Dieser Artikel bietet eine Übersicht über die verfügbaren Mechanismen zum Steuern des Zugriffs auf Synapse-Computeressourcen und -Daten.  

## <a name="overview"></a>Übersicht

Synapse bietet ein umfassendes und differenziertes Zugriffssteuerungssystem, in das folgende Mechanismen integriert sind: 
- **Azure-Rollen** für die Ressourcenverwaltung und den Zugriff auf Daten im Speicher 
- **Synapse-Rollen** für die Verwaltung des Livezugriffs auf Code und die Ausführung 
- **SQL-Rollen** für den Zugriff auf Datenebene auf Daten in SQL-Pools und 
- **Git-Berechtigungen** für die Quellcodeverwaltung, einschließlich Unterstützung für Continuous Integration und Deployment  

Synapse-Rollen bieten Berechtigungssätze, die für verschiedene Bereiche angewendet werden können. Dank dieser Granularität ist es ganz einfach, Administratoren, Entwicklern, Sicherheitspersonal und Operatoren den entsprechenden Zugriff auf Computeressourcen und Daten zu gewähren.

Die Zugriffssteuerung kann mithilfe von Sicherheitsgruppen, die auf die Aufgabengebiete der Benutzer zugeschnitten sind, vereinfacht werden.  Um den Zugriff zu verwalten, müssen Sie lediglich den entsprechenden Sicherheitsgruppen Benutzer hinzufügen bzw. daraus entfernen.

## <a name="access-control-elements"></a>Elemente der Zugriffssteuerung

### <a name="creating-and-managing-synapse-resources"></a>Erstellen und Verwalten von Synapse-Ressourcen

Mit Azure-Rollen wird die Verwaltung folgender Ressourcen gesteuert: 
- Dedizierte SQL-Pools 
- Apache Spark-Pools 
- Integration Runtimes 

Zum Erstellen dieser Ressourcen müssen Sie ein Azure-Besitzer oder ein Mitwirkender für die Ressourcengruppe sein.  Um sie zu verwalten, müssen Sie ein Azure-Besitzer oder ein Mitwirkender für die Ressourcengruppe oder die einzelnen Ressourcen sein. 

### <a name="developing-and-executing-code-in-synapse"></a>Entwickeln und Ausführen von Code in Synapse 

Synapse unterstützt zwei Entwicklungsmodelle.

- **Synapse-Liveentwicklung**.  Sie entwickeln und debuggen Code in Synapse Studio und **veröffentlichen** den Code anschließend, um ihn zu speichern und auszuführen.  Der Synapse-Dienst ist die „Source of Truth“ (Quelle der Wahrheit) für die Codebearbeitung und -ausführung.  Nicht veröffentlichte Arbeit geht verloren, wenn Sie Synapse Studio schließen.  
- **Entwicklung mit Aktivierung von Git**. Sie entwickeln und debuggen Code in Synapse Studio und **committen** Änderungen in einen Arbeitsbranch eines Git-Repositorys. Die Arbeit aus einem oder mehreren Branches wird in einen Kollaborationsbranch eingebunden, über den Sie sie im Dienst **veröffentlichen** können.  Das Git-Repository ist die Source of Truth für die Codebearbeitung und der Dienst die Source of Truth für die Ausführung. Änderungen müssen vor dem Schließen von Synapse Studio in das Git-Repository committet oder im Dienst veröffentlicht werden. Weitere Informationen zur Verwendung von Synapse Analytics mit Git finden Sie [hier](https://go.microsoft.com/fwlink/?linkid=2150100).

Bei beiden Entwicklungsmodellen kann jeder Benutzer mit Zugriff auf Synapse Studio Codeartefakte erstellen.  Sie benötigen jedoch zusätzliche Berechtigungen, um Artefakte im Dienst zu veröffentlichen, veröffentlichte Artefakte zu lesen, Änderungen in Git zu committen, Code auszuführen und auf verknüpfte Daten zuzugreifen, die durch Anmeldeinformationen geschützt sind.

### <a name="synapse-roles"></a>Synapse-Rollen

Synapse-Rollen werden verwendet, um den Zugriff auf den Synapse-Dienst zu steuern, und ermöglichen Ihnen Folgendes: 
- Auflisten veröffentlichter Codeartefakte 
- Veröffentlichen von Codeartefakten, verknüpften Diensten und Anmeldeinformationsdefinitionen
- Ausführen von Codes oder Pipelines, die Synapse-Computeressourcen verwenden
- Ausführen von Codes oder Pipelines, die auf verknüpfte Daten zugreifen, die durch Anmeldeinformationen geschützt sind
- Anzeigen von Ausgaben, die veröffentlichten Codeartefakten zugeordnet sind
- Überwachen des Computeressourcenstatus und Anzeigen von Laufzeitprotokollen

Synapse-Rollen können auf Arbeitsbereichsebene oder unter Verwendung differenzierterer Bereiche zugewiesen werden, um die für bestimmte Synapse-Ressourcen erteilten Berechtigungen einzuschränken.

### <a name="git-permissions"></a>Git-Berechtigungen

Wenn Sie die Entwicklung mit Aktivierung von Git im Git-Modus verwenden, bestimmen Ihre Git-Berechtigungen, ob Sie Änderungen an Codeartefakten (einschließlich Definitionen von verknüpften Diensten und Anmeldeinformationen) lesen und committen können.   
   
### <a name="accessing-data-in-sql"></a>Zugreifen auf Daten in SQL

Wenn Sie mit dedizierten und serverlosen SQL-Pools arbeiten, wird der Zugriff auf Datenebene mithilfe von SQL-Berechtigungen gesteuert. 

Der Ersteller eines Arbeitsbereichs wird als Active Directory-Administrator des Arbeitsbereichs zugewiesen.  Nach der Erstellung kann diese Rolle im Azure-Portal einem anderen Benutzer oder einer Sicherheitsgruppe zugewiesen werden.

**Serverlose SQL-Pools**: Synapse-Administratoren werden Berechtigungen vom Typ `db_owner` (`DBO`) für den serverlosen SQL-Pool „Integriert“ erteilt. Um anderen Benutzern Zugriff auf serverlose SQL-Pools zu gewähren, müssen Synapse-Administratoren SQL-Skripts für jeden serverlosen Pool ausführen.  

**Dedizierte SQL-Pools**: Die Active Directory-Administratorberechtigung wird dem Ersteller des Arbeitsbereichs und der Arbeitsbereichs-MSI (Managed Service Identity, verwaltete Dienstidentität) erteilt.  Die Berechtigung für den Zugriff auf dedizierte SQL-Pools wird nicht automatisch gewährt. Um anderen Benutzern oder Gruppen Zugriff auf dedizierte SQL-Pools zu gewähren, muss der Active Directory-Administrator SQL-Skripts für jeden dedizierten SQL-Pool ausführen.

Beispiele für SQL-Skripts zum Erteilen von SQL-Berechtigungen für SQL-Pools finden Sie unter [Einrichten der Synapse-Zugriffssteuerung](./how-to-set-up-access-control.md).  

 ### <a name="accessing-system-managed-data-in-storage"></a>Zugreifen auf systemseitig verwaltete Daten im Speicher

Serverlose SQL-Pools und Apache Spark-Tabellen speichern Daten in einem ADLS Gen2-Container, der dem Arbeitsbereich zugeordnet ist.  Benutzern und der Arbeitsbereichs-MSI muss das Zugriffsrecht „Mitwirkender an Storage-Blobdaten“ für diesen ADLS Gen2-Speichercontainer zugewiesen werden.  

## <a name="using-security-groups-as-a-best-practice"></a>Verwenden von Sicherheitsgruppen als bewährte Methode

Um die Verwaltung der Zugriffssteuerung zu vereinfachen, können Sie einzelnen Benutzern und Gruppen mithilfe von Sicherheitsgruppen Rollen zuweisen. Sicherheitsgruppen können erstellt werden, um Personas oder Tätigkeitsbereiche in Ihrer Organisation zu spiegeln, die Zugriff auf Synapse-Ressourcen oder -Artefakte benötigen.  Diesen auf Personas beruhenden Sicherheitsgruppen können dann eine oder mehrere Azure-Rollen, Synapse-Rollen, SQL-Berechtigungen oder Git-Berechtigungen zugewiesen werden. Mit gut gewählten Sicherheitsgruppen können Sie Benutzern problemlos die erforderlichen Berechtigungen zuweisen, indem Sie sie der entsprechenden Sicherheitsgruppe hinzufügen. 

>[!Note]
>Wenn Sie den Zugriff mithilfe von Sicherheitsgruppen verwalten, werden Änderungen mit einer zusätzlichen durch Azure Active Directory verursachten Wartezeit wirksam. 

## <a name="access-control-enforcement-in-synapse-studio"></a>Erzwingung der Zugriffssteuerung in Synapse Studio

Synapse Studio verhält sich je nach Berechtigungen und aktuellem Modus unterschiedlich:
- **Synapse-Livemodus:** Synapse Studio verhindert, dass Sie veröffentlichte Inhalte anzeigen, Inhalte veröffentlichen oder andere Aktionen ausführen, wenn Sie nicht über die erforderliche Berechtigung verfügen.  In einigen Fällen ist die Erstellung von Codeartefakten, die Sie nicht verwenden oder speichern können, nicht möglich. 
- **Git-Modus:** Wenn Sie über Git-Berechtigungen verfügen, die das Committen von Änderungen in den aktuellen Branch erlauben, ist die Commitaktion auch dann zulässig, wenn Sie nicht über die Berechtigung zum Veröffentlichen von Änderungen im Livedienst verfügen.  

In einigen Fällen können Sie auch ohne Berechtigung zum Veröffentlichen oder Committen Codeartefakte erstellen.  Dies ermöglicht es Ihnen, Code auszuführen (mit den erforderlichen Ausführungsberechtigungen). Weitere Informationen zu den erforderlichen Rollen für gängige Aufgaben finden Sie [hier](./synapse-workspace-understand-what-role-you-need.md). 

Wenn eine Funktion in Synapse Studio deaktiviert ist, wird die erforderliche Berechtigung in einer QuickInfo angezeigt.  Die erforderlichen Rollen zum Bereitstellen der fehlenden Berechtigungen können Sie im [Leitfaden zu den RBAC-Rollen von Synapse](./synapse-workspace-synapse-rbac-roles.md#synapse-rbac-actions-and-the-roles-that-permit-them) nachlesen.


## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [Synapse RBAC](./synapse-workspace-synapse-rbac.md).
- Erfahren Sie mehr über die [RBAC-Rollen von Synapse](./synapse-workspace-synapse-rbac-roles.md).
- Erfahren Sie, wie Sie mithilfe von Sicherheitsgruppen die [Zugriffssteuerung für einen Synapse-Arbeitsbereich einrichten](./how-to-set-up-access-control.md).
- Erfahren Sie, wie Sie [Synapse RBAC-Rollenzuweisungen überprüfen](./how-to-review-synapse-rbac-role-assignments.md).
- Erfahren Sie, wie Sie [Synapse RBAC-Rollenzuweisungen verwalten](./how-to-manage-synapse-rbac-role-assignments.md).