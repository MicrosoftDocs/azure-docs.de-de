---
title: Sicherheitsübersicht
titleSuffix: Azure Cognitive Search
description: Informieren Sie sich über die Sicherheitsfeatures in Azure Cognitive Search zum Schutz von Endpunkten, Inhalten und Vorgängen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/04/2021
ms.custom: references_regions
ms.openlocfilehash: 46f2035e5f8409cd38faeb9c327b88b06fc7d7a0
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100097635"
---
# <a name="security-overview-for-azure-cognitive-search"></a>Übersicht über die Sicherheit für Azure Cognitive Search

In diesem Artikel werden die Sicherheitsfeatures in Azure Cognitive Search beschrieben, mit denen Inhalte und Vorgänge geschützt werden.

Für eingehende Anforderungen an einen Suchdienst gibt es eine Folge von Maßnahmen mit steigendem Sicherheitsniveau zum Schutz des Endpunkts des Suchdiensts: von API-Schlüsseln in der Anforderung über Eingangsregeln in der Firewall bis zu privaten Endpunkten, die Ihren Dienst vollständig vom öffentlichen Internet abschirmen.

Bei ausgehenden Anforderungen an andere Dienste überwiegen die Anforderungen von Indexern, die Inhalte aus externen Quellen lesen. Sie können Anmeldeinformationen in der Verbindungszeichenfolge angeben. Ober Sie können eine verwaltete Identität einrichten, um die Suche zu einem vertrauenswürdigen Dienst zu machen, wenn auf Daten aus Azure Storage, Azure SQL, Cosmos DB oder anderen Azure-Datenquellen zugegriffen wird. Eine verwaltete Identität stellt einen Ersatz für Anmeldeinformationen oder Zugriffsschlüssel für die Verbindung dar. Weitere Informationen zu dieser Funktion finden Sie unter [Herstellen einer Verbindung mit einer Datenquelle mithilfe einer verwalteten Identität](search-howto-managed-identities-data-sources.md).

Es gibt nur wenige Schreibvorgänge in externe Dienste: Ein Suchdienst schreibt in Protokolldateien, sowie beim Erstellen von Wissensspeichern, persistierenden zwischengespeicherten Anreicherungen und Debugsitzungen schreibt er in Azure Storage. Andere Dienst-zu-Dienst-Aufrufe, wie z. B. Cognitive Services, werden über das interne Netzwerk getätigt.

Eine Übersicht der Sicherheitsarchitektur und der einzelnen Featurekategorien finden Sie in diesem temporeichen Video.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Azure-Cognitive-Search-Whats-new-in-security/player]

## <a name="network-security"></a>Netzwerksicherheit

<a name="service-access-and-authentication"></a>

Features der Eingangssicherheit schützen den Suchdienst-Endpunkt mithilfe ansteigender Sicherheits- und Komplexitätsstufen. Zunächst ist für alle Anforderungen ein API-Schlüssel für den authentifizierten Zugriff erforderlich. Zweitens können Sie optional Firewallregeln festlegen, die den Zugriff auf bestimmte IP-Adressen beschränken. Für erweiterten Schutz kann als dritte Option Azure Private Link aktiviert werden, um Ihren Dienstendpunkt von jeglichem Internetdatenverkehr abzuschirmen.

### <a name="public-access-using-api-keys"></a>Öffentlicher Zugriff mithilfe von API-Schlüsseln

Standardmäßig erfolgt der Zugriff auf einen Suchdienst über die Public Cloud, wobei für den Administrator- oder Abfragezugriff auf den Suchdienst-Endpunkt schlüsselbasierte Authentifizierung verwendet wird. Die Übermittlung eines gültigen Schlüssels gilt als Beleg dafür, dass die Anforderung von einer vertrauenswürdigen Entität stammt. Die schlüsselbasierte Authentifizierung wird im nächsten Abschnitt behandelt.

### <a name="configure-ip-firewalls"></a>Konfigurieren von IP-Firewalls

Um den Zugriff auf Ihren Suchdienst weiter zu steuern, können Sie Firewall-Eingangsregeln erstellen, die nur den Zugriff auf eine bestimmte IP-Adresse oder einen Bereich von IP-Adressen zulassen. Alle Clientverbindungen müssen über eine zulässige IP-Adresse erfolgen, sonst wird die Verbindung verweigert.

:::image type="content" source="media/search-security-overview/inbound-firewall-ip-restrictions.png" alt-text="Diagramm mit Beispielarchitektur für IP-eingeschränkten Zugriff":::

