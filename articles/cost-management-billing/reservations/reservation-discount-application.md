---
title: Anwendung des Rabatts für Azure-Reservierungen
description: In diesem Artikel erfahren Sie, wie Rabatte für reservierte Instanzen im Allgemeinen angewendet werden.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 03/22/2020
ms.author: banders
ms.openlocfilehash: 1dec8253d785973aa900434c10f85ec0a63e0599
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99574871"
---
# <a name="how-a-reservation-discount-is-applied"></a>Anwendung eines Reservierungsrabatts

In diesem Artikel erfahren Sie, wie Rabatte für reservierte Instanzen im Allgemeinen angewendet werden. Der Reservierungsrabatt gilt für die Ressourcennutzung, die den Attributen entspricht, die Sie beim Kauf der Reservierung auswählen. Zu den Attributen gehört der Bereich, in dem die entsprechenden VMs, SQL-Datenbank-Instanzen, Azure Cosmos DB-Instanzen oder anderen Ressourcen ausgeführt werden. Beispiel: Wenn Sie einen Rabatt auf eine Reservierung für vier VMs vom Typ „Standard D2“ in der Region „USA, Westen“ nutzen möchten, wählen Sie das Abonnement aus, in dem die VMs ausgeführt werden.

Reservierungsrabatte funktionieren nach dem Prinzip „*use-it-or-lose-it*“. Wenn Sie für eine Stunde nicht über die entsprechenden Ressourcen verfügen, verlieren Sie folglich eine Reservierungsmenge für diese Stunde. Sie können ungenutzte reservierte Stunden nicht übertragen.

Wenn Sie eine Ressource beenden, wird der Reservierungsrabatt automatisch auf eine andere entsprechende Ressource im angegebenen Reservierungsumfang angewandt. Wenn keine übereinstimmenden Ressourcen im angegebenen Reservierungsumfang gefunden werden, gehen die reservierten Stunden *verloren*.

Beispielsweise können Sie später eine Ressource erstellen und über eine entsprechende Reservierung verfügen, die nicht ausgelastet ist. Der Reservierungsrabatt gilt automatisch für die neue übereinstimmende Ressource.

Wenn die virtuellen Computer in verschiedenen Abonnements in Ihrer Registrierung/Ihrem Konto ausgeführt werden, wählen Sie den Bereich „Freigegeben“ aus. Der Bereich „Freigegeben“ ermöglicht die abonnementübergreifende Anwendung des Reservierungsrabatts. Sie können den Bereich nach dem Erwerb einer Reservierung ändern. Weitere Informationen finden Sie unter [Verwalten von Azure-Reservierungen](manage-reserved-vm-instance.md).

Der Rabatt auf Reservierungen gilt nur für Ressourcen, denen Enterprise, eine Microsoft-Kundenvereinbarung, CSP oder Abonnements mit nutzungsbasierter Bezahlung zugeordnet sind. Für Ressourcen, die in einem Abonnement mit anderen Angebotstypen ausgeführt werden, gilt der Reservierungsrabatt nicht.

## <a name="when-the-reservation-term-expires"></a>Wann das Ende des Reservierungszeitraums erreicht ist

Am Ende der Reservierungslaufzeit läuft der Abrechnungsrabatt ab, und die Ressourcen werden zu den Preisen für die nutzungsbasierte Bezahlung abgerechnet. Die Reservierungen werden standardmäßig nicht automatisch verlängert. Sie können die automatische Verlängerung einer Reservierung aktivieren, indem Sie die Option in den Verlängerungseinstellungen auswählen. Bei aktivierter automatischer Verlängerung wird nach Ablauf der vorhandenen Reservierung eine Ersatzreservierung erworben. Die Ersatzreservierung hat standardmäßig die gleichen Attribute wie die ablaufende Reservierung. Fakturierungsintervall, Laufzeit und Menge können jedoch optional in den Verlängerungseinstellungen geändert werden. Jeder Benutzer mit Besitzerzugriff auf die Reservierung und das Abonnement, das für die Abrechnung verwendet wird, kann eine Verlängerung einrichten.  

## <a name="discount-applies-to-different-sizes"></a>Rabatt gilt für verschiedene Größen

