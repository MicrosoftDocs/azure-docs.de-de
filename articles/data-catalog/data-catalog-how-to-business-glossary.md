---
title: Einrichten des Unternehmensglossars in Azure Data Catalog
description: Anleitungsartikel zum Unternehmensglossar in Azure Data Catalog zum Definieren und Verwenden eines allgemeinen Geschäftsvokabulars zum Markieren registrierter Datenobjekte.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: f91728435b885f3b4d9415bc81e18fdaaea0148f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021944"
---
# <a name="set-up-the-business-glossary-for-governed-tagging"></a>Einrichten des Unternehmensglossars für gesteuertes Markieren

## <a name="introduction"></a>Einführung

Azure Data Catalog ermöglicht die Datenquellenermittlung. Sie können dadurch Datenquellen, die sie zum Durchführen von Analysen und Treffen von Entscheidungen benötigen, leicht ermitteln und verstehen. Diese Funktionen haben den größten Nutzen, wenn Sie möglichst viele verfügbare Datenquellen finden und verstehen können.

Ein Data Catalog-Feature, das das Verständnis von Dateiobjekten fördert, ist das Markieren. Mithilfe des Markierens können Sie einem Datenobjekt oder einer Spalte Schlüsselworte zuordnen, wodurch das Datenobjekt bei Suchen einfacher gefunden werden kann. Das Markieren hilft Ihnen auch, den Kontext und Zweck des Datenobjekts einfacher zu verstehen.

Das Markieren kann jedoch gelegentlich spezielle Probleme verursachen. Es folgen Beispiele für Probleme, die das Markieren verursachen kann:

* Das Verwenden von Abkürzungen für einige Datenobjekte und von ausführlichem Text für andere. Diese Inkonsistenz behindert die Ermittlung von Datenobjekten, obwohl es das Ziel war, die Datenobjekte mit dem gleichen Tag zu markieren.
* Potenzielle Bedeutungsabweichungen abhängig vom Kontext. So kann sich z.B. ein Tag namens *Umsatz* für ein Kundendataset auf den Umsatz pro Kunde beziehen. Doch das gleiche Tag für ein Dataset zum Quartalsabsatz kann sich auf den Quartalsumsatz des Unternehmens beziehen.  

Um diesen und anderen ähnlichen Problemen zu begegnen, bietet Data Catalog ein Unternehmensglossar.

Das Unternehmensglossar von Data Catalog ermöglicht einer Organisation, die wichtigsten Geschäftsbegriffe und ihre Definitionen zu dokumentieren, um ein allgemeines Geschäftsvokabular zu erstellen. Diese Governance sorgt für Konsistenz bei der Verwendung von Daten in der gesamten Organisation. Nachdem ein Begriff im Unternehmensglossar definiert wurde, kann er einem Datenobjekt im Katalog zugewiesen werden. Diese *gesteuertes Markieren* genannte Vorgehensweise folgt demselben Ansatz wie das Markieren.

## <a name="glossary-availability-and-privileges"></a>Glossarverfügbarkeit und Berechtigungen

Das Unternehmensglossar ist nur in der Standard Edition von Azure Data Catalog verfügbar. Die Free Edition von Data Catalog bietet kein Glossar und keine Möglichkeiten zum gesteuerten Markieren.

Sie können auf das Unternehmensglossar über die Option **Glossar** im Navigationsmenü des Data Catalog-Portals zugreifen.  

![Data Catalog: Zugriff auf das Unternehmensglossar](./media/data-catalog-how-to-business-glossary/01-portal-menu.png)

Data Catalog-Administratoren und Mitglieder der Rolle „Glossaradministratoren“ können Glossarbegriffe im Unternehmensglossar erstellen, bearbeiten und löschen. Alle Data Catalog-Benutzer können die Begriffsdefinitionen anzeigen und Datenobjekte mit Glossarbegriffen markieren.

![Data Catalog: Hinzufügen eines neuen Glossarbegriffs](./media/data-catalog-how-to-business-glossary/02-new-term.png)

## <a name="creating-glossary-terms"></a>Erstellen von Glossarbegriffen

