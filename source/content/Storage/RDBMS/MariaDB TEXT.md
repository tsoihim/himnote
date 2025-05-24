## **Description**
A `TEXT` column with a maximum length of `65,535` (`216 - 1`) characters.
Each `TEXT` value is stored using a two-byte length prefix that indicates the number of bytes in the value. If you need a bigger storage, consider using [MEDIUMTEXT](https://mariadb.com/kb/en/mediumtext/) instead.
An optional length `M` can be given for this type. If this is done, MariaDB creates the column as the smallest `TEXT` type large enough to hold values `M` characters long.

## **Indexing**
A [unique index](https://mariadb.com/kb/en/getting-started-with-indexes/#unique-index) can be created on a `TEXT` column. This was not possible prior to [MariaDB 10.4](https://mariadb.com/kb/en/what-is-mariadb-104/)
Internally, this uses hash indexing to quickly check the values and if a hash collision is found, the actual stored values are compared in order to retain the uniqueness.

## **Difference between VARCHAR and TEXT**
- [VARCHAR](https://mariadb.com/kb/en/varchar/) columns can be fully indexed. `TEXT` columns can only be indexed over a specified length.
- Using TEXT or [BLOB](https://mariadb.com/kb/en/blob/) in a [SELECT](https://mariadb.com/kb/en/select/) query that uses temporary tables for storing intermediate results will force the temporary table to be disk based (using the [Aria storage engine](https://mariadb.com/kb/en/aria-storage-engine/) instead of the [memory storage engine](https://mariadb.com/kb/en/memory-storage-engine/), which is a bit slower. This is not that bad as the [Aria storage engine](https://mariadb.com/kb/en/aria-storage-engine/) caches the rows in memory. To get the benefit of this, one should ensure that the [aria_pagecache_buffer_size](https://mariadb.com/kb/en/aria-system-variables/#aria_pagecache_buffer_size) variable is big enough to hold most of the row and index data for temporary tables.

## Example
```
CREATE TABLE TB_LEGACY_MODE_SWITCH # DDL
(
    ID                  BIGINT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    MGMT_IP             VARCHAR(100) NOT NULL,
    DPID                VARCHAR(100) NOT NULL,
    PREV_CONF           TEXT,
    CREATED_TIME        DATETIME  NULL,
    UPDATED_TIME        DATETIME  NULL,
    CONSTRAINT UNIQUE_MGMT_IP UNIQUE (MGMT_IP),
    CONSTRAINT UNIQUE_DPID UNIQUE (DPID)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

MariaDB [access]> show columns from TB_LEGACY_MODE_SWITCH;
+--------------+--------------+------+-----+---------+----------------+
| Field        | Type         | Null | Key | Default | Extra          |
+--------------+--------------+------+-----+---------+----------------+
| ID           | bigint(20)   | NO   | PRI | NULL    | auto_increment |
| MGMT_IP      | varchar(100) | NO   | UNI | NULL    |                |
| DPID         | varchar(100) | NO   | UNI | NULL    |                |
| PREV_CONF    | text         | YES  |     | NULL    |                |
| CREATED_TIME | datetime     | YES  |     | NULL    |                |
| UPDATED_TIME | datetime     | YES  |     | NULL    |                |
+--------------+--------------+------+-----+---------+----------------+
6 rows in set (0.003 sec)
```


**References**
- [https://mariadb.com/kb/en/text/](https://mariadb.com/kb/en/text/)