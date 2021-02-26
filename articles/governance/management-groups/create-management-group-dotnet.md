---
title: 'Schnellstart: Erstellen einer Verwaltungsgruppe mit .NET Core'
description: In dieser Schnellstartanleitung erstellen Sie mithilfe von .NET Core eine Verwaltungsgruppe, um Ihre Ressourcen in einer Ressourcenhierarchie zu organisieren.
ms.date: 02/05/2021
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: a74cea9b142785c093b8ed235fc40049746f11a0
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99592550"
---
# <a name="quickstart-create-a-management-group-with-net-core"></a>Schnellstart: Erstellen einer Verwaltungsgruppe mit .NET Core

Bei Verwaltungsgruppen handelt es sich um Container, mit denen Sie Zugriff, Richtlinien und Konformität abonnementübergreifend verwalten können. Erstellen Sie diese Container, um eine effektive und effiziente Hierarchie zu erstellen, die mit [Azure Policy](../policy/overview.md) und mit der [rollenbasierten Zugriffssteuerung (Role-Based Access Controls, RBAC) von Azure](../../role-based-access-control/overview.md) verwendet werden kann. Weitere Informationen zu Verwaltungsgruppen finden Sie unter [Organisieren von Ressourcen mit Azure-Verwaltungsgruppen](overview.md).

