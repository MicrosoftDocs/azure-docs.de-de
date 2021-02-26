---
title: Automatische Skalierung von Azure-Ressourcen basierend auf Daten oder Zeitplan
description: Erstellen einer Einstellung für die automatische Skalierung für einen App Service-Plan anhand metrischer Daten und eines Zeitplans
author: anirudhcavale
services: azure-monitor
ms.topic: tutorial
ms.date: 12/11/2017
ms.author: ancav
ms.custom: mvc
ms.subservice: autoscale
ms.openlocfilehash: b5bcc68ddf26dd1fb8421224307c5aa20179afab
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100597827"
---
# <a name="create-an-autoscale-setting-for--azure-resources-based-on-performance-data-or-a-schedule"></a>Erstellen einer Einstellung für die automatische Skalierung von Azure-Ressourcen basierend auf Leistungsdaten oder einem Zeitplan

Über die Einstellungen für die automatische Skalierung können Sie basierend auf vordefinierten Bedingungen Instanzen des Diensts hinzufügen oder entfernen. Diese Einstellungen können über das Portal erstellt werden. Bei dieser Methode wird eine browserbasierte Benutzeroberfläche zum Erstellen und Konfigurieren einer Einstellung für die automatische Skalierung verwendet. 

In diesem Tutorial lernen Sie Folgendes: 
> [!div class="checklist"]
> * Erstellen einer Web-App und eines App Service-Plans
> * Konfigurieren von Regeln für die automatische Skalierung zum Auf- und Abskalieren nach der Anzahl der Anforderungen, die eine Web-App empfängt
> * Auslösen einer Aktion zur horizontalen Hochskalierung und Anzeigen der steigenden Anzahl von Instanzen
> * Auslösen einer Aktion zur horizontalen Herunterskalierung und Anzeigen der sinkenden Anzahl von Instanzen
> * Bereinigen von Ressourcen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="log-in-to-the-azure-portal"></a>Anmelden beim Azure-Portal

