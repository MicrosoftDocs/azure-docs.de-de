---
title: Sicherheit und Datenschutz – Azure Search
description: Azure Search ist kompatibel mit SOC 2, HIPAA und anderen Zertifizierungen. Verbindungs- und Datenverschlüsselung, Authentifizierung und Identitätszugriff durch Benutzer- und Gruppensicherheits-IDs in Azure Search-Filtern.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: f366726f539a817f515a78fbc35bfeaa3b65514e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65024495"
---
# <a name="security-and-data-privacy-in-azure-search"></a>Sicherheit und Datenschutz in Azure Search

Umfassende Sicherheitsfunktionen und Zugriffssteuerungen sind in Azure Search integriert, um sicherzustellen, dass private Inhalte geschützt werden. In diesem Artikel werden die in Azure Search integrierten Sicherheitsfeatures und Standardskonformität aufgelistet.

Die Sicherheitsarchitektur von Azure Search umfasst physische Sicherheit, verschlüsselte Übertragung, verschlüsselte Speicherung und plattformweite Standardskonformität. Im laufenden Betrieb akzeptiert Azure Search ausschließlich authentifizierte Anforderungen. Optional kann eine benutzerspezifische Zugriffssteuerung über Sicherheitsfilter für Inhalte hinzugefügt werden. Dieser Artikel geht zwar auf die einzelnen Sicherheitsebenen ein, beschäftigt sich aber hauptsächlich mit dem Schutz von Daten und Vorgängen in Azure Search.

## <a name="standards-compliance-iso-27001-soc-2-hipaa"></a>Einhaltung von Standards: ISO 27001, SOC 2, HIPAA

