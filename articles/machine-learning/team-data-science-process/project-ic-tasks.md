---
title: Aufgaben für einen einzelnen Mitwirkenden am Team Data Science-Prozess
description: Ausführliche exemplarische Vorgehensweise zu den Aufgaben eines einzelnen Mitwirkenden an einem Data Science-Teamprojekt.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4ecb5fef9c9b14bde72de29a45e29d7e16131bd1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000996"
---
# <a name="tasks-for-an-individual-contributor-in-the-team-data-science-process"></a>Aufgaben für einen einzelnen Mitwirkenden am Team Data Science-Prozess

In diesem Thema werden die Aufgaben beschrieben, die von einem *einzelnen Mitwirkenden* zum Einrichten eines Projekts im [Team Data Science-Prozess](overview.md) (TDSP) abgeschlossen werden. Ziel ist es, in einer auf Zusammenarbeit ausgerichteten Teamumgebung zu arbeiten, die gemäß dem TDSP vereinheitlicht ist. Der TDSP soll die Zusammenarbeit und die Lernprozesse im Team verbessern. Unter [Rollen und Aufgaben im Team Data Science-Prozess](roles-tasks.md) finden Sie eine Übersicht über die wichtigsten Mitarbeiterrollen und die zugehörigen Aufgaben für ein Data Science-Team, das eine Vereinheitlichung gemäß dem TDSP anstrebt.

Das folgende Diagramm zeigt die Aufgaben, die einzelne Mitwirkende (Data Scientists) zum Einrichten ihrer Teamumgebung ausführen. Informationen zum Ausführen eines Data Science-Projekts gemäß dem TDSP finden Sie unter [Ausführung von Data Science-Projekten](./agile-development.md). 

![Aufgaben einzelner Mitwirkender](./media/project-ic-tasks/project-ic-1-tdsp-data-scientist.png)

- **ProjectRepository** ist das Repository, das Ihr Projektteam zum gemeinsamen Nutzen von Projektvorlagen und Ressourcen verwaltet.
- **TeamUtilities** ist das Repository, das Ihr Team speziell für sich selbst verwaltet. 
- **GroupUtilities** ist das Repository, in dem Ihre Gruppe nützliche Hilfsprogramme für die gesamte Gruppe bereitstellt. 

> [!NOTE] 
> In diesem Artikel werden Azure Repos und eine Data Science Virtual Machine (DSVM) verwendet, um eine TDSP-Umgebung einzurichten, da auf diese Weise TDSP bei Microsoft implementiert wird. Wenn Ihr Team andere Codehosting- oder Entwicklungsplattformen verwendet, sind die Aufgaben der einzelnen Mitwirkenden dieselben, aber die Ausführung kann unterschiedlich ausfallen.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Tutorial wird davon ausgegangen, dass die folgenden Ressourcen und Berechtigungen von Ihrem [Gruppenleiter](group-manager-tasks.md), dem [Teamleiter](team-lead-tasks.md) und dem [Projektleiter](project-lead-tasks.md) eingerichtet wurden:

- Die Azure DevOps-**Organisation** für Ihre Data Science-Einheit
- Ein **Projektrepository**, das von Ihrem Projektleiter für die gemeinsame Nutzung von Projektvorlagen und Ressourcen eingerichtet wurde
- Die Repositorys **GroupUtilities** und **TeamUtilities**, die ggf. vom Gruppenleiter und Teamleiter eingerichtet werden
- Azure-**Dateispeicher**, der ggf. für freigegebene Ressourcen für Ihr Team oder Projekt eingerichtet wurde
- **Berechtigungen** für das Klonen aus dem Projektrepository und das Pushen zurück in dieses 

Damit Sie Repositorys klonen und Inhalte auf Ihrem lokalen Computer oder Ihrer DSVM ändern können oder um Azure-Dateispeicher auf Ihrer DSVM einzubinden, müssen Sie die folgende Prüfliste beachten:

