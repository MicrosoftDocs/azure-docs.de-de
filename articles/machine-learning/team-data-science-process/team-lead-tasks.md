---
title: Aufgaben für die Teamleitung im Team Data Science-Prozessteam
description: Eine ausführliche exemplarische Vorgehensweise zu den Aufgaben für die Teamleitung in einem Team Data Science-Prozessteam.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: df7d2278487c1b098615a14562c498b9187c56eb
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000027"
---
# <a name="tasks-for-the-team-lead-on-a-team-data-science-process-team"></a>Aufgaben für die Teamleitung in einem Team Data Science-Prozessteam

In diesem Artikel werden die Aufgaben beschrieben, die ein *Teamleiter* für sein Data Science-Team erledigt. Ziel des Teamleiters ist es, eine auf Zusammenarbeit ausgelegte Teamumgebung einzurichten, die gemäß dem [Team Data Science Process](overview.md) (TDSP) vereinheitlicht ist. Der TDSP soll die Zusammenarbeit und die Lernprozesse im Team verbessern. 

Der TDSP ist eine flexible, iterative Data Science-Methodik zur effizienten Bereitstellung von Predictive Analytics-Lösungen und intelligenten Anwendungen. Der Prozess übernimmt die relevanten Teile der bewährten Methoden und Strukturen von Microsoft und der Branche.  Das Ziel ist die erfolgreiche Implementierung von Data Science-Initiativen und die vollständige Ausnutzung der Vorteile ihrer Analyseprogramme. Unter [Rollen und Aufgaben im Team Data Science-Prozess](roles-tasks.md) finden Sie eine Übersicht über die Mitarbeiterrollen und die dazugehörigen Aufgaben für ein Data Science-Team, das eine Vereinheitlichung gemäß dem TDSP anstrebt.

Ein Teamleiter verwaltet ein aus verschiedenen Datenanalysten bestehendes Team in der Data Science-Einheit eines Unternehmens. Je nach Größe und Struktur der Data Science-Einheit können der [Gruppenleiter](group-manager-tasks.md) und der Teamleiter dieselbe Person sein, oder sie könnten ihre Aufgaben an Stellvertreter delegieren. Die Aufgaben selbst ändern sich jedoch dabei nicht. 

Die folgende Abbildung zeigt den Workflow für die Aufgaben, die die Teamleitung zur Einrichtung einer Teamumgebung ausführt:

![Workflow für Teamleiteraufgaben](./media/team-lead-tasks/team-leads-1-creating-teams.png)

1. Erstellen Sie ein **Teamprojekt** in der Organisation der Gruppe in Azure DevOps. 
  
1. Benennen Sie das standardmäßige Teamrepository in **TeamUtilities** um.
  
1. Erstellen Sie ein neues **TeamTemplate**-Repository im Teamprojekt. 
  
1. Importieren Sie den Inhalt der Repositorys **GroupUtilities** und **GroupProjectTemplate** der Gruppe in die Repositorys **TeamUtilities** und **TeamTemplate**. 
  
1. Richten Sie eine **Sicherheitskontrolle** ein, indem Sie Teammitglieder hinzufügen und die zugehörigen Berechtigungen konfigurieren.
  
1. Erstellen Sie bei Bedarf Ressourcen für Teamdaten und Analysen:
   - Fügen Sie teamspezifische Hilfsprogramme zum **TeamUtilities**-Repository hinzu. 
   - Erstellen Sie einen **Azure-Dateispeicher** zum Speichern von Datenassets, die für das gesamte Team nützlich sein können. 
   - Stellen Sie den Azure-Dateispeicher auf der **Data Science Virtual Machine** (DSVM) des Teamleiters bereit, und fügen Sie ihr Datenassets hinzu.

Das folgende Tutorial führt ausführlich durch die entsprechenden Schritte.

> [!NOTE] 
> In diesem Artikel werden Azure DevOps und eine DSVM verwendet, um eine TDSP-Teamumgebung einzurichten, da auf diese Weise TDSP bei Microsoft implementiert wird. Wenn Ihr Team andere Codehosting- oder Entwicklungsplattformen verwendet, sind die Aufgaben des Teamleiters dieselben, aber die Art und Weise der Ausführung kann unterschiedlich sein.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Tutorial wird davon ausgegangen, dass die folgenden Ressourcen und Berechtigungen von Ihrem [Gruppenleiter](group-manager-tasks.md) eingerichtet wurden:

- Die Azure DevOps-**Organisation** für Ihre Dateneinheit
- Die Repositorys **GroupProjectTemplate** und **GroupUtilities**, die mit dem Inhalt der Repositorys des Microsoft TDSP-Teams **ProjectTemplate** und **Utilities** gefüllt sind.
- Berechtigungen für Ihr Organisationskonto, damit Sie Projekte und Repositorys für Ihr Team erstellen können

Damit Sie Repositorys klonen und deren Inhalt auf Ihrem lokalen Computer oder Ihrer DSVM ändern können, oder um Azure-Dateispeicher einzurichten und auf Ihrer DSVM einzubinden, benötigen Sie Folgendes:

- Ein Azure-Abonnement.
- Git muss auf Ihrem Computer installiert sein. Wenn Sie eine DSVM verwenden, ist Git vorinstalliert. Andernfalls helfen Ihnen die Informationen im [Anhang von Plattformen und Tools](platforms-and-tools.md#appendix) weiter.
- Wenn Sie eine DSVM verwenden möchten, wird die Windows- oder Linux-DSVM in Azure erstellt und konfiguriert. Weitere Informationen und Anweisungen finden Sie in der Dokumentation zur [Data Science Virtual Machine](../data-science-virtual-machine/index.yml).
- Für eine Windows DSVM muss [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) auf Ihrem Computer installiert sein. Scrollen Sie in der Datei *README.md* nach unten zum Abschnitt für **Download und Installation**, und wählen Sie dann das **neueste Installationsprogramm** aus. Laden Sie das Installationsprogramm mit der Erweiterung *EXE* von der Installationsprogrammseite herunter, und führen Sie es aus. 
- Für eine Linux-DSVM muss ein öffentlicher SSH-Schlüssel auf Ihrer DSVM eingerichtet und in Azure DevOps hinzugefügt werden. Weitere Informationen und Anweisungen finden Sie im [Anhang zu Plattformen und Tools](platforms-and-tools.md#appendix) im Abschnitt **Erstellen eines öffentlichen SSH-Schlüssels**. 

## <a name="create-a-team-project-and-repositories"></a>Erstellen eines Teamprojekts und von Repositorys

In diesem Abschnitt erstellen Sie die folgenden Ressourcen in der Azure DevOps-Organisation Ihrer Gruppe:

- Das Projekt **MyTeam** in Azure DevOps
- Das Repository **TeamTemplate**
- Das Repository **TeamUtilities**

Die in diesem Tutorial angegebenen Namen für die Repositorys und Verzeichnisse gehen davon aus, dass Sie ein eigenes Projekt für Ihr eigenes Team innerhalb einer größeren Data Science-Organisation erstellen möchten. Die gesamte Gruppe kann jedoch unter einem einzelnen Projekt arbeiten, das vom Gruppenleiter oder Organisationsadministrator erstellt wurde. Anschließend erstellen alle Data Science-Teams Repositorys unter diesem einzelnen Projekt. Dies Szenario kann gültig sein für:
- Eine kleine Data Science-Gruppe, die nicht in mehrere Data Science-Teams unterteilt ist. 
- Eine größere Data Science-Gruppe mit mehreren Data Science-Teams, die jedoch die teamübergreifende Zusammenarbeit mit Aktivitäten wie Sprintplanung auf der Gruppenebene optimieren möchten. 

Wenn Teams ihre teamspezifischen Repositorys unter einem einzelnen Gruppenprojekt verwenden, sollten die Teamleiter die Repositorys mit Namen wie *\<TeamName>Template* und *\<TeamName>Utilities* erstellen. Beispiel: *TeamATemplate* und *TeamAUtilities*. 

In jedem Fall müssen Teamleiter ihren Teammitgliedern mitteilen, welche Repositorys für Vorlagen und Hilfsprogramme sie einrichten und klonen müssen. Projektleiter müssen die [Aufgaben des Projektleiters eines Data Science-Teams](project-lead-tasks.md) ausführen, um Projektrepositorys zu erstellen, wobei es keine Rolle spielt, ob dies unter separaten Projekten oder einem einzelnen Projekt erfolgt. 

### <a name="create-the-myteam-project"></a>Erstellen des Projekts MyTeam

So erstellen Sie ein separates Projekt für Ihr Team

1. Wechseln Sie in Ihrem Webbrowser zur Startseite der Azure DevOps-Organisation Ihrer Gruppe unter der URL *https:\//\<server name>/\<organization name>* , und wählen Sie **Neues Projekt** aus. 
   
   ![„Neues Projekt“ auswählen](./media/team-lead-tasks/team-leads-2-create-new-team.png)
   
1. Geben Sie im Dialogfeld **Projekt erstellen** unter **Projektname** Ihren Teamnamen ein, z. B. *MyTeam*, und wählen Sie dann **Erweitert** aus. 
   
1. Wählen Sie unter **Versionskontrolle** die Option **Git** und dann unter **Arbeitselementprozess** die Option **Agile** aus. Klicken Sie anschließend auf **Erstellen**. 
   
   ![Projekt erstellen](./media/team-lead-tasks/team-leads-3-create-new-team-2.png)
   
Die Seite **Zusammenfassung** des Teamprojekts wird mit der Seiten-URL *https:\//\<server name>/\<organization name>/\<team name>* geöffnet.

### <a name="rename-the-myteam-default-repository-to-teamutilities"></a>Umbenennen des standardmäßigen Repositorys „MyTeam“ in „TeamUtilities“

1. Wählen Sie auf der Seite **Zusammenfassung** des **MyTeam**-Projekts unter **Mit welchem Dienst möchten Sie starten?** die Option **Repos** aus. 
   
   ![„Repos“ auswählen](./media/team-lead-tasks/team-leads-6-rename-team-project-repo.png)
   
1. Wählen Sie auf der **MyTeam**-Repositoryseite das **MyTeam**-Repository oben auf der Seite aus. Anschließend wählen Sie **Repositorys verwalten** aus der Dropdownliste aus. 
   
   ![„Repositorys verwalten“ auswählen](./media/team-lead-tasks/team-leads-7-rename-team-project-repo-2.png)
1. Wählen Sie auf der Seite **Projekteinstellungen** die Auslassungspunkte ( **...** ) neben **MyTeam** und dann **Repository umbenennen** aus. 
   
   ![„Repository umbenennen“ auswählen](./media/team-lead-tasks/team-leads-8-rename-team-project-repo-3.png)
   
1. Geben Sie im Popupfenster **MyTeam-Repository umbenennen** die Option *TeamUtilities* ein, und wählen Sie dann **Umbenennen** aus. 

### <a name="create-the-teamtemplate-repository"></a>Erstellen des TeamTemplate-Repositorys

1. Wählen Sie auf der Seite **Projekteinstellungen** die Option **Neues Repository** aus. 
   
   ![Auswählen von „Neues Repository“](./media/team-lead-tasks/team-leads-9-create-team-utilities.png)
   
   Alternativ können Sie auch **Repos** aus dem linken Navigationsbereich der Seite **Zusammenfassung** des **MyTeam**-Projekts auswählen, oben auf der Seite ein Repository und dann **Neues Repository** aus der Dropdownliste auswählen.
   
1. Stellen Sie im Dialogfeld **Neues Repository erstellen** sicher, dass **Git** unter **Typ** ausgewählt ist. Geben Sie *TeamTemplate* unter **Repository-Name** ein, und wählen Sie dann **Erstellen** aus.
   
   ![Erstellen eines Repositorys](./media/team-lead-tasks/team-leads-10-create-team-utilities-2.png)
   
1. Bestätigen Sie, dass Sie die beiden Repositorys **TeamUtilities** und **TeamTemplate** auf Ihrer Projekteinstellungsseite sehen können. 
   
   ![Zwei Teamrepositorys](./media/team-lead-tasks/team-leads-11-two-repo-in-team.png)

### <a name="import-the-contents-of-the-group-common-repositories"></a>Importieren der Inhalte der allgemeinen Gruppenrepositorys

So füllen Sie Ihre Teamrepositorys mit dem Inhalt der von Ihrem Gruppenleiter eingerichteten allgemeinen Gruppenrepositorys

1. Wählen Sie auf der Startseite des **MyTeam**-Projekts auf der linken Navigationsleiste **Repos** aus. Wenn Sie die Meldung erhalten, dass die Vorlage **MyTeam** nicht gefunden wurde, wählen Sie den Link in **Andernfalls zum standardmäßigen TeamTemplate-Repository navigieren** aus. 
   
   Das Standardrepository **TeamTemplate** wird geöffnet. 
   
1. Wählen Sie auf der Seite **TeamTemplate ist leer** die Option **Importieren**. 
   
   ![„Importieren“ auswählen](./media/team-lead-tasks/import-repo.png)
   
1. Wählen Sie im Dialogfeld **Git-Repository importieren** die Option **Git** als **Quelltyp** aus, und geben Sie die URL für Ihr allgemeines Gruppenvorlagenrepository unter **URL klonen** ein. Die URL ist *https:\//\<server name>/\<organization name>/_git/\<repository name>* . Beispiel: *https:\//dev.azure.com/DataScienceUnit/GroupCommon/_git/GroupProjectTemplate*. 
   
1. Wählen Sie **Importieren** aus. Der Inhalt Ihres Gruppenvorlagenrepositorys wird in Ihr Teamvorlagenrepository importiert. 
   
   ![Importieren des allgemeinen Gruppenvorlagenrepositorys](./media/team-lead-tasks/import-repo-2.png)
   
1. Wählen Sie oben auf der Seite **Repos** Ihres Projekts das Dropdownmenü und dann das Repository **TeamUtilities** aus.
   
1. Wiederholen Sie den Importvorgang, um den Inhalt des Utilities-Repositorys der allgemeinen Gruppe, z. B. *GroupUtilities*, in Ihr **TeamUtilities**-Repository zu importieren. 
   
Jedes der beiden Teamrepositorys enthält jetzt die Dateien aus dem entsprechenden allgemeinen Gruppenrepository. 

### <a name="customize-the-contents-of-the-team-repositories"></a>Anpassen der Inhalte der Teamrepositorys

Wenn Sie die Inhalte Ihrer Teamrepositorys an die speziellen Anforderungen Ihres Teams anpassen möchten, können Sie dies jetzt durchführen. Sie können Dateien ändern, die Verzeichnisstruktur anpassen oder Dateien und Ordner hinzufügen.

So können Sie Dateien oder Ordner direkt in Azure DevOps ändern, hochladen oder erstellen

1. Wählen Sie auf der Seite **Zusammenfassung** des Projekts **MyTeam** die Option **Repos** aus. 
   
1. Wählen Sie oben auf der Seite das Repository aus, das Sie anpassen möchten.

1. Navigieren Sie in der Verzeichnisstruktur des Repositorys zu dem Ordner oder der Datei, den bzw. die Sie ändern möchten. 
   
   - Wählen Sie zum Erstellen neuer Ordner oder Dateien den Pfeil neben **Neu** aus. 
     
     ![Erstellen einer neuen Datei](./media/team-lead-tasks/new-file.png)
     
   - Wählen Sie zum Hochladen von Dateien **Datei(en) hochladen** aus. 
     
     ![Hochladen von Dateien](./media/team-lead-tasks/upload-files.png)
     
   - Navigieren Sie zum Bearbeiten vorhandener Dateien zu der entsprechenden Datei, und wählen Sie dann **Bearbeiten** aus. 
     
     ![Bearbeiten einer Datei](./media/team-lead-tasks/edit-file.png)
     
1. Nachdem Sie Dateien hinzugefügt oder bearbeitet haben, wählen Sie **Commit** aus.
   
   ![Committen von Änderungen](./media/team-lead-tasks/commit.png)

Um mit Repositorys auf Ihrem lokalen Computer oder Ihrer DSVM zu arbeiten, kopieren oder *klonen* Sie zunächst die Repositorys auf Ihren lokalen Computer, und übertragen und verschieben Sie dann Ihre Änderungen in die freigegebenen Teamrepositorys, 

So klonen Sie Repositorys

1. Wählen Sie auf der Seite **Zusammenfassung** des **MyTeam**-Projekts die Option **Repos** und dann oben auf der Seite das zu klonende Repository aus.
   
1. Wählen Sie auf der Repositoryseite oben rechts **Klonen** aus.
   
1. Wählen Sie im Dialogfeld **Repository klonen** unter **Befehlszeile** die Option **HTTPS** für eine HTTP-Verbindung oder **SSH** für eine SSH-Verbindung aus, und kopieren Sie die Klon-URL in die Zwischenablage.
   
   ![Klon-URL kopieren](./media/team-lead-tasks/clone.png)
   
1. Erstellen Sie auf Ihrem lokalen Computer die folgenden Verzeichnisse:
   
   - Windows: **C:\GitRepos\MyTeam**
   - Für Linux: **$home/GitRepos/MyTeam** 
   
1. Wechseln Sie in das von Ihnen erstellte Verzeichnis.
   
1. Führen Sie in Git Bash den Befehl `git clone <clone URL>` aus, wobei \<clone URL> die URL ist, die Sie aus dem Dialogfeld zum **Klonen** kopiert haben.
   
   Verwenden Sie z. B. einen der folgenden Befehle, um das Repository **TeamUtilities** in das Verzeichnis *MyTeam* auf Ihrem lokalen Computer zu kopieren. 
   
   **HTTPS-Verbindung:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/MyTeam/_git/TeamUtilities
   ```
   
   **SSH-Verbindung:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/MyTeam/TeamUtilities
   ```

Nachdem Sie alle gewünschten Änderungen im lokalen Klon Ihres Repositorys vorgenommen haben, committen oder pushen Sie die Änderungen in die freigegebenen Teamrepositorys. 

Führen Sie die folgenden Git Bash-Befehle über Ihr lokales Verzeichnis **GitRepos\MyTeam\TeamTeamTemplate** oder **GitRepos\MyTeam\TeamUtilities** aus.

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

## <a name="add-team-members-and-configure-permissions"></a>Hinzufügen von Teammitgliedern und Konfigurieren von Berechtigungen

So fügen Sie dem Team Mitglieder hinzu

1. Wählen Sie in Azure DevOps auf der Startseite des **MyTeam**-Projekts die Option **Projekteinstellungen** im linken Navigationsbereich aus. 
   
1. Wählen Sie unter **Projekteinstellungen** die Option **Teams** und dann auf der Seite **Teams** die Option **MyTeam-Team** aus. 
   
   ![Konfigurieren von Teams](./media/team-lead-tasks/teams.png)
   
1. Wählen Sie auf der Seite **Teamprofil** die Option **Hinzufügen** aus.
   
   ![Hinzufügen zum MyTeam-Team](./media/team-lead-tasks/add-to-team.png)
   
1. Suchen Sie im Dialogfeld **Benutzer und Gruppen hinzufügen** nach Mitgliedern, die zur Gruppe hinzugefügt werden sollen, und wählen Sie diese aus. Anschließend wählen Sie **Änderungen speichern** aus. 
   
   ![Hinzufügen von Benutzern und Gruppen](./media/team-lead-tasks/add-users.png)
   

So konfigurieren Sie Berechtigungen für Teammitglieder

1. Wählen Sie im linken Navigationsbereich **Projekteinstellungen** die Option **Berechtigungen** aus. 
   
1. Wählen Sie auf der Seite **Berechtigungen** die Gruppe aus, zu der Sie Mitglieder hinzufügen möchten. 
   
1. Wählen Sie auf der Seite für diese Gruppe die Option **Mitglieder** und dann **Hinzufügen** aus. 
   
1. Suchen Sie im Popupfenster **Mitglieder einladen** nach Mitgliedern, die Sie der Gruppe hinzufügen möchten. Wählen Sie dann diese Mitglieder und anschließend **Speichern** aus. 
   
   ![Gewähren von Berechtigungen für Mitglieder](./media/team-lead-tasks/grant-permissions.png)

## <a name="create-team-data-and-analytics-resources"></a>Erstellen von Teamdaten und Analyseressourcen

Dieser Schritt ist optional, aber die Freigabe von Daten- und Analyseressourcen für Ihr gesamtes Team hat Leistungs- und Kostenvorteile. Teammitglieder können ihre Projekte auf den freigegebenen Ressourcen ausführen, Budgets schonen und effizienter zusammenarbeiten. Sie können Azure-Dateispeicher erstellen und auf Ihrer DSVM einbinden, um ihn für Teammitglieder freizugeben. 

Informationen zum Freigeben anderer Ressourcen für Ihr Team, z. B. Azure HDInsight Spark-Cluster, finden Sie unter [Plattformen und Tools](platforms-and-tools.md). In diesem Thema erhalten Sie eine Anleitung aus Data Science-Perspektive zum Auswählen von geeigneten Ressourcen für Ihre jeweiligen Anforderungen. Zudem finden Sie hier Links zu Produktseiten und anderen relevanten und hilfreichen Tutorials.

>[!NOTE]
> Um Datenübertragungen zwischen Rechenzentren zu vermeiden (dies kann ein sehr langsamer und kostspieliger Vorgang sein), müssen Sie sicherstellen, dass sich Ihre Azure-Ressourcengruppe, Ihr Speicherkonto und Ihre DSVM in derselben Azure-Region befinden. 

### <a name="create-azure-file-storage"></a>Erstellen von Azure-Dateispeicher

1. Führen Sie das folgende Skript aus, um Azure-Dateispeicher für Datenassets zu erstellen, die für Ihr gesamtes Team nützlich sind. Das Skript fordert Sie auf, Ihre Azure-Abonnementinformationen einzugeben, also halten Sie diese bereit. 

   - Führen Sie das Skript auf einem Windows-Computer über die PowerShell-Eingabeaufforderung aus:
     
     ```powershell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.ps1" -outfile "CreateFileShare.ps1"
     .\CreateFileShare.ps1
     ```
     
   - Führen Sie das Skript auf einem Linux-Computer in der Linux-Shell aus:
     
     ```shell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.sh"
     bash CreateFileShare.sh
     ```
   
1. Melden Sie sich nach entsprechender Aufforderung bei Ihrem Microsoft Azure-Konto an, und wählen Sie das zu verwendende Abonnement aus.
   
1. Wählen Sie das zu verwendende Speicherkonto aus, oder erstellen Sie unter dem ausgewählten Abonnement ein neues Speicherkonto: Sie können Kleinbuchstaben, Zahlen und Bindestriche für den Namen des Azure-Dateispeichers verwenden.
   
1. Um das Einbinden und Freigeben des Speichers zu vereinfachen, drücken Sie die EINGABETASTE oder geben Sie *J* ein, um die Informationen für den Azure-Dateispeicher in einer Textdatei in Ihrem aktuellen Verzeichnis zu speichern. Sie können diese Textdatei in Ihr **TeamTemplate**-Repository einchecken, idealerweise unter **Docs\DataDictionaries**, sodass alle Projekte in Ihrem Team darauf zugreifen können. Sie benötigen die Dateiinformationen auch, um Ihren Azure-Dateispeicher im nächsten Abschnitt auf Ihrer Azure DSVM einzubinden. 
   
### <a name="mount-azure-file-storage-on-your-local-machine-or-dsvm"></a>Einbinden von Azure-Dateispeicher auf Ihrem lokalen Computer oder Ihrer DSVM

1. Verwenden Sie das folgende Skript, um Ihren Azure-Dateispeicher auf Ihrem lokalen Computer oder Ihrer DSVM einzubinden.
   
   - Führen Sie das Skript auf einem Windows-Computer über die PowerShell-Eingabeaufforderung aus:
     
     ```powershell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.ps1" -outfile "AttachFileShare.ps1"
     .\AttachFileShare.ps1
     ```
     
   - Führen Sie das Skript auf einem Linux-Computer in der Linux-Shell aus:
     
     ```shell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.sh"
     bash AttachFileShare.sh
     ```
   
1. Drücken Sie die EINGABETASTE oder geben Sie *J* ein, um fortzufahren, wenn Sie im vorherigen Schritt eine Datei mit Informationen für den Azure-Dateispeicher gespeichert haben. Geben Sie den vollständigen Pfad und Namen der von Ihnen erstellten Datei ein. 
   
   Wenn Sie keine Datei mit Informationen zum Azure-Dateispeicher besitzen, geben Sie *N* ein und befolgen Sie die Anweisungen, um Ihr Abonnement, Ihr Azure-Speicherkonto und die Informationen für den Azure-Dateispeicher einzugeben.
   
1. Geben Sie den Namen eines lokalen oder TDSP-Laufwerks ein, auf dem die Dateifreigabe eingebunden werden soll. Auf dem Bildschirm wird eine Liste der vorhandenen Laufwerksnamen angezeigt. Geben Sie einen Laufwerksnamen an, der nicht bereits vorhanden ist.
   
1. Vergewissern Sie sich, dass der neue Datenträger und Speicher erfolgreich auf Ihrem Computer eingebunden wurde.

## <a name="next-steps"></a>Nächste Schritte

Es folgen Links zu ausführlichen Beschreibungen der anderen Rollen und Aufgaben, die vom Team Data Science-Prozess definiert wurden:

- [Group Manager tasks for a data science team (Aufgaben des Gruppenleiters eines Data Science-Teams)](group-manager-tasks.md)
- [Project Lead tasks for a data science team (Aufgaben des Projektleiters eines Data Science-Teams)](project-lead-tasks.md)
- [Aufgaben für einen einzelnen Mitwirkenden am Team Data Science-Prozess](project-ic-tasks.md)