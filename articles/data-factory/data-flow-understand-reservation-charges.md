---
title: Grundlegendes zu Rabatten für Reservierungen für Azure Data Factory-Datenflüsse | Microsoft-Dokumentation
description: Erfahren Sie, wie ein Reservierungsrabatt auf ausgeführte ADF-Datenflüsse angewendet wird. Der Rabatt wird auf Stundenbasis auf diese Datenflüsse angewendet.
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: makromer
ms.openlocfilehash: 3b97f09c2b6f4c5f5352bbaa081e0bcae900ebd5
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99808022"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-data-factory-data-flows"></a>Anwendung eines Reservierungsrabatts auf Azure Data Factory-Datenflüsse

Nachdem Sie die reservierte ADF-Datenflusskapazität erworben haben, wird der Reservierungsrabatt automatisch mithilfe einer Azure Integration Runtime-Instanz auf Datenflüsse angewendet, die mit dem Computetyp und der Anzahl von Kernen der Reservierung identisch sind.

## <a name="how-reservation-discount-is-applied"></a>Wie der Reservierungsrabatt angewendet wird

Reservierungsrabatte funktionieren nach dem Prinzip „*use-it-or-lose-it*“. Das heißt, wenn Sie für eine Stunde nicht über die entsprechenden Azure Integration-Ressourcen verfügen, verlieren Sie eine Reservierungsmenge für diese Stunde. Sie können ungenutzte reservierte Stunden nicht übertragen.

Wenn Sie die Verwendung der Integration Runtime-Instanz für Datenflüsse beenden, wird der Reservierungsrabatt automatisch auf eine andere entsprechende Ressource im angegebenen Umfang angewandt. Wenn keine übereinstimmenden Ressourcen im angegebenen Reservierungsumfang gefunden werden, gehen die reservierten Stunden *verloren*.

## <a name="discount-applied-to-adf-data-flows"></a>Auf ADF-Datenflüsse angewendeter Rabatt

Der Rabatt auf die reservierte ADF-Datenflusskapazität wird auf Stundenbasis für die Ausführung von Integration Runtime-Instanzen verwendet. Die Reservierung, die Sie kaufen, ist auf den Computetyp abgestimmt, der von den Integrations Runtimes für Ihre Datenflüsse verwendet wird. Bei Datenflüssen, die nicht über die gesamte Stunde hinweg ausgeführt werden, wird die Reservierung automatisch auf andere Datenflüsse angewendet, die den Reservierungsattributen entsprechen. Der Rabatt kann auch für parallel ausgeführte Datenflüsse gelten. Wenn die den Reservierungsattributen entsprechenden Datenflüsse nicht über die gesamte Stunde hinweg ausgeführt werden, profitieren Sie nicht im vollen Umfang vom Reservierungsrabatt für diese Stunde.

Die folgenden Beispiele zeigen, wie der Rabatt für reservierte ADF-Datenflusskapazitäten in Abhängigkeit von der Anzahl der Kerne, die Sie erworben haben, und der Dauer ihrer Ausführung angewendet wird.

- Szenario 1: Sie kaufen eine ADF-Datenflussreservierung für 1 Stunde mit 80 arbeitsspeicheroptimierten Computekernen, indem Sie 80 als Menge für den arbeitsspeicheroptimierten Computetyp eingeben. Sie führen für eine Stunde einen Datenfluss mit einer Azure Integration Runtime-Instanz aus, die auf 144 Kerne von arbeitsspeicheroptimiertem Compute festgelegt ist. Ihnen wird der Preis für die nutzungsbasierte Bezahlung für die Datenflussnutzung von 64 Kernen für eine Stunde in Rechnung gestellt. Sie erhalten den Reservierungsrabatt für die einstündige Nutzung für 80 Kerne von arbeitsspeicheroptimiertem Compute.
- Szenario 2: Sie kaufen eine ADF-Datenflussreservierung für 1 Stunde mit 32 universellen Computekernen, indem Sie 32 als Menge für den universellen Computetyp eingeben. Sie debuggen ihre Datenflüsse eine Stunde lang mit Azure Integration Runtime mit 32 Kernen von Compute für allgemeine Zwecke. Sie erhalten den Reservierungsrabatt für die gesamte Nutzungsstunde.

Wenn Sie grundlegende Informationen wünschen und die Anwendung Ihrer Azure-Reservierungen in Abrechnungsnutzungsberichten anzeigen möchten, lesen Sie [Grundlegendes zur Nutzung von Azure-Reservierungen](https://docs.microsoft.com/azure/cost-management-billing/reservations/understand-reserved-instance-usage-ea).

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Kontakt

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure-Reservierungen finden Sie in folgendem Artikel:

- [Was sind Azure-Reservierungen?](https://docs.microsoft.com/azure/cost-management-billing/reservations/save-compute-costs-reservations)
