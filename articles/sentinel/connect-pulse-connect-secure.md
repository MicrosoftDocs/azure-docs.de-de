---
title: Verbinden von Pulse Connect Secure-Daten mit Azure Sentinel | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Pulse Connect Secure-Daten mit Azure Sentinel verbinden.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 71e1c7787243713b29be9455fee966eff54f6d90
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100097652"
---
# <a name="connect-your-pulse-connect-secure-to-azure-sentinel"></a>Verbinden von Pulse Connect Secure mit Azure Sentinel

> [!IMPORTANT]
> Der Pulse Connect Secure-Datenconnector in Azure Sentinel ist derzeit als öffentliche Vorschau verfügbar.
> Dieses Feature wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

In diesem Artikel wird erläutert, wie Sie Ihre [Pulse Connect Secure](https://www.pulsesecure.net/products/pulse-connect-secure/)-Appliance mit Azure Sentinel verbinden. Der Pulse Connect Secure-Datenconnector ermöglicht es Ihnen, Ihre Pulse Connect Secure-Protokolle auf einfache Weise mit Azure Sentinel zu verbinden, um Dashboards anzuzeigen, benutzerdefinierte Warnungen zu erstellen und die Untersuchung von Daten zu verbessern. Für die Integration von Pulse Connect Secure und Azure Sentinel wird Syslog genutzt.

> [!NOTE]
> Daten werden am geografischen Standort des Arbeitsbereichs gespeichert, in dem Sie Azure Sentinel ausführen.

## <a name="forward-pulse-connect-secure-logs-to-the-syslog-agent"></a>Weiterleiten von Pulse Connect Secure-Protokolle an den Syslog-Agent  

Konfigurieren Sie Pulse Connect Secure für das Weiterleiten von Syslog-Nachrichten an Ihren Azure-Arbeitsbereich über den Syslog-Agent.

1. Klicken Sie im Azure Sentinel-Portal auf **Datenconnectors**, und wählen Sie den Connector **Pulse Connect Secure** aus.

1. Wählen Sie **Connectorseite öffnen** aus.

1. Befolgen Sie die Anweisungen auf der Seite **Pulse Connect Secure**.

## <a name="find-your-data"></a>Suchen von Daten

Nachdem Sie eine Verbindung hergestellt haben, werden die Daten in Log Analytics unter „Syslog“ angezeigt.

## <a name="validate-connectivity"></a>Überprüfen der Konnektivität

Es kann bis zu 20 Minuten dauern, bis Ihre Protokolle in Log Analytics angezeigt werden. 

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument wurde beschrieben, wie Sie Pulse Connect Secure mit Azure Sentinel verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:

- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](quickstart-get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Verwenden Sie Arbeitsmappen](tutorial-monitor-your-data.md), um Ihre Daten zu überwachen.