---
title: Implementieren der Notfallwiederherstellung mit Sichern und Wiederherstellen in API Management
titleSuffix: Azure API Management
description: Erfahren Sie, wie Sie Sichern und Wiederherstellen zur Notfallwiederherstellung in Azure API Management verwenden.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/05/2020
ms.author: apimpm
ms.openlocfilehash: 25356e7101293fc27d4107b3a618cfc481aee969
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96779582"
---
# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>So implementieren Sie die Notfallwiederherstellung mit Sichern und Wiederherstellen von Diensten in Azure API Management

Indem Sie Ihre APIs über Azure API Management veröffentlichen und verwalten, profitieren Sie von Fehlertoleranz- und Infrastrukturfunktionen, die Sie sonst erst manuell entwickeln, implementieren und verwalten müssten. Die Azure-Plattform minimiert einen Großteil der potenziellen Ausfälle zu einem Bruchteil der Kosten.

Bei Verfügbarkeitsproblemen in der Region, in der Ihr API Management-Dienst gehostet wird, sollten Sie jederzeit dazu in der Lage sein, Ihren Dienst in einer anderen Region wiederherzustellen. Abhängig von Ihrer RTO (Recovery Time Objective) empfiehlt es sich gegebenenfalls, einen Standbydienst in mindestens einer Region bereitzuhalten. Sie haben auch die Möglichkeit, die Konfiguration und Inhalte der Regionen gemäß Ihrer RPO (Recovery Point Objective) mit dem aktiven Dienst zu synchronisieren. Die Features zur Sicherung und Wiederherstellung von Diensten bieten die notwendigen Bausteine für die Implementierung einer Notfallwiederherstellungsstrategie.

Sicherungs- und Wiederherstellungsvorgänge können auch zur Replikation der API Management-Dienstkonfiguration zwischen Betriebsumgebungen, z. B. Entwicklung und Staging, verwendet werden. Beachten Sie, dass auch Laufzeitdaten wie Benutzer und Abonnements kopiert werden, was unter Umständen nicht immer wünschenswert ist.

Diese Anleitung zeigt, wie Sie Sicherungs- und Wiederherstellungsvorgänge automatisieren und wie Sie eine erfolgreiche Authentifizierung von Sicherungs- und Wiederherstellungsanforderungen durch Azure Resource Manager sicherstellen.

> [!IMPORTANT]
> Die benutzerdefinierte Hostnamenkonfiguration des Zieldiensts wird durch den Wiederherstellungsvorgang nicht geändert. Wir empfehlen, den gleichen benutzerdefinierten Hostnamen und das gleiche TLS-Zertifikat sowohl für aktive Dienste als auch für Standbydienste zu verwenden, sodass der Datenverkehr nach Abschluss der Wiederherstellung durch eine einfache DNS-CNAME-Änderung an die Standbyinstanz umgeleitet werden kann.
>
> Der Sicherungsvorgang erfasst keine voraggregierten Protokolldaten, die in Berichten auf dem Analytics-Blatt im Azure-Portal verwendet werden.

> [!WARNING]
> Jede Sicherung läuft nach 30 Tagen ab. Wenn Sie versuchen, eine Sicherung nach dem Ablaufzeitraum von 30 Tagen wiederherzustellen, erhalten Sie die Fehlermeldung `Cannot restore: backup expired`.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="authenticating-azure-resource-manager-requests"></a>Authentifizieren von Anforderungen des Azure-Ressourcen-Managers

> [!IMPORTANT]
> Die REST-API für die Sicherung und Wiederherstellung verwendet den Azure-Ressourcen-Manager und weist eine andere Authentifizierungsmethode als die REST-APIs für die Verwaltung von API Management-Entitäten auf. In den Schritten in diesem Abschnitt wird beschrieben, wie Anforderungen des Azure-Ressourcen-Managers authentifiziert werden. Weitere Informationen finden Sie unter [Authentifizieren von Anforderungen des Azure-Ressourcen-Managers](/rest/api/index).

Alle Aufgaben, die Sie mithilfe von Azure Resource Manager für Ressourcen ausführen, müssen entsprechend den folgenden Schritten mit Azure Active Directory authentifiziert werden:

-   Fügen Sie dem Azure Active Directory-Mandanten eine Anwendung hinzu.
-   Legen Sie Berechtigungen für die hinzugefügte Anwendung fest.
-   Rufen Sie das Token zum Authentifizieren von Anforderungen an den Azure-Ressourcen-Manager ab.

