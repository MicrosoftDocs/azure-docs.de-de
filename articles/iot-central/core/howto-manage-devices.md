---
title: Verwalten der Geräte in Ihrer Azure IoT Central-Anwendung | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie als Operator Gerätegruppen in der Azure IoT Central-Anwendung verwalten. Erfahren Sie, wie Sie einzelne Geräte verwalten und Massenimporte und -exporte der Geräte in Ihrer Anwendung ausführen.
author: dominicbetts
ms.author: dobett
ms.date: 10/08/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.custom: contperf-fy21q2
ms.openlocfilehash: 2f0b6feea5e586c87191b22f42e3ab86e85ba7b3
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97032523"
---
# <a name="manage-devices-in-your-azure-iot-central-application"></a>Verwalten von Geräten in Ihrer Azure IoT Central-Anwendung

In diesem Artikel wird beschrieben, wie Sie als Operator Geräte in Ihrer Azure IoT Central-Anwendung verwalten. Als Operator können Sie folgende Aktionen ausführen:

- Mit der **Geräte**-Seite Geräte anzeigen, hinzufügen und löschen, die mit Ihrer Azure IoT Central-Anwendung verbunden sind.
- Führen Sie Massenimporte und -exporte von Geräten aus.
- Verwalten Sie ein aktuelles Inventar Ihrer Geräte.
- Halten Sie Ihre Gerätemetadaten durch Ändern der in den Geräteeigenschaften gespeicherten Werte in Ihren Ansichten auf dem neuesten Stand.
- Steuern Sie das Verhalten Ihrer Geräte, indem Sie eine Einstellung auf einem bestimmten Gerät in Ihren Ansichten aktualisieren.

Informationen zum Verwalten von benutzerdefinierten Gruppen von Geräten finden Sie im [Tutorial: Verwenden von Gerätegruppen zum Analysieren von Gerätetelemetriedaten](tutorial-use-device-groups.md).

## <a name="view-your-devices"></a>Anzeigen von Geräten

So zeigen Sie ein einzelnes Gerät an:

1. Wählen Sie im linken Bereich **Geräte** aus. Hier sehen Sie eine Liste all Ihrer Geräte und Gerätevorlagen.

1. Wählen Sie eine Gerätevorlage aus.

1. Im rechten Bereich der Seite **Geräte** sehen Sie eine Liste von Geräten, die anhand dieser Gerätevorlage erstellt wurden. Wählen Sie ein einzelnes Gerät aus, um die Seite „Gerätedetails“ für dieses Gerät anzuzeigen:

    ![Seite „Gerätedetails“](./media/howto-manage-devices/devicelist.png)

## <a name="add-a-device"></a>Hinzufügen eines Geräts

So fügen Sie ein Gerät Ihrer Azure IoT Central-Anwendung hinzu:

1. Wählen Sie im linken Bereich **Geräte** aus.

1. Wählen Sie die Gerätevorlage aus, von der Sie ein Gerät erstellen möchten.

1. Wählen Sie **+ Neu** aus.

1. Schalten Sie die Umschaltfläche **Simuliert** auf **Ein** oder **Aus**. Ein reales Gerät steht für ein physisches Gerät, dessen Verbindung mit der Azure IoT Central-Anwendung Sie herstellen. Ein simuliertes Gerät enthält Beispieldaten, die von Azure IoT Central für Sie generiert werden.

1. Klicken Sie auf **Erstellen**.

1. Dieses Gerät wird jetzt in der Geräteliste für diese Vorlage angezeigt. Wählen Sie das Gerät aus, um die Seite „Gerätedetails“ anzuzeigen, die alle Ansichten für das Gerät enthält.

## <a name="import-devices"></a>Importieren von Geräten

Um eine Verbindung einer großen Anzahl von Geräten mit Ihrer Anwendung herzustellen, können Sie einen Massenimport von Geräten aus einer CSV-Datei ausführen. Die CSV-Datei sollte die folgenden Spalten und Kopfzeilen haben:

* **IOTC_DeviceID**: Die Geräte-ID kann Buchstaben, Ziffern und das Zeichen `-` enthalten.
* **IOTC_DeviceName** – diese Spalte ist optional.

