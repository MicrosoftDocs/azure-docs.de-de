---
title: Aufgaben eines Gruppenleiters im Team Data Science-Prozess
description: Enthält einen Überblick über die Aufgaben des Gruppenleiters eines Data Science-Teamprojekts.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 85a4aca0c4b80eaab1f43bcbec33dc9cf37aa655
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65950105"
---
# <a name="tasks-for-a-group-manager-on-a-data-science-team-project"></a>Aufgaben eines Gruppenleiters eines Data Science-Teamprojekts

In diesem Thema werden die Aufgaben beschrieben, die ein Gruppenleiter für seine Data Science-Organisation zu erfüllen hat. Das Ziel besteht darin, eine auf Zusammenarbeit ausgelegte Gruppenumgebung zu schaffen, die gemäß dem [Team Data Science-Prozess (TDSP)](overview.md) vereinheitlicht ist. Unter [Rollen und Aufgaben im Team Data Science-Prozess](roles-tasks.md) finden Sie eine Übersicht über die wichtigsten Mitarbeiterrollen und die dazugehörigen Aufgaben für ein Data Science-Team, das eine Vereinheitlichung gemäß diesem Prozess anstrebt.

Der **Gruppenleiter** verwaltet die gesamte Data Science-Einheit in einem Unternehmen. Eine Data Science-Einheit kann aus mehreren Teams bestehen, von denen jedes an unterschiedlichen Data Science-Projekten in bestimmten Branchen arbeitet. Ein Gruppenleiter kann seine Aufgaben an einen Stellvertreter delegieren. Dadurch bleiben die der Rolle zugeordneten Aufgaben aber identisch. Wie im folgenden Diagramm dargestellt, gibt es sechs Hauptaufgaben:

![0](./media/group-manager-tasks/tdsp-group-manager.png)


> [!NOTE] 
> In den unten angegebenen Anweisungen werden die erforderlichen Schritte zum Einrichten einer TDSP-Gruppenumgebung mit Azure DevOps Services beschrieben. Es wird angegeben, wie diese Aufgaben mit Azure DevOps Services erledigt werden können, da TDSP auf diese Weise bei Microsoft implementiert wird. Wenn für Ihre Gruppe eine andere Plattform zum Hosten des Codes verwendet wird, ändern sich die vom Gruppenleiter auszuführenden Aufgaben in der Regel nicht. Sie müssen aber auf andere Weise ausgeführt werden.

1. Richten Sie **Azure DevOps Services** für die Gruppe ein.
2. Erstellen eines **Gruppenprojekts** bei Azure DevOps Services (für Azure DevOps Services-Benutzer)
3. Erstellen des Repositorys **GroupProjectTemplate**
4. Erstellen des Repositorys **GroupUtilities**
5. Durchführen des Seedings für die Repositorys **GroupProjectTemplate** und **GroupUtilities** für Azure DevOps Services mit Inhalten aus den TDSP-Repositorys
6. Einrichten der **Sicherheitskontrollen** für Teammitglieder für den Zugriff auf die Repositorys GroupProjectTemplate und GroupUtilities

Jeder dieser Schritte wird ausführlich beschrieben. Zuerst werden aber die Abkürzungen vorgestellt und die Voraussetzungen für die Verwendung von Repositorys beschrieben.

### <a name="abbreviations-for-repositories-and-directories"></a>Abkürzungen für Repositorys und Verzeichnisse

In diesem Tutorial werden abgekürzte Namen für Repositorys und Verzeichnisse verwendet. Diese Definitionen erleichtern es Ihnen, die Vorgänge zwischen den Repositorys und Verzeichnissen zu verfolgen. Diese Notation wird in den folgenden Abschnitten verwendet:

- **G1**: Das Projektvorlagenrepository, das vom TDSP-Team von Microsoft entwickelt und verwaltet wird.
- **G2**: Das Hilfsprogrammrepository, das vom TDSP-Team von Microsoft entwickelt und verwaltet wird.
- **R1**: Das Repository GroupProjectTemplate auf Git, das Sie auf Ihrem Azure DevOps-Gruppenserver einrichten.
- **R2**: Das Repository GroupUtilities auf Git, das Sie auf Ihrem Azure DevOps-Gruppenserver einrichten.
- **LG1** und **LG2**: Die lokalen Verzeichnisse auf Ihrem Computer, in denen Sie das Klonen für G1 bzw. G2 durchführen.
- **LR1** und **LR2**: Die lokalen Verzeichnisse auf Ihrem Computer, in denen Sie das Klonen für R1 bzw. R2 durchführen.

