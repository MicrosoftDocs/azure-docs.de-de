---
title: Kopieren und Transformieren von Daten in Azure Blob Storage
description: Hier erfahren Sie, wie Sie Daten mithilfe von Data Factory in und aus Blob Storage kopieren sowie Daten in Blob Storage transformieren.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/08/2020
ms.openlocfilehash: 63613307847ba2bf617d7a6a1018d083dc5db3fe
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100393121"
---
# <a name="copy-and-transform-data-in-azure-blob-storage-by-using-azure-data-factory"></a>Kopieren und Transformieren von Daten in Azure Blob Storage mithilfe von Azure Data Factory

> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](v1/data-factory-azure-blob-connector.md)
> * [Aktuelle Version](connector-azure-blob-storage.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten in und aus Azure Blob Storage zu kopieren. Außerdem erfahren Sie, wie Sie die Datenflussaktivität zum Transformieren von Daten in Azure Blob Storage verwenden. Informationen zu Azure Data Factory finden Sie im [Einführungsartikel](introduction.md).

>[!TIP]
>Für Data Lake- oder Data Warehouse-Migrationsszenarios finden Sie weitere Informationen unter [Verwenden von Azure Data Factory zum Migrieren von Daten aus einem Data Lake oder Data Warehouse zu Azure](data-migration-guidance-overview.md).

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Dieser Azure Blob Storage-Connector wird für die folgenden Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen/Senken-Matrix](copy-activity-overview.md)
- [Mapping Data Flow](concepts-data-flow-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)
- [GetMetadata-Aktivität](control-flow-get-metadata-activity.md)
- [Delete-Aktivität](delete-activity.md)

Bei der Kopieraktivität unterstützt dieser Blob Storage-Connector Folgendes:

