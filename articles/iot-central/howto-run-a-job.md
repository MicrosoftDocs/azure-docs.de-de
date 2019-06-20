---
title: Erstellen und Ausführen von Aufträgen in Ihrer Azure IoT Central-Anwendung | Microsoft-Dokumentation
description: Im Rahmen von Azure IoT Central-Aufträgen können Funktionen zur Verwaltung von mehreren Geräten gleichzeitig ausgeführt werden, wie das Aktualisieren einer Geräteeigenschaft oder -einstellung oder das Ausführen eines Befehls.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 03/18/2019
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: ec7033719316bb186408ea78f6dabac43c383491
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60519358"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Erstellen und Ausführen von Aufträgen in Ihrer Azure IoT Central-Anwendung

Sie können Microsoft Azure IoT Central verwenden, um Ihre angeschlossenen Geräte bedarfsgerecht mithilfe von Aufträgen zu überwachen. Mit Aufträgen können Sie Massenaktualisierungen an Geräteeigenschaften, -einstellungen und -befehlen vornehmen. In diesem Artikel wird der Einstieg in die Verwendung von Aufträgen in der eigenen Anwendung beschrieben.

## <a name="create-and-run-a-job"></a>Erstellen und Ausführen eines Auftrags

In diesem Abschnitt erfahren Sie, wie Sie einen Auftrag erstellen und ausführen. Außerdem erfahren Sie, wie Sie die Lüfterdrehzahl für mehrere gekühlte Verkaufsautomaten erhöhen.

1. Navigieren Sie im Navigationsbereich zu „Aufträge“.

1. Klicken Sie auf **+ Neu**, um einen neuen Auftrag zu erstellen.

    ![Neuen Auftrag erstellen](./media/howto-run-a-job/createnewjob.png)

1. Geben Sie einen Namen und eine Beschreibung ein, mit denen Sie Ihren Auftrag wiedererkennen können.

1. Wählen Sie die Gerätegruppe aus, auf die Ihr Auftrag angewendet werden soll. Nachdem Sie die Gerätegruppe ausgewählt haben, werden auf der rechten Seite die in der Gerätegruppe enthaltenen Geräte aufgeführt. Wenn Sie eine fehlerhafte Gerätegruppe auswählen, werden keine Geräte, stattdessen jedoch eine Meldung angezeigt, dass Ihre Gerätegruppe fehlerhaft ist.

1. Wählen Sie anschließend den zu definierenden Auftragstyp (Einstellung, Eigenschaft oder Befehl) aus. Klicken Sie neben dem ausgewählten Auftragstyp auf **+** , und fügen Sie die Vorgänge hinzu.

    ![Auftrag konfigurieren](./media/howto-run-a-job/configurejob.png)

1. Wählen Sie auf der rechten Seite die Geräte aus, auf denen der Auftrag ausgeführt werden soll. Wenn Sie auf das obere Kontrollkästchen klicken, werden alle Geräte in der gesamten Gerätegruppe ausgewählt. Wenn Sie auf das Kontrollkästchen neben **Name** klicken, werden alle Geräte auf der aktuellen Seite ausgewählt.

1. Nachdem Sie die Geräte ausgewählt haben, klicken Sie auf **Ausführen** oder **Speichern**. Der Auftrag wird nun auf der Hauptseite **Aufträge** angezeigt. In dieser Ansicht können Sie Ihre derzeit ausgeführten Aufträge sowie den Verlauf aller bisher ausgeführten Aufträge sehen. Der aktuell ausgeführte Auftrag wird immer oben in der Liste angezeigt. Sie können den gespeicherten Auftrag jederzeit erneut öffnen, um ihn zu bearbeiten oder auszuführen.

    ![Auftrag anzeigen](./media/howto-run-a-job/viewjob.png)

    > [!NOTE]
    > Der Verlauf von bisher ausgeführten Aufträgen wird bis zu 30 Tage lang angezeigt.

1. Eine Übersicht zu Ihrem Auftrag erhalten Sie, indem Sie den anzuzeigenden Auftrag aus der Liste auswählen. Diese Übersicht enthält die Auftragsdetails, Geräte und Gerätestatuswerte. In dieser Übersicht können Sie auch auf **Auftragsdetails herunterladen** klicken, um eine CSV-Datei mit den Auftragsdetails, einschließlich der Geräte und ihrer Statuswerte, herunterzuladen. Diese Informationen können bei der Problembehandlung hilfreich sein.

    ![Anzeigen des Gerätestatus](./media/howto-run-a-job/downloaddetails.png)

