---
title: Bereitstellungstechnologien in Azure Functions
description: Lernen Sie die verschiedenen Methoden kennen, mit denen Code in Azure Functions bereitgestellt werden kann.
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.openlocfilehash: 4a65a00c28a20c9381d3dcc6fd7545137528d5c0
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943635"
---
# <a name="deployment-technologies-in-azure-functions"></a>Bereitstellungstechnologien in Azure Functions

Ihnen stehen verschiedene Technologien zur Verfügung, um Code aus Azure Functions-Projekten in Azure bereitzustellen. Dieser Artikel bietet eine Übersicht über die für Sie verfügbaren Bereitstellungsmethoden sowie Empfehlungen für in den jeweiligen verschiedenen Szenarien zu verwendende beste Methode. Außerdem finden Sie hier eine vollständige Liste der zugrunde liegenden Bereitstellungstechnologien und ihrer wichtigsten Details. 

## <a name="deployment-methods"></a>Bereitstellungsmethoden

Die Bereitstellungstechnologie, mit der Sie Code in Azure veröffentlichen, wird in der Regel durch die Art und Weise bestimmt, in der Sie Ihre App veröffentlichen. Die geeignete Bereitstellungsmethode wird durch die spezifischen Anforderungen und den Zeitpunkt im Entwicklungszyklus bestimmt. Beispielsweise können Sie während der Entwicklung und während Tests direkt aus Ihrem Entwicklungstool bereitstellen, z. B. Visual Studio Code. Wenn sich Ihre App in der Produktion befindet, ist es wahrscheinlicher, dass Sie kontinuierlich aus der Quellcodeverwaltung oder mithilfe einer automatisierten Veröffentlichungspipeline veröffentlichen, die zusätzliche Validierung und Tests umfasst.  

In der folgenden Tabelle sind die verfügbaren Bereitstellungsmethoden für Ihr Funktionsprojekt beschrieben.

