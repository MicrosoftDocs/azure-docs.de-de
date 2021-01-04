---
title: Durchführen eines Betriebssystemupgrades für SAP HANA in Azure (große Instanzen) | Microsoft-Dokumentation
description: Durchführen von Betriebssystemupgrades für SAP HANA in Azure (große Instanzen)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/04/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cdc6dd49fe98085edf3c6fb16606b9f540b5a3a0
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96608689"
---
# <a name="operating-system-upgrade"></a>Betriebssystemupgrade
In diesem Dokument werden die Details zu Betriebssystemupgrades für SAP HANA (große Instanzen) beschrieben.

>[!NOTE]
>Das Betriebssystemupgrade liegt in der Zuständigkeit des Kunden. Der Microsoft Operations-Support kann Sie dabei auf wichtige Punkte hinweisen, die beim Upgrade zu beachten sind. Bevor Sie Planungen für ein Upgrade treffen, sollten Sie auch den Hersteller Ihres Betriebssystems zurate ziehen.

> [!NOTE]
> Dieser Artikel enthält Verweise auf den Begriff *Blacklist*, der von Microsoft nicht mehr verwendet wird. Sobald der Begriff aus der Software entfernt wurde, wird er auch aus diesem Artikel entfernt.

Das Microsoft Operations-Team installiert das Betriebssystem während der Bereitstellung der HLI-Einheiten.
Im Lauf der Zeit müssen Sie Wartungsvorgänge für das Betriebssystem für die HLI-Einheit vornehmen (z.B. Patches erstellen, optimieren, Upgrades durchführen).

Bevor Sie bedeutende Änderungen am Betriebssystem vornehmen (z. B. ein Upgrade von SP1 auf SP2), müssen Sie sich zunächst an das Microsoft Operations-Team wenden, indem Sie ein Supportticket öffnen.

Geben Sie folgende Informationen in Ihrem Ticket an:

* Ihre HLI-Abonnement-ID
* Ihren Servernamen
* Die Patchebene, die Sie anwenden möchten
* Das Datum, an dem Sie diese Änderung planen 

Es wird empfohlen, dieses Ticket mindestens eine Woche vor dem gewünschten Upgrade zu öffnen, damit das Operations-Team die gewünschte Firmwareversion kennt.

