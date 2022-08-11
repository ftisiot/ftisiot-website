---
title: 'Define a PostgreSQL database connection in JSON and import it in PGAdmin 4'
date: "2022-08-11T14:47:17+02:00"
url: "/posts/create_json_connection_pg_admin4"
description: "How to define a PostgreSQL database connection in JSON and import it in PGAdmin 4"
tldr: ""
image: "/images/2022/pgadmin_json.png"
credit: ""
thumbnail: "/images/2022/pgadmin_json_thumbnail.png"
categories:
- PGAdmin4
- PostgreSQL
- Connections
- Import
- JSON
---

Yesterday I updated my PGAdmin 4 and found an interesting option to manage database connection via the **Tools** -> **Import/Export Servers**, allowing to pass almost all the parameters required (it doesn't export/import the password) to connect to a database via JSON files.

The question is: how can I define a connection in JSON so it can pick it up and imported in PGAdmin 4? It's actually pretty simple, all you have to do is, substitute all the parameters in the following example

```
{
    "Servers": {
        "1": {
            "Name": "CONNECTION_NAME",
            "Group": "GROUP_NAME",
            "Host": "HOSTNAME",
            "Port": PORT,
            "MaintenanceDB": "DATABASE_NAME",
            "Username": "USERNAME",
            "Password": "PASSWORD",
            "SSLMode": "SSLMODE"
        }
    }
}
```

Where

* `CONNECTION_NAME` is the logical name you want to give to the connection
* `GROUP_NAME` is the logical group name that you can use to group more connections
* `HOSTNAME`, `PORT`, `DATABASE_NAME`, `USERNAME`, `PASSWORD`, `SSLMODE` are the usual parameters to connect to the database.

You can also add more than one database connection in the mix, E.g.

```
{
    "Servers": {
        "1": {
            "Name": "CONNECTION_NAME1",
            "Group": "GROUP_NAME1",
            "Host": "HOSTNAME1",
            "Port": PORT1,
            "MaintenanceDB": "DATABASE_NAME1",
            "Username": "USERNAME1",
            "Password": "PASSWORD1",
            "SSLMode": "SSLMODE1"
        },
        "2": {
            "Name": "CONNECTION_NAME2",
            "Group": "GROUP_NAME2",
            "Host": "HOSTNAME2",
            "Port": PORT2,
            "MaintenanceDB": "DATABASE_NAME2",
            "Username": "USERNAME2",
            "Password": "PASSWORD2",
            "SSLMode": "SSLMODE2"
        }
    }
}
```

## Import the JSON definition in PGAdmin 4

Once you have the above config stored in a JSON file, you can then import in PGAdmin4 with:

* select **Tools** -> **Import/Export Servers**
* select **Import**
* select the connections you want to import and click on **Next**
* click on **Finish**

And done! you imported all your JSON connections