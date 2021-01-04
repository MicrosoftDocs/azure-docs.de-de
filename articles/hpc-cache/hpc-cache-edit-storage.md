---
title: Aktualisieren von Azure HPC Cache-Speicherzielen
description: Bearbeiten von Azure HPC Cache-Speicherzielen
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: 7b7bc87f83925baed15bc6a411f5b4e4f78df6cc
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94654506"
---
# <a name="edit-storage-targets"></a>Bearbeiten von Speicherzielen

Sie können Speicherziele mithilfe des Azure-Portals oder der Azure CLI entfernen oder ändern.

Je nach Speichertyp können Sie die folgenden Werte für Speicherziele ändern:

* Für Bobspeicherziele können Sie den Namespacepfad ändern.

* Für NFS-Speicherziele können Sie die folgenden Werte ändern:

  * Namespacepfade
  * Der einem Namespacepfad zugeordnete Speicherexport oder das entsprechende Exportunterverzeichnis
  * Nutzungsmodell

Sie können den Namen, den Typ oder das Back-End-Speichersystem eines Speicherziels (Blobcontainer oder NFS-Hostname/IP-Adresse) nicht bearbeiten. Wenn Sie diese Eigenschaften ändern müssen, müssen Sie das Speicherziel löschen und einen Ersatz mit dem neuen Wert erstellen.

> [!TIP]
> Im [Video zur Verwaltung von Azure HPC Cache](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) wird gezeigt, wie Sie ein Speicherziel im Azure-Portal bearbeiten.

## <a name="remove-a-storage-target"></a>Entfernen eines Speicherziels

### <a name="portal"></a>[Portal](#tab/azure-portal)

Wenn Sie ein Speicherziel entfernen möchten, öffnen Sie die Seite **Speicherziele**. Wählen Sie das Speicherziel aus der Liste aus, und klicken Sie auf die Schaltfläche **Löschen**.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

[Einrichten der Azure CLI für Azure HPC Cache](./az-cli-prerequisites.md).

Verwenden Sie [az hpc-cache storage-target remove](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-remove), um ein Speicherziel aus dem Cache zu löschen.

```azurecli
$ az hpc-cache storage-target remove --resource-group cache-rg --cache-name doc-cache0629 --name blob1

{- Finished ..
  "endTime": "2020-07-09T21:45:06.1631571+00:00",
  "name": "2f95eac1-aded-4860-b19c-3f089531a7ec",
  "startTime": "2020-07-09T21:43:38.5461495+00:00",
  "status": "Succeeded"
}
```

---

Durch das Löschen eines Speicherziels wird die Zuordnung des Speichersystems zu diesem Azure HPC Cache-System entfernt, das Back-End-Speichersystem wird jedoch nicht geändert. Wenn Sie beispielsweise einen Azure Blob Storage-Container verwendet haben, sind der Container und sein Inhalt nach dem Löschen aus dem Cache weiterhin vorhanden. Im Azure-Portal können Sie den Container einer anderen Azure HPC Cache-Instanz hinzufügen, ihn erneut diesem Cache hinzufügen oder löschen.

Alle im Cache gespeicherten Dateiänderungen werden in das Back-End-Speichersystem geschrieben, bevor das Speicherziel entfernt wird. Dieser Vorgang kann eine Stunde oder länger dauern, wenn sich viele geänderte Daten im Cache befinden.

## <a name="change-a-blob-storage-targets-namespace-path"></a>Ändern des Namespacepfads eines Blobspeicherziels

Bei Namespacepfaden handelt es sich um die Pfade, die Clients verwenden, um dieses Speicherziel einzubinden. Weitere Informationen finden Sie unter [Planen des aggregierten Namespace](hpc-cache-namespace.md) und [Einrichten des aggregierten Namespace](add-namespace-paths.md).

Der Namespacepfad ist die einzige Aktualisierung, die Sie für ein Blobspeicherziel in Azure vornehmen können. Sie können das Azure-Portal oder die Azure CLI verwenden, um die Änderung vorzunehmen.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Verwenden Sie die Seite **Namespace** für Ihre Azure HPC Cache-Instanz. Die Seite „Namespace“ wird im Artikel [Einrichten des aggregierten Namespace](add-namespace-paths.md) im Detail beschrieben.

Klicken Sie auf den Namen des Pfads, den Sie ändern möchten, und erstellen Sie den neuen Pfad im Bearbeitungsfenster, das angezeigt wird.

![Screenshot der Seite „Namespace“ nach dem Klicken auf einen Blob-Namespacepfad. Die Bearbeitungsfelder werden rechts in einem Bereich angezeigt.](media/edit-namespace-blob.png)

Klicken Sie auf **OK**, nachdem Sie die Änderungen vorgenommen haben, um das Speicherziel zu aktualisieren, oder klicken Sie auf **Abbrechen**, um die Änderungen zu verwerfen.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

[Einrichten der Azure CLI für Azure HPC Cache](./az-cli-prerequisites.md).

Verwenden Sie den Befehl [az hpc-cache blob-storage-target update](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-update), um den Namespace eines Blobspeicherziels zu ändern. Nur der `--virtual-namespace-path`-Wert kann geändert werden.

  ```azurecli
  az hpc-cache blob-storage-target update --cache-name cache-name --name target-name \
    --resource-group rg --virtual-namespace-path "/new-path"
  ```

---

## <a name="update-an-nfs-storage-target"></a>Aktualisieren eines neuen NFS-Speicherziels

Bei NFS-Speicherzielen können Sie virtuelle Namespacepfade ändern oder hinzufügen, die Werte für den NFS-Export oder das Unterverzeichnis ändern, auf den ein Namespacepfad verweist, und das Nutzungsmodell ändern.

