---
title: Kopieren von Daten aus Office 365 mithilfe von Azure Data Factory
description: Erfahren Sie, wie Daten aus Office 365 mithilfe einer Kopieraktivität in einer Azure Data Factory-Pipeline in unterstützte Senkendatenspeicher kopiert werden.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/20/2019
ms.author: jingwang
ms.openlocfilehash: 365896fec555340c3932192aa82086d140d4db0c
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92632990"
---
# <a name="copy-data-from-office-365-into-azure-using-azure-data-factory"></a>Kopieren von Daten aus Office 365 mithilfe von Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Dank der Integration von Azure Data Factory und [Microsoft Graph Data Connect](/graph/data-connect-concept-overview) können Sie die umfangreichen Unternehmensdaten in Ihrem Office 365-Mandanten auf skalierbare Weise in Azure überführen und auf Grundlage dieser wertvollen Datenbestände Analyseanwendungen erstellen und Erkenntnisse extrahieren. Die Integration in Privileged Access Management bietet sichere Zugriffssteuerung für die wertvollen zusammengestellten Daten in Office 365.  Über [diesen Link](/graph/data-connect-concept-overview) erhalten Sie eine Übersicht über Microsoft Graph Data Connect und über [diesen Link](/graph/data-connect-policies#licensing) Informationen zur Lizenzierung.

In diesem Artikel wird beschrieben, wie Sie die Kopieraktivität in Azure Data Factory verwenden, um Daten aus Office 365 zu kopieren. Er baut auf dem Artikel zur [Übersicht über die Kopieraktivität](copy-activity-overview.md) auf, der eine allgemeine Übersicht über die Kopieraktivität enthält.

## <a name="supported-capabilities"></a>Unterstützte Funktionen
Der ADF Office 365-Connector und Microsoft Graph Data Connect ermöglichen die umfangreiche Erfassung verschiedener Datasettypen von Exchange-E-Mail-fähigen Postfächern, einschließlich Adressbuchkontakte, Kalenderereignisse, E-Mails, Benutzerinformationen, Postfacheinstellungen usw.  [Hier](/graph/data-connect-datasets) finden Sie die vollständige Liste verfügbarer Datasets.

