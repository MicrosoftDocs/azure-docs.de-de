---
title: Häufig gestellte Fragen zu Azure Data Catalog
description: Häufig gestellte Fragen zu Azure Data Catalog, einschließlich Funktionen für die Ermittlung von Datenquellen, Anmerkungen und Verwaltung.
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.assetid: 5c7e209a-458c-4bb4-96bb-7ed178f9528a
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 7c5241b9df23bb0334a39f2c684fd1bdff40b4c2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61004040"
---
# <a name="azure-data-catalog-frequently-asked-questions"></a>Häufig gestellte Fragen zu Azure Data Catalog
Dieser Artikel bietet Antworten auf häufig gestellte Fragen im Zusammenhang mit dem Azure Data Catalog-Dienst.

## <a name="what-is-azure-data-catalog"></a>Was ist Azure Data Catalog?
Azure Data Catalog ist ein vollständig verwalteter in Microsoft Azure gehosteter Dienst, der als Registrierungs- und Ermittlungssystem für Datenquellen von Unternehmen dient. Data Catalog ermöglicht Benutzern – von Analysten über Data Scientists bis hin zu Entwicklern –, Datenquellen zu registrieren, zu ermitteln, zu verstehen und zu nutzen.

## <a name="what-customer-challenges-does-it-solve"></a>Welche Kundenprobleme werden gelöst?
Mit Data Catalog werden Probleme bei der Ermittlung von Datenquellen und „obskuren Daten“ gelöst, indem Benutzer Datenquellen von Unternehmen ermitteln und verstehen können.

## <a name="what-are-its-target-audiences"></a>Was sind die Zielgruppen?
Data Catalog ist für technische und nicht technische Benutzer gedacht, z.B.:

* Datenentwickler, BI- und Analyseexperten: Personen, die für die Produktion von Daten- und Analyseinhalten zur Verwendung durch andere Benutzer verantwortlich sind.
* Data Stewards: Mitarbeiter, die über die Kenntnisse zu den Daten verfügen, ihre Bedeutung kennen und über die beabsichtigte Nutzung Bescheid wissen.
* Datennutzer: Mitarbeiter, die Daten leicht ermitteln und verstehen und Verbindungen damit herstellen müssen, um ihre Aufgaben mit dem Tool ihrer Wahl zu erledigen.
* Zentrale IT-Abteilung: Mitarbeite, die Hunderte von Datenquellen für die Ermittlung durch Sachbearbeiter bereitstellen und überwachen müssen, wie und von wem Daten verwendet werden.

## <a name="what-is-its-availability-by-region"></a>Wie ist die Verfügbarkeit nach Region?
Data Catalog-Dienste sind derzeit in den folgenden Rechenzentren verfügbar:

* USA (Westen)
* USA (Ost)
* Europa, Westen
* Nordeuropa
* Australien (Osten)
* Asien, Südosten

## <a name="what-are-its-limits-on-the-number-of-data-assets"></a>Welche Grenzwerte gelten für die Anzahl der Datenobjekte?
Die Free Edition von Data Catalog ist auf 5.000 registrierte Datenobjekte begrenzt.

Die Standard Edition von Data Catalog unterstützt bis zu 100.000 registrierte Datenobjekte.

Jedes in Data Catalog registrierte Objekt, z.B. Tabellen, Ansichten, Dateien und Berichte, zählen als Datenobjekt.

## <a name="what-are-its-supported-data-source-and-asset-types"></a>Welche Datenquellen und -objekttypen werden unterstützt?
Eine Liste mit den derzeit unterstützten Datenquellen finden Sie in der [Liste der unterstützten Datenquellen](data-catalog-dsr.md).

