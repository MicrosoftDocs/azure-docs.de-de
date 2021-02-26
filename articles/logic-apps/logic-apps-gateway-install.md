---
title: Installieren eines lokalen Datengateways
description: Bevor Sie über Azure Logic Apps lokal auf Daten zugreifen können, müssen Sie das lokale Datengateway herunterladen und installieren.
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: article
ms.date: 05/15/2020
ms.openlocfilehash: 799e879b4d9fd54367d54c17b3d275acfc5f34c1
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99054770"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>Installieren des lokalen Datengateways für Azure Logic Apps

Bevor Sie eine [Verbindung mit lokalen Datenquellen aus Azure Logic Apps](../logic-apps/logic-apps-gateway-connection.md) herstellen können, müssen Sie das [lokale Datengateway](https://aka.ms/on-premises-data-gateway-installer) herunterladen und auf einem lokalen Computer installieren. Das Gateway fungiert als Brücke, die schnelle Datenübertragung und Verschlüsselung zwischen lokalen Datenquellen und Ihren Logik-Apps bereitstellt. Sie können die gleiche Gatewayinstallation für andere Clouddienste verwenden, etwa für Power BI, Power Automate, PowerApps und Azure Analysis Services. Weitere Informationen zum Verwenden des Gateways mit diesen Diensten finden Sie in den folgenden Artikeln:

* [Microsoft Power Automate für das lokale Datengateway](/power-automate/gateway-reference)
* [Microsoft Power BI | Lokales Datengateway](/power-bi/service-gateway-onprem)
* [Lokales Microsoft Power Apps-Datengateway](/powerapps/maker/canvas-apps/gateway-reference)
* [Azure Analysis Services | Lokales Datengateway](../analysis-services/analysis-services-gateway.md)

Dieser Artikel zeigt, wie Sie Ihr lokales Datengateway herunterladen, installieren und einrichten, damit Sie über Azure Logic Apps auf lokale Datenquellen zugreifen können. Außerdem erfahren Sie weiter unten in diesem Thema mehr über die [Funktionsweise des Datengateways](#gateway-cloud-service). Weitere Informationen zum Gateway finden Sie unter [Was ist ein lokales Gateway](/data-integration/gateway/service-gateway-onprem)? Um Aufgaben zur Installation und Verwaltung von Gateways zu automatisieren, besuchen Sie den PowerShell-Katalog mit den [PowerShell-Cmdlets für DataGateway](https://www.powershellgallery.com/packages/DataGateway/3000.15.15).

<a name="requirements"></a>

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto und ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Konto mit einem Abonnement verfügen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

  * Bei Ihrem Azure-Konto muss es sich um ein Geschäfts-, Schul- oder Unikonto handeln (`username@contoso.com`). Sie können keine Azure B2B-Konten (Gastkonten) oder persönliche Microsoft-Konten wie @hotmail.com oder @outlook.com verwenden.

    > [!NOTE]
    > Wenn Sie sich für ein Microsoft 365-Angebot registriert und nicht Ihre geschäftliche E-Mail-Adresse angegeben haben, kann Ihre Adresse beispielsweise wie folgt aussehen: `username@domain.onmicrosoft.com`. Ihr Konto wird auf einem Azure AD-Mandanten gespeichert. In den meisten Fällen ist der Benutzerprinzipalname (User Principal Name, UPN) für Ihr Azure-Konto mit Ihrer E-Mail-Adresse identisch.

    Wenn Sie ein [Visual Studio-Standardabonnement](https://visualstudio.microsoft.com/vs/pricing/) verwenden möchten, das einem Microsoft-Konto zugeordnet ist, [erstellen Sie zunächst einen Azure AD-Mandanten](../active-directory/develop/quickstart-create-new-tenant.md), oder verwenden Sie das Standardverzeichnis. Fügen Sie dem Verzeichnis einen Benutzer mit einem Kennwort hinzu, und erteilen Sie diesem Benutzer dann Zugriff auf Ihr Azure-Abonnement. Sie können sich dann während der Installation des Gateways mit diesem Benutzernamen und Kennwort anmelden.

  * Ihr Azure-Konto muss zu einem einzigen [Azure Active Directory-Mandanten oder -Verzeichnis (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md#terminology) gehören. Sie müssen das gleiche Azure-Konto zum Installieren und Verwalten des Gateways auf dem lokalen Computer verwenden.

  * Während der Gatewayinstallation melden Sie sich mit Ihrem Azure-Konto an. Dadurch wird Ihre Gatewayinstallation ausschließlich mit Ihrem Azure-Konto verknüpft. Sie können eine Gatewayinstallation nicht mit mehreren Azure-Konten oder Azure AD-Mandanten verknüpfen.

  * Später müssen Sie im Azure-Portal dasselbe Azure-Konto verwenden, um eine Azure-Gatewayressource zu erstellen, die mit Ihrer Gatewayinstallation verknüpft ist. Sie können nur eine Gatewayinstallation und eine Azure-Gatewayressource miteinander verknüpfen. Ihr Azure-Konto kann jedoch mit unterschiedlichen Gatewayinstallationen verknüpft sein, die jeweils einer Azure-Gatewayressource zugeordnet sind. Ihre Logik-Apps können diese Gatewayressource dann in Triggern und Aktionen verwenden, die auf lokale Datenquellen zugreifen können.

* Dies sind die Anforderungen für den lokalen Computer:

  **Mindestanforderungen**

  * .NET Framework 4.7.2
  * 64-Bit-Version von Windows 7 oder Windows Server 2008 R2 (oder höher)

  **Empfohlen**

  * 8-Kern-CPU
  * 8 GB Arbeitsspeicher
  * 64-Bit-Version von Windows Server 2012 R2 oder höher
  * SSD-Speicher (Solid State Drive) für Spoolvorgang

  > [!NOTE]
  > Das Gateway unterstützt Windows Server Core nicht.

* **Damit zusammenhängende Überlegungen**

  * Installieren Sie das lokale Datengateway nur auf einem lokalen Computer und nicht auf einem Domänencontroller. Sie müssen das Gateway nicht auf dem Computer installieren, auf dem sich die Datenquelle befindet. Sie benötigen nur ein Gateway für alle Ihre Datenquellen, Sie müssen das Gateway also nicht für jede Datenquelle installieren.

    > [!TIP]
    > Um Wartezeiten zu minimieren, können Sie das Gateway so nah wie möglich zur Datenquelle oder auf demselben Computer installieren, vorausgesetzt, Sie haben die Berechtigungen.

  * Installieren Sie das Gateway auf einem lokalen Computer in einem verkabelten Netzwerk, der mit dem Internet verbunden ist, immer eingeschaltet ist und sich nicht im Energiesparmodus befindet. Andernfalls kann das Gateway nicht ausgeführt werden, und die Gatewayleistung wird beim Einsatz in einem Drahtlosnetzwerk ggf. beeinträchtigt.

  * Wenn Sie planen, Windows-Authentifizierung zu verwenden, stellen Sie sicher, dass Sie das Gateway auf einem Computer installieren, der Mitglied derselben Active Directory-Umgebung ist wie Ihre Datenquellen.

  * Die Region, die Sie für Ihre Gatewayinstallation auswählen, ist die gleiche Region, die Sie auswählen müssen, wenn Sie später die Azure-Gatewayressource für Ihre Logik-App erstellen. Standardmäßig ist diese Region derselbe Standort wie Ihr Azure AD-Mandant, der Ihr Azure-Konto verwaltet. Der Standort kann jedoch während der Gatewayinstallation geändert werden.

  * Wenn Sie Ihre Gatewayinstallation aktualisieren, deinstallieren Sie zuerst Ihr aktuelles Gateway, um die Erfahrung zu bereinigen.

    Als bewährte Methode stellen Sie sicher, dass Sie eine unterstützte Version verwenden. Microsoft gibt jeden Monat ein neues Update für das lokale Datengateway frei und unterstützt derzeit nur die letzten sechs Releases für das lokale Datengateway. Wenn Sie Probleme mit der Version haben, die Sie verwenden, versuchen Sie, [ein Upgrade auf die neueste Version](https://aka.ms/on-premises-data-gateway-installer) auszuführen, da Ihr Problem möglicherweise in der aktuellen Version behoben ist.

  * Das Gateway verfügt über zwei Modi: Standardmodus und persönlicher Modus, der nur für Power BI gilt. Auf demselben Computer können nicht mehrere Gateways im gleichen Modus ausgeführt werden.

  * Azure Logic Apps unterstützt Lese- und Schreibvorgänge über das Gateway. Allerdings besitzen diese Vorgänge [Limits hinsichtlich Ihrer Nutzlastgröße](/data-integration/gateway/service-gateway-onprem#considerations).

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>Installieren eines Datengateways

1. [Laden Sie den Gateway-Installer auf einen lokalen Computer herunter, und führen Sie den Installer aus](https://aka.ms/on-premises-data-gateway-installer).

1. Überprüfen Sie die Mindestanforderungen, behalten Sie den Standardinstallationspfad bei, akzeptieren Sie die Nutzungsbedingungen, und wählen Sie dann **Installieren** aus.

   ![Überprüfen der Anforderungen und Akzeptieren der Nutzungsbedingungen](./media/logic-apps-gateway-install/review-and-accept-terms-of-use.png)

1. Nachdem das Gateway erfolgreich installiert wurde, geben Sie die E-Mail-Adresse für Ihr Azure-Konto an und wählen dann **Anmelden** aus. Beispiel:

   ![Anmelden mit einem Geschäfts-, Schul- oder Unikonto](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

   Die Gatewayinstallation kann nur mit einem Azure-Konto verknüpft werden.

1. Wählen Sie **Ein neues Gateway auf diesem Computer registrieren** > **Weiter** aus. Durch diesen Schritt wird Ihre Gatewayinstallation beim [Gatewayclouddienst](#gateway-cloud-service) registriert.

   ![Registrieren des Gateways auf dem lokalen Computer](./media/logic-apps-gateway-install/register-gateway-local-computer.png)

1. Geben Sie die folgenden Informationen für Ihre Gatewayinstallation an:

   * Einen Gatewaynamen, der in Ihrem Azure AD Mandanten eindeutig ist
   * Den zu verwendenden Wiederherstellungsschlüssel (mindestens acht Zeichen)
   * Bestätigung für den Wiederherstellungsschlüssel

   ![Bereitstellen von Informationen für die Gatewayinstallation](./media/logic-apps-gateway-install/gateway-name-recovery-key.png)

   > [!IMPORTANT]
   > Bewahren Sie Ihren Wiederherstellungsschlüssel an einem sicheren Ort auf. Sie benötigen diesen Schlüssel, wenn Sie den Standort ändern, verschieben oder wiederherstellen bzw. eine Gatewayinstallation übernehmen möchten.

   Beachten Sie die Option zum **Hinzufügen zu einem vorhandenen Gatewaycluster**, die Sie auswählen, wenn Sie zusätzliche Gateways für Szenarien [Hochverfügbarkeit](#high-availability) installieren.

1. Überprüfen Sie die Region für den Gatewayclouddienst und die [Azure Service Bus Messaging-Instanz](../service-bus-messaging/service-bus-messaging-overview.md), die von Ihrer Gatewayinstallation verwendet wird. Standardmäßig ist diese Region derselbe Standort wie Ihr Azure AD-Mandant für Ihr Azure-Konto.

   ![Bestätigen der Region für den Gatewaydienst und Service Bus](./media/logic-apps-gateway-install/confirm-gateway-region.png)

1. Klicken Sie zum Übernehmen der Standardregion auf **Konfigurieren**. Wenn die Standardregion jedoch nicht die nächstgelegene Region ist, können Sie die Region ändern.

   *Warum sollte ich die Region für die Gatewayinstallation ändern?*

   Sie können die Region des Gateways in die Region der Logik-App ändern, um beispielsweise die Wartezeit zu verringern. Alternativ können Sie die Region auswählen, die der lokalen Datenquelle am nächsten liegt. Ihre *Gatewayressource in Azure* und Ihre Logik-App können unterschiedliche Standorte haben.

   1. Klicken Sie neben der aktuellen Region auf **Region ändern**.

      ![Ändern der aktuellen Gatewayregion](./media/logic-apps-gateway-install/change-gateway-service-region.png)

   1. Öffnen Sie auf der nächsten Seite die Liste **Region auswählen**, wählen Sie die gewünschte Region aus, und klicken Sie dann auf **Fertig**.

      ![Auswählen einer anderen Region für den Gatewaydienst](./media/logic-apps-gateway-install/select-region-gateway-install.png)

1. Überprüfen Sie die Informationen im abschließenden Bestätigungsfenster. In diesem Beispiel wird das gleiche Konto für Logic Apps, Power BI, PowerApps und Power Automate verwendet, sodass das Gateway für alle diese Dienste verfügbar ist. Wählen Sie **Schließen** aus, wenn Sie fertig sind.

   ![Bestätigen der Informationen zum Datengateway](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

1. [Erstellen Sie jetzt die Azure-Ressource für Ihre Gatewayinstallation](../logic-apps/logic-apps-gateway-connection.md).

<a name="communication-settings"></a>

## <a name="check-or-adjust-communication-settings"></a>Überprüfen oder Anpassen von Kommunikationseinstellungen

Das lokale Datengateway ist für Cloudkonnektivität von [Azure Service Bus Messaging](../service-bus-messaging/service-bus-messaging-overview.md) abhängig und richtet die entsprechenden ausgehenden Verbindungen zu der dem Gateway zugeordneten Azure-Region ein. Wenn in Ihrer Arbeitsumgebung der Datenverkehr über einen Proxy oder eine Firewall an das Internet weitergeleitet werden muss, verhindert diese Einschränkung unter Umständen, dass das lokale Datengateway eine Verbindung mit dem Gatewayclouddienst und Azure Service Bus Messaging herstellt. Das Gateway verfügt über mehrere Kommunikationseinstellungen, die Sie anpassen können.

In einem Beispielszenario verwenden Sie benutzerdefinierte Connectors, die auf lokale Ressourcen zugreifen, indem Sie die lokale Datengatewayressource in Azure verwenden. Wenn Sie auch über eine Firewall verfügen, die den Datenverkehr an bestimmte IP-Adressen einschränkt, müssen Sie die Gatewayinstallation einrichten, um den Zugriff auf die entsprechenden *[ausgehenden IP-Adressen](logic-apps-limits-and-config.md#outbound) der verwalteten Connectors* zu ermöglichen. Für *alle* Logik-Apps in derselben Region werden dieselben IP-Adressbereiche verwendet.

Weitere Informationen finden Sie in den folgenden Themen:

* [Anpassen von Kommunikationseinstellungen für das lokale Datengateway](/data-integration/gateway/service-gateway-communication)
* [Konfigurieren von Proxyeinstellungen für das lokale Datengateway](/data-integration/gateway/service-gateway-proxy)

<a name="high-availability"></a>

## <a name="high-availability-support"></a>Unterstützung für Hochverfügbarkeit

Um einzelne Fehlerquellen (Single Points of Failure) für lokalen Datenzugriff zu vermeiden, können Sie mehrere Gatewayinstallationen (nur Standardmodus) für jeweils eine Fehlerquelle auf einem anderen Computer einrichten und Sie als Cluster oder Gruppe einrichten. Auf diese Weise werden Datenanforderungen an das zweite Gateway weitergeleitet, wenn das primäre Gateway nicht verfügbar ist. Da Sie nur ein Standardgateway auf einem Computer installieren können, müssen Sie jedes weitere Gateway, das sich im Cluster befindet, auf einem anderen Computer installieren. Alle Connectors, die mit dem lokalen Datengateway zusammenarbeiten, unterstützen Hochverfügbarkeit.

* Sie müssen bereits über mindestens eine Gatewayinstallation im gleichen Azure-Konto wie das primäre Gateway sowie über den Wiederherstellungsschlüssel für diese Installation verfügen.

* Für das primäre Gateway muss das Gatewayupdate vom November 2017 oder ein höheres Update ausgeführt werden.

Nachdem Sie Ihr primäres Gateway eingerichtet haben, wählen Sie zum Installieren eines weiteren Gateways **Einem vorhandenen Gatewaycluster hinzufügen** aus, wählen Sie das primäre Gateway (das erste Gateway, das Sie installiert haben) aus, und geben Sie den Wiederherstellungsschlüssel für dieses Gateway an. Weitere Informationen finden Sie unter [Hochverfügbarkeitscluster für lokale Datengateways](/data-integration/gateway/service-gateway-install#add-another-gateway-to-create-a-cluster).

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>Ändern des Orts oder Migrieren, Wiederherstellen oder Übernehmen eines vorhandenen Gateways

Wenn Sie den Ort Ihres Gateways ändern, die Gatewayinstallation auf einen neuen Computer verschieben, ein beschädigtes Gateway wiederherstellen oder den Besitz eines vorhandenen Gateways übernehmen müssen, benötigen Sie den Wiederherstellungsschlüssel, der bei der Gatewayinstallation bereitgestellt wurde.

> [!NOTE]
> Bevor Sie das Gateway auf dem Computer wiederherstellen, auf dem sich die ursprüngliche Gatewayinstallation befindet, müssen Sie zunächst das Gateway auf diesem Computer deinstallieren. Bei dieser Aktion wird das ursprüngliche Gateway getrennt.
> Wenn Sie einen Gatewaycluster für einen beliebigen Clouddienst entfernen oder löschen, können Sie diesen Cluster nicht wiederherstellen.

1. Führen Sie den Gateway-Installer auf dem Computer aus, der über das vorhandene Gateway verfügt.

1. Nachdem der Installer geöffnet wurde, melden Sie sich mit dem gleichen Azure-Konto an, das beim Installieren des Gateways verwendet wurde.

1. Wählen Sie **Migrieren, Wiederherstellen oder Übernehmen eines vorhandenen Gateways** > **Weiter** aus. Beispiel:

   ![Aktivieren von „Migrate, restore, or takeover an existing gateway“ (Migrieren, Wiederherstellen oder Übernehmen eines vorhandenen Gateways)](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

1. Wählen aus den verfügbaren Clustern und Gateways aus, und geben Sie den Wiederherstellungsschlüssel für das ausgewählte Gateway ein. Beispiel:

   ![Auswählen des Gateways und Bereitstellen des Wiederherstellungsschlüssels](./media/logic-apps-gateway-install/select-existing-gateway.png)

1. Wählen Sie zum Ändern der Region **Region ändern** und dann die neue Region aus.

1. Wenn Sie fertig sind, wählen Sie **Konfigurieren** aus, damit Sie die Aufgabe abschließen können.

## <a name="tenant-level-administration"></a>Verwaltung auf Mandantenebene

Um einen Einblick in alle lokalen Datengateways in einem Azure AD-Mandanten zu erhalten, können sich globale Administratoren in diesem Mandanten am [Power Platform Admin Center](https://powerplatform.microsoft.com) als Mandantenadministrator anmelden und die Option **Datengateways** auswählen. Weitere Informationen finden Sie unter [Verwaltung auf Mandantenebene für das lokale Datengateway](/data-integration/gateway/service-gateway-tenant-level-admin).

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>Neustarten des Gateways

Standardmäßig wird die Gatewayinstallation auf Ihrem lokalen Computer als Windows-Dienstkonto mit dem Namen „Lokaler Datengatewaydienst“ ausgeführt. Die Gatewayinstallation verwendet jedoch den Namen `NT SERVICE\PBIEgwService` für die Kontoanmeldeinformationen „Anmelden als“ und verfügt über die Berechtigungen „Anmelden als Dienst“.

> [!NOTE]
> Ihr Windows-Dienstkonto ist weder mit dem Konto, das zum Herstellen von Verbindungen mit den lokalen Datenquellen verwendet wird, noch mit dem Azure-Konto identisch, das Sie zum Anmelden bei Clouddiensten verwenden.

Sie können Gateways (wie jeden anderen Windows-Dienst) auf verschiedene Arten starten und beenden. Weitere Informationen finden Sie unter [Neustarten eines lokalen Datengateways](/data-integration/gateway/service-gateway-restart).

<a name="gateway-cloud-service"></a>

## <a name="how-the-gateway-works"></a>So funktioniert das Gateway

Benutzer in Ihrer Organisation können auf lokale Daten zugreifen, für die sie bereits über autorisierten Zugriff verfügen. Bevor diese Benutzer jedoch eine Verbindung mit Ihrer lokalen Datenquelle herstellen können, müssen Sie ein lokales Datengateway installieren und einrichten. Normalerweise ist ein Administrator die Person, die ein Gateway installiert und einrichtet. Diese Aktionen erfordern möglicherweise Serveradministratorberechtigungen oder besondere Kenntnisse zu Ihren lokalen Servern.

Das Gateway ermöglicht die schnellere und sicherere Kommunikation hinter den Kulissen. Diese Kommunikation erfolgt zwischen einem Benutzer in der Cloud, dem Gatewayclouddienst und ihrer lokalen Datenquelle. Der Gatewayclouddienst verschlüsselt und speichert Ihre Anmeldeinformationen für Datenquellen und Ihre Gatewaysdetails. Der Dienst leitet außerdem Abfragen und deren Ergebnisse zwischen dem Benutzer, dem Gateway und Ihrer lokalen Datenquelle weiter.

Das Gateway funktioniert mit Firewalls und verwendet nur ausgehende Verbindungen. Der gesamte Datenverkehr stammt als sicherer ausgehender Datenverkehr vom Gateway-Agent. Das Gateway sendet die Daten von lokalen Quellen durch verschlüsselte Kanäle über [Azure Service Bus Messaging](../service-bus-messaging/service-bus-messaging-overview.md). Diese Service Bus-Instanz erstellt einen Kanal zwischen dem Gateway und dem aufrufenden Dienst, speichert jedoch keine Daten. Alle über das Gateway übertragenen Daten sind verschlüsselt.

![Architektur für das lokale Datengateway](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

> [!NOTE]
> Abhängig vom Clouddienst müssen Sie möglicherweise eine Datenquelle für das Gateway einrichten.

In den folgenden Schritten wird beschrieben, was geschieht, wenn Sie mit einem Element interagieren, das mit einer lokalen Datenquelle verbunden ist:

1. Der Clouddienst erstellt eine Abfrage mit den verschlüsselten Anmeldeinformationen für die lokale Datenquelle. Der Dienst sendet dann die Abfrage und die Anmeldeinformationen zur Verarbeitung an die Gatewaywarteschlange.

1. Der Gatewayclouddienst analysiert die Abfrage und pusht die Anforderung dann an Azure Service Bus Messaging.

1. Azure Service Bus Messaging sendet die ausstehenden Anforderungen an das Gateway.

1. Das Gateway ruft die Abfrage ab, entschlüsselt die Anmeldeinformationen und stellt anhand dieser Anmeldeinformationen eine Verbindung mit mindestens einer Datenquelle her.

1. Das Gateway sendet die Abfrage zur Ausführung an die Datenquelle.

1. Die Ergebnisse werden aus der Datenquelle zurück an das Gateway und dann an den Gatewayclouddienst gesendet. Der Gatewayclouddienst verwendet dann die Ergebnisse.

### <a name="authentication-to-on-premises-data-sources"></a>Authentifizierung bei lokalen Datenquellen

Gespeicherte Anmelde Informationen werden verwendet, um eine Verbindung zwischen dem Gateway und lokalen Datenquellen herzustellen. Unabhängig vom Benutzer verwendet das Gateway die gespeicherten Anmeldeinformationen, um eine Verbindung herzustellen. Möglicherweise gibt es Authentifizierungsausnahmen für bestimmte Dienste, z.B. DirectQuery und LiveConnect für Analysis Services in Power BI.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)

Microsoft Cloud Services nutzen [Azure AD](../active-directory/fundamentals/active-directory-whatis.md) zum Authentifizieren von Benutzern. Ein Azure AD-Mandant enthält Benutzernamen und Sicherheitsgruppen. In der Regel ist die E-Mail-Adresse, die Sie für die Anmeldung verwenden, identisch mit dem Benutzerprinzipalnamen (User Principal Name, UPN) für Ihr Konto.

### <a name="what-is-my-upn"></a>Was ist mein UPN?

Wenn Sie kein Domänenadministrator sind, kennen Sie Ihren UPN möglicherweise nicht. Um den UPN für Ihr Konto zu ermitteln, führen Sie den Befehl `whoami /upn` über Ihre Arbeitsstation aus. Obwohl das Ergebnis wie eine E-Mail-Adresse aussieht, ist es der UPN für Ihr lokales Domänenkonto.

### <a name="synchronize-an-on-premises-active-directory-with-azure-ad"></a>Synchronisieren eines lokalen Active Directory mit Azure AD

Der UPN für Ihre lokalen Active Directory Konten-und Azure AD-Konten muss identisch sein. Stellen Sie daher sicher, dass jedes lokale Active Directory-Konto mit Ihrem Azure AD-Konto übereinstimmt. Die Clouddienste kennen nur die Konten in Azure AD. Daher müssen Sie Ihrem lokalen Active Directory kein Konto hinzufügen. Wenn das Konto in Azure AD nicht vorhanden ist, können Sie dieses Konto nicht verwenden.

Nachfolgend finden Sie Möglichkeiten, wie Sie Ihre lokalen Active Directory-Konten mit Azure AD abgleichen können.

* Manuelles Hinzufügen von Konten zu Azure AD.

  Erstellen Sie ein Konto im Azure-Portal oder im Microsoft 365 Admin Center. Stellen Sie sicher, dass der Kontoname mit dem UPN für das lokale Active Directory-Konto übereinstimmt.

* Synchronisieren lokaler Konten mit Ihrem Azure AD-Mandanten, indem Sie das Azure Active Directory Connect-Tool verwenden.

  Das Azure AD Connect-Tool stellt Optionen für die Verzeichnissynchronisierung und die Einrichtung von Authentifizierung bereit. Diese Optionen umfassen Kennworthashsynchronisierung, Pass-Through-Authentifizierung und Verbund. Wenn Sie kein Mandantenadministrator oder lokaler Domänenadministrator sind, wenden Sie sich an Ihren IT-Administrator, um Azure AD Connect einzurichten. Azure AD Connect stellt sicher, dass der Azure AD-UPN mit dem lokalen Active Directory-UPN übereinstimmt. Dieser Abgleich ist hilfreich, wenn Sie Analysis Services-Liveverbindungen mit Power BI-oder SSO-Funktionen (einmaliges Anmelden) verwenden.

  > [!NOTE]
  > Durch die Synchronisierung von Konten mit dem Azure AD Connect-Tool werden neue Konten in Ihrem Azure AD-Mandanten erstellt.

<a name="faq"></a>

## <a name="faq-and-troubleshooting"></a>FAQ und Fehlerbehebung

* [Lokales Datengateway: Häufig gestellte Fragen](/data-integration/gateway/service-gateway-onprem-faq)
* [Problembehandlung des lokalen Datengateways](/data-integration/gateway/service-gateway-tshoot)
* [Überwachen und Optimieren der Gatewayleistung](/data-integration/gateway/service-gateway-performance)

## <a name="next-steps"></a>Nächste Schritte

* [Herstellen einer Verbindung mit dem lokalen Datengateway für Logik-Apps](../logic-apps/logic-apps-gateway-connection.md)
* [Unternehmensintegrationsfeatures](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Connectors für Azure Logic Apps](../connectors/apis-list.md)
