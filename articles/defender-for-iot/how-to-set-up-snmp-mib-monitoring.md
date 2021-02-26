---
title: Einrichten der SNMP-MIB-Überwachung
description: Sie können die Überwachung der Sensorintegrität mithilfe von SNMP durchführen. Der Sensor antwortet auf SNMP-Abfragen, die von einem autorisierten Überwachungsserver gesendet wurden.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/14/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 051ce1be66f91d60f719ca3695f15e6c8001b20f
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/15/2021
ms.locfileid: "100523820"
---
# <a name="set-up-snmp-mib-monitoring"></a>Einrichten der SNMP-MIB-Überwachung

Sie können die Überwachung der Sensorintegrität mithilfe von SNMP (Simple Network Management Protocol) durchführen. Der Sensor antwortet auf SNMP-Abfragen, die von einem autorisierten Überwachungsserver gesendet wurden. Der SNMP-Monitor fragt die Sensor-OIDs periodisch ab (bis zu 50 Mal pro Sekunde).

Die unterstützten SNMP-Versionen sind SNMP v2 oder SNMP v3. SNMP verwendet UDP als Transportprotokoll mit Port 161 (SNMP).

Bevor Sie mit der Konfiguration der SNMP-Überwachung beginnen, müssen Sie den Port UDP 161 in der Firewall öffnen.

## <a name="oids"></a>OIDs

