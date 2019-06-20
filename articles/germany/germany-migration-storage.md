---
title: Migration von Azure-Speicherressourcen von Azure Deutschland zu Azure weltweit
description: Dieser Artikel enthält Informationen zum Migrieren von Azure-Speicherressourcen von Azure Deutschland zu Azure weltweit.
author: gitralf
services: germany
cloud: Azure Germany
ms.author: ralfwi
ms.service: germany
ms.date: 08/15/2018
ms.topic: article
ms.custom: bfmigrate
ms.openlocfilehash: db26380ef50fd7546f90ceccaec854587e4690c4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66165933"
---
# <a name="migrate-storage-resources-to-global-azure"></a>Migrieren von Speicherressourcen zu Azure weltweit

Dieser Artikel enthält Informationen dazu, wie Sie Azure-Speicherressourcen von Azure Deutschland zu Azure weltweit migrieren können.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="blobs"></a>Blobs (in englischer Sprache)

AzCopy ist ein kostenloses Tool, mit dem Sie Blobs, Dateien und Tabellen kopieren können. AzCopy kann für Azure-zu-Azure-, Lokal-zu-Azure- und Azure-zu-lokal-Migrationen verwendet werden. Verwenden Sie AzCopy für Ihre Migration, um Blobs direkt von Azure Deutschland nach Azure weltweit zu kopieren.

