---
title: Anzeigen von Schlüsselmetriken mit Cloudyn-Dashboards in Azure | Microsoft Docs
description: In diesem Artikel wird beschrieben, wie in Cloudyn Schlüsselmetriken mit Dashboards angezeigt werden.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management
manager: vitavor
ms.custom: seodec18
ms.openlocfilehash: b83368b913bf1303b49e3a56e3a15248af222cbe
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002051"
---
# <a name="view-key-cost-metrics-with-dashboards"></a>Anzeigen der wichtigsten Kostenmetriken mit Dashboards

Dashboards in Cloudyn bieten eine allgemeine Ansicht von Berichten. Mithilfe von Dashboards können Sie die wichtigsten Kostenmetriken in einer einzigen Ansicht anzeigen. Zudem stellen sie die wichtigsten Aspekte geschäftlicher Entwicklungen bereit, damit Sie fundierte Entscheidungen für Ihr Unternehmen treffen können.

Dashboards werden auch zum Erstellen von Ansichten für weitere Personen mit anderen Zuständigkeiten in Ihrer Organisation verwendet, zum Beispiel:

- Finanzcontroller
- Anwendungs- oder Projektbesitzer
- DevOps-Entwickler
- Führungskräfte

Dashboards bestehen aus Gadgets, und jedes Gadget ist im Wesentlichen eine Miniaturansicht eines Berichts. Klicken Sie auf ein Gadget, um den zugehörigen Bericht zu öffnen. Wenn Sie Berichte anpassen, speichern Sie diese unter „Meine Berichte“, und sie werden dem Dashboard hinzugefügt.

