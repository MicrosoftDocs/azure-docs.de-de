---
title: Vorläufiges Löschen für Container (Vorschau)
titleSuffix: Azure Storage
description: Vorläufiges Löschen für Container (Vorschau) schützt Ihre Daten, damit Sie sie leichter wiederherstellen können, wenn sie irrtümlich von einer Anwendung oder einem anderen Speicherkontobenutzer geändert oder gelöscht wurden.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/25/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 1ebfa137357e60a1f462ef90c6430cbf0cb5b968
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746585"
---
# <a name="soft-delete-for-containers-preview"></a>Vorläufiges Löschen für Container (Vorschau)

Vorläufiges Löschen für Container (Vorschau) schützt Ihre Daten vor einem versehentlichen oder irrtümlichen Ändern oder Löschen. Wenn das vorläufige Löschen von Containern für ein Speicherkonto aktiviert ist, werden alle gelöschten Container und deren Inhalte in Azure Storage für den von Ihnen festgelegten Zeitraum aufbewahrt. Während dieses Aufbewahrungszeitraums können Sie zuvor gelöschte Container und alle darin enthaltenen Blobs wiederherstellen.

Microsoft empfiehlt die Aktivierung der folgenden Datenschutzfeatures, um den vollständigen Schutz für Ihre Blobdaten zu gewährleisten:

- Vorläufiges Löschen von Containern zum Schutz vor versehentlichem Löschen oder Überschreiben eines Containers. Informationen zum Aktivieren des vorläufigen Löschens für Container finden Sie unter [Aktivieren und Verwalten des vorläufigen Löschens für Container (Vorschau)](soft-delete-container-enable.md).
- Vorläufiges Löschen von Blobs zum Schutz vor versehentlichem Löschen oder Überschreiben eines einzelnen Blobs. Informationen zum Aktivieren des vorläufigen Löschens von Blobs finden Sie unter [Vorläufiges Löschen für Blobs](soft-delete-blob-overview.md).
- Blobversionsverwaltung zum automatischen Verwalten früherer Versionen eines Blobs. Wenn Blobversionsverwaltung aktiviert ist, können Sie eine frühere Version eines Blobs wiederherstellen, um Daten wiederherzustellen, wenn diese irrtümlich geändert oder gelöscht wurden. Informationen zum Aktivieren der Blobversionsverwaltung finden Sie unter [Aktivieren und Verwalten der Blobversionsverwaltung (Vorschau)](versioning-enable.md).

> [!WARNING]
> Das Löschen eines Speicherkontos kann nicht rückgängig gemacht werden. Vorläufiges Löschen schützt nicht vor dem Löschen eines Speicherkontos. Konfigurieren Sie eine **CannotDelete** -Sperre für die Speicherkontoressource, um ein versehentliches Löschen des Speicherkontos zu verhindern. Weitere Informationen zum Sperren von Azure-Ressourcen finden Sie unter [Sperren von Ressourcen, um unerwartete Änderungen zu verhindern](../../azure-resource-manager/management/lock-resources.md).

## <a name="how-container-soft-delete-works"></a>Funktionsweise des vorläufigen Löschens von Containern

Wenn Sie das vorläufige Löschen von Containern aktivieren, können Sie einen Aufbewahrungszeitraum für gelöschte Container zwischen 1 und 365 Tagen angeben. Der Standardaufbewahrungszeitraum beträgt sieben Tage. Während des Aufbewahrungszeitraums können Sie einen gelöschten Container wiederherstellen, indem Sie den Vorgang **Undelete Container** (Container wiederherstellen) aufrufen.

Bei der Wiederherstellung eines Containers können Sie diesen mit dem ursprünglichen Namen wiederherstellen, wenn dieser Name nicht wiederverwendet wurde. Wenn der ursprüngliche Containername bereits verwendet wird, können Sie den Container mit einem neuen Namen wiederherstellen.

Nach Ablauf des Aufbewahrungszeitraums wird der Container endgültig aus Azure Storage gelöscht und kann nicht wiederhergestellt werden. Der Aufbewahrungszeitraum beginnt zum Zeitpunkt der Löschung des Containers. Sie können den Aufbewahrungszeitraum jederzeit ändern. Denken Sie jedoch daran, dass ein aktualisierter Aufbewahrungszeitraum nur für neu gelöschte Container gilt. Zuvor gelöschte Container werden basierend auf dem Aufbewahrungszeitraum endgültig gelöscht, der zum Zeitpunkt der Löschung dieser Container festgelegt war.

