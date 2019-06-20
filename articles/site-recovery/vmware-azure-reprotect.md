---
title: Erneutes Schützen von VMs aus Azure in einem lokalen Standort während der Notfallwiederherstellung von VMware-VMs und physischen Servern | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie nach dem Failover auf Azure während der Notfallwiederherstellung von VMware-VMs und physischen Servern ein Failback von Azure auf den lokalen Standort ausführen.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 3/12/2019
ms.author: mayg
ms.openlocfilehash: 4202d95b540efb98b526f8a8abd17da22a908ebe
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60482930"
---
# <a name="reprotect-and-fail-back-machines-to-an-on-premises-site-after-failover-to-azure"></a>Erneutes Schützen und Ausführen eines Failbacks für Computer auf einen lokalen Standort nach einem Failover auf Azure

Nach dem [Failover](site-recovery-failover.md) von lokalen VMware-VMs oder physischen Servern in Azure besteht der erste Schritt beim Failback zurück an Ihren lokalen Standort im erneuten Schützen der Azure-VMs, die während des Failovers erstellt wurden. Dieser Artikel beschreibt die entsprechende Vorgehensweise. 

Sehen Sie sich das folgende Video zum Failover von Azure zu einem lokalen Standort an, um eine kurze Übersicht zu erhalten.<br /><br />
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="before-you-begin"></a>Voraussetzungen

Wenn Sie zum Erstellen Ihrer virtuellen Computer eine Vorlage verwendet haben, sollten Sie sicherstellen, dass jeder virtuelle Computer über eine eigene UUID für die Datenträger verfügt. Falls ein Konflikt zwischen der UUID des lokalen virtuellen Computers und der UUID des Masterziels besteht, weil beide mit der gleichen Vorlage erstellt wurden, tritt beim erneuten Schützen ein Fehler auf. Stellen Sie ein anderes Masterziel bereit, das nicht mit der gleichen Vorlage erstellt wurde. Beachten Sie die folgenden Informationen:
- Wenn Sie versuchen, ein Failback auf einen alternativen vCenter-Computer durchzuführen, stellen Sie sicher, dass Ihr neuer vCenter-Computer und der Masterzielserver erkannt werden. Als typisches Symptom sind die Datenspeicher im Dialogfeld **Erneut schützen** nicht zugänglich oder sichtbar.
- Sie benötigen eine Failbackrichtlinie, um zurück auf den lokalen Standort zu replizieren. Diese Richtlinie wird automatisch erstellt, wenn Sie eine vorwärts gerichtete Richtlinie erstellen. Beachten Sie die folgenden Informationen:
    - Diese Richtlinie wird während der Erstellung automatisch dem Konfigurationsserver zugeordnet.
    - Diese Richtlinie kann nicht bearbeitet werden.
    - Sie ist auf folgende Werte festgelegt: RPO-Schwellenwert = 15 Min., Aufbewahrungszeitraum des Wiederherstellungspunkts = 24 Stunden, Momentaufnahmehäufigkeit für Anwendungskonsistenz = 60 Min.
