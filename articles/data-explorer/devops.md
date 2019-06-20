---
title: Azure DevOps-Aufgabe für Azure Data Explorer
description: In diesem Thema erfahren Sie, wie Sie eine Releasepipeline erstellen und bereitstellen.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/05/2019
ms.openlocfilehash: 0628d5c07d7258cc4d68727c364e65bd81c78e8e
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388999"
---
# <a name="azure-devops-task-for-azure-data-explorer"></a>Azure DevOps-Aufgabe für Azure Data Explorer

[Azure DevOps Services](https://azure.microsoft.com/services/devops/) bietet Tools für die Entwicklungszusammenarbeit wie leistungsstarke Pipelines, kostenlose private Git-Repositorys, konfigurierbare Kanban-Boards und umfangreiche automatisierte und kontinuierliche Testfunktionen. [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) ist eine Azure DevOps-Funktion, die es Ihnen ermöglicht, CI/CD zu verwalten, um Ihren Code mit leistungsstarken Pipelines bereitzustellen, die mit jeder Sprache, Plattform und Cloud funktionieren.
[Azure Data Explorer - Admin Commands](https://marketplace.visualstudio.com/items?itemName=Azure-Kusto.PublishToADX) ist die Azure Pipelines-Aufgabe, mit der Sie Releasepipelines erstellen und Ihre Datenbankänderungen in Ihren Azure Data Explorer-Datenbanken bereitstellen können. Diese finden Sie kostenlos im [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

Dieses Dokument beschreibt ein einfaches Beispiel für die Verwendung der Aufgabe **Azure Data Explorer – Admin Commands** zum Bereitstellen Ihrer Schemaänderungen in Ihrer Datenbank. Vollständige CI/CD-Pipelines finden Sie unter [Azure DevOps-Dokumentation](/azure/devops/user-guide/what-is-azure-devops?view=azure-devops#vsts).

## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* Setup eines Azure Data Explorer-Clusters:
    * Ein [Azure Data Explorer-Cluster und eine Datenbank](/azure/data-explorer/create-cluster-database-portal)
    * Erstellen Sie eine Azure Active Directory (Azure AD)-App mit [Bereitstellung einer Azure AD-Anwendung](/azure/kusto/management/access-control/how-to-provision-aad-app).
    * Gewährleisten Sie den Zugriff auf Ihre Azure AD-App in Ihrer Azure Data Explorer-Datenbank, indem Sie [die Berechtigungen der Azure Data Explorer-Datenbank verwalten](/azure/data-explorer/manage-database-permissions).
* Azure DevOps-Setup:
    * [Für eine kostenlose Organisation registrieren](/azure/devops/user-guide/sign-up-invite-teammates?view=azure-devops)
    * [Eine Organisation erstellen](/azure/devops/organizations/accounts/create-organization?view=azure-devops)
    * [Erstellen eines Projekts in Azure DevOps](/azure/devops/organizations/projects/create-project?view=azure-devops)
    * [Mit Git codieren](/azure/devops/user-guide/code-with-git?view=azure-devops)

## <a name="create-folders"></a>Erstellen Sie die Ordner.

Erstellen Sie die folgenden Beispielordner (*Funktionen*, *Richtlinien*, *Tabellen*) in Ihrem Git-Repository. Kopieren Sie die Dateien von [hier](https://github.com/Azure/azure-kusto-docs-samples/tree/master/DevOps_release_pipeline) in die entsprechenden Ordner wie unten gezeigt, und übertragen Sie die Änderungen. Die Beispieldateien werden zur Verfügung gestellt, um den folgenden Workflow auszuführen.

![Erstellen Sie die Ordner.](media/devops/create-folders.png)

> [!TIP]
> Bei der Erstellung eines eigenen Workflows empfehlen wir, den Code idempotent zu gestalten. Verwenden Sie beispielsweise [.create-merge table](/azure/kusto/management/tables#create-merge-tables) anstelle von [.create table](/azure/kusto/management/tables#create-table) und [.create-or-alter](/azure/kusto/management/functions#create-or-alter-function)-Funktion anstelle der [.create](/azure/kusto/management/functions#create-function)-Funktion.

## <a name="create-a-release-pipeline"></a>Erstellen einer Releasepipeline

1. Melden Sie sich bei Ihrer [Azure DevOps-Organisation](https://dev.azure.com/) an.
1. Wählen Sie **Pipelines** > **Releases** aus, und wählen im linken Menü **Neue Pipeline**.

    ![Neue Pipeline](media/devops/new-pipeline.png)

1. Das Fenster **Neue Releasepipeline** wird geöffnet. Wählen Sie auf der Registerkarte **Pipelines** im Bereich **Eine Vorlage auswählen** die Option **Leerer Auftrag** aus.

     ![Vorlage auswählen](media/devops/select-template.png)

1. Klicken Sie auf die Schaltfläche **Stufe**. Fügen Sie im Bereich **Stufe** den **Namen der Stufe** hinzu. Klicken Sie auf **Speichern**, um Ihre Pipeline zu speichern.

    ![Stufe benennen](media/devops/stage-name.png)

1. Klicken Sie auf die Schaltfläche **Artefakt hinzufügen**. Wählen Sie im Bereich **Artefakt hinzufügen** das Repository aus, in dem sich Ihr Code befindet, geben Sie die relevanten Informationen ein, und klicken Sie auf **Hinzufügen**. Klicken Sie auf **Speichern**, um Ihre Pipeline zu speichern.

    ![Hinzufügen eines Artefakts](media/devops/add-artifact.png)

1. Wählen Sie auf der Registerkarte **Variablen** **+ Hinzufügen**, um eine Variable für **Endpunkt-URL** zu erstellen, die in der Aufgabe verwendet wird. Geben Sie den **Namen** und den **Wert** des Endpunkts ein. Klicken Sie auf **Speichern**, um Ihre Pipeline zu speichern. 

    ![Variable erstellen](media/devops/create-variable.png)

    Um Ihre Endpoint_URL zu finden, enthält die Übersichtsseite Ihres **Azure Data Explorer-Cluster** im Azure-Portal die URI des Azure Data Explorer-Cluster. Erstellen Sie den URI im folgenden Format `https://<Azure Data Explorer cluster URI>?DatabaseName=<DBName>`.  Beispiel: https:\//kustodocs.westus.kusto.windows.net?DatabaseName=SampleDB

    ![Azure Data Explorer-Cluster-URI](media/devops/adx-cluster-uri.png)

## <a name="create-tasks-to-deploy"></a>Erstellen von Aufgaben zum Bereitstellen

1. Klicken Sie auf der Registerkarte **Pipeline** auf **1 Auftrag, 0 Aufgabe**, um Aufgaben hinzuzufügen. 

    ![Hinzufügen von Aufgaben](media/devops/add-task.png)

1. Erstellen Sie drei Aufgaben, um **Tabellen**, **Funktionen** und **Richtlinien** (in dieser Reihenfolge) bereitzustellen. 

1. Wählen Sie auf der Registerkarte **Aufgaben** **+** neben **Agent-Auftrag**. Suchen Sie nach **Azure Data Explorer**. Installieren Sie unter **Marktplatz** die Erweiterung **Azure Data Explorer – Admin Commands**. Wählen Sie dann **Hinzufügen** unter **Azure Data Explorer-Befehl ausführen**.

     ![Administratorbefehle hinzufügen](media/devops/add-admin-commands.png)

1. Klicken Sie links auf **Kusto-Befehl**, und aktualisieren Sie die Aufgabe mit den folgenden Informationen:
    * **Anzeigename**: Der Name der Aufgabe.
    * **Dateipfad**: Geben Sie in der Aufgabe **Tabellen** */Tables/* .csl an, da sich die Dateien zur Tabellenerstellung im Ordner *Tabelle* befinden.
    * **Endpunkt-URL**: geben Sie die im vorherigen Schritt erstellte `EndPoint URL`-Variable ein.
    * Wählen Sie **Dienstendpunkt verwenden**, und wählen Sie **+ Neu**.

    ![Kusto-Befehlsaufgabe aktualisieren](media/devops/kusto-command-task.png)

1. Geben Sie die folgenden Informationen im Fenster **Data Explorer-Dienstverbindung hinzufügen** ein:

    |Einstellung  |Empfohlener Wert  |
    |---------|---------|
    |**Verbindungsname**     |    Geben Sie den Namen zum Identifizieren dieses Dienstendpunkts ein.     |
    |**Cluster-URL**    |    Den Wert finden Sie im Übersichtsbereich Ihres Azure Data Explorer-Clusters im Azure-Portal. | 
    |**Dienstprinzipal-ID**    |    Geben Sie die AAD-App-ID (als Voraussetzung erstellt) ein.     |
    |**Dienstprinzipal-App-Schlüssel**     |    Geben Sie den AAD-App-Schlüssel (als Voraussetzung erstellt) ein.    |
    |**ID des AAD-Mandanten**    |      Geben Sie Ihren AAD-Mandanten ein (z.B. „microsoft.com“, „contoso.com“...)    |

    Aktivieren Sie das Kontrollkästchen **Nutzung dieser Verbindung für alle Pipelines erlauben**. Klicken Sie auf **OK**.

    ![Dienstverbindung hinzufügen](media/devops/add-service-connection.png)

1. Wiederholen Sie die Schritte 1-5 zweimal, um Dateien aus den Ordnern *Funktionen* und *Richtlinien* bereitzustellen. Wählen Sie **Speichern** aus. Auf der Registerkarte **Aufgaben** finden Sie die drei erstellten Aufgaben: **Bereitstellen von Tabellen**, **Bereitstellen von Funktionen** und **Bereitstellen von Richtlinien**.

    ![Alle Ordner bereitstellen](media/devops/deploy-all-folders.png)

1. Wählen Sie **+ Release** > **Release erstellen**, um ein Release zu erstellen.

    ![Erstellen eines Release](media/devops/create-release.png)

1. Überprüfen Sie auf der Registerkarte **Protokolle**, ob die Bereitstellung erfolgreich war.

    ![Bereitstellung erfolgreich](media/devops/deployment-successful.png)

Sie haben nun die Erstellung einer Releasepipeline für die Bereitstellung von drei Aufgaben in der Präproduktion abgeschlossen.