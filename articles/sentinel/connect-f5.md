---
title: Verknüpfen von Daten des F5-ASM mit Azure Sentinel | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit dem ASM-Datenconnector von F5 F5-ASM-Protokolle in Azure Sentinel pullen können. Zeigen Sie F5-ASM-Daten in Arbeitsmappen an, Erstellen Sie Warnungen, und verbessern Sie die Untersuchung.
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
ms.date: 05/20/2020
ms.author: yelevin
ms.openlocfilehash: 81a0f0e01827c48518f2eb1fba062e9e6536d9df
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655696"
---
# <a name="connect-f5-asm-to-azure-sentinel"></a>Verbinden des F5-ASM mit Azure Sentinel

In diesem Artikel wird beschrieben, wie Sie mit dem ASM-Datenconnector von F5 problemlos F5-ASM-Protokolle in Azure Sentinel abrufen. Dies ermöglicht es Ihnen, F5-ASM-Daten in Arbeitsmappen anzuzeigen, sie zum Erstellen benutzerdefinierter Warnungen zu verwenden und zur Verbesserung der Untersuchung zu integrieren. Mit den Daten des F5-ASM in Azure Sentinel erhalten Sie mehr Einblicke in die Webanwendungssicherheit Ihrer Organisation und können die Sicherheitsfunktionen verbessern. 

## <a name="configure-your-f5-asm-to-send-cef-messages"></a>Konfigurieren des F5-ASM zum Senden von CEF-Nachrichten

1. Befolgen Sie die Anleitung im F5-Artikel [Konfigurieren der Ereignisprotokollierung für die Anwendungssicherheit](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-11-5-0/12.html) zum Einrichten der Remoteprotokollierung anhand der folgenden Richtlinien:
   - Legen Sie **Remote storage type** auf **CEF** fest.
   - Legen Sie das **Protokoll** auf **TCP** fest.
   - Legen Sie **IP address** auf die IP-Adresse des Syslog-Servers fest.
   - Legen Sie **port number** auf **514** oder den Port fest, den Sie für Ihren Agent angegeben haben.
   - Sie können die maximale Größe von Abfragezeichenfolgen (**Maximum Query String Size**) auf die Größe festlegen, die Sie in Ihrem Agent angegeben haben.

1. Um das relevante Schema in Log Analytics für CEF-Ereignisse zu verwenden, suchen Sie nach `CommonSecurityLog`.

1. Fahren Sie mit [SCHRITT 3: Überprüfen der Konnektivität](connect-cef-verify.md) fort.


## <a name="next-steps"></a>Nächste Schritte
In diesem Dokument wurde beschrieben, wie Sie den F5-ASM mit Azure Sentinel verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](quickstart-get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](./tutorial-detect-threats-built-in.md).
- [Verwenden Sie Arbeitsmappen](tutorial-monitor-your-data.md), um Ihre Daten zu überwachen.