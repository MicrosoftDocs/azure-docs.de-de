---
title: Sparen von Computekosten mit reservierter Kapazität
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Erfahren Sie, wie Sie reservierte Kapazitäten für Azure SQL-Datenbank- und verwaltete SQL-Instanzen kaufen, um Computekosten einzusparen.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein
ms.date: 10/13/2020
ms.openlocfilehash: b4a8b809fc135a4af26fae10912890f2017153f4
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92780919"
---
# <a name="save-costs-for-resources-with-reserved-capacity---azure-sql-database--sql-managed-instance"></a>Sparen von Kosten für Ressourcen mit reservierter Kapazität – Azure SQL-Datenbank und SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)] 

Sparen Sie mit Azure SQL-Datenbank und SQL Managed Instance Geld im Vergleich zur nutzungsbasierten Bezahlung, indem Sie sich auf eine Reservierung für Computeressourcen festlegen. Mit reservierten Kapazitäten legen Sie sich für die Nutzung von SQL-Datenbank und/oder SQL Managed Instance für einen Zeitraum von einem oder drei Jahren fest und profitieren dafür von einem immensen Rabatt auf Computekosten. Um reservierte Kapazität zu erwerben, müssen Sie die Azure-Region, den Bereitstellungstyp, die Leistungsstufe und die Laufzeit angeben.

Die Reservierung muss keiner bestimmten Datenbank oder verwalteten Instanz zugewiesen werden. Entsprechende vorhandene Bereitstellungen, die bereits ausgeführt oder neu bereitgestellt werden, profitieren automatisch von dem Vorteil. Beim Kauf einer Reservierung zahlen Sie für einen Zeitraum von einem oder drei Jahren die für die Nutzung anfallenden Computekosten. Sobald Sie eine Reservierung erworben haben, werden die Computegebühren, die den Reservierungsattributen entsprechen, nicht mehr zu den Preisen der nutzungsbasierten Bezahlung abgerechnet. 