## <a name="how-do-i-request-support-for-another-data-source"></a>Wie fordere ich die Unterstützung für eine andere Datenquelle an?
Zum Übermitteln von Anfragen zu Features und anderem Feedback können Sie die [Azure-Foren für Feedback zu Data Catalog](https://feedback.azure.com/forums/906052-data-catalog/category/320788-data-sources) verwenden.

## <a name="how-do-i-get-started-with-data-catalog"></a>Wie beginne ich mit der Nutzung von Data Catalog?
Unter [Erste Schritte mit Data Catalog](data-catalog-get-started.md) erfahren Sie, wie Sie am besten mit der Nutzung beginnen. Dieser Artikel enthält eine umfassende Übersicht der Funktionen des Diensts.

## <a name="how-do-i-register-my-data"></a>Wie registriere ich meine Daten?
So registrieren Sie Ihre Daten in Data Catalog
1. Starten Sie im Azure Data Catalog-Portal im Bereich **Veröffentlichen** das Azure Data Catalog-Registrierungstool. 
2. Melden Sie sich im Data Catalog-Datenquellen-Registrierungstool mit den Anmeldeinformationen an, mit denen Sie auch auf das Data Catalog-Portal zugreifen.
3. Wählen Sie die Datenquelle und spezifischen Datenobjekte aus, die Sie registrieren möchten.

## <a name="what-properties-does-it-extract-for-data-assets-that-are-registered"></a>F: Welche Eigenschaften werden für Datenobjekte extrahiert, die registriert werden?
Die spezifischen Eigenschaften variieren je nach Datenquelle, aber im Allgemeinen werden mit dem Data Catalog-Veröffentlichungsdienst die folgenden Informationen extrahiert:

* Datenobjektname
* Datenobjekttyp
* Datenobjektbeschreibung
* Namen von Attributen/Spalten
* Datentypen von Attributen/Spalten
* Beschreibung von Attributen/Spalten

> [!IMPORTANT]
> Durch das Registrieren von Datenobjekten bei Data Catalog werden Ihre Daten nicht in die Cloud verschoben oder kopiert. Beim Registrieren von Datenobjekten in einer Datenquelle werden die Metadaten des Datenobjekts in Azure kopiert, doch die eigentlichen Daten verbleiben am vorhandenen Speicherort der Datenquelle. Die Ausnahme dieser Regel ist, wenn Sie sich beim Registrieren von Datenobjekten zum Hochladen von Vorschaudatensätzen oder eines Datenprofils entscheiden. Beim Hinzufügen einer Vorschau werden bis zu 20 Datensätze aus jedem Datenobjekt kopiert und als Momentaufnahme in Data Catalog gespeichert. Wenn Sie ein Datenprofil einbeziehen, werden aggregierte Informationen berechnet und den Metadaten hinzugefügt, die im Katalog gespeichert sind. Zu aggregierten Informationen können die Größe von Tabellen, der Prozentsatz der NULL-Werte pro Spalte oder der Mindest-, Höchst- und Durchschnittswert von Spalten gehören. 
>
>

> [!NOTE]
> Für Datenquellen wie SQL Server Analysis Services, die über eine leistungsstarke **Description**-Eigenschaft verfügen, wird dieser Eigenschaftswert vom Data Catalog-Datenquellen-Registrierungstool extrahiert. Für relationale SQL Server-Datenbanken ohne leistungsstarke **Description**-Eigenschaft extrahiert das Data Catalog-Datenquellen-Registrierungstool den Wert aus der erweiterten **ms_description**-Eigenschaft für Objekte und Spalten. Weitere Informationen finden Sie unter [Verwenden von erweiterten Eigenschaften für Datenbankobjekte](https://technet.microsoft.com/library/ms190243%28v=sql.105%29.aspx).
>
>

## <a name="how-long-should-it-take-for-newly-registered-assets-to-appear-in-the-catalog"></a>Wie lange dauert es, bis neu registrierte Datenobjekte in Data Catalog angezeigt werden?
Nach dem Registrieren von Datenobjekten bei Data Catalog kann es 5-10 Sekunden dauern, ehe sie im Data Catalog-Portal angezeigt werden.

## <a name="how-do-i-annotate-and-enrich-the-metadata-for-my-registered-data-assets"></a>Wie kann ich die Metadaten für meine registrierten Datenobjekte mit Anmerkungen versehen und erweitern?
Die einfachste Möglichkeit, Metadaten für registrierte Datenobjekte bereitzustellen, ist das Datenobjekt im Data Catalog-Portal auszuwählen und anschließend die Metadatenwerte im Eigenschaften- oder Schemabereich für das ausgewählte Objekt einzugeben.

Sie können auch während des Registrierungsvorgangs einige Metadaten angeben, z. B. Experten und Tags. Die im Data Catalog-Veröffentlichungsdienst angegebenen Werte gelten für alle Datenobjekte, die zum jeweiligen Zeitpunkt registriert werden. Um die zuletzt registrierten Objekte im Portal anzuzeigen und ihnen neue Anmerkungen hinzuzufügen, klicken Sie auf dem letzten Bildschirm des Data Catalog-Datenquellen-Registrierungstools auf die Schaltfläche **Portal anzeigen**.

## <a name="how-do-i-delete-my-registered-data-objects"></a>Wie lösche ich meine registrierten Datenobjekte?
Sie können ein Objekt aus Data Catalog löschen, indem Sie das Objekt im Portal auswählen und dann auf die Schaltfläche **Löschen** klicken. Beim Entfernen des Objekts werden seine Metadaten aus Data Catalog entfernt. Dieser Vorgang wirkt sich jedoch nicht auf die zugrunde liegende Datenquelle aus.

## <a name="what-is-an-expert"></a>Was ist ein Experte?
Ein Experte ist eine Person, die über fundiertes Wissen zu einem Datenobjekt verfügt. Ein Objekt kann mehrere Experten haben. Ein Experte muss nicht unbedingt der Besitzer eines Objekts sein. Es handelt sich lediglich um eine Person, die weiß, wie die Daten verwendet werden können und sollten.

## <a name="how-do-i-share-information-with-the-data-catalog-team-if-i-encounter-problems"></a>Wie stelle ich Informationen für das Data Catalog-Team bereit, wenn Probleme auftreten?
Nutzen Sie bitte [Azure Data Catalog-Forum](https://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409), um Probleme zu melden, Informationen zu teilen und Fragen zu stellen.

## <a name="does-the-catalog-work-with-another-data-source-that-im-interested-in"></a>Funktioniert der Katalog mit einer anderen Datenquelle, die mich interessiert?
Wir arbeiten aktiv daran, Data Catalog weitere Datenquellen hinzuzufügen. Wenn Sie für eine bestimmte Datenquelle Unterstützung wünschen, können Sie diese in den [Azure-Foren für Feedback zu Data Catalog](https://feedback.azure.com/forums/906052-data-catalog) vorschlagen (oder eine ggf. bereits vorhandene Anfrage unterstützen).

## <a name="how-is-azure-data-catalog-related-to-the-data-catalog-in-power-bi-for-office-365"></a>Welche Verbindung besteht zwischen Azure Data Catalog und Data Catalog in Power BI für Office 365?
Sie können sich Azure Data Catalog als Weiterentwicklung von Data Catalog in Power BI vorstellen. Ab Frühjahr 2017 ist mithilfe von Azure Data Catalog die gemeinsame Nutzung und die Ermittlung von Abfragen in Excel 2016 und Power Query für Excel möglich. Data Catalog-Funktionen in Excel sind für Benutzer mit Power BI Pro-Lizenzen verfügbar.

## <a name="what-permissions-do-i-need-to-register-assets-with-data-catalog"></a>Welche Berechtigungen benötige ich, um Datenobjekte bei Data Catalog registrieren zu können?
Zum Ausführen des Azure Data Catalog-Registrierungstools benötigen Sie Berechtigungen für die Datenquelle, die Ihnen das Auslesen von Metadaten aus der Quelle ermöglicht. Um auch eine Vorschau hinzuzufügen, müssen Sie über Berechtigungen verfügen, die Ihnen das Einlesen der Daten aus den registrierten Objekten erlauben.

Mit Data Catalog können Katalogadministratoren auch einschränken, welche Benutzer und Gruppen dem Katalog Metadaten hinzufügen können. Weitere Informationen finden Sie unter [Schützen des Zugriffs auf Data Catalog und Datenobjekte](data-catalog-how-to-secure-catalog.md).

## <a name="will-data-catalog-be-made-available-for-on-premises-deployment-as-well"></a>Wird Azure Data Catalog auch für die lokale Bereitstellung verfügbar gemacht?
Data Catalog ist ein Clouddienst, der sowohl Clouddatenquellen als auch lokale Datenquellen verarbeiten kann. Es handelt sich also um eine Hybridlösung zur Ermittlung von Datenquellen. Es ist derzeit keine Version des Data Catalog-Diensts geplant, die lokal ausgeführt wird.

## <a name="can-i-extract-more-or-richer-metadata-from-the-data-sources-i-register"></a>Können aus den von mir registrierten Datenquellen auch mehr oder umfangreichere Metadaten extrahiert werden?
Wir arbeiten derzeit daran, die Funktionen von Data Catalog zu erweitern. Wenn zusätzliche Metadaten während der Registrierung aus der Datenquelle extrahiert werden sollen, können Sie dies in den [Azure-Foren für Feedback zu Data Catalog](https://feedback.azure.com/forums/906052-data-catalog) vorschlagen (oder einen bereits vorhandenen Vorschlag unterstützen). 

Falls Sie Spalten-/Schemametadaten, Vorschauversionen oder Datenprofile einbinden möchten, können Sie für Datenquellen, bei denen diese Metadaten nicht vom Datenquellen-Registrierungstool extrahiert werden, die Data Catalog-API zum Hinzufügen dieser Metadaten verwenden. Weitere Informationen finden Sie unter [Azure Data Catalog](https://docs.microsoft.com/rest/api/datacatalog/).

## <a name="how-do-i-restrict-the-visibility-of-registered-data-assets-so-that-only-certain-people-can-discover-them"></a>Wie kann ich die Sichtbarkeit der registrierten Datenobjekte so einschränken, dass nur bestimmte Personen sie ermitteln können?
Wählen Sie die Datenobjekte in Data Catalog aus, und klicken Sie auf die Schaltfläche **Besitz übernehmen**. Besitzer von Datenobjekten in Data Catalog können die Einstellungen zur Sichtbarkeit ändern, um entweder für alle Benutzer die Ermittlung der im Besitz befindlichen Ressourcen zuzulassen oder die Sichtbarkeit auf bestimmte Benutzer zu beschränken. Weitere Informationen finden Sie unter [Verwalten von Datenobjekten in Azure Data Catalog](data-catalog-how-to-manage.md).

## <a name="how-do-i-update-the-registration-for-a-data-asset-so-that-changes-in-the-data-source-are-reflected-in-the-catalog"></a>Wie aktualisiere ich die Registrierung für ein Datenobjekt so, dass Änderungen in der Datenquelle im Katalog widergespiegelt werden?
Zum Aktualisieren der Metadaten für Datenobjekte, die im Katalog bereits registriert sind, registrieren Sie die Datenquelle mit den Datenobjekten einfach erneut. Alle Änderungen in der Datenquelle, z.B. hinzugefügte oder entfernte Spalten in Tabellen oder Ansichten, werden im Katalog aktualisiert, aber alle von Benutzern angegebenen Anmerkungen werden beibehalten.

## <a name="my-question-isnt-answered-here-where-can-i-go-for-answers"></a>Meine Frage wird hier nicht beantwortet wird. Wo finde ich Antworten?
Besuchen Sie das [Azure Data Catalog-Forum](https://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409). Fragen, die dort gestellt werden, werden in diesen Artikel aufgenommen.
