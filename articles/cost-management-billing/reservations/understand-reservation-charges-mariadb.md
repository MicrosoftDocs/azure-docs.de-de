---
title: 'Grundlegendes zum Reservierungsrabatt: Azure Database for MariaDB'
description: Hier erfahren Sie, wie ein Reservierungsrabatt auf Azure Database for MariaDB angewendet wird.
author: mksuni
ms.author: sumuth
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 2197f3ec9907aa9f1afde6272dd2e8a807465476
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93240391"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-database-for-mariadb"></a>Anwendung eines Reservierungsrabatts auf Azure Database for MariaDB

Nachdem Sie eine reservierte Azure Database for MariaDB-Kapazität erworben haben, wird der Reservierungsrabatt automatisch auf MariaDB-Server angewendet, die den Attributen und der Menge der Reservierung entsprechen. Eine Reservierung deckt nur die Computekosten Ihrer Azure Database for MariaDB-Instanz ab. Kosten für Speicher und Netzwerk werden zum regulären Satz abgerechnet.

## <a name="how-reservation-discount-is-applied"></a>Wie der Reservierungsrabatt angewendet wird

Nicht in Anspruch genommener Reservierungsrabatt **geht verloren**. Das heißt, wenn Sie für eine Stunde nicht über die entsprechenden Ressourcen verfügen, verlieren Sie eine Reservierungsmenge für diese Stunde. Sie können ungenutzte reservierte Stunden nicht übertragen.

Wenn Sie eine Ressource beenden, wird der Reservierungsrabatt automatisch auf eine andere entsprechende Ressource im angegebenen Reservierungsumfang angewandt. Werden im angegebenen Reservierungsumfang keine entsprechenden Ressourcen gefunden, gehen die reservierten Stunden verloren.

## <a name="discount-applied-to-azure-database-for-mariadb"></a>Auf Azure Database for MariaDB angewendeter Rabatt

Der Rabatt für reservierte Azure Database for MariaDB-Kapazität wird auf Stundenbasis auf die Ausführung Ihrer MariaDB-Server angewendet. Die erworbene Reservierung wird mit der Computenutzung abgeglichen, die von den ausgeführten Azure Database for MariaDB-Servern ausgegeben wird. Bei MariaDB-Servern, die nicht über die gesamte Stunde hinweg ausgeführt werden, wird die Reservierung automatisch auf andere Azure Database for MariaDB-Instanzen angewendet, die den Reservierungsattributen entsprechen. Der Rabatt kann auf gleichzeitig ausgeführte Azure Database for MariaDB-Server angewendet werden. Wenn Sie über keinen MariaDB-Server verfügen, der über die gesamte Stunde hinweg ausgeführt wird und den Reservierungsattributen entspricht, profitieren Sie nicht in vollem Umfang vom Reservierungsrabatt für diese Stunde.

Die folgenden Beispiele veranschaulichen, wie der Rabatt für reservierte Azure Database for MariaDB-Kapazität angewendet wird – abhängig davon, wie viele Kerne Sie erworben haben und wann sie ausgeführt werden.

**Beispiel 1** : Sie erwerben reservierte Azure Database for MariaDB-Kapazität für acht V-Kerne. Wenn Sie einen Azure Database for MariaDB-Server mit 16 V-Kernen ausführen, der den restlichen Attributen der Reservierung entspricht, werden Ihnen acht V-Kerne der Computenutzung Ihres MariaDB-Servers zum Preis für die nutzungsbasierte Bezahlung in Rechnung gestellt, und Sie erhalten den Reservierungsrabatt für eine Stunde der Computenutzung von acht V-Kernen des MariaDB-Servers.

Bei den übrigen Beispielen wird davon ausgegangen, dass die reservierte Azure Database for MariaDB-Kapazität, die Sie erwerben, für eine Azure Database for MariaDB-Instanz mit 16 V-Kernen gilt und die restlichen Reservierungsattribute den ausgeführten MariaDB-Servern entsprechen.

* **Beispiel 2:** Sie führen eine Stunde lang zwei Azure Database for MariaDB-Server mit jeweils acht V-Kernen aus. Der Reservierungsrabatt für 16 V-Kerne wird auf die Computenutzung beider Azure Database for MariaDB-Server mit acht V-Kernen angewendet.

* **Beispiel 3:** Sie führen einen Azure Database for MariaDB-Server mit 16 V-Kernen von 13 Uhr bis 13:30 Uhr aus. Sie führen einen weiteren Azure Database for MariaDB-Server mit 16 V-Kernen von 13:30 Uhr bis 14 Uhr aus. Beide Instanzen sind durch den Reservierungsrabatt abgedeckt.

* **Beispiel 4:** Sie führen einen Azure Database for MariaDB-Server mit 16 V-Kernen von 13 Uhr bis 13:45 Uhr aus. Sie führen einen weiteren Azure Database for MariaDB-Server mit 16 V-Kernen von 13:30 Uhr bis 14 Uhr aus. Die zusätzlichen 15 Minuten werden Ihnen zu den Preisen der nutzungsbasierten Bezahlung in Rechnung gestellt. Für den restlichen Zeitraum wird der Reservierungsrabatt auf die Computenutzung angewendet.

Wenn Sie grundlegende Informationen wünschen und die Anwendung Ihrer Azure-Reservierungen in Abrechnungsnutzungsberichten anzeigen möchten, lesen Sie [Grundlegendes zur Nutzung von Azure-Reservierungen](./understand-reserved-instance-usage-ea.md).

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).