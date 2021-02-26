---
title: Kopieren von Daten aus SAP Business Warehouse mithilfe von Open Hub
description: Erfahren Sie, wie Daten aus SAP Business Warehouse (BW) über Open Hub mithilfe einer Kopieraktivität in eine Azure Data Factory-Pipeline in unterstützte Senkendatenspeicher kopiert werden.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/02/2020
ms.openlocfilehash: b766ce248a3543ef3323e026d760e550a0e3dd75
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100386678"
---
# <a name="copy-data-from-sap-business-warehouse-via-open-hub-using-azure-data-factory"></a>Kopieren von Daten aus SAP Business Warehouse über Open Hub mithilfe von Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten aus SAP Business Warehouse (BW) über Open Hub zu kopieren. Er baut auf dem Artikel zur [Übersicht über die Kopieraktivität](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

>[!TIP]
>Informationen zur allgemeinen Unterstützung des SAP-Datenintegrationsszenarios durch ADF finden Sie im [Whitepaper zur SAP-Datenintegration mit Azure Data Factory](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf). Dort finden Sie auch eine detaillierte Einführung in jeden SAP-Connector, einen Vergleich und Anleitungen.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Der Connector „SAP Business Warehouse über Open Hub“ wird für die folgenden Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen/Senken-Matrix](copy-activity-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)

Sie können Daten aus SAP Business Warehouse über Open Hub in jeden unterstützten Senkendatenspeicher kopieren. Eine Liste der Datenspeicher, die als Quellen oder Senken für die Kopieraktivität unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).

Dieser SAP Business Warehouse Open Hub-Connector unterstützt insbesondere Folgendes:

- SAP Business Warehouse **Version 7.01 oder höher (in einem aktuellen, nach 2015 veröffentlichten SAP-Supportpaket)** . SAP BW/4HANA wird von diesem Connector nicht unterstützt.
- Kopieren von Daten über lokale Open Hub Destination-Tabelle, wie z. B. DSO, InfoCube, MultiProvider, DataSource usw.
- Kopieren von Daten mithilfe der Standardauthentifizierung
- Herstellen einer Verbindung mit einem SAP-Anwendungsserver oder einem SAP-Nachrichtenserver.
- Abrufen von Daten über RFC.

## <a name="sap-bw-open-hub-integration"></a>SAP BW Open Hub-Integration 