### <a name="stop-a-running-job"></a>Beenden eines ausgeführten Auftrags

Sie können einen Auftrag anhalten, indem Sie ihn auswählen und im angezeigten Bereich auf **Anhalten** klicken. Der Auftragsstatus wird geändert, um anzugeben, dass der Auftrag angehalten wurde.

   ![Auftrag beenden](./media/howto-run-a-job/stopjob.png)

### <a name="run-a-stopped-job"></a>Ausführen eines angehaltenen Auftrags

Zum Ausführen eines angehaltenen Auftrags wählen Sie diesen aus, und klicken Sie im angezeigten Bereich auf **Ausführen**. Der Auftragsstatus wird geändert, um anzugeben, dass der Auftrag wieder ausgeführt wird.

   ![Fortgesetzter Auftrag](./media/howto-run-a-job/resumejob.png)

## <a name="copy-a-job"></a>Kopieren eines Auftrags

Wählen Sie einen Auftrag auf der Hauptseite für Aufträge aus, und klicken Sie auf **Kopieren**, um einen vorhandenen Auftrag zu kopieren. Daraufhin wird eine neue Kopie der Auftragskonfiguration zur Bearbeitung geöffnet. Sie können den neuen Auftrag speichern oder ausführen. Wenn Sie Änderungen an der ausgewählten Gerätegruppe vorgenommen haben, werden diese zur Bearbeitung im kopierten Auftrag angezeigt.

   ![Auftrag kopieren](./media/howto-run-a-job/copyjob.png)

## <a name="view-the-job-status"></a>Anzeigen des Auftragsstatus

Nachdem ein Auftrag erstellt wurde, wird die Spalte **Status** mit der neuesten Statusmeldung des Auftrags aktualisiert. In der folgenden Tabelle werden die möglichen Statuswerte aufgeführt:

| Statusmeldung       | Bedeutung der Statusmeldung                                          |
| -------------------- | ------------------------------------------------------- |
| Abgeschlossen            | Dieser Auftrag wurde auf allen Geräten ausgeführt.              |
| Fehler               | Bei diesem Auftrag ist ein Fehler aufgetreten. Er wurde auf den Geräten nicht vollständig ausgeführt.  |
| Ausstehend              | Mit der Ausführung dieses Auftrags wurde auf den Geräten noch nicht begonnen.         |
| Wird ausgeführt              | Dieser Auftrag wird derzeit auf Geräten ausgeführt.             |
| Beendet              | Dieser Auftrag wurde von einem Benutzer manuell beendet.           |

Der Statusmeldung folgt eine Übersicht über die Geräte im Auftrag. In der folgenden Tabelle werden die möglichen Gerätestatuswerte aufgeführt:

| Statusmeldung       | Bedeutung der Statusmeldung                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Succeeded            | Die Anzahl der Geräte, auf denen der Auftrag erfolgreich ausgeführt wurde.       |
| Fehler               | Die Anzahl der Geräte, auf denen der Auftrag nicht erfolgreich ausgeführt wurde.       |

### <a name="view-the-device-status"></a>Anzeigen des Gerätestatus

Zum Anzeigen des Status eines Auftrags und aller betroffenen Geräte wählen Sie den Auftrag aus. Klicken Sie auf **Auftragsdetails herunterladen**, um eine CSV-Datei herunterzuladen, die die Auftragsdetails sowie eine Liste der Geräte mit ihren Statuswerten enthält. Neben den einzelnen Gerätenamen werden folgende Statusmeldungen angezeigt:

| Statusmeldung       | Bedeutung der Statusmeldung                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Abgeschlossen            | Der Auftrag wurde auf diesem Gerät ausgeführt.                                     |
| Fehler               | Beim Ausführen des Auftrags auf diesem Gerät ist ein Fehler aufgetreten. In der Fehlermeldung sind weitere Informationen enthalten.  |
| Ausstehend              | Der Auftrag wurde auf diesem Gerät noch nicht ausgeführt.                                   |

> [!NOTE]
> Wenn ein Gerät gelöscht wurde, können Sie das Gerät nicht auswählen und es wird mit der Geräte-ID als gelöscht angezeigt.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun erfahren haben, wie Aufträge in Ihrer Azure IoT Central-Anwendung erstellt werden, können Sie die nächsten Schritte ausführen:

- [Verwenden von Gerätegruppen](howto-use-device-sets.md)
- [Verwalten von Geräten](howto-manage-devices.md)
- [Verwalten der Versionen Ihrer Gerätevorlage](howto-version-devicetemplate.md)
