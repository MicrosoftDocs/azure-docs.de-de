---
title: 'Verhindern von Angriffen mithilfe von Smart Lockout: Azure Active Directory'
description: Hier erfahren Sie, wie Smart Lockout von Azure Active Directory dazu beiträgt, Ihre Organisation vor Brute-Force-Angriffen zu schützen, bei denen versucht wird, Benutzerkennwörter zu erraten.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/20/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5f501c19da3c2ddc06ad89fe5649789477af7ec
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99255372"
---
# <a name="protect-user-accounts-from-attacks-with-azure-active-directory-smart-lockout"></a>Schützen von Benutzerkonten vor Angriffen mithilfe von Smart Lockout von Azure Active Directory

Smart Lockout unterstützt Sie dabei, Angreifer auszusperren, die versuchen, Benutzerkennwörter zu erraten oder sich mithilfe von Brute-Force-Methoden Zugang zu verschaffen. Smart Lockout kann Anmeldungen gültiger Benutzer erkennen und anders behandeln als Anmeldungen von Angreifern und anderen unbekannten Quellen. Angreifer werden ausgesperrt, während Ihre Benutzer weiterhin auf ihre Konten zugreifen und produktiv arbeiten können.

## <a name="how-smart-lockout-works"></a>Funktionsweise von Smart Lockout

Nach zehn Fehlversuchen sperrt Smart Lockout das Konto standardmäßig eine Minute lang für Anmeldeversuche. Das Konto wird nach jedem weiteren fehlgeschlagenen Anmeldeversuch zuerst für eine Minute und bei anschließenden Versuchen länger gesperrt. Um die Möglichkeiten eines Angreifers zum Umgehen dieses Verhaltens auf ein Minimum zu beschränken, geben wir nicht bekannt, mit welcher Rate der Sperrzeitraum bei weiteren erfolglosen Anmeldeversuchen ausgeweitet wird.

Smart Lockout verfolgt die letzten drei fehlerhaften Kennworthashes, um zu vermeiden, dass der Sperrungszähler für dasselbe Kennwort erhöht wird. Wenn eine Person mehrmals das falsche Kennwort eingibt, hat dies keine Sperrung des Kontos zur Folge.

> [!NOTE]
> Für Kunden mit aktivierter Passthrough-Authentifizierung ist die Hashnachverfolgung nicht verfügbar, da die Authentifizierung lokal und nicht in der Cloud stattfindet.

Verbundbereitstellungen mit AD FS 2016 und AD FS 2019 können mit [AD FS Extranet Lockout und Extranet Smart Lockout](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) ähnliche Vorteile bieten.

Smart Lockout ist für alle Azure AD-Kunden ständig aktiv und bietet standardmäßig die richtige Mischung aus Sicherheit und Benutzerfreundlichkeit. Wenn Sie die Smart Lockout-Einstellungen mit spezifischen Werten für Ihre Organisation konfigurieren möchten, benötigen Sie mindestens Azure AD Premium P1-Lizenzen für Ihre Benutzer.

Die Verwendung von Smart Lockout garantiert nicht, dass ein echter Benutzer niemals ausgesperrt wird. Wenn ein Benutzerkonto durch Smart Lockout gesperrt wird, versuchen wir unser Bestes, nicht den echten Benutzer auszusperren. Der Sperrdienst versucht sicherzustellen, dass Angreifer keinen Zugriff auf die Konten echter Benutzer erhalten. Es gelten die folgenden Bedingungen:

* Sperren werden von den einzelnen Azure AD-Rechenzentren unabhängig voneinander nachverfolgt. Ein Benutzer hat (*Schwellenwert · Rechenzentrumsanzahl*) Versuche, wenn der Benutzer auf die einzelnen Rechenzentren trifft.
* Ein böswilliger Benutzer und ein echter Benutzer werden von Smart Lockout über einen bekannten/unbekannten Standort unterschieden. Unbekannte und bekannte Standorte verfügen jeweils über separate Zähler für Sperren.

Smart Lockout ist in Hybridbereitstellungen integrierbar und kann mittels Kennworthashsynchronisierung oder Passthrough-Authentifizierung verhindern, dass lokale AD DS-Konten (Active Directory Domain Services) durch Angreifer gesperrt werden. Durch korrektes Festlegen von Smart Lockout-Richtlinien in Azure AD können Angriffe herausgefiltert werden, bevor sie die lokale AD DS-Instanz erreichen.

Bei Verwendung der [Passthrough-Authentifizierung](../hybrid/how-to-connect-pta.md) gilt Folgendes:

* Der Azure AD-Sperrschwellenwert ist **kleiner** als der Schwellenwert für eine AD DS-Kontosperrung. Legen Sie die Werte so fest, dass der Schwellenwert für eine AD DS-Kontosperrung mindestens das Zwei- oder Dreifache des Azure AD-Sperrschwellenwerts beträgt.
* Die Azure AD-Sperrdauer muss länger sein als die AD DS-Zurücksetzungsdauer des Kontosperrungszählers. Die Azure AD-Dauer wird in Sekunden festgelegt, die AD-Dauer dagegen in Minuten.

