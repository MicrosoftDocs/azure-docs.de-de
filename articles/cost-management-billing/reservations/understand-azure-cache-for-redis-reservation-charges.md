---
title: Grundlegendes zur Anwendung des Reservierungsrabatts auf Azure Cache for Redis | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Reservierungsrabatt auf Azure Cache for Redis-Instanzen angewendet wird.
author: yegu-ms
manager: maiye
ms.service: cache
ms.topic: conceptual
ms.date: 01/22/2020
ms.author: yegu
ms.openlocfilehash: 5f9e0a18db0920acd35ebd7b133ed3fe5d0eaee9
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352948"
---
# <a name="how-the-reservation-discount-is-applied-to-azure-cache-for-redis"></a>Anwendung des Reservierungsrabatts auf Azure Cache for Redis

Nachdem Sie eine reservierte Azure Cache for Redis-Kapazität erworben haben, wird der Reservierungsrabatt automatisch auf Cache-Instanzen angewendet, die den Attributen und der Menge der Reservierung entsprechen. Eine Reservierung deckt nur die Computekosten Ihrer Azure Cache for Redis-Instanz ab. Kosten für Speicher und Netzwerk werden zum regulären Satz abgerechnet. Reservierte Kapazität steht nur für Caches im [Premium-Tarif](../../azure-cache-for-redis/quickstart-create-redis.md) zur Verfügung.

## <a name="how-reservation-discount-is-applied"></a>Wie der Reservierungsrabatt angewendet wird

Nicht in Anspruch genommener Reservierungsrabatt **geht verloren**. Das heißt, wenn Sie für eine Stunde nicht über die entsprechenden Ressourcen verfügen, verlieren Sie eine Reservierungsmenge für diese Stunde. Sie können ungenutzte reservierte Stunden nicht übertragen.

Wenn Sie eine Ressource beenden, wird der Reservierungsrabatt automatisch auf eine andere entsprechende Ressource im angegebenen Reservierungsumfang angewandt. Werden im angegebenen Reservierungsumfang keine entsprechenden Ressourcen gefunden, gehen die reservierten Stunden verloren.

## <a name="discount-applied-to-azure-cache-for-redis"></a>Auf Azure Cache for Redis angewendeter Rabatt

Der Rabatt für reservierter Azure Cache for Redis-Kapazität wird auf Stundenbasis auf Ihre Cache-Instanzen angewendet. Die von Ihnen erworbene Reservierung wird mit der Computenutzung abgeglichen, die von den aktiven Cache-Instanzen ausgegeben wird. Bei Cache-Instanzen, die nicht über die gesamte Stunde hinweg ausgeführt werden, wird die Reservierung automatisch auf andere Cache-Instanzen angewendet, die den Reservierungsattributen entsprechen. Der Rabatt kann auf Cache-Instanzen angewendet werden, die gleichzeitig ausgeführt werden. Wenn Sie nicht über Cache-Instanzen verfügen, die eine ganze Stunde ausgeführt werden und den Reservierungsattributen entsprechen, profitieren Sie nicht im vollen Umfang vom Reservierungsrabatt für diese Stunde.

Die folgenden Beispiele veranschaulichen, wie der Rabatt für reservierte Azure Cache for Redis-Kapazität angewendet wird – abhängig davon, wie viele Cache-Instanzen Sie erworben haben und wann sie ausgeführt werden.

**Beispiel 1**: Sie erwerben reservierte Azure Cache for Redis-Kapazität für einen 6-GB-Cache. Wenn Sie einen 13-GB-Cache verwenden, der den restlichen Attributen der Reservierung entspricht, werden 7 GB Ihrer Azure Cache for Redis-Nutzung zum Preis für die nutzungsbasierte Bezahlung abgerechnet, und Sie erhalten den Reservierungsrabatt für eine Computenutzungsstunde des 6-GB-Caches.

Bei den übrigen Beispielen wird davon ausgegangen, dass die von Ihnen erworbene reservierte Azure Cache for Redis-Kapazität für einen 26-GB-Cache gilt und die restlichen Reservierungsattribute dem aktiven Cache entsprechen.

* **Beispiel 2:** Sie nutzen zwei 13-GB-Cache-Instanzen für eine Stunde. Der Reservierungsrabatt für 26 GB wird auf die Computenutzung beider Cache-Instanzen angewendet.

* **Beispiel 3:** Sie nutzen eine 26-GB-Cache-Instanz von 13 Uhr bis 13:30 Uhr und eine weitere 26-GB-Cache-Instanz von 13:30 Uhr bis 14 Uhr. Beide Instanzen sind durch den Reservierungsrabatt abgedeckt.

* **Beispiel 4:** Sie nutzen eine 26-GB-Cache-Instanz von 13 Uhr bis 13:45 Uhr und eine weitere 26-GB-Cache-Instanz von 13:30 Uhr bis 14 Uhr. Die zusätzlichen 15 Minuten werden Ihnen zu den Preisen der nutzungsbasierten Bezahlung in Rechnung gestellt. Für den restlichen Zeitraum wird der Reservierungsrabatt auf die Computenutzung angewendet.

Wenn Sie grundlegende Informationen wünschen und die Anwendung Ihrer Azure-Reservierungen in Abrechnungsnutzungsberichten anzeigen möchten, lesen Sie [Grundlegendes zur Nutzung von Azure-Reservierungen](./understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Kontakt
Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).