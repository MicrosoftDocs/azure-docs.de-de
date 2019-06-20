---
title: 'Gemeinsames Schreiben von Code mit Git: Team Data Science-Prozess'
description: Erfahren Sie, wie eine gemeinschaftliche Codeentwicklung für Data Science-Projekte mithilfe von Git mit Agile-Planung umgesetzt wird.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/28/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 551d0cd149c4d1555a40ccf0d7baeff97c6809c2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60336295"
---
# <a name="collaborative-coding-with-git"></a>Gemeinsames Schreiben von Code mit Git

In diesem Artikel beschreiben wir die gemeinschaftliche Codeentwicklung für Data Science-Projekte mithilfe von Git als Entwicklungsframework für freigegebenen Code. Dabei wird auch behandelt, wie diese Codeentwicklungsaktivitäten mit der in [agiler Entwicklung](agile-development.md) geplanten Arbeit verknüpft werden und wie Code Reviews durchgeführt werden.


## 1. <a name='Linkaworkitemwithagitbranch-1'></a>Verknüpfen eines Arbeitselements mit einem Git-Branch 

Azure DevOps Services bietet eine einfache Möglichkeit zum Verbinden eines Arbeitselements (Story oder Aufgabe) mit einem Git-Branch. Dies ermöglicht Ihnen, Ihre Story oder Aufgabe direkt mit dem zugeordneten Code zu verknüpfen. 

Um ein Arbeitselement mit einer neuen Verzweigung zu verbinden, doppelklicken Sie auf das Arbeitselement, und klicken Sie im Popupfenster auf **Neue Verzweigung erstellen** unter **+ Link hinzufügen**.  

![1](./media/collaborative-coding-with-git/1-sprint-board-view.png)

Geben Sie die Informationen für diesen neuen Branch ein, etwa den Namen des Branches, das Git-Basisrepository und den Branch. Das ausgewählte Git-Repository muss das Repository für dasselbe Projekt sein, dem das Arbeitselement angehört. Die Basisverzweigung kann die Hauptverzweigung oder eine andere vorhandene Verzweigung sein.

![2](./media/collaborative-coding-with-git/2-create-a-branch.png)

Eine bewährte Methode besteht darin, für jedes Storyarbeitselement einen Git-Branch zu erstellen. Anschließend erstellen Sie für jedes Arbeitselement eine Verzweigung anhand der Storyverzweigung. Die Verzweigungen auf diese hierarchische Weise zu organisieren, die den Beziehungen zwischen Story und Aufgabe entspricht, ist nützlich, wenn mehrere Personen an unterschiedlichen Storys im selben Projekt oder an unterschiedlichen Aufgaben in derselben Story arbeiten. Konflikte können minimiert werden, wenn jedes Teammitglied an einer anderen Verzweigung arbeitet und bei gemeinsamer Arbeit an einer Verzweigung jedes Mitglied an unterschiedlichen Codes oder anderen Artefakten arbeitet. 

