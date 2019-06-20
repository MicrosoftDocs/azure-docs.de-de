---
title: Bereitstellen von Modulen im Azure-Portal – Azure IoT Edge | Microsoft-Dokumentation
description: Bereitstellen von Modulen auf einem IoT Edge-Gerät über das Azure-Portal
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/19/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 9d7729dce5419c5813de3c4dfce55c40098f5988
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60595239"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Bereitstellen von Azure IoT Edge-Modulen über das Azure-Portal

Nachdem Sie IoT Edge-Module mit Ihrer Geschäftslogik erstellen, sollten Sie sie auf Ihren Geräten für den Betrieb im Edge-Bereich bereitstellen. Wenn bei Ihnen mehrere Module gemeinsam Daten erfassen und verarbeiten, können Sie alle auf einmal bereitstellen und die Routingregeln, mit denen sie verbunden werden, deklarieren.

In diesem Artikel wird gezeigt, wie das Azure-Portal Sie durch das Erstellen eines Bereitstellungsmanifests und die Übertragung der Bereitstellung auf einem IoT Edge-Gerät führt. Informationen zum Erstellen einer Bereitstellung, die basierend auf freigegebenen Tags auf mehrere Geräte ausgerichtet ist, finden Sie unter [Bereitstellen und Überwachen von IoT Edge-Modulen im großen Maßstab](how-to-deploy-monitor.md).

## <a name="prerequisites"></a>Voraussetzungen

* Ein [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) in Ihrem Azure-Abonnement.
* Ein [IoT Edge-Gerät](how-to-register-device-portal.md) mit installierter IoT Edge-Runtime.

## <a name="select-your-device"></a>Auswählen Ihres Geräts

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu Ihrem IoT Hub.
1. Wählen Sie im Menü **IoT Edge** aus.
1. Klicken Sie in der Liste der Geräte auf die ID des Zielgeräts.
1. Wählen Sie **Module festlegen** aus.

## <a name="configure-a-deployment-manifest"></a>Konfigurieren eines Bereitstellungsmanifests

Ein Bereitstellungsmanifest ist ein JSON-Dokument, das beschreibt, welche Module bereitgestellt werden sollen, wie Daten zwischen den Modulen übermittelt werden und welche Eigenschaften die Modulzwillinge aufweisen sollen. Weitere Informationen zur Funktionsweise und Erstellung von Bereitstellungsmanifesten finden Sie unter [Verstehen, wie IoT Edge-Module verwendet, konfiguriert und wiederverwendet werden können](module-composition.md).

Das Azure-Portal verfügt über einen Assistenten, der Sie durch das Erstellen des Bereitstellungsmanifests führt, sodass Sie das JSON-Dokument nicht manuell erstellen müssen. Der Assistent umfasst drei Schritte: **Hinzufügen von Modulen**, **Angeben von Routen** und **Überprüfen der Bereitstellung**.

### <a name="add-modules"></a>Hinzufügen von Modulen

1. Geben Sie im Abschnitt **Registrierungseinstellungen** der Seite die Anmeldeinformationen für den Zugriff auf private Containerregistrierungen mit Ihren Modulimages an.

1. Wählen Sie im Abschnitt **Bereitstellungsmodule** der Seite die Option **Hinzufügen** aus.

1. Sehen Sie sich die Modultypen in der Dropdownliste an:

   * **IoT Edge-Modul:** Standardoption.
   * **Azure Stream Analytics-Modul:** nur Module, die aus einer Azure Stream Analytics-Workload generiert wurden.
   * **Azure Machine Learning-Modul:** nur Modellimages, die aus einem Azure Machine Learning-Arbeitsbereich generiert wurden.

1. Wählen Sie das **IoT Edge-Modul** aus.

1. Geben Sie einen Namen für das Modul und dann das Containerimage an. Beispiel:

   * **Name:** tempSensor
   * **Bild-URI:** mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0