Wenn der Azure AD-Zähler also beispielsweise höher sein soll als der AD DS-Wert, muss der Azure AD-Wert auf 120 Sekunden (2 Minuten) und der Wert Ihrer lokalen AD-Instanz auf eine Minute (60 Sekunden) festgelegt werden.

> [!IMPORTANT]
> Derzeit können die Cloudkonten der Benutzer nicht von einem Administrator entsperrt werden, wenn sie von Smart Lockout gesperrt wurden. Der Administrator muss warten, bis die Sperrdauer abgelaufen ist. Der Benutzer kann das Konto jedoch von einem vertrauenswürdigen Gerät oder Standort aus mithilfe der Self-Service-Kennwortzurücksetzung (SSPR) entsperren.

## <a name="verify-on-premises-account-lockout-policy"></a>Überprüfen der lokalen Kontosperrungsrichtlinie

Führen Sie auf einem in die Domäne eingebundenen System mit Administratorrechten die folgenden Schritte aus, um Ihre lokale AD DS-Kontosperrungsrichtlinie zu überprüfen:

1. Öffnen Sie das Gruppenrichtlinienverwaltungstool.
2. Bearbeiten Sie die Gruppenrichtlinie, die die Kontosperrungsrichtlinie Ihrer Organisation enthält (beispielsweise die **Standarddomänenrichtlinie**).
3. Navigieren Sie zu **Computerkonfiguration** > **Richtlinien** > **Windows-Einstellungen** > **Sicherheitseinstellungen** > **Kontorichtlinien** > **Kontosperrungsrichtlinien**.
4. Überprüfen Sie Ihre Werte für **Kontensperrungsschwelle** und **Zurücksetzungsdauer des Kontosperrungszählers**.

![Ändern der lokalen Active Directory-Kontosperrungsrichtlinie](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>Verwalten der Werte für Azure AD Smart Lockout

Die Werte für Werte für Azure AD Smart Lockout können auf die Anforderungen Ihrer Organisation abgestimmt werden. Wenn Sie die Smart Lockout-Einstellungen mit spezifischen Werten für Ihre Organisation konfigurieren möchten, benötigen Sie mindestens Azure AD Premium P1-Lizenzen für Ihre Benutzer.

Gehen Sie wie folgt vor, um die Smart Lockout-Werte zu überprüfen und ggf. für Ihre Organisation anzupassen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie nach *Azure Active Directory*, und wählen Sie die entsprechende Option aus. Wählen Sie anschließend **Sicherheit** > **Authentifizierungsmethoden** > **Kennwortschutz** aus.
1. Legen Sie **Schwellenwert für Sperre** auf die Anzahl nicht erfolgreicher Anmeldeversuche fest, nach der das Konto erstmals gesperrt werden soll.

    Der Standardwert ist 10.

1. Legen unter **Sperrdauer in Sekunden** fest, wie viele Sekunden die Sperre jeweils dauern soll.

    Der Standardwert ist 60 Sekunden (eine Minute).

> [!NOTE]
> Ist der erste Anmeldeversuch nach einer Sperre wieder nicht erfolgreich, wird das Konto erneut sperrt. Bei wiederholter Kontosperrung erhöht sich die Sperrdauer.

![Anpassen Azure AD Smart Lockout-Richtlinie über das Azure-Portal](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)

## <a name="how-to-determine-if-the-smart-lockout-feature-is-working-or-not"></a>Ermitteln, ob das Smart Lockout-Feature funktioniert

Wenn der Schwellenwert von Smart Lockout ausgelöst wird, wird das Konto gesperrt und die folgende Meldung angezeigt:

*Ihr Konto wurde vorübergehend gesperrt, um eine unbefugte Nutzung zu verhindern. Versuchen Sie es später noch mal. Wenden Sie sich an Ihren Administrator, wenn das Problem weiterhin besteht.*

Wenn Sie Smart Lockout testen, werden Ihre Anmeldeanforderungen möglicherweise von unterschiedlichen Rechenzentren verarbeitet, da der Azure AD-Authentifizierungsdienst geografisch verteilt ist und über einen Lastenausgleich verfügt. Da in diesem Szenario jedes Azure AD-Rechenzentrum die Sperre unabhängig nachverfolgt, kann es mehr Versuche erfordern, als durch den Sperrschwellenwert festgelegt sind, bis eine Sperre ausgelöst wird. Ein Benutzer hat (*Schwellenwert × Rechenzentrumsanzahl*) fehlerhafte Versuche, wenn der Benutzer auf die einzelnen Rechenzentren trifft, bevor eine Sperrung erfolgt.

## <a name="next-steps"></a>Nächste Schritte

Zur weiteren Anpassung können Sie [benutzerdefinierte gesperrte Kennwörter für den Azure AD-Kennwortschutz konfigurieren](tutorial-configure-custom-password-protection.md).

Um Benutzer beim Zurücksetzen oder Ändern ihres Kennworts über einen Webbrowser zu unterstützen, können Sie die [Self-Service-Kennwortzurücksetzung in Azure AD konfigurieren](tutorial-enable-sspr.md).
