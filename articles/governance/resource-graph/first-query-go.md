---
title: 'Schnellstart: Ihre erste Go-Abfrage'
description: In dieser Schnellstartanleitung führen Sie die Schritte zum Aktivieren des Resource Graph-Pakets für Go und zum Ausführen Ihrer ersten Abfrage aus.
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: 4949801b3dc97904680d09e685fd225812a0e14d
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920061"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-go"></a>Schnellstart: Ausführen Ihrer ersten Resource Graph-Abfrage mithilfe von Go

Wenn Sie Azure Resource Graph verwenden möchten, müssen Sie zuerst überprüfen, ob die erforderlichen Pakete für Go installiert sind. In dieser Schnellstartanleitung werden Sie durch den Prozess zum Hinzufügen der Pakete zu Ihrer Go-Installation geführt.

Am Ende dieses Prozesses haben Sie Ihrer Go-Installation die Pakete hinzugefügt und Ihre erste Resource Graph-Abfrage ausgeführt.

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="add-the-resource-graph-package"></a>Hinzufügen des Resource Graph-Pakets

Damit Go Azure Resource Graph abfragen kann, muss das Paket hinzugefügt werden. Dieses Paket funktioniert überall dort, wo Go verwendet werden kann. Dies schließt [Bash unter Windows 10](/windows/wsl/install-win10) oder eine lokale Installation ein.

