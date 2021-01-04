---
title: Aktivieren des lokalen Azure AD-Kennwortschutzes
description: Es wird beschrieben, wie Sie die Problembehandlung beim Azure AD-Kennwortschutz in einer lokalen Active Directory Domain Services-Umgebung aktivieren.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2e11fa1bb9f1d3a3986a19da8ed44229829ec81
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96741778"
---
# <a name="enable-on-premises-azure-active-directory-password-protection"></a>Aktivieren des lokalen Azure Active Directory-Kennwortschutzes

Benutzer erstellen häufig Kennwörter, in denen gängige Wörter mit regionalem Bezug verwendet werden, z. B. eine Schule, eine Sportmannschaft oder eine prominente Person. Diese Kennwörter sind leicht zu erraten und halten wörterbuchbasierten Angriffen oft nicht stand. Zum Erzwingen von sicheren Kennwörtern in Ihrer Organisation bietet der Azure Active Directory-Kennwortschutz (Azure AD) eine Liste mit global und benutzerdefiniert gesperrten Kennwörtern. Die Anforderung einer Kennwortänderung ist nicht erfolgreich, wenn sich eine Übereinstimmung mit dieser Liste mit gesperrten Kennwörtern ergibt.

Um Ihre lokale Active Directory Domain Services-Umgebung (AD DS) zu schützen, können Sie den Azure AD-Kennwortschutz installieren und konfigurieren, sodass Ihr lokaler DC verwendet wird. In diesem Artikel wird veranschaulicht, wie Sie den Azure AD-Kennwortschutz für Ihre lokale Umgebung aktivieren.

Weitere Informationen zur Funktionsweise des Azure AD-Kennwortschutzes in einer lokalen Umgebung finden Sie unter [Erzwingen des Azure AD-Kennwortschutzes für Windows Server Active Directory](concept-password-ban-bad-on-premises.md).

## <a name="before-you-begin"></a>Voraussetzungen

In diesem Artikel wird veranschaulicht, wie Sie den Azure AD-Kennwortschutz für Ihre lokale Umgebung aktivieren. Führen Sie die [Installation und Registrierung des Proxydiensts und der DC-Agents für den Azure AD-Kennwortschutz](howto-password-ban-bad-on-premises-deploy.md) in Ihrer lokalen AD DS-Umgebung durch, bevor Sie diesen Artikel durcharbeiten.

## <a name="enable-on-premises-password-protection"></a>Aktivieren des lokalen Kennwortschutzes

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu **Azure Active Directory** > **Sicherheit** > **Authentifizierungsmethoden** > **Kennwortschutz**.
1. Legen Sie die Option für **Kennwortschutz für Windows Server Active Directory aktivieren** auf *Ja* fest.

    Wenn diese Einstellung auf *Nein* festgelegt ist, werden alle bereitgestellten DC-Agents für den Azure AD-Kennwortschutz in einen Ruhemodus versetzt, in dem alle Kennwörter akzeptiert werden. Es werden keine Überprüfungsaktivitäten durchgeführt und keine Überwachungsereignisse generiert.

1. Wir empfehlen Ihnen, den **Modus** anfänglich auf *Überwachen* festzulegen. Wenn Sie sich mit dem Feature und der Auswirkung auf die Benutzer Ihrer Organisation vertraut gemacht haben, können Sie den **Modus** in *Erzwungen* ändern. Weitere Informationen finden Sie unten im Abschnitt [Betriebsmodi](#modes-of-operation).
1. Wählen Sie **Speichern** aus, wenn Sie so weit sind.

    [![Aktivieren des lokalen Kennwortschutzes unter „Authentifizierungsmethoden“ im Azure-Portal](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords-cropped.png)](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords.png#lightbox)

## <a name="modes-of-operation"></a>Betriebsmodi

Beim Aktivieren des lokalen Azure AD-Kennwortschutzes können Sie entweder den Modus *Überwachen* oder *Erzwingen* verwenden. Wir empfehlen Ihnen, bei der anfänglichen Bereitstellung und beim Testen immer im Überwachungsmodus zu beginnen. Die Einträge im Ereignisprotokoll sollten dann überwacht werden, um antizipieren zu können, ob bestehende Betriebsvorgänge ggf. gestört werden, wenn der Modus *Erzwingen* aktiviert wird.

### <a name="audit-mode"></a>Überwachungsmodus

Der Modus *Überwachen* ist als Möglichkeit gedacht, die Software in einem „Was-wäre-wenn“-Modus auszuführen. Jeder DC-Agent-Dienst des Azure AD-Kennwortschutzes wertet ein eingehendes Kennwort gemäß der derzeit aktiven Richtlinie aus.

Wenn die aktuelle Richtlinie im Überwachungsmodus konfiguriert ist, führen „unzulässige“ Kennwörter zu Ereignisprotokollmeldungen, aber sie werden verarbeitet und aktualisiert. Dieses Verhalten ist der einzige Unterschied zwischen dem Überwachungs- und dem Erzwingungsmodus. Alle anderen Vorgänge sind identisch.

### <a name="enforced-mode"></a>Erzwingungsmodus

Der Modus *Erzwingen* dient als endgültige Konfiguration. Wie im Überwachungsmodus wertet jeder DC-Agent-Dienst des Azure AD-Kennwortschutzes eingehende Kennwörter gemäß der derzeit aktiven Richtlinie aus. Bei aktiviertem Erzwingungsmodus wird ein Kennwort, das basierend auf der Richtlinie als unsicher angesehen wird, aber abgelehnt.

Wenn ein Kennwort im Erzwingungsmodus vom DC-Agent des Azure AD Kennwortschutzes abgelehnt wird, wird für Endbenutzer ein ähnlicher Fehler wie bei der Ablehnung des Kennworts durch die herkömmliche lokale Erzwingung der Kennwortkomplexität angezeigt. Beispielsweise wird einem Benutzer die folgende herkömmliche Fehlermeldung auf dem Windows-Bildschirm zum Anmelden bzw. Ändern des Kennworts angezeigt:

*„Kennwort konnte nicht aktualisiert werden. Der Wert für das neue Kennwort erfüllt nicht die Anforderungen der Domäne hinsichtlich Länge, Komplexität oder Verlauf.“*

Diese Meldung ist nur ein Beispiel von mehreren möglichen Ergebnissen. Die spezifische Fehlermeldung kann je nach Software oder Szenario variieren, für die bzw. das ein unsicheres Kennwort festgelegt werden soll.

Betroffene Endbenutzer müssen sich unter Umständen bei ihrer IT-Abteilung über die neuen Anforderungen informieren, die für die Wahl sicherer Kennwörter gelten.

> [!NOTE]
> Der Kennwortschutz für Azure AD hat keine Kontrolle über die speziellen Fehlermeldungen, die beim Ablehnen eines schwachen Kennworts vom Clientcomputer angezeigt werden.

## <a name="next-steps"></a>Nächste Schritte

Informationen zur Anpassung der Liste mit gesperrten Kennwörtern für Ihre Organisation finden Sie unter [Tutorial: Konfigurieren von benutzerdefinierten gesperrten Kennwörtern für den Azure Active Directory-Kennwortschutz](tutorial-configure-custom-password-protection.md).

Informationen zur Überwachung von lokalen Ereignissen finden Sie unter [Überwachen und Überprüfen der Protokolle für lokale Umgebungen mit Azure AD-Kennwortschutz](howto-password-ban-bad-on-premises-monitor.md).
