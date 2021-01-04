---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 08/12/2019
ms.author: cephalin
ms.openlocfilehash: 92e39f128e90ba83a919388e217f0edc86f81770
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018427"
---
## <a name="deploy-zip-file-with-rest-apis"></a><a name="rest"></a>Bereitstellen einer ZIP-Datei mit REST-APIs 

Sie können die [REST-APIs des Bereitstellungsdiensts](https://github.com/projectkudu/kudu/wiki/REST-API) verwenden, um die ZIP-Datei in Ihrer App in Azure bereitzustellen. Senden Sie zum Bereitstellen eine POST-Anforderung an „https://<app_name>.scm.azurewebsites.net/api/zipdeploy“. Die POST-Anforderung muss die ZIP-Datei im Nachrichtentext enthalten. Die Anmeldeinformationen für die Bereitstellung für Ihre App werden in der Anforderung mithilfe von HTTP-Standardauthentifizierung bereitgestellt. Weitere Informationen finden Sie in der [.zip push deployment reference](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file) (Referenz zur ZIP-Push-Bereitstellung). 

Für die HTTP-Standardauthentifizierung benötigen Sie die Anmeldeinformationen für die App Service-Bereitstellung. Informationen zum Festlegen der Anmeldeinformationen für Ihre Bereitstellung finden Sie unter [Festlegen und Zurücksetzen von Anmeldeinformationen auf Benutzerebene](../articles/app-service/deploy-configure-credentials.md#userscope).

### <a name="with-curl"></a>Mit cURL

Im folgenden Beispiel wird das cURL-Tool verwendet, um eine ZIP-Datei bereitzustellen. Ersetzen Sie die Platzhalter `<deployment_user>`, `<zip_file_path>` und `<app_name>`. Wenn Sie von cURL dazu aufgefordert werden, geben Sie das Kennwort ein.

```bash
curl -X POST -u <deployment_user> --data-binary @"<zip_file_path>" https://<app_name>.scm.azurewebsites.net/api/zipdeploy
```

Diese Anforderung löst die Push-Bereitstellung aus der hochgeladenen ZIP-Datei aus. Sie können die aktuelle und vorhergegangene Bereitstellungen mithilfe des Endpunkts `https://<app_name>.scm.azurewebsites.net/api/deployments` überprüfen, wie im folgenden cURL-Beispiel zu sehen. Ersetzen Sie auch hier `<app_name>` durch den Namen Ihrer App und `<deployment_user>` durch den Benutzernamen Ihrer Anmeldeinformationen für die Bereitstellung.

```bash
curl -u <deployment_user> https://<app_name>.scm.azurewebsites.net/api/deployments
```

### <a name="with-powershell"></a>Mit PowerShell

Im folgenden Beispiel wird die ZIP-Datei mit [Publish-AzWebapp](/powershell/module/az.websites/publish-azwebapp) hochgeladen. Ersetzen Sie die Platzhalter `<group-name>`, `<app-name>` und `<zip-file-path>`.

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <zip-file-path>
```

Diese Anforderung löst die Push-Bereitstellung aus der hochgeladenen ZIP-Datei aus. 

Führen Sie die folgenden Befehle aus, um die aktuellen und vergangenen Bereitstellungen zu prüfen. Ersetzen Sie wieder die Platzhalter `<deployment-user>`, `<deployment-password>` und `<app-name>`.

```bash
$username = "<deployment-user>"
$password = "<deployment-password>"
$apiUrl = "https://<app-name>.scm.azurewebsites.net/api/deployments"
$base64AuthInfo = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $username, $password)))
$userAgent = "powershell/1.0"
Invoke-RestMethod -Uri $apiUrl -Headers @{Authorization=("Basic {0}" -f $base64AuthInfo)} -UserAgent $userAgent -Method GET
```
