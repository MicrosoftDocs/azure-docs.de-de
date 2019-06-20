---
title: Verwalten der Geräte in Ihrer Azure IoT Central-Anwendung | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie als Operator Gerätegruppen in der Azure IoT Central-Anwendung verwalten.
author: ellenfosborne
ms.author: elfarber
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: a4a22cc2161af444ba2169cc2f83124e80c7ec11
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052997"
---
# <a name="manage-devices-in-your-azure-iot-central-application"></a>Verwalten von Geräten in Ihrer Azure IoT Central-Anwendung

In diesem Artikel wird beschrieben, wie Sie als Operator Gerätegruppen in Ihrer Azure IoT Central-Anwendung verwalten. Als Operator können Sie folgende Aktionen ausführen:

- Sie können mit der **Device Explorer**-Seite Geräte anzeigen, hinzufügen und löschen, die mit Ihrer Azure IoT Central-Anwendung verbunden sind.
- Verwalten Sie ein aktuelles Inventar Ihrer Geräte.
- Halten Sie Ihre Gerätemetadaten durch Ändern der in den Geräteeigenschaften gespeicherten Werte auf dem neuesten Stand.
- Steuern Sie das Verhalten Ihrer Geräte, indem Sie eine Einstellung auf einem bestimmten Gerät auf der Seite **Einstellungen** aktualisieren.

## <a name="view-your-devices"></a>Anzeigen von Geräten

So zeigen Sie ein einzelnes Gerät an:

1. Wählen Sie im **Device Explorer** auf der linken Seite die Option Explorer aus. Hier sehen Sie eine Liste Ihrer [Gerätevorlagen](howto-set-up-template.md).

1. Wählen Sie in der Liste **Vorlagen** eine Gerätevorlage aus.

1. Im rechten Bereich der Seite **Device Explorer** sehen Sie eine Liste von Geräten, die anhand dieser Gerätevorlage erstellt wurden. Wählen Sie ein einzelnes Gerät aus, um die Seite „Gerätedetails“ für dieses Gerät anzuzeigen:

    ![Seite „Gerätedetails“](./media/howto-manage-devices/devicelist.png)

## <a name="add-a-device"></a>Hinzufügen eines Geräts

So fügen Sie ein Gerät Ihrer Azure IoT Central-Anwendung hinzu:

1. Wählen Sie im **Device Explorer** auf der linken Seite die Option Explorer aus.

1. Wählen Sie die Gerätevorlage aus, von der Sie ein Gerät erstellen möchten.

1. Wählen Sie **+ Neu** aus.

1. Wählen Sie **Real** oder **Simuliert** aus. Ein reales Gerät steht für ein physisches Gerät, dessen Verbindung mit der Azure IoT Central-Anwendung Sie herstellen. Ein simuliertes Gerät enthält Beispieldaten, die von Azure IoT Central für Sie generiert werden.

## <a name="import-devices"></a>Importieren von Geräten

Um eine Verbindung einer großen Anzahl von Geräten mit Ihrer Anwendung herzustellen, können Sie einen Massenimport von Geräten aus einer CSV-Datei ausführen. Die CSV-Datei sollte die folgenden Spalten und Kopfzeilen haben:

* **IOTC_DeviceID** – die Geräte-ID sollte ganz aus Kleinbuchstaben bestehen.
* **IOTC_DeviceName** – diese Spalte ist optional.

So führen Sie die Massenregistrierung von Geräten in Ihrer Anwendung durch:

1. Wählen Sie im **Device Explorer** auf der linken Seite die Option Explorer aus.

1. Wählen Sie im linken Bereich die Gerätevorlage aus, für die Sie das Massenerstellen der Geräte ausführen möchten.

    > [!NOTE]
    > Wenn Sie noch keine Gerätevorlage haben, können Sie Geräte unter **Nicht zugeordnete Geräte** importieren und ohne eine Vorlage registrieren. Nachdem die Geräte importiert wurden, können Sie sie einer Vorlage zuordnen.

