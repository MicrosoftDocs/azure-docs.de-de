---
title: Verwalten von Volumes auf StorSimple Virtual Array | Microsoft-Dokumentation
description: Beschreibt den StorSimple-Geräte-Manager und erläutert, wie Sie darüber Volumes auf Ihrem StorSimple Virtual Array verwalten.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: caa6a26b-b7ba-4a05-b092-1a79450225cf
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: 79e3ce8c1605e5d68ff44901f53854d2f5f10abc
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014379"
---
# <a name="use-storsimple-device-manager-service-to-manage-volumes-on-the-storsimple-virtual-array"></a>Verwenden des StorSimple-Geräte-Manager-Diensts zum Verwalten von Volumes auf dem StorSimple Virtual Array

## <a name="overview"></a>Übersicht

In diesem Tutorial wird erläutert, wie Sie den StorSimple-Geräte-Manager-Dienst zum Erstellen und Verwalten von Volumes auf Ihrem StorSimple Virtual Array verwenden können.

Der StorSimple-Geräte-Manager-Dienst ist eine Erweiterung im Azure-Portal, mit der Sie Ihre StorSimple-Lösung über eine einzelne Weboberfläche verwalten können. Sie können den StorSimple-Geräte-Manager-Dienst nutzen, um Freigaben und Volumes zu verwalten. Außerdem können Sie ihn nutzen, um Geräte anzuzeigen und zu verwalten, Warnungen anzuzeigen sowie um Sicherungsrichtlinien und den Sicherungskatalog anzuzeigen und zu verwalten.

## <a name="volume-types"></a>Volumetypen

Folgende StorSimple-Volumes sind möglich:

* **Lokal:** Daten auf diesen Volumes verbleiben durchgehend im Array und gelangen nicht in die Cloud.
* **Mehrstufig:** Die Daten in diesen Volumes können in die Cloud ausweichen. Wenn Sie ein mehrstufiges Volume erstellen, werden ca. 10 % des Speicherplatzes auf der lokalen Ebene und 90 % des Speicherplatzes in der Cloud bereitgestellt. Wenn Sie beispielsweise ein Volume mit 1 TB bereitstellen, befinden sich 100 GB lokal, und 900 GB werden in der Cloud zum Anordnen der Daten genutzt. Dies bedeutet wiederum Folgendes: Wenn der gesamte lokale Speicherplatz auf dem Gerät aufgebraucht ist, können Sie kein mehrstufiges Volume bereitstellen (da die lokal erforderlichen 10 % nicht verfügbar sind).

### <a name="provisioned-capacity"></a>Bereitgestellte Kapazität
In der folgenden Tabelle finden Sie die bereitgestellte Maximalkapazität für die einzelnen Volumetypen.

| **Begrenzungsbezeichner**                                       | **Begrenzung**     |
|------------------------------------------------------------|---------------|
| Mindestgröße eines mehrstufigen Volumes                            | 500 GB        |
| Maximale Größe eines mehrstufigen Volumes                            | 5 TB          |
| Mindestgröße eines lokalen Volumes                    | 50 GB         |
| Maximale Größe eines lokalen Volumes                    | 200 GB        |

## <a name="the-volumes-blade"></a>Das Blatt „Volumes“
Mithilfe des Menüs **Volumes** auf Ihrem StorSimple-Dienstübersichtsblatt können Sie die Liste der Speichervolumes auf einem bestimmten StorSimple-Array anzeigen und sie verwalten.

![Blatt „Volumes“](./media/storsimple-virtual-array-manage-volumes/volumes-blade.png)

Ein Volume umfasst eine Reihe von Attributen:

* **Volumename** : Ein beschreibender Name, der eindeutig sein muss und das Volume identifiziert.
* **Status** – online oder offline. Offline-Volumes sind für die Initiatoren (Server), die über Zugriff für die Verwendung des Volumes verfügen, nicht sichtbar.
* **Typ**: Gibt an, ob das Volume **mehrstufig** (Standard) oder **lokal** ist.
* **Kapazität** – Gibt die Menge der verwendeten Daten an im Vergleich mit der Gesamtmenge der Daten, die vom Initiator (Server) gespeichert werden können
* **Backup** – Im Fall des StorSimple Virtual Array sind alle Volumes automatisch für das Backup aktiviert.
* **Verbundene Hosts** – Gibt die Initiatoren (Server) an, die Zugriff auf dieses Volume haben

