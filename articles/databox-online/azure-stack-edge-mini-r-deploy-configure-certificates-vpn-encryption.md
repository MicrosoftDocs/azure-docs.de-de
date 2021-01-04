---
title: Tutorial zum Konfigurieren von Zertifikaten für ein Azure Stack Edge Mini R-Gerät im Azure-Portal | Microsoft-Dokumentation
description: In diesem Tutorial zur Bereitstellung von Azure Stack Edge Mini R erfahren Sie, wie Sie Zertifikate für Ihr physisches Gerät konfigurieren.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/21/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure certificates for Azure Stack Edge Mini R  so I can use it to transfer data to Azure.
ms.openlocfilehash: 1e81ab61b04f9cca6aff57de1736eac25a871c97
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96464286"
---
# <a name="tutorial-configure-certificates-vpn-encryption-for-your-azure-stack-edge-mini-r"></a>Tutorial: Konfigurieren von Zertifikaten, VPN und Verschlüsselung für Ihr Azure Stack Edge Mini R-Gerät

In diesem Tutorial erfahren Sie, wie Sie Zertifikate, VPN und die Verschlüsselung ruhender Daten für Ihr Azure Stack Edge Mini R-Gerät über die lokale Webbenutzeroberfläche konfigurieren.

Die für diesen Schritt benötigte Zeit kann je nach der von Ihnen gewählten Option und der Einrichtung des Zertifikatflows in Ihrer Umgebung variieren.

In diesem Tutorial lernen Sie Folgendes kennen:

> [!div class="checklist"]
>
> * Voraussetzungen
> * Konfigurieren von Zertifikaten für ein physisches Gerät
> * Konfigurieren des VPN
> * Konfigurieren der Verschlüsselung ruhender Daten

## <a name="prerequisites"></a>Voraussetzungen

Vergewissern Sie sich, dass folgende Voraussetzungen erfüllt sind, bevor Sie ein Azure Stack Edge Mini R-Gerät konfigurieren und einrichten:

* Sie haben das physische Gerät gemäß der Anleitung unter [Installieren von Azure Stack Edge Mini R](azure-stack-edge-mini-r-deploy-install.md) installiert.

* Wenn Sie beabsichtigen, eigene Zertifikate zu nutzen:
    - Sie müssen Ihre Zertifikate im entsprechenden Format einschließlich des Signaturkettenzertifikats bereithalten. Einzelheiten zu Zertifikaten finden Sie unter [Verwalten von Zertifikaten](azure-stack-edge-j-series-manage-certificates.md).

    - Falls Ihr Gerät nicht in der öffentlichen Azure-Cloud, sondern in Azure Government, in Azure Government Secret oder in einer streng geheimen Azure Government-Cloud bereitgestellt wird, ist für die Aktivierung des Geräts ein Signaturkettenzertifikat erforderlich. 
    Ausführliche Informationen zu Zertifikaten finden Sie unter [Verwenden von Zertifikaten mit einem Azure Stack Edge Pro-GPU-Gerät](azure-stack-edge-j-series-manage-certificates.md).


## <a name="configure-certificates-for-device"></a>Konfigurieren von Zertifikaten für ein Gerät

