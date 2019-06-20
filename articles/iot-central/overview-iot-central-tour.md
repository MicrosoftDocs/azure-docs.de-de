---
title: Kennenlernen der Benutzeroberfläche von Azure IoT Central | Microsoft-Dokumentation
description: Machen Sie sich als Ersteller mit den wichtigsten Bereichen der Benutzeroberfläche von Azure IoT Central vertraut, die Sie zur Erstellung von IoT-Lösungen verwenden.
author: dominicbetts
ms.author: dobett
ms.date: 05/31/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 53306b2087a47f5a61fc2a228e2d1f527127f746
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66476321"
---
# <a name="take-a-tour-of-the-azure-iot-central-ui"></a>Kennenlernen der Benutzeroberfläche von Azure IoT Central

In diesem Artikel wird die Benutzeroberfläche von Microsoft Azure IoT Central vorgestellt. Über die Benutzeroberfläche können Sie Azure IoT Central-Lösungen und die damit verbundenen Geräte erstellen, verwalten und verwenden.

_Ersteller_ verwenden die Benutzeroberfläche von Azure IoT Central, um ihre Azure IoT Central-Lösung zu definieren. Die Benutzeroberfläche bietet folgende Möglichkeiten:

- Definieren der Art von Gerät, das eine Verbindung mit Ihrer Lösung herstellt
- Konfigurieren der Regeln und Aktionen für Ihre Geräte
- Anpassen der Benutzeroberfläche für einen _Bediener_, der Ihre Lösung verwendet

_Bediener_ verwenden die Benutzeroberfläche von Azure IoT Central, um ihre Azure IoT Central-Lösung zu verwalten. Die Benutzeroberfläche bietet folgende Möglichkeiten:

- Überwachen Ihrer Geräte
- Konfigurieren Ihrer Geräte
- Behandeln und Beheben von Problemen mit Ihren Geräten
- Bereitstellen von neuen Geräten

## <a name="use-the-left-navigation-menu"></a>Verwenden des linken Navigationsmenüs

Über das linke Navigationsmenü können Sie auf die verschiedenen Bereiche der Anwendung zugreifen. Wählen Sie **<** oder **>** aus, um die Navigationsleiste zu erweitern oder zu reduzieren:

| Menü | BESCHREIBUNG |
| ---- | ----------- |
| ![Linkes Navigationsmenü](media/overview-iot-central-tour/navigationbar.png) | <ul><li>Die **Dashboard**-Schaltfläche zeigt Ihr Anwendungsdashboard an. Als Ersteller können Sie das Dashboard für Ihre Bediener anpassen. Benutzer können auch ihre eigenen Dashboards erstellen.</li><li>Die Schaltfläche **Device Explorer** dient zum Auflisten der simulierten und echten Geräte, die den einzelnen Gerätevorlagen in der Anwendung zugeordnet sind. Bediener verwenden den **Device Explorer**, um ihre verbundenen Geräte zu verwalten.</li><li>Die Schaltfläche **Device Sets** (Gerätegruppen) dient zum Anzeigen und Erstellen von Gerätegruppen. Bediener können Gerätegruppen als logische, durch eine Abfrage angegebene Sammlung von Geräten erstellen.</li><li>Die Schaltfläche **Analytics** dient zum Anzeigen von Analysen, die auf Gerätetelemetriedaten für Geräte und Gerätegruppen basieren. Bediener können benutzerdefinierte Ansichten auf der Grundlage von Gerätedaten erstellen, um basierend auf ihrer Anwendung Erkenntnisse zu gewinnen.</li><li>Die Schaltfläche **Aufträge** ermöglicht eine Geräteverwaltung per Massenvorgang, da Sie Aufträge für Updates in großem Umfang erstellen und ausführen können.</li><li>Über die Schaltfläche **Gerätevorlagen** werden die Tools angezeigt, die ein Ersteller zum Erstellen und Verwalten von Gerätevorlagen verwendet.</li><li>Über die Schaltfläche **Kontinuierlicher Datenexport** kann ein Administrator einen kontinuierlichen Export an andere Azure-Dienste (etwa Speicher und Warteschlangen) konfigurieren.</li><li>Die Schaltfläche **Verwaltung** dient zum Anzeigen der Seiten für die Anwendungsverwaltung, auf denen ein Administrator Anwendungseinstellungen, Benutzer und Rollen verwalten kann.</li></ul> |

