---
title: Rollen und Aufgaben im Team Data Science-Prozess
description: Enthält einen Überblick über die Hauptkomponenten, Mitarbeiterrollen und zugeordneten Aufgaben für eine Data Science-Gruppe.
author: marktab
manager: marktab
editor: marktab
services: machine-learning
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4bcbbd9378b6ea861c926eed16d2ceed8131b913
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000962"
---
# <a name="team-data-science-process-roles-and-tasks"></a>Rollen und Aufgaben im Team Data Science-Prozess

Der Team Data Science-Prozess (TDSP) ist ein von Microsoft entwickeltes Framework, mit dem eine strukturierte Methodik zum effizienten Erstellen von Predictive Analytics-Lösungen und von intelligenten Anwendungen bereitgestellt wird. In diesem Artikel finden Sie eine Übersicht über die wichtigsten Mitarbeiterrollen und die zugeordneten Aufgaben für ein Data Science-Team, das diesen Prozess standardmäßig einsetzt.

Dieser Einführungsartikel enthält Links zu Tutorials zum Einrichten der TDSP-Umgebung. Die Tutorials bieten ausführliche Anleitungen zur Verwendung von Azure DevOps Projects, Azure Repos-Repositorys und Azure Boards.  Sie sollen dazu motivieren, vom Konzept über die Modellierung zur Bereitstellung zu gelangen.

