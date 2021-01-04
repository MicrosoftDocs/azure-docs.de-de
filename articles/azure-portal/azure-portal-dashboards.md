---
title: Erstellen und Freigeben von Dashboards im Azure-Portal
description: In diesem Artikel wird beschrieben, wie Dashboards im Azure-Portal erstellt, angepasst, veröffentlicht und freigegeben werden.
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: 10c322c9d9d18f7b79a4419bd9efa2e366a5b7c3
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745824"
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>Erstellen und Freigeben von Dashboards im Azure-Portal

Dashboards bieten eine fokussierte und organisierte Ansicht Ihrer Cloudressourcen im Azure-Portal. Verwenden Sie Dashboards als Arbeitsbereich, in dem Sie schnell Aufgaben für alltägliche Vorgänge starten und Ressourcen überwachen können. Erstellen Sie benutzerdefinierte Dashboards, die beispielsweise auf Projekten, Aufgaben oder Benutzerrollen basieren.

Das Azure-Portal stellt ein Standarddashboard als Ausgangspunkt bereit. Das Standarddashboard kann bearbeitet werden. Sie können zusätzliche Dashboards erstellen und anpassen sowie Dashboards veröffentlichen und freigeben, um sie für andere Benutzer verfügbar zu machen. In diesem Artikel wird beschrieben, wie Sie ein neues Dashboard erstellen, die Benutzeroberfläche anpassen und Dashboards veröffentlichen und freigeben.

## <a name="create-a-new-dashboard"></a>Erstellen eines neuen Dashboards

In diesem Beispiel erstellen wir ein neues privates Dashboard und weisen ihm einen Namen zu. Gehen Sie dazu wie folgt vor:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie im Menü des Azure-Portals die Option **Dashboard** aus. Die Standardansicht ist möglicherweise bereits auf das Dashboard festgelegt.

    ![Öffnen des Dashboards](./media/azure-portal-dashboards/portal-menu-dashboard.png)

1. Wählen Sie **Neues Dashboard** aus.

    ![Screenshot des neuen Dashboards](./media/azure-portal-dashboards/create-new-dashboard.png)

    Mit dieser Aktion wird der **Kachelkatalog** geöffnet, aus dem Sie Kacheln auswählen, sowie ein leeres Raster, in dem Sie die Kacheln anordnen.

    ![Screenshot des Kachelkatalogs und eines leeren Rasters](./media/azure-portal-dashboards/dashboard-name.png)

1. Wählen Sie in der Dashboardbezeichnung den Text **Mein Dashboard** aus, und geben Sie einen Namen ein, mit dem Sie das benutzerdefinierte Dashboard leicht identifizieren können.

1. Wählen Sie **Anpassung abgeschlossen** in der Kopfzeile der Seite aus, um den Bearbeitungsmodus zu beenden.

In der Dashboardansicht wird nun das neue Dashboard angezeigt. Wählen Sie den Pfeil neben dem Namen des Dashboards aus, um die für Sie verfügbaren Dashboards anzuzeigen. Die Liste kann Dashboards enthalten, die von anderen Benutzern erstellt und freigegeben wurden.

## <a name="edit-a-dashboard"></a>Bearbeiten eines Dashboards

Nun bearbeiten wir das Dashboard zum Hinzufügen, Ändern der Größe und Anordnen von Kacheln, die Ihre Azure-Ressourcen darstellen.

### <a name="add-tiles-from-the-dashboard"></a>Hinzufügen von Kacheln auf dem Dashboard

Führen Sie die folgenden Schritte aus, um einem Dashboard Kacheln hinzuzufügen:

1. Wählen Sie ![Bearbeitungssymbol](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Bearbeiten** in der Kopfzeile der Seite aus.

    ![Screenshot des Dashboards der hervorgehobenen Bearbeitungsoption](./media/azure-portal-dashboards/dashboard-edit.png)

1. Durchsuchen Sie den **Kachelkatalog**, oder verwenden Sie das Suchfeld, um nach der gewünschten Kachel zu suchen.

1. Wählen Sie **Hinzufügen** aus, um die Kachel dem Dashboard mit einer Standardgröße und einer Standardposition hinzuzufügen. Sie können die Kachel auf das Raster ziehen und dann an der gewünschten Position platzieren.

> [!TIP]
> Wenn Sie mit mehreren Organisationen arbeiten, fügen Sie dem Dashboard die Kachel **Organisationsidentität** hinzu, um die Organisation eindeutig anzuzeigen, zu der die Ressourcen gehören.

### <a name="add-tiles-from-a-resource-page"></a>Hinzufügen von Kacheln auf einer Ressourcenseite

Es gibt eine alternative Möglichkeit zum Hinzufügen von Kacheln auf Ihrem Dashboard. Viele Ressourcenseiten enthalten ein Reißzweckensymbol in der Befehlsleiste. Wenn Sie das Symbol auswählen, wird eine Kachel, die die Quellseite darstellt, an das aktuell aktive Dashboard angeheftet. 

![Screenshot der Befehlsleiste der Seite mit Reißzweckensymbol](./media/azure-portal-dashboards/dashboard-pin-blade.png)

### <a name="resize-or-rearrange-tiles"></a>Ändern der Größe oder Neuanordnung von Kacheln

Führen Sie die folgenden Schritte aus, um die Größe einer Kachel zu ändern oder die Kacheln auf einem Dashboard neu anzuordnen:

1. Wählen Sie ![Bearbeitungssymbol](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Bearbeiten** in der Kopfzeile der Seite aus.

1. Wählen Sie in der oberen rechten Ecke einer Kachel das Kontextmenü aus. Wählen Sie dann eine Kachelgröße aus. Kacheln, die eine beliebige Größe unterstützen, enthalten auch einen „Ziehpunkt“ in der unteren rechten Ecke, mit dem Sie die Kachel auf die gewünschte Größe ziehen können.

    ![Screenshot des Dashboards mit geöffneter Menü „Kachelgröße“](./media/azure-portal-dashboards/dashboard-tile-resize.png)

1. Wählen Sie eine Kachel aus, und ziehen Sie sie an eine neue Position im Raster, um das Dashboard anzuordnen.

### <a name="additional-tile-configuration"></a>Zusätzliche Kachelkonfiguration

Einige Kacheln erfordern möglicherweise weitere Konfiguration, um die gewünschten Informationen anzuzeigen. Beispielsweise muss die Kachel **Metrikdiagramm** so eingerichtet werden, dass eine Metrik aus **Azure Monitor** angezeigt wird. Sie können Kacheldaten auch anpassen, um die Standardzeiteinstellungen des Dashboards außer Kraft zu setzen.

Für alle Kacheln, die eingerichtet werden müssen, wird ein Banner **Kachel konfigurieren** angezeigt, bis Sie die Kachel anpassen. So passen Sie die Kachel an

1. Wählen Sie **Anpassung abgeschlossen** in der Kopfzeile der Seite aus, um den Bearbeitungsmodus zu beenden.

1. Wählen Sie das Banner aus, und nehmen Sie dann die gewünschte Einrichtung vor.

    ![Screenshot einer Kachel, die konfiguriert werden muss](./media/azure-portal-dashboards/dashboard-configure-tile.png)

> [!NOTE]
> Mithilfe einer Markdownkachel können Sie benutzerdefinierte, statische Inhalte in Ihrem Dashboard anzeigen. Dabei kann es sich um grundlegende Anweisungen, ein Bild, eine Reihe von Links oder sogar um Kontaktinformationen handeln. Weitere Informationen zum Verwenden einer Markdownkachel finden Sie unter [Verwenden einer Markdownkachel in Azure-Dashboards zum Anzeigen benutzerdefinierter Inhalte](azure-portal-markdown-tile.md).

### <a name="customize-tile-data"></a>Anpassen von Kacheldaten

Die Daten im Dashboard zeigen automatisch Aktivitäten der letzten 24 Stunden an. Gehen Sie folgendermaßen vor, um eine andere Zeitspanne nur für diese Kachel anzuzeigen:

1. Wählen Sie im Kontextmenü **Kacheldaten anpassen** aus, oder wählen Sie in der oberen linken Ecke der Kachel das ![Filtersymbol](./media/azure-portal-dashboards/dashboard-filter.png) Filtersymbol aus.

    ![Screenshot des Kachelkontextmenüs](./media/azure-portal-dashboards/dashboard-customize-tile-data.png)

1. Aktivieren Sie das Kontrollkästchen zum **Außerkraftsetzen der Zeiteinstellungen für das Dashboard auf Kachelebene**.

    ![Screenshot des Dialogfelds zum Konfigurieren der Zeiteinstellungen der Kachel](./media/azure-portal-dashboards/dashboard-override-time-settings.png)

1. Wählen Sie die Zeitspanne aus, die für diese Kachel angezeigt werden soll. Sie können zwischen den letzten 30 Minuten bis zu den letzten 30 Tagen wählen oder einen benutzerdefinierten Bereich definieren.

1. Wählen Sie die anzuzeigende Zeitgranularität aus. Sie können ein beliebiges Inkrement zwischen einer Minute und einem Monat anzeigen.

1. Wählen Sie **Übernehmen**.

## <a name="delete-a-tile"></a>Löschen einer Kachel

Um eine Kachel aus einem Dashboard zu entfernen, führen Sie die folgenden Schritte aus:

* Wählen Sie das Kontextmenü in der oberen rechten Ecke der Kachel aus, und wählen Sie dann **Aus Dashboard entfernen** aus. Oder

* Wählen Sie ![Bearbeitungssymbol](./media/azure-portal-dashboards/dashboard-edit-icon.png) **Bearbeiten** aus, um den Anpassungsmodus einzugeben. Platzieren Sie den Mauszeiger in der oberen rechten Ecke der Kachel, und wählen Sie dann das ![Löschsymbol](./media/azure-portal-dashboards/dashboard-delete-icon.png) Löschsymbol aus, um die Kachel aus dem Dashboard zu entfernen.

   ![Screenshot: Entfernen einer Kachel aus dem Dashboard](./media/azure-portal-dashboards/dashboard-delete-tile.png)

## <a name="clone-a-dashboard"></a>Klonen eines Dashboards

Führen Sie die folgenden Schritte aus, um ein vorhandenes Dashboard als Vorlage für ein neues Dashboard zu verwenden:

1. Stellen Sie sicher, dass in der Dashboardansicht das Dashboard angezeigt wird, das Sie kopieren möchten.

1. Wählen Sie in der Kopfzeile der Seite ![Klonsymbol](./media/azure-portal-dashboards/dashboard-clone.png) **Klonen** aus.

1. Eine Kopie des Dashboards mit dem Namen **Klon von** *Ihr Dashboardname* wird im Bearbeitungsmodus geöffnet. Verwenden Sie die weiter oben beschriebenen Schritte in diesem Artikel, um das Dashboard umzubenennen und anzupassen.

## <a name="publish-and-share-a-dashboard"></a>Veröffentlichen und Freigeben eines Dashboards

Wenn Sie ein Dashboard erstellen, ist es standardmäßig ein privates Dashboard. Dies bedeutet, dass Sie die einzige Person sind, die es anzeigen kann. Um Dashboards für andere Benutzer verfügbar zu machen, können Sie sie veröffentlichen und freigeben. Weitere Informationen finden Sie unter [Freigeben von Azure-Dashboards mithilfe der rollenbasierten Zugriffssteuerung](azure-portal-dashboard-share-access.md).

### <a name="open-a-shared-dashboard"></a>Öffnen eines freigegebenen Dashboards

Gehen Sie folgendermaßen vor, um ein freigegebenes Dashboard zu suchen und zu öffnen:

1. Wählen Sie den Pfeil neben dem Dashboardnamen aus.

1. Wählen Sie ein Dashboard in der angezeigten Liste der Dashboards aus. Gehen Sie wie folgt vor, wenn das Dashboard, das Sie öffnen möchten, nicht aufgeführt wird:

    1. Wählen Sie **Alle Dashboards durchsuchen** aus.

        ![Screenshot des Menüs „Dashboardauswahl“](./media/azure-portal-dashboards/dashboard-browse.png)

    1. Wählen Sie im Feld **Typ** die Option **Freigegebene Dashboards** aus.

        ![Screenshot des Auswahlmenüs „Alle Dashboards“](./media/azure-portal-dashboards/dashboard-browse-all.png)

    1. Wählen Sie mindestens ein Abonnement aus. Sie können auch Text eingeben, um Dashboards nach Namen zu filtern.

    1. Wählen Sie ein Dashboard aus der Liste der freigegebenen Dashboards aus.

## <a name="delete-a-dashboard"></a>Löschen eines Dashboards

Führen Sie die folgenden Schritte aus, um ein privates oder freigegebenes Dashboard dauerhaft zu löschen:

1. Wählen Sie das zu löschende Dashboard aus der Liste neben dem Namen des Dashboards aus.

1. Wählen Sie ![Löschsymbol](./media/azure-portal-dashboards/dashboard-delete-icon.png) **Löschen** in der Kopfzeile der Seite aus.

1. Wählen Sie für ein privates Dashboard im Bestätigungsdialogfeld **OK** aus, um das Dashboard zu entfernen. Aktivieren Sie für ein freigegebenes Dashboard im Bestätigungsdialogfeld das Kontrollkästchen, um zu bestätigen, dass das veröffentlichte Dashboard von anderen Benutzern nicht mehr angezeigt werden kann. Wählen Sie anschließend **OK** aus.

    ![Screenshot der Löschbestätigung](./media/azure-portal-dashboards/dashboard-delete-dash.png)

## <a name="next-steps"></a>Nächste Schritte

* [Freigeben von Azure-Dashboards mithilfe der rollenbasierten Zugriffssteuerung](azure-portal-dashboard-share-access.md)
* [Programmgesteuertes Erstellen von Azure-Dashboards](azure-portal-dashboards-create-programmatically.md)
