---
title: include file
description: include file
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 02/05/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: eea8cb61ce98b2394abff6995e5cc89f00a7cf46
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99628904"
---
1. Wählen Sie auf der Seite für Ihr virtuelles WAN die Option **Benutzer-VPN-Konfigurationen** aus.
1. Wählen Sie auf der Seite **Benutzer-VPN-Konfigurationen** eine Konfiguration und anschließend **VPN-Profil für Virtual WAN-Benutzer herunterladen** aus. Beim Herunterladen der Konfiguration auf WAN-Ebene wird ein integriertes Benutzer-VPN-Profil bereitgestellt, das auf Traffic Manager basiert. Weitere Informationen zu globalen oder hubbasierten Profilen finden Sie unter [Herunterladen eines globalen oder hubbasierten Profils für Benutzer-VPN-Clients](../articles/virtual-wan/global-hub-profile.md). Failoverszenarien werden durch ein globales Profil vereinfacht.

   
   Wenn ein Hub aus irgendeinem Grund nicht verfügbar ist, stellt die integrierte Datenverkehrsverwaltung des Diensts die Konnektivität mit Azure-Ressourcen für Point-to-Site-Benutzer (über einen anderen Hub) sicher. Sie können jederzeit eine hubspezifische VPN-Konfiguration herunterladen, indem Sie zum entsprechenden Hub navigieren. Laden Sie unter **Benutzer-VPN (Point-to-Site)** das Profil **Benutzer-VPN** für den virtuellen Hub herunter.
1. Wählen Sie auf der Seite **VPN-Profil für Virtual WAN-Benutzer herunterladen** den **Authentifizierungstyp** und anschließend **Profil generieren und herunterladen** aus. Das Profilpaket wird generiert, und eine ZIP-Datei mit den Konfigurationseinstellungen wird heruntergeladen.