Die folgende Abbildung zeigt die empfohlene Verzweigungsstrategie für TDSP. Möglicherweise benötigen Sie nicht so viele Verzweigungen, wie hier dargestellt sind, insbesondere, wenn nur eine oder zwei Personen am selben Projekt arbeiten oder nur eine Person alle Aufgaben einer Story bearbeitet. Das Trennen der Entwicklungsverzweigung von der Hauptverzweigung ist jedoch immer ratsam. Dies verringert das Risiko, dass die Releaseverzweigung durch Entwicklungsaktivitäten unterbrochen wird. Eine ausführlichere Beschreibung des Git-Branchmodells finden Sie unter [A Successful Git Branching Model](https://nvie.com/posts/a-successful-git-branching-model/) (Ein erfolgreiches Git-Branchmodell).

![3](./media/collaborative-coding-with-git/3-git-branches.png)

Um in die Verzweigung zu wechseln, die Sie bearbeiten möchten, führen Sie den folgenden Shellbefehl aus (Windows oder Linux). 

    git checkout <branch name>

Durch das Ändern von *<branch name\>* in **master** gelangen Sie zurück zur **Hauptverzweigung**. Nach dem Wechsel in die Arbeitsverzweigung können Sie an diesem Arbeitselement arbeiten und die Code- oder Dokumentationsartefakte entwickeln, die zum Fertigstellen des Elements erforderlich sind. 

Sie können auch ein Arbeitselement mit einer vorhandenen Verzweigung verknüpfen. Klicken Sie auf der Seite **Detail** eines Arbeitselements nicht auf **Neue Verzweigung erstellen**, sondern auf **+ Link hinzufügen**. Wählen Sie dann die Verzweigung aus, mit der Sie das Arbeitselement verknüpfen möchten. 

![4](./media/collaborative-coding-with-git/4-link-to-an-existing-branch.png)

Sie können auch mit Git Bash-Befehlen einen neuen Branch erstellen. Wenn <bas branch name\> fehlt, basiert <new branch name\> auf der Verzweigung _master_. 
    
    git checkout -b <new branch name> <base branch name>


## 2. <a name='WorkonaBranchandCommittheChanges-2'></a>Arbeiten an einer Verzweigung und Committen der Änderungen 

Angenommen, Sie nehmen eine Änderung an der Verzweigung *data\_ingestion* für das Arbeitselement vor, beispielsweise, indem Sie eine R-Datei in dem Zweig auf Ihrem lokalen Computer hinzufügen. Sofern Sie sich in Ihrer Git-Shell in der Verzweigung für dieses Arbeitselement befinden, können Sie die R-Datei, die Sie dieser Verzweigung hinzugefügt haben, mithilfe der folgenden Git-Befehle committen:

    git status
    git add .
    git commit -m"added a R scripts"
    git push origin data_ingestion

![5](./media/collaborative-coding-with-git/5-sprint-push-to-branch.png)

## 3. <a name='CreateapullrequestonVSTS-3'></a>Erstellen eines Pull Requests für Azure DevOps Services 

Wenn Sie nach einigen Commits und Push-Vorgängen bereit sind, den aktuellen Branch mit ihrer Basisverzweigung zusammenzuführen, können Sie einen **Pull Request** an Azure DevOps Services senden. 

Wechseln Sie zur Hauptseite Ihres Projekts, und klicken Sie auf **CODE**. Wählen Sie den Branch, der zusammengeführt werden soll, und den Namen des Git-Repositorys, mit dem der Branch zusammengeführt werden soll, aus. Klicken Sie dann auf **Pullanforderungen**, klicken Sie auf **Neue Pullanforderung**, um eine Pullanforderungsüberprüfung zu erstellen, bevor die Arbeit an der Verzweigung mit der Basisverzweigung zusammengeführt wird.

![6](./media/collaborative-coding-with-git/6-spring-create-pull-request.png)

Geben Sie eine Beschreibung zu dieser Pullanforderung an, fügen Sie Prüfer hinzu, und senden Sie die Anforderung.

![7](./media/collaborative-coding-with-git/7-spring-send-pull-request.png)

## 4. <a name='ReviewandMerge-4'></a>Überprüfen und Zusammenführen 

Wenn die Pullanforderung erstellt wird, erhalten die Prüfer eine E-Mail-Benachrichtigung, um die Pullanforderungen überprüfen zu können. Die Prüfer müssen überprüfen, ob die Änderungen funktionieren, und die Änderungen mit dem Antragsteller testen, wenn möglich. Basierend auf ihrer Bewertung können die Prüfer die Pullanforderung genehmigen oder ablehnen. 

![8](./media/collaborative-coding-with-git/8-add_comments.png)

![9](./media/collaborative-coding-with-git/9-spring-approve-pullrequest.png)

Nach der Überprüfung wird die Verzweigung durch Klicken auf die Schaltfläche **Abschließen** mit der Basisverzweigung zusammengeführt. Sie können die Verzweigung löschen, nachdem sie zusammengeführt wurde. 

![10](./media/collaborative-coding-with-git/10-spring-complete-pullrequest.png)

Bestätigen Sie in der linken oberen Ecke, dass die Anforderung als **ABGESCHLOSSEN** markiert ist. 

![11](./media/collaborative-coding-with-git/11-spring-merge-pullrequest.png)

Wenn Sie zum Repository unter **CODE** zurückkehren, erfahren Sie, dass Sie zur Hauptverzweigung weitergeleitet wurden.

![12](./media/collaborative-coding-with-git/12-spring-branch-deleted.png)

Sie können auch mit den folgenden Git-Befehlen Ihre Arbeitsverzweigung mit ihrer Basisverzweigung zusammenführen und anschließend die Arbeitsverzweigung löschen:

    git checkout master
    git merge data_ingestion
    git branch -d data_ingestion

![13](./media/collaborative-coding-with-git/13-spring-branch-deleted-commandline.png)


 
## <a name="next-steps"></a>Nächste Schritte

Unter [Ausführen von Data Science-Aufgaben](execute-data-science-tasks.md) wird gezeigt, wie Sie mit Hilfsprogrammen verschiedene allgemeine Data Science-Aufgaben wie interaktive Datenerkundung, Datenanalysen, Berichterstellung und Modellerstellung ausführen.

Exemplarische Vorgehensweisen, in denen sämtliche Schritte im Prozess für **bestimmte Szenarien** gezeigt werden, sind ebenfalls verfügbar. Sie sind im Artikel [Exemplarische Vorgehensweisen](walkthroughs.md) aufgeführt und mit Miniaturansichtsbeschreibungen verlinkt. Sie zeigen, wie Cloud- und lokale Tools und Dienste in einem Workflow oder einer Pipeline zum Erstellen einer intelligenten Anwendung kombiniert werden. 

