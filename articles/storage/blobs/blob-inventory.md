---
title: Verwalten von Blobdaten mit dem Azure Storage-Bestand (Vorschau)
description: Der Azure Storage-Bestand ist ein Tool, mit dem Sie eine Übersicht über alle Ihre Blobdaten in einem Speicherkonto erhalten.
services: storage
author: mhopkins-msft
ms.service: storage
ms.date: 12/03/2020
ms.topic: conceptual
ms.author: mhopkins
ms.reviewer: yzheng
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: 86ded3dea819702631b1fa04dbc56f727566fc98
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96602681"
---
# <a name="use-azure-storage-blob-inventory-to-manage-blob-data-preview"></a>Verwalten von Blobdaten mit dem Azure Storage-Blobbestand (Vorschau)

Die Funktion Azure Storage-Blobbestand bietet Ihnen eine Übersicht über Ihre Blobdaten in einem Speicherkonto. Mithilfe des Bestandsberichts können Sie die gesamte Datengröße, das Alter, den Verschlüsselungsstatus usw. besser verstehen. Der Bericht enthält eine Übersicht über Ihre Daten, die Sie auch für Geschäfts- und Complianceanforderungen nutzen können. Nach der Aktivierung wird automatisch täglich ein Bestandsbericht erstellt.

## <a name="availability"></a>Verfügbarkeit

Der Blobbestand wird sowohl für die universelle Version 2 (General Purpose Version, GPv2) als auch für Blockblobspeicher mit Premium-Leistung unterstützt. Diese Funktion wird mit oder ohne aktivierte Funktion [hierarchischer Namespace](data-lake-storage-namespace.md) unterstützt.

### <a name="preview-regions"></a>Vorschauregionen

Die Vorschauversion des Blobbestands ist für Speicherkonten in den folgenden Regionen verfügbar:

- Frankreich, Mitte
- Kanada, Mitte
- Kanada, Osten

### <a name="pricing-and-billing"></a>Preise und Abrechnung

Die Gebühr für Bestandsberichte wird während der Vorschauphase nicht abgerechnet. Die Preise werden erst festgelegt, wenn dieses Feature allgemein verfügbar ist.

## <a name="enable-inventory-reports"></a>Aktivieren von Bestandsberichten

Sie aktivieren Blobbestandsberichte, indem Sie Ihrem Speicherkonto eine Richtlinie hinzufügen. Sie können eine Richtlinie über das [Azure-Portal](https://portal.azure.com/) hinzufügen, bearbeiten oder entfernen.

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com/).
1. Wählen Sie eines Ihrer Speicherkonten aus.
1. Wählen Sie unter **Blob-Dienst** die Option **Blob inventory** (Blobbestand) aus.
1. Stellen Sie sicher, dass **Blob inventory enabled** (Blobinventur aktiviert) aktiviert ist.
1. Wählen Sie **Regel hinzufügen** aus.
1. Benennen Sie die neue Regel.
1. Wählen Sie die **Blobtypen** für Ihren Bestandsbericht aus.
1. Fügen Sie ein Präfix für die Übereinstimmung hinzu, um Ihren Bestandsbericht zu filtern.
1. Entscheiden Sie, ob Sie **Blobversionen** und **Momentaufnahmen** in den Bestandsbericht einschließen möchten. Versionen und Momentaufnahmen müssen im Konto aktiviert sein, damit eine neue Regel mit der entsprechenden aktivierten Option gespeichert werden kann.
1. Wählen Sie **Speichern** aus.

:::image type="content" source="./media/blob-inventory/portal-blob-inventory.png" alt-text="Screenshot des Hinzufügens einer Blobbestandsregel über das Azure-Portal":::

Bestandsrichtlinien werden vollständig gelesen oder geschrieben. Teilaktualisierungen werden nicht unterstützt.