Die Dashboardversionen unterscheiden sich für Management- (MSP-), Enterprise- und Cloudyn Premium-Benutzer. Die Unterschiede richten sich nach den Entitätszugriffsebenen. Weitere Informationen zu Zugriffsebenen finden Sie unter [Zugriffsebenen von Entitäten](tutorial-user-access.md#entity-access-levels).

Die Dashboardverfügbarkeit hängt von der Art des Kontos des Clouddienstanbieters ab, das zur Anzeige von Dashboards verwendet wird. Die Art der verfügbaren und von Cloudyn erfassten Informationen besitzt Auswirkungen auf die Berichte in Dashboards. Wenn Sie zum Beispiel kein AWS-Konto besitzen, wird Ihnen das S3 Tracker-Dashboard nicht angezeigt. Ebenso wenig werden Ihnen Azure-spezifische Informationen in den Gadgets des Optimiererdashboards angezeigt, wenn Sie den Azure Resource Manager-Zugriff auf Cloudyn nicht aktivieren.

Sie können eines der vorgefertigten Dashboards verwenden oder Ihr eigenes Dashboard mit angepassten Berichten erstellen. Wenn Sie mit Cloudyn-Berichten nicht vertraut sind, finden Sie weitere Informationen dazu unter [Verwenden von Cloudyn-Berichten](use-reports.md).

## <a name="create-a-custom-dashboard"></a>Erstellen eines benutzerdefinierten Dashboards

Um schnell mit einem benutzerdefinierten Dashboard zu beginnen, können Sie ein vorhandenes duplizieren und dessen Eigenschaften verwenden. Anschließend können Sie es Ihren Anforderungen entsprechend ändern. Klicken Sie für das Dashboard, das Sie kopieren möchten, auf **Speichern unter**. Sie können nur benutzerdefinierte Dashboards duplizieren. Die in Cloudyn enthaltenen Dashboards können nicht dupliziert werden.

So erstellen Sie ein benutzerdefiniertes Dashboard

1. Klicken Sie auf der Startseite auf **Neu hinzufügen +**. Die Seite „Mein Dashboard“ wird angezeigt.  
    ![Die Seite „Mein Dashboard“, auf der Sie neue Berichte hinzufügen](./media/dashboards/my-dashboard.png)
2. Klicken Sie auf **Neuen Bericht hinzufügen**. Das Feld „Bericht hinzufügen“ wird angezeigt.
3. Wählen Sie den Bericht, den Sie dem Dashboardgadget hinzufügen möchten. Das Gadget wird dem Dashboard hinzugefügt.
4. Wiederholen Sie die vorherigen Schritte, bis das Dashboard vollständig ist.
5. Um den Namen des Dashboards zu ändern, klicken Sie auf der Dashboardstartseite auf den Namen des Dashboards, und geben Sie den neuen Namen ein.

## <a name="modify-a-custom-dashboard"></a>Ändern eines benutzerdefinierten Dashboards

Wie bei der Erstellung eines benutzerdefinierten Dashboards können die in Cloudyn enthaltenen Dashboards nicht geändert werden. So ändern Sie einen benutzerdefinierten Dashboardbericht

1. Suchen Sie im Dashboard den Bericht, den Sie ändern möchten, und klicken Sie auf **Bearbeiten**. Der Bericht wird angezeigt.
2. Nehmen Sie die gewünschten Änderungen am Bericht vor, und klicken Sie auf **Speichern**. Der Bericht wird aktualisiert und zeigt Ihre Änderungen an.

## <a name="share-a-custom-dashboard"></a>Freigeben eines benutzerdefinierten Dashboards

Sie können ein benutzerdefiniertes Dashboard unter _Öffentlich_ oder _Meine Entität_ für andere Benutzer freigeben. Wenn Sie es unter „Öffentlich“ freigeben, können alle Benutzer das Dashboard anzeigen. Nur Benutzer mit Zugriff auf die aktuelle Entität können das Dashboard anzeigen, wenn Sie es unter „Meine Entität“ freigeben. Zum Freigeben eines benutzerdefinierten Dashboards unter „Öffentlich“ oder unter „Meine Entität“ müssen ähnliche Schritte durchgeführt werden.

So geben Sie ein benutzerdefiniertes Dashboard öffentlich frei

1. Klicken Sie in einem Dashboard auf **Dashboardeinstellungen**. Das Feld „Dashboardeinstellungen“ wird angezeigt.  
    ![Dashboardeinstellungen für ein benutzerdefiniertes Dashboard](./media/dashboards/dashboard-options.png)
2. Klicken Sie im Feld „Dashboardeinstellungen“ auf das Pfeilsymbol, und klicken Sie dann auf **Öffentlich**. Das Bestätigungsdialogfeld für öffentliche Dashboards wird angezeigt.
3. Klicken Sie auf **Ja**. Das Dashboard ist jetzt für andere Benutzer verfügbar.

## <a name="delete-a-custom-dashboard-report"></a>So löschen Sie einen benutzerdefinierten Dashboardbericht

Sie können eine benutzerdefinierte Berichtskomponente aus dem Dashboard löschen. Durch das Löschen des Berichts aus dem Dashboard wird der Bericht nicht aus der Berichtsliste gelöscht. Der Bericht wird dadurch lediglich aus dem Dashboard entfernt.

Um eine Dashboardkomponente zu löschen, klicken Sie für die Dashboardkomponente auf **Löschen**. Durch einen Klick auf **Löschen** wird die Dashboardkomponente sofort gelöscht.

## <a name="share-a-dashboard-enterprise"></a>Freigeben eines Dashboards (Enterprise)

Sie können benutzerdefinierte Dashboards für alle Benutzer in Ihrer Organisation oder für die Benutzer der aktuellen Entität freigeben. Durch die Freigabe eines Dashboards erhalten andere Benutzer eine kurze allgemeine Übersicht über die KPI. Wenn Sie ein Dashboard freigeben, wird es automatisch an all Ihre Cloudyn-Entitäten/-Kunden repliziert. Änderungen am freigegebenen Dashboard werden automatisch aktualisiert.

So geben Sie ein Dashboard für alle Benutzer einschließlich Unterentitäten frei

1. Klicken Sie auf der Dashboardstartseite auf **Bearbeiten**.
2. Klicken Sie auf **Freigeben**, und wählen Sie dann **Öffentlich**.
3. Das Bestätigungsfeld für globale öffentliche Dashboards wird angezeigt.
4. Klicken Sie auf **Ja**, um das Dashboard als globales öffentliches Dashboard festzulegen.

So geben Sie ein Dashboard für alle Benutzer einer aktuellen Entität frei

1. Klicken Sie auf der Dashboardstartseite auf **Bearbeiten**.
2. Klicken Sie auf **Freigeben**, und wählen Sie dann **Meine Entität**.
3. Klicken Sie auf **Ja**, um das Dashboard als öffentliches Dashboard festzulegen.

## <a name="duplicate-a-custom-dashboard"></a>Duplizieren eines benutzerdefinierten Dashboards

Bei der Erstellung eines neuen Dashboards können Sie auf ähnliche Eigenschaften eines vorhandenen Dashboards zurückgreifen. Sie können das Dashboard duplizieren, um ein neues zu erstellen.

Nur benutzerdefinierte Dashboards können dupliziert werden. Das Duplizieren von Standarddashboards ist nicht möglich.

So duplizieren (klonen) Sie ein benutzerdefiniertes Dashboard

1. Klicken Sie für das Dashboard, das Sie duplizieren möchten, auf **Speichern unter**. Ein neues Dashboard mit dem gleichen Namen und einer Zahl wird geöffnet.
2. Benennen Sie das duplizierte Dashboard um, und ändern Sie es nach Belieben.

-Oder-

1. Klicken Sie in den Dashboardeinstellungen in der Zeile des Dashboards, das Sie duplizieren möchten, auf **Speichern unter**.
2. Das duplizierte Dashboard wird geöffnet.
3. Benennen Sie das Dashboard um, und ändern Sie es nach Belieben.

## <a name="set-a-default-dashboard"></a>Festlegen eines Standarddashboards

Sie können ein beliebiges Dashboard als Standard festlegen. In diesem Fall wird die zugehörige Registerkarte in der Dashboard-Registerkartenliste ganz links aufgeführt. Das Standarddashboard wird angezeigt, wenn Sie das Cloudyn-Portal öffnen.

- Klicken Sie auf die Dashboardregisterkarte, die Sie als Standard festlegen möchten, und dann auf der rechten Seite auf **Standard**.

-Oder-

1. Klicken Sie auf **Dashboardeinstellungen**, um die Liste der verfügbaren Dashboards anzuzeigen, und wählen Sie das Dashboard aus, das Sie als Standard festlegen möchten.  
    ![Dashboardeinstellungen für ein Standarddashboard](./media/dashboards/dashboard-options.png)
2. Klicken Sie in der Zeile des Dashboards auf **Standard**. Das Bestätigungsfeld für das Standarddashboards wird angezeigt.
3. Klicken Sie auf **Ja**. Das Dashboard wird als Standard festgelegt.

## <a name="management-dashboard"></a>Dashboard für die Verwaltung
Das Dashboard für die Verwaltung (oder das MSP-Dashboard für MSP-Benutzer) umfasst die wichtigsten Aspekte der wesentlichen Berichtsarten.  
![Verwaltungsdashboard mit verschiedenen Berichten](./media/dashboards/management-dash.png)

### <a name="cost-entity-summary-enterprise-only"></a>Zusammenfassung der Kostenentitäten (nur Enterprise)
In diesem Gadget werden die verwalteten Kostenentitäten einschließlich der Anzahl von Entitäten und der Kontenanzahl zusammengefasst.
- Klicken Sie auf das Gadget, um den Bericht mit Unternehmensdetails zu öffnen.

### <a name="cost-over-time"></a>Kosten im Zeitverlauf
Dieses Gadget hilft Ihnen beim Ermitteln der Kostenentwicklung. Es hebt die Kosten für den letzten Tag basierend auf dem Trend der letzten 30 Tage hervor.
- Klicken Sie auf das Gadget, um den Bericht mit den Istkosten im Zeitverlauf zu öffnen und zusätzliche Details anzuzeigen und zu filtern.

### <a name="asset-controller"></a>Assetcontroller
Dieses Gadget zeigt die Anzahl der am Vortag ausgeführten Instanzen über dem Nutzungstrend der letzten 30 Tage an.
- Klicken Sie auf das Gadget, um das Dashboard mit dem Assetcontroller zu öffnen.

### <a name="unused-ri-detector"></a>Erkennung nicht verwendeter RIs
Dieses Gadget zeigt die Anzahl der nicht verwendeten Amazon EC2-Reservierungen.
- Klicken Sie auf das Gadget, um den Bericht zu den derzeit nicht verwendeten Reservierungen zu öffnen. Darin können Sie die nicht verwendeten Reservierungen anzeigen, die Sie ändern können.

### <a name="cost-by-service"></a>Kosten nach Dienst
Dieses Gadget zeigt die amortisierten Kosten nach Dienst für die letzten 30 Tage an. Bewegen Sie den Mauszeiger über das Kreisdiagramm, um die Kosten pro Dienst anzuzeigen.
- Klicken Sie auf das Gadget, um den Bericht mit der Analyse der Istkosten zu öffnen.

### <a name="potential-savings"></a>Potenzielle Einsparungen
Dieses Gadget zeigt Preisempfehlungen pro Instanztyp für Amazon EC2 und Amazon RDS.
- Klicken Sie auf das Gadget, um den Bericht mit der Einsparungsanalyse zu öffnen. Darin werden Ihre Kosten nach Instanztypen mit potenziellen Einsparungen aufgeführt.

### <a name="compute-instances---daily-trend"></a>Compute-Instanzen – Tagestrend
Dieses Gadget zeigt die aktiven Instanzen nach Typ für die letzten 30 Tage an.
- Klicken Sie auf das Gadget, um den Bericht zu Instanzen im Zeitverlauf zu öffnen. Darin sehen Sie eine Aufschlüsselung aller Instanzen, die während der letzten 30 Tage ausgeführt wurden.

### <a name="storage-by-department"></a>Speicher nach Abteilung
Dieses Gadget zeigt die von Abteilungen verwendeten Speicherdienste. Bewegen Sie den Mauszeiger über das Kreisdiagramm, um den Speicherverbrauch nach Abteilung anzuzeigen.
- Klicken Sie auf das Gadget, um das Dashboard mit dem S3 Tracker zu öffnen.

## <a name="cost-controller-dashboard"></a>Kostencontrollerdashboard
Das Kostencontrollerdashboard zeigt wesentliche Aspekte der voreingestellten Kostenzuteilung.  
![Kostencontroller-Dashboard mit verschiedenen Berichten](./media/dashboards/cost-controller-dashboard.png)

### <a name="cost-over-time"></a>Kosten im Zeitverlauf
Dieses Gadget hilft Ihnen beim Ermitteln der Kostenentwicklung. Es hebt die Kosten für den letzten Tag basierend auf dem Trend der letzten 30 Tage hervor.
- Klicken Sie auf das Gadget, um den Bericht mit den Istkosten im Zeitverlauf zu öffnen und zusätzliche Details anzuzeigen und zu filtern.

### <a name="monthly-cost-trends"></a>Monatliche Kostenentwicklung
Dieses Gadget zeigt die voraussichtlichen amortisierten Ausgaben und die tatsächlichen Ausgaben seit Monatsanfang.
- Klicken Sie auf das Gadget, um den Bericht zur Kostenprognose für den aktuellen Monat zu öffnen. Dieser enthält eine Kostenzusammenfassung seit Monatsanfang bis zum aktuellen Datum.

Dieser Bericht zeigt die Kosten seit Monatsanfang, die Kosten des vorherigen Monats und die Kostenprognose für den aktuellen Monat an. Die Kostenprognose für den aktuellen Monat wird durch Addition der aktuellen monatlichen Kosten und der prognostizierten Kosten berechnet. Die Prognose beruht auf den Kosten, die im Verlauf der letzten 30 Tage beobachtet wurden.

### <a name="12-month-planner"></a>12-Monats-Planer
Dieses Gadget zeigt die Kostenprognose für die nächsten 12 Monate sowie potenzielle Einsparungen.
- Klicken Sie auf das Gadget, um den Bericht mit der Prognose für die jährlichen Kosten zu öffnen.

### <a name="cost-by-service"></a>Kosten nach Dienst
Dieses Gadget zeigt die amortisierten Kosten nach Dienst für die letzten 30 Tage an.
- Bewegen Sie den Mauszeiger über das Kreisdiagramm, um die Kosten pro Dienst anzuzeigen.
- Klicken Sie auf das Gadget, um den Bericht mit der Analyse der Istkosten zu öffnen.

### <a name="cost-by-account"></a>Kosten nach Konto
Dieses Gadget zeigt die amortisierten Kosten nach Konto für die letzten 30 Tage an.
- Bewegen Sie den Mauszeiger über das Kreisdiagramm, um die Kosten pro Konto anzuzeigen.
- Klicken Sie auf das Gadget, um den Bericht mit der Analyse der Istkosten zu öffnen.

### <a name="cost-trend-by-day"></a>Kostenentwicklung nach Tag
Dieses Gadget zeigt die Ausgaben im Verlauf der letzten 30 Tage.
- Bewegen Sie den Mauszeiger über das Balkendiagramm, um die Kosten pro Tag anzuzeigen.
- Klicken Sie auf das Gadget, um den Bericht mit den Istkosten im Zeitverlauf zu öffnen.

### <a name="cost-trend-by-month---last-6-months"></a>Kostenentwicklung nach Monat – letzte 6 Monate

Dieses Gadget zeigt die Ausgaben im Verlauf der letzten sechs Monate.
- Bewegen Sie den Mauszeiger über das Balkendiagramm, um die Kosten pro Monat anzuzeigen.
- Klicken Sie auf das Gadget, um den Bericht mit den Istkosten im Zeitverlauf zu öffnen.

## <a name="asset-controller-dashboard"></a>Assetcontrollerdashboard

Dieses Dashboard zeigt die Anzahl ausgeführter Instanzen, verfügbare und verwendete Datenträger, die Verteilung von Instanztypen sowie Speicherinformationen an.  
![Assetcontroller-Dashboard mit verschiedenen Berichten](./media/dashboards/asset-controller-dashboard.png)

### <a name="compute-instances"></a>Compute-Instanzen
Dieses Gadget zeigt die Anzahl ausgeführter Instanzen basierend auf dem Nutzungstrend der letzten 30 Tage an.
- Klicken Sie auf das Gadget, um den Bericht mit den Instanzen im Zeitverlauf zu öffnen.

### <a name="disks"></a>Datenträger
Dieses Gadget zeigt die Gesamtanzahl und das Volumen von Datenträgern an, die in Verwendung und verfügbar sind.
- Klicken Sie auf das Gadget, um den Bericht zu aktiven Datenträgern zu öffnen.

### <a name="instance-type-distribution"></a>Instanztypverteilung
Dieses Gadget zeigt die Instanztypen in einem Kreisdiagramm.
- Klicken Sie auf das Gadget, um den Bericht zur Instanzverteilung zu öffnen. Dieser enthält eine Aufschlüsselung Ihrer aktiven Instanzen nach ausgewählter Aggregation.

### <a name="compute-instances---daily-trend"></a>Compute-Instanzen – Tagestrend
Dieses Gadget zeigt die Compute-Instanzen (Spot, reserviert und bei Bedarf) pro Tag für die letzten 30 Tage.
- Bewegen Sie den Mauszeiger über das Diagramm, um die Anzahl von Compute-Instanzen pro Typ und pro Tag anzuzeigen.
- Klicken Sie auf das Gadget, um den Bericht mit den Instanzen im Zeitverlauf zu öffnen.

### <a name="all-buckets-s3"></a>Alle Buckets (S3)
Dieses Gadget zeigt den gesamten S3-Speicher und die Anzahl gespeicherter Objekte.
- Klicken Sie auf das Gadget, um das Dashboard mit dem S3 Tracker zu öffnen. Mit diesem Dashboard können Sie Ihre aktuelle Speichernutzung sowie Speichertrends finden, analysieren und anzeigen.

### <a name="sql-db-instances-rds"></a>SQL DB-Instanzen (RDS)
Dieses Gadget zeigt die Anzahl ausgeführter Amazon-RDS-Instanzen basierend auf dem Trend der letzten 30 Tage.
- Klicken Sie auf das Gadget, um den Bericht zur RDS-Instanz im Zeitverlauf zu öffnen.

## <a name="optimizer-dashboard"></a>Optimiererdashboard
Dieses Dashboard zeigt Empfehlungen zum Downsizing, nicht verwendete Ressourcen und potenzielle Einsparungen.  
![Optimiererdashboard mit verschiedenen Berichten](./media/dashboards/optimizer-dashboard.png)

### <a name="ri-calculator"></a>RI-Rechner
Dieses Gadget zeigt die Anzahl von RI-Kaufempfehlungen an und stellt die potenziellen Einsparungen pro Jahr heraus.
- Klicken Sie auf das Gadget, um den Rechner für reservierte Instanzen zu öffnen. Darin können Sie ermitteln, wann bedarfsorientierte und wann reservierungsbasierte Tarife verwendet werden sollen.

### <a name="sizing"></a>Festlegen der Größe
Dieses Gadget zeigt die empfohlene Dimensionierung und die potenziellen Einsparungen bei deren Implementierung auf.
- Klicken Sie auf das Gadget, um den Bericht mit den Empfehlungen zur kostengünstigen EC2-Dimensionierung anzuzeigen.

### <a name="unused-ri-detector"></a>Erkennung nicht verwendeter RIs
Dieses Gadget zeigt die Anzahl der nicht verwendeten Amazon EC2-Reservierungen.
- Klicken Sie auf das Gadget, um den Bericht zu den derzeit nicht verwendeten Reservierungen zu öffnen. Darin können Sie die nicht verwendeten Reservierungen anzeigen, die Sie ändern können.

###  <a name="available-disks"></a>Verfügbare Datenträger
Dieses Gadget zeigt die Anzahl nicht angefügter Datenträger in Ihrer Bereitstellung.
- Klicken Sie auf das Gadget, um den Bericht zu nicht angefügten Datenträgern zu öffnen.

### <a name="rds-ri-calculator"></a>RDS-RI-Rechner
Dieses Gadget zeigt die Anzahl von Reservierungsempfehlungen für Ihre Amazon-RDS-Instanzen sowie die möglichen Einsparungen.
- Klicken Sie auf das Gadget, um den Bericht mit RDS-RI-Kaufempfehlungen zu öffnen. Dieser enthält Cloudyn-Empfehlungen zur Verwendung reservierter Instanzen anstelle von bedarfsgesteuerten Instanzen.

### <a name="rds-sizing"></a>RDS-Dimensionierung
Dieses Gadget zeigt die Anzahl von Größenempfehlungen und die möglichen Einsparungen.
- Klicken Sie auf das Gadget, um den Bericht mit den RDS-Größenempfehlungen zu öffnen. Darin werden ausführliche Empfehlungen zur Amazon RDS-Dimensionierung angezeigt.

Die Optimierungsempfehlungen basieren auf den Nutzungs- und Leistungsdaten, die im Laufe des letzten Monats beobachtet wurden.

## <a name="s3-tracker-dashboard"></a>S3 Tracker-Dashboard
Mit dem S3 Tracker-Dashboard können Sie Ihre aktuelle Speichernutzung sowie Speichertrends finden, analysieren und anzeigen.  
![S3 Tracker-Dashboard mit verschiedenen Berichten](./media/dashboards/s3-tracker-dashboard.png)

### <a name="all-buckets"></a>Alle Buckets
Dieses Gadget zeigt die Gesamtgröße aller Buckets in GB sowie die Gesamtanzahl von Objekten in Ihren Buckets.
- Klicken Sie auf das Gadget, um den Bericht zur Verteilung der S3-Größe zu öffnen. Dieser Bericht unterstützt Sie bei der Analyse Ihrer S3-Größe nach Bucket, Ordner der obersten Ebene, Speicherklasse und Versionsverwaltungsstatus.

### <a name="bucket-properties"></a>Bucketeigenschaften
Dieses Gadget zeigt Gesamtzahl von Speicherbuckets.
- Klicken Sie auf das Gadget, um den Bericht zu den S3-Bucketeigenschaften anzuzeigen.

### <a name="scan-status"></a>Überprüfungsstatus
Dieses Gadget zeigt, wann die letzte S3-Überprüfung durchgeführt wurde und wann die nächste gestartet wird.
- Klicken Sie auf das Gadget, um den Bericht zum S3-Überprüfungsstatus zu öffnen.

### <a name="storage-by-bucket"></a>Speicher nach Bucket
Dieses Gadget zeigt den Prozentsatz, der von den einzelnen Bucketspeicherklassen beansprucht wird.
- Klicken Sie auf das Gadget, um den Bericht zur Verteilung der S3-Größe zu öffnen. Dieser Bericht unterstützt Sie bei der Analyse Ihrer S3-Größe nach Bucket, Ordner der obersten Ebene, Speicherklasse und Versionsverwaltungsstatus.

### <a name="number-of-objects-by-bucket"></a>Anzahl von Objekten nach Bucket
Dieses Gadget zeigt die Anzahl von Objekten pro Bucket als Zahl und als Prozentsatz. Bewegen Sie den Mauszeiger über den Bucket, um die Gesamtzahl von Objekten anzuzeigen.
- Klicken Sie auf das Gadget, um den Bericht zur Verteilung der S3-Größe (basierend auf der Überprüfung) zu öffnen.

## <a name="cloud-comparison-dashboard"></a>Dashboard zum Cloudvergleich
Das Dashboard zum Cloudvergleich ermöglicht Ihnen den Vergleich der Kosten unterschiedlicher Cloudanbieter auf der Grundlage von Preis, CPU-Typ und RAM-Größe.  
![Dashboard zum Cloudvergleich mit verschiedenen Berichten](./media/dashboards/cloud-comparison-dashboard.png)

### <a name="ec2-cost-in-azure-by-instance-type"></a>EC2-Kosten in Azure nach Instanztyp
Dieses Gadget zeigt die Nutzung der letzten 30 Tage im bedarfsgesteuerten Tarif. Es vergleicht die aktuellen Amazon EC2-Kosten mit den potenziellen Kosten in Azure.
- Bewegen Sie den Mauszeiger über die Balken, um die Kosten pro Instanztyp zu vergleichen.
- Klicken Sie auf das Gadget, um den Bericht zur Kostenanalyse beim Portieren der Bereitstellung zu öffnen.

### <a name="ec2-cost-in-azure"></a>EC2-Kosten in Azure
Dieses Gadget zeigt Ihre aktuellen Amazon EC2-Kosten und vergleicht diese mit Azure. Der Vergleich basiert auf der Nutzung der letzten 30 Tage im bedarfsgesteuerten Tarif.
- Klicken Sie auf das Gadget, um den Bericht zur Kostenanalyse beim Portieren der Bereitstellung zu öffnen.

### <a name="ec2azure-instance-type-mapping"></a>Zuordnung der EC2-/Azure-Instanztypen
Dieses Gadget zeigt die beste Zuordnung elastischer Compute-Einheiten zwischen Amazon EC2 und Azure.
- Klicken Sie auf das Gadget, um den Bericht mit der Zuordnung von Instanztypen zu öffnen.

## <a name="next-steps"></a>Nächste Schritte
- Lesen Sie den Artikel [Verwenden von Cloudyn-Berichten](use-reports.md), um mehr über Berichte zu erfahren.
