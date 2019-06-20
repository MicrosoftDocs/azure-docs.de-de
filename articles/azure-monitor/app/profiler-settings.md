---
title: Verwenden des Einstellungsbereichs für Azure Application Insights Profiler | Microsoft-Dokumentation
description: Anzeigen des Profiler-Status und Starten von Profilerstellungssitzungen
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: 2bc32e28ffd6dd9dab2da61078684791a04709cc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64922985"
---
# <a name="configure-application-insights-profiler"></a>Konfigurieren von Application Insights Profiler

## <a name="profiler-settings-pane"></a>Einstellungsbereich von Profiler

Um den Einstellungsbereich von Azure Application Insights Profiler zu öffnen, wechseln Sie zum Bereich „Leistung“ von Application Insights, und wählen Sie dann die Schaltfläche **Profiler** aus.

![Konfigurieren des Profiler-Bereichs][configure-profiler-entry]

Der Bereich **Application Insights Profiler konfigurieren** enthält vier Funktionen: 
* **Jetzt Profil erstellen:** Startet die Profilerstellungssitzungen für alle Apps, die mit dieser Instanz von Application Insights verknüpft sind.
* **Verknüpfte Apps:** Listet die Anwendungen auf, die Profilerstellungsdaten an diese Application Insights-Ressource senden.
* **Aktuelle Sitzungen:** Zeigt den Status der Sitzung an, wenn Sie **Jetzt Profil erstellen** auswählen. 
* **Letzte Profilerstellungssitzungen:** Zeigt Informationen zu vergangenen Profilerstellungssitzungen an.

![Profiler auf Anforderung][profiler-on-demand]

## <a name="app-service-environment"></a>App Service-Umgebung
Abhängig von der Konfiguration Ihrer Azure App Service-Umgebung ist der Aufruf zum Überprüfen des Agent-Status möglicherweise blockiert. Im Bereich wird möglicherweise eine Meldung angezeigt, dass der Agent nicht ausgeführt wird, obwohl er ausgeführt wird. Zur Sicherheit können Sie den WebJob in Ihrer Anwendung überprüfen. Wenn alle Werte der App-Einstellungen richtig sind und die Application Insights-Websiteerweiterung in Ihrer Anwendung installiert ist, wird der Profiler ausgeführt. Wenn Ihre Anwendung genügend Datenverkehr empfängt, sollten in einer Liste die letzten Profilerstellungssitzungen angezeigt werden.

## <a id="profileondemand"></a> Manuelles Auslösen von Profiler

