---
title: Verschieben von Daten aus ODBC-Datenspeichern
description: Erfahren Sie, wie Sie Daten aus ODBC-Datenspeichern mithilfe von Azure Data Factory verschieben.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: ad70a598-c031-4339-a883-c6125403cb76
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/19/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 3c68b1f4d76a1899ce473c57f3a6d5de1eab71c6
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92636866"
---
# <a name="move-data-from-odbc-data-stores-using-azure-data-factory"></a>Verschieben von Daten aus ODBC-Datenspeichern mithilfe von Azure Data Factory
> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](data-factory-odbc-connector.md)
> * [Version 2 (aktuelle Version)](../connector-odbc.md)

> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, finden Sie weitere Informationen unter [ODBC-Connector in V2](../connector-odbc.md).


Dieser Artikel beschreibt, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten aus einem lokalen ODBC-Datenspeicher zu verschieben. Dieser Artikel baut auf dem Artikel zu [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) auf, der eine allgemeine Übersicht zur Datenverschiebung mit der Kopieraktivität bietet.

Sie können Daten aus einem ODBC-Datenspeicher in beliebige unterstützte Senkendatenspeicher kopieren. Eine Liste der Datenspeicher, die als Senken für die Kopieraktivität unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Data Factory unterstützt derzeit nur das Verschieben von Daten aus einem ODBC-Datenspeicher in andere Datenspeicher, aber nicht das Verschieben von Daten aus anderen Datenspeichern in einen ODBC-Datenspeicher.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="enabling-connectivity"></a>Herstellen der Verbindung
Der Data Factory-Dienst unterstützt das Herstellen einer Verbindung mit lokalen ODBC-Datenquellen über das Datenverwaltungsgateway. Im Artikel [Verschieben von Daten zwischen lokalen Standorten und Cloud](data-factory-move-data-between-onprem-and-cloud.md) erfahren mehr zum Datenverwaltungsgateway und erhalten eine schrittweise Anleitung zum Einrichten des Gateways. Nutzen Sie das Gateway, um eine Verbindung mit einem ODBC-Datenspeicher herzustellen, auch wenn der Datenspeicher auf einer Azure IaaS-VM gehostet wird.

Sie können das Gateway auf dem gleichen lokalen Computer oder der Azure-VM als ODBC-Datenspeicher installieren. Allerdings sollten Sie das Gateway auf einem separaten Computer/einer separaten Azure IaaS-VM installieren, um Ressourcenkonflikte zu vermeiden und die Leistung zu steigern. Wenn Sie das Gateway auf einem separaten Computer installieren, muss dieser Computer auf den Computer mit dem ODBC-Datenspeicher zugreifen können.

Neben dem Datenverwaltungsgateway müssen Sie auch den ODBC-Treiber für den Datenspeicher auf dem Gatewaycomputer installieren.

> [!NOTE]
> Unter [Problembehandlung bei Gateways](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) finden Sie Tipps zur Behandlung von Verbindungs- bzw. Gatewayproblemen.

## <a name="getting-started"></a>Erste Schritte
Sie können eine Pipeline mit einer Kopieraktivität erstellen, die Daten mithilfe verschiedener Tools/APIs aus einem ODBC-Datenspeicher verschiebt.

Am einfachsten erstellen Sie eine Pipeline mit dem **Kopier-Assistenten** . Siehe [Tutorial: Erstellen einer Pipeline mit dem Kopier-Assistenten](data-factory-copy-data-wizard-tutorial.md) finden Sie eine kurze exemplarische Vorgehensweise zum Erstellen einer Pipeline mithilfe des Assistenten zum Kopieren von Daten.

Sie können auch die folgenden Tools zum Erstellen einer Pipeline verwenden: **Visual Studio** , **Azure PowerShell** , **Azure Resource Manager-Vorlage** , **.NET-API** und **REST-API** . Im [Tutorial zur Kopieraktivität](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) finden Sie detaillierte Anweisungen, wie Sie eine Pipeline mit einer Kopieraktivität erstellen können.

Unabhängig davon, ob Sie Tools oder APIs verwenden, führen Sie die folgenden Schritte aus, um eine Pipeline zu erstellen, die Daten aus einem Quelldatenspeicher in einen Senkendatenspeicher verschiebt:

1. Erstellen **verknüpfter Dienste** zum Verknüpfen von Eingabe- und Ausgabedatenspeichern mit Ihrer Data Factory.
2. Erstellen von **Datasets** zur Darstellung von Eingabe- und Ausgabedaten für den Kopiervorgang.
3. Erstellen einer **Pipeline** mit einer Kopieraktivität, die ein Dataset als Eingabe und ein Dataset als Ausgabe akzeptiert.

