---
title: 'Transformieren von Daten mit dem U-SQL-Skript: Azure'
description: Hier wird die Datenverarbeitung oder -transformation durch Ausführen von U-SQL-Skripts für einen Azure Data Lake Analytics-Computedienst (Version 1) erläutert.
services: data-factory
documentationcenter: ''
ms.assetid: e17c1255-62c2-4e2e-bb60-d25274903e80
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/01/2017
author: nabhishek
ms.author: abnarain
ms.custom: devx-track-csharp
manager: anandsub
robots: noindex
ms.openlocfilehash: a5e53cab30f1adca05652a3b3b7541e12ebebbdb
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92631460"
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Transformieren von Daten durch Ausführen von U-SQL-Skripts für Azure Data Lake Analytics 
> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](data-factory-usql-activity.md)
> * [Version 2 (aktuelle Version)](../transform-data-using-data-lake-analytics.md)

> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, finden Sie weitere Informationen unter [U-SQL-Aktivität in V2](../transform-data-using-data-lake-analytics.md).

Eine Pipeline in einer Azure Data Factory verarbeitet Daten in verknüpften Speicherdiensten mithilfe verknüpfter Compute Services. Sie enthält eine Abfolge von Aktivitäten, wobei jede Aktivität einen bestimmten Verarbeitungsvorgang ausführt. In diesem Kapitel wird die **U-SQL-Aktivität von Data Lake Analytics** beschrieben, die ein **U-SQL** -Skript auf einem mit **Azure Data Lake Analytics** verknüpften Computedienst ausführt. 

Erstellen Sie ein Azure Data Lake Analytics-Konto, bevor Sie mit einer U-SQL-Aktivität von Data Lake Analytics eine Pipeline erstellen. Weitere Informationen zu Azure Data Lake Analytics finden Sie unter [Erste Schritte mit Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-get-started-portal.md).

Im Tutorial [Erstellen Ihrer ersten Pipeline](data-factory-build-your-first-pipeline.md) finden Sie ausführliche Anweisungen zum Erstellen von Data Factorys, verknüpften Diensten, Datasets und Pipelines. Verwenden Sie JSON-Codeausschnitte mit Data Factory-Editor, Visual Studio oder Azure PowerShell, um Data Factory-Entitäten zu erstellen.

## <a name="supported-authentication-types"></a>Unterstützte Authentifizierungstypen
Die U-SQL-Aktivität unterstützt die unten angegebenen Authentifizierungstypen für Data Lake Analytics:
* Dienstprinzipalauthentifizierung
* Authentifizierung von Benutzeranmeldeinformationen (OAuth) 

