---
title: Datenbanksicherheit – Azure Cosmos DB
description: Erfahren Sie, wie Azure Cosmos DB Datenbankschutz und Datensicherheit für Ihre Daten bereitstellt.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/21/2020
ms.author: mjbrown
ms.openlocfilehash: 19b4c8466e88159839ce1f43a5ba282b1bb3ec9e
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2020
ms.locfileid: "94636910"
---
# <a name="security-in-azure-cosmos-db---overview"></a>Sicherheit bei Azure Cosmos DB – Übersicht
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Dieser Artikel erläutert bewährte Methoden und wichtige Funktionen von Azure Cosmos DB für die Sicherheit von Datenbanken, mit denen Sie Sicherheitsverletzungen in einer Datenbank verhindern bzw. erkennen und darauf reagieren können.

## <a name="whats-new-in-azure-cosmos-db-security"></a>Neuerungen bei der Sicherheit von Azure Cosmos DB

Die Verschlüsselung ruhender Daten, die in Azure Cosmos DB gespeichert sind, ist jetzt für Dokumente und Sicherungen in allen Azure-Regionen verfügbar. Die Verschlüsselung ruhender Daten wird automatisch auf neue und bestehende Kunden in diesen Regionen angewendet. Es ist keine Konfiguration erforderlich. Sie können die gleichen Vorteile im Hinblick auf Wartezeit, Durchsatz, Verfügbarkeit und Funktionalität wie zuvor nutzen und können außerdem auf die Sicherheit Ihrer verschlüsselten ruhenden Daten vertrauen.

## <a name="how-do-i-secure-my-database"></a>Wie schütze ich meine Datenbank?

Für die Sicherheit der Daten sind Sie, Ihr Kunde und Ihr Datenbankanbieter gemeinsam verantwortlich. Der Umfang Ihres Verantwortungsbereichs kann je nach Datenbankanbieter unterschiedlich ausfallen. Wenn Sie sich für eine lokale Lösung entschieden haben, müssen Sie weitgehend selbst für die Sicherheit sorgen – vom Endpunktschutz bis hin zur physischen Sicherung Ihrer Hardware. Das ist keine leichte Aufgabe. Wenn Sie einen PaaS-basierten Clouddatenbankanbieter wie Azure Cosmos DB ins Boot holen, verkleinert sich Ihr Verantwortungsbereich erheblich. Die folgende Abbildung – entnommen aus dem Microsoft-Whitepaper [Shared Responsibilities for Cloud Computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91) (Gemeinsame Verantwortung für das Cloud Computing) – zeigt, wie viel weniger Verantwortung auf Ihren Schultern liegt, wenn Sie mit einem PaaS-Anbieter wie Azure Cosmos DB arbeiten.

:::image type="content" source="./media/database-security/nosql-database-security-responsibilities.png" alt-text="Aufgaben von Kunde und Datenbankanbieter":::

Das obige Diagramm zeigt allgemeine Sicherheitskomponenten in der Cloud. Um welche Elemente müssen Sie sich aber im Speziellen für Ihre Datenbanklösung kümmern? Und wie vergleichen Sie Lösungen sinnvoll miteinander?

Wir empfehlen die folgende Checkliste mit Anforderungen, anhand derer Sie Datenbanksysteme vergleichen können:

- Netzwerksicherheit und Firewalleinstellungen
- Benutzerauthentifizierung und differenzierte Benutzersteuerung
- Möglichkeit zum globalen Replizieren von Daten bei regionalen Ausfällen
- Möglichkeit zum Durchführen eines Failovers zwischen Rechenzentren
- Lokale Datenreplikation innerhalb eines Rechenzentrums
- Automatische Datensicherungen
- Wiederherstellung gelöschter Daten aus Sicherungskopien
- Schützen und Isolieren von vertraulichen Daten
- Überwachen auf Angriffe
- Reagieren auf Angriffe
- Möglichkeit, Daten per Geofencing auf einen geografischen Raum einzugrenzen, um Anforderungen an die Datengovernance zu erfüllen
- Physischer Schutz von Servern in geschützten Rechenzentren
- Zertifizierungen