Sie können das Portal zum [Konfigurieren des eingehenden Zugriffs](service-configure-firewall.md) verwenden.

Alternativ können Sie die Verwaltungs-REST-APIs verwenden. Ab API-Version 2020-03-13 können Sie mit dem [IpRule](/rest/api/searchmanagement/services/createorupdate#iprule)-Parameter den Zugriff auf Ihren Dienst einschränken, indem Sie die IP-Adressen, die Zugriff auf Ihren Dienst erhalten sollen, einzeln oder als Bereich angeben.

### <a name="network-isolation-through-a-private-endpoint-no-internet-traffic"></a>Netzwerkisolation über einen privaten Endpunkt (kein Internetdatenverkehr)

Sie können einen [privaten Endpunkt](../private-link/private-endpoint-overview.md) für Azure Cognitive Search einrichten, der ermöglicht, dass ein Client in einem [virtuellen Netzwerk](../virtual-network/virtual-networks-overview.md) über eine [private Verbindung](../private-link/private-link-overview.md) sicher auf Daten in einem Suchindex zugreifen kann.

Der private Endpunkt verwendet eine IP-Adresse aus dem Adressraum des virtuellen Netzwerks für Verbindungen mit Ihrem Suchdienst. Der Netzwerkdatenverkehr zwischen dem Client und dem Suchdienst wird über das virtuelle Netzwerk und eine private Verbindung im Microsoft-Backbonenetzwerk geleitet, sodass keine Offenlegung im öffentlichen Internet erfolgt. Ein VNET ermöglicht eine sichere Kommunikation zwischen Ressourcen in Ihrem lokalen Netzwerk und im Internet.

:::image type="content" source="media/search-security-overview/inbound-private-link-azure-cog-search.png" alt-text="Diagramm mit Beispielarchitektur für Zugriff über privaten Endpunkt":::

Dies ist zwar die sicherste Lösung, die Verwendung zusätzlicher Dienste bedeutet aber auch zusätzliche Kosten. Sie sollten ein genaues Verständnis der Vorteile besitzen, bevor Sie sich dafür entscheiden. Weitere Informationen zu den Kosten finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/private-link/). Weitere Informationen über das Zusammenwirken dieser Komponenten sehen Sie im Video, das oben in diesem Artikel verlinkt ist. Die Option des privaten Endpunkts wird ab 5:48 m im Video behandelt. Anweisungen zum Einrichten des Endpunkts finden Sie unter [Erstellen eines privaten Endpunkts für Azure Cognitive Search](service-create-private-endpoint.md).

## <a name="authentication"></a>Authentifizierung

Bei eingehenden Anforderungen an den Suchdienst erfolgt die Authentifizierung über einen [obligatorischen API-Schlüssel](search-security-api-keys.md) (eine Zeichenfolge aus nach dem Zufallsprinzip generierten Ziffern und Buchstaben), der beweist, dass die Anforderung von einer vertrauenswürdigen Quelle stammt. Cognitive Search unterstützt derzeit keine Azure Active Directory-Authentifizierung für eingehende Anforderungen.

Von einem Indexer gestellte ausgehende Anforderungen unterliegen der Authentifizierung durch den externen Dienst. Der Indexer-Subdienst in Cognitive Search kann zu einem vertrauenswürdigen Dienst auf Azure gemacht werden, der sich über eine verwaltete Identität mit anderen Diensten verbindet. Weitere Informationen finden Sie unter [Einrichten einer Indexerverbindung mit einer Datenquelle mithilfe einer verwalteten Identität](search-howto-managed-identities-data-sources.md).

## <a name="authorization"></a>Authorization

Cognitive Search bietet verschiedene Autorisierungsmodelle für das Inhaltsverwaltung und Dienstverwaltung. 

### <a name="authorization-for-content-management"></a>Autorisierung für die Inhaltsverwaltung

Die Autorisierung für Inhalte und Vorgänge im Zusammenhang mit Inhalten ist entweder Schreibzugriff, wie er durch den bei der Anforderung angegebenen [API-Schlüssel](search-security-api-keys.md) gewährt wird. Der API-Schlüssel ist ein Authentifizierungsmechanismus, autorisiert aber auch den Zugriff abhängig vom Typ des API-Schlüssels.

+ Administratorschlüssel (erlaubt Lese-/Schreibzugriff für CRUD-Vorgänge (Create-Read-Update-Delete, Erstellen-Lesen-Aktualisieren-Löschen) im Suchdienst), erstellt bei Bereitstellung des Diensts

