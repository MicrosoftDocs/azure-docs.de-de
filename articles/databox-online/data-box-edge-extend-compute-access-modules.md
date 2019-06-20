---
title: Verwalten des Computingnetzwerks in Azure Data Box Edge für den Zugriff auf Module | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie das Computingnetzwerk in Data Box Edge erweitern, um über eine externe IP-Adresse auf Module zuzugreifen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/17/2019
ms.author: alkohli
ms.openlocfilehash: 907647725dd6795b3b6482476de7442fbbf66114
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65917114"
---
# <a name="enable-compute-network-on-your-azure-data-box-edge"></a>Aktivieren des Computingnetzwerks auf Ihrem Azure Data Box Edge-Gerät

In diesem Artikel wird beschrieben, wie die in Azure Data Box Edge ausgeführten Module auf das auf dem Gerät aktivierte Computingnetzwerk zugreifen können.

Zum Konfigurieren des Netzwerks müssen Sie die folgenden Schritte vornehmen:

- Aktivieren einer Netzwerkschnittstelle auf Ihrem Data Box Edge-Gerät für das Computing
- Hinzufügen eines Moduls für den Zugriff auf das Computingnetzwerk auf Ihrem Data Box Edge-Gerät
- Überprüfen des Zugriffs auf die aktivierte Netzwerkschnittstelle durch das Modul

In diesem Tutorial verwenden Sie ein Webserver-App-Modul zur Veranschaulichung des Szenarios.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie beginnen, müssen die folgenden Voraussetzungen erfüllt sein:

- Sie besitzen ein Data Box Edge-Gerät mit abgeschlossenem Gerätesetup.
- Sie haben den Schritt **Konfigurieren der Computeumgebung** unter [Tutorial: Transformieren von Daten mit Azure Data Box Edge für erweiterten Bereitstellungsablauf](data-box-edge-deploy-configure-compute-advanced.md#configure-compute) auf Ihrem Gerät abgeschlossen. Ihrem Gerät sollte eine IoT Hub-Ressource, ein IoT-Gerät und ein IoT Edge-Gerät zugeordnet sein.

## <a name="enable-network-interface-for-compute"></a>Aktivieren einer Netzwerkschnittstelle für Computing

Sie müssen auf Ihrem Gerät eine IP-Adresse einer Netzwerkschnittstelle zuweisen, um über ein externes Netzwerk auf die Module zuzugreifen, die auf Ihrem Gerät ausgeführt werden. Sie können diese Computeeinstellungen über Ihre lokale Webbenutzeroberfläche verwalten.

Führen Sie zum Konfigurieren von Computeeinstellungen die folgenden Schritte auf der lokalen Webbenutzeroberfläche aus.

1. Navigieren Sie auf der lokalen Webbenutzeroberfläche zu **Konfiguration > Computeeinstellungen**.  

2. **Aktivieren** Sie die Netzwerkschnittstelle, über die Sie eine Verbindung mit einem Computemodul herstellen möchten, das Sie auf Ihrem Gerät ausführen.

    - Geben Sie bei Verwendung von statischen IP-Adressen eine IP-Adresse für die Netzwerkschnittstelle ein.
    - Bei Verwendung von DHCP werden die IP-Adressen automatisch zugewiesen. In diesem Beispiel wird DHCP verwendet.

    ![Aktivieren von Computeeinstellungen 1](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-1.png)

3. Wählen Sie zum Anwenden der Einstellungen die Option **Übernehmen** aus. Notieren Sie sich die IP-Adresse, die der Netzwerkschnittstelle zugewiesen ist, wenn Sie DHCP verwenden.

    ![Aktivieren von Computeeinstellungen](media/data-box-edge-extend-compute-access-modules/enable-compute-setting-2.png)

## <a name="add-webserver-app-module"></a>Hinzufügen eines Webserver-App-Moduls

Führen Sie die folgenden Schritte aus, um ein Webserver-App-Modul zu Ihrem Data Box Edge-Gerät hinzuzufügen.

1. Navigieren Sie zur IoT Hub-Ressource, die Ihrem Data Box Edge-Gerät zugeordnet ist, und klicken Sie dann auf **IoT Edge-Gerät**.
2. Wählen Sie das IoT Edge-Gerät aus, das Ihrem Data Box Edge-Gerät zugeordnet ist. Klicken Sie in den **Gerätedetails** auf **Module festlegen**. Klicken Sie unter **Module hinzufügen** auf **+ Hinzufügen**, und wählen Sie dann **IoT Edge-Modul** aus.
3. Gehen Sie auf dem Blatt **Benutzerdefinierte IoT Edge-Module** wie folgt vor:

    1. Legen Sie einen **Namen** für Ihr Webserver-App-Modul fest, das Sie bereitstellen möchten.
    2. Stellen Sie einen **Image-URI** für Ihr Modulimage bereit. Ein Modul, das dem angegebenen Namen und den Tags entspricht, wird abgerufen. In diesem Fall ruft `nginx:stable` ein stabiles nginx-Image (als stabil gekennzeichnet) per Pull aus dem öffentlichen [Docker-Repository](https://hub.docker.com/_/nginx/) ab.
    3. Fügen Sie den folgenden Beispielcode in die **Optionen für Containererstellung** ein:  

        ```
        {
            "HostConfig": {
                "PortBindings": {
                    "80/tcp": [
                        {
                            "HostPort": "8080"
                        }
                    ]
                }
            }
        }
        ```

        Mit dieser Konfiguration können Sie mithilfe der IP-Adresse des Computingnetzwerks per *HTTP* über den TCP-Port 8080 auf das Modul zugreifen (der Webserver-Standardport ist Port 80).

        ![Festlegen der Portinformationen auf dem Blatt „Benutzerdefiniertes IoT Edge-Modul“](media/data-box-edge-extend-compute-access-modules/module-information.png)

    4. Wählen Sie **Speichern** aus.

## <a name="verify-module-access"></a>Überprüfen des Modulzugriffs

1. Überprüfen Sie, ob das Modul erfolgreich bereitgestellt wurde und ausgeführt wird. Der Laufzeitstatus des Moduls auf der Registerkarte **Module** der Seite **Gerätedetails** sollte **Wird ausgeführt** lauten.  
2. Stellen Sie eine Verbindung mit dem Webserver-App-Modul her. Öffnen Sie ein Browserfenster, und geben Sie Folgendes ein:

    `http://<compute-network-IP-address>:8080`

    Es sollte angezeigt werden, dass die Webserver-App ausgeführt wird.

    ![Überprüfen der Verbindung mit dem Modul über den festgelegten Port](media/data-box-edge-extend-compute-access-modules/verify-connect-module-1.png)

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie [Benutzer über das Azure-Portal verwalten](data-box-edge-manage-users.md).
