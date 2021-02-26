---
title: 'Schnellstart: Erstellen einer Verwaltungsgruppe mit Python'
description: In dieser Schnellstartanleitung erstellen Sie mithilfe von Python eine Verwaltungsgruppe, um Ihre Ressourcen in einer Ressourcenhierarchie zu organisieren.
ms.date: 01/29/2021
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: e3c55cc14a8ac980318fd0de9485a3e0ca31b582
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100101654"
---
# <a name="quickstart-create-a-management-group-with-python"></a>Schnellstart: Erstellen einer Verwaltungsgruppe mit Python

Bei Verwaltungsgruppen handelt es sich um Container, mit denen Sie Zugriff, Richtlinien und Konformität abonnementübergreifend verwalten können. Erstellen Sie diese Container, um eine effektive und effiziente Hierarchie zu erstellen, die mit [Azure Policy](../policy/overview.md) und mit der [rollenbasierten Zugriffssteuerung (Role-Based Access Controls, RBAC) von Azure](../../role-based-access-control/overview.md) verwendet werden kann. Weitere Informationen zu Verwaltungsgruppen finden Sie unter [Organisieren von Ressourcen mit Azure-Verwaltungsgruppen](overview.md).

Es kann bis zu 15 Minuten dauern, bis die Erstellung der ersten Verwaltungsgruppe im Verzeichnis abgeschlossen ist. Bei der ersten Erstellung werden Prozesse zum Einrichten des Verwaltungsgruppendiensts in Azure für Ihr Verzeichnis ausgeführt. Sie erhalten eine Benachrichtigung, wenn der Vorgang abgeschlossen ist. Weitere Informationen finden Sie unter [Erstmalige Einrichtung von Verwaltungsgruppen](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

- Alle Azure AD-Benutzer im Mandanten können eine Verwaltungsgruppe erstellen, ohne dafür die Schreibberechtigung für die Verwaltungsgruppe zu benötigen, wenn der [Hierarchieschutz](./how-to/protect-resource-hierarchy.md#setting---require-authorization) nicht aktiviert ist. Diese neue Verwaltungsgruppe ist dann der Stammverwaltungsgruppe oder der [Standardverwaltungsgruppe](./how-to/protect-resource-hierarchy.md#setting---default-management-group) untergeordnet, und dem Ersteller wird die Rolle „Besitzer“ zugewiesen. Der Verwaltungsgruppendienst ermöglicht dies, damit Rollen nicht auf Stammebene zugewiesen werden müssen. Bei der Erstellung der Stammverwaltungsgruppe hat kein Benutzer auf sie Zugriff. Der Sinn dahinter, dass Verwaltungsgruppen zu Beginn auf Stammebene erstellt werden können, ist die Vermeidung der Hürde, erst nach den globalen Administratoren für Azure AD suchen zu müssen, bevor Verwaltungsgruppen verwendet werden können.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-library"></a>Hinzufügen der Resource Graph-Bibliothek

Die Bibliothek muss hinzugefügt werden, um in Python die Verwaltung von Verwaltungsgruppen zu ermöglichen. Diese Bibliothek funktioniert überall dort, wo Python verwendet werden kann, dies schließt [Bash unter Windows 10](/windows/wsl/install-win10) oder eine lokale Installation ein.

1. Überprüfen Sie, ob die aktuelle Python-Version (mindestens **3.8**) installiert ist. Falls sie noch nicht installiert ist, laden Sie die Version von [Python.org](https://www.python.org/downloads/) herunter.

1. Überprüfen Sie, ob die aktuelle Azure CLI-Version (mindestens **2.5.1**) installiert ist. Falls sie noch nicht installiert ist, lesen Sie den Artikel [Installieren der Azure CLI](/cli/azure/install-azure-cli).

   > [!NOTE]
   > Azure CLI ist erforderlich, um Python für die Verwendung der **CLI-basierten Authentifizierung** in den folgenden Beispielen zu aktivieren. Informationen zu anderen Optionen finden Sie unter [Authentifizieren mit Azure-Verwaltungsbibliotheken für Python](/azure/developer/python/azure-sdk-authenticate).

1. Führen Sie die Authentifizierung über Azure CLI aus.

   ```azurecli
   az login
   ```

1. Installieren Sie in der Python-Umgebung Ihrer Wahl die erforderlichen Bibliotheken für Verwaltungsgruppen:

   ```bash
   # Add the management groups library for Python
   pip install azure-mgmt-managementgroups

   # Add the Resources library for Python
   pip install azure-mgmt-resource

   # Add the CLI Core library for Python for authentication (development only!)
   pip install azure-cli-core
   ```

   > [!NOTE]
   > Wenn Python für alle Benutzer installiert ist, muss dieser Befehl in einer Konsole mit erhöhten Rechten ausgeführt werden.

1. Überprüfen Sie, ob die Bibliotheken installiert wurden. `azure-mgmt-managementgroups` sollte mindestens **0.2.0** sein, `azure-mgmt-resource` sollte mindestens **9.0.0** sein, und `azure-cli-core` sollte mindestens **2.5.0** sein.

   ```bash
   # Check each installed library
   pip show azure-mgmt-managementgroups azure-mgmt-resource azure-cli-core
   ```

## <a name="create-the-management-group"></a>Erstellen der Verwaltungsgruppe

1. Erstellen Sie das Python-Skript, und speichern Sie die folgende Quelle als `mgCreate.py`:

   ```python
   # Import management group classes
   from azure.mgmt.managementgroups import ManagementGroupsAPI
   
   # Import specific methods and models from other libraries
   from azure.common.credentials import get_azure_cli_credentials
   from azure.common.client_factory import get_client_from_cli_profile
   from azure.mgmt.resource import ResourceManagementClient, SubscriptionClient
   
   # Wrap all the work in a function
   def createmanagementgroup( strName ):
       # Get your credentials from Azure CLI (development only!) and get your subscription list
       subsClient = get_client_from_cli_profile(SubscriptionClient)
       subsRaw = []
       for sub in subsClient.subscriptions.list():
           subsRaw.append(sub.as_dict())
       subsList = []
       for sub in subsRaw:
           subsList.append(sub.get('subscription_id'))
       
       # Create management group client and set options
       mgClient = get_client_from_cli_profile(ManagementGroupsAPI)
       mg_request = {'name': strName, 'display_name': strName}
       
       # Create management group
       mg = mgClient.management_groups.create_or_update(group_id=strName,create_management_group_request=mg_request)
       
       # Show results
       print(mg)
   
   createmanagementgroup("MyNewMG")
   ```

1. Authentifizieren Sie sich mit `az login` bei der Azure CLI.

1. Geben Sie den folgenden Befehl in das Terminal ein:

   ```bash
   py mgCreate.py
   ```

Das Ergebnis der Erstellung der Verwaltungsgruppe wird an die Konsole als `LROPoller`-Objekt ausgegeben.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die installierten Bibliotheken aus Ihrer Python-Umgebung entfernen möchten, können Sie dazu den folgenden Befehl verwenden:

```bash
# Remove the installed libraries from the Python environment
pip uninstall azure-mgmt-managementgroups azure-mgmt-resource azure-cli-core
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie eine Verwaltungsgruppe zum Organisieren der Ressourcenhierarchie erstellt. Die Verwaltungsgruppe kann Abonnements oder andere Verwaltungsgruppen enthalten.

Weitere Informationen zu Verwaltungsgruppen und zur Verwaltung Ihrer Ressourcenhierarchie finden Sie im folgenden Artikel:

> [!div class="nextstepaction"]
> Verwalten von Ressourcen mit Verwaltungsgruppen