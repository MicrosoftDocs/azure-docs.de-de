---
title: Ermitteln der erforderlichen Appliances
description: Erfahren Sie mehr über Hardware und virtuelle Appliances für zertifizierte Defender für IoT-Sensoren und die lokale Verwaltungskonsole.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 01/13/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 92bf066c9769cc4b2525923b9e18ed3c0e9c577a
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98937145"
---
# <a name="identify-required-appliances"></a>Ermitteln der erforderlichen Appliances

Dieser Artikel enthält Informationen zu zertifizierten Defender für IOT-Sensor-Appliances. Defender für IOT kann auf physischen und virtuellen Appliances bereitgestellt werden.

Dazu gehören sowohl zertifizierte *vorkonfigurierte* Appliances, auf denen die Software bereits installiert ist, als auch nicht konfigurierte zertifizierte Appliances, auf die Sie die benötigte Software herunterladen und installieren können.

Der Artikel enthält auch Spezifikationen für eine lokale Verwaltungskonsolen-Appliance. Die lokale Verwaltungskonsole ist nicht als vorkonfigurierte Appliance verfügbar.

- Wenn Sie einen vorkonfigurierten Sensor kaufen möchten, prüfen Sie die verfügbaren Modelle im Abschnitt [Sensor-Appliances](#sensor-appliances), und fahren Sie dann mit dem Kauf fort.

- Wenn Sie ein eigenes Gerät kaufen möchten, sehen Sie sich die verfügbaren Modelle im Abschnitt [Sensor-Appliances](#sensor-appliances) und im Abschnitt [Zusätzliche zertifizierte Appliances](#additional-certified-appliances) an. Nach dem Kauf der Appliance können Sie die Software herunterladen und installieren.

- Wenn Sie die lokale Verwaltungskonsole erwerben möchten, lesen Sie die Informationen im Abschnitt [lokale Verwaltungskonsolen-Appliance](#on-premises-management-console-appliance). Nach dem Kauf des Geräts können Sie die Software herunterladen und installieren.

Nachdem Sie die hier aufgeführten Aufgaben erledigt haben, können Sie die Software installieren und Ihr Netzwerk einrichten.

## <a name="sensor-appliances"></a>Sensor-Appliances

Defender für IoT unterstützt sowohl physische als auch virtuelle Bereitstellungen.

### <a name="physical-sensors"></a>Physische Sensoren

Dieser Abschnitt enthält eine Übersicht über die verfügbaren Modelle physischer Sensoren. Sie können Sensoren mit vorkonfigurierter Software erwerben oder Sensoren kaufen, die nicht vorkonfiguriert sind.

| Bereitstellungstyp | Unternehmen | Enterprise | SMB |
|--|--|--|--|
| Image | :::image type="content" source="media/how-to-prepare-your-network/corporate-hpe-proliant-dl360-v2.png" alt-text="Das Modell auf Unternehmensebene."::: | :::image type="content" source="media/how-to-prepare-your-network/enterprise-and-smb-hpe-proliant-dl20-v2.png" alt-text="Das Modell auf Enterprise-Ebene."::: | :::image type="content" source="media/how-to-prepare-your-network/enterprise-and-smb-hpe-proliant-dl20-v2.png" alt-text="Das Modell auf SMB-Ebene."::: |
| Modell | HPE ProLiant DL360 | HPE ProLiant DL20 | HPE ProLiant DL20 |
| Überwachungsports | Bis zu 15 RJ45 oder 8 OPT | Bis zu 8 RJ45 oder 6 OPT | 4 RJ45 |
| Maximale Bandbreite [1](#anchortext) | 3 G pro Sekunde | 1 G pro Sekunde | 200 MB pro Sekunde |
| Maximale Anzahl geschützter Geräte | 30.000 | 15.000 | 1\.000 |

Unter [Appliance-Spezifikationen](#appliance-specifications) finden Sie Herstellerdetails.

Informationen zu vorkonfigurierten Sensoren: Microsoft ist eine Partnerschaft mit Arrow eingegangen, um vorkonfigurierte Sensoren bereitstellen zu können. Wenn Sie einen vorkonfigurierten Sensor kaufen möchten, wenden Sie sich an Arrow unter der folgenden Adresse: <hardware.sales@arrow.com>.

Informationen zum Einbinden Ihrer eigenen Appliance: Überprüfen Sie die hier beschriebenen unterstützten Modelle. Nachdem Sie Ihre Appliance erworben haben, navigieren Sie zu **Defender für IoT** > **Netzwerksensoren ISO** > **Installation**, um die Software herunterzuladen.

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-sensor-download-software-screen.png" alt-text="Netzwerksensoren-ISO.":::

<a id="anchortext">1</a> Bandbreitenkapazität kann variieren, abhängig von der Verteilung von Protokollen.

### <a name="virtual-sensors"></a>Virtuelle Sensoren

Dieser Abschnitt enthält eine Übersicht über die verfügbaren virtuellen Sensoren.

| Bereitstellungstyp | Unternehmen | Enterprise | SMB |
|--|--|--|--|
| Maximale Bandbreite | 2,5 GB/s | 800 MB/s | 160 MB/s |
| Maximale Anzahl geschützter Geräte | 30.000 | 10.000 | 2\.500 |

## <a name="on-premises-management-console-appliance"></a>Lokale Verwaltungskonsolen-Appliance

Die Verwaltungskonsole ist als virtuelle Bereitstellung verfügbar.

| Bereitstellungstyp | Enterprise |
|--|--|
| Appliancetyp | HPE DL20, VM |
| Anzahl der verwalteten Sensoren | Bis zu 300 |

Nachdem Sie eine lokale Verwaltungskonsole erworben haben, navigieren Sie zu **Defender für IoT** > **Lokale Verwaltungskonsole** > **ISO-Installation**, um die ISO herunterzuladen.

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-iso-download-screen.png" alt-text="Lokale Verwaltungskonsole.":::

## <a name="appliance-specifications"></a>Appliance-Spezifikationen

In diesem Abschnitt sind die Hardwarespezifikationen für die folgenden Appliances beschrieben:

- Unternehmensbereitstellung: HPE ProLiant DL360

- Enterprise-Bereitstellung: HPE ProLiant DL20

- SMB-Bereitstellung: HPE ProLiant DL20

- Spezifikationen virtueller Appliances

## <a name="corporate-deployment-hpe-proliant-dl360"></a>Unternehmensbereitstellung: HPE ProLiant DL360

| Komponente | Technische Spezifikationen |
|--|--|
| Gehäuse | 1U-Rackserver |
| Dimensionen | 42,9 x 43,46 x 70,7 cm (16,9" x 17,11" x 27,83") |
| Weight | Max. 16,27 kg (35,86 lb) |
| Prozessor | Intel Xeon Silver 4215 R 3.2 GHz, 11M Cache, 8c/16T, 130 W |
| Chipsatz | Intel C621 |
| Arbeitsspeicher | 32 GB = 2 x 16 GB 2666MT/s DDR4 ECC UDIMM |
| Storage | 6 x 1,2 TB SAS 12G Enterprise 10K SFF (2,5") Hot-Plug Hard Drive – RAID 5 |
| Netzwerkcontroller | Auf dem Board: 2 x 1 GB, Broadcom BCM5720<br>On-Board LOM: iDRAC Port-Karte 1 GB Broadcom BCM5720<br><br>Extern: 1 x Intel Ethernet i350 QP 1 GB-Serveradapter, niedriges Profil |
| Verwaltung | HPE iLO Advanced |
| Gerätezugriff | Zwei USB 3.0 (hinten)<br>Einen USB 2.0 (vorne)<br>Einen internen USB 3.0 |
| Leistung | 2 x HPE 500 W Flex Slot Platinum Hot Plug Low Halogen-Stromversorgungskit |
| Rackbefestigung | HPE 1U Gen10 SFF Easy Install Rail Kit |

### <a name="appliance-bom"></a>Appliance-BOM

| PN | BESCHREIBUNG | Menge |
|--|--|--|
| P19766-B21 | HPE DL360 Gen10 8SFF NC CTO Server | 1 |
| P19766-B21 | Europa: mehrsprachige Lokalisierung | 1 |
| P24479-L21 | Intel Xeon-S 4215 R FIO Kit für DL360 G10 | 1 |
| P24479-B21 | Intel Xeon-S 4215 R Kit für DL360 Gen10 | 1 |
| P00922-B21 | HPE 16 GB 2Rx8 PC4-2933Y-R Smart Kit | 2 |
| 872479-B21 | HPE 1.2 TB SAS 10K SFF SC DS HDD | 6 |
| 811546-B21 | HPE 1-GbE 4-p BASE-T I350 Adapter | 1 |
| P02377-B21 | HPE Smart Hybrid Capacitor mit 145 mm-Kabel | 1 |
| 804331-B21 | HPE Smart Array P408i-a SR Gen10 Controller | 1 |
| 665240-B21 | HPE 1-GbE 4-p FLR-T I350 Adapter | 1 |
| 871244-B21 | HPE DL360 Gen10-Hochleistungsventilator-Kit | 1 |
| 865408-B21 | HPE 500-W FS Plat Hot Plug LH-Stromversorgungskit | 2 |
| 512485-B21 | HPE iLO Adv 1-Serverlizenz, Support für 1 Jahr | 1 |
| 874543-B21 | HPE 1U Gen10 SFF Easy Install Rail Kit | 1 |

## <a name="enterprise-deployment-hpe-proliant-dl20"></a>Enterprise-Bereitstellung: HPE ProLiant DL20

| Komponente | Technische Spezifikationen |
|--|--|
| Gehäuse | 1U-Rackserver |
| Maße (Höhe x Breite x Tiefe) | 4,32 x 43,46 x 38,22 cm (1,70" x 17,11" x 15,05") |
| Weight | 7,9 kg (17,41 lb) |
| Prozessor | Intel Xeon E-2234, 3,6 GHz, 4C/8T, 71 W |
| Chipsatz | Intel C242 |
| Arbeitsspeicher | 2 x 16 GB Dual Rank x8 DDR4-2666 |
| Storage | 3 x 1 TB SATA 6G Midline 7.2 K SFF (2,5") – RAID 5 mit Smart Array P408i-a SR Controller |
| Netzwerkcontroller | Auf dem Board: 2 x 1 GB <br>On-Board: iLO Port-Karte 1 GB <br>Extern: 1 x HPE Ethernet 1 GB 4-Port 366FLR Adapter |
| Verwaltung | HPE iLO Advanced |
| Gerätezugriff | Vorderseite: 1 x USB 3.0, 1 x USB iLO Service-Port <br>Rückseite: 2 x USB 3.0 <br>Intern: 1 x USB 3.0 |
| Leistung | Dual Hot Plug-Stromversorgung 500 W |
| Rackbefestigung | HPE 1U Short Friction Rail Kit |

### <a name="appliance-bom"></a>Appliance-BOM

| PN | Beschreibung: High-End | Menge |
|--|--|--|
| P06963-B21 | HPE DL20 Gen10 4SFF CTO Server | 1 |
| P06963-B21 | HPE DL20 Gen10 4SFF CTO Server | 1 |
| P17104-L21 | HPE DL20 Gen10 E-2234 FIO Kit | 1 |
| 879507-B21 | HPE 16 GB 2Rx8 PC4-2666V-E STND Kit | 2 |
| 655710-B21 | HPE 1 TB SATA 7.2 K SFF SC DS HDD | 3 |
| P06667-B21 | HPE DL20 Gen10 x8x16 FLOM Riser Kit | 1 |
| 665240-B21 | HPE Ethernet 1 GB 4-Port 366FLR Adapter | 1 |
| 782961-B21 | HPE 12-W Smart Storage-Akku | 1 |
| 869081-B21 | HPE Smart Array P408i-a SR G10 LH Controller | 1 |
| 865408-B21 | HPE 500-W FS Plat Hot Plug LH-Stromversorgungskit | 2 |
| 512485-B21 | HPE iLO Adv 1-Serverlizenz, Support für 1 Jahr | 1 |
| P06722-B21 | HPE DL20 Gen10 RPS Enablement FIO Kit | 1 |
| 775612-B21 | HPE 1U Short Friction Rail Kit | 1 |

## <a name="smb-deployment-hpe-proliant-dl20"></a>SMB-Bereitstellung: HPE ProLiant DL20

| Komponente | Technische Spezifikationen |
|--|--|
| Gehäuse | 1U-Rackserver |
| Maße (Höhe x Breite x Tiefe) | 4,32 x 43,46 x 38,22 cm (1,70" x 17,11" x 15,05") |
| Weight | 7,88 kg (17,37 lb) |
| Prozessor | Intel Xeon E-2224, 3,4 GHz, 4C, 71 W |
| Chipsatz | Intel C242 |
| Arbeitsspeicher | 1 x 8 GB Dual Rank x8 DDR4-2666 |
| Storage | 2 x 1 TB SATA 6G Midline 7.2 K SFF (2,5") – RAID 1 mit Smart Array P208i-a |
| Netzwerkcontroller | Auf dem Board: 2 x 1 GB <br>On-Board: iLO Port-Karte 1 GB <br>Extern: 1 x HPE Ethernet 1 GB 4-Port 366FLR Adapter |
| Verwaltung | HPE iLO Advanced |
| Gerätezugriff | Vorderseite: 1 x USB 3.0, 1 x USB iLO Service-Port <br>Rückseite: 2 x USB 3.0 <br>Intern: 1 x USB 3.0 |
| Leistung | Hot Plug-Stromversorgung 290 W |
| Rackbefestigung | HPE 1U Short Friction Rail Kit |

### <a name="appliance-bom"></a>Appliance-BOM

| PN | BESCHREIBUNG | Menge |
|--|--|--|
| P06961-B21 | HPE DL20 Gen10 NHP 2LFF CTO Server | 1 |
| P06961-B21 | HPE DL20 Gen10 NHP 2LFF CTO Server | 1 |
| P17102-L21 | HPE DL20 Gen10 E-2224 FIO Kit | 1 |
| 879505-B21 | HPE 8 GB 1Rx8 PC4-2666V-E STND Kit | 1 |
| 801882-B21 | HPE 1 TB SATA 7.2 K LFF RW HDD | 2 |
| P06667-B21 | HPE DL20 Gen10 x8x16 FLOM Riser Kit | 1 |
| 665240-B21 | HPE Ethernet 1 GB 4-Port 366FLR Adapter | 1 |
| 869079-B21 | HPE Smart Array E208i-a SR G10 LH Controller | 1 |
| P21649-B21 | HPE DL20 Gen10 Plat 290 W FIO PSU Kit | 1 |
| P06683-B21 | HPE DL20 Gen10 M.2 SATA/LFF AROC -Kabel-Kit | 1 |
| 512485-B21 | HPE iLO Adv 1-Serverlizenz, Support für 1 Jahr | 1 |
| 775612-B21 | HPE 1U Short Friction Rail Kit | 1 |

## <a name="virtual-appliance-specifications"></a>Spezifikationen virtueller Appliances

### <a name="sensors"></a>Sensoren

| type | Unternehmen | Enterprise | SMB |
|--|--|--|--|
| vCPU | 32 | 8 | 4 |
| Arbeitsspeicher | 32 GB | 32 GB | 8 GB |
| Storage | 5,6 TB | 1,8 TB | 500 GB |

### <a name="on-premises-management-console-appliance"></a>Lokale Verwaltungskonsolen-Appliance

| type | Enterprise |
|--|--|
| BESCHREIBUNG | Virtuelle Appliance für Enterprise-Bereitstellungstypen |
| vCPU | 8 |
| Arbeitsspeicher | 32 GB |
| Storage | 1,8 TB |

Unterstützte Hypervisoren: VMware ESXi Version 5.0 und höher, Hyper-V

## <a name="additional-certified-appliances"></a>Weitere zertifizierte Appliances

In diesem Abschnitt werden weitere Appliances beschrieben, die von Microsoft zertifiziert wurden, aber nicht als vorkonfigurierte Appliances angeboten werden.

| Bereitstellungstyp | Enterprise |
|--|--|
| Image | :::image type="content" source="media/how-to-prepare-your-network/deployment-type-enterprise-for-azure-defender-for-iot-v2.png" alt-text="Enterprise-Bereitstellungstyp."::: |
| Modell | Dell PowerEdge R340 XL |
| Überwachungsports | Bis zu neun RJ45 oder sechs OPT |
| Maximale Bandbreite[1](#anchortext2)| 1 GB/s |
| Maximale Anzahl geschützter Geräte | 10.000 |

<a id="anchortext2">1</a> Bandbreitenkapazität kann variieren, abhängig von der Verteilung der Protokolle.

Nachdem Sie Ihre Appliance gekauft haben, navigieren Sie zu **Defender für IoT** > **Netzwerksensoren ISO** > **Installation**, um die Software herunterzuladen.

:::image type="content" source="media/how-to-prepare-your-network/azure-defender-for-iot-sensor-download-software-screen.png" alt-text="Netzwerksensoren-ISO.":::

## <a name="enterprise-deployment-dell-poweredge-r340-xl"></a>Enterprise-Bereitstellung: Dell PowerEdge R340 XL

| Komponente | Technische Spezifikationen |
|--|--|
| Gehäuse | 1U-Rackserver |
| Dimensionen | 42,8 x 434,0 x 596 mm (1,67" x 17,09" x 23,5") |
| Weight | Max. 13,6 kg (29,98 lb) |
| Prozessor | Intel Xeon E-2144G 3,6 GHz, 8M Cache, 4C/8T, Turbo (71 W) |
| Chipsatz | Intel C246 |
| Arbeitsspeicher | 32 GB = 2 x 16 GB 2666MT/s DDR4 ECC UDIMM |
| Storage | 3 x 2 TB 7,2 K RPM SATA 6 Gbit/s 512n 3,5" Hot-Plug Hard Drive – RAID 5 |
| Netzwerkcontroller | Auf dem Board: 2 x 1 Gb Broadcom BCM5720<br>On-Board LOM: iDRAC Port-Karte 1 GB Broadcom BCM5720 <br><br>Extern: 1 x Intel Ethernet i350 QP 1 GB-Serveradapter, niedriges Profil |
| Verwaltung | iDRAC nine Enterprise |
| Gerätezugriff | Zwei USB 3.0 (hinten) <br> Einen USB 3.0 (vorne) |
| Leistung | Dual Hot Plug-Stromversorgung 350 W |
| Rackbefestigung | ReadyRails II-Gleitschienen zur werkzeuglosen Montage in 4-Säulen-Racks mit quadratischen oder gewindelosen Rundlöchern oder zur werkzeuggestützten Montage in 4-Säulen-Racks mit Gewindelöchern, mit Befestigung für einen optionalen werkzeuglosen Kabelführungsarm. |

## <a name="dell-r340-bom"></a>Dell R340 BOM

:::image type="content" source="media/how-to-prepare-your-network/enterprise-deployment-for-azure-defender-for-iot-dell-r340-bom.png" alt-text="Dell R340 BOM.":::

## <a name="next-steps"></a>Nächste Schritte

[Informationen zur Installation von Azure Defender für IoT](how-to-install-software.md)

[Informationen zur Netzwerkeinrichtung von Azure Defender für IoT](how-to-set-up-your-network.md)
