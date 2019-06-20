---
title: Erstellen und Konfigurieren von Clustern mit dem Enterprise-Sicherheitspaket in Azure HDInsight
description: Erfahren Sie, wie Sie Cluster mit dem Enterprise-Sicherheitspaket in Azure HDInsight erstellen und konfigurieren.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: howto
ms.date: 05/09/2019
ms.openlocfilehash: 7457c06f9f151cb310704a985c79572c7b770859
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67166227"
---
# <a name="create-and-configure-enterprise-security-package-clusters-in-azure-hdinsight"></a>Erstellen und Konfigurieren von Clustern mit dem Enterprise-Sicherheitspaket in Azure HDInsight

Das Enterprise-Sicherheitspaket für Azure HDInsight bietet Ihnen Zugriff auf die Active Directory-basierte Authentifizierung, Mehrbenutzerunterstützung und rollenbasierte Zugriffssteuerung für Ihre Apache Hadoop-Cluster in Azure. HDInsight-Cluster mit dem Enterprise-Sicherheitspaket ermöglichen Organisationen mit strengen unternehmensinternen Sicherheitsrichtlinien die Verarbeitung sensibler Daten.

In diesem Leitfaden wird beschrieben, wie Sie die erforderlichen Ressourcen korrekt konfigurieren, damit sich lokale Benutzer bei einem HDInsight-Cluster mit aktiviertem Enterprise-Sicherheitspaket anmelden können. Der Artikel führt Sie durch die erforderlichen Schritte zum Erstellen eines Azure HDInsight-Clusters mit aktiviertem Enterprise-Sicherheitspaket. Dies beinhaltet die Erstellung einer Windows-IaaS-VM, auf der Active Directory (AD) und Domain Name Service (DNS) aktiviert sind. Dieser Server dient hier als Ersatz für Ihre **tatsächliche** lokale Umgebung. Auf ihm führen Sie die Einrichtungs- und Konfigurationsschritte aus, sodass Sie sie später in Ihrer eigenen Umgebung wiederholen können. In diesem Leitfaden wird auch beschrieben, wie Sie eine Umgebung mit Hybrididentitäten mithilfe der Kennworthashsynchronisierung mit Azure Active Directory erstellen.

Dieser Leitfaden ist als Ergänzung des Artikels [Verwendung des Enterprise-Sicherheitspakets in HDInsight](apache-domain-joined-architecture.md) zu verstehen.

Richten Sie Active Directory und Domain Name Service (DNS) ein, bevor Sie die hier beschriebenen Schritte in Ihrer Umgebung ausführen. Aktivieren Sie außerdem Azure Active Directory, und synchronisieren Sie lokale Benutzerkonten mit Azure Active Directory.

![Architekturdiagramm](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image002.png)

## <a name="create-on-premises-environment"></a>Erstellen der lokalen Umgebung

Übersicht: In diesem Abschnitt verwenden Sie eine Azure-Vorlage für die schnelle Bereitstellung, um neue VMs zu erstellen sowie Domain Name Service (DNS) und eine neue AD-Gesamtstruktur zu konfigurieren.

