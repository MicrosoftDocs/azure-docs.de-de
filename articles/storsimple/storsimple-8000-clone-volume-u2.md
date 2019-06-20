---
title: Klonen eines Volumes auf einem Gerät der StorSimple 8000-Serie | Microsoft-Dokumentation
description: Beschreibt die verschiedenen Klontypen und ihre Verwendung und wie Sie mit einem Sicherungssatz ein einzelnes Volume auf einem Gerät der StorSimple 8000-Serie klonen.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 12/05/2017
ms.author: alkohli
ms.openlocfilehash: 84734aefb72a3330d99c5707b461de2cd5e30484
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60637859"
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-clone-a-volume"></a>Verwenden des StorSimple-Geräte-Manager-Diensts im Azure-Portal zum Klonen eines Volumes

## <a name="overview"></a>Übersicht

In diesem Tutorial wird beschrieben, wie Sie einen Sicherungssatz zum Klonen eines einzelnen Volumes über den **Sicherungskatalog** verwenden können. Außerdem wird der Unterschied zwischen *vorübergehenden* und *dauerhaften* Klonen beschrieben. Die Anleitung in diesem Tutorial gilt für alle Geräte der StorSimple 8000-Serie, auf denen Update 3 oder höher ausgeführt wird.

Auf dem Blatt **Sicherungskatalog** des StorSimple-Geräte-Manager-Diensts werden alle Sicherungssätze angezeigt, die mithilfe manueller oder automatisierter Sicherungen erstellt wurden. Sie können dann in einem Sicherungssatz ein Volume auswählen, das geklont werden soll.

 ![Liste der Sicherungssätze](./media/storsimple-8000-clone-volume-u2/bucatalog.png)

## <a name="considerations-for-cloning-a-volume"></a>Überlegungen beim Klonen eines Volumes

Beim Klonen eines Volumes müssen Sie folgende Informationen beachten.

- Ein Klon verhält sich genauso wie ein reguläres Volume. Jeder Vorgang, der auf einem Volume möglich ist, ist auch für den Klon möglich.

- Die Überwachung und die Standardsicherung werden auf geklonten Volumes automatisch deaktiviert. Sie müssen für alle Sicherungen ein geklontes Volume konfigurieren.

