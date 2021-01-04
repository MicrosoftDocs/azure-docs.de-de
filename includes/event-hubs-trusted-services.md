---
title: include file
description: include file
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 11/09/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: d828a0c3eb2582a833ee8ad07bdf4f18002c9dca
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015575"
---
## <a name="trusted-microsoft-services"></a>Vertrauenswürdige Microsoft-Dienste
Wenn Sie die Einstellung **Vertrauenswürdigen Microsoft-Diensten die Umgehung dieser Firewall erlauben** aktivieren, wird den folgenden Diensten Zugriff auf Ihre Event Hubs-Ressourcen gewährt.

| Vertrauenswürdiger Dienst | Unterstützte Verwendungsszenarien | 
| --------------- | ------------------------- | 
| Azure Event Grid | Ermöglicht Azure Event Grid das Senden von Ereignissen an Event Hubs in Ihrem Event Hubs-Namespace. Sie müssen außerdem die folgenden Schritte ausführen: <ul><li>Aktivieren der vom System zugewiesenen Identität für ein Thema oder eine Domäne</li><li>Hinzufügen der Identität der Rolle „Azure Event Hubs-Datenabsender“ für den Event Hubs-Namespace</li><li>Konfigurieren Sie dann das Ereignisabonnement, das einen Event Hub als Endpunkt verwendet, um die vom System zugewiesene Identität zu verwenden.</li></ul> <p>Weitere Informationen finden Sie unter [Ereignisübermittlung mit einer verwalteten Identität](../articles/event-grid/managed-service-identity.md).</p>|
| Azure Monitor (Diagnoseeinstellungen) | Ermöglicht Azure Monitor das Senden von Diagnoseinformationen an Event Hubs in Ihrem Event Hubs-Namespace. |
| Azure Stream Analytics | Ermöglicht einem Azure Stream Analytics-Auftrag das Lesen von Daten von Event Hubs für [Eingaben](../articles/stream-analytics/stream-analytics-add-inputs.md) bzw. das Schreiben von Daten in Event Hubs für [Ausgaben](../articles/stream-analytics/event-hubs-output.md) in Ihrem Event Hubs-Namespace. |
| Azure IoT Hub | Ermöglicht Azure IoT Hub das Senden von Nachrichten an Event Hubs in Ihrem Event Hubs-Namespace. Sie müssen außerdem die folgenden Schritte ausführen: <ul><li>Aktivieren der systemseitig zugewiesenen Identität für Ihren IoT-Hub</li><li>Fügen Sie die Identität der Rolle „Azure Event Hubs-Datensender“ im Event Hubs-Namespace hinzu.</li><li>Konfigurieren Sie dann die IoT Hub-Instanz, die einen Event Hub als benutzerdefinierten Endpunkt nur Nutzung der identitätsbasierten Authentifizierung verwendet.</li></ul>
