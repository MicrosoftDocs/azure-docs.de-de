---
title: 'Schnellstart: Herstellen einer Verbindung mit Azure MySQL mithilfe von GitHub Actions'
description: Verwenden von Azure MySQL über einen GitHub Actions-Workflow
author: juliakm
ms.service: mysql
ms.topic: quickstart
ms.author: jukullam
ms.date: 10/12/2020
ms.custom: github-actions-azure
ms.openlocfilehash: 57e740e6c47d9518c12a49473e103d0abe772618
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93337012"
---
# <a name="quickstart-use-github-actions-to-connect-to-azure-mysql"></a>Schnellstart: Verwenden von GitHub Actions zum Herstellen einer Verbindung mit Azure MySQL

Verwenden Sie als Einstieg in [GitHub Actions](https://docs.github.com/en/actions) einen Workflow zum Bereitstellen von Datenbankupdates für [Azure Database for MySQL](https://azure.microsoft.com/services/mysql/). 

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Folgendes: 
- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Ein GitHub-Repository mit Beispieldaten (`data.sql`). Wenn Sie kein GitHub-Konto besitzen, können Sie sich [kostenlos registrieren](https://github.com/join).  
- Ein Azure Database for MySQL-Server.
    - [Schnellstart: Erstellen eines Azure Database for MySQL-Servers im Azure-Portal](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="workflow-file-overview"></a>Übersicht über die Workflowdatei

Ein GitHub Actions-Workflow wird durch eine YAML-Datei (.yml) im Pfad `/.github/workflows/` in Ihrem Repository definiert. Diese Definition enthält die verschiedenen Schritte und Parameter, die den Workflow bilden.

Die Datei besteht aus zwei Abschnitten:

|`Section`  |Aufgaben  |
|---------|---------|
|**Authentifizierung** | 1. Definieren eines Dienstprinzipals. <br /> 2. Erstellen eines GitHub-Geheimnisses. |
|**Bereitstellen** | 1. Bereitstellen der Datenbank |

## <a name="generate-deployment-credentials"></a>Generieren von Anmeldeinformationen für die Bereitstellung

Sie können mit dem Befehl [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac&preserve-view=true) in der [Azure CLI](/cli/azure/) einen [Dienstprinzipal](../active-directory/develop/app-objects-and-service-principals.md) erstellen. Führen Sie diesen Befehl mit [Azure Cloud Shell](https://shell.azure.com/) im Azure-Portal oder durch Auswählen der Schaltfläche **Ausprobieren** aus.

Ersetzen Sie die Platzhalter `server-name` durch den Namen Ihres in Azure gehosteten MySQL-Servers. Ersetzen Sie `subscription-id` und `resource-group` durch die Abonnement-ID und die Ressourcengruppe, die mit Ihrem MySQL-Server verbunden sind.  

```azurecli-interactive
   az ad sp create-for-rbac --name {server-name} --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
```

Die Ausgabe ist ein JSON-Objekt mit den Anmeldeinformationen für die Rollenzuweisung, die ähnlich wie unten gezeigt Zugriff auf Ihre Datenbank gewähren. Kopieren Sie dieses JSON-Ausgabeobjekt zur späteren Verwendung.

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> Es ist immer empfehlenswert, den minimalen Zugriff zu gewähren. Der Bereich im vorherigen Beispiel ist auf den spezifischen Server beschränkt und umfasst nicht die gesamte Ressourcengruppe.

## <a name="copy-the-mysql-connection-string"></a>Kopieren der MySQL-Verbindungszeichenfolge 

Wechseln Sie im Azure-Portal zum Azure Database for MySQL-Server, und öffnen Sie **Einstellungen** > **Verbindungszeichenfolgen**. Kopieren Sie die **ADO.NET** -Verbindungszeichenfolge. Ersetzen Sie die Platzhalterwerte für `your_database` und `your_password`. Die Verbindungszeichenfolge ähnelt der folgenden Ausgabe: 

```output
   Server=my-mysql-server.mysql.database.azure.com; Port=3306; Database={your_database}; Uid=adminname@my-mysql-server; Pwd={your_password}; SslMode=Preferred;
```
Sie verwenden die Verbindungszeichenfolge als GitHub-Geheimnis. 

## <a name="configure-the-github-secrets"></a>Konfigurieren der GitHub-Geheimnisse

1. Suchen Sie auf [GitHub](https://github.com/) nach Ihrem Repository.

1. Wählen Sie **Settings > Secrets > New secret** (Einstellungen > Geheimnisse > Neues Geheimnis) aus.

1. Fügen Sie die gesamte JSON-Ausgabe aus dem Azure CLI-Befehl in das Wertfeld des Geheimnisses ein. Geben Sie dem Geheimnis den Namen `AZURE_CREDENTIALS`.

    Wenn Sie die Workflowdatei später konfigurieren, verwenden Sie das Geheimnis für die Eingabe `creds` der Azure-Anmeldeaktion. Beispiel:

    ```yaml
    - uses: azure/login@v1
    with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
   ```

1. Wählen Sie erneut **New secret** (Neues Geheimnis) aus. 

1. Fügen Sie den Wert der Verbindungszeichenfolge in das Wertfeld des Geheimnisses ein. Geben Sie dem Geheimnis den Namen `AZURE_MYSQL_CONNECTION_STRING`.


## <a name="add-your-workflow"></a>Hinzufügen des Workflows

1. Navigieren Sie für Ihr GitHub-Repository zu **Actions** (Aktionen). 

2. Wählen Sie **Set up your workflow yourself** (Workflow selbst einrichten) aus. 

2. Löschen Sie alles nach dem Abschnitt `on:` Ihrer Workflowdatei. Der verbleibende Workflow könnte beispielsweise wie folgt aussehen: 

    ```yaml
    name: CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]
    ```

1. Benennen Sie den Workflow in `MySQL for GitHub Actions` um, und fügen Sie die Aktionen zum Auschecken und Anmelden hinzu. Diese Aktionen checken Ihren Websitecode aus und authentifizieren Sie mithilfe des zuvor erstellten GitHub-Geheimnisses `AZURE_CREDENTIALS` mit Azure. 

    ```yaml
    name: MySQL for GitHub Actions

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]

    jobs:
    build:
        runs-on: windows-latest
        steps:
        - uses: actions/checkout@v1
        - uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

1. Verwenden Sie die Aktion für die Azure MySQL-Bereitstellung, um eine Verbindung mit Ihrer MySQL-Instanz herzustellen. Ersetzen Sie `MYSQL_SERVER_NAME` durch den Namen Ihres Servers. Sie sollten über eine MySQL-Datendatei namens `data.sql` auf der Stammebene Ihres Repositorys verfügen. 

    ```yaml
    - uses: azure/mysql@v1
      with:
        server-name: MYSQL_SERVER_NAME
        connection-string: ${{ secrets.AZURE_MYSQL_CONNECTION_STRING }}
        sql-file: './data.sql'
    ``` 

1. Vervollständigen Sie Ihren Workflow, indem Sie eine Aktion zum Abmelden von Azure hinzufügen. Hier sehen Sie den fertigen Workflow: Die Datei wird im Ordner `.github/workflows` Ihres Repositorys angezeigt.

    ```yaml
   name: MySQL for GitHub Actions

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]


    jobs:
    build:
        runs-on: windows-latest
        steps:
        - uses: actions/checkout@v1
        - uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}

    - uses: azure/mysql@v1
      with:
        server-name: MYSQL_SERVER_NAME
        connection-string: ${{ secrets.AZURE_MYSQL_CONNECTION_STRING }}
        sql-file: './data.sql'

        # Azure logout 
    - name: logout
      run: |
         az logout
    ```

## <a name="review-your-deployment"></a>Überprüfen der Bereitstellung

1. Navigieren Sie für Ihr GitHub-Repository zu **Actions** (Aktionen). 

1. Öffnen Sie das erste Ergebnis, um ausführliche Protokolle zur Ausführung des Workflows anzuzeigen. 
 
    :::image type="content" source="media/quickstart-mysql-github-actions/github-actions-run-mysql.png" alt-text="Protokoll zur Ausführung der GitHub-Aktionen":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Ihre Azure MySQL-Datenbank und das Repository nicht mehr benötigt werden, bereinigen Sie die bereitgestellten Ressourcen, indem Sie die Ressourcengruppe und Ihr GitHub-Repository löschen. 

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Weitere Informationen zu Azure und zur GitHub-Integration](/azure/developer/github/)