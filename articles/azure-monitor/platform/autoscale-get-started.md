---
title: Erste Schritte mit der automatischen Skalierung in Azure
description: Informationen zum Skalieren Ihrer Ressource Web-App, Clouddienst, virtueller Computer oder VM-Skalierungsgruppe in Azure.
author: rajram
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/07/2017
ms.author: rajram
ms.subservice: autoscale
ms.openlocfilehash: 0535c84a8ee0776c2c35a46d3c7510a2cd615cf6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60788493"
---
# <a name="get-started-with-autoscale-in-azure"></a>Erste Schritte mit der automatischen Skalierung in Azure
In diesem Artikel wird beschrieben, wie Sie Ihre automatische Skalierungseinstellung für Ihre Ressource im Microsoft Azure-Portal einrichten.

Die automatische Skalierung von Azure Monitor gilt nur für [VM.Skalierungsgruppen](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Clouddienste](https://azure.microsoft.com/services/cloud-services/), [App Service – Web-Apps](https://azure.microsoft.com/services/app-service/web/) und [API Management-Dienste](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

## <a name="discover-the-autoscale-settings-in-your-subscription"></a>Ermitteln der Einstellungen der automatischen Skalierung in Abonnements
Sie können alle Ressourcen ermitteln, für die die automatische Skalierung in Azure Monitor angewendet wird. Befolgen Sie die folgenden Schritte für eine ausführliche exemplarische Vorgehensweise:

1. Öffnen Sie das [Azure-Portal][1].
1. Klicken Sie auf das Azure Monitor-Zeichen im linken Bereich.
  ![Öffnen Sie Azure Monitor][2].
1. Klicken Sie auf **Automatische Skalierung**, um alle Ressourcen anzuzeigen, für die die automatische Skalierung zutrifft, zusammen mit dem aktuellen Status der automatischen Skalierung.
  ![Entdecken von Metriken und automatischer Skalierung in Azure Monitor][3]

Über den Filterbereich im oberen Bereich können Sie die Liste nach unten verschieben, um Ressourcen in einer bestimmten Ressourcengruppe, bestimmte Ressourcentypen oder eine bestimmte Ressource auszuwählen.

Zu jeder Ressource finden Sie die aktuelle Anzahl der Instanzen sowie den jeweiligen Autoskalierungsstatus. Der Autoskalierungsstatus kann wie folgt lauten:

- **Nicht konfiguriert:** Sie haben die automatische Skalierung für diese Ressource noch nicht aktiviert.
- **Aktiviert:** Sie haben die automatische Skalierung für diese Ressource aktiviert.
- **Deaktiviert**: Sie haben die automatische Skalierung für diese Ressource deaktiviert.

## <a name="create-your-first-autoscale-setting"></a>Erstellen Ihrer ersten Autoskalierungseinstellung

Nun können Sie eine einfache schrittweise exemplarische Vorgehensweise zum Erstellen Ihrer ersten Einstellung für die automatische Skalierung durchgehen.

1. Öffnen Sie in Azure Monitor das Blatt **Automatisch skalieren**, und wählen Sie eine Ressource aus, die skaliert werden soll. (Die folgenden Schritte beruhen auf einem App Service-Plan, der einer Web-App zugeordnet ist. Sie können [Ihre erste ASP.NET-Web-App in Azure in fünf Minuten erstellen][4].)
1. Beachten Sie, dass die aktuelle Instanzenanzahl 1 beträgt. Klicken Sie auf **Automatische Skalierung aktivieren**.
  ![Skalierungseinstellung für die neue Web-App][5]
1. Geben Sie einen Namen für die Skalierungseinstellung an, und klicken Sie dann auf **Regel hinzufügen**. Beachten Sie die Optionen für die Skalierungsregel, die auf der rechten Seite als Kontextbereich geöffnet wird. Standardmäßig wird die Option zum Skalieren der Anzahl Ihrer Instanzen auf „1“ festgelegt, wenn der CPU-Prozentsatz der Ressource 70 % überschreitet. Behalten Sie die Standardwerte bei, und klicken Sie auf **Hinzufügen**.
  ![Erstellen der Skalierungseinstellung für eine Web-App][6]
1. Nun haben Sie Ihre erste Skalierungsregel erstellt. Beachten Sie, dass die UX bewährte Methoden empfiehlt und angibt, dass empfohlen wird, mindestens eine Skala in der Regel einzufügen. Gehen Sie dazu wie folgt vor:

    a. Klicken Sie auf **Hinzufügen einer Regel**

    b. Legen Sie **Operator** auf **Weniger als** fest.

    c. Legen Sie **Schwellenwert** auf **20** fest.

    d. Legen Sie **Vorgang** auf **Anzahl verringern um** fest.

   Sie sollten nun über eine Skalierungseinstellung verfügen, die basierend auf der CPU-Auslastung zentral hoch- bzw. herunterskaliert.
   ![Skalieren basierend auf der CPU][8]
1. Klicken Sie auf **Speichern**.

Glückwunsch! Sie haben nun Ihre erste Skalierungseinstellung erfolgreich für die automatische Skalierung Ihrer Web-App basierend auf der CPU-Auslastung konfiguriert.

> [!NOTE]
> Diese Schritte gelten auch für die ersten Schritte mit der VM-Skalierungsgruppe oder einer Clouddienstrolle.

## <a name="other-considerations"></a>Weitere Überlegungen
### <a name="scale-based-on-a-schedule"></a>Skalieren basierend auf einem Zeitplan
Neben der Skalierung basierend auf der CPU können Sie festlegen, dass die Skalierung unterschiedlich an bestimmten Wochentage skaliert werden soll.

1. Klicken Sie auf **Skalierungsbedingung hinzufügen**.
1. Legen Sie den Skalierungsmodus fest. Es gelten dieselben Regeln wie bei der Standardbedingung.
1. Wählen Sie für den Zeitplan **An bestimmten Tagen wiederholen** aus.
1. Wählen Sie die Tage sowie Start- bzw. Endzeit aus, an denen bzw. zu der die Skalierungsbedingung angewendet werden soll.

![Skalierungsbedingung basierend auf einem Zeitplan][9]
### <a name="scale-differently-on-specific-dates"></a>Individuelles Skalieren an bestimmten Daten
Neben der Skalierung basierend auf der CPU können Sie auch festlegen, dass die Skalierung unterschiedlich an bestimmten Daten skaliert werden soll.

1. Klicken Sie auf **Skalierungsbedingung hinzufügen**.
1. Legen Sie den Skalierungsmodus fest. Es gelten dieselben Regeln wie bei der Standardbedingung.
1. Wählen Sie für den Zeitplan **Start-/Enddatum angeben** aus.
1. Wählen Sie die Start-/End-Datumsangaben sowie Start- bzw. Endzeit aus, an denen bzw. zu der die Skalierungsbedingung angewendet werden soll.

![Skalierungsbedingung basierend auf Daten][10]

### <a name="view-the-scale-history-of-your-resource"></a>Anzeigen des Skalierungsverlaufs der Ressource
Wenn die Ressource zentral hoch- oder herunterskaliert wird, wird ein Ereignis im Aktivitätsprotokoll protokolliert. Sie können den Skalierungsverlauf der Ressource der letzten 24 Stunden anzeigen, indem Sie zur Registerkarte **Verlauf ausführen** wechseln.

![Ausführungsverlauf][11]

Wenn Sie den gesamten Skalierungsverlauf (für bis zu 90 Tage) anzeigen möchten, klicken Sie auf **Klicken Sie hier, um weitere Details anzuzeigen**. Das Aktivitätsprotokoll wird mit der vorausgewählten Kategorie „Automatisch skalieren“ für Ihre Ressource und Kategorie geöffnet.

### <a name="view-the-scale-definition-of-your-resource"></a>Anzeigen der Skalierungsdefinition Ihrer Ressource
Die automatische Skalierung ist eine Azure Resource Manager-Ressource. Sie können die Skalierungsdefinition im JSON-Format anzeigen, indem Sie zur Registerkarte **JSON** wechseln.

![Skalierungsdefinition][12]

Bei Bedarf können Sie Änderungen direkt in JSON vornehmen. Diese Änderungen werden nach dem Speichern berücksichtigt.

### <a name="disable-autoscale-and-manually-scale-your-instances"></a>Deaktivieren der automatischen Skalierung und manuelles Skalieren der Instanzen
Es kann möglicherweise vorkommen, dass Sie Ihre aktuelle Skalierungseinstellung deaktivieren und die Ressource manuell skalieren müssen.

Klicken Sie oben auf die Schaltfläche **Automatische Skalierung deaktivieren**.
![Deaktivieren der automatischen Skalierung][13]

> [!NOTE]
> Diese Option deaktiviert Ihre Konfiguration. Sie können jedoch zu ihr zurückgelangen, wenn Sie die automatische Skalierung erneut aktivieren.

Nun können Sie die Anzahl der Instanzen festlegen, die manuell skaliert werden sollen.

![Festlegen der manuellen Skalierung][14]

Sie können immer zur automatischen Skalierung zurückkehren, indem Sie auf **Automatische Skalierung aktivieren** und dann auf **Speichern** klicken.

## <a name="next-steps"></a>Nächste Schritte
- [Erstellen einer Aktivitätsprotokollwarnung, um alle Vorgänge der Engine für die automatische Skalierung für Ihr Abonnement zu überwachen](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Erstellen Sie eine Aktivitätsprotokollwarnung, um alle Autoskalierungs-Vorgänge zum horizontalen Herunterskalieren und horizontalen Hochskalieren in Ihrem Abonnement, bei denen Fehler aufgetreten sind, zu überwachen.](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

<!--Reference-->
[1]:https://portal.azure.com
[2]: ./media/autoscale-get-started/azure-monitor-launch.png
[3]: ./media/autoscale-get-started/discover-autoscale-azure-monitor.png
[4]: https://docs.microsoft.com/azure/app-service/app-service-web-get-started-dotnet
[5]: ./media/autoscale-get-started/scale-setting-new-web-app.png
[6]: ./media/autoscale-get-started/create-scale-setting-web-app.png
[7]: ./media/autoscale-get-started/scale-in-recommendation.png
[8]: ./media/autoscale-get-started/scale-based-on-cpu.png
[9]: ./media/autoscale-get-started/scale-condition-schedule.png
[10]: ./media/autoscale-get-started/scale-condition-dates.png
[11]: ./media/autoscale-get-started/scale-history.png
[12]: ./media/autoscale-get-started/scale-definition-json.png
[13]: ./media/autoscale-get-started/disable-autoscale.png
[14]: ./media/autoscale-get-started/set-manualscale.png