So führen Sie die Massenregistrierung von Geräten in Ihrer Anwendung durch:

1. Wählen Sie im linken Bereich **Geräte** aus.

1. Wählen Sie im linken Bereich die Gerätevorlage aus, für die Sie das Massenerstellen der Geräte ausführen möchten.

    > [!NOTE]
    > Wenn Sie noch keine Gerätevorlage haben, können Sie Geräte unter **Alle Geräte** importieren und ohne Vorlage registrieren. Nachdem die Geräte importiert wurden, können Sie sie zu einer Vorlage migrieren.

1. Wählen Sie **Importieren** aus.

    ![Importaktion](./media/howto-manage-devices/bulkimport1a.png)


1. Wählen Sie die CSV-Datei aus, die die Liste der zu importierenden Geräte-IDs enthält.

1. Der Geräteimport beginnt, sobald die Datei hochgeladen wurde. Sie können den Status des Importvorgangs im Vorgangspanel des Geräts verfolgen. Dieses Panel wird automatisch nach dem Start des Imports angezeigt, oder Sie können es über das Glockensymbol oben rechts aufrufen.

1. Nach Abschluss des Imports wird eine Erfolgsmeldung im Vorgangspanel des Geräts angezeigt.

    ![Import erfolgreich](./media/howto-manage-devices/bulkimport3a.png)

Wenn beim Geräteimport ein Fehler auftritt, wird im Vorgangspanel des Geräts eine Fehlermeldung angezeigt. Eine Protokolldatei zur Erfassung aller Fehler wird generiert, die Sie herunterladen können.

## <a name="migrate-devices-to-a-template"></a>Migrieren von Geräten zu einer Vorlage

Wenn Sie Geräte registrieren, indem Sie den Import unter **Alle Geräte** starten, dann werden die Geräte ohne Gerätevorlagenzuordnung erstellt. Geräte müssen mit einer Vorlage verknüpft sein, damit ein Zugriff auf Daten und andere Details zum Gerät möglich ist. Führen Sie die folgenden Schritte aus, um Geräte mit einer Vorlage zu verknüpfen:

1. Wählen Sie im linken Bereich **Geräte** aus.

1. Wählen Sie im linken Bereich die Option **Alle Geräte** aus:

    ![Nicht zugeordnete Geräte](./media/howto-manage-devices/unassociateddevices1a.png)

1. Verwenden Sie den Filter im Raster, um festzustellen, ob der Wert in der Spalte **Gerätevorlage** für eines Ihrer Geräte **Nicht zugeordnet** lautet.

1. Wählen Sie die Geräte aus, die Sie mit einer Vorlage verknüpfen möchten:

1. Wählen Sie **Migrieren** aus:

    ![Zuordnen von Geräten](./media/howto-manage-devices/unassociateddevices2a.png)

1. Wählen Sie eine verfügbare Vorlage in der Liste aus, und wählen Sie **Migrieren** aus.

1. Die ausgewählten Geräte werden mit der ausgewählten Gerätevorlage verknüpft.

## <a name="export-devices"></a>Exportieren von Geräten

Wenn Sie ein echtes Gerät mit IoT Central verbinden möchten, benötigen Sie dessen Verbindungszeichenfolge. Sie können die Gerätedetails per Massenexport exportieren, um die Informationen abzurufen, die Sie zum Erstellen der Verbindungszeichenfolgen für die Geräte benötigen. Durch den Exportvorgang wird eine CSV-Datei mit der Geräteidentität, dem Gerätenamen und den Schlüsseln für alle ausgewählten Geräte erstellt.

So führen Sie den Massenexport von Geräten aus Ihrer Anwendung durch:

1. Wählen Sie im linken Bereich **Geräte** aus.

1. Wählen Sie im linken Bereich die Gerätevorlage aus, aus der Sie die Geräte exportieren möchten.

1. Wählen Sie die Geräte aus, die Sie exportieren möchten, und wählen Sie dann die Aktion **Exportieren** aus.

    ![Exportieren](./media/howto-manage-devices/export1a.png)

1. Der Exportvorgang wird gestartet. Sie können den Status des Status im Vorgangspanel des Geräts verfolgen.

