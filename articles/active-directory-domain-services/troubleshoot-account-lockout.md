---
title: Troubleshooting der Kontosperrung in Azure AD Domain Services | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie häufige Probleme beheben können, die zu Kontosperrungen in Azure Active Directory Domain Services führen.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 7967347fa63c657ba6211328bdd1d55512358521
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96618772"
---
# <a name="troubleshoot-account-lockout-problems-with-an-azure-active-directory-domain-services-managed-domain"></a>Beheben von Problemen mit gesperrten Konten bei einer verwalteten Azure Active Directory Domain Services-Domäne

Um wiederholte schädliche Anmeldeversuche zu verhindern, sperrt eine verwaltete Azure Active Directory Domain Services (Azure AD DS)-Domäne die Konten nach einem definierten Schwellenwert. Die Kontosperrung kann versehentlich auch ohne einen Angriffsvorfall bei der Anmeldung erfolgen. Wenn ein Benutzer beispielsweise wiederholt das falsche Kennwort eingibt oder ein Dienst versucht, ein altes Kennwort zu verwenden, wird das Konto gesperrt.

Dieser Troubleshooting-Artikel beschreibt, aus welchen Gründen Konten gesperrt werden können. Außerdem erfahren Sie, wie Sie das Verhalten konfigurieren und wie Sie protokollierte Sicherheitsüberwachungen auswerten, um Sperrvorfälle zu beheben.

## <a name="what-is-an-account-lockout"></a>Was ist eine Kontosperrung?

Benutzerkonten werden in einer verwalteten Azure AD DS-Domäne gesperrt, wenn ein festgelegter Schwellenwert für fehlgeschlagene Anmeldeversuche erreicht wurde. Das Verhalten bei Kontosperrungen ist so konzipiert, dass Sie vor wiederholt versuchten Brute-Force-Anmeldungen geschützt werden, die auf einen automatisierten digitalen Angriff hinweisen können.

**Wenn in 2 Minuten 5 Versuche mit ungültigen Kennwörtern vorliegen, wird das Konto standardmäßig für 30 Minuten gesperrt.**

Die Standard-Schwellenwerte für die Kontosperrung werden mithilfe differenzierter Kennwortrichtlinien konfiguriert. Sie können diese standardmäßigen Schwellenwerte für Kontosperren Ihren spezifischen Anforderungen entsprechend außer Kraft setzen. Jedoch raten wir davon ab, die Schwellenwerte mit dem Ziel zu erhöhen, die Anzahl der Kontosperrungen zu reduzieren. Beheben Sie zunächst die Ursache der Kontosperrungen.

### <a name="fine-grained-password-policy"></a>Eine differenzierte Kennwortrichtlinie

Mit differenzierten Kennwortrichtlinien (FGPP) können Sie bestimmte Einschränkungen für Kennwort- und Kontosperrungsrichtlinien auf verschiedene Benutzer in einer Domäne anwenden. Differenzierte Kennwortrichtlinien betreffen nur Benutzer in einer verwalteten Domäne. Cloudbenutzer und Domänenbenutzer, die von Azure AD in der verwalteten Domäne synchronisiert werden, sind nur innerhalb der verwalteten Domäne von den Kennwortrichtlinien betroffen. Deren Konten in Azure AD oder in einem lokalen Verzeichnis sind davon nicht betroffen.

Richtlinien werden über die Gruppenzuordnung in der verwalteten Domäne verteilt. Vorgenommene Änderungen werden bei der nächsten Benutzeranmeldung angewendet. Wenn Sie die Richtlinie ändern, wird ein bereits gesperrtes Benutzerkonto nicht entsperrt.

Weitere Informationen zu differenzierten Kennwortrichtlinien sowie zu den Unterschieden zwischen Benutzern, die direkt in Azure AD DS erstellt werden, und Benutzern, die über Azure AD synchronisiert werden, finden Sie unter [Konfigurieren von Kennwort- und Kontosperrungsrichtlinien][configure-fgpp].

