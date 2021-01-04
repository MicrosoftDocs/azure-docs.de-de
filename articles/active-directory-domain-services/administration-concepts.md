---
title: Verwaltungskonzepte für Azure AD Domain Services | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie eine verwaltete Azure Active Directory Domain Services-Domäne und das Verhalten von Benutzerkonten und Kennwörtern verwalten.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: justinha
ms.openlocfilehash: 76af477af8290634d2658c58574b8f96cff7b1fb
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96620340"
---
# <a name="management-concepts-for-user-accounts-passwords-and-administration-in-azure-active-directory-domain-services"></a>Verwaltungskonzepte für Benutzerkonten, Kennwörter und die Verwaltung in Azure Active Directory Domain Services

Beim Erstellen und Ausführen einer verwalteten Azure AD DS-Domäne (Active Directory Domain Services) gibt es einige Unterschiede im Verhalten im Vergleich zu einer herkömmlichen lokalen AD DS-Umgebung. Sie verwenden dieselben Verwaltungstools in Azure AD DS wie eine selbstverwaltete Domäne, aber Sie können nicht direkt auf die Domänencontroller (DC) zugreifen. Es gibt auch einige Unterschiede im Verhalten von Kennwortrichtlinien und Kennworthashes, die von der Quelle der Erstellung des Benutzerkontos abhängig sind.

In diesem konzeptionellen Artikel erfahren Sie, wie Sie eine verwaltete Domäne und das unterschiedliche Verhalten von Benutzerkonten abhängig von der Art ihrer Erstellung verwalten.

## <a name="domain-management"></a>Domänenverwaltung

Eine verwaltete Domäne ist ein DNS-Namespace und ein entsprechendes Verzeichnis. In einer verwalteten Domäne sind die Domänencontroller (DCs), die alle Ressourcen wie Benutzer und Gruppen, Anmeldeinformationen und Richtlinien enthalten, Teil des verwalteten Diensts. Aus Redundanzgründen werden zwei DCs als Teil einer verwalteten Domäne erstellt. Sie können sich nicht bei diesen DCs anmelden, um Verwaltungsaufgaben auszuführen. Stattdessen erstellen Sie eine Verwaltungs-VM, die der verwalteten Domäne hinzugefügt wird, und installieren dann die regulären AD DS Verwaltungstools. Sie können z.B. das Active Directory-Verwaltungscenter oder MMC-Snap-Ins (Microsoft Management Console) wie etwa DNS oder Gruppenrichtlinienobjekte verwenden.

## <a name="user-account-creation"></a>Erstellung von Benutzerkonten

Benutzerkonten können in einer verwalteten Domäne auf verschiedene Weise erstellt werden. Die meisten Benutzerkonten werden aus Azure AD synchronisiert. Dies kann auch Benutzerkonten beinhalten, die aus einer lokalen AD DS-Umgebung synchronisiert werden. Sie können Konten auch direkt in einer verwalteten Domäne manuell erstellen. Einige Features wie die anfängliche Kennwortsynchronisierung oder die Kennwortrichtlinie verhalten sich abhängig davon, wie und wo Benutzerkonten erstellt werden, unterschiedlich.

* Das Benutzerkonto kann aus Azure AD synchronisiert werden. Dazu gehören reine Cloudbenutzerkonten, die direkt in Azure AD erstellt wurden, sowie hybride Benutzerkonten, die aus einer lokalen AD DS-Umgebung mit Azure AD Connect synchronisiert werden.
    * Der Großteil der Benutzerkonten in einer verwalteten Domäne wird durch den Synchronisierungsprozess aus Azure AD erstellt.
* Das Benutzerkonto kann manuell in einer verwalteten Domäne erstellt werden und ist dann in Azure AD nicht vorhanden.
    * Wenn Sie Dienstkonten für Anwendungen erstellen müssen, die nur in der verwalteten Domäne ausgeführt werden, können Sie diese manuell in der verwalteten Domäne erstellen. Da die Synchronisierung aus Azure AD unidirektional ist, werden Benutzerkonten, die in der verwalteten Domäne erstellt werden, nicht mit Azure AD synchronisiert.

