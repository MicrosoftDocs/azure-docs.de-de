---
title: ZIP-Push-Bereitstellung für Azure Functions
description: Verwenden Sie die Funktionen zur Bereitstellung von ZIP-Dateien des Kudu-Bereitstellungdiensts zum Veröffentlichen Ihrer Azure Functions.
ms.topic: conceptual
ms.date: 08/12/2018
ms.openlocfilehash: e104661dcdf1f6c6fd6dd5eb1024748980e7931f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018429"
---
# <a name="zip-deployment-for-azure-functions"></a>ZIP-Bereitstellung für Azure Functions

In diesem Artikel wird beschrieben, wie Sie Ihre Projektdateien für Funktions-Apps aus einer ZIP-Datei (komprimierten Datei) in Azure bereitstellen. Sie erfahren, wie Push-Bereitstellungen ausgeführt werden, sowohl mithilfe der Azure-Befehlszeilenschnittstelle als auch mithilfe der REST-APIs. [Azure Functions Core Tools](functions-run-local.md) verwenden auch beim Veröffentlichen eines lokalen Projekts in Azure diese Bereitstellungs-APIs.

Azure Functions bietet die ganze Bandbreite an Optionen für Continuous Deployment und Integration, die in Azure App Service zur Verfügung stehen. Weitere Informationen finden Sie unter [Continuous Deployment für Azure Functions](functions-continuous-deployment.md).

Um die Entwicklung zu beschleunigen, ist es für Sie womöglich einfacher, Ihre Projektdateien für die Funktions-App direkt über eine ZIP-Datei bereitzustellen. Die ZIP-Bereitstellungs-API extrahiert den Inhalt einer ZIP-Datei in den Ordner `wwwroot` Ihrer Funktions-App. Bei dieser Bereitstellung per ZIP-Datei wird der gleiche Kudu-Dienst verwendet, der auch bei der Continuous Integration-basierten Bereitstellungen zum Einsatz kommt, einschließlich dieser Funktionalität:

+ Löschen von Dateien, die von früheren Bereitstellungen übrig geblieben sind
+ Anpassen der Bereitstellung, einschließlich der Ausführung von Bereitstellungsskripts
+ Bereitstellungsprotokolle
+ Synchronisierung von Funktionstriggern in einer [Verbrauchstarif](functions-scale.md)-Funktions-App

