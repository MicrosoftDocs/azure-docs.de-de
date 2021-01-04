---
title: Aufgaben eines Gruppenleiters im Team Data Science-Prozess
description: Befolgen Sie diese ausführliche exemplarische Vorgehensweise zu den Aufgaben des Gruppenleiters, die dieser für ein Data Science-Teamprojekt durchführt.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/17/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 7ab6d6511d1e2cec82b321003c9d663249ddcf49
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94740225"
---
# <a name="team-data-science-process-group-manager-tasks"></a>Aufgaben eines Gruppenleiters im Team Data Science-Prozess

In diesem Artikel werden die Aufgaben beschrieben, die ein *Gruppenleiter* für eine Data Science-Organisation erledigt. Der Gruppenleiter verwaltet die gesamte Data Science-Einheit in einem Unternehmen. Eine Data Science-Einheit kann aus mehreren Teams bestehen, von denen jedes an vielen Data Science-Projekten in bestimmten Branchen arbeitet. Das Ziel des Gruppenleiters besteht darin, eine auf Zusammenarbeit ausgelegte Gruppenumgebung zu schaffen, die gemäß dem [Team Data Science-Prozess (TDSP)](overview.md) vereinheitlicht ist. Unter [Rollen und Aufgaben im Team Data Science-Prozess](roles-tasks.md) finden Sie eine Übersicht über alle Mitarbeiterrollen und die dazugehörigen Aufgaben für ein Data Science-Team, das eine Vereinheitlichung gemäß dem TDSP anstrebt.

Das folgende Diagramm zeigt die sechs wichtigsten Einrichtungsaufgaben des Gruppenleiters. Gruppenleiter können ihre Aufgaben an Stellvertreter delegieren, aber die mit der Rolle verbundenen Aufgaben bleiben unverändert.

![Aufgaben eines Gruppenleiters](./media/group-manager-tasks/tdsp-group-manager.png)

1. Richten Sie eine **Azure DevOps-Organisation** für die Gruppe ein.
2. Erstellen Sie das standardmäßige **GroupCommon-Projekt** in der Azure DevOps-Organisation.
3. Erstellen Sie das **GroupProjectTemplate**-Repository in Azure Repos.
4. Erstellen Sie das **GroupUtilities**-Repository in Azure Repos.
5. Importieren Sie die Inhalte der Repositorys **ProjectTemplate** und **Utilities** des Microsoft TDSP-Teams in die gemeinsamen Repositorys der Gruppe.
6. Richten Sie **Mitgliedschaft** und **Berechtigungen** ein, damit Teammitglieder auf die Gruppe zugreifen können.

Das folgende Tutorial führt ausführlich durch die entsprechenden Schritte. 

> [!NOTE] 
> In diesem Artikel wird Azure DevOps verwendet, um eine TDSP-Gruppenumgebung einzurichten, da auf diese Weise TDSP bei Microsoft implementiert wird. Wenn Ihre Gruppe andere Codehosting- oder Entwicklungsplattformen verwendet, sind die Aufgaben des Gruppenmanagers dieselben, aber die Art und Weise der Ausführung kann unterschiedlich sein.

## <a name="create-an-organization-and-project-in-azure-devops"></a>Erstellen einer Organisation und eines Projekts in Azure DevOps

