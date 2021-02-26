---
title: 'Tutorial: Erstellen einer App zur Überwachung der Wasserqualität mit Azure IoT Central'
description: 'Tutorial: Es wird beschrieben, wie Sie mit Azure IoT Central-Anwendungsvorlagen eine Anwendung zur Überwachung der Wasserqualität erstellen.'
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: a928b1223b91faae6d4aca68b81cc16c827933b3
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820063"
---
# <a name="tutorial-create-a-water-quality-monitoring-application-in-azure-iot-central"></a>Tutorial: Erstellen einer Anwendung zur Überwachung der Wasserqualität in Azure IoT Central

In diesem Tutorial wird Schritt für Schritt die Erstellung einer Anwendung zur Überwachung der Wasserqualität in Azure IoT Central beschrieben. Sie erstellen die Anwendung mit der Azure IoT Central-Anwendungsvorlage **Überwachung der Wasserqualität**.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Verwenden der Vorlage **Überwachung der Wasserqualität** für die Erstellung einer entsprechenden Anwendung
> * Erkunden und Anpassen eines Bedienerdashboards
> * Erkunden einer Gerätevorlage für die Überwachung der Wasserqualität
> * Erkunden von simulierten Geräten
> * Erkunden und Konfigurieren von Regeln
> * Konfigurieren von Aufträgen
> * Anpassen des Brandings von Anwendungen per Whitelabeling

## <a name="prerequisites"></a>Voraussetzungen

