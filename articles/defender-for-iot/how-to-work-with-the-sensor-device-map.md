---
title: Arbeiten mit der Sensorgerätezuordnung
description: Die Geräteübersicht bietet eine grafische Darstellung erkannter Netzwerkgeräte. Mithilfe der Übersicht können Sie Geräteinformationen und Netzwerksegmente analysieren und verwalten sowie Berichte generieren.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/7/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 5b7059129c45149c64bc7fc145c68d9e09a7c046
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523837"
---
# <a name="investigate-sensor-detections-in-the-device-map"></a>Untersuchen von Sensorerkennungen in der Geräteübersicht

Die Geräteübersicht bietet eine grafische Darstellung erkannter Netzwerkgeräte. Nutzen Sie die Übersicht für folgende Aufgaben:

  - Abrufen, Analysieren und Verwalten von Geräteinformationen

  - Analysieren von Netzwerksegmenten, z. B. bestimmter Gruppen von Interesse oder Purdue-Ebenen

  - Generieren von Berichten, z. B. durch Exportieren von Gerätedetails und -zusammenfassungen

:::image type="content" source="media/how-to-work-with-maps/device-map-v2.png" alt-text="Screenshot der Geräteübersicht.":::

So greifen Sie auf die Übersicht zu

  - Wählen Sie im Hauptbereich der Konsole **Device Map** (Geräteübersicht) aus.

## <a name="map-search-and-layout-tools"></a>Such- und Layouttools für die Übersicht

Die folgenden Tools dienen zum Arbeiten in der Übersicht.

Die Benutzerrolle bestimmt, welche Tools im Fenster „Geräteübersicht“ verfügbar sind. Einzelheiten zu Benutzerrollen finden Sie unter [Erstellen und Verwalten von Benutzern](how-to-create-and-manage-users.md).

| Symbol | BESCHREIBUNG |
|---|---|
| :::image type="icon" source="media/how-to-work-with-maps/search-bar-icon-v2.png" border="false":::| Dient zum Suchen nach der IP- oder der MAC-Adresse eines bestimmtes Geräts. Geben Sie die IP- oder MAC-Adresse in das Textfeld ein. In der Übersicht wird das gesuchte Gerät mit daran angeschlossenen Geräten angezeigt. |
| Gruppenhervorhebung und -filter <br /> :::image type="content" source="media/how-to-work-with-maps/group-highlight-and-filters-v2.png" alt-text="Screenshot der Gruppenhervorhebungen und -filter"::: | Sie können die Übersicht auf Grundlage standardmäßiger und benutzerdefinierter Gerätegruppen filtern oder hervorheben. |
| :::image type="icon" source="media/how-to-work-with-maps/collapse-view-icon.png" border="false"::: | Zuklappen der IT-Ansicht, um eine auf OT-Geräte fokussierte Ansicht zu ermöglichen und IT-Geräte zu gruppieren.  |
| :::image type="icon" source="media/how-to-work-with-maps/device-management-icon.png" border="false"::: | Dient zum Beibehalten der aktuellen Geräteanordnung in der Übersicht. Wenn Sie z. B. Geräte an neue Positionen in der Übersicht ziehen, verbleiben die Geräte beim Beenden der Übersicht an diesen Positionen. |
| :::image type="icon" source="media/how-to-work-with-maps/fit-to-screen-icon.png" border="false"::: | An Bildschirm anpassen |
| :::image type="icon" source="media/how-to-work-with-maps/layer-icon.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/layouts-icon-v2.png" border="false"::: | Anzeigen der für dieses Gerät identifizierte Purdue-Ebene, einschließlich „Automatisch“, „Prozesssteuerung“, „Aufsicht“ und „Unternehmen“ <br /> Anzeigen von Verbindungen zwischen Geräten|
| :::image type="icon" source="media/how-to-work-with-maps/broadcast-icon.png" border="false"::: | Anzeigen oder Ausblenden zwischen Broadcast und Multicast |
| :::image type="icon" source="media/how-to-work-with-maps/time-icon.png" border="false"::: | Filtern der Geräte in der Übersicht nach der Uhrzeit, zu der sie zuletzt mit anderen Geräten kommuniziert haben |
| :::image type="icon" source="media/how-to-work-with-maps/notifications-icon.png" alt-text="Benachrichtigungen" border="false"::: | Anzeigen von Benachrichtigungen zu einem Gerät Wenn beispielsweise eine neue IP-Adresse für ein Gerät erkannt wurde, das eine vorhandene MAC-Adresse verwendet |
| :::image type="icon" source="media/how-to-work-with-maps/export-import.png" alt-text="Export" border="false"::: | Exportieren/Importieren von Geräteinformationen |
| :::image type="icon" source="media/how-to-work-with-maps/properties-icon.png" alt-text="properties" border="false"::: | Anzeigen grundlegender Geräteeigenschaften für ausgewählte Geräte |
| :::image type="icon" source="media/how-to-work-with-maps/zoom-in-icon-v2.png" alt-text="Vergrößern" border="false"::: oder :::image type="icon" source="media/how-to-work-with-maps/zoom-out-icon-v2.png" alt-text="Verkleinern" border="false"::: | Vergrößern oder Verkleinern von Geräten in der Übersicht |

## <a name="view-ot-elements-only"></a>Ausschließliches Anzeigen von OT-Elementen

Standardmäßig werden IT-Geräte automatisch nach Subnetz aggregiert, sodass die Ansicht der Übersicht auf OT- und ICS-Netze fokussiert ist. Die Darstellung der IT-Netzwerkelemente ist auf ein Minimum reduziert, wodurch die Gesamtanzahl der Geräte in der Übersicht reduziert wird und ein klares Bild der OT- und ICS-Netzwerkelemente entsteht.

Jedes Subnetz wird in der Geräteübersicht als einzelne Entität dargestellt, einschließlich einer interaktiven Funktion zum Auf- und Zuklappen, die das Untersuchen der Details eines IT-Subnetzes und die Rückkehr ermöglicht.

Die folgende Abbildung zeigt ein zugeklapptes IT-Subnetz mit 27 IT-Netzwerkelementen.

:::image type="content" source="media/how-to-work-with-maps/shrunk-it-subnet-v2.png" alt-text="Zugeklapptes IT-Subnetz mit 27 IT-Netzwerkelementen":::

So aktivieren Sie die Zuklappfunktion für IT-Netzwerke

- Vergewissern Sie sich im Fenster **Systemeinstellungen**, dass die Funktion „IT-Netzwerkgruppierung umschalten“ aktiviert ist.

:::image type="content" source="media/how-to-work-with-maps/shrunk-it-subnet-v2.png" alt-text="Fenster „Systemeinstellung“":::

So klappen Sie ein IT-Subnetz auf

1. Um zwischen IT- und OT-Netzwerken zu unterscheiden, wählen Sie auf dem Bildschirm „Systemeinstellungen“ die Option **Subnetze** aus.

   > [!NOTE]
   > Es wird empfohlen, jedes Subnetz mit einem aussagekräftigen Namen zu versehen, aufgrund dessen Benutzer IT- und OT-Netzwerken ohne Mühe unterscheiden können.

   :::image type="content" source="media/how-to-work-with-maps/subnet-list.png" alt-text="Subnetzkonfiguration":::

