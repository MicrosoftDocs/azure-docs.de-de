---
title: Mithilfe von Azure PowerShell können Sie fortlaufende Sicherungen und Zeitpunktwiederherstellungen in Azure Cosmos DB konfigurieren.
description: Hier erfahren Sie, wie Sie ein Konto mit fortlaufenden Sicherungs- und Wiederherstellungsdaten mithilfe von Azure PowerShell bereitstellen.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 73652f821abfa4a092e4a61ffe2be9e7262a2f10
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99538543"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore-preview---using-azure-powershell"></a>Konfigurieren und Verwalten von fortlaufender Sicherung und Zeitpunktwiederherstellung (Vorschau) – Verwendung von Azure PowerShell
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Das Feature „Zeitpunktwiederherstellung“ (fortlaufender Sicherungsmodus) für Azure Cosmos DB befindet sich zurzeit in der öffentlichen Vorschau.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Mithilfe des Azure Cosmos DB-Features „Zeitpunktwiederherstellung“ (Vorschau) können Sie nach einer versehentlichen Änderung in einem Container ein gelöschtes Konto, eine Datenbank oder einen Container wiederherstellen oder aber eine Wiederherstellung in einer beliebigen Region (in der es Sicherungen gab) durchführen. Der fortlaufende Sicherungsmodus ermöglicht Ihnen die Wiederherstellung auf jeden beliebigen Zeitpunkt innerhalb der letzten 30 Tage.

In diesem Artikel wird beschrieben, wie Sie ein Konto mit fortlaufenden Sicherungs- und Wiederherstellungsdaten mithilfe von Azure PowerShell bereitstellen können.

## <a name="install-azure-powershell"></a><a id="install-powershell"></a>Installieren von Azure Powershell

1. Führen Sie den folgenden Befehl in Azure PowerShell zum Installieren des `Az.CosmosDB`-Vorschaumoduls aus, das die Befehle enthält, die sich auf die Zeitpunktwiederherstellung beziehen:

   ```azurepowershell
   Install-Module -Name Az.CosmosDB -AllowPrerelease
   ```

1. Melden Sie sich nach der Installation der Module bei Azure an mit

   ```azurepowershell
   Connect-AzAccount
   ```

1. Wählen Sie ein bestimmtes Abonnement mit folgendem Befehl aus:

   ```azurepowershell
   Select-AzSubscription -Subscription <SubscriptionName>
   ```

## <a name="provision-a-sql-api-account-with-continuous-backup"></a><a id="provision-sql-api"></a>Bereitstellen eines SQL-API-Kontos mit fortlaufender Sicherung

Fügen Sie zum Bereitstellen eines Kontos mit fortlaufender Sicherung das Argument `-BackupPolicyType Continuous` zusammen mit dem regulären Bereitstellungsbefehl hinzu.

Das folgende Cmdlet ist ein Beispiel für das Konto `pitracct2` mit einer einzelnen Schreibregion und einer Richtlinie für die fortlaufende Sicherung, das in der Region „USA, Westen“ unter der Ressourcengruppe „myrg“ erstellt wurde:

```azurepowershell

New-AzCosmosDBAccount `
  -ResourceGroupName "myrg" `
  -Location "West US" `
  -BackupPolicyType Continuous `
  -Name "pitracct2" `
  -ApiKind "Sql"
      
```

## <a name="provision-a-mongodb-api-account-with-continuous-backup"></a><a id="provision-mongodb-api"></a>Bereitstellen eines MongoDB-API-Kontos mit fortlaufender Sicherung

Das folgende Cmdlet ist ein Beispiel für das fortlaufende Sicherungskonto „pitracct2“, das in der Region „USA, Westen“ unter der Ressourcengruppe „myrg“ erstellt wurde:

```azurepowershell

New-AzCosmosDBAccount `
  -ResourceGroupName "myrg" `
  -Location "West US" `
  -BackupPolicyType Continuous `
  -Name "pitracct3" `
  -ApiKind "MongoDB" `
  -ServerVersion "3.6"

```

## <a name="trigger-a-restore-operation"></a><a id="trigger-restore"></a>Auslösen eines Wiederherstellungsvorgangs

Das folgende Cmdlet ist ein Beispiel für das Auslösen eines Wiederherstellungsvorgangs mit dem Befehl „restore“, wobei das Zielkonto, das Quellkonto, der Standort, die Ressourcengruppe und der Zeitstempel verwendet werden:

```azurepowershell

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName <resourceGroupName> `
  -TargetDatabaseAccountName <restored-account-name> `
  -SourceDatabaseAccountName <sourceDatabaseAccountName> `
  -RestoreTimestampInUtc <UTC time> `
  -Location <Azure Region Name>