| Bereitstellungstyp&nbsp; | Methoden | Am besten geeignet für: |
| -- | -- | -- |
| Toolsbasiert | &bull;&nbsp;[Visual&nbsp;Studio&nbsp;Code&nbsp;veröffentlichen](functions-develop-vs-code.md#publish-to-azure)<br/>&bull;&nbsp;[Visual Studio veröffentlichen](functions-develop-vs.md#publish-to-azure)<br/>&bull;&nbsp;[Core Tools veröffentlichen](functions-run-local.md#publish) | Bereitstellungen während der Entwicklung und anderen Ad-hoc-Bereitstellungen. Bereitstellungen werden lokal von den Tools verwaltet. | 
| Von App Service verwaltet| &bull;&nbsp;[Bereitstellungscenter&nbsp;(CI/CD)](functions-continuous-deployment.md)&nbsp;<br/>&bull;&nbsp;[Containerbereitstellungen](functions-create-function-linux-custom-image.md#enable-continuous-deployment-to-azure)&nbsp; |  Continuous Deployment (CI/CD) aus der Quellcodeverwaltung oder aus einer Containerregistrierung. Bereitstellungen werden von der App Service-Plattform (Kudu) verwaltet.|
| Externe Pipelines|&bull;&nbsp;[Azure Pipelines](functions-how-to-azure-devops.md)<br/>&bull;&nbsp;[GitHub-Aktionen](functions-how-to-github-actions.md) | Produktions- und DevOps-Pipelines, die zusätzliche Validierungs-, Test- und andere Aktionen umfassen, können als Teil einer automatisierten Bereitstellung ausgeführt werden. Bereitstellungen werden von der Pipeline verwaltet. |

Auch wenn bestimmte Functions-Bereitstellungen die beste Technologie auf Grundlage ihres Kontexts verwenden, basieren die meisten Bereitstellungsmethoden auf der [ZIP-Bereitstellung](#zip-deploy).

## <a name="deployment-technology-availability"></a>Bereitstellungstechnologie: Verfügbarkeit

Azure Functions unterstützt die plattformübergreifende lokale Entwicklung sowie Hosting unter Windows und Linux. Derzeit sind drei Hostingpläne verfügbar:

+ [Verbrauch](consumption-plan.md)
+ [Premium](functions-premium-plan.md)
+ [Dediziert (App Service)](dedicated-plan.md)

Jeder Plan weist ein anderes Verhalten auf. Nicht alle Bereitstellungstechnologien stehen für jede Variante von Azure Functions zur Verfügung. Das folgende Diagramm zeigt die jeweils unterstützten Bereitstellungstechnologien für die verschiedenen Kombination aus Betriebssystem und Hostingplan:

| Bereitstellungstechnologie | Windows: Verbrauch | Windows Premium | Windows: Dediziert  | Linux: Verbrauch | Linux Premium | Linux: Dediziert |
|-----------------------|:-------------------:|:-------------------------:|:------------------:|:---------------------------:|:-------------:|:---------------:|
| Externe Paket-URL<sup>1</sup> |✔|✔|✔|✔|✔|✔|
| ZIP-Bereitstellung |✔|✔|✔|✔|✔|✔|
| Docker-Container | | | | |✔|✔|
| Web Deploy |✔|✔|✔| | | |
| Quellcodeverwaltung |✔|✔|✔| |✔|✔|
| Lokales Git<sup>1</sup> |✔|✔|✔| |✔|✔|
| Cloudsynchronisierung<sup>1</sup> |✔|✔|✔| |✔|✔|
| FTP<sup>1</sup> |✔|✔|✔| |✔|✔|
| Portalbearbeitung |✔|✔|✔| |✔<sup>2</sup>|✔<sup>2</sup>|

<sup>1</sup> Bereitstellungstechnologie, die eine [manuelle Triggersynchronisierung](#trigger-syncing) erfordert.
<sup>2</sup> Die Portalbearbeitung steht nur für HTTP-Trigger und Trigger mit Timer zur Verfügung (für Functions unter Linux mit dem Tarif „Premium“ oder „Dedicated“).

## <a name="key-concepts"></a>Wichtige Begriffe

Einige Schlüsselkonzepte sind wichtig, um zu verstehen, wie Bereitstellungen in Azure Functions funktionieren.

### <a name="trigger-syncing"></a>Triggersynchronisierung

Wenn Sie einen Trigger ändern, muss die Infrastruktur von Functions über die vorgenommenen Änderungen informiert werden. Die Synchronisierung erfolgt bei vielen Bereitstellungstechnologien automatisch. Manchmal müssen die Trigger jedoch manuell synchronisiert werden. Eine manuelle Synchronisierung der Trigger ist erforderlich, wenn Sie Ihre Aktualisierungen über einen Verweis auf eine externe Paket-URL oder einen lokalen Git-Speicherort oder über Cloudsynchronisierung bzw. FTP bereitstellen. Trigger können auf drei Arten synchronisiert werden:

* Neustarten der Funktions-App über das Azure-Portal
* Senden einer HTTP POST-Anforderung an `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` unter Verwendung des [Hauptschlüssels](functions-bindings-http-webhook-trigger.md#authorization-keys)
* Senden einer HTTP POST-Anforderung an `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01` Ersetzen Sie die Platzhalter durch Ihre Abonnement-ID, den Namen Ihrer Ressourcengruppe und den Namen Ihrer Funktions-App.

### <a name="remote-build"></a>Remotebuild

In Azure Functions können automatisch Builds für den Code ausgeführt werden, der nach ZIP-Bereitstellungen empfangen wird. Diese Builds verhalten sich etwas unterschiedlich, je nachdem, ob Ihre App unter Windows oder Linux ausgeführt wird. Remotebuilds werden nicht ausgeführt, wenn eine App bereits für die Ausführung im Modus [Run From Package](run-functions-from-deployment-package.md) (Aus Paket ausführen) festgelegt wurde. Informationen zum Verwenden von Remotebuilds finden Sie unter [ZIP-Bereitstellung](#zip-deploy).

> [!NOTE]
> Wenn bei der Verwendung eines Remotebuilds Probleme auftreten, liegt es möglicherweise daran, dass Ihre App erstellt wurde, bevor die Funktion verfügbar gemacht wurde (1. August 2019). Erstellen Sie eine neue Funktions-App, oder führen Sie `az functionapp update -g <RESOURCE_GROUP_NAME> -n <APP_NAME>` aus, um ihre Funktions-App zu aktualisieren. Dieser Befehl kann zwei Versuche in Anspruch nehmen, bis er erfolgreich ist.

#### <a name="remote-build-on-windows"></a>Remotebuild unter Windows

Alle Funktions-Apps, die unter Windows ausgeführt werden, enthalten eine kleine Verwaltungs-App, die SCM-Website (oder [Kudu](https://github.com/projectkudu/kudu)-Website). Auf dieser Website wird ein Großteil der Bereitstellungs- und Buildlogik für Azure Functions verarbeitet.

Bei der Bereitstellung einer App unter Windows werden sprachspezifische Befehle, z. B. `dotnet restore` (C#) oder `npm install` (JavaScript) ausgeführt.

#### <a name="remote-build-on-linux"></a>Remotebuild unter Linux

Zum Aktivieren des Remotebuilds unter Linux müssen Sie die folgenden [Anwendungseinstellungen](functions-how-to-use-azure-function-app-settings.md#settings) festlegen:

* `ENABLE_ORYX_BUILD=true`
* `SCM_DO_BUILD_DURING_DEPLOYMENT=true`

Standardmäßig führen die [Azure Functions Core Tools](functions-run-local.md) und die [Azure Functions-Erweiterung für Visual Studio Code](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure) bei der Bereitstellung unter Linux Remotebuilds aus. Aus diesem Grund erstellen beide Tools diese Einstellungen automatisch für Sie in Azure.

Wenn Apps remote unter Linux erstellt werden, werden sie [über das Bereitstellungspaket ausgeführt](run-functions-from-deployment-package.md).

##### <a name="consumption-plan"></a>Verbrauchsplan

Linux-Funktions-Apps, die im Verbrauchsplan ausgeführt werden, umfassen keine SCM/Kudu-Website, wodurch die Bereitstellungsoptionen eingeschränkt sind. Funktions-Apps unter Linux, die im Verbrauchsplan ausgeführt werden, unterstützen jedoch Remotebuilds.

##### <a name="dedicated-and-premium-plans"></a>Dedizierte und Premium-Pläne

Funktions-Apps, die unter Linux im [App Service-Plan Dedicated](dedicated-plan.md) oder [Premium](functions-premium-plan.md) ausgeführt werden, umfassen auch eine eingeschränkte SCM/Kudu-Website.

## <a name="deployment-technology-details"></a>Bereitstellungstechnologie: Details

Die folgenden Bereitstellungsmethoden sind in Azure Functions verfügbar.

### <a name="external-package-url"></a>Externe Paket-URL

Sie können eine externe Paket-URL verwenden, um auf eine Remotepaketdatei (ZIP-Datei) zu verweisen, die ihre Funktions-App enthält. Die Datei wird von der angegebenen URL heruntergeladen, und die App wird im Modus [Aus Paketdatei ausführen](run-functions-from-deployment-package.md) ausgeführt.

>__Verwendung:__ Fügen Sie [`WEBSITE_RUN_FROM_PACKAGE`](functions-app-settings.md#website_run_from_package) zu Ihren Anwendungseinstellungen hinzu. Der Wert dieser Einstellung muss eine URL sein (der Speicherort der spezifischen Paketdatei, die Sie ausführen möchten). Einstellungen können entweder [über das Portal](functions-how-to-use-azure-function-app-settings.md#settings) oder [mithilfe der Azure-Befehlszeilenschnittstelle](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) hinzugefügt werden.
>
>Bei Verwendung von Blob Storage muss ein privater Container mit einer [Shared Access Signature (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) verwendet werden, damit Functions auf das Paket zugreifen kann. Jedes Mal, wenn die Anwendung neu gestartet wird, ruft Sie eine Kopie des Inhalts ab. Ihr Verweis muss für die Lebensdauer der Anwendung gültig sein.

>__Einsatzgebiete:__ Eine externe Paket-URL ist die einzige Bereitstellungsmethode, die für Azure Functions unter Linux im Verbrauchsplan unterstützt wird, wenn der Benutzer nicht möchte, dass ein [Remotebuild](#remote-build) ausgeführt wird. Wenn Sie die Paketdatei aktualisieren, auf die eine Funktions-App verweist, müssen Sie die [Trigger manuell synchronisieren](#trigger-syncing), um Azure darüber zu informieren, dass sich Ihre Anwendung geändert hat.

### <a name="zip-deploy"></a>ZIP-Bereitstellung

Verwenden Sie ZIP-Bereitstellung, um eine ZIP-Datei mit ihrer Funktions-APP in Azure zu pushen. Optional können Sie die App so festlegen, dass sie [über ein Paket ausgeführt wird](run-functions-from-deployment-package.md), oder Sie können angeben, dass ein [Remotebuild](#remote-build) ausgeführt wird.

>__Verwendung:__ Führen Sie die Bereitstellung mithilfe Ihres bevorzugten Clienttools aus: [Visual Studio Code](functions-develop-vs-code.md#publish-to-azure), [Visual Studio](functions-develop-vs.md#publish-to-azure) oder über die Befehlszeile mithilfe der [Azure Functions Core Tools](functions-run-local.md#project-file-deployment). Standardmäßig verwenden diese Tools ZIP-Bereitstellung und werden [aus dem Paket](run-functions-from-deployment-package.md) ausgeführt. Die Core Tools und die Visual Studio Code-Erweiterung aktivieren bei der Bereitstellung unter Linux die [Remotebuildfunktion](#remote-build). Eine Anleitung zum manuellen Bereitstellen einer ZIP-Datei für Ihre Funktions-App finden Sie unter [Bereitstellen mithilfe einer ZIP-Datei oder URL](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

>Wenn die Bereitstellung mithilfe der ZIP-Bereitstellung erfolgt, können Sie die App auf [Run from Package](run-functions-from-deployment-package.md) (Aus Paket ausführen) festlegen. Für die Ausführung über ein Paket legen Sie den Wert der [`WEBSITE_RUN_FROM_PACKAGE`](functions-app-settings.md#website_run_from_package-Anwendungseinstellung auf `1` fest. Wir empfehlen die ZIP-Bereitstellung. Sie führt zu schnelleren Ladezeiten für Ihre Anwendungen und ist die Standardeinstellung für VS Code, Visual Studio und die Azure CLI.

>__Einsatzgebiete:__ Die ZIP-Bereitstellung ist die empfohlene Bereitstellungstechnologie für Azure Functions.

### <a name="docker-container"></a>Docker-Container

Sie können ein Linux-Containerimage bereitstellen, das Ihre Funktions-App enthält.

>__Verwendung:__ Erstellen Sie eine Linux-Funktions-App im Plan „Premium“ oder „Dediziert“, und geben Sie das gewünschte Containerimage für die Ausführung an. Hierzu stehen zwei Möglichkeiten zur Verfügung:
>
>* Erstellen Sie eine Linux-Funktions-App unter einem Azure App Service-Plan über das Azure-Portal. Wählen Sie für **Veröffentlichen** das **Docker-Image** aus, und konfigurieren Sie dann den Container. Geben Sie den Speicherort ein, an dem das Image gehostet wird.
>* Erstellen Sie eine Linux-Funktions-App unter einem App Service-Plan mithilfe der Azure CLI. Eine entsprechende Anleitung finden Sie unter [Erstellen einer Funktion unter Linux mithilfe eines benutzerdefinierten Images](functions-create-function-linux-custom-image.md#create-supporting-azure-resources-for-your-function).
>
>Verwenden Sie für die Bereitstellung einer vorhandenen App mit einem benutzerdefinierten Container den Befehl [`func deploy`](functions-run-local.md#publish) der [Azure Functions Core Tools](functions-run-local.md).

>__Einsatzgebiete:__ Verwenden Sie die Option „Docker-Container“, wenn Sie mehr Kontrolle über die Linux-Umgebung benötigen, in der Ihre Funktions-App ausgeführt wird. Dieser Bereitstellungsmechanismus steht nur für Functions unter Linux zur Verfügung.

### <a name="web-deploy-msdeploy"></a>Web Deploy (MSDeploy)

Bei der Web Deploy-Methode werden Ihre Windows-Anwendungen verpackt und auf einem beliebigen IIS-Server bereitgestellt (einschließlich Ihrer Funktions-Apps unter Windows in Azure).

>__Verwendung:__ Verwenden Sie die [Visual Studio-Tools für Azure Functions](functions-create-your-first-function-visual-studio.md). Deaktivieren Sie das Kontrollkästchen **Aus Paketdatei ausführen (empfohlen)** .
>
>Sie können auch [Web Deploy 3.6](https://www.iis.net/downloads/microsoft/web-deploy) herunterladen und `MSDeploy.exe` direkt aufrufen.

>__Einsatzgebiete:__ Web Deploy wird zwar problemlos unterstützt, inzwischen wird jedoch der Mechanismus [ZIP-Bereitstellung im Modus „Aus Paketdatei ausführen“](#zip-deploy) bevorzugt. Weitere Informationen finden Sie im [Entwicklungsleitfaden für Visual Studio](functions-develop-vs.md#publish-to-azure).

### <a name="source-control"></a>Quellcodeverwaltung

Verbinden Sie Ihre Funktions-App mithilfe der Quellcodeverwaltung mit einem Git-Repository. Ein Update des Codes in diesem Repository löst die Bereitstellung aus. Weitere Informationen finden Sie im [Kudu-Wiki](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments).

>__Verwendung:__ Verwenden Sie das Bereitstellungscenter im Functions-Bereich des Portals, um die auf Quellcodeverwaltung basierende Veröffentlichung einzurichten. Weitere Informationen finden Sie unter [Continuous Deployment für Azure Functions](functions-continuous-deployment.md).

>__Einsatzgebiete:__ Die Verwendung von Quellcodeverwaltung ist die bewährte Methode für Teams, die an ihren Funktions-Apps zusammenarbeiten. Quellcodeverwaltung ist eine gute Bereitstellungsoption, die anspruchsvollere Bereitstellungspipelines ermöglicht.

### <a name="local-git"></a>Lokales Git

Bei dieser Methode können Sie Code unter Verwendung von lokalem Git von Ihrem lokalen Computer aus in Azure Functions pushen.

>__Verwendung:__ Eine entsprechende Anleitung finden Sie unter [Lokale Git-Bereitstellung in Azure App Service](../app-service/deploy-local-git.md).

>__Einsatzgebiete:__ Im Allgemeinen wird empfohlen, eine andere Bereitstellungsmethode zu verwenden. Bei der Veröffentlichung über ein lokales Git müssen Sie [Trigger manuell synchronisieren](#trigger-syncing).

### <a name="cloud-sync"></a>Cloudsynchronisierung

Verwenden Sie Cloudsynchronisierung, um Ihre Inhalte aus Dropbox und OneDrive mit Azure Functions zu synchronisieren.

>__Verwendung:__ Eine entsprechende Anleitung finden Sie unter [Synchronisieren von Inhalt aus einem Cloudordner in Azure App Service](../app-service/deploy-content-sync.md).

>__Einsatzgebiete:__ Im Allgemeinen werden andere Bereitstellungsmethoden empfohlen. Bei der Veröffentlichung mit Cloudsynchronisierung müssen Sie [Trigger manuell synchronisieren](#trigger-syncing).

### <a name="ftp"></a>FTP

Die können FTP verwenden, um Dateien direkt an Azure Functions zu übertragen.

>__Verwendung:__ Eine entsprechende Anleitung finden Sie unter [Bereitstellen von Inhalten mithilfe von FTP/S](../app-service/deploy-ftp.md).

>__Einsatzgebiete:__ Im Allgemeinen werden andere Bereitstellungsmethoden empfohlen. Bei der Veröffentlichung mit FTP müssen Sie [Trigger manuell synchronisieren](#trigger-syncing).

### <a name="portal-editing"></a>Portalbearbeitung

Im portalbasierten Editor können Sie die Dateien, die sich in ihrer Funktions-App befinden, direkt bearbeiten (im Wesentlichen erfolgt die Bereitstellung bei jedem Speichern der Änderungen).

>__Verwendung:__ Wenn Sie Ihre Funktionen im Azure-Portal bearbeiten möchten, müssen [Ihre Funktionen im Portal erstellt](./functions-get-started.md) worden sein. Bei Verwendung einer anderen Bereitstellungsmethode wird Ihre Funktion schreibgeschützt und kann nicht mehr über das Portal bearbeitet werden, um eine zentrale zuverlässige Datenquelle (Single Source Of Truth, SSOT) zu gewährleisten. Sie können den Bearbeitungsmodus manuell erneut auf `Read/Write` festlegen und alle bereitstellungsbezogenen Anwendungseinstellungen (etwa [`WEBSITE_RUN_FROM_PACKAGE`](functions-app-settings.md#website_run_from_package)) entfernen, um wieder zu einem Zustand zurückzukehren, in dem Sie Ihre Dateien über das Portal bearbeiten können.

>__Einsatzgebiete:__ Das Portal ist eine gute Möglichkeit, um erste Schritte mit Azure Functions auszuführen. Bei intensiveren Entwicklungsarbeiten empfiehlt sich die Verwendung eines der folgenden Clienttools:
>
>* [Visual Studio Code](./create-first-function-vs-code-csharp.md)
>* [Azure Functions Core Tools (Befehlszeile)](functions-run-local.md)
>* [Visual Studio](functions-create-your-first-function-visual-studio.md)

Die folgende Tabelle gibt Aufschluss über die Betriebssysteme und Programmiersprachen, die Portalbearbeitung unterstützen:

| Sprache | Windows: Verbrauch | Windows Premium | Windows: Dediziert | Linux: Verbrauch | Linux Premium | Linux: Dediziert |
|-|:-----------------: |:----------------:|:-----------------:|:-----------------:|:-------------:|:---------------:|
| C# | | | | | |
| C#-Skript |✔|✔|✔| |✔<sup>\*</sup> |✔<sup>\*</sup>|
| F# | | | | | | |
| Java | | | | | | |
| JavaScript (Node.js) |✔|✔|✔| |✔<sup>\*</sup>|✔<sup>\*</sup>|
| Python (Vorschauversion) | | | | | | |
| PowerShell (Vorschauversion) |✔|✔|✔| | | |
| TypeScript (Node.js) | | | | | | |

<sup>*</sup> Die Portalbearbeitung steht nur für HTTP-Trigger und Trigger mit Timer zur Verfügung (für Functions unter Linux mit dem Tarif „Premium“ oder „Dedicated“).

## <a name="deployment-behaviors"></a>Bereitstellungsverhalten

Wenn Sie eine Bereitstellung durchführen, können alle vorhandenen Ausführungen abgeschlossen oder zeitlich begrenzt werden. Danach wird der neue Code geladen, um mit der Verarbeitung von Anforderungen zu beginnen.

Wenn Sie mehr Kontrolle über diesen Übergang benötigen, sollten Sie Bereitstellungsslots verwenden.

## <a name="deployment-slots"></a>Bereitstellungsslots

Wenn Sie Ihre Funktions-App in Azure bereitstellen, können Sie als Bereitstellungsziel einen separaten Bereitstellungsslot verwenden, anstatt die Bereitstellung direkt in der Produktionsumgebung vorzunehmen. Weitere Informationen zu Bereitstellungsslots finden Sie in der Dokumentation zu [Azure Functions-Bereitstellungsslots](functions-deployment-slots.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Bereitstellen von Funktions-Apps finden Sie in den folgenden Artikeln:

+ [Kontinuierliche Bereitstellung für Azure Functions](functions-continuous-deployment.md)
+ [Continuous Delivery mit Azure DevOps](functions-how-to-azure-devops.md)
+ [ZIP-Bereitstellung für Azure Functions](deployment-zip-push.md)
+ [Ausführen Ihrer Azure Functions aus einem Paket](run-functions-from-deployment-package.md)
+ [Automatisieren der Ressourcenbereitstellung für Ihre Funktions-App in Azure Functions](functions-infrastructure-as-code.md)
