---
title: Methoden für die Zertifikaterstellung
description: Möglichkeiten zum Erstellen eines Zertifikats in Key Vault.
services: key-vault
author: msmbaldwin
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 67720256cfac68c350c800291653a4a0c1d7ee46
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427817"
---
# <a name="certificate-creation-methods"></a>Methoden für die Zertifikaterstellung

 Ein Key Vault (KV)-Zertifikat kann entweder erstellt oder in einen Schlüsseltresor importiert werden. Wenn ein KV-Zertifikat erstellt wird, wird der private Schlüssel innerhalb des Schlüsseltresors erstellt und nie dem Zertifikatsinhaber verfügbar gemacht. Im Folgenden finden Sie Möglichkeiten, ein Zertifikat in Key Vault zu erstellen:  

-   **Erstellen eines selbstsignierten Zertifikats**: Dadurch wird ein öffentlich-privates Schlüsselpaar erstellt und mit einem Zertifikat verknüpft. Das Zertifikat wird mit einem eigenen Schlüssel signiert.  

-    **Manuelles Erstellen eines neuen Zertifikats**: Dadurch wird ein öffentlich-privates Schlüsselpaar erstellt und eine X.509-Zertifikatsignatur angefordert. Die Signaturanforderung kann von Ihrer Registrierungs- oder Zertifizierungsstelle signiert werden. Das signierte x509-Zertifikat lässt sich dann mit dem ausstehenden Schlüsselpaar zusammenführen, um das KV-Zertifikat in Key Vault zu vervollständigen. Obwohl diese Methode mehr Schritte erfordert, bietet sie Ihnen mehr Sicherheit, da der private Schlüssel in Key Vault erstellt und darauf beschränkt wird. Dies wird im folgenden Diagramm erläutert.  

![Erstellen eines Zertifikats mit Ihrer eigenen Zertifizierungsstelle](media/certificate-authority-1.png)  

Die folgenden Beschreibungen entsprechen den in Grün nummerierten Schritten im vorigen Diagramm.

1. Im obigen Diagramm wird von Ihrer Anwendung ein Zertifikat erstellt, das intern mit der Erstellung eines Schlüssels in Ihrem Schlüsselspeicher beginnt.
2. Key Vault sendet Ihrer Anwendung eine Zertifikatsignieranforderung (Certificate Signing Request, CSR) zurück.
3. Ihre Anwendung übergibt die CSR an die von Ihnen gewählte Zertifizierungsstelle.
4. Ihre ausgewählte Zertifizierungsstelle antwortet mit einem X.509-Zertifikat.
5. Ihre Anwendung schließt die Erstellung des neuen Zertifikats durch das Zusammenführen mit dem X509-Zertifikat Ihrer Zertifizierungsstelle ab.

-   **Erstellen eines Zertifikats mit einem bekannten Zertifikataussteller:** Bei dieser Methode müssen Sie einmalig ein Ausstellerobjekt erstellen. Sobald ein Ausstellerobjekt in Ihrem Schlüsselspeicher erstellt wurde, kann in der Richtlinie des KV-Zertifikats auf seinen Namen verwiesen werden. Durch die Anforderung für die Erstellung eines solchen KV-Zertifikats wird ein Schlüsselpaar in Key Vault erstellt. Außerdem wird mit dem Zertifikatausstellerdienst unter Verwendung der Informationen im referenzierten Ausstellerobjekt kommuniziert, um ein x509-Zertifikat zu erhalten. Das x509-Zertifikat wird vom Zertifikatausstellerdienst abgerufen und mit dem Schlüsselpaar zusammengeführt, um die Erstellung des KV-Zertifikats abzuschließen.  

![Erstellen eines Zertifikats mit einer Key Vault-Partnerzertifizierungsstelle](media/certificate-authority-2.png)  

Die folgenden Beschreibungen entsprechen den in Grün nummerierten Schritten im vorigen Diagramm.

1. Im obigen Diagramm wird von Ihrer Anwendung ein Zertifikat erstellt, das intern mit der Erstellung eines Schlüssels in Ihrem Schlüsselspeicher beginnt.
2. Key Vault sendet eine SSL-Zertifikatanforderung an die Zertifizierungsstelle.
3. Ihre Anwendung fragt in einem Schleifen- und Warteprozess Ihren Key Vault für die Zertifikatsvervollständigung ab. Die Zertifikatserstellung ist abgeschlossen, sobald Key Vault die Antwort der Zertifizierungsstelle mit dem x509-Zertifikat erhält.
4. Die Zertifizierungsstelle antwortet auf die SSL-Zertifikatanforderung von Key Vault mit einem x509-SSL-Zertifikat.
5. Die Erstellung Ihres neuen Zertifikats wird mit der Zusammenführung des x509-Zertifikats für die Zertifizierungsstelle abgeschlossen.

## <a name="asynchronous-process"></a>Asynchroner Prozess
Die Erstellung von KV-Zertifikaten ist ein asynchroner Prozess. Bei diesem Vorgang wird eine KV-Zertifikatanforderung erstellt, woraufhin ein HTTP-Statuscode von 202 (Akzeptiert) zurückgegeben wird. Der Status der Anforderung kann durch Abfragen des ausstehenden Objekts verfolgt werden, das durch diesen Vorgang erstellt wurde. Der vollständige URI des ausstehenden Objekts wird im LOCATION-Header zurückgegeben.  

Sobald eine Anforderung zur Erstellung eines KV-Zertifikats abgeschlossen ist, ändert sich der Status des ausstehenden Objekts von „in Bearbeitung“ in „abgeschlossen“, und es wird eine neue Version des KV-Zertifikats erstellt. Dies ist dann die aktuelle Version.  

## <a name="first-creation"></a>Erster Erstellungsvorgang
 Wenn ein KV-Zertifikat zum ersten Mal erstellt wird, wird auch ein adressierbarer Schlüssel und ein geheimer Schlüssel mit dem gleichen Namen wie das Zertifikat erstellt. Wird der Name bereits verwendet, tritt bei dem Vorgang ein Fehler mit einem HTTP-Statuscode von 409 (Konflikt) auf.
Der adressierbare Schlüssel und der geheime Schlüssel erhalten ihre Attribute aus den Attributen des KV-Zertifikats. Die so erstellten adressierbaren und geheimen Schlüssel werden als verwaltete Schlüssel und Geheimnisse gekennzeichnet, die während ihrer Lebensdauer von Key Vault verwaltet werden. Verwaltete Schlüssel und Geheimnisse sind schreibgeschützt. Hinweis: Wenn ein KV-Zertifikat abläuft oder deaktiviert ist, werden der entsprechende Schlüssel und das entsprechende Geheimnis unbrauchbar.  

 Wenn dies der erste Vorgang zum Erstellen eines KV-Zertifikats ist, müssen Sie eine Richtlinie festlegen.  Eine Richtlinie kann auch mit aufeinanderfolgenden Erstellungsvorgängen versehen werden, um die Richtlinienressource zu ersetzen. Wenn eine Richtlinie nicht bereitgestellt wird, wird die Richtlinienressource für den Dienst verwendet, um eine nächste Version des KV-Zertifikats zu erstellen. Beachten Sie, dass das aktuelle KV-Zertifikat und der entsprechende adressierbare Schlüssel sowie das Geheimnis unverändert bleiben, während eine Anforderung zur Erstellung einer neuen Version läuft.  

## <a name="self-issued-certificate"></a>Selbstausgestelltes Zertifikat
 Um ein selbstausgestelltes Zertifikat zu erstellen, setzen Sie den Ausstellernamen in der Zertifikatsrichtlinie auf „Self“, wie in folgendem Codeausschnitt der Zertifikatsrichtlinie gezeigt.  

```  
"issuer": {  
       "name": "Self"  
    }  

```  

 Wird der Ausstellername nicht angegeben, wird dieser auf „Unknown“ gesetzt. Wenn der Aussteller „Unknown“ ist, muss der Zertifikatinhaber manuell ein x509-Zertifikat vom Zertifikataussteller seiner Wahl abrufen und dann das öffentliche x509-Zertifikat mit dem ausstehenden KV-Zertifikat zusammenführen, um die Zertifikatserstellung abzuschließen.

```  
"issuer": {  
       "name": "Unknown"  
    }  

```  

## <a name="partnered-ca-providers"></a>Partnerzertifikataussteller
Die Zertifikatserstellung kann manuell oder über einen „Self“-Zertifikataussteller erfolgen. Key Vault arbeitet auch mit bestimmten Zertifikatausstellern zusammen, um die Erstellung von Zertifikaten zu vereinfachen. Die folgenden Zertifikatstypen können für den Schlüsseltresor bei diesen Partnerzertifikatausstellern bestellt werden.  

|Anbieter|Zertifikattyp|  
|--------------|----------------------|  
|DigiCert|Key Vault bietet OV- oder EV-SSL-Zertifikate mit DigiCert|
|GlobalCert|Key Vault bietet OV- oder EV-SSL-Zertifikate mit Globaltrust |

 Ein Zertifikataussteller ist eine Entität, die in Azure Key Vault (KV) als eine CertificateIssuer-Ressource dargestellt wird. Sie dient zur Bereitstellung von Informationen zur Quelle eines KV-Zertifikats: Ausstellername, Anbieter, Anmeldeinformationen und andere administrative Details.

Beachten Sie, dass bei einer Bestellung beim Zertifikataussteller die x509-Zertifikaterweiterungen und die Gültigkeitsdauer des Zertifikats je nach Zertifikatstyp berücksichtigt oder außer Kraft gesetzt werden können.  

 Autorisierung: Benötigt die Berechtigung für Zertifikate bzw. die Erstellung.

## <a name="see-also"></a>Siehe auch
 - [Informationen zu Schlüsseln, Geheimnissen und Zertifikaten](about-keys-secrets-and-certificates.md)
 - [Überwachen und Verwalten der Zertifikaterstellung](create-certificate-scenarios.md)
