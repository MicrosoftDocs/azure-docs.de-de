---
title: Tutorial zum Konfigurieren von Zertifikaten für ein Azure Stack Edge Pro-Gerät mit GPU im Azure-Portal | Microsoft-Dokumentation
description: Im Tutorial zur Bereitstellung eines Azure Stack Edge Pro-Geräts mit GPU erfahren Sie, wie Sie Zertifikate auf Ihrem physischen Gerät konfigurieren.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure certificates for Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: d8d21f9eea0258175195529c4d8b72ee9085dc77
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100546974"
---
# <a name="tutorial-configure-certificates-for-your-azure-stack-edge-pro-with-gpu"></a>Tutorial: Konfigurieren von Zertifikaten für Ihr Azure Stack Edge Pro-Gerät mit GPU

In diesem Tutorial wird erläutert, wie Sie Zertifikate für Ihr Azure Stack Edge Pro-Gerät mit eingebauter GPU auf der lokalen Webbenutzeroberfläche konfigurieren.

Die für diesen Schritt benötigte Zeit kann je nach der von Ihnen gewählten Option und der Einrichtung des Zertifikatflows in Ihrer Umgebung variieren.

In diesem Tutorial lernen Sie Folgendes kennen:

> [!div class="checklist"]
>
> * Voraussetzungen
> * Konfigurieren von Zertifikaten für ein physisches Gerät

## <a name="prerequisites"></a>Voraussetzungen

Überprüfen Sie Folgendes, bevor Sie ein Azure Stack Edge Pro-Gerät mit GPU konfigurieren und einrichten:

* Sie haben Ihr physisches Gerät gemäß der Anleitung unter [Installieren von Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-install.md) installiert.
* Wenn Sie beabsichtigen, eigene Zertifikate zu nutzen:
    - Sie müssen Ihre Zertifikate im entsprechenden Format einschließlich des Signaturkettenzertifikats bereithalten. Einzelheiten zu Zertifikaten finden Sie unter [Verwalten von Zertifikaten](azure-stack-edge-gpu-manage-certificates.md).

<!--    - If your device is deployed in Azure Government or Azure Government Secret or Azure Government top secret cloud and not deployed in Azure public cloud, a signing chain certificate is required before you can activate your device. 
    For details on certificate, go to [Manage certificates](azure-stack-edge-gpu-manage-certificates.md).-->


## <a name="configure-certificates-for-device"></a>Konfigurieren von Zertifikaten für ein Gerät

