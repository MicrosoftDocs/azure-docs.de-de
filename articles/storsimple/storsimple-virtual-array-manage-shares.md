---
title: Verwalten von StorSimple Virtual Array-Freigaben | Microsoft-Dokumentation
description: Beschreibt den StorSimple-Geräte-Manager und erläutert, wie Sie darüber Freigaben auf Ihrem StorSimple Virtual Array verwalten.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: 0a799c83-fde5-4f3f-af0e-67535d1882b6
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: 340b910319cabe3379bdb1bad1c09bc71c17f072
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994943"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-shares-on-the-storsimple-virtual-array"></a>Verwenden des StorSimple-Geräte-Manager-Diensts zum Verwalten von Freigaben auf dem StorSimple Virtual Array

## <a name="overview"></a>Übersicht

In diesem Tutorial wird erläutert, wie Sie den StorSimple-Geräte-Manager-Dienst zum Erstellen und Verwalten von Freigaben auf Ihrem StorSimple Virtual Array verwenden können.

Der StorSimple-Geräte-Manager-Dienst ist eine Erweiterung im Azure-Portal, mit der Sie Ihre StorSimple-Lösung über eine einzelne Weboberfläche verwalten können. Sie können den StorSimple-Geräte-Manager-Dienst nutzen, um Freigaben und Volumes zu verwalten. Außerdem können Sie ihn verwenden, um Geräte anzuzeigen und zu verwalten, Warnungen anzuzeigen sowie um Sicherungsrichtlinien und den Sicherungskatalog zu verwalten.

## <a name="share-types"></a>Freigabetypen

Es gibt folgende StorSimple-Freigaben:

* **Lokal**: Daten in diesen Freigaben verbleiben durchgehend im Array und weichen nicht in die Cloud aus.
* **Mehrstufig**: Daten in diesen Freigaben können in die Cloud ausweichen. Wenn Sie eine mehrstufige Freigabe erstellen, werden ca. 10 % des Speicherplatzes auf der lokalen Ebene und 90 % des Speicherplatzes in der Cloud bereitgestellt. Wenn Sie beispielsweise eine Freigabe mit 1 TB bereitstellen, befinden sich 100 GB lokal, und 900 GB werden in der Cloud zum Anordnen der Daten genutzt. Dies bedeutet wiederum Folgendes: Wenn der gesamte lokale Speicherplatz auf dem Gerät aufgebraucht ist, können Sie keine mehrstufige Freigabe bereitstellen (da die lokal erforderlichen 10 % nicht verfügbar sind).

### <a name="provisioned-capacity"></a>Bereitgestellte Kapazität

In der folgenden Tabelle finden Sie die bereitgestellte Maximalkapazität für die einzelnen Freigabetypen.

| **Begrenzungsbezeichner** | **Begrenzung** |
| --- | --- |
| Mindestgröße einer mehrstufigen Freigabe |500 GB |
| Maximale Größe einer mehrstufigen Freigabe |20 TB |
| Mindestgröße einer lokalen Freigabe |50 GB |
| Maximale Größe einer lokalen Freigabe |2 TB |

## <a name="the-shares-blade"></a>Das Blatt „Freigaben“

Mithilfe des Menüs **Freigaben** auf Ihrem StorSimple-Dienstübersichtsblatt können Sie die Liste der Speicherfreigaben auf einem bestimmten StorSimple-Array anzeigen und sie verwalten.

![Blatt „Freigaben“](./media/storsimple-virtual-array-manage-shares/shares-blade.png)

Eine Freigabe umfasst eine Reihe von Attributen:

* **Freigabename** – Ein beschreibender Name, der eindeutig sein muss und die Freigabe identifiziert
* **Status** – online oder offline. Wenn eine Freigabe offline ist, können Benutzer der Freigabe nicht darauf zugreifen.
* **Typ** – Gibt an, ob die Freigabe **Mehrstufig** (Standard) oder **Lokal** ist
* **Kapazität** – Gibt die Menge der verwendeten Daten im Vergleich mit der Gesamtmenge der Daten an, die auf der Freigabe gespeichert werden können
* **Beschreibung** – eine optionale Einstellung, die die Freigabe beschreibt
* **Berechtigungen** – NTFS-Berechtigungen für die Freigabe, die über Windows Explorer verwaltet werden können
* **Backup** – Im Fall von StorSimple Virtual Array sind alle Freigaben automatisch für die Sicherung aktiviert.

