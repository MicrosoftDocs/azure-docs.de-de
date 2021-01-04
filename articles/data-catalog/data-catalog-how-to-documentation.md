---
title: Dokumentieren von Datenquellen in Azure Data Catalog
description: Anleitungsartikel, in dem die Dokumentation von Datenressourcen in Azure Data Catalog beschrieben wird
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 3674c316e34b1e54ed8282ac7d2c228a2b774c06
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019020"
---
# <a name="how-to-document-data-sources-in-azure-data-catalog"></a>Dokumentieren von Datenquellen in Azure Data Catalog

## <a name="introduction"></a>Einführung
**Microsoft Azure Data Catalog** ist ein vollständig verwalteter Clouddienst, der als Registrierungs- und Ermittlungssystem für Datenquellen von Unternehmen dient. Mit anderen Worten ist es die Aufgabe von **Azure Data Catalog** , den Benutzern zu helfen, Datenquellen zu ermitteln, zu *verstehen* und zu nutzen, und Unternehmen zu helfen, mehr Nutzen aus ihren vorhandenen Daten zu ziehen.

Wenn eine Datenquelle in **Azure Data Catalog** registriert wird, werden ihre Metadaten vom Dienst kopiert und indiziert. Dies ist aber noch nicht alles. **Azure Data Catalog** ermöglicht Benutzern auch das Bereitstellen ihrer eigenen vollständigen Dokumentation, in der die Verwendung und allgemeine Szenarien für die Datenquelle beschrieben werden können.

Unter [Hinzufügen von Anmerkungen zu Datenquellen](data-catalog-how-to-annotate.md) erfahren Sie, dass Experten, denen die Datenquelle bekannt ist, diese mit Tags und einer Beschreibung versehen können. Das **Azure Data Catalog**-Portal enthält einen Rich-Text-Editor, damit Benutzer Datenressourcen und Container vollständig dokumentieren können. Der Editor bietet Absatzformatierung, z. B. für Überschriften, das Formatieren von Text, Aufzählungen, nummerierte Listen und Tabellen.

Tags und Beschreibungen eignen sich hervorragend für einfache Anmerkungen. Um Datennutzern ein besseres Verständnis der Verwendung einer Datenquelle und von Geschäftsszenarien für eine Datenquelle zu vermitteln, kann ein Experte eine vollständige, ausführliche Dokumentation bereitstellen. Das Dokumentieren einer Datenquelle ist einfach. Wählen Sie einfach ein Datenasset oder einen Container aus, und wählen Sie dann **Dokumentation**.

![Registerkarte „Dokumentation“ in Data Catalog](media/data-catalog-documentation/data-catalog-documentation.png)

## <a name="documenting-data-assets"></a>Dokumentieren von Datenressourcen
Der Vorteil der **Azure Data Catalog** -Dokumentation besteht darin, dass Sie Ihren Data Catalog als Inhaltsrepository zum Erstellen einer vollständigen Textbeschreibung Ihrer Datenressourcen verwenden können. Es stehen Ihnen detaillierte Inhalte zur Verfügung, die Container und Tabellen beschreiben. Wenn Sie bereits Inhalt in einem anderen Inhaltsrepository wie SharePoint oder einer Dateifreigabe haben, können Sie der Ressourcendokumentation Links hinzufügen, die auf diesen vorhandenen Inhalt verweisen. Mit diesem Feature sind Ihre vorhandenen Dokumente leichter auffindbar.

> [!NOTE]
> Dokumentation ist nicht im Suchindex enthalten.
>

![Registerkarte „Dokumentation“ und Hyperlink zum Weblink](media/data-catalog-documentation/data-catalog-documentation2.png)

In der Dokumentation können z. B. die Merkmale und der Wert eines Datenressourcencontainers beschrieben werden, es kann sich jedoch auch um eine ausführliche Beschreibung des Tabellenschemas innerhalb eines Containers handeln. Der Umfang der bereitgestellten Dokumentation sollte von Ihren geschäftlichen Anforderungen abhängig sein. Hier finden Sie einige allgemeine Vor- und Nachteile der Dokumentation von Datenressourcen:

* Dokumentation nur für einen Container: Der gesamte Inhalt befindet sich an einem Ort, Benutzern fehlen jedoch möglicherweise erforderliche Informationen, um eine fundierte Entscheidung zu treffen.
* Dokumentation nur für die Tabellen: Der Inhalt bezieht sich auf das bestimmte Objekt, Benutzer verwenden jedoch mehrere Orte für Dokumente.
* Dokumentation für Container und Tabellen: Dies ist der umfassendste Ansatz, kann jedoch eine zusätzliche Wartung der Dokumente zur Folge haben.

## <a name="summary"></a>Zusammenfassung
Durch das Dokumentieren von Datenquellen mit **Azure Data Catalog** können Sie eine Textbeschreibung Ihrer Datenressourcen mit allen erforderlichen Details erstellen.  Mithilfe von Links können Sie auf Inhalte in einem vorhandenen Inhaltsrepository verweisen; so werden Ihre vorhandenen Dokumente und Datenressourcen zusammengefasst. Sobald Ihre Benutzer geeignete Datenressourcen ermitteln, steht ihnen ein vollständiger Dokumentationssatz zur Verfügung.
