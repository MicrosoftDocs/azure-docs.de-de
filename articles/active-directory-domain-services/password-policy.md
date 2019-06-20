---
title: 'Azure Active Directory Domain Services: Kennwortrichtlinie | Microsoft-Dokumentation'
description: Informationen zu Kennwortrichtlinien in verwalteten Domänen
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 1a14637e-b3d0-4fd9-ba7a-576b8df62ff2
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2019
ms.author: mstephen
ms.openlocfilehash: 71511fd83f9c00f768f5f7bedb3516fef8599e70
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245423"
---
# <a name="password-and-account-lockout-policies-on-managed-domains"></a>Kennwort- und Kontosperrungsrichtlinien in verwalteten Domänen
Dieser Artikel beschreibt die Standardkennwortrichtlinien in einer verwalteten Domäne. Sie erfahren auch, wie Sie diese Richtlinien konfigurieren können.

## <a name="fine-grained-password-policies-fgpp"></a>Differenzierte Kennwortrichtlinien (FGPP)
Verwenden Sie differenzierte Kennwortrichtlinien, um viele Kennwortrichtlinien in einer einzelnen Domäne anzugeben. Mit FGPP können Sie auf verschiedene Gruppen von Benutzern in einer Domäne unterschiedliche Einschränkungen für Kennwort- und Kontosperrungsrichtlinien anwenden. Beispielsweise können Sie strenge Kennworteinstellungen auf privilegierte Konten anwenden.

Sie können die folgenden Kennworteinstellungen mit FGPP konfigurieren:
* Minimale Kennwortlänge
* Kennwortverlauf
* Kennwörter müssen den Anforderungen an die Komplexität entsprechen
* Minimales Kennwortalter
* Maximales Kennwortalter
* Kontosperrungsrichtlinie
    * Kontosperrungsdauer
    * Zulässige Anzahl fehlgeschlagener Anmeldeversuche
    * Anzahl fehlgeschlagener Anmeldeversuche zurücksetzen nach


## <a name="default-fine-grained-password-policy-settings-on-a-managed-domain"></a>Standardeinstellungen von differenzierten Kennwortrichtlinien in einer verwalteten Domäne
Der folgende Screenshot veranschaulicht die standardmäßige differenzierte Kennwortrichtlinie, die für eine verwaltete Azure AD Domain Services-Domäne konfiguriert wurde.

![Standardmäßige differenzierte Kennwortrichtlinie](./media/how-to/default-fgpp.png)

> [!NOTE]
> Sie können die standardmäßige integrierte differenzierte Kennwortrichtlinie nicht ändern oder löschen. Mitglieder der Gruppe „AAD DC-Administratoren“ können eine benutzerdefinierte FGPP erstellen und so konfigurieren, dass sie die integrierte Standard-FGPP außer Kraft setzt.
>
>

## <a name="password-policy-settings"></a>Einstellungen von Kennwortrichtlinien
In einer verwalteten Domäne werden die folgenden Kennwortrichtlinien standardmäßig konfiguriert:
* Minimale Kennwortlänge (Zeichen): 7
* Maximales Kennwortalter (Gültigkeitsdauer): 90 Tage
* Kennwörter müssen den Anforderungen an die Komplexität entsprechen

### <a name="account-lockout-settings"></a>Einstellungen für die Kontosperrung
In einer verwalteten Domäne werden die folgenden Kontosperrungsrichtlinien standardmäßig konfiguriert:
* Kontosperrungsdauer: 30
* Zulässige Anzahl fehlerhafter Anmeldeversuche: 5
* Anzahl fehlerhafter Anmeldeversuche zurücksetzen nach: 30 Minuten

Dies bedeutet: Benutzerkonten werden für 30 Minuten gesperrt, wenn innerhalb von zwei Minuten fünf ungültige Kennwörter verwendet werden. Nach 30 Minuten werden die Konten automatisch wieder entsperrt.


## <a name="create-a-custom-fine-grained-password-policy-fgpp-on-a-managed-domain"></a>Erstellen einer benutzerdefinierten differenzierten Kennwortrichtlinie (FGPP) in einer verwalteten Domäne
Sie können eine benutzerdefinierte FGPP erstellen und auf bestimmte Gruppen in der verwalteten Domäne anwenden. Diese Konfiguration überschreibt effektiv die Standard-FGPP, die für die verwaltete Domäne konfiguriert ist.

> [!TIP]
> Nur Mitglieder der Gruppe **AAD DC-Administratoren** verfügen über die Berechtigungen zum Erstellen von benutzerdefinierten differenzierten Kennwortrichtlinien.
>
>