Wir empfehlen Ihnen, die Dienstprinzipalauthentifizierung zu verwenden. Dies gilt besonders für eine geplante U-SQL-Ausführung. Bei der Authentifizierung der Benutzeranmeldeinformationen kann es zu einem Ablauf von Token kommen. Informationen zu den Konfigurationsdetails finden Sie im Abschnitt [Eigenschaften des verknüpften Diensts](#azure-data-lake-analytics-linked-service).

## <a name="azure-data-lake-analytics-linked-service"></a>Mit Azure Data Lake Analytics verknüpfter Dienst
Sie erstellen einen mit **Azure Data Lake Analytics** verknüpften Dienst, um einen Azure Data Lake Analytics-Computedienst mit einer Azure Data Factory zu verknüpfen. Die Data Lake Analytics-U-SQL-Aktivität in der Pipeline verweist auf diesen verknüpften Dienst. 

Die folgende Tabelle enthält Beschreibungen der allgemeinen Eigenschaften, die in der JSON-Definition verwendet werden. Sie können überdies zwischen der Authentifizierung per Dienstprinzipal und per Benutzeranmeldeinformationen wählen.

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| **type** |Legen Sie die Typeigenschaft auf **AzureDataLakeAnalytics** fest. |Ja |
| **accountName** |Name des Azure Data Lake Analytics-Kontos. |Ja |
| **dataLakeAnalyticsUri** |URI des Azure Data Lake Analytics-Kontos. |Nein |
| **subscriptionId** |Azure-Abonnement-ID |Nein (falls nicht angegeben, wird das Abonnement der Data Factory verwendet). |
| **resourceGroupName** |Azure-Ressourcengruppenname |Nein (falls nicht angegeben, wird die Ressourcengruppe der Data Factory verwendet). |

### <a name="service-principal-authentication-recommended"></a>Dienstprinzipalauthentifizierung (empfohlen)
Wenn Sie die Dienstprinzipalauthentifizierung verwenden möchten, registrieren Sie in Azure Active Directory (Azure AD) eine Anwendungsentität und gewähren ihr Zugriff auf Data Lake Store. Eine ausführliche Anleitung finden Sie unter [Dienst-zu-Dienst-Authentifizierung](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md). Notieren Sie sich die folgenden Werte, die Sie zum Definieren des verknüpften Diensts verwenden:
* Anwendungs-ID
* Anwendungsschlüssel 
* Mandanten-ID

Verwenden Sie die Dienstprinzipalauthentifizierung, indem Sie die folgenden Eigenschaften angeben:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| **servicePrincipalId** | Geben Sie die Client-ID der Anwendung an. | Ja |
| **servicePrincipalKey** | Geben Sie den Schlüssel der Anwendung an. | Ja |
| **tenant** | Geben Sie die Mandanteninformationen (Domänenname oder Mandanten-ID) für Ihre Anwendung an. Diese können Sie abrufen, indem Sie im Azure-Portal mit der Maus auf den Bereich oben rechts zeigen. | Ja |

**Beispiel: Dienstprinzipalauthentifizierung**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics.net",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

### <a name="user-credential-authentication"></a>Authentifizierung mit Benutzeranmeldeinformationen
Alternativ können Sie die Authentifizierung mit Benutzeranmeldeinformationen für Data Lake Analytics verwenden, indem Sie die folgenden Eigenschaften angeben:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| **Autorisierung** | Klicken Sie im Data Factory-Editor auf die Schaltfläche **Autorisieren** , und geben Sie Ihre Anmeldeinformationen ein. Hierdurch wird die automatisch generierte Autorisierungs-URL dieser Eigenschaft zugewiesen. | Ja |
| **sessionId** | OAuth-Sitzungs-ID aus der OAuth-Autorisierungssitzung. Jede Sitzungs-ID ist eindeutig und darf nur einmal verwendet werden. Diese Einstellung wird automatisch generiert, wenn Sie den Data Factory-Editor verwenden. | Ja |

**Beispiel: Authentifizierung mit Benutzeranmeldeinformationen**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>", 
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="token-expiration"></a>Tokenablauf
Der von Ihnen mithilfe der Schaltfläche **Autorisieren** generierte Autorisierungscode läuft nach einer gewissen Zeit ab. Die Zeiten bis zum Ablaufen der Autorisierungscodes für die verschiedenen Benutzerkonten finden Sie in der folgenden Tabelle. Unter Umständen wird Ihnen nach dem **Ablauf des Tokens** folgende Fehlermeldung angezeigt: „Fehler beim Anmeldevorgang: invalid_grant – AADSTS70002: Fehler beim Überprüfen der Anmeldeinformationen. AADSTS70008: Die angegebene Zugriffserteilung ist abgelaufen oder wurde widerrufen. Ablaufverfolgungs-ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 Korrelations-ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Zeitstempel: 2015-12-15 21:09:31Z“

| Benutzertyp | Läuft ab nach |
|:--- |:--- |
| Benutzerkonten, die NICHT von Azure Active Directory verwaltet werden (@hotmail.com, @live.com usw.) |12 Stunden |
| Benutzerkonten, die von Azure Active Directory (AAD) verwaltet werden |14 Tage nach der letzten Sliceausführung. <br/><br/>90 Tage, wenn ein Slice, das auf einem verknüpften OAuth-Dienst basiert, mindestens einmal alle 14 Tage ausgeführt wird. |

Um diesen Fehler zu vermeiden oder zu beheben, autorisieren Sie sich durch Klicken auf die Schaltfläche **Autorisieren** erneut, wenn das **Token abläuft** , und stellen den verknüpften Dienst anschließend erneut bereit. Sie können auch Werte für die Eigenschaften **sessionId** und **authorization** programmgesteuert mit folgendem Code generieren:

```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```

Nähere Informationen zu den im Code verwendeten Data Factory-Klassen finden Sie in den Themen [AzureDataLakeStoreLinkedService-Klasse](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice), [AzureDataLakeAnalyticsLinkedService-Klasse](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice) und [AuthorizationSessionGetResponse-Klasse](/dotnet/api/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse). Fügen Sie für die WindowsFormsWebAuthenticationDialog-Klasse einen Verweis auf Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll hinzu. 

## <a name="data-lake-analytics-u-sql-activity"></a>U-SQL-Aktivität für Data Lake Analytics
Der folgende JSON-Ausschnitt definiert eine Pipeline mit einer U-SQL-Aktivität für Data Lake Analytics. Die Aktivitätsdefinition verwendet einen Verweis auf den zuvor erstellten mit Azure Data Lake Analytics verknüpften Dienst.   

```json
{
    "name": "ComputeEventsByRegionPipeline",
    "properties": {
        "description": "This is a pipeline to compute events for en-gb locale and date less than 2012/02/19.",
        "activities": 
        [
            {
                "type": "DataLakeAnalyticsU-SQL",
                "typeProperties": {
                    "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
                    "scriptLinkedService": "StorageLinkedService",
                    "degreeOfParallelism": 3,
                    "priority": 100,
                    "parameters": {
                        "in": "/datalake/input/SearchLog.tsv",
                        "out": "/datalake/output/Result.tsv"
                    }
                },
                "inputs": [
                    {
                        "name": "DataLakeTable"
                    }
                ],
                "outputs": 
                [
                    {
                        "name": "EventsByRegionTable"
                    }
                ],
                "policy": {
                    "timeout": "06:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "EventsByRegion",
                "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
            }
        ],
        "start": "2015-08-08T00:00:00Z",
        "end": "2015-08-08T01:00:00Z",
        "isPaused": false
    }
}
```

Die folgende Tabelle beschreibt die Namen und Eigenschaften, die für diese Aktivität spezifisch sind. 

| Eigenschaft            | BESCHREIBUNG                              | Erforderlich                                 |
| :------------------ | :--------------------------------------- | :--------------------------------------- |
| type                | Die type-Eigenschaft muss auf **DataLakeAnalyticsU-SQL** festgelegt werden. | Ja                                      |
| linkedServiceName   | Verweis auf den Azure Data Lake Analytics-Dienst, der als verknüpfter Dienst in Data Factory registriert ist | Ja                                      |
| scriptPath          | Der Pfad zum Ordner, der das U-SQL-Skript enthält. Beim Dateinamen wird Groß-/Kleinschreibung unterschieden. | Nein (wenn script verwendet wird)                   |
| scriptLinkedService | Verknüpfter Dienst, der den Speicher, der das Skript enthält, mit der Data Factory verknüpft. | Nein (wenn script verwendet wird)                   |
| script              | Geben Sie anstelle von scriptPath und scriptLinkedService ein Inlineskript an. Beispiel: `"script": "CREATE DATABASE test"`. | Nein (wenn scriptPath and scriptLinkedService verwendet werden) |
| degreeOfParallelism | Die maximale Anzahl von Knoten, die zum Ausführen des Auftrags gleichzeitig verwendet werden. | Nein                                       |
| priority            | Bestimmt, welche der in der Warteschlange befindlichen Aufträge als erstes ausgeführt werden. Je niedriger die Zahl, desto höher die Priorität. | Nein                                       |
| parameters          | Parameter für das U-SQL-Skript          | Nein                                       |
| runtimeVersion      | Die Runtime-Version der zu verwendenden U-SQL-Engine | Nein                                       |
| compilationMode     | <p>Der Kompilierungsmodus von U-SQL. Muss einen der folgenden Werte aufweisen:</p> <ul><li>**Semantic:** Es werden nur Semantiküberprüfungen und erforderliche Integritätsprüfungen ausgeführt.</li><li>**Full:** Es wird die vollständige Kompilierung ausgeführt, einschließlich Syntaxprüfung, Optimierung, Codegenerierung usw.</li><li>**SingleBox:** Es wird die vollständige Kompilierung ausgeführt, wobei die „TargetType“-Einstellung auf „SingleBox“ festgelegt ist.</li></ul><p>Wenn Sie für diese Eigenschaft keinen Wert angeben, bestimmt der Server den optimalen Kompilierungsmodus. </p> | Nein                                       |

Die Skriptdefinition finden Sie unter [Skriptdefinition „SearchLogProcessing.txt“](#sample-u-sql-script) . 

## <a name="sample-input-and-output-datasets"></a>Eingabe- und Ausgabedatasets als Beispiel
### <a name="input-dataset"></a>Eingabedataset
In diesem Beispiel befinden sich die Eingabedaten in einem Azure Data Lake-Speicher (Datei SearchLog.tsv im Ordner datalake/input). 

```json
{
    "name": "DataLakeTable",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}    
```

### <a name="output-dataset"></a>Ausgabedataset
In diesem Beispiel werden die von dem U-SQL-Skript erzeugten Ausgabedaten in einem Azure Data Lake-Speicher abgelegt (im Ordner „datalake/output“). 

```json
{
    "name": "EventsByRegionTable",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/output/"
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="sample-data-lake-store-linked-service"></a>Beispiel für einen mit Azure Data Lake Store verknüpften Dienst
Hier finden Sie die Definition des mit Azure Data Lake Store verknüpften Beispieldiensts, der von den Eingabe-/Ausgabedatasets verwendet wird. 

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
        }
    }
}
```

Im Artikel [Verschieben von Daten in und aus Azure Data Lake Store mithilfe von Azure Data Factory](data-factory-azure-datalake-connector.md) finden Sie Beschreibungen der JSON-Eigenschaften. 

## <a name="sample-u-sql-script"></a>Beispiel-U-SQL-Skript

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM @in
    USING Extractors.Tsv(nullEscape:"#NULL#");

@rs1 =
    SELECT Start, Region, Duration
    FROM @searchlog
WHERE Region == "en-gb";

@rs1 =
    SELECT Start, Region, Duration
    FROM @rs1
    WHERE Start <= DateTime.Parse("2012/02/19");

OUTPUT @rs1   
    TO @out
      USING Outputters.Tsv(quoting:false, dateTimeFormat:null);
```

Die Werte für die Parameter **\@in** und **\@out** im U-SQL-Skript werden von ADF dynamisch mithilfe des Abschnitts „parameters“ übergeben. Informationen finden Sie in der Pipelinedefinition im Abschnitt „parameters“.

Sie können in Ihrer Pipelinedefinition auch andere Eigenschaften wie etwa degreeOfParallelism oder „priority“ für die Aufträge angeben, die im Azure Data Lake Analytics-Dienst ausgeführt werden.

## <a name="dynamic-parameters"></a>Dynamische Parameter
In der beispielhaften Pipelinedefinition werden die Eingabe- und Ausgabeparameter mit hartcodierten Werten zugewiesen. 

```json
"parameters": {
    "in": "/datalake/input/SearchLog.tsv",
    "out": "/datalake/output/Result.tsv"
}
```

Anstelle von hartcodierten Werten können dynamische Parameter verwendet werden. Beispiel: 

```json
"parameters": {
    "in": "$$Text.Format('/datalake/input/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)",
    "out": "$$Text.Format('/datalake/output/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)"
}
```

In diesem Fall werden die Eingabedateien weiterhin aus dem Ordner „/datalake/input“ abgerufen und die Ausgabedateien im Ordner „datalake/output“ generiert. Die Dateinamen sind dynamisch und basieren auf der Startzeit des Slices.