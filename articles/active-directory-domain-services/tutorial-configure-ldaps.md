---
title: 'Tutorial: Konfigurieren von LDAPS für Azure Active Directory Domain Services | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie das Secure Lightweight Directory Access Protocol (LDAPS) für eine verwaltete Azure Active Directory Domain Services-Domäne konfigurieren.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: d5dbb7b71e2d67ed5b3f624c93c3c143d6c98e5d
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96618534"
---
# <a name="tutorial-configure-secure-ldap-for-an-azure-active-directory-domain-services-managed-domain"></a>Tutorial: Konfigurieren von Secure LDAP (LDAPS) für eine verwaltete Azure AD Domain Services-Domäne

Zur Kommunikation mit Ihrer verwalteten Azure Active Directory Domain Services-Domäne (Azure AD DS) wird das Lightweight Directory Access Protocol (LDAP) verwendet. LDAP-Datenverkehr ist standardmäßig nicht verschlüsselt – dies führt in vielen Umgebungen zu Bedenken hinsichtlich der Sicherheit.

Mit Azure AD DS können Sie die verwaltete Domäne für die Verwendung des Secure Lightweight Directory Access Protocol (LDAPS) konfigurieren. Bei Verwendung von Secure LDAP wird der Datenverkehr verschlüsselt. Secure LDAP ist auch bekannt als „LDAP über Secure Sockets Layer (SSL)/Transport Layer Security (TLS)“.

In diesem Tutorial wird gezeigt, wie Sie LDAPS für eine verwaltete Azure AD DS-Domäne konfigurieren.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines digitalen Zertifikats für die Verwendung mit Azure AD DS
> * Aktivieren von Secure LDAP für Azure AD DS
> * Konfigurieren von Secure LDAP für die Verwendung aus dem öffentlichen Internet
> * Binden und Testen von Secure LDAP für eine verwaltete Domäne

