---
title: 'Integration der Quellcodeverwaltung in Azure Automation: Legacy'
description: In diesem Artikel wird die Integration der Quellcodeverwaltung mit GitHub in Azure Automation erläutert.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/01/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c95af40c3fa3f9dad2bfb5ea4a1b9f585c636928
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60881128"
---
# <a name="source-control-integration-in-azure-automation---legacy"></a>Integration der Quellcodeverwaltung in Azure Automation: Legacy

> [!NOTE]
> Es gibt eine neue Umgebung für die Quellcodeverwaltung. Weitere Informationen zur neuen Umgebung finden Sie unter [Integration der Quellcodeverwaltung in Azure Automation](source-control-integration.md).

Durch die Integration der Quellcodeverwaltung können Sie Runbooks in Ihrem Automation-Konto einem GitHub-Repository für die Quellcodeverwaltung zuordnen. Sie können mit der Quellcodeverwaltung einfach mit Ihrem Team zusammenarbeiten, Änderungen nachverfolgen und Ihre Runbooks auf frühere Versionen zurücksetzen. Die Quellcodeverwaltung ermöglicht beispielsweise das Synchronisieren verschiedener Verzweigungen der Quellcodeverwaltung Ihrer Automation-Konten für Entwicklung, Test oder Produktion. Dadurch können Sie Code, der in Ihrer Entwicklungsumgebung getestet wurde, in Ihr Automation-Produktionskonto hochstufen.

Mit der Quellcodeverwaltung können Sie Code aus Azure Automation in die Quellcodeverwaltung übertragen oder Ihre Runbooks aus der Quellcodeverwaltung in Azure Automation verschieben. Dieser Artikel beschreibt die Quellcodeverwaltung in Ihrer Azure-Automation-Umgebung. Wir beginnen zunächst mit dem Konfigurieren von Azure Automation, um Zugriff auf Ihr GitHub-Repository zu erhalten, und durchlaufen dann verschiedene Vorgänge, die mithilfe einer Integration der Quellcodeverwaltung möglich sind. 

