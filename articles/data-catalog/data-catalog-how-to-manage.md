---
title: Verwalten von Datenobjekten in Azure Data Catalog
description: In diesem Artikel wird erläutert, wie Sie Sichtbarkeit und Besitz von Datenobjekten steuern, die in Azure Data Catalog registriert sind.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 9d035417b613c81487c493bde90cdcc461f04412
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017286"
---
# <a name="manage-data-assets-in-azure-data-catalog"></a>Verwalten von Datenobjekten in Azure Data Catalog
## <a name="introduction"></a>Einführung
Zweck von Azure Data Catalog ist die Datenquellenermittlung. Sie können Datenquellen, die sie zum Durchführen von Analysen und Treffen von Entscheidungen benötigen, leicht ermitteln und verstehen. Diese Ermittlungsfunktionen haben den größten Nutzen, wenn Sie und andere Benutzer möglichst viele verfügbare Datenquellen finden und verstehen können. Vor diesem Hintergrund geht es beim Standardverhalten von Data Catalog darum, dass alle registrierten Datenquellen für alle Katalogbenutzer sichtbar und ermittelbar sein sollen.

Mit Data Catalog erhalten Benutzer keinen Zugriff auf die Daten selbst. Der Datenzugriff wird vom Besitzer der Datenquelle kontrolliert. Mit Data Catalog können Sie Datenquellen ermitteln und die Metadaten der im Katalog registrierten Quellen anzeigen.

Es kann aber zu Situationen kommen, in denen Datenquellen nur für bestimmte Benutzer oder Mitglieder bestimmter Gruppen sichtbar sein sollen. Für diese Szenarien ist es möglich, dass Benutzer den Besitz von im Katalog registrierten Datenobjekten übernehmen und dann die Sichtbarkeit ihrer Datenobjekte steuern.

> [!NOTE]
> Die in diesem Artikel beschriebenen Funktionen sind nur in der Standard Edition von Azure Data Catalog verfügbar. Die Free Edition bietet keine Funktionen für die Besitzübernahme und die Einschränkung der Sichtbarkeit von Datenobjekten.
>
>

## <a name="manage-ownership-of-data-assets"></a>Verwalten des Besitzes von Datenobjekten
Standardmäßig sind Datenobjekte, die in Data Catalog registriert sind, ohne Besitzer. Benutzer mit der Berechtigung zum Zugriff auf den Katalog können diese Objekte ermitteln und mit Anmerkungen versehen. Benutzer können den Besitz von Datenobjekten ohne Besitzer übernehmen und die Sichtbarkeit ihrer Datenobjekte anschließend einschränken.

Wenn ein Datenobjekt in Data Catalog einen Besitzer hat, können nur vom Besitzer (oder mehreren Besitzern) autorisierte Benutzer das Datenobjekt ermitteln und die dazugehörigen Metadaten anzeigen. Außerdem kann das Datenobjekt nur von den Besitzern aus Data Catalog gelöscht werden.

> [!NOTE]
> Der Besitz wirkt sich in Data Catalog nur auf die darin gespeicherten Metadaten aus. Über den Besitz werden keine Berechtigungen für die zugrunde liegende Datenquelle übertragen.
>
>

### <a name="take-ownership"></a>Übernehmen des Besitzes
Benutzer können den Besitz von Datenobjekten übernehmen, indem im Data Catalog-Portal die Option **Besitz übernehmen** gewählt wird. Für das Übernehmen des Besitzes eines Datenobjekts ohne Besitzer sind keine besonderen Berechtigungen erforderlich. Jeder Benutzer kann den Besitz eines Datenobjekts ohne Besitzer übernehmen.

### <a name="add-owners-and-co-owners"></a>Hinzufügen von Besitzern und Mitbesitzern
Wenn ein Datenobjekt bereits einen Besitzer hat, können Benutzer nicht einfach den Besitz übernehmen. Sie müssen vom jeweiligen vorhandenen Besitzer als Mitbesitzer hinzugefügt werden. Alle Besitzer können weitere Benutzer oder Sicherheitsgruppen als Mitbesitzer hinzufügen.

> [!NOTE]
> Es ist eine bewährte Methode, dass Datenobjekte mindestens zwei Besitzer haben.
>
>

### <a name="remove-owners"></a>Entfernen von Besitzern
Genauso wie jeder Datenobjektbesitzer Mitbesitzer hinzufügen kann, können Besitzer Mitbesitzer entfernen.

Wenn ein Datenobjektbesitzer sich selbst als Besitzer entfernt, kann er das Datenobjekt nicht mehr verwalten. Sobald sich ein Datenobjektbesitzer als Besitzer entfernt und keine anderen Mitbesitzer vorhanden sind, wird das Datenobjekt in den Zustand ohne Besitzer zurückversetzt.

## <a name="control-visibility"></a>Steuern der Sichtbarkeit
Besitzer von Datenobjekten können deren Sichtbarkeit steuern. Zum Einschränken der Sichtbarkeit aus der Standardeinstellung, bei der alle Data Catalog-Benutzer das Datenobjekt ermitteln und anzeigen können, kann der Besitzer des Datenobjekts die Sichtbarkeitseinstellung in den Eigenschaften des Datenobjekts von **Jeder** in **Besitzer und diese Benutzer** ändern. Besitzer können dann bestimmte Benutzer und Sicherheitsgruppen hinzufügen.

> [!NOTE]
> Nach Möglichkeit sollten der Datenobjektbesitz und die Sichtbarkeitsberechtigungen Sicherheitsgruppen und nicht einzelnen Benutzern zugewiesen werden.
>
>

## <a name="catalog-administrators"></a>Data Catalog-Administratoren
Bei Data Catalog-Administratoren handelt es sich implizit um Mitbesitzer aller Datenobjekte im Katalog. Datenobjektbesitzer können die Sichtbarkeit für Administratoren nicht aufheben, und Administratoren können den Besitz und die Sichtbarkeit für alle Datenobjekte im Katalog verwalten.

## <a name="summary"></a>Zusammenfassung
Beim Crowdsourcing-Modell von Data Catalog für die Ermittlung von Metadaten und Datenobjekten können alle Data Catalog-Benutzer Beiträge leisten und die Ermittlung durchführen. Die Standard Edition von Data Catalog enthält Funktionen für Besitz und Verwaltung, damit die Sichtbarkeit und Nutzung bestimmter Datenobjekte eingeschränkt werden kann.
