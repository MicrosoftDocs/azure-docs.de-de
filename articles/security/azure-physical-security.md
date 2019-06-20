---
title: Physische Sicherheit der Azure-Rechenzentren – Microsoft Azure | Microsoft-Dokumentation
description: Der Artikel beschreibt, was Microsoft zur Sicherung der Azure-Rechenzentren unternimmt, einschließlich der physischen Infrastruktur, der Sicherheit und des Complianceangebots.
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
ms.openlocfilehash: d1b95695de809668987ebb6ef6720a3751205171
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64939848"
---
# <a name="azure-facilities-premises-and-physical-security"></a>Azure-Einrichtungen, Gelände und physische Sicherheit
In diesem Artikel wird beschrieben, welche Maßnahmen Microsoft zur Sicherung der Azure-Infrastruktur ergreift.

## <a name="datacenter-infrastructure"></a>Infrastruktur der Rechenzentren
Azure besteht aus einer [global verteilten Rechenzentrumsinfrastruktur](https://azure.microsoft.com/global-infrastructure/), die Tausende von Onlinediensten unterstützt und mehr als 100 höchst sichere Einrichtungen weltweit umfasst.

Die Infrastruktur bringt Anwendungen und Benutzer weltweit näher zusammen, stellt dabei die Datenresidenz sicher und bietet den Kunden umfassende Optionen hinsichtlich Compliance und Ausfallsicherheit. Azure hat 52-Regionen weltweit und ist in 140 Ländern/Regionen verfügbar.

Als Region wird eine Reihe von Rechenzentren bezeichnet, die über ein großes und robustes Netzwerk miteinander verbunden sind. Das Netzwerk umfasst standardmäßig Inhaltsverteilung, Lastenausgleich, Redundanz und Verschlüsselung. Azure verfügt über mehr globale Regionen als jeder andere Cloudanbieter und bietet Ihnen damit die Flexibilität, Ihre Anwendungen genau dort bereitzustellen, wo Sie diese benötigen.

Azure-Regionen sind in Geografien unterteilt. Eine Azure-Geografie sorgt dafür, dass Anforderungen an Datenresidenz, Datenhoheit, Compliance und Ausfallsicherheit innerhalb geografischer Grenzen erfüllt werden.

In Geografien können Kunden mit spezifischen Anforderungen an Datenresidenz und Compliance ihre Daten und Anwendungen eng zusammenhalten. Geografien sind fehlertolerant und erhalten dank ihrer Verbindung mit unserer dedizierten Netzwerkinfrastruktur mit sehr hohen Kapazitäten die Verfügbarkeit auch beim Ausfall einer ganzen Region.

Verfügbarkeitszonen sind physisch getrennte Standorte in einer Azure-Region. Jede Verfügbarkeitszone besteht aus mindestens einem Rechenzentrum, dessen Stromversorgung, Kühlung und Netzwerkbetrieb unabhängig funktionieren. Durch Verfügbarkeitszonen können Sie unternehmenskritische Anwendungen mit hoher Verfügbarkeit sowie Wiederherstellbarkeit mit geringer Latenz ausführen.

Die folgende Abbildung veranschaulicht, wie die globale Azure-Infrastruktur Regionen und Verfügbarkeitszonen innerhalb der gleichen Datenresidenzgrenze koppelt, um für Hochverfügbarkeit, Notfallwiederherstellung und Sicherungsfunktionen zu sorgen.

![Diagramm zur Veranschaulichung der Datenresidenzgenze][1]

Dank der geografischen Verteilung der Rechenzentren ist Microsoft in der Nähe der Kunden und kann so die Netzwerklatenz reduzieren und für georedundante Sicherung und Failover sorgen.

## <a name="physical-security"></a>Physische Sicherheit
Microsoft entwickelt, erstellt und betreibt Rechenzentren so, dass der physische Zugriff auf die Bereiche, in denen Ihre Daten gespeichert sind, streng kontrolliert wird. Microsoft weiß genau, wie wichtig der Schutz Ihrer Daten ist, und hat sich den Schutz der Rechenzentren, die Ihre Daten enthalten, auf die Fahnen geschrieben. Eine komplette Abteilung befasst sich bei Microsoft mit der Entwicklung, der Erstellung und dem Betrieb der Einrichtungen, die Azure unterstützen. Dieses Team hat die Aufgabe, die physische Sicherheit auf dem neuesten Stand zu halten.

Microsoft verfolgt einen mehrstufigen Ansatz bei der physischen Sicherheit, um das Risiko zu verringern, dass nicht autorisierte Benutzer physischen Zugriff auf Daten und Rechenzentrumsressourcen erhalten. Von Microsoft verwaltete Rechenzentren haben umfassende Schutzebenen: Zugriffsgenehmigung an der Einrichtungsgrenze, an der Gebäudegrenze, im Gebäude und in der Rechenzentrumsetage. Physische Sicherheitsebenen sind:

- **Anforderung und Genehmigung des Zugangs.** Sie müssen den Zugang anfordern, bevor Sie im Rechenzentrum eintreffen. Sie müssen eine triftige geschäftliche Begründung Ihres Besuchs angeben, z.B. Compliance- oder Überwachungszwecke. Alle Anforderungen werden nach Notwendigkeit des Zugangs von Microsoft-Mitarbeitern genehmigt. Dank der „Notwendigkeit des Zugangs“ kann die Anzahl der Personen, die für die Durchführung einer Aufgabe in Rechenzentren erforderlich sind, auf das absolute Minimum reduziert werden. Nachdem Microsoft die Berechtigung erteilt hat, erhält eine Person ausschließlich zu dem abgegrenzten Bereich des Rechenzentrums Zugang, der gemäß der genehmigten geschäftlichen Begründung erforderlich ist. Berechtigungen sind auf einen bestimmten Zeitraum befristet und laufen danach ab.

- **Grundstücksgrenze der Einrichtung**. Wenn Sie im Rechenzentrum eintreffen, müssen Sie dieses durch einen genau definierten Zugangspunkt betreten. In der Regel besteht jeder Zentimeter dieser Eingrenzung aus hohen Zäunen aus Stahl und Beton. Die Rechenzentren sind von Kameras umgeben, und ein Sicherheitsteam überwacht die Aufnahmen jederzeit.

- **Eingang in das Gebäude**. Der Eingang des Rechenzentrums wird von Sicherheitspersonal überwacht, das eine rigorose Ausbildung durchlaufen hat und gründlichen Hintergrundüberprüfungen unterzogen wurde. Das Sicherheitspersonal geht außerdem routinemäßig das Rechenzentrum ab und überwacht jederzeit die Aufnahmen der Kameras im Rechenzentrum.

- **Im Gebäude**. Nachdem Sie das Gebäude betreten haben, müssen Sie eine zweistufige Authentifizierung mittels biometrischer Daten durchlaufen, damit Sie sich weiter im Rechenzentrum bewegen können. Nachdem Ihre Identität überprüft wurde, können Sie ausschließlich den Teil des Rechenzentrums betreten, für den der Zugang genehmigt wurde. Sie können sich dort nur für die Dauer der genehmigten Zeitspanne aufhalten.

- **Rechenzentrumsetage**. Sie dürfen nur die Etage betreten, für die Sie eine Genehmigung haben. Sie müssen eine Personenkontrolle mit Metalldetektor durchlaufen. Um das Risiko zu verringern, dass ohne unser Wissen unberechtigte Daten in das Rechenzentrum gelangen oder dieses verlassen, dürfen nur genehmigte Geräte in die Rechenzentrumsetage mitgenommen werden. Darüber hinaus überwachen Kameras die Vorder- und Rückseite jedes Serverracks. Wenn Sie die Rechenzentrumsetage verlassen, müssen Sie erneut eine vollständige Personenkontrolle mit Metalldetektor durchlaufen. Bevor Sie das Rechenzentrum verlassen können, müssen Sie sich einer weiteren Sicherheitsüberprüfung unterziehen.

Besucher müssen ihre Besucherausweise vor dem Verlassen eines Microsoft-Gebäudes abgeben.

## <a name="physical-security-reviews"></a>Physische Sicherheitsprüfungen
Wir überprüfen in regelmäßigen Abständen die physische Sicherheit der Einrichtungen, um sicherzustellen, dass die Rechenzentren die Azure-Sicherheitsanforderungen erfüllen. Die Mitarbeiter des Rechenzentrum-Hostinganbieters sind nicht für die Verwaltung von Azure-Diensten zuständig. Mitarbeiter können sich nicht bei Azure-Systemen anmelden und haben keinen physischen Zugriff auf den Raum für das Azure-Serverhousing und die Azure-Gehäuse.

## <a name="data-bearing-devices"></a>Geräte mit Daten
Microsoft verwendet bewährte Verfahren und eine Zurücksetzungslösung, die [NIST 800-88-konform](https://csrc.nist.gov/publications/detail/sp/800-88/archive/2006-09-01) ist. Für Festplatten, die nicht zurückgesetzt werden können, verwenden wir ein Verfahren, das die Platte zerstört und eine Wiederherstellung der Daten unmöglich macht. Mögliche Zerstörungsverfahren sind Zersetzen, Schreddern, Pulverisieren oder Verbrennen. Wir bestimmen die Art der Entsorgung anhand des Ressourcentyps. Wir bewahren die Dokumentation der Zerstörung auf.  

## <a name="equipment-disposal"></a>Entsorgung von Ausrüstung
Am Ende der Nutzungsdauer eines Systems befolgen die für den Betrieb zuständigen Microsoft-Mitarbeiter strenge Verfahren zum Umgang mit Daten und zur Entsorgung von Hardware, um sicherzustellen, dass Hardware, die eventuell Ihre Daten enthält, in keinem Fall nicht vertrauenswürdigen Dritten zugänglich gemacht wird. Wir verwenden ein sicheres Löschverfahren für Festplatten, die dieses Verfahren unterstützen. Bei Festplatten, die nicht zurückgesetzt werden können, verwenden wir ein Verfahren, das die Platte zerstört und eine Wiederherstellung der Daten unmöglich macht. Mögliche Zerstörungsverfahren sind Zersetzen, Schreddern, Pulverisieren oder Verbrennen. Wir bestimmen die Art der Entsorgung anhand des Ressourcentyps. Wir bewahren die Dokumentation der Zerstörung auf. Alle Azure-Dienste setzen anerkannte Dienste zur Speicherung und Entsorgung von Medien ein.

## <a name="compliance"></a>Compliance
Wir haben die Azure-Infrastruktur zur Erfüllung einer Vielzahl von internationalen und branchenspezifischen Compliancestandards wie ISO 27001, HIPAA, FedRAMP, SOC 1 und SOC 2 konzipiert und verwalten sie entsprechend. Wir erfüllen auch länder- bzw. regionsspezifische Standards, z.B. Australia IRAP, UK G-Cloud und Singapore MTCS. In rigorosen Audits durch Drittanbieter – zum Beispiel durch das British Standards Institute – wird die Einhaltung der von diesen Standards geforderten strengen Sicherheitskontrollen überprüft.

Eine vollständige Liste der von Azure eingehaltenen Compliancestandards finden Sie unter [Complianceangebote](https://www.microsoft.com/trustcenter/compliance/complianceofferings).

## <a name="next-steps"></a>Nächste Schritte
In den folgenden Artikeln erfahren Sie mehr über den Schutz der Azure-Infrastruktur durch Microsoft:

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
[1]: ./media/azure-physical-security/data-residency-boundary.png
