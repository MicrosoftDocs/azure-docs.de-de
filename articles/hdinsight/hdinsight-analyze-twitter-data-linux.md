---
title: Analysieren von Twitter-Daten mit Apache Hive – Azure HDInsight
description: Erfahren Sie, wie Sie Apache Hive und Apache Hadoop in HDInsight verwenden, um Twitter-Rohdaten in eine durchsuchbare Hive-Tabelle zu transformieren.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 12/16/2019
ms.openlocfilehash: fe511ed2d6b724c1215f9986c9d6c50aae076935
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95971908"
---
# <a name="analyze-twitter-data-using-apache-hive-and-apache-hadoop-on-hdinsight"></a>Analysieren von Twitter-Daten mit Apache Hive und Apache Hadoop in HDInsight

Hier erfahren Sie, wie Sie Twitter-Daten mit [Apache Hive](https://hive.apache.org/) verarbeiten. Als Ergebnis erhalten Sie eine Liste der Twitter-Benutzer, die die meisten Tweets mit einem bestimmten Begriff gesendet haben.

> [!IMPORTANT]  
> Die Schritte in diesem Dokument wurden in HDInsight 3.6 getestet.

## <a name="get-the-data"></a>Abrufen von Daten

Twitter ermöglicht das Abrufen der Daten für jeden Tweet als JavaScript Object Notation-Dokument (JSON) über eine REST-API. [OAuth](https://oauth.net) ist für die Authentifizierung gegenüber der API erforderlich.

### <a name="create-a-twitter-application"></a>Erstellen einer Twitter-Anwendung

1. Melden Sie sich in einem Webbrowser bei [https://developer.twitter.com/apps/](https://developer.twitter.com/apps/) an. Wählen Sie den Link **Registriere Dich jetzt** aus, wenn Sie noch kein Twitter-Konto haben.

2. Wählen Sie **Create New App** (Neue App erstellen) aus.

3. Geben Sie **Name**, **Description** und **Website** ein. Für das Feld **Website** können Sie eine URL erfinden. Die folgende Tabelle zeigt einige mögliche Beispielwerte:

   | Feld | Wert |
   |--- |--- |
   | Name |MyHDInsightApp |
   | BESCHREIBUNG |MyHDInsightApp |
   | Website |`https://www.myhdinsightapp.com` |

4. Wählen Sie **Yes, I agree** (Ja, ich stimme zu) und dann **Create your Twitter application** (Erstellen Sie Ihre Twitter-Anwendung) aus.

5. Wählen Sie die Registerkarte **Permissions** (Berechtigungen) aus. Die Standardberechtigung ist **Read only**.

6. Wählen Sie die Registerkarte **Keys and Access Tokens** .

7. Wählen Sie **Create my access token** (Erstellen meines Zugriffstokens) aus.

8. Wählen Sie oben rechts auf der Seite **Test OAuth** (OAuth testen) aus.

9. Notieren Sie **consumer key**, **Consumer secret**, **Access token** und **Access token secret**.

### <a name="download-tweets"></a>Herunterladen von Tweets

Mit dem folgenden Python-Code werden 10.000 Tweets von Twitter heruntergeladen und in einer Datei mit dem Namen **tweets.txt** gespeichert.

> [!NOTE]  
> Die folgenden Schritte werden auf dem HDInsight-Cluster ausgeführt, da Python bereits installiert ist.

1. Verwenden Sie einen [ssh-Befehl](./hdinsight-hadoop-linux-use-ssh-unix.md) zum Herstellen der Verbindung mit dem Cluster. Bearbeiten Sie den folgenden Befehl, indem Sie CLUSTERNAME durch den Namen Ihres Clusters ersetzen, und geben Sie den Befehl dann ein:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Verwenden Sie die folgenden Befehle, um [Tweepy](https://www.tweepy.org/), [ProgressBar](https://pypi.python.org/pypi/progressbar/2.2) und andere erforderliche Pakete zu installieren:

   ```bash
   sudo apt install python-dev libffi-dev libssl-dev
   sudo apt remove python-openssl
   python -m pip install virtualenv
   mkdir gettweets
   cd gettweets
   virtualenv gettweets
   source gettweets/bin/activate
   pip install tweepy progressbar pyOpenSSL requests[security]
   ```

1. Verwenden Sie den folgenden Befehl, um eine Datei mit dem Namen **gettweets.py** zu erstellen:

   ```bash
   nano gettweets.py
   ```

1. Bearbeiten Sie den folgenden Code, indem Sie `Your consumer secret`, `Your consumer key`, `Your access token` und `Your access token secret` durch die entsprechenden Informationen aus Ihrer Twitter-Anwendung ersetzen. Fügen Sie dann den bearbeiteten Code als Inhalt der Datei **gettweets.py** ein.

   ```python
   #!/usr/bin/python

   from tweepy import Stream, OAuthHandler
   from tweepy.streaming import StreamListener
   from progressbar import ProgressBar, Percentage, Bar
   import json
   import sys

   #Twitter app information
   consumer_secret='Your consumer secret'
   consumer_key='Your consumer key'
   access_token='Your access token'
   access_token_secret='Your access token secret'

   #The number of tweets we want to get
   max_tweets=100

   #Create the listener class that receives and saves tweets
   class listener(StreamListener):
       #On init, set the counter to zero and create a progress bar
       def __init__(self, api=None):
           self.num_tweets = 0
           self.pbar = ProgressBar(widgets=[Percentage(), Bar()], maxval=max_tweets).start()

       #When data is received, do this
       def on_data(self, data):
           #Append the tweet to the 'tweets.txt' file
           with open('tweets.txt', 'a') as tweet_file:
               tweet_file.write(data)
               #Increment the number of tweets
               self.num_tweets += 1
               #Check to see if we have hit max_tweets and exit if so
               if self.num_tweets >= max_tweets:
                   self.pbar.finish()
                   sys.exit(0)
               else:
                   #increment the progress bar
                   self.pbar.update(self.num_tweets)
           return True

       #Handle any errors that may occur
       def on_error(self, status):
           print status

   #Get the OAuth token
   auth = OAuthHandler(consumer_key, consumer_secret)
   auth.set_access_token(access_token, access_token_secret)
   #Use the listener class for stream processing
   twitterStream = Stream(auth, listener())
   #Filter for these topics
   twitterStream.filter(track=["azure","cloud","hdinsight"])
   ```

    > [!TIP]  
    > Passen Sie den Themenfilter in der letzten Zeile zum Nachverfolgen von beliebten Schlüsselwörtern an. Verwenden Sie Schlüsselwörter, die zum Zeitpunkt der Skriptausführung häufig verwendet werden, um eine schnellere Erfassung von Daten zu ermöglichen.

1. Drücken Sie zum Speichern der Datei **STRG+X** und anschließend **Y**

1. Verwenden Sie den folgenden Befehl, um die Datei auszuführen und Tweets herunterzuladen:

    ```bash
    python gettweets.py
    ```

    Eine Statusanzeige wird eingeblendet. Sie zählt beim Herunterladen der Tweets auf 100 %.

   > [!NOTE]  
   > Wenn es lange dauert, bis in der Statusanzeige ein Fortschritt zu erkennen ist, sollten Sie den Filter zum Nachverfolgen von Trendthemen ändern. Wenn es viele Tweets zu dem Thema im Filter gibt, kommen die benötigten 100 Tweets sehr schnell zusammen.

### <a name="upload-the-data"></a>Hochladen der Daten

Laden Sie die Daten mit den folgenden Befehlen in HDInsight Storage hoch:

```bash
hdfs dfs -mkdir -p /tutorials/twitter/data
hdfs dfs -put tweets.txt /tutorials/twitter/data/tweets.txt
```

Mit diesen Befehlen werden die Daten an einem Speicherort gespeichert, auf den alle Knoten im Cluster zugreifen können.

## <a name="run-the-hiveql-job"></a>Ausführen des HiveQL-Auftrags

1. Verwenden Sie den folgenden Befehl, um eine Datei mit [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)-Anweisungen zu erstellen:

   ```bash
   nano twitter.hql
   ```

    Verwenden Sie als Inhalt der Datei den folgenden Text:

   ```hiveql
   set hive.exec.dynamic.partition = true;
   set hive.exec.dynamic.partition.mode = nonstrict;
   -- Drop table, if it exists
   DROP TABLE tweets_raw;
   -- Create it, pointing toward the tweets logged from Twitter
   CREATE EXTERNAL TABLE tweets_raw (
       json_response STRING
   )
   STORED AS TEXTFILE LOCATION '/tutorials/twitter/data';
   -- Drop and recreate the destination table
   DROP TABLE tweets;
   CREATE TABLE tweets
   (
       id BIGINT,
       created_at STRING,
       created_at_date STRING,
       created_at_year STRING,
       created_at_month STRING,
       created_at_day STRING,
       created_at_time STRING,
       in_reply_to_user_id_str STRING,
       text STRING,
       contributors STRING,
       retweeted STRING,
       truncated STRING,
       coordinates STRING,
       source STRING,
       retweet_count INT,
       url STRING,
       hashtags array<STRING>,
       user_mentions array<STRING>,
       first_hashtag STRING,
       first_user_mention STRING,
       screen_name STRING,
       name STRING,
       followers_count INT,
       listed_count INT,
       friends_count INT,
       lang STRING,
       user_location STRING,
       time_zone STRING,
       profile_image_url STRING,
       json_response STRING
   );
   -- Select tweets from the imported data, parse the JSON,
   -- and insert into the tweets table
   FROM tweets_raw
   INSERT OVERWRITE TABLE tweets
   SELECT
       cast(get_json_object(json_response, '$.id_str') as BIGINT),
       get_json_object(json_response, '$.created_at'),
       concat(substr (get_json_object(json_response, '$.created_at'),1,10),' ',
       substr (get_json_object(json_response, '$.created_at'),27,4)),
       substr (get_json_object(json_response, '$.created_at'),27,4),
       case substr (get_json_object(json_response,    '$.created_at'),5,3)
           when "Jan" then "01"
           when "Feb" then "02"
           when "Mar" then "03"
           when "Apr" then "04"
           when "May" then "05"
           when "Jun" then "06"
           when "Jul" then "07"
           when "Aug" then "08"
           when "Sep" then "09"
           when "Oct" then "10"
           when "Nov" then "11"
           when "Dec" then "12" end,
       substr (get_json_object(json_response, '$.created_at'),9,2),
       substr (get_json_object(json_response, '$.created_at'),12,8),
       get_json_object(json_response, '$.in_reply_to_user_id_str'),
       get_json_object(json_response, '$.text'),
       get_json_object(json_response, '$.contributors'),
       get_json_object(json_response, '$.retweeted'),
       get_json_object(json_response, '$.truncated'),
       get_json_object(json_response, '$.coordinates'),
       get_json_object(json_response, '$.source'),
       cast (get_json_object(json_response, '$.retweet_count') as INT),
       get_json_object(json_response, '$.entities.display_url'),
       array(
           trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[1].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[2].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[3].text'))),
           trim(lower(get_json_object(json_response, '$.entities.hashtags[4].text')))),
       array(
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[1].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[2].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[3].screen_name'))),
           trim(lower(get_json_object(json_response, '$.entities.user_mentions[4].screen_name')))),
       trim(lower(get_json_object(json_response, '$.entities.hashtags[0].text'))),
       trim(lower(get_json_object(json_response, '$.entities.user_mentions[0].screen_name'))),
       get_json_object(json_response, '$.user.screen_name'),
       get_json_object(json_response, '$.user.name'),
       cast (get_json_object(json_response, '$.user.followers_count') as INT),
       cast (get_json_object(json_response, '$.user.listed_count') as INT),
       cast (get_json_object(json_response, '$.user.friends_count') as INT),
       get_json_object(json_response, '$.user.lang'),
       get_json_object(json_response, '$.user.location'),
       get_json_object(json_response, '$.user.time_zone'),
       get_json_object(json_response, '$.user.profile_image_url'),
       json_response
   WHERE (length(json_response) > 500);
   ```

1. Drücken Sie zum Speichern der Datei **STRG+X** und anschließend **Y**.

1. Verwenden Sie den folgenden Befehl, um den in der Datei enthaltenen HiveQL-Auftrag auszuführen:

   ```bash
   beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i twitter.hql
   ```

    Mit diesem Befehl wird die Datei **twitter.hql** ausgeführt. Nach Abschluss der Abfrage wird die Eingabeaufforderung `jdbc:hive2//localhost:10001/>` angezeigt.

1. Verwenden Sie die folgende Abfrage an der Beeline-Eingabeaufforderung, um sicherzustellen, dass die Daten importiert wurden:

   ```hiveql
   SELECT name, screen_name, count(1) as cc
   FROM tweets
   WHERE text like "%Azure%"
   GROUP BY name,screen_name
   ORDER BY cc DESC LIMIT 10;
   ```

    Bei dieser Abfrage werden maximal 10 Tweets zurückgegeben, die den Begriff **Azure** im Meldungstext enthalten.

    > [!NOTE]  
    > Wenn Sie den Filter im Skript `gettweets.py` geändert haben, ersetzen Sie **Azure** durch einen der von Ihnen verwendeten Filter.

## <a name="next-steps"></a>Nächste Schritte

Sie haben gelernt, wie Sie ein unstrukturiertes JSON-Dataset in eine strukturierte [Apache Hive](https://hive.apache.org/)-Tabelle umwandeln. Weitere Informationen zu Hive in HDInsight finden Sie in den folgenden Artikeln:

* [Erste Schritte mit HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Analysieren von Daten zu Flugverspätungen mit HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)