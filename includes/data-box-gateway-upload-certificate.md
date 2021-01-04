---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 27a4d775b8d88e02be69655e5adfc6155f867ef6
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96581045"
---
Ein ordnungsgemäßes SSL-Zertifikat stellt sicher, dass Sie verschlüsselte Informationen an den richtigen Server senden. Neben der Verschlüsselung ermöglicht das Zertifikat auch die Authentifizierung. Sie können über die PowerShell-Schnittstelle des Geräts Ihr eigenes vertrauenswürdiges SSL-Zertifikat hochladen.

1. [Herstellen einer Verbindung mit der PowerShell-Schnittstelle](#connect-to-the-powershell-interface).
2. Verwenden Sie das Cmdlet `Set-HcsCertificate`, um das Zertifikat hochzuladen. Geben Sie bei entsprechender Aufforderung die folgenden Parameter an:

   - `CertificateFilePath`: Pfad zur Freigabe, der die Zertifikatdatei im *PFX*-Format enthält.
   - `CertificatePassword`: Kennwort zum Schutz des Zertifikats.
   - `Credentials`: Benutzername für den Zugriff auf die Freigabe, die das Zertifikat enthält. Geben Sie bei Aufforderung das Kennwort für die Netzwerkfreigabe an.

     Nachfolgend sehen Sie ein Beispiel für die Verwendung dieses Cmdlets:

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username"
     ```
