---
title: FortiGate-Bereitstellungshandbuch | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie die FortiGate-Firewall der nächsten Generation von Fortinet einrichten und verwenden.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/30/2020
ms.author: jeedes
ms.openlocfilehash: cdaa6a9601452100ab90ef8b0f2191002f256b74
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025466"
---
# <a name="fortigate-azure-virtual-machine-deployment-guide"></a>FortiGate als virtueller Azure-Computer: Bereitstellungshandbuch

In diesem Bereitstellungshandbuch ist beschrieben, wie Sie die FortiGate-Firewall der nächsten Generation von Fortinet einrichten und verwenden, indem Sie sie als virtuellen Azure-Computer bereitstellen. Darüber hinaus konfigurieren Sie die Azure AD-Katalog-App „FortiGate SSL VPN“, um die VPN-Authentifizierung über Azure Active Directory zu ermöglichen.

## <a name="redeem-the-fortigate-license"></a>Einlösen der FortiGate-Lizenz

Das Fortinet-Produkt „FortiGate Next-Generation Firewall“ (FortiGate: Firewall der nächsten Generation) ist als virtueller Computer in Azure IaaS (Infrastructure-as-a-Service) verfügbar. Für diesen virtuellen Computer gibt es zwei Lizenzierungsmodi: „Nutzungsbasierte Bezahlung“ und „Bring Your Own License“ (BYOL).

Wenn Sie eine FortiGate-Lizenz von Fortinet erworben haben, um sie für die BYOL-Option für die VM-Bereitstellung zu verwenden, führen Sie die Einlösung auf der Fortinet-Seite zum Aktivieren von Produkten durch: https://support.fortinet.com. Die sich ergebende Lizenzdatei hat die Dateierweiterung „.lic“.

## <a name="download-firmware"></a>Herunterladen der Firmware

Zum Zeitpunkt der Artikelerstellung wird die Fortinet FortiGate-Azure-VM nicht mit der für die SAML-Authentifizierung erforderlichen Firmwareversion ausgeliefert. Die aktuelle Version muss von Fortinet abgerufen werden.

1. Melden Sie sich bei https://support.fortinet.com/ an.
2. Navigieren Sie zu **Download** > **Firmware Images** (Herunterladen > Firmwareimages).
3. Wählen Sie rechts neben **Release Notes** (Versionshinweise) die Option **Download** (Herunterladen) aus.
4. Wählen Sie **v6.00** > **6.4** > **6.4.2** aus.
5. Laden Sie **FGT_VM64_AZURE-v6-build1723-FORTINET.out** herunter, indem Sie in derselben Zeile den **HTTPS**-Link auswählen.
6. Speichern Sie die Datei zur späteren Verwendung.

## <a name="deploy-the-fortigate-vm"></a>Bereitstellen des virtuellen FortiGate-Computers

1. Navigieren Sie zum Azure-Portal, und melden Sie sich bei dem Abonnement an, unter dem Sie den virtuellen FortiGate-Computer bereitstellen möchten.
2. Erstellen Sie eine neue Ressourcengruppe, oder öffnen Sie die Ressourcengruppe, in der Sie den virtuellen FortiGate-Computer bereitstellen möchten.
3. Wählen Sie **Hinzufügen**.
4. Geben Sie unter **Marketplace durchsuchen** den Suchbegriff *Forti* ein. Wählen Sie **Fortinet FortiGate Next-Generation Firewall** aus.
5. Wählen Sie den Softwareplan aus („Bring Your Own License“, wenn Sie über eine Lizenz verfügen, andernfalls „Nutzungsbasierte Bezahlung“). Klicken Sie auf **Erstellen**.
6. Fügen Sie die VM-Konfiguration ein.

    ![Screenshot: Virtuellen Computer erstellen](virtual-machine.png)

