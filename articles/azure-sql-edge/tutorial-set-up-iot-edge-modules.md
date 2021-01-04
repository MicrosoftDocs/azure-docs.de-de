---
title: Einrichten von IoT Edge-Modulen in Azure SQL Edge
description: Im zweiten Teil dieses dreiteiligen Azure SQL Edge-Tutorials zum Vorhersagen von Eisenerzverunreinigungen richten Sie IoT Edge-Module und -Verbindungen ein.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sourabha, sstein
ms.date: 09/22/2020
ms.openlocfilehash: 75e6ebaea4c5ba883820d2309212b35fed128142
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422126"
---
# <a name="set-up-iot-edge-modules-and-connections"></a>Einrichten von IoT Edge-Modulen und -Verbindungen

Im zweiten Teil dieses dreiteiligen Tutorials zum Vorhersagen von Eisenerzverunreinigungen in Azure SQL Edge richten Sie die folgenden IoT Edge Module ein:

- Azure SQL Edge
- Datengenerator „IoT Edge-Modul“

## <a name="specify-container-registry-credentials"></a>Angeben von Anmeldeinformationen für die Containerregistrierung

Die Anmeldeinformationen für die Containerregistrierungen zum Hosten von Modulimages müssen angegeben werden. Sie finden diese Informationen in der Containerregistrierung, die in Ihrer Ressourcengruppe erstellt wurde. Navigieren Sie zum Abschnitt **Zugriffsschlüssel**. Notieren Sie sich die folgenden Felder:

- Registrierungsname
- Anmeldeserver
- Username
- Kennwort

Geben Sie jetzt die Containeranmeldeinformationen im IoT Edge-Modul an.

1. Navigieren Sie zu dem IoT-Hub, der in Ihrer Ressourcengruppe erstellt wurde.

2. Klicken Sie im Abschnitt **IoT Edge** unter **Automatische Geräteverwaltung** auf **Geräte-ID**. Für dieses Tutorial lautet die ID `IronOrePredictionDevice`.

3. Wählen Sie den Abschnitt **Module festlegen** aus.

4. Geben Sie unter **Anmeldeinformationen für Containerregistrierung** die folgenden Werte ein:

   _Feld_|_Wert_
   -------|-------
   Name|Registrierungsname
   Adresse|Anmeldeserver
   Benutzername|Username
   Kennwort|Kennwort
  
## <a name="build-push-and-deploy-the-data-generator-module"></a>Erstellen, Pushen und Bereitstellen des Datengeneratormoduls

