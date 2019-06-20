---
title: Einrichten einer Gerätevorlage in einer Azure IoT Central-Anwendung | Microsoft-Dokumentation
description: Informationen zum Einrichten einer Gerätevorlage mit Messungen, Einstellungen, Eigenschaften, Regeln und Dashboard.
author: viv-liu
ms.author: viviali
ms.date: 01/30/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: d1704220a13b6d6b5a48b7167d7912a38057127d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65466506"
---
# <a name="set-up-a-device-template"></a>Einrichten einer Gerätevorlage

Eine Gerätevorlage ist eine Blaupause, die die Merkmale und das Verhalten eines Gerätetyps definiert, der eine Verbindung mit einer Azure IoT Central-Anwendung herstellt.

Beispielsweise kann ein Hersteller eine Gerätevorlage für einen mit IoT verbundenen Lüfter mit folgenden Merkmalen erstellen:

- Messung der Temperaturtelemetrie

- Messung von Lüftermotor-Fehlerereignissen

- Messung des Lüfterbetriebszustands

- Einstellung der Lüfterdrehzahl

- Standorteigenschaft

- Regeln zum Senden von Warnungen

- Dashboard mit einer allgemeinen Übersicht über das Gerät

Anhand dieser Gerätevorlage kann ein Operator reale Lüftergeräte mit Namen wie z.B. **Lüfter 1** und **Lüfter 2** erstellen und verbinden. All diese Lüfter weisen Messungen, Einstellungen, Eigenschaften, Regeln und ein Dashboard auf, die von Benutzern Ihrer Anwendung überwacht und verwaltet werden können.

> [!NOTE]
> Nur Ersteller und Administratoren können Gerätevorlagen erstellen, bearbeiten und löschen. Auf der Seite **Device Explorer** kann jeder Benutzer Geräte anhand vorhandener Gerätevorlagen erstellen.

## <a name="create-a-device-template"></a>Erstellen einer Gerätevorlage

1. Navigieren Sie zur Seite **Gerätevorlagen**.

2. Um eine Vorlage zu erstellen, wählen Sie zunächst **+ Neu** aus.

3. Um schnell einzusteigen, wählen Sie zwischen den vorhandenen vorab erstellten Vorlagen aus. Wählen Sie andernfalls **Benutzerdefiniert** aus, geben Sie einen Namen ein, und klicken Sie auf **Erstellen**, um eine eigene Vorlage von Grund auf neu zu erstellen.

   ![Gerätevorlagenbibliothek](./media/howto-set-up-template/newtemplate.png)

4. Wenn Sie eine benutzerdefinierte Vorlage erstellen, wird die Seite **Gerätedetails** für Ihre neue Gerätevorlage angezeigt. IoT Central erstellt automatisch ein simuliertes Gerät, wenn Sie eine Gerätevorlage erstellen. Mit einem simulierten Gerät können Sie das Verhalten Ihrer Anwendung testen, bevor Sie eine Verbindung mit einem echten Gerät herstellen.

Die folgenden Abschnitte beschreiben die Registerkarten auf der Seite **Gerätevorlage**.

## <a name="measurements"></a>Messungen

Messungen sind die Daten, die von Ihrem Gerät stammen. Sie können Ihrer Gerätevorlage mehrere Messungen hinzufügen, die den Funktionen Ihres Geräts entsprechen.

- **Telemetriemessungen** sind die numerischen Datenpunkte, die im Lauf der Zeit von Ihrem Gerät erfasst werden. Diese werden als kontinuierlicher Datenstrom dargestellt. Ein Beispiel ist Temperatur.
- **Ereignismessungen** sind zeitpunktbezogene Daten, die ein bedeutsames Ereignis auf dem Gerät darstellen. Ein Schweregrad stellt die Bedeutung eines Ereignisses dar. Ein Beispiel ist ein Lüftermotorfehler.
- **Zustandsmessungen** bilden den Zustand des Geräts oder der zugehörigen Komponenten über einen Zeitraum ab. Beispielsweise können für den Lüftermodus **In Betrieb** und **Angehalten** als die beiden möglichen Zustandsangaben definiert werden.

### <a name="create-a-telemetry-measurement"></a>Erstellen einer Telemetriemessung

Um eine neue Telemetriemessung hinzuzufügen, wählen Sie **+ Neue Messung** aus, wählen Sie den Messungstyp **Telemetrie** aus, und geben Sie die Details im Formular ein.

