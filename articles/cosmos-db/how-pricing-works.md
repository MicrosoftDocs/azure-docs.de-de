---
title: Preismodell für Azure Cosmos DB
description: In diesem Artikel werden das Preismodell für Azure Cosmos DB und die Möglichkeiten zur Vereinfachung von Kostenmanagement und Kostenplanung beschrieben.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: 978a2909202e01d8c58ba9253bfe0b3996b72d56
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100880"
---
# <a name="pricing-model-in-azure-cosmos-db"></a>Preismodell in Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Das Preismodell für Azure Cosmos DB vereinfacht das Kostenmanagement und die Kostenplanung. Mit Azure Cosmos DB zahlen Sie für die Vorgänge, die Sie für die Datenbank ausführen, und für den von Ihren Daten genutzten Speicher.

- **Datenbankvorgänge** : Die Art und Weise, wie Ihre Datenbankvorgänge abgerechnet werden, hängt vom Typ des Azure Cosmos-Kontos ab, das Sie verwenden.

  - **Bereitgestellter Durchsatz** : Der [bereitgestellte Durchsatz](set-throughput.md) (auch als reservierter Durchsatz bezeichnet) bietet eine hohe Leistungsfähigkeit bei jeder Größenordnung. Sie geben den benötigten Durchsatz in [Anforderungseinheiten](request-units.md) pro Sekunde (RUs/s) an, und Azure Cosmos DB reserviert die Ressourcen, die erforderlich sind, um den konfigurierten Durchsatz bereitzustellen. Sie können [Durchsatz entweder für eine Datenbank oder einen Container bereitstellen](set-throughput.md). Basierend auf Ihren Workloadanforderungen können Sie den Durchsatz jederzeit hoch- oder herunterskalieren oder [Autoskalierung](provision-throughput-autoscale.md) verwenden (obwohl ein Mindestdurchsatz für eine Datenbank oder einen Container erforderlich ist, um die SLAs zu garantieren). Die Abrechnung erfolgt auf Stundenbasis für den für die jeweilige Stunde bereitgestellten maximalen Durchsatz.

   > [!NOTE]
   > Da das Modell mit bereitgestelltem Durchsatz Ressourcen dediziert für Ihren Container oder Ihre Datenbank bereitstellt, wird Ihnen der Durchsatz, den Sie bereitgestellt haben, auch dann in Rechnung gestellt, wenn Sie keine Workloads ausführen.

  - **Serverlos** : Im [serverlosen](serverless.md) Modus müssen Sie beim Erstellen von Ressourcen in Ihrem Azure Cosmos-Konto keinen Durchsatz bereitstellen. Am Ende Ihres Abrechnungszeitraums wird Ihnen die Anzahl der Anforderungseinheiten in Rechnung gestellt, die von den Datenbankvorgängen genutzt wurden.

- **Storage** : Für die Gesamtmenge des Speichers (in GB), der für Daten und Indizes für eine angegebene Stunde genutzt wird, wird eine Pauschale berechnet. Der Speicher wird auf Nutzungsbasis abgerechnet, sodass Sie keinen Speicher im Voraus reservieren müssen. Die Berechnung erfolgt nur für den Speicher, den Sie nutzen.

