---
title: Ausführen eines Failovers und einer Notfallwiederherstellung für StorSimple Virtual Array
description: Erfahren Sie mehr über die Notfallwiederherstellung für Ihr Microsoft Azure StorSimple Virtual Array sowie die ausführlichen Schritte für ein Failover auf ein anderes virtuelles Array.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 3c1f9c62-af57-4634-a0d8-435522d969aa
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 509a3ac383cebd91821e9c4b872c253ab3b0a947
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005859"
---
# <a name="disaster-recovery-and-device-failover-for-your-storsimple-virtual-array-via-azure-portal"></a>Notfallwiederherstellung und Gerätefailover für StorSimple Virtual Array über das Azure-Portal

## <a name="overview"></a>Übersicht
Dieser Artikel beschreibt die Notfallwiederherstellung für Ihr Microsoft Azure StorSimple Virtual Array sowie die ausführlichen Schritte für ein Failover auf ein anderes virtuelles Array. Durch ein Failover können Sie Ihre Daten von einem *Quellgerät* im Datencenter auf ein *Zielgerät* verschieben. Das Zielgerät kann sich am gleichen Speicherort oder einem anderen geografischen Ort befinden. Das Gerätefailover gilt für das gesamte Gerät. Während des Failovers gehen die Clouddaten für das Quellgerät in den Besitz des Zielgeräts über.

Dieser Artikel gilt nur für StorSimple Virtual Arrays. Informationen zum Failover für ein Gerät der 8000er-Serie finden Sie unter [Ausführen eines Failovers und einer Notfallwiederherstellung für das StorSimple-Gerät](./storsimple-8000-device-failover-disaster-recovery.md).

## <a name="what-is-disaster-recovery-and-device-failover"></a>Was ist Notfallwiederherstellung und Gerätefailover?

Bei einer Notfallwiederherstellung funktioniert das primäre Gerät nicht mehr. In diesem Fall können Sie die Clouddaten, die dem ausgefallenen Gerät zugeordnet sind, auf ein anderes Gerät verschieben. Sie können das primäre Gerät als *Quelle* verwenden und ein anderes Gerät als *Ziel*. Dieser Vorgang wird als *Failover* bezeichnet. Während des Failovers wechseln alle Volumes bzw. Freigaben den Besitzer und werden auf das Zielgerät übertragen. Eine Filterung der Daten ist nicht zulässig.

Die Notfallwiederherstellung ist als Wiederherstellung des gesamten Geräts ausgelegt, und es wird die auf Heat Maps basierende Staffelung und Nachverfolgung verwendet. Eine Heat Map wird definiert, indem den Daten anhand von Lese- und Schreibmustern ein Heat-Wert zugewiesen wird. Mit dieser Heat Map werden die Datenelemente mit den niedrigsten Heat-Werten zuerst in der Cloud angeordnet, und die Datenelemente mit den hohen Heat-Werten (am häufigsten verwendet) verbleiben auf der lokalen Ebene. Bei der Notfallwiederherstellung verwendet StorSimple das Wärmebild, um die Daten aus der Cloud wiederherzustellen und zu aktivieren. Mit dem Gerät werden alle Volumes/Freigaben der letzten Sicherung (intern ermittelt) abgerufen, und anhand dieser Sicherungsdaten wird eine Wiederherstellung durchgeführt. Das virtuelle Array orchestriert den gesamten Wiederherstellungsprozess.

> [!IMPORTANT]
> Das Quellgerät wird am Ende des Gerätefailovers gelöscht, und daher wird ein Failback nicht unterstützt.
> 
> 

Die Notfallwiederherstellung wird über das Feature „Gerätefailover“ koordiniert und über das Blatt **Geräte** eingeleitet. Auf diesem Blatt werden alle StorSimple-Geräte aufgeführt, die mit Ihrem StorSimple-Geräte-Manager-Dienst verbunden sind. Für jedes Gerät werden Anzeigename, Status, bereitgestellte und maximale Kapazität, Typ und Modell angezeigt.

## <a name="prerequisites-for-device-failover"></a>Voraussetzungen für das Gerätefailover

### <a name="prerequisites"></a>Voraussetzungen

Bei jedem Gerätefailover sollten die folgenden Voraussetzungen erfüllt sein:

* Das Quellgerät muss sich in einem **deaktivierten** Zustand befinden.
* Das Zielgerät muss im Azure-Portal als **Zur Einrichtung bereit** angezeigt werden. Stellen Sie ein virtuelles Zielarray mit der gleichen oder einer höheren Kapazität bereit. Verwenden Sie die lokale Webbenutzeroberfläche, um das virtuelle Zielarray zu konfigurieren und erfolgreich zu registrieren.
  
  > [!IMPORTANT]
  > Versuchen Sie nicht, das registrierte virtuelle Gerät über den Dienst zu konfigurieren. Über den Dienst sollte keine Gerätekonfiguration durchgeführt werden.
  > 
  > 
