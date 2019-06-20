---
title: Speichern von Suchen und Anheften von Datenobjekten in Azure Data Catalog
description: Anleitungsartikel zu den Funktionen in Azure Data Catalog zum Speichern von Datenquellen und -objekten für die spätere Verwendung
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.assetid: 6bd00a81-820d-4b7c-91fa-ab09e575474c
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: df3220ddb80ebc329ed6b0024ca4eefd2bdfb321
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61002069"
---
# <a name="save-searches-and-pin-data-assets-in-azure-data-catalog"></a>Speichern von Suchen und Anheften von Datenobjekten in Azure Data Catalog
## <a name="introduction"></a>Einführung
Azure Data Catalog bietet Funktionen für die Datenquellenermittlung. Sie können den Katalog schnell durchsuchen und filtern, um Datenquellen zu finden und deren Zweck zu verstehen. So ist es einfacher, für die aktuelle Aufgabe die richtigen Daten zu finden.

Aber was ist, wenn Sie regelmäßig mit den gleichen Daten arbeiten müssen? Was ist, wenn Sie und andere Benutzer mit ihrem Wissen regelmäßig an den gleichen Datenquellen im Katalog mitwirken? In diesen Situationen kann sich die wiederholte Ausführung derselben Suchvorgänge als nicht besonders effizient herausstellen. Die Lösung sind gespeicherte Suchvorgänge und angeheftete Datenobjekte.

## <a name="saved-searches"></a>Gespeicherte Suchvorgänge
Ein gespeicherter Suchvorgang in Data Catalog ist eine wiederverwendbare Suchdefinition für einen bestimmten Benutzer. Sie können eine Suche samt Suchbegriffen, Tags und anderer Filter definieren und anschließend speichern. Sie können den gespeicherten Suchvorgang später erneut ausführen, um die den Suchkriterien entsprechenden Datenobjekte zurückzugeben.

### <a name="create-a-saved-search"></a>Erstellen eines gespeicherten Suchvorgangs
Um einen gespeicherten Suchvorgang zu erstellen, führen Sie folgende Schritte aus:
1. Klicken Sie im Azure Data Catalog-Portal im Feld **Aktuelle Suche** auf **Speichern**. 

    ![Einstellungen für „Aktuelle Suche“, Link „Speichern“](./media/data-catalog-how-to-save-pin/01-save-option.png) 

2. Geben Sie die Suchkriterien ein, die Sie wiederverwenden möchten, und klicken Sie dann auf **Speichern**.

    ![Einstellungen für „Aktuelle Suche“, Name der gespeicherten Suche](./media/data-catalog-how-to-save-pin/02-name.png)

3. Geben Sie bei Aufforderung einen Namen für die gespeicherte Suche ein. Wählen Sie einen Namen, der aussagekräftig ist und die Datenobjekte gut beschreibt, die von der Suche zurückgegeben werden.

### <a name="manage-saved-searches"></a>Verwalten gespeicherter Suchvorgänge
Nachdem Sie einen oder mehrere Suchvorgänge gespeichert haben, wird die Option **Gespeicherte Suchvorgänge** unter dem Feld **Aktuelle Suche** angezeigt. Wenn die Liste erweitert wird, werden alle gespeicherten Suchvorgänge angezeigt.

 ![Liste der gespeicherten Suchvorgänge](./media/data-catalog-how-to-save-pin/03-list.png)

Führen Sie einen der folgenden Schritte aus:

* Um eine Suche auszuführen, wählen Sie eine gespeicherte Suche in der Liste aus.

* Um eine Liste der Verwaltungsoptionen für eine gespeicherte Suche anzuzeigen, klicken Sie auf den nach unten zeigenden Pfeil neben dem Suchnamen.

    ![Optionen zum Verwalten gespeicherter Suchvorgänge](./media/data-catalog-how-to-save-pin/04-managing.png)

