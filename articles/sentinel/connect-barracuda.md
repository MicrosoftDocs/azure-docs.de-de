---
title: Verknüpfen von Barracuda-Daten mit der Vorschauversion von Azure Sentinel | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Barracuda-Daten mit Azure Sentinel verknüpfen.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 3b33b4aa-7286-4d79-b461-8e1812edc2e1
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: dd45be69ec29fdcd00710b7366348846f325b151
ms.sourcegitcommit: d73c46af1465c7fd879b5a97ddc45c38ec3f5c0d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65921984"
---
# <a name="connect-your-barracuda-appliance"></a>Verbinden Ihrer Barracuda-Appliance 

> [!IMPORTANT]
> Azure Sentinel ist derzeit als öffentliche Vorschauversion (Public Preview) verfügbar.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Mit dem Connector für Barracuda Web Application Firewall (WAF) können Sie Ihre Barracuda-Protokolle einfach mit Azure Sentinel verbinden, um Dashboards anzuzeigen, benutzerdefinierte Warnungen zu erstellen und Untersuchungen zu verbessern. Dadurch erhalten Sie einen besseren Einblick in das Netzwerk Ihrer Organisation und bessere Möglichkeiten für Sicherheitsvorgänge. Azure Sentinel nutzt die native Integration zwischen **Barracuda** und Microsoft Monitoring Agent, um nahtlose Integration zu bieten. 


> [!NOTE]
> Daten werden am geografischen Standort des Arbeitsbereichs gespeichert, in dem Sie Azure Sentinel ausführen.

## <a name="configure-and-connect-barracuda-waf"></a>Konfigurieren und Verbinden von Barracuda WAF
Barracuda Web Application Firewall kann Protokolle über Microsoft Monitoring Agent direkt in Azure Sentinel integrieren und exportieren.
1. Wechseln Sie zu [Barracuda WAF configuration flow](https://campus.barracuda.com/product/webapplicationfirewall/doc/73696965/configure-the-barracuda-web-application-firewall-to-integrate-with-the-oms-server-and-export-logs/) (Barracuda WAF-Konfigurationsflow), und befolgen Sie die Anweisungen zum Einrichten der Verbindung unter Verwendung der folgenden Parameter:
    - **Workspace ID** (Arbeitsbereichs-ID): Kopieren Sie den Wert Ihrer Arbeitsbereichs-ID von der Seite des Azure Sentinel-Barracuda-Connectors.
    - **Primary key** (Primärer Schlüssel): Kopieren Sie den Wert Ihres primären Schlüssels von der Seite des Azure Sentinel-Barracuda-Connectors.
2. Wechseln Sie im Azure Sentinel-Portal zu dem Arbeitsbereich, in dem Sie Azure Sentinel bereitgestellt haben, wählen Sie die Auslassungspunkte (...) am Ende der Zeile und dann **Erweiterte Einstellungen** aus. 
1. Wählen Sie **Daten** und dann **Syslog** aus.
1. Achten Sie darauf, dass die in Barracuda ausgewählte Einrichtung vorhanden ist, legen Sie den Schweregrad fest, und klicken Sie auf **Speichern**.
6. Um das relevante Schema in Log Analytics für die Barracuda-Ereignisse zu verwenden, suchen Sie nach **CommonSecurityLog**.


## <a name="validate-connectivity"></a>Überprüfen der Konnektivität

Es kann bis zu 20 Minuten dauern, bis Ihre Protokolle in Log Analytics angezeigt werden. 



## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie gelernt, wie Sie Barracuda-Appliances mit Azure Sentinel verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](quickstart-get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](tutorial-detect-threats.md).

