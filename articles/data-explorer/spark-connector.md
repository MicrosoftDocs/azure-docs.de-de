---
title: Verwenden des Azure Data Explorer-Connectors für Apache Spark zum Verschieben von Daten zwischen Azure Data Explorer- und Spark-Clustern.
description: In diesem Thema erfahren Sie, wie Sie Daten zwischen Azure Data Explorer- und Apache Spark-Clustern verschieben.
author: orspod
ms.author: orspodek
ms.reviewer: michazag
ms.service: data-explorer
ms.topic: conceptual
ms.date: 4/29/2019
ms.openlocfilehash: 854e29b67b6e24c583a98b5851bf17551cfcbf61
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65441354"
---
# <a name="azure-data-explorer-connector-for-apache-spark-preview"></a>Azure Data Explorer Connector für Apache Spark (Preview)

[Apache Spark](https://spark.apache.org/) ist eine vereinheitlichte Engine zur Verarbeitung von umfangreichen Daten. Azure Data Explorer ist ein schneller, vollständig verwalteter Datenanalysedienst für Echtzeitanalysen großer Mengen an Daten. 

Azure Data Explorer-Connector für Spark implementiert Datenquelle und Datensenke zum Verschieben von Daten zwischen Azure Data Explorer- und Spark-Clustern, um Funktionen von beiden verwenden zu können. Mit Azure Data Explorer und Apache Spark können Sie schnelle und skalierbare Anwendungen für datengesteuerte Szenarien erstellen. Beispiele dafür sind maschinelles Lernen (Machine Learning, ML), Extrahieren, Transformieren und Laden (Extract-Transform-Load, ETL) und Log Analytics. Das Schreiben in Azure Data Explorer kann im Batch- und im Streamingmodus erfolgen.
Das Lesen aus Azure Data Explorer unterstützt Löschen von Spalten und Prädikat-Pushdown, wodurch das Volumen der übertragenen Daten verringert wird, indem Daten in Azure Data Explorer ausgefiltert werden.

Azure Data Explorer-Connector für Spark ist ein [Open Source-Projekt](https://github.com/Azure/azure-kusto-spark), das auf jedem Spark-Cluster ausgeführt werden kann.

> [!NOTE]
> Obwohl sich einige der unten stehenden Beispiele auf einen [Azure Databricks](https://docs.azuredatabricks.net/) Spark-Cluster beziehen, akzeptiert Azure Data Explorer-Connector für Spark keine direkten Abhängigkeiten von Databricks oder einer anderen Spark-Distribution.

## <a name="prerequisites"></a>Voraussetzungen

* [Erstellen eines Azure Data Explorer-Clusters und einer Datenbank](/azure/data-explorer/create-cluster-database-portal) 
* Erstellen eines Spark-Clusters
* Installieren Sie die Azure Data Explorer-Connectorbibliothek sowie die unter [Abhängigkeiten](https://github.com/Azure/azure-kusto-spark#dependencies) aufgeführten Bibliotheken, einschließlich der folgenden[Kusto Java SDK](/azure/kusto/api/java/kusto-java-client-library)-Bibliotheken:
    * [Kusto Data Client](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)
    * [Kusto Ingest Client](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)
* Vorgefertigte Bibliotheken für [Spark 2.4, Scala 2.11](https://github.com/Azure/azure-kusto-spark/releases)

## <a name="how-to-build-the-spark-connector"></a>Erstellen des Spark-Connectors

Spark-Connector kann, wie unten erläutert, aus [Quellen](https://github.com/Azure/azure-kusto-spark) erstellt werden.

> [!NOTE]
> Dieser Schritt ist optional. Wenn Sie vorgefertigte Bibliotheken verwenden, wechseln Sie zu [Einrichtung des Spark-Clusters](#spark-cluster-setup).

### <a name="build-prerequisites"></a>Buildvoraussetzungen

* Java 1.8 SDK-Installation
* [Maven 3.x](https://maven.apache.org/download.cgi)-Installation
* Apache Spark, Version 2.4.0 oder höher

> [!TIP]
> 2.3.x-Versionen werden ebenfalls unterstützt, erfordern aber möglicherweise Änderungen in den „pom.xml“-Abhängigkeiten.

Wenn Sie Scala-/Java-Anwendungen mit Maven-Projektdefinitionen verwenden, verknüpfen Sie Ihre Anwendung mit dem folgenden Artefakt (die neueste Version kann abweichen):

```Maven
   <dependency>
     <groupId>com.microsoft.azure</groupId>
     <artifactId>spark-kusto-connector</artifactId>
     <version>1.0.0-Beta-02</version>
   </dependency>
```

### <a name="build-commands"></a>Erstellen von Befehlen

Führen Sie den folgenden Befehl aus, um die JAR-Datei zu erstellen und alle Tests auszuführen:

```
mvn clean package
```

Um die JAR-Datei zu erstellen, führen Sie alle Tests aus, und installieren Sie die JAR-Datei in Ihrem lokalen Maven-Repository:

```
mvn clean install
```

Weitere Informationen finden Sie unter [Connectorverwendung](https://github.com/Azure/azure-kusto-spark#usage).

## <a name="spark-cluster-setup"></a>Einrichtung des Spark-Clusters

> [!NOTE]
> Es wird empfohlen, das neueste Release des Azure Data Explorer-Connectors für Spark zu verwenden, wenn Sie die folgenden Schritte ausführen:

1. Legen Sie die folgenden Spark-Clustereinstellungen auf Grundlage von Azure Databricks-Cluster unter Verwendung von Spark 2.4 und Scala 2.11 fest: 

    ![Databricks-Clustereinstellungen](media/spark-connector/databricks-cluster.png)

1. Importieren Sie die Azure Data Explorer-Connectorbibliothek:

    ![Importieren der Azure Data Explorer-Bibliothek](media/spark-connector/db-create-library.png)

1. Fügen Sie zusätzliche Abhängigkeiten hinzu:

    ![Hinzufügen von Abhängigkeiten](media/spark-connector/db-dependencies.png)

    > [!TIP]
    > Die richtige Java-Releaseversion für jedes Spark-Release finden Sie [hier](https://github.com/Azure/azure-kusto-spark#dependencies).

1. Überprüfen Sie, ob alle erforderlichen Bibliotheken installiert sind:

    ![Überprüfen von installierten Bibliotheken](media/spark-connector/db-libraries-view.png)

## <a name="authentication"></a>Authentication

Azure Data Explorer-Connector für Spark gestattet Ihnen die Authentifizierung mit Azure Active Directory (Azure AD) unter Verwendung einer [Azure AD-Anwendung](#azure-ad-application-authentication), eines [Azure AD-Zugriffstokens](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#direct-authentication-with-access-token), mit [Geräteauthentifizierung ](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#device-authentication) (für Nicht-Produktionsszenarien) oder mit [Azure Key Vault](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#key-vault). Der Benutzer muss das „azure-keyvault“-Paket installieren und Anwendungsanmeldeinformationen für den Zugriff auf die Key Vault-Ressource bereitstellen.

### <a name="azure-ad-application-authentication"></a>Azure AD-Anwendungsauthentifizierung

Die einfachsten und gängigsten Authentifizierungsmethoden. Diese Methode wird für die Verwendung von Azure Data Explorer-Connector für Spark empfohlen.

|Eigenschaften  |BESCHREIBUNG  |
|---------|---------|
|**KUSTO_AAD_CLIENT_ID**     |   (Client-)Bezeichner der Azure AD-Anwendung.      |
|**KUSTO_AAD_AUTHORITY_ID**     |  Azure AD-Authentifizierungsautorität. Azure AD Directory-(Mandanten-)ID.        |
|**KUSTO_AAD_CLIENT_PASSWORD**    |    Azure AD-Anwendungsschlüssel für den Client.     |

### <a name="azure-data-explorer-privileges"></a>Azure Data Explorer-Berechtigungen

Die folgenden Berechtigungen müssen für einen Azure Data Explorer-Cluster gewährt werden:

* Zum Lesen (Datenquelle) muss die Azure AD-Anwendung *Anzeigen*-Berechtigungen für die Zieldatenbank besitzen oder *Administrator*berechtigungen für die Zieltabelle.
* Zum Schreiben (Datensenke) muss die Azure AD-Anwendung *Erfassen*-Berechtigungen für die Zieldatenbank besitzen. Außerdem benötigt sie *Benutzer*berechtigungen für die Zieldatenbank, um neue Tabellen zu erstellen. Wenn die Zieltabelle bereits vorhanden ist, können *Administrator*berechtigungen für die Zieltabelle konfiguriert werden.
 
Weitere Informationen zu Azure Data Explorer-Prinzipalrollen finden Sie unter [Rollenbasierte Autorisierung](/azure/kusto/management/access-control/role-based-authorization). Informationen zum Verwalten von Sicherheitsrollen finden Sie unter [Sicherheitsrollenverwaltung](/azure/kusto/management/security-roles).

## <a name="spark-sink-writing-to-azure-data-explorer"></a>Spark-Senke: Schreiben in Azure Data Explorer

1. Einrichten von Senkenparametern:

     ```scala
    val KustoSparkTestAppId = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppId")
    val KustoSparkTestAppKey = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppKey")
 
    val appId = KustoSparkTestAppId
    val appKey = KustoSparkTestAppKey
    val authorityId = "72f988bf-86f1-41af-91ab-2d7cd011db47"
    val cluster = "Sparktest.eastus2"
    val database = "TestDb"
    val table = "StringAndIntTable"
    ```

1. Schreiben von Spark DataFrame in Azure Data Explorer-Cluster als Batch:

    ```scala
    df.write
      .format("com.microsoft.kusto.spark.datasource")
      .option(KustoOptions.KUSTO_CLUSTER, cluster)
      .option(KustoOptions.KUSTO_DATABASE, database)
      .option(KustoOptions.KUSTO_TABLE, table)
      .option(KustoOptions.KUSTO_AAD_CLIENT_ID, appId)
      .option(KustoOptions.KUSTO_AAD_CLIENT_PASSWORD, appKey) 
      .option(KustoOptions.KUSTO_AAD_AUTHORITY_ID, authorityId)
      .save()
    ```

1. Schreiben von Streamingdaten:

    ```scala    
    import org.apache.spark.sql.streaming.Trigger
    import java.util.concurrent.TimeUnit
    
    // Set up a checkpoint and disable codeGen. Set up a checkpoint and disable codeGen as a workaround for an known issue 
    spark.conf.set("spark.sql.streaming.checkpointLocation", "/FileStore/temp/checkpoint")
    spark.conf.set("spark.sql.codegen.wholeStage","false")
    
    // Write to a Kusto table fro streaming source
    val kustoQ = csvDf
          .writeStream
          .format("com.microsoft.kusto.spark.datasink.KustoSinkProvider")
          .options(Map(
            KustoOptions.KUSTO_CLUSTER -> cluster,
            KustoOptions.KUSTO_TABLE -> table,
            KustoOptions.KUSTO_DATABASE -> database,
            KustoOptions.KUSTO_AAD_CLIENT_ID -> appId,
            KustoOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey,
            KustoOptions.KUSTO_AAD_AUTHORITY_ID -> authorityId))
          .trigger(Trigger.Once)
    
    kustoQ.start().awaitTermination(TimeUnit.MINUTES.toMillis(8))
    ```

## <a name="spark-source-reading-from-azure-data-explorer"></a>Spark-Quelle: Lesen aus Azure Data Explorer

1. Beim Lesen kleiner Mengen von Daten definieren Sie die Datenabfrage:

    ```scala
    val conf: Map[String, String] = Map(
          KustoOptions.KUSTO_AAD_CLIENT_ID -> appId,
          KustoOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey,
          KustoOptions.KUSTO_QUERY -> s"$table | where (ColB % 1000 == 0) | distinct ColA"      
        )
    
    // Simplified syntax flavor
    import org.apache.spark.sql._
    import com.microsoft.kusto.spark.sql.extension.SparkExtension._
    import org.apache.spark.SparkConf
    
    val df = spark.read.kusto(cluster, database, "", conf)
    display(df)
    ```

1. Beim Lesen großer Mengen von Daten muss temporärer Blob-Speicher bereitgestellt werden. SAS-Schlüssel des Speichercontainers oder Speicherkontonamen, Kontoschlüssel und Containernamen angeben. Dieser Schritt ist nur für die aktuelle Vorschauversion des Spark-Connectors erforderlich.

    ```scala
    // Use either container/account-key/account name, or container SaS
    val container = dbutils.secrets.get(scope = "KustoDemos", key = "blobContainer")
    val storageAccountKey = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountKey")
    val storageAccountName = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountName")
    // val storageSas = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageSasUrl")
    ```

    Im obigen Beispiel greifen wir nicht mithilfe der Connector-Benutzeroberfläche auf den Schlüsseltresor zu. Alternativ verwenden wir eine einfachere Methode der Verwendung von Databricks Geheimnissen.

1. Lesen aus Azure Data Explorer:

    ```scala
    val df2 = spark.read.kusto(cluster, database, "ReallyBigTable", conf3)
    
    val dfFiltered = df2
      .where(df2.col("ColA").startsWith("row-2"))
      .filter("ColB > 12")
      .filter("ColB <= 21")
      .select("ColA")
    
    display(dfFiltered)
    ```
