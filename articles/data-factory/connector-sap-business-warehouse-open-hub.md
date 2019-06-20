---
title: Kopieren von Daten aus SAP Business Warehouse über Open Hub mithilfe von Azure Data Factory | Microsoft-Dokumentation
description: Erfahren Sie, wie Daten aus SAP Business Warehouse (BW) über Open Hub mithilfe einer Kopieraktivität in eine Azure Data Factory-Pipeline in unterstützte Senkendatenspeicher kopiert werden.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: jingwang
ms.openlocfilehash: 6fb989632d3165ac5e54e540aae4385fc2258c85
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66256907"
---
# <a name="copy-data-from-sap-business-warehouse-via-open-hub-using-azure-data-factory"></a>Kopieren von Daten aus SAP Business Warehouse über Open Hub mithilfe von Azure Data Factory

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten aus SAP Business Warehouse (BW) über Open Hub zu kopieren. Er baut auf dem Artikel zur [Übersicht über die Kopieraktivität](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Sie können Daten aus SAP Business Warehouse über Open Hub in jeden unterstützten Senkendatenspeicher kopieren. Eine Liste der Datenspeicher, die als Quellen oder Senken für die Kopieraktivität unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

Dieser SAP Business Warehouse Open Hub-Connector unterstützt insbesondere Folgendes:

- SAP Business Warehouse **Version 7.01 oder höher (in einem aktuellen, nach 2015 veröffentlichten SAP-Supportpaket)** .
- Kopieren von Daten über lokale Open Hub Destination-Tabelle, wie z.B. DSO, InfoCube, MultiProvider, DataSource usw.
- Kopieren von Daten mithilfe der Standardauthentifizierung
- Herstellen einer Verbindung zum Anwendungsserver.

## <a name="sap-bw-open-hub-integration"></a>SAP BW Open Hub-Integration 

Der [SAP BW Open Hub Service](https://wiki.scn.sap.com/wiki/display/BI/Overview+of+Open+Hub+Service) ist eine effiziente Möglichkeit zum Extrahieren von Daten aus SAP BW. Die folgende Abbildung zeigt einen der typischen Abläufe von Kunden in ihrem SAP-System, in diesem Fall Datenflüsse aus SAP ECC -> PSA -> DSO -> Cube.

SAP BW Open Hub Destination (OHD) definiert das Ziel, an das die SAP-Daten weitergeleitet werden. Alle von SAP Data Transfer Process (DTP) unterstützten Objekte können als Open Hub-Datenquellen verwendet werden, z.B. DSO, InfoCube, DataSource usw. Ein Open Hub Destination-Typ – wo die weitergeleiteten Daten gespeichert werden – können Datenbanktabellen (lokal oder remote) und Flatfiles sein. Dieser SAP BW Open Hub-Connector unterstützt das Kopieren von Daten aus lokalen OHD-Tabelle in BW. Wenn Sie andere Typen verwenden, können Sie sich über andere Connectors direkt mit der Datenbank oder dem Dateisystem verbinden.

![SAP BW Open Hub](./media/connector-sap-business-warehouse-open-hub/sap-bw-open-hub.png)

## <a name="delta-extraction-flow"></a>Ablauf der Deltaextraktion

ADF SAP BW Open Hub Connector verfügt über die beiden optionalen Eigenschaften `excludeLastRequest` und `baseRequestId`, die zum Behandeln der Deltalast von Open Hub verwendet werden können. 

- **excludeLastRequestId**: Damit entscheiden Sie, ob die Datensätze der letzten Anforderung ausgeschlossen werden. Der Standardwert lautet „true“. 
- **baseRequestId**: Die ID der Anforderung für das Deltaladen. Sobald sie festgelegt ist, werden nur noch Daten abgerufen, bei denen requestId größer als der Wert dieser Eigenschaft ist. 

Insgesamt umfasst die Extraktion von SAP InfoProviders in Azure Data Factory (ADF) zwei Schritte: 

1. **SAP BW Data Transfer Process (DTP)** : In diesem Schritt werden die Daten aus einer SAP BW InfoProvider-Instanz in eine SAP BW Open Hub-Tabelle kopiert. 

1. **ADF Data Copy**: In diesem Schritt wird die Open Hub-Tabelle vom ADF Connector gelesen. 

![Ablauf der Deltaextraktion](media/connector-sap-business-warehouse-open-hub/delta-extraction-flow.png)

Im ersten Schritt wird ein Datenübertragungsprozess (DTP) durchgeführt. Bei jeder Ausführung wird eine neue SAP-Anforderungs-ID erstellt. Die Anforderungs-ID wird in der Open Hub-Tabelle gespeichert und dann vom ADF Connector verwendet, um das Delta zu identifizieren. Die beiden Schritte werden asynchron ausgeführt: Der DTP wird von SAP ausgelöst, und der ADF-Datenkopiervorgang wird über ADF ausgelöst. 

Standardmäßig wird das aktuelle Delta von ADF nicht aus der Open Hub-Tabelle gelesen (Option „exclude last request“ ist „true“). Die Daten in ADF sind daher nicht zu 100 % aktuell, was die Daten in der Open Hub-Tabelle betrifft (das letzte Delta fehlt). Mit diesem Verfahren wird dafür sichergestellt, dass aufgrund der asynchronen Extraktion keine Zeilen verloren gehen. Dies funktioniert auch dann gut, wenn ADF die Open Hub-Tabelle liest, während für DTP noch Schreibvorgänge in dieselbe Tabelle durchgeführt werden. 

Normalerweise speichern Sie die Anforderungs-ID für die maximalen Kopiervorgänge der letzten Ausführung durch ADF in einem Stagingdatenspeicher (z. B. Azure-Blob im obigen Diagramm). Diese Anforderung wird dann von ADF während der nächsten Ausführung nicht noch ein zweites Mal gelesen. Beachten Sie hierbei, dass die Daten nicht automatisch aus der Open Hub-Tabelle gelöscht werden.

Für die korrekte Deltaverarbeitung ist es nicht zulässig, Anforderungs-IDs unterschiedlicher DTPs in derselben Open Hub-Tabelle zu verwenden. Aus diesem Grund dürfen Sie für jedes Open Hub-Ziel (Open Hub Destination, OHD) nicht mehr als einen DTP erstellen. Falls Sie für eine InfoProvider-Instanz sowohl eine vollständige als auch eine Deltaextraktion benötigen, sollten Sie für die InfoProvider-Instanz zwei OHDs erstellen. 

## <a name="prerequisites"></a>Voraussetzungen

Zur Verwendung dieses SAP Business Warehouse Open Hub-Connectors müssen Sie folgende Schritte durchführen:

- Richten Sie eine selbstgehostete Integration Runtime Version 3.13 oder höher ein. Im Artikel [Selbstgehostete Integration Runtime](create-self-hosted-integration-runtime.md) finden Sie Details.

- Laden Sie den **[SAP .NET Connector 3.0](https://support.sap.com/en/product/connectors/msnet.html)** (64 Bit) von der SAP-Website herunter und installieren Sie ihn auf dem selbstgehosteten IR-Computer. Vergewissern Sie sich bei der Installation im Fenster der optionalen Einrichtungsschritte, dass Sie die Option **Assemblys in GAC installieren** wie in der folgenden Abbildung gezeigt auswählen. 

    ![Installieren von SAP .NET Connector](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- Der SAP-Benutzer, der im Data Factory BW-Connector verwendet wird, muss über folgende Berechtigungen verfügen: 

    - Autorisierung für RFC und SAP BW. 
    - Berechtigungen für die Aktivität „Execute“ des Autorisierungsobjekts „S_SDSAUTH“.

- Erstellen Sie den SAP Open Hub Destination-Typ **Datenbanktabelle**, wobei die Option „Technischer Schlüssel“ ausgewählt ist.  Es wird auch empfohlen, die Option „Daten aus der Tabelle löschen“ nicht zu aktivieren, obwohl sie nicht erforderlich ist. Nutzen Sie den DTP (direkt ausführen oder in bestehende Prozessketten integrieren), um Daten aus dem Quellobjekt (z.B. Cube), das Sie ausgewählt haben, in die Open Hub-Zieltabelle zu übernehmen.

## <a name="getting-started"></a>Erste Schritte

> [!TIP]
>
> Eine exemplarische Vorgehensweise zur Verwendung von SAP BW Open Hub Connector finden Sie unter [Laden von Daten aus SAP Business Warehouse (BW) mithilfe von Azure Data Factory](load-sap-bw-data.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für den SAP Business Warehouse Open Hub-Connector verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den mit SAP Business Warehouse Open Hub verknüpften Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft muss auf Folgendes festgelegt werden: **SapOpenHub** | Ja |
| server | Der Name des Servers, auf dem sich die SAP BW-Instanz befindet. | Ja |
| systemNumber | Die Systemnummer des SAP BW-Systems.<br/>Zulässiger Wert: Zweistellige Dezimalzahl, die als Zeichenfolge angegeben ist. | Ja |
| clientId | Client-ID des Clients im SAP BW-System.<br/>Zulässiger Wert: Dreistellige Dezimalzahl, die als Zeichenfolge angegeben ist. | Ja |
| language | Sprache, die das SAP-System verwendet. | Nein (Standardwert ist **EN**).|
| userName | Der Name des Benutzers, der Zugriff auf den SAP-Server hat. | Ja |
| password | Kennwort für den Benutzer Markieren Sie dieses Feld als SecureString, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). | Ja |
| connectVia | Die [Integrationslaufzeit](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden muss. Eine selbstgehostete Integrationslaufzeit ist erforderlich, wie unter [Voraussetzungen](#prerequisites) erwähnt wird. |Ja |

**Beispiel:**

```json
{
    "name": "SapBwOpenHubLinkedService",
    "properties": {
        "type": "SapOpenHub",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset-Eigenschaften

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu [Datasets](concepts-datasets-linked-services.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die vom SAP BW Open Hub-Dataset unterstützt werden.

Legen Sie zum Kopieren von Daten aus und nach SAP BW Open Hub die type-Eigenschaft des Datasets auf **SapOpenHubTable** fest. Die folgenden Eigenschaften werden unterstützt.

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft muss auf **SapOpenHubTable** festgelegt werden.  | Ja |
| openHubDestinationName | Der Name des Open Hub-Ziels, aus dem Daten kopiert werden. | Ja |
| excludeLastRequest | Damit entscheiden Sie, ob die Datensätze der letzten Anforderung ausgeschlossen werden. | Nein (Standardwert ist **true**). |
| baseRequestId | Die ID der Anforderung für das Deltaladen. Sobald sie festgelegt ist, werden nur noch Daten mit requestId **größer als** der Wert dieser Eigenschaft abgerufen.  | Nein |

>[!TIP]
>Wenn Ihre Open Hub-Tabelle z.B. nur die Daten enthält, die durch eine einzige Anforderungs-ID generiert wurden, Sie immer eine vollständige Ladung durchführen und die vorhandenen Daten in der Tabelle überschreiben, oder Sie den DTP nur einmal zum Testen ausführen, denken Sie daran, die Option „excludeLastRequest“ zu deaktivieren, um die Daten zu kopieren.

**Beispiel:**

```json
{
    "name": "SAPBWOpenHubDataset",
    "properties": {
        "type": "SapOpenHubTable",
        "linkedServiceName": {
            "referenceName": "<SAP BW Open Hub linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "openHubDestinationName": "<open hub destination name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der SAP BW Open Hub-Quelle unterstützt werden.

### <a name="sap-bw-open-hub-as-source"></a>SAP BW Open Hub als Quelle

Legen Sie zum Kopieren von Daten aus SAP BW Open Hub den Quelltyp in der Kopieraktivität auf **SapOpenHubSource** fest. Es gibt keine zusätzlichen typspezifischen Eigenschaften, die im Abschnitt **Quelle** der Kopieraktivität erforderlich sind.

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromSAPBWOpenHub",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW Open Hub input dataset name>",
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
                "type": "SapOpenHubSource"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-bw-open-hub"></a>Datentypzuordnung für SAP BW Open Hub

Beim Kopieren von Daten aus SAP BW Open Hub werden die folgenden Zuordnungen von SAP BW-Datentypen zu Azure Data Factory-Zwischendatentypen verwendet. Unter [Schema- und Datentypzuordnungen](copy-activity-schema-and-type-mapping.md) erfahren Sie, wie Sie Aktivitätszuordnungen für Quellschema und Datentyp in die Senke kopieren.

| SAP ABAP-Typ | Data Factory-Zwischendatentyp |
|:--- |:--- |
| C (String) | Zeichenfolge |
| I (integer) | Int32 |
| F (Float) | Double |
| D (Date) | Zeichenfolge |
| T (Time) | Zeichenfolge |
| P (BCD Packed, Currency, Decimal, Qty) | Decimal |
| N (Numc) | Zeichenfolge |
| X („Binary“ und „Raw“) | Zeichenfolge |

## <a name="next-steps"></a>Nächste Schritte
Eine Liste der Datenspeicher, die als Quellen und Senken für die Kopieraktivität in Azure Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).
