---
title: Was ist Azure Key Vault? | Microsoft-Dokumentation
description: Erfahren Sie, wie der Azure Key Vault kryptografische Schlüssel und Geheimnisse schützt, die von Cloudanwendungen und -diensten verwendet werden.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: mbaldwin
ms.openlocfilehash: 6c1da45115303bb0a67d6ff796a40ef47c24224a
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287430"
---
# <a name="azure-key-vault-basic-concepts"></a>Grundlegende Konzepte von Azure Key Vault

Azure Key Vault ist ein Clouddienst zum sicheren Speichern und Zugreifen auf Geheimnisse. Als Geheimnis wird alles bezeichnet, für das Sie den Zugriff streng kontrollieren möchten, z. B. API-Schlüssel, Kennwörter, Zertifikate oder kryptografische Schlüssel. Der Key Vault-Dienst unterstützt zwei Arten von Containern: Tresore und verwaltete HSM-Pools. Tresore unterstützen die Speicherung von Software und HSM-gestützten Schlüsseln, Geheimnissen und Zertifikaten. Verwaltete HSM-Pools unterstützen nur HSM-gestützte Schlüssel. Ausführliche Informationen finden Sie unter [Übersicht über die Azure Key Vault-REST-API](about-keys-secrets-certificates.md).

Hier sind weitere wichtige Begriffe:

- **Tenant:** Ein Mandant ist die Organisation, die eine bestimmte Instanz von Microsoft-Clouddiensten besitzt und verwaltet. Der am häufigsten verwendete Begriff, um auf die Azure- und Microsoft 365-Dienste für eine Organisation zu verweisen.

- **Tresorbesitzer:** Ein Tresorbesitzer kann einen Schlüsseltresor erstellen und über Vollzugriff sowie über uneingeschränkte Kontrolle verfügen. Außerdem kann der Tresorbesitzer auch eine Überprüfung einrichten, um zu protokollieren, wer auf Geheimnisse und Schlüssel zugreift. Administratoren können den Lebenszyklus des Schlüssels steuern. Sie können zu einer neuen Version des Schlüssels wechseln, den Schlüssel sichern und ähnliche Aufgaben ausführen.

- **Tresorconsumer:** Ein Tresorconsumer kann Aktionen für die Objekte im Schlüsseltresor ausführen, wenn der Tresorbesitzer dem Consumer Zugriff gewährt. Welche Aktionen verfügbar sind, hängt von den gewährten Berechtigungen ab.

- **Administratoren für verwaltete HSMs** : Benutzer, denen die Rolle „Administrator“ zugewiesen ist, haben die vollständige Kontrolle über einen verwalteten HSM-Pool. Sie können weitere Rollenzuweisungen erstellen, um den kontrollierten Zugriff an andere Benutzer zu delegieren.

- **Kryptoverantwortlicher/Kryptografiebenutzer für verwaltete HSMs** : Integrierte Rollen, die normalerweise Benutzern oder Dienstprinzipalen zugewiesen werden, die kryptografische Vorgänge mithilfe von Schlüsseln in verwaltetem HSM durchführen. Der Kryptografiebenutzer kann neue Schlüssel erstellen, aber keine Schlüssel löschen.

- **Kryptografiedienstverschlüsselung für verwaltete HSMs** : Integrierte Rolle, die in der Regel für die Verschlüsselung von ruhenden Daten mit kundenseitig verwaltetem Schlüssel einem Dienstkonto mit verwalteter Dienstidentität (z. B. Speicherkonto) zugewiesen wird.

- **Ressource:** Eine Ressource ist ein verwaltbares Element, das über Azure verfügbar ist. Allgemeine Beispiele sind virtuelle Computer, Speicherkonten, Web-Apps, Datenbanken und virtuelle Netzwerke. Es gibt noch viele weitere.

- **Ressourcengruppe** : Eine Ressourcengruppe ist ein Container, der verwandte Ressourcen für eine Azure-Lösung enthält. Die Ressourcengruppe kann alle Ressourcen für die Lösung oder nur die Ressourcen enthalten, die Sie als Gruppe verwalten möchten. Sie entscheiden in Abhängigkeit davon, was für Ihre Organisation am sinnvollsten ist, wie Sie die Ressourcen den Ressourcengruppen zuordnen möchten.

- **Sicherheitsprinzipal:** Ein Azure-Sicherheitsprinzipal ist eine Sicherheitsidentität, die durch von Benutzern erstellte Apps, Dienste und Automatisierungstools verwendet wird, um auf bestimmte Azure-Ressourcen zuzugreifen. Das Konzept lässt sich als „Benutzeridentität“ (Benutzername und Kennwort oder Zertifikat) mit einer bestimmten Rolle und streng kontrollierten Berechtigungen beschreiben. Ein Sicherheitsprinzipal sollte – im Gegensatz zu einer allgemeinen Benutzeridentität – nur für bestimmte Dinge zuständig sein. Wenn Sie ihm nur die Berechtigungen gewähren, die er zum Ausführen seiner Verwaltungsaufgaben benötigt, verbessert das die Sicherheit. Ein Sicherheitsprinzipal, der mit einer Anwendung oder einem Dienst verwendet wird, wird als **Dienstprinzipal** bezeichnet.