Die Supportmatrix der anderen SAP HANA-Versionen mit den verschiedenen Linux-Versionen finden Sie im [SAP-Hinweis 2235581](https://launchpad.support.sap.com/#/notes/2235581).

## <a name="known-issues"></a>Bekannte Probleme

Im Folgenden werden einige bekannte Probleme bei Upgrades erläutert:
- Bei SKUs von SKU-Typ II wird Software Foundation Software (SFS) nach der Durchführung eines Betriebssystemupgrades entfernt. Nach dem Betriebssystemupgrade müssen Sie die kompatible SFS-Version neu installieren.
- Für Ethernet-Kartentreiber (ENIC und FNIC) wird ein Rollback auf die ältere Version ausgeführt. Nach dem Upgrade müssen Sie die kompatible Version der Treiber neu installieren.

## <a name="sap-hana-large-instance-type-i-recommended-configuration"></a>Empfohlene Konfiguration für SAP HANA (große Instanzen, Typ I)

Die Betriebssystemkonfiguration kann sich im Lauf der Zeit aufgrund angewendeter Patches, Systemupgrades und vom Kunden vorgenommener Änderungen von den empfohlenen Einstellungen fortbewegen. Darüber hinaus identifiziert Microsoft die erforderlichen Updates für bestehende Systeme, um sicherzustellen, dass diese optimal für die beste Leistung und Ausfallsicherheit konfiguriert sind. Die folgenden Anweisungen enthalten Empfehlungen, die sich auf die Netzwerkleistung, die Systemstabilität und die optimale HANA-Leistung beziehen.

### <a name="compatible-enicfnic-driver-versions"></a>Kompatible eNIC/fNIC-Treiberversionen
  Um eine ordnungsgemäße Netzwerkleistung und Systemstabilität zu gewährleisten, sollte unbedingt darauf geachtet werden, dass die betriebssystemspezifische geeignete Version der eNIC- und fNIC-Treiber installiert ist, wie in der folgenden Kompatibilitätstabelle dargestellt. Server werden mit kompatiblen Versionen an Kunden ausgeliefert. Beim Anwenden von Patches auf das Betriebssystem/den Kernel kann in manchen Fällen ein Rollback der Treiber auf die Standardtreiberversionen erfolgen. Stellen Sie sicher, dass nach Patchvorgängen an Betriebssystem/Kernel die passende Treiberversion ausgeführt wird.
       
      
  |  Betriebssystemhersteller    |  Betriebssystem-Paketversion     |  Firmware Version  |  eNIC-Treiber |  fNIC-Treiber | 
  |---------------|-------------------------|--------------------|--------------|--------------|
  |   SuSE        |  SLES 12 SP2            |   3.1.3h           |  2.3.0.40    |   1.6.0.34   |
  |   SuSE        |  SLES 12 SP3            |   3.1.3h           |  2.3.0.44    |   1.6.0.36   |
  |   SuSE        |  SLES 12 SP4            |   3.2.3i           |  4.0.0.6     |   2.0.0.60   |
  |   SuSE        |  SLES 12 SP2            |   3.2.3i           |  2.3.0.45    |   1.6.0.37   |
  |   SuSE        |  SLES 12 SP3            |   3.2.3i           |  2.3.0.43    |   1.6.0.36   |
  |   SuSE        |  SLES 12 SP5            |   3.2.3i           |  4.0.0.8     |   2.0.0.60   |
  |   Red Hat     |  RHEL 7.2               |   3.1.3h           |  2.3.0.39    |   1.6.0.34   |
 

### <a name="commands-for-driver-upgrade-and-to-clean-old-rpm-packages"></a>Befehle für das Treiberupgrade und zum Bereinigen alter RPM-Pakete

#### <a name="command-to-check-existing-installed-drivers"></a>Befehl zum Überprüfen vorhandener installierter Treiber
```
rpm -qa | grep enic/fnic 
```
#### <a name="delete-existing-enicfnic-rpm"></a>Löschen vorhandener eNIC/fNIC rpm
```
rpm -e <old-rpm-package>
```
#### <a name="install-the-recommended-enicfnic-driver-packages"></a>Installieren der empfohlenen eNIC-/fNIC-Treiberpakete
```
rpm -ivh <enic/fnic.rpm> 
```

#### <a name="commands-to-confirm-the-installation"></a>Befehle zum Bestätigen der Installation
```
modinfo enic
modinfo fnic
```

#### <a name="steps-for-enicfnic-drivers-installation-during-os-upgrade"></a>Schritte zur Installation von eNIC/fNIC-Treibern während des Betriebssystemupgrades

* Upgrade der Betriebssystemversion
* Entfernen alter RPM-Pakete
* Installieren kompatibler eNIC/fNIC-Treiber entsprechend der installierten Betriebssystemversion
* Neustarten des Systems
* Überprüfen der eNIC/fNIC-Version nach dem Neustart


### <a name="suse-hlis-grub-update-failure"></a>SuSE HLIs GRUB-Updatefehler
SAP in Azure HANA (große Instanzen, Typ I) kann sich nach dem Upgrade in einem nicht startfähigen Zustand befinden. Das Problem lässt sich mit dem unten beschriebenen Verfahren beheben.
#### <a name="execution-steps"></a>Ausführungsschritte


*   Führen Sie den `multipath -ll`-Befehl aus.
*   Rufen Sie die LUN ID ab, deren Größe ungefähr 50G beträgt, oder verwenden Sie den Befehl: `fdisk -l | grep mapper`
*   Aktualisieren Sie die Datei `/etc/default/grub_installdevice` mit der Zeile `/dev/mapper/<LUN ID>`. Beispiel: /dev/mapper/3600a09803830372f483f495242534a56
>[!NOTE]
>Die LUN ID unterscheidet sich auf den einzelnen Servern.


### <a name="disable-edac"></a>Deaktivieren von EDAC 
   Das EDAC-Modul (Error Detection And Correction) unterstützt das Erkennen und Beheben von Speicherfehlern. Allerdings erfüllt die zugrundeliegende Hardware für SAP HANA in Azure (große Instanzen, Typ I) bereits die gleiche Funktion. Die Aktivierung der gleichen Funktion auf Hardware- und Betriebssystemebene kann zu Konflikten führen und gelegentliches, außerplanmäßiges Herunterfahren des Servers bewirken. Daher empfiehlt es sich, das Betriebssystemmodul zu deaktivieren.

#### <a name="execution-steps"></a>Ausführungsschritte

* Überprüfen Sie, ob das EDAC-Modul aktiviert ist. Wenn der Befehl unten eine Ausgabe zurückgibt, bedeutet dies, dass das Modul aktiviert ist. 
```
lsmod | grep -i edac 
```
* Deaktivieren Sie die Module, indem Sie der Datei `/etc/modprobe.d/blacklist.conf` die folgenden Zeilen anfügen:
```
blacklist sb_edac
blacklist edac_core
```
Ein Neustart ist erforderlich, damit die Änderungen wirksam werden. Führen Sie den `lsmod`-Befehl aus, um zu überprüfen, dass das Modul nicht in der Ausgabe vorhanden ist.

### <a name="kernel-parameters"></a>Kernelparameter
   Vergewissern Sie sich, dass die richtigen Einstellungen für `transparent_hugepage`, `numa_balancing`, `processor.max_cstate`, `ignore_ce` und `intel_idle.max_cstate` angewendet werden.

* intel_idle.max_cstate=1
* processor.max_cstate=1
* transparent_hugepage=never
* numa_balancing=disable
* mce=ignore_ce

#### <a name="execution-steps"></a>Ausführungsschritte

* Fügen Sie diese Parameter der Zeile `GRB_CMDLINE_LINUX` in der Datei `/etc/default/grub` hinzu.
```
intel_idle.max_cstate=1 processor.max_cstate=1 transparent_hugepage=never numa_balancing=disable mce=ignore_ce
```
* Erstellen Sie eine neue Grub-Datei.
```
grub2-mkconfig -o /boot/grub2/grub.cfg
```
* Starten Sie das System neu.


## <a name="next-steps"></a>Nächste Schritte
- Informationen zur SKU von Typ I für Betriebssystemsicherungen finden Sie unter [Sicherung und Wiederherstellung](hana-overview-high-availability-disaster-recovery.md).
- Unter [OS-Sicherung und -Wiederherstellung für Typ-II-SKUs](os-backup-type-ii-skus.md) finden Sie weitere Informationen zu SKU-Klassen des Typs II.