Wenn Sie keine verwalteten Datenträger für Ihren virtuellen Quellcomputer nutzen, verwenden Sie AzCopy, um die VHD-Dateien in die Zielumgebung zu kopieren. Andernfalls müssen Sie einige Schritte vorab ausführen. Weitere Informationen finden Sie unter [Empfehlungen für verwaltete Datenträger](#managed-disks).

Im folgenden Beispiel wird die Arbeitsweise von AzCopy veranschaulicht. Eine vollständige Referenz finden Sie in der [AzCopy-Dokumentation](../storage/common/storage-use-azcopy.md).

AzCopy verwendet die Begriffe **Source** und **Dest**, ausgedrückt als URIs. URIs für Azure Deutschland weisen immer das folgende Format auf:

```http
https://<storageaccountname>.blob.core.cloudapi.de/<containername>/<blobname>
```

URIs für Azure weltweit weisen immer das folgende Format auf:

```http
https://<storageaccountname>.blob.core.windows.net/<containername>/<blobname>
```

Sie erhalten die drei Teile des URIs (*storageaccountname*, *containername* und *blobname*) über das Portal, durch Verwenden von PowerShell oder durch Verwenden der Azure-Befehlszeilenschnittstelle. Der Name des Blobs kann Teil des URIs sein, oder er kann als ein Muster (z. B. *vm121314.vhd*) angegeben sein.

Außerdem benötigen Sie die Speicherkontoschlüssel, um auf das Azure Storage-Konto zugreifen zu können. Rufen Sie diese über das Portal, durch Verwenden von PowerShell oder durch Verwenden der Befehlszeilenschnittstelle ab. Beispiel:

```powershell
Get-AzStorageAccountKey -Name <saname> -ResourceGroupName <rgname>
```

Wie immer benötigen Sie nur einen der beiden Schlüssel für jedes Speicherkonto.

Beispiel:

URI-Teil | Beispielwert
-------- | --------------
Quell-storageAccount | `migratetest`
Quellcontainer | `vhds`
Quellblob | `vm-121314.vhd`
Ziel-storageAccount | `migratetarget`
Zielcontainer | `targetcontainer`

Dieser Befehl kopiert eine virtuelle Festplatte aus Azure Deutschland in Azure weltweit (die Schlüssel wurden gekürzt, um die Lesbarkeit zu verbessern):

```cmd
azcopy -v /source:https://migratetest.blob.core.cloudapi.de/vhds /sourcekey:"0LN...w==" /dest:https://migratetarget.blob.core.windows.net/targetcontainer /DestKey:"o//ucDi5TN...w==" /Pattern:vm-121314.vhd
```

Um eine konsistente Kopie der VHD zu erhalten, fahren Sie den virtuellen Computer herunter, bevor Sie die VHD kopieren. Planen Sie einige Ausfallzeit für den Kopiervorgang ein. Nachdem die VHD kopiert ist, [erstellen Sie Ihren virtuellen Computer in der Zielumgebung neu](../backup/backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

Weitere Informationen finden Sie unter:

- Lesen Sie die [AZCopy-Dokumentation](../storage/common/storage-use-azcopy.md).
- Erfahren Sie, wie Sie [einen virtuellen Computer aus wiederhergestellten Datenträgern erstellen](../backup/backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

## <a name="managed-disks"></a>Managed Disks

Azure Managed Disks vereinfacht die Datenträgerverwaltung für Azure Infrastructure-as-a-Service-VMs (IaaS-VMs), indem die Speicherkonten verwaltet werden, die dem VM-Datenträger zugeordnet sind. 

Da Sie keinen direkten Zugriff auf die VHD-Datei haben, können Sie Tools wie AzCopy nicht direkt dazu verwenden, Ihre Dateien zu kopieren (siehe [Blobs](#blobs)). Die Problemumgehung besteht darin, den verwalteten Datenträger zunächst zu exportieren, indem Sie einen temporären Shared Access Signature-URI abrufen, und den Datenträger dann mit diesen Informationen herunterzuladen oder zu kopieren. In den folgenden Abschnitten wird in einem Beispiel veranschaulicht, wie der Shared Access Signature-URI abgerufen wird, und wie er verwendet werden kann.

### <a name="step-1-get-the-shared-access-signature-uri"></a>Schritt 1: Abrufen des Shared Access Signature-URIs

1. Suchen Sie im Portal nach Ihrem verwalteten Datenträger. (Es befindet sich in derselben Ressourcengruppe wie Ihr virtueller Computer. Der Ressourcentyp ist **Datenträger**.)
1. Wählen Sie auf der Seite **Übersicht** die Schaltfläche **Exportieren** im oberen Menü aus (Sie müssen zuerst Ihren virtuellen Computer herunterfahren und dessen Zuordnung aufheben, oder Sie müssen den virtuellen Computer trennen).
1. Definieren Sie eine Zeit, nach der der URI abgelaufen sein soll (der Standardwert ist 3.600 Sekunden).
1. Generieren Sie eine URL (dieser Schritt sollte nur wenige Sekunden dauern).
1. Kopieren Sie die URL (sie wird nur einmal angezeigt).

### <a name="step-2-azcopy"></a>Schritt 2: AzCopy

Beispiele zur Verwendung von AzCopy finden Sie unter [Blobs](#blobs). Verwenden Sie AzCopy (oder ein vergleichbares Tool), um den Datenträger direkt aus Ihrer Quellumgebung in die Zielumgebung zu kopieren. In AzCopy müssen Sie den URI in den Basis-URI und den SAS-Bestandteil (Shared Access Signature) aufteilen. Der SAS-Bestandteil des URIs beginnt mit dem Zeichen „ **?** “. Das Portal stellt diesen URI für den SAS-URI bereit:

```http
https://md-kp4qvrzhj4j5.blob.core.cloudapi.de/r0pmw4z3vk1g/abcd?sv=2017-04-17&sr=b&si=22970153-4c56-47c0-8cbb-156a24b6e4b5&sig=5Hfu0qMw9rkZf6mCjuCE4VMV6W3IR8FXQSY1viji9bg%3D>
```

Die folgenden Befehle zeigen die Quellparameter für AzCopy:

```cmd
/source:"https://md-kp4qvrzhj4j5.blob.core.cloudapi.de/r0pmw4z3vk1g/abcd"
```

```cmd
/sourceSAS:" ?sv=2017-04-17&sr=b&si=22970153-4c56-47c0-8cbb-156a24b6e4b5&sig=5Hfu0qMw9rkZf6mCjuCE4VMV6W3IR8FXQSY1viji9bg%3D"
```

Hier ist der vollständige Befehl:

```cmd
azcopy -v /source:"https://md-kp4qvrzhj4j5.blob.core.cloudapi.de/r0pmw4z3vk1g/abcd" /sourceSAS:"?sv=2017-04-17&sr=b&si=22970153-4c56-47c0-8cbb-156a24b6e4b5&sig=5Hfu0qMw9rkZf6mCjuCE4VMV6W3IR8FXQSY1viji9bg%3D" /dest:"https://migratetarget.blob.core.windows.net/targetcontainer/newdisk.vhd" /DestKey:"o//ucD... Kdpw=="
```

### <a name="step-3-create-a-new-managed-disk-in-the-target-environment"></a>Schritt 3: Erstellen eines neuen verwalteten Datenträgers in der Zielumgebung

Sie haben mehrere Optionen zum Erstellen eines neuen verwalteten Datenträgers. Die Vorgehensweise im Azure-Portal sieht wie folgt aus:

1. Wählen Sie im Portal den Befehl **Neu** > **Verwalteter Datenträger** > **Erstellen** aus.
1. Geben Sie einen Namen für den neuen Datenträger ein.
1. Wählen Sie eine Ressourcengruppe aus.
1. Wählen Sie unter **Quelltyp** die Option **Speicherblob** aus. Kopieren Sie nun den Ziel-URI aus dem AzCopy-Befehl, oder navigieren Sie zum Ziel-URI, um ihn auszuwählen.
1. Wenn Sie einen Betriebssystemdatenträger kopiert haben, wählen Sie den Typ **Betriebssystem** aus. Für einen anderen Datenträgertyp wählen Sie **Erstellen** aus.

### <a name="step-4-create-the-vm"></a>Schritt 4: Erstellen des virtuellen Computers

Wie bereits erwähnt, gibt es mehrere Möglichkeiten, einen virtuellen Computer durch Verwenden dieses neuen verwalteten Datenträgers zu erstellen. Es folgen zwei Optionen:

- Wählen Sie im Portal den Datenträger aus, und wählen Sie dann **Virtuellen Computer erstellen** aus. Definieren Sie die anderen Parameter Ihres virtuellen Computers wie gewohnt.
- Informationen zur Vorgehensweise mit PowerShell finden Sie unter [Erstellen eines virtuellen Computers aus wiederhergestellten Datenträgern](../backup/backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

Weitere Informationen finden Sie unter:

- Erfahren Sie, wie Sie [über die API](/rest/api/compute/disks/grantaccess) auf den Datenträger exportieren, indem Sie einen Shared Access Signature-URI abrufen. 
- Erfahren Sie, wie Sie einen verwalteten Datenträger [über die API](/rest/api/compute/disks/createorupdate#create_a_managed_disk_by_importing_an_unmanaged_blob_from_a_different_subscription.) aus einem nicht verwalteten Blob erstellen.


## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über Tools, Techniken und Empfehlungen zum Migrieren von Ressourcen in den folgenden Dienstkategorien:

- [Compute](./germany-migration-compute.md)
- [Netzwerk](./germany-migration-networking.md)
- [Web](./germany-migration-web.md)
- [Datenbanken](./germany-migration-databases.md)
- [Analyse](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [Integration](./germany-migration-integration.md)
- [Identität](./germany-migration-identity.md)
- [Sicherheit](./germany-migration-security.md)
- [Verwaltungstools](./germany-migration-management-tools.md)
- [Medien](./germany-migration-media.md)

