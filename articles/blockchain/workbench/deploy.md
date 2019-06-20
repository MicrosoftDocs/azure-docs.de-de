---
title: Bereitstellen von Azure Blockchain Workbench
description: Bereitstellen von Azure Blockchain Workbench
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/06/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: 4fffc54428b152a060594a5c107d3ac08457aaaa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65154640"
---
# <a name="deploy-azure-blockchain-workbench"></a>Bereitstellen von Azure Blockchain Workbench

Azure Blockchain Workbench wird mithilfe einer Projektmappenvorlage im Azure Marketplace bereitgestellt. Die Vorlage vereinfacht die Bereitstellung von Komponenten, die für die Erstellung von Blockchainanwendungen benötigt werden. Nach der Bereitstellung bietet Blockchain Workbench Zugriff auf Client-Apps zum Erstellen und Verwalten von Benutzern und Blockchainanwendungen.

Weitere Informationen zu den Komponenten der Blockchain Workbench finden Sie unter [Azure Blockchain Workbench-Architektur](architecture.md).

## <a name="prepare-for-deployment"></a>Vorbereiten der Bereitstellung

Blockchain Workbench ermöglicht die Bereitstellung eines Blockchainledgers zusammen mit einer Gruppe relevanter Azure-Dienste, die am häufigsten für die Erstellung einer blockchainbasierten Anwendung verwendet wird. Bei der Bereitstellung von Blockchain Workbench werden die folgenden Azure-Dienste innerhalb einer Ressourcengruppe im Azure-Abonnement bereitgestellt.

* App Service Plan (Standard)
* Application Insights
* Event Grid
* Azure Key Vault
* Service Bus
* SQL-Datenbank (Standard S0) + logischer SQL-Server
* Azure Storage-Konto (Standard LRS)
* VM-Skalierungsgruppe mit der Kapazität 1
* Virtual Network-Ressourcengruppe (mit Load Balancer, Netzwerksicherheitsgruppe, öffentlicher IP-Adresse, virtuellem Netzwerk)
* Optional: Azure Blockchain-Dienst (Standardeinstellung: Basic B0)

Im Folgenden wird eine Beispielbereitstellung gezeigt, die in der Ressourcengruppe **myblockchain** erstellt wurde.

![Beispielbereitstellung](media/deploy/example-deployment.png)

