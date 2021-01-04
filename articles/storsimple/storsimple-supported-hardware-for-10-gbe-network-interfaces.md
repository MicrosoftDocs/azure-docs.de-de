---
title: Hardware für StorSimple-10-GbE-Schnittstellen | Microsoft Docs
description: Beschreibt die unterstützten SFP-Transceiver, Kabel und Switches für 10-GbE-Netzwerkschnittstellen auf Ihrem StorSimple-Gerät.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: df8d40c7-f5ad-4f84-93eb-779fbd5f7243
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/21/2016
ms.author: alkohli
ms.openlocfilehash: fd30c7ec15eadd3d7945349a8aae4ac824bfdc7f
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94954085"
---
# <a name="supported-hardware-for-the-10-gbe-network-interfaces-on-your-storsimple-device"></a>Unterstützte Hardware für 10-GbE-Netzwerkschnittstellen auf Ihrem StorSimple-Gerät

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Übersicht
Dieser Artikel enthält Informationen zu zusätzlicher Hardware, die mit dem Microsoft Azure StorSimple-Gerät verwendet werden kann.

## <a name="list-of-devices-tested-by-microsoft"></a>Liste der Geräte, die von Microsoft getestet wurden
Microsoft hat die folgenden SFP (Small Form-factor Pluggable)-Transceiver, Kabel und Switches getestet, um sicherzustellen, dass sie optimal mit Geräten funktionieren. (Die folgenden Tabellen werden aktualisiert, wenn neue Hardware getestet wird.)

### <a name="sfp-transceivers"></a>SFP+-Transceiver
| Make | Modell |
| --- | --- |
| Cisco |SFP-10G-SR |

### <a name="cables"></a>Kabel
| S. Nein. | Make | Modell |
| --- | --- | --- |
| 1. |Cisco |SFP-H10GB-CU1M |
| 2. |Cisco |SFP-H10GB-CU2M |
| 3. |Cisco |SFP-H10GB-CU3M |
| 4. |Tripp Lite |N820-05M (OM3) |

### <a name="switches"></a>Switches
| S. Nein. | Make | Modell |
| --- | --- | --- |
| 1. |Cisco |N3K-C3172PQ-10GE |
| 2. |Cisco |N3K-C3048-ZM-F |
| 3. |Cisco |N5K-C5596UP-FA |

## <a name="list-of-devices-tested-in-the-field"></a>Liste der Geräte, die in der Praxis getestet wurden
Dieser Abschnitt enthält eine Liste der Geräte, die von StorSimple-Kunden erfolgreich in der Praxis eingesetzt wurden. Diese wurden nicht von Microsoft getestet, funktionieren aber wahrscheinlich mit dem StorSimple-Gerät.

| Parameter | value |
| --- | --- |
| Switch-Fabrikat |Juniper |
| Switch-Modell |ex4550-32F |
| Switch-Betriebssystemversion |JunOS 12.3R9.4 |
| Blade-Modell |Integrierte Ports (PIC 0) |
| Transceiver-Fabrikat |Juniper |
| Transceiver-Modell |Teilenummer 740-021308 <br></br> Teilenummer 740-030658 |
| Transceiver-Firmwareversion |Rev 01 Version 0.0 (gemeldet) |
| Kabelmodell |Duplex Jumper LC/LC 50/125µ,   OM3, LSZH |
| StorSimple-Modell |8600 |
| StorSimple-Softwareversion |6.3.9600.17491 |

## <a name="list-of-devices-tested-by-oem-provider-mellanox"></a>Liste der Geräte, die von OEM-Anbieter getestet wurden (Mellanox)
Mellanox hat die folgenden SFP (Small Form-factor Pluggable)-Transceiver, Kabel und Switches getestet, um sicherzustellen, dass sie optimal mit Netzwerkschnittstellen von Mellanox, beispielsweise den 10-GbE-Netzwerkschnittstellen Ihres StorSimple-Geräts, funktionieren.

### <a name="cables-and-modules-supported-by-mellanox"></a>Von Mellanox unterstützte Kabel und Module
Die folgende Tabelle enthält die Kabel und Module, die von Mellanox unterstützt werden. Diese wurden nicht von Microsoft getestet, funktionieren aber wahrscheinlich mit dem StorSimple-Gerät.