Wenn Sie den Assistenten verwenden, werden automatisch JSON-Definitionen für diese Data Factory-Entitäten (verknüpfte Diensten, Datasets und die Pipeline) erstellt. Bei Verwendung von Tools und APIs (mit Ausnahme der .NET-API) definieren Sie diese Data Factory-Entitäten im JSON-Format. Ein Beispiel mit JSON-Definitionen für Data Factory-Entitäten, die zum Kopieren von Daten aus einem ODBC-Datenspeicher verwendet werden, finden Sie im Abschnitt [JSON-Beispiel: Kopieren von Daten aus dem ODBC-Datenspeicher in ein Azure-Blob](#json-example-copy-data-from-odbc-data-store-to-azure-blob) in diesem Artikel.

Die folgenden Abschnitte enthalten Details zu JSON-Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für ODBC-Datenspeicher verwendet werden:

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts
Die folgende Tabelle enthält eine Beschreibung der JSON-Elemente, die für den mit ODBC verknüpften Dienst spezifisch sind.

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| type |Die type-Eigenschaft muss auf Folgendes festgelegt werden: **OnPremisesOdbc** . |Ja |
| connectionString |Der nicht für den Zugriff bestimmte Teil der Anmeldeinformationen in der Verbindungszeichenfolge sowie optional verschlüsselte Anmeldeinformationen. Siehe Beispiele in den folgenden Abschnitten. <br/><br/>Sie können die Verbindungszeichenfolge mit einem Muster wie `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"` angeben oder den System-DSN (Data Source Name) verwenden, den Sie mit `"DSN=<name of the DSN>;"` auf dem Gatewaycomputer eingerichtet haben. (Sie müssen nach wie vor den Teil mit den Anmeldeinformationen im verknüpften Dienst entsprechend angeben.) |Ja |
| Anmeldeinformationen (credential) |Der zum Zugriff bestimmte Teil der Anmeldeinformationen in der Verbindungszeichenfolge. Er wird in einem treiberspezifischen Format in Eigenschaft und Wert angegeben. Beispiel: `"Uid=<user ID>;Pwd=<password>;RefreshToken=<secret refresh token>;"`. |Nein |
| authenticationType |Typ der Authentifizierung für die Verbindung mit dem ODBC-Datenspeicher. Mögliche Werte: „Anonymous“ und „Basic“. |Ja |
| userName |Geben Sie den Benutzernamen an, wenn Sie die Standardauthentifizierung verwenden. |Nein |
| password |Geben Sie das Kennwort für das Benutzerkonto an, das Sie für „userName“ angegeben haben. |Nein |
| gatewayName |Der Name des Gateways, das der Data Factory-Dienst zum Verbinden mit dem ODBC-Datenspeicher verwenden soll. |Ja |

### <a name="using-basic-authentication"></a>Verwenden der Standardauthentifizierung

```json
{
    "name": "odbc",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
            "userName": "username",
            "password": "password",
            "gatewayName": "mygateway"
        }
    }
}
```
### <a name="using-basic-authentication-with-encrypted-credentials"></a>Verwenden der Standardauthentifizierung mit verschlüsselten Anmeldeinformationen
Sie können die Anmeldeinformationen mithilfe des Cmdlets [New-AzDataFactoryEncryptValue](/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) (Version 1.0 von Azure PowerShell) oder [New-AzureDataFactoryEncryptValue](/previous-versions/azure/dn834940(v=azure.100)) (Version 0.9 von Azure PowerShell oder früher) verschlüsseln.

```json
{
    "name": "odbc",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=myserver.database.windows.net; Database=TestDatabase;;EncryptedCredential=eyJDb25uZWN0...........................",
            "gatewayName": "mygateway"
        }
    }
}
```

### <a name="using-anonymous-authentication"></a>Verwenden der anonymen Authentifizierung

```json
{
    "name": "odbc",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "connectionString": "Driver={SQL Server};Server={servername}.database.windows.net; Database=TestDatabase;",
            "credential": "UID={uid};PWD={pwd}",
            "gatewayName": "mygateway"
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset-Eigenschaften
Eine vollständige Liste der Abschnitte und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel [Erstellen von Datasets](data-factory-create-datasets.md). Abschnitte wie „structure“, „availability“ und „policy“ des JSON-Codes eines Datasets sind bei allen Dataset-Typen (Azure SQL, Azure-Blob, Azure-Tabelle usw.) ähnlich.

Der Abschnitt **typeProperties** unterscheidet sich bei jedem Typ von Dataset und bietet Informationen zum Speicherort der Daten im Datenspeicher. Der Abschnitt „typeProperties“ für ein Dataset vom Typ **RelationalTable** (mit ODBC-Dataset) hat folgende Eigenschaften:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| tableName |Der Name der Tabelle im ODBC-Datenspeicher. |Ja |

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität
Eine vollständige Liste der Abschnitte und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Erstellen von Pipelines](data-factory-create-pipelines.md). Eigenschaften wie Name, Beschreibung, Eingabe- und Ausgabetabellen und Richtlinien sind für alle Arten von Aktivitäten verfügbar.

Eigenschaften im Abschnitt **typeProperties** der Aktivität können dagegen je nach Aktivitätstyp variieren. Für die Kopieraktivität variieren die Eigenschaften je nach Art der Quellen und Senken.

Wenn bei der Kopieraktivität eine Quelle vom Typ **RelationalSource** (mit ODBC) verwendet wird, sind im Abschnitt typeProperties folgende Eigenschaften verfügbar:

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| Abfrage |Verwendet die benutzerdefinierte Abfrage zum Lesen von Daten. |SQL-Abfragezeichenfolge. Beispiel: select * from MyTable. |Ja |


## <a name="json-example-copy-data-from-odbc-data-store-to-azure-blob"></a>JSON-Beispiel: Kopieren von Daten aus dem ODBC-Datenspeicher in ein Azure-Blob
Dieses Beispiel stellt JSON-Definitionen bereit, die Sie zum Erstellen einer Pipeline mit [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) oder mit [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) verwenden können. Darin wird veranschaulicht, wie Sie Daten aus einer ODBC-Quelle in Azure Blob Storage kopieren. Daten können jedoch auch mithilfe der Kopieraktivität in Azure Data Factory in eine beliebige der [hier](data-factory-data-movement-activities.md#supported-data-stores-and-formats) aufgeführten Senken kopiert werden.

Das Beispiel enthält die folgenden Data Factory-Entitäten:

1. Einen verknüpften Dienst des Typs [OnPremisesOdbc](#linked-service-properties)
2. Einen verknüpften Dienst des Typs [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
3. Ein [Eingabedataset](data-factory-create-datasets.md) des Typs [RelationalTable](#dataset-properties)
4. Ein [Ausgabedataset](data-factory-create-datasets.md) des Typs [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
5. Eine [Pipeline](data-factory-create-pipelines.md) mit Kopieraktivität, die [RelationalSource](#copy-activity-properties) und [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) verwendet

Das Beispiel kopiert stündlich Daten aus einem Abfrageergebnis in einem ODBC-Datenspeicher in ein Blob. Die bei diesen Beispielen verwendeten JSON-Eigenschaften werden in den Abschnitten beschrieben, die auf die Beispiele folgen.

Als Erstes richten Sie das Datenverwaltungsgateway ein. Anweisungen dazu finden Sie im Artikel [Verschieben von Daten zwischen lokalen Standorten und Cloud](data-factory-move-data-between-onprem-and-cloud.md) .

**Mit ODBC verknüpfter Dienst** . Bei diesem Beispiel wird die Standardauthentifizierung verwendet. Informationen zu den verwendbaren Authentifizierungstypen finden Sie im Abschnitt [Mit ODBC verknüpfter Dienst](#linked-service-properties).

```json
{
    "name": "OnPremOdbcLinkedService",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
            "userName": "username",
            "password": "password",
            "gatewayName": "mygateway"
        }
    }
}
```

**Mit Azure Storage verknüpfter Dienst**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

**ODBC-Eingabedataset**

Im Beispiel wird vorausgesetzt, dass Sie die Tabelle „MyTable“ in einer ODBC-Datenbank erstellt haben, die für Zeitreihendaten eine Spalte namens „timestampcolumn“ enthält.

Durch Festlegen von „external“ auf „true“ wird dem Data Factory-Dienst mitgeteilt, dass das Dataset für die Data Factory extern ist und nicht durch eine Aktivität in der Data Factory erzeugt wird.

```json
{
    "name": "ODBCDataSet",
    "properties": {
        "published": false,
        "type": "RelationalTable",
        "linkedServiceName": "OnPremOdbcLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

**Azure-Blob-Ausgabedataset**

Daten werden stündlich in ein neues Blob geschrieben (frequency: hour, interval: 1). Der Ordnerpfad des Blobs wird basierend auf der Startzeit des Slices, der verarbeitet wird, dynamisch ausgewertet. Im Ordnerpfad werden Jahr, Monat, Tag und die Stundenteile der Startzeit verwendet.

```json
{
    "name": "AzureBlobOdbcDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/odbc/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Kopieraktivität in einer Pipeline mit ODBC-Quelle (RelationalSource) und Blobsenke (BlobSink):**

Die Pipeline enthält eine Kopieraktivität, die für das Verwenden der Ein- und Ausgabedatasets und für eine stündliche Ausführung konfiguriert ist. In der JSON-Definition der Pipeline ist der Typ **source** auf **RelationalSource** und der Typ **sink** auf **BlobSink** festgelegt. Die für die **query** -Eigenschaft angegebene SQL-Abfrage wählt die aus der letzten Stunde zu kopierenden Daten aus.

```json
{
    "name": "CopyODBCToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "OdbcDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOdbcDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "OdbcToBlob"
            }
        ],
        "start": "2016-06-01T18:00:00Z",
        "end": "2016-06-01T19:00:00Z"
    }
}
```
### <a name="type-mapping-for-odbc"></a>Typzuordnung für ODBC
Wie im Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) beschrieben, führt die Kopieraktivität automatische Typkonvertierungen von Quelltypen in Senkentypen mithilfe des folgenden aus zwei Schritten bestehenden Ansatzes durch:

1. Konvertieren von systemeigenen Quelltypen in den .NET-Typ
2. Konvertieren vom .NET-Typ in systemeigenen Senkentyp

Beim Verschieben von Daten aus ODBC-Datenspeichern werden die ODBC-Datentypen den .NET-Typen zugeordnet, wie im Thema [ODBC-Datentypmappings](/dotnet/framework/data/adonet/odbc-data-type-mappings) beschrieben.

## <a name="map-source-to-sink-columns"></a>Zuordnen von Quell- zur Senkenspalten
Weitere Informationen zum Zuordnen von Spalten im Quelldataset zu Spalten im Senkendataset finden Sie unter [Zuordnen von Datasetspalten in Azure Data Factory](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Wiederholbare Lesevorgänge aus relationalen Quellen
Beim Kopieren von Daten aus relationalen Datenspeichern müssen Sie die Wiederholbarkeit berücksichtigen, um unbeabsichtigte Ergebnisse zu vermeiden. Sie können einen Slice in Azure Data Factory manuell erneut ausführen. Sie können auch eine Wiederholungsrichtlinie für ein Dataset konfigurieren, sodass ein Slice erneut ausgeführt wird, wenn ein Fehler auftritt. Wenn ein Slice erneut ausgeführt wird, müssen Sie sicherstellen, dass dieselben Daten gelesen werden – egal wie oft ein Slice ausgeführt wird. Weitere Informationen finden Sie unter [Wiederholbare Lesevorgänge aus relationalen Quellen](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="troubleshoot-connectivity-issues"></a>Behandeln von Konnektivitätsproblemen
Um Verbindungsprobleme zu behandeln, verwenden Sie die Registerkarte **Diagnose** im **Datenverwaltungsgateway-Konfigurations-Manager** .

1. Starten Sie den **Datenverwaltungsgateway-Konfigurations-Manager** . Sie können entweder „C:\Programme\Microsoft Data Management Gateway\1.0\Shared\ConfigManager.exe“ direkt ausführen oder nach **Gateway** suchen, um einen Link zur Anwendung **Microsoft-Datenverwaltungsgateway** zu finden, wie in der folgenden Abbildung dargestellt:

    ![Gateway suchen](./media/data-factory-odbc-connector/search-gateway.png)
2. Wechseln Sie zur Registerkarte **Diagnose** .

    ![Gatewaydiagnose](./media/data-factory-odbc-connector/data-factory-gateway-diagnostics.png)
3. Wählen Sie den **Typ** von Datenspeicher (verknüpfter Dienst) aus.
4. Geben Sie die **Authentifizierung** an, und geben Sie **Anmeldeinformationen** oder die **Verbindungszeichenfolge** ein, um eine Verbindung mit dem Datenspeicher herzustellen.
5. Klicken Sie auf **Verbindung testen** , um die Verbindung mit dem Datenspeicher zu testen.

## <a name="performance-and-tuning"></a>Leistung und Optimierung
Der Artikel [Handbuch zur Leistung und Optimierung der Kopieraktivität](data-factory-copy-activity-performance.md) beschreibt wichtige Faktoren, die sich auf die Leistung der Datenverschiebung (Kopieraktivität) in Azure Data Factory auswirken, sowie verschiedene Möglichkeiten zur Leistungsoptimierung.