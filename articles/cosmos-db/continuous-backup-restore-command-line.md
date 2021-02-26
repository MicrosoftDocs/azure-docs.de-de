---
title: Mit der Azure-Befehlszeilenschnittstelle können Sie fortlaufende Sicherungen und Zeitpunktwiederherstellungen in Azure Cosmos DB konfigurieren.
description: Erfahren Sie, wie Sie ein Konto mit fortlaufenden Sicherungs- und Wiederherstellungsdaten mit der Azure-Befehlszeilenschnittstelle bereitstellen.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 2e09542cbe56df7c8d6984a98fe77142f543ec03
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539191"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore-preview---using-azure-cli"></a>Konfigurieren und Verwalten von fortlaufender Sicherung und Zeitpunktwiederherstellung (Vorschau) – Azure-Befehlszeilenschnittstelle
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Das Feature der Zeitpunktwiederherstellung (fortlaufender Sicherungsmodus) für Azure Cosmos DB befindet sich zurzeit in der öffentlichen Vorschauphase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Mithilfe des Azure Cosmos DB-Features der Zeitpunktwiederherstellung (Vorschau) können Sie nach einer versehentlichen Änderung in einem Container ein gelöschtes Konto, eine Datenbank oder einen Container wiederherstellen oder aber eine Wiederherstellung in einer beliebigen Region (in der es Sicherungen gab) durchführen. Der fortlaufende Sicherungsmodus ermöglicht Ihnen die Wiederherstellung zu jedem beliebigen Zeitpunkt innerhalb der letzten 30 Tage.

In diesem Artikel wird beschrieben, wie Sie ein Konto mit fortlaufenden Sicherungs- und Wiederherstellungsdaten mithilfe der Azure-Befehlszeilenschnittstelle bereitstellen können.

## <a name="install-azure-cli"></a><a id="install"></a>Installieren der Azure-Befehlszeilenschnittstelle

1. Installieren der neuesten Version der Azure-Befehlszeilenschnittstelle

   * Installieren Sie die neueste Version der [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli), in jedem Fall Version über 2.17.1.
   * Wenn Sie die Befehlszeilenschnittstelle bereits installiert haben, führen Sie den Befehl `az upgrade` aus, um ein Update auf die neueste Version auszuführen. Dieser Befehl funktioniert nur mit einer Befehlszeilenschnittstellenversion über Version 2.11. Wenn Sie über eine frühere Version verfügen, installieren Sie die neueste Version über den obigen Link.

1. Installieren Sie die `cosmosdb-preview`-Erweiterung für die Befehlszeilenschnittstelle.

   * Die Befehle für die Zeitpunktwiederherstellung sind über die `cosmosdb-preview`-Erweiterung verfügbar.
   * Sie können diese Erweiterung mit dem folgenden Befehl installieren: `az extension add --name cosmosdb-preview`
   * Sie können diese Erweiterung mit dem folgenden Befehl deinstallieren: `az extension remove --name cosmosdb-preview`

1. Anmelden und Auswählen Ihres Abonnements

   * Melden Sie sich bei Ihrem Azure-Konto mit dem Befehl `az login` an.
   * Wählen Sie mit dem Befehl `az account set -s <subscriptionguid>` das gewünschte Abonnement aus.

## <a name="provision-a-sql-api-account-with-continuous-backup"></a><a id="provision-sql-api"></a>Bereitstellen eines SQL-API-Kontos mit fortlaufender Sicherung

Zum Bereitstellen eines SQL-API-Kontos mit fortlaufender Sicherung sollte zusammen mit dem regulären Bereitstellungsbefehl das zusätzliche Argument `--backup-policy-type Continuous` übergeben werden. Der folgende Befehl ist ein Beispiel für ein Konto namens `pitracct2` mit einer einzelnen Schreibregion und einer Richtlinie für die fortlaufende Sicherung, das in der Region „USA, Westen“ unter der Ressourcengruppe „myrg“ erstellt wurde:

```azurecli-interactive

az cosmosdb create \
  --name pitracct2 \
  --resource-group myrg \
  --backup-policy-type Continuous \
  --default-consistency-level Session \
  --locations regionName="West US"

```

## <a name="provision-an-azure-cosmos-db-api-for-mongodb-account-with-continuous-backup"></a><a id="provision-mongo-api"></a>Bereitstellen einer Azure Cosmos DB-API für ein MongoDB-Konto mit fortlaufender Sicherung

Der folgende Befehl ist ein Beispiel für ein Konto namens `pitracct3` mit einer einzelnen Schreibregion und einer Richtlinie für die fortlaufende Sicherung, das in der Region „USA, Westen“ unter der Ressourcengruppe „myrg“ erstellt wurde:

```azurecli-interactive

az cosmosdb create \
  --name pitracct3 \
  --kind MongoDB \
  --resource-group myrg \
  --server-version "3.6" \
  --backup-policy-type Continuous \
  --default-consistency-level Session \
  --locations regionName="West US"

```