- [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md): Azure AD ist der Active Directory-Dienst für einen Mandanten. Jedes Verzeichnis verfügt über mindestens eine Domäne. Einem Verzeichnis können viele Abonnements zugeordnet sein, aber nur ein Mandant.

- **ID des Azure-Mandanten:** Eine Azure-Mandanten-ID ist eine Möglichkeit zur eindeutigen Identifizierung einer Azure AD-Instanz in einem Azure-Abonnement.

- **Verwaltete Identitäten** : Azure Key Vault bietet eine Möglichkeit zum sicheren Speichern von Anmeldeinformationen und anderen Schlüsseln und Geheimnissen. Um diese abrufen zu können, muss sich Ihr Code jedoch bei Key Vault authentifizieren. Die Verwendung einer verwalteten Identität vereinfacht die Lösung dieses Problems, indem für Azure-Dienste eine automatisch verwaltete Identität in Azure AD bereitgestellt wird. Sie können diese Identität für die Authentifizierung bei Key Vault oder jedem anderen Dienst verwenden, der die Azure AD-Authentifizierung unterstützt. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein. Weitere Informationen finden Sie in der nachfolgenden Abbildung und unter [Verwaltete Identitäten für Azure-Ressourcen – Übersicht](../../active-directory/managed-identities-azure-resources/overview.md).

    ![Diagramm zur Funktionsweise von verwalteten Identitäten für Azure-Ressourcen](../media/key-vault-whatis/msi.png)

## <a name="authentication"></a>Authentifizierung
Sie müssen sich zuerst authentifizieren, um Vorgänge mit Key Vault durchführen zu können. Es gibt drei Möglichkeiten für die Authentifizierung bei Key Vault:

- [Verwaltete Identitäten für Azure-Ressourcen:](../../active-directory/managed-identities-azure-resources/overview.md) Wenn Sie eine App auf einer VM in Azure bereitstellen, können Sie Ihrer VM eine Identität zuweisen, die Zugriff auf den Key Vault hat. Sie können Identitäten auch [anderen Azure-Ressourcen](../../active-directory/managed-identities-azure-resources/overview.md) zuweisen. Der Vorteil bei dieser Vorgehensweise ist, dass die App bzw. der Dienst die Drehung des ersten Geheimnisses nicht verwaltet. Die Identität wird von Azure automatisch gedreht. Als bewährte Methode wird diese Vorgehensweise empfohlen. 
- **Dienstprinzipal und Zertifikat:** Sie können einen Dienstprinzipal und ein zugehöriges Zertifikat verwenden, das Zugriff auf Key Vault hat. Diese Vorgehensweise wird nicht empfohlen, da der Besitzer oder Entwickler der Anwendung das Zertifikat drehen muss.
- **Dienstprinzipal und Geheimnis:** Obwohl Sie einen Dienstprinzipal und ein Geheimnis zur Authentifizierung bei Key Vault verwenden können, wird diese Vorgehensweise nicht empfohlen. Es ist schwierig, das Bootstrapgeheimnis, das zur Authentifizierung bei Key Vault verwendet wird, automatisch zu drehen.


## <a name="key-vault-roles"></a>Key Vault-Rollen

Die folgende Tabelle enthält Informationen dazu, wie Sie mithilfe des Schlüsseltresors die Anforderungen von Entwicklern und Sicherheitsadministratoren erfüllen können.

