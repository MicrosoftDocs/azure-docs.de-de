---
title: Kopieren von Daten in Dynamics (Common Data Service)
description: In diesem Artikel erfahren Sie, wie mithilfe einer Kopieraktivität in einer Data Factory-Pipeline Daten aus Microsoft Dynamics CRM oder Microsoft Dynamics 365 (Common Data Service) in unterstützte Senkendatenspeicher oder aus unterstützten Quelldatenspeichern nach Dynamics CRM oder Dynamics 365 kopiert werden.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 09/23/2020
ms.openlocfilehash: 204399186ae229324f9dc478e0ef58a173060013
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92638175"
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-service-or-dynamics-crm-by-using-azure-data-factory"></a>Kopieren von Daten aus und nach Dynamics 365 (Common Data Service) oder Dynamics CRM mithilfe von Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, wie Sie mit einer Kopieraktivität in Azure Data Factory Daten aus und nach Microsoft Dynamics 365 oder Dynamics CRM kopieren. Er baut auf dem Artikel [Kopieraktivität in Azure Data Factory](copy-activity-overview.md) auf, der eine allgemeine Übersicht über eine Kopieraktivität enthält.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Dieser Connector wird für die folgenden Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen- und Senkenmatrix](copy-activity-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)

Sie können Daten aus Dynamics 365 (Common Data Service) oder Dynamics CRM in jeden unterstützten Senkendatenspeicher kopieren. Zudem können Sie Daten aus jedem unterstützten Quelldatenspeicher in Dynamics 365 (Common Data Service) oder Dynamics CRM kopieren. Die Datenspeicher, die für eine Kopieraktivität als Quellen und Senken unterstützt werden, finden Sie in der Tabelle [Unterstützte Datenspeicher und Formate](copy-activity-overview.md#supported-data-stores-and-formats).

Dieser Dynamics-Connector unterstützt die Dynamics-Versionen 7 bis 9 sowohl online als auch lokal. Dies gilt insbesondere in folgenden Fällen:

- Version 7 ist Dynamics CRM 2015 zugeordnet.
- Version 8 ist Dynamics CRM 2016 und der frühen Version von Dynamics 365 zugeordnet.
- Version 9 ist der neueren Version von Dynamics 365 zugeordnet.

In der folgenden Tabelle finden Sie Informationen zu den unterstützten Authentifizierungstypen und Konfigurationen für die jeweiligen Dynamics-Versionen und -Produkte.

| Dynamics-Versionen | Authentifizierungstypen | Beispiele für verknüpfte Dienste |
|:--- |:--- |:--- |
| Common Data Service <br/><br/> Dynamics 365 (online) <br/><br/> Dynamics CRM Online | Azure Active Directory-Dienstprinzipal (Azure AD) <br/><br/> Office 365 | [Eigenschaften des verknüpften Diensts](#dynamics-365-and-dynamics-crm-online) |
| Lokale Dynamics 365-Instanz mit einer Bereitstellung mit Internetzugriff (Internet-Facing Deployment, IFD) <br/><br/> Dynamics CRM 2016 lokal mit IFD <br/><br/> Dynamics CRM 2015 lokal mit IFD | IFD | [Dynamics 365 und Dynamics CRM lokal mit IFD](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

Für Dynamics 365 werden insbesondere die folgenden Anwendungstypen unterstützt:

- Dynamics 365 for Sales
- Dynamics 365 for Customer Service
- Dynamics 365 for Field Service
- Dynamics 365 for Project Service Automation
- Dynamics 365 for Marketing

Andere Anwendungstypen wie Dynamics 365 Finance, Dynamics 365 for Operations und Dynamics 365 Talent werden von diesem Connector nicht unterstützt.

Dieser Dynamics-Connector basiert auf [Dynamics XRM-Tools](/dynamics365/customer-engagement/developer/build-windows-client-applications-xrm-tools).

>[!TIP]
>Mit dem [Dynamics AX-Connector](connector-dynamics-ax.md) können Sie Daten aus Dynamics 365 Finance und Dynamics 365 for Operations kopieren.

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie sich mit diesem Connector bei einem Azure AD-Dienstprinzipal authentifizieren möchten, müssen Sie die Server-zu-Server-Authentifizierung (S2S) in Common Data Service oder Dynamics einrichten. Eine ausführliche Anleitung finden Sie in [diesem Artikel](/powerapps/developer/common-data-service/build-web-applications-server-server-s2s-authentication).

## <a name="get-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für Dynamics verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Die folgenden Eigenschaften werden für den mit Dynamics verknüpften Dienst unterstützt.

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 und Dynamics CRM Online

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft muss auf "Dynamics", "DynamicsCrm" oder "CommonDataServiceForApps" festgelegt werden. | Ja |
| deploymentType | Der Bereitstellungstyp der Dynamics-Instanz. Für die Onlineversion von Dynamics muss der Wert "Online" lauten. | Ja |
| serviceUri | Die Dienst-URL Ihrer Dynamics-Instanz, auf die Sie auch über den Browser zugreifen. Ein Beispiel ist „https://\<organization-name>.crm[x].dynamics.com“. | Ja |
| authenticationType | Der Authentifizierungstyp für die Herstellung der Verbindung mit dem Dynamics-Server. Gültige Werte sind "AADServicePrincipal" und "Office365". | Ja |
| servicePrincipalId | Die Client-ID der Azure AD-Anwendung. | Ja, wenn "AADServicePrincipal" für authenticationType festgelegt ist |
| servicePrincipalCredentialType | Die Art von Anmeldeinformationen, die für die Authentifizierung beim Dienstprinzipal verwendet werden. Gültige Werte sind "ServicePrincipalKey" und "ServicePrincipalCert". | Ja, wenn "AADServicePrincipal" für authenticationType festgelegt ist |
| servicePrincipalCredential | Die Anmeldeinformationen für den Dienstprinzipal. <br/><br/>Wenn Sie "ServicePrincipalKey" als Anmeldeinformationstyp verwenden, kann `servicePrincipalCredential` eine Zeichenfolge sein, die Azure Data Factory bei der Bereitstellung eines verknüpften Diensts verschlüsselt. Alternativ kann es sich um einen Verweis auf ein Geheimnis in Azure Key Vault handeln. <br/><br/>Wenn Sie "ServicePrincipalCert" als Anmeldeinformation verwenden, muss `servicePrincipalCredential` ein Verweis auf ein Zertifikat in Azure Key Vault sein. | Ja, wenn "AADServicePrincipal" für authenticationType festgelegt ist |
| username | Der Benutzername, mit dem die Verbindung zu Dynamics hergestellt wird | Ja, wenn "Office365" für authenticationType festgelegt ist |
| password | Das Kennwort für das Benutzerkonto, das Sie für „username“ angegeben haben. Markieren Sie dieses Feld mit "SecureString", um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). | Ja, wenn "Office365" für authenticationType festgelegt ist |
| connectVia | Die [Integration Runtime](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden soll. Ohne Angabe eines Wertes verwendet diese Eigenschaft automatisch Azure Integration Runtime. | Nein für die Quelle. Ja für die Senke, wenn der mit der Quelle verknüpfte Dienst keine Integration Runtime aufweist. |

>[!NOTE]
>Der Dynamics-Connector hat zum Identifizieren Ihrer Dynamics CRM- oder Dynamics 365-Onlineinstanz früher die optionale Eigenschaft **organizationName** verwendet. Diese Eigenschaft funktioniert zwar immer noch, es wird jedoch empfohlen, stattdessen die neue Eigenschaft **serviceUri** anzugeben, um eine bessere Leistung bei der Instanzermittlung zu erzielen.

#### <a name="example-dynamics-online-using-azure-ad-service-principal-and-key-authentication"></a>Beispiel: Onlineversion von Dynamics unter Verwendung des Azure AD-Dienstprinzipals und der Schlüsselauthentifizierung

```json
{  
    "name": "DynamicsLinkedService",  
    "properties": {  
        "type": "Dynamics",  
        "typeProperties": {  
            "deploymentType": "Online",  
            "serviceUri": "https://<organization-name>.crm[x].dynamics.com",  
            "authenticationType": "AADServicePrincipal",  
            "servicePrincipalId": "<service principal id>",  
            "servicePrincipalCredentialType": "ServicePrincipalKey",  
            "servicePrincipalCredential": "<service principal key>"
        },  
        "connectVia": {  
            "referenceName": "<name of Integration Runtime>",  
            "type": "IntegrationRuntimeReference"  
        }  
    }  
}  
```
#### <a name="example-dynamics-online-using-azure-ad-service-principal-and-certificate-authentication"></a>Beispiel: Onlineversion von Dynamics unter Verwendung des Azure AD-Dienstprinzipals und der Zertifikatauthentifizierung

```json
{ 
    "name": "DynamicsLinkedService", 
    "properties": { 
        "type": "Dynamics", 
        "typeProperties": { 
            "deploymentType": "Online", 
            "serviceUri": "https://<organization-name>.crm[x].dynamics.com", 
            "authenticationType": "AADServicePrincipal", 
            "servicePrincipalId": "<service principal id>", 
            "servicePrincipalCredentialType": "ServicePrincipalCert", 
            "servicePrincipalCredential": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<AKV reference>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<certificate name in AKV>" 
            } 
        }, 
        "connectVia": { 
            "referenceName": "<name of Integration Runtime>", 
            "type": "IntegrationRuntimeReference" 
        } 
    } 
} 
```

#### <a name="example-dynamics-online-using-office-365-authentication"></a>Beispiel: Onlineversion von Dynamics mit Office 365-Authentifizierung

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "Online",
            "serviceUri": "https://<organization-name>.crm[x].dynamics.com",
            "authenticationType": "Office365",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>Dynamics 365 und Dynamics CRM lokal mit IFD

Die zusätzlichen Eigenschaften im Vergleich zur Onlineversion von Dynamics lauten **hostName** und **port** .

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft muss auf "Dynamics", "DynamicsCrm" oder "CommonDataServiceForApps" festgelegt werden. | Ja. |
| deploymentType | Der Bereitstellungstyp der Dynamics-Instanz. Der Wert muss für die lokale Dynamics-Bereitstellung mit IFD "OnPremisesWithIfd" lauten.| Ja. |
| hostName | Der Hostname des lokalen Dynamics-Servers. | Ja. |
| port | Der Port des lokalen Dynamics-Servers. | Nein. Der Standardwert ist 443. |
| organizationName | Der Organisationsname der Dynamics-Instanz. | Ja. |
| authenticationType | Der Authentifizierungstyp für die Herstellung der Verbindung mit dem Dynamics-Server. Geben Sie für eine lokale Dynamics-Bereitstellung mit IFD "Ifd" an. | Ja. |
| username | Der Benutzername, mit dem die Verbindung zu Dynamics hergestellt wird. | Ja. |
| password | Das Kennwort für das Benutzerkonto, das Sie bei „username“ angegeben haben. Sie können dieses Feld mit "SecureString" markieren, um es sicher in Data Factory zu speichern. Alternativ können Sie das Kennwort auch in Azure Key Vault speichern und es über die Kopieraktivität pullen lassen, wenn die Daten kopiert werden. Weitere Informationen finden Sie unter [Speichern von Anmeldeinformationen in Key Vault](store-credentials-in-key-vault.md). | Ja. |
| connectVia | Die [Integration Runtime](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden soll. Ohne Angabe eines Wertes verwendet diese Eigenschaft automatisch Azure Integration Runtime. | Nein für die Quelle, ja für die Senke |

#### <a name="example-dynamics-on-premises-with-ifd-using-ifd-authentication"></a>Beispiel: Dynamics (lokal) mit IFD und IFD-Authentifizierung

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics on-premises with IFD linked service using IFD authentication",
        "typeProperties": {
            "deploymentType": "OnPremisesWithIFD",
            "hostName": "contosodynamicsserver.contoso.com",
            "port": 443,
            "organizationName": "admsDynamicsTest",
            "authenticationType": "Ifd",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu [Datasets](concepts-datasets-linked-services.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die vom Dynamics-Dataset unterstützt werden.

Beim Kopieren von Daten aus und nach Dynamics werden die folgenden Eigenschaften unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft des Datasets muss auf "DynamicsEntity", "DynamicsCrmEntity" oder "CommonDataServiceForAppsEntity" festgelegt werden. |Ja |
| entityName | Der logische Name der abzurufenden Entität. | Nein für die Quelle, wenn für die Aktivitätsquelle "query" angegeben ist. Ja für die Senke. |

#### <a name="example"></a>Beispiel

```json
{
    "name": "DynamicsDataset",
    "properties": {
        "type": "DynamicsEntity",
        "schema": [],
        "typeProperties": {
            "entityName": "account"
        },
        "linkedServiceName": {
            "referenceName": "<Dynamics linked service name>",
            "type": "linkedservicereference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von den Quell- und Senkentypen für Dynamics unterstützt werden.

### <a name="dynamics-as-a-source-type"></a>Dynamics als Quelltyp

Beim Kopieren von Daten aus Dynamics werden die folgenden Eigenschaften im Abschnitt **source** der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft der Quelle der Kopieraktivität muss auf "DynamicsSource", "DynamicsCrmSource" oder "CommonDataServiceForAppsSource" festgelegt werden. | Ja |
| Abfrage | FetchXML ist eine proprietäre Abfragesprache, die in Dynamics (online und lokal) verwendet wird. Siehe folgendes Beispiel. Weitere Informationen finden Sie unter [Erstellen von Abfragen mit FetchXML](/previous-versions/dynamicscrm-2016/developers-guide/gg328332(v=crm.8)). | Nein, wenn `entityName` im Dataset angegeben ist |

>[!NOTE]
>Die PK-Spalte wird immer herauskopiert. Dies gilt auch, wenn sie nicht in der Spaltenprojektion enthalten ist, die Sie in der FetchXML-Abfrage konfigurieren.

> [!IMPORTANT]
>- Wenn Sie Daten aus Dynamics kopieren, ist die explizite Spaltenzuordnung aus Dynamics zur Senke optional. Es wird jedoch dringend zu der Zuordnung geraten, um ein deterministisches Kopierergebnis sicherzustellen.
>- Wenn ein Schema in Data Factory in der Erstellungsbenutzeroberfläche importiert wird, wird auf das Schema rückgeschlossen. Dies erfolgt, in dem die obersten Zeilen aus dem Ergebnis der Dynamics-Abfrage gesamplet werden, um die Liste der Quellspalten zu initialisieren. In diesem Fall werden Spalten ohne Werte in den obersten Reihen ausgelassen. Dasselbe Verhalten gilt für Kopiervorgänge, wenn keine explizite Zuordnung vorliegt. Sie können Spalten überprüfen und weitere zur Zuordnung hinzufügen. Diese Spalten werden auch während der Laufzeit des Kopiervorgangs berücksichtigt.

#### <a name="example"></a>Beispiel

```json
"activities":[
    {
        "name": "CopyFromDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DynamicsSource",
                "query": "<FetchXML Query>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sample-fetchxml-query"></a>FetchXML-Beispielabfrage

```xml
<fetch>
  <entity name="account">
    <attribute name="accountid" />
    <attribute name="name" />
    <attribute name="marketingonly" />
    <attribute name="modifiedon" />
    <order attribute="modifiedon" descending="false" />
    <filter type="and">
      <condition attribute ="modifiedon" operator="between">
        <value>2017-03-10 18:40:00z</value>
        <value>2017-03-12 20:40:00z</value>
      </condition>
    </filter>
  </entity>
</fetch>
```

### <a name="dynamics-as-a-sink-type"></a>Dynamics als Senkentyp

Beim Kopieren von Daten nach Dynamics werden die folgenden Eigenschaften im Abschnitt **sink** der Kopieraktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft der Senke der Kopieraktivität muss auf "DynamicsSink", "DynamicsCrmSink" oder "CommonDataServiceForAppsSink" festgelegt werden. | Ja. |
| writeBehavior | Das Schreibverhalten des Vorgangs. Der Wert muss "Upsert" lauten. | Ja |
| alternateKeyName | Der alternative Schlüsselname, der zum Ausführen eines upsert-Vorgangs für Ihre Entität definiert wurde | Nein. |
| writeBatchSize | Die Zeilenanzahl der Daten, die in jedem Batch in Dynamics geschrieben werden. | Nein. Der Standardwert ist 10. |
| ignoreNullValues | Gibt an, ob NULL-Werte aus anderen Eingabedaten als Schlüsselfeldern während eines Schreibvorgangs ignoriert werden sollen.<br/><br/>Gültige Werte sind **TRUE** und **FALSE** .<ul><li>**TRUE** : Daten im Zielobjekt bleiben unverändert, wenn Sie einen upsert- oder update-Vorgang ausführen. Fügt beim Ausführen eines insert-Vorgangs einen definierten Standardwert ein.</li><li>**FALSE** : Aktualisieren Sie die Daten im Zielobjekt auf einen NULL-Wert, wenn Sie einen upsert- oder update-Vorgang ausführen. Fügen Sie beim Ausführen eines insert-Vorgangs einen NULL-Wert ein.</li></ul> | Nein. Der Standardwert ist **FALSE** . |

>[!NOTE]
>Sowohl für die Senke **writeBatchSize** als auch für die Kopieraktivität **[parallelCopies](copy-activity-performance-features.md#parallel-copy)** für die Dynamics-Senke lautet der Standardwert 10. Daher werden standardmäßig 100 Datensätze gleichzeitig an Dynamics übermittelt.

Für Dynamics 365 (online) besteht ein Limit von [zwei gleichzeitigen Batchaufrufen pro Organisation](/previous-versions/dynamicscrm-2016/developers-guide/jj863631(v=crm.8)#Run-time%20limitations). Bei Überschreitung dieses Grenzwerts wird eine Ausnahme aufgrund von Serverauslastung ausgelöst, bevor die erste Anforderung jemals ausgeführt wird. Sorgen Sie dafür, dass **writeBatchSize** den Wert 10 nicht überschreitet, um eine solche Einschränkung gleichzeitiger Aufrufe zu vermeiden.

Die optimale Kombination von **writeBatchSize** und **parallelCopies** hängt vom Schema Ihrer Entität ab. Schemaelemente enthalten die Anzahl von Spalten, die Zeilengröße und die Anzahl der Plug-Ins, Workflows oder Workflowaktivitäten, die an diese Aufrufe gekoppelt sind. Die Standardeinstellung **writeBatchSize** (10) &times; **parallelCopies** (10) wird für den Dynamics-Dienst empfohlen. Dieser Wert funktioniert für die meisten Dynamics-Entitäten, obwohl er unter Umständen nicht die beste Leistung erzielt. Sie können die Leistung optimieren, indem Sie die Kombination in Ihren Kopieraktivitätseinstellungen anpassen.

#### <a name="example"></a>Beispiel

```json
"activities":[
    {
        "name": "CopyToDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Dynamics output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DynamicsSink",
                "writeBehavior": "Upsert",
                "writeBatchSize": 10,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="data-type-mapping-for-dynamics"></a>Datentypzuordnung für Dynamics

In der folgenden Tabelle finden Sie die Zuordnungen von Dynamics-Datentypen zu Data Factory-Zwischendatentypen, die beim Kopieren von Daten aus Dynamics verwendet werden. Im Artikel [Schema- und Datentypzuordnungen in der Kopieraktivität](copy-activity-schema-and-type-mapping.md) erfahren Sie, wie eine Kopieraktivität einem Quellschema und ein Datentyp einer Senke zugeordnet wird.

Konfigurieren Sie anhand der folgenden Zuordnungstabelle den entsprechenden Data Factory-Datentyp in einer Datasetstruktur, die auf Ihrem Dynamics-Quelldatentyp basiert.

| Dynamics-Datentyp | Data Factory-Zwischendatentyp | Als Quelle unterstützt | Als Senke unterstützt |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode.BigInt | Long | ✓ | ✓ |
| AttributeTypeCode.Boolean | Boolean | ✓ | ✓ |
| AttributeType.Customer | GUID | ✓ | ✓ ([weitere Informationen](#writing-data-to-a-lookup-field)) |
| AttributeType.DateTime | Datetime | ✓ | ✓ |
| AttributeType.Decimal | Decimal | ✓ | ✓ |
| AttributeType.Double | Double | ✓ | ✓ |
| AttributeType.EntityName | String | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | GUID | ✓ | ✓ ([weitere Informationen](#writing-data-to-a-lookup-field)) |
| AttributeType.ManagedProperty | Boolean | ✓ | |
| AttributeType.Memo | String | ✓ | ✓ |
| AttributeType.Money | Decimal | ✓ | ✓ |
| AttributeType.Owner | GUID | ✓ | ✓ ([weitere Informationen](#writing-data-to-a-lookup-field)) |
| AttributeType.Picklist | Int32 | ✓ | ✓ |
| AttributeType.Uniqueidentifier | GUID | ✓ | ✓ |
| AttributeType.String | String | ✓ | ✓ |
| AttributeType.State | Int32 | ✓ | ✓ |
| AttributeType.Status | Int32 | ✓ | ✓ |

> [!NOTE]
> Die Dynamics-Datentypen **AttributeType.CalendarRules** , **AttributeType.MultiSelectPicklist** und **AttributeType.PartyList** werden nicht unterstützt.

## <a name="writing-data-to-a-lookup-field"></a>Schreiben von Daten in ein Nachschlagefeld

Wenn Sie Daten in ein Nachschlagefeld mit mehreren Zielen wie „Kunde“ und „Besitzer“ schreiben möchten, halten Sie sich an die folgende Anleitung und das entsprechende Beispiel:

1. Stellen Sie sicher, dass Ihre Quelle beide Feldwerte und den Namen der Zielentität enthält.
   - Wenn alle Datensätze der gleichen Zielentität zugeordnet werden, stellen Sie sicher, dass eine der folgenden Bedingungen zutrifft:
      - Ihre Quelldaten verfügen über eine Spalte, in der der Name der Zielentität gespeichert ist.
      - Sie haben der Quelle der Kopieraktivität eine zusätzliche Spalte hinzugefügt, um die Zielentität zu definieren.
   - Wenn unterschiedliche Datensätze verschiedenen Zielentitäten zugeordnet werden, müssen Sie sich vergewissern, dass Ihre Quelldaten über eine Spalte verfügen, in der der Name der entsprechenden Zielentität gespeichert ist.

1. Ordnen Sie die Quellspalte mit den Werten und die mit den Entitätsverweisen der Senke zu. Die Spalte mit den Entitätsverweisen muss einer virtuellen Spalte mit dem besonderen Benennungsmuster `{lookup_field_name}@EntityReference` zugeordnet werden. Die Spalte gibt es in Dynamics in der Tat gar nicht. Mit ihr wird angegeben, dass es sich bei dieser Spalte um die Metadatenspalte eines bestimmten Nachschlagefelds mit mehreren Zielen handelt.

Angenommen, die Quelle verfügt über die folgenden beiden Spalten:

- Die Spalte **CustomerField** weist den Typ **GUID** auf, wobei es sich um den Wert des Primärschlüssels der Zielentität in Dynamics handelt.
- Die Spalte **Target** weist den Typ **String** (Zeichenfolge) auf, wobei es sich um den logischen Namen der Zielentität handelt.

Sie möchten nun solche Daten für die Senke in das Dynamics-Entitätsfeld **CustomerField** vom Typ **Customer** kopieren.

Ordnen Sie die beiden Spalten in der Spaltenzuordnung für die Kopieraktivität wie folgt zu:

- **CustomerField** zu **CustomerField** . Bei dieser Zuordnung handelt es sich um die normale Feldzuordnung.
- **Target** zu **CustomerField\@EntityReference** . Die Senkenspalte ist virtuell und stellt den Entitätsverweis dar. Sie müssen solche Feldnamen in einer Zuordnung eingeben, da diese nach einem Schemaimport nicht angezeigt werden.

![Spaltenzuordnung für Dynamics-Nachschlagefelder](./media/connector-dynamics-crm-office-365/connector-dynamics-lookup-field-column-mapping.png)

Wenn alle Ihre Quelldatensätze derselben Zielentität zugeordnet werden und Ihre Quelldaten nicht den Namen der Zielentität enthalten, fügen Sie einfach in der Quelle der Kopieraktivität eine zusätzliche Spalte hinzu. Geben Sie der neuen Spalte einen Namen nach dem Muster `{lookup_field_name}@EntityReference`, legen Sie den Namen der Zielentität als Wert fest, und fahren Sie wie gewohnt mit der Spaltenzuordnung fort. Wenn die Spaltennamen Ihrer Quelle and Senke identisch sind, können Sie die explizite Spaltenzuordnung auch überspringen, da die Kopieraktivität Spalten automatisch nach dem Namen zuordnet.

![Dynamics-Nachschlagefeld, das eine Spalte für den Entitätsverweis hinzufügt](./media/connector-dynamics-crm-office-365/connector-dynamics-add-entity-reference-column.png)

## <a name="lookup-activity-properties"></a>Eigenschaften der Lookup-Aktivität

Ausführliche Informationen zu den Eigenschaften finden Sie unter [Lookup-Aktivität in Azure Data Factory](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Nächste Schritte
Eine Liste der Datenspeicher, die von der Kopieraktivität in Azure Data Factory als Quellen und Senken unterstützt werden, finden Sie unter [Unterstützte Datenspeicher und Formate](copy-activity-overview.md#supported-data-stores-and-formats).