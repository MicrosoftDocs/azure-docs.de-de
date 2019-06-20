---
title: Überwachen der Verfügbarkeit und Reaktionsfähigkeit von Websites | Microsoft Docs
description: Richten Sie Webtests in Application Insights ein. Erhalten Sie Benachrichtigungen, wenn eine Website nicht mehr zur Verfügung steht oder langsam reagiert.
services: application-insights
documentationcenter: ''
author: lgayhardt
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/22/2019
ms.reviewer: sdash
ms.author: lagayhar
ms.openlocfilehash: 76bbcd6fa400111514ec3496005a28ec28ae6ab7
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65977902"
---
# <a name="monitor-availability-and-responsiveness-of-any-web-site"></a>Überwachen der Verfügbarkeit und Reaktionsfähigkeit von Websites
Nachdem Sie die Web-App oder Website an einen beliebigen Server bereitgestellt haben, können Sie Tests einrichten, um die Verfügbarkeit und Reaktionsfähigkeit zu überwachen. [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) sendet regelmäßig Webanforderungen von verschiedenen Punkten auf der ganzen Welt an Ihre Anwendung. Sie werden benachrichtigt, wenn Ihre Anwendung langsam oder gar nicht reagiert.

Sie können für jeden HTTP- oder HTTPS-Endpunkt, der über das öffentliche Internet zugänglich ist, Verfügbarkeitstests einrichten. Der zu testenden Website muss nichts hinzugefügt werden. Es muss sich nicht einmal um Ihre Website handeln: Sie können auch einen benötigten REST-API-Dienst testen.

Es gibt zwei Arten von Verfügbarkeitstests:

* [URL-Pingtest](#create): Dies ist ein einfacher Test, den Sie im Azure-Portal erstellen können.
* [Mehrstufiger Webtest](#multi-step-web-tests): Diesen Test erstellen Sie in Visual Studio Enterprise und laden ihn in das Portal hoch.

Sie können bis zu 100 Verfügbarkeitstests pro Anwendungsressource erstellen.


## <a name="create"></a>Öffnen einer Ressource für Ihre Verfügbarkeitstestberichte

**Falls Sie Application Insights bereits für Ihre Web-App konfiguriert haben**, können Sie die dazugehörige Application Insights-Ressource im [Azure-Portal](https://portal.azure.com) öffnen.

**Wenn Sie Ihre Berichte in einer neuen Ressource anzeigen möchten**, können Sie zum [Azure-Portal](https://portal.azure.com) navigieren und eine Application Insights-Ressource erstellen.

![Erstellen einer Ressource > Entwicklertools > Application Insights](./media/monitor-web-app-availability/1create-resource-appinsights.png)

Klicken Sie auf **All resources** (Alle Ressourcen), um das Blatt „Übersicht“ für die neue Ressource zu öffnen.

## <a name="setup"></a>Erstellen eines URL-Pingtests
Öffnen Sie das Blatt „Verfügbarkeit“, und fügen Sie einen Test hinzu.

![Mindestens die URL der Website eintragen](./media/monitor-web-app-availability/2addtest-url.png)

* **Die URL** kann eine beliebige Webseite sein, die Sie testen möchten, aber sie muss über das öffentliche Internet sichtbar sein. Die URL kann eine Abfragezeichenfolge enthalten. So können Sie beispielsweise Ihre Datenbank abfragen. Wenn die URL in eine Umleitung aufgelöst wird, werden bis zu 10 Umleitungen verfolgt.
* **Abhängige Anforderungen analysieren**: Wenn diese Option aktiviert ist, werden beim Test Bilder, Skripts, Formatdateien und andere Dateien angefordert, die Teil der zu testenden Webseite sind. Die aufgezeichnete Antwortzeit enthält auch die Zeit, die zum Abrufen dieser Dateien erforderlich ist. Der Test schlägt fehl, wenn alle diese Ressourcen innerhalb des Zeitlimits für den gesamten Test nicht erfolgreich heruntergeladen werden können. Wenn die Option nicht aktiviert ist, wird beim Test nur die Datei unter der von Ihnen angegebenen URL angefordert.

* **Wiederholungen aktivieren**:  Wenn diese Option aktiviert ist, wird der Test nach kurzer Zeit wiederholt, falls er fehlschlägt. Nur wenn drei aufeinander folgende Versuche scheitern, wird ein Fehler gemeldet. Nachfolgende Tests werden dann in der üblichen Häufigkeit ausgeführt. Die Wiederholung wird bis zum nächsten Erfolg vorübergehend eingestellt. Diese Regel wird an jedem Teststandort unabhängig angewendet. Wir empfehlen Ihnen, diese Option zu verwenden. Im Durchschnitt treten ca. 80% der Fehler bei einer Wiederholung nicht mehr auf.

* **Testhäufigkeit**: Legt fest, wie oft der Test von jedem Teststandort aus ausgeführt wird. Mit einer Standardfrequenz von fünf Minuten und fünf Teststandorten wird Ihre Website im Durchschnitt jede Minute getestet.

* **Teststandorte** sind die Orte, von denen aus unsere Server Webanforderungen an Ihre URL senden. Die empfohlene Mindestanzahl für Teststandorte beträgt fünf, um sicherzustellen, dass Sie Probleme mit Ihrer Website von Netzwerkproblemen unterscheiden können. Sie können bis zu 16 Standorte auswählen.

> [!NOTE]
> * Es wird dringend empfohlen, Tests von mehreren Standorten auszuführen (mindestens 5 Standorte). Dies dient dazu, Fehlalarme zu vermeiden, die durch vorübergehende Probleme an einem bestimmten Standort entstehen können. Darüber hinaus haben wir festgestellt, dass in einer optimalen Konfiguration die Anzahl von Teststandorten dem Warnungsschwellenwert für Standorte + 2 entspricht. 
> * Das Aktivieren der Option „Abhängige Anforderungen analysieren“ resultiert in einer strengeren Überprüfung. Der Test könnte bei Fällen fehlschlagen, was möglicherweise nicht auffällt, wenn Sie den Standort manuell durchsuchen.

* **Erfolgskriterien**:

    **Timeout für Tests**: Reduzieren Sie diesen Wert, um über langsame Antworten benachrichtigt zu werden. Der Test wird als ein Fehler gezählt, wenn die Antworten von Ihrer Website nicht innerhalb dieses Zeitraums empfangen wurden. Bei Auswahl von **Abhängige Anforderungen analysieren**müssen alle Bilder, Styledateien, Skripts und anderen abhängigen Ressourcen innerhalb dieses Zeitraums empfangen werden.

    **HTTP-Antwort**: Der zurückgegebene Statuscode, der als Erfolg gezählt wird. 200 ist der Code, der angibt, dass eine normale Webseite zurückgegeben wurde.

    **Inhaltsübereinstimmung**: Eine Zeichenfolge, zum Beispiel „Willkommen!“ Wir vergewissern uns, dass in jeder Antwort eine exakte Übereinstimmung unter Berücksichtigung der Groß-und Kleinschreibung vorkommt. Dies muss eine Zeichenfolge in Klartext, ohne Platzhalter sein. Vergessen Sie nicht, diese zu aktualisieren, wenn sich der Seiteninhalt ändert. **Inhaltsübereinstimmungen werden derzeit nur für englische Zeichen unterstützt.** 

* **Schwellenwert für den Warnungsspeicherort**: Es wird ein Mindestwert von 3/5 Standorten empfohlen. Das optimale Verhältnis zwischen dem Warnungsschwellenwert für Standorte und der Anzahl von Teststandorten lautet **Warnungsschwellenwert für Standort** = **Anzahl von Teststandorten** – 2, bei einer Mindestanzahl von fünf Teststandorten.

## <a name="multi-step-web-tests"></a>Webtests mit mehreren Schritten
Sie können ein Szenario überwachen, das eine Sequenz mit mehreren URLs umfasst. Wenn Sie zum Beispiel eine Verkaufswebsite überwachen, können Sie testen, ob das Hinzufügen von Artikeln zum Einkaufswagen ordnungsgemäß funktioniert.

> [!NOTE]
> Für Webtests mit mehreren Schritten wird eine Gebühr erhoben. Informationen hierzu finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/application-insights/).
> 

Um einen mehrstufigen Test zu erstellen, zeichnen das Szenario mit Visual Studio Enterprise auf. Laden Sie dann die Aufzeichnung in Application Insights hoch. Application Insights wiederholt das Szenario in bestimmten Abständen und überprüft die Antworten.

> [!NOTE]
> * Es ist nicht möglich, in Ihren Tests codierte Funktionen oder Schleifen zu verwenden. Der Test muss vollständig im WEBTEST-Skript enthalten sein. Sie können aber Standard-Plug-Ins nutzen.
> * In den Webtests mit mehreren Schritten werden nur Zeichen der englischen Sprache unterstützt. Wenn Sie Visual Studio in anderen Sprachen verwenden, aktualisieren Sie die Webtest-Definitionsdatei, sodass nicht-englische Zeichen übersetzt/ausgeschlossen werden.
>

#### <a name="1-record-a-scenario"></a>1. Aufzeichnen eines Szenarios
Verwenden Sie Visual Studio Enterprise, um eine Websitzung aufzuzeichnen.

1. Erstellen Sie ein Webleistungstest-Projekt.

    ![Erstellen Sie in der Visual Studio Enterprise Edition ein Projekt aus der Vorlage für Webleistungs- und Auslastungstests.](./media/monitor-web-app-availability/appinsights-71webtest-multi-vs-create.png)

 * *Wird die Vorlage für Webleistungs- und Auslastungstests nicht angezeigt?* - Schließen Sie Visual Studio Enterprise. Öffnen Sie den **Visual Studio-Installer**, um Ihre Visual Studio Enterprise-Installation zu ändern. Wählen Sie unter **Einzelne Komponenten** die Option **Tools für Webleistung und Auslastungstests**.

2. Öffnen Sie die .webtest-Datei und starten Sie die Aufzeichnung.

    ![Öffnen Sie die WEBTEST-Datei, und klicken Sie auf "Aufzeichnen".](./media/monitor-web-app-availability/appinsights-71webtest-multi-vs-start.png)
3. Führen Sie die Benutzeraktionen durch, die Sie im Test simulieren möchten: Öffnen Sie Ihre Website, fügen Sie dem Warenkorb ein Produkt hinzu usw. Beenden Sie dann den Test.

    ![Die Webtestaufzeichnung wird im Internet Explorer ausgeführt.](./media/monitor-web-app-availability/appinsights-71webtest-multi-vs-record.png)

    Erstellen Sie kein langes Szenario. Es gibt eine Beschränkung von 100 Schritten und 2 Minuten.
4. Bearbeiten Sie den Test:

   * Fügen Sie Validierungen zum Überprüfen der empfangenen Text- und Antwortcodes hinzu.
   * Entfernen Sie alle überflüssigen Interaktionen. Sie können auch abhängige Anforderungen für Bilder oder für Werbe- oder Nachverfolgungswebsites entfernen.

     Denken Sie daran, dass Sie nur das Testskript bearbeiten können. Sie können keinen benutzerdefinierten Code hinzufügen oder andere Webtests aufrufen. Fügen Sie keine Schleifen in den Test ein. Sie können standardmäßige Webtest-Plug-Ins verwenden.
5. Führen Sie den Test in Visual Studio aus, um sicherzustellen, dass er funktioniert.

    Das Webtest-Ausführungsprogramm öffnet einen Webbrowser und wiederholt die aufgezeichneten Aktionen. Stellen Sie sicher, dass der Test wie erwartet funktioniert.

    ![Öffnen Sie in Visual Studio die WEBTEST-Datei, und klicken Sie auf "Ausführen".](./media/monitor-web-app-availability/appinsights-71webtest-multi-vs-run.png)

#### <a name="2-upload-the-web-test-to-application-insights"></a>2. Hochladen des Webtests in Application Insights
1. Klicken Sie im Application Insights-Portal auf dem Blatt „Verfügbarkeit“ auf „Test hinzufügen“.

    ![Wählen Sie auf dem Blatt „Verfügbarkeit“ die Option „Test hinzufügen“ aus.](./media/monitor-web-app-availability/3addtest-web.png)
2. Wählen Sie einen mehrstufigen Test aus und laden Sie die .webtest-Datei hoch.

    Legen Sie die Teststandorte, Häufigkeit und Warnungsparameter auf die gleiche Weise wie für Ping-Tests fest.

### <a name="plugging-time-and-random-numbers-into-your-multi-step-test"></a>Einfügen von Zeiten und Zufallszahlen in einen mehrstufigen Test
Angenommen, Sie testen ein Tool, das zeitabhängige Daten, wie z. B. Bestände, aus einem externen Feed abruft. Beim Aufzeichnen eines Webtests müssen Sie bestimmte Zeiten verwenden, aber als Parameter des Tests – StartTime und EndTime – festlegen.

![Ein Webtest mit Parametern.](./media/monitor-web-app-availability/appinsights-72webtest-parameters.png)

Wenn Sie den Test ausführen, sollte "EndTime" immer die aktuelle Uhrzeit widerspiegeln, und "StartTime" sollte der Uhrzeit vor 15 Minuten entsprechen.

Webtest-Plug-Ins sind eine Möglichkeit zum Parametrisieren von Zeiten.

1. Fügen Sie ein Webtest-Plug-In für jeden gewünschten Variablenparameterwert hinzu. Wählen Sie in der Webtest-Symbolleiste **Webtest-Plug-In hinzufügen**aus.

    ![Wählen Sie "Webtest-Plug-In hinzufügen", und wählen Sie einen Typ aus.](./media/monitor-web-app-availability/appinsights-72webtest-plugins.png)

    In diesem Beispiel verwenden wir zwei Instanzen des Datums-/Uhrzeit-Plug-Ins. Eine Instanz gilt für „vor 15 Minuten“ und eine weitere für „jetzt“.
2. Öffnen Sie die Eigenschaften der einzelnen Plug-Ins. Geben Sie einen Namen ein, und legen Sie es so fest, dass die aktuelle Uhrzeit verwendet wird. Legen Sie für eines der Plug-Ins "Add Minutes = -15" fest.

    ![Legen Sie den Name fest, wählen Sie "Aktuelle Uhrzeit verwenden" und "Minuten hinzufügen".](./media/monitor-web-app-availability/appinsights-72webtest-plugin-parameters.png)
3. Verwenden Sie im Webtestparameter {{Plug-In-Name}} zum Verweisen auf einen Plug-In-Namen.

    ![Verwenden Sie im Testparameter "{{Plug-In-Name}}".](./media/monitor-web-app-availability/appinsights-72webtest-plugin-name.png)

Laden Sie nun den Test in das Portal hoch. Bei jeder Ausführung des Tests werden die dynamischen Werte verwendet.


## <a name="monitor"></a>Anzeigen der Verfügbarkeitstestergebnisse

Auf der Registerkarte „Übersicht“ wird die Erfolgsrate der Tests angezeigt, während auf der Registerkarte „Details“ ein Punktdiagramm und Raster mit spezifischen Details angezeigt wird.

Klicken Sie nach einigen Minuten auf **Aktualisieren**, um die Testergebnisse anzuzeigen.

![Punktdiagramm auf Blatt „Details“](./media/monitor-web-app-availability/4refresh.png)

Das Punktdiagramm zeigt Stichproben der Testergebnisse an, die Diagnosedetails zu Testschritten enthalten. Die Test-Engine speichert Diagnosedetails für Tests mit Fehlern. Für erfolgreiche Tests werden Diagnosedetails für eine Teilmenge der Ausführungen gespeichert. Bewegen Sie den Mauszeiger über einen der grünen oder roten Punkte, um Zeitstempel, Dauer, Standort und Name des Tests anzuzeigen. Klicken Sie auf einen Punkt im Punktdiagramm, um die Details des Testergebnisses anzuzeigen.  

Wählen Sie einen bestimmten Test oder Standort aus, oder verringern Sie den Zeitraum, um weitere Ergebnisse um den gewünschten Zeitraum anzuzeigen. Verwenden Sie den Suchexplorer, um Ergebnisse von allen Ausführungen anzuzeigen, oder Analytics-Abfragen, um benutzerdefinierte Berichte für diese Daten auszuführen.

Zusätzlich zu den reinen Ergebnissen gibt es im Metrik-Explorer zwei Verfügbarkeitsmetriken: 

1. Verfügbarkeit: Prozentsatz der erfolgreichen Tests für alle Testausführungen. 
2. Testdauer: Durchschnittliche Testdauer für alle Testausführungen.

Sie können Filter auf den Testnamen oder Standort anwenden, um Trends eines bestimmten Tests und/oder Standorts zu analysieren.

## <a name="edit"></a> Überprüfen und Bearbeiten der Tests

Wählen Sie auf der Registerkarte „Details“ für einen bestimmten Test die Auslassungspunkte ganz rechts aus, um ihn zu bearbeiten, vorübergehend zu deaktivieren, zu löschen oder um einen Webtest herunterzuladen. Es kann bis zu 20 Minuten dauern, bevor Konfigurationsänderungen weitergegeben werden.

Wählen Sie für einen bestimmten Test **Testdetails anzeigen** aus, um sein Punktdiagramm und spezifische Teststandortdetails anzuzeigen.

![Testdetails anzeigen, Webtest bearbeiten und deaktivieren](./media/monitor-web-app-availability/5viewdetails.png)

Eventuell möchten Sie Verfügbarkeitstests oder die damit verknüpften Warnungsregeln deaktivieren, während Sie Ihren Dienst warten.

![Deaktivieren eines Webtests](./media/monitor-web-app-availability/6disable.png)  
![Bearbeiten eines Tests](./media/monitor-web-app-availability/8edittest.png)

## <a name="failures"></a>Wenn Sie Fehler finden ...
Klicken Sie auf einen roten Punkt.

![Auf einen roten Punkt klicken](./media/monitor-web-app-availability/open-instance-3.png)

Aus einem Verfügbarkeitstestergebnis können Sie die Transaktionsdetails für alle Komponenten ablesen. Mögliche nächste Schritte:

* Untersuchen Sie die vom Server erhaltene Antwort.
* Diagnostizieren Sie den Fehler mit korrelierten serverseitigen Telemetriedaten, die während der Verarbeitung des fehlerhaften Verfügbarkeitstests gesammelt wurden.
* Protokollieren Sie in Git oder Azure Boards ein Problem oder eine Arbeitsaufgabe, um das Problem nachzuverfolgen. Der Fehler enthält einen Link zu diesem Ereignis.
* Öffnen Sie das Webtestergebnis in Visual Studio.

Weitere Informationen zur End-to-End-Transaktionsdiagnoseerfahrung finden Sie [hier](../../azure-monitor/app/transaction-diagnostics.md).

Klicken Sie auf die Ausnahmezeile, um die Details der serverseitigen Ausnahme anzuzeigen, die zum Fehlschlagen des synthetischen Verfügbarkeitstest geführt hat. Sie können auch die [Debugmomentaufnahme](../../azure-monitor/app/snapshot-debugger.md) abrufen, um eine umfangreichere Diagnose auf Codeebene durchzuführen.

![Serverseitige Diagnose](./media/monitor-web-app-availability/open-instance-4.png)

## <a name="alerts"></a> Verfügbarkeitswarnungen
Sie können die folgenden Typen von Warnungsregeln für Verfügbarkeitsdaten haben und dabei die klassische Warnungserfahrung verwenden:
1. X von Y Standorten melden Fehler in einem Zeitraum
2. Der aggregierte Prozentsatz der Verfügbarkeit fällt unter einen Schwellenwert
3. Die durchschnittliche Testdauer steigt über einen Schwellenwert

### <a name="alert-on-x-out-of-y-locations-reporting-failures"></a>Warnung bei X von Y Standorten, die Fehler melden
Die Warnungsregel „X von Y Standorten“ ist in der [neuen einheitlichen Warnungserfahrung](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts) standardmäßig aktiviert, wenn Sie einen neuen Verfügbarkeitstest erstellen. Sie können sich dagegen entscheiden, indem Sie die „klassische“ Option auswählen oder die Warnungsregel deaktivieren.

![Erstellen einer Erfahrung](./media/monitor-web-app-availability/appinsights-71webtestUpload.png)

> [!NOTE]
>  Mit den [neuen einheitlichen Warnungen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts), **müssen** der Schweregrad der Warnungsregel sowie die Benachrichtigungseinstellungen mit [Aktionsgruppen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) in der Warnungserfahrung konfiguriert werden. Ohne die folgenden Schritten erhalten Sie nur portalinterne Benachrichtigungen.

1. Nach dem Speichern des Verfügbarkeitstests, klicken Sie auf der Registerkarte „Details“ auf die Auslassungspunkte neben dem Test, den Sie gerade erstellt haben. Klicken Sie auf „Warnung bearbeiten“.
![Nach dem Speichern bearbeiten](./media/monitor-web-app-availability/9editalert.png)

2. Legen Sie den gewünschten Schweregrad, die Regelbeschreibung und vor allem die Aktionsgruppe fest, die über die Benachrichtigungseinstellungen verfügt, die Sie für diese Warnungsregel verwenden möchten.
![Nach dem Speichern bearbeiten](./media/monitor-web-app-availability/10editalert.png)


> [!NOTE]
> * Konfigurieren Sie die Aktionsgruppen so, dass sie Benachrichtigungen empfangen, wenn die Warnung ausgelöst wird, indem Sie die oben beschriebenen Schritte ausführen. Ohne diese Schritte erhalten Sie nur portalinterne Benachrichtigungen, wenn die Regel ausgelöst wird.
>

### <a name="alert-on-availability-metrics"></a>Warnung bei Verfügbarkeitsmetriken
Mithilfe der [neuen einheitlichen Warnungen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts) können Sie sowohl bei segmentierter aggregierter Verfügbarkeit als auch bei Testdauermetriken warnen:

1. Wählen Sie eine Application Insights-Ressource in der Metrikenerfahrung aus, und wählen Sie eine Verfügbarkeitsmetrik aus:  ![Verfügbarkeitsmetrikenauswahl](./media/monitor-web-app-availability/selectmetric.png)

2. Wenn Sie die Option „Warnungen“ über das Menü konfigurieren, gelangen Sie zur neuen Erfahrung, wo Sie bestimmte Tests oder Standorte auswählen können, für die Warnungsregeln eingerichtet werden sollen. Sie können hier außerdem die Aktionsgruppen für diese Warnungsregel konfigurieren.
    ![Konfiguration von Verfügbarkeitswarnungen](./media/monitor-web-app-availability/availabilitymetricalert.png)

### <a name="alert-on-custom-analytics-queries"></a>Warnung bei benutzerdefinierten Analyseabfragen
Mithilfe der [neuen einheitlichen Warnungen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts) können Sie bei [benutzerdefinierten Protokollabfragen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log) warnen. Mit benutzerdefinierten Abfragen können Sie bei jeder beliebigen Bedingung warnen, die Ihnen hilft, das zuverlässigste Signal für Verfügbarkeitsprobleme abzurufen. Dies ist insbesondere anwendbar, wenn Sie benutzerdefinierte Verfügbarkeitsergebnisse mithilfe des TrackAvailability SDKs senden. 

> [!Tip]
> * Die Metriken zu Verfügbarkeitsdaten umfassen alle benutzerdefinierten Verfügbarkeitsergebnisse, die Sie möglicherweise durch Aufrufen unseres TrackAvailability SDKs übermitteln. Sie können die Unterstützung für Warnungen bei Metriken verwenden, um bei benutzerdefinierten Verfügbarkeitsergebnissen zu warnen.
>

## <a name="dealing-with-sign-in"></a>Umgang mit der Anmeldung
Wenn sich Benutzer bei Ihrer App anmelden, stehen Ihnen verschiedene Optionen für die Anmeldungssimulation zur Verfügung, damit Sie Seiten testen können, die auf die Anmeldung folgen. Der verwendete Ansatz hängt vom Typ der von der App bereitgestellten Sicherheit ab.

In allen Fällen sollten Sie in der Anwendung ein Konto erstellen, das nur Testzwecken dient. Schränken Sie die Berechtigungen dieses Testkontos nach Möglichkeit ein, damit ausgeschlossen ist, dass sich die Webtests auf echte Benutzer auswirken.

### <a name="simple-username-and-password"></a>Einfacher Benutzername und Kennwort
Zeichnen Sie einen Webtest auf normale Weise auf. Löschen Sie zuerst Cookies.

### <a name="saml-authentication"></a>SAML-Authentifizierung
Verwenden Sie für Webtests das verfügbare SAML-Plug-In.

### <a name="client-secret"></a>Geheimer Clientschlüssel
Verwenden Sie diese Route, wenn die Anmelderoute Ihrer App einen geheimen Clientschlüssel umfasst. Azure Active Directory (AAD) ist ein Beispiel für einen Dienst, bei dem eine Anmeldung mit geheimem Clientschlüssel bereitgestellt wird. In AAD ist der geheime Clientschlüssel der App-Schlüssel.

Hier ist ein Beispiel für einen Webtest einer Azure-Web-App mit App-Schlüssel angegeben:

![Beispiel für geheimen Clientschlüssel](./media/monitor-web-app-availability/110.png)

1. Rufen Sie das Token aus AAD mit dem geheimen Clientschlüssel (AppKey) ab.
2. Extrahieren Sie das Bearertoken aus der Antwort.
3. Rufen Sie die API mit dem Bearertoken im Autorisierungsheader auf.

Stellen Sie sicher, dass der Webtest ein richtiger Client ist – also über eine eigene App in AAD verfügt –, und verwenden Sie dessen Client-ID und App-Schlüssel (clientId und appkey). Ihr getesteter Dienst verfügt ebenfalls über eine eigene App in AAD: Der appID-URI dieser App wird im Webtest im Feld „resource“ angegeben.

### <a name="open-authentication"></a>Offene Authentifizierung
Ein Beispiel für die offene Authentifizierung ist die Anmeldung mit Ihrem Microsoft- oder Google-Konto. Viele Apps, die OAuth verwenden, stellen die Alternative mit dem geheimen Clientschlüssel bereit. Ihre erste Taktik sollte also darin bestehen, diese Möglichkeit zu untersuchen.

Wenn bei Ihrem Test die Anmeldung mit OAuth erforderlich ist, lautet die allgemeine Vorgehensweise wie folgt:

* Verwenden Sie ein Tool wie Fiddler, um den Datenverkehr zwischen Ihrem Webbrowser, der Authentifizierungswebsite und Ihrer App zu untersuchen.
* Führen Sie mindestens zwei Anmeldungen auf verschiedenen Computern bzw. in verschiedenen Browsern oder mit großen zeitlichen Abständen durch (damit Token ablaufen können).
* Vergleichen Sie verschiedene Sitzungen, um das von der authentifizierenden Website zurückgegebene Token zu identifizieren, das nach der Anmeldung dann an Ihren App-Server übergeben wird.
* Zeichnen Sie einen Webtests mit Visual Studio auf.
* Parametrisieren Sie die Token, indem Sie den Parameter festlegen, wenn das Token vom Authentifikator zurückgegeben wird, und ihn in der Abfrage an die Website verwenden.
  (Visual Studio versucht, den Test zu parametrisieren, aber die Token werden nicht richtig parametrisiert.)

## <a name="performance-tests"></a>Leistungstests
> [!NOTE]  
> Der cloudbasierte Auslastungstestdienst ist veraltet. Weitere Informationen zu Veralten, Dienstverfügbarkeit und alternativen Diensten finden Sie [hier](https://docs.microsoft.com/azure/devops/test/load-test/overview?view=azure-devops).

Sie können für Ihre Website einen Auslastungstest durchführen. Wie beim Verfügbarkeitstest auch, können Sie entweder einfache Anforderungen oder Anforderungen mit mehreren Schritten von unseren weltweit vorhandenen Punkten senden. Im Gegensatz zu einem Verfügbarkeitstest werden viele Anforderungen gesendet, um mehrere gleichzeitige Benutzer zu simulieren.

Wechseln Sie unter **Konfigurieren**, zu **Leistungstests**, und klicken Sie auf „Neu“, um einen Test zu erstellen.

![Erstellen eines neuen Leistungstest](./media/monitor-web-app-availability/11new-performance-test.png)

Nach Abschluss des Tests werden die Antwortzeiten und Erfolgsraten angezeigt.

![Leistungstestergebnisse](./media/monitor-web-app-availability/12performance-test.png)

> [!TIP]
> Verwenden Sie zum Beobachten der Auswirkungen eines Leistungstests [Livestream](../../azure-monitor/app/live-stream.md) und [Profiler](../../azure-monitor/app/profiler.md).
>

## <a name="automation"></a>Automation
* [Verwenden Sie PowerShell-Skripts zum automatischen Einrichten eines Verfügbarkeitstests](../../azure-monitor/app/powershell.md#add-an-availability-test).
* Richten Sie einen [Webhook](../../azure-monitor/platform/alerts-webhooks.md) ein, der bei einer Warnung aufgerufen wird.

## <a name="qna"></a> Häufig gestellte Fragen (FAQ)

* *Die Website sieht korrekt aus, aber ich sehe Testfehler. Warum warnt mich Application Insights?*

    * Hat Ihr Test „Abhängige Anforderungen analysieren“ aktiviert? Dies führt zu einer strengen Überprüfung von Ressourcen wie Skripts, Bildern usw. Diese Typen von Fehlern machen sich in einem Browser möglicherweise nicht bemerkbar. Überprüfen Sie alle Bilder, Skripts, Stylesheets und anderen Dateien, die von der Seite geladen werden. Wenn eines dieser Elemente einen Fehler verursacht, wird der Test auch dann als fehlerhaft gemeldet, wenn die HTML-Hauptseite problemlos geladen wird. Deaktivieren Sie einfach in der Testkonfiguration die Option „Abhängige Anforderungen analysieren“, um solche Ressourcenfehler vom Test auszuschließen. 

    * Stellen Sie sicher, dass die Konfigurationsoption „Enable retries for test failures" (Wiederholungen bei Testfehlern zulassen) aktiviert ist, um Störungen infolge vorübergehender Netzwerkprobleme zu verringern. Sie können den Test auch an mehreren Standorten durchführen und den Schwellenwert der Warnungsregel entsprechen verwalten, um zu verhindern, dass standortspezifische Probleme übermäßige Warnungen auslösen.

    * Klicken Sie auf einen der roten Punkte in der Verfügbarkeitserfahrung oder auf einen Verfügbarkeitsfehler im Such-Explorer, um die Details anzuzeigen, warum wir den Fehler gemeldet haben. Das Testergebnis sollte, zusammen mit der korrelierten serverseitigen Telemetrie (sofern aktiviert), Aufschluss darüber geben, warum der Test nicht erfolgreich war. Häufige Ursachen für vorübergehende Probleme sind Netzwerk- oder Verbindungsprobleme. 

    * Kam es beim Test zum Timeout? Wir brechen Tests nach 2 Minuten ab. Wenn Ihr Ping- oder mehrstufiger Test länger als zwei Minuten dauert, melden wir ihn als Fehler. Erwägen Sie, den Test in mehrere aufzuteilen, die sich schneller abschließen lassen.

    * Haben allel Standorte Fehler gemeldet oder nur einige? Wenn nur einige Standorte Fehler gemeldet haben, kann es an Netzwerk-/CDN-Problemen liegen. Auch in diesem Fall sollte das Klicken auf die roten Punkte Ihnen zu einem besseren Verständnis verhelfen, warum der Standort Fehler gemeldet hat.

* *Ich habe keine E-Mail erhalten, als die Warnung ausgelöst oder behoben wurde, oder beides?*

    Überprüfen Sie die klassische Warnungskonfiguration, um zu bestätigen, dass Ihre E-Mail-Adresse direkt aufgeführt ist, oder dass eine Verteilerliste, deren Mitglied Sie sind, für den Empfang von Benachrichtigungen konfiguriert ist. Ist dies der Fall, überprüfen Sie die Konfiguration der Verteilerliste, um zu bestätigen, dass sie externe E-Mails empfangen kann. Überprüfen Sie außerdem, ob Ihr E-Mail-Administrator eventuell Richtlinien konfiguriert hat, die dieses Problem verursachen können.

* *Ich habe die Webhookbenachrichtigung nicht empfangen.*

    Überprüfen Sie, ob die Anwendung, die die Webhookbenachrichtigung empfängt, verfügbar ist und die Webhookanforderungen erfolgreich verarbeitet. Weitere Informationen finden Sie [hier](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook).

* *Zeitweiliger Testfehler aufgrund einer Protokollverletzung?*

    Der Fehler („protocol violation..CR must be followed by LF“) weist auf ein Problem mit dem Server (oder mit Abhängigkeiten) hin. Er wird angezeigt, wenn falsch formatierte Header in der Antwort festgelegt werden. Der Fehler kann durch Lastenausgleichsmodule oder CDNs verursacht werden. Genauer gesagt geben einige Header unter Umständen das Zeilenende nicht mit CRLF an, was gegen die HTTP-Spezifikation verstößt. Daher schlägt die Validierung auf .NET-WebRequest-Ebene fehl. Überprüfen Sie die Antwort, um Header zu ermitteln, die gegen diese Spezifikation verstoßen.
    
    Hinweis: Bei der URL tritt in Browsern mit einer nicht so strengen Validierung von HTTP-Headern unter Umständen kein Fehler auf. Eine ausführliche Erläuterung des Problems finden Sie in diesem Blogbeitrag: http://mehdi.me/a-tale-of-debugging-the-linkedin-api-net-and-http-protocol-violations/.  
    
* *Ich sehe keine zugehörigen serverseitigen Telemetriedaten zum Diagnostizieren von Testfehlern.*
    
    Wenn Sie Application Insights für Ihre serverseitige Anwendung eingerichtet haben, liegt dies möglicherweise daran, dass [Sampling](../../azure-monitor/app/sampling.md) in Betrieb ist. Wählen Sie ein anderes Verfügbarkeitsergebnis aus.

* *Kann ich Code aus meinem Webtest aufrufen?*

    Nein. Die Schritte des Tests müssen in der Webtest-Datei enthalten sein. Und Sie können keine anderen Webtests aufrufen oder Schleifen verwenden. Aber es gibt mehrere hilfreiche Plug-Ins.

* *Wird HTTPS unterstützt?*

    Wir unterstützen TLS 1.1 und TLS 1.2. Derzeit führen wir keine Überprüfung auf HTTPS-Zertifikatfehler durch.  

* *Gibt es einen Unterschied zwischen „Webtests“ und „Verfügbarkeitstests“?*

    Die beiden Begriffe sind austauschbar. „Verfügbarkeitstests“ ist ein allgemeinerer Begriff, der neben den mehrstufigen Webtests auch die einzelnen URL-Pingtests enthält.
    
* *Ich möchte Verfügbarkeitstests auf unserem internen Server verwenden, der hinter einer Firewall ausgeführt wird.*

    Es gibt zwei mögliche Lösungen:
    
    * Konfigurieren Sie die Firewall so, dass eingehende Anforderungen von den [IP-Adressen der Webtest-Agents](../../azure-monitor/app/ip-addresses.md) zugelassen werden.
    * Schreiben Sie eigenen Code zum regelmäßigen Testen Ihres internen Servers. Führen Sie den Code als Hintergrundprozess auf einem Testserver hinter Ihrer Firewall aus. Die Ergebnisse des Testvorgangs können an Application Insights gesendet werden, indem die [TrackAvailability()](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability)-API im Core SDK-Paket verwendet wird. Hierfür ist es erforderlich, dass Ihr Testserver Zugriff in ausgehender Richtung auf den Application Insights-Erfassungsendpunkt hat. Dies ist aber ein deutlich geringeres Sicherheitsrisiko als bei der Alternativlösung, bei der eingehende Anforderungen zugelassen werden. Die Ergebnisse werden nicht auf den Blättern der Verfügbarkeitswebtests angezeigt, sondern als Verfügbarkeitsergebnisse in Analytics, Search und Metrik-Explorer.

* *Fehler beim Hochladen eines Webtests mit mehreren Schritten*

    Einige Gründe, warum dies geschehen kann:
    * Die Größenbeschränkung beträgt 300 K.
    * Schleifen werden nicht unterstützt.
    * Verweise auf andere Webtests werden nicht unterstützt.
    * Datenquellen werden nicht unterstützt.

* *Test mit mehreren Schritten wird nicht abgeschlossen*

    Pro Test können maximal 100 Anforderungen verwendet werden. Der Test wird außerdem beendet, wenn seine Ausführung länger als zwei Minuten dauert.

* *Wie kann ich einen Test mit Clientzertifikaten durchführen?*

    Dies wird leider nicht unterstützt.

## <a name="who-receives-the-classic-alert-notifications"></a>Wer erhält die (klassischen) Warnungsbenachrichtigungen?

Dieser Abschnitt gilt nur für klassische Benachrichtigungen und hilft Ihnen, Ihre Warnungsbenachrichtigungen zu optimieren, um sicherzustellen, dass nur die gewünschten Empfänger Benachrichtigungen erhalten. Um mehr über den Unterschied zwischen [klassischen Benachrichtigungen](../platform/alerts-classic.overview.md) und der neuen Benutzeroberfläche für Warnungen zu erfahren, lesen Sie den Artikel [Überblick über Warnungen in Microsoft Azure](../platform/alerts-overview.md). Um die Warnungsbenachrichtigung in der neuen Benutzeroberfläche für Warnungen zu steuern, verwenden Sie [Aktionsgruppen](../platform/action-groups.md).

* Wir empfehlen die Verwendung bestimmter Empfänger für klassische Warnungsbenachrichtigungen.

* Für Warnungen zu Fehlern bei X von Y Standorten werden bei aktivierter Option **Massenversand/Gruppe** Benutzer mit Administrator-/Co-Administratorrolle benachrichtigt.  Im Wesentlichen erhalten _alle_ Administratoren des _Abonnements_ Benachrichtigungen.

* Für Warnungen zu Verfügbarkeitsmetriken (oder beliebigen Application Insights-Metriken zu diesem Zweck) werden bei aktivierter Option **Massenversand/Gruppe** Benachrichtigungen an Benutzer mit der Rolle „Besitzer“, „Mitwirkender“ oder „Leser“ im Abonnement gesendet. Tatsächlich sind _alle_ Benutzer mit Zugriff auf das Abonnement der Application Insights-Ressource im Umfang enthalten und erhalten Benachrichtigungen. 

> [!NOTE]
> Wenn Sie aktuell die Option **Massenversand/Gruppe** verwenden und diese deaktivieren, können Sie die Änderung nicht rückgängig machen.

Verwenden Sie die neue Benutzeroberfläche für Warnungen/Warnungen nahezu in Echtzeit, wenn Sie Benutzer basierend auf ihren Rollen benachrichtigen müssen. Mit [Aktionsgruppen](../platform/action-groups.md) können Sie E-Mail-Benachrichtigungen für Benutzer mit den Rollen „Mitwirkender“, „Besitzer“, „Leser“ konfigurieren (nicht kombiniert als eine einzige Option).



## <a name="next"></a>Nächste Schritte
[Durchsuchen der Diagnoseprotokolle][diagnostic]

[Behandeln von Problemen][qna]

[IP-Adressen von Webtest-Agents](../../azure-monitor/app/ip-addresses.md)

<!--Link references-->

[azure-availability]: ../../insights-create-web-tests.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[start]: ../../azure-monitor/app/app-insights-overview.md