Melden Sie sich beim [Azure-Portal](https://portal.azure.com/)an.

## <a name="create-a-web-app-and-app-service-plan"></a>Erstellen einer Web-App und eines App Service-Plans
1. Klicken Sie im linken Navigationsbereich auf die Option **Ressource erstellen**.
2. Suchen Sie nach dem Element *Web-App*, wählen Sie es aus, und klicken Sie auf **Erstellen**.
3. Wählen Sie einen App-Namen wie *MyTestScaleWebApp*. Erstellen Sie eine neue Ressourcengruppe mit dem Namen *myResourceGroup*, und platzieren Sie sie in der Ressourcengruppe Ihrer Wahl.

Ihre Ressourcen sollten innerhalb weniger Minuten bereitgestellt werden. Verwenden Sie die Web-App und den dazugehörigen App Service-Plan im weiteren Verlauf dieses Tutorials.

   ![Erstellen eines neuen App Service-Plans im Portal](./media/tutorial-autoscale-performance-schedule/Web-App-Create.png)

## <a name="navigate-to-autoscale-settings"></a>Navigieren zu den Einstellungen für die automatische Skalierung
1. Klicken Sie im linken Navigationsbereich auf die Option **Überwachen**. Warten Sie, bis die Seite geladen wurde, und klicken Sie dann auf die Registerkarte **Automatisch skalieren**.
2. Eine Liste der Ressourcen in Ihrem Abonnement, die die automatische Skalierung unterstützen, wird hier aufgeführt. Ermitteln Sie den App Service-Plan, der zuvor im Tutorial erstellt wurde, und klicken Sie darauf.

    ![Screenshot des Azure-Portals mit nacheinander ausgewählten Optionen „Überwachen“ und „Automatisch skalieren“](./media/tutorial-autoscale-performance-schedule/monitor-blade-autoscale.png)

3. Klicken Sie in der Einstellung für die automatische Skalierung auf die Schaltfläche **Automatische Skalierung aktivieren**.

In den folgenden Schritten erfahren Sie, wie Sie den Bildschirm für die automatische Skalierung so ausfüllen, dass er wie in der folgenden Abbildung aussieht:

   ![Speichern einer Einstellung für die automatische Skalierung](./media/tutorial-autoscale-performance-schedule/Autoscale-Setting-Save.png)

## <a name="configure-default-profile"></a>Konfigurieren des Standardprofils
1. Geben Sie unter **Name** einen Namen für die Einstellung für die automatischen Skalierung an.
2. Vergewissern Sie sich im Standardprofil, dass der **Skalierungsmodus** auf „Auf eine bestimmte Anzahl von Instanzen skalieren“ festgelegt ist.
3. Legen Sie die Anzahl der Instanzen auf **1** fest. Durch diese Einstellung wird sichergestellt, dass das die Anzahl der Instanzen im Standardprofil auf „1“ zurückgesetzt wird, wenn kein anderes Profil aktiv oder wirksam ist.

   ![Screenshot der Seite mit der Einstellung für die Autoskalierung mit einem für die Einstellung eingegebenen Namen](./media/tutorial-autoscale-performance-schedule/autoscale-setting-profile.png)


## <a name="create-recurrence-profile"></a>Erstellen eines Wiederholungsprofils

1. Klicken Sie im Standardprofil auf den Link **Skalierungsbedingung hinzufügen**.

2. Ändern Sie den **Namen** dieses Profils in „Profil Montag bis Freitag“.

3. Stellen Sie sicher, dass der **Skalierungsmodus** auf „Basierend auf einer Metrik skalieren“ festgelegt ist.

4. Legen Sie für **Instanzgrenzwerte** die Option **Minimum** auf „1“, **Maximum** auf „2“ und **Standard** auf „1“ fest. Diese Einstellung stellt sicher, dass dieses Profil den Service-Plan nicht automatisch auf weniger als eine Instanz oder mehr als zwei Instanzen skaliert. Wenn das Profil nicht genügend Daten für die Entscheidung aufweist, wird die Standardanzahl der Instanzen (in diesem Fall 1) verwendet.

5. Wählen Sie für **Zeitplan** die Option „An bestimmten Tagen wiederholen“ aus.

6. Legen Sie fest, dass das Profil montags bis freitags von 09:00 Uhr PST bis 18:00 Uhr PST wiederholt wird. Durch diese Einstellung wird sichergestellt, dass dieses Profil nur montags bis freitags zwischen 9:00 und 18:00 Uhr aktiv ist und angewendet wird. Ansonsten wird das Profil „Standard“ für die Einstellung für die automatische Skalierung verwendet.

## <a name="create-a-scale-out-rule"></a>Erstellen einer Regel zum horizontalen Hochskalieren

1. Führen Sie in „Monday to Friday profile“ folgende Schritte durch:

2. Klicken Sie auf den Link **Regel hinzufügen**.

3. Legen Sie **Metrikquelle** auf „Andere Resource“ fest. Legen Sie **Ressourcentyp** auf „App Services“ und **Ressource** auf die Web-App fest, die weiter oben in diesem Tutorial erstellt wurde.

4. Legen Sie **Zeitaggregation** auf „Gesamt“, **Metrikname** auf „Anforderungen“ und **Statistik zum Aggregationsintervall** auf „Summe“ fest.

5. Legen Sie **Operator** auf „Größer als“, **Schwellenwert** auf „10“ und **Dauer** auf „5“ Minuten fest.

6. Legen Sie **Vorgang** auf „Anzahl erhöhen um“, **Anzahl von Instanzen** auf „1“ und **Abkühlen (Minuten)** auf fünf Minuten fest.

7. Klicken Sie auf die Schaltfläche **Hinzufügen** .

Mit dieser Regel wird sichergestellt, dass zur Steuerung des Ladevorgangs eine zusätzliche Instanz zu Ihrem App Service-Plan hinzugefügt wird, wenn Ihre Web-App innerhalb von maximal fünf Minuten mehr als zehn Anforderungen empfängt.

   ![Erstellen einer Regel zum horizontalen Hochskalieren](./media/tutorial-autoscale-performance-schedule/Scale-Out-Rule.png)

## <a name="create-a-scale-in-rule"></a>Erstellen einer Regel zum horizontalen Herunterskalieren
Es wird empfohlen, bei der Erstellung einer Regel zum horizontalen Herunterskalieren stets auch eine Regel zum horizontalen Hochskalieren hinzuzufügen. So wird eine übermäßige Bereitstellung Ihrer Ressourcen verhindert. Eine übermäßige Bereitstellung bedeutet, dass mehr Instanzen ausgeführt werden, als für die Verarbeitung der aktuellen Last erforderlich sind. 

1. Führen Sie in „Monday to Friday profile“ folgende Schritte durch:

2. Klicken Sie auf den Link **Regel hinzufügen**.

3. Legen Sie **Metrikquelle** auf „Andere Resource“ fest. Legen Sie **Ressourcentyp** auf „App Services“ und **Ressource** auf die Web-App fest, die weiter oben in diesem Tutorial erstellt wurde.

4. Legen Sie **Zeitaggregation** auf „Gesamt“, **Metrikname** auf „Anforderungen“ und **Statistik zum Aggregationsintervall** auf „Durchschnittlich“ fest.

5. Legen Sie **Operator** auf „Kleiner als“, **Schwellenwert** auf „5“ und **Dauer** auf „5“ Minuten fest.

6. Legen Sie **Vorgang** auf „Anzahl verringern um“, **Anzahl von Instanzen** auf „1“ und **Abkühlen (Minuten)** auf fünf Minuten fest.

7. Klicken Sie auf die Schaltfläche **Hinzufügen** .

    ![Erstellen einer Regel zum horizontalen Herunterskalieren](./media/tutorial-autoscale-performance-schedule/Scale-In-Rule.png)

8. **Speichern** der Einstellung für die automatische Skalierung.

    ![Speichern einer Einstellung für die automatische Skalierung](./media/tutorial-autoscale-performance-schedule/Autoscale-Setting-Save.png)

## <a name="trigger-scale-out-action"></a>Auslösen einer Aktion zum horizontalen Hochskalieren
Um die Bedingung zum horizontalen Hochskalieren in der zuvor erstellten Einstellung für die automatische Skalierung auszulösen, muss die Web-App mehr als 10 Anforderungen in weniger als 5 Minuten erhalten.

1. Öffnen Sie ein Browserfenster, und navigieren Sie zu der Web-App, die Sie zuvor in diesem Tutorial erstellt haben. Die URL für Ihre Web-App finden Sie im Azure-Portal, indem Sie zu Ihrer Web-App-Ressource navigieren und dann auf der Registerkarte „Übersicht“ auf die Schaltfläche **Durchsuchen** klicken.

2. Aktualisieren Sie die Seite mehr als zehnmal in schneller Folge.

3. Klicken Sie im linken Navigationsbereich auf die Option **Überwachen**. Nachdem die Seite geladen wurde, wählen Sie die Registerkarte **Automatisch skalieren**.

4. Wählen Sie in der Liste den App Service-Plan aus, den Sie in diesem Tutorial verwendet haben.

5. Klicken Sie in der Einstellung für die automatische Skalierung auf die Registerkarte **Ausführungsverlauf**.

6. Ein Diagramm wird angezeigt, dass die Anzahl der Instanzen des App Service-Plans im Zeitverlauf darstellt.

7. In wenigen Minuten sollte die Anzahl der Instanzen von „1“ auf „2“ steigen.

8. Im Diagramm sehen Sie die Aktivitätsprotokolleinträge für jede Skalierungsaktion, die von dieser Einstellung für die automatische Skalierung durchgeführt wurde.

## <a name="trigger-scale-in-action"></a>Auslösen einer Aktion zum horizontalen Herunterskalieren
Die Bedingung zum horizontalen Herunterskalieren in der Einstellung für die automatische Skalierung wird ausgelöst, wenn über einen Zeitraum von 10 Minuten weniger als 5 Anforderungen an die Web-App gesendet werden. 

1. Stellen Sie sicher, dass keine Anforderungen an Ihre Web-App gesendet werden.

2. Laden Sie das Azure-Portal.

3. Klicken Sie im linken Navigationsbereich auf die Option **Überwachen**. Nachdem die Seite geladen wurde, wählen Sie die Registerkarte **Automatisch skalieren**.

4. Wählen Sie in der Liste den App Service-Plan aus, den Sie in diesem Tutorial verwendet haben.

5. Klicken Sie in der Einstellung für die automatische Skalierung auf die Registerkarte **Ausführungsverlauf**.

6. Ein Diagramm wird angezeigt, dass die Anzahl der Instanzen des App Service-Plans im Zeitverlauf darstellt.

7. In wenigen Minuten sollte die Anzahl der Instanzen von 2 auf 1 sinken. Der Vorgang dauert mindestens 100 Minuten.  

8. Im Diagramm wird die entsprechende Gruppe von Aktivitätsprotokolleinträgen für jede Skalierungsaktion angezeigt, die von dieser Einstellung für die automatische Skalierung durchgeführt wurde.

    ![Anzeigen von Aktionen zum horizontalen Herunterskalieren](./media/tutorial-autoscale-performance-schedule/Scale-In-Chart.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

1. Klicken Sie im Azure-Portal im Menü auf der linken Seite auf **Alle Ressourcen**, und wählen Sie die in diesem Tutorial erstellte Web-App aus.

2. Klicken Sie auf der Seite mit Ihren Ressourcen auf **Löschen**, bestätigen Sie den Löschvorgang, indem Sie **Ja** in das Textfeld eingeben, und klicken Sie anschließend auf **Löschen**.

3. Wählen Sie anschließend die Ressource des App Service-Plans aus, und klicken Sie auf **Löschen**.

4. Bestätigen Sie den Löschvorgang, indem Sie **Ja** in das Textfeld eingeben, und klicken Sie anschließend auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes durchgeführt:  
> [!div class="checklist"]
> * Erstellen einer Web-App und eines App Service-Plans
> * Konfigurieren von Regeln für die automatische Skalierung zum Ab- und Aufskalieren nach der Anzahl der Anforderungen, die eine Web-App empfängt
> * Auslösen einer Aktion zur horizontalen Hochskalierung und Anzeigen der steigenden Anzahl von Instanzen
> * Auslösen einer Aktion zur horizontalen Hochskalierung und Anzeigen der steigenden Anzahl von Instanzen
> * Bereinigen von Ressourcen


Um mehr über die Einstellungen für die automatische Skalierung zu erfahren, fahren Sie mit der [Übersicht zur automatischen Skalierung](../autoscale/autoscale-overview.md) fort.

> [!div class="nextstepaction"]
> [Archivieren von Überwachungsdaten](../essentials/platform-logs-overview.md)