> [!IMPORTANT]
> Wenn Sie Firewallregeln für Ihr Speicherkonto aktivieren, werden Bestandsanforderungen möglicherweise blockiert. Sie können die Sperre dieser Anforderungen durch Bereitstellen von Ausnahmen für vertrauenswürdige Microsoft-Dienste aufheben. Weitere Informationen finden Sie im Abschnitt „Ausnahmen“ unter [Konfigurieren von Firewalls und virtuellen Netzwerken](../common/storage-network-security.md#exceptions).

Es wird automatisch eine tägliche Blobinventur geplant. Die Inventur kann bis zum Abschluss bis zu 24 Stunden benötigen. Sie konfigurieren einen Bestandsbericht, indem Sie eine Inventurrichtlinie mit mindestens einer Regel hinzufügen.

## <a name="inventory-policy"></a>Inventurrichtlinie

Eine Inventurrichtlinie ist eine Sammlung von Regeln in einem JSON-Dokument.

```json
{
    "destination": "destinationContainer",
    "enabled": true,
    "rules": [
    {
        "enabled": true,
        "name": "inventoryrule1",
        "definition": {. . .}
    },
    {
        "enabled": true,
        "name": "inventoryrule2",
        "definition": {. . .}
    }]
}
```

Sie zeigen den JSON-Code für eine Inventurrichtlinie an, indem Sie im Azure-Portal im Abschnitt **Blob Inventory** (Blobbestand) die Registerkarte **Codeansicht** auswählen.

| Parametername | Parametertyp        | Notizen | Erforderlich? |
|----------------|-----------------------|-------|-----------|
| destination    | Zeichenfolge                | Der Zielcontainer, in dem alle Bestandsdateien generiert werden. Der Zielcontainer muss bereits vorhanden sein. | Ja |
| enabled        | Boolean               | Dient zum Deaktivieren der gesamten Richtlinie. Wenn diese Option auf **true** festgelegt ist, überschreibt das aktivierte Feld auf Regelebene diesen Parameter. Wenn diese Option deaktiviert ist, wird die Inventur für alle Regeln deaktiviert. | Ja |
| rules          | Ein Array von Regelobjekten | Eine Richtlinie muss mindestens eine Regel enthalten. Bis zu 10 Regeln werden unterstützt. | Ja |

## <a name="inventory-rules"></a>Inventurregeln

Eine Regel erfasst die Filterbedingungen und Ausgabeparameter zum Erstellen eines Bestandsberichts. Jeder Regel erstellt einen Bestandsbericht. Regeln können überlappende Präfixe aufweisen. Ein Blob kann abhängig von den Regeldefinitionen auch in mehreren Beständen enthalten sein.

Jede Regel in der Richtlinie umfasst mehrere Parameter:

| Parametername | Parametertyp                 | Notizen | Erforderlich? |
|----------------|--------------------------------|-------|-----------|
| name           | String                         | Ein Regelname kann bis zu 256 alphanumerische Zeichen (Groß-/Kleinschreibung beachten) enthalten. Der Name muss innerhalb einer Richtlinie eindeutig sein. | Ja |
| enabled        | Boolean                        | Ein Flag, das es ermöglicht, eine Regel zu aktivieren oder zu deaktivieren. Der Standardwert lautet **true**. | Ja |
| Definition     | JSON-Definition der Inventurregel | Jede Definition beinhaltet einen Regelfiltersatz. | Ja |

Das globale Flag **Blob inventory enabled** (Blobinventur aktiviert) hat Vorrang vor dem *enabled*-Parameter in einer Regel.

### <a name="rule-filters"></a>Regelfilter

Zum Anpassen eines Blobbestandsberichts sind mehrere Filter verfügbar:

| Filtername         | Filtertyp                     | Notizen | Erforderlich? |
|---------------------|---------------------------------|-------|-----------|
| blobTypes           | Ein Array von vordefinierten Enumerationswerten. | Gültige Werte sind `blockBlob` und `appendBlob` für Konten mit aktiviertem hierarchischem Namespace sowie `blockBlob`, `appendBlob` und `pageBlob` für andere Konten. | Ja |
| prefixMatch         | Ein Array von bis zu 10 Zeichenfolgen für Präfixe, die abgeglichen werden sollen. Ein Präfix muss mit einem Containernamen beginnen, z. B. „container1/foo“. | Wenn Sie *prefixMatch* nicht definieren oder ein leeres Präfix angeben, gilt die Regel für alle Blobs im Speicherkonto. | Nein |
| includeSnapshots    | Boolean                         | Gibt an, ob Momentaufnahmen in die Inventur eingeschlossen werden sollen. Die Standardeinstellung lautet **false**. | Nein |
| includeBlobVersions | Boolean                         | Gibt an, ob Blobversionen in die Inventur eingeschlossen werden sollen. Die Standardeinstellung lautet **false**. | Nein |

Sie zeigen den JSON-Code für Inventurrichtlinien an, indem Sie im Azure-Portal im Abschnitt **Blob Inventory** (Blobbestand) die Registerkarte **Codeansicht** auswählen. Filter werden innerhalb einer Regeldefinition angegeben.

```json
{
    "destination": "destinationContainer",
    "enabled": true,
    "rules": [
    {
        "enabled": true,
        "name": "inventoryrule1",
        "definition":
        {
            "filters":
            {
                "blobTypes": ["blockBlob", "appendBlob", "pageBlob"],
                "prefixMatch": ["inventorycontainer1", "inventorycontainer2/abcd", "etc"]
            }
        }
    },
    {
        "enabled": true,
        "name": "inventoryrule2",
        "definition":
        {
            "filters":
            {
                "blobTypes": ["pageBlob"],
                "prefixMatch": ["inventorycontainer-disks-", "inventorycontainer4/"],
                "includeSnapshots": true,
                "includeBlobVersions": true
            }
        }
    }]
}
```

## <a name="inventory-output"></a>Inventurausgabe

Bei jeder Inventur werden CSV-Dateien im angegebenen Zielcontainer für die Inventur generiert. Die Inventurausgabe wird unter folgendem Pfad generiert: `https://<accountName>.blob.core.windows.net/<inventory-destination-container>/YYYY/MM/DD/HH-MM-SS/`. Dabei gilt:

- *accountName* ist der Name Ihres Azure Blob Storage-Kontos.
- *inventory-destination-container* ist der in Ihrer Inventurrichtlinie angegebene Zielcontainer.
- *YYYY/MM/DD/HH-MM-SS* ist die Uhrzeit, zu der die Inventur gestartet wurde.

### <a name="inventory-files"></a>Inventurdateien

Bei jeder Inventur werden die folgenden Dateien generiert:

- **CSV-Datei mit dem Bestand:** Eine CSV-Datei für jede Inventurregel. Jede Datei enthält die übereinstimmenden Objekte und ihre Metadaten. Die erste Zeile in jeder CSV-Datei enthält immer das Schema. Die folgende Abbildung zeigt eine in Microsoft Excel geöffnete CSV-Bestandsdatei.

   :::image type="content" source="./media/blob-inventory/csv-file-excel.png" alt-text="Screenshot einer in Microsoft Excel geöffneten CSV-Bestandsdatei":::

- **Manifestdatei:** Eine Datei „manifest.json“ mit Details zu den Bestandsdateien, die für jede Regel in dieser Ausführung generiert wurden. Die Manifestdatei enthält auch die vom Benutzer angegebene Regeldefinition und den Pfad zum Bestand zu dieser Regel.

- **Prüfsummendatei:** Eine Datei „manifest.checksum“ mit der MD5-Prüfsumme des Inhalts der Datei „manifest.json“. Die Generierung der Datei „manifest.checksum“ markiert das Ende einer Inventur.

## <a name="inventory-completed-event"></a>Inventurabschlussereignis

Abonnieren Sie das Inventurabschlussereignis, um benachrichtigt zu werden, wenn die Inventur abgeschlossen ist. Dieses Ereignis wird generiert, wenn die Datei „manifest.checksum“ erstellt wird. Das Inventurabschlussereignis tritt auch ein, wenn vor dem Start der Inventur ein Benutzerfehler auftritt. Beispielsweise wird dieses Ereignis durch eine ungültige Richtlinie oder einen fehlenden Zielcontainer ausgelöst. Das Ereignis wird im Blobbestandsthema veröffentlicht.

Beispielereignis:

```json
{
  "topic": "/subscriptions/3000151d-7a84-4120-b71c-336feab0b0f0/resourceGroups/BlobInventory/providers/Microsoft.EventGrid/topics/BlobInventoryTopic",
  "subject": "BlobDataManagement/BlobInventory",
  "eventType": "Microsoft.Storage.BlobInventoryPolicyCompleted",
  "id": "c99f7962-ef9d-403e-9522-dbe7443667fe",
  "data": {
    "scheduleDateTime": "2020-10-13T15:37:33Z",
    "accountName": "inventoryaccountname",
    "policyRunStatus": "Succeeded",
    "policyRunStatusMessage": "Inventory run succeeded, refer manifest file for inventory details.",
    "policyRunId": "b5e1d4cc-ee23-4ed5-b039-897376a84f79",
    "manifestBlobUrl": "https://inventoryaccountname.blob.core.windows.net/inventory-destination-container/2020/10/13/15-37-33/manifest.json"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-10-13T15:47:54Z"
}
```

## <a name="next-steps"></a>Nächste Schritte

[Verwalten des Azure Blob Storage-Lebenszyklus](storage-lifecycle-management-concepts.md)
