---
title: Konfigurieren von Serverprotokollen für Azure Database for PostgreSQL (Einzelserver) und Zugreifen auf diese im Azure-Portal
description: In diesem Artikel wird beschrieben, wie Sie über das Azure-Portal die Serverprotokolle in Azure Database for PostgreSQL (Einzelserver) konfigurieren und auf diese zugreifen.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 13a8c1ee4b7ca114211f93245d74866e5aa5d52c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65067446"
---
# <a name="configure-and-access-azure-database-for-postgresql---single-server-logs-in-the-azure-portal"></a>Konfigurieren von Protokollen für Azure Database for PostgreSQL (Einzelserver) und Zugreifen auf diese im Azure-Portal

Sie können die [Azure Database for PostgreSQL-Protokolle](concepts-server-logs.md) im Azure-Portal konfigurieren und auflisten sowie über dieses herunterladen.

## <a name="prerequisites"></a>Voraussetzungen
Zum Ausführen der Schritte in dieser Anleitung benötigen Sie Folgendes:
- [Azure Database for PostgreSQL-Server](quickstart-create-server-database-portal.md)

## <a name="configure-logging"></a>Konfigurieren der Protokollierung
Konfigurieren Sie den Zugriff auf die Abfrage- und Fehlerprotokolle. 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

2. Wählen Sie Ihre Azure Database for PostgreSQL-Server aus.

3. Wählen Sie im Abschnitt **Überwachung** in der Randleiste die Option **Serverprotokolle** aus. 

   ![Wählen Sie „Serverprotokolle“ aus, und wählen Sie dann „Klicken Sie hier zum Aktivieren“ aus.](./media/howto-configure-server-logs-in-portal/1-select-server-logs-configure.png)

4. Wählen Sie die Überschrift **Klicken Sie hier, um Protokolle zu aktivieren und Protokollparameter zu konfigurieren** aus, um die Serverparameter anzuzeigen.

5. Ändern Sie die Parameter, die Sie anpassen müssen. Alle in dieser Sitzung vorgenommenen Änderungen werden in violett hervorgehoben.

   Nachdem Sie die Parameter geändert haben, klicken Sie auf **Speichern**. Sie können Ihre Änderungen auch **verwerfen**. 

   ![Vollständige Liste der Parameter mit zu speichernden oder zu verwerfenden Änderungen](./media/howto-configure-server-logs-in-portal/3-save-discard.png)

6. Kehren Sie zur Liste der Protokolle zurück, indem Sie auf der Seite **Serverparameter** auf die **Schaltfläche „Schließen“** (X-Symbol) klicken.

## <a name="view-list-and-download-logs"></a>Anzeigen der Liste und Herunterladen von Protokollen
Sobald die Protokollierung beginnt, können Sie eine Liste der verfügbaren Protokolle anzeigen und einzelne Protokolldateien im Bereich „Serverprotokolle“ herunterladen. 

1. Öffnen Sie das Azure-Portal.

2. Wählen Sie Ihre Azure Database for PostgreSQL-Server aus.

3. Wählen Sie im Abschnitt **Überwachung** in der Randleiste die Option **Serverprotokolle** aus. Die Seite zeigt wie im folgenden Beispiel eine Liste der Protokolldateien an:

   ![Liste der Serverprotokolle](./media/howto-configure-server-logs-in-portal/4-server-logs-list.png)

   > [!TIP]
   > Die Namenskonvention des Protokolls ist **postgresql-jjjj-mm-tt_hh0000.log**. Das im Dateinamen verwendete Datum und die Uhrzeit geben den Zeitpunkt an, zu dem das Protokoll ausgestellt wurde. Die Protokolldateien rotieren jede Stunde bzw. bei einer Größe von 100 MB, wenn diese Größe früher erreicht wird.

4. Verwenden Sie bei Bedarf das **Suchfeld**, um schnell ein spezifisches Protokoll basierend auf Datum/Uhrzeit einzugrenzen. Die Suche erfolgt anhand des Namens des Protokolls.

   ![Beispielsuche nach Protokollnamen](./media/howto-configure-server-logs-in-portal/5-search.png)

5. Laden Sie einzelne Protokolldateien wie gezeigt über die Schaltfläche **Download** (nach unten gerichtetes Pfeilsymbol) neben jeder Protokolldatei in der Tabellenzeile herunter:

   ![Klicken Sie auf das Symbol „Download“.](./media/howto-configure-server-logs-in-portal/6-download.png)

## <a name="next-steps"></a>Nächste Schritte
- Lesen Sie [Zugriff auf Serverprotokolle in der CLI](howto-configure-server-logs-using-cli.md), um weitere Informationen zum programmgesteuerten Herunterladen von Protokollen zu erhalten.
- Weitere Informationen zu [Serverprotokollen](concepts-server-logs.md) von Azure DB for PostgreSQL. 
- Weitere Informationen zu den Parameterdefinitionen und der PostgreSQL-Protokollierung finden Sie in der PostgreSQL-Dokumentation unter [Fehlerberichterstellung und Protokollierung](https://www.postgresql.org/docs/current/static/runtime-config-logging.html).