1. Wählen Sie **Importieren** aus.

    ![Importaktion](./media/howto-manage-devices/bulkimport1a.png)

1. Wählen Sie die CSV-Datei aus, die die Liste der zu importierenden Geräte-IDs enthält.

1. Der Geräteimport beginnt, sobald die Datei hochgeladen wurde. Sie können den Status des Importvorgangs am Kopf des Geräterasters verfolgen.

1. Nach Abschluss des Imports wird eine Erfolgsmeldung auf dem Geräteraster angezeigt.

    ![Import erfolgreich](./media/howto-manage-devices/bulkimport3a.png)

Wenn beim Geräteimport ein Fehler auftritt, wird auf dem Geräteraster eine Fehlermeldung angezeigt. Eine Protokolldatei zur Erfassung aller Fehler wird generiert, die Sie herunterladen können.

**Verknüpfen von Geräten mit einer Vorlage**

Wenn Sie Geräte registrieren, indem Sie den Import unter **Nicht zugeordnete Geräte** starten, dann werden die Geräte ohne Gerätevorlagenzuordnung erstellt. Geräte müssen mit einer Vorlage verknüpft sein, damit ein Zugriff auf Daten und andere Details zum Gerät möglich ist. Führen Sie die folgenden Schritte aus, um Geräte mit einer Vorlage zu verknüpfen:

1. Wählen Sie im **Device Explorer** auf der linken Seite die Option Explorer aus.

1. Wählen Sie im linken Bereich **Nicht zugeordnete Geräte** aus:

    ![Nicht zugeordnete Geräte](./media/howto-manage-devices/unassociateddevices1a.png)

1. Wählen Sie die Geräte aus, die Sie mit einer Vorlage verknüpfen möchten:

1. Wählen Sie **Zuordnen** aus:

    ![Zuordnen von Geräten](./media/howto-manage-devices/unassociateddevices2a.png)

1. Wählen Sie eine verfügbare Vorlage in der Liste aus, und wählen Sie **Zuordnen** aus.

1. Die ausgewählten Geräte werden mit der ausgewählten Gerätevorlage verknüpft.

> [!NOTE]
> Nachdem ein Gerät mit einer Vorlage verknüpft wurde, kann seine Zuordnung nicht mehr aufgehoben werden, und es kann auch keiner anderen Vorlage zugeordnet werden.

## <a name="export-devices"></a>Exportieren von Geräten

Wenn Sie ein echtes Gerät mit IoT Central verbinden möchten, benötigen Sie dessen Verbindungszeichenfolge. Sie können die Gerätedetails per Massenexport exportieren, um die Informationen abzurufen, die Sie zum Erstellen der Verbindungszeichenfolgen für die Geräte benötigen. Durch den Exportvorgang wird eine CSV-Datei mit der Geräteidentität, dem Gerätenamen und den Schlüsseln für alle ausgewählten Geräte erstellt.

So führen Sie den Massenexport von Geräten aus Ihrer Anwendung durch:

1. Wählen Sie im **Device Explorer** auf der linken Seite die Option Explorer aus.

1. Wählen Sie im linken Bereich die Gerätevorlage aus, aus der Sie die Geräte exportieren möchten.

1. Wählen Sie die Geräte aus, die Sie exportieren möchten, und wählen Sie dann die Aktion **Exportieren** aus.

    ![Export](./media/howto-manage-devices/export1a.png)

1. Der Exportvorgang wird gestartet. Sie können dessen Status oben im Raster verfolgen.

1. Wenn der Exportvorgang abgeschlossen ist, wird eine Erfolgsmeldung zusammen mit einem Link zum Download der generierten Datei angezeigt.

1. Wählen Sie die **Erfolgsmeldung** aus, um die Datei in einen lokalen Ordner auf dem Datenträger herunterzuladen.

    ![Export erfolgreich](./media/howto-manage-devices/export2a.png)