Folgendes mag auf der Hand liegen, aber [umfassende Verletzungen der Datenbanksicherheit](https://thehackernews.com/2017/01/mongodb-database-security.html) in jüngster Zeit erinnern uns daran, wie wichtig diese vermeintlich einfachen Anforderungen sind:

- Gepatchte Server, die immer auf dem neuesten Stand gehalten werden
- Standardmäßig HTTPS sowie TLS-Verschlüsselung
- Administratorkonten mit sicheren Kennwörtern

## <a name="how-does-azure-cosmos-db-secure-my-database"></a>Wie schützt Azure Cosmos DB meine Datenbank?

Sehen wir uns die obige Liste an: Wie viele dieser Sicherheitsanforderungen erfüllt Azure Cosmos DB? Jede einzelne.

Betrachten wir die Anforderungen nun im Detail.

|Sicherheitsanforderung|Sicherheitsansatz von Azure Cosmos DB|
|---|---|
|Netzwerksicherheit|Die Verwendung einer IP-Firewall ist die erste Schutzschicht, um Ihre Datenbank zu sichern. Azure Cosmos DB unterstützt die richtliniengesteuerte IP-basierte Zugriffssteuerung zur Unterstützung der Firewall für eingehende Verbindungen. Die IP-basierte Zugriffssteuerung ähnelt den Firewallregeln, die von herkömmlichen Datenbanksystemen verwendet werden, erweitert diese jedoch: Auf ein Azure Cosmos-Datenbankkonto kann nur von genehmigten Computern oder Clouddiensten aus zugegriffen werden. Weitere Informationen dazu erhalten Sie im Artikel [Azure Cosmos DB-Firewallunterstützung](how-to-configure-firewall.md).<br><br>Mit Azure Cosmos DB können Sie eine bestimmte IP-Adresse (168.61.48.0), einen IP-Adressbereich (168.61.48.0/8) sowie Kombinationen aus IP-Adressen und -Adressbereichen aktivieren. <br><br>Alle Anforderungen von Computern, die sich nicht auf dieser Zulassungsliste befinden, werden von Azure Cosmos DB blockiert. Anforderungen von zugelassenen Computern und Clouddiensten müssen den Authentifizierungsprozess durchlaufen, um Zugriff auf die Ressourcen zu erhalten.<br><br> Sie können mithilfe von [Diensttags in virtuellen Netzwerken](../virtual-network/service-tags-overview.md) eine Netzwerkisolation erreichen und Ihre Azure Cosmos DB-Ressourcen vor dem allgemeinen Internet schützen. Verwenden Sie Diensttags anstelle von spezifischen IP-Adressen, wenn Sie Sicherheitsregeln erstellen. Wenn Sie den Diensttagnamen (beispielsweise „AzureCosmosDB“) im entsprechenden Quell- oder Zielfeld einer Regel angeben, können Sie den Datenverkehr für den entsprechenden Dienst zulassen oder verweigern.|
|Authorization|Azure Cosmos DB verwendet einen hashbasierten Code für die Authentifizierung von Nachrichten (Hash-based Message Authentication Code, HMAC) zur Autorisierung. <br><br>Für jede Anforderung wird mithilfe des geheimen Kontoschlüssels ein Hash erstellt, und der daraus resultierende Base64-codierte Hashwert wird mit jedem Aufruf an Azure Cosmos DB gesendet. Zum Überprüfen der Anforderung verwendet der Azure Cosmos DB-Dienst den richtigen geheimen Schlüssel und die zugehörigen Eigenschaften, um einen Hash zu generieren. Anschließend vergleicht der Dienst diesen Wert mit dem Wert in der Anforderung. Wenn die beiden Werte übereinstimmen, wird der Vorgang autorisiert und die Anforderung verarbeitet. Andernfalls wird ein Autorisierungsfehler ausgegeben und die Anforderung abgelehnt.<br><br>Sie können entweder einen [Primärschlüssel](#primary-keys) oder ein [Ressourcentoken](secure-access-to-data.md#resource-tokens) verwenden, das differenzierten Zugriff auf eine Ressource wie z. B. ein Dokument ermöglicht.<br><br>Weitere Informationen dazu erhalten Sie unter [Sicherer Zugriff auf Daten in Azure Cosmos DB](secure-access-to-data.md).|
|Benutzer und Berechtigungen|Mit dem Primärschlüssel für das Konto können Sie Benutzerressourcen und Berechtigungsressourcen für jede Datenbank erstellen. Ein Ressourcentoken wird einer Berechtigung in einer Datenbank zugeordnet und legt fest, ob der Benutzer Zugriff (Lese-/Schreibzugriff, schreibgeschützten Zugriff oder keinen Zugriff) auf eine Anwendungsressource in der Datenbank erhält. Zu den Anwendungsressourcen gehören Container, Dokumente, Anlagen, gespeicherte Prozeduren, Trigger und benutzerdefinierte Funktionen. Das Ressourcentoken wird dann während der Authentifizierung verwendet, um den Zugriff auf die Ressource zu gewähren oder zu verweigern.<br><br>Weitere Informationen dazu erhalten Sie unter [Sicherer Zugriff auf Daten in Azure Cosmos DB](secure-access-to-data.md).|
|Active Directory-Integration (Azure RBAC)| Sie können auch den Zugriff auf das Cosmos-Konto, die Datenbank, den Container und die Angebote (Durchsatz) über die Zugriffssteuerung (IAM) im Azure-Portal bereitstellen oder einschränken. IAM stellt eine rollenbasierte Zugriffssteuerung bereit und lässt sich in Active Directory integrieren. Sie können für Einzelbenutzer und Gruppen integrierte oder benutzerdefinierte Rollen verwenden. Im Artikel [Active Directory-Integration](role-based-access-control.md) erhalten Sie weitere Informationen.|
|Globale Replikation|Azure Cosmos DB bietet eine sofort einsatzbereite globale Verteilung, mit der Sie Ihre Daten mit einem einzigen Mausklick in jedes der weltweiten Azure-Rechenzentren replizieren können. Mit der globalen Replikation können Sie global skalieren und für niedrige Latenzen beim Zugriff auf Ihre Daten auf der ganzen Welt sorgen.<br><br>Im Kontext der Sicherheit schützt die globale Replikation Ihre Daten vor regionalen Ausfällen.<br><br>Weitere Informationen finden Sie unter [Globale Verteilung von Daten](distribute-data-globally.md).|
|Regionale Failover|Wenn Sie Ihre Daten in mehr als ein Rechenzentrum repliziert haben, führt Azure Cosmos DB automatisch ein Rollover Ihrer Vorgänge aus, falls ein regionales Rechenzentrum offline geschaltet wird. Sie können anhand der Regionen, in denen Ihre Daten repliziert werden, eine Prioritätsliste mit Failoverregionen erstellen. <br><br>Weitere Informationen finden Sie unter [Regionale Failover in Azure Cosmos DB](high-availability.md).|
|Lokale Replikation|Azure Cosmos DB repliziert Daten automatisch auch innerhalb eines einzelnen Rechenzentrums und sorgt so für Hochverfügbarkeit sowie die Möglichkeit, verschiedene [Konsistenzebenen](consistency-levels.md) auszuwählen. Diese Replikation garantiert eine [Verfügbarkeits-SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db) von 99,99 % für alle Konten mit einer einzelnen Region und für alle Konten mit mehreren Regionen und gelockerter Konsistenz sowie eine Leseverfügbarkeit von 99,999 % für alle Datenbankkonten mit mehreren Regionen.|
|Automatisierte Onlinesicherungen|Azure Cosmos-Datenbanken werden regelmäßig aktualisiert und in einem georedundanten Speicher gespeichert. <br><br>Weitere Informationen erhalten Sie unter [Automatische Onlinesicherung und -wiederherstellung mit Azure Cosmos DB](online-backup-and-restore.md).|
|Wiederherstellen gelöschter Daten|Automatisierte Onlinesicherungen können zum Wiederherstellen von Daten verwendet werden, die versehentlich gelöscht wurden. Dies ist bis zu ca. 30 Tage nach dem Löschen möglich. <br><br>Weitere Informationen erhalten Sie unter [Automatische Onlinesicherung und -wiederherstellung mit Azure Cosmos DB](online-backup-and-restore.md).|
|Schützen und Isolieren von vertraulichen Daten|Jetzt sind alle ruhenden Daten verschlüsselt, die sich in den unter „Neuerungen“ aufgeführten Regionen befinden.<br><br>Personenbezogene und vertrauliche Daten können in einem bestimmten Container isoliert werden, und der Lese-/Schreibzugriff bzw. der schreibgeschützte Zugriff kann auf bestimmte Benutzer beschränkt werden.|
|Überwachen auf Angriffe|Mithilfe von [Überwachungsprotokollierung und Aktivitätsprotokollen](./monitor-cosmos-db.md) können Sie Ihr Konto auf normale und ungewöhnliche Aktivitäten überwachen. Sie können anzeigen, welche Vorgänge für Ihre Ressourcen ausgeführt wurden, wer den jeweiligen Vorgang initiiert hat, wann der Vorgang ausgeführt wurde, welchen Status der Vorgang aufweist und vieles mehr, wie im Screenshot unter dieser Tabelle gezeigt.|
|Reagieren auf Angriffe|Nachdem Sie den Azure-Support kontaktiert haben, um einen möglichen Angriff zu melden, wird ein aus fünf Schritten bestehender Prozess zur Reaktion auf den Incident angestoßen. Ziel dieses Prozesses ist es, die normale Dienstsicherheit und den normalen Betrieb so schnell wie möglich wiederherzustellen, nachdem ein Problem erkannt und eine Untersuchung gestartet wurde.<br><br>Weitere Informationen erhalten Sie unter [Microsoft Azure Security Response in the Cloud](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91) (Sicherheitsreaktion von Microsoft Azure in der Cloud).|
|Geofencing (geografische Eingrenzung)|Azure Cosmos DB stellt die Datengovernance und -compliance für unabhängige Regionen sicher (z.B. Deutschland, China, US Government).|
|Geschützte Einrichtungen|Die Daten in Azure Cosmos DB sind auf SSDs in den geschützten Rechenzentren von Azure gespeichert.<br><br>Weitere Informationen erhalten Sie unter [Globale Rechenzentren von Microsoft](https://www.microsoft.com/en-us/cloud-platform/global-datacenters).|
|HTTPS-/SSL-/TLS-Verschlüsselung|Alle Verbindungen mit Azure Cosmos DB unterstützen HTTPS. Azure Cosmos DB unterstützt auch TLS 1.2.<br>Es ist möglich, eine serverseitige TLS-Mindestversion zu erzwingen. Wenden Sie sich zu diesem Zweck an [azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com).|
|Verschlüsselung ruhender Daten|Alle in Azure Cosmos DB gespeicherte Daten werden im Ruhezustand verschlüsselt. Weitere Informationen finden Sie unter [Azure Cosmos DB-Verschlüsselung ruhender Daten](./database-encryption-at-rest.md).|
|Gepatchte Server|Da Azure Cosmos DB eine verwaltete Datenbank ist, müssen Sie keine Server verwalten oder patchen – das wird automatisch für Sie erledigt.|
|Administratorkonten mit sicheren Kennwörtern|Es ist kaum zu glauben, dass diese Anforderung überhaupt erwähnt werden muss, aber im Gegensatz zu einigen unserer Wettbewerber ist es in Azure Cosmos DB unmöglich, ein Administratorkonto ohne Kennwort einzurichten.<br><br> Die Sicherheit über TLS und die auf HMAC-Geheimnissen basierende Authentifizierung sind standardmäßig integriert.|
|Zertifizierungen für Sicherheit und Datenschutz| Die aktuelle Liste mit Zertifizierungen finden Sie auf der allgemeinen [Website mit Complianceangeboten für Azure](https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings) und im aktuellen [Dokument zur Azure-Compliance](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) mit allen Zertifizierungen (suchen Sie nach „Cosmos“). Ausführlichere Informationen finden Sie im Beitrag [Azure #CosmosDB: sicher, privat, konform] vom 25. April 2018, in dem es um SOCS 1/2 Type 2, HITRUST, PCI-DSS Level 1, ISO 27001, HIPAA, FedRAMP High und vieles mehr geht.

Der folgende Screenshot zeigt, wie Sie Überwachungs- und Aktivitätsprotokolle zum Überwachen Ihres Kontos verwenden können: :::image type="content" source="./media/database-security/nosql-database-security-application-logging.png" alt-text="Aktivitätsprotokolle für Azure Cosmos DB":::

<a id="primary-keys"></a>

## <a name="primary-keys"></a>Primärschlüssel

Primärschlüssel ermöglichen den Zugriff auf alle Verwaltungsressourcen für das Datenbankkonto. Primärschlüssel:

- Ermöglichen den Zugriff auf Konten, Datenbanken, Benutzer und Berechtigungen. 
- Können nicht zur präzisen Steuerung des Zugriffs auf Container und Dokumente verwendet werden.
- Werden im Zuge der Kontoerstellung erstellt.
- Können jederzeit neu generiert werden.

Jedes Konto umfasst zwei Primärschlüssel: einen primären und einen sekundären Schlüssel. Dank der Verwendung von zwei Schlüsseln können Sie Schlüssel neu generieren oder ersetzen und trotzdem ohne Unterbrechung auf Ihr Konto und Ihre Daten zugreifen.

Neben den beiden Primärschlüsseln für das Cosmos DB-Konto stehen noch zwei schreibgeschützte Schlüssel zur Verfügung. Diese schreibgeschützten Schlüssel ermöglichen ausschließlich Lesevorgänge für das Konto. Schreibgeschützte Schlüssel bieten keinen Zugriff auf Leseberechtigungsressourcen.

Primäre, sekundäre und schreibgeschützte Primärschlüssel sowie Primärschlüssel mit Lese-/Schreibzugriff können über das Azure-Portal abgerufen und neu generiert werden. Eine entsprechende Anleitung finden Sie unter [Anzeigen, Kopieren und erneutes Generieren von Zugriffsschlüsseln](manage-with-cli.md#regenerate-account-key).

:::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-portal.png" alt-text="Zugriffssteuerung (IAM) im Azure-Portal: Veranschaulichung der NoSQL-Datenbanksicherheit":::

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Primärschlüsseln und Ressourcentoken finden Sie unter [Sichern des Zugriffs auf Azure Cosmos DB-Daten](secure-access-to-data.md).

Weitere Informationen zur Überwachungsprotokollierung finden Sie unter [Diagnoseprotokollierung für Azure Cosmos DB](./monitor-cosmos-db.md).

Weitere Informationen zu Microsoft-Zertifizierungen finden Sie im [Azure Trust Center](https://azure.microsoft.com/support/trust-center/).