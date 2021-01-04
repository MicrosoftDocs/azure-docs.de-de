---
title: Häufig gestellte Fragen (FAQ)
description: In diesem Artikel finden Sie häufig gestellte Fragen zu Azure Purview.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: eca0b9986c4da30adeeb02bc3d90d1e3d2892df7
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96550400"
---
# <a name="frequently-asked-questions-faq-about-azure-purview"></a>Häufig gestellte Fragen (FAQ) zu Azure Purview

## <a name="overview"></a>Übersicht

Viele Organisationen verfügen nicht über ein ganzheitliches Verständnis ihrer Daten. Es ist schwierig, genau zu verstehen, welche Daten vorhanden sind, wo sich die Daten befinden und wie das Auffinden und Zugreifen auf relevante Daten möglich ist. Für Daten fehlt Kontext, z. B. in Bezug auf Herkunft, Klassifizierung und umfassende Metadaten. Dies erschwert geschäftlichen Benutzern die Suche nach den richtigen Daten und deren entsprechende Nutzung. Aus diesem Grund wird nur ein Bruchteil der gesammelten Daten als Grundlage für Geschäftsentscheidungen genutzt. Hinzu kommt, dass keine einheitliche Vorgehensweise für die Identifizierung von Datensicherheitsproblemen und den Schutz vertraulicher Daten verfolgt werden kann. Dies erfordert ständig hohen Zeit- und Arbeitsaufwand, vor allem in Bezug auf die Sicherstellung der Datenflexibilität.

Azure Purview ist eine Lösung für Datengovernance. Sie ermöglicht es Kunden, ein tiefes Verständnis ihrer gesamten Daten zu erlangen und gleichzeitig die Nutzung zu steuern. Mit Azure Purview können Organisationen Daten ermitteln und zusammenstellen. Sie erhalten Einblick in ihren Datenbestand und können den Zugriff auf die Daten zentral steuern.

## <a name="purpose-of-this-faq"></a>Zweck dieser häufig gestellten Fragen

Dieser FAQ-Artikel enthält die Antworten auf Fragen, die von Kunden und Außendienstmitarbeitern häufig gestellt werden. Er soll Antworten auf Fragen zu Azure Purview und den zugehörigen Lösungen liefern, z. B. Azure Data Catalog (ADC) Gen2 (veraltet) und Azure Information Protection.

### <a name="what-are-the-source-types-available-for-metadata-scanning-and-classification"></a>Welche Quelltypen sind für die Überprüfung und Klassifizierung von Metadaten verfügbar?

|Azure|Nicht-Azure|
|---------|---------|
|Azure Blob Storage|Power BI|
|Azure Synapse Analytics (SQL Data Warehouse)|SQL Server |
|Azure Cosmos DB|Teradata (ab Ende 2020 verfügbar)|
|Verwaltete Azure SQL-Instanz|SAP ECC (ab Ende 2020 verfügbar)|
|Azure-Daten-Explorer|SAP S/4 HANA (ab Ende 2020 verfügbar)|
|Azure Data Lake Storage Gen1|Hive-Metastore (ab Ende 2020 verfügbar)|
|Azure Data Lake Storage Gen2|--|
|Azure Files|--|
|Azure SQL-Datenbank|--|

### <a name="what-data-systemsprocessors-can-we-connect-and-get-lineage"></a>Für welche Datensysteme/Prozessoren können wir Verbindungen herstellen und Informationen zur Herkunft erhalten?

|Datensystem/Prozessor 
|---------
|Azure Data Factory: Kopieraktivität, Datenflussaktivität 
|Benutzerdefinierte Herkunft   
|Azure Data Share   
|Power BI    |
|SQL Server Integration Services  

### <a name="how-are-adc-gen-2-azure-information-protection-and-azure-purview-related"></a>Welche Verbindung besteht zwischen ADC Gen2, Azure Information Protection und Azure Purview?

Azure Purview basiert auf ADC Gen2, aber der Umfang hat sich seitdem erweitert. Die Lösung verfügt jetzt über die erweiterten Katalogfunktionen von ADC Gen 2 in Kombination mit den Azure Information Protection-Funktionen für die Datenklassifizierung, Bezeichnung und Erzwingung von Konformitätsrichtlinien. Mittlerweile ist sie viel stärker an der allgemeinen Branchendefinition der „Datengovernance“ ausgerichtet.