+ Abfrageschlüssel (ermöglicht den schreibgeschützten Zugriff auf die Dokumentsammlung eines Indexes), erstellt bei Bedarf und entworfen für Clientanwendungen, die Abfragen ausgeben

Im Anwendungscode geben Sie den Endpunkt und einen API-Schlüssel an, um den Zugriff auf Inhalte und Optionen zu ermöglichen. Ein Endpunkt kann der Dienst selbst, die Indexsammlung, ein bestimmter Index, eine Dokumentsammlung oder ein bestimmtes Dokument sein. Verkettet bilden der Endpunkt, der Vorgang (z. B. eine Erstellungs- oder Aktualisierungsanforderung) und die Berechtigungsstufe (vollständige oder Nur-Lese-Rechte basierend auf dem Schlüssel) die Sicherheitsformel, die Inhalte und Vorgänge schützt.

### <a name="controlling-access-to-indexes"></a>Steuern des Zugriffs auf Indizes

In der kognitiven Azure-Suche ist ein einzelner Index kein sicherungsfähiges Objekt. Stattdessen wird der Zugriff auf einen Index durch eine Kombination aus Dienstebene (Lese- oder Schreibzugriff, basierend auf dem bereitgestellten API-Schlüssel) und Vorgangskontext bestimmt.

Für schreibgeschützten Zugriff können Sie Abfrageanforderungen so strukturieren, dass die Verbindungsherstellung unter Verwendung eines [Abfrageschlüssels](search-security-rbac.md) erfolgt, und den spezifischen von Ihrer App verwendeten Index einschließen. Da es in einer Abfrageanforderung kein Konzept für die Verknüpfung von Indizes oder den gleichzeitigen Zugriff auf mehrere Indizes gibt, sind alle Anforderungen definitionsgemäß auf einen einzelnen Index ausgerichtet. Folglich wird die Sicherheitsgrenze durch die Konstruktion der Abfrageanforderung selbst (Kombination aus Schlüssel und einzelnem Zielindex) definiert.

Der Indexzugriff von Administratoren und Entwicklern ist undifferenziert: Beide benötigen Schreibzugriff, um vom Dienst verwaltete Objekte erstellen, löschen und aktualisieren zu können. Jeder Benutzer mit einem [Administratorschlüssel](search-security-rbac.md) für Ihren Dienst kann jeden beliebigen Index in diesem Dienst lesen, ändern und löschen. Zum Schutz vor der versehentlichen oder böswilligen Löschung von Indizes können Sie Ihre interne Quellcodeverwaltung für Coderessourcen verwenden und unerwünschte Lösch- oder Änderungsvorgänge für Indizes rückgängig machen. Zur Gewährleistung der Verfügbarkeit bietet die kognitive Azure-Suche ein clusterinternes Failover-Feature. Ihr proprietärer Code zum Erstellen oder Laden von Indizes wird jedoch nicht gespeichert oder ausgeführt.

Mehrinstanzenfähige Lösungen, die Sicherheitsgrenzen auf der Indexebene erfordern, enthalten in der Regel eine mittlere Ebene, die Kunden für die Indexisolierung verwenden. Weitere Informationen zum mehrinstanzenfähigen Anwendungsfall finden Sie unter [Entwurfsmuster für mehrinstanzenfähige SaaS-Anwendungen und kognitive Azure-Suche](search-modeling-multitenant-saas-applications.md).

### <a name="controlling-access-to-documents"></a>Steuern des Zugriffs auf Dokumente

Wenn Sie eine detaillierte Kontrolle über Suchergebnisse pro Benutzer benötigen, können Sie Sicherheitsfilter für Ihre Abfragen erstellen, sodass mit einer bestimmten Sicherheitsidentität verknüpfte Dokumente zurückgegeben werden. 

Die Autorisierung für Inhalt innerhalb des Indexes entspricht konzeptionell der „Sicherheit auf Zeilenebene“. und wird nicht nativ durch vordefinierte Rollen oder Rollenzuweisungen unterstützt, die Entitäten in Azure Active Directory zugeordnet sind. Alle Benutzerberechtigungen für Daten in externen Systemen, wie z. B. Cosmos DB, werden nicht mit diesen Daten übertragen, da sie von Cognitive Search indiziert werden.

Problemumgehungen für Lösungen, die „Sicherheit auf Zeilenebene“ erfordern, sind beispielsweise die Erstellung eines Felds in der Datenquelle, das eine Sicherheitsgruppe oder Benutzeridentität repräsentiert, und die anschließende Verwendung von Filtern in Cognitive Search, um die Dokumente und Inhalte in den Suchergebnissen basierend auf Identitäten selektiv einzuschränken. In der folgenden Tabelle werden zwei Ansätze zur Einschränkung von Suchergebnissen für nicht autorisierte Inhalte beschrieben.

