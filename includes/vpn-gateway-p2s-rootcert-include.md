---
title: Includedatei
description: Includedatei
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/11/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4c8e7e5272f180c482ca7fdd44302f49eb888b25
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66157254"
---
Verwenden Sie entweder ein Stammzertifikat, das mit einer Unternehmenslösung generiert wurde (empfohlen), oder generieren Sie ein selbstsigniertes Zertifikat. Nach dem Erstellen des Stammzertifikats exportieren Sie die Daten des öffentlichen Zertifikats (nicht den privaten Schlüssel) als Base64-codierte X.509-CER-Datei. Laden Sie dann Daten des öffentlichen Zertifikats in den Azure-Server hoch.

* **Unternehmenszertifikat:** Bei einer Unternehmenslösung können Sie Ihre vorhandene Zertifikatkette verwenden. Rufen Sie die CER-Datei für das Stammzertifikat ab, das Sie verwenden möchten.
* **Selbstsigniertes Stammzertifikat**: Wenn Sie keine Unternehmenszertifikatlösung verwenden, erstellen Sie ein selbstsigniertes Stammzertifikat. Andernfalls sind die erstellten Zertifikate nicht mit P2S-Verbindungen kompatibel, und auf Clients wird beim Verbindungsversuch ein Verbindungsfehler angezeigt. Sie können Azure PowerShell, MakeCert oder OpenSSL verwenden. Die Schritte in den folgenden Artikeln beschreiben das Generieren eines kompatiblen selbstsignierten Stammzertifikats:

  * [Anweisungen für PowerShell unter Windows 10](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md): Für diese Anweisungen sind Windows 10 und PowerShell erforderlich, um Zertifikate zu generieren. Clientzertifikate, die von der Stammzertifizierungsstelle generiert werden, können auf jedem unterstützten P2S-Client installiert werden.
  * [MakeCert-Anweisungen](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): Verwenden Sie MakeCert, wenn Sie keinen Zugriff auf einen Windows 10-Computer haben, um Zertifikate zu generieren. MakeCert ist zwar veraltet, kann aber trotzdem zum Generieren von Zertifikaten verwendet werden. Clientzertifikate, die Sie vom Stammzertifikat generieren, können auf jedem unterstützten P2S-Client installiert werden.
  * [Linux-Anweisungen](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md)
