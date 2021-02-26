---
title: 'Tutorial: Erstellen einer App für die vernetzte Abfallwirtschaft mit Azure IoT Central'
description: 'Tutorial: Hier erfahren Sie, wie Sie mit Azure IoT Central-Anwendungsvorlagen eine Anwendung für die vernetzte Abfallwirtschaft erstellen.'
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 65245b3c4b7e18670682f7e9e890453e32337644
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820078"
---
# <a name="tutorial-create-a-connected-waste-management-app"></a>Tutorial: Erstellen einer App für die vernetzte Abfallwirtschaft

In diesem Tutorial erfahren Sie, wie Sie mithilfe von Azure IoT Central eine Anwendung für die vernetzte Abfallwirtschaft erstellen. 

Dabei wird insbesondere Folgendes vermittelt: 

> [!div class="checklist"]
> Verwenden der Azure IoT Central-Vorlage *Vernetzte Abfallwirtschaft* für die App-Erstellung
> Erkunden und Anpassen des Operatordashboards Erkunden der Gerätevorlage für vernetzte Abfallbehälter
> Erkunden von simulierten Geräten
> Erkunden und Konfigurieren von Regeln
> Konfigurieren von Aufträgen
> Anpassen des Brandings Ihrer Anwendung

## <a name="prerequisites"></a>Voraussetzungen