Wenn Sie kein Azure-Abonnement besitzen, [erstellen Sie ein Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie die folgenden Ressourcen und Berechtigungen:

* Ein aktives Azure-Abonnement.
    * Wenn Sie kein Azure-Abonnement besitzen, [erstellen Sie ein Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Einen mit Ihrem Abonnement verknüpften Azure Active Directory-Mandanten, der entweder mit einem lokalen Verzeichnis synchronisiert oder ein reines Cloudverzeichnis ist.
    * [Erstellen Sie einen Azure Active Directory-Mandanten][create-azure-ad-tenant], oder [verknüpfen Sie ein Azure-Abonnement mit Ihrem Konto][associate-azure-ad-tenant], sofern erforderlich.
* Eine verwaltete Azure Active Directory Domain Services-Domäne, die in Ihrem Azure AD-Mandanten aktiviert und konfiguriert ist.
    * [Erstellen und konfigurieren Sie eine verwaltete Azure Active Directory Domain Services-Domäne][create-azure-ad-ds-instance], sofern erforderlich.
* Das Tool *LDP.exe* muss auf Ihrem Computer installiert sein.
    * Bei Bedarf [installieren Sie die Remoteserver-Verwaltungstools][rsat] für *Active Directory Domain Services und LDAP*.

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

In diesem Tutorial konfigurieren Sie Secure LDAP für die verwaltete Domäne im Azure-Portal. Melden Sie sich zunächst beim [Azure-Portal](https://portal.azure.com) an.

## <a name="create-a-certificate-for-secure-ldap"></a>Erstellen eines Zertifikats für Secure LDAP

Bei Secure LDAP wird ein digitales Zertifikat zum Verschlüsseln der Kommunikation verwendet. Dieses digitale Zertifikat wird auf Ihre verwaltete Domäne angewendet und ermöglicht Tools wie *LDP.exe* eine sichere verschlüsselte Kommunikation beim Abfragen von Daten. Es gibt zwei Möglichkeiten, um ein Zertifikat für den Zugriff auf die verwaltete Domäne über Secure LDAP zu erstellen:

* Ein Zertifikat von einer öffentlichen Zertifizierungsstelle oder einer Unternehmenszertifizierungsstelle.
    * Wenn Ihre Organisation Zertifikate von einer öffentlichen Zertifizierungsstelle erhält, fordern Sie das Zertifikat für Secure LDAP bei dieser öffentlichen Zertifizierungsstelle an. Wenn Sie eine Unternehmenszertifizierungsstelle verwenden, fordern Sie das Zertifikat für Secure LDAP bei der Unternehmenszertifizierungsstelle an.
    * Eine öffentliche Zertifizierungsstelle funktioniert nur, wenn Sie einen benutzerdefinierten DNS-Namen in Ihrer verwalteten Domäne verwenden. Wenn der DNS-Domänenname Ihrer verwalteten Domäne auf *.onmicrosoft.com* endet, können Sie kein digitales Zertifikat erstellen, um Verbindungen mit dieser Standarddomäne zu sichern. Die Domäne *.onmicrosoft.com* ist im Besitz von Microsoft, daher stellt keine öffentliche Zertifizierungsstelle ein Zertifikat aus. Erstellen Sie in diesem Szenario ein selbstsigniertes Zertifikat, und verwenden Sie es, um sicheres LDAP zu konfigurieren.
* Ein selbstsigniertes Zertifikat, das Sie selbst erstellen.
    * Diese Methode eignet sich gut für Testzwecke und wird in diesem Tutorial vorgestellt.

Das Zertifikat, das Sie anfordern oder erstellen, muss die folgenden Anforderungen erfüllen. In Ihrer verwalteten Domäne treten Probleme auf, wenn Sie Secure LDAP mit einem ungültigen Zertifikat aktivieren:

* **Vertrauenswürdiger Aussteller**: Das Zertifikat muss von einer Zertifizierungsstelle ausgestellt sein, der die Computer vertrauen, die über sicheres LDAP eine Verbindung mit der Domäne herstellen. Hierbei kann es sich um eine öffentliche Zertifizierungsstelle oder um eine Unternehmenszertifizierungsstelle handeln, die von diesen Computern als vertrauenswürdig eingestuft wird.
* **Lebensdauer** : Das Zertifikat muss mindestens für die nächsten 3 bis 6 Monate gültig sein. Der Zugriff auf Ihre verwaltete Domäne über sicheres LDAP wird unterbrochen, wenn das Zertifikat abläuft.
* **Antragstellername**: Der Name des Antragstellers im Zertifikat muss Ihre verwaltete Domäne sein. Wenn der Name Ihrer Domäne z. B. *aaddscontoso.com* lautet, muss als Antragstellername im Zertifikat * *.aaddscontoso.com* angegeben sein.
    * Der DNS-Name oder alternative Antragstellername des Zertifikats muss ein Platzhalterzertifikat sein, um sicherzustellen, dass Secure LDAP ordnungsgemäß mit den Azure AD Domain Services funktioniert. Domänencontroller verwenden zufällig vergebene Namen und können entfernt oder hinzugefügt werden, um sicherzustellen, dass der Dienst verfügbar bleibt.
* **Schlüsselverwendung**: Das Zertifikat muss für *digitale Signaturen* und *Schlüsselverschlüsselung* konfiguriert sein.
* **Zertifikatzweck** : Das Zertifikat muss für die TLS-Serverauthentifizierung gültig sein.

Es sind verschiedene Tools verfügbar, mit denen ein selbstsigniertes Zertifikat erstellt werden kann, z. B. OpenSSL, Keytool, MakeCert, Cmdlet [New-SelfSignedCertificate][New-SelfSignedCertificate] usw.

In diesem Tutorial erstellen Sie mithilfe des Cmdlets [New-SelfSignedCertificate][New-SelfSignedCertificate] ein selbstsigniertes Zertifikat für Secure LDAP.

Öffnen Sie ein PowerShell-Fenster als **Administrator**, und führen Sie die folgenden Befehle aus. Ersetzen Sie die Variable *$dnsName* durch den DNS-Namen, der von Ihrer verwalteten Domäne verwendet wird (z. B. *aaddscontoso.com*):

```powershell
# Define your own DNS name used by your managed domain
$dnsName="aaddscontoso.com"

# Get the current date to set a one-year expiration
$lifetime=Get-Date

# Create a self-signed certificate for use with Azure AD DS
New-SelfSignedCertificate -Subject *.$dnsName `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.$dnsName, $dnsName
```

Die folgende Beispielausgabe zeigt, dass das Zertifikat erfolgreich generiert wurde und im lokalen Zertifikatspeicher (*LocalMachine\MY*) gespeichert wird:

```output
PS C:\WINDOWS\system32> New-SelfSignedCertificate -Subject *.$dnsName `
>>   -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
>>   -Type SSLServerAuthentication -DnsName *.$dnsName, $dnsName.com

   PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\MY

Thumbprint                                Subject
----------                                -------
959BD1531A1E674EB09E13BD8534B2C76A45B3E6  CN=aaddscontoso.com
```

## <a name="understand-and-export-required-certificates"></a>Verstehen und Exportieren von erforderlichen Zertifikaten

Zur Verwendung von Secure LDAP wird der Netzwerkdatenverkehr mithilfe einer Public Key-Infrastruktur (PKI) verschlüsselt.

* Ein **privater** Schlüssel wird auf die verwaltete Domäne angewendet.
    * Mit diesem privaten Schlüssel wird der Datenverkehr über Secure LDAP *entschlüsselt*. Der private Schlüssel sollte nur auf die verwaltete Domäne angewendet und nicht auf Clientcomputer verteilt werden.
    * Ein Zertifikat, das den privaten Schlüssel enthält, verwendet das Dateiformat *PFX*.
    * Als Verschlüsselungsalgorithmus für das Zertifikat muss *TripleDES-SHA1* verwendet werden.
* Ein **öffentlicher** Schlüssel wird auf die Clientcomputer angewendet.
    * Mit diesem öffentlichen Schlüssel wird der Datenverkehr über Secure LDAP *verschlüsselt*. Der öffentliche Schlüssel kann auf Clientcomputer verteilt werden.
    * Zertifikate ohne privaten Schlüssel verwenden das Dateiformat *CER*.

Diese beiden Schlüssel, der *private* und der *öffentliche*, stellen sicher, dass nur geeignete Computer erfolgreich miteinander kommunizieren können. Wenn Sie eine öffentliche Zertifizierungsstelle oder eine Unternehmenszertifizierungsstelle verwenden, wird Ihnen ein Zertifikat ausgestellt, das den privaten Schlüssel enthält und auf eine verwaltete Domäne angewendet werden kann. Der öffentliche Schlüssel sollte den Clientcomputern bereits bekannt sein und von diesen als vertrauenswürdig eingestuft werden.

In diesen Tutorial haben Sie ein selbstsigniertes Zertifikat mit dem privaten Schlüssel erstellt, daher müssen Sie die entsprechenden privaten und öffentlichen Komponenten exportieren.

### <a name="export-a-certificate-for-azure-ad-ds"></a>Exportieren eines Zertifikats für Azure AD DS

Bevor Sie das im vorherigen Schritt erstellte digitale Zertifikat in Ihrer verwalteten Domäne verwenden können, müssen Sie das Zertifikat in eine *PFX*-Zertifikatdatei exportieren, die den privaten Schlüssel enthält.

1. Um das Dialogfeld *Ausführen* zu öffnen, drücken Sie **Windows** + **R**.
1. Öffnen Sie eine Microsoft Management Console (MMC), indem Sie im Dialogfeld *Ausführen* die Zeichenfolge **mmc** eingeben und auf **OK** klicken.
1. Wählen Sie an der Eingabeaufforderung der **Benutzerkontensteuerung** die Option **Ja** aus, um die MMC als Administrator zu starten.
1. Wählen Sie im Menü **Datei** die Option **Snap-In hinzufügen/entfernen...** aus.
1. Wählen Sie im Assistenten für das **Zertifikat-Snap-In** die Option **Computerkonto** aus, und klicken Sie auf **Weiter**.
1. Wählen Sie auf der Seite **Computer auswählen** die Option **Lokaler Computer: (Computer, auf dem diese Konsole ausgeführt wird)** aus, und klicken Sie auf **Fertig stellen**.
1. Wählen Sie im Dialogfeld **Snap-Ins hinzufügen/entfernen** auf **OK**, um der MMC das Zertifikat-Snap-In hinzuzufügen.
1. Erweitern Sie im MMC-Fenster den **Konsolenstamm**. Wählen Sie **Zertifikate (Lokaler Computer)** aus, und erweitern Sie nacheinander die Knoten **Eigene Zertifikate** und **Zertifikate**.

    ![Öffnen des Speichers mit eigenen Zertifikaten in der Microsoft Management Console](./media/tutorial-configure-ldaps/open-personal-store.png)

1. Das im vorherigen Schritt erstellte selbstsignierte Zertifikat wird angezeigt (z. B. *aaddscontoso.com*). Klicken Sie mit der rechten Maustaste auf dieses Zertifikat, und wählen Sie **Alle Aufgaben > Exportieren...** aus.

    ![Exportieren eines Zertifikats in der Microsoft Management Console](./media/tutorial-configure-ldaps/export-cert.png)

1. Klicken Sie im **Zertifikatexport-Assistenten** auf **Weiter**.
1. Der private Schlüssel für das Zertifikat muss exportiert werden. Wenn der private Schlüssel nicht im exportierten Zertifikat enthalten ist, tritt bei der Aktion zum Aktivieren von Secure LDAP für Ihre verwaltete Domäne ein Fehler auf.

    Wählen Sie auf der Seite **Privaten Schlüssel exportieren** die Option **Ja, privaten Schlüssel exportieren** aus, und klicken Sie auf **Weiter**.
1. Verwaltete Domänen unterstützen nur das Zertifikatdateiformat *PFX*, das den privaten Schlüssel enthält. Exportieren Sie das Zertifikat nicht im Dateiformat *CER* ohne den privaten Schlüssel.

    Wählen Sie auf der Seite **Format der zu exportierenden Datei** als Dateiformat für das zu exportierende Zertifikat die Option **Privater Informationsaustausch – PKCS #12 (.PFX)** aus. Aktivieren Sie das Kontrollkästchen *Wenn möglich, alle Zertifikate im Zertifizierungspfad einbeziehen*:

    ![Auswählen der Option zum Exportieren des Zertifikats im PKCS 12-Dateiformat (PFX)](./media/tutorial-configure-ldaps/export-cert-to-pfx.png)

1. Da dieses Zertifikat zum Entschlüsseln von Daten verwendet wird, sollten Sie den Zugriff sorgfältig steuern. Zum Schutz des Zertifikats kann ein Kennwort verwendet werden. Ohne das richtige Kennwort kann das Zertifikat nicht auf einen Dienst angewendet werden.

    Wählen Sie auf der Seite **Sicherheit** die Option **Kennwort** aus, um die *PFX*-Zertifikatdatei zu schützen. Als Verschlüsselungsalgorithmus muss *TripleDES-SHA1* verwendet werden. Geben Sie ein Kennwort ein, bestätigen Sie es, und klicken Sie auf **Weiter**. Dieses Kennwort wird im nächsten Abschnitt zum Aktivieren von Secure LDAP für Ihre verwaltete Domäne verwendet.
1. Geben Sie auf der Seite **Zu exportierende Datei** den Dateinamen und den Speicherort für den Export des Zertifikats an, z. B. *C:\Benutzer\Kontoname\azure-ad-ds.pfx*. Notieren Sie sich das Kennwort und den Speicherort der *PFX*-Datei, da Sie diese Informationen in den nächsten Schritten benötigen.
1. Klicken Sie auf der Überprüfungsseite auf **Fertig stellen**, um das Zertifikat in eine *PFX*-Zertifikatdatei zu exportieren. Wenn das Zertifikat erfolgreich exportiert wurde, wird ein Bestätigungsdialogfeld angezeigt.
1. Lassen Sie die MMC für den nächsten Abschnitt geöffnet.

### <a name="export-a-certificate-for-client-computers"></a>Exportieren eines Zertifikats für Clientcomputer

Clientcomputer müssen dem Aussteller des Secure LDAP-Zertifikats vertrauen, damit die Verbindung mit der verwalteten Domäne über LDAPS erfolgreich hergestellt werden kann. Die Clientcomputer benötigen ein Zertifikat, um Daten, die von Azure AD DS verschlüsselt wurden, erfolgreich zu entschlüsseln. Wenn Sie eine öffentliche Zertifizierungsstelle verwenden, muss der Computer diesen Zertifikatausstellern automatisch vertrauen und über ein entsprechendes Zertifikat verfügen.

In diesem Tutorial verwenden Sie ein selbstsigniertes Zertifikat. Im vorherigen Schritt haben Sie ein Zertifikat generiert, das den privaten Schlüssel enthält. Jetzt exportieren Sie das selbstsignierte Zertifikat und installieren es dann im Speicher für vertrauenswürdige Zertifikate auf den Clientcomputern:

1. Navigieren Sie in der MMC zurück zu *Zertifikate (Lokaler Computer) > Eigene Zertifikate > Zertifikate*. Das zuvor erstellte selbstsignierte Zertifikat wird angezeigt (z. B. *aaddscontoso.com*). Klicken Sie mit der rechten Maustaste auf dieses Zertifikat, und wählen Sie **Alle Aufgaben > Exportieren...** aus.
1. Klicken Sie im **Zertifikatexport-Assistenten** auf **Weiter**.
1. Da Sie den privaten Schlüssel für Clients nicht benötigen, wählen Sie auf der Seite **Privaten Schlüssel exportieren** die Option **Nein, privaten Schlüssel nicht exportieren** aus und klicken dann auf **Weiter**.
1. Wählen Sie auf der Seite **Format der zu exportierenden Datei** als Dateiformat für das exportierte Zertifikat die Option **Base-64-codiert X.509 (.CER)** aus:

    ![Auswählen der Option zum Exportieren des Zertifikats im Dateiformat „Base-64-codiert X.509 (.CER)“](./media/tutorial-configure-ldaps/export-cert-to-cer-file.png)

1. Geben Sie auf der Seite **Zu exportierende Datei** den Dateinamen und den Speicherort für den Export des Zertifikats an, z. B. *C:\Benutzer\Kontoname\azure-ad-ds-client.cer*.
1. Klicken Sie auf der Überprüfungsseite auf **Fertig stellen**, um das Zertifikat in eine *CER*-Zertifikatdatei zu exportieren. Wenn das Zertifikat erfolgreich exportiert wurde, wird ein Bestätigungsdialogfeld angezeigt.

Die *CER*-Zertifikatdatei kann jetzt auf Clientcomputer verteilt werden, die der Secure LDAP-Verbindung mit der verwalteten Domäne vertrauen müssen. Installieren Sie das Zertifikat jetzt auf dem lokalen Computer.

1. Öffnen Sie den Datei-Explorer, und navigieren Sie zu dem Speicherort, an dem Sie die *CER*-Zertifikatdatei gespeichert haben, z. B. *C:\Benutzer\Kontoname\azure-ad-ds-client.cer*.
1. Klicken Sie mit der Maustaste auf die *CER*-Zertifikatdatei, und wählen Sie **Zertifikat installieren** aus.
1. Wählen Sie im **Zertifikatimport-Assistenten** die Option *Lokaler Computer* zum Speichern des Zertifikats aus, und klicken Sie dann auf **Weiter**:

    ![Auswählen der Option zum Importieren des Zertifikats in den Speicher des lokalen Computers](./media/tutorial-configure-ldaps/import-cer-file.png)

1. Klicken Sie bei der entsprechenden Aufforderung auf **Ja**, um dem Computer das Vornehmen von Änderungen zu gestatten.
1. Wählen Sie die Option **Zertifikatspeicher automatisch auswählen (auf dem Zertifikattyp basierend)** aus, und klicken Sie dann auf **Weiter**.
1. Klicken Sie auf der Überprüfungsseite auf **Fertig stellen**, um die *CER*-Zertifikatdatei zu importieren. Wenn das Zertifikat erfolgreich importiert wurde, wird ein Bestätigungsdialogfeld angezeigt.

## <a name="enable-secure-ldap-for-azure-ad-ds"></a>Aktivieren von Secure LDAP für Azure AD DS

Sie haben ein digitales Zertifikat erstellt und exportiert, das den privaten Schlüssel enthält, und Sie haben den Clientcomputer so eingerichtet, dass er der Verbindung vertraut. Jetzt aktivieren Sie Secure LDAP für Ihre verwaltete Domäne. Führen Sie die folgenden Konfigurationsschritte aus, um Secure LDAP für eine verwaltete Domäne zu aktivieren:

1. Geben Sie im [Azure-Portal](https://portal.azure.com) im Feld **Ressourcen suchen** den Begriff *Domänendienste* ein. Wählen Sie aus dem Suchergebnis **Azure AD Domain Services** aus.
1. Wählen Sie Ihre verwaltete Domäne (z. B. *aaddscontoso.com*) aus.
1. Wählen Sie auf der linken Seite des Azure AD DS-Fensters die Option **Secure LDAP** aus.
1. Standardmäßig ist der sichere LDAP-Zugriff auf Ihre verwaltete Domäne deaktiviert. Ändern Sie die Einstellung für **Secure LDAP** in **Aktivieren**.
1. Der Secure LDAP-Zugriff auf Ihre verwaltete Domäne über das Internet ist standardmäßig deaktiviert. Wenn Sie den öffentlichen Secure LDAP-Zugriff aktivieren, ist Ihre Domäne anfällig für Brute-Force-Kennwortangriffe aus dem Internet. Im nächsten Schritt konfigurieren Sie eine Netzwerksicherheitsgruppe, um den Zugriff auf die erforderlichen IP-Quelladressbereiche zu beschränken.

    Ändern Sie die Einstellung für **Sicheren LDAP-Zugriff über das Internet aktivieren** in **Aktivieren**.

1. Klicken Sie auf das Ordnersymbol neben der **PFX-Datei mit Secure LDAP-Zertifikat**. Navigieren Sie zum Pfad der *PFX*-Datei, und wählen Sie das in einem vorherigen Schritt erstellte Zertifikat aus, das den privaten Schlüssel enthält.

    > [!IMPORTANT]
    > Wie oben im Abschnitt mit den Zertifikatanforderungen erwähnt, können Sie mit der standardmäßigen Domäne *.onmicrosoft.com* kein Zertifikat von einer öffentlichen Zertifizierungsstelle verwenden. Die Domäne *.onmicrosoft.com* ist im Besitz von Microsoft, daher stellt keine öffentliche Zertifizierungsstelle ein Zertifikat aus.
    >
    > Stellen Sie sicher, dass Ihr Zertifikat im geeigneten Format vorliegt. Andernfalls generiert die Azure-Plattform Zertifikatüberprüfungsfehler, wenn Sie Secure LDAP aktivieren.

1. Geben Sie das **Kennwort zum Entschlüsseln der PFX-Datei** ein, das Sie in einem vorherigen Schritt beim Exportieren des Zertifikats in eine *PFX*-Datei festgelegt haben.
1. Klicken Sie auf **Speichern**, um Secure LDAP zu aktivieren.

    ![Aktivieren von Secure LDAP für eine verwaltete Domäne im Azure-Portal](./media/tutorial-configure-ldaps/enable-ldaps.png)

Sie werden in einer Benachrichtigung darüber informiert, dass Secure LDAP für die verwaltete Domäne konfiguriert wird. Solange dieser Vorgang nicht abgeschlossen ist, können Sie keine anderen Einstellungen für die verwaltete Domäne ändern.

Es dauert einige Minuten, bis Secure LDAP für Ihre verwaltete Domäne aktiviert ist. Wenn das von Ihnen bereitgestellte Secure LDAP-Zertifikat die erforderlichen Kriterien nicht erfüllt, tritt beim Aktivieren von Secure LDAP für die verwaltete Domäne ein Fehler auf.

Häufige Gründe für Fehler sind: Der Domänenname ist falsch, als Verschlüsselungsalgorithmus für das Zertifikat ist nicht *TripleDES-SHA1* festgelegt, oder das Zertifikat läuft bald ab oder ist bereits abgelaufen. Sie können das Zertifikat mit gültigen Parametern erneut erstellen und dann Secure LDAP mit diesem aktualisierten Zertifikat aktivieren.

## <a name="lock-down-secure-ldap-access-over-the-internet"></a>Beschränken des Secure LDAP-Zugriffs über das Internet

Wenn Sie Secure LDAP-Zugriff auf Ihre verwaltete Domäne über das Internet zulassen, entsteht ein Sicherheitsrisiko. Auf die verwaltete Domäne kann aus dem Internet über TCP-Port 636 zugegriffen werden. Es empfiehlt sich, den Zugriff auf die verwaltete Domäne auf bestimmte bekannte IP-Adressen für Ihre Umgebung zu beschränken. Zum Beschränken des Zugriffs auf Secure LDAP kann eine Azure-Netzwerksicherheitsgruppen-Regel verwendet werden.

Erstellen Sie jetzt eine Regel, um eingehenden Secure LDAP-Zugriff über TCP-Port 636 nur für eine angegebene Gruppe von IP-Adressen zuzulassen. Auf den gesamten restlichen eingehenden Datenverkehr aus dem Internet wird eine *DenyAll*-Standardregel mit niedrigerer Priorität angewendet, sodass nur die angegebenen Adressen Ihre verwaltete Domäne über Secure LDAP erreichen können.

1. Wählen Sie im Azure-Portal im linken Navigationsbereich *Ressourcengruppen* aus.
1. Wählen Sie Ihre Ressourcengruppe (z. B. *myResourceGroup*) und Ihre Netzwerksicherheitsgruppe (z. B. *aaads-nsg*) aus.
1. Die Liste der vorhandenen Sicherheitsregeln für eingehenden und ausgehenden Datenverkehr wird angezeigt. Wählen Sie auf der linken Seite des Fensters „Netzwerksicherheitsgruppe“ die Optionen **Einstellungen > Eingangssicherheitsregeln** aus.
1. Klicken Sie auf **Hinzufügen**, und erstellen Sie eine Regel zum Zulassen von *TCP*-Port *636*. Wählen Sie zur Verbesserung der Sicherheit *IP-Adressen* als Quelle aus, und geben Sie die eigene gültige IP-Adresse oder den eigenen gültigen IP-Adressbereich für Ihre Organisation an.

    | Einstellung                           | Wert        |
    |-----------------------------------|--------------|
    | `Source`                            | IP-Adressen |
    | IP-Quelladressen/CIDR-Bereiche | Eine gültige IP-Adresse oder ein gültiger IP-Adressbereich für Ihre Umgebung |
    | Source port ranges                | *            |
    | Destination                       | Any          |
    | Zielportbereiche           | 636          |
    | Protocol                          | TCP          |
    | Aktion                            | Zulassen        |
    | Priority                          | 401          |
    | Name                              | AllowLDAPS   |

1. Wenn Sie fertig sind, klicken Sie auf **Hinzufügen**, um die Regel zu speichern und anzuwenden.

    ![Erstellen einer Netzwerksicherheitsgruppen-Regel für den Secure LDAP-Zugriff über das Internet](./media/tutorial-configure-ldaps/create-inbound-nsg-rule-for-ldaps.png)

## <a name="configure-dns-zone-for-external-access"></a>Konfigurieren einer DNS-Zone für den externen Zugriff

Wenn der Secure LDAP-Zugriff über das Internet aktiviert wurde, aktualisieren Sie die DNS-Zone, damit die Clientcomputer die verwaltete Domäne finden können. Die *Externe Secure LDAP-IP-Adresse* wird auf der Registerkarte **Eigenschaften** für Ihre verwaltete Domäne aufgeführt:

![Anzeigen der externen Secure LDAP-IP-Adresse für Ihre verwaltete Domäne im Azure-Portal](./media/tutorial-configure-ldaps/ldaps-external-ip-address.png)

Konfigurieren Sie Ihren externen DNS-Anbieter, und erstellen Sie einen Hosteintrag wie z. B. *ldaps*, um diesen in diese externe IP-Adresse aufzulösen. Um dieses Szenario zuerst auf Ihrem Computer zu testen, können Sie einen Eintrag in der Windows-Datei „hosts“ erstellen. Zum Bearbeiten der Datei „hosts“ auf Ihrem lokalen Computer öffnen Sie den *Editor* als Administrator. Dort öffnen Sie die hosts-Datei im Ordner *C:\Windows\System32\drivers\etc\hosts*.

Durch den folgenden DNS-Beispieleintrag (entweder bei Ihrem externen DNS-Anbieter oder in der lokalen Datei „hosts“) wird Datenverkehr für *ldaps.aaddscontoso.com* in die externe IP-Adresse *168.62.205.103* aufgelöst:

```
168.62.205.103    ldaps.aaddscontoso.com
```

## <a name="test-queries-to-the-managed-domain"></a>Testen von Abfragen in der verwalteten Domäne

Zum Herstellen von Verbindungen und Bindungen mit Ihrer verwalteten Domäne sowie für Suchvorgänge über LDAP verwenden Sie das Tool *LDP.exe*. Dieses Tool ist im RSAT-Paket (Remote Server Administration Tools, Remoteserver-Verwaltungstools) enthalten. Weitere Informationen finden Sie unter [Installieren der Remoteserver-Verwaltungstools][rsat].

1. Öffnen Sie *LDP.exe*, und stellen Sie eine Verbindung mit der verwalteten Domäne her. Wählen Sie **Verbindung** und dann **Verbinden...** aus.
1. Geben Sie den DNS-Domänennamen Ihrer verwalteten Domäne für Secure LDAP ein, den Sie im vorherigen Schritt erstellt haben (z. B. *ldaps.aaddscontoso.com*). Um Secure LDAP zu verwenden, legen Sie den **Port** auf *636* fest und aktivieren das Kontrollkästchen für **SSL**.
1. Klicken Sie auf **OK**, um eine Verbindung mit der verwalteten Domäne herzustellen.

Als Nächstes erstellen Sie eine Bindung mit Ihrer verwalteten Domäne. Benutzer (und Dienstkonten) können keine einfachen LDAP-Bindungen ausführen, wenn Sie die NTLM-Kennworthashsynchronisierung für Ihre verwaltete Domäne deaktiviert haben. Weitere Informationen zum Deaktivieren der NTLM-Kennworthashsynchronisierung finden Sie unter [Schützen Ihrer verwalteten Domäne][secure-domain].

1. Wählen Sie die Menüoption **Verbindung** und dann die Option **Binden...** aus.
1. Geben Sie die Anmeldeinformationen eines Benutzerkontos an, das zur verwalteten Domäne gehört. Geben Sie das Kennwort des Benutzerkontos und dann Ihre Domäne (z. B. *aaddscontoso.com*) ein.
1. Wählen Sie als **Bindungstyp** die Option *Bindung mit Anmeldeinformationen* aus.
1. Wählen Sie **OK** aus, um die Bindung mit Ihrer verwalteten Domäne zu erstellen.

Gehen Sie wie folgt vor, um die in Ihrer verwalteten Domäne gespeicherten Objekte anzuzeigen:

1. Wählen Sie die Menüoption **Ansicht** aus, und klicken Sie dann auf **Struktur**.
1. Lassen Sie das Feld *BaseDN* leer, und klicken Sie auf **OK**.
1. Wählen Sie einen Container aus, z. B. *AADDC Users*, klicken Sie mit der rechten Maustaste auf den Container, und wählen Sie **Suchen** aus.
1. Lassen Sie die vorab ausgefüllten Felder unverändert, und klicken Sie auf **Ausführen**. Die Ergebnisse der Abfrage werden im rechten Fenster angezeigt, wie in der folgenden Beispielausgabe gezeigt:

    ![Suchen nach Objekten in Ihrer verwalteten Domäne mit „LDP.exe“](./media/tutorial-configure-ldaps/ldp-query.png)

Wenn Sie einen bestimmten Container direkt abfragen möchten, können Sie im Menü **Ansicht > Struktur** einen Wert vom Typ **BaseDN** angeben (z. B. *OU=AADDC Users,DC=AADDSCONTOSO,DC=COM* oder *OU=AADDC Computers,DC=AADDSCONTOSO,DC=COM*). Weitere Informationen zum Formatieren und Erstellen von Abfragen finden Sie unter [Grundlagen zu LDAP-Abfragen][ldap-query-basics].

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie der lokalen Datei „hosts“ auf Ihrem Computer einen DNS-Eintrag hinzugefügt haben, um für dieses Tutorial die Konnektivität zu testen, entfernen Sie diesen Eintrag, und fügen Sie einen formalen Eintrag in Ihrer DNS-Zone hinzu. Um den Eintrag aus der lokalen hosts-Datei zu entfernen, gehen Sie folgendermaßen vor:

1. Öffnen Sie den *Editor* auf Ihrem lokalen Computer als Administrator.
1. Navigieren Sie zu *C:\Windows\System32\drivers\etc\hosts*, und öffnen Sie die Datei.
1. Löschen Sie die Zeile für den von Ihnen hinzugefügten Eintrag, z. B. `168.62.205.103    ldaps.aaddscontoso.com`.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen eines digitalen Zertifikats für die Verwendung mit Azure AD DS
> * Aktivieren von Secure LDAP für Azure AD DS
> * Konfigurieren von Secure LDAP für die Verwendung aus dem öffentlichen Internet
> * Binden und Testen von Secure LDAP für eine verwaltete Domäne

> [!div class="nextstepaction"]
> [Konfigurieren der Kennworthashsynchronisierung für eine Azure AD-Hybridumgebung](tutorial-configure-password-hash-sync.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[secure-domain]: secure-your-domain.md

<!-- EXTERNAL LINKS -->
[rsat]: /windows-server/remote/remote-server-administration-tools
[ldap-query-basics]: /windows/desktop/ad/creating-a-query-filter
[New-SelfSignedCertificate]: /powershell/module/pkiclient/new-selfsignedcertificate