> [!NOTE]
> Die Feldnamen in der Gerätevorlage müssen mit den Eigenschaftennamen im entsprechenden Gerätecode übereinstimmen, damit die Telemetriemessung in der Anwendung angezeigt werden, wenn ein echtes Gerät verbunden ist. Gehen Sie beim Konfigurieren von Einstellungen, Geräteeigenschaften und Befehlen auf die gleiche Weise vor, wenn Sie die Gerätevorlage in den folgenden Abschnitten weiter definieren.
png. Beispielsweise können Sie eine neue Temperaturtelemetriemessung hinzufügen:

| Anzeigename        | Feldname    |  Units    | Min   |max|
| --------------------| ------------- |-----------|-------|---|
| Temperatur         | temp          |  degC     |  0    |100|

![Formular „Telemetrie erstellen“ mit Details zur Temperaturmessung](./media/howto-set-up-template/measurementsform.png)

Nachdem Sie auf **Speichern** geklickt haben, wird die Messung **Temperatur** in der Liste der Messungen angezeigt. In Kürze sehen Sie die Visualisierung der Temperaturdaten des simulierten Geräts.

Beim Anzeigen von Telemetriedaten können Sie zwischen den folgenden Aggregationsoptionen auswählen: Mittelwert, Minimum, Maximum, Summe und Anzahl. **Mittelwert** ist als Standardaggregation im Diagramm ausgewählt. 

> [!NOTE]
> Die Telemetriemessung gibt Daten als Gleitkommazahl aus.

### <a name="create-an-event-measurement"></a>Erstellen einer Ereignismessung

Um eine neue Ereignismessung hinzuzufügen, wählen Sie **+ Neue Messung** aus, und wählen Sie den Messungstyp **Ereignis** aus. Geben Sie die Details im Formular **Ereignis erstellen** ein.

Geben Sie in diesem Formular den **Anzeigenamen**, **Feldnamen** und Details zum **Schweregrad** des Ereignisses an. Sie können aus den folgenden drei Schweregraden auswählen: **Fehler**, **Warnung** und **Information**.

Beispielsweise können Sie das neue Ereignis **Lüftermotorfehler** hinzufügen.

| Anzeigename        | Feldname    |  Standardschweregrad |
| --------------------| ------------- |-----------|
| Fan Motor Error     | fanmotorerror |  Error    |

![Formular „Ereignis erstellen“ mit Details zu einem Lüftermotorereignis](./media/howto-set-up-template/eventmeasurementsform.png)

Nachdem Sie auf **Speichern** geklickt haben, wird die Messung **Lüftermotorfehler** in der Liste der Messungen angezeigt. In Kürze sehen Sie die Visualisierung der Ereignisdaten des simulierten Geräts.

Wählen Sie im Diagramm das Ereignissymbol aus, um zusätzliche Details zu einem Ereignis anzuzeigen:

![Details für das Ereignis „Lüftermotorfehler“](./media/howto-set-up-template/eventmeasurementsdetail.png)

> [!NOTE]
> Die Ereignismessung gibt Daten vom Typ „string“ aus.

### <a name="create-a-state-measurement"></a>Erstellen einer Zustandsmessung

Um eine neue Zustandsmessung hinzuzufügen, wählen Sie **+ Neue Messung** aus, und wählen Sie den Messungstyp **Zustand** aus. Geben Sie die Details im Formular **Zustand erstellen** ein.

Geben Sie in diesem Formular den **Anzeigenamen**, **Feldnamen** und **Werte** für den Zustand an. Jeder Wert kann auch einen Anzeigenamen aufweisen, der bei der Anzeige des Werts in Diagrammen und Tabellen verwendet wird.

Sie können z.B. den neuen Zustand **Lüftermodus** hinzufügen, der zwei mögliche Werte hat, die vom Gerät gesendet werden können: **In Betrieb** und **Angehalten**.

| Anzeigename | Feldname    |  Wert 1   | Anzeigename | Wert 2    |Anzeigename  | 
| -------------| ------------- |----------- | -------------| -----------| -------------|
| Fan Mode     | fanmode       |  1         | Operating    |     0      | Beendet      |

![Formular „Zustand bearbeiten“ mit Details zum Lüftermodus](./media/howto-set-up-template/statemeasurementsform.png)

Nachdem Sie auf **Speichern** geklickt haben, wird die Messung **Lüftermodus** in der Liste der Messungen angezeigt. In Kürze sehen Sie die Visualisierung der Zustandsdaten des simulierten Geräts.

Wenn das Gerät in einer kleinen Zeitspanne zu viele Datenpunkte sendet, wird die Zustandsmessung mit einem anderen visuellen Element angezeigt. Wählen Sie das Diagramm aus, um alle Datenpunkte innerhalb dieses Zeitraums in chronologischer Reihenfolge anzuzeigen. Sie können den Zeitbereich auch einschränken, um die Messung im Diagramm anzuzeigen.

