---
title: Erste Schritte mit Key Vault-Zertifikaten
description: Die folgenden Szenarien stellen verschiedene der primären Verwendungen des Key Vault-Zertifikatverwaltungdiensts dar. Dazu gehören auch die zusätzlichen Schritte, die zum Erstellen Ihres ersten Zertifikats in Ihrem Schlüsseltresor erforderlich sind.
services: key-vault
author: msmbaldwin
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 805f11d57a635f4e73309d025e185049b511570b
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427842"
---
# <a name="get-started-with-key-vault-certificates"></a>Erste Schritte mit Key Vault-Zertifikaten
Die folgenden Szenarien stellen verschiedene der primären Verwendungen des Key Vault-Zertifikatverwaltungdiensts dar. Dazu gehören auch die zusätzlichen Schritte, die zum Erstellen Ihres ersten Zertifikats in Ihrem Schlüsseltresor erforderlich sind.

Folgendes wird beschrieben:
- Erstellen Ihres ersten Key Vault-Zertifikats
- Erstellen eines Zertifikats mit einer Zertifizierungsstelle, die mit Key Vault verbunden ist
- Erstellen eines Zertifikats mit einer Zertifizierungsstelle, die nicht mit Key Vault verbunden ist
- Importieren eines Zertifikats

## <a name="certificates-are-complex-objects"></a>Zertifikate sind komplexe Objekte
Zertifikate bestehen aus drei zusammenhängenden Ressourcen, die als Key Vault-Zertifikat verknüpft sind: Zertifikatmetadaten, ein Schlüssel und ein Geheimnis.


![Zertifikate sind komplex](media/azure-key-vault.png)


## <a name="creating-your-first-key-vault-certificate"></a>Erstellen Ihres ersten Key Vault-Zertifikats  
 Bevor ein Zertifikat in einem Schlüsseltresor (Key Vault, KV) erstellt werden kann, müssen die erforderlichen Schritte 1 und 2 erfolgreich durchgeführt werden, und ein Schlüsseltresor muss für diesen Benutzer/diese Organisation vorhanden sein.  

**Schritt 1**: Anbieter von Zertifizierungsstellen  
-   Onboarding als IT-Administrator, PKI-Administrator oder als ein anderer Benutzer, der Konten bei Zertifizierungsstellen verwaltet, für ein bestimmtes Unternehmen (z.B. Contoso) ist eine Voraussetzung für die Verwendung von Key Vault-Zertifikaten.  
    Die folgenden Zertifizierungsstellen sind aktuell Key Vault-Partneranbieter:  
    -   DigiCert: Key Vault bietet OV-SSL-Zertifikate mit DigiCert.  
    -   Globaltrust: Key Vault bietet OV-SSL-Zertifikate mit Globaltrust.  

**Schritt 2**: Ein Kontoadministrator für den Anbieter einer Zertifizierungsstelle erstellt Anmeldeinformationen, die von Key Vault verwendet werden, um SSL-Zertifikate über Key Vault zu registrieren, zu erneuern und zu verwenden.

**Schritt 3**: Ein Contoso-Administrator und ein Contoso-Mitarbeiter (Key Vault-Benutzer), der Zertifikate besitzt, kann abhängig von der Zertifizierungsstelle ein Zertifikat vom Administrator oder direkt über das Konto bei der Zertifizierungsstelle abrufen.  

