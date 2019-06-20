---
title: Überwachen des Azure Data Box Edge-Geräts | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie über das Azure-Portal und die lokale Webbenutzeroberfläche Ihr Azure Data Box Edge-Gerät überwachen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 188f5c6cfbb4650ad1ff767955d064f8e0c3cb70
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60756739"
---
# <a name="monitor-your-azure-data-box-edge"></a>Überwachen Ihres Azure Data Box Edge-Geräts

In diesem Artikel wird beschrieben, wie Sie Ihr Azure Data Box Edge-Gerät überwachen. Zum Überwachen Ihres Geräts können Sie das Azure-Portal oder die lokale Webbenutzeroberfläche verwenden. Verwenden Sie das Azure-Portal, um Geräteereignisse anzuzeigen, Warnungen zu konfigurieren und zu verwalten und Metriken anzuzeigen. Verwenden Sie die lokale Webbenutzeroberfläche auf Ihrem physischen Gerät, um den Hardwarestatus der verschiedenen Gerätekomponenten anzuzeigen.

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> * Anzeigen von Geräteereignissen und den entsprechenden Warnungen
> * Anzeigen des Hardwarestatus von Gerätekomponenten
> * Anzeigen von Kapazitäts- und Transaktionsmetriken für Ihr Gerät
> * Konfigurieren und Verwalten von Warnungen

## <a name="view-device-events"></a>Anzeigen von Geräteereignissen

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-device-events.md)]

## <a name="view-hardware-status"></a>Anzeigen des Hardwarestatus

Führen Sie die folgenden Schritte auf der lokalen Webbenutzeroberfläche aus, um den Hardwarestatus Ihrer Gerätekomponenten anzuzeigen.

1. Stellen Sie eine Verbindung mit der lokalen Webbenutzeroberfläche Ihres Geräts her.
2. Navigieren Sie zu **Wartung > Hardwarestatus**. Sie können die Integrität der verschiedenen Gerätekomponenten anzeigen.

    ![Anzeigen des Hardwarestatus](media/data-box-edge-monitor/view-hardware-status.png)

## <a name="view-metrics"></a>Anzeigen von Metriken

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-metrics.md)]

## <a name="manage-alerts"></a>Warnungen verwalten

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-manage-alerts.md)]

## <a name="next-steps"></a>Nächste Schritte 

Erfahren Sie, wie Sie [Bandbreite verwalten](data-box-edge-manage-bandwidth-schedules.md).