- Während des erneuten Schützens und des Failbacks muss der lokale Konfigurationsserver ausgeführt werden und verbunden sein.
- Wenn die virtuellen Computer, für die Sie das Failback ausführen möchten, von einem vCenter-Server verwaltet werden, stellen Sie sicher, dass Sie auf den vCenter-Servern über die [erforderlichen Berechtigungen](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) für die Ermittlung von virtuellen Computern verfügen.
- Löschen Sie vor dem erneuten Schützen die Momentaufnahmen auf dem Masterzielserver. Wenn auf dem lokalen Masterziel oder lokalen virtuellen Computer Momentaufnahmen vorhanden sind, tritt beim Aktivieren des erneuten Schutzes ein Fehler auf. Die Momentaufnahmen auf dem virtuellen Computer werden während des Auftrags zum erneuten Schützen automatisch zusammengeführt.
- Alle virtuellen Computer einer Replikationsgruppe müssen vom gleichen Betriebssystemtyp sein (alle entweder Windows oder Linux). Eine Replikationsgruppe mit gemischten Betriebssystemen wird derzeit für das erneute Schützen und lokale Failbacks nicht unterstützt. Der Grund hierfür ist, dass das Betriebssystem bei Masterziel und VM identisch sein muss. Alle virtuellen Computer einer Replikationsgruppe müssen dasselbe Masterziel haben. 
- Ein Konfigurationsserver ist lokal erforderlich, wenn Sie ein Failback durchführen. Während des Failbacks muss der virtuelle Computer in der Konfigurationsserverdatenbank vorhanden sein. Andernfalls ist das Failback nicht erfolgreich. Stellen Sie sicher, dass Sie die regelmäßigen geplanten Sicherungen des Konfigurationsservers durchführen. Stellen Sie im Notfall den Server mit der gleichen IP-Adresse wieder her, damit das Failback funktioniert.
- Für das erneute Schützen und das Failback ist eine Site-to-Site-VPN-Verbindung (S2S) für die Datenreplikation erforderlich. Stellen Sie das Netzwerk bereit, damit die virtuellen Computer in Azure, für die ein Failover durchgeführt wurde, den lokalen Konfigurationsserver erreichen können (per Ping). Sie können auch einen Prozessserver im Azure-Netzwerk des virtuellen Computers bereitstellen, für den das Failover durchgeführt wurde. Dieser Prozessserver muss außerdem mit dem lokalen Konfigurationsserver kommunizieren können.
- Stellen Sie sicher, dass Sie die folgenden Ports für Failover und Failback öffnen:

    ![Ports für Failover und Failback](./media/vmware-azure-reprotect/failover-failback.png)