Das Deaktivieren des vorläufigen Löschens von Containern führt nicht zur endgültigen Löschung der Container, die zuvor vorläufig gelöscht wurden. Vorläufig gelöschte Container werden nach Ablauf des Aufbewahrungszeitraums endgültig gelöscht, der zum Zeitpunkt der Löschung dieser Container festgelegt war.

## <a name="about-the-preview"></a>Informationen zur Vorschau

Das vorläufige Löschen von Containern ist in allen öffentlichen Azure-Regionen in der Vorschauversion verfügbar.

> [!IMPORTANT]
> Die Vorschauversion des vorläufigen Löschens für Container ist nur für die Verwendung außerhalb der Produktion bestimmt. Produktions-SLAs (Service Level Agreements, Vereinbarungen zum Servicelevel) sind derzeit nicht verfügbar.

Version 2019-12-12 und höher der Azure Storage-REST-API unterstützt das vorläufige Löschen von Containern.

### <a name="storage-account-support"></a>Speicherkontounterstützung

Das vorläufige Löschen von Containern ist für die folgenden Typen von Speicherkonten verfügbar:

- Speicherkonten vom Typ „Universell V2“
- Blockblob-Speicherkonten
- Blob-Speicherkonten

Wenn Ihr Speicherkonto vom Typ „Universell V1“ ist, verwenden Sie das Azure-Portal, um ein Upgrade auf ein Konto vom Typ „Universell V2“ durchzuführen. Weitere Informationen zu Speicherkonten finden Sie unter [Azure-Speicherkonto – Übersicht](../common/storage-account-overview.md).

Speicherkonten mit einem hierarchischen Namespace, die für die Verwendung mit Azure Data Lake Storage Gen2 aktiviert sind, werden ebenfalls unterstützt.

### <a name="register-for-the-preview"></a>Registrieren für die Vorschau

Registrieren Sie sich für die Vorschauversion des vorläufigen Löschens von Containern, indem Sie über PowerShell oder die Azure CLI eine Anforderung zum Registrieren des Features für Ihr Abonnement senden. Wenn die Anforderung genehmigt wurde, können Sie das vorläufige Löschen von Containern mit allen neuen oder bereits vorhandenen Speicherkonten vom Typ „Universell v2“ sowie mit Blob Storage-Konten oder Blockblob-Speicherkonten mit dem Tarif „Premium“ aktivieren.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Rufen Sie für die Registrierung mit PowerShell den Befehl [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) auf.

```powershell
# Register for container soft delete (preview)
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName ContainerSoftDelete

# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Zum Registrieren bei Azure CLI rufen Sie den Befehl „[az feature register](/cli/azure/feature#az-feature-register)“ auf.

```azurecli
az feature register --namespace Microsoft.Storage --name ContainerSoftDelete
az provider register --namespace 'Microsoft.Storage'
```

---

### <a name="check-the-status-of-your-registration"></a>Überprüfen des Registrierungsstatus

Verwenden Sie PowerShell oder die Azure CLI, um den Status Ihrer Registrierung zu überprüfen.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Um den Status Ihrer Registrierung mit PowerShell zu überprüfen, rufen Sie den Befehl [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) auf.

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName ContainerSoftDelete
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um den Status Ihrer Registrierung über die Azure-Befehlszeilenschnittstelle zu überprüfen, rufen Sie den Befehl [az feature](/cli/azure/feature#az-feature-show) auf.

```azurecli
az feature show --namespace Microsoft.Storage --name ContainerSoftDelete
```

---

## <a name="pricing-and-billing"></a>Preise und Abrechnung

Für das Aktivieren des vorläufigen Löschens von Containern fallen keine zusätzlichen Kosten an. Daten in vorläufig gelöschten Containern werden zum selben Tarif wie aktive Daten in Rechnung gestellt.

## <a name="next-steps"></a>Nächste Schritte

- [Aktivieren und Verwalten des vorläufigen Löschens für Container (Vorschau)](soft-delete-container-enable.md)
- [Vorläufiges Löschen für Blobs](soft-delete-blob-overview.md)
- [Blobversionsverwaltung](versioning-overview.md)