### <a name="create-an-azure-active-directory-application"></a>Erstellen einer Azure Active Directory-Anwendung

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Verwenden Sie das Abonnement, das Ihre API Management-Dienstinstanz enthält, und navigieren Sie zur Registerkarte **App-Registrierungen** in **Azure Active Directory** (Azure Active Directory > Verwalten > App-Registrierungen).

    > [!NOTE]
    > Wenn das Azure Active Directory-Standardverzeichnis in Ihrem Konto nicht angezeigt wird, bitten Sie den Administrator des Azure-Abonnements, die erforderlichen Berechtigungen für das Konto zu erteilen.

3. Klicken Sie auf **Registrierung einer neuen Anwendung**.

    Das Fenster **Erstellen** wird auf der rechten Seite angezeigt. Dort geben Sie die relevanten Informationen der AAD-App ein.

4. Geben Sie einen Namen für die Anwendung ein.
5. Wählen Sie als Anwendungstyp **Nativ** aus.
6. Geben Sie eine Platzhalter-URL wie z.B. `http://resources` für den **Umleitungs-URI** ein, da es sich um ein Pflichtfeld handelt. Der Wert wird jedoch später nicht verwendet. Klicken Sie auf das Kontrollkästchen, um die Anwendung zu speichern.
7. Klicken Sie auf **Erstellen**.

### <a name="add-an-application"></a>Hinzufügen einer Anwendung

1. Klicken Sie nach dem Erstellen der Anwendung auf **API-Berechtigungen**.
2. Klicken Sie auf **+ Add a permission** (+ Berechtigung hinzufügen).
4. Klicken Sie auf **Select Microsoft APIs** (Microsoft-APIs auswählen).
5. Klicken Sie auf **Azure Service Management** (Azure-Dienstverwaltung).
6. Drücken Sie **Auswählen**.

    ![Hinzufügen von Berechtigungen](./media/api-management-howto-disaster-recovery-backup-restore/add-app.png)

7. Klicken Sie neben der neu hinzugefügten Anwendung auf **Delegierte Berechtigungen**, und aktivieren Sie das Kontrollkästchen **Access Azure Service Management (Vorschau)** .
8. Drücken Sie **Auswählen**.
9. Klicken Sie auf **Berechtigungen erteilen**.

### <a name="configuring-your-app"></a>Konfigurieren der App

Vor dem Aufrufen der APIs, die die Sicherung erstellen und wiederherstellen, muss ein Token abgerufen werden. Im folgenden Beispiel wird das Token mit dem NuGet-Paket [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) abgerufen.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System;

namespace GetTokenResourceManagerRequests
{
    class Program
    {
        static void Main(string[] args)
        {
            var authenticationContext = new AuthenticationContext("https://login.microsoftonline.com/{tenant id}");
            var result = authenticationContext.AcquireTokenAsync("https://management.azure.com/", "{application id}", new Uri("{redirect uri}"), new PlatformParameters(PromptBehavior.Auto)).Result;

            if (result == null) {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }

            Console.WriteLine(result.AccessToken);

            Console.ReadLine();
        }
    }
}
```

Ersetzen Sie `{tenant id}`, `{application id}` und `{redirect uri}` entsprechend den folgenden Anweisungen:

1. Ersetzen Sie `{tenant id}` durch die Mandanten-ID der Azure Active Directory-Anwendung, die Sie erstellt haben. Auf die ID können Sie durch Klicken auf **App-Registrierungen** -> **Endpunkte** zugreifen.

    ![Endpunkte][api-management-endpoint]

2. Ersetzen Sie `{application id}` durch den Wert, den Sie durch Navigieren zur Seite **Einstellungen** erhalten.
3. Ersetzen Sie `{redirect uri}` durch den Wert der Registerkarte **Weiterleitungs-URIs** Ihrer Azure Active Directory-Anwendung.

    Wenn die Werte angegeben wurden, sollte im Codebeispiel ein Token ähnlich dem folgenden Beispiel zurückgegeben werden:

    ![Token][api-management-arm-token]

    > [!NOTE]
    > Das Token kann nach einer bestimmten Zeit ablaufen. Führen Sie das Codebeispiel erneut aus, um ein neues Token zu generieren.

## <a name="calling-the-backup-and-restore-operations"></a>Aufrufen der Sicherungs- und Wiederherstellungsvorgänge

Die REST-APIs sind [API Management-Dienst – Backup](/rest/api/apimanagement/2019-12-01/apimanagementservice/backup) und [API Management-Dienst – Restore](/rest/api/apimanagement/2019-12-01/apimanagementservice/restore).

Legen Sie vor dem Aufrufen der in den folgenden Abschnitten beschriebenen Vorgänge zur Sicherung und Wiederherstellung den Autorisierungsanforderungsheader für den REST-Aufruf fest.

```csharp
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

### <a name="back-up-an-api-management-service"></a><a name="step1"> </a>Sichern eines API Management-Diensts

Zum Sichern eines API Management-Diensts führen Sie die folgende HTTP-Anforderung aus:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}
```

Dabei gilt:

-   `subscriptionId` – ID des Abonnements, das den API Management-Dienst enthält, den Sie sichern möchten
-   `resourceGroupName` – der Name der Ressourcengruppe Ihres Azure API Management-Diensts
-   `serviceName` – der Name des zu sichernden API Management-Diensts zum Zeitpunkt seiner Erstellung
-   `api-version` – durch `2019-12-01` ersetzen

Geben Sie im Hauptteil der Anforderung das Azure-Zielspeicherkonto, den Zugriffsschlüssel, den Blobcontainernamen und den Sicherungsnamen an:

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

Legen Sie für den `Content-Type`-Anforderungsheader den Wert `application/json` fest.

Die Sicherung ist ein länger anhaltender Vorgang, der bis zum Abschluss mehrere Minuten dauern kann. Falls die Anforderung erfolgreich war und der Sicherungsvorgang eingeleitet wurde, erhalten Sie den `202 Accepted` Antwortstatuscode mit einem `Location`-Header. Senden Sie GET-Anforderungen der URL im `Location` -Header, um den Status des Vorgangs zu ermitteln. Während der Sicherung erhalten Sie weiterhin den Statuscode „202 Accepted“. Mit dem Antwortcode `200 OK` wird der erfolgreiche Abschluss des Sicherungsvorgangs angezeigt.

### <a name="restore-an-api-management-service"></a><a name="step2"> </a>Wiederherstellen eines API Management-Diensts

Zum Wiederherstellen eines API Management-Diensts aus einer zuvor erstellten Sicherung führen Sie die folgende HTTP-Anforderung aus:

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}
```

Dabei gilt:

-   `subscriptionId` – ID des Abonnements, das den API Management-Dienst enthält, in den Sie eine Sicherung erstellen
-   `resourceGroupName` – Name der Ressourcengruppe mit dem Azure-API Management-Dienst, in dem Sie eine Sicherung wiederherstellen
-   `serviceName` – der Name des wiederherzustellenden API Management-Diensts zum Zeitpunkt seiner Erstellung
-   `api-version` – durch `api-version=2019-12-01` ersetzen

Geben Sie im Anforderungstext den Speicherort der Sicherungsdatei an. Fügen Sie das Azure-Zielspeicherkonto, den Zugriffsschlüssel, den Blobcontainernamen und den Sicherungsnamen an:

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

Legen Sie für den `Content-Type`-Anforderungsheader den Wert `application/json` fest.

Die Wiederherstellung ist ein länger anhaltender Vorgang, der bis zum Abschluss bis zu 30 Minuten dauern kann. Falls die Anforderung erfolgreich war und der Wiederherstellungsvorgang eingeleitet wurde, erhalten Sie den `202 Accepted` Antwortstatuscode mit einem `Location`-Header. Senden Sie GET-Anforderungen der URL im `Location` -Header, um den Status des Vorgangs zu ermitteln. Während der Wiederherstellung erhalten Sie weiterhin den Statuscode „202 Accepted“. Mit dem Antwortcode `200 OK` wird der erfolgreiche Abschluss des Wiederherstellungsvorgangs angezeigt.

> [!IMPORTANT]
> **Die SKU** des wiederherzustellenden Diensts **muss** mit der SKU des gesicherten Diensts übereinstimmen.
>
> **Änderungen** an der Dienstkonfiguration (z.B. APIs, Richtlinien, Erscheinungsbild des Entwicklerportals), die während des Wiederherstellungsvorgangs vorgenommen werden, **könnten überschrieben werden**.

<!-- Dummy comment added to suppress markdown lint warning -->

> [!NOTE]
> Sicherungs- und Wiederherstellungsvorgänge können außerdem mithilfe der PowerShell-Befehle [_Backup-AzApiManagement_](/powershell/module/az.apimanagement/backup-azapimanagement) bzw. [_Restore-AzApiManagement_](/powershell/module/az.apimanagement/restore-azapimanagement) ausgeführt werden.

## <a name="constraints-when-making-backup-or-restore-request"></a>Einschränkungen beim Durchführen von Backup- oder Wiederherstellungsanforderungen

-   Der im Hauptteil der Anforderung angegebene **Container** **muss vorhanden sein**.
-   Vermeiden Sie während der Sicherung **Verwaltungsänderungen im Dienst** wie beispielsweise SKU-Upgrades oder Herabstufungen, Änderungen am Domänennamen usw.
-   Die Wiederherstellung einer Sicherung nach ihrer Erstellung **wird nur 30 Tage lange garantiert**.
-   **Änderungen** an der Dienstkonfiguration (z.B. APIs, Richtlinien, Erscheinungsbild des Entwicklerportals), die während des Sicherungsvorgangs vorgenommen werden, sind ggf. **nicht in der Sicherung enthalten und gehen verloren**.
-   Wenn das Azure Storage-Konto für die [Firewall][azure-storage-ip-firewall] aktiviert ist, muss der Kunde die Gruppe von [Azure API Management-IP-Adressen der Steuerungsebene][control-plane-ip-address] in seinem Speicherkonten für Sicherungen oder Wiederherstellungen **zulassen**, damit es funktioniert. Das Azure Storage-Konto kann sich in einer beliebigen Azure-Region befinden, mit Ausnahme derjenigen, in der sich der API Management-Dienst befindet. Wenn sich der API Management-Dienst beispielsweise in der Region „USA, Westen“ befindet, kann sich das Azure Storage-Konto in der Region „USA, Westen 2“ befinden, und der Kunde muss die IP-Adresse 13.64.39.16 der Steuerungsebene (API Management-IP-Adresse der Steuerungsebene für „USA, Westen“) in der Firewall öffnen. Der Grund dafür ist, dass für Anforderungen an Azure Storage keine Übersetzung in eine öffentliche IP-Adresse über „Compute“ (Azure API Management-Steuerungsebene) in derselben Azure-Region erfolgt. Bei regionsübergreifenden Speicheranforderungen wird eine Übersetzung in die öffentliche IP-Adresse durchgeführt.
-   [Ursprungsübergreifende Ressourcenfreigabe (CORS, Cross-Origin Resource Sharing)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) sollte für den BLOB-Dienst im Azure Storage-Konto **nicht** aktiviert sein.
-   **Die SKU** des wiederherzustellenden Diensts **muss** mit der SKU des gesicherten Diensts übereinstimmen.

## <a name="what-is-not-backed-up"></a>Nicht gesicherte Elemente
-   **Nutzungsdaten** zum Erstellen von Analyseberichten sind in der Sicherung **nicht enthalten**. Verwenden Sie [Azure API Management REST API][azure api management rest api] , um regelmäßig Analyseberichte zur Aufbewahrung abzurufen.
-   [TLS/SSL-Zertifikate für die benutzerdefinierte Domäne](configure-custom-domain.md)
-   [Benutzerdefiniertes Zertifizierungsstellenzertifikat](api-management-howto-ca-certificates.md), wozu Zwischen- oder Stammzertifikate gehören, die vom Kunden hochgeladen wurden.
-   Integrationseinstellungen für [virtuelle Netzwerke](api-management-using-with-vnet.md)
-   Konfiguration der [verwalteten Identität](api-management-howto-use-managed-service-identity.md)
-   [Azure Monitor-Diagnosekonfiguration](api-management-howto-use-azure-monitor.md)
-   Einstellungen für [Protokolle und Verschlüsselungsverfahren](api-management-howto-manage-protocols-ciphers.md)
-   Inhalt des [Entwicklerportals](api-management-howto-developer-portal.md#is-the-portals-content-saved-with-the-backuprestore-functionality-in-api-management).

Die Häufigkeit, mit der Sie Dienstsicherungen durchführen, wirkt sich auf das Ziel Ihres Wiederherstellungspunkts aus. Um die Auswirkungen zu minimieren, empfehlen wir, regelmäßige Sicherungen zu implementieren und bei Bedarf Sicherungen durchzuführen, wenn Sie Änderungen an Ihrem API Management-Dienst vorgenommen haben.

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die folgenden Ressourcen für verschiedene Vorgehensweisen für den Sicherungs- und Wiederherstellungsvorgang an.

-   [Replicate Azure API Management Accounts (in englischer Sprache)](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/)
-   [Automating API Management Backup and Restore with Logic Apps](https://github.com/Azure/api-management-samples/tree/master/tutorials/automating-apim-backup-restore-with-logic-apps) (Automatisieren der Sicherung und Wiederherstellung von API Management mit Logic Apps)
-   [Azure API Management: Sichern und Wiederherstellen der Konfiguration](/archive/blogs/stuartleeks/azure-api-management-backing-up-and-restoring-configuration)
     _–der Ansatz von Stuart entspricht nicht der offiziellen Anleitung, ist aber sehr interessant._

[backup an api management service]: #step1
[restore an api management service]: #step2
[azure api management rest api]: /rest/api/apimanagement/apimanagementrest/api-management-rest
[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png
[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png
[control-plane-ip-address]: api-management-using-with-vnet.md#control-plane-ips
[azure-storage-ip-firewall]: ../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range