2. Deaktivieren Sie im Fenster **Edit Subnets Configuration** (Konfiguration der Subnetze bearbeiten) das Kontrollkästchen **ICS-Subnetz** für jedes Subnetz, das Sie als IT-Subnetz definieren möchten. Die IT-Subnetze werden in der Geräteübersicht mit den Benachrichtigungen für ICS-Geräte, wie z. B. Controller oder SPS, in IT-Netzwerken zugeklappt dargestellt.

   :::image type="content" source="media/how-to-work-with-maps/edit-config.png" alt-text="Konfiguration der Subnetze bearbeiten":::

3. Um das IT-Netzwerk in der Übersicht zu erweitern, klicken Sie im Fenster „Geräte“ mit der rechten Maustaste darauf, und wählen Sie **Netzwerk aufklappen** aus.

   :::image type="content" source="media/how-to-work-with-maps/expand-network.png" alt-text="Aufklappen der Ansicht Ihres Netzwerks.":::

4. Ein Bestätigungsfeld wird mit dem Hinweis angezeigt, dass die Layoutänderung nicht mehr rückgängig gemacht werden kann.

5. Klicken Sie auf **OK**. Die IT-Subnetzelemente werden in der Übersicht angezeigt.

   :::image type="content" source="media/how-to-work-with-maps/fixed-map.png" alt-text="OK":::

So klappen Sie ein IT-Subnetz zu

1.  Wählen Sie im linken Bereich die Option **Geräte** aus.

2. Wählen Sie im Fenster „Geräte“ das Zuklappsymbol aus. Die Zahl in Rot gibt an, wie viele erweiterte IT-Subnetze derzeit in der Übersicht angezeigt werden.

   :::image type="content" source="media/how-to-work-with-maps/devices-notifications.png" alt-text="Gerätefenster":::

3. Wählen Sie die Subnetze aus, die Sie zuklappen möchten, oder wählen Sie **Alle zuklappen** aus. Das ausgewählte Subnetz wird in der Übersicht zugeklappt angezeigt.

   :::image type="content" source="media/how-to-work-with-maps/close-all-subnets.png" alt-text="Alle reduzieren":::

Das Zuklappsymbol wird mit der aktualisierten Anzahl der aufgeklappten IT-Subnetze aktualisiert.

## <a name="view-or-highlight-device-groups"></a>Anzeigen oder Hervorheben von Gerätegruppen

Sie können die Anzeige der Übersicht auf Grundlage von Gerätegruppen anpassen. Ein Beispiel sind Gruppen von Geräten, die einem bestimmten OT-Protokoll, VLAN oder Subnetz zugeordnet sind. Vordefinierte Gruppen sind verfügbar, benutzerdefinierte Gruppen können erstellt werden.

Zeigen Sie Gruppen durch Ausführen der folgenden Aufgaben an:

  - **Hervorheben**: Heben Sie die zu einer bestimmten Gruppe gehörenden Geräte blau hervor.

  - **Filtern**: Zeigen Sie nur die zu einer bestimmten Gruppe gehörenden Geräte an.

:::image type="content" source="media/how-to-work-with-maps/port-standard.png" alt-text="Standardansicht Ihres Ports":::

Die folgenden vordefinierten Gruppen sind verfügbar:

| Gruppenname | BESCHREIBUNG |
|--|--|
| **Bekannte Anwendungen** | Geräte, die reservierte Ports verwenden, wie z. B. TCP.  |
| **Nicht standardmäßige Ports (Standard)** | Geräte, die nicht standardmäßige Ports oder Ports verwenden, denen kein Alias zugewiesen wurde. |
| **OT-Protokolle (Standard)** | Geräte, die den bekannten OT-Datenverkehr verarbeiten. |
| **Autorisierung (Standard)** | Geräte, die während des Lernprozesses im Netzwerk ermittelt oder offiziell im Netzwerk autorisiert wurden. |
| **Filter für den Gerätebestand** | Geräte, die gemäß den Filtern gruppiert sind, werden in der Tabelle „Gerätebestand“ gespeichert. |
| **Abrufintervalle** | Geräte, die nach Abrufintervallen gruppiert sind. Abrufintervalle werden automatisch anhand von zyklischen Kanälen bzw. Zeiträumen generiert. Beispiel: 15,0 Sekunden, 3,0 Sekunden, 1,5 Sekunden oder beliebiges Intervall. Durch Überprüfung dieser Informationen können Sie feststellen, ob Systeme zu schnell oder zu langsam abrufen. |
| **Programmieren** | Engineeringstationen und Programmiercomputer. |
| **Subnetze** | Geräte, die zu einem bestimmten Subnetz gehören. |
| **VLAN** | Geräte, die einer bestimmten VLAN-ID zugeordnet sind. |
| **Subnetzübergreifende Verbindungen** | Geräte, die von einem Subnetz mit einem anderen Subnetz kommunizieren. |
| **Angeheftete Warnungen** | Geräte, für die der Benutzer eine Warnung angeheftet hat |
| **Angriffsvektorsimulationen** | Gefährdete Geräte, die in Berichten zu Angriffsvektoren erkannt wurden. Um diese Geräte in der Übersicht anzuzeigen, aktivieren Sie das Kontrollkästchen **Display on Device Map** (In Geräteübersicht anzeigen), wenn der Angriffsvektor generiert wird. :::image type="content" source="media/how-to-work-with-maps/add-attack-v2.png" alt-text="Hinzufügen von Angriffsvektorsimulationen":::. |
| **Zuletzt gesehen** | Geräte, gruppiert nach dem Zeitrahmen, in dem sie zuletzt erkannt wurden, z. B.: eine Stunde, sechs Stunden, ein Tag, sieben Tage. |
| **Nicht in Active Directory** | Alle Nicht-SPS-Geräte, die nicht mit Active Directory kommunizieren. |

So lassen sich Geräte hervorheben oder filtern

1. Wählen Sie im seitlichen Menü **Device Map** (Geräteübersicht) aus.

2. Wählen Sie das Filtersymbol aus. :::image type="content" source="media/how-to-work-with-maps/menu-icon.png" alt-text="Menü":::

3. Wählen Sie im Bereich „Gruppen“ die Gruppe aus, die Sie hervorheben oder deren Geräte Sie filtern möchten.

4. Wählen Sie **Hervorheben** oder **Filtern** aus. Schalten Sie dieselbe Auswahl ein, um die Hervorhebung oder den Filter zu entfernen.

## <a name="define-custom-groups"></a>Definieren benutzerdefinierter Gruppen

Neben der Ansicht vordefinierter Gruppen können Sie auch eigene Gruppen definieren. Die Gruppen werden in den Berichten „Geräteübersicht“, „Gerätebestand“ und „Data Mining“ angezeigt.

> [!NOTE]
> Sie können auch Gruppen anhand des Gerätebestands erstellen.

