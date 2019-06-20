---
title: Sicherheit der Azure-Infrastruktur | Microsoft-Dokumentation
description: Dieser Artikel beschreibt, wie Microsoft die Sicherheit seiner Azure-Rechenzentren gewährleistet.
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
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: dc9b4db37e811d8bac6df2d532fd3629d98c9650
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60586549"
---
# <a name="azure-infrastructure-security"></a>Sicherheit der Azure-Infrastruktur
Microsoft Azure wird in Rechenzentren ausgeführt, die von Microsoft verwaltet und betrieben werden. Diese geografisch voneinander getrennten Rechenzentren erfüllen die wichtigsten Branchenstandards, wie etwa ISO/IEC 27001:2013 und NIST SP 800-53, bezüglich Sicherheit und Zuverlässigkeit. Die Rechenzentren werden von Microsoft-Betriebspersonal verwaltet und überwacht. Das Betriebspersonal verfügt über langjährige Erfahrung in der Bereitstellung der weltweit größten Onlinedienste, die jeden Tag rund um die Uhr verfügbar sind.

Diese Artikelreihe enthält Informationen zu den Maßnahmen, die Microsoft zum Schutz der Azure-Infrastruktur ergreift. Die Artikel behandeln folgende Themen:

- [Physische Sicherheit](azure-physical-security.md)
- [Verfügbarkeit](azure-infrastructure-availability.md)
- [Komponenten und Grenzen](azure-infrastructure-components.md)
- [Netzwerkarchitektur](azure-infrastructure-network.md)
- [Produktionsnetzwerk](azure-production-network.md)
- [SQL-Datenbank](azure-infrastructure-sql.md)
- [Vorgänge](azure-infrastructure-operations.md)
- [Überwachung](azure-infrastructure-monitoring.md)
- [Integrität](azure-infrastructure-integrity.md)
- [Datenschutz](azure-protection-of-customer-data.md)

## <a name="shared-responsibility-model"></a>Modell der gemeinsamen Zuständigkeit
Es ist wichtig, die Aufteilung der Zuständigkeit zwischen Ihnen und Microsoft zu verstehen. In einer lokalen Umgebung sind Sie für den gesamten Stapel zuständig. Bei einem Wechsel in die Cloud wird dagegen ein Teil der Zuständigkeit auf Microsoft übertragen. Die folgende Abbildung veranschaulicht die Zuständigkeitsbereiche abhängig von der Art der Bereitstellung Ihres Stapels (Software-as-a-Service (SaaS), Platform as a Service (PaaS), Infrastructure-as-a-Service (IaaS) und lokal).

![Abbildung zu den Zuständigkeiten][1]

Sie tragen stets die Verantwortung für die folgenden Elemente, unabhängig von der Art der Bereitstellung:

- Daten
- Endpunkte
- Konto
- Zugriffsverwaltung

Vergewissern Sie sich, dass Sie die Aufteilung der Zuständigkeit zwischen Ihnen und Microsoft in einer SaaS-, PaaS- und IaaS-Bereitstellung verstanden haben. Weitere Informationen dazu finden Sie unter [Gemeinsame Verantwortung für das Cloud Computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf).

## <a name="next-steps"></a>Nächste Schritte
In den folgenden Artikeln erfahren Sie mehr über den Schutz der Azure-Infrastruktur durch Microsoft:

- [Azure-Einrichtungen, Gelände und physische Sicherheit](azure-physical-security.md)
- [Verfügbarkeit der Azure-Infrastruktur](azure-infrastructure-availability.md)
- [Komponenten und Grenzen des Azure-Informationssystems](azure-infrastructure-components.md)
- [Azure-Netzwerkarchitektur](azure-infrastructure-network.md)
- [Azure-Produktionsnetzwerk](azure-production-network.md)
- [Sicherheitsfeatures für Azure SQL-Datenbank](azure-infrastructure-sql.md)
- [Produktionsvorgänge und Verwaltung von Azure](azure-infrastructure-operations.md)
- [Azure-Infrastrukturüberwachung](azure-infrastructure-monitoring.md)
- [Integrität der Azure-Infrastruktur](azure-infrastructure-integrity.md)
- [Schutz der Azure-Kundendaten](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-security-infrastructure/responsibility-zones.png