> [!NOTE]
> Die Statusmessung gibt Daten vom Typ „string“ aus.

## <a name="settings"></a>Einstellungen

Ein Gerät wird durch Einstellungen gesteuert. Diese ermöglichen den Bedienern die Angabe von Eingaben für das Gerät. Sie können Ihrer Gerätevorlage mehrere Einstellungen hinzufügen, die auf der Registerkarte **Einstellungen** als Kacheln angezeigt und von Operatoren verwendet werden können. Sie können verschiedene Arten von Einstellungen hinzufügen: Drehzahl, Text, Datum, Ein-/Ausschalten, Auswahlliste und Abschnittsbezeichnung.

Einstellungen können einen von drei Zuständen aufweisen. Das Gerät meldet diese Zustände.

- **Synchronisiert**: Das Gerät wurde entsprechend dem Einstellungswert geändert.

- **Pending**: Das Gerät wird zurzeit auf den Einstellungswert geändert.

- **Fehler**: Das Gerät hat einen Fehler zurückgegeben.

Sie können zum Beispiel eine neue Einstellung für die Lüfterdrehzahl hinzufügen, indem Sie **Einstellungen** auswählen und die neue Einstellung für die **Nummer** eingeben:

| Anzeigename  | Feldname    |  Units  | Dezimalstellen |Initial|
| --------------| ------------- |---------| ---------|---- |
| Lüfterdrehzahl     | fanSpeed      | U/Min     | 2        | 0   |

![Formular „Drehzahl konfigurieren“ mit Details zu Geschwindigkeitseinstellungen](./media/howto-set-up-template/settingsform.png)

Nach der Auswahl von **Speichern** wird die Einstellung **Lüfterdrehzahl** als Kachel angezeigt. Ein Bediener kann mit der Einstellung auf der Seite **Device Explorer** die Lüfterdrehzahl des Geräts ändern.

## <a name="properties"></a>Eigenschaften

Eigenschaften sind die dem Gerät zugeordneten Gerätemetadaten, z.B. Gerätestandort und Seriennummer. Fügen Sie Ihrer Gerätevorlage mehrere Eigenschaften hinzu, die auf der Registerkarte **Eigenschaften** als Kacheln angezeigt werden. Eine Eigenschaft kann einen Typ wie z.B. Anzahl, Text, Datum, Ein-/Ausschalten, Geräteeigenschaft, Bezeichnung oder Standort haben. Ein Bediener kann die Werte für Eigenschaften beim Erstellen eines neuen Geräts angeben und sie anschließend jederzeit bearbeiten. Geräteeigenschaften sind schreibgeschützt und werden vom Gerät an die Anwendung gesendet. Ein Bediener kann Eigenschaften des Geräts nicht ändern. Wenn ein echtes Gerät eine Verbindung herstellt, wird die Geräteeigenschaftenkachel in der Anwendung aktualisiert.

Es gibt zwei Kategorien von Eigenschaften:

- _Geräteeigenschaften_ werden vom Gerät an die IoT Central-Anwendung gemeldet. Geräteeigenschaften sind schreibgeschützte Werte, die vom Gerät gemeldet und in der Anwendung aktualisiert werden, wenn das echte Gerät verbunden wird.
- _Anwendungseigenschaften_ werden in der Anwendung gespeichert und können vom Bediener bearbeitet werden. Das Gerät erkennt keine Anwendungseigenschaften.

Sie können z.B. das Datum der letzten Wartung für das Gerät als neue Eigenschaft **Datum** (eine Anwendungseigenschaft) auf der Registerkarte **Eigenschaften** hinzufügen:

| Anzeigename  | Feldname | Anfangswert   |
| --------------| -----------|-----------------|
| Datum der letzten Wartung      | lastServiced        | 29\.01.2019     |

![Formular zum Konfigurieren des Datums der letzten Wartung auf der Registerkarte „Eigenschaften“](./media/howto-set-up-template/propertiesform.png)

Nach der Auswahl von **Speichern** wird das Datum der letzten Wartung für das Gerät als Kachel angezeigt.

Nach Erstellung der Kachel können Sie den Wert der Anwendungseigenschaft im **Device Explorer** ändern.

### <a name="create-a-location-property-through-azure-maps"></a>Erstellen einer Standorteigenschaft über Azure Maps

