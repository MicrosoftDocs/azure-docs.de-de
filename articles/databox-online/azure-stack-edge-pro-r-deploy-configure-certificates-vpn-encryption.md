---
title: Tutorial zum Konfigurieren von Zertifikaten für ein Azure Stack Edge Pro R-Gerät im Azure-Portal | Microsoft-Dokumentation
description: In diesem Tutorial zur Bereitstellung von Azure Stack Edge Pro R erfahren Sie, wie Sie Zertifikate für Ihr physisches Gerät konfigurieren.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/19/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure certificates for Azure Stack Edge Pro R so I can use it to transfer data to Azure.
ms.openlocfilehash: abea192f209b968c6842e985620c067c93990687
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100548147"
---
# <a name="tutorial-configure-certificates-for-your-azure-stack-edge-pro-r"></a>Tutorial: Konfigurieren von Zertifikaten für Ihr Azure Stack Edge Pro R-Gerät

In diesem Tutorial erfahren Sie, wie Sie Zertifikate für Ihr Azure Stack Edge Pro R-Gerät über die lokale Webbenutzeroberfläche konfigurieren.

Die für diesen Schritt benötigte Zeit kann je nach der von Ihnen gewählten Option und der Einrichtung des Zertifikatflows in Ihrer Umgebung variieren.

In diesem Tutorial lernen Sie Folgendes kennen:

> [!div class="checklist"]
>
> * Voraussetzungen
> * Konfigurieren von Zertifikaten für ein physisches Gerät
> * Konfigurieren des VPN
> * Konfigurieren der Verschlüsselung ruhender Daten

## <a name="prerequisites"></a>Voraussetzungen

Vergewissern Sie sich, dass folgende Voraussetzungen erfüllt sind, bevor Sie ein Azure Stack Edge Pro R-Gerät konfigurieren und einrichten:

* Sie haben das physische Gerät gemäß der Anleitung unter [Tutorial: Installieren von Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-install.md) installiert.
* Wenn Sie beabsichtigen, eigene Zertifikate zu nutzen:
    - Sie müssen Ihre Zertifikate im entsprechenden Format einschließlich des Signaturkettenzertifikats bereithalten. Einzelheiten zu Zertifikaten finden Sie unter [Verwalten von Zertifikaten](azure-stack-edge-gpu-manage-certificates.md).



## <a name="configure-certificates-for-device"></a>Konfigurieren von Zertifikaten für ein Gerät

