---
title: 'SSL/TLS: Azure Database for PostgreSQL (Einzelserver)'
description: Hier finden Sie Anweisungen und Informationen zum Konfigurieren der TLS-Konnektivität für Azure Database for PostgreSQL (Einzelserver).
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: c98ee8f747975d4237c2906be2060eddbc7b9990
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000945"
---
# <a name="configure-tls-connectivity-in-azure-database-for-postgresql---single-server"></a>Konfigurieren der TLS-Konnektivität in Azure Database for PostgreSQL (Einzelserver)

Azure Database for PostgreSQL bevorzugt das Herstellen einer Verbindung zwischen Ihren Clientanwendungen und dem PostgreSQL-Dienst über TLS (Transport Layer Security), ehemals als SSL (Secure Sockets Layer) bezeichnet. Durch das Erzwingen von TLS-Verbindungen zwischen Ihrem Datenbankserver und Ihren Clientanwendungen können Sie sich vor Man-in-the-Middle-Angriffen schützen, indem Sie den Datenstrom zwischen dem Server und Ihrer Anwendung verschlüsseln.

Standardmäßig ist der PostgreSQL-Datenbankdienst so konfiguriert, dass TLS-Verbindungen erforderlich sind. Sie können das Erfordern von TLS deaktivieren, wenn Ihre Clientanwendung keine TLS-Verbindungen unterstützt.

>[!NOTE]
> Basierend auf dem Feedback von Kunden haben wir die eingestellte Unterstützung des Stammzertifikats für unsere vorhandene Baltimore-Stammzertifizierungsstelle bis zum 15. Februar 2021 (15.02.2021) verlängert.

> [!IMPORTANT] 
> Das SSL-Stammzertifikat wird ab dem 15. Februar 2021 (15.02.2021) auslaufen. Aktualisieren Sie Ihre Anwendung bitte mithilfe des [neuen Zertifikats](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem). Weitere Informationen finden Sie unter [Geplante Zertifikatupdates](concepts-certificate-rotation.md)


## <a name="enforcing-tls-connections"></a>Erzwingen von TLS-Verbindungen

Bei allen Azure Database for PostgreSQL-Servern, die über das Azure-Portal und die CLI bereitgestellt werden, ist die Erzwingung von TLS-Verbindungen standardmäßig aktiviert. 

Entsprechend enthalten Verbindungszeichenfolgen, die im Azure-Portal unter dem Server in den Verbindungszeichenfolgen-Einstellungen vorab definiert sind, die erforderlichen Parameter für gängige Sprachen für die Verbindung mit dem Datenbankserver mithilfe von TLS. Der TLS-Parameter variiert je nach Connector, z. B. „ssl=true“ oder „sslmode=require“ oder „sslmode=required“ und weitere Variationen.

## <a name="configure-enforcement-of-tls"></a>Konfigurieren der Erzwingung von TLS

Sie können die Erzwingung von TLS-Verbindungen optional deaktivieren. Microsoft Azure empfiehlt, die Einstellung **SSL-Verbindung erzwingen** immer zu aktivieren, um die Sicherheit zu erhöhen.

### <a name="using-the-azure-portal"></a>Verwenden des Azure-Portals

Rufen Sie Ihren Azure-Datenbank für PostgreSQL-Server auf, und klicken Sie auf **Verbindungssicherheit**. Verwenden Sie die Umschaltfläche, um die Einstellung **SSL-Verbindung erzwingen** zu aktivieren/deaktivieren. Klicken Sie dann auf **Speichern**.

:::image type="content" source="./media/concepts-ssl-connection-security/1-disable-ssl.png" alt-text="Verbindungssicherheit: Erzwingung von TLS/SSL deaktivieren":::

Bestätigen Sie die Einstellung auf der Seite **Übersicht**, indem Sie den Indikator für den **SSL-Erzwingungsstatus** anzeigen.

### <a name="using-azure-cli"></a>Verwenden der Azure-Befehlszeilenschnittstelle

Sie können den Parameter **ssl-enforcement** in der Azure CLI mit den Werten `Enabled` bzw. `Disabled` aktivieren oder deaktivieren.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Sicherstellen, dass die Anwendung oder das Framework TLS-Verbindungen unterstützt

Einige Anwendungsframeworks, die PostgreSQL für ihre Datenbankdienste verwenden, aktivieren TLS nicht standardmäßig während der Installation. Wenn Ihr PostgreSQL-Server TLS-Verbindungen erzwingt, die Anwendung aber nicht für TLS konfiguriert wurde, kann die Anwendung unter Umständen keine Verbindung mit dem Datenbankserver herstellen. Lesen Sie in der Dokumentation Ihrer Anwendung nach, wie TLS-Verbindungen aktiviert werden.

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>Anwendungen, die eine Zertifikatüberprüfung für TLS-Verbindungen erfordern

