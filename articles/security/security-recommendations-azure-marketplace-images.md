---
title: Sicherheitsempfehlungen für Azure Marketplace-Images | Microsoft-Dokumentation
description: Dieser Artikel enthält Empfehlungen für Images im Marketplace.
services: security
documentationcenter: na
author: barclayn
manager: barbkess
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: barclayn
ms.openlocfilehash: fa521b81c95f7c0556b082e5487848ef4d7ecaf7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60444111"
---
# <a name="security-recommendations-for-azure-marketplace-images"></a>Sicherheitsempfehlungen für Azure Marketplace-Images

Alle Lösungen sollten den folgenden Empfehlungen zur Sicherheitskonfiguration entsprechen. Dadurch wird ein hohes Maß an Sicherheit für Images von Partnerlösungen im Azure Marketplace gewährleistet.

Diese Empfehlungen sind auch für Organisationen hilfreich, die keine Images im Azure Marketplace bereitstellen. Sie können die Konfigurationen der Windows- und Linux-Images Ihres Unternehmens anhand der Richtlinien in den folgenden Tabellen überprüfen:

## <a name="open-source-based-images"></a>Open-Source-basierte Images

|||
|--------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Kategorie**                                                 | **Überprüfung**                                                                                                                                                                                                                                                                              |
| Sicherheit                                                     | Alle aktuellen Sicherheitspatches für die Linux-Distribution sind installiert.                                                                                                                                                                                                              |
| Sicherheit                                                     | Branchenrichtlinien zum Schutz des VM-Images für die jeweilige Linux-Distribution wurden befolgt.                                                                                                                                                                                     |
| Sicherheit                                                     | Die Angriffsfläche wurde minimiert, indem nur die absolut erforderlichen Windows Server-Rollen, -Features, -Dienste und Netzwerkports aktiviert wurden.                                                                                                                                               |
| Sicherheit                                                     | Quellcode und resultierendes VM-Image wurden auf Schadsoftware gescannt.                                                                                                                                                                                                                                   |
| Sicherheit                                                     | Das VHD-Image enthält nur erforderliche gesperrte Konten, die über keine Standardkennwörter verfügen, die eine interaktive Anmeldung zulassen würden; keine Hintertüren.                                                                                                                                           |
| Sicherheit                                                     | Alle Firewallregeln, die nicht für die Funktionalität der Anwendung benötigt werden, z.B. für eine Firewall-Appliance, wurden deaktiviert.                                                                                                                                                                             |
| Sicherheit                                                     | Alle vertraulichen Informationen wurden aus dem VHD-Image entfernt – z.B. SSH-Testschlüssel, Datei der bekannten Hosts, Protokolldateien und nicht benötigte Zertifikate.                                                                                                                                       |
| Sicherheit                                                     | Es wird empfohlen, LVM nicht zu verwenden.                                                                                                                                                                                                                                            |
| Sicherheit                                                     | Neueste Versionen der erforderlichen Bibliotheken: </br> - OpenSSL v1.0 oder höher </br> - Python 2.5 oder höher (Python 2.6+ wird dringend empfohlen) </br> - Python-Paket pyasn1, falls nicht bereits installiert </br> - d.OpenSSL v1.0 oder höher                                                                |
| Sicherheit                                                     | Bash-/Shell-Verlaufseinträge müssen gelöscht werden.                                                                                                                                                                                                                                             |
| Netzwerk                                                   | Ein SSH-Server sollte standardmäßig enthalten sein. Legen Sie SSH-Keep-Alive mit der folgenden Option auf „sshd config“ fest: ClientAliveInterval 180                                                                                                                                                        |
| Netzwerk                                                   | Das Image darf keine benutzerdefinierte Netzwerkkonfiguration enthalten. Löschen Sie die Datei „resolv.conf“: `rm /etc/resolv.conf`                                                                                                                                                                                |
| Bereitstellung                                                   | Der neueste VM-Agent muss installiert sein. </br> - Der Agent sollte mithilfe des RPM- oder Deb-Pakets installiert werden.  </br> - Sie können auch den manuellen Installationsprozess verwenden, aber die Installationspakete werden empfohlen und bevorzugt. </br> - Wenn der Agent manuell aus dem GitHub-Repository installiert wird, kopieren Sie zunächst die Datei `waagent` nach `/usr/sbin`, und führen Sie (als root) Folgendes aus: </br>`# chmod 755 /usr/sbin/waagent` </br>`# /usr/sbin/waagent -install` </br>Die Agent-Konfigurationsdatei wird unter `/etc/waagent.conf` gespeichert.    |
| Bereitstellung                                                   | Stellt sicher, dass der Azure-Support unseren Partnern bei Bedarf die serielle Konsolenausgabe übermitteln kann und ein geeignetes Timeout für die Einbindung von Betriebssystem-Datenträgern aus Cloudspeicher bereitstellen kann. Der Startzeile für den Kernel des Images müssen außerdem folgende Parameter hinzugefügt werden: `console=ttyS0 earlyprintk=ttyS0 rootdelay=300` |
| Bereitstellung                                                   | Keine swap-Partition auf dem Betriebssystem-Datenträger. Swap kann durch den Linux-Agent für die Erstellung auf dem Datenträger der lokalen Ressource angefordert werden.         |
| Bereitstellung                                                   | Es wird empfohlen, eine einzelne Stammpartition für den Betriebssystem-Datenträger zu erstellen.      |
| Bereitstellung                                                   | Nur 64-Bit-Gastbetriebssysteme.                                                                                                                                                                                                                                                          |