So erstellen Sie eine Gruppe:

1. Wählen Sie im seitlichen Menü **Geräte** aus. Die Geräteübersicht wird angezeigt.

1. Wählen Sie :::image type="content" source="media/how-to-work-with-maps/menu-icon.png" alt-text="Gruppeneinstellung"::: aus, um die Gruppeneinstellungen anzuzeigen.

1. Wählen Sie :::image type="content" source="media/how-to-work-with-maps/create-group-v2.png" alt-text="Gruppen"::: aus, um eine neue benutzerdefinierte Gruppe zu erstellen.

:::image type="content" source="media/how-to-work-with-maps/custom-group-v2.png" alt-text="Bildschirm zum Erstellen einer benutzerdefinierten Gruppe":::

1. Fügen Sie den Namen der Gruppe mit maximal 30 Zeichen hinzu.

1. Wählen Sie die relevanten Geräte wie folgt aus:

   - Fügen Sie die Geräte aus diesem Menü hinzu, indem Sie sie in der Liste auswählen (auf die Pfeilschaltfläche klicken).<br /> Oder <br /> 
   - Fügen Sie die Geräte aus diesem Menü hinzu, indem Sie sie aus einer ausgewählten Gruppe kopieren (mit der Pfeilschaltfläche auswählen).

1. Wählen Sie **Gruppe hinzufügen** aus, um vorhandene Gruppen zu benutzerdefinierten Gruppen hinzuzufügen.

### <a name="add-devices-to-a-custom-group"></a>Hinzufügen von Geräten zu einer benutzerdefinierten Gruppe

Sie können Geräte einer benutzerdefinierten Gruppe hinzufügen oder eine neue benutzerdefinierte Gruppe und das Gerät erstellen.

1. Klicken Sie in der Übersicht mit der rechten Maustaste auf ein oder mehrere Geräte.

1. Wählen Sie **Zu Gruppe hinzufügen** aus.

1. Geben Sie im Feld „Gruppe“ einen Gruppennamen ein und wählen Sie „+“ aus. Eine neue Gruppe wird angezeigt. Wenn die Gruppe bereits vorhanden ist, wird Sie der vorhandenen benutzerdefinierten Gruppe hinzugefügt.

   :::image type="content" source="media/how-to-work-with-maps/groups-section-v2.png" alt-text="Gruppenname":::

1. Fügen Sie einer Gruppe Geräte hinzu, indem Sie die Schritte 1-3 wiederholen.

## <a name="map-zoom-views"></a>Zoomansichten der Übersicht

Das Arbeiten mit Übersichtsansichten hilft, die Forensik bei der Analyse großer Netzwerke zu beschleunigen.

