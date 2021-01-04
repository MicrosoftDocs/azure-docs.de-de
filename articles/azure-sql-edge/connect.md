---
title: Herstellen einer Verbindung mit und Abfragen von Azure SQL Edge
description: Erfahren Sie, wie Sie eine Verbindung mit Azure SQL Edge herstellen und Azure SQL Edge abfragen.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 07/25/2020
ms.openlocfilehash: b56b65261950e9cf534a3755d214229ef7d5bb1e
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93395205"
---
# <a name="connect-and-query-azure-sql-edge"></a>Herstellen einer Verbindung mit und Abfragen von Azure SQL Edge

In Azure SQL Edge können Sie nach der Bereitstellung eines Containers von einem der folgenden Standorte eine Verbindung mit der Datenbank-Engine herstellen:

- Innerhalb des Containers.
- Von einem anderen Docker-Container, der auf demselben Host ausgeführt wird
- Über den Hostcomputer.
- Von einem beliebigen anderen Clientcomputer im Netzwerk

## <a name="tools-to-connect-to-azure-sql-edge"></a>Tools zum Herstellen einer Verbindung mit Azure SQL Edge

Sie können eine Verbindung mit einer Azure SQL Edge-Instanz von einem dieser gängigen Tools aus herstellen:

* [sqlcmd](/sql/linux/sql-server-linux-setup-tools): sqlcmd-Clienttools sind bereits im Azure SQL Edge-Containerimage enthalten. Wenn Sie mithilfe einer interaktiven Bash-Shell mit einem ausgeführten Container verbunden sind, können Sie die Tools lokal ausführen. Auf der ARM64-Plattform sind KEINE SQL-Clienttools verfügbar, da Sie nicht in der ARM64-Version der SQL Edge-Container enthalten sind. 
* [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms)
* [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)
* [Visual Studio Code](/sql/visual-studio-code/sql-server-develop-use-vscode)

Um von einem Netzwerkcomputer aus eine Verbindung mit einer Azure SQL Edge-Datenbank-Engine herzustellen, benötigen Sie Folgendes:

- **IP-Adresse oder Netzwerkname des Hostcomputers** : Dies ist der Hostcomputer, auf dem der Azure SQL Edge-Container ausgeführt wird.
- **Hostportzuordnung für den Azure SQL Edge-Container** : Dies ist die Zuordnung des Docker-Containerports zu einem Port auf dem Host. Innerhalb des Containers wird Azure SQL Edge immer Port 1433 zugeordnet. Sie können dies auf Wunsch ändern. Um die Portnummer zu ändern, aktualisieren Sie die **Optionen für Containererstellung** für das Azure SQL Edge-Modul in Azure IoT Edge. Im folgenden Beispiel wird Port 1433 des Containers dem Port 1600 des Hosts zugeordnet.

    ```JSON
    {
        "PortBindings": {
          "1433/tcp": [
            {
              "HostPort": "1600"
            }
          ]
        }
    }
    ```

- **Systemadministratorkennwort für die Azure SQL Edge-Instanz** : Dieser Wert wurde während der Bereitstellung von Azure SQL Edge für die `SA_PASSWORD`-Umgebungsvariable angegeben.

## <a name="connect-to-the-database-engine-from-within-the-container"></a>Herstellen einer Verbindung mit der Datenbank-Engine aus dem Container

Die [SQL Server-Befehlszeilentools](/sql/linux/sql-server-linux-setup-tools) sind im Azure SQL Edge-Containerimage enthalten. Wenn Sie den Container über eine interaktive Befehlszeile anfügen, können Sie die Tools lokal ausführen. Auf der ARM64-Plattform sind KEINE SQL-Clienttools verfügbar, da Sie nicht in der ARM64-Version der SQL Edge-Container enthalten sind. 

