---
title: Grundlagen der Azure Key Vault-Authentifizierung
description: Erfahren Sie, wie das Authentifizierungsmodell von Key Vault funktioniert.
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 09/25/2020
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: 6de0fc52ae265a47ca7f52d46e5f44b74c1277aa
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96530917"
---
# <a name="key-vault-authentication-fundamentals"></a>Grundlagen der Key Vault-Authentifizierung

Mit Azure Key Vault können Sie die Anmeldeinformationen von Anwendungen, z. B. Geheimnisse, Schlüssel und Zertifikate, in einem zentralen und sicheren Cloudrepository speichern und verwalten. Mit Key Vault entfällt die Notwendigkeit, Anmeldeinformationen in Ihren Anwendungen zu speichern. Ihre Anwendungen können sich zur Laufzeit bei Key Vault authentifizieren, um die Anmeldeinformationen abzurufen.

Als Administrator können Sie genau steuern, welche Benutzer und Anwendungen auf Ihren Schlüsseltresor zugreifen dürfen, und Sie können die von ihnen ausgeführten Vorgänge einschränken und überwachen. In diesem Dokument werden die grundlegenden Konzepte des Zugriffsmodells von Key Vault erläutert. Es enthält das erforderliche Basiswissen und zeigt, wie Sie einen Benutzer oder eine Anwendung im Schlüsseltresor vom Anfang bis zum Ende authentifizieren können.

## <a name="required-knowledge"></a>Erforderliche Kenntnisse

In diesem Dokument wird vorausgesetzt, dass Sie mit folgenden Konzepten vertraut sind. Wenn Sie sich mit diesen Konzepten nicht auskennen, nutzen Sie die Hilfelinks, bevor Sie fortfahren.

* [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)
* [Sicherheitsprinzipale](./authentication.md#app-identity-and-security-principals)

## <a name="key-vault-configuration-steps-summary"></a>Zusammenfassung der Schritte zur Key Vault-Konfiguration

1. Registrieren Sie den Benutzer oder die Anwendung als Sicherheitsprinzipal in Azure Active Directory.
1. Konfigurieren Sie in Azure Active Directory eine Rollenzuweisung für den Sicherheitsprinzipal.
1. Konfigurieren Sie Richtlinien für den Zugriff auf den Schlüsseltresor für Ihren Sicherheitsprinzipal.
1. Konfigurieren Sie in der Key Vault-Firewall den Zugriff auf Ihren Schlüsseltresor (optional).
1. Testen Sie, ob Ihr Sicherheitsprinzipal auf Key Vault zugreifen kann.

## <a name="register-a-user-or-application-in-azure-active-directory-as-a-security-principal"></a>Registrieren eines Benutzers oder einer Anwendung als Sicherheitsprinzipal in Azure Active Directory

Wenn ein Benutzer oder eine Anwendung eine Anforderung an den Schlüsseltresor übermittelt, muss diese Anforderung zunächst von Azure Active Directory authentifiziert werden. Damit dies möglich ist, muss der Benutzer oder die Anwendung in Azure Active Directory als Sicherheitsprinzipal registriert sein.

Unter den nachfolgenden Dokumentationslinks erfahren Sie, wie Sie einen Benutzer oder eine Anwendung in Azure Active Directory registrieren.
**Erstellen Sie unbedingt ein Kennwort für die Benutzerregistrierung und für Anwendungen ein Clientgeheimnis oder ein Clientzertifikat mit den Anmeldeinformationen.**

* [Registrieren eines Benutzers in Azure Active Directory](../../active-directory/fundamentals/add-users-azure-active-directory.md)
* [Registrieren einer Anwendung in Azure Active Directory](../../active-directory/develop/quickstart-register-app.md)

## <a name="assign-your-security-principal-a-role"></a>Zuweisen einer Rolle zum Sicherheitsprinzipal

Sie können die rollenbasierte Zugriffssteuerung in Azure (Role-Based Access Control, Azure RBAC) verwenden, um Sicherheitsprinzipalen Berechtigungen zuzuweisen. Diese Berechtigungen werden als Rollenzuweisungen bezeichnet.

Bei Key Vault legen diese Rollenzuweisungen die Zugriffsebene eines Sicherheitsprinzipals auf die Verwaltungsebene (auch als Steuerungsebene bezeichnet) des Schlüsseltresors fest. Diese Rollenzuweisungen bieten keinen direkten Zugriff auf die Geheimnisse der Datenebene, aber der gewährte Zugriff ermöglicht das Verwalten von Eigenschaften des Schlüsseltresors. Beispielsweise kann ein Benutzer oder eine Anwendung mit der zugewiesenen Rolle **Leser** keine Änderungen an den Firewalleinstellungen des Schlüsseltresors vornehmen. Dafür benötigen Benutzer oder Anwendungen die Rolle **Mitwirkender**. Keine dieser Rollen hat jedoch direkten Zugriff für das Durchführen von Vorgängen mit Geheimnissen, Schlüsseln oder Zertifikaten, z. B. zum Erstellen oder Abrufen ihres Werts, bis ihnen der Zugriff auf die Datenebene des Schlüsseltresors zugewiesen wird. Dies wird im nächsten Schritt behandelt.

>[!IMPORTANT]
> Obwohl Benutzer mit der Rolle „Mitwirkender“ oder „Besitzer“ standardmäßig keinen Zugriff haben, mit dem sie Vorgänge mit im Schlüsseltresor gespeicherten Geheimnissen durchführen können, umfassen diese Rollen Berechtigungen zum Hinzufügen oder Entfernen von Zugriffsrichtlinien für Geheimnisse im Schlüsseltresor. Daher kann ein Benutzer mit diesen Rollenzuweisungen sich selbst Zugriff auf Geheimnisse im Schlüsseltresor gewähren. Aus diesem Grund sollten ausschließlich Administratoren Zugriff auf die Rollen „Mitwirkender“ oder „Besitzer“ erhalten. Benutzern und Anwendungen, die nur Geheimnisse aus dem Schlüsseltresor abrufen müssen, sollte die Rolle „Leser“ zugewiesen werden. **Weitere Informationen finden Sie im nächsten Abschnitt.**

>[!NOTE]
> Wenn Sie einem Benutzer eine Rollenzuweisung auf Mandantenebene von Azure Active Directory zuweisen, werden die zugehörigen Berechtigungen auf alle Abonnements, Ressourcengruppen und Ressourcen im Bereich der Zuweisung übertragen. Um das Prinzip minimaler Berechtigung einzuhalten, können Sie diese Rollenzuweisung auf einen differenzierteren Bereich anwenden. Beispielsweise können Sie einem Benutzer die Rolle „Leser“ auf Abonnementebene und die Rolle „Besitzer“ für einen spezifischen Schlüsseltresor zuweisen. Wenn Sie präzisere Rollenzuweisungen vornehmen möchten, wechseln Sie zu den IAM-Einstellungen (Identity & Access Management) des Abonnements, der Ressourcengruppe oder des Schlüsseltresors.

* Weitere Informationen zu Azure-Rollen [Link](../../role-based-access-control/built-in-roles.md)
* [Weitere Informationen zum Zuweisen oder Aufheben von Rollenzuweisungen](../../role-based-access-control/role-assignments-portal.md)

## <a name="configure-key-vault-access-policies-for-your-security-principal"></a>Konfigurieren von Zugriffsrichtlinien auf den Schlüsseltresor für einen Sicherheitsprinzipal

Bevor Sie Benutzern und Anwendungen Zugriff auf einen Schlüsseltresor gewähren, ist es wichtig, die verschiedenen Arten von Vorgängen zu verstehen, die mit einem Schlüsseltresor ausgeführt werden können. Es gibt zwei Hauptkategorien von Schlüsseltresorvorgängen: Vorgänge auf Verwaltungsebene (auch als Steuerungsebene bezeichnet) und Vorgänge auf Datenebene.

In der folgenden Tabelle finden Sie mehrere Beispiele für die verschiedenen Vorgänge, die auf Verwaltungs- bzw. Datenebene gesteuert werden. Vorgänge, mit denen Eigenschaften des Schlüsseltresors geändert werden, erfolgen auf Verwaltungsebene. Vorgänge, mit denen der Wert von im Schlüsseltresor gespeicherten Geheimnissen geändert oder abgerufen wird, erfolgen auf Datenebene.

|Vorgänge auf Verwaltungsebene (Beispiele)|Vorgänge auf Datenebene (Beispiele)|
| --- | --- |
| Erstellen eines Schlüsseltresors | Erstellen von Schlüsseln, Geheimnissen, Zertifikaten
| Löschen von Schlüsseltresoren | Löschen von Schlüsseln, Geheimnissen, Zertifikaten
| Hinzufügen oder Entfernen von Rollenzuweisungen für den Schlüsseltresor | Auflisten und Abrufen der Werte von Schlüsseln, Geheimnissen, Zertifikaten
| Hinzufügen oder Entfernen von Zugriffsrichtlinien für den Schlüsseltresor | Sichern und Wiederherstellen von Schlüsseln, Geheimnissen, Zertifikaten
| Ändern von Key Vault-Firewalleinstellungen | Erneuern von Schlüsseln, Geheimnissen, Zertifikaten
| Ändern von Key Vault-Wiederherstellungseinstellungen | Bereinigen oder Wiederherstellen von vorläufig gelöschten Schlüsseln, Geheimnissen, Zertifikaten
| Ändern von Einstellungen der Key Vault-Diagnoseprotokolle

### <a name="management-plane-access--azure-active-directory-role-assignments"></a>Zugriff auf Verwaltungsebene und Azure Active Directory-Rollenzuweisungen

Mit Azure Active Directory-Rollenzuweisungen wird der erforderliche Zugriff für Vorgänge auf Verwaltungsebene eines Schlüsseltresors gewährt. Dieser Zugriff wird in der Regel Benutzern und nicht Anwendungen gewährt. Die Einschränkung der Vorgänge auf Verwaltungsebene, die ein Benutzer durchführen darf, nehmen Sie über die Rollenzuweisung des Benutzers vor.

Wenn Sie einem Benutzer beispielsweise die Key Vault-Rolle „Leser“ zuweisen, darf dieser zwar die Eigenschaften Ihres Schlüsseltresors (z. B. die Zugriffsrichtlinien) anzeigen, aber keine Änderungen vornehmen. Wird einem Benutzer die Rolle „Besitzer“ zugewiesen, erhält er damit Vollzugriff zum Ändern der Einstellungen auf Verwaltungsebene des Schlüsseltresors.

Rollenzuweisungen werden auf dem Blatt „Zugriffssteuerung (IAM)“ festgelegt. Wenn Sie einem bestimmten Benutzer den Zugriff als „Leser“ oder „Administrator“ mehrerer Schlüsseltresorressourcen gewähren möchten, können Sie eine Rollenzuweisung auf Tresor-, Ressourcengruppen- oder Abonnementebene erstellen. Diese Rollenzuweisung wird dann allen Ressourcen innerhalb des zugewiesenen Bereichs hinzugefügt.

Der Zugriff auf Datenebene oder zum Ausführen von Vorgängen mit Schlüsseln, Geheimnissen und Zertifikaten, die im Schlüsseltresor gespeichert sind, kann auf zwei Arten hinzugefügt werden.

### <a name="data-plane-access-option-1-classic-key-vault-access-policies"></a>Zugriff auf Datenebene – Option 1: Klassische Key Vault-Zugriffsrichtlinien

Key Vault-Zugriffsrichtlinien gewähren Benutzern und Anwendungen den erforderlichen Zugriff für das Ausführen von Vorgängen auf der Datenebene eines Schlüsseltresors.

> [!NOTE]
> Dieses Zugriffsmodell ist nicht mit der unter „Option 2“ beschriebenen rollenbasierten Zugriffssteuerung in Azure (Azure RBAC) für Key Vault kompatibel. Sie müssen sich für eine Option entscheiden. Sie treffen diese Auswahl, indem Sie in Ihrem Schlüsseltresor auf die Registerkarte „Zugriffsrichtlinie“ klicken.

Klassische Zugriffsrichtlinien sind sehr präzise. Sie können einzelnen Benutzern oder Anwendungen ganz spezifische Vorgänge innerhalb eines Schlüsseltresors erlauben oder verweigern. Hier sind einige Beispiele:

* Sicherheitsprinzipal 1 kann sämtliche Vorgänge mit Schlüsseln ausführen, aber keine mit Geheimnissen oder Zertifikaten.
* Sicherheitsprinzipal 2 kann alle Schlüssel, Geheimnisse und Zertifikate auflisten und lesen, aber keine Vorgänge zum Erstellen, Löschen oder Erneuern ausführen.
* Sicherheitsprinzipal 3 kann alle Geheimnisse sichern und wiederherstellen, aber den Wert der Geheimnisse selbst nicht lesen.

Allerdings lassen klassische Zugriffsrichtlinien keine Berechtigungen auf Objektebene zu, und die zugewiesenen Berechtigungen gelten für den Bereich eines einzelnen Schlüsseltresors. Wenn Sie z. B. über eine Zugriffsrichtlinie einem Sicherheitsprinzipal in einem bestimmten Schlüsseltresor die Berechtigung „Geheimnis abrufen“ gewähren, kann der Sicherheitsprinzipal alle Geheimnisse in diesem Schlüsseltresor abrufen. Diese Berechtigung „Geheimnis abrufen“ wird jedoch nicht automatisch auf andere Schlüsseltresore ausgeweitet und muss explizit zugewiesen werden.

> [!IMPORTANT]
> Klassische Key Vault-Zugriffsrichtlinien und Azure Active Directory-Rollenzuweisungen sind voneinander unabhängig. Wenn Sie einem Sicherheitsprinzipal die Rolle „Mitwirkender“ auf Abonnementebene zuweisen, hat er nicht automatisch die Berechtigung, Vorgänge auf Datenebene für jeden Schlüsseltresor innerhalb des Bereichs des Abonnements auszuführen. Dem Sicherheitsprinzipal müssen weiterhin (eventuell auch durch sich selbst) Berechtigungen über Zugriffsrichtlinien gewährt werden, damit er Vorgänge auf Datenebene ausführen kann.

### <a name="data-plane-access-option-2--azure-rbac-for-key-vault-preview"></a>Zugriff auf Datenebene – Option 2:  Azure RBAC für Key Vault (Vorschauversion)

Eine neue Möglichkeit zum Gewähren des Zugriffs auf Datenebene für einen Schlüsseltresor stellt die rollenbasierte Zugriffssteuerung in Azure (Role-Based Access Control, Azure RBAC) für Key Vault dar.

> [!NOTE]
> Dieses Zugriffsmodell ist nicht mit den oben beschriebenen klassischen Zugriffsrichtlinien für Key Vault kompatibel. Sie müssen sich für eine Option entscheiden. Sie treffen diese Auswahl, indem Sie in Ihrem Schlüsseltresor auf die Registerkarte „Zugriffsrichtlinie“ klicken.

Key Vault-Rollenzuweisungen sind verschiedene in Azure integrierte Rollen mit allgemeinen Berechtigungen für den Zugriff auf Schlüssel, Geheimnisse oder Zertifikate. Dieses Berechtigungsmodell bietet darüber hinaus zusätzliche Funktionen, die beim klassischen Key Vault-Modell mit Zugriffsrichtlinien nicht verfügbar sind.

* Azure RBAC-Berechtigungen können im großen Stil verwaltet werden, da diese Rollen Benutzern auf der Ebene eines einzelnen Abonnements, einer Ressourcengruppe oder eines Schlüsseltresors zugewiesen werden können. Ein Benutzer erhält die Berechtigungen auf Datenebene für alle Schlüsseltresore innerhalb des Bereichs der Azure RBAC-Zuweisung. Dadurch entfällt die Notwendigkeit, Zugriffsrichtlinienberechtigungen einzeln speziellen Benutzern oder Anwendungen für jeweils einen Schlüsseltresor zuzuweisen.

* Azure RBAC-Berechtigungen sind mit Privileged Identity Management (PIM) kompatibel. Auf diese Weise können Sie Just-In-Time-Zugriffssteuerungen für privilegierte Rollen wie „Key Vault-Administrator“ konfigurieren. Dies ist eine bewährte Sicherheitsmaßnahme, die dem Prinzip minimaler Berechtigungen folgt, da kein dauerhafter Zugriff auf Ihre Schlüsseltresore gewährt wird.

Weitere Informationen zu Azure RBAC für Key Vault finden Sie in den folgenden Dokumenten:

* Azure RBAC für Key Vault [Link](./secure-your-key-vault.md#management-plane-and-azure-rbac)
* Azure RBAC-Rollen für Key Vault (Vorschauversion) [Link](../../role-based-access-control/built-in-roles.md#key-vault-administrator-preview)

## <a name="configure-key-vault-firewall"></a>Konfigurieren der Key Vault-Firewall

Standardmäßig erlaubt Key Vault das Senden von Datenverkehr aus dem öffentlichen Internet an Ihren Schlüsseltresor über einen öffentlichen Endpunkt. Als zusätzliche Sicherheitsebene können Sie die Azure Key Vault-Firewall so konfigurieren, dass der Zugriff auf den öffentlichen Endpunkt des Schlüsseltresors beschränkt wird.

Zum Aktivieren der Key Vault-Firewall klicken Sie im Portal des Schlüsseltresors auf die Registerkarte „Netzwerk“ und aktivieren das Optionsfeld, mit dem der folgende Zugriff zugelassen wird: „Privater Endpunkt und ausgewählte Netzwerke“. Wenn Sie die Key Vault-Firewall aktivieren, können Sie Datenverkehr durch die Key Vault-Firewall über folgende Maßnahmen zulassen:

* Fügen Sie der Positivliste der Key Vault-Firewall IPv4-Adressen hinzu. Diese Option funktioniert am besten bei Anwendungen mit statischen IP-Adressen.

* Fügen Sie der Key Vault-Firewall ein virtuelles Netzwerk hinzu. Diese Option funktioniert am besten bei Azure-Ressourcen mit dynamischen IP-Adressen, z. B. virtuellen Computern. Sie können Azure-Ressourcen einem virtuellen Netzwerk hinzufügen und das virtuelle Netzwerk dann der Positivliste der Key Vault-Firewall hinzufügen. Bei dieser Option wird ein Dienstendpunkt verwendet, eine private IP-Adresse im virtuellen Netzwerk. Dies bietet eine zusätzliche Sicherheitsebene, da kein Datenverkehr zwischen dem Schlüsseltresor und Ihrem virtuellen Netzwerk über das öffentliche Internet weitergeleitet wird. Weitere Informationen zu Dienstendpunkten finden Sie in dieser Dokumentation: [Link](./network-security.md).

* Fügen Sie eine private Verbindung mit dem Schlüsseltresor hinzu. Bei dieser Option wird das virtuelle Netzwerk direkt mit einer bestimmten Instanz von Key Vault verbunden, sodass Ihr Schlüsseltresor in Ihr virtuelles Netzwerk eingebunden wird. Weitere Informationen zum Konfigurieren einer Verbindung zwischen einem privaten Endpunkt und einem Schlüsseltresor finden Sie unter folgendem [Link](./private-link-service.md).

## <a name="test-your-service-principals-ability-to-access-key-vault"></a>Testen des Zugriffs Ihres Sicherheitsprinzipals auf den Schlüsseltresor

Nachdem Sie alle oben beschriebenen Schritte ausgeführt haben, können Sie Geheimnisse in Ihrem Schlüsseltresor festlegen und abrufen.

### <a name="authentication-process-for-users-examples"></a>Authentifizierungsvorgang für Benutzer (Beispiele)

* Benutzer den Schlüsseltresor verwenden, indem sie sich beim Azure-Portal anmelden. [Schnellstart für das Key Vault-Portal](./quick-create-portal.md)

* Benutzer können den Schlüsseltresor über die Azure-Befehlszeilenschnittstelle verwenden. [Schnellstart für die Verwendung der Azure-Befehlszeilenschnittstelle mit Key Vault](./quick-create-cli.md)

* Benutzer können den Schlüsseltresor über Azure PowerShell verwenden. [Schnellstart für die Verwendung von Azure PowerShell mit Key Vault](./quick-create-powershell.md)

### <a name="azure-active-directory-authentication-process-for-applications-or-services-examples"></a>Azure Active Directory-Authentifizierungsprozess für Anwendungen oder Dienste (Beispiele)

* Eine Anwendung stellt in einer Funktion einen geheimen Clientschlüssel und eine Client-ID bereit, um ein Azure Active Directory-Token zu erhalten. 

* Eine Anwendung stellt ein Zertifikat bereit, um ein Azure Active Directory-Token zu erhalten. 

* Eine Azure-Ressource verwendet die MSI-Authentifizierung, um ein Azure Active Directory-Token zu erhalten. 

* [Weitere Informationen zur MSI-Authentifizierung](../../active-directory/managed-identities-azure-resources/overview.md)

### <a name="authentication-process-for-application-python-example"></a>Authentifizierungsvorgang für die Anwendungen (Python-Beispiel)

Verwenden Sie das folgende Codebeispiel, um zu testen, ob Ihre Anwendung mit dem von Ihnen konfigurierten Dienstprinzipal ein Geheimnis aus Ihrem Schlüsseltresor abrufen kann.

>[!NOTE]
>Dieses Beispiel dient nur zu Demonstrations- und Testzwecken. **VERWENDEN SIE DIE AUTHENTIFIZIERUNG MIT GEHEIMEN CLIENTSCHLÜSSELN NICHT IN PRODUKTIONSUMGEBUNGEN**, da dies keine sichere Entwurfspraxis darstellt. Sie sollten ein Clientzertifikat oder die MSI-Authentifizierung als bewährte Methode nutzen.

```python
from azure.identity import ClientSecretCredential
from azure.keyvault.secrets import SecretClient

tenant_id = "{ENTER YOUR TENANT ID HERE}"                          ##ENTER AZURE TENANT ID
vault_url = "https://{ENTER YOUR VAULT NAME}.vault.azure.net/"     ##ENTER THE URL OF YOUR KEY VAULT
client_id = "{ENTER YOUR CLIENT ID HERE}"                          ##ENTER THE CLIENT ID OF YOUR SERVICE PRINCIPAL
cert_path = "{ENTER YOUR CLIENT SECRET HERE}"                      ##ENTER THE CLIENT SECRET OF YOUR SERVICE PRINCIPAL

def main():

    #AUTHENTICATION TO Azure Active Directory USING CLIENT ID AND CLIENT CERTIFICATE (GET Azure Active Directory TOKEN)
    token = ClientSecretCredential(tenant_id=tenant_id, client_id=client_id, client_secret=client_secret)

    #AUTHENTICATION TO KEY VAULT PRESENTING Azure Active Directory TOKEN
    client = SecretClient(vault_url=vault_url, credential=token)

    #CALL TO KEY VAULT TO GET SECRET
    #ENTER NAME OF A SECRET STORED IN KEY VAULT
    secret = client.get_secret('{SECRET_NAME}')

    #GET PLAINTEXT OF SECRET
    print(secret.value)

#CALL MAIN()
if __name__ == "__main__":
    main()
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Key Vault-Authentifizierung finden Sie im folgenden Dokument: [Authentifizieren bei Azure Key Vault](./authentication.md)