- Kopieren von Daten in bzw. aus Azure Storage-Konten für allgemeine Zwecke und heißen/kalten Blob Storage. 
- Kopieren von Blobs mithilfe eines Kontoschlüssels, der Dienst-SAS (Shared Access Signature), des Dienstprinzipals oder verwalteter Identitäten für die Azure-Ressourcenauthentifizierung
- Kopieren von Blobs aus Block-, Anfüge- oder Seitenblobs und Kopieren von Daten ausschließlich in Blockblobs.
- Kopieren von Blobs im jeweiligen Zustand oder Analysieren bzw. Generieren von Blobs mit [unterstützten Dateiformaten und Komprimierungscodecs](supported-file-formats-and-compression-codecs.md)
- [Beibehalten von Dateimetadaten beim Kopieren](#preserving-metadata-during-copy)

## <a name="get-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für Blob Storage verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Dieser Blob Storage-Connector unterstützt die folgenden Authentifizierungsypen. Weitere Informationen finden Sie in den entsprechenden Abschnitten.

- [Kontoschlüsselauthentifizierung](#account-key-authentication)
- [SAS-Authentifizierung (Shared Access Signature)](#shared-access-signature-authentication)
- [Dienstprinzipalauthentifizierung](#service-principal-authentication)
- [Verwaltete Identitäten für Azure-Ressourcenauthentifizierung](#managed-identity)

>[!NOTE]
>- Wenn Sie die öffentliche Azure Integration Runtime-Instanz zum Herstellen einer Verbindung mit Blob Storage verwenden möchten, indem Sie in der Azure Storage-Firewall die Option **Vertrauenswürdigen Microsoft-Diensten den Zugriff auf dieses Speicherkonto erlauben** aktivieren, müssen Sie die [Authentifizierung per verwalteter Identität](#managed-identity) verwenden.
>- Wenn Sie Daten mit PolyBase oder der COPY-Anweisung in Azure Synapse Analytics laden und Ihr Quell- oder Stagingblobspeicher mit einem Azure Virtual Network-Endpunkt konfiguriert wurde, müssen Sie die Authentifizierung per verwalteter Identität – wie für Synapse erforderlich – verwenden. Im Abschnitt [Verwaltete Identitäten für Azure-Ressourcenauthentifizierung](#managed-identity) sind weitere Konfigurationsvoraussetzungen aufgeführt.

>[!NOTE]
>Azure HDInsights- und Azure Machine Learning-Aktivitäten unterstützen nur die Authentifizierung mit Azure Blob Storage-Kontoschlüsseln.

### <a name="account-key-authentication"></a>Kontoschlüsselauthentifizierung

Data Factory unterstützt die folgenden Eigenschaften für die Authentifizierung mit Speicherkontoschlüsseln:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft muss auf **AzureBlobStorage** (empfohlen) oder **AzureStorage** (siehe Hinweise unten) festgelegt werden. |Ja |
| connectionString | Geben Sie für die **connectionString**-Eigenschaft die Informationen ein, die zum Herstellen einer Verbindung mit Azure Storage erforderlich sind. <br/> Sie können auch den Kontoschlüssel in Azure Key Vault speichern und die `accountKey`-Konfiguration aus der Verbindungszeichenfolge pullen. Weitere Informationen finden Sie in den folgenden Beispielen und im Artikel [Speichern von Anmeldeinformationen in Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| connectVia | Die [Integration Runtime](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden soll. Sie können die Azure Integration Runtime oder eine selbstgehostete Integration Runtime verwenden (sofern sich Ihr Datenspeicher in einem privaten Netzwerk befindet). Wenn diese Eigenschaft nicht angegeben ist, verwendet der Dienst die normale Azure Integration Runtime. |Nein |

>[!NOTE]
>Ein sekundärer Blob-Dienstendpunkt wird bei Verwendung der Kontoschlüsselauthentifizierung nicht unterstützt. Sie können andere Authentifizierungstypen verwenden.

>[!NOTE]
>Wenn Sie den verknüpften Dienst vom Typ „AzureStorage“ verwenden, wird er weiterhin unverändert unterstützt. Es wird jedoch empfohlen, in Zukunft den neuen verknüpften Dienst vom Typ „AzureBlobStorage“ zu verwenden.

**Beispiel:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Beispiel: Speichern des Kontoschlüssels in Azure Key Vault**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;",
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }            
    }
}
```

### <a name="shared-access-signature-authentication"></a>SAS-Authentifizierung (Shared Access Signature)

Shared Access Signatures bieten delegierten Zugriff auf Ressourcen in Ihrem Speicherkonto. Sie können eine SAS verwenden, um einem Client für einen bestimmten Zeitraum eingeschränkte Berechtigungen für Objekte in Ihrem Speicherkonto zu gewähren. 

Sie müssen die Zugriffsschlüssel für Ihr Konto nicht freigeben. Die SAS ist ein URI, dessen Abfrageparameter alle erforderlichen Informationen für den authentifizierten Zugriff auf eine Speicherressource enthalten. Um mit der SAS auf Speicherressourcen zuzugreifen, muss der Client diese nur an den entsprechenden Konstruktor bzw. die entsprechende Methode übergeben. 

Weitere Informationen zu Shared Access Signatures finden Sie unter [Shared Access Signatures (SAS): Verstehen des Shared Access Signature-Modells](../storage/common/storage-sas-overview.md).

> [!NOTE]
>- Data Factory unterstützt jetzt *SAS (Shared Access Signatures) für Dienste* sowie für *Konten*. Weitere Informationen zu SAS (Shared Access Signatures) finden Sie unter [Gewähren von eingeschränktem Zugriff auf Azure Storage-Ressourcen mithilfe von SAS (Shared Access Signature)](../storage/common/storage-sas-overview.md).
>- Bei späteren Datasetkonfigurationen ist der Ordnerpfad der absolute Pfad ab der Containerebene. Sie müssen einen Pfad konfigurieren, der sich am Pfad in Ihrem SAS-URI orientiert.

Data Factory unterstützt die folgenden Eigenschaften für die Verwendung der SAS-Authentifizierung:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft muss auf **AzureBlobStorage** (empfohlen) oder **AzureStorage** (siehe Hinweise unten) festgelegt werden. |Ja |
| sasUri | Geben Sie den SAS-URI für Azure Storage-Ressourcen wie Blobs oder Container an. <br/>Markieren Sie dieses Feld als **SecureString**, um es sicher in Data Factory zu speichern. Sie können auch das SAS-Token in Azure Key Vault speichern, um die automatische Rotation zu nutzen und den Tokenabschnitt zu entfernen. Weitere Informationen finden Sie in den folgenden Beispielen sowie unter [Speichern von Anmeldeinformationen in Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| connectVia | Die [Integration Runtime](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden soll. Sie können die Azure Integration Runtime oder eine selbstgehostete Integration Runtime verwenden (sofern sich Ihr Datenspeicher in einem privaten Netzwerk befindet). Wenn diese Eigenschaft nicht angegeben ist, verwendet der Dienst die normale Azure Integration Runtime. |Nein |

>[!NOTE]
>Wenn Sie den verknüpften Dienst vom Typ „AzureStorage“ verwenden, wird er weiterhin unverändert unterstützt. Es wird jedoch empfohlen, in Zukunft den neuen verknüpften Dienst vom Typ „AzureBlobStorage“ zu verwenden.

**Beispiel:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource e.g. https://<accountname>.blob.core.windows.net/?sv=<storage version>&st=<start time>&se=<expire time>&sr=<resource>&sp=<permissions>&sip=<ip range>&spr=<protocol>&sig=<signature>>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Beispiel: Speichern des Kontoschlüssels in Azure Key Vault**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource without token e.g. https://<accountname>.blob.core.windows.net/>"
            },
            "sasToken": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName with value of SAS token e.g. ?sv=<storage version>&st=<start time>&se=<expire time>&sr=<resource>&sp=<permissions>&sip=<ip range>&spr=<protocol>&sig=<signature>>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Berücksichtigen Sie beim Erstellen eines SAS-URIs die folgenden Aspekte:

- Legen Sie geeignete Lese-/Schreib-Berechtigungen für Objekte fest, basierend auf der Verwendung des verknüpften Diensts in Ihrer Data Factory (Lesen, Schreiben, Lesen/Schreiben).
- Legen Sie für **Ablaufzeit** einen geeigneten Wert fest. Stellen Sie sicher, dass der Zugriff auf Storage-Objekte nicht während des aktiven Zeitraums der Pipeline abläuft.
- Der URI sollte je nach Bedarf auf der richtigen Container- oder Blobebene erstellt werden. Ein SAS-URI zu einem Blob ermöglicht Data Factory den Zugriff auf dieses spezielle Blob. Ein SAS-URI zu einem Blobspeichercontainer ermöglicht Data Factory das Durchlaufen von Blobs in diesem Container. Wenn Sie den Zugriff später auf mehr Objekte ausweiten oder auf weniger Objekte beschränken oder den SAS-URI aktualisieren möchten, denken Sie daran, den verknüpften Dienst mit dem neuen URI zu aktualisieren.

### <a name="service-principal-authentication"></a>Dienstprinzipalauthentifizierung

Allgemeine Informationen zur Dienstprinzipalauthentifizierung für Azure Storage finden Sie unter [Autorisieren des Zugriffs auf Blobs und Warteschlangen mit Azure Active Directory](../storage/common/storage-auth-aad.md).

Zum Verwenden der Dienstprinzipalauthentifizierung führen Sie die folgenden Schritte aus:

1. Registrieren Sie eine Anwendungsentität in Azure Active Directory (Azure AD), indem Sie die Anleitungen unter [Registrieren Ihrer Anwendung bei einem Azure AD-Mandanten](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant) befolgen. Notieren Sie sich die folgenden Werte, die Sie zum Definieren des verknüpften Diensts verwenden können:

    - Anwendungs-ID
    - Anwendungsschlüssel
    - Mandanten-ID

2. Erteilen Sie dem Dienstprinzipal die entsprechenden Berechtigung in Azure Blob Storage. Weitere Informationen zu den Rollen finden Sie unter [Zuweisen einer Azure-Rolle für den Zugriff auf Blob- und Warteschlangendaten über das Azure-Portal](../storage/common/storage-auth-aad-rbac-portal.md).

    - Erteilen Sie ihm **als Quelle** in der **Zugriffssteuerung (IAM)** mindestens die Rolle **Storage-Blobdatenleser**.
    - Weisen Sie ihm **Als Senke** in der **Zugriffssteuerung (IAM)** mindestens die Rolle **Mitwirkender an Storage-Blobdaten** zu.

Diese Eigenschaften werden für den mit Azure Blob Storage verknüpften Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft muss auf **AzureBlobStorage** festgelegt sein. |Ja |
| serviceEndpoint | Geben Sie den Azure Blob Storage-Dienstendpunkt mit dem Muster `https://<accountName>.blob.core.windows.net/` an. |Ja |
| accountKind | Geben Sie die Art Ihres Speicherkontos an. Zulässige Werte sind: **Storage** (Universell v1), **StorageV2** (Universell v2), **BlobStorage** oder **BlockBlobStorage**. <br/> Bei Verwendung des mit Azure Blob verknüpften Diensts im Datenfluss wird die Authentifizierung per verwalteter Identität oder Dienstprinzipal nicht unterstützt, wenn der Kontotyp leer ist oder der Wert „Storage“ lautet. Geben Sie die richtige Kontoart an, wählen Sie eine andere Authentifizierung aus, oder aktualisieren Sie Ihr Speicherkonto auf „Universell v2“. |Nein |
| servicePrincipalId | Geben Sie die Client-ID der Anwendung an. | Ja |
| servicePrincipalKey | Geben Sie den Schlüssel der Anwendung an. Markieren Sie dieses Feld als **SecureString**, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). | Ja |
| tenant | Geben Sie die Mandanteninformationen (Domänenname oder Mandanten-ID) für Ihre Anwendung an. Diese können Sie abrufen, indem Sie im Azure-Portal mit der Maus auf den Bereich oben rechts zeigen. | Ja |
| azureCloudType | Geben Sie für die Dienstprinzipalauthentifizierung die Art der Azure-Cloudumgebung an, bei der Ihre Azure Active Directory-Anwendung registriert ist. <br/> Zulässige Werte sind **AzurePublic**, **AzureChina**, **AzureUsGovernment** und **AzureGermany**. Standardmäßig wird die Cloudumgebung der Data Factory verwendet. | Nein |
| connectVia | Die [Integration Runtime](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden soll. Sie können die Azure Integration Runtime oder eine selbstgehostete Integration Runtime verwenden (sofern sich Ihr Datenspeicher in einem privaten Netzwerk befindet). Wenn diese Eigenschaft nicht angegeben ist, verwendet der Dienst die normale Azure Integration Runtime. |Nein |

>[!NOTE]
>Wenn für Ihr Blobkonto die Option [Vorläufiges Löschen](../storage/blobs/soft-delete-blob-overview.md) freigeschaltet ist, wird die Authentifizierung des Dienstprinzipals in Datenflüssen nicht unterstützt.

>[!NOTE]
>Die Dienstprinzipalauthentifizierung wird nur durch den verknüpften Dienst vom Typ „AzureBlobStorage“unterstützt, nicht jedoch vom vorherigen verknüpften Dienst vom Typ „AzureStorage“.

**Beispiel:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/",
            "accountKind": "StorageV2",
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

### <a name="managed-identities-for-azure-resource-authentication"></a><a name="managed-identity"></a> Verwaltete Identitäten für Azure-Ressourcenauthentifizierung

Eine Data Factory kann einer [verwalteten Identität für Azure-Ressourcen](data-factory-service-identity.md) zugeordnet werden, die diese spezielle Data Factory darstellt. Sie können diese verwaltete Identität direkt für die Blob Storage-Authentifizierung verwenden, ähnlich wie bei der Verwendung Ihres eigenen Dienstprinzipals. Sie erlaubt dieser bestimmten Factory den Zugriff auf und das Kopieren von Daten aus bzw. in Blob Storage.

Weitere allgemeine Informationen zur Azure Storage-Authentifizierung finden Sie unter [Autorisieren des Zugriffs auf Blobs und Warteschlangen mit Azure Active Directory](../storage/common/storage-auth-aad.md). Wenn Sie verwaltete Identitäten für die Azure-Ressourcenauthentifizierung verwenden möchten, gehen Sie folgendermaßen vor:

1. [Rufen Sie die Informationen zur verwalteten Data Factory-Identität ab](data-factory-service-identity.md#retrieve-managed-identity), indem Sie den Wert der Objekt-ID der verwalteten Identität kopieren, der zusammen mit Ihrer Factory generiert wurde.

2. Erteilen Sie der verwalteten Identität Berechtigungen in Azure Blob Storage. Weitere Informationen zu den Rollen finden Sie unter [Zuweisen einer Azure-Rolle für den Zugriff auf Blob- und Warteschlangendaten über das Azure-Portal](../storage/common/storage-auth-aad-rbac-portal.md).

    - Erteilen Sie ihm **als Quelle** in der **Zugriffssteuerung (IAM)** mindestens die Rolle **Storage-Blobdatenleser**.
    - Weisen Sie ihm **Als Senke** in der **Zugriffssteuerung (IAM)** mindestens die Rolle **Mitwirkender an Storage-Blobdaten** zu.

>[!IMPORTANT]
>Wenn Sie Daten mithilfe von PolyBase oder der COPY-Anweisung aus Blob Storage (als Quelle oder Stagingkomponente) in Azure Synapse Analytics laden und dazu die Authentifizierung per verwalteter Identität für Blob Storage verwenden, müssen Sie unbedingt auch die Schritte 1 bis 3 in [dieser Anleitung](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage) befolgen. Mit diesen Schritten wird der Server bei Azure AD registriert, und Ihrem Server wird die Rolle „Mitwirkender an Storage-Blobdaten“ zugewiesen. Data Factory kümmert sich um den Rest. Wenn Sie Blob Storage mit einem Azure Virtual Network-Endpunkt konfigurieren, muss außerdem im Einstellungsmenü **Firewalls und virtuelle Netzwerke** des Azure Storage-Kontos die Option **Vertrauenswürdigen Microsoft-Diensten den Zugriff auf dieses Speicherkonto erlauben** aktiviert sein (wie für Synapse erforderlich).

Diese Eigenschaften werden für den mit Azure Blob Storage verknüpften Dienst unterstützt:

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft muss auf **AzureBlobStorage** festgelegt sein. |Ja |
| serviceEndpoint | Geben Sie den Azure Blob Storage-Dienstendpunkt mit dem Muster `https://<accountName>.blob.core.windows.net/` an. |Ja |
| accountKind | Geben Sie die Art Ihres Speicherkontos an. Zulässige Werte sind: **Storage** (Universell v1), **StorageV2** (Universell v2), **BlobStorage** oder **BlockBlobStorage**. <br/> Bei Verwendung des mit Azure Blob verknüpften Diensts im Datenfluss wird die Authentifizierung per verwalteter Identität oder Dienstprinzipal nicht unterstützt, wenn der Kontotyp leer ist oder der Wert „Storage“ lautet. Geben Sie die richtige Kontoart an, wählen Sie eine andere Authentifizierung aus, oder aktualisieren Sie Ihr Speicherkonto auf „Universell v2“. |Nein |
| connectVia | Die [Integration Runtime](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden soll. Sie können die Azure Integration Runtime oder eine selbstgehostete Integration Runtime verwenden (sofern sich Ihr Datenspeicher in einem privaten Netzwerk befindet). Wenn diese Eigenschaft nicht angegeben ist, verwendet der Dienst die normale Azure Integration Runtime. |Nein |

> [!NOTE]
> Wenn für Ihr Blobkonto die Option [Vorläufiges Löschen](../storage/blobs/soft-delete-blob-overview.md) freigeschaltet ist, wird die Authentifizierung von verwalteten Identitäten in Datenflüssen nicht unterstützt.

> [!NOTE]
> Verwaltete Identitäten für die Azure-Ressourcenauthentifizierung werden nur vom verknüpften Dienst vom Typ „AzureBlobStorage“ unterstützt, nicht jedoch vom vorherigen verknüpften Dienst vom Typ „AzureStorage“.

**Beispiel:**

```json
{
    "name": "AzureBlobStorageLinkedService",
    "properties": {
        "type": "AzureBlobStorage",
        "typeProperties": {            
            "serviceEndpoint": "https://<accountName>.blob.core.windows.net/",
            "accountKind": "StorageV2" 
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

Folgende Eigenschaften werden für Azure Blob Storage unter den `location`-Einstellungen in formatbasierten Datasets unterstützt:

| Eigenschaft   | BESCHREIBUNG                                                  | Erforderlich |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Die **type**-Eigenschaft des Speicherorts im Dataset muss auf **AzureBlobStorageLocation** festgelegt werden. | Ja      |
| Container  | Der BLOB-Container.                                          | Ja      |
| folderPath | Der Pfad zum Ordner unter dem angegebenen Container. Wenn Sie einen Platzhalter verwenden möchten, um den Ordner zu filtern, überspringen Sie diese Einstellung, und geben Sie entsprechende Aktivitätsquelleneinstellungen an. | Nein       |
| fileName   | Der Dateiname unter dem angegebenen Container und Ordnerpfad. Wenn Sie Platzhalter verwenden möchten, um Dateien zu filtern, überspringen Sie diese Einstellung, und geben Sie dies entsprechend in den Aktivitätsquelleneinstellungen an. | Nein       |

**Beispiel:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
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

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste mit Eigenschaften, die von der Blob Storage-Quelle und -Senke unterstützt werden.

### <a name="blob-storage-as-a-source-type"></a>Blob Storage als Quelltyp

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Folgende Eigenschaften werden für Azure Blob Storage unter den `storeSettings`-Einstellungen in formatbasierten Kopierquellen unterstützt:

| Eigenschaft                 | BESCHREIBUNG                                                  | Erforderlich                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Die **type**-Eigenschaft unter `storeSettings` muss auf **AzureBlobStorageReadSettings** festgelegt werden. | Ja                                           |
| ***Suchen Sie die zu kopierenden Dateien:*** |  |  |
| OPTION 1: statischer Pfad<br> | Kopieren Sie aus dem angegebenen Container oder Ordner/Dateipfad, der im Dataset angegeben ist. Wenn Sie alle Blobs aus einem Container oder Ordner kopieren möchten, geben Sie zusätzlich `wildcardFileName` als `*` an. |  |
| Option 2: Blobpräfix<br>– prefix | Präfix für den Blobnamen unter dem angegebenen Container, der in einem Dataset zum Filtern von Quellblobs konfiguriert wurde. Es werden die Blobs ausgewählt, deren Namen mit `container_in_dataset/this_prefix` beginnen. Für Blob Storage wird der serverseitige Filter verwendet, dessen Leistung besser ist als die eines Platzhalterfilters.<br><br>Wenn Sie das Präfix verwenden und in eine dateibasierte Senke mit Beibehaltung der Hierarchie kopieren, wird der Unterpfad nach dem letzten „/“ im Präfix beibehalten. Wenn Sie beispielsweise `container/folder/subfolder/file.txt` als Quelle haben und das Präfix als `folder/sub` konfigurieren, lautet der beibehaltene Dateipfad `subfolder/file.txt`. | Nein                                                          |
| OPTION 3: Platzhalter<br>– wildcardFolderPath | Der Ordnerpfad mit Platzhalterzeichen unter dem angegebenen Container, der in einem Dataset für das Filtern von Quellordnern konfiguriert ist. <br>Folgende Platzhalter sind zulässig: `*` (entspricht null [0] oder mehr Zeichen) und `?` (entspricht null [0] oder einem einzelnen Zeichen). Verwenden Sie `^` als Escapezeichen, wenn Ihr Ordnername einen Platzhalter oder dieses Escapezeichen enthält. <br>Weitere Beispiele finden Sie unter [Beispiele für Ordner- und Dateifilter](#folder-and-file-filter-examples). | Nein                                            |
| OPTION 3: Platzhalter<br>– wildcardFileName | Der Dateiname mit Platzhalterzeichen unter dem angegebenen Container und Ordnerpfad (oder Platzhalterordnerpfad) für das Filtern von Quelldateien. <br>Folgende Platzhalter sind zulässig: `*` (entspricht null [0] oder mehr Zeichen) und `?` (entspricht null [0] oder einem einzelnen Zeichen). Verwenden Sie `^` als Escapezeichen, wenn der tatsächliche Dateiname einen Platzhalter oder dieses Escapezeichen enthält. Weitere Beispiele finden Sie unter [Beispiele für Ordner- und Dateifilter](#folder-and-file-filter-examples). | Ja |
| OPTION 4: eine Liste von Dateien<br>– fileListPath | Gibt an, dass eine bestimmte Dateigruppe kopiert werden soll. Verweisen Sie auf eine Textdatei, die eine Liste der zu kopierenden Dateien enthält, und zwar eine Datei pro Zeile. Dies ist der relative Pfad zu dem im Dataset konfigurierten Pfad.<br/>Wenn Sie diese Option verwenden, geben Sie keinen Dateinamen im Dataset an. Weitere Beispiele finden Sie unter [Beispiele für Dateilisten](#file-list-examples). |Nein |
| ***Zusätzliche Einstellungen:*** |  | |
| recursive | Gibt an, ob die Daten rekursiv aus den Unterordnern oder nur aus dem angegebenen Ordner gelesen werden. Beachten Sie Folgendes: Wenn **recursive** auf **TRUE** festgelegt ist und es sich bei der Senke um einen dateibasierten Speicher handelt, wird ein leerer Ordner oder Unterordner nicht in die Senke kopiert und dort auch nicht erstellt. <br>Zulässige Werte sind **true** (Standard) und **false**.<br>Diese Eigenschaft gilt nicht, wenn Sie `fileListPath` konfigurieren. |Nein |
| deleteFilesAfterCompletion | Gibt an, ob die Binärdateien nach dem erfolgreichen Verschieben in den Zielspeicher aus dem Quellspeicher gelöscht werden. Die Dateien werden einzeln gelöscht, sodass Sie bei einem Fehler der Kopieraktivität feststellen werden, dass einige Dateien bereits ins Ziel kopiert und aus der Quelle gelöscht wurden, wohingegen sich andere weiter im Quellspeicher befinden. <br/>Diese Eigenschaft ist nur im Szenario zum Kopieren von Binärdateien gültig. Standardwert: FALSE. |Nein |
| modifiedDatetimeStart    | Die Dateien werden anhand des Attributs „Letzte Änderung“ gefiltert. <br>Die Dateien werden ausgewählt, wenn der Zeitpunkt der letzten Änderung innerhalb des Zeitbereichs zwischen `modifiedDatetimeStart` und `modifiedDatetimeEnd` liegt. Die Zeit wird auf die UTC-Zeitzone im Format „2018-12-01T05:00:00Z“ angewendet. <br> Die Eigenschaften können **NULL** sein, was bedeutet, dass kein Dateiattributfilter auf das Dataset angewendet wird.  Wenn `modifiedDatetimeStart` einen datetime-Wert aufweist, aber `modifiedDatetimeEnd` **NULL** ist, werden die Dateien ausgewählt, deren Attribut für die letzte Änderung größer oder gleich dem datetime-Wert ist.  Wenn `modifiedDatetimeEnd` den datetime-Wert aufweist, aber `modifiedDatetimeStart` **NULL** ist, werden die Dateien ausgewählt, deren Attribut für die letzte Änderung kleiner als der datetime-Wert ist.<br/>Diese Eigenschaft gilt nicht, wenn Sie `fileListPath` konfigurieren. | Nein                                            |
| modifiedDatetimeEnd      | Wie oben.                                               | Nein                                            |
| enablePartitionDiscovery | Geben Sie bei partitionierten Dateien an, ob die Partitionen anhand des Dateipfads analysiert und als zusätzliche Quellspalten hinzugefügt werden sollen.<br/>Zulässige Werte sind **false** (Standard) und **true**. | Nein                                            |
| partitionRootPath | Wenn die Partitionsermittlung aktiviert ist, geben Sie den absoluten Stammpfad an, um partitionierte Ordner als Datenspalten zu lesen.<br/><br/>Ohne Angabe gilt standardmäßig Folgendes:<br/>- Wenn Sie den Dateipfad im Dataset oder die Liste der Dateien in der Quelle verwenden, ist der Partitionsstammpfad der im Dataset konfigurierte Pfad.<br/>Wenn Sie einen Platzhalterordnerfilter verwenden, ist der Stammpfad der Partition der Unterpfad vor dem ersten Platzhalter.<br/>Wenn Sie Präfix verwenden, ist der Stammpfad der Partition ein Unterpfad vor dem letzten „/“. <br/><br/>Angenommen, Sie konfigurieren den Pfad im Dataset als „root/folder/year=2020/month=08/day=27“:<br/>- Wenn Sie den Stammpfad der Partition als „root/folder/year=2020“ angeben, generiert die Kopieraktivität zusätzlich zu den Spalten in den Dateien die beiden weiteren Spalten `month` und `day` mit den Werten „08“ bzw. „27“.<br/>- Wenn kein Stammpfad für die Partition angegeben ist, wird keine zusätzliche Spalte generiert. | Nein                                            |
| maxConcurrentConnections | Die Anzahl gleichzeitiger Verbindungen mit dem Speicher. Geben Sie diesen Wert nur an, wenn Sie die gleichzeitigen Verbindungen mit dem Datenspeicher begrenzen möchten. | Nein                                            |

> [!NOTE]
> Beim Parquet-Format/Textformat mit Trennzeichen wird die im nächsten Abschnitt beschriebene Quelle der Kopieraktivität vom Typ **BlobSource** aus Gründen der Abwärtskompatibilität weiterhin unverändert unterstützt. Es wird allerdings empfohlen, das neue Modell zu verwenden, bis die Data Factory-Erstellungsoberfläche für die Generierung dieser neuen Typen angepasst wurde.

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
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
                    "type": "AzureBlobStorageReadSettings",
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

> [!NOTE]
> Der Container `$logs`, der während der Aktivierung von Storage Analytics für eine Speicherkonto automatisch erstellt wird, wird beim Ausführen eines Auflistungsvorgangs für Container über die Data Factory-Benutzeroberfläche nicht angezeigt. Der Dateipfad muss direkt angegeben werden, damit Data Factory Dateien aus dem Container `$logs` verwenden kann.

### <a name="blob-storage-as-a-sink-type"></a>Blob Storage als Senkentyp

[!INCLUDE [data-factory-v2-file-sink-formats](../../includes/data-factory-v2-file-sink-formats.md)] 

Folgende Eigenschaften werden für Azure Blob Storage unter den `storeSettings`-Einstellungen in formatbasierten Kopiersenken unterstützt:

| Eigenschaft                 | Beschreibung                                                  | Erforderlich |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | Die **type**-Eigenschaft unter `storeSettings` muss auf **AzureBlobStorageWriteSettings** festgelegt werden. | Ja      |
| copyBehavior             | Definiert das Kopierverhalten, wenn es sich bei der Quelle um Dateien aus einem dateibasierten Datenspeicher handelt.<br/><br/>Zulässige Werte sind:<br/><b>- PreserveHierarchy (Standard)</b>: Behält die Dateihierarchie im Zielordner bei. Der relative Pfad der Quelldatei zum Quellordner ist mit dem relativen Pfad der Zieldatei zum Zielordner identisch.<br/><b>- FlattenHierarchy</b>: Alle Dateien aus dem Quellordner befinden sich auf der ersten Ebene des Zielordners. Die Namen für die Zieldateien werden automatisch generiert. <br/><b>- MergeFiles</b>: Alle Dateien aus dem Quellordner werden in einer Datei zusammengeführt. Wenn der Datei- oder Blobname angegeben wurde, entspricht der Name der Zusammenführungsdatei dem angegebenen Namen. Andernfalls wird der Dateiname automatisch generiert. | Nein       |
| blockSizeInMB | Geben Sie die Blockgröße, die zum Schreiben von Daten in Blockblobs verwendet wird, in Megabyte an. Informieren Sie sich ausführlicher über [Blockblobs](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs). <br/>Der zulässige Wert liegt *zwischen 4 und 100 MB*. <br/>Standardmäßig bestimmt Data Factory automatisch die Blockgröße, basierend auf dem Quellspeichertyp und den Daten. Bei einer nicht binären Kopie in Blob Storage beträgt die Standardblockgröße 100 MB, damit sie in maximal 4,95 TB Daten passt. Dies ist möglicherweise nicht optimal, wenn Ihre Daten nicht groß sind – insbesondere, wenn Sie eine selbstgehostete Integration Runtime mit einer schlechten Netzwerkverbindung verwenden, was zu einem Timeout des Vorgangs oder Leistungsproblemen führt. Sie können explizit eine Blockgröße angeben und gleichzeitig sicherstellen, dass `blockSizeInMB*50000` groß genug ist, um die Daten zu speichern. Andernfalls tritt bei der Ausführung der Kopieraktivität ein Fehler auf. | Nein |
| maxConcurrentConnections | Die Anzahl gleichzeitiger Verbindungen mit dem Speicher. Geben Sie diesen Wert nur an, wenn Sie die gleichzeitigen Verbindungen mit dem Datenspeicher begrenzen möchten. | Nein       |

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
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
                    "type": "AzureBlobStorageWriteSettings",
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
| `container/Folder*` | (empty, use default) | false | Container<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AndererOrdnerB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei6.csv |
| `container/Folder*` | (empty, use default) | true | Container<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AndererOrdnerB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei6.csv |
| `container/Folder*` | `*.csv` | false | Container<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AndererOrdnerB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei6.csv |
| `container/Folder*` | `*.csv` | true | Container<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AndererOrdnerB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei6.csv |

### <a name="file-list-examples"></a>Beispiele für Dateilisten

In diesem Abschnitt wird das resultierende Verhalten beschrieben, wenn ein Dateilistenpfad in der Quelle einer Kopieraktivität verwendet wird.

Angenommen, Sie haben die folgende Quellordnerstruktur und möchten die Dateien kopieren, deren Namen fett formatiert sind:

| Beispielquellstruktur                                      | Inhalt in „FileListToCopy.txt“                             | Konfiguration der Data Factory                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| Container<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadaten<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | Datei1.csv<br>Unterordner1/Datei3.csv<br>Unterordner1/Datei5.csv | **Im Dataset:**<br>– Container: `container`<br>– Ordnerpfad: `FolderA`<br><br>**In der Quelle der Kopieraktivität:**<br>– Dateilistenpfad: `container/Metadata/FileListToCopy.txt` <br><br>Der Dateilistenpfad verweist auf eine Textdatei im selben Datenspeicher, der eine Liste der zu kopierenden Dateien enthält, und zwar eine Datei pro Zeile. Diese enthält den relativen Pfad zu dem im Dataset konfigurierten Pfad. |

### <a name="some-recursive-and-copybehavior-examples"></a>Beispiele für „recursive“ und „copyBehavior“

Dieser Abschnitt beschreibt das resultierende Verhalten des Kopiervorgangs für verschiedene Kombinationen von **recursive**- und **copyBehavior**-Werten.

| recursive | copyBehavior | Struktur des Quellordners | Resultierendes Ziel |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5 | Der Zielordner „Ordner1“ wird mit der gleichen Struktur erstellt wie die Quelle:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5 |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5 | Der Zielordner „Ordner1“ wird mit der folgenden Struktur erstellt: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch generierter Name für Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch generierter Name für Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch generierter Name für Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch generierter Name für Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch generierter Name für Datei5 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5 | Der Zielordner „Ordner1“ wird mit der folgenden Struktur erstellt: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Die Inhalte von Datei1 + Datei2 + Datei3 + Datei4 + Datei5 werden in einer Datei mit einem automatisch generierten Dateinamen zusammengeführt. |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5 | Der Zielordner „Ordner1“ wird mit der folgenden Struktur erstellt: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/><br/>Unterordner1 mit Datei3, Datei4 und Datei5 wird nicht übernommen. |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5 | Der Zielordner „Ordner1“ wird mit der folgenden Struktur erstellt: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch generierter Name für Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch generierter Name für Datei2<br/><br/>Unterordner1 mit Datei3, Datei4 und Datei5 wird nicht übernommen. |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5 | Der Zielordner „Ordner1“ wird mit der folgenden Struktur erstellt:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Die Inhalte von Datei1 + Datei2 werden in einer Datei mit einem automatisch generierten Dateinamen zusammengeführt. Automatisch generierter Name für Datei1<br/><br/>Unterordner1 mit Datei3, Datei4 und Datei5 wird nicht übernommen. |

## <a name="preserving-metadata-during-copy"></a>Beibehalten von Metadaten beim Kopieren

Beim Kopieren von Dateien von Amazon S3, Azure Blob Storage oder Azure Data Lake Storage Gen2 nach Azure Data Lake Storage Gen2 oder Azure Blob Storage können Sie festlegen, dass die Dateimetadaten zusätzlich zu den Daten beibehalten werden sollen. Weitere Informationen finden Sie unter [Beibehalten von Metadaten](copy-activity-preserve-metadata.md#preserve-metadata).

## <a name="mapping-data-flow-properties"></a>Eigenschaften von Mapping Data Flow

Wenn Sie Daten in Zuordnungsdatenflüsse transformieren, können Sie Dateien aus Azure Blob Storage in den folgenden Formaten lesen und schreiben:
* [Avro](format-avro.md#mapping-data-flow-properties)
* [Text mit Trennzeichen](format-delimited-text.md#mapping-data-flow-properties)
* [Delta](format-delta.md#mapping-data-flow-properties)
* [Excel](format-excel.md#mapping-data-flow-properties)
* [JSON](format-json.md#mapping-data-flow-properties)
* [Parquet](format-parquet.md#mapping-data-flow-properties)

Formatspezifische Einstellungen finden Sie in der Dokumentation für das jeweilige Format. Weitere Informationen finden Sie unter [Quelltransformation in einem Zuordnungsdatenfluss](data-flow-source.md) und [Senkentransformation in einem Zuordnungsdatenfluss](data-flow-sink.md).

### <a name="source-transformation"></a>Quellentransformation

Bei der Quelltransformation können Sie in Azure Blob Storage Daten aus einem Container, Ordner oder einer einzelnen Datei lesen. Über die Registerkarte **Source options** (Quellenoptionen) können Sie verwalten, wie die Dateien gelesen werden. 

![Quelloptionen](media/data-flow/sourceOptions1.png "Quelloptionen")

**Platzhalterpfade:** Mithilfe eines Platzhaltermusters wird Data Factory angewiesen, die einzelnen übereinstimmenden Ordner und Dateien in einer einzigen Quelltransformation zu durchlaufen. Dies ist eine effektive Methode zur Verarbeitung von mehreren Dateien in einem einzigen Datenfluss. Über das Pluszeichen (+), das angezeigt wird, wenn Sie mit dem Cursor auf Ihr vorhandenes Platzhaltermuster zeigen, können Sie weitere Platzhaltermuster hinzufügen.

Wählen Sie in Ihrem Quellcontainer eine Reihe von Dateien aus, die einem Muster entsprechen. Es kann nur ein Container im Dataset angegeben werden. Daher muss Ihr Platzhalterpfad auch den Ordnerpfad des Stammordners enthalten.

Beispiele für Platzhalter:

* ```*```: stellt eine beliebige Zeichenfolge dar
* ```**```: stellt eine rekursive Verzeichnisschachtelung dar
* ```?```: ersetzt ein Zeichen
* ```[]```: stimmt mit mindestens einem Zeichen in den Klammern überein

* ```/data/sales/**/*.csv```: ruft alle CSV-Dateien unter „/data/sales“ ab
* ```/data/sales/20??/**/```: ruft alle Dateien aus dem 20. Jahrhundert ab
* ```/data/sales/*/*/*.csv```: ruft CSV-Dateien auf zwei Ebenen unter „/data/sales“ ab
* ```/data/sales/2004/*/12/[XY]1?.csv```: ruft alle CSV-Dateien von Dezember 2004 ab, die mit X oder Y und einer zweistelligen Zahl als Präfix beginnen

**Partitionsstammpfad:** Wenn Ihre Dateiquelle partitionierte Ordner mit dem Format ```key=value``` (z. B. `year=2019`) enthält, können Sie die oberste Ebene dieser Ordnerstruktur einem Spaltennamen im Datenstrom Ihres Datenflusses zuweisen.

Legen Sie zunächst einen Platzhalter fest, um darin alle Pfade, die die partitionierten Ordner sind, sowie die Blattdateien einzuschließen, die gelesen werden sollen.

![Einstellungen für die Partitionsquelldatei](media/data-flow/partfile2.png "Einstellung der Partitionsdatei")

Verwenden Sie die Einstellung **Partition root path** (Partitionsstammpfad), um zu definieren, was die oberste Ebene der Ordnerstruktur ist. Wenn Sie die Inhalte Ihrer Daten über die Datenvorschau anzeigen, sehen Sie, dass Data Factory die aufgelösten Partitionen hinzufügen wird, die auf den einzelnen Ordnerebenen gefunden werden.

![Partitionsstammpfad](media/data-flow/partfile1.png "Vorschau des Partitionsstammpfads")

**Liste der Dateien**: Dies ist eine Dateigruppe. Erstellen Sie eine Textdatei mit einer Liste der relativen Pfade der zu verarbeitenden Dateien. Verweisen Sie auf diese Textdatei.

**Spalte für die Speicherung im Dateinamen**: Speichern Sie den Namen der Quelldatei in einer Spalte in den Daten. Geben Sie hier einen neuen Spaltennamen ein, um die Zeichenfolge für den Dateinamen zu speichern.

**Nach der Fertigstellung**: Wählen Sie aus, ob Sie nach dem Ausführen des Datenflusses nichts mit der Quelldatei anstellen, die Quelldatei löschen oder die Quelldateien verschieben möchten. Die Pfade für das Verschieben sind relative Pfade.

Um Quelldateien an einen anderen Speicherort nach der Verarbeitung zu verschieben, wählen Sie zuerst für den Dateivorgang die Option „Verschieben“ aus. Legen Sie dann das Quellverzeichnis („from“/„aus“) fest. Wenn Sie keine Platzhalter für Ihren Pfad verwenden, entspricht die Einstellung „from“ dem Quellordner.

Wenn Sie über einen Quellpfad mit Platzhalter verfügen, sieht Ihre Syntax ähnlich wie hier aus:

```/data/sales/20??/**/*.csv```

Geben Sie „from“ beispielsweise wie folgt an:

```/data/sales```

„To“ können Sie wie folgt angeben:

```/backup/priorSales```

In diesem Fall werden alle Dateien, die aus „/Data/Sales“ erstellt wurden, in „/Backup/priorSales“ verschoben.

> [!NOTE]
> Die Dateivorgänge werden nur ausgeführt, wenn der Datenfluss anhand der Aktivität zum Ausführen des Datenflusses in einer Pipeline über eine Pipelineausführung ausgeführt wird (Debuggen der Pipeline oder Ausführung). Dateivorgänge werden *nicht* im Datenfluss-Debugmodus ausgeführt.

**Nach der letzten Änderung filtern**: Sie können einen Datumsbereich angeben, um die zu verarbeitenden Dateien nach der letzten Änderung zu filtern. Alle Datums-/Uhrzeitangaben erfolgen in UTC. 

### <a name="sink-properties"></a>Senkeneigenschaften

In der Senkentransformation können Sie in Azure Blob Storage in einen Container oder Ordner schreiben. Über die Registerkarte **Einstellungen** können Sie verwalten, wie die Dateien geschrieben werden.

![Senkenoptionen](media/data-flow/file-sink-settings.png "Senkenoptionen")

**Ordner löschen:** Bestimmt, ob der Zielordner vor dem Schreiben der Daten gelöscht wird.

**Dateinamenoption:** Bestimmt, wie die Zieldateien im Zielordner benannt werden. Es gibt folgende Dateinamenoptionen:
   * **Standard:** Lassen Sie zu, dass Spark Dateien basierend auf den PART-Standards benennt.
   * **Muster:** Geben Sie ein Muster ein, das Ihre Ausgabedateien pro Partition aufführt. Zum Beispiel erstellt **loans[n].csv** die Dateien „loans1.csv“, „loans2.csv“ usw.
   * **Pro Partition:** Geben Sie einen Dateinamen pro Partition ein.
   * **Wie Daten in Spalte:** Legen Sie die Ausgabedatei auf den Wert einer Spalte fest. Der Pfad ist relativ zum Datasetcontainer und nicht zum Zielordner. Wenn Ihr Dataset einen Ordnerpfad enthält, wird er überschrieben.
   * **Ausgabe in eine einzelne Datei:** Mit dieser Option werden die partitionierten Ausgabedateien in einer einzelnen Datei kombiniert. Der Pfad ist relativ zum Datasetordner. Bedenken Sie, dass der Zusammenführungsvorgang je nach Knotengröße zu Fehlern führen kann. Diese Option wird für große Datasets nicht empfohlen.

**Alle in Anführungszeichen:** bestimmt, ob alle Werte in Anführungszeichen stehen sollen.

## <a name="lookup-activity-properties"></a>Eigenschaften der Lookup-Aktivität

Ausführliche Informationen zu den Eigenschaften finden Sie unter [Lookup-Aktivität](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Eigenschaften der GetMetadata-Aktivität

Ausführliche Informationen zu den Eigenschaften finden Sie unter [GetMetadata-Aktivität](control-flow-get-metadata-activity.md). 

## <a name="delete-activity-properties"></a>Eigenschaften der Delete-Aktivität

Ausführliche Informationen zu den Eigenschaften finden Sie unter [Delete-Aktivität](delete-activity.md).

## <a name="legacy-models"></a>Legacy-Modelle

>[!NOTE]
>Die folgenden Modelle werden aus Gründen der Abwärtskompatibilität weiterhin unverändert unterstützt. Es wird empfohlen, das oben erwähnte neue Modell zu verwenden. Die Data Factory-Erstellungsoberfläche wurde für die Generierung des neuen Modells angepasst.

### <a name="legacy-dataset-model"></a>Legacy-Datasetmodell

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft des Datasets muss auf **AzureBlob** festgelegt werden. |Ja |
| folderPath | Der Pfad zum Container und Ordner in Blob Storage. <br/><br/>Für den Pfad mit Ausnahme des Containernamens werden Platzhalterfilter unterstützt. Folgende Platzhalter sind zulässig: `*` (entspricht null [0] oder mehr Zeichen) und `?` (entspricht null [0] oder einem einzelnen Zeichen). Verwenden Sie `^` als Escapezeichen, wenn Ihr Ordnername einen Platzhalter oder dieses Escapezeichen enthält. <br/><br/>Beispiel: myblobcontainer/myblobfolder/. Weitere Beispiele finden Sie unter [Beispiele für Ordner- und Dateifilter](#folder-and-file-filter-examples). |Ja für die Kopier- oder Suchaktivität, nein für die GetMetadata-Aktivität |
| fileName | Name oder Platzhalterfilter für die Blobs unter dem angegebenen Wert für **folderPath**. Wenn Sie für diese Eigenschaft keinen Wert angeben, zeigt das Dataset auf alle Blobs im Ordner. <br/><br/>Für Filter sind folgende Platzhalter zulässig: `*` (entspricht null (0) oder mehr Zeichen) und `?` (entspricht null (0) oder einem einzelnen Zeichen).<br/>- Beispiel 1: `"fileName": "*.csv"`<br/>- Beispiel 2: `"fileName": "???20180427.txt"`<br/>Verwenden Sie `^` als Escapezeichen, wenn der tatsächliche Dateiname einen Platzhalter oder dieses Escapezeichen enthält.<br/><br/>Wenn **fileName** nicht für ein Ausgabedataset und **preserveHierarchy** nicht in der Aktivitätssenke angegeben ist, generiert die Kopieraktivität den Blobnamen automatisch mit dem folgenden Muster: *Data.[GUID der Aktivitätsausführungs-ID].[GUID bei Verwendung von „FlattenHierarchy“].[Format, sofern konfiguriert].[Komprimierung, sofern konfiguriert]* . Beispiel: „Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz“. <br/><br/>Wenn Sie Daten aus einer Quelle im Tabellenformat kopieren und dabei anstelle einer Abfrage den Tabellennamen verwenden, lautet das Namensmuster „ *[Tabellenname].[Format].[Komprimierung, sofern konfiguriert]* “. Beispiel: „MyTable.csv“. |Nein |
| modifiedDatetimeStart | Die Dateien werden anhand des Attributs „Letzte Änderung“ gefiltert. Die Dateien werden ausgewählt, wenn der Zeitpunkt der letzten Änderung innerhalb des Zeitbereichs zwischen `modifiedDatetimeStart` und `modifiedDatetimeEnd` liegt. Die Zeit wird auf die UTC-Zeitzone im Format „2018-12-01T05:00:00Z“ angewandt. <br/><br/> Hinweis: Die Aktivierung dieser Einstellung wirkt sich auf die Gesamtleistung der Datenverschiebung aus, wenn Sie große Dateimengen filtern möchten. <br/><br/> Die Eigenschaften können **NULL** sein, was bedeutet, dass kein Dateiattributfilter auf das Dataset angewendet wird.  Wenn `modifiedDatetimeStart` einen datetime-Wert aufweist, aber `modifiedDatetimeEnd` **NULL** ist, werden die Dateien ausgewählt, deren Attribut für die letzte Änderung größer oder gleich dem datetime-Wert ist.  Wenn `modifiedDatetimeEnd` den datetime-Wert aufweist, aber `modifiedDatetimeStart` **NULL** ist, werden die Dateien ausgewählt, deren Attribut für die letzte Änderung kleiner als der datetime-Wert ist.| Nein |
| modifiedDatetimeEnd | Die Dateien werden anhand des Attributs „Letzte Änderung“ gefiltert. Die Dateien werden ausgewählt, wenn der Zeitpunkt der letzten Änderung innerhalb des Zeitbereichs zwischen `modifiedDatetimeStart` und `modifiedDatetimeEnd` liegt. Die Zeit wird auf die UTC-Zeitzone im Format „2018-12-01T05:00:00Z“ angewandt. <br/><br/> Hinweis: Die Aktivierung dieser Einstellung wirkt sich auf die Gesamtleistung der Datenverschiebung aus, wenn Sie große Dateimengen filtern möchten. <br/><br/> Die Eigenschaften können **NULL** sein, was bedeutet, dass kein Dateiattributfilter auf das Dataset angewendet wird.  Wenn `modifiedDatetimeStart` einen datetime-Wert aufweist, aber `modifiedDatetimeEnd` **NULL** ist, werden die Dateien ausgewählt, deren Attribut für die letzte Änderung größer oder gleich dem datetime-Wert ist.  Wenn `modifiedDatetimeEnd` den datetime-Wert aufweist, aber `modifiedDatetimeStart` **NULL** ist, werden die Dateien ausgewählt, deren Attribut für die letzte Änderung kleiner als der datetime-Wert ist.| Nein |
| format | Wenn Sie Dateien unverändert zwischen dateibasierten Speichern kopieren möchten (binäre Kopie), überspringen Sie den Formatabschnitt in den Definitionen der Eingabe- und Ausgabedatasets.<br/><br/>Für das Analysieren oder Generieren von Dateien mit einem bestimmten Format werden die folgenden Dateiformattypen unterstützt: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** und **ParquetFormat**. Sie müssen die **type**-Eigenschaft unter **format** auf einen dieser Werte festlegen. Weitere Informationen finden Sie in den Abschnitten [Textformat](supported-file-formats-and-compression-codecs-legacy.md#text-format), [JSON-Format](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro-Format](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Orc-Format](supported-file-formats-and-compression-codecs-legacy.md#orc-format) und [Parquet-Format](supported-file-formats-and-compression-codecs-legacy.md#parquet-format). |Nein (nur für Szenarien mit Binärkopien) |
| compression | Geben Sie den Typ und den Grad der Komprimierung für die Daten an. Weitere Informationen finden Sie unter [Unterstützte Dateiformate und Codecs für die Komprimierung](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Unterstützte Typen sind **GZip**, **Deflate**, **BZIP2** und **ZipDeflate**.<br/>Unterstützte Grade sind **Optimal** und **Schnellste**. |Nein |

>[!TIP]
>Um alle Blobs in einem Ordner zu kopieren, geben Sie nur **folderPath** an.<br>Sie können einen einzelnen Blob mit einem angegebenen Namen kopieren, indem Sie **folderPath** für den Ordner und **fileName** für den Dateinamen angeben.<br>Sie können eine Teilmenge der Blobs in einen Ordner kopieren, indem Sie **folderPath** für den Ordner und **fileName** für den Platzhalterfilter angeben. 

**Beispiel:**

```json
{
    "name": "AzureBlobDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
            "referenceName": "<Azure Blob storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
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

### <a name="legacy-source-model-for-the-copy-activity"></a>Legacyquellmodell für die Kopieraktivität

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft der Quelle der Kopieraktivität muss auf **BlobSource** festgelegt werden. |Ja |
| recursive | Gibt an, ob die Daten rekursiv aus den Unterordnern oder nur aus dem angegebenen Ordner gelesen werden. Beachten Sie Folgendes: Wenn **recursive** auf **TRUE** festgelegt ist und es sich bei der Senke um einen dateibasierten Speicher handelt, wird ein leerer Ordner oder Unterordner nicht in die Senke kopiert und dort auch nicht erstellt.<br/>Zulässige Werte sind **true** (Standard) und **false**. | Nein |
| maxConcurrentConnections | Die Anzahl gleichzeitiger Verbindungen mit dem Speicher. Geben Sie diesen Wert nur an, wenn Sie die gleichzeitigen Verbindungen mit dem Datenspeicher begrenzen möchten. | Nein |

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Blob input dataset name>",
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
                "type": "BlobSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="legacy-sink-model-for-the-copy-activity"></a>Legacysenkenmodell für die Kopieraktivität

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| type | Die **type**-Eigenschaft der Senke der Kopieraktivität muss auf **BlobSink** festgelegt werden. |Ja |
| copyBehavior | Definiert das Kopierverhalten, wenn es sich bei der Quelle um Dateien aus einem dateibasierten Datenspeicher handelt.<br/><br/>Zulässige Werte sind:<br/><b>- PreserveHierarchy (Standard)</b>: Behält die Dateihierarchie im Zielordner bei. Der relative Pfad der Quelldatei zum Quellordner entspricht dem relativen Pfad der Zieldatei zum Zielordner.<br/><b>- FlattenHierarchy</b>: Alle Dateien aus dem Quellordner befinden sich auf der ersten Ebene des Zielordners. Die Namen für die Zieldateien werden automatisch generiert. <br/><b>- MergeFiles</b>: Alle Dateien aus dem Quellordner werden in einer Datei zusammengeführt. Wenn der Datei- oder Blobname angegeben wurde, entspricht der Name der Zusammenführungsdatei dem angegebenen Namen. Andernfalls wird der Dateiname automatisch generiert. | Nein |
| maxConcurrentConnections | Die Anzahl gleichzeitiger Verbindungen mit dem Speicher. Geben Sie diesen Wert nur an, wenn Sie die gleichzeitigen Verbindungen mit dem Datenspeicher begrenzen möchten. | Nein |

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Blob output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "BlobSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

## <a name="next-steps"></a>Nächste Schritte

Eine Liste der Datenspeicher, die von der Kopieraktivität in Data Factory als Quellen und Senken unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).