1. Zertifikate werden auf der Seite **Zertifikate** konfiguriert. Abhängig davon, ob Sie den Gerätenamen oder die DNS-Domäne auf der Seite **Gerät** geändert haben, können Sie für Ihre Zertifikate eine der folgenden Optionen auswählen.

    - Wenn Sie den Gerätenamen oder die DNS-Domäne im vorherigen Schritt nicht geändert haben und keine eigenen Zertifikate bereitstellen möchten, können Sie diesen Schritt überspringen und mit dem nächsten Schritt fortfahren. Fürs Erste hat das Gerät automatisch selbstsignierte Zertifikate generiert. 

        ![Seite „Zertifikate“ auf der lokalen Webbenutzeroberfläche](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-2.png)

    - Wenn Sie den Gerätenamen oder die DNS-Domäne geändert haben, wird als Status der Zertifikate **Nicht gültig** angezeigt. 

        ![Seite „Zertifikate“ auf der lokalen Webbenutzeroberfläche 2](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-1.png)    

        Wählen Sie ein Zertifikat aus, um die Statusdetails anzuzeigen.

        ![Seite „Zertifikate“ auf der lokalen Webbenutzeroberfläche 3](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-1a.png)  

        Der Grund dafür ist, dass die Zertifikate nicht den aktualisierten Gerätenamen oder die aktualisierte DNS-Domäne aufweisen (diese werden für „Antragstellername“ und „Alternativer Antragstellername“ verwendet). Wählen Sie zur erfolgreichen Aktivierung Ihres Geräts eine der folgenden Optionen aus: 
    
        - **Generieren aller Gerätezertifikate**. Diese Gerätezertifikate sollten nur zu Testzwecken und nicht mit Produktionsworkloads verwendet werden. Weitere Informationen finden Sie unter [Generieren von Gerätezertifikaten auf einem Azure Stack Edge Pro-Gerät](#generate-device-certificates).

        - **Bereitstellen eines eigenen Zertifikats**. Sie können Ihre eigenen signierten Endpunktzertifikate und die entsprechenden Signaturketten nutzen. Sie fügen zunächst die Signaturkette hinzu und laden dann die Endpunktzertifikate hoch. **Wir empfehlen, dass Sie für Produktionsworkloads immer Ihre eigenen Zertifikate bereitstellen.** Weitere Informationen finden Sie unter [Bereitstellen eigener Zertifikate auf einem Azure Stack Edge Pro-Gerät](#bring-your-own-certificates).
    
        - Sie können einige Ihrer eigenen Zertifikate bereitstellen und einige Gerätezertifikate generieren. Über die Option **Zertifikate generieren** werden nur die Gerätezertifikate neu generiert.

    - Wenn Sie den Gerätenamen oder die DNS-Domäne geändert haben und keine Zertifikate generieren oder Ihre eigenen Zertifikate bereitstellen, wird die **Aktivierung blockiert**.


### <a name="generate-device-certificates"></a>Generieren von Gerätezertifikaten

Befolgen Sie diese Schritte, um Gerätezertifikate zu generieren.

Führen Sie diese Schritte aus, um die Azure Stack Edge Pro-Gerätezertifikate neu zu generieren und herunterzuladen:

1. Navigieren Sie auf der lokalen Benutzeroberfläche Ihres Geräts zu **Konfiguration > Zertifikate**. Wählen Sie **Zertifikate generieren** aus.

    ![Zertifikat generieren und herunterladen 1](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-3.png)

2. Wählen Sie im Abschnitt **Gerätezertifikate generieren** die Option **Generieren** aus. 

    ![Zertifikat generieren und herunterladen 2](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-4.png)

    Die Gerätezertifikate werden nun generiert und zugewiesen. Der Vorgang zum Generieren und Anwenden der Zertifikate dauert einige Minuten.
    
    > [!IMPORTANT]
    > Während der Vorgang zum Generieren von Zertifikaten läuft, dürfen Sie keine eigenen Zertifikate bereitstellen. Versuchen Sie, diese über die Option **+ Zertifikat hinzufügen** hinzuzufügen.

    Sie werden benachrichtigt, sobald der Vorgang erfolgreich abgeschlossen wurde. **Starten Sie Ihren Browser neu, um potenzielle Cacheprobleme zu vermeiden.**
    
    ![Zertifikat generieren und herunterladen 4](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-5.png)

3. Nach der Zertifikatgenerierung: 

    - Der Status aller Zertifikate lautet **Gültig**. 

        ![Zertifikat generieren und herunterladen 5](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-6.png)

    - Sie können einen bestimmten Zertifikatnamen auswählen und die Zertifikatdetails anzeigen. 

        ![Zertifikat generieren und herunterladen 6](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-6a.png)

    - Die Spalte **Download** ist jetzt gefüllt. Diese Spalte enthält Links zum Herunterladen der neu generierten Zertifikate. 

        ![Zertifikat generieren und herunterladen 7](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-6b.png)


4. Wählen Sie den Downloadlink für ein Zertifikat aus, und speichern Sie das Zertifikat bei Aufforderung. 

    ![Zertifikat generieren und herunterladen 8](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-7.png)

5. Wiederholen Sie diesen Vorgang für alle Zertifikate, die Sie herunterladen möchten. 
    
    ![Zertifikat generieren und herunterladen 9](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-8.png)

    Die vom Gerät generierten Zertifikate werden als DER-Zertifikate im folgendem Namensformat gespeichert: 

    `<Device name>_<Endpoint name>.cer`. Diese Zertifikate enthalten den öffentlichen Schlüssel für die entsprechenden auf dem Gerät installierten Zertifikate. 

Sie müssen diese Zertifikate auf dem Clientsystem installieren, das Sie für den Zugriff auf die Endpunkte auf dem Azure Stack Edge-Gerät verwenden. Diese Zertifikate schaffen ein Vertrauensverhältnis zwischen Client und Gerät.

Zum Importieren und Installieren dieser Zertifikate auf dem Client, den Sie für den Zugriff auf das Gerät verwenden, folgen Sie den Schritten unter [Importieren von Zertifikaten auf Clients, die auf Ihr Azure Stack Edge Pro-Gerät zugreifen](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device). 

Wenn Sie Azure Storage-Explorer verwenden, müssen Sie Zertifikate im PEM-Format auf Ihrem Client installieren und die vom Gerät generierten Zertifikate in das PEM-Format konvertieren. 

> [!IMPORTANT]
> - Der Downloadlink ist nur für die vom Gerät generierten Zertifikate verfügbar und nicht, wenn Sie Ihre eigenen Zertifikate bereitstellen.
> - Sie können sich für eine Kombination aus vom Gerät generierten Zertifikaten entscheiden und Ihre eigenen Zertifikate bereitstellen, solange andere Zertifikatsanforderungen erfüllt sind. Weitere Informationen finden Sie unter [Zertifikatanforderungen](azure-stack-edge-gpu-certificate-requirements.md).
    

### <a name="bring-your-own-certificates"></a>Bereitstellen eigener Zertifikate

Befolgen Sie diese Schritte, um Ihre eigenen Zertifikate einschließlich Signaturkette hinzuzufügen.

1. Um ein Zertifikat hochzuladen, wählen Sie auf der Seite **Zertifikat** die Option **+ Zertifikat hinzufügen** aus.

    ![Seite „Zertifikate“ auf der lokalen Webbenutzeroberfläche 4](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-1.png)

2. Laden Sie zuerst die Signaturkette hoch, und wählen Sie **Überprüfen und hinzufügen** aus.

    ![Seite „Zertifikate“ auf der lokalen Webbenutzeroberfläche 5](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-2.png)

3. Jetzt können Sie andere Zertifikate hochladen. Sie können z. B. die Zertifikate für Azure Resource Manager- und Blob Storage-Endpunkte hochladen.

    ![Seite „Zertifikate“ auf der lokalen Webbenutzeroberfläche 6](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-3.png)

    Sie können auch das Zertifikat der lokalen Webbenutzeroberfläche hochladen. Nachdem Sie dieses Zertifikat hochgeladen haben, müssen Sie den Browser starten und den Cache leeren. Sie müssen dann eine Verbindung mit der lokalen Webbenutzeroberfläche des Geräts herstellen.  

    ![Seite „Zertifikate“ auf der lokalen Webbenutzeroberfläche 7](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-5.png)

    Sie können auch das Knotenzertifikat hochladen.

    ![Seite „Zertifikate“ auf der lokalen Webbenutzeroberfläche 8](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-4.png)

    Sie können jederzeit ein Zertifikat auswählen und die Details anzeigen, um sicherzustellen, dass die Informationen mit Ihrem hochgeladenen Zertifikat übereinstimmen.

    ![Seite „Zertifikate“ auf der lokalen Webbenutzeroberfläche 9](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-6.png)

    Die Seite „Zertifikate“ muss aktualisiert werden, um die neu hinzugefügten Zertifikate widerzuspiegeln.

    ![Seite „Zertifikate“ auf der lokalen Webbenutzeroberfläche 10](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-7.png)  

    > [!NOTE]
    > Mit Ausnahme der öffentlichen Azure-Cloud sind für alle Cloudkonfigurationen (Azure Government oder Azure Stack) Signaturkettenzertifikate erforderlich, die vor der Aktivierung bereitgestellt werden müssen.


Ihr Gerät kann jetzt aktiviert werden. Wählen Sie **< Zurück zu „Erste Schritte“** aus.


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial lernen Sie Folgendes kennen:

> [!div class="checklist"]
>
> * Voraussetzungen
> * Konfigurieren von Zertifikaten für ein physisches Gerät

Informationen zum Aktivieren Ihres Azure Stack Edge Pro-Geräts finden Sie unter:

> [!div class="nextstepaction"]
> [Aktivieren eines Azure Stack Edge Pro-Geräts](./azure-stack-edge-gpu-deploy-activate.md)
