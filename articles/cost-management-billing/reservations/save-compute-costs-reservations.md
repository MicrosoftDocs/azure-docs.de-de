---
title: Was sind Azure-Reservierungen?
description: Hier erfahren Sie mehr über Azure-Reservierungen und -Preise, um Kosten bei den reservierten Instanzen für virtuelle Computer, SQL-Datenbank, Azure Cosmos DB und andere Ressourcen zu sparen.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: overview
ms.date: 11/18/2020
ms.author: banders
ms.openlocfilehash: 0ef3f833d1bac4f26f49ace2b366cdfcf5ef3e35
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352982"
---
# <a name="what-are-azure-reservations"></a>Was sind Azure-Reservierungen?

Mit Azure-Reservierungen können Sie Geld sparen, indem Sie sich bei mehreren Produkten für Pläne mit einer Laufzeit von einem Jahr oder drei Jahren entscheiden. Dadurch können Sie einen Rabatt für die von Ihnen genutzten Ressourcen in Anspruch nehmen. Reservierungen ermöglichen eine deutliche Senkung der Kosten für Ihre Ressource von bis zu 72 % im Vergleich zur nutzungsbasierten Bezahlung. Reservierungen bieten einen Abrechnungsrabatt und wirken sich nicht auf den Laufzeitstatus Ihrer Ressourcen aus. Nach dem Kauf einer Reservierung gilt der Rabatt automatisch für die übereinstimmenden Ressourcen.

Sie können für eine Reservierung im Voraus oder monatlich bezahlen. Die Gesamtkosten für vorab bezahlte und monatliche Reservierungen sind gleich. Es fallen keine zusätzlichen Gebühren an, wenn Sie sich für die monatliche Zahlung entscheiden. Die monatliche Zahlung ist für Azure-Reservierungen verfügbar, nicht für Produkte von Drittanbietern.

Sie können eine Reservierung im [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) erwerben.

## <a name="why-buy-a-reservation"></a>Warum eine Reservierung kaufen?

Bei einer konsistenten Ressourcennutzung, die Reservierungen unterstützt, können Sie durch den Erwerb einer Reservierung Ihre Kosten senken. Wenn Sie beispielsweise kontinuierlich Instanzen von einem Dienst ohne Reservierung ausführen, fallen die Gebühren für die nutzungsbasierte Bezahlung an. Wenn Sie eine Reservierung kaufen, erhalten Sie sofort den Reservierungsrabatt. Die Ressourcen werden nicht mehr mit den Gebühren für die nutzungsbasierte Bezahlung in Rechnung gestellt.

## <a name="how-reservation-discount-is-applied"></a>Wie der Reservierungsrabatt angewendet wird

Nach dem Kauf gilt der Reservierungsrabatt automatisch für die Ressourcennutzung, die den Attributen entspricht, die Sie beim Erwerb der Reservierung auswählen. Zu den Attributen zählen die SKU, Regionen (falls zutreffend) und der Bereich. Mit dem Reservierungsbereich wird ausgewählt, wo die Reservierungseinsparungen gelten.

Weitere Informationen zur Anwendung des Rabatts finden Sie unter [Anwendung des Rabatts für reservierte Instanzen](reservation-discount-application.md).