### <a name="what-happens-to-customers-using-adc-gen-1"></a>Was geschieht mit Kunden, die ADC Gen1 verwenden?

Azure Purview steht im Mittelpunkt aller Produktinnovationen für Kataloglösungen von Microsoft. ADC Gen 1 wird weiterhin unterstützt.

### <a name="can-customers-have-multiple-azure-purview-accounts-in-the-same-subscription"></a>Können Kunden unter einem Abonnement über mehrere Azure Purview-Konten verfügen?

Ja. Die Nutzung einer größeren Zahl von Azure Purview-Konten pro Abonnement und Mandant wird unterstützt.

### <a name="can-i-run-adc-gen-1-and-azure-purview-in-parallel"></a>Kann ich ADC Gen1 und Azure Purview parallel ausführen?

Ja. Beides sind unabhängige Dienste.

### <a name="how-do-i-migrate-existing-adc-gen-1-data-assets-to-azure-purview"></a>Wie kann ich für vorhandene ADC Gen1-Datenressourcen die Migration zu Azure Purview durchführen?

Verwenden Sie die Azure Purview-APIs, um Daten aus ADC Gen1 zu extrahieren und in Azure Purview zu erfassen. Für das Glossar werden Tools für Massenvorgänge auf CSV-Basis unterstützt.

### <a name="how-do-i-encrypt-sensitive-data-for-sql-tables-using-azure-purview"></a>Wie kann ich vertrauliche Daten für SQL-Tabellen mit Azure Purview verschlüsseln?

Die Datenverschlüsselung wird auf Datenquellenebene durchgeführt. Azure Purview speichert nur die Metadaten. Für Daten erfolgt kein Vorschauvorgang.

### <a name="will-all-the-capabilities-of-adc-gen-2-exist-in-azure-purview"></a>Sind alle Funktionen von ADC Gen2 auch in Azure Purview vorhanden?

Ja.

<!--## Is the data lineage feature available in Azure Purview?

Yes, but it's limited to the Azure Data Factory connector.

<!-- ## How can I scan SQL Server on-premises? 

Use the self-host integration runtime capability. !-->

<!--### What is the difference between classification in Azure SQL Database and classification in Azure Purview?

|Azure SQL DB classification  |Azure Purview classification  |
|---------|---------|
|Classification is based on SQL metadata from system catalogs. |Classification is based on Azure Purview's sampling technique by using the system-defined or custom-defined regex pattern.|
|Custom classification is supported.     |Custom classification is supported.         |
|Doesn't use Microsoft 365 system classifiers out of the box.    | Uses Microsoft 365 system classifiers out of the box.        |
-->

### <a name="whats-the-difference-between-a-glossary-and-classification"></a>Worin besteht der Unterschied zwischen einem Glossar und einer Klassifizierung?

In einem Glossar wird eine Namenskonvention für nicht vorrangig technisch bewanderte bzw. geschäftliche Benutzer der Daten (auch als Datenconsumer bezeichnet) verwendet. Bei diesen Personen handelt es sich um Business Analysts oder wissenschaftliche Fachkräfte für Daten, die Azure Purview je nach Geschäftsbereich zum Suchen nach bestimmten Arten von Daten nutzen. Beispielsweise müssen Supply-Chain Analysts ggf. nach Begriffen wie *SKU-Typen* und *Versanddetails* suchen. Sie durchsuchen das Glossar nach diesen Begriffen, um die relevanten Daten zu finden.
Eine Klassifizierung ist ein Tag, das auf eine Datenressource auf Tabellen-, Spalten- oder Dateiebene angewendet wird, um anzugeben, welche Daten die Ressource enthält. Die Klassifizierung kann basierend auf dem Typ der gefundenen Daten automatisch oder manuell angewendet werden. Normalerweise nutzen Sie Klassifizierungstags, um anzugeben, ob eine Ressource vertrauliche Daten enthält und um welche Art von Daten es sich dabei handelt.

### <a name="does-azure-purview-scan-and-classify-emails-pdfs-etc-in-my-sharepoint-and-onedrive"></a>Werden von Azure Purview E-Mails, PDF-Dateien usw. für meine SharePoint- und OneDrive-Instanzen überprüft und klassifiziert?

