---
title: 'Tutorial: Einrichten einer Gen2-Umgebung – Azure Time Series Insights Gen2 | Microsoft-Dokumentation'
description: 'Tutorial: Hier erfahren Sie, wie Sie eine Umgebung in Azure Time Series Insights Gen2 einrichten.'
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: tutorial
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: eeb3de2fc3f0e3e0be9c98002f11e470eaf04f8c
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020928"
---
# <a name="tutorial-set-up-an-azure-time-series-insights-gen2-environment"></a>Tutorial: Einrichten einer Azure Time Series Insights Gen2-Umgebung

Dieses Tutorial führt Sie durch das Erstellen einer Azure Time Series Insights Gen2-Umgebung mit *nutzungsbasierter Bezahlung* (Pay-As-You-Go, PAYG).

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Erstellen einer Azure Time Series Insights Gen2-Umgebung
> * Herstellen einer Verbindung der Azure Time Series Insights Gen2-Umgebung mit einer IoT Hub-Instanz
> * Ausführen eines Solution Accelerator-Beispiels zum Streamen von Daten in die Azure Time Series Insights Gen2-Umgebung
> * Durchführen einer grundlegenden Analyse für die Daten
> * Definieren eines Zeitreihenmodell-Typs und einer Hierarchie und Zuordnen zu Ihren Instanzen