> [!NOTE]
> Die Quellcodeverwaltung unterstützt das Abrufen und Verschieben von [PowerShell-Workflow-Runbooks](automation-runbook-types.md#powershell-workflow-runbooks) sowie [PowerShell-Runbooks](automation-runbook-types.md#powershell-runbooks). [Grafische Runbooks](automation-runbook-types.md#graphical-runbooks) werden noch nicht unterstützt.

Sie müssen nur zwei Schritte ausführen, um die Quellcodeverwaltung für Ihr Automation-Konto zu konfigurieren. Wenn Sie ein GitHub-Konto besitzen, ist nur ein Schritt erforderlich. Sie lauten wie folgt:

## <a name="step-1--create-a-github-repository"></a>Schritt 1: Erstellen eines GitHub-Repositorys

Wenn Sie bereits ein GitHub-Konto und ein Repository besitzen, das Sie mit Azure Automation verknüpfen möchten, dann melden Sie sich bei Ihrem vorhandenen Konto an, und beginnen Sie mit Schritt 2. Navigieren Sie andernfalls zu [GitHub](https://github.com/), registrieren Sie sich für ein neues Konto, und [erstellen Sie ein neues Repository](https://help.github.com/articles/create-a-repo/).

## <a name="step-2--set-up-source-control-in-azure-automation"></a>Schritt 2: Einrichten der Quellcodeverwaltung in Azure Automation

1. Klicken Sie im Azure-Portal auf der Seite „Automation-Konto“ unter **Kontoeinstellungen** auf **Quellcodeverwaltung**.

2. Die Seite **Quellcodeverwaltung** wird geöffnet. Hier können Sie die Details Ihres GitHub-Kontos konfigurieren. Nachfolgend sehen Sie die Liste der zu konfigurierenden Parameter:  

   | **Parameter** | **Beschreibung** |
   |:--- |:--- |
   | Quelle auswählen |Wählen Sie die Quelle aus. Derzeit wird nur **GitHub** unterstützt. |
   | Autorisierung |Klicken Sie auf die Schaltfläche **Autorisieren** , um Azure Automation Zugriff auf Ihr GitHub-Repository zu gewähren. Wenn Sie bereits in einem anderen Fenster bei Ihrem GitHub-Konto angemeldet sind, werden die Anmeldeinformationen dieses Kontos verwendet. Nach erfolgreicher Autorisierung zeigt die Seite unter **Authorization Property** (Autorisierungseigenschaft) Ihren GitHub-Benutzernamen an. |
   | Repository auswählen |Wählen Sie ein GitHub-Repository aus der Liste der verfügbaren Repositorys aus. |
   | Verzweigung auswählen |Wählen Sie eine Verzweigung aus der Liste der verfügbaren Verzweigungen aus. Wenn Sie keine Verzweigungen erstellt haben, wird nur die **master** -Verzweigung angezeigt. |
   | Runbook-Ordnerpfad |Der Runbook-Ordnerpfad gibt den Pfad im GitHub-Repository zum Abrufen und Verschieben Ihres Codes an. Er muss im Format **/Ordnername/Unterordnername**eingegeben werden. Nur Runbooks im Runbook-Ordnerpfad werden mit Ihrem Automation-Konto synchronisiert. Runbooks in den Unterordnern des Runbook-Ordnerpfads werden **NICHT** synchronisiert. Mit **/** synchronisieren Sie alle Runbooks im Repository. |
3. Nehmen wir an, Sie haben ein Repository **PowerShell-Skripts** genannt. Dieses enthält einen Ordner namens **Stammordner**, der wiederum einen Ordner namens **Unterordner** enthält. Mit den folgenden Zeichenfolgen können Sie jede der Ordnerebenen synchronisieren:

   1. Der Pfad zum Synchronisieren von Runbooks im gesamten **Repository**lautet */*
   2. Der Pfad zum Synchronisieren von Runbooks im gesamten **Stammordner**lautet */Stammordner*
   3. Der Pfad zum Synchronisieren von Runbooks im **Unterordner**lautet */Stammordner/Unterordner*.
4. Nach dem Konfigurieren der Parameter werden sie auf der Seite **Quellcodeverwaltung einrichten** angezeigt.  

    ![Seite der Quellcodeverwaltung mit den Einstellungen](media/source-control-integration-legacy/automation-SourceControlConfigure.png)
5. Nach dem Klicken auf **OK** ist die Integration der Quellcodeverwaltung für Ihr Automation-Konto konfiguriert und sollte mit Ihren GitHub-Informationen aktualisiert werden. Sie können jetzt auf diesen Teil klicken, um den gesamten Verlauf Ihrer Synchronisierungsaufträge für die Quellcodeverwaltung anzuzeigen.  

    ![Werte für die aktuelle Konfiguration der Quellcodeverwaltung](media/source-control-integration-legacy/automation-RepoValues.png)
6. Nach dem Einrichten der Quellcodeverwaltung werden zwei [Variablenobjekte](automation-variables.md) in Ihrem Azure Automation-Konto erstellt. Darüber hinaus wird eine autorisierte Anwendung zu Ihrem GitHub-Konto hinzugefügt.

   * Die Variable **Microsoft.Azure.Automation.SourceControl.Connection** enthält die Werte der Verbindungszeichenfolge (wie unten dargestellt).  

     | **Parameter** | **Wert** |
     |:--- |:--- |
     | `Name`  |Microsoft.Azure.Automation.SourceControl.Connection |
     | `Type`  |string |
     | `Value` |{„Verzweigung“:\<*Name Ihrer Verzweigung*>, „Runbook-Ordnertyp“:\<*Runbook-Ordnerpfad*>, „Anbietertyp“:\<*hat einen Wert 1 für GitHub*>, „Verzeichnis“:\<*Name Ihres Repositorys*>, „Benutzername“:\<*Ihr GitHub-Benutzername*>} |

   * Die Variable **Microsoft.Azure.Automation.SourceControl.OauthToken**enthält den sicher verschlüsselten Wert Ihres OAuth-Tokens.  

     |**Parameter**            |**Wert** |
     |:---|:---|
     | `Name`  | Microsoft.Azure.Automation.SourceControl.OauthToken |
     | `Type`  | Unbekannte (Verschlüsselt) |
     | `Value` | <*Verschlüsseltes OAuthToken*> |  

     ![Fenster mit den Variablen der Quellcodeverwaltung](media/source-control-integration-legacy/automation-Variables.png)  

   * **Automation-Quellcodeverwaltung** wird Ihrem GitHub-Konto als autorisierte Anwendung hinzugefügt. So zeigen Sie die Anwendung an Navigieren Sie auf Ihrer GitHub-Homepage zu **Profile** > **Settings** > **Applications** (Profil > Einstellungen > Anwendungen). Diese Anwendung ermöglicht Azure-Automation die Synchronisierung Ihres GitHub-Repositorys mit einem Automation-Konto.  

     ![Anwendungseinstellungen in GitHub](media/source-control-integration-legacy/automation-GitApplication.png)

## <a name="using-source-control-in-automation"></a>Verwenden der Quellcodeverwaltung in Automation

### <a name="check-in-a-runbook-from-azure-automation-to-source-control"></a>Einchecken eines Runbooks aus Azure Automation in die Quellcodeverwaltung

Durch Einchecken des Runbooks können Sie die Änderungen, die Sie an einem Runbook in Azure Automation vorgenommen haben, in das Quellcodeverwaltungs-Repository übertragen. Mit den folgenden Schritten können Sie ein Runbook einchecken:

1. In Ihrem Automation-Konto können Sie [ein neues Textrunbook erstellen](automation-first-runbook-textual.md) oder [ein vorhandenes Textrunbook bearbeiten](automation-edit-textual-runbook.md). Dieses Runbook kann ein PowerShell-Workflow oder eines PowerShell-Skript-Runbook sein.  
2. Speichern Sie Ihr Runbook nach dem Bearbeiten, und klicken Sie auf der Seite **Bearbeiten** auf **Einchecken**.  

    ![Fenster mit der Schaltfläche zum Anmelden bei GitHub](media/source-control-integration-legacy/automation-CheckinButton.png)

     > [!NOTE] 
     > Beim Einchecken aus Azure Automation wird der derzeit in Ihrer Quellcodeverwaltung vorhandene Code überschrieben. Die entsprechende Git-Befehlszeilenanweisung zum Einchecken lautet **git add + git commit + git push**.  

3. Wenn Sie auf **Einchecken** klicken, wird eine Bestätigungsmeldung angezeigt. Klicken Sie zum Fortfahren auf **Ja**.  

    ![Dialogfeld zur Bestätigung der Anmeldung bei der Quellcodeverwaltung](media/source-control-integration-legacy/automation-CheckinMessage.png)
4. Der Check-In startet das Quellcodeverwaltungs-Runbook **Sync-MicrosoftAzureAutomationAccountToGitHubV1**. Dieses Runbook stellt eine Verbindung zu GitHub her und überträgt Änderungen in Azure Automation in Ihr Repository. Wenn Sie den Verlauf eingecheckter Aufträge anzeigen möchten, wechseln Sie zurück zur Registerkarte **Integration der Quellcodeverwaltung**, und klicken Sie, um die Seite „Repositorysynchronisierung“ zu öffnen. Diese Seite enthält alle Quellcodeverwaltungsaufträge.  Wählen Sie den Auftrag, den Sie anzeigen möchten, und klicken Sie, um die Details anzuzeigen.  

    ![Fenster mit den Ergebnissen eines Synchronisierungsauftrags](media/source-control-integration-legacy/automation-CheckinRunbook.png)

   > [!NOTE]
   > Quellcodeverwaltungs-Runbooks sind spezielle Automation-Runbooks, die Sie weder anzeigen noch bearbeiten können. Obwohl sie nicht in Ihrer Runbookliste angezeigt werden, enthält die Auftragsliste Synchronisierungsaufträge.

5. Der Name des geänderten Runbooks wird als Eingabeparameter an das eingecheckte Runbook gesendet. Sie können die [Auftragsdetails anzeigen](automation-runbook-execution.md#viewing-job-status-from-the-azure-portal), indem Sie das Runbook auf der Seite **Repositorysynchronisierung** erweitern.  

    ![Fenster mit der Eingabe für einen Synchronisierungsauftrag](media/source-control-integration-legacy/automation-CheckinInput.png)
6. Aktualisieren Sie das GitHub-Repository nach Abschluss des Auftrags, um die Änderungen anzuzeigen.  Ihr Repository sollte einen Commit mit der Meldung enthalten, dass eine **Aktualisierung von *Runbookname* in Azure Automation** erfolgt ist.  

### <a name="sync-runbooks-from-source-control-to-azure-automation"></a>Synchronisieren von Runbooks aus der Quellcodeverwaltung in Azure Automation

Mit der Synchronisierungsschaltfläche auf der Seite „Repositorysynchronisierung“ können Sie alle Runbooks im Runbook-Ordnerpfad des Repositorys in Ihr Automation-Konto verschieben. Dasselbe Repository kann mit mehreren Automation-Konten synchronisiert werden. Mit den folgenden Schritten können Sie ein Runbook synchronisieren:

1. Öffnen Sie in dem Automation-Konto, in dem Sie die Quellcodeverwaltung einrichten, die Seite **Integration der Quellcodeverwaltung/Repositorysynchronisierung**, und klicken Sie auf **Synchronisieren**.  Klicken Sie in der angezeigten Bestätigungsmeldung auf **Ja**, um den Vorgang fortzusetzen.  

    ![Schaltfläche "Synchronisieren" mit Bestätigungsmeldung, dass alle Runbooks synchronisiert werden](media/source-control-integration-legacy/automation-SyncButtonwithMessage.png)

2. Die Synchronisierung startet das Runbook **Sync-MicrosoftAzureAutomationAccountFromGitHubV1**. Dieses Runbook stellt eine Verbindung zu GitHub her und überträgt die Änderungen aus Ihrem Repository in Azure Automation. Sie sollten daraufhin einen neuen Auftrag auf der Seite **Repositorysychronisierung** für diese Aktion sehen. Um Details zum Synchronisierungsauftrag anzuzeigen, klicken Sie, um die Seite mit den Auftragsdetails zu öffnen.  

    ![Fenster mit den Synchronisierungsergebnissen eines Synchronisierungsauftrags für ein GitHub-Repository](media/source-control-integration-legacy/automation-SyncRunbook.png)

    > [!NOTE]
    > Die Synchronisierung der Quellcodeverwaltung überschreibt die Entwurfsversion für **ALLE** Runbooks in Ihrem Automation-Konto, die derzeit in der Quellcodeverwaltung vorhanden sind. Die entsprechende Git-Befehlszeilenanweisung zum Synchronisieren lautet **git pull**.

![Fenster mit allen Protokollen eines angehaltenen Synchronisierungsauftrags für die Quellcodeverwaltung](media/source-control-integration-legacy/automation-AllLogs.png)

## <a name="disconnecting-source-control"></a>Trennen der Verbindung zur Quellcodeverwaltung

Öffnen Sie zum Trennen der Verbindung mit Ihrem GitHub-Konto die Seite „Repositorysynchronisierung“, und klicken Sie auf **Trennen**. Nach dem Trennen der Verbindung mit der Quellcodeverwaltung verbleiben zuvor synchronisierte Runbooks weiterhin in Ihrem Automation-Konto, aber die Seite „Repositorysynchronisierung“ ist nicht aktiviert.  

  ![Fenster mit der Schaltfläche „Trennen“ zum Trennen der Quellcodeverwaltung](media/source-control-integration-legacy/automation-Disconnect.png)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Integration einer Quellcodeverwaltung finden Sie unter den folgenden Ressourcen:  

* [Azure Automation: Integration der Quellcodeverwaltung in Azure Automation](https://azure.microsoft.com/blog/azure-automation-source-control-13/)  
* [Stimmen Sie für Ihr bevorzugtes Quellcodeverwaltungssystem ab.](https://www.surveymonkey.com/r/?sm=2dVjdcrCPFdT0dFFI8nUdQ%3d%3d)  
* [Azure Automation: Integrating Runbook Source Control using Visual Studio Online](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/) (Azure Automation: Integrieren der Runbook-Quellcodeverwaltung mithilfe von Visual Studio Online)  