Details finden Sie unten:

* [Ändern aggregierter Namespacewerte](#change-aggregated-namespace-values) (virtueller Namespacepfad, Export und Exportunterverzeichnis)
* [Ändern des Nutzungsmodells](#change-the-usage-model)

### <a name="change-aggregated-namespace-values"></a>Ändern aggregierter Namespacewerte

Sie können das Azure-Portal oder die Azure CLI verwenden, um den clientseitigen Namespacepfad, den Speicherexport und das Exportunterverzeichnis (wenn verwendet) zu ändern.

Lesen Sie die Anleitung unter [NFS-Namespacepfade](add-namespace-paths.md#nfs-namespace-paths), wenn Sie wissen möchten, wie Sie mehrere gültige Pfade für ein Speicherziel erstellen.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Verwenden Sie die Seite **Namespace** für Ihre Azure HPC Cache-Instanz, um Namespacewerte zu aktualisieren. Diese Seite wird im Artikel [Einrichten des aggregierten Namespace](add-namespace-paths.md) im Detail beschrieben.

![Screenshot der Seite „Namespace“ im Portal mit der geöffneten NFS-Aktualisierungsseite rechts](media/update-namespace-nfs.png)

1. Klicken Sie auf den Namen des Pfads, den Sie ändern möchten.
1. Verwenden Sie das Bearbeitungsfenster, um den neuen virtuellen Pfad und die Werte für Export oder Unterverzeichnis einzugeben.
1. Klicken Sie auf **OK**, nachdem Sie die Änderungen vorgenommen haben, um das Speicherziel zu aktualisieren, oder klicken Sie auf **Abbrechen**, um die Änderungen zu verwerfen.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

[Einrichten der Azure CLI für Azure HPC Cache](./az-cli-prerequisites.md).

Verwenden Sie die ``--junction``-Option im [az hpc-cache nfs-storage-target update](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target)-Befehl, um den Namespacepfad, den NFS-Export oder das Exportunterverzeichnis zu ändern.

Der Parameter ``--junction`` verwendet diese Werte:

* ``namespace-path``: Der clientseitige virtuelle Dateipfad.
* ``nfs-export``: Der Speichersystemexport, der dem clientseitigen Pfad zugeordnet werden soll.
* ``target-path`` (optional): Ein Unterverzeichnis des Exports, falls erforderlich.

Beispiel: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

Sie müssen in der ``--junction``-Anweisung alle drei Werte für jeden Pfad angeben. Verwenden Sie die vorhandenen Werte für alle Werte, die Sie nicht ändern möchten.

Der Cachename, der Name des Speicherziels und die Ressourcengruppe sind für alle Aktualisierungsbefehle ebenfalls erforderlich.

Beispielbefehl:

```azurecli
az hpc-cache nfs-storage-target update --cache-name mycache \
  --name st-name --resource-group doc-rg0619 \
  --junction namespace-path="/new-path" nfs-export="/my-export" target-path="my-subdirectory"
```

---

### <a name="change-the-usage-model"></a>Ändern des Nutzungsmodells

Das Nutzungsmodell beeinflusst, wie Daten im Cache gespeichert werden. Weitere Informationen finden Sie unter [Auswählen eines Nutzungsmodells](hpc-cache-add-storage.md#choose-a-usage-model).

Wenn Sie das Nutzungsmodell für ein NFS-Speicherziel ändern möchten, verwenden Sie eine der folgenden Methoden.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Ändern Sie das Nutzungsmodell auf der Seite **Speicherziele** im Azure-Portal. Klicken Sie auf den Namen des Speicherziels, das Sie ändern möchten.

![Screenshot der Bearbeitungsseite für ein NFS-Speicherziel](media/edit-storage-nfs.png)

Verwenden Sie den Selektor im Dropdown, um ein neues Nutzungsmodell auszuwählen. Klicken Sie auf **OK**, um das Speicherziel zu aktualisieren, oder klicken Sie auf **Abbrechen**, um die Änderungen zu verwerfen.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

[Einrichten der Azure CLI für Azure HPC Cache](./az-cli-prerequisites.md).

Verwenden Sie den [az hpc-cache nfs-storage-target update](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target?view=azure-cli-latest#ext-hpc-cache-az-hpc-cache-nfs-storage-target-update)-Befehl.

Der Aktualisierungsbefehl ist beinahe identisch mit dem Befehl, den Sie verwenden, um ein NFS-Speicherziel hinzuzufügen. Details und Beispiele finden Sie unter [Erstellen eines NFS-Speicherziels](hpc-cache-add-storage.md#create-an-nfs-storage-target).

Aktualisieren Sie die Option ``--nfs3-usage-model``, um das Nutzungsmodell zu ändern. Beispiel: ``--nfs3-usage-model WRITE_WORKLOAD_15``

Die Werte für Cachename, für den Namen des Speicherziels und für die Ressourcengruppe sind ebenfalls erforderlich.

Wenn Sie die Namen der Nutzungsmodelle überprüfen möchten, verwenden Sie den Befehl [az hpc-cache usage-model list](/cli/azure/ext/hpc-cache/hpc-cache/usage-model#ext-hpc-cache-az-hpc-cache-usage-model-list).

Wenn der Cache beendet wurde oder sich nicht in einem fehlerfreien Zustand befindet, wird das Update angewendet, nachdem der Cache fehlerfrei ist.

---

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu diesen Optionen finden Sie unter [Hinzufügen von Speicherzielen](hpc-cache-add-storage.md).
* Weitere Tipps zur Verwendung virtueller Pfade finden Sie unter [Planen des aggregierten Namespace](hpc-cache-namespace.md).
