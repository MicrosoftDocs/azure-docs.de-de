---
title: 'Häufig gestellte Fragen: Azure Key Vault-Zertifikatimport'
description: Enthält Antworten auf häufig gestellte Fragen zum Import von Azure Key Vault-Zertifikaten.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: e75786c42b54882c249b5ed7100ebd12f3f78e78
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99254358"
---
# <a name="importing-azure-key-vault-certificates-faq"></a>Importieren von Azure Key Vault-Zertifikaten: Häufig gestellte Fragen

Dieser Artikel enthält Antworten auf häufig gestellte Fragen zum Import von Azure Key Vault-Zertifikaten.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="how-can-i-import-a-certificate-in-azure-key-vault"></a>Wie kann ich ein Zertifikat in Azure Key Vault importieren?

Für den Vorgang zum Importieren von Zertifikaten akzeptiert Azure Key Vault zwei Zertifikatformate: PEM und PFX. Es gibt zwar auch PEM-Dateien, die nur den öffentlichen Teil enthalten, aber Key Vault benötigt und akzeptiert nur eine PEM- oder PFX-Datei mit einem privaten Schlüssel. Weitere Informationen finden Sie unter [Importieren eines Zertifikats in Key Vault](./tutorial-import-certificate.md#import-a-certificate-to-key-vault).

### <a name="after-i-import-a-password-protected-certificate-to-key-vault-and-then-download-it-why-cant-i-see-the-password-thats-associated-with-it"></a>Warum kann ich nach dem Importieren eines kennwortgeschützten Zertifikats in Key Vault und dem anschließenden Download das zugehörige Kennwort nicht sehen?
    
Nachdem ein Zertifikat in Key Vault importiert und geschützt wurde, wird das zugehörige Kennwort nicht mehr gespeichert. Das Kennwort wird nur einmal während des Importvorgangs benötigt. Dies ist so beabsichtigt, aber Sie können das Zertifikat immer als Geheimnis abrufen und aus Base64 in PFX konvertieren, indem Sie das Kennwort über [Azure PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx) hinzufügen.

### <a name="how-can-i-resolve-a-bad-parameter-error-what-are-the-supported-certificate-formats-for-importing-to-key-vault"></a>Wie kann ich einen Fehler vom Typ „Falscher Parameter“ beheben? Welche Zertifikatformate werden beim Importieren in Key Vault unterstützt?

Wenn Sie ein Zertifikat importieren, müssen Sie sicherstellen, dass der Schlüssel in der Datei enthalten ist. Falls Sie einen privaten Schlüssel in einem anderen Format gespeichert haben, müssen Sie den Schlüssel mit dem Zertifikat kombinieren. Einige Zertifizierungsstellen (ZS) stellen Zertifikate in anderen Formaten bereit. Stellen Sie vor dem Importieren des Zertifikats daher sicher, dass es im Dateiformat PEM oder PFX vorliegt und dass für den Schlüssel entweder die RSA- (Rivest-Shamir-Adleman) oder die ECC-Verschlüsselung (Elliptic Curve Cryptography) verwendet wird. 

Weitere Informationen finden Sie unter [Zertifikatanforderungen](./certificate-scenarios.md#formats-of-import-we-support) und [Kryptografischer Schutz](../keys/about-keys.md).

###  <a name="can-i-import-a-certificate-by-using-an-arm-template"></a>Kann ich ein Zertifikat mit einer ARM-Vorlage importieren?

Nein. Es ist nicht möglich, Zertifikatvorgänge mit einer ARM-Vorlage (Azure Resource Manager) durchzuführen. Eine empfohlene Problemumgehung ist die Verwendung der Methoden für den Zertifikatimport in der Azure API, in der Azure CLI oder in PowerShell. Wenn Sie über ein vorhandenes Zertifikat verfügen, können Sie es als Geheimnis importieren.

### <a name="when-i-import-a-certificate-via-the-azure-portal-i-get-a-something-went-wrong-error-how-can-i-investigate-further"></a>Wenn ich ein Zertifikat über das Azure-Portal importiere, tritt ein Fehler der Art „Es ist ein Problem aufgetreten“ auf. Wie kann ich weitere Untersuchungen durchführen?
    
Importieren Sie die Zertifikatsdatei mit der [Azure CLI](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-import) oder mit [PowerShell](/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate?view=azurermps-6.13.0), wenn Sie einen Fehler mit aussagekräftigeren Informationen anzeigen möchten.

### <a name="how-can-i-resolve-error-type-access-denied-or-user-is-unauthorized-to-import-certificate"></a>Wie kann ich den folgenden Fehlertyp beheben: „Access denied or user is unauthorized to import certificate“ (Zugriff verweigert, oder der Benutzer ist nicht autorisiert, das Zertifikat zu importieren)?
    
Für den Importvorgang müssen Sie die Benutzerberechtigungen zum Importieren des Zertifikats über die Zugriffsrichtlinien gewähren. Navigieren Sie hierfür zu Ihrem Schlüsseltresor, wählen Sie **Zugriffsrichtlinien** > **Zugriffsrichtlinie hinzufügen** > **Zertifikatberechtigungen auswählen** > **Prinzipal** aus, suchen Sie nach dem Benutzer, und fügen Sie anschließend die E-Mail-Adresse des Benutzers hinzu. 

Weitere Informationen zu zertifikatbezogenen Zugriffsrichtlinien finden Sie unter [Informationen zu Azure Key Vault-Zertifikaten](./about-certificates.md#certificate-access-control).


### <a name="how-can-i-resolve-error-type-conflict-when-creating-a-certificate"></a>Wie kann ich den folgenden Fehlertyp beheben: „Conflict when creating a certificate“ (Konflikt beim Erstellen eines Zertifikats)?
    
Jeder Zertifikatname muss eindeutig sein. Ein Zertifikat mit demselben Namen kann sich unter Umständen im vorläufig gelöschten Zustand befinden. Außerdem gilt basierend auf der [Zusammensetzung eines Zertifikats](./about-certificates.md#composition-of-a-certificate) Folgendes: Wenn ein neues Zertifikat erstellt wird, wird ein adressierbares Geheimnis mit demselben Namen erstellt. Wenn also im Schlüsseltresor ein weiterer Schlüssel oder ein Geheimnis mit demselben Namen enthalten ist, den oder das Sie für Ihr Zertifikat festlegen möchten, schlägt die Zertifikaterstellung fehl, und Sie müssen den Schlüssel oder das Geheimnis entweder entfernen oder einen anderen Namen für Ihr Zertifikat verwenden. 

Weitere Informationen finden Sie unter [Vorgang „GetDeletedCertificate“](/rest/api/keyvault/getdeletedcertificate/getdeletedcertificate).

### <a name="why-am-i-getting-error-type-char-length-is-too-long"></a>Warum erhalte ich den Fehlertyp „char length is too long“ (Zeichenlänge ist zu lang)?
Dieser Fehler kann aus zwei Gründen auftreten:    
* Der Name des Zertifikatantragstellers ist auf eine Länge von 200 Zeichen beschränkt.
* Das Zertifikatkennwort ist auf eine Länge von 200 Zeichen beschränkt.


### <a name="error-the-specified-pem-x509-certificate-content-is-in-an-unexpected-format-please-check-if-certificate-is-in-valid-pem-format"></a>Fehler: „Der Inhalt des angegebenen PEM-X.509-Zertifikats weist ein unerwartetes Format auf. Überprüfen Sie das Zertifikat auf Gültigkeit des PEM-Formats.“
Vergewissern Sie sich, dass der Inhalt der PEM-Datei Zeilentrennzeichen im UNIX-Stil (`(\n)`) verwendet.

### <a name="can-i-import-an-expired-certificate-to-azure-key-vault"></a>Kann ich ein abgelaufenes Zertifikat in Azure Key Vault importieren?
    
Nein. Abgelaufene PFX-Zertifikate können nicht in Key Vault importiert werden.

### <a name="how-can-i-convert-my-certificate-to-the-proper-format"></a>Wie kann ich mein Zertifikat in das richtige Format konvertieren?

Sie können bei Ihrer Zertifizierungsstelle darum bitten, dass das Zertifikat im gewünschten Format bereitgestellt wird. Es gibt auch Drittanbietertools, mit denen Sie das Zertifikat in das richtige Format konvertieren können.

### <a name="can-i-import-certificates-from-non-partner-cas"></a>Kann ich Zertifikate von Zertifizierungsstellen importieren, die keine Partner sind?
Ja. Sie können Zertifikate von beliebigen Zertifizierungsstellen importieren, aber diese können von Ihrem Schlüsseltresor nicht automatisch verlängert werden. Sie können Erinnerungen einrichten, damit Sie über den Ablauf des Zertifikats benachrichtigt werden.

### <a name="if-i-import-a-certificate-from-a-partner-ca-will-the-autorenewal-feature-still-work"></a>Funktioniert das Feature für die automatische Verlängerung auch, wenn ich ein Zertifikat von einer Partnerzertifizierungsstelle importiere?
Ja. Nachdem Sie das Zertifikat hochgeladen haben, müssen Sie die automatische Rotation in der Ausstellungsrichtlinie des Zertifikats angeben. Ihre Einstellungen bleiben so lange in Kraft, bis der nächste Zyklus beginnt oder die nächste Zertifikatversion veröffentlicht wird.

### <a name="why-cant-i-see-the-app-service-certificate-that-i-imported-to-key-vault"></a>Warum kann ich das App Service-Zertifikat nicht sehen, dass ich in Key Vault importiert habe? 
Wenn der Import des Zertifikats erfolgreich war, sollten Sie dies im Bereich **Geheimnisse** bestätigen können.


## <a name="next-steps"></a>Nächste Schritte

- [Azure Key Vault-Zertifikate](./about-certificates.md)
