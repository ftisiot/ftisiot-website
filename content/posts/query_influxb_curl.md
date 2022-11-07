---
title: 'How to query remote InfluxDB via cURL'
date: "2022-11-07T10:15:10+01:00"
url: "/posts/query_influxb_curl"
description: "An example on how to query a remote server InfluxDB via cURL"
tldr: "An example on how to query a remote server InfluxDB via cURL"
image: "/images/influxb_query.png"
credit: "https://twitter.com/ftisiot/"
thumbnail: "/images/influxb_query.png"
categories:
- InfluxDB
- Query
---

Sometimes you want to query a remote InfluxDB server to understand the data in it. This can be done via cURL as explained in the [InfluxDB docs](https://docs.influxdata.com/influxdb/v1.8/guides/query_data/)

<!--more-->

## Parameters

* `HOSTNAME`: Database hostname
* `PORT`: Database port
* `PROTOCOL`: Protocol to connect to the database `HTTPS`, `HTTP`
* `USERNAME`: Username to connect to the database
* `PASSWORD`: Password to connect to the database
* `DB_NAME`: Database name
* `QUERY_STR`: Query string to be exectuted. The query string needs to be properly escaped, e.g. `SELECT \"ts_ms\" FROM \"test_measurement\"`


## cURL Query Code

Execute the following by replacing the parameters above

```
curl -G 'https://<USERNAME>:<PASSWORD>@<HOSTNAME>:<PORT>/query?pretty=true' \
    --data-urlencode "db=<DB_NAME>" \
    --data-urlencode "q=<QUERY_STR>"
```

As example to get the `SELECT \"ts_ms\" FROM \"test_measurement\"` you can run:

```
curl -G 'https://<USERNAME>:<PASSWORD>@<HOSTNAME>:<PORT>/query?pretty=true' \
    --data-urlencode "db=<DB_NAME>" \
    --data-urlencode "q=SELECT \"ts_ms\" FROM \"test_measurement\""
```

Results:

```
{
    "results": [
        {
            "statement_id": 0,
            "series": [
                {
                    "name": "TEST",
                    "columns": [
                        "time",
                        "ts_ms"
                    ],
                    "values": [
                        [
                            "2022-11-07T08:50:20.772317361Z",
                            1667809412536
                        ],
                        [
                            "2022-11-07T08:50:20.789690226Z",
                            1667809412547
                        ],
                        [
                            "2022-11-07T08:50:20.79052872Z",
                            1667809412548
                        ],
                        [
                            "2022-11-07T08:51:53.165095297Z",
                            1667811112824
                        ],
                        ]
                }
            ]
        }
    ]
}
```
