---
title: Verbinden von Apache HTTP Server mit Azure Sentinel | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit dem Apache HTTP Server-Connector Apache-Protokolle in Azure Sentinel pullen. Zeigen Sie Apache-Daten in Arbeitsmappen an, erstellen Sie Warnungen, und verbessern Sie die Untersuchung.
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
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 59ef34f9d6d42277d8e83bf4e0ebb8ee29096f52
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99566609"
---
# <a name="connect-your-apache-http-server-to-azure-sentinel"></a>Verbinden von Apache HTTP Server mit Azure Sentinel

> [!IMPORTANT]
> Der Apache HTTP Server-Connector befindet sich derzeit in der **Vorschauversion**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

In diesem Artikel wird erläutert, wie Sie Apache HTTP Server mit Azure Sentinel verbinden. Mit dem Apache HTTP Server-Connector lassen sich Ihre Apache HTTP Server-Protokolle problemlos in Azure Sentinel erfassen, sodass Sie die Daten dann in Arbeitsmappen anzeigen, zum Erstellen benutzerdefinierter Warnungen abfragen und zur Verbesserung der Untersuchung von Problemen verwenden können. Die Integration von Apache HTTP Server und Azure Sentinel basiert auf der lokalen Dateiverarbeitung durch den Log Analytics-Agent.

> [!NOTE]
> Daten werden am geografischen Standort des Arbeitsbereichs gespeichert, in dem Sie Azure Sentinel ausführen.

## <a name="prerequisites"></a>Voraussetzungen

- Sie benötigen Schreibzugriff auf den Azure Sentinel-Arbeitsbereich.

## <a name="configure-and-integrate-apache-http-server-logs-via-log-analytics-agent"></a>Konfigurieren und Integrieren von Apache HTTP Server-Protokollen über den Log Analytics-Agent

Konfigurieren Sie den Speicherort der Apache HTTP Server-Protokolldateien, um Daten über den Log Analytics-Agent an Ihren Azure-Arbeitsbereich weiterzuleiten.
Konfigurieren Sie den Log Analytics-Agent für das Lesen von Apache HTTP Server-Protokolldateien.

1. Folgen Sie den Anweisungen unter https://httpd.apache.org/docs/2.4/logs.html, um den Speicherort der Protokolldateien in Apache HTTP Server einzurichten.

1. Wählen Sie im Navigationsmenü von Azure Sentinel die Option **Datenconnectors** und dann **Apache HTTP Server (Vorschau)** aus.

1. Wählen Sie **Connectorseite öffnen** aus.

1. Folgen Sie den Anweisungen auf der Seite für Apache HTTP Server.

## <a name="find-your-data"></a>Suchen von Daten

Nachdem Sie eine Verbindung hergestellt haben, werden die Daten in Log Analytics unter „ApacheHTTPServer_CL“ aufgeführt.

## <a name="validate-connectivity"></a>Überprüfen der Konnektivität

Es kann bis zu 20 Minuten dauern, bis Ihre Protokolle in Log Analytics angezeigt werden.

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie erfahren, wie Sie Apache HTTP Server mit Azure Sentinel verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:

- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](quickstart-get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Verwenden Sie Arbeitsmappen](tutorial-monitor-your-data.md), um Ihre Daten zu überwachen.
