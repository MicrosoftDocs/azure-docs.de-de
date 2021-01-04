---
title: ITSM-Connector – Secure Export in Azure Monitor
description: In diesem Artikel erfahren Sie, wie Sie Ihre ITSM-Produkte/-Dienste mit Secure Export in Azure Monitor verbinden, um ITSM-Arbeitselemente zentral zu überwachen und zu verwalten.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 09/08/2020
ms.openlocfilehash: 1cd8041f801a418f67d26461c5f4e9ebff7e5c30
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97507301"
---
# <a name="connect-azure-to-itsm-tools-by-using-secure-export"></a>Verbinden von Azure mit ITSM-Tools unter Verwendung von Secure Export

In diesem Artikel erfahren Sie, wie Sie die Verbindung zwischen Ihrem ITSM-Produkt oder -Dienst (IT-Service-Management) mithilfe von Secure Export konfigurieren.

Secure Export ist eine aktualisierte Version des [ITSM-Connectors (ITSMC)](./itsmc-overview.md). Beide Versionen ermöglichen es Ihnen, Arbeitselemente in einem ITSM-Tool zu erstellen, wenn Azure Monitor Warnungen sendet. Diese Funktionalität umfasst Metrik-, Protokoll- und Aktivitätsprotokollwarnungen.

Der ITSM-Connector verwendet Anmeldeinformationen aus Benutzername und Kennwort. Da Secure Export Azure Active Directory (Azure AD) nutzt, bietet diese Variante eine strengere Authentifizierung. Azure AD ist der cloudbasierte Identitäts- und Zugriffsverwaltungsdienst von Microsoft. Über diesen Dienst können Benutzer sich anmelden und auf interne oder externe Ressourcen zugreifen. Bei Verwendung von Azure AD mit ITSM können Azure-Warnungen (anhand der Anwendungs-ID von Azure AD) identifiziert werden, die an das externe System gesendet wurden.

> [!NOTE]
> Die Option zum Herstellen einer Verbindung zwischen Azure und ITSM-Tools über Secure Export befindet sich in der Vorschauphase.

## <a name="secure-export-architecture"></a>Secure Export-Architektur

Mit der Secure Export-Architektur werden die folgenden neuen Funktionen eingeführt:

* **Neue Aktionsgruppe**: Warnungen werden nicht über die ITSM-Aktionsgruppe, die ITSMC verwendet, sondern über die Aktionsgruppe für sichere Webhooks an das ITSM-Tool gesendet.
* **Azure AD-Authentifizierung**: Die Authentifizierung erfolgt nicht über Anmeldeinformationen aus Benutzername und Kennwort, sondern über Azure AD.

## <a name="secure-export-data-flow"></a>Secure Export-Datenfluss

Nachfolgend sind die Schritte des Secure Export-Datenflusses gezeigt:

1. Azure Monitor sendet eine Warnung, die für die Verwendung von Secure Export konfiguriert ist.
2. Die Nutzdaten der Warnung werden von einer „Sicherer Webhook“-Aktion an das ITSM-Tool gesendet.
3. Die ITSM-Anwendung überprüft mit Azure AD, ob die Warnung autorisiert ist und vom ITSM-Tool empfangen werden darf.
4. Wenn die Warnung autorisiert ist, führt die Anwendung folgende Schritte aus:
   
   1. Erstellen eines Arbeitselements (z. B. einen Incident) im ITSM-Tool.
   2. Binden der ID des Konfigurationselements an die Kundenverwaltungsdatenbank (Customer Management Database, CMDB).

![Die Abbildung zeigt, wie das ITSM-Tool mit Azure AD, Azure-Warnungen und einer Aktionsgruppe kommuniziert.](media/it-service-management-connector-secure-webhook-connections/secure-export-diagram.png)

## <a name="benefits-of-secure-export"></a>Vorteile von Secure Export

Die Integration hat unter anderem folgende Vorteile:

* **Bessere Authentifizierung**: Azure AD bietet eine sicherere Authentifizierung ohne die Timeouts, die bei ITSMC häufig auftreten.
* **Warnungen werden im ITSM-Tool behandelt**: Bei Metrikwarnungen werden die Status „ausgelöst“ und „behoben“ verwendet. Wenn die Bedingung erfüllt ist, lautet der Warnungsstatus „ausgelöst“. Ist die Bedingung nicht mehr erfüllt, lautet der Status der Warnung „behoben“. In ITSMC können Warnungen nicht automatisch behandelt werden. Bei Secure Export wird der Status „behoben“ an das ITSM-Tool übermittelt und automatisch aktualisiert.
* **[Einheitliches Warnungsschema](./alerts-common-schema.md)** : In ITSMC variiert das Schema der Nutzdaten einer Warnung basierend auf dem Warnungstyp. In Secure Export wird ein einheitliches Schema für alle Warnungstypen verwendet. Dieses gemeinsame Schema umfasst das Konfigurationselement für alle Warnungstypen. Sämtliche Warnungstypen können ihr Konfigurationselement an die CMDB binden.

