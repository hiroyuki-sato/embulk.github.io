---
layout: posts
title: "Scheduled bulk data loading to Elasticsearch + Kibana 5 from CSV files"
date: 2017-03-29 17:23:51 +0900
last_modified_at: 2020-07-03 16:45:48 +0900
author: "hiroyuki-sato"
---

This article shows how to:

* Bulk load CSV files to Elasticsearch.
* Visualize the data with Kibana interactively.
* Schedule the data loading every hour using cron.

This guide assumes you are using Ubuntu 16.10 Precise or macOS.

### Setup Elasticsearch and Kibana 5

#### Step 1. Download and start Elasticsearch.

You can find releases from the [Elasticsearch website](https://www.elastic.co/downloads/elasticsearch>).
For the smallest setup, you can unzip the package and run `./bin/elasticsearch` command:

```
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.3.0.zip
unzip elasticsearch-5.3.0.zip
cd elasticsearch-5.3.0
./bin/elasticsearch
```

#### Step 2. Download and unzip Kibana:

You can find releases from the [Kibana website](https://www.elastic.co/downloads/kibana>). Open a new console and run following commands:

```
wget https://artifacts.elastic.co/downloads/kibana/kibana-5.3.0-linux-x86_64.tar.gz
tar zxvf kibana-5.3.0-linux-x86_64.tar.gz
cd kibana-5.3.0-linux-x86_64
./bin/kibana
```

Note: If you're using macOS, [https://artifacts.elastic.co/downloads/kibana/kibana-5.3.0-darwin-x86_64.tar.gz](https://artifacts.elastic.co/downloads/kibana/kibana-5.3.0-darwin-x86_64.tar.gz) is the URL to download.

Now Elasticsearch and Kibana started. Open [http://localhost:5601/](http://localhost:5601/) using your browser to see the Kibana's graphical interface.

### Setup Embulk

#### Step 1. Download Embulk binary:

You can find the latest Embulk binary from [GitHub Releases](https://github.com/embulk/embulk/releases). Because Embulk is a single executable binary, you can simply download it to `~/.embulk/bin` directory and set executable flag as following:

```
curl --create-dirs -o ~/.embulk/bin/embulk -L "https://dl.embulk.org/embulk-latest.jar"
chmod +x ~/.embulk/bin/embulk
echo 'export PATH="$HOME/.embulk/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

#### Step 2. Install Elasticsearch plugin

You also need Elasticsearch plugin for Embulk. You can install the plugin with this command:

```
embulk gem install embulk-output-elasticsearch
```

Embulk includes CSV file reader in itself. Now everything is ready to use.

### Loading a CSV file

Assuming you have a CSV files at `./mydata/csv/` directory. If you don't have CSV files, you can create ones using `embulk example ./mydata` command.

Create this configuration file and save as `seed.yml`:

```
in:
  type: file
  path_prefix: ./mydata/csv/
out:
  type: elasticsearch
  index: embulk
  index_type: embulk
  nodes:
    - host: localhost
```

In fact, this configuration lacks some important information. However, embulk guesses the other information. So, next step is to order embulk to guess them:

```
embulk guess ./mydata/seed.yml -o config.yml
```

The generated config.yml file should include complete information as following:

```
in:
  type: file
  path_prefix: ./mydata/csv/
  decoders:
  - {type: gzip}
  parser:
    charset: UTF-8
    newline: CRLF
    type: csv
    delimiter: ','
    quote: '"'
    escape: ''
    null_string: 'NULL'
    skip_header_lines: 1
    columns:
    - {name: id, type: long}
    - {name: account, type: long}
    - {name: time, type: timestamp, format: '%Y-%m-%d %H:%M:%S'}
    - {name: purchase, type: timestamp, format: '%Y%m%d'}
    - {name: comment, type: string}
out:
  type: elasticsearch
  index: embulk
  index_type: embulk
  nodes:
  - {host: localhost}
```

Note: If the CSV file contains timestamp in local time zone, set `default_timezone` parameter to parser config as following (since time zone is assumed as UTC by default).

```
  parser:
    default_timezone: 'Asia/Tokyo'
```

Now, you can run the bulk loading:

```
embulk run config.yml -c diff.yml
```

### Scheduling loading by cron

At the last step, you ran embulk command with `-c diff.yml` file. The `diff.yml` file should include a parameter named `last_path`:

```
in: {last_path: mydata/csv/sample_01.csv.gz}
out: {}
```

With this configuration, embulk loads the files newer than this file in alphabetical order.

For example, if you create `./mydata/csv/sample_02.csv.gz` file, embulk skips `sample_01.csv.gz` file and loads `sample_02.csv.gz` only next time. And the next `diff.yml` file has `last_path: mydata/csv/sample_02.csv.gz` for the next next execution.

So, if you want to loads newly created files every day, you can setup this cron schedule:

```
0 * * * * embulk run /path/to/config.yml -c /path/to/diff.yml
```
