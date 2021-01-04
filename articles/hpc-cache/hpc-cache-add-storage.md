---
title: Hinzufügen von Speicher zu einem Azure HPC Cache
description: Definieren von Speicherzielen, damit Ihr Azure HPC Cache Ihr lokales NFS-System oder Azure-Blobcontainer für die langfristige Speicherung von Dateien verwenden kann
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: b2497a49703ab675bde50c7845995c92de32f376
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94657175"
---
# <a name="add-storage-targets"></a>Hinzufügen von Speicherzielen

*Speicherziele* sind Back-End-Speicher für Dateien, auf die über einen Azure HPC Cache zugegriffen wird. Sie können NFS-Speicher hinzufügen, z. B. ein lokales Hardwaresystem, oder Daten in Azure Blob Storage speichern.

Es können bis zu zehn verschiedene Speicherziele für einen Cache definiert werden. Der Cache stellt alle Speicherziele in einem aggregierten Namespace dar.

Die Namespacepfade werden separat konfiguriert, nachdem Sie die Speicherziele hinzugefügt haben. Im Allgemeinen kann ein NFS-Speicherziel über bis zu zehn Namespacepfade verfügen, oder bei manchen umfangreichen Konfigurationen über mehr. Details finden Sie unter [NFS-Namespacepfade](add-namespace-paths.md#nfs-namespace-paths).

Beachten Sie, dass der Zugriff auf die Speicherexporte vom virtuellen Netzwerk Ihres Caches aus möglich sein muss. Für einen lokalen Hardwarespeicher müssen Sie möglicherweise einen DNS-Server einrichten, der Hostnamen für den NFS-Speicherzugriff auflösen kann. Weitere Informationen finden Sie unter [DNS-Zugriff](hpc-cache-prerequisites.md#dns-access).

Fügen Sie nach dem Erstellen des Caches Speicherziele hinzu. Folgen Sie diesem Prozess:

1. [Erstellen des Caches](hpc-cache-create.md)
1. Definieren eines Speicherziels (Informationen in diesem Artikel)
1. [Erstellen Sie die clientseitigen Pfade](add-namespace-paths.md) (für den [aggregierten Namespace](hpc-cache-namespace.md)).

Das Verfahren zum Hinzufügen eines Speicherziels unterscheidet sich geringfügig, je nachdem, ob Sie Azure Blobspeicher oder einen NFS-Export hinzufügen. Details zu beiden Szenarien finden Sie unten.

Klicken Sie auf das Bild unten, um eine [Videodemonstration](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/) der Cacheerstellung und des Hinzufügens eines Speicherziels im Azure-Portal anzusehen.

[![Videominiaturansicht: Azure HPC Cache: Setup (klicken Sie, um die Videoseite aufzurufen)](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="add-a-new-azure-blob-storage-target"></a>Hinzufügen eines neuen Azure Blobspeicherziels

Für ein neues Blobspeicherziel ist ein leerer Blobcontainer oder ein Container erforderlich, der mit Daten im Format des Azure HPC Cache-Clouddateisystems aufgefüllt ist. Weitere Informationen zum Vorabladen eines Blobcontainers finden Sie unter [Daten in Azure-Blobspeicher verschieben](hpc-cache-ingest.md).

Im Azure-Portal finden Sie auf der Seite **Speicherziel hinzufügen** die Option, mit der Sie einen neuen Blobcontainer direkt vor dem Hinzufügen erstellen können.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Öffnen Sie im Azure-Portal Ihre Cache-Instanz, und klicken Sie in der linken Seitenleiste auf **Speicherziele**.

![Screenshot der Seite „Einstellungen > Speicherziel“ mit zwei vorhandenen Speicherzielen in einer Tabelle und einer Hervorhebung um die Schaltfläche „+ Speicherziel hinzufügen“ oberhalb der Tabelle.](media/add-storage-target-button.png)

Auf der Seite **Speicherziele** werden alle vorhandenen Ziele und ein Link zum Hinzufügen eines neuen Ziels aufgelistet.

Klicken Sie auf die Schaltfläche **Speicherziel hinzufügen**.

![Screenshot der Seite „Speicherziel hinzufügen“, aufgefüllt mit Informationen für ein neues Azure-Blobspeicherziel](media/hpc-cache-add-blob.png)

Geben Sie diese Informationen ein, um einen Azure Blobcontainer zu definieren.

* **Name des Speicherziels**: Legen Sie einen Namen fest, der dieses Speicherziel im Azure HPC Cache identifiziert.
* **Zieltyp**: Wählen Sie **Blob** aus.
* **Speicherkonto**: Wählen Sie das Speicherkonto aus, das Sie verwenden möchten.

  Sie müssen die Cache-Instanz für den Zugriff auf das Speicherkonto autorisieren, wie unter [Hinzufügen der Zugriffsrollen](#add-the-access-control-roles-to-your-account) beschrieben.

  Informationen zur Art des Speicherkontos, das Sie verwenden können, finden Sie unter [Blobspeicheranforderungen](hpc-cache-prerequisites.md#blob-storage-requirements).

* **Speichercontainer**: Wählen Sie den Blobcontainer für dieses Ziel aus, oder klicken Sie auf **Neu erstellen**.

  ![Screenshot des Dialogfelds zum Angeben von Name und Zugriffsebene (privat) für neuen Container](media/add-blob-new-container.png)

Klicken Sie abschließend auf **OK**, um das Speicherziel hinzuzufügen.

> [!NOTE]
> Wenn Ihre Speicherkontofirewall so festgelegt ist, dass der Zugriff nur auf „ausgewählte Netzwerke“ eingeschränkt wird, verwenden Sie die temporäre Problemumgehung, die unter [Problemumgehung für die Firewalleinstellungen von Blobspeicherkonten](hpc-cache-blob-firewall-fix.md) dokumentiert ist.

### <a name="add-the-access-control-roles-to-your-account"></a>Hinzufügen der Zugriffssteuerungsrollen zu Ihrem Konto

Azure HPC Cache verwendet die [rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)](../role-based-access-control/index.yml), um den Cachedienst für den Zugriff auf Ihr Speicherkonto für Azure-Blobspeicherziele zu autorisieren.

Der Besitzer des Speicherkontos muss die Rollen [Speicherkontomitwirkender](../role-based-access-control/built-in-roles.md#storage-account-contributor) und [Mitwirkender an Storage-Blobdaten](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) für den Benutzer „HPC Cache-Ressourcenanbieter“ explizit hinzufügen.

Sie können dies im Voraus erledigen oder indem Sie auf einen Link auf der Seite klicken, auf der Sie ein Blobspeicherziel hinzufügen. Beachten Sie, dass es bis zu fünf Minuten dauern kann, bis die Rolleneinstellungen in der Azure-Umgebung weitergegeben werden. Daher sollten Sie nach dem Hinzufügen der Rollen einige Minuten warten, bevor Sie ein Speicherziel erstellen.

Nachfolgend finden Sie die Schritte zum Hinzufügen der Azure-Rollen:

1. Öffnen Sie die Seite **Zugriffssteuerung (IAM)** für das Speicherkonto. (Der Link auf der Seite **Speicherziel hinzufügen** öffnet diese Seite für das ausgewählte Konto automatisch.)

1. Klicken Sie auf die **+** oben auf der Seite, und wählen Sie **Rollenzuweisung hinzufügen** aus.

1. Wählen Sie in der Liste die Rolle "Speicherkontomitwirkender" aus.

1. Übernehmen Sie im Feld **Zugriff zuweisen zu** den Standardwert ("Azure AD-Benutzer, -Gruppe oder -Dienstprinzipal").  

1. Suchen Sie im Feld **Auswählen** nach „hpc“.  Diese Zeichenfolge sollte mit einem Dienstprinzipal mit dem Namen "HPC Cache-Ressourcenanbieter" übereinstimmen. Klicken Sie auf diesen Prinzipal, um ihn auszuwählen.

   > [!NOTE]
   > Wenn eine Suche nach „hpc“ nicht funktioniert, versuchen Sie stattdessen, die Zeichenfolge „storagecache“ zu verwenden. Benutzer, die an Vorschauen (vor der allgemeinen Verfügbarkeit) teilgenommen haben, müssen möglicherweise den älteren Namen für den Dienstprinzipal verwenden.

1. Klicken Sie unten auf die Schaltfläche **Speichern**.

1. Wiederholen Sie diesen Vorgang, um die Rolle "Mitwirkender an Storage-Blobdaten" zuzuweisen.  

![Screenshot des GUI zum Hinzufügen von Rollenzuweisungen](media/hpc-cache-add-role.png)

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

### <a name="prerequisite-storage-account-access"></a>Voraussetzung: Zugriff auf das Speicherkonto

[Einrichten der Azure CLI für Azure HPC Cache](./az-cli-prerequisites.md).

Vergewissern Sie sich vor dem Hinzufügen eines Blobspeicherziels, dass der Cache über die richtigen Rollen für den Zugriff auf das Speicherkonto verfügt und die Firewalleinstellungen die Erstellung des Speicherziels zulassen.

Azure HPC Cache verwendet die [rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)](../role-based-access-control/index.yml), um den Cachedienst für den Zugriff auf Ihr Speicherkonto für Azure-Blobspeicherziele zu autorisieren.

Der Besitzer des Speicherkontos muss die Rollen [Speicherkontomitwirkender](../role-based-access-control/built-in-roles.md#storage-account-contributor) und [Mitwirkender an Storage-Blobdaten](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) für den Benutzer „HPC Cache-Ressourcenanbieter“ explizit hinzufügen.

Wenn der Cache nicht über diese Rollen verfügt, kann das Speicherziel nicht erstellt werden.

Es kann bis zu fünf Minuten dauern, bis die Rolleneinstellungen in der Azure-Umgebung weitergegeben werden. Daher sollten Sie nach dem Hinzufügen der Rollen einige Minuten warten, bevor Sie ein Speicherziel erstellen.

Ausführliche Anweisungen finden Sie unter [Hinzufügen oder Entfernen von Azure-Rollenzuweisungen mithilfe der Azure-Befehlszeilenschnittstelle](../role-based-access-control/role-assignments-cli.md).

Überprüfen Sie auch die Firewalleinstellungen Ihres Speicherkontos. Wenn in den Firewalleinstellungen der Zugriff nur auf „ausgewählte Netzwerke“ beschränkt ist, kann das Speicherziel möglicherweise nicht erstellt werden. Verwenden Sie die im Artikel [Umgehen der Firewalleinstellungen für Blobspeicherkonten](hpc-cache-blob-firewall-fix.md) beschriebene Problemumgehung.

### <a name="add-a-blob-storage-target-with-azure-cli"></a>Hinzufügen eines Blobspeicherziels mit der Azure-Befehlszeilenschnittstelle

Verwenden Sie den Schnittstellenbefehl [az hpc-cache blob-storage-target add](/cli/azure/ext/hpc-cache/hpc-cache/blob-storage-target#ext-hpc-cache-az-hpc-cache-blob-storage-target-add), um ein Azure-Blobspeicherziel zu definieren.

> [!NOTE]
> Für die Azure CLI-Befehle ist es derzeit erforderlich, dass Sie beim Hinzufügen eines Speicherziels einen Namespacepfad hinzufügen. Dies unterscheidet sich von dem Prozess, der mit der Azure-Portalschnittstelle verwendet wird.

Zusätzlich zu den Standardparametern für die Ressourcengruppe und den Cachenamen müssen Sie die folgenden Optionen für das Speicherziel angeben:

* ``--name``: Legen Sie einen Namen fest, der dieses Speicherziel im Azure HPC Cache identifiziert.

* ``--storage-account``: Geben Sie den Kontobezeichner im folgenden Format an: /subscriptions/ *<Abonnement-ID>* /resourceGroups/ *<Speicherressourcengruppe>* /providers/Microsoft.Storage/storageAccounts/ *<Kontoname>*

  Informationen zur Art des Speicherkontos, das Sie verwenden können, finden Sie unter [Blobspeicheranforderungen](hpc-cache-prerequisites.md#blob-storage-requirements).

* ``--container-name``: Geben Sie den Namen des Containers an, der für dieses Speicherziel verwendet werden soll.

* ``--virtual-namespace-path``: Legen Sie den clientseitigen Dateipfad für dieses Speicherziel fest. Setzen Sie Pfadnamen in Anführungszeichen. Weitere Informationen zum Feature des virtuellen Namespace finden Sie unter [Planen des aggregierten Namespace](hpc-cache-namespace.md).

Beispielbefehl:

```azurecli
az hpc-cache blob-storage-target add --resource-group "hpc-cache-group" \
    --cache-name "cache1" --name "blob-target1" \
    --storage-account "/subscriptions/<subscriptionID>/resourceGroups/myrgname/providers/Microsoft.Storage/storageAccounts/myaccountname" \
    --container-name "container1" --virtual-namespace-path "/blob1"
```

---

## <a name="add-a-new-nfs-storage-target"></a>Hinzufügen eines neuen NFS-Speicherziels

Ein NFS-Speicherziel besitzt andere Einstellungen als ein Blob-Speicherziel. Mithilfe der Einstellung „Verwendungsmodell“ kann der Cache Daten aus diesem Speichersystem effizient zwischenspeichern.

![Screenshot der Seite zum Hinzufügen von Speicherzielen mit definiertem NFS-Ziel](media/add-nfs-target.png)

> [!NOTE]
> Stellen Sie vor dem Erstellen eines NFS-Speicherziels sicher, dass das Speichersystem über den Azure HPC Cache zugänglich ist und die Berechtigungsanforderungen erfüllt. Das Erstellen des Speicherziels schlägt fehl, wenn der Cache auf das Speichersystem nicht zugreifen kann. Einzelheiten dazu finden Sie in [NFS-Speicheranforderungen](hpc-cache-prerequisites.md#nfs-storage-requirements) und [Behandeln von Problemen mit der NAS-Konfiguration und dem NFS-Speicherziel](troubleshoot-nas.md).

### <a name="choose-a-usage-model"></a>Auswählen eines Nutzungsmodells
<!-- referenced from GUI - update aka.ms link if you change this heading -->

Wenn Sie ein Speicherziel erstellen, das auf ein NFS-Speichersystem verweist, müssen Sie das Nutzungsmodell für dieses Ziel auswählen. Dieses Modell bestimmt, wie Ihre Daten zwischengespeichert werden.

Drei Optionen stehen zur Verfügung:

* **Leseintensive, unregelmäßige Schreibvorgänge**: Verwenden Sie diese Option, wenn Sie den Lesezugriff auf Dateien beschleunigen möchten, die statisch sind oder selten geändert werden.

  Mit dieser Option werden von Clients gelesene Dateien zwischengespeichert, die Schreibvorgänge werden jedoch sofort an den Back-End-Speicher übergeben. Im Cache gespeicherte Dateien werden niemals mit den Dateien auf dem NFS-Speichervolume verglichen.

  Verwenden Sie diese Option nicht, wenn das Risiko besteht, dass eine Datei direkt im Speichersystem geändert werden kann, ohne sie zuvor in den Cache zu schreiben. In diesem Fall wird die zwischengespeicherte Version der Datei niemals mit Änderungen aus dem Back-End aktualisiert, und das Dataset kann inkonsistent werden.

* **Mehr als 15 % Schreibvorgänge**: Diese Option beschleunigt die Lese-und Schreibleistung. Wenn diese Option verwendet wird, müssen alle Clients über den Azure HPC-Cache auf Dateien zugreifen, anstatt den Back-End-Speicher direkt einzubinden. Die zwischengespeicherten Dateien enthalten aktuelle Änderungen, die nicht auf dem Back-End gespeichert werden.

  Bei diesem Nutzungsmodell werden Dateien im Cache nicht mit den Dateien im Back-End-Speicher verglichen. Es wird davon ausgegangen, dass die zwischengespeicherte Version der Datei aktueller ist. Eine geänderte Datei im Cache wird in das Back-End-Speichersystem geschrieben, nachdem sie sich eine Stunde lang ohne zusätzliche Änderungen im Cache befunden hat.

* **Clients umgehen den Cache und schreiben in das NFS-Ziel**: Wählen Sie diese Option aus, wenn Clients in Ihrem Workflow Daten direkt in das Speichersystem schreiben, ohne zuvor in den Cache zu schreiben. Die von Clients angeforderten Dateien werden zwischengespeichert, aber alle Änderungen an diesen Dateien vom Client werden sofort an das Back-End-Speichersystem zurückgegeben.

  Bei diesem Nutzungsmodell werden die Dateien im Cache häufig anhand der Back-End-Versionen auf Updates überprüft. Bei dieser Überprüfung können Dateien außerhalb des Caches geändert werden, während die Datenkonsistenz gewahrt bleibt.

In dieser Tabelle werden die Unterschiede im Nutzungsmodell zusammengefasst:

| Nutzungsmodell                   | Cachemodus | Back-End-Überprüfung | Maximale Zurückschreibverzögerung |
|-------------------------------|--------------|-----------------------|--------------------------|
| Leseintensiv, unregelmäßige Schreibvorgänge | Lesen         | Nie                 | Keine                     |
| Mehr als 15 % Schreibvorgänge       | Lesen/Schreiben   | Nie                 | 1 Stunde                   |
| Clients umgehen den Cache      | Lesen         | 30 Sekunden            | Keine                     |

### <a name="create-an-nfs-storage-target"></a>Erstellen eines NFS-Speicherziels

### <a name="portal"></a>[Portal](#tab/azure-portal)

Öffnen Sie im Azure-Portal Ihre Cache-Instanz, und klicken Sie in der linken Seitenleiste auf **Speicherziele**.

![Screenshot der Seite „Einstellungen > Speicherziel“ mit zwei vorhandenen Speicherzielen in einer Tabelle und einer Hervorhebung um die Schaltfläche „+ Speicherziel hinzufügen“ oberhalb der Tabelle.](media/add-storage-target-button.png)

Auf der Seite **Speicherziele** werden alle vorhandenen Ziele und ein Link zum Hinzufügen eines neuen Ziels aufgelistet.

Klicken Sie auf die Schaltfläche **Speicherziel hinzufügen**.

![Screenshot der Seite zum Hinzufügen von Speicherzielen mit definiertem NFS-Ziel](media/add-nfs-target.png)

Geben Sie diese Informationen für ein NFS-gestütztes Speicherziel an:

* **Name des Speicherziels**: Legen Sie einen Namen fest, der dieses Speicherziel im Azure HPC Cache identifiziert.

* **Zieltyp**: Wählen Sie **NFS** aus.

* **Hostname**: Geben Sie die IP-Adresse oder den vollqualifizierten Domänennamen Ihres NFS-Speichersystems ein. (Verwenden Sie nur dann einen Domänennamen, wenn Ihr Cache Zugriff auf einen DNS-Server hat, der den Namen auflösen kann.)

* **Nutzungsmodell**: Wählen Sie auf der Grundlage Ihres Workflows eines der oben im Abschnitt [Auswählen eines Nutzungsmodells](#choose-a-usage-model) beschriebenen Datencacheprofile aus.

Klicken Sie abschließend auf **OK**, um das Speicherziel hinzuzufügen.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

[Einrichten der Azure CLI für Azure HPC Cache](./az-cli-prerequisites.md).

Verwenden Sie den Azure CLI-Befehl [az hpc-cache nfs-storage-target add](/cli/azure/ext/hpc-cache/hpc-cache/nfs-storage-target#ext-hpc-cache-az-hpc-cache-nfs-storage-target-add), um das Speicherziel zu erstellen.

> [!NOTE]
> Für die Azure CLI-Befehle ist es derzeit erforderlich, dass Sie beim Hinzufügen eines Speicherziels einen Namespacepfad hinzufügen. Dies unterscheidet sich von dem Prozess, der mit der Azure-Portalschnittstelle verwendet wird.

Geben Sie zusätzlich zum Cachenamen und zur Cacheressourcengruppe die folgenden Werte an:

* ``--name``: Legen Sie einen Namen fest, der dieses Speicherziel im Azure HPC Cache identifiziert.
* ``--nfs3-target``: Die IP-Adresse Ihres NFS-Speichersystems. (Sie können hier einen vollqualifizierten Domänennamen verwenden, wenn Ihr Cache Zugriff auf einen DNS-Server hat, der den Namen auflösen kann.)
* ``--nfs3-usage-model``: Geben Sie eines der oben im Abschnitt [Auswählen eines Nutzungsmodells](#choose-a-usage-model) beschriebenen Datencacheprofile an.

  Überprüfen Sie die Namen der Nutzungsmodelle mithilfe des Befehls [az hpc-cache usage-model list](/cli/azure/ext/hpc-cache/hpc-cache/usage-model#ext-hpc-cache-az-hpc-cache-usage-model-list).

* ``--junction``: Der „junction“-Parameter verknüpft den clientseitigen virtuellen Dateipfad mit einem Exportpfad auf dem Speichersystem.

  Ein NFS-Speicherziel kann mehrere virtuelle Pfade aufweisen, solange jeder Pfad ein anderes Export- oder Unterverzeichnis in demselben Speichersystem darstellt. Erstellen Sie alle Pfade für ein Speicherziel auf einem Speichersystem.

  Sie können [Namespacepfade](add-namespace-paths.md) für ein Speicherziel jederzeit hinzufügen und bearbeiten.

  Der Parameter ``--junction`` verwendet diese Werte:

  * ``namespace-path``: Der clientseitige virtuelle Dateipfad.
  * ``nfs-export``: Der Speichersystemexport, der dem clientseitigen Pfad zugeordnet werden soll.
  * ``target-path`` (optional): Ein Unterverzeichnis des Exports, falls erforderlich.

  Beispiel: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

  Weitere Informationen zum Feature virtueller Namespace finden Sie unter [Aggregierten Namespace konfigurieren](hpc-cache-namespace.md).

Beispielbefehl:

```azurecli

az hpc-cache nfs-storage-target add --resource-group "hpc-cache-group" --cache-name "doc-cache0629" \
    --name nfsd1 --nfs3-target 10.0.0.4 --nfs3-usage-model WRITE_WORKLOAD_15 \
    --junction namespace-path="/nfs1/data1" nfs-export="/datadisk1" target-path=""
```

Ausgabe:
```azurecli

{- Finished ..
  "clfs": null,
  "id": "/subscriptions/<subscriptionID>/resourceGroups/hpc-cache-group/providers/Microsoft.StorageCache/caches/doc-cache0629/storageTargets/nfsd1",
  "junctions": [
    {
      "namespacePath": "/nfs1/data1",
      "nfsExport": "/datadisk1",
      "targetPath": ""
    }
  ],
  "location": "eastus",
  "name": "nfsd1",
  "nfs3": {
    "target": "10.0.0.4",
    "usageModel": "WRITE_WORKLOAD_15"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "hpc-cache-group",
  "targetType": "nfs3",
  "type": "Microsoft.StorageCache/caches/storageTargets",
  "unknown": null
}

```

---

## <a name="view-storage-targets"></a>Anzeigen von Speicherzielen

Sie können das Azure-Portal oder die Azure CLI verwenden, um die bereits für Ihren Cache definierten Speicherziele anzuzeigen.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Öffnen Sie im Azure-Portal Ihre Cache-Instanz, und klicken Sie in der linken Seitenleiste unter der Überschrift „Einstellungen“ auf **Speicherziele**. Auf der Seite „Speicherziele“ werden alle vorhandenen Ziele sowie Steuerelemente zum Hinzufügen oder Löschen von Zielen aufgelistet.

Klicken Sie auf den Namen eines Speicherziels, um seine zugehörige Detailseite zu öffnen.

Weitere Informationen finden Sie unter [Bearbeiten von Speicherzielen](hpc-cache-edit-storage.md).

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

[Einrichten der Azure CLI für Azure HPC Cache](./az-cli-prerequisites.md).

Verwenden Sie die Option [az hpc-cache storage-target list](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-list), um die vorhandenen Speicherziele für einen Cache anzuzeigen. Geben Sie den Cachenamen und die Ressourcengruppe an (sofern Sie dies nicht global festgelegt haben).

```azurecli
az hpc-cache storage-target list --resource-group "scgroup" --cache-name "sc1"
```

Verwenden Sie [az hpc-cache storage-target show](/cli/azure/ext/hpc-cache/hpc-cache/storage-target#ext-hpc-cache-az-hpc-cache-storage-target-list), um Details zu einem bestimmten Speicherziel anzuzeigen. (Geben Sie das Speicherziel anhand des Namens an.)

Beispiel:

```azurecli
$ az hpc-cache storage-target show --cache-name doc-cache0629 --name nfsd1

{
  "clfs": null,
  "id": "/subscriptions/<subscription_ID>/resourceGroups/scgroup/providers/Microsoft.StorageCache/caches/doc-cache0629/storageTargets/nfsd1",
  "junctions": [
    {
      "namespacePath": "/nfs1/data1",
      "nfsExport": "/datadisk1",
      "targetPath": ""
    }
  ],
  "location": "eastus",
  "name": "nfsd1",
  "nfs3": {
    "target": "10.0.0.4",
    "usageModel": "WRITE_WORKLOAD_15"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "scgroup",
  "targetType": "nfs3",
  "type": "Microsoft.StorageCache/caches/storageTargets",
  "unknown": null
}
$
```

---

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Speicherziele erstellt haben, fahren Sie mit diesen Aufgaben fort, um Ihren Cache einsatzbereit zu machen:

* [Einrichten des aggregierten Namespace](add-namespace-paths.md)
* [Einbinden der Azure HPC Cache-Instanz](hpc-cache-mount.md)
* [Daten in Azure-Blobspeicher verschieben](hpc-cache-ingest.md)

Wenn Sie Einstellungen aktualisieren müssen, können Sie ein [Speicherziel bearbeiten](hpc-cache-edit-storage.md).