![Details zu den Freigaben](./media/storsimple-virtual-array-manage-shares/share-details.png)

Verwenden Sie die Anweisungen in diesem Tutorial, um die folgenden Aufgaben auszuführen:

* Hinzufügen einer Freigabe
* Ändern einer Freigabe
* Offlineschalten einer Freigabe
* Löschen einer Freigabe

## <a name="add-a-share"></a>Hinzufügen einer Freigabe

1. Klicken Sie auf dem StorSimple-Dienstübersichtsblatt in der Befehlsleiste auf **+ Freigabe hinzufügen**. Daraufhin öffnet sich das Blatt **Freigabe hinzufügen**.

    ![Hinzufügen der Freigabe](./media/storsimple-virtual-array-manage-shares/add-share.png)

2. Gehen Sie auf dem Blatt **Freigabe hinzufügen** wie folgt vor:
   
   1. Geben Sie im Feld **Freigabename** einen eindeutigen Namen für Ihre Freigabe ein. Der Name muss eine Zeichenfolge mit einer Länge von 3 bis 127 Zeichen sein.

   2. Geben Sie eine optionale **Beschreibung** für die Freigabe an. Die Beschreibung ist für die Identifizierung der Freigabenbesitzer hilfreich.

   3. Geben Sie in der Dropdownliste **Typ** an, ob für die zu erstellende Freigabe die Option **Mehrstufig** oder **Lokal** gelten soll. Für Workloads, die lokale Garantien, niedrige Latenzzeiten und höhere Leistung benötigen, wählen Sie **Lokale Freigabe** aus. Wählen Sie für alle anderen Daten **Mehrstufige Freigabe** aus.

   4. Geben Sie im Feld **Kapazität** die Größe der Freigabe an. Eine mehrstufige Freigabe muss zwischen 500 GB und 20 TB groß sein und eine lokale Freigabe zwischen 50 GB und 2 GB.

   5. Weisen Sie die Berechtigungen im Feld **Set default full permissions to**  (Vollzugriff als Standardeinstellung festlegen) dem Benutzer oder der Gruppe zu, der bzw. die auf die Freigabe zugreift. Geben Sie den Namen des Benutzers oder der Benutzergruppe im Format _john@contoso.com_ ein. Es wird empfohlen, eine Benutzergruppe (anstelle eines einzelnen Benutzers) zu verwenden, um für Administratorberechtigungen den Zugriff auf diese Freigaben zuzulassen. Nachdem Sie hier die Berechtigungen zugewiesen haben, können Sie den Datei-Explorer verwenden, um diese Berechtigungen zu ändern.
3. Wenn Sie die Konfiguration Ihrer Freigabe abgeschlossen haben, klicken Sie auf **Erstellen**. Eine Freigabe wird mit den angegebenen Einstellungen erstellt, und Sie erhalten eine Benachrichtigung. Standardmäßig wird für die Freigabe die Sicherungsfunktion aktiviert.
4. Wechseln Sie zum Blatt **Freigaben**, um sicherzustellen, dass die Freigabe erfolgreich erstellt wurde. Die Freigabe sollte hier aufgeführt werden.
   
    ![Erfolgreiches Erstellen der Freigabe](./media/storsimple-virtual-array-manage-shares/share-success.png)

## <a name="modify-a-share"></a>Ändern einer Freigabe

Ändern Sie eine Freigabe, wenn Sie die Beschreibung der Freigabe ändern müssen. Alle anderen Freigabeeigenschaften können nicht geändert werden, sobald die Freigabe erstellt ist.

#### <a name="to-modify-a-share"></a>So ändern Sie eine Freigabe