7. Legen Sie den **Authentifizierungstyp** auf **Kennwort** fest, und geben Sie Administratoranmeldeinformationen für den virtuellen Computer ein.
8. Klicken Sie auf **Überprüfen** > **Erstellen**.
9. Warten Sie, bis die Bereitstellung des virtuellen Computers abgeschlossen ist.


### <a name="set-a-static-public-ip-address-and-assign-a-fully-qualified-domain-name"></a>Festlegen einer statischen öffentlichen IP-Adresse und Zuweisen eines vollqualifizierten Domänennamens

Um eine einheitliche Benutzerumgebung zu erhalten, sollten Sie die der FortiGate-VM zugewiesene öffentliche IP-Adresse so festlegen, dass ihre Zuweisung statisch erfolgt. Ordnen Sie sie zusätzlich einem vollqualifizierten Domänennamen (FQDN) zu.

1. Navigieren Sie zum Azure-Portal, und öffnen Sie die Einstellungen für die FortiGate-VM.
2. Wählen Sie auf dem Bildschirm **Übersicht** die öffentliche IP-Adresse aus.

    ![Screenshot: FortiGate SSL VPN](public-ip-address.png)

3. Wählen Sie **Statisch** > **Speichern** aus.

Erstellen Sie einen Hosteintrag (A) für den virtuellen Computer, wenn Sie über einen öffentlich routingfähigen Domänennamen für die Umgebung verfügen, in der der virtuelle FortiGate-Computer bereitgestellt wird. Dieser Eintrag wird der vorherigen öffentlichen IP-Adresse zugeordnet, die statisch zugewiesen wurde.

### <a name="create-a-new-inbound-network-security-group-rule-for-tcp-port-8443"></a>Erstellen einer neuen Netzwerksicherheitsgruppen-Eingangsregel für den TCP-Port 8443

1. Navigieren Sie zum Azure-Portal, und öffnen Sie die Einstellungen für die FortiGate-VM.
2. Klicken Sie im Menü auf der linken Seite auf **Netzwerk**. Die Netzwerkschnittstelle wird aufgeführt, und die Regeln für eingehende Ports werden angezeigt.
3. Wählen Sie **Regel für eingehenden Port hinzufügen** aus.
4. Erstellen Sie eine neue Regel für den eingehenden Port für TCP 8443.

    ![Screenshot: Eingangssicherheitsregel hinzufügen](port-rule.png)

5. Wählen Sie **Hinzufügen**.

## <a name="create-a-second-virtual-nic-for-the-vm"></a>Erstellen einer zweiten virtuellen Netzwerkkarte für die VM

Damit interne Ressourcen für Benutzer verfügbar gemacht werden können, muss der FortiGate-VM eine zweite virtuelle Netzwerkkarte hinzugefügt werden. Das virtuelle Netzwerk in Azure, in dem sich die virtuelle Netzwerkkarte befindet, muss über eine routingfähige Verbindung mit diesen internen Ressourcen verfügen.

1. Navigieren Sie zum Azure-Portal, und öffnen Sie die Einstellungen für die FortiGate-VM.
2. Wenn die FortiGate-VM nicht bereits beendet wurde, wählen Sie die Option **Beenden** aus und warten, bis die VM heruntergefahren wurde.
3. Klicken Sie im Menü auf der linken Seite auf **Netzwerk**.
4. Wählen Sie **Netzwerkschnittstelle anfügen** aus.
5. Wählen Sie die Option **Netzwerkschnittstelle erstellen und anfügen** aus.
6. Konfigurieren Sie die Eigenschaften für die neue Netzwerkschnittstelle, und wählen Sie anschließend die Option **Erstellen** aus.

    ![Screenshot: Netzwerkschnittstelle erstellen](new-network-interface.png)

7. Starten Sie die FortiGate-VM.


## <a name="configure-the-fortigate-vm"></a>Konfigurieren des virtuellen FortiGate-Computers

In den folgenden Abschnitten wird Schritt für Schritt beschrieben, wie Sie die FortiGate-VM einrichten.

### <a name="install-the-license"></a>Installieren der Lizenz