## <a name="trigger-a-restore-operation-with-cli"></a><a id="trigger-restore"></a>Auslösen eines Wiederherstellungsvorgangs über die Befehlszeilenschnittstelle

Die einfachste Möglichkeit zum Auslösen einer Wiederherstellung besteht darin, den Wiederherstellungsbefehl mit den Namen von Ziel- und Quellkonto, der Ressourcengruppe, dem Zeitstempel (UTC) und optional den Namen von Datenbank und Container auszugeben. Im Folgenden finden Sie einige Beispiele für das Auslösen des Wiederherstellungsvorgangs:

1. Erstellen Sie ein neues Azure Cosmos DB-Konto, indem Sie ein bereits vorhandenes Konto wiederherstellen.

   ```azurecli-interactive

   az cosmosdb restore \
    --target-database-account-name MyRestoredCosmosDBDatabaseAccount \
    --account-name MySourceAccount \
    --restore-timestamp 2020-07-13T16:03:41+0000 \
    --resource-group MyResourceGroup \
    --location "West US"

   ```

2. Erstellen Sie ein neues Azure Cosmos DB-Konto, indem Sie nur ausgewählte Datenbanken und Container aus einem vorhandenen Datenbankkonto wiederherstellen.

   ```azurecli-interactive

   az cosmosdb restore \
    --resource-group MyResourceGroup \
    --target-database-account-name MyRestoredCosmosDBDatabaseAccount \
    --account-name MySourceAccount \
    --restore-timestamp 2020-07-13T16:03:41+0000 \
    --location "West US" \
    --databases-to-restore name=MyDB1 collections=collection1 collection2 \
    --databases-to-restore name=MyDB2 collections=collection3 collection4

   ```

## <a name="enumerate-restorable-resources-for-sql-api"></a><a id="enumerate-sql-api"></a>Auflisten von wiederherstellbaren Ressourcen für die SQL-API

Mithilfe der unten beschriebenen Enumerationsbefehle können Sie die Ressourcen ermitteln, die für verschiedene Zeitstempel wiederhergestellt werden können. Außerdem bieten sie einen Feed von Schlüsselereignissen für die wiederherstellbaren Konto-, Datenbank- und Containerressourcen.

**Auflisten aller Konten, die im aktuellen Abonnement wiederhergestellt werden können**

Führen Sie den folgenden CLI-Befehl aus, um alle Konten aufzulisten, die im aktuellen Abonnement wiederhergestellt werden können.

```azurecli-interactive
az cosmosdb restorable-database-account list --account-name "pitrbb"
```

Die Antwort enthält alle Datenbankkonten (sowohl Live- als auch gelöschte Daten), die wiederhergestellt werden können, sowie die Regionen, aus denen sie wiederhergestellt werden können:

```json
{
    "accountName": "pitrbb",
    "apiType": "Sql",
    "creationTime": "2021-01-08T23:34:11.095870+00:00",
    "deletionTime": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865",
    "identity": null,
    "location": "West US",
    "name": "7133a59a-d1c0-4645-a699-6e296d6ac865",
    "restorableLocations": [
      {
        "creationTime": "2021-01-08T23:34:11.095870+00:00",
        "deletionTime": null,
        "locationName": "West US",
        "regionalDatabaseAccountInstanceId": "f02df26b-c0ec-4829-8bef-3482d36e6230"
      }
    ],
    "tags": null,
    "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts"
  }
```

So wie für das Konto gibt es auch für die Region Werte für „CreationTime“ und „DeletionTime“. Diese Zeiten ermöglichen Ihnen, die richtige Region und einen gültigen Zeitbereich für die Wiederherstellung in dieser Region auszuwählen.

**Auflisten aller Versionen von Datenbanken in einem aktiven Datenbankkonto**

Durch das Auflisten aller Versionen von Datenbanken können Sie die richtige Datenbank auswählen, wenn in Ihrem Szenario die tatsächliche Zeit, in der die Datenbank vorhanden war, unbekannt ist.

Führen Sie den folgenden CLI-Befehl aus, um alle Versionen von Datenbanken aufzulisten. Dieser Befehl funktioniert nur bei aktiven Konten. Die Parameter „instanceId“ und „location“ werden aus der Antwort des Befehls `az cosmosdb restorable-database-account list` von den Eigenschaften „name“ und „location“ abgeleitet. Das instanceId-Attribut ist auch eine Eigenschaft des Quelldatenbankkontos, das wiederhergestellt wird:

```azurecli-interactive
az cosmosdb sql restorable-database list \
  --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
  --location "West US"
```

Die Befehlsausgabe zeigt nun an, wann eine Datenbank erstellt und gelöscht wurde.