| Vorgehensweise | BESCHREIBUNG |
|----------|-------------|
|[Einschränkung aus Sicherheitsgründen auf der Grundlage von Identitätsfiltern](search-security-trimming-for-azure-search.md)  | Beschreibt den grundlegenden Workflow für die Implementierung der Zugriffssteuerung auf der Grundlage der Benutzeridentität. Der Artikel behandelt das Hinzufügen von Sicherheitsbezeichnern zu einem Index sowie das Filtern nach dem entsprechenden Feld, um unzulässige Inhalte aus den Ergebnissen zu entfernen. |
|[Einschränkung aus Sicherheitsgründen auf der Grundlage von Azure Active Directory-Identitäten](search-security-trimming-for-azure-search-with-aad.md)  | Dieser Artikel baut auf dem vorherigen Artikel auf und enthält Schritte zum Abrufen von Identitäten aus Azure Active Directory (Azure AD) – einem der [kostenlosen Dienste](https://azure.microsoft.com/free/) der Azure-Cloudplattform. |

### <a name="authorization-for-service-management"></a>Autorisierung für die Dienstverwaltung

Dienstverwaltungsvorgänge werden über die [rollenbasierte Zugriffskontrolle von Azure (Azure RBAC)](../role-based-access-control/overview.md) autorisiert. Azure RBAC ist ein Autorisierungssystem für die Bereitstellung von Azure-Ressourcen, das auf [Azure Resource Manager](../azure-resource-manager/management/overview.md) basiert. 

In Azure Cognitive Search wird der Resource Manager zum Erstellen oder Löschen des Diensts, zum Verwalten der API-Schlüssel und zum Skalieren des Diensts verwendet. Daher bestimmen die Azure-Rollenzuweisungen, wer diese Aufgaben ausführen kann, unabhängig davon, ob dazu das [Portal](search-manage.md), [PowerShell](search-manage-powershell.md) oder die [Verwaltungs-REST-APIs](/rest/api/searchmanagement/search-howto-management-rest-api) verwendet werden.

Für Suchdienstadministratoren sind [drei grundlegende Rollen](search-security-rbac.md#management-tasks-by-role) definiert. Die Rollenzuweisungen können mit jeder unterstützten Methodik (Portal, PowerShell usw.) vorgenommen werden und werden dienstübergreifend beachtet. Mit den Rollen „Besitzer“ und „Mitwirkender“ können verschiedene Verwaltungsfunktionen durchgeführt werden. Sie können die Rolle „Leser“ Benutzern zuweisen, die wichtige Informationen anzeigen können sollen.

> [!Note]
> Mithilfe von Azure-weit gültigen Mechanismen können Sie ein Abonnement oder eine Ressource sperren, um die versehentliche oder nicht autorisierte Löschung Ihres Suchdiensts durch Benutzer mit Administratorrechten zu verhindern. Weitere Informationen finden Sie unter [Sperren von Ressourcen zum Verhindern der unerwarteten Löschung](../azure-resource-manager/management/lock-resources.md).

<a name="encryption"></a>

## <a name="data-protection"></a>Datenschutz

Auf Speicherebene ist die Datenverschlüsselung für alle vom Dienst verwalteten Inhalte, die auf Datenträgern gespeichert werden, integriert: Indizes, Synonymzuordnungen und die Definitionen von Indexern, Datenquellen und Skillsets. Optional können Sie kundenseitig verwaltete Schlüssel (Customer-Managed Keys CMK) zur zusätzlichen Verschlüsselung indizierter Inhalte hinzufügen. Bei Diensten, die nach dem 1. August 2020 erstellt wurden, erstreckt sich die CMK-Verschlüsselung auch auf Daten auf temporären Datenträgern, um eine vollständige doppelte Verschlüsselung der indexierten Inhalte zu erreichen.

### <a name="data-in-transit"></a>Daten während der Übertragung

Die Verschlüsselung in Azure Cognitive Search beginnt bei Verbindungen und Übertragungen und reicht bis zu den auf Datenträgern gespeicherten Inhalten. Für Suchdienste im öffentlichen Internet lauscht Azure Cognitive Search an HTTPS-Port 443. Alle Client-zu-Dienst-Verbindungen verwenden TLS 1.2-Verschlüsselung. Frühere Versionen (1.0 oder 1.1) werden nicht unterstützt.

### <a name="encrypted-data-at-rest"></a>Verschlüsselte ruhende Daten

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

## <a name="security-management"></a>Sicherheitsverwaltung

### <a name="api-keys"></a>API-Schlüssel

Die Abhängigkeit von der auf API-Schlüsseln basierenden Authentifizierung bedeutet, dass Sie über einen Plan für die regelmäßige erneute Generierung des Administratorschlüssels gemäß den bewährten Azure-Sicherheitsmethoden verfügen sollten. Pro Suchdienst können maximal zwei Administratorschlüssel vorhanden sein. Weitere Informationen zum Sichern und Verwalten von API-Schlüsseln finden Sie unter [Erstellen und Verwalten von API-Schlüsseln](search-security-api-keys.md).

#### <a name="activity-and-diagnostic-logs"></a>Aktivitäts- und Diagnoseprotokolle

Cognitive Search protokolliert keine Benutzeridentitäten, sodass Sie sich nicht auf Protokolle beziehen können, um Informationen über einen bestimmten Benutzer zu erhalten. Der Dienst protokolliert jedoch CRUD-Vorgänge (Create-Read-Update-Delete, Erstellen-Lesen-Aktualisieren-Löschen), die Sie möglicherweise mit anderen Protokollen korrelieren können, um die Wirkung bestimmter Aktionen zu verstehen.

Mithilfe von Warnmeldungen und der Protokollierungsinfrastruktur in Azure können Sie Spitzen im Abfragevolumen oder andere Aktionen, die von den erwarteten Workloads abweichen, ermitteln. Weitere Informationen zum Einrichten von Protokollen finden Sie unter [Sammeln und Analysieren von Protokolldaten](search-monitor-logs.md) sowie [Überwachen von Abfrageanforderungen](search-monitor-queries.md).

### <a name="certifications-and-compliance"></a>Zertifizierungen und Compliance

Azure Cognitive Search nimmt an regelmäßigen Überprüfungen teil und wurde in Bezug auf mehrere globale, regionale und branchenspezifische Standards sowohl für die öffentliche Cloud als auch für Azure Government zertifiziert. Um die vollständige Liste zu erhalten, laden Sie das Whitepaper [**Microsoft Azure-Complianceangebote**](https://azure.microsoft.com/resources/microsoft-azure-compliance-offerings/) von der offiziellen Seite mit Überwachungsberichten herunter.

Aus Konformitätsgründen können Sie [Azure Policy](../governance/policy/overview.md) verwenden, um die bewährten Methoden für höchste Sicherheit des [Azure-Sicherheitsvergleichstests](../security/benchmarks/introduction.md) zu implementieren. Der Azure-Sicherheitsvergleichstest ist eine Sammlung von Sicherheitsempfehlungen, die in Sicherheitskontrollen programmiert sind. Diese sind den wichtigsten Maßnahmen zugeordnet, die Sie zur Verringerung von Bedrohungen für Dienste und Daten ergreifen sollten. Derzeit gibt es elf Sicherheitskontrollen, darunter [Netzwerksicherheit](../security/benchmarks/security-control-network-security.md), [Protokollierung und Überwachung](../security/benchmarks/security-control-logging-monitoring.md) und [Datenschutz](../security/benchmarks/security-control-data-protection.md), um nur einige zu nennen.

Azure Policy ist eine in Azure integrierte Funktion, mit der Sie die Konformität für mehrere Standards, einschließlich denen des Azure-Sicherheitsvergleichstests, verwalten können. Azure Policy bietet für gängige Bezugsgrößen integrierte Definitionen, die sowohl Kriterien als auch eine umsetzbare Reaktion bei Nichteinhaltung liefern.

Für Azure Cognitive Search gibt es derzeit eine integrierte Definition. Diese ist für die Diagnoseprotokollierung. Mit dieser integrierten Definition können Sie eine Richtlinie zuweisen, die jeden Suchdienst identifiziert, für den die Diagnoseprotokollierung fehlt, und diesen dann aktiviert. Weitere Informationen erhalten Sie unter [Kontrollen zur Einhaltung gesetzlicher Bestimmungen in Azure Policy für Azure Cognitive Search](security-controls-policy.md).

## <a name="see-also"></a>Weitere Informationen

+ [Grundlegende Azure-Sicherheitsinformationen](../security/fundamentals/index.yml)
+ [Sicherheit von Azure](https://azure.microsoft.com/overview/security)
+ [Azure Security Center](../security-center/index.yml)