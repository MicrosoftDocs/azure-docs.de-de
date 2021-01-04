---
title: Durchführen einer Überprüfung für Azure Files
description: In diesem Leitfaden wird beschrieben, wie Sie eine Überprüfung für Azure Files durchführen.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/01/2020
ms.openlocfilehash: a0bd7a4cd8afafc16f05b4a37cd5723304ad931e
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96550902"
---
# <a name="register-and-scan-azure-files"></a>Registrieren und Überprüfen von Azure Files

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Azure Files unterstützt vollständige und inkrementelle Überprüfungen, um die Metadaten zu erfassen und basierend auf System- und Kundenklassifizierungen entsprechende Klassifizierungen auf die Metadaten anzuwenden.

## <a name="prerequisites"></a>Voraussetzungen

- Erstellen Sie vor dem Registrieren der Datenquellen zunächst ein Azure Purview-Konto. Weitere Informationen zum Erstellen eines Purview-Kontos finden Sie unter [Schnellstart: Erstellen eines Azure Purview-Kontos im Azure-Portal](create-catalog-portal.md).
- Sie müssen ein Datenquellenadministrator sein, um Überprüfungen einrichten und planen zu können. Ausführlichere Informationen hierzu finden Sie unter [Katalogberechtigungen](catalog-permissions.md).

## <a name="register-an-azure-files-storage-account"></a>Registrieren eines Azure Files-Speicherkontos

Gehen Sie wie folgt vor, um in Ihrem Datenkatalog ein neues Azure Files-Konto zu registrieren:

1. Navigieren Sie zu Ihrem Purview-Datenkatalog.
1. Wählen Sie im linken Navigationsbereich die Option **Verwaltungscenter** aus.
1. Wählen Sie unter **Sources and scanning** (Quellen und Überprüfung) die Option **Datenquellen** aus.
1. Wählen Sie **+ Neu** aus.
1. Wählen Sie unter **Register sources** (Quellen registrieren) die Option **Azure Files** aus. Wählen Sie **Weiter**.

:::image type="content" source="media/register-scan-azure-files/register-new-data-source.png" alt-text="Registrieren einer neuen Datenquelle" border="true":::

Gehen Sie auf dem Bildschirm **Register sources (Azure Files)** (Quellen registrieren (Azure Files)) wie folgt vor:

1. Geben Sie unter **Name** einen Namen ein, unter dem die Datenquelle im Katalog aufgeführt werden soll.
1. Wählen Sie aus, wie auf Ihr gewünschtes Speicherkonto verwiesen werden soll:
   1. Wählen Sie die Option **Aus Azure-Abonnement** und dann im Dropdownfeld **Azure-Abonnement** das entsprechende Abonnement und im Dropdownfeld **Speicherkontoname** das entsprechende Speicherkonto aus.
   1. Alternativ können Sie auch die Option **Manuell eingeben** auswählen und einen Dienstendpunkt (URL) eingeben.
1. Wählen Sie **Fertig stellen** aus, um die Datenquelle zu registrieren.

:::image type="content" source="media/register-scan-azure-files/register-sources.png" alt-text="Optionen für die Quellenregistrierung" border="true":::

## <a name="set-up-authentication-for-a-scan"></a>Einrichten der Authentifizierung für eine Überprüfung

Gehen Sie wie folgt vor, um die Authentifizierung für Azure Files-Speicher mit einem Kontoschlüssel einzurichten:

1. Wählen Sie **Kontoschlüssel** als Authentifizierungsmethode aus.
2. Wählen Sie die Option **Aus Azure-Abonnement** aus.
3. Wählen Sie Ihr Azure-Abonnement aus, in dem das Azure Files-Konto enthalten ist.
4. Wählen Sie in der Liste den Namen Ihres Speicherkontos aus.
5. Klicken Sie auf **Fertig stellen**.

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Nächste Schritte

- [Browsen im Azure Purview-Datenkatalog](how-to-browse-catalog.md)
- [Suchen im Azure Purview-Datenkatalog](how-to-search-catalog.md)