| Role | Problembeschreibung | Lösung durch den Azure-Schlüsseltresor |
| --- | --- | --- |
| Entwickler einer Azure-Anwendung |„Ich möchte eine Anwendung für Azure schreiben, die Schlüssel für die Signierung und Verschlüsselung verwendet. Diese Schlüssel sollen sich jedoch außerhalb meiner Anwendung befinden, damit die Lösung auch für eine geografisch verteilte Anwendung geeignet ist. <br/><br/>Ich möchte Schlüssel und Geheimnisse schützen, ohne den Code dafür selbst zu schreiben. Außerdem sollen die Schlüssel auf einfache Weise und mit optimaler Leistung in meinen Anwendungen verwendbar sein.“ |√ Schlüssel werden in einem Tresor gespeichert und bei Bedarf über einen URI aufgerufen.<br/><br/> √ Schlüssel werden mit branchenüblichen Algorithmen, Schlüssellängen und Hardwaresicherheitsmodulen von Azure geschützt.<br/><br/> √ Schlüssel werden in HSMs verarbeitet, die sich in den gleichen Azure-Datencentern befinden wie die Anwendungen. Durch diese Methode werden im Gegensatz zu einer Lösung, bei der die Schlüssel an einem anderen Ort (beispielsweise lokal) vorliegen, eine höhere Zuverlässigkeit und eine geringere Latenz erreicht. |
| Entwickler von SaaS-Lösungen (Software-as-a-Service) |„Ich möchte weder die Verantwortung noch die Haftung für Schlüssel und geheime Schlüssel für Kundenmandanten übernehmen. <br/><br/>Ich möchte, dass Kunden sowohl die Verantwortung als auch die Verwaltung für ihre Schlüssel übernehmen, sodass ich mich auf meine eigentlichen Aufgaben konzentrieren kann – die Bereitstellung der Softwarefunktionen.“ |√ Kunden können eigene Schlüssel in Azure importieren und diese verwalten. Wenn eine SaaS-Anwendung kryptografische Vorgänge unter Verwendung von Kundenschlüsseln ausführen muss, führt der Schlüsseltresor diese Vorgänge im Auftrag der Anwendung aus. Die Anwendung hat keine Informationen über die Kundenschlüssel. |
| Sicherheitsbeauftragter (Chief Security Officer, CSO) |„Ich möchte sicherstellen, dass unsere Anwendungen den Anforderungen für FIPS 140-2 Level 2 oder FIPS 140-2 Level 3 HSMs für die sichere Schlüsselverwaltung entsprechen. <br/><br/>Ich möchte sicherstellen, dass meine Organisation die Kontrolle über den gesamten Lebenszyklus der Schlüssel behält und die Schlüsselverwendung überwachen kann. <br/><br/>Auch wenn wir verschiedene Azure-Dienste und -Ressourcen nutzen, möchte ich die Schlüssel über einen einzigen Speicherort in Azure verwalten.“ |√ Wählen Sie **Tresore** für HSMs aus, die gemäß FIPS 140-2 Level 2 überprüft wurden.<br/>√ Wählen Sie **Verwaltete HSM-Pools** für HSMs aus, die gemäß FIPS 140-2 Level 3 überprüft wurden.<br/><br/>√ Mit Key Vault sind Ihre Schlüssel für Microsoft nicht sichtbar und können auch nicht extrahiert werden.<br/>√ Die Schlüsselverwendung wird nahezu in Echtzeit protokolliert.<br/><br/>√ Der Tresor stellt eine einzige Benutzeroberfläche bereit – unabhängig davon, über wie viele Tresore Sie in Azure verfügen, welche Regionen diese unterstützen und von welchen Anwendungen die Tresore verwendet werden. |

Jeder Benutzer mit einem Azure-Abonnement kann Schlüsseltresore erstellen und verwenden. Wenngleich der Schlüsseltresor insbesondere Entwicklern und Sicherheitsadministratoren Vorteile bietet, kann er durch einen Organisationsadministrator implementiert und verwaltet werden, der andere Azure-Dienste verwaltet. So kann sich dieser Administrator beispielsweise mit einem Azure-Abonnement anmelden, einen Tresor für die Schlüsselspeicherung der Organisation erstellen und dann operative Vorgänge wie die folgenden übernehmen:

- Erstellen oder Importieren eines Schlüssels oder geheimer Informationen
- Widerrufen oder Löschen eines Schlüssels oder geheimer Informationen
- Autorisieren von Benutzern oder Anwendungen für den Zugriff auf den Schlüsseltresor, sodass sie die Schlüssel und geheimen Schlüssel verwalten oder verwenden können
- Konfigurieren der Schlüsselverwendung (z. B. Anmelden oder Verschlüsseln)
- Übersachen der Schlüsselverwendung

Dieser Administrator stellt dann Entwicklern URIs bereit, die sie aus ihren Anwendungen aufrufen können. Dieser Administrator stellt auch Protokollierungsinformationen zur Schlüsselverwendung für den Sicherheitsadministrator bereit. 

![Übersicht über die Funktionsweise von Azure Key Vault][1]

Entwickler können die Schlüssel außerdem durch Verwendung von APIs direkt verwalten. Weitere Informationen finden Sie im [Entwicklerhandbuch für den Schlüsseltresor](developers-guide.md).

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über das [Schützen Ihres Tresors](secure-your-key-vault.md).
- Erfahren Sie, wie Sie Ihre [verwalteten HSM-Pools sichern können](../managed-hsm/access-control.md).

<!--Image references-->
[1]: ../media/key-vault-whatis/AzureKeyVault_overview.png
Azure-Tresorschlüssel ist in den meisten Regionen verfügbar. Weitere Informationen finden Sie auf der Seite [Preisübersicht für Schlüsseltresor](https://azure.microsoft.com/pricing/details/key-vault/).