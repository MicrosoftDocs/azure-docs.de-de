---
title: Zertifikatrotation für Azure SQL-Datenbank und SQL Managed Instance
description: Hier erfahren Sie mehr über die bevorstehenden Änderungen an Stammzertifikaten im Zusammenhang mit Azure SQL-Datenbank und Azure SQL Managed Instance.
author: srdan-bozovic-msft
ms.author: srbozovi
ms.service: sql-db-mi
ms.subservice: service
ms.topic: conceptual
ms.date: 09/13/2020
ms.openlocfilehash: b0a10744d2b48fa620b48b731144222199f711c7
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92792530"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-sql-database--sql-managed-instance"></a>Grundlegendes zu den Änderungen im Zusammenhang mit der Stammzertifizierungsstelle für Azure SQL-Datenbank und SQL Managed Instance

Bei Azure SQL-Datenbank und SQL Managed Instance ändert sich das Stammzertifikat für die Clientanwendung oder den Clienttreiber mit SSL-Aktivierung, die bzw. den Sie zum Herstellen einer sicheren TDS-Verbindung verwenden. Für das [aktuelle Stammzertifikat](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) wird im Rahmen der Standardwartung und gemäß den Best Practices zum Thema Sicherheit der Ablauf auf den 26. Oktober 2020 festgelegt. In diesem Artikel erhalten Sie ausführlichere Informationen zu den bevorstehenden Änderungen, den betroffenen Ressourcen sowie den Schritten, die erforderlich sind, um sicherzustellen, dass die Konnektivität Ihrer Anwendung mit dem Datenbankserver bestehen bleibt.

## <a name="what-update-is-going-to-happen"></a>Welches Update wird durchgeführt?

