---
title: Verbinden von Syslog-Daten mit Azure Sentinel (Vorschauversion) | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Syslog-Daten mit Azure Sentinel verbinden.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 5dd59729-c623-4cb4-b326-bb847c8f094b
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: 673b1df6094703bebcbfd9d82c1268c01d46e814
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65233586"
---
# <a name="connect-your-external-solution-using-syslog"></a>Verbinden Sie Ihre externe Lösung mithilfe von Syslog

> [!IMPORTANT]
> Azure Sentinel ist aktuell als Public Preview verfügbar.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Sie können jede lokale Appliance, die Syslog unterstützt, mit Azure Sentinel verbinden. Hierzu wird ein Agent verwendet, der sich auf einem Linux-Computer befindet, der sich zwischen der Appliance und Azure Sentinel befindet. Ist Ihr Linux-Computer in Azure angeordnet, können Sie die Protokolle von Ihrer Appliance oder Anwendung an einen dedizierten Arbeitsbereich streamen, den Sie in Azure erstellt und verbunden haben. Ist Ihr Linux-Computer nicht in Azure angeordnet, können Sie die Protokolle von Ihrer Appliance an einen dedizierten lokalen virtuellen Computer oder Computer streamen, auf dem Sie den Agent für Linux installiert haben. 

> [!NOTE]
> Wenn Ihre Appliance Syslog CEF unterstützt, ist die Verbindung vollständiger, und Sie sollten Sie diese Option wählen die Anweisungen unter [Verbinden von Daten aus CEF](connect-common-event-format.md) befolgen.

## <a name="how-it-works"></a>So funktioniert's

Syslog-Verbindung erfolgt über einen Agent für Linux. Standardmäßig empfängt der Agent für Linux Ereignisse vom Syslog-Daemon über UDP. Aber in Fällen, in denen für einen Linux-Rechner erwartet wird, dass er sehr viele Syslog-Ereignisse sammelt, etwa wenn ein Linux-Agent Ereignisse von anderen Geräten empfängt, wird die Konfiguration so geändert, dass TCP-Transport zwischen dem Syslog-Daemon und dem Agent verwendet wird.

## <a name="connect-your-syslog-appliance"></a>Verbinden Ihrer Syslog-Appliance

1. Wählen Sie im Azure Sentinel-Portal die Option **Data connectors** (Datenconnectors) aus, und klicken Sie auf die **Syslog**-Kachel.
2. Ist Ihr Linux-Computer nicht in Azure angeordnet, laden Sie den Azure Sentinel-**Agent für Linux** auf Ihre Appliance herunter, und installieren Sie ihn dort. 
1. Wenn Sie in Azure arbeiten, wählen Sie einen virtuellen Computers aus, oder erstellen Sie einen virtuellen Computer, der sich in dem Azure Sentinel-Arbeitsbereich befindet, der zum Empfangen von Syslog-Nachrichten vorgesehen ist. Wählen Sie den virtuellen Computer in Azure Sentinel-Arbeitsbereichen aus, und klicken Sie auf **Verbinden** oben im linken Bereich.
3. Klicken Sie auf **Zu verbindende Protokolle konfigurieren** im Setup des Syslog-Connectors. 
4. Klicken Sie auf **Press here to open the configuration blade** (Hier drücken, um das Konfigurationsblatt zu öffnen).
1. Wählen Sie **Daten** und dann **Syslog** aus.
   - Vergewissern Sie sich, dass jede Einrichtung, die Sie über Syslog senden, in der Tabelle enthalten ist. Legen Sie für jede Einrichtung, die Sie überwachen möchten, einen Schweregrad fest. Klicken Sie auf **Anwenden**.
1. Überprüfen Sie auf Ihrem Syslog-Computer, dass Sie diese Einrichtungen senden. 

3. Um das entsprechende Schema in Log Analytics für die Syslog-Protokolle zu verwenden, suchen Sie nach **Syslog**.




## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie gelernt, wie Sie lokale Syslog-Appliances mit Azure Sentinel verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](quickstart-get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](tutorial-detect-threats.md).
