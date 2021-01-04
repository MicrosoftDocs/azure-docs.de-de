---
title: Erstellen einer Azure-Dateifreigabe
titleSuffix: Azure Files
description: Hier erfahren Sie, wie Sie über das Azure-Portal, mithilfe von PowerShell oder mit der Azure CLI eine Azure-Dateifreigabe erstellen.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 2/22/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, references_regions
ms.openlocfilehash: 4df4c3d91c30dfd63de9073d8435f6f96c6ecd95
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2020
ms.locfileid: "94626825"
---
# <a name="create-an-azure-file-share"></a>Erstellen einer Azure-Dateifreigabe
Um eine Azure-Dateifreigabe zu erstellen, müssen Sie drei Fragen zur Verwendung beantworten:

- **Welche Leistungsanforderungen gelten für Ihre Azure-Dateifreigabe?**  
    Azure Files verfügt über Standard-Dateifreigaben (einschließlich transaktionsoptimierter, heißer und kalter Dateifreigaben), die auf festplattenbasierter Hardware (HDD-basiert) gehostet werden, und über Premium-Dateifreigaben, die auf SSD-Datenträgerhardware (SSD-basiert) gehostet werden.

- **Welche Größe benötigen Sie für Dateifreigaben?**  
    Standard-Dateifreigaben können bis zu 100 TiB groß sein, aber dieses Feature ist standardmäßig nicht aktiviert. Falls Sie eine Dateifreigabe benötigen, die größer als 5 TiB ist, müssen Sie das Feature für große Dateifreigaben für Ihr Speicherkonto aktivieren. Premium-Dateifreigaben können ohne spezielle Einstellung bis zu 100 TiB groß sein, aber sie werden gesondert bereitgestellt. Es wird keine nutzungsbasierte Bezahlung wie bei Standard-Dateifreigaben verwendet. Dies bedeutet, dass sich die Gesamtkosten für den Speicher erhöhen, wenn eine Dateifreigabe bereitgestellt wird, die Ihren Bedarf deutlich überschreitet.

