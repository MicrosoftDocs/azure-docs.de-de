---
title: 'Generieren und Exportieren von Zertifikaten für Point-to-Site-Verbindungen: Linux: CLI: Azure | Microsoft-Dokumentation'
description: Erstellen eines selbstsignierten Stammzertifikats, Exportieren des öffentlichen Schlüssels und Generieren von Clientzertifikaten mit der Linux-Befehlszeilenschnittstelle (strongSwan)
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/31/2019
ms.author: cherylmc
ms.openlocfilehash: b673be47d4951adab08f04efc56410095f549356
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60766167"
---
# <a name="generate-and-export-certificates"></a>Generieren und Exportieren von Zertifikaten

Punkt-zu-Standort-Verbindungen verwenden Zertifikate zur Authentifizierung. In diesem Artikel erfahren Sie, wie Sie mithilfe der Linux-Befehlszeilenschnittstelle und strongSwan ein selbstsigniertes Stammzertifikat erstellen und Clientzertifikate generieren. Anleitungen für andere Zertifikate finden Sie in den Artikeln zu [PowerShell](vpn-gateway-certificates-point-to-site.md) und [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md). Informationen zum Installieren von strongSwan mithilfe der grafischen Benutzeroberfläche anstelle der CLI finden Sie in den Schritten im Artikel [Clientkonfiguration](point-to-site-vpn-client-configuration-azure-cert.md#install).

## <a name="generate-and-export"></a>Generieren und Exportieren
[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit Ihrer Point-to-Site-Konfiguration zum [Erstellen und Installieren von VPN-Clientkonfigurationsdateien](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli) fort.
