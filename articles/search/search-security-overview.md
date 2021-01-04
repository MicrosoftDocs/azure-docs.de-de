---
title: Sicherheitsübersicht
titleSuffix: Azure Cognitive Search
description: Die kognitive Azure-Suche ist kompatibel mit SOC 2, HIPAA und anderen Zertifizierungen. Verbindungs- und Datenverschlüsselung, Authentifizierung und Identitätszugriff durch Benutzer- und Gruppensicherheits-IDs in Filterausdrücken.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/01/2020
ms.custom: references_regions
ms.openlocfilehash: f314394d3a0ac453d525079e096162d8739f67cf
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011794"
---
# <a name="security-in-azure-cognitive-search---overview"></a>Sicherheit in Azure Cognitive Search: Übersicht

In diesem Artikel werden die wichtigsten Sicherheitsfeatures in Azure Cognitive Search beschrieben, mit denen Inhalte und Vorgänge geschützt werden können.

+ Auf der Speicherebene ist die Verschlüsselung ruhender Daten für alle vom Dienst verwalteten Inhalte, die auf Datenträgern gespeichert werden, integriert, einschließlich Indizes, Synonymzuordnungen und die Definitionen von Indexern, Datenquellen und Skillsets. Azure Cognitive Search unterstützt auch das Hinzufügen von kundenseitig verwalteten Schlüsseln (Customer-Managed Keys, CMK) zur zusätzlichen Verschlüsselung indexierter Inhalte. Bei Diensten, die nach dem 1. August 2020 erstellt wurden, erstreckt sich die CMK-Verschlüsselung auch auf Daten auf temporären Datenträgern, um eine vollständige doppelte Verschlüsselung der indexierten Inhalte zu erreichen.

+ Die Eingangssicherheit schützt den Endpunkt des Suchdiensts mit steigendem Sicherheitsniveau: von API-Schlüsseln in der Anforderung über Eingangsregeln in der Firewall bis zu privaten Endpunkten, die Ihren Dienst vollständig vom öffentlichen Internet abschirmen.

+ Die Ausgangssicherheit betrifft Indexer, die Inhalte aus externen Quellen abrufen. Richten Sie für ausgehende Anforderungen eine verwaltete Identität ein, um die Suche zu einem vertrauenswürdigen Dienst zu machen, wenn auf Daten aus Azure Storage, Azure SQL, Cosmos DB oder anderen Azure-Datenquellen zugegriffen wird. Eine verwaltete Identität stellt einen Ersatz für Anmeldeinformationen oder Zugriffsschlüssel für die Verbindung dar. Ausgehende Sicherheit wird in diesem Artikel nicht behandelt. Weitere Informationen zu dieser Funktion finden Sie unter [Herstellen einer Verbindung mit einer Datenquelle mithilfe einer verwalteten Identität](search-howto-managed-identities-data-sources.md).

Eine Übersicht der Sicherheitsarchitektur und der einzelnen Featurekategorien finden Sie in diesem temporeichen Video.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Azure-Cognitive-Search-Whats-new-in-security/player]

<a name="encryption"></a>

## <a name="encrypted-transmissions-and-storage"></a>Verschlüsselte Übertragung und Speicherung

Die Verschlüsselung in Azure Cognitive Search beginnt bei Verbindungen und Übertragungen und reicht bis zu den auf Datenträgern gespeicherten Inhalten. Für Suchdienste im öffentlichen Internet lauscht Azure Cognitive Search an HTTPS-Port 443. Alle Client-zu-Dienst-Verbindungen verwenden TLS 1.2-Verschlüsselung. Frühere Versionen (1.0 oder 1.1) werden nicht unterstützt.

:::image type="content" source="media/search-security-overview/encryption-at-rest-cmk.png" alt-text="Diagramm mit den verschiedenen Arten der Sicherheit auf jeder Ebene der Diensteinbindung":::