1. Wechseln Sie zu [visualstudio.microsoft.com](https://visualstudio.microsoft.com), wählen Sie oben rechts **Anmelden** aus, und melden Sie sich bei Ihrem Microsoft-Konto an. 
   
   ![Anmelden bei Ihrem Azure-Konto](./media/group-manager-tasks/signinvs.png)
   
   Wenn Sie kein Microsoft-Konto besitzen, wählen Sie **Jetzt anmelden** aus, erstellen Sie ein Microsoft-Konto, und melden Sie sich dann mit diesem Konto an. Wenn Ihr Unternehmen über ein Visual Studio-Abonnement verfügt, melden Sie sich mit den Anmeldeinformationen für dieses Abonnement an.
   
1. Nachdem Sie sich angemeldet haben, wählen Sie oben rechts auf der Azure Devops-Seite **Neue Organisation erstellen** aus.
   
   ![Erstellen einer neuen Organisation](./media/group-manager-tasks/create-organization.png)
   
1. Wenn Sie aufgefordert werden, den Vertragsbedingungen, den Datenschutzbestimmungen und den Verhaltensregeln zuzustimmen, wählen Sie **Weiter** aus.
   
1. Benennen Sie Ihre Azure DevOps-Organisation im Anmeldedialogfeld, und akzeptieren Sie die Zuordnung der Hostregion, oder wählen Sie eine andere Region aus. Klicken Sie anschließend auf **Weiter**. 

1. Geben Sie unter **Erstellen Sie als ersten Schritt ein Projekt** den Wert *GroupCommon* ein, und wählen Sie dann **Projekt erstellen** aus. 
   
   ![Projekt erstellen](./media/group-manager-tasks/create-project.png)

Die Seite **Zusammenfassung** des Projekts **GroupCommon** wird geöffnet. Die Seiten-URL lautet *https:\//\<servername>/\<organization-name>/GroupCommon*.

![Seite „Zusammenfassung“ für das Projekt](./media/group-manager-tasks/project-summary.png)

## <a name="set-up-the-group-common-repositories"></a>Einrichten der allgemeinen Gruppenrepositorys

Azure Repos hostet die folgenden Arten von Repositorys für Ihre Gruppe:

- **Allgemeine Gruppenrepositorys**: Universelle Repositorys, die von mehreren Teams innerhalb einer Data Science-Einheit für viele Data Science-Projekte übernommen werden können. 
- **Teamrepositorys**:  Repositorys für bestimmte Teams in einer Data Science-Einheit. Diese Repositorys sind spezifisch für die Anforderungen eines Teams und können für mehrere Projekte innerhalb dieses Teams verwendet werden. Sie sind aber nicht ausreichend universell, um von mehreren Teams innerhalb einer Data Science-Einheit verwendet zu werden.
- **Projektrepositorys**: Repositorys für bestimmte Projekte. Solche Repositorys sind möglicherweise nicht ausreichend universell für mehrere Projekte innerhalb eines Teams oder für andere Teams in einer Data Science-Einheit.

Gehen Sie wie folgt vor, um die allgemeinen Gruppenrepositorys in Ihrem Projekt einzurichten: 
- Benennen Sie das Standardrepository **GroupCommon** in **GroupProjectTemplate** um.
- Erstellen Sie ein neues **GroupUtilities**-Repository.

### <a name="rename-the-default-project-repository-to-groupprojecttemplate"></a>Benennen Sie das Standardprojektrepository in „GroupProjectTemplate“ um.

So benennen Sie das standardmäßige **GroupCommon**-Projektrepository in **GroupProjectTemplate** um

1. Wählen Sie auf der Seite **Zusammenfassung** des Projekts **GroupCommon** die Option **Repos** aus. Diese Aktion führt Sie zum standardmäßigen **GroupCommon**-Repository des GroupCommon-Projekts, das derzeit leer ist.
   
1. Wählen Sie oben auf der Seite den Pfeil neben **GroupCommon** und dann **Repositorys verwalten** aus.
   
   ![Verwalten von Repositorys](./media/group-manager-tasks/rename-groupcommon-repo-3.png)
   
1. Wählen Sie auf der Seite **Projekteinstellungen** die Auslassungspunkte ( **...** ) neben **GroupCommon** und dann **Repository umbenennen** aus. 
   
   ![Auslassungspunkte (...) und dann „Repository umbenennen“ auswählen](./media/group-manager-tasks/rename-groupcommon-repo-4.png)
   
1. Geben Sie im Popupfenster **GroupCommon-Repository umbenennen** die Zeichenfolge *GroupProjectTemplate* ein, und wählen Sie dann **Umbenennen** aus. 
   
   ![Repository umbenennen](./media/group-manager-tasks/rename-groupcommon-repo-6.png)

### <a name="create-the-grouputilities-repository"></a>Erstellen des GroupUtilities-Repositorys

So erstellen Sie das **GroupUtilities**-Repository

1. Wählen Sie auf der Seite **Zusammenfassung** des Projekts **GroupCommon** die Option **Repos** aus. 
   
1. Wählen Sie oben auf der Seite den Pfeil neben **GroupProjectTemplate** und dann **Neues Repository** aus.
   
   ![Auswählen von „Neues Repository“](./media/group-manager-tasks/create-grouputilities-repo-1.png)
   
1. Wählen Sie im Dialogfeld **Neues Repository erstellen** die Option **Git** als **Typ** aus, geben Sie *GroupUtilities* als **Repositoryname** ein, und wählen Sie anschließend **Erstellen** aus.
   
   ![Erstellen des GroupUtilities-Repositorys](./media/group-manager-tasks/create-grouputilities-repo-2.png)
   
1. Wählen Sie auf der Seite **Projekteinstellungen** unter **Repos** auf der linken Navigationsleiste **Repositorys** aus, um die beiden Gruppenrepositorys anzuzeigen: **GroupProjectTemplate** und **GroupUtilities**.
   
   ![Zwei Gruppenrepositorys](./media/group-manager-tasks/two-repositories.png)

## <a name="import-the-microsoft-tdsp-team-repositories"></a>Importieren der Microsoft TDSP-Teamrepositorys

In diesem Teil des Tutorials importieren Sie den Inhalt der vom Microsoft TDSP-Team verwalteten Repositorys **ProjectTemplate** und **Utilities** in Ihre Repositorys **GroupProjectTemplate** und **GroupUtilities**. 

So importieren Sie die TDSP-Teamrepositorys

1. Wählen Sie auf der Startseite des **GroupCommon**-Projekts auf der linken Navigationsleiste **Repos** aus. Das standardmäßige **GroupProjectTemplate**-Repository wird geöffnet. 
   
1. Wählen Sie auf der Seite **GroupProjectTemplate ist leer** die Option **Importieren** aus. 
   
   ![„Importieren“ auswählen](./media/group-manager-tasks/import-repo.png)
   
1. Wählen Sie im Dialogfeld **Git-Repository importieren** die Option **Git** als **Quelltyp** aus, und geben Sie *https:\//github.com/Azure/Azure-TDSP-ProjectTemplate.git* für die **Klon-URL** ein. Wählen Sie dann **Importieren** aus. Die Inhalte des Microsoft TDSP-Teamrepositorys „ProjectTemplate“ werden in Ihr „GroupProjectTemplate“-Repository importiert. 
   
   ![Importieren des Microsoft TDSP-Teamrepositorys](./media/group-manager-tasks/import-repo-2.png)
   
1. Wählen Sie oben auf der Seite **Repos** das Dropdownmenü und dann das Repository **GroupUtilities** aus.
   
Jedes Ihrer beiden Gruppenrepositorys enthält nun alle Dateien, mit Ausnahme der Dateien im Verzeichnis *.git*, aus dem entsprechenden Repository des Microsoft TDSP-Teams. 

## <a name="customize-the-contents-of-the-group-repositories"></a>Anpassen der Inhalte der Gruppenrepositorys

Wenn Sie die Inhalte Ihrer Gruppenrepositorys an die speziellen Anforderungen Ihrer Gruppe anpassen möchten, können Sie dies jetzt durchführen. Sie können die Dateien ändern, die Verzeichnisstruktur anpassen oder Dateien hinzufügen, die von Ihrer Gruppe entwickelt wurden oder die für Ihre Gruppe hilfreich sind.

### <a name="make-changes-in-azure-repos"></a>Vornehmen von Änderungen in Azure Repos

So passen Sie die Repositoryinhalte an

1. Wählen Sie auf der Seite **Zusammenfassung** des Projekts **GroupCommon** die Option **Repos** aus. 
   
1. Wählen Sie oben auf der Seite das Repository aus, das Sie anpassen möchten.

1. Navigieren Sie in der Verzeichnisstruktur des Repositorys zu dem Ordner oder der Datei, den bzw. die Sie ändern möchten. 
   
   - Wählen Sie zum Erstellen neuer Ordner oder Dateien den Pfeil neben **Neu** aus. 
     
     ![Erstellen einer neuen Datei](./media/group-manager-tasks/new-file.png)
     
   - Wählen Sie zum Hochladen von Dateien **Datei(en) hochladen** aus. 
     
     ![Hochladen von Dateien](./media/group-manager-tasks/upload-files.png)
     
   - Navigieren Sie zum Bearbeiten vorhandener Dateien zu der entsprechenden Datei, und wählen Sie dann **Bearbeiten** aus. 
     
     ![Bearbeiten einer Datei](./media/group-manager-tasks/edit-file.png)
     
1. Nachdem Sie Dateien hinzugefügt oder bearbeitet haben, wählen Sie **Commit** aus.
   
   ![Committen von Änderungen](./media/group-manager-tasks/commit.png)

### <a name="make-changes-using-your-local-machine-or-dsvm"></a>Vornehmen von Änderungen mithilfe des lokalen Computers oder der DSVM

Wenn Sie Änderungen mit Ihrem lokalen Computer oder der DSVM vornehmen und die Änderungen in die Gruppenrepositorys übertragen möchten, stellen Sie sicher, dass Sie über die Voraussetzungen für die Arbeit mit Git und DSVMs verfügen:

- Ein Azure-Abonnement, wenn Sie eine DSVM erstellen möchten.
- Git muss auf Ihrem Computer installiert sein. Wenn Sie eine DSVM verwenden, ist Git vorinstalliert. Andernfalls helfen Ihnen die Informationen im [Anhang von Plattformen und Tools](platforms-and-tools.md#appendix) weiter.
- Wenn Sie eine DSVM verwenden möchten, wird die Windows- oder Linux-DSVM in Azure erstellt und konfiguriert. Weitere Informationen und Anweisungen finden Sie in der Dokumentation zur [Data Science Virtual Machine](../data-science-virtual-machine/index.yml).
- Für eine Windows DSVM muss [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) auf Ihrem Computer installiert sein. Scrollen Sie in der Datei *README.md* nach unten zum Abschnitt für **Download und Installation**, und wählen Sie dann das **neueste Installationsprogramm** aus. Laden Sie das Installationsprogramm mit der Erweiterung *EXE* von der Installationsprogrammseite herunter, und führen Sie es aus. 
- Für eine Linux-DSVM muss ein öffentlicher SSH-Schlüssel auf Ihrer DSVM eingerichtet und in Azure DevOps hinzugefügt werden. Weitere Informationen und Anweisungen finden Sie im [Anhang zu Plattformen und Tools](platforms-and-tools.md#appendix) im Abschnitt **Erstellen eines öffentlichen SSH-Schlüssels**. 

Kopieren oder *klonen* Sie zunächst das Repository auf Ihren lokalen Computer. 
   
1. Wählen Sie auf der Seite **Zusammenfassung** des **GroupCommon**-Projekts die Option **Repos** und dann oben auf der Seite das zu klonende Repository aus.
   
1. Wählen Sie auf der Repositoryseite oben rechts **Klonen** aus.
   
1. Wählen Sie im Dialogfeld **Repository klonen** die Option **HTTPS** für eine HTTP-Verbindung oder **SSH** für eine SSH-Verbindung aus, und kopieren Sie die Klon-URL unter **Befehlszeile** in die Zwischenablage.
   
   ![Repository klonen](./media/group-manager-tasks/clone.png)
   
1. Erstellen Sie auf Ihrem lokalen Computer die folgenden Verzeichnisse:
   
   - Windows: **C:\GitRepos\GroupCommon**
   - Unter Linux: **$/GitRepos/GroupCommon** in Ihrem Basisverzeichnis. 
   
1. Wechseln Sie in das von Ihnen erstellte Verzeichnis.
   
1. Führen Sie in Git Bash den Befehl `git clone <clone URL>.` aus.
   
   Jeder der beiden folgenden Befehle klont z. B. das Repository **GroupUtilities** in das Verzeichnis *GroupCommon* auf Ihrem lokalen Computer. 
   
   **HTTPS-Verbindung:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/GroupCommon/_git/GroupUtilities
   ```
   
   **SSH-Verbindung:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/GroupCommon/GroupUtilities
   ```

Nachdem Sie alle gewünschten Änderungen im lokalen Klon Ihres Repositorys vorgenommen haben, können Sie die Änderungen per Push in die gemeinsamen Repositorys der freigegebenen Gruppe übertragen. 

Führen Sie die folgenden Git Bash-Befehle in Ihrem lokalen **GroupProjectTemplate**- oder **GroupUtilities**-Verzeichnis aus.

```bash
git add .
git commit -m "push from local"
git push
```

> [!NOTE]
> Wenn Sie erstmalig einen Commit in ein Git-Repository ausführen, müssen Sie möglicherweise die globalen Parameter *user.name* und *user.email* konfigurieren, bevor Sie den Befehl `git commit` ausführen. Führen Sie die folgenden beiden Befehle aus:
> 
> `git config --global user.name <your name>`
> 
> `git config --global user.email <your email address>`
> 
> Wenn Sie mit mehreren Git-Repositorys ein Commit ausführen, verwenden Sie denselben Namen und dieselbe E-Mail-Adresse für alle Repositorys. Die Verwendung desselben Namens und derselben E-Mail-Adresse ist praktisch, wenn Sie Power BI-Dashboards erstellen, um Ihre Git-Aktivitäten in mehreren Repositorys nachzuverfolgen.

## <a name="add-group-members-and-configure-permissions"></a>Hinzufügen von Gruppenmitgliedern und Konfigurieren von Berechtigungen

So fügen Sie der Gruppe Mitglieder hinzu

1. Wählen Sie in Azure DevOps auf der Startseite des **GroupCommon**-Projekts die Option **Projekteinstellungen** im linken Navigationsbereich aus. 
   
1. Wählen Sie unter **Projekteinstellungen** die Option **Teams** und dann auf der Seite **Teams** die Option **GroupCommon-Team** aus. 
   
   ![Konfigurieren von Teams](./media/group-manager-tasks/teams.png)
   
1. Wählen Sie auf der Seite **Teamprofil** die Option **Hinzufügen** aus.
   
   ![Hinzufügen zum GroupCommon-Team](./media/group-manager-tasks/add-to-team.png)
   
1. Suchen Sie im Dialogfeld **Benutzer und Gruppen hinzufügen** nach Mitgliedern, die zur Gruppe hinzugefügt werden sollen, und wählen Sie diese aus. Anschließend wählen Sie **Änderungen speichern** aus. 
   
   ![Hinzufügen von Benutzern und Gruppen](./media/group-manager-tasks/add-users.png)
   

So konfigurieren Sie Berechtigungen für Mitglieder

1. Wählen Sie im linken Navigationsbereich **Projekteinstellungen** die Option **Berechtigungen** aus. 
   
1. Wählen Sie auf der Seite **Berechtigungen** die Gruppe aus, zu der Sie Mitglieder hinzufügen möchten. 
   
1. Wählen Sie auf der Seite für diese Gruppe die Option **Mitglieder** und dann **Hinzufügen** aus. 
   
1. Suchen Sie im Popupfenster **Mitglieder einladen** nach Mitgliedern, die Sie der Gruppe hinzufügen möchten. Wählen Sie dann diese Mitglieder und anschließend **Speichern** aus. 
   
   ![Gewähren von Berechtigungen für Mitglieder](./media/group-manager-tasks/grant-permissions.png)

## <a name="next-steps"></a>Nächste Schritte

Es folgen Links zu ausführlichen Beschreibungen der anderen Rollen und Aufgaben im Team Data Science-Prozess:

- [Team Lead tasks for a data science team (Aufgaben des Teamleiters eines Data Science-Teams)](team-lead-tasks.md)
- [Project Lead tasks for a data science team (Aufgaben des Projektleiters eines Data Science-Teams)](project-lead-tasks.md)
- [Aufgaben für einen einzelnen Mitwirkenden am Team Data Science-Prozess](project-ic-tasks.md)
