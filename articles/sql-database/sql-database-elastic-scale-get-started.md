---
title: Erste Schritte mit Tools für elastische Datenbanken – Azure| Microsoft Docs
description: Grundlegende Erläuterung der Tools für elastische Datenbanken von Azure SQL-Datenbank, einschließlich einer einfachen Beispiel-App.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anumjs
ms.author: anjangsh
ms.reviewer: sstein
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 7c2e3b2bea7ddef3dfbf69f85b499d9a60158689
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60585013"
---
# <a name="get-started-with-elastic-database-tools"></a>Erste Schritte mit Tools für elastische Datenbanken

Dieses Dokument enthält eine Einführung in die Entwickleroberfläche für die [Clientbibliothek für elastische Datenbanken](sql-database-elastic-database-client-library.md), die anhand einer Beispiel-App vorgestellt wird. Mit der Beispiel-App wird eine einfache Shardinganwendung erstellt, und es werden die wichtigsten Funktionen des Features „Tools für elastische Datenbanken“ von Azure SQL-Datenbank erläutert. Dabei stehen die Anwendungsfälle für [Shardzuordnungsverwaltung](sql-database-elastic-scale-shard-map-management.md), [datenabhängiges Routing](sql-database-elastic-scale-data-dependent-routing.md) und [Abfragen mehrerer Shards](sql-database-elastic-scale-multishard-querying.md) im Mittelpunkt. Die Clientbibliothek ist sowohl für .NET als auch für Java verfügbar. 

## <a name="elastic-database-tools-for-java"></a>Tools für elastische Datenbanken für Java

### <a name="prerequisites"></a>Voraussetzungen