## <a name="password-policy"></a>Kennwortrichtlinie

Azure AD DS enthält eine Standardkennwortrichtlinie, die Einstellungen für Vorgänge wie Kontosperrung, maximales Kennwortalter und Kennwortkomplexität definiert. Einstellungen wie die Kontosperrungsrichtlinie gelten für alle Benutzer in einer verwalteten Domäne, und zwar unabhängig davon, wie der Benutzer erstellt wurde (im vorherigen Abschnitt beschrieben). Einige Einstellungen (z.B. die minimale Kennwortlänge und die Kennwortkomplexität) gelten nur für Benutzer, die direkt in einer verwalteten Domäne erstellt werden.

Sie können eigene benutzerdefinierte Kennwortrichtlinien erstellen, um die Standardrichtlinie in einer verwalteten Domäne außer Kraft zu setzen. Diese benutzerdefinierten Richtlinien können dann nach Bedarf auf bestimmte Gruppen von Benutzern angewendet werden.

Weitere Informationen zu den Unterschieden bei der Anwendung von Kennwortrichtlinien in Abhängigkeit von der Quelle der Benutzererstellung finden Sie unter [Kennwort- und Kontosperrungsrichtlinien für verwaltete Domänen ][password-policy].

## <a name="password-hashes"></a>Kennworthashes

Um Benutzer in der verwalteten Domäne authentifizieren zu können, benötigt Azure AD DS Kennworthashes in einem Format, das für die Authentifizierung über NT LAN Manager (NTLM) und Kerberos geeignet ist. Azure AD generiert oder speichert erst dann Kennworthashes in dem für die NTLM- oder Kerberos-Authentifizierung erforderlichen Format, wenn Sie Azure AD DS für Ihren Mandanten aktivieren. Aus Sicherheitsgründen speichert Azure AD Kennwörter nicht als Klartext. Daher kann Azure AD diese NTLM- oder Kerberos-Kennworthashes nicht automatisch auf der Grundlage bereits vorhandener Anmeldeinformationen von Benutzern generieren.

Bei reinen Cloudbenutzerkonten müssen Benutzer ihre Kennwörter ändern, bevor sie die verwaltete Domäne verwenden können. Diese Kennwortänderung führt dazu, dass die Kennworthashes für die Kerberos- und NTLM-Authentifizierung in Azure AD generiert und gespeichert werden. Das Konto wird erst von Azure AD zu Azure AD DS synchronisiert, wenn das Kennwort geändert wurde.

Für Benutzer, die mit Azure AD Connect aus einer lokalen AD DS-Umgebung synchronisiert werden, [aktivieren Sie die Synchronisierung von Kennworthashes][hybrid-phs].

> [!IMPORTANT]
> Azure AD Connect synchronisiert Legacykennworthashes nur, wenn Sie Azure AD DS für Ihren Azure AD-Mandanten aktivieren. Legacykennworthashes werden nicht verwendet, wenn Sie Azure AD Connect nur zum Synchronisieren einer lokalen AD DS-Umgebung mit Azure AD verwenden.
>
> Wenn Ihre Legacyanwendungen keine NTLM-Authentifizierung oder einfachen LDAP-Bindungen verwenden, empfiehlt es sich, die NTLM-Kennworthashsynchronisierung für Azure AD DS zu deaktivieren. Weitere Informationen finden Sie unter [Deaktivieren von schwachen Verschlüsselungssammlungen und der Synchronisierung von NTLM-Anmeldeinformationshashes][secure-domain].

Nach entsprechender Konfiguration werden die verwendbaren Kennworthashes in der verwalteten Domäne gespeichert. Wenn Sie die verwaltete Domäne löschen, werden alle zu diesem Zeitpunkt gespeicherten Kennworthashes ebenfalls gelöscht. Synchronisierte Anmeldeinformationen in Azure AD können nicht wiederverwendet werden, wenn Sie später eine weitere verwaltete Domäne erstellen. Sie müssen die Kennworthashsynchronisierung erneut konfigurieren, um die Kennworthashes wieder zu speichern. VMs oder Benutzer, die zuvor in eine Domäne eingebunden wurden, können sich nicht sofort authentifizieren, weil Azure AD die Kennworthashes in der neuen verwalteten Domäne generieren und speichern muss. Weitere Informationen finden Sie unter [Prozess der Kennworthashsynchronisierung für Azure AD DS und Azure AD Connect][azure-ad-password-sync].

