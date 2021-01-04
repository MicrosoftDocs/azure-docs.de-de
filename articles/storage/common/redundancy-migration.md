---
title: Ändern der Replikation eines Speicherkontos
titleSuffix: Azure Storage
description: In diesem Artikel erfahren Sie, wie Sie Art und Weise ändern, in der Daten in einem vorhandenen Speicherkonto repliziert werden.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/24/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 228595bf633ef0545a13abe19308e49da82cf75a
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844011"
---
# <a name="change-how-a-storage-account-is-replicated"></a>Ändern der Replikation eines Speicherkontos

Azure Storage speichert immer mehrere Kopien Ihrer Daten, damit sie vor geplanten und ungeplanten Ereignissen geschützt sind – von vorübergehend auftretenden Hardwarefehlern über Netzwerk- oder Stromausfälle bis hin zu schweren Naturkatastrophen. Durch diese Redundanz wird sichergestellt, dass Ihr Speicherkonto auch bei Ausfällen die [Servicelevelvereinbarung (SLA) für Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/) erfüllt.

Azure Storage bietet die folgenden Replikationstypen:

- Lokal redundanter Speicher (LRS)
- Zonenredundanter Speicher (ZRS)
- Georedundanter Speicher (GRS) oder georedundanter Speicher mit Lesezugriff (RA-GRS)
- Geozonenredundanter Speicher (GZRS) oder geozonenredundanter Speicher mit Lesezugriff (RA-GZRS)

Eine Übersicht über diese Optionen finden Sie unter [Azure Storage-Redundanz](storage-redundancy.md).

## <a name="switch-between-types-of-replication"></a>Wechseln zwischen Replikationstypen

Sie können den Replikationstyp von Speicherkonten ändern, allerdings sind einige Szenarien etwas einfacher als andere. Wenn Sie Georeplikation oder Lesezugriff zur sekundären Region hinzufügen oder daraus entfernen möchten, können Sie das Azure-Portal, PowerShell oder die Azure CLI verwenden, um die Replikationseinstellung zu ändern. Wenn Sie jedoch durch Wechseln von LRS zu ZRS oder umgekehrt die Art und Weise ändern möchten, in der Daten in der primären Region repliziert werden, müssen Sie eine manuelle Migration ausführen.

Die folgende Tabelle bietet eine Übersicht über die Möglichkeiten zum Wechseln zwischen den Replikationstypen:

| Wechsel | … zu LRS | … zu GRS/RA-GRS | … zu ZRS | … zu GZRS/RA-GZRS |
|--------------------|----------------------------------------------------|---------------------------------------------------------------------|----------------------------------------------------|---------------------------------------------------------------------|
| <b>… von LRS</b> | – | Verwenden von Azure-Portal, PowerShell oder CLI zum Ändern der Replikationseinstellung<sup>1</sup> | Ausführen einer manuellen Migration <br /><br /> oder <br /><br /> Anfordern einer Livemigration | Ausführen einer manuellen Migration <br /><br /> oder <br /><br /> Zuerst Wechseln zu GRS/RA-GRS und dann Anfordern einer Livemigration<sup>1</sup> |
| <b>… von GRS/RA-GRS</b> | Verwenden von Azure-Portal, PowerShell oder CLI zum Ändern der Replikationseinstellung | – | Ausführen einer manuellen Migration <br /><br /> oder <br /><br /> Zuerst Wechseln zu LRS und dann Anfordern einer Livemigration | Ausführen einer manuellen Migration <br /><br /> oder <br /><br /> Anfordern einer Livemigration |
| <b>… von ZRS</b> | Ausführen einer manuellen Migration | Ausführen einer manuellen Migration | – | Verwenden von Azure-Portal, PowerShell oder CLI zum Ändern der Replikationseinstellung<sup>1,2</sup> |
| <b>… von GZRS/RA-GZRS</b> | Ausführen einer manuellen Migration | Ausführen einer manuellen Migration | Verwenden von Azure-Portal, PowerShell oder CLI zum Ändern der Replikationseinstellung | – |

