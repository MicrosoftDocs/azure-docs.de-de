---
title: Konfigurieren des aggregierten Azure HPC Cache-Namespace
description: Erstellen von clientseitigen Pfaden für den Back-End-Speicher mit Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: e525fc0705dffcd4765e6a1f6c5235bdef260fcd
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96339675"
---
# <a name="set-up-the-aggregated-namespace"></a>Einrichten des aggregierten Namespace

Nachdem Sie Speicherziele erstellt haben, müssen Sie für diese auch Namespacepfade erstellen. Clientcomputer verwenden diese virtuellen Pfade, um über den Cache auf Dateien zuzugreifen, anstatt direkt eine Verbindung mit dem Back-End-Speicher herzustellen. Mit diesem System können Cacheadministratoren Back-End-Speichersysteme ändern, ohne Clientanweisungen neu schreiben zu müssen.

Weitere Informationen zu dieser Funktion finden Sie unter [Planen des aggregierten Namespace](hpc-cache-namespace.md).

Auf der Seite **Namespace** im Azure-Portal werden die Pfade angezeigt, die Clients verwenden, um über den Cache auf Ihre Daten zuzugreifen. Auf dieser Seite können Sie Namespacepfade erstellen, entfernen oder ändern. Sie können Namespacepfade auch über die Azure-Befehlszeilenschnittstelle konfigurieren.

Alle vorhandenen clientseitigen Pfade sind auf der Seite **Namespace** aufgeführt. Wenn ein Speicherziel keine Pfade umfasst, wird es nicht in der Tabelle angezeigt.

Durch Klicken auf die Pfeile können Sie die Tabellenspalten sortieren und sich mit dem aggregierten Namespace des Caches vertraut machen.

![Screenshot der Seite „Namespace“ im Portal mit zwei Pfaden in einer Tabelle Spaltenüberschriften: „Namespacepfad“, „Speicherziel“, „Exportpfad“ und „Unterverzeichnis für Export“. Die Elemente in der ersten Spalte sind klickbare Links. Schaltflächen im oberen Bereich: „Namespacepfad hinzufügen“, „Aktualisieren“, „Löschen“](media/namespace-page.png)

## <a name="add-or-edit-client-facing-namespace-paths"></a>Hinzufügen oder Bearbeiten von clientseitigen Namespacepfaden

Sie müssen mindestens einen Namespacepfad erstellen, damit Clients auf das Speicherziel zugreifen können. (Weitere Informationen zum Clientzugriff finden Sie unter [Einbinden einer Azure HPC Cache-Instanz](hpc-cache-mount.md).)

### <a name="blob-namespace-paths"></a>Blob-Namespacepfade

Ein Azure Blob Storage-Ziel kann nur einen Namespacepfad enthalten.

Befolgen Sie die nachstehenden Anweisungen, um den Pfad über das Azure-Portal oder die Azure-Befehlszeilenschnittstelle festzulegen oder zu ändern.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Öffnen Sie im Azure-Portal die Einstellungsseite **Namespace**. Auf dieser Seite können Sie Namespacepfade hinzufügen, ändern oder löschen.

* **Hinzufügen eines neuen Pfads:** Klicken Sie oben auf die Schaltfläche **+ Hinzufügen**, und geben Sie die entsprechenden Informationen im Bearbeitungsbereich ein.

  * Wählen Sie in der Dropdownliste das Speicherziel aus. (In diesem Screenshot kann das Blob Storage-Ziel nicht ausgewählt werden, da es bereits über einen Namespacepfad verfügt.)

    ![Screenshot der Bearbeitungsfelder des neuen Namespace mit hervorgehobener Auswahlliste des Speicherziels](media/namespace-select-storage-target.png)

  * Für ein Azure Blob Storage-Ziel werden die Export- und Unterverzeichnispfade automatisch auf ``/`` festgelegt.

* **Ändern eines vorhandenen Pfads:** Klicken Sie auf den Namespacepfad. Der Bearbeitungsbereich wird geöffnet, und Sie können den Pfad ändern.

  ![Screenshot der Seite „Namespace“ nach dem Klicken auf einen Blob-Namespacepfad. Die Bearbeitungsfelder werden rechts in einem Bereich angezeigt.](media/edit-namespace-blob.png)

* **Löschen eines Namespacepfads:** Aktivieren Sie das Kontrollkästchen links neben dem Pfad, und klicken Sie auf die Schaltfläche **Löschen**.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

[Einrichten der Azure CLI für Azure HPC Cache](./az-cli-prerequisites.md).

Wenn Sie die Azure-Befehlszeilenschnittstelle verwenden, müssen Sie beim Erstellen des Speicherziels einen Namespacepfad hinzufügen. Weitere Informationen finden Sie unter [Hinzufügen eines neuen Azure Blob Storage-Ziels](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-azure-blob-storage-target).

Verwenden Sie den Befehl [az hpc-cache blob-storage-target update](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-update), um den Namespacepfad eines Ziels zu aktualisieren. Die Argumente für den update-Befehl ähneln den Argumenten vom create-Befehl, mit dem Unterschied, dass der Containername oder das Speicherkonto nicht übergeben werden.

Über die Azure-Befehlszeilenschnittstelle können Sie einen Namespacepfad nicht aus einem Blob Storage-Ziel löschen, Sie können aber den Pfad mit einem anderen Wert überschreiben.

---

