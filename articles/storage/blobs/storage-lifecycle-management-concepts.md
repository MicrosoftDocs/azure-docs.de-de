---
title: Optimieren der Kosten durch Automatisieren der Azure Blob Storage-Zugriffsebenen
description: Erstellen Sie automatisierte Regeln zum Verschieben von Daten zwischen den Ebenen „heiß“, „kalt“ und „Archiv“.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 10/29/2020
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: yzheng
ms.custom: devx-track-azurepowershell, references_regions
ms.openlocfilehash: 1b568687ffe646a91544c1bb75d26d552a23f49c
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "96005281"
---
# <a name="optimize-costs-by-automating-azure-blob-storage-access-tiers"></a>Optimieren der Kosten durch Automatisieren der Azure Blob Storage-Zugriffsebenen

Datasets haben eindeutige Lebenszyklen. Früh im Lebenszyklus greifen Benutzer häufig auf einige Daten zu. Aber der Zugriffsbedarf sinkt mit zunehmendem Alter der Daten drastisch. Außerdem gibt es Daten, die in der Cloud lediglich vorgehalten werden und auf die nach der Speicherung nur selten zugegriffen wird. Einige Daten laufen Tage oder Monate nach der Erstellung ab, während andere Daten im Verlauf ihres gesamten Lebens aktiv gelesen und geändert werden. Die Azure Blob Storage-Lebenszyklusverwaltung verfügt über eine umfassende, regelbasierte Richtlinie für GPv2- und Blob Storage-Konten. Verwenden Sie die Richtlinie, um Ihre Daten in die entsprechenden Zugriffsebenen zu übertragen oder am Ende des Lebenszyklus der Daten ablaufen zu lassen.

Mit der Richtlinie für die Lebenszyklusverwaltung können Sie die folgenden Aufgaben ausführen:

- Sofortige Übertragung von kalten zu heißen Blobs, wenn zur Leistungsoptimierung darauf zugegriffen wird 
- Übertragung von Blobs, Blobversionen und Blobmomentaufnahmen aus Kostengründen auf eine kältere Speicherebene (heiß zu kalt, heiß zu Archiv oder kalt zu Archiv), wenn für einen bestimmten Zeitraum nicht darauf zugegriffen wurde bzw. sie nicht geändert wurden
- Löschen von Blobs, Blobversionen und Blobmomentaufnahmen am Ende ihrer Lebenszyklen
- Definieren von Regeln, die ein Mal täglich auf Speicherkontoebene ausgeführt werden
- Anwenden von Regeln auf Container oder eine Teilmenge von Blobs (mit Namenspräfixen oder [Blobindextags](storage-manage-find-blobs.md) als Filter)

Stellen Sie sich ein Szenario vor, bei dem in den frühen Phasen des Lebenszyklus häufig auf Daten zugegriffen wird, nach zwei Wochen aber nur noch gelegentlich. Nach dem ersten Monat wird auf das Dataset nur noch selten zugegriffen. In diesem Szenario empfiehlt sich in den frühen Phasen heißer Speicher. Die kalte Speicherebene eignet sich am besten für den gelegentlichen Zugriff. Die Archivspeicherebene ist die beste Option, wenn die Daten mehr als einen Monat alt sind. Durch Anpassen der Speicherebenen im Hinblick auf das Alter der Daten können Sie die kostengünstigsten Speicheroptionen für Ihre Anforderungen entwerfen. Für diesen Übergang stehen Richtlinienregeln für die Lebenszyklusverwaltung zur Verfügung, um alternde Daten in kühlere Ebenen zu verschieben.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

>[!NOTE]
>Wenn Daten lesbar bleiben müssen, z. B. wenn sie von StorSimple verwendet werden, legen Sie keine Richtlinie fest, um Blobs in die Archivebene zu verschieben.

## <a name="availability-and-pricing"></a>Verfügbarkeit und Preismodell

Das Feature zur Lebenszyklusverwaltung ist in allen Azure-Regionen für GPv2-Konten (Universell v2), Blob Storage-Konten, Premium-Blockblob-Speicherkonten und Azure Data Lake Storage Gen2-Konten verfügbar. Für ein vorhandenes GPv1-Konto (Universell V1) kann in einem einfachen Prozess im Azure-Portal ein Upgrade auf ein GPv2-Konto erfolgen. Weitere Informationen zu Speicherkonten finden Sie unter [Azure-Speicherkonto – Übersicht](../common/storage-account-overview.md).

