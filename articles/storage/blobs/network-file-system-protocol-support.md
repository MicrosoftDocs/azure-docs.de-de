---
title: Unterstützung für Network File System 3.0 in Azure Blob Storage (Vorschau) | Microsoft-Dokumentation
description: Blob Storage unterstützt jetzt das NFS 3.0-Protokoll (Network File System). Diese Unterstützung ermöglicht es Windows- und Linux-Clients, einen Container in Blob Storage von einem virtuellen Azure-Computer (VM) oder einem lokalen Computer einzubinden.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 08/04/2020
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: b4146f11804dd11f7dd0f5536bc040cb30e25b03
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96902730"
---
# <a name="network-file-system-nfs-30-protocol-support-in-azure-blob-storage-preview"></a>Unterstützung für Network File System 3.0 (NFS) in Azure Blob Storage (Vorschau)

Blob Storage unterstützt jetzt das NFS 3.0-Protokoll (Network File System). Diese Unterstützung ermöglicht es Windows- und Linux-Clients, einen Container in Blob Storage von einem virtuellen Azure-Computer (VM) oder einem lokalen Computer einzubinden. 

> [!NOTE]
> Die Unterstützung für das NFS 3.0-Protokoll in Azure Blob Storage befindet in der öffentlichen Vorschau. Das Protokoll unterstützt GPV2-Speicherkonten mit der Leistungsstufe „Standard“ in den folgenden Regionen: „Australien, Osten“, „Südkorea, Mitte“ und „USA, Süden-Mitte“. Die Vorschau unterstützt auch Blockblobs mit der Leistungsstufe „Premium“ in allen öffentlichen Regionen.

## <a name="general-workflow-mounting-a-storage-account-container"></a>Allgemeiner Workflow: Einbinden eines Speicherkontocontainers

Zum Einbinden eines Speicherkontocontainers müssen Sie diese Schritte ausführen.

1. Registrieren des NFS 3.0-Protokollfeatures in Ihrem Abonnement.

2. Überprüfen, ob das Feature registriert ist.

3. Erstellen eines virtuellen Azure-Netzwerks (VNet).

4. Konfigurieren der Netzwerksicherheit.

5. Erstellen und Konfigurieren des Speicherkontos, das nur Datenverkehr aus dem VNet akzeptiert.

6. Erstellen eines Containers im Speicherkonto.

7. Einbinden des Containers.

Eine Schrittanleitung finden Sie unter [Einbinden von Blob-Speicher unter Linux mithilfe des NFS 3.0-Protokolls (Network File System) (Vorschau)](network-file-system-protocol-support-how-to.md).

> [!IMPORTANT]
> Es ist wichtig, diese Aufgaben in der aufgeführten Reihenfolge durch zuführen. Sie können keine Container einbinden, die Sie erstellen, bevor Sie das NFS 3.0-Protokoll für Ihr Konto aktivieren. Außerdem können Sie das NFS 3.0-Protokoll nach seiner Aktivierung für Ihr Konto nicht mehr deaktivieren.

## <a name="network-security"></a>Netzwerksicherheit

Ihr Speicherkonto muss in einem VNet sein. Ein VNet ermöglicht Clients eine sichere Verbindung mit Ihrem Speicherkonto. Die einzige Möglichkeit zum Sichern der Daten in Ihrem Konto besteht darin, ein VNet und andere Netzwerksicherheitseinstellungen zu verwenden. Alle anderen Tools, die zum Sichern von Daten verwendet werden, wie Kontoschlüsselautorisierung, Azure Active Directory (AD) und Zugriffssteuerungslisten (Access Control Lists, ACLs), werden in Konten, für die die Unterstützung des NFS 3.0-Protokolls aktiviert ist, noch nicht unterstützt. 

