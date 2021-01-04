---
title: Kopieren und Transformieren von Daten in Azure Data Lake Storage Gen2
description: Hier erfahren Sie, wie Sie mithilfe von Azure Data Factory Daten in oder aus Azure Data Lake Storage Gen2 kopieren und Daten in Azure Data Lake Storage Gen2 transformieren.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/28/2020
ms.openlocfilehash: c0f7df8db79f549dfeca15e6411ae72cbe66d2bd
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97346281"
---
# <a name="copy-and-transform-data-in-azure-data-lake-storage-gen2-using-azure-data-factory"></a>Kopieren und Transformieren von Daten in Azure Data Lake Storage Gen2 mithilfe von Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Lake Storage Gen2 (ADLS Gen2) baut auf [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) auf und bietet eine Reihe von Funktionen für die Big Data-Analyse. So haben Sie eine Schnittstelle zu Ihren Daten sowohl über das Dateisystem als auch den Objektspeicher.

In diesem Artikel wird beschrieben, wie Sie Daten mithilfe der Kopieraktivität in Azure Data Factory aus und in Azure Data Lake Storage Gen2 kopieren sowie Daten mithilfe von Datenfluss in Azure Data Lake Storage Gen2 transformieren. Informationen zu Azure Data Factory finden Sie im [Einführungsartikel](introduction.md).

>[!TIP]
>Bei einem Data Lake- oder Data Warehouse-Migrationsszenario finden Sie weitere Informationen unter [Verwenden von Azure Data Factory zum Migrieren von Daten aus einem Data Lake oder Data Warehouse zu Azure](data-migration-guidance-overview.md).

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Dieser Azure Data Lake Storage Gen2-Connector wird für die folgenden Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen/Senken-Matrix](copy-activity-overview.md)
- [Mapping Data Flow](concepts-data-flow-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)
- [GetMetadata-Aktivität](control-flow-get-metadata-activity.md)
- [Delete-Aktivität](delete-activity.md)

Dieser Connector bietet Ihnen für die Kopieraktivität folgende Möglichkeiten:

