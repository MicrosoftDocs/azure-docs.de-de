---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/30/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e3ad8fcb6cd5cf68b02ac522d0e5ef5a18ba8a3c
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/01/2020
ms.locfileid: "93144993"
---
Für native Point-to-Site-Verbindungen mit Azure-Zertifikatauthentifizierung werden die folgenden Komponenten verwendet, die Sie in dieser Übung konfigurieren:

* Ein RouteBased-VPN-Gateway.
* Der öffentliche Schlüssel (CER-Datei) für ein Stammzertifikat (in Azure hochgeladen). Sobald das Zertifikat hochgeladen wurde, wird es als vertrauenswürdiges Zertifikat betrachtet und für die Authentifizierung verwendet.
* Ein Clientzertifikat, das von der Stammzertifizierungsstelle generiert wird. Das auf jedem Clientcomputer, für den eine Verbindung mit dem VNET hergestellt wird, installierte Clientzertifikat. Dieses Zertifikat wird für die Clientauthentifizierung verwendet.
* VPN-Clientkonfiguration. Der VPN-Client wird mithilfe von VPN-Clientkonfigurationsdateien konfiguriert. Diese Dateien enthalten die erforderlichen Informationen, damit der Client eine Verbindung mit dem VNET herstellen kann. Die Dateien konfigurieren den vorhandenen nativen VPN-Client des Betriebssystems. Jeder Client, der eine Verbindung herstellt, muss mit den Einstellungen in den Konfigurationsdateien konfiguriert werden.