Wenn Sie eine Reservierung erwerben, kann der Rabatt auf andere Instanzen mit Attributen angewendet werden, die sich in der gleichen Größengruppe befinden. Dieses Feature wird als „Instanzgrößenflexibilität“ bezeichnet. Die Flexibilität der Rabattabdeckung hängt vom Reservierungstyp und den Attributen ab, die Sie beim Erwerb der Reservierung auswählen.

Servicepläne:

- Reservierte VM-Instanzen: Wenn Sie die Reservierung erwerben und **Optimiert für: Flexibilität bei der Instanzgröße** wählen, hängt die Abdeckung des Rabatts von der ausgewählten VM-Größe ab. Die Reservierung kann für VM-Größen in derselben Größenordnung gelten. Weitere Informationen finden Sie unter [Flexibilität bei der VM-Größe mit reservierten VM-Instanzen](../../virtual-machines/reserved-vm-instance-size-flexibility.md).
- Reservierte Azure Storage-Kapazität: Sie können reservierte Kapazität für Azure Storage Standard-Konten in Einheiten von 100 TiB oder 1 PiB pro Monat erwerben. Informationen zu den Regionen, die reservierte Azure Storage-Kapazität unterstützen, finden Sie unter [Preise für Blockblobs](https://azure.microsoft.com/pricing/details/storage/blobs/). Reservierte Azure Storage-Kapazität ist für alle Zugriffsebenen („Heiß“, „Kalt“ und „Archiv“) und für alle Replikationskonfigurationen (LRS, GRS und ZRS) verfügbar.
- Reservierte SQL-Datenbank-Kapazität: Die Rabattabdeckung hängt von der ausgewählten Leistungsstufe ab. Weitere Informationen finden Sie unter [Grundlegendes zur Anwendung eines Rabatts für Azure-Reservierungen auf SQL-Datenbank-Instanzen](understand-reservation-charges.md).
- Reservierte Azure Cosmos DB-Kapazität: Die Rabattabdeckung hängt vom bereitgestellten Durchsatz ab. Weitere Informationen finden Sie unter [Understand how an Azure Cosmos DB reservation discount is applied (Grundlegendes zur Anwendung eines Rabatts für Azure Cosmos DB-Reservierungen)](understand-cosmosdb-reservation-charges.md).

## <a name="how-discounts-apply-to-specific-azure-services"></a>Anwendung von Rabatten auf bestimmte Azure-Dienste

In den folgenden Artikeln erfahren Sie, wie Rabatte auf einen bestimmten Azure-Dienst angewendet werden:

- [App Service](reservation-discount-app-service.md)
- [Azure Cache for Redis](understand-azure-cache-for-redis-reservation-charges.md)
- [Cosmos DB](understand-cosmosdb-reservation-charges.md)
- [Database for MariaDB](understand-reservation-charges-mariadb.md)
- [Database for MySQL](understand-reservation-charges-mysql.md)
- [Database for PostgreSQL](understand-reservation-charges-postgresql.md)
- [Databricks](reservation-discount-databricks.md)
- [Data Explorer](understand-azure-data-explorer-reservation-charges.md)
- [Dedicated Host](billing-understand-dedicated-hosts-reservation-charges.md)
- [Disk Storage](understand-disk-reservations.md)
- [Red Hat Linux Enterprise](understand-rhel-reservation-charges.md)
- [Softwarepläne](understand-suse-reservation-charges.md)
- [Storage](understand-storage-charges.md)
- [SQL-Datenbank](understand-reservation-charges.md)
- [Azure Synapse Analytics](reservation-discount-azure-sql-dw.md)
- [Virtuelle Computer](../manage/understand-vm-reservation-charges.md)


## <a name="next-steps"></a>Nächste Schritte

- [Verwalten von Azure-Reservierungen](manage-reserved-vm-instance.md)
- [Grundlegendes zur Reservierungsnutzung bei einem Abonnement mit nutzungsbasierten Tarifen ](understand-reserved-instance-usage.md)
- [Grundlegendes zur Nutzung von Azure-Reservierungen für den Konzernbeitritt](understand-reserved-instance-usage-ea.md)
- [Nicht in Azure-Reservierungen enthaltene Windows-Softwarekosten](reserved-instance-windows-software-costs.md)