```

**Beispiel 1:** Wiederherstellen des gesamten Kontos:

```azurepowershell

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName "rg" `
  -TargetDatabaseAccountName "pitrbb-ps-1" `
  -SourceDatabaseAccountName "source-sql" `
  -RestoreTimestampInUtc "2021-01-05T22:06:00" `
  -Location "West US"

```

**Beispiel 2:** Wiederherstellen bestimmter Sammlungen und Datenbanken. In diesem Beispiel werden die Sammlungen „myCol1“, „myCol2“ aus „myDB1“ und die gesamte Datenbank „myDB2“ wiederhergestellt, die alle Container darunter enthält.

```azurepowershell
$datatabaseToRestore1 = New-AzCosmosDBDatabaseToRestore -DatabaseName "myDB1" -CollectionName "myCol1", "myCol2"
$datatabaseToRestore2 = New-AzCosmosDBDatabaseToRestore -DatabaseName "myDB2"

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName "rg" `
  -TargetDatabaseAccountName "pitrbb-ps-1" `
  -SourceDatabaseAccountName "source-sql" `
  -RestoreTimestampInUtc "2021-01-05T22:06:00" `
  -DatabasesToRestore $datatabaseToRestore1, $datatabaseToRestore2 `
  -Location "West US"

```

## <a name="enumerate-restorable-resources-for-sql-api"></a><a id="enumerate-sql-api"></a>Aufzählen von wiederherstellbaren Ressourcen für die SQL-API

Mithilfe von Enumerations-Cmdlets können Sie die Ressourcen ermitteln, die für die Wiederherstellung zu verschiedenen Zeitstempeln zur Verfügung stehen. Außerdem bieten sie einen Feed von Schlüsselereignissen für die wiederherstellbaren Konto-, Datenbank- und Containerressourcen.

**Auflisten aller Konten, die im aktuellen Abonnement wiederhergestellt werden können**

Führen Sie den PowerShell-Befehl `Get-AzCosmosDBRestorableDatabaseAccount` zum Auflisten aller Konten aus, die im aktuellen Abonnement wiederhergestellt werden können.

Die Antwort enthält alle Datenbankkonten (sowohl aktive als auch gelöschte), die wiederhergestellt werden können, sowie die Regionen, aus denen sie wiederhergestellt werden können.

```console
{
    "accountName": "sampleaccount",
    "apiType": "Sql",
    "creationTime": "2020-08-08T01:04:52.070190+00:00",
    "deletionTime": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/23e99a35-cd36-4df4-9614-f767a03b9995",
    "identity": null,
    "location": "West US",
    "name": "23e99a35-cd36-4df4-9614-f767a03b9995",
    "restorableLocations": [
      {
        "creationTime": "2020-08-08T01:04:52.945185+00:00",
        "deletionTime": null,
        "locationName": "West US",
        "regionalDatabaseAccountInstanceId": "30701557-ecf8-43ce-8810-2c8be01dccf9"
      },
      {
        "creationTime": "2020-08-08T01:15:43.507795+00:00",
        "deletionTime": null,
        "locationName": "East US",
        "regionalDatabaseAccountInstanceId": "8283b088-b67d-4975-bfbe-0705e3e7a599"
      }
    ],
    "tags": null,
    "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts"
  },
```

Genauso wie bei „CreationTime“ oder „DeletionTime“ für das Konto gibt es auch für die Region Werte für „CreationTime“ und „DeletionTime“. Diese Zeiten ermöglichen es Ihnen, die richtige Region und einen gültigen Zeitbereich für die Wiederherstellung in dieser Region auszuwählen.

**Auflisten aller Versionen von SQL-Datenbanken in einem aktiven Datenbankkonto**

Durch das Auflisten aller Versionen von Datenbanken können Sie die richtige Datenbank in einem Szenario auswählen, in dem die tatsächliche Zeit, in der die Datenbank vorhanden war, unbekannt ist.

