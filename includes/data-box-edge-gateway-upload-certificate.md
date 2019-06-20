---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/23/2019
ms.author: alkohli
ms.openlocfilehash: d7a9923d5bd9e357bcd75fae6e0a7d1bcd437a53
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178548"
---
Ein ordnungsgemäßes SSL-Zertifikat stellt sicher, dass Sie verschlüsselte Informationen an den richtigen Server senden. Neben der Verschlüsselung ermöglicht das Zertifikat auch die Authentifizierung. Sie können über die PowerShell-Schnittstelle des Geräts Ihr eigenes vertrauenswürdiges SSL-Zertifikat hochladen.

1. [Herstellen einer Verbindung mit der PowerShell-Schnittstelle](#connect-to-the-powershell-interface).
2. Verwenden Sie das Cmdlet `Set-HcsCertificate`, um das Zertifikat hochzuladen. Geben Sie bei entsprechender Aufforderung die folgenden Parameter an:

   - `CertificateFilePath`: Pfad zur Freigabe, der die Zertifikatdatei im *PFX*-Format enthält.
   - `CertificatePassword`: Kennwort zum Schutz des Zertifikats.
   - `Credentials`: Benutzername und Kennwort für den Zugriff auf die Freigabe, die das Zertifikat enthält.

     Nachfolgend sehen Sie ein Beispiel für die Verwendung dieses Cmdlets:

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username/Password"
     ```

