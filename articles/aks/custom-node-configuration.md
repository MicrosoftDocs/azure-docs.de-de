---
title: Anpassen der Knotenkonfiguration für AKS-Knotenpools (Azure Kubernetes Service) (Vorschau)
description: Erfahren Sie, wie Sie die Konfiguration für AKS-Clusterknoten (Azure Kubernetes Service) und -Knotenpools anpassen.
ms.service: container-service
ms.topic: article
ms.date: 12/03/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 589081149d08983d3cd5a4a8822873f5a6cfca0e
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99559445"
---
# <a name="customize-node-configuration-for-azure-kubernetes-service-aks-node-pools-preview"></a>Anpassen der Knotenkonfiguration für AKS-Knotenpools (Azure Kubernetes Service) (Vorschau)

Durch Anpassen der Knotenkonfiguration können Sie die Betriebssystemeinstellungen oder die kubelet-Parameter konfigurieren oder optimieren, um die Anforderungen der Workloads zu erfüllen. Wenn Sie einen AKS-Cluster erstellen oder dem Cluster einen Knotenpool hinzufügen, können Sie eine Teilmenge der häufig verwendeten Betriebssystem- und kubelet-Einstellungen anpassen. Um Einstellungen über diese Teilmenge hinaus zu konfigurieren, [verwenden Sie einen Daemonsatz, um die erforderlichen Konfigurationen anzupassen, ohne AKS-Unterstützung für Ihre Knoten zu verlieren](support-policies.md#shared-responsibility).

## <a name="register-the-customnodeconfigpreview-preview-feature"></a>Registrieren der Previewfunktion `CustomNodeConfigPreview`

Um einen AKS-Cluster oder einen Knotenpool zu erstellen, der die kubelet-Parameter oder die Betriebssystemeinstellungen anpassen kann, müssen Sie das Featureflag `CustomNodeConfigPreview` für Ihr Abonnement aktivieren.

Registrieren Sie das Featureflag `CustomNodeConfigPreview` mithilfe des Befehls [az feature register][az-feature-register], wie im folgenden Beispiel gezeigt:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "CustomNodeConfigPreview"
```

Es dauert einige Minuten, bis der Status *Registered (Registriert)* angezeigt wird. Überprüfen Sie den Registrierungsstatus mithilfe des Befehls [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/CustomNodeConfigPreview')].{Name:name,State:properties.state}"
```

Wenn der Vorgang abgeschlossen ist, können Sie die Registrierung des *Microsoft.ContainerService*-Ressourcenanbieters mit dem Befehl [az provider register][az-provider-register] aktualisieren:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="install-aks-preview-cli-extension"></a>Installieren der CLI-Erweiterung „aks-preview“

Wenn Sie einen AKS-Cluster oder Knotenpool erstellen möchten, der die kubelet- Parameter oder die Betriebssystemeinstellungen anpassen kann, benötigen Sie die aktuelle Erweiterung *aks-preview* der Azure CLI. Installieren Sie die Erweiterung *aks-preview* der Azure-Befehlszeilenschnittstelle mithilfe des Befehls [az extension add][az-extension-add]. Alternativ können Sie verfügbare Updates mithilfe des Befehls [az extension update][az-extension-update] installieren.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 

## <a name="use-custom-node-configuration"></a>Verwenden einer benutzerdefinierten Knotenkonfiguration

### <a name="kubelet-custom-configuration"></a>Benutzerdefinierte kubelet-Konfiguration

Die unterstützten kubelet-Parameter und akzeptierten Werte werden unten aufgeführt.

| Parameter | Zulässige Werte/Intervall | Standard | BESCHREIBUNG |
| --------- | ----------------------- | ------- | ----------- |
| `cpuManagerPolicy` | none, static | none | Mit der statischen Richtlinie können Container in [Garantierten Pods](https://kubernetes.io/docs/tasks/configure-pod-container/quality-service-pod/) mit CPU-Integeranforderungen auf exklusive CPUs auf dem Knoten zugreifen. |
| `cpuCfsQuota` | true, false | true |  Aktivieren/Deaktivieren der CPU-CFS-Kontingenterzwingung für Container, die CPU-Grenzwerte angeben. | 
| `cpuCfsQuotaPeriod` | Intervall in Millisekunden (ms) | `100ms` | Legt den Wert des CPU-CFS-Kontingentzeitraums fest. | 
| `imageGcHighThreshold` | 0–100 | 85 | Der Prozentsatz der Datenträgerauslastung, nach der die Image-Garbage Collection immer ausgeführt wird. Die minimale Datenträgerauslastung, die Garbage Collection **auslöst**. Legen Sie den Wert zum Deaktivieren von Image-Garbage Collection auf 100 fest. | 
| `imageGcLowThreshold` | 0 bis 100, nicht größer als `imageGcHighThreshold` | 80 | Der Prozentsatz der Datenträgerauslastung, vor der die Image-Garbage Collection nie ausgeführt wird. Die minimale Datenträgerauslastung, die Garbage Collection auslösen **kann**. |
| `topologyManagerPolicy` | none, best-effort, restricted, single-numa-node | none | Optimieren der NUMA-Knotenausrichtung. Weitere Informationen finden Sie [hier](https://kubernetes.io/docs/tasks/administer-cluster/topology-manager/). Nur Kubernetes v1.18 oder höher. |
| `allowedUnsafeSysctls` | `kernel.shm*`, `kernel.msg*`, `kernel.sem`, `fs.mqueue.*`, `net.*` | Keine | Zulässige Liste unsicherer sysctls oder unsicherer sysctl-Muster. | 

### <a name="linux-os-custom-configuration"></a>Benutzerdefinierte Linux-Betriebssystemkonfiguration

Die unterstützten Betriebssystemeinstellungen und akzeptierten Werte werden unten aufgeführt.

#### <a name="file-handle-limits"></a>Dateihandlegrenzwerte

Wenn Sie viel Datenverkehr verarbeiten, kommt es häufig vor, dass der Datenverkehr, den Sie verarbeiten, von einer großen Anzahl lokaler Dateien stammt. Sie können die folgenden Kerneleinstellungen und integrierten Grenzwerte anpassen, damit Sie auf Kosten des Systemarbeitsspeichers mehr Datenverkehr verarbeiten können.

| Einstellung | Zulässige Werte/Intervall | Standard | BESCHREIBUNG |
| ------- | ----------------------- | ------- | ----------- |
| `fs.file-max` | 8192 - 12000500 | 709620 | Maximale Anzahl von Dateihandles, die der Linux-Kernel zuweist. Durch Erhöhen dieses Werts können Sie die maximal zulässige Anzahl geöffneter Dateien erhöhen. |
| `fs.inotify.max_user_watches` | 781250 - 2097152 | 1048576 | Maximale Anzahl von Dateiüberwachungen, die vom System zugelassen werden. Jedes *Überwachungselement* ist für einen 32-Bit-Kernel ungefähr 90 Bytes groß und ungefähr 160 Bytes für einen 64-Bit-Kernel. | 
| `fs.aio-max-nr` | 65536 - 6553500 | 65536 | Der Wert aio-nr zeigt die aktuelle systemweite Anzahl von asynchronen E/A-Anforderungen an. Mit aio-max-nr können Sie den maximalen Wert ändern, auf den aio-nr anwachsen kann. |
| `fs.nr_open` | 8192 - 20000500 | 1048576 | Die maximale Anzahl von Dateihandles, die ein Prozess zuordnen kann. |


#### <a name="socket-and-network-tuning"></a>Socket- und Netzwerkoptimierung

Für Agent-Knoten, die eine sehr große Anzahl gleichzeitiger Sitzungen verarbeiten sollen, können Sie die unten angegebene Teilmenge der TCP- und Netzwerkoptionen verwenden, die Sie pro Knotenpool optimieren können. 

| Einstellung | Zulässige Werte/Intervall | Standard | BESCHREIBUNG |
| ------- | ----------------------- | ------- | ----------- |
| `net.core.somaxconn` | 4096 - 3240000 | 16384 | Maximale Anzahl von Verbindungsanforderungen, die für einen beliebigen Lauschsocket in die Warteschlange eingereiht werden können. Eine Obergrenze für den Wert des backlog-Parameters, der an die Funktion [listen(2)](http://man7.org/linux/man-pages/man2/listen.2.html) übergeben wird. Wenn das backlog-Argument größer als `somaxconn` ist, erfolgt automatisch eine Kürzung auf diesen Grenzwert.
| `net.core.netdev_max_backlog` | 1000 - 3240000 | 1000 | Maximale Anzahl von Paketen, die auf der INPUT-Seite in die Warteschlange eingereiht werden, wenn die Schnittstelle Pakete schneller empfängt, als der Kernel diese verarbeiten kann. |
| `net.core.rmem_max` | 212992 - 134217728 | 212992 | Maximale Größe des Empfangssocketpuffers in Bytes. |
| `net.core.wmem_max` | 212992 - 134217728 | 212992 | Maximale Größe des Sendesocketpuffers in Bytes. | 
| `net.core.optmem_max` | 20480 - 4194304 | 20.480 | Maximale Größe des zusätzlichen Puffers (Option Arbeitsspeicherpuffer), die pro Socket zulässig ist. Der Arbeitsspeicher der Socket-Option wird in einigen Fällen verwendet, um zusätzliche Strukturen im Zusammenhang mit der Verwendung des Sockets zu speichern. | 
| `net.ipv4.tcp_max_syn_backlog` | 128 - 3240000 | 16384 | Maximale Anzahl von Verbindungsanforderungen in der Warteschlange, die noch keine Bestätigung vom Client empfangen haben, der eine Verbindung herstellt. Wenn dieser Wert überschritten wird, beginnt der Kernel mit dem Löschen von Anforderungen. |
| `net.ipv4.tcp_max_tw_buckets` | 8000 - 1440000 | 32768 | Maximale Anzahl von `timewait`-Sockets, die gleichzeitig vom System gehalten werden. Wenn dieser Wert überschritten wird, wird der time-wait-Socket sofort gelöscht, und es wird eine Warnung ausgegeben. |
| `net.ipv4.tcp_fin_timeout` | 5 - 120 | 60 | Die Zeitspanne, für die eine verwaiste (von keiner Anwendung mehr referenzierte) Verbindung im FIN_WAIT_2-Zustand verbleibt, bevor sie am lokalen Ende abgebrochen wird. |
| `net.ipv4.tcp_keepalive_time` | 30 - 432000 | 7200 | Gibt an, wie oft TCP `keepalive`-Nachrichten sendet, wenn `keepalive` aktiviert ist. |
| `net.ipv4.tcp_keepalive_probes` | 1 - 15 | 9 | Gibt an, wie viele `keepalive`-Tests von TCP gesendet werden, bis die Verbindung als getrennt angenommen wird. |
| `net.ipv4.tcp_keepalive_intvl` | 1 - 75 | 75 | Gibt an, wie oft die Tests gesendet werden. Multipliziert mit `tcp_keepalive_probes` ergibt sich die Zeit, nach der eine Verbindung nach dem Start von Tests beendet wird, die nicht antwortet. | 
| `net.ipv4.tcp_tw_reuse` | 0 oder 1 | 0 | Hiermit wird die Wiederverwendung von `TIME-WAIT`-Sockets für neue Verbindungen ermöglicht, wenn dies aus Sicht des Protokolls sicher ist. | 
| `net.ipv4.ip_local_port_range` | Erster: 1024 - 60999 und letzter: 32768 - 65000] | Erster: 32768 und letzter: 60999 | Der lokale Portbereich, der von TCP- und UDP-Datenverkehr verwendet wird, um den lokalen Port auszuwählen. Besteht aus zwei Zahlen: Die erste Zahl ist der erste lokale Port, der für TCP- und UDP-Datenverkehr auf dem Agent-Knoten zulässig ist. Der zweite Wert ist die letzte lokale Portnummer. | 
| `net.ipv4.neigh.default.gc_thresh1`|  128 - 80000 | 4096 | Mindestanzahl von Einträgen, die sich im ARP-Cache befinden können. Garbage Collection wird nicht ausgelöst, wenn die Anzahl der Einträge unterhalb dieser Einstellung liegt. | 
| `net.ipv4.neigh.default.gc_thresh2`|  512 - 90000 | 8192 | Weiche maximale Anzahl von Einträgen, die sich möglicherweise im ARP-Cache befinden. Diese Einstellung ist wohl die wichtigste, da ARP-Garbage Collection ungefähr 5 Sekunden nach Erreichen dieses weichen Maximalwerts ausgelöst wird. |
| `net.ipv4.neigh.default.gc_thresh3`|  1024 - 100000 | 16384 | Harte maximale Anzahl von Einträgen im ARP-Cache. |
| `net.netfilter.nf_conntrack_max` | 131072 - 589824 | 131072 | `nf_conntrack` ist ein Modul, das Verbindungseinträge für NAT innerhalb von Linux nachverfolgt. Das `nf_conntrack`-Modul verwendet eine Hashtabelle, um den *eingerichteten Verbindungsdatensatz* des TCP-Protokolls aufzuzeichnen. `nf_conntrack_max` ist die maximale Anzahl von Knoten in der Hashtabelle, d. h. die maximale Anzahl von Verbindungen, die vom `nf_conntrack`-Modul unterstützt werden, oder die Größe der Verbindungsnachverfolgungstabelle. | 
| `net.netfilter.nf_conntrack_buckets` | 65536 - 147456 | 65536 | `nf_conntrack` ist ein Modul, das Verbindungseinträge für NAT innerhalb von Linux nachverfolgt. Das `nf_conntrack`-Modul verwendet eine Hashtabelle, um den *eingerichteten Verbindungsdatensatz* des TCP-Protokolls aufzuzeichnen. `nf_conntrack_buckets` die Größe der Hashtabelle. | 

#### <a name="worker-limits"></a>Workerlimits

Wie bei Dateideskriptoren wird die Anzahl von Workern oder Threads, die ein Prozess erstellen kann, sowohl durch eine Kerneleinstellung als auch durch Benutzergrenzwerte eingeschränkt. Der Benutzergrenzwert für AKS ist unbegrenzt. 

| Einstellung | Zulässige Werte/Intervall | Standard | BESCHREIBUNG |
| ------- | ----------------------- | ------- | ----------- |
| `kernel.threads-max` | 20 - 513785 | 55601 | Prozesse können Arbeitsthreads einrichten. Die maximale Anzahl von Threads, die erstellt werden können, wird mit der Kerneleinstellung `kernel.threads-max` festgelegt. | 

#### <a name="virtual-memory"></a>Virtueller Arbeitsspeicher

Die folgenden Einstellungen können verwendet werden, um den Betrieb des VM-Subsystems (virtueller Arbeitsspeicher) des Linux-Kernels und den `writeout`-Vorgang ungültiger Daten auf den Datenträger zu optimieren.

| Einstellung | Zulässige Werte/Intervall | Standard | BESCHREIBUNG |
| ------- | ----------------------- | ------- | ----------- |
| `vm.max_map_count` |  65530 - 262144 | 65530 | Diese Datei enthält die maximale Anzahl von Speicherzuordnungsbereichen, die ein Prozess aufweisen kann. Speicherzuordnungsbereiche werden als Nebeneffekte des Aufrufs von `malloc` direkt durch `mmap`, `mprotect` und `madvise` sowie beim Laden von freigegebenen Bibliotheken verwendet. | 
| `vm.vfs_cache_pressure` | 1 - 500 | 100 | Dieser Prozentwert steuert die Tendenz des Kernels, den Speicher freizugeben, der zum Zwischenspeichern von Verzeichnis- und I-Knoten-Objekten verwendet wird. |
| `vm.swappiness` | 0-100 | 60 | Dieses Steuerelement wird verwendet, um zu definieren, wie aggressiv der Kernel Speicherseiten auslagern soll. Höhere Werte erhöhen die Aggressivität, niedrigere Werte verringern das Volumen der Auslagerung. Der Wert 0 weist den Kernel an, die Auslagerung erst zu initiieren, wenn die Menge der freien und dateigestützten Seiten kleiner ist als die obere Grenze in einer Zone. | 
| `swapFileSizeMB` | 1 MB: Größe des [temporären Datenträgers](../virtual-machines/managed-disks-overview.md#temporary-disk) (/dev/sdb) | Keine | SwapFileSizeMB gibt die Größe einer Auslagerungsdatei in MB an, die auf den Agent-Knoten aus diesem Knotenpool erstellt wird. | 
| `transparentHugePageEnabled` | `always`, `madvise`, `never` | `always` | [Transparente Hugepages](https://www.kernel.org/doc/html/latest/admin-guide/mm/transhuge.html#admin-guide-transhuge) ist eine Linux-Kernelfunktion, die zur Verbesserung der Leistung beitragen soll, indem die Speicherzuordnungshardware Ihres Prozessors effizienter genutzt wird. Wenn diese Option aktiviert ist, versucht der Kernel, nach Möglichkeit `hugepages` zuzuordnen, und jeder Linux-Prozess erhält 2-MB-Seiten, wenn die `mmap`-Region bei natürlicher Ausrichtung 2 MB groß ist. In bestimmten Fällen, in denen `hugepages` systemweit aktiviert sind, belegen Anwendungen möglicherweise mehr Speicherressourcen. Eine Anwendung kann `mmap` für eine großen Region verwenden, aber nur 1 Byte davon berühren. In diesem Fall wird möglicherweise eine 2-MB-Seite anstelle einer 4K-Seite zugeordnet. Aufgrund dieses Szenarios ist es möglich, `hugepages` systemweit zu deaktivieren oder nur in `MADV_HUGEPAGE madvise`-Regionen zuzulassen. | 
| `transparentHugePageDefrag` | `always`, `defer`, `defer+madvise`, `madvise`, `never` | `madvise` | Dieser Wert steuert, ob der Kernel Speicherkomprimierung aggressiv verwenden soll, um weitere `hugepages` zur Verfügung zu stellen. | 

> [!IMPORTANT]
> Um die Suche und Lesbarkeit zu vereinfachen, werden die Betriebssystemeinstellungen in diesem Dokument anhand ihres Namens angezeigt, sollten aber der JSON- Konfigurationsdatei oder der AKS-API in [camelCase-Schreibweise](/dotnet/standard/design-guidelines/capitalization-conventions) hinzugefügt werden.

Erstellen Sie eine Datei `kubeletconfig.json` mit folgendem Inhalt:

```json
{
 "cpuManagerPolicy": "static",
 "cpuCfsQuota": true,
 "cpuCfsQuotaPeriod": "200ms",
 "imageGcHighThreshold": 90,
 "imageGcLowThreshold": 70,
 "topologyManagerPolicy": "best-effort",
 "allowedUnsafeSysctls": [
  "kernel.msg*",
  "net.*"
],
 "failSwapOn": false
}
```
Erstellen Sie eine Datei `linuxosconfig.json` mit folgendem Inhalt:

```json
{
 "transparentHugePageEnabled": "madvise",
 "transparentHugePageDefrag": "defer+madvise",
 "swapFileSizeMB": 1500,
 "sysctls": {
  "netCoreSomaxconn": 163849,
  "netIpv4TcpTwReuse": true,
  "netIpv4IpLocalPortRange": "32000 60000"
 }
}
```

Erstellen Sie einen neuen Cluster, indem Sie die Konfigurationen für kubelet und Betriebssystem mithilfe der im vorherigen Schritt erstellten JSON-Dateien angeben. 

> [!NOTE]
> Wenn Sie einen Cluster erstellen, können Sie die kubelet-Konfiguration, die Betriebssystemkonfiguration oder beides angeben. Wenn Sie beim Erstellen eines Clusters eine Konfiguration angeben, wird diese Konfiguration nur für die Knoten im anfänglichen Knotenpool angewendet. Alle Einstellungen, die nicht in der JSON-Datei konfiguriert sind, behalten den Standardwert bei.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --kubelet-config ./kubeletconfig.json --linux-os-config ./linuxosconfig.json
```

Fügen Sie einen neuen Knotenpool hinzu, indem Sie die kubelet-Parameter mithilfe der erstellten JSON-Datei angeben.

> [!NOTE]
> Wenn Sie einem vorhandenen Cluster einen Knotenpool hinzufügen, können Sie die kubelet-Konfiguration, die Betriebssystemkonfiguration oder beides angeben. Wenn Sie beim Hinzufügen eines Knotenpools eine Konfiguration angeben, wird diese Konfiguration nur auf die Knoten im neuen Knotenpool angewendet. Alle Einstellungen, die nicht in der JSON-Datei konfiguriert sind, behalten den Standardwert bei.

```azurecli
az aks nodepool add --name mynodepool1 --cluster-name myAKSCluster --resource-group myResourceGroup --kubelet-config ./kubeletconfig.json
```

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie [Ihren AKS-Cluster konfigurieren](cluster-configuration.md).
- Erfahren Sie, wie Sie ein [Upgrade der Knotenimages](node-image-upgrade.md) in Ihrem Cluster durchführen.
- Unter [Upgrade eines Azure Kubernetes Service-Clusters (AKS)](upgrade-cluster.md) erfahren Sie, wie Sie Ihren Cluster auf die neueste Version von Kubernetes aktualisieren können.
- In der Liste der [Häufig gestellten Fragen zu AKS](faq.md) finden Sie Antworten auf einige häufig gestellte Fragen zu AKS.

<!-- LINKS - internal -->
[aks-faq]: faq.md
[aks-faq-node-resource-group]: faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group
[aks-multiple-node-pools]: use-multiple-node-pools.md
[aks-scale-apps]: tutorial-kubernetes-scale.md
[aks-support-policies]: support-policies.md
[aks-upgrade]: upgrade-cluster.md
[aks-view-master-logs]: ./view-master-logs.md#enable-resource-logs
[autoscaler-profile-properties]: #using-the-autoscaler-profile
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-update]: /cli/azure/aks#az-aks-update
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[upgrade-cluster]: upgrade-cluster.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade


<!-- LINKS - external -->
[az-aks-update-preview]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
[kubernetes-faq]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why
