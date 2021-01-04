---
title: Hinzufügen und Verwalten von TLS-/SSL-Zertifikaten
description: Erstellen Sie ein kostenloses Zertifikat, importieren Sie ein App Service-Zertifikat, importieren Sie ein Key Vault-Zertifikat, oder erwerben Sie ein App Service-Zertifikat in Azure App Service.
tags: buy-ssl-certificates
ms.topic: tutorial
ms.date: 10/25/2019
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: dff98a5c54d2fee350e2b35dc00148c19ea233b8
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94956499"
---
# <a name="add-a-tlsssl-certificate-in-azure-app-service"></a>Hinzufügen eines TLS-/SSL-Zertifikats in Azure App Service

Von [Azure App Service](overview.md) wird ein hochgradig skalierbarer Webhostingdienst mit Self-Patching bereitgestellt. In diesem Artikel erfahren Sie, wie Sie ein privates oder öffentliches Zertifikat in App Service erstellen, hochladen oder importieren. 

Nach dem Hinzufügen eines Zertifikats zu Ihrer App Service- oder [Funktions-App](../azure-functions/index.yml) können Sie [damit einen benutzerdefinierten DNS-Namen schützen](configure-ssl-bindings.md) oder [es in Ihrem Anwendungscode verwenden](configure-ssl-certificate-in-code.md).

In der folgenden Tabelle sind die Optionen zum Hinzufügen von Zertifikaten in App Service aufgeführt:

