---
title: Bereitstellen über ein lokales Git-Repository
description: Erfahren Sie, wie Sie die lokale Git-Bereitstellung in Azure App Service aktivieren. Es handelt sich um einen der einfachsten Wege, Code von Ihrem lokalen Computer bereitzustellen.
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.topic: article
ms.date: 06/18/2019
ms.reviewer: dariac
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 26fd8bc73fad3ea313641fc4b1e0f454ee2c0813
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97347777"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Lokale Git-Bereitstellung in Azure App Service

In dieser Anleitung erfahren Sie, wie Sie Ihre App aus einem Git-Repository auf dem lokalen Computer in [Azure App Service](overview.md) bereitstellen.

## <a name="prerequisites"></a>Voraussetzungen

Ausführen der Schritte in dieser Anleitung:

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Git installieren](https://www.git-scm.com/downloads).
  
- Über ein lokales Git-Repository mit dem Code verfügen, den Sie bereitstellen möchten. Führen Sie zum Herunterladen eines Beispielrepositorys den folgenden Befehl in Ihrem lokalen Terminalfenster aus:
  
  ```bash
  git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
  ```

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="deploy-with-kudu-build-server"></a>Bereitstellen mit Kudu-Buildserver

Die einfachste Möglichkeit zum Aktivieren einer lokalen Git-Bereitstellung für Ihre App mit dem Kudu App Service-Buildserver ist die Verwendung von Azure Cloud Shell. 

### <a name="configure-a-deployment-user"></a>Konfigurieren eines Bereitstellungsbenutzers

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="get-the-deployment-url"></a>Abrufen der Bereitstellungs-URL

Um die URL zum Aktivieren der lokalen Git-Bereitstellung für eine vorhandene App abzurufen, führen Sie [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source#az-webapp-deployment-source-config-local-git) in Cloud Shell aus. Ersetzen Sie \<app-name> und \<group-name> durch die Namen Ihrer App und der zugehörigen Azure-Ressourcengruppe.

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```
> [!NOTE]
> Wenn Sie einen Linux-App Service-Plan verwenden, müssen Sie diesen Parameter hinzufügen: --runtime python|3.7


Oder führen Sie zum Erstellen einer neuen Git-fähigen App [`az webapp create`](/cli/azure/webapp#az-webapp-create) in Cloud Shell mit dem Parameter `--deployment-local-git` aus. Ersetzen Sie \<app-name>, \<group-name> und \<plan-name> durch die Namen für Ihre neue Git-App, die zugehörige Azure-Ressourcengruppe und den zugehörigen Azure App Service-Plan.

```azurecli-interactive
az webapp create --name <app-name> --resource-group <group-name> --plan <plan-name> --deployment-local-git
```

Beide Befehle geben eine URL zurück wie `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git`. Verwenden Sie diese URL zum Bereitstellen Ihrer App im nächsten Schritt.

Statt diese URL auf Kontoebene zu verwenden, können Sie auch lokales Git mithilfe von Anmeldeinformationen auf App-Ebene aktivieren. Azure App Service generiert diese Anmeldeinformationen automatisch für jede App. 

Rufen Sie die App-Anmeldeinformationen ab, indem Sie den folgenden Befehl in Cloud Shell ausführen. Ersetzen Sie \<app-name> und \<group-name> durch die Namen Ihrer App und der zugehörigen Azure-Ressourcengruppe.

```azurecli-interactive
az webapp deployment list-publishing-credentials --name <app-name> --resource-group <group-name> --query scmUri --output tsv
```

Verwenden Sie die zurückgegebene URL im nächsten Schritt zum Bereitstellen Ihrer App.

### <a name="deploy-the-web-app"></a>Bereitstellen der Web-App

1. Öffnen Sie ein lokales Terminalfenster, und fügen Sie Ihrem lokalen Git-Repository ein Azure-Remoterepository hinzu. Ersetzen Sie im folgenden Befehl \<url> durch die benutzerspezifische URL für die Bereitstellung oder die App-spezifische URL, die Sie im vorherigen Schritt abgerufen haben.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Übertragen Sie mithilfe von Push mit `git push azure master` zum Azure-Remoterepository. 
   
1. Geben Sie im Fenster **Git-Anmeldeinformationsverwaltung** Ihr [Benutzerkennwort für die Bereitstellung](#configure-a-deployment-user) und nicht Ihr Azure-Kennwort für die Anmeldung ein.
   
1. Überprüfen Sie die Ausgabe. Sie enthält möglicherweise eine laufzeitspezifische Automatisierung wie MSBuild für ASP.NET, `npm install` für Node.js und `pip install` für Python. 
   
1. Navigieren Sie im Azure-Portal zu Ihrer App, um zu überprüfen, ob der Inhalt bereitgestellt wurde.

## <a name="deploy-with-azure-pipelines-builds"></a>Bereitstellen mit Azure Pipelines-Builds

Wenn Ihr Konto über die erforderlichen Berechtigungen verfügt, können Sie Azure Pipelines (Vorschau) einrichten, um die lokale Git-Bereitstellung für Ihre App zu aktivieren. 

- Ihr Azure-Konto muss über Berechtigungen zum Schreiben in Azure Active Directory und zum Erstellen eines Diensts verfügen. 
  
- Ihr Azure-Konto muss in Ihrem Azure-Abonnement über die Rolle **Besitzer** verfügen.

- Sie müssen ein Administrator in dem Azure DevOps-Projekt sein, das Sie verwenden möchten.

So aktivieren Sie die lokale Git-Bereitstellung für Ihre App mit Azure Pipelines (Vorschau):

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach **App Services**, und wählen Sie den Eintrag aus. 

1. Wählen Sie Ihre Azure App Service-App und dann im linken Menü **Bereitstellungscenter** aus.
   
1. Wählen Sie auf der Seite **Bereitstellungscenter** nacheinander **Lokales Git** und dann **Weiter** aus. 
   
   ![„Lokales Git“ und dann „Weiter“ auswählen](media/app-service-deploy-local-git/portal-enable.png)
   
1. Wählen Sie auf der Seite **Buildanbieter** nacheinander **Azure Pipelines (Vorschau)** und dann **Weiter** aus. 
   
   ![„Azure Pipelines (Vorschau)“ und dann „Weiter“ auswählen](media/app-service-deploy-local-git/pipeline-builds.png)

1. Konfigurieren Sie auf der Seite **Konfigurieren** eine neue Azure DevOps-Organisation, oder geben Sie eine vorhandene Organisation an. Wählen Sie dann **Weiter** aus.
   
   > [!NOTE]
   > Wenn Ihre vorhandene Azure DevOps-Organisation nicht aufgeführt ist, müssen Sie sie möglicherweise mit Ihrem Azure-Abonnement verknüpfen. Weitere Informationen finden Sie unter [Definieren Ihrer CD-Releasepipeline](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd).
   
1. Abhängig vom [Tarif](https://azure.microsoft.com/pricing/details/app-service/plans/) Ihres App Service-Plans wird ggf. auch eine Seite **Für Staging bereitstellen** angezeigt. Wählen Sie, ob [Bereitstellungsslots aktiviert werden sollen](deploy-staging-slots.md), und wählen Sie **Weiter** aus.
   
1. Überprüfen Sie auf der Seite **Zusammenfassung** die Einstellungen, und wählen Sie **Weiter** aus.
   
1. Wenn die Azure-Pipeline bereit ist, kopieren Sie die Git-Repository-URL von der Seite **Bereitstellungscenter**, um sie im nächsten Schritt zu verwenden. 
   
   ![Die Git-Repository-URL kopieren](media/app-service-deploy-local-git/vsts-repo-ready.png)

1. Fügen Sie in Ihrem lokalen Terminalfenster Ihrem lokalen Git-Repository ein Azure-Remoterepository hinzu. Ersetzen Sie im Befehl \<url> durch die URL des Git-Repositorys, die Sie im vorherigen Schritt abgerufen haben.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Übertragen Sie mithilfe von Push mit `git push azure master` zum Azure-Remoterepository. 
   
1. Melden Sie sich auf der Seite **Git-Anmeldeinformationsverwaltung** mit Ihrem „visualstudio.com“-Benutzernamen an. Weitere Authentifizierungsmethoden finden Sie in der [Übersicht über die Azure DevOps Services-Authentifizierung](/vsts/git/auth-overview?view=vsts).
   
1. Sobald die Bereitstellung abgeschlossen ist, zeigen Sie den Buildfortschritt unter `https://<azure_devops_account>.visualstudio.com/<project_name>/_build` und den Bereitstellungsstatus unter `https://<azure_devops_account>.visualstudio.com/<project_name>/_release` an.
   
1. Navigieren Sie im Azure-Portal zu Ihrer App, um zu überprüfen, ob der Inhalt bereitgestellt wurde.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-deployment"></a>Problembehandlung bei der Bereitstellung

Möglicherweise werden die folgenden häufigen Fehlermeldungen angezeigt, wenn Sie eine App Service-App mithilfe von Git in Azure veröffentlichen:

|`Message`|Ursache|Lösung
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|Die App wird nicht ordnungsgemäß ausgeführt.|Starten Sie die App im Azure-Portal. Die Git-Bereitstellung ist nicht verfügbar, wenn die Web-App beendet wurde.|
|`Couldn't resolve host 'hostname'`|Die Adressinformationen für die ‚azure‘-Remotewebsite sind falsch.|Verwenden Sie den Befehl `git remote -v`, um alle Remotewebsites zusammen mit der jeweils zugehörigen URL aufzulisten. Überprüfen Sie, ob die URL für die 'azure'-Remotewebsite korrekt ist. Entfernen Sie diese Remote-Website bei Bedarf und erstellen Sie sie mit der korrekten URL neu.|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'main'.`|Sie haben während `git push` keinen Branch angegeben, oder Sie haben den Wert `push.default` in `.gitconfig` nicht festgelegt.|Führen Sie `git push` erneut aus, und geben Sie dabei den Hauptbranch an: `git push azure master`.|
|`src refspec [branchname] does not match any.`|Sie haben versucht, einen anderen Branch als den Hauptbranch mithilfe von Push in das „azure“-Remoterepository zu übertragen.|Führen Sie `git push` erneut aus, und geben Sie dabei den Masterbranch an: `git push azure master`.|
|`RPC failed; result=22, HTTP code = 5xx.`|Dieser Fehler kann auftreten, wenn Sie versuchen, ein großes Git-Repository über HTTPS mithilfe von Push zu übertragen.|Ändern Sie die Git-Konfiguration auf dem lokalen Computer, um den `postBuffer` zu vergrößern. Beispiel: `git config --global http.postBuffer 524288000`.|
|`Error - Changes committed to remote repository but your web app not updated.`|Sie haben eine Node.js-App mit einer Datei von Typ _package.json_ bereitgestellt, die zusätzliche erforderliche Module angibt.|Überprüfen Sie die Fehlermeldungen vom Typ `npm ERR!` vor diesem Fehler, um mehr Kontext zu erhalten. Es folgen die bekannten Ursachen für diesen Fehler und die entsprechenden Meldungen vom Typ `npm ERR!`:<br /><br />**Falsch formatierte „package.json“-Datei**: `npm ERR! Couldn't read dependencies.`<br /><br />**Systemeigenes Modul verfügt über keine binäre Verteilung für Windows**:<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />oder <br />`npm ERR! [modulename@version] preinstall: \make || gmake\ `|

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Project Kudu documentation](https://github.com/projectkudu/kudu/wiki)
- [Continuous Deployment in Azure App Service](deploy-continuous-deployment.md)
- [Beispiel: Erstellen einer Web-App und Bereitstellen von Code über ein lokales Git-Repository (Azure CLI)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [Beispiel: Erstellen einer Web-App und Bereitstellen von Code über ein lokales Git-Repository (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
