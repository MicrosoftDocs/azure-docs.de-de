---
title: Verwalten von Bandbreitenvorlagen für Geräte der StorSimple 8000-Serie | Microsoft-Dokumentation
description: Beschreibt die Verwaltung von StorSimple-Bandbreitenvorlagen, mit denen Sie die Auslastung der Netzwerkbandbreite steuern.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 56170ffbbfe14248bcfd1f94549a3565873f8646
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005893"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-bandwidth-templates"></a>Verwalten von StorSimple-Bandbreitenvorlagen mithilfe des StorSimple-Geräte-Manager-Diensts

## <a name="overview"></a>Übersicht

Bandbreitenvorlagen ermöglichen es Ihnen, die Netzwerkbandbreitennutzung für mehrere Zeitpläne für bestimmte Tageszeiten einzurichten, um den Datenverkehr vom StorSimple-Gerät zur Cloud zu konfigurieren.

Mit Zeitplänen zur Bandbreiteneinschränkung können Sie folgende Aktionen ausführen:

* Geben Sie benutzerdefinierte Bandbreitenzeitpläne je nach Workloadnetzwerknutzung an.
* Zentralisieren Sie die Verwaltung und verwenden Sie die Zeitpläne wiederholt einfach und nahtlos auf mehreren Geräten.

> [!NOTE]
> Dieses Feature steht nur für physische StorSimple-Geräte (Modelle 8100 und 8600) und nicht für StorSimple Cloud Appliances (Modelle 8010 und 8020) zur Verfügung.


## <a name="the-bandwidth-templates-blade"></a>Das Blatt „Bandbreitenvorlagen“

Auf dem Blatt **Bandbreitenvorlagen** finden Sie alle Bandbreitenvorlagen für den Dienst in einer Tabelle mit den folgenden Informationen:

* **Name:** der eindeutige Name für das Konto, der bei dessen Erstellung zugewiesen wurde.
* **Zeitplan:** die Anzahl der Zeitpläne in einer angegebenen Bandbreitenvorlage.
* **Used by:** die Anzahl der Datenträger, die das Speicherkonto verwenden.

Zusätzliche Informationen zur Konfiguration von Bandbreitenvorlagen finden Sie in den folgenden Ressourcen:

* [Fragen und Antworten zu Bandbreitenvorlagen](#questions-and-answers-about-bandwidth-templates)
* [Bewährte Methoden für Bandbreitenvorlagen](#best-practices-for-bandwidth-templates)

## <a name="add-a-bandwidth-template"></a>Hinzufügen einer Bandbreitenvorlage

Führen Sie die folgenden Schritte aus, um eine Bandbreitenvorlage zu erstellen.

#### <a name="to-add-a-bandwidth-template"></a>So fügen Sie eine Bandbreitenvorlage hinzu

1. Navigieren Sie zu Ihrem StorSimple-Geräte-Manager-Dienst, klicken Sie auf **Bandbreitenvorlagen** und dann auf **+ Bandbreitenvorlage hinzufügen**.

    ![Klicken Sie auf „+ Bandbreitenvorlage hinzufügen/“](./media/storsimple-8000-manage-bandwidth-templates/addbwtemp1.png)

2. Führen Sie auf dem Blatt **Bandbreitenvorlage hinzufügen** die folgenden Schritte aus:
   
    1. Geben Sie einen eindeutigen Namen für Ihre Bandbreitenvorlage an.
    2. Definieren Sie einen Bandbreitenzeitplan. So erstellen Sie einen Zeitplan
   
        1. Wählen Sie in der Dropdownliste die **Tage** aus, für die der Zeitplan konfiguriert werden soll. Sie können mehrere Tage auswählen.        
        
        2. Geben Sie eine **Startzeit** im Format _HH:MM_ ein. Dies liegt den Beginn des Zeitplans fest.

        3. Geben Sie eine **Endzeit** im Format _HH:MM_ ein. Dies liegt das Ende des Zeitplans fest.
      
           > [!NOTE]
           > Sich überschneidende Zeitpläne sind nicht zulässig. Falls durch die Start- und Endzeiten eine Überschneidung entsteht, erhalten Sie eine entsprechende Fehlermeldung.

        4. Geben Sie die **Bandbreitenrate** an. Dies ist die Bandbreite in Megabit pro Sekunde (MBit/s), die Ihr StorSimple-Gerät bei Cloudvorgängen (Uploads und Downloads) verwendet. Geben Sie eine Zahl zwischen 1 und 1000 in dieses Feld ein.

            ![Definieren eines Bandbreitenzeitplans](./media/storsimple-8000-manage-bandwidth-templates/addbwtemp2.png)
         
            Wiederholen Sie die vorherigen Schritte, um die gewünschte Anzahl von Zeitplänen für die Vorlage zu definieren.

        5. Klicken Sie auf **Hinzufügen**, um mit der Erstellung einer Bandbreitenvorlage zu beginnen. Die erstellte Vorlage wird der Liste der Bandbreitenvorlagen hinzugefügt.
      

## <a name="edit-a-bandwidth-template"></a>Bearbeiten einer Bandbreitenvorlage

Führen Sie die folgenden Schritte aus, um eine Bandbreitenvorlage zu bearbeiten.

### <a name="to-edit-a-bandwidth-template"></a>So bearbeiten Sie eine Bandbreitenvorlage

1. Wechseln Sie zu Ihrem StorSimple-Geräte-Manager-Dienst, und klicken Sie auf **Bandbreitenvorlagen**.
2. Wählen Sie in der Liste der Bandbreitenvorlagen die Vorlage aus, die Sie löschen möchten. Klicken Sie mit der rechten Maustaste, und wählen Sie im Kontextmenü die Option **Löschen** aus.
3. Wenn Sie zur Bestätigung aufgefordert werden, klicken Sie auf **OK**. Dadurch sollte die Bandbreitenvorlage gelöscht werden. 
4. Die Liste der Bandbreitevorlagen wird entsprechend aktualisiert.

> [!NOTE]
> Sie können die Änderungen nicht speichern, wenn sich der bearbeitete Zeitplan mit einem vorhandenen Zeitplan in der Bandbreitenvorlage überschneidet, die Sie ändern.

## <a name="delete-a-bandwidth-template"></a>Löschen einer Bandbreitenvorlage

Führen Sie die folgenden Schritte aus, um eine Bandbreitenvorlage zu löschen.

#### <a name="to-delete-a-bandwidth-template"></a>So löschen Sie eine Bandbreitenvorlage

1. Wechseln Sie zu Ihrem StorSimple-Geräte-Manager-Dienst, und klicken Sie auf **Bandbreitenvorlagen**.
2. Wählen Sie in der Liste der Bandbreitenvorlagen die Vorlage aus, die Sie löschen möchten. Klicken Sie mit der rechten Maustaste, und wählen Sie im Kontextmenü die Option „Löschen“ aus.
3. Wenn Sie zur Bestätigung aufgefordert werden, klicken Sie auf **OK**. Dadurch sollte die Bandbreitenvorlage gelöscht werden.
4. Die Liste der Bandbreitevorlagen wird entsprechend aktualisiert.

Wenn die Vorlage von einem Volume verwendet wird, kann sie nicht gelöscht werden. Sie erhalten eine Fehlermeldung, dass die Vorlage verwendet wird. Ein Dialogfeld mit einer Fehlermeldung wird angezeigt, in der Sie darauf hingewiesen werden, alle Verweise auf die Vorlage zu entfernen.

Sie können alle Verweise auf die Vorlage löschen, indem Sie auf die Seite **Volumecontainer** zugreifen und die Volumecontainer bearbeiten, die diese Vorlage verwenden, sodass diese eine andere Vorlage oder eine benutzerdefinierte Einstellung bzw. unbegrenzte Bandbreite verwenden. Wenn alle Verweise entfernt wurden, können Sie die Vorlage löschen.

## <a name="use-a-default-bandwidth-template"></a>Verwenden einer Standardbandbreitenvorlage

Eine Standardbandbreitenvorlage wird bereitgestellt, die standardmäßig von Volumecontainern verwendet wird, um Bandbreitensteuerung für den Cloudzugriff zu erzwingen. Die Standardvorlage dient auch als Referenz für Benutzer, die ihre eigenen Vorlagen erstellen. Im Folgenden sind Details dieser Standardvorlage aufgeführt:

* **Name:** unbegrenzte Nächte und Wochenenden
* **Zeitplan:** ein einzelner Zeitplan von Montag bis Freitag, der eine Bandbreite von 1 MBit/s zwischen 8:00 Uhr und 17:00 Uhr Gerätezeit festlegt. Die Bandbreite wird für den Rest der Woche auf "Unbegrenzt" festgelegt.

Die Standardvorlage kann bearbeitet werden. Die Verwendung dieser Vorlage (einschließlich bearbeiteter Versionen) wird nachverfolgt.

## <a name="create-an-all-day-bandwidth-template-that-starts-at-a-specified-time"></a>Erstellen einer Bandbreitenvorlage für einen gesamten Tag, die zu einem bestimmten Zeitpunkt startet

Gehen Sie folgendermaßen vor, um einen Zeitplan zu erstellen, der zu einem angegebenen Zeitpunkt gestartet und über den gesamten Tag ausgeführt wird. Im Beispiel wird der Zeitplan um 9:00 Uhr gestartet und bis um 9:00 Uhr am nächsten Morgen ausgeführt. Beachten Sie, dass die Start- und Endzeiten für einen bestimmten Zeitplan beide im gleichen 24-Stunden-Zeitplan enthalten sein müssen und nicht über mehrere Tage ausgedehnt werden können. Wenn Sie Bandbreitenvorlagen benötigen, die mehrere Tage umfassen, müssen Sie mehrere Zeitpläne verwenden (wie im Beispiel gezeigt).

#### <a name="to-create-an-all-day-bandwidth-template"></a>So erstellen Sie eine ganztägige Bandbreitenvorlage

1. Erstellen Sie einen Zeitplan, der um 9:00 Uhr beginnt und bis Mitternacht ausgeführt wird.
2. Fügen Sie einen weiteren Zeitplan hinzu. Konfigurieren Sie den zweiten Zeitplan von Mitternacht bis 9:00 Uhr.
3. Speichern Sie die Bandbreitenvorlage

Der zusammengesetzte Zeitplan wird zu einem Zeitpunkt Ihrer Wahl gestartet und ganztägig ausgeführt.

## <a name="questions-and-answers-about-bandwidth-templates"></a>Fragen und Antworten zu Bandbreitenvorlagen

**F**. Was geschieht mit der Bandbreitensteuerung zwischen den Zeitplänen? (Ein Zeitplan wurde beendet, und es hat noch kein anderer begonnen.)

**A**. In solchen Fällen wird keine Bandbreitensteuerung eingesetzt. Dies bedeutet, dass das Gerät uneingeschränkte Bandbreite für den Datenverkehr zur Cloud nutzen kann.

**F**. Können Bandbreitenvorlagen auf einem Gerät geändert werden, das offline ist?

**A**. Sie können keine Bandbreitenvorlagen für Volumecontainer ändern, wenn das entsprechende Gerät offline ist.

**F**. Lässt sich eine Bandbreitenvorlage bearbeiten, die einem Volumecontainer zugeordnet ist, wenn sich die damit verknüpften Volumes offline befinden?

**A**. Sie können eine Bandbreitenvorlage bearbeiten, die einem Volumecontainer zugeordnet ist, dessen verknüpfte Volumes offline sind. Beachten Sie, dass bei Volumes, die offline sind, keine Daten vom Gerät in die Cloud gesendet werden können.

**F**. Kann eine Standardvorlage gelöscht werden?

**A**. Obwohl Sie eine Standardvorlage löschen können, ist dies nicht empfehlenswert. Die Verwendung einer Standardvorlage (einschließlich bearbeiteter Versionen) wird nachverfolgt. Die Nachverfolgungsdaten werden analysiert und im Laufe der Zeit verwendet, um die Standardvorlage zu verbessern.

**F**. Wie stelle ich fest, dass eine Bandbreitenvorlagen geändert werden muss?

**A**. Eines der Anzeichen, dass eine Bandbreitenvorlage geändert werden muss, ist eine merkliche Verlangsamung des Netzwerks mehrmals am Tag. In diesem Fall sollten Sie die Speicher- und Netzwerknutzung anhand der E/A-Leistung und die Netzwerkdurchsatz-Diagramme überwachen.

Anhand des Netzwerkdaten-Durchsatzes können Sie die Tageszeit und die Volumecontainer ermitteln, in denen der Netzwerkengpass auftritt. Wenn dies bei der Übertragung von Daten in die Cloud auftritt (diese Informationen erhalten Sie über die E/A-Leistung für alle Volumecontainer vom Gerät zur Cloud), müssen Sie die den Volumecontainern zugeordneten Bandbreitenvorlagen ändern.

Nachdem die geänderten Vorlagen verwendet werden, müssen Sie das Netzwerk erneut auf bemerkbare Latenzen überwachen. Wenn Sie immer noch vorhanden sind, müssen Sie Bandbreitenvorlagen erneut ändern.

**F**. Was geschieht, wenn sich die Zeitpläne mehrerer Volumecontainer auf meinem Gerät überschneiden, jedoch verschiedene Einschränkungen für diese gelten?

**A**. Angenommen, Sie verfügen über ein Gerät mit 3 Volumecontainern. Die zugeordneten Zeitpläne dieser Container überlappen sich vollständig. Für diese Container gelten jeweils Bandbreiteneinschränkungen von 5, 10 und 15 MBit/s. Wenn E/A-Vorgänge gleichzeitig in allen Containern stattfinden, gilt die niedrigste der drei Bandbreiteneinschränkungen: in diesem Fall 5 MBit/s, da die ausgehenden E/A-Anforderungen dieselbe Warteschlange nutzen.

## <a name="best-practices-for-bandwidth-templates"></a>Bewährte Methoden für Bandbreitenvorlagen

Folgen Sie diesen Empfehlungen für Ihr StorSimple-Gerät:

* Konfigurieren Sie die Bandbreitenvorlagen auf dem Gerät für variable Drosselung des Netzwerkdurchsatzes zu unterschiedlichen Tageszeiten. Diese Bandbreitenvorlagen können in Verbindung mit Sicherungszeitplänen effizient zusätzliche Netzwerkbandbreite außerhalb der Spitzenzeiten für Cloudvorgänge nutzen.
* Berechnen Sie die tatsächliche Bandbreite für eine bestimmte Bereitstellung anhand der Größe der Bereitstellung und der erforderlichen Recovery Time Objective (RTO).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr [Verwalten von StorSimple-Geräten mithilfe des StorSimple-Geräte-Manager-Diensts](storsimple-8000-manager-service-administration.md).