Weitere Informationen finden Sie in der [Referenz zur ZIP-Bereitstellung](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

## <a name="deployment-zip-file-requirements"></a>Anforderungen an ZIP-Bereitstellungsdateien

Die ZIP-Datei, die Sie für die Push-Bereitstellung verwenden, muss alle Projektdateien zur Ausführung Ihrer Funktion beinhalten.

>[!IMPORTANT]
> Wenn Sie ZIP-Bereitstellung verwenden, werden alle Dateien aus einer vorhandenen Bereitstellung, die nicht in der ZIP-Datei vorhanden sind, aus Ihrer Funktions-App gelöscht.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Eine Funktionen-App umfasst alle Dateien und Ordner im Verzeichnis `wwwroot`. Eine ZIP-Bereitstellungsdatei enthält den Inhalt des Verzeichnisses `wwwroot`, aber nicht das Verzeichnis selbst. Wenn Sie ein C#-Klassenbibliotheksprojekt bereitstellen, müssen Sie die kompilierten Bibliotheksdateien und Abhängigkeiten in einem `bin`-Unterordner im ZIP-Paket einschließen.

## <a name="download-your-function-app-files"></a>Herunterladen der Dateien Ihrer Funktionen-App

Wenn Sie auf einem lokalen Computer entwickeln, ist es einfach, eine ZIP-Datei des Projektordners der Funktions-App auf Ihrem Entwicklungscomputer zu erstellen.

Allerdings haben Sie Ihre Funktionen möglicherweise mithilfe des Editors im Azure-Portal erstellt. Sie können ein vorhandenes Funktionen-App-Projekt auf eine der folgenden Weisen herunterladen:

+ **Im Azure-Portal**

  1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie dann zu Ihrer Funktions-App.

  2. Wählen Sie auf der Registerkarte **Übersicht** den Befehl **App-Inhalt herunterladen** aus. Wählen Sie Ihre Downloadoptionen und anschließend **Herunterladen** aus.

      ![Herunterladen des Funktions-App-Projekts](./media/deployment-zip-push/download-project.png)

     Die heruntergeladene ZIP-Datei weist das richtige Format auf, um mithilfe der ZIP-Push-Bereitstellung wieder in Ihrer Funktions-App veröffentlicht zu werden. Beim Herunterladen im Portal können auch die Dateien hinzugefügt werden, die zum Öffnen der Funktionen-App direkt in Visual Studio erforderlich sind.

+ **Mit REST-APIS**

    Verwenden Sie die folgende GET-Bereitstellungs-API, um Dateien aus Ihrem `<function_app>`-Projekt herunterzuladen: 

    ```http
    https://<function_app>.scm.azurewebsites.net/api/zip/site/wwwroot/
    ```

    Das Hinzufügen von `/site/wwwroot/` stellt sicher, dass die ZIP-Datei nur die Dateien des Funktionen-App-Projekts und nicht die gesamte Website enthält. Wenn Sie nicht bereits in Azure angemeldet sind, werden Sie aufgefordert, sich anzumelden.  

Sie können aber auch eine ZIP-Datei aus einem GitHub-Repository herunterladen. Wenn Sie ein GitHub-Repositorys als ZIP-Datei herunterladen, fügt GitHub eine zusätzliche Ordnerebene für den Branch hinzu. Diese zusätzliche Ordnerebene bedeutet, dass Sie die ZIP-Datei nicht direkt so, wie Sie sie von GitHub heruntergeladen haben, bereitstellen können. Wenn Sie ein GitHub-Repository zum Verwalten Ihrer Funktions-App verwenden, sollten Sie die App mithilfe von [Continuous Integration](functions-continuous-deployment.md) bereitstellen.  

## <a name="deploy-by-using-azure-cli"></a><a name="cli"></a>Bereitstellen über die Azure-Befehlszeilenschnittstelle

Sie können eine Push-Bereitstellung auch mithilfe der Azure-Befehlszeilenschnittstelle auslösen. Führen Sie eine Push-Bereitstellung einer ZIP-Datei in Ihrer Funktions-App mithilfe des Befehls [az functionapp deployment source config-zip](/cli/azure/functionapp/deployment/source#az-functionapp-deployment-source-config-zip) aus. Zum Ausführen dieses Befehls müssen Sie Azure CLI, Version 2.0.21 oder höher, verwenden. Sie können mithilfe des `az --version`-Befehls anzeigen, welche Azure CLI-Version Sie verwenden.

Ersetzen Sie im folgenden Befehl den `<zip_file_path>`-Platzhalter durch den Pfad zum Speicherort der ZIP-Datei. Ersetzen Sie außerdem `<app_name>` durch den eindeutigen Namen Ihrer Funktions-App, und ersetzen Sie `<resource_group>` durch den Namen Ihrer Ressourcengruppe.

```azurecli-interactive
az functionapp deployment source config-zip -g <resource_group> -n \
<app_name> --src <zip_file_path>
```

Mit diesem Befehl werden Projektdateien aus der heruntergeladenen ZIP-Datei in Ihrer Funktions-App in Azure bereitgestellt. Anschließend wird die App neu gestartet. Um die Liste der Bereitstellungen für diese Funktions-App anzuzeigen, müssen Sie die REST-APIs verwenden.

Wenn Sie die Azure-Befehlszeilenschnittstelle auf ihrem lokalen Computer verwenden, ist `<zip_file_path>` der Pfad zur ZIP-Datei auf Ihrem Computer. Sie können die Azure-Befehlszeilenschnittstelle ebenfalls in der [Azure Cloud Shell](../cloud-shell/overview.md) ausführen. Wenn Sie Cloud Shell verwenden, müssen Sie zunächst die ZIP-Datei für die Bereitstellung auf das Azure Files-Konto hochladen, das Ihrer Cloud Shell zugeordnet ist. In diesem Fall ist `<zip_file_path>` der Speicherort, der von Ihren Cloud Shell-Konto verwendet wird. Weitere Informationen finden Sie unter [Beibehalten von Dateien in Azure Cloud Shell](../cloud-shell/persisting-shell-storage.md).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]

## <a name="run-functions-from-the-deployment-package"></a>Ausführen von Funktionen aus dem Bereitstellungspaket

Sie können Ihre Funktionen auch direkt aus der Bereitstellungspaketdatei heraus ausführen. Bei dieser Methode wird der Bereitstellungsschritt übersprungen, indem Dateien aus dem Paket in das `wwwroot`-Verzeichnis Ihrer Funktions-App kopiert werden. Stattdessen wird die Paketdatei durch die Functions-Laufzeit bereitgestellt, und der Inhalt des `wwwroot` Verzeichnisses ist schreibgeschützt.  

Die ZIP-Bereitstellung ist mit diesem Feature integriert. Sie können es aktivieren, indem Sie die Einstellung `WEBSITE_RUN_FROM_PACKAGE` der Funktions-App auf einen Wert von `1` festlegen. Weitere Informationen finden Sie unter [Ausführen von Functions über eine Bereitstellungspaketdatei](run-functions-from-deployment-package.md).

[!INCLUDE [app-service-deploy-zip-push-custom](../../includes/app-service-deploy-zip-push-custom.md)]

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Kontinuierliche Bereitstellung für Azure Functions](functions-continuous-deployment.md)

[.zip push deployment reference topic]: https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file