- Beginnen Sie mit einem Vorgang zum Hinzufügen von Anmeldeinformationen zu einem Schlüsseltresor, indem Sie eine [Zertifikataussteller-Ressource festlegen](/rest/api/keyvault/setcertificateissuer/setcertificateissuer). Ein Zertifikataussteller ist eine Entität, die in Azure Key Vault (KV) als eine CertificateIssuer-Ressource dargestellt wird. Sie dient zur Bereitstellung von Informationen zur Quelle eines KV-Zertifikats: Ausstellername, Anbieter, Anmeldeinformationen und andere administrative Details.
  - Beispiel: MyDigiCertIssuer  
    -   Anbieter  
    -   Anmeldeinformationen: Kontoanmeldeinformationen für die Zertifizierungsstelle. Jede Zertifizierungsstelle verfügt über eigene spezifische Daten.  

    Weitere Informationen zum Erstellen von Konten bei Anbietern von Zertifizierungsstellen finden Sie im zugehörigen Beitrag im [Key Vault-Blog](https://aka.ms/kvcertsblog).  

**Schritt 3.1**: Einrichten von [Zertifikatkontakten](/rest/api/keyvault/setcertificatecontacts/setcertificatecontacts) für Benachrichtigungen. Dies ist der Kontakt für den Key Vault-Benutzer. Key Vault erzwingt diesen Schritt nicht.  

Hinweis: Dieser Prozess bis Schritt 3.1 ist ein einmaliger Vorgang.  

## <a name="creating-a-certificate-with-a-ca-partnered-with-key-vault"></a>Erstellen eines Zertifikats mit einer mit Key Vault verbundenen Zertifizierungsstelle

![Erstellen eines Zertifikats mit einer Key Vault-Partnerzertifizierungsstelle](media/certificate-authority-2.png)

**Schritt 4**: Die folgenden Beschreibungen entsprechen den in Grün nummerierten Schritten im vorherigen Diagramm.  
  (1): Im obigen Diagramm wird von Ihrer Anwendung ein Zertifikat erstellt, dies beginnt intern mit der Erstellung eines Schlüssels in Ihrem Schlüsseltresor.  
  (2): Key Vault sendet eine SSL-Zertifikatanforderung an die Zertifizierungsstelle.  
  (3): Ihre Anwendung fragt in einem Schleifen- und Warteprozess Ihren Key Vault für die Zertifikatsvervollständigung ab. Die Zertifikaterstellung ist abgeschlossen, sobald Key Vault die Antwort der Zertifizierungsstelle mit dem X.509-Zertifikat erhält.  
  (4): Die Zertifizierungsstelle antwortet auf die SSL-Zertifikatanforderung von Key Vault mit einem X.509-SSL-Zertifikat.  
  (5): Die Erstellung Ihres neuen Zertifikats wird mit der Zusammenführung des X.509-Zertifikats für die Zertifizierungsstelle abgeschlossen.  

  Key Vault-Benutzer: Erstellt ein Zertifikat durch Angabe einer Richtlinie

  -   Nach Bedarf wiederholen  
  -   Richtlinieneinschränkungen  
      -   X.509-Eigenschaften  
      -   Schlüsseleigenschaften  
      -   Anbieterreferenz - > Beispiel: MyDigiCertIssure  
      -   Erneuerungsinformationen - > Beispiel: 90 Tage vor Ablauf  

  - Ein Zertifikaterstellungsprozess ist normalerweise ein asynchroner Prozess und umfasst das Abrufen des Status der Zertifikaterstellung von Ihrem Schlüsseltresor.  
[Zertifikatabrufvorgang](/rest/api/keyvault/getcertificateoperation/getcertificateoperation)  
      -   Status: abgeschlossen, Fehler mit Fehlerinformationen oder abgebrochen  
      -   Aufgrund einer Verzögerung bei der Erstellung kann ein Abbruchvorgang initiiert werden. Der Abbruchvorgang ist aber möglicherweise nicht wirksam.  

## <a name="import-a-certificate"></a>Importieren eines Zertifikats  
 Alternative: Ein Zertifikat kann in Key Vault als PFX oder PEM importiert werden.  

 Weitere Informationen zum PEM-Format finden Sie im Abschnitt „Zertifikate“ unter [Informationen zu Schlüsseln, Geheimnissen und Zertifikaten](about-keys-secrets-and-certificates.md).  

 Zertifikat importieren: Erfordert eine PEM oder PFX auf dem Datenträger und einen privaten Schlüssel. 
-   Sie müssen angeben: Tresorname und Zertifikatname (Richtlinie ist optional)

-   PEM-/PFX-Dateien enthalten Attribute, die Key Vault analysieren und zum Auffüllen der Zertifikatrichtlinie verwenden kann. Wenn eine Zertifikatrichtlinie bereits angegeben wurde, versucht Key Vault Daten aus der PFX-/PEM-Datei abzugleichen.  

-   Nach Abschluss des Imports verwenden nachfolgende Vorgänge die neue Richtlinie (neue Versionen).  

-   Wenn keine weiteren Vorgänge ausstehen, sendet Key Vault zuerst einen Ablaufhinweis. 

-   Darüber hinaus kann der Benutzer die Richtlinie bearbeiten, die zum Zeitpunkt des Imports funktionsfähig ist, aber an den Stellen Standardwerte enthält, an denen beim Import keine Informationen angegeben waren. Beispiel: Keine Informationen zum Aussteller  

### <a name="formats-of-import-we-support"></a>Unterstützte Importformate
Die folgenden Importtypen werden für das PEM-Dateiformat unterstützt. Ein einzelnes PEM-codiertes Zertifikat mit einem PKCS#8-codiertem, nicht verschlüsselten Schlüssel, das Folgendes aufweist:

-----BEGIN CERTIFICATE----- -----END CERTIFICATE-----

-----BEGIN PRIVATE KEY----- -----END PRIVATE KEY-----

Bei der Zusammenführung von Zertifikaten werden zwei PEM-basierte Formate unterstützt. Sie können entweder ein einzelnes PKCS#8-codiertes Zertifikat oder eine base64-codierte P7B-Datei zusammenführen. -----BEGIN CERTIFICATE----- -----END CERTIFICATE-----

EC-Schlüssel werden derzeit nicht im PEM-Format unterstützt.

## <a name="creating-a-certificate-with-a-ca-not-partnered-with-key-vault"></a>Erstellen eines Zertifikats mit einer nicht mit Key Vault verbundenen Zertifizierungsstelle  
 Diese Methode ermöglicht das Arbeiten mit anderen Zertifizierungsstellen als den Key Vault-Partneranbietern. Ihre Organisation kann also mit einer Zertifizierungsstelle ihrer Wahl arbeiten.  

![Erstellen eines Zertifikats mit Ihrer eigenen Zertifizierungsstelle](media/certificate-authority-1.png)  

 Die folgenden Schrittbeschreibungen entsprechen den in Grün nummerierten Schritten im vorherigen Diagramm.  

  (1): Im obigen Diagramm wird von Ihrer Anwendung ein Zertifikat erstellt, dies beginnt intern mit der Erstellung eines Schlüssels in Ihrem Schlüsseltresor.  

  (2): Key Vault sendet Ihrer Anwendung eine Zertifikatsignieranforderung (Certificate Signing Request, CSR) zurück.  

  (3): Ihre Anwendung übergibt die CSR an die von Ihnen gewählte Zertifizierungsstelle.  

  (4): Ihre gewählte Zertifizierungsstelle antwortet mit einem X.509-Zertifikat.  

  (5): Ihre Anwendung schließt die Erstellung des neuen Zertifikats durch das Zusammenführen mit dem X.509-Zertifikat Ihrer Zertifizierungsstelle ab.

## <a name="see-also"></a>Siehe auch

- [Informationen zu Schlüsseln, Geheimnissen und Zertifikaten](about-keys-secrets-and-certificates.md)