|Option|BESCHREIBUNG|
|-|-|
| Erstellen eines von App Service verwalteten Zertifikats (Vorschau) | Ein privates Zertifikat, das einfach zu verwenden ist, wenn Sie nur Ihre [benutzerdefinierte `www`-Domäne](app-service-web-tutorial-custom-domain.md) oder eine nicht „nackte“ Domäne in App Service schützen müssen. |
| Erwerben eines App Service-Zertifikats | Ein von Azure verwaltetes privates Zertifikat. Es ermöglicht eine einfache automatisierte Zertifikatverwaltung und bietet flexible Verlängerungs- und Exportoptionen. |
| Importieren eines Zertifikats aus Key Vault | Diese Option ist nützlich, wenn Sie [Azure Key Vault](../key-vault/index.yml) für die Verwaltung Ihrer [PKCS12-Zertifikate](https://wikipedia.org/wiki/PKCS_12) verwenden. Siehe [Anforderungen an private Zertifikate](#private-certificate-requirements) |
| Hochladen eines privaten Zertifikats | Wenn Sie bereits über ein privates Zertifikat von einem Drittanbieter verfügen, können Sie es hochladen. Siehe [Anforderungen an private Zertifikate](#private-certificate-requirements) |
| Hochladen eines öffentlichen Zertifikats | Öffentliche Zertifikate werden nicht zum Schützen benutzerdefinierter Domänen verwendet. Sie können sie jedoch in Ihren Code laden, wenn sie für den Zugriff auf Remoteressourcen benötigt werden. |

## <a name="prerequisites"></a>Voraussetzungen

Im Rahmen dieser Schrittanleitung müssen Sie folgende Schritte durchführen:

- [Erstellen Sie eine App Service-App](./index.yml).
- Nur kostenloses Zertifikat: Ordnen Sie App Service eine Unterdomäne (z. B. `www.contoso.com`) mit einem [CNAME-Eintrag](app-service-web-tutorial-custom-domain.md#map-a-cname-record) zu.

## <a name="private-certificate-requirements"></a>Anforderungen an private Zertifikate

> [!NOTE]
> AES256 wird von Azure Web Apps **nicht** unterstützt, und alle PFX-Dateien müssen mit TripleDES verschlüsselt werden.

Das [von App Service verwaltete kostenlose Zertifikat](#create-a-free-certificate-preview) bzw. das [App Service-Zertifikat](#import-an-app-service-certificate) erfüllt bereits die Anforderungen von App Service. Wenn Sie ein privates Zertifikat in App Service hochladen oder importieren möchten, muss Ihr Zertifikat die folgenden Anforderungen erfüllen:

* Als [kennwortgeschützte PFX-Datei](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Certificate_filename_extensions) exportiert
* Enthält einen privaten Schlüssel mit mindestens 2048 Bit
* Enthält alle Zwischenzertifikate in der Zertifikatkette

Zum Schützen einer benutzerdefinierten Domäne in einer TLS-Bindung gelten für das Zertifikat zusätzliche Anforderungen:

* Beinhaltet [erweiterte Schlüsselverwendung](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Extensions_informing_a_specific_usage_of_a_certificate) für die Serverauthentifizierung (OID = 1.3.6.1.5.5.7.3.1).
* Von einer vertrauenswürdigen Zertifizierungsstelle signiert

> [!NOTE]
> **ECC-Zertifikate (Elliptic Curve Cryptography, Kryptografie für elliptische Kurven)** können mit App Service funktionieren, werden in diesem Artikel aber nicht behandelt. Erarbeiten Sie mit Ihrer Zertifizierungsstelle die einzelnen Schritte zum Erstellen von ECC-Zertifikaten.

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

## <a name="create-a-free-certificate-preview"></a>Erstellen eines kostenlosen Zertifikats (Vorschau)

Das von App Service verwaltete kostenlose Zertifikat ist eine vorgefertigte Lösung zum Schützen Ihres benutzerdefinierten DNS-Namens in App Service. Es handelt sich dabei um ein voll funktionsfähiges TLS-/SSL-Zertifikat, das von App Service verwaltet und automatisch verlängert wird. Für das kostenlose Zertifikat gelten die folgenden Einschränkungen:

- Platzhalterzertifikate werden nicht unterstützt.
- Es unterstützt keine „nackten“ Domänen.
- Es kann nicht exportiert werden.
- Es wird in einer App Service-Umgebung (App Service Environment, ASE) nicht unterstützt.
- A-Einträge werden nicht unterstützt. Beispielsweise funktioniert die automatische Verlängerung für A-Einträge nicht.

> [!NOTE]
> Das kostenlose Zertifikat wird von DigiCert ausgestellt. Bei einigen Domänen der obersten Ebene müssen Sie DigiCert explizit als Zertifikataussteller zulassen, indem Sie einen [CAA-Domäneneintrag](https://wikipedia.org/wiki/DNS_Certification_Authority_Authorization) (Certification Authority Authorization) mit dem folgenden Wert erstellen: `0 issue digicert.com`.
> 

So erstellen Sie ein von App Service verwaltetes Zertifikat:

Wählen Sie im <a href="https://portal.azure.com" target="_blank">Azure-Portal</a> im linken Menü **App Services** >  **\<app-name>** aus.

Wählen Sie im linken Navigationsbereich Ihrer App **TLS-/SSL-Einstellungen** > **Private Schlüsselzertifikate (PFX)**  > **Von App Service verwaltetes Zertifikat erstellen** aus.

![Erstellen eines kostenlosen Zertifikats in App Service](./media/configure-ssl-certificate/create-free-cert.png)

Im Dialogfeld sind alle nicht „nackten“ Domänen aufgeführt, die Ihrer App ordnungsgemäß mit einem CNAME-Eintrag zugeordnet sind. Wählen Sie die benutzerdefinierte Domäne aus, für die ein kostenloses Zertifikat erstellt werden soll, und wählen Sie dann **Erstellen** aus. Sie können nur ein Zertifikat für jede unterstützte benutzerdefinierte Domäne erstellen.

Nach Abschluss des Vorgangs wird das Zertifikat in der Liste **Private Schlüsselzertifikate** angezeigt.

![Erstellung eines kostenlosen Zertifikats abgeschlossen](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> Sie müssen noch eine Zertifikatsbindung erstellen, um eine benutzerdefinierte Domäne mit diesem Zertifikat zu schützen. Führen Sie die Schritte unter [Erstellen einer Bindung](configure-ssl-bindings.md#create-binding) aus.
>

## <a name="import-an-app-service-certificate"></a>Importieren eines App Service-Zertifikats

Wenn Sie ein App Service-Zertifikat von Azure erwerben, verwaltet Azure die folgenden Aufgaben:

- Abwickeln des Kaufs von GoDaddy
- Ausführen der Domänenüberprüfung des Zertifikats
- Speichern des Zertifikats in [Azure Key Vault](../key-vault/general/overview.md)
- Verwalten der Zertifikatsverlängerung (siehe [Verlängern des Zertifikats](#renew-certificate))
- Automatisches Synchronisieren des Zertifikats mit den importierten Kopien in App Service-Apps

Gehen Sie zu [Starten einer Zertifikatbestellung](#start-certificate-order), um ein App Service-Zertifikat zu erwerben.

Sie haben folgende Möglichkeiten, wenn Sie bereits ein funktionierendes App Service-Zertifikat besitzen:

- [Importieren des Zertifikats in App Service](#import-certificate-into-app-service)
- [Verwalten des Zertifikats](#manage-app-service-certificates), etwa Verlängern und Exportieren des Zertifikats sowie erneute Schlüsselerstellung für das Zertifikat
> [!NOTE]
> App Service-Zertifikate werden zurzeit nicht in nationalen Azure-Clouds unterstützt.

### <a name="start-certificate-order"></a>Starten einer Zertifikatreihenfolge

Starten Sie eine App Service-Zertifikatreihenfolge auf der <a href="https://portal.azure.com/#create/Microsoft.SSL" target="_blank">App Service Certificate-Seite zum Erstellen eines Zertifikats</a>.

![Starten des Kaufprozesses für ein App Service-Zertifikat](./media/configure-ssl-certificate/purchase-app-service-cert.png)

Die folgende Tabelle unterstützt Sie bei der Konfiguration des Zertifikats. Klicken Sie auf **Erstellen**, wenn Sie fertig sind.

| Einstellung | BESCHREIBUNG |
|-|-|
| Name | Ein Anzeigename für Ihr App Service-Zertifikat. |
| Reiner Domänenhostname | Geben Sie hier die Stammdomäne an. Das ausgestellte Zertifikat sichert *sowohl* die Stamm Domäne als auch die Unterdomäne `www`. Im ausgestellten Zertifikat enthält das Feld „Allgemeiner Name“ die Stammdomäne, und das Feld „Alternativer Antragstellername“ enthält die Domäne `www`. Um eine beliebige Unterdomäne zu sichern, geben Sie den vollqualifizierten Domänennamen der Unterdomäne hier an (z.B. `mysubdomain.contoso.com`).|
| Subscription | Das Abonnement, das das Zertifikat enthält |
| Resource group | Die Ressourcengruppe, die das Zertifikat enthält Sie können eine neue Ressourcengruppe verwenden oder z.B. die gleiche Ressourcengruppe wie die Ihrer App Service-App auswählen. |
| Zertifikat-SKU | Bestimmt den Typ des zu erstellenden Zertifikats, ganz gleich, ob es sich um ein Standardzertifikat oder ein [Platzhalterzertifikat](https://wikipedia.org/wiki/Wildcard_certificate) handelt. |
| Rechtliche Bedingungen | Klicken Sie auf diese Option, um zu bestätigen, dass Sie mit den rechtlichen Bedingungen einverstanden sind. Die Zertifikate werden von GoDaddy abgerufen. |

> [!NOTE]
> Von Azure erworbene App Service-Zertifikate werden von GoDaddy ausgestellt. Bei einigen Domänen der obersten Ebene müssen Sie GoDaddy explizit als Zertifikataussteller zulassen, indem Sie einen [CAA-Domäneneintrag](https://wikipedia.org/wiki/DNS_Certification_Authority_Authorization) (Certification Authority Authorization) mit dem folgenden Wert erstellen: `0 issue godaddy.com`.
> 

### <a name="store-in-azure-key-vault"></a>Speichern in Azure Key Vault

Sobald der Zertifikatkaufvorgang abgeschlossen ist, müssen Sie noch ein paar Schritte ausführen, bevor Sie das Zertifikat verwenden können. 

Wählen Sie das Zertifikat auf der Seite [App Service-Zertifikate](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) aus, und klicken Sie dann auf **Zertifikatkonfiguration** > **Schritt 1: Speichern**.

![Konfigurieren des Key Vault-Speichers eines App Service-Zertifikats](./media/configure-ssl-certificate/configure-key-vault.png)

[Key Vault](../key-vault/general/overview.md) ist ein Azure-Dienst zum Schutz von kryptografischen Schlüsseln und Geheimnissen, die von Cloudanwendungen und -diensten verwendet werden. Dies ist der ideale Speicher für App Service-Zertifikate.

Klicken Sie auf der Seite **Key Vault-Status** auf **Key Vault-Repository**, um einen neuen Tresor zu erstellen oder einen vorhandenen Tresor auszuwählen. Wenn Sie einen neuen Tresor erstellen möchten, konfigurieren Sie mithilfe der folgende Tabelle den Tresor, und klicken Sie auf „Erstellen“. Erstellen Sie die neue Key Vault-Instanz im gleichen Abonnement und in der gleichen Ressourcengruppe wie Ihre App Service-App.

| Einstellung | BESCHREIBUNG |
|-|-|
| Name | Ein eindeutiger Name aus alphanumerischen Zeichen und Bindestrichen. |
| Resource group | Es wird empfohlen, die gleiche Ressourcengruppe wie bei Ihrem App Service-Zertifikat auszuwählen. |
| Standort | Wählen Sie denselben Speicherort wie bei Ihrer App Service-App aus. |
| Tarif | Weitere Informationen finden Sie unter [Key Vault – Preise](https://azure.microsoft.com/pricing/details/key-vault/). |
| Zugriffsrichtlinien| Definiert die Anwendungen und den zulässigen Zugriff auf die Tresorressourcen. Sie können dies später konfigurieren, indem Sie die Schritte unter [Zuweisen einer Key Vault-Zugriffsrichtlinie im Azure-Portal](../key-vault/general/assign-access-policy-portal.md) durchführen. |
| Zugriff über virtuelles Netzwerk | Beschränkt den Tresorzugriff auf bestimmte virtuelle Azure-Netzwerke. Sie können dies später konfigurieren, indem Sie die Schritte unter [Konfigurieren von Azure Key Vault-Firewalls und virtuellen Netzwerken](../key-vault/general/network-security.md) durchführen. |

Wenn Sie den Tresor ausgewählt haben, schließen Sie die Seite **Key Vault-Repository**. Für die Option **Schritt 1: Speichern** sollte ein grünes Häkchen (erfolgreiche Ausführung) angezeigt werden. Lassen Sie die Seite für den nächsten Schritt geöffnet.

### <a name="verify-domain-ownership"></a>Überprüfen des Domänenbesitzes

Klicken Sie auf der Seite **Zertifikatkonfiguration**, die Sie im letzten Schritt verwendet haben, auf **Schritt 2: Überprüfen**.

![Überprüfen der Domäne für das App Service-Zertifikat](./media/configure-ssl-certificate/verify-domain.png)

Klicken Sie auf **App Service-Überprüfung**. Da Sie die Domäne bereits Ihrer Web-App zugeordnet (siehe [Voraussetzungen](#prerequisites)), wurde sie bereits überprüft. Klicken Sie einfach auf **Überprüfen**, um diesen Schritt abzuschließen. Klicken Sie auf die Schaltfläche **Aktualisieren**, bis die Meldung **Zertifikatdomäne wurde überprüft.** angezeigt wird.

> [!NOTE]
> Vier Arten von Domänenüberprüfungsmethoden werden unterstützt: 
> 
> - **App Service**: Die einfachste Option, wenn die Domäne bereits einer App Service-App im gleichen Abonnement zugeordnet ist. Sie nutzt die Tatsache aus, dass die App Service-App den Domänenbesitz bereits überprüft hat.
> - **Domäne**: Mit dieser Option wird eine [App Service-Domäne überprüft, die Sie von Azure erworben haben](manage-custom-dns-buy-domain.md). Azure fügt die TXT-Überprüfungseinträge automatisch für Sie hinzu und schließt den Vorgang ab.
> - **E-Mail**: Mit dieser Option wird die Domäne überprüft, indem Sie eine E-Mail an den Domänenadministrator senden. Anweisungen werden bei Auswahl der Option bereitgestellt.
> - **Manuell**: Hiermit wird die Domäne entweder mit einer HTML-Seite (nur **Standard**-Zertifikat) oder einem DNS-TXT-Eintrag überprüft. Anweisungen werden bei Auswahl der Option bereitgestellt.

### <a name="import-certificate-into-app-service"></a>Importieren des Zertifikats in App Service

Wählen Sie im <a href="https://portal.azure.com" target="_blank">Azure-Portal</a> im linken Menü **App Services** >  **\<app-name>** aus.

Wählen Sie im linken Navigationsbereich Ihrer App **TLS-/SSL-Einstellungen** > **Private Schlüsselzertifikate (PFX)**  > **App Service-Zertifikat importieren** aus.

![Importieren eines App Service-Zertifikats in App Service](./media/configure-ssl-certificate/import-app-service-cert.png)

Wählen Sie das Zertifikat aus, das Sie soeben gekauft haben, und wählen Sie dann **OK** aus.

Nach Abschluss des Vorgangs wird das Zertifikat in der Liste **Private Schlüsselzertifikate** angezeigt.

![Import des App Service-Zertifikats abgeschlossen](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!IMPORTANT] 
> Sie müssen noch eine Zertifikatsbindung erstellen, um eine benutzerdefinierte Domäne mit diesem Zertifikat zu schützen. Führen Sie die Schritte unter [Erstellen einer Bindung](configure-ssl-bindings.md#create-binding) aus.
>

## <a name="import-a-certificate-from-key-vault"></a>Importieren eines Zertifikats aus Key Vault

Wenn Sie Ihre Zertifikate mit Azure Key Vault verwalten, können Sie ein PKCS12-Zertifikat aus Key Vault in App Service importieren, sofern es die [Anforderungen erfüllt](#private-certificate-requirements).

### <a name="authorize-app-service-to-read-from-the-vault"></a>Autorisieren von App Service für das Lesen aus dem Tresor
Der App Service-Ressourcenanbieter hat standardmäßig keinen Zugriff auf den Schlüsseltresor. Damit Sie einen Schlüsseltresor für eine Zertifikatbereitstellung verwenden können, müssen Sie [den Lesezugriff des Ressourcenanbieters auf den Schlüsseltresor autorisieren](../key-vault/general/assign-access-policy-cli.md). 

`abfa0a7c-a6b6-4736-8310-5855508787cd` ist der Dienstprinzipalname des Ressourcenanbieters für App Service. Er lautet für alle Azure-Abonnements gleich. Verwenden Sie für die Azure Government-Cloudumgebung stattdessen `6a02c803-dafd-4136-b4c3-5a6f318b4714` als Dienstprinzipalnamen des Ressourcenanbieters.

### <a name="import-a-certificate-from-your-vault-to-your-app"></a>Importieren eines Zertifikats aus dem Tresor in Ihre App

Wählen Sie im <a href="https://portal.azure.com" target="_blank">Azure-Portal</a> im linken Menü **App Services** >  **\<app-name>** aus.

Wählen Sie im linken Navigationsbereich Ihrer App **TLS-/SSL-Einstellungen** > **Private Schlüsselzertifikate (PFX)**  > **Key Vault-Zertifikat importieren** aus.

![Importieren eines Key Vault-Zertifikats in App Service](./media/configure-ssl-certificate/import-key-vault-cert.png)

Die folgende Tabelle unterstützt Sie beim Auswählen des Zertifikats:

| Einstellung | BESCHREIBUNG |
|-|-|
| Subscription | Das Abonnement, zu dem die Key Vault-Instanz gehört |
| Key Vault | Der Tresor mit dem zu importierenden Zertifikat |
| Zertifikat | Wählen Sie in der Liste der PKCS12-Zertifikate im Tresor ein Zertifikat aus. Alle PKCS12-Zertifikate im Tresor sind mit ihrem Fingerabdruck aufgelistet, es werden jedoch nicht alle in App Service unterstützt. |

Nach Abschluss des Vorgangs wird das Zertifikat in der Liste **Private Schlüsselzertifikate** angezeigt. Tritt beim Importieren ein Fehler auf, erfüllt das Zertifikat nicht die [Anforderungen für App Service](#private-certificate-requirements).

![Import des Key Vault-Zertifikats abgeschlossen](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!NOTE]
> Wenn Sie Ihr Zertifikat in Key Vault mit einem neuen Zertifikat aktualisieren, synchronisiert App Service Ihr Zertifikat automatisch innerhalb von 48 Stunden.

> [!IMPORTANT] 
> Sie müssen noch eine Zertifikatsbindung erstellen, um eine benutzerdefinierte Domäne mit diesem Zertifikat zu schützen. Führen Sie die Schritte unter [Erstellen einer Bindung](configure-ssl-bindings.md#create-binding) aus.
>

## <a name="upload-a-private-certificate"></a>Hochladen eines privaten Zertifikats

Wenn Sie über ein Zertifikat von Ihrem Zertifikatanbieter verfügen, gehen Sie wie in diesem Abschnitt beschrieben vor, um es für App Service vorzubereiten.

### <a name="merge-intermediate-certificates"></a>Zusammenführen von Zwischenzertifikaten

Wenn Ihre Zertifizierungsstelle Ihnen mehrere Zertifikate in der Zertifikatskette bereitstellt, müssen Sie die Zertifikate nacheinander zusammenführen.

Öffnen Sie hierzu alle Zertifikate, die Sie erhalten haben, in einem Text-Editor.

Erstellen Sie eine Datei für das zusammengeführte Zertifikat mit dem Namen _mergedcertificate.crt_. Kopieren Sie den Inhalt der einzelnen Zertifikate in einem Text-Editor in diese Datei. Die Reihenfolge Ihrer Zertifikate sollte der Reihenfolge in der Zertifikatkette folgen, beginnend mit Ihrem Zertifikat und endend mit dem Stammzertifikat. Dies sieht in etwa wie im folgenden Beispiel aus:

```
-----BEGIN CERTIFICATE-----
<your entire Base64 encoded SSL certificate>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 1>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 2>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded root certificate>
-----END CERTIFICATE-----
```

### <a name="export-certificate-to-pfx"></a>Exportieren des Zertifikats als PFX-Datei

Exportieren Sie Ihr zusammengeführtes TLS-/SSL-Zertifikat mit dem privaten Schlüssel, mit dem Ihre Zertifikatanforderung generiert wurde.

Wenn Sie die Zertifikatanforderung mittels OpenSSL generiert haben, haben Sie eine Datei des privaten Schlüssels erstellt. Führen Sie folgenden Befehl aus, um Ihr Zertifikat nach PFX zu exportieren. Ersetzen Sie die Platzhalter _&lt;private-key-file>_ und _&lt;merged-certificate-file>_ mit den Pfaden zu Ihrem privaten Schlüssel und Ihrer zusammengeführten Zertifikatdatei.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>  
```

Definieren Sie ein Kennwort für den Export, wenn Sie dazu aufgefordert werden. Dieses Kennwort verwenden Sie, wenn Sie Ihr TLS-/SSL-Zertifikat zu einem späteren Zeitpunkt in App Service hochladen.

Wenn Sie IIS oder _Certreq.exe_ zum Generieren Ihrer Zertifikatanforderung verwendet haben, installieren Sie das Zertifikat auf dem lokalen Computer, und klicken Sie anschließend auf [Zertifikat nach PFX exportieren](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754329(v=ws.11)).

### <a name="upload-certificate-to-app-service"></a>Hochladen eines Zertifikats in App Service

Nun können Sie das Zertifikat in App Service hochladen.

Wählen Sie im <a href="https://portal.azure.com" target="_blank">Azure-Portal</a> im linken Menü **App Services** >  **\<app-name>** aus.

Wählen Sie im linken Navigationsbereich Ihrer App **TLS-/SSL-Einstellungen** > **Private Schlüsselzertifikate (PFX)**  > **Zertifikat hochladen** aus.

![Hochladen eines privaten Zertifikats in App Service](./media/configure-ssl-certificate/upload-private-cert.png)

Wählen Sie unter **PFX-Zertifikatdatei** Ihre PFX-Datei aus. Geben Sie unter **Zertifikatkennwort** das Kennwort ein, das Sie beim Exportieren der PFX-Datei erstellt haben. Klicken Sie abschließend auf **Hochladen**. 

Nach Abschluss des Vorgangs wird das Zertifikat in der Liste **Private Schlüsselzertifikate** angezeigt.

![Upload des Zertifikats abgeschlossen](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> Sie müssen noch eine Zertifikatsbindung erstellen, um eine benutzerdefinierte Domäne mit diesem Zertifikat zu schützen. Führen Sie die Schritte unter [Erstellen einer Bindung](configure-ssl-bindings.md#create-binding) aus.
>

## <a name="upload-a-public-certificate"></a>Hochladen eines öffentlichen Zertifikats

Öffentliche Zertifikate werden im Format *.cer*-Format unterstützt. 

Wählen Sie im <a href="https://portal.azure.com" target="_blank">Azure-Portal</a> im linken Menü **App Services** >  **\<app-name>** aus.

Klicken Sie im linken Navigationsbereich Ihrer App auf **TLS-/SSL-Einstellungen** > **Öffentliche Zertifikate (.cer)**  > **Öffentliches Schlüsselzertifikat hochladen**.

Geben Sie unter **Name** einen Namen für das Zertifikat ein. Wählen Sie unter **CER-Zertifikatdatei** Ihre CER-Datei aus.

Klicken Sie auf **Hochladen**.

![Hochladen eines öffentlichen Zertifikats in App Service](./media/configure-ssl-certificate/upload-public-cert.png)

Sobald das Zertifikat hochgeladen wurde, kopieren Sie den Zertifikatfingerabdruck, und [stellen Sie sicher, dass auf das Zertifikat zugegriffen werden kann](configure-ssl-certificate-in-code.md#make-the-certificate-accessible).

## <a name="manage-app-service-certificates"></a>Verwalten von App Service-Zertifikaten

In diesem Abschnitt wird gezeigt, wie Sie ein App Service-Zertifikat verwalten, das Sie unter [Importieren eines App Service-Zertifikats](#import-an-app-service-certificate) erworben haben.

- [Erstellen neuer Schlüssel für das Zertifikat](#rekey-certificate)
- [Verlängern eines Zertifikats](#renew-certificate)
- [Exportieren eines Zertifikats](#export-certificate)
- [Löschen eines Zertifikats](#delete-certificate)

### <a name="rekey-certificate"></a>Neue Schlüssel für Zertifikat erstellen

Wenn Sie vermuten, das der private Schlüssel Ihres Zertifikats gefährdet ist, können Sie neue Schlüssel für das Zertifikat erstellen. Wählen Sie das Zertifikat auf der Seite [App Service Certificate](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) aus, und klicken Sie dann im linken Navigationsbereich auf **Neuen Schlüssel erstellen und synchronisieren**.

Klicken Sie auf **Erneute Schlüsselerstellung**, um den Prozess zu starten. Dieser Prozess kann 1 bis 10 Minuten in Anspruch nehmen.

![Erstellen neuer Schlüssel für ein App Service-Zertifikat](./media/configure-ssl-certificate/rekey-app-service-cert.png)

Bei erneuter Schlüsselerstellung für Ihr Zertifikat wird von der Zertifizierungsstelle ein neues Zertifikat ausgestellt.

Wenn der Vorgang zur erneuten Schlüsselerstellung abgeschlossen ist, klicken Sie auf **Synchronisierung**. Der Synchronisierungsvorgang aktualisiert automatisch die Hostnamenbindungen für das Zertifikat in App Service, ohne dass es zu Downtime für Ihre Apps kommt.

> [!NOTE]
> Wenn Sie nicht auf **Synchronisierung** klicken, synchronisiert App Service Ihr Zertifikat innerhalb von 48 Stunden.

### <a name="renew-certificate"></a>Verlängern des Zertifikats

Um zu einem beliebigen Zeitpunkt die automatische Verlängerung Ihres Zertifikats zu aktivieren, wählen Sie das Zertifikat auf der Seite [App Service Certificate](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) aus, und klicken Sie dann im linken Navigationsbereich auf **Einstellungen für die automatische Verlängerung**. Standardmäßig haben App Service-Zertifikate eine Gültigkeitsdauer von einem Jahr.

Klicken Sie nacheinander auf **Ein** und **Speichern**. Zertifikate können vor Ablauf automatisch um 60 Tage verlängert werden, wenn Sie die automatische Verlängerung aktiviert haben.

![Automatisches Verlängern eines App Service-Zertifikats](./media/configure-ssl-certificate/auto-renew-app-service-cert.png)

Um das Zertifikat stattdessen manuell zu verlängern, klicken Sie auf **Manuelle Verlängerung**. Sie können anfordern, dass Ihr Zertifikat vor Ablauf manuell um 60 Tage verlängert wird.

Wenn der Verlängerungsvorgang abgeschlossen ist, klicken Sie auf **Synchronisierung**. Der Synchronisierungsvorgang aktualisiert automatisch die Hostnamenbindungen für das Zertifikat in App Service, ohne dass es zu Downtime für Ihre Apps kommt.

> [!NOTE]
> Wenn Sie nicht auf **Synchronisierung** klicken, synchronisiert App Service Ihr Zertifikat innerhalb von 48 Stunden.

### <a name="export-certificate"></a>Exportieren des Zertifikats

Bei einem App Service-Zertifikat handelt es sich um ein [Key Vault-Geheimnis](../key-vault/general/about-keys-secrets-certificates.md). Sie können daher eine PFX-Kopie davon exportieren und für andere Azure-Dienste oder außerhalb von Azure verwenden.

Führen Sie zum Exportieren des App Service-Zertifikats als PFX-Datei die folgenden Befehle in [Cloud Shell](https://shell.azure.com) aus. Sie können sie auch lokal ausführen, wenn Sie die [Azure CLI installiert](/cli/azure/install-azure-cli) haben. Ersetzen Sie die Platzhalter durch die Namen, die Sie beim [Erstellen des App Service-Zertifikats](#start-certificate-order) verwendet haben.

```azurecli-interactive
secretname=$(az resource show \
    --resource-group <group-name> \
    --resource-type "Microsoft.CertificateRegistration/certificateOrders" \
    --name <app-service-cert-name> \
    --query "properties.certificates.<app-service-cert-name>.keyVaultSecretName" \
    --output tsv)

az keyvault secret download \
    --file appservicecertificate.pfx \
    --vault-name <key-vault-name> \
    --name $secretname \
    --encoding base64
```

Die heruntergeladene Datei *appservicecertificate.pfx* ist eine PKCS12-Rohdatendatei, die sowohl das öffentliche als auch das private Zertifikat enthält. Verwenden Sie an jeder Eingabeaufforderung für das Importkennwort und die PEM-Passphrase eine leere Zeichenfolge.

### <a name="delete-certificate"></a>Löschen eines Zertifikats 

Das Löschen eines App Service-Zertifikats ist endgültig und kann nicht rückgängig gemacht werden. Wird eine App Service Certificate-Ressource gelöscht, wird das Zertifikat widerrufen. Alle Bindungen in App Service mit diesem Zertifikat werden ungültig. Azure sperrt das Zertifikat, um ein versehentliches Löschen zu verhindern. Wenn Sie ein App Service-Zertifikat löschen möchten, müssen Sie zunächst die Löschsperre des Zertifikats entfernen.

Wählen Sie das Zertifikat auf der Seite [App Service-Zertifikate](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) aus, und wählen Sie dann im linken Navigationsbereich **Sperren** aus.

Suchen Sie die Sperre für Ihr Zertifikat mit dem Sperrentyp **Löschen**. Wählen Sie rechts davon **Löschen** aus.

![Löschsperre für das App Service-Zertifikat](./media/configure-ssl-certificate/delete-lock-app-service-cert.png)

Nun können Sie das App Service-Zertifikat löschen. Wählen Sie im linken Navigationsbereich **Übersicht** > **Löschen** aus. Geben Sie im Bestätigungsdialogfeld den Zertifikatnamen ein, und wählen Sie **OK** aus.

## <a name="automate-with-scripts"></a>Automatisieren mit Skripts

### <a name="azure-cli"></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom TLS/SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom TLS/SSL certificate to a web app")]

## <a name="more-resources"></a>Weitere Ressourcen

* [Schützen eines benutzerdefinierten DNS-Namens mit einer TLS-/SSL-Bindung in Azure App Service](configure-ssl-bindings.md)
* [Erzwingen von HTTPS](configure-ssl-bindings.md#enforce-https)
* [Erzwingen von TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions)
* [Verwenden eines TLS-/SSL-Zertifikats in Ihrem Code in Azure App Service](configure-ssl-certificate-in-code.md)
* [Häufig gestellte Fragen: App Service-Zertifikate](./faq-configuration-and-management.md)
