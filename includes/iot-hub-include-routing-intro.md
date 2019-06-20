---
title: Includedatei
description: Includedatei
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/05/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: d0accd01926743d64fa4911dfe56806537170c2d
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66271540"
---
Das [Nachrichtenrouting](../articles/iot-hub/iot-hub-devguide-messages-d2c.md) ermöglicht das Senden von Telemetriedaten von Ihren IoT-Geräten an integrierte, Event Hub-kompatible Endpunkte oder benutzerdefinierte Endpunkte wie Blob Storage-Instanzen, Service Bus-Warteschlangen, Service Bus-Themen und Event Hubs. Zum Konfigurieren des benutzerdefinierten Nachrichtenroutings erstellen Sie [Routingabfragen](../articles/iot-hub/iot-hub-devguide-routing-query-syntax.md), um die Route anzupassen, die eine bestimmte Bedingung erfüllt. Nach der Einrichtung werden die eingehenden Daten von IoT Hub automatisch an die Endpunkte weitergeleitet. Wenn eine Nachricht nicht mit einer der definierten Routingabfragen übereinstimmt, wird sie an den Standardendpunkt geleitet.

In diesem zweiteiligen Tutorial lernen Sie, wie Sie diese benutzerdefinierten Routingabfragen mit IoT Hub einrichten und verwenden. Sie leiten Nachrichten von einem IoT-Gerät an einen von mehreren Endpunkten weiter, z. B. Blob Storage oder eine Service Bus-Warteschlange. Nachrichten an die Service Bus-Warteschlange werden über eine Logik-App per E-Mail gesendet. Nachrichten, für die kein benutzerdefiniertes Nachrichtenrouting definiert ist, werden an den Standardendpunkt gesendet und dann von Azure Stream Analytics ausgewählt und in einer Power BI-Visualisierung angezeigt.

Sie haben die folgenden Aufgaben ausgeführt, um Teil 1 und 2 dieses Tutorials durchzuarbeiten:

**Teil 1: Erstellen von Ressourcen, Einrichten des Nachrichtenroutings**
> [!div class="checklist"]
> * Sie erstellen die Ressourcen: einen IoT-Hub, ein Speicherkonto, eine Service Bus-Warteschlange und ein simuliertes Gerät. Hierfür können Sie das Portal, die Azure CLI, Azure PowerShell oder eine Azure Resource Manager-Vorlage verwenden.
> * Sie konfigurieren die Endpunkte und Nachrichtenrouten in IoT Hub für das Speicherkonto und die Service Bus-Warteschlange.

**Teil 2: Senden von Nachrichten an den Hub, Anzeigen von Routingergebnissen**
> [!div class="checklist"]
> * Sie erstellen eine Logik-App, die ausgelöst wird und eine E-Mail sendet, wenn der Service Bus-Warteschlange eine Nachricht hinzugefügt wird.
> * Sie laden eine App herunter und führen sie aus, die ein IoT-Gerät simuliert, das für die verschiedenen Weiterleitungsoptionen Nachrichten an den Hub sendet.
> * Sie erstellen eine Power BI-Visualisierung für die Daten, die an den Standardendpunkt gesendet werden.
> * Sie zeigen die Ergebnisse an:
> * – in der Service Bus-Warteschlange und in E-Mails
> * – im Speicherkonto
> * – in der Power BI-Visualisierung

## <a name="prerequisites"></a>Voraussetzungen

* Für Teil 1 dieses Tutorials:
  - Sie benötigen ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

* Für Teil 2 dieses Tutorials:
  - Sie müssen Teil 1 dieses Tutorials abgeschlossen haben, und die Ressourcen müssen noch verfügbar sein.
  - Installieren Sie [Visual Studio](https://www.visualstudio.com/).
  - Ein Power BI-Konto zum Ausführen von Stream Analytics-Funktionen des Standardendpunkts. ([Power BI kostenlos testen](https://app.powerbi.com/signupredirect?pbi_source=web))
  - Ein Office 365-Konto zum Senden von Benachrichtigungs-E-Mails.

[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]