Weitere Informationen finden Sie unter [Netzwerksicherheitsempfehlungen für Blob-Speicher](security-recommendations.md#networking).

## <a name="supported-network-connections"></a>Unterstützte Netzwerkverbindungen

Ein Client kann über einen öffentlichen oder einen [privaten Endpunkt](../common/storage-private-endpoints.md) sowie von jeder der folgenden Netzwerkadressen aus eine Verbindung herstellen:

- Das VNet, das Sie für Ihr Speicherkonto konfigurieren. 

  Im Rahmen dieses Artikels bezeichnen wir dieses VNet als das *primäre VNet*. Weitere Informationen finden Sie unter [Gewähren des Zugriffs aus einem virtuellen Netzwerk](../common/storage-network-security.md#grant-access-from-a-virtual-network).

- Ein Peer-VNet, das sich in derselben Region wie das primäre VNet befindet.

  Sie müssen Ihr Speicherkonto so konfigurieren, dass es den Zugriff auf dieses Peer-VNet zulässt. Weitere Informationen finden Sie unter [Gewähren des Zugriffs aus einem virtuellen Netzwerk](../common/storage-network-security.md#grant-access-from-a-virtual-network).

- Ein lokales Netzwerk, das mit Ihrem primären VNet über ein [VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) oder ein [ExpressRoute-Gateway](../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md) verbunden ist. 

  Weitere Informationen finden Sie unter [Konfigurieren des Zugriffs aus lokalen Netzwerken](../common/storage-network-security.md#configuring-access-from-on-premises-networks).

- Ein lokales Netzwerk, das mit einem Peer-Netzwerk verbunden ist.

  Dies kann mithilfe eines [VPN Gateways](../../vpn-gateway/vpn-gateway-about-vpngateways.md) oder eines [ExpressRoute-Gateways](../../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md) zusammen mit [Gatewaytransit](/azure/architecture/reference-architectures/hybrid-networking/vnet-peering#gateway-transit) erfolgen. 

> [!IMPORTANT]
> Wenn Sie eine Verbindung aus einem lokalen Netzwerk heraus herstellen, stellen Sie sicher, dass Ihr Client die ausgehende Kommunikation über die Ports 111 und 2048 zulässt. Das NFS 3.0-Protokoll verwendet diese Ports.

## <a name="azure-storage-features-not-yet-supported"></a>Noch nicht unterstützte Azure Storage-Funktionen

Die folgenden Azure Storage-Funktionen werden nicht unterstützt, wenn Sie das NFS 3.0-Protokoll für Ihr Konto aktivieren. 

- Azure Active Directory (AD)-Sicherheit

- POSIX-ähnliche Zugriffssteuerungslisten (Access Control Lists, ACLs)

- Die Möglichkeit zum Aktivieren der NFS 3.0-Unterstützung für vorhandene Speicherkonten

- Die Möglichkeit zum Deaktivieren der NFS 3.0-Unterstützung in einem Speicherkonto (nachdem Sie sie aktiviert haben)

- Die Möglichkeit zum Schreiben in Blobs mithilfe von REST-APIs oder SDKs. 
  
## <a name="nfs-30-features-not-yet-supported"></a>Noch nicht unterstützte NFS 3.0-Funktionen

Die folgenden NFS 3.0-Funktionen werden noch nicht mit Azure Data Lake Storage Gen2 unterstützt.

- NFS 3.0 über UDP. Nur NFS 3.0 über TCP wird unterstützt.

- Sperren von Dateien mithilfe des Netzwerksperrungs-Managers (NLM). Einbindungsbefehle müssen den `-o nolock`-Parameter enthalten.

- Einbinden von Unterverzeichnissen. Sie können nur das Stammverzeichnis (Container) einbinden.

- Auflisten von Einbindungen (z. B. mit dem Befehl `showmount -a`)

- Auflisten von Exporten (z. B. mit dem Befehl `showmount -e`)

- Schreibgeschütztes Exportieren eines Containers

## <a name="pricing"></a>Preise

Während der Vorschauphase werden die Daten, die in Ihrem Speicherkonto gespeichert sind, mit dem gleichen Kapazitätssatz abgerechnet, der für den Blob-Speicher pro GB pro Monat berechnet wird. 

Für eine Transaktion fallen während der Vorschauphase keine Kosten an. Die Preise für Transaktionen können sich ändern und werden zum Zeitpunkt der allgemeinen Verfügbarkeit festgelegt.

## <a name="next-steps"></a>Nächste Schritte

Informationen zu den ersten Schritten finden Sie unter [Einbinden von Blob-Speicher mithilfe des NFS 3.0-Protokolls (Network File System) (Vorschau)](network-file-system-protocol-support-how-to.md).