1. Die Vorlage für die schnelle Bereitstellung finden Sie unter [Create an Azure VM with a new AD Forest](https://azure.microsoft.com/resources/templates/active-directory-new-domain/) (Azure-VM mit einer neuen AD-Gesamtstruktur erstellen).

1. Klicken Sie auf **In Azure bereitstellen**.
1. Melden Sie sich bei Ihrem Azure-Abonnement an.
1. Führen Sie auf dem Bildschirm **Create an Azure VM with a new AD Forest** (Azure-VM mit einer neuen AD-Gesamtstruktur erstellen) die folgenden Schritte aus:
    1. Wählen Sie in der Dropdownliste **Abonnement** das Abonnement aus, in dem die Ressourcen bereitgestellt werden sollen.
    1. Klicken Sie neben **Ressourcengruppe** auf **Neu erstellen**, und geben Sie den Namen **OnPremADVRG** ein.
    1. Geben Sie die folgenden Details in die weiteren Vorlagenfelder ein:

        * **Standort**: USA (Mitte)
        * **Administratorbenutzername**: HDIFabrikamAdmin
        * **Administratorkennwort**: <IHR_KENNWORT>
        * **Domäne**: HDIFabrikam.com
        * **DNS-Präfix**: hdifabrikam

        ![Vorlage zum Erstellen einer Azure-VM und AD-Gesamtstruktur](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-azure-vm-ad-forest.png)

    1. Klicken Sie auf **Kaufen**.
    1. Überwachen Sie die Bereitstellung, und warten Sie, bis sie abgeschlossen ist.
    1. Vergewissern Sie sich, dass die Ressourcen in der Ressourcengruppe `OnPremADVRG` erstellt wurden.

## <a name="configure-users-and-groups-for-cluster-access"></a>Konfigurieren von Benutzern und Gruppen für den Clusterzugriff

Übersicht: In diesem Abschnitt erstellen Sie die Benutzer, die am Ende dieser Anleitung Zugriff auf den HDInsight-Cluster haben.

1. Stellen Sie mit Remotedesktop eine Verbindung mit dem Domänencontroller her.
    1. Wenn Sie die zu Beginn erwähnte Vorlage verwendet haben, ist der Domänencontroller eine VM namens **adVM** in der Ressourcengruppe `OnPremADVRG`.
    1. Navigieren Sie im Azure-Portal zu **Ressourcengruppen** > **OnPremADVRG** > **adVM** > **Verbinden**.
    1. Klicken Sie auf die Registerkarte **RDP** und dann auf **RDP-Datei herunterladen**.
    1. Speichern Sie die Datei auf Ihrem Computer, und öffnen Sie sie.
    1. Verwenden Sie `HDIFabrikam\HDIFabrikamAdmin` als Benutzernamen, wenn Sie zur Eingabe von Anmeldeinformationen aufgefordert werden, und geben Sie dann das Kennwort ein, das Sie für das Administratorkonto ausgewählt haben.

1. Nachdem die Remotedesktopsitzung auf der Domänencontroller-VM geöffnet wurde, starten Sie über das Dashboard **Server-Manager** das Snap-In **Active Directory-Benutzer und -Computer**. Klicken Sie oben rechts auf **Tools**, und wählen Sie dann in der Dropdownliste **Active Directory-Benutzer und -Computer** aus.

    ![Server-Manager: Öffnen der Active Directory-Verwaltung](./media/apache-domain-joined-create-configure-enterprise-security-cluster/server-manager-active-directory-screen.png)

1. Erstellen Sie zwei neue Benutzer: **HDIAdmin** und **HDIUser**. Diese beiden Benutzer werden zur Anmeldung beim HDInsight-Cluster verwendet.

    1. Klicken Sie auf dem Bildschirm **Active Directory-Benutzer und -Computer** auf **Aktion** > **Neu** > **Benutzer**.

        ![Erstellen eines neuen Active Directory-Benutzers](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-user.png)

    1. Geben Sie auf dem Bildschirm **Neues Objekt – Benutzer** den Namen `HDIUser` in das Feld **Benutzeranmeldename** ein, und klicken Sie auf **Weiter**.

        ![Erstellen des ersten Administratorbenutzers](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image020.png)

    1. Geben Sie im Popupfenster das gewünschte Kennwort für das neue Konto ein. Aktivieren Sie das Kontrollkästchen **Kennwort läuft nie ab**. Klicken Sie auf **OK**.
    1. Klicken Sie auf **Fertig stellen**, um das neue Konto zu erstellen.
    1. Erstellen Sie einen weiteren Benutzer `HDIAdmin`.

        ![Erstellen des zweiten Administratorbenutzers](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image024.png)

1. Klicken Sie auf dem Bildschirm **Active Directory-Benutzer und -Computer** auf **Aktion** > **Neu** > **Gruppe**. Erstellen Sie eine neue Gruppe `HDIUserGroup`.

    ![Erstellen einer neuen Active Directory-Gruppe](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-group.png)

    ![Erstellen einer neuen Gruppe 2](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image028.png)

1. Fügen Sie den im vorherigen Schritt erstellten Benutzer **HDIUser** der Gruppe **HDIUserGroup** als Mitglied hinzu.

    1. Klicken Sie mit der rechten Maustaste auf **HDIUserGroup**, und klicken Sie dann auf **Eigenschaften**.
    1. Wechseln Sie zur Registerkarte **Mitglieder**, und klicken Sie auf **Hinzufügen**.
    1. Geben Sie im Feld **Geben Sie die zu verwendenden Objektnamen ein** `HDIUser` ein, und klicken Sie auf **OK**.
    1. Wiederholen Sie die obigen Schritte für das Konto `HDIAdmin`.

        ![Hinzufügen von Mitgliedern zur Gruppe](./media/apache-domain-joined-create-configure-enterprise-security-cluster/active-directory-add-users-to-group.png)

Sie haben nun Ihre Active Directory-Umgebung sowie zwei Benutzer und eine Benutzergruppe für den Zugriff auf den HDInsight-Cluster erstellt.

Diese Benutzer werden mit Azure AD synchronisiert.

### <a name="create-a-new-azure-active-directory"></a>Erstellen einer neuen Azure Active Directory-Instanz

1. Melden Sie sich beim Azure-Portal an.
1. Klicken Sie auf **Ressource erstellen**, und geben Sie **directory** ein. Wählen Sie **Azure Active Directory** > **Erstellen** aus.
1. Geben Sie unter **Organisationsname** den Namen **HDIFabrikam** ein.
1. Geben Sie unter **Name der Anfangsdomäne** den Namen **HDIFabrikamoutlook** ein.
1. Klicken Sie auf **Create**.
1. Klicken Sie im Azure-Portal auf der linken Seite auf **Azure Active Directory**.
1. Klicken Sie ggf. auf **Verzeichnis wechseln**, um in das neu erstellte Verzeichnis **HDIFabrikamoutlook** zu wechseln.
1. Klicken Sie unter**Verwalten** auf **Benutzerdefinierte Domänennamen** > **Benutzerdefinierte Domäne hinzufügen**.
1. Geben Sie unter **Benutzerdefinierter Domänenname** den Namen **HDIFabrikam.com** ein, und klicken Sie auf **Domäne hinzufügen**.

![Erstellen einer neuen Azure Active Directory-Instanz](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-new-directory.png)

![Erstellen einer neuen benutzerdefinierten Domäne](./media/apache-domain-joined-create-configure-enterprise-security-cluster/create-custom-domain.png)

## <a name="configure-your-azure-ad-tenant"></a>Konfigurieren Ihres Azure AD-Mandanten

Übersicht: Jetzt konfigurieren Sie Ihren Azure AD-Mandanten, damit Sie Benutzer und Gruppen aus der lokalen Active Directory-Instanz mit der Cloud synchronisieren können.

1. Erstellen Sie einen Active Directory-Mandantenadministrator.
    1. Melden Sie sich beim Azure-Portal an, und wählen Sie Ihren Azure AD-Mandanten **HDIFabrikam** aus.
    1. Wählen Sie unter **Verwalten** die Option **Benutzer** und dann **Neuer Benutzer** aus.
    1. Geben Sie die folgenden Details für den neuen Benutzer ein:

        * Name: fabrikamazureadmin
        * Benutzername: fabrikamazureadmin@hdifabrikam.com
        * Kennwort: Ein beliebiges sicheres Kennwort

    1. Klicken Sie auf den Abschnitt **Gruppen**, suchen Sie nach **AAD DC-Administratoren**, und klicken Sie auf **Auswählen**.

        ![Gruppen](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image038.png)

    1. Klicken Sie auf den Abschnitt **Verzeichnisrolle**, und wählen Sie auf der rechten Seite die Rolle **Globaler Administrator** aus. Klicken Sie auf **OK**.

        ![Verzeichnisrolle](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image040.png)

    1. Geben Sie ein Kennwort für den Benutzer ein. Klicken Sie auf **Create**.

1. Wenn Sie das Kennwort für den neu erstellten Benutzer <fabrikamazureadmin@hdifabrikam.com> ändern möchten, melden Sie sich mit der Identität beim Azure-Portal an. Sie werden dann aufgefordert, das Kennwort zu ändern.

## <a name="sync-on-premises-users-to-azure-ad"></a>Synchronisieren lokaler Benutzer mit Azure AD

### <a name="download-and-install-microsoft-azure-active-directory-connect"></a>Herunterladen und Installieren von Microsoft Azure Active Directory Connect

1. [Azure AD Connect herunterladen](https://www.microsoft.com/download/details.aspx?id=47594).

1. Installieren Sie Microsoft Azure Active Directory Connect auf dem Domänencontroller.
    1. Öffnen Sie die ausführbare Datei, die Sie im vorherigen Schritt heruntergeladen haben, und akzeptieren Sie die Lizenzbedingungen. Klicken Sie auf **Weiter**.

        ![Azure AD Connect](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image052.png)

    1. Klicken Sie auf **Express-Einstellungen verwenden**, und schließen Sie die Installation ab.

        ![Express-Einstellungen verwenden](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image054.png)

### <a name="configure-sync-with-on-premises-domain-controller"></a>Konfigurieren der Synchronisierung mit dem lokalen Domänencontroller

1. Geben Sie auf dem Bildschirm **Mit Azure AD verbinden** den Benutzernamen und das Kennwort des globalen Administrators für Azure AD ein. Klicken Sie auf  **Weiter**. Dies ist der Benutzername `fabrikamazureadmin@hdifabrikam.com`, den Sie beim Konfigurieren Ihres Active Directory-Mandanten erstellt haben.
    ![Herstellen einer Verbindung mit Azure AD](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image058.png)
1. Geben Sie auf dem Bildschirm **Mit Active Directory Domain Services verbinden** den Benutzernamen und das Kennwort für ein Unternehmensadministratorkonto ein. Klicken Sie auf  **Weiter**. Dies sind der Benutzername `HDIFabrikam\HDIFabrikamAdmin` und das zugehörige Kennwort, das Sie zuvor erstellt haben.

   ![Mit Active Directory Domain Services verbinden](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image060.png)
1. Klicken Sie auf der Seite **Konfiguration der Azure AD-Anmeldung** auf **Weiter**.
    ![Konfiguration der Azure AD-Anmeldung](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image062.png)
1. Klicken Sie auf dem Bildschirm „Bereit zur Konfiguration“ auf  **Installieren**.
    ![Installieren](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image064.png)
1. Klicken Sie auf **Beenden**, wenn der Bildschirm **Konfiguration abgeschlossen** angezeigt wird.
    ![Konfiguration abgeschlossen](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image078.png)

1. Vergewissern Sie sich nach Abschluss der Synchronisierung, dass die Benutzer, die Sie in der IaaS-Active Directory-Instanz erstellt haben, mit Azure Active Directory synchronisiert wurden.
    1. Melden Sie sich beim Azure-Portal an.
    1. Wählen Sie **Azure Active Directory** > **HDIFabrikam** > **Benutzer** aus.

### <a name="create-an-user-assigned-managed-identity"></a>Erstellen einer benutzerseitig zugewiesenen verwalteten Identität

Erstellen Sie eine benutzerseitig zugewiesene verwaltete Identität zum Konfigurieren von Azure Active Directory Domain Services (Azure AD DS). Weitere Informationen zum Erstellen und einer benutzerseitig zugewiesenen verwalteten Identität finden Sie unter [Erstellen, Auflisten, Löschen oder Zuweisen einer Rolle zu einer benutzerseitig zugewiesenen verwalteten Identität über das Azure-Portal](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

1. Melden Sie sich beim Azure-Portal an.
1. Klicken Sie auf **Ressource erstellen**, und geben Sie **verwaltete Identität** ein. Wählen Sie **Benutzerseitig zugewiesene verwaltete Identität** > **Erstellen** aus.
1. Geben Sie **HDIFabrikamManagedIdentity** in das Feld **Ressourcenname** ein.
1. Wählen Sie Ihr Abonnement aus.
1. Klicken Sie unter **Ressourcengruppe** auf **Neu erstellen**, und geben Sie **HDIFabrikam-CentralUS** ein.
1. Wählen Sie unter **Standort** die Option **USA, Mitte** aus.
1. Klicken Sie auf **Create**.

![Erstellen einer neuen benutzerseitig zugewiesenen verwalteten Identität](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image082.png)

### <a name="enable-azure-active-directory-domain-services"></a>Aktivieren von Azure Active Directory Domain Services

Weitere Informationen finden Sie unter [Aktivieren von Azure Active Directory Domain Services mithilfe des Azure-Portals](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

1. Erstellen Sie das virtuelle Netzwerk zum Hosten von Azure AD DS. Führen Sie den folgenden PowerShell-Code aus.

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Set-AzContext -Subscription 'SUBSCRIPTION_ID'
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS' -Location 'Central US' -Name 'HDIFabrikam-AADDSVNET' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'AADDS-subnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Melden Sie sich beim Azure-Portal an.
1. Klicken Sie auf **Ressource erstellen**, geben Sie **Domain services** ein, und wählen Sie **Azure AD Domain Services** aus.
1. Führen Sie auf dem Bildschirm **Grundlagen** die folgenden Schritte aus:
    1. Wählen Sie unter **Verzeichnisname** die Azure Active Directory-Instanz **HDIFabrikam** aus, die Sie in diesem Tutorial erstellt haben.
    1. Geben Sie **HDIFabrikam.com** in das Feld **DNS-Domänenname** ein.
    1. Wählen Sie Ihr Abonnement aus.
    1. Geben Sie die Ressourcengruppe **HDIFabrikam-CentralUS** an, und wählen Sie unter **Standort** die Option **USA, Mitte** aus.

        ![Azure AD DS: Grundlegende Informationen](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image084.png)

1. Wählen Sie auf dem Bildschirm **Netzwerk** das Netzwerk (**HDIFabrikam-VNET**) und das Subnetz (**AADDS-subnet**) aus, die Sie mit dem obigen PowerShell-Skript erstellt haben. Alternativ können Sie an dieser Stelle auf **Neu erstellen** klicken, um ein virtuelles Netzwerk zu erstellen.

    ![Auswählen des Netzwerks](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image086.png)

1. Auf dem Bildschirm **Administratorgruppe** sollte eine Benachrichtigung angezeigt werden, dass bereits eine Gruppe mit dem Namen **AAD DC-Administratoren** zur Verwaltung dieser Gruppe erstellt wurde. Sie können die Mitgliedschaft der Gruppe optional ändern, für die Schritte in diesem Tutorial ist dies jedoch nicht erforderlich. Klicken Sie auf **OK**.

    ![Anzeigen der Administratorgruppe](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image088.png)

1. Aktivieren Sie auf dem Bildschirm **Synchronisierung** die vollständige Synchronisierung, indem Sie **Alle** auswählen und dann auf **OK** klicken.

    ![Aktivieren der Synchronisierung](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image090.png)

1. Überprüfen Sie auf dem Bildschirm **Zusammenfassung** die Details der Azure AD DS-Instanz, und klicken Sie auf **OK**.

    ![Überprüfen der Details](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image092.png)

1. Wenn Sie AD DS aktiviert haben, wird ein lokaler DNS-Server auf den Azure-VMs ausgeführt.

### <a name="configure-your-azure-ad-ds-virtual-network"></a>Konfigurieren des virtuellen Azure AD DS-Netzwerks

In diesem Abschnitt konfigurieren Sie Ihr virtuelles Azure AD DS-Netzwerk (**HDIFabrikam-AADDSVNET**) zur Verwendung Ihrer benutzerdefinierten DNS-Server.

1. Suchen Sie nach den IP-Adressen Ihrer benutzerdefinierten DNS-Server. Klicken Sie dazu auf die AD DS-Ressource **HDIFabrikam.com** und dann unter **Verwalten** auf  **Eigenschaften** . Die IP-Adressen werden unter  **IP-Adresse im virtuellen Netzwerk** angezeigt.

    ![Suchen nach den benutzerdefinierten DNS-IP-Adressen für Azure AD DS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image096.png)

1. Konfigurieren Sie **HDIFabrikam-AADDSVNET** mit den benutzerdefinierten IP-Adressen `10.0.0.4` und `10.0.0.5`.

    1. Wählen Sie unter der Kategorie  **Einstellungen**  die Option **DNS-Server**  aus. Aktivieren Sie das Optionsfeld  **Benutzerdefiniert**, geben Sie die erste IP-Adresse (10.0.0.4) unten in das Textfeld ein, und klicken Sie dann auf  **Speichern**.
    1. Wiederholen Sie diese Schritte, um weitere IP-Adressen hinzuzufügen (in diesem Beispiel 10.0.0.5).

1. In unserem Szenario wurde Azure AD DS wie im folgenden Screenshot dargestellt zur Verwendung der IP-Adressen 10.0.0.4 und 10.0.0.5 konfiguriert und dabei die gleiche IP-Adresse im Azure AD DS-VNET festgelegt.

    ![Anzeigen benutzerdefinierter DNS-Server](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image098.png)

## <a name="securing-ldap-traffic"></a>Sichern des LDAP-Datenverkehrs

Das Lightweight Directory Access Protocol (LDAP) wird zum Lesen und Schreiben in Active Directory verwendet. Sie können die Vertraulichkeit und Sicherheit von LDAP-Datenverkehr mithilfe von Secure Sockets Layer (SSL)/Transport Layer Security (TLS) gewährleisten. Sie können LDAP über SSL (Secure LDAP, LDAPS) aktivieren, indem Sie ein korrekt formatiertes Zertifikat installieren.

Weitere Informationen zu LDAPS finden Sie unter [Konfigurieren von sicherem LDAP (LDAPS) für eine über Azure AD Domain Services verwaltete Domäne](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap).

In diesem Abschnitt erstellen Sie ein selbstsigniertes Zertifikat, laden das Zertifikat herunter und konfigurieren LDAP (LDAPS) für die durch Azure AD DS verwaltete Domäne verwaltete **hdifabrikam**.

Das folgende Skript erstellt ein Zertifikat für „hdifabrikam“. Das Zertifikat wird unter dem Pfad „LocalMachine“ gespeichert.

> [!Note] 
> Zum Erstellen der SSL-Zertifikatanforderung kann ein Hilfsprogramm oder eine Anwendung verwendet werden, das bzw. die eine gültige PKCS \#10-Anforderung erstellt.

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

Überprüfen Sie, ob das Zertifikat im persönlichen Speicher auf dem Computer installiert wurde. Führen Sie die folgenden Schritte aus:

1. Starten Sie die Microsoft Management Console (MMC).
1. Fügen Sie das Snap-In „Zertifikate“ hinzu, das Zertifikate auf dem lokalen Computer verwaltet.
1. Erweitern Sie  **Zertifikate (lokaler Computer)** ,  **Persönlich** und dann  **Zertifikate**. Der persönliche Speicher sollte ein neues Zertifikat enthalten. Dieses Zertifikat ist auf den vollständig qualifizierten Hostnamen ausgestellt.

    ![Überprüfen der Zertifikaterstellung](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image102.png)

1. Klicken Sie im rechten Bereich mit der rechten Maustaste auf das Zertifikat, das Sie im vorherigen Schritt erstellt haben, zeigen Sie auf  **Alle Aufgaben**, und klicken Sie dann auf  **Exportieren**.

1. Klicken Sie auf der Seite  **Privaten Schlüssel exportieren**  auf  **Ja, privaten Schlüssel exportieren**. Der private Schlüssel ist erforderlich, um die verschlüsselten Nachrichten von dem Computer zu lesen, auf dem der Schlüssel importiert wird.

    ![Privaten Schlüssel exportieren](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image103.png)

1. Übernehmen Sie auf der Seite  **Exportdateiformat**  die Standardeinstellungen, und klicken Sie dann auf  **Weiter**. 
1. Geben Sie auf der Seite  **Kennwort**  ein Kennwort für den privaten Schlüssel ein, wählen Sie für **Verschlüsselung** die Option **TripleDES-SHA1** aus, und klicken Sie auf **Weiter**.
1. Geben Sie auf der Seite  **Export in Datei**  den Pfad und Namen der exportierten Zertifikatsdatei ein, und klicken Sie dann auf  **Weiter**.
1. Der Dateiname muss die Erweiterung PFX haben. Diese Datei wird im Azure-Portal konfiguriert, um eine sichere Verbindung herzustellen.
1. Aktivieren Sie LDAP (LDAPS) für eine über Azure AD DS verwaltete Domäne.
    1. Wählen Sie im Azure-Portal die Domäne **HDIFabrikam.com** aus.
    1. Klicken Sie unter **Verwalten** auf **Secure LDAP**.
    1. Klicken Sie auf dem Bildschirm **Secure LDAP** unter **Secure LDAP** auf **Aktivieren**.
    1. Suchen Sie nach der PFX-Zertifikatsdatei, die Sie auf Ihren Computer exportiert haben.
    1. Geben Sie das Kennwort für das Zertifikat ein.

    ![Aktivieren von LDAPS](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image113.png)

1. Nachdem Sie LDAPS aktiviert haben, öffnen Sie Port 636, um die Erreichbarkeit sicherzustellen.
    1. Klicken Sie auf die Netzwerksicherheitsgruppe **AADDS-HDIFabrikam.com-NSG** in der Ressourcengruppe **HDIFabrikam-CentralUS**.
    1. Klicken Sie unter **Einstellungen** auf **Eingangssicherheitsregeln** > **Hinzufügen**.
    1. Geben Sie auf dem Bildschirm **Eingangssicherheitsregel hinzufügen** die folgenden Eigenschaften ein, und klicken Sie auf **Hinzufügen**:

        | Eigenschaft | Wert |
        |---|---|
        | `Source` | Beliebig |
        | Quellportbereiche | * |
        | Ziel | Beliebig |
        | Zielportbereich | 636 |
        | Protocol | Beliebig |
        | Aktion | ZULASSEN |
        | Priorität | <Desired Number> |
        | NAME | Port_LDAP_636 |

    ![Eingangssicherheitsregel](./media/apache-domain-joined-create-configure-enterprise-security-cluster/add-inbound-security-rule.png)

1. `HDIFabrikamManagedIdentity` ist die benutzerseitig zugewiesene verwaltete Identität. Für die verwaltete Identität ist die Rolle „Mitwirkender für die HDInsight-Domänendienste“ aktiviert, sodass sie Domänendienstvorgänge lesen, erstellen, ändern und löschen kann.

    ![Erstellen einer benutzerseitig zugewiesenen verwalteten Identität](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image117.png)

## <a name="creating-enterprise-security-package-enabled-hdinsight-cluster"></a>Erstellen eines HDInsight-Clusters mit aktiviertem Enterprise-Sicherheitspaket

Für diesen Schritt müssen vorab die folgenden Aufgaben ausgeführt werden:

1. Erstellen Sie eine neue Ressourcengruppe `HDIFabrikam-WestUS` am Standort `West US`.
1. Erstellen Sie ein virtuelles Netzwerk, das den HDInsight-Cluster mit dem Enterprise-Sicherheitspaket hostet.

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS' -Location 'West US' -Name 'HDIFabrikam-HDIVNet' -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name 'SparkSubnet' -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Erstellen Sie eine Peerbeziehung zwischen dem virtuellen Netzwerk, das Azure AD DS hostet (`HDIFabrikam-AADDSVNET`), und dem virtuellen Netzwerk, das den HDInsight-Cluster mit dem Enterprise-Sicherheitspaket hostet (`HDIFabrikam-HDIVNet`). Führen Sie mit dem folgenden PowerShell-Code das Peering dieser beiden virtuellen Netzwerke durch.

    ```powershell
    Add-AzVirtualNetworkPeering -Name 'HDIVNet-AADDSVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS')

    Add-AzVirtualNetworkPeering -Name 'AADDSVNet-HDIVNet' -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-WestUS').Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName 'HDIFabrikam-CentralUS')
    ```

1. Erstellen Sie ein neues Azure Data Lake Storage Gen2-Konto **Hdigen2store**, das mit der benutzerseitig verwalteten Identität **HDIFabrikamManagedIdentity** konfiguriert ist. Weitere Informationen zum Erstellen von Azure Data Lake Storage Gen2-Konten mit aktivierten benutzerseitig verwalteten Identitäten finden Sie unter [Verwenden von Azure Data Lake Storage Gen2 mit Azure HDInsight-Clustern](../hdinsight-hadoop-use-data-lake-storage-gen2.md).

1. Richten Sie benutzerdefiniertes DNS im virtuellen Netzwerk **HDIFabrikam-AADDSVNET** ein.
    1. Navigieren Sie im Azure-Portal zu **Ressourcengruppen** > **OnPremADVRG** > **HDIFabrikam-AADDSVNET** > **DNS-Server**.
    1. Wählen Sie **Benutzerdefinierte** aus, und geben Sie `10.0.0.4` und `10.0.0.5` ein.
    1. Klicken Sie auf **Speichern**.

        ![Speichern der benutzerdefinierten DNS-Einstellungen](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image123.png)

1. Erstellen Sie einen neuen HDInsight Spark-Cluster mit aktiviertem Enterprise-Sicherheitspaket.
    1. Klicken Sie auf **Benutzerdefiniert (Größe, Einstellungen, Apps)** .
    2. Geben Sie die gewünschten Details für Abschnitt 1 **Grundlagen** ein. Stellen Sie sicher, dass der **Clustertyp** auf **Spark 2.3 (HDI 3.6)** festgelegt ist und die **Ressourcengruppe** auf **HDIFabrikam-CentralUS**.

    1. Führen Sie in Abschnitt 2 **Sicherheit und Netzwerkbetrieb** die folgenden Schritte aus:
        1. Klicken Sie unter **Enterprise-Sicherheitspaket** auf **Aktiviert**.
        1. Klicken Sie auf **Clusteradministratorbenutzer**, und wählen Sie das Konto **HDIAdmin** aus, das Sie zuvor als lokalen Administratorbenutzer erstellt haben. Klicken Sie auf **Auswählen**.

        1. Klicken Sie auf **Clusterzugriffsgruppe**, und wählen Sie dann **HDIUserGroup** aus. Jeder Benutzer, den Sie dieser Gruppe später hinzufügen, kann auf HDInsight-Cluster zugreifen.

            ![Auswählen der Clusterzugriffsgruppe](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image129.jpg)

    1. Führen Sie die weiteren Schritte der Clusterkonfiguration aus, und überprüfen Sie die Details in der **Clusterübersicht**. Klicken Sie auf **Create**.

1. Melden Sie sich mit Ihrem Administratorbenutzernamen `hdiadmin@hdifabrikam.com` und Ihrem Kennwort bei der Ambari-Benutzeroberfläche für den neuen Cluster unter `https://CLUSTERNAME.azurehdinsight.net` an.

    ![Anmelden bei Ambari](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image135.jpg)

1. Klicken Sie im Cluster-Dashboard auf **Roles** (Rollen).
1. Geben Sie auf der Seite **Roles** (Rollen) unter **Assign roles to these** (Rollen zuweisen zu) die Gruppe **hdiusergroup** ein, um sie der Rolle **Cluster Administrator** (Clusteradministrator) zuzuweisen.

    ![Zuweisen der Clusteradministratorrolle zu „hdiusergroup“](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image137.jpg)

1. Öffnen Sie Ihren SSH-Client, und melden Sie sich mit dem Benutzer **hdiuser**, den Sie zuvor in der lokalen Active Directory-Instanz erstellt haben, beim Cluster an.

    ![Anmelden beim Cluster mit SSH](./media/apache-domain-joined-create-configure-enterprise-security-cluster/image139.jpg)

Wenn Sie sich mit diesem Konto anmelden können, ist der Cluster mit dem Enterprise-Sicherheitspaket korrekt zur Synchronisierung mit Ihrer lokalen Active Directory-Instanz konfiguriert.

## <a name="next-steps"></a>Nächste Schritte

* [Einführung in die Apache Hadoop-Sicherheit mit dem Enterprise-Sicherheitspaket](apache-domain-joined-introduction.md)