Sie können Ihre Standortdaten mit geografischem Kontext in Azure IoT Central versehen und Breiten- und Längengradkoordinaten einer Anschrift zuordnen. Alternativ können Sie Breiten- und Längengradkoordinaten zuordnen. Azure Maps unterstützt diese Funktion in IoT Central.

Sie können zwei Arten von Standorteigenschaften hinzufügen:

- **Standort als eine Anwendungseigenschaft**, die in der Anwendung gespeichert wird. Das Gerät erkennt keine Anwendungseigenschaften.
- **Standort als eine Geräteeigenschaft**, die vom Gerät an die Anwendung gemeldet wird.

#### <a name="add-location-as-an-application-property"></a>Hinzufügen eines Standorts als eine Anwendungseigenschaft

Sie können mithilfe von Azure Maps in Ihrer Azure IoT Central-Anwendung eine Standorteigenschaft als eine Anwendungseigenschaft erstellen. Beispielsweise können Sie die Geräteinstallationsadresse hinzufügen:

1. Navigieren Sie zur Registerkarte **Eigenschaften**.

2. Wählen Sie in der Bibliothek **Standort** aus.

3. Konfigurieren Sie den **Anzeigenamen**, **Feldnamen** und (optional) den **Anfangswert** des Standorts.

    | Anzeigename  | Feldname | Anfangswert |
    | --------------| -----------|---------| 
    | Installationsadresse | installAddress | Microsoft, 1 Microsoft Way, Redmond, WA 98052   |

   ![Formular „Standort konfigurieren“ mit Details zum Standort](./media/howto-set-up-template/locationcloudproperty2.png)

   Es gibt zwei unterstützte Formate zum Hinzufügen eines Standorts:
   - **Standort als Adresse**
   - **Standort als Koordinaten**

4. Wählen Sie **Speichern** aus. Ein Bediener kann den Standortwert im **Device Explorer** aktualisieren.

#### <a name="add-location-as-a-device-property"></a>Hinzufügen eines Standorts als eine Geräteeigenschaft

Sie können eine Standorteigenschaft als eine Geräteeigenschaft erstellen, die vom Gerät gemeldet wird. Angenommen, Sie möchten den Gerätestandort nachverfolgen:

1. Navigieren Sie zur Registerkarte **Eigenschaften**.

2. Klicken Sie in der Bibliothek auf **Geräteeigenschaft**.

3. Konfigurieren Sie den Anzeigenamen und den Feldnamen, und wählen Sie **Standort** als Datentyp:

    | Anzeigename  | Feldname | Datentyp |
    | --------------| -----------|-----------|
    | Gerätestandort | deviceLocation | location  |

   > [!NOTE]
   > Die Feldnamen müssen mit den Eigenschaftennamen im entsprechenden Gerätecode übereinstimmen.

   ![Formular „Geräteeigenschaften konfigurieren“ mit Details zum Standort](./media/howto-set-up-template/locationdeviceproperty2.png)

