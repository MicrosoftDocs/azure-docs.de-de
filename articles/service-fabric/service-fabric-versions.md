---
title: Unterstützte Clusterversionen in Azure Service Fabric
description: Erfahren Sie mehr über Clusterversionen in Azure Service Fabric, einschließlich eines Links zu den neuesten Releases des Service Fabric-Teamblogs.
ms.topic: troubleshooting
ms.date: 06/15/2020
ms.openlocfilehash: 46b1a7845a0da1ede30ed450f1e673f40e450105
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905382"
---
# <a name="supported-service-fabric-versions"></a>Unterstützte Service Fabric-Versionen

Achten Sie darauf, dass in Ihrem Cluster immer eine unterstützte Azure Service Fabric-Version ausgeführt wird. Nach der Ankündigung einer neuen Service Fabric-Version beträgt die verbleibende Supportdauer für die vorherige Version noch mindestens 60 Tage. Ankündigungen neuer Versionen finden Sie im [Blog des Service Fabric-Teams](https://azure.microsoft.com/updates/?product=service-fabric).

Für eine bestimmte Version der Service Fabric-Runtime können Sie die angegebene oder ältere Versionen der SDK/NuGet-Pakete verwenden. Neuere Versionen der Pakete werden nicht unterstützt und weisen möglicherweise Probleme beim Umgang mit älteren Clustern auf, da sie Feature- oder Protokolländerungen aufweisen können, die von diesen Umgebungen nicht unterstützt werden.

In den folgenden Dokumenten finden Sie Details dazu, wie Sie sicherstellen können, dass in Ihrem Cluster immer eine unterstützte Service Fabric-Version ausgeführt wird:

- [Upgrade von Azure Service Fabric-Clustern](service-fabric-cluster-upgrade.md)
- [Upgraden der in Ihrem Cluster ausgeführten Service Fabric-Version](service-fabric-cluster-upgrade-windows-server.md)

## <a name="supported-versions"></a>Unterstützte Versionen

Die folgende Tabelle enthält die Versionen von Service Fabric und das jeweilige Datum des Ablaufs des Supports.

| Service Fabric-Runtime im Cluster | Kann direkt von Clusterversion aktualisiert werden |Kompatible SDK- oder NuGet-Paket-Version | Ende des Supports |
| --- | --- |--- | --- |
| Alle Clusterversionen vor 5.3.121 | 5.1.158.* |Alle Versionen bis einschließlich Version 2.3 |20. Januar 2017 |
| 5.3.* | 5.1.158.* |Alle Versionen bis einschließlich Version 2.3 |24. Februar 2017 |
| 5.4.* | 5.1.158.* |Alle Versionen bis einschließlich Version 2.4 |10. Mai 2017       |
| 5.5.* | 5.4.164.* |Alle Versionen bis einschließlich Version 2.5 |10. August 2017    |
| 5.6.* | 5.4.164.* |Alle Versionen bis einschließlich Version 2.6 |13. Oktober 2017   |
| 5.7.* | 5.4.164.* |Alle Versionen bis einschließlich Version 2.7 |15. Dezember 2017  |
| 6.0.* | 5.6.205.* |Alle Versionen bis einschließlich Version 2.8 |30. März 2018     |
| 6.1.* | 5.7.221.* |Alle Versionen bis einschließlich Version 3.0 |15. Juli 2018      |
| 6.2.* | 6.0.232.* |Alle Versionen bis einschließlich Version 3.1 |26. Oktober 2018   |
| 6.3.* | 6.1.480.* |Alle Versionen bis einschließlich Version 3.2 |31. März 2019  |
| 6.4.* | 6.2.301.* |Alle Versionen bis einschließlich Version 3.3 |15. September 2019 |
| 6.5.* | 6.4.617.* |Alle Versionen bis einschließlich Version 3.4 |1\. August 2020 |
| 7.0.466.* | 6.4.664.* |Alle Versionen bis einschließlich Version 4.0|31. Januar 2021  |
| 7.0.466.* | 6.5.* |Alle Versionen bis einschließlich Version 4.0|31. Januar 2021 |
| 7.0.470.* | 7.0.466.* |Alle Versionen bis einschließlich Version 4.0 |31. Januar 2021  |
| 7.0.472.* | 7.0.466.* |Alle Versionen bis einschließlich Version 4.0 |31. Januar 2021  |
| 7.0.478.* | 7.0.466.* |Alle Versionen bis einschließlich Version 4.0 |31. Januar 2021  |
| 7.1.409.* | 7.0.466.* |Alle Versionen bis einschließlich Version 4.1 |31. März 2021 |
| 7.1.417.* | 7.0.466.* |Alle Versionen bis einschließlich Version 4.1 |31. März 2021 |
| 7.1.428.* | 7.0.466.* |Alle Versionen bis einschließlich Version 4.1 |31. März 2021 |
| 7.1.456.* | 7.0.466.* |Alle Versionen bis einschließlich Version 4.1 |31. März 2021 |
| 7.1.458.* | 7.0.466.* |Alle Versionen bis einschließlich Version 4.1 |31. März 2021 |
| 7.1.459.* | 7.0.466.* |Alle Versionen bis einschließlich Version 4.1 |31. März 2021 |
| 7.1.503.* | 7.0.466.* |Alle Versionen bis einschließlich Version 4.1 |31. März 2021 |
| 7.2.413.* | 7.0.470.* |Alle Versionen bis einschließlich Version 4.2 |Aktuelle Version, daher kein Enddatum |
| 7.2.432.* | 7.0.470.* |Alle Versionen bis einschließlich Version 4.2 |Aktuelle Version, daher kein Enddatum |
| 7.2.433.* | 7.0.470.* |Alle Versionen bis einschließlich Version 4.2 |Aktuelle Version, daher kein Enddatum |
| 7.2.445.* | 7.0.470.* |Alle Versionen bis einschließlich Version 4.2 |Aktuelle Version, daher kein Enddatum |

## <a name="supported-operating-systems"></a>Unterstützte Betriebssysteme

Die folgende Tabelle enthält die unterstützten Betriebssysteme für die unterstützten Service Fabric-Versionen.

| Betriebssystem | Erste unterstützte Service Fabric-Version |
| --- | --- |
| Windows Server 2012 R2 | Alle Versionen |
| Windows Server 2016 | Alle Versionen |
| Windows Server 1709 | 6.0 |
| Windows Server 1803 | 6.4 |
| Windows Server 1809 | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16.04 | 6.0 |
| Linux Ubuntu 18.04 | 7.1 |

## <a name="supported-version-names"></a>Unterstützte Versionsnamen

In der folgende Tabelle werden die Versionsnamen von Service Fabric und die zugehörigen Versionsnummern aufgeführt.

| Versionsname | Windows-Versionsnummer | Linux-Versionsnummer |
| --- | --- | --- |
| 5.3 RTO | 5.3.121.9494 | Nicht verfügbar |
| 5.3 CU1 | 5.3.204.9494 | Nicht verfügbar |
| 5.3 CU2 | 5.3.301.9590 | Nicht verfügbar |
| 5.3 CU3 | 5.3.311.9590 | Nicht verfügbar |
| 5.4 CU2 | 5.4.164.9494 | Nicht verfügbar |
| 5.5 CU1 | 5.5.216.0    | Nicht verfügbar |
| 5.5 CU2 | 5.5.219.0    | Nicht verfügbar |
| 5.5 CU3 | 5.5.227.0    | Nicht verfügbar |
| 5.5 CU4 | 5.5.232.0    | Nicht verfügbar |
| 5.6 RTO | 5.6.204.9494 | Nicht verfügbar |
| 5.6 CU2 | 5.6.210.9494 | Nicht verfügbar |
| 5.6 CU3 | 5.6.220.9494 | Nicht verfügbar |
| 5.7 RTO | 5.7.198.9494 | Nicht verfügbar |
| 5.7 CU4 | 5.7.221.9494 | Nicht verfügbar |
| 6.0 RTO | 6.0.211.9494 | 6.0.120.1 |
| 6.0 CU1 | 6.0.219.9494 | 6.0.127.1 |
| 6.0 CU2 | 6.0.232.9494 | 6.0.133.1 |
| 6.1 CU1 | 6.1.456.9494 | 6.1.183.1 |
| 6.1 CU2 | 6.1.467.9494 | 6.1.185.1 |
| 6.1 CU3 | 6.1.472.9494 | Nicht verfügbar |
| 6.1 CU4 | 6.1.480.9494 | 6.1.187.1 |
| 6.2 RTO | 6.2.269.9494 | 6.2.184.1 | 
| 6.2 CU1 | 6.2.274.9494 | 6.2.191.1 |
| 6.2 CU2 | 6.2.283.9494 | 6.2.194.1 |
| 6.2 CU3 | 6.2.301.9494 | 6.2.199.1 |
| 6.3 RTO | 6.3.162.9494 | 6.3.119.1 |
| 6.3 CU1 | 6.3.176.9494 | 6.3.124.1 |
| 6.3 CU1 | 6.3.187.9494 | 6.3.129.1 |
| 6.4 RTO | 6.4.617.9590 | 6.4.625.1 |
| 6.4 CU2 | 6.4.622.9590 | Nicht verfügbar |
| 6.4 CU3 | 6.4.637.9590 | 6.4.634.1 |
| 6.4 CU4 | 6.4.644.9590 | 6.4.639.1 |
| 6.4 CU5 | 6.4.654.9590 | 6.4.649.1 |
| 6.4 CU6 | 6.4.658.9590 | Nicht verfügbar |
| 6.4 CU7 | 6.4.664.9590 | 6.4.661.1 |
| 6.4 CU8 | 6.4.670.9590 | Nicht verfügbar |
| 6.5 RTO | 6.5.639.9590 | 6.5.435.1 |
| 6.5 CU1 | 6.5.641.9590 | 6.5.454.1 |
| 6.5 CU2 | 6.5.658.9590 | 6.5.460.1 |
| 6.5 CU3 | 6.5.664.9590 | 6.5.466.1 |
| 6.5 CU5 | 6.5.676.9590 | 6.5.467.1 |
| 7.0 RTO | 7.0.457.9590 | 7.0.457.1 |
| 7.0 CU2 | 7.0.464.9590 | 7.0.464.1 |
| 7.0 CU3 | 7.0.466.9590 | 7.0.465.1 |
| 7.0 CU4 | 7.0.470.9590 | 7.0.469.1 |
| 7.0 CU6 | 7.0.472.9590 | 7.0.471.1 |
| 7.0 CU9 | 7.0.478.9590 | 7.0.472.1 |
| 7.1 RTO | 7.1.409.9590 | 7.1.410.1 |
| 7.1 CU1 | 7.1.417.9590 | 7.1.418.1 |
| 7.1 CU2 | 7.1.428.9590 | 7.1.428.1 |
| 7.1 CU3 | 7.1.456.9590 | 7.1.452.1 |
| 7.1 CU5 | 7.1.458.9590 | 7.1.454.1 |
| 7.1 CU6 | 7.1.459.9590 | 7.1.455.1 |
| 7.1 CU8 | 7.1.503.9590 | 7.1.508.1 |
| 7.2 RTO | 7.2.413.9590 | Nicht verfügbar |
| 7.2 CU2 | 7.2.432.9590 | 7.2.431.1 |
| 7.2 CU3 | 7.2.433.9590 | Nicht verfügbar |
| 7.2 CU4 | 7.2.445.9590 | 7.2.447.1 |