- Ein Azure-Abonnement.
- Git muss auf Ihrem Computer installiert sein. Wenn Sie eine DSVM verwenden, ist Git vorinstalliert. Andernfalls helfen Ihnen die Informationen im [Anhang von Plattformen und Tools](platforms-and-tools.md#appendix) weiter.
- Wenn Sie eine DSVM verwenden möchten, wird die Windows- oder Linux-DSVM in Azure erstellt und konfiguriert. Weitere Informationen und Anweisungen finden Sie in der Dokumentation zur [Data Science Virtual Machine](../data-science-virtual-machine/index.yml).
- Für eine Windows DSVM muss [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) auf Ihrem Computer installiert sein. Scrollen Sie in der Datei *README.md* nach unten zum Abschnitt für **Download und Installation**, und wählen Sie dann das **neueste Installationsprogramm** aus. Laden Sie das Installationsprogramm mit der Erweiterung *EXE* von der Installationsprogrammseite herunter, und führen Sie es aus. 
- Für eine Linux-DSVM muss ein öffentlicher SSH-Schlüssel auf Ihrer DSVM eingerichtet und in Azure DevOps hinzugefügt werden. Weitere Informationen und Anweisungen finden Sie im [Anhang zu Plattformen und Tools](platforms-and-tools.md#appendix) im Abschnitt **Erstellen eines öffentlichen SSH-Schlüssels**. 
- Die Azure-Dateispeicherinformationen für alle Azure-Dateispeicher, die Sie auf Ihrer DSVM einbinden müssen. 

## <a name="clone-repositories"></a>Klonen von Repositorys

Um lokal mit Repositorys zu arbeiten und Ihre Änderungen in die freigegebenen Team- und Projektrepositorys zu übertragen, kopieren oder *klonen* Sie zunächst die Repositorys auf Ihren lokalen Computer. 

1. Wechseln Sie in Azure DevOps zur Übersichtsseite für das Projekt Ihres Teams unter *https:\//\<server name>/\<organization name>/\<team name>* , z. B. **https:\//dev.azure.com/DataScienceUnit/MyTeam**.
   
1. Wählen Sie im linken Navigationsbereich **Repos** und dann oben auf der Seite das Repository aus, das Sie klonen möchten.
   
1. Wählen Sie auf der Repositoryseite oben rechts **Klonen** aus.
   
1. Wählen Sie im Dialogfeld **Repository klonen** die Option **HTTPS** für eine HTTP-Verbindung oder **SSH** für eine SSH-Verbindung aus, und kopieren Sie die Klon-URL unter **Befehlszeile** in die Zwischenablage.
   
   ![Repository klonen](./media/project-ic-tasks/clone.png)
   
1. Erstellen Sie auf Ihrem lokalen Computer oder Ihrer DSVM die folgenden Verzeichnisse:
   
   - Windows: **C:\GitRepos**
   - Für Linux: **$home/GitRepos**
   
1. Wechseln Sie in das von Ihnen erstellte Verzeichnis.
   
1. Führen Sie in Git Bash den Befehl `git clone <clone URL>` für jedes zu klonende Repository aus. 
   
   Beispielsweise klont der folgende Befehl das Repository **TeamUtilities** in das Verzeichnis *MyTeam* auf Ihrem lokalen Computer. 
   
   **HTTPS-Verbindung:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/MyTeam/_git/TeamUtilities
   ```
   
   **SSH-Verbindung:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/MyTeam/TeamUtilities
   ```
   
1. Vergewissern Sie sich, dass die Ordner für die geklonten Repositorys in Ihrem lokalen Projektverzeichnis angezeigt werden.
   
   ![Drei lokale Repositoryordner](./media/project-ic-tasks/project-ic-5-three-repo-cloned-to-ic-linux.png)

## <a name="mount-azure-file-storage-to-your-dsvm"></a>Einbinden des Azure-Dateispeichers auf Ihrer DSVM

Wenn Ihr Team oder Projekt über freigegebene Ressourcen in Azure-Dateispeicher verfügt, binden Sie den Dateispeicher auf dem lokalen Computer oder der DSVM ein. Befolgen Sie die Anweisungen unter [Einbinden von Azure-Dateispeicher auf Ihrem lokalen Computer oder Ihrer DSVM](team-lead-tasks.md#mount-azure-file-storage-on-your-local-machine-or-dsvm).

## <a name="next-steps"></a>Nächste Schritte

Es folgen Links zu ausführlichen Beschreibungen der anderen Rollen und Aufgaben, die vom Team Data Science-Prozess definiert wurden:

- [Group Manager tasks for a data science team (Aufgaben des Gruppenleiters eines Data Science-Teams)](group-manager-tasks.md)
- [Team Lead tasks for a data science team (Aufgaben des Teamleiters eines Data Science-Teams)](team-lead-tasks.md)
- [Project Lead tasks for a data science team (Aufgaben des Projektleiters eines Data Science-Teams)](project-lead-tasks.md)