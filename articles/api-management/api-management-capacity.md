---
title: Kapazität einer Azure API Management-Instanz | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, was unter der Kapazitätsmetrik zu verstehen ist, und wie man fundierte Entscheidungen in Bezug auf die Skalierung einer Azure API Management-Instanz trifft.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 06/18/2018
ms.author: apimpm
ms.openlocfilehash: fe77361c4c9bed9310f8443ed4ff37faf7ea53a9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60658307"
---
# <a name="capacity-of-an-azure-api-management-instance"></a>Kapazität einer Azure API Management-Instanz

**Kapazität** ist die einzige und wichtigste [Azure Monitor-Metrik](api-management-howto-use-azure-monitor.md#view-metrics-of-your-apis) für das Treffen fundierter Entscheidungen im Hinblick auf die Skalierung einer API Management-Instanz zur Bewältigung höherer Lasten. Ihre Erstellung ist komplex und setzt ein bestimmtes Verhalten voraus.

In diesem Artikel wird erläutert, was die **Kapazität** ist und wie sie sich verhält. Sie erfahren, wie Sie auf **Kapazitätsmetriken** im Azure-Portal zugreifen können, und wann Sie Ihre API Management-Instanz skalieren oder aktualisieren sollten.

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie den Schritten in diesem Artikel folgen können, benötigen Sie folgende Komponenten:

+ Ein aktives Azure-Abonnement.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Eine APIM-Instanz. Weitere Informationen finden Sie unter [Erstellen einer neuen Azure API Management-Dienstinstanz](get-started-create-service-instance.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="what-is-capacity"></a>Was bedeutet Kapazität?

![Kapazitätsmetrik](./media/api-management-capacity/capacity-ingredients.png)

**Kapazität** ist ein Indikator für die Auslastung einer APIM-Instanz. Sie spiegelt die Ressourcenverwendung (CPU, Speicher) und die Länge von Netzwerkwarteschlangen wider. Die CPU- und Speicherauslastung zeigt die Ressourcenverwendung von:

+ APIM-Diensten, wie z.B. Verwaltungsaktionen oder Anfragenbearbeitung, die das Weiterleiten von Anforderungen oder das Ausführen einer Richtlinie einschließen können, und
+ ausgewählten Betriebssystemprozessen, einschließlich Prozesse, die Kosten für SSL-Handshakes bei neuen Verbindungen verursachen.

Die **Gesamtkapazität** ist ein Durchschnitt der eigenen Werte aus jeder Einheit einer API Management-Instanz.

## <a name="capacity-metric-behavior"></a>Verhalten der Kapazitätsmetrik

Aufgrund ihrer Erstellung kann die **Kapazität** in der Realität z.B. durch viele Variablen beeinflusst werden:

+ Verbindungsmuster (neue Verbindung auf Anfrage und Wiederverwendung der bestehenden Verbindung im Vergleich)
+ Größe einer Anforderung und Antwort
+ Richtlinien, die für jede API oder Anzahl der Clients, die Anfragen senden, konfiguriert sind.

Je komplexer die Vorgänge für die Anforderungen sind, desto höher ist der **Kapazitätsverbrauch**. Beispielsweise verbrauchen komplexe Transformationsrichtlinien viel mehr CPU als eine einfache Weiterleitung von Anforderungen. Langsame Back-End-Dienst-Antworten führen ebenfalls zu einer höheren CPU-Last.

> [!IMPORTANT]
> Die **Kapazität** entspricht nicht direkt der Anzahl der verarbeiteten Anforderungen.

![Metrik mit Kapazitätsspitzen](./media/api-management-capacity/capacity-spikes.png)

Die **Kapazität** kann auch vorübergehend Spitzen aufweisen oder größer als 0 (null) sein, selbst wenn keine Anforderungen verarbeitet werden. Dies geschieht aufgrund von system- oder plattformspezifischen Aktionen und sollte bei der Entscheidung, ob eine Instanz skaliert werden soll, nicht berücksichtigt werden.
  
## <a name="use-the-azure-portal-to-examine-capacity"></a>Verwenden des Azure-Portals zum Untersuchen der Kapazität
  
![Kapazitätsmetrik](./media/api-management-capacity/capacity-metric.png)  

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu Ihrer APIM-Instanz.
2. Wählen Sie **Metriken (Vorschau)** aus.
3. Wählen Sie im violetten Abschnitt die Metrik **Kapazität** aus den verfügbaren Metriken aus, und behalten Sie den Standardaggregationstyp **Mittelwert** bei.

    > [!TIP]
    > Um Fehlinterpretationen zu vermeiden, sollten Sie sich immer eine Aufschlüsselung der **Kapazität** nach Standorten ansehen.

4. Wählen Sie im grünen Bereich den **Speicherort** für die Aufteilung der Metrik nach Dimension aus.
5. Wählen Sie den gewünschten Zeitraum aus der oberen Leiste des Abschnitts aus.

    Sie können eine Metrikwarnung festlegen, damit Sie benachrichtigt werden, wenn etwas Unerwartetes passiert. Beispiel: Sie erhalten Benachrichtigungen, wenn Ihre APIM-Instanz ihre erwartete Höchstkapazität mehr als 20 Minuten überschritten hat.

    >[!TIP]
    > Sie können Warnungen konfigurieren, die Sie darüber informieren, wenn der Dienst mit niedriger Kapazität ausgeführt wird, oder die automatischen Skalierungsfunktionen von Azure Monitor verwenden, um automatisch eine Azure API Management-Einheit hinzuzufügen. Der Skalierungsvorgang kann ca. 30 Minuten dauern, daher sollten Sie Ihre Regeln entsprechend planen.  
    > Nur die Skalierung des Masterspeicherorts ist erlaubt.

## <a name="use-capacity-for-scaling-decisions"></a>Treffen von Skalierungsentscheidungen anhand der Kapazität

**Kapazität** ist die Metrik, anhand derer entschieden werden sollte, ob eine API Management-Instanz zur Bewältigung höherer Lasten skaliert werden sollte. Berücksichtigen Sie dabei Folgendes:

+ Berücksichtigen Sie den langfristigen Trend und den Durchschnitt.
+ Ignorieren Sie plötzliche Spitzen, die höchstwahrscheinlich nicht mit einem Anstieg der Last zusammenhängen (siehe Abschnitt „Verhalten der Kapazitätsmetrik“ für eine Erklärung).
+ Aktualisieren oder skalieren Sie die Instanz, wenn der Wert der **Kapazität** über einen längeren Zeitraum (z.B. 30 Minuten) um 60 % oder 70 % steigt. Andere Werte funktionieren möglicherweise besser für Ihren Dienst oder Ihr Szenario.

>[!TIP]  
> Wenn Sie den Datenverkehr im Vorfeld schätzen können, testen Sie Ihre APIM-Instanz auf die von Ihnen erwarteten Workloads. Sie können die Anforderungslast auf Ihren Mandanten schrittweise erhöhen und prüfen, welcher Wert der Kapazitätsmetrik Ihrer Spitzenlast entspricht. Führen Sie die Schritte aus dem vorherigen Abschnitt zur Verwendung des Azure-Portals aus, um zu erkennen, wie viel Kapazität zu einem bestimmten Zeitpunkt verbraucht wird.

## <a name="next-steps"></a>Nächste Schritte

[So skalieren oder aktualisieren Sie eine Azure API Management-Dienstinstanz](upgrade-and-scale.md)