In der folgenden Tabelle werden für Daten, die vom Suchdienst intern verarbeitet werden, die [Datenverschlüsselungsmodelle](../security/fundamentals/encryption-models.md) beschrieben. Einige Features, wie z. B. der Wissensspeicher, inkrementelle Anreicherung und indexerbasierte Indizierung, lesen von oder schreiben in Datenstrukturen in anderen Azure Services. Diese Dienste verfügen über eigene Ebene für die Unterstützung von Verschlüsselungen, die von Azure Cognitive Search getrennt sind.

| Modell | Schlüssel&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Anforderungen&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | Beschränkungen | Gilt für: |
|------------------|-------|-------------|--------------|------------|
| Serverseitige Verschlüsselung | Von Microsoft verwaltete Schlüssel | Keine (integriert) | Keine, verfügbar auf allen Ebenen, in allen Regionen, für Inhalte, die nach dem 24. Januar 2018 erstellt wurden. | Inhalt (Indizes und Synonymzuordnungen) und Definitionen (Indexer, Datenquellen, Skillsets) |
| Serverseitige Verschlüsselung | Kundenseitig verwaltete Schlüssel | Azure-Schlüsseltresor | Verfügbar für abrechenbare Tarife in allen Regionen für Inhalte, die nach Januar 2019 erstellt wurden. | Inhalt (Indizes und Synonymzuordnungen) auf Datenträgern |
| Serverseitige doppelte Verschlüsselung | Kundenseitig verwaltete Schlüssel | Azure-Schlüsseltresor | Verfügbar für abrechenbare Tarife in ausgewählten Regionen in den Suchdiensten nach dem 1. August 2020. | Inhalt (Indizes und Synonymzuordnungen) auf Datenträgern und temporären Datenträgern |

### <a name="service-managed-keys"></a>Dienstseitig verwaltete Schlüssel

Dienstseitig verwaltete Verschlüsselung ist ein Microsoft-interner Vorgang, der auf einer [256-Bit-AES-Verschlüsselung](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) durch die [Azure-Speicherdienstverschlüsselung](../storage/common/storage-service-encryption.md) basiert. Sie wird automatisch auf die gesamte Indizierung angewendet, einschließlich auf inkrementelle Updates für nicht vollständig verschlüsselte Indizes (vor Januar 2018 erstellt).

### <a name="customer-managed-keys-cmk"></a>Kundenseitig verwaltete Schlüssel (CMK)

Kundenseitig verwaltete Schlüssel erfordern einen zusätzlichen kostenpflichtigen Dienst, Azure Key Vault, der sich in einer anderen Region, aber unter demselben Abonnement wie Azure Cognitive Search befinden kann. Durch Aktivieren der CMK-Verschlüsselung wird die Indexgröße erhöht und die Abfrageleistung beeinträchtigt. Basierend auf den bisherigen Beobachtungen können Sie mit einem Anstieg der Abfragezeiten um 30 %–60 % rechnen, wobei die tatsächliche Leistung je nach Indexdefinition und Art der Abfragen variiert. Aufgrund dieser Auswirkungen auf die Leistung wird empfohlen, diese Funktion nur für Indizes zu aktivieren, für die sie wirklich erforderlich ist. Weitere Informationen finden Sie unter [Konfigurieren von kundenseitig verwalteten Schlüsseln für die Datenverschlüsselung in Azure Cognitive Search](search-security-manage-encryption-keys.md).

<a name="double-encryption"></a>

### <a name="double-encryption"></a>Doppelte Verschlüsselung

Die doppelte Verschlüsselung in Azure Cognitive Search ist eine Erweiterung von CMK. Es handelt sich dabei um eine zweifache Verschlüsselung (einmal durch CMK und einmal durch dienstseitig verwaltete Schlüssel), die sowohl eine langfristige Speicherung auf einem Datenträger und eine kurzfristige Speicherung auf temporären Datenträgern umfasst. Der Unterschied zwischen CMK vor dem 1. August 2020 und danach, und was CMK zu einem doppelten Verschlüsselungsfeature in Azure Cognitive Search macht, ist die zusätzliche Verschlüsselung von ruhenden Daten auf temporären Datenträgern.

