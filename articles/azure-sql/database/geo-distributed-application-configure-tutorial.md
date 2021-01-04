---
title: Implementieren einer geografisch verteilten Lösung
description: Es wird beschrieben, wie Sie Ihre Datenbank in Azure SQL-Datenbank und die Clientanwendung für ein Failover zu einer replizierten Datenbank konfigurieren und ein Testfailover durchführen.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1, devx-track-azurecli, devx-track-azurepowershell
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 03/12/2019
ms.openlocfilehash: 98e3eb4927b8eb9e52fd974c1ef7c417aff2ad54
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422789"
---
# <a name="tutorial-implement-a-geo-distributed-database-azure-sql-database"></a>Tutorial: Implementieren einer geografisch verteilten Datenbank (Azure SQL-Datenbank)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Konfigurieren Sie eine Datenbank in Azure SQL-Datenbank und eine Clientanwendung für das Failover zu einer Remoteregion, und testen Sie dann Ihren Failoverplan. Folgendes wird vermittelt:

> [!div class="checklist"]
>
> - Erstellen einer [Failovergruppe](auto-failover-group-overview.md)
> - Ausführen einer Java-Anwendung zum Abfragen einer Datenbank in SQL-Datenbank
> - Testfailover

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Das PowerShell Azure Resource Manager-Modul wird von Azure SQL-Datenbank weiterhin unterstützt, aber alle zukünftigen Entwicklungen erfolgen für das Az.Sql-Modul. Informationen zu diesen Cmdlets finden Sie unter [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Die Argumente für die Befehle im Az-Modul und den AzureRm-Modulen sind im Wesentlichen identisch.

Für dieses Tutorial muss Folgendes installiert sein:

- [Azure PowerShell](/powershell/azure/)
- Eine Einzeldatenbank in einer Azure SQL-Datenbank. Verwenden Sie für die Erstellung Folgendes:
  - [Das Azure-Portal](single-database-create-quickstart.md)
  - [Die Azure-CLI](az-cli-script-samples-content-guide.md)
  - [PowerShell](powershell-script-content-guide.md)

  > [!NOTE]
  > In diesem Tutorial wird die Beispieldatenbank *AdventureWorksLT* verwendet.

- Java und Maven finden Sie unter [Erstellen einer App mit SQL Server](https://www.microsoft.com/sql-server/developer-get-started/). Markieren Sie **Java** , wählen Sie Ihre Umgebung aus, und führen Sie dann die Schritte aus.

> [!IMPORTANT]
> Richten Sie auf dem Computer, auf dem Sie die Schritte des Tutorials ausführen, Firewallregeln für die öffentliche IP-Adresse des Computers ein. Firewallregeln auf Datenbankebene werden automatisch auf den sekundären Server repliziert.
>
> Weitere Informationen finden Sie unter [Erstellen einer Firewallregel auf Datenbankebene](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database). Eine Beschreibung, wie Sie die IP-Adresse für die Firewallregel auf Serverebene für Ihren Computer ermitteln, finden Sie unter [Erstellen einer Firewall auf Serverebene](firewall-create-server-level-portal-quickstart.md).  

## <a name="create-a-failover-group"></a>Erstellen einer Failovergruppe

Erstellen Sie mit Azure PowerShell [Failovergruppen](auto-failover-group-overview.md) zwischen einem vorhandenen Server und einem neuen Server in einer anderen Region. Fügen Sie dann der Failovergruppe die Beispieldatenbank hinzu.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> [!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

Führen Sie das folgende Skript aus, um eine Failovergruppe zu erstellen:

```powershell
$admin = "<adminName>"
$password = "<password>"
$resourceGroup = "<resourceGroupName>"
$location = "<resourceGroupLocation>"
$server = "<serverName>"
$database = "<databaseName>"
$drLocation = "<disasterRecoveryLocation>"
$drServer = "<disasterRecoveryServerName>"
$failoverGroup = "<globallyUniqueFailoverGroupName>"

# create a backup server in the failover region
New-AzSqlServer -ResourceGroupName $resourceGroup -ServerName $drServer `
    -Location $drLocation -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
    -ArgumentList $admin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

# create a failover group between the servers
New-AzSqlDatabaseFailoverGroup –ResourceGroupName $resourceGroup -ServerName $server `
    -PartnerServerName $drServer –FailoverGroupName $failoverGroup –FailoverPolicy Automatic -GracePeriodWithDataLossHours 2

# add the database to the failover group
Get-AzSqlDatabase -ResourceGroupName $resourceGroup -ServerName $server -DatabaseName $database | `
    Add-AzSqlDatabaseToFailoverGroup -ResourceGroupName $resourceGroup -ServerName $server -FailoverGroupName $failoverGroup
```

# <a name="the-azure-cli"></a>[Die Azure-CLI](#tab/azure-cli)

> [!IMPORTANT]
> Führen Sie `az login` aus, um sich bei Azure anzumelden.

```azurecli
$admin = "<adminName>"
$password = "<password>"
$resourceGroup = "<resourceGroupName>"
$location = "<resourceGroupLocation>"
$server = "<serverName>"
$database = "<databaseName>"
$drLocation = "<disasterRecoveryLocation>" # must be different then $location
$drServer = "<disasterRecoveryServerName>"
$failoverGroup = "<globallyUniqueFailoverGroupName>"

# create a backup server in the failover region
az sql server create --admin-password $password --admin-user $admin `
    --name $drServer --resource-group $resourceGroup --location $drLocation

# create a failover group between the servers
az sql failover-group create --name $failoverGroup --partner-server $drServer `
    --resource-group $resourceGroup --server $server --add-db $database `
    --failover-policy Automatic --grace-period 2
```

* * *

Einstellungen für die Georeplikation können auch im Azure-Portal geändert werden. Wählen Sie dazu Ihre Datenbank und dann **Einstellungen** > **Georeplikation** aus.

![Einstellungen für die Georeplikation](./media/geo-distributed-application-configure-tutorial/geo-replication.png)

## <a name="run-the-sample-project"></a>Ausführen des Beispielprojekts

1. Erstellen Sie in der Konsole mit dem folgenden Befehl ein Maven-Projekt:

   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```

1. Geben Sie **Y** ein, und drücken Sie die **EINGABETASTE**.

1. Wechseln Sie zum Verzeichnis des neuen Projekts.

   ```bash
   cd SqlDbSample
   ```

1. Öffnen Sie die Datei *pom.xml* im Projektordner in Ihrem bevorzugten Editor.

1. Fügen Sie SQL Server den Microsoft JDBC-Treiber als Abhängigkeit hinzu. Verwenden Sie dazu den folgenden Abschnitt `dependency`. Die Abhängigkeit muss innerhalb des größeren Abschnitts `dependencies` eingefügt werden.

   ```xml
   <dependency>
     <groupId>com.microsoft.sqlserver</groupId>
     <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
   </dependency>
   ```

1. Geben Sie die Java-Version an, indem Sie den Abschnitt `properties` hinter dem Abschnitt `dependencies` hinzufügen:

   ```xml
   <properties>
     <maven.compiler.source>1.8</maven.compiler.source>
     <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. Fügen Sie Unterstützung von Manifestdateien hinzu, indem Sie den Abschnitt `build` hinter dem Abschnitt `properties` hinzufügen:

   ```xml
   <build>
     <plugins>
       <plugin>
         <groupId>org.apache.maven.plugins</groupId>
         <artifactId>maven-jar-plugin</artifactId>
         <version>3.0.0</version>
         <configuration>
           <archive>
             <manifest>
               <mainClass>com.sqldbsamples.App</mainClass>
             </manifest>
           </archive>
        </configuration>
       </plugin>
     </plugins>
   </build>
   ```

1. Speichern und schließen Sie die Datei *pom.xml*.

1. Öffnen Sie die Datei *App.java* in „..\SqlDbSample\src\main\java\com\sqldbsamples“, und ersetzen Sie ihren Inhalt durch folgenden Code:

   ```java
   package com.sqldbsamples;

   import java.sql.Connection;
   import java.sql.Statement;
   import java.sql.PreparedStatement;
   import java.sql.ResultSet;
   import java.sql.Timestamp;
   import java.sql.DriverManager;
   import java.util.Date;
   import java.util.concurrent.TimeUnit;

   public class App {

      private static final String FAILOVER_GROUP_NAME = "<your failover group name>";  // add failover group name
  
      private static final String DB_NAME = "<your database>";  // add database name
      private static final String USER = "<your admin>";  // add database user
      private static final String PASSWORD = "<your password>";  // add database password

      private static final String READ_WRITE_URL = String.format("jdbc:" +
         "sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;" +
         "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", +
         FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);
      private static final String READ_ONLY_URL = String.format("jdbc:" +
         "sqlserver://%s.secondary.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;" +
         "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", +
         FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);

      public static void main(String[] args) {
         System.out.println("#######################################");
         System.out.println("## GEO DISTRIBUTED DATABASE TUTORIAL ##");
         System.out.println("#######################################");
         System.out.println("");

         int highWaterMark = getHighWaterMarkId();

         try {
            for(int i = 1; i < 1000; i++) {
                //  loop will run for about 1 hour
                System.out.print(i + ": insert on primary " +
                   (insertData((highWaterMark + i))?"successful":"failed"));
                TimeUnit.SECONDS.sleep(1);
                System.out.print(", read from secondary " +
                   (selectData((highWaterMark + i))?"successful":"failed") + "\n");
                TimeUnit.SECONDS.sleep(3);
            }
         } catch(Exception e) {
            e.printStackTrace();
      }
   }

   private static boolean insertData(int id) {
      // Insert data into the product table with a unique product name so we can find the product again
      String sql = "INSERT INTO SalesLT.Product " +
         "(Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL);
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         pstmt.setInt(2, 200989 + id + 10000);
         pstmt.setString(3, "Blue");
         pstmt.setDouble(4, 75.00);
         pstmt.setDouble(5, 89.99);
         pstmt.setTimestamp(6, new Timestamp(new Date().getTime()));
         return (1 == pstmt.executeUpdate());
      } catch (Exception e) {
         return false;
      }
   }

   private static boolean selectData(int id) {
      // Query the data previously inserted into the primary database from the geo replicated database
      String sql = "SELECT Name, Color, ListPrice FROM SalesLT.Product WHERE Name = ?";

      try (Connection connection = DriverManager.getConnection(READ_ONLY_URL);
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         try (ResultSet resultSet = pstmt.executeQuery()) {
            return resultSet.next();
         }
      } catch (Exception e) {
         return false;
      }
   }

   private static int getHighWaterMarkId() {
      // Query the high water mark id stored in the table to be able to make unique inserts
      String sql = "SELECT MAX(ProductId) FROM SalesLT.Product";
      int result = 1;
      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL);
              Statement stmt = connection.createStatement();
              ResultSet resultSet = stmt.executeQuery(sql)) {
         if (resultSet.next()) {
             result =  resultSet.getInt(1);
            }
         } catch (Exception e) {
          e.printStackTrace();
         }
         return result;
      }
   }
   ```

1. Speichern und schließen Sie die Datei *App.java*.

1. Führen Sie den folgenden Befehl an der Befehlskonsole aus:

   ```bash
   mvn package
   ```

1. Starten Sie die Anwendung. Sie wird ca. 1 Stunde lang ausgeführt, bis sie manuell beendet wird. In dieser Zeit können Sie den Failovertest ausführen.

   ```bash
   mvn -q -e exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   ```

   ```output
   #######################################
   ## GEO DISTRIBUTED DATABASE TUTORIAL ##
   #######################################

   1. insert on primary successful, read from secondary successful
   2. insert on primary successful, read from secondary successful
   3. insert on primary successful, read from secondary successful
   ...
   ```

## <a name="test-failover"></a>Testfailover

Führen Sie die folgenden Skripts aus, um ein Failover zu simulieren und die Anwendungsergebnisse zu beobachten. Beachten Sie, wie einige Einfüge- und Auswahlvorgänge während der Datenbankmigration zu Fehlern führen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Sie können während des Tests mit dem folgenden Befehl die Rolle des Notfallwiederherstellungsservers überprüfen:

```powershell
(Get-AzSqlDatabaseFailoverGroup -FailoverGroupName $failoverGroup `
    -ResourceGroupName $resourceGroup -ServerName $drServer).ReplicationRole
```

So testen Sie ein Failover

1. Starten Sie das manuelle Failover der Failovergruppe:

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup -ResourceGroupName $resourceGroup `
    -ServerName $drServer -FailoverGroupName $failoverGroup
   ```

1. Stellen Sie die Failovergruppe auf dem primären Server wieder her:

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup -ResourceGroupName $resourceGroup `
    -ServerName $server -FailoverGroupName $failoverGroup
   ```

# <a name="the-azure-cli"></a>[Die Azure-CLI](#tab/azure-cli)

Sie können während des Tests mit dem folgenden Befehl die Rolle des Notfallwiederherstellungsservers überprüfen:

```azurecli
az sql failover-group show --name $failoverGroup --resource-group $resourceGroup --server $drServer
```

So testen Sie ein Failover

1. Starten Sie das manuelle Failover der Failovergruppe:

   ```azurecli
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $drServer
   ```

1. Stellen Sie die Failovergruppe auf dem primären Server wieder her:

   ```azurecli
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $server
   ```

* * *

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie eine Azure SQL-Datenbank-Instanz und eine Anwendung für das Failover zu einer Remoteregion konfiguriert und einen Failoverplan getestet. Sie haben Folgendes gelernt:

> [!div class="checklist"]
>
> - Erstellen einer Georeplikations-Failovergruppe
> - Ausführen einer Java-Anwendung zum Abfragen einer Datenbank in SQL-Datenbank
> - Testfailover

Fahren Sie mit dem nächsten Tutorial fort, in dem beschrieben wird, wie Sie einer Failovergruppe eine Instanz von Azure SQL Managed Instance hinzufügen:

> [!div class="nextstepaction"]
> [Tutorial: Hinzufügen einer Instanz von SQL Managed Instance zu einer Failovergruppe](../managed-instance/failover-group-add-instance-tutorial.md)
