---
title: Kopieren von Daten nach oder aus Azure Data Lake Storage Gen2 mit Data Factory | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mithilfe von Azure Data Factory Daten in oder aus Azure Data Lake Storage Gen2 kopieren.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: jingwang
ms.openlocfilehash: 1e248a005b499227a667bebacf7244fc3df9c828
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239146"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen2-using-azure-data-factory"></a>Kopieren von Daten nach und aus Azure Data Lake Storage Gen2 mithilfe von Azure Data Factory

Azure Data Lake Storage Gen2 (ADLS Gen2) baut auf [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) auf und bietet eine Reihe von Funktionen für die Big Data-Analyse. Er ermöglicht Ihnen die Arbeit mit Ihren Daten über das Dateisystem und den Objektspeicher.

In diesem Artikel wird beschrieben, wie Sie Daten in und aus Azure Data Lake Storage Gen2 kopieren. Informationen zu Azure Data Factory finden Sie im [Einführungsartikel](introduction.md).

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Dieser Azure Data Lake Storage Gen2-Connector wird für die folgenden Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen/Senken-Matrix](copy-activity-overview.md)
- [Mapping Data Flow](concepts-data-flow-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)
- [GetMetadata-Aktivität](control-flow-get-metadata-activity.md)

Dieser Connector unterstützt insbesondere Folgendes:

- Kopieren von Dateien durch Verwendung eines Kontoschlüssels, eines Dienstprinzipals oder verwalteter Identitäten für die Authentifizierung von Azure-Ressourcen.
- Kopieren von Dateien im jeweiligen Zustand oder Analysieren bzw. Generieren von Dateien mit den [unterstützten Dateiformaten und Codecs für die Komprimierung](supported-file-formats-and-compression-codecs.md)

>[!TIP]
>Wenn Sie den hierarchischen Namespace aktivieren, beachten Sie, dass derzeit keine Interoperabilität bei Vorgängen zwischen Blob- und ADLS Gen2-APIs besteht. Falls der Fehler „ErrorCode=FilesystemNotFound“ mit dem Meldungstext „Das angegebene Dateisystem ist nicht vorhanden.“ auftritt, ist dies auf das angegebene Senkendateisystem zurückzuführen, das an anderer Stelle anstelle der ADLS Gen2-API über die Blob-API erstellt wurde. Geben Sie zum Beheben des Problems ein neues Dateisystem mit einem Namen an, der nicht als Name eines Blobcontainers vorhanden ist. Von ADF wird dieses Dateisystem automatisch während des Datenkopiervorgangs erstellt.

>[!NOTE]
>Wenn Sie die Option _„Vertrauenswürdigen Microsoft-Diensten den Zugriff auf dieses Speicherkonto erlauben“_ in den Firewalleinstellungen von Azure Storage aktivieren, tritt beim Herstellen der Verbindung mit Data Lake Storage Gen2 mithilfe von Azure Integration Runtime der Fehler „Verboten“ auf, da ADF nicht als vertrauenswürdiger Microsoft-Dienst gelten. Verwenden Sie stattdessen die selbstgehostete Integration Runtime zum Herstellen der Verbindung.

## <a name="get-started"></a>Erste Schritte