Es kann bis zu 15 Minuten dauern, bis die Erstellung der ersten Verwaltungsgruppe im Verzeichnis abgeschlossen ist. Bei der ersten Erstellung werden Prozesse zum Einrichten des Verwaltungsgruppendiensts in Azure für Ihr Verzeichnis ausgeführt. Sie erhalten eine Benachrichtigung, wenn der Vorgang abgeschlossen ist. Weitere Informationen finden Sie unter [Erstmalige Einrichtung von Verwaltungsgruppen](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

- Ein Azure-Dienstprinzipal, einschließlich _clientId_ und _clientSecret_. Falls Sie nicht über einen Dienstprinzipal für die Verwendung mit Azure Policy verfügen oder einen neuen erstellen möchten, helfen Ihnen die Informationen unter [Azure-Verwaltungsbibliotheken für die .NET-Authentifizierung](/dotnet/azure/sdk/authentication#mgmt-auth) weiter.
  Überspringen Sie den Schritt zum Installieren der .NET Core-Pakete, weil wir dies in den nächsten Schritten durchführen.

- Alle Azure AD-Benutzer im Mandanten können eine Verwaltungsgruppe erstellen, ohne dafür die Schreibberechtigung für die Verwaltungsgruppe zu benötigen, wenn der [Hierarchieschutz](./how-to/protect-resource-hierarchy.md#setting---require-authorization) nicht aktiviert ist. Diese neue Verwaltungsgruppe ist dann der Stammverwaltungsgruppe oder der [Standardverwaltungsgruppe](./how-to/protect-resource-hierarchy.md#setting---default-management-group) untergeordnet, und dem Ersteller wird die Rolle „Besitzer“ zugewiesen. Der Verwaltungsgruppendienst ermöglicht dies, damit Rollen nicht auf Stammebene zugewiesen werden müssen. Bei der Erstellung der Stammverwaltungsgruppe hat kein Benutzer auf sie Zugriff. Der Sinn dahinter, dass Verwaltungsgruppen zu Beginn auf Stammebene erstellt werden können, ist die Vermeidung der Hürde, erst nach den globalen Administratoren für Azure AD suchen zu müssen, bevor Verwaltungsgruppen verwendet werden können.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="application-setup"></a>Anwendungseinrichtung

Erstellen Sie eine neue Konsolenanwendung, und installieren Sie die erforderlichen Pakete, um für .NET Core das Verwalten von Verwaltungsgruppen zu ermöglichen.

1. Überprüfen Sie, ob die aktuelle .NET Core-Version (mindestens **3.1.8**) installiert ist. Falls nicht, können Sie sie unter [dotnet.microsoft.com](https://dotnet.microsoft.com/download/dotnet-core) herunterladen.

1. Initialisieren Sie eine neue .NET Core-Konsolenanwendung mit dem Namen „mgCreate“:

   ```dotnetcli
   dotnet new console --name "mgCreate"
   ```

1. Ändern Sie die Verzeichnisse in den neuen Projektordner, und installieren Sie die erforderlichen Pakete für Azure Policy:

   ```dotnetcli
   # Add the Azure Policy package for .NET Core
   dotnet add package Microsoft.Azure.Management.ManagementGroups --version 1.1.1-preview

   # Add the Azure app auth package for .NET Core
   dotnet add package Microsoft.Azure.Services.AppAuthentication --version 1.5.0
   ```

1. Ersetzen Sie die Standarddatei `program.cs` durch den folgenden Code, und speichern Sie die aktualisierte Datei:

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Threading.Tasks;
   using Microsoft.IdentityModel.Clients.ActiveDirectory;
   using Microsoft.Rest;
   using Microsoft.Azure.Management.ManagementGroups;
   using Microsoft.Azure.Management.ManagementGroups.Models;
   
   namespace mgCreate
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strGroupId = args[3];
               string strDisplayName = args[4];
   
               var authContext = new AuthenticationContext($"https://login.microsoftonline.com/{strTenant}");
               var authResult = await authContext.AcquireTokenAsync(
                      "https://management.core.windows.net",
                      new ClientCredential(strClientId, strClientSecret));
   
               using (var client = new ManagementGroupsAPIClient(new TokenCredentials(authResult.AccessToken)))
               {
                   var mgRequest = new CreateManagementGroupRequest
                   {
                       DisplayName = strDisplayName
                   };
                   var response = await client.ManagementGroups.CreateOrUpdateAsync(strGroupId, mgRequest);
               }
           }
       }
   }
   ```

1. Kompilieren und veröffentlichen Sie die Konsolenanwendung `mgCreate`:

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

## <a name="create-the-management-group"></a>Erstellen der Verwaltungsgruppe

In dieser Schnellstartanleitung erstellen Sie eine neue Verwaltungsgruppe in der Stammverwaltungsgruppe.

1. Ändern Sie die Verzeichnisse in den `{run-folder}`, den Sie mit dem vorherigen `dotnet publish`-Befehl definiert haben.

1. Geben Sie den folgenden Befehl in das Terminal ein:

   ```bash
   mgCreate.exe `
      "{tenantId}" `
      "{clientId}" `
      "{clientSecret}" `
      "{groupID}" `
      "{displayName}"
   ```

In diesen Befehlen werden folgende Informationen verwendet:

- Ersetzen Sie `{tenantId}` durch Ihre Mandanten-ID.
- `{clientId}`: Ersetzen Sie diese ID durch die Client-ID Ihres Dienstprinzipals.
- `{clientSecret}`: Ersetzen Sie dieses Geheimnis durch den geheimen Clientschlüssel Ihres Dienstprinzipals.
- `{groupID}`: Ersetzen Sie diese Variable durch die ID der neuen Verwaltungsgruppe.
- `{displayName}`: Ersetzen Sie diese Variable durch den Anzeigenamen der neuen Verwaltungsgruppe.

Das Ergebnis ist eine neue Verwaltungsgruppe in der Stammverwaltungsgruppe.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

- Löschen Sie die neue Ressourcengruppe über das Portal.

- Falls Sie die .NET Core-Konsolenanwendung und die installierten Pakete entfernen möchten, löschen Sie den Projektordner `mgCreate`.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie eine Verwaltungsgruppe zum Organisieren der Ressourcenhierarchie erstellt. Die Verwaltungsgruppe kann Abonnements oder andere Verwaltungsgruppen enthalten.

Weitere Informationen zu Verwaltungsgruppen und zur Verwaltung Ihrer Ressourcenhierarchie finden Sie im folgenden Artikel:

> [!div class="nextstepaction"]
> Verwalten von Ressourcen mit Verwaltungsgruppen