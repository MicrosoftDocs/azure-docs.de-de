---
title: Continuous Integration und Continuous Delivery für Synapse-Arbeitsbereiche
description: Erfahren Sie, wie Sie Continuous Integration und Continuous Delivery verwenden, um Änderungen an einem Arbeitsbereich aus einer Umgebung (Entwicklung, Test, Produktion) in eine andere zu überführen.
services: synapse-analytics
author: liud
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: 5dbd49312b58dc656e2239e8a0a4acea614023de
ms.sourcegitcommit: e5f9126c1b04ffe55a2e0eb04b043e2c9e895e48
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96317135"
---
# <a name="continuous-integration-and-delivery-for-azure-synapse-workspace"></a>Continuous Integration und Continuous Delivery für Azure Synapse-Arbeitsbereiche

## <a name="overview"></a>Übersicht

Continuous Integration (CI) ist der Prozess, mit dem das Erstellen und Testen von Code bei jedem Committen von Änderungen durch Teammitglieder an die Versionskontrolle automatisiert werden kann. Continuous Deployment (CD) bezeichnet das Erstellen, Testen, Konfigurieren und Bereitstellen aus mehreren Test- oder Stagingumgebungen in einer Produktionsumgebung.

Bei Azure Synapse-Arbeitsbereichen dienen Continuous Integration und Continuous Delivery (CI/CD) dazu, sämtliche Entitäten aus einer Umgebung (Entwicklung, Test, Produktion) in eine andere zu verschieben. Um Ihren Arbeitsbereich auf einen anderen Arbeitsbereich höher zu stufen, müssen Sie zwei Schritte ausführen: Verwenden Sie [Azure Resource Manager-Vorlagen](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview), um Arbeitsbereichsressourcen (Pools und Arbeitsbereich) zu erstellen oder zu aktualisieren, und migrieren Sie Artefakte (SQL-Skripts, Notebooks, Spark-Auftragsdefinitionen, Pipelines, Datasets, Datenflüsse usw.) mit Synapse-CI/CD-Tools zu Azure DevOps. 

In diesem Artikel wird die Verwendung von Azure-Releasepipelines zum Automatisieren der Bereitstellung eines Synapse-Arbeitsbereichs in mehreren Umgebungen beschrieben.

## <a name="prerequisites"></a>Voraussetzungen

-   Der für die Entwicklung verwendete Arbeitsbereich wurde mit einem Git-Repository in Studio konfiguriert. Informationen hierzu finden Sie unter [Quellcodeverwaltung in Synapse Studio](source-control.md).
-   Ein Azure DevOps-Projekt wurde für die Ausführung der Releasepipeline vorbereitet.

## <a name="set-up-a-release-pipelines"></a>Einrichten einer Releasepipeline

