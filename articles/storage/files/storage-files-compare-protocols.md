---
title: Verfügbare Azure Files-Protokolle – NFS und SMB
description: Erfahren Sie mehr über die verfügbaren Protokolle, bevor Sie eine Azure-Dateifreigabe erstellen, einschließlich Server Message Block (SMB) und Network File System (NFS).
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 288d1740c1c4c67dd8756de5fc0652966ae89488
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96620973"
---
# <a name="azure-file-share-protocols"></a>Azure-Dateifreigabeprotokolle

Azure Files bietet zwei Protokolle zum Verbinden und Einbinden Ihrer Azure-Dateifreigaben. Das [Server Message Block(SMB)-Protokoll](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) und das [Network File System(NFS)-Protokoll](https://en.wikipedia.org/wiki/Network_File_System) (Vorschau). Azure Files unterstützt derzeit nicht den Zugriff auf mehrere Protokolle, daher kann eine Freigabe entweder eine NFS-Freigabe oder eine SMB-Freigabe sein. Aus diesem Grund wird empfohlen, vor dem Erstellen von Azure-Dateifreigaben zu bestimmen, welches Protokoll Ihren Anforderungen am besten entspricht.

## <a name="differences-at-a-glance"></a>Im Vergleich

|Komponente  |NFS (Vorschau)  |SMB  |
|---------|---------|---------|
|Zugriffsprotokolle     |NFS 4.1         |SMB 2.1, SMB 3.0         |
|Unterstütztes Betriebssystem     |Linux-Kernelversion 4.3 und höher         |Windows 2008 R2 und höher, Linux-Kernel Version 4.11 und höher         |
|[Verfügbare Tarife](storage-files-planning.md#storage-tiers)     |Storage Premium         |Storage Premium, transaktionsoptimiert, heiß, kalt         |
|[Redundanz](storage-files-planning.md#redundancy)     |LRS, ZRS         |LRS, ZRS, GRS         |
|Authentifizierung     |Nur Host-basierte Authentifizierung        |Identitätsbasierte Authentifizierung, benutzerbasierte Authentifizierung         |
|Berechtigungen     |Berechtigungen im UNIX-Format         |Berechtigungen im NTFS-Format         |
|Dateisystemsemantik     |POSIX-kompatibel         |Nicht POSIX-kompatibel         |
|Groß- und Kleinschreibung     |Groß-/Kleinschreibung beachten         |keine Unterscheidung zwischen Groß-/Kleinbuchstaben         |
|Unterstützung fester Links     |Unterstützt         |Nicht unterstützt         |
|Unterstützung symbolischer Verknüpfungen     |Unterstützt         |Nicht unterstützt         |
|Löschen oder Ändern geöffneter Dateien     |Unterstützt         |Nicht unterstützt         |
|Sperren     |Netzwerksperrungs-Manager im Bytebereich (Empfehlung)         |Unterstützt         |
|Sichere Auflistung öffentlicher IP-Adressen | Nicht unterstützt | Unterstützt|
|Protokollinteroperabilität| Nicht unterstützt | FileREST|

## <a name="nfs-shares-preview"></a>NFS-Freigaben (Vorschau)

Das Einbinden von Azure-Dateifreigaben mit NFS 4.1 befindet sich aktuell in der Vorschauphase. Die Vorschauversion bietet eine engere Integration mit Linux. Dabei handelt es sich um ein vollständig POSIX-kompatibles Angebot, ein Standard für Varianten von UNIX und anderen *nix-basierten Betriebssystemen. Dieser Dateispeicherdienst auf Unternehmensniveau wird zentral hochskaliert, um Ihren Speicherbedarf gerecht zu werden, und aktuell können Tausende von Compute-Instanzen gleichzeitig darauf zugreifen.

### <a name="limitations"></a>Einschränkungen

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

#### <a name="regional-availability"></a>Regionale Verfügbarkeit

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

### <a name="best-suited"></a>Passende Szenarios

NFS mit Azure Files eignet sich ideal für folgende Szenarios:

- für Workloads, die POSIX-kompatible Dateifreigaben erfordern, Szenarios, in denen die Groß-/Kleinschreibung wichtig ist oder bei Berechtigungen im UNIX-Format
- für Linux-basierte Workloads, die keinen Windows-Zugriff erfordern

### <a name="security"></a>Sicherheit

Alle Azure Files-Daten werden im Ruhezustand verschlüsselt. Für die Verschlüsselung während der Übertragung bietet Azure eine Verschlüsselungsschicht für alle Daten, die zwischen Azure-Rechenzentren übertragen werden: [MACSec](https://en.wikipedia.org/wiki/IEEE_802.1AE). Dadurch ist eine Verschlüsselung möglich, wenn Daten zwischen Azure-Rechenzentren übertragen werden. Anders als Azure Files-Dateien mit dem SMB-Protokoll bieten Dateifreigaben, die das NFS-Protokoll verwenden, keine benutzerbasierte Authentifizierung. Die Authentifizierung für NFS-Freigaben basiert auf den konfigurierten Netzwerksicherheitsregeln. Aus diesem Grund müssen Sie entweder Dienstendpunkte oder private Endpunkte verwenden, um sicherzustellen, dass nur sichere Verbindungen mit der NFS-Freigabe hergestellt werden. Wenn Sie lokal auf Freigaben zugreifen möchten, müssen Sie zusätzlich zu einem privaten Endpunkt ein VPN oder eine ExpressRoute-Verbindung einrichten. Anforderungen, die nicht von den folgenden Quellen ausgehen, werden abgelehnt:

- [Anforderungen von einem privaten Endpunkt](storage-files-networking-overview.md#private-endpoints)
- [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
    - [Anforderungen von einem Point-to-Site-VPN](../../vpn-gateway/point-to-site-about.md)
    - [Standort-zu-Standort](../../vpn-gateway/design.md#s2smulti)
- [ExpressRoute](../../expressroute/expressroute-introduction.md)
- [Anforderungen von einem eingeschränkten öffentlichen Endpunkt](storage-files-networking-overview.md#storage-account-firewall-settings)

Weitere Informationen zu den verfügbaren Netzwerkoptionen finden Sie im Artikel zu [Azure Files-Netzwerken](storage-files-networking-overview.md).

## <a name="smb-shares"></a>SMB-Freigaben

Azure-Dateifreigaben, die mit dem SMB bereitgestellt werden, bieten mehr Azure Files-Features und haben keine Funktionseinschränkungen, da Sie allgemein verfügbar sind.

### <a name="features"></a>Features

- Azure-Dateisynchronisierung
- Identitätsbasierte Authentifizierung
- Azure Backup-Unterstützung
- Momentaufnahmen
- Vorläufiges Löschen
- Verschlüsselung während der Übertragung und Verschlüsselung ruhender Daten

### <a name="best-suited"></a>Passende Szenarios

Der SMB mit Azure Files eignet sich ideal für folgende Szenarios:

- Produktionsumgebungen
- Kunden, die eine der [oben aufgelisteten](#features) Funktionen benötigen

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen einer NFS-Dateifreigabe](storage-files-how-to-create-nfs-shares.md)
- [Erstellen einer SMB-Dateifreigabe“](storage-how-to-create-file-share.md)