In einigen Fällen erfordern Anwendungen eine lokale Zertifikatdatei, die auf der Grundlage der Zertifikatdatei einer vertrauenswürdigen Zertifizierungsstelle (Certificate Authority, CA) generiert wurde, um eine sichere Verbindung herzustellen. Das Zertifikat für die Verbindungsherstellung mit einem Azure Database for PostgreSQL-Server befindet sich unter https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem. Laden Sie die Zertifikatdatei herunter, und speichern Sie sie am gewünschten Ort. 

Zertifikate für Server in Sovereign Clouds finden Sie unter den folgenden Links: [Azure Government](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem), [Azure China](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) und [Azure Deutschland](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt).

### <a name="connect-using-psql"></a>Herstellen einer Verbindung mit psql

Das folgende Beispiel zeigt, wie Sie einen PostgreSQL-Server mithilfe des psql-Befehlszeilen-Hilfsprogramms verbinden können. Verwenden Sie die Einstellung `sslmode=verify-full` für die Verbindungszeichenfolge, um die Überprüfung des TLS-/SSL-Zertifikats zu erzwingen. Übergeben Sie den Pfad der lokalen Zertifikatdatei an den Parameter `sslrootcert`.

Der folgende Befehl ist ein Beispiel für die psql-Verbindungszeichenfolge:

```shell
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> Vergewissern Sie sich, dass der Wert, den Sie an `sslrootcert` übergeben haben, mit dem Dateipfad für das von Ihnen gespeicherte Zertifikat übereinstimmt.

## <a name="tls-enforcement-in-azure-database-for-postgresql-single-server"></a>Durchsetzung von TLS in Azure Database for PostgreSQL (Einzelserver)

Azure Database for PostgreSQL (Einzelserver) unterstützt Verschlüsselung für Clients, die eine Verbindung mit dem Datenbankserver mithilfe von Transport Layer Security (TLS) herstellen. TLS ist ein Standardprotokoll der Branche, das sichere Netzwerkverbindungen zwischen dem Datenbankserver und Clientanwendungen gewährleistet, sodass Sie Konformitätsanforderungen einhalten können.

### <a name="tls-settings"></a>TLS-Einstellungen

Azure Database for PostgreSQL (Einzelserver) bietet die Möglichkeit, die TLS-Version für die Clientverbindungen vorzuschreiben. Um die TLS-Version zu erzwingen, verwenden Sie die Optionseinstellung **TLS-Mindestversion**. Für diese Optionseinstellung sind die folgenden Werte zulässig:

|  TLS-Mindesteinstellung             | Unterstützte Client-TLS-Version                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled (Standardwert) | Kein TLS erforderlich                      |
| TLS1_0                           | TLS 1.0, TLS 1.1, TLS 1.2 und höher |
| TLS1_1                           | TLS 1.1, TLS 1.2 und höher          |
| TLS1_2                           | TLS-Version 1.2 und höher           |


Wenn Sie diese Mindesteinstellung für die TLS-Version beispielsweise auf TLS 1.0 festlegen, bedeutet das, dass Ihr Server Verbindungen von Clients mithilfe von TLS 1.0, 1.1 und 1.2+ zulässt. Alternativ bedeutet die Festlegung auf 1.2, dass Sie nur Verbindungen von Clients mithilfe von TLS 1.2+ zulassen und alle Verbindungen mit TLS 1.0 und TLS 1.1 abgewiesen werden.

> [!Note] 
> Von Azure Database for PostgreSQL wird standardmäßig keine TLS-Mindestversion erzwungen (Einstellung `TLSEnforcementDisabled`).
>
> Wenn Sie eine TLS-Mindestversion erzwingen, kann diese Erzwingung später nicht mehr deaktiviert werden.

Informationen zum Festlegen der TLS-Einstellung für Ihren Azure Database for PostgreSQL (Einzelserver) finden Sie unter [Konfigurieren der TLS-Einstellung](howto-tls-configurations.md).

## <a name="cipher-support-by-azure-database-for-postgresql-single-server"></a>Unterstützung für Verschlüsselungsverfahren durch Azure Database for PostgreSQL – Einzelserver

Im Rahmen der SSL/TLS-Kommunikation werden die Verschlüsselungssammlungen überprüft. Nur Sammlungen, die mit dem Datenbankserver kommunizieren dürfen, werden zugelassen. Die Überprüfung der Verschlüsselungssammlungen wird in der [Gatewayschicht](concepts-connectivity-architecture.md#connectivity-architecture) gesteuert, nicht explizit im Knoten selbst. Wenn die Sammlungen keiner der unten aufgeführten Sammlungen entsprechen, werden eingehende Clientverbindungen abgelehnt.

### <a name="cipher-suite-supported"></a>Unterstützte Verschlüsselungssammlungen

*   TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
*   TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
*   TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
*   TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

## <a name="next-steps"></a>Nächste Schritte

Überprüfen verschiedener Anwendungskonnektivitätsoptionen gemäß [Datenverbindungsbibliotheken für Azure Database for PostgreSQL](concepts-connection-libraries.md).

- Informationen zum [Konfigurieren von TLS](howto-tls-configurations.md).