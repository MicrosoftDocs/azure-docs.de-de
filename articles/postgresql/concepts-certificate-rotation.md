---
title: Rotation für Zertifikate für Azure Database for PostgreSQL-Einzelserver
description: Hier erfahren Sie mehr über die bevorstehenden Änderungen an Stammzertifikaten im Zusammenhang mit Azure Database for PostgreSQL-Einzelserver.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/02/2020
ms.openlocfilehash: 96720e156963a5fb542e72823a602aa2cc6a0ead
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93349000"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-database-for-postgresql-single-server"></a>Grundlegendes zu den Änderungen im Zusammenhang mit der Stammzertifizierungsstelle für Azure Database for PostgreSQL-Einzelserver

Bei Azure Database for PostgreSQL ändert sich das Stammzertifikat für die Clientanwendung oder den Clienttreiber mit SSL-Aktivierung, die bzw. den Sie zum [Herstellen einer Verbindung mit dem Datenbankserver](concepts-connectivity-architecture.md) verwenden. Für das zurzeit verfügbare Stammzertifikat wird im Rahmen der standardmäßigen Wartung und bewährten Sicherheitsmethoden der Ablauf auf den 15. Februar 2021 (15.02.2021) festgelegt. In diesem Artikel erhalten Sie ausführlichere Informationen zu den bevorstehenden Änderungen, den betroffenen Ressourcen sowie den Schritten, die erforderlich sind, um sicherzustellen, dass die Konnektivität Ihrer Anwendung mit dem Datenbankserver bestehen bleibt.

>[!NOTE]
> Basierend auf dem Feedback von Kunden haben wir die eingestellte Unterstützung des Stammzertifikats für unsere vorhandene Baltimore-Stammzertifizierungsstelle vom 26. Oktober 2020 bis zum 15. Februar 2021 verlängert. Wir hoffen, dass diese Verlängerung eine ausreichende Vorlaufzeit für unsere Benutzer zur Implementierung der Clientänderungen ermöglicht, wenn sie davon betroffen sind.

## <a name="what-update-is-going-to-happen"></a>Welches Update wird durchgeführt?

