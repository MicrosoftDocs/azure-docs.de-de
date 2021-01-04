---
title: Erste Schritte mit Key Vault-Zertifikaten
description: Die folgenden Szenarien stellen verschiedene der primären Verwendungen des Key Vault-Zertifikatverwaltungdiensts dar. Dazu gehören auch die zusätzlichen Schritte, die zum Erstellen Ihres ersten Zertifikats in Ihrem Schlüsseltresor erforderlich sind.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 06/13/2020
ms.author: mbaldwin
ms.openlocfilehash: 85f17897c0e3089a2d2bc5b172e98fa24e8085ff
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94920439"
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


![Zertifikate sind komplex](../media/azure-key-vault.png)


## <a name="creating-your-first-key-vault-certificate"></a>Erstellen Ihres ersten Key Vault-Zertifikats  
 Bevor ein Zertifikat in einem Schlüsseltresor (Key Vault, KV) erstellt werden kann, müssen die erforderlichen Schritte 1 und 2 erfolgreich durchgeführt werden, und ein Schlüsseltresor muss für diesen Benutzer/diese Organisation vorhanden sein.  

**Schritt 1**: Anbieter von Zertifizierungsstellen  
-   Onboarding als IT-Administrator, PKI-Administrator oder als ein anderer Benutzer, der Konten bei Zertifizierungsstellen verwaltet, für ein bestimmtes Unternehmen (z.B. Contoso) ist eine Voraussetzung für die Verwendung von Key Vault-Zertifikaten.  
    Die folgenden Zertifizierungsstellen sind aktuell Key Vault-Partneranbieter. [Hier](./create-certificate.md#partnered-ca-providers) erhalten Sie weitere Informationen.   
    -   DigiCert: Key Vault bietet OV-TLS-/SSL-Zertifikate mit DigiCert.  
    -   GlobalSign: Key Vault bietet OV-TSL-/SSL-Zertifikate mit GlobalSign.  

**Schritt 2**: Ein Kontoadministrator für den Anbieter einer Zertifizierungsstelle erstellt Anmeldeinformationen, die von Key Vault verwendet werden, um TSL-/SSL-Zertifikate über Key Vault zu registrieren, zu erneuern und zu verwenden.

**Schritt 3**: Ein Contoso-Administrator und ein Contoso-Mitarbeiter (Key Vault-Benutzer), der Zertifikate besitzt, kann abhängig von der Zertifizierungsstelle ein Zertifikat vom Administrator oder direkt über das Konto bei der Zertifizierungsstelle abrufen.  

- Beginnen Sie mit einem Vorgang zum Hinzufügen von Anmeldeinformationen zu einem Schlüsseltresor, indem Sie eine [Zertifikataussteller-Ressource festlegen](/rest/api/keyvault/setcertificateissuer/setcertificateissuer). Ein Zertifikataussteller ist eine Entität, die in Azure Key Vault (KV) als eine CertificateIssuer-Ressource dargestellt wird. Sie dient zur Bereitstellung von Informationen zur Quelle eines KV-Zertifikats: Ausstellername, Anbieter, Anmeldeinformationen und andere administrative Details.
  - Ex. MyDigiCertIssuer  
    -   Anbieter  
    -   Anmeldeinformationen: Kontoanmeldeinformationen für die Zertifizierungsstelle. Jede Zertifizierungsstelle verfügt über eigene spezifische Daten.  

    Weitere Informationen zum Erstellen von Konten bei Anbietern von Zertifizierungsstellen finden Sie im zugehörigen Beitrag im [Key Vault-Blog](/archive/blogs/kv/manage-certificates-via-azure-key-vault).  

**Schritt 3.1**: Einrichten von [Zertifikatkontakten](/rest/api/keyvault/setcertificatecontacts/setcertificatecontacts) für Benachrichtigungen. Dies ist der Kontakt für den Key Vault-Benutzer. Key Vault erzwingt diesen Schritt nicht.  

Hinweis: Dieser Prozess bis Schritt 3.1 ist ein einmaliger Vorgang.  

## <a name="creating-a-certificate-with-a-ca-partnered-with-key-vault"></a>Erstellen eines Zertifikats mit einer mit Key Vault verbundenen Zertifizierungsstelle

![Erstellen eines Zertifikats mit einer Key Vault-Partnerzertifizierungsstelle](../media/certificate-authority-2.png)

**Schritt 4**: Die folgenden Beschreibungen entsprechen den in Grün nummerierten Schritten im vorherigen Diagramm.  
  (1): Im obigen Diagramm wird von Ihrer Anwendung ein Zertifikat erstellt, dies beginnt intern mit der Erstellung eines Schlüssels in Ihrem Schlüsseltresor.  
  (2): Key Vault sendet eine TSL-/SSL-Zertifikatanforderung an die Zertifizierungsstelle.  
  (3): Ihre Anwendung fragt in einem Schleifen- und Warteprozess Ihren Key Vault für die Zertifikatsvervollständigung ab. Die Zertifikatserstellung ist abgeschlossen, sobald Key Vault die Antwort der Zertifizierungsstelle mit dem x509-Zertifikat erhält.  
  (4): Die Zertifizierungsstelle antwortet auf die TSL-/SSL-Zertifikatanforderung von Key Vault mit einem x509-TSL-/SSL-Zertifikat.  
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

### <a name="network-security-and-access-policies-associated-with-integrated-ca"></a>Netzwerksicherheit und Zugriffsrichtlinien, die der integrierten Zertifizierungsstelle zugeordnet sind
Der Key Vault-Dienst sendet Anforderungen an die Zertifizierungsstelle (ausgehender Datenverkehr). Daher ist sie vollständig kompatibel mit firewallfähigen Schlüsseltresoren. Der Key Vault-Dienst verwendet keine Zugriffsrichtlinien gemeinsam mit der Zertifizierungsstelle. Die Zertifizierungsstelle muss so konfiguriert werden, dass Signieranforderungen unabhängig akzeptiert werden. [Integrieren von Key Vault in die DigiCert-Zertifizierungsstelle](./how-to-integrate-certificate-authority.md)

## <a name="import-a-certificate"></a>Importieren eines Zertifikats  
 Alternative: Ein Zertifikat kann in Key Vault als PFX oder PEM importiert werden.  

 Zertifikat importieren: Erfordert eine PEM oder PFX auf dem Datenträger und einen privaten Schlüssel. 
-   Sie müssen angeben: Tresorname und Zertifikatname (Richtlinie ist optional)

-   PEM-/PFX-Dateien enthalten Attribute, die Key Vault analysieren und zum Auffüllen der Zertifikatrichtlinie verwenden kann. Wenn eine Zertifikatrichtlinie bereits angegeben wurde, versucht Key Vault Daten aus der PFX-/PEM-Datei abzugleichen.  

-   Nach Abschluss des Imports verwenden nachfolgende Vorgänge die neue Richtlinie (neue Versionen).  

-   Wenn keine weiteren Vorgänge ausstehen, sendet Key Vault zuerst einen Ablaufhinweis. 

-   Darüber hinaus kann der Benutzer die Richtlinie bearbeiten, die zum Zeitpunkt des Imports funktionsfähig ist, aber an den Stellen Standardwerte enthält, an denen beim Import keine Informationen angegeben waren. Ex. Keine Informationen zum Aussteller  

### <a name="formats-of-import-we-support"></a>Unterstützte Importformate
Azure Key Vault unterstützt PEM- und PFX-Zertifikatsdateien für den Import von Zertifikaten.
Die folgenden Importtypen werden für das PEM-Dateiformat unterstützt. Ein einzelnes PEM-codiertes Zertifikat mit einem PKCS#8-codiertem, nicht verschlüsselten Schlüssel, das Folgendes aufweist:

-----BEGIN CERTIFICATE----- -----END CERTIFICATE-----

-----BEGIN PRIVATE KEY----- -----END PRIVATE KEY-----

Wenn Sie das Zertifikat importieren, müssen Sie sicherstellen, dass der Schlüssel in der Datei selbst enthalten ist. Wenn der private Schlüssel separat in einem anderen Format vorliegt, müssen Sie den Schlüssel mit dem Zertifikat kombinieren. Einige Zertifizierungsstellen bieten Zertifikate in verschiedenen Formaten an. Stellen Sie daher vor dem Importieren des Zertifikats sicher, dass in einem der Formate PEM oder PFX vorliegt. 

### <a name="formats-of-merge-csr-we-support"></a>Unterstützte Formate von Merge CSR
AKV unterstützt zwei PEM-basierte Formate. Sie können ein einzelnes PKCS#8-codiertes Zertifikat oder eine Base64-codierte P7B-Datei (von der Zertifizierungsstelle signierte Zertifikatkette) zusammenführen. 

-----BEGIN CERTIFICATE----- -----END CERTIFICATE-----


## <a name="creating-a-certificate-with-a-ca-not-partnered-with-key-vault"></a>Erstellen eines Zertifikats mit einer nicht mit Key Vault verbundenen Zertifizierungsstelle  
 Diese Methode ermöglicht das Arbeiten mit anderen Zertifizierungsstellen als den Key Vault-Partneranbietern. Ihre Organisation kann also mit einer Zertifizierungsstelle ihrer Wahl arbeiten.  

![Erstellen eines Zertifikats mit Ihrer eigenen Zertifizierungsstelle](../media/certificate-authority-1.png)  

 Die folgenden Schrittbeschreibungen entsprechen den in Grün nummerierten Schritten im vorherigen Diagramm.  

  (1): Im obigen Diagramm wird von Ihrer Anwendung ein Zertifikat erstellt, dies beginnt intern mit der Erstellung eines Schlüssels in Ihrem Schlüsseltresor.  

  (2): Key Vault sendet Ihrer Anwendung eine Zertifikatsignieranforderung (Certificate Signing Request, CSR) zurück.  

  (3): Ihre Anwendung übergibt die CSR an die von Ihnen gewählte Zertifizierungsstelle.  

  (4): Ihre gewählte Zertifizierungsstelle antwortet mit einem X.509-Zertifikat.  

  (5): Ihre Anwendung schließt die Erstellung des neuen Zertifikats durch das Zusammenführen mit dem X.509-Zertifikat Ihrer Zertifizierungsstelle ab.
