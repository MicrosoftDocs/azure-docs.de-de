---
title: Zertifikatsanforderungen und Problembehandlung bei Azure Stack Edge Pro | Microsoft-Dokumentation
description: Beschreibt die Zertifikatsanforderungen und die Problembehandlung von Zertifikatsfehlern bei Azure Stack Edge Pro-Geräten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 11/17/2020
ms.author: alkohli
ms.openlocfilehash: bbe4be0b6cc1d3ea9e0391e82ce21e307e9de640
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100549440"
---
# <a name="certificate-requirements"></a>Zertifikatanforderungen

In diesem Artikel werden die Zertifikatsanforderungen beschrieben, die erfüllt sein müssen, damit Zertifikate auf Ihrem Azure Stack Edge Pro-Gerät installiert werden können. Die Anforderungen beziehen sich auf die PFX-Zertifikate, die ausstellende Zertifizierungsstelle, den Namen des Zertifikatantragstellers und den alternativen Antragstellernamen sowie die unterstützten Zertifikatsalgorithmen.

## <a name="certificate-issuing-authority"></a>Zertifizierungsstelle für Zertifikate

Die Anforderungen für die ausstellende Zertifizierungsstelle sind wie folgt:

* Zertifikate müssen von einer internen oder öffentlichen Zertifizierungsstelle ausgestellt werden.

* Die Verwendung selbstsignierter Zertifikate wird nicht unterstützt.

* Das Feld *Ausgestellt für:* darf nicht mit dem Feld *Ausgestellt von:* identisch sein, ausgenommen bei Zertifikaten von Stammzertifizierungsstellen.


## <a name="certificate-algorithms"></a>Zertifikatsalgorithmen

Für Zertifikatsalgorithmen gelten die folgenden Anforderungen:

* Zertifikate müssen den RSA-Schlüsselalgorithmus verwenden.

* Es werden nur RSA-Zertifikate mit „Microsoft RSA/SChannel Cryptographic Provider“ unterstützt.

* Der Zertifikatsignaturalgorithmus darf nicht SHA1 sein.

* Die minimale Schlüsselgröße ist 4096.

## <a name="certificate-subject-name-and-subject-alternative-name"></a>Name des Zertifikatantragstellers und alternativer Antragstellername

Zertifikate müssen die folgenden Anforderungen in Bezug auf den Namen des Zertifikatantragstellers und den alternativen Antragstellernamen erfüllen:

* Sie können entweder ein einzelnes Zertifikat verwenden, das alle Namespaces in den SAN-Feldern („Alternativer Antragstellername“) des Zertifikats abdeckt. Alternativ dazu können Sie für die einzelnen Namespaces eigene Zertifikate verwenden. Beide Ansätze erfordern ggf. die Verwendung von Platzhaltern für Endpunkte, z. B. Binary Large Object (Blob).

* Stellen Sie sicher, dass die Antragstellernamen (allgemeiner Name im Antragstellernamen) Teil der alternativen Antragstellernamen in der Erweiterung für alternative Antragstellernamen sind.

* Sie können ein einzelnes Platzhalterzertifikat verwenden, das alle Namespaces in den SAN-Feldern des Zertifikats abdeckt.

