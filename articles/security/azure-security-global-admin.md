---
title: Aktivieren der MFA für alle Azure-Administratoren
description: Anleitungen zum Aktivieren von globalen Administratoren
ms.service: security
author: barclayn
manager: barbkess
editor: TomSh
ms.topic: article
ms.date: 03/20/2018
ms.author: barclayn
ms.openlocfilehash: fbc3bba3d95526277da3487cb8670c1f1a88a925
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65951773"
---
# <a name="enforce-multi-factor-authentication-mfa-for-subscription-administrators"></a>Erzwingen der mehrstufigen Authentifizierung (Multi-Factor Authentication, MFA) für Abonnementadministratoren

Bei der Erstellung Ihrer Administratoren, einschließlich Ihres globalen Administratorkontos, ist es wichtig, dass Sie sehr starke Authentifizierungsmethoden verwenden.

Sie können die tagtägliche Verwaltung durch Zuweisen von bestimmten Administratorrollen – z.B. Exchange-Administrator oder Kennwortadministrator – zu Benutzerkonten von IT-Mitarbeitern nach Bedarf durchführen.
Aktivieren Sie darüber hinaus [Azure Multi-Factor Authentication (MFA)](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) für Ihre Administratoren, um eine zweite Sicherheitsebene für Benutzeranmeldungen und Transaktionen hinzuzufügen. Azure MFA hilft der IT, die Wahrscheinlichkeit zu verringern, dass kompromittierte Anmeldeinformationen Zugriff auf die Daten einer Organisation erhalten.

Beispiel:  Sie erzwingen Azure MFA für Ihre Benutzer und konfigurieren den Vorgang so, dass zur Verifizierung ein Telefonanruf oder eine SMS verwendet wird. Wenn die Anmeldeinformationen eines Benutzers kompromittiert werden, kann der Angreifer dann nicht auf Ressourcen zugreifen, da er keinen Zugriff auf das Telefon des Benutzers hat. Organisationen, die keine zusätzliche Schicht zur Identitätssicherung hinzufügen, sind anfälliger für Angriffe mit dem Ziel des Diebstahls von Anmeldeinformationen, was wiederum zu kompromittierten Daten führen kann.

Eine Alternative für Organisationen, die die gesamte Authentifizierung weiterhin lokal steuern möchten, ist die Verwendung des [Azure Multi-Factor Authentication-Servers](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-server). Dies wird auch als „MFA lokal“ bezeichnet. Mithilfe dieser Methode können Sie weiterhin die Multi-Factor Authentication erzwingen und dabei den MFA-Server lokal halten.

Mit folgendem Microsoft Azure AD-V2-PowerShell-Befehl können Sie überprüfen, wer in Ihrer Organisation über Administratorrechte verfügt:

```azurepowershell-interactive
Get-AzureADDirectoryRole | Where { $_.DisplayName -eq "Company Administrator" } | Get-AzureADDirectoryRoleMember | Ft DisplayName
```

## <a name="enabling-mfa"></a>Aktivieren von MFA

Erfahren Sie, wie [MFA](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next) arbeitet, bevor Sie fortfahren.

Solange Ihre Benutzer über Lizenzen verfügen, die Azure Multi-Factor Authentication enthalten, müssen Sie nichts unternehmen, um Azure MFA zu aktivieren. Sie können die zweistufige Überprüfung auf Benutzerbasis obligatorisch machen. Azure MFA kann mit den folgenden Lizenzen aktiviert werden:

- Azure Multi-Factor Authentication
- Azure Active Directory Premium
- Enterprise Mobility + Security

## <a name="turn-on-two-step-verification-for-users"></a>Aktivieren der zweistufigen Überprüfung für Benutzer

Verwenden Sie eine der unter [Vorgehensweise zum Erzwingen einer zweistufigen Überprüfung für einen Benutzer oder eine Gruppe](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states) aufgeführten Verfahrensweisen, um mit der Verwendung von Azure MFA zu beginnen. Sie können die zweistufige Überprüfung auch für alle Anmeldungen erzwingen oder Richtlinien für bedingten Zugriff erstellen, um die zweistufige Überprüfung nur dann vorauszusetzen, wenn Sie es für wichtig erachten.