```json
[
  {
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865/restorableSqlDatabases/40e93dbd-2abe-4356-a31a-35567b777220",
    ..
    "name": "40e93dbd-2abe-4356-a31a-35567b777220",
    "resource": {
      "database": {
        "id": "db1"
      },
      "eventTimestamp": "2021-01-08T23:27:25Z",
      "operationType": "Create",
      "ownerId": "db1",
      "ownerResourceId": "YuZAAA=="
    },
    ..
  },
  {
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865/restorableSqlDatabases/243c38cb-5c41-4931-8cfb-5948881a40ea",
    ..
    "name": "243c38cb-5c41-4931-8cfb-5948881a40ea",
    "resource": {
      "database": {
        "id": "spdb1"
      },
      "eventTimestamp": "2021-01-08T23:25:25Z",
      "operationType": "Create",
      "ownerId": "spdb1",
      "ownerResourceId": "OIQ1AA=="
    },
   ..
  }
]
```

**Auflisten aller Versionen von SQL-Containern einer Datenbank in einem aktiven Datenbankkonto**

Führen Sie den folgenden Befehl aus, um alle Versionen von SQL-Containern aufzulisten. Dieser Befehl funktioniert nur bei aktiven Konten. Der databaseRid-Parameter ist die „ResourceId“ der Datenbank, die Sie wiederherstellen möchten. Dies ist der Wert des ownerResourceid-Attributs aus der Antwort des Befehls `az cosmosdb sql restorable-database list`.

```azurecli-interactive
az cosmosdb sql restorable-container list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --database-rid "OIQ1AA==" \
    --location "West US"
```

Die Befehlsausgabe enthält die Liste der Vorgänge, die für alle Container in dieser Datenbank ausgeführt wurden:

```json
[
  {
    ...
    
      "eventTimestamp": "2021-01-08T23:25:29Z",
      "operationType": "Replace",
      "ownerId": "procol3",
      "ownerResourceId": "OIQ1APZ7U18="
...
  },
  {
    ...
      "eventTimestamp": "2021-01-08T23:25:26Z",
      "operationType": "Create",
      "ownerId": "procol3",
      "ownerResourceId": "OIQ1APZ7U18="
  },
]
```

**Suchen von Datenbanken oder Containern, die für einen beliebigen Zeitstempel wiederhergestellt werden können**

Verwenden Sie den folgenden Befehl, um die Liste der Datenbanken oder Container abzurufen, die mit einem bestimmten Zeitstempel wiederhergestellt werden können. Dieser Befehl funktioniert nur bei aktiven Konten.

```azurecli-interactive

az cosmosdb sql restorable-resource list \
  --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
  --location "West US" \
  --restore-location "West US" \  
  --restore-timestamp "2021-01-10 T01:00:00+0000"

```

```json
[
  {
    "collectionNames": [
      "procol1",
      "procol2"
    ],
    "databaseName": "db1"
  },
  {
    "collectionNames": [
      "procol3",
       "spcol1"
    ],
    "databaseName": "spdb1"
  }
]
```

## <a name="enumerate-restorable-resources-for-mongodb-api-account"></a><a id="enumerate-mongodb-api"></a>Auflisten von wiederherstellbaren Ressourcen für ein MongoDB-API-Konto

Mithilfe der unten beschriebenen Enumerationsbefehle können Sie die Ressourcen ermitteln, die für verschiedene Zeitstempel wiederhergestellt werden können. Außerdem bieten sie einen Feed von Schlüsselereignissen für die wiederherstellbaren Konto-, Datenbank- und Containerressourcen. Wie bei der SQL-API können Sie den Befehl `az cosmosdb` verwenden, jedoch mit „mongodb“ anstelle von „sql“ als Parameter. Diese Befehle funktionieren nur für aktive Konten.

**Auflisten aller Versionen von MongoDB-Datenbanken in einem aktiven Datenbankkonto**

```azurecli-interactive
az cosmosdb mongodb restorable-database list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --location "West US"
```

**Auflisten aller Versionen von MongoDB-Sammlungen einer Datenbank in einem aktiven Datenbankkonto**

```azurecli-interactive
az cosmosdb mongodb restorable-collection list \
    --instance-id "<InstanceID>" \
    --database-rid "AoQ13r==" \
    --location "West US"
```

**Auflisten aller Ressourcen eines MongoDB-Datenbankkontos, die an einem bestimmten Zeitstempel und in einer bestimmten Region wiederhergestellt werden können**

```azurecli-interactive
az cosmosdb mongodb restorable-resource list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --location "West US" \
    --restore-location "West US" \
    --restore-timestamp "2020-07-20T16:09:53+0000"
```

## <a name="next-steps"></a>Nächste Schritte

* Konfigurieren und verwalten Sie die fortlaufende Sicherung über das [Azure-Portal](continuous-backup-restore-portal.md), mithilfe von [PowerShell](continuous-backup-restore-powershell.md) oder mit [Azure Resource Manager](continuous-backup-restore-template.md).
* [Ressourcenmodell des fortlaufenden Sicherungsmodus](continuous-backup-restore-resource-model.md)
* [Verwalten Sie Berechtigungen](continuous-backup-restore-permissions.md), die zum Wiederherstellen von Daten mit dem fortlaufenden Sicherungsmodus erforderlich sind.