1. Die exportierte CSV-Datei enthält die folgenden Spalten: Geräte-ID, Name des Geräts, Geräteschlüssel und X509-Zertifikatfingerabdrücke:

    * IOTC_DEVICEID
    * IOTC_DEVICENAME
    * IOTC_SASKEY_PRIMARY
    * IOTC_SASKEY_SECONDARY
    * IOTC_X509THUMBPRINT_PRIMARY
    * IOTC_X509THUMBPRINT_SECONDARY

Unter [Gerätekonnektivität in Azure IoT Central](concepts-connectivity.md) finden Sie weitere Informationen zu Verbindungszeichenfolgen und zum Verbinden von echten Geräten für Ihre IoT Central-Anwendung.

## <a name="delete-a-device"></a>Gerät löschen

So löschen Sie entweder ein reales oder simuliertes Gerät aus Ihrer Azure IoT Central-Anwendung:

1. Wählen Sie im Navigationsmenü die Option **Device Explorer** aus.

1. Wählen Sie die Gerätevorlage des Geräts aus, das Sie löschen möchten.

1. Aktivieren Sie das Kontrollkästchen neben dem zu löschenden Gerät.

1. Wählen Sie **Löschen** aus.

## <a name="change-a-device-setting"></a>Ändern einer Geräteeinstellung

Einstellungen steuern das Verhalten eines Geräts. D.h., damit können Sie Eingaben für Ihr Gerät bereitstellen. Sie können Geräteeinstellungen auf der Seite **Gerätedetails** anzeigen und aktualisieren.

1. Wählen Sie im Navigationsmenü die Option **Device Explorer** aus.

1. Wählen Sie die Gerätevorlage des Geräts aus, dessen Einstellungen Sie ändern möchten.

1. Wählen Sie die Registerkarte **Einstellungen** aus. Hier sehen Sie alle Einstellungen Ihres Geräts und ihre aktuellen Werte. Für jede Einstellung können Sie sehen, ob das Gerät noch synchronisiert wird.

1. Ändern Sie die Einstellungen auf die benötigten Werte. Sie können jeweils mehrere Einstellungen ändern und alle auf einmal aktualisieren.

1. Wählen Sie **Aktualisieren** aus. Die Werte werden an Ihr Gerät gesendet. Wenn das Gerät die Einstellungsänderung bestätigt, ändert sich der Status der Einstellung wieder in **Synchronisiert**.

## <a name="change-a-property"></a>Ändern einer Eigenschaft

Eigenschaften sind die dem Gerät zugeordneten Gerätemetadaten, z.B. der Ort und die Seriennummer. Sie können Eigenschaften auf der Seite **Gerätedetails** anzeigen und aktualisieren.

1. Wählen Sie im Navigationsmenü die Option **Device Explorer** aus.

1. Wählen Sie die Gerätevorlage des Geräts aus, dessen Eigenschaften Sie ändern möchten.

1. Wählen Sie die Registerkarte **Eigenschaften** aus, auf der alle Eigenschaften angezeigt werden.

1. Ändern Sie die Anwendungseigenschaften in die benötigten Werte. Sie können mehrere Eigenschaften auf einmal ändern und alle gleichzeitig aktualisieren. Wählen Sie **Aktualisieren** aus.

> [!NOTE]
> Sie können den Wert der _Geräteeigenschaften_ nicht ändern. Geräteeigenschaften werden vom Gerät festgelegt und sind in der Azure IoT Central-Anwendung schreibgeschützt.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie erfahren haben, wie Geräte in Ihrer Azure IoT Central-Anwendung verwaltet werden, sollte dies der nächste Schritt sein:

> [!div class="nextstepaction"]
> [Verwenden von Gerätegruppen](howto-use-device-sets.md)

<!-- Next how-tos in the sequence -->