Azure Search ist wie [im Juni 2018 angekündigt](https://azure.microsoft.com/blog/azure-search-is-now-certified-for-several-levels-of-compliance/) für die folgenden Standards zertifiziert:

+ [ISO 27001:2013](https://www.iso.org/isoiec-27001-information-security.html) 
+ [Konformität mit SOC 2 Type 2](https://www.aicpa.org/interestareas/frc/assuranceadvisoryservices/aicpasoc2report.html) Den vollständigen Bericht finden Sie im [SOC 2 Type II-Bericht für Azure und Azure Government](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuide?command=Download&downloadType=Document&downloadId=93292f19-f43e-4c4e-8615-c38ab953cf95&docTab=4ce99610-c9c0-11e7-8c2c-f908a777fa4d_SOC%20%2F%20SSAE%2016%20Reports). 
+ [Health Insurance Portability and Accountability Act (HIPAA)](https://en.wikipedia.org/wiki/Health_Insurance_Portability_and_Accountability_Act)
+ [GxP (21 CFR Teil 11)](https://en.wikipedia.org/wiki/Title_21_CFR_Part_11)
+ [HITRUST](https://en.wikipedia.org/wiki/HITRUST)
+ [PCI-DSS Level 1](https://en.wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard)
+ [Australia IRAP Unclassified DLM](https://asd.gov.au/infosec/irap/certified_clouds.htm)

Die Standardkonformität gilt für allgemein verfügbare Features. Previewfunktionen werden beim Übergang in die allgemeine Verfügbarkeit zertifiziert und dürfen nicht in Lösungen, für die bestimmte Standardanforderungen erfüllt werden müssen, verwendet werden. Die Konformitätszertifizierung ist in [Übersicht der Microsoft Azure-Konformität](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) und [Trust Center](https://www.microsoft.com/en-us/trustcenter) dokumentiert. 

## <a name="encrypted-transmission-and-storage"></a>Verschlüsselte Übertragung und Speicherung

Die Verschlüsselung erstreckt sich auf die gesamte Indexpipeline – von Verbindungen über die Übertragung bis hin zu indizierten in Azure Search gespeicherten Daten.

| Sicherheitsebene | BESCHREIBUNG |
|----------------|-------------|
| Verschlüsselung während der Übertragung <br>(HTTPS/SSL/TLS) | Azure Search lauscht an HTTPS-Port 443. Verbindungen mit Azure-Diensten sind plattformweit verschlüsselt. <br/><br/>Alle Azure Search-Interaktionen zwischen Client und Dienst erfolgen über SSL/TLS 1.2.  Achten Sie darauf, TLS v1.2 für SSL-Verbindungen mit Ihrem Dienst zu verwenden.|
| Verschlüsselung ruhender Daten <br>Von Microsoft verwaltete Schlüssel | Die Verschlüsselung ist vollständig in den Indizierungsprozess integriert, ohne messbare Auswirkungen auf die Durchführungsdauer der Indizierung oder die Indexgröße. Sie wird automatisch auf die gesamte Indizierung angewendet, einschließlich auf inkrementelle Updates für einen nicht vollständig verschlüsselten Index (vor Januar 2018 erstellt).<br><br>Intern basiert die Verschlüsselung auf der [256-Bit-AES-Verschlüsselung](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) von [Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).<br><br> Die Verschlüsselung ist in Azure Search integriert, wobei Zertifikate und Schlüssel intern von Microsoft verwaltet und universell angewendet werden. Sie können die Verschlüsselung nicht aktivieren bzw. deaktivieren, eigene Schlüssel verwalten oder ersetzen oder Verschlüsselungseinstellungen im Portal oder programmgesteuert anzeigen.<br><br>Die Verschlüsselung ruhender Daten wurde am 24. Januar 2018 angekündigt und gilt für alle Dienstebenen, einschließlich gemeinsamer Dienste (Free), in sämtlichen Regionen. Damit die Verschlüsselung vollständig angewendet werden kann, müssen vor diesem Datum erstellte Indizes gelöscht und neu erstellt werden. Anderenfalls werden nur neue Daten, die nach dem 24. Januar hinzugefügt wurden, verschlüsselt.|
| Verschlüsselung ruhender Daten <br>Vom Kunden verwaltete Schlüssel | Die Verschlüsselung mit von Kunden verwalteten Schlüsseln ist eine **Previewfunktion**, die für kostenlose Dienste nicht verfügbar ist. Für kostenpflichtige Dienste steht sie nur für Suchdienste zur Verfügung, die im oder nach Januar 2019 mit der neuesten API-Vorschauversion (api-version=2019-05-06-Preview) erstellt wurden.<br><br>Indizes und Synonymzuordnungen von Azure Search können nun im Ruhezustand mit von Kunden verwalteten Schlüsseln in Azure Key Vault verschlüsselt werden. Weitere Informationen finden Sie unter [Verwalten von Verschlüsselungsschlüsseln in Azure Search](search-security-manage-encryption-keys.md).<br>Diese Funktion ersetzt die Standardverschlüsselung ruhender Daten nicht, sondern wird zusätzlich angewandt.<br>Durch Aktivieren dieser Funktion wird die Indexgröße erhöht und die Abfrageleistung beeinträchtigt. Basierend auf den bisherigen Beobachtungen können Sie mit einem Anstieg der Abfragezeiten um 30 %–60 % rechnen, wobei die tatsächliche Leistung je nach Indexdefinition und Art der Abfragen variiert. Aufgrund dieser Auswirkungen auf die Leistung wird empfohlen, diese Funktion nur für Indizes zu aktivieren, für die sie wirklich erforderlich ist.

## <a name="azure-wide-user-access-controls"></a>Azure-weite Benutzerzugriffssteuerungen

Für die gesamte Azure-Umgebung sind verschiedene Sicherheitsmechanismen verfügbar – und so automatisch auch für die von Ihnen erstellten Azure Search-Ressourcen.

+ [Sperren auf Abonnement- oder Ressourcenebene zur Verhinderung von Löschvorgängen](../azure-resource-manager/resource-group-lock-resources.md)
+ [Rollenbasierte Zugriffssteuerung (Role-based Access Control, RBAC) zur Steuerung des Zugriffs auf Informationen und Verwaltungsvorgänge](../role-based-access-control/overview.md)

Alle Azure-Dienste unterstützen die rollenbasierte Zugriffssteuerung (Role-based Access Control, RBAC) zum einheitlichen Festlegen von Zugriffsebenen für alle Dienste. So ist beispielsweise das Anzeigen sensibler Daten (etwa des Administratorschlüssels) auf die Rollen „Besitzer“ und „Mitwirkender“ beschränkt, der Dienststatus kann dagegen von Mitgliedern aller Rollen angezeigt werden. RBAC bietet die Rollen „Besitzer“, „Mitwirkenden“ und „Leser“. Administratoren sind standardmäßig Mitglieder der Rolle "Besitzer".

<a name="service-access-and-authentication"></a>

## <a name="service-access-and-authentication"></a>Dienstzugriff und -authentifizierung

Azure Search erbt nicht nur die Sicherheitsmaßnahmen der Azure-Plattform, sondern bietet auch eine eigene schlüsselbasierte Authentifizierung. Ein API-Schlüssel ist eine Zeichenfolge, die aus zufällig generierten Zahlen und Buchstaben besteht. Der Typ des Schlüssels (Administrator oder Abfrage) bestimmt die Zugriffsebene. Die Übermittlung eines gültigen Schlüssels gilt als Beleg dafür, dass die Anforderung von einer vertrauenswürdigen Entität stammt. 

Es gibt zwei Ebenen des Zugriffs auf Ihren Suchdienst, die durch zwei Arten von Schlüsseln aktiviert werden:

* Administratorzugriff (gültig für alle Lese-/Schreibvorgänge für den Dienst)
* Abfragezugriff (gültig für Lesevorgänge, z. B. Abfragen der Dokumentsammlung eines Index)

*Administratorschlüssel* werden erstellt, wenn der Dienst bereitgestellt wird. Es gibt zwei Administratorschlüssel, die zur besseren Unterscheidung als *primärer* und *sekundärer* Schlüssel bezeichnet werden. In Wirklichkeit sind sie aber austauschbar. Jeder Dienst verfügt über zwei Admin-Schlüssel, sodass Sie für einen Schlüssel ein Rollover durchführen können, ohne den Zugriff auf den Dienst zu verlieren. Sie können gemäß den bewährten Methoden von Azure regelmäßig die [Administratorschlüssel neu generieren](search-security-api-keys.md#regenerate-admin-keys), aber Sie können der Gesamtanzahl der Administratorschlüssel keine weiteren hinzufügen. Pro Suchdienst können maximal zwei Administratorschlüssel vorhanden sein.

*Abfrageschlüssel* werden bei Bedarf erstellt und sind für Clientanwendungen bestimmt, die Abfragen ausgeben. Sie können bis zu 50 Abfrage-Schlüssel generieren. Im Anwendungscode geben Sie die Such-URL und einen Abfrage-API-Schlüssel ein, um den Lesezugriff auf die Dokumentsammlung eines bestimmten Index zuzulassen. Zusammen definieren der Endpunkt, ein API-Schlüssel für den Lesezugriff und ein Zielindex den Bereich und die Zugriffsebene der Verbindung von Ihrer Clientanwendung.

Für jede Anforderung – bestehend aus einem obligatorischen Schlüssel, einem Vorgang und einem Objekt – ist eine Authentifizierung erforderlich. In Kombination lässt sich durch die beiden Berechtigungsstufen (uneingeschränkte bzw. Lese-/Schreibberechtigung) und den Kontext (z.B. eine Indexabfrage) ein vollumfängliches Sicherheitsspektrum für Dienstvorgänge erreichen. Weitere Informationen finden Sie unter [Erstellen und Verwalten von API-Schlüsseln für einen Azure Search-Dienst](search-security-api-keys.md).

## <a name="index-access"></a>Indexzugriff

In Azure Search ist ein einzelner Index kein sicherungsfähiges Objekt. Stattdessen wird der Zugriff auf einen Index durch eine Kombination aus Dienstebene (Lese- oder Schreibzugriff) und Vorgangskontext bestimmt.

Für den Endbenutzerzugriff können Sie Abfrageanforderungen so strukturieren, dass die Verbindungsherstellung unter Verwendung eines Abfrageschlüssels erfolgt – wodurch alle Abfragen nur schreibgeschützten Zugriff erhalten – und den spezifischen, von Ihrer App verwendeten Index einschließen. Da es in einer Abfrageanforderung kein Konzept für die Verknüpfung von Indizes oder den gleichzeitigen Zugriff auf mehrere Indizes gibt, sind alle Anforderungen definitionsgemäß auf einen einzelnen Index ausgerichtet. Folglich wird die Sicherheitsgrenze durch die Konstruktion der Abfrageanforderung selbst (Kombination aus Schlüssel und einzelnem Zielindex) definiert.

Der Indexzugriff von Administratoren und Entwicklern ist undifferenziert: Beide benötigen Schreibzugriff, um vom Dienst verwaltete Objekte erstellen, löschen und aktualisieren zu können. Ein Benutzer mit einem Administratorschlüssel für Ihren Dienst kann jeden beliebigen Index in diesem Dienst lesen, ändern und löschen. Zum Schutz vor der versehentlichen oder böswilligen Löschung von Indizes können Sie Ihre interne Quellcodeverwaltung für Coderessourcen verwenden und unerwünschte Lösch- oder Änderungsvorgänge für Indizes rückgängig machen. Zur Gewährleistung der Verfügbarkeit verfügt Azure Search über ein clusterinternes Failover-Feature. Ihr proprietärer Code zum Erstellen oder Laden von Indizes wird jedoch nicht gespeichert oder ausgeführt.

Mehrinstanzenfähige Lösungen, die Sicherheitsgrenzen auf der Indexebene erfordern, enthalten in der Regel eine mittlere Ebene, die Kunden für die Indexisolierung verwenden. Weitere Informationen zum mehrinstanzenfähigen Anwendungsfall finden Sie unter [Entwurfsmuster für mehrinstanzenfähige SaaS-Anwendungen und Azure Search](search-modeling-multitenant-saas-applications.md).

## <a name="admin-access"></a>Administratorzugriff

[Rollenbasierter Zugriff (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) legt fest, ob Sie Zugriff auf Steuerelemente für den Dienst und seinen Inhalt haben. Wenn Sie ein Besitzer oder Mitwirkender für einen Azure Search-Dienst sind, können Sie das Portal oder das PowerShell-Modul **Az.Search** verwenden, um im Dienst Objekte zu erstellen, zu aktualisieren oder zu löschen. Sie können auch die [REST-API für die Azure Search-Verwaltung](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api) verwenden.

## <a name="user-access"></a>Benutzerzugriff

Standardmäßig wird der Benutzerzugriff auf einen Index durch den Zugriffsschlüssel auf die Abfrageanforderung bestimmt. Die meisten Entwickler erstellen und weisen [*Abfrageschlüssel*](search-security-api-keys.md) für clientseitige Suchanforderungen zu. Ein Abfrageschlüssel gewährt Lesezugriff auf alle Inhalte innerhalb des Index.

Wenn Sie eine detaillierte Kontrolle über Inhalte pro Benutzer benötigen, können Sie Sicherheitsfilter für Ihre Abfragen erstellen, sodass mit einer bestimmten Sicherheitsidentität verknüpfte Dokumente zurückgegeben werden. Anstelle vordefinierter Rollen und Rollenzuweisungen wird eine identitätsbasierte Zugriffssteuerung als *Filter* implementiert, die die Suchergebnisse von Dokumenten und Inhalten auf der Grundlage von Identitäten einschränken. In der folgenden Tabelle werden zwei Ansätze zur Einschränkung von Suchergebnissen für nicht autorisierte Inhalte beschrieben.

| Vorgehensweise | BESCHREIBUNG |
|----------|-------------|
|[Einschränkung aus Sicherheitsgründen auf der Grundlage von Identitätsfiltern](search-security-trimming-for-azure-search.md)  | Beschreibt den grundlegenden Workflow für die Implementierung der Zugriffssteuerung auf der Grundlage der Benutzeridentität. Der Artikel behandelt das Hinzufügen von Sicherheitsbezeichnern zu einem Index sowie das Filtern nach dem entsprechenden Feld, um unzulässige Inhalte aus den Ergebnissen zu entfernen. |
|[Einschränkung aus Sicherheitsgründen auf der Grundlage von Azure Active Directory-Identitäten](search-security-trimming-for-azure-search-with-aad.md)  | Dieser Artikel baut auf dem vorherigen Artikel auf und enthält Schritte zum Abrufen von Identitäten aus Azure Active Directory (AAD) – einem der [kostenlosen Dienste](https://azure.microsoft.com/free/) der Azure-Cloudplattform. |

## <a name="table-permissioned-operations"></a>Tabelle: Berechtigungsbasierte Vorgänge

Die folgende Tabelle enthält eine Zusammenfassung der in Azure Search zulässigen Vorgänge sowie Informationen dazu, welcher Schlüssel für den jeweiligen Vorgang erforderlich ist.

| Vorgang | Berechtigungen |
|-----------|-------------------------|
| Erstellen von Diensten | Azure-Abonnementinhaber|
| Skalieren von Diensten | Administratorschlüssel, RBAC-Rolle „Besitzer“ oder „Mitwirkender“ für die Ressource  |
| Löschen von Diensten | Administratorschlüssel, RBAC-Rolle „Besitzer“ oder „Mitwirkender“ für die Ressource |
| Erstellen/Ändern/Löschen von Objekten für den Dienst: <br>Indizes und Komponententeile (einschließlich Analysedefinitionen, Bewertungsprofile und CORS-Optionen), Indexer, Datenquellen, Synonyme und Vorschläge. | Administratorschlüssel, RBAC-Rolle „Besitzer“ oder „Mitwirkender“ für die Ressource  |
| Abfragen von Indizes | Administrator- oder Abfrageschlüssel (RBAC nicht verfügbar) |
| Abfragen von Systeminformationen (etwa zum Zurückgeben von Statistiken, Anzahlen und Objektlisten) | Administratorschlüssel, RBAC-Rolle für die Ressource („Besitzer“, „Mitwirkender“ oder „Leser“) |
| Verwalten von Administratorschlüsseln | Administratorschlüssel, RBAC-Rolle „Besitzer“ oder „Mitwirkender“ für die Ressource |
| Verwalten von Abfrageschlüsseln |  Administratorschlüssel, RBAC-Rolle „Besitzer“ oder „Mitwirkender“ für die Ressource.  |

## <a name="physical-security"></a>Physische Sicherheit

Microsoft-Rechenzentren sind mit branchenführenden physischen Sicherheitsmaßnahmen ausgestattet und erfüllen eine breite Palette von Standards und Bestimmungen. Weitere Informationen finden Sie auf der Seite [Global Datacenters](https://www.microsoft.com/cloud-platform/global-datacenters) sowie im folgenden kurzen Video zur Sicherheit von Rechenzentren:

> [!VIDEO https://www.youtube.com/embed/r1cyTL8JqRg]


## <a name="see-also"></a>Weitere Informationen

+ [Erste Schritte mit .NET (veranschaulicht das Erstellen eines Index unter Verwendung eines Administratorschlüssels)](search-create-index-dotnet.md)
+ [Erste Schritte mit REST (veranschaulicht das Erstellen eines Index unter Verwendung eines Administratorschlüssels)](search-create-index-rest-api.md)
+ [Identitätsbasierte Zugriffssteuerung mit Azure Search-Filtern](search-security-trimming-for-azure-search.md)
+ [Auf der Active Directory-Identität basierende Zugriffssteuerung mit Azure Search-Filtern](search-security-trimming-for-azure-search-with-aad.md)
+ [Filter in Azure Search](search-filters.md)