---
title: Verwenden von Apache Maven zum Erstellen eines Java-HBase-Clients für Azure HDInsight
description: Erfahren Sie, wie Sie mit Apache Maven eine Java-basierte Apache HBase-Anwendung erstellen und anschließend in HBase unter Azure HDInsight bereitstellen können.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seodec18, devx-track-java
ms.date: 12/24/2019
ms.openlocfilehash: 61b8aaf9ea61ebe85eac6708d7390c386dea2696
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547910"
---
# <a name="build-java-applications-for-apache-hbase"></a>Erstellen von Java-Anwendungen für die Apache-HBase

Erfahren Sie, wie Sie eine [Apache HBase](https://hbase.apache.org/)-Anwendung in Java erstellen. Setzen Sie die Anwendung anschließend mit HBase unter Azure HDInsight ein.

Die Schritte in diesem Dokument verwenden [Apache Maven](https://maven.apache.org/) zum Erstellen des Projekts. Maven ist ein Projektmanagement- und Verständnistool, mit dem Sie Software, Dokumentationen und Berichte für Java-Projekte erstellen können.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Apache HBase-Cluster in HDInsight. Weitere Informationen finden Sie unter [Erste Schritte mit Apache HBase](./apache-hbase-tutorial-get-started-linux.md).

* [Java Developer Kit (JDK), Version 8](/azure/developer/java/fundamentals/java-jdk-long-term-support)

* Ordnungsgemäße [Installation](https://maven.apache.org/install.html) von [Apache Maven](https://maven.apache.org/download.cgi) (gemäß Apache).  Maven ist ein Projekterstellungssystem für Java-Projekte.

* Einen SSH-Client. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit HDInsight (Hadoop) per SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Bei Verwendung von PowerShell benötigen Sie das [Az-Modul](/powershell/azure/).

* Ein Texteditor In diesem Artikel wird der Microsoft-Editor verwendet.

## <a name="test-environment"></a>Testumgebung

Für diesen Artikel wurde ein Computer unter Windows 10 verwendet.  Die Befehle wurden an einer Eingabeaufforderung ausgeführt, und die verschiedenen Dateien wurden mit dem Windows-Editor bearbeitet. Nehmen Sie die Ihrer Umgebung entsprechenden Änderungen vor.

Geben Sie an einer Eingabeaufforderung die folgenden Befehle ein, um eine Arbeitsumgebung zu erstellen:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Erstellen eines Maven-Projekts

1. Geben Sie den folgenden Befehl ein, um ein Maven-Projekt namens **hbaseapp** zu erstellen:

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    cd hbaseapp
    mkdir conf
    ```

    Dieser Befehl erstellt am aktuellen Speicherort ein neues Verzeichnis namens `hbaseapp`, das ein einfaches Maven-Projekt enthält. Der zweite Befehl ändert das Arbeitsverzeichnis in `hbaseapp`. Der dritte Befehl erstellt ein neues Verzeichnis (`conf`) zur späteren Verwendung. Das `hbaseapp`-Verzeichnis enthält die folgenden Elemente:

    * `pom.xml`:  Das Projektobjektmodell ([POM](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html)) enthält Informationen und Konfigurationsdetails zum Erstellen des Projekts.
    * `src\main\java\com\microsoft\examples`: Enthält Ihren Anwendungscode.
    * `src\test\java\com\microsoft\examples`: Enthält Tests für Ihre Anwendung.

2. Entfernen Sie den generierten Beispielcode. Geben Sie die folgenden Befehle ein, um die generierten Test- und Anwendungsdateien (`AppTest.java` und `App.java`) zu löschen:

    ```cmd
    DEL src\main\java\com\microsoft\examples\App.java
    DEL src\test\java\com\microsoft\examples\AppTest.java
    ```

## <a name="update-the-project-object-model"></a>Aktualisieren des Projektobjektmodells

Eine vollständige Referenz der Datei „pom.xml“ finden Sie unter https://maven.apache.org/pom.html.  Geben Sie den folgenden Befehl ein, um `pom.xml` zu öffnen:

```cmd
notepad pom.xml
```

### <a name="add-dependencies"></a>Hinzufügen von Abhängigkeiten

Fügen Sie in `pom.xml` im Abschnitt `<dependencies>` den folgenden Text hinzu:

```xml
<dependency>
    <groupId>org.apache.hbase</groupId>
    <artifactId>hbase-shaded-client</artifactId>
    <version>1.1.2</version>
</dependency>
<dependency>
    <groupId>org.apache.phoenix</groupId>
    <artifactId>phoenix-core</artifactId>
    <version>4.14.1-HBase-1.1</version>
</dependency>
```  

In diesem Abschnitt ist angegeben, dass für das Projekt die Komponenten **hbase-client** und **phoenix-core** erforderlich sind. Beim Kompilieren werden diese Abhängigkeiten aus dem Maven-Standardrepository heruntergeladen. Sie können die [Suche im zentralen Maven-Repository](https://search.maven.org/artifact/org.apache.hbase/hbase-client/1.1.2/jar) verwenden, wenn Sie weitere Informationen zu dieser Abhängigkeit erhalten möchten.

> [!IMPORTANT]  
> Die Versionsnummer der hbase-client-Komponente muss mit der Version von Apache HBase übereinstimmen, die mit Ihrem HDInsight-Cluster bereitgestellt wird. Suchen Sie die richtige Versionsnummer mithilfe der folgenden Tabelle.

| HDInsight-Clusterversion | Zu verwendende Apache HBase-Version |
| --- | --- |
| 3.6 | 1.1.2 |
| 4,0 | 2.0.0 |

Weitere Informationen zu HDInsight-Versionen und Komponenten finden Sie unter [Was sind die verschiedenen Apache Hadoop-Komponenten, die in HDInsight verfügbar sind?](../hdinsight-component-versioning.md).

### <a name="build-configuration"></a>Buildkonfiguration

Mithilfe von Maven-Plug-Ins können Sie die Buildphasen des Projekts anpassen. Dieser Abschnitt wird zum Hinzufügen von Plug-Ins, Ressourcen und anderen Optionen für die Buildkonfiguration verwendet.

Fügen Sie der Datei `pom.xml` den folgenden Code hinzu, und speichern und schließen Sie die Datei. Dieser Text muss sich in der Datei innerhalb der `<project>...</project>`-Tags befinden, z.B. zwischen `</dependencies>` und `</project>`.

```xml
<build>
    <sourceDirectory>src</sourceDirectory>
    <resources>
    <resource>
        <directory>${basedir}/conf</directory>
        <filtering>false</filtering>
        <includes>
        <include>hbase-site.xml</include>
        </includes>
    </resource>
    </resources>
    <plugins>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.1</version>
        <configuration>
            <source>1.8</source>
            <target>1.8</target>
        </configuration>
        </plugin>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>3.2.1</version>
        <configuration>
        <transformers>
            <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
            </transformer>
        </transformers>
        </configuration>
        <executions>
        <execution>
            <phase>package</phase>
            <goals>
            <goal>shade</goal>
            </goals>
        </execution>
        </executions>
    </plugin>
    </plugins>
</build>
```

Mit diesem Abschnitt wird eine Ressource (`conf/hbase-site.xml`) konfiguriert, die die Konfigurationsinformationen für HBase enthält.

> [!NOTE]  
> Sie können die Konfigurationswerte auch per Code festlegen. Siehe dazu die Kommentare im Beispiel `CreateTable`.

In diesem Abschnitt werden auch das [Apache Maven Compiler-Plug-In](https://maven.apache.org/plugins/maven-compiler-plugin/) und das [Apache Maven Shade-Plug-In](https://maven.apache.org/plugins/maven-shade-plugin/) konfiguriert. Das Compiler-Plug-In wird zum Kompilieren der Topologie verwendet. Das Shade-Plug-In wird verwendet, um die Lizenzduplizierung in dem von Maven erstellten JAR-Paket zu verhindern. Dieses Plug-In wird verwendet, um einen Fehler des Typs „Doppelte Lizenzdateien“ zur Laufzeit im HDInsight-Cluster zu vermeiden. Wenn das Maven-Shade-Plug-In mit der `ApacheLicenseResourceTransformer`-Implementierung verwendet wird, wird der Fehler vermieden.

Das maven-shade-plugin generiert außerdem ein Uberjar, das alle Abhängigkeiten enthält, die in der Anwendung benötigt werden.

### <a name="download-the-hbase-sitexml"></a>Herunterladen von „hbase-site.xml“

Verwenden Sie den folgenden Befehl, um die HBase-Konfiguration aus dem HBase-Cluster an den Spoeicherort `conf` zu kopieren. Ersetzen Sie `CLUSTERNAME` durch Ihren HDInsight-Clusternamen, und geben Sie dann den folgenden Befehl ein:

```cmd
scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml
```

## <a name="create-the-application"></a>Erstellen der Anwendung

### <a name="implement-a-createtable-class"></a>Implementieren einer CreateTable-Klasse

Geben Sie den folgenden Befehl ein, um die neue Datei `CreateTable.java` zu erstellen und zu öffnen. Wählen an der Eingabeaufforderung **Ja** aus, um eine neue Datei zu erstellen.

```cmd
notepad src\main\java\com\microsoft\examples\CreateTable.java
```

Kopieren Sie den folgenden Java-Code, und fügen Sie ihn in die neue Datei ein. Schließen Sie dann die Datei.

```java
package com.microsoft.examples;
import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.client.HBaseAdmin;
import org.apache.hadoop.hbase.HTableDescriptor;
import org.apache.hadoop.hbase.TableName;
import org.apache.hadoop.hbase.HColumnDescriptor;
import org.apache.hadoop.hbase.client.HTable;
import org.apache.hadoop.hbase.client.Put;
import org.apache.hadoop.hbase.util.Bytes;

public class CreateTable {
    public static void main(String[] args) throws IOException {
    Configuration config = HBaseConfiguration.create();

    // Example of setting zookeeper values for HDInsight
    // in code instead of an hbase-site.xml file
    //
    // config.set("hbase.zookeeper.quorum",
    //            "zookeepernode0,zookeepernode1,zookeepernode2");
    //config.set("hbase.zookeeper.property.clientPort", "2181");
    //config.set("hbase.cluster.distributed", "true");
    //
    //NOTE: Actual zookeeper host names can be found using Ambari:
    //curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts"

    //Linux-based HDInsight clusters use /hbase-unsecure as the znode parent
    config.set("zookeeper.znode.parent","/hbase-unsecure");

    // create an admin object using the config
    HBaseAdmin admin = new HBaseAdmin(config);

    // create the table...
    HTableDescriptor tableDescriptor = new HTableDescriptor(TableName.valueOf("people"));
    // ... with two column families
    tableDescriptor.addFamily(new HColumnDescriptor("name"));
    tableDescriptor.addFamily(new HColumnDescriptor("contactinfo"));
    admin.createTable(tableDescriptor);

    // define some people
    String[][] people = {
        { "1", "Marcel", "Haddad", "marcel@fabrikam.com"},
        { "2", "Franklin", "Holtz", "franklin@contoso.com" },
        { "3", "Dwayne", "McKee", "dwayne@fabrikam.com" },
        { "4", "Rae", "Schroeder", "rae@contoso.com" },
        { "5", "Rosalie", "burton", "rosalie@fabrikam.com"},
        { "6", "Gabriela", "Ingram", "gabriela@contoso.com"} };

    HTable table = new HTable(config, "people");

    // Add each person to the table
    //   Use the `name` column family for the name
    //   Use the `contactinfo` column family for the email
    for (int i = 0; i< people.length; i++) {
        Put person = new Put(Bytes.toBytes(people[i][0]));
        person.add(Bytes.toBytes("name"), Bytes.toBytes("first"), Bytes.toBytes(people[i][1]));
        person.add(Bytes.toBytes("name"), Bytes.toBytes("last"), Bytes.toBytes(people[i][2]));
        person.add(Bytes.toBytes("contactinfo"), Bytes.toBytes("email"), Bytes.toBytes(people[i][3]));
        table.put(person);
    }
    // flush commits and close the table
    table.flushCommits();
    table.close();
    }
}
```

Dieser Code ist die `CreateTable`-Klasse, die eine Tabelle namens `people` erzeugt und sie mit einigen vordefinierten Benutzern füllt.

### <a name="implement-a-searchbyemail-class"></a>Implementieren einer SearchByEmail-Klasse

Geben Sie den folgenden Befehl ein, um die neue Datei `SearchByEmail.java` zu erstellen und zu öffnen. Wählen an der Eingabeaufforderung **Ja** aus, um eine neue Datei zu erstellen.

```cmd
notepad src\main\java\com\microsoft\examples\SearchByEmail.java
```

Kopieren Sie den folgenden Java-Code, und fügen Sie ihn in die neue Datei ein. Schließen Sie dann die Datei.

```java
package com.microsoft.examples;
import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.client.HTable;
import org.apache.hadoop.hbase.client.Scan;
import org.apache.hadoop.hbase.client.ResultScanner;
import org.apache.hadoop.hbase.client.Result;
import org.apache.hadoop.hbase.filter.RegexStringComparator;
import org.apache.hadoop.hbase.filter.SingleColumnValueFilter;
import org.apache.hadoop.hbase.filter.CompareFilter.CompareOp;
import org.apache.hadoop.hbase.util.Bytes;
import org.apache.hadoop.util.GenericOptionsParser;

public class SearchByEmail {
    public static void main(String[] args) throws IOException {
    Configuration config = HBaseConfiguration.create();

    // Use GenericOptionsParser to get only the parameters to the class
    // and not all the parameters passed (when using WebHCat for example)
    String[] otherArgs = new GenericOptionsParser(config, args).getRemainingArgs();
    if (otherArgs.length != 1) {
        System.out.println("usage: [regular expression]");
        System.exit(-1);
    }

    // Open the table
    HTable table = new HTable(config, "people");

    // Define the family and qualifiers to be used
    byte[] contactFamily = Bytes.toBytes("contactinfo");
    byte[] emailQualifier = Bytes.toBytes("email");
    byte[] nameFamily = Bytes.toBytes("name");
    byte[] firstNameQualifier = Bytes.toBytes("first");
    byte[] lastNameQualifier = Bytes.toBytes("last");

    // Create a regex filter
    RegexStringComparator emailFilter = new RegexStringComparator(otherArgs[0]);
    // Attach the regex filter to a filter
    //   for the email column
    SingleColumnValueFilter filter = new SingleColumnValueFilter(
        contactFamily,
        emailQualifier,
        CompareOp.EQUAL,
        emailFilter
    );

    // Create a scan and set the filter
    Scan scan = new Scan();
    scan.setFilter(filter);

    // Get the results
    ResultScanner results = table.getScanner(scan);
    // Iterate over results and print  values
    for (Result result : results ) {
        String id = new String(result.getRow());
        byte[] firstNameObj = result.getValue(nameFamily, firstNameQualifier);
        String firstName = new String(firstNameObj);
        byte[] lastNameObj = result.getValue(nameFamily, lastNameQualifier);
        String lastName = new String(lastNameObj);
        System.out.println(firstName + " " + lastName + " - ID: " + id);
        byte[] emailObj = result.getValue(contactFamily, emailQualifier);
        String email = new String(emailObj);
        System.out.println(firstName + " " + lastName + " - " + email + " - ID: " + id);
    }
    results.close();
    table.close();
    }
}
```

Die `SearchByEmail`-Klasse kann für Abfragen von Zeilen nach der E-Mail-Adresse verwendet werden. Da sie einen Filter für reguläre Ausdrücke verwendet, können Sie beim Einsatz der Klasse entweder eine Zeichenfolge angeben oder einen regulären Ausdruck.

### <a name="implement-a-deletetable-class"></a>Implementieren einer DeleteTable-Klasse

Geben Sie den folgenden Befehl ein, um die neue Datei `DeleteTable.java` zu erstellen und zu öffnen. Wählen an der Eingabeaufforderung **Ja** aus, um eine neue Datei zu erstellen.

```cmd
notepad src\main\java\com\microsoft\examples\DeleteTable.java
```

Kopieren Sie den folgenden Java-Code, und fügen Sie ihn in die neue Datei ein. Schließen Sie dann die Datei.

```java
package com.microsoft.examples;
import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.client.HBaseAdmin;

public class DeleteTable {
    public static void main(String[] args) throws IOException {
    Configuration config = HBaseConfiguration.create();

    // Create an admin object using the config
    HBaseAdmin admin = new HBaseAdmin(config);

    // Disable, and then delete the table
    admin.disableTable("people");
    admin.deleteTable("people");
    }
}
```

Mit der `DeleteTable`-Klasse werden die in diesem Beispiel erstellten HBase-Tabellen bereinigt. Dabei wird die mit der `CreateTable`-Klasse erstellte Tabelle deaktiviert und gelöscht.

## <a name="build-and-package-the-application"></a>Erstellen und Packen der Anwendung

1. Verwenden Sie im Verzeichnis `hbaseapp` den folgenden Befehl, um eine JAR-Datei zu erstellen, die die Anwendung enthält:

    ```cmd
    mvn clean package
    ```

    Dieser Befehl erstellt und verpackt die Anwendung in eine JAR-Datei.

2. Sobald der Befehl abgeschlossen ist, enthält das Verzeichnis `hbaseapp/target` eine Datei mit dem Namen `hbaseapp-1.0-SNAPSHOT.jar`.

   > [!NOTE]  
   > Die Datei `hbaseapp-1.0-SNAPSHOT.jar` ist eine Uber-JAR-Datei. Sie enthält alle Abhängigkeiten, die zum Ausführen der Anwendung erforderlich sind.

## <a name="upload-the-jar-and-run-jobs-ssh"></a>Hochladen der JAR-Datei und Ausführen von Aufträgen (SSH)

In den folgenden Schritten wird `scp` zum Kopieren der JAR-Datei auf den primären Hauptknoten von Apache HBase im HDInsight-Cluster verwendet. Der Befehl `ssh` dient dann zum Herstellen einer Verbindung mit dem Cluster und direkten Ausführen des Beispiels auf dem Hauptknoten.

1. Laden Sie die JAR-Datei in den Cluster hoch. Ersetzen Sie `CLUSTERNAME` durch Ihren HDInsight-Clusternamen, und geben Sie dann den folgenden Befehl ein:

    ```cmd
    scp ./target/hbaseapp-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:hbaseapp-1.0-SNAPSHOT.jar
    ```

2. Stellen Sie eine Verbindung mit dem HBase-Cluster her. Ersetzen Sie `CLUSTERNAME` durch Ihren HDInsight-Clusternamen, und geben Sie dann den folgenden Befehl ein:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Verwenden Sie den folgenden Befehl in Ihrer offenen SSH-Verbindung, um eine HBase-Tabelle mithilfe der Java-Anwendung zu erstellen:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.CreateTable
    ```

    Mit diesem Befehl wird eine HBase-Tabelle mit dem Namen **people** erstellt, die dann mit Daten aufgefüllt wird.

4. Suchen Sie mit dem folgenden Befehl nach E-Mail-Adressen in der Tabelle:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.SearchByEmail contoso.com
    ```

    Sie erhalten folgende Ergebnisse:

    ```console
    Franklin Holtz - ID: 2
    Franklin Holtz - franklin@contoso.com - ID: 2
    Rae Schroeder - ID: 4
    Rae Schroeder - rae@contoso.com - ID: 4
    Gabriela Ingram - ID: 6
    Gabriela Ingram - gabriela@contoso.com - ID: 6
    ```

5. Verwenden Sie zum Löschen der Tabelle den folgenden Befehl:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable
    ```

## <a name="upload-the-jar-and-run-jobs-powershell"></a>Hochladen der JAR-Datei und Ausführen von Aufträgen (PowerShell)

In den folgenden Schritten wird das Azure PowerShell [AZ-Modul](/powershell/azure/new-azureps-module-az) zum Hochladen der JAR-Datei in den Standardspeicher Ihres Apache HBase-Clusters verwendet. HDInsight-Cmdlets werden dann zur Remoteausführung der Beispiele verwendet.

1. Nachdem Sie das AZ-Modul installiert und konfiguriert haben, erstellen Sie eine Datei mit dem Namen `hbase-runner.psm1`. Verwenden Sie als Inhalt der Datei den folgenden Text:

   ```powershell
    <#
    .SYNOPSIS
    Copies a file to the primary storage of an HDInsight cluster.
    .DESCRIPTION
    Copies a file from a local directory to the blob container for
    the HDInsight cluster.
    .EXAMPLE
    Start-HBaseExample -className "com.microsoft.examples.CreateTable"
    -clusterName "MyHDInsightCluster"

    .EXAMPLE
    Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
    -clusterName "MyHDInsightCluster"
    -emailRegex "contoso.com"

    .EXAMPLE
    Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
    -clusterName "MyHDInsightCluster"
    -emailRegex "^r" -showErr
    #>

    function Start-HBaseExample {
    [CmdletBinding(SupportsShouldProcess = $true)]
    param(
    #The class to run
    [Parameter(Mandatory = $true)]
    [String]$className,

    #The name of the HDInsight cluster
    [Parameter(Mandatory = $true)]
    [String]$clusterName,

    #Only used when using SearchByEmail
    [Parameter(Mandatory = $false)]
    [String]$emailRegex,

    #Use if you want to see stderr output
    [Parameter(Mandatory = $false)]
    [Switch]$showErr
    )

    Set-StrictMode -Version 3

    # Is the Azure module installed?
    FindAzure

    # Get the login for the HDInsight cluster
    $creds=Get-Credential -Message "Enter the login for the cluster" -UserName "admin"

    # The JAR
    $jarFile = "wasb:///example/jars/hbaseapp-1.0-SNAPSHOT.jar"

    # The job definition
    $jobDefinition = New-AzHDInsightMapReduceJobDefinition `
        -JarFile $jarFile `
        -ClassName $className `
        -Arguments $emailRegex

    # Get the job output
    $job = Start-AzHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds
    Write-Host "Wait for the job to complete ..." -ForegroundColor Green
    Wait-AzHDInsightJob `
        -ClusterName $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds
    if($showErr)
    {
    Write-Host "STDERR"
    Get-AzHDInsightJobOutput `
                -Clustername $clusterName `
                -JobId $job.JobId `
                -HttpCredential $creds `
                -DisplayOutputType StandardError
    }
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzHDInsightJobOutput `
                -Clustername $clusterName `
                -JobId $job.JobId `
                -HttpCredential $creds
    }

    <#
    .SYNOPSIS
    Copies a file to the primary storage of an HDInsight cluster.
    .DESCRIPTION
    Copies a file from a local directory to the blob container for
    the HDInsight cluster.
    .EXAMPLE
    Add-HDInsightFile -localPath "C:\temp\data.txt"
    -destinationPath "example/data/data.txt"
    -ClusterName "MyHDInsightCluster"
    .EXAMPLE
    Add-HDInsightFile -localPath "C:\temp\data.txt"
    -destinationPath "example/data/data.txt"
    -ClusterName "MyHDInsightCluster"
    -Container "MyContainer"
    #>

    function Add-HDInsightFile {
        [CmdletBinding(SupportsShouldProcess = $true)]
        param(
            #The path to the local file.
            [Parameter(Mandatory = $true)]
            [String]$localPath,

            #The destination path and file name, relative to the root of the container.
            [Parameter(Mandatory = $true)]
            [String]$destinationPath,

            #The name of the HDInsight cluster
            [Parameter(Mandatory = $true)]
            [String]$clusterName,

            #If specified, overwrites existing files without prompting
            [Parameter(Mandatory = $false)]
            [Switch]$force
        )

        Set-StrictMode -Version 3

        # Is the Azure module installed?
        FindAzure

        # Get authentication for the cluster
        $creds=Get-Credential

        # Does the local path exist?
        if (-not (Test-Path $localPath))
        {
            throw "Source path '$localPath' does not exist."
        }

        # Get the primary storage container
        $storage = GetStorage -clusterName $clusterName

        # Upload file to storage, overwriting existing files if -force was used.
        Set-AzStorageBlobContent -File $localPath `
            -Blob $destinationPath `
            -force:$force `
            -Container $storage.container `
            -Context $storage.context
    }

    function FindAzure {
        # Is there an active Azure subscription?
        $sub = Get-AzSubscription -ErrorAction SilentlyContinue
        if(-not($sub))
        {
            Connect-AzAccount
        }
    }

    function GetStorage {
        param(
            [Parameter(Mandatory = $true)]
            [String]$clusterName
        )
        $hdi = Get-AzHDInsightCluster -ClusterName $clusterName
        # Does the cluster exist?
        if (!$hdi)
        {
            throw "HDInsight cluster '$clusterName' does not exist."
        }
        # Create a return object for context & container
        $return = @{}
        $storageAccounts = @{}

        # Get storage information
        $resourceGroup = $hdi.ResourceGroup
        $storageAccountName=$hdi.DefaultStorageAccount.split('.')[0]
        $container=$hdi.DefaultStorageContainer
        $storageAccountKey=(Get-AzStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        # Get the resource group, in case we need that
        $return.resourceGroup = $resourceGroup
        # Get the storage context, as we can't depend
        # on using the default storage context
        $return.context = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        # Get the container, so we know where to
        # find/store blobs
        $return.container = $container
        # Return storage accounts to support finding all accounts for
        # a cluster
        $return.storageAccount = $storageAccountName
        $return.storageAccountKey = $storageAccountKey

        return $return
    }
    # Only export the verb-phrase things
    export-modulemember *-*
   ```

    Diese Datei enthält zwei Module:

   * **Add-HDInsightFile** wird für das Hochladen von Dateien in den Cluster verwendet.
   * **Start-HBaseExample** wird für das Ausführen der bereits erstellten Klassen verwendet.

2. Speichern Sie die Datei `hbase-runner.psm1` im Verzeichnis `hbaseapp`.

3. Registrieren Sie die Module mit Azure PowerShell. Öffnen Sie ein neues Azure PowerShell-Fenster, und bearbeiten Sie den folgenden Befehl, indem Sie `CLUSTERNAME` durch den Namen Ihres Clusters ersetzen. Geben Sie dann die folgenden Befehle ein:

    ```powershell
    cd C:\HDI\hbaseapp
    $myCluster = "CLUSTERNAME"
    Import-Module .\hbase-runner.psm1
    ```

4. Verwenden Sie den folgenden Befehl zum Hochladen von `hbaseapp-1.0-SNAPSHOT.jar` in Ihren Cluster.

    ```powershell
    Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName $myCluster
    ```

    Geben Sie bei entsprechender Aufforderung den Anmeldenamen (Administrator) und das Kennwort für den Cluster ein. Der Befehl lädt `hbaseapp-1.0-SNAPSHOT.jar` an den Speicherort `example/jars` im primären Speicher für Ihren Cluster hoch.

5. Verwenden Sie den folgenden Befehl, um eine Tabelle mithilfe von `hbaseapp` zu erstellen:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName $myCluster
    ```

    Geben Sie bei entsprechender Aufforderung den Anmeldenamen (Administrator) und das Kennwort für den Cluster ein.

    Dieser Befehl erstellt eine Tabelle namens **people** in HBase in Ihrem HDInsight-Cluster. Bei diesem Befehl erfolgt keine Ausgabe im Konsolenfenster.

6. Verwenden Sie den folgenden Befehl, um nach Einträgen in die Tabelle zu suchen:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName $myCluster -emailRegex contoso.com
    ```

    Geben Sie bei entsprechender Aufforderung den Anmeldenamen (Administrator) und das Kennwort für den Cluster ein.

    Dieser Befehl verwendet die Klasse `SearchByEmail`, um nach allen Zeilen zu suchen, in denen die Spaltenfamilie `contactinformation` und die Spalte `email` die Zeichenfolge `contoso.com` enthalten. Daraufhin sollte Sie folgende Ergebnisse erhalten:

    ```output
    Franklin Holtz - ID: 2
    Franklin Holtz - franklin@contoso.com - ID: 2
    Rae Schroeder - ID: 4
    Rae Schroeder - rae@contoso.com - ID: 4
    Gabriela Ingram - ID: 6
    Gabriela Ingram - gabriela@contoso.com - ID: 6
    ```

    Wenn **fabrikam.com** als `-emailRegex`-Wert verwendet wird, werden Benutzer zurückgegeben, die **fabrikam.com** im Feld „E-Mail“ enthalten. Sie können als Suchbegriffe auch reguläre Ausdrücke verwenden. Mit **^r** werden beispielsweise E-Mail-Adressen zurückgegeben, die mit dem Buchstaben „r“ beginnen.

7. Verwenden Sie zum Löschen der Tabelle den folgenden Befehl:

    ```PowerShell
    Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName $myCluster
    ```

### <a name="no-results-or-unexpected-results-when-using-start-hbaseexample"></a>Keine oder unerwartete Ergebnisse bei Verwenden von "Start-HBaseExample"

Verwenden Sie den Parameter `-showErr`, wenn Sie den Standardfehler (STDERR) sehen möchten, der während der Ausführung des Auftrags erzeugt wurde.

## <a name="next-steps"></a>Nächste Schritte

[Informationen zum Verwenden von SQLLine mit Apache HBase](apache-hbase-query-with-phoenix.md)