Eine Reservierung gilt sowohl für primäre als auch für abrechenbare sekundäre Computereplikate, deckt jedoch keine Software-, Netzwerk- oder Speichergebühren ab, die mit dem Dienst verbunden sind. Nach Ablauf der Reservierungslaufzeit erlischt der Abrechnungsvorteil, und die Datenbank bzw. die verwaltete Instanz wird mit den Preisen für die nutzungsbasierte Bezahlung in Rechnung gestellt. Reservierungen werden nicht automatisch verlängert. Weitere Informationen zu den Preisen finden Sie unter [Azure SQL-Datenbank – Preise](https://azure.microsoft.com/pricing/details/sql-database/managed/).

Reservierte Kapazität kann über das [Azure-Portal](https://portal.azure.com) erworben werden. Bezahlen Sie die Reservierung [im Voraus oder monatlich](../../cost-management-billing/reservations/prepare-buy-reservation.md). So erwerben Sie reservierte Kapazität:

- Ihnen muss die Besitzerrolle für mindestens ein Enterprise-Abonnement oder ein individuelles Abonnement mit nutzungsbasierter Bezahlung zugeordnet sein.
- Bei Enterprise-Abonnements muss im [EA-Portal](https://ea.azure.com) die Option **Reservierte Instanzen hinzufügen** aktiviert werden. Wenn diese Einstellung deaktiviert ist, müssen Sie ein EA-Administrator für das Abonnement sein. Reservierte Kapazität.

Weitere Informationen zur Berechnung der Reservierung von Kapazitäten für Unternehmenskunden und Kunden mit nutzungsbasierter Bezahlung finden Sie unter [Grundlegendes zur Nutzung von Azure-Reservierungen für die Enterprise-Registrierung](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) und [Informationen zur Azure-Reservierungsnutzung bei Ihrem einzelnen Abonnement mit nutzungsbasierter Bezahlung](../../cost-management-billing/reservations/understand-reserved-instance-usage.md).

> [!NOTE]
> Ein Erwerb einer reservierten Kapazität hat nicht zur Folge, dass bestimmte Infrastrukturressourcen (virtuelle Computer oder Knoten) für ihre Verwendung vorab zugeordnet oder reserviert werden.

## <a name="determine-correct-size-before-purchase"></a>Ermitteln der richtigen Größe vor dem Erwerb

Die Größe der Reservierung sollte auf der Gesamtmenge der Computeressourcen basieren, die von der bereits vorhandenen oder in Kürze bereitzustellenden Datenbank oder verwalteten Instanz in einer bestimmten Region genutzt werden und die gleiche Leistungsstufe und Hardwaregeneration verwenden.

Nehmen Sie beispielsweise an, Sie führen einen universellen Pool für elastische Gen5-Datenbanken mit 16 virtuellen Kernen sowie zwei unternehmenskritische Gen5-Einzeldatenbanken mit 4 virtuellen Kernen aus. Sie möchten innerhalb des nächsten Monats einen weiteren universellen Pool für elastische Gen5-Datenbanken mit 16 virtuellen Kernen sowie einen unternehmenskritischen Pool für elastische Gen5-Datenbanken mit 32 virtuellen Kernen bereitstellen. Darüber hinaus wissen Sie, dass Sie diese Ressourcen mindestens 1 Jahr benötigen. In diesem Fall sollten Sie Folgendes erwerben: eine 1-Jahres-Reservierung für Singletons oder Pools für elastische Datenbanken (Gen5) mit der SKU „Universell“ und 32 virtuellen Kernen (2 × 16) sowie eine 1-Jahres-Reservierung für Singletons/Pools für elastische Datenbanken (Gen5) mit der SKU „Unternehmenskritisch“ und 40 virtuellen Kernen (2 × 4 + 32).

## <a name="buy-reserved-capacity"></a>Erwerben von reservierter Kapazität

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Klicken Sie auf **Alle Dienste** > **Reservierungen** .
3. Wählen Sie **Hinzufügen** und dann im Bereich **Reservierungen erwerben** die Option **SQL-Datenbank** aus, um eine neue Reservierung für SQL-Datenbank zu erwerben.
4. Füllen Sie die erforderlichen Felder aus. Bei vorhandenen Datenbanken in SQL-Datenbank und SQL Managed Instance, die den von Ihnen ausgewählten Attributen entsprechen, wird der Rabatt für reservierte Kapazitäten angewendet. Die tatsächliche Anzahl von Datenbanken oder verwalteten Instanzen, die den Rabatt erhalten, hängt vom ausgewählten Bereich und der ausgewählten Menge ab.

    ![Screenshot vor der Übermittlung des Kaufs der reservierten Kapazität](./media/reserved-capacity-overview/sql-reserved-vcores-purchase.png)

    In der folgenden Tabelle werden die erforderlichen Felder beschrieben.
    
    | Feld      | Beschreibung|
    |------------|--------------|
    |Subscription|Das zum Bezahlen für die Kapazitätsreservierung verwendete Abonnement. Die Zahlungsmethode für das Abonnement wird mit Vorauszahlungen für die Reservierung belastet. Als Abonnementtyp muss „Enterprise Agreement“ (Angebotsnummer MS-AZR-0017P oder MS-AZR-0148P) oder ein Einzelvertrag mit nutzungsbasierter Bezahlung (Angebotsnummer MS-AZR-0003P oder MS-AZR-0023P) festgelegt werden. Bei einem Enterprise-Abonnement werden die Gebühren vom Verpflichtungsguthaben der Reservierung abgezogen oder als Überschreitung belastet. Bei einem individuellen Abonnement mit Preisen für nutzungsbasierte Zahlung wird die Kreditkarte mit den Gebühren belastet, oder die Gebühren werden für Zahlung auf Rechnung für das Abonnement in Rechnung gestellt.|
    |`Scope`       |Die Reservierung virtueller Kerne kann ein Abonnement oder mehrere Abonnements (freigegebener Bereich) umfassen. Optionen: <br/><br/>**Gemeinsam** : Der Rabatt auf die Reservierung virtueller Kerne wird auf die Datenbank oder verwaltete Instanz angewendet, die in einem beliebigen Abonnement innerhalb des Abrechnungskontexts ausgeführt wird. Für Enterprise-Kunden stellt der freigegebene Bereich die Registrierung dar und umfasst alle Abonnements in der Registrierung. Für Kunden mit nutzungsbasierter Zahlung stellt der freigegebene Bereich alle Abonnements mit nutzungsbasierter Zahlung dar, die vom Kontoadministrator erstellt wurden.<br/><br/>**Einzelabonnement** : Der Rabatt auf die Reservierung virtueller Kerne wird auf die Datenbank oder verwaltete Instanz in diesem Abonnement angewendet. <br/><br/>**Einzelne Ressourcengruppe** : Der Reservierungsrabatt wird auf die Datenbankinstanzen oder die verwalteten Instanzen im ausgewählten Abonnement und die ausgewählte Ressourcengruppe in diesem Abonnement angewendet.|
    |Region      |Die Azure-Region, die durch die Kapazitätsreservierung abgedeckt wird.|
    |Bereitstellungstyp|Der SQL-Ressourcentyp, für den Sie die Reservierung erwerben möchten.|
    |Leistungsstufe|Die Dienstebene für die Datenbanken oder verwalteten Instanzen. |
    |Begriff        |Ein Jahr oder drei Jahre|
    |Menge    |Die Menge an Computeressourcen, die innerhalb der Kapazitätsreservierung erworben werden. Die Menge entspricht einer Anzahl von virtuellen Kernen in der ausgewählten Azure-Region und der Leistungsstufe, die reserviert werden und den Abrechnungsrabatt erhalten. Wenn Sie beispielsweise mehrere Datenbanken mit der gesamten Computekapazität von 16 virtuellen Gen5-Kernen in der Region „USA, Osten“ ausführen oder ausführen möchten, geben Sie als Menge „16“ an, um die Vorteile für alle Datenbanken zu maximieren. |

1. Überprüfen Sie die Kosten für die Kapazitätsreservierung im Abschnitt **Kosten** .
1. Wählen Sie die Option **Kaufen** .
1. Klicken Sie auf **Diese Reservierung anzeigen** , um den Status des Einkaufs anzuzeigen.

## <a name="cancel-exchange-or-refund-reservations"></a>Stornieren, Umtauschen oder Rückerstatten von Reservierungen

Reservierungen können unter bestimmten Einschränkungen storniert, umgetauscht oder rückerstattet werden. Weitere Informationen finden Sie unter [Self-Service-Umtausch und -Rückerstattungen für Azure-Reservierungen](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="vcore-size-flexibility"></a>Flexibilität der V-Kern-Größe

Die Flexibilität der V-Kern-Größe ermöglicht ein Hoch- oder Herunterskalieren innerhalb einer Leistungsstufe und Region, ohne den Vorteil reservierter Kapazität einzubüßen. Reservierte Kapazität bietet Ihnen auch die Flexibilität, Ihre aktiven Datenbanken im Rahmen des normalen Betriebs vorübergehend in und aus Pools für elastische Datenbanken (innerhalb derselben Region und Leistungsstufe) zu verschieben, ohne den Vorteil der reservierten Kapazität zu verlieren. Durch einen nicht angewendeten Puffer in Ihrer Reservierung können Sie Leistungsspitzen effektiv verwalten, ohne Ihr Budget zu überschreiten.

## <a name="limitation"></a>Einschränkung

In SQL-Datenbank können Sie keine DTU-basierten Datenbanken (Basic, Standard oder Premium) reservieren.

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Kontakt

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nächste Schritte

Der Rabatt auf die Reservierung virtueller Kerne wird automatisch auf die Anzahl von Datenbanken oder verwalteten Instanzen angewendet, die dem Kapazitätsreservierungsumfang und entsprechenden Attributen entsprechen. Sie können den Umfang der Kapazitätsreservierung im [Azure-Portal](https://portal.azure.com), mit PowerShell, der Azure-Befehlszeilenschnittstelle oder der API aktualisieren.

Informationen zum Verwalten der Kapazitätsreservierung finden Sie unter [Verwalten von Reservekapazität](../../cost-management-billing/reservations/manage-reserved-vm-instance.md).

Weitere Informationen zu Azure-Reservierungen finden Sie in den folgenden Artikeln:

- [Was sind Azure-Reservierungen?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Verwalten von Azure-Reservierungen](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [Grundlegendes zum Rabatt für Azure-Reservierungen](../../cost-management-billing/reservations/understand-reservation-charges.md)
- [Grundlegendes zur Nutzung von Azure-Reservierungen für das Abonnement mit nutzungsbasierter Bezahlung](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [Grundlegendes zur Nutzung von Azure-Reservierungen für den Konzernbeitritt](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- [Verkaufen Microsoft Azure Reserved Instances](/partner-center/azure-reservations)