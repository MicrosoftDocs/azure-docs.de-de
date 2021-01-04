---
title: Überprüfen von Azure Data Explorer
description: In diesem Leitfaden wird beschrieben, wie Sie eine Überprüfung für Azure Data Explorer durchführen.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/9/2020
ms.openlocfilehash: 01a1ded570d20d175b5e8eadb3e6cc8556155a85
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96550927"
---
# <a name="register-and-scan-azure-data-explorer"></a>Registrieren und Überprüfen von Azure Data Explorer

In diesem Artikel wird beschrieben, wie Sie ein Azure Data Explorer-Konto in Azure Purview registrieren und eine Überprüfung einrichten.

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Für Azure Data Explorer werden vollständige und inkrementelle Überprüfungen zum Erfassen der Metadaten und Schemas unterstützt. Darüber hinaus werden die Daten bei Überprüfungen basierend auf den System- und benutzerdefinierten Klassifizierungsregeln automatisch klassifiziert.

## <a name="prerequisites"></a>Voraussetzungen

- Erstellen Sie vor dem Registrieren der Datenquellen zunächst ein Azure Purview-Konto. Weitere Informationen zum Erstellen eines Purview-Kontos finden Sie unter [Schnellstart: Erstellen eines Azure Purview-Kontos im Azure-Portal](create-catalog-portal.md).
- Sie müssen ein Azure Purview-Datenquellenadministrator sein.

## <a name="setting-up-authentication-for-a-scan"></a>Einrichten der Authentifizierung für eine Überprüfung

Es gibt nur eine Möglichkeit, die Authentifizierung für Azure Data Explorer einzurichten:

- Dienstprinzipal

### <a name="service-principal"></a>Dienstprinzipal

Sie können für Überprüfungen eine vorhandene Dienstprinzipalauthentifizierung verwenden oder eine neue erstellen. 

> [!Note]
> Führen Sie die folgenden Schritte aus, falls Sie einen neuen Dienstprinzipal erstellen müssen:
> 1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).
> 1. Wählen Sie im Menü auf der linken Seite die Option **Azure Active Directory** aus.
> 1. Wählen Sie **App-Registrierungen** aus.
> 1. Wählen Sie **+ Registrierung einer neuen Anwendung** aus.
> 1. Geben Sie einen Namen für die **Anwendung** ein (Dienstprinzipalname).
> 1. Wählen Sie **Nur Konten in diesem Organisationsverzeichnis** aus.
> 1. Wählen Sie als Umleitungs-URI die Option **Web** aus, und geben Sie die gewünschte URL ein. Hierbei muss es sich nicht um eine reale oder geschäftliche URL handeln.
> 1. Klicken Sie anschließend auf **Registrieren**.

Es ist erforderlich, die Anwendungs-ID und das Geheimnis des Dienstprinzipals abzurufen:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem Dienstprinzipal.
1. Kopieren Sie die Werte von **Anwendungs-ID (Client)** unter **Übersicht** und von **Geheimer Clientschlüssel** unter **Zertifikate und Geheimnisse**.
1. Navigieren zum Schlüsseltresor
1. Wählen Sie **Einstellungen > Geheimnisse** aus.
1. Wählen Sie **+ Generieren/Importieren** aus, und geben Sie unter **Name** einen gewünschten Namen und den **Wert** als **Geheimen Clientschlüssel** Ihres Dienstprinzipals ein.
1. Wählen Sie **Erstellen** aus, um den Vorgang abzuschließen.
1. Falls für Ihren Schlüsseltresor noch keine Verbindung mit Purview hergestellt wurde, müssen Sie eine [neue Schlüsseltresorverbindung erstellen](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).
1. [Erstellen Sie abschließend neue Anmeldeinformationen](manage-credentials.md#create-a-new-credential), indem Sie den Dienstprinzipal zum Einrichten Ihrer Überprüfung verwenden.

#### <a name="granting-the-service-principal-access-to-your-azure-data-explorer-instance"></a>Gewähren des Zugriffs auf Ihre Azure Data Explorer-Instanz für den Dienstprinzipal

1. Navigieren Sie zum Azure-Portal. Navigieren Sie anschließend zu Ihrer Azure Data Explorer-Instanz.

1. Fügen Sie den Dienstprinzipal auf der Registerkarte **Berechtigungen** der Rolle **AllDatabasesViewer** hinzu. Dies ist im folgenden Screenshot dargestellt.

    :::image type="content" source="./media/register-scan-azure-data-explorer/permissions-auth.png" alt-text="Screenshot: Hinzufügen eines Dienstprinzipals in den Berechtigungen" border="true":::

## <a name="register-an-azure-data-explorer-account"></a>Registrieren eines Azure Data Explorer-Kontos

Gehen Sie wie folgt vor, um für Ihren Datenkatalog ein neues Azure Data Explorer-Konto (Kusto) zu registrieren:

1. Navigieren Sie zu Ihrem Purview-Konto.
1. Wählen Sie im linken Navigationsbereich die Option **Quellen** aus.
1. Wählen Sie **Registrieren** aus.
1. Wählen Sie unter **Register sources** (Quellen registrieren) die Option **Azure Data Explorer** aus.
1. Wählen Sie **Weiter**.

:::image type="content" source="media/register-scan-azure-data-explorer/register-new-data-source.png" alt-text="Registrieren einer neuen Datenquelle" border="true":::

Führen Sie auf dem Bildschirm **Register sources (Azure Data Explorer (Kusto))** (Quellen registrieren (Azure Data Explorer (Kusto))) die folgenden Schritte aus:

1. Geben Sie unter **Name** einen Namen ein, unter dem die Datenquelle im Katalog aufgeführt werden soll.
1. Wählen Sie aus, wie auf Ihr gewünschtes Speicherkonto verwiesen werden soll:
   1. Wählen Sie die Option **Aus Azure-Abonnement** und dann im Dropdownfeld **Azure-Abonnement** das entsprechende Abonnement und im Dropdownfeld **Cluster** den entsprechenden Cluster aus.
   1. Alternativ können Sie auch die Option **Manuell eingeben** auswählen und einen Dienstendpunkt (URL) eingeben.
1. Wählen Sie **Fertig stellen** aus, um die Datenquelle zu registrieren.

:::image type="content" source="media/register-scan-azure-data-explorer/register-sources.png" alt-text="Optionen für die Quellenregistrierung" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Nächste Schritte

- [Browsen im Azure Purview-Datenkatalog](how-to-browse-catalog.md)
- [Suchen im Azure Purview-Datenkatalog](how-to-search-catalog.md)
