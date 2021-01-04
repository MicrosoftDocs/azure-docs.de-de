---
title: Transport Layer Security in Azure Backup
description: Erfahren Sie, wie Sie für Azure Backup die Verwendung von TLS (Transport Layer Security) für das Verschlüsselungsprotokoll aktivieren, um Daten bei der Übertragung über ein Netzwerk zu schützen.
ms.topic: conceptual
ms.date: 11/01/2020
ms.openlocfilehash: ba9c9d91f562f54695a0739908c8a409d14d5852
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327116"
---
# <a name="transport-layer-security-in-azure-backup"></a>Transport Layer Security in Azure Backup

Transport Layer Security (TLS) ist ein Verschlüsselungsprotokoll, mit dem Daten bei der Übertragung über ein Netzwerk geschützt werden. Azure Backup verwendet TLS, um den Datenschutz der Sicherungsdaten bei der Übertragung zu gewährleisten. In diesem Artikel werden die Schritte zum Aktivieren des TLS 1.2-Protokolls beschrieben, das gegenüber früheren Versionen eine höhere Sicherheit bietet.

## <a name="earlier-versions-of-windows"></a>Frühere Windows-Versionen

Wenn auf dem Computer frühere Versionen von Windows ausgeführt werden, müssen die unten aufgeführten Updates installiert und die in den KB-Artikeln dokumentierten Registrierungsänderungen angewandt werden.

|Betriebssystem  |KB-Artikel |
|---------|---------|
|Windows Server 2008 SP2   |   <https://support.microsoft.com/help/4019276>      |
|Windows Server 2008 R2, Windows 7, Windows Server 2012   | <https://support.microsoft.com/help/3140245>         |

>[!NOTE]
>Mit dem Update werden die erforderlichen Protokollkomponenten installiert. Nach der Installation müssen Sie die in den KB-Artikeln oben erwähnten Änderungen an den Registrierungsschlüsseln vornehmen, um die erforderlichen Protokolle ordnungsgemäß zu aktivieren.

## <a name="verify-windows-registry"></a>Überprüfen der Windows-Registrierung

### <a name="configuring-schannel-protocols"></a>Konfigurieren von SChannel-Protokollen

Die folgenden Registrierungsschlüssel stellen sicher, dass das TLS 1.2-Protokoll auf der Ebene der SChannel-Komponente aktiviert ist:

```reg
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
    "Enabled"=dword:00000001

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
    "DisabledByDefault"=dword:00000000
```

>[!NOTE]
>Die gezeigten Werte werden in Windows Server 2012 R2 und neueren Versionen standardmäßig festgelegt. Wenn die Registrierungsschlüssel nicht vorhanden sind, müssen sie für diese Versionen von Windows nicht erstellt werden.

### <a name="configuring-net-framework"></a>Konfigurieren von .NET Framework

Mit den folgenden Registrierungsschlüsseln wird .NET Framework für die Unterstützung starker Kryptografie konfiguriert. Weitere Informationen zum Konfigurieren von .NET Framework finden Sie [hier](/dotnet/framework/network-programming/tls#configuring-schannel-protocols-in-the-windows-registry).

```reg
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
    "SystemDefaultTlsVersions"=dword:00000001
    "SchUseStrongCrypto" = dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
    "SystemDefaultTlsVersions"=dword:00000001
    "SchUseStrongCrypto" = dword:00000001
```

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="why-enable-tls-12"></a>Warum muss TLS 1.2 aktiviert werden?

TLS 1.2 ist sicherer als ältere Kryptografieprotokolle wie SSL 2.0, SSL 3.0, TLS 1.0 und TLS 1.1. Azure Backup-Dienste unterstützen TLS 1.2 bereits vollständig.

### <a name="what-determines-the-encryption-protocol-used"></a>Wodurch wird das verwendete Verschlüsselungsprotokoll festgelegt?

Beim Einrichten der verschlüsselten Konversation wird die höchste Protokollversion ausgehandelt, die sowohl vom Client als auch vom Server unterstützt wird. Weitere Informationen zum TLS-Handshakeprotokoll finden Sie unter [Einrichten einer sicheren Sitzung mithilfe von TLS](/windows/win32/secauthn/tls-handshake-protocol#establishing-a-secure-session-by-using-tls).

### <a name="what-is-the-impact-of-not-enabling-tls-12"></a>Welche Auswirkungen hat es, wenn TLS 1.2 nicht aktiviert wird?

Um den Schutz vor Angriffen durch Protokolldowngrades zu verbessern, beginnt Azure Backup damit, TLS-Versionen vor 1.2 stufenweise zu deaktivieren. Dies ist Teil einer langfristigen Umstellung aller Dienste, um Verbindungen mit älteren Protokollen und Verschlüsselungssammlungen zu unterbinden. Azure Backup-Dienste und -Komponenten unterstützen TLS 1.2 vollständig. Windows-Versionen, auf denen die erforderlichen Updates oder bestimmte angepasste Konfigurationen fehlen, können jedoch dennoch verhindern, dass TLS 1.2-Protokolle bereitgestellt werden. Dies kann unter anderem zu folgenden Fehlern führen:

- Fehler bei Sicherungs- und Wiederherstellungsvorgängen
- Verbindungsfehler 10054 bei Sicherungskomponenten. (Eine vorhandene Verbindung wurde erzwungenermaßen vom Remotehost geschlossen.)
- Ungewöhnliche Starts oder Beendigungen von Diensten im Zusammenhang mit Azure Backup

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Transport Layer Security-Protokoll](/windows/win32/secauthn/transport-layer-security-protocol)
- [Sicherstellen der Unterstützung von TLS 1.2 in den bereitgestellten Betriebssystemen](/security/engineering/solving-tls1-problem#ensuring-support-for-tls-12-across-deployed-operating-systems)
- [Bewährte Methoden für Transport Layer Security (TLS) mit .NET Framework](/dotnet/framework/network-programming/tls)