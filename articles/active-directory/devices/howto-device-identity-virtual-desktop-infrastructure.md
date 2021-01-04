---
title: Geräteidentität und Desktopvirtualisierung – Azure Active Directory
description: Erfahren Sie, wie Sie die virtuelle Desktopinfrastruktur (VDI) und Azure AD-Geräteidentitäten gemeinsam verwenden können.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: c30ad26f079e6353dc4763b9ae968c33882d8ab6
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96029346"
---
# <a name="device-identity-and-desktop-virtualization"></a>Geräteidentität und Desktopvirtualisierung

Administratoren stellen in ihren Organisationen häufig VDI-Plattformen (Virtual Desktop Infrastructure) bereit, auf denen Windows-Betriebssysteme gehostet werden. Administratoren stellen VDI-Plattformen für Folgendes bereit:

- Einfachere Verwaltung
- Kostensenkung durch Konsolidierung und Zentralisierung von Ressourcen
- Endbenutzermobilität mit der Freiheit, jederzeit, überall und von jedem Gerät aus auf virtuelle Desktops zuzugreifen

Es gibt zwei Hauptarten von virtuellen Desktops:

- Beständig
- Nicht beständig

Bei der beständigen Version wird ein eindeutiges Desktopimage für jeden Benutzer oder einen Benutzerpool verwendet. Diese eindeutigen Desktops können angepasst und zur späteren Verwendung gespeichert werden. 

Bei der nicht beständigen Version wird eine Sammlung von Desktops verwendet, auf die Benutzer bei Bedarf zugreifen können. Diese nicht beständigen Desktops werden in ihren ursprünglichen Zustand zurückversetzt. Bei aktuellen Windows-Geräten<sup>1</sup> geschieht dies, wenn ein virtueller Computer heruntergefahren, neu gestartet oder sein Betriebssystem zurückgesetzt wird. Bei kompatiblen Windows-Geräten<sup>2</sup> geschieht dies, wenn ein Benutzer sich abmeldet.

Die Anzahl von nicht beständigen VDI-Bereitstellungen hat zugenommen, da Remotearbeit weiterhin stark verbreitet ist. Wenn Kunden nicht beständige VDI-Plattformen bereitstellen, ist es wichtig, den häufigen Wechsel von Geräten zu verwalten. Dabei kommt es möglicherweise zu Problemen, wenn eine häufige Geräteregistrierung erfolgt, ohne dass eine geeignete Strategie für die Verwaltung des Gerätelebenszyklus vorhanden ist.

> [!IMPORTANT]
> Die mangelnde Verwaltung des Wechsels von Geräten kann den Druck auf die Nutzung der Mandantenkontingente erhöhen und zu Dienstunterbrechungen führen, wenn die Anzahl der Mandantenkontingente nicht ausreicht. Um diese Situation zu vermeiden, beachten Sie den folgenden Leitfaden, wenn Sie nicht beständige VDI-Umgebungen bereitstellen.

Dieser Artikel enthält den Microsoft-Leitfaden für Administratoren zur Unterstützung von Geräteidentität und VDI. Weitere Informationen zur Geräteidentität finden Sie im Artikel [Was ist eine Geräteidentität](overview.md).

## <a name="supported-scenarios"></a>Unterstützte Szenarios

Bevor Sie die Geräteidentitäten in Azure AD für Ihre VDI-Umgebung konfigurieren, machen Sie sich mit den unterstützten Szenarien vertraut. In der folgenden Tabelle wird veranschaulicht, welche Bereitstellungsszenarien unterstützt werden. Eine Bereitstellung in diesem Zusammenhang impliziert, dass ein Administrator Geräteidentitäten skaliert konfigurieren kann, ohne dass eine Endbenutzerinteraktion erforderlich ist.

| Geräteidentitätstyp | Identitätsinfrastruktur | Windows-Geräte | VDI-Plattformversion | Unterstützt |
| --- | --- | --- | --- | --- |
| Hybrid in Azure AD eingebunden | Im Verbund<sup>3</sup> | Aktuelle Windows-Geräte und kompatible Windows-Geräte | Beständig | Ja |
|   |   | Aktuelle Windows-Geräte | Nicht beständig | Ja<sup>5</sup> |
|   |   | Kompatible Windows-Geräte | Nicht beständig | Ja<sup>6</sup> |
|   | Verwaltet<sup>4</sup> | Aktuelle Windows-Geräte und kompatible Windows-Geräte | Beständig | Ja |
|   |   | Aktuelle Windows-Geräte | Nicht beständig | Nein |
|   |   | Kompatible Windows-Geräte | Nicht beständig | Ja<sup>6</sup> |
| In Azure AD eingebunden | Im Verbund | Aktuelle Windows-Geräte | Beständig | Nein |
|   |   |   | Nicht beständig | Nein |
|   | Verwaltet | Aktuelle Windows-Geräte | Beständig | Nein |
|   |   |   | Nicht beständig | Nein |
| Bei Azure AD registriert | Im Verbund/verwaltet | Aktuelle Windows-Geräte/kompatible Windows-Geräte | Beständig/nicht beständig | Nicht zutreffend |