1. Zertifikate werden auf der Seite **Zertifikate** konfiguriert. Abhängig davon, ob Sie den Gerätenamen oder die DNS-Domäne auf der Seite **Gerät** geändert haben, können Sie für Ihre Zertifikate eine der folgenden Optionen auswählen.

    - Wenn Sie die Standardangaben für Gerätename und DNS-Domäne im vorherigen Schritt nicht geändert haben und keine eigenen Zertifikate bereitstellen möchten, können Sie diesen Schritt überspringen und mit dem nächsten Schritt fortfahren. Fürs Erste hat das Gerät automatisch selbstsignierte Zertifikate generiert. 

       <!-- ![Local web UI "Certificates" page](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/certificate-1.png)-->

    - Wenn Sie den Gerätenamen oder die DNS-Domäne geändert haben, wird als Status der Zertifikate **Nicht gültig** angezeigt. 

        ![Seite „Zertifikate“ auf der lokalen Webbenutzeroberfläche 2](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/certificate-2.png)    

        Wählen Sie ein Zertifikat aus, um die Statusdetails anzuzeigen.

        <!--![Local web UI "Certificates" page 3](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-1a.png)-->  

        Die Zertifikate haben den Status **Nicht gültig**, weil die Zertifikate nicht über den aktualisierten Gerätenamen oder die aktualisierte DNS-Domäne verfügen. (Diese werden für „Antragstellername“ und „Alternativer Antragstellername“ verwendet.) Zur erfolgreichen Aktivierung Ihres Geräts können Sie Ihre eigenen signierten Endpunktzertifikate und die entsprechenden Signaturketten verwenden. Sie fügen zunächst die Signaturkette hinzu und laden dann die Endpunktzertifikate hoch. Weitere Informationen finden Sie unter [Bereitstellen eigener Zertifikate](#bring-your-own-certificates).


    - Wenn Sie den Gerätenamen oder die DNS-Domäne geändert haben und keine eigenen Zertifikate bereitstellen, **wird die Aktivierung blockiert**.


#### <a name="bring-your-own-certificates"></a>Bereitstellen eigener Zertifikate

Die Signaturkette wurde bereits in einem früheren Schritt auf diesem Gerät hinzugefügt. Nun können Sie die Endpunktzertifikate, das Knotenzertifikat, das Zertifikat für die lokale Benutzeroberfläche und das VPN-Zertifikat hochladen. Gehen Sie wie hier beschrieben vor, um Ihre eigenen Zertifikate hinzuzufügen.

1. Um ein Zertifikat hochzuladen, wählen Sie auf der Seite **Zertifikat** die Option **+ Zertifikat hinzufügen** aus.

    [![Seite „Zertifikate“ auf der lokalen Webbenutzeroberfläche 4](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-1.png#lightbox)


1. Sie können auch andere Zertifikate hochladen. Sie können z. B. die Zertifikate für Azure Resource Manager- und Blob Storage-Endpunkte hochladen.

    ![Seite „Zertifikate“ auf der lokalen Webbenutzeroberfläche 6](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-3.png)

1. Sie können auch das Zertifikat der lokalen Webbenutzeroberfläche hochladen. Nachdem Sie dieses Zertifikat hochgeladen haben, müssen Sie den Browser starten und den Cache leeren. Sie müssen dann eine Verbindung mit der lokalen Webbenutzeroberfläche des Geräts herstellen.  

    ![Seite „Zertifikate“ auf der lokalen Webbenutzeroberfläche 7](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-4.png)

1. Sie können auch das Knotenzertifikat hochladen.


    ![Seite „Zertifikate“ auf der lokalen Webbenutzeroberfläche 8](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-5.png)

1. Abschließend können Sie das VPN-Zertifikat hochladen.
        
    ![Seite „Zertifikate“ auf der lokalen Webbenutzeroberfläche](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-6.png)

1. Sie können jederzeit ein Zertifikat auswählen und die Details anzeigen, um sicherzustellen, dass die Informationen mit Ihrem hochgeladenen Zertifikat übereinstimmen.

    [![Seite „Zertifikate“ auf der lokalen Webbenutzeroberfläche 9](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-7.png)](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-7.png#lightbox)

    Die Seite „Zertifikate“ muss aktualisiert werden, um die neu hinzugefügten Zertifikate widerzuspiegeln.

    [![Seite „Zertifikate“ auf der lokalen Webbenutzeroberfläche 10](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-8.png)](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-8.png#lightbox)  

    > [!NOTE]
    > Mit Ausnahme der öffentlichen Azure-Cloud sind für alle Cloudkonfigurationen (Azure Government oder Azure Stack Hub) Signaturkettenzertifikate erforderlich, die vor der Aktivierung bereitgestellt werden müssen.


## <a name="configure-vpn"></a>Konfigurieren des VPN

1. Wählen Sie auf der Kachel **Sicherheit** für VPN die Option **Konfigurieren** aus. 

    Vergewissern Sie sich zum Konfigurieren von VPN zunächst, dass in Azure alle erforderlichen Konfigurationsschritte ausgeführt wurden. Ausführliche Informationen finden Sie unter [Konfigurieren von VPN für Ihr Azure Stack Edge Mini R-Gerät über PowerShell](azure-stack-edge-placeholder.md). Anschließend können Sie die Konfiguration über die lokale Benutzeroberfläche durchführen.
    
    1. Wählen Sie auf der VPN-Seite die Option **Konfigurieren** aus.
        ![Konfigurieren von VPN: Lokale Benutzeroberfläche 1](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/configure-vpn-1.png)

    1. Gehen Sie auf dem Blatt **VPN konfigurieren** wie folgt vor:

        1. Geben Sie das Telefonbuch als Eingabe an.
        2. Geben Sie die JSON-Datei mit dem IP-Adressbereich des Azure-Rechenzentrums als Eingabe an. Diese Datei können Sie hier herunterladen: [https://www.microsoft.com/download/details.aspx?id=56519](https://www.microsoft.com/download/details.aspx?id=56519).
        3. Wählen Sie **eastus** als Region aus.
        4. Wählen Sie **Übernehmen**.

        ![Konfigurieren von VPN: Lokale Benutzeroberfläche 2](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/configure-vpn-2.png)
    
    1. Konfigurieren Sie IP-Adressbereiche, auf die nur über VPN zugegriffen werden soll. 
    
        - Wählen Sie unter **IP-Adressbereiche, auf die nur über VPN zugegriffen werden soll** die Option **Konfigurieren** aus.
        - Geben Sie den VNET-IPv4-Bereich ein, den Sie für Ihre Azure Virtual Network-Instanz ausgewählt haben.
        - Wählen Sie **Übernehmen**.
    
        ![Konfigurieren von VPN: Lokale Benutzeroberfläche 3](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/configure-vpn-3.png)

Ihr Gerät kann jetzt verschlüsselt werden. Konfigurieren Sie die Verschlüsselung ruhender Daten.


## <a name="enable-encryption"></a>Aktivieren der Verschlüsselung

1. Wählen Sie auf der Kachel **Sicherheit** für die Verschlüsselung ruhender Daten die Option **Konfigurieren** aus. Dies ist eine erforderliche Einstellung. Das Gerät kann erst aktiviert werden, wenn diese Einstellung erfolgreich konfiguriert wurde. 

    ![Lokale Webbenutzeroberfläche: Verschlüsselung ruhender Daten, Seite 1](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-1.png)

    Im Werk wird die BitLocker-Verschlüsselung auf Volumeebene aktiviert, nachdem die Geräte mit einem Image versehen wurden. Nachdem Sie das Gerät erhalten haben, müssen Sie die Verschlüsselung ruhender Daten konfigurieren. Speicherpool und Volumes werden neu erstellt, und Sie können BitLocker-Schlüssel angeben, um die Verschlüsselung ruhender Daten zu aktivieren und so eine zweite Verschlüsselungsebene für Ihre ruhenden Daten zu erhalten.

1. Geben Sie im Bereich **Verschlüsselung ruhender Daten** einen 32-stelligen Schlüssel (AES 256-Bit) mit Base-64-Verschlüsselung ein. Dieser Konfigurationsschritt muss nur einmal ausgeführt werden. Der Schlüssel dient zum Schutz des eigentlichen Verschlüsselungsschlüssels. 

    ![Lokale Webbenutzeroberfläche: Verschlüsselung ruhender Daten, Seite 2](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-2.png)

    Sie können auch auswählen, dass dieser Schlüssel automatisch generiert werden soll.

    ![Lokale Webbenutzeroberfläche: Verschlüsselung ruhender Daten, Seite 3](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-3.png)

1. Wählen Sie **Übernehmen**. Dieser Vorgang dauert einige Minuten. Der Status des Vorgangs wird auf der Kachel **Sicherheit** angezeigt.

    ![Seite für die Verschlüsselung ruhender Daten auf der lokalen Webbenutzeroberfläche 4](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-3.png)

1. Warten Sie, bis der Status **Abgeschlossen** lautet, und kehren Sie dann zu **Erste Schritte** zurück.

Ihr Gerät kann jetzt aktiviert werden.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial lernen Sie Folgendes kennen:

> [!div class="checklist"]
>
> * Voraussetzungen
> * Konfigurieren von Zertifikaten für ein physisches Gerät
> * Konfigurieren des VPN
> * Konfigurieren der Verschlüsselung ruhender Daten

Informationen zum Aktivieren Ihres Azure Stack Edge Mini R-Geräts finden Sie hier:

> [!div class="nextstepaction"]
> [Tutorial: Aktivieren von Azure Stack Edge Mini R](./azure-stack-edge-mini-r-deploy-activate.md)
