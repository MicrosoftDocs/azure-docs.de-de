---
title: Leitfaden zum Installieren und Bereitstellen des C-basierten Linux-Agents von Azure Security Center für IoT (Vorschauversion) | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Azure Security Center für IoT-Agent unter Linux (32 Bit und 64 Bit) installieren.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 3ccf2aec-106a-4d2c-8079-5f3e8f2afdcb
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2019
ms.author: mlottner
ms.openlocfilehash: 49ebb5932e1e918330625fd0df98811873dd5cd5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65200656"
---
# <a name="deploy-azure-security-center-for-iot-c-based-security-agent-for-linux"></a>Bereitstellen des C-basierten Sicherheits-Agents von Azure Security Center für IoT unter Linux

> [!IMPORTANT]
> Azure Security Center für IoT befindet sich derzeit in der öffentlichen Vorschauphase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

In diesem Leitfaden erfahren Sie, wie Sie den C-basierten Sicherheits-Agent von Azure Security Center (ASC) für IoT unter Linux installieren und bereitstellen.

In diesem Artikel lernen Sie Folgendes: 
> [!div class="checklist"]
> * Installieren
> * Überprüfen der Bereitstellung
> * Deinstallieren des Agents
> * Problembehandlung 

## <a name="prerequisites"></a>Voraussetzungen

Informationen zu anderen Plattformen und Agent-Varianten finden Sie unter [Choose the right security agent](how-to-deploy-agent.md) (Auswählen des richtigen Sicherheits-Agents).

1. Um den Sicherheits-Agent bereitstellen zu können, müssen Sie auf dem Computer, auf dem Sie ihn installieren möchten, über lokale Administratorrechte verfügen (sudo).

1. [Erstellen Sie ein Sicherheitsmodul](quickstart-create-security-twin.md) für das Gerät.

## <a name="installation"></a>Installation 

Gehen Sie zum Installieren und Bereitstellen des Sicherheits-Agents wie folgt vor:


1. Laden Sie von [GitHub](https://aka.ms/iot-security-github-c) die neueste Version auf Ihren Computer herunter.

1. Extrahieren Sie den Inhalt des Pakets, und navigieren Sie zum Ordner _/Install_.

1. Führen Sie Folgendes aus, um dem Skript **InstallSecurityAgent** Ausführungsberechtigungen hinzuzufügen:
    
   ```
   chmod +x InstallSecurityAgent.sh
   ```

1. Führen Sie anschließend Folgendes aus: 

   ```
   ./InstallSecurityAgent.sh -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -i
   ```
   
   Weitere Informationen zu Authentifizierungsparametern finden Sie unter [Security agent authentication methods](concept-security-agent-authentication-methods.md) (Authentifizierungsmethoden des Sicherheits-Agents).

Das Skript bewirkt Folgendes:

1. Installieren der erforderlichen Komponenten

2. Hinzufügen eines Dienstbenutzers (mit deaktivierter interaktiver Anmeldung)

3. Installieren des Agents als **Daemon** (wobei vorausgesetzt wird, dass das Gerät **systemd** für die Dienstverwaltung verwendet)

4. Konfigurieren des Agents mit den angegebenen Authentifizierungsparametern 

Sollten Sie weitere Hilfe benötigen, führen Sie das Skript mit dem Parameter „–help“ aus: 
    
    ./InstallSecurityAgent.sh --help

### <a name="uninstall-the-agent"></a>Deinstallieren des Agents

Wenn Sie den Agent deinstallieren möchten, führen Sie das Skript mit dem Parameter „–uninstall“ aus:

    ./InstallSecurityAgent.sh -–uninstall

## <a name="troubleshooting"></a>Problembehandlung
Führen Sie Folgendes aus, um den Bereitstellungsstatus zu überprüfen:

    systemctl status ASCIoTAgent.service


## <a name="next-steps"></a>Nächste Schritte
- Lesen Sie die [Übersicht](overview.md) über ASC für IoT.
- Informieren Sie sich ausführlicher über die [Architektur](architecture.md) von ASC für IoT.
- Aktivieren Sie den [Dienst](quickstart-onboard-iot-hub.md).
- Lesen Sie die [häufig gestellten Fragen](resources-frequently-asked-questions.md).
- Machen Sie sich mit [Sicherheitswarnungen](concept-security-alerts.md) vertraut.