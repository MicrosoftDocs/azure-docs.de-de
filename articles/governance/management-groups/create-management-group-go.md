---
title: 'Schnellstart: Erstellen einer Verwaltungsgruppe mit Go'
description: In dieser Schnellstartanleitung erstellen Sie mithilfe von Go eine Verwaltungsgruppe, um Ihre Ressourcen in einer Ressourcenhierarchie zu organisieren.
ms.date: 09/30/2020
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 7b6ca4d10f2a86ecb55fec2afe72b4aabfbb94f9
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100101672"
---
# <a name="quickstart-create-a-management-group-with-go"></a>Schnellstart: Erstellen einer Verwaltungsgruppe mit Go

Bei Verwaltungsgruppen handelt es sich um Container, mit denen Sie Zugriff, Richtlinien und Konformität abonnementübergreifend verwalten können. Erstellen Sie diese Container, um eine effektive und effiziente Hierarchie zu erstellen, die mit [Azure Policy](../policy/overview.md) und mit der [rollenbasierten Zugriffssteuerung (Role-Based Access Controls, RBAC) von Azure](../../role-based-access-control/overview.md) verwendet werden kann. Weitere Informationen zu Verwaltungsgruppen finden Sie unter [Organisieren von Ressourcen mit Azure-Verwaltungsgruppen](overview.md).