Die Überprüfung von lokalen SharePoint-Websites und -Bibliotheken wird über den Azure Information Protection-Scanner durchgeführt. Der „Scanner“ (also die Überprüfungsfunktion) kann über das Microsoft 365-Abonnement eines Kunden, das über die folgenden SKUs verfügt, genutzt werden: AIP P1, EMS E3 und M365 E3. Wenn Sie eine dieser SKUs verwenden, sollten Sie über die richtigen Berechtigungen für die Nutzung des Azure Information Protection-Scanners verfügen.

<!--### What is the difference between classifications and sensitivity labels in Azure Purview?

Azure Purview's data governance solution is based on the Apache Atlas framework. As defined by Atlas, classification is a way to identify the contents of an asset (table or file) or an entity (table column or structured file). This classification becomes a metadata property that allows Azure Purview to understand the data within each asset and govern and protect them.

Sensitivity labels are a Microsoft 365 concept that resembles classification at the asset level. You create a label with a collection of classifications applied at the asset or entity level.

Atlas-centric customers will see no real distinction between classifications and labels. To these customers, everything is a classification and labels aren't needed.

Security-focused customers will see a distinction between classification and labeling, but only because in Microsoft 365 the classifications aren't exposed directly to the user; only labels are visible. So, similar to Atlas, Office 365 security customers don't need to deal with both entities.
-->

### <a name="what-is-the-compute-used-for-the-scan"></a>Was für ein Computemodul wird für die Überprüfung verwendet?
Es wird eine von Microsoft verwaltete Überprüfungsinfrastruktur verwendet. Für die meisten von uns unterstützten Azure-/AWS-Ressourcen müssen Sie keine Überprüfungsinfrastruktur bereitstellen.

### <a name="is-there-a-way-to-provision-azure-purview-via-azure-resource-manager-arm-template--cli--powershell"></a>Gibt es eine Möglichkeit, Azure Purview per ARM-Vorlage (Azure Resource Manager), CLI oder PowerShell bereitzustellen?

Ja. Hierfür kann eine ARM-Vorlage verwendet werden.

<!--### Does Azure Purview support guest users in AAD?-->

### <a name="im-already-using-atlas-can-i-easily-move-to-azure-purview"></a>Ich nutze bereits Atlas. Kann ich ohne großen Aufwand die Umstellung auf Azure Purview durchführen?

Azure Purview ist mit der Atlas-API kompatibel. Wenn Sie die Migration von Atlas durchführen, ist es ratsam, Ihre Datenquellen zuerst mit Azure Purview zu überprüfen. Wenn die Ressourcen in Ihrem Konto verfügbar sind, können Sie ähnliche Atlas-APIs für Integrationsvorgänge nutzen, z. B. Aktualisieren von Ressourcen oder Hinzufügen von benutzerdefinierten Herkunftsinformationen. Von Azure Purview wird die Search-API so geändert, dass Azure Search verwendet wird. Daher sollten Sie die erweiterte Suche nutzen können.

### <a name="can-i-create-multiple-catalogs-in-my-tenant"></a>Kann ich für meinen Mandanten mehrere Kataloge erstellen?

Ja. Sie können mehrere Azure Purview-Konten pro Abonnement und Mandant erstellen. Lesen Sie die Seite mit den Informationen zu den Grenzwerten: [Verwalten und Erhöhen der Kontingente für Ressourcen mit Azure Purview](how-to-manage-quotas.md).

Weitere Empfehlungen zur Nutzung von nur einem oder mehreren Konten finden Sie unter [Bewährte Methoden zur Bereitstellung von Azure Purview](deployment-best-practices.md).

### <a name="can-i-register-multiple-tenants-within-a-single-azure-purview-account"></a>Kann ich unter einem Azure Purview-Konto mehrere Mandanten registrieren?

Nein. Zum Überprüfen der Datenquelle eines anderen Mandanten müssen Sie derzeit ein separates Azure Purview-Konto auf dem Mandanten erstellen.

### <a name="does-azure-purview-support-column-level-lineage"></a>Werden für Azure Purview Herkunftsinformationen auf Spaltenebene unterstützt?

Ja. Für Azure Purview werden Herkunftsinformationen auf Spaltenebene unterstützt.