> [!IMPORTANT]
> Azure AD Connect sollte nur für die Synchronisierung mit lokalen AD DS-Umgebungen installiert und konfiguriert werden. Die Installation von Azure AD Connect in einer verwalteten Domäne zur erneuten Synchronisierung von Objekten mit Azure AD wird nicht unterstützt.

## <a name="forests-and-trusts"></a>Gesamtstrukturen und Vertrauensstellungen

Eine *Gesamtstruktur* ist ein logisches Konstrukt, das von Active Directory Domain Services (AD DS) zum Gruppieren von *Domänen* verwendet wird. Die Domänen speichern dann Objekte für Benutzer oder Gruppen und stellen Authentifizierungsdienste bereit.

In Azure AD DS enthält die Gesamtstruktur nur eine einzelne Domäne. Lokale AD DS-Gesamtstrukturen enthalten häufig mehrere Domänen. In großen Unternehmen sind, insbesondere nach Fusionen und Übernahmen, unter Umständen mehrere lokale Gesamtstrukturen vorhanden, die jeweils mehrere Domänen enthalten.

Eine verwaltete Domäne wird standardmäßig als *Benutzergesamtstruktur* erstellt. Mit dieser Art von Gesamtstruktur werden alle Objekte aus Azure AD synchronisiert – einschließlich aller in einer lokalen AD DS-Umgebung erstellten Benutzerkonten. Benutzerkonten können sich direkt bei der verwalteten Domäne authentifizieren, um sich beispielsweise bei einem in eine Domäne eingebundenen virtuellen Computer anzumelden. Eine Benutzergesamtstruktur ist geeignet, wenn die Kennworthashes synchronisiert werden können und Benutzer keine exklusiven Anmeldemethoden wie etwa die Smartcard-Authentifizierung verwenden.

In einer *Ressourcengesamtstruktur* für Azure AD DS authentifizieren sich Benutzer über eine unidirektionale Gesamtstruktur-*Vertrauensstellung* von der lokalen AD DS-Instanz aus. Hierbei werden die Benutzerobjekte und Kennworthashes nicht mit Azure AD DS synchronisiert. Die Benutzerobjekte und Anmeldeinformationen sind nur in der lokalen AD DS-Instanz vorhanden. Mit diesem Ansatz können Unternehmen Ressourcen und Anwendungsplattformen in Azure hosten, die eine klassische Authentifizierung (beispielsweise LDAPS, Kerberos oder NTLM) benötigen, und gleichzeitig sämtliche Authentifizierungsprobleme oder -bedenken beseitigen.

Weitere Informationen zu Gesamtstrukturtypen in Azure AD DS finden Sie unter [Was sind Ressourcengesamtstrukturen?][concepts-forest] sowie unter [Wie funktionieren Gesamtstruktur-Vertrauensstellungen in Azure AD DS?][concepts-trust].

## <a name="azure-ad-ds-skus"></a>Azure AD DS-SKUs

In Azure AD DS beruhen die verfügbare Leistung und die Funktionen auf der SKU. Beim Erstellen der verwalteten Domäne wählen Sie eine SKU aus und wechseln die SKU, wenn sich Ihre Geschäftsanforderungen nach der Bereitstellung der verwalteten Domäne ändern. In der folgenden Tabelle werden die verfügbaren SKUs und die Unterschiede zwischen ihnen aufgeführt:

| SKU-Name   | Maximale Anzahl von Objekten | Sicherungshäufigkeit | Maximale Anzahl von ausgehenden Gesamtstrukturvertrauensstellungen |
|------------|----------------------|------------------|----|
| Standard   | Unbegrenzt            | Alle 7 Tage     | 0  |
| Enterprise | Unbegrenzt            | Alle 3 Tage     | 5  |
| Premium    | Unbegrenzt            | Täglich            | 10 |

