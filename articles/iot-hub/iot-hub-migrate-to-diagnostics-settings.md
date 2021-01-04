---
title: Migrieren der Azure IoT Hub-Betriebsüberwachung zu IoT Hub-Ressourcenprotokollen in Azure Monitor | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Azure IoT Hub aktualisieren, damit zur Echtzeitüberwachung des Status von Vorgängen in Ihrem IoT-Hub Azure Monitor anstelle der Betriebsüberwachung verwendet wird.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: kgremban
ms.openlocfilehash: dfd819d82f5e35183802e33e5d423cad4de36c38
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461584"
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-azure-monitor-resource-logs"></a>Migrieren Ihrer IoT Hub-Instanz von der Betriebsüberwachung zu Azure Monitor-Ressourcenprotokollen

Kunden, die mit der [Betriebsüberwachung](iot-hub-operations-monitoring.md) den Status von Vorgängen in IoT Hub nachverfolgen, können diesen Workflow zu [Azure Monitor-Ressourcenprotokollen](../azure-monitor/platform/platform-logs-overview.md) migrieren, einem Feature von Azure Monitor. Ressourcenprotokolle bieten vielen Azure-Diensten Diagnoseinformationen auf Ressourcenebene.

**Die Funktionalität zur Vorgangsüberwachung von IoT Hub ist veraltet** und wurde aus dem Portal entfernt. Dieser Artikel enthält die Schritte zum Verlagern Ihrer Workloads von der Betriebsüberwachung zu Azure Monitor-Ressourcenprotokollen. Weitere Informationen zur Veraltungszeitachse finden Sie unter [Monitor your Azure IoT solutions with Azure Monitor and Azure Resource Health](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health/) (Überwachen Ihrer Azure IoT-Lösungen mit Azure Monitor und Azure Resource Health).

## <a name="update-iot-hub"></a>Aktualisieren von IoT Hub

Um Ihre IoT Hub-Instanz im Azure-Portal zu aktualisieren, erstellen Sie zuerst eine Diagnoseeinstellung und deaktivieren dann die Betriebsüberwachung.  

### <a name="create-a--diagnostic-setting"></a>Erstellen einer Diagnoseeinstellung

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu Ihrem IoT Hub.

1. Wählen Sie im linken Bereich unter **Überwachung** die Option **Diagnoseeinstellungen** aus. Wählen Sie dann **Diagnoseeinstellung hinzufügen** aus.

   :::image type="content" source="media/iot-hub-migrate-to-diagnostics-settings/open-diagnostic-settings.png" alt-text="Screenshot, auf dem „Diagnoseeinstellungen“ im Abschnitt „Überwachung“ hervorgehoben ist":::

1. Benennen Sie die Diagnoseeinstellung im Bereich **Diagnoseeinstellungen**.

1. Wählen Sie unter **Kategoriedetails** die Kategorien für die Vorgänge aus, die Sie überwachen möchten. Weitere Informationen zu den für IoT Hub verfügbaren Vorgangskategorien finden Sie unter [Ressourcenprotokolle](monitor-iot-hub-reference.md#resource-logs).

1. Wählen Sie unter **Zieldetails** aus, wohin Sie die Protokolle senden möchten. Sie können eine beliebige Kombination dieser Ziele auswählen:

   * In einem Speicherkonto archivieren
   * An einen Event Hub streamen
   * Über einen Log Analytics-Arbeitsbereich an Azure Monitor-Protokolle senden

   Der folgende Screenshot zeigt eine Diagnoseeinstellung, die Vorgänge in den Kategorien „Verbindungen“ und „Gerätetelemetrie“ an einen Log Analytics-Arbeitsbereich weiterleitet:

   :::image type="content" source="media/iot-hub-migrate-to-diagnostics-settings/add-diagnostic-setting.png" alt-text="Screenshot einer vollständigen Diagnoseeinstellung":::

1. Klicken Sie auf **Speichern**, um die Einstellungen zu speichern.

Neue Einstellungen werden in etwa zehn Minuten wirksam. Danach werden im konfigurierten Ziel Protokolle angezeigt. Weitere Informationen zur Konfiguration von Diagnosen finden Sie unter [Erfassen und Nutzen von Protokolldaten aus Ihren Azure-Ressourcen](../azure-monitor/platform/platform-logs-overview.md).

Weitere Information zum Erstellen von Diagnoseeinstellungen, z. B. mit PowerShell und der Azure CLI, finden Sie in der Dokumentation zu Azure Monitor unter [Diagnoseeinstellungen](../azure-monitor/platform/diagnostic-settings.md).

### <a name="turn-off-operations-monitoring"></a>Deaktivieren der Vorgangsüberwachung

> [!NOTE]
> Am 11. März 2019 wird die Funktion zur Vorgangsüberwachung aus der Azure-Portaloberfläche von IoT Hub entfernt. Die unten aufgeführten Schritte gelten nicht mehr. Vergewissern Sie sich vor dem Migrieren, dass mit einer der oben genannten Azure Monitor-Diagnoseeinstellungen die richtigen Kategorien zu einem Ziel weitergeleitet werden.

Nachdem Sie die neuen Diagnoseeinstellungen in Ihrem Workflow getestet haben, können Sie das Vorgangsüberwachungsfeature deaktivieren. 

1. Wählen Sie in Ihrem IoT Hub-Menü die Option **Vorgangsüberwachung**.

2. Wählen Sie unter jeder Überwachungskategorie die Option **Keine**.

3. Speichern Sie die Änderungen der Vorgangsüberwachung.

## <a name="update-applications-that-use-operations-monitoring"></a>Aktualisieren von Anwendungen, die Vorgangsüberwachung verwenden

Die Schemas für Betriebsüberwachung und Ressourcenprotokolle variieren geringfügig. Es ist wichtig, dass Sie die Anwendungen, die derzeit die Betriebsüberwachung verwenden, so aktualisieren, dass sie dem von Ressourcenprotokollen verwendeten Schema entsprechen.

Darüber hinaus bieten IoT Hub-Ressourcenprotokolle fünf neue Kategorien für die Nachverfolgung. Fügen Sie nach Aktualisieren der Anwendungen für das vorhandene Schema auch die neuen Kategorien hinzu:

* Cloud-zu-Gerät-Zwillingsvorgänge
* Gerät-zu-Cloud-Zwillingsvorgänge
* Zwillingsabfragen
* Auftragsvorgänge
* Direkte Methoden

Informationen zu den jeweiligen Schemastrukturen finden Sie unter [Ressourcenprotokolle](monitor-iot-hub-reference.md#resource-logs).

## <a name="monitoring-device-connect-and-disconnect-events-with-low-latency"></a>Überwachen von Verbindungsherstellungs- und Verbindungstrennungsereignissen für Geräte mit geringer Wartezeit

Zur Überwachung von Verbindungsherstellungs- und Verbindungstrennungsereignissen für Geräte in der Produktion empfiehlt es sich, das [Ereignis **Gerät getrennt**](iot-hub-event-grid.md#event-types) in Event Grid zu abonnieren, um Warnungen zu erhalten und den Verbindungsstatus des Geräts zu überwachen. In diesem [Tutorial](iot-hub-how-to-order-connection-state-events.md) erfahren Sie, wie Sie die Ereignisse „Gerät verbunden“ und „Gerät getrennt“ aus IoT Hub in Ihre IoT-Lösung integrieren.

## <a name="next-steps"></a>Nächste Schritte

[Überwachen von IoT Hub](monitor-iot-hub.md)