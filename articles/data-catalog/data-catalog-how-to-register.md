---
title: Registrieren von Datenquellen in Azure Data Catalog
description: Dieser Artikel bietet Informationen zum Registrieren von Datenquellen bei Azure Data Catalog, einschließlich der während der Registrierung extrahierten Metadatenfelder.
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.assetid: bab89906-186f-4d35-9ffd-61b1d903905d
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 57b9a040b875c584b126e2062e4938b37875a31b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61001292"
---
# <a name="register-data-sources-in-azure-data-catalog"></a>Registrieren von Datenquellen in Azure Data Catalog
## <a name="introduction"></a>Einführung
Azure Data Catalog ist ein vollständig verwalteter Clouddienst, der als Registrierungs- und Ermittlungssystem für Datenquellen von Unternehmen dient. Data Catalog unterstützt Benutzer also beim Ermitteln, Nachvollziehen und Verwenden von Datenquellen und Unternehmen dabei, mehr Nutzen aus ihren vorhandenen Daten zu ziehen. Der erste Schritt, damit eine Datenquelle über Data Catalog ermittelt werden kann, ist die Registrierung der Datenquelle.

## <a name="register-data-sources"></a>Registrieren von Datenquellen
Die Registrierung umfasst das Extrahieren der Metadaten aus der Datenquelle und das Kopieren der Daten in den Data Catalog-Dienst. Die Daten verbleiben, wo sie sich derzeit befinden, und unter der Kontrolle der Administratoren und Richtlinien des aktuellen Systems.

Führen Sie folgende Schritte aus, um eine Datenquelle zu registrieren:
1. Starten Sie im Azure Data Catalog-Portal das Tool zum Registrieren von Datenquellen. 
2. Melden Sie sich mit Ihrem Geschäfts-, Schul- oder Unikonto mit den gleichen Azure Active Directory-Anmeldeinformationen an, mit denen Sie sich beim Portal anmelden.
3. Wählen Sie die Datenquelle aus, die Sie registrieren möchten.

Weitere detaillierte Informationen finden Sie im Tutorial [Erste Schritte mit Azure Data Catalog](data-catalog-get-started.md).

Nachdem Sie die Datenquelle registriert haben, verfolgt der Katalog ihren Speicherort nach und indiziert ihre Metadaten. Benutzer können die Datenquelle suchen, durchsuchen und ermitteln und anschließend über die Anwendung oder das Tool nach Wahl eine Verbindung damit herstellen.

## <a name="supported-data-sources"></a>Unterstützte Datenquellen
Eine Liste mit den derzeit unterstützten Datenquellen finden Sie in der [Liste der unterstützten Datenquellen](data-catalog-dsr.md).

## <a name="structural-metadata"></a>Strukturelle Metadaten
Wenn Sie eine Datenquelle registrieren, extrahiert das Registrierungstool Informationen zur Struktur der Objekte, die Sie auswählen. Diese Informationen werden als strukturelle Metadaten bezeichnet.

Für alle Objekte enthalten diese strukturellen Metadaten den jeweiligen Speicherort des Objekts, damit Benutzer, die diese Daten ermitteln, mit ihrer Hilfe die Verbindung mit dem Objekt in den Clienttools ihrer Wahl herstellen können. Andere strukturelle Metadaten sind der Objektname und -typ, der Attribut-/Spaltenname und der Datentyp.

## <a name="descriptive-metadata"></a>Beschreibende Metadaten
Zusätzlich zu den aus der Datenquelle extrahierten strukturellen Metadaten extrahiert das Registrierungstool für Datenquellen auch beschreibende Metadaten. Für SQL Server Analysis Services und SQL Server Reporting Services stammen diese Metadaten aus den Beschreibungseigenschaften, die von diesen Diensten verfügbar gemacht werden. Für SQL Server werden Werte extrahiert, die von der erweiterten Eigenschaft „ms\_description“ bereitgestellt werden. Für Oracle Database extrahiert das Registrierungstool für Datenquellen die Spalte COMMENTS aus der Sicht ALL\_TAB\_COMMENTS.

