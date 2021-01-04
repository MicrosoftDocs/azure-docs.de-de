---
title: Bereitstellen von Code mit einer ZIP- oder WAR-Datei
description: Erfahren Sie, wie Sie Ihre App mit einer ZIP-Datei (oder für Java-Entwickler mit einer WAR-Datei) in Azure App Service bereitstellen.
ms.topic: article
ms.date: 08/12/2019
ms.reviewer: sisirap
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 150f6b57f1dec0b6d925ef53b4a7001ae9f23607
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007907"
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-or-war-file"></a>Bereitstellen der App in Azure App Service mit einer ZIP- oder WAR-Datei

In diesem Artikel erfahren Sie, wie Sie Ihre Web-App mithilfe einer ZIP- oder WAR-Datei in [Azure App Service](overview.md) bereitstellen. 

Bei dieser Bereitstellung per ZIP-Datei wird der gleiche Kudu-Dienst verwendet, der auch bei der Continuous Integration-basierten Bereitstellungen zum Einsatz kommt. Kudu unterstützt die folgenden Funktionen für die Bereitstellung per ZIP-Datei: 

- Löschen von Dateien aus einer vorherigen Bereitstellung
- Aktivieren des Standarderstellungsprozesses, der die Paketwiederherstellung umfasst
- Anpassen der Bereitstellung, einschließlich der Ausführung von Bereitstellungsskripts  
- Bereitstellungsprotokolle 
- Eine maximale Dateigröße von 2048 MB.

Weitere Informationen finden Sie in der [Kudu-Dokumentation](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

Bei der Bereitstellung mit einer WAR-Datei wird Ihre [WAR](https://wikipedia.org/wiki/WAR_(file_format))-Datei in App Service bereitgestellt, um dort Ihre Java-Web-App auszuführen. Weitere Informationen finden Sie unter [Bereitstellen einer WAR-Datei](#deploy-war-file).

> [!NOTE]
> Wenn Sie `ZipDeploy` verwenden, werden Dateien nur kopiert, wenn ihre Zeitstempel nicht mit den bereits bereitgestellten Daten übereinstimmen. Das Erstellen einer ZIP-Datei mit einem Buildprozess, der Ausgaben zwischenspeichert, kann Bereitstellungen beschleunigen. Weitere Informationen finden Sie unter [Deploying from a zip file or url](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url) (Bereitstellen über eine ZIP-Datei oder URL).

## <a name="prerequisites"></a>Voraussetzungen

Um die Schritte in diesem Artikel durchzuführen, [erstellen Sie eine App Service-App](./index.yml), oder verwenden Sie eine App, die Sie für ein anderes Tutorial erstellt haben.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]
Der oben erwähnte Endpunkt funktioniert zurzeit nicht für App Services unter Linux. Verwenden Sie stattdessen FTP oder die [ZIP Deploy-API](faq-app-service-linux.md#continuous-integration-and-deployment).

## <a name="deploy-zip-file-with-azure-cli"></a>Bereitstellen einer ZIP-Datei mit der Azure-Befehlszeilenschnittstelle

Stellen Sie die hochgeladene ZIP-Datei mit dem Befehl [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source#az-webapp-deployment-source-config-zip) für Ihre Web-App bereit.  

Im folgenden Beispiel wird die ZIP-Datei bereitgestellt, die Sie hochgeladen haben. Wenn Sie eine lokale Installation der Azure-Befehlszeilenschnittstelle verwenden, geben Sie den Pfad zu Ihrer lokalen ZIP-Datei als `--src` an.

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src clouddrive/<filename>.zip
```

Mit diesem Befehl werden die Dateien und Verzeichnisse aus der ZIP-Datei in Ihrem Standardordner der App Service-Anwendung (`\home\site\wwwroot`) bereitgestellt, und die App wird neu gestartet.

Standardmäßig geht die Bereitstellungs-Engine davon aus, dass eine ZIP-Datei ohne weitere Maßnahmen ausführungsfähig ist, und führt deshalb auch keine Buildautomatisierung aus. Um dieselbe Buildautomatisierung wie bei einer [Git-Bereitstellung](deploy-local-git.md) zu aktivieren, legen Sie die App-Einstellung `SCM_DO_BUILD_DURING_DEPLOYMENT` fest, indem Sie in den folgenden Befehl in [Cloud Shell](https://shell.azure.com) ausführen:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings SCM_DO_BUILD_DURING_DEPLOYMENT=true
```

Weitere Informationen finden Sie in der [Kudu-Dokumentation](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="deploy-war-file"></a>Bereitstellen einer WAR-Datei

Um eine WAR-Datei in App Service bereitzustellen, senden Sie eine POST-Anforderung an `https://<app-name>.scm.azurewebsites.net/api/wardeploy`. Die POST-Anforderung muss die WAR-Datei im Nachrichtentext enthalten. Die Anmeldeinformationen für die Bereitstellung für Ihre App werden in der Anforderung mithilfe von HTTP-Standardauthentifizierung bereitgestellt.

Verwenden Sie immer `/api/wardeploy`, wenn Sie WAR-Dateien bereitstellen. Diese API erweitert ihre WAR-Datei und legt sie auf dem freigegebenen Dateilaufwerk ab. Die Verwendung anderer Bereitstellungs-APIs kann zu inkonsistentem Verhalten führen. 

Für die HTTP-Standardauthentifizierung benötigen Sie die Anmeldeinformationen für die App Service-Bereitstellung. Informationen zum Festlegen der Anmeldeinformationen für Ihre Bereitstellung finden Sie unter [Festlegen und Zurücksetzen von Anmeldeinformationen auf Benutzerebene](deploy-configure-credentials.md#userscope).

### <a name="with-curl"></a>Mit cURL

Im folgenden Beispiel wird das cURL-Tool verwendet, um eine WAR-Datei bereitzustellen. Ersetzen Sie die Platzhalter `<username>`, `<war-file-path>` und `<app-name>`. Wenn Sie von cURL dazu aufgefordert werden, geben Sie das Kennwort ein.

```bash
curl -X POST -u <username> --data-binary @"<war-file-path>" https://<app-name>.scm.azurewebsites.net/api/wardeploy
```

### <a name="with-powershell"></a>Mit PowerShell

Im folgenden Beispiel wird die WAR-Datei mit [Publish-AzWebapp](/powershell/module/az.websites/publish-azwebapp) hochgeladen. Ersetzen Sie die Platzhalter `<group-name>`, `<app-name>` und `<war-file-path>`.

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <war-file-path>
```

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Nächste Schritte

Für komplexere Bereitstellungsszenarien versuchen Sie die [Bereitstellung in Azure mit Git](deploy-local-git.md). Die Git-basierte Bereitstellung in Azure ermöglicht Versionskontrolle, Paketwiederherstellung, MSBuild und mehr.

## <a name="more-resources"></a>Weitere Ressourcen

* [Kudu: Bereitstellen aus einer ZIP-Datei](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Anmeldeinformationen für die Azure App Service-Bereitstellung](deploy-ftp.md)
