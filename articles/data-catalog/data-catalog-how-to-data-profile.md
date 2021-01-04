---
title: Verwenden der Profilerstellung für Datenquellen in Azure Data Catalog
description: Anleitungsartikel, in dem beschrieben wird, wie Sie beim Registrieren von Datenquellen in Azure Data Catalog Datenprofile auf Tabellen- und Spaltenebene hinzufügen und Datenprofile zum Nachvollziehen von Datenquellen verwenden.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: b27ff631ce13d70d15a6fd2b6dd5ad5bccaf5450
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021910"
---
# <a name="how-to-data-profile-data-sources-in-azure-data-catalog"></a>Erstellen von Datenprofilen von Datenquellen in Azure Data Catalog

## <a name="introduction"></a>Einführung

**Microsoft Azure Data Catalog** ist ein vollständig verwalteter Clouddienst, der als Registrierungs- und Ermittlungssystem für Datenquellen von Unternehmen dient. Mit anderen Worten ist es die Aufgabe von **Azure Data Catalog** , den Benutzern zu helfen, Datenquellen zu ermitteln, zu verstehen und zu nutzen, und Unternehmen zu helfen, mehr Nutzen aus ihren vorhandenen Daten zu ziehen. Wenn eine Datenquelle in **Azure Data Catalog** registriert wird, werden ihre Metadaten vom Dienst kopiert und indiziert. Dies ist aber noch nicht alles.

Das Feature für die **Datenprofilerstellung** in **Azure Data Catalog** untersucht die Daten der unterstützten Datenquellen in Ihrem Katalog und sammelt Statistiken und Informationen zu diesen Daten. Es ist einfach, ein Profil Ihrer Datenassets einzuschließen. Wählen Sie beim Registrieren eines Datenassets im Tool für die Datenquellenregistrierung die Option **Datenprofil einschließen** .

## <a name="what-is-data-profiling"></a>Beschreibung der Datenprofilerstellung

Bei der Datenprofilerstellung werden die Daten der Datenquelle untersucht, die registriert wird, und es werden Statistiken und Informationen zu diesen Daten gesammelt. Bei der Ermittlung der Datenquellen können Benutzer mit diesen statistischen Daten besser bestimmen, ob die Daten zur Lösung des Geschäftsproblems geeignet sind.

<!-- In [How to discover data sources](data-catalog-how-to-discover.md), you learn about **Azure Data Catalog's** extensive search capabilities including searching for data assets that have a profile. See [How to include a data profile when registering a data source](#howto). -->

Die Datenprofilerstellung wird für die folgenden Datenquellen unterstützt:

* SQL Server-Tabellen und -Sichten (einschließlich Azure SQL DB und Azure Synapse Analytics)
* Oracle-Tabellen und -Sichten
* Teradata-Tabellen und -Sichten
* Hive-Tabellen

Das Einschließen von Datenprofilen beim Registrieren von Datenassets dient Benutzern als Unterstützung zur Beantwortung von Fragen zu Datenquellen, z.B.:

* Können die Daten verwendet werden, um mein Geschäftsproblem zu lösen?
* Erfüllen die Daten bestimmte Standards oder Muster?
* Welche Anomalien bestehen für die Datenquelle?
* Welche Schwierigkeiten können beim Integrieren der Daten in meine Anwendung unter Umständen auftreten?

> [!NOTE]
> Sie können einem Asset auch Dokumentation hinzufügen, um zu beschreiben, wie Daten in eine Anwendung integriert werden können. Weitere Informationen finden Sie unter [Dokumentieren von Datenquellen](data-catalog-how-to-documentation.md).
>

<a name="howto"></a>

## <a name="how-to-include-a-data-profile-when-registering-a-data-source"></a>Einschließen eines Datenprofils beim Registrieren einer Datenquelle

Es ist einfach, ein Profil Ihrer Datenquelle einzuschließen. Wählen Sie beim Registrieren einer Datenquelle im Tool zum Registrieren von Datenquellen unter **Zu registrierende Objekte** die Option **Datenprofil einschließen** aus.

![Kontrollkästchen „Datenprofil einschließen“](media/data-catalog-data-profile/data-catalog-register-profile.png)

Weitere Informationen zum Registrieren von Datenquellen finden Sie unter [Registrieren von Datenquellen](data-catalog-how-to-register.md) und [Erste Schritte mit Azure Data Catalog](data-catalog-get-started.md).

## <a name="filtering-on-data-assets-that-include-data-profiles"></a>Filtern nach Datenassets mit Datenprofilen

Zum Ermitteln von Datenassets, die ein Datenprofil enthalten, können Sie `has:tableDataProfiles` oder `has:columnsDataProfiles` als einen Ihrer Suchbegriffe einbinden.

> [!NOTE]
> Bei Auswahl von **Datenprofil einschließen** in das Tool zum Registrieren von Datenquellen werden Tabellen- und Profilinformationen auf Spaltenebene einbezogen. Allerdings ermöglicht die Data Catalog-API die Registrierung von Datenassets nur mit einem einzigen Satz von einbezogenen Profilinformationen.
>

## <a name="viewing-data-profile-information"></a>Anzeigen von Datenprofilinformationen

Nachdem Sie eine geeignete Datenquelle mit einem Profil gefunden haben, können Sie die Details zum Datenprofil anzeigen. Wählen Sie zum Anzeigen des Datenprofils ein Datenasset aus, und wählen Sie im Data Catalog-Portalfenster dann die Option **Datenprofil** .

![Registerkarte „Datenprofil“](media/data-catalog-data-profile/data-catalog-view.png)

Für ein Datenprofil in **Azure Data Catalog** werden Profilinformationen für Tabellen und Spalten angezeigt:

### <a name="object-data-profile"></a>Objektdatenprofil

* Anzahl von Zeilen
* Tabellengröße
* Letzte Aktualisierung des Objekts

### <a name="column-data-profile"></a>Spaltendatenprofil

* Spaltendatentyp
* Anzahl der unterschiedlichen Werte
* Anzahl der Zeilen mit NULL-Werten
* Kleinste, größte, Durchschnitts- und Standardabweichung für Spaltenwerte

## <a name="summary"></a>Zusammenfassung

Bei der Datenprofilerstellung werden Statistiken und Informationen zu registrierten Datenassets bereitgestellt, damit Benutzer die Eignung der Daten zum Lösen von Geschäftsproblemen bestimmen können. Wenn Datenquellen zusätzlich mit Anmerkungen versehen und dokumentiert werden, erhalten Benutzer mit Datenprofilen ein tieferes Verständnis Ihrer Daten.

## <a name="see-also"></a>Weitere Informationen

* [Registrieren von Datenquellen](data-catalog-how-to-register.md)
* [Erste Schritte mit Azure Data Catalog](data-catalog-get-started.md)
