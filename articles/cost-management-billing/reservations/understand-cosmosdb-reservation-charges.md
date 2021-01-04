---
title: Grundlegendes zum Reservierungsrabatt in Azure Cosmos DB
description: Hier erfahren Sie, wie der Rabatt für Reservierungen auf bereitgestellten Durchsatz (RU/s) in Azure Cosmos DB angewendet wird.
author: bandersmsft
ms.author: banders
ms.reviewer: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/12/2020
ms.openlocfilehash: ca4c1810912771f56661ca5b682b6991735f526e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023141"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-cosmos-db"></a>Grundlegendes zur Anwendung des Rabatts für Reservierungen auf Azure Cosmos DB

Nachdem Sie eine reservierte Azure Cosmos DB-Kapazität erworben haben, wird der Reservierungsrabatt automatisch auf Azure Cosmos DB-Ressourcen angewendet, die den Attributen und der Menge der Reservierung entsprechen. Eine Reservierung deckt den für die Azure Cosmos DB-Ressourcen bereitgestellten Durchsatz ab. Sie deckt jedoch keine Gebühren für Software, Netzwerke, Speicher oder vordefinierte Container ab.

## <a name="how-reservation-discount-is-applied"></a>Wie der Reservierungsrabatt angewendet wird

Reservierungsrabatte funktionieren nach dem Prinzip „*use-it-or-lose-it*“. Das heißt, wenn Sie für eine Stunde nicht über die entsprechenden Ressourcen verfügen, verlieren Sie eine Reservierungsmenge für diese Stunde. Sie können ungenutzte reservierte Stunden nicht übertragen.

Wenn Sie eine Ressource beenden, wird der Reservierungsrabatt automatisch auf eine andere entsprechende Ressource im angegebenen Reservierungsumfang angewandt. Wenn keine übereinstimmenden Ressourcen im angegebenen Reservierungsumfang gefunden werden, gehen die reservierten Stunden *verloren*.

## <a name="reservation-discount-applied-to-azure-cosmos-db-accounts"></a>Auf Azure Cosmos DB-Konten angewendeter Rabatt für Reservierungen

Auf den [bereitgestellten Durchsatz](../../cosmos-db/request-units.md) wird ein Reservierungsrabatt in Form von Anforderungseinheiten pro Sekunde (RU/s) auf Stundenbasis angewendet. Bei Azure Cosmos DB-Ressourcen, die nicht über die gesamte Stunde hinweg ausgeführt werden, wird der Reservierungsrabatt automatisch auf andere Cosmos DB-Ressourcen angewendet, die den Reservierungsattributen entsprechen. Der Rabatt kann auf Azure Cosmos DB-Ressourcen angewendet werden, die gleichzeitig ausgeführt werden. Wenn die den Reservierungsattributen entsprechenden Cosmos DB-Ressourcen nicht über die gesamte Stunde hinweg ausgeführt werden, profitieren Sie nicht im vollen Umfang vom Reservierungsrabatt für diese Stunde.

Die Rabatte sind gestaffelt. Für Reservierungen mit mehr Anforderungseinheiten gibt es höhere Rabatte.