* Das Zielgerät darf nicht den gleichen Namen wie das Quellgerät haben.
* Quell- und Zielgerät müssen vom gleichen Typ sein. Ein Failover auf einen anderen Dateiserver kann nur für ein als Dateiserver konfiguriertes virtuelles Array durchgeführt werden. Dies gilt auch für einen iSCSI-Server.
* Für eine Dateiserver-Notfallwiederherstellung empfehlen wir Ihnen, das Zielgerät mit der gleichen Domäne wie das Quellgerät zu verbinden. Eine solche Konfiguration sorgt dafür, dass die Freigabeberechtigungen automatisch aufgelöst werden. Ein Failover wird nur auf ein Zielgerät in derselben Domäne unterstützt.
* Die verfügbaren Zielgeräte für die Notfallwiederherstellung sind Geräte, die im Vergleich zum Quellgerät die gleiche oder eine höhere Kapazität aufweisen. Geräte, die mit Ihrem Dienst verbunden sind, aber das Speicherplatzkriterium nicht erfüllen, stehen als Zielgeräte nicht zur Verfügung.

### <a name="other-considerations"></a>Weitere Überlegungen

* Für ein geplantes Failover:
  
  * Es ist ratsam, alle Volumes oder Freigaben auf dem Quellgerät in den Offlinezustand zu versetzen.
  * Wir empfehlen Ihnen, zuerst eine Sicherung des Geräts zu erstellen, bevor Sie mit dem Failover beginnen, um Datenverluste zu minimieren.
* Wenn es sich um ein nicht geplantes Failover handelt, verwendet das Gerät die letzte Sicherung für die Wiederherstellung der Daten.

### <a name="device-failover-prechecks"></a>Vorüberprüfungen für das Gerätefailover

Vor Beginn der Notfallwiederherstellung werden auf dem Gerät Vorüberprüfungen durchgeführt. Mit diesen Überprüfungen können Sie sicherstellen, dass keine Fehler auftreten, wenn die Notfallwiederherstellung beginnt. Die Vorüberprüfungen umfassen Folgendes:

* Überprüfen des Speicherkontos
* Überprüfen der Cloudverbindung mit Azure
* Überprüfen des verfügbaren Speicherplatzes auf dem Zielgerät
* Überprüfen, ob ein iSCSI-Server-Quellgerät-Volume über Folgendes verfügt:
  
  * gültige ACR-Namen
  * gültige IQN (nicht länger als 220 Zeichen)
  * gültige CHAP-Kennwörter (12 bis 16 Zeichen lang)

Falls eine dieser Vorüberprüfungen fehlschlägt, kann die Notfallwiederherstellung nicht durchgeführt werden. Sie müssen diese Fehler zunächst beheben, bevor Sie die Notfallwiederherstellung wiederholen können.

Nachdem die Notfallwiederherstellung erfolgreich abgeschlossen wurde, gehen die Clouddaten auf dem Quellgerät in den Besitz des Zielgeräts über. Das Quellgerät ist dann nicht mehr im Portal verfügbar. Der Zugriff auf alle Volumes/Freigaben auf dem Quellgerät ist blockiert, und das Zielgerät wird in den aktiven Zustand versetzt.

> [!IMPORTANT]
> Auch wenn das Gerät nicht mehr verfügbar ist, werden von der virtuellen Maschine, die Sie auf dem Hostsystem bereitgestellt haben, noch Ressourcen verbraucht. Nachdem die Notfallwiederherstellung erfolgreich abgeschlossen wurde, können Sie den virtuellen Computer aus Ihrem Hostsystem löschen.
> 
> 

## <a name="fail-over-to-a-virtual-array"></a>Durchführen eines Failovers auf ein virtuelles Array

Wir empfehlen Ihnen, ein anderes StorSimple Virtual Array für Ihren StorSimple-Geräte-Manager-Dienst bereitzustellen, zu konfigurieren und zu registrieren, bevor Sie dieses Verfahren ausführen.

> [!IMPORTANT]
> 
> * Das Failover eines StorSimple-Geräts der 8000e-Serie auf ein virtuelles Gerät der 1200er-Serie ist unzulässig.
> * Sie können für ein virtuelles Gerät, das für den Federal Information Processing Standard (FIPS) aktiviert ist, ein Failover auf ein anderes virtuelles Gerät mit FIPS-Aktivierung ausführen bzw. auf ein Gerät, das im Government-Portal bereitgestellt wird und für das keine FIPS-Aktivierung gilt.


Führen Sie die folgenden Schritte aus, um Ihr Gerät auf einem virtuellen StorSimple-Zielgerät wiederherzustellen.