- Kopieren von Daten aus/in Azure Data Lake Storage Gen2 durch Verwendung eines Kontoschlüssels, eines Dienstprinzipals oder verwalteter Identitäten für die Authentifizierung von Azure-Ressourcen.
- Kopieren von Dateien im jeweiligen Zustand oder Analysieren bzw. Generieren von Dateien mit [unterstützten Dateiformaten und Komprimierungscodecs](supported-file-formats-and-compression-codecs.md).
- [Beibehalten von Dateimetadaten beim Kopieren](#preserve-metadata-during-copy)
- [Beibehalten von ACLs](#preserve-acls) beim Kopieren aus Azure Data Lake Storage Gen1/Gen2

## <a name="get-started"></a>Erste Schritte

>[!TIP]
>Eine exemplarische Vorgehensweise zur Verwendung des Data Lake Storage Gen2-Connectors finden Sie unter [Laden von Daten in Azure Data Lake Storage Gen2](load-azure-data-lake-storage-gen2.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Die folgenden Abschnitte enthalten Informationen zu Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für Data Lake Storage Gen2 verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Der Azure Data Lake Storage Gen2-Connector unterstützt die folgenden Authentifizierungstypen. Weitere Informationen finden Sie in den entsprechenden Abschnitten:

- [Kontoschlüsselauthentifizierung](#account-key-authentication)
- [Dienstprinzipalauthentifizierung](#service-principal-authentication)
- [Verwaltete Identitäten für Azure-Ressourcenauthentifizierung](#managed-identity)

>[!NOTE]
>- Wenn Sie die öffentliche Azure Integration Runtime-Instanz zum Herstellen einer Verbindung mit Data Lake Storage Gen2 verwenden möchten, indem Sie in der Azure Storage-Firewall die Option **Vertrauenswürdigen Microsoft-Diensten den Zugriff auf dieses Speicherkonto erlauben** aktivieren, müssen Sie die [Authentifizierung per verwalteter Identität](#managed-identity) verwenden.
>- Wenn Sie Daten mit PolyBase oder der COPY-Anweisung in Azure Synapse Analytics laden und Ihre Quell- oder Staginginstanz von Data Lake Storage Gen2 mit einem Azure Virtual Network-Endpunkt konfiguriert wurde, müssen Sie die Authentifizierung per verwalteter Identität – wie für Synapse erforderlich – verwenden. Im Abschnitt [Verwaltete Identitäten für Azure-Ressourcenauthentifizierung](#managed-identity) sind weitere Konfigurationsvoraussetzungen aufgeführt.

### <a name="account-key-authentication"></a>Kontoschlüsselauthentifizierung

Für die Verwendung der Authentifizierung mit dem Speicherkontoschlüssel werden die folgenden Eigenschaften unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die „type“-Eigenschaft muss auf **AzureBlobFS** festgelegt sein. |Ja |
| url | Endpunkt für Data Lake Storage Gen2 im Format `https://<accountname>.dfs.core.windows.net`. | Ja |
| accountKey | Kontoschlüssel für Data Lake Storage Gen2. Markieren Sie dieses Feld als SecureString, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). |Ja |
| connectVia | Die [Integration Runtime](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden soll. Sie können die Azure Integration Runtime oder eine selbstgehostete Integration Runtime verwenden (sofern sich Ihr Datenspeicher in einem privaten Netzwerk befindet). Wenn diese Eigenschaft nicht angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. |Nein |

>[!NOTE]
>Der sekundäre ADLS-Dateisystem-Endpunkt wird bei Verwendung der Kontoschlüsselauthentifizierung nicht unterstützt. Sie können andere Authentifizierungstypen verwenden.

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

Zum Verwenden der Dienstprinzipalauthentifizierung führen Sie die folgenden Schritte aus.

1. Registrieren Sie eine Anwendungsentität in Azure Active Directory (Azure AD), indem Sie die Schritte unter [Registrieren der Anwendung bei einem Azure AD-Mandanten](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant) befolgen. Notieren Sie sich die folgenden Werte, die Sie zum Definieren des verknüpften Diensts verwenden:

    - Anwendungs-ID
    - Anwendungsschlüssel
    - Mandanten-ID

2. Erteilen Sie dem Dienstprinzipal geeignete Berechtigungen. Beispiele zur Funktionsweise von Berechtigungen in Data Lake Storage Gen2 finden Sie unter [Zugriffssteuerungslisten für Dateien und Verzeichnisse](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

    - **Als Quelle**: Weisen Sie im Storage-Explorer mindestens die Berechtigung **Ausführen** für ALLE Upstreamordner und das Dateisystem sowie die Berechtigung **Lesen** für die zu kopierenden Dateien zu. Weisen Sie alternativ in der Zugriffssteuerung (IAM) mindestens die Rolle **Storage-Blobdatenleser** zu.
    - **Als Senke**: Weisen Sie im Storage-Explorer mindestens die Berechtigung **Ausführen** für ALLE Upstreamordner und das Dateisystem sowie die Berechtigung **Schreiben** für den Senkenordner zu. Weisen Sie in der Zugriffssteuerung (IAM) mindestens die Rolle **Mitwirkender an Storage-Blobdaten** zu.

>[!NOTE]
>Wenn Sie die Data Factory-Benutzeroberfläche für die Erstellung verwenden und der Dienstprinzipal nicht mit der Rolle „Storage-Blobdatenleser/Mitwirkender an Storage-Blobdaten“ in IAM festgelegt ist, wählen Sie beim Testen der Verbindung oder beim Durchsuchen von/Navigieren in Ordnern die Option „Test connection to file path“ (Verbindung mit Dateipfad testen) bzw. „Browse from specified path“ (Von angegebenem Pfad suchen) aus. Geben Sie einen Pfad mit **Lese- und Ausführungsberechtigungen** an, um fortzufahren.

Diese Eigenschaften werden im verknüpften Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die „type“-Eigenschaft muss auf **AzureBlobFS** festgelegt sein. |Ja |
| url | Endpunkt für Data Lake Storage Gen2 im Format `https://<accountname>.dfs.core.windows.net`. | Ja |
| servicePrincipalId | Geben Sie die Client-ID der Anwendung an. | Ja |
| servicePrincipalCredentialType | Die Art von Anmeldeinformationen, die für die Authentifizierung beim Dienstprinzipal verwendet werden. Gültige Werte sind **ServicePrincipalKey** und **ServicePrincipalCert**. | Ja |
| servicePrincipalCredential | Die Anmeldeinformationen für den Dienstprinzipal. <br/> Wenn Sie **ServicePrincipalKey** als Anmeldeinformationstyp verwenden, geben Sie den Schlüssel der Anwendung an. Markieren Sie dieses Feld als **SecureString**, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). <br/> Wenn Sie **ServicePrincipalCert** als Anmeldeinformationen verwenden,verweisen Sie auf ein Zertifikat in Azure Key Vault. | Ja |
| servicePrincipalKey | Geben Sie den Schlüssel der Anwendung an. Markieren Sie dieses Feld als **SecureString**, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). <br/> Diese Eigenschaft wird weiterhin unverändert für `servicePrincipalId` + `servicePrincipalKey` unterstützt. Wenn ADF eine neue Dienstprinzipal-Zertifikatauthentifizierung hinzugefügt wird, ist das neue Modell für die Dienstprinzipalauthentifizierung `servicePrincipalId` + `servicePrincipalCredentialType` + `servicePrincipalCredential`. | Nein |
| tenant | Geben Sie die Mandanteninformationen (Domänenname oder Mandanten-ID) für Ihre Anwendung an. Diese können Sie abrufen, indem Sie im Azure-Portal mit der Maus auf den Bereich oben rechts zeigen. | Ja |
| azureCloudType | Geben Sie für die Dienstprinzipalauthentifizierung die Art der Azure-Cloudumgebung an, bei der Ihre Azure Active Directory-Anwendung registriert ist. <br/> Zulässige Werte sind **AzurePublic**, **AzureChina**, **AzureUsGovernment** und **AzureGermany**. Standardmäßig wird die Cloudumgebung der Data Factory verwendet. | Nein |
| connectVia | Die [Integration Runtime](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden soll. Sie können die Azure Integration Runtime oder eine selbstgehostete Integration Runtime verwenden (sofern sich Ihr Datenspeicher in einem privaten Netzwerk befindet). Wenn kein Wert angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. |Nein |

**Beispiel: Verwenden der Dienstprinzipal-Schlüsselauthentifizierung**

Sie können den Dienstprinzipalschlüssel auch in Azure Key Vault speichern.

```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalCredentialType": "ServicePrincipalKey",
            "servicePrincipalCredential": {
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

**Beispiel: Verwenden der Dienstprinzipal-Zertifikatauthentifizierung**
```json
{
    "name": "AzureDataLakeStorageGen2LinkedService",
    "properties": {
        "type": "AzureBlobFS",
        "typeProperties": {
            "url": "https://<accountname>.dfs.core.windows.net", 
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalCredentialType": "ServicePrincipalCert",
            "servicePrincipalCredential": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<AKV reference>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<certificate name in AKV>" 
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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Verwaltete Identitäten für Azure-Ressourcenauthentifizierung

Eine Data Factory kann einer [verwalteten Identität für Azure-Ressourcen](data-factory-service-identity.md) zugeordnet werden, die diese spezielle Data Factory darstellt. Ähnlich wie bei der Verwendung Ihres eigenen Dienstprinzipals können Sie diese verwaltete Identität direkt für die Data Lake Storage Gen2-Authentifizierung verwenden. Sie erlaubt dieser bestimmten Factory den Zugriff auf Ihren Data Lake Storage Gen2 sowie das Kopieren von Daten nach oder aus Data Lake Storage Gen2.

Um verwaltete Identitäten für die Azure-Ressourcenauthentifizierung zu verwenden, gehen Sie folgendermaßen vor.

1. [Rufen Sie die Informationen zur verwalteten Data Factory-Identität ab](data-factory-service-identity.md#retrieve-managed-identity), indem Sie den Wert von **Objekt-ID der verwalteten Identität** kopieren, der zusammen mit Ihrer Factory generiert wurde.

2. Erteilen Sie der verwalteten Entität geeignete Berechtigungen. Beispiele zur Funktionsweise von Berechtigungen in Data Lake Storage Gen2 finden Sie unter [Zugriffssteuerungslisten für Dateien und Verzeichnisse](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

    - **Als Quelle**: Weisen Sie im Storage-Explorer mindestens die Berechtigung **Ausführen** für ALLE Upstreamordner und das Dateisystem sowie die Berechtigung **Lesen** für die zu kopierenden Dateien zu. Weisen Sie alternativ in der Zugriffssteuerung (IAM) mindestens die Rolle **Storage-Blobdatenleser** zu.
    - **Als Senke**: Weisen Sie im Storage-Explorer mindestens die Berechtigung **Ausführen** für ALLE Upstreamordner und das Dateisystem sowie die Berechtigung **Schreiben** für den Senkenordner zu. Weisen Sie in der Zugriffssteuerung (IAM) mindestens die Rolle **Mitwirkender an Storage-Blobdaten** zu.

>[!NOTE]
>Wenn Sie die Data Factory-Benutzeroberfläche für die Erstellung verwenden und die verwaltete Identität nicht mit der Rolle „Storage-Blobdatenleser/Mitwirkender an Storage-Blobdaten“ in IAM festgelegt ist, wählen Sie beim Testen der Verbindung oder beim Durchsuchen von/Navigieren in Ordnern die Option „Test connection to file path“ (Verbindung mit Dateipfad testen) bzw. „Browse from specified path“ (Von angegebenem Pfad suchen) aus. Geben Sie einen Pfad mit **Lese- und Ausführungsberechtigungen** an, um fortzufahren.

>[!IMPORTANT]
>Wenn Sie Daten mithilfe von PolyBase oder der COPY-Anweisung aus Data Lake Storage Gen2 in Azure Synapse Analytics laden und dazu die Authentifizierung per verwalteter Identität für Data Lake Storage Gen2 verwenden, müssen Sie unbedingt auch die Schritte 1 bis 3 in [dieser Anleitung](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage) befolgen. Mit diesen Schritten wird der Server bei Azure AD registriert, und Ihrem Server wird die Rolle „Mitwirkender an Storage-Blobdaten“ zugewiesen. Data Factory kümmert sich um den Rest. Wenn Sie Blob Storage mit einem Azure Virtual Network-Endpunkt konfigurieren, muss außerdem im Einstellungsmenü **Firewalls und virtuelle Netzwerke** des Azure Storage-Kontos die Option **Vertrauenswürdigen Microsoft-Diensten den Zugriff auf dieses Speicherkonto erlauben** aktiviert sein (wie für Synapse erforderlich).

Diese Eigenschaften werden im verknüpften Dienst unterstützt:

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| type | Die „type“-Eigenschaft muss auf **AzureBlobFS** festgelegt sein. |Ja |
| url | Endpunkt für Data Lake Storage Gen2 im Format `https://<accountname>.dfs.core.windows.net`. | Ja |
| connectVia | Die [Integration Runtime](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden soll. Sie können die Azure Integration Runtime oder eine selbstgehostete Integration Runtime verwenden (sofern sich Ihr Datenspeicher in einem privaten Netzwerk befindet). Wenn kein Wert angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. |Nein |

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

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Folgende Eigenschaften werden für Data Lake Storage Gen2 unter `location`-Einstellungen in formatbasierten Datasets unterstützt:

| Eigenschaft   | Beschreibung                                                  | Erforderlich |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Die „type“-Eigenschaft unter `location` im Dataset muss auf **AzureBlobFSLocation** festgelegt werden. | Ja      |
| fileSystem | Der Name des Data Lake Storage Gen2-Dateisystems.                              | Nein       |
| folderPath | Der Pfad zu einem Ordner im angegebenen Dateisystem. Wenn Sie einen Platzhalter verwenden möchten, um Ordner zu filtern, überspringen Sie diese Einstellung, und geben Sie entsprechende Aktivitätsquelleneinstellungen an. | Nein       |
| fileName   | Der Dateiname im angegebenen „fileSystem“ und „folderPath“. Wenn Sie einen Platzhalter verwenden möchten, um Dateien zu filtern, überspringen Sie diese Einstellung, und geben Sie ihn in den entsprechenden Aktivitätsquelleneinstellungen an. | Nein       |

**Beispiel:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Data Lake Storage Gen2 linked service name>",
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

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie unter [Konfigurationen für die Kopieraktivität](copy-activity-overview.md#configuration) und [Pipelines und Aktivitäten](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der Data Lake Storage Gen2-Quelle und -Senke unterstützt werden.

### <a name="azure-data-lake-storage-gen2-as-a-source-type"></a>Azure Data Lake Storage Gen2 als Quelltyp

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Sie haben mehrere Optionen zum Kopieren von Daten aus ADLS Gen2:

- Kopieren Sie aus dem im Dataset angegebenen Pfad.
- Den Platzhalterfilter für Ordnerpfad oder Dateiname finden Sie unter `wildcardFolderPath` und `wildcardFileName`.
- Kopieren Sie die in einer bestimmten Textdatei definierten Dateien als Dateigruppe (siehe `fileListPath`).

Folgende Eigenschaften werden für Data Lake Storage Gen2 unter `storeSettings`-Einstellungen in der formatbasierten Kopierquelle unterstützt:

| Eigenschaft                 | Beschreibung                                                  | Erforderlich                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Die „type“-Eigenschaft unter `storeSettings` muss auf **AzureBlobFSReadSettings** festgelegt werden. | Ja                                           |
| **_Suchen nach den zu kopierenden Dateien:_* |  |  |
| OPTION 1: statischer Pfad<br> | Kopieren Sie aus dem im Dataset angegebenen Dateisystem oder Ordner/Dateipfad. Wenn Sie alle Dateien aus einem Dateisystem/Ordner kopieren möchten, geben Sie außerdem für `wildcardFileName` den Wert `_` an. |  |
| OPTION 2: Platzhalter<br>– wildcardFolderPath | Der in Datasets zum Filtern von Quellordnern konfigurierte Ordnerpfad mit Platzhalterzeichen im angegebenen Dateisystem. <br>Zulässige Platzhalter sind: `*` (entspricht null oder mehr Zeichen) und `?` (entspricht null oder einem einzelnen Zeichen). Verwenden Sie `^` als Escapezeichen, wenn Ihr tatsächlicher Dateiname einen Platzhalter oder dieses Escapezeichen enthält. <br>Weitere Beispiele finden Sie unter [Beispiele für Ordner- und Dateifilter](#folder-and-file-filter-examples). | Nein                                            |
| OPTION 2: Platzhalter<br>– wildcardFileName | Der Dateiname mit Platzhalterzeichen im angegebenen Dateisystem und „folderPath/wildcardFolderPath“ zum Filtern von Quelldateien. <br>Zulässige Platzhalter sind: `*` (entspricht null oder mehr Zeichen) und `?` (entspricht null oder einem einzelnen Zeichen). Verwenden Sie `^` als Escapezeichen, wenn Ihr tatsächlicher Dateiname einen Platzhalter oder dieses Escapezeichen enthält.  Weitere Beispiele finden Sie unter [Beispiele für Ordner- und Dateifilter](#folder-and-file-filter-examples). | Ja |
| OPTION 3: eine Liste von Dateien<br>– fileListPath | Gibt an, dass eine bestimmte Dateigruppe kopiert werden soll. Verweisen Sie auf eine Textdatei, die eine Liste der zu kopierenden Dateien enthält, und zwar eine Datei pro Zeile. Dies ist der relative Pfad zu dem im Dataset konfigurierten Pfad.<br/>Wenn Sie diese Option verwenden, dürfen Sie keinen Dateinamen im Dataset angeben. Weitere Beispiele finden Sie unter [Beispiele für Dateilisten](#file-list-examples). |Nein |
| ***Zusätzliche Einstellungen:** |  | |
| recursive | Gibt an, ob die Daten rekursiv aus den Unterordnern oder nur aus dem angegebenen Ordner gelesen werden. Beachten Sie Folgendes: Wenn „recursive“ auf „true“ festgelegt ist und es sich bei der Senke um einen dateibasierten Speicher handelt, wird ein leerer Ordner oder Unterordner nicht in die Senke kopiert und dort auch nicht erstellt. <br>Zulässige Werte sind *true** (Standard) und **false**.<br>Diese Eigenschaft gilt nicht, wenn Sie `fileListPath` konfigurieren. |Nein |
| deleteFilesAfterCompletion | Gibt an, ob die Binärdateien nach dem erfolgreichen Verschieben in den Zielspeicher aus dem Quellspeicher gelöscht werden. Die Dateien werden einzeln gelöscht, sodass Sie bei einem Fehler der Kopieraktivität feststellen werden, dass einige Dateien bereits ins Ziel kopiert und aus der Quelle gelöscht wurden, wohingegen sich andere weiter im Quellspeicher befinden. <br/>Diese Eigenschaft ist nur im Szenario zum Kopieren von Binärdateien gültig. Standardwert: FALSE. |Nein |
| modifiedDatetimeStart    | Dateifilterung basierend auf dem Attribut: Letzte Änderung. <br>Die Dateien werden ausgewählt, wenn der Zeitpunkt der letzten Änderung innerhalb des Zeitbereichs zwischen `modifiedDatetimeStart` und `modifiedDatetimeEnd` liegt. Die Zeit wird auf die UTC-Zeitzone im Format „2018-12-01T05:00:00Z“ angewandt. <br> Die Eigenschaften können NULL sein, was bedeutet, dass kein Dateiattributfilter auf das Dataset angewandt wird.  Wenn `modifiedDatetimeStart` den datetime-Wert aufweist, aber `modifiedDatetimeEnd` NULL ist, bedeutet dies, dass die Dateien ausgewählt werden, deren Attribut für die letzte Änderung größer oder gleich dem datetime-Wert ist.  Wenn `modifiedDatetimeEnd` den datetime-Wert aufweist, aber `modifiedDatetimeStart` NULL ist, bedeutet dies, dass die Dateien ausgewählt werden, deren Attribut für die letzte Änderung kleiner als der datetime-Wert ist.<br/>Diese Eigenschaft gilt nicht, wenn Sie `fileListPath` konfigurieren. | Nein                                            |
| modifiedDatetimeEnd      | Wie oben.                                               | Nein                                            |
| enablePartitionDiscovery | Geben Sie bei partitionierten Dateien an, ob die Partitionen anhand des Dateipfads analysiert und als zusätzliche Quellspalten hinzugefügt werden sollen.<br/>Zulässige Werte sind **false** (Standard) und **true**. | Nein                                            |
| partitionRootPath | Wenn die Partitionsermittlung aktiviert ist, geben Sie den absoluten Stammpfad an, um partitionierte Ordner als Datenspalten zu lesen.<br/><br/>Ohne Angabe gilt standardmäßig Folgendes:<br/>- Wenn Sie den Dateipfad im Dataset oder die Liste der Dateien in der Quelle verwenden, ist der Partitionsstammpfad der im Dataset konfigurierte Pfad.<br/>Wenn Sie einen Platzhalterordnerfilter verwenden, ist der Stammpfad der Partition der Unterpfad vor dem ersten Platzhalter.<br/><br/>Angenommen, Sie konfigurieren den Pfad im Dataset als „root/folder/year=2020/month=08/day=27“:<br/>- Wenn Sie den Stammpfad der Partition als „root/folder/year=2020“ angeben, generiert die Kopieraktivität zusätzlich zu den Spalten in den Dateien die beiden weiteren Spalten `month` und `day` mit den Werten „08“ bzw. „27“.<br/>- Wenn kein Stammpfad für die Partition angegeben ist, wird keine zusätzliche Spalte generiert. | Nein                                            |
| maxConcurrentConnections | Die Anzahl von Verbindungen, die gleichzeitig mit einem Speicher hergestellt werden können. Geben Sie diesen Wert nur an, wenn Sie die gleichzeitigen Verbindungen mit dem Datenspeicher begrenzen möchten. | Nein                                            |

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
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureBlobFSReadSettings",
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

### <a name="azure-data-lake-storage-gen2-as-a-sink-type"></a>Azure Data Lake Storage Gen2 als Senkentyp

[!INCLUDE [data-factory-v2-file-sink-formats](../../includes/data-factory-v2-file-sink-formats.md)]

Folgende Eigenschaften werden für Data Lake Storage Gen2 unter `storeSettings`-Einstellungen in formatbasierten Kopiersenken unterstützt:

| Eigenschaft                 | Beschreibung                                                  | Erforderlich |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | Die „type“-Eigenschaft unter `storeSettings` muss auf **AzureBlobFSWriteSettings** festgelegt werden. | Ja      |
| copyBehavior             | Definiert das Kopierverhalten, wenn es sich bei der Quelle um Dateien aus einem dateibasierten Datenspeicher handelt.<br/><br/>Zulässige Werte sind:<br/><b>- PreserveHierarchy (Standard)</b>: Behält die Dateihierarchie im Zielordner bei. Der relative Pfad der Quelldatei zum Quellordner ist mit dem relativen Pfad der Zieldatei zum Zielordner identisch.<br/><b>- FlattenHierarchy</b>: Alle Dateien aus dem Quellordner befinden sich auf der ersten Ebene des Zielordners. Die Namen für die Zieldateien werden automatisch generiert. <br/><b>- MergeFiles</b>: Alle Dateien aus dem Quellordner werden in einer Datei zusammengeführt. Wenn der Dateiname angegeben wurde, entspricht der zusammengeführte Dateiname dem angegebenen Namen. Andernfalls wird der Dateiname automatisch generiert. | Nein       |
| blockSizeInMB | Geben Sie die Blockgröße, die zum Schreiben von Daten in ADLS Gen2 verwendet wird, in MB an. Informieren Sie sich ausführlicher über [Blockblobs](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs). <br/>Der zulässige Wert liegt **zwischen 4 und 100 MB**. <br/>Standardmäßig bestimmt ADF automatisch die Blockgröße, basierend auf dem Quellspeichertyp und den Daten. Bei einer nicht binären Kopie in ADLS Gen2 beträgt die Standardblockgröße 100 MB, damit sie in maximal 4,95 TB Daten passt. Dies ist möglicherweise nicht optimal, wenn Ihre Daten nicht groß sind – insbesondere, wenn Sie eine selbstgehostete Integration Runtime bei einem unzureichenden Netzwerk verwenden, was zu einem Timeout bei Vorgang oder einem Leistungsproblem führt. Sie können eine Blockgröße explizit angeben und gleichzeitig sicherstellen, dass „blockSizeInMB*50000“ groß genug zum Speichern der Daten ist. Andernfalls tritt bei Ausführung der Kopieraktivität ein Fehler auf. | Nein |
| maxConcurrentConnections | Die Anzahl von Verbindungen, die gleichzeitig mit dem Datenspeicher hergestellt werden können. Geben Sie diesen Wert nur an, wenn Sie die gleichzeitigen Verbindungen mit dem Datenspeicher begrenzen möchten. | Nein       |

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
                    "type": "AzureBlobFSWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Beispiele für Ordner- und Dateifilter

Dieser Abschnitt beschreibt das sich ergebende Verhalten für den Ordnerpfad und den Dateinamen mit Platzhalterfiltern.

| folderPath | fileName | recursive | Quellordnerstruktur und Filterergebnis (Dateien mit **Fettformatierung** werden abgerufen.)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (Leer, Standardwert verwenden) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Datei1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Datei2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5.csv<br/>AndererOrdnerB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei6.csv |
| `Folder*` | (Leer, Standardwert verwenden) | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Datei1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Datei2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei5.csv**<br/>AndererOrdnerB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Datei1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5.csv<br/>AndererOrdnerB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Datei1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei5.csv**<br/>AndererOrdnerB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei6.csv |

### <a name="file-list-examples"></a>Beispiele für Dateilisten

In diesem Abschnitt wird das resultierende Verhalten beschrieben, wenn der Dateilistenpfad in der Quelle der Kopieraktivität verwendet wird.

Angenommen, Sie haben die folgende Quellordnerstruktur und möchten die Dateien kopieren, deren Namen fett formatiert sind:

| Beispielquellstruktur                                      | Inhalt in „FileListToCopy.txt“                             | ADF-Konfiguration                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| filesystem<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadaten<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | Datei1.csv<br>Unterordner1/Datei3.csv<br>Unterordner1/Datei5.csv | **Im Dataset:**<br>– Dateisystem: `filesystem`<br>– Ordnerpfad: `FolderA`<br><br>**In der Quelle der Kopieraktivität:**<br>– Dateilistenpfad: `filesystem/Metadata/FileListToCopy.txt` <br><br>Der Dateilistenpfad verweist auf eine Textdatei im selben Datenspeicher, der eine Liste der zu kopierenden Dateien enthält, und zwar eine Datei pro Zeile. Diese enthält den relativen Pfad zu dem im Dataset konfigurierten Pfad. |


### <a name="some-recursive-and-copybehavior-examples"></a>Beispiele für „recursive“ und „copyBehavior“

Dieser Abschnitt beschreibt das resultierende Verhalten des Kopiervorgangs für verschiedene Kombinationen von „recursive“- und „copyBehavior“-Werten.

| recursive | copyBehavior | Struktur des Quellordners | Resultierendes Ziel |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5 | Der Zielordner „Ordner1“ wird mit der gleichen Struktur erstellt wie die Quelle:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5 | Der Zielordner „Ordner1“ wird mit der folgenden Struktur erstellt: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch generierter Name für Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch generierter Name für Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch generierter Name für Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch generierter Name für Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch generierter Name für Datei5 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5 | Der Zielordner „Ordner1“ wird mit der folgenden Struktur erstellt: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Die Inhalte von Datei1 + Datei2 + Datei3 + Datei4 + Datei5 werden in einer Datei mit einem automatisch generierten Dateinamen zusammengeführt. |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5 | Der Zielordner „Ordner1“ wird mit der folgenden Struktur erstellt: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/><br/>Unterordner1 mit Datei3, Datei4 und Datei5 wird nicht übernommen. |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5 | Der Zielordner „Ordner1“ wird mit der folgenden Struktur erstellt: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch generierter Name für Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch generierter Name für Datei2<br/><br/>Unterordner1 mit Datei3, Datei4 und Datei5 wird nicht übernommen. |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5 | Der Zielordner „Ordner1“ wird mit der folgenden Struktur erstellt: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Die Inhalte von Datei1 + Datei2 werden in einer Datei mit einem automatisch generierten Dateinamen zusammengeführt. Automatisch generierter Name für Datei1<br/><br/>Unterordner1 mit Datei3, Datei4 und Datei5 wird nicht übernommen. |

## <a name="preserve-metadata-during-copy"></a>Beibehalten von Metadaten beim Kopieren

Beim Kopieren von Dateien von Amazon S3/Azure Blob/Azure Data Lake Storage Gen2 nach Azure Data Lake Storage Gen2/Azure Blob können Sie festlegen, dass die Dateimetadaten zusätzlich zu den Daten beibehalten werden sollen. Weitere Informationen finden Sie unter [Beibehalten von Metadaten](copy-activity-preserve-metadata.md#preserve-metadata).

## <a name="preserve-acls-from-data-lake-storage-gen1gen2"></a><a name="preserve-acls"></a> Beibehalten von Zugriffssteuerungslisten aus Data Lake Storage Gen1/Gen2

Beim Kopieren von Dateien aus Azure Data Lake Storage Gen1/Gen2 in Gen2 können Sie die Option wählen, bei der die POSIX-Zugriffssteuerungslisten (Access Control Lists, ACLs) zusammen mit den Daten beibehalten werden. Weitere Informationen finden Sie unter [Beibehalten von ACLs aus Data Lake Storage Gen1/Gen2 in Gen2](copy-activity-preserve-metadata.md#preserve-acls).

>[!TIP]
>Allgemeine Informationen zum Kopieren von Daten von Azure Data Lake Storage Gen1 in Gen2 sowie eine exemplarische Vorgehensweise und bewährte Methoden finden Sie unter [Kopieren von Daten aus Azure Data Lake Storage Gen1 in Gen2 mit Azure Data Factory](load-azure-data-lake-storage-gen2-from-gen1.md).

## <a name="mapping-data-flow-properties"></a>Eigenschaften von Mapping Data Flow

Wenn Sie Daten in Zuordnungsdatenflüsse transformieren, können Sie Dateien aus Azure Data Lake Storage Gen2 in den folgenden Formaten lesen und schreiben:
* [Avro](format-avro.md#mapping-data-flow-properties)
* [Common Data Model (Vorschau)](format-common-data-model.md#mapping-data-flow-properties)
* [Text mit Trennzeichen](format-delimited-text.md#mapping-data-flow-properties)
* [Delta](format-delta.md#mapping-data-flow-properties)
* [Excel](format-excel.md#mapping-data-flow-properties)
* [JSON](format-json.md#mapping-data-flow-properties)
* [Parquet](format-parquet.md#mapping-data-flow-properties)

Formatspezifische Einstellungen finden Sie in der Dokumentation für das jeweilige Format. Weitere Informationen finden Sie unter [Quelltransformation in einem Zuordnungsdatenfluss](data-flow-source.md) und [Senkentransformation in einem Zuordnungsdatenfluss](data-flow-sink.md).

### <a name="source-transformation"></a>Quellentransformation

Bei der Quellentransformation können Sie Daten in Azure Data Lake Storage Gen2 aus einem Container, einem Ordner oder einer einzelnen Datei auslesen. Über die Registerkarte **Quellenoptionen** können Sie verwalten, wie die Dateien gelesen werden. 

![Quelloptionen](media/data-flow/sourceOptions1.png "Quelloptionen")

**Platzhalterpfad**: Mithilfe eines Platzhaltermusters wird ADF angewiesen, die einzelnen übereinstimmenden Ordner und Dateien in einer einzigen Quelltransformation zu durchlaufen. Dies ist eine effektive Methode zur Verarbeitung von mehreren Dateien in einem einzigen Datenfluss. Mit dem Pluszeichen (+), das angezeigt wird, wenn Sie mit dem Cursor auf Ihr vorhandenes Platzhaltermuster zeigen, können Sie weitere Platzhaltermuster hinzufügen.

Wählen Sie in Ihrem Quellcontainer eine Reihe von Dateien aus, die einem Muster entsprechen. Nur der Container kann im Dataset angegeben werden. Daher muss Ihr Platzhalterpfad auch den Ordnerpfad des Stammordners enthalten.

Beispiele für Platzhalter:

* ```*```: Stellt eine beliebige Zeichenfolge dar
* ```**```: Stellt rekursive Verzeichnisschachtelung dar
* ```?```: Ersetzt ein Zeichen
* ```[]```: Stimmt mit einem oder mehreren Zeichen in den Klammern überein

* ```/data/sales/**/*.csv```: Ruft alle CSV-Dateien unter „/data/sales“ ab
* ```/data/sales/20??/**/```: Ruft alle Dateien aus dem 20. Jahrhundert ab
* ```/data/sales/*/*/*.csv```: Ruft CSV-Dateien auf zwei Ebenen unter „/data/sales“ ab
* ```/data/sales/2004/*/12/[XY]1?.csv```: Ruft alle CSV-Dateien aus Dezember 2004 ab, die mit X oder Y und einer zweistelligen Zahl als Präfix beginnen

**Partitionsstammpfad**: Wenn Ihre Dateiquelle partitionierte Ordner mit dem Format ```key=value``` (z.B. „Jahr=2019“) enthält, können Sie die oberste Ebene dieser Ordnerstruktur einem Spaltennamen in Ihrem Datenfluss-Datenstrom zuweisen.

Legen Sie zunächst einen Platzhalter fest, um darin alle Pfade, die die partitionierten Ordner sind, sowie die zu lesenden Blattdateien einzuschließen.

![Einstellungen für die Partitionsquelldatei](media/data-flow/partfile2.png "Einstellung der Partitionsdatei")

Verwenden Sie die Einstellung „Partitionsstammpfad“, um zu definieren, was die oberste Ebene der Ordnerstruktur ist. Wenn Sie die Inhalte Ihrer Daten über die Datenvorschau anzeigen, sehen Sie, dass ADF die aufgelösten Partitionen hinzufügen wird, die auf den einzelnen Ordnerebenen gefunden werden.

![Partitionsstammpfad](media/data-flow/partfile1.png "Vorschau des Partitionsstammpfads")

**Liste der Dateien**: Dies ist eine Dateigruppe. Erstellen Sie eine Textdatei mit einer Liste der relativen Pfade der zu verarbeitenden Dateien. Verweisen Sie auf diese Textdatei.

**Spalte für die Speicherung im Dateinamen**: Speichern Sie den Namen der Quelldatei in einer Spalte in den Daten. Geben Sie hier einen neuen Spaltennamen ein, um die Zeichenfolge für den Dateinamen zu speichern.

**Nach der Fertigstellung**: Wählen Sie aus, ob Sie nach dem Ausführen des Datenflusses nichts mit der Quelldatei anstellen, die Quelldatei löschen oder die Quelldateien verschieben möchten. Die Pfade für das Verschieben sind relative Pfade.

Um Quelldateien an einen anderen Speicherort nach der Verarbeitung zu verschieben, wählen Sie zuerst für den Dateivorgang die Option „Verschieben“ aus. Legen Sie dann das Quellverzeichnis („from“/„aus“) fest. Wenn Sie keine Platzhalter für Ihren Pfad verwenden, entspricht die Einstellung „from“ dem Quellordner.

Wenn Sie über einen Quellpfad mit Platzhalter verfügen, sieht Ihre Syntax ähnlich wie hier aus:

```/data/sales/20??/**/*.csv```

Geben Sie „from“ beispielsweise wie folgt an:

```/data/sales```

Und „to“ können Sie wie folgt angeben:

```/backup/priorSales```

In diesem Fall werden alle Dateien, die aus „/Data/Sales“ erstellt wurden, in „/Backup/priorSales“ verschoben.

> [!NOTE]
> Die Dateivorgänge werden nur ausgeführt, wenn der Datenfluss anhand der Aktivität zum Ausführen des Datenflusses in einer Pipeline über eine Pipelineausführung ausgeführt wird (Debuggen der Pipeline oder Ausführung). Dateivorgänge werden *nicht* im Datenfluss-Debugmodus ausgeführt.

**Nach der letzten Änderung filtern**: Sie können einen Datumsbereich angeben, um die zu verarbeitenden Dateien nach der letzten Änderung zu filtern. Alle Zeitangaben sind in UTC. 

### <a name="sink-properties"></a>Senkeneigenschaften

In der Senkentransformation können Sie in Azure Data Lake Storage Gen2 in einen Container oder Ordner schreiben. Über die Registerkarte **Einstellungen** können Sie verwalten, wie die Dateien geschrieben werden.

![Senkenoptionen](media/data-flow/file-sink-settings.png "Senkenoptionen")

**Ordner löschen:** Bestimmt, ob der Zielordner vor dem Schreiben der Daten gelöscht wird.

**Dateinamenoption:** Bestimmt, wie die Zieldateien im Zielordner benannt werden. Es gibt folgende Dateinamenoptionen:
   * **Standard:** Lassen Sie zu, dass Spark Dateien basierend auf den PART-Standards benennt.
   * **Muster:** Geben Sie ein Muster ein, das Ihre Ausgabedateien pro Partition aufführt. Zum Beispiel erstellt **loans[n].csv** die Dateien „loans1.csv“, „loans2.csv“ usw.
   * **Pro Partition:** Geben Sie einen Dateinamen pro Partition ein.
   * **Wie Daten in Spalte:** Legen Sie die Ausgabedatei auf den Wert einer Spalte fest. Der Pfad ist relativ zum Datasetcontainer und nicht zum Zielordner. Wenn Ihr Dataset einen Ordnerpfad enthält, wird er überschrieben.
   * **Ausgabe in eine einzelne Datei:** Mit dieser Option werden die partitionierten Ausgabedateien in einer einzelnen Datei kombiniert. Der Pfad ist relativ zum Datasetordner. Bedenken Sie, dass der Zusammenführungsvorgang je nach Knotengröße zu Fehlern führen kann. Diese Option wird für große Datasets nicht empfohlen.

**Alle in Anführungszeichen:** Bestimmt, ob alle Werte in Anführungszeichen eingeschlossen werden sollen.

## <a name="lookup-activity-properties"></a>Eigenschaften der Lookup-Aktivität

Ausführliche Informationen zu den Eigenschaften finden Sie unter [Lookup-Aktivität](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Eigenschaften der GetMetadata-Aktivität

Ausführliche Informationen zu den Eigenschaften finden Sie unter [GetMetadata-Aktivität](control-flow-get-metadata-activity.md). 

## <a name="delete-activity-properties"></a>Eigenschaften der Delete-Aktivität

Ausführliche Informationen zu den Eigenschaften finden Sie unter [Delete-Aktivität](delete-activity.md).

## <a name="legacy-models"></a>Legacy-Modelle

>[!NOTE]
>Die folgenden Modelle werden aus Gründen der Abwärtskompatibilität weiterhin unverändert unterstützt. Es wird jedoch empfohlen, in Zukunft das in den obigen Abschnitten erwähnte neue Modell zu verwenden, da das neue Modell nun von der ADF-Benutzeroberfläche für die Erstellung generiert wird.

### <a name="legacy-dataset-model"></a>Legacy-Datasetmodell

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| type | Die „type“-Eigenschaft des Datasets muss auf **AzureBlobFSFile** festgelegt sein. |Ja |
| folderPath | Pfad zum Ordner in Data Lake Storage Gen2. Wenn keine Angabe vorhanden ist, wird auf das Stammverzeichnis verwiesen. <br/><br/>Der Platzhalterfilter wird unterstützt. Folgende Platzhalter sind zulässig: `*` (entspricht null [0] oder mehr Zeichen) und `?` (entspricht null [0] oder einem einzelnen Zeichen). Verwenden Sie `^` als Escapezeichen, wenn der tatsächliche Ordnername einen Platzhalter oder dieses Escapezeichen enthält. <br/><br/>Beispiele: „Dateisystem/Ordner/“. Weitere Beispiele finden Sie unter [Beispiele für Ordner- und Dateifilter](#folder-and-file-filter-examples). |Nein |
| fileName | Name oder Platzhalterfilter für die Dateien unter dem angegebenen Wert für „folderPath“. Wenn Sie für diese Eigenschaft keinen Wert angeben, verweist das Dataset auf alle Dateien im Ordner. <br/><br/>Für Filter sind die Platzhalter `*` (entspricht null [0] oder mehr Zeichen) und `?` (entspricht null [0] oder einem einzelnen Zeichen) zulässig.<br/>- Beispiel 1: `"fileName": "*.csv"`<br/>- Beispiel 2: `"fileName": "???20180427.txt"`<br/>Verwenden Sie `^` als Escapezeichen, wenn der tatsächliche Dateiname einen Platzhalter oder dieses Escapezeichen enthält.<br/><br/>Wenn „fileName“ nicht für ein Ausgabedataset und **preserveHierarchy** nicht in der Aktivitätssenke angegeben sind, generiert die Kopieraktivität den Dateinamen automatisch mit dem folgenden Muster: „*Data.[GUID der Aktivitätsausführungs-ID].[GUID, sofern „FlattenHierarchy“].[Format, sofern konfiguriert].[Komprimierung, sofern konfiguriert]* “, z.B. „Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz“. Wenn Sie Daten aus einer Quelle im Tabellenformat kopieren und dabei anstelle einer Abfrage den Tabellennamen verwenden, lautet das Namensmuster „ *[Tabellenname].[Format].[Komprimierung, sofern konfiguriert]* “, z.B. „MyTable.csv“. |Nein |
| modifiedDatetimeStart | Dateifilterung basierend auf dem Attribut „Letzte Änderung“. Die Dateien werden ausgewählt, wenn der Zeitpunkt der letzten Änderung innerhalb des Zeitbereichs zwischen `modifiedDatetimeStart` und `modifiedDatetimeEnd` liegt. Die Zeit wird auf die UTC-Zeitzone im Format „2018-12-01T05:00:00Z“ angewandt. <br/><br/> Die generelle Leistung der Datenverschiebung wird beeinträchtigt, wenn Sie diese Einstellung aktivieren und eine Dateifilterung für eine große Zahl von Dateien vornehmen möchten. <br/><br/> Die Eigenschaften können NULL sein, was bedeutet, dass kein Dateiattributfilter auf das Dataset angewandt wird. Wenn `modifiedDatetimeStart` einen datetime-Wert aufweist, aber `modifiedDatetimeEnd` NULL ist, bedeutet dies, dass die Dateien ausgewählt werden, deren Attribut für die letzte Änderung größer oder gleich dem datetime-Wert ist. Wenn `modifiedDatetimeEnd` einen datetime-Wert aufweist, aber `modifiedDatetimeStart` NULL ist, bedeutet dies, dass die Dateien ausgewählt werden, deren Attribut für die letzte Änderung kleiner als der datetime-Wert ist.| Nein |
| modifiedDatetimeEnd | Dateifilterung basierend auf dem Attribut „Letzte Änderung“. Die Dateien werden ausgewählt, wenn der Zeitpunkt der letzten Änderung innerhalb des Zeitbereichs zwischen `modifiedDatetimeStart` und `modifiedDatetimeEnd` liegt. Die Zeit wird auf die UTC-Zeitzone im Format „2018-12-01T05:00:00Z“ angewandt. <br/><br/> Die generelle Leistung der Datenverschiebung wird beeinträchtigt, wenn Sie diese Einstellung aktivieren und eine Dateifilterung für eine große Zahl von Dateien vornehmen möchten. <br/><br/> Die Eigenschaften können NULL sein, was bedeutet, dass kein Dateiattributfilter auf das Dataset angewandt wird. Wenn `modifiedDatetimeStart` einen datetime-Wert aufweist, aber `modifiedDatetimeEnd` NULL ist, bedeutet dies, dass die Dateien ausgewählt werden, deren Attribut für die letzte Änderung größer oder gleich dem datetime-Wert ist. Wenn `modifiedDatetimeEnd` einen datetime-Wert aufweist, aber `modifiedDatetimeStart` NULL ist, bedeutet dies, dass die Dateien ausgewählt werden, deren Attribut für die letzte Änderung kleiner als der datetime-Wert ist.| Nein |
| format | Wenn Sie Dateien unverändert zwischen dateibasierten Speichern kopieren möchten (binäre Kopie), überspringen Sie den Formatabschnitt in den Definitionen der Eingabe- und Ausgabedatasets.<br/><br/>Für das Analysieren oder Generieren von Dateien mit einem bestimmten Format werden die folgenden Dateiformattypen unterstützt: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** und **ParquetFormat**. Sie müssen die **type**-Eigenschaft unter **format** auf einen dieser Werte festlegen. Weitere Informationen finden Sie in den Abschnitten [Textformat](supported-file-formats-and-compression-codecs-legacy.md#text-format), [JSON-Format](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro-Format](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [ORC-Format](supported-file-formats-and-compression-codecs-legacy.md#orc-format) und [Parquet-Format](supported-file-formats-and-compression-codecs-legacy.md#parquet-format). |Nein (nur für Szenarien mit Binärkopien) |
| compression | Geben Sie den Typ und den Grad der Komprimierung für die Daten an. Weitere Informationen finden Sie unter [Unterstützte Dateiformate und Codecs für die Komprimierung](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Unterstützte Typen sind **GZip**, **Deflate**, **BZIP2** und **ZipDeflate**.<br/>Unterstützte Grade sind **Optimal** und **Schnellste**. |Nein |

>[!TIP]
>Wenn Sie alle Dateien eines Ordners kopieren möchten, geben Sie nur **folderPath** an.<br>Wenn Sie eine einzelne Datei mit einem bestimmten Namen kopieren möchten, geben Sie **folderPath** mit einem Ordner und **fileName** mit einem Dateinamen an.<br>Wenn Sie eine Teilmenge der Dateien eines Ordners kopieren möchten, geben Sie **folderPath** mit einem Ordner und **fileName** mit einem Platzhalterfilter an. 

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

### <a name="legacy-copy-activity-source-model"></a>Legacy-Kopieraktivität: Quellenmodell

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| type | Die „type“-Eigenschaft der Quelle der Kopieraktivität muss auf **AzureBlobFSSource** festgelegt sein. |Ja |
| recursive | Gibt an, ob die Daten rekursiv aus den Unterordnern oder nur aus dem angegebenen Ordner gelesen werden. Wenn „recursive“ auf „true“ festgelegt ist und es sich bei der Senke um einen dateibasierten Speicher handelt, wird ein leerer Ordner oder Unterordner nicht in die Senke kopiert oder dort erstellt.<br/>Zulässige Werte sind **true** (Standard) und **false**. | Nein |
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

### <a name="legacy-copy-activity-sink-model"></a>Legacy-Kopieraktivität – Senkenmodell

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| type | Die „type“-Eigenschaft der Senke der Kopieraktivität muss auf **AzureBlobFSSink** festgelegt sein. |Ja |
| copyBehavior | Definiert das Kopierverhalten, wenn es sich bei der Quelle um Dateien aus einem dateibasierten Datenspeicher handelt.<br/><br/>Zulässige Werte sind:<br/><b>- PreserveHierarchy (Standard)</b>: Behält die Dateihierarchie im Zielordner bei. Der relative Pfad der Quelldatei zum Quellordner ist mit dem relativen Pfad der Zieldatei zum Zielordner identisch.<br/><b>- FlattenHierarchy</b>: Alle Dateien aus dem Quellordner befinden sich auf der ersten Ebene des Zielordners. Die Namen für die Zieldateien werden automatisch generiert. <br/><b>- MergeFiles</b>: Alle Dateien aus dem Quellordner werden in einer Datei zusammengeführt. Wenn der Dateiname angegeben wurde, entspricht der zusammengeführte Dateiname dem angegebenen Namen. Andernfalls wird der Dateiname automatisch generiert. | Nein |
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

## <a name="next-steps"></a>Nächste Schritte

Eine Liste der Datenspeicher, die als Quellen und Senken für die Kopieraktivität in Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).