1. Füllen Sie bei Bedarf die optionalen Felder aus. Weitere Informationen zu Containererstellungsoptionen, Neustartrichtlinien und gewünschtem Status finden Sie unter [Gewünschte EdgeAgent-Eigenschaften](module-edgeagent-edgehub.md#edgeagent-desired-properties). Weitere Informationen zum Modulzwilling finden Sie unter [Definieren oder Aktualisieren gewünschter Eigenschaften](module-composition.md#define-or-update-desired-properties).

1. Wählen Sie **Speichern** aus.

1. Wiederholen Sie die Schritte 2 bis 6, um Ihrer Bereitstellung weitere Module hinzuzufügen.

1. Klicken Sie auf **Weiter**, um mit dem Abschnitt über Routen fortzufahren.

### <a name="specify-routes"></a>Angeben von Routen

Standardmäßig erhalten Sie vom Assistenten eine Route mit dem Namen **route**, die als **FROM /\* INTO $upstream** definiert ist. Dies bedeutet, dass alle Nachrichten, die von Modulen ausgegeben werden, an Ihren IoT Hub gesendet werden.  

Fügen Sie die Routen mit Informationen aus dem Abschnitt [Deklarieren von Routen](module-composition.md#declare-routes) hinzu, oder aktualisieren Sie diese, und wählen Sie dann **Weiter** aus, um mit dem Abschnitt zur Überprüfung fortzufahren.

### <a name="review-deployment"></a>Überprüfen der Bereitstellung

Im Abschnitt zur Überprüfung wird das JSON-Bereitstellungsmanifest angezeigt, das anhand Ihrer Auswahl in den vorherigen beiden Abschnitten erstellt wurde. Beachten Sie, dass zwei Module deklariert wurden, die Sie nicht hinzugefügt haben: **$edgeAgent** und **$edgeHub**. Diese beiden Module bilden die [IoT Edge-Runtime](iot-edge-runtime.md) und sind in jeder Bereitstellung erforderliche Standardvorgaben.

Überprüfen Sie die Bereitstellungsinformationen, und klicken Sie dann auf **Senden**.

## <a name="view-modules-on-your-device"></a>Anzeigen von Modulen auf dem Gerät

Nachdem Sie die Module auf Ihrem Gerät bereitgestellt haben, können Sie sie auf der Seite **Gerätedetails** des Portals anzeigen. Auf dieser Seite werden die Namen der einzelnen bereitgestellten Modul sowie nützliche Informationen wie der Bereitstellungsstatus und der Exitcode angezeigt.

## <a name="deploy-modules-from-azure-marketplace"></a>Bereitstellen von Modulen aus Azure Marketplace

Azure Marketplace ist ein Onlinemarktplatz für Anwendungen und Dienste, in dem sie eine breite Palette an Unternehmensanwendungen und -lösungen durchsuchen können, die für die Ausführung unter Azure, einschließlich [IoT Edge-Modulen](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules), zertifiziert und optimiert sind. Sie können auch über das Azure-Portal unter **Ressource erstellen** auf den Azure Marketplace zugreifen.

Sie können ein IoT Edge-Modul entweder über Azure Marketplace oder das Azure-Portal folgendermaßen installieren:

1. Suchen Sie ein Modul, und beginnen Sie den Bereitstellungsprozess.

   * Azure-Portal: Suchen Sie ein Modul, und wählen Sie **Erstellen** aus.

   * Azure Marketplace:

     1. Suchen Sie ein Modul, und wählen Sie **Jetzt herunterladen** aus.
     1. Bestätigen Sie die Nutzungsbedingungen und Datenschutzrichtlinie des Anbieters durch Auswählen von **Weiter**.

1. Wählen Sie Ihr Abonnement und den IoT-Hub aus, der mit dem Zielgerät verbunden ist.

1. Wählen Sie **Für Gerät bereitstellen** aus.

1. Geben Sie den Namen des Geräts ein, oder wählen Sie **Gerät suchen** aus, um die mit dem Hub registrierten Geräte zu durchsuchen.

1. Wählen Sie **Erstellen** aus, um den Standardprozess des Konfigurieren eines Bereitstellungsmanifests fortzusetzen. Dazu gehört auch das Hinzufügen weiterer Module bei Bedarf. Details für das neue Modul, z.B. Image-URI, Erstellungsoptionen und gewünschte Eigenschaften, sind vordefiniert, können aber geändert werden.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie [IoT Edge-Module im großen Maßstab bereitstellen und überwachen](how-to-deploy-monitor.md).