1. Stellen Sie ein Zielgerät bereit, das die [Voraussetzungen für das Gerätefailover](#prerequisites) erfüllt, und konfigurieren Sie es. Führen Sie die Gerätekonfiguration über die lokale Webbenutzeroberfläche aus, und registrieren Sie das Gerät für Ihren StorSimple-Geräte-Manager-Dienst. Wenn Sie einen Dateiserver erstellen, wechseln Sie zu Schritt 1 im Artikel: [Deploy StorSimple Virtual Array - Set up as file server (Bereitstellen von StorSimple Virtual Array – Einrichten als Dateiserver)](storsimple-virtual-array-deploy3-fs-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device). Wenn Sie einen iSCSI-Server erstellen, wechseln Sie zu Schritt 1 im Artikel: [Deploy StorSimple Virtual Array – Set up your virtual device as an iSCSI server (Bereitstellen von StorSimple Virtual Array – Einrichten des virtuellen Geräts als iSCSI-Server)](storsimple-virtual-array-deploy3-iscsi-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device).

2. Versetzen Sie die Volumes/Freigaben auf dem Host in den Offlinezustand. Befolgen Sie die betriebssystemspezifischen Anweisungen auf dem Host, um Volumes bzw. Freigaben in den Offlinezustand zu versetzen. Falls sie nicht bereits offline sind, müssen Sie alle Volumes/Freigaben auf dem Gerät offline schalten, indem Sie wie folgt vorgehen:
   
    1. Wechseln Sie zum Blatt **Geräte**, und wählen Sie Ihr Gerät aus.
   
    2. Wechseln Sie zu **Einstellungen > Verwalten > Freigaben** (oder **Einstellungen > Verwalten > Volumes**). 
   
    3. Wählen Sie ein(e) Freigabe/Volume aus, klicken Sie mit der rechten Maustaste, und wählen Sie **Take offline** (Offline schalten) aus. 
   
    4. Wenn Sie zur Bestätigung aufgefordert werden, versehen Sie **Ich verstehe die Auswirkung des Offlineschaltens dieser Freigabe.** mit einem Häkchen. 
   
    5. Klicken Sie auf **Offline schalten**.

3. Wechseln Sie in Ihrem StorSimple-Geräte-Manager-Dienst zu **Management > Geräte**. Wählen Sie auf dem Blatt **Geräte** das Quellgerät aus, und klicken Sie darauf.

4. Klicken Sie auf dem Blatt **Gerätedashboard** auf **Deaktivieren**.

5. Sie werden auf dem Blatt **Deaktivieren** zur Bestätigung aufgefordert. Die Gerätedeaktivierung ist ein *endgültiger* Vorgang und kann nicht rückgängig gemacht werden. Sie werden außerdem daran erinnert, dass Sie die Freigaben/Volumes auf dem Host offline schalten sollten. Geben Sie den zu bestätigenden Gerätenamen ein, und klicken Sie auf **Deaktivieren**.
   
    ![Screenshot des Blatts „Deaktivieren“. Das Feld „Gerätename“ ist ausgefüllt und die Schaltfläche „Deaktivieren“ wird hervorgehoben.](./media/storsimple-virtual-array-failover-dr/failover1.png)
6. Die Deaktivierung wird gestartet. Nachdem die Deaktivierung erfolgreich abgeschlossen ist, erhalten Sie eine Benachrichtigung.
   
    ![Screenshot einer Statusleiste, an der zu erkennen ist, dass das Gerät deaktiviert wird.](./media/storsimple-virtual-array-failover-dr/failover2.png)
7. Der Gerätezustand auf der Seite „Geräte“ ändert sich jetzt zu **Deaktiviert**.
    ![Screenshot der Seite „Geräte“. Die Eigenschaften des deaktivierten Geräts werden angezeigt, einschließlich des Status, der als „Deaktiviert“ angegeben ist.](./media/storsimple-virtual-array-failover-dr/failover3.png)
8. Wählen Sie auf dem Blatt **Geräte** das deaktivierte Quellgerät für das Failover aus, und klicken Sie darauf. 
9. Klicken Sie auf dem Blatt **Gerätedashboard** auf **Failover**. 
10. Gehen Sie auf dem Blatt **Failover des Geräts** wie folgt vor:
    
    1. Das Feld für das Quellgerät wird automatisch aufgefüllt. Beachten Sie die Gesamtdatengröße für das Quellgerät. Die Datengröße muss kleiner als die verfügbare Kapazität auf dem Zielgerät sein. Überprüfen Sie die Details des Quellgeräts, z.B. Gerätename, Gesamtkapazität und die Namen der Freigaben, die am Failover beteiligt sind.

    2. Wählen Sie in der Dropdownliste mit den verfügbaren Geräten ein **Zielgerät** aus. Nur die Geräte, die über ausreichende Kapazität verfügen, werden in der Dropdownliste angezeigt.

    3. Versehen Sie **Mir ist bekannt, dass durch diesen Vorgang ein Failover von Daten an das Zielgerät erfolgt.** mit einem Häkchen. 

    4. Klicken Sie auf **Failover**.
    
        ![Screenshot des Blatts „Failover des Geräts“, auf dem Quell- und Zielgerät angegeben sind, die Option aktiviert ist und die Schaltfläche „Failover“ hervorgehoben wird.](./media/storsimple-virtual-array-failover-dr/failover4.png)
11. Ein Failoverauftrag wird eingeleitet, und Sie erhalten eine Benachrichtigung. Wechseln Sie zu **Geräte > Aufträge**, um das Failover zu überwachen.
    
     ![Screenshot einer Statusleiste, an der zu erkennen ist, dass ein Failover für das Gerät ausgeführt wird.](./media/storsimple-virtual-array-failover-dr/failover5.png)
12. Auf dem Blatt **Aufträge** sehen Sie einen Failoverauftrag, der für das Quellgerät erstellt wurde. Mit diesem Auftrag werden die Vorüberprüfungen für die Notfallwiederherstellung durchgeführt.
    
    ![Ein Screenshot, auf dem zu sehen ist, dass der Failoverauftrag erfolgreich gestartet wurde.](./media/storsimple-virtual-array-failover-dr/failover6.png)
    
     Nachdem die Vorüberprüfungen für die Notfallwiederherstellung erfolgreich abgeschlossen wurden, erzeugt der Failoverauftrag Wiederherstellungsaufträge für jede Freigabe/jedes Volume, die bzw. das auf dem Quellgerät vorhanden ist.
    
    ![Screenshot, auf dem die Details des Failoverauftrags angezeigt werden, z. B. der Status, das Gerät und die Dauer.](./media/storsimple-virtual-array-failover-dr/failover7.png)
13. Wechseln Sie nach Abschluss des Failover zum Blatt **Geräte**.
    
    1. Wählen Sie das StorSimple-Gerät aus, das Sie als Zielgerät für das Failover verwendet haben, und klicken Sie darauf.
    2. Wechseln Sie zu **Einstellungen > Verwaltung > Freigaben** (oder **Volumes**, wenn es sich um einen iSCSI-Server handelt). Auf dem Blatt **Freigaben** können Sie alle Freigaben (Volumes) des alten Geräts anzeigen.
        ![Screenshot des Blatts „Geräte“. Das Zielgerät wird mit dem Status „Online“ angezeigt.](./media/storsimple-virtual-array-failover-dr/failover9.png)
14. Sie müssen [einen DNS-Alias-Eintrag erstellen](https://support.microsoft.com/kb/168322). Dadurch werden alle Anwendungen, die versuchen, eine Verbindung herzustellen, auf das neue Gerät umgeleitet.

## <a name="errors-during-dr"></a>Fehler bei der Notfallwiederherstellung

**Ausfall der Cloudverbindung während der Notfallwiederherstellung**

Falls die Cloudverbindung nach dem Starten der Notfallwiederherstellung und vor Abschluss der Wiederherstellung des Geräts unterbrochen wird, tritt für die Notfallwiederherstellung ein Fehler auf. Dann erhalten Sie eine Fehlerbenachrichtigung. Das Zielgerät für die Notfallwiederherstellung wird als *Nicht verwendbar* gekennzeichnet. Dieses Zielgerät kann für künftige Notfallwiederherstellungen nicht verwendet werden.

**Keine kompatiblen Zielgeräte**

Wenn die verfügbaren Zielgeräte nicht über genügend Speicherplatz verfügen, wird ein Fehler mit dem Hinweis angezeigt, dass keine kompatiblen Zielgeräte vorhanden sind.

**Fehler bei Vorüberprüfungen**

Falls eine der Vorüberprüfungen nicht erfolgreich ist, werden Vorüberprüfungsfehler angezeigt.

## <a name="business-continuity-disaster-recovery-bcdr"></a>Business Continuity Disaster Recovery (BCDR)

Ein Business Continuity Disaster Recovery (BCDR)-Szenario liegt vor, wenn das gesamte Azure-Rechenzentrum nicht mehr funktioniert. Dies kann sich auf den StorSimple-Geräte-Manager-Dienst und die zugehörigen StorSimple-Geräte auswirken.

Wenn StorSimple-Geräte direkt vor einem Notfall registriert wurden, müssen diese StorSimple-Geräte unter Umständen gelöscht werden. Nach dem Notfall können Sie diese Geräte neu erstellen und konfigurieren.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr darüber, wie Sie das [StorSimple Virtual Array mit der lokalen Webbenutzeroberfläche verwalten](storsimple-ova-web-ui-admin.md).