Die Funktion zur Lebenszyklusverwaltung ist kostenlos. Kunden werden die regulären Betriebskosten für die [Set Blob Tier](/rest/api/storageservices/set-blob-tier)-API-Aufrufe in Rechnung gestellt. Löschvorgänge sind kostenlos. Weitere Informationen zu den Preisen finden Sie unter [Preise für Blockblobs](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="add-or-remove-a-policy"></a>Hinzufügen oder Entfernen einer Richtlinie

Sie können eine Richtlinie hinzufügen, bearbeiten oder entfernen, indem Sie eine der folgenden Methoden verwenden:

* [Azure portal](https://portal.azure.com)
* [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)
* [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli)
* [REST-APIs](/rest/api/storagerp/managementpolicies)

Eine Richtlinie kann vollständig gelesen oder geschrieben werden. Teilaktualisierungen werden nicht unterstützt. 

> [!NOTE]
> Wenn Sie Firewallregeln für Ihr Speicherkonto aktivieren, werden Anforderungen für die Lebenszyklusverwaltung möglicherweise blockiert. Sie können die Sperre dieser Anforderungen durch Bereitstellen von Ausnahmen für vertrauenswürdige Microsoft-Dienste aufheben. Weitere Informationen finden Sie im Abschnitt „Ausnahmen“ unter [Konfigurieren von Firewalls und virtuellen Netzwerken](../common/storage-network-security.md#exceptions).

In diesem Artikel wird die Verwaltung einer Richtlinie über das Portal und über PowerShell erläutert.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Es gibt zwei Möglichkeiten zum Hinzufügen einer Richtlinie über das Azure-Portal. 

* [Listenansicht des Azure-Portals](#azure-portal-list-view)
* [Codeansicht des Azure-Portals](#azure-portal-code-view)

#### <a name="azure-portal-list-view"></a>Listenansicht des Azure-Portals

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Suchen Sie im Azure-Portal nach Ihrem Speicherkonto, und wählen Sie es aus. 

1. Wählen Sie unter **Blob-Dienst** die Option **Lebenszyklusverwaltung** aus, um Ihre Regeln anzuzeigen oder zu ändern.

1. Wählen Sie die Registerkarte **Listenansicht** aus.

1. Wählen Sie **Regel hinzufügen** aus, und geben Sie Ihrer Regel im Formular **Details** einen Namen. Darüber hinaus können Sie Werte für **Regelbereich**, **Blobtyp** und **Blobuntertyp** festlegen. Im folgenden Beispiel wird der Bereich zum Filtern von Blobs festgelegt. Daraufhin wird die Registerkarte **Filtersatz** angezeigt.

   :::image type="content" source="media/storage-lifecycle-management-concepts/lifecycle-management-details.png" alt-text="Seite mit Details zum Hinzufügen einer Regel in der Lebenszyklusverwaltung im Azure-Portal":::

1. Wählen Sie **Basisblobs** aus, um die Bedingungen für Ihre Regel festzulegen. Im nachstehenden Beispiel werden Blobs in den kalten Speicher verschoben, wenn sie während 30 Tagen nicht geändert wurden.

   :::image type="content" source="media/storage-lifecycle-management-concepts/lifecycle-management-base-blobs.png" alt-text="Seite mit Basisblobs in der Lebenszyklusverwaltung im Azure-Portal":::

   Die Option **Letzter Zugriff** ist in der Vorschau in den folgenden Regionen verfügbar:

    - Frankreich, Mitte
    - Kanada, Osten
    - Kanada, Mitte

   > [!IMPORTANT]
   > Die Vorschau der Zeitüberwachung für den letzten Zugriff ist nur zur Verwendung in Umgebungen außerhalb der Produktion bestimmt. Produktions-SLAs (Service Level Agreements, Vereinbarungen zum Servicelevel) sind derzeit nicht verfügbar.
   
   Wenn Sie die Option **Letzter Zugriff** verwenden möchten, wählen Sie im Azure-Portal auf der Seite **Lebenszyklusverwaltung** die Option **Access tracking enabled** (Zugriffsüberwachung aktiviert) aus. Weitere Informationen zur Option **Letzter Zugriff** finden Sie unter [Verschieben von Daten basierend auf dem Datum des letzten Zugriffs (Vorschau)](#move-data-based-on-last-accessed-date-preview).

1. Wenn Sie auf der Seite **Details** die Option **Limit blobs with filters** (Blobs mit Filtern einschränken) ausgewählt haben, wählen Sie **Filtersatz** aus, um einen optionalen Filter hinzuzufügen. Im folgenden Beispiel werden im Container *mylifecyclecontainer* Blobs gefiltert, die mit „log“ beginnen.

   :::image type="content" source="media/storage-lifecycle-management-concepts/lifecycle-management-filter-set.png" alt-text="Seite mit dem Filtersatz für Lebenszyklusverwaltung im Azure-Portal":::

1. Wählen Sie **Hinzufügen** aus, um die neue Richtlinie hinzuzufügen.

#### <a name="azure-portal-code-view"></a>Codeansicht des Azure-Portals
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Suchen Sie im Azure-Portal nach Ihrem Speicherkonto, und wählen Sie es aus.

1. Wählen Sie unter **Blob-Dienst** die Option **Lebenszyklusverwaltung** aus, um Ihre Richtlinie anzuzeigen oder zu ändern.

1. Der folgende JSON-Code ist ein Beispiel für eine Richtlinie, die in die Registerkarte **Codeansicht** eingefügt werden kann.

   ```json
   {
     "rules": [
       {
         "enabled": true,
         "name": "move-to-cool",
         "type": "Lifecycle",
         "definition": {
           "actions": {
             "baseBlob": {
               "tierToCool": {
                 "daysAfterModificationGreaterThan": 30
               }
             }
           },
           "filters": {
             "blobTypes": [
               "blockBlob"
             ],
             "prefixMatch": [
               "mylifecyclecontainer/log"
             ]
           }
         }
       }
     ]
   }
   ```

1. Wählen Sie **Speichern** aus.

1. Weitere Informationen zu diesem JSON-Beispiel finden Sie in den Abschnitten [Richtlinie](#policy) und [Regeln](#rules).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Das folgende PowerShell-Skript kann verwendet werden, um Ihrem Speicherkonto eine Richtlinie hinzuzufügen. Die Variable `$rgname` muss mit Ihrem Ressourcengruppennamen initialisiert werden. Die Variable `$accountName` muss mit Ihrem Speicherkontonamen initialisiert werden.

```powershell
#Install the latest module
Install-Module -Name Az -Repository PSGallery

#Initialize the following with your resource group and storage account names
$rgname = ""
$accountName = ""

#Create a new action object
$action = Add-AzStorageAccountManagementPolicyAction -BaseBlobAction Delete -daysAfterModificationGreaterThan 2555
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -BaseBlobAction TierToArchive -daysAfterModificationGreaterThan 90
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -BaseBlobAction TierToCool -daysAfterModificationGreaterThan 30
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -SnapshotAction Delete -daysAfterCreationGreaterThan 90

# Create a new filter object
# PowerShell automatically sets BlobType as “blockblob” because it is the only available option currently
$filter = New-AzStorageAccountManagementPolicyFilter -PrefixMatch ab,cd

#Create a new rule object
#PowerShell automatically sets Type as “Lifecycle” because it is the only available option currently
$rule1 = New-AzStorageAccountManagementPolicyRule -Name Test -Action $action -Filter $filter

#Set the policy
Set-AzStorageAccountManagementPolicy -ResourceGroupName $rgname -StorageAccountName $accountName -Rule $rule1
```

# <a name="template"></a>[Vorlage](#tab/template)

Sie können die Lebenszyklusverwaltung definieren, indem Sie Azure Resource Manager-Vorlagen verwenden. Hier ist eine Beispielvorlage für das Bereitstellen eines GPv2-Speicherkontos mit RA-GRS mit einer Richtlinie für die Lebenszyklusverwaltung angegeben.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "storageAccountName": "[uniqueString(resourceGroup().id)]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "apiVersion": "2019-04-01",
      "sku": {
        "name": "Standard_RAGRS"
      },
      "kind": "StorageV2",
      "properties": {
        "networkAcls": {}
      }
    },
    {
      "name": "[concat(variables('storageAccountName'), '/default')]",
      "type": "Microsoft.Storage/storageAccounts/managementPolicies",
      "apiVersion": "2019-04-01",
      "dependsOn": [
        "[variables('storageAccountName')]"
      ],
      "properties": {
        "policy": {...}
      }
    }
  ],
  "outputs": {}
}
```

---

## <a name="policy"></a>Richtlinie

Eine Richtlinie zur Lebenszyklusverwaltung ist eine Sammlung von Regeln in einem JSON-Dokument:

```json
{
  "rules": [
    {
      "name": "rule1",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {...}
    },
    {
      "name": "rule2",
      "type": "Lifecycle",
      "definition": {...}
    }
  ]
}
```

Eine Richtlinie ist eine Sammlung von Regeln:

| Parametername | Parametertyp | Notizen |
|----------------|----------------|-------|
| `rules`        | Ein Array von Regelobjekten | Eine Richtlinie muss mindestens eine Regel enthalten. Sie können in einer Richtlinie bis zu 100 Regeln definieren.|

Jede Regel in der Richtlinie umfasst mehrere Parameter:

| Parametername | Parametertyp | Notizen | Erforderlich |
|----------------|----------------|-------|----------|
| `name`         | String |Ein Regelname kann bis zu 256 alphanumerische Zeichen enthalten. Bei Regelnamen wird die Groß-/Kleinschreibung unterschieden. Er muss innerhalb einer Richtlinie eindeutig sein. | True |
| `enabled`      | Boolean | Ein optionaler boolescher Wert, über den eine Regel temporär deaktiviert werden kann. Der Standardwert ist „True“, wenn dieser Wert nicht festgelegt wurde. | False | 
| `type`         | Ein Enumerationswert | Aktuell ist `Lifecycle` der gültige Typ. | True |
| `definition`   | Ein Objekt, das die Lebenszyklusregel definiert | Jede Definition beinhaltet einen Filtersatz und einen Aktionssatz. | True |

## <a name="rules"></a>Regeln

Jede Regeldefinition enthält einen Filtersatz und einen Aktionssatz. Der [Filtersatz](#rule-filters) schränkt Regelaktionen auf eine bestimmte Menge von Objekten innerhalb eines Containers oder auf Objektnamen ein. Der [Aktionssatz](#rule-actions) wendet die Ebenen- oder Löschaktionen auf den gefilterten Satz von Objekten an.

### <a name="sample-rule"></a>Beispielregel

Mit der folgenden Beispielregel wird das Konto so gefiltert, dass Aktionen für Objekte ausgeführt werden, die sich in `container1` befinden und mit `foo` beginnen.

>[!NOTE]
>- Die Lebenszyklusverwaltung unterstützt die Typen „Blockblob“ und „Anfügeblob“.<br>
>- Die Lebenszyklusverwaltung hat keinen Einfluss auf Systemcontainer wie $logs und $web.

- Blob 30 Tage nach der letzten Änderung in die kalte Ebene verschieben
- Blob 90 Tage nach der letzten Änderung in die Archivebene verschieben
- Blob 2.555 Tage (sieben Jahre) nach der letzten Änderung löschen
- Löschen früherer Blobversionen 90 Tage nach der Erstellung

```json
{
  "rules": [
    {
      "enabled": true,
      "name": "rulefoo",
      "type": "Lifecycle",
      "definition": {
        "actions": {
          "version": {
            "delete": {
              "daysAfterCreationGreaterThan": 90
            }
          },
          "baseBlob": {
            "tierToCool": {
              "daysAfterModificationGreaterThan": 30
            },
            "tierToArchive": {
              "daysAfterModificationGreaterThan": 90
            },
            "delete": {
              "daysAfterModificationGreaterThan": 2555
            }
          }
        },
        "filters": {
          "blobTypes": [
            "blockBlob"
          ],
          "prefixMatch": [
            "container1/foo"
          ]
        }
      }
    }
  ]
}
```

### <a name="rule-filters"></a>Regelfilter

Filter schränken Regelaktionen auf eine Teilmenge der Blobs innerhalb des Speicherkontos ein. Wenn mehrere Filter definiert sind, wird ein logisches `AND` für alle Filter ausgeführt.

Filter umfassen Folgendes:

| Filtername | Filtertyp | Notizen | Ist erforderlich |
|-------------|-------------|-------|-------------|
| blobTypes   | Ein Array von vordefinierten Enumerationswerten. | In der aktuellen Version werden `blockBlob` und `appendBlob` unterstützt. Für `appendBlob` werden nur Blobs zum Löschen unterstützt, festgelegte Ebenen dagegen nicht. | Ja |
| prefixMatch | Ein Array von Zeichenfolgen für Präfixe, die abgeglichen werden sollen. In jeder Regel können bis zu 10 Präfixe definiert werden. Eine Präfixzeichenfolge muss mit einem Containernamen beginnen. Wenn Sie für eine Regel beispielsweise eine Übereinstimmung mit allen Blobs unter „`https://myaccount.blob.core.windows.net/container1/foo/...`“ erzielen möchten, lautet der prefixMatch-Wert `container1/foo`. | Wenn Sie prefixMatch nicht definieren, gilt die Regel für alle Blobs im Speicherkonto. | Nein |
| blobIndexMatch | Ein Array von Wörterbuchwerten, die aus dem Blobindextag-Schlüssel und den Wertbedingungen bestehen, die abgeglichen werden sollen. In jeder Regel können bis zu 10 Blobindextag-Bedingungen definiert werden. Wenn Sie beispielsweise alle Blobs mit `Project = Contoso` unter `https://myaccount.blob.core.windows.net/` für eine Regel abgleichen möchten, lautet der blobIndexMatch-Wert `{"name": "Project","op": "==","value": "Contoso"}`. | Wenn Sie blobIndexMatch nicht definieren, gilt die Regel für alle Blobs im Speicherkonto. | Nein |

> [!NOTE]
> Der Blobindex befindet sich in der öffentlichen Vorschauphase und ist in den Regionen **Kanada, Mitte**, **Kanada, Osten**, **Frankreich, Mitte** und **Frankreich, Süden** verfügbar. Weitere Informationen zu dieser Funktion sowie zu bekannten Problemen und Einschränkungen finden Sie unter [Verwalten und Suchen von Daten in Azure Blob Storage mit dem Blobindex (Vorschau)](storage-manage-find-blobs.md).

### <a name="rule-actions"></a>Regelaktionen

Aktionen werden auf die gefilterten Blobs angewandt, wenn die Ausführungsbedingung erfüllt ist.

Die Lebenszyklusverwaltung unterstützt Ebenenverschiebung und Löschen von Blobs, früheren Blobversionen und Blobmomentaufnahmen. Definieren Sie mindestens eine Aktion für jede Regel für Basisblobs, früheren Blobversionen oder Blobmomentaufnahmen.

| Aktion                      | Basisblob                                  | Momentaufnahme      | Version
|-----------------------------|--------------------------------------------|---------------|---------------|
| tierToCool                  | Unterstützt für `blockBlob`                  | Unterstützt     | Unterstützt     |
| enableAutoTierToHotFromCool | Unterstützt für `blockBlob`                  | Nicht unterstützt | Nicht unterstützt |
| tierToArchive               | Unterstützt für `blockBlob`                  | Unterstützt     | Unterstützt     |
| delete                      | Für `blockBlob` und `appendBlob` unterstützt | Unterstützt     | Unterstützt     |

>[!NOTE]
>Wenn für das gleiche Blob mehrere Aktionen definiert sind, wendet die Lebenszyklusverwaltung die am wenigsten teure Aktion auf das Blob an. Beispielsweise ist die Aktion `delete` kostengünstiger als die Aktion `tierToArchive`. Die Aktion `tierToArchive` ist kostengünstiger als die Option `tierToCool`.

Die Ausführungsbedingungen basieren auf dem Alter. Basisblobs verwenden den Zeitpunkt der letzten Änderung, Blobversionen den Erstellungszeitpunkt und Blobmomentaufnahmen den Erstellungszeitpunkt der Momentaufnahme, um das Alter nachzuverfolgen.

| Aktionsausführungsbedingung               | Wert der Bedingung                          | BESCHREIBUNG                                                                      |
|------------------------------------|------------------------------------------|----------------------------------------------------------------------------------|
| daysAfterModificationGreaterThan   | Ganzzahliger Wert, der das Alter in Tagen angibt | Bedingung für Basisblobaktionen                                              |
| daysAfterCreationGreaterThan       | Ganzzahliger Wert, der das Alter in Tagen angibt | Bedingung für Aktionen für Blobversionen und Blobmomentaufnahmen                         |
| daysAfterLastAccessTimeGreaterThan | Ganzzahliger Wert, der das Alter in Tagen angibt | (Vorschau) Die Bedingung für Basisblobaktionen, wenn die Uhrzeit für den letzten Zugriff aktiviert ist |

## <a name="examples"></a>Beispiele

Die folgenden Beispiele veranschaulichen die Behandlung von gängigen Szenarien mithilfe von Regeln zur Lebenszyklusverwaltung.

### <a name="move-aging-data-to-a-cooler-tier"></a>Verschieben von alternden Daten auf eine kühlere Ebene

Dieses Beispiel zeigt den Übergang von Blockblobs mit dem Präfix `container1/foo` oder `container2/bar`. Die Richtlinie überführt Blobs, die mehr als 30 Tage nicht verändert wurden, in den kalten Speicher und Blobs, die in 90 Tagen nicht verändert wurden, in die Archivebene:

```json
{
  "rules": [
    {
      "name": "agingRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "container1/foo", "container2/bar" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

### <a name="move-data-based-on-last-accessed-date-preview"></a>Verschieben von Daten basierend auf dem Datum des letzten Zugriffs (Vorschau)

Sie können die Zeitüberwachung für den letzten Zugriff aktivieren, damit aufgezeichnet wird, wann Ihr Blog zuletzt gelesen oder beschrieben wurde. Sie können den letzten Zugriff als Filter zum Verwalten von Tiering und Aufbewahrung von Blobdaten verwenden.

Die Option **Letzter Zugriff** ist in der Vorschau in den folgenden Regionen verfügbar:

 - Frankreich, Mitte
 - Kanada, Osten
 - Kanada, Mitte

> [!IMPORTANT]
> Die Vorschau der Zeitüberwachung für den letzten Zugriff ist nur zur Verwendung in Umgebungen außerhalb der Produktion bestimmt. Produktions-SLAs (Service Level Agreements, Vereinbarungen zum Servicelevel) sind derzeit nicht verfügbar.

Wenn Sie die Option **Letzter Zugriff** verwenden möchten, wählen Sie im Azure-Portal auf der Seite **Lebenszyklusverwaltung** die Option **Access tracking enabled** (Zugriffsüberwachung aktiviert) aus.

#### <a name="how-last-access-time-tracking-works"></a>Funktionsweise der Zeitüberwachung für den letzten Zugriff

Wenn die Zeitüberwachung für den letzten Zugriff aktiviert ist, wird die Blobeigenschaft, mit der `LastAccessTime` aufgerufen wird, aktualisiert, wenn ein Blob gelesen oder beschrieben wird. Dabei gilt der Vorgang [Get Blob](/rest/api/storageservices/get-blob) als Zugriff. [Get Blob Properties](/rest/api/storageservices/get-blob-properties), [Get Blob Metadata](/rest/api/storageservices/get-blob-metadata) und [Get Blob Tags](/rest/api/storageservices/get-blob-tags) gelten nicht als Zugriff. Daher wird bei diesen Vorgängen die Uhrzeit des letzten Zugriffs nicht aktualisiert.

Um die Auswirkungen auf die Lesezugriffswartezeit zu minimieren, wird die Uhrzeit des letzten Zugriffs nur beim ersten Lesezugriff der letzten 24 Stunden aktualisiert. Bei nachfolgenden Lesezugriffen innerhalb desselben 24-Stunden-Zeitraums wird die Uhrzeit des letzten Zugriffs nicht aktualisiert. Wenn ein Blob zwischen zwei Lesezugriffen geändert wird, ist die Uhrzeit des letzten Zugriffs die aktuellere der beiden Werte.

Im nachstehenden Beispiel werden Blobs in den kalten Speicher verschoben, wenn auf sie während 30 Tagen nicht zugegriffen wurde. Bei der `enableAutoTierToHotFromCool`-Eigenschaft handelt es sich um einen booleschen Wert, der angibt, ob für einen Blob automatisch ein Tiering von der kalten Ebene zurück zur heißen Ebene durchgeführt werden soll, wenn nach dem Tiering auf die kalte Ebene noch mal auf den Blob zugegriffen wird.

```json
{
  "enabled": true,
  "name": "last-accessed-thirty-days-ago",
  "type": "Lifecycle",
  "definition": {
    "actions": {
      "baseBlob": {
        "enableAutoTierToHotFromCool": true,
        "tierToCool": {
          "daysAfterLastAccessTimeGreaterThan": 30
        }
      }
    },
    "filters": {
      "blobTypes": [
        "blockBlob"
      ],
      "prefixMatch": [
        "mylifecyclecontainer/log"
      ]
    }
  }
}
```

#### <a name="storage-account-support"></a>Speicherkontounterstützung

Die Zeitüberwachung für den letzten Zugriff ist für die folgenden Typen von Speicherkonten verfügbar:

 - Speicherkonten vom Typ „Universell V2“
 - Blockblob-Speicherkonten
 - Blob-Speicherkonten

Wenn Ihr Speicherkonto vom Typ „Universell V1“ ist, verwenden Sie das Azure-Portal, um ein Upgrade auf ein Konto vom Typ „Universell V2“ durchzuführen.

Speicherkonten mit einem hierarchischen Namespace, die für die Verwendung mit Azure Data Lake Storage Gen2 aktiviert sind, werden nun unterstützt.

#### <a name="pricing-and-billing"></a>Preise und Abrechnung

Die Aktualisierung der Uhrzeit des letzten Zugriffs gilt als [sonstiger Vorgang](https://azure.microsoft.com/pricing/details/storage/blobs/).

### <a name="archive-data-after-ingest"></a>Archivieren von Daten nach der Erfassung

Außerdem gibt es Daten, die in der Cloud lediglich vorgehalten werden und auf die nach der Speicherung nur sehr selten oder gar nicht zugegriffen wird. Die folgende Lebenszyklusrichtlinie ist so konfiguriert, dass Daten kurz nach der Erfassung archiviert werden. In diesem Beispiel werden Blockblobs im Speicherkonto im Container `archivecontainer` an eine Archivebene überführt. Die Umstellung wird durch die Ausführung der Aktion für Blobs 0 Tage nach dem Zeitpunkt der letzten Änderung erreicht:

> [!NOTE] 
> Es wird empfohlen, die Blobs für mehr Effizienz direkt auf die Archivebene hochzuladen. Sie können den Header „x-ms-access-tier“ für [PutBlob](/rest/api/storageservices/put-blob) oder [PutBlockList](/rest/api/storageservices/put-block-list) mit der REST-Version 2018-11-09 und höher oder unseren neuesten Blobspeicher-Clientbibliotheken verwenden. 

```json
{
  "rules": [
    {
      "name": "archiveRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "archivecontainer" ]
        },
        "actions": {
          "baseBlob": {
              "tierToArchive": { "daysAfterModificationGreaterThan": 0 }
          }
        }
      }
    }
  ]
}

```

### <a name="expire-data-based-on-age"></a>Ablauf von Daten nach dem Alter

Für einige Daten wird erwartet, dass sie einige Tage oder Monate nach der Erstellung ablaufen. Sie können eine Richtlinie zur Lebenszyklusverwaltung so einrichten, dass Daten durch Löschung auf der Grundlage ihres Alters ablaufen. Das folgende Beispiel zeigt eine Richtlinie, mit der alle Blockblobs gelöscht werden, die älter als 365 Tage sind.

```json
{
  "rules": [
    {
      "name": "expirationRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ]
        },
        "actions": {
          "baseBlob": {
            "delete": { "daysAfterModificationGreaterThan": 365 }
          }
        }
      }
    }
  ]
}
```

### <a name="delete-data-with-blob-index-tags"></a>Löschen von Daten mit Blobindextags
Einige Daten sollten nur ablaufen, wenn sie explizit zur Löschung gekennzeichnet sind. Sie können eine Lebenszyklusverwaltungsrichtlinie so konfigurieren, dass Daten ablaufen, die mit Schlüssel-Wert-Attributen für den Blobindex gekennzeichnet sind. Im folgenden Beispiel ist eine Richtlinie dargestellt, mit der alle Blockblobs gelöscht werden, die mit `Project = Contoso` gekennzeichnet sind. Weitere Informationen zum Blobindex finden Sie unter [Verwalten und Suchen von Daten in Azure Blob Storage mit dem Blobindex (Vorschau)](storage-manage-find-blobs.md).

```json
{
    "rules": [
        {
            "enabled": true,
            "name": "DeleteContosoData",
            "type": "Lifecycle",
            "definition": {
                "actions": {
                    "baseBlob": {
                        "delete": {
                            "daysAfterModificationGreaterThan": 0
                        }
                    }
                },
                "filters": {
                    "blobIndexMatch": [
                        {
                            "name": "Project",
                            "op": "==",
                            "value": "Contoso"
                        }
                    ],
                    "blobTypes": [
                        "blockBlob"
                    ]
                }
            }
        }
    ]
}
```

### <a name="manage-versions"></a>Verwalten von Versionen

Für Daten, die während ihrer gesamten Lebensdauer regelmäßig geändert werden und auf die regelmäßig zugegriffen wird, können Sie die Blob Storage-Versionsverwaltung aktivieren, um frühere Versionen eines Objekts automatisch zu verwalten. Sie können eine Richtlinie erstellen, um frühere Versionen Ebenen zuzuordnen oder zu löschen. Das Alter der Version wird durch Auswertung der Erstellungszeit der Version bestimmt. Entsprechend dieser Richtlinienregel werden frühere Versionen innerhalb des Containers `activedata`, die 90 Tage oder älter sind (nach der Versionserstellung), der Ebene „kalt“ zugeordnet und frühere Versionen, die 365 Tage oder älter sind, gelöscht.

```json
{
  "rules": [
    {
      "enabled": true,
      "name": "versionrule",
      "type": "Lifecycle",
      "definition": {
        "actions": {
          "version": {
            "tierToCool": {
              "daysAfterCreationGreaterThan": 90
            },
            "delete": {
              "daysAfterCreationGreaterThan": 365
            }
          }
        },
        "filters": {
          "blobTypes": [
            "blockBlob"
          ],
          "prefixMatch": [
            "activedata"
          ]
        }
      }
    }
  ]
}
```

## <a name="faq"></a>Häufig gestellte Fragen

**Ich habe eine neue Richtlinie erstellt. Warum werden die Aktionen nicht sofort ausgeführt?**

Die Plattform führt die Lebenszyklusrichtlinie ein Mal täglich aus. Nachdem Sie eine neue Richtlinie konfiguriert haben, kann es bis zu 24 Stunden dauern, bis einige Aktionen das erste Mal ausgeführt werden.

**Wie lange dauert es nach dem Aktualisieren einer vorhandenen Richtlinie, bis die Aktionen ausgeführt werden?**

Es dauert bis zu 24 Stunden, bis die aktualisierte Richtlinie in Kraft tritt. Sobald die Richtlinie gültig ist, kann es bis zu 24 Stunden dauern, bis die Aktionen ausgeführt werden. Daher kann der Abschluss von Richtlinieaktionen bis zu 48 Stunden dauern.

**Ich habe ein archiviertes Blob manuell wieder aktiviert. Wie kann ich vorübergehend verhindern, dass es zurück auf die Archivspeicherebene verschoben wird?**

Wenn ein Blob von einer Zugriffsebene auf eine andere verschoben wird, ändert sich der Zeitpunkt der letzten Änderung nicht. Wenn Sie ein archiviertes Blob auf der heißen Ebene manuell aktivieren, würde es vom Modul für die Lebenszyklusverwaltung zurück auf die Archivspeicherebene verschoben. Deaktivieren Sie vorübergehend die Regel, die sich auf dieses Blob auswirkt, um zu verhindern, dass es erneut archiviert wird. Aktivieren Sie die Regel erneut, wenn das Blob sicher zurück auf die Archivspeicherebene verschoben werden kann. Sie können das Blob auch an einen anderen Speicherort kopieren, falls es sich dauerhaft auf einer heißen oder kalten Ebene befinden muss.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Daten nach versehentlichem Löschen wiederhergestellt werden:

- [Vorläufiges Löschen für Azure Storage-Blobs](./soft-delete-blob-overview.md)

Erfahren Sie, wie Sie Daten mit dem Blobindex verwalten und suchen können:

- [Verwalten und Suchen von Daten in Azure Blob Storage mit dem Blobindex](storage-manage-find-blobs.md)