>[!TIP]
>Eine exemplarische Vorgehensweise zur Verwendung des Data Lake Storage Gen2-Connectors finden Sie unter [Laden von Daten in Azure Data Lake Storage Gen2](load-azure-data-lake-storage-gen2.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für Data Lake Storage Gen2 verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Der Azure Data Lake Storage Gen2-Connector unterstützt die folgenden Authentifizierungstypen (Details finden Sie im entsprechenden Abschnitt):

- [Kontoschlüsselauthentifizierung](#account-key-authentication)
- [Dienstprinzipalauthentifizierung](#service-principal-authentication)
- [Verwaltete Identitäten für Azure-Ressourcenauthentifizierung](#managed-identity)

### <a name="account-key-authentication"></a>Kontoschlüsselauthentifizierung

Für die Verwendung der Authentifizierung mit dem Speicherkontoschlüssel werden die folgenden Eigenschaften unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die „type“-Eigenschaft muss auf **AzureBlobFS** festgelegt sein. |Ja |
| url | Endpunkt für Data Lake Storage Gen2 im Format `https://<accountname>.dfs.core.windows.net` | Ja |
| accountKey | Kontoschlüssel für den Data Lake Storage Gen2-Dienst. Markieren Sie dieses Feld als SecureString, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). |Ja |
| connectVia | Die [Integration Runtime](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden soll. Sie können die Azure Integration Runtime oder die selbstgehostete Integration Runtime verwenden (sofern sich Ihr Datenspeicher in einem privaten Netzwerk befindet). Wenn keine Option angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. |Nein |

**Beispiel:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "accountkey": { 
                "type": "SecureString", 
                "value": "<accountkey>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication"></a>Dienstprinzipalauthentifizierung

Zum Verwenden der Dienstprinzipalauthentifizierung führen Sie die folgenden Schritte aus:

1. Registrieren Sie eine Anwendungsentität in Azure Active Directory (Azure AD), indem Sie die Anleitungen unter [Registrieren Ihrer Anwendung bei einem Azure AD-Mandanten](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant) befolgen. Notieren Sie sich die folgenden Werte, die Sie zum Definieren des verknüpften Diensts verwenden:

    - Anwendungs-ID
    - Anwendungsschlüssel
    - Mandanten-ID

2. Erteilen Sie dem Dienstprinzipal geeignete Berechtigungen.

    - **Als Quelle**: Erteilen Sie in Storage-Explorer mindestens die Berechtigung **Lesen + Ausführen**, um Dateien in Ordnern und Unterordnern aufzulisten und zu kopieren, oder erteilen Sie die Berechtigung **Lesen**, um eine einzelne Datei zu kopieren. Weisen Sie alternativ in der Zugriffssteuerung (IAM) mindestens die Rolle **Storage-Blobdatenleser** zu.
    - **Als Senke**: Erteilen Sie in Storage-Explorer mindestens die Berechtigung **Schreiben + Ausführen**, um untergeordnete Elemente im Ordner zu erstellen. Weisen Sie in der Zugriffssteuerung (IAM) mindestens die Rolle **Mitwirkender an Storage-Blobdaten** zu.

>[!NOTE]
>Zum Auflisten von Ordnern ab der Kontoebene oder Testen der Verbindung müssen Sie die Berechtigung, die dem Dienstprinzipal für das Speicherkonto erteilt wird, **mit der Berechtigung „Ausführen“ in IAM festlegen**. Dies gilt, wenn Sie:
>- Das **Tool zum Kopieren von Daten** verwenden, um eine Kopierpipeline zu erstellen
>- Die **Data Factory-Benutzeroberfläche** verwenden, um die Verbindung zu testen und während der Erstellung in Ordnern zu navigieren 
>Wenn Sie Bedenken haben, Berechtigungen auf Kontoebene zu vergeben, können Sie die Testverbindung und den Eingabepfad manuell während der Erstellung überspringen. Kopieraktivitäten funktionieren weiterhin, solange dem Dienstprinzipal die erforderlichen Berechtigungen für die zu kopierenden Dateien erteilt wurden.

Diese Eigenschaften werden im verknüpften Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die „type“-Eigenschaft muss auf **AzureBlobFS** festgelegt sein. |Ja |
| url | Endpunkt für Data Lake Storage Gen2 im Format `https://<accountname>.dfs.core.windows.net` | Ja |
| servicePrincipalId | Geben Sie die Client-ID der Anwendung an. | Ja |
| servicePrincipalKey | Geben Sie den Schlüssel der Anwendung an. Markieren Sie dieses Feld als **SecureString**, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). | Ja |
| tenant | Geben Sie die Mandanteninformationen (Domänenname oder Mandanten-ID) für Ihre Anwendung an. Diese können Sie abrufen, indem Sie den Mauszeiger über den rechten oberen Bereich im Azure-Portal bewegen. | Ja |
| connectVia | Die [Integration Runtime](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden soll. Sie können die Azure Integration Runtime oder die selbstgehostete Integration Runtime verwenden (sofern sich Ihr Datenspeicher in einem privaten Netzwerk befindet). Wenn keine Option angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. |Nein |

**Beispiel:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>" 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a>Verwaltete Identitäten für Azure-Ressourcenauthentifizierung

Eine Data Factory kann einer [verwalteten Identität für Azure-Ressourcen](data-factory-service-identity.md) zugeordnet werden, die diese spezielle Data Factory darstellt. Sie können diese verwaltete Identität direkt für die ADLS Gen2-Authentifizierung verwenden, ähnlich wie bei der Verwendung Ihres eigenen Dienstprinzipals. Sie erlaubt dieser bestimmten Factory den Zugriff auf und das Kopieren von Daten aus bzw. in ADLS Gen2.

Um verwaltete Identitäten für die Azure-Ressourcenauthentifizierung zu verwenden, gehen Sie folgendermaßen vor:

1. [Rufen Sie die verwalteten Data Factory-Identitätsinformationen ab](data-factory-service-identity.md#retrieve-managed-identity), indem Sie den Wert von „DIENSTIDENTITÄTSANWENDUNGS-ID“ kopieren, der zusammen mit der Factory generiert wurde.

2. Erteilen Sie der verwalteten Entität geeignete Berechtigungen. 

    - **Als Quelle**: Erteilen Sie in Storage-Explorer mindestens die Berechtigung **Lesen + Ausführen**, um Dateien in Ordnern und Unterordnern aufzulisten und zu kopieren, oder erteilen Sie die Berechtigung **Lesen**, um eine einzelne Datei zu kopieren. Weisen Sie alternativ in der Zugriffssteuerung (IAM) mindestens die Rolle **Storage-Blobdatenleser** zu.
    - **Als Senke**: Erteilen Sie in Storage-Explorer mindestens die Berechtigung **Schreiben + Ausführen**, um untergeordnete Elemente im Ordner zu erstellen. Weisen Sie in der Zugriffssteuerung (IAM) mindestens die Rolle **Mitwirkender an Storage-Blobdaten** zu.

>[!NOTE]
>Zum Auflisten von Ordnern ab der Kontoebene oder Testen der Verbindung müssen Sie die Berechtigung, die der verwalteten Identität für das Speicherkonto erteilt wird, **mit der Berechtigung „Ausführen“ in IAM festlegen**. Dies gilt, wenn Sie:
>- Das **Tool zum Kopieren von Daten** verwenden, um eine Kopierpipeline zu erstellen
>- Die **Data Factory-Benutzeroberfläche** verwenden, um die Verbindung zu testen und während der Erstellung in Ordnern zu navigieren 
>Wenn Sie Bedenken haben, Berechtigungen auf Kontoebene zu vergeben, können Sie die Testverbindung und den Eingabepfad manuell während der Erstellung überspringen. Kopieraktivitäten funktionieren weiterhin, solange der verwalteten Identität die erforderlichen Berechtigungen für die zu kopierenden Dateien gewährt werden.

>[!IMPORTANT]
>Wenn Sie PolyBase verwenden, um Daten aus ADLS Gen2 in SQL DW zu laden, und verwaltete ADLS Gen2-Identität eingesetzt wird, stellen Sie sicher, auch die Schritte 1 und 2 in [dieser Anleitung](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage) auszuführen, um Ihren SQL-Datenbankserver bei Azure Active Directory (AAD) zu registrieren und Ihrem SQL-Datenbankserver die Rolle „Storage Blob Data Contributor RBAC“ (Speicherblobdaten-Mitwirkender RBAC) zuzuweisen. Der Rest wird von ADF erledigt. Wenn ADLS Gen2 bei Ihnen mit einem VNET-Dienstendpunkt konfiguriert ist, müssen Sie die Authentifizierung mit der verwalteten Identität verwenden, damit mithilfe von PolyBase Daten dafür geladen werden können.

Diese Eigenschaften werden im verknüpften Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die „type“-Eigenschaft muss auf **AzureBlobFS** festgelegt sein. |Ja |
| url | Endpunkt für Data Lake Storage Gen2 im Format `https://<accountname>.dfs.core.windows.net` | Ja |
| connectVia | Die [Integration Runtime](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden soll. Sie können die Azure Integration Runtime oder die selbstgehostete Integration Runtime verwenden (sofern sich Ihr Datenspeicher in einem privaten Netzwerk befindet). Wenn keine Option angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. |Nein |

**Beispiel:**

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset-Eigenschaften

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu [Datasets](concepts-datasets-linked-services.md). 

- Informationen zum **Parquet-Format und zum Textformat mit Trennzeichen** finden Sie im Abschnitt [Dataset für Parquet-Format und Textformat mit Trennzeichen](#parquet-and-delimited-text-format-dataset).
- Informationen zu anderen Formaten wie **ORC/Avro/JSON/Binär** finden Sie im Abschnitt [Dataset in anderen Formaten](#other-format-dataset).

### <a name="parquet-and-delimited-text-format-dataset"></a>Dataset im Parquet-Format und im Textformat mit Trennzeichen

Informationen zum Kopieren von Daten in und aus ADLS Gen2 im **Parquet-Format oder im Textformat mit Trennzeichen** finden Sie in den Artikeln [Parquet-Format](format-parquet.md) und [Textformat mit Trennzeichen](format-delimited-text.md) zu formatbasierten Datasets und unterstützten Einstellungen. Folgende Eigenschaften werden für ADLS Gen2 unter `location`-Einstellungen in formatbasierten Datasets unterstützt:

| Eigenschaft   | BESCHREIBUNG                                                  | Erforderlich |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Die „type“-Eigenschaft unter `location` im Dataset muss auf **AzureBlobFSLocation** festgelegt werden. | Ja      |
| fileSystem | Der ADLS Gen2-Dateisystemname.                              | Nein       |
| folderPath | Der Pfad zum Ordner im angegebenen Dateisystem. Wenn Sie Platzhalter verwenden möchten, um Ordner zu filtern, überspringen Sie diese Einstellung, und geben Sie entsprechende Aktivitätsquelleneinstellungen an. | Nein       |
| fileName   | Der Dateiname im angegebenen „fileSystem“ und „folderPath“. Wenn Sie zum Filtern von Dateien Platzhalter verwenden möchten, überspringen Sie diese Einstellung und geben sie in den Aktivitätsquelleneinstellungen an. | Nein       |

> [!NOTE]
> Die im nächsten Abschnitt erwähnten Datasets vom Typ **AzureBlobFSFile** mit Parquet-/Textformat werden für Copy-/Lookup-/GetMetadata-Aktivitäten aus Gründen der Abwärtskompatibilität unverändert unterstützt. Mit Mapping Data Flow können sie jedoch nicht verwendet werden. Es wird jedoch empfohlen, in Zukunft das neue Modell zu verwenden, da diese neuen Typen nun von der Benutzeroberfläche für die ADF-Dokumentenerstellung generiert werden.

**Beispiel:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<ADLS Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobFSLocation",
                "fileSystem": "filesystemname",
                "folderPath": "folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

### <a name="other-format-dataset"></a>Dataset in anderen Formaten

Zum Kopieren von Daten in und aus ADLS Gen2 im **ORC/Avro/JSON/Binärformat** werden folgende Eigenschaften unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die „type“-Eigenschaft des Datasets muss auf **AzureBlobFSFile** festgelegt sein. |Ja |
| folderPath | Pfad zum Ordner in Data Lake Storage Gen2. Wenn keine Angabe vorhanden ist, wird auf das Stammverzeichnis verwiesen. <br/><br/>Platzhalterfilter werden unterstützt. Zulässige Platzhalter sind: `*` (entspricht null oder mehr Zeichen) und `?` (entspricht null oder einem einzelnen Zeichen). Verwenden Sie `^` als Escapezeichen, wenn Ihr tatsächlicher Ordnername einen Platzhalter oder dieses Escapezeichen enthält. <br/><br/>Beispiele: Dateisystem/Ordner/. Weitere Beispiele finden Sie unter [Beispiele für Ordner- und Dateifilter](#folder-and-file-filter-examples). |Nein |
| fileName | **Name oder Platzhalterfilter** für die Dateien unter dem angegebenen Wert für „folderPath“. Wenn Sie für diese Eigenschaft keinen Wert angeben, verweist das Dataset auf alle Dateien im Ordner. <br/><br/>Für Filter sind folgende Platzhalter zulässig: `*` (entspricht null [0] oder mehr Zeichen) und `?` (entspricht null [0] oder einem einzelnen Zeichen).<br/>- Beispiel 1: `"fileName": "*.csv"`<br/>- Beispiel 2: `"fileName": "???20180427.txt"`<br/>Verwenden Sie `^` als Escapezeichen, wenn der tatsächliche Dateiname einen Platzhalter oder dieses Escapezeichen enthält.<br/><br/>Wenn „fileName“ nicht für ein Ausgabedataset und **preserveHierarchy** nicht in der Aktivitätssenke angegeben sind, generiert die Kopieraktivität den Dateinamen automatisch mit dem folgenden Muster: „*Data.[GUID der Aktivitätsausführungs-ID].[GUID, sofern „FlattenHierarchy“].[Format, sofern konfiguriert].[Komprimierung, sofern konfiguriert]* “, z. B. „Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz“. Wenn Sie Daten aus einer Quelle im Tabellenformat kopieren und dabei anstelle einer Abfrage den Tabellennamen verwenden, lautet das Namensmuster „ *[Tabellenname].[ Format].[Komprimierung, sofern konfiguriert]* “, z.B. „MyTable.csv“. |Nein |
| modifiedDatetimeStart | Dateifilterung basierend auf dem Attribut: Letzte Änderung. Die Dateien werden ausgewählt, wenn der Zeitpunkt der letzten Änderung innerhalb des Zeitbereichs zwischen `modifiedDatetimeStart` und `modifiedDatetimeEnd` liegt. Die Zeit wird auf die UTC-Zeitzone im Format „2018-12-01T05:00:00Z“ angewandt. <br/><br/> Beachten Sie, dass die generelle Leistung der Datenverschiebung beeinträchtigt wird, wenn Sie diese Einstellung aktivieren und eine Dateifilterung für eine große Zahl von Dateien vornehmen möchten. <br/><br/> Die Eigenschaften können NULL sein, was bedeutet, dass kein Dateiattributfilter auf das Dataset angewandt wird.  Wenn `modifiedDatetimeStart` den datetime-Wert aufweist, aber `modifiedDatetimeEnd` NULL ist, bedeutet dies, dass die Dateien ausgewählt werden, deren Attribut für die letzte Änderung größer oder gleich dem datetime-Wert ist.  Wenn `modifiedDatetimeEnd` den datetime-Wert aufweist, aber `modifiedDatetimeStart` NULL ist, bedeutet dies, dass die Dateien ausgewählt werden, deren Attribut für die letzte Änderung kleiner als der datetime-Wert ist.| Nein |
| modifiedDatetimeEnd | Dateifilterung basierend auf dem Attribut: Letzte Änderung. Die Dateien werden ausgewählt, wenn der Zeitpunkt der letzten Änderung innerhalb des Zeitbereichs zwischen `modifiedDatetimeStart` und `modifiedDatetimeEnd` liegt. Die Zeit wird auf die UTC-Zeitzone im Format „2018-12-01T05:00:00Z“ angewandt. <br/><br/> Beachten Sie, dass die generelle Leistung der Datenverschiebung beeinträchtigt wird, wenn Sie diese Einstellung aktivieren und eine Dateifilterung für eine große Zahl von Dateien vornehmen möchten. <br/><br/> Die Eigenschaften können NULL sein, was bedeutet, dass kein Dateiattributfilter auf das Dataset angewandt wird.  Wenn `modifiedDatetimeStart` den datetime-Wert aufweist, aber `modifiedDatetimeEnd` NULL ist, bedeutet dies, dass die Dateien ausgewählt werden, deren Attribut für die letzte Änderung größer oder gleich dem datetime-Wert ist.  Wenn `modifiedDatetimeEnd` den datetime-Wert aufweist, aber `modifiedDatetimeStart` NULL ist, bedeutet dies, dass die Dateien ausgewählt werden, deren Attribut für die letzte Änderung kleiner als der datetime-Wert ist.| Nein |
| format | Wenn Sie Dateien unverändert zwischen dateibasierten Speichern kopieren möchten (binäre Kopie), überspringen Sie den Formatabschnitt in den Definitionen der Eingabe- und Ausgabedatasets.<br/><br/>Für das Analysieren oder Generieren von Dateien mit einem bestimmten Format werden die folgenden Dateiformattypen unterstützt: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** und **ParquetFormat**. Sie müssen die **type**-Eigenschaft unter **format** auf einen dieser Werte festlegen. Weitere Informationen finden Sie in den Abschnitten [Textformat](supported-file-formats-and-compression-codecs.md#text-format), [JSON-Format](supported-file-formats-and-compression-codecs.md#json-format), [Avro-Format](supported-file-formats-and-compression-codecs.md#avro-format), [Orc-Format](supported-file-formats-and-compression-codecs.md#orc-format) und [Parquet-Format](supported-file-formats-and-compression-codecs.md#parquet-format). |Nein (nur für Szenarien mit Binärkopien) |
| compression | Geben Sie den Typ und den Grad der Komprimierung für die Daten an. Weitere Informationen finden Sie unter [Unterstützte Dateiformate und Codecs für die Komprimierung](supported-file-formats-and-compression-codecs.md#compression-support).<br/>Unterstützte Typen sind **GZip**, **Deflate**, **BZIP2** und **ZipDeflate**.<br/>Unterstützte Grade sind **Optimal** und **Schnellste**. |Nein |

>[!TIP]
>Wenn Sie alle Dateien eines Ordners kopieren möchten, geben Sie nur **folderPath** an.<br>Wenn Sie eine einzelne Datei mit einem bestimmten Namen kopieren möchten, geben Sie **folderPath** mit dem Ordner und **fileName** mit dem Dateinamen an.<br>Wenn Sie eine Teilmenge der Dateien eines Ordners kopieren möchten, geben Sie **folderPath** mit dem Ordner und **fileName** mit dem Platzhalterfilter an. 

**Beispiel:**

```json
{
    "name": "ADLSGen2Dataset",
    "properties": {
        "type": "AzureBlobFSFile",
        "linkedServiceName": {
            "referenceName": "<Azure Data Lake Storage Gen2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "myfilesystem/myfolder",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie in den Artikeln [Konfigurationen für die Kopieraktivität](copy-activity-overview.md#configuration) und [Pipelines und Aktivitäten](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der Data Lake Storage Gen2-Quelle und -Senke unterstützt werden.

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>Azure Data Lake Storage Gen2 als Quelltyp

- Informationen zum Kopieren aus dem **Parquet-Format und dem Textformat mit Trennzeichen** finden Sie im Abschnitt [Quelle im Parquet-Format und im Textformat mit Trennzeichen](#parquet-and-delimited-text-format-source).
- Informationen zum Kopieren aus anderen Formaten wie **ORC/Avro/JSON/Binär** finden Sie im Abschnitt [Quelle in anderen Formaten](#other-format-source).

#### <a name="parquet-and-delimited-text-format-source"></a>Quelle im Parquet-Format und im Textformat mit Trennzeichen

Informationen zum Kopieren von Daten aus ADLS Gen2 im **Parquet-Format oder im Textformat mit Trennzeichen** finden Sie in den Artikeln [Parquet-Format](format-parquet.md) und [Textformat mit Trennzeichen](format-delimited-text.md) zu formatbasierten Quellen für Copy-Aktivitäten und unterstützten Einstellungen. Folgende Eigenschaften werden für ADLS Gen2 unter `storeSettings`-Einstellungen in formatbasierten Kopierquellen unterstützt:

| Eigenschaft                 | BESCHREIBUNG                                                  | Erforderlich                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Die „type“-Eigenschaft unter `storeSettings` muss auf **AzureBlobFSReadSetting** festgelegt werden. | Ja                                           |
| recursive                | Gibt an, ob die Daten rekursiv aus den Unterordnern oder nur aus dem angegebenen Ordner gelesen werden. Beachten Sie Folgendes: Wenn „recursive“ auf „true“ festgelegt ist und es sich bei der Senke um einen dateibasierten Speicher handelt, wird ein leerer Ordner oder Unterordner nicht in die Senke kopiert und dort auch nicht erstellt. Zulässige Werte sind **true** (Standard) und **false**. | Nein                                            |
| wildcardFolderPath       | Der in Datasets zum Filtern von Quellordnern konfigurierte Ordnerpfad mit Platzhalterzeichen im angegebenen Dateisystem. <br>Zulässige Platzhalter sind: `*` (entspricht null oder mehr Zeichen) und `?` (entspricht null oder einem einzelnen Zeichen). Verwenden Sie `^` als Escapezeichen, wenn Ihr tatsächlicher Dateiname einen Platzhalter oder dieses Escapezeichen enthält. <br>Weitere Beispiele finden Sie unter [Beispiele für Ordner- und Dateifilter](#folder-and-file-filter-examples). | Nein                                            |
| wildcardFileName         | Der Dateiname mit Platzhalterzeichen im angegebenen Dateisystem und „folderPath/wildcardFolderPath“ zum Filtern von Quelldateien. <br>Zulässige Platzhalter sind: `*` (entspricht null oder mehr Zeichen) und `?` (entspricht null oder einem einzelnen Zeichen). Verwenden Sie `^` als Escapezeichen, wenn Ihr tatsächlicher Dateiname einen Platzhalter oder dieses Escapezeichen enthält.  Weitere Beispiele finden Sie unter [Beispiele für Ordner- und Dateifilter](#folder-and-file-filter-examples). | Ja, wenn `fileName` nicht im Dataset angegeben ist |
| modifiedDatetimeStart    | Dateifilterung basierend auf dem Attribut: Letzte Änderung. Die Dateien werden ausgewählt, wenn der Zeitpunkt der letzten Änderung innerhalb des Zeitbereichs zwischen `modifiedDatetimeStart` und `modifiedDatetimeEnd` liegt. Die Zeit wird auf die UTC-Zeitzone im Format „2018-12-01T05:00:00Z“ angewandt. <br> Die Eigenschaften können NULL sein, was bedeutet, dass kein Dateiattributfilter auf das Dataset angewandt wird.  Wenn `modifiedDatetimeStart` den datetime-Wert aufweist, aber `modifiedDatetimeEnd` NULL ist, bedeutet dies, dass die Dateien ausgewählt werden, deren Attribut für die letzte Änderung größer oder gleich dem datetime-Wert ist.  Wenn `modifiedDatetimeEnd` den datetime-Wert aufweist, aber `modifiedDatetimeStart` NULL ist, bedeutet dies, dass die Dateien ausgewählt werden, deren Attribut für die letzte Änderung kleiner als der datetime-Wert ist. | Nein                                            |
| modifiedDatetimeEnd      | Wie oben.                                               | Nein                                            |
| maxConcurrentConnections | Die Anzahl von Verbindungen, die gleichzeitig mit einem Speicher hergestellt werden können. Geben Sie diesen Wert nur an, wenn Sie die gleichzeitigen Verbindungen mit dem Datenspeicher begrenzen möchten. | Nein                                            |

> [!NOTE]
> Beim Parquet-Format/Textformat mit Trennzeichen wird die im nächsten Abschnitt beschriebene Quelle der Kopieraktivität vom Typ **AzureBlobFSSource** aus Gründen der Abwärtskompatibilität unverändert unterstützt. Es wird jedoch empfohlen, in Zukunft das neue Modell zu verwenden, da diese neuen Typen nun von der Benutzeroberfläche für die ADF-Dokumentenerstellung generiert werden.

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
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
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureBlobFSReadSetting",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="other-format-source"></a>Quelle in anderen Formaten

Zum Kopieren von Daten aus ADLS Gen2 im **ORC/Avro/JSON/Binärformat** werden folgende Eigenschaften im Abschnitt **source** der Copy-Aktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die „type“-Eigenschaft der Quelle der Kopieraktivität muss auf **AzureBlobFSSource** festgelegt sein. |Ja |
| recursive | Gibt an, ob die Daten rekursiv aus den Unterordnern oder nur aus dem angegebenen Ordner gelesen werden. Beachten Sie Folgendes: Wenn „recursive“ auf „true“ festgelegt ist und es sich bei der Senke um einen dateibasierten Speicher handelt, wird ein leerer Ordner oder Unterordner nicht in die Senke kopiert und dort auch nicht erstellt.<br/>Zulässige Werte sind **true** (Standard) und **false**. | Nein |
| maxConcurrentConnections | Die Anzahl von Verbindungen, die gleichzeitig mit dem Datenspeicher hergestellt werden können. Geben Sie diesen Wert nur an, wenn Sie die gleichzeitigen Verbindungen mit dem Datenspeicher begrenzen möchten. | Nein |

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen2 input dataset name>",
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
                "type": "AzureBlobFSSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-data-lake-storage-gen2-as-a-sink-type"></a>Azure Data Lake Storage Gen2 als Senkentyp

- Informationen zum Kopieren in das **Parquet-Format und das Textformat mit Trennzeichen** finden Sie im Abschnitt [Senke im Parquet-Format und im Textformat mit Trennzeichen](#parquet-and-delimited-text-format-sink).
- Informationen zum Kopieren in andere Formate wie **ORC/Avro/JSON/Binärformat** finden Sie im Abschnitt [Senke in anderen Formaten](#other-format-sink).

#### <a name="parquet-and-delimited-text-format-sink"></a>Senke im Parquet-Format und im Textformat mit Trennzeichen

Informationen zum Kopieren von Daten in ADLS Gen2 im **Parquet-Format oder im Textformat mit Trennzeichen** finden Sie in den Artikeln [Parquet-Format](format-parquet.md) und [Textformat mit Trennzeichen](format-delimited-text.md) zu formatbasierten Senken für Copy-Aktivitäten und unterstützten Einstellungen. Folgende Eigenschaften werden für ADLS Gen2 unter `storeSettings`-Einstellungen in formatbasierten Kopiersenken unterstützt:

| Eigenschaft                 | BESCHREIBUNG                                                  | Erforderlich |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | Die „type“-Eigenschaft unter `storeSettings` muss auf **AzureBlobFSWriteSetting** festgelegt werden. | Ja      |
| copyBehavior             | Definiert das Kopierverhalten, wenn es sich bei der Quelle um Dateien aus einem dateibasierten Datenspeicher handelt.<br/><br/>Zulässige Werte sind:<br/><b>- PreserveHierarchy (Standard)</b>: Behält die Dateihierarchie im Zielordner bei. Der relative Pfad der Quelldatei zum Quellordner entspricht dem relativen Pfad der Zieldatei zum Zielordner.<br/><b>- FlattenHierarchy</b>: Alle Dateien aus dem Quellordner befinden sich auf der ersten Ebene des Zielordners. Die Namen für die Zieldateien werden automatisch generiert. <br/><b>- MergeFiles</b>: Alle Dateien aus dem Quellordner werden in einer Datei zusammengeführt. Wenn der Dateiname angegeben wurde, entspricht der zusammengeführte Dateiname dem angegebenen Namen. Andernfalls wird der Dateiname automatisch generiert. | Nein       |
| maxConcurrentConnections | Die Anzahl von Verbindungen, die gleichzeitig mit dem Datenspeicher hergestellt werden können. Geben Sie diesen Wert nur an, wenn Sie die gleichzeitigen Verbindungen mit dem Datenspeicher begrenzen möchten. | Nein       |

> [!NOTE]
> Beim Parquet-Format/Textformat mit Trennzeichen wird die im nächsten Abschnitt beschriebene Senke der Kopieraktivität vom Typ **AzureBlobFSSink** aus Gründen der Abwärtskompatibilität unverändert unterstützt. Es wird jedoch empfohlen, in Zukunft das neue Modell zu verwenden, da diese neuen Typen nun von der Benutzeroberfläche für die ADF-Dokumentenerstellung generiert werden.

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyToADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "AzureBlobFSWriteSetting",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

#### <a name="other-format-sink"></a>Senke in anderen Formaten

Zum Kopieren von Daten in ADLS Gen2 im **ORC/Avro/JSON/Binärformat** werden im Abschnitt **sink** folgende Eigenschaften unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die „type“-Eigenschaft der Senke der Kopieraktivität muss auf **AzureBlobFSSink** festgelegt sein. |Ja |
| copyBehavior | Definiert das Kopierverhalten, wenn es sich bei der Quelle um Dateien aus einem dateibasierten Datenspeicher handelt.<br/><br/>Zulässige Werte sind:<br/><b>- PreserveHierarchy (Standard)</b>: Behält die Dateihierarchie im Zielordner bei. Der relative Pfad der Quelldatei zum Quellordner entspricht dem relativen Pfad der Zieldatei zum Zielordner.<br/><b>- FlattenHierarchy</b>: Alle Dateien aus dem Quellordner befinden sich auf der ersten Ebene des Zielordners. Die Namen für die Zieldateien werden automatisch generiert. <br/><b>- MergeFiles</b>: Alle Dateien aus dem Quellordner werden in einer Datei zusammengeführt. Wenn der Dateiname angegeben wurde, entspricht der zusammengeführte Dateiname dem angegebenen Namen. Andernfalls wird der Dateiname automatisch generiert. | Nein |
| maxConcurrentConnections | Die Anzahl von Verbindungen, die gleichzeitig mit dem Datenspeicher hergestellt werden können. Geben Sie diesen Wert nur an, wenn Sie die gleichzeitigen Verbindungen mit dem Datenspeicher begrenzen möchten. | Nein |

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyToADLSGen2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen2 output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureBlobFSSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Beispiele für Ordner- und Dateifilter

Dieser Abschnitt beschreibt das resultierende Verhalten für den Ordnerpfad und den Dateinamen mit Platzhalterfiltern.

| folderPath | fileName | recursive | Quellordnerstruktur und Filterergebnis (Dateien mit **Fettformatierung** werden abgerufen.)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (leer, Standardwert verwenden) | false | OrdnerA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Datei1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Datei2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5.csv<br/>AndererOrdnerB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei6.csv |
| `Folder*` | (leer, Standardwert verwenden) | true | OrdnerA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Datei1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Datei2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei5.csv**<br/>AndererOrdnerB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei6.csv |
| `Folder*` | `*.csv` | false | OrdnerA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Datei1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5.csv<br/>AndererOrdnerB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei6.csv |
| `Folder*` | `*.csv` | true | OrdnerA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Datei1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei5.csv**<br/>AndererOrdnerB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei6.csv |

### <a name="some-recursive-and-copybehavior-examples"></a>Beispiele für „recursive“ und „copyBehavior“

Dieser Abschnitt beschreibt das resultierende Verhalten des Kopiervorgangs für verschiedene Kombinationen von rekursiven und CopyBehavior-Werten.

| recursive | copyBehavior | Struktur des Quellordners | Resultierendes Ziel |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Ordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei5 | Der Zielordner „Ordner1“ wird mit der gleichen Struktur erstellt wie die Quelle:<br/><br/>Ordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei5 |
| true |flattenHierarchy | Ordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei5 | Der Zielordner „Ordner1“ wird mit der folgenden Struktur erstellt: <br/><br/>Ordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch generierter Name für Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch generierter Name für Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch generierter Name für Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch generierter Name für Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch generierter Name für Datei5 |
| true |mergeFiles | Ordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei5 | Der Zielordner „Ordner1“ wird mit der folgenden Struktur erstellt: <br/><br/>Ordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Die Inhalte von Datei1 + Datei2 + Datei3 + Datei4 + Datei5 werden in einer Datei mit einem automatisch generierten Dateinamen zusammengeführt. |
| false |preserveHierarchy | Ordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei5 | Der Zielordner „Ordner1“ wird mit der folgenden Struktur erstellt: <br/><br/>Ordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/><br/>Unterordner1 mit Datei3, Datei4 und Datei5 wird nicht übernommen. |
| false |flattenHierarchy | Ordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei5 | Der Zielordner „Ordner1“ wird mit der folgenden Struktur erstellt: <br/><br/>Ordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch generierter Name für Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch generierter Name für Datei2<br/><br/>Unterordner1 mit Datei3, Datei4 und Datei5 wird nicht übernommen. |
| false |mergeFiles | Ordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;Datei5 | Der Zielordner „Ordner1“ wird mit der folgenden Struktur erstellt:<br/><br/>Ordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Die Inhalte von Datei1 + Datei2 werden in einer Datei mit einem automatisch generierten Dateinamen zusammengeführt. Automatisch generierter Name für Datei1<br/><br/>Unterordner1 mit Datei3, Datei4 und Datei5 wird nicht übernommen. |

## <a name="preserve-acls-from-data-lake-storage-gen1"></a>Bewahren von Zugriffssteuerungslisten für Azure Data Lake Storage Gen1

>[!TIP]
>Allgemeine Informationen zum Kopieren von Daten von Azure Data Lake Storage Gen1 in Gen2 sowie eine exemplarische Vorgehensweise und bewährte Methoden finden Sie unter [Kopieren von Daten aus Azure Data Lake Storage Gen1 in Gen2 mit Azure Data Factory](load-azure-data-lake-storage-gen2-from-gen1.md).

Beim Kopieren von Dateien von Azure Data Lake Storage (ADLS) Gen1 in Gen2 können Sie die Option wählen, bei der die POSIX-Zugriffssteuerungslisten (Access Control Lists, ACLs) zusammen mit den Daten beibehalten werden. Ausführlichere Informationen zur Zugriffssteuerung finden Sie unter [Zugriffssteuerung in Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md) und [Zugriffssteuerung in Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md).

Mit der Azure Data Factory-Kopieraktivität können die folgenden Arten von ACLs beibehalten werden. Sie können einen oder mehrere Typen auswählen:

- **ACL**: Kopieren und Beibehalten von **POSIX-Zugriffssteuerungslisten** in Dateien und Verzeichnissen. Hierbei werden die vollständigen vorhandenen ACLs von der Quelle in die Senke kopiert. 
- **Besitzer:** Kopieren und Beibehalten des **besitzenden Benutzers** von Dateien und Verzeichnissen. Hierfür ist Administratorzugriff auf die ADLS Gen2-Senke erforderlich.
- **Gruppe**: Kopieren und Beibehalten der **besitzenden Gruppe** von Dateien und Verzeichnissen. Hierfür ist Administratorzugriff auf die ADLS Gen2-Senke oder der Zugriff des besitzenden Benutzers erforderlich (sofern der besitzende Benutzer auch Mitglied der Zielgruppe ist).

Wenn Sie das Kopieren aus einem Ordner angeben, repliziert Data Factory die ACLs für diesen Ordner und die darin enthaltenen Dateien und Verzeichnisse (sofern `recursive` auf „true“ festgelegt ist). Falls Sie das Kopieren aus einer einzelnen Datei angeben, werden die ACLs in dieser Datei kopiert.

>[!IMPORTANT]
>Bei Auswahl der Beibehaltung von ACLs sollten Sie sicherstellen, dass Sie ausreichende Berechtigungen für die Verwendung von ADF für Ihr ADLS Gen2-Senkenkonto gewähren. Verwenden Sie beispielsweise die Kontoschlüsselauthentifizierung, oder weisen Sie dem Dienstprinzipal bzw. der verwalteten Identität die Rolle „Besitzer von Speicherblobdaten“ zu.

Wenn Sie die Quelle als ADLS Gen1 mit binärer Kopieroption bzw. binärem Format und die Senke als ADLS Gen2 mit binärer Kopieroption bzw. binärem Format konfigurieren, können Sie die Option **Beibehalten** auf der **Seite mit den Einstellungen für das Tool zum Kopieren von Daten** oder auf der Registerkarte **Kopieraktivität > Einstellungen** für die Aktivitätserstellung verwenden.

![ADLS Gen1 zu Gen2: ACL beibehalten](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

Hier ist ein Beispiel für die JSON-Konfiguration angegeben (siehe `preserve`): 

```json
"activities":[
    {
        "name": "CopyFromGen1ToGen2",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "AzureDataLakeStoreSource",
                "recursive": true
            },
            "sink": {
                "type": "AzureBlobFSSink",
                "copyBehavior": "PreserveHierarchy"
            },
            "preserve": [
                "ACL",
                "Owner",
                "Group"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Azure Data Lake Storage Gen1 input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Lake Storage Gen2 output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="mapping-data-flow-properties"></a>Eigenschaften von Mapping Data Flow

Ausführliche Informationen hierzu finden Sie unter [Quellentransformation](data-flow-source.md) und [Senkentransformation](data-flow-sink.md) in Mapping Data Flow.

## <a name="next-steps"></a>Nächste Schritte

Eine Liste der Datenspeicher, die als Quellen und Senken für die Kopieraktivität in Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md##supported-data-stores-and-formats).
