---
ms.topic: include
ms.service: time-series-insights
author: kingdomofends
ms.author: adgera
ms.date: 04/29/2019
ms.openlocfilehash: 8a3c630b54ff95a9b1200e2421c787a514a0aa52
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66431043"
---
## <a name="business-disaster-recovery"></a>Business Disaster Recovery

Dieser Abschnitt beschreibt Features von Azure Time Series Insights, die den Betrieb von Apps und Diensten auch im Katastrophenfall sicherstellen (bekannt als *Business Disaster Recovery*).

### <a name="high-availability"></a>Hochverfügbarkeit

Als Azure-Dienst bietet Time Series Insights bestimmte *Hochverfügbarkeitsfeatures*, indem Redundanzen auf Azure-Regionsebene bereitgestellt werden. Beispielsweise unterstützt Azure dank des Features für die *regionsübergreifende Verfügbarkeit* von Azure Funktionen für die Notfallwiederherstellung.

Zu den zusätzlichen Hochverfügbarkeitsfeatures von Azure (auch für alle Time Series Insights-Instanzen verfügbar) gehören:

- **Failover**: Azure bietet [Georeplikation und Lastenausgleich](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region).
- **Daten-** und **Speicherwiederherstellung**: Azure bietet [mehrere Optionen zum Beibehalten und Wiederherstellen von Daten](https://docs.microsoft.com/azure/architecture/resiliency/recovery-data-corruption).
- **Sitewiederherstellung**: Azure bietet Features für die Sitewiederherstellung über [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/).

Stellen Sie sicher, dass Sie die relevanten Azure-Features aktivieren, um eine globale, regionsübergreifende Hochverfügbarkeit für Ihre Geräte und Benutzer zu gewährleisten.

> [!NOTE]
> Ist Azure für regionsübergreifende Verfügbarkeit konfiguriert, ist in Azure Time Series Insights keine zusätzliche Konfiguration der regionsübergreifenden Verfügbarkeit erforderlich.

### <a name="iot-and-event-hubs"></a>IoT und Event Hubs

Einige Azure IoT-Dienste enthalten auch integrierte Business Disaster Recovery-Features:

- [Hochverfügbarkeit und Notfallwiederherstellung von IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-ha-dr), einschließlich intraregionaler Redundanz
- [Event Hub-Richtlinien](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr)
- [Azure-Speicherredundanz](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

Die Integration von Time Series Insights mit den anderen Diensten bietet zusätzliche Möglichkeiten für die Notfallwiederherstellung. Beispielsweise können an Ihren Event Hub gesendete Telemetriedaten in einer Sicherungsdatenbank von Azure Blob Storage gespeichert werden.

### <a name="time-series-insights"></a>Time Series Insights

Es gibt mehrere Möglichkeiten, den Betrieb Ihrer Daten, Anwendungen und Dienste von Time Series Insights aufrechtzuerhalten, auch wenn sie gestört sind. 

Möglicherweise ist auch eine vollständige Sicherungskopie Ihrer Azure Time Series-Umgebung für folgende Zwecke erforderlich:

- Als eine *Failoverinstanz* speziell für Time Series Insights, zu der Daten und Datenverkehr umgeleitet werden
- Zum Bewahren von Daten und Überwachungsinformationen

Im Allgemeinen ist der beste Weg, eine Time Series Insights-Umgebung zu duplizieren, die Erstellung einer zweiten Time Series Insights-Umgebung in einer Sicherungs-Azure-Region. Ereignisse werden auch von Ihrer primären Ereignisquelle Ereignisse an diese sekundäre Umgebung gesendet. Stellen Sie sicher, dass Sie eine zweite, dedizierte Consumergruppe verwenden. Folgen Sie den Business Disaster Recovery-Richtlinien dieser Quelle, wie es weiter oben beschrieben ist.

So erstellen Sie eine Duplikatumgebung:

1. Erstellen Sie eine Umgebung in einer zweiten Region. Weitere Informationen finden Sie unter [Erstellen einer neuen Time Series Insights-Umgebung über das Azure-Portal](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-get-started).
1. Erstellen Sie eine zweite dedizierte Consumergruppe für Ihre Ereignisquelle.
1. Verbinden Sie diese Ereignisquelle mit der neuen Umgebung. Stellen Sie sicher, dass Sie die zweite, dedizierte Consumergruppe festlegen.
1. Machen Sie sich mit der [IoT Hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)- und [Event Hub](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access)-Dokumentation für Time Series Insights vertraut.

Bei Auftreten eines Ereignisses:

1. Wenn Ihre primäre Region während eines Notfalls betroffen ist, leiten Sie den Betrieb in die Time Series Insights-Sicherungsumgebung um.
1. Verwenden Sie Ihre zweite Region, um alle Time Series Insights-Telemetrie- und Abfragedaten zu sichern und wiederherzustellen.

> [!IMPORTANT]
> Bei Auftreten eines Failovers:
> 
> * Es kann auch eine Verzögerung auftreten.
> * Es kann eine momentane Spitze bei der Nachrichtenverarbeitung auftreten, wenn der Betrieb und Vorgänge umgeleitet werden.
> 
> Weitere Informationen finden Sie unter [Mildern von Wartezeiten in Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-environment-mitigate-latency).

