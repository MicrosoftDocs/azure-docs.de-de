---
title: Kopieren von Daten nach bzw. aus Azure Data Lake Storage Gen1
description: Es wird beschrieben, wie Sie Daten per Azure Data Factory nach bzw. aus Data Lake Store kopieren.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 25b1ff3c-b2fd-48e5-b759-bb2112122e30
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
ms.custom: devx-track-csharp
robots: noindex
ms.openlocfilehash: 16cef1fb35efcbe12a4054304e3f354c03b37227
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92637648"
---
# <a name="copy-data-to-and-from-data-lake-storage-gen1-by-using-data-factory"></a>Kopieren von Daten nach bzw. aus Data Lake Storage Gen1 mit Data Factory
> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](data-factory-azure-datalake-connector.md)
> * [Version 2 (aktuelle Version)](../connector-azure-data-lake-store.md)

> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, helfen Ihnen die Informationen unter [Azure Data Lake Storage Gen1-Connector in V2](../connector-azure-data-lake-store.md) weiter.

In diesem Artikel wird beschrieben, wie Sie die Copy-Aktivität (Kopieraktivität) in Azure Data Factory verwenden, um Daten nach und aus Azure Data Lake Storage Gen1 zu verschieben (bisher als Azure Data Lake Store bezeichnet). Er baut auf dem Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) (Übersicht über die Datenverschiebung per Kopieraktivität) auf.

## <a name="supported-scenarios"></a>Unterstützte Szenarios
Sie können Daten **aus Azure Data Lake Store** in die folgenden Datenspeicher kopieren:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Sie können Daten aus den folgenden Datenspeichern **nach Azure Data Lake Store** kopieren:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> Erstellen Sie ein Data Lake Store-Konto, bevor Sie eine Pipeline mit Kopieraktivität erstellen. Weitere Informationen finden Sie unter [Erste Schritte mit Data Lake Store mithilfe des Azure-Portals](../../data-lake-store/data-lake-store-get-started-portal.md).

## <a name="supported-authentication-types"></a>Unterstützte Authentifizierungstypen
Der Data Lake Store-Connector unterstützt die folgenden Authentifizierungstypen:
* Dienstprinzipalauthentifizierung
* Authentifizierung von Benutzeranmeldeinformationen (OAuth)