| Verwaltungskonsole und Sensor | OID | Format | BESCHREIBUNG |
|--|--|--|--|
| Appliance-Name | 1.3.6.1.2.1.1.5.0 | DISPLAYSTRING | Name der Appliance für die lokale Verwaltungskonsole |
| Hersteller | 1.3.6.1.2.1.1.4.0 | DISPLAYSTRING | Microsoft-Support (support.microsoft.com) |
| Plattform | 1.3.6.1.2.1.1.1.0 | DISPLAYSTRING | Sensor oder lokale Verwaltungskonsole |
| Seriennummer | 1.3.6.1.4.1.9.9.53313.1 | DISPLAYSTRING | Von der Lizenz verwendete Zeichenfolge |
| Softwareversion | 1.3.6.1.4.1.9.9.53313.2 | DISPLAYSTRING | Zeichenfolge der Xsense-Vollversion und Zeichenfolge der Vollversion der Verwaltung |
| CPU-Auslastung | 1.3.6.1.4.1.9.9.53313.3.1 | GAUGE32 | Angabe für 0 (null) bis 100 |
| CPU-Temperatur | 1.3.6.1.4.1.9.9.53313.3.2 | DISPLAYSTRING | Celsius-Angabe für 0 (null) bis 100 basierend auf der Linux-Eingabe |
| Speicherauslastung | 1.3.6.1.4.1.9.9.53313.3.3 | GAUGE32 | Angabe für 0 (null) bis 100 |
| Datenträgerverwendung | 1.3.6.1.4.1.9.9.53313.3.4 | GAUGE32 | Angabe für 0 (null) bis 100 |
| Dienststatus | 1.3.6.1.4.1.9.9.53313.5 | DISPLAYSTRING | Online oder offline, wenn eine der vier entscheidenden Komponenten ausfällt |
| Bandbreite | Außerhalb des gültigen Bereichs für 2.4 |  | Die an jeder Monitorschnittstelle in Xsense empfangene Bandbreite |

   - Nicht vorhandene Schlüssel antworten mit Null, HTTP 200, basierend auf [Stack Overflow](https://stackoverflow.com/questions/51419026/querying-for-non-existing-record-returns-null-with-http-200).
    
   - Hardwarebezogene MIBs (CPU-Nutzung, CPU-Temperatur, Arbeitsspeichernutzung, Datenträgernutzung) sollten auf allen Architekturen und physischen Sensoren getestet werden. Die CPU-Temperatur wird erwartungsgemäß nicht auf virtuelle Maschinen angewendet.

Sie können das Protokoll herunterladen, das alle SNMP-Anfragen enthält, die der Sensor empfängt, einschließlich der Verbindungsdaten und der Rohdaten des Pakets.

So definieren Sie die SNMP v2-Integritätsüberwachung

1. Wählen Sie im Seitenmenü **Systemeinstellungen** aus.

2. Wählen Sie im Bereich **Aktive Ermittlung** die Option **SNMP MIB-Überwachung** :::image type="icon" source="media/how-to-set-up-snmp-mib-monitoring/snmp-icon.png" border="false"::: aus.

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/edit-snmp.png" alt-text="Bearbeiten Sie das SNMP-Fenster.":::

3. Wählen Sie im Bereich **Zulässige Hosts** die Option **Host hinzufügen** aus, und geben Sie die IP-Adresse des Servers ein, der die Systemintegritätsüberwachung durchführt.

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/snmp-allowed-ip-addess.png" alt-text="Geben Sie die IP-Adresse für zulässige Hosts ein.":::

4. Geben Sie im Abschnitt **Authentifizierung** im Feld **SNMP v2 Community-Zeichenfolge** die Zeichenfolge ein. Die SNMP Community-Zeichenfolge kann bis zu 32 Zeichen enthalten und eine beliebige Kombination aus alphanumerischen Zeichen (Großbuchstaben, Kleinbuchstaben und Zahlen) umfassen. Leerzeichen sind nicht zulässig.

5. Wählen Sie **Speichern** aus.

So definieren Sie die SNMP v3-Integritätsüberwachung

1. Wählen Sie im Seitenmenü **Systemeinstellungen** aus.

2. Wählen Sie im Bereich **Aktive Ermittlung** die Option **SNMP MIB-Überwachung** :::image type="icon" source="media/how-to-set-up-snmp-mib-monitoring/snmp-icon.png" border="false"::: aus.

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/edit-snmp.png" alt-text="Bearbeiten Sie das SNMP-Fenster.":::

3. Wählen Sie im Bereich **Zulässige Hosts** die Option **Host hinzufügen** aus, und geben Sie die IP-Adresse des Servers ein, der die Systemintegritätsüberwachung durchführt.

    :::image type="content" source="media/how-to-set-up-snmp-mib-monitoring/snmp-allowed-ip-addess.png" alt-text="Geben Sie die IP-Adresse für die zulässigen Hosts ein.":::

4. Legen Sie im Abschnitt **Authentifizierung** die folgenden Parameter fest:

    | Parameter | BESCHREIBUNG |
    |--|--|
    | **Benutzername** | Der SNMP-Benutzername kann bis zu 32 Zeichen enthalten und eine beliebige Kombination aus alphanumerischen Zeichen (Großbuchstaben, Kleinbuchstaben und Zahlen) umfassen. Leerzeichen sind nicht zulässig. <br /> <br />Der Benutzername für die SNMP v3-Authentifizierung muss auf dem System und auf dem SNMP-Server konfiguriert werden. |
    | **Kennwort** | Geben Sie ein Authentifizierungskennwort unter Berücksichtigung von Groß-/Kleinschreibung ein. Das Authentifizierungskennwort kann 8–12 Zeichen enthalten und eine beliebige Kombination aus alphanumerischen Zeichen (Großbuchstaben, Kleinbuchstaben und Zahlen) umfassen. <br /> <br/>Der Benutzername für die SNMP v3-Authentifizierung muss auf dem System und auf dem SNMP-Server konfiguriert werden. |
    | **Authentifizierungstyp** | Wählen Sie MD5 oder SHA aus. |
    | **Verschlüsselung** | Wählen Sie DES oder AES aus. |
    | **Geheimer Schlüssel** | Der Schlüssel muss aus genau acht Zeichen bestehen und eine beliebige Kombination aus alphanumerischen Zeichen (Großbuchstaben, Kleinbuchstaben und Zahlen) umfassen. |

5. Wählen Sie **Speichern** aus.

## <a name="next-steps"></a>Nächste Schritte

[Exportieren von Problembehandlungsprotokollen](how-to-troubleshoot-the-sensor-and-on-premises-management-console.md)
