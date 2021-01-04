---
title: 'Tutorial: Auslösen einer Containergruppe nach Azure-Funktion'
description: Erstellen einer über HTTP ausgelösten, serverlosen PowerShell-Funktion zum Automatisieren der Erstellung von Azure-Containerinstanzen
ms.topic: tutorial
ms.date: 06/10/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7b94d583ca26b88d093810528d3193f20d765f1d
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349247"
---
# <a name="tutorial-use-an-http-triggered-azure-function-to-create-a-container-group"></a>Tutorial: Verwenden einer über HTTP ausgelösten Azure-Funktion zum Erstellen einer Containergruppe

[Azure Functions](../azure-functions/functions-overview.md) ist ein serverloser Computedienst, der Skripts oder Code als Reaktion auf eine Vielzahl von Ereignissen ausführen kann, z. B. eine HTTP-Anforderung, einen Timer oder eine Nachricht in einer Azure Storage-Warteschlange.

In diesem Tutorial erstellen Sie eine Azure-Funktion, die über eine HTTP-Anforderung die Bereitstellung einer [Containergruppe](container-instances-container-groups.md) auslöst. In diesem Beispiel werden die Grundlagen der Verwendung von Azure Functions zum automatischen Erstellen von Ressourcen in Azure Container Instances veranschaulicht. Sie können das Beispiel für komplexere Szenarien oder andere Ereignistrigger ändern oder erweitern. 

Folgendes wird vermittelt:

> [!div class="checklist"]
> * Verwenden von Visual Studio Code mit der [Azure Functions-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions), um eine einfache über HTTP ausgelöste PowerShell-Funktion zu erstellen
> * Aktivieren einer Identität in der Funktions-App und Erteilen von Berechtigungen zum Erstellen von Azure-Ressourcen
> * Ändern und erneutes Veröffentlichen der PowerShell-Funktion, um die Bereitstellung einer Containergruppe mit nur einem Container zu automatisieren
> * Überprüfen der über HTTP ausgelösten Bereitstellung des Containers

## <a name="prerequisites"></a>Voraussetzungen