Die doppelte Verschlüsselung ist zurzeit für neue Dienste verfügbar, die nach dem 1. August in diesen Regionen erstellt werden:

+ USA, Westen 2
+ East US
+ USA Süd Mitte
+ US Government, Virginia
+ US Gov Arizona

<a name="service-access-and-authentication"></a>

## <a name="inbound-security-and-endpoint-protection"></a>Eingangssicherheit und Endpunktschutz

Features der Eingangssicherheit schützen den Suchdienst-Endpunkt mithilfe ansteigender Sicherheits- und Komplexitätsstufen. Zunächst ist für alle Anforderungen ein API-Schlüssel für den authentifizierten Zugriff erforderlich. Zweitens können Sie optional Firewallregeln festlegen, die den Zugriff auf bestimmte IP-Adressen beschränken. Für erweiterten Schutz kann als dritte Option Azure Private Link aktiviert werden, um Ihren Dienstendpunkt von jeglichem Internetdatenverkehr abzuschirmen.

### <a name="public-access-using-api-keys"></a>Öffentlicher Zugriff mithilfe von API-Schlüsseln

Standardmäßig erfolgt der Zugriff auf einen Suchdienst über die Public Cloud, wobei für den Administrator- oder Abfragezugriff auf den Suchdienst-Endpunkt schlüsselbasierte Authentifizierung verwendet wird. Ein API-Schlüssel ist eine Zeichenfolge, die aus zufällig generierten Zahlen und Buchstaben besteht. Der Typ des Schlüssels (Administrator oder Abfrage) bestimmt die Zugriffsebene. Die Übermittlung eines gültigen Schlüssels gilt als Beleg dafür, dass die Anforderung von einer vertrauenswürdigen Entität stammt.

Es gibt zwei Ebenen des Zugriffs auf Ihren Suchdienst, die durch die folgenden API-Schlüssel aktiviert werden:

+ Administratorschlüssel (erlaubt Schreib-/Lesezugriff für [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete)-Vorgänge (Create-Read-Update-Delete, Erstellen-Lesen-Aktualisieren-Löschen) im Suchdienst)

+ Abfrageschlüssel (erlaubt den schreibgeschützten Zugriff auf die Dokumentsammlung eines Index)

