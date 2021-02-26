---
title: Informationen zu Geräten, die von allen Unternehmenssensoren erkannt werden
description: Sie können sich über den Gerätebestand in der lokalen Verwaltungskonsole einen umfassenden Überblick über die Geräteinformationen von verbundenen Sensoren verschaffen. Verwenden Sie die Tools zum Importieren, Exportieren und Filtern, um diese Informationen zu verwalten.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/02/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: b4d824386a7c14165697f4b93f2e93cf0182a855
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/15/2021
ms.locfileid: "100524024"
---
# <a name="investigate-all-enterprise-sensor-detections-in-the-device-inventory"></a>Untersuchen aller Unternehmenssensorerkennungen im Gerätebestand

Über den *Gerätebestand* in der lokalen Verwaltungskonsole können Sie die Geräteinformationen von verbundenen Sensoren anzeigen. Dieses Feature bietet einen umfassenden Überblick über alle Netzwerkinformationen. Verwenden Sie die Tools zum Importieren, Exportieren und Filtern, um diese Informationen zu verwalten. Die Statusinformationen zu den Versionen des verbundenen Sensors werden ebenfalls angezeigt.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/device-inventory-data-table.png" alt-text="Screenshot: Tabelle mit Gerätebestandsdaten":::

Im Folgenden werden die Spalten in der Gerätebestandstabelle beschrieben.

| Parameter | BESCHREIBUNG |
|--|--|
| **Nicht bestätigte Warnungen** | Die Anzahl der nicht behandelten Warnungen, die diesem Gerät zugeordnet sind |
| **Geschäftseinheit** | Die Geschäftseinheit, in der dieses Gerät enthalten ist |
| **Region** | Die Region, in der dieses Gerät enthalten ist |
| **Website** | Der Standort, der dieses Gerät enthält |
| **Zone** | Die Zone, in der dieses Gerät enthalten ist |
| **Appliance** | Der Azure Defender für IoT-Sensor, der dieses Gerät schützt |
| **Name** | Der Name des Geräts, wie von Defender für IoT erkannt |
| **Type** | Der Typ des Geräts, z. B. PLC oder HMI |
| **Hersteller** | Der Name des Herstellers des Geräts, wie in der MAC-Adresse angegeben |
| **Betriebssystem** | Das Betriebssystem des Geräts |
| **Firmware** | Die Firmware des Geräts |
| **IP-Adresse** | Die IP-Adresse des Geräts |
| **VLAN** | Das VLAN des Geräts |
| **MAC-Adresse** | Die MAC-Adresse des Geräts. |
| **Protokolle** | Die Protokolle, die vom Gerät verwendet werden |
| **Nicht bestätigte Warnungen** | Die Anzahl der nicht behandelten Warnungen, die diesem Gerät zugeordnet sind |
| **Ist autorisiert** | Der Autorisierungsstatus des Geräts:<br />- **true**: Das Gerät wurde autorisiert.<br />- **false**: Das Gerät wurde nicht autorisiert. |
| **Als Scanner bekannt** | Gibt an, ob dieses Gerät scannerähnliche Aktivitäten im Netzwerk durchführt |
| **Ist Programmiergerät** | Gibt an, ob es sich um ein Programmiergerät handelt:<br />- **true**: Das Gerät führt Programmieraktivitäten für PLCs, RTUs und Controller aus, die für Engineeringstationen relevant sind.<br />- **false**: Das Gerät ist kein Programmiergerät. |
| **Gruppen** | Gruppen, an denen dieses Gerät teilnimmt |
| **Letzte Aktivität** | Die letzte Aktivität, die das Gerät ausgeführt hat |
| **Discovered** | Wann dieses Gerät zum ersten Mal im Netzwerk erkannt wurde |

## <a name="integrate-data-into-the-enterprise-device-inventory"></a>Integrieren von Daten in den Gerätebestand des Unternehmens

Mit den Funktionen für die Datenintegration können Sie die Daten im Gerätebestand mit Informationen aus anderen Unternehmensressourcen verbessern. Zu diesen Quellen gehören Konfigurationsverwaltungsdatenbanken (CMDB), DNS, Firewalls und Web-APIs.