<sup>1</sup> **Aktuelle Windows-Geräte** bezieht sich auf Geräte unter Windows 10, Windows Server 2016 v1803 oder höher und Windows Server 2019.
<sup>2</sup> **Kompatible Windows-Geräte** bezieht sich auf Geräte mit Windows 7, Windows 8.1, Windows Server 2008 R2, Windows Server 2012 und Windows Server 2012 R2. Supportinformationen zu Windows 7 finden Sie unter [Der Support für Windows 7 läuft aus](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support). Supportinformationen zu Windows Server 2008 R2 finden Sie unter [Der Support für Windows Server 2008 wird eingestellt](https://www.microsoft.com/cloud-platform/windows-server-2008).

<sup>3</sup> Eine Identitätsinfrastruktur-Umgebung des Typs **Im Verbund** ist eine Umgebung mit einem Identitätsanbieter wie AD FS oder einem Drittanbieter.

<sup>4</sup> Eine Identitätsinfrastruktur-Umgebung des Typs **Verwaltet** ist eine Umgebung mit Azure AD als Identitätsanbieter und wird entweder mit [Kennworthashsynchronisierung (PHS)](../hybrid/whatis-phs.md) oder [Passthrough-Authentifizierung (PTA)](../hybrid/how-to-connect-pta.md) und [nahtloser einmaliger Anmeldung](../hybrid/how-to-connect-sso.md) bereitgestellt.

<sup>5</sup> **Die Unterstützung nicht beständiger Versionen für aktuelle Windows-Geräte** erfordert zusätzliche Überlegungen, wie unten im Leitfaden beschrieben. Für dieses Szenario ist Windows 10 1803, Windows Server 2019 oder Windows Server (halbjährlicher Kanal) ab Version 1803 erforderlich.

<sup>6</sup> **Die Unterstützung nicht beständiger Versionen für kompatible Windows-Geräte** erfordert zusätzliche Überlegungen, wie unten im Leitfaden beschrieben.


## <a name="microsofts-guidance"></a>Leitfaden von Microsoft

Administratoren sollten auf Grundlage ihrer Identitätsinfrastruktur die folgenden Artikel heranziehen, um zu erfahren, wie sie die Azure AD-Hybrideinbindung konfigurieren können.

- [Konfigurieren der Azure Active Directory-Hybrideinbindung für eine Verbundumgebung](hybrid-azuread-join-federated-domains.md)
- [Konfigurieren der Azure Active Directory-Hybrideinbindung für eine verwaltete Umgebung](hybrid-azuread-join-managed-domains.md)

Microsoft empfiehlt IT-Administratoren, den folgenden Leitfaden zu implementieren, wenn eine nicht beständige VDI-Plattform bereitgestellt wird. Wenn dies nicht beachtet wird, enthält Ihr Verzeichnis eine Vielzahl veralteter, in Azure AD Hybrid eingebundener Geräte, die über Ihre nicht beständige VDI-Plattform registriert wurden. Dies erhöht den Druck auf die Mandantenkontingente und kann zu Dienstunterbrechungen führen, wenn die Anzahl der Mandantenkontingente nicht ausreicht.

- Wenn Sie das Systemvorbereitungstool (sysprep.exe) und ein Image einer niedrigeren Version als Windows 10 1809 für die Installation verwenden, stellen Sie sicher, dass dieses Image nicht von einem Gerät stammt, das bereits als Azure AD-Hybrideinbindung bei Azure AD registriert ist.
- Wenn Sie zusätzliche VMs mit einer Momentaufnahme des virtuellen Computers erstellen, stellen Sie sicher, dass diese Momentaufnahme nicht von einem Computer stammt, der bereits als Azure AD-Hybrideinbindung bei Azure AD registriert ist.
- Erstellen und verwenden Sie ein Präfix für den Anzeigenamen (z. B. „NBVDI-“) des Computers, das darauf hinweist, dass der Desktop VDI-basiert und nicht beständig ist.
- Für kompatible Windows-Geräte:
   - Implementieren Sie den Befehl **autoworkplacejoin /leave** als Teil des Abmeldeskripts. Dieser Befehl sollte im Kontext des Benutzers ausgelöst werden und ausgeführt werden, bevor sich der Benutzer vollständig abgemeldet hat, während noch Netzwerkkonnektivität besteht.
- Für aktuelle Windows-Geräte in einer Verbundumgebung (z. B. AD FS):
   - Implementieren Sie **dsregcmd /join** als Teil der VM-Startsequenz.
   - Führen Sie „dsregcmd /leave“ **NICHT** als Teil des Prozesses zum Herunterfahren/Neustarten der VM aus.
- Definieren und implementieren Sie einen Prozess zum [Verwalten veralteter Geräte](manage-stale-devices.md).
   - Sobald Sie über eine Strategie zum Identifizieren Ihrer nicht beständigen, in Azure AD Hybrid eingebundenen Geräte verfügen (z. B. die Verwendung eines Präfixes für den Computeranzeigenamen), können Sie die Bereinigung dieser Geräte aggressiver betreiben, um sicherzustellen, dass Ihr Verzeichnis nicht von vielen veralteten Geräten genutzt wird.
   - Für nicht beständige VDI-Bereitstellungen von aktuellen und kompatiblen Windows-Geräten sollten Sie Geräte löschen, deren **ApproximateLastLogonTimestamp** älter als 15 Tage ist.
 
## <a name="next-steps"></a>Nächste Schritte

[Konfigurieren der Azure Active Directory-Hybrideinbindung für eine Verbundumgebung](hybrid-azuread-join-federated-domains.md)