Der [SAP BW Open Hub Service](https://wiki.scn.sap.com/wiki/display/BI/Overview+of+Open+Hub+Service) ist eine effiziente Möglichkeit zum Extrahieren von Daten aus SAP BW. Die folgende Abbildung zeigt einen der typischen Abläufe von Kunden in ihrem SAP-System, in diesem Fall Datenflüsse aus SAP ECC -> PSA -> DSO -> Cube.

SAP BW Open Hub Destination (OHD) definiert das Ziel, an das die SAP-Daten weitergeleitet werden. Alle von SAP Data Transfer Process (DTP) unterstützten Objekte können als Open Hub-Datenquellen verwendet werden, z.B. DSO, InfoCube, DataSource usw. Ein Open Hub Destination-Typ – wo die weitergeleiteten Daten gespeichert werden – können Datenbanktabellen (lokal oder remote) und Flatfiles sein. Dieser SAP BW Open Hub-Connector unterstützt das Kopieren von Daten aus lokalen OHD-Tabelle in BW. Wenn Sie andere Typen verwenden, können Sie sich über andere Connectors direkt mit der Datenbank oder dem Dateisystem verbinden.

![SAP BW Open Hub](./media/connector-sap-business-warehouse-open-hub/sap-bw-open-hub.png)

## <a name="delta-extraction-flow"></a>Ablauf der Deltaextraktion

ADF SAP BW Open Hub Connector verfügt über die beiden optionalen Eigenschaften `excludeLastRequest` und `baseRequestId`, die zum Behandeln der Deltalast von Open Hub verwendet werden können. 

- **excludeLastRequestId**: Damit entscheiden Sie, ob die Datensätze der letzten Anforderung ausgeschlossen werden. Der Standardwert ist true. 
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

- Erstellen Sie den SAP Open Hub Destination-Typ **Datenbanktabelle**, wobei die Option „Technischer Schlüssel“ ausgewählt ist.  Es wird auch empfohlen, die Option „Daten aus der Tabelle löschen“ nicht zu aktivieren, obwohl sie nicht erforderlich ist. Nutzen Sie den DTP (direkt ausführen oder in bestehende Prozessketten integrieren), um Daten aus dem Quellobjekt (z. B. Cube), das Sie ausgewählt haben, in die Open Hub-Zieltabelle zu übernehmen.

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
| messageServer | Der Hostname des SAP-Nachrichtenservers.<br/>Verwenden Sie sie zum Herstellen einer Verbindung mit einem SAP-Nachrichtenserver. | Nein |
| messageServerService | Der Dienstname oder die Portnummer des Nachrichtenservers.<br/>Verwenden Sie sie zum Herstellen einer Verbindung mit einem SAP-Nachrichtenserver. | Nein |
| systemId | Die ID des SAP-Systems, in dem sich die Tabelle befindet.<br/>Verwenden Sie sie zum Herstellen einer Verbindung mit einem SAP-Nachrichtenserver. | Nein |
| logonGroup | Die Anmeldegruppe für das SAP-System.<br/>Verwenden Sie sie zum Herstellen einer Verbindung mit einem SAP-Nachrichtenserver. | Nein |
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

Wenn Sie `excludeLastRequest` und `baseRequestId` im Dataset festgelegt haben, wird es weiterhin unverändert unterstützt. Es wird jedoch empfohlen, zukünftig das neue Modell in der Aktivitätsquelle zu verwenden.

**Beispiel:**

```json
{
    "name": "SAPBWOpenHubDataset",
    "properties": {
        "type": "SapOpenHubTable",
        "typeProperties": {
            "openHubDestinationName": "<open hub destination name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP BW Open Hub linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der SAP BW Open Hub-Quelle unterstützt werden.

### <a name="sap-bw-open-hub-as-source"></a>SAP BW Open Hub als Quelle

Beim Kopieren von Daten aus SAP BW Open Hub werden die folgenden Eigenschaften im Abschnitt **source** der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft der Quelle der Kopieraktivität muss auf **SapOpenHubSource** festgelegt werden. | Ja |
| excludeLastRequest | Damit entscheiden Sie, ob die Datensätze der letzten Anforderung ausgeschlossen werden. | Nein (Standardwert ist **true**). |
| baseRequestId | Die ID der Anforderung für das Deltaladen. Sobald sie festgelegt ist, werden nur noch Daten mit requestId **größer als** der Wert dieser Eigenschaft abgerufen.  | Nein |

>[!TIP]
>Wenn Ihre Open Hub-Tabelle z.B. nur die Daten enthält, die durch eine einzige Anforderungs-ID generiert wurden, Sie immer eine vollständige Ladung durchführen und die vorhandenen Daten in der Tabelle überschreiben, oder Sie den DTP nur einmal zum Testen ausführen, denken Sie daran, die Option „excludeLastRequest“ zu deaktivieren, um die Daten zu kopieren.

Um das Laden der Daten zu beschleunigen, können Sie [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) in der Kopieraktivität festlegen, um Daten parallel aus SAP BW Open Hub zu laden. Wenn Sie `parallelCopies` beispielsweise auf vier festlegen, führt Data Factory vier RFC-Aufrufe gleichzeitig aus. Jeder RFC-Aufruf ruft einen Teil der Daten aus Ihrer SAP BW Open Hub-Tabelle ab, die durch die DTP-Anforderungs-ID und Paket-ID partitioniert wurde. Dies gilt, wenn die Anzahl von eindeutigen DTP-Anforderungs-IDs plus der Anzahl der Paket-IDs größer als der Wert von `parallelCopies` ist. Beim Kopieren von Daten in einen dateibasierten Datenspeicher wird außerdem empfohlen, unter Verwendung von „Mehrere Dateien“ in einen Ordner zu schreiben (Sie müssen nur den Ordnernamen angeben). In diesem Fall ist die Leistung besser als beim Schreiben in eine einzelne Datei.

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
                "type": "SapOpenHubSource",
                "excludeLastRequest": true
            },
            "sink": {
                "type": "<sink type>"
            },
            "parallelCopies": 4
        }
    }
]
```

## <a name="data-type-mapping-for-sap-bw-open-hub"></a>Datentypzuordnung für SAP BW Open Hub

Beim Kopieren von Daten aus SAP BW Open Hub werden die folgenden Zuordnungen von SAP BW-Datentypen zu Azure Data Factory-Zwischendatentypen verwendet. Unter [Schema- und Datentypzuordnungen](copy-activity-schema-and-type-mapping.md) erfahren Sie, wie Sie Aktivitätszuordnungen für Quellschema und Datentyp in die Senke kopieren.

| SAP ABAP-Typ | Data Factory-Zwischendatentyp |
|:--- |:--- |
| C (String) | String |
| I (integer) | Int32 |
| F (Float) | Double |
| D (Date) | String |
| T (Time) | String |
| P (BCD Packed, Currency, Decimal, Qty) | Decimal |
| N (Numc) | String |
| X („Binary“ und „Raw“) | String |

## <a name="lookup-activity-properties"></a>Eigenschaften der Lookup-Aktivität

Ausführliche Informationen zu den Eigenschaften finden Sie unter [Lookup-Aktivität](control-flow-lookup-activity.md).

## <a name="troubleshooting-tips"></a>Tipps zur Problembehandlung

**Symptome:** Wenn Sie SAP BW auf HANA ausführen und beobachten, dass nur eine Teilmenge der Daten mit der ADF-Kopieraktivität kopiert wird (1 Million Zeilen), besteht die mögliche Ursache darin, dass Sie in Ihrer DTP die Option „SAP HANA-Ausführung" aktiviert haben. In diesem Fall kann ADF nur den ersten Datenstapel abrufen.

**Lösung:** Deaktivieren Sie die Option „SAP HANA-Ausführung“ in DTP, verarbeiten Sie die Daten erneut, und versuchen Sie dann erneut, die Kopieraktivität auszuführen.

## <a name="next-steps"></a>Nächste Schritte
Eine Liste der Datenspeicher, die als Quellen und Senken für die Kopieraktivität in Azure Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).
