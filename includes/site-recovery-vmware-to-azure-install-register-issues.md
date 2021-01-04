---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 5f794420eebb052538de0903727951de39f44a48
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001554"
---
### <a name="installation-failures"></a>Fehler bei der Installation
| **Beispielfehlermeldung** | **Empfohlene Maßnahme** |
|--------------------------|------------------------|
|ERROR   Failed to load Accounts. Error: System.IO.IOException: Unable to read data from the transport connection when installing and registering the CS server. (FEHLER   Fehler beim Laden von Konten. Fehler: System.IO.IOException: Von der Übertragungsverbindung können keine Daten gelesen werden, wenn der CS-Server installiert und registriert wird.)| Vergewissern Sie sich, dass TLS 1.0 auf dem Computer aktiviert ist. |

### <a name="registration-failures"></a>Fehler bei der Registrierung
Sehen Sie sich zum Debuggen von Registrierungsfehlern die Protokolle im Ordner **%ProgramData%\ASRLogs** an.

| **Beispielfehlermeldung** | **Empfohlene Maßnahme** |
|--------------------------|------------------------|
|**09:20:06**:InnerException.Type: SrsRestApiClientLib.AcsException,InnerException.<br>Message: ACS50008: SAML token is invalid. (Nachricht: ACS50008: SAML-Token ist ungültig.)<br>Ablaufverfolgungs-ID: 1921ea5b-4723-4be7-8087-a75d3f9e1072<br>Korrelations-ID: 62fea7e6-2197-4be4-a2c0-71ceb7aa2d97><br>Zeitstempel: **2016-12-12 14:50:08Z<br>** | Vergewissern Sie sich, dass die Uhrzeit Ihrer Systemuhr nicht mehr als 15 Minuten von der Ortszeit abweicht. Führen Sie das Installationsprogramm erneut aus, um die Registrierung durchzuführen.|
|**09:35:27** :DRRegistrationException while trying to get all disaster recovery vault for the selected certificate: : Threw Exception.Type:Microsoft.DisasterRecovery.Registration.DRRegistrationException, Exception.Message: ACS50008: SAML token is invalid. (:DRRegistrationException beim Abrufen aller Notfallwiederherstellungstresore für das ausgewählte Zertifikat: : Auslösung von Exception.Type:Microsoft.DisasterRecovery.Registration.DRRegistrationException, Exception.Message: ACS50008: SAML-Token ist ungültig.)<br>Ablaufverfolgungs-ID: e5ad1af1-2d39-4970-8eef-096e325c9950<br>Korrelations-ID: abe9deb8-3e64-464d-8375-36db9816427a<br>Zeitstempel: **2016-05-19 01:35:39Z**<br> | Vergewissern Sie sich, dass die Uhrzeit Ihrer Systemuhr nicht mehr als 15 Minuten von der Ortszeit abweicht. Führen Sie das Installationsprogramm erneut aus, um die Registrierung durchzuführen.|
|06:28:45:Fehler beim Erstellen des Zertifikats.<br>06:28:45:Setup kann nicht fortgesetzt werden. Ein Zertifikat kann nicht erstellt werden, das für die Authentifizierung für Site Recovery erforderlich ist. Führen Sie Setup erneut aus. | Vergewissern Sie sich, dass Sie das Setup als lokaler Administrator ausführen. |
