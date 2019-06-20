---
title: Herstellen einer Verbindung mit dem Azure Data Explorer von Azure Databricks mit Python
description: In diesem Thema erfahren Sie, wie Sie eine Python-Bibliothek in Azure Databricks verwenden, um vom Azure Data Explorer aus mit einer der beiden Authentifizierungsmethoden auf Daten zuzugreifen.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/27/2018
ms.openlocfilehash: 55257d441916971b505432247f28033d6222c3be
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60861205"
---
# <a name="connect-to-azure-data-explorer-from-azure-databricks-by-using-python"></a>Herstellen einer Verbindung mit dem Azure Data Explorer von Azure Databricks mit Python

[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks) ist eine Apache Spark-basierte Analyseplattform, die für die Microsoft Azure Platform optimiert ist. In diesem Artikel erfahren Sie, wie Sie eine Python-Bibliothek in Azure Databricks verwenden, um vom Azure Data Explorer aus auf Daten zuzugreifen. Es gibt mehrere Möglichkeiten der Authentifizierung mit Azure Data Explorer, z.B. per Geräteanmeldung und per Azure AD-App (Azure Active Directory).

## <a name="prerequisites"></a>Voraussetzungen

- [Erstellen eines Azure Data Explorer-Clusters und einer Datenbank](/azure/data-explorer/create-cluster-database-portal)
- [Erstellen eines Azure Databricks-Arbeitsbereichs](/azure/azure-databricks/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace). Wählen Sie unter **Azure Databricks-Dienst** in der Dropdownliste **Tarif** die Option **Premium**. Wenn Sie diese Option wählen, können Sie mit Azure Databricks-Geheimnissen Ihre Anmeldeinformationen speichern und dann in Notebooks und Aufträgen darauf verweisen.

- [Erstellen Sie einen Cluster](https://docs.azuredatabricks.net/user-guide/clusters/create.html) in Azure Databricks mit den folgenden Spezifikationen (minimale Einstellungen, die zum Ausführen des Beispielnotebooks erforderlich sind):

   ![Spezifikationen für das Erstellen eines Clusters](media/connect-from-databricks/databricks-create-cluster.png)

## <a name="install-the-python-library-on-your-azure-databricks-cluster"></a>Installieren der Python-Bibliothek in Ihrem Azure Databricks-Cluster

Gehen Sie wie folgt vor, um die [Python-Bibliothek](/azure/kusto/api/python/kusto-python-client-library) in Ihrem Azure Databricks-Cluster zu installieren:

1. Wechseln Sie zu Ihrem Azure Databricks-Arbeitsbereich, und [erstellen Sie eine Bibliothek](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library).
2. [Laden Sie ein Python PyPI-Paket oder Python Egg hoch](https://docs.azuredatabricks.net/user-guide/libraries.html#upload-a-python-pypi-package-or-python-egg).
   - Laden Sie die Bibliothek hoch, installieren Sie sie, und fügen Sie sie in Ihren Databricks-Cluster ein.
   - Geben Sie den PyPi-Namen ein: **azure-kusto-data**.

## <a name="connect-to-azure-data-explorer-by-using-a-device-login"></a>Herstellen einer Verbindung mit Azure Data Explorer per Geräteanmeldung

[Importieren Sie ein Notebook](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook), indem Sie das Notebook [Query-ADX-device-login](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-device-login.ipynb) verwenden. Anschließend können Sie mit Ihren Anmeldeinformationen eine Verbindung mit Azure Data Explorer herstellen.

## <a name="connect-to-adx-by-using-an-azure-ad-app"></a>Herstellen einer Verbindung mit Azure Data Explorer per Azure AD-App

1. Erstellen Sie eine Azure AD-App mit [Bereitstellung einer Azure AD-Anwendung](/azure/kusto/management/access-control/how-to-provision-aad-app).
1. Gewähren Sie in Ihrer Azure Data Explorer-Datenbank wie folgt Zugriff auf Ihre Azure AD-App:

    ```kusto
    .set database <DB Name> users ('aadapp=<AAD App ID>;<AAD Tenant ID>') 'AAD App to connect Spark to ADX
    ```
    |   |   |
    | - | - |
    | ```DB Name``` | Ihr Datenbankname |
    | ```AAD App ID``` | Ihre Azure AD-App-ID |
    | ```AAD Tenant ID``` | Ihre Azure AD-Mandanten-ID |

### <a name="find-your-azure-ad-tenant-id"></a>Ermitteln Ihrer Azure AD-Mandanten-ID

Für die Authentifizierung von Anwendungen verwendet der Azure Data Explorer Ihre Azure AD-Mandanten-ID. Verwenden Sie die folgende URL, um nach Ihrer Mandanten-ID zu suchen. Ersetzen Sie *YourDomain* durch Ihre Domäne.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Wenn Ihre Domäne beispielsweise *contoso.com* ist, lautet die URL: [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Wählen Sie diese URL aus, um die Ergebnisse anzuzeigen. Die erste Zeile lautet wie folgt: 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

Ihre Mandanten-ID ist `6babcaad-604b-40ac-a9d7-9fd97c0b779f`. 

### <a name="store-and-secure-your-azure-ad-app-id-and-key"></a>Speichern und Sichern Ihrer Azure AD-App-ID und des Schlüssels 

Speichern und sichern Sie Ihre Azure AD-App-ID und Ihren Schlüssel wie folgt mit Azure Databricks-[Geheimnissen](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets):
1. [Richten Sie die CLI ein](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-the-cli).
1. [Installieren Sie die CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 
1. [Richten Sie die Authentifizierung ein.](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-authentication)
1. Konfigurieren Sie die [Geheimnisse](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) mit den folgenden Beispielbefehlen:

    ```databricks secrets create-scope --scope adx```

    ```databricks secrets put --scope adx --key myaadappid```

    ```databricks secrets put --scope adx --key myaadappkey```

    ```databricks secrets list --scope adx```

### <a name="import-a-notebook"></a>Importieren eines Notebooks
[Importieren Sie ein Notebook](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) mithilfe des Notebooks [Query-ADX-AAD-App](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-AAD-App.ipynb), um eine Verbindung mit Azure Data Explorer herzustellen. Aktualisieren Sie die Platzhalterwerte mit Ihrem Clusternamen, dem Namen der Datenbank und der Azure AD-Mandanten-ID.
