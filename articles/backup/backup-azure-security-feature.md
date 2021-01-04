---
title: Sicherheitsfeatures für den Schutz von Hybridsicherungen
description: In diesem Artikel wird erläutert, wie Sie mithilfe der Azure Backup-Sicherheitsfunktionen mehr Sicherheit für Ihre Sicherungen gewinnen können.
ms.reviewer: utraghuv
ms.topic: conceptual
ms.date: 06/08/2017
ms.openlocfilehash: 8c671b1b54b937f518f7179bb6940f31a28a78d4
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94841017"
---
# <a name="security-features-to-help-protect-hybrid-backups-that-use-azure-backup"></a>Sicherheitsfeatures für den Schutz von Hybridsicherungen mit Azure Backup

Die Sorgen bezüglich Sicherheitsproblemen wie Schadsoftware, Ransomware und Eindringlingen werden immer größer. Diese Sicherheitsprobleme können erhebliche Daten- und finanzielle Verluste mit sich bringen. Zum Schutz gegen solche Angriffe bietet Azure Backup jetzt neue Sicherheitsfeatures für den Schutz von Hybridsicherungen. In diesem Artikel werden Aktivierung und Verwendung dieser Features mit einem Azure Recovery Services-Agent und Azure Backup Server beschrieben. Zu den Features zählen:

- **Prävention**. Es wurde eine zusätzliche Authentifizierungsebene hinzugefügt, die bei allen kritischen Vorgängen wie Passphrasenänderungen zum Tragen kommt. Durch diese Überprüfung wird gewährleistet, dass solche Vorgänge nur von Benutzern durchgeführt werden können, die über gültige Azure-Anmeldeinformationen verfügen.
- **Warnungen**. Bei kritischen Vorgängen wie dem Löschen von Sicherungsdaten wird eine E-Mail-Benachrichtigung an den Administrator der Abonnements gesendet. Diese E-Mail stellt sicher, dass der Benutzer schnell über solche Aktionen benachrichtigt wird.
- **Wiederherstellen**. Gelöschte Sicherungsdaten werden für zusätzliche 14 Tage ab dem Löschdatum aufbewahrt. So wird gewährleistet, dass die Daten innerhalb des vorgegebenen Zeitraums wiederhergestellt werden können und auch bei Angriffen keine Datenverluste auftreten. Zum Schutz vor beschädigten Daten werden darüber hinaus auch mehr Mindestwiederherstellungspunkte gepflegt.

> [!NOTE]
> Sicherheitsfeatures sollten nicht aktiviert werden, wenn Sie eine IaaS-VM-Sicherung (Infrastructure-as-a-Service) verwenden. Sie stehen für die IaaS-VM-Sicherung noch nicht zur Verfügung, die Aktivierung hat also keine Auswirkungen. Die Sicherheitsfeatures sollten nur aktiviert werden, wenn Sie Folgendes verwenden: <br/>
>
> - **Azure Backup-Agent**. Agent-Mindestversion: 2.0.9052. Nach der Aktivierung dieser Features sollten Sie auf diese Agent-Version aktualisieren, um kritische Vorgänge auszuführen. <br/>
> - **Azure Backup Server**. Azure Backup-Agent-Mindestversion: 2.0.9052 mit Azure Backup Server-Update 1. <br/>
> - **System Center Data Protection Manager**. Azure Backup-Agent-Mindestversion 2.0.9052 mit Data Protection Manager 2012 R2 UR12 oder Data Protection Manager 2016 UR2. <br/>

> [!NOTE]
> Diese Features sind nur für den Recovery Services-Tresor verfügbar. Bei allen neu erstellten Recovery Services-Tresoren sind diese Features standardmäßig aktiviert. Für vorhandene Recovery Services-Tresore müssen die Benutzer diese Features anhand der Schritte im folgenden Abschnitt aktivieren. Nach der Aktivierung gelten die Features für alle Recovery Services-Agent-Computer, Azure Backup Server-Instanzen und Data Protection Manager-Server, die im Tresor registriert sind. Die Aktivierung dieser Einstellung ist eine einmalige Aktion, und die Features können nach der Aktivierung nicht wieder deaktiviert werden.
>

## <a name="enable-security-features"></a>Aktivieren der Sicherheitsfeatures