Wenn das echte Gerät verbunden wird, wird der als Geräteeigenschaft hinzugefügte Standort mit dem vom Gerät gesendeten Wert aktualisiert. Nachdem Sie nun Ihre Standorteigenschaft konfiguriert haben, können Sie [eine Karte hinzufügen, um den Standort auf dem Gerätedashboard zu visualisieren](#add-an-azure-maps-location-in-the-dashboard).

## <a name="commands"></a>Befehle

Befehle werden verwendet, um ein Gerät remote zu verwalten. Sie ermöglichen Operatoren, Befehle auf dem Gerät auszuführen. Sie können Ihrer Gerätevorlage mehrere Befehle hinzufügen, die auf der Registerkarte **Befehle** als Kacheln angezeigt und von Bedienern verwendet werden können. Als Gerätehersteller können Sie Befehle nach Ihren Wünschen flexibel definieren.

Wie unterscheidet sich ein Befehl von einer Einstellung?

* **Einstellung**: Eine Einstellung ist eine Konfiguration, die Sie auf ein Gerät anwenden möchten. Sie möchten, dass das Gerät die Konfiguration beibehält, bis Sie sie ändern. Angenommen, Sie möchten die Temperatur Ihres Gefrierschranks einstellen, und Sie wünschen diese Einstellung auch dann, wenn der Gefrierschrank neu gestartet wird.

* **Befehl**: Sie verwenden Befehle, um einen Befehl von IoT Central aus auf dem Gerät sofort remote auszuführen. Wenn ein Gerät nicht angeschlossen ist, wird der Befehl mit einem Fehler abgebrochen. Angenommen, Sie möchten ein Gerät neu starten.

Sie können z. B. einen neuen **Echo**-Befehl hinzufügen, indem Sie die Registerkarte **Befehle** auswählen, **+ Neuer Befehl** auswählen und die neuen Befehlsdetails eingeben:

| Anzeigename  | Feldname | Standardzeitlimit | Datentyp |
| --------------| -----------|---------------- | --------- |
| Echo-Befehl  | Echo       |  30             | text      |

![Formular „Befehl konfigurieren“ mit Details zu „Echo“](./media/howto-set-up-template/commandsecho1.png)

Nach der Auswahl von **Speichern** wird der Befehl **Echo** als Kachel angezeigt und kann vom **Device Explorer** verwendet werden, sobald Ihr echtes Gerät verbunden ist. Die Feldnamen Ihres Befehls müssen mit den Eigenschaftennamen im entsprechenden Gerätecode übereinstimmen, damit die Befehle erfolgreich ausgeführt werden können.

## <a name="rules"></a>Regeln

Anhand von Regeln können Operatoren Geräte nahezu in Echtzeit überwachen. Regeln rufen automatisch Aktionen auf. Beispielsweise kann bei Auslösen der Regel eine E-Mail gesendet werden. Zurzeit ist ein Regeltyp verfügbar:

- Eine **Telemetrieregel** wird ausgelöst, wenn die ausgewählten Gerätetelemetriedaten einen angegebenen Schwellenwert überschreiten. [Weitere Informationen zu Telemetrieregeln](howto-create-telemetry-rules.md).

## <a name="dashboard"></a>Dashboard

Im Dashboard findet ein Operator Informationen zu einem Gerät. Als Hersteller können Sie dieser Seite Kacheln hinzufügen, mit deren Hilfe Bediener das Verhalten des Geräts nachvollziehen können. Sie können Ihrer Gerätevorlage mehrere Dashboardkacheln hinzufügen. Sie können verschiedene Arten von Dashboardkacheln hinzufügen. Hierzu zählen unter anderem Bild, Liniendiagramm, Balkendiagramm, Key Performance Indicator (KPI), Einstellungen und Eigenschaften sowie Bezeichnung.

Beispielsweise können Sie eine Kachel **Einstellungen und Eigenschaften** hinzufügen, um eine Auswahl der aktuellen Werte für Einstellungen und Eigenschaften anzuzeigen, indem Sie die Registerkarte **Dashboard** und die Kachel aus der Bibliothek auswählen:

![Formular „Gerätedetails konfigurieren“ mit Details zu Einstellungen und Eigenschaften](./media/howto-set-up-template/dashboardsettingsandpropertiesform1.png)

Wenn jetzt Bediener das Dashboard im **Device Explorer** anzeigen, können sie die Kachel sehen.

### <a name="add-an-azure-maps-location-in-the-dashboard"></a>Hinzufügen eines Azure Maps-Standorts im Dashboard

Wenn Sie eine Standorteigenschaft konfiguriert haben, können Sie den Standort mithilfe einer Karte im Dashboard Ihres Geräts visualisieren.

1. Navigieren Sie zur **Dashboard**-Registerkarte.

1. Wählen Sie auf dem Gerätedashboard in der Bibliothek **Karte** aus.

1. Geben Sie der Karte einen Titel. Das folgende Beispiel hat den Titel **Installationsstandort**. Wählen Sie dann die Standorteigenschaft aus, die Sie zuvor auf der Registerkarte **Eigenschaften** konfiguriert haben. Im folgenden Beispiel wird **Installationsadresse** ausgewählt.

   ![Formular „Karte konfigurieren“ mit Details zu Titel und Eigenschaften](./media/howto-set-up-template/locationcloudproperty5map.png)

4. Wählen Sie **Speichern** aus. Die Kartenkachel zeigt jetzt den Standort an, den Sie ausgewählt haben.

Sie können die Karte in die gewünschte Größe ändern. Wenn ein Bediener nun das Dashboard im **Device Explorer** anzeigt, kann dieser alle von Ihnen konfigurierten Dashboardkacheln einsehen, einschließlich einer Standortkarte.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie erfahren haben, wie eine Gerätevorlage in Ihrer Azure IoT Central-Anwendung eingerichtet wird, wird als Nächstes Folgendes empfohlen:

> [!div class="nextstepaction"]
> [Erstellen einer neuen Gerätevorlagenversion](howto-version-devicetemplate.md)
> [Herstellen einer Verbindung zwischen einem MXChip IoT DevKit-Gerät und Ihrer Azure IoT Central-Anwendung](howto-connect-devkit.md)
> [Verbinden einer generischen Clientanwendung mit Ihrer Azure IoT Central-Anwendung (Node.js)](howto-connect-nodejs.md)