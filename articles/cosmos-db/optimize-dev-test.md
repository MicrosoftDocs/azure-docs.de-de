---
title: Optimale Entwicklungs- und Testmöglichkeiten mit Azure Cosmos DB
description: In diesem Artikel wird erläutert, wie Sie mit Azure Cosmos DB mehrere kostenlose Möglichkeiten für die Entwicklung und das Testen des Diensts nutzen können.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.openlocfilehash: f9cb18b66def144b84de708351743832d1831fbf
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967264"
---
# <a name="optimize-development-and-testing-cost-in-azure-cosmos-db"></a>Optimieren der Entwicklungs- und Testkosten in Azure Cosmos DB

Dieser Artikel beschreibt die verschiedenen Optionen, die Sie mit Azure Cosmos DB kostenlos für Entwicklungs- und Testzwecke verwenden können.

## <a name="azure-cosmos-db-emulator-locally-downloadable-version"></a>Azure Cosmos DB-Emulator (lokal zum Download verfügbare Version)

[Azure Cosmos DB-Emulator](local-emulator.md) ist eine Version, die lokal heruntergeladen werden kann und den Azure Cosmos DB-Clouddienst imitiert. Sie können ohne jegliche Kosten Code schreiben und testen, der die Azure Cosmos DB-APIs verwendet, selbst wenn Sie keine Verbindung zum Netzwerk haben. Der Azure Cosmos DB-Emulator stellt eine lokale Umgebung für Entwicklungszwecke mit hoher Wiedergabetreue für den Clouddienst bereit. Sie können Ihre Anwendung lokal entwickeln und testen, ohne ein Azure-Abonnement zu erstellen. Wenn Sie soweit sind, dass Sie Ihre Anwendung in der Cloud bereitstellen möchten, aktualisieren Sie einfach die Verbindungszeichenfolge, um eine Verbindung mit dem Azure Cosmos DB-Endpunkt in der Cloud herzustellen. Weitere Änderungen sind nicht erforderlich. Um Tests auszuführen, können Sie auch [eine CI/CD-Pipeline mit dem Buildtask des Azure Cosmos DB-Emulators in Azure DevOps einrichten](tutorial-setup-ci-cd.md). Informationen zum Einstieg finden Sie im Artikel [Azure Cosmos DB-Emulator](local-emulator.md).

## <a name="try-azure-cosmos-db-for-free"></a>Azure Cosmos DB kostenlos testen

Mit [Azure Cosmos DB kostenlos testen](https://azure.microsoft.com/try/cosmosdb/) können Sie kostenlos Datenbanken und Sammlungen erstellen und mit Azure Cosmos DB in der Cloud experimentieren. Sie brauchen sich nicht für Azure zu registrieren und auch nichts zu bezahlen. Die Azure Cosmos DB-Testkonten sind für einen begrenzten Zeitraum (derzeit 30 Tage) verfügbar. Sie können jederzeit erneuert werden. Mit Azure Cosmos DB-Testkonten können Sie ganz einfach Azure Cosmos DB evaluieren und mithilfe der Schnellstartanleitung oder der Tutorials eine Anwendung erstellen und testen. Sie können eine Demoversion erstellen oder Komponententests ausführen, ohne dass Kosten entstehen. Wenn Sie Azure Cosmos DB für kostenlose Konten testen, können Sie auch die Premiumfunktionen von Azure Cosmos DB, einschließlich der sofort einsatzbereiten globalen Verteilung, SLAs und der Konsistenzmodelle, kostenlos auswerten. Sie können eine Datenbank mit bis zu 25 Azure Cosmos-Containern und einem Durchsatz von 10.000 RUs/Sek. erstellen. Sie können die Beispielanwendung ausführen, ohne sich für ein Azure-Konto registrieren oder Ihre Kreditkarte verwenden zu müssen. Mit dem kostenlosen Testangebot für Azure Cosmos DB können Sie in wenigen Minuten ein Azure Cosmos-Konto mit mehreren Regionen erstellen und eine App damit ausführen. Informationen zum Einstieg finden Sie auf der Seite [Azure Cosmos DB kostenlos testen](https://azure.microsoft.com/try/cosmosdb/).

## <a name="azure-free-account"></a>Kostenloses Azure-Konto

Azure Cosmos DB ist in dem [kostenlosen Azure-Konto](https://azure.microsoft.com/free) enthalten. Mit diesem Konto erhalten Sie ein kostenloses Azure-Guthaben und -Ressourcen für einen bestimmten Zeitraum. Speziell für Azure Cosmos DB bietet dieses kostenlose Konto einen Speicher von 5 GB und bereitgestellten Durchsatz von 400 RUs/Sek. für das ganze Jahr. Diese Funktion ermöglicht jedem Entwickler ganz einfach und kostenlos das Testen der Funktionen von Azure Cosmos DB oder der Integration mit anderen Azure-Diensten. Mit dem kostenlosen Azure-Konto erhalten Sie ein Guthaben von 200 US-Dollar, das Sie in den ersten 30 Tagen verbrauchen können. Selbst wenn Sie die Dienste schon nutzen, fallen Kosten erst an, wenn Sie ein Upgrade durchführen. Informationen zum Einstieg finden Sie auf der Seite [Kostenloses Azure-Konto](https://azure.microsoft.com/free).

## <a name="next-steps"></a>Nächste Schritte

Zum Einstieg in die Verwendung des Emulators oder des kostenlosen Azure Cosmos DB-Kontos sind folgende Artikel verfügbar:

* Weitere Informationen unter [Optimale Entwicklungs- und Testmöglichkeiten mit Azure Cosmos DB](optimize-dev-test.md)
* Weitere Informationen unter [Erläuterungen zu Ihrer Azure Cosmos DB-Rechnung](understand-your-bill.md)
* Weitere Informationen unter [Optimieren der Durchsatzkosten](optimize-cost-throughput.md)
* Weitere Informationen unter [Optimieren der Speicherkosten](optimize-cost-storage.md)
* Weitere Informationen unter [Optimieren der Kosten für Lese- und Schreibvorgänge](optimize-cost-reads-writes.md)
* Weitere Informationen unter [Optimieren der Kosten von Abfragen](optimize-cost-queries.md)
* Weitere Informationen unter [Optimieren der Kosten bei Cosmos-Konten mit mehreren Regionen](optimize-cost-regions.md)