1. Verwenden Sie den Befehl `docker exec -it`, um in Ihrem laufenden Container eine interaktive Bash-Shell zu starten. Im folgenden Beispiel entspricht `e69e056c702d` der Container-ID.

    ```bash
    docker exec -it <Azure SQL Edge container ID or name> /bin/bash
    ```

    > [!TIP]
    > Sie müssen nicht immer die gesamte Container-ID angeben. Sie müssen nur ausreichend Zeichen angeben, um diese eindeutig zu identifizieren. In diesem Beispiel reicht es aus, `e6` oder `e69` anstelle der vollständigen ID zu verwenden.

2. Stellen Sie eine lokale Verbindung aus dem Container heraus mit „sqlcmd“ her. Da „sqlcmd“ nicht standardmäßig im Pfad enthalten ist, müssen Sie selbst den vollständigen Pfand angeben.

    ```bash
    /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P '<YourPassword>'
    ```

3. Wenn Sie mit „sqlcmd“ fertig sind, geben Sie `exit` ein.

4. Wenn Sie mit der interaktiven Eingabeaufforderung fertig sind, geben Sie `exit` ein. Der Container wird auch nach dem Beenden der interaktiven Bash-Shell weiter ausgeführt.

## <a name="connect-to-azure-sql-edge-from-another-container-on-the-same-host"></a>Herstellen einer Verbindung mit Azure SQL Edge aus einem anderen Container auf demselben Host

Da zwei Container, die auf demselben Host ausgeführt werden, sich im selben Docker-Netzwerk befinden, können Sie problemlos über den Containernamen und die Portadresse des Diensts auf sie zugreifen. Wenn Sie z. B. eine Verbindung mit der Azure SQL Edge-Instanz aus einem anderen Python-Modul (Container) auf demselben Host herstellen, können Sie eine Verbindungszeichenfolge ähnlich der folgenden verwenden. (Im folgenden Beispiel wird davon ausgegangen, dass Azure SQL Edge für das Lauschen am Standardport konfiguriert ist.)

```python

import pyodbc
server = 'MySQLEdgeContainer' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

## <a name="connect-to-azure-sql-edge-from-another-network-machine"></a>Herstellen einer Verbindung mit Azure SQL Edge von einem anderen Netzwerkcomputer

Möglicherweise möchten Sie von einem anderen Computer im Netzwerk aus eine Verbindung mit der Azure SQL Edge-Instanz herstellen. Verwenden Sie hierzu die IP-Adresse des Docker-Hosts und den Hostport, dem der Azure SQL Edge-Container zugeordnet ist. Wenn die IP-Adresse des Docker-Hosts beispielsweise *xxx.xxx.xxx.xxx* lautet und der Azure SQL Edge-Container Hostport  *1600* zugeordnet ist, wäre die Serveradresse für die Azure SQL Edge-Instanz *xxx.xxx.xxx.xxx,1600*. Das aktualisierte Python-Skript lautet:

```python

import pyodbc
server = 'xxx.xxx.xxx.xxx,1600' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

Informationen zum Herstellen einer Verbindung mit einer Azure SQL Edge-Instanz mithilfe von SQL Server Management Studio auf einem Windows-Computer finden Sie unter [SQL Server Management Studio](/sql/linux/sql-server-linux-manage-ssms).

Informationen zum Herstellen einer Verbindung mit einer Azure SQL Edge-Instanz mithilfe von Visual Studio Code auf einem Windows-, Mac- oder Linux-Computer finden Sie unter [Visual Studio Code](/sql/visual-studio-code/sql-server-develop-use-vscode).

Informationen zum Herstellen einer Verbindung mit einer Azure SQL Edge-Instanz mithilfe von Azure Data Studio auf einem Windows-, Mac- oder Linux-Computer finden Sie unter [Azure Data Studio](/sql/azure-data-studio/quickstart-sql-server).

## <a name="next-steps"></a>Nächste Schritte

[Herstellen einer Verbindung und Ausführen von Abfragen](/sql/linux/sql-server-linux-configure-docker#connect-and-query)

[Installieren von SQL Server-Tools unter Linux](/sql/linux/sql-server-linux-setup-tools)