## <a name="windows-server-based-images"></a>Windows Server-basierte Images

|||
|-------------| -------------------------|
| **Kategorie**                                                     | **Überprüfung**                                                                                                                                                                |
| Sicherheit                                                         | Es muss ein sicheres Basisimage für das Betriebssystem verwendet werden. Die für die Quelle von Images auf Basis von Windows Server verwendete virtuelle Festplatte (VHD) muss mit den Windows Server-Betriebssystemimages, die über Microsoft Azure bereitgestellt werden, erstellt werden. |
| Sicherheit                                                         | Es müssen alle aktuellen Sicherheitsupdates installiert sein.                                                                                                                                     |
| Sicherheit                                                         | Anwendungen dürfen nicht von eingeschränkten Benutzernamen wie „Administrator“, „root“ oder „admin“ abhängig sein.                                                                |
| Sicherheit                                                         | Die BitLocker-Laufwerkverschlüsselung wird auf der Betriebssystem-Festplatte nicht unterstützt. BitLocker kann auf Datenträgern für Arbeitsdaten verwendet werden.                                                            |
| Sicherheit                                                         | Die Angriffsfläche wurde minimiert, indem nur die absolut erforderlichen Windows Server-Rollen, -Features, -Dienste und Netzwerkports aktiviert wurden.                         |
| Sicherheit                                                         | Quellcode und resultierendes VM-Image wurden auf Schadsoftware gescannt.                                                                                                                     |
| Sicherheit                                                         | Für die Aktualisierung der Sicherheit von Windows Server-Images sollten automatische Updates festgelegt werden.                                                                                                                |
| Sicherheit                                                         | Das VHD-Image enthält nur erforderliche gesperrte Konten, die über keine Standardkennwörter verfügen, die eine interaktive Anmeldung zulassen würden; keine Hintertüren.                             |
| Sicherheit                                                         | Alle Firewallregeln, die nicht für die Funktionalität der Anwendung benötigt werden, z.B. für eine Firewall-Appliance, wurden deaktiviert.                                                               |
| Sicherheit                                                         | Alle vertraulichen Informationen wurden aus dem VHD-Image entfernt. Beispielsweise sollten die hosts-Datei, Protokolldateien und nicht benötigte Zertifikate entfernt werden.                                              |
| Bereitstellung                                                       | Nur 64-Bit-Gastbetriebssysteme.                            |