Sie können diese Informationen verwenden, um sich ein umfassenderes Bild zu machen. Beispiel:

- Kaufdatum des Geräts und Ende der Garantiezeit

- Benutzer, die für das jeweilige Gerät zuständig sind

- Geöffnete Gerätetickets

- Das letzte Datum, an dem die Firmware aktualisiert wurde

- Geräte, die Zugriff auf das Internet haben

- Geräte, die aktive Virenschutzanwendungen ausführen

- Benutzer, die sich bei Geräten angemeldet haben

:::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-screen-with-items-highlighted-v2.png" alt-text="Datentabelle auf dem Gerätebestandbildschirm.":::

Sie können Daten wie folgt integrieren:

- Manuelles Hinzufügen

- Ausführen von benutzerdefinierten Skripts, die Defender für IoT bereitstellt

:::image type="content" source="media/how-to-work-with-asset-inventory-information/enterprise-data-integrator-graph.png" alt-text="Diagramm des Unternehmensdatenintegrators":::

Der technische Support für Defender für IoT kann Sie beim Einrichten Ihres Systems für den Empfang von Web-API-Abfragen unterstützen.

So fügen Sie Daten manuell hinzu

1. Klicken Sie im Seitenmenü auf **Gerätebestand**, und wählen Sie dann :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon.png" border="false"::: aus.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-settings-v2.png" alt-text="Bearbeiten der Einstellungen für den Gerätebestand":::

2. Wählen Sie im Dialogfeld **Gerätebestandseinstellungen** die Option **BENUTZERDEFINIERTE SPALTE HINZUFÜGEN** aus.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column.png" alt-text="Hinzufügen einer benutzerdefinierten Spalte zum Bestand":::

3. Fügen Sie im Dialogfeld **Benutzerdefinierte Spalte hinzufügen** den neuen Spaltennamen (bis zu 250 Zeichen UTF) hinzu, wählen Sie **Manuell** aus, und klicken Sie auf **SPEICHERN**. Das neue Element wird im Dialogfeld **Gerätebestandseinstellungen** angezeigt.

4. Klicken Sie in der oberen rechten Ecke des Fensters **Gerätebestand** auf :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false":::, und wählen Sie die Option **Alle Gerätebestandsinformationen exportieren** aus. Die CSV-Datei wird generiert.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/sample-exported-csv-file.png" alt-text="Die exportierte CSV-Datei":::

5. Fügen Sie der neuen Spalte die Informationen manuell hinzu, und speichern Sie die Datei.

6. Klicken Sie in der oberen rechten Ecke des Fensters **Gerätebestand** auf :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false":::, wählen Sie die Option **Manuelle Eingabespalten importieren** aus, und navigieren Sie zur CSV-Datei. Die neuen Daten werden in der Tabelle **Gerätebestand** angezeigt.

So integrieren Sie Daten aus anderen Unternehmensentitäten

1. Klicken Sie in der oberen rechten Ecke des Fensters **Gerätebestand** auf :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false":::, und wählen Sie die Option **Alle Gerätebestandsinformationen exportieren** aus.

2. Wählen Sie im Dialogfeld **Gerätebestandseinstellungen** die Option **BENUTZERDEFINIERTE SPALTE HINZUFÜGEN** aus.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column.png" alt-text="Hinzufügen einer benutzerdefinierten Spalte zum Bestand":::

3. Fügen Sie im Dialogfeld **Benutzerdefinierte Spalte hinzufügen** den neuen Spaltennamen (bis zu 250 Zeichen UTF) hinzu, und wählen Sie **Automatisch** aus. Die Optionen **SKRIPT HOCHLADEN** und **SKRIPT TESTEN** werden angezeigt.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column-automatic.png" alt-text="Automatisches Hinzufügen von benutzerdefinierten Spalten":::