1. Gehe zu `https://<address>`. Hierbei steht `<address>` für den vollqualifizierten Domänennamen oder die öffentliche IP-Adresse, die dem virtuellen FortiGate-Computer zugewiesen ist.

2. Setzen Sie den Vorgang trotz etwaiger Zertifikatfehler fort.
3. Melden Sie sich mit den Administratoranmeldeinformationen an, die während der Bereitstellung des virtuellen FortiGate-Computers bereitgestellt wurden.
4. Wenn für die Bereitstellung das BYOL-Modell (Bring Your Own License) verwendet wird, wird eine Aufforderung zum Hochladen einer Lizenz angezeigt. Wählen Sie die weiter oben erstellte Lizenzdatei aus, und laden Sie sie hoch. Wählen Sie **OK** aus, und starten Sie den virtuellen FortiGate-Computer neu.

    ![Screenshot: FortiGate-VM-Lizenz](license.png)

5. Melden Sie sich nach dem Neustart erneut mit den Administratoranmeldeinformationen an, um die Lizenz zu überprüfen.

### <a name="update-firmware"></a>Aktualisieren der Firmware

1. Gehe zu `https://<address>`. Hierbei steht `<address>` für den vollqualifizierten Domänennamen oder die öffentliche IP-Adresse, die dem virtuellen FortiGate-Computer zugewiesen ist.

2. Setzen Sie den Vorgang trotz etwaiger Zertifikatfehler fort.
3. Melden Sie sich mit den Administratoranmeldeinformationen an, die während der Bereitstellung des virtuellen FortiGate-Computers bereitgestellt wurden.
4. Wählen Sie im Menü auf der linken Seite **System** > **Firmware** aus.
5. Wählen Sie unter **Firmwareverwaltung** die Option **Durchsuchen** und dann die zuvor heruntergeladene Firmwaredatei aus.
6. Ignorieren Sie die Warnung, und wählen Sie **Backup config and upgrade** (Konfiguration sichern und aktualisieren) aus.

    ![Screenshot: Firmwareverwaltung](backup-configure-upgrade.png)

7. Wählen Sie **Weiter**.
8. Wählen Sie **Speichern** aus, wenn Sie zum Speichern der FortiGate-Konfiguration (als CONF-Datei) aufgefordert werden.
9. Warten Sie, bis die Firmware hochgeladen und angewendet wurde. Warten Sie, bis der virtuelle FortiGate-Computer neu gestartet wurde.
10. Melden Sie sich nach dem Neustart des virtuellen FortiGate-Computers erneut mit den Administratoranmeldeinformationen an.
11. Wählen Sie **Später** aus, wenn Sie zum Einrichten des Dashboards aufgefordert werden.
12. Wählen Sie **OK** aus, wenn das Tutorialvideo beginnt.

### <a name="change-the-management-port-to-tcp-8443"></a>Ändern des Verwaltungsports in „TCP 8443“

1. Gehe zu `https://<address>`. Hierbei steht `<address>` für den vollqualifizierten Domänennamen oder die öffentliche IP-Adresse, die dem virtuellen FortiGate-Computer zugewiesen ist.

2. Setzen Sie den Vorgang trotz etwaiger Zertifikatfehler fort.
3. Melden Sie sich mit den Administratoranmeldeinformationen an, die während der Bereitstellung des virtuellen FortiGate-Computers bereitgestellt wurden.
4. Wählen Sie im Menü auf der linken Seite die Option **System** aus.
5. Ändern Sie unter **Administration Settings** (Verwaltungseinstellungen) den HTTPS-Port in **8443**, und wählen Sie **Übernehmen** aus.
6. Nachdem die Änderung übernommen wurde, wird vom Browser versucht, die Verwaltungsseite erneut zu laden. Hierbei tritt aber ein Fehler auf. Ab jetzt lautet die Adresse für die Verwaltungsseite wie folgt: `https://<address>:8443`.

    ![Screenshot: Remotezertifikat hochladen](certificate.png)