Das Preismodell in Azure Cosmos DB ist für alle APIs einheitlich. Weitere Informationen finden Sie auf der [Azure Cosmos DB-Preisseite](https://azure.microsoft.com/pricing/details/cosmos-db/), unter [Erläuterungen zu Ihrer Azure Cosmos DB-Rechnung](understand-your-bill.md) sowie unter [Kosteneffektivität des Azure Cosmos DB-Preismodells für Kunden](total-cost-ownership.md).

Wenn Sie Ihr Azure Cosmos DB-Konto in einer Region in den USA bereitstellen, die keine Government-Region ist, gilt ein Mindestpreis für datenbank- und containerbasierten Durchsatz im Modus „bereitgestellter Durchsatz“. Im serverlosen Modus gibt es keinen Mindestpreis. Die Preise variieren je nach verwendeter Region. Aktuelle Preisinformationen finden Sie auf der Seite [Azure Cosmos DB – Preise](https://azure.microsoft.com/pricing/details/cosmos-db/).

## <a name="try-azure-cosmos-db-for-free"></a>Azure Cosmos DB kostenlos testen

Azure Cosmos DB bietet viele Optionen für Entwickler kostenlos an. Die Optionen umfassen:

* **Free-Tarif von Azure Cosmos DB** : Der Free-Tarif von Azure Cosmos DB erleichtert Ihnen den Einstieg, die Entwicklung und die Durchführung von Tests für Ihre Anwendungen. Sie können sogar kleinere Produktionsworkloads kostenlos ausführen. Wenn für ein Konto der Free-Tarif aktiviert ist, sind die ersten 400 RU/s und 5 GB Speicher während der gesamten Lebensdauer des Kontos kostenlos. Sie können pro Azure-Abonnement maximal ein Konto im Free-Tarif einrichten und müssen sich beim Erstellen des Kontos registrieren. [Erstellen Sie zunächst ein neues Konto im Azure-Portal mit aktiviertem Free-Tarif](create-cosmosdb-resources-portal.md), oder verwenden Sie eine [Resource Manager-Vorlage](./manage-with-templates.md#free-tier).

* **Kostenloses Azure-Konto** : Azure bietet einen [Free-Tarif](https://azure.microsoft.com/free/) an, mit dem Sie ein Azure-Guthaben von 200 USD für die ersten 30 Tage und 12 Monate lang eine begrenzte Anzahl kostenloser Dienste erhalten. Weitere Informationen finden Sie unter [Kostenloses Azure-Konto](../cost-management-billing/manage/avoid-charges-free-account.md). Azure Cosmos DB ist Bestandteil des kostenlosen Azure-Kontos. Speziell für Azure Cosmos DB bietet dieses kostenlose Konto einen Speicher von 5 GB und einen bereitgestellten Durchsatz von 400 RU/s für das ganze Jahr.

* **Azure Cosmos DB kostenlos testen** : Azure Cosmos DB bietet eine zeitlich begrenzte Benutzererfahrung, indem Sie Azure Cosmos DB für kostenlose Konten testen. Mithilfe der Schnellstartanleitungen und den Tutorials können Sie ein Azure Cosmos DB-Konto sowie eine Datenbank und Sammlungen erstellen und eine Beispielanwendung ausführen. Sie können die Beispielanwendung ausführen, ohne sich für ein Azure-Konto registrieren oder Ihre Kreditkarte verwenden zu müssen. [Azure Cosmos DB kostenlos testen](https://azure.microsoft.com/try/cosmosdb/) stellt Azure Cosmos DB einen Monat lang mit der Möglichkeit bereit, Ihr Konto beliebig oft zu erneuern.

* **Azure Cosmos DB-Emulator** : Der Azure Cosmos DB-Emulator stellt eine lokale Umgebung bereit, die den Azure Cosmos DB-Dienst zu Entwicklungszwecken emuliert. Der Emulator wird kostenlos und mit hoher Wiedergabetreue für den Clouddienst angeboten. Bei Verwendung des Azure Cosmos DB-Emulators können Sie die Anwendung lokal entwickeln und testen, ohne ein Azure-Abonnement zu erstellen oder Kosten zu verursachen. Sie können Ihre Anwendungen mit dem Emulator lokal entwickeln, bevor sie in die Produktion gehen. Nachdem Sie mit der Funktionalität der Anwendung im Emulator zufrieden sind, können Sie zum Azure Cosmos DB-Konto in der Cloud wechseln und so erheblich Kosten sparen. Ausführlichere Informationen zum Emulator finden Sie im Artikel [Verwenden von Azure Cosmos DB für Entwicklungs- und Testzwecke](local-emulator.md).

## <a name="pricing-with-reserved-capacity"></a>Preise mit reservierter Kapazität

Die [reservierte Kapazität](cosmos-db-reserved-capacity.md) von Azure Cosmos DB hilft Ihnen, Geld zu sparen, wenn der Modus „bereitgestellter Durchsatz“ verwendet wird, indem Sie die Ressourcen von Azure Cosmos DB entweder für ein Jahr oder für drei Jahre im Voraus bezahlen. Mit der Vorabfestlegung und Bezahlung für ein Jahr oder drei Jahre können Sie Ihre Kosten deutlich senken und gegenüber der regulären Preisgestaltung zwischen 20 % und 65 % sparen. Mit der reservierten Kapazität von Azure Cosmos DB können Sie durch Zahlung des bereitgestellten Durchsatzes (RUs/Sek.) für ein Jahr oder drei Jahre im Voraus die Kosten senken und auf den bereitgestellten Umsatz einen Rabatt erhalten. 

Die reservierte Kapazität bezieht sich auf einen Rechnungsrabatt und wirkt sich nicht auf den Status Ihrer Azure Cosmos DB-Ressourcen zur Laufzeit aus. Die reservierte Kapazität steht einheitlich für alle APIs (einschließlich MongoDB, Cassandra, SQL, Gremlin und Azure Tables ) sowie alle Regionen weltweit zur Verfügung. Erfahren Sie mehr über reservierte Kapazität im Artikel [Vorauszahlen für Azure Cosmos DB-Ressourcen mit reservierter Kapazität](cosmos-db-reserved-capacity.md), und erwerben Sie reservierte Kapazität im [Azure-Portal](https://portal.azure.com/).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die Optimierung der Kosten für Ihre Azure Cosmos DB-Ressourcen in den folgenden Artikeln:

* Weitere Informationen unter [Optimale Entwicklungs- und Testmöglichkeiten mit Azure Cosmos DB](optimize-dev-test.md)
* Weitere Informationen unter [Erläuterungen zu Ihrer Azure Cosmos DB-Rechnung](understand-your-bill.md)
* Weitere Informationen unter [Optimieren der Durchsatzkosten](optimize-cost-throughput.md)
* Weitere Informationen unter [Optimieren der Speicherkosten](optimize-cost-storage.md)
* Weitere Informationen unter [Optimieren der Kosten für Lese- und Schreibvorgänge](optimize-cost-reads-writes.md)
* Weitere Informationen unter [Optimieren der Kosten von Abfragen](./optimize-cost-reads-writes.md)
* Weitere Informationen unter [Optimieren der Kosten bei Cosmos-Konten mit mehreren Regionen](optimize-cost-regions.md)
* Weitere Informationen zur [Reservierte Azure Cosmos DB-Kapazität](cosmos-db-reserved-capacity.md)
* Weitere Informationen zum [Azure Cosmos DB-Emulator](local-emulator.md)