## <a name="search-help-and-support"></a>Suche, Hilfe und Support

Das obere Menü wird auf jeder Seite angezeigt:

![Symbolleiste](media/overview-iot-central-tour/toolbar.png)

- Wenn Sie nach Gerätevorlagen und Geräten suchen möchten, geben Sie einen Wert für die **Suche** ein.
- Wählen Sie zum Ändern der Sprache der Benutzeroberfläche oder des Designs das Symbol **Einstellungen** aus.
- Wählen Sie das Symbol **Konto** aus, um sich von der Anwendung abzumelden.
- Wenn Sie Hilfe oder Unterstützung benötigen, klicken Sie auf das Dropdownmenü **Hilfe**, um eine Liste mit Ressourcen anzuzeigen. In einer Testanwendung beinhalten die Supportressourcen Zugriff auf den [Livechat](howto-show-hide-chat.md).

Sie können zwischen einem hellen und einem dunklen Benutzeroberflächendesign wählen:

![Auswählen eines Designs für die Benutzeroberfläche](media/overview-iot-central-tour/themes.png)

> [!NOTE]
> Die Option zum Wählen zwischen hellen und dunklen Designs ist nicht verfügbar, wenn Ihr Administrator ein benutzerdefiniertes Design für die Anwendung konfiguriert hat.

## <a name="dashboard"></a>Dashboard

![Dashboard](media/overview-iot-central-tour/homepage.png)

