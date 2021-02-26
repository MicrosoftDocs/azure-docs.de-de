---
title: Kopieren von Daten aus Presto mithilfe von Azure Data Factory
description: Erfahren Sie, wie Daten aus Presto mithilfe einer Kopieraktivität in eine Azure Data Factory-Pipeline in unterstützte Senkendatenspeicher kopiert werden.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: jingwang
ms.openlocfilehash: 33e521d418c219be8eb85b79a0e07d999edb1b08
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100374268"
---
# <a name="copy-data-from-presto-using-azure-data-factory"></a>Kopieren von Daten aus Presto mithilfe von Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten aus Presto zu kopieren. Er baut auf dem Artikel zur [Übersicht über die Kopieraktivität](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Der Presto-Connector wird für die folgenden Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen/Senken-Matrix](copy-activity-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)

Sie können Daten aus Presto in beliebige unterstützte Senkendatenspeicher kopieren. Eine Liste der Datenspeicher, die als Quellen oder Senken für die Kopieraktivität unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory enthält einen integrierten Treiber zum Sicherstellen der Konnektivität. Daher müssen Sie mit diesem Connector keinen Treiber manuell installieren.

## <a name="getting-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für den Presto-Connector verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den mit Presto verknüpften Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft muss auf Folgendes festgelegt werden: **Presto** | Ja |
| host | IP-Adresse oder Hostname des Presto-Servers. (Beispiel: 192.168.222.160)  | Ja |
| serverVersion | Die Version des Presto-Servers. (Beispiel: 0.148-t)  | Ja |
| catalog | Der Katalogkontext für alle Anforderungen an den Server.  | Ja |
| port | Der TCP-Port, den der Presto-Server verwendet, um auf Clientverbindungen zu lauschen. Der Standardwert ist 8080.  | Nein |
| authenticationType | Der Authentifizierungsmechanismus, der für die Verbindung mit dem Presto-Server verwendet werden soll. <br/>Zulässige Werte sind: **Anonym**, **LDAP** | Ja |
| username | Der Benutzername, der für die Verbindung mit dem Presto-Server verwendet werden soll.  | Nein |
| password | Das Kennwort, das zum Benutzernamen gehört. Markieren Sie dieses Feld als SecureString, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). | Nein |
| enableSsl | Gibt an, ob Verbindungen mit dem Server mit TLS verschlüsselt werden. Der Standardwert ist „FALSE“.  | Nein |
| trustedCertPath | Der vollständige Pfad der PEM-Datei mit vertrauenswürdigen Zertifizierungsstellenzertifikaten zur Überprüfung des Servers beim Verbindungsaufbau über TLS. Diese Eigenschaft kann nur festgelegt werden, wenn TLS in einer selbstgehosteten IR verwendet wird. Der Standardwert ist die Datei „cacerts.pem“, die mit der IR installiert wird.  | Nein |
| useSystemTrustStore | Gibt an, ob ein Zertifizierungsstellenzertifikat aus dem Vertrauensspeicher des Systems oder aus einer angegebenen PEM-Datei verwendet werden soll. Der Standardwert ist „FALSE“.  | Nein |
| allowHostNameCNMismatch | Gibt an, ob der Name eines von der Zertifizierungsstelle ausgestellten TLS-/SSL-Zertifikats mit dem Hostnamen des Servers übereinstimmen muss, wenn eine Verbindung über TLS hergestellt wird. Der Standardwert ist „FALSE“.  | Nein |
| allowSelfSignedServerCert | Gibt an, ob vom Server selbstsignierte Zertifikate zugelassen werden. Der Standardwert ist „FALSE“.  | Nein |
| timeZoneID | Die lokale Zeitzone, die von der Verbindung verwendet wird. Gültige Werte für diese Option sind in der Zeitzonendatenbank der IANA angegeben. Der Standardwert ist die Zeitzone des Systems.  | Nein |

**Beispiel:**

```json
{
    "name": "PrestoLinkedService",
    "properties": {
        "type": "Presto",
        "typeProperties": {
            "host" : "<host>",
            "serverVersion" : "0.148-t",
            "catalog" : "<catalog>",
            "port" : "<port>",
            "authenticationType" : "LDAP",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            },
            "timeZoneID" : "Europe/Berlin"
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset-Eigenschaften

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu [Datasets](concepts-datasets-linked-services.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die vom Presto-Dataset unterstützt werden.

Legen Sie zum Kopieren von Daten aus Presto die „type“-Eigenschaft des Datasets auf **PrestoObject** fest. Folgende Eigenschaften werden unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft des Datasets muss auf folgenden Wert festgelegt werden: **PrestoObject** | Ja |
| schema | Name des Schemas. |Nein (wenn „query“ in der Aktivitätsquelle angegeben ist)  |
| table | Der Name der Tabelle. |Nein (wenn „query“ in der Aktivitätsquelle angegeben ist)  |
| tableName | Name der Tabelle mit Schema. Diese Eigenschaft wird aus Gründen der Abwärtskompatibilität weiterhin unterstützt. Verwenden Sie `schema` und `table` für eine neue Workload. | Nein (wenn „query“ in der Aktivitätsquelle angegeben ist) |

**Beispiel**

```json
{
    "name": "PrestoDataset",
    "properties": {
        "type": "PrestoObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Presto linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der Presto-Quelle unterstützt werden.

### <a name="presto-as-source"></a>Presto als Quelle

Legen Sie zum Kopieren von Daten aus Presto den Quelltyp in der Kopieraktivität auf **PrestoSource** fest. Folgende Eigenschaften werden im Abschnitt **source** der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft der Quelle der Kopieraktivität muss auf Folgendes festgelegt werden: **PrestoSource** | Ja |
| Abfrage | Verwendet die benutzerdefinierte SQL-Abfrage zum Lesen von Daten. Beispiel: `"SELECT * FROM MyTable"`. | Nein (wenn „tableName“ im Dataset angegeben ist) |

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromPresto",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Presto input dataset name>",
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
                "type": "PrestoSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Eigenschaften der Lookup-Aktivität

Ausführliche Informationen zu den Eigenschaften finden Sie unter [Lookup-Aktivität](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Nächste Schritte
Eine Liste der Datenspeicher, die als Quellen und Senken für die Kopieraktivität in Azure Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).