* Verwenden Sie die folgende Tabelle beim Erstellen eines Endpunktzertifikats:

    |type |Antragstellername  |Alternativer Antragstellername  |Beispiel für Antragstellername |
    |---------|---------|---------|---------|
    |Azure Resource Manager|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Blob Storage|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |Lokale Benutzeroberfläche| `<Device name>.<DnsDomain>`|`<Device name>.<DnsDomain>`| `mydevice1.microsoftdatabox.com` |
    |Einzelnes Zertifikat für mehrere alternative Antragstellernamen für beide Endpunkte|`<Device name>.<dnsdomain>`|`<Device name>.<dnsdomain>`<br>`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |
    |Node|`<NodeSerialNo>.<DnsDomain>`|`*.<DnsDomain>`<br><br>`<NodeSerialNo>.<DnsDomain>`|`mydevice1.microsoftdatabox.com` |
    |VPN|`AzureStackEdgeVPNCertificate.<DnsDomain>`<br><br> * AzureStackEdgeVPNCertificate ist hartcodiert.  | `*.<DnsDomain>`<br><br>`<AzureStackVPN>.<DnsDomain>` | `edgevpncertificate.microsoftdatabox.com`|
    
## <a name="pfx-certificate"></a>PFX-Zertifikat

Die auf Ihrem Azure Stack Edge Pro-Gerät installierten PFX-Zertifikate müssen die folgenden Anforderungen erfüllen:

* Wenn Sie Ihre Zertifikate von der SSL-Zertifizierungsstelle erhalten, stellen Sie sicher, dass Sie die vollständige Signaturkette für die Zertifikate erhalten.

* Stellen Sie beim Exportieren von PFX-Zertifikaten sicher, dass Sie die Option **Include all certificates in the chain, if possible** (Wenn möglich, alle Zertifikate in der Kette einbeziehen) ausgewählt haben.

* Verwenden Sie ein PFX-Zertifikat für Endpunkte, lokale Benutzeroberflächen, Knoten, VPN und WLAN, da für Azure Stack Edge Pro sowohl der öffentliche als auch der private Schlüssel benötigt werden. Für den privaten Schlüssel muss das Schlüsselattribut des lokalen Computers festgelegt sein.

* Die PFX-Verschlüsselung des Zertifikats sollte mit 3DES erfolgen. Dies ist die Standardverschlüsselung, die beim Exportieren von einem Windows 10-Client oder einem Windows Server 2016-Zertifikatspeicher verwendet wird. Weitere Informationen zu 3DES finden Sie unter [Triple DES](https://en.wikipedia.org/wiki/Triple_DES).

* Die PFX-Zertifikatdateien müssen im Feld *Schlüsselverwendung* einen Wert für *Digitale Signatur* und *Schlüsselchiffrierung* enthalten.

* Die PFX-Zertifikatdateien müssen die Werte *Serverauthentifizierung (1.3.6.1.5.5.7.3.1)* und *Clientauthentifizierung (1.3.6.1.5.5.7.3.2)* im Feld *Erweiterte Schlüsselverwendung* aufweisen.

* Bei Verwendung des Azure Stack Readiness Checker-Tools müssen die Kennwörter aller PFX-Zertifikatdateien zum Zeitpunkt der Bereitstellung identisch sein. Weitere Informationen finden Sie unter [Erstellen von Zertifikaten für Azure Stack Edge Pro-Geräte mit dem Azure Stack Hub Readiness Checker-Tool](azure-stack-edge-gpu-create-certificates-tool.md).

* Für die PFX-Zertifikatdatei muss ein komplexes Kennwort verwendet werden. Notieren Sie sich dieses Kennwort, da es später als Bereitstellungsparameter benötigt wird.

* Verwenden Sie nur RSA-Zertifikate mit „Microsoft RSA/SChannel Cryptographic Provider“.

Weitere Informationen finden Sie unter [Exportieren von PFX-Zertifikaten mit privatem Schlüssel](azure-stack-edge-gpu-manage-certificates.md#export-certificates-as-pfx-format-with-private-key).

## <a name="next-steps"></a>Nächste Schritte

[Verwenden von Zertifikaten mit Azure Stack Edge Pro](azure-stack-edge-gpu-manage-certificates.md)

[Erstellen von Zertifikaten für Azure Stack Edge Pro-Geräte mit dem Azure Stack Hub Readiness Checker-Tool](azure-stack-edge-gpu-create-certificates-tool.md)

[Exportieren von PFX-Zertifikaten mit privatem Schlüssel](azure-stack-edge-gpu-manage-certificates.md#export-certificates-as-pfx-format-with-private-key)

[Problembehandlung für Zertifikate](azure-stack-edge-gpu-certificate-troubleshooting.md)