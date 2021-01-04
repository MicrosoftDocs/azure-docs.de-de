---
title: 'Tutorial: Erstellen einer App für die vernetzte Abfallwirtschaft mit Azure IoT Central'
description: 'Tutorial: Es wird beschrieben, wie Sie mit Azure IoT Central-Anwendungsvorlagen eine Anwendung für die vernetzte Abfallwirtschaft erstellen.'
author: miriambrus
ms.author: miriamb
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 0a7f5ec0231612a2cee2a9435144c886527c228e
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95014474"
---
# <a name="tutorial-create-a-connected-waste-management-application-in-iot-central"></a>Tutorial: Erstellen einer Anwendung für die vernetzte Abfallwirtschaft in IoT Central



In diesem Tutorial wird Schritt für Schritt beschrieben, wie Sie mit der Anwendungsvorlage **Vernetzte Abfallwirtschaft** eine entsprechende Azure IoT Central-Anwendung erstellen. 

In diesem Tutorial lernen Sie Folgendes: 

> [!div class="checklist"]

> * Verwenden der Azure IoT Central-Vorlage **Vernetzte Abfallwirtschaft**, um eine entsprechende Anwendung zu erstellen
> * Erkunden und Anpassen des Bedienerdashboards 
> * Erkunden der Gerätevorlage für vernetzte Abfallbehälter
> * Erkunden von simulierten Geräten
> * Erkunden und Konfigurieren von Regeln
> * Konfigurieren von Aufträgen
> * Anpassen des Brandings Ihrer Anwendung per Whitelabeling

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:
-  Es wird ein Azure-Abonnement empfohlen. Optional können Sie eine kostenlose 7-Tage-Testversion verwenden. Wenn Sie kein Azure-Abonnement besitzen, können Sie auf der Seite [Azure-Anmeldeseite](https://aka.ms/createazuresubscription) eines erstellen.

## <a name="create-connected-waste-management-app-in-iot-central"></a>Erstellen einer App für vernetzte Abfallwirtschaft in IoT Central

In diesem Abschnitt verwenden Sie die Azure IoT Central-Vorlage **Vernetzte Abfallwirtschaft**, um in IoT Central eine Anwendung dieser Art zu erstellen.

Erstellen Sie wie folgt eine neue Azure IoT Central-Anwendung für die vernetzte Abfallwirtschaft:  

1. Navigieren Sie zur [Startseite von Azure IoT Central](https://aka.ms/iotcentral).

    Wenn Sie ein Azure-Abonnement haben, melden Sie sich mit den üblichen Anmeldeinformationen an. Verwenden Sie andernfalls ein Microsoft-Konto:

    ![Eingeben Ihres Organisationskontos](./media/tutorial-connectedwastemanagement/sign-in.png)

2. Klicken Sie im linken Bereich auf **Build**, und wählen Sie die Registerkarte **Regierung** aus. Auf der Seite „Regierung“ werden mehrere Anwendungsvorlagen für den Behördenbereich angezeigt.

    ![Erstellen von App-Vorlagen für Behörden](./media/tutorial-connectedwastemanagement/iotcentral-government-tab-overview.png)

1. Wählen Sie die Anwendungsvorlage **Vernetzte Abfallwirtschaft** aus. Diese Vorlage enthält ein Beispiel für eine Gerätevorlage für vernetzte Abfallbehälter, ein simuliertes Gerät, ein Bedienerdashboard und vorkonfigurierte Überwachungsregeln.    

2. Klicken Sie auf **App erstellen**. Das Formular **Neue Anwendung** für die Erstellung mit den folgenden Feldern wird geöffnet:
    * **Anwendungsname**: Standardmäßig wird für die Anwendung der Name *Vernetzte Abfallwirtschaft* verwendet, gefolgt von einer eindeutigen ID-Zeichenfolge, die von IoT Central generiert wird. Wählen Sie optional einen benutzerfreundlichen Anzeigenamen aus. Sie können den Anwendungsnamen bei Bedarf später noch ändern.
    * **URL**: Optional können Sie auch Ihre gewünschte URL auswählen. Es ist auch möglich, die URL zu einem späteren Zeitpunkt zu ändern. 
    * Wenn Sie ein Azure-Abonnement haben, geben Sie die entsprechenden Angaben für *Verzeichnis, Azure-Abonnement und Region* ein. Wenn Sie kein Abonnement haben, können Sie eine **7-tägige kostenlose Testversion** aktivieren und die erforderlichen Kontaktinformationen eingeben.  

    Weitere Informationen zu Verzeichnissen und Abonnements finden Sie unter [Erstellen einer Azure IoT Central-Anwendung](../core/quick-deploy-iot-central.md).

5. Klicken Sie am unteren Rand der Seite auf die Schaltfläche **Erstellen**. 

    ![Azure IoT Central-Seite zur Erstellung einer Anwendung für die vernetzte Abfallwirtschaft](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement.png)
    
    ![Azure IoT Central: Erstellen von Informationen zur vernetzten Abrechnung](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement-billinginfo.png)

 
6. Sie haben nun eine App für die vernetzte Abfallwirtschaft erstellt, indem Sie die Azure IoT Central-Vorlage **Vernetzte Abfallwirtschaft** verwendet haben. 

Glückwunsch! Ihre neu erstellte Anwendung ist bereits vorkonfiguriert und verfügt über folgende Elemente:
* Beispieldashboards für Bediener
* Vordefinierte Beispielgerätevorlagen für vernetzte Abfallbehälter
* Simulierte Geräte für vernetzte Abfallbehälter
* Vorkonfigurierten Regeln und Aufträgen
* Beispiel für Branding mit Whitelabeling 

Es ist Ihre Anwendung, und Sie können sie jederzeit ändern. Wir erkunden nun die Anwendung und nehmen einige Anpassungen vor.  

## <a name="explore-and-customize-operator-dashboard"></a>Erkunden und Anpassen des Bedienerdashboards 
Nachdem Sie die Anwendung erstellt haben, gelangen Sie zum **Wide World Waste-Dashboard für vernetzte Abfallwirtschaft**.

   ![Dashboard für vernetzte Abfallwirtschaft](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard1.png)

Als Lösungsentwickler können Sie Ansichten im Dashboard für Bediener erstellen und anpassen. Bevor wir auf die Anpassungen eingehen, erkunden wir zunächst das Dashboard. 

> [!NOTE]
> Alle im Dashboard angezeigten Daten basieren auf simulierten Gerätedaten, die im nächsten Abschnitt beschrieben werden. 

Das Dashboard besteht aus unterschiedlichen Arten von Kacheln:

* ***Wide World Waste Utility-Bildkachel** _: Die erste Kachel im Dashboard ist eine Bildkachel des fiktiven Abfallentsorgungsunternehmens „Wide World Waste“. Sie können die Kachel anpassen und Ihr eigenes Bild einfügen oder das Bild entfernen. 

_ ***Bildkachel für Abfallbehälter** _: Sie können mithilfe von Bild- und Inhaltskacheln eine visuelle Darstellung des zu überwachenden Geräts zusammen mit einem beschreibenden Text erstellen. 

_ ***KPI-Kachel für Füllstand** _: Auf der Kachel wird ein Wert angezeigt, der von einem Sensor vom Typ „Füllstand“ in einem Abfallbehälter gemeldet wird. Der Sensor für den *Füllstand* und andere Abfallbehältersensoren für *Geruchsmessung* oder *Gewicht* können per Remotezugriff überwacht werden. Ein Bediener kann dann Maßnahmen ergreifen, z. B. ein Fahrzeug der Stadtreinigung losschicken. 

*  ***Karte mit dem Abfallüberwachungsbereich** _: Für die Karte wird Azure Maps verwendet, und Sie können die Konfiguration direkt in Azure IoT Central durchführen. Auf der Kachel mit der Karte wird der Gerätestandort angezeigt. Bewegen Sie den Mauszeiger auf die Karte, und probieren Sie die Steuerelemente aus, z. B. Vergrößern, Verkleinern und Erweitern.

     ![Karte im Dashboard für vernetzte Abfallwirtschaft](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-map.png)


_ ***Balkendiagramm für Füllstand, Geruch oder Gewicht**: Sie können ein oder mehrere Element(e) der Gerätetelemetriedaten in einem Balkendiagramm visualisieren. Sie können das Balkendiagramm auch erweitern.  

  ![Balkendiagramm im Dashboard für vernetzte Abfallwirtschaft](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-barchart.png)


* **Inhaltskachel für Field Service**: Das Dashboard enthält einen Link für die Integration in Dynamics 365 for Field Service aus Ihrer Azure IoT Central-Anwendung. Beispielsweise können Sie Field Service nutzen, um Tickets für das Losschicken von Abfallsammlungsteams zu erstellen. 


### <a name="customize-dashboard"></a>Anpassen des Dashboards 

Als Lösungsentwickler können Sie Ansichten im Dashboard für Ihre Bediener anpassen. Sie können Folgendes ausprobieren:
1. Klicken Sie auf **Bearbeiten**, um das **Wide World Waste-Dashboard für vernetzte Abfallwirtschaft** anzupassen. Sie können das Dashboard anpassen, indem Sie auf das Menü **Bearbeiten** klicken. Wenn sich das Dashboard im Modus **Bearbeiten** befindet, können Sie neue Kacheln hinzufügen oder Konfigurationen vornehmen. 

    ![Bearbeiten des Dashboards](./media/tutorial-connectedwastemanagement/edit-dashboard.png)

2. Sie können auch auf **+ Neu** klicken, um ein neues Dashboard zu erstellen und es neu zu konfigurieren. Sie können mehrere Dashboards nutzen und über das entsprechende Menü zwischen Ihren Dashboards navigieren. 

## <a name="explore-connected-waste-bin-device-template"></a>Erkunden der Gerätevorlage für vernetzte Abfallbehälter

Mit einer Gerätevorlage in Azure IoT Central wird die Funktion eines Geräts definiert, z. B. Telemetriedaten, Eigenschaften oder Befehl. Als Lösungsentwickler können Sie Gerätevorlagen definieren, die für die Funktionen der zu verbindenden Geräte stehen. 
 

Die Anwendung **Vernetzte Abfallwirtschaft** verfügt über eine Beispielgerätevorlage für vernetzte Abfallwirtschaft.

Zeigen Sie die Gerätevorlage wie folgt an:

1. Klicken Sie in IoT Central im linken Bereich Ihrer Anwendung auf **Gerätevorlagen**. 

    ![Screenshot: Liste der Gerätevorlagen in der Anwendung](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate.png)

2. In der Liste mit den Gerätevorlagen wird **Connected Waste Bin** (Vernetzter Abfallbehälter) angezeigt. Öffnen Sie die Vorlage, indem Sie auf den Namen klicken.

3. Machen Sie sich mit den Funktionen der Gerätevorlage vertraut. Sie sehen, dass dafür beispielsweise Sensoren vom Typ *Fill level* (Füllstand), *Odor meter* (Geruchsmessung), *Weight* (Gewicht) und *Location* (Standort) definiert sind.

   ![Screenshot: Details der Gerätevorlage für vernetzte Abfallbehälter](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-connectedbin.png)

   

### <a name="customizing-the-device-template"></a>Anpassen der Gerätevorlage

Versuchen Sie, Folgendes anzupassen:
1. Navigieren Sie im Menü für Gerätevorlagen zu **Anpassen**.
2. Suchen Sie nach dem Telemetrietyp `Odor meter`.
3. Aktualisieren Sie die Option **Anzeigename**, indem Sie `Odor meter` in `Odor level` ändern.
4. Sie können auch versuchen, die Maßeinheit zu aktualisieren, oder legen Sie Werte für *Minimalwert* und *Maximalwert* fest.
5. Wählen Sie **Speichern**, um die Änderungen zu speichern. 

### <a name="add-a-cloud-property"></a>Hinzufügen einer Cloudeigenschaft 

1. Navigieren Sie über das Gerätevorlagenmenü zu **Cloudeigenschaft**.
2. Fügen Sie eine neue Cloudeigenschaft hinzu, indem Sie auf **+ Cloudeigenschaft hinzufügen** klicken. In IoT Central können Sie eine Eigenschaft hinzufügen, die für das Gerät relevant ist, aber für die das Senden durch ein Gerät nicht erwartet wird. Bei einer Cloudeigenschaft kann es sich beispielsweise um einen Warnungsschwellenwert handeln, der für einen bestimmten Installationsbereich oder bestimmte Ressourcen- oder Wartungsinformationen oder andere Informationen gilt. 
3. Wählen Sie **Speichern**, um die Änderungen zu speichern. 
 
### <a name="views"></a>Sichten 
* Die Gerätevorlage für vernetzte Abfallbehälter verfügt über vordefinierte Ansichten. Sie können die Ansichten erkunden und Updates vornehmen. In den Ansichten ist definiert, wie Bedienern die Gerätedaten angezeigt werden, aber es können auch Cloudeigenschaften eingegeben werden. 

  ![Ansichten der Gerätevorlage](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-views.png)

### <a name="publish"></a>Veröffentlichen 

* Falls Sie Änderungen vorgenommen haben, sollten Sie für die Gerätevorlage die Option **Veröffentlichen** verwenden. 

### <a name="create-a-new-device-template"></a>Erstellen einer neuen Gerätevorlage 

* Wählen Sie die Option **+ Neu** aus, um eine neue Gerätevorlage zu erstellen, und führen Sie die Schritte des Erstellungsprozesses aus. Sie können eine benutzerdefinierte Gerätevorlage von Grund auf neu erstellen oder im Azure-Gerätekatalog eine Gerätevorlage auswählen. 

## <a name="explore-simulated-devices"></a>Erkunden von simulierten Geräten

In IoT Central können Sie simulierte Geräte erstellen, um Ihre Gerätevorlage und die Anwendung zu testen. 

Die Anwendung **Vernetzte Abfallwirtschaft** verfügt über zwei simulierte Geräte, die der Gerätevorlage für vernetzte Abfallbehälter zugeordnet sind. 

### <a name="to-view-the-devices"></a>Zeigen Sie die Geräte wie folgt an:

1. Navigieren Sie im linken Bereich von IoT Central zu **Gerät**. 

   ![Geräte](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices.png)

2. Wählen Sie das Gerät „Connected Waste Bin“ (Vernetzter Abfallbehälter) aus, und klicken Sie darauf.  

     ![Gerät 1](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices-bin1.png)

3. Navigieren Sie zur Registerkarte **Cloudeigenschaften**, und versuchen Sie, den Wert `Bin full alert threshold` von `95` in `100` zu ändern. 
* Erkunden Sie die Registerkarten **Geräteeigenschaften** und **Gerätedashboard**. 

> [!NOTE]
> Beachten Sie, dass alle Registerkarten über die **Ansichten der Gerätevorlage** konfiguriert wurden.

### <a name="add-new-devices"></a>Hinzufügen neuer Geräte

* Sie können neue Geräte hinzufügen, indem Sie auf der Registerkarte **Geräte** auf **+ Neu** klicken. 

## <a name="explore-and-configure-rules"></a>Erkunden und Konfigurieren von Regeln

In Azure IoT Central können Sie Regeln zum automatischen Überwachen der Gerätetelemetrie erstellen und festlegen, dass Aktionen ausgelöst werden, wenn eine oder mehrere Bedingungen erfüllt sind. Die Aktionen können das Senden von E-Mail-Benachrichtigungen oder aber das Auslösen einer Power Automate- oder Webhookaktion zum Senden von Daten an andere Dienste umfassen.

Die Anwendung **Vernetzte Abfallwirtschaft** verfügt über vier Beispielregeln.

### <a name="to-view-rules"></a>Zeigen Sie die Regeln wie folgt an:
1. Navigieren Sie im linken Bereich von IoT Central zu **Regeln**.

   ![Regeln](./media/tutorial-connectedwastemanagement/connectedwastemanagement-rules.png)

2. Wählen Sie **Bin full alert** (Warnung: Behälter voll) aus.

     ![Bin full alert (Warnung: Behälter voll)](./media/tutorial-connectedwastemanagement/connectedwastemanagement-binfullalert.png)

 3. Mit `Bin full alert` (Warnung: Behälter voll) wird überprüft, ob die **Bedingung** `Fill level is greater than or equal to Bin full alert threshold` (Füllstand größer oder gleich Schwellenwert für „Warnung: Behälter voll“) erfüllt ist.

    `Bin full alert threshold` (Schwellenwert für „Warnung: Behälter voll“) ist eine *Cloudeigenschaft*, die in der Gerätevorlage `Connected waste bin` (Vernetzter Abfallbehälter) definiert ist. 

Als Nächstes erstellen wir eine E-Mail-Aktion.

### <a name="create-an-email-action"></a>Erstellen einer E-Mail-Aktion
Konfigurieren Sie in der Aktionsliste der Regel wie folgt eine E-Mail-Aktion:
1. Wählen Sie **+ E-Mail** aus. 
2. Geben Sie unter **Anzeigename** den Namen *High pH alert* (Warnung: Hoher pH-Wert) für die Aktion ein.
3. Geben Sie unter **An** die E-Mail-Adresse ein, die Ihrem IoT Central-Konto zugeordnet ist. 
4. Geben Sie optional eine Notiz ein, die in den Text der E-Mail aufgenommen werden soll.
5. Wählen Sie **Fertig** aus, um die Aktion fertig zu stellen.
6. Wählen Sie **Speichern** aus, um die neue Regel zu speichern und zu aktivieren. 

Sie sollten eine E-Mail mit dem Hinweis erhalten, dass die konfigurierte **Bedingung** erfüllt ist.

> [!NOTE]
> Von der Anwendung wird jedes Mal eine E-Mail gesendet, wenn eine Bedingung erfüllt ist. **Deaktivieren** Sie die Regel, um den Empfang von E-Mails über die automatisierte Regel zu beenden. 
  
Erstellen Sie wie folgt eine neue Regel: 
1. Wählen Sie im linken Bereich unter **Regeln** die Option **+ Neu** aus.

## <a name="configure-jobs"></a>Konfigurieren von Aufträgen

In IoT Central können Sie über Aufträge für mehrere Geräte Updates für Geräte- oder Cloudeigenschaften auslösen. Sie können Aufträge nicht nur für Eigenschaften verwenden, sondern auch zum Auslösen von Gerätebefehlen auf mehreren Geräten. Der Workflow wird von IoT Central für Sie automatisiert. 

1. Wechseln Sie im linken Bereich zu **Aufträge**. 
2. Klicken Sie auf **+ Neu**, und konfigurieren Sie einen oder mehrere Aufträge. 


## <a name="customize-your-application"></a>Anpassen Ihrer Anwendung 

Als Lösungsersteller können Sie verschiedene Einstellungen ändern, um die Benutzeroberfläche in Ihrer Anwendung anzupassen.

### <a name="to-change-the-application-theme"></a>Ändern Sie das Anwendungsdesign wie folgt:

1. Navigieren Sie zu **Verwaltung > Anwendung anpassen**.
3. Verwenden Sie die Schaltfläche **Ändern**, um ein Bild auszuwählen, das als **Anwendungslogo** hochgeladen werden soll.
4. Verwenden Sie die Schaltfläche **Ändern**, um ein Bild für das **Browsersymbol** auszuwählen, das auf den Registerkarten des Browsers angezeigt wird.
5. Sie können auch die standardmäßigen **Browserfarben** ersetzen, indem Sie hexadezimale HTML-Farbcodes hinzufügen.

   ![Azure IoT Central: Anpassen Ihrer Anwendung](./media/tutorial-connectedwastemanagement/connectedwastemanagement-customize-your-application.png)

6. Sie können Anwendungsbilder ändern, indem Sie unter **Verwaltung > Anwendungseinstellungen** auf die Schaltfläche **Bild auswählen** klicken, um ein Bild für den Upload als Anwendungsbild auszuwählen.
7. Abschließend können Sie noch das **Design** ändern, indem Sie im Mastertitel der Anwendung auf **Einstellungen** klicken.

  
## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie diese Anwendung nicht weiterverwenden möchten, sollten Sie sie mit den folgenden Schritten löschen:

1. Öffnen Sie im linken Bereich Ihrer IoT Central-Anwendung die Registerkarte „Verwaltung“.
2. Wählen Sie „Anwendungseinstellungen“ aus, und klicken Sie unten auf der Seite auf die Schaltfläche „Löschen“.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen
 
> [!div class="nextstepaction"]
> [Konzepte im Zusammenhang mit der vernetzten Abfallwirtschaft ](./concepts-connectedwastemanagement-architecture.md)