Zusätzlich zu den aus der Datenquelle extrahierten beschreibenden Metadaten können Benutzer mit dem Registrierungstool für Datenquellen auch beschreibende Metadaten eingeben. Benutzer können Tags hinzufügen und Experten für die registrierten Objekte bestimmen. Alle diese beschreibenden Metadaten werden zusammen mit den strukturellen Metadaten in den Data Catalog-Dienst kopiert.

## <a name="include-previews"></a>Einschließen einer Vorschau
In der Standardeinstellung werden nur Metadaten aus Datenquellen extrahiert und in den Data Catalog-Dienst kopiert. Doch eine Datenquelle lässt sich häufig anhand eines Beispiels der darin enthaltenen Daten veranschaulichen.

Mit dem Data Catalog-Tool zum Registrieren von Datenquellen können Sie eine Vorschau der Daten in Form einer Momentaufnahme jeder registrierten Tabelle oder Sicht hinzufügen. Wenn Sie sich während der Registrierung für das Einschließen von Vorschauen entscheiden, fügt das Registrierungstool bis zu 20 Datensätze aus jeder Tabelle oder Sicht hinzu. Diese Momentaufnahme wird dann zusammen mit den strukturellen und beschreibenden Metadaten in den Katalog kopiert.

> [!NOTE]
> Für eine breite Tabellen mit einer großen Anzahl von Spalten werden möglicherweise weniger als 20 Datensätze in die Vorschau eingefügt.
>
>

## <a name="include-data-profiles"></a>Einschließen von Datenprofilen
Ähnlich wie ein Einschließen der Vorschau nützlichen Kontext für Benutzer bereitstellen kann, die in Data Catalog nach Datenquellen suchen, lässt sich durch Einschließen eines Datenprofils erreichen, dass ermittelte Datenquellen einfacher verstanden werden.

Mit dem Data Catalog-Tool zum Registrieren von Datenquellen können Sie für jede registrierte Tabelle oder Sicht ein Datenprofil hinzufügen. Wenn Sie sich während der Registrierung für das Hinzufügen eines Datenprofils entscheiden, fügt das Registrierungstool aggregierte Statistiken über die Daten in jeder Tabelle oder Ansicht hinzu, wie z.B.:

* Anzahl der Zeilen und Größe der Daten im Objekt
* Datum der letzten Aktualisierung der Daten und Objektschema
* Anzahl von Datensätzen mit NULL-Werten und von unterschiedlichen Werten für Spalten
* Mindest-, Höchst-, Durchschnitts- und Standardabweichungswerte für Spalten

Diese statistischen Daten werden dann zusammen mit den strukturellen und beschreibenden Metadaten in den Katalog kopiert.

> [!NOTE]
> Für Text- oder Datumsspalten werden keine Durchschnitts- und Standardabweichungswerte in deren Datenprofile eingeschlossen.
>
>

## <a name="update-registrations"></a>Aktualisieren von Registrierungen
Durch das Registrieren einer Datenquelle kann diese in Data Catalog anhand der Metadaten und der optionalen Vorschau ermittelt werden, die während der Registrierung extrahiert wurden. Wenn die Datenquelle im Katalog aktualisiert werden muss (z.B. wenn das Schema eines Objekts geändert wurde, ursprünglich ausgeschlossene Tabellen eingeschlossen werden sollen oder ein Benutzer die in der Vorschau enthaltenen Daten aktualisieren möchte), kann das Registrierungstool für Datenquellen erneut ausgeführt werden.

Beim erneuten Registrieren einer bereits registrierten Datenquelle werden neue Objekte erstellt, während vorhandene Objekte aktualisiert werden. Alle von Benutzern über Data Catalog-Portal bereitgestellten Metadaten bleiben erhalten.

## <a name="summary"></a>Zusammenfassung
Weil bei Registrierung einer Datenquelle in Data Catalog strukturelle und beschreibende Metadaten aus der Datenquelle in den Katalogdienst kopiert werden, lässt sich die Datenquelle leichter ermitteln und verstehen. Nachdem Sie die Datenquelle registriert haben, können Sie sie mit Anmerkungen versehen, verwalten und mithilfe des Data Catalog-Portals ermitteln.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum Registrieren von Datenquellen finden Sie im Tutorial [Erste Schritte mit Azure Data Catalog](data-catalog-get-started.md).