- Informieren Sie sich über die [Voraussetzungen für Ports und die Aufnahme von URLs in die Whitelist](vmware-azure-deploy-configuration-server.md#prerequisites).

## <a name="deploy-a-process-server-in-azure"></a>Bereitstellen eines Prozessservers in Azure

Sie benötigen möglicherweise einen Prozessserver in Azure, bevor Sie ein Failover zum lokalen Standort ausführen können:
- Der Prozessserver empfängt Daten vom geschützten virtuellen Computer in Azure und sendet Daten an den lokalen Standort.
- Zwischen dem Prozessserver und dem geschützten virtuellen Computer ist ein Netzwerk mit geringer Wartezeit erforderlich. Im Allgemeinen müssen Sie bei der Entscheidung, ob Sie einen Prozessserver in Azure benötigen, die Wartezeit berücksichtigen:
    - Wenn Sie eine Azure ExpressRoute-Verbindung eingerichtet haben, können Sie einen lokalen Prozessserver zum Senden von Daten verwenden, da die Wartezeit zwischen dem virtuellen Computer und dem Prozessserver gering ist.
    - Wenn Sie dagegen nur über ein S2S-VPN verfügen, empfiehlt es sich, den Prozessserver in Azure bereitzustellen.
    - Es empfiehlt sich, beim Failback einen Azure-basierten Prozessserver zu verwenden. Die Replikationsleistung ist höher, wenn der Prozessserver sich näher beim replizierenden virtuellen Computer (dem Computer in Azure, für den das Failover durchgeführt wurde) befindet. Für einen Proof-of-Concept können Sie den lokalen Prozessserver und ExpressRoute mit privatem Peering verwenden.

So stellen Sie einen Prozessserver in Azure bereit:

1. Wie Sie einen Prozessserver in Azure bereitstellen, erfahren Sie unter [Einrichten eines Prozessservers für das Failback in Azure](vmware-azure-set-up-process-server-azure.md).
2. Die Azure-VMs senden Replikationsdaten an den Prozessserver. Konfigurieren Sie Netzwerke so, dass die Azure-VMs den Prozessserver erreichen können.
3. Beachten Sie, dass die Replikation von Azure zu einem lokalen Standort nur über das S2S-VPN oder das private Peering Ihres ExpressRoute-Netzwerks erfolgen kann. Vergewissern Sie sich, dass für diesen Netzwerkkanal genügend Bandbreite zur Verfügung steht.

## <a name="deploy-a-separate-master-target-server"></a>Bereitstellen eines separaten Masterzielservers

Der Masterzielserver empfängt Failbackdaten. Standardmäßig wird der Masterzielserver auf dem lokalen Konfigurationsserver ausgeführt. Je nach Datenverkehrsvolumen beim Failback müssen Sie jedoch u.U. einen separaten Masterzielserver für das Failback erstellen. Gehen Sie zum Erstellen wie folgt vor:

* [Richten Sie einen Linux-Masterzielserver](vmware-azure-install-linux-master-target.md) für Failbacks von Linux-VMs ein. Dies ist erforderlich. Beachten Sie, dass der Masterzielserver auf der LVM nicht unterstützt wird.
* Erstellen Sie optional einen separaten Masterzielserver für das Failback virtueller Windows-Computer. Führen Sie zu diesem Zweck das einheitliche Setup erneut aus, und wählen Sie dabei die Erstellung eines Masterzielservers aus. [Weitere Informationen](site-recovery-plan-capacity-vmware.md#deploy-additional-master-target-servers) 

Nachdem Sie einen Masterzielserver erstellt haben, führen Sie folgende Aufgaben aus:

- Wenn sich der virtuelle Computer lokal auf dem vCenter-Server befindet, muss der Masterzielserver auf die VMDK-Datei (Virtual Machine Disk) des lokalen virtuellen Computers zugreifen können. Der Zugriff ist erforderlich, um die replizierten Daten auf die Datenträger des virtuellen Computers schreiben zu können. Stellen Sie sicher, dass der Datenspeicher des lokalen virtuellen Computers auf dem Host des Masterziels mit Lese-/Schreibzugriff bereitgestellt wird.
- Wenn der virtuelle Computer nicht lokal auf dem vCenter-Server vorhanden ist, muss der Site Recovery-Dienst beim erneuten Schützen einen neuen virtuellen Computer erstellen. Dieser virtuelle Computer wird auf dem ESX-Host erstellt, auf dem Sie das Masterziel erstellen. Wählen Sie den ESX-Host mit Bedacht, damit der virtuelle Failbackcomputer auf dem gewünschten Host erstellt wird.
- Es ist nicht möglich, Storage vMotion für den Masterzielserver zu verwenden. Wenn Storage vMotion für den Masterzielserver verwendet wird, kann beim Failback ein Fehler auftreten. Der virtuelle Computer kann nicht gestartet werden, weil die Datenträger nicht für ihn verfügbar sind. Um dieses Problem zu verhindern, schließen Sie die Masterzielserver aus der vMotion-Liste aus.
- Wenn für ein Masterziel nach dem erneuten Schützen eine Storage vMotion-Aufgabe durchgeführt wird, werden die geschützten VM-Datenträger, die dem Masterziel angefügt sind, zu dem Ziel der vMotion-Aufgabe migriert. Wenn Sie anschließend versuchen, ein Failback durchzuführen, schlägt die Trennung der Datenträger fehl, da die Datenträger nicht gefunden werden. Es wird dann schwierig, die Datenträger in den Speicherkonten zu finden. Sie müssen sie manuell suchen und an den virtuellen Computer anfügen. Anschließend kann der lokale virtuelle Computer neu gestartet werden.
- Fügen Sie dem vorhandenen Windows-Masterzielserver ein Aufbewahrungslaufwerk hinzu. Fügen Sie einen neuen Datenträger hinzu, und formatieren Sie das Laufwerk. Das Aufbewahrungslaufwerk wird verwendet, um die Zeitpunkte festzuhalten, zu denen der virtuelle Computer zurück an den lokalen Standort repliziert wird. Nachfolgend sind einige Kriterien eines Aufbewahrungslaufwerks aufgeführt. Wenn diese Kriterien nicht erfüllt sind, wird das Laufwerk nicht für den Masterzielserver aufgelistet:
    - Das Volume wird nicht für andere Zwecke genutzt, z.B. als Ziel der Replikation.
    - Das Volume ist nicht im Sperrmodus.
    - Das Volume ist kein Cachevolume. Die Masterzielinstallation kann auf diesem Volume nicht vorhanden sein. Das Volume für die benutzerdefinierte Prozessserver- und Masterzielinstallation kann nicht für das Aufbewahrungsvolume ausgewählt werden. Wenn der Prozessserver und Masterzielserver auf einem Volume installiert sind, ist das Volume ein Cachevolume des Masterziels.
    - Der Dateisystemtyp des Volumes ist nicht FAT oder FAT32.
    - Die Volumekapazität ist ungleich null.
    - Das Standardaufbewahrungsvolume für Windows ist das R-Volume.
    - Das Standardaufbewahrungsvolume für Linux ist „/mnt/retention“.
- Sie müssen ein neues Laufwerk hinzufügen, wenn Sie einen vorhandenen Prozessserver-/Konfigurationsservercomputer, eine Skalierungsgruppe oder einen Prozessserver-/Masterzielservercomputer verwenden. Das neue Laufwerk muss die oben genannten Anforderungen erfüllen. Wenn das Aufbewahrungslaufwerk nicht vorhanden ist, wird es in der Auswahldropdownliste im Portal nicht angezeigt. Nach dem Hinzufügen eines Laufwerks zum lokalen Masterziel dauert es bis zu 15 Minuten, bis das Laufwerk in der Auswahl im Portal angezeigt wird. Sie können auch den Konfigurationsserver aktualisieren, wenn das Laufwerk nach 15 Minuten nicht angezeigt wird.
- Installieren Sie VMware-Tools oder open-vm-tools auf dem Masterzielserver. Ohne die Tools werden die Datenspeicher auf dem ESXi-Host des Masterziels nicht erkannt.
- Legen Sie die Einstellung `disk.EnableUUID=true` in den Konfigurationsparametern des virtuellen Masterzielcomputers in VMware fest. Wenn diese Zeile nicht vorhanden ist, fügen Sie sie hinzu. Diese Einstellung ist erforderlich, um der VMDK eine konsistente UUID bereitzustellen, damit sie ordnungsgemäß eingebunden wird.
- Dem ESX-Host, auf dem das Masterziel erstellt wird, muss mindestens ein VMFS-Datenspeicher (Virtual Machine File System, VM-Dateisystem) angefügt sein. Wenn keine VMFS-Datenspeicher angefügt sind, ist die Eingabe **Datenspeicher** auf der Seite für das erneute Schützen leer, und Sie können den Vorgang nicht fortsetzen.
- Auf den Datenträgern des Masterzielservers können keine Momentaufnahmen enthalten sein. Wenn Momentaufnahmen vorhanden sind, schlagen das erneute Schützen und das Failback fehl.
- Das Masterziel kann nicht über einen Paravirtual-SCSI-Controller verfügen. Als Controller kann nur ein LSI Logic-Controller verwendet werden. Ohne LSI Logic-Controller schlägt das erneute Schützen fehl.
- Für jede beliebige Instanz können dem Masterziel jeweils maximal 60 Datenträger angefügt werden. Wenn virtuelle Computer, die auf dem lokalen Masterziel erneut geschützt werden, zusammen mehr als 60 Datenträger aufweisen, tritt beim erneuten Schützen auf dem Masterziel ein Fehler auf. Stellen Sie sicher, dass auf dem Masterzielserver genügend Datenträgerslots verfügbar sind, oder stellen Sie zusätzliche Masterzielserver bereit.
    

## <a name="enable-reprotection"></a>Aktivieren des erneuten Schutzes

Nachdem ein virtueller Computer in Azure gestartet wurde, dauert es eine Weile, bis der Agent auf dem Konfigurationsserver registriert wird (bis zu 15 Minuten). Während dieses Zeitraums können Sie keinen erneuten Schutz aktivieren, und es wird eine Fehlermeldung mit dem Hinweis angezeigt, dass der Agent nicht installiert ist. Warten Sie in diesem Fall einige Minuten, und versuchen Sie dann noch einmal, das erneute Schützen durchzuführen:


1. Wählen Sie **Tresor** > **Replizierte Elemente**. Klicken Sie mit der rechten Maustaste auf den virtuellen Computer, für den ein Failover durchgeführt wurde, und wählen Sie dann **Erneut schützen**. Sie können auch über die Befehlsschaltflächen den Computer und dann **Erneut schützen** auswählen.
2. Vergewissern Sie sich, dass die Schutzrichtung **Azure auf lokal** ausgewählt ist.
3. Wählen Sie unter **Masterzielserver** und **Prozessserver** den lokalen Masterzielserver und den Prozessserver aus.  
4. Wählen Sie für **Datenspeicher** den Datenspeicher aus, in dem die Datenträger lokal wiederhergestellt werden sollen. Diese Option wird verwendet, wenn der lokale virtuelle Computer gelöscht wird und Sie neue Datenträger erstellen müssen. Diese Option wird ignoriert, wenn die Datenträger bereits vorhanden sind. Sie müssen noch einen Wert angeben.
5. Wählen Sie das Aufbewahrungslaufwerk aus.
6. Die Failbackrichtlinie wird automatisch ausgewählt.
7. Wählen Sie **OK** aus, um das erneute Schützen zu starten. Ein Auftrag beginnt mit der Replikation des virtuellen Computers von Azure am lokalen Standort. Sie können den Fortschritt auf der Registerkarte **Aufträge** nachverfolgen. Nach dem erfolgreichen erneuten Schützen befindet sich der virtuelle Computer in einem geschützten Zustand.

Beachten Sie die folgenden Informationen:
- Wenn die Wiederherstellung an einem anderen Ort erfolgen soll (falls der lokale virtuelle Computer gelöscht wurde), wählen Sie das für den Masterzielserver konfigurierte Aufbewahrungslaufwerk und den entsprechenden Datenspeicher aus. Beim Ausführen des Failbacks zum lokalen Standort verwenden die virtuellen VMware-Computer im Failback-Schutzplan den gleichen Datenspeicher wie der Masterzielserver. Anschließend wird in vCenter ein neuer virtueller Computer erstellt.
- Wenn Sie den virtuellen Computer in Azure auf einem vorhandenen lokalen virtuellen Computer wiederherstellen möchten, stellen Sie die Datenspeicher des lokalen virtuellen Computers mit Lese-/Schreibzugriff für den ESXi-Host des Masterzielservers bereit.

    ![Dialogfeld „Erneut schützen“](./media/vmware-azure-reprotect/reprotectinputs.png)

- Sie können das erneute Schützen auch auf der Ebene eines Wiederherstellungsplans durchführen. Eine Replikationsgruppe kann nur mithilfe eines Wiederherstellungsplans erneut geschützt werden. Sie müssen beim erneuten Schützen mithilfe eines Wiederherstellungsplans für jeden geschützten Computer die Werte angeben.
- Verwenden Sie zum erneuten Schützen einer Replikationsgruppe denselben Masterzielserver. Wenn Sie für das erneute Schützen einer Replikationsgruppe einen anderen Masterzielserver verwenden, kann der Server keinen gemeinsamen Zeitpunkt angeben.
- Der lokale virtuelle Computer wird während des erneuten Schützens deaktiviert. Dies hilft, die Konsistenz der Daten während der Replikation sicherzustellen. Schalten Sie den virtuellen Computer nach Abschluss des erneuten Schützens nicht ein.



## <a name="common-issues"></a>Häufige Probleme

- Wenn Sie die vCenter-Ermittlung schreibgeschützter Benutzer ausführen und virtuelle Computer schützen, ist das Schützen erfolgreich, und das Failover funktioniert. Während des erneuten Schützens tritt beim Failover ein Fehler auf, da die Datenspeicher nicht ermittelt werden können. Ein Symptom dafür ist, dass die Datenspeicher während des erneuten Schützens nicht aufgelistet werden. Zum Beheben dieses Problems können Sie die vCenter-Anmeldeinformationen mit einem entsprechenden Konto aktualisieren, das über die erforderlichen Berechtigungen verfügt, und dann den Auftrag wiederholen. 
- Wenn Sie ein Failback für einen virtuellen Linux-Computer durchführen und ihn lokal ausführen, sehen Sie, dass das Netzwerk-Manager-Paket auf dem Computer deinstalliert wurde. Der Grund für die Deinstallation ist, dass beim Wiederherstellen des virtuellen Computers in Azure das Netzwerk-Manager-Paket entfernt wird.
- Wenn ein virtueller Linux-Computer mit einer statischen IP-Adresse konfiguriert ist und ein Failover zu Azure ausgeführt wird, wird die IP-Adresse über DHCP abgerufen. Nach dem Failover zum lokalen Standort verwendet der virtuelle Computer weiterhin DHCP zum Abrufen der IP-Adresse. Melden Sie sich manuell beim Computer an, und setzen Sie dann ggf. die IP-Adresse auf die statische Adresse zurück. Ein virtueller Windows-Computer kann seine statische IP-Adresse wiederbeschaffen.
- Wenn Sie die kostenlose Edition ESXi 5.5 oder die kostenlose Edition vSphere 6 Hypervisor verwenden, ist das Failover erfolgreich, aber das Failback nicht. Sie müssen auf die Evaluierungslizenz eines der Programme aktualisieren, um das Failback zu aktivieren.
- Wenn der Konfigurationsserver auf dem Prozessserver nicht erreichbar ist, können Sie Telnet verwenden, um die Konnektivität mit dem Konfigurationsserver an Port 443 zu überprüfen. Sie können auch versuchen, den Konfigurationsserver auf dem Prozessserver per Ping zu erreichen. Ein Prozessserver sollte außerdem einen Takt aufweisen, wenn er mit dem Konfigurationsserver verbunden ist.
- Für einen Server vom Typ Windows Server 2008 R2 SP1, der als physischer lokaler Server geschützt wird, kann kein Failback von Azure zu einem lokalen Standort durchgeführt werden.
- In den folgenden Situationen ist kein Failback möglich:
    - Sie haben Computer zu Azure migriert. [Weitere Informationen](migrate-overview.md#what-do-we-mean-by-migration)
    - Sie haben eine VM in eine andere Ressourcengruppe verschoben.
    - Sie haben die Azure-VM gelöscht.
    - Sie haben den Schutz des virtuellen Computers deaktiviert.
    - Sie haben den virtuellen Computer manuell in Azure erstellt. Der Computer muss zuerst lokal geschützt und dann muss ein Failover zu Azure für ihn ausgeführt worden sein, damit er erneut geschützt werden kann.
    - Sie können nur Failbacks zum ESXi-Host ausführen. Sie können kein Failback von VMware-VMs oder physischen Servern zu Hyper-V-Hosts, physischen Computern oder VMware-Arbeitsstationen ausführen.


## <a name="next-steps"></a>Nächste Schritte

Wenn sich der virtuelle Computer in einem geschützten Zustand befindet, können Sie ein [Failback initiieren](vmware-azure-failback.md). Beim Failback wird der virtuelle Computer in Azure heruntergefahren und der lokale virtuelle Computer gestartet. Erwarten Sie eine Ausfallzeit für die Anwendung. Führen Sie das Failback zu einer Zeit aus, zu der dies für Ihre Anwendung kein Problem darstellt.


