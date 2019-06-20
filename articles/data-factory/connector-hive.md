---
title: Kopieren von Daten aus Hive mithilfe von Azure Data Factory | Microsoft-Dokumentation
description: Erfahren Sie, wie Daten aus Hive mithilfe einer Kopieraktivität in eine Azure Data Factory-Pipeline in unterstützte Senkendatenspeicher kopiert werden.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: jingwang
ms.openlocfilehash: b245a80967d91b793fcf360772c0dec758f8f252
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60808906"
---
# <a name="copy-data-from-hive-using-azure-data-factory"></a>Kopieren von Daten aus Hive mithilfe von Azure Data Factory 

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten aus Hive zu kopieren. Er baut auf dem Artikel zur [Übersicht über die Kopieraktivität](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Sie können Daten aus Hive in beliebige unterstützte Senkendatenspeicher kopieren. Eine Liste der Datenspeicher, die als Quellen oder Senken für die Kopieraktivität unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory enthält einen integrierten Treiber zum Sicherstellen der Konnektivität. Daher müssen Sie mit diesem Connector keinen Treiber manuell installieren.

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
| authenticationType | Die Authentifizierungsmethode für den Zugriff auf den Hive-Server. <br/>Zulässige Werte sind: **Anonymous**, **Username**, **UsernameAndPassword**, **WindowsAzureHDInsightService** | Ja |
| serviceDiscoveryMode | „true“, um das Verwenden des Diensts ZooKeeper anzugeben, andernfalls „false“.  | Nein |
| zooKeeperNameSpace | Der Namespace für ZooKeeper, unter dem Hive Server 2-Knoten hinzugefügt werden.  | Nein |
| useNativeQuery | Gibt an, ob der Treiber native HiveQL-Abfragen verwendet oder diese in eine äquivalente Form in HiveQL konvertiert.  | Nein |
| userName | Der Benutzername für den Zugriff auf den Hive-Server.  | Nein |
| password | Das Kennwort für den Benutzer. Markieren Sie dieses Feld als SecureString, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). | Nein |
| httpPath | Die Teil-URL, die dem Hive-Server entspricht.  | Nein |
| enableSsl | Gibt an, ob die Verbindungen mit dem Server mit SSL verschlüsselt werden. Der Standardwert ist „false“.  | Nein |
| trustedCertPath | Der vollständige Pfad der PEM-Datei mit vertrauenswürdigen Zertifizierungsstellenzertifikaten zur Überprüfung des Servers beim Verbindungsaufbau über SSL. Diese Eigenschaft kann nur festgelegt werden, wenn SSL in einer selbstgehostetem IR verwendet wird. Der Standardwert ist die Datei „cacerts.pem“, die mit der IR installiert wird.  | Nein |
| useSystemTrustStore | Gibt an, ob ein Zertifizierungsstellenzertifikat aus dem Vertrauensspeicher des Systems oder aus einer angegebenen PEM-Datei verwendet werden soll. Der Standardwert ist „false“.  | Nein |
| allowHostNameCNMismatch | Gibt an, ob ein von der Zertifizierungsstelle ausgestellter SSL-Zertifikatsname erforderlich ist, der mit dem Hostnamen des Servers übereinstimmt, wenn eine Verbindung über SSL hergestellt wird. Der Standardwert ist „false“.  | Nein |
| allowSelfSignedServerCert | Gibt an, ob vom Server selbstsignierte Zertifikate zugelassen werden. Der Standardwert ist „false“.  | Nein |
| connectVia | Die [Integration Runtime](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden muss. Sie können die selbstgehostete Integration Runtime oder Azure Integration Runtime verwenden (sofern Ihr Datenspeicher öffentlich zugänglich ist). Wenn keine Option angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. |Nein |

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

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft des Datasets muss auf folgenden Wert festgelegt werden: **HiveObject** | Ja |
| tableName | Name der Tabelle. | Nein (wenn „query“ in der Aktivitätsquelle angegeben ist) |

**Beispiel**

```json
{
    "name": "HiveDataset",
    "properties": {
        "type": "HiveObject",
        "linkedServiceName": {
            "referenceName": "<Hive linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der Hive-Quelle unterstützt werden.

### <a name="hivesource-as-source"></a>HiveSource als Quelle

Legen Sie zum Kopieren von Daten aus einem Hive den Quelltyp in der Kopieraktivität auf **HiveSource** fest. Folgende Eigenschaften werden im Abschnitt **source** der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft der Quelle der Kopieraktivität muss auf Folgendes festgelegt werden: **HiveSource** | Ja |
| query | Verwendet die benutzerdefinierte SQL-Abfrage zum Lesen von Daten. Beispiel: `"SELECT * FROM MyTable"`. | Nein (wenn „tableName“ im Dataset angegeben ist) |

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

## <a name="next-steps"></a>Nächste Schritte
Eine Liste der Datenspeicher, die als Quellen und Senken für die Kopieraktivität in Azure Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).
