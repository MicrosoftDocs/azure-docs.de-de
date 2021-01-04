---
title: 'Übersicht: Azure Database for MariaDB'
description: Hier finden Sie Informationen zum Azure Database for MariaDB-Dienst, einem relationalen Datenbankdienst in der Microsoft-Cloud, der auf der MySQL Community-Edition basiert.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: overview
ms.custom: mvc
ms.date: 3/18/2020
ms.openlocfilehash: 8115625099543d378728a6313a8cc4c95fec0cd2
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94536998"
---
# <a name="what-is-azure-database-for-mariadb"></a>Was ist Azure Database for MariaDB?

Azure Database for MariaDB ist ein relationaler Datenbankdienst in der Microsoft-Cloud. Azure Database for MariaDB basiert auf den Versionen 10.2 und 10.3 der Datenbank-Engine [MariaDB Community Edition](https://mariadb.org/download/) (verfügbar unter der GPLv2-Lizenz).

Azure Database for MariaDB-Server stellt Folgendes bereit:

- Integrierte Hochverfügbarkeit ohne zusätzliche Kosten.
- Vorhersagbare Leistung inklusive nutzungsbasierter Bezahlung.
- Skalierung je nach Bedarf innerhalb von Sekunden.
- Sicherer Schutz von sensiblen Daten im Ruhezustand und während der Übertragung.
- Automatische Sicherungen und Point-in-Time-Wiederherstellung für bis zu 35 Tage.
- Sicherheit und Konformität auf Unternehmensniveau.

Der Verwaltungsaufwand für diese Funktionen ist minimal. Sie werden ohne zusätzliche Kosten bereitgestellt. Azure Database for MariaDB kann die Entwicklung Ihrer App sowie deren Markteinführung beschleunigen. Sie müssen weder wertvolle Zeit noch wertvolle Ressourcen aufwenden, um virtuelle Computer oder die Infrastruktur zu verwalten. Sie können Ihre Anwendung auch weiterhin unter Verwendung Ihrer bevorzugten Open-Source-Tools und Plattform entwickeln. Entwickeln Sie mit der Geschwindigkeit und Effizienz, die Ihr Unternehmen erfordert, und greifen Sie dabei auf Ihre bereits vorhandenen Kenntnisse zurück.

Informationen zu zentralen Konzepten und Features in Azure Database for MariaDB (einschließlich Leistung, Skalierbarkeit und Verwaltbarkeit) finden Sie in den folgenden Schnellstartanleitungen:

- [Erstellen eines Azure Database for MariaDB-Servers über das Azure-Portal](quickstart-create-mariadb-server-database-using-azure-portal.md)
- [Erstellen eines Azure Database for MariaDB-Servers mit der Azure-Befehlszeilenschnittstelle](quickstart-create-mariadb-server-database-using-azure-cli.md)

<!--
For a set of Azure CLI samples, see:
- [Azure CLI samples for Azure Database for MariaDB](sample-scripts-azure-cli.md) 
-->

## <a name="adjust-performance-and-scale-within-seconds"></a>Anpassen von Leistung und Skalierung in nur wenigen Sekunden

Für den Azure Database for MariaDB-Dienst sind verschiedene Dienstebenen verfügbar: „Basic“, „Universell“ und „Arbeitsspeicheroptimiert“. Jeder Tarif bietet verschiedene Leistungsstufen und Funktionen zur Unterstützung geringer und hoher Datenbankworkloads. Sie können Ihre erste App mit einer kleinen Datenbank für wenige USD im Monat erstellen und die Skalierung dann so ändern, dass sie den Anforderungen Ihrer Lösung entspricht. Dank dynamischer Skalierung kann Ihre Datenbank transparent auf schnell wechselnde Ressourcenanforderungen reagieren. Sie bezahlen nur für die benötigten Ressourcen und auch nur dann, wenn Sie sie verwenden. Einzelheiten hierzu finden Sie unter [Tarife](concepts-pricing-tiers.md).

## <a name="monitoring-and-alerting"></a>Überwachung und Warnung

Wie entscheiden Sie, wann Sie hoch- oder herunterskalieren müssen? Sie können die integrierten Leistungsüberwachungs- und Warnungsfeatures von Azure Database for MariaDB verwenden – kombiniert mit V-Kern-basierten Leistungsbewertungen. Mit diesen Tools können Sie schnell die Auswirkungen des zentralen Hoch- oder Herunterskalierens von virtuellen Kernen auf der Grundlage Ihrer derzeitigen oder erwarteten Leistungsanforderungen bewerten. Einzelheiten hierzu finden Sie unter [Warnungen](howto-alert-metric.md).

## <a name="keep-your-app-and-business-running"></a>Aufrechterhalten von App-Ausführung und Geschäftskontinuität

Die branchenführende SLA von Azure mit einer Verfügbarkeit von 99,99 Prozent wird durch ein globales Netzwerk von durch Microsoft verwalteten Datencentern ermöglicht. Das Netzwerk sorgt dafür, dass Ihre App rund um die Uhr ausgeführt wird. Profitieren Sie von der integrierten Sicherheit, der Fehlertoleranz und dem Datenschutz in Azure Database for MariaDB. Mit Azure Database for MariaDB können Sie über die Point-in-Time-Wiederherstellung den früheren Status eines Servers innerhalb der letzten 35 Tage wiederherstellen.

## <a name="secure-your-data"></a>Sichern der Daten

Azure-Datenbankdienste sind für ihren Datenschutz bekannt. Diese Tradition wird auch von Azure Database for MariaDB fortgeführt. Azure Database for MariaDB bietet Features zur Einschränkung des Zugriffs, zum Schutz von Daten (sowohl in Ruhe als auch während der Übertragung) sowie zur Überwachung von Aktivitäten. Weitere Informationen zur Sicherheit der Azure-Plattform finden Sie im [Azure Trust Center](https://www.microsoft.com/trustcenter/security). Weitere Informationen zu den Sicherheitsfeatures von Azure Database for MariaDB finden Sie in der [Übersicht über die Sicherheit](concepts-security.md).

## <a name="contacts"></a>Kontakte

Fragen oder Vorschläge im Zusammenhang mit der Verwendung von Azure Database for MariaDB können an das [Team für Azure DB for MariaDB](mailto:AskAzureDBforMariaDB@service.microsoft.com) (kein Alias für den technischen Support) gesendet werden.

Weitere Kontaktmöglichkeiten:
- Wenn Sie den Azure-Support kontaktieren möchten, richten Sie im Azure-Portal eine [Anfrage an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Sollten Sie ein Problem mit Ihrem Konto haben, richten Sie im Azure-Portal eine [Anfrage an den Support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
- Wenn Sie Feedback abgeben oder Vorschläge für neue Features einreichen möchten, erstellen Sie einen Eintrag in den [Azure-Feedbackforen](https://feedback.azure.com/forums/915439-azure-database-for-mariadb).

## <a name="next-steps"></a>Nächste Schritte

Nach dieser Einführung in Azure Database for MariaDB können Sie mit Folgendem fortfahren:
- Sehen Sie sich auf der [Seite mit der Preisgestaltung](https://azure.microsoft.com/pricing/details/mariadb/) Kostenvergleiche und -rechner an. 
- [Erstellen Sie Ihren ersten Server.](quickstart-create-mariadb-server-database-using-azure-portal.md)

<!--- - Build your first app using your preferred language: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md) --->
