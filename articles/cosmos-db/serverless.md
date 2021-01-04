---
title: Verbrauchsbasiertes serverloses Angebot in Azure Cosmos DB
description: Erfahren Sie mehr über das nutzungsbasierte serverlose Angebot für Azure Cosmos DB.
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/25/2020
ms.openlocfilehash: cabc243c6ba74217873b5b0a5fa51a7cb410512e
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96170830"
---
# <a name="azure-cosmos-db-serverless-preview"></a>Azure Cosmos DB serverlos (Vorschau)
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

> [!IMPORTANT]
> Azure Cosmos DB serverlos befindet sich derzeit in der Vorschauphase. Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Mit Azure Cosmos DB serverlos können Sie Ihr Azure Cosmos-Konto auf verbrauchsbasierte Weise verwenden. Dabei werden Ihnen nur die Anforderungseinheiten in Rechnung gestellt, die von den Datenbankvorgängen und dem von Ihren Daten genutzten Speicher beansprucht werden. Wenn Sie Azure Cosmos DB im serverlosen Modus verwenden, fällt keine Mindestgebühr an.

> [!IMPORTANT] 
> Haben Sie Feedback zu Azure Cosmos DB serverlos? Teilen Sie uns Ihre Meinung mit! Sie können eine Nachricht an das Azure Cosmos DB serverlos-Team senden: [azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com).

Wenn Sie Azure Cosmos DB verwenden, werden für jeden Datenbankvorgang Kosten in [Anforderungseinheiten](request-units.md) ausgedrückt. Wie Ihnen diese Kosten in Rechnung gestellt werden, hängt vom Typ des Azure Cosmos-Kontos ab, das Sie verwenden:

- Im Modus [bereitgestellter Durchsatz](set-throughput.md) müssen Sie sich auf eine bestimmte Menge an Durchsatz (ausgedrückt in Anforderungseinheiten pro Sekunde) festlegen, der für Ihre Datenbanken und Container bereitgestellt wird. Die Kosten für Ihre Datenbankvorgänge werden dann von der Anzahl der pro Sekunde verfügbaren Anforderungseinheiten abgezogen. Am Ende Ihres Abrechnungszeitraums wird Ihnen die bereitgestellte Menge an Durchsatz in Rechnung gestellt.
- Im serverlosen Modus müssen Sie beim Erstellen von Containern in Ihrem Azure Cosmos-Konto keinen Durchsatz bereitstellen. Am Ende Ihres Abrechnungszeitraums wird Ihnen die Anzahl der Anforderungseinheiten in Rechnung gestellt, die von den Datenbankvorgängen genutzt wurden.

## <a name="use-cases"></a>Anwendungsfälle

Azure Cosmos DB Server serverlos ist am besten für Szenarien geeignet, in denen Folgendes erwartet wird:

- **Niedriger, zeitweiliger und unvorhersehbarer Datenverkehr:** Da die Bereitstellung von Kapazität in solchen Fällen nicht erforderlich und unter Umständen kostenintensiv ist.
- **Mittlere Leistung**: Da serverlose Container [bestimmten Leistungsmerkmale](#performance) aufweisen.

Aus diesen Gründen sollte Azure Cosmos DB serverlos für die folgenden Workloadtypen in Betracht gezogen werden:

- Entwicklung
- Testen
- Prototypenerstellung
- Proof of Concept (PoC)
- Nicht kritische Anwendung mit geringem Datenverkehr

Weitere Anleitungen zum Auswählen des Angebots, das Ihrem Anwendungsfall am besten entspricht, finden Sie im Artikel [Wählen zwischen bereitgestelltem Durchsatz und serverlos](throughput-serverless.md).

## <a name="using-serverless-resources"></a>Verwenden von serverlosen Ressourcen

Serverlos ist ein neuer Azure Cosmos-Kontotyp. Dies bedeutet, dass Sie zwischen **bereitgestelltem Durchsatz** und **serverlos** wählen müssen, wenn Sie ein neues Konto erstellen. Sie müssen ein neues serverloses Konto erstellen, um mit serverless zu beginnen. Während der Vorschauversion ist die einzige unterstützte Möglichkeit, ein neues serverloses Konto zu erstellen, die [Verwendung des Azure-Portals](create-cosmosdb-resources-portal.md). Das Migrieren vorhandener Konten zum/aus dem serverlosen Modus wird derzeit nicht unterstützt.

Jeder Container, der in einem serverlosen Konto erstellt wird, ist ein serverloser Container. Serverlose Container stellen dieselben Funktionen wie Container bereit, die im Modusfpr bereitgestellten Durchsatz erstellt wurden, sodass Sie Ihre Daten genau auf die gleiche Weise lesen, schreiben und abfragen. Serverlose Konten und Container haben jedoch auch bestimmte Merkmale:

> [!IMPORTANT]
> Einige dieser Einschränkungen können gelockert oder aufgehoben werden, wenn serverlos allgemein verfügbar wird, und **Ihr Feedback** wird uns bei der Entscheidung unterstützen! Nehmen Sie Kontakt mit uns auf, und erzählen Sie uns mehr über Ihre Erfahrungen mit serverlos: [azurecosmosdbserverless@service.microsoft.com](mailto:azurecosmosdbserverless@service.microsoft.com).

- Ein serverloses Konto kann nur in einer einzelnen Azure-Region ausgeführt werden. Es ist nicht möglich, einem serverlosen Konto nach der Erstellung zusätzliche Azure-Regionen hinzuzufügen.
- Es ist nicht möglich, die [Synapse Link-Vorschaufunktion](synapse-link.md) für ein serverloses Konto zu aktivieren.
- Bereitstellung von Durchsatz ist für serverlose Container nicht erforderlich, daher gelten die folgenden Aussagen:
    - Wenn Sie einen serverlosen Container erstellen, können Sie keinen Durchsatz übergeben. Bei einem Versuch wird ein Fehler zurückgegeben.
    - Sie können den Durchsatz für einen serverlosen Container nicht lesen oder aktualisieren, und es wird ein Fehler zurückgegeben.
    - Sie können in einem serverlosen Konto keine Datenbank mit freigegebenem Durchsatz erstellen, und es wird ein Fehler zurückgegeben.
- Serverlose Container können maximal 50 GB Daten und Indizes speichern.

## <a name="monitoring-your-consumption"></a>Überwachen Ihres Verbrauchs

Wenn Sie Azure Cosmos DB bereits im Modus „Bereitgestellter Durchsatz“ verwendet haben, werden Sie feststellen, dass serverlos kostengünstiger ist, wenn Ihr Datenverkehr die bereitgestellte Kapazität nicht rechtfertigt. Der Kompromiss besteht darin, dass Ihre Kosten weniger vorhersehbar werden, da die Abrechnung auf der Grundlage der Anzahl der Anforderungen erfolgt, die Ihre Datenbank bearbeitet hat. Aus diesem Grund ist es wichtig, den aktuellen Verbrauch im Auge zu behalten.

Wenn Sie den Bereich **Metriken** Ihres Kontos durchsuchen, finden Sie unter der Registerkarte **Übersicht** ein Diagramm mit dem Namen **Verbrauchte Anforderungseinheiten**. Dieses Diagramm zeigt, wie viele Anforderungseinheiten Ihr Konto verbraucht hat:

:::image type="content" source="./media/serverless/request-units-consumed.png" alt-text="Diagramm mit den verbrauchten Anforderungseinheiten" border="false":::

Sie finden dasselbe Diagramm, wenn Sie Azure Monitor verwenden, wie [hier](monitor-request-unit-usage.md) beschrieben. Beachten Sie, dass Sie mit Azure Monitor [Warnungen](../azure-monitor/platform/alerts-metric-overview.md) einrichten können, mit denen Sie benachrichtigt werden können, wenn der Verbrauch von Anforderungseinheiten einen bestimmten Schwellenwert überschritten hat.

## <a name="performance"></a><a id="performance"></a>Leistung

Serverlose Ressourcen ergeben bestimmte Leistungsmerkmale, die davon unterscheiden, was Ressourcen mit bereitgestelltem Durchsatz bieten:

- **Verfügbarkeit:** Nachdem das serverlose Angebot allgemein verfügbar ist, wird die Verfügbarkeit der serverlosen Container durch eine Vereinbarung zum Servicelevel (SLA) von 99,9 % abgedeckt, wenn keine Verfügbarkeitszonen (Zonenredundanz) verwendet werden. Die SLA beträgt 99,99 %, wenn Verfügbarkeitszonen verwendet werden.
- **Latenz**: Nachdem das serverlose Angebot allgemein verfügbar ist, wird die Latenz der serverlosen Container durch ein Servicelevelziel (SLO) von 10 Millisekunden oder weniger für Punktlesevorgänge und 30 Millisekunden oder weniger für Schreibvorgänge abgedeckt. Ein Punktlesevorgang besteht aus dem Abrufen eines einzelnen Elements anhand seiner ID und seines Partitionsschlüsselwerts.
- **Burstfähigkeit**: Nachdem das serverlose Angebot allgemein verfügbar ist, wird die Burstfähigkeit von serverlosen Containern durch ein Servicelevelziel (SLO) von 95 % abgedeckt. Dies bedeutet, dass die maximale Burstfähigkeit mindestens 95 % der Zeit erreicht werden kann.

> [!NOTE]
> Wie jede Azure-Vorschauversion wird Azure Cosmos DB serverlos aus Vereinbarungen zum Service Level (SLA) ausgeschlossen. Die oben erwähnten Leistungsmerkmale werden als Vorschauversion bereitgestellt, die das Angebot bereitstellt, wenn es allgemein verfügbar ist.

## <a name="next-steps"></a>Nächste Schritte

Machen Sie sich mit den folgenden Artikeln mit serverlos vertraut:

- [Anforderungseinheiten in Azure Cosmos DB](request-units.md)
- [Wählen zwischen bereitgestelltem Durchsatz und serverlos](throughput-serverless.md)
- [Preismodell in Azure Cosmos DB](how-pricing-works.md)
