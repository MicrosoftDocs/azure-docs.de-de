---
title: Prinzipien der Notfallwiederherstellung und Vorbereitung darauf unter SAP HANA in Azure (große Instanzen) | Microsoft-Dokumentation
description: Prinzipien der Notfallwiederherstellung und Vorbereitung darauf unter SAP HANA in Azure (große Instanzen)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7f0782738a8aa57b2bc0b87e1378972e3fa5d31c
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967855"
---
# <a name="disaster-recovery-principles"></a>Prinzipien für die Notfallwiederherstellung

Für große HANA-Instanzen ist eine Notfallwiederherstellungsfunktion zwischen Stapeln für HANA (große Instanzen) in verschiedenen Azure-Regionen verfügbar. Wenn Sie beispielsweise Einheiten von HANA (große Instanzen) in der Azure-Region „USA, Westen“ bereitstellen, können Sie Einheiten von HANA (große Instanzen) in der Region „USA, Osten“ als Notfallwiederherstellungseinheiten verwenden. Die Notfallwiederherstellung wird wie bereits erwähnt nicht automatisch konfiguriert, da sie eine weitere kostenpflichtige Einheit von HANA (große Instanzen) in der Notfallwiederherstellungsregion erfordert. Die Notfallwiederherstellungseinrichtung kann mit zentralem und mit horizontalem Hochskalieren verwendet werden. 

In den bislang bereitgestellten Szenarien haben Kunden die Einheit in der Notfallwiederherstellungsregion zum Ausführen produktionsfremder Systeme mit einer installierten HANA-Instanz verwendet. Die Einheit von HANA (große Instanzen) muss der gleichen SKU angehören wie die für die Produktion verwendete SKU. In der folgenden Abbildung ist dargestellt, wie die Datenträgerkonfiguration zwischen der Servereinheit in der Azure-Produktionsregion und der Region für die Notfallwiederherstellung aussieht:

![Konfiguration mit Notfallwiederherstellungseinrichtung aus Datenträgersicht](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_setup.PNG)

Wie in dieser Übersichtsgrafik dargestellt, müssen Sie anschließend eine zweite Gruppe von Datenträgervolumes anfordern. Die Zieldatenträgervolumes weisen dieselbe Größe wie die Produktionsvolumes für die Produktionsinstanz in den Notfallwiederherstellungseinheiten auf. Diese Datenträgervolumes sind mit der HANA-Servereinheit (große Instanz) am Standort der Notfallwiederherstellung verknüpft. Die folgenden Volumes werden von der Produktionsregion auf den Standort für die Notfallwiederherstellung repliziert:

- /hana/data
- /hana/logbackups 
- /hana/shared (einschließlich „/usr/sap“)

Das Volume „/hana/log“ wird nicht repliziert, da das SAP HANA-Transaktionsprotokoll für ein solches Wiederherstellungsverfahren basierend auf diesen Volumes nicht erforderlich ist. 

Die bereitgestellte Notfallwiederherstellungsfunktion basiert auf der Speicherreplikationsfunktion der Infrastruktur für HANA (große Instanzen). Die speicherseitig verwendete Funktion ist kein konstanter Datenstrom mit Änderungen, die asynchron repliziert werden, wenn Änderungen am Speichervolume auftreten. Stattdessen basiert der Mechanismus darauf, dass in regelmäßigen Abständen Momentaufnahmen dieser Volumes erstellt werden. Anschließend werden die Veränderungen zwischen einer bereits replizierten und einer neuen, noch nicht replizierten Momentaufnahme auf Zieldatenträgervolumes am Standort für die Notfallwiederherstellung übertragen.  Diese Momentaufnahmen werden auf den Volumes gespeichert und müssen bei einer Notfallwiederherstellung auf diesen Volumes wiederhergestellt werden.  

Die erste Übertragung der gesamten Daten des Volumes sollte durchgeführt werden, bevor die Menge der Daten kleiner wird als die Veränderungen zwischen den Momentaufnahmen. Somit enthalten die Volumes am Standort für die Notfallwiederherstellung sämtliche Volumemomentaufnahmen, die am Produktionsstandort durchgeführt wurden. Sie können das Notfallwiederherstellungssystem dann verwenden, um zu einem früheren Status zurückzukehren und verloren gegangene Daten wiederherzustellen, ohne dass ein Rollback des Produktionssystems erforderlich ist.

Bei MCOD-Bereitstellungen mit mehreren unabhängigen SAP HANA-Instanzen auf einer HANA-Einheit (große Instanzen) wird davon ausgegangen, dass der Speicher aller SAP HANA-Instanzen auf der Notfallwiederherstellungsseite (DR) repliziert wird.

In Fällen, in denen Sie die HANA Systemreplikation als Hochverfügbarkeitsfunktion für Ihren Produktionsstandort und die speicherbasierte Replikation für den DR-Standort verwenden, werden die Volumes beider Knoten am primären Standort auf die DR-Instanz repliziert. Sie müssen zusätzlichen Speicher (mit derselben Größe wie am primären Knoten) am DR-Standort kaufen, um die Replikation sowohl des primären als auch des sekundären Knotens für die Notfallwiederherstellung zu ermöglichen. 