Mit dem Erwerb von Reservierungen werden Rabatte auf alle Regionen im Verhältnis zu den regionalen On-Demand-Preisen angewendet. Informationen zu den Reservierungsrabattsätzen in den einzelnen Regionen finden Sie im Abschnitt [Reservierungsrabatte nach Region](#reservation-discount-per-region) dieses Artikels.

## <a name="reservation-discount-per-region"></a>Reservierungsrabatte nach Region
Reservierungsrabatte werden auf Stundenbasis auf die Azure Cosmos DB-Durchsatzkosten angewendet. Sie gelten entweder pro Abonnement oder pro registriertem Umfang/Kontobereich. Der Reservierungsrabatt wird auf Verbrauchseinheiten in den verschiedenen Regionen mit folgenden Sätzen angewendet:

|Beschreibung der Verbrauchseinheit  |Region |Verhältnis  |
|---------|---------|---------|
|Azure Cosmos DB – 100 RU/s pro Stunde – Asien-Pazifik, Südosten  |  Asien-Pazifik, Südosten    |   1      |
|Azure Cosmos DB – 100 RU/s pro Stunde – Asien-Pazifik, Osten |   Asien-Pazifik, Osten   |    1     |
|Azure Cosmos DB – 100 RU/s pro Stunde – Europa, Norden|  Europa, Norden       |    1     |
|Azure Cosmos DB – 100 RU/s pro Stunde – Südkorea, Süden|    Südkorea, Süden     |     1    |
|Azure Cosmos DB – 100 RU/s pro Stunde – Europa, Westen|    Europa, Westen     |      1   |
|Azure Cosmos DB – 100 RU/s pro Stunde – Südkorea, Mitte|   Südkorea, Mitte    |       1  |
|Azure Cosmos DB – 100 RU/s pro Stunde – Vereinigtes Königreich, Süden|   UK, Süden      |     1    |
|Azure Cosmos DB – 100 RU/s pro Stunde – Vereinigtes Königreich, Westen|   UK, Westen      |    1     |
|Azure Cosmos DB – 100 RU/s pro Stunde – Vereinigtes Königreich, Norden |   Vereinigtes Königreich, Norden    |     1    |
|Azure Cosmos DB – 100 RU/s pro Stunde – Vereinigtes Königreich, Süden 2|   Vereinigtes Königreich, Süden 2      |     1    |
|Azure Cosmos DB – 100 RU/s pro Stunde – USA, Osten 2|  USA (Ost 2)     |     1    |
|Azure Cosmos DB – 100 RU/s pro Stunde – USA, Norden-Mitte|   USA, Norden-Mitte      |     1    |
|Azure Cosmos DB – 100 RU/s pro Stunde – USA, Westen|   USA, Westen      |     1    |
|Azure Cosmos DB – 100 RU/s pro Stunde – USA, Mitte| USA, Mitte        |     1    |
|Azure Cosmos DB – 100 RU/s pro Stunde – USA, Westen 2|   USA, Westen 2      |      1   |
|Azure Cosmos DB – 100 RU/s pro Stunde – USA, Westen-Mitte|   USA, Westen-Mitte      |       1  |
|Azure Cosmos DB – 100 RU/s pro Stunde – USA, Osten|   USA, Osten      |  1       |
|Azure Cosmos DB – 100 RU/s pro Stunde – Südafrika, Norden|     Südafrika, Norden    |   1      |
|Azure Cosmos DB – 100 RU/s pro Stunde – Südafrika, Westen |    Südafrika, Westen      |    1     |
|Azure Cosmos DB – 100 RU/s pro Stunde – Indien, Süden|    Indien, Süden     |    1,0375    |
|Azure Cosmos DB – 100 RU/s pro Stunde – Kanada, Osten|   Kanada, Osten      |    1.1      |
|Azure Cosmos DB – 100 RU/s pro Stunde – Japan, Osten|   Japan, Osten      |    1,125     |
|Azure Cosmos DB – 100 RU/s pro Stunde – Japan, Westen|     Japan, Westen    |   1,125       |
|Azure Cosmos DB – 100 RU/s pro Stunde – Indien, Westen|     Indien, Westen    |    1,1375     |
|Azure Cosmos DB – 100 RU/s pro Stunde – Indien, Mitte|    Indien, Mitte     |  1,1375       |
|Azure Cosmos DB – 100 RU/s pro Stunde – Australien, Osten|     Australien, Osten    |   1,15       |
|Azure Cosmos DB – 100 RU/s pro Stunde – Kanada, Mitte|  Kanada, Mitte       |   1.2       |
|Azure Cosmos DB – 100 RU/s pro Stunde – Frankreich, Mitte|   Frankreich, Mitte      |    1,25      |
|Azure Cosmos DB – 100 RU/s pro Stunde – Brasilien, Süden|  Brasilien, Süden       |   1.5      |
|Azure Cosmos DB – 100 RU/s pro Stunde – Australien, Mitte|   Australien, Mitte      |   1.5      |
|Azure Cosmos DB – 100 RU/s pro Stunde – Australien, Mitte 2| Australien, Mitte 2        |    1.5     |
|Azure Cosmos DB – 100 RU/s pro Stunde – Frankreich, Süden|    Frankreich, Süden     |    1,625     |

## <a name="scenarios-that-show-how-the-reservation-discount-is-applied"></a>Szenarios zur Anwendung des Rabatts für Reservierungen

Für eine Reservierung gelten folgende Anforderungen:

* Erforderlicher Durchsatz: 50.000 RU/s  
* Genutzte Regionen: 2

In diesem Fall werden Ihnen On-Demand-Gebühren für 500 Einheiten zu 100 RU/s Verbrauchseinheiten in diesen beiden Regionen berechnet. Der Gesamtverbrauch beträgt 100.000 RU/s pro Stunde.

**Szenario 1**

Beispiel: Sie benötigen Azure Cosmos DB-Bereitstellungen in den Regionen „USA, Norden-Mitte“ und „USA, Westen“. Jede Region verbraucht einen Durchsatz von 50.000 RU/s. Ein Reservierungserwerb von 100.000 RU/s würde Ihre On-Demand-Gebühren vollständig ausgleichen.

Der durch eine Reservierung abgedeckte Rabatt wird wie folgt berechnet: verbrauchter Durchsatz * Reservierungsrabattsatz_für_diese_Region. Für die Regionen „USA, Norden-Mitte“ und „USA, Westen“ beträgt der Reservierungsrabattsatz „1“. Somit wird für insgesamt 100.000 RU/s ein Rabatt gewährt. Dieser Wert wird wie folgt berechnet: 50.000 × 1 + 50.000 × 1 = 100.000 RU/s. Sie müssen keine zusätzlichen Gebühren zu den üblichen Tarifen der nutzungsbasierten Bezahlung zahlen.

|Beschreibung der Verbrauchseinheit | Region |Verbrauchter Durchsatz (RU/s) |Anwendung des Reservierungsrabatts auf Anforderungseinheiten pro Sekunde |
|---------|---------|---------|---------|
|Azure Cosmos DB – 100 RU/s pro Stunde – USA, Norden-Mitte  |   USA, Norden-Mitte  | 50.000  | 50.000  |
|Azure Cosmos DB – 100 RU/s pro Stunde – USA, Westen  |  USA, Westen   |  50.000  |  50.000 |

**Szenario 2**

Beispiel: Sie benötigen Azure Cosmos DB-Bereitstellungen in den Regionen „Australien, Mitte 2“ und „Frankreich, Süden“. Jede Region verbraucht einen Durchsatz von 50.000 RU/s. Hier würde der folgende Reservierungserwerb von 100.000 RU/s wie folgt gelten (vorausgesetzt, der Verbrauch für „Australien, Mitte 2“ wird zuerst rabattiert):

|Beschreibung der Verbrauchseinheit | Region |Verbrauchter Durchsatz (RU/s) |Anwendung des Reservierungsrabatts auf Anforderungseinheiten pro Sekunde |
|---------|---------|---------|---------|
|Azure Cosmos DB – 100 RU/s pro Stunde – Australien, Mitte 2  |  Australien, Mitte 2   |  50.000  |  50.000   |
|Azure Cosmos DB – 100 RU/s pro Stunde – Frankreich, Süden  |  Frankreich, Süden   |  50.000 |  15.384  |

50.000 Verbrauchseinheiten in der Region „Australien, Mitte 2“ entsprechen 75.000 RU/s abrechenbarem Verbrauch (oder normalisertem Verbrauch). Dieser Wert wird wie folgt berechnet: verbrauchter Durchsatz * Reservierungsrabattsatz_für_diese_Region. Die Berechnung entspricht 75.000 RU/s abrechenbarer bzw. normalisierter Nutzung. Dieser Wert wird wie folgt berechnet: 50.000 × 1,5 = 75.000 RU/s.

Ein Reservierungserwerb von 100.000 RU/s würde die 75.000 RU/s in der Region „Australien, Mitte 2“ ausgleichen. 25.000 RU/s blieben für die Region „Frankreich, Süden“ übrig. Mit den restlichen 25.000 RU/s wird ein Reservierungsrabatt von 15.384 RU/s auf die Region „Frankreich, Süden“ angewendet. Dieser Rabattwert wird wie folgt berechnet: 25.000 / 1,625 = 15.384 RU/s. Die restlichen 34.616 RU/s in der Region „Frankreich, Süden“ werden mit den üblichen Tarifen der nutzungsbasierten Bezahlung berechnet.

Vom Azure-Abrechnungssystem wird der Abrechnungsvorteil für Reservierungen der Instanz zugewiesen, die als erste verarbeitet wird und der Reservierungskonfiguration entspricht, in diesem Fall beispielsweise der Region „Australien, Mitte 2“.

Wenn Sie grundlegende Informationen wünschen und die Anwendung Ihrer Azure-Reservierungen in Abrechnungsnutzungsberichten anzeigen möchten, lesen Sie [Grundlegendes zur Nutzung von Azure-Reservierungen für den Konzernbeitritt](understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Wenden Sie sich an uns.

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure-Reservierungen finden Sie in den folgenden Artikeln:

* [Was sind Azure-Reservierungen?](save-compute-costs-reservations.md)  
* [Vorauszahlen für Azure Cosmos DB-Ressourcen mit reservierter Azure Cosmos DB-Kapazität](../../cosmos-db/cosmos-db-reserved-capacity.md)  
* [Vorauszahlen von SQL-Datenbank-Computeressourcen mit reservierter Azure SQL-Datenbank-Kapazität](../../azure-sql/database/reserved-capacity-overview.md)  
* [Verwalten von Azure-Reservierungen](manage-reserved-vm-instance.md)  
* [Grundlegendes zur Nutzung von Azure-Reservierungen für das Abonnement mit nutzungsbasierter Bezahlung](understand-reserved-instance-usage.md)  
* [Grundlegendes zur Nutzung von Azure-Reservierungen für den Konzernbeitritt](understand-reserved-instance-usage-ea.md)
* [Grundlegendes zur Verwendung von Azure-Reservierungen für CSP-Abonnements](/partner-center/azure-reservations)