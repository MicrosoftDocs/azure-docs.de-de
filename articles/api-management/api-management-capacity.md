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
ms.custom: fasttrack-edit
ms.openlocfilehash: c7c002b57f2220ac0a9fba43a8081b2a4ed800e7
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93081058"
---
# <a name="capacity-of-an-azure-api-management-instance"></a>Kapazität einer Azure API Management-Instanz

**Kapazität** ist die wichtigste [Azure Monitor-Metrik](api-management-howto-use-azure-monitor.md#view-metrics-of-your-apis) für das Treffen fundierter Entscheidungen im Hinblick auf die Skalierung einer API Management-Instanz zur Bewältigung höherer Lasten. Ihre Erstellung ist komplex und setzt ein bestimmtes Verhalten voraus.

In diesem Artikel wird erläutert, was die **Kapazität** ist und wie sie sich verhält. Sie erfahren, wie Sie auf **Kapazitätsmetriken** im Azure-Portal zugreifen können, und wann Sie Ihre API Management-Instanz skalieren oder aktualisieren sollten.

> [!IMPORTANT]
> In diesem Artikel wird erläutert, wie Sie Ihre Azure API Management-Instanz basierend auf der Kapazitätsmetrik überwachen und skalieren können. Es ist jedoch ebenso wichtig zu verstehen, was passiert, wenn die Kapazität einer API Management-Instanz tatsächlich *erreicht* ist. In Azure API Management wird keine Drosselung auf Dienstebene angewandt, um eine physische Überladung der Instanzen zu verhindern. Wenn die physische Kapazität einer Instanz erreicht ist, gleicht das Verhalten dem eines überlasteten Webservers, auf dem eingehende Anforderungen nicht verarbeitet werden können: Die Latenz steigt an, Verbindungen werden unterbrochen, Timeoutfehler treten auf usw. Das bedeutet, dass API-Clients so vorbereitet werden sollten, dass in diesem Fall wie bei jedem anderen externen Dienst vorgegangen wird (z. B. durch Anwendung von Wiederholungsrichtlinien).

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie den Schritten in diesem Artikel folgen können, benötigen Sie folgende Komponenten:

+ Ein aktives Azure-Abonnement.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Eine APIM-Instanz. Weitere Informationen finden Sie unter [Erstellen einer neuen Azure API Management-Dienstinstanz](get-started-create-service-instance.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="what-is-capacity"></a>Was bedeutet Kapazität?

![Diagramm zur Erläuterung der Kapazitätsmetrik](./media/api-management-capacity/capacity-ingredients.png)

Die **Kapazität** ist ein Indikator für die Auslastung einer API Management-Instanz. Sie spiegelt die Ressourcenverwendung (CPU, Speicher) und die Länge von Netzwerkwarteschlangen wider. Die CPU- und Speicherauslastung zeigt die Ressourcenverwendung von:

+ API Management-Diensten auf Datenebene, wie z. B. die Anforderungsverarbeitung, die das Weiterleiten von Anforderungen oder das Ausführen einer Richtlinie umfassen kann
+ API Management-Diensten auf Verwaltungsebene, z. B. über das Azure-Portal oder ARM angewandte Verwaltungsaktionen oder vom [Entwicklerportal](api-management-howto-developer-portal.md) stammende Lasten
+ Ausgewählten Betriebssystemprozessen, einschließlich Prozessen, für die Kosten für TLS-Handshakes bei neuen Verbindungen anfallen

Die **Gesamtkapazität** ist ein Durchschnitt der eigenen Werte aus jeder Einheit einer API Management-Instanz.

Obwohl die **Kapazitätsmetrik** für die Ermittlung von Problemen mit Ihrer API Management-Instanz entworfen wurde, gibt es Fälle, bei denen Probleme nicht in den Änderungen der **Kapazitätsmetrik** berücksichtigt werden.

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

Eine niedrige **Kapazitätsmetrik** bedeutet nicht zwingend, dass Ihre API Management-Instanz keine Probleme aufweist.
  
## <a name="use-the-azure-portal-to-examine-capacity"></a>Verwenden des Azure-Portals zum Untersuchen der Kapazität
  
![Kapazitätsmetrik](./media/api-management-capacity/capacity-metric.png)  

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu Ihrer APIM-Instanz.
2. Klicken Sie auf **Metriken**.
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

- [So skalieren oder aktualisieren Sie eine Azure API Management-Dienstinstanz](upgrade-and-scale.md)
- [Ermitteln und Analysieren von Kosten mit der Kostenanalyse](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)