* Nach der Anmeldung bei Ihrer Azure IoT Central-Anwendung wird als Erstes das Dashboard angezeigt. Als Ersteller können Sie das Anwendungsdashboard für andere Benutzer anpassen, indem Sie Kacheln hinzufügen. Weitere Informationen finden Sie im Tutorial [Customize the Azure IoT Central operator's view](tutorial-customize-operator.md) (Anpassen der Azure IoT Central-Ansicht für Bediener).

* Als Bediener können Sie personalisierte Dashboards erstellen und zwischen diesen und dem Standarddashboard wechseln. Weitere Informationen finden Sie in der Anleitung [Erstellen und Verwalten persönlicher Dashboards](howto-personalize-dashboard.md).

## <a name="device-explorer"></a>Device Explorer

![Explorer-Seite](media/overview-iot-central-tour/explorer.png)

Auf der Explorer-Seite werden die _Geräte_ in Ihrer Azure IoT Central-Anwendung angezeigt (gruppiert nach _Gerätevorlage_).

* Eine Gerätevorlage definiert einen Gerätetyp, der eine Verbindung mit Ihrer Anwendung herstellen kann. Weitere Informationen finden Sie unter [Define a new device type in your Azure IoT Central application](tutorial-define-device-type.md) (Definieren eines neuen Gerätetyps in Ihrer Azure IoT Central-Anwendung).
* Bei einem Gerät handelt es sich um ein echtes oder simuliertes Gerät in Ihrer Anwendung. Weitere Informationen finden Sie unter [Add a real device to your Azure IoT Central application](tutorial-add-device.md) (Hinzufügen eines echten Geräts zu Ihrer Azure IoT Central-Anwendung).

## <a name="device-sets"></a>Gerätegruppen

![Seite „Device Sets“ (Gerätegruppen)](media/overview-iot-central-tour/devicesets.png)

Auf der Seite _Device Sets_ (Gerätegruppen) werden Gerätegruppen angezeigt, die der Ersteller erstellt hat. Bei einer Gerätegruppe handelt es sich um eine Sammlung verwandter Geräte. Ein Ersteller definiert eine Abfrage, um die in einer Gerätegruppe enthaltenen Geräte zu identifizieren. Gerätegruppen werden verwendet, wenn Sie die Analyse in Ihrer Anwendung anpassen. Weitere Informationen finden Sie im Artikel [Use device sets in your Azure IoT Central application](howto-use-device-sets.md) (Definieren von Gerätegruppen in Ihrer Azure IoT Central-Anwendung).

## <a name="analytics"></a>Analytics

![Seite „Analytics“](media/overview-iot-central-tour/analytics.png)

Anhand der Diagramme auf der Analyseseite können Sie das Verhalten der mit Ihrer Anwendung verbundenen Geräte nachvollziehen. Bediener verwenden diese Seite zur Überwachung und Untersuchung von Problemen mit verbundenen Geräten. Der Ersteller kann definieren, welche Diagramme auf dieser Seite angezeigt werden. Weitere Informationen finden Sie im Artikel [How to use analytics to analyze your device data](howto-use-device-sets.md) (Analysieren Ihrer Anwendungsdaten mithilfe von Analysen).

## <a name="jobs"></a>Aufträge

![Seite „Aufträge“](media/overview-iot-central-tour/jobs.png)

Auf der Seite „Aufträge“ können Sie Massenvorgänge für die Geräteverwaltung auf Ihren Geräten ausführen. Ersteller verwenden diese Seite, um Eigenschaften, Einstellungen und Befehle für Geräte zu aktualisieren. Weitere Informationen finden Sie im Artikel [Ausführen eines Auftrags](howto-run-a-job.md).

## <a name="device-templates"></a>Gerätevorlagen

![Seite „Gerätevorlagen“](media/overview-iot-central-tour/templates.png)

Auf der Seite „Gerätevorlagen“ können Ersteller die Gerätevorlagen in der Anwendung erstellen und verwalten. In einer Gerätevorlage werden spezielle Geräteeigenschaften angegeben, z.B. die folgenden:

- Telemetriedaten, Status und Ereignismessungen.
- Einstellungen und Eigenschaften.
- Befehle.
- Regeln auf Grundlage von Ereignissen oder Telemetriewerten.

Weitere Informationen finden Sie im Tutorial [Define a new device type in your Azure IoT Central application](tutorial-define-device-type.md) (Definieren eines neuen Gerätetyps in Ihrer Azure IoT Central-Anwendung).

## <a name="continuous-data-export"></a>Kontinuierlicher Datenexport

![Seite „Kontinuierlicher Datenexport“](media/overview-iot-central-tour/export.png)

Auf der Seite „Kontinuierlicher Datenexport“ können Administratoren definieren, wie Daten (beispielsweise Telemetriedaten) aus der Anwendung gestreamt werden sollen. Die exportierten Daten können von anderen Diensten gespeichert oder zu Analysezwecken genutzt werden. Weitere Informationen finden Sie im Artikel [Exportieren von Daten in Azure IoT Central](howto-export-data.md).

## <a name="administration"></a>Verwaltung

![Verwaltungsseite](media/overview-iot-central-tour/administration.png)

Die Verwaltungsseite enthält Links zu den Tools, die ein Administrator verwendet, um beispielsweise Benutzer und Rollen in der Anwendung zu definieren oder die Benutzeroberfläche anzupassen. Weitere Informationen finden Sie im Artikel [How to administer your application](howto-administer.md) (Verwalten Ihrer Anwendung).

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich nun einen Überblick über Azure IoT Central verschafft und sich mit dem Layout der Benutzeroberfläche vertraut gemacht haben, empfehlen wir, mit der Schnellstartanleitung zum [Erstellen einer Azure IoT Central-Anwendung](quick-deploy-iot-central.md) fortzufahren.