Führen Sie die folgenden Schritte aus, um den ITSM-Connector zu verwenden:

1. Registrieren Sie Ihre App in Azure AD.
2. Erstellen Sie eine Aktionsgruppe „Sicherer Webhook“.
3. Konfigurieren Sie Ihre Partnerumgebung. 

Von Secure Export werden Verbindungen mit den folgenden ITSM-Tools unterstützt:
* [ServiceNow](#connect-servicenow-to-azure-monitor)
* [BMC Helix](#connect-bmc-helix-to-azure-monitor)

## <a name="register-with-azure-active-directory"></a>Registrieren bei Azure Active Directory

Führen Sie diese Schritte aus, um die Anwendung bei Azure AD zu registrieren:

1. Befolgen Sie die unter [Registrieren einer Anwendung bei Microsoft Identity Platform](../../active-directory/develop/quickstart-register-app.md) beschriebenen Schritte.
2. Wählen Sie in Azure AD die Option **Anwendung verfügbar machen** aus.
3. Wählen Sie für **Anwendungs-ID-URI** die Option **Festlegen** aus.

   [![Screenshot der Option zum Festlegen des Anwendungs-ID-URIs.](media/it-service-management-connector-secure-webhook-connections/azure-ad.png)](media/it-service-management-connector-secure-webhook-connections/azure-ad-expand.png#lightbox)
4. Wählen Sie **Speichern** aus.

## <a name="create-a-secure-webhook-action-group"></a>Erstellen einer Aktionsgruppe „Sicherer Webhook“

Nachdem Ihre Anwendung bei Azure AD registriert wurde, können Sie basierend auf Azure-Warnungen Arbeitselemente in Ihrem ITSM-Tool erstellen. Dazu verwenden Sie die Aktion „Sicherer Webhook“ in den Aktionsgruppen.

Aktionsgruppen bieten eine modulare und wiederverwendbare Methode zum Auslösen von Aktionen für Azure-Warnungen. Sie können Aktionsgruppen im Azure-Portal mit Metrikwarnungen, Aktivitätsprotokollwarnungen und Azure Log Analytics-Warnungen verwenden.
Weitere Informationen zu Aktionsgruppen finden Sie unter [Erstellen und Verwalten von Aktionsgruppen im Azure-Portal](./action-groups.md).

Befolgen Sie diese Anweisungen für sichere Webhooks, um einen Webhook zu einer Aktion hinzuzufügen:

1. Suchen Sie im [Azure-Portal](https://portal.azure.com/) nach dem Eintrag **Überwachen**, und wählen Sie ihn aus. Im Bereich **Überwachen** sind alle Ihre Überwachungseinstellungen und -daten an einem zentralen Ort zusammengefasst.
2. Wählen Sie **Warnungen** > **Aktionen verwalten** aus.
3. Wählen Sie [Aktionsgruppe hinzufügen](./action-groups.md#create-an-action-group-by-using-the-azure-portal), und füllen Sie die Felder aus.
4. Geben Sie jeweils einen Namen in die Felder **Aktionsgruppenname** und **Kurzname** ein. Der Kurzname wird anstelle eines vollständigen Aktionsgruppennamens verwendet, wenn Benachrichtigungen mithilfe dieser Gruppe gesendet werden.
5. Wählen Sie **Sicherer Webhook** aus.
6. Legen Sie folgende Einstellungen fest:
   1. Wählen Sie die Objekt-ID der Azure Active Directory-Instanz aus, die Sie registriert haben.
   2. Fügen Sie für den URI die Webhook-URL ein, die Sie aus der [ITSM-Toolumgebung](#configure-the-itsm-tool-environment) kopiert haben.
   3. Legen Sie für **Allgemeines Warnungsschema aktivieren** die Option **Ja** fest. 

   Die folgende Abbildung zeigt die Konfiguration einer „Sicherer Webhook“-Beispielaktion:

   ![Screenshot einer Aktion „Sicherer Webhook“.](media/it-service-management-connector-secure-webhook-connections/secure-webhook.png)

## <a name="configure-the-itsm-tool-environment"></a>Konfigurieren der ITSM-Toolumgebung

Die Konfiguration umfasst zwei Schritte:
1. Abrufen des URI für die Definition des sicheren Exports
2. Definitionen gemäß dem Flow des ITSM-Tools


### <a name="connect-servicenow-to-azure-monitor"></a>Herstellen einer Verbindung von ServiceNow mit Azure Monitor

In den folgenden Abschnitten finden Sie Einzelheiten dazu, wie Sie Ihr ServiceNow-Produkt und den sicheren Export in Azure verbinden.

### <a name="prerequisites"></a>Voraussetzungen

Stellen Sie sicher, dass die folgenden Voraussetzungen erfüllt sind:

* Azure AD wurde registriert.
* Sie verfügen über eine unterstützte Version von The ServiceNow Event Management – ITOM (Version „Orlando“ oder höher).

### <a name="configure-the-servicenow-connection"></a>Konfigurieren der ServiceNow-Verbindung

1. Verwenden Sie diesen Link als URI für die Definition des sicheren Exports: https://(instance name).service-now.com/api/sn_em_connector/em/inbound_event?source=azuremonitor.

2. Gehen Sie gemäß der versionsspezifischen Anleitung vor:
   * [Paris](https://docs.servicenow.com/bundle/paris-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [Orlando](https://docs.servicenow.com/bundle/orlando-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [New York](https://docs.servicenow.com/bundle/newyork-it-operations-management/page/product/event-management/task/azure-events-authentication.html)

### <a name="connect-bmc-helix-to-azure-monitor"></a>Verbinden von BMC Helix mit Azure Monitor

In den folgenden Abschnitten finden Sie Einzelheiten dazu, wie Sie Ihr BMC Helix-Produkt und Secure Export in Azure verbinden.

### <a name="prerequisites"></a>Voraussetzungen

Stellen Sie sicher, dass die folgenden Voraussetzungen erfüllt sind:

* Azure AD wurde registriert.
* Sie verfügen über die unterstützte Version von BMC Helix Multi-Cloud Service Management (Version 19.08 oder höher).

### <a name="configure-the-bmc-helix-connection"></a>Konfigurieren der BMC Helix-Verbindung

1. Führen Sie in der BMC Helix-Umgebung die folgenden Schritte aus, um den URI für den sicheren Export abzurufen:

   1. Melden Sie sich bei Integration Studio an.
   2. Suchen Sie nach dem Flow zum **Erstellen von Incidents anhand von Azure-Warnungen**.
   3. Kopieren Sie die Webhook-URL.
   
   ![Screenshot der Webhook-URL in Integration Studio.](media/it-service-management-connector-secure-webhook-connections/bmc-url.png)
   
2. Gehen Sie gemäß der versionsspezifischen Anleitung vor:
   * [Aktivieren der vordefinierten Integration in Azure Monitor für Version 20.02](https://docs.bmc.com/docs/multicloud/enabling-prebuilt-integration-with-azure-monitor-879728195.html)
   * [Aktivieren der vordefinierten Integration in Azure Monitor für Version 19.11](https://docs.bmc.com/docs/multicloudprevious/enabling-prebuilt-integration-with-azure-monitor-904157623.html)

3. Wechseln Sie bei der Konfiguration der Verbindung in BMC Helix zu Ihrer BMC-Integrationsinstanz, und führen Sie folgende Schritte aus:

   1. Wählen Sie einen **Katalog** aus.
   2. Wählen Sie **Azure-Warnungen** aus.
   3. Wählen Sie **Connectors** aus.
   4. Wählen Sie eine **Konfiguration** aus.
   5. Wählen Sie die Konfiguration **Neue Verbindung hinzufügen** aus.
   6. Geben Sie im Abschnitt zur Konfiguration die folgenden Informationen ein:
      - **Name**: Legen Sie einen eigenen Namen fest.
      - **Autorisierungstyp**: **NONE**
      - **Beschreibung**: Geben Sie eine eigene Beschreibung ein.
      - **Site**: **Cloud**
      - **Anzahl von Instanzen**: **2** (Standardwert).
      - **Aktivieren**: Standardmäßig ausgewählt, um die Verwendung zu ermöglichen.
      - Die Azure-Mandanten-ID und die Azure-Anwendungs-ID stammen von der Anwendung, die Sie zuvor definiert haben.

![Screenshot einer BMC-Konfiguration.](media/it-service-management-connector-secure-webhook-connections/bmc-configuration.png)




## <a name="next-steps"></a>Nächste Schritte

* [Erstellen von ITSM-Arbeitselementen aus Azure-Warnungen](./itsmc-overview.md)
