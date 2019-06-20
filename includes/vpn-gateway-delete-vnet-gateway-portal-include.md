---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d36d2be59010c47348a8e196b28d87e5b967868e
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177962"
---
### <a name="step-1-navigate-to-the-virtual-network-gateway"></a>Schritt 1: Navigieren zum Gateway für virtuelle Netzwerke

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu **Alle Ressourcen**. 
2. Um die Seite mit dem Gateway für virtuelle Netzwerke zu öffnen, navigieren Sie zu dem Gateway für virtuelle Netzwerke, das Sie löschen möchten, und klicken Sie darauf.

### <a name="step-2-delete-connections"></a>Schritt 2: Löschen von Verbindungen

1. Klicken Sie auf der Seite für das Gateway für virtuelle Netzwerke auf **Verbindungen**, um alle Verbindungen mit dem Gateway anzuzeigen.
2. Klicken Sie auf **...** in der Zeile mit dem Namen der Verbindung, und wählen Sie in der Dropdownliste die Option **Löschen** aus.
3. Klicken Sie auf **Ja**, um zu bestätigen, dass Sie die Verbindung löschen möchten. Wenn Sie über mehrere Verbindungen verfügen, löschen Sie jede Verbindung.

### <a name="step-3-delete-the-virtual-network-gateway"></a>Schritt 3: Löschen des Gateways des virtuellen Netzwerks

Hinweis: Wenn Sie neben der S2S-Konfiguration noch über eine P2S-Konfiguration für dieses VNET verfügen, werden automatisch alle P2S-Clients ohne Warnung getrennt, wenn Sie das Gateway des virtuellen Netzwerks löschen.

1. Klicken Sie auf der Seite für das Gateway für virtuelle Netzwerke auf **Übersicht**.
2. Klicken Sie auf der Seite **Übersicht** auf **Löschen**, um das Gateway zu löschen.