4. Laden Sie das Skript hoch, das Sie vom [Microsoft-Support](https://support.serviceshub.microsoft.com/supportforbusiness/create?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099) erhalten haben, und testen Sie es.

## <a name="retrieve-information-from-the-device-inventory"></a>Abrufen von Informationen aus dem Gerätebestand

Sie können eine umfangreiche Bandbreite an Geräteinformationen abrufen, die von verwalteten Sensoren erkannt werden, und diese Informationen in Partnersysteme integrieren. Sie können zum Beispiel Sensor-, Zonen-, Standort-ID-, IP-Adress-, MAC-Adress-, Firmware-, Protokoll- und Herstellerinformationen abrufen. Die abgerufenen Informationen lassen sich nach folgenden Kriterien filtern:

- Autorisierte und nicht autorisierte Geräte

- Geräte, die bestimmten Standorten zugeordnet sind

- Geräte, die bestimmten Zonen zugeordnet sind

- Geräte, die bestimmten Sensoren zugeordnet sind

Verwenden Sie API-Befehle für Defender für IoT, um diese Informationen abzurufen und zu integrieren. Weitere Informationen finden Sie unter [Sensor- und Verwaltungskonsolen-APIs für Defender für IoT](references-work-with-defender-for-iot-apis.md).

## <a name="filter-the-device-inventory"></a>Filtern des Gerätebestands

Der Gerätebestand lässt sich filtern, um relevante Spalten anzuzeigen. So können Sie sich zum Beispiel PLC-Geräteinformationen anzeigen lassen.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-view-v2.png" alt-text="Screenshot des Gerätebestands.":::

Der Filter wird gelöscht, wenn Sie das Fenster schließen.

Wenn Sie einen Filter mehrmals verwenden möchten, können Sie diesen Filter oder eine Kombination von Filtern speichern. Im linken Bereich können Sie die gespeicherten Filter anzeigen:

:::image type="content" source="media/how-to-work-with-asset-inventory-information/view-your-asset-inventories-v2.png" alt-text="Bildschirm: Gerätebestände.":::

So filtern Sie den Gerätebestand

1. Wählen Sie in der Spalte, die Sie filtern möchten, das Symbol :::image type="icon" source="media/how-to-work-with-asset-inventory-information/filter-a-column-icon.png" border="false"::: aus.

2. Wählen Sie im Dialogfeld **Filter** den folgenden Filtertyp aus:

   - **Equals** (gleich): Der genaue Wert, nach dem Sie die Spalte filtern möchten. Wenn Sie z. B. die Protokollspalte nach **Equals** (gleich) und `value=ICMP` filtern, werden in der Spalte nur Geräte angezeigt, die das ICMP-Protokoll verwenden.

   - **Contains** (Enthält): Der Wert, der in anderen Werten in der Spalte enthalten ist. Wenn Sie z. B. die Protokollspalte nach **Contains** (Enthält) und `value=ICMP` filtern, werden in der Spalte nur Geräte angezeigt, die das ICMP-Protokoll als Teil der Liste der Protokolle, die das Gerät benutzt, verwenden.

3. Wählen Sie :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-order-icon.png" border="false"::: aus, um die Spalteninformationen alphabetisch zu sortieren. Sie können die Reihenfolge mithilfe der Pfeile :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-a-z-order-icon.png" border="false"::: und :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-z-a-order-icon.png" border="false"::: anpassen.

4. Definieren Sie den Filter, und wählen Sie **Speichern unter** aus, um den neuen Filter zu speichern.

5. Ändern Sie die Definitionen, und wählen Sie **Änderungen speichern** aus, um die Filterdefinitionen anzupassen.

## <a name="view-device-information-per-zone"></a>Anzeigen von Geräteinformationen nach Zone

Sie können die folgenden Informationen zu Geräten in einer Zone abrufen.

### <a name="view-a-device-map"></a>Anzeigen der Gerätezuordnung

So zeigen Sie eine Sensorgerätezuordnung für eine ausgewählte Zone an

- Wählen Sie im Fenster **Standortverwaltung** die Option **Zonenzuordnung anzeigen** in der Liste mit dem Zonennamen aus.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-region-to-default-business-unit-v2.png" alt-text="Standardregion für Standardgeschäftseinheit":::

Das Fenster **Gerätezuordnung** wird angezeigt. Es zeigt alle Netzwerkelemente an, die sich auf die ausgewählte Zone beziehen, einschließlich der Sensoren, der Geräte, die mit ihnen verbunden sind, und anderer Informationen.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/zone-map-screenshot.png" alt-text="Screenshot: Zonenzuordnung":::

Die folgenden Tools sind verfügbar, um Geräte und Geräteinformationen aus der Zuordnung anzuzeigen. Ausführliche Informationen zu den einzelnen Features finden Sie im *Benutzerleitfaden zur Defender für IoT-Plattform*.

- **Vergrößerung von Zuordnungsansichten:** Vereinfachte Ansicht, Verbindungsansicht und detaillierte Ansicht. Die angezeigte Zuordnungsansicht variiert abhängig von der Zoomstufe. Durch Anpassen der Zoomstufen können Sie zwischen Zuordnungsansichten wechseln.

  :::image type="icon" source="media/how-to-work-with-asset-inventory-information/zoom-icon.png" border="false":::

- **Such- und Layouttools für die Zuordnung:** Tools für die Anzeige verschiedener Netzwerksegmente, Geräte, Gerätegruppen oder Ebenen.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/search-and-layout-tools.png" alt-text="Screenshot: Ansicht „Such- und Layouttools“":::

- **Bezeichnungen und Indikatoren auf Geräten:** Beispielsweise die Anzahl der Geräte, die in einem Subnetz in einem IT-Netzwerk gruppiert sind. In diesem Beispiel lautet sie 8.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/labels-and-indicators.png" alt-text="Screenshot: Bezeichnungen und Indikatoren":::

- **Anzeigen von Geräteeigenschaften:** Beispielsweise der Sensor, der die Geräte- und grundlegenden Geräteeigenschaften überwacht. Klicken Sie mit der rechten Maustaste auf das Gerät, um die Geräteeigenschaften anzuzeigen.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-properties-v2.png" alt-text="Screenshot: Ansicht „Geräteeigenschaften“.":::

- **Einem Gerät zugeordnete Warnungen:** Klicken Sie mit der rechten Maustaste auf das Gerät, um die damit verbundenen Warnungen anzuzeigen.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/show-alerts.png" alt-text="Screenshot: Ansicht „Warnungen anzeigen“":::

### <a name="view-alerts-associated-with-a-zone"></a>Anzeigen von Warnungen, die einer Zone zugeordnet sind

So zeigen Sie Warnungen an, die einer bestimmten Zone zugeordnet sind

- Wählen Sie das Warnungssymbol im Fenster **Zone** aus. 

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/business-unit-view-v2.png" alt-text="Die Ansicht „Standardgeschäftseinheit“ mit Beispielen":::

Weitere Informationen finden Sie unter [Übersicht: Arbeiten mit Warnungen](how-to-work-with-alerts-on-premises-management-console.md).

### <a name="view-the-device-inventory-of-a-zone"></a>Anzeigen des Gerätebestands einer Zone

So zeigen Sie den einer bestimmten Zone zugeordneten Gerätebestand an

- Wählen Sie im Fenster **Zone** die Option **Gerätebestand anzeigen** aus.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-business-unit.png" alt-text="Der Bildschirm „Gerätebestand“ wird angezeigt.":::

Weitere Informationen finden Sie unter [Untersuchen aller Unternehmenssensorerkennungen in einem Gerätebestand](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md).

### <a name="view-additional-zone-information"></a>Anzeigen weiterer Zoneninformationen

Folgende zusätzliche Zoneninformationen sind verfügbar:

- **Zonendetails:** Anzeigen der Anzahl von Geräten, Warnungen und Sensoren, die der Zone zugeordnet sind

- **Sensordetails:** Zeigen Sie den Namen, die IP-Adresse und die Version der einzelnen Sensoren an, die der Zone zugewiesen sind.

- **Konnektivitätsstatus:** Wenn ein Sensor getrennt ist, stellen Sie eine Verbindung mit diesem Sensor her. Weitere Informationen finden Sie unter [Verbinden von Sensoren mit der lokalen Verwaltungskonsole](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console). 

- **Aktualisierungsstatus:** Wenn der verbundene Sensor aktualisiert wird, werden die Upgradestatus angezeigt. Während des Upgradevorgangs empfängt die lokale Verwaltungskonsole keine Geräteinformationen des Sensors.

## <a name="next-steps"></a>Nächste Schritte

[Untersuchen von Sensorerkennungen in einem Gerätebestand](how-to-investigate-sensor-detections-in-a-device-inventory.md)