<sup>1</sup> Hierbei fällt eine einmalige Gebühr für ausgehende Daten an.<br />
<sup>2</sup> Konvertierung aus ZRS in GZRS/RA-GZRS oder umgekehrt wird in den folgenden Regionen nicht unterstützt: „USA, Osten 2“, „USA, Osten“, „Europa, Westen“.

> [!CAUTION]
> Wenn Sie ein [Kontofailover](storage-disaster-recovery-guidance.md) für Ihr (RA-)GRS- oder (RA-)GZRS-Konto durchgeführt haben, ist das Konto in der neuen primären Region nach dem Failover lokal redundant. Livemigration zu ZRS oder GZRS für ein LRS-Konto, das sich aus einem Failover ergibt, wird nicht unterstützt. Dies gilt sogar für sogenannte Failbackvorgänge. Wenn Sie z. B. ein Kontofailover von RA-GZRS zum LRS-Konto in der sekundären Region durchführen und das Konto dann erneut für RA-GRS konfigurieren und ein weiteres Kontofailover zur ursprünglichen primären Region durchführen, können Sie sich nicht an den Support wenden, um die ursprüngliche Livemigration zu RA-GZRS in der primären Region durchzuführen. Stattdessen müssen Sie eine manuelle Migration zu ZRS oder GZRS durchführen.

## <a name="change-the-replication-setting"></a>Ändern der Replikationseinstellung

Sie können das Azure-Portal, PowerShell oder die Azure CLI verwenden, um die Replikationseinstellung für ein Speicherkonto zu ändern, sofern Sie nicht die Art und Weise verändern, in der Daten in der primären Region repliziert werden. Wenn Sie von LRS in der primären Region zu ZRS in der primären Region oder umgekehrt migrieren, müssen Sie entweder eine manuelle Migration oder eine Livemigration durchführen.

Durch die Änderung des Replikationstyps für Ihr Speicherkonto ergeben sich keine Ausfallzeiten für Ihre Anwendungen.

# <a name="portal"></a>[Portal](#tab/portal)

Um die Redundanzoption für Ihr Speicherkonto im Azure-Portal zu ändern, führen Sie die folgenden Schritte aus:

1. Navigieren Sie zum Speicherkonto im Azure-Portal.
1. Wählen Sie die Einstellung **Konfiguration** aus.
1. Aktualisieren Sie die Einstellung **Replikation**.