>[!TIP]
> Mit [IoT Solution Accelerators](https://www.azureiotsolutions.com/Accelerators) werden für Unternehmen konzipierte vorkonfigurierte Lösungen bereitgestellt, mit denen Sie die Entwicklung benutzerdefinierter IoT-Lösungen beschleunigen können.

Registrieren Sie sich für ein [kostenloses Azure-Abonnement](https://azure.microsoft.com/free/), falls Sie noch keins besitzen.

## <a name="prerequisites"></a>Voraussetzungen

* Sie müssen mindestens über die Rolle **Mitwirkender** für das Azure-Abonnement verfügen. Weitere Informationen finden Sie unter [Hinzufügen oder Entfernen von Rollenzuweisungen mithilfe des Azure-Portals](../role-based-access-control/role-assignments-portal.md).

## <a name="create-a-device-simulation"></a>Erstellen einer Gerätesimulation

In diesem Abschnitt werden Sie drei simulierte Geräte erstellen, die Daten an eine Azure IoT Hub-Instanz senden.

1. Öffnen Sie die Seite [Azure IoT Solution Accelerators](https://www.azureiotsolutions.com/Accelerators). Auf dieser Seite finden Sie mehrere vorgefertigte Beispiele. Melden Sie sich mit Ihrem Azure-Konto an. Wählen Sie dann **Gerätesimulation** aus.

   [![Seite „Azure IoT Solution Accelerators“](media/v2-update-provision/iot-solution-accelerators-landing-page.png)](media/v2-update-provision/iot-solution-accelerators-landing-page.png#lightbox)

1. Geben Sie das Abonnement ein, in dem Sie die Gerätesimulation erstellen möchten.

   Parameter|BESCHREIBUNG
   ---|---
   **Bereitstellungsname** | Mit diesem eindeutigen Wert wird eine neue Ressourcengruppe erstellt. Die aufgelisteten Azure-Ressourcen werden erstellt und der Ressourcengruppe zugewiesen.
   **Azure-Abonnement** | Geben Sie dasselbe Abonnement an, das Sie auch bei der Erstellung Ihrer Azure Time Series Insights Gen2-Umgebung im vorherigen Abschnitt verwendet haben.
   **Bereitstellungsoptionen** | Wählen Sie **Provision new IoT Hub** (Neuen IoT Hub bereitstellen) aus, um einen für dieses Tutorial spezifischen neuen IoT-Hub zu erstellen.
   **Azure-Standort** | Geben Sie dieselbe Region an, die Sie auch bei der Erstellung Ihrer Azure Time Series Insights Gen2-Umgebung im vorherigen Abschnitt verwendet haben.

   Wählen Sie nach Abschluss des Vorgangs die Option **Lösung erstellen** aus, um die Azure-Ressourcen der Lösung bereitzustellen. Dieser Vorgang kann bis zu 20 Minuten dauern.

   [![Bereitstellen der Gerätesimulationslösung](media/v2-update-provision/iot-solution-accelerators-configuration.png)](media/v2-update-provision/iot-solution-accelerators-configuration.png#lightbox)

1. Nach Abschluss der Bereitstellung werden zwei Benachrichtigungen angezeigt, die angeben, dass der Bereitstellungsstatus von **Wird bereitgestellt** in **Bereit** geändert wurde.

   >[!IMPORTANT]
   > Geben Sie noch nicht Ihre Solution Accelerator-Instanz an. Lassen Sie diese Webseite geöffnet, da Sie später hierhin zurückkehren.

   [![Bereitstellungen der Gerätesimulationslösung abgeschlossen](media/v2-update-provision/iot-solution-accelerator-ready.png)](media/v2-update-provision/iot-solution-accelerator-ready.png#lightbox)

1. Anschließend können Sie die neu erstellten Ressourcen im Azure-Portal überprüfen. Auf der Seite **Ressourcengruppen** wurde eine neue Ressourcengruppe mit dem **Lösungsnamen** erstellt, den Sie im letzten Schritt angegeben haben. Notieren Sie sich die Ressourcen, die für die Gerätesimulation erstellt wurden.

   [![Ressourcen für die Gerätesimulation](media/v2-update-provision/tsi-device-sim-solution-resources.png)](media/v2-update-provision/tsi-device-sim-solution-resources.png#lightbox)

## <a name="create-an-azure-time-series-insights-gen2-environment"></a>Erstellen einer Azure Time Series Insights Gen2-Umgebung

In diesem Abschnitt wird beschrieben, wie Sie eine Azure Time Series Insights Gen2-Umgebung erstellen und mit dem IoT-Hub verbinden, der vom IoT Solution Accelerator über das [Azure-Portal](https://portal.azure.com/) erstellt wurde.

1. Melden Sie sich unter Verwendung Ihres Azure-Abonnementkontos beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie oben links die Option **+ Ressource erstellen**.
1. Wählen Sie die Kategorie **Internet der Dinge (IoT)** und dann **Time Series Insights** aus.

   [![Auswählen der Time Series Insights-Umgebungsressource](media/v2-update-provision/tsi-create-new-environment.png)](media/v2-update-provision/tsi-create-new-environment.png#lightbox)

1. Legen Sie im Bereich **Time Series Insights-Umgebung erstellen** auf der Registerkarte **Grundlagen** die folgenden Parameter fest:

    | Parameter | Aktion |
    | --- | ---|
    | **Umgebungsname** | Geben Sie einen eindeutigen Namen für die Azure Time Series Insights Gen2-Umgebung ein. |
    | **Abonnement** | Geben Sie das Abonnement ein, in dem Sie die Azure Time Series Insights Gen2-Umgebung erstellen möchten. Eine bewährte Methode ist die Verwendung des Abonnements, das Sie auch für die anderen vom Gerätesimulator erstellten IoT-Ressourcen verwenden. |
    | **Ressourcengruppe** | Wählen Sie eine vorhandene Ressourcengruppe für die Ressource der Azure Time Series Insights Gen2-Umgebung aus, oder erstellen Sie eine neue Ressourcengruppe. Eine Ressourcengruppe ist ein Container für Azure-Ressourcen. Eine bewährte Methode ist die Verwendung der Ressourcengruppe, die Sie auch für die anderen vom Gerätesimulator erstellten IoT-Ressourcen verwenden. |
    | **Location** | Wählen Sie eine Rechenzentrumsregion für Ihre Azure Time Series Insights Gen2-Umgebung aus. Um zusätzliche Wartezeiten zu vermeiden, empfiehlt es sich, die Azure Time Series Insights Gen2-Umgebung in derselben Region wie den IoT-Hub zu erstellen, der vom Gerätesimulator erstellt wurde. |
    | **Tier** |  Wählen Sie **Gen2(L1)** aus. Dies ist die SKU für das Azure Time Series Insights Gen2-Produkt. |
    | **Name der Zeitreihen-ID-Eigenschaft** | Geben Sie einen Namen für eine Eigenschaft ein, die Werte enthält, die ihre Zeitreiheninstanzen eindeutig identifizieren. Der Wert, den Sie in das Feld **Eigenschaftenname** als Zeitreihen-ID eingeben, kann später nicht geändert werden. Geben Sie für dieses Tutorial **_iothub-connection-device-id_* _ ein. Weitere Informationen zur Zeitreihen-ID (einschließlich der zusammengesetzten Zeitreihen-ID) finden Sie unter [Bewährte Methoden für die Auswahl einer Time Series-ID](./how-to-select-tsid.md). |
    | _ *Speicherkontoname** | Geben Sie einen global eindeutigen Namen für ein neues Speicherkonto ein.|
    | **Art des Speicherkontos** | Wählen Sie die Speicherart für ein neues Speicherkonto aus. Empfohlen wird StorageV2.|
    | **Speicherkontoreplikation** | Wählen Sie die Speicherart für ein neues Speicherkonto aus. Basierend auf der Auswahl des Standorts können Sie zwischen LRS, GRS und ZRS wählen. Für dieses Tutorial können Sie LRS auswählen.|
    | **Hierarchischer Namespace** |Diese Option ist auswählbar, wenn Sie die Speicherart auf „StorageV2“ festgelegt haben. Sie ist standardmäßig deaktiviert. In diesem Tutorial können Sie die Standardeinstellung *Deaktiviert* übernehmen.|
    |**Warm Storage aktivieren**|Wählen Sie **Ja** aus, um den Warm Storage zu aktivieren. Diese Einstellung kann deaktiviert und erneut aktiviert werden, nachdem die Umgebung erstellt wurde. |
    |**Datenaufbewahrung in Tagen**|Wählen Sie die Standardoption von sieben Tagen aus. |

    [![Neue Time Series Insights-Umgebungskonfiguration](media/v2-update-provision/tsi-environment-configuration.png)](media/v2-update-provision/tsi-environment-configuration.png#lightbox)

1. Klicken Sie auf **Weiter: Ereignisquelle**.

   [![Konfigurieren der Zeitreihen-ID für die Umgebung](media/v2-update-provision/tsi-time-series-id-selection.png)](media/v2-update-provision/tsi-time-series-id-selection.png#lightbox)

1. Legen Sie auf der Registerkarte **Ereignisquelle** die folgenden Parameter fest:

   | Parameter | Aktion |
   | --- | --- |
   | **Ereignisquelle erstellen?** | Wählen Sie **Ja** aus.|
   | **Name** | Geben Sie einen eindeutigen Wert für den Namen der Ereignisquelle ein. |
   | **Quellentyp** | Wählen Sie **IoT Hub** aus. |
   | **Hub auswählen** | Wählen Sie **Vorhandenen auswählen** aus. |
   | **Abonnement** | Wählen Sie das Abonnement aus, das Sie für den Gerätesimulator verwendet haben. |
   | **IoT Hub-Name** | Wählen Sie den Namen des IoT-Hubs aus, den Sie für den Gerätesimulator erstellt haben. |
   | **Zugriffsrichtlinie für IoT Hub** | Wählen Sie **iothubowner** aus. |
   | **IoT Hub-Consumergruppe** | Wählen Sie **Neu** aus, geben Sie einen eindeutigen Namen ein, und wählen Sie anschließend **+ Hinzufügen** aus. Bei der Consumergruppe muss es sich um einen eindeutigen Wert in Azure Time Series Insights Gen2 handeln. |
   | **Timestamp property** (Timestamp-Eigenschaft) | Mit diesem Wert wird die Eigenschaft **Zeitstempel** in Ihren eingehenden Telemetriedaten identifiziert. Lassen Sie das Feld für dieses Tutorial leer. Dieser Simulator verwendet den aus IoT Hub eingehenden Zeitstempel, der standardmäßig von Azure Time Series Insights Gen2 verwendet wird. |

1. Klicken Sie auf **Überprüfen + erstellen**.

   [![Konfigurieren des erstellten IoT-Hubs als Ereignisquelle](media/v2-update-provision/tsi-configure-event-source.png)](media/v2-update-provision/tsi-configure-event-source.png#lightbox)

1. Klicken Sie auf **Erstellen**.

    [![Seite „Überprüfen + erstellen“ mit der Schaltfläche „Erstellen“](media/v2-update-provision/tsi-environment-confirmation.png)](media/v2-update-provision/tsi-environment-confirmation.png#lightbox)

    Sie können den Status Ihrer Bereitstellung überprüfen:

    [![Benachrichtigung, dass die Bereitstellung abgeschlossen ist](media/v2-update-provision/tsi-deployment-notification.png)](media/v2-update-provision/tsi-deployment-notification.png#lightbox)

1. Wenn Sie ein Besitzer des Azure-Abonnements sind, haben Sie standardmäßig Zugriff auf Ihre Azure Time Series Insights Gen2-Umgebung. Vergewissern Sie sich, dass Sie Zugriff haben:

   1. Suchen Sie nach Ihrer Ressourcengruppe, und wählen Sie dann Ihre neu erstellte Azure Time Series Insights Gen2-Umgebung aus:

      [![Auswählen und Anzeigen Ihrer Umgebung](media/v2-update-provision/verify-tsi-resource-in-group.png)](media/v2-update-provision/verify-tsi-resource-in-group.png#lightbox)

   1. Wählen Sie auf der Seite für Azure Time Series Insights Gen2 die Option **Datenzugriffsrichtlinien** aus:

      [![Überprüfen der Datenzugriffsrichtlinien](media/v2-update-provision/tsi-data-access-panel.png)](media/v2-update-provision/tsi-data-access-panel.png#lightbox)

   1. Stellen Sie sicher, dass Ihre Anmeldeinformationen aufgelistet sind:

      Falls Ihre Anmeldeinformationen nicht aufgelistet sind, müssen Sie sich selbst die Zugriffsberechtigung für die Umgebung erteilen, indem Sie „Hinzufügen“ auswählen und nach Ihren Anmeldeinformationen suchen. Weitere Informationen zum Festlegen von Berechtigungen finden Sie unter [Gewähren von Datenzugriff für eine Umgebung](./concepts-access-policies.md).

## <a name="stream-data"></a>Streamen von Daten

Sie haben Ihre Azure Time Series Insights Gen2-Umgebung bereitgestellt und können jetzt mit dem Streaming von Daten zur Analyse beginnen.

1. Wechseln Sie zurück zum [Solution Accelerators-Dashboard](https://www.azureiotsolutions.com/Accelerators#dashboard). Melden Sie sich an, falls dies erforderlich ist, indem Sie dasselbe Azure-Konto wie im gesamten bisherigen Tutorial verwenden. Wählen Sie Ihre „Gerätelösung“ aus, und **wechseln Sie anschließend zu Ihrem Solution Accelerator**, um die bereitgestellte Lösung zu starten.

   [![Solution Accelerators-Dashboard](media/v2-update-provision/iot-solution-accelerator-ready.png)](media/v2-update-provision/iot-solution-accelerator-ready.png#lightbox)

1. Die Web-App für die Gerätesimulation fordert Sie zunächst auf, der Webanwendung die Berechtigung **Sie anmelden und Ihr Profil lesen** zu erteilen. Mit dieser Berechtigung kann die Anwendung die Benutzerprofilinformationen abrufen, die zur Unterstützung der Funktionsweise einer Anwendung erforderlich sind:

   [![Gerätesimulation: Einwilligung für Webanwendung](media/v2-update-provision/sawa-signin-consent.png)](media/v2-update-provision/sawa-signin-consent.png#lightbox)

1. Wählen Sie **+ Neue Simulation** aus.

    1. Geben Sie die erforderlichen Parameter ein, nachdem die Seite mit dem **Simulationssetup** geladen wurde.

        | Parameter | Aktion |
        | --- | --- |
        | **Name** | Geben Sie einen eindeutigen Namen für einen Simulator ein. |
        | **Beschreibung** | Geben Sie eine Definition ein. |
        | **Simulationsdauer** | Verwenden Sie die Option **Run indefinitely** (Unbegrenzt). |
        | **Gerätemodell** | Klicken Sie auf **+ Add a device type** (Gerätetyp hinzufügen). <br />**Name**: Geben Sie **Elevator** (Aufzug) ein. <br />**Menge**: Geben Sie **3** ein. <br /> Belassen Sie die übrigen Werte in ihrer Standardeinstellung. |
        | **IoT-Zielhub** | Verwenden Sie die Option **Use pre-provisioned IoT Hub** (Vorab bereitgestellten IoT-Hub verwenden). |

        [![Konfigurieren der Parameter und Starten](media/v2-update-provision/tsi-launch-solution-accelerator.png)](media/v2-update-provision/tsi-launch-solution-accelerator.png#lightbox)

    1. Wählen Sie **Simulation starten** aus. Auf dem Gerätesimulationsdashboard werden die **aktiven Geräte** und die **Gesamtzahl der Nachrichten** angezeigt.

        [![Azure IoT-Simulationsdashboard](media/v2-update-provision/tsi-see-active-devices-and-messages.png)](media/v2-update-provision/tsi-see-active-devices-and-messages.png#lightbox)

## <a name="analyze-data"></a>Daten analysieren

In diesem Abschnitt führen Sie mit dem [Azure Time Series Insights Gen2-Explorer](./concepts-ux-panels.md) grundlegende Analysen für Ihre Zeitreihendaten durch.

1. Navigieren Sie zu Ihrem Azure Time Series Insights Gen2-Explorer, indem Sie im [Azure-Portal](https://portal.azure.com/) auf der Ressourcenseite die URL auswählen.

    [![URL des Azure Time Series Insights Gen2-Explorers](media/v2-update-provision/tsi-select-explorer-url.png)](media/v2-update-provision/tsi-select-explorer-url.png#lightbox)

1. Im Azure Time Series Insights Gen2-Explorer wird ein Balken angezeigt, der den oberen Rand des Bildschirms umfasst. Dies ist Ihre Verfügbarkeitsauswahl. Vergewissern Sie sich, dass mindestens zwei Minuten ausgewählt sind, und erweitern Sie bei Bedarf den Zeitrahmen, indem Sie die Auswahlhandles auswählen und nach links und rechts ziehen.

1. **Zeitreiheninstanzen** werden auf der linken Seite angezeigt.

    [![Liste der Instanzen ohne übergeordnete Elemente](media/v2-update-provision/tsi-explorer-unparented-instances.png)](media/v2-update-provision/tsi-explorer-unparented-instances.png#lightbox)

1. Wählen Sie die erste Zeitreiheninstanz aus. Wählen Sie anschließend **Temperatur anzeigen** aus.

    [![Ausgewählte Zeitreiheninstanz mit Menübefehl zum Anzeigen der Durchschnittstemperatur](media/v2-update-provision/select-instance-and-temperature.png)](media/v2-update-provision/select-instance-and-temperature.png#lightbox)

    Ein Diagramm mit Zeitreihen wird angezeigt. Ändern Sie den Wert für das **Intervall** in **30** Sekunden.

1. Wiederholen Sie den vorherigen Schritt mit den beiden anderen Zeitreiheninstanzen, sodass alle drei angezeigt werden, wie im folgenden Diagramm dargestellt:

    [![Diagramm mit allen Zeitreihen](media/v2-update-provision/tsi-explorer-add-three-instances.png)](media/v2-update-provision/tsi-explorer-add-three-instances.png#lightbox)

1. Wählen Sie die Zeitspannenauswahl in der oberen rechten Ecke aus. Hier können Sie bestimmte Start- und Endzeiten bis auf die Millisekunde genau auswählen oder aus vorkonfigurierten Optionen wie **Letzte 30 Minuten** auswählen. Sie können auch die Standardzeitzone ändern.

    [![Festlegen des Zeitbereichs auf die letzten 30 Minuten](media/v2-update-provision/tsi-explorer-thirty-minute-time-range.png)](media/v2-update-provision/tsi-explorer-thirty-minute-time-range.png#lightbox)

    Daraufhin wird im Azure Time Series Insights Gen2-Explorer der Solution Accelerator-Verlauf für **Letzte 30 Minuten** angezeigt.

## <a name="define-and-apply-a-model"></a>Definieren und Anwenden eines Modells

In diesem Abschnitt wenden Sie ein Modell zum Strukturieren der Daten an. Um das Modell zu vervollständigen, definieren Sie Typen, Hierarchien und Instanzen. Weitere Informationen zur Datenmodellierung finden Sie unter [Time Series-Modell](./concepts-model-overview.md).

1. Wählen Sie im Explorer die Registerkarte **Modell** aus:

   [![Anzeigen der Registerkarte „Modell“ im Explorer](media/v2-update-provision/tsi-select-model-view.png)](media/v2-update-provision/tsi-select-model-view.png#lightbox)

   Wählen Sie auf der Registerkarte **Typen** die Option **+ Hinzufügen** aus.

1. Legen Sie die folgenden Parameter fest:

    | Parameter | Aktion |
    | --- | ---|
    | **Name** | Geben Sie **Elevator** (Aufzug) ein. |
    | **Beschreibung** | Geben Sie **Dies ist eine Typdefinition für Aufzug** ein. |

1. Wählen Sie als nächstes die Registerkarte **Variablen** aus.

    1. Wählen Sie **+ Add Variable** (Variable hinzufügen) aus, und geben Sie die folgenden Werte für die erste Variable vom Typ „Elevator“ (Aufzug) ein. Sie werden insgesamt drei Variablen erstellen.

        | Parameter | Aktion |
        | --- | --- |
        | **Name** | Geben Sie **Avg Temperature** ein. |
        | **Kind** | Wählen Sie **Numeric** (Numerisch) aus. |
        | **Wert** | Aus Voreinstellung auswählen: Wählen Sie **temperature (Double) (Temperatur (Double))** aus. <br /> Hinweis: Es kann einige Minuten dauern, bis das Feld **Wert** automatisch aufgefüllt wird, nachdem Azure Time Series Insights Gen2 damit beginnt, Ereignisse zu empfangen.|
        | **Aggregationsvorgang** | Erweitern Sie **Erweiterte Optionen**. <br /> Wählen Sie **AVG** aus. |

    1. Wählen Sie **Übernehmen**. Wählen Sie erneut **+ Variable hinzufügen** aus, und legen Sie die folgenden Werte fest:

        | Parameter | Aktion |
        | --- | --- |
        | **Name** | Geben Sie **Avg Vibration** (Durchschnittliche Vibration) ein. |
        | **Kind** | Wählen Sie **Numeric** (Numerisch) aus. |
        | **Wert** | Aus Voreinstellung auswählen: Wählen Sie **Vibration (Double)** aus. <br /> Hinweis: Es kann einige Minuten dauern, bis das Feld **Wert** automatisch aufgefüllt wird, nachdem Azure Time Series Insights Gen2 damit beginnt, Ereignisse zu empfangen.|
        | **Aggregationsvorgang** | Erweitern Sie **Erweiterte Optionen**. <br /> Wählen Sie **AVG** aus. |

    1. Wählen Sie **Übernehmen**. Wählen Sie erneut **+ Variable hinzufügen** aus, und legen Sie die folgenden Werte für die dritte und letzte Variable fest:

        | Parameter | Aktion |
        | --- | --- |
        | **Name** | Geben Sie **Floor** (Etage) ein. |
        | **Kind** | Wählen Sie **Categorical** (Kategorisch) aus. |
        | **Wert** | Aus Voreinstellung auswählen: Wählen Sie **Floor (Double)** (Etage (Double)) aus. <br /> Hinweis: Es kann einige Minuten dauern, bis das Feld **Wert** automatisch aufgefüllt wird, nachdem Azure Time Series Insights Gen2 damit beginnt, Ereignisse zu empfangen.|
        | **Kategorien** | <span style="text-decoration: underline">Bezeichnung </span>  - <span style="text-decoration: underline">Werte</span> <br /> Unten: 1,2,3,4 <br /> Mitte: 5,6,7,8,9 <br /> Oben: 10,11,12,13,14,15 |
        | **Standardkategorie** | Geben Sie **Unbekannt** ein. |

        [![Hinzufügen von Typvariablen](media/v2-update-provision/tsi-add-type-variables.png)](media/v2-update-provision/tsi-add-type-variables.png#lightbox)

    1. Wählen Sie **Übernehmen**. 
    1. Wählen Sie **Speichern** aus. Drei Variablen werden erstellt und angezeigt.

        [![Der hinzugefügte Typ kann in der Modellansicht überprüft werden.](media/v2-update-provision/tsi-add-type-and-view.png)](media/v2-update-provision/tsi-add-type-and-view.png#lightbox)

1. Wählen Sie die Registerkarte **Hierarchies** (Hierarchien) aus. Wählen Sie dann **+ Hinzufügen** aus.

   1. Legen Sie im Bereich **Hierarchie bearbeiten** die folgenden Parameter fest:

        | Parameter | Aktion |
        | --- | ---|
        | **Name** | Geben Sie **Location Hierarchy** ein. |
        |**Ebenen**| Geben Sie **Country** (Land/Region) als Namen der ersten Ebene ein. <br /> Wählen Sie **+ Add Level** (Ebene hinzufügen) aus. <br /> Geben Sie **City** (Ort) für die zweite Ebene ein, und wählen Sie dann **+ Add Level** (Ebene hinzufügen) aus. <br /> Geben Sie **Building** (Gebäude) als Namen der dritten und letzten Ebene ein. |

   1. Wählen Sie **Speichern** aus.

        [![Anzeigen Ihrer neuen Hierarchie in der Modellansicht](media/v2-update-provision/tsi-add-hierarchy-and-view.png)](media/v2-update-provision/tsi-add-hierarchy-and-view.png#lightbox)

1. Navigieren Sie zu **Instanzen**.

    1. Wählen Sie ganz rechts unter den **Aktionen** das Stiftsymbol aus, um die erste Instanz mit den folgenden Werten zu bearbeiten:

        | Parameter | Aktion |
        | --- | --- |
        | **Typ** | Wählen Sie **Elevator** (Aufzug) aus. |
        | **Name** | Geben Sie **Elevator 1** (Aufzug 1) ein.|
        | **Beschreibung** | Geben Sie **Instance for Elevator 1** (Instanz für Aufzug 1) ein. |

    1. Navigieren Sie zu **Instance Fields** (Instanzfelder), und geben Sie folgende Werte ein:

        | Parameter | Aktion |
        | --- | --- |
        | **Hierarchien** | Wählen Sie **Location Hierarchy** (Standorthierarchie) aus. |
        | **Country** | Geben Sie **USA** ein. |
        | **City (Ort)** | Geben Sie **Seattle** ein. |
        | **Building (Gebäude)** | Geben Sie **Space Needle** ein. |

    1. Wählen Sie **Speichern** aus.

1. Wiederholen Sie den vorherigen Schritt mit den beiden anderen Instanzen und folgenden Werten:

    **Elevator 2** (Aufzug 2):

    | Parameter | Aktion |
    | --- | --- |
    | **Typ** | Wählen Sie **Elevator** (Aufzug) aus. |
    | **Name** | Geben Sie **Elevator 2** (Aufzug 2) ein.|
    | **Beschreibung** | Geben Sie **Instance for Elevator 2** (Instanz für Aufzug 2) ein. |
    | **Hierarchien** | Wählen Sie **Location Hierarchy** (Standorthierarchie) aus. |
    | **Country** | Geben Sie **USA** ein. |
    | **City (Ort)** | Geben Sie **Seattle** ein. |
    | **Building (Gebäude)** | Geben Sie **Pacific Science Center** ein. |

    **Elevator 3** (Aufzug 3):

    | Parameter | Aktion |
    | --- | --- |
    | **Typ** | Wählen Sie **Elevator** (Aufzug) aus. |
    | **Name** | Geben Sie **Elevator 3** (Aufzug 3) ein.|
    | **Beschreibung** | Geben Sie **Instance for Elevator 3** (Instanz für Aufzug 3) ein. |
    | **Hierarchien** | Wählen Sie **Location Hierarchy** (Standorthierarchie) aus. |
    | **Country** | Geben Sie **USA** ein. |
    | **City (Ort)** | Geben Sie **New York** ein. |
    | **Building (Gebäude)** | Geben Sie **Empire State Building** ein. |

    [![Anzeigen der aktualisierten Instanzen](media/v2-update-provision/iot-solution-accelerator-instances.png)](media/v2-update-provision/iot-solution-accelerator-instances.png#lightbox)

1. Navigieren Sie zurück zur Registerkarte **Analyze** (Analysieren), um den Diagrammbereich anzuzeigen. Erweitern Sie unter **Location Hierarchy** alle Hierarchieebenen, um die Zeitreiheninstanzen anzuzeigen:

    [![Anzeigen aller Hierarchien in der Diagrammansicht](media/v2-update-provision/iot-solution-accelerator-view-hierarchies.png)](media/v2-update-provision/iot-solution-accelerator-view-hierarchies.png#lightbox)

1. Wählen Sie unter **Pacific Science Center** die Zeitreiheninstanz **Elevator 2** (Aufzug 2) und anschließend **Show Average Temperature** (Durchschnittliche Temperatur anzeigen) aus.

1. Wählen Sie für dieselbe Instanz, **Elevator 2** (Aufzug 2), die Option **Show Floor** (Etage anzeigen) aus.

    Mit Ihrer Kategorievariablen können Sie bestimmen, wie viel Zeit der Aufzug auf den oberen, unteren und mittleren Etagen verbracht hat.

    [![Visualisieren von „Elevator 2“ (Aufzug 2) mit Hierarchie und Daten](media/v2-update-provision/iot-solution-accelerator-elevator-two.png)](media/v2-update-provision/iot-solution-accelerator-elevator-two.png#lightbox)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Bereinigen Sie nach dem Tutorial die von Ihnen erstellten Ressourcen:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) im Menü auf der linken Seite **Alle Ressourcen** aus, und suchen Sie Ihre Azure Time Series Insights Gen2-Ressourcengruppe.
1. Löschen Sie entweder die gesamte Ressourcengruppe (und alle darin enthaltenen Ressourcen), indem Sie auf **Löschen** klicken, oder entfernen Sie die Ressourcen einzeln.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:  

* Erstellen und Verwenden eines Accelerators zur Gerätesimulation.
* Erstellen einer Azure Time Series Insights Gen2-PAYG-Umgebung
* Herstellen einer Verbindung der Azure Time Series Insights Gen2-Umgebung mit einem IoT-Hub
* Ausführen eines Solution Accelerator-Beispiels zum Streamen von Daten in die Azure Time Series Insights Gen2-Umgebung
* Durchführen einer grundlegenden Analyse der Daten.
* Definieren eines Zeitreihenmodell-Typs und einer Hierarchie und deren Zuordnung zu Ihren Instanzen.

Da Sie nun Ihre eigene Azure Time Series Insights Gen2-Umgebung erstellen können, informieren Sie sich ausführlicher über die wichtigsten Konzepte in Azure Time Series Insights Gen2.

Weitere Informationen zur Azure Time Series Insights Gen2-Erfassung finden Sie hier:

> [!div class="nextstepaction"]
> [Übersicht über die Azure Time Series Insights Gen2-Datenerfassung](./concepts-ingestion-overview.md)

Weitere Informationen zum Azure Time Series Insights Gen2-Speicher finden Sie hier:

> [!div class="nextstepaction"]
> [Datenspeicherung](./concepts-storage.md)

Erfahren Sie mehr über Zeitreihenmodelle:

> [!div class="nextstepaction"]
> [Zeitreihenmodell in Azure Time Series Insights Gen2](./concepts-model-overview.md)

Weitere Informationen zum Verbinden Ihrer Umgebung mit Power BI:

> [!div class="nextstepaction"]
> [Visualisieren der Daten von Time Series Insights Gen2 in Power BI](./how-to-connect-power-bi.md)