## <a name="common-account-lockout-reasons"></a>Allgemeine Gründe für Kontosperrungen

Folgende Szenarien stellen die häufigsten Gründe für eine Kontosperrung dar, wenn böswillige Absichten oder Faktoren keine Rolle spielen:

* **Der Benutzer hat sich selbst gesperrt.**
    * Hat der Benutzer nach einer kürzlichen Kennwortänderung das vorherige Kennwort weiterverwendet? Die Standardrichtlinie für Kontosperrungen (5 fehlgeschlagene Versuche in 2 Minuten) kann durch einen Benutzer ausgelöst werden, der versehentlich ein altes Kennwort verwendet.
* **Eine Anwendung oder ein Dienst nutzt ein altes Kennwort.**
    * Wenn ein Konto von Anwendungen oder Diensten verwendet wird, können diese Ressourcen wiederholt versuchen, sich mit einem alten Kennwort anzumelden. Dieses Verhalten bewirkt, dass das Konto gesperrt wird.
    * Versuchen Sie, die Nutzung eines Kontos in mehreren unterschiedlichen Apps oder Diensten einzuschränken und vermerken Sie, wo Anmeldeinformationen genutzt werden. Wenn ein Kontokennwort geändert wird, aktualisieren Sie die zugehörigen Anwendungen oder Dienste entsprechend.
* **Das Kennwort wurde in einer anderen Umgebung geändert, und das neue Kennwort wurde noch nicht synchronisiert.**
    * Beim Ändern eines Kontokennworts außerhalb der verwalteten Domäne, z. B. in einer lokalen AD DS-Umgebung, kann es einige Minuten dauern, bis die Kennwortänderung über Azure AD und in der verwalteten Domäne synchronisiert wird.
    * Wenn ein Benutzer vor Abschluss der Kennwortsynchronisierung versucht, sich bei einer Ressource in der verwalteten Domäne anzumelden, hat dies die Sperrung des Kontos zur Folge.

## <a name="troubleshoot-account-lockouts-with-security-audits"></a>Troubleshooting für Kontosperrungen mit protokollierten Sicherheitsüberwachungen

Aktivieren Sie zur Problembehandlung bei auftretenden Kontosperrungsereignissen [Sicherheitsüberwachungen für Azure AD Domain Services][security-audit-events]. Überwachungsereignisse werden nur ab der Aktivierung des Features erfasst. Im Idealfall sollten Sie Sicherheitsüberwachungen bereits aktivieren, *bevor* ein zu behebendes Problem mit Kontosperrungen auftritt. Wenn bei einem Benutzerkonto wiederholt Probleme mit Kontosperrungen auftreten, können Sie die Sicherheitsüberwachung im Hinblick auf den nächsten Vorfall aktivieren.

Nach Aktivierung der Sicherheitsüberwachung zeigen Ihnen die folgenden Musterabfragen, wie Sie *Kontosperrungsereignisse*, Code *4740*, auswerten.

Alle Kontosperrungsereignisse der letzten sieben Tage anzeigen:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

Alle Kontosperrungsereignisse der letzten sieben Tage für das Konto *driley* anzeigen.

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
| where "driley" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

Alle Kontosperrungsereignisse zwischen 26. Juni 2020, 9.00 Uhr, und 1. Juli 2020 um Mitternacht, aufsteigend nach Datum und Uhrzeit anzeigen:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2020-06-26 09:00) and TimeGenerated <= datetime(2020-07-01)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu differenzierten Kennwortrichtlinien finden Sie unter [Konfigurieren von Richtlinien für Kennwörter und Kontosperrungen][configure-fgpp].

Sollten beim Einbinden Ihres virtuellen Computers in die verwaltete Domäne weiterhin Probleme auftreten, [öffnen Sie ein Supportticket für Azure Active Directory][azure-ad-support], um Hilfe zu erhalten.

<!-- INTERNAL LINKS -->
[configure-fgpp]: password-policy.md
[security-audit-events]: security-audit-events.md
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