* Ein Java Developer Kit (JDK), Version 1.8 oder höher
* [Maven](https://maven.apache.org/download.cgi)
* Ein SQL-Datenbank-Server in Azure oder eine lokale SQL Server-Instanz

### <a name="download-and-run-the-sample-app"></a>Herunterladen und Ausführen der Beispiel-App

Verfahren Sie wie folgt, um die JAR-Dateien zu erstellen und erste Schritte mit dem Beispielprojekt auszuführen: 
1. Klonen Sie das [GitHub-Repository](https://github.com/Microsoft/elastic-db-tools-for-java), das die Clientbibliothek zusammen mit der Beispiel-App enthält. 

2. Bearbeiten Sie die Datei _./sample/src/main/resources/resource.properties_, um folgende Eigenschaften festzulegen:
    * TEST_CONN_USER
    * TEST_CONN_PASSWORD
    * TEST_CONN_SERVER_NAME

3. Führen Sie im Verzeichnis _./sample_ den folgenden Befehl aus, um das Beispielprojekt zu erstellen:

    ```
    mvn install
    ```
    
4. Führen Sie im Verzeichnis _./sample_ den folgenden Befehl aus, um das Beispielprojekt zu starten: 
    
    ```
    mvn -q exec:java "-Dexec.mainClass=com.microsoft.azure.elasticdb.samples.elasticscalestarterkit.Program"
    ```
    
5. Experimentieren Sie mit den verschiedenen Optionen, um mehr über die Funktionen der Clientbibliothek zu erfahren. Sehen Sie sich den Code an, um mehr über die Implementierung der Beispiel-App zu erfahren.

    ![Fortschritt in Java][5]
    
Glückwunsch! Sie haben mit den Tools für elastische Datenbanken in Azure SQL-Datenbank erfolgreich Ihre erste Shardinganwendung erstellt und ausgeführt. Werfen Sie einen Blick auf die im Beispiel erstellten Shards, indem Sie mithilfe von Visual Studio oder SQL-Server Management Studio eine Verbindung mit Ihrer SQL-Datenbank herstellen. Sie sehen, dass mit dem Beispiel neue Beispiel-Shard-Datenbanken und eine Shard-Map-Managerdatenbank erstellt wurden. 

Um die Clientbibliothek Ihrem eigenen Maven-Projekt hinzuzufügen, fügen Sie die folgende Abhängigkeit in Ihrer POM-Datei hinzu:

```xml
<dependency> 
    <groupId>com.microsoft.azure</groupId> 
    <artifactId>elastic-db-tools</artifactId> 
    <version>1.0.0</version> 
</dependency> 
```

## <a name="elastic-database-tools-for-net"></a>Tools für elastische Datenbanken für .NET

### <a name="prerequisites"></a>Voraussetzungen

* Visual Studio 2012 oder eine neuere Version mit C#. Laden Sie eine kostenlose Version unter [Visual Studio-Downloads](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)herunter.
* NuGet 2.7 oder eine neuere Version. Die aktuelle Version finden Sie unter [Installing NuGet](https://docs.nuget.org/docs/start-here/installing-nuget) (Installieren von NuGet).

### <a name="download-and-run-the-sample-app"></a>Herunterladen und Ausführen der Beispiel-App

Um die Bibliothek zu installieren, wechseln Sie zu [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Die Bibliothek wird mit der im nachfolgenden Abschnitt beschriebenen Beispiel-App installiert.

Gehen Sie folgendermaßen vor, um das Beispiel herunterzuladen und auszuführen: 

1. Laden Sie das Beispiel [Elastic DB Tools for Azure SQL - Getting Started (Elastische DB-Tools für Azure SQL – Erste Schritte)](https://code.msdn.microsoft.com/windowsapps/Elastic-Scale-with-Azure-a80d8dc6) von MSDN herunter. Entzippen Sie das Beispiel an einem Speicherort Ihrer Wahl.

2. Um ein Projekt zu erstellen, öffnen Sie die Projektmappe *ElasticScaleStarterKit.sln* im Verzeichnis *C#* .

3. Öffnen Sie in der Projektmappe für das Beispielprojekt der Datei *app.config*. Folgen Sie dann den Anweisungen in der Datei, um den Namen des Azure SQL-Datenbank-Servers und Ihre Anmeldeinformationen (Benutzername und Kennwort) hinzuzufügen.

4. Erstellen Sie die Anwendung, und führen Sie sie aus. Wenn Sie dazu aufgefordert werden, lassen Sie die NuGet-Pakete der Projektmappe von Visual Studio wiederherstellen. Dadurch wird die aktuelle Version der Clientbibliothek für elastische Datenbanken von NuGet heruntergeladen.

5. Experimentieren Sie mit den verschiedenen Optionen, um mehr über die Funktionen der Clientbibliothek zu erfahren. Beachten Sie die von der Anwendung ausgeführten Schritte in der Konsolenausgabe, und erkunden Sie den zugrunde liegenden Code.
   
    ![Status][4]

Glückwunsch! Sie haben mit den Tools für elastische Datenbanken in SQL-Datenbank erfolgreich Ihre erste Shardinganwendung erstellt und ausgeführt. Werfen Sie einen Blick auf die im Beispiel erstellten Shards, indem Sie mithilfe von Visual Studio oder SQL-Server Management Studio eine Verbindung mit Ihrer SQL-Datenbank herstellen. Sie sehen, dass mit dem Beispiel neue Beispiel-Shard-Datenbanken und eine Shard-Map-Managerdatenbank erstellt wurden.

> [!IMPORTANT]
> Wir empfehlen, immer die neueste Version von Management Studio zu verwenden, damit Sie mit Updates von Azure und SQL-Datenbank synchron sind. [Aktualisieren Sie SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="key-pieces-of-the-code-sample"></a>Zentrale Elemente des Codebeispiels

* **Verwalten von Shards und Shardzuordnungen**: Der Code in der Datei *ShardManagementUtils.cs* veranschaulicht die Arbeit mit Shards, Bereichen und Zuordnungen. Weitere Informationen finden Sie unter [Horizontales Hochskalieren von Datenbanken mit dem Shardzuordnungs-Manager](https://go.microsoft.com/?linkid=9862595).  

* **Datenabhängiges Routing**: Das Routing von Transaktionen zum richtigen Shard wird in der Datei *DataDependentRoutingSample.cs* dargestellt. Weitere Informationen finden Sie unter [Datenabhängiges Routing](https://go.microsoft.com/?linkid=9862596). 

* **Abfragen über mehrere Shards hinweg**: Shardübergreifende Abfragen werden in der Datei *MultiShardQuerySample.cs* veranschaulicht. Weitere Informationen finden Sie unter [Abfragen mehrerer Shards](https://go.microsoft.com/?linkid=9862597).

* **Hinzufügen leerer Shards**: Das iterative Hinzufügen neuer leerer Shards wird mit dem Code in der Datei *CreateShardSample.cs* durchgeführt. Weitere Informationen finden Sie unter [Horizontales Hochskalieren von Datenbanken mit dem Shardzuordnungs-Manager](https://go.microsoft.com/?linkid=9862595).

## <a name="other-elastic-scale-operations"></a>Weitere Elastic Scale-Operationen

* **Aufteilen eines vorhandenen Shards**: Die Möglichkeit zum Aufteilen von Shards wird durch das Split-Merge-Tool bereitgestellt. Weitere Informationen finden Sie unter [Verschieben von Daten zwischen horizontal hochskalierten Clouddatenbanken](sql-database-elastic-scale-overview-split-and-merge.md).

* **Zusammenführen vorhandener Shards**: Shardzusammenführungen werden ebenfalls mit dem Split-Merge-Tool durchgeführt. Weitere Informationen finden Sie unter [Verschieben von Daten zwischen horizontal hochskalierten Clouddatenbanken](sql-database-elastic-scale-overview-split-and-merge.md).   

## <a name="cost"></a>Kosten

Die Bibliothek für Tools für elastische Datenbanken ist kostenlos. Bei der Verwendung der Tools für elastische Datenbanken entstehen neben den Gebühren für die Nutzung von Azure keine zusätzlichen Kosten. 

Die Beispielanwendung erstellt z. B. neue Datenbanken. Die Kosten dieser Funktion richten sich nach der ausgewählten Edition von SQL-Datenbank und nach der Azure-Nutzung Ihrer Anwendung.

Preisinformationen finden Sie unter [SQL-Datenbank – Preisdetails](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den Tools für elastische Datenbanken finden Sie in den folgenden Artikeln:

* Codebeispiele: 
  * Tools für elastische Datenbanken ([.NET](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE), [Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22))
  * [Tools für elastische Datenbanken für Azure SQL – Entity Framework-Integration](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
  * [Shard-Elastizität im Script Center](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
* Blog: [Ankündigung der elastischen Skalierung](https://azure.microsoft.com/blog/20../../introducing-elastic-scale-preview-for-azure-sql-database/) (in englischer Sprache)
* Channel 9: [Übersichtsvideo zur elastischen Skalierung](https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale) (in englischer Sprache)
* Diskussionsforum: [Forum zu Azure SQL-Datenbank](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) (in englischer Sprache)
* Messen der Leistung: [Leistungsindikatoren für den Shardzuordnungs-Manager](sql-database-elastic-database-client-library.md)

<!--Anchors-->
[The Elastic Scale Sample Application]: #The-Elastic-Scale-Sample-Application
[Download and Run the Sample App]: #Download-and-Run-the-Sample-App
[Cost]: #Cost
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-get-started/newProject.png
[2]: ./media/sql-database-elastic-scale-get-started/click-online.png
[3]: ./media/sql-database-elastic-scale-get-started/click-CSharp.png
[4]: ./media/sql-database-elastic-scale-get-started/output2.png
[5]: ./media/sql-database-elastic-scale-get-started/java-client-library.PNG

