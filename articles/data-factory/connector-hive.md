---
title: Kopieren von Daten aus Hive mithilfe von Azure Data Factory
description: Erfahren Sie, wie Daten aus Hive mithilfe einer Kopieraktivität in eine Azure Data Factory-Pipeline in unterstützte Senkendatenspeicher kopiert werden.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: jingwang
ms.openlocfilehash: 8f6e85d82c01663e404f7046f84706feb209ba5a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100367026"
---
# <a name="copy-and-transform-data-from-hive-using-azure-data-factory"></a>Kopieren und Transformieren von Daten aus Hive mithilfe von Azure Data Factory 
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten aus Hive zu kopieren. Er baut auf dem Artikel zur [Übersicht über die Kopieraktivität](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Der Hive-Connector wird für die folgenden Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen/Senken-Matrix](copy-activity-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)

Sie können Daten aus Hive in beliebige unterstützte Senkendatenspeicher kopieren. Eine Liste der Datenspeicher, die als Quellen oder Senken für die Kopieraktivität unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory enthält einen integrierten Treiber zum Sicherstellen der Konnektivität. Daher müssen Sie mit diesem Connector keinen Treiber manuell installieren.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für den Hive-Connector verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den mit Hive verknüpften Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft muss auf Folgendes festgelegt werden: **Hive** | Ja |
| host | IP-Adresse oder Hostname des Hive-Servers, bei mehreren Hosts durch „;“ getrennt (nur wenn „serviceDiscoveryMode“ aktiviert ist).  | Ja |
| port | Der TCP-Port, den der Hive-Server verwendet, um auf Clientverbindungen zu lauschen. Geben Sie beim Herstellen einer Verbindung mit Azure HDInsights als Port 443 an. | Ja |
| serverType | Der Typ des Hive-Servers. <br/>Zulässige Werte sind: **HiveServer1**, **HiveServer2**, **HiveThriftServer** | Nein |
| thriftTransportProtocol | Das auf der Thrift-Ebene zu verwendende Transportprotokoll. <br/>Zulässige Werte sind: **Binary**, **SASL**, **HTTP** | Nein |
| authenticationType | Die Authentifizierungsmethode für den Zugriff auf den Hive-Server. <br/>Zulässige Werte sind: **Anonymous**, **Username**, **UsernameAndPassword** und **WindowsAzureHDInsightService**. Kerberos-Authentifizierung wird derzeit nicht unterstützt. | Ja |
| serviceDiscoveryMode | „true“, um das Verwenden des Diensts ZooKeeper anzugeben, andernfalls „false“.  | Nein |
| zooKeeperNameSpace | Der Namespace für ZooKeeper, unter dem Hive Server 2-Knoten hinzugefügt werden.  | Nein |
| useNativeQuery | Gibt an, ob der Treiber native HiveQL-Abfragen verwendet oder diese in eine äquivalente Form in HiveQL konvertiert.  | Nein |
| username | Der Benutzername für den Zugriff auf den Hive-Server.  | Nein |
| password | Das Kennwort für den Benutzer. Markieren Sie dieses Feld als SecureString, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). | Nein |
| httpPath | Die Teil-URL, die dem Hive-Server entspricht.  | Nein |
| enableSsl | Gibt an, ob Verbindungen mit dem Server mit TLS verschlüsselt werden. Der Standardwert ist „FALSE“.  | Nein |
| trustedCertPath | Der vollständige Pfad der PEM-Datei mit vertrauenswürdigen Zertifizierungsstellenzertifikaten zur Überprüfung des Servers beim Verbindungsaufbau über TLS. Diese Eigenschaft kann nur festgelegt werden, wenn TLS in einer selbstgehosteten IR verwendet wird. Der Standardwert ist die Datei „cacerts.pem“, die mit der IR installiert wird.  | Nein |
| useSystemTrustStore | Gibt an, ob ein Zertifizierungsstellenzertifikat aus dem Vertrauensspeicher des Systems oder aus einer angegebenen PEM-Datei verwendet werden soll. Der Standardwert ist „FALSE“.  | Nein |
| allowHostNameCNMismatch | Gibt an, ob der Name eines von der Zertifizierungsstelle ausgestellten TLS-/SSL-Zertifikats mit dem Hostnamen des Servers übereinstimmen muss, wenn eine Verbindung über TLS hergestellt wird. Der Standardwert ist „FALSE“.  | Nein |
| allowSelfSignedServerCert | Gibt an, ob vom Server selbstsignierte Zertifikate zugelassen werden. Der Standardwert ist „FALSE“.  | Nein |
| connectVia | Die [Integrationslaufzeit](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden muss. Weitere Informationen finden Sie im Abschnitt [Voraussetzungen](#prerequisites). Wenn keine Option angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. |Nein |
| storageReference | Ein Verweis auf den verknüpften Dienst des Speicherkontos, das für das Staging von Daten im Zuordnungsdatenfluss verwendet wird. Nur erforderlich, wenn im Zuordnungsdatenfluss der verknüpfte Hive-Dienst verwendet wird. | Nein |

**Beispiel:**

```json
{
    "name": "HiveLinkedService",
    "properties": {
        "type": "Hive",
        "typeProperties": {
            "host" : "<cluster>.azurehdinsight.net",
            "port" : "<port>",
            "authenticationType" : "WindowsAzureHDInsightService",
            "username" : "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset-Eigenschaften

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu [Datasets](concepts-datasets-linked-services.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die vom Hive-Dataset unterstützt werden.

Legen Sie zum Kopieren von Daten aus HTTP die „type“-Eigenschaft des Datasets auf **HiveObject** fest. Folgende Eigenschaften werden unterstützt:

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft des Datasets muss auf folgenden Wert festgelegt werden: **HiveObject** | Ja |
| schema | Name des Schemas. |Nein (wenn „query“ in der Aktivitätsquelle angegeben ist)  |
| table | Der Name der Tabelle. |Nein (wenn „query“ in der Aktivitätsquelle angegeben ist)  |
| tableName | Name der Tabelle einschließlich Schemateil. Diese Eigenschaft wird aus Gründen der Abwärtskompatibilität weiterhin unterstützt. Verwenden Sie für eine neue Workload `schema` und `table`. | Nein (wenn „query“ in der Aktivitätsquelle angegeben ist) |

**Beispiel**

```json
{
    "name": "HiveDataset",
    "properties": {
        "type": "HiveObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Hive linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der Hive-Quelle unterstützt werden.

### <a name="hivesource-as-source"></a>HiveSource als Quelle

Legen Sie zum Kopieren von Daten aus einem Hive den Quelltyp in der Kopieraktivität auf **HiveSource** fest. Folgende Eigenschaften werden im Abschnitt **source** der Kopieraktivität unterstützt:

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft der Quelle der Kopieraktivität muss auf Folgendes festgelegt werden: **HiveSource** | Ja |
| Abfrage | Verwendet die benutzerdefinierte SQL-Abfrage zum Lesen von Daten. Beispiel: `"SELECT * FROM MyTable"`. | Nein (wenn „tableName“ im Dataset angegeben ist) |

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromHive",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Hive input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "HiveSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>Eigenschaften von Mapping Data Flow

Der Hive-Connector wird als Quelle vom Typ [Inlinedataset](data-flow-source.md#inline-datasets) in Zuordnungsdatenflüssen unterstützt. Daten können mithilfe einer Abfrage oder direkt aus einer Hive-Tabelle in HDInsight gelesen werden. Hive-Daten werden in einem Speicherkonto als Parquet-Dateien gestaged, bevor sie im Rahmen eines Datenflusses transformiert werden. 

### <a name="source-properties"></a>Quelleigenschaften

Die folgende Tabelle enthält die von einer Hive-Quelle unterstützten Eigenschaften. Sie können diese Eigenschaften auf der Registerkarte **Quelloptionen** bearbeiten.

| Name | Beschreibung | Erforderlich | Zulässige Werte | Datenflussskript-Eigenschaft |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Speicher | Als Speicher muss `hive` verwendet werden. | ja |  `hive` | store | 
| Format | Gibt an, ob Daten aus einer Tabelle oder per Abfrage gelesen werden. | ja | `table` oder `query` | format |
| Schemaname | Das Schema der Quelltabelle (beim Lesen aus einer Tabelle). |  Ja, wenn das Format `table` lautet. | String | schemaName |
| Tabellenname | Der Tabellenname (beim Lesen aus einer Tabelle). |   Ja, wenn das Format `table` lautet. | String | tableName |
| Abfrage | Die Quellabfrage für den verknüpften Hive-Dienst, wenn das Format `query` lautet. | Ja, wenn das Format `query` lautet. | String | Abfrage |
| Gestaffelt | Die Hive-Tabelle wird immer in einem Stagingbereich bereitgestellt. | ja | `true` | staged |
| Speichercontainer | Speichercontainer, der vor dem Lesen aus bzw. dem Schreiben in Hive zum Stagen von Daten verwendet wird. Der Hive-Cluster muss Zugriff auf diesen Container haben. | ja | String | storageContainer |
| Stagingdatenbank | Das Schema bzw. die Datenbank, auf das bzw. auf die das im verknüpften Dienst angegebene Benutzerkonto Zugriff hat. Dient zum Erstellen externer Tabellen während des Stagings und wird hinterher gelöscht. | nein | `true` oder `false` | stagingDatabaseName |
| SQL-Skripts vor Vorgang | SQL-Code, der vor dem Lesen der Daten für die Hive-Tabelle ausgeführt werden soll. | nein | String | preSQLs |

#### <a name="source-example"></a>Quellbeispiel

Im Anschluss finden Sie ein Beispiel für eine Hive-Quellkonfiguration:

![Beispiel für Hive-Quelle](media/data-flow/hive-source.png "[Beispiel für Hive-Quelle")

Aus diesen Einstellungen ergibt sich das folgende Datenflussskript:

```
source(
    allowSchemaDrift: true,
    validateSchema: false,
    ignoreNoFilesFound: false,
    format: 'table',
    store: 'hive',
    schemaName: 'default',
    tableName: 'hivesampletable',
    staged: true,
    storageContainer: 'khive',
    storageFolderPath: '',
    stagingDatabaseName: 'default') ~> hivesource
```
### <a name="known-limitations"></a>Bekannte Einschränkungen

* Komplexe Typen wie Arrays, Zuordnungen, Strukturen und Unions werden für Lesevorgänge nicht unterstützt. 
* Der Hive-Connector unterstützt nur Hive-Tabellen in Azure HDInsight ab Version 4.0 (Apache Hive 3.1.0).

## <a name="lookup-activity-properties"></a>Eigenschaften der Lookup-Aktivität

Ausführliche Informationen zu den Eigenschaften finden Sie unter [Lookup-Aktivität](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Nächste Schritte
Eine Liste der Datenspeicher, die als Quellen und Senken für die Kopieraktivität in Azure Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).
