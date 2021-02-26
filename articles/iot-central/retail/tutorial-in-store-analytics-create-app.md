---
title: 'Tutorial: Erstellen einer Anwendung für die In-Store-Analyse in Azure IoT Central'
description: In diesem Tutorial wird gezeigt, wie Sie eine Anwendung für die In-Store-Analyse für den Einzelhandel in IoT Central erstellen. Sie werden die Anwendung erstellen, anpassen und Sensorgeräte hinzufügen.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: timlt
author: timlt
ms.date: 11/12/2019
ms.openlocfilehash: 112039b9227d5ff34e68b6dc6643597f335fbdb5
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832231"
---
# <a name="tutorial-create-an-in-store-analytics-application-in-azure-iot-central"></a>Tutorial: Erstellen einer Anwendung für die In-Store-Analyse in Azure IoT Central

In diesem Tutorial wird für Lösungsersteller erläutert, wie sie in Azure IoT Central eine Anwendung für die In-Store-Analyse entwickeln können. Die Beispielanwendung ist für einen Laden im Einzelhandel gedacht. Es handelt sich um eine Lösung für die allgemeine Geschäftsanforderung, die Auslastung und Umgebungsbedingungen zu überwachen und entsprechende Anpassungen vornehmen zu können.

Die Beispielanwendung, die Sie gleich erstellen werden, umfasst drei echte Geräte: ein Rigado Cascade 500-Gateway und zwei RuuviTag-Sensoren. Das Tutorial zeigt außerdem, wie Sie zu Testzwecken den in der Anwendungsvorlage enthaltenen simulierten Auslastungssensor verwenden können. Das Rigado C500-Gateway dient in Ihrer Anwendung als Kommunikationshub. Es kommuniziert mit Sensoren in Ihrem Laden und verwaltet deren Verbindungen mit der Cloud. Der RuuviTag ist ein Umgebungssensor, der Telemetriedaten zu beispielsweise Temperatur, Luftfeuchtigkeit und Luftdruck bereitstellt. Der simulierte Auslastungssensor bietet eine Möglichkeit, Bewegungen und Kundenaufkommen in den Kassenbereichen eines Ladens zu verfolgen. 

Dieses Tutorial enthält Anweisungen zum Verbinden der Rigado- und RuuviTag-Geräte mit Ihrer Anwendung. Wenn Sie ein anderes Gateway und andere Sensoren verwenden, können Sie Ihre Anwendung trotzdem mit den folgenden Schritten erstellen. In diesem Tutorial wird außerdem gezeigt, wie Sie simulierte RuuviTag-Sensoren erstellen. Mit den simulierten Sensoren können Sie die Anwendung erstellen, auch wenn Sie keine echten Geräte verwenden. 

Die Überwachungslösung für den Kassenbereich und die Umgebungsbedingungen erstellen Sie in drei Teilen:

* Erstellen der Anwendung und Verbinden von Geräten zum Überwachen der Umgebungsbedingungen
* Anpassen des Dashboards, damit Bediener Geräte überwachen und verwalten können
* Konfigurieren des Datenexports, damit Filialleiter Analysen ausführen und Einblicke visualisieren können

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
> * Verwenden der Azure IoT Central-Vorlage **In-store analytics - checkout** zum Erstellen einer Filialenanwendung
> * Anpassen der Anwendungseinstellungen
> * Erstellen und Anpassen von IoT-Gerätevorlagen
> * Verbinden von Geräten mit Ihrer Anwendung
> * Hinzufügen von Regeln und Aktionen zum Überwachen von Umgebungsbedingungen

## <a name="prerequisites"></a>Voraussetzungen