1. Wenn der Exportvorgang abgeschlossen ist, wird eine Erfolgsmeldung zusammen mit einem Link zum Download der generierten Datei angezeigt.

1. Wählen Sie den Link **Datei herunterladen** aus, um die Datei in einen lokalen Ordner auf dem Datenträger herunterzuladen.

    ![Export erfolgreich](./media/howto-manage-devices/export2a.png)

1. Die exportierte CSV-Datei enthält die folgenden Spalten: Geräte-ID, Name des Geräts, Geräteschlüssel und X509-Zertifikatfingerabdrücke:

    * IOTC_DEVICEID
    * IOTC_DEVICENAME
    * IOTC_SASKEY_PRIMARY
    * IOTC_SASKEY_SECONDARY
    * IOTC_X509THUMBPRINT_PRIMARY
    * IOTC_X509THUMBPRINT_SECONDARY

Unter [Gerätekonnektivität in Azure IoT Central](concepts-get-connected.md) finden Sie weitere Informationen zu Verbindungszeichenfolgen und zum Verbinden von echten Geräten für Ihre IoT Central-Anwendung.

## <a name="delete-a-device"></a>Löschen eines Mediums

So löschen Sie entweder ein reales oder simuliertes Gerät aus Ihrer Azure IoT Central-Anwendung:

1. Wählen Sie im linken Bereich **Geräte** aus.

1. Wählen Sie die Gerätevorlage des Geräts aus, das Sie löschen möchten.

1. Verwenden Sie die Filtertools, um Ihre Geräte zu filtern und zu suchen. Aktivieren Sie das Kontrollkästchen neben den zu löschenden Geräten.

1. Wählen Sie **Löschen** aus. Sie können den Status dieses Löschvorgangs im Vorgangspanel des Geräts verfolgen.

## <a name="change-a-property"></a>Ändern einer Eigenschaft

Cloudeigenschaften sind die dem Gerät zugeordneten Gerätemetadaten, z.B. der Ort und die Seriennummer. Cloudeigenschaften sind nur in der IoT Central-Anwendung vorhanden und werden nicht mit Ihren Geräten synchronisiert. Beschreibbare Eigenschaften steuern das Verhalten eines Geräts und ermöglichen Ihnen, den Zustand eines Geräts remote festzulegen, indem Sie z. B. die Zieltemperatur eines Thermostats angeben.  Geräteeigenschaften werden vom Gerät festgelegt und sind in Azure IoT Central schreibgeschützt. Sie können Eigenschaften in der Ansicht **Gerätedetails** anzeigen und aktualisieren.

1. Wählen Sie im linken Bereich **Geräte** aus.

1. Wählen Sie die Gerätevorlage des Geräts aus, dessen Eigenschaften Sie ändern möchten, und das Zielgerät.

1. Wählen Sie die Ansicht, die Eigenschaften für Ihr Gerät enthält. In dieser Ansicht können Sie Werte eingeben und oben auf der Seite **Speichern** auswählen. Hier sehen Sie alle Eigenschaften Ihres Geräts und ihre aktuellen Werte. Cloudeigenschaften und schreibbare Eigenschaften haben bearbeitbare Felder, Geräteeigenschaften sind schreibgeschützt. Bei schreibbaren Eigenschaften sehen Sie deren Synchronisationsstatus am unteren Rand des Feldes. 

1. Ändern Sie die Eigenschaften in die benötigten Werte. Sie können mehrere Eigenschaften auf einmal ändern und alle gleichzeitig aktualisieren.

1. Wählen Sie **Speichern** aus. Wenn Sie schreibbare Eigenschaften gespeichert haben, werden die Werte an Ihr Gerät gesendet. Wenn das Gerät die Änderung für die beschreibbare Eigenschaft bestätigt, wird der Status wieder auf **synchronisiert** gesetzt. Wenn Sie eine Cloudeigenschaft gespeichert haben, wird der Wert aktualisiert.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun erfahren haben, wie Sie Geräte in Ihrer Azure IoT Central-Anwendung verwalten, wird als nächster Schritt das [Konfigurieren von Regeln](howto-configure-rules.md) für Ihre Geräte empfohlen.