Wir empfehlen Ihnen, zum Durcharbeiten dieses Tutorials ein Azure-Abonnement zu nutzen. Wenn Sie kein Azure-Abonnement besitzen, können Sie auf der Seite [Azure-Anmeldeseite](https://aka.ms/createazuresubscription) eines erstellen.

## <a name="create-a-water-quality-monitoring-application-in-azure-iot-central"></a>Erstellen einer Anwendung zur Überwachung der Wasserqualität in Azure IoT Central

In diesem Abschnitt verwenden Sie die Azure IoT Central-Vorlage **Überwachung der Wasserqualität**, um eine entsprechende Anwendung zu erstellen.

1. Navigieren Sie zur [Startseite von Azure IoT Central](https://aka.ms/iotcentral).

    Wenn Sie über ein Azure-Abonnement verfügen, können Sie sich mit den zugehörigen Anmeldeinformationen anmelden. Melden Sie sich andernfalls mit einem Microsoft-Konto an:

    ![Anmelden mit Ihrem Organisationskonto](./media/tutorial-waterqualitymonitoring/sign-in.png)

1. Wählen Sie in Azure IoT Central im Bereich ganz links die Option **Build** und dann die Registerkarte **Regierung** aus. Im Bereich „Regierung“ werden mehrere Anwendungsvorlagen für den Behördenbereich angezeigt.

    ![Anwendungsvorlagen für den Behördenbereich](./media/tutorial-waterqualitymonitoring/iotcentral-government-tab-overview1.png)

1. Wählen Sie die Anwendungsvorlage **Überwachung der Wasserqualität** aus. Diese Anwendungsvorlage enthält eine Gerätevorlage für die Wasserqualität, simulierte Geräte, ein Bedienerdashboard und vorkonfigurierte Überwachungsregeln.

1. Wählen Sie **App erstellen** aus. Der Bereich **Neue Anwendung** mit den folgenden Elementen wird geöffnet:

    * **Anwendungsname**: Standardmäßig lautet der Anwendungsname **Überwachung der Wasserqualität**, gefolgt von einer eindeutigen ID-Zeichenfolge, die von Azure IoT Central generiert wird. Bei Bedarf können Sie einen Anzeigenamen eingeben oder den Anwendungsnamen später ändern.
    * **URL**: Sie können eine beliebige URL eingeben oder den URL-Wert später ändern.
    * Geben Sie Werte für **Verzeichnis**, **Azure-Abonnement** und **Region** ein, wenn Sie über ein Azure-Abonnement verfügen. Wenn Sie kein Abonnement haben, können Sie eine **7-tägige kostenlose Testversion** aktivieren und die erforderlichen Kontaktinformationen eingeben.

    Weitere Informationen zu Verzeichnissen und Abonnements finden Sie in der Schnellstartanleitung unter [Erstellen einer Azure IoT Central-Anwendung](../core/quick-deploy-iot-central.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

1. Wählen Sie unten links auf der Seite die Schaltfläche **Erstellen** aus.

    ![Seite für neue Anwendung in Azure IoT Central](./media/tutorial-waterqualitymonitoring/new-application-waterqualitymonitoring1.png)

    ![Neue Anwendung in Azure IoT Central: Abrechnungsinformationen](./media/tutorial-waterqualitymonitoring/new-application-waterqualitymonitoring1-billinginfo.png)

Sie haben nun eine Anwendung zur Überwachung der Wasserqualität erstellt, indem Sie die Azure IoT Central-Vorlage **Überwachung der Wasserqualität** verwendet haben.

Ihre neue Anwendung verfügt über die folgenden vorkonfigurierten Komponenten:

* Bedienerdashboards
* Gerätevorlagen für die Überwachung der Wasserqualität
* Simulierte Geräte zur Überwachung der Wasserqualität
* Regeln und Aufträge
* Branding mit Whitelabeling

Sie können Ihre Anwendung jederzeit ändern.

Erkunden Sie als Nächstes die Anwendung, und nehmen Sie einige Anpassungen vor.

## <a name="explore-and-customize-the-operator-dashboard"></a>Erkunden und Anpassen des Bedienerdashboards

Nachdem Sie die Anwendung erstellt haben, wird der Bereich mit dem **Wide World Water-Dashboard zur Wasserqualität** geöffnet.

   ![Dashboard für die Überwachung der Wasserqualität](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-dashboard1.png)

Als Lösungsentwickler können Sie im Dashboard Ansichten für Bediener erstellen und anpassen. Erkunden Sie aber zunächst das Dashboard, bevor Sie die Anpassungen durchführen.

Alle im Dashboard angezeigten Daten basieren auf simulierten Gerätedaten. Dies wird im nächsten Abschnitt beschrieben.

Das Dashboard enthält die folgenden Arten von Kacheln:

* **Wide World Water Utility-Bildkachel**: Die erste Kachel oben links im Dashboard ist eine Bildkachel für das fiktive Versorgungsunternehmen „Wide World Water“. Sie können die Kachel anpassen, um Ihr eigenes Bild zu verwenden, oder Sie können sie entfernen.

* **KPI-Kachel für pH-Mittelwert**: KPI-Kacheln, z. B. **Average pH in the last 30 minutes** (pH-Mittelwert in den letzten 30 Minuten) sind oben im Dashboardbereich angeordnet. Sie können KPI-Kacheln anpassen und dafür jeweils einen anderen Typ und Zeitbereich festlegen.

* **Karte mit Bereich der Wasserüberwachung**: Für Azure IoT Central wird Azure Maps verwendet. Sie können dieses Feature direkt in Ihrer Anwendung festlegen, um den Gerätestandort anzuzeigen. Sie können Ihrem Gerät auch die Standortinformationen Ihrer Anwendung zuordnen und dann Azure Maps verwenden, um die Informationen auf einer Karte anzuzeigen. Bewegen Sie den Mauszeiger auf die Karte, und probieren Sie die Steuerelemente aus.

* **Wärmebild zur Verteilung des pH-Mittelwerts**: Sie können verschiedene Visualisierungsdiagramme auswählen, um die Gerätetelemetriedaten so anzuzeigen, wie dies für Ihre Anwendung am besten geeignet ist.

* **Liniendiagramm zu wichtigen Qualitätsindikatoren**: Sie können die Gerätetelemetriedaten so visualisieren, dass sie als Liniendiagramm mit einem bestimmten Zeitbereich dargestellt werden.  

* **Balkendiagramm zur Konzentration chemischer Substanzen**: Sie können Gerätetelemetriedaten als Balkendiagramm visualisieren.

* **Schaltfläche „Aktion“** : Das Dashboard enthält eine Kachel für Aktionen, die ein Bediener direkt über das Dashboard für die Überwachung initiieren kann. Das Zurücksetzen der Eigenschaften eines Geräts ist ein Beispiel für Aktionen dieser Art.

* **Kacheln mit Eigenschaftenlisten**: Das Dashboard verfügt über mehrere Kacheln mit Eigenschaften, die für Informationen zu Schwellenwert, Geräteintegrität und Wartung stehen.

### <a name="customize-the-dashboard"></a>Anpassen des Dashboards

Als Lösungsentwickler können Sie im Dashboard Ansichten für Bediener anpassen.

1. Wählen Sie die Option **Bearbeiten** aus, um den Bereich **Wide World Water-Dashboard zur Wasserqualität** anzupassen. Sie können das Dashboard anpassen, indem Sie Befehle im Menü **Bearbeiten** auswählen. Wenn sich das Dashboard im Modus „Bearbeiten“ befindet, können Sie neue Kacheln hinzufügen oder die vorhandenen Dateien konfigurieren.

    ![Bearbeiten Ihres Dashboards](./media/tutorial-waterqualitymonitoring/edit-dashboard.png)

1. Wählen Sie die Option **+ Neu** aus, um ein neues Dashboard zu erstellen, das Sie konfigurieren können. Sie können mehrere Dashboards nutzen und über das Dashboardmenü dazwischen navigieren.

## <a name="explore-a-water-quality-monitoring-device-template"></a>Erkunden einer Gerätevorlage für die Überwachung der Wasserqualität

Mit einer Gerätevorlage in Azure IoT Central werden die Funktionen eines Geräts definiert. Die verfügbaren Funktionen sind Telemetrie, Eigenschaften und Befehle. Als Lösungsentwickler können Sie in Azure IoT Central Gerätevorlagen für die Funktionen der angeschlossenen Geräte definieren. Außerdem können Sie simulierte Geräte erstellen, um Ihre Gerätevorlage und die Anwendung zu testen.

Die von Ihnen erstellte Anwendung zur Überwachung der Wasserqualität verfügt über eine Gerätevorlage zur Überwachung der Wasserqualität.

Zeigen Sie die Gerätevorlage wie folgt an:

1. Wählen Sie in Azure IoT Central ganz links im Bereich für Ihre Anwendung die Option **Gerätevorlagen** aus.
1. Wählen Sie in der Liste mit den Gerätevorlagen die Option **Überwachung der Wasserqualität**  aus. Die entsprechende Gerätevorlage wird geöffnet.

    ![Gerätevorlage](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate.png)

### <a name="customize-the-device-template"></a>Anpassen der Gerätevorlage

Üben Sie das Anpassen der folgenden Einstellungen für Gerätevorlagen:

1. Wählen Sie im Gerätevorlagenmenü die Option **Anpassen** aus.
1. Navigieren Sie zum Telemetrietyp **Temperatur**.
1. Ändern Sie den Wert **Anzeigename** in **Reported temperature** (Gemeldete Temperatur).
1. Ändern Sie die Maßeinheit, oder legen Sie Werte für **Minimalwert** und **Maximalwert** fest.
1. Wählen Sie **Speichern** aus.

#### <a name="add-a-cloud-property"></a>Hinzufügen einer Cloudeigenschaft

1. Wählen Sie im Gerätevorlagenmenü die Option **Cloudeigenschaft** aus.
1. Wählen Sie zum Hinzufügen einer neuen Cloudeigenschaft die Option **+ Cloudeigenschaft hinzufügen** aus. In Azure IoT Central können Sie eine Eigenschaft hinzufügen, die für ein Gerät relevant ist, aber für die das Senden durch das Gerät nicht erwartet wird. Ein Beispiel für eine Eigenschaft dieser Art ist ein Warnungsschwellenwert, der für einen bestimmten Installationsbereich oder bestimmte Ressourcen- oder Wartungsinformationen gilt.
1. Wählen Sie **Speichern** aus.

### <a name="explore-views"></a>Erkunden von Ansichten

Die Gerätevorlage „Überwachung der Wasserqualität“ verfügt über vordefinierte Ansichten. Mit den Ansichten wird definiert, wie die Gerätedaten für Bediener angezeigt werden und wie diese die Cloudeigenschaften festlegen können. Erkunden Sie die Ansichten, und üben Sie das Vornehmen von Änderungen.

  ![Ansichten der Gerätevorlage](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate-views.png)

### <a name="publish-the-device-template"></a>Veröffentlichen der Gerätevorlage

Achten Sie beim Vornehmen von Änderungen darauf, dass Sie die Option **Veröffentlichen** auswählen, um die Gerätevorlage zu veröffentlichen.

### <a name="create-a-new-device-template"></a>Erstellen einer neuen Gerätevorlage

1. Wählen Sie die Option **+ Neu** aus, um eine neue Gerätevorlage zu erstellen, und führen Sie die Schritte des Erstellungsprozesses aus.
1. Erstellen Sie eine benutzerdefinierte Gerätevorlage, oder wählen Sie im Azure IoT-Gerätekatalog eine Gerätevorlage aus.

## <a name="explore-simulated-devices"></a>Erkunden von simulierten Geräten

Die Anwendung zur Überwachung der Wasserqualität, die Sie mit der Anwendungsvorlage erstellt haben, verfügt über zwei simulierte Geräte. Diese Geräte sind der Gerätevorlage „Überwachung der Wasserqualität“ zugeordnet.

### <a name="view-the-devices"></a>Anzeigen der Geräte

1. Wählen Sie in Ihrer Anwendung im Bereich ganz links die Option **Geräte** aus.

   ![Geräte](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devices.png)

1. Wählen Sie ein simuliertes Gerät aus.

    ![Auswählen von Gerät 1](./media/tutorial-waterqualitymonitoring/waterqualitymonitor-device1.png)

1. Ändern Sie auf der Registerkarte **Cloudeigenschaften** den Wert unter **Acidity (pH) threshold** (Azidität (pH): Schwellenwert) von **8** in **9**.
1. Erkunden Sie die Registerkarten **Geräteeigenschaften** und **Gerätedashboard**.

> [!NOTE]
> Alle Registerkarten wurden über **Ansichten der Gerätevorlage** konfiguriert.

### <a name="add-new-devices"></a>Hinzufügen neuer Geräte

Wählen Sie auf der Registerkarte **Geräte** die Option **+ Neu** aus, um ein neues Gerät hinzuzufügen.

## <a name="explore-and-configure-rules"></a>Erkunden und Konfigurieren von Regeln

In Azure IoT Central können Sie Regeln erstellen, mit denen die Gerätetelemetrie automatisch überwacht wird. Diese Regeln lösen eine Aktion aus, wenn eine entsprechende Bedingung erfüllt ist. Eine mögliche Aktion ist das Senden von E-Mail-Benachrichtigungen. Andere Möglichkeiten sind eine Power Automate-Aktion oder eine Webhookaktion zum Senden von Daten an andere Dienste.

Die von Ihnen erstellte Anwendung zur Überwachung der Wasserqualität verfügt über zwei vorkonfigurierte Regeln.

### <a name="view-rules"></a>Anzeigen von Regeln

1. Wählen Sie in Ihrer Anwendung im Bereich ganz links die Option **Regeln** aus.

   ![Regeln](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-rules.png)

1. Wählen Sie die Option **High pH alert** (Warnung: Hoher pH-Wert) aus. Dies ist eine der vorkonfigurierten Regeln in der Anwendung.

   ![Warnungsregel für hohen pH-Wert](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-highphalert.png)

   Die Regel **High pH alert** (Warnung: Hoher pH-Wert) ist so konfiguriert, dass überprüft wird, ob der Wert für die Azidität (pH-Wert) höher als 8 ist.

Fügen Sie der Regel als Nächstes eine E-Mail-Aktion hinzu:

1. Wählen Sie **+ E-Mail** aus.
1. Geben Sie im Feld **Anzeigename** den Namen **High pH alert** (Warnung: Hoher pH-Wert) ein.
1. Geben Sie im Feld **An** die E-Mail-Adresse ein, die Ihrem Azure IoT Central-Konto zugeordnet ist.
1. Geben Sie optional eine Notiz ein, die in den Text der E-Mail aufgenommen werden soll.
1. Wählen Sie **Fertig** aus, um die Aktion fertig zu stellen.
1. Wählen Sie **Speichern** aus, um die neue Regel zu speichern und zu aktivieren.

Sie sollten nach einigen Minuten eine E-Mail mit dem Hinweis erhalten, dass die konfigurierte Bedingung erfüllt wurde.

> [!NOTE]
> Von der Anwendung wird jedes Mal eine E-Mail gesendet, wenn eine Bedingung erfüllt ist. Wählen Sie die Option **Deaktivieren** für eine Regel aus, um den Empfang von automatisierten E-Mails über diese Regel zu beenden.
  
Wählen Sie zum Erstellen einer neuen Regel ganz links in Ihrer Anwendung die Option **Regeln** und dann **+ Neu** aus.

## <a name="configure-jobs"></a>Konfigurieren von Aufträgen

Mit Azure IoT Central-Aufträgen können Sie Updates für Geräte- oder Cloudeigenschaften auf mehreren Geräten auslösen. Sie können auch Aufträge verwenden, um Gerätebefehle auf mehreren Geräten auszulösen. Der Workflow wird von Azure IoT Central für Sie automatisiert.

1. Wählen Sie in Ihrer Anwendung im Bereich ganz links die Option **Aufträge** aus.
1. Wählen Sie **+ Neu** aus, und konfigurieren Sie einen oder mehrere Aufträge.

## <a name="customize-your-application"></a>Anpassen Ihrer Anwendung

Als Lösungsersteller können Sie verschiedene Einstellungen ändern, um die Benutzeroberfläche in Ihrer Anwendung anzupassen.

1. Wählen Sie **Verwaltung** > **Anwendung anpassen** aus.
1. Wählen Sie unter **Anwendungslogo** die Option **Ändern** aus, um das Bild auszuwählen, das als Logo hochgeladen werden soll.
1. Wählen Sie unter **Browsersymbol** die Option **Ändern** aus, um das Bild für die Anzeige auf Browsertabs auszuwählen.
1. Unter **Browserfarben** können Sie die Standardwerte durch hexadezimale HTML-Farbcodes ersetzen.
1. Wählen Sie **Einstellungen** aus, um den Wert von **Design** zu ändern.

   ![Anpassen Ihrer Anwendung](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-customize-your-application1.png)

### <a name="update-the-application-image"></a>Aktualisieren des Anwendungsbilds

1. Wählen Sie **Verwaltung** > **Anwendungseinstellungen** aus.

1. Verwenden Sie die Schaltfläche **Bild auswählen**, um ein Bild auszuwählen, das als Anwendungsbild hochgeladen werden soll.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie Ihre Anwendung nicht weiterverwenden möchten, sollten Sie sie mit den folgenden Schritten löschen:

1. Öffnen Sie in Ihrer Anwendung ganz links die Registerkarte **Verwaltung**.
1. Wählen Sie **Anwendungseinstellungen** und dann die Schaltfläche **Löschen** aus.

    ![Löschen Ihrer Anwendung](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-application-settings-delete-app1.png)

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen 

> [!div class="nextstepaction"]
> [Überwachung der Wasserqualität: Referenzarchitektur](./concepts-waterqualitymonitoring-architecture.md)