- **Welche Redundanzanforderungen gelten für Ihre Azure-Dateifreigabe?**  
    Standard-Dateifreigaben ermöglichen die lokal redundante (LRS), zonenredundante (ZRS), georedundante (GRS) und geozonenredundante (GZRS) Speicherung. Das Feature für große Dateifreigaben wird aber nur für lokal redundante und zonenredundante Dateifreigaben unterstützt. Für Premium-Dateifreigaben wird keine Form von Georedundanz unterstützt.

    Premium-Dateifreigaben sind mit lokaler Redundanz in den meisten Regionen verfügbar, in denen Speicherkonten angeboten werden. Mit Zonenredundanz sind sie in einer kleineren Teilmenge der Regionen verfügbar. Um herauszufinden, ob Premium-Dateifreigaben derzeit in Ihrer Region verfügbar sind, lesen Sie die Seite [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=storage) für Azure. Informationen zu Regionen, die ZRS unterstützen, finden Sie unter [Azure Storage-Redundanz](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

Weitere Informationen zu diesen drei Optionen finden Sie unter [Planung für eine Azure Files-Bereitstellung](storage-files-planning.md).

## <a name="prerequisites"></a>Voraussetzungen
- In diesem Artikel wird davon ausgegangen, dass Sie bereits ein Azure-Abonnement erstellt haben. Wenn Sie noch kein Abonnement haben, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.
- Falls Sie Azure PowerShell verwenden möchten, [installieren Sie die neueste Version](/powershell/azure/install-az-ps).
- Falls Sie die Azure CLI verwenden möchten, [installieren Sie die neueste Version](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).

## <a name="create-a-storage-account"></a>Speicherkonto erstellen
Azure-Dateifreigaben werden in *Speicherkonten* bereitgestellt, bei denen es sich um Objekte der obersten Ebene handelt, die einen freigegebenen Speicherpool darstellen. Dieser Speicherpool kann verwendet werden, um mehrere Dateifreigaben bereitzustellen. 

Azure unterstützt mehrere Arten von Speicherkonten für unterschiedliche Kundenszenarien, aber es gibt zwei Hauptarten von Speicherkonten für Azure Files. Welche Art von Speicherkonto Sie erstellen müssen, hängt davon ab, ob Sie eine Standard- oder Premium-Dateifreigabe erstellen möchten: 

- **Speicherkonten vom Typ „Universell Version 2 (GPv2)“** : GPv2-Speicherkonten ermöglichen Ihnen die Bereitstellung von Azure-Dateifreigaben auf Standard- bzw. festplattenbasierter Hardware (HDD-basiert). Neben Azure-Dateifreigaben können in GPv2-Speicherkonten auch andere Speicherressourcen wie Blobcontainer, Warteschlangen oder Tabellen gespeichert werden. Dateifreigaben können auf den transaktionsoptimierten (Standardeinstellung), heißen und kalten Ebenen bereitgestellt werden.

- **FileStorage-Speicherkonten**: FileStorage-Speicherkonten ermöglichen Ihnen die Bereitstellung von Azure-Dateifreigaben auf SSD-basierter Hardware (Premium/Solid State Drive). FileStorage-Konten können nur zum Speichern von Azure-Dateifreigaben verwendet werden. In einem FileStorage-Konto können keine anderen Speicherressourcen (Blobcontainer, Warteschlangen, Tabellen usw.) bereitgestellt werden.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Wählen Sie zum Erstellen eines Speicherkontos über das Azure-Portal im Dashboard die Option **+ Ressource erstellen** aus. Suchen Sie im angezeigten Azure Marketplace-Suchfenster nach **Speicherkonto**, und wählen Sie das entsprechende Suchergebnis aus. Sie gelangen auf eine Übersichtsseite für Speicherkonten. Wählen Sie **Erstellen** aus, um den Assistenten für die Erstellung von Speicherkonten zu starten.

![Screenshot: Option zur schnellen Erstellung eines Speicherkontos in einem Browser](media/storage-how-to-create-file-share/create-storage-account-0.png)

#### <a name="the-basics-section"></a>Abschnitt „Grundlagen“
Der erste Abschnitt bei der Erstellung eines Speicherkontos hat die Bezeichnung **Grundlagen**. Er enthält alle erforderlichen Felder für die Erstellung eines Speicherkontos. Stellen Sie bei der Erstellung eines GPv2-Speicherkontos sicher, dass das Optionsfeld **Leistung** auf *Standard* festgelegt und in der Dropdownliste **Kontoart** die Option *StorageV2 (universell v2)* ausgewählt ist.

![Screenshot: Optionsfeld „Leistung“ mit Auswahl von „Standard“ und Auswahl von „StorageV2 (universell v2)“ für „Kontoart“](media/storage-how-to-create-file-share/create-storage-account-1.png)

Stellen Sie zum Erstellen eines FileStorage-Speicherkontos sicher, dass das Optionsfeld **Leistung** auf *Premium* festgelegt und in der Dropdownliste **Kontoart** die Option *FileStorage* ausgewählt ist.

![Screenshot: Optionsfeld „Leistung“ mit Auswahl von „Premium“ und Auswahl von „FileStorage“ für „Kontoart“](media/storage-how-to-create-file-share/create-storage-account-2.png)

Die anderen Felder für die Grundlagen sind unabhängig von der Auswahl des Speicherkontos:
- **Abonnement**: Das Abonnement, unter dem das Speicherkonto bereitgestellt werden soll. 
- **Ressourcengruppe**: Die Ressourcengruppe, in der das Speicherkonto bereitgestellt werden soll. Sie können entweder eine neue Ressourcengruppe erstellen oder eine vorhandene verwenden. Eine Ressourcengruppe ist ein logischer Container zur Gruppierung Ihrer Azure-Dienste. Beim Erstellen eines Speicherkontos haben Sie die Wahlmöglichkeit, entweder eine neue Ressourcengruppe zu erstellen oder eine vorhandene Ressourcengruppe zu verwenden.
- **Speicherkontoname**: Der Name der Speicherkontoressource, die erstellt werden soll. Dieser Name muss global eindeutig sein, aber ansonsten gelten keine weiteren Einschränkungen. Der Name des Speicherkontos wird als Servername verwendet, wenn Sie eine Azure-Dateifreigabe per SMB einbinden.
- **Standort**: Die Region, in der das Speicherkonto bereitgestellt werden soll. Dies kann die Region sein, die der Ressourcengruppe zugeordnet ist, oder eine andere verfügbare Region.
- **Replikation**: Die Bezeichnung dieser Option lautet zwar „Replikation“, aber es geht eigentlich um **Redundanz**. Hier wird die gewünschte Redundanzebene angegeben: lokale Redundanz (LRS), Zonenredundanz (ZRS), Georedundanz (GRS) und Geozonenredundanz. Diese Dropdownliste enthält auch Optionen für Georedundanz mit Lesezugriff (RA-GRS) und Geozonenredundanz mit Lesezugriff (RA-GZRS), die nicht für Azure-Dateifreigaben gelten. Alle Dateifreigaben, die mit diesen Optionen in einem Speicherkonto erstellt werden, sind entweder georedundant oder geozonenredundant. Je nach Region oder ausgewähltem Speicherkontotyp sind einige Redundanzoptionen ggf. nicht zulässig.
- **Blobzugriffsebene**: Dieses Feld gilt nicht für Azure Files. Sie können also eines der beiden Optionsfelder auswählen. 

> [!Important]  
> Die Auswahl der Blobzugriffsebene hat keinen Einfluss auf die Ebene der Dateifreigabe.

#### <a name="the-networking-blade"></a>Blatt „Netzwerk“
Im Abschnitt „Netzwerk“ können Sie die Netzwerkoptionen konfigurieren. Diese Einstellungen sind für die Erstellung des Speicherkontos optional und können bei Bedarf zu einem späteren Zeitpunkt konfiguriert werden. Weitere Informationen zu diesen Optionen finden Sie unter [Azure Files – Überlegungen zum Netzwerkbetrieb](storage-files-networking-overview.md).

#### <a name="the-advanced-blade"></a>Blatt „Erweitert“
Der Abschnitt „Erweitert“ enthält mehrere wichtige Einstellungen für Azure-Dateifreigaben:

- **Sichere Übertragung erforderlich**: Dieses Feld gibt an, ob für die Kommunikation mit dem Speicherkonto in eingehender Richtung eine Verschlüsselung während der Übertragung erforderlich ist. Wir empfehlen Ihnen, diese Option aktiviert zu lassen. Falls Sie SMB 2.1-Unterstützung benötigen, müssen Sie sie aber deaktivieren. Bei einer Deaktivierung der Verschlüsselung empfehlen wir Ihnen, den Speicherkontozugriff auf ein virtuelles Netzwerk mit Dienstendpunkten bzw. privaten Endpunkten einzuschränken.
- **Große Dateifreigaben**: Mit diesem Feld wird für das Speicherkonto die Nutzung von Dateifreigaben mit einer Größe von bis zu 100 TiB ermöglicht. Wenn Sie dieses Feature aktivieren, wird Ihr Speicherkonto auf die Nutzung von lokal redundantem und zonenredundantem Speicher beschränkt. Nachdem für ein GPv2-Speicherkonto große Dateifreigaben aktiviert wurden, können Sie diese Funktion nicht mehr deaktivieren. Für Speicherkonten vom Typ „FileStorage“ (Speicherkonten für Premium-Dateifreigaben) ist diese Option nicht vorhanden, da alle Premium-Dateifreigaben auf bis zu 100 TiB hochskaliert werden können. 

![Screenshot: Wichtige erweiterte Einstellungen für Azure Files](media/storage-how-to-create-file-share/create-storage-account-3.png)

Die anderen Einstellungen, die auf der Registerkarte „Erweitert“ verfügbar sind (vorläufiges Löschen von Blobs, hierarchischer Namespace für Azure Data Lake Storage Gen2 und NFSv3 für Blobspeicher) gelten nicht für Azure Files.

#### <a name="tags"></a>`Tags`
Tags sind Name/Wert-Paare, die Ihnen das Kategorisieren von Ressourcen und die Anzeige einer konsolidierten Abrechnung ermöglichen, indem Sie dasselbe Tag auf mehrere Ressourcen und Ressourcengruppen anwenden. Sie sind optional und können angewendet werden, nachdem das Speicherkonto erstellt wurde.

#### <a name="review--create"></a>Bewerten + erstellen
Der letzte Schritt zur Erstellung des Speicherkontos ist die Auswahl der Schaltfläche **Erstellen** auf der Registerkarte **Bewerten + erstellen**. Diese Schaltfläche ist nicht verfügbar, wenn nicht alle erforderlichen Felder für ein Speicherkonto ausgefüllt sind.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Wir verwenden das Cmdlet `New-AzStorageAccount`, um mit PowerShell ein Speicherkonto zu erstellen. Dieses Cmdlet hat viele Optionen, von denen nur die erforderlichen Optionen angezeigt werden. Weitere Informationen zu den erweiterten Optionen finden Sie in der [Dokumentation zum Cmdlet `New-AzStorageAccount`](/powershell/module/az.storage/new-azstorageaccount).

Zur Vereinfachung der Erstellung des Speicherkontos und der nachfolgenden Dateifreigabe speichern wir Parameter in Variablen. Sie können den Inhalt der Variablen durch beliebige Werte ersetzen. Beachten Sie hierbei aber, dass der Name des Speicherkontos global eindeutig sein muss.

```powershell
$resourceGroupName = "myResourceGroup"
$storageAccountName = "mystorageacct$(Get-Random)"
$region = "westus2"
```

Wir verwenden den folgenden Befehl, um ein Speicherkonto zu erstellen, mit dem Azure-Dateifreigaben vom Typ „Standard“ gespeichert werden können. Der Parameter `-SkuName` bezieht sich auf den gewünschten Redundanztyp. Wenn Sie ein georedundantes oder geozonenredundantes Speicherkonto verwenden möchten, müssen Sie auch den Parameter `-EnableLargeFileShare` entfernen.

```powershell
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Standard_LRS `
    -Location $region `
    -Kind StorageV2 `
    -EnableLargeFileShare
```

Wir verwenden den folgenden Befehl, um ein Speicherkonto zu erstellen, mit dem Azure-Dateifreigaben vom Typ „Premium“ gespeichert werden können. Beachten Sie, dass sich der Parameter `-SkuName` geändert hat und nun sowohl `Premium` als auch die gewünschte Redundanzebene „Lokale Redundanz“ (`LRS`) enthält. Für den Parameter `-Kind` wird `FileStorage` anstelle von `StorageV2` verwendet, da Premium-Dateifreigaben nicht unter einem GPv2-Speicherkonto, sondern unter einem FileStorage-Speicherkonto erstellt werden müssen.

```powershell
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Premium_LRS `
    -Location $region `
    -Kind FileStorage 
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
Um ein Speicherkonto über die Azure-Befehlszeilenschnittstelle zu erstellen, verwenden wir den Befehl „az storage account create“. Dieser Befehl hat viele Optionen, von denen nur die erforderlichen Optionen angezeigt werden. Weitere Informationen zu den erweiterten Optionen finden Sie in der [Dokumentation zum Befehl `az storage account create`](/cli/azure/storage/account).

Zur Vereinfachung der Erstellung des Speicherkontos und der nachfolgenden Dateifreigabe speichern wir Parameter in Variablen. Sie können den Inhalt der Variablen durch beliebige Werte ersetzen. Beachten Sie hierbei aber, dass der Name des Speicherkontos global eindeutig sein muss.

```bash
resourceGroupName="myResourceGroup"
storageAccountName="mystorageacct$RANDOM"
region="westus2"
```

Wir verwenden den folgenden Befehl, um ein Speicherkonto zu erstellen, mit dem Azure-Dateifreigaben vom Typ „Standard“ gespeichert werden können. Der Parameter `--sku` bezieht sich auf den gewünschten Redundanztyp. Wenn Sie ein georedundantes oder geozonenredundantes Speicherkonto verwenden möchten, müssen Sie auch den Parameter `--enable-large-file-share` entfernen.

```bash
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind StorageV2 \
    --sku Standard_ZRS \
    --enable-large-file-share \
    --output none
```

Wir verwenden den folgenden Befehl, um ein Speicherkonto zu erstellen, mit dem Azure-Dateifreigaben vom Typ „Premium“ gespeichert werden können. Beachten Sie, dass sich der Parameter `--sku` geändert hat und nun sowohl `Premium` als auch die gewünschte Redundanzebene „Lokale Redundanz“ (`LRS`) enthält. Für den Parameter `--kind` wird `FileStorage` anstelle von `StorageV2` verwendet, da Premium-Dateifreigaben nicht unter einem GPv2-Speicherkonto, sondern unter einem FileStorage-Speicherkonto erstellt werden müssen.

```bash
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind FileStorage \
    --sku Premium_LRS \
    --output none
```

---

## <a name="create-file-share"></a>Erstellen einer Dateifreigabe
Nachdem Sie Ihr Speicherkonto erstellt haben, fehlt nur noch die Erstellung der Dateifreigabe. Dieser Prozess ist unabhängig davon, ob eine Premium- oder Standard-Dateifreigabe verwendet wird, größtenteils identisch. Sie sollten die folgenden Unterschiede berücksichtigen.

Standarddateifreigaben können in einer der Standardebenen bereitgestellt werden: transaktionsoptimierte (Standard), heiße oder kalte Ebene. Hierbei handelt es sich um eine Ebene pro Dateifreigabe, die nicht von der **Blobzugriffsebene** des Speicherkontos betroffen ist (diese Eigenschaft bezieht sich nur auf Azure-Blobspeicher – sie bezieht sich überhaupt nicht auf Azure Files). Sie können die Ebene der Freigabe jederzeit nach der Bereitstellung ändern. Premium-Dateifreigaben können nicht direkt in Standarddateifreigaben auf einer beliebigen Standardebene umgewandelt werden.

> [!Important]  
> Sie können Dateifreigaben zwischen Ebenen innerhalb von GPv2-Speicherkontotypen verschieben (transaktionsoptimiert, heiß und kalt). Freigabeverschiebungen zwischen Ebenen verursachen Transaktionen: Beim Verschieben von einer heißeren Ebene in eine kältere Ebene fällt für jede Datei in der Freigabe die Gebühr für Schreibtransaktionen der kälteren Ebene an. Beim Verschieben von einer kälteren Ebene in eine heißere Ebene hingegen fällt für jede Datei in der Freigabe die Gebühr für Lesetransaktionen der kalten Ebene an.

Die **quota**-Eigenschaft unterscheidet sich leicht zwischen Premium- und Standarddateifreigaben:

- Bei Standard-Dateifreigaben ist dies eine Obergrenze für die Azure-Dateifreigabe, die von Endbenutzern nicht überschritten werden darf. Der Hauptgrund bei der Verwendung eines Kontingents für eine Standard-Dateifreigabe ist das Budget: „Ich möchte nicht, dass die Größe dieser Dateifreigabe diese Grenze überschreitet.“ Wenn kein Kontingent angegeben wird, kann eine Standard-Dateifreigabe eine Größe von bis zu 100 TiB haben (bzw. 5 TiB, wenn die Eigenschaft für große Dateifreigaben für ein Speicherkonto nicht festgelegt wurde).

- Für Premium-Dateifreigaben ist mit dem Kontingent die **bereitgestellte Größe** gemeint. Die bereitgestellte Größe ist die Menge, die Ihnen unabhängig von der tatsächlichen Nutzung berechnet wird. Es ist ratsam, beim Bereitstellen einer Premium-Dateifreigabe zwei Faktoren zu berücksichtigen: 1) zukünftiges Wachstum der Freigabe aus Sicht der Speicherplatzauslastung und 2) erforderlicher IOPS-Wert für Ihre Workload. Für jedes bereitgestellte GiB haben Sie Anspruch auf zusätzliche reservierte bzw. Burst-IOPS-Kapazität. Weitere Informationen zur Planung einer Premium-Dateifreigabe finden Sie im Abschnitt zum [Bereitstellen von Premium-Dateifreigaben](storage-files-planning.md#understanding-provisioning-for-premium-file-shares).

# <a name="portal"></a>[Portal](#tab/azure-portal)
Wenn Sie Ihr Speicherkonto gerade erstellt haben, können Sie vom Bereitstellungsbildschirm dorthin navigieren, indem Sie **Zu Ressource wechseln** auswählen. Falls Sie das Speicherkonto schon vorher erstellt haben, können Sie darauf über die Ressourcengruppe zugreifen, in der es enthalten ist. Wählen Sie im Speicherkonto die Kachel **Dateifreigaben** aus. (Sie können auch über das Inhaltsverzeichnis des Speicherkontos zu **Dateifreigaben** navigieren.)

![Screenshot: Kachel „Dateifreigaben“](media/storage-how-to-create-file-share/create-file-share-1.png)

In der Liste mit den Dateifreigaben sollten alle Dateifreigaben angezeigt werden, die Sie unter diesem Speicherkonto zuvor erstellt haben. Die Tabelle ist leer, falls noch keine Dateifreigaben erstellt wurden. Wählen Sie **+ Dateifreigabe** aus, um eine neue Dateifreigabe zu erstellen.

Das Blatt „Neue Dateifreigabe“ wird angezeigt. Füllen Sie die Felder des Blatts „Neue Dateifreigabe“ aus, um eine Dateifreigabe zu erstellen:

- **Name**: Der Name der Dateifreigabe, die erstellt werden soll.
- **Kontingent**: Das Kontingent der Dateifreigabe für Standard-Dateifreigaben. Dies ist die bereitgestellte Dateifreigabegröße für Premium-Dateifreigaben.
- **Ebenen**: Die ausgewählte Ebene für eine Dateifreigabe. Dieses Feld ist nur in einem **GPv2-Speicherkonto (Universell)** verfügbar. Sie können „transaktionsoptimiert“, „heiß“ oder „kalt“ auswählen. Die Ebene der Freigabe kann jederzeit geändert werden. Es wird empfohlen, während einer Migration die höchstmögliche Ebene zu wählen, um die Transaktionskosten zu minimieren, und nach Abschluss der Migration bei Bedarf auf eine niedrigere Ebene zu wechseln.

Wählen Sie **Erstellen** aus, um die Erstellung der neuen Freigabe abzuschließen. Beachten Sie Folgendes: Wenn sich Ihr Speicherkonto in einem virtuellen Netzwerk befindet, ist die Erstellung einer Azure-Dateifreigabe nur erfolgreich, wenn Ihr Client in demselben virtuellen Netzwerk angeordnet ist. Sie können diese Point-in-Time-Einschränkung umgehen, indem Sie das Azure PowerShell-Cmdlet `New-AzRmStorageShare` verwenden.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Sie können eine Azure-Dateifreigabe mit dem Cmdlet [`New-AzRmStorageShare`](/powershell/module/az.storage/New-AzRmStorageShare) erstellen. Bei den folgenden PowerShell-Befehlen wird davon ausgegangen, dass Sie die Variablen `$resourceGroupName` und `$storageAccountName` gemäß der obigen Definition im Abschnitt zum „Erstellen eines Speicherkontos mit Azure PowerShell“ festgelegt haben. 

Das folgende Beispiel zeigt das Erstellen einer Dateifreigabe mit einer expliziten Ebene mithilfe des Parameters `-AccessTier`. Dies erfordert die Verwendung des Az.Storage-Vorschaumoduls, wie im Beispiel angegeben. Wenn keine Ebene angegeben wird, weil Sie entweder das Az.Storage-GA-Modul verwenden oder Sie diesen Befehl nicht einbezogen haben, ist die Standardebene für Standarddateifreigaben transaktionsoptimiert.

> [!Important]  
> Bei Premium-Dateifreigaben verweist der Parameter `-QuotaGiB` auf die bereitgestellte Größe der Dateifreigabe. Die bereitgestellte Größe der Dateifreigabe ist die Menge, die Ihnen unabhängig von der Nutzung berechnet wird. Standard-Dateifreigaben werden nicht basierend auf der Nutzung, sondern auf der bereitgestellten Größe berechnet.

```powershell
# Update the Azure storage module to use the preview version. You may need to close and 
# reopen PowerShell before running this command. If you are running PowerShell 5.1, ensure 
# the following:
# - Run the below cmdlets as an administrator.
# - Have PowerShellGet 2.2.3 or later. Uncomment the following line to check.
# Get-Module -ListAvailable -Name PowerShellGet
Remove-Module -Name Az.Storage -ErrorAction SilentlyContinue
Uninstall-Module -Name Az.Storage
Install-Module -Name Az.Storage -RequiredVersion "2.1.1-preview" -AllowClobber -AllowPrerelease 

# Assuming $resourceGroupName and $storageAccountName from earlier in this document have already
# been populated. The access tier parameter may be TransactionOptimized, Hot, or Cool for GPv2 
# storage accounts. Standard tiers are only available in standard storage accounts. 
$shareName = "myshare"

New-AzRmStorageShare `
        -ResourceGroupName $resourceGroupName `
        -StorageAccountName $storageAccountName `
        -Name $shareName `
        -AccessTier TransactionOptimized `
        -QuotaGiB 1024 | `
    Out-Null
```

> [!Note]  
> Die Funktionalität zum Einrichten und Ändern von Tarifen über PowerShell wird im PowerShell-Vorschaumodul „Az.Storage“ bereitgestellt. Denken Sie beim Erstellen der Skripts daran, dass diese Cmdlets bzw. deren Ausgabe sich vor Veröffentlichung der allgemein verfügbaren Version des Moduls ändern können.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
Sie können eine Azure-Dateifreigabe mit dem Befehl [`az storage share-rm create`](/cli/azure/storage/share-rm?preserve-view=true&view=azure-cli-latest#az_storage_share_rm_create) erstellen. Bei den folgenden Azure CLI-Befehlen wird davon ausgegangen, dass Sie die Variablen `$resourceGroupName` und `$storageAccountName` gemäß der obigen Definition im Abschnitt zum „Erstellen eines Speicherkontos mit Azure CLI“ festgelegt haben.

Die Funktionalität zum Erstellen oder Verschieben einer Dateifreigabe in eine bestimmte Dienstebene ist im letzten Azure CLI-Update verfügbar. Das Aktualisieren von Azure CLI ist für die von Ihnen verwendete Betriebssystem-/Linux-Distribution spezifisch. Anweisungen zum Aktualisieren der Azure CLI auf Ihrem System finden Sie unter [Installieren der Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).

> [!Important]  
> Bei Premium-Dateifreigaben verweist der Parameter `--quota` auf die bereitgestellte Größe der Dateifreigabe. Die bereitgestellte Größe der Dateifreigabe ist die Menge, die Ihnen unabhängig von der Nutzung berechnet wird. Standard-Dateifreigaben werden nicht basierend auf der Nutzung, sondern auf der bereitgestellten Größe berechnet.

```bash
shareName="myshare"

az storage share-rm create \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --access-tier "TransactionOptimized" \
    --quota 1024 \
    --output none
```

> [!Note]  
> Die Funktion zum Festlegen eines Tarifs mit dem Parameter `--access-tier` ist als Vorschau im neuesten Azure CLI-Paket enthalten. Denken Sie beim Erstellen der Skripts daran, dass dieser Befehl bzw. dessen Ausgabe sich ändern kann, bevor er als allgemein verfügbar gekennzeichnet wird.

---

> [!Note]  
> Der Name der Dateifreigabe darf nur Kleinbuchstaben enthalten. Ausführliche Informationen zur Benennung von Dateifreigaben und Dateien finden Sie unter [Benennen und Referenzieren von Freigaben, Verzeichnissen, Dateien und Metadaten](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

### <a name="changing-the-tier-of-an-azure-file-share"></a>Ändern der Ebene einer Azure-Dateifreigabe
Dateifreigaben, die in **GPv2-Speicherkonto (Universell v2)** bereitgestellt werden, können auf den transaktionsoptimierten, heißen oder kalten Ebenen liegen. Sie können die Ebene der Azure-Dateifreigabe jederzeit ändern, vorbehaltlich der oben beschriebenen Transaktionskosten.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Wählen Sie auf der Seite des Hauptspeicherkontos die Option **Dateifreigaben** und dann die Kachel mit der Bezeichnung **Dateifreigaben** aus (Sie können auch über das Inhaltsverzeichnis des Speicherkontos zu **Dateifreigaben** navigieren).

![Screenshot: Kachel „Dateifreigaben“](media/storage-how-to-create-file-share/create-file-share-1.png)

Wählen Sie in der tabellarischen Liste der Dateifreigaben die Dateifreigabe aus, für die Sie die Ebene ändern möchten. Wählen Sie auf der Übersichtsseite der Dateifreigabe **Ebene ändern** aus dem Menü aus.

![Screenshot: Übersichtsseite der Dateifreigabe mit hervorgehobener Schaltfläche „Ebene ändern“.](media/storage-how-to-create-file-share/change-tier-0.png)

Wählen Sie im darauf folgenden Dialogfeld die gewünschte Ebene aus: transaktionsoptimiert, heiß oder kalt.

![Screenshot: Dialogfeld „Ebene ändern“.](media/storage-how-to-create-file-share/change-tier-1.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Das folgende PowerShell-Cmdlet setzt voraus, dass Sie die Variablen `$resourceGroupName`, `$storageAccountName`, `$shareName` wie in den früheren Abschnitten dieses Dokuments beschrieben festgelegt haben.

```PowerShell
# This cmdlet requires Az.Storage version 2.1.1-preview, which is installed
# in the earlier example.
Update-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -AccessTier Cool
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
Der folgende Azure CLI-Befehl setzt voraus, dass Sie die Variablen `$resourceGroupName`, `$storageAccountName` und `$shareName` wie in den früheren Abschnitten dieses Dokuments beschrieben festgelegt haben.

```bash
az storage share-rm update \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --access-tier "Cool"
```

---

## <a name="next-steps"></a>Nächste Schritte
- [Planen der Bereitstellung von Azure Files](storage-files-planning.md) oder [Planen der Bereitstellung der Azure-Dateisynchronisierung](storage-sync-files-planning.md). 
- [Netzwerkübersicht](storage-files-networking-overview.md).
- Führen Sie die Verbindungsherstellung und Einbindung einer Dateifreigabe unter [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md) und [Linux](storage-how-to-use-files-linux.md) durch.