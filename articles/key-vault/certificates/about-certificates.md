---
title: Informationen zu Azure Key Vault-Zertifikaten – Azure Key Vault
description: Hier finden Sie eine Übersicht über die Azure Key Vault-REST-Schnittstelle und Azure Key Vault-Zertifikate.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 66f077028b9f9f7a7644a318d4447eeaaab19e98
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94919929"
---
# <a name="about-azure-key-vault-certificates"></a>Informationen zu Azure Key Vault-Zertifikaten

Die Unterstützung von Key Vault-Zertifikaten ermöglicht die Verwaltung Ihrer x509-Zertifikate sowie folgende Szenarien:  

-   Ermöglicht einem Zertifikatbesitzer die Erstellung eines Zertifikats über einen Key Vault-Erstellungsvorgang oder durch den Import eines vorhandenen Zertifikats. Dies schließt sowohl selbstsignierte als auch von einer Zertifizierungsstelle generierte Zertifikate ein.
-   Ermöglicht dem Besitzer eines Key Vault-Zertifikats die Implementierung von sicherem Speicher sowie die Verwaltung von X509-Zertifikaten ohne Interaktion mit privaten Schlüsseln.  
-   Ermöglicht einem Zertifikatbesitzer die Erstellung einer Richtlinie, die Key Vault anweist, den Lebenszyklus eines Zertifikats zu verwalten.  
-   Ermöglicht Zertifikatbesitzern die Angabe von Kontaktinformationen zur Benachrichtigung über Lebenszyklusereignisse wie Ablauf und Verlängerung eines Zertifikats.  
-   Unterstützt die automatische Verlängerung mit dem ausgewählten Aussteller – Key Vault-Partneranbieter oder -Partnerzertifizierungsstellen für X509-Zertifikate.

>[!Note]
>Anbieter/Zertifizierungsstellen, mit denen keine Partnerschaft besteht, sind ebenfalls zulässig. Das Feature für die automatische Verlängerung wird von ihnen jedoch nicht unterstützt.

## <a name="composition-of-a-certificate"></a>Zusammensetzung eines Zertifikats

Wenn ein Key Vault-Zertifikat erstellt wird, werden auch ein adressierbarer Schlüssel und ein Geheimnis gleichen Namens erstellt. Der Key Vault-Schlüssel ermöglicht Schlüsselvorgänge, und das Key Vault-Geheimnis ermöglicht den Abruf des Zertifikatwerts als Geheimnis. Ein Key Vault-Zertifikat enthält auch öffentliche X509-Zertifikatmetadaten.  

Bezeichner und Version der Zertifikate entsprechen denen von Schlüsseln und Geheimnissen. Eine bestimmte Version eines mit der Key Vault-Zertifikatversion erstellten adressierbaren Schlüssels und Geheimnisses ist in der Antwort des Key Vault-Zertifikats verfügbar.
 
![Zertifikate sind komplexe Objekte](../media/azure-key-vault.png)

## <a name="exportable-or-non-exportable-key"></a>Exportierbarer oder nicht exportierbarer Schlüssel

Wenn ein Key Vault-Zertifikat erstellt wird, kann es aus dem adressierbaren Geheimnis mit dem privaten Schlüssel entweder im PFX- oder PEM-Format abgerufen werden. Die zum Erstellen des Zertifikats verwendete Richtlinie muss angeben, dass der Schlüssel exportierbar ist. Wenn die Richtlinie angibt, dass der Schlüssel nicht exportierbar ist, ist der private Schlüssel beim Abruf als Geheimnis kein Teil des Werts.  

Der adressierbare Schlüssel erhält bei nicht exportierbaren Key Vault-Zertifikaten höhere Relevanz. Die Vorgänge des adressierbaren Key Vault-Schlüssels werden vom Feld *keyusage* der Key Vault-Zertifikatrichtlinie zugeordnet, mit der das Key Vault-Zertifikat erstellt wird.  