![Details der Volumes](./media/storsimple-virtual-array-manage-volumes/volume-details.png)

Verwenden Sie die Anweisungen in diesem Tutorial, um die folgenden Aufgaben auszuführen:

* Hinzufügen eines Volumes
* Ändern von Volumes
* Offlineschalten von Volumes
* Löschen von Volumes

## <a name="add-a-volume"></a>Hinzufügen eines Volumes

1. Klicken Sie auf dem StorSimple-Dienstübersichtsblatt in der Befehlsleiste auf **+ Volume hinzufügen**. Daraufhin öffnet sich das Blatt **Volume hinzufügen**.
   
    ![Screenshot: Schaltfläche und Bereich „Volume hinzufügen“](./media/storsimple-virtual-array-manage-volumes/add-volume.png)
2. Gehen Sie auf dem Blatt **Volume hinzufügen** wie folgt vor:
   
   * Geben Sie im Feld **Volumename** einen eindeutigen Namen für Ihr Volume ein. Der Name muss eine Zeichenfolge mit einer Länge von 3 bis 127 Zeichen sein.
   * Geben Sie in der Dropdownliste **Typ** an, ob das von Ihnen erstellte Volume **Mehrstufig** oder **Lokal** sein soll. Für Workloads, die lokale Garantien, niedrige Latenzzeiten und höhere Leistung benötigen, wählen Sie **Lokales Volume** aus. Wählen Sie für alle anderen Daten **Mehrstufiges Volume** aus.
   * Geben Sie im Feld **Kapazität** die Größe des Volumes an. Ein mehrstufiges Volume muss zwischen 500 GB und 5 TB groß sein und ein lokales Volume zwischen 50 GB und 500 GB.
   * * Klicken Sie auf **Verbundene Hosts**, wählen Sie einen Access Control-Datensatz (ACR) aus, der dem iSCSI-Initiator entspricht, den Sie mit diesem Volume verbinden möchten, und klicken Sie anschließend auf **Auswählen**.
3. Klicken Sie zum Hinzufügen eines neuen verbundenen Hosts auf **Neu hinzufügen**, geben Sie einen Namen für den Host und seinen IQN-Namen (qualifizierter iSCSI-Name) ein, und klicken Sie anschließend auf **Hinzufügen**.
   
    ![Screenshot: Bereich „Verbundene Hosts“ mit der Option „Neu hinzufügen“](./media/storsimple-virtual-array-manage-volumes/volume-add-acr.png)
4. Wenn Sie die Konfiguration Ihres Volume abgeschlossen haben, klicken Sie auf **Erstellen**. Ein Volume wird mit den angegebenen Einstellungen erstellt, und Sie erhalten eine Benachrichtigung über die erfolgreiche Erstellung. Standardmäßig wird für das Volume die Sicherungsfunktion aktiviert.
5. Wechseln Sie zum Blatt **Volumes**, um sicherzustellen, dass das Volume erfolgreich erstellt wurde. Das Volume sollte hier aufgeführt werden.
   
    ![Erfolgreiches Erstellen des Volume](./media/storsimple-virtual-array-manage-volumes/volume-success.png)

## <a name="modify-a-volume"></a>Ändern von Volumes

Zum Ändern eines Volumes müssen Sie die Hosts ändern, die auf das Volume zugreifen. Die anderen Attribute eines Volumes werden nicht geändert, nachdem das Volume erstellt wurde.

#### <a name="to-modify-a-volume"></a>So ändern Sie ein Volume

1. Wählen Sie auf dem StorSimple-Dienstübersichtsblatt aus den Einstellungen **Volumes** das virtuelle Array aus, auf dem sich das Volume befindet, das Sie ändern möchten.
2. **Wählen Sie** das Volume aus, und klicken Sie auf **Verbundene Hosts**, um den derzeit verbundenen Server anzuzeigen und auf einen anderen Server zu ändern.
   
    ![Bearbeiten eines Volume](./media/storsimple-virtual-array-manage-volumes/volume-edit-acr.png)