Vorerst können Sie in einer einzelnen Kopieraktivität **Daten aus Office 365 nur im JSON-Format (setOfObjects-Typ) in [Azure Blob Storage](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) und [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)** kopieren. Wenn Sie Office 365-Daten in andere Typen von Datenspeichern oder in anderen Formaten laden möchten, können Sie die erste Kopieraktivität mit einer nachfolgenden Kopieraktivität verketten, um Daten in einen der [unterstützten ADF-Zielspeicher](copy-activity-overview.md#supported-data-stores-and-formats) zu laden (siehe Spalte „Als Senke unterstützt“ in der Tabelle „Unterstützte Datenspeicher und Formate“).

>[!IMPORTANT]
>- Das Azure-Abonnement mit der Data Factory und der Senkendatenspeicher müssen sich im gleichen Azure Active Directory-Mandanten (Azure AD) befinden wie der Office 365-Mandant.
>- Stellen Sie sicher, dass die für die Kopieraktivität verwendete Azure Integration Runtime-Region und das Ziel in der gleichen Region sind, in der sich das Postfach der Benutzer des Office 365-Mandanten befindet. [Hier](concepts-integration-runtime.md#integration-runtime-location) erfahren Sie, wie der Azure Integration Runtime-Standort bestimmt wird. In [dieser Tabelle](/graph/data-connect-datasets#regions) finden Sie die Liste der unterstützten Office-Regionen und die entsprechenden Azure-Regionen.
>- Die Dienstprinzipalauthentifizierung ist der einzige Authentifizierungsmechanismus, der für Azure Blob Storage, Azure Data Lake Storage Gen1 und Azure Data Lake Storage Gen2 als Zielspeicher unterstützt wird.

## <a name="prerequisites"></a>Voraussetzungen

Zum Kopieren von Daten aus Office 365 in Azure müssen Sie die folgenden Schritte ausführen:

- Ihr Office 365-Mandantenadministrator muss Onboardingaktionen ausführen, wie [hier](/graph/data-connect-get-started) beschrieben.
- Erstellen und konfigurieren Sie eine Azure AD-Webanwendung in Azure Active Directory.  Anweisungen finden Sie unter [Erstellen einer Azure AD-Anwendung](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal).
- Notieren Sie sich die folgenden Werte, die Sie zum Definieren des verknüpften Diensts für Office 365 verwenden:
    - Mandanten-ID. Anweisungen finden Sie unter [Abrufen der Mandanten-ID](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).
    - Anwendungs-ID und Authentifizierungsschlüssel.  Anweisungen finden Sie unter [Abrufen der Anwendungs-ID und des Authentifizierungsschlüssels](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).
- Fügen Sie die Identität des Benutzers, der die Datenzugriffsanforderung erstellt, als Besitzer der Azure AD-Webanwendung hinzu (wählen Sie in der Azure AD-Webanwendung „Einstellungen > Besitzer > Besitzer hinzufügen“ aus). 
    - Die Benutzeridentität muss sich in der Office 365-Organisation befinden, von der Sie Daten erhalten, und darf kein Gastbenutzer sein.

## <a name="approving-new-data-access-requests"></a>Genehmigen neuer Datenzugriffsanforderungen

Wenn Sie erstmals Daten für diesen Kontext anfordern (also für eine Kombination daraus, auf welche Datentabelle zugegriffen wird, in welches Zielkonto die Daten geladen werden und welche Benutzeridentität die Datenzugriffsanforderung sendet), wird als Status der Kopieraktivität „In Bearbeitung“ angezeigt. Nur wenn Sie unter [„Aktionen“ auf den Link „Details“](copy-activity-overview.md#monitoring) klicken, wird als Status „RequestingConsent“ angezeigt.  Ein Mitglied aus der Gruppe der Datenzugriffsgenehmiger muss die Anforderung in Privileged Access Management genehmigen, bevor die Datenextraktion fortgesetzt werden kann.

[Hier](/graph/data-connect-tips#approve-pam-requests-via-office-365-admin-portal) erfahren Sie, wie die genehmigende Person die Datenzugriffsanforderung genehmigen kann, und [hier](/graph/data-connect-pam) finden Sie eine Erläuterung der allgemeinen Integration in Privileged Access Management, einschließlich Informationen zum Einrichten der Gruppe von genehmigenden Personen für den Datenzugriff.

## <a name="policy-validation"></a>Richtlinienüberprüfung

Wenn ADF als Teil einer verwalteten App erstellt wird und Azure-Richtlinienzuweisungen zu Ressourcen innerhalb der Verwaltungsressourcengruppe vorgenommen werden, prüft ADF für jede Ausführung der Kopieraktivität, ob die Richtlinienzuweisungen durchgesetzt werden. [Hier](/graph/data-connect-policies#policies) finden Sie eine Liste der unterstützten Richtlinien.

## <a name="getting-started"></a>Erste Schritte

>[!TIP]
>Eine exemplarische Vorgehensweise zur Verwendung des Office 365-Connectors finden Sie im Artikel [Laden von Daten aus Office 365](load-office-365-data.md).

Sie können mithilfe eines der folgenden Tools oder SDKs eine Pipeline mit der Copy-Aktivität erstellen. Klicken Sie auf einen Link, um ein Tutorial mit Schritt-für-Schritt-Anweisungen zum Erstellen einer Pipeline mit einer Copy-Aktivität aufzurufen. 

- [Azure portal](quickstart-create-data-factory-portal.md)
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
| type | Die type-Eigenschaft muss auf Folgendes festgelegt werden: **Office365** | Ja |
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
| type | Die type-Eigenschaft des Datasets muss auf folgenden Wert festgelegt werden: **Office365Table** | Ja |
| tableName | Der Name des Datasets, das aus Office 365 extrahiert werden soll. [Hier](/graph/data-connect-datasets#datasets) finden Sie die Liste der Office 365-Datasets, die für zum Extrahieren verfügbar sind. | Ja |

Wenn Sie `dateFilterColumn`, `startTime`, `endTime` und `userScopeFilterUri` im Dataset festgelegt haben, wird es weiterhin unverändert unterstützt. Es wird jedoch empfohlen, zukünftig das neue Modell in der Aktivitätsquelle zu verwenden.

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
        "schema": [],
        "typeProperties": {
            "tableName": "BasicDataSet_v0.Event_v1"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Pipelines](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der Office 365-Quelle unterstützt werden.

### <a name="office-365-as-source"></a>Office 365 als Quelle

Beim Kopieren von Daten aus Office 365 werden die folgenden Eigenschaften im Abschnitt **source** der Copy-Aktivität unterstützt:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die type-Eigenschaft der Quelle der Kopieraktivität muss auf Folgendes festgelegt werden: **Office365Source** | Ja |
| allowedGroups | Gruppenauswahlprädikat.  Verwenden Sie diese Eigenschaft, um bis zu 10 Benutzergruppen auszuwählen, für die die Daten abgerufen werden sollen.  Wenn keine Gruppen angegeben sind, werden Daten für die gesamte Organisation zurückgegeben. | Nein |
| userScopeFilterUri | Wenn die Eigenschaft `allowedGroups` nicht angegeben ist, können Sie einen Prädikatsausdruck verwenden, der auf den gesamten Mandanten angewendet wird, um die spezifischen Zeilen zu filtern, die aus Office 365 extrahiert werden sollen. Das Prädikatsformat sollte dem Abfrageformat von Microsoft Graph-APIs entsprechen, z.B. `https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'`. | Nein |
| dateFilterColumn | Name der Filterspalte „DateTime“. Verwenden Sie diese Eigenschaft, um den Zeitraum zu begrenzen, für den Office 365-Daten extrahiert werden. | Ja, wenn das Dataset mindestens eine DateTime-Spalte enthält. Eine Liste der Datasets, die diesen DateTime-Filter benötigen, finden Sie [hier](/graph/data-connect-filtering#filtering). |
| startTime | Start-DateTime-Value, nach dem gefiltert werden soll. | Ja, wenn `dateFilterColumn` angegeben ist. |
| endTime | End-DateTime-Value, nach dem gefiltert werden soll. | Ja, wenn `dateFilterColumn` angegeben ist. |
| outputColumns | Array der Spalten, die in die Senke kopiert werden sollen | Nein |

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
                "type": "Office365Source",
                "dateFilterColumn": "CreatedDateTime",
                "startTime": "2019-04-28T16:00:00.000Z",
                "endTime": "2019-05-05T16:00:00.000Z",
                "userScopeFilterUri": "https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'",
                "outputColumns": [
                    {
                        "name": "Id"
                    },
                    {
                        "name": "CreatedDateTime"
                    },
                    {
                        "name": "LastModifiedDateTime"
                    },
                    {
                        "name": "ChangeKey"
                    },
                    {
                        "name": "Categories"
                    },
                    {
                        "name": "OriginalStartTimeZone"
                    },
                    {
                        "name": "OriginalEndTimeZone"
                    },
                    {
                        "name": "ResponseStatus"
                    },
                    {
                        "name": "iCalUId"
                    },
                    {
                        "name": "ReminderMinutesBeforeStart"
                    },
                    {
                        "name": "IsReminderOn"
                    },
                    {
                        "name": "HasAttachments"
                    },
                    {
                        "name": "Subject"
                    },
                    {
                        "name": "Body"
                    },
                    {
                        "name": "Importance"
                    },
                    {
                        "name": "Sensitivity"
                    },
                    {
                        "name": "Start"
                    },
                    {
                        "name": "End"
                    },
                    {
                        "name": "Location"
                    },
                    {
                        "name": "IsAllDay"
                    },
                    {
                        "name": "IsCancelled"
                    },
                    {
                        "name": "IsOrganizer"
                    },
                    {
                        "name": "Recurrence"
                    },
                    {
                        "name": "ResponseRequested"
                    },
                    {
                        "name": "ShowAs"
                    },
                    {
                        "name": "Type"
                    },
                    {
                        "name": "Attendees"
                    },
                    {
                        "name": "Organizer"
                    },
                    {
                        "name": "WebLink"
                    },
                    {
                        "name": "Attachments"
                    },
                    {
                        "name": "BodyPreview"
                    },
                    {
                        "name": "Locations"
                    },
                    {
                        "name": "OnlineMeetingUrl"
                    },
                    {
                        "name": "OriginalStart"
                    },
                    {
                        "name": "SeriesMasterId"
                    }
                ]
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