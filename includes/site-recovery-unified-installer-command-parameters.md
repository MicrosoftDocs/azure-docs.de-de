---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 8076a6af2382ccec1ac832cd83c3946d8c7c6f57
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019134"
---
|Parametername| type | BESCHREIBUNG| Mögliche Werte|
|-|-|-|-|
| /ServerMode|Obligatorisch.|Gibt an, ob die Installation sowohl den Konfigurations- als auch den Prozessserver oder nur den Prozessserver umfassen soll.|CS<br>PS|
|/InstallLocation|Obligatorisch.|Der Ordner, in dem die Komponenten installiert werden.| Beliebiger Ordner auf dem Computer|
|/MySQLCredsFilePath|Obligatorisch.|Der Dateipfad, unter dem die MySQL Server-Anmeldeinformationen gespeichert werden.|Die Datei sollte das unten angegebene Format haben.|
|/VaultCredsFilePath|Obligatorisch.|Pfad der Datei mit Anmeldeinformationen für den Tresor|Gültiger Dateipfad|
|/EnvType|Obligatorisch.|Typ der zu schützenden Umgebung |VMware<br>NonVMware|
|/PSIP|Obligatorisch.|IP-Adresse der Netzwerkkarte für die Übertragung von Replikationsdaten| Beliebige gültige IP-Adresse|
|/CSIP|Obligatorisch.|IP-Adresse der Netzwerkkarte, über die der Konfigurationsserver lauscht| Beliebige gültige IP-Adresse|
|/PassphraseFilePath|Obligatorisch.|Vollständiger Pfad zum Speicherort der Datei mit der Passphrase|Gültiger Dateipfad|
|/BypassProxy|Optional|Gibt an, dass der Konfigurationsserver die Verbindung mit Azure ohne Proxy herstellt.||
|/ProxySettingsFilePath|Optional|Proxyeinstellungen (für den standardmäßigen Proxy ist eine Authentifizierung oder ein benutzerdefinierter Proxy erforderlich).|Die Datei sollte das unten angegebene Format haben.|
|DataTransferSecurePort|Optional|Die Portnummer der PSIP, die für Replikationsdaten verwendet werden soll.| Gültige Portnummer (Standardwert ist 9433)|
|/SkipSpaceCheck|Optional|Überspringt die Überprüfung des Speicherplatzes für den Cachedatenträger.| |
|/AcceptThirdpartyEULA|Obligatorisch.|Das Flag impliziert die Akzeptanz von Drittanbieterlizenzbedingungen.| |
|/ShowThirdpartyEULA|Optional|Zeigt Drittanbieterlizenzbedingungen an. Alle anderen Parameter werden ignoriert, wenn sie als Eingabe bereitgestellt werden.| |