| S. Nein. | Geschwindigkeit | Modell | BESCHREIBUNG | Make |
| --- | --- | --- | --- | --- |
| 1. |10 GbE |CAB-SFP-SFP-1M |Passives Kupferkabel, SFP+, 10 GBit/s (1 m) |Arista |
| 2. |10 GbE |CAB-SFP-SFP-2M |Passives Kupferkabel, SFP+, 10 GBit/s (2 m) |Arista |
| 3. |10 GbE |CAB-SFP-SFP-3M |Passives Kupferkabel, SFP+, 10 GBit/s (3 m) |Arista |
| 4. |10 GbE |CAB-SFP-SFP-5M |Passives Kupferkabel, SFP+, 10 GBit/s (5 m) |Arista |
| 5. |10 GbE |Cisco SFP-H10GBCU1M |SFP+-Kabel von Cisco |Cisco |
| 6. |10 GbE |Cisco SFP-H10GBCU3M |SFP+-Kabel von Cisco |Cisco |
| 7. |10 GbE |Cisco SFP-H10GBCU5M |SFP+-Kabel von Cisco |Cisco |
| 8. |10 GbE |J9281B HP X242 10G |SFP+ zu SFP+, Direktanschluss-Kupferkabel (1 m) |HP |
| 9. |10 GbE |455883-B21 HP BLc |SR SFP+ Opt (10 GBit) |HP |
| 10. |10 GbE |455886-B21 HP BLc |LR SFP + Opt (10 GBit) |HP |
| 11. |10 GbE |487649-B21 HP BLc |SFP+, 10-GbE-Kupferkabel (0,5 m) |HP |
| 12. |10 GbE |487652-B21 HP BLc |SFP+, 10-GbE-Kupferkabel (1 m) |HP |
| 13. |10 GbE |487655-B21 HP BLc |SFP+, 10-GbE-Kupferkabel (3 m) |HP |
| 14. |10 GbE |487658-B21 HP BLc |SFP+, 10-GbE-Kupferkabel (7 m) |HP |
| 15. |10 GbE |537963-B21 HP BLc |SFP+, 10-GbE-Kupferkabel (5 m) |HP |
| 16. |10 GbE |AP784A HP |Passives SFP+-Kupferkabel, C-Serie (3 m) |HP |
| 17. |10 GbE |AP785A HP |Passives SFP+-Kupferkabel, C-Serie (5 m) |HP |
| 18. |10 GbE |AP818A HP |Aktives SFP+-Kupferkabel, B-Serie (1 m) |HP |
| 19. |10 GbE |AP819A HP |Aktives SFP+-Kupferkabel, B-Serie (3 m) |HP |
| 20. |10 GbE |J9150A HP |X 132 10G SFP + LC SR Transceiver |HP |
| 21. |10 GbE |J9151A HP |X132 10G SFP+ LC LR Transceiver |HP |
| 22. |10 GbE |J9283B HP |X242 10G SFP+ SFP+ DAC-Kabel (3 m) |HP |
| 23. |10 GbE |J9285B HP |X242 10G SFP+ SFP+ DAC-Kabel (7 m) |HP |
| 24. |10 GbE |JD095B HP |X240 10G SFP+ SFP+, DAC-Kabel (0,65 m) |HP |
| 25. |10 GbE |JD096B HP |X240 10G SFP+ SFP+ DAC-Kabel (1,2 m) |HP |
| 26. |10 GbE |JD097B HP |X240 10G SFP+ SFP+ DAD-Kabel (3 m) |HP |
| 27. |10 GbE |MAM1Q00A-QSA Mellanox |QSFP-zu-SFP+-Adapter |Mellanox Technologies |
| 28. |10 GbE |MC2309124-006 Mt |Passives Kupferkabel 1x SFP+ zu QSFP 10 GBit/s, 24awg (7 m) |Mellanox Technologies |
| 29. |10 GbE |MC2309124-007 Mt |Passives Kupferkabel 1x SFP+ zu QSFP 10 GBit/s, 24awg (7 m) |Mellanox Technologies |
| 30. |10 GbE |MC2309130-003 Mt |Passives Kupferkabel 1x SFP+ zu QSFP 10 GBit/s, 30 AWG (3 m) |Mellanox Technologies |
| 31. |10 GbE |MC2309130-00A Mt |Passives Kupferkabel 1x SFP+ zu QSFP 10GBit/s, 30 AWG (0,5 m) |Mellanox Technologies |
| 32. |10 GbE |MC3309124-005 Mt |Passives Kupferkabel 1x SFP+ 10 GBit/s, 24 AWG (5 m) |Mellanox Technologies |
| 33. |10 GbE |MC3309124-007 Mt |Passives Kupferkabel 1x SFP+ 10 GBit/s, 24 AWG (7 m) |Mellanox Technologies |
| 34. |10 GbE |MC3309130-003 Mt |Passives Kupferkabel 1x SFP+ 10 GBit/s, 30 AWG (3 m) |Mellanox Technologies |
| 35. |10 GbE |MC3309130-00A Mt |Passives Kupferkabel 1x SFP+ 10 GBit/s, 30 AWG (0,5 m) |Mellanox Technologies |

### <a name="switches-supported-by-mellanox"></a>Liste der von Mellanox unterstützten Switches
Die folgende Tabelle enthält die Switches, die von Mellanox unterstützt werden. Diese wurden nicht von Microsoft getestet, funktionieren aber wahrscheinlich mit dem StorSimple-Gerät.

| S. Nein. | Geschwindigkeit | Modell | BESCHREIBUNG | Make |
| --- | --- | --- | --- | --- |
| 1. |10 GbE |516733-B21 |HP ProCurve 6120XG Ethernet Blade Switch (10 GbE) |HP |
| 2. |10 GbE |538113-B21 |HP 10-GbE-Pass-Through-Modul (PTM) |HP |
| 3. |10 GbE |EN4093 |IBM PureFlex System Fabric EN4093, Skalierbares Switch-Modul (10 Gigabit) |IBM |
| 4. |1 GbE |3020 |Cisco Catalyst 3020 Switch Blade (1 GbE) |Cisco |
| 5. |1 GbE |3020X |Cisco Catalyst 3020X Switch Blade (1 GbE) |Cisco |
| 6. |1 GbE |438030-B21 |HP Switch-Modul (1 GbE) – GbE2c Layer 2/3 Ethernet Blade Switch |HP |
| 7. |1 GbE |6120G |HP ProCurve 6120G/XG Switch Blade (1 GbE) |HP |

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu [StorSimple-Hardwarekomponenten und ihrem Status](./storsimple-8000-monitor-hardware-status.md).