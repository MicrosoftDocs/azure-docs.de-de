---
title: Generieren von Trend- und Statistikberichten
description: Verschaffen Sie sich mithilfe der Trend- und Statistikwidgets von Defender für IoT Einblicke in Netzwerkaktivitäten, Statistiken und Trends.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 01/24/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: c28e2f1c24d39ceb915be9f4f6f222d70de9ee73
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522222"
---
# <a name="sensor-trends-and-statistics-reports"></a>Sensortrend- und Statistikberichte

## <a name="about-sensor-trends-and-statistics-reports"></a>Informationen zu Sensortrend- und Statistikberichten

Sie können Widgetdiagramme und Kreisdiagramme erstellen, um Erkenntnisse über Netzwerktrends und Statistiken zu gewinnen. Widgets können unter benutzerdefinierten Dashboards gruppiert werden.

> [!NOTE]
> Administratoren und Sicherheitsanalysten können Trends und Statistikberichte erstellen.

Das Dashboard besteht aus Widgets, die die folgenden Informationstypen grafisch beschreiben:

- Datenverkehr nach Port
- Bester Datenverkehr nach Port
- Bandbreite des Kanals
- Bandbreite gesamt
- Aktive TCP-Verbindung
- Beste Bandbreite nach VLAN
- Geräte:
  - Neue Geräte
  - Ausgelastete Geräte
  - Geräte nach Hersteller
  - Geräte nach Betriebssystem
  - Anzahl der Geräte pro VLAN
  - Getrennte Geräte
- Verbindungsabbrüche nach Stunden
- Warnungen zu Vorfällen nach Typ
- Zugriff auf Datenbanktabellen
- Widgets zur Protokollanalyse
- DELTAV
  - Verteilung von DeltaV roc-Vorgängen
  - DeltaV roc-Ereignisse nach Name
  - DeltaV-Ereignisse nach Zeit
- AMS
  - AMS-Datenverkehr nach Serverport
  - AMS-Datenverkehr nach Befehl
- Ethernet- und IP-Adresse:
  - Ethernet- und IP-Adressdatenverkehr nach CIP-Dienst
  - Ethernet- und IP-Adressdatenverkehr nach CIP-Klasse
  - Ethernet- und IP-Adressdatenverkehr nach Befehl
- OPC:
  - Größte OPC-Verwaltungsvorgänge
  - Größte OPC-E/A-Vorgänge
- Siemens S7:
  - S7-Datenverkehr nach Steuerungsfunktion
  - S7-Datenverkehr nach Unterfunktion
- VLAN
  - Anzahl der Geräte pro VLAN
  - Beste Bandbreite nach VLAN
- 60870-5-104
  - IEC-60870-Datenverkehr nach ASDU
- BACNET
  - BACnet-Dienste
- DNP3
  - DNP3-Datenverkehr nach Funktion
- SRTP:
  - SRTP-Datenverkehr nach Dienstcode
  - SRTP-Fehler nach Tag
- SuiteLink:
  - Am meisten abgefragte SuiteLink-Tags
  - Verhalten von numerischen SuiteLink-Tags
- IEC-60870-Datenverkehr nach ASDU
- DNP3-Datenverkehr nach Funktion
- MMS-Datenverkehr nach Dienst
- Modbus-Datenverkehr nach Funktion
- OPC-UA-Datenverkehr nach Dienst

> [!NOTE]
>  Die Zeit in den Widgets wird entsprechend der Sensorzeit festgelegt.

## <a name="create-reports"></a>Erstellen von Berichten

So zeigen Sie Dashboards und Widgets an:

Wählen Sie im Seitenmenü die Option **Trends und Statistiken** aus.

:::image type="content" source="media/how-to-generate-reports/investigation-screenshot.png" alt-text="Screenshot einer Untersuchung.":::

Standardmäßig werden Ergebnisse für Erkennungen in den letzten 7 Tagen angezeigt. Sie können Filtertools verwenden, um diesen Bereich zu ändern. Beispielsweise eine Freitextsuche.

## <a name="next-steps"></a>Nächste Schritte

[Berichterstellung zur Risikobewertung](how-to-create-risk-assessment-reports.md)
[Sensor-Data Mining-Abfragen](how-to-create-data-mining-queries.md)
[Angriffsvektor-Berichterstellung](how-to-create-attack-vector-reports.md)