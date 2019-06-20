---
title: Leitfaden zum Installieren und Bereitstellen des C#-basierten Linux-Agents von Azure Security Center für IoT (Vorschauversion) | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Azure Security Center für IoT-Agent unter Linux (32 Bit und 64 Bit) installieren.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: b0982203-c3c8-4a0b-8717-5b5ac4038d8c
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 5623b9870788edfb3b96ef248154e8b9f60b4593
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65198426"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>Bereitstellen des C#-basierten Sicherheits-Agents von Azure Security Center für IoT unter Linux

> [!IMPORTANT]
> Azure Security Center für IoT befindet sich derzeit in der öffentlichen Vorschauphase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

In diesem Leitfaden erfahren Sie, wie Sie den C#-basierten Sicherheits-Agent von Azure Security Center für IoT unter Linux installieren und bereitstellen.

In diesem Artikel lernen Sie Folgendes: 
> [!div class="checklist"]
> * Installieren
> * Überprüfen der Bereitstellung
> * Deinstallieren des Agents
> * Problembehandlung 

## <a name="prerequisites"></a>Voraussetzungen

Informationen zu anderen Plattformen und Agent-Varianten finden Sie unter [Choose the right security agent](how-to-deploy-agent.md) (Auswählen des richtigen Sicherheits-Agents).

1. Um den Sicherheits-Agent bereitstellen zu können, müssen Sie auf dem Computer, auf dem Sie ihn installieren möchten, über lokale Administratorrechte verfügen. 

1. [Erstellen Sie ein Sicherheitsmodul](quickstart-create-security-twin.md) für das Gerät.

## <a name="installation"></a>Installation 

Gehen Sie zum Bereitstellen des Sicherheits-Agents wie folgt vor:

1. Laden Sie von [GitHub](https://aka.ms/iot-security-github-cs) die neueste Version auf Ihren Computer herunter.

1. Extrahieren Sie den Inhalt des Pakets, und navigieren Sie zum Ordner _/Install_.

1. Führen Sie `chmod +x InstallSecurityAgent.sh` aus, um dem Skript **InstallSecurityAgent** Ausführungsberechtigungen hinzuzufügen. 

1. Führen Sie als Nächstes Folgendes aus: 

   ```
   ./InstallSecurityAgent.sh -i -aui <authentication identity>  -aum <authentication method> -f <file path> -hn <host name>  -di <device id> -cl <certificate location kind>
   ```
   
   Weitere Informationen zu Authentifizierungsparametern finden Sie unter [Security agent authentication methods](concept-security-agent-authentication-methods.md) (Authentifizierungsmethoden des Sicherheits-Agents).

Das Skript bewirkt Folgendes:

- Installieren der erforderlichen Komponenten

- Hinzufügen eines Dienstbenutzers (mit deaktivierter interaktiver Anmeldung)

- Installieren des Agents als **Daemon** (wobei vorausgesetzt wird, dass das Gerät **systemd** für die Dienstverwaltung verwendet)

- Konfigurieren von **sudoers**, damit der Agent bestimmte Aufgaben als Root ausführen kann

- Konfigurieren des Agents mit den angegebenen Authentifizierungsparametern


Sollten Sie weitere Hilfe benötigen, führen Sie das Skript mit dem Parameter „–help“ aus: `./InstallSecurityAgent.sh --help`

### <a name="uninstall-the-agent"></a>Deinstallieren des Agents

Wenn Sie den Agent deinstallieren möchten, führen Sie das Skript mit dem Parameter „–u“ aus: `./InstallSecurityAgent.sh -u`. 

> [!NOTE]
> Bei der Deinstallation werden keine der fehlenden erforderlichen Komponenten entfernt, die ggf. im Zuge der Installation installiert wurden.

## <a name="troubleshooting"></a>Problembehandlung  

1. Führen Sie Folgendes aus, um den Bereitstellungsstatus zu überprüfen:

    `systemctl status ASCIoTAgent.service`

2. Aktivieren Sie die Protokollierung.  
   Sollte der Agent nicht starten, aktivieren Sie die Protokollierung, um weitere Informationen zu erhalten.

   So aktivieren Sie die Protokollierung:

   1. Öffnen Sie die Konfigurationsdatei zur Bearbeitung in einem beliebigen Linux-Editor:

        `vi /var/ASCIoTAgent/General.config`

   1. Bearbeiten Sie die folgenden Werte: 

      ```
      <add key="logLevel" value="Debug"/>
      <add key="fileLogLevel" value="Debug"/>
      <add key="diagnosticVerbosityLevel" value="Some" /> 
      <add key="logFilePath" value="IotAgentLog.log"/>
      ```
       Der Wert **logFilePath** ist konfigurierbar. 

       > [!NOTE]
       > Es empfiehlt sich, die Protokollierung nach Abschluss der Problembehandlung zu **deaktivieren**. Bei **aktivierter** Protokollierung erhöhen sich Protokolldateigröße und Datennutzung.

   1. Führen Sie Folgendes aus, um den Agent neu zu starten:

       `systemctl restart ASCIoTAgent.service`

   1. Sehen Sie sich die Protokolldatei an, um mehr über den Fehler zu erfahren.  

       Speicherort der Protokolldatei: `/var/ASCIoTAgent/IotAgentLog.log`

       Ändern Sie den Dateipfad gemäß dem Namen, den Sie in Schritt 2 für **logFilePath** gewählt haben. 

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie die [Übersicht](overview.md) über ASC für IoT.
- Informieren Sie sich ausführlicher über die [Architektur](architecture.md) von ASC für IoT.
- Aktivieren Sie den [Dienst](quickstart-onboard-iot-hub.md).
- Lesen Sie die [häufig gestellten Fragen](resources-frequently-asked-questions.md).
- Machen Sie sich mit [Warnungen](concept-security-alerts.md) vertraut.