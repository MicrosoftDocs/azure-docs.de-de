---
title: Konfigurieren und Anpassen des Sicherheitsmoduls für Azure RTOS
description: Erfahren Sie, wie Sie Ihr Sicherheitsmodul für Azure RTOS konfigurieren und anpassen.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: fb2b7810c0829859f4a104c62b6df2ca0495bac7
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809200"
---
# <a name="configure-and-customize-security-module-for-azure-rtos-preview"></a>Konfigurieren und Anpassen des Sicherheitsmoduls für Azure RTOS (Vorschau)

Verwenden Sie die folgende Datei, um das Verhalten Ihres Geräts zu konfigurieren.

## <a name="azure_iot_security_moduleincasc_porth"></a>azure_iot_security_module/inc/asc_port.h

 Das Standardverhalten jeder Konfiguration wird in den folgenden Tabellen angegeben: 

### <a name="general"></a>Allgemein

| Name | type | Standard | Details |
| - | - | - | - |
| ASC_SECURITY_MODULE_ID | String | --- | Der eindeutige Bezeichner des Geräts  |
| ASC_SECURITY_MODULE_PENDING_TIME  | Nr. | 300 | Zeit in Sekunden, die das Sicherheitsmodul ausstehend ist. Wenn die Zeit überschritten wird, änder sich der Zustand in „Angehalten“. |

#### <a name="collection"></a>Collection

| Name | type | Standard | Details |
| - | - | - | - |
| ASC_HIGH_PRIORITY_INTERVAL | Nr. | 10 | Das Intervall des Collectors für Gruppen mit hoher Priorität in Sekunden. |
| ASC_MEDIUM_PRIORITY_INTERVAL | Nr. | 30 | Das Intervall des Collectors für Gruppen mit mittlerer Priorität in Sekunden. |
| ASC_LOW_PRIORITY_INTERVAL | Nr. | 145,440  | Das Intervall des Collectors für Gruppen mit niedriger Priorität in Sekunden. |

#### <a name="collector-network-activity"></a>Netzwerkaktivität des Collectors

Um die Konfiguration der Netzwerkaktivität Ihres Collectors anzupassen, verwenden Sie Folgendes:

| Name | type | Standard | Details |
| - | - | - | - |
| ASC_COLLECTOR_NETWORK_ACTIVITY_TCP_DISABLED | Boolean | false | `TCP`-Netzwerkaktivität filtern |
| ASC_COLLECTOR_NETWORK_ACTIVITY_UDP_DISABLED | Boolean | false | `UDP`-Netzwerkaktivität filtern |
| ASC_COLLECTOR_NETWORK_ACTIVITY_ICMP_DISABLED | Boolean | false | `ICMP`-Netzwerkaktivitätsereignisse filtern |
| ASC_COLLECTOR_NETWORK_ACTIVITY_CAPTURE_UNICAST_ONLY | Boolean | true | Nur eingehende Unicastpakete erfassen; bei Festlegung auf „false“ ebenfalls Broadcast- und Multicastpakete erfassen. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV4_OBJECTS_IN_CACHE | Nr. | 64 | Maximale Anzahl von IPv4-Netzwerkereignissen, die im Arbeitsspeicher gespeichert werden sollen. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV6_OBJECTS_IN_CACHE | Nr. | 64  | Maximale Anzahl von IPv6-Netzwerk Ereignissen, die im Arbeitsspeicher gespeichert werden sollen. |


## <a name="compile-flags"></a>Kompilierungsflags
Mithilfe von Kompilierungsflags können Sie die vordefinierten Konfigurationen überschreiben.

### <a name="collectors"></a>Collectors
| Name | type | Standard | Details |
| - | - | - | - |
| collector_heartbeat_enabled | Boolean | EIN | Collector für Heartbeat aktivieren |
| collector_network_activity_enabled | Boolean | EIN | Collector für Netzwerkaktivitäten aktivieren |
| collector_system_information_enabled | Boolean | EIN | Collector für Systeminformationen aktivieren |

## <a name="supported-security-alerts-and-recommendations"></a>Unterstützte Sicherheitswarnungen und -empfehlungen

Das Sicherheitsmodul für Azure RTOS unterstützt spezifische Sicherheitswarnungen und -empfehlungen. Stellen Sie sicher, dass Sie die [relevanten Warnungs- und Empfehlungswerte für Ihren Dienst überprüfen und anpassen](concept-rtos-security-alerts-recommendations.md).

## <a name="log-analytics-optional"></a>Log Analytics (optional)

Das Aktivieren und Konfigurieren von Log Analytics ist zwar optional und nicht erforderlich, kann jedoch hilfreich sein, wenn Sie Geräteereignisse und -aktivitäten noch weiter untersuchen möchten. Weitere Informationen zum Einrichten und Verwenden von Log Analytics mit dem Defender für IoT-Dienst finden Sie [hier](how-to-security-data-access.md#log-analytics). 

## <a name="next-steps"></a>Nächste Schritte

- Überprüfen und Anpassen von [Sicherheitswarnungen und -empfehlungen](concept-rtos-security-alerts-recommendations.md) für das Sicherheitsmodul für Azure RTOS
- Lesen Sie bei Bedarf in [Sicherheitsmodul für Azure RTOS-API](azure-rtos-security-module-api.md) nach.