Es wird ein Azure-Abonnement empfohlen. Alternativ können Sie eine kostenlose 7-Tage-Testversion verwenden. Wenn Sie kein Azure-Abonnement besitzen, können Sie auf der Seite [Azure-Anmeldeseite](https://aka.ms/createazuresubscription) eines erstellen.

## <a name="create-your-app-in-azure-iot-central"></a>Erstellen Ihrer App in Azure IoT Central

In diesem Abschnitt wird die Vorlage für vernetzte Abfallwirtschaft verwendet, um Ihre App in Azure IoT Central zu erstellen. Gehen Sie dabei folgendermaßen vor:

1. Navigieren Sie zu [Azure IoT Central](https://aka.ms/iotcentral).

    Wenn Sie über ein Azure-Abonnement verfügen, können Sie sich mit den zugehörigen Anmeldeinformationen anmelden. Melden Sie sich andernfalls mit einem Microsoft-Konto an:

    ![Screenshot: Microsoft-Anmeldung](./media/tutorial-connectedwastemanagement/sign-in.png)

1. Wählen Sie im linken Bereich die Option **Erstellen** aus. Wählen Sie anschließend die Registerkarte **Behörden** aus. Auf der Seite „Regierung“ werden mehrere Anwendungsvorlagen für den Behördenbereich angezeigt.

    ![Screenshot: Erstellungsseite in Azure IoT Central](./media/tutorial-connectedwastemanagement/iotcentral-government-tab-overview.png)

1. Wählen Sie die Anwendungsvorlage **Vernetzte Abfallwirtschaft** aus. Diese Vorlage enthält eine exemplarische Gerätevorlage für vernetzte Abfallbehälter, ein simuliertes Gerät, ein Operatordashboard und vorkonfigurierte Überwachungsregeln.    

1. Wählen Sie **App erstellen** aus. Daraufhin wird das Dialogfeld **Neue Anwendung** geöffnet. Geben Sie Informationen für die folgenden Felder an:
    * **Anwendungsname**: Standardmäßig wird für die Anwendung der Name **Vernetzte Abfallwirtschaft** verwendet, gefolgt von einer eindeutigen ID-Zeichenfolge, die von Azure IoT Central generiert wird. Sie können optional einen benutzerfreundlichen Anzeigenamen wählen. Der Anwendungsname kann auch später noch geändert werden.
    * **URL**. Sie können optional die gewünschte URL auswählen. Die URL kann später noch geändert werden. 
    * **Tarif**: Falls Sie über ein Azure-Abonnement verfügen, geben Sie Ihr Verzeichnis, Ihr Azure-Abonnement und Ihre Region in die entsprechenden Felder des Dialogfelds **Abrechnungsinformationen** ein. Sollten Sie über kein Abonnement verfügen, wählen Sie die Option **Free** aus, um die kostenlose 7-Tage-Testversion zu aktivieren, und geben Sie die erforderlichen Kontaktinformationen an.  

    Weitere Informationen zu Verzeichnissen und Abonnements finden Sie unter [Schnellstart: Erstellen einer Azure IoT Central-Anwendung](../core/quick-deploy-iot-central.md).

1. Wählen Sie am unteren Rand der Seite die Option **Erstellen** aus. 

    ![Screenshot: Dialogfeld „Neue Anwendung erstellen“ in Azure IoT Central](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement.png)
    
    ![Screenshot: Dialogfeld „Abrechnungsinformationen“ in Azure IoT Central](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement-billinginfo.png)

 
Ihre neu erstellte Anwendung ist bereits vorkonfiguriert und verfügt über folgende Elemente:
* Beispieldashboards für Operatoren
* Vordefinierte Beispielgerätevorlagen für vernetzte Abfallbehälter
* Simulierte Geräte für vernetzte Abfallbehälter
* Regeln und Aufträge
* Beispielbranding 

Sie können Ihre Anwendung jederzeit ändern. Wir erkunden nun die Anwendung und nehmen einige Anpassungen vor.  

## <a name="explore-and-customize-the-operator-dashboard"></a>Erkunden und Anpassen des Bedienerdashboards 

Sehen Sie sich das **Wide World-Dashboard für die Abfallwirtschaft** an, das nach dem Erstellen Ihrer App angezeigt wird.

   ![Screenshot: Wide World-Dashboard für die Abfallwirtschaft](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard1.png)

Als Lösungsentwickler können Sie Ansichten im Dashboard für Bediener erstellen und anpassen. Sehen wir uns zunächst das Dashboard etwas genauer an. 

>[!NOTE]
>Alle im Dashboard angezeigten Daten basieren auf simulierten Gerätedaten. Weitere Informationen hierzu folgen im nächsten Abschnitt. 

Das Dashboard umfasst verschiedene Kacheln:

* **Bildkachel für das Entsorgungsunternehmen „Wide World Waste“:** Die erste Kachel auf dem Dashboard ist eine Bildkachel des fiktiven Abfallentsorgungsunternehmens „Wide World Waste“. Sie können die Kachel anpassen und Ihr eigenes Bild einfügen oder das Bild entfernen. 

* **Bildkachel für Abfallbehälter:** Mithilfe von Bild- und Inhaltskacheln können Sie eine visuelle Darstellung des zu überwachenden Geräts sowie eine Beschreibung erstellen. 

* **KPI-Kachel für Füllstand:** Auf dieser Kachel wird ein Wert angezeigt, der von einem *Füllstandssensor* in einem Abfallbehälter gemeldet wird. Der Füllstandssensor sowie andere Abfallbehältersensoren für *Geruchsmessung* oder *Gewicht* können remote überwacht werden. Ein Operator kann dann geeignete Maßnahmen ergreifen und beispielsweise ein Fahrzeug der Stadtreinigung losschicken. 

* **Umgebungskarte für die Abfallüberwachung:** Diese Kachel basiert auf Azure Maps und kann direkt in Azure IoT Central konfiguriert werden. Auf der Kartenkachel wird der Gerätestandort angezeigt. Bewegen Sie den Mauszeiger auf die Karte, und probieren Sie die Steuerelemente wie „Vergrößern“, „Verkleinern“ und „Erweitern“ aus.

     ![Screenshot: Vorlage für vernetzte Abfallwirtschaft: Karte auf dem Dashboard](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-map.png)


* **Balkendiagramm für Füllstand, Geruch und Gewicht:** Sie können einzelne oder mehrere Arten von Gerätetelemetriedaten in einem Balkendiagramm visualisieren. Sie können das Balkendiagramm auch erweitern.  

  ![Screenshot: Vorlage für vernetzte Abfallwirtschaft: Balkendiagramm auf dem Dashboard](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-barchart.png)


* **Field Services:** Das Dashboard enthält einen Link für die Integration in Dynamics 365 for Field Service aus Ihrer Azure IoT Central-Anwendung. Beispielsweise können Sie Field Services nutzen, um Tickets für die Abfallsammlung zu erstellen. 


### <a name="customize-the-dashboard"></a>Anpassen des Dashboards 

Sie können das Dashboard anpassen, indem Sie das Menü **Bearbeiten** auswählen. Anschließend können Sie neue Kacheln hinzufügen oder bereits vorhandene Kacheln konfigurieren. So sieht das Dashboard im Bearbeitungsmodus aus: 

![Screenshot: Vorlage für vernetzte Abfallwirtschaft: Dashboard im Bearbeitungsmodus](./media/tutorial-connectedwastemanagement/edit-dashboard.png)

Sie können auch **+ Neu** auswählen, um ein neues Dashboard zu erstellen und es neu zu konfigurieren. Sie können mehrere Dashboards verwenden und über das Dashboardmenü zwischen Ihren Dashboards wechseln. 

## <a name="explore-the-device-template"></a>Erkunden der Gerätevorlage

Eine Gerätevorlage in Azure IoT Central definiert die Funktionen eines Geräts und kann Telemetriedaten, Eigenschaften oder Befehle umfassen. Als Lösungsentwickler können Sie Gerätevorlagen definieren, die die Funktionen der zu vernetzenden Geräte darstellen. 

Die Anwendung für vernetzte Abfallwirtschaft beinhaltet eine Beispielgerätevorlage für vernetzte Abfallbehälter.

Zeigen Sie die Gerätevorlage wie folgt an:

1. Wählen Sie in Azure IoT Central im linken Bereich Ihrer App die Option **Gerätevorlagen** aus. 

    ![Screenshot: Liste mit den Gerätevorlagen in der Anwendung](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate.png)

1. Wählen Sie in der Liste **Gerätevorlagen** die Option **Connected Waste Bin** (Vernetzter Abfallbehälter) aus.

1. Sehen Sie sich die Funktionen der Gerätevorlage an. Wie Sie sehen, werden Sensoren wie **Fill level** (Füllstand), **Odor meter** (Geruchsmessung), **Weight** (Gewicht) und **Location** (Standort) definiert.

   ![Screenshot: Details der Gerätevorlage für vernetzte Abfallbehälter](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-connectedbin.png)


### <a name="customize-the-device-template"></a>Anpassen der Gerätevorlage

Versuchen Sie, Folgendes anzupassen:
1. Wählen Sie im Gerätevorlagenmenü die Option **Anpassen** aus.
1. Navigieren Sie zum Telemetrietyp **Odor meter** (Geruchsmessung).
1. Ändern Sie unter **Anzeigename** den Namen **Odor meter** (Geruchsmessung) in **Odor level** (Geruchsstärke).
1. Versuchen Sie, die Maßeinheit zu ändern, oder legen Sie **Minimalwert** und **Maximalwert** fest.
1. Wählen Sie **Speichern** aus. 

### <a name="add-a-cloud-property"></a>Hinzufügen einer Cloudeigenschaft 

Gehen Sie dabei folgendermaßen vor:
1. Wählen Sie im Gerätevorlagenmenü die Option **Cloudeigenschaft** aus.
1. Wählen Sie **+ Cloudeigenschaft hinzufügen** aus. In Azure IoT Central können Sie eine Eigenschaft hinzufügen, die für das Gerät relevant ist, aber nicht von einem Gerät gesendet werden soll. Bei einer Cloudeigenschaft kann es sich beispielsweise um einen Warnungsschwellenwert handeln, der für einen bestimmten Installationsbereich oder für bestimmte Ressourcen- oder Wartungsinformationen gilt. 
1. Wählen Sie **Speichern** aus. 
 
### <a name="views"></a>Sichten 
Die Gerätevorlage für vernetzte Abfallbehälter verfügt über vordefinierte Ansichten. Erkunden Sie die Ansichten, und aktualisieren Sie sie, falls gewünscht. Mit den Ansichten wird definiert, wie die Gerätedaten für Operatoren angezeigt werden und wie sie Cloudeigenschaften festlegen können. 

  ![Screenshot: Vorlage für vernetzte Abfallwirtschaft: Gerätevorlagenansichten](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-views.png)

### <a name="publish"></a>Veröffentlichen 

Falls Sie Änderungen vorgenommen haben, denken Sie daran, die Gerätevorlage zu veröffentlichen. 

### <a name="create-a-new-device-template"></a>Erstellen einer neuen Gerätevorlage 

Wählen Sie zum Erstellen einer neuen Gerätevorlage die Option **+ Neu** aus, und führen Sie die entsprechenden Schritte aus. Sie können eine benutzerdefinierte Gerätevorlage von Grund auf neu erstellen oder eine Gerätevorlage aus dem Azure-Gerätekatalog auswählen. 

## <a name="explore-simulated-devices"></a>Erkunden von simulierten Geräten

In Azure IoT Central können Sie simulierte Geräte erstellen, um Ihre Gerätevorlage und Ihre Anwendung zu testen. 

Die Anwendung für vernetzte Abfallwirtschaft verfügt über zwei simulierte Geräte, die der Gerätevorlage für vernetzte Abfallbehälter zugeordnet sind. 

### <a name="view-the-devices"></a>Anzeigen der Geräte

1. Wählen Sie im linken Bereich von Azure IoT Central die Option **Geräte** aus. 

   ![Screenshot: Vorlage für vernetzte Abfallwirtschaft: Geräte](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices.png)

1. Wählen Sie das Gerät **Connected Waste Bin** (Vernetzter Abfallbehälter) aus.  

     ![Screenshot: Vorlage für vernetzte Abfallwirtschaft: Geräteeigenschaften](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices-bin1.png)

1. Navigieren Sie zur Registerkarte **Cloudeigenschaften**. Ändern Sie den Schwellenwert für die Warnung bei vollem Behälter (**Bin full alert threshold**) von **95** in **100**. 

Erkunden Sie die Registerkarten **Geräteeigenschaften** und **Gerätedashboard**. 

> [!NOTE]
> Alle Registerkarten wurden über die Ansichten der Gerätevorlage konfiguriert.

### <a name="add-new-devices"></a>Hinzufügen neuer Geräte

Sie können neue Geräte hinzufügen, indem Sie auf der Registerkarte **Geräte** die Option **+ Neu** auswählen. 

## <a name="explore-and-configure-rules"></a>Erkunden und Konfigurieren von Regeln

In Azure IoT Central können Sie Regeln zur automatischen Überwachung von Gerätetelemetriedaten erstellen und festlegen, dass Aktionen ausgelöst werden sollen, wenn einzelne oder mehrere Bedingungen erfüllt sind. Mögliche Aktionen sind beispielsweise das Senden von E-Mail-Benachrichtigungen, das Auslösen einer Aktion in Power Automate oder das Starten einer Webhookaktion zum Senden von Daten an andere Dienste.

Die Anwendung Vernetzte Abfallwirtschaft verfügt über vier Beispielregeln.

### <a name="view-rules"></a>Anzeigen von Regeln
1. Wählen Sie im linken Bereich von Azure IoT Central die Option **Regeln** aus.

   ![Screenshot: Vorlage für vernetzte Abfallwirtschaft: Regeln](./media/tutorial-connectedwastemanagement/connectedwastemanagement-rules.png)

1. Wählen Sie **Bin full alert** (Warnung: Behälter voll) aus.

     ![Screenshot: Warnung für vollen Behälter](./media/tutorial-connectedwastemanagement/connectedwastemanagement-binfullalert.png)

 1. Durch **Bin full alert** (Warnung: Behälter voll) wird folgende Bedingung überprüft: **Füllstand ist größer oder gleich dem Schwellenwert für die Warnung bei vollem Behälter.**

    Der Schwellenwert für die Warnung bei vollem Behälter (**Bin full alert threshold**) ist eine in der Gerätevorlage für vernetzte Abfallbehälter definierte Cloudeigenschaft. 

Als Nächstes erstellen wir eine E-Mail-Aktion.

### <a name="create-an-email-action"></a>Erstellen einer E-Mail-Aktion

In der Liste **Aktionen** der Regel können Sie eine E-Mail-Aktion konfigurieren:
1. Wählen Sie **+ E-Mail** aus. 
1. Geben Sie unter **Anzeigename** den Namen **High pH alert** (Warnung: Hoher pH-Wert) ein.
1. Geben Sie unter **An** die E-Mail-Adresse ein, die Ihrem Azure IoT Central-Konto zugeordnet ist. 
1. Geben Sie optional eine Notiz ein, die in den Text der E-Mail aufgenommen werden soll.
1. Wählen Sie **Fertig** > **Speichern** aus. 

Nun erhalten Sie eine E-Mail, wenn die konfigurierte Bedingung erfüllt ist.

>[!NOTE]
>Von der Anwendung wird jedes Mal eine E-Mail gesendet, wenn eine Bedingung erfüllt ist. Deaktivieren Sie die Regel, um den Empfang von E-Mails über die automatisierte Regel zu beenden. 
  
Wenn Sie eine neue Regel erstellen möchten, können Sie im linken Bereich unter **Regeln** die Option **+ Neu** auswählen.

## <a name="configure-jobs"></a>Konfigurieren von Aufträgen

In Azure IoT Central können Sie Aufträge verwenden, um Geräte- oder Cloudeigenschaften für mehrere Geräte zu aktualisieren. Sie können auch Aufträge verwenden, um Gerätebefehle auf mehreren Geräten auszulösen. Der Workflow wird von Azure IoT Central für Sie automatisiert. 

1. Wählen Sie im linken Bereich von Azure IoT Central die Option **Aufträge** aus. 
1. Wählen Sie **+ Neu** aus, und konfigurieren Sie eine beliebige Anzahl von Aufträgen. 

## <a name="customize-your-application"></a>Anpassen Ihrer Anwendung 

Als Lösungsersteller können Sie verschiedene Einstellungen ändern, um die Benutzeroberfläche in Ihrer Anwendung anzupassen.

### <a name="change-the-application-theme"></a>Ändern des Anwendungsdesigns

Gehen Sie dabei folgendermaßen vor:
1. Navigieren Sie zu **Verwaltung** > **Anwendung anpassen**.
1. Wählen Sie **Ändern** aus, um ein Bild auszuwählen, das Sie als **Anwendungslogo** hochladen möchten.
1. Wählen Sie **Ändern** aus, um ein Bild auszuwählen, das Sie als **Browsersymbol** (Bild, das auf Browsertabs angezeigt wird) hochladen möchten.
1. Sie können auch hexadezimale HTML-Farbcodes hinzufügen, um die standardmäßigen Browserfarben zu ersetzen. Verwenden Sie hierzu die Felder **Header** und **Akzent**.

   ![Screenshot: Vorlage für vernetzte Abfallwirtschaft: Anpassen Ihrer Anwendung](./media/tutorial-connectedwastemanagement/connectedwastemanagement-customize-your-application.png)

1. Sie können auch Anwendungsbilder ändern. Wählen Sie **Verwaltung** > **Anwendungseinstellungen** > **Bild auswählen** aus, um ein Bild auszuwählen, das Sie als Anwendungsbild hochladen möchten.
1. Abschließend können Sie noch das Design ändern, indem Sie im Mastertitel der Anwendung die Option **Einstellungen** auswählen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie diese Anwendung nicht weiterverwenden möchten, sollten Sie sie mit den folgenden Schritten löschen:

1. Wählen Sie im linken Bereich Ihrer Azure IoT Central-App die Option **Verwaltung** aus.
1. Wählen Sie **Anwendungseinstellungen** > **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Konzepte im Zusammenhang mit der vernetzten Abfallwirtschaft ](./concepts-connectedwastemanagement-architecture.md)
