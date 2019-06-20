---
title: Abfragen eines Linux-Docker-Containers für SQL Server in einem virtuellen Netzwerk aus einem Azure Databricks-Notebook
description: Dieser Artikel beschreibt, wie Sie Azure Databricks in Ihrem virtuellen Netzwerk bereitstellen, auch bekannt als VNet-Einschleusung.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 345e07fac30f4ad0c8e9918cb8a1ff0fb8aeb811
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60770796"
---
# <a name="tutorial-query-a-sql-server-linux-docker-container-in-a-virtual-network-from-an-azure-databricks-notebook"></a>Tutorial: Abfragen eines Linux-Docker-Containers für SQL Server in einem virtuellen Netzwerk aus einem Azure Databricks-Notebook

Dieses Tutorial zeigt Ihnen, wie Sie Azure Databricks mit einem SQL Server-Linux-Docker-Container in ein virtuelles Netzwerk integrieren können. 

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Bereitstellen eines Azure Databricks-Arbeitsbereichs in einem virtuellen Netzwerk
> * Installieren eines virtuellen Linux-Computers in einem öffentlichen Netzwerk
> * Installieren von Docker
> * Installieren von Microsoft SQL Server in einem Linux-Docker-Container
> * Abfragen von der SQL Server mithilfe von JDBC aus einem Databricks-Notebook

## <a name="prerequisites"></a>Voraussetzungen

* Erstellen Sie einen [Azure Databricks-Arbeitsbereichs in einem virtuellen Netzwerk](quickstart-create-databricks-workspace-vnet-injection.md).

* Installieren Sie [Ubuntu für Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab).

* Laden Sie [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) herunter.

## <a name="create-a-linux-virtual-machine"></a>Erstellen einer virtuellen Linux-Maschine

1. Wählen Sie im Azure-Portal das Symbol für **virtuelle Computer** aus. Wählen Sie dann **+ Hinzufügen** aus.

    ![Hinzufügen eines neuen virtuellen Azure-Computers](./media/vnet-injection-sql-server/add-virtual-machine.png)

2. Wählen Sie auf der Registerkarte **Grundlagen** die Option „Ubuntu Server 16.04 LTS“. Ändern Sie die VM-Größe in B1ms, die eine VCPUS und 2 GB RAM enthält. Die Mindestanforderung für einen Linux SQL Server-Docker-Container beträgt 2 GB. Wählen Sie den Benutzernamen und das Kennwort eines Administrators aus.

    ![Registerkarte „Grundlagen“ der Konfiguration eines neuen virtuellen Computers](./media/vnet-injection-sql-server/create-virtual-machine-basics.png)

3. Navigieren Sie zur Registerkarte **Netzwerk**. Wählen Sie das virtuelle Netzwerk und das öffentliche Subnetz aus, das Ihren Azure Databricks-Cluster enthält. Wählen Sie **Überprüfen + Erstellen** aus und dann **Erstellen**, um den virtuellen Computer bereitzustellen.

    ![Registerkarte „Netzwerk“ der Konfiguration eines neuen virtuellen Computers](./media/vnet-injection-sql-server/create-virtual-machine-networking.png)

4. Wenn die Bereitstellung abgeschlossen ist, navigieren Sie zum virtuellen Computer. Notieren Sie die öffentliche IP-Adresse und das virtuelle Netzwerk/Subnetz unter **Übersicht**. Wählen Sie **Öffentliche IP-Adresse** aus.

    ![Übersicht über virtuelle Computer](./media/vnet-injection-sql-server/virtual-machine-overview.png)

5. Ändern Sie **Zuweisung** in **Statisch**, und geben Sie eine **DNS-Namensbezeichnung** ein. Wählen Sie **Speichern** aus, und starten Sie den virtuellen Computer neu.

    ![Konfiguration der öffentlichen IP-Adresse](./media/vnet-injection-sql-server/virtual-machine-staticip.png)

6. Wählen Sie die Registerkarte **Netzwerk** unter **Einstellungen** aus. Beachten Sie, dass die Netzwerksicherheitsgruppe, die während der Bereitstellung von Azure Databricks erstellt wurde, dem virtuellen Computer zugeordnet ist. Wählen Sie **Regel für eingehenden Port hinzufügen** aus.