1.  Öffnen Sie in [Azure DevOps](https://dev.azure.com/) das Projekt, das für das Release erstellt wurde.

1.  Wählen Sie links auf der Seite die Option **Pipelines** und dann **Releases** aus.

    ![Auswählen von „Pipelines“ > „Releases“](media/create-release-1.png)

1.  Wählen Sie **Neue Pipeline** oder – falls Pipelines vorhanden sind – die Option **Neu** und dann **Neue Releasepipeline** aus.

1.  Wählen Sie die Vorlage **Leere Stufe** aus.

    ![Auswählen von „Leere Stufe“](media/create-release-select-empty.png)

1.  Geben Sie im Feld **Name der Stufe** den Namen der Umgebung ein.

1.  Wählen Sie **Artefakt hinzufügen** und dann das Git-Repository aus, das bei der Entwicklung in Synapse Studio konfiguriert wurde. Wählen Sie das Git-Repository aus, das Sie zum Verwalten der ARM-Vorlage für Pools und den Arbeitsbereich verwendet haben. Wenn Sie als Quelle GitHub verwenden, müssen Sie eine Dienstverbindung für Ihr GitHub-Konto erstellen und Repositorys pullen. Weitere Informationen zur [Dienstverbindung](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints) 

    ![Hinzufügen eines Branches für die Veröffentlichung](media/release-creation-github.png)

1.  Wählen Sie den Branch der ARM-Vorlage für das Ressourcenupdate aus. Wählen Sie unter **Standardversion** die Option **Letztes Element aus Standardbranch** aus.

    ![Hinzufügen einer ARM-Vorlage](media/release-creation-arm-branch.png)

1.  Wählen Sie den [Branch für die Veröffentlichung](source-control.md#configure-publishing-settings) des Repositorys für den **Standardbranch** aus. Dieser Branch für die Veröffentlichung lautet standardmäßig `workspace_publish`. Wählen Sie unter **Standardversion** die Option **Letztes Element aus Standardbranch** aus.

    ![Hinzufügen eines Artefakts](media/release-creation-publish-branch.png)

## <a name="set-up-a-stage-task-for-arm-resource-create-and-update"></a>Einrichten einer Stagingaufgabe für das Erstellen und Aktualisieren von ARM-Ressourcen 

Fügen Sie eine Azure Resource Manager-Bereitstellungsaufgabe hinzu, um Ressourcen wie den Arbeitsbereich und Pools zu erstellen oder zu aktualisieren:

1. Wählen Sie in der Stufenansicht die Option **Stufenaufgaben anzeigen** aus.

    ![Stufenansicht](media/release-creation-stage-view.png)

1. Erstellen Sie eine neue Aufgabe. Suchen Sie nach **AMR-Vorlagenbereitstellung**, und wählen Sie dann **Hinzufügen** aus.

1. Wählen Sie in der Bereitstellungsaufgabe das Abonnement, die Ressourcengruppe und den Speicherort für den Zielarbeitsbereich aus. Geben Sie die Anmeldeinformationen an, falls dies erforderlich ist.

1. Wählen Sie in der Liste **Aktion** den Eintrag **Create or update resource group** (Ressourcengruppe erstellen oder aktualisieren) aus.

1. Wählen Sie neben dem Feld **Vorlage** die Schaltfläche mit den Auslassungszeichen ( **…** ) aus. Suchen Sie nach der Azure Resource Manager-Vorlage für Ihren Zielarbeitsbereich.

1. Wählen Sie **…** neben dem Feld **Vorlagenparameter** aus, um die Parameterdatei auszuwählen.

1. Wählen Sie **…** neben dem Feld **Vorlagenparameter überschreiben** aus, und geben Sie die gewünschten Parameterwerte für den Zielarbeitsbereich ein. 

1. Wählen Sie unter **Bereitstellungsmodus** die Option **Inkrementell** aus.
    
    ![Bereitstellen von Arbeitsbereich und Pools](media/pools-resource-deploy.png)

1. (Optional:) Fügen Sie **Azure PowerShell** hinzu, um die Rollenzuweisung für den Arbeitsbereich festzulegen und zu aktualisieren. Wenn Sie einen Synapse-Arbeitsbereich mit der Releasepipeline erstellen, wird der Dienstprinzipal der Pipeline als Standardadministrator für den Arbeitsbereich hinzugefügt. Sie können PowerShell ausführen, um auch anderen Konten Zugriff auf den Arbeitsbereich zu gewähren. 
    
    ![Erteilen einer Berechtigung](media/release-creation-grant-permission.png)

 > [!WARNING]
> Im vollständigen Bereitstellungsmodus werden Ressourcen, die in der Ressourcengruppe vorhanden, aber in der neuen Resource Manager-Vorlage nicht angegeben sind, **gelöscht**. Weitere Informationen finden Sie unter [Azure Resource Manager-Bereitstellungsmodi](https://docs.microsoft.com/azure/azure-resource-manager/templates/deployment-modes).

## <a name="set-up-a-stage-task-for-artifacts-deployment"></a>Einrichten einer Stagingaufgabe für die Artefaktbereitstellung 

Verwenden Sie die Erweiterung für die [Synapse-Arbeitsbereichsbereitstellung](https://marketplace.visualstudio.com/items?itemName=AzureSynapseWorkspace.synapsecicd-deploy), um andere Elemente im Synapse-Arbeitsbereich wie Datasets, SQL-Skripts, Notebooks, Spark-Auftragsdefinitionen, Datenflüsse, Pipelines, verknüpfte Dienste, Anmeldeinformationen und IRs (Integration Runtime) bereitzustellen.  

1. Suchen Sie die Erweiterung im **Azure DevOps-Marketplace** (https://marketplace.visualstudio.com/azuredevops), und laden Sie sie herunter. 

     ![Abrufen einer Erweiterung](media/get-extension-from-market.png)

1. Wählen Sie eine Organisation aus, um die Erweiterung zu installieren. 

     ![Installieren der Erweiterung](media/install-extension.png)

1. Stellen Sie sicher, dass dem Dienstprinzipal der Azure DevOps-Pipeline die Berechtigung des Abonnements erteilt wurde und dass er auch als Arbeitsbereichsadministrator für den Zielarbeitsbereich zugewiesen wurde. 

1. Erstellen Sie eine neue Aufgabe. Suchen Sie nach **Synapse-Arbeitsbereichsbereitstellung**, und wählen Sie **Hinzufügen** aus.

     ![Erweiterung hinzufügen](media/add-extension-task.png)

1.  Geben Sie in der Aufgabe die entsprechenden Informationen zum Git-Repository von **workspace_publish** an, und wählen Sie die Ressourcengruppe, die Region, den Namen und die Cloudumgebung für den Zielarbeitsbereich aus. Geben Sie bei Bedarf Parameter und Werte an.

    ![Synapse-Arbeitsbereichsbereitstellung](media/create-release-artifacts-deployment.png)

> [!IMPORTANT]
> In CI/CD-Szenarien muss der Typ der Integration Runtime (IR) in unterschiedlichen Umgebungen identisch sein. Wenn sich in Ihrer Entwicklungsumgebung beispielsweise eine selbstgehostete IR befindet, muss diese IR auch in anderen Umgebungen, z. B. Test- und Produktionsumgebungen, den Typ „Selbstgehostet“ aufweisen. Wenn Sie Integration Runtimes in mehreren Stufen freigeben, müssen Sie die Integration Runtimes in allen Umgebungen, z. B. Entwicklungs-, Test- und Produktionsumgebungen, als „Verknüpft selbstgehostet“ konfigurieren.

## <a name="create-release-for-deployment"></a>Erstellen eines Release für die Bereitstellung 

Nachdem alle Änderungen gespeichert wurden, können Sie **Release erstellen** auswählen, um manuell ein Release zu erstellen. Informationen zum Automatisieren der Erstellung von Releases finden Sie unter [Azure DevOps-Releasetrigger](https://docs.microsoft.com/azure/devops/pipelines/release/triggers).

   ![Auswählen von „Release erstellen“](media/release-creation-manually.png)

## <a name="best-practices-for-cicd"></a>Bewährte Methoden für CI/CD

Wenn Sie die Git-Integration mit Ihre, Synapse-Arbeitsbereich verwenden und über eine CI/CD-Pipeline verfügen, die Ihre Änderungen aus der Entwicklungs- in die Test- und dann in die Produktionsumgebung verschiebt, empfehlen wir Ihnen diese bewährten Methoden:

-   **Git-Integration**. Konfigurieren Sie nur Ihren Synapse-Arbeitsbereich für die Entwicklung mit Git-Integration. Änderungen an den Test- und Produktionsarbeitsbereichen werden über CI/CD bereitgestellt, und eine Git-Integration wird hierfür nicht benötigt.
-   **Bereiten Sie vor der Migration von Artefakten Pools vor.** Wenn Sie über SQL-Skripts oder Notebooks verfügen, die im Entwicklungsarbeitsbereich an Pools angefügt sind, werden in den anderen Umgebungen dieselben Poolnamen erwartet. 
-   **Infrastruktur als Code (Infrastructure-as-Code, IaC).** Verwenden Sie für die Verwaltung von Infrastruktur (Netzwerken, VMs, Lastenausgleichsmodulen und der Verbindungstopologie) in einem beschreibenden Modell die gleiche Versionsverwaltung, die das DevOps-Team für den Quellcode nutzt. 
-   **Sonstige.** Weitere Informationen finden Sie unter [Bewährte Methoden für ADF-Artefakte](/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd).


