---
title: Verwenden von Azure Monitor-Arbeitsmappen mit Azure AD Domain Services | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe von Azure Monitor-Arbeitsmappen Sicherheitsüberwachungen überprüfen und Probleme in einer verwalteten Azure Active Directory Domain Services-Domäne ermitteln.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 3067388265fb69b916fe0e179cb896401b7c2dc4
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96618160"
---
# <a name="review-security-audit-events-in-azure-active-directory-domain-services-using-azure-monitor-workbooks"></a>Überprüfen von Sicherheitsüberwachungsereignissen in Azure Active Directory Domain Services mithilfe von Azure Monitor-Arbeitsmappen

Sie können Sicherheitsüberwachungsereignisse aktivieren, um den Status Ihrer verwalteten Azure Active Directory Domain Services-Domäne (Azure AD DS-Domäne) zu erfassen. Diese Sicherheitsüberwachungsereignisse können dann mithilfe von Azure Monitor-Arbeitsmappen überprüft werden, die Text, Analyseabfragen und Parameter in umfangreichen interaktiven Berichten kombinieren. Azure AD DS enthält Arbeitsmappenvorlagen für Sicherheitsübersicht und Kontoaktivität, mit denen Sie Überwachungsereignisse untersuchen und Ihre Umgebung verwalten können.

In diesem Artikel wird erläutert, wie Sie mithilfe von Azure Monitor-Arbeitsmappen Sicherheitsüberwachungsereignisse in Azure AD DS überprüfen können.

## <a name="before-you-begin"></a>Voraussetzungen

Für diesen Artikel benötigen Sie die folgenden Ressourcen und Berechtigungen:

* Ein aktives Azure-Abonnement.
    * Wenn Sie kein Azure-Abonnement besitzen, [erstellen Sie ein Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Einen mit Ihrem Abonnement verknüpften Azure Active Directory-Mandanten, der entweder mit einem lokalen Verzeichnis synchronisiert oder ein reines Cloudverzeichnis ist.
    * [Erstellen Sie einen Azure Active Directory-Mandanten][create-azure-ad-tenant], oder [verknüpfen Sie ein Azure-Abonnement mit Ihrem Konto][associate-azure-ad-tenant], sofern erforderlich.
* Eine verwaltete Azure Active Directory Domain Services-Domäne, die in Ihrem Azure AD-Mandanten aktiviert und konfiguriert ist.
    * Führen Sie bei Bedarf das Tutorial zum [Erstellen und Konfigurieren einer verwalteten Azure Active Directory Domain Services-Domäne][create-azure-ad-ds-instance] aus.
* Sicherheitsüberwachungsereignisse, die für Ihre verwaltete Domäne aktiviert sind und Daten in einen Log Analytics-Arbeitsbereich streamen.
    * Sie können bei Bedarf [Sicherheitsüberwachungen in Azure AD DS aktivieren][enable-security-audits].

## <a name="azure-monitor-workbooks-overview"></a>Übersicht über Azure Monitor-Arbeitsmappen

Wenn Sicherheitsüberwachungsereignisse in Azure AD DS aktiviert sind, kann es schwierig sein, Probleme in der verwalteten Domäne zu analysieren und zu erkennen. Mit Azure Monitor können Sie diese Sicherheitsüberwachungsereignisse aggregieren und die Daten abfragen. Mit Azure Monitor-Arbeitsmappen können Sie diese Daten visualisieren, um Probleme schneller und einfacher erkennen zu können.

Arbeitsmappenvorlagen sind zusammengestellte Berichte, die von mehreren Benutzern und Teams flexibel wiederverwendet werden können. Wenn Sie eine Arbeitsmappenvorlage öffnen, werden die Daten aus der Azure Monitor-Umgebung geladen. Sie können Vorlagen verwenden, ohne dass sich dies auf andere Benutzer in der Organisation auswirkt, und basierend auf der Vorlage Ihre eigenen Arbeitsmappen speichern.

Azure AD DS schließt die folgenden beiden Arbeitsmappenvorlagen ein:

* Security Overview Report (Sicherheitsübersichtsbericht)
* Account Activity Report (Bericht zur Kontoaktivität)

Weitere Informationen zum Bearbeiten und Verwalten von Arbeitsmappen finden Sie unter [Übersicht über Azure Monitor-Arbeitsmappen](../azure-monitor/platform/workbooks-overview.md).

## <a name="use-the-security-overview-report-workbook"></a>Verwenden der Arbeitsmappe „Security Overview Report“ (Sicherheitsübersichtsbericht)

Damit Sie die Nutzung besser verstehen und potenzielle Sicherheitsbedrohungen erkennen können, sind im „Security Overview Report“ (Sicherheitsübersichtsbericht) die Anmeldedaten zusammengefasst und Konten angegeben, die Sie überprüfen sollten. Sie können Ereignisse in einem bestimmten Datumsbereich anzeigen und Detailinformationen zu spezifischen Anmeldeereignissen anzeigen, z. B. Versuche mit falschem Kennwort oder mit deaktiviertem Konto.

Führen Sie die folgenden Schritte aus, um auf die Arbeitsmappenvorlage für „Security Overview Report“ (Sicherheitsübersichtsbericht) zuzugreifen:

1. Suchen Sie im Azure-Portal die Option **Azure Active Directory Domain Services**, und wählen Sie sie aus.
1. Wählen Sie Ihre verwaltete Domäne (z. B. *aaddscontoso.com*) aus.
1. Wählen Sie im Menü auf der linken Seite **Überwachung > Arbeitsmappen** aus.

    ![Screenshot, aus dem hervorgeht, wo der Sicherheitsübersichtsbericht und der Bericht zur Kontoaktivität ausgewählt werden.](./media/use-azure-monitor-workbooks/select-workbooks-in-azure-portal.png)

1. Wählen Sie **Security Overview Report** (Sicherheitsübersichtsbericht) aus.
1. Wählen Sie in den Dropdownmenüs oben in der Arbeitsmappe Ihr Azure-Abonnement und dann einen Azure Monitor-Arbeitsbereich aus.

    Wählen Sie unter **Zeitbereich** einen Wert aus, etwa *Letzte 7 Tage*, wie im folgenden Beispielscreenshot gezeigt:

    ![Auswählen der Menüoption „Arbeitsmappen“ im Azure-Portal](./media/use-azure-monitor-workbooks/select-query-filters.png)

    Auch die Optionen **Kachelansicht** und **Diagrammansicht** können so geändert werden, dass die Daten wie gewünscht analysiert und visualisiert werden.

1. Um Detailinformationen zu einem spezifischen Ereignistyp anzuzeigen, wählen Sie eine der Karten unter **Anmeldeergebnisse** aus, z. B. *Account Locked Out* (Konto gesperrt), wie im folgenden Beispiel gezeigt:

    ![Beispiel: Daten von „Security Overview Report“ (Sicherheitsübersichtsbericht) visualisiert in Azure Monitor-Arbeitsmappen](./media/use-azure-monitor-workbooks/example-security-overview-report.png)

1. Im unteren Bereich von „Security Overview Report“ (Sicherheitsübersichtsbericht) unterhalb des Diagramms wird dann der ausgewählte Aktivitätstyp aufgeschlüsselt. Sie können wie im folgenden Beispielbericht gezeigt auf der rechten Seite nach enthaltenen Benutzernamen filtern:

    [![Details zu Kontosperrungen in Azure Monitor-Arbeitsmappen](./media/use-azure-monitor-workbooks/account-lockout-details-cropped.png)](./media/use-azure-monitor-workbooks/account-lockout-details.png#lightbox)

## <a name="use-the-account-activity-report-workbook"></a>Verwenden der Arbeitsmappe „Account Activity Report“ (Bericht zur Kontoaktivität)

Zur Unterstützung bei der Behandlung von Problemen bei einem spezifischen Benutzerkonto werden in „Account Activity Report“ (Bericht zur Kontoaktivität) Informationen zu Überwachungsereignisprotokollen detailliert aufgegliedert. Sie können sehen, wann ein ungültiger Benutzername oder ein ungültiges Passwort bei der Anmeldung angegeben wurde, und die Quelle des Anmeldeversuchs überprüfen.

Führen Sie die folgenden Schritte aus, um auf die Arbeitsmappenvorlage für „Account Activity Report“ (Bericht zur Kontoaktivität) zuzugreifen:

1. Suchen Sie im Azure-Portal die Option **Azure Active Directory Domain Services**, und wählen Sie sie aus.
1. Wählen Sie Ihre verwaltete Domäne (z. B. *aaddscontoso.com*) aus.
1. Wählen Sie im Menü auf der linken Seite **Überwachung > Arbeitsmappen** aus.
1. Wählen Sie **Account Activity Report** (Bericht zur Kontoaktivität) aus.
1. Wählen Sie in den Dropdownmenüs oben in der Arbeitsmappe Ihr Azure-Abonnement und dann einen Azure Monitor-Arbeitsbereich aus.

    Wählen Sie einen **Zeitbereich** aus, z. B. *Letzte 30 Tage*, und legen Sie dann fest, wie die Daten in der **Kachelansicht** dargestellt werden sollen.

    Sie können nach **Kontobenutzername** filtern, z. B. *felix*, wie im folgenden Beispielbericht dargestellt:

    [![„Account Activity Report“ (Bericht zur Kontoaktivität) in Azure Monitor-Arbeitsmappen](./media/use-azure-monitor-workbooks/account-activity-report-cropped.png)](./media/use-azure-monitor-workbooks/account-activity-report.png#lightbox)

    Im Bereich unterhalb des Diagramms werden einzelne Anmeldeereignisse mit zugehörigen Informationen angezeigt, z. B. dem Aktivitätsergebnis und der Quellarbeitsstation. Anhand dieser Informationen können Sie wiederholte Quellen von Anmeldeereignissen ermitteln, die möglicherweise zu Kontosperrungen führen oder auf einen potenziellen Angriff hindeuten.

Wie in „Security Overview Report“ (Sicherheitsübersichtsbericht) können Sie oben im Bericht Detailinformationen zu den unterschiedlichen Kacheln anzeigen, um die Daten nach Bedarf zu visualisieren und zu analysieren.

## <a name="save-and-edit-workbooks"></a>Speichern und Bearbeiten von Arbeitsmappen

Die beiden in Azure AD DS bereitgestellten Arbeitsmappenvorlagen bieten einen guten Ausgangspunkt für eigene Datenanalysen. Wenn Sie in den Datenabfragen und -untersuchungen differenziertere Informationen benötigen, können Sie eigene Arbeitsmappen speichern und die Abfragen bearbeiten.

1. Um eine Kopie einer der Arbeitsmappenvorlagen zu speichern, wählen Sie **Bearbeiten > Speichern unter > Freigegebene Berichte** aus, geben Sie einen Namen ein, und speichern Sie die Kopie.
1. Wählen Sie der Kopie der Vorlage die Option **Bearbeiten** aus, um in den Bearbeitungsmodus zu wechseln. Sie können neben jedem Bereich des Berichts die blaue Schaltfläche **Bearbeiten** auswählen und den Bereich ändern.

Alle Diagramme und Tabellen in Azure Monitor-Arbeitsmappen werden mithilfe von Kusto-Abfragen generiert. Weitere Informationen zum Erstellen eigener Abfragen finden Sie unter [Protokollabfragen in Azure Monitor][azure-monitor-queries] und im [Tutorial zu Kusto-Abfragen][kusto-queries].

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie Kennwort- und Kontosperrungsrichtlinien anpassen möchten, finden Sie entsprechende Informationen unter [Kennwort- und Kontosperrungsrichtlinien in verwalteten Domänen][password-policy].

Weitere Informationen zu Problemen bei Benutzern finden Sie unter der Behandlung von [Problemen bei der Anmeldung][troubleshoot-sign-in] oder [Problemen mit gesperrten Konten][troubleshoot-account-lockout].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[enable-security-audits]: security-audit-events.md
[password-policy]: password-policy.md
[troubleshoot-sign-in]: troubleshoot-sign-in.md
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-monitor-queries]: /azure/data-explorer/kusto/query/
[kusto-queries]: /azure/kusto/query/tutorial?pivots=azuredataexplorer