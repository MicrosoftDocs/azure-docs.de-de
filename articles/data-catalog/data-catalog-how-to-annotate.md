---
title: Kommentieren von Datenquellen in Azure Data Catalog
description: Anleitungsartikel zum Hinzufügen von Anmerkungen zu Datenressourcen in Azure Data Catalog, einschließlich Anzeigenamen, Tags, Beschreibungen und Experten.
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.assetid: 5a7e6bb2-863c-4eca-b614-1c814920d9ed
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 18dfd9eb66c1dc71ecb14bb748d9cdd6afeb00a7
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65953175"
---
# <a name="how-to-annotate-data-sources"></a>Hinzufügen von Anmerkungen zu Datenquellen
## <a name="introduction"></a>Einführung
**Microsoft Azure Data Catalog** ist ein vollständig verwalteter Clouddienst, der als Registrierungs- und Ermittlungssystem für Datenquellen von Unternehmen dient. Data Catalog unterstützt Benutzer also beim Ermitteln, Nachvollziehen und Verwenden von Datenquellen und Unternehmen dabei, mehr Nutzen aus ihren vorhandenen Daten zu ziehen. Wenn eine Datenquelle in Data Catalog registriert wird, werden ihre Metadaten vom Dienst kopiert und indiziert. Das ist aber noch nicht alles. Data Catalog bietet Benutzern die Möglichkeit, die aus der Datenquelle extrahierten Metadaten durch eigene beschreibende Metadaten (beispielsweise Beschreibungen und Tags) zu ergänzen, um die Datenquelle für andere Benutzer verständlicher zu machen.

## <a name="annotation-and-crowdsourcing"></a>Anmerkung und Crowdsourcing
Jeder hat eine Meinung. Und das ist auch gut so.
Data Catalog berücksichtigt die Tatsache, dass unterschiedliche Benutzer Unternehmensdatenquellen aus verschiedenen Perspektiven betrachten und jede dieser Perspektiven nützlich sein kann. Stellen Sie sich folgendes Szenario vor:

* Der Systemadministrator kennt die Vereinbarung zum Servicelevel für die Server und Dienste, die die Datenquelle hosten.
* Der Datenbankadministrator kennt den Sicherungszeitplan für jede Datenbank und die zulässigen ETL-Verarbeitungsfenster.
* Der Systembesitzer weiß, wie Benutzer Zugriff auf die Datenquelle anfordern.
* Der Data Steward weiß, wie die Ressourcen und Attribute in der Datenquelle dem Unternehmensdatenmodell zugeordnet sind.
* Der Analyst weiß, wie die Daten im Kontext der von ihm unterstützten Geschäftsprozesse verwendet werden.

Da jede dieser Perspektiven wertvoll ist, verwendet Data Catalog einen Crowdsourcing-Ansatz für Metadaten. Auf diese Weise kann jede Perspektive berücksichtigt und ein lückenloses Bild der registrierten Datenquellen bereitgestellt werden. Mithilfe des Data Catalog-Portals kann jeder Benutzer eigene Anmerkungen hinzufügen und bearbeiten und gleichzeitig die Anmerkungen anderer Benutzer anzeigen.

## <a name="different-types-of-annotations"></a>Anmerkungstypen
Data Catalog unterstützt folgende Anmerkungstypen:

| Anmerkung | Notizen |
| --- | --- |
| Anzeigename |Anzeigenamen können auf der Datenressourcenebene angegeben werden, um das Verständnis der Datenressource zu erleichtern. Anzeigenamen sind besonders hilfreich, wenn der zugrunde liegende Objektname unverständlich, abgekürzt oder für Benutzer nicht aussagekräftig ist. |
| BESCHREIBUNG |Beschreibungen können auf der Datenressourcen- und Attribut-/Spaltenebene angegeben werden. Beschreibungen sind kurze Textanmerkungen ohne vorgegebene Form, die beschreiben, was der Benutzer über die Datenressource weiß oder wie er sie verwendet. |
| Tags (Benutzertags) |Tags können auf der Datenressourcen- und Attribut-/Spaltenebene angegeben werden. Benutzertags sind benutzerdefinierte Bezeichnungen, die zum Kategorisieren von Datenassets oder Attributen verwendet werden können. |
| Tags (Glossartags) |Tags können auf der Datenressourcen- und Attribut-/Spaltenebene angegeben werden. Glossartags sind zentral definierte Glossarbegriffe, die zum Kategorisieren von Datenassets oder Attributen mithilfe einer allgemeinen Unternehmenstaxonomie verwendet werden können. Weitere Informationen finden Sie unter [Einrichten des Unternehmensglossars für gesteuertes Markieren](data-catalog-how-to-business-glossary.md). |
| Experten |Experten können auf der Datenressourcenebene angegeben werden. Experten sind Benutzer oder Gruppen, die über fundiertes Wissen über die Daten verfügen und als Ansprechpartner für Benutzer fungieren können, die die registrierten Datenquellen finden und Fragen haben, die durch die vorhandenen Anmerkungen nicht beantwortet werden. |
| Zugriff anfordern |Anmerkungen vom Typ „Zugriff anfordern“ können auf der Datenressourcenebene angegeben werden. Diese Anmerkung ist für Benutzer vorgesehen, die eine Datenquelle finden, für die sie noch keine Zugriffsberechtigung besitzen. Benutzer können die E-Mail-Adresse des Benutzers oder der Gruppe, der bzw. die für die Erteilung der Zugriffsberechtigung zuständig ist, die URL des für den Zugriff erforderlichen Prozesses oder Tools oder den Prozess selbst als Text eingeben. |
| Dokumentation |Eine Dokumentation kann auf der Datenressourcenebene angegeben werden. Bei einer Ressourcendokumentation handelt es sich um Rich-Text-Informationen, die Links und Bilder enthalten und somit Informationen vermitteln können, die sich nicht über Beschreibungen und Tags bereitstellen lassen. |

## <a name="annotating-multiple-assets"></a>Hinzufügen einer Anmerkung zu mehreren Datenressourcen
Benutzer können im Data Catalog-Portal mehrere Datenressourcen auswählen und allen ausgewählten Ressourcen in nur einem Arbeitsschritt eine Anmerkung hinzufügen. Da Anmerkungen für alle ausgewählten Datenressourcen gelten, ist es ganz einfach, einheitliche Beschreibungen, Tags und Experten für verwandte Datenressourcen bereitzustellen.

> [!NOTE]
> Tags und Experten können auch beim Registrieren von Datenressourcen mit dem Data Catalog-Tool zur Datenquellenregistrierung angegeben werden.
>
>

Bei Auswahl mehrerer Tabellen und Ansichten werden im Data Catalog-Portal nur Spalten angezeigt, die in allen Datenressourcen vorhanden sind. So können Benutzer Tags und Beschreibungen für alle Spalten hinzufügen, die für alle ausgewählten Datenressourcen den gleichen Namen aufweisen.

## <a name="annotations-and-discovery"></a>Anmerkungen und Ermittlung
Genau wie die Metadaten, die bei der Registrierung aus der Datenquelle extrahiert und dem Data Catalog-Suchindex hinzugefügt werden, werden auch die vom Benutzer angegebenen Metadaten indiziert. Anmerkungen erleichtern Benutzern somit nicht nur das Verständnis der gefundenen Daten, sondern auch die Ermittlung von Datenressourcen, da sie anhand der für sie sinnvollen Begriffe nach ihnen können.

## <a name="summary"></a>Zusammenfassung
Durch die Registrierung einer Datenquelle in Data Catalog werden strukturelle und beschreibende Metadaten aus der Datenquelle in den Katalogdienst kopiert, sodass die darin enthaltenen Daten leichter auffindbar sind. Sobald eine Datenquelle registriert wurde, können Benutzer im Data Catalog-Portal Anmerkungen hinzufügen, um das Verständnis der Daten zu erleichtern und die Ermittlung zu vereinfachen.

## <a name="see-also"></a>Weitere Informationen
* [Erste Schritte mit Azure Data Catalog](data-catalog-get-started.md) .
