---
title: Verbinden von Cisco Umbrella mit Azure Sentinel | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit dem Cisco Umbrella-Datenconnector Umbrella-Daten in Azure Sentinel pullen. Zeigen Sie Umbrella-Daten in Arbeitsmappen an, erstellen Sie Warnungen, und verbessern Sie die Untersuchung.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 22796134dae5c345e3f915e47bc1300affb9f60e
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99566620"
---
# <a name="connect-your-cisco-umbrella-to-azure-sentinel"></a>Verbinden von Cisco Umbrella mit Azure Sentinel

> [!IMPORTANT]
> Der Cisco Umbrella-Connector befindet sich derzeit in der **Vorschauversion**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

Mit dem Cisco Umbrella-Connector können Sie alle Protokolle Ihrer Cisco Umbrella-Sicherheitslösungen einfach mit Azure Sentinel verbinden, um Dashboards anzuzeigen, benutzerdefinierte Warnungen zu erstellen und die Untersuchung von Daten zu verbessern. Die Integration von Cisco Umbrella und Azure Sentinel basiert auf der REST-API.

> [!NOTE]
> Daten werden am geografischen Standort des Arbeitsbereichs gespeichert, in dem Sie Azure Sentinel ausführen.

## <a name="configure-and-connect-cisco-umbrella"></a>Konfigurieren und Verbinden von Cisco Umbrella

Cisco Umbrella kann in Azure Sentinel integriert werden und Protokolle dann direkt in Azure Sentinel exportieren.

1. Wählen Sie im Azure Sentinel-Navigationsmenü **Datenconnectors** aus.

1. Wählen Sie im Katalog **Datenconnectors** den Eintrag **Cisco Umbrella (Vorschau)** und dann **Connectorseite öffnen** aus.

1. Führen Sie die im Abschnitt **Konfiguration** auf der Connectorseite beschriebenen Schritte aus.

## <a name="find-your-data"></a>Suchen von Daten

Nachdem eine erfolgreiche Verbindung hergestellt wurde, werden die Daten im Abschnitt **CustomLogs** in **Logs** in einer der folgenden Tabellen angezeigt:
- `Cisco_Umbrella_dns_CL`
- `Cisco_Umbrella_proxy_CL`
- `Cisco_Umbrella_ip_CL`
- `Cisco_Umbrella_cloudfirewall_CL`

## <a name="validate-connectivity"></a>Überprüfen der Konnektivität

Es kann bis zu 20 Minuten dauern, bis Ihre Protokolle in Log Analytics angezeigt werden.

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument wurde beschrieben, wie Sie Cisco Umbrella-Daten mit Azure Sentinel verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:

- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](quickstart-get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Verwenden Sie Arbeitsmappen](tutorial-monitor-your-data.md), um Ihre Daten zu überwachen.