Zum Abschließen dieses Tutorials benötigen Sie Folgendes:
* Es wird ein Azure-Abonnement empfohlen. Optional können Sie eine kostenlose 7-Tage-Testversion verwenden. Wenn Sie kein Azure-Abonnement besitzen, können Sie auf der Seite [Azure-Anmeldeseite](https://aka.ms/createazuresubscription) eines erstellen.
* Zugriff auf ein Gatewaygerät und zwei Umgebungssensoren (optional können Sie simulierte Geräte verwenden, wie im Tutorial beschrieben)
* Gerätevorlagen für die von Ihnen verwendeten Geräte (für alle in diesem Tutorial verwendeten Geräte werden Vorlagen bereitgestellt)

## <a name="create-an-application"></a>Erstellen einer Anwendung
In diesem Abschnitt erstellen Sie eine neue Azure IoT Central-Anwendung aus einer Vorlage. Sie verwenden diese Anwendung in der gesamten Tutorialreihe, um eine umfassende Lösung zu erstellen.

So erstellen Sie eine neue Azure IoT Central-Anwendung:

1. Navigieren Sie zur Website des [Azure IoT Central-Anwendungs-Managers](https://aka.ms/iotcentral).

1. Wenn Sie ein Azure-Abonnement haben, melden Sie sich mit den üblichen Anmeldeinformationen an. Verwenden Sie andernfalls ein Microsoft-Konto:

    ![Eingeben Ihres Organisationskontos](./media/tutorial-in-store-analytics-create-app/sign-in.png)

1. Wählen Sie **Neue Anwendung** aus, um mit der Erstellung einer neuen Azure IoT Central-Anwendung zu beginnen.

1. Wählen Sie **Einzelhandel** aus.  Auf der Seite „Einzelhandel“ werden verschiedene Vorlagen für Einzelhandelsanwendungen angezeigt.

Erstellen Sie wie folgt eine neue Anwendung vom Typ „In-Store-Analyse – Auschecken“:  

1. Wählen Sie die Anwendungsvorlage **In-store analytics - checkout** aus. Diese Vorlage enthält Gerätevorlagen für alle im Tutorial verwendeten Geräte mit Ausnahme der RuuviTag-Sensoren. Die Vorlage bietet auch ein Dashboard für Bediener zur Überwachung von Kassen- und Umgebungsbedingungen sowie des Gerätestatus. 

1. Wählen Sie optional einen benutzerfreundlichen **Anzeigenamen** aus. Diese Anwendung basiert auf einem fiktiven Einzelhandelsgeschäft mit dem Namen „Contoso“. In diesem Tutorial wird als **Anwendungsname** *Contoso checkout* (Contoso-Kasse) verwendet. Die Anwendungsvorlage basiert auf dem fiktiven Unternehmen Northwind. In diesem Tutorial lernen Sie anhand von Contoso, wie Sie die Anwendung anpassen.

    > [!NOTE]
    > Wenn Sie einen benutzerfreundlichen **Anwendungsnamen** verwenden, müssen Sie dennoch einen eindeutigen Wert für die Anwendungs-**URL** angeben.

1. Wenn Sie ein Azure-Abonnement haben, geben Sie die entsprechenden Angaben für *Verzeichnis, Azure-Abonnement und Region* ein. Wenn Sie kein Abonnement haben, können Sie eine **7-tägige kostenlose Testversion** aktivieren und die erforderlichen Kontaktinformationen eingeben.  

    Weitere Informationen zu Verzeichnissen und Abonnements finden Sie unter [Erstellen einer Azure IoT Central-Anwendung](../core/quick-deploy-iot-central.md).

1. Klicken Sie auf **Erstellen**.

    ![Azure IoT Central-Seite „Anwendung erstellen“](./media/tutorial-in-store-analytics-create-app/preview-application-template.png)

    ![„Anwendung erstellen“ in Azure IoT Central: Abrechnungsinformationen](./media/tutorial-in-store-analytics-create-app/preview-application-template-billinginfo.png)

## <a name="customize-application-settings"></a>Anpassen der Anwendungseinstellungen

Als Lösungsersteller können Sie verschiedene Einstellungen ändern, um die Benutzeroberfläche in Ihrer Anwendung anzupassen. In diesem Abschnitt wählen Sie ein vordefiniertes Anwendungsdesign aus. Optional erfahren Sie, wie Sie ein benutzerdefiniertes Design erstellen und das Bild der Anwendung aktualisieren. Mit einem benutzerdefinierten Design können Sie die Farben des Anwendungsbrowsers, das Browsersymbol und das Anwendungslogo festlegen, das im Mastertitel angezeigt wird.

So wählen Sie ein vordefiniertes Anwendungsdesign aus:

1. Wählen Sie im Mastertitel die Option **Einstellungen** aus.

    ![Azure IoT Central-Anwendungseinstellungen](./media/tutorial-in-store-analytics-create-app/settings-icon.png)

2. Wählen Sie ein neues **Design** aus.

3. Wählen Sie **Speichern** aus.

Anstatt ein vordefiniertes Design zu verwenden, können Sie ein benutzerdefiniertes erstellen. Wenn Sie zum Anpassen der Anwendung und zum Vervollständigen des Tutorials einen Satz Beispielbilder verwenden möchten, laden Sie die [Contoso-Beispielbilder](https://github.com/Azure-Samples/iot-central-docs-samples/tree/master/retail) herunter.

So erstellen Sie eine benutzerdefinierte Design:

1. Erweitern Sie den linken Bereich, wenn dies noch nicht geschehen ist.

    ![Linker Bereich in Azure IoT Central](./media/tutorial-in-store-analytics-create-app/dashboard-expand.png)

1. Wählen Sie **Verwaltung > Anwendung anpassen** aus.

1. Verwenden Sie die Schaltfläche **Ändern**, um ein Bild auszuwählen, das als **Anwendungslogo** hochgeladen werden soll. Geben Sie optional einen Wert für **Alternativtext für Logo** an. 

1. Verwenden Sie die Schaltfläche **Ändern**, um ein Bild für das **Browsersymbol** auszuwählen, das auf den Registerkarten des Browsers angezeigt wird.

1. Optional können Sie die standardmäßigen **Browserfarben** durch Hinzufügen von hexadezimalen HTML-Farbcodes ersetzen. Geben Sie für **Header** den Code *#008575* ein.  Geben Sie für **Akzente** den Code *#A1F3EA* ein. 

1. Wählen Sie **Speichern** aus. 

    ![Angepasstes Azure IoT Central-Logo](./media/tutorial-in-store-analytics-create-app/select-application-logo.png)

    Nach dem Speichern aktualisiert die Anwendung die Browserfarben, das Logo im Mastertitel und das Browsersymbol. 

    ![Aktualisierte Azure IoT Central-Anwendungseinstellungen](./media/tutorial-in-store-analytics-create-app/saved-application-settings.png)

So aktualisieren Sie das Anwendungsbild:

1. Wählen Sie **Verwaltung > Anwendungseinstellungen** aus.

1. Verwenden Sie die Schaltfläche **Bild auswählen**, um ein Bild auszuwählen, das als Anwendungsbild hochgeladen werden soll. Dieses Bild erscheint auf der Anwendungskachel auf der Seite **Meine Apps** des IoT Central-Anwendungs-Managers.

1. Wählen Sie **Speichern** aus.

1. Navigieren Sie optional zur Ansicht **Meine Apps** auf der Website des [Azure IoT Central-Anwendungs-Managers](https://aka.ms/iotcentral). Auf der Anwendungskachel wird das aktualisierte Anwendungsbild angezeigt.

    ![Angepasstes Azure IoT Central-Anwendungsbild](./media/tutorial-in-store-analytics-create-app/customize-application-image.png)

## <a name="create-device-templates"></a>Erstellen von Gerätevorlagen
Als Lösungsersteller können Sie Gerätevorlagen anlegen, mit denen Sie und die Bediener der Anwendung Geräte konfigurieren und verwalten können. Zum Erstellen einer Vorlage können Sie eine benutzerdefinierte Vorlage erstellen, eine vorhandene Vorlagendatei importieren oder eine Vorlage aus dem Azure IoT-Gerätekatalog importieren. Nachdem Sie eine Gerätevorlage erstellt und angepasst haben, können Sie diese verwenden, um echte Geräte mit Ihrer Anwendung zu verbinden. Optional können Sie eine Gerätevorlage verwenden, um zu Testzwecken simulierte Geräte zu generieren.

Die Anwendungsvorlage **In-store analytics - checkout** enthält Gerätevorlagen für verschiedene Geräte.  Es gibt Gerätevorlagen für zwei der drei Geräte, die Sie in der Anwendung verwenden. Die RuuviTag-Gerätevorlage ist nicht in der Anwendungsvorlage **In-store analytics - checkout** enthalten. In diesem Abschnitt fügen Sie Ihrer Anwendung eine Gerätevorlage für RuuviTag-Sensoren hinzu.

So fügen Sie eine RuuviTag-Gerätevorlage zu Ihrer Anwendung hinzu:

1. Wählen Sie im linken Bereich **Gerätevorlagen** aus.

1. Wählen Sie **+ Neu** aus, um eine neue Gerätevorlage zu erstellen.

1. Suchen Sie im Azure IoT-Gerätekatalog die Gerätevorlage für den **RuuviTag**-Sensor. 

1. Klicken Sie auf **Weiter: Anpassen**.

    ![Der Screenshot hebt die Schaltfläche „Weiter: Anpassen“ hervor.](./media/tutorial-in-store-analytics-create-app/ruuvitag-device-template.png)

1. Klicken Sie auf **Erstellen**. Die Anwendung fügt die RuuviTag-Gerätevorlage hinzu.

1. Wählen Sie im linken Bereich **Gerätevorlagen** aus. Auf der Seite werden alle Gerätevorlagen angezeigt, die in der Anwendungsvorlage und der gerade hinzugefügten RuuviTag-Gerätevorlage enthalten sind.

    ![Azure IoT Central-Gerätevorlage für den RuuviTag-Sensor](./media/tutorial-in-store-analytics-create-app/device-templates-list.png)

## <a name="customize-device-templates"></a>Anpassen von Gerätevorlagen
Es gibt drei Möglichkeiten, um Gerätevorlagen in Ihrer Anwendung anzupassen. Die erste Möglichkeit besteht darin, die nativen integrierten Schnittstellen in Ihren Geräten anzupassen, indem Sie die Gerätefunktionen ändern. So können Sie beispielsweise bei einem Temperatursensor Details wie den Anzeigenamen der Temperaturschnittstelle, den Datentyp, die Maßeinheiten sowie den minimalen und maximalen Betriebsbereich ändern. 

Eine zweite Möglichkeit ist die Anpassung Ihrer Gerätevorlagen durch Hinzufügen von Cloudeigenschaften. Cloudeigenschaften sind kein Teil der integrierten Gerätefunktionen. Cloudeigenschaften sind benutzerdefinierte Daten, die Ihre Azure IoT Central-Anwendung erstellt, speichert und mit Ihren Geräten verknüpft. Ein Beispiel für eine Cloudeigenschaft sind etwa berechnete Werte oder auch Metadaten wie ein Standort, den Sie mit einer Reihe von Geräten verknüpfen möchten. 

Die dritte Möglichkeit für das Anpassen von Gerätevorlagen ist das Erstellen benutzerdefinierter Ansichten. Mit Ansichten können Bediener Telemetrie- und Metadaten für Ihre Geräte visualisieren, wie z. B. die Gerätemetriken und die -integrität.

Hier verwenden Sie die ersten beiden Methoden, um die Gerätevorlage für Ihre RuuviTag-Sensoren anzupassen. Weitere Informationen zum Erstellen von Ansichten für Ihre Sensoren finden Sie im Schnellstart [Hinzufügen eines simulierten Geräts zu Ihrer IoT Central-Anwendung](../core/quick-create-simulated-device.md).

So passen Sie die integrierten Schnittstellen der RuuviTag-Gerätevorlage an:

1. Wählen Sie im linken Bereich **Gerätevorlagen** aus. 

1. Wählen Sie die Vorlage für RuuviTag-Sensoren aus. 

1. Blenden Sie den linken Bereich aus. In der Zusammenfassung der Vorlage werden die Gerätefunktionen angezeigt.

    ![Zusammenfassung der Azure IoT Central-Gerätevorlage für den RuuviTag-Sensor](./media/tutorial-in-store-analytics-create-app/ruuvitag-device-summary-view.png)

1. Wählen Sie im Menü der RuuviTag-Gerätevorlage die Option **Anpassen** aus. 

1. Suchen Sie in der Funktionenliste den Telemetrietyp `humidity`. Das ist das Zeilenelement mit dem bearbeitbaren Wert **Luftfeuchtigkeit** für den *Anzeigename*.

In den folgenden Schritten passen Sie den Telemetrietyp `humidity` für die RuuviTag-Sensoren an. Optional können Sie einige der anderen Telemetrietypen anpassen.

Nehmen Sie für den Telemetrietyp `humidity` die folgenden Änderungen vor:

1. Wählen Sie das Steuerelement **Erweitern** aus, um die Schemadetails für die Zeile zu erweitern.

1. Ändern Sie den **Anzeigename** von *Luftfeuchtigkeit* in einen benutzerdefinierten Wert wie *Relative Luftfeuchtigkeit*.

1. Ändern Sie die Option **Semantischer Typ** von *Keiner* in *Luftfeuchtigkeit*.  Optional können Sie in der erweiterten Schemaansicht Schemawerte für den Telemetrietyp „Luftfeuchtigkeit“ festlegen. Mit den Schemaeinstellungen können Sie detaillierte Validierungsanforderungen für die Daten erstellen, die Ihre Sensoren nachverfolgen. Beispielsweise können Sie für eine bestimmte Schnittstelle minimale und maximale Betriebsbereichswerte festlegen.

1. Wählen Sie **Speichern**, um Ihre Änderungen zu speichern.

    ![Der Screenshot zeigt den Bildschirm „Anpassen“ und hebt die Schaltfläche „Speichern“ hervor.](./media/tutorial-in-store-analytics-create-app/ruuvitag-device-template-customize.png)

So fügen Sie eine Cloudeigenschaft zu einer Gerätevorlage in der Anwendung hinzu:

1. Wählen Sie im Menü der RuuviTag-Gerätevorlage die Option **Cloudeigenschaften** aus.

1. Wählen Sie **Cloudeigenschaft hinzufügen** aus. 

Geben Sie die folgenden Werte an, um eine benutzerdefinierte Eigenschaft zum Speichern des Standorts der einzelnen Geräte zu erstellen:

1. Geben Sie für **Anzeigename** den Wert *Standort* ein. Dieser Wert wird automatisch in das Feld **Name** kopiert. Dies ist eine benutzerfreundliche Bezeichnung für die Eigenschaft. Sie können den kopierten Wert verwenden oder ihn ändern.

1. Wählen Sie in der Dropdownliste **Schema** die Option *Zeichenfolge* aus. Mit einem Zeichenfolgentyp können Sie basierend auf der Vorlage eine Zeichenfolge für den Standortnamen einem beliebigen Gerät zuordnen. Beispielsweise können Sie jedem Gerät einen Bereich in einem Laden zuordnen. Optional können Sie den **semantischen Typ** Ihrer Eigenschaft auf *Standort* festlegen. Dadurch wird für das **Schema** automatisch *Geopunkt* verwendet. Damit ist es möglich, einem Gerät GPS-Koordinaten zuzuordnen. 

1. Legen Sie für **Mindestlänge** *2* fest. 

1. Stellen Sie die Option **Leerzeichen kürzen** auf **Ein**.

1. Wählen Sie **Speichern** aus, um die benutzerdefinierte Cloudeigenschaft zu speichern.

    ![Anpassung der Azure IoT Central-Gerätevorlage für den RuuviTag-Sensor](./media/tutorial-in-store-analytics-create-app/ruuvitag-device-template-cloud-property.png)

1. Wählen Sie **Veröffentlichen**. 

    Wenn Sie eine Gerätevorlage veröffentlichen, wird sie für die Bediener der Anwendung sichtbar. Nachdem Sie eine Vorlage veröffentlicht haben, erstellen Sie damit zu Testzwecken simulierte Geräte oder verbinden Sie echte Geräte mit Ihrer Anwendung. Wenn bereits Geräte mit Ihrer Anwendung verbunden sind, werden beim Veröffentlichen einer benutzerdefinierten Vorlage die Änderungen auf die Geräte übertragen.

## <a name="add-devices"></a>Hinzufügen von Geräten
Nachdem Sie Gerätevorlagen erstellt und angepasst haben, können Sie nun Geräte hinzufügen. 

Für dieses Tutorial verwenden Sie zum Erstellen der Anwendung den folgenden Satz an echten und simulierten Geräten:
- Ein echtes Rigado C500-Gateway
- Zwei echte RuuviTag-Sensoren
- Einen simulierten **Auslastungssensor**. Der simulierte Sensor ist in der Anwendungsvorlage enthalten, sodass Sie ihn nicht mehr erstellen müssen. 

> [!NOTE]
> Wenn Sie keine echten Geräte haben, können Sie dieses Tutorial trotzdem absolvieren. Erstellen Sie dafür einfach simulierte RuuviTag-Sensoren. Die folgenden Anweisungen enthalten Schritte zum Erstellen eines simulierten RuuviTag-Sensors. Sie müssen kein simuliertes Gateway erstellen.

Führen Sie die Schritte in den folgenden beiden Artikeln aus, um eine Verbindung mit einem echten Rigado-Gateway und RuuviTag-Sensoren herzustellen. Kehren Sie zu diesem Tutorial zurück, wenn Sie fertig sind. Da Sie in diesem Tutorial bereits Gerätevorlagen erstellt haben, müssen Sie diese in der folgenden Anleitung nicht erneut erstellen.

- Informationen zum Verbinden eines Rigado-Gateways finden Sie in [Verbinden eines Rigado Cascade 500 mit Ihrer Azure IoT Central-Anwendung](../core/howto-connect-rigado-cascade-500.md?toc=/azure/iot-central/retail/toc.json&bc=/azure/iot-central/retail/breadcrumb/toc.json).
- Informationen zum Verbinden eines RuuviTag-Sensors finden Sie in [Verbinden eines RuuviTag-Sensors mit Ihrer Azure IoT Central-Anwendung](../core/howto-connect-ruuvi.md?toc=/azure/iot-central/retail/toc.json&bc=/azure/iot-central/retail/breadcrumb/toc.json). Mithilfe dieser Anweisungen können Sie bei Bedarf auch zwei simulierte Sensoren erstellen.

## <a name="add-rules-and-actions"></a>Hinzufügen von Regeln und Aktionen
Als Teil der Verwendung von Sensoren in Ihrer Azure IoT Central-Anwendung zur Überwachung von Umgebungsbedingungen können Sie Regeln erstellen, mit denen Aktionen ausgeführt werden, wenn bestimmte Bedingungen erfüllt sind. Eine Regel ist einer Gerätevorlage und einem oder mehreren Geräten zugeordnet und enthält Bedingungen, die aufgrund von Gerätetelemetriedaten oder Ereignissen erfüllt sein müssen. Einer Regel sind zudem eine oder mehrere Aktionen zugeordnet. Die Aktionen können das Senden von E-Mail-Benachrichtigungen oder das Auslösen einer Webhook-Aktion zum Senden von Daten an andere Dienste umfassen. Die Anwendungsvorlage **In-store analytics - checkout** enthält einige vordefinierte Regeln für die in der Anwendung verwendeten Geräte.

In diesem Abschnitt erstellen Sie eine neue Regel, die die maximale relative Luftfeuchtigkeit basierend auf den Telemetriedaten des RuuviTag-Sensors überprüft. Sie fügen der Regel eine Aktion hinzu, damit die Anwendung eine E-Mail sendet, wenn die Luftfeuchtigkeit den maximalen Wert überschreitet. 

So erstellen Sie eine Regel: 

1. Erweitern Sie den linken Bereich.

1. Wählen Sie **Regeln** aus.

1. Wählen Sie **+ Neu** aus.

1. Geben Sie *Luftfeuchtigkeitsgrad* als Namen der Regel ein. 

1. Wählen Sie in **Bereiche** die RuuviTag-Gerätevorlage aus. Die von Ihnen definierte Regel wird basierend auf dieser Vorlage für alle Sensoren angewendet. Optional können Sie einen Filter erstellen, der die Regel nur auf eine definierte Teilmenge der Sensoren anwendet. 

1. Wählen Sie `Relative humidity` als **Telemetrie** aus. Dies ist die Gerätefunktion, die Sie in einem vorherigen Schritt angepasst haben.

1. Wählen Sie `Is greater than` als den **Bediener** aus. 

1. Geben Sie als **Wert** einen gängigen oberen Bereich für die Luftfeuchtigkeit im Innenbereich für Ihre Umgebung ein. Geben Sie z. B. *65* ein. Sie haben eine Bedingung für Ihre Regel festgelegt, die eintritt, wenn die relative Luftfeuchtigkeit an einem echten oder simulierten RuuviTag-Sensor diesen Wert überschreitet. Möglicherweise müssen Sie den Wert abhängig vom normalen Luftfeuchtigkeitsbereich in Ihrer Umgebung nach oben oder unten anpassen.  

   ![Azure IoT Central: Hinzufügen von Regelbedingungen](./media/tutorial-in-store-analytics-create-app/rules-add-conditions.png)

So fügen Sie der Regel eine Aktion hinzu:

1. Wählen Sie **+ E-Mail** aus. 

1. Geben Sie *Benachrichtigung bei hoher Luftfeuchtigkeit* als benutzerfreundlichen **Anzeigename** für die Aktion ein. 

1. Geben Sie unter **An** die Ihrem Konto zugeordnete E-Mail-Adresse ein. Wenn Sie eine andere E-Mail-Adresse verwenden, muss die von Ihnen verwendete Adresse einem Benutzer zugeordnet sein, der der Anwendung hinzugefügt wurde. Der Benutzer muss sich auch mindestens einmal an- und abmelden.

1. Geben Sie optional eine Notiz ein, die in den Text der E-Mail aufgenommen werden soll.

1. Wählen Sie **Fertig** aus, um die Aktion fertig zu stellen.

   ![Azure IoT Central: Hinzufügen von Aktionen zu Regeln](./media/tutorial-in-store-analytics-create-app/rules-add-action.png)

1. Wählen Sie **Speichern** aus, um die neue Regel zu speichern und zu aktivieren. 

    Innerhalb weniger Minuten sollten die ersten E-Mails beim angegebenen E-Mail-Konto eingehen. Die Anwendung sendet jedes Mal eine E-Mail, wenn ein Sensor anzeigt, dass die Luftfeuchtigkeit den in Ihrer Bedingung angegebenen Wert überschritten hat.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

* Verwenden der Azure IoT Central-Vorlage **In-store analytics - checkout** zum Erstellen einer Filialenanwendung
* Anpassen der Anwendungseinstellungen
* Erstellen und Anpassen von IoT-Gerätevorlagen
* Verbinden von Geräten mit Ihrer Anwendung
* Hinzufügen von Regeln und Aktionen zum Überwachen von Umgebungsbedingungen

Nachdem Sie nun eine Azure IoT Central-Anwendung zur Überwachung von Umgebungsbedingungen erstellt haben, wird als Nächstes dieser Schritt empfohlen:

> [!div class="nextstepaction"]
> [Anpassen des Dashboards für Bediener](./tutorial-in-store-analytics-customize-dashboard.md)