* Um einen neuen Namen für die gespeicherte Suche einzugeben, wählen Sie **Umbenennen** aus. Die Suchdefinition wird nicht geändert.

* Wählen Sie zum Entfernen der gespeicherten Suche aus Ihrer Liste **Löschen** aus, und bestätigen Sie anschließend den Vorgang.

* Um die gespeicherte Suche als Standardsuche zu markieren, wählen Sie **Als Standard speichern** aus. Wenn Sie auf der Azure Data Catalog-Startseite eine „leere“ Suche ausführen, wird Ihre Standardsuche ausgeführt. Darüber hinaus wird die Suche, die als die Standardsuche markiert ist, in der Liste **Gespeicherte Suchvorgänge** oben angezeigt.

### <a name="organizational-saved-searches"></a>Gespeicherte Suchvorgänge in der Organisation
Alle Benutzer in Ihrer Organisation können Suchvorgänge für die eigene Verwendung speichern. Data Catalog-Administratoren können auch Suchvorgänge für alle Benutzer innerhalb der Organisation speichern. Wenn Administratoren eine Suche speichern, wird diese mit der Option **Im Unternehmen freigeben** angezeigt. Bei Wahl dieser Option wird die gespeicherte Suche für alle Benutzer in der Organisation freigegeben.

 ![Gespeicherte Suchvorgänge in der Organisation](./media/data-catalog-how-to-save-pin/08-organizational-saved-search.png)

## <a name="pinned-data-assets"></a>Angeheftete Datenressourcen
Mithilfe von gespeicherten Suchvorgängen können Sie Suchdefinitionen speichern und wiederverwenden. Die Datenobjekte, die von den Suchvorgängen zurückgegeben werden, können sich mit der Zeit ändern, sobald sich der Inhalt des Katalogs ändert. Durch Anheften von Datenobjekten können Benutzer bestimmte Datenobjekte explizit bestimmen und so einfacher darauf zugreifen, ohne dass eine Suche erforderlich ist.

Das Anheften eines Datenobjekts ist ganz einfach. Um das Datenobjekt Ihrer Liste angehefteter Objekte hinzuzufügen, klicken Sie auf das Symbol **Anheften**. Das Symbol wird in der Kachelansicht in der Ecke der Objektkachel bzw. in der Listenansicht im Azure Data Catalog-Portal in der linken Spalte angezeigt.

![Symbol zum Anheften von Datenobjekten](./media/data-catalog-how-to-save-pin/05-pinning.png)

Das Lösen eines Datenobjekts ist ebenfalls ganz einfach. Klicken Sie einfach auf das Symbol **Lösen**, um die Einstellung für das ausgewählte Datenobjekt umzuschalten.

![Symbol zum Lösen von Datenobjekten](./media/data-catalog-how-to-save-pin/06-unpinning.png)

## <a name="the-my-assets-section"></a>Der Abschnitt „Meine Ressourcen“
Die Startseite des Data Catalog-Portals enthält den Bereich **Meine Ressourcen**, in dem die Datenobjekte angezeigt werden, die für den aktuellen Benutzer interessant sind. Dieser Bereich enthält sowohl angeheftete Datenobjekte als auch gespeicherte Suchvorgänge.

![Der Abschnitt „Meine Ressourcen“ auf der Startseite](./media/data-catalog-how-to-save-pin/07-my-assets.png)

## <a name="summary"></a>Zusammenfassung
Azure Data Catalog bietet Funktionen, mit deren Hilfe Sie und andere Organisationsmitglieder die benötigten Datenquellen einfacher finden können, damit das Suchen nach Daten schneller erfolgt und mehr Zeit für das Arbeiten mit den Daten bleibt. Gespeicherte Suchvorgänge und angeheftete Datenobjekte funktionieren auf Grundlage dieser Funktionen, damit Benutzer häufig benötigte Datenquellen schnell bestimmen können.