![Screenshot zur Änderung der Replikationsoption im Portal](media/redundancy-migration/change-replication-option.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Um die Redundanzoption für Ihr Speicherkonto mit PowerShell zu ändern, führen Sie den Befehl [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) aus, und geben Sie den Parameter `-SkuName` an:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage_account> `
    -SkuName <sku>
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um die Redundanzoption für Ihr Speicherkonto mit der Azure CLI zu ändern, führen Sie den Befehl [az storage account update](/cli/azure/storage/account#az-storage-account-update) aus, und geben Sie den Parameter `--sku` an:

```azurecli-interactive
az storage account update \
    --name <storage-account>
    --resource-group <resource_group> \
    --sku <sku>
```

---

## <a name="perform-a-manual-migration-to-zrs-gzrs-or-ra-gzrs"></a>Ausführen einer manuellen Migration zu ZRS, GZRS oder RA-GZRS

Wenn Sie durch Wechseln von LRS zu ZRS oder umgekehrt die Art und Weise ändern möchten, in der Daten in Ihrem Speicherkonto in der primären Region repliziert werden, können Sie eine manuelle Migration ausführen. Eine manuelle Migration bietet mehr Flexibilität als eine Livemigration. Bei einer manuellen Migration können Sie den zeitlichen Ablauf steuern. Verwenden Sie diese Option also, wenn die Migration an einem bestimmten Datum abgeschlossen sein soll.

Wenn Sie eine manuelle Migration von LRS zu ZRS oder umgekehrt in der primären Region durchführen, kann das Zielspeicherkonto georedundant und auch für den Lesezugriff auf die sekundäre Region konfiguriert sein. Sie können beispielsweise ein LRS-Konto in einem Schritt zu einem GZRS- oder RA-GZRS-Konto migrieren.

Eine manuelle Migration kann zu Ausfallzeiten der Anwendung führen. Wenn Ihre Anwendung eine hohe Verfügbarkeit erfordert, bietet Microsoft auch eine Option für eine Livemigration. Eine Livemigration ist eine direkte Migration ohne Ausfallzeiten.

Bei einer manuellen Migration kopieren Sie die Daten aus Ihrem vorhandenen Speicherkonto in ein neues Speicherkonto, das ZRS in der primären Region verwendet. Zum Ausführen einer manuellen Migration können Sie eine der folgenden Optionen verwenden:

- Kopieren Sie Daten mithilfe eines vorhandenen Tools wie z. B. AzCopy, einer der Azure Storage-Clientbibliotheken oder zuverlässigen Drittanbietertools.
- Wenn Sie mit Hadoop oder HDInsight vertraut sind, können Sie sowohl das Quell- als auch das Zielspeicherkonto an Ihren Cluster anfügen. Danach parallelisieren Sie den Datenkopiervorgang mit einem Tool wie z.B. DistCp.

## <a name="request-a-live-migration-to-zrs-gzrs-or-ra-gzrs"></a>Anfordern einer Livemigration zu ZRS, GZRS oder RA-GZRS

Wenn Sie Ihr Speicherkonto von LRS zu ZRS in der primären Region migrieren müssen, ohne dass dadurch Ausfallzeiten entstehen, können Sie eine Livemigration bei Microsoft anfordern. Um von LRS zu GZRS oder RA-GZRS zu migrieren, wechseln Sie zunächst zu GRS oder RA-GRS, und fordern Sie dann eine Livemigration an. Auf gleiche Weise können Sie eine Livemigration von GRS oder RA-GRS zu GZRS oder RA-GZRS anfordern. Um von GRS oder RA-GRS zu ZRS zu migrieren, wechseln Sie zunächst zu LRS, und fordern Sie dann eine Livemigration an.

Während einer Livemigration können Sie ohne jegliche Verluste hinsichtlich Dauerhaftigkeit oder Verfügbarkeit auf die Daten in Ihrem Speicherkonto zugreifen. Die Azure Storage-SLA wird während des Migrationsprozesses aufrechterhalten. Bei einer Livemigration treten keine Datenverluste auf. Dienstendpunkte, Zugriffsschlüssel, Shared Access Signatures und andere Kontooptionen sind auch nach der Migration unverändert.

ZRS unterstützt nur Konten vom Typ „Allgemein v2“. Bevor Sie also eine Anforderung für eine Livemigration zu ZRS übermitteln, führen Sie ein Upgrade Ihres Speicherkontos auf „Allgemein v2“ durch. Weitere Informationen finden Sie unter [Durchführen eines Upgrades auf ein Speicherkonto vom Typ „Allgemein v2“](storage-account-upgrade.md). Ein Speicherkonto muss Daten enthalten, damit eine Livemigration durchgeführt werden kann.

Die Livemigration wird nur für Speicherkonten unterstützt, die LRS- oder GRS-Replikation nutzen. Wenn Ihr Konto RA-GRS verwendet, müssen Sie zuerst den Replikationstyp Ihres Kontos auf LRS oder GRS ändern, bevor Sie fortfahren. Dieser Zwischenschritt entfernt vor der Migration den sekundären schreibgeschützten Endpunkt, der von RA-GRS bereitgestellt wird.

Auch wenn Microsoft auf Ihre Anforderung zur Livemigration sofort reagiert, gibt es keine Garantie, zu welchem Zeitpunkt eine Livemigration abgeschlossen sein wird. Wenn Ihre Daten bis zu einem bestimmten Datum zu ZRS migriert werden müssen, empfiehlt Microsoft die Durchführung einer manuellen Migration. Grundsätzlich gilt: Je mehr Daten in Ihrem Konto vorhanden sind, desto länger dauert die Migration dieser Daten.

In folgenden Fällen müssen Sie eine manuelle Migration ausführen:

- Sie möchten Ihre Daten zu einem ZRS-Speicherkonto migrieren, das sich in einer anderen Region als das Quellkonto befindet.
- Ihr Speicherkonto ist ein Seitenblob- oder Blockblobkonto in einem Premium-Tarif.
- Sie möchten Daten von ZRS zu LRS, GRS oder RA-GRS migrieren.
- Ihr Speicherkonto enthält Daten auf Archivebene.

Sie können eine Livemigration über das [Azure-Support-Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) anfordern. Wählen Sie im Portal das Speicherkonto aus, das Sie in ZRS konvertieren möchten.

1. Wählen Sie **Neue Supportanfrage** aus.
2. Geben Sie die **Grundlagen** basierend auf den Informationen Ihres Kontos an: 
    - **Problemtyp**: Wählen Sie **Technisch** aus.
    - **Dienst**: Wählen Sie **Meine Dienste** und **Speicherkontenverwaltung** aus.
    - **Ressource:** Wählen Sie die Ressource aus, die Sie zu ZRS konvertieren möchten.
3. Wählen Sie **Weiter** aus.
4. Geben Sie im Abschnitt **Problem** die folgenden Werte an:
    - **Schweregrad**: Behalten Sie den Standardwert bei.
    - **Problemtyp**: Wählen Sie **Datenmigration** aus.
    - **Kategorie:** Wählen Sie **Zu ZRS migrieren** aus.
    - **Titel**: Geben Sie einen aussagekräftigen Titel ein, z.B. **ZRS-Kontomigration**.
    - **Details**: Geben Sie im Feld **Details** weitere Informationen ein, z.B. „Ich möchte von [LRS, GRS] in der Region „\_\_“ zu ZRS migrieren“.
5. Wählen Sie **Weiter** aus.
6. Überprüfen Sie, ob die Kontaktinformationen auf dem Blatt **Kontaktinformationen** korrekt sind.
7. Klicken Sie auf **Erstellen**.

Ein Supportmitarbeiter wird sich mit Ihnen in Verbindung setzen und Sie nach Bedarf und Wunsch unterstützen.

> [!NOTE]
> Premium-Dateifreigaben (FileStorage-Konten) sind nur für LRS und ZRS verfügbar.
>
> GZRS-Speicherkonten unterstützen die Archivebene derzeit nicht. Unter [Azure Blob Storage: Zugriffsebenen „Heiß“, „Kalt“ und „Archiv“](../blobs/storage-blob-storage-tiers.md) finden Sie weitere Details.
>
> Verwaltete Datenträger sind nur für LRS verfügbar und können nicht zu ZRS migriert werden. Sie können Momentaufnahmen und Images für verwaltete SSD Standard-Datenträger in einem HDD Standard-Speicher speichern und [zwischen LRS- und ZRS-Optionen wählen](https://azure.microsoft.com/pricing/details/managed-disks/). Informationen zur Integration in Verfügbarkeitsgruppen finden Sie unter [Einführung in verwaltete Azure-Datenträger](../../virtual-machines/managed-disks-overview.md#integration-with-availability-sets).

## <a name="switch-from-zrs-classic"></a>Wechsel von ZRS (klassisch)

> [!IMPORTANT]
> Microsoft wird ZRS Classic-Konten am 31. März 2021 als veraltet markieren und migrieren. Bevor die Option als veraltet gekennzeichnet wird, erhalten ZRS Classic-Kunden weitere Informationen.
>
> Sobald ZRS in einer Region allgemein verfügbar wird, können Kunden über das Azure-Portal in dieser Region keine klassischen ZRS-Konten mehr erstellen. Die Verwendung von Microsoft PowerShell und der Azure CLI zum Erstellen von ZRS Classic-Konten ist eine Option, bis ZRS Classic als veraltet gekennzeichnet wird. Informationen zur Verfügbarkeit von ZRS finden Sie unter [Azure Storage-Redundanz](storage-redundancy.md).

ZRS Classic repliziert Daten asynchron zwischen Rechenzentren in einer oder zwei Regionen. Replizierte Daten sind möglicherweise erst wieder verfügbar, wenn Microsoft das Failover auf die sekundäre Region initiiert. Ein ZRS Classic-Konto kann nicht zu oder aus LRS, GRS oder RA-GRS konvertiert werden. ZRS Classic-Konten unterstützen zudem weder Metriken noch Protokollierungen.

ZRS Classic ist nur für **Blockblobs** in Speicherkonten vom Typ „Allgemein v1 (GPv1)“ verfügbar. Weitere Informationen zu Speicherkonten finden Sie unter [Azure-Speicherkonto – Übersicht](storage-account-overview.md).

Um ZRS-Kontodaten manuell zu oder von einem LRS-, GRS-, RA-GRS- oder klassischen ZRS-Konto zu migrieren, verwenden Sie eins der folgenden Tools: AzCopy, Azure Storage-Explorer, PowerShell oder Azure CLI. Sie können auch Ihre eigene Migrationslösung mit einer der Azure Storage-Clientbibliotheken erstellen.

Sie können in den Regionen, in denen ZRS verfügbar ist, auch ein Upgrade Ihres klassischen ZRS-Speicherkontos auf ZRS durchführen. Verwenden Sie dafür das Azure-Portal, PowerShell oder die Azure CLI.

# <a name="portal"></a>[Portal](#tab/portal)

Um im Azure-Portal ein Upgrade auf ZRS durchzuführen, navigieren Sie zu den **Konfigurationseinstellungen** des Kontos, und wählen Sie **Upgrade** aus:

![Upgrade von ZRS (klassisch) zu ZRS im Portal](media/redundancy-migration/portal-zrs-classic-upgrade.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Führen Sie folgenden Befehl aus, um mithilfe von PowerShell ein Upgrade auf ZRS durchzuführen:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> -AccountName <storage_account> -UpgradeToStorageV2
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Führen Sie folgenden Befehl aus, um mithilfe der Azure CLI ein Upgrade auf ZRS durchzuführen:

```cli
az storage account update -g <resource_group> -n <storage_account> --set kind=StorageV2
```

---

## <a name="costs-associated-with-changing-how-data-is-replicated"></a>Kosten in Zusammenhang mit einer Änderung der Datenreplikation

Die Kosten für eine Änderung der Datenreplikation richten sich nach der Quell- und der Zieloption für den Wechsel. Die Reihenfolge der Azure Storage-Redundanzangebote vom kostengünstigsten bis zum kostenintensivsten: LRS, ZRS, GRS, RA-GRS, GZRS und RA-GZRS.

Beispielsweise fallen beim Wechsel *von* LRS zu einem beliebigen anderen Replikationstyp zusätzliche Kosten an, da Sie in eine komplexere Redundanzebene wechseln. Beim Wechsel *zu* GRS oder RA-GRS fallen Kosten für eine ausgehende Bandbreite an, da Ihre Daten (in Ihrer primären Region) in Ihre sekundäre Remoteregion repliziert werden. Dies sind einmalige Kosten bei der ersten Einrichtung. Nachdem die Daten kopiert sind, fallen keine weiteren Kosten für die Migration an. Ausführliche Informationen zu Bandbreitengebühren finden Sie auf der [Seite mit Informationen zu Azure Storage-Preisen](https://azure.microsoft.com/pricing/details/storage/blobs/).

Wenn Sie Ihr Speicherkonto von GRS zu LRS migrieren, entstehen keine zusätzlichen Kosten, aber Ihre replizierten Daten werden am sekundären Standort gelöscht.

> [!IMPORTANT]
> Wenn Sie Ihr Speicherkonto von RA-GRS zu GRS oder LRS migrieren, wird das Konto nach dem Umstellungsdatum für weitere 30 Tage als RA-GRS abgerechnet.

## <a name="see-also"></a>Weitere Informationen

- [Azure-Speicherredundanz](storage-redundancy.md)
- [Überprüfen der Eigenschaft „Zeitpunkt der letzten Synchronisierung“ für ein Speicherkonto](last-sync-time-get.md)
- [Verwenden von Georedundanz zum Entwerfen von hochverfügbaren Anwendungen](geo-redundant-design.md)