Weitere Informationen zur Funktionsweise des Reservierungsbereichs finden Sie unter [Bereichsreservierungen](prepare-buy-reservation.md#scope-reservations).

## <a name="determine-what-to-purchase"></a>Entscheidungshilfe für den Kauf 

Mit Ausnahme von Azure Databricks sind alle Reservierungen stundenbasiert. Erwerben Sie Reservierungen auf der Grundlage der konsistenten Basisnutzung. Sie können bestimmen, welche Reservierung erworben werden soll, indem Sie Ihre Nutzungsdaten analysieren oder Reservierungsempfehlungen nutzen. Empfehlungen sind hier verfügbar:

- Azure Advisor (nur VMs)
- Benutzeroberfläche für den Reservierungskauf im Azure-Portal
- Cost Management-Power BI-App
- APIs 

Weitere Informationen finden Sie unter  [Ermitteln der zu erwerbenden Reservierung](determine-reservation-purchase.md). 

## <a name="buying-a-reservation"></a>Erwerben einer Reservierung 

Sie können Reservierungen über das Azure-Portal, APIs, PowerShell und die CLI erwerben. 

Wechseln Sie für den Kauf zum [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Docs).

Weitere Informationen finden Sie unter  [Kaufen einer Reservierung](prepare-buy-reservation.md).

## <a name="how-is-a-reservation-billed"></a>Wie wird eine Reservierung abgerechnet? 

Die Reservierung wird mit der Zahlungsmethode in Rechnung gestellt, die mit dem Abonnement verknüpft ist. Die Reservierungskosten werden ggf. von Ihrem Verpflichtungsguthaben abgezogen. Wenn Ihr Zahlungsverpflichtungssaldo die Kosten für die Reservierung nicht abdeckt, wird Ihnen die Überschreitung in Rechnung gestellt. Wenn Sie über ein Abonnement in einem individuellen Plan mit Preisen für nutzungsbasierte Bezahlung verfügen, wird die Kreditkarte Ihres Konto umgehend für Vorabkäufe belastet. Monatliche Zahlungen werden auf Ihrer Rechnung angezeigt, und Ihre Kreditkarte wird monatlich belastet. Wenn Sie Rechnungen erhalten, sind die Gebühren Ihrer nächsten Rechnung aufgeführt. 

## <a name="permissions-to-view-and-manage-reservations"></a>Berechtigungen zum Anzeigen und Verwalten von Reservierungen 

Der Benutzer, der eine Reservierung erwirbt, und der Kontoadministrator des Abonnements, das für die Abrechnung der Reservierung verwendet wird, erhalten die Rolle „Besitzer“ im Reservierungsauftrag und in der Reservierung.

Sie können die Verwaltung einer Reservierung delegieren, indem Sie den Rollen im Reservierungsauftrag oder in der Reservierung Personen hinzufügen. Sie weisen die Rollen im Azure-Portal oder mithilfe von APIs und PowerShell zu. 

Weitere Informationen finden Sie unter  [Hinzufügen oder Ändern von Benutzern, die eine Reservierung verwalten können](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation). 

## <a name="get-reservation-details-and-utilization-after-purchase"></a>Abrufen der Reservierungsdetails und -nutzung nach dem Kauf

Wenn Sie über die Berechtigung zum Anzeigen der Reservierung verfügen, können Sie diese und ihre Verwendung im Azure-Portal anzeigen. Sie können die Daten auch mithilfe von APIs abrufen. 

Weitere Informationen zum Anzeigen von Reservierungen im Azure-Portal finden Sie unter  [Anzeigen von Azure-Reservierungen im Azure-Portal](view-reservations.md). 

## <a name="manage-reservations-after-purchase"></a>Verwalten von Reservierungen nach dem Kauf 

Nachdem Sie eine Azure-Reservierung erworben haben, können Sie den Bereich aktualisieren, um die Reservierung auf ein anderes Abonnement anzuwenden, ändern, wer die Reservierung verwalten kann, eine Reservierung in kleinere Teile aufgliedern oder die Instanzgrößenflexibilität ändern. 

Weitere Informationen finden Sie unter  [Verwalten von Reservierungen für Azure-Ressourcen](manage-reserved-vm-instance.md). 

## <a name="flexibility-with-azure-reservations"></a>Flexibilität mit Azure-Reservierungen

Mit Azure-Reservierungen können Sie Ihre sich ändernden Anforderungen flexibel erfüllen. Sie können eine Reservierung gegen eine andere Reservierung des gleichen Typs umtauschen. Sie können auch eine Rückerstattung für eine Reservierung erhalten, wenn Sie sie nicht mehr benötigen (bis zu 50.000 US-Dollar in einem rollierenden Zeitfenster von zwölf Monaten). Das Maximum für die Rückerstattung gilt für alle Reservierungen im Rahmen Ihrer Vereinbarung mit Microsoft.

Weitere Informationen finden Sie unter [Self-Service-Umtausch und -Rückerstattungen für Azure-Reservierungen](exchange-and-refund-azure-reservations.md).


## <a name="charges-covered-by-reservation"></a>Gebühren, die durch Reservierung abgedeckt werden

- **Reservierte VM-Instanz**: Eine Reservierung deckt nur die Computekosten von virtuellen Computern sowie von Clouddiensten ab. Eine Reservierung deckt keine zusätzlichen Kosten für Software, Windows, Netzwerke oder Speicher ab.
- **Reservierte Azure Storage-Kapazität**: Eine Reservierung deckt die Speicherkapazität für Storage Standard-Konten für Blobspeicher oder Azure Data Lake Gen2-Speicher ab. Die Reservierung deckt keine Bandbreite oder Transaktionsraten ab.
- **Reservierte Azure Cosmos DB-Kapazität**: Eine Reservierung deckt den für Ihre Ressourcen bereitgestellten Durchsatz ab. Die Speicher- und Netzwerkkosten werden nicht abgedeckt.
- **Reservierte virtuelle Kerne für SQL-Datenbank**: Deckt sowohl SQL Managed Instance als auch einen Pool für elastische SQL-Datenbanken/eine Einzeldatenbank ab. In einer Reservierung sind nur die Computekosten enthalten. Die SQL-Lizenz wird separat abgerechnet. 
- **Azure Synapse Analytics:** Eine Reservierung deckt die cDWU-Nutzung ab. Sie deckt keine mit der Azure Synapse Analytics-Nutzung verbundenen Speicher- oder Netzwerkgebühren ab.
- **Azure Databricks**: Eine Reservierung deckt nur die DBU-Nutzung ab. Andere Gebühren (etwa Compute-, Speicher- und Netzwerkgebühren) werden separat angewendet.
- **App Service-Stempelgebühr**: Eine Reservierung deckt die Stempelnutzung ab. Sie gilt nicht für Worker. Jede andere dem Stempel zugeordnete Ressource wird also separat abgerechnet.
- **Azure Database for MySQL**: In einer Reservierung sind nur die Computekosten enthalten. Eine Reservierung deckt nicht die Software-, Netzwerk- oder Speichergebühren für den MySQL-Datenbankserver ab.
- **Azure Database for PostgreSQL**: In einer Reservierung sind nur die Computekosten enthalten. Eine Reservierung deckt nicht die Software-, Netzwerk- oder Speichergebühren für PostgreSQL-Datenbankserver ab.
- **Azure Database for MariaDB**: In einer Reservierung sind nur die Computekosten enthalten. Eine Reservierung deckt nicht die Software-, Netzwerk- oder Speichergebühren für den MariaDB-Datenbankserver ab.
- **Azure Data Explorer**: Eine Reservierung beinhaltet die Markupgebühren. Sie deckt keine mit den Clustern verbundenen Compute-, Netzwerk- oder Speichergebühren ab.
- **Azure Cache for Redis**: In einer Reservierung sind nur die Computekosten enthalten. Eine Reservierung deckt keine Netzwerk- oder Speichergebühren im Zusammenhang mit den Redis-Cache-Instanzen ab.
- **Azure Dedicated Host**: In Dedicated Host sind nur die Computekosten enthalten.
- **Azure Disk Storage-Reservierungen**: Eine Reservierung deckt nur SSD Premium-Datenträger der Größe P30 oder höher ab. Es werden keine anderen Datenträgertypen oder -größen abgedeckt, die kleiner als P30 sind.

Softwarepläne:

- **SUSE Linux**: Eine Reservierung deckt die Kosten für den Softwareplan ab. Die Rabatte gelten nur für SUSE-Verbrauchseinheiten und nicht für die Nutzung virtueller Computer.
- **Red Hat-Pläne**: Eine Reservierung deckt die Kosten für den Softwareplan ab. Die Rabatte gelten nur für RedHat-Verbrauchseinheiten und nicht für die Nutzung virtueller Computer.
- **Azure VMware Solution by CloudSimple**: Eine Reservierung deckt die VMware CloudSimple-Knoten ab. Kosten für zusätzliche Softwarekosten fallen weiterhin an.
- **Azure Red Hat OpenShift**: Eine Reservierung gilt für die OpenShift-Kosten, nicht für die Azure-Infrastrukturkosten.

Bei virtuellen Windows-Computern und SQL-Datenbank wird der Reservierungsrabatt nicht auf die Softwarekosten angewendet. Die Lizenzkosten können Sie mit dem [Azure-Hybridvorteil](https://azure.microsoft.com/pricing/hybrid-benefit/) abdecken.

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Wenden Sie sich an uns.

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Azure-Reservierungen finden Sie in den folgenden Artikeln:
    - [Verwalten von Azure-Reservierungen](manage-reserved-vm-instance.md)
    - [Grundlegendes zur Reservierungsnutzung bei einem Abonnement mit nutzungsbasierten Tarifen ](understand-reserved-instance-usage.md)
    - [Grundlegendes zur Nutzung von Azure-Reservierungen für den Konzernbeitritt](understand-reserved-instance-usage-ea.md)
    - [Nicht in Azure-Reservierungen enthaltene Windows-Softwarekosten](reserved-instance-windows-software-costs.md)
    - [Verkaufen Microsoft Azure Reserved Instances](/partner-center/azure-reservations)

- Weitere Informationen zu Reservierungen für Diensttarife:
    - [Virtuelle Computer mit Azure Reserved VM Instances](../../virtual-machines/prepay-reserved-vm-instances.md)
    - [Azure Cosmos DB-Ressourcen mit reservierter Azure Cosmos DB-Kapazität](../../cosmos-db/cosmos-db-reserved-capacity.md)
    - [SQL-Datenbank-Computeressourcen mit reservierter Azure SQL-Datenbank-Kapazität](../../azure-sql/database/reserved-capacity-overview.md)
    - [Azure Cache for Redis-Ressourcen mit reservierter Azure Cache for Redis-Kapazität](../../azure-cache-for-redis/cache-reserved-pricing.md). Erfahren Sie mehr über Reservierungen für Softwarepläne:
    - [Red Hat-Softwarepläne aus Azure-Reservierungen](../../virtual-machines/linux/prepay-suse-software-charges.md)
    - [SUSE-Softwarepläne aus Azure-Reservierungen](../../virtual-machines/linux/prepay-suse-software-charges.md)