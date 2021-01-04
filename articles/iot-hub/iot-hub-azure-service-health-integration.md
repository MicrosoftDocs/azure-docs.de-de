---
title: Überprüfen der Integrität des Azure IoT Hub-Diensts und der -Ressourcen | Microsoft-Dokumentation
description: Verwenden von Azure Service Health und Azure Resource Health zum Überwachen Ihrer IoT Hub-Instanz
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: robinsh
ms.custom:
- amqp
- 'Role: Cloud Development'
- 'Role: Technical Support'
- devx-track-csharp
ms.openlocfilehash: 27fca7b76ab148fc355eb7d52ee0cbcbd3540458
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92548413"
---
# <a name="check-iot-hub-service-and-resource-health"></a>Überprüfen der Integrität des IoT Hub-Diensts und der -Ressourcen

Azure IoT Hub ist mit dem [Azure Service Health-Dienst](../service-health/overview.md) integriert, um es Ihnen zu ermöglichen, die Integrität des IoT Hub-Diensts und individueller IoT Hub-Instanzen auf Dienstebene zu überwachen. Sie können auch Warnungen einrichten, damit Sie benachrichtigt werden, wenn sich der Status des IoT Hub-Diensts oder einer IoT Hub-Instanz ändert. Der Azure Service Health-Dienst ist eine Kombination aus drei kleineren Diensten: Azure Resource Health, Azure Service Health und die Azure-Statusseite. Die in diesem Artikel enthaltenen Abschnitte enthalten ausführlichere Beschreibungen der einzelnen Dienste und ihrer Beziehung mit IoT Hub.

Der Azure Service Health-Dienst unterstützt Sie bei der Überwachung von Ereignissen auf Dienstebene. Dabei handelt es sich beispielsweise um Ausfälle und Upgrades, die sich auf die Verfügbarkeit des IoT Hub-Diensts und Ihre individuellen IoT Hub-Instanzen auswirken können. IoT Hub kann auch mit Azure Monitor integriert werden, um IoT Hub-Plattformmetriken und -Ressourcenprotokolle bereitzustellen, die Sie zur Überwachung von Betriebsfehlern und Bedingungen verwenden können, die in einer spezifischen IoT Hub-Instanz auftreten. Weitere Informationen finden Sie unter [Überwachen von IoT Hub](monitor-iot-hub.md).

## <a name="check-health-of-an-iot-hub-with-azure-resource-health"></a>Überprüfen der Integrität einer IoT Hub-Instanz mit Azure Resource Health

Azure Resource Health ist ein Teil des Azure Service Health-Diensts, der die Integrität einzelner Ressourcen nachverfolgt. Sie können den Integritätsstatus Ihrer IoT Hub-Instanz direkt über das Portal überprüfen.

Führen Sie die folgenden Schritte aus, um den Status und den Statusverlauf Ihrer IoT Hub-Instanz mithilfe des Portals anzuzeigen:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer IoT Hub-Instanz.

1. Klicken Sie im linken Bereich unter **Support und Problembehandlung** auf **Resource Health**.

    :::image type="content" source="./media/iot-hub-azure-service-health-integration/iot-hub-resource-health.png" alt-text="Resource Health-Seite für eine IoT Hub-Instanz":::

Weitere Informationen über Azure Resource Health und zur Interpretation von Integritätsdaten finden Sie in der [Übersicht über Resource Health](../service-health/resource-health-overview.md) in der Azure Service Health-Dokumentation.

Sie können auch auf **Warnung zu Ressourcenintegrität hinzufügen** klicken, um Warnungen einzurichten, die ausgelöst werden, wenn sich der Integritätsstatus Ihrer IoT Hub-Instanz ändert. Weitere Informationen finden Sie unter [Konfigurieren von Warnungen für Dienstintegritätsereignisse](../service-health/alerts-activity-log-service-notifications-portal.md) und in verwandten Artikeln in der Azure Service Health-Dokumentation.

## <a name="check-health-of-iot-hubs-in-your-subscription-with-azure-service-health"></a>Überprüfen der Integrität von IoT Hub-Instanzen in Ihrem Abonnement mit Azure Service Health

Mit Azure Service Health können Sie den Integritätsstatus aller IoT Hub-Instanzen in Ihrem Abonnement überprüfen.

Um die Integrität Ihrer IoT Hubs zu überprüfen, gehen Sie folgendermaßen vor:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Navigieren Sie zu **Dienstintegrität** > **Ressourcenintegrität**.

3. Wählen Sie in den Dropdownlisten Ihr Abonnement und dann **IoT Hub** als Ressourcentyp aus.

Weitere Informationen über Azure Service Health und zur Interpretation von Integritätsdaten finden Sie in der [Übersicht über Service Health](../service-health/service-health-overview.md) in der Azure Service Health-Dokumentation.

Informationen zum Einrichten von Warnungen mit Azure Service Health finden Sie unter [Konfigurieren von Warnungen für Dienstintegritätsereignisse](../service-health/alerts-activity-log-service-notifications-portal.md) und in verwandten Artikeln in der Azure Service Health-Dokumentation.

## <a name="check-health-of-the-iot-hub-service-by-region-on-azure-status-page"></a>Überprüfen der Integrität des IoT Hub-Diensts nach Region auf der Azure-Statusseite

Sie können die [Azure-Statusseite](https://status.azure.com/status) verwenden, um den Status von IoT Hub und anderen Diensten nach Region weltweit anzuzeigen. Weitere Informationen über die Azure-Statusseite finden Sie in der Azure Service Health-Dokumentation unter [Azure-Statusübersicht](../service-health/azure-status-overview.md).

## <a name="next-steps"></a>Nächste Schritte

* Ausführliche Informationen über Azure Service Health, Azure Resource Health und die Azure-Statusseite finden Sie unter [Azure Service Health-Dienst](../service-health/overview.md).
* Eine Beschreibung der Überwachung von Azure IoT Hub finden Sie unter [Überwachen von Azure IoT Hub](monitor-iot-hub.md).