7. Fügen Sie eine Regel zum Öffnen von Port 22 für SSH hinzu. Verwenden Sie folgende Einstellungen:
    
    |Einstellung|Empfohlener Wert|BESCHREIBUNG|
    |-------|---------------|-----------|
    |`Source`|IP-Adressen|„IP-Adressen“ legt fest, dass eingehender Datenverkehr von einer bestimmten Quell-IP-Adresse durch diese Regel zugelassen oder verweigert wird.|
    |Quell-IP-Adressen|<ihre öffentliche ip\>|Geben Sie Ihre öffentliche IP-Adresse ein. Sie können Ihre öffentliche IP-Adresse finden, indem Sie [bing.com](https://www.bing.com/) besuchen und nach **Meine IP-Adresse** suchen.|
    |Quellportbereiche|*|Lassen Sie Datenverkehr von einem beliebigen Port zu.|
    |Ziel|IP-Adressen|„IP-Adressen“ legt fest, dass ausgehender Datenverkehr für eine bestimmte Quell-IP-Adresse durch diese Regel zugelassen oder verweigert wird.|
    |Ziel-IP-Adressen|<die öffentliche ip ihrer vm\>|Geben Sie die öffentliche IP-Adresse Ihres virtuellen Computers ein. Sie finden diese auf der Seite **Übersicht** Ihres virtuellen Computers.|
    |Zielportbereiche|22|Öffnen Sie Port 22 für SSH.|
    |Priorität|290|Weisen Sie der Regel eine Priorität zu.|
    |NAME|ssh-databricks-tutorial-vm|Geben Sie der Regel einen Namen.|


    ![Eingangssicherheitsregel für Port 22 hinzufügen](./media/vnet-injection-sql-server/open-port.png)

8. Fügen Sie eine Regel zum Öffnen von Port 1433 für SQL mit den folgenden Einstellungen hinzu:

    |Einstellung|Empfohlener Wert|BESCHREIBUNG|
    |-------|---------------|-----------|
    |`Source`|IP-Adressen|„IP-Adressen“ legt fest, dass eingehender Datenverkehr von einer bestimmten Quell-IP-Adresse durch diese Regel zugelassen oder verweigert wird.|
    |Quell-IP-Adressen|10.179.0.0/16|Geben Sie den Adressbereich für Ihr virtuelles Netzwerk ein.|
    |Quellportbereiche|*|Lassen Sie Datenverkehr von einem beliebigen Port zu.|
    |Ziel|IP-Adressen|„IP-Adressen“ legt fest, dass ausgehender Datenverkehr für eine bestimmte Quell-IP-Adresse durch diese Regel zugelassen oder verweigert wird.|
    |Ziel-IP-Adressen|<die öffentliche ip ihrer vm\>|Geben Sie die öffentliche IP-Adresse Ihres virtuellen Computers ein. Sie finden diese auf der Seite **Übersicht** Ihres virtuellen Computers.|
    |Zielportbereiche|1433|Öffnen Sie Port 22 für SQL Server.|
    |Priorität|300|Weisen Sie der Regel eine Priorität zu.|
    |NAME|sql-databricks-tutorial-vm|Geben Sie der Regel einen Namen.|

    ![Eingangssicherheitsregel für Port 1433 hinzufügen](./media/vnet-injection-sql-server/open-port2.png)

## <a name="run-sql-server-in-a-docker-container"></a>Ausführen von SQL Server in einem Docker-Container

1. Öffnen Sie [Ubuntu für Windows](https://www.microsoft.com/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab), oder ein anderes Tool, das es Ihnen ermöglicht, SSH in den virtuellen Computer zu integrieren. Navigieren Sie zu Ihrem virtuellen Computer im Azure-Portal und wählen Sie **Verbinden**, um den SSH-Befehl zu erhalten, den Sie für die Verbindung benötigen.

    ![Herstellen der Verbindung mit dem virtuellen Computer](./media/vnet-injection-sql-server/vm-ssh-connect.png)

2. Geben Sie den Befehl in Ihrem Ubuntu-Terminal ein und außerdem das Administratorkennwort, das Sie bei der Konfiguration des virtuellen Computers erstellt haben.

    ![SSH-Anmeldung am Ubuntu-Terminal](./media/vnet-injection-sql-server/vm-login-terminal.png)

3. Verwenden Sie den folgenden Befehl, um Docker auf dem virtuellen Computer zu installieren:

    ```bash
    sudo apt-get install docker.io
    ```

    Überprüfen Sie die Installation von Docker mit dem folgenden Befehl:

    ```bash
    sudo docker --version
    ```

4. Installieren Sie das Image.

    ```bash
    sudo docker pull mcr.microsoft.com/mssql/server:2017-latest
    ```

    Überprüfen Sie alle Images.

    ```bash
    sudo docker images
    ```

5. Führen Sie den Container aus dem Image aus.

    ```bash
    sudo docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=Password1234' -p 1433:1433 --name sql1  -d mcr.microsoft.com/mssql/server:2017-latest
    ```

    Stellen Sie sicher, dass der Container ausgeführt wird.

    ```bash
    sudo docker ps -a
    ```

## <a name="create-a-sql-database"></a>Erstellen einer SQL-Datenbank

1. Öffnen Sie SQL Server Management Studio, und verbinden Sie sich mit dem Server über den Servernamen und die SQL-Authentifizierung. Der Benutzername für die Anmeldung lautet **SA** und das Kennwort ist das im Docker-Befehl festgelegte Kennwort. Das Kennwort im Beispielbefehl lautet `Password1234`.

    ![Herstellen einer Verbindung mit der SQL Server-Instanz mit SQL Server Management Studio](./media/vnet-injection-sql-server/ssms-login.png)

2. Nachdem Sie sich erfolgreich verbunden haben, wählen Sie **Neue Abfrage** und geben Sie den folgenden Codeausschnitt ein, um eine Datenbank, eine Tabelle und einige Datensätze in die Tabelle einzufügen.

    ```SQL
    CREATE DATABASE MYDB;
    GO
    USE MYDB;
    CREATE TABLE states(Name VARCHAR(20), Capitol VARCHAR(20));
    INSERT INTO states VALUES ('Delaware','Dover');
    INSERT INTO states VALUES ('South Carolina','Columbia');
    INSERT INTO states VALUES ('Texas','Austin');
    SELECT * FROM states
    GO
    ```

    ![Abfrage zum Erstellen einer SQL Server-Datenbank](./media/vnet-injection-sql-server/create-database.png)

## <a name="query-sql-server-from-azure-databricks"></a>Abfragen von SQL Server aus Azure Databricks

1. Navigieren Sie zu Ihrem Azure Databricks-Arbeitsbereich und überprüfen Sie, ob Sie einen Cluster als Teil der Voraussetzungen erstellt haben. Wählen Sie dann **Ein Notebook erstellen**. Geben Sie dem Notebook einen Namen, wählen Sie *Python* als Sprache, und wählen Sie den von Ihnen erstellten Cluster aus.

    ![Einstellungen des neuen Databricks-Notebooks](./media/vnet-injection-sql-server/create-notebook.png)

2. Verwenden Sie den folgenden Befehl, um die interne IP-Adresse des virtuellen SQL Server-Computers anzupingen. Dieser Ping sollte erfolgreich sein. Wenn nicht, überprüfen Sie, ob der Container ausgeführt wird, und überprüfen Sie die Konfiguration der Netzwerksicherheitsgruppe (NSG).

    ```python
    %sh
    ping 10.179.64.4
    ```

    Für die Überprüfung können Sie auch den Befehlt „nslookup“ verwenden.

    ```python
    %sh
    nslookup databricks-tutorial-vm.westus2.cloudapp.azure.com
    ```

3. Nachdem Sie den SQL Server erfolgreich angepingt haben, können Sie die Datenbank und die Tabellen abfragen. Führen Sie den folgenden Python-Code aus:

    ```python
    jdbcHostname = "10.179.64.4"
    jdbcDatabase = "MYDB"
    userName = 'SA'
    password = 'Password1234'
    jdbcPort = 1433
    jdbcUrl = "jdbc:sqlserver://{0}:{1};database={2};user={3};password={4}".format(jdbcHostname, jdbcPort, jdbcDatabase, userName, password)

    df = spark.read.jdbc(url=jdbcUrl, table='states')
    display(df)
    ```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe, den Azure Databricks-Arbeitsbereich und alle dazugehörigen Ressourcen, wenn Sie sie nicht mehr benötigen. Das Löschen des Auftrags vermeidet unnötige Abrechnung. Wenn Sie planen, den Azure Databricks-Arbeitsbereich in Zukunft zu nutzen, können Sie den Cluster stoppen und später neu starten. Wenn Sie diesen Azure Databricks-Arbeitsbereich nicht mehr verwenden möchten, löschen Sie alle Ressourcen, die im Rahmen dieses Tutorials erstellt wurden, mithilfe der folgenden Schritte:

1. Klicken Sie im Azure-Portal im Menü auf der linken Seite auf **Ressourcengruppen**, und klicken Sie auf den Namen der erstellten Ressourcengruppe.

2. Klicken Sie auf der Seite mit Ihrer Ressourcengruppe auf **Löschen**, geben Sie im Textfeld den Namen der zu löschenden Ressource ein, und klicken Sie dann erneut auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

Im nächsten Artikel erfahren Sie, wie Sie unter Verwendung von Azure Databricks Daten extrahieren, transformieren und laden.
> [!div class="nextstepaction"]
> [Tutorial: Extrahieren, Transformieren und Laden von Daten mithilfe von Azure Databricks](databricks-extract-load-sql-data-warehouse.md)
