---
title: Versionshinweise zu Azure Stack Edge Pro (Preview) | Microsoft-Dokumentation
description: In diesem Artikel werden schwerwiegende offene Probleme und Lösungen im Release mit verfügbarer Vorschauversion von Azure Stack Edge Pro beschrieben.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 09/07/2020
ms.author: alkohli
ms.openlocfilehash: 25db4e7f3e4e1f7056979c4c40c6ffc61f340439
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96345370"
---
# <a name="azure-stack-edge-pro-with-gpu-preview-release-notes"></a>Versionshinweise zu Azure Stack Edge Pro (Preview) mit GPU

In den folgenden Versionshinweisen werden die schwerwiegenden offenen und die behobenen Issues für das Vorschaurelease 2008 für Azure Stack Edge Pro-Geräte mit GPU beschrieben.

Die Versionshinweise werden fortlaufend aktualisiert, und wenn schwerwiegende Probleme festgestellt werden, die eine Problemumgehung erfordern, werden sie hinzugefügt. Lesen Sie vor der Bereitstellung Ihres Azure Stack Edge Pro-Geräts die Informationen in den Versionshinweisen sorgfältig durch.

Dieser Artikel bezieht sich auf das folgende Softwarerelease: **Azure Stack Edge Pro 2008**. 

<!--- **2.1.1328.1904**-->

## <a name="whats-new"></a>Neues

Die folgenden neuen Features wurden im Azure Stack Edge 2008-Release hinzugefügt. Abhängig von der konkreten Vorschauversion der Software, die Sie ausführen, wird möglicherweise nur ein Teil dieser Features angezeigt. 

