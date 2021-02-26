---
title: Transformieren von Daten mit dem U-SQL-Skript
description: Erläutert die Datenverarbeitung oder -transformation durch Ausführen von U-SQL-Skripts für einen Azure Data Lake Analytics-Computedienst.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/01/2018
ms.openlocfilehash: 94b584f0bea01754a4e955d1418a7a9a2e9aeace
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100373911"
---
# <a name="process-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Verarbeiten von Daten durch Ausführen von U-SQL-Skripts in Azure Data Lake Analytics 
> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](v1/data-factory-usql-activity.md)
> * [Aktuelle Version](transform-data-using-data-lake-analytics.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Eine Pipeline in einer Azure Data Factory verarbeitet Daten in verknüpften Speicherdiensten mithilfe verknüpfter Compute Services. Sie enthält eine Abfolge von Aktivitäten, wobei jede Aktivität einen bestimmten Verarbeitungsvorgang ausführt. In diesem Kapitel wird die **U-SQL-Aktivität von Data Lake Analytics** beschrieben, die ein **U-SQL**-Skript auf einem mit **Azure Data Lake Analytics** verknüpften Computedienst ausführt. 

Erstellen Sie ein Azure Data Lake Analytics-Konto, bevor Sie mit einer U-SQL-Aktivität von Data Lake Analytics eine Pipeline erstellen. Weitere Informationen zu Azure Data Lake Analytics finden Sie unter [Erste Schritte mit Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md).


## <a name="azure-data-lake-analytics-linked-service"></a>Mit Azure Data Lake Analytics verknüpfter Dienst
Sie erstellen einen mit **Azure Data Lake Analytics** verknüpften Dienst, um einen Azure Data Lake Analytics-Computedienst mit einer Azure Data Factory zu verknüpfen. Die Data Lake Analytics-U-SQL-Aktivität in der Pipeline verweist auf diesen verknüpften Dienst. 

Die folgende Tabelle enthält Beschreibungen der allgemeinen Eigenschaften, die in der JSON-Definition verwendet werden. 

| Eigenschaft                 | BESCHREIBUNG                              | Erforderlich                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| **type**                 | Legen Sie die type-Eigenschaft auf **AzureDataLakeAnalytics** fest. | Ja                                      |
| **accountName**          | Name des Azure Data Lake Analytics-Kontos.  | Ja                                      |
| **dataLakeAnalyticsUri** | URI des Azure Data Lake Analytics-Kontos.           | Nein                                       |
| **subscriptionId**       | Azure-Abonnement-ID                    | Nein                                       |
| **resourceGroupName**    | Azure-Ressourcengruppenname                | Nein                                       |

### <a name="service-principal-authentication"></a>Dienstprinzipalauthentifizierung
Der mit Azure Data Lake Analytics verknüpfte Dienst erfordert für die Verbindung mit dem Azure Data Lake Analytics-Dienst eine Dienstprinzipalauthentifizierung. Wenn Sie die Dienstprinzipalauthentifizierung verwenden möchten, registrieren Sie in Azure Active Directory (Azure AD) eine Anwendungsentität und gewähren ihr Zugriff auf Data Lake Analytics und den verwendeten Data Lake Store. Eine ausführliche Anleitung finden Sie unter [Dienst-zu-Dienst-Authentifizierung](../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md). Notieren Sie sich die folgenden Werte, die Sie zum Definieren des verknüpften Diensts verwenden:

* Anwendungs-ID
* Anwendungsschlüssel 
* Mandanten-ID

Verwenden Sie den [Assistenten für das Hinzufügen von Benutzern](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#add-a-new-user), um Azure Data Lake Analytics die Dienstprinzipalberechtigung zu erteilen.

Verwenden Sie die Dienstprinzipalauthentifizierung, indem Sie die folgenden Eigenschaften angeben:

| Eigenschaft                | BESCHREIBUNG                              | Erforderlich |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Geben Sie die Client-ID der Anwendung an.     | Ja      |
| **servicePrincipalKey** | Geben Sie den Schlüssel der Anwendung an.           | Ja      |
| **tenant**              | Geben Sie die Mandanteninformationen (Domänenname oder Mandanten-ID) für Ihre Anwendung an. Diese können Sie abrufen, indem Sie im Azure-Portal mit der Maus auf den Bereich oben rechts zeigen. | Ja      |

**Beispiel: Dienstprinzipalauthentifizierung**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "<account name>",
            "dataLakeAnalyticsUri": "<azure data lake analytics URI>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "value": "<service principal key>",
                "type": "SecureString"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }       
    }
}
```

Weitere Informationen zu diesem verknüpften Dienst finden Sie im Artikel [Von Azure Data Factory unterstützten Compute-Umgebungen](compute-linked-services.md).

## <a name="data-lake-analytics-u-sql-activity"></a>U-SQL-Aktivität für Data Lake Analytics
Der folgende JSON-Ausschnitt definiert eine Pipeline mit einer U-SQL-Aktivität für Data Lake Analytics. Die Aktivitätsdefinition verwendet einen Verweis auf den zuvor erstellten mit Azure Data Lake Analytics verknüpften Dienst. Um ein Data Lake Analytics-U-SQL-Skript auszuführen, übermittelt Data Factory das von Ihnen angegebene Skript an Data Lake Analytics. Die erforderlichen Ein- und Ausgaben werden im Skript definiert, damit Data Lake Analytics sie abruft und ausgibt. 

```json
{
    "name": "ADLA U-SQL Activity",
    "description": "description",
    "type": "DataLakeAnalyticsU-SQL",
    "linkedServiceName": {
        "referenceName": "<linked service name of Azure Data Lake Analytics>",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "<linked service name of Azure Data Lake Store or Azure Storage which contains the U-SQL script>",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
        "degreeOfParallelism": 3,
        "priority": 100,
        "parameters": {
            "in": "/datalake/input/SearchLog.tsv",
            "out": "/datalake/output/Result.tsv"
        }
    }   
}
```

Die folgende Tabelle beschreibt die Namen und Eigenschaften, die für diese Aktivität spezifisch sind. 

| Eigenschaft            | BESCHREIBUNG                              | Erforderlich |
| :------------------ | :--------------------------------------- | :------- |
| name                | Name der Aktivität in der Pipeline     | Ja      |
| description         | Ein Text, der beschreibt, was mit der Aktivität ausgeführt wird.  | Nein       |
| type                | Für die Data Lake Analytics-U-SQL-Aktivität ist der Aktivitätstyp **DataLakeAnalyticsU-SQL**. | Ja      |
| linkedServiceName   | Mit Azure Data Lake Analytics verknüpfter Dienst. Weitere Informationen zu diesem verknüpften Dienst finden Sie im Artikel [Von Azure Data Factory unterstützten Compute-Umgebungen](compute-linked-services.md).  |Ja       |
| scriptPath          | Der Pfad zum Ordner, der das U-SQL-Skript enthält. Beim Dateinamen wird Groß-/Kleinschreibung unterschieden. | Ja      |
| scriptLinkedService | Verknüpfter Dienst, der eine Verknüpfung mit der **Azure Data Lake Store**- oder **Azure Storage**-Instanz herstellt, die das Skript für die Data Factory enthält. | Ja      |
| degreeOfParallelism | Die maximale Anzahl von Knoten, die zum Ausführen des Auftrags gleichzeitig verwendet werden. | Nein       |
| priority            | Bestimmt, welche der in der Warteschlange befindlichen Aufträge als erstes ausgeführt werden. Je niedriger die Zahl, desto höher die Priorität. | Nein       |
| parameters          | Parameter, die an das U-SQL-Skript übergeben werden sollen.    | Nein       |
| runtimeVersion      | Die Runtimeversion der zu verwendenden U-SQL-Engine. | Nein       |
| compilationMode     | <p>Der Kompilierungsmodus von U-SQL. Muss einen der folgenden Werte aufweisen: **Semantic:** Es werden nur Semantiküberprüfungen und erforderliche Integritätsprüfungen durchgeführt. **Full:** Es wird die vollständige Kompilierung ausgeführt, einschließlich Syntaxprüfung, Optimierung, Codegenerierung usw. **SingleBox:** Es wird die vollständige Kompilierung durchgeführt, wobei die TargetType-Einstellung auf „SingleBox“ festgelegt ist. Wenn Sie für diese Eigenschaft keinen Wert angeben, bestimmt der Server den optimalen Kompilierungsmodus. | Nein |

Die Skriptdefinition finden Sie unter [SearchLogProcessing.txt](#sample-u-sql-script). 

## <a name="sample-u-sql-script"></a>U-SQL-Beispielskript

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int,
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

Im obigem Skriptbeispiel wird die Ein- und Ausgabe des Skripts in den Parametern **\@in** und **\@out** definiert. Die Werte für die Parameter **\@in** und **\@out** im U-SQL-Skript werden von Data Factory dynamisch mithilfe des Abschnitts „parameters“ übergeben. 

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
    "in": "/datalake/input/@{formatDateTime(pipeline().parameters.WindowStart,'yyyy/MM/dd')}/data.tsv",
    "out": "/datalake/output/@{formatDateTime(pipeline().parameters.WindowStart,'yyyy/MM/dd')}/result.tsv"
}
```

In diesem Fall werden die Eingabedateien weiterhin aus dem Ordner „/datalake/input“ abgerufen und die Ausgabedateien im Ordner „datalake/output“ generiert. Die Dateinamen sind dynamisch und basieren auf dem Start des Zeitfensters, der übergeben wird, wenn die Pipeline ausgelöst wird.  

## <a name="next-steps"></a>Nächste Schritte
In den folgenden Artikeln erfahren Sie, wie Daten auf andere Weisen transformiert werden: 

* [Hive-Aktivität](transform-data-using-hadoop-hive.md)
* [Pig-Aktivität](transform-data-using-hadoop-pig.md)
* [MapReduce-Aktivität](transform-data-using-hadoop-map-reduce.md)
* [Hadoop-Streamingaktivität](transform-data-using-hadoop-streaming.md)
* [Spark-Aktivität](transform-data-using-spark.md)
* [Benutzerdefinierte .NET-Aktivität](transform-data-using-dotnet-custom-activity.md)
* [Batchausführungsaktivität für Azure Machine Learning Studio (klassisch)](transform-data-using-machine-learning.md)
* [Aktivität „Gespeicherte Prozedur“](transform-data-using-stored-procedure.md)