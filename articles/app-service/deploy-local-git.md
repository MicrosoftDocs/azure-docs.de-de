---
title: Bereitstellen über ein lokales Git-Repository – Azure App Service
description: Erfahren Sie, wie Sie die lokale Git-Bereitstellung in Azure App Service aktivieren.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2018
ms.author: dariagrigoriu;cephalin
ms.custom: seodec18
ms.openlocfilehash: b879036dcd79901cb634fa197932e833cb22d12a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "65956067"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Lokale Git-Bereitstellung in Azure App Service

In dieser Anleitung erfahren Sie, wie Sie Ihren Code aus einem Git-Repository auf dem lokalen Computer in [Azure App Service](overview.md) bereitstellen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Ausführen der Schritte in dieser Anleitung:

* [Git installieren](https://www.git-scm.com/downloads).
* Verwalten Sie ein lokales Git-Repository mit Code, den Sie bereitstellen möchten.

Zur Verwendung eines Beispielrepositorys zum Nachzuvollziehen führen Sie den folgenden Befehl in Ihrem lokalen Terminalfenster aus:

```bash
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-with-kudu-builds"></a>Bereitstellen mit Kudu-Builds

Die einfachste Möglichkeit zum Aktivieren einer lokalen Git-Bereitstellung für Ihre App mit dem Kudu-Buildserver ist die Verwendung von Cloud Shell.

### <a name="configure-a-deployment-user"></a>Konfigurieren eines Bereitstellungsbenutzers

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Aktivieren von lokalem Git mit Kudu

Zum Aktivieren einer lokalen Git-Bereitstellung für Ihre App mit dem Kudu-Buildserver führen Sie [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) in Cloud Shell aus.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Wenn Sie stattdessen eine Git-fähige App erstellen möchten, führen Sie [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) in Cloud Shell mit dem Parameter `--deployment-local-git` aus.

```azurecli-interactive
az webapp create --name <app_name> --resource-group <group_name> --plan <plan_name> --deployment-local-git
```

Die Ausgabe des Befehls `az webapp create` sollte ungefähr wie folgt aussehen:

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="deploy-your-project"></a>Bereitstellen des Projekts

Kehren Sie zum _lokalen Terminalfenster_ zurück, und fügen Sie Ihrem lokalen Git-Repository einen Azure-Remotespeicherort hinzu. Ersetzen Sie _\<Url>_ durch die URL des Git-Remoterepositorys, die Sie beim [Aktivieren von Git für die App](#enable-local-git-with-kudu) erhalten haben.

```bash
git remote add azure <url>
```

Führen Sie einen Pushvorgang zum Azure-Remotespeicherort durch, um Ihre App mit dem folgenden Befehl bereitzustellen. Stellen Sie sicher, dass Sie bei der entsprechenden Aufforderung das Kennwort eingeben, das Sie unter [Konfigurieren eines Bereitstellungsbenutzers](#configure-a-deployment-user) erstellt haben, und nicht das Kennwort für die Anmeldung am Azure-Portal.

```bash
git push azure master
```

Die Ausgabe enthält u.U. laufzeitspezifische Automatisierungen wie MSBuild für ASP.NET, `npm install` für Node.js und `pip install` für Python. 

Navigieren Sie zu Ihrer App, um sicherzustellen, dass der Inhalt bereitgestellt wird.

## <a name="deploy-with-azure-devops-builds"></a>Bereitstellen mit Azure DevOps-Builds

> [!NOTE]
> Damit App Service die erforderlichen Azure-Pipelines in Ihrer Azure DevOps Services-Organisation erstellt, muss Ihr Azure-Konto die Rolle **Besitzer** in Ihrem Azure-Abonnement aufweisen.
>

Zum Aktivieren einer lokalen Git-Bereitstellung für Ihre App mit dem Kudu-Buildserver navigieren Sie zu Ihrer App im [Azure-Portal](https://portal.azure.com).

Klicken Sie im linken Navigationsbereich Ihrer App-Seite auf **Bereitstellungscenter** > **Lokales Git** > **Weiter**.

![](media/app-service-deploy-local-git/portal-enable.png)

Klicken Sie auf **Azure Pipelines (Vorschau)** > **Weiter**.

![](media/app-service-deploy-local-git/pipeline-builds.png)

Konfigurieren Sie auf der Seite **Konfigurieren** eine neue Azure DevOps-Organisation, oder geben Sie eine vorhandene Organisation an. Klicken Sie abschließend auf **Weiter**.

> [!NOTE]
> Wenn Sie eine vorhandene Azure DevOps-Organisation verwenden möchten, die nicht aufgeführt ist, müssen Sie [die Azure DevOps Services-Organisation mit Ihrem Azure-Abonnement verknüpfen](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

Abhängig vom [Tarif](https://azure.microsoft.com/pricing/details/app-service/plans/) Ihres App Service-Plans wird ggf. auch eine Seite **Für Staging bereitstellen** angezeigt. Wählen Sie aus, ob Bereitstellungsslots aktiviert werden sollen, und klicken Sie dann auf **Weiter**.

Überprüfen Sie auf der Seite **Zusammenfassung** die Optionen, und klicken Sie dann auf **Fertig stellen**.

Es dauert einige Minuten, bis die Azure DevOps Services-Organisation bereit ist. Sobald dies der Fall ist, kopieren Sie die Git-Repository-URL in das Bereitstellungscenter.

![](media/app-service-deploy-local-git/vsts-repo-ready.png)

Kehren Sie zum _lokalen Terminalfenster_ zurück, und fügen Sie Ihrem lokalen Git-Repository einen Azure-Remotespeicherort hinzu. Ersetzen Sie  _\<URL>_ durch die URL aus dem letzten Schritt.

```bash
git remote add vsts <url>
```

Führen Sie einen Pushvorgang zum Azure-Remotespeicherort durch, um Ihre App mit dem folgenden Befehl bereitzustellen. Melden Sie sich nach Aufforderung durch die Git-Anmeldeinformationsverwaltung mit Ihrem visualstudio.com-Benutzerkonto an. Zusätzliche Authentifizierungsmethoden finden Sie in der [Übersicht über die Azure DevOps Services-Authentifizierung](/vsts/git/auth-overview?view=vsts).

```bash
git push vsts master
```

Sobald die Bereitstellung abgeschlossen ist, finden Sie den Buildfortschritt unter `https://<vsts_account>.visualstudio.com/<project_name>/_build` und den Bereitstellungsstatus unter `https://<vsts_account>.visualstudio.com/<project_name>/_release`.

Navigieren Sie zu Ihrer App, um sicherzustellen, dass der Inhalt bereitgestellt wird.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshooting-kudu-deployment"></a>Problembehandlung bei der Kudu-Bereitstellung

Die folgenden Fehler und Probleme treten häufig auf, wenn Git zum Veröffentlichen in einer App Service-App in Azure verwendet wird:

---
**Symptom**: `Unable to access '[siteURL]': Failed to connect to [scmAddress]`

**Ursache**: Dieser Fehler kann auftreten, wenn die App nicht ordnungsgemäß ausgeführt wird.

**Lösung**: Starten Sie die App im Azure-Portal. Die Git-Bereitstellung ist nicht verfügbar, wenn die Web-App beendet wurde.

---
**Symptom**: `Couldn't resolve host 'hostname'`

**Ursache**: Dieser Fehler kann auftreten, wenn die beim Erstellen des Remoterepositorys „azure“ eingegebenen Adressinformationen falsch waren.

**Lösung**: Verwenden Sie den Befehl `git remote -v`, um alle Remotewebsites zusammen mit der jeweils zugehörigen URL aufzulisten. Überprüfen Sie, ob die URL für die 'azure'-Remotewebsite korrekt ist. Entfernen Sie diese Remote-Website bei Bedarf und erstellen Sie sie mit der korrekten URL neu.

---
**Symptom**: `No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`

**Ursache**: Dieser Fehler kann auftreten, wenn Sie während `git push` keine Verzweigung angeben, oder wenn Sie den Wert `push.default` in `.gitconfig` nicht festgelegt haben.

**Lösung**: Führen Sie `git push` erneut aus, und geben Sie dabei den Masterbranch an. Beispiel: 

```bash
git push azure master
```

---
**Symptom**: `src refspec [branchname] does not match any.`

**Ursache**: Dieser Fehler kann auftreten, wenn Sie versuchen, etwas per Push auf eine andere Verzweigung als die Hauptverzweigung auf dem Remoterepository „azure“ zu übertragen.

**Lösung**: Führen Sie `git push` erneut aus, und geben Sie dabei den Masterbranch an. Beispiel: 

```bash
git push azure master
```

---
**Symptom**: `RPC failed; result=22, HTTP code = 5xx.`

**Ursache**: Dieser Fehler kann auftreten, wenn Sie versuchen, ein großes Git-Repository über HTTPS mithilfe von Push zu übertragen.

**Lösung**: Ändern Sie die Git-Konfiguration auf dem lokalen Computer, sodass der postBuffer größer ist.

```bash
git config --global http.postBuffer 524288000
```

---
**Symptom**: `Error - Changes committed to remote repository but your web app not updated.`

**Ursache**: Dieser Fehler kann auftreten, wenn Sie eine Node.js-App mit einer Datei von Typ _package.json_ bereitstellen, die zusätzliche erforderliche Module angibt.

**Lösung**: Zusätzliche Meldungen mit „npm ERR!“ müssen vor diesem Fehler protokolliert werden und können zusätzlichen Kontext für diesen Fehler bereitstellen. Es folgen bekannte Ursachen für diesen Fehler und die entsprechende Meldung vom Typ „npm ERR!“ :

* **Ungültige package.json-Datei**: npm ERR! Couldn't read dependencies.
* **Systemeigenes Modul ohne binäre Distribution für Windows**:

  * `npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1`

      OR
  * `npm ERR! [modulename@version] preinstall: \make || gmake\`

## <a name="additional-resources"></a>Weitere Ressourcen

* [Project Kudu documentation](https://github.com/projectkudu/kudu/wiki)
* [Continuous Deployment in Azure App Service](deploy-continuous-deployment.md)
* [Beispiel: Erstellen einer Web-App und Bereitstellen von Code über ein lokales Git-Repository (Azure CLI)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
* [Beispiel: Erstellen einer Web-App und Bereitstellen von Code über ein lokales Git-Repository (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