*Administratorschlüssel* werden erstellt, wenn der Dienst bereitgestellt wird. Es gibt zwei Administratorschlüssel, die zur besseren Unterscheidung als *primärer* und *sekundärer* Schlüssel bezeichnet werden. In Wirklichkeit sind sie aber austauschbar. Jeder Dienst verfügt über zwei Admin-Schlüssel, sodass Sie für einen Schlüssel ein Rollover durchführen können, ohne den Zugriff auf den Dienst zu verlieren. Sie können gemäß den bewährten Methoden von Azure regelmäßig die [Administratorschlüssel neu generieren](search-security-api-keys.md#regenerate-admin-keys), aber Sie können der Gesamtanzahl der Administratorschlüssel keine weiteren hinzufügen. Pro Suchdienst können maximal zwei Administratorschlüssel vorhanden sein.

*Abfrageschlüssel* werden bei Bedarf erstellt und sind für Clientanwendungen bestimmt, die Abfragen ausgeben. Sie können bis zu 50 Abfrage-Schlüssel generieren. Im Anwendungscode geben Sie die Such-URL und einen Abfrage-API-Schlüssel ein, um den Lesezugriff auf die Dokumentsammlung eines bestimmten Index zuzulassen. Zusammen definieren der Endpunkt, ein API-Schlüssel für den Lesezugriff und ein Zielindex den Bereich und die Zugriffsebene der Verbindung von Ihrer Clientanwendung.

Für jede Anforderung – bestehend aus einem obligatorischen Schlüssel, einem Vorgang und einem Objekt – ist eine Authentifizierung erforderlich. In Kombination lässt sich durch die beiden Berechtigungsstufen (uneingeschränkte bzw. Lese-/Schreibberechtigung) und den Kontext (z.B. eine Indexabfrage) ein vollumfängliches Sicherheitsspektrum für Dienstvorgänge erreichen. Weitere Informationen finden Sie unter [Erstellen und Verwalten von API-Schlüsseln für einen Azure Search-Dienst](search-security-api-keys.md).

### <a name="ip-restricted-access"></a>IP-eingeschränkter Zugriff

Um den Zugriff auf Ihren Suchdienst weiter zu steuern, können Sie Firewall-Eingangsregeln erstellen, die nur den Zugriff auf eine bestimmte IP-Adresse oder einen Bereich von IP-Adressen zulassen. Alle Clientverbindungen müssen über eine zulässige IP-Adresse erfolgen, sonst wird die Verbindung verweigert.

:::image type="content" source="media/search-security-overview/inbound-firewall-ip-restrictions.png" alt-text="Diagramm mit Beispielarchitektur für IP-eingeschränkten Zugriff":::

Sie können das Portal zum [Konfigurieren des eingehenden Zugriffs](service-configure-firewall.md) verwenden.

Alternativ können Sie die Verwaltungs-REST-APIs verwenden. Ab API-Version 2020-03-13 können Sie mit dem [IpRule](/rest/api/searchmanagement/services/createorupdate#iprule)-Parameter den Zugriff auf Ihren Dienst einschränken, indem Sie die IP-Adressen, die Zugriff auf Ihren Dienst erhalten sollen, einzeln oder als Bereich angeben.

### <a name="private-endpoint-no-internet-traffic"></a>Privater Endpunkt (kein Internetdatenverkehr)

Ein [privater Endpunkt](../private-link/private-endpoint-overview.md) für Azure Cognitive Search ermöglicht es, dass ein Client in einem [virtuellen Netzwerk](../virtual-network/virtual-networks-overview.md) über eine [private Verbindung](../private-link/private-link-overview.md) sicher auf Daten in einem Suchindex zugreifen kann.

Der private Endpunkt verwendet eine IP-Adresse aus dem Adressraum des virtuellen Netzwerks für Verbindungen mit Ihrem Suchdienst. Der Netzwerkdatenverkehr zwischen dem Client und dem Suchdienst wird über das virtuelle Netzwerk und eine private Verbindung im Microsoft-Backbonenetzwerk geleitet, sodass keine Offenlegung im öffentlichen Internet erfolgt. Ein VNET ermöglicht eine sichere Kommunikation zwischen Ressourcen in Ihrem lokalen Netzwerk und im Internet.

:::image type="content" source="media/search-security-overview/inbound-private-link-azure-cog-search.png" alt-text="Diagramm mit Beispielarchitektur für Zugriff über privaten Endpunkt":::

Dies ist zwar die sicherste Lösung, die Verwendung zusätzlicher Dienste bedeutet aber auch zusätzliche Kosten. Sie sollten ein genaues Verständnis der Vorteile besitzen, bevor Sie sich dafür entscheiden. Weitere Informationen zu den Kosten finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/private-link/). Weitere Informationen über das Zusammenwirken dieser Komponenten sehen Sie im Video, das oben in diesem Artikel verlinkt ist. Die Option des privaten Endpunkts wird ab 5:48 m im Video behandelt. Anweisungen zum Einrichten des Endpunkts finden Sie unter [Erstellen eines privaten Endpunkts für Azure Cognitive Search](service-create-private-endpoint.md).

## <a name="index-access"></a>Indexzugriff

In der kognitiven Azure-Suche ist ein einzelner Index kein sicherungsfähiges Objekt. Stattdessen wird der Zugriff auf einen Index durch eine Kombination aus Dienstebene (Lese- oder Schreibzugriff auf den Dienst) und Vorgangskontext bestimmt.

Für den Endbenutzerzugriff können Sie Abfrageanforderungen so strukturieren, dass die Verbindungsherstellung unter Verwendung eines Abfrageschlüssels erfolgt – wodurch alle Abfragen nur schreibgeschützten Zugriff erhalten – und den spezifischen, von Ihrer App verwendeten Index einschließen. Da es in einer Abfrageanforderung kein Konzept für die Verknüpfung von Indizes oder den gleichzeitigen Zugriff auf mehrere Indizes gibt, sind alle Anforderungen definitionsgemäß auf einen einzelnen Index ausgerichtet. Folglich wird die Sicherheitsgrenze durch die Konstruktion der Abfrageanforderung selbst (Kombination aus Schlüssel und einzelnem Zielindex) definiert.

Der Indexzugriff von Administratoren und Entwicklern ist undifferenziert: Beide benötigen Schreibzugriff, um vom Dienst verwaltete Objekte erstellen, löschen und aktualisieren zu können. Ein Benutzer mit einem Administratorschlüssel für Ihren Dienst kann jeden beliebigen Index in diesem Dienst lesen, ändern und löschen. Zum Schutz vor der versehentlichen oder böswilligen Löschung von Indizes können Sie Ihre interne Quellcodeverwaltung für Coderessourcen verwenden und unerwünschte Lösch- oder Änderungsvorgänge für Indizes rückgängig machen. Zur Gewährleistung der Verfügbarkeit bietet die kognitive Azure-Suche ein clusterinternes Failover-Feature. Ihr proprietärer Code zum Erstellen oder Laden von Indizes wird jedoch nicht gespeichert oder ausgeführt.

Mehrinstanzenfähige Lösungen, die Sicherheitsgrenzen auf der Indexebene erfordern, enthalten in der Regel eine mittlere Ebene, die Kunden für die Indexisolierung verwenden. Weitere Informationen zum mehrinstanzenfähigen Anwendungsfall finden Sie unter [Entwurfsmuster für mehrinstanzenfähige SaaS-Anwendungen und kognitive Azure-Suche](search-modeling-multitenant-saas-applications.md).

## <a name="user-access"></a>Benutzerzugriff

Wie ein Benutzer auf einen Index und andere Objekte zugreift, hängt vom Typ des API-Schlüssels für die Anforderung ab. Die meisten Entwickler erstellen und weisen [*Abfrageschlüssel*](search-security-api-keys.md) für clientseitige Suchanforderungen zu. Ein Abfrageschlüssel gewährt schreibgeschützten Zugriff auf alle durchsuchbaren Inhalte innerhalb des Index.

Wenn Sie eine detaillierte Kontrolle über Suchergebnisse pro Benutzer benötigen, können Sie Sicherheitsfilter für Ihre Abfragen erstellen, sodass mit einer bestimmten Sicherheitsidentität verknüpfte Dokumente zurückgegeben werden. Anstelle vordefinierter Rollen und Rollenzuweisungen wird eine identitätsbasierte Zugriffssteuerung als *Filter* implementiert, die die Suchergebnisse von Dokumenten und Inhalten auf der Grundlage von Identitäten einschränken. In der folgenden Tabelle werden zwei Ansätze zur Einschränkung von Suchergebnissen für nicht autorisierte Inhalte beschrieben.

| Vorgehensweise | BESCHREIBUNG |
|----------|-------------|
|[Einschränkung aus Sicherheitsgründen auf der Grundlage von Identitätsfiltern](search-security-trimming-for-azure-search.md)  | Beschreibt den grundlegenden Workflow für die Implementierung der Zugriffssteuerung auf der Grundlage der Benutzeridentität. Der Artikel behandelt das Hinzufügen von Sicherheitsbezeichnern zu einem Index sowie das Filtern nach dem entsprechenden Feld, um unzulässige Inhalte aus den Ergebnissen zu entfernen. |
|[Einschränkung aus Sicherheitsgründen auf der Grundlage von Azure Active Directory-Identitäten](search-security-trimming-for-azure-search-with-aad.md)  | Dieser Artikel baut auf dem vorherigen Artikel auf und enthält Schritte zum Abrufen von Identitäten aus Azure Active Directory (Azure AD) – einem der [kostenlosen Dienste](https://azure.microsoft.com/free/) der Azure-Cloudplattform. |

## <a name="administrative-rights"></a>Administratorrechte

Der [Rollenbasierte Zugriff in Azure (Azure RBAC)](../role-based-access-control/overview.md) ist ein Autorisierungssystem für die Bereitstellung von Azure-Ressourcen, das auf [Azure Resource Manager](../azure-resource-manager/management/overview.md) basiert. In Azure Cognitive Search wird der Resource Manager zum Erstellen oder Löschen des Diensts, zum Verwalten der API-Schlüssel und zum Skalieren des Diensts verwendet. Daher bestimmen die Azure-Rollenzuweisungen, wer diese Aufgaben ausführen kann, unabhängig davon, ob dazu das [Portal](search-manage.md), [PowerShell](search-manage-powershell.md) oder die [Verwaltungs-REST-APIs](/rest/api/searchmanagement/search-howto-management-rest-api) verwendet werden.

Im Gegensatz dazu werden Administratorrechte für Inhalte, die vom Dienst gehostet werden, z. B. die Möglichkeit zum Erstellen oder Löschen eines Indexes, über API-Schlüssel übertragen, wie im [vorherigen Abschnitt](#index-access) beschrieben.

> [!TIP]
> Mithilfe von Azure-weit gültigen Mechanismen können Sie ein Abonnement oder eine Ressource sperren, um die versehentliche oder nicht autorisierte Löschung Ihres Suchdiensts durch Benutzer mit Administratorrechten zu verhindern. Weitere Informationen finden Sie unter [Sperren von Ressourcen zum Verhindern der unerwarteten Löschung](../azure-resource-manager/management/lock-resources.md).

## <a name="certifications-and-compliance"></a>Zertifizierungen und Compliance

Azure Cognitive Search wurde als konform mit mehreren globalen, regionalen und branchenspezifischen Standards sowohl für die öffentliche Cloud als auch für Azure Government zertifiziert. Um die vollständige Liste zu erhalten, laden Sie das Whitepaper [**Microsoft Azure-Complianceangebote**](https://azure.microsoft.com/resources/microsoft-azure-compliance-offerings/) von der offiziellen Seite mit Überwachungsberichten herunter.

Aus Konformitätsgründen können Sie [Azure Policy](../governance/policy/overview.md) verwenden, um die bewährten Methoden für höchste Sicherheit des [Azure-Sicherheitsvergleichstests](../security/benchmarks/introduction.md) zu implementieren. Der Azure-Sicherheitsvergleichstest ist eine Sammlung von Sicherheitsempfehlungen, die in Sicherheitskontrollen programmiert sind. Diese sind den wichtigsten Maßnahmen zugeordnet, die Sie zur Verringerung von Bedrohungen für Dienste und Daten ergreifen sollten. Derzeit gibt es elf Sicherheitskontrollen, darunter [Netzwerksicherheit](../security/benchmarks/security-control-network-security.md), [Protokollierung und Überwachung](../security/benchmarks/security-control-logging-monitoring.md) und [Datenschutz](../security/benchmarks/security-control-data-protection.md), um nur einige zu nennen.

Azure Policy ist eine in Azure integrierte Funktion, mit der Sie die Konformität für mehrere Standards, einschließlich denen des Azure-Sicherheitsvergleichstests, verwalten können. Azure Policy bietet für gängige Bezugsgrößen integrierte Definitionen, die sowohl Kriterien als auch eine umsetzbare Reaktion bei Nichteinhaltung liefern.

Für Azure Cognitive Search gibt es derzeit eine integrierte Definition. Diese ist für die Diagnoseprotokollierung. Mit dieser integrierten Definition können Sie eine Richtlinie zuweisen, die jeden Suchdienst identifiziert, für den die Diagnoseprotokollierung fehlt, und diesen dann aktiviert. Weitere Informationen erhalten Sie unter [Kontrollen zur Einhaltung gesetzlicher Bestimmungen in Azure Policy für Azure Cognitive Search](security-controls-policy.md).

## <a name="see-also"></a>Weitere Informationen

+ [Grundlegende Azure-Sicherheitsinformationen](../security/fundamentals/index.yml)
+ [Sicherheit von Azure](https://azure.microsoft.com/overview/security)
+ [Azure Security Center](../security-center/index.yml)