In einigen Fällen verwenden Anwendungen eine lokale Zertifikatdatei, die auf der Grundlage der Zertifikatdatei einer vertrauenswürdigen Zertifizierungsstelle (Certificate Authority, CA) generiert wurde, um eine sichere Verbindung herzustellen. Derzeit können Kunden nur [dieses vordefinierte Zertifikat](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) zum Herstellen einer Verbindung mit einem Azure Database for PostgreSQL-Server verwenden. Im [Zertifizierungsstellen-Browserforum](https://cabforum.org/) wurden jedoch kürzlich gemeldet, dass mehrere von Zertifizierungsstellenanbietern ausgestellte Zertifikate nicht konform sind.

Gemäß den Konformitätsanforderungen der Branche haben Zertifizierungsstellenanbieter damit begonnen, Zertifizierungsstellenzertifikate für nicht konforme Zertifizierungsstellen zu sperren. Daher müssen Server Zertifikate verwenden, die von konformen Zertifizierungsstellen ausgestellt und durch Zertifizierungsstellenzertifikate von diesen konformen Zertifizierungsstellen signiert wurden. Da Azure Database for PostgreSQL derzeit eines dieser nicht konformen Zertifikate verwendet, die von Clientanwendungen zum Überprüfen der SSL-Verbindungen genutzt werden, müssen wir sicherstellen, dass entsprechende Maßnahmen ergriffen werden (siehe unten), um die potenziellen Auswirkungen auf Ihre PostgreSQL-Server zu minimieren.

Das neue Zertifikat wird ab dem 15. Februar 2021 (15.02.2021) verwendet. Wenn Sie beim Herstellen einer Verbindung von einem PostgreSQL-Client entweder die Zertifizierungsstellenüberprüfung oder die vollständige Überprüfung des Serverzertifikats („sslmode=verify-ca“ bzw. „sslmode=verify-full“) verwenden, müssen Sie Ihre Anwendungskonfiguration vor dem 15. Februar 2021 (15.02.2021) aktualisieren.

## <a name="how-do-i-know-if-my-database-is-going-to-be-affected"></a>Wie erkenne ich, ob meine Datenbank betroffen ist?

Alle Anwendungen, die SSL/TLS verwenden und das Stammzertifikat überprüfen, müssen das Stammzertifikat aktualisieren. Durch Überprüfen der Verbindungszeichenfolge können Sie ermitteln, ob Ihre Verbindungen das Stammzertifikat überprüfen.
-    Wenn Ihre Verbindungszeichenfolge `sslmode=verify-ca` oder `sslmode=verify-full` enthält, müssen Sie das Zertifikat aktualisieren.
-    Wenn Ihre Verbindungszeichenfolge `sslmode=disable`, `sslmode=allow`, `sslmode=prefer` oder `sslmode=require` enthält, müssen Sie keine Zertifikate aktualisieren. 
-    Wenn in Ihrer Verbindungszeichenfolge „sslmode“ nicht angegeben ist, müssen Sie keine Zertifikat aktualisieren.

Wenn Sie einen Client verwenden, der die Verbindungszeichenfolge abstrahiert, lesen Sie die Dokumentation des Clients, um zu ermitteln, ob Zertifikate überprüft werden.

Informationen zur PostgreSQL-sslmode-Überprüfung finden Sie in den [Beschreibungen des SSL-Modus](https://www.postgresql.org/docs/11/libpq-ssl.html#ssl-mode-descriptions) in der PostgreSQL-Dokumentation.

Informationen dazu, wie Sie verhindern, dass die Verfügbarkeit Ihrer Anwendung aufgrund unerwartet widerrufener Zertifikate unterbrochen wird, oder wie Sie ein widerrufenes Zertifikat aktualisieren, finden Sie im Abschnitt [**Was muss ich tun, um die Konnektivität aufrechtzuerhalten?** ](concepts-certificate-rotation.md#what-do-i-need-to-do-to-maintain-connectivity).

## <a name="what-do-i-need-to-do-to-maintain-connectivity"></a>Was muss ich tun, um die Konnektivität aufrechtzuerhalten?

Führen Sie die unten angegebenen Schritte aus, um zu verhindern, dass die Verfügbarkeit Ihrer Anwendung aufgrund unerwartet widerrufener Zertifikate unterbrochen wird, oder um ein widerrufenes Zertifikat zu aktualisieren. Die Idee dahinter ist, eine neue *PEM* -Datei zu erstellen, die das aktuelle Zertifikat und das neue Zertifikat kombiniert. Während der SSL-Zertifikatüberprüfung wird einer der zulässigen Werte verwendet. Sehen Sie sich die folgenden Schritte an:

*   Laden Sie die Stammzertifizierungsstellen BaltimoreCyberTrustRoot und DigiCertGlobalRootG2 über die folgenden Links herunter:
    *   https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem
    *   https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem

*   Generieren Sie einen kombinierten Zertifizierungsstellen-Zertifikatspeicher, in dem sowohl **BaltimoreCyberTrustRoot** als auch **DigiCertGlobalRootG2** enthalten sind.
    *   Führen Sie für Java-Benutzer (PostgreSQL-JDBC) mit DefaultJavaSSLFactory Folgendes aus:

          ```console
          keytool -importcert -alias PostgreSQLServerCACert  -file D:\BaltimoreCyberTrustRoot.crt.pem  -keystore truststore -storepass password -noprompt
          ```

          ```console
          keytool -importcert -alias PostgreSQLServerCACert2  -file D:\DigiCertGlobalRootG2.crt.pem -keystore truststore -storepass password  -noprompt
          ```

          Ersetzen Sie dann die ursprüngliche Keystore-Datei durch die neu generierte Datei:
        *   System.setProperty("javax.net.ssl.trustStore","Pfad_zur_Truststore-Datei"); 
        *   System.setProperty("javax.net.ssl.trustStorePassword","Kennwort");
        
    *   Stellen Sie für .NET-Benutzer (Npgsql) unter Windows sicher, dass **Baltimore CyberTrust Root** und **DigiCert Global Root G2** als vertrauenswürdige Stammzertifizierungsstellen im Windows-Zertifikatspeicher vorhanden sind. Ist eines der Zertifikate nicht vorhanden, importieren Sie das fehlende Zertifikat.

        ![Azure Database for PostgreSQL-.NET-Zertifikat](media/overview/netconnecter-cert.png)

    *   Stellen Sie bei .NET-Benutzern (Npgsql) unter Linux, die SSL_CERT_DIR verwenden, sicher, dass **BaltimoreCyberTrustRoot** und **DigiCertGlobalRootG2** in dem durch SSL_CERT_DIR angegebenen Verzeichnis enthalten sind. Ist eines der Zertifikate nicht vorhanden, erstellen Sie die fehlende Zertifikatsdatei.

    *   Für andere PostgreSQL-Client Benutzer können Sie zwei ZS-Zertifikats Dateien wie in diesem Format weiter unten zusammenführen.

        </br>-----BEGIN CERTIFICATE-----
 </br>(Root CA1: BaltimoreCyberTrustRoot.crt.pem)
 </br>-----END CERTIFICATE-----
 </br>-----BEGIN CERTIFICATE-----
 </br>(Root CA2: DigiCertGlobalRootG2.crt.pem)
 </br>-----END CERTIFICATE-----

*   Ersetzen Sie die ursprüngliche PEM-Datei der Stammzertifizierungsstelle durch die kombinierte Datei der Stammzertifizierungsstelle, und starten Sie die Anwendung bzw. den Client neu.
*    Nachdem das neue Zertifikat auf der Serverseite bereitgestellt wurde, können Sie die PEM-Datei der Zertifizierungsstelle in Zukunft in „DigiCertGlobalRootG2.crt.pem“ ändern.

## <a name="what-can-be-the-impact-of-not-updating-the-certificate"></a>Welche Auswirkungen kann es haben, wenn das Zertifikat nicht aktualisiert wird?
Wenn Sie das Baltimore CyberTrust-Stammzertifikat zur Überprüfung der SSL-Verbindung mit Azure Database for PostgreSQL wie hier dokumentiert verwenden, wird die Verfügbarkeit Ihrer Anwendung möglicherweise unterbrochen, da die Datenbank nicht erreichbar ist. Abhängig von Ihrer Anwendung erhalten Sie möglicherweise eine Vielzahl von Fehlermeldungen, einschließlich der folgenden:
*    Ungültiges Zertifikat/Widerrufenes Zertifikat
*    Timeout bei Verbindung

> [!NOTE]
> Löschen oder ändern Sie das **Baltimore-Zertifikat** erst, nachdem die Zertifikatänderung vorgenommen wurde. Sobald die Änderung abgeschlossen ist, senden wir Ihnen eine Mitteilung,und danach können Sie das Baltimore-Zertifikat auf sichere Weise löschen. 

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

###    <a name="1-if-i-am-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>1. Muss ich die Stammzertifizierungsstelle auch aktualisieren, wenn ich SSL/TLS nicht verwende?
Wenn Sie SSL/TLS nicht verwenden, sind keine Schritte erforderlich. 

### <a name="2-if-i-am-using-ssltls-do-i-need-to-restart-my-database-server-to-update-the-root-ca"></a>2. Muss ich bei Verwendung von SSL/TLS meinen Datenbankserver neu starten, um die Stammzertifizierungsstelle zu aktualisieren?
Nein, der Datenbankserver muss nicht neu gestartet werden, damit das neue Zertifikat verwendet werden kann. Es handelt sich um eine clientseitige Änderung, und die eingehenden Clientverbindungen müssen das neue Zertifikat verwenden, um sicherzustellen, dass sie eine Verbindung mit dem Datenbankserver herstellen können.

### <a name="3-what-will-happen-if-i-do-not-update-the-root-certificate-before-february-15-2021-02152021"></a>3. Was geschieht, wenn ich das Stammzertifikat bis zum 15. Februar 2021 (15.02.2021) nicht aktualisiere?
Wenn Sie das Stammzertifikat vor dem 15. Februar 2021 (15.02.2021) nicht aktualisieren, können Ihre Anwendungen, die eine Verbindung über SSL/TLS herstellen und die Überprüfung für das Stammzertifikat durchführen, mit dem PostgreSQL-Datenbankserver nicht mehr kommunizieren. Außerdem treten bei der Anwendung Verbindungsprobleme im Zusammenhang mit Ihrem PostgreSQL-Datenbankserver auf.

### <a name="4-what-is-the-impact-if-using-app-service-with-azure-database-for-postgresql"></a>4. Welche Auswirkungen hat es, wenn App Service mit Azure Database for PostgreSQL verwendet wird?
Für Azure App Service-Instanzen, die eine Verbindung mit Azure Database for PostgreSQL herstellen, gibt es zwei mögliche Szenarien, je nachdem, wie Sie SSL mit Ihrer Anwendung verwenden.
*   Das neue Zertifikat wurde App Service auf Plattformebene hinzugefügt. Wenn Sie die SSL-Zertifikate verwenden, die in der App Service-Plattform in Ihrer Anwendung enthalten sind, ist keine Aktion erforderlich.
*   Wenn Sie den Pfad zur SSL-Zertifikatsdatei explizit in Ihren Code einschließen, müssen Sie das neue Zertifikat herunterladen und den Code so aktualisieren, dass das neue Zertifikat verwendet wird. Ein gutes Beispiel für dieses Szenario: Wenn Sie benutzerdefinierte Container in App Service so verwenden, wie in der [App Service-Dokumentation](../app-service/tutorial-multi-container-app.md#configure-database-variables-in-wordpress) zu lesen ist.

### <a name="5-what-is-the-impact-if-using-azure-kubernetes-services-aks-with-azure-database-for-postgresql"></a>5. Welche Auswirkungen hat es, wenn Azure Kubernetes Service (AKS) mit Azure Database for PostgreSQL verwendet wird?
Wenn Sie versuchen, mithilfe von Azure Kubernetes Service (AKS) eine Verbindung mit Azure Database for PostgreSQL herzustellen, ähnelt dies dem Zugriff über eine dedizierte Kundenhostumgebung. Die entsprechenden Schritte finden Sie [hier](../aks/ingress-own-tls.md).

### <a name="6-what-is-the-impact-if-using-azure-data-factory-to-connect-to-azure-database-for-postgresql"></a>6. Welche Auswirkungen hat es, wenn Azure Data Factory zum Herstellen einer Verbindung mit Azure Database for PostgreSQL verwendet wird?
Ein Connector, der Azure Integration Runtime verwendet, nutzt Zertifikate im Windows-Zertifikatspeicher in der von Azure gehosteten Umgebung. Diese Zertifikate sind bereits mit den neu angewendeten Zertifikaten kompatibel, daher ist keine Aktion erforderlich.

Bei einem Connector, der die selbstgehostete Integration Runtime verwendet, für die Sie den Pfad zur SSL-Zertifikatsdatei explizit in die Verbindungszeichenfolge einschließen, müssen Sie das [neue Zertifikat](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) herunterladen und die Verbindungszeichenfolge aktualisieren, damit es verwendet werden kann.

### <a name="7-do-i-need-to-plan-a-database-server-maintenance-downtime-for-this-change"></a>7. Muss ich für diese Änderung eine Wartungsdowntime für den Datenbankserver planen?
Nein. Da die Änderung hier nur auf der Clientseite erfolgt, um eine Verbindung mit dem Datenbankserver herzustellen, ist für diese Änderung keine Wartungsdowntime beim Datenbankserver erforderlich.

### <a name="8--what-if-i-cannot-get-a-scheduled-downtime-for-this-change-before-february-15-2021-02152021"></a>8.  Was geschieht, wenn vor dem 15. Februar 2021 (15.02.2021) keine geplante Downtime für diese Änderung möglich ist?
Da die Clients, die zum Herstellen einer Verbindung mit dem Server verwendet werden, die Zertifikatinformationen gemäß [diesem Abschnitt](./concepts-certificate-rotation.md#what-do-i-need-to-do-to-maintain-connectivity) aktualisieren müssen, ist in diesem Fall keine Downtime beim Server erforderlich.

### <a name="9-if-i-create-a-new-server-after-february-15-2021-02152021-will-i-be-impacted"></a>9. Bin ich betroffen, wenn ich nach dem 15. Februar 2021 (15.02.2021) einen neuen Server erstelle?
Bei Servern, die nach dem 15. Februar 2021 (15.02.2021) erstellt werden, können Sie das neu ausgestellte Zertifikat für Ihre Anwendungen nutzen, um eine Verbindung mithilfe von SSL herzustellen.

###    <a name="10-how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>10. Wie oft aktualisiert Microsoft seine Zertifikate bzw. welche Ablaufrichtlinie gilt?
Diese von Azure Database for PostgreSQL verwendeten Zertifikate werden von vertrauenswürdigen Zertifizierungsstellen (ZS) bereitgestellt. Die Unterstützung dieser Zertifikate in Azure Database for PostgreSQL ist also an die Unterstützung dieser Zertifikate durch die ZS gebunden. Wie in diesem Fall kann es jedoch zu unvorhergesehenen Fehlern in diesen vordefinierten Zertifikaten kommen, die schnellstmöglich behoben werden müssen.

###    <a name="11-if-i-am-using-read-replicas-do-i-need-to-perform-this-update-only-on-the-primary-server-or-the-read-replicas"></a>11. Muss ich bei Verwendung von Lesereplikaten dieses Update nur auf dem primären Server oder für die gelesenen Replikate ausführen?
Da dieses Update eine clientseitige Änderung ist, müssen Sie die Änderungen auch für diese Clients anwenden, wenn der Client Daten vom Replikatserver gelesen hat. 

### <a name="12-do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>12. Gibt es eine serverseitige Abfrage, um zu überprüfen, ob SSL verwendet wird?
Um zu überprüfen, ob Sie eine SSL-Verbindung zum Herstellen einer Verbindung mit dem Server verwenden, lesen Sie die Informationen unter [Überprüfen der SSL-Verbindung](concepts-ssl-connection-security.md#applications-that-require-certificate-verification-for-tls-connectivity).

### <a name="13-is-there-an-action-needed-if-i-already-have-the-digicertglobalrootg2-in-my-certificate-file"></a>13. Ist eine Aktion erforderlich, wenn sich DigiCertGlobalRootG2 bereits in meiner Zertifikatsdatei befindet?
Nein Wenn in Ihrer Zertifikatsdatei **DigiCertGlobalRootG2** bereits enthalten ist, ist keine Aktion erforderlich.

### <a name="14-what-is-you-are-using-docker-image-of-pgbouncer-sidecar-provided-by-microsoft"></a>14. Was ist, wenn Sie ein Docker-Image des von Microsoft bereitgestellten PgBouncer Sidecar verwenden?
Ein neues Docker-Image, das sowohl [**Baltimore**](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) als auch [**DigiCert**](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) unterstützt, wird [hier](https://hub.docker.com/_/microsoft-azure-oss-db-tools-pgbouncer-sidecar) veröffentlicht (neuestes Tag). Sie können dieses neue Image abrufen, um eine Unterbrechung der Konnektivität ab dem 15. Februar 2021 zu vermeiden. 

###    <a name="15-what-if-i-have-further-questions"></a>15. Wie gehe ich vor, wenn ich weitere Fragen habe?
Bei Fragen können Sie Antworten von Communityexperten auf [Microsoft Q&A](mailto:AzureDatabaseforPostgreSQL@service.microsoft.com) erhalten. Wenn Sie über einen Supportplan verfügen und technische Hilfe benötigen, [kontaktieren Sie uns](mailto:AzureDatabaseforPostgreSQL@service.microsoft.com).