Wir empfehlen Ihnen, die Dienstprinzipalauthentifizierung zu verwenden. Dies gilt besonders für einen geplanten Datenkopiervorgang. Bei der Authentifizierung der Benutzeranmeldeinformationen kann es zu einem Ablauf von Token kommen. Informationen zu den Konfigurationsdetails finden Sie im Abschnitt [Eigenschaften des verknüpften Diensts](#linked-service-properties).

## <a name="get-started"></a>Erste Schritte
Sie können eine Pipeline mit einer Kopieraktivität erstellen, die Daten mithilfe verschiedener Tools/APIs in einen bzw. aus einem Azure Data Lake Store verschiebt.

Die einfachste Möglichkeit, eine Pipeline zum Kopieren von Daten zu erstellen, ist die Verwendung des **Kopier-Assistenten** . Ein Tutorial zur Erstellung einer Pipeline mit dem Kopier-Assistenten finden Sie unter [Tutorial: Erstellen einer Pipeline mit dem Kopier-Assistenten](data-factory-copy-data-wizard-tutorial.md).

Sie können auch die folgenden Tools zum Erstellen einer Pipeline verwenden: **Visual Studio** , **Azure PowerShell** , **Azure Resource Manager-Vorlage** , **.NET-API** und **REST-API** . Im [Tutorial zur Kopieraktivität](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) finden Sie detaillierte Anweisungen, wie Sie eine Pipeline mit einer Kopieraktivität erstellen können.

Unabhängig davon, ob Sie Tools oder APIs verwenden, führen Sie die folgenden Schritte aus, um eine Pipeline zu erstellen, die Daten aus einem Quelldatenspeicher in einen Senkendatenspeicher verschiebt:

1. Eine **Data Factory** . Eine Data Factory kann eine oder mehrere Pipelines enthalten.
2. Erstellen **verknüpfter Dienste** zum Verknüpfen von Eingabe- und Ausgabedatenspeichern mit Ihrer Data Factory. Wenn Sie beispielsweise Daten aus einer Azure Blob Storage- in eine Azure Data Lake Store-Instanz kopieren, erstellen Sie zwei verknüpfte Dienste, um Ihr Azure Storage-Konto und Azure Data Lake Store mit Ihrer Data Factory zu verknüpfen. Informationen zu Eigenschaften von verknüpften Diensten, die spezifisch für Azure Data Lake Store sind, finden Sie im Abschnitt [Eigenschaften des verknüpften Diensts](#linked-service-properties).
2. Erstellen von **Datasets** zur Darstellung von Eingabe- und Ausgabedaten für den Kopiervorgang. Im Beispiel, das im letzten Schritt erwähnt wurde, erstellen Sie ein Dataset, um den Blobcontainer und den Ordner mit den Eingabedaten anzugeben. Außerdem erstellen Sie ein weiteres Dataset zum Angeben des Ordners und Dateipfads in der Data Lake Store-Instanz, die die aus dem Blobspeicher kopierten Daten enthält. Informationen zu Dataset-Eigenschaften, die spezifisch für Azure Data Lake Store sind, finden Sie im Abschnitt [Dataset-Eigenschaften](#dataset-properties).
3. Erstellen einer **Pipeline** mit einer Kopieraktivität, die ein Dataset als Eingabe und ein Dataset als Ausgabe akzeptiert. Im oben erwähnten Beispiel verwenden Sie BlobSource als Quelle und AzureDataLakeStoreSink als Senke für die Kopieraktivität. Wenn Sie einen Kopiervorgang von Azure Data Lake Store zu Azure Blob Storage durchführen, verwenden Sie entsprechend AzureDataLakeStoreSource und BlobSink in der Kopieraktivität. Informationen zu den Eigenschaften von Kopieraktivitäten, die spezifisch für Azure Data Lake Store sind, finden Sie im Abschnitt [Eigenschaften der Kopieraktivität](#copy-activity-properties). Ausführliche Informationen zur Verwendung eines Datenspeichers als Quelle oder Senke erhalten Sie, indem Sie im vorherigen Abschnitt auf den Link für Ihren Datenspeicher klicken.

Wenn Sie den Assistenten verwenden, werden automatisch JSON-Definitionen für diese Data Factory-Entitäten (verknüpfte Diensten, Datasets und die Pipeline) erstellt. Bei Verwendung von Tools und APIs (mit Ausnahme der .NET-API) definieren Sie diese Data Factory-Entitäten im JSON-Format. Beispiele mit JSON-Definitionen für Data Factory-Entitäten für das Kopieren von Daten in und aus Azure Data Lake Store finden Sie in diesem Artikel im Abschnitt [JSON-Beispiele](#json-examples-for-copying-data-to-and-from-data-lake-store).

Die folgenden Abschnitte enthalten Details zu JSON-Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für Data Lake Store verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts
Ein verknüpfter Dienst verbindet einen Data Store mit einer Data Factory. Sie erstellen einen verknüpften Dienst vom Typ **AzureDataLakeStore** , um Ihre Data Lake Store-Instanz mit Ihrer Data Factory zu verknüpfen. In der folgenden Tabelle sind die JSON-Elemente beschrieben, die für verknüpfte Dienste von Data Lake Store spezifisch sind. Sie können zwischen der Authentifizierung per Dienstprinzipal und per Benutzeranmeldeinformationen wählen.

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| **type** | Die type-Eigenschaft muss auf **AzureDataLakeStore** festgelegt werden. | Ja |
| **dataLakeStoreUri** | Informationen zum Azure Data Lake Store-Konto. Diese Informationen haben eines der folgenden Formate: `https://[accountname].azuredatalakestore.net/webhdfs/v1` oder `adl://[accountname].azuredatalakestore.net/`. | Ja |
| **subscriptionId** | ID des Azure-Abonnements, zu dem das Data Lake Store-Konto gehört. | Erforderlich für Senke |
| **resourceGroupName** | Name der Azure-Ressourcengruppe, zu der das Data Lake Store-Konto gehört. | Erforderlich für Senke |

### <a name="service-principal-authentication-recommended"></a>Dienstprinzipalauthentifizierung (empfohlen)
Wenn Sie die Dienstprinzipalauthentifizierung verwenden möchten, registrieren Sie in Azure Active Directory (Azure AD) eine Anwendungsentität und gewähren ihr Zugriff auf Data Lake Store. Eine ausführliche Anleitung finden Sie unter [Dienst-zu-Dienst-Authentifizierung](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md). Notieren Sie sich die folgenden Werte, die Sie zum Definieren des verknüpften Diensts verwenden:
* Anwendungs-ID
* Anwendungsschlüssel
* Mandanten-ID

> [!IMPORTANT]
> Erteilen Sie dem Dienstprinzipal die korrekte Berechtigung in Azure Data Lake Store:
>- **Wenn Sie Data Lake Store als Quelle verwenden möchten** , erteilen Sie mindestens die Datenzugriffsberechtigung **Lesen und Ausführen** , um den Inhalt eines Ordners aufzulisten und zu kopieren, oder die Berechtigung **Lesen** , um eine einzelne Datei zu kopieren. Es gelten keine Anforderungen für die Zugriffssteuerung auf Kontoebene.
>- **Wenn Sie Data Lake Store als Senke verwenden möchten** , erteilen Sie mindestens die Datenzugriffsberechtigung **Schreiben und Ausführen** für die Erstellung untergeordneter Elemente im Ordner. Wenn Sie Azure IR für die Erstellung von Kopien verwenden (sowohl Quellen als auch Senken befinden sich in der Cloud), um Data Factory die Erkennung der Data Lake Store-Region zu ermöglichen, erteilen Sie in der Kontozugriffssteuerung (IAM) mindestens die Rolle **Leser** . Falls Sie die Verwendung dieser IAM-Rolle vermeiden möchten, geben Sie in der Kopieraktivität [executionLocation](data-factory-data-movement-activities.md#global) mit dem Standort Ihrer Data Lake Store-Instanz an.
>- Wenn Sie **Pipelines mithilfe des Kopier-Assistenten erstellen** , erteilen Sie in der Kontozugriffssteuerung (IAM) mindestens die Rolle **Leser** . Erteilen Sie außerdem für Ihren Data Lake Store-Stamm („/“) und die untergeordneten Elemente mindestens die Berechtigung **Lesen und Ausführen** . Andernfalls wird unter Umständen die Meldung „Die angegebenen Anmeldeinformationen sind ungültig“ angezeigt.

Verwenden Sie die Dienstprinzipalauthentifizierung, indem Sie die folgenden Eigenschaften angeben:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| **servicePrincipalId** | Geben Sie die Client-ID der Anwendung an. | Ja |
| **servicePrincipalKey** | Geben Sie den Schlüssel der Anwendung an. | Ja |
| **tenant** | Geben Sie die Mandanteninformationen (Domänenname oder Mandanten-ID) für Ihre Anwendung an. Diese können Sie abrufen, indem Sie im Azure-Portal mit der Maus auf den Bereich oben rechts zeigen. | Ja |

**Beispiel: Dienstprinzipalauthentifizierung**
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

### <a name="user-credential-authentication"></a>Authentifizierung mit Benutzeranmeldeinformationen
Alternativ können Sie die Authentifizierung mit Benutzeranmeldeinformationen verwenden, um Daten aus bzw. nach Data Lake Store zu kopieren. Geben Sie hierzu die folgenden Eigenschaften an:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| **Autorisierung** | Klicken Sie im Data Factory-Editor auf die Schaltfläche **Autorisieren** , und geben Sie Ihre Anmeldeinformationen ein. Hierdurch wird die automatisch generierte Autorisierungs-URL dieser Eigenschaft zugewiesen. | Ja |
| **sessionId** | OAuth-Sitzungs-ID aus der OAuth-Autorisierungssitzung. Jede Sitzungs-ID ist eindeutig und darf nur einmal verwendet werden. Diese Einstellung wird automatisch generiert, wenn Sie den Data Factory-Editor verwenden. | Ja |

> [!IMPORTANT]
> Erteilen Sie dem Benutzer die korrekte Berechtigung in Azure Data Lake Store:
>- **Wenn Sie Data Lake Store als Quelle verwenden möchten** , erteilen Sie mindestens die Datenzugriffsberechtigung **Lesen und Ausführen** , um den Inhalt eines Ordners aufzulisten und zu kopieren, oder die Berechtigung **Lesen** , um eine einzelne Datei zu kopieren. Es gelten keine Anforderungen für die Zugriffssteuerung auf Kontoebene.
>- **Wenn Sie Data Lake Store als Senke verwenden möchten** , erteilen Sie mindestens die Datenzugriffsberechtigung **Schreiben und Ausführen** für die Erstellung untergeordneter Elemente im Ordner. Wenn Sie Azure IR für die Erstellung von Kopien verwenden (sowohl Quellen als auch Senken befinden sich in der Cloud), um Data Factory die Erkennung der Data Lake Store-Region zu ermöglichen, erteilen Sie in der Kontozugriffssteuerung (IAM) mindestens die Rolle **Leser** . Falls Sie die Verwendung dieser IAM-Rolle vermeiden möchten, geben Sie in der Kopieraktivität [executionLocation](data-factory-data-movement-activities.md#global) mit dem Standort Ihrer Data Lake Store-Instanz an.
>- Wenn Sie **Pipelines mithilfe des Kopier-Assistenten erstellen** , erteilen Sie in der Kontozugriffssteuerung (IAM) mindestens die Rolle **Leser** . Erteilen Sie außerdem für Ihren Data Lake Store-Stamm („/“) und die untergeordneten Elemente mindestens die Berechtigung **Lesen und Ausführen** . Andernfalls wird unter Umständen die Meldung „Die angegebenen Anmeldeinformationen sind ungültig“ angezeigt.

**Beispiel: Authentifizierung mit Benutzeranmeldeinformationen**
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<session ID>",
            "authorization": "<authorization URL>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

#### <a name="token-expiration"></a>Tokenablauf
Der Autorisierungscode, den Sie mit der Schaltfläche **Autorisieren** generieren, läuft nach einer bestimmten Zeit ab. Die folgende Meldung bedeutet, dass das Authentifizierungstoken abgelaufen ist:

Fehler beim Anmeldevorgang: invalid_grant – AADSTS70002: Fehler beim Überprüfen der Anmeldeinformationen. AADSTS70008: Die angegebene Zugriffserteilung ist abgelaufen oder wurde widerrufen. Ablaufverfolgungs-ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 Korrelations-ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Zeitstempel: 2015-12-15 21-09-31Z.

Die Zeiten bis zum Ablauf der Autorisierungscodes für die verschiedenen Benutzerkonten finden Sie in der folgenden Tabelle:

| Benutzertyp | Läuft ab nach |
|:--- |:--- |
| Benutzerkonten, die *nicht* von Azure Active Directory verwaltet werden (z.B. @hotmail.com oder @live.com) |12 Stunden |
| Benutzerkonten, die von Azure Active Directory verwaltet werden |14 Tage nach der letzten Sliceausführung <br/><br/>90 Tage, wenn ein Slice, das auf einem verknüpften OAuth-Dienst basiert, mindestens einmal alle 14 Tage ausgeführt wird. |

Wenn Sie Ihr Kennwort vor dem Ablaufzeitpunkt des Tokens ändern, läuft das Token sofort ab. Die oben angegebene Meldung wird angezeigt.

Sie können die Autorisierung für das Konto mit der Schaltfläche **Autorisieren** erneut durchführen, wenn das Token abgelaufen ist, um den verknüpften Dienst erneut bereitzustellen. Sie können Werte für die Eigenschaften **sessionId** und **authorization** auch programmgesteuert generieren. Verwenden Sie hierzu den folgenden Code:


```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```
Nähere Informationen zu den im Code verwendeten Data Factory-Klassen finden Sie in den Themen [AzureDataLakeStoreLinkedService-Klasse](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice), [AzureDataLakeAnalyticsLinkedService-Klasse](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice) und [AuthorizationSessionGetResponse-Klasse](/dotnet/api/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse). Fügen Sie einen Verweis auf Version `2.9.10826.1824` von `Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll` für die im Code verwendete `WindowsFormsWebAuthenticationDialog`-Klasse hinzu.

## <a name="troubleshooting-tips"></a>Tipps zur Problembehandlung

**Symptom:** Beim Kopieren von Daten **nach** Azure Data Lake Storage tritt folgender Fehler bei der Kopieraktivität auf:

  ```
  Failed to detect the region for Azure Data Lake account {your account name}. Please make sure that the Resource Group name: {resource group name} and subscription ID: {subscription ID} of this Azure Data Lake Store resource are correct.
  ```

**Grundursache:** Es gibt zwei mögliche Ursachen:

1. Die Werte von `resourceGroupName` und/oder `subscriptionId`, die im verknüpften Azure Data Lake Store-Dienst angegeben sind, sind falsch;
2. Der Benutzer oder der Dienstprinzipal verfügt nicht über die erforderliche Berechtigung.

**Lösung:**

1. Achten Sie darauf, dass die `subscriptionId` und der `resourceGroupName`, die Sie in den `typeProperties` für den verknüpften Dienst angeben, tatsächlich den für Ihr Data Lake-Konto gültigen Werten entsprechen.

2. Achten Sie darauf, dem Benutzer oder dem Dienstprinzipal für das Data Lake-Konto mindestens die Rolle **Leser** zu erteilen. Dazu gehen Sie wie folgt vor:

    1. Navigieren Sie im Azure-Portal zu Ihrem Data Lake Storage-Konto.
    2. Klicken Sie auf dem Data Lake Storage-Blatt auf **Zugriffssteuerung (IAM)** .
    3. Klicken Sie auf **Rollenzuweisung hinzufügen** .
    4. Legen Sie die **Rolle** auf **Leser** fest, und wählen Sie den Benutzer oder den Dienstprinzipal aus, den Sie zum Kopieren verwenden, um den Zugriff zu erteilen

3. Wenn Sie dem Benutzer oder dem Dienstprinzipal nicht die Rolle **Leser** zuweisen möchten, können Sie alternativ in der Kopieraktivität [einen Ausführungsort explizit angeben](data-factory-data-movement-activities.md#global), nämlich den Speicherort Ihrer Data Lake Storage-Instanz. Beispiel:

    ```json
    {
      "name": "CopyToADLS",
      "type": "Copy",
      ......
      "typeProperties": {
        "source": {
          "type": "<source type>"
        },
        "sink": {
          "type": "AzureDataLakeStoreSink"
        },
        "exeuctionLocation": "West US"
      }
    }
    ```

## <a name="dataset-properties"></a>Dataset-Eigenschaften
Um ein Dataset zur Darstellung von Eingabedaten in einem Data Lake Store anzugeben, legen Sie die **type** -Eigenschaft des Datasets auf **AzureDataLakeStore** fest. Legen Sie die **linkedServiceName** -Eigenschaft des Datasets auf den Namen des mit dem Data Lake Store verknüpften Diensts fest. Eine vollständige Liste mit den JSON-Abschnitten und -Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel [Erstellen von Datasets](data-factory-create-datasets.md). Die Abschnitte eines Datasets in JSON-Code, z.B. **structure** , **availability** und **policy** , sind für alle Datasettypen (z.B. Azure SQL-Datenbank, Azure-Blob und Azure-Tabelle) ähnlich. Der Abschnitt **typeProperties** unterscheidet sich bei jeder Art von Dataset und enthält beispielsweise Informationen zum Speicherort und Format der Daten im Datenspeicher.

Der Abschnitt **typeProperties** für ein Dataset des Typs **AzureDataLakeStore** enthält die folgenden Eigenschaften:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| **folderPath** |Der Pfad zum Container und Ordner in Data Lake Store. |Ja |
| **fileName** |Der Name der Datei in Azure Data Lake Store. Die **fileName** -Eigenschaft ist optional, und die Groß-/Kleinschreibung wird berücksichtigt. <br/><br/>Wenn Sie für **fileName** einen Wert angeben, funktioniert die Aktivität (einschließlich Kopieren) für die jeweilige Datei.<br/><br/>Wenn **fileName** nicht angegeben ist, werden beim Kopieren alle Dateien unter **folderPath** für das Eingabedataset einbezogen.<br/><br/>Wenn **fileName** nicht für ein Ausgabedataset und **preserveHierarchy** nicht in der Aktivitätssenke angegeben ist, hat der Name der generierten Datei das folgende Format: `Data._Guid_.txt`. Beispiel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt. |Nein |
| **partitionedBy** |Die **partitionedBy** -Eigenschaft ist optional. Sie können sie verwenden, um einen dynamischen Pfad und Dateinamen für Zeitreihendaten anzugeben. Beispiel: **folderPath** kann für jedes stündliche Datenaufkommen parametrisiert werden. Weitere Informationen und Beispiele finden Sie unter „partitionedBy-Eigenschaft“. |Nein |
| **format** | Die folgenden Formattypen werden unterstützt: **TextFormat** , **JsonFormat** , **AvroFormat** , **OrcFormat** und **ParquetFormat** . Sie müssen die **type** -Eigenschaft unter **format** auf einen dieser Werte festlegen. Weitere Informationen finden Sie in den Abschnitten [Textformat](data-factory-supported-file-and-compression-formats.md#text-format), [JSON-Format](data-factory-supported-file-and-compression-formats.md#json-format), [Avro-Format](data-factory-supported-file-and-compression-formats.md#avro-format), [ORC-Format](data-factory-supported-file-and-compression-formats.md#orc-format) und [Parquet-Format](data-factory-supported-file-and-compression-formats.md#parquet-format) des Artikels [Von Azure Data Factory unterstützte Datei- und Komprimierungsformate](data-factory-supported-file-and-compression-formats.md). <br><br> Wenn Sie Dateien unverändert zwischen dateibasierten Speichern kopieren möchten (binäre Kopie), können Sie den `format`-Abschnitt bei den Definitionen von Eingabe- und Ausgabedatasets überspringen. |Nein |
| **compression** | Geben Sie den Typ und den Grad der Komprimierung für die Daten an. Unterstützte Typen sind **GZip** , **Deflate** , **BZIP2** und **ZipDeflate** . Unterstützte Grade sind **Optimal** und **Schnellste** . Weitere Informationen finden Sie unter [Von Azure Data Factory unterstützte Datei- und Komprimierungsformate](data-factory-supported-file-and-compression-formats.md#compression-support). |Nein |

### <a name="the-partitionedby-property"></a>partitionedBy-Eigenschaft
Sie können die Eigenschaften **folderPath** und **fileName** für Zeitreihendaten mit der **partitionedBy** -Eigenschaft, Data Factory-Funktionen und Systemvariablen angeben. Ausführliche Informationen finden Sie im Artikel [Azure Data Factory – Funktionen und Systemvariablen](data-factory-functions-variables.md).


Im folgenden Beispiel wird `{Slice}` durch den Wert der Data Factory-Systemvariablen `SliceStart` im angegebenen Format (`yyyyMMddHH`) ersetzt. Der Name `SliceStart` bezieht sich auf die Startzeit des Slice. Die `folderPath`-Eigenschaft ist für jedes Slice anders, z.B. `wikidatagateway/wikisampledataout/2014100103` und `wikidatagateway/wikisampledataout/2014100104`.

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

Im folgenden Beispiel werden das Jahr, der Monat, der Tag und die Uhrzeit von `SliceStart` in separate Variablen extrahiert, die von den Eigenschaften `folderPath` und `fileName` verwendet werden:
```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
[
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```
Weitere Details zu Zeitreihen-Datasets, Planung und Slices finden Sie in den Artikeln [Datasets in Azure Data Factory](data-factory-create-datasets.md) und [Data Factory – Planung und Ausführung](data-factory-scheduling-and-execution.md).


## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität
Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Erstellen von Pipelines](data-factory-create-pipelines.md). Eigenschaften wie Name, Beschreibung, Eingabe- und Ausgabetabellen und Richtlinie sind für alle Arten von Aktivitäten verfügbar.

Die Eigenschaften im Abschnitt **typeProperties** einer Aktivität können je nach Aktivitätstyp variieren. Für eine Kopieraktivität variieren die Eigenschaften je nach Art der Quellen und Senken.

**AzureDataLakeStoreSource** unterstützt die folgende Eigenschaft im Abschnitt **typeProperties** :

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| **recursive** |Gibt an, ob die Daten rekursiv aus den Unterordnern oder nur aus dem angegebenen Ordner gelesen werden. |True (Standardwert), False |Nein |

**AzureDataLakeStoreSink** unterstützt die folgenden Eigenschaften im Abschnitt **typeProperties** :

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| **copyBehavior** |Gibt das Kopierverhalten an. |<b>PreserveHierarchy:</b> Behält die Dateihierarchie im Zielordner bei. Der relative Pfad der Quelldatei zum Quellordner entspricht dem relativen Pfad der Zieldatei zum Zielordner.<br/><br/><b>FlattenHierarchy:</b> Alle Dateien aus dem Quellordner werden auf der ersten Ebene des Zielordners erstellt. Die Namen der Zieldateien werden automatisch generiert.<br/><br/><b>MergeFiles:</b> Alle Dateien aus dem Quellordner werden in einer Datei zusammengeführt. Wenn der Datei- oder Blobname angegeben wurde, entspricht der Name der Zusammenführungsdatei dem angegebenen Namen. Andernfalls wird der Dateiname automatisch generiert. |Nein |

### <a name="recursive-and-copybehavior-examples"></a>Beispiele für "recursive" und "copyBehavior"
Dieser Abschnitt beschreibt das resultierende Verhalten des Kopiervorgangs für verschiedene Kombinationen von rekursiven und CopyBehavior-Werten.

| recursive | copyBehavior | Resultierendes Verhalten |
| --- | --- | --- |
| true |preserveHierarchy |Für den Quellordner „Ordner1“ mit der folgenden Struktur: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5<br/><br/>wird der Zielordner „Ordner1“ mit der gleichen Struktur erstellt wie die Quelle<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5 |
| true |flattenHierarchy |Für den Quellordner „Ordner1“ mit der folgenden Struktur: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5<br/><br/>wird der Zielordner „Ordner1“ mit der folgenden Struktur erstellt: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch generierter Name für Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch generierter Name für Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch generierter Name für Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch generierter Name für Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch generierter Name für Datei5 |
| true |mergeFiles |Für den Quellordner „Ordner1“ mit der folgenden Struktur: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5<br/><br/>wird der Zielordner „Ordner1“ mit der folgenden Struktur erstellt: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp; Inhalte von Datei1 + Datei2 + Datei3 + Datei4 + Datei5 werden in einer Datei mit einem automatisch generierten Namen zusammengeführt. |
| false |preserveHierarchy |Für den Quellordner „Ordner1“ mit der folgenden Struktur: <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5<br/><br/>wird der Zielordner „Ordner1“ mit der folgenden Struktur erstellt:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/><br/><br/>Unterordner1 mit Datei3, Datei4 und Datei5 wird nicht übernommen. |
| false |flattenHierarchy |Für den Quellordner „Ordner1“ mit der folgenden Struktur:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5<br/><br/>wird der Zielordner „Ordner1“ mit der folgenden Struktur erstellt:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch generierter Name für Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Automatisch generierter Name für Datei2<br/><br/><br/>Unterordner1 mit Datei3, Datei4 und Datei5 wird nicht übernommen. |
| false |mergeFiles |Für den Quellordner „Ordner1“ mit der folgenden Struktur:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5<br/><br/>wird der Zielordner „Ordner1“ mit der folgenden Struktur erstellt:<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Inhalte von Datei1 + Datei2 werden zu einer Datei mit einem automatisch generierten Namen zusammengeführt. Automatisch generierter Name für Datei1<br/><br/>Unterordner1 mit Datei3, Datei4 und Datei5 wird nicht übernommen. |

## <a name="supported-file-and-compression-formats"></a>Unterstützte Datei- und Komprimierungsformate
Ausführliche Informationen finden Sie im Artikel [Datei- und Komprimierungsformate in Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

## <a name="json-examples-for-copying-data-to-and-from-data-lake-store"></a>JSON-Beispiele zum Kopieren von Daten in bzw. aus Data Lake Store
Die folgenden Beispiele enthalten JSON-Beispieldefinitionen. Sie können diese Beispieldefinitionen zum Erstellen einer Pipeline mithilfe von [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) oder [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) verwenden. Die Beispiele verdeutlichen, wie Sie Daten in und aus Data Lake Store und Azure Blob Storage kopieren. Allerdings können Daten _direkt_ aus einer der Quellen in eine der unterstützten Senken kopiert werden. Weitere Informationen finden Sie im Abschnitt „Unterstützte Datenspeicher und Formate“ des Artikels [Verschieben von Daten mit der Kopieraktivität](data-factory-data-movement-activities.md).

### <a name="example-copy-data-from-azure-blob-storage-to-azure-data-lake-store"></a>Beispiel: Kopieren von Daten aus Azure Blob Storage nach Azure Data Lake Storage
Mit dem Beispielcode in diesem Abschnitt wird Folgendes veranschaulicht:

* Einen verknüpften Dienst des Typs [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Einen verknüpften Dienst des Typs [AzureDataLakeStore](#linked-service-properties)
* Ein [Eingabedataset](data-factory-create-datasets.md) des Typs [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* Ein [Ausgabedataset](data-factory-create-datasets.md) des Typs [AzureDataLakeStore](#dataset-properties)
* Eine [Pipeline](data-factory-create-pipelines.md) mit Kopieraktivität, die [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) und [AzureDataLakeStoreSink](#copy-activity-properties) verwendet

Die Beispiele zeigen, wie Zeitreihendaten aus Azure Blob Storage jede Stunde in die Data Lake Store-Instanz kopiert werden.

**Mit Azure Storage verknüpfter Dienst**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

**Mit Azure Data Lake-Speicher verknüpfter Dienst**

```JSON
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

> [!NOTE]
> Informationen zu den Konfigurationsdetails finden Sie im Abschnitt [Eigenschaften des verknüpften Diensts](#linked-service-properties).
>

**Azure-Blob-Eingabedataset**

Im folgenden Beispiel werden jede Stunde Daten eines neuen Blobs kopiert (`"frequency": "Hour", "interval": 1`). Ordnerpfad und Dateiname des Blobs werden basierend auf der Startzeit des Slices, der verarbeitet wird, dynamisch ausgewertet. Für den Ordnerpfad werden das Jahr, der Monat und der Tag der Startzeit verwendet. Im Dateinamen wird der Stundenteil der Startzeit verwendet. Mit der Einstellung `"external": true` wird dem Data Factory-Dienst mitgeteilt, dass diese Tabelle für die Data Factory extern ist und nicht durch eine Aktivität in der Data Factory erzeugt wird.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
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

**Azure Data Lake Store-Ausgabedataset**

Im folgenden Beispiel werden Daten in die Data Lake Store-Instanz kopiert. Jede Stunde werden neue Daten in die Data Lake Store-Instanz kopiert.

```JSON
{
    "name": "AzureDataLakeStoreOutput",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/output/"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Kopieraktivität in einer Pipeline mit einer Blobquelle und einer Data Lake Store-Senke**

Im folgenden Beispiel enthält die Pipeline eine Kopieraktivität, die für die Verwendung der Ein- und Ausgabedatasets konfiguriert ist. Für die Kopieraktivität ist die Ausführung einmal pro Stunde geplant. In der JSON-Definition der Pipeline ist der Typ `source` auf `BlobSource` und der Typ `sink` auf `AzureDataLakeStoreSink` festgelegt.

```json
{
    "name":"SamplePipeline",
    "properties":
    {
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":
        [
            {
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureDataLakeStoreOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "AzureDataLakeStoreSink"
                    }
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

### <a name="example-copy-data-from-azure-data-lake-store-to-an-azure-blob"></a>Beispiel: Kopieren von Daten aus Azure Data Lake Storage in ein Azure-Blob
Mit dem Beispielcode in diesem Abschnitt wird Folgendes veranschaulicht:

* Einen verknüpften Dienst des Typs [AzureDataLakeStore](#linked-service-properties)
* Einen verknüpften Dienst des Typs [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Ein [Eingabedataset](data-factory-create-datasets.md) des Typs [AzureDataLakeStore](#dataset-properties)
* Ein [Ausgabedataset](data-factory-create-datasets.md) des Typs [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* Eine [Pipeline](data-factory-create-pipelines.md) mit Kopieraktivität, die [AzureDataLakeStoreSource](#copy-activity-properties) und [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) verwendet

Im Code werden Zeitreihendaten jede Stunde aus Data Lake Store in ein Azure-Blob kopiert.

**Mit Azure Data Lake-Speicher verknüpfter Dienst**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        }
    }
}
```

> [!NOTE]
> Informationen zu den Konfigurationsdetails finden Sie im Abschnitt [Eigenschaften des verknüpften Diensts](#linked-service-properties).
>

**Mit Azure Storage verknüpfter Dienst**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Azure Data Lake-Eingabedataset**

Durch Festlegen von `"external"` auf `true` wird dem Data Factory-Dienst mitgeteilt, dass die Tabelle für die Data Factory extern ist und nicht durch eine Aktivität in der Data Factory erzeugt wird.

```json
{
    "name": "AzureDataLakeStoreInput",
    "properties":
    {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
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

Im folgenden Beispiel werden Daten jede Stunde in ein neues Blob geschrieben (`"frequency": "Hour", "interval": 1`). Der Ordnerpfad des Blobs wird basierend auf der Startzeit des Slices, der verarbeitet wird, dynamisch ausgewertet. Im Ordnerpfad werden die Teile Jahr, Monat, Tag und Stunde der Startzeit verwendet.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Eine Kopieraktivität in einer Pipeline mit einer Azure Data Lake Store-Quelle und einer Blobsenke**

Im folgenden Beispiel enthält die Pipeline eine Kopieraktivität, die für die Verwendung der Ein- und Ausgabedatasets konfiguriert ist. Für die Kopieraktivität ist die Ausführung einmal pro Stunde geplant. In der JSON-Definition der Pipeline ist der Typ `source` auf `AzureDataLakeStoreSource` und der Typ `sink` auf `BlobSink` festgelegt.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[
            {
                "name": "AzureDakeLaketoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureDataLakeStoreInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "AzureDataLakeStoreSource",
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

In der Definition der Kopieraktivität können Sie Spalten aus dem Quelldataset auch Spalten im Senkendataset zuordnen. Weitere Informationen finden Sie unter [Zuordnen von Datasetspalten in Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Leistung und Optimierung
Informationen zu den Faktoren, die sich auf die Leistung der Kopieraktivität auswirken, und zur damit verbundenen Optimierung finden Sie im Artikel [Handbuch zur Leistung und Optimierung der Kopieraktivität](data-factory-copy-activity-performance.md).