>[!NOTE]
>Die Speicherreplikationsfunktion von HANA (große Instanzen) spiegelt und repliziert Speichermomentaufnahmen. Wenn Sie keine Speichermomentaufnahmen durchführen, wie in diesem Artikel im Abschnitt zur „Sicherung und Wiederherstellung“ erläutert, ist keine Replikation am Standort für die Notfallwiederherstellung möglich. Die Erstellung von Speichermomentaufnahmen ist eine Voraussetzung für die Speicherreplikation am Standort für die Notfallwiederherstellung.



## <a name="preparation-of-the-disaster-recovery-scenario"></a>Vorbereitung für das Notfallwiederherstellungsszenario
In diesem Szenario wird davon ausgegangen, dass in der Azure-Produktionsregion ein Produktionssystem in HANA (große Instanzen) ausgeführt wird. Für die folgenden Schritte wird angenommen, dass die SID dieses HANA-Systems „PRD“ lautet und dass Sie in HANA (große Instanzen) in der Azure-Region für die Notfallwiederherstellung ein nicht für die Produktion bestimmtes System ausführen. Für das letztgenannte System lautet die SID „TST“. Diese Konfiguration ist in der folgenden Abbildung dargestellt:

![Starten der Notfallwiederherstellungseinrichtung](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start1.PNG)

Wenn die Serverinstanz noch nicht mit dem zusätzlichen Speichervolumesatz angefordert wurde, fügt das Dienstverwaltungsteam für SAP HANA in Azure den zusätzlichen Volumesatz als Ziel für das Produktionsreplikat an die HANA-Einheit (große Instanz) an, auf der die HANA-Instanz „TST“ ausgeführt wird. Zu diesem Zweck müssen Sie die SID Ihrer HANA-Produktionsinstanz angeben. Nachdem das Dienstverwaltungsteam für SAP HANA in Azure sichergestellt hat, dass diese Volumes angefügt wurden, müssen Sie die Volumes in die HANA-Einheit (große Instanz) einbinden.

![Nächster Schritt: Notfallwiederherstellungseinrichtung](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start2.PNG)

Als Nächstes installieren Sie in der Azure-Region für die Notfallwiederherstellung die zweite SAP HANA-Instanz auf der HANA-Einheit (große Instanzen), in der Sie die HANA-Instanz „TST“ ausführen. Die neu installierte SAP HANA-Instanz muss dieselbe SID aufweisen. Die erstellten Benutzer müssen dieselbe UID und Gruppen-ID wie die Produktionsinstanz aufweisen. Ausführliche Informationen finden Sie unter [Sichern und Wiederherstellen](hana-backup-restore.md). Wenn die Installation erfolgreich war, müssen Sie Folgendes tun:

- Führen Sie Schritt 2 der Vorbereitung der Speichermomentaufnahme durch, wie unter [Sichern und Wiederherstellen](hana-backup-restore.md) beschrieben.
- Erstellen Sie einen öffentlichen Schlüssel für die Notfallwiederherstellungseinheit der HANA-Einheit (große Instanzen), falls Sie dies noch nicht durchgeführt haben. Weitere Informationen finden Sie in Schritt 3 der unter [Sichern und Wiederherstellen](hana-backup-restore.md) beschriebenen Vorbereitung der Speichermomentaufnahme.
- Behalten Sie die Datei *HANABackupCustomerDetails.txt* für die neue HANA-Instanz bei, und testen Sie, ob Verbindungen mit dem Speicher ordnungsgemäß funktionieren.  
- Beenden Sie die neu installierte SAP HANA-Instanz auf der HANA-Einheit (große Instanzen) in der Azure-Region für die Notfallwiederherstellung.
- Heben Sie die Bereitstellung dieser PRD-Volumes auf, und wenden Sie sich an das Dienstverwaltungsteam für SAP HANA in Azure. Die Volumes können nicht mehr für die Einheit bereitgestellt werden, da nicht auf diese zugegriffen werden kann, wenn sie als Speicherreplikationsziel fungiert.  

![Diagramm: Replikationsbeziehung zwischen den PRD-Volumes in der Azure-Produktionsregion und den PRD-Volumes in der Azure-Region für die Notfallwiederherstellung](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start3.PNG)

Das Betriebsteam stellt die Replikationsbeziehung zwischen den PRD-Volumes in der Azure-Produktionsregion und den PRD-Volumes in der Azure-Region für die Notfallwiederherstellung her.

>[!IMPORTANT]
>Das Volume „/hana/log“ wird nicht repliziert, da es nicht erforderlich ist, die replizierte SAP HANA-Datenbank am Notfallwiederherstellungsstandort in einem konsistenten Zustand wiederherzustellen.

