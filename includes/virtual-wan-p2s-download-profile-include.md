---
title: include file
description: include file
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 317d75dae83e8529bda25897b77824d8cd36e72e
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95553873"
---
1. Wählen Sie auf der Seite für Ihr virtuelles WAN die Option **Benutzer-VPN-Konfigurationen** aus.
1. Wählen Sie oben auf der Seite die Option **Benutzer-VPN-Konfiguration herunterladen** aus. Beim Herunterladen der Konfiguration auf WAN-Ebene wird ein integriertes Benutzer-VPN-Profil bereitgestellt, das auf Traffic Manager basiert. Weitere Informationen zu globalen oder hubbasierten Profilen finden Sie unter [Herunterladen eines globalen oder hubbasierten Profils für Benutzer-VPN-Clients](../articles/virtual-wan/global-hub-profile.md). Failoverszenarien werden durch ein globales Profil vereinfacht.

   Wenn ein Hub aus irgendeinem Grund nicht verfügbar ist, stellt die integrierte Datenverkehrsverwaltung des Diensts die Konnektivität mit Azure-Ressourcen für Point-to-Site-Benutzer (über einen anderen Hub) sicher. Sie können jederzeit eine hubspezifische VPN-Konfiguration herunterladen, indem Sie zum entsprechenden Hub navigieren. Laden Sie unter **Benutzer-VPN (Point-to-Site)** das Profil **Benutzer-VPN** für den virtuellen Hub herunter.
1. Klicken Sie nach Abschluss der Erstellung der Datei auf den Link, um die Datei herunterzuladen.
1. Verwenden Sie die Profildatei, um die VPN-Clients zu konfigurieren.