1. Zertifikate werden auf der Seite **Zertifikate** konfiguriert. Abhängig davon, ob Sie den Gerätenamen oder die DNS-Domäne auf der Seite **Gerät** geändert haben, können Sie für Ihre Zertifikate eine der folgenden Optionen auswählen.

    - Wenn Sie den Gerätenamen und die DNS-Domäne im vorherigen Schritt nicht geändert haben, können Sie diesen Schritt überspringen und mit dem nächsten Schritt fortfahren. Fürs Erste hat das Gerät automatisch selbstsignierte Zertifikate generiert. 

    - Wenn Sie den Gerätenamen oder die DNS-Domäne geändert haben, wird als Status der Zertifikate **Nicht gültig** angezeigt. 

        ![Seite „Zertifikate“ auf der lokalen Webbenutzeroberfläche 2](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/generate-certificate-1.png)    

        Wählen Sie ein Zertifikat aus, um die Statusdetails anzuzeigen.

        ![Seite „Zertifikate“ auf der lokalen Webbenutzeroberfläche 3](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/generate-certificate-1a.png)  

        Der Grund dafür ist, dass die Zertifikate nicht den aktualisierten Gerätenamen oder die aktualisierte DNS-Domäne aufweisen (diese werden für „Antragstellername“ und „Alternativer Antragstellername“ verwendet). Zur erfolgreichen Aktivierung Ihres Geräts können Sie Ihre eigenen signierten Endpunktzertifikate und die entsprechenden Signaturketten verwenden. Sie fügen zunächst die Signaturkette hinzu und laden dann die Endpunktzertifikate hoch. Weitere Informationen finden Sie unter [Bereitstellen eigener Zertifikate](#bring-your-own-certificates).

    - Wenn Sie den Gerätenamen oder die DNS-Domäne geändert haben und keine eigenen Zertifikate bereitstellen, **wird die Aktivierung blockiert**.

    
#### <a name="bring-your-own-certificates"></a>Bereitstellen eigener Zertifikate

Befolgen Sie diese Schritte, um Ihre eigenen Zertifikate einschließlich Signaturkette hinzuzufügen.

1. Um ein Zertifikat hochzuladen, wählen Sie auf der Seite **Zertifikat** die Option **+ Zertifikat hinzufügen** aus.

    ![Seite „Zertifikate“ auf der lokalen Webbenutzeroberfläche 4](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-1.png)

2. Laden Sie zuerst die Signaturkette hoch, und wählen Sie **Überprüfen und hinzufügen** aus.

    ![Seite „Zertifikate“ auf der lokalen Webbenutzeroberfläche 5](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-2.png)

3. Jetzt können Sie andere Zertifikate hochladen. Sie können z. B. die Zertifikate für Azure Resource Manager- und Blob Storage-Endpunkte hochladen.

    ![Seite „Zertifikate“ auf der lokalen Webbenutzeroberfläche 6](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-3.png)

    Sie können auch das Zertifikat der lokalen Webbenutzeroberfläche hochladen. Nachdem Sie dieses Zertifikat hochgeladen haben, müssen Sie den Browser starten und den Cache leeren. Sie müssen dann eine Verbindung mit der lokalen Webbenutzeroberfläche des Geräts herstellen.  

    ![Seite „Zertifikate“ auf der lokalen Webbenutzeroberfläche 7](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-5.png)

    Sie können auch das Knotenzertifikat hochladen.

    ![Seite „Zertifikate“ auf der lokalen Webbenutzeroberfläche 8](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-4.png)

    Abschließend können Sie das VPN-Zertifikat hochladen.

    ![Seite „Zertifikate“ auf der lokalen Webbenutzeroberfläche 9](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-4.png)

    Sie können jederzeit ein Zertifikat auswählen und die Details anzeigen, um sicherzustellen, dass die Informationen mit Ihrem hochgeladenen Zertifikat übereinstimmen.

    Die Seite „Zertifikate“ muss aktualisiert werden, um die neu hinzugefügten Zertifikate widerzuspiegeln.

    ![Seite „Zertifikate“ auf der lokalen Webbenutzeroberfläche 10](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-7.png)  

    > [!NOTE]
    > Mit Ausnahme der öffentlichen Azure-Cloud sind für alle Cloudkonfigurationen (Azure Government oder Azure Stack) Signaturkettenzertifikate erforderlich, die vor der Aktivierung bereitgestellt werden müssen.

1. Wählen Sie **< Zurück zu „Erste Schritte“** aus.

## <a name="configure-vpn"></a>Konfigurieren des VPN

1. Wählen Sie auf der Kachel **Sicherheit** für VPN die Option **Konfigurieren** aus.

    ![VPN-Seite auf der lokalen Webbenutzeroberfläche](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-1.png)  

    Vergewissern Sie sich zum Konfigurieren von VPN zunächst, dass in Azure alle erforderlichen Konfigurationsschritte ausgeführt wurden. Weitere Informationen finden Sie unter [Konfigurieren der Voraussetzungen](azure-stack-edge-placeholder.md) sowie unter [Konfigurieren von Azure-Ressourcen für VPN](azure-stack-edge-placeholder.md). Anschließend können Sie die Konfiguration über die lokale Benutzeroberfläche durchführen.
    
    1. Wählen Sie auf der VPN-Seite die Option **Konfigurieren** aus.
    2. Gehen Sie auf dem Blatt **VPN konfigurieren** wie folgt vor:

    - Aktivieren Sie **VPN-Einstellungen**.
    - Geben Sie das **gemeinsame VPN-Geheimnis** an. Dies ist der gemeinsam verwendete Schlüssel, den Sie beim Erstellen des Azure-VPN-Verbindungsobjekts angegeben haben.
    - Geben Sie die **IP-Adresse des VPN-Gateways** an. Dies ist die IP-Adresse des Gateways des lokalen Azure-Netzwerks.
    - Wählen Sie für **PFS-Gruppe** die Option **Keine** aus. 
    - Wählen Sie für **DH-Gruppe** die Option **Gruppe2** aus.
    - Wählen Sie für **IPSec-Integritätsmethode** die Option **SHA256** aus.
    - Wählen Sie für **Transformationskonstanten für IPsec-Verschlüsselung** die Option **GCMAES256** aus.
    - Wählen Sie für **Transformationskonstanten für IPsec-Authentifizierung** die Option **GCMAES256** aus.
    - Wählen Sie für **IKE-Verschlüsselungsmethode** die Option **AES256** aus.
    - Wählen Sie **Übernehmen**.

        ![Konfigurieren auf der lokalen Benutzeroberfläche 2](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-2.png)

    3. Wählen Sie zum Hochladen der VPN-Routenkonfigurationsdatei **Hochladen** aus. 
    
        ![Konfigurieren auf der lokalen Benutzeroberfläche 3](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-3.png)
    
        - Navigieren Sie zu der *JSON-Datei* mit der VPN-Konfiguration, die Sie im vorherigen Schritt auf Ihr lokales System heruntergeladen haben.
        - Wählen Sie die dem Gerät, dem virtuellen Netzwerk und den Gateways zugeordnet Region als Azure-Region aus.
        - Wählen Sie **Übernehmen**.
    
            ![Konfigurieren auf der lokalen Benutzeroberfläche 4](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-4.png)
    
    4. Konfigurieren Sie zum Hinzufügen clientspezifischer Routen IP-Adressbereiche, auf die nur per VPN zugegriffen werden soll. 
    
        - Wählen Sie unter **IP-Adressbereiche, auf die nur über VPN zugegriffen werden soll** die Option **Konfigurieren** aus.
        - Geben Sie einen gültigen IPv4-Bereich an, und wählen Sie **Hinzufügen** aus. Wiederholen Sie die Schritte, um weitere Bereiche hinzuzufügen.
        - Wählen Sie **Übernehmen**.
    
            ![Konfigurieren auf der lokalen Benutzeroberfläche 5](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-5.png)

1. Wählen Sie **< Zurück zu „Erste Schritte“** aus.

## <a name="configure-encryption-at-rest"></a>Konfigurieren der Verschlüsselung ruhender Daten

1. Wählen Sie auf der Kachel **Sicherheit** für die Verschlüsselung ruhender Daten die Option **Konfigurieren** aus. Dies ist eine erforderliche Einstellung. Das Gerät kann erst aktiviert werden, wenn diese Einstellung erfolgreich konfiguriert wurde. 

    Im Werk wird die BitLocker-Verschlüsselung auf Volumeebene aktiviert, nachdem die Geräte mit einem Image versehen wurden. Nachdem Sie das Gerät erhalten haben, müssen Sie die Verschlüsselung ruhender Daten konfigurieren. Speicherpool und Volumes werden neu erstellt, und Sie können BitLocker-Schlüssel angeben, um die Verschlüsselung ruhender Daten zu aktivieren und so eine zweite Verschlüsselungsebene für Ihre ruhenden Daten zu erhalten.

1. Geben Sie im Bereich **Verschlüsselung ruhender Daten** einen 32-stelligen Schlüssel mit Base-64-Verschlüsselung an. Dieser Konfigurationsschritt muss nur einmal ausgeführt werden. Der Schlüssel dient zum Schutz des eigentlichen Verschlüsselungsschlüssels. Sie können auswählen, dass dieser Schlüssel automatisch generiert werden soll, oder selbst einen Schlüssel eingeben.

    ![Bereich für die Verschlüsselung ruhender Daten auf der lokalen Webbenutzeroberfläche](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/encryption-key-1.png)

    Der Schlüssel wird in einer Schlüsseldatei auf der Seite **Clouddetails** gespeichert, nachdem das Gerät aktiviert wurde.

1. Wählen Sie **Übernehmen**. Dieser Vorgang dauert einige Minuten. Der Status des Vorgangs wird auf der Kachel **Sicherheit** angezeigt.

    ![Seite für die Verschlüsselung ruhender Daten auf der lokalen Webbenutzeroberfläche](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-status-1.png)

1. Warten Sie, bis der Status **Abgeschlossen** lautet, und wählen Sie dann **< Zurück zu "Erste Schritte"** aus.

Ihr Gerät kann jetzt aktiviert werden. 


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial lernen Sie Folgendes kennen:

> [!div class="checklist"]
>
> * Voraussetzungen
> * Konfigurieren von Zertifikaten für ein physisches Gerät
> * Konfigurieren des VPN
> * Konfigurieren der Verschlüsselung ruhender Daten

Informationen zum Aktivieren Ihres Azure Stack Edge Pro R-Geräts finden Sie hier:

> [!div class="nextstepaction"]
> [Tutorial: Aktivieren eines Azure Stack Edge Pro R-Geräts](./azure-stack-edge-pro-r-deploy-activate.md)