Data Catalog- und Glossaradministratoren können neue Glossarbegriffe erstellen, indem sie auf die Schaltfläche **Neuer Begriff** klicken. Glossarbegriffe enthalten die folgenden Felder:

* Eine geschäftliche Definition für den Begriff
* Eine Beschreibung, die die beabsichtigte Verwendung oder die Geschäftsregeln für das Datenobjekt oder die Spalte erfasst
* Eine Liste der Beteiligten, die den Begriff am besten kennen
* Der übergeordnete Begriff, der die Hierarchie definiert, in die der Begriff eingegliedert ist

## <a name="glossary-term-hierarchies"></a>Hierarchien von Glossarbegriffen

Mithilfe des Data Catalog-Unternehmensglossars kann eine Organisation ihr Geschäftsvokabular als Begriffshierarchie beschreiben und eine Begriffsklassifizierung erstellen, die ihre Geschäftstaxonomie besser abbildet.

Ein Begriff muss auf einer bestimmten Ebene der Hierarchie eindeutig sein. Doppelte Namen sind nicht zulässig. Es gibt keine Beschränkung der Anzahl der Hierarchieebenen, aber eine aus höchstens drei Ebenen bestehende Hierarchie ist oft leichter zu verstehen.

Die Verwendung von Hierarchien im Unternehmensglossar ist optional. Wenn Sie das übergeordnete Begriffsfeld für Glossarbegriffe frei lassen, wird eine flache (nicht hierarchische) Liste von Begriffen im Glossar erstellt.  

## <a name="tagging-assets-with-glossary-terms"></a>Markieren von Datenobjekten mit Glossarbegriffen

Sobald Glossarbegriffe innerhalb des Katalogs definiert wurden, wird das Markierens von Datenobjekten so optimiert, dass das Glossar durchsucht wird, sobald ein Benutzer ein Tag eingibt. Das Data Catalog-Portal zeigt eine Liste übereinstimmender Glossarbegriffe zur Auswahl an. Wenn der Benutzer einen Glossarbegriff in der Liste auswählt, wird er dem Datenobjekt als Tag (bzw. Glossartag) hinzugefügt. Wahlweise kann der Benutzer auch ein neues Tag erstellen, indem er einen nicht im Glossar enthaltenen Begriff eingibt (was als Benutzertag bezeichnet wird).

![Datenobjekt, das mit einem Benutzertag und zwei Glossartags markiert ist](./media/data-catalog-how-to-business-glossary/03-tagged-asset.png)

> [!NOTE]
> Benutzertags sind der einzige Tagtyp, der in der Free Edition von Data Catalog unterstützt wird.

### <a name="hover-behavior-on-tags"></a>Zeigeverhalten bei Tags

Im Data Catalog-Portal unterscheiden sich die beiden Tagtypen visuell und weisen ein unterschiedliches Zeigeverhalten auf. Wenn Sie den Cursor auf ein Benutzertag bewegen, sehen Sie den Tagtext und die Benutzer, die das Tag hinzugefügt haben. Wenn Sie den Cursor auf ein Glossartag bewegen, sehen Sie auch die Definition des Glossarbegriffs und einen Link zum Öffnen des Unternehmensglossars, um die vollständige Definition des Begriffs anzuzeigen.

### <a name="search-filters-for-tags"></a>Suchfilter für Tags

Glossar- und Benutzertags können durchsucht und als Filter in einer Suche angewendet werden.

## <a name="summary"></a>Zusammenfassung

Dank des Unternehmensglossars in Azure Data Catalog und des dadurch ermöglichten gesteuerten Markierens können Datenobjekte einheitlich bestimmt, verwaltet und ermittelt werden. Das Unternehmensglossar kann das Erlernen des Geschäftsvokabulars durch die Mitarbeiter der Organisation fördern. Das Glossar unterstützt auch das Erfassen aussagekräftiger Metadaten, was das Ermitteln und Verstehen von Datenobjekten vereinfacht.

## <a name="next-steps"></a>Nächste Schritte

* [REST-API-Dokumentation für Unternehmensglossarvorgänge](/rest/api/datacatalog/data-catalog-glossary)