Der Typ des Schlüsselpaars, das für Zertifikate unterstützt wird

 - Unterstützte Schlüsseltypen: RSA, RSA-HSM, EC, EC-HSM, oct ([hier](/rest/api/keyvault/createcertificate/createcertificate#jsonwebkeytype) aufgeführt). Exportierbare Schlüssel sind nur bei RSA und EC zulässig. HSM-Schlüssel sind nicht exportierbar.

|Schlüsseltyp|Info|Sicherheit|
|--|--|--|
|**RSA**| Softwaregeschützter RSA-Schlüssel|FIPS 140-2 Level 1|
|**RSA-HSM**| Durch HSM geschützter RSA-Schlüssel (nur Premium-SKU)|HSM mit FIPS 140-2 Level 2|
|**EC**| Softwaregeschützter Elliptic Curve-Schlüssel.|FIPS 140-2 Level 1|
|**EC-HSM**| Durch HSM geschützter Elliptic Curve-Schlüssel (nur Premium-SKU)|HSM mit FIPS 140-2 Level 2|
|||

## <a name="certificate-attributes-and-tags"></a>Zertifikatattribute und Tags

Außer den Zertifikatmetadaten, einem adressierbaren Schlüssel und einem adressierbaren Geheimnis enthält ein Key Vault-Zertifikat auch Attribute und Tags.  

### <a name="attributes"></a>Attributes

Die Zertifikatattribute werden in Attributen des adressierbaren Schlüssels und Geheimnisses gespiegelt, wenn ein Key Vault-Zertifikat erstellt wird.  

Ein Key Vault-Zertifikat weist folgende Attribute auf:  

-   *enabled*: Boolesch, optional, Standardwert ist **true**. Mit diesem Attribut kann angegeben werden, ob die Zertifikatdaten als Geheimnis oder als funktionsfähiger Schlüssel abgerufen werden können. Das Attribut wird auch in Verbindung mit *nbf* und *exp* verwendet, wenn ein Vorgang zwischen *nbf* und *exp* stattfindet. Der Vorgang wird nur zugelassen, wenn „enabled“ auf „true“ festgelegt ist. Vorgänge außerhalb des Fensters zwischen *nbf* und *exp* werden automatisch nicht zugelassen.  

Es gibt zusätzliche schreibgeschützte Attribute, die in die Antwort einbezogen werden:

-   *created*: IntDate: gibt an, wann diese Version des Zertifikats erstellt wurde  
-   *updated*: IntDate: gibt an, wann diese Version des Zertifikats aktualisiert wurde  
-   *exp*: IntDate: enthält den Wert des Ablaufdatums des X.509-Zertifikats  
-   *nbf*: IntDate: enthält den Wert des Datums des X.509-Zertifikats  

> [!Note] 
> Wenn ein Key Vault-Zertifikat abläuft, sind sein adressierbarer Schlüssel und sein Geheimnis nicht mehr funktionsfähig.  

### <a name="tags"></a>`Tags`

 Vom Client angegebenes Wörterbuch von Schlüssel-Wert-Paaren, die Tags in Schlüsseln und Geheimnissen ähneln.  

 > [!Note]
> Tags sind für Aufrufer lesbar, die über die *list*- oder *get*-Berechtigung für diesen Objekttyp (Schlüssel, Geheimnisse oder Zertifikate) verfügen.

## <a name="certificate-policy"></a>Zertifikatrichtlinie

Eine Zertifikatrichtlinie enthält Informationen zum Erstellen und Verwalten des Lebenszyklus eines Key Vault-Zertifikats. Wenn ein Zertifikat mit privatem Schlüssel in den Schlüsseltresor importiert wird, wird durch Lesen des X509-Zertifikats eine Standardrichtlinie erstellt.  

Wenn ein Key Vault-Zertifikat von Grund auf neu erstellt wird, muss eine Richtlinie angegeben werden. Die Richtlinie gibt an, wie diese oder die nächste Key Vault-Zertifikatversion erstellt werden soll. Nachdem eine Richtlinie eingerichtet wurde, ist bei nachfolgenden Erstellungsvorgängen für zukünftige Versionen keine weitere Richtlinie erforderlich. Es gibt nur eine Instanz einer Richtlinie für alle Versionen eines Key Vault-Zertifikats.  

Generell enthält eine Zertifikatsrichtlinie folgende Informationen (deren Definitionen finden Sie [hier](/powershell/module/az.keyvault/set-azkeyvaultcertificatepolicy?view=azps-4.4.0)):  

-   Eigenschaften des X.509-Zertifikats: Enthält den Namen und einen alternativen Namen des Antragstellers sowie weitere Eigenschaften zum Erstellen einer X.509-Zertifikatanforderung.  
-   Schlüsseleigenschaften: enthalten Schlüsseltyp, Schlüssellänge sowie Exportierbarkeits- und ReuseKeyOnRenewal-Felder. Diese Felder teilen Key Vault mit, wie ein Schlüssel generiert werden soll. 
     - Unterstützte Schlüsseltypen: RSA, RSA-HSM, EC, EC-HSM, oct ([hier](/rest/api/keyvault/createcertificate/createcertificate#jsonwebkeytype) aufgeführt) 
-   Geheimniseigenschaften: enthält Geheimniseigenschaften wie den Inhaltstyp eines adressierbaren Geheimnisses zum Generieren des Geheimniswerts, um das Zertifikat als Geheimnis abzurufen.  
-   Lebensdaueraktionen: enthält Lebensdaueraktionen für das Key Vault-Zertifikat. Jede Lebensdaueraktion enthält:  

     - Trigger: wird über Tage vor Ablauf oder Lebensdauerprozentsatz angegeben  

     - Aktion: Angabe des Aktionstyps – *emailContacts* oder *autoRenew*  

-   Aussteller: Parameter über den zum Ausstellen von X.509-Zertifikaten zu verwendenden Aussteller des Zertifikats.  
-   Richtlinienattribute: enthält Attribute, die der Richtlinie zugeordnet sind  

### <a name="x509-to-key-vault-usage-mapping"></a>Zuordnung von X509 zur Key Vault-Verwendung

In der folgenden Tabelle wird eine X509-Schlüsselverwendungsrichtlinie effektiven Schlüsselvorgängen eines Schlüssels zugeordnet, der im Rahmen einer Key Vault-Zertifikatserstellung erstellt wurde.

|**X509-Flags für Schlüsselverwendung**|**Key Vault-Schlüsseloptionen**|**Standardverhalten**|
|----------|--------|--------|
|DataEncipherment|encrypt, decrypt| – |
|DecipherOnly|Entschlüsseln| –  |
|DigitalSignature|sign, verify| Key Vault-Standard ohne Verwendungsspezifikation zum Zeitpunkt der Zertifikatserstellung | 
|EncipherOnly|encrypt| – |
|KeyCertSign|sign, verify|–|
|KeyEncipherment|wrapKey, unwrapKey| Key Vault-Standard ohne Verwendungsspezifikation zum Zeitpunkt der Zertifikatserstellung | 
|NonRepudiation|sign, verify| – |
|crlsign|sign, verify| – |

## <a name="certificate-issuer"></a>Zertifikataussteller

Ein Key Vault-Zertifikatsobjekt enthält eine Konfiguration zur Kommunikation mit einem ausgewählten Zertifikatsausstelleranbieter über das Bestellen von X509-Zertifikaten.  

-   Key Vault-Partner mit den folgenden Zertifikatsausstelleranbietern für TSL-/SSL-Zertifikate

|**Anbietername**|**Speicherorte**|
|----------|--------|
|DigiCert|Wird an allen Key Vault-Dienststandorten in öffentlicher Cloud und Azure Government unterstützt|
|GlobalSign|Wird an allen Key Vault-Dienststandorten in öffentlicher Cloud und Azure Government unterstützt|

Bevor ein Zertifikatsaussteller in einer Key Vault-Instanz erstellt werden kann, müssen die folgenden erforderlichen Schritte 1 und 2 erfolgreich durchgeführt werden.  

1. Integrieren in Anbieter von Zertifizierungsstellen (CA)  

    -   Ein Organisationsadministrator muss sein Unternehmen (z.B. Contoso) in mindestens einen CA-Anbieter integrieren.  

2. Der Administrator erstellt die Anmeldeinformationen der anfordernden Person, damit Key Vault TSL-/SSL-Zertifikate registrieren (und erneuern) kann.  

    -   Stellt die Konfiguration zum Erstellen eines Ausstellerobjekts des Anbieters im Schlüsseltresor bereit  

Weitere Informationen zum Erstellen von Ausstellerobjekten im Zertifikateportal finden Sie im [Blog zu Key Vault-Zertifikaten](/archive/blogs/kv/manage-certificates-via-azure-key-vault).  

Key Vault ermöglicht die Erstellung mehrerer Ausstellerobjekte mit unterschiedlicher Ausstelleranbieterkonfiguration. Sobald ein Ausstellerobjekt erstellt ist, kann in einer oder mehreren Zertifikatrichtlinien auf seinen Namen verwiesen werden. Beim Verweis auf das Ausstellerobjekt wird Key Vault angewiesen, die Konfiguration gemäß den Angaben im Ausstellerobjekt zu verwenden, wenn das X509-Zertifikat bei Zertifikaterstellung und -verlängerung beim CA-Anbieter angefordert wird.  

Ausstellerobjekte werden im Tresor erstellt und können nur in demselben Tresor mit Key Vault-Zertifikaten verwendet werden.  

## <a name="certificate-contacts"></a>Zertifikatkontakte

Zertifikatkontakte enthalten Kontaktinformationen zum Senden von Benachrichtigungen, die durch Zertifikatlebensdauer-Ereignisse ausgelöst werden. Die Kontaktinformationen werden von allen Zertifikaten im Schlüsseltresor gemeinsam genutzt. Eine Benachrichtigung zu einem Ereignis eines beliebigen Zertifikats im Schlüsseltresor wird an alle angegebenen Kontakte gesendet. Informationen zum Festlegen des Zertifikatkontatks finden Sie [hier](overview-renew-certificate.md#steps-to-set-certificate-notifications).  

## <a name="certificate-access-control"></a>Zertifikatzugriffssteuerung

 Die Zugriffssteuerung für Zertifikate wird von Key Vault verwaltet und wird von der Key Vault-Instanz bereitgestellt, die diese Zertifikate enthält. Die Zugriffssteuerungsrichtlinie für Zertifikate unterscheidet sich von der Zugriffssteuerungsrichtlinie für Schlüssel und Geheimnisse im selben Key Vault. Benutzer können einen oder mehrere Tresore zum Speichern von Zertifikaten erstellen und müssen für eine dem Szenario entsprechende Segmentierung und Verwaltung von Zertifikaten sorgen.  Weitere Informationen zur Zertifikatzugriffssteuerung finden Sie [hier](certificate-access-control.md).

## <a name="next-steps"></a>Nächste Schritte

- [Informationen zu Key Vault](../general/overview.md)
- [Informationen zu Schlüsseln, Geheimnissen und Zertifikaten](../general/about-keys-secrets-certificates.md)
- [Informationen zu Schlüsseln](../keys/about-keys.md)
- [Informationen zu Geheimnissen](../secrets/about-secrets.md)
- [Authentifizierung, Anforderungen und Antworten](../general/authentication-requests-and-responses.md)
- [Entwicklerhandbuch für Key Vault](../general/developers-guide.md)
