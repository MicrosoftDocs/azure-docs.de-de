---
title: Konfigurieren Ihrer Sicherheitslösung zum Verbinden von CEF-Daten mit der Vorschauversion von Azure Sentinel | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Ihre Sicherheitslösung so konfigurieren, dass sie CEF-Daten mit Azure Sentinel verbindet.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: fec3f25c4b401ff7c3bc73d249b716b9c12e6529
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96548544"
---
# <a name="step-2-configure-your-security-solution-to-send-cef-messages"></a>SCHRITT 2: Konfigurieren Ihrer Sicherheitslösung zum Senden von CEF-Nachrichten

In diesem Schritt führen Sie die erforderlichen Konfigurationsänderungen an Ihrer Sicherheitslösung durch, sodass Protokolle an den CEF-Agent gesendet werden.

## <a name="configure-a-solution-with-a-connector"></a>Konfigurieren einer Lösung mit einem Connector

Wenn Ihre Sicherheitslösung bereits über einen Connector verfügt, verwenden Sie die connectorspezifischen Anweisungen wie folgt:

- [AI Vectra Detect](connect-ai-vectra-detect.md)
- [Check Point](connect-checkpoint.md)
- [Cisco](connect-cisco.md)
- [ExtraHop Reveal(x)](connect-extrahop.md)
- [F5 ASM](connect-f5.md)  
- [Forcepoint-Produkte](connect-forcepoint-casb-ngfw.md)
- [Fortinet](connect-fortinet.md)
- [Illusive Networks AMS](connect-illusive-attack-management-system.md)
- [One Identity Safeguard](connect-one-identity.md)
- [Palo Alto Networks](connect-paloalto.md)
- [Trend Micro Deep Security](connect-trend-micro.md)
- [Zscaler](connect-zscaler.md)
## <a name="configure-any-other-solution"></a>Konfigurieren einer beliebigen anderen Lösung

Wenn für Ihre spezifische Sicherheitslösung kein Connector vorhanden ist, verwenden Sie die folgenden allgemeinen Anweisungen für das Weiterleiten von Protokollen an den CEF-Agent.

1. Schritte zum Konfigurieren Ihrer Lösung für das Senden von CEF-Nachrichten finden Sie im Artikel zur entsprechenden Konfiguration. Wenn Ihre Lösung nicht aufgeführt ist, müssen Sie auf der Appliance folgende Werte festlegen, damit die erforderlichen Protokolle von der Appliance im erforderlichen Format an den Azure Sentinel-Syslog-Agent (der auf dem Log Analytics-Agent basiert) gesendet werden. Sie können diese Parameter in Ihrer Appliance ändern, sofern Sie diese im Syslog-Daemon auf dem Azure Sentinel-Agent ändern.
    - Protokoll = TCP
    - Port = 514
    - Format = CEF
    - IP-Adresse: Achten Sie darauf, die CEF-Nachrichten an die IP-Adresse des virtuellen Computers zu senden, den Sie für diesen Zweck reserviert haben.

   Diese Lösung unterstützt Syslog RFC 3164 und RFC 5424.

1. Wenn Sie in Log Analytics nach CEF-Ereignissen möchten, geben Sie `CommonSecurityLog` in das Abfragefenster ein.

1. Fahren Sie mit SCHRITT 3: [Überprüfen der Konnektivität](connect-cef-verify.md) fort.

> [!NOTE]
> **Ändern der Quelle des Felds „TimeGenerated“**
>
> - Standardmäßig füllt der Log Analytics-Agent das Feld *TimeGenerated* im Schema mit der Zeit auf, zur der der Agent das Ereignis vom Syslog-Daemon empfangen hat. Folglich wird die Zeit, zu der das Ereignis im Quellsystem generiert wurde, nicht in Azure Sentinel erfasst.
>
> - Sie können jedoch den folgenden Befehl ausführen, um das Skript `TimeGenerated.py` herunterzuladen und auszuführen. Mit diesem Skript wird der Log Analytics-Agent so konfiguriert, dass das Feld *TimeGenerated* mit der ursprünglichen Zeit des Ereignisses aus dem Quellsystem aufgefüllt wird, und nicht mit der Zeit, zu der es vom Agent empfangen wurde.
>
>    ```bash
>    wget -O TimeGenerated.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/TimeGenerated.py && python TimeGenerated.py {ws_id}
>    ```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie CEF-Appliances mit Azure Sentinel verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](quickstart-get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](./tutorial-detect-threats-built-in.md).