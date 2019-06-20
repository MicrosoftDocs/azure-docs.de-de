---
title: Aufgaben des Projektleiters im Team Data Science-Prozess
description: Eine Übersicht über die Aufgaben, die ein Projektleiter für einen Team Data Science-Prozess erfüllen muss.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 00b1b58a39724951f2d5e4e688df8eb178654bbb
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65952840"
---
# <a name="tasks-for-the-project-lead-in-the-team-data-science-process"></a>Aufgaben des Projektleiters im Team Data Science-Prozess

Dieses Tutorial beschreibt die Aufgaben, die ein Projektleiter für sein Projektteam ausführen muss. Ziel ist es, eine auf Zusammenarbeit ausgelegte Teamumgebung einzurichten, die gemäß dem [Team Data Science Process](overview.md) (TDSP) vereinheitlicht ist. TDSP ist ein von Microsoft entwickeltes Framework, das eine strukturierte Abfolge von Aktivitäten bereitstellt, die cloudbasierte, prädiktive Analyselösungen effizient ausführt. Unter [Team Data Science Process: Roles and tasks](roles-tasks.md) (Team Data Science-Prozess: Rollen und Aufgaben) finden Sie eine Übersicht über die wichtigsten Mitarbeiterrollen und zugeordneten Aufgaben für ein Data Science-Team, das eine Vereinheitlichung gemäß diesem Prozess anstrebt.

Ein **Projektleiter** verwaltet die täglichen Aktivitäten der einzelnen Datenanalysten auf einem bestimmten Data Science-Projekt. Der Workflow für die Aufgaben, die von Projektleitern zum Einrichten dieser Umgebung ausgeführt werden, sind in der folgenden Abbildung dargestellt:

![1](./media/project-lead-tasks/project-leads-1-tdsp-creating-projects.png)

Dieses Thema enthält derzeit die Aufgaben 1,2 und 6 dieses Workflows für Projektleiter.

> [!NOTE]
> In den folgenden Anweisungen werden die erforderlichen Schritte zum Einrichten einer TDSP-Teamumgebung für ein Projekt per Azure DevOps beschrieben. Es wird angegeben, wie diese Aufgaben mit Azure DevOps erledigt werden können, da bei Microsoft der TDSP auf diese Weise implementiert wird. Wenn für Ihre Gruppe eine andere Plattform zum Hosten des Codes verwendet wird, ändern sich die vom Teamleiter auszuführenden Aufgaben in der Regel nicht. Sie müssen jedoch auf andere Weise ausgeführt werden.


## <a name="repositories-and-directories"></a>Repositorys und Verzeichnisse

In diesem Tutorial werden abgekürzte Namen für Repositorys und Verzeichnisse verwendet. Diese Namen erleichtern es, den Vorgängen zwischen den Repositorys und Verzeichnissen zu folgen. Diese Notation (R für Git-Repositorys und D für lokale Verzeichnisse auf Ihrer DSVM) wird in den folgenden Abschnitten verwendet:

- **R3**: Das Team-Repository **ProjectTemplate** auf Git, das Ihr Teamleiter eingerichtet hat.
- **R5**: Das Projekt-Repository auf Git, das Sie für Ihr Projekt eingerichtet haben.
- **D3**: Das aus R3 geklonte lokale Verzeichnis.
- **D5**: Das aus R5 geklonte lokale Verzeichnis.


## <a name="0-prerequisites"></a>0. Voraussetzungen

Zum Erfüllen der Voraussetzungen müssen die dem Gruppenleiter und dem Teamleiter zugewiesenen Aufgaben ausgeführt werden. Siehe dazu [Group Manager tasks for a data science team (Aufgaben des Gruppenleiters eines Data Science-Teams)](group-manager-tasks.md) und [Team lead tasks for a data science team (Aufgaben des Teamleiters eines Data Science-Teams)](team-lead-tasks.md). 

Kurz zusammengefasst müssen die folgenden Anforderungen erfüllt sein, ehe Sie mit den Aufgaben des Teamleiters beginnen: 