Als Nächstes müssen Sie den Sicherungszeitplan für Speichermomentaufnahmen einrichten oder anpassen, um Ihren RTO- und RPO-Wert für den Notfall zu ermitteln. Legen Sie zur Minimierung der Recovery Point Objective folgende Replikationsintervalle im HANA-Dienst (große Instanz) fest:
- Legen Sie für die durch die kombinierte Momentaufnahme abgedeckten Volumes (Momentaufnahmetyp **hana**) fest, dass sie alle 15 Minuten an die entsprechenden Speichervolumeziele am Standort für die Notfallwiederherstellung repliziert werden.
- Legen Sie für das Volume für die Transaktionsprotokollsicherung (Momentaufnahmetyp **logs**) fest, dass es alle drei Minuten an den entsprechenden Speichervolumezielen am Standort für die Notfallwiederherstellung repliziert wird.

Richten Sie zur Minimierung der Recovery Point Objective Folgendes ein:
- Führen Sie alle 30 bis 60 Minuten eine Speichermomentaufnahme vom Typ **hana** (siehe „Schritt 7: Durchführen von Momentaufnahmen“) durch.
- Führen Sie alle fünf Minuten SAP HANA-Transaktionsprotokollsicherungen durch.
- Führen Sie alle 5 bis 15 Minuten Speichermomentaufnahmen vom Typ **logs** durch. Mit diesem Intervall erzielen Sie einen RPO-Wert von etwa 15 bis 25 Minuten.

Die folgende Grafik zeigt eine mögliche Sequenz der Transaktionsprotokollsicherungen und Speichermomentaufnahmen sowie der Replikation des Volumes mit der HANA-Transaktionsprotokollsicherung und von „/hana/data“ und „/hana/shared“ (einschließlich „/usr/sap“) bei Verwendung dieser Einstellungen:

 ![Beziehung zwischen Momentaufnahme der Transaktionsprotokollsicherung und Momentaufnahmespiegelung im Zeitverlauf](./media/hana-overview-high-availability-disaster-recovery/snapmirror.PNG)

Um bei der Notfallwiederherstellung einen noch besseren RPO-Wert zu erzielen, können Sie die HANA-Transaktionsprotokollsicherungen aus SAP HANA in Azure (große Instanzen) in die andere Azure-Region kopieren. Führen Sie für diese weitere Reduzierung des RPO-Werts die folgenden Schritte aus:

1. Sichern Sie das HANA-Transaktionsprotokoll so häufig wie möglich unter „/hana/logbackups“.
1. Kopieren Sie mithilfe von rsync die Transaktionsprotokollsicherungen auf die virtuellen Azure-Computer, die auf der NFS-Freigabe gehostet werden. Die VMs befinden sich in virtuellen Azure-Netzwerken in der Azure-Produktionsregion und den Regionen für die Notfallwiederherstellung. Sie müssen beide virtuelle Azure-Netzwerke mit den großen HANA-Produktionsinstanzen in Azure verbinden. Weitere Informationen finden Sie in der Grafik im Abschnitt [Überlegungen zu Netzwerken für die Notfallwiederherstellung mit HANA (große Instanzen)](#Network-considerations-for-disaster recovery-with-HANA-Large-Instances). 
1. Belassen Sie die Transaktionsprotokollsicherungen in der Region auf der VM, die an den exportierten NFS-Speicher angefügt ist.
1. Ergänzen Sie bei einer Notfallwiederherstellung die Transaktionsprotokollsicherungen aus dem Volume „/hana/logbackups“ mit neueren Transaktionsprotokollsicherungen auf der NFS-Freigabe am Standort für die Notfallwiederherstellung. 
1. Starten Sie eine Wiederherstellung der Transaktionsprotokollsicherung auf die neueste Sicherung, die in der Region für die Notfallwiederherstellung gespeichert werden konnte.

Wenn das Betriebsteam für HANA (große Instanzen) bestätigt, dass die Replikationsbeziehung eingerichtet ist, und Sie die Speichermomentaufnahme-Sicherungen für die Ausführung starten, beginnt die Replikation der Daten.

![Schritt zur Notfallwiederherstellungseinrichtung vor der Einrichtung der Replikation](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start4.PNG)

Bei der Replikation werden die Momentaufnahmen nicht auf den PRD-Volumes in den Azure-Regionen für die Notfallwiederherstellung wiederhergestellt. Sie werden lediglich gespeichert. Wenn die Volumes in einem solchen Zustand eingebunden werden, stellen sie den Status dar, in dem Sie die Bereitstellung dieser Volumes aufgehoben haben, nachdem die SAP HANA-Instanz „PRD“ in der Servereinheit in der Azure-Region für die Notfallwiederherstellung installiert wurde. Darüber hinaus bezeichnen sie die Speichersicherungen, die noch nicht wiederhergestellt wurden.

Bei einem Failover können Sie anstelle der neuesten Speichermomentaufnahme auch eine ältere Speichermomentaufnahme wiederherstellen.

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie [Failoverprozedur für die Notfallwiederherstellung](hana-failover-procedure.md).
