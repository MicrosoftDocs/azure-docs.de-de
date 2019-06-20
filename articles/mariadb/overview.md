---
title: Übersicht über den relationalen Datenbankdienst Azure Database for MariaDB
description: Übersicht über den relationalen Datenbankdienst Azure Database for MariaDB
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: overview
ms.custom: mvc
ms.date: 03/20/2019
ms.openlocfilehash: a5d00c24531099e66afcb6ccf07cfdf99abd28d1
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "60846241"
---
# <a name="what-is-azure-database-for-mariadb"></a>Was ist Azure Database for MariaDB?

Azure Database for MariaDB ist ein relationaler Datenbankdienst in der Microsoft-Cloud. Azure Database for MariaDB basiert auf Version 10.2 der Datenbank-Engine [MariaDB Community Edition](https://mariadb.org/download/) (verfügbar unter der GPLv2-Lizenz).

Azure Database for MariaDB bietet Folgendes:

- Integrierte Hochverfügbarkeit ohne zusätzliche Kosten.
- Vorhersagbare Leistung inklusive nutzungsbasierter Bezahlung.
- Bedarfsgerechte Skalierung in Sekundenschnelle.
- Schutz von sensiblen Daten (sowohl in Ruhe als auch während der Übertragung).
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

Für den Azure Database for MariaDB-Dienst sind verschiedene Dienstebenen verfügbar: „Basic“, „Allgemein“ und „Arbeitsspeicheroptimiert“. Jeder Tarif bietet verschiedene Leistungsstufen und Funktionen zur Unterstützung geringer und hoher Datenbankworkloads. Sie können Ihre erste App mit einer kleinen Datenbank für wenige USD im Monat erstellen und die Skalierung dann so ändern, dass sie den Anforderungen Ihrer Lösung entspricht. Dank dynamischer Skalierung kann Ihre Datenbank transparent auf schnell wechselnde Ressourcenanforderungen reagieren. Sie bezahlen nur für die benötigten Ressourcen und auch nur dann, wenn Sie sie verwenden. Einzelheiten hierzu finden Sie unter  [Tarife](concepts-pricing-tiers.md).

## <a name="monitoring-and-alerting"></a>Überwachung und Warnung

Wie entscheiden Sie, wann Sie hoch- oder herunterskalieren müssen? Sie können die integrierten Leistungsüberwachungs- und Warnungsfeatures von Azure Database for MariaDB verwenden – kombiniert mit V-Kern-basierten Leistungsbewertungen. Mit diesen Tools können Sie schnell die Auswirkungen des zentralen Hoch- oder Herunterskalierens von virtuellen Kernen auf der Grundlage Ihrer derzeitigen oder erwarteten Leistungsanforderungen bewerten. Einzelheiten hierzu finden Sie unter [Warnungen](howto-alert-metric.md).

## <a name="keep-your-app-and-business-running"></a>Aufrechterhalten von App-Ausführung und Geschäftskontinuität

Die branchenführende SLA von Azure mit einer Verfügbarkeit von 99,99 Prozent wird durch ein globales Netzwerk von durch Microsoft verwalteten Datencentern ermöglicht. Das Netzwerk sorgt dafür, dass Ihre App rund um die Uhr ausgeführt wird. Profitieren Sie von der integrierten Sicherheit, der Fehlertoleranz und dem Datenschutz in Azure Database for MariaDB. Mit Azure Database for MariaDB können Sie über die Point-in-Time-Wiederherstellung den früheren Status eines Servers innerhalb der letzten 35 Tage wiederherstellen.

## <a name="secure-your-data"></a>Sichern der Daten

Azure-Datenbankdienste sind für ihren Datenschutz bekannt. Diese Tradition wird auch von Azure Database for MariaDB fortgeführt. Azure Database for MariaDB bietet Features zur Einschränkung des Zugriffs, zum Schutz von Daten (sowohl in Ruhe als auch während der Übertragung) sowie zur Überwachung von Aktivitäten. Weitere Informationen zur Sicherheit der Azure-Plattform finden Sie im [Azure Trust Center](https://www.microsoft.com/en-us/trustcenter/security).

Der Azure Database for MariaDB-Dienst verwendet Speicherverschlüsselung für ruhende Daten und ist mit FIPS 140-2 konform. Daten werden auf dem Datenträger verschlüsselt. Das gilt auch für Sicherungsdaten. (Temporäre Dateien, die von der Engine beim Ausführen von Abfragen erstellt werden, werden auf dem Datenträger nicht verschlüsselt.) Der Dienst verwendet das in der Azure-Speicherverschlüsselung inbegriffene AES-256-Bit-Verschlüsselungsverfahren. Die Schlüssel werden vom System verwaltet. Die Speicherverschlüsselung ist immer aktiviert und kann nicht deaktiviert werden.

In der Standardeinstellung ist der Azure Database for MariaDB-Dienst so konfiguriert, dass für Daten während der Übertragung innerhalb des Netzwerks [SSL-Verbindungssicherheit](./concepts-ssl-connection-security.md) erforderlich ist. Das Erzwingen von SSL-Verbindungen zwischen dem Datenbankserver und Clientanwendungen trägt zum Schutz vor Man-in-the-Middle-Angriffen bei, indem der Datenstrom zwischen dem Server und der Anwendung verschlüsselt wird. Falls Ihre Clientanwendung keine SSL-Konnektivität unterstützt, können Sie optional die SSL-Anforderung deaktivieren, um eine Verbindung mit Ihrem Datenbankdienst herstellen zu können.

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