- Ihr **Azure DevOps-Gruppenserver** (oder ein Gruppenkonto für eine andere Codehostingplattform) wurde von Ihrem Gruppenmanager eingerichtet.
- Ihr **TeamProjectTemplate-Repository** (R3) wurde unter Ihrem Gruppenkonto von Ihrem Teamleiter auf der Plattform zum Hosten von Code, die Sie verwenden möchten, eingerichtet .
- Sie wurden von Ihrem Teamleiter zur Erstellung von Repositorys auf Ihrem Gruppenkonto für Ihr Team **autorisiert**.
- Git muss auf Ihrem Computer installiert sein. Wenn Sie eine Data Science Virtual Machine (DSVM) verwenden, ist Git bereits installiert, sodass Sie gleich loslegen können. Andernfalls helfen Ihnen die Informationen im [Anhang von Plattformen und Tools](platforms-and-tools.md#appendix) weiter.  
- Bei Verwendung einer **Windows DSVM** muss [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) auf Ihrem Computer installiert sein. Scrollen Sie in der Datei „README.md“ nach unten zum Abschnitt **Download and Install**, und klicken Sie auf den *neuesten Installer*. Dadurch gelangen Sie zur Seite mit dem neuesten Installer. Laden Sie den Installer mit der Erweiterung EXE herunter, und führen Sie ihn aus. 
- Bei Verwendung einer **Linux-DSVM** erstellen Sie auf Ihrer DSVM einen öffentlichen SSH-Schlüssel und fügen ihn Azure DevOps Services für Ihre Gruppe hinzu. Weitere Informationen zu SSH finden Sie im [Anhang von Plattformen und Tools](platforms-and-tools.md#appendix) im Abschnitt **Erstellen eines öffentlichen SSH-Schlüssels**. 


## <a name="1-create-a-project-repository-r5"></a>1. Erstellen Sie ein Projekt-Repository (R5)

- Melden Sie sich bei Azure DevOps Services für Ihre Gruppe unter *https://\<Name des Azure DevOps-Services\>.visualstudio.com* an. 
- Klicken Sie unter **Zuletzt geöffnete Projekte und Teams** auf **Durchsuchen**. In einem eingeblendeten Fenster werden alle Projekte bei Azure DevOps Services aufgelistet. 

    ![2](./media/project-lead-tasks/project-leads-2-create-project-repo.png)

- Klicken Sie auf den Namen des Projekts, in dem Sie Ihr Projektrepository erstellen möchten. Klicken Sie in diesem Beispiel auf **MyTeam**. 
- Klicken Sie dann auf **Navigieren**, um zur Startseite des Projekts **MyTeam** zu gelangen:

    ![3](./media/project-lead-tasks/project-leads-3-create-project-repo-2.png)

- Klicken Sie auf **An Code zusammenarbeiten**, um zur Git-Startseite des Projekts zu gelangen.  

    ![4](./media/project-lead-tasks/project-leads-4-create-project-repo-3.png)

- Klicken Sie auf den nach unten weisenden Pfeil in der oberen linken Ecke, und wählen Sie **+ Neues Repository** aus. 
    
    ![5](./media/project-lead-tasks/project-leads-5-create-project-repo-4.png)

- Geben Sie im Fenster **Ein neues Repository erstellen** einen Namen für Ihr Git-Projektrepository ein. Stellen Sie sicher, dass Sie **Git** als Typ des Repositorys auswählen. In diesem Beispiel verwenden wir den Namen *DSProject1*. 

    ![6](./media/project-lead-tasks/project-leads-6-create-project-repo-5.png)

- Klicken Sie zum Erstellen Ihres Git-Projektrepositorys ***DSProject1*** auf **Erstellen**.


## <a name="2-seed-the-dsproject1-project-repository"></a>2. Seeding für das Projektrepository DSProject1 ausführen

Die Aufgabe besteht darin, Seeding für das Projektrepository **DSProject1** (R5) von Ihrem Repository für Projektvorlagen (R3) auszuführen. Das Seeding-Verfahren verwendet die Verzeichnisse D3 und D5 auf Ihrem lokalen DSVM als fortgeschrittene Stagingsites. Zusammenfassend lautet der Seeding-Pfad: R3 -> D3 -> D5 -> R5.

Wenn Sie Ihr Projektrepository **DSProject1** anpassen möchten, um einige bestimmte Projektbedürfnisse zu erfüllen, müssen Sie dies im vorletzten Schritt des Verfahrens tun. Hier sehen Sie eine Zusammenfassung der Schritte, durch die der Inhalt des Projektrepositorys **DSProject1** angelegt wird. Die einzelnen Schritte entsprechen den Unterabschnitten im Seeding-Verfahren:

- Klonen Sie das Projektvorlagenrepository in das lokale Verzeichnis: Team R3 – geklont zu -> lokale D3.
- Klonen Sie das Repository DSProject1 in ein lokales Verzeichnis: Team R5 - geklont zu -> lokale D5.
- Kopieren Sie die Inhalte der geklonten Projektvorlagen in den lokalen Klon des DSProject1-Repositorys:  D3 – Inhalt kopiert nach –> D5
- (Optional) Anpassung der lokalen D5.
- Pushen des lokalen DSProject1-Inhalts in Teamrepositorys: D5 – Inhalt hinzufügen zu -> Team R5


### <a name="clone-your-project-template-repository-r3-to-a-directory-d3-on-your-local-machine"></a>Klonen Sie Ihr Projektvorlagenrepository (R3) in ein Verzeichnis (D3) auf dem lokalen Computer.

Erstellen Sie ein Verzeichnis auf dem lokalen Computer:

- *C:\GitRepos\MyTeamCommon* für Windows 
- *$home/GitRepos/MyTeamCommon* für Linux

Navigieren Sie in dieses Verzeichnis. Führen Sie dann den folgenden Befehl aus, um Ihr Projektvorlagenrepository auf Ihren lokalen Computer zu klonen. 

**Windows**
            
    git clone <the HTTPS URL of the TeamProjectTemplate repository>
    
Wenn Sie Azure DevOps als Codehostingplattform verwenden, lautet die *HTTPS-URL Ihres Projektvorlagenrepositorys* in der Regel:

 ***https://\<Azure DevOps Services-Name\>.visualstudio.com/\<Name Ihres Projekts\>/_git/\<Name Ihres Projektvorlagenrepositorys\>***. 

In diesem Beispiel sehen wir:

***https://mysamplegroup.visualstudio.com/MyTeam/_git/MyTeamProjectTemplate***. 

![7](./media/project-lead-tasks/project-leads-7-clone-team-project-template.png)
            
**Linux**

    git clone <the SSH URL of the TeamProjectTemplate repository>
        
![8](./media/project-lead-tasks/project-leads-8-clone-team-project-template-linux.png)

Wenn Sie Azure DevOps als Codehostingplattform verwenden, lautet die *SSH-URL des Projektvorlagenrepositorys* in der Regel:

***ssh://\<Azure DevOps Services-Name\>\@\<Azure DevOps Services-Name\>.visualstudio.com:22/\<Name Ihres Projekts>/_git/\<Name Ihres Projektvorlagenrepositorys\>.*** 

In diesem Beispiel sehen wir:

***ssh://mysamplegroup\@mysamplegroup.visualstudio.com:22/MyTeam/_git/MyTeamProjectTemplate***. 

### <a name="clone-dsproject1-repository-r5-to-a-directory-d5-on-your-local-machine"></a>Klonen Sie das Repository DSProject1 (R5) in ein Verzeichnis (D5) auf dem lokalen Computer

Navigieren Sie zum Verzeichnis **GitRepos**, und führen Sie dann den folgenden Befehl aus, um das Projektrepository auf Ihren lokalen Computer zu klonen. 

**Windows**
            
    git clone <the HTTPS URL of the Project repository>

![9](./media/project-lead-tasks/project-leads-9-clone-project-repository.png)

Wenn Sie Azure DevOps als Codehostingplattform verwenden, lautet die _HTTPS-URL des Projektrepositorys_ in der Regel ***https://\<Azure DevOps Services-Name\>.visualstudio.com/\<Name Ihres Projekts>/_git/<Name Ihres Projektrepositorys\>***. In diesem Beispiel haben wir ***https://mysamplegroup.visualstudio.com/MyTeam/_git/DSProject1***.

**Linux**

    git clone <the SSH URL of the Project repository>

![10](./media/project-lead-tasks/project-leads-10-clone-project-repository-linux.png)

Wenn Sie Azure DevOps als Codehostingplattform verwenden, lautet die _SSH-URL des Projektrepositorys_ in der Regel „ssh://<Azure DevOps Services-Name\>@<Azure DevOps Services-Name\>.visualstudio.com:22/<Name Ihres Projekts\>/\_git/<Name Ihres Projektrepositorys\>“. In diesem Beispiel ist dies ***ssh://mysamplegroup\@mysamplegroup.visualstudio.com:22/MyTeam/_git/DSProject1***.

### <a name="copy-contents-of-d3-to-d5"></a>Kopieren Sie Inhalte von D3 auf D5 

Sie müssen nun in Ihrem lokalen Computer den Inhalt von _D3_ nach _D5_ kopieren, mit Ausnahme der Git-Metadaten im .git-Verzeichnis. Die folgenden Skripts werden die Aufgabe erledigen. Stellen Sie sicher, dass Sie die richtigen und vollständigen Pfade in die Verzeichnisse eingeben. Der Quellordner ist für Ihr Team (_D3_); der Zielordner ist für Ihr Projekt bestimmt (_D5_).    

**Windows**
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 -role 3
    
![11](./media/project-lead-tasks/project-leads-11-local-copy-project-lead-new.png)

Nun können Sie im Ordner _DSProject1_ alle Dateien (mit Ausnahme der .git-Dateien) sehen, die von _MyTeamProjectTemplate_ kopiert wurden.

![12](./media/project-lead-tasks/project-leads-12-teamprojectTemplate_copied_to_local.png)

**Linux**
            
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 3
        
![13](./media/project-lead-tasks/project-leads-13-local_copy_project_lead_linux_new.png)

Nun können Sie im Ordner _DSProject1_ alle Dateien (mit Ausnahme der Metadaten der .git-Dateien) sehen, die von _MyTeamProjectTemplate_ kopiert wurden.

![14](./media/project-lead-tasks/project-leads-14-teamprojectTemplate_copied_to_local_linux_new.png)


### <a name="customize-d5-if-you-need-to-optional"></a>Anpassen von D5 bei Bedarf (optional)

Wenn das Projekt einige bestimmte Verzeichnisse oder Dokumente erfordert, die Sie nicht aus Ihren Projektvorlagen (im vorherigen Schritt in Ihr Verzeichnis D5 kopiert) erhalten, können Sie den Inhalt von D5 jetzt anpassen. 

### <a name="add-contents-of-dsproject1-in-d5-to-r5-on-your-group-azure-devops-services"></a>Hinzufügen von Inhalten von DSProject1 in D5 zu R5 auf Ihrer Azure DevOps Services-Instanz

Jetzt müssen Sie Inhalte in **_DSProject1_** in das Repository _R5_ im Projekt Ihrer Azure DevOps Services-Instanz pushen. 


- Wechseln Sie zum Verzeichnis **D5**. 
- Verwenden Sie die folgenden Git-Befehle zum Hinzufügen von Inhalten in **D5** auf **R5**. Die Befehle sind für Windows- und Linux-Systeme identisch. 
    
    git status git add .
    git commit -m"push from win DSVM" git push
    
- Führen Sie ein Commit der Änderungen und Push durch. 

> [!NOTE]
> Wenn dies das erste Mal ist, dass Sie ein Commit in einem Git-Repository ausführen, müssen Sie globale Parameter wie *user.name* und *user.email* vor dem Ausführen des `git commit`-Befehls konfigurieren. Führen Sie die folgenden zwei Befehle aus:
        
    git config --global user.name <your name>
    git config --global user.email <your email address>
 
> Wenn Sie mit mehreren Git-Repositorys ein Commit ausführen, verwenden Sie den gleichen Namen und die gleiche E-Mail-Adresse für alle. Der gleiche Name und die gleiche E-Mail-Adresse stellen sich später als nützlich heraus, wenn Sie Power BI-Dashboards erstellen, um Ihre Git-Aktivitäten auf mehreren Repositorys nachzuverfolgen.

![15](./media/project-lead-tasks/project-leads-15-git-config-name.png)


## <a name="6-create-and-mount-azure-file-storage-as-project-resources-optional"></a>6. Erstellen Sie den Azure-Dateispeicher und stellen Sie ihn als Projektressourcen bereit (Optional)

Wenn Sie einen Azure-Dateispeicher zur Datenfreigabe von z.B. Projektrohdaten oder für Ihr Projekt erstellte Funktionen erstellen möchten, sodass alle Projektmitglieder über Zugang zu den gleichen Datasets aus mehreren DSVMs verfügen, dann befolgen Sie die Anweisungen in den Abschnitten 3 und 4 der [Team Lead tasks for a data science team (Aufgaben des Teamleiters eines Data Science-Teams)](team-lead-tasks.md). 


## <a name="next-steps"></a>Nächste Schritte

Es folgen Links zu den ausführlicheren Beschreibungen der Rollen und Aufgaben, die vom Team Data Science-Prozess definiert werden:

- [Group Manager tasks for a data science team (Aufgaben des Gruppenleiters eines Data Science-Teams)](group-manager-tasks.md)
- [Team Lead tasks for a data science team (Aufgaben des Teamleiters eines Data Science-Teams)](team-lead-tasks.md)
- [Project Lead tasks for a data science team (Aufgaben des Projektleiters eines Data Science-Teams)](project-lead-tasks.md)
- [Project Individual Contributors for a data science team (Einzelne Mitwirkende zu einem Projekt eines Data Science-Teams)](project-ic-tasks.md)
