---
title: Häufig gestellte Fragen zum Feature Zeitpunktwiederherstellung von Azure Cosmos DB
description: Dieser Artikel enthält häufig gestellte Fragen zum Feature „Zeitpunktwiederherstellung“ von Azure Cosmos DB, das über den Modus „Fortlaufende Sicherung“ genutzt werden kann.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: c0af1db12f3ade2945524f48e4539d2d2e9aa6b9
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539179"
---
# <a name="frequently-asked-questions-on-the-azure-cosmos-db-point-in-time-restore-feature-preview"></a>Häufig gestellte Fragen zum Feature Zeitpunktwiederherstellung von Azure Cosmos DB (Vorschau)
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Das Feature der Zeitpunktwiederherstellung (fortlaufender Sicherungsmodus) für Azure Cosmos DB befindet sich zurzeit in der öffentlichen Vorschauphase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Dieser Artikel enthält häufig gestellte Fragen zum Feature Zeitpunktwiederherstellung (Vorschauversion) von Azure Cosmos DB, das über den Modus „Fortlaufende Sicherung“ genutzt werden kann.

## <a name="how-much-time-does-it-takes-to-restore"></a>Wie lange dauert eine Wiederherstellung?
Die Dauer der Wiederherstellung hängt von der Größe Ihrer Daten ab.

### <a name="can-i-submit-the-restore-time-in-local-time"></a>Kann ich die Wiederherstellungszeit in meiner Ortszeit übermitteln?
Die Wiederherstellung kann möglicherweise nicht durchgeführt werden, wenn die Schlüsselressourcen wie Datenbanken oder Container zu diesem Zeitpunkt nicht vorhanden waren. Sie können dies überprüfen, indem Sie die Zeit eingeben und sich die ausgewählte Datenbank oder Container zu diesem Zeitpunkt ansehen. Wenn keine Ressourcen für die Wiederherstellung vorhanden sind, funktioniert der Wiederherstellungsvorgang nicht.

### <a name="how-can-i-track-if-an-account-is-being-restored"></a>Wie kann ich nachverfolgen, ob ein Konto wiederhergestellt wird?
Nachdem Sie den Wiederherstellungsbefehl übermittelt haben, können Sie auf der Seite verbleiben. Nach Abschluss des Vorgangs wird auf der Statusleiste eine Meldung über das erfolgreich wiederhergestellte Konto angezeigt. Sie können auch nach dem wiederhergestellten Konto suchen und den [Status des wiederhergestellten Kontos](continuous-backup-restore-portal.md#track-restore-status) nachverfolgen. Während die Wiederherstellung ausgeführt wird, lautet der Status des Kontos „Wird erstellt“. Nach Abschluss des Wiederherstellungsvorgangs wird der Kontostatus in „Online“ geändert.

Sie können mit PowerShell und der Befehlszeilenschnittstelle auf ähnliche Weise den Fortschritt des Wiederherstellungsvorgangs nachverfolgen, indem Sie den Befehl `az cosmosdb show` wie folgt ausführen:

```azurecli-interactive
az cosmosdb show --name "accountName" --resource-group "resourceGroup"
```

Der Wert von provisioningState lautet „Succeeded“ (Erfolgreich), wenn das Konto online ist.

```json
{
"virtualNetworkRules": [],
"writeLocations" : [
{
    "documentEndpoint": "https://<accountname>.documents.azure.com:443/", 
    "failoverpriority": 0,
    "id": "accountName" ,
    "isZoneRedundant" : false, 
    "locationName": "West US 2", 
    "provisioningState": "Succeeded"
}
]
}
```

### <a name="how-can-i-find-out-whether-an-account-was-restored-from-another-account"></a>Wie kann ich herausfinden, ob ein Konto aus einem anderen Konto wiederhergestellt wurde?
Führen Sie den Befehl `az cosmosdb show` aus, und überprüfen Sie in der Ausgabe den Wert der `createMode`-Eigenschaft. Wenn der Wert **Restore** (Wiederherstellung) lautet, wurde das Konto aus einem anderen Konto wiederhergestellt. Die `restoreParameters`-Eigenschaft enthält weitere Details, z. B. `restoreSource` mit der ID des Quellkontos. Die letzte GUID im `restoreSource`-Parameter ist die instanceId des Quellkontos.

In der folgenden Ausgabe lautet die Instanz-ID des Quellkontos z. B. „7b4bb-f6a0-430e-ade1-638d781830cc“.

```json
"restoreParameters": {
   "databasesToRestore" : [],
   "restoreMode": "PointInTime",
   "restoreSource": "/subscriptions/2a5b-f6a0-430e-ade1-638d781830dd/providers/Microsoft.DocumentDB/locations/westus/restorableDatabaseAccounts/7b4bb-f6a0-430e-ade1-638d781830cc",
   "restoreTimestampInUtc": "2020-06-11T22:05:09Z"
}
```

### <a name="what-happens-when-i-restore-a-shared-throughput-database-or-a-container-within-a-shared-throughput-database"></a>Was geschieht, wenn ich eine Datenbank mit gemeinsam genutztem Durchsatz oder einen Container in einer Datenbank mit gemeinsam genutztem Durchsatz wiederherstelle?
Die gesamte Datenbank mit gemeinsam genutztem Durchsatz wird wiederhergestellt. Es ist nicht möglich, eine Teilmenge der Container in einer Datenbank mit gemeinsam genutztem Durchsatz für die Wiederherstellung auszuwählen.

### <a name="what-is-the-use-of-instanceid-in-the-account-definition"></a>Wozu dient die Instanz-ID in der Kontodefinition?
Die accountName-Eigenschaft des Azure Cosmos DB-Kontos ist zu jedem Zeitpunkt, zu dem das Konto aktiv ist, global eindeutig. Nachdem das Konto gelöscht wurde, kann jedoch ein anderes Konto mit diesem Namen erstellt werden. Daher reicht accountName nicht mehr aus, um eine Instanz eines Kontos zu identifizieren. 

Die ID (instanceId) ist eine Eigenschaft einer Instanz eines Kontos, die bei der Wiederherstellung zur Unterscheidung zwischen mehreren Konten (aktiv und gelöscht) verwendet wird, wenn diese denselben Namen haben. Die Instanz-ID können Sie durch Ausführen der Befehle `Get-AzCosmosDBRestorableDatabaseAccount` oder `az cosmosdb restorable-database-account` abrufen. Der Wert des name-Attributs ist die instanceId.

## <a name="next-steps"></a>Nächste Schritte

* Was ist der Modus für die [fortlaufende Sicherung](continuous-backup-restore-introduction.md)?
* Konfigurieren und verwalten Sie die fortlaufende Sicherung über das [Azure-Portal](continuous-backup-restore-portal.md) oder aber mithilfe von [PowerShell](continuous-backup-restore-powershell.md), [CLI](continuous-backup-restore-command-line.md) oder [Azure Resource Manager](continuous-backup-restore-template.md).
* [Verwalten Sie Berechtigungen](continuous-backup-restore-permissions.md), die zum Wiederherstellen von Daten mit dem fortlaufenden Sicherungsmodus erforderlich sind.
* [Ressourcenmodell des fortlaufenden Sicherungsmodus](continuous-backup-restore-resource-model.md)