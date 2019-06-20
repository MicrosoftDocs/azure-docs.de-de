---
title: Verhindern von Brute-Force-Angriffen mithilfe von Azure AD Smart Lockout – Azure Active Directory
description: Azure AD Smart Lockout trägt dazu bei, Ihre Organisation vor Brute-Force-Angriffen zu schützen, bei denen versucht wird, Kennwörter zu erraten.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/31/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c81a9f3891130f1c6fc2f1a665d7065fb983227
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60358119"
---
# <a name="azure-active-directory-smart-lockout"></a>Azure Active Directory Smart Lockout

Smart Lockout unterstützt Sie beim Sperren von Angreifern, die versuchen, Benutzerkennwörter zu erraten oder mithilfe von Brute-Force-Methoden in Ihr System einzudringen. Smart Lockout kann Anmeldungen gültiger Benutzer erkennen und anders behandeln als Anmeldungen von Angreifern und anderen unbekannten Quellen. Smart Lockout sperrt die Angreifer aus, während Ihre Benutzer weiterhin auf ihre Konten zugreifen und produktiv arbeiten können.

Nach zehn Fehlversuchen sperrt Smart Lockout das Konto standardmäßig eine Minute lang für Anmeldeversuche. Das Konto wird nach jedem weiteren fehlgeschlagenen Anmeldeversuch zuerst für eine Minute und bei anschließenden Versuchen länger gesperrt.

Smart Lockout verfolgt die letzten drei fehlerhaften Kennworthashes, um zu vermeiden, dass der Sperrungszähler für dasselbe Kennwort erhöht wird. Wenn eine Person mehrere Male das falsche Kennwort eingibt, wird dadurch keine Kontosperre verursacht.

 > [!NOTE]
 > Eine Funktion zur Hashnachverfolgung ist nicht für Kunden verfügbar, die die Pass-Through-Authentifizierung aktiviert haben, da die Authentifizierung lokal und nicht in der Cloud erfolgt.

Smart Lockout ist für alle Azure AD-Kunden ständig aktiv und bietet standardmäßig die richtige Mischung aus Sicherheit und Nutzbarkeit. Wenn Sie die Smart Lockout-Einstellungen mit spezifischen Werten für Ihre Organisation konfigurieren möchten, benötigen Sie mindestens Azure AD Basic-Lizenzen für Ihre Benutzer.

Die Verwendung von Smart Lockout garantiert nicht, dass ein echter Benutzer niemals gesperrt wird. Wenn ein Benutzerkonto durch Smart Lockout gesperrt wird, versuchen wir unser Bestes, nicht den echten Benutzer zu sperren. Der Sperrdienst versucht sicherzustellen, dass Angreifer keinen Zugriff auf die Konten echter Benutzer erhalten.  

* Sperren werden von den einzelnen Azure Active Directory-Rechenzentren unabhängig voneinander verfolgt. Ein Benutzer besitzt (Schwellenwert * Rechenzentrumsanzahl) Versuche, wenn der Benutzer auf die einzelnen Rechenzentren trifft.
* Ein böswilliger Benutzer und ein echter Benutzer werden von Smart Lockout über einen bekannten/unbekannten Standort unterschieden. Unbekannte und bekannte Standorte weisen jeweils separate Zähler für Sperren auf.

Smart Lockout ist in Hybridbereitstellungen integrierbar und kann mittels Kennworthashsynchronisierung oder Passthrough-Authentifizierung verhindern, dass lokale Active Directory-Konten von Angreifern gesperrt werden. Durch korrektes Festlegen von Smart Lockout-Richtlinien in Azure AD können Angriffe herausgefiltert werden, bevor sie die lokale Active Directory-Instanz erreichen.

Bei Verwendung der [Passthrough-Authentifizierung](../hybrid/how-to-connect-pta.md) muss Folgendes sichergestellt werden:

* Der Azure AD-Sperrschwellenwert ist **kleiner** als der Schwellenwert für eine Active Directory-Kontosperrung. Legen Sie die Werte so fest, dass der Schwellenwert für eine Active Directory-Kontosperrung mindestens das Zwei- oder Dreifache des Azure AD-Sperrschwellenwerts beträgt. 
* Die Azure AD-Sperrdauer (in **Sekunden**) ist **länger** als die Active Directory-Zurücksetzungsdauer des Kontosperrungszählers (in **Minuten**).

> [!IMPORTANT]
> Derzeit können die Cloudkonten der Benutzer nicht von einem Administrator entsperrt werden, wenn sie von Smart Lockout gesperrt wurden. Der Administrator muss warten, bis die Sperrdauer abgelaufen ist.

## <a name="verify-on-premises-account-lockout-policy"></a>Überprüfen der lokalen Kontosperrungsrichtlinie

Gehen Sie wie folgt vor, um Ihre lokale Active Directory-Kontosperrungsrichtlinie zu überprüfen:

1. Öffnen Sie das Gruppenrichtlinienverwaltungstool.
2. Bearbeiten Sie die Gruppenrichtlinie, die die Kontosperrungsrichtlinie Ihrer Organisation enthält (beispielsweise die **Standarddomänenrichtlinie**).
3. Navigieren Sie zu **Computerkonfiguration** > **Richtlinien** > **Windows-Einstellungen** > **Sicherheitseinstellungen** > **Kontorichtlinien** > **Kontosperrungsrichtlinien**.
4. Überprüfen Sie Ihre Werte für **Kontensperrungsschwelle** und **Zurücksetzungsdauer des Kontosperrungszählers**.

![Ändern der lokalen Active Directory-Kontosperrungsrichtlinie](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>Verwalten der Werte für Azure AD Smart Lockout

Abhängig von den Anforderungen Ihrer Organisation müssen die Werte für Smart Lockout ggf. angepasst werden. Wenn Sie die Smart Lockout-Einstellungen mit spezifischen Werten für Ihre Organisation konfigurieren möchten, benötigen Sie mindestens Azure AD Basic-Lizenzen für Ihre Benutzer.

Gehen Sie wie folgt vor, um die Smart Lockout-Werte zu überprüfen und ggf. für Ihre Organisation anzupassen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und klicken Sie auf **Azure Active Directory** > **Authentifizierungsmethoden**.
1. Legen Sie **Schwellenwert für Sperre** auf die Anzahl nicht erfolgreicher Anmeldeversuche fest, nach der das Konto erstmals gesperrt werden soll. (Standardwert: 10.)
1. Legen unter **Sperrdauer in Sekunden** fest, wie viele Sekunden die Sperre jeweils dauern soll. Der Standardwert ist 60 Sekunden (eine Minute).

> [!NOTE]
> Ist der erste Anmeldeversuch nach einer Sperre wieder nicht erfolgreich, wird das Konto erneut sperrt. Bei wiederholter Kontosperrung erhöht sich die Sperrdauer.

![Anpassen Azure AD Smart Lockout-Richtlinie über das Azure-Portal](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)

## <a name="how-to-determine-if-the-smart-lockout-feature-is-working-or-not"></a>Ermitteln, ob das Smart Lockout-Feature funktioniert

Wenn der Schwellenwert von Smart Lockout ausgelöst wird, wird das Konto gesperrt und die folgende Meldung angezeigt:

**Ihr Konto wurde vorübergehend gesperrt, um eine unbefugte Nutzung zu verhindern. Versuchen Sie es später noch mal. Wenden Sie sich an Ihren Administrator, wenn das Problem weiterhin besteht.**

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie mithilfe von Azure AD ungültige Kennwörter aus Ihrer Organisation verbannen.](howto-password-ban-bad.md)
* [Konfigurieren Sie die Self-Service-Kennwortzurücksetzung, damit Benutzer ihre Konten selbst entsperren können.](quickstart-sspr.md)
