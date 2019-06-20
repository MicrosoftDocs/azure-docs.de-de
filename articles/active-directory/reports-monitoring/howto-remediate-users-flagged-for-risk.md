---
title: Sicherheitsbericht „Benutzer mit Risikomarkierung“ im Azure Active Directory-Portal | Microsoft-Dokumentation
description: Hier erfahren Sie mehr über den Sicherheitsbericht „Benutzer mit Risikomarkierung“ im Azure Active Directory-Portal.
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7209f468f493e226fae22ccd260e8ceb2e570494
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60286674"
---
# <a name="remediate-users-flagged-for-risk-in-the-azure-active-directory-portal"></a>Behandeln von „Benutzer mit Risikomarkierung“ im Azure Active Directory-Portal

Mit den Sicherheitsberichten in Azure Active Directory (Azure AD) können Sie die Wahrscheinlichkeit für kompromittierte Benutzerkonten in Ihrer Umgebung einschätzen. Ein Benutzer mit Risikomarkierung ist ein Indikator für ein möglicherweise kompromittiertes Benutzerkonto.

Microsoft ist bestrebt, Sie beim Schutz Ihrer Umgebungen zu unterstützen. Im Rahmen dieses Engagements sucht Microsoft kontinuierlich nach ungewöhnlichen Aktivitäten sowie nach Aktivitäten, die bekannten Angriffsmustern entsprechen. 

Wenn ungewöhnliche Aktivitäten erkannt werden, die auf nicht autorisierte Zugriffe auf einige Konten Ihrer Benutzer hindeuten, erhalten Sie Benachrichtigungen, um darauf reagieren zu können. Dies bedeutet nicht, dass die eigenen Systeme von Microsoft kompromittiert wurden.

## <a name="access-the-users-flagged-for-risk-report"></a>Zugreifen auf den Bericht „Benutzer mit Risikomarkierung“

Sie können Benutzer mit Risikomarkierung im Azure-Portal über den [Bericht „Benutzer mit Risikomarkierung“](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RiskyUsers) anzeigen. Wenn Sie nicht über Azure AD verfügen, können Sie sich unter [https://aka.ms/AccessAAD](https://aka.ms/AccessAAD) kostenlos anmelden. 

Dem Bericht „Benutzer mit Risikomarkierung“ können Sie für jeden Benutzer die folgenden Aktionen entnehmen:

- Generieren eines temporären Kennworts
- Festlegen, dass der Benutzer bei der nächsten Anmeldung eine sichere Kennwortzurücksetzung ausführen muss
- Verwerfen des Benutzerrisikos, ohne eine Korrekturaktion auszuführen

Weitere Informationen finden Sie unter [Sicherheitsbericht „Benutzer mit Risikomarkierung“](concept-user-at-risk.md).

### <a name="azure-ad-subscription-for-office-365-customers"></a>Azure AD-Abonnement für Office 365-Kunden

Außerdem können Sie mit Ihren Office 365-Anmeldeinformationen auch auf das **Azure Admin Center** zugreifen. Nachdem Sie Ihren Zugriff auf Azure AD aktiviert haben, werden Sie zum Azure AD-Portal umgeleitet. Auf der Abonnementebene „Basic“ stehen in den Berichten nur eingeschränkte Details zur Verfügung. Azure Premium-Abonnementen haben dagegen Zugang zu weiteren Daten und Analysen.

So greifen Sie im Microsoft 365 Admin Center auf den Bericht **Benutzer mit Risikomarkierung** zu

1.  Wählen Sie im Navigationsbereich auf der linken Seite die Option **Admin Center**. 
2.  Wählen Sie **Azure AD**.
3.  Melden Sie sich beim **Azure Active Directory Admin Center** an.
4.  Wählen Sie den Link aus, falls am oberen Seitenrand ein Banner mit dem Text **Check out the new portal** (Neues Portal ausprobieren) erscheint.
4.  Wählen Sie im Navigationsmenü auf der linken Seite die Option **Azure Active Directory**. 
5.  Wählen Sie im Navigationsbereich im Abschnitt **Sicherheit** die Option **Benutzer mit Risikomarkierung**.

## <a name="remediation-actions"></a>Aktionen zur Problembehebung

Führen Sie die folgenden Schritte aus, um die betroffenen Konten zu korrigieren und Ihre Umgebung zu schützen:

1.  [Überprüfen Sie die Informationen](https://aka.ms/MFAValid) für die mehrstufige Authentifizierung und die Self-Service-Kennwortzurücksetzung. 
2.  [Aktivieren Sie die mehrstufige Authentifizierung](https://aka.ms/MFAuth) für alle Benutzer. 
3.  Verwenden Sie [dieses Bereinigungsskript](https://aka.ms/remediate), um für jedes betroffene Konto automatisch folgende Schritte auszuführen: 

    a. Zurücksetzen des Kennworts, um das Konto zu schützen und die Beendigung aktiver Sitzungen zu erzwingen

    b. Entfernen von Postfachstellvertretungen

    c. Deaktivieren von Regeln zur Weiterleitung von E-Mails an externe Domänen

    d. Entfernen der globalen E-Mail-Weiterleitungseigenschaft für das Postfach

    e. Aktivieren der mehrstufigen Authentifizierung für das Konto des Benutzers

    f. Festlegen einer hohen Kennwortkomplexität für das Konto

    g. Aktivieren der Postfachüberwachung

    h. Erstellen eines Überwachungsprotokolls zur Überprüfung durch den Administrator

4. Untersuchen Sie Ihren Office 365-Mandanten sowie die restliche IT-Infrastruktur – einschließlich aller Mandanteneinstellungen, Benutzerkonten und der benutzerspezifischen Konfigurationseinstellungen – auf mögliche Änderungen. Suchen Sie nach Anzeichen für Persistenzmethoden sowie nach Anzeichen dafür, dass ein Eindringling an VPN-Anmeldeinformationen gelangt ist oder sich Zugriff auf andere Ressourcen der Organisation verschafft hat. 

5.  Überlegen Sie sich im Rahmen Ihrer Untersuchung, ob Sie ggf. die Behörden (einschließlich der Strafverfolgungsbehörden) einschalten sollten.

Darüber hinaus empfiehlt sich Folgendes:

- Lesen und implementieren Sie diesen [Leitfaden für den Umgang mit ungewöhnlichen Aktivitäten](https://aka.ms/fixaccount). 
- [Aktivieren Sie die Überwachungspipeline](https://aka.ms/improvesecurity), um die Analyse der Aktivitäten Ihres Mandanten zu vereinfachen. Nach Abschluss des Vorgangs wird Ihr Überwachungsspeicher mit den Aktivitätsprotokollen gefüllt. Nun können Sie auch die [Such- und Untersuchungsressourcen des Security & Compliance Centers](https://aka.ms/sccsearch) nutzen. 
- Verwenden Sie [dieses Skript, um die Postfachüberwachung für alle Ihre Konten zu aktivieren](https://aka.ms/mailboxaudit1). 
- Überprüfen Sie die Berechtigungen der Stellvertretung und die E-Mail-Weiterleitungsregeln für alle Ihre Postfächer. Hierzu können Sie [dieses PowerShell-Skript](https://aka.ms/delegateforwardrules) verwenden. 

## <a name="next-steps"></a>Nächste Schritte

* [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
* [Sicherheitsbericht „Benutzer mit Risikomarkierung“ im Azure Active Directory-Portal](concept-user-at-risk.md)
