---
title: 'Testen von Data Science-Code mit Azure DevOps Services: Team Data Science-Prozess'
description: Data Science-Codetests in Azure mit dem Dataset der UCI zur Vorhersage des Einkommens von Erwachsenen mit Team Data Science-Prozess und Azure DevOps Services
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=weig, previous-ms.author=weig
ms.openlocfilehash: bcebc568db4a0d2b4b287045ba0a67c88d9b8c92
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020601"
---
# <a name="data-science-code-testing-on-azure-with-the-team-data-science-process-and-azure-devops-services"></a>Data Science-Codetests in Azure mit dem Team Data Science-Prozess und Azure DevOps Services
Dieser Artikel ist ein vorläufiger Leitfaden für das Testen von Code in einem Data Science-Workflow. Solche Tests bieten Data Scientists eine systematische und effiziente Möglichkeit, die Qualität und das erwartete Ergebnis ihres Codes zu überprüfen. Wir verwenden ein TDSP-Projekt (Team Data Science-Prozess) mit dem [UCI Adult Income-Dataset](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome). Dieses Projekt wurde zu einem früheren Zeitpunkt veröffentlicht, um zu zeigen, wie das Codetesting funktioniert. 

## <a name="introduction-on-code-testing"></a>Einführung in das Codetesting
„Komponententests“ sind ein seit Jahren bewährtes Verfahren in der Softwareentwicklung. Auf dem Gebiet der Data Science ist es jedoch häufig nicht klar, was „Komponententest“ bedeutet und wie der Code in verschiedenen Phasen eines Data Science-Lebenszyklus wie den folgenden getestet werden sollte:

* Datenaufbereitung
* Untersuchung der Datenqualität
* Modellierung
* Modellbereitstellung 

In diesem Artikel wird der Begriff „Komponententest“ durch den Begriff „Codetest“ ersetzt. „Testing“ bezieht sich auf die Funktionen, anhand derer Sie beurteilen können, ob der Code für einen bestimmten Schritt des Data Science-Lebenszyklus die erwarteten Ergebnisse erzielt. Die Person, die den Test schreibt, definiert, was als „wie erwartet“ gilt, je nach Ergebnis der Funktion – z.B. bei der Datenqualitätsprüfung oder der Datenmodellierung.

Dieser Artikel bietet Verweise zu nützlichen Ressourcen.

## <a name="azure-devops-for-the-testing-framework"></a>Azure DevOps für das Testframework
In diesem Artikel wird beschrieben, wie Sie Tests mithilfe von Azure DevOps durchführen und automatisieren. Sie können auch andere Tools verwenden. Wir zeigen Ihnen auch, wie Sie mithilfe von Azure DevOps und Build-Agents einen automatischen Build einrichten. Als Build-Agents verwenden wir Azure Data Science Virtual Machines (DSVMs).

## <a name="flow-of-code-testing"></a>Ablauf von Codetests
Der gesamte Workflow für das Testen von Code in einem Data Science-Projekt sieht folgendermaßen aus: 

![Flussdiagramm für das Codetesting](./media/code-test/test-flow-chart.PNG)

    
## <a name="detailed-steps"></a>Ausführliche Schritte

Führen Sie die folgenden Schritte aus, um Codetests und einen automatischen Build mit einem Build-Agent und Azure DevOps einzurichten und auszuführen:

1. Erstellen Sie ein Projekt in der Visual Studio-Desktopanwendung:

    ![Bildschirm „Neues Projekt erstellen“ in Visual Studio](./media/code-test/create_project.PNG)

   Nachdem Sie das Projekt erstell haben, finden Sie es im Projektmappen-Explorer im rechten Bereich:
    
    ![Schritte zum Erstellen eines Projekts](./media/code-test/create_python_project_in_vs.PNG)

    ![Projektmappen-Explorer](./media/code-test/solution_explorer_in_vs.PNG)

1. Fügen Sie Ihren Projektcode in das Azure DevOps-Projektcoderepository ein: 

    ![Projektcoderepository](./media/code-test/create_repo.PNG)