- Ein lokales Volume wird als mehrstufiges Volume geklont. Wenn das geklonte Volume lokal fixiert werden muss, können Sie den Klon in ein lokal fixiertes Volume konvertieren, nachdem der Klonvorgang erfolgreich abgeschlossen wurde. Informationen zum Konvertieren eines mehrstufigen Volumes in ein lokal fixiertes Volume finden Sie unter [Ändern des Volumetyps](storsimple-8000-manage-volumes-u2.md#change-the-volume-type).

- Wenn Sie versuchen, ein geklontes Volume von mehrstufig in lokal zu ändern, misslingt die Konvertierung sofort nach dem Klonen (wenn es sich noch um einen vorübergehenden Klon handelt) mit der folgenden Fehlermeldung:

    `Unable to modify the usage type for volume {0}. This can happen if the volume being modified is a transient clone and hasn’t been made permanent. Take a cloud snapshot of this volume and then retry the modify operation.`

    Dieser Fehler wird nur ausgegeben, wenn das Klonen auf einem anderen Gerät erfolgt. Sie können das Volume in lokal fixiert konvertieren, wenn Sie zunächst den vorübergehenden Klon in einen permanenten Klon konvertieren. Erstellen Sie eine Cloudmomentaufnahme des vorübergehenden Klons, um ihn in einen dauerhaften Klon zu konvertieren.

## <a name="create-a-clone-of-a-volume"></a>Erstellen von Klonen von einem Volume

Sie können einen Klon anhand einer lokalen oder Cloudmomentaufnahme auf dem gleichen Gerät, auf einem anderen Gerät oder sogar auf einem Cloudgerät erstellen.

Das folgende Verfahren beschreibt, wie ein Klon über den Sicherungskatalog erstellt wird.  Eine alternative Methode zum Initiieren eines Klons ist wie folgt: Wechseln Sie zu **Volumes**, und wählen Sie ein Volume aus. Klicken Sie mit der rechten Maustaste, um das Kontextmenü aufzurufen, und wählen Sie **Klonen** aus.

Führen Sie die folgenden Schritte aus, um einen Klon des Volumes aus dem Sicherungskatalog zu erstellen.

#### <a name="to-clone-a-volume"></a>So klonen Sie ein Volume

1. Navigieren Sie zum StorSimple-Geräte-Manager-Dienst, und klicken Sie dann auf **Sicherungskatalog**.

2. Wählen Sie wie folgt einen Sicherungssatz aus:
   
   1. Wählen Sie das entsprechende Gerät aus.
   2. Wählen Sie in der Dropdownliste das Volume oder die Sicherungsrichtlinie für die gewünschte Sicherung aus.
   3. Geben Sie den Zeitraum an.
   4. Klicken Sie auf **Anwenden**, um diese Abfrage durchzuführen.

      Die dem ausgewählten Volume oder der Sicherungsrichtlinie zugeordneten Sicherungen sollten in der Liste der Sicherungssätze angezeigt werden.
   
      ![Liste der Sicherungssätze](./media/storsimple-8000-clone-volume-u2/bucatalog.png)
     
3. Erweitern Sie den Sicherungssatz, um das zugehörige Volume anzuzeigen, und wählen Sie ein Volume in einem Sicherungssatz aus. Klicken Sie mit der rechten Maustaste, und wählen Sie im Kontextmenü **Klonen** aus.

   ![Liste der Sicherungssätze](./media/storsimple-8000-clone-volume-u2/clonevol3b.png) 

3. Führen Sie auf dem Blatt **Klonen** die folgenden Schritte aus:
   
   1. Ermitteln Sie ein Zielgerät. Dies ist der Speicherort, an dem der Klon erstellt wird. Sie können das gleiche Gerät auswählen oder ein anderes Gerät angeben.

      > [!NOTE]
      > Stellen Sie sicher, dass für den Klon weniger Kapazität benötigt wird, als auf dem Zielgerät zur Verfügung steht.
       
   2. Geben Sie einen eindeutigen Volumenamen für Ihren Klon an. Der Name muss zwischen 3 und 127 Zeichen lang sein.
      
       > [!NOTE]
       > Das Feld **Volume klonen als** lautet selbst dann **Mehrstufig**, wenn Sie ein lokales Volume klonen. Sie können diese Einstellung nicht ändern. Sie können jedoch das geklonte Volume nach dem erfolgreichen Erstellen des Klons in ein lokales Volume konvertieren. Informationen zum Konvertieren eines mehrstufigen Volumes in ein lokal fixiertes Volume finden Sie unter [Ändern des Volumetyps](storsimple-8000-manage-volumes-u2.md#change-the-volume-type).
          
   3. Geben Sie unter **Verbundene Hosts** einen Access Control-Datensatz (ACR) für den Klon an. Sie können einen neuen ACR hinzufügen oder diesen aus der Liste mit vorhandenen ACRs auswählen. Der ACR bestimmt, welche Hosts auf diesen Klon zugreifen können.
      
       ![Liste der Sicherungssätze](./media/storsimple-8000-clone-volume-u2/clonevol3a.png) 

   4. Klicken Sie auf **Klonen**, um den Vorgang abzuschließen.

4. Es wird ein Klonauftrag initiiert, und Sie werden benachrichtigt, sobald der Klon erstellt wurde. Klicken Sie auf die Auftragsbenachrichtigung, oder wechseln Sie zum Blatt **Aufträge**, um den Klonauftrag zu überwachen.

    ![Liste der Sicherungssätze](./media/storsimple-8000-clone-volume-u2/clonevol5.png)

7. Nachdem der Klonauftrag abgeschlossen wurde, wechseln Sie zu Ihrem Gerät und klicken dann auf **Volumes**. In der Liste der Volumes sollte der soeben erstellte Klon im Volumecontainer mit dem Quellvolume angezeigt werden.

    ![Liste der Sicherungssätze](./media/storsimple-8000-clone-volume-u2/clonevol6.png)

Ein auf diese Weise erstellter Klon ist ein vorübergehender Klon. Weitere Informationen zu Klontypen finden Sie unter [Vergleich von vorübergehenden und dauerhaften Klonen](#transient-vs-permanent-clones).


## <a name="transient-vs-permanent-clones"></a>Vergleich von vorübergehenden und dauerhaften Klonen
Vorübergehende Klone werden nur erstellt, wenn der Klonvorgang auf ein anderes Gerät erfolgt. Sie können ein bestimmtes Volume aus einem Sicherungssatz auf einem anderen vom StorSimple-Geräte-Manager verwalteten Gerät klonen. Der vorübergehende Klon enthält Verweise auf die Daten im ursprünglichen Volume und verwendet diese Daten zum lokalen Lesen und Schreiben auf dem Zielgerät.

Nachdem Sie eine Cloudmomentaufnahme eines vorübergehenden Klons erstellt haben, wird der daraus resultierende Klon ein *dauerhafter* Klon. Während dieses Vorgangs wird eine Kopie der Daten in der Cloud erstellt, und die Zeit zum Kopieren dieser Daten wird durch die Datenmenge und die Azure-Latenzen bestimmt (hierbei handelt es sich um eine Azure-zu-Azure-Kopie). Dieser Vorgang kann einige Tage bis Wochen dauern. Der vorübergehende Klon wird zum dauerhaften Klon und verfügt nicht über Verweise auf die ursprünglichen Volumedaten, aus denen er geklont wurde.

## <a name="scenarios-for-transient-and-permanent-clones"></a>Szenarios für vorübergehende und dauerhafte Klone
In den folgenden Abschnitten werden beispielhafte Situationen beschreiben, in denen vorübergehende und dauerhafte Klone verwendet werden können.

### <a name="item-level-recovery-with-a-transient-clone"></a>Wiederherstellung auf Elementebene mit einem vorübergehenden Klon
Sie müssen eine ein Jahr alte Datei mit einer Microsoft PowerPoint-Präsentation wiederherstellen. Der IT-Administrator identifiziert die entsprechende Sicherung aus diesem Zeitraum und filtert das Volume dann. Anschließend klont der Administrator das Volume, ermittelt die Datei, nach der Sie suchen, und stellt sie Ihnen dann zur Verfügung. In diesem Szenario wird ein vorübergehender Klon verwendet.

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>Testen in der Produktionsumgebung mit einem dauerhaften Klon
Sie müssen einen Testfehler in der Produktionsumgebung überprüfen. Sie erstellen einen Klon des Volumes in der Produktionsumgebung und dann eine Cloudmomentaufnahme dieses Klons, um ein unabhängiges geklontes Volume zu erstellen. In diesem Szenario wird ein dauerhafter Klon verwendet.

## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie, wie Sie [StorSimple-Volumes aus einem Sicherungssatz wiederherstellen](storsimple-8000-restore-from-backup-set-u2.md).
* Erfahren Sie, wie Sie [Ihr StorSimple-Gerät mithilfe des StorSimple-Geräte-Manager-Diensts verwalten](storsimple-8000-manager-service-administration.md).