Es kann bis zu 15 Minuten dauern, bis die Erstellung der ersten Verwaltungsgruppe im Verzeichnis abgeschlossen ist. Bei der ersten Erstellung werden Prozesse zum Einrichten des Verwaltungsgruppendiensts in Azure für Ihr Verzeichnis ausgeführt. Sie erhalten eine Benachrichtigung, wenn der Vorgang abgeschlossen ist. Weitere Informationen finden Sie unter [Erstmalige Einrichtung von Verwaltungsgruppen](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

- Ein Azure-Dienstprinzipal, einschließlich _clientId_ und _clientSecret_. Falls Sie nicht über einen Dienstprinzipal für die Verwendung mit Azure Policy verfügen oder einen neuen erstellen möchten, helfen Ihnen die Informationen unter [Azure-Verwaltungsbibliotheken für die .NET-Authentifizierung](/dotnet/azure/sdk/authentication#mgmt-auth) weiter.
  Überspringen Sie den Schritt zum Installieren der .NET Core-Pakete, weil wir dies in den nächsten Schritten durchführen.

- Alle Azure AD-Benutzer im Mandanten können eine Verwaltungsgruppe erstellen, ohne dafür die Schreibberechtigung für die Verwaltungsgruppe zu benötigen, wenn der [Hierarchieschutz](./how-to/protect-resource-hierarchy.md#setting---require-authorization) nicht aktiviert ist. Diese neue Verwaltungsgruppe ist dann der Stammverwaltungsgruppe oder der [Standardverwaltungsgruppe](./how-to/protect-resource-hierarchy.md#setting---default-management-group) untergeordnet, und dem Ersteller wird die Rolle „Besitzer“ zugewiesen. Der Verwaltungsgruppendienst ermöglicht dies, damit Rollen nicht auf Stammebene zugewiesen werden müssen. Bei der Erstellung der Stammverwaltungsgruppe hat kein Benutzer auf sie Zugriff. Der Sinn dahinter, dass Verwaltungsgruppen zu Beginn auf Stammebene erstellt werden können, ist die Vermeidung der Hürde, erst nach den globalen Administratoren für Azure AD suchen zu müssen, bevor Verwaltungsgruppen verwendet werden können.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-management-group-package"></a>Hinzufügen des Verwaltungsgruppenpakets

Das Paket muss hinzugefügt werden, um in Go die Verwaltung von Verwaltungsgruppen zu ermöglichen. Dieses Paket funktioniert überall dort, wo Go verwendet werden kann. Dies schließt [Bash unter Windows 10](/windows/wsl/install-win10) oder eine lokale Installation ein.

1. Überprüfen Sie, ob die aktuelle Go-Version (mindestens **1.15**) installiert ist. Falls sie noch nicht installiert ist, laden Sie die Version von [Golang.org](https://golang.org/dl/) herunter.

1. Überprüfen Sie, ob die aktuelle Azure CLI-Version (mindestens **2.5.1**) installiert ist. Falls sie noch nicht installiert ist, lesen Sie den Artikel [Installieren der Azure CLI](/cli/azure/install-azure-cli).

   > [!NOTE]
   > Die Azure CLI ist erforderlich, um Go die Verwendung der `auth.NewAuthorizerFromCLI()`-Methode im folgenden Beispiel zu ermöglichen. Weitere Informationen zu anderen Optionen finden Sie unter [Azure SDK für Go – Weitere Authentifizierungsdetails](https://github.com/Azure/azure-sdk-for-go#more-authentication-details).

1. Führen Sie die Authentifizierung über Azure CLI aus.

   ```azurecli
   az login
   ```

1. Installieren Sie in der Go-Umgebung Ihrer Wahl die erforderlichen Pakete für Verwaltungsgruppen:

   ```bash
   # Add the management group package for Go
   go get -u github.com/Azure/azure-sdk-for-go/services/preview/resources/mgmt/2018-03-01-preview/managementgroups

   # Add the Azure auth package for Go
   go get -u github.com/Azure/go-autorest/autorest/azure/auth
   ```

## <a name="application-setup"></a>Anwendungseinrichtung

Wurden die Go-Pakete der Umgebung Ihrer Wahl hinzugefügt, ist es an der Zeit, die Go-Anwendung einzurichten, die eine Verwaltungsgruppe erstellen kann.

1. Erstellen Sie die Go-Anwendung, und speichern Sie die folgende Quelle als `mgCreate.go`:

   ```Go
   package main
   
   import (
    "context"
    "fmt"
    "os"
   
    mg "github.com/Azure/azure-sdk-for-go/services/preview/resources/mgmt/2018-03-01-preview/managementgroups"
    "github.com/Azure/go-autorest/autorest/azure/auth"
   )
   
   func main() {
    // Get variables from command line arguments
    var mgName = os.Args[1]
   
    // Create and authorize a client
    mgClient := mg.NewClient()
    authorizer, err := auth.NewAuthorizerFromCLI()
    if err == nil {
        mgClient.Authorizer = authorizer
    } else {
        fmt.Printf(err.Error())
    }
   
    // Create the request
    Request := mg.CreateManagementGroupRequest{
        Name: &mgName,
    }
   
    // Run the query and get the results
    var results, queryErr = mgClient.CreateOrUpdate(context.Background(), mgName, Request, "no-cache")
    if queryErr == nil {
        fmt.Printf("Results: " + fmt.Sprint(results) + "\n")
    } else {
        fmt.Printf(queryErr.Error())
    }
   }
   ```

1. Erstellen Sie die Go-Anwendung:

   ```bash
   go build mgCreate.go
   ```

1. Erstellen Sie mithilfe der kompilierten Go-Anwendung eine Verwaltungsgruppe. Ersetzen Sie `<Name>` durch den Namen Ihrer neuen Verwaltungsgruppe:

   ```bash
   mgCreate "<Name>"
   ```

Das Ergebnis ist eine neue Verwaltungsgruppe in der Stammverwaltungsgruppe.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die installierten Pakete aus Ihrer Go-Umgebung entfernen möchten, können Sie dazu den folgenden Befehl verwenden:

```bash
# Remove the installed packages from the Go environment
go clean -i github.com/Azure/azure-sdk-for-go/services/preview/resources/mgmt/2018-03-01-preview/managementgroups
go clean -i github.com/Azure/go-autorest/autorest/azure/auth
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie eine Verwaltungsgruppe zum Organisieren der Ressourcenhierarchie erstellt. Die Verwaltungsgruppe kann Abonnements oder andere Verwaltungsgruppen enthalten.

Weitere Informationen zu Verwaltungsgruppen und zur Verwaltung Ihrer Ressourcenhierarchie finden Sie im folgenden Artikel:

> [!div class="nextstepaction"]
> Verwalten von Ressourcen mit Verwaltungsgruppen