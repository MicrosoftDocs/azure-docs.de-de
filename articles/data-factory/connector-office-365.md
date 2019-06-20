---
title: Kopieren von Daten aus Office 365 mithilfe von Azure Data Factory | Microsoft-Dokumentation
description: Erfahren Sie, wie Daten aus Office 365 mithilfe einer Kopieraktivität in einer Azure Data Factory-Pipeline in unterstützte Senkendatenspeicher kopiert werden.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: jingwang
ms.openlocfilehash: 1a8d622aa280794d9a4d6fe7320ddcc21ac044f4
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475663"
---
# <a name="copy-data-from-office-365-into-azure-using-azure-data-factory"></a>Kopieren von Daten aus Office 365 mithilfe von Azure Data Factory

Dank der Integration von Azure Data Factory und [Microsoft Graph Data Connect](https://docs.microsoft.com/graph/data-connect-concept-overview) können Sie die umfangreichen Unternehmensdaten in Ihrem Office 365-Mandanten auf skalierbare Weise in Azure überführen und auf Grundlage dieser wertvollen Datenbestände Analyseanwendungen erstellen und Erkenntnisse extrahieren. Die Integration in Privileged Access Management bietet sichere Zugriffssteuerung für die wertvollen zusammengestellten Daten in Office 365.  Über [diesen Link](https://docs.microsoft.com/graph/data-connect-concept-overview) erhalten Sie eine Übersicht über Microsoft Graph Data Connect und über [diesen Link](https://docs.microsoft.com/graph/data-connect-policies#licensing) Informationen zur Lizenzierung.

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten aus Office 365 zu kopieren. Er baut auf dem Artikel zur [Übersicht über die Kopieraktivität](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

## <a name="supported-capabilities"></a>Unterstützte Funktionen
Der ADF Office 365-Connector und Microsoft Graph Data Connect ermöglichen die umfangreiche Erfassung verschiedener Datasettypen von Exchange-E-Mail-fähigen Postfächern, einschließlich Adressbuchkontakte, Kalenderereignisse, E-Mails, Benutzerinformationen, Postfacheinstellungen usw.  [Hier](https://docs.microsoft.com/graph/data-connect-datasets) finden Sie die vollständige Liste verfügbarer Datasets.

Vorerst können Sie in einer einzelnen Kopieraktivität **Daten aus Office 365 nur im JSON-Format (setOfObjects-Typ) in [Azure Blob Storage](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) und [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)** kopieren. Wenn Sie Office 365-Daten in andere Typen von Datenspeichern oder in anderen Formaten laden möchten, können Sie die erste Kopieraktivität mit einer nachfolgenden Kopieraktivität verketten, um Daten in einen der [unterstützten ADF-Zielspeicher](copy-activity-overview.md#supported-data-stores-and-formats) zu laden (siehe Spalte „Als Senke unterstützt“ in der Tabelle „Unterstützte Datenspeicher und Formate“).

>[!IMPORTANT]
>- Das Azure-Abonnement mit der Data Factory und der Senkendatenspeicher müssen sich im gleichen Azure Active Directory-Mandanten (Azure AD) befinden wie der Office 365-Mandant.
>- Stellen Sie sicher, dass die für die Kopieraktivität verwendete Azure Integration Runtime-Region und das Ziel in der gleichen Region sind, in der sich das Postfach der Benutzer des Office 365-Mandanten befindet. [Hier](concepts-integration-runtime.md#integration-runtime-location) erfahren Sie, wie der Azure Integration Runtime-Standort bestimmt wird. In [dieser Tabelle](https://docs.microsoft.com/graph/data-connect-datasets#regions) finden Sie die Liste der unterstützten Office-Regionen und die entsprechenden Azure-Regionen.
>- Die Dienstprinzipalauthentifizierung ist der einzige Authentifizierungsmechanismus, der für Azure Blob Storage, Azure Data Lake Storage Gen1 und Azure Data Lake Storage Gen2 als Zielspeicher unterstützt wird.

## <a name="prerequisites"></a>Voraussetzungen

Zum Kopieren von Daten aus Office 365 in Azure müssen Sie die folgenden Schritte ausführen:

- Ihr Office 365-Mandantenadministrator muss Onboardingaktionen ausführen, wie [hier](https://docs.microsoft.com/graph/data-connect-get-started) beschrieben.
- Erstellen und konfigurieren Sie eine Azure AD-Webanwendung in Azure Active Directory.  Anweisungen finden Sie unter [Erstellen einer Azure AD-Anwendung](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application).
- Notieren Sie sich die folgenden Werte, die Sie zum Definieren des verknüpften Diensts für Office 365 verwenden:
    - Mandanten-ID. Anweisungen finden Sie unter [Abrufen der Mandanten-ID](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
    - Anwendungs-ID und Authentifizierungsschlüssel.  Anweisungen finden Sie unter [Abrufen der Anwendungs-ID und des Authentifizierungsschlüssels](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
- Fügen Sie die Identität des Benutzers, der die Datenzugriffsanforderung erstellt, als Besitzer der Azure AD-Webanwendung hinzu (wählen Sie in der Azure AD-Webanwendung „Einstellungen > Besitzer > Besitzer hinzufügen“ aus). 
    - Die Benutzeridentität muss sich in der Office 365-Organisation befinden, von der Sie Daten erhalten, und darf kein Gastbenutzer sein.

## <a name="approving-new-data-access-requests"></a>Genehmigen neuer Datenzugriffsanforderungen

Wenn Sie erstmals Daten für diesen Kontext anfordern (also für eine Kombination daraus, auf welche Datentabelle zugegriffen wird, in welches Zielkonto die Daten geladen werden und welche Benutzeridentität die Datenzugriffsanforderung sendet), wird als Status der Kopieraktivität „In Bearbeitung“ angezeigt. Nur wenn Sie unter [„Aktionen“ auf den Link „Details“](copy-activity-overview.md#monitoring) klicken, wird als Status „RequestingConsent“ angezeigt.  Ein Mitglied aus der Gruppe der Datenzugriffsgenehmiger muss die Anforderung in Privileged Access Management genehmigen, bevor die Datenextraktion fortgesetzt werden kann.

[Hier](https://docs.microsoft.com/graph/data-connect-tips#approve-pam-requests-via-office-365-admin-portal) erfahren Sie, wie die genehmigende Person die Datenzugriffsanforderung genehmigen kann, und [hier](https://docs.microsoft.com/graph/data-connect-pam) finden Sie eine Erläuterung der allgemeinen Integration in Privileged Access Management, einschließlich Informationen zum Einrichten der Gruppe von genehmigenden Personen für den Datenzugriff.

## <a name="policy-validation"></a>Richtlinienüberprüfung

Wenn ADF als Teil einer verwalteten App erstellt wird und Azure-Richtlinienzuweisungen zu Ressourcen innerhalb der Verwaltungsressourcengruppe vorgenommen werden, prüft ADF für jede Ausführung der Kopieraktivität, ob die Richtlinienzuweisungen durchgesetzt werden. [Hier](https://docs.microsoft.com/graph/data-connect-policies#policies) finden Sie eine Liste der unterstützten Richtlinien.

## <a name="getting-started"></a>Erste Schritte

>[!TIP]
>Eine exemplarische Vorgehensweise zur Verwendung des Office 365-Connectors finden Sie im Artikel [Laden von Daten aus Office 365](load-office-365-data.md).

Sie können mithilfe eines der folgenden Tools oder SDKs eine Pipeline mit der Copy-Aktivität erstellen. Klicken Sie auf einen Link, um ein Tutorial mit Schritt-für-Schritt-Anweisungen zum Erstellen einer Pipeline mit einer Copy-Aktivität aufzurufen. 

- [Azure-Portal](quickstart-create-data-factory-portal.md)
- [.NET SDK](quickstart-create-data-factory-dot-net.md)
- [Python SDK](quickstart-create-data-factory-python.md)
- [Azure PowerShell](quickstart-create-data-factory-powershell.md)
- [REST-API](quickstart-create-data-factory-rest-api.md)
- [Azure Resource Manager-Vorlage](quickstart-create-data-factory-resource-manager-template.md). 

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für den Office 365-Connector verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den mit Office 365 verknüpften Dienst unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft muss auf Folgendes festgelegt werden: **Office 365** | Ja |
| office365TenantId | Die Azure-Mandanten-ID, zu der das Office 365-Konto gehört. | Ja |
| servicePrincipalTenantId | Geben Sie die Mandanteninformationen Ihrer Azure AD-Webanwendung an. | Ja |
| servicePrincipalId | Geben Sie die Client-ID der Anwendung an. | Ja |
| servicePrincipalKey | Geben Sie den Schlüssel der Anwendung an. Markieren Sie dieses Feld als „SecureString“, um es sicher in Data Factory zu speichern. | Ja |
| connectVia | Die Integration Runtime, die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden soll.  Wenn keine Option angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. | Nein |

>[!NOTE]
> Der Unterschied zwischen **office365TenantId** und **servicePrincipalTenantId** und der entsprechende anzugebende Wert:
>- Wenn Sie ein Unternehmensentwickler sind und eine Anwendung für Office 365-Daten für die Nutzung in Ihrer eigenen Organisation entwickeln, sollten Sie die gleiche Mandanten-ID für beide Eigenschaften angeben, und zwar die AAD-Mandanten-ID Ihrer Organisation.
>- Wenn Sie ein ISV-Entwickler sind und eine Anwendung für Ihre Kunden entwickeln, dann ist „office365TenantId“ die AAD-Mandanten-ID Ihres Kunden (der die Anwendung installiert), und „servicePrincipalTenantId“ ist die AAD-Mandanten-ID Ihres Unternehmens.

**Beispiel:**

```json
{
    "name": "Office365LinkedService",
    "properties": {
        "type": "Office365",
        "typeProperties": {
            "office365TenantId": "<Office 365 tenant id>",
            "servicePrincipalTenantId": "<AAD app service principal tenant id>",
            "servicePrincipalId": "<AAD app service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<AAD app service principal key>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset-Eigenschaften

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel zu [Datasets](concepts-datasets-linked-services.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die vom Office 365-Dataset unterstützt werden.

Zum Kopieren von Daten aus Office 365 werden die folgenden Eigenschaften unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft des Datasets muss auf Folgendes festgelegt werden: **Office365Table** | Ja |
| tableName | Der Name des Datasets, das aus Office 365 extrahiert werden soll. [Hier](https://docs.microsoft.com/graph/data-connect-datasets#datasets) finden Sie die Liste der Office 365-Datasets, die für zum Extrahieren verfügbar sind. | Ja |
| allowedGroups | Gruppenauswahlprädikat.  Verwenden Sie diese Eigenschaft, um bis zu 10 Benutzergruppen auszuwählen, für die die Daten abgerufen werden sollen.  Wenn keine Gruppen angegeben sind, werden Daten für die gesamte Organisation zurückgegeben. | Nein |
| userScopeFilterUri | Wenn die Eigenschaft `allowedGroups` nicht angegeben ist, können Sie einen Prädikatsausdruck verwenden, der auf den gesamten Mandanten angewendet wird, um die spezifischen Zeilen zu filtern, die aus Office 365 extrahiert werden sollen. Das Prädikatsformat sollte dem Abfrageformat von Microsoft Graph-APIs entsprechen, z.B. `https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'`. | Nein |
| dateFilterColumn | Name der Filterspalte „DateTime“. Verwenden Sie diese Eigenschaft, um den Zeitraum zu begrenzen, für den Office 365-Daten extrahiert werden. | Ja, wenn das Dataset mindestens eine DateTime-Spalte enthält. Eine Liste der Datasets, die diesen DateTime-Filter benötigen, finden Sie [hier](https://docs.microsoft.com/graph/data-connect-filtering#filtering). |
| startTime | Start-DateTime-Value, nach dem gefiltert werden soll. | Ja, wenn `dateFilterColumn` angegeben ist. |
| endTime | End-DateTime-Value, nach dem gefiltert werden soll. | Ja, wenn `dateFilterColumn` angegeben ist. |

**Beispiel**

```json
{
    "name": "DS_May2019_O365_Message",
    "properties": {
        "type": "Office365Table",
        "linkedServiceName": {
            "referenceName": "<Office 365 linked service name>",
            "type": "LinkedServiceReference"
        },
        "structure": [
            {
                "name": "Id",
                "type": "String",
                "description": "The unique identifier of the event."
            },
            {
                "name": "CreatedDateTime",
                "type": "DateTime",
                "description": "The date and time that the event was created."
            },
            {
                "name": "LastModifiedDateTime",
                "type": "DateTime",
                "description": "The date and time that the event was last modified."
            },
            {
                "name": "ChangeKey",
                "type": "String",
                "description": "Identifies the version of the event object. Every time the event is changed, ChangeKey changes as well. This allows Exchange to apply changes to the correct version of the object."
            },
            {
                "name": "Categories",
                "type": "String",
                "description": "The categories associated with the event. Format: ARRAY<STRING>"
            },
            {
                "name": "OriginalStartTimeZone",
                "type": "String",
                "description": "The start time zone that was set when the event was created. See DateTimeTimeZone for a list of valid time zones."
            },
            {
                "name": "OriginalEndTimeZone",
                "type": "String",
                "description": "The end time zone that was set when the event was created. See DateTimeTimeZone for a list of valid time zones."
            },
            {
                "name": "ResponseStatus",
                "type": "String",
                "description": "Indicates the type of response sent in response to an event message. Format: STRUCT<Response: STRING, Time: STRING>"
            },
            {
                "name": "iCalUId",
                "type": "String",
                "description": "A unique identifier that is shared by all instances of an event across different calendars."
            },
            {
                "name": "ReminderMinutesBeforeStart",
                "type": "Int32",
                "description": "The number of minutes before the event start time that the reminder alert occurs."
            },
            {
                "name": "IsReminderOn",
                "type": "Boolean",
                "description": "Set to true if an alert is set to remind the user of the event."
            },
            {
                "name": "HasAttachments",
                "type": "Boolean",
                "description": "Set to true if the event has attachments."
            },
            {
                "name": "Subject",
                "type": "String",
                "description": "The text of the event's subject line."
            },
            {
                "name": "Body",
                "type": "String",
                "description": "The body of the message associated with the event.Format: STRUCT<ContentType: STRING, Content: STRING>"
            },
            {
                "name": "Importance",
                "type": "String",
                "description": "The importance of the event: Low, Normal, High."
            },
            {
                "name": "Sensitivity",
                "type": "String",
                "description": "Indicates the level of privacy for the event: Normal, Personal, Private, Confidential."
            },
            {
                "name": "Start",
                "type": "String",
                "description": "The start time of the event. Format: STRUCT<DateTime: STRING, TimeZone: STRING>"
            },
            {
                "name": "End",
                "type": "String",
                "description": "The date and time that the event ends. Format: STRUCT<DateTime: STRING, TimeZone: STRING>"
            },
            {
                "name": "Location",
                "type": "String",
                "description": "Location information of the event. Format: STRUCT<DisplayName: STRING, Address: STRUCT<Street: STRING, City: STRING, State: STRING, CountryOrRegion: STRING, PostalCode: STRING>, Coordinates: STRUCT<Altitude: DOUBLE, Latitude: DOUBLE, Longitude: DOUBLE, Accuracy: DOUBLE, AltitudeAccuracy: DOUBLE>>"
            },
            {
                "name": "IsAllDay",
                "type": "Boolean",
                "description": "Set to true if the event lasts all day. Adjusting this property requires adjusting the Start and End properties of the event as well."
            },
            {
                "name": "IsCancelled",
                "type": "Boolean",
                "description": "Set to true if the event has been canceled."
            },
            {
                "name": "IsOrganizer",
                "type": "Boolean",
                "description": "Set to true if the message sender is also the organizer."
            },
            {
                "name": "Recurrence",
                "type": "String",
                "description": "The recurrence pattern for the event. Format: STRUCT<Pattern: STRUCT<Type: STRING, `Interval`: INT, Month: INT, DayOfMonth: INT, DaysOfWeek: ARRAY<STRING>, FirstDayOfWeek: STRING, Index: STRING>, `Range`: STRUCT<Type: STRING, StartDate: STRING, EndDate: STRING, RecurrenceTimeZone: STRING, NumberOfOccurrences: INT>>"
            },
            {
                "name": "ResponseRequested",
                "type": "Boolean",
                "description": "Set to true if the sender would like a response when the event is accepted or declined."
            },
            {
                "name": "ShowAs",
                "type": "String",
                "description": "The status to show: Free, Tentative, Busy, Oof, WorkingElsewhere, Unknown."
            },
            {
                "name": "Type",
                "type": "String",
                "description": "The event type: SingleInstance, Occurrence, Exception, SeriesMaster."
            },
            {
                "name": "Attendees",
                "type": "String",
                "description": "The collection of attendees for the event. Format: ARRAY<STRUCT<EmailAddress: STRUCT<Name: STRING, Address: STRING>, Status: STRUCT<Response: STRING, Time: STRING>, Type: STRING>>"
            },
            {
                "name": "Organizer",
                "type": "String",
                "description": "The organizer of the event. Format: STRUCT<EmailAddress: STRUCT<Name: STRING, Address: STRING>>"
            },
            {
                "name": "WebLink",
                "type": "String",
                "description": "The URL to open the event in Outlook Web App."
            },
            {
                "name": "Attachments",
                "type": "String",
                "description": "The FileAttachment and ItemAttachment attachments for the message. Navigation property. Format: ARRAY<STRUCT<LastModifiedDateTime: STRING, Name: STRING, ContentType: STRING, Size: INT, IsInline: BOOLEAN, Id: STRING>>"
            },
            {
                "name": "BodyPreview",
                "type": "String",
                "description": "The preview of the message associated with the event. It is in text format."
            },
            {
                "name": "Locations",
                "type": "String",
                "description": "The locations where the event is held or attended from. The location and locations properties always correspond with each other. Format:  ARRAY<STRUCT<DisplayName: STRING, Address: STRUCT<Street: STRING, City: STRING, State: STRING, CountryOrRegion: STRING, PostalCode: STRING>, Coordinates: STRUCT<Altitude: DOUBLE, Latitude: DOUBLE, Longitude: DOUBLE, Accuracy: DOUBLE, AltitudeAccuracy: DOUBLE>, LocationEmailAddress: STRING, LocationUri: STRING, LocationType: STRING, UniqueId: STRING, UniqueIdType: STRING>>"
            },
            {
                "name": "OnlineMeetingUrl",
                "type": "String",
                "description": "A URL for an online meeting. The property is set only when an organizer specifies an event as an online meeting such as a Skype meeting"
            },
            {
                "name": "OriginalStart",
                "type": "DateTime",
                "description": "The start time that was set when the event was created in UTC time."
            },
            {
                "name": "SeriesMasterId",
                "type": "String",
                "description": "The ID for the recurring series master item, if this event is part of a recurring series."
            }
        ],
        "typeProperties": {
            "tableName": "BasicDataSet_v0.Event_v1",
            "dateFilterColumn": "CreatedDateTime",
            "startTime": "2019-04-28T16:00:00.000Z",
            "endTime": "2019-05-05T16:00:00.000Z",
            "userScopeFilterUri": "https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der Office 365-Quelle unterstützt werden.

### <a name="office-365-as-source"></a>Office 365 als Quelle

Legen Sie zum Kopieren von Daten aus Office 365 den Quelltyp in der Kopieraktivität auf **Office365Source** fest. Keine weiteren Eigenschaften werden im Abschnitt **source** der Kopieraktivität unterstützt.

**Beispiel:**

```json
"activities": [
    {
        "name": "CopyFromO365ToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Office 365 input dataset name>",
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
                "type": "Office365Source"
            },
            "sink": {
                "type": "BlobSink"
            }
        }
    }
]
```

## <a name="next-steps"></a>Nächste Schritte
Eine Liste der Datenspeicher, die als Quellen und Senken für die Kopieraktivität in Azure Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](copy-activity-overview.md#supported-data-stores-and-formats).
