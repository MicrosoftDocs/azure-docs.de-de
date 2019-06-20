---
title: Verfügbarkeit der Azure-Infrastruktur – Azure-Sicherheit
description: Dieser Artikel enthält Informationen darüber, was Microsoft unternimmt, um die Azure-Infrastruktur zu schützen und eine maximale Verfügbarkeit der Kundendaten zu gewährleisten.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2019
ms.author: terrylan
ms.openlocfilehash: d13c79152afe00cb326f69b523e9fd7f0c3550b0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64872584"
---
# <a name="azure-infrastructure-availability"></a>Verfügbarkeit der Azure-Infrastruktur
Dieser Artikel enthält Informationen darüber, was Microsoft unternimmt, um die Azure-Infrastruktur zu schützen und eine maximale Verfügbarkeit der Kundendaten zu gewährleisten. Azure bietet zuverlässige Verfügbarkeit auf Grundlage umfassender Redundanz mithilfe von Virtualisierungstechnologie.

## <a name="temporary-outages-and-natural-disaster"></a>Temporäre Ausfälle und Naturkatastrophen
Das Microsoft Cloud Infrastructure and Operations-Team entwirft, erstellt, betreibt und verbessert den Schutz der Cloudinfrastruktur. Dieses Team stellt für die Azure-Infrastruktur Hochverfügbarkeit und Zuverlässigkeit, hohe Effizienz, intelligente Skalierbarkeit sicher. Das Team bietet eine sicherere, private und vertrauenswürdige Cloud.

Unterbrechungsfreie Stromversorgungen und riesige Batteriebänke gewährleisten eine fortgesetzte Energieversorgung bei kurzfristigen Stromausfällen. Notstromaggregate sorgen bei längeren Ausfallzeiten und geplanter Wartung für Reservestrom. Im Falle einer Naturkatastrophe kann das Rechenzentrum die vor Ort befindlichen Brennstoffreserven verwenden.

Stabile Glasfasernetze für hohe Geschwindigkeit verbinden die Rechenzentren mit anderen wichtigen Hubs und Internetbenutzern. Serverknoten hosten Workloads näher am Benutzer, um die Latenz zu verringern, Georedundanz bereitzustellen und die Resilienz von Diensten insgesamt zu steigern. Ein Technikerteam arbeitet rund um die Uhr, um sicherzustellen, dass die Dienste ständig zur Verfügung stehen.

Microsoft gewährleistet Hochverfügbarkeit durch erweiterte Überwachung und Reaktion auf Vorfälle, Dienstunterstützung sowie Sicherungs- und Failoverfunktion. Geografisch verteilte Microsoft-Betriebszentren sind 24 Stunden am Tag, 7 Tage die Woche und 365 Tage im Jahr in Betrieb. Das Azure-Netzwerk ist eines der größten der Welt. Das Glasfasernetz für die Inhaltsverteilung verbindet Rechenzentren und Edge-Knoten, um hohe Leistung und Zuverlässigkeit sicherzustellen.

## <a name="disaster-recovery"></a>Notfallwiederherstellung
Azure speichert Ihre Daten dauerhaft an zwei Orten. Sie können den Ort des Sicherungsstandorts auswählen. An beiden Standorten verwaltet Azure ständig drei (3) fehlerfreie Replikate Ihrer Daten.

## <a name="database-availability"></a>Datenbankverfügbarkeit
Azure stellt über ein Internetgateway mit anhaltender Datenbankverfügbarkeit sicher, dass über das Internet auf eine Datenbank zugegriffen werden kann. Bei der Überwachung werden Integrität und Status der aktiven Datenbanken in 5-Minuten-Intervallen ausgewertet.

## <a name="storage-availability"></a>Speicherverfügbarkeit
Azure bietet Speicherung über einen hochgradig skalierbaren und dauerhaften Speicherdienst, der Konnektivitätsendpunkte bietet. Das bedeutet, dass eine Anwendung direkt auf den Speicherdienst zugreifen kann. Der Speicherdienst verarbeitet eingehende Speicheranforderungen effizient mit Transaktionsintegrität.

## <a name="next-steps"></a>Nächste Schritte
In den folgenden Artikeln erfahren Sie mehr über den Schutz der Azure-Infrastruktur durch Microsoft:

- [Azure-Einrichtungen, Gelände und physische Sicherheit](azure-physical-security.md)
- [Komponenten und Grenzen des Azure-Informationssystems](azure-infrastructure-components.md)
- [Azure-Netzwerkarchitektur](azure-infrastructure-network.md)
- [Azure-Produktionsnetzwerk](azure-production-network.md)
- [Sicherheitsfeatures für Azure SQL-Datenbank](azure-infrastructure-sql.md)
- [Produktionsvorgänge und Verwaltung von Azure](azure-infrastructure-operations.md)
- [Azure-Infrastrukturüberwachung](azure-infrastructure-monitoring.md)
- [Integrität der Azure-Infrastruktur](azure-infrastructure-integrity.md)
- [Schutz der Azure-Kundendaten](azure-protection-of-customer-data.md)