1. Überprüfen Sie, ob die aktuelle Go-Version (mindestens **1.14**) installiert ist. Falls sie noch nicht installiert ist, laden Sie die Version von [Golang.org](https://golang.org/dl/) herunter.

1. Überprüfen Sie, ob die aktuelle Azure CLI-Version (mindestens **2.5.1**) installiert ist. Falls sie noch nicht installiert ist, lesen Sie den Artikel [Installieren der Azure CLI](/cli/azure/install-azure-cli).

   > [!NOTE]
   > Die Azure CLI ist erforderlich, um Go die Verwendung der `auth.NewAuthorizerFromCLI()`-Methode im folgenden Beispiel zu ermöglichen. Weitere Informationen zu anderen Optionen finden Sie unter [Azure SDK für Go – Weitere Authentifizierungsdetails](https://github.com/Azure/azure-sdk-for-go#more-authentication-details).

1. Führen Sie die Authentifizierung über Azure CLI aus.

   ```azurecli
   az login
   ```

1. Installieren Sie in der Go-Umgebung Ihrer Wahl die erforderlichen Pakete für Azure Resource Graph:

   ```bash
   # Add the Resource Graph package for Go
   go get -u github.com/Azure/azure-sdk-for-go/services/resourcegraph/mgmt/2019-04-01/resourcegraph

   # Add the Azure auth package for Go
   go get -u github.com/Azure/go-autorest/autorest/azure/auth
   ```

## <a name="run-your-first-resource-graph-query"></a>Ausführen Ihrer ersten Resource Graph-Abfrage

Nachdem die Go-Pakete der Umgebung Ihrer Wahl hinzugefügt wurden, können Sie jetzt eine einfache Resource Graph-Abfrage ausprobieren. Die Abfrage gibt die ersten fünf Azure-Ressourcen mit dem **Namen** und **Ressourcentyp** der einzelnen Ressourcen zurück.

1. Erstellen Sie die Go-Anwendung, und speichern Sie die folgende Quelle als `argQuery.go`:

   ```Go
   package main
   
   import (
      "fmt"
      "os"
      "context"
      "strconv"
      arg "github.com/Azure/azure-sdk-for-go/services/resourcegraph/mgmt/2019-04-01/resourcegraph"
      "github.com/Azure/go-autorest/autorest/azure/auth"
   )
   
   func main() {
       // Get variables from command line arguments
       var query = os.Args[1]
       var subList = os.Args[2:]
   
       // Create and authorize a ResourceGraph client
       argClient := arg.New()
       authorizer, err := auth.NewAuthorizerFromCLI()
       if err == nil {
           argClient.Authorizer = authorizer
       } else {
           fmt.Printf(err.Error())
       }
     
       // Set options
       RequestOptions := arg.QueryRequestOptions {
           ResultFormat: "objectArray",
       }
     
       // Create the query request
       Request := arg.QueryRequest {
           Subscriptions: &subList,
           Query: &query,
           Options: &RequestOptions,
       }
     
       // Run the query and get the results
       var results, queryErr = argClient.Resources(context.Background(), Request)
       if queryErr == nil {
           fmt.Printf("Resources found: " + strconv.FormatInt(*results.TotalRecords, 10) + "\n")
           fmt.Printf("Results: " + fmt.Sprint(results.Data) + "\n")
       } else {
           fmt.Printf(queryErr.Error())
       }
   }
   ```

1. Erstellen Sie die Go-Anwendung:

   ```bash
   go build argQuery.go
   ```

1. Führen Sie Ihre erste Azure Resource Graph-Abfrage mithilfe der kompilierten Go-Anwendung aus. Ersetzen Sie `<SubID>` durch Ihre Abonnement-ID:

   ```bash
   argQuery "Resources | project name, type | limit 5" "<SubID>"
   ```

   > [!NOTE]
   > Da dieses Abfragebeispiel keinen Sortierungsmodifizierer wie z.B. `order by` umfasst, ergibt die mehrfache Ausführung dieser Abfrage vermutlich pro Anforderung einen anderen Satz von Ressourcen.

1. Ändern Sie den ersten Parameter in `argQuery`, und ändern Sie die Abfrage so, dass die Sortierung (`order by`) nach der Eigenschaft **Name** erfolgt: Ersetzen Sie `<SubID>` durch Ihre Abonnement-ID:

   ```bash
   argQuery "Resources | project name, type | limit 5 | order by name asc" "<SubID>"
   ```

   > [!NOTE]
   > Genau wie bei der ersten Abfrage ergibt die mehrfache Ausführung dieser Abfrage vermutlich pro Anforderung einen anderen Satz von Ressourcen. Die Reihenfolge der Abfragebefehle ist wichtig. In diesem Beispiel kommt `order by` nach `limit`. Durch diese Befehlsreihenfolge werden die Abfrageergebnisse zuerst eingeschränkt und dann sortiert.

1. Ändern Sie den ersten Parameter in `argQuery`, und ändern Sie die Abfrage so, dass zuerst die Sortierung (`order by`) nach der Eigenschaft **Name** und dann die Begrenzung (`limit`) auf die ersten fünf Ergebnisse erfolgt: Ersetzen Sie `<SubID>` durch Ihre Abonnement-ID:

   ```bash
   argQuery "Resources | project name, type | order by name asc | limit 5" "<SubID>"
   ```

Wenn die letzte Abfrage mehrmals ausgeführt wird und in Ihrer Umgebung keine Änderungen vorgenommenen werden, sind die zurückgegebenen Ergebnisse konsistent und nach der Eigenschaft **Name** sortiert, aber immer noch auf die ersten fünf Ergebnisse begrenzt.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die installierten Pakete aus Ihrer Go-Umgebung entfernen möchten, können Sie dazu den folgenden Befehl verwenden:

```bash
# Remove the installed packages from the Go environment
go clean -i github.com/Azure/azure-sdk-for-go/services/resourcegraph/mgmt/2019-04-01/resourcegraph
go clean -i github.com/Azure/go-autorest/autorest/azure/auth
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie Ihrer Go-Umgebung die Resource Graph-Pakete hinzugefügt und Ihre erste Abfrage ausgeführt. Wenn Sie mehr über die Resource Graph-Sprache erfahren möchten, fahren Sie mit der Seite mit den Details zur Abfragesprache fort.

> [!div class="nextstepaction"]
> [Erfahren Sie mehr über die Abfragesprache](./concepts/query-language.md).