Die Voraussetzungen für die Installation und Verwendung von Visual Studio Code mit der Azure Functions-Erweiterung unter Ihrem Betriebssystem finden Sie unter [Konfigurieren Ihrer Umgebung](../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-powershell#configure-your-environment).

Für zusätzliche Schritte in diesem Artikel wird Azure PowerShell verwendet. Wenn Sie eine Installation oder ein Upgrade ausführen müssen, lesen Sie die Informationen unter [Installieren von Azure PowerShell][azure-powershell-install] und [Anmelden bei Azure](/powershell/azure/get-started-azureps#sign-in-to-azure).

## <a name="create-a-basic-powershell-function"></a>Erstellen einer einfachen PowerShell-Funktion

Führen Sie die Schritte unter [Erstellen Ihrer ersten PowerShell-Funktion in Azure](../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-powershell) aus, um mit der Vorlage „HTTP-Trigger“ eine PowerShell-Funktion zu erstellen. Verwenden Sie für die Azure-Funktion den Standardnamen **HttpTrigger**. Folgen Sie den Anweisungen im Schnellstart, um die Funktion lokal zu testen, und veröffentlichen Sie das Projekt in einer Funktions-App in Azure. Dieses Beispiel ist eine einfache, über HTTP ausgelöste Funktion, die eine Textzeichenfolge zurückgibt. In späteren Schritten dieses Artikels ändern Sie die Funktion, um eine Containergruppe zu erstellen.

In diesem Artikel wird davon ausgegangen, dass Sie das Projekt mit dem Namen *myfunctionapp* in einer Azure-Ressourcengruppe veröffentlichen, die automatisch entsprechend dem Namen der Funktions-App benannt wird (auch *myfunctionapp*). Ersetzen Sie diese Namen in späteren Schritten durch den eindeutigen Namen Ihrer Funktions-App und Ressourcengruppe.

## <a name="enable-an-azure-managed-identity-in-the-function-app"></a>Aktivieren einer von Azure verwalteten Identität in der Funktions-App

Mit den folgenden Befehlen wird eine systemseitig zugewiesene [verwaltete Identität](../app-service/overview-managed-identity.md?toc=/azure/azure-functions/toc.json#add-a-system-assigned-identity) in Ihrer Funktions-App aktiviert. Der PowerShell-Host, auf dem die App ausgeführt wird, kann sich automatisch mit dieser Identität bei Azure authentifizieren, sodass Funktionen Aktionen für Azure-Dienste ausführen können, für die der Identität Zugriff gewährt wird. In diesem Tutorial erteilen Sie der verwalteten Identität Berechtigungen zum Erstellen von Ressourcen in der Ressourcengruppe der Funktions-App. 

Sie müssen der Funktions-App eine [Identität hinzufügen](../app-service/overview-managed-identity.md?tabs=dotnet#using-azure-powershell-1):

```powershell
Update-AzFunctionApp -Name myfunctionapp `
    -ResourceGroupName myfunctionapp `
    -IdentityType SystemAssigned
```

Weisen Sie der Identität die Rolle „Mitwirkender“ der Gruppe im Ressourcengruppenbereich zu:

```powershell
$SP=(Get-AzADServicePrincipal -DisplayName myfunctionapp).Id
$RG=(Get-AzResourceGroup -Name myfunctionapp).ResourceId
New-AzRoleAssignment -ObjectId $SP -RoleDefinitionName "Contributor" -Scope $RG
```

## <a name="modify-httptrigger-function"></a>Ändern der HttpTrigger-Funktion

Ändern Sie den PowerShell-Code für die **HttpTrigger**-Funktion, um eine Containergruppe zu erstellen. Suchen Sie in der Datei `run.ps1` für die Funktion nach dem folgenden Codeblock. Dieser Code zeigt einen Namenswert an, wenn ein solcher Wert in der Funktions-URL als Abfragezeichenfolge übergeben wird:

```powershell
[...]
if ($name) {
    $body = "Hello, $name. This HTTP triggered function executed successfully."
}
[...]
```

Ersetzen Sie diesen Code durch den folgenden Beispielblock. Hier wird ein in der Abfragezeichenfolge übergebener Namenswert verwendet, um mit dem Cmdlet [New-AzContainerGroup][new-azcontainergroup] eine Containergruppe zu benennen und zu erstellen. Denken Sie daran, den Ressourcengruppennamen *myfunctionapp* durch den Namen der Ressourcengruppe für Ihre Funktions-App zu ersetzen:

```powershell
[...]
if ($name) {
    New-AzContainerGroup -ResourceGroupName myfunctionapp -Name $name `
        -Image alpine -OsType Linux `
        -Command "echo 'Hello from an Azure container instance triggered by an Azure function'" `
        -RestartPolicy Never
    if ($?) {
        $body = "This HTTP triggered function executed successfully. Started container group $name"
    }
    else  {
        $body = "There was a problem starting the container group."
    }
[...]
```

Dieses Beispiel erstellt eine Containergruppe mit einer einzelnen Containerinstanz, die das Image `alpine` ausführt. Der Container führt einen einzigen Befehl `echo` aus und wird dann beendet. In der Praxis könnten Sie beispielsweise die Erstellung einer oder mehrerer Containergruppen zum Ausführen eines Batchauftrags auslösen.
 
## <a name="test-function-app-locally"></a>Lokales Testen der Funktion

Stellen Sie sicher, dass die Funktion lokal ausgeführt wird, bevor Sie das Funktions-App-Projekt erneut in Azure veröffentlichen. Wenn die Funktion lokal ausgeführt wird, erstellt sie keine Azure-Ressourcen. Sie können den Funktionsflow jedoch mit und ohne Übergabe eines Namenswerts in einer Abfragezeichenfolge testen. Informationen zum Debuggen der Funktion finden Sie unter [Lokales Debuggen von PowerShell Azure Functions](../azure-functions/functions-debug-powershell-local.md).

## <a name="republish-azure-function-app"></a>Erneutes Veröffentlichen der Azure-Funktions-App

Nachdem Sie sichergestellt haben, dass die Funktion lokal ausgeführt wird, können Sie das Projekt erneut in der vorhandenen Funktions-App in Azure veröffentlichen.

1. Öffnen Sie in Visual Studio Code die Befehlspalette. Suchen Sie nach `Azure Functions: Deploy to Function App...`, und wählen Sie diese Option aus.
1. Wählen Sie den aktuellen Arbeitsordner zum Zippen und Bereitstellen aus.
1. Wählen Sie das Abonnement und dann den Namen der vorhandenen Funktions-App (*myfunctionapp*) aus. Bestätigen Sie, dass Sie die vorherige Bereitstellung überschreiben möchten.

Nach der Erstellung der Funktions-App wird eine Benachrichtigung angezeigt, und das Bereitstellungspaket wird angewendet. Wählen Sie in dieser Benachrichtigung **Ausgabe anzeigen** aus, um die Erstellungs- und Bereitstellungsergebnisse (auch für die von Ihnen aktualisierten Azure-Ressourcen) anzuzeigen.

## <a name="run-the-function-in-azure"></a>Ausführen der Funktion in Azure

Rufen Sie nach erfolgreichem Abschluss der Bereitstellung die Funktions-URL ab. Verwenden Sie beispielsweise den Bereich **Azure: Funktionen** in Visual Studio Code, um die **HttpTrigger**-Funktions-URL zu kopieren, oder rufen Sie die URL im [Azure-Portal](../azure-functions/functions-create-first-azure-function.md#test-the-function) ab.

Die Funktions-URL hat folgendes Format:

```
https://myfunctionapp.azurewebsites.net/api/HttpTrigger
```

### <a name="run-function-without-passing-a-name"></a>Ausführen der Funktion, ohne einen Namen zu übergeben

Führen Sie als ersten Test den Befehl `curl` aus, und übergeben Sie die Funktions-URL, ohne eine `name`-Abfragezeichenfolge anzufügen. 

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger"
```

Die Funktion gibt den Statuscode 200 und den Text `This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response` zurück:

```
[...]
> GET /api/HttpTrigger? HTTP/1.1
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.64.1
> Accept: */*
> 
* Connection state changed (MAX_CONCURRENT_STREAMS updated)!
< HTTP/1.1 200 OK
< Content-Length: 135
< Content-Type: text/plain; charset=utf-8
< Request-Context: appId=cid-v1:d0bd0123-f713-4579-8990-bb368a229c38
< Date: Wed, 10 Jun 2020 17:50:27 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.* Closing connection 0
```

### <a name="run-function-and-pass-the-name-of-a-container-group"></a>Ausführen der Funktion und Übergeben des Namens einer Containergruppe

Führen Sie nun den Befehl `curl` aus, und fügen Sie den Namen einer Containergruppe (*mycontainergroup*) als Abfragezeichenfolge `?name=mycontainergroup` an:

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?name=mycontainergroup"
```

Die Funktion gibt den Statuscode 200 zurück und löst die Erstellung der Containergruppe aus:

```
[...]
> GET /api/HttpTrigger1?name=mycontainergroup HTTP/1.1
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.64.1
> Accept: */*
> 
< HTTP/1.1 200 OK
< Content-Length: 92
< Content-Type: text/plain; charset=utf-8
< Request-Context: appId=cid-v1:d0bd0123-f713-4579-8990-bb368a229c38
< Date: Wed, 10 Jun 2020 17:54:31 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
This HTTP triggered function executed successfully. Started container group mycontainergroup* Closing connection 0
```

Vergewissern Sie sich mit dem Befehl [Get-AzContainerInstanceLog][get-azcontainerinstancelog], dass der Container ausgeführt wurde:

```azurecli
Get-AzContainerInstanceLog -ResourceGroupName myfunctionapp `
  -ContainerGroupName mycontainergroup 
```

Beispielausgabe:

```console
Hello from an Azure container instance triggered by an Azure function
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die in diesem Tutorial erstellten Ressourcen nicht mehr benötigen, können Sie den Befehl [az group delete][az-group-delete] ausführen, um die Ressourcengruppe und alle darin enthaltenen Ressourcen zu entfernen. Dieser Befehl löscht die von Ihnen erstellte Funktions-App sowie den ausgeführten Container und alle zugehörigen Ressourcen.

```azurecli-interactive
az group delete --name myfunctionapp
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie eine Azure-Funktion erstellt, die über eine HTTP-Anforderung die Bereitstellung einer Containergruppe auslöst. Sie haben Folgendes gelernt:

> [!div class="checklist"]
> * Verwenden von Visual Studio Code mit der Azure Functions-Erweiterung, um eine einfache über HTTP ausgelöste PowerShell-Funktion zu erstellen
> * Aktivieren einer Identität in der Funktions-App und Erteilen von Berechtigungen zum Erstellen von Azure-Ressourcen
> * Ändern des Codes der PowerShell-Funktion, um die Bereitstellung einer Containergruppe mit nur einem Container zu automatisieren
> * Überprüfen der über HTTP ausgelösten Bereitstellung des Containers

Ein ausführliches Beispiel zum Starten und Überwachen eines Containerauftrags finden Sie im Blogbeitrag [Event-Driven Serverless Containers with PowerShell Azure Functions and Azure Container Instances](https://dev.to/azure/event-driven-serverless-containers-with-powershell-azure-functions-and-azure-container-instances-e9b) (Ereignisgesteuerte serverlose Container mit PowerShell Azure Functions und Azure Container Instances) und dem zugehörigen [Codebeispiel](https://github.com/anthonychu/functions-powershell-run-aci).

Eine detaillierte Anleitung zum Erstellen von Azure-Funktionen und Veröffentlichen eines Functions-Projekts finden Sie in der [Azure Functions-Dokumentation](../azure-functions/index.yml). 

<!-- IMAGES -->


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-powershell-install]: /powershell/azure/install-az-ps
[new-azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[get-azcontainerinstancelog]: /powershell/module/az.containerinstance/get-azcontainerinstancelog