Wenn Sie einen Recovery Services-Tresor erstellen, können Sie alle Sicherheitsfeatures verwenden. Aktivieren Sie die Sicherheitsfeatures bei Verwendung eines bereits vorhandenen Tresors mit folgenden Schritten:

1. Melden Sie sich mit Ihren Azure-Anmeldeinformationen beim Azure-Portal an.
2. Wählen Sie **Durchsuchen**, und geben Sie **Recovery Services** ein.

    ![Screenshot des Azure-Portals mit der Option „Durchsuchen“](./media/backup-azure-security-feature/browse-to-rs-vaults.png) <br/>

    Die Liste mit den Recovery Services-Tresoren wird angezeigt. Wählen Sie aus dieser Liste einen Tresor aus. Das ausgewählte Tresor-Dashboard wird geöffnet.
3. Klicken Sie in der Liste mit Elementen, die für den Tresor angezeigt wird, unter **Einstellungen** auf **Eigenschaften**.

    ![Screenshot der Optionen des Recovery Services-Tresors](./media/backup-azure-security-feature/vault-list-properties.png)
4. Klicken Sie unter **Sicherheitseinstellungen** auf **Aktualisieren**.

    ![Screenshot der Eigenschaften des Recovery Services-Tresors](./media/backup-azure-security-feature/security-settings-update.png)

    Über diesen Updatelink wird der Bereich **Sicherheitseinstellungen** geöffnet, in dem Sie eine Zusammenfassung der Features finden und diese aktivieren können.
5. Wählen Sie aus der Dropdownliste **Haben Sie Azure AD Multi-Factor Authentication konfiguriert?** und dann einen Wert aus, um zu bestätigen, dass Sie [Azure AD Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md) aktiviert haben. Wenn Azure Multi-Factor Authentication aktiviert ist, werden Sie gebeten, sich bei der Anmeldung beim Azure-Portal über ein anderes Gerät (z. B. Mobiltelefon) zu authentifizieren.

   Wenn Sie wichtige Vorgänge in der Sicherung ausführen, müssen Sie eine im Azure Portal verfügbare Sicherheits-PIN eingeben. Das Aktivieren der Azure AD Multi-Factor Authentication sorgt so für eine zusätzliche Sicherheitsebene. Nur autorisierte Benutzer mit gültigen Azure-Anmeldeinformationen, die über ein zweites Gerät authentifiziert sind, können auf das Azure-Portal zugreifen.
6. Klicken Sie zum Speichern der Sicherheitseinstellungen auf **Aktivieren** und dann auf **Speichern**. **Aktivieren** können Sie nur dann auswählen, wenn Sie im vorherigen Schritt einen Wert aus der Dropdownliste **Haben Sie Azure AD Multi-Factor Authentication konfiguriert?** ausgewählt haben.

    ![Screenshot der Sicherheitseinstellungen](./media/backup-azure-security-feature/enable-security-settings-dpm-update.png)

## <a name="recover-deleted-backup-data"></a>Wiederherstellen von gelöschten Sicherungsdaten

Backup bewahrt gelöschte Sicherungsdaten weitere 14 Tage lang auf und löscht sie nicht sofort, wenn der Vorgang **Stop backup with delete backup data** (Sicherung abbrechen mit Löschen der Sicherungsdaten) ausgeführt wird. Um diese Daten innerhalb dieser 14 Tage wiederherzustellen, gehen Sie je nachdem, was Sie verwenden, folgendermaßen vor:

Für Benutzer des **Microsoft Azure Recovery Services-Agent**:

1. Wenn der Computer, auf dem die Sicherungen durchgeführt wurden, noch verfügbar ist, schützen Sie die gelöschten Datenquellen erneut. Verwenden Sie in den Azure Recovery Services die [Wiederherstellung von Daten auf demselben Computer](backup-azure-restore-windows-server.md#use-instant-restore-to-recover-data-to-the-same-machine), um die Wiederherstellung mithilfe aller alten Wiederherstellungspunkte durchzuführen.
2. Wenn dieser Computer nicht verfügbar ist, verwenden Sie die [Wiederherstellung auf einem anderen Computer](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine), um einen anderen Azure Recovery Services-Computer zum Abrufen dieser Daten zu verwenden.

Für Benutzer von **Azure Backup Server** :

1. Wenn der Server, auf dem die Sicherungen durchgeführt wurden, noch verfügbar ist, schützen Sie die gelöschten Datenquellen erneut. Verwenden Sie das Feature **Daten wiederherstellen**, um die Wiederherstellung mithilfe aller alten Wiederherstellungspunkte durchzuführen.
2. Wenn dieser Server nicht verfügbar ist, befolgen Sie die Schritte unter [Wiederherstellen von Daten von Azure Backup Server](backup-azure-alternate-dpm-server.md), um eine andere Azure Backup Server-Instanz zum Abrufen dieser Daten zu verwenden.

Für Benutzer von **Data Protection Manager**:

1. Wenn der Server, auf dem die Sicherungen durchgeführt wurden, noch verfügbar ist, schützen Sie die gelöschten Datenquellen erneut. Verwenden Sie das Feature **Daten wiederherstellen**, um die Wiederherstellung mithilfe aller alten Wiederherstellungspunkte durchzuführen.
2. Wenn dieser Server nicht verfügbar ist, verwenden Sie [Externen DPM hinzufügen](backup-azure-alternate-dpm-server.md), um einen anderen Data Protection Manager-Server zum Abrufen dieser Daten zu verwenden.

## <a name="prevent-attacks"></a>Verhindern von Angriffen

Es wurden Prüfungen hinzugefügt, um sicherzustellen, dass bestimmte Vorgänge nur von gültigen Benutzern ausgeführt werden können. Dazu gehören das Hinzufügen einer zusätzlichen Authentifizierungsebene und das Einhalten einer minimalen Aufbewahrungsdauer für die Wiederherstellung.

### <a name="authentication-to-perform-critical-operations"></a>Authentifizierung für das Ausführen von kritischen Vorgängen

Bei Hinzufügung einer zusätzlichen Authentifizierungsebene für kritische Vorgänge werden Sie beim Ausführen der Vorgänge **Stop Protection with Delete data** (Schutz mit Datenlöschung beenden) und **Passphrase ändern** dazu aufgefordert, die Sicherheits-PIN einzugeben.

> [!NOTE]
>
> Die Sicherheits-PIN wird derzeit für **Schutz beenden und Daten löschen** für DPM und MABS nicht unterstützt.

So erhalten Sie diese PIN:

1. Melden Sie sich beim Azure-Portal an.
2. Navigieren Sie zu **Recovery Services-Tresor** > **Einstellungen** > **Eigenschaften**.
3. Wählen Sie unter **Sicherheits-PIN** die Option **Generieren** aus. Daraufhin wird ein Bereich geöffnet, der die in der Benutzeroberfläche des Azure Recovery Services-Agents einzugebende PIN enthält.
    Diese PIN gilt nur für fünf Minuten und wird nach Ablauf dieses Zeitraums automatisch generiert.

### <a name="maintain-a-minimum-retention-range"></a>Einhalten einer minimalen Aufbewahrungsdauer

Um sicherzustellen, dass immer eine geeignete Anzahl von Wiederherstellungspunkten verfügbar ist, wurden die folgenden Prüfungen hinzugefügt:

- Bei einer täglichen Aufbewahrung sollten mindestens **sieben** Aufbewahrungstage vorgesehen werden.
- Bei einer wöchentlichen Aufbewahrung sollten mindestens **vier** Aufbewahrungswochen vorgesehen werden.
- Bei einer monatlichen Aufbewahrung sollten mindestens **drei** Aufbewahrungsmonate vorgesehen werden.
- Bei einer jährlichen Aufbewahrung sollte mindestens **ein** Aufbewahrungsjahr vorgesehen werden.

## <a name="notifications-for-critical-operations"></a>Benachrichtigungen über kritische Vorgänge

Wenn kritische Vorgänge ausgeführt werden, erhält der Administrator der Abonnements in der Regel eine E-Mail-Benachrichtigung mit den Details des Vorgangs. Sie können im Azure-Portal zusätzliche E-Mail-Empfänger für diese Benachrichtigungen konfigurieren.

Die in diesem Artikel genannten Sicherheitsfeatures bieten Abwehrmechanismen gegen gezielte Angriffe. Vor allem aber bieten Ihnen diese Features die Möglichkeit, bei einem Angriff Ihre Daten wiederherzustellen.

## <a name="troubleshooting-errors"></a>Problembehandlung

| Vorgang | Fehlerdetails | Lösung |
| --- | --- | --- |
| Richtlinienänderung |Die Sicherungsrichtlinie konnte nicht geändert werden. Error: Beim aktuellen Vorgang ist aufgrund eines internen Dienstfehlers [0x29834] ein Fehler aufgetreten. Wiederholen Sie den Vorgang nach einiger Zeit. Wenn das Problem weiterhin besteht, wenden Sie sich an den Microsoft-Support. |**Ursache:**<br/>Dieser Fehler tritt auf, wenn Sicherheitseinstellungen aktiviert sind, Sie versuchen, die Beibehaltungsdauer unter die oben angegebenen Mindestwerte zu verkürzen, und eine nicht unterstützte Version verwenden (die unterstützten Versionen sind im ersten Hinweis in diesem Artikel angegeben). <br/>**Empfohlene Maßnahme:**<br/> In diesem Fall müssen Sie die Beibehaltungsdauer höher als den angegebenen Mindestzeitraum festlegen (sieben Tage für „Täglich“, vier Wochen für „Wöchentlich“, drei Wochen für „Monatlich“ oder ein Jahr für „Jährlich“), um mit richtlinienbezogenen Aktualisierungen fortzufahren. Die bevorzugte, optionale Vorgehensweise ist das Aktualisieren des Backup-Agents, von Azure Backup Server und/oder DPM-Updaterollups (UR), um alle Sicherheitsupdates zu erhalten. |
| Ändern der Passphrase |Die eingegebene Sicherheits-PIN ist nicht korrekt. (ID: 100130) Geben Sie die richtige Sicherheits-PIN an, um diesen Vorgang abzuschließen. |**Ursache:**<br/> Dieser Fehler tritt bei der Eingabe einer ungültigen oder abgelaufenen Sicherheits-PIN beim Ausführen eines kritischen Vorgangs (z.B. Ändern der Passphrase) auf. <br/>**Empfohlene Maßnahme:**<br/> Um den Vorgang abzuschließen, müssen Sie die gültige Sicherheits-PIN eingeben. Um die PIN abzurufen, melden Sie sich beim Azure-Portal an und navigieren zu „Recovery Services-Tresor > Einstellungen > Eigenschaften > Sicherheits-PIN generieren“. Verwenden Sie diese PIN, um die Passphrase zu ändern. |
| Ändern der Passphrase |Fehler bei dem Vorgang. ID: 120002 |**Ursache:**<br/>Dieser Fehler tritt auf, wenn Sicherheitseinstellungen aktiviert sind, Sie versuchen, die Passphrase zu ändern, und eine nicht unterstützte Version verwenden (die unterstützten Versionen sind im ersten Hinweis in diesem Artikel angegeben).<br/>**Empfohlene Maßnahme:**<br/> Um die Passphrase zu ändern, müssen Sie zuerst den Backup-Agent auf die Mindestversion 2.0.9052, Azure Backup-Server auf mindestens Update 1 und/oder DPM auf mindestens DPM 2012 R2 UR12 oder DPM 2016 UR2 (Downloadlinks siehe unten) aktualisieren und dann die gültige Sicherheits-PIN eingeben. Um die PIN abzurufen, melden Sie sich beim Azure-Portal an, und navigieren Sie zu „Recovery Services-Tresor“ > „Einstellungen“ > „Eigenschaften“ > „Sicherheits-PIN generieren“. Verwenden Sie diese PIN, um die Passphrase zu ändern. |

## <a name="next-steps"></a>Nächste Schritte

- [Erste Schritte mit Azure Recovery Services-Tresor](backup-azure-vms-first-look-arm.md), um diese Features zu aktivieren.
- [Laden Sie den neuesten Azure Recovery Services-Agent herunter](https://aka.ms/azurebackup_agent), um Ihre Windows-Computer und Ihre Sicherungsdaten gegen Angriffe zu schützen.
- [Laden Sie die neueste Version von Azure Backup Server herunter](https://support.microsoft.com/help/4457852/microsoft-azure-backup-server-v3), um Ihre Workloads und Sicherungsdaten gegen Angriffe zu schützen.
- Laden Sie [UR12 für System Center 2012 R2 Data Protection Manager](https://support.microsoft.com/help/3209592/update-rollup-12-for-system-center-2012-r2-data-protection-manager) oder [UR2 für System Center 2016 Data Protection Manager](https://support.microsoft.com/help/3209593/update-rollup-2-for-system-center-2016-data-protection-manager) herunter, um Workloads und Ihre Sicherungsdaten vor Angriffen zu schützen.