### <a name="nfs-namespace-paths"></a>NFS-Namespacepfade

Ein NFS-Speicherziel kann mehrere virtuelle Pfade aufweisen, solange jeder Pfad ein anderes Export- oder Unterverzeichnis in demselben Speichersystem darstellt.

Wenn Sie den Namespace für ein NFS-Speicherziel planen, beachten Sie, dass jeder Pfad eindeutig sein muss und kein Unterverzeichnis eines anderen Namespacepfads sein darf. Bei dem Namespacepfad ``/parent-a`` können Sie beispielsweise keine Namespacepfade wie ``/parent-a/user1`` und ``/parent-a/user2`` erstellen. Diese Verzeichnispfade sind im Namespace bereits als Unterverzeichnisse von ``/parent-a`` verfügbar.

Alle Namespacepfade für ein NFS-Speichersystem werden in einem Speicherziel erstellt. Die meisten Cachekonfigurationen können bis zu zehn Namespacepfade pro Speicherziel unterstützen, größere Konfigurationen sogar bis zu 20.

Im Folgenden ist die maximale Anzahl der Namespacepfade pro Konfiguration aufgeführt.

* Bei einem Durchsatz von bis zu 2 GB/s:

  * 3 TB Cache – 10 Namespacepfade
  * 6 TB Cache – 10 Namespacepfade
  * 23 TB Cache – 20 Namespacepfade

* Bei einem Durchsatz von bis zu 4 GB/s:

  * 6 TB Cache – 10 Namespacepfade
  * 12 TB Cache – 10 Namespacepfade
  * 24 TB Cache – 20 Namespacepfade

* Bei einem Durchsatz von bis zu 8 GB/s:

  * 12 TB Cache – 10 Namespacepfade
  * 24 TB Cache – 10 Namespacepfade
  * 48 TB Cache – 20 Namespacepfade

Geben Sie für jeden NFS-Namespacepfad den clientseitigen Pfad, das Verzeichnis für den Speichersystemexport und optional ein Exportunterverzeichnis an.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Öffnen Sie im Azure-Portal die Einstellungsseite **Namespace**. Auf dieser Seite können Sie Namespacepfade hinzufügen, bearbeiten oder löschen.

* **So fügen Sie einen neuen Pfad hinzu** Klicken Sie oben auf die Schaltfläche **+ Hinzufügen**, und geben Sie die entsprechenden Informationen im Bearbeitungsbereich ein.
* **So ändern Sie einen vorhandenen Pfad** Klicken Sie auf den Namespacepfad. Der Bearbeitungsbereich wird geöffnet, und Sie können den Pfad ändern.
* **So löschen Sie einen Namespacepfad** Aktivieren Sie das Kontrollkästchen links neben dem Pfad, und klicken Sie auf die Schaltfläche **Löschen**.

Geben Sie diese Werte für die einzelnen Namespacepfade ein:

* **Namespacepfad:** der clientseitige Dateipfad.

* **Speicherziel:** Wenn Sie einen neuen Namespacepfad erstellen, wählen Sie im Dropdownmenü ein Speicherziel aus.

* **Exportpfad:** Geben Sie den Pfad zum NFS-Export ein. Achten Sie darauf, den Exportnamen richtig einzugeben. Im Portal wird zwar die Syntax für dieses Feld überprüft, der Export wird jedoch erst überprüft, wenn die Änderung übermittelt wird.

* **Unterverzeichnis für Export:** Wenn über den Pfad ein bestimmtes Unterverzeichnis des Exports eingebunden werden soll, geben Sie es hier ein. Lassen Sie dieses Feld andernfalls leer.

![Screenshot der Seite „Namespace“ im Portal mit der geöffneten Aktualisierungsseite rechts](media/update-namespace-nfs.png)

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

[Einrichten der Azure CLI für Azure HPC Cache](./az-cli-prerequisites.md).

Wenn Sie die Azure-Befehlszeilenschnittstelle verwenden, müssen Sie beim Erstellen des Speicherziels mindestens einen Namespacepfad hinzufügen. Weitere Informationen finden Sie unter [Hinzufügen eines neuen NFS-Speicherziels](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-nfs-storage-target).

Verwenden Sie den Befehl [az hpc-cache nfs-storage-target update](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target#ext-hpc-cache-az-hpc-cache-nfs-storage-target-update), um den Namespacepfad des Ziels zu aktualisieren oder weitere Pfade hinzuzufügen. Verwenden Sie die Option ``--junction``, um alle gewünschten Namespacepfade anzugeben.

Die für den update-Befehl verwendeten Optionen ähneln denen vom create-Befehl, mit dem Unterschied, dass die Informationen zum Speichersystem (IP-Adresse oder Hostname) nicht übergeben werden und dass das Verwendungsmodell optional ist. Weitere Informationen zur Syntax der Option ``--junction`` finden Sie unter [Hinzufügen eines neuen NFS-Speicherziels](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-nfs-storage-target).

---

## <a name="next-steps"></a>Nächste Schritte

Nach dem Erstellen des aggregierten Namespace für Ihre Speicherziele können Sie Clients im Cache einbinden. Weitere Informationen finden Sie in den folgenden Artikeln.

* [Einbinden der Azure HPC Cache-Instanz](hpc-cache-mount.md)
* [Daten in Azure-Blobspeicher verschieben](hpc-cache-ingest.md)