3. Speichern Sie die Änderungen, indem Sie in der Befehlsleiste auf **Speichern** klicken. Die angegebenen Einstellungen werden angewendet, und Sie erhalten eine Benachrichtigung.

## <a name="take-a-volume-offline"></a>Offlineschalten von Volumes

Möglicherweise müssen Sie ein Volume offline schalten, wenn Sie dieses ändern oder löschen möchten. Wenn ein Volume offline ist, ist es nicht für den Lese-/ Schreibzugriff verfügbar. Sie müssen das Volume sowohl auf dem Host als auch auf dem Gerät offline schalten.

#### <a name="to-take-a-volume-offline"></a>So schalten Sie ein Volume offline

1. Stellen Sie vor dem Offlineschalten sicher, dass das betreffende Volume nicht verwendet wird.
2. Schalten Sie das Volume zunächst auf dem Host offline. Dadurch werden potenzielle Schäden an den Daten auf dem Volume vermieden. Genaue Anweisungen finden Sie in den Anweisungen für das Hostbetriebssystem.
3. Wenn das Volume auf dem Host offline ist, schalten Sie das Volume anhand der folgenden Schritte auf dem Array offline:
   
   * Wählen Sie auf dem StorSimple-Dienstübersichtsblatt aus den Einstellungen **Volumes** das virtuelle Array aus, auf dem sich das Volume befindet, das Sie offline schalten möchten.
   * **Wählen** Sie das Volume aus, und klicken Sie auf **...** (Sie können auch mit der rechten Maustaste in dieser Zeile klicken). Wählen Sie anschließend im Kontextmenü **Take offline** (Offline schalten) aus.
     
        ![Offline-Volume](./media/storsimple-virtual-array-manage-volumes/volume-offline.png)
   * Überprüfen Sie die Informationen auf dem Blatt **Take offline** (Offline schalten), und bestätigen Sie den Vorgang. Klicken Sie auf **Take offline** (Offline schalten), um das Volume offline zu schalten. Sie erhalten eine Benachrichtigung, dass der Vorgang in Bearbeitung ist.
   * Wechseln Sie zum Blatt **Volumes**, um sicherzustellen, dass das Volume erfolgreich offline geschaltet wurde. Der Status des Volume sollte als offline angezeigt werden.
     
       ![Bestätigung über Offline-Volume](./media/storsimple-virtual-array-manage-volumes/volume-offline-confirm.png)

## <a name="delete-a-volume"></a>Löschen von Volumes

> [!IMPORTANT]
> Sie können ein Volume nur löschen, wenn dieses offline ist.
> 
> 

Führen Sie die folgenden Schritte durch, um ein Volume zu löschen.

#### <a name="to-delete-a-volume"></a>So löschen Sie ein Volume

1. Wählen Sie auf dem StorSimple-Dienstübersichtsblatt aus den Einstellungen **Volumes** das virtuelle Array aus, auf dem sich das Volume befindet, das Sie löschen möchten.
2. **Wählen** Sie das Volume aus, und klicken Sie auf **...** (Sie können auch mit der rechten Maustaste in dieser Zeile klicken). Wählen Sie anschließend im Kontextmenü **Löschen** aus.
   
    ![Löschen eines Volume](./media/storsimple-virtual-array-manage-volumes/volume-delete.png)
3. Überprüfen Sie den Status des zu löschenden Volumes. Wenn das zu löschende Volume nicht offline ist,schalten Sie es zunächst mithilfe der Schritte unter [Offlineschalten von Volumes](#take-a-volume-offline)offline.
4. Wenn Sie auf dem Blatt **Löschen** zur Bestätigung aufgefordert werden, akzeptieren Sie die Bestätigung, und klicken Sie auf **Löschen**. Das Volume wird nun gelöscht. Auf dem Blatt **Volumes** wird die aktualisierte Liste der Volumes innerhalb des virtuellen Arrays angezeigt.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie ein [StorSimple-Volume klonen](storsimple-virtual-array-clone.md).

