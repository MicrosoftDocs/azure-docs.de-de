---
title: Vorauszahlung für Compute mit reservierter Kapazität (Azure Database for MariaDB)
description: Vorauszahlung für Azure Database for MariaDB-Computeressourcen mit reservierter Kapazität
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: c28abc882635f7cd0815952fe0ae183bef7138d2
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93241615"
---
# <a name="prepay-for-azure-database-for-mariadb-compute-resources-with-reserved-capacity"></a>Vorauszahlung für Azure Database for MariaDB-Computeressourcen mit reservierter Kapazität

Mit Azure Database for MariaDB können Sie jetzt Geld im Vergleich zur nutzungsbasierten Bezahlung sparen, indem Sie Computeressourcen im Voraus bezahlen. Mit reservierten Azure Database for MariaDB-Kapazitäten leisten Sie eine Vorauszahlung für MariaDB-Server für einen Zeitraum von einem Jahr oder drei Jahren und erhalten dafür einen immensen Rabatt auf Computekosten. Um reservierte Azure Database for MariaDB-Kapazität zu erwerben, müssen Sie die Azure-Region, den Bereitstellungstyp, die Leistungsstufe und die Laufzeit angeben. </br>

Sie müssen die Reservierung nicht bestimmten Azure Database for MariaDB-Servern zuweisen. Azure Database for MariaDB-Server, die bereits ausgeführt oder neu bereitgestellt werden, profitieren automatisch von dem Reservierungspreisvorteil. Beim Kauf einer Reservierung bezahlen Sie im Voraus die Computekosten für einen Zeitraum von einem oder drei Jahren. Sobald Sie eine Reservierung gekauft haben, werden die Azure Database for MariaDB-Computegebühren, die den Reservierungsattributen entsprechen, nicht mehr zu den Preisen der nutzungsbasierten Bezahlung abgerechnet. Eine Reservierung deckt nicht die Software-, Netzwerk- oder Speichergebühren für den MariaDB-Datenbankserver ab. Nach Ablauf der Reservierungslaufzeit erlischt der Abrechnungsvorteil, und die Azure Database for MariaDB-Server werden mit den Preisen für die nutzungsbasierte Bezahlung in Rechnung gestellt. Reservierungen werden nicht automatisch verlängert. Weitere Informationen zu den Preisen finden Sie unter [Kapazitätsreservierungsangebot für Azure Database for MariaDB](https://azure.microsoft.com/pricing/details/mariadb/). </br>

Sie können die reservierte Azure Database for MariaDB-Kapazität über das [Azure-Portal](https://portal.azure.com/) erwerben. Bezahlen Sie die Reservierung [im Voraus oder monatlich](../cost-management-billing/reservations/prepare-buy-reservation.md). So erwerben Sie reservierte Kapazität:

* Ihnen muss die Besitzerrolle für mindestens ein Enterprise-Abonnement oder ein individuelles Abonnement mit nutzungsbasierter Bezahlung zugeordnet sein.
* Bei Enterprise-Abonnements muss im [EA-Portal](https://ea.azure.com/) die Option **Reservierte Instanzen hinzufügen** aktiviert werden. Wenn diese Einstellung deaktiviert ist, müssen Sie ein EA-Administrator für das Abonnement sein.
* Für das Cloud Solution Provider-Programm (CSP) können nur die Administrator- oder Vertriebs-Agents reservierte Azure Database for MariaDB-Kapazität kaufen. </br>

Einzelheiten zur Berechnung der Reservierung von Kapazitäten für Unternehmenskunden und Kunden mit nutzungsbasierter Bezahlung finden Sie unter [Grundlegendes zur Nutzung von Azure-Reservierungen für den Konzernbeitritt](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) und [Grundlegendes zur Nutzung von Azure-Reservierungen für das Abonnement mit nutzungsbasierter Bezahlung](../cost-management-billing/reservations/understand-reserved-instance-usage.md).


## <a name="determine-the-right-server-size-before-purchase"></a>Bestimmen der richtigen Servergröße vor dem Kauf

Die Größe der Reservierung sollte auf der Gesamtmenge der Computeressourcen basieren, die von den bereits vorhandenen oder in Kürze zu erstellenden Datenbankinstanzen innerhalb einer bestimmten Region genutzt werden und die gleiche Leistungsstufe und Hardwaregeneration verwenden.</br>

Nehmen Sie beispielsweise an, Sie führen eine universelle Gen5-MariaDB-Datenbank mit 32 virtuellen Kernen sowie zwei arbeitsspeicheroptimierte Gen5-MariaDB-Datenbank mit 16 virtuellen Kernen aus. Außerdem möchten Sie innerhalb des nächsten Monats einen weiteren universellen Gen5-Datenbankserver mit 32 virtuellen Kernen sowie einen arbeitsspeicheroptimierten Gen5-Datenbankserver mit 16 virtuellen Kernen bereitstellen. Sie wissen außerdem, dass Sie diese Ressourcen mindestens 1 Jahr benötigen. In diesem Fall sollten Sie Folgendes erwerben: eine 1-Jahres-Reservierung für eine universelle Gen5-Einzeldatenbank mit 64 virtuellen Kernen (2 × 32) sowie eine 1-Jahres-Reservierung für eine arbeitsspeicheroptimierte Gen5-Einzeldatenbank mit 48 virtuellen Kernen (2 × 16 + 16).


## <a name="buy-azure-database-for-mariadb-reserved-capacity"></a>Reservierte Kapazität für Azure Database for MariaDB kaufen

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Klicken Sie auf **Alle Dienste** > **Reservierungen**.
3.  Wählen Sie **Hinzufügen** und dann im Bereich „Reservierungen erwerben“ **Azure Database for MariaDB** aus, um eine neue Reservierung für Ihre MariaDB-Datenbanken zu erwerben.
4.  Füllen Sie die Pflichtfelder aus. Bei vorhandenen oder neuen Datenbanken, die den von Ihnen ausgewählten Attributen entsprechen, wird der Rabatt auf reservierte Kapazitäten angewendet. Die tatsächliche Anzahl der Azure Database for MariaDB-Server, die den Rabatt erhalten, hängt vom ausgewählten Bereich und der ausgewählten Menge ab.


![Übersicht über Reservierungspreise](media/concepts-reserved-pricing/mariadb-reserved-price.png)


In der folgenden Tabelle werden die erforderlichen Felder beschrieben.

| Feld | Beschreibung |
| :------------ | :------- |
| Subscription   | Das Abonnement, das für die Zahlung der Reservierung von Azure Database for MariaDB-Kapazitäten verwendet wird. Die Zahlungsmethode für das Abonnement wird mit Vorauszahlungen für die Reservierung von Azure Database for MariaDB-Kapazitäten belastet. Der Abonnementtyp muss „Enterprise Agreement“ (Angebotsnummern: MS-AZR-0017P oder MS-AZR-0148P) oder eine einzelne Vereinbarung mit Preisen für nutzungsbasierte Bezahlung (Angebotsnummern: MS-AZR-0003P oder MS-AZR-0023P) sein. Bei einem Enterprise-Abonnement werden die Gebühren vom Verpflichtungsguthaben der Reservierung abgezogen oder als Überschreitung belastet. Bei einem individuellen Abonnement mit Preisen für nutzungsbasierte Zahlung wird die Kreditkarte mit den Gebühren belastet, oder die Gebühren werden für Zahlung auf Rechnung für das Abonnement in Rechnung gestellt.
| `Scope` | Die Reservierung virtueller Kerne kann ein Abonnement oder mehrere Abonnements (freigegebener Bereich) umfassen. Optionen: </br></br> **Gemeinsam** : Der Rabatt auf die Reservierung virtueller Kerne wird auf Azure Database for MariaDB-Server angewendet, die in einem beliebigen Abonnement innerhalb des Abrechnungskontexts ausgeführt werden. Für Enterprise-Kunden stellt der freigegebene Bereich die Registrierung dar und umfasst alle Abonnements in der Registrierung. Für Kunden mit nutzungsbasierter Zahlung stellt der freigegebene Bereich alle Abonnements mit nutzungsbasierter Zahlung dar, die vom Kontoadministrator erstellt wurden.</br></br> **Einzelabonnement** : Der Rabatt auf die Reservierung virtueller Kerne wird auf Azure Database for MariaDB-Server in diesem Abonnement angewendet. </br></br> **Einzelne Ressourcengruppe** : Der Reservierungsrabatt wird auf Azure Database for MariaDB-Server im ausgewählten Abonnement und die ausgewählte Ressourcengruppe in diesem Abonnement angewendet.
| Region | Die Azure-Region, die durch die Reservierung von Azure Database for MariaDB-Kapazitäten abgedeckt wird.
| Bereitstellungstyp | Der Azure Database for MariaDB-Ressourcentyp, für den Sie die Reservierung erwerben möchten.
| Leistungsstufe | Die Dienstebene für die Azure Database for MariaDB-Server.
| Begriff | Ein Jahr
| Menge | Die Menge an Computeressourcen, die im Rahmen der Azure Database for MariaDB-Kapazitätsreservierung erworben werden. Die Menge entspricht einer Anzahl von virtuellen Kernen in der ausgewählten Azure-Region und der Leistungsstufe, die reserviert werden und den Abrechnungsrabatt erhalten. Wenn Sie beispielsweise Azure Database for MariaDB-Server mit der gesamten Computekapazität von 16 virtuellen Gen5-Kernen in der Region „USA, Osten“ ausführen oder deren Ausführung beabsichtigen, geben Sie als Menge „16“ an, um den Vorteil für alle Server zu maximieren.

## <a name="cancel-exchange-or-refund-reservations"></a>Stornieren, Umtauschen oder Rückerstatten von Reservierungen

Reservierungen können unter bestimmten Einschränkungen storniert, umgetauscht oder rückerstattet werden. Weitere Informationen finden Sie unter [Self-Service-Umtausch und -Rückerstattungen für Azure-Reservierungen](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="vcore-size-flexibility"></a>Flexibilität der V-Kern-Größe

Die Flexibilität der V-Kern-Größe ermöglicht ein Hoch- oder Herunterskalieren innerhalb einer Leistungsstufe und Region, ohne den Vorteil reservierter Kapazität einzubüßen. 

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Kontakt

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nächste Schritte

Der Rabatt auf die Reservierung virtueller Kerne wird automatisch auf die Anzahl der Azure Database for MariaDB-Server angewendet, die dem Reservierungsumfang und den Attributen der Azure Database for MariaDB-Kapazitätsreservierung entsprechen. Sie können den Umfang der Azure Database for MariaDB-Kapazitätsreservierung über das Azure-Portal, PowerShell, die CLI oder die API aktualisieren. </br></br>
Informationen zum Verwalten der reservierten Azure Database for MariaDB-Kapazität finden Sie im Abschnitt zur Verwaltung der reservierten Azure Database for MariaDB-Kapazität.

Weitere Informationen zu Azure-Reservierungen finden Sie in den folgenden Artikeln:

* [Was sind Azure-Reservierungen](../cost-management-billing/reservations/save-compute-costs-reservations.md)?
* [Verwalten von Azure-Reservierungen](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
* [Grundlegendes zum Rabatt für Azure-Reservierungen](../cost-management-billing/reservations/understand-reservation-charges.md)
* [Grundlegendes zur Nutzung von Azure-Reservierungen für das Abonnement mit nutzungsbasierter Bezahlung](../cost-management-billing/reservations/understand-reservation-charges-mariadb.md)
* [Grundlegendes zur Nutzung von Azure-Reservierungen für den Konzernbeitritt](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
* [Verkaufen Microsoft Azure Reserved Instances](/partner-center/azure-reservations)