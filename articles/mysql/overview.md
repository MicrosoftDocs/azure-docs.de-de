---
title: Übersicht über den relationalen Datenbankdienst Azure Database for MySQL
description: Übersicht über den relationalen Datenbankdienst Azure-Datenbank für MySQL.
ms.service: mysql
author: ajlam
ms.author: andrela
ms.custom: mvc
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 2852cab05fab8e15b7e60a22f54cc866d2f0f178
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61226228"
---
# <a name="what-is-azure-database-for-mysql"></a>Was ist Azure-Datenbank für MySQL?

Azure Database for MySQL ist ein relationaler Datenbankdienst in der Microsoft-Cloud, der auf den Versionen 5.6 und 5.7 der Datenbank-Engine [MySQL Community Edition](https://www.mysql.com/products/community/) (verfügbar unter der GPLv2-Lizenz) basiert. Azure-Datenbank für MySQL bietet Folgendes:

- Integrierte Hochverfügbarkeit ohne zusätzliche Kosten.
- Vorhersagbare Leistung inklusive nutzungsbasierter Bezahlung.
- Bedarfsgerechte Skalierung in Sekundenschnelle
- Gesicherte Datenbank zum Schutz sensibler Daten in Ruhe und Bewegung.
- Automatische Sicherungen und Point-in-Time-Wiederherstellung für bis zu 35 Tage.
- Sicherheit und Konformität auf Unternehmensniveau.

Diese Funktionen erfordern fast keine Administration und werden alle ohne zusätzliche Kosten bereitgestellt. Mit diesen Funktionen können Sie sich auf die schnelle Entwicklung von Apps und die Verkürzung des Zeitraums bis zur Markteinführung konzentrieren, anstatt wertvolle Zeit und Ressourcen für die Verwaltung von virtuellen Computern und der Infrastruktur aufwenden zu müssen. Darüber hinaus können Sie Ihre Anwendung weiterhin mit den Open-Source-Tools und der Plattform Ihrer Wahl entwickeln und mit der Geschwindigkeit und Effizienz liefern, die Ihr Unternehmen verlangt, ohne dass Sie sich dafür neue Fähigkeiten aneignen müssen.

![Konzeptdiagramm zu Azure-Datenbank für MySQL](media/overview/1-azure-db-for-mysql-conceptual-diagram.png)

Dieser Artikel bietet eine Einführung in die wesentlichen Konzepte und Features von Azure-Datenbank für MySQL im Hinblick auf Leistung, Skalierbarkeit und Verwaltbarkeit sowie Links zu detaillierten Informationen. Diese Schnellstarts erleichtern Ihnen den Einstieg:

- [Erstellen eines Servers für Azure-Datenbank für MySQL mithilfe des Azure-Portals](quickstart-create-mysql-server-database-using-azure-portal.md)
- [Erstellen eines Servers für Azure-Datenbank für MySQL mithilfe der Azure CLI](quickstart-create-mysql-server-database-using-azure-cli.md)

Eine Reihe von Azure CLI-Beispielen finden Sie unter:

- [Azure CLI-Beispiele für Azure-Datenbank für MySQL](sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-within-seconds"></a>Anpassen von Leistung und Skalierung in nur wenigen Sekunden
Für den Azure Database for MySQL-Dienst sind verschiedene Dienstebenen verfügbar: „Basic“, „Allgemein“ und „Arbeitsspeicheroptimiert“. Jeder Tarif bietet verschiedene Leistungsstufen und Funktionen zur Unterstützung geringer und hoher Datenbankworkloads. Sie können Ihre erste App mit einer kleinen Datenbank für wenige USD im Monat erstellen und die Skalierung dann so ändern, dass sie den Anforderungen Ihrer Lösung entspricht. Durch die dynamische Skalierung kann Ihre Datenbank transparent auf schnell wechselnde Ressourcenanforderungen reagieren. Sie bezahlen nur für die benötigten Ressourcen und auch nur dann, wenn Sie sie verwenden. Einzelheiten hierzu finden Sie unter  [Tarife](concepts-service-tiers.md).

## <a name="monitoring-and-alerting"></a>Überwachung und Warnung
Wie entscheiden Sie, wann Sie nach oben oder nach unten skalieren? Verwenden Sie die integrierten Features für Leistungsüberwachung und Warnungen in Kombination mit den Leistungsbewertungen nach virtuellen Kernen. Mit diesen Tools können Sie die Auswirkungen des zentralen Hoch- oder Herunterskalierens von virtuellen Kernen je nach Ihren derzeitigen oder erwarteten Leistungsanforderungen schnell bewerten. Einzelheiten hierzu finden Sie unter [Warnungen](howto-alert-on-metric.md).

## <a name="keep-your-app-and-business-running"></a>Aufrechterhalten von App-Ausführung und Geschäftskontinuität
Durch die Unterstützung eines globalen Netzwerks von Microsoft-verwalteten Rechenzentren stellt die branchenführende Verfügbarkeit von Azure mit einer Vereinbarung zum Servicelevel (SLA) von 99,99 % sicher, dass Ihre Apps rund um die Uhr ausgeführt werden. Mit jedem Azure-Datenbank für MySQL-Server nutzen Sie die integrierte Sicherheit, die Fehlertoleranz und die Datensicherheit, die Sie andernfalls entwerfen oder kaufen, erstellen und verwalten müssten. Mit Azure-Datenbank für MySQL können Sie über die Point-in-Time-Wiederherstellung den früheren Status eines Servers innerhalb der letzten 35 Tage wiederherstellen.

## <a name="secure-your-data"></a>Sichern der Daten
Azure-Datenbank für MySQL führt die Tradition der Datensicherheit von Azure-Datenbankdiensten fort – mit Features zum Einschränken des Zugriffs, zum Schützen ruhender und bewegter Daten und zum Überwachen von Aktivitäten. Weitere Informationen zur Sicherheit der Azure-Plattform finden Sie im [Azure Trust Center](https://www.microsoft.com/en-us/trustcenter/security).

Der Azure Database for MySQL-Dienst verwendet Speicherverschlüsselung für ruhende Daten und ist mit FIPS 140-2 konform. Die Daten, einschließlich der Sicherungen, werden auf dem Datenträger verschlüsselt (mit Ausnahme von temporären Dateien, die von der Engine während der Ausführung von Abfragen erstellt werden). Der Dienst verwendet das in der Azure-Speicherverschlüsselung inbegriffene AES-256-Bit-Verschlüsselungsverfahren und die Schlüssel werden vom System verwaltet. Die Speicherverschlüsselung ist immer aktiviert und kann nicht deaktiviert werden.

In der Standardeinstellung ist die Azure-Datenbank für den MySQL-Dienst so konfiguriert, dass [SSL-Verbindungssicherheit](./concepts-ssl-connection-security.md) für Daten während Übertragungen innerhalb des Netzwerks erforderlich ist. Das Erzwingen von SSL-Verbindungen zwischen dem Datenbankserver und Clientanwendungen trägt zum Schutz vor Man-in-the-Middle-Angriffen bei, indem der Datenstrom zwischen dem Server und der Anwendung verschlüsselt wird. Wenn Ihre Clientanwendung keine SSL-Konnektivität unterstützt, können Sie optional die Erzwingung von SSL zum Herstellen einer Verbindung mit dem Datenbankdienst deaktivieren.

## <a name="contacts"></a>Kontakte
Sollten Sie Fragen oder Vorschläge im Zusammenhang mit der Verwendung von Azure Database for MySQL haben, senden Sie eine E-Mail an das zuständige Team ([@Ask Azure DB for MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)). Beachten Sie, dass es sich hierbei nicht um einen Alias für den technischen Support handelt.

Weitere Kontaktmöglichkeiten:

- Wenn Sie den Azure-Support kontaktieren möchten, [fordern Sie im Azure-Portal ein Ticket an](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Um ein Problem mit Ihrem Konto zu beheben, richten Sie im Azure-Portal eine [Anfrage an den Support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
- Wenn Sie Feedback abgeben oder Vorschläge für neue Features einreichen möchten, erstellen Sie einen Eintrag über [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie nun eine Einführung in Azure-Datenbank für MySQL erhalten haben, in der die Frage „Was ist Azure-Datenbank für MySQL?“ beantwortet wurde, sind Sie für Folgendes bereit:

- Auf der Seite mit der Preisgestaltung finden Sie Kostenvergleiche und Rechner. [Preise](https://azure.microsoft.com/pricing/details/mysql/)
- Legen Sie los, und erstellen Sie Ihren ersten Server. [Erstellen eines Servers für Azure-Datenbank für MySQL mithilfe des Azure-Portals](quickstart-create-mysql-server-database-using-azure-portal.md)
- Erstellen Sie Ihre erste App in Ihrer bevorzugten Sprache: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md)