Drei Gerätedetailansichten sind möglich:

  - [Vogelperspektive](#birds-eye-view)

  - [Ansicht der Gerätetypen und -verbindungen](#device-type-and-connection-view)

  - [Detailansicht](#detailed-view)

### <a name="birds-eye-view"></a>Vogelperspektive

Diese Ansicht bietet eine Übersicht über die Geräte, die wie folgt gekennzeichnet sind:

  - Rote Punkte für Geräte mit Warnungen

  - Punkte in Sternform für als wichtig markierte Geräte

  - Schwarze Punkte für Geräte ohne Warnungen.

:::image type="content" source="media/how-to-work-with-maps/colored-dots-v2.png" alt-text="Vogelperspektive":::

### <a name="device-type-and-connection-view"></a>Ansicht der Gerätetypen und -verbindungen 

In dieser Ansicht werden Geräte in der Übersicht als Symbole dargestellt, um Geräte mit Warnungen, Gerätetypen und verbundene Geräte hervorzuheben.

  - Geräte mit Warnungen werden mit einem roten Ring gezeigt.

  - Geräte ohne Warnungen werden mit einem grauen Ring gezeigt.

  - Als Stern angezeigte Geräte wurden als wichtig markiert.

Das Gerätetypsymbol wird mit verbundenen Geräten gezeigt.

:::image type="content" source="media/how-to-work-with-maps/colored-rings.png" alt-text="Verbindungsansicht":::

### <a name="detailed-view"></a>Detailansicht 

Die Detailansicht zeigt Geräte und Gerätebezeichnungen und -indikatoren mit den folgenden Informationen:

:::image type="content" source="media/how-to-work-with-maps/device-map-v2.png" alt-text="Detailansicht":::

### <a name="control-the-zoom-view"></a>Steuern der Zoomansicht

Die Übersichtsansicht hängt von der Zoomstufe der Übersicht ab. Der Wechsel zwischen den Ansichten der Übersicht erfolgt durch Ändern der Zoomstufen.

:::image type="content" source="media/how-to-work-with-maps/zoom-in-out.png" alt-text="Steuern der Zoomansicht":::

### <a name="enable-simplified-zoom-views"></a>Aktivieren vereinfachter Zoomansichten

Administratoren, die Sicherheitsanalysten und Benutzern mit Leseberechtigung den Zugriff auf die Vogelperspektive und die Ansicht der Gerätetypen und -verbindungen ermöglichen möchten, sollten die Option „Vereinfachte Ansicht“ aktivieren.

So aktivieren Sie vereinfachte Ansichten der Übersicht

  - Wählen Sie **Systemeinstellungen** aus, und schalten Sie dann auf die Option **Simplified Map View** (Vereinfachte Ansicht der Übersicht) um.

:::image type="content" source="media/how-to-work-with-maps/simplify-view-v2.png" alt-text="Vereinfachte Ansicht der Übersicht":::

## <a name="learn-more-about-devices"></a>Weitere Informationen zu Geräten

Es steht ein umfangreiches Spektrum an Tools zur Verfügung, um mehr über Geräte in der Geräteübersicht zu erfahren:

- [Gerätebezeichnungen und -indikatoren](#device-labels-and-indicators)

- [Schnellansichten von Geräten](#device-quick-views)

- [Anzeigen und Verwalten von Geräteeigenschaften](#view-and-manage-device-properties)

- [Anzeigen von Gerätetypen](#view-device-types)

- [Rückwandplatine](#backplane-properties)

- [Anzeigen einer Zeitskala der Ereignisse für das Gerät](#view-a-timeline-of-events-for-the-device)

- [Analysieren von Programmierdetails und -änderungen](#analyze-programming-details-and-changes)

### <a name="device-labels-and-indicators"></a>Gerätebezeichnungen und -indikatoren

Die folgenden Bezeichnungen und Indikatoren werden möglicherweise für Geräte in der Übersicht gezeigt:

| Gerätebezeichnung | BESCHREIBUNG |
|--|--|
| :::image type="content" source="media/how-to-work-with-maps/host-v2.png" alt-text="IP-Hostname"::: | IP-Adresse und Hostname und IP-Adresse bzw. Subnetzadressen |
| :::image type="content" source="media/how-to-work-with-maps/amount-alerts-v2.png" alt-text="Anzahl der Warnungen"::: | Anzahl der mit dem Gerät verbundenen Warnungen |
| :::image type="icon" source="media/how-to-work-with-maps/type-v2.png" border="false"::: | Symbol des Gerätetyps, z. B. Speicher, SPS oder Historian. |
| :::image type="content" source="media/how-to-work-with-maps/grouped-v2.png" alt-text="Gruppierte Geräte"::: | Anzahl der Geräte, die in einem Subnetz in einem IT-Netzwerk gruppiert sind. In diesem Beispiel 8. |
| :::image type="content" source="media/how-to-work-with-maps/not-authorized-v2.png" alt-text="Nach Lernphase erkanntes Geräts"::: | Ein Gerät, das nach der Lernphase erkannt und nicht als Netzwerkgerät autorisiert wurde. |
| Durchgezogene Linie | Logische Verbindung zwischen Geräten |
| :::image type="content" source="media/how-to-work-with-maps/new-v2.png" alt-text="Neues Gerät"::: | Neues Gerät, das nach Abschluss der Lernphase erkannt wurde. |

### <a name="device-quick-views"></a>Schnellansichten von Geräten

Greifen Sie in der Übersicht auf Geräteeigenschaften und -verbindungen zu.

So öffnen Sie das Menü „Quick Properties“ (Schnelleigenschaften) zu

  - Wählen Sie das Menü „Quick Properties“ (Schnelleigenschaften) :::image type="content" source="media/how-to-work-with-maps/properties.png" alt-text="„Quick Properties“ (Schnelleigenschaften)"::: aus.

#### <a name="quick-device-properties"></a>Schnellgeräteeigenschaften

Wählen Sie ein oder mehrere Geräte aus, während der Bildschirm „Quick Properties“ (Schnelleigenschaften) geöffnet ist, um die Besonderheiten dieser Geräte anzuzeigen:

:::image type="content" source="media/how-to-work-with-maps/device-information.png" alt-text="Schnellgeräteeigenschaften":::

#### <a name="quick-connection-properties"></a>Schnellverbindungseigenschaften

Wählen Sie eine Verbindung aus, während der Bildschirm „Quick Properties“ (Schnelleigenschaften) geöffnet ist, um die Protokolle anzuzeigen, die in dieser Verbindung verwendet werden und wann sie zuletzt erkannt wurden:

:::image type="content" source="media/how-to-work-with-maps/connection-details-v2.png" alt-text="Schnellverbindungseigenschaften":::

## <a name="view-and-manage-device-properties"></a>Anzeigen und Verwalten von Geräteeigenschaften

Sie können Geräteeigenschaften für jedes in der Übersicht vorhandene Gerät anzeigen. Beispiel: Gerätename, Typ oder Betriebssystem, Firmware oder Hersteller.

:::image type="content" source="media/how-to-work-with-maps/device-properties-v2.png" alt-text="Anzeigen und Verwalten von Geräteeigenschaften":::

Die folgenden Informationen können manuell geändert werden. Manuell eingegebene Informationen überschreiben Informationen, die von Defender für IoT erkannt werden.

  - Name

  - type

  - OS

  - Purdue-Ebene

  - BESCHREIBUNG

| Element | BESCHREIBUNG |
|--|--|
| Basic Information | Die benötigten grundlegenden Informationen. |
| Name | Den Gerätenamen. <br /> Standardmäßig erkennt der Sensor den Gerätenamen entsprechend seiner Festlegung im Netzwerk. Beispielsweise ein Name, der im DNS-Server festgelegt ist. <br /> Wenn keine derartigen Namen festgelegt wurden, wird in diesem Feld die IP-Adresse des Geräts angezeigt. <br /> Sie können einen Gerätenamen manuell ändern. Geben Sie Ihren Geräten aussagekräftige Namen, die ihre Funktionalität widerspiegeln. |
| type | Der vom Sensor erkannte Gerätetyp. <br /> Weitere Informationen finden Sie unter [Anzeigen von Gerätetypen](#view-device-types). |
| Hersteller | Der Gerätehersteller. Dies wird durch die führenden Zeichen der MAC-Adresse des Geräts bestimmt. Dieses Feld ist schreibgeschützt. |
| Betriebssystem | Das vom Sensor erkannte Betriebssystem des Geräts. |
| Purdue-Ebene | Die vom Sensor für dieses Gerät erkannte Purdue-Ebene wie z. B.: <br /> Automatisch <br /> Prozesssteuerung <br /> Aufsicht <br /> – Enterprise |
| BESCHREIBUNG | Ein Feld für die freie Texteingabe. <br /> Fügen Sie weitere Informationen zum Gerät hinzu. |
| Attributes | Alle zusätzlichen Informationen, die in der Lernphase über das Gerät entdeckt wurden und nicht zu anderen Kategorien gehören, werden im Abschnitt „Attribute“ angezeigt. <br /> Diese Informationen sind schreibgeschützt. |
| Einstellungen | Sie können Geräteeinstellungen manuell ändern, um falsch positive Ergebnisse zu vermeiden: <br /> - **Autorisiertes Gerät**: In der Lernphase werden alle im Netzwerk erkannten Geräte als autorisierte Geräte identifiziert. Wenn ein Gerät nach der Lernphase erkannt wird, wird es standardmäßig als nicht autorisiertes Gerät ausgewiesen. Sie können diese Festlegung manuell ändern. <br /> - **Als Scanner bekannt**: Aktivieren Sie diese Option, wenn Sie wissen, dass dieses Gerät als Scanner bekannt ist und keine Notwendigkeit besteht, Sie davor zu warnen. <br /> - **Programmiergerät**: Aktivieren Sie diese Option, wenn Sie wissen, dass dieses Gerät als Programmiergerät bekannt ist und zur Vornahme von Programmierungsänderungen verwendet wird. Wenn Sie es als Programmiergerät identifizieren, werden Warnungen wegen Programmierungsänderungen verhindert, die von dieser Ressource stammen. |
| Benutzerdefinierte Gruppen | Die benutzerdefinierten Gruppen in der Geräteübersicht, zu denen dieses Gerät gehört. |
| State | Der Sicherheits- und Autorisierungsstatus des Geräts: <br /> Der Status ist `Secured`, wenn keine Warnungen vorhanden sind. <br /> Wenn Warnungen zum Gerät vorliegen, wird die Anzahl der Warnungen angezeigt. <br /> Der Status `Unauthorized` wird für Geräte angezeigt, die nach der Lernphase dem Netzwerk hinzugefügt wurden. Sie können das Gerät in den Einstellungen manuell als `Authorized Device` festlegen. <br /> Für den Fall, dass die Adresse dieses Geräts als dynamische Adresse festgelegt ist, wird `DHCP` dem Status hinzugefügt. |


| Netzwerk | BESCHREIBUNG |
|--|--|
| Schnittstellen | Die Geräteschnittstellen. Ein schreibgeschütztes Feld. |
| Protokolle | Die vom Gerät verwendeten Protokolle. Ein schreibgeschütztes Feld. |
| Firmware | Wenn Informationen zur Rückwandplatine verfügbar sind, werden keine Firmwareinformationen angezeigt. |
| Adresse | Die IP-Adresse des Geräts. |
| Seriell | Die Seriennummer des Geräts. |
| Moduladresse | Gerätemodell und Einschubfachnummer oder -ID. |
| Modell | Die Gerätemodellnummer. |
| Firmware Version | Die Firmwareversionsnummer. |

So zeigen Sie Geräteinformationen an

1. Wählen Sie im seitlichen Menü **Geräte** aus.

2. Klicken Sie mit der rechten Maustaste auf ein Gerät, und wählen Sie **Eigenschaften anzeigen** aus. Das Fenster „Geräteeigenschaften“ wird angezeigt.

3. Wählen Sie die gewünschte Warnung am unteren Rand des Fensters aus, um detaillierte Informationen zu den Warnungen für dieses Gerät anzuzeigen.

### <a name="view-device-types"></a>Anzeigen von Gerätetypen

Der Gerätetyp wird vom Sensor während des Geräteerkennungsprozesses automatisch erkannt. Sie können den Typ manuell ändern.

:::image type="content" source="media/how-to-work-with-maps/type-of-device.png" alt-text="Anzeigen von Gerätetypen":::

In der folgenden Tabelle sind alle Typen im System aufgeführt:

| Category | Gerätetyp |
|--|--|
| ICS | Engineeringstation <br /> SPS <br />Historian <br />HMI <br />IED <br />DCS-Controller <br />RTU <br />Industrielle Packanlage <br />Industrieller Maßstab <br />Industrieroboter <br />Slot <br />Zähler <br />Frequenzumrichter  <br />Robotersteuerung <br />Servoantrieb <br />Pneumatisches Gerät <br />Marquee |
| IT | Domänencontroller <br />Datenbankserver <br />Arbeitsstation <br />Server <br />Endstation <br />Storage <br />Smart Phone <br />Tablet <br />Sicherungsserver |
| IoT | IP-Kamera <br />Drucker  <br />Stechuhr <br />ATM <br />Smart TV <br />Spielkonsole <br />DVR <br />Türbedienfeld <br />Heizung, Lüftung, Klimatechnik <br />Thermostat <br />Feuermelder <br />Intelligentes Lichtsystem <br />Intelligenter Zwischenstecker <br />Brandmelder <br />IP-Telefon <br />Alarmanlage <br />Alarmsirene <br />Motion Detector <br />Aufzug <br />Feuchtigkeitssensor <br />Barcodescanner <br />Unterbrechungsfreie Stromversorgung <br />Personenzählsystem <br />Intercom <br />Drehkreuz |
| Netzwerk | Drahtloser Zugriffspunkt <br />Router <br />Schalter <br />Firewall <br />VPN Gateway <br />NTP Server <br />WLAN-Ananas <br />Physischer Standort <br />E/A-Adapter <br /> Protokollkonverter |

So zeigen Sie Geräteinformationen an

1.  Wählen Sie im seitlichen Menü **Geräte** aus.

2. Klicken Sie mit der rechten Maustaste auf ein Gerät, und wählen Sie **Eigenschaften anzeigen** aus. Das Fenster „Geräteeigenschaften“ wird angezeigt.

3. Wählen Sie die gewünschte Warnung aus, um detaillierte Informationen zu den Warnungen für dieses Gerät anzuzeigen.

### <a name="backplane-properties"></a>Eigenschaften der Rückwandplatine

Wenn eine SPS mehrere Module enthält, die in Racks und Einschübe aufgeteilt sind, können die Eigenschaften je nach Modulkarte unterschiedlich sein. Wenn z. B. die IP- und MAC-Adresse gleich sind, kann die Firmware unterschiedlich sein.

Sie können die Option „Backplane“ (Rückwandplatine) verwenden, um mehrere Steuerungen/Karten und deren geschachtelte Geräte als eine Entität mit einer Vielzahl von Definitionen zu betrachten. Jeder Einschub in der Ansicht „Rückwandplatine“ stellt die zugrunde liegenden Geräte dar, also die Geräte, die dahinter erkannt wurden.

:::image type="content" source="media/how-to-work-with-maps/backplane-image-v2.png" alt-text="Eigenschaften der Rückwandplatine":::

:::image type="content" source="media/how-to-work-with-maps/backplane-details-v2.png" alt-text="Geräteeigenschaften der Rückwandplatine":::

Eine Rückwandplatine kann bis zu 30 Steuerungskarten und bis zu 30 Rackeinheiten enthalten. Die Gesamtanzahl der Geräte, die auf den einzelnen Ebenen enthalten sind, kann bis zu 200 betragen.

Der Bereich „Rückwandplatine“ wird im Fenster „Geräteeigenschaften“ angezeigt, wenn Details der Rückwandplatine erkannt werden.

Jeder Einschub wird mit der Anzahl der zugrunde liegenden Geräte und dem Symbol angezeigt, das den Modultyp angibt.

| Symbol | Modultyp |
|--|--|
| :::image type="content" source="media/how-to-work-with-maps/power.png" alt-text="Stromversorgung"::: | Stromversorgung |
| :::image type="content" source="media/how-to-work-with-maps/analog.png" alt-text="Analog-E/A"::: | Analog-E/A |
| :::image type="content" source="media/how-to-work-with-maps/comms.png" alt-text="Kommunikationsadapter"::: | Kommunikationsadapter |
| :::image type="content" source="media/how-to-work-with-maps/digital.png" alt-text="Digital-E/A"::: | Digital-E/A |
| :::image type="content" source="media/how-to-work-with-maps/computer-processor.png" alt-text="CPU"::: | CPU |
| :::image type="content" source="media/how-to-work-with-maps/HMI-icon.png" alt-text="HMI"::: | HMI |
| :::image type="content" source="media/how-to-work-with-maps/average.png" alt-text="Allgemein"::: | Allgemein |

Wenn Sie einen Einschub auswählen, werden dessen Details angezeigt:

:::image type="content" source="media/how-to-work-with-maps/slot-selection-v2.png" alt-text="Auswählen eines Einschubs":::

Um die zugrunde liegenden Geräte hinter dem Einschub anzuzeigen, wählen Sie **VIEW ON MAP** (In Übersicht anzeigen) aus. Der Einschub wird in der Geräteübersicht mit allen zugrunde liegenden Modulen und daran angeschlossenen Geräten angezeigt.

:::image type="content" source="media/how-to-work-with-maps/map-appearance-v2.png" alt-text="In Übersicht anzeigen"::::

## <a name="view-a-timeline-of-events-for-the-device"></a>Anzeigen einer Zeitskala der Ereignisse für das Gerät

Zeigen Sie eine Zeitskala der mit dem Gerät verbundenen Ereignisse an.

So zeigen Sie die Zeitskala an

1. Klicken Sie in der Übersicht mit der rechten Maustaste auf ein Gerät.

2. Wählen Sie **Ereignisse anzeigen** aus. Das Fenster „Event Timeline“ (Zeitskala von Ereignissen) wird mit Informationen zu Ereignissen geöffnet, die für das ausgewählte Gerät erkannt wurden.

Weitere Informationen finden Sie unter [Zeitskala von Ereignissen](#event-timeline).

## <a name="analyze-programming-details-and-changes"></a>Analysieren von Programmierdetails und -änderungen

Verbessern Sie die Forensik, indem Sie Programmierereignisse anzeigen, die auf Ihren Geräten im Netzwerk erfolgen, und analysieren Sie Änderungen am Code. Diese Informationen helfen Ihnen beispielsweise beim Aufspüren verdächtiger Programmieraktivitäten:

  - Menschlicher Fehler: Ein Techniker programmiert das falsche Gerät.

  - Beschädigte Programmierautomatisierung: Die Programmierung erfolgt fälschlicherweise aufgrund eines Automatisierungsfehlers.

  - Gehackte Systeme: Unbefugte Benutzer haben sich an einem Programmiergerät angemeldet.

Sie können ein programmiertes Gerät anzeigen und durch verschiedene Programmieränderungen scrollen, die von anderen Geräten an diesem Gerät vorgenommen wurden.

Zeigen Sie Code an, der vom Programmiergerät hinzugefügt, geändert, entfernt oder neu geladen wurde. Suchen Sie nach Programmieränderungen auf Basis von Dateitypen, Datumsangaben oder Uhrzeiten von Interesse.

### <a name="when-to-review-programming-activity"></a>Zeitpunkt der Überprüfung der Programmieraktivität 

In folgenden Fällen müssen Sie die Programmieraktivität überprüfen:

  - Nach dem Anzeigen einer Warnung zu einer nicht autorisierten Programmierung

  - Nach einem geplanten Update für Steuerungen

  - Wenn ein Prozess oder Computer nicht ordnungsgemäß funktioniert (um zu sehen, wer das letzte Update wann vorgenommen hat)

:::image type="content" source="media/how-to-work-with-maps/differences.png" alt-text="Änderungsprotokoll der Programmierung":::

Andere Optionen sind:

  - Markieren von Ereignissen von Interesse mit einem Stern

  - Herunterladen einer TXT-Datei mit dem aktuellen Code

### <a name="about-authorized-vs-unauthorized-programming-events"></a>Informationen zu autorisierten und nicht autorisierten Programmierereignissen 

Nicht autorisierte Programmierungsereignisse werden von Geräten ausgeführt, die nicht als Programmiergeräte erkannt oder manuell festgelegt wurden. Autorisierte Programmierereignisse werden von Geräten ausgeführt, die als Programmiergeräte bestimmt oder manuell festgelegt wurden.

Im Fenster für die Programmieranalyse werden sowohl autorisierte als auch nicht autorisierte Programmierereignisse gezeigt.

### <a name="accessing-programming-details-and-changes"></a>Zugreifen auf Programmierdetails und -änderungen

Sie haben folgende Möglichkeiten des Zugriffs auf das Fenster für die Programmieranalyse:

- [Zeitskala der Ereignisse](#event-timeline)

- [Warnungen zu nicht autorisierter Programmierung](#unauthorized-programming-alerts)

### <a name="event-timeline"></a>Zeitskala der Ereignisse

Die Zeitskala der Ereignisse zeigt Ereignisse, bei denen Programmieränderungen erkannt wurden.

:::image type="content" source="media/how-to-work-with-maps/timeline.png" alt-text="Eine Ansicht der Zeitskala der Ereignisse.":::

### <a name="unauthorized-programming-alerts"></a>Warnungen zu nicht autorisierter Programmierung

Warnungen werden ausgelöst, wenn nicht autorisierte Programmiergeräte Programmieraktivitäten ausführen.

:::image type="content" source="media/how-to-work-with-maps/unauthorized.png" alt-text="Warnungen zu nicht autorisierter Programmierung":::

> [!NOTE]
> Sie können auch grundlegende Programmierinformationen in den Fenstern „Geräteeigenschaften“ und „Gerätebestand“ einsehen.

### <a name="working-in-the-programming-timeline-window"></a>Arbeiten im Fenster mit der Zeitskala der Programmierung

In diesem Abschnitt wird beschrieben, wie Sie Programmierdateien anzeigen und Versionen vergleichen. Suchen Sie nach bestimmten Dateien, die an ein programmiertes Gerät übertragen wurden. Suchen Sie Dateien basierend auf Folgendem:

  - Date

  - Dateityp

:::image type="content" source="media/how-to-work-with-maps/timeline-view.png" alt-text="Fenster mit der Zeitskala der Programmierung":::

|Typ der Zeitskala der Programmierung | BESCHREIBUNG |
|--|--|
| Programmiertes Gerät | Bietet Details zum Gerät, das programmiert wurde, einschließlich Hostname und Datei. |
| Aktuelle Ereignisse | Zeigt die 50 letzten Ereignisse an, die vom Sensor erkannt wurden. <br />Um ein Ereignis hervorzuheben, zeigen Sie darauf und klicken auf den Stern. :::image type="icon" source="media/how-to-work-with-maps/star.png" border="false"::: <br /> Die letzten 50 Ereignisse können eingesehen werden. |
| Dateien | Zeigt die für das gewählte Datum erkannten Dateien und die Dateigröße auf dem programmierten Gerät an. <br /> Standardmäßig können pro Gerät maximal 300 Dateien angezeigt werden. <br /> Standardmäßig ist die maximale Dateigröße pro Datei 15 MB. |
| Dateistatus :::image type="icon" source="media/how-to-work-with-maps/status-v2.png" border="false"::: | Dateibezeichnungen geben den Status der Datei auf dem Gerät an: <br /> **Hinzugefügt**: Die Datei wurde dem Endpunkt am ausgewählten Datum oder zur ausgewählten Uhrzeit hinzugefügt. <br /> **Aktualisiert**: Die Datei wurde am ausgewählten Datum oder zur ausgewählten Uhrzeit aktualisiert. <br /> **Gelöscht:** Diese Datei wurde entfernt. <br /> **Keine Bezeichnung**: Die Datei wurde nicht geändert.   |
| Programmiergerät | Das Gerät, das die Programmierung geändert hat. Möglicherweise haben mehrere Geräte Programmieränderungen an einem programmierten Gerät vorgenommen. Hostname, Datum oder Uhrzeit der Änderung und der angemeldete Benutzer werden angezeigt. |
| :::image type="icon" source="media/how-to-work-with-maps/current.png" border="false"::: | Zeigt die aktuelle Datei an, die auf dem programmierten Gerät installiert ist. |
| :::image type="icon" source="media/how-to-work-with-maps/download-text.png" border="false"::: | Dient zum Herunterladen einer Textdatei des angezeigten Codes. |
| :::image type="icon" source="media/how-to-work-with-maps/compare.png" border="false"::: | Vergleicht die aktuelle Datei mit der Datei, die an einem ausgewählten Datum erkannt wurde. |

### <a name="choose-a-file-to-review"></a>Auswählen einer zu überprüfenden Datei

In diesem Abschnitt wird beschrieben, wie Sie eine zu überprüfende Datei auswählen.

So wählen Sie eine zu überprüfende Datei

1. Wählen Sie im Bereich **Aktuelle Ereignisse** ein Ereignis aus.

2. Wählen Sie im Bereich „Datei“ eine Datei aus. Die Datei wird im Bereich „Aktuell“ angezeigt.

:::image type="content" source="media/how-to-work-with-maps/choose-file.png" alt-text="Wählen Sie die Datei aus, mit der Sie arbeiten möchten.":::

### <a name="compare-files"></a>Vergleichen von Dateien

In diesem Abschnitt wird beschrieben, wie Sie Programmierdateien vergleichen.

So erfolgt der Vergleich

1. Wählen Sie im Bereich „Aktuelle Ereignisse“ ein Ereignis aus.

2. Wählen Sie im Bereich „Datei“ eine Datei aus. Die Datei wird im Bereich „Aktuell“ angezeigt. Sie können diese Datei mit anderen Dateien vergleichen.

3. Wählen Sie den Vergleichsindikator aus.

   :::image type="content" source="media/how-to-work-with-maps/compare.png" alt-text="Vergleichsindikator":::

   Im Fenster wird jedes Datum angezeigt, an denen die ausgewählte Datei auf dem programmierten Gerät erkannt wurde. Möglicherweise wurde die Datei auf dem programmierten Gerät von mehreren Programmiergeräten aktualisiert.

   Die Anzahl der erkannten Unterschiede wird rechts oben im Fenster gezeigt. Sie müssen möglicherweise nach unten scrollen, um Unterschiede anzuzeigen.

   :::image type="content" source="media/how-to-work-with-maps/scroll.png" alt-text="Scrollen Sie nach unten zu Ihrer Auswahl.":::

   Der Wert errechnet sich aus benachbarten Zeilen mit geändertem Text. Wenn z. B. acht aufeinanderfolgende Codezeilen geändert (gelöscht, aktualisiert oder hinzugefügt) wurden, wird dies als ein Unterschied berechnet.

   :::image type="content" source="media/how-to-work-with-maps/program-timeline.png" alt-text="Ansicht der Zeitskala Ihrer Programmierung.":::

4. Wählen Sie ein Datum aus. Die am ausgewählten Datum erkannte Datei wird im Fenster angezeigt.

5. Die im Bereich „Aktuelle Ereignisse/Dateien“ ausgewählte Datei wird stets auf der rechten Seite angezeigt.

### <a name="device-programming-information-other-locations"></a>Informationen zur Geräteprogrammierung: Andere Orte

Zusätzlich zur Überprüfung von Details auf der Zeitskala der Programmierung können Sie in den Fenstern „Geräteeigenschaften“ und „Gerätebestand“ auf Programmierinformationen zugreifen.

| Gerätetyp | BESCHREIBUNG |
|--|--|
| Geräteeigenschaften | Das Fenster „Geräteeigenschaften“ gibt Auskunft über das letzte auf dem Gerät erkannte Programmierereignis. :::image type="content" source="media/how-to-work-with-maps/information-from-device-v2.png" alt-text="Die Eigenschaften Ihres Geräts"::: |
| Der Gerätebestand | Der Gerätebestand gibt an, ob das Gerät ein Programmiergerät ist. :::image type="content" source="media/how-to-work-with-maps/inventory-v2.png" alt-text="Der Bestand an Geräten"::: |

## <a name="manage-device-information-from-the-map"></a>Verwalten von Geräteinformationen in der Übersicht

Der Sensor aktualisiert oder beeinflusst Geräte nicht direkt im Netzwerk. Hier vorgenommene Änderungen wirken sich nur auf die Analyse des Geräts aus.

### <a name="delete-devices"></a>Löschen von Geräten

Möglicherweise möchten Sie ein Gerät löschen, wenn die gewonnenen Informationen nicht relevant sind. Beispiel:

  - Ein Vertragspartner an einer Engineeringstation verbindet sich vorübergehend mit dem Gerät, um die Konfiguration zu aktualisieren. Nach Abschluss der Aufgabe wird das Gerät entfernt.

  - Aufgrund von Änderungen im Netzwerk sind einige Geräte nicht mehr verbunden.

Wenn Sie das Gerät nicht löschen, wird es vom Sensor weiterhin überwacht. Nach 60 Tagen wird eine Benachrichtigung mit einer Löschempfehlung angezeigt.

Sie erhalten möglicherweise eine Warnung, dass das Gerät nicht reagiert, wenn ein anderes Gerät darauf zuzugreifen versucht. In diesem Fall ist das Netzwerk möglicherweise falsch konfiguriert.

Das Gerät wird aus den Berichten „Geräteübersicht“, „Gerätebestand“ und „Data Mining“ entfernt. Andere Informationen, z. B. in Widgets gespeicherte Informationen, bleiben erhalten.

Das Gerät muss mindestens 10 Minuten inaktiv sein, damit es gelöscht werden kann.

So löschen Sie ein Gerät aus der Geräteübersicht

1. Wählen Sie im seitlichen Menü **Geräte** aus.

2. Klicken Sie mit der rechten Maustaste auf ein Gerät, und wählen Sie **Löschen** aus.

### <a name="merge-devices"></a>Zusammenführen von Geräten

Unter bestimmten Umständen müssen Sie Geräte möglicherweise zusammenführen. Dies kann erforderlich sein, wenn der Sensor separate Netzwerkentitäten erkennt, die einem einzigen Gerät zugeordnet sind. Beispiel:

  - Eine SPS mit vier Netzwerkkarten.

  - Einen Laptop mit WLAN und physischer Netzwerkkarte.
  
  - Eine Arbeitsstation mit zwei oder mehr Netzwerkkarten.

Beim Zusammenführen weisen Sie den Sensor an, die Geräteeigenschaften von zwei Geräten zu einem Gerät zusammenzufassen. Wenn Sie dies tun, werden das Fenster „Geräteeigenschaften“ und die Sensorberichte mit den neuen Details zu den Geräteeigenschaften aktualisiert.

Wenn Sie z. B. zwei Geräte, jedes mit einer IP-Adresse, zusammenführen, werden beide IP-Adressen als separate Schnittstellen im Fenster „Geräteeigenschaften“ angezeigt. Sie können nur autorisierte Geräte zusammenführen.

:::image type="content" source="media/how-to-work-with-maps/device-properties-v2.png" alt-text="Fenster „Geräteeigenschaften“":::

Auf der Zeitskala der Ereignisse ist das Zusammenführungsereignis zu sehen.

:::image type="content" source="media/how-to-work-with-maps/events-time.png" alt-text="Die Zeitskala der Ereignisse mit zusammengeführten Ereignissen.":::

Das Zusammenführen von Geräten kann nicht rückgängig gemacht werden. Wenn Sie versehentlich zwei Geräte zusammengeführt haben, löschen Sie das Gerät, und warten Sie, bis der Sensor beide erneut erkennt.

So führen Sie Geräte zusammen

1. Wählen Sie zwei Geräte aus (UMSCHALT+Klick), und klicken Sie dann mit der rechten Maustaste auf eins der Geräte.

2. Wählen Sie **Zusammenführen** aus, um die Geräte zusammenzuführen. Die Zusammenführung kann bis zu zwei Minuten dauern.

3. Wählen Sie im Dialogfeld zum Festlegen der Attribute zum Zusammenführen von Geräten einen Gerätenamen aus.

   :::image type="content" source="media/how-to-work-with-maps/name-the-device-v2.png" alt-text="Dialogfeld mit Attributen":::

4. Wählen Sie **Speichern** aus.

### <a name="authorize-and-unauthorize-devices"></a>Autorisieren von Geräten und Aufheben der Autorisierung

In der Lernphase werden alle im Netzwerk erkannten Geräte als autorisierte Geräte identifiziert. In der Geräteübersicht wird die Bezeichnung **Autorisiert** bei diesen Geräten nicht angezeigt.

Wenn ein Gerät nach der Lernphase erkannt wird, wird es als nicht autorisiertes Gerät angezeigt. Sie können nicht autorisierte Geräte nicht nur in der Übersicht sehen, sondern auch im Gerätebestand.

:::image type="content" source="media/how-to-work-with-maps/inventory-icon.png" alt-text="Gerätebestand":::

**Vergleich von neuen und nicht autorisierten Geräten**

Neue nach der Lernphase erkannte Geräte werden mit der Bezeichnung `New` und `Unauthorized` angezeigt.

Wenn Sie ein Gerät in der Übersicht verschieben oder die Geräteeigenschaften manuell ändern, wird die Bezeichnung `New` vom Gerätesymbol entfernt.

#### <a name="unauthorized-devices---attack-vectors-and-risk-assessment-reports"></a>Nicht autorisierte Geräte: Berichte zu Angriffsvektoren und Risikobewertung

Nicht autorisierte Geräte werden in Berichten zu Risikobewertung und Angriffsvektoren berücksichtigt.

- **Berichte zu Angriffsvektoren:** Als nicht autorisiert markierte Geräte werden im Angriffsvektor als mutmaßlich nicht autorisierte Geräte aufgelöst, die eine Bedrohung für das Netzwerk darstellen können.

   :::image type="content" source="media/how-to-work-with-maps/attack-vector-reports.png" alt-text="Anzeigen von Berichten zu Angriffsvektoren":::

- **Berichte zur Risikobewertung**: Als nicht autorisiert markierte Geräte werden:

  - In Berichten zur Risikobewertung identifiziert

So können Sie Geräte manuell autorisieren oder ihre Autorisierung aufheben

1. Klicken Sie in der Übersicht mit der rechten Maustaste auf das Gerät, und wählen Sie **Autorisierung aufheben** aus.

### <a name="mark-devices-as-important"></a>Kennzeichnen von Geräten als wichtig

Sie können wesentliche Netzwerkgeräte als wichtig markieren, z. B. geschäftskritische Server. Diese Geräte sind in der Übersicht mit einem Stern gekennzeichnet. Der Stern variiert entsprechend der Zoomstufe der Übersicht.

:::image type="icon" source="media/how-to-work-with-maps/star-one.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/star-two.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/star-3.png" border="false":::

### <a name="important-devices---attack-vectors-and-risk-assessment-reports"></a>Wichtige Geräte: Berichte zu Angriffsvektoren und Risikobewertung

Wichtige Geräte werden in Berichten zu Risikobewertung und Angriffsvektoren berechnet.

  - Berichte zu Angriffsvektoren: Geräte, die als wichtig gekennzeichnet sind, werden im Angriffsvektor als Angriffsziele aufgelöst. 

  - Berichte zur Risikobewertung: Als wichtig markierte Geräte werden bei der Berechnung der Sicherheitsbewertung im Bericht zur Risikobewertung berücksichtigt.

## <a name="generate-activity-reports-from-the-map"></a>Generieren von Aktivitätsberichten anhand der Übersicht

Generieren Sie über 1, 6, 12 oder 24 Stunden einen Aktivitätsbericht für ein ausgewähltes Gerät. Folgende Informationen stehen zur Verfügung:

  - Kategorie: Grundlegende Erkennungsinformationen basierend auf Datenverkehrsszenarien.

  - Quell- und Zielgeräte

  - Daten: Zusätzliche Informationen erkannt.

  - Uhrzeit und Datum der letzten Erkennung.

Sie können den Bericht als Microsoft Excel- oder Word-Datei speichern.

:::image type="content" source="media/how-to-work-with-maps/historical-information.png" alt-text="Letzte Aktivität":::

So generieren Sie einen Aktivitätsbericht für ein Gerät

1. Klicken Sie in der in der Übersicht mit der rechten Maustaste auf ein Gerät.

2. Wählen Sie einen Aktivitätsbericht aus.

   :::image type="content" source="media/how-to-work-with-maps/activity-report.png" alt-text="Anzeigen eines Berichts Ihrer Aktivität.":::

## <a name="generate-attack-vector-reports-from-the-map"></a>Generieren von Berichten zu Angriffsvektoren anhand der Übersicht

Simulieren Sie einen Bericht zu Angriffsvektoren, um zu erfahren, ob ein Gerät in der von Ihnen ausgewählten Übersicht ein gefährdetes Angriffsziel ist.

Berichte zu Angriffsvektoren bieten eine grafische Darstellung einer Risikokette von Geräten mit Sicherheitslücken. Durch diese Sicherheitslücken kann ein Angreifer Zugriff auf wichtige Netzwerkgeräte erhalten. Der Angriffsvektorsimulator berechnet Angriffsvektoren in Echtzeit und analysiert alle Angriffsvektoren für ein bestimmtes Ziel.

So zeigen Sie ein Gerät in einem Bericht zu Angriffsvektoren an

1. Klicken Sie in der Übersicht mit der rechten Maustaste auf ein Gerät.

2. Wählen Sie **Angriffsvektoren simulieren** aus. Das Dialogfeld „Angriffsvektor“ wird mit dem Gerät geöffnet, das Sie als Angriffsziel auswählen.

   :::image type="content" source="media/how-to-work-with-maps/simulation.png" alt-text="Hinzufügen einer Angriffsvektorsimulation":::

3. Fügen Sie dem Dialogfeld die restlichen Parameter hinzu, und wählen Sie **Simulation hinzufügen** aus.

## <a name="export-device-information-from-the-map"></a>Exportieren von Geräteinformationen aus der Übersicht

Exportieren Sie folgenden Geräteinformationen aus der Übersicht.

  - Gerätedetails (Microsoft Excel)

  - Gerätezusammenfassung (Microsoft Excel)

  - Textdatei mit Gruppen (Microsoft Word)

So erfolgt das Exportieren

1. Wählen Sie in der Übersicht das Exportsymbol aus.

1. Wählen Sie eine Exportoption aus.

## <a name="next-steps"></a>Nächste Schritte

[Untersuchen von Sensorerkennungen in einem Gerätebestand](how-to-investigate-sensor-detections-in-a-device-inventory.md)