Im [Browserforum für Zertifizierungsstellen](https://cabforum.org/) wurde jedoch kürzlich gemeldet, dass mehrere von Zertifizierungsstellenanbietern ausgestellte Zertifikate nicht konform sind.

Gemäß den Konformitätsanforderungen der Branche haben Zertifizierungsstellenanbieter damit begonnen, Zertifizierungsstellenzertifikate für nicht konforme Zertifizierungsstellen zu sperren, sodass Server Zertifikate verwenden müssen, die von konformen Zertifizierungsstellen ausgestellt und durch Zertifizierungsstellenzertifikate von diesen konformen Zertifizierungsstellen signiert wurden. Da Azure SQL-Datenbank und SQL Managed Instance derzeit eines dieser nicht konformen Zertifikate verwendet, die von Clientanwendungen zum Überprüfen der SSL-Verbindungen genutzt werden, müssen wir sicherstellen, dass entsprechende Maßnahmen ergriffen werden (siehe unten), um die potenziellen Auswirkungen auf Ihre SQL-Server in Azure zu minimieren.

Das neue Zertifikat wird ab dem 26. Oktober 2020 verwendet. Wenn Sie die vollständige Überprüfung für das Serverzertifikat durchführen, wenn Sie eine Verbindung von einem SQL-Client herstellen (TrustServerCertificate=true), müssen Sie sicherstellen, dass Ihr SQL-Client in der Lage wäre, das neue Stammzertifikat vor dem 26. Oktober 2020 zu überprüfen.

## <a name="how-do-i-know-if-my-application-might-be-affected"></a>Betroffene Anwendungen

Alle Anwendungen, die SSL/TLS verwenden und das Stammzertifikat überprüfen, müssen das Stammzertifikat aktualisieren, um eine Verbindung zu Azure SQL-Datenbank und SQL Managed Instance herstellen zu können. 

Wenn Sie SSL/TLS aktuell nicht verwenden, besteht keine Auswirkung auf die Verfügbarkeit Ihrer Anwendung. Sie können überprüfen, ob Ihre Clientanwendung versucht, das Stammzertifikat zu überprüfen, indem Sie sich die Verbindungszeichenfolge ansehen. Wenn TrustServerCertificate explizit auf TRUE festgelegt ist, sind Sie nicht betroffen.

Wenn Ihr Clienttreiber den Zertifikatspeicher des Betriebssystems verwendet, was bei den meisten Treibern der Fall ist, und wenn Ihr Betriebssystem regelmäßig gewartet wird, wirkt sich diese Änderung vermutlich nicht auf Sie aus, da das Stammzertifikat, das geändert wird, in Ihrem Zertifikatspeicher für vertrauenswürdige Stammzertifikate bereits verfügbar sein sollte. Suchen Sie nach Baltimore CyberDigiCert GlobalRoot G2, und überprüfen Sie, ob es vorhanden ist.

Wenn Ihr Clienttreiber einen Zertifikatspeicher mit lokalen Dateien nutzt, finden Sie unter [**Was muss ich tun, um die Konnektivität aufrechtzuerhalten?**](./ssl-root-certificate-expiring.md#what-do-i-need-to-do-to-maintain-connectivity) Informationen dazu, wie Sie verhindern, dass die Verfügbarkeit Ihrer Anwendung aufgrund unerwartet widerrufener Zertifikate unterbrochen wird, oder wie Sie ein widerrufenes Zertifikat aktualisieren.

## <a name="what-do-i-need-to-do-to-maintain-connectivity"></a>Was muss ich tun, um die Konnektivität aufrechtzuerhalten?

Führen Sie die unten angegebenen Schritte aus, um zu verhindern, dass die Verfügbarkeit Ihrer Anwendung aufgrund unerwartet widerrufener Zertifikate unterbrochen wird, oder um ein widerrufenes Zertifikat zu aktualisieren:

*   Laden Sie die Stammzertifizierungsstellen Baltimore CyberTrust Root und DigiCert GlobalRoot G2 über die folgenden Links herunter:
    *   https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem
    *   https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem

*   Generieren Sie einen kombinierten Zertifizierungsstellen-Zertifikatspeicher, in dem sowohl **BaltimoreCyberTrustRoot** als auch **DigiCertGlobalRootG2** enthalten sind.

## <a name="what-can-be-the-impact"></a>Mögliche Auswirkungen
Wenn Sie Serverzertifikate wie hier dokumentiert überprüfen, wird die Verfügbarkeit Ihrer Anwendung möglicherweise unterbrochen, da die Datenbank nicht erreichbar ist. Abhängig von Ihrer Anwendung erhalten Sie möglicherweise eine Vielzahl von Fehlermeldungen, einschließlich der folgenden:
*   Ungültiges Zertifikat/Widerrufenes Zertifikat
*   Timeout bei Verbindung
*   Fehler (sofern zutreffend)

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="if-i-am-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>Muss ich die Stammzertifizierungsstelle auch aktualisieren, wenn ich SSL/TLS nicht verwende?
Wenn Sie SSL/TLS nicht verwenden, sind keine Schritte erforderlich. Dennoch sollten Sie einen Plan festlegen, um mit der Verwendung der aktuellen TLS-Version zu beginnen, da für die nahe Zukunft eine TLS-Erzwingung vorgesehen ist.

### <a name="what-will-happen-if-i-do-not-update-the-root-certificate-before-october-26-2020"></a>Was geschieht, wenn ich das Stammzertifikat bis zum 26. Oktober 2020 nicht aktualisiere?
Wenn Sie das Stammzertifikat nicht vor dem 30. November 2020 aktualisieren, können Ihre Anwendungen, die über SSL/TLS eine Verbindung herstellen und die Überprüfung des Stammzertifikats durchführen, nicht mit Azure SQL-Datenbank und SQL Managed Instance kommunizieren. Außerdem treten für Ihre Anwendung Verbindungsprobleme im Zusammenhang mit Azure SQL-Datenbank und SQL Managed Instance auf.

### <a name="do-i-need-to-plan-a-maintenance-downtime-for-this-changebr"></a>Muss ich für diese Änderung eine Wartungsdowntime planen?<BR>
Nein. Da die Änderung hier nur auf der Clientseite erfolgt, um eine Verbindung mit dem Server herzustellen, ist für diese Änderung keine Wartungsdowntime erforderlich.

### <a name="what-if-i-cannot-get-a-scheduled-downtime-for-this-change-before-october-26-2020"></a>Was geschieht, wenn vor dem 26. Oktober 2020 keine geplante Downtime für diese Änderung möglich ist?
Da die Clients, die zum Herstellen einer Verbindung mit dem Server verwendet werden, die Zertifikatinformationen gemäß [diesem Abschnitt](./ssl-root-certificate-expiring.md#what-do-i-need-to-do-to-maintain-connectivity) aktualisieren müssen, ist in diesem Fall keine Downtime beim Server erforderlich.

### <a name="if-i-create-a-new-server-after-november-30-2020-will-i-be-impacted"></a>Bin ich betroffen, wenn ich nach dem 30. November 2020 einen neuen Server erstelle?
Für Server, die nach dem 26. Oktober 2020 erstellt werden, können Sie das neu ausgestellte Zertifikat für Ihre Anwendungen verwenden, um eine Verbindung mithilfe von SSL herzustellen.

### <a name="how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>Wie oft aktualisiert Microsoft seine Zertifikate, bzw. welche Ablaufrichtlinie gilt?
Diese von Azure SQL-Datenbank und SQL Managed Instance verwendeten Zertifikate werden von vertrauenswürdigen Zertifizierungsstellen (ZS) bereitgestellt. Die Unterstützung dieser Zertifikate in Azure SQL-Datenbank und SQL Managed Instance ist also an die Unterstützung dieser Zertifikate durch die ZS gebunden. Wie in diesem Fall kann es jedoch zu unvorhergesehenen Fehlern in diesen vordefinierten Zertifikaten kommen, die schnellstmöglich behoben werden müssen.

### <a name="if-i-am-using-read-replicas-do-i-need-to-perform-this-update-only-on-master-server-or-all-the-read-replicas"></a>Muss ich bei Verwendung von Lesereplikaten dieses Update nur auf dem Masterserver oder auch für alle Lesereplikate ausführen?
Da dieses Update eine clientseitige Änderung ist, müssen Sie die Änderungen auch für diese Clients anwenden, wenn der Client Daten vom Replikatserver gelesen hat. 

### <a name="do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>Gibt es eine serverseitige Abfrage, um zu überprüfen, ob SSL verwendet wird?
Da diese Konfiguration clientseitig ist, liegen auf Seite des Servers keine Informationen vor.

### <a name="what-if-i-have-further-questions"></a>Wie gehe ich vor, wenn ich weitere Fragen habe?
Wenn Sie über einen Supportplan verfügen und technische Hilfe benötigen, können Sie eine Anfrage an den Azure-Support einreichen. Wie das funktioniert, erfahren Sie unter [Erstellen einer Azure-Supportanfrage](../../azure-portal/supportability/how-to-create-azure-support-request.md).