### <a name="pre-requisites-for-cloning-repositories-and-checking-code-in-and-out"></a>Voraussetzungen für das Klonen von Repositorys und das Aus- und Einchecken von Code

- Git muss auf Ihrem Computer installiert sein. Wenn Sie eine Data Science Virtual Machine (DSVM) verwenden, ist Git bereits installiert, sodass Sie gleich loslegen können. Andernfalls helfen Ihnen die Informationen im [Anhang von Plattformen und Tools](platforms-and-tools.md#appendix) weiter.
- Bei Verwendung einer **Windows DSVM** muss [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) auf Ihrem Computer installiert sein. Scrollen Sie in der Datei „README.md“ nach unten zum Abschnitt **Herunterladen und installieren**, und klicken Sie auf den *neuesten Installer*. Mit diesem Schritt gelangen Sie zur Seite mit dem neuesten Installer. Laden Sie den Installer mit der Erweiterung EXE herunter, und führen Sie ihn aus.
- Bei Verwendung einer **Linux-DSVM** erstellen Sie auf Ihrer DSVM einen öffentlichen SSH-Schlüssel und fügen ihn Azure DevOps Services für Ihre Gruppe hinzu. Weitere Informationen zu SSH finden Sie im [Anhang von Plattformen und Tools](platforms-and-tools.md#appendix) im Abschnitt **Erstellen eines öffentlichen SSH-Schlüssels**.


## <a name="1-create-account-on-azure-devops-services"></a>1. Erstellen eines Kontos bei Azure DevOps Services

Bei Azure DevOps Services werden die folgenden Repositorys gehostet:

- **Allgemeine Gruppenrepositorys**: Repositorys für allgemeine Zwecke, die von mehreren Teams einer Gruppe für verschiedene Data Science-Projekte genutzt werden können. Beispiele hierfür sind die Repositorys *GroupProjectTemplate* und *GroupUtilities*.
- **Teamrepositorys**:  Repositorys für bestimmte Teams einer Gruppe. Diese Repositorys gelten jeweils für die Anforderungen eines Teams und können für mehrere Projekte genutzt werden, die von diesem Team ausgeführt werden. Sie sind aber nicht so allgemein gehalten, dass sie für mehrere Teams einer Data Science-Gruppe nützlich sind.
- **Projektrepositorys**: Repositorys, die für bestimmte Projekte verfügbar sind. Repositorys dieser Art sind meist nicht so allgemein gehalten, dass sie für mehrere Projekte eines Teams oder mehrere Teams einer Data Science-Gruppe nützlich sind.


### <a name="setting-up-the-azure-devops-services-sign-into-your-microsoft-account"></a>Einrichten der Azure DevOps Services-Anmeldung in Ihrem Microsoft-Konto

Navigieren Sie zu [Visual Studio Online](https://www.visualstudio.com/), klicken Sie oben rechts auf **Anmelden**, und melden Sie sich an Ihrem Microsoft-Konto an.

![1](./media/group-manager-tasks/login.PNG)

Gehen Sie wie folgt vor, wenn Sie kein Microsoft-Konto haben: Klicken Sie auf **Jetzt anmelden**, um ein Microsoft-Konto zu erstellen, und melden Sie sich dann mit diesem Konto an.

Gehen Sie wie folgt vor, wenn Ihre Organisation über ein Visual Studio/MSDN-Abonnement verfügt: Klicken Sie auf das grüne Feld **Mit Ihrem Geschäfts-, Schul- oder Unikonto anmelden**, und melden Sie sich mit den Anmeldeinformationen an, die dem Abonnement zugeordnet sind.

![2](./media/group-manager-tasks/signin.PNG)



Klicken Sie nach dem Anmelden oben rechts auf **Neues Konto erstellen**. Dies ist in der folgenden Abbildung dargestellt:

![3](./media/group-manager-tasks/create-account-1.PNG)

Geben Sie die Informationen für die Azure DevOps Services-Instanz, die Sie erstellen möchten, im Assistenten **Konto erstellen** mit den folgenden Werten ein:

- **Server-URL**: Ersetzen Sie *mysamplegroup* durch Ihren eigenen *Servernamen*. Die URL des Servers lautet wie folgt: *https://\<servername\>.visualstudio.com*.
- **Verwalten der Codeverwendung:** Wählen Sie **_Git_** aus.
- **Projektname:** Geben Sie *GroupCommon* ein.
- **Aufgaben organisieren mithilfe von:** Wählen Sie *Agile* aus.
- **Hosten des Projekts in:** Wählen Sie einen geografischen Standort aus. In diesem Beispiel wählen wir *USA, Süden-Mitte*.

![4](./media/group-manager-tasks/fill-in-account-information.png)

> [!NOTE] 
> Wenn nach dem Klicken auf **Neues Konto erstellen** das folgende Popupfenster angezeigt wird, müssen Sie auf **Details ändern** klicken, um alle aufgeführten Felder anzuzeigen.

![5](./media/group-manager-tasks/create-account-2.png)


Klicken Sie auf **Weiter**.

## <a name="2-groupcommon-project"></a>2. Projekt GroupCommon

Die Seite **GroupCommon** (*https://\<Servername\>.visualstudio.com/GroupCommon*) wird geöffnet, nachdem Ihre Azure DevOps Services-Instanz erstellt wurde.

![6](./media/group-manager-tasks/server-created-2.PNG)

## <a name="3-create-the-grouputilities-r2-repository"></a>3. Erstellen des GroupUtilities-Repositorys (R2)

So erstellen Sie das Repository **GroupUtilities** (R2) unter Azure DevOps Services

- Klicken Sie zum Öffnen des Assistenten **Neues Repository erstellen** auf der Registerkarte **Versionskontrolle** Ihres Projekts auf **Neues Repository**.

  ![7](./media/group-manager-tasks/create-grouputilities-repo-1.png)

- Wählen Sie *Git* als **Typ** aus, und geben Sie *GroupUtilities* als **Name** ein. Klicken Sie anschließend auf **Erstellen**.

  ![8](./media/group-manager-tasks/create-grouputilities-repo-2.png)

Jetzt sollten die beiden Git-Repositorys **GroupProjectTemplate** und **GroupUtilities** auf der Seite **Versionskontrolle** in der linken Spalte angezeigt werden:

![9](./media/group-manager-tasks/two-repo-under-groupCommon.PNG)


## <a name="4-create-the-groupprojecttemplate-r1-repository"></a>4. Erstellen des Repositorys GroupProjectTemplate (R1)

Das Einrichten der Repositorys für den Azure DevOps-Gruppenserver umfasst zwei Aufgaben:

- Umbenennen des Standardrepositorys **GroupCommon** in ***GroupProjectTemplate***
- Erstellen des Repositorys **GroupUtilities** bei Azure DevOps Services unter dem Projekt **GroupCommon**

Eine Anleitung für die erste Aufgabe ist in diesem Abschnitt enthalten, nachdem die Namenskonventionen bzw. die Repositorys und Verzeichnisse beschrieben wurden. Die Anleitung für die zweite Aufgabe ist im folgenden Abschnitt für Schritt 4 enthalten.

### <a name="rename-the-default-groupcommon-repository"></a>Umbenennen des Standardrepositorys GroupCommon

Gehen Sie wie folgt vor, um das Standardrepository **GroupCommon** in *GroupProjectTemplate* umzubenennen (in diesem Tutorial als **R1** bezeichnet):

- Klicken Sie auf der Projektseite **GroupCommon** auf **An Code zusammenarbeiten**. Dadurch gelangen Sie auf die Git-Repository-Standardseite des Projekts **GroupCommon**. Dieses Git-Repository ist derzeit leer.

  ![10](./media/group-manager-tasks/rename-groupcommon-repo-3.png)

- Klicken Sie oben links auf der Git-Repositoryseite von **GroupCommon** auf **GroupCommon** (in der folgenden Abbildung durch einen roten Kasten gekennzeichnet), und wählen Sie die Option **Repositorys verwalten** (durch einen grünen Kasten gekennzeichnet). Die **SYSTEMSTEUERUNG** wird aufgerufen.
- Wählen Sie die Registerkarte **Versionskontrolle** Ihres Projekts aus.

  ![11](./media/group-manager-tasks/rename-groupcommon-repo-4.png)

- Klicken Sie im linken Bereich rechts vom Repository **GroupCommon** auf **...** , und wählen Sie die Option **Repository umbenennen**.

  ![12](./media/group-manager-tasks/rename-groupcommon-repo-5.png)

- Geben Sie im angezeigten Assistenten zum **Umbenennen des Repositorys GroupCommon** im Feld **Repositoryname** den Text *GroupProjectTemplate* ein, und klicken Sie anschließend auf **Umbenennen**.

  ![13](./media/group-manager-tasks/rename-groupcommon-repo-6.png)



## <a name="5-seed-the-r1--r2-repositories-on-the-azure-devops-services"></a>5. Durchführen des Seedings für die Repositorys R1 und R2 bei Azure DevOps Services

In dieser Phase des Verfahrens führen Sie das Seeding für die Repositorys *GroupProjectTemplate* (R1) und *GroupUtilities* (R2) durch, die Sie im vorherigen Abschnitt eingerichtet haben. Für diese Repositorys erfolgt das Seeding mit den Repositorys ***ProjectTemplate*** (**G1**) und ***Utilities*** (**G2**), die von Microsoft für den Team Data Science-Prozess verwaltet werden. Nach Abschluss des Seedings gilt Folgendes:

- Ihr R1-Repository verfügt über den gleichen Satz mit Verzeichnissen und Dokumentvorlagen wie G1.
- Ihr R2-Repository enthält den Satz mit den von Microsoft entwickelten Data Science-Hilfsprogrammen.

Für das Seeding-Verfahren werden die Verzeichnisse auf Ihrem lokalen DSVM als Stagingsites für die Zwischenspeicherung verwendet. Hier sind die Schritte angegeben, die in diesem Abschnitt ausgeführt werden:

- G1 & G2: Klonvorgang nach LG1 & LG2
- R1 & R2: Klonvorgang nach LR1 & LR2
- LG1 & LG2: Dateikopiervorgang nach LR1 & LR2
- (Optional) Anpassung von LR1 & LR2
- LR1 & LR2: Hinzufügung von Inhalten zu R1 & R2


### <a name="clone-g1--g2-repositories-to-your-local-dsvm"></a>Klonen der Repositorys G1 und G2 auf Ihrer lokalen DSVM

In diesem Schritt klonen Sie die Repositorys ProjectTemplate (G1) und Utilities (G2) des Team Data Science-Prozesses (TDSP) aus den TDSP-GitHub-Repositorys als LG1 und LG2 in Ordnern auf Ihrer lokalen DSVM:

- Erstellen Sie ein Verzeichnis, das als Stammverzeichnis für das Hosten Ihrer gesamten Klone der Repositorys dient.
  -  Erstellen Sie auf der Windows DSVM das Verzeichnis *C:\GitRepos\TDSPCommon*.
  -  Erstellen Sie auf der Linux DSVM in Ihrem Stammverzeichnis das Verzeichnis *GitRepos\TDSPCommon*.

- Führen Sie im Verzeichnis *GitRepos\TDSPCommon* den folgenden Befehlssatz aus.

  `git clone https://github.com/Azure/Azure-TDSP-ProjectTemplate`<br>
  `git clone https://github.com/Azure/Azure-TDSP-Utilities`

  ![14](./media/group-manager-tasks/two-folder-cloned-from-TDSP-windows.PNG)

- Hier ist anhand der abgekürzten Repositorynamen angegeben, was mit diesen Skripts erreicht wurde:
    - G1: Klonvorgang nach LG1
    - G2: Klonvorgang nach LG2
- Nach Abschluss des Klonvorgangs sollten im Verzeichnis **GitRepos\TDSPCommon** die beiden Verzeichnisse _ProjectTemplate_ und _Utilities_ angezeigt werden.

### <a name="clone-r1--r2-repositories-to-your-local-dsvm"></a>Klonen der Repositorys R1 und R2 auf Ihrer lokalen DSVM

In diesem Schritt klonen Sie die Repositorys GroupProjectTemplate (R1) und GroupUtilities (R2) in lokalen Verzeichnissen (als LR1 bzw. LR2 bezeichnet) unter **GitRepos\GroupCommon** auf Ihrer DSVM.

- Navigieren Sie zur **GroupCommon**-Startseite in Azure DevOps Services, um die URLs der Repositorys R1 und R2 abzurufen. Die URL lautet normalerweise *https://\<Ihr Azure DevOps Services-Name\>.visualstudio.com/GroupCommon*.
- Klicken Sie auf **CODE**.
- Wählen Sie die Repositorys **GroupProjectTemplate** und **GroupUtilities** aus. Kopieren und speichern Sie die einzelnen URLs (HTTPS für Windows, SSH für Linux) wiederum aus dem Element **URL klonen**, um sie in den folgenden Skripts zu verwenden:

  ![15](./media/group-manager-tasks/find_https_ssh_2.PNG)

- Wechseln Sie auf Ihrer Windows- bzw. Linux-DSVM zum Verzeichnis **GitRepos\GroupCommon**, und führen Sie einen der folgenden Befehlssätze aus, um R1 und R2 in diesem Verzeichnis zu klonen.

Hier sind die Skripts für Windows und Linux angegeben:

    # Windows DSVM

    git clone <the HTTPS URL of the GroupProjectTemplate repository>
    git clone <the HTTPS URL of the GroupUtilities repository>

![16](./media/group-manager-tasks/clone-two-empty-group-reo-windows-2.PNG)

    # Linux DSVM

    git clone <the SSH URL of the GroupProjectTemplate repository>
    git clone <the SSH URL of the GroupUtilities repository>

![17](./media/group-manager-tasks/clone-two-empty-group-reo-linux-2.PNG)

> [!NOTE] 
> Rechnen Sie mit Warnmeldungen mit dem Hinweis, dass LR1 und LR2 leer sind.

- Hier ist anhand der abgekürzten Repositorynamen angegeben, was mit diesen Skripts erreicht wurde:
    - R1: Klonvorgang nach LR1
    - R2: Klonvorgang nach LR2   


### <a name="seed-your-groupprojecttemplate-lr1-and-grouputilities-lr2"></a>Durchführen des Seedings für GroupProjectTemplate (LR1) und GroupUtilities (LR2)

Kopieren Sie als Nächstes auf dem lokalen Computer den Inhalt der Verzeichnisse ProjectTemplate und Utilities (mit Ausnahme der Metadaten in den Git-Verzeichnissen) unter „GitRepos\TDSPCommon“ in die Verzeichnisse GroupProjectTemplate und GroupUtilities unter **GitRepos\GroupCommon**. In diesem Schritt werden die beiden folgenden Aufgaben durchgeführt:

- Kopieren Sie die Dateien des Verzeichnisses „GitRepos\TDSPCommon\ProjectTemplate“ (**LG1**) nach „GitRepos\GroupCommon\GroupProjectTemplate“ (**LR1**).
- Kopieren Sie die Dateien des Verzeichnisses „GitRepos\TDSPCommon\Utilities“ (**LG2**) nach „GitRepos\GroupCommon\Utilities“ (**LR2**).

Führen Sie hierzu die folgenden Skripts in der PowerShell-Konsole (Windows) oder in der Shell-Skriptkonsole (Linux) aus. Sie werden aufgefordert, die vollständigen Pfade zu LG1, LR1, LG2 und LR2 einzugeben. Die von Ihnen eingegebenen Pfade werden überprüft. Falls Sie ein nicht vorhandenes Verzeichnis eingeben, werden Sie aufgefordert, die Eingabe erneut vorzunehmen.

    # Windows DSVM
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 1

![18](./media/group-manager-tasks/copy-two-folder-to-group-folder-windows-2.PNG)

Sie sehen jetzt, dass die Dateien in den Verzeichnissen LG1 und LG2 (mit Ausnahme der Dateien im Git-Verzeichnis) nach LR1 bzw. LR2 kopiert wurden.

![19](./media/group-manager-tasks/copy-two-folder-to-group-folder-windows.PNG)

    # Linux DSVM

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 1

![20](./media/group-manager-tasks/copy-two-folder-to-group-folder-linux-2.PNG)

Sie sehen jetzt, dass die Dateien aus den beiden Ordnern (mit Ausnahme der Dateien im Git-Verzeichnis) nach GroupProjectTemplate bzw. GroupUtilities kopiert wurden.

![21](./media/group-manager-tasks/copy-two-folder-to-group-folder-linux.PNG)

- Hier ist anhand der abgekürzten Repositorynamen angegeben, was mit diesen Skripts erreicht wurde:
    - LG1: Dateikopiervorgang nach LR1
    - LG2: Dateikopiervorgang nach LR2

### <a name="option-to-customize-the-contents-of-lr1--lr2"></a>Option zum Anpassen des Inhalts von LR1 und LR2
    
Wenn Sie den Inhalt von LR1 und LR2 anpassen möchten, um die jeweiligen Anforderungen Ihrer Gruppe zu erfüllen, ist dies hierfür die richtige Phase des Verfahrens. Sie können die Vorlagendokumente ändern, die Verzeichnisstruktur ändern und vorhandene Hilfsprogramme hinzufügen, die von Ihrer Gruppe entwickelt wurden oder die für die gesamte Gruppe hilfreich sind.

### <a name="add-the-contents-in-lr1--lr2-to-r1--r2-on-group-server"></a>Hinzufügen des Inhalts von LR1 und LR2 in R1 und R2 auf dem Gruppenserver

Als Nächstes fügen Sie den Inhalt von LR1 und LR2 den Repositorys R1 und R2 hinzu. Hier sind die Git Bash-Befehle angegeben, die Sie in Windows PowerShell oder Linux ausführen können.

Führen Sie im Verzeichnis „GitRepos\GroupCommon\GroupProjectTemplate“ die folgenden Befehle aus:

    git status
    git add .
    git commit -m"push from DSVM"
    git push

Mit der Option „-m“ können Sie eine Meldung für den Git-Commitvorgang festlegen.

![22](./media/group-manager-tasks/push-to-group-server-2.PNG)

Sie sehen, dass die Dateien auf der Azure DevOps Services-Instanz Ihrer Gruppe im Repository GroupProjectTemplate sofort synchronisiert werden.

![23](./media/group-manager-tasks/push-to-group-server-showed-up-2.PNG)

Wechseln Sie abschließend in das Verzeichnis **GitRepos\GroupCommon\GroupUtilities**, und führen Sie den gleichen Satz an Git Bash-Befehlen aus:

    git status
    git add .
    git commit -m"push from DSVM"
    git push

> [!NOTE] 
> Wenn dies das erste Mal ist, dass Sie ein Commit in einem Git-Repository ausführen, müssen Sie globale Parameter wie *user.name* und *user.email* vor dem Ausführen des Befehls `git commit` konfigurieren. Führen Sie die folgenden beiden Befehle aus:
>
>  `git config --global user.name <your name>`  
>  `git config --global user.email <your email address>`
>
> Wenn Sie einen Commit für mehrere Git-Repositorys durchführen, sollten Sie bei den einzelnen Commit-Vorgängen jeweils den gleichen Namen und die gleiche E-Mail-Adresse verwenden. Der gleiche Name und die gleiche E-Mail-Adresse stellen sich später als nützlich heraus, wenn Sie Power BI-Dashboards erstellen, um Ihre Git-Aktivitäten auf mehreren Repositorys nachzuverfolgen.


- Hier ist anhand der abgekürzten Repositorynamen angegeben, was mit diesen Skripts erreicht wurde:
    - LR1: Hinzufügung von Inhalten zu R1
    - LR2: Hinzufügung von Inhalten zu R2

## <a name="6-add-group-members-to-the-group-server"></a>6. Hinzufügen von Gruppenmitgliedern zum Gruppenserver

Klicken Sie auf Ihrer Azure DevOps Services-Homepage rechts oben auf das **Zahnradsymbol** neben Ihrem Benutzernamen, und wählen Sie dann die Registerkarte **Sicherheit** aus. Sie können Ihrer Gruppe hier Mitglieder mit unterschiedlichen Berechtigungen hinzufügen.

![24](./media/group-manager-tasks/add_member_to_group.PNG)


## <a name="next-steps"></a>Nächste Schritte

Es folgen Links zu den ausführlicheren Beschreibungen der Rollen und Aufgaben, die vom Team Data Science-Prozess definiert werden:

- [Group Manager tasks for a data science team (Aufgaben des Gruppenleiters eines Data Science-Teams)](group-manager-tasks.md)
- [Team Lead tasks for a data science team (Aufgaben des Teamleiters eines Data Science-Teams)](team-lead-tasks.md)
- [Project Lead tasks for a data science team (Aufgaben des Projektleiters eines Data Science-Teams)](project-lead-tasks.md)
- [Project Individual Contributors for a data science team (Einzelne Mitwirkende zu einem Projekt eines Data Science-Teams)](project-ic-tasks.md)
