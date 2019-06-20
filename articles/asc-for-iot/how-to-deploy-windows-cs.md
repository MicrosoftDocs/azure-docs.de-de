---
title: Windows-Installation des Azure Security Center für IoT-Agents (Vorschauversion) | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie den Azure Security Center für IoT-Agent auf Geräten unter Windows (32 Bit oder 64 Bit) installieren.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2019
ms.author: mlottner
ms.openlocfilehash: 64fc576885bb9d9c3c46aafd808db65d2f8ff77f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65200605"
---
# <a name="deploy-an-azure-security-center-for-iot-c-based-security-agent-for-windows"></a>Bereitstellen eines C#-basierten Sicherheits-Agents von Azure Security Center für IoT unter Windows

> [!IMPORTANT]
> Azure Security Center für IoT befindet sich derzeit in der öffentlichen Vorschauphase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

In diesem Leitfaden erfahren Sie, wie Sie den C#-basierten Sicherheits-Agent von Azure Security Center (ASC) für IoT unter Windows installieren.

In diesem Artikel lernen Sie Folgendes: 
> [!div class="checklist"]
> * Installieren
> * Überprüfen der Bereitstellung
> * Deinstallieren des Agents
> * Problembehandlung 

## <a name="prerequisites"></a>Voraussetzungen

Informationen zu anderen Plattformen und Agent-Varianten finden Sie unter [Select and deploy a security agent on your IoT device](how-to-deploy-agent.md) (Auswählen eines Sicherheits-Agents und Bereitstellen des Agents auf Ihrem IoT-Gerät).

1. Lokale Administratorrechte auf dem Computer, auf dem Sie installieren möchten. 

1. [Erstellen Sie ein Sicherheitsmodul](quickstart-create-security-twin.md) für das Gerät.

## <a name="installation"></a>Installation 

Gehen Sie zum Installieren des Sicherheits-Agents wie folgt vor:

1. Laden Sie zum Installieren des C#-basierten ASC für IoT-Agents (Windows) auf dem Gerät die neueste Version aus dem [GitHub-Repository](https://github.com/Azure/Azure-IoT-Security-Agent-CS) von ASC für IoT auf Ihren Computer herunter.

2. Extrahieren Sie den Inhalt des Pakets, und navigieren Sie zum Ordner „/Install“.

3. Öffnen Sie Windows PowerShell als Administrator. 
    1. Führen Sie ```Unblock-File .\InstallSecurityAgent.ps1``` aus, um dem Skript „InstallSecurityAgent“ Ausführungsberechtigungen hinzuzufügen.
    
        Führen Sie anschließend Folgendes aus:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```
    
    Beispiel:
    
    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
    ```
    
    Weitere Informationen zu Authentifizierungsparametern finden Sie unter [Security agent authentication methods](concept-security-agent-authentication-methods.md) (Authentifizierungsmethoden des Sicherheits-Agents).

Das Skript bewirkt Folgendes:

- Installieren der erforderlichen Komponenten

- Hinzufügen eines Dienstbenutzers (mit deaktivierter interaktiver Anmeldung)

- Installieren des Agents als **Systemdienst**

- Konfigurieren des Agents mit den angegebenen Authentifizierungsparametern


Weitere Hilfe erhalten Sie bei Bedarf mithilfe des Befehls „Get-Help“ in PowerShell. <br>Beispiel für „Get-Help“:  
    ```Get-Help .\InstallSecurityAgent.ps1```

### <a name="verify-deployment-status"></a>Überprüfen des Bereitstellungsstatus

- Führen Sie Folgendes aus, um den Bereitstellungsstatus des Agents zu überprüfen:<br>
    ```sc.exe query "ASC IoT Agent"```

### <a name="uninstall-the-agent"></a>Deinstallieren des Agents

So deinstallieren Sie den Agent:

1. Führen Sie das folgende PowerShell-Skript aus, und legen Sie dabei den Parameter **-mode** auf **Uninstall** fest:  

    ```
    .\InstallSecurityAgent.ps1 -Uninstall
    ``` 

## <a name="troubleshooting"></a>Problembehandlung

Sollte der Agent nicht starten, aktivieren Sie die Protokollierung, um weitere Informationen zu erhalten. (Die Protokollierung ist standardmäßig *deaktiviert*.)

So aktivieren Sie die Protokollierung:

1. Öffnen Sie die Konfigurationsdatei (General.config) für die Bearbeitung mit einem standardmäßigen Datei-Editor.

1. Bearbeiten Sie die folgenden Werte:

   ```xml
   <add key="logLevel" value="Debug" />
   <add key="fileLogLevel" value="Debug"/> 
   <add key="diagnosticVerbosityLevel" value="Some" /> 
   <add key="logFilePath" value="IoTAgentLog.log" />
   ```

    > [!NOTE]
    > Es empfiehlt sich, die Protokollierung nach Abschluss der Problembehandlung zu **deaktivieren**. Bei **aktivierter** Protokollierung erhöhen sich Protokolldateigröße und Datennutzung. 

1. Starten Sie den Agent über PowerShell oder über die Befehlszeile neu:

    **PowerShell**
     ```
     Restart-Service "ASC IoT Agent"
     ```
     
   oder

    **Befehlszeile**
     ```
     sc.exe stop "ASC IoT Agent" 
     sc.exe start "ASC IoT Agent" 
     ```

1. Sehen Sie sich die Protokolldatei an, um mehr über den Fehler zu erfahren.

   Speicherort der Protokolldatei: `%WinDir%/System32/IoTAgentLog.log`


## <a name="next-steps"></a>Nächste Schritte
- Lesen Sie die [Übersicht](overview.md) über ASC für IoT.
- Informieren Sie sich ausführlicher über die [Architektur](architecture.md) von ASC für IoT.
- Aktivieren Sie den [Dienst](quickstart-onboard-iot-hub.md).
- Lesen Sie die [häufig gestellten Fragen](resources-frequently-asked-questions.md).
- Machen Sie sich mit [Warnungen](concept-security-alerts.md) vertraut.