Vor diesen Azure AD DS-SKUs wurde in der verwalteten Domäne ein Abrechnungsmodell verwendet, das auf der Anzahl von Objekten (Benutzer- und Computerkonten) basierte. Die variable Preisgestaltung, die auf der Anzahl der Objekte in der verwalteten Domäne beruhte, gibt es nicht mehr.

Weitere Informationen finden Sie auf der Seite [Azure AD DS – Preise][pricing].

### <a name="managed-domain-performance"></a>Leistung der verwalteten Domäne

Die Leistung der Domäne hängt davon ab, wie die Authentifizierung für eine Anwendung implementiert wird. Zusätzliche Computeressourcen können dabei helfen, die Antwortzeit bei Abfragen zu verbessern und die Zeit für Synchronisierungsvorgänge zu reduzieren. Je höher der SKU-Tarif, desto mehr Computeressourcen stehen für die verwaltete Domäne zur Verfügung. Überwachen Sie die Leistung Ihrer Anwendungen, und planen Sie die erforderlichen Ressourcen.

Wenn sich Ihre Geschäfts- oder Anwendungsanforderungen ändern und Sie zusätzliche Rechenleistung für Ihre verwaltete Domäne benötigen, können Sie zu einer anderen SKU wechseln.

### <a name="backup-frequency"></a>Sicherungshäufigkeit

Die Sicherungshäufigkeit bestimmt, wie oft eine Momentaufnahme der verwalteten Domäne erstellt wird. Sicherungen sind ein automatischer Prozess, der von der Azure-Plattform verwaltet wird. Bei einem Problem mit Ihrer verwalteten Domäne kann der Azure-Support Ihnen bei der Wiederherstellung von einer Sicherung helfen. Da die Synchronisierung *von* Azure AD nur unidirektional erfolgt, wirken sich mögliche Probleme bei einer verwalteten Domäne nicht auf Azure AD oder lokale AD DS-Umgebungen und -Funktionen aus.

Je höher der SKU-Tarif, desto häufiger werden Sicherungsmomentaufnahmen erstellt. Überprüfen Sie Ihre Geschäftsanforderungen und Recovery Point Objective (RPO), um die erforderliche Sicherungshäufigkeit für Ihre verwaltete Domäne zu bestimmen. Wenn sich Ihre Geschäfts-oder Anwendungsanforderungen ändern und Sie häufigere Sicherungen benötigen, können Sie zu einer anderen SKU wechseln.

### <a name="outbound-forest-trusts"></a>Ausgehende Gesamtstruktur-Vertrauensstellungen

Im vorherigen Abschnitt wurde die unidirektionale ausgehende Gesamtstrukturvertrauensstellung von einer verwalteten Domäne zu einer lokalen AD DS-Umgebung beschrieben. Die SKU bestimmt die maximale Anzahl von Gesamtstrukturvertrauensstellungen, die Sie für eine verwaltete Domäne erstellen können. Überprüfen Sie Ihre Geschäfts- und Anwendungsanforderungen, um zu bestimmen, wie viele Vertrauensstellungen Sie tatsächlich benötigen, und wählen Sie die entsprechende Azure AD DS-SKU aus. Wenn sich Ihre geschäftlichen Anforderungen ändern und Sie zusätzliche Gesamtstrukturvertrauensstellungen erstellen müssen, können Sie zu einer anderen SKU wechseln.

## <a name="next-steps"></a>Nächste Schritte

Erstellen Sie als Einstieg [eine verwaltete Azure AD DS-Domäne][create-instance].

<!-- INTERNAL LINKS -->
[password-policy]: password-policy.md
[hybrid-phs]: tutorial-configure-password-hash-sync.md#enable-synchronization-of-password-hashes
[secure-domain]: secure-your-domain.md
[azure-ad-password-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[create-instance]: tutorial-create-instance.md
[tutorial-create-instance-advanced]: tutorial-create-instance-advanced.md
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md

<!-- EXTERNAL LINKS -->
[pricing]: https://azure.microsoft.com/pricing/details/active-directory-ds/