1. Nehmen wir an, Sie hätten bereits einige Tätigkeiten zur Vorbereitung der Daten ausgeführt, z.B. Erfassen der Daten, Entwickeln von Merkmalen und Erstellen von Bezeichnungsspalten. Sie möchten sicherstellen, dass Ihr Code genau die Ergebnisse generiert, die Sie erwarten. Mit folgendem Code können Sie testen, ob der Code für die Datenverarbeitung ordnungsgemäß funktioniert:

    * Überprüfen Sie, ob die Spaltennamen richtig sind:
    
      ![Code für den Abgleich von Spaltennamen](./media/code-test/check_column_names.PNG)

    * Überprüfen Sie, ob die Antwortebenen richtig sind:

      ![Code für den Abgleich von Ebenen](./media/code-test/check_response_levels.PNG)

    * Überprüfen Sie, ob der Prozentsatz an Antworten angemessen ist:

      ![Code für den Prozentsatz an Antworten](./media/code-test/check_response_percentage.PNG)

    * Überprüfen Sie die Quote an fehlenden Werten für jede Spalte in den Daten:
    
      ![Code für die Quote an fehlenden Werten](./media/code-test/check_missing_rate.PNG)


1. Nachdem Sie die Datenverarbeitung und Merkmalsentwicklung abgeschlossen und ein gutes Modell trainiert haben, stellen Sie sicher, dass das trainierte Modell neue Datasets richtig bewerten kann. Sie können die folgenden beiden Tests verwenden, um die Vorhersageebenen und die Verteilung der Bezeichnungswerte zu überprüfen:

    * Überprüfen Sie die Vorhersageebenen:
    
      ![Code für die Überprüfung der Vorhersageebenen](./media/code-test/check_prediction_levels.PNG)

    * Überprüfen Sie die Verteilung der Vorhersagewerte:

      ![Code für die Überprüfung der Vorhersagewerte](./media/code-test/check_prediction_values.PNG)

1. Fügen Sie alle Testfunktionen in ein Python-Skript namens **test_funcs.py** ein:

    ![Python-Skript für Testfunktionen](./media/code-test/create_file_test_func.PNG)


1. Nachdem die Testcodes vorbereitet sind, können Sie die Testumgebung in Visual Studio einrichten.

   Erstellen Sie eine Python-Datei namens **test1.py**. Erstellen Sie in dieser Datei eine Klasse, die alle Tests enthält, die Sie ausführen möchten. Das folgende Beispiel zeigt sechs vorbereitete Tests:
    
    ![Python-Datei mit einer Liste von Tests in einer Klasse](./media/code-test/create_file_test1_class.PNG)

1. Diese Tests können automatisch ermittelt werden, wenn Sie **codetest.testCase** nach dem Klassennamen einfügen. Öffnen Sie den Test-Explorer im rechten Bereich, und wählen Sie **Alle ausführen** aus. Alle Tests werden nacheinander ausgeführt und informieren Sie darüber, ob der Test erfolgreich war oder nicht.

    ![Ausführen der Tests](./media/code-test/run_tests.PNG)

1. Checken Sie Ihren Code mithilfe von Git-Befehlen in das Projektrepository ein. Die letzten von Ihnen ausgeführten Tätigkeiten spiegeln sich innerhalb kurzer Zeit in Azure DevOps wider.

    ![Git-Befehle zum Einchecken von Code](./media/code-test/git_check_in.PNG)

    ![Letzte ausgeführte Tätigkeiten in Azure DevOps](./media/code-test/git_check_in_most_recent_work.PNG)

