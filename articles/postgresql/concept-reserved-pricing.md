---
title: Preise für reserviertes Compute – Azure Database for PostgreSQL (Einzelserver)
description: Vorauszahlung für Azure Database for PostgreSQL-Computeressourcen mit reservierter Kapazität
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: dcc6cc40c936876c856326e330b67ef465f90cde
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93240442"
---
# <a name="prepay-for-azure-database-for-postgresql---single-server-compute-resources-with-reserved-capacity"></a>Vorauszahlung für Azure Database for PostgreSQL – Einzelserver-Computeressourcen mit reservierter Kapazität

Mit Azure Database for PostgreSQL können Sie jetzt Geld im Vergleich zur nutzungsbasierten Bezahlung sparen, indem Sie Computeressourcen im Voraus bezahlen. Mit reservierten Azure Database for PostgreSQL-Kapazitäten leisten Sie eine Vorauszahlung für PostgreSQL-Server für einen Zeitraum von einem oder drei Jahren und erhalten dafür einen immensen Rabatt auf die Computekosten. Um reservierte Azure Database for PostgreSQL-Kapazität zu erwerben, müssen Sie die Azure-Region, den Bereitstellungstyp, die Leistungsstufe und die Laufzeit angeben. </br>

Sie müssen die Reservierung nicht bestimmten Azure Database for PostgreSQL-Servern zuweisen. Azure Database for PostgreSQL-Server, die bereits ausgeführt oder neu bereitgestellt werden, profitieren automatisch von dem Reservierungspreisvorteil. Beim Kauf einer Reservierung bezahlen Sie im Voraus die Computekosten für einen Zeitraum von einem oder drei Jahren. Sobald Sie eine Reservierung gekauft haben, werden die Azure Database for PostgreSQL-Computegebühren, die den Reservierungsattributen entsprechen, nicht mehr zu den Preisen der nutzungsbasierten Bezahlung abgerechnet. Eine Reservierung deckt nicht die Software-, Netzwerk- oder Speichergebühren für den PostgreSQL-Datenbankserver ab. Nach Ablauf der Reservierungslaufzeit erlischt der Abrechnungsvorteil, und die Azure Database for PostgreSQL-Server werden mit den Preisen für die nutzungsbasierte Bezahlung in Rechnung gestellt. Reservierungen werden nicht automatisch verlängert. Weitere Informationen zu den Preisen finden Sie unter [Azure Database for PostgreSQL – Preise](https://azure.microsoft.com/pricing/details/postgresql/). </br>

> [!IMPORTANT]
> Die Preise für reservierte Kapazitäten sind für Azure Database for PostgreSQL in den Bereitstellungsoptionen [Einzelserver](./overview.md#azure-database-for-postgresql---single-server) und [Hyperscale Citus](./overview.md#azure-database-for-postgresql--hyperscale-citus) verfügbar. Informationen zu den RI-Preisen für Hyperscale (Citus) finden Sie auf [dieser Seite](concepts-hyperscale-reserved-pricing.md).

Sie können die reservierte Azure Database for PostgreSQL-Kapazität über das [Azure-Portal](https://portal.azure.com/) erwerben. Bezahlen Sie die Reservierung [im Voraus oder monatlich](../cost-management-billing/reservations/prepare-buy-reservation.md). So erwerben Sie reservierte Kapazität:

* Ihnen muss die Besitzerrolle für mindestens ein Enterprise-Abonnement oder ein individuelles Abonnement mit nutzungsbasierter Bezahlung zugeordnet sein.
* Bei Enterprise-Abonnements muss im [EA-Portal](https://ea.azure.com/) die Option **Reservierte Instanzen hinzufügen** aktiviert werden. Wenn diese Einstellung deaktiviert ist, müssen Sie ein EA-Administrator für das Abonnement sein.
* Für das Cloud Solution Provider-Programm (CSP) können nur die Administrator- oder Vertriebs-Agents reservierte Azure Database for PostgreSQL-Kapazität kaufen. </br>

Einzelheiten zur Berechnung der Reservierung von Kapazitäten für Unternehmenskunden und Kunden mit nutzungsbasierter Bezahlung finden Sie unter [Grundlegendes zur Nutzung von Azure-Reservierungen für den Konzernbeitritt](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) und [Grundlegendes zur Nutzung von Azure-Reservierungen für das Abonnement mit nutzungsbasierter Bezahlung](../cost-management-billing/reservations/understand-reserved-instance-usage.md).


## <a name="determine-the-right-server-size-before-purchase"></a>Bestimmen der richtigen Servergröße vor dem Kauf

Die Größe der Reservierung muss auf der Gesamtmenge der Computeressourcen basieren, die von den bereits vorhandenen oder in Kürze bereitzustellenden Servern innerhalb einer bestimmten Region genutzt werden und die gleiche Leistungsstufe und Hardwaregeneration verwenden.</br>

Nehmen Sie beispielsweise an, Sie führen eine universelle Gen5-PostgreSQL-Datenbank mit 32 virtuellen Kernen sowie zwei arbeitsspeicheroptimierte Gen5-PostgreSQL-Datenbanken mit 16 virtuellen Kernen aus. Außerdem möchten Sie innerhalb des nächsten Monats einen weiteren universellen Gen5-Datenbankserver mit 32 virtuellen Kernen sowie einen arbeitsspeicheroptimierten Gen5-Datenbankserver mit 16 virtuellen Kernen bereitstellen. Sie wissen außerdem, dass Sie diese Ressourcen mindestens ein Jahr benötigen. In diesem Fall sollten Sie Folgendes erwerben: eine einjährige Reservierung für eine universelle Gen5-Einzeldatenbank mit 64 virtuellen Kernen (2 × 32) sowie eine einjährige Reservierung für eine arbeitsspeicheroptimierte Gen5-Einzeldatenbank mit 48 virtuellen Kernen (2 × 16 + 16).


## <a name="buy-azure-database-for-postgresql-reserved-capacity"></a>Kaufen von reservierter Kapazität für Azure Database for PostgreSQL

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Klicken Sie auf **Alle Dienste** > **Reservierungen**.
3. Wählen Sie **Hinzufügen** und dann im Bereich „Reservierungen erwerben“ die Option **Azure Database for PostgreSQL** aus, um eine neue Reservierung für Ihre PostgreSQL-Datenbanken zu erwerben.
4. Füllen Sie die erforderlichen Felder aus. Bei vorhandenen oder neuen Datenbanken, die den von Ihnen ausgewählten Attributen entsprechen, wird der Rabatt auf reservierte Kapazitäten angewendet. Die tatsächliche Anzahl der Azure Database for PostgreSQL-Server, die den Rabatt erhalten, hängt vom ausgewählten Bereich und der ausgewählten Menge ab.


:::image type="content" source="media/concepts-reserved-pricing/postgresql-reserved-price.png" alt-text="Übersicht über Reservierungspreise":::


In der folgenden Tabelle werden die erforderlichen Felder beschrieben.

| Feld | BESCHREIBUNG |
| :------------ | :------- |
| Subscription   | Das Abonnement, das für die Zahlung der Reservierung von Azure Database for PostgreSQL-Kapazitäten verwendet wird. Die Zahlungsmethode für das Abonnement wird mit Vorauszahlungen für die Reservierung von Azure Database for PostgreSQL-Kapazitäten belastet. Der Abonnementtyp muss „Enterprise Agreement“ (Angebotsnummern: MS-AZR-0017P oder MS-AZR-0148P) oder eine einzelne Vereinbarung mit Preisen für nutzungsbasierte Bezahlung (Angebotsnummern: MS-AZR-0003P oder MS-AZR-0023P) sein. Bei einem Enterprise-Abonnement werden die Gebühren vom Verpflichtungsguthaben der Reservierung abgezogen oder als Überschreitung belastet. Bei einem individuellen Abonnement mit Preisen für nutzungsbasierte Zahlung wird die Kreditkarte mit den Gebühren belastet, oder die Gebühren werden für Zahlung auf Rechnung für das Abonnement in Rechnung gestellt.
| `Scope` | Der Umfang der Reservierung virtueller Kerne kann ein Abonnement oder mehrere Abonnements (freigegebener Bereich) umfassen. Optionen: </br></br> **Gemeinsam** : Der Rabatt auf die Reservierung virtueller Kerne wird auf Azure Database for PostgreSQL-Server angewendet, die in einem beliebigen Abonnement innerhalb des Abrechnungskontexts ausgeführt werden. Für Enterprise-Kunden stellt der freigegebene Bereich die Registrierung dar und umfasst alle Abonnements in der Registrierung. Für Kunden mit nutzungsbasierter Zahlung stellt der freigegebene Bereich alle Abonnements mit nutzungsbasierter Zahlung dar, die vom Kontoadministrator erstellt wurden.</br></br> **Einzelabonnement** : Der Rabatt auf die Reservierung virtueller Kerne wird auf Azure Database for PostgreSQL-Server in diesem Abonnement angewendet. </br></br> **Einzelne Ressourcengruppe** : Der Reservierungsrabatt wird auf Azure Database for PostgreSQL-Server im ausgewählten Abonnement und die ausgewählte Ressourcengruppe in diesem Abonnement angewendet.
| Region | Die Azure-Region, die durch die Reservierung von Azure Database for PostgreSQL-Kapazitäten abgedeckt wird.
| Bereitstellungstyp | Der Azure Database for PostgreSQL-Ressourcentyp, für den Sie die Reservierung erwerben möchten.
| Leistungsstufe | Die Dienstebene für die Azure Database for PostgreSQL-Server.
| Begriff | Ein Jahr
| Menge | Die Menge an Computeressourcen, die im Rahmen der Azure Database for PostgreSQL-Kapazitätsreservierung erworben werden. Die Menge entspricht einer Anzahl von virtuellen Kernen in der ausgewählten Azure-Region und der Leistungsstufe, die reserviert werden und den Abrechnungsrabatt erhalten. Wenn Sie beispielsweise Azure Database for PostgreSQL-Server mit der Gesamtcomputekapazität von 16 virtuellen Gen5-Kernen in der Region „USA, Osten“ ausführen oder deren Ausführung beabsichtigen, geben Sie als Menge „16“ an, um den Vorteil für alle Server zu maximieren.

## <a name="cancel-exchange-or-refund-reservations"></a>Stornieren, Umtauschen oder Rückerstatten von Reservierungen

Reservierungen können unter bestimmten Einschränkungen storniert, umgetauscht oder rückerstattet werden. Weitere Informationen finden Sie unter [Self-Service-Umtausch und -Rückerstattungen für Azure-Reservierungen](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="vcore-size-flexibility"></a>Flexibilität der V-Kern-Größe

Die Flexibilität der V-Kern-Größe ermöglicht ein Hoch- oder Herunterskalieren innerhalb einer Leistungsstufe und Region, ohne den Vorteil reservierter Kapazität einzubüßen. 

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Kontakt

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nächste Schritte

Der Rabatt auf die Reservierung virtueller Kerne wird automatisch auf die Anzahl der Azure Database for PostgreSQL-Server angewendet, die dem Reservierungsumfang und den Attributen der Azure Database for PostgreSQL-Kapazitätsreservierung entsprechen. Sie können den Umfang der Azure Database for PostgreSQL-Kapazitätsreservierung über das Azure-Portal, PowerShell, die CLI oder die API aktualisieren.

Weitere Informationen zu Azure-Reservierungen finden Sie in den folgenden Artikeln:

* [Was sind Azure-Reservierungen](../cost-management-billing/reservations/save-compute-costs-reservations.md)?
* [Verwalten von Azure-Reservierungen](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
* [Grundlegendes zum Rabatt für Azure-Reservierungen](../cost-management-billing/reservations/understand-reservation-charges.md)
* [Grundlegendes zur Nutzung von Azure-Reservierungen für das Abonnement mit nutzungsbasierter Bezahlung](../cost-management-billing/reservations/understand-reservation-charges-postgresql.md)
* [Grundlegendes zur Nutzung von Azure-Reservierungen für den Konzernbeitritt](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
* [Verkaufen Microsoft Azure Reserved Instances](/partner-center/azure-reservations)