Die Kosten für Blockchain Workbench stellen eine Aggregation der Kosten der zugrunde liegenden Azure-Dienste dar. Preise zu Azure-Dienste können mit dem [Preisrechner](https://azure.microsoft.com/pricing/calculator/) berechnet werden.

## <a name="prerequisites"></a>Voraussetzungen

Für Azure Blockchain Workbench sind Konfigurationen in Azure AD und Anwendungsregistrierungen vonnöten. Sie können die Azure AD-Konfigurationen vor einer Bereitstellung [manuell](#azure-ad-configuration) durchführen oder ein Skript nach der Bereitstellung ausführen. Wenn Sie Blockchain Workbench erneut bereitstellen, lesen Sie den Abschnitt [Azure AD-Konfiguration](#azure-ad-configuration) zur Überprüfung Ihrer Azure AD-Konfiguration.

> [!IMPORTANT]
> Workbench muss nicht in demselben Mandanten bereitgestellt werden, den Sie zum Registrieren einer Azure AD-Anwendung verwenden. Workbench muss in einem Mandanten bereitgestellt werden, in dem Sie über ausreichende Berechtigungen zum Bereitstellen von Ressourcen verfügen. Weitere Informationen über Azure AD-Mandanten finden Sie unter [Einrichten eines Azure Active Directory-Mandanten](../../active-directory/develop/quickstart-create-new-tenant.md) und [Integrieren von Anwendungen in Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="deploy-blockchain-workbench"></a>Bereitstellen von Blockchain Workbench

Wenn die erforderlichen Schritte abgeschlossen sind, können Sie die Blockchain Workbench bereitstellen. In den folgenden Abschnitten werden die Schritte zur Bereitstellung des Frameworks beschrieben.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie oben rechts Ihr Konto aus, und wechseln Sie zum gewünschten Azure AD-Mandanten, in dem Sie Azure Blockchain Workbench bereitstellen möchten.
3. Wählen Sie im linken Bereich **Ressource erstellen**. Suchen Sie nach `Azure Blockchain Workbench` in der Suchleiste **Marketplace durchsuchen**. 

    ![Marketplace-Suchleiste](media/deploy/marketplace-search-bar.png)

4. Wählen Sie **Azure Blockchain Workbench** aus.

    ![Marketplace-Suchergebnisse](media/deploy/marketplace-search-results.png)

5. Klicken Sie auf **Erstellen**.
6. Nehmen Sie die grundlegenden Einstellungen vor.

    ![Erstellen der Azure Blockchain Workbench](media/deploy/blockchain-workbench-settings-basic.png)

    | Einstellung | BESCHREIBUNG  |
    |---------|--------------|
    | Ressourcenpräfix | Kurzer eindeutiger Bezeichner für Ihre Bereitstellung. Dieser Wert wird als Grundlage bei der Benennung von Ressourcen verwendet. |
    | VM-Benutzername | Der Benutzername wird als Administrator für alle virtuellen Computer (VMs) verwendet. |
    | Authentifizierungsart | Wählen Sie diese aus, wenn Sie ein Kennwort oder einen Schlüssel für die Verbindung mit VMs nutzen möchten. |
    | Kennwort | Das Kennwort wird für die Verbindung mit VMs verwendet. |
    | SSH | Verwenden Sie einen öffentlichen RSA-Schlüssel im einzeiligen Format beginnend mit **ssh-rsa**, oder verwenden Sie das mehrzeilige PEM-Format. Sie können SSH-Schlüssel mit `ssh-keygen` unter Linux und OS X oder mit PuTTYGen unter Windows erzeugen. Weitere Informationen zu SSH-Schlüsseln finden Sie unter [Verwenden von SSH-Schlüsseln mit Windows in Azure](../../virtual-machines/linux/ssh-from-windows.md). |
    | Datenbank- und Blockchain-Kennwort | Geben Sie das Kennwort für den Zugriff auf die im Rahmen der Bereitstellung erstellte Datenbank an. Das Kennwort muss drei der folgenden vier Anforderungen erfüllen: Es muss zwischen 12 und 72 Zeichen lang sein und muss einen Kleinbuchstaben, einen Großbuchstaben, eine Zahl und ein Sonderzeichen beinhalten (nicht zulässig: Raute (#), Prozentzeichen (%), Komma (,), Stern (*), Backquote (\`), doppelte Anführungszeichen ("), einfache Anführungszeichen ('), Bindestriche (-) oder Semikolons (;)). |
    | Bereitstellungsregion | Geben Sie an, wo die Blockchain Workbench-Ressourcen bereitgestellt werden sollen. Für optimale Verfügbarkeit sollte dies der Einstellung **Ort** entsprechen. |
    | Abonnement | Geben Sie das Azure-Abonnement an, das Sie für Ihre Bereitstellung verwenden möchten. |
    | Ressourcengruppen | Erstellen Sie eine Ressourcengruppe, indem Sie **Neu erstellen** auswählen, und geben Sie einen eindeutigen Namen für die Ressourcengruppe ein. |
    | Location | Geben Sie die Region an, in der Sie das Framework bereitstellen möchten. |

7. Wählen Sie **OK**, um die Konfiguration der Grundeinstellungen fertig zu stellen.

8. Wählen Sie unter **Erweiterte Einstellungen**, ob Sie ein neues Blockchainnetzwerk erstellen oder ein bestehendes Blockchainnetzwerk mit Proof-of-Authority (PoA) verwenden möchten.

    Für **Neues Element erstellen**:

    Mit der Option *Neues Element erstellen* wird ein Quorum-Ledger im Azure Blockchain-Dienst mit der Standard-SKU „Basic“ bereitgestellt.

    ![Erweiterte Einstellungen für ein neues Blockchainnetzwerk](media/deploy/advanced-blockchain-settings-new.png)

    | Einstellung | BESCHREIBUNG  |
    |---------|--------------|
    | Azure Blockchain-Diensttarif | Wählen Sie für Blockchain Workbench den Azure Blockchain-Diensttarif **Basic** oder **Standard** aus. |
    | Azure Active Directory-Einstellungen | Klicken Sie auf **Später hinzufügen**.</br>Hinweis: Wenn Sie [Azure AD vorab konfigurieren](#azure-ad-configuration) möchten oder wenn sie es erneut bereitstellen, klicken Sie auf *Jetzt hinzufügen*. |
    | VM-Auswahl | Wählen Sie die bevorzugte Speicherleistung und VM-Größe für Ihr Blockchainnetzwerk aus. Wählen Sie eine kleinere VM-Größe wie *Standard DS1 v2* aus, wenn Sie ein Abonnement mit niedrigen Dienstlimits verwenden, z.B. den Azure-Free-Tarif. |

    Für **Vorhandene verwenden**:

    Die Option *Vorhandene verwenden* ermöglicht Ihnen, ein Ethereum-PoA-Blockchainnetzwerk festzulegen. Endpunkte haben die folgenden Anforderungen:

   * Beim Endpunkt muss es sich um ein Ethereum-PoA-Blockchainnetzwerk handeln.
   * Der Endpunkt muss öffentlich über das Netzwerk zugänglich sein.
   * Für das PoA-Blockchainnetzwerk sollte der Gaspreis auf 0 (null) gesetzt sein.

     > [!NOTE]
     > Blockchain Workbench-Konten werden nicht finanziert. Wenn Geldmittel erforderlich sind, tritt ein Fehler bei der Transaktion auf.

     ![Erweiterte Einstellungen für ein vorhandenes Blockchainnetzwerk](media/deploy/advanced-blockchain-settings-existing.png)

     | Einstellung | BESCHREIBUNG  |
     |---------|--------------|
     | Ethereum-RPC-Endpunkt | Geben Sie den RPC-Endpunkt eines bestehenden PoA-Blockchainnetzwerks an. Der Endpunkt beginnt mit „https://“ oder „http://“ und endet mit einer Portnummer. Zum Beispiel, `http<s>://<network-url>:<port>` |
     | Azure Active Directory-Einstellungen | Klicken Sie auf **Später hinzufügen**.</br>Hinweis: Wenn Sie [Azure AD vorab konfigurieren](#azure-ad-configuration) möchten oder wenn sie es erneut bereitstellen, klicken Sie auf *Jetzt hinzufügen*. |
     | VM-Auswahl | Wählen Sie die bevorzugte Speicherleistung und VM-Größe für Ihr Blockchainnetzwerk aus. Wählen Sie eine kleinere VM-Größe wie *Standard DS1 v2* aus, wenn Sie ein Abonnement mit niedrigen Dienstlimits verwenden, z.B. den Azure-Free-Tarif. |

9. Klicken Sie auf **OK**, um die Konfiguration der erweiterten Einstellungen abzuschließen.

10. Überprüfen Sie die Zusammenfassung, um sicherzustellen, dass die eingegebenen Parameter richtig sind.

    ![Zusammenfassung](media/deploy/blockchain-workbench-summary.png)

11. Wählen Sie **Erstellen** aus, um den Bedingungen zuzustimmen und Ihre Azure Blockchain Workbench bereitzustellen.

Die Bereitstellung kann bis zu 90 Minuten dauern. Den Fortschritt können Sie im Azure-Portal überwachen. Wählen Sie in der neu erstellten Ressourcengruppe **Bereitstellungen > Übersicht**, um den Status der bereitgestellten Artefakte anzuzeigen.

> [!IMPORTANT]
> Nach der Bereitstellung müssen Sie die Active Directory-Einstellungen festlegen. Wenn Sie auf **Später hinzufügen** geklickt haben, müssen Sie das [Azure AD-Konfigurationsskript](#azure-ad-configuration-script) ausführen.  Wenn Sie auf **Jetzt hinzufügen** klicken, müssen Sie die [Antwort-URL konfigurieren](#configuring-the-reply-url).

## <a name="blockchain-workbench-web-url"></a>Blockchain Workbench-Web-URL

Wenn die Bereitstellung der Blockchain Workbench abgeschlossen ist, sind Ihre Blockchain Workbench-Ressourcen in einer neuen Ressourcengruppe enthalten. Der Zugriff auf die Blockchain Workbench-Dienste erfolgt über eine Web-URL. In den folgenden Schritten wird beschrieben, wie Sie die Web-URL des bereitgestellten Frameworks abrufen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie im linken Navigationsbereich **Ressourcengruppen** aus.
3. Wählen Sie den Namen der Ressourcengruppe, den Sie bei der Bereitstellung der Blockchain Workbench angegeben haben.
4. Klicken Sie auf die Spaltenüberschrift **TYP**, um die Liste alphabetisch nach Typ zu sortieren.
5. Es gibt zwei Ressourcen vom Typ **App-Dienst**. Wählen Sie die Ressource vom Typ **App-Dienst** *ohne* das Suffix „-api“.

    ![Liste der App-Dienste](media/deploy/resource-group-list.png)

6. Kopieren Sie im Abschnitt **Zusammenfassung** zum App-Dienst den **URL**-Wert, der die Web-URL darstellt, in Ihre bereitgestellte Blockchain Workbench.

    ![Zusammenfassung zum App-Dienst](media/deploy/app-service.png)

Informationen zum Verknüpfen eines benutzerdefinierten Domänennamens mit Blockchain Workbench finden Sie unter [Konfigurieren eines benutzerdefinierten Domänennamens für eine Web-App in Azure App Services, der Traffic Manager verwendet](../../app-service/web-sites-traffic-manager-custom-domain-name.md).

## <a name="azure-ad-configuration-script"></a>Azure AD-Konfigurationsskript

Um die Blockchain Workbench-Bereitstellung abzuschließen, muss Azure AD konfiguriert werden. Für die Konfiguration verwenden Sie ein PowerShell-Skript.

1. Navigieren Sie in einem Browser zur [Blockchain Workbench-Web-URL](#blockchain-workbench-web-url).
2. Es werden Anweisungen zum Einrichten von Azure AD mithilfe von Cloud Shell angezeigt. Kopieren Sie den Befehl, und starten Sie Cloud Shell.

    ![Starten des AAD-Skripts](media/deploy/launch-aad-script.png)

3. Wählen Sie den Azure AD-Mandanten aus, auf dem Sie Blockchain Workbench bereitgestellt haben.
4. Fügen Sie in Cloud Shell den Befehl ein, und führen Sie ihn aus.
5. Wenn Sie dazu aufgefordert werden, rufen Sie den Azure AD-Mandanten auf, den Sie für Blockchain Workbench verwenden möchten. Dies ist der Mandant, der die Benutzer für Blockchain Workbench enthält.

    > [!IMPORTANT]
    > Der authentifizierte Benutzer erfordert Berechtigungen zum Erstellen von Azure AD-Anwendungsregistrierungen und gewährt delegierte Anwendungsberechtigungen im Mandanten. Sie müssen einen Administrator des Mandanten darum bitten, das Azure AD-Konfigurationsskript auszuführen oder einen neuen Mandanten zu erstellen.

    ![Aufrufen des Azure AD-Mandanten](media/deploy/choose-tenant.png)

6. Sie werden aufgefordert, sich mithilfe eines Browsers beim Azure AD-Mandanten zu authentifizieren. Öffnen Sie die Web-URL in einem Browser, geben Sie den Code ein, und authentifizieren Sie sich.

    ![Authentifizieren mit dem Code](media/deploy/authenticate.png)

7. Das Skript gibt mehrere Statusmeldungen aus. Sie erhalten eine **SUCCESS**-Statusmeldung, wenn der Mandant erfolgreich bereitgestellt wurde.
8. Rufen Sie die Blockchain Workbench-URL auf. Sie werden aufgefordert, ihre Zustimmung zu erteilen, um Leseberechtigungen für das Verzeichnis zu gewähren. Dadurch erhält die Blockchain Workbench-Web-App Zugriff auf die Benutzer im Mandanten. Wenn Sie der Mandantenadministrator sind, können Sie Ihre Zustimmung für die gesamte Organisation erteilen. Diese Option akzeptiert die Zustimmung für alle Benutzer im Mandanten. Andernfalls wird jeder Benutzer aufgefordert, seine Zustimmung zur ersten Verwendung der Blockchain Workbench-Webanwendung zu erteilen.
9. Klicken Sie auf **Akzeptieren**, um Ihre Zustimmung zu erteilen.

     ![Zustimmung zum Lesen von Benutzerprofilen](media/deploy/graph-permission-consent.png)

10. Nachdem Sie Ihre Zustimmung erteilt haben, kann die Blockchain Workbench-Web-App verwendet werden.

## <a name="azure-ad-configuration"></a>Azure AD-Konfiguration

Wenn Sie Azure AD-Einstellungen manuell konfigurieren oder vor der Bereitstellung überprüfen möchten, führen Sie alle Schritte in diesem Abschnitt durch. Wenn Sie Azure AD-Einstellungen automatisch konfigurieren lassen möchten, verwenden Sie das [Azure AD-Konfigurationsskript](#azure-ad-configuration-script), nachdem Sie Blockchain Workbench bereitgestellt haben.

### <a name="blockchain-workbench-api-app-registration"></a>Blockchain Workbench-API-App-Registrierung

Die Blockchain Workbench-Bereitstellung erfordert die Registrierung einer Azure AD-Anwendung. Sie benötigen einen Azure Active Directory (Azure AD)-Mandanten, um die App zu registrieren. Sie können einen bestehenden Mandanten verwenden oder einen neuen Mandanten anlegen. Wenn Sie einen bestehenden Azure AD-Mandanten verwenden, benötigen Sie ausreichende Berechtigungen, um innerhalb eines Azure AD-Mandanten Anwendungen zu registrieren und Berechtigungen für die Graph-API zu erteilen. Wenn Sie in einem vorhandenen Azure AD-Mandanten nicht über ausreichende Berechtigungen verfügen, erstellen Sie einen neuen Mandanten.


1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie oben rechts Ihr Konto aus, und wechseln Sie zum gewünschten Azure AD-Mandanten. Der Mandant sollte der Mandant des Abonnementadministrators sein, in dem die Workbench installiert ist. Zudem müssen Sie über ausreichende Berechtigungen verfügen, um Anwendungen zu registrieren.
3. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus. Wählen Sie **App-Registrierungen** > **Registrierung einer neuen Anwendung**.

    ![App-Registrierung](media/deploy/app-registration.png)

4. Geben Sie einen **Namen** und eine **Anmelde-URL** an. Sie können Platzhalterwerte verwenden, da die Werte während der Bereitstellung geändert werden. 

    ![Erstellen der App-Registrierung](media/deploy/app-registration-create.png)

    |Einstellung  | Wert  |
    |---------|---------|
    |NAME | `Blockchain API` |
    |Anwendungstyp |Web-App/API|
    |Anmelde-URL | `https://blockchainapi` |

5. Wählen Sie **Erstellen**, um die Azure AD-Anwendung zu registrieren.

### <a name="modify-manifest"></a>Anpassen des Manifests

Als Nächstes müssen Sie das Manifest für die Verwendung von Anwendungsrollen innerhalb von Azure AD ändern, um Blockchain Workbench-Administratoren festzulegen.  Weitere Informationen zu Anwendungsmanifesten finden Sie unter [Azure Active Directory-Anwendungsmanifest](../../active-directory/develop/reference-app-manifest.md).

1. Wählen Sie für die Anwendung, die Sie registriert haben, im Detailbereich der registrierten Anwendung **Manifest** aus.
2. Generieren Sie eine GUID. Eine GUID kann mit dem PowerShell-Befehl „[guid] :: NewGuid ()“ oder mit dem Cmdlet „New-GUID“ generiert werden. Alternativ können Sie auch eine GUID-Generator-Website verwenden.
3. Sie werden den Abschnitt **appRoles** des Manifests aktualisieren. Wählen Sie im Bereich „Manifest bearbeiten“ die Option **Bearbeiten**, und ersetzen Sie `"appRoles": []` mit der gegebenen JSON-Datei. Stellen Sie sicher, dass Sie den Wert für das Feld **ID** durch die von Ihnen generierte GUID ersetzen. 

    ![Bearbeiten des Manifests](media/deploy/edit-manifest.png)

    ``` json
    "appRoles": [
         {
           "allowedMemberTypes": [
             "User",
             "Application"
           ],
           "displayName": "Administrator",
           "id": "<A unique GUID>",
           "isEnabled": true,
           "description": "Blockchain Workbench administrator role allows creation of applications, user to role assignments, etc.",
           "value": "Administrator"
         }
       ],
    ```

    > [!IMPORTANT]
    > Der Wert **Administrator** wird benötigt, um Blockchain Workbench-Administratoren zu identifizieren.

4. Ändern Sie im Manifest auch den Wert **Oauth2AllowImplicitFlow** in **true**.

    ``` json
    "oauth2AllowImplicitFlow": true,
    ```

5. Klicken Sie zum Speichern der Manifeständerungen auf **Speichern**.

### <a name="add-graph-api-required-permissions"></a>Hinzufügen der erforderlichen Berechtigungen für die Graph-API

Der Benutzer muss zulassen, dass die API-Anwendung auf das Verzeichnis zuzugreifen darf. Legen Sie die folgenden erforderlichen Berechtigungen für die API-Anwendung fest:

1. Wählen Sie in der Registrierung der Blockchain-API-App **Einstellungen > Erforderliche Berechtigungen > API auswählen > Microsoft Graph**.

    ![Auswählen einer API](media/deploy/client-app-select-api.png)

    Klicken Sie auf **Auswählen**.

2. Klicken Sie im Abschnitt **Zugriff gewähren** unter **Delegierte Berechtigungen** auf **Lesezugriff auf grundlegende Profile aller Benutzer**.

    ![Gewähren des Zugriffs](media/deploy/client-app-read-perms.png)

    Klicken Sie auf **Speichern** und dann auf **Fertig**.

3. Wählen Sie unter **Erforderliche Berechtigungen** die Option **Berechtigungen erteilen**, und wählen Sie dann zur Bestätigung **Ja**.

   ![Erteilen von Berechtigungen](media/deploy/client-app-grant-permissions.png)

   Durch das Erteilen von Berechtigungen kann die Blockchain Workbench auf Benutzer im Verzeichnis zugreifen. Die Leseberechtigung ist erforderlich, um Mitglieder in der Blockchain Workbench zu suchen und hinzuzufügen.

### <a name="get-application-id"></a>Abrufen der Anwendungs-ID

Die Anwendungs-ID und die Mandateninformationen werden für die Bereitstellung benötigt. Sammeln und speichern Sie die Informationen für die Verwendung während der Bereitstellung.

1. Wählen Sie für die Anwendung, die Sie registriert haben, **Einstellungen** > **Eigenschaften** aus.
2. Kopieren und speichern Sie im Bereich **Eigenschaften** die folgenden Werte für die spätere Verwendung während der Bereitstellung.

    ![API-App-Eigenschaften](media/deploy/app-properties.png)

    | Zu speichernde Einstellung  | Verwendung bei der Bereitstellung |
    |------------------|-------------------|
    | Anwendungs-ID | Azure Active Directory-Setup > Anwendungs-ID |

### <a name="get-tenant-domain-name"></a>Abrufen des Domänennamens des Mandanten

Sammeln und speichern Sie den Domänennamen des Active Directory-Mandanten, unter dem die Anwendungen registriert sind. 

Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus. Wählen Sie **Benutzerdefinierte Domänennamen**. Kopieren und speichern Sie den Domänennamen.

![Domänenname](media/deploy/domain-name.png)

### <a name="guest-user-settings"></a>Gastbenutzereinstellungen

Wenn Sie über Gastbenutzer in Ihrem Azure AD-Mandanten verfügen, führen Sie die zusätzlichen Schritte aus, um sicherzustellen, dass die Zuweisung und Verwaltung von Blockchain Workbench-Benutzern ordnungsgemäß funktioniert.

1. Wechseln Sie zu Ihrem Azure AD-Mandanten, und klicken Sie auf **Azure Active Directory > Benutzereinstellungen > Externe Einstellungen zur Zusammenarbeit verwalten**.
2. Legen Sie **Berechtigungen für Gastbenutzer sind eingeschränkt** auf **Nein** fest.
    ![Externe Einstellungen zur Zusammenarbeit](media/deploy/user-collaboration-settings.png)

## <a name="configuring-the-reply-url"></a>Konfiguration der Antwort-URL

Nachdem Azure Blockchain Workbench bereitgestellt wurde, muss im nächsten Schritt sichergestellt werden, dass die Azure Active Directory-Clientanwendung (Azure AD) für die korrekte **Antwort-URL** der bereitgestellten Blockchain Workbench-Web-URL registriert ist.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Vergewissern Sie sich, dass Sie sich im Mandanten befinden, bei dem Sie die Azure AD-Clientanwendung registriert haben.
3. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus. Wählen Sie **App-Registrierungen** aus.
4. Wählen Sie die Azure AD-Clientanwendung aus, die Sie im Abschnitt für die Voraussetzungen registriert haben.
5. Wählen Sie **Einstellungen > Antwort-URLs** aus.
6. Geben Sie die Haupt-Web-URL der Azure Blockchain Workbench-Bereitstellung an, die Sie im Abschnitt zur **Azure Blockchain Workbench-Web-URL** abgerufen haben. Die Antwort-URL weist das Präfix `https://` auf. Zum Beispiel, `https://myblockchain2-7v75.azurewebsites.net`

    ![Antwort-URLs](media/deploy/configure-reply-url.png)

7. Wählen Sie **Speichern** aus, um die Clientregistrierung zu aktualisieren.

## <a name="remove-a-deployment"></a>Entfernen einer Bereitstellung

Wenn eine Bereitstellung nicht mehr benötigt wird, können Sie eine Bereitstellung durch Löschen der Blockchain Workbench-Ressourcengruppe entfernen.

1. Navigieren Sie im Azure-Portal im linken Navigationsbereich zu **Ressourcengruppe**, und wählen Sie die Ressourcengruppe aus, die gelöscht werden soll. 
2. Wählen Sie die Option **Ressourcengruppe löschen**. Überprüfen Sie den Löschvorgang, indem Sie den Ressourcengruppennamen eingeben und auf **Löschen** klicken.

    ![Ressourcengruppe löschen](media/deploy/delete-resource-group.png)

## <a name="next-steps"></a>Nächste Schritte

Mithilfe der Anleitung in diesem Artikel haben Sie Azure Blockchain Workbench bereitgestellt. Informationen zum Erstellen einer Blockchainanwendung finden Sie in der nächsten Anleitung.

> [!div class="nextstepaction"]
> [Erstellen einer Blockchainanwendung in Azure Blockchain Workbench](create-app.md)