### <a name="upload-the-azure-ad-saml-signing-certificate"></a>Hochladen des Azure AD-SAML-Signaturzertifikats

1. Gehe zu `https://<address>:8443`. Hierbei steht `<address>` für den vollqualifizierten Domänennamen oder die öffentliche IP-Adresse, die dem virtuellen FortiGate-Computer zugewiesen ist.

2. Setzen Sie den Vorgang trotz etwaiger Zertifikatfehler fort.
3. Melden Sie sich mit den Administratoranmeldeinformationen an, die während der Bereitstellung des virtuellen FortiGate-Computers bereitgestellt wurden.
4. Wählen Sie im Menü auf der linken Seite **System** > **Zertifikate** aus.
5. Wählen Sie **Import** > **Remote Certificate** (Importieren > Remotezertifikat) aus.
6. Navigieren Sie zu dem Zertifikat, das bei der benutzerdefinierten FortiGate-App-Bereitstellung im Azure-Mandanten heruntergeladen wurde. Wählen Sie es aus, und wählen Sie anschließend **OK** aus.

### <a name="upload-and-configure-a-custom-ssl-certificate"></a>Hochladen und Konfigurieren eines benutzerdefinierten SSL-Zertifikats

Es kann ratsam sein, den virtuellen FortiGate-Computer mit Ihrem eigenen SSL-Zertifikat zu konfigurieren, das den von Ihnen verwendeten vollqualifizierten Domänennamen unterstützt. Wenn Sie Zugriff auf ein SSL-Zertifikat haben, das mit dem privaten Schlüssel im PFX-Format verpackt ist, kann es für diesen Zweck verwendet werden.

1. Gehe zu `https://<address>:8443`. Hierbei steht `<address>` für den vollqualifizierten Domänennamen oder die öffentliche IP-Adresse, die dem virtuellen FortiGate-Computer zugewiesen ist.

2. Setzen Sie den Vorgang trotz etwaiger Zertifikatfehler fort.
3. Melden Sie sich mit den Administratoranmeldeinformationen an, die während der Bereitstellung des virtuellen FortiGate-Computers bereitgestellt wurden.
4. Wählen Sie im Menü auf der linken Seite **System** > **Zertifikate** aus.
5. Wählen Sie **Import** > **Local Certificate** > **PKCS #12 Certificate** (Importieren > Lokales Zertifikat > PKCS #12-Zertifikat) aus.
6. Navigieren Sie zur PFX-Datei, die das SSL-Zertifikat und den privaten Schlüssel enthält.
7. Geben Sie das PFX-Kennwort und einen aussagekräftigen Namen für das Zertifikat an. Klicken Sie anschließend auf **OK**.
8. Wählen Sie im Menü auf der linken Seite **System** > **Einstellungen** aus.
9. Erweitern Sie unter **Administration Settings** (Verwaltungseinstellungen) die Liste neben dem **HTTPS-Serverzertifikat**, und wählen Sie das zuvor importierte SSL-Zertifikat aus.
10. Wählen Sie **Übernehmen**.
11. Schließen Sie das Browserfenster, und navigieren Sie zu `https://<address>:8443`.
12. Melden Sie sich mit den FortiGate-Administratoranmeldeinformationen an. Nun sollte die Verwendung des richtigen SSL-Zertifikats angezeigt werden.

### <a name="configure-authentication-timeout"></a>Konfigurieren des Authentifizierungszeitlimits

1. Navigieren Sie zum Azure-Portal, und öffnen Sie die Einstellungen für die FortiGate-VM.
2. Wählen Sie im Menü auf der linken Seite **Serielle Konsole** aus.
3. Melden Sie sich mit den Administratoranmeldeinformationen des virtuellen FortiGate-Computers bei der seriellen Konsole an.
4. Führen Sie in der seriellen Konsole die folgenden Befehle aus:

    ```
    config system global
    set remoteauthtimeout 60
    end
    ```