1. Richten Sie automatische Build- und Testvorgänge in Azure DevOps ein:

    a. Klicken Sie im Projektrepository auf **Erstellen und Freigeben** und dann auf **+Neu**, um einen neuen Buildprozess zu erstellen.

    ![Auswahlmöglichkeiten zum Starten eines neuen Buildprozesses](./media/code-test/create_new_build.PNG)

    b. Befolgen Sie die Anweisungen, um den Speicherort Ihres Quellcodes, den Projektnamen, das Repository und Informationen zum Branch auszuwählen.
    
    ![Quelle, Name, Repository und Branchinformationen](./media/code-test/fill_in_build_info.PNG)

    c. Wählen Sie eine Vorlage aus. Da es keine Python-Projektvorlage gibt, wählen Sie **Leerer Prozess** aus. 

    ![Liste der Vorlagen und Schaltfläche „Leerer Prozess“](./media/code-test/start_empty_process_template.PNG)

    d. Benennen Sie den Build, und wählen Sie den Agent aus. Sie können hier den Standard-Agent verwenden, wenn Sie eine DSVM zum Abschließen des Buildprozesses verwenden möchten. Weitere Informationen zum Einrichten von Agents finden Sie unter [Build- und Release-Agents](/azure/devops/pipelines/agents/agents?view=vsts).
    
    ![Auswahloptionen für Build und Agent](./media/code-test/select_agent.PNG)

    e. Wählen Sie im linken Bereich **+** aus, um einen Task für diese Buildphase hinzuzufügen. Da wir das Python-Skript **test1.py** ausführen, um alle Überprüfungen abzuschließen, wird in diesem Task zum Ausführen von Python-Code ein PowerShell-Befehl verwendet.
    
    ![Bereich zum Hinzufügen von Tasks, PowerShell ausgewählt](./media/code-test/add_task_powershell.PNG)

    f. Geben Sie für PowerShell die erforderlichen Informationen an, wie z.B. den Namen und die Version von PowerShell. Wählen Sie als Typ **Inlineskript** aus. 
    
    In das Feld unter **Inlineskript** können Sie **python test1.py** eingeben. Stellen Sie sicher, dass die Umgebungsvariable richtig für Python festgelegt ist. Wenn Sie eine andere Version oder einen anderen Kernel von Python benötigen, können Sie den entsprechenden Pfad explizit angeben, wie in dieser Abbildung gezeigt: 
    
    ![Details in PowerShell](./media/code-test/powershell_scripts.PNG)

    g. Wählen Sie **Speichern und in Warteschlange einreihen** aus, um den Buildpipelinevorgang abzuschließen.

    ![Schaltfläche „Speichern und in Warteschlange einreihen“](./media/code-test/save_and_queue_build_definition.PNG)

Jetzt wird jedes Mal der Buildprozess gestartet, wenn ein neuer Commit per Push an das Coderepository übertragen wird. Sie können einen beliebigen Branch definieren. Der Prozess führt die Datei **test1.py** auf dem Agent-Computer aus, um sicherzustellen, dass alle im Code definierten Elemente ordnungsgemäß ausgeführt werden. 

Wenn die Warnungen nicht ordnungsgemäß eingerichtet wurden, erhalten Sie nach Abschluss des Buildprozesses eine Benachrichtigung per E-Mail. Sie können den Buildstatus auch in Azure DevOps überprüfen. Wenn der Prozess nicht erfolgreich abgeschlossen wird, überprüfen Sie die Einzelheiten des Builds, um herauszufinden, welcher Bestandteil nicht richtig ist.

![E-Mail-Benachrichtigung zur erfolgreichen Builderstellung](./media/code-test/email_build_succeed.PNG)

![Benachrichtigung in Azure DevOps zur erfolgreichen Builderstellung](./media/code-test/vs_online_build_succeed.PNG)

## <a name="next-steps"></a>Nächste Schritte
* Konkrete Beispiele für Komponententests für Data Science-Szenarien finden Sie im [UCI Income Prediction-Repository](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome).
* Nutzen Sie die oben genannten Verfahren und Beispiele des UCI-Szenarios zur Vorhersage des Einkommens in Ihren eigenen Data Science-Projekten.

## <a name="references"></a>References
* [Team Data Science-Prozess](./index.yml)
* [Visual Studio-Testtools](https://www.visualstudio.com/vs/features/testing-tools/)
* [Azure DevOps-Testressourcen](https://www.visualstudio.com/team-services/)
* [Data Science Virtual Machines](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)