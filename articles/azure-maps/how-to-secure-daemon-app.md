---
title: Schützen einer Daemon-Anwendung
titleSuffix: Azure Maps
description: Verwenden Sie das Azure-Portal zum Verwalten der Authentifizierung, um eine vertrauenswürdige Daemon-Anwendung zu konfigurieren.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 2dd04f404330a6c86e2df09da610e16ba9b721f3
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895646"
---
# <a name="secure-a-daemon-application"></a>Schützen einer Daemon-Anwendung

Die folgende Anleitung gilt für Hintergrundprozesse, Timer und Aufträge, die in einer vertrauenswürdigen und geschützten Umgebung gehostet werden. Beispiele dafür sind Azure Web Jobs, Azure Function-Apps, Windows-Dienste und andere zuverlässige Hintergrunddienste.

> [!Tip]
> Microsoft empfiehlt die Implementierung von Azure Active Directory (Azure AD) und der rollenbasierten Zugriffssteuerung von Azure (Role-Based Access Control, Azure RBAC) für Produktionsanwendungen. Eine Übersicht über die Konzepte finden Sie unter [Azure Maps-Authentifizierung](./azure-maps-authentication.md).

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="scenario-shared-key-authentication"></a>Szenario: Authentifizierung mit gemeinsam verwendetem Schlüssel

Nach dem Erstellen eines Azure Maps-Kontos werden der Primärschlüssel und sekundäre Schlüssel generiert. Es wird empfohlen, den Primärschlüssel als Abonnementschlüssel zu verwenden, wenn Sie [Azure Maps mithilfe der Authentifizierung mit gemeinsam verwendetem Schlüssel aufrufen](./azure-maps-authentication.md#shared-key-authentication). Sie können einen sekundären Schlüssel in Szenarien wie Änderungen beim Schlüsselrollover verwenden. Weitere Informationen finden Sie unter [Authentifizierung in Azure Maps](./azure-maps-authentication.md).

### <a name="securely-store-shared-key"></a>Sicheres Speichern von gemeinsam verwendeten Schlüsseln

Der primäre und der sekundäre Schlüssel ermöglichen die Autorisierung bei allen APIs für das Azure Maps-Konto Anwendungen sollten die Schlüssel in einem sicheren Speicher wie Azure Key Vault speichern. Die Anwendung muss den gemeinsam genutzten Schlüssel als Azure Key Vault-Geheimnis abrufen, um das Speichern des gemeinsam genutzten Schlüssels als Nur-Text in der Anwendungskonfiguration zu vermeiden. Weitere Informationen zum Konfigurieren von Azure Key Vault finden Sie im [Azure Key Vault-Entwicklerhandbuch](../key-vault/general/developers-guide.md).

Dieser Vorgang wird in den folgenden Schritten beschrieben:

1. Erstellen Sie eine Azure Key Vault-Instanz.
2. Erstellen Sie einen Azure AD-Dienstprinzipal, indem Sie eine App-Registrierung oder verwaltete Identität erstellen. Der erstellte Dienstprinzipal ist für den Zugriff auf Azure Key Vault verantwortlich.
3. Weisen Sie dem Dienstprinzipalzugriff auf Azure Key Vault-Geheimnisse die `Get`-Berechtigung zu.
4. Weisen Sie den Zugriff auf die `Set`-Berechtigung für Geheimnisse sich selbst als Entwickler zu.
5. Legen Sie den gemeinsam genutzten Schlüssel in den Key Vault-Geheimnissen fest, referenzieren Sie die Geheimnis-ID als Konfiguration für die Daemon-Anwendung, und entfernen Sie die `Set`-Berechtigung Ihrer Geheimnisse.
6. Implementieren Sie die Azure AD-Authentifizierung in die Daemon-Anwendung, um das Geheimnis für den gemeinsam genutzten Schlüssel aus Azure Key Vault abzurufen.
7. Erstellen Sie die Azure Maps-REST-API-Anforderung mit dem gemeinsam genutzten Schlüssel.

> [!Tip]
> Wenn die App in einer Azure-Umgebung gehostet wird, sollten Sie eine verwaltete Identität implementieren, um die Kosten und die Komplexität der Verwaltung eines Geheimnisses für die Authentifizierung bei Azure Key Vault zu reduzieren. Weitere Informationen hierzu finden Sie im [Azure Key Vault-Tutorial zum Herstellen einer Verbindung über eine verwaltete Identität](../key-vault/general/tutorial-net-create-vault-azure-web-app.md).

Die Daemon-Anwendung ist für das Abrufen des gemeinsam genutzten Schlüssels aus einem sicheren Speicher verantwortlich. Für die Implementierung mit Azure Key Vault ist die Authentifizierung über Azure AD des Zugriffs auf das Geheimnis erforderlich. Aufgrund der zusätzlichen Komplexität und Betriebsanforderungen bei Verwendung der Authentifizierung mit gemeinsam genutzten Schlüsseln wird stattdessen die direkte Azure AD-Authentifizierung bei Azure Maps empfohlen.

> [!IMPORTANT]
> Zur Vereinfachung der Neugenerierung von Schlüsseln wird empfohlen, dass Anwendungen nur einen Schlüssel verwenden. Anwendungen können den nicht verwendeten Schlüssel neu generieren und den neu generierten Schlüssel in einem geschützten Geheimnisspeicher wie Azure Key Vault bereitstellen.

## <a name="scenario-azure-ad-role-based-access-control"></a>Szenario: Rollenbasierte Zugriffssteuerung von Azure AD

Sobald ein Azure Maps-Konto erstellt wird, wird der `x-ms-client-id`-Wert von Azure Maps auf der Authentifizierungsdetailseite des Azure-Portals angezeigt. Dieser Wert stellt das Konto dar, das für REST-API-Anforderungen verwendet wird. Dieser Wert sollte in der Anwendungskonfiguration gespeichert und abgerufen werden, bevor HTTP-Anforderung ausgeführt werden. Das Ziel des Szenarios besteht darin, die Daemon-Anwendung für die Authentifizierung bei Azure AD zu aktivieren und Azure Maps-REST-APIs aufzurufen.

> [!Tip]
> Es wird empfohlen, Azure Virtual Machines, Virtual Machine Scale Sets oder App Services als Host zu verwenden, damit Sie die Vorteile der Komponenten der verwalteten Identität nutzen können.

### <a name="daemon-hosted-on-azure-resources"></a>Daemon auf anderen Azure-Ressourcen als Hosts

Wenn die Daemon-Anwendung auf Azure-Ressourcen ausgeführt wird, können Sie verwaltete Azure-Identitäten konfigurieren, um von niedrigen Kosten und verringertem Verwaltungsaufwand für Anmeldeinformationen zu profitieren. 

Informationen zum Aktivieren des Anwendungszugriffs auf eine verwaltete Identität finden Sie in der [Übersicht über verwaltete Identitäten](../active-directory/managed-identities-azure-resources/overview.md).

Vorteile der verwalteten Identität:

* Kryptografieauthentifizierung mit einem öffentlichen Schlüssel des vom Azure-System verwalteten X509-Zertifikats
* Azure AD-Sicherheit mit X509-Zertifikaten anstelle von geheimen Clientschlüsseln
* Azure verwaltet und erneuert alle Zertifikate, die der verwalteten Identitätsressource zugeordnet sind.
* Vereinfachte Verwaltung für Anmeldeinformationen, da keine geschützten Geheimnisspeicherdienste wie Azure Key Vault erforderlich sind 

### <a name="daemon-hosted-on-non-azure-resources"></a>Daemon auf anderen nicht von Azure stammenden Ressourcen als Hosts

Bei der Ausführung in einer nicht von Azure stammenden Umgebung sind verwaltete Identitäten nicht verfügbar. Daher müssen Sie einen Dienstprinzipal über eine Azure AD-Anwendungsregistrierung für die Daemon-Anwendung konfigurieren.

1. Wählen Sie im Azure-Portal in der Liste der Azure-Dienste die Optionen **Azure Active Directory** > **App-Registrierungen** > **Neue Registrierung** aus.  

    > [!div class="mx-imgBorder"]
    > ![App-Registrierung](./media/how-to-manage-authentication/app-registration.png)

2. Wenn Sie die App bereits registriert haben, fahren Sie mit dem nächsten Schritt fort. Wenn Sie die App noch nicht registriert haben, geben Sie einen **Namen** ein, und wählen Sie einen **unterstützten Kontotyp** und dann **Registrieren** aus.  

    > [!div class="mx-imgBorder"]
    > ![App-Registrierungsdetails](./media/how-to-manage-authentication/app-create.png)

3. Navigieren Sie zu der Anwendung, um Azure Maps die delegierten API-Berechtigungen zuzuweisen. Wählen Sie dann unter **App-Registrierungen** die Optionen **API-Berechtigungen** > **Berechtigung hinzufügen** aus. Suchen Sie unter **Von meiner Organisation verwendete APIs** die Option **Azure Maps** , und wählen Sie sie aus.

    > [!div class="mx-imgBorder"]
    > ![Hinzufügen von App-API-Berechtigungen](./media/how-to-manage-authentication/app-permissions.png)

4. Aktivieren Sie das Kontrollkästchen neben **Auf Azure Maps zugreifen** , und wählen Sie dann **Berechtigungen hinzufügen** aus.

    > [!div class="mx-imgBorder"]
    > ![Auswählen von App-API-Berechtigungen](./media/how-to-manage-authentication/select-app-permissions.png)

5. Führen Sie die folgenden Schritte aus, um einen geheimen Clientschlüssel zu erstellen oder ein Zertifikat zu konfigurieren.

    * Wenn in der Anwendung die Server- oder Anwendungsauthentifizierung verwendet wird, navigieren Sie auf der Registrierungsseite für die App zu **Zertifikate & Geheimnisse** . Laden Sie dann entweder ein öffentliches Schlüsselzertifikat hoch, oder erstellen Sie ein Kennwort, indem Sie **Neuer geheimer Clientschlüssel** auswählen.

        > [!div class="mx-imgBorder"]
        > ![Erstellen eines geheimen Clientschlüssels](./media/how-to-manage-authentication/app-keys.png)

    * Nachdem Sie auf **Hinzufügen** geklickt haben, kopieren Sie das Geheimnis, und speichern Sie es sicher in einem Dienst wie Azure Key Vault. Anweisungen zum sicheren Speichern eines Zertifikats oder Geheimnisses finden Sie im [Azure Key Vault-Entwicklerhandbuch](../key-vault/general/developers-guide.md). Dieses Geheimnis verwenden Sie zum Abrufen von Tokens aus Azure AD.

        > [!div class="mx-imgBorder"]
        > ![Hinzufügen eines geheimen Clientschlüssels](./media/how-to-manage-authentication/add-key.png)

### <a name="grant-role-based-access-for-the-daemon-application-to-azure-maps"></a>Gewähren des rollenbasierten Zugriffs der Daemonanwendung auf Azure Maps

Sie gewähren die *rollenbasierte Zugriffssteuerung von Azure (Azure RBAC)* , indem Sie entweder der verwalteten Identität oder dem Dienstprinzipal mindestens eine Azure Maps-Rollendefinition zuweisen. Informationen über die für Azure Maps verfügbaren Rollendefinitionen für die rollenbasierte Zugriffssteuerung von Azure finden Sie unter **Zugriffssteuerung (IAM)** . Wählen Sie **Rollen** aus, und suchen Sie dann nach Rollen, die mit *Azure Maps* beginnen. Hierbei handelt es sich um die Azure Maps-Rollen, denen Sie den Zugriff gewähren können.

> [!div class="mx-imgBorder"]
> ![Anzeigen verfügbarer Rollen](./media/how-to-manage-authentication/how-to-view-avail-roles.png)

1. Navigieren Sie zu Ihrem **Azure Maps-Konto** . Wählen Sie **Zugriffssteuerung (IAM)**  > **Rollenzuweisungen** aus.

    > [!div class="mx-imgBorder"]
    > ![Gewähren von Zugriff mithilfe von Azure RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. Klicken Sie auf der Registerkarte **Rollenzuweisungen** auf **Hinzufügen** , um eine Rollenzuweisung hinzuzufügen. 

    > [!div class="mx-imgBorder"]
    > ![Screenshot: Rollenzuweisungen mit ausgewählter Option „Hinzufügen“](./media/how-to-manage-authentication/add-role-assignment.png)

3. Wählen Sie eine integrierte Azure Maps-Rollendefinition wie **Azure Maps-Datenleser** oder **Azure Maps-Datenmitwirkender** aus. Wählen Sie unter **Zugriff zuweisen zu** die Option **Azure AD-Benutzer, -Gruppe oder -Dienstprinzipal** oder verwaltete Identität mit **benutzerseitig zugewiesene verwaltete Identität** / **systemseitig zugewiesene verwaltete Identität** aus. Wählen Sie den Prinzipal aus. Klicken Sie dann auf **Speichern** .

    > [!div class="mx-imgBorder"]
    > ![Hinzufügen einer Rollenzuweisung](./media/how-to-manage-authentication/how-to-add-role-assignment.png)

4. Sie können auf der Registerkarte „Rollenzuweisungen“ überprüfen, ob die Rollenzuweisung angewendet wurde.

## <a name="request-token-with-managed-identity"></a>Anfordern des Tokens mit einer verwalteten Identität

Sobald eine verwaltete Identität für die Hostressource konfiguriert ist, können Sie das Azure SDK oder die REST-API zum Abrufen eines Tokens für Azure Maps verwenden. Weitere Informationen hierzu finden Sie unter [Abrufen eines Zugriffstokens](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md). Im Rahmen der Anleitung wird erwartet, dass ein Zugriffstoken zurückgegeben wird, das für REST-API-Anforderungen verwendet werden kann.

## <a name="request-token-with-application-registration"></a>Anforderungstoken mit Anwendungsregistrierung

Nachdem Sie Ihre App registriert und mit Azure Maps verknüpft haben, können Sie Zugriffstoken anfordern.

* Azure AD-Ressourcen-ID `https://atlas.microsoft.com/`
* Azure AD-App-ID
* Azure AD-Mandanten-ID
* Clientgeheimnis für die Azure AD-App-Registrierung

Anforderung:

```http
POST /<Azure AD Tenant ID>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=<Azure AD App ID>&resource=https://atlas.microsoft.com/&client_secret=<client secret>&grant_type=client_credentials
```

Antwort:

```json
{
    "token_type": "Bearer",
    "expires_in": "...",
    "ext_expires_in": "...",
    "expires_on": "...",
    "not_before": "...",
    "resource": "https://atlas.microsoft.com/",
    "access_token": "ey...gw"
}
```

Ausführlichere Beispiele finden Sie unter [Authentifizierungsszenarios für Azure AD](../active-directory/develop/authentication-vs-authorization.md).

## <a name="next-steps"></a>Nächste Schritte

Suchen der API-Nutzungsmetriken für Ihr Azure Maps-Konto:
> [!div class="nextstepaction"]
> [Anzeigen von Nutzungsmetriken](how-to-view-api-usage.md)

Beispiele für die Integration von Azure AD in Azure Maps:
> [!div class="nextstepaction"]
> [Azure Maps-Beispiele](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)