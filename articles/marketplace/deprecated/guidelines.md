---
title: Azure Marketplace- und AppSource-Richtlinien für Herausgeber | Azure
description: Azure Marketplace und AppSource-Richtlinien für Herausgeber von Apps und Diensten
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: jm-aditi-ms
manager: pabutler
ms.service: marketplace
ms.topic: article
ms.date: 06/13/2018
ms.author: ellacroi
ms.openlocfilehash: 57eeb9b6ac6eb826e2b371e6ff4dfd71f8cef49c
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66259360"
---
# <a name="guidelines"></a>Richtlinien  

<!--
## Guidelines for AppSource  
-->
---

## <a name="guidelines-for-azure-marketplace"></a>Richtlinien für Azure Marketplace  

### <a name="guidelines-for-creating-a-microsoft-id-to-manage-a-marketplace-account"></a>Richtlinien für die Erstellung einer Microsoft-ID zur Verwaltung eines Marketplace-Kontos  
Wenn mehrere Personen Zugriff auf die gleiche Microsoft-ID benötigen, mit der Ihr Marketplace-Konto erstellt wurde, sollten Sie diese Richtlinien befolgen, um ein Unternehmenskonto zu erstellen. 

>[!IMPORTANT]
>Um mehrere Benutzer für den Zugriff auf Ihr Konto für Microsoft Developer Center (Dev Center) zu autorisieren, empfiehlt Microsoft die Verwendung von Azure Active Directory (Azure AD). Dort können Sie einzelnen Benutzern Rollen zuweisen. Jeder Benutzer muss für den Zugriff auf das Konto eigene Azure AD-Anmeldeinformationen verwenden. Erstellen Sie eine Microsoft-ID unter Verwendung einer E-Mail-Adresse in einer Domäne, die für Ihr Unternehmen registriert ist. Microsoft empfiehlt, diese E-Mail-Adresse nicht einer Einzelperson zuzuweisen. Ein Beispiel ist `windowsapps@fabrikam.com`.  
>*   Weitere Informationen finden Sie im Abschnitt [Problem: Microsoft-ID in einer Azure AD-Verbunddomäne](#issue-microsoft-id-in-an-azure-ad-federated-domain).  

*   Beschränken Sie den Zugriff auf die Microsoft-ID auf eine möglich geringe Anzahl von Entwicklern. 
*   Richten Sie im Unternehmens-E-Mail-Konto eine Verteilerliste mit allen Personen ein, die auf Ihr Dev Center-Konto zugreifen müssen. Fügen Sie die E-Mail-Adresse der Verteilerliste zu Ihren Sicherheitsinformationen hinzu. Mit einer solchen Verteilerliste können alle Mitarbeiter auf der Liste auf Anforderung Sicherheitscodes erhalten und die Sicherheitsinformationen für Ihre Microsoft-ID verwalten. Falls die Einrichtung einer Verteilerliste nicht möglich ist, muss der Besitzer des einzelnen E-Mail-Kontos verfügbar sein, um bei entsprechender Aufforderung auf den Sicherheitscode zuzugreifen und diesen weiterzugeben.  
    *   Der Besitzer erhält beispielsweise eine Benachrichtigung, wenn der Microsoft-ID neue Sicherheitsinformationen hinzugefügt werden oder wenn von einem neuen Gerät aus auf die ID zugegriffen wird.  
*   Fügen Sie eine Telefonnummer hinzu, die keine Durchwahl benötigt, und auf die wichtige Teammitglieder zugreifen können.  
*   Im Allgemeinen sollten Sie Entwickler verpflichten, vertrauenswürdige Geräte für die Anmeldung bei Ihrem Dev Center-Konto zu verwenden. Alle wichtigen Teammitglieder sollten auf die vertrauenswürdigen Geräte zugreifen können. Durch die Verwendung von vertrauenswürdigen Geräten ist es seltener erforderlich, Sicherheitscodes zu senden, wenn jemand auf das Dev Center-Konto zugreift.  
*   Wenn Sie Zugriff auf das Dev Center-Konto von nicht vertrauenswürdigen Computern gewähren müssen, sollten Sie diesen Zugriff auf maximal fünf Entwickler beschränken. Im Idealfall sollten Ihre Entwickler mit Computern auf das Konto zugreifen, die sich am gleichen geografischen und Netzwerkstandort befinden.  
*   Überprüfen Sie Ihre Sicherheitsinformationen häufig.  
    *   Zum Anzeigen der Sicherheitsinformationen besuchen Sie die Seite mit den Sicherheitseinstellungen unter [account.live.com/proofs/Manage](https://account.live.com/proofs/Manage).

Der Zugriff auf Ihr Dev Center-Konto sollte primär über vertrauenswürdige Computer erfolgen. Dies ist sehr wichtig, denn die Anzahl von Codes, die pro Woche für ein Dev Center-Konto generiert werden können, ist begrenzt. Durch die Verwendung von vertrauenswürdigen Computern gewährleisten Sie zudem ein höchst sicheres und konsistentes Anmeldeverfahren. 
*   Weitere Informationen zu zusätzlichen Richtlinien und Sicherheitsmaßnahmen für Dev Center-Konten finden Sie im Artikel „Eröffnen eines Entwicklerkontos“ unter [docs.microsoft.com/windows/uwp/publish/opening-a-developer-account](https://docs.microsoft.com/windows/uwp/publish/opening-a-developer-account). 

---

#### <a name="issue-microsoft-id-in-an-azure-ad-federated-domain"></a>Problem: Microsoft-ID in einer Azure AD-Verbunddomäne  
Ihr Unternehmenskonto befindet sich möglicherweise in einem Azure Active Directory-Verbund. Wenn Sie versuchen, eine Microsoft-ID mit einer Unternehmens-E-Mail-Adresse zu erstellen, die sich in einem Azure AD-Verbund befindet, erhalten Sie eine Fehlermeldung. Wenn ein Fehler auftritt, sollten Sie bei Ihrem IT-Team nachfragen, ob sich Ihr Konto in einem Azure AD-Verbund befindet. E-Mail-Adressen in einem Azure AD-Verbund sind ein bekanntes Problem, an dessen Lösung Microsoft arbeitet.  
*   Weitere Informationen zu Azure AD finden Sie in der Dokumentation zu Azure Active Directory unter [docs.microsoft.com/azure/active-directory](https://docs.microsoft.com/azure/active-directory).

Microsoft empfiehlt eine Problemumgehung. Führen Sie die folgenden Schritte aus, um eine neue E-Mail-Adresse in der Domäne `outlook.com` und eine Regel zur Weiterleitung der Kommunikation zu erstellen.  
1.  Wechseln Sie zur Seite „Konto erstellen“, und klicken Sie auf den Link „Neue E-Mail-Adresse anfordern“. 
    *   Um sich für Ihre Microsoft-ID zu registrieren, öffnen Sie die Seite „Konto erstellen“ unter [signup.live.com/signup](https://signup.live.com/signup).  
2.  Erstellen Sie die neue E-Mail-Adresse, und geben Sie ein Kennwort ein. In der Domäne `outlook.com` werden eine neue Microsoft-ID und ein neues E-Mail-Postfach erstellt. Setzen Sie die Registrierung fort, bis das Konto erstellt ist.  

    >[!IMPORTANT]
    >Sie müssen eine als Microsoft-ID registrierte E-Mail-Adresse oder Verteilerliste verwenden, um sich bei Dev Center zu registrieren. Microsoft empfiehlt die Verwendung einer Verteilerliste, um die Abhängigkeit von Einzelpersonen zu beseitigen. Wenn Ihre E-Mail-Adresse oder Verteilerliste noch nicht registriert ist, müssen Sie die Registrierung jetzt durchführen.    

    >[!Important]
    >Wenn sich Ihre E-Mail-Adresse in der `Microsoft`-Unternehmensdomäne befindet, können Sie diese Adresse nicht für die Registrierung bei Dev Center verwenden.  

3.  Nachdem Sie die Microsoft-ID mit der Outlook-E-Mail-Adresse erstellt haben, melden Sie sich bei Ihrem Outlook-Postfach an. Erstellen Sie eine Regel zur Weiterleitung von E-Mails. Diese Regel sollte alle E-Mails, die im Outlook-Postfach empfangen werden, an die E-Mail-Adresse in Ihrer Domäne weiterleiten, die Sie für die Verwaltung Ihres Marketplace-Kontos erstellt haben.  
    *   Um sich bei Ihrem Outlook-Postfach anzumelden, öffnen Sie die Outlook-Seite unter [outlook.live.com/owa](https://outlook.live.com/owa).  
    *   Weitere Informationen zu Weiterleitungsregeln finden Sie auf der Seite „Verwenden von Regeln in Outlook Web App zum automatischen Weiterleiten von Nachrichten an ein anderes Konto“ unter [support.office.com/article/Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed](https://support.office.com/article/Use-rules-in-Outlook-Web-App-to-automatically-forward-messages-to-another-account-1433e3a0-7fb0-4999-b536-50e05cb67fed).  

1.  Die Weiterleitungsregel sendet alle E-Mails, die im Outlook-E-Mail-Konto empfangen werden, an eine E-Mail-Adresse in einer für Ihr Unternehmen registrierten Domäne. Ihre `outlook.com`-E-Mail-Adresse muss für die Authentifizierung sowohl in Dev Center als auch im Cloud-Partnerportal verwendet werden.  

## <a name="next-steps"></a>Nächste Schritte

*   Besuchen Sie die Seite [Herausgeberleitfaden für Azure Marketplace und AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide). 
 
---