Darüber hinaus können Sie auch benutzerdefinierte differenzierte Kennwortrichtlinien erstellen und auf benutzerdefinierte Organisationseinheiten anwenden, die Sie in der verwalteten Domäne erstellen.

Sie können eine benutzerdefinierte FGPP aus den folgenden Gründen konfigurieren:
* Zum Festlegen einer anderen Kontosperrungsrichtlinie
* Zum Konfigurieren einer Standardeinstellung für die Kennwortgültigkeitsdauer für die verwaltete Domäne

So erstellen Sie eine benutzerdefinierte FGPP in Ihrer verwalteten Domäne
1. Melden Sie sich bei der Windows-VM an, die Sie zum Verwalten der verwalteten Domäne verwenden. Wenn Sie keine haben, führen Sie die unter [Verwalten einer Azure AD-Domain Services-Domäne](manage-domain.md) beschriebenen Anweisungen aus.
2. Starten Sie das **Active Directory-Verwaltungscenter** auf der VM.
3. Klicken Sie auf den Domänennamen (beispielsweise „contoso100.com“).
4. Doppelklicken Sie auf **System**, um den Systemcontainer zu öffnen.
5. Doppelklicken Sie auf **Kennworteinstellungscontainer**.
6. Daraufhin wird die standardmäßige integrierte FGPP für die verwaltete Domäne **AADDSSTFPSO** angezeigt. Sie können diese integrierte FGPP nicht ändern. Sie können jedoch eine neue benutzerdefinierte FGPP zur Außerkraftsetzung der Standard-FGPP erstellen.
7. Klicken Sie rechts im Bereich **Aufgaben** auf **Neu** und dann auf **Kennworteinstellungen**.
8. Geben Sie im Dialogfeld **Kennworteinstellungen erstellen** die benutzerdefinierten Kennworteinstellungen an, die als Teil der benutzerdefinierten FGPP angewendet werden sollen. Denken Sie daran, die Rangfolge entsprechend festzulegen, um die Standard-FGPP außer Kraft zu setzen.

   ![Erstellen einer benutzerdefinierten FGPP](./media/how-to/custom-fgpp.png)

   > [!TIP]
   > **Denken Sie daran, die Option „Vor versehentlichem Löschen schützen“ zu deaktivieren.** Wenn diese Option aktiviert ist, kann die FGPP nicht gespeichert werden.
   >
   >

9. Klicken Sie in **Direkt anwendbar auf** auf die Schaltfläche **Hinzufügen**. Klicken Sie im Dialogfeld **Benutzer oder Gruppen auswählen** auf die Schaltfläche **Speicherorte**.

   ![Auswählen von „Benutzer und Gruppen“](./media/how-to/fgpp-applies-to.png)

10. Erweitern Sie im Dialogfeld **Speicherorte** den Domänennamen, und klicken Sie auf **AADDC Users**. Sie können jetzt eine Gruppe aus der integrierten Benutzer-Organisationseinheit auswählen, auf die die FGPP angewendet werden soll.

    ![Auswählen der Organisationseinheit, zu der die Gruppe gehört](./media/how-to/fgpp-container.png)

11. Geben Sie den Namen der Gruppe ein, und klicken Sie auf die Schaltfläche **Namen überprüfen**, um zu überprüfen, ob die Gruppe vorhanden ist.

    ![Auswählen der Gruppe zum Anwenden der FGPP](./media/how-to/fgpp-apply-group.png)

12. Der Name der Gruppe wird im Abschnitt **Direkt anwendbar auf** angezeigt. Klicken Sie auf die Schaltfläche **OK**, um diese Änderungen zu speichern.

    ![FGPP angewendet](./media/how-to/fgpp-applied.png)

> [!TIP]
> **Anwendung benutzerdefinierter Kennwortrichtlinien für Benutzerkonten in einer benutzerdefinierten Organisationseinheit**: Differenzierte Kennwortrichtlinien (FGPP) können nur auf Gruppen angewendet werden. Erstellen Sie eine Gruppe, die die Benutzer in dieser Organisationseinheit enthält, um eine benutzerdefinierte Kennwortrichtlinie nur für Benutzer aus einer benutzerdefinierten Organisationseinheit zu konfigurieren.
>
>

## <a name="next-steps"></a>Nächste Schritte
* [Erfahren Sie mehr über differenzierte Active Directory-Kennwortrichtlinien](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770394(v=ws.10)).
* [Konfigurieren Sie differenzierte Kennwortrichtlinien mit dem Active Directory-Verwaltungscenter](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/adac/introduction-to-active-directory-administrative-center-enhancements--level-100-#fine_grained_pswd_policy_mgmt).