1. Klonen Sie die [Projektdateien](https://github.com/microsoft/sqlsourabh/tree/main/SQLEdgeSamples/IoTEdgeSamples/IronOreSilica) auf Ihrem Computer.
2. Öffnen Sie die Datei **IronOre_Silica_Predict.sln** mithilfe von Visual Studio 2019.
3. Aktualisieren Sie die Details der Containerregistrierung in der Datei **deployment.template.json**. 
   ```json
   "registryCredentials":{
        "RegistryName":{
            "username":"",
            "password":""
            "address":""
        }
    }
   ```
4. Aktualisieren Sie die Datei **modules.json**, um die Zielcontainerregistrierung (oder das Repository für das Modul) anzugeben.
   ```json
   "image":{
        "repository":"samplerepo.azurecr.io/ironoresilicapercent",
        "tag":
    }
   ```
5. Führen Sie das Projekt im Debug- oder Releasemodus aus, um sicherzustellen, dass es ohne Probleme ausgeführt wird 
6. Pushen Sie das Projekt an Ihre Containerregistrierung, indem Sie mit der rechten Maustaste auf den Projektnamen klicken und dann **Build and Push IoT Edge Modules** (IoT Edge-Module erstellen und pushen) auswählen.
7. Stellen Sie das Datengeneratormodul als IoT Edge-Modul auf Ihrem Edge-Gerät bereit. 

## <a name="deploy-the-azure-sql-edge-module"></a>Bereitstellen des Azure SQL Edge-Moduls

1. Stellen Sie das Azure SQL Edge-Modul bereit, indem Sie auf **+ Hinzufügen** und anschließend auf **Marketplace-Modul** klicken. 

2. Suchen Sie auf dem Blatt **IoT Edge-Modul aus Marketplace** nach *Azure SQL Edge*, und wählen Sie *Azure SQL Edge Developer* aus. 

3. Klicken Sie unter **IoT Edge-Module** auf das neu hinzugefügte Modul *Azure SQL Edge*, um das Azure SQL Edge-Modul zu konfigurieren. Weitere Informationen zu den Konfigurationsoptionen finden Sie unter [Bereitstellen von Azure SQL Edge (Vorschau)](./deploy-portal.md).

4. Fügen Sie der Bereitstellung des Moduls *Azure SQL Edge* die Umgebungsvariable `MSSQL_PACKAGE` hinzu, und geben Sie die SAS-URL der DACPAC-Datenbankdatei an, die Sie im [ersten Teil](tutorial-deploy-azure-resources.md) dieses Tutorials in Schritt 8 erstellt haben.

5. Klicken Sie auf **Update** (Aktualisieren).

6. Klicken Sie auf der Seite **Module für Gerät festlegen** auf **Weiter: Routen >** .

7. Geben Sie im Routenbereich der Seite **Module für Gerät festlegen** die Routen für die Kommunikation zwischen Modul und IoT Edge-Hub wie im Anschluss beschrieben an. Aktualisieren Sie dabei die Modulnamen in den unten angegebenen Routendefinitionen.

   ```
   FROM /messages/modules/<your_data_generator_module>/outputs/IronOreMeasures INTO
   BrokeredEndpoint("/modules/<your_azure_sql_edge_module>/inputs/IronOreMeasures")
   ```

   Beispiel:

   ```
   FROM /messages/modules/ASEDataGenerator/outputs/IronOreMeasures INTO BrokeredEndpoint("/modules/AzureSQLEdge/inputs/IronOreMeasures")
   ```


7. Klicken Sie auf der Seite **Module für Gerät festlegen** auf **Weiter: Überprüfen und erstellen >** .

8. Klicken Sie auf der Seite **Module für Gerät festlegen** auf **Erstellen**.

## <a name="create-and-start-the-t-sql-streaming-job-in-azure-sql-edge"></a>Erstellen Sie einen T-SQL-Streamingauftrag in Azure SQL Edge, und starten Sie ihn.

1. Öffnen Sie Azure Data Studio.

2. Starten Sie auf der Registerkarte **Willkommen** eine neue Verbindung mit den folgenden Details:

   |_Feld_|_Wert_|
   |-------|-------|
   |Verbindungstyp| Microsoft SQL Server|
   |Server|Öffentliche IP-Adresse in der VM, die für diese Demo erstellt wurde|
   |Username|sa|
   |Kennwort|Das starke Kennwort, das bei der Erstellung der Azure SQL Edge-Instanz verwendet wurde|
   |Datenbank|Standard|
   |Servergruppe|Standard|
   |Name (optional)|Geben Sie einen optionalen Namen an.|

3. Klicken Sie auf das Menü **Verbinden**

4. Öffnen Sie über die Menüregisterkarte **Datei** ein neues Notebook, oder drücken Sie STRG+N.

5. Führen Sie im Abfragefenster das unten bereitgestellte Skript aus, um den T-SQL-Streamingauftrag zu erstellen. Ändern Sie vor dem Ausführen des Skripts die folgenden Variablen: 
   - *SQL_SA_Password:* Der Wert „MSSQL_SA_PASSWORD“, der beim Bereitstellen des Azure SQL Edge-Moduls angegeben wurde. 
   
   ```sql
   Use IronOreSilicaPrediction
   Go

   Declare @SQL_SA_Password varchar(200) = '<SQL_SA_Password>'
   declare @query varchar(max) 

   /*
   Create Objects Required for Streaming
   */

   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'MyStr0ng3stP@ssw0rd';

   If NOT Exists (select name from sys.external_file_formats where name = 'JSONFormat')
   Begin
      CREATE EXTERNAL FILE FORMAT [JSONFormat]  
      WITH ( FORMAT_TYPE = JSON)
   End 


   If NOT Exists (select name from sys.external_data_sources where name = 'EdgeHub')
   Begin
      Create EXTERNAL DATA SOURCE [EdgeHub] 
      With(
         LOCATION = N'edgehub://'
      )
   End 

   If NOT Exists (select name from sys.external_streams where name = 'IronOreInput')
   Begin
      CREATE EXTERNAL STREAM IronOreInput WITH 
      (
         DATA_SOURCE = EdgeHub,
         FILE_FORMAT = JSONFormat,
         LOCATION = N'IronOreMeasures'
       )
   End


   If NOT Exists (select name from sys.database_scoped_credentials where name = 'SQLCredential')
   Begin
       set @query = 'CREATE DATABASE SCOPED CREDENTIAL SQLCredential
                 WITH IDENTITY = ''sa'', SECRET = ''' + @SQL_SA_Password + ''''
       Execute(@query)
   End 

   If NOT Exists (select name from sys.external_data_sources where name = 'LocalSQLOutput')
   Begin
      CREATE EXTERNAL DATA SOURCE LocalSQLOutput WITH (
      LOCATION = 'sqlserver://tcp:.,1433',CREDENTIAL = SQLCredential)
   End

   If NOT Exists (select name from sys.external_streams where name = 'IronOreOutput')
   Begin
      CREATE EXTERNAL STREAM IronOreOutput WITH 
      (
         DATA_SOURCE = LocalSQLOutput,
         LOCATION = N'IronOreSilicaPrediction.dbo.IronOreMeasurements'
      )
   End

   EXEC sys.sp_create_streaming_job @name=N'IronOreData',
   @statement= N'Select * INTO IronOreOutput from IronOreInput'

   exec sys.sp_start_streaming_job @name=N'IronOreData'
   ```

6. Vergewissern Sie sich mithilfe der folgenden Abfrage, dass die Daten aus dem Datengenerierungsmodul an die Datenbank gestreamt werden: 

   ```sql
   Select Top 10 * from dbo.IronOreMeasurements
   order by timestamp desc
   ```


In diesem Tutorial wurden das Datengenerierungsmodul und das SQL Edge-Modul bereitgestellt. Anschließend wurde ein Streamingauftrag erstellt, um die vom Datengenerierungsmodul generierten Daten an SQL zu streamen. 

## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen eines ML-Modells in Azure SQL Edge mithilfe von ONNX](tutorial-run-ml-model-on-sql-edge.md)