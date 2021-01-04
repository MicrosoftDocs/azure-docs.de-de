---
title: Konfigurieren von Continuous Deployment
description: Hier erfahren Sie, wie Sie in Azure App Service CI/CD aus GitHub, BitBucket, Azure Repos und anderen Repositorys aktivieren. Wählen Sie die Buildpipeline aus, die Ihren Anforderungen entspricht.
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 03/20/2020
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: b7730558e2a660b0cf00a5b6962d1e2275dd472c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96004342"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Continuous Deployment in Azure App Service

Der [Azure App Service](overview.md) ermöglicht die kontinuierliche Bereitstellung von GitHub-, BitBucket- und [Azure Repos-](https://azure.microsoft.com/services/devops/repos/)Repositorys durch Abrufen der neuesten Updates. Dieser Artikel zeigt Ihnen, wie Sie das Azure-Portal nutzen können, um Ihre App kontinuierlich über den Kudu Build-Dienst oder [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) bereitzustellen. 

Weitere Informationen zu den Quellcodeverwaltungsdiensten finden Sie unter [Erstellen eines Repositorys (GitHub)], [Erstellen eines Repositorys (BitBucket)] oder [Erstellen eines neuen Git-Repositorys (Azure Repos)].

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="authorize-azure-app-service"></a>Autorisieren von Azure App Service 

Um Azure Repos zu verwenden, stellen Sie sicher, dass Ihre Azure DevOps-Organisation mit Ihrem Azure-Abonnement verknüpft ist. Weitere Informationen finden Sie unter [„Einrichten eines Azure DevOps-Dienstkontos zur Bereitstellung für eine Webanwendung“](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops&preserve-view=true).

Für Bitbucket oder GitHub autorisieren Sie den Azure App Service, sich mit Ihrem Repository zu verbinden. Sie müssen sich nur einmal bei einem Quellcodverwaltungsdienst autorisieren. 

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach **App Services**, und wählen Sie den Eintrag aus.

   ![Suchen Sie nach App Services.](media/app-service-continuous-deployment/search-for-app-services.png)

1. Wählen Sie den bereitzustellenden App Service aus.

   ![Wählen Sie Ihre App aus.](media/app-service-continuous-deployment/select-your-app.png)
   
1. Wählen Sie auf der App-Seite im linken Menü **„Bereitstellungscenter“** aus.
   
1. Wählen Sie auf der Seite **„Bereitstellungscenter“** die Option **GitHub** oder **Bitbucket** und dann **„Autorisieren“** . 
   
   ![Wählen Sie den Quellcodeverwaltungdienst und dann „Autorisieren“.](media/app-service-continuous-deployment/github-choose-source.png)
   
1. Melden Sie sich bei Bedarf beim Dienst an, und folgen Sie den Anweisungen zur Autorisierung. 

## <a name="enable-continuous-deployment"></a>Aktivieren von Continuous Deployment 

Nachdem Sie einen Quellcodeverwaltungdienst autorisiert haben, konfigurieren Sie Ihre Anwendung für die fortlaufende Bereitstellung über den integrierten [Kudu App Service Build-Server](#option-1-kudu-app-service) oder über [Azure Pipelines](#option-2-azure-pipelines). 

### <a name="option-1-kudu-app-service"></a>Option 1: Kudu App Service

Sie können den integrierten Kudu App Service Build-Server verwenden, um die Bereitstellung kontinuierlich von GitHub, Bitbucket oder Azure Repos aus vorzunehmen. 

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach **App Services**, und wählen Sie dann den App Service aus, den Sie bereitstellen möchten. 
   
1. Wählen Sie auf der App-Seite im linken Menü **„Bereitstellungscenter“** aus.
   
1. Wählen Sie auf der Seite **„Bereitstellungscenter“** Ihren autorisierten Quellcodeverwaltungdienst aus und wählen Sie **„Weiter“** . Für GitHub oder Bitbucket können Sie auch **„Konto ändern“** wählen, um das autorisierte Konto zu ändern. 
   
   > [!NOTE]
   > Um Azure Repos zu verwenden, stellen Sie sicher, dass Ihre Azure DevOps Services-Organisation mit Ihrem Azure-Abonnement verknüpft ist. Weitere Informationen finden Sie unter [„Einrichten eines Azure DevOps-Dienstkontos zur Bereitstellung für eine Webanwendung“](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops&preserve-view=true).
   
1. Wählen Sie für GitHub oder Azure Repos auf der Seite **„Buildanbieter“** die Option **„App Service-Builddienst“** und dann **„Weiter“** . Bitbucket verwendet immer den App Service-Builddienst.
   
   ![Wählen Sie die Option „App Service-Builddienst“ und dann „Weiter“.](media/app-service-continuous-deployment/choose-kudu.png)
   
1. Auf der Seite **Konfigurieren**:
   
   - Wählen Sie das Dropdownmenü für GitHub, und wählen Sie dann die **„Organisation“** , **„Repository“** und **„Branch“** , die sie fortlaufend bereitstellen möchten.
     
     > [!NOTE]
     > Wenn Sie keine Repositorys sehen, müssen Sie möglicherweise den Azure App Service in GitHub autorisieren. Navigieren Sie zu Ihrem GitHub-Repository und wählen Sie **Einstellungen** > **Anwendungen** > **Autorisierte OAuth-Apps**. Wählen Sie **Azure App Service** und dann **„Zuweisung“** . Bei Organisationsrepositorys müssen Sie Besitzer der Organisation sein, um die Berechtigungen erteilen zu können.
     
   - Wählen Sie für Bitbucket **„Team“** aus, dann **„Repository“** und die **„Branch“** , die Sie fortlaufend nutzen möchten.
     
   - Wählen Sie für Azure Repos die **Azure DevOps-Organisation**, das **Projekt**, das **Repository** und **Branch** aus, die Sie fortlaufend nutzen möchten.
     
     > [!NOTE]
     > Wenn Ihre Azure DevOps-Organisation nicht aufgeführt ist, stellen Sie sicher, dass sie mit Ihrem Azure-Abonnement verknüpft ist. Weitere Informationen finden Sie unter [„Einrichten eines Azure DevOps-Dienstkontos zur Bereitstellung für eine Webanwendung“](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops&preserve-view=true).
     
1. Wählen Sie **Weiter**.
   
   ![Geben Sie die Repository-Informationen ein und wählen Sie dann „Weiter“.](media/app-service-continuous-deployment/configure-kudu.png)
   
1. Nachdem Sie den Buildanbieter konfiguriert haben, überprüfen Sie die Einstellungen auf der Seite **„Zusammenfassung“** und wählen Sie dann **„Fertig stellen“** .
   
1. Neue Commits im ausgewählten Repository und Branch werden nun fortlaufend in Ihrer App Service-App bereitgestellt. Auf der Seite **„Bereitstellungscenter“** können Sie die Commits und Bereitstellen verfolgen.
   
   ![Verfolgung von Commits und Bereitstellen im Bereitstellungscenter](media/app-service-continuous-deployment/github-finished.png)

### <a name="option-2-azure-pipelines"></a>Option 2: Azure Pipelines 

Wenn Ihr Konto über die erforderlichen Berechtigungen verfügt, können Sie Azure Pipelines so einrichten, dass sie kontinuierlich aus GitHub oder Azure Repos bereitgestellt werden. Weitere Informationen zur Bereitstellung über Azure Pipelines finden Sie unter [„Bereitstellen einer Webanwendung für Azure App Services“](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps).

#### <a name="prerequisites"></a>Voraussetzungen

Damit Azure App Service Continuous Delivery mithilfe von Azure Pipelines einrichten kann, sollte Ihre Azure DevOps-Organisation folgende Berechtigungen besitzen: 

- Ihr Azure-Konto muss über Berechtigungen zum Schreiben in Azure Active Directory und zum Erstellen eines Diensts verfügen. 
  
- Ihr Azure-Konto muss in Ihrem Azure-Abonnement über die Rolle **Besitzer** verfügen.

- Sie müssen ein Administrator in dem Azure DevOps-Projekt sein, das Sie verwenden möchten.

#### <a name="github--azure-pipelines"></a>GitHub + Azure Pipelines

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach **App Services**, und wählen Sie dann den App Service aus, den Sie bereitstellen möchten. 
   
1. Wählen Sie auf der App-Seite im linken Menü **„Bereitstellungscenter“** aus.

1. Wählen Sie auf der Seite **Bereitstellungscenter** **GitHub** als Quellcodeverwaltungsanbieter aus, und wählen Sie **Weiter** aus. Für **GitHub** können Sie **Konto ändern** auswählen, um das autorisierte Konto zu ändern.

    :::image type="content" source="media/app-service-continuous-deployment/deployment-center-src-control.png" alt-text="Ein Screenshot der Seite „App Service-Bereitstellungscenter“.":::
   
1. Wählen Sie auf der Seite **Buildanbieter** nacheinander **Azure Pipelines (Vorschau)** und dann **Weiter** aus.

    :::image type="content" source="media/app-service-continuous-deployment/select-build-provider.png" alt-text="Ein Screenshot, der die Seite „Bereitstellungscenter“ mit ausgewählten „Azure Pipelines (Vorschau)“ zeigt.":::
   
1. Wählen Sie auf der Seite **Konfigurieren** im Abschnitt **Code** die **Organisation**, das **Repository** und den **Branch** aus, aus dem Continuous Deployment erfolgen soll, und wählen Sie **Weiter** aus.
     
     > [!NOTE]
     > Wenn Sie keine Repositorys sehen, müssen Sie möglicherweise den Azure App Service in GitHub autorisieren. Navigieren Sie zu Ihrem GitHub-Repository und wählen Sie **Einstellungen** > **Anwendungen** > **Autorisierte OAuth-Apps**. Wählen Sie **Azure App Service** und dann **„Zuweisung“** . Bei Organisationsrepositorys müssen Sie Besitzer der Organisation sein, um die Berechtigungen erteilen zu können.
       
    Geben Sie im Abschnitt **Build** die Azure DevOps-Organisation, das Projekt und das Sprachenframework an, die/das Azure Pipelines verwenden soll, um Buildaufgaben auszuführen, und wählen Sie dann **Weiter** aus.

   :::image type="content" source="media/app-service-continuous-deployment/build-configure.png" alt-text="Ein Screenshot des Abschnitts „Erstellen“ mit Beispieltext in den Feldern.":::

1. Nachdem Sie den Buildanbieter konfiguriert haben, überprüfen Sie die Einstellungen auf der Seite **„Zusammenfassung“** und wählen Sie dann **„Fertig stellen“** .

   :::image type="content" source="media/app-service-continuous-deployment/summary.png" alt-text="Ein Screenshot der Seite „Bereitstellungscenter“, auf der Commits und Bereitstellungen mit hervorgehobener Schaltfläche „Aktualisieren“ angezeigt werden.":::
   
1. Neue Commits im ausgewählten Repository und Branch werden nun fortlaufend in Ihrem App Service bereitgestellt. Auf der Seite **„Bereitstellungscenter“** können Sie die Commits und Bereitstellen verfolgen.
   
   ![Verfolgung von Commits und Bereitstellen im Bereitstellungscenter](media/app-service-continuous-deployment/github-finished.png)

#### <a name="azure-repos--azure-pipelines"></a>Azure Repos + Azure Pipelines

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach **App Services**, und wählen Sie dann den App Service aus, den Sie bereitstellen möchten. 
   
1. Wählen Sie auf der App-Seite im linken Menü **„Bereitstellungscenter“** aus.

1. Wählen Sie auf der Seite **Bereitstellungscenter** **Azure Repos** als Quellcodeverwaltungsanbieter aus, und wählen Sie **Weiter** aus.

    :::image type="content" source="media/app-service-continuous-deployment/deployment-center-src-control.png" alt-text="Ein Screenshot der Seite „Bereitstellungscenter“ mit ausgewählten Continuous Deployment (CI/CD)-Optionen.":::

1. Wählen Sie auf der Seite **Buildanbieter** nacheinander **Azure Pipelines (Vorschau)** und dann **Weiter** aus.

    :::image type="content" source="media/app-service-continuous-deployment/azure-pipelines.png" alt-text="Ein Screenshot des Bereitstellungscenters mit angezeigten „Azure Pipelines (Vorschau)“.":::

1. Wählen Sie auf der Seite **Konfigurieren** im Abschnitt **Code** die **Organisation**, das **Repository** und den **Branch** aus, aus dem Continuous Deployment erfolgen soll, und wählen Sie **Weiter** aus.

   > [!NOTE]
   > Wenn Ihre vorhandene Azure DevOps-Organisation nicht aufgeführt ist, müssen Sie sie möglicherweise mit Ihrem Azure-Abonnement verknüpfen. Weitere Informationen finden Sie unter [Definieren Ihrer CD-Releasepipeline](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd).

   Geben Sie im Abschnitt **Build** die Azure DevOps-Organisation, das Projekt und das Sprachenframework an, die/das Azure Pipelines verwenden soll, um Buildaufgaben auszuführen, und wählen Sie dann **Weiter** aus.

   :::image type="content" source="media/app-service-continuous-deployment/build-configure.png" alt-text="Ein Screenshot des Abschnitts „Erstellen“, in dem die mit Beispielen ausgefüllten Felder „Azure DevOps-Organisation“ und „Projekt“ angezeigt werden.":::

1. Nachdem Sie den Buildanbieter konfiguriert haben, überprüfen Sie die Einstellungen auf der Seite **„Zusammenfassung“** und wählen Sie dann **„Fertig stellen“** .  
     
   :::image type="content" source="media/app-service-continuous-deployment/summary-azure-pipelines.png" alt-text="Ein Screenshot, der die ausgewählten Einstellungen auf der Seite „Zusammenfassung“ zeigt.":::

1. Neue Commits im ausgewählten Repository und Branch werden nun fortlaufend in Ihrem App Service bereitgestellt. Auf der Seite **„Bereitstellungscenter“** können Sie die Commits und Bereitstellen verfolgen.

## <a name="disable-continuous-deployment"></a>Deaktivieren der fortlaufenden Bereitstellung

Um Continuous Deployment zu deaktivieren, wählen Sie **„Trennen“** oben auf der Seite **„Bereitstellungscenter“** Ihrer Anwendung.

![Deaktivieren der fortlaufenden Bereitstellung](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="use-unsupported-repos"></a>Nicht unterstützte Repositorys

Für Windows-Apps können Sie Continuous Deployment aus einem Git- oder Mecurial-Cloudrepository, das das Portal nicht direkt unterstützt (beispielsweise [GitLab](https://gitlab.com/)), manuell konfigurieren. Wählen Sie hierzu das Feld „Extern“ auf der Seite **Bereitstellungscenter** aus. Weitere Informationen finden Sie unter [Einrichten von Continuous Deployment mit manuellen Schritten](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Untersuchen häufiger Probleme mit Continuous Deployment](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Verwenden von Azure PowerShell](/powershell/azure/)
* [Git-Dokumentation](https://git-scm.com/documentation)
* [Projekt Kudu](https://github.com/projectkudu/kudu/wiki)

[Erstellen eines Repositorys (GitHub)]: https://help.github.com/articles/create-a-repo
[Erstellen eines Repositorys (BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[Erstellen eines neuen Git-Repositorys (Azure Repos)]: /azure/devops/repos/git/creatingrepo