---
title: Verknüpfen von DNS-Daten mit Azure Sentinel Preview | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie DNS-Daten mit Azure Sentinel verknüpfen.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 77af84f9-47bc-418e-8ce2-4414d7b58c0c
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 6429568b33ece3ed4f26614e55e8c3069dd65d71
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65204402"
---
# <a name="connect-your-domain-name-server"></a>Verbinden eines Domänennamenservers

> [!IMPORTANT]
> Azure Sentinel ist aktuell als Public Preview verfügbar.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Sie können jeden Domänennamenserver (DNS), der unter Windows ausgeführt wird, mit Azure Sentinel verbinden. Dies erfolgt durch Installieren eines Agents auf dem DNS-Computer. Mithilfe von DNS-Protokollen können Sie sicherheits-, leistungs- und vorgangsbezogene Einblicke in die DNS-Infrastruktur Ihrer Organisation gewinnen, indem Sie Analyse- und Überwachungsprotokolle und andere zugehörige Daten von den DNS-Servern sammeln.

Das Aktivieren der DNS-Protokollverbindung ermöglicht Ihnen Folgendes:
- Identifizieren von Clients, die versuchen, schädliche Domänennamen aufzulösen
- Identifizieren von veralteten Ressourceneinträgen
- Identifizieren von häufig abgefragten Domänennamen und DNS-Clients mit hoher Aktivität
- Anzeigen der Anforderungslast auf DNS-Servern
- Anzeigen von Fehlern der dynamischen DNS-Registrierung

## <a name="how-it-works"></a>So funktioniert's

Die DNS-Verbindung wird umgesetzt, indem ein Agent auf dem DNS-Computer installiert wird. Der Agent ruft Ereignisse vom DNS-Computer ab und übergibt sie an Log Analytics (Protokollanalyse).

## <a name="connect-your-dns-appliance"></a>Verbinden Ihrer DNS-Appliance

1. Wählen Sie im Azure Sentinel-Portal die Option **Data connectors** (Datenconnectors) aus, und klicken Sie auf die **DNS**-Kachel.
1. Wenn sich Ihre DNS-Computer in Azure befinden:
    1. Klicken Sie auf **Download & install agent for Windows virtual machines** (Agent für virtuelle Windows-Computer herunterladen und installieren).
    1. Wählen Sie in der Liste **Virtual Machines** den DNS-Computer aus, für den Sie an Azure Sentinel streamen möchten. Stellen Sie sicher, dass dies ein virtueller Windows-Computer ist.
    1. Klicken Sie in dem Fenster, das für diesen virtuellen Computer wird geöffnet, auf **Verbinden**.  
    1. Klicken Sie auf **Enable** (Aktivieren) im Fenster **DNS-Connector**. 

2. Wenn Ihr DNS-Computer kein virtueller Azure-Computer ist:
    1. Klicken Sie auf **Download & install agent for Windows non-Azure machines** (Agent für virtuelle Windows-Nicht-Azure-Computer herunterladen und installieren).
    1. Wählen Sie im Fenster **Direkt-Agent** entweder **Windows-Agent herunterladen (64 Bit)** oder **Windows-Agent herunterladen (32 Bit)** aus.
    1. Installieren Sie den Agent auf Ihrem DNS-Computer. Kopieren Sie **Arbeitsbereich-ID**, **Primärschlüssel** und **Sekundärschlüssel**, und verwenden Sie diese Werte, wenn Sie während der Installation zur Eingabe aufgefordert werden.

3. Um das entsprechende Schema in Log Analytics für die DNS-Protokolle zu verwenden, suchen Sie nach **DnsEvents**.

## <a name="validate"></a>Überprüfen 

Suchen Sie in Log Analytics nach dem Schema **DnsEvents**, und stellen Sie sicher, dass drei Ereignisse vorhanden sind.

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie gelernt, wie Sie lokale DNS-Appliances mit Azure Sentinel verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](quickstart-get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](tutorial-detect-threats.md).
