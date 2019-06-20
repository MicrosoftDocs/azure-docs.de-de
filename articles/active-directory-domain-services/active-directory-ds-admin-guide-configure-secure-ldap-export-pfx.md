---
title: Erstellen einer PFX-Datei mit dem Zertifikat für sicheres LDAP (LDAPS) für eine Azure AD Domain Services-Domäne
description: Erstellen eines Zertifikats für sicheres LDAP für eine über Azure AD Domain Services verwaltete Domäne
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: mstephen
ms.openlocfilehash: b79418c6e64727e4fdf13b3f4f267d84fcf5da85
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235021"
---
# <a name="create-a-pfx-file-with-the-secure-ldap-ldaps-certificate-for-a-managed-domain"></a>Erstellen einer PFX-Datei mit dem Zertifikat für sicheres LDAP (LDAPS) für eine verwaltete Domäne

## <a name="before-you-begin"></a>Voraussetzungen

Führen Sie [Aufgabe 1: Erwerben eines Zertifikats für sicheres LDAP](configure-ldaps.md) aus.

## <a name="task-2-export-the-secure-ldap-certificate-to-a-pfx-file"></a>Aufgabe 2: Exportieren des Zertifikats für sicheres LDAP in eine PFX-Datei

Rufen Sie vor dem Ausführen dieser Aufgabe das Zertifikat für sicheres LDAP von einer öffentlichen Zertifizierungsstelle ab, oder erstellen Sie ein selbstsigniertes Zertifikat.

So exportieren Sie das LDAPS-Zertifikat in eine PFX-Datei

1. Klicken Sie auf die Schaltfläche **Start**, und geben Sie **R** ein. Geben Sie im Dialogfeld **Ausführen** den Befehl **mmc** ein, und klicken Sie auf **OK**.

    ![Starten der MMC-Konsole](./media/active-directory-domain-services-admin-guide/secure-ldap-start-run.png)
2. Klicken Sie an der Eingabeaufforderung der **Benutzerkontensteuerung** auf **JA**, um die MMC (Microsoft Management Console) als Administrator zu starten.
3. Klicken Sie im Menü **Datei** auf **Snap-In hinzufügen/entfernen**.

    ![Hinzufügen des Snap-Ins zur MMC-Konsole](./media/active-directory-domain-services-admin-guide/secure-ldap-add-snapin.png)
4. Wählen Sie im Dialogfeld **Snap-Ins hinzufügen bzw. entfernen** das Snap-In **Zertifikate** aus, und klicken Sie auf die Schaltfläche **Hinzufügen >** .

    ![Hinzufügen des Zertifikat-Snap-Ins zur MMC-Konsole](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin.png)
5. Wählen Sie im Assistenten **Zertifikat-Snap-In** die Option **Computerkonto** aus, und klicken Sie auf **Weiter**.

    ![Hinzufügen des Zertifikat-Snap-Ins für das Computerkonto](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-computer-account.png)
6. Wählen Sie auf der Seite **Computer auswählen** die Option **Lokaler Computer: (Computer, auf dem diese Konsole ausgeführt wird)** aus, und klicken Sie auf **Fertig stellen**.

    ![Hinzufügen des Zertifikat-Snap-Ins – Computer auswählen](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-local-computer.png)
7. Klicken Sie im Dialogfeld **Snap-Ins hinzufügen bzw. entfernen** auf **OK**, um der MMC das Zertifikat-Snap-In hinzuzufügen.

    ![Hinzufügen des Zertifikat-Snap-Ins zur MMC-Konsole – fertig](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin-done.png)
8. Klicken Sie im MMC-Fenster auf den **Konsolenstamm**, um ihn zu erweitern. Das Zertifikat-Snap-In sollte angezeigt werden. Klicken Sie auf **Zertifikate (Lokaler Computer)** , um diesen Knoten zu erweitern. Klicken Sie nacheinander auf die Knoten **Eigene Zertifikate** und **Zertifikate**, um sie zu erweitern.

    ![Öffnen des eigenen Zertifikatspeichers](./media/active-directory-domain-services-admin-guide/secure-ldap-open-personal-store.png)
9. Sie sollten das zuvor erstellte selbstsignierte Zertifikat sehen. Sie können die Eigenschaften des Zertifikats überprüfen und sicherstellen, dass der Fingerabdruck demjenigen entspricht, der beim Erstellen des Zertifikats in den PowerShell-Fenstern angezeigt wurde.
10. Wählen Sie das selbstsignierte Zertifikat aus, und **klicken Sie mit der rechten Maustaste darauf**. Wählen Sie im Kontextmenü **Alle Aufgaben** und anschließend **Exportieren** aus.

    ![Exportieren des Zertifikats](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert.png)
11. Klicken **Sie im Zertifikatexport-Assistenten** auf **Weiter**.

    ![Exportieren des Zertifikats – Assistent](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert-wizard.png)
12. Wählen Sie auf der Seite **Privaten Schlüssel exportieren** die Option **Ja, privaten Schlüssel exportieren** aus, und klicken Sie auf **Weiter**.

    ![Exportieren des Zertifikats – privater Schlüssel](./media/active-directory-domain-services-admin-guide/secure-ldap-export-private-key.png)

    > [!WARNING]
    > Sie MÜSSEN den privaten Schlüssel gemeinsam mit dem Zertifikat exportieren. Wenn Sie eine PFX-Datei bereitstellen, die nicht den privaten Schlüssel für das Zertifikat enthält, kommt es beim Aktivieren von sicherem LDAP für Ihre verwaltete Domäne zu Fehlern.
    >
    >

13. Wählen Sie auf der Seite **Format der zu exportierenden Datei** als Dateiformat für das zu exportierende Zertifikat die Option **Privater Informationsaustausch – PKCS #12 (.PFX)** aus.

    ![Exportieren des Zertifikats – Dateiformat](./media/active-directory-domain-services-admin-guide/secure-ldap-export-to-pfx.png)

    > [!NOTE]
    > Es wird nur das Dateiformat .PFX unterstützt. Exportieren Sie das Zertifikat nicht im Dateiformat .CER.
    >
    >

14. Wählen Sie auf der Seite **Sicherheit** die Option **Kennwort** aus, und geben Sie ein Kennwort zum Schutz der PFX-Datei ein. Notieren Sie sich dieses Kennwort, denn Sie benötigen es in der nächsten Aufgabe. Klicken Sie auf **Weiter**.

    ![Kennwort für den Zertifikatexport](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-password.png)

    > [!NOTE]
    > Notieren Sie sich dieses Kennwort. Sie benötigen es zum Aktivieren von sicherem LDAP für diese verwaltete Domäne in [Aufgabe 3: Aktivieren von sicherem LDAP für die verwaltete Domäne](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)
    >
    >

15. Geben Sie auf der Seite **Zu exportierende Datei** den Dateinamen und den Speicherort für den Export des Zertifikats an.

    ![Pfad für den Zertifikatexport](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-path.png)
16. Klicken Sie auf der folgenden Seite auf **Fertig stellen** , um das Zertifikat in eine PFX-Datei zu exportieren. Nach dem Export des Zertifikats wird ein Bestätigungsdialogfeld angezeigt.

    ![Exportieren des Zertifikats – fertig](./media/active-directory-domain-services-admin-guide/secure-ldap-exported-as-pfx.png)

## <a name="next-step"></a>Nächster Schritt

[Aufgabe 3: Aktivieren von sicherem LDAP für die verwaltete Domäne](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md)