### <a name="ensure-network-interfaces-are-obtaining-ip-addresses"></a>Sicherstellen des Empfangs von IP-Adressen für Netzwerkschnittstellen

1. Gehe zu `https://<address>:8443`. Hierbei steht `<address>` für den vollqualifizierten Domänennamen oder die öffentliche IP-Adresse, die dem virtuellen FortiGate-Computer zugewiesen ist.

2. Melden Sie sich mit den Administratoranmeldeinformationen an, die während der Bereitstellung des virtuellen FortiGate-Computers bereitgestellt wurden.
3. Klicken Sie im Menü auf der linken Seite auf **Netzwerk**.
4. Wählen Sie unter „Netzwerk“ die Option **Schnittstellen** aus.
5. Sehen Sie sich „port1“ (externe Schnittstelle) und „port2“ (interne Schnittstelle) an, um sicherzustellen, dass hierfür eine IP-Adresse aus dem richtigen Azure-Subnetz empfangen wird.
    a. Wenn keiner dieser beiden Ports eine IP-Adresse aus dem Subnetz erhält (per DHCP), klicken Sie mit der rechten Maustaste auf den Port und wählen die Option **Bearbeiten** aus.
    b. Vergewissern Sie sich, dass unter „Adressierungsmodus“ die Option **DHCP** ausgewählt ist.
    c. Wählen Sie **OK** aus.

    ![Screenshot: Adressierung für Netzwerkschnittstellen](addressing.png)

### <a name="ensure-fortigate-vm-has-correct-route-to-on-premises-corporate-resources"></a>Sicherstellen, dass die FortiGate-VM über die richtige Route zu lokalen Unternehmensressourcen verfügt

Bei mehrfach vernetzten Azure-VMs befinden sich alle Netzwerkschnittstellen in demselben virtuellen Netzwerk (aber ggf. in separaten Subnetzen). Häufig bedeutet dies, dass für beide Netzwerkschnittstellen eine Verbindung mit den lokalen Unternehmensressourcen besteht, die über FortiGate veröffentlicht werden. Aus diesem Grund müssen Einträge für benutzerdefinierte Routen erstellt werden, mit denen sichergestellt wird, dass der Datenverkehr von der richtigen Schnittstelle ausgeht, wenn Anforderungen für lokale Unternehmensressourcen gesendet werden.

1. Gehe zu `https://<address>:8443`. Hierbei steht `<address>` für den vollqualifizierten Domänennamen oder die öffentliche IP-Adresse, die dem virtuellen FortiGate-Computer zugewiesen ist.

2. Melden Sie sich mit den Administratoranmeldeinformationen an, die während der Bereitstellung des virtuellen FortiGate-Computers bereitgestellt wurden.
3. Klicken Sie im Menü auf der linken Seite auf **Netzwerk**.
4. Wählen Sie unter „Netzwerk“ die Option **Statische Routen** aus.
5. Wählen Sie **Neu erstellen** aus.
6. Wählen Sie unter „Ziel“ die Option **Subnetz** aus.
7. Geben Sie unter „Subnetz“ die Informationen des Subnetzes an, in dem sich die lokalen Unternehmensressourcen befinden (z. B. 10.1.0.0/255.255.255.0).
8. Geben Sie neben „Gatewayadresse“ das Gateway des Azure-Subnetzes an, mit dem „port2“ verbunden ist (endet meist auf „1“, z. B. „10.6.1.1“).
9. Wählen Sie unter „Schnittstelle“ die interne Netzwerkschnittstelle „port2“ aus.
10. Klicken Sie auf **OK**.

    ![Screenshot: Konfigurieren einer Route](route.png)

## <a name="configure-fortigate-ssl-vpn"></a>Konfigurieren des FortiGate SSL VPN

Führen Sie die Schritte aus, die unter https://docs.microsoft.com/azure/active-directory/saas-apps/fortigate-ssl-vpn-tutorial beschrieben sind.