In den Tutorials wird Azure DevOps verwendet, da dies der Implementierung des TDSP bei Microsoft entspricht. Azure DevOps ermöglicht die Zusammenarbeit per Integration der rollenbasierten Sicherheit, Verwaltung und Nachverfolgung von Arbeitselementen sowie Hosting, Freigabe und Quellcodeverwaltung für Code. In den Tutorials wird außerdem eine [Data Science Virtual Machine (DSVM)](https://aka.ms/dsvm) von Azure als Analysedesktop verwendet, der über mehrere beliebte vorkonfigurierte Data Science-Tools verfügt und in Microsoft-Software und Azure-Dienste integriert ist. 

Sie können die Tutorials zum Implementieren des TDSP auch nutzen, indem Sie andere Tools und Umgebungen für Codehosting, agile Planung und Entwicklung verwenden. Einige Features sind dann aber ggf. nicht verfügbar.

## <a name="structure-of-data-science-groups-and-teams"></a>Struktur von Data Science-Gruppen und -Teams

Data Science-Funktionen werden in Unternehmen häufig gemäß der folgenden Hierarchie strukturiert:

- Data Science-Gruppe
  - Data Science-Team(s) innerhalb der Gruppe

In einer Struktur dieser Art gibt es Gruppen- und Teamleiter. Normalerweise wird ein Data Science-Projekt von einem Data Science-Team durchgeführt. Data Science-Teams verfügen über Projektleiter für Projektmanagement und Governancetasks und über einzelne Data Scientists und Techniker, die die Data Science- und Data Engineering-Aufgaben des Projekts durchführen. Die anfängliche Einrichtung des Projekts und die Governanceschritte werden von den Gruppen-, Team- oder Projektleitern übernommen.

## <a name="definition-and-tasks-for-the-four-tdsp-roles"></a>Definition und Aufgaben für die vier TDSP-Rollen
Falls die Data Science-Einheit aus Teams innerhalb einer Gruppe besteht, gibt es für TDSP-Mitarbeiter vier Rollen:

1. **Gruppenleiter**: Leitet die gesamte Data Science-Einheit in einem Unternehmen. Eine Data Science-Einheit kann aus mehreren Teams bestehen, von denen jedes an verschiedenen Data Science-Projekten in bestimmten Branchen arbeitet. Ein Gruppenleiter kann seine Aufgaben an einen Stellvertreter delegieren. Dadurch bleiben die der Rolle zugeordneten Aufgaben jedoch unverändert.
   
2. **Teamleiter**: Leitet ein Team in der Data Science-Einheit eines Unternehmens. Ein Team besteht aus mehreren Datenanalysten. In einer kleineren Data Science-Einheit kann es sich beim Gruppenleiter und Teamleiter um dieselbe Person handeln.
   
3. **Projektleiter**: Leitet die täglichen Aktivitäten der einzelnen Data Scientists eines bestimmten Data Science-Projekts.
   
4. **Einzelne Projektmitwirkende**: Data Scientists, Business Analysts, Data Engineers, Architects und andere Personen, die an einem Data Science-Projekt beteiligt sind.

> [!NOTE]
> Je nach Struktur und Größe eines Unternehmens kann eine einzelne Person mehrere Rollen innehaben, oder mehrere Personen können eine Rolle ausfüllen.

### <a name="tasks-to-be-completed-by-the-four-roles"></a>Aufgaben der vier Rollen

Im folgenden Diagramm sind die wichtigsten Aufgaben der einzelnen Rollen des Team Data Science-Prozesses dargestellt. Dieses Schema und die folgende ausführlichere Auflistung der Aufgaben für die einzelnen TDSP-Rollen dient Ihnen als Hilfe beim Auswählen des Tutorials, das zu Ihren Anforderungen passt.

![Überblick über die Rollen und Aufgaben](./media/roles-tasks/overview-tdsp-top-level.png)

## <a name="group-manager-tasks"></a>Aufgaben eines Gruppenleiters

Die folgenden Aufgaben werden vom Gruppenleiter oder einem festgelegten TDSP-Systemadministrator durchgeführt, um den TDSP einzuführen:

- Erstellt eine Azure DevOps-**Organisation** und anschließend darin ein Gruppenprojekt. 
- Erstellt im Azure DevOps-Gruppenprojekt ein **Projektvorlagenrepository** und führt dafür über das vom Microsoft TDSP-Team entwickelte Projektvorlagenrepository das Seeding durch. Das Microsoft TDSP-Projektvorlagenrepository enthält Folgendes:
  - Eine **standardisierte Verzeichnisstruktur** mit Verzeichnissen für Daten, Code und Dokumente.
  - Eine Gruppe von **standardisierten Dokumentvorlagen** zur Sicherstellung eines effizienten Data Science-Prozesses.
- Erstellt ein **Hilfsprogrammrepository** und führt dafür über das vom Microsoft TDSP-Team entwickelte Hilfsprogrammrepository das Seeding durch. Das TDSP-Hilfsprogrammrepository von Microsoft enthält einige nützliche Hilfsprogramme, um die Arbeit von Data Scientists effizienter zu machen. Das Microsoft-Hilfsprogrammrepository enthält Hilfsprogramme für die interaktive Datenuntersuchung, Analyse, Berichterstellung und Basismodellierung/-berichterstattung.
- Richtet die **Sicherheitskontrollrichtlinie** für das Organisationskonto ein.

Eine ausführliche Anleitung finden Sie unter [Aufgaben des Gruppenleiters eines Data Science-Teams](group-manager-tasks.md).

## <a name="team-lead-tasks"></a>Aufgaben des Teamleiters

Die folgenden Aufgaben werden vom Teamleiter oder einem festgelegten Projektadministrator durchgeführt, um den TDSP einzuführen:

- Erstellt in der Azure DevOps-Organisation der Gruppe ein **Teamprojekt**.
- Erstellt das **Projektvorlagenrepository** im Projekt und führt über das Projektvorlagenrepository der Gruppe, das vom Gruppenleiter oder einem Stellvertreter eingerichtet wurde, das Seeding durch.
- Erstellt das **Hilfsprogrammrepository für das Team**, führt über das Hilfsprogrammrepository der Gruppe das Seeding durch und fügt dem Repository teamspezifische Hilfsprogramme hinzu.
- Erstellt optional [Azure-Dateispeicher](https://azure.microsoft.com/services/storage/files/), in dem die hilfreichen Datenressourcen des Teams gespeichert werden. Die anderen Teammitglieder können diesen freigegebenen Cloud-Dateispeicher auf ihren Analysedesktops bereitstellen.
- Stellt optional den Azure-Dateispeicher auf der **DSVM** bereit und fügt die Datenressourcen des Teams hinzu.
- Richtet eine **Sicherheitskontrolle** ein, indem Teammitglieder hinzugefügt und die zugehörigen Berechtigungen konfiguriert werden.

Ausführliche Anleitungen finden Sie unter [Aufgaben des Teamleiters eines Data Science-Teams](team-lead-tasks.md).


## <a name="project-lead-tasks"></a>Aufgaben des Projektleiters

Der Projektleiter führt die folgenden Aufgaben aus, um den TDSP einzuführen:

- Erstellt im Teamprojekt ein **Projektrepository** und führt dafür über das Projektvorlagenrepository das Seeding durch.
- Erstellt optional **Azure-Dateispeicher**, in dem die Datenressourcen des Projekts gespeichert werden.
- Stellt optional den Azure-Dateispeicher auf der **DSVM** bereit und fügt darin Datenressourcen für das Projekt hinzu.
- Richtet eine **Sicherheitskontrolle** ein, indem Projektmitglieder hinzugefügt und die zugehörigen Berechtigungen konfiguriert werden.

Eine ausführliche Anleitung finden Sie unter [Aufgaben des Projektleiters eines Data Science-Teams](project-lead-tasks.md).

## <a name="project-individual-contributor-tasks"></a>Aufgaben einzelner Projektmitwirkender

Ein einzelner Projektmitwirkender, normalerweise ein Data Scientist, führt per TDSP die folgenden Aufgaben durch:

- Klont das **Projektrepository**, das vom Projektleiter eingerichtet wurde.
- Stellt optional den freigegebenen **Azure-Dateispeicher** für das Team und das Projekt auf der **Data Science Virtual Machine** (DSVM) bereit.
- Führt das Projekt aus.

Ausführliche Anleitungen für die Integration in ein Projekt finden Sie unter [Aufgaben für einen einzelnen Mitwirkenden am Team Data Science-Prozess](project-ic-tasks.md).

## <a name="data-science-project-execution-workflow"></a>Ausführungsworkflow des Data Science-Projekts

Indem sie die relevanten Tutorials durcharbeiten, können Data Scientists, Projektleiter und Teamleiter Arbeitselemente erstellen, um alle Aufgaben und Phasen eines Projekts vom Anfang bis zum Ende nachzuverfolgen. Mit Azure Repos wird die Zusammenarbeit der Datenanalysten gefördert. Außerdem wird sichergestellt, dass die während der Ausführung des Projekts entstandenen Artefakte mit Versionskontrolle ausgeführt und von allen Projektmitgliedern gemeinsam verwendet werden. Mit Azure DevOps können Sie Ihre Azure Boards-Arbeitselemente mit Ihren Azure Repos Repositorybranches verknüpfen und leicht nachverfolgen, welche Schritte für ein Arbeitselement ausgeführt wurden.

In der folgenden Abbildung ist der TDSP-Workflow für die Projektausführung dargestellt:

![Typischer Data Science-Projektworkflow](./media/roles-tasks/overview-project-execute.png)

Die Workflowschritte können nach drei Aktivitäten gruppiert werden:

- Projektleiter führen eine Sprintplanung durch
- Data Scientists entwickeln für Arbeitselemente Artefakte auf `git`-Branches
- Projektleiter oder andere Teammitglieder führen Code Reviews durch und mergen Arbeitsbranches mit dem primären Branch.

Eine ausführliche Anleitung zum Workflow der Projektausführung finden Sie unter [Agile Entwicklung von Data Science-Projekten](agile-development.md).

## <a name="tdsp-project-template-repository"></a>Repository mit TDSP-Projektvorlagen

Verwenden Sie das [Projektvorlagenrepository](https://github.com/Azure/Azure-TDSP-ProjectTemplate) des Microsoft TDSP-Teams, um eine effiziente Projektausführung und Zusammenarbeit zu unterstützen. Das Repository enthält eine standardisierte Verzeichnisstruktur und Dokumentvorlagen, die Sie für Ihre eigenen TDSP-Projekte verwenden können.

## <a name="next-steps"></a>Nächste Schritte

Unter den folgenden Links finden Sie ausführlichere Beschreibungen der Rollen und Aufgaben, die vom Team Data Science-Prozess definiert werden:

- [Group Manager tasks for a data science team (Aufgaben des Gruppenleiters eines Data Science-Teams)](group-manager-tasks.md)
- [Team Lead tasks for a data science team (Aufgaben des Teamleiters eines Data Science-Teams)](team-lead-tasks.md)
- [Project Lead tasks for a data science team (Aufgaben des Projektleiters eines Data Science-Teams)](project-lead-tasks.md)
- [Aufgaben für einen einzelnen Mitwirkenden am Team Data Science-Prozess](project-ic-tasks.md)