1. Wählen Sie auf dem StorSimple-Dienstübersichtsblatt aus den Einstellungen **Freigaben** das virtuelle Array aus, auf dem sich die Freigabe befindet, die Sie ändern möchten.
2. **Wählen Sie**  die Freigabe aus, um die aktuelle Beschreibung anzuzeigen, und ändern Sie sie.
3. Speichern Sie die Änderungen, indem Sie in der Befehlsleiste auf **Speichern** klicken. Die angegebenen Einstellungen werden angewendet, und Sie erhalten eine Benachrichtigung.
   
    ![ Bearbeiten der Freigabe](./media/storsimple-virtual-array-manage-shares/share-edit.png)

## <a name="take-a-share-offline"></a>Offlineschalten einer Freigabe

Möglicherweise müssen Sie eine Freigabe offline schalten, wenn Sie diese ändern oder löschen möchten. Wenn eine Freigabe offline ist, ist sie nicht für den Lese-/Schreibzugriff verfügbar. Sie müssen die Freigabe sowohl auf dem Host als auch auf dem Gerät offline schalten.

#### <a name="to-take-a-share-offline"></a>So schalten Sie eine Freigabe offline

1. Stellen Sie vor dem Offlineschalten sicher, dass die betreffende Freigabe nicht verwendet wird.
2. Schalten Sie die Freigabe auf dem Array offline, indem Sie die folgenden Schritte ausführen:
   
    1. Wählen Sie auf dem StorSimple-Dienstübersichtsblatt aus den Einstellungen **Freigaben** das virtuelle Array aus, auf dem sich die Freigabe befindet, die Sie offline schalten möchten.

    2. **Wählen** Sie die Freigabe aus, und klicken Sie auf **...** (Sie können auch mit der rechten Maustaste in diese Zeile klicken). Wählen Sie anschließend im Kontextmenü **Take offline** (Offline schalten) aus.
     
        ![Offlinefreigabe](./media/storsimple-virtual-array-manage-shares/shares-offline.png)

    3. Überprüfen Sie die Informationen auf dem Blatt **Take offline** (Offline schalten), und bestätigen Sie den Vorgang. Klicken Sie auf **Take offline** (Offline schalten), um die Freigabe offline zu schalten. Sie erhalten eine Benachrichtigung, dass der Vorgang in Bearbeitung ist.

    4. Wechseln Sie zum Blatt **Freigaben**, um zu überprüfen, ob die Freigabe erfolgreich offline geschaltet wurde. Der Status der Freigabe sollte als offline angezeigt werden.

## <a name="delete-a-share"></a>Löschen einer Freigabe

> [!IMPORTANT]
> Sie können eine Freigabe nur dann löschen, wenn sie offline ist.


Führen Sie die folgenden Schritte durch, um eine Freigabe zu löschen.

#### <a name="to-delete-a-share"></a>So löschen Sie eine Freigabe

1. Wählen Sie auf dem StorSimple-Dienstübersichtsblatt aus den Einstellungen **Freigaben** das virtuelle Array aus, auf dem sich die Freigabe befindet, die Sie löschen möchten.
2. **Wählen** Sie die Freigabe aus, und klicken Sie auf **...** (Sie können auch mit der rechten Maustaste in dieser Zeile klicken). Wählen Sie anschließend im Kontextmenü **Löschen** aus.
   
    ![Freigabe löschen](./media/storsimple-virtual-array-manage-shares/share-delete.png)
3. Überprüfen Sie den Status der zu löschenden Freigabe. Wenn die Freigabe, die Sie löschen möchten, nicht als offline erscheint, schalten Sie sie zunächst offline. Führen Sie die unter [Offlineschalten einer Freigabe](#take-a-share-offline) genannten Schritte aus.
4. Wenn Sie auf dem Blatt **Löschen** zur Bestätigung aufgefordert werden, akzeptieren Sie die Bestätigung, und klicken Sie auf **Löschen**. Jetzt wird die Freigabe gelöscht, und das Blatt **Freigaben** zeigt die aktualisierte Liste der Freigaben innerhalb des virtuellen Arrays an.

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie, wie Sie [eine StorSimple-Freigabe klonen (in englischer Sprache)](storsimple-virtual-array-clone.md).