Führen Sie den folgenden PowerShell-Befehl aus, um alle Versionen von Datenbanken aufzulisten. Dieser Befehl funktioniert nur bei aktiven Konten. Die Parameter „DatabaseAccountInstanceId“ und „LocationName“ werden aus der Antwort des Cmdlets `Get-AzCosmosDBRestorableDatabaseAccount` von den Eigenschaften „name“ und „location“ abgeleitet. Das Attribut „DatabaseAccountInstanceId“ verweist auf die Eigenschaft "instanceId" des Quelldatenbankkontos, das gerade wiederhergestellt wird:


```azurepowershell

Get-AzCosmosdbSqlRestorableDatabase `
  -LocationName "East US" `
  -DatabaseAccountInstanceId <DatabaseAccountInstanceId>

```

**Auflisten aller Versionen von SQL-Containern einer Datenbank in einem aktiven Datenbankkonto.**

Mit dem folgenden Befehl können Sie alle Versionen von SQL-Containern auflisten. Dieser Befehl funktioniert nur bei aktiven Konten. Der Parameter „databaseRid“ ist die „ResourceId“ der Datenbank, die Sie wiederherstellen möchten. Dies ist der Wert des Attributs „ownerResourceid“ in der Antwort des Cmdlets `Get-AzCosmosdbSqlRestorableDatabase`. Die Antwort enthält auch eine Liste der Vorgänge, die für alle Container in dieser Datenbank ausgeführt wurden.

```azurepowershell

Get-AzCosmosdbSqlRestorableContainer `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -DatabaseRid "AoQ13r==" `
  -LocationName "West US"

```

**Suchen von Datenbanken oder Containern, die zu jedem beliebigen Zeitstempel wiederhergestellt werden können**

Verwenden Sie den folgenden Befehl zum Abrufen der Liste von Datenbanken oder Containern, die zu jedem beliebigen Zeitstempel wiederhergestellt werden können. Dieser Befehl funktioniert nur bei aktiven Konten.

```azurepowershell

Get-AzCosmosdbSqlRestorableResource `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -LocationName "West US" `
  -RestoreLocation "East US" `
  -RestoreTimestamp "2020-07-20T16:09:53+0000"

```

## <a name="enumerate-restorable-resources-for-mongodb"></a><a id="enumerate-mongodb-api"></a>Aufzählen von wiederherstellbaren Ressourcen für MongoDB

Mithilfe der unten beschriebenen Enumerationsbefehle können Sie die Ressourcen ermitteln, die für die Wiederherstellung zu verschiedenen Zeitstempeln zur Verfügung stehen. Außerdem bieten sie einen Feed von Schlüsselereignissen für die wiederherstellbaren Konto-, Datenbank- und Containerressourcen. Diese Befehle funktionieren nur bei aktiven Konten und ähneln SQL-API-Befehlen, aber mit „MongoDB“ statt „sql“ im Befehlsnamen.

**Auflisten aller Versionen von MongoDB-Datenbanken in einem aktiven Datenbankkonto**

```azurepowershell

Get-AzCosmosdbMongoDBRestorableDatabase `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -LocationName "West US"

```

**Auflisten aller Versionen von MongoDB-Sammlungen einer Datenbank in einem aktiven Datenbankkonto**

```azurepowershell

Get-AzCosmosdbMongoDBRestorableCollection `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -DatabaseRid "AoQ13r==" `
  -LocationName "West US"
```

**Auflisten aller Ressourcen eines MongoDB-Datenbankkontos, die zu einem bestimmten Zeitstempel und in einer bestimmten Region wiederhergestellt werden können**

```azurepowershell

Get-AzCosmosdbMongoDBRestorableResource `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -LocationName "West US" `
  -RestoreLocation "West US" `
  -RestoreTimestamp "2020-07-20T16:09:53+0000"
```

## <a name="next-steps"></a>Nächste Schritte

* Konfigurieren und verwalten Sie die fortlaufende Sicherung mithilfe von [Azure CLI](continuous-backup-restore-command-line.md), [Azure Resource Manager](continuous-backup-restore-template.md) oder über das [Azure-Portal](continuous-backup-restore-portal.md).
* [Ressourcenmodell des fortlaufenden Sicherungsmodus](continuous-backup-restore-resource-model.md)
* [Verwalten Sie Berechtigungen](continuous-backup-restore-permissions.md), die zum Wiederherstellen von Daten mit dem fortlaufenden Sicherungsmodus erforderlich sind.