- **Speicherklassen**: Im vorherigen Release konnten Sie Speicher nur statisch über SMB- oder NFS-Freigaben für zustandsbehaftete Anwendungen bereitstellen, die im Kubernetes-Cluster auf dem Azure Stack Edge Pro-Gerät bereitgestellt wurden. In diesem Release wurden Speicherklassen hinzugefügt, mit denen Speicher dynamisch bereitgestellt werden kann. Weitere Informationen finden Sie unter [Kubernetes-Speicherverwaltung auf dem Azure Stack Edge Pro-GPU-Gerät](azure-stack-edge-gpu-kubernetes-storage.md#dynamicprovisioning). 
- **Kubernetes-Dashboard mit Metrics Server:** In diesem Release wird ein Kubernetes-Dashboard mit dem Metrics Server-Add-On hinzugefügt. Sie können das Dashboard verwenden, um eine Übersicht über die auf dem Azure Stack Edge Pro-Gerät ausgeführten Anwendungen, den Status von Kubernetes-Clusterressourcen und eventuelle auf dem Gerät aufgetretene Fehler anzuzeigen. Mit dem Metrics Server-Add-On wird die CPU- und Speicherauslastung der Kubernetes-Ressourcen auf dem Gerät aggregiert. Weitere Informationen finden Sie unter [Überwachen des Azure Stack Edge Pro-GPU-Geräts über das Kubernetes-Dashboard](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md).
- **Azure Arc für Azure Stack Edge Pro**: Ab diesem Release können Sie Anwendungsworkloads auf dem Azure Stack Edge Pro-Gerät über Azure Arc bereitstellen. Azure Arc ist ein Hybridverwaltungstool, mit dem Sie Anwendungen in Ihren Kubernetes-Clustern bereitstellen können. Weitere Informationen finden Sie unter [Bereitstellen von Workloads über Azure Arc auf Ihrem Azure Stack Edge Pro-Gerät](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md).  

## <a name="known-issues"></a>Bekannte Probleme 

Die folgende Tabelle enthält eine Zusammenfassung der bekannten Issues für das Azure Stack Edge Pro-Gerät.

| Nein. | Funktion | Problem | Problemumgehung/Kommentare |
| --- | --- | --- | --- |
| **1.** |Azure Stack Edge Pro + Azure SQL | Zum Erstellen einer SQL-Datenbank ist Administratorzugriff erforderlich.   |Führen Sie statt der Schritte 1 und 2 unter [https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database](../iot-edge/tutorial-store-data-sql-server.md#create-the-sql-database) die folgenden Schritte aus. <ul><li>Aktivieren Sie in der lokalen Benutzeroberfläche des Geräts die Computeschnittstelle. Wählen Sie **Compute > Portnr. > Für Compute aktivieren > Anwenden** aus.</li><li>Laden Sie `sqlcmd` von https://docs.microsoft.com/sql/tools/sqlcmd-utility auf den Clientcomputer herunter. </li><li>Stellen Sie eine Verbindung mit der IP-Adresse der Computeschnittstelle (dem aktivierten Port) her, und fügen Sie am Ende der Adresse „,1401“ hinzu.</li><li>Der endgültige Befehl sieht wie folgt aus: sqlcmd -S {IP-Adresse-der-Schnittstelle},1401 -U SA -P "Starkes!Kennw0rt".</li>Danach sollten die Schritte 3 und 4 aus der aktuellen Dokumentation identisch sein. </li></ul> |
| **2.** |Aktualisieren| Inkrementelle Änderungen an Blobs, die über **Aktualisieren** wiederhergestellt werden, sind NICHT möglich. |Bei Blobendpunkten können Teilaktualisierungen von Blobs nach dem erneuten Laden dazu führen, dass die Aktualisierungen nicht in die Cloud hochgeladen werden. Dies betrifft beispielsweise eine Sequenz folgender Aktionen:<ul><li>Erstellen eines Blobs in der Cloud. Oder Löschen eines zuvor hochgeladenen Blobs auf dem Gerät.</li><li>Aktualisieren des Blobs mithilfe der Aktualisierungsfunktion aus der Cloud auf der Appliance.</li><li>Aktualisieren nur eines Teils des Blobs mithilfe von Azure SDK-REST-APIs.</li></ul>Diese Aktionen können dazu führen, dass die aktualisierten Abschnitte des Blobs nicht in der Cloud aktualisiert werden. <br>**Problemumgehung**: Verwenden Sie Tools wie Robocopy, oder führen Sie reguläre Dateikopiervorgänge über Explorer oder die Befehlszeile durch, um ganze Blobs zu ersetzen.|
|**3.**|Drosselung|Wenn bei der Drosselung neue Schreibvorgänge auf dem Gerät nicht zulässig sind, tritt bei über den NFS-Client durchgeführten Schreibvorgängen der Fehler „Zugriff verweigert“ auf.| Der Fehler wird wie folgt angezeigt:<br>`hcsuser@ubuntu-vm:~/nfstest$ mkdir test`<br>mkdir: Verzeichnis „test“ kann nicht erstellt werden: Zugriff verweigert|
|**4.**|Blob Storage-Erfassung|Wenn Sie AzCopy Version 10 für die Erfassung von Blob Storage verwenden, führen Sie AzCopy mit folgendem Argument aus: `Azcopy <other arguments> --cap-mbps 2000`| Wenn diese Grenzwerte für AzCopy nicht angegeben werden, wird möglicherweise eine große Anzahl von Anforderungen an das Gerät gesendet. Dies kann zu Problemen mit dem Dienst führen.|
|**5.**|Mehrstufige Speicherkonten|Bei der Verwendung von mehrstufigen Speicherkonten gilt Folgendes:<ul><li> Es werden nur Blockblobs unterstützt. Seitenblobs werden nicht unterstützt.</li><li>Momentaufnahmen- oder Kopieren-APIs werden nicht unterstützt.</li><li> Die Erfassung von Hadoop-Workloads über `distcp` wird nicht unterstützt, da dabei der Kopiervorgang intensiv genutzt wird.</li></ul>||
|**6.**|Verbindung mit NFS-Freigabe|Wenn mehrere Prozesse in dieselbe Freigabe kopiert werden und das Attribut `nolock` nicht verwendet wird, werden beim Kopiervorgang möglicherweise Fehler angezeigt.|Zum Kopieren von Dateien in die NFS-Freigabe muss das Attribut `nolock` an den mount-Befehl übergeben werden. Beispiel: `C:\Users\aseuser mount -o anon \\10.1.1.211\mnt\vms Z:`.|
|**7.**|Kubernetes-Cluster|Wenn Sie auf Ihrem Gerät, auf dem ein Kubernetes-Cluster ausgeführt wird, ein Update anwenden, werden die virtuellen Kubernetes-Computer neu gestartet. In diesem Fall werden nur Pods, die mit angegebenen Replikaten bereitgestellt werden, nach einem Update automatisch wiederhergestellt.  |Wenn Sie einzelne Pods außerhalb eines Replikationscontrollers ohne Angabe einer Replikatgruppe erstellt haben, werden diese Pods nach dem Geräteupdate nicht automatisch wiederhergestellt. Diese Pods müssen wiederhergestellt werden.<br>Eine Replikatgruppe ersetzt Pods, die aus einem bestimmten Grund gelöscht oder beendet werden, z. B. Knotenfehler oder Aktualisierung eines unterbrochenen Knotens. Aus diesem Grund empfiehlt es sich, eine Replikatgruppe auch dann zu verwenden, wenn in der Anwendung nur ein einziger Pod erforderlich ist.|
|**8.**|Kubernetes-Cluster|Kubernetes auf Azure Stack Edge Pro wird nur mit Helm v3 oder höher unterstützt. Weitere Informationen finden Sie unter [Frequently asked questions: Removal of Tiller](https://v3.helm.sh/docs/faq/) (Häufig gestellte Fragen: Entfernen von Tiller).|
|**9.**|Azure Arc + Azure Stack Edge Pro|Azure Arc-Bereitstellungen werden nicht unterstützt, wenn auf dem Azure Stack Edge Pro-Gerät ein Webproxy konfiguriert ist.||
|**10.**|Kubernetes |Port 31000 ist für das Kubernetes-Dashboard reserviert. Entsprechend sind die IP-Adressen 172.28.0.1 und 172.28.0.10 in der Standardkonfiguration jeweils für den Kubernetes-Dienst bzw. den Core DNS-Dienst reserviert.|Reservierte IP-Adressen dürfen nicht verwendet werden.|
|**11.**|Kubernetes |In Kubernetes sind derzeit keine Lastenausgleichsdienste für mehrere Protokolle zulässig, beispielsweise ein DNS-Dienst, der auf TCP sowie auf UDP lauschen soll. |Zum Umgehen dieser Einschränkung von Kubernetes mit MetalLB können für die gleiche Podauswahl zwei Dienste (einer für TCP, einer für UDP) erstellt werden. Diese Dienste verwenden denselben Freigabeschlüssel und denselben Wert spec.loadBalancerIP und nutzen so die gleiche IP-Adresse gemeinsam. IP-Adressen können auch gemeinsam genutzt werden, wenn Sie über mehr Dienste als verfügbare IP-Adressen verfügen. <br> Weitere Informationen finden Sie unter [IP Address Sharing](https://metallb.universe.tf/usage/#ip-address-sharing) (Freigeben von IP-Adressen).|
|**12.**|Kubernetes-Cluster|Vorhandene Marketplace-Module für Azure IoT Edge werden im Kubernetes-Cluster nicht als Hostingplattform für IoT Edge auf dem Azure Stack Edge-Gerät ausgeführt.|Die Module müssen geändert werden, bevor sie auf dem Azure Stack Edge-Gerät bereitgestellt werden. Weitere Informationen finden Sie unter „Ändern von Azure IoT Edge-Modulen aus dem Marketplace zum Ausführen auf dem Azure Stack Edge-Gerät“.<!-- insert link-->|
|**13.**|Kubernetes |Dateibasierte Bindungseinbindungen werden mit Azure IoT Edge in Kubernetes auf dem Azure Stack Edge-Gerät nicht unterstützt.|IoT Edge verwendet eine Übersetzungsebene, um `ContainerCreate`-Optionen in Kubernetes-Konstrukte zu übersetzen. Bei Erstellung von `Binds` wird das Verzeichnis „hostpath“ zugeordnet oder erstellt, daher können dateibasierte Bindungseinbindungen nicht an Pfade in IoT Edge-Containern gebunden werden.|
|**14.**|Kubernetes |Wenn Sie eigene Zertifikate für IoT Edge verwenden und auf Ihrem Azure Stack Edge-Gerät hinzufügen, werden die neuen Zertifikate nicht als Teil der Aktualisierung der Helm-Charts übernommen.|Um dieses Problem zu umgehen, [stellen Sie eine Verbindung mit der PowerShell-Schnittstelle des Geräts her](azure-stack-edge-gpu-connect-powershell-interface.md). Starten Sie die Pods `iotedged` und `edgehub` neu.|
|**15.**|Zertifikate |In bestimmten Fällen kann es einige Sekunden dauern, bis der Zertifikatsstatus in der lokalen Benutzeroberfläche aktualisiert ist. |Dies kann sich in den folgenden Szenarios auf die lokale Benutzeroberfläche auswirken:<ul><li>Spalte **Status** auf der Seite **Zertifikate**</li><li>Kachel **Sicherheit** auf der Seite **Erste Schritte**</li><li>Kachel **Konfiguration** auf der Seite **Übersicht**</li></ul>  |

## <a name="next-steps"></a>Nächste Schritte

- [Vorbereiten der Bereitstellung von Azure Stack Edge Pro-Geräten mit GPU](azure-stack-edge-gpu-deploy-prep.md)