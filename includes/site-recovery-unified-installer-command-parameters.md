---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 9d9790c9b3dbe3b130be999dd76092ae64f7b52c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177797"
---
|Parametername| Type | BESCHREIBUNG| Mögliche Werte|
|-|-|-|-|
| /ServerMode|Erforderlich|Gibt an, ob die Installation sowohl den Konfigurations- als auch den Prozessserver oder nur den Prozessserver umfassen soll.|CS<br>PS|
|/InstallLocation|Erforderlich|Der Ordner, in dem die Komponenten installiert werden.| Beliebiger Ordner auf dem Computer|
|/MySQLCredsFilePath|Erforderlich|Der Dateipfad, unter dem die MySQL Server-Anmeldeinformationen gespeichert werden.|Die Datei sollte das unten angegebene Format haben.|
|/VaultCredsFilePath|Erforderlich|Pfad der Datei mit Anmeldeinformationen für den Tresor|Gültiger Dateipfad|
|/EnvType|Erforderlich|Typ der zu schützenden Umgebung |VMware<br>NonVMware|
|/PSIP|Erforderlich|IP-Adresse der Netzwerkkarte für die Übertragung von Replikationsdaten| Beliebige gültige IP-Adresse|
|/CSIP|Erforderlich|IP-Adresse der Netzwerkkarte, über die der Konfigurationsserver lauscht| Beliebige gültige IP-Adresse|
|/PassphraseFilePath|Erforderlich|Vollständiger Pfad zum Speicherort der Datei mit der Passphrase|Gültiger Dateipfad|
|/BypassProxy|Optional|Gibt an, dass der Konfigurationsserver die Verbindung mit Azure ohne Proxy herstellt.|Zum Abrufen dieses Werts von Venu|
|/ProxySettingsFilePath|Optional|Proxyeinstellungen (für den standardmäßigen Proxy ist eine Authentifizierung oder ein benutzerdefinierter Proxy erforderlich).|Die Datei sollte das unten angegebene Format haben.|
|DataTransferSecurePort|Optional|Die Portnummer der PSIP, die für Replikationsdaten verwendet werden soll.| Gültige Portnummer (Standardwert ist 9433)|
|/SkipSpaceCheck|Optional|Überspringt die Überprüfung des Speicherplatzes für den Cachedatenträger.| |
|/AcceptThirdpartyEULA|Erforderlich|Das Flag impliziert die Akzeptanz von Drittanbieterlizenzbedingungen.| |
|/ShowThirdpartyEULA|Optional|Zeigt Drittanbieterlizenzbedingungen an. Alle anderen Parameter werden ignoriert, wenn sie als Eingabe bereitgestellt werden.| |