### <a name="minimum-requirements"></a>Mindestanforderungen 
Damit ein Benutzer eine Profilersitzung manuell auslösen kann, muss er in seiner Rolle mindestens über Schreibzugriff für die Application Insights-Komponente verfügen. In den meisten Fällen erhalten Sie diesen Zugriff automatisch, und es sind keine weiteren Arbeitsschritte erforderlich. Wenn Sie Probleme haben, müssen Sie die entsprechende Rolle für den Abonnementbereich, d.h. „Mitwirkender der Application Insights-Komponente“, hinzufügen. [Weitere Informationen zur Rollenzugriffssteuerung mithilfe der Azure-Überwachung](https://docs.microsoft.com/azure/azure-monitor/app/resources-roles-access-control).

Sie können Profiler manuell mit einem einzigen Mausklick auslösen. Nehmen wir an, dass Sie einen Webleistungstest ausführen. Sie benötigen Ablaufverfolgungen, um besser zu verstehen, wie Ihre Web-App unter Last ausgeführt wird. Die Kontrolle über den Zeitpunkt der Erfassung von Ablaufverfolgungen ist entscheidend, da Sie wissen, wann der Auslastungstest ausgeführt wird. Bei einem zufälligen Intervall für die Stichprobenentnahme verpassen Sie diesen Zeitpunkt aber möglicherweise.

Die folgenden Abschnitte veranschaulichen dieses Szenario:

### <a name="step-1-optional-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>Schritt 1: (Optional:) Generieren von Datenverkehr für Ihre Web-App durch Starten eines Webleistungstests

Wenn Ihre Web-App bereits über eingehenden Datenverkehr verfügt oder wenn Sie einfach nur manuell Datenverkehr generieren möchten, überspringen Sie diesen Abschnitt, und fahren Sie mit Schritt 2 fort.

1. Wählen Sie im Application Insights-Portal **Konfigurieren** > **Leistungstests** aus. 

1. Wählen Sie die Schaltfläche **Neu** aus, um einen neuen Leistungstest zu starten.

   ![Neuen Leistungstest erstellen][create-performance-test]

1. Konfigurieren Sie im Bereich **Neuer Leistungstest** die Testziel-URL. Übernehmen Sie alle Standardeinstellungen, und wählen Sie dann **Test ausführen** aus, um die Ausführung des Auslastungstests zu starten.

    ![Konfigurieren von Auslastungstests][configure-performance-test]

    Der neue Test wird zunächst in die Warteschlange eingereiht und erhält dann den Status *In Bearbeitung*.

    ![Auslastungstest übermittelt und in die Warteschlange gestellt][load-test-queued]

    ![Ausführung des Auslastungstests in Bearbeitung][load-test-in-progress]

### <a name="step-2-start-a-profiler-on-demand-session"></a>Schritt 2: Starten einer bedarfsgesteuerten Profiler-Sitzung

1. Wenn der Auslastungstest ausgeführt wird, starten Sie Profiler, um Ablaufverfolgungen in der Web-App zu erfassen, während diese die Last empfängt.

1. Wechseln Sie zum Bereich **Profiler konfigurieren**.


### <a name="step-3-view-traces"></a>Schritt 3: Ablaufverfolgungen anzeigen

Wenn die Profiler-Ausführung abgeschlossen ist, folgen Sie den Anweisungen zur Benachrichtigung, um zum Bereich „Leistung“ zu wechseln und Ablaufverfolgungen anzuzeigen.

## <a name="troubleshoot-the-profiler-on-demand-session"></a>Beheben von Problemen bei bedarfsgesteuerten Profiler-Sitzungen

Nach einer bedarfsgesteuerten Sitzung empfangen Sie möglicherweise eine Fehlermeldung zu Profiler-Timeoutfehlern:

![Profiler-Timeoutfehler][profiler-timeout]

Dieser Fehler kann folgende Gründe haben:

* Die bedarfsgesteuerte Profiler-Sitzung war erfolgreich, aber Application Insights hat mehr Zeit als erwartet zum Verarbeiten der gesammelten Daten benötigt.  

  Wenn die Daten nicht innerhalb von 15 Minuten verarbeitet werden, wird im Portal eine Timeoutmeldung angezeigt. Kurze Zeit später werden jedoch Profiler-Ablaufverfolgungen angezeigt. Wenn Sie eine Fehlermeldung erhalten, ignorieren Sie sie zunächst. Wir arbeiten mit Hochdruck an der Lösung dieses Problems.

* Ihre Web-App verfügt über eine ältere Version des Profiler-Agent, die nicht über die On-Demand-Funktion verfügt.  

  Wenn Sie Application Insights Profiler bereits aktiviert haben, ist es wahrscheinlich, dass Sie Ihren Profiler-Agent aktualisieren müssen, um die On-Demand-Funktion verwenden zu können.
  
Wechseln Sie zum Bereich **App-Einstellungen** von App Services, und überprüfen Sie die folgenden Einstellungen:
* **APPINSIGHTS_INSTRUMENTATIONKEY**: Ersetzen Sie den Wert durch den ordnungsgemäßen Instrumentierungsschlüssel für Application Insights.
* **APPINSIGHTS_PORTALINFO**: ASP.NET
* **APPINSIGHTS_PROFILERFEATURE_VERSION**: 1.0.0

Wenn einer der obigen Werte nicht festgelegt wurde, installieren Sie die aktuelle Websiteerweiterung folgendermaßen:

1. Wechseln Sie im App Services-Portal zum Bereich **Application Insights**.

    ![Aktivieren von Application Insights im App Services-Portal][enable-app-insights]

1. Wenn im Bereich **Application Insights** eine Schaltfläche **Aktualisieren** angezeigt wird, wählen Sie sie aus, um die Application Insights-Websiteerweiterung zu aktualisieren, mit der der neueste Profiler-Agent installiert wird.

    ![Websiteerweiterung aktualisieren][update-site-extension]

1. Um sicherzustellen, dass Profiler aktiviert ist, wählen Sie **Ändern** und dann **OK** aus, um die Änderungen zu speichern.

    ![Ändern und Speichern von App Insights][change-and-save-appinsights]

1. Wechseln Sie zurück zum Bereich **App-Einstellungen** für App Service, um zu überprüfen, ob die folgenden Werte festgelegt wurden:
   * **APPINSIGHTS_INSTRUMENTATIONKEY**: Ersetzen Sie den Wert durch den ordnungsgemäßen Instrumentierungsschlüssel für Application Insights.
   * **APPINSIGHTS_PORTALINFO**: ASP.NET 
   * **APPINSIGHTS_PROFILERFEATURE_VERSION**: 1.0.0

     ![App-Einstellungen für Profiler][app-settings-for-profiler]

1. Wählen Sie optional **Erweiterungen** aus, und überprüfen Sie dann die Erweiterungsversion und ob ein Update verfügbar ist.

    ![Überprüfen auf ein Erweiterungsupdate][check-for-extension-update]

## <a name="next-steps"></a>Nächste Schritte
[Aktivieren von Profiler und Anzeigen von Ablaufverfolgungen](profiler-overview.md?toc=/azure/azure-monitor/toc.json)

[profiler-on-demand]: ./media/profiler-settings/Profiler-on-demand.png
[configure-profiler-entry]: ./media/profiler-settings/configure-profiler-entry.png
[create-performance-test]: ./media/profiler-settings/new-performance-test.png
[configure-performance-test]: ./media/profiler-settings/configure-performance-test.png
[load-test-queued]: ./media/profiler-settings/load-test-queued.png
[load-test-in-progress]: ./media/profiler-settings/load-test-inprogress.png
[enable-app-insights]: ./media/profiler-settings/enable-app-insights-blade-01.png
[update-site-extension]: ./media/profiler-settings/update-site-extension-01.png
[change-and-save-appinsights]: ./media/profiler-settings/change-and-save-appinsights-01.png
[app-settings-for-profiler]: ./media/profiler-settings/appsettings-for-profiler-01.png
[check-for-extension-update]: ./media/profiler-settings/check-extension-update-01.png
[profiler-timeout]: ./media/profiler-settings/profiler-timeout.png
