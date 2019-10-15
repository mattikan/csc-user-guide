
# Introduction to the Allas storage service

## What is Allas?

**Allas** is CSC's general purpose research data storage server. It is a part of the CSC storage portfolio and can be accessed on the CSC servers as well as from anywhere on the Internet. Allas can be used both for static research data that needs to be available for analysis and to collect and host cumulating or changing data. A CSC project is required to import data to Allas. Allas can be used to host data as long as the CSC project is active. 

From the technical point of view, Allas is a modern object storage system. It comes with _S3_ and _Swift_ interfaces on a _CEPH_ storage. In practice, this means that instead of files, the data is stored as objects in buckets. A bucket is a container for objects that may also include metadata describing the bucket. 

The stored objects can be of any data type, such as images or compressed data files. In general, objects are similar to files. The object storage can be used for a variety of purposes. It has benefits but also limitations.

**Benefits**

 * The object storage can handle practically any static data.
 * The data can be accessed from anywhere using the URL.
 * The data can have different levels of access control.

**Limitations**

 * Specific tools are required to use object storage. The object storage cannot be properly mounted for local disk-like usage. There are some tools that can do this, but they have their limitations. For example, _svfs_ can be used to mount _Swift_ as a file system, but it uses _FUSE_ which is slow.
 * It is unsuitable for files that change constantly during their lifetime (e.g. most SQL databases).
 * The data cannot be modified while it is in Allas. It must be downloaded to a server for processing and the previous version replaced with a new one.
 * Files larger than 5 GB must be divided into smaller segments. Normally, this is done automatically during upload. See [Files larger than 5 GB](./using_allas/common_use_cases.md#files-larger-than-5-gb).

## Different ways to use Allas

You cannot mount Allas direcly to a computer.  This means that in order to use Allas, you need software tools to access it. There are four main ways to access Allas:

![Allas access clients](img/allas-access-flavors.png)

1. In the CSC computing environment (e.g. **Puhti**), there are ready-to-use tools provided by CSC to access Allas. These tools are mostly the same that can also be installed in any linux environment, e.g. a virtual machine in cPouta or local Linux server.  
In the CSC computing environment, Allas should be used to store any data that needs to be preserved for longer than a few weeks. The supercomputer's own storage has a policy to delete idle data, so the data must be moved to Allas after computing. See [Computing disk environment](../../computing/disk.md)

2. WWW access to Allas is provided by the web interface of the cPouta cloud environment [https://pouta.csc.fi](https://pouta.csc.fi). No special software is required to access Allas with a browser, making this the by far simplest way to access Allas. On the other hand, the browser user interface has a number of limitations compared to other clients, the most notable of which are lower performance and uploading/downloading only a single file at a time. The instructions for accessing and using Allas with a browser: [OpenStack Horizon web interface](./using_allas/web_client.md)

3. To access Allas with command line commands, client software supporting the _Swift_ or _S3_ protocol is required. This is the most flexible way to access Allas, but it requires more effort from than other access methods. Instructions to use a command line client: [Accessing Allas with Linux](./accessing_allas.md#accessing-allas-in-the-csc-computing-environment-and-other-linux-platforms).

4. To access Allas with a GUI client, a suitable GUI client is required. The client needs to be capable to use the _Swift_ or _S3_ access protocol. Instructions to use a GUI client: [Accessing Allas with Windows and Mac](./accessing_allas.md#accessing-allas-with-windows-or-mac).

See also the [common Use cases](./using_allas/common_use_cases.md).

## Billing and quotas

Allas is used with project-based storage quotas. The default quota for a new project is 10 TB, but that can be increased if needed. Allas is the preferred storage site for any large datasets in the CSC environment, so you should not hesitate to request a larger quota for Allas, if you work with larger data sets. 

All project members have equal access rights to the storage area that has been granted for the project. In practice, this means that if one project member uploads data to Allas, all other project members can also read, edit and delete the data. Allas itself does not store any information about who has uploaded the data to Allas.

The default quotas for projects:

| Resource | Limit |
| :-------- |:------- |
| Storage amount | 10 TiB |
| Buckets per project | 1 000 |
| Objects per bucket | 500 000 |

Storing data in Allas consumes _billing units_. In Allas, billing is based on the amount data stored in Allas. The rate is 1 BU/TiBh, i.e. 1 TB of data stored in Allas consumes 24 BU in a day and 8760 BU in a year.

Unlike most other object storage providers, CSC does <u>not</u> charge for object storage network transfers or API calls.

## Protocols

The object storage service is provided over two different protocols, _Swift_ and _S3_. From the user perspective, one of the main differences between S3 and Swift is authentication. The token-based Swift authentication used in Allas remains valid for three hours at a time, but in the key-based S3, the connection can stay permanently open. The permanent connection of S3 is practical in many ways, but it includes a security aspect: if the server where Allas is used is compromised, the object storage space will be compromised as well.

Due to this security concern, Swift is the recommended protocol for multiple-user servers, such as Mahti and Puhti. Thus, for example, the CSC-specific *a-commands* as well as the standard _rclone_ configuration in Puhti are based on Swift. However, in some cases, the permanent connections provided by the S3 protocol may be the most reasonable option, for example, in personal virtual machines running in cPouta.

The Swift and S3 protocols are <u>not</u> compatible when handling objects. For small objects that do not need to be split during upload, the protocols can be used interchangeably, but split objects can be accessed only with the protocol that was used for uploading them. The size limit for splitting an object depends on the settings and on the protocol. The limit is typically between 500 MB and 5 GB.

Generic recommendations for selecting the protocol:
 
 * If possible, use the _Swift_ protocol. It is better supported.
 * In any case, choose only one of the protocols. Do not mix _S3_ and _Swift_.
 * It is better to store a few large objects than many small objects.

## Clients

Allas is accessed via client software that takes care of moving data to and from Allas and managing data objects. There are several different kinds of client software for accessing the object storage servers. Allas can be used with any object storage client that is compatible with the Swift or S3 protocol.

| Client | Notes |
| :-------- | :------- |
| [web client](./using_allas/web_client.md) | Use via [https://pouta.csc.fi](https://pouta.csc.fi). Provides basic functions. |
| [a-commands](./using_allas/a_commands.md) | Provides easy-to-use tools for basic use. Requires Rclone, Swift and OpenStack. |
| [swift python-swiftclient](./using_allas/swift_client.md)| The recommended Swift client. |
| [s3cmd](./using_allas/s3_client.md) | The recommended S3 client (version 2.0.2 or later). |
| [python-swift-library](./using_allas/python_library.md) |	| 
| [rclone](./using_allas/rclone.md) | Useful with supercomputers. |
| libs3	| |	 	 
| python-openstackclient | |
| aws-cli | aws-cli and the boto3 python library. |
| curl | Extremely simple to use with public objects and temporary URLs. |
| wget | Same as curl. |

### Client operations

A _web client_ is suitable for using the basic functions. *a_commands* offers easy-to-use functions for using Allas either via a personal computer or supercomputer. Power users might want to consider the clients _rclone_, _Swift_ and _s3cmd_. The table displays the core functions of the power clients concerning data management in Allas.

| | &nbsp;&nbsp;&nbsp; web client | &nbsp;&nbsp;&nbsp; a_commands | &nbsp;&nbsp;&nbsp;&nbsp; swift &nbsp;| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; s3cmd &nbsp;|
| :----- | :-----: | :----: | :-----: | :----: |
| Usage | _Basic_ | _Basic_ | _Power_ | _Power_ |
| **Create buckets** | <font color="green">&#x2714;</font> | <font color="green">&#x2714;</font> | <font color="green">&#x2714;</font> | <font color="green">&#x2714;</font> |
| **Upload objects** | <font color="green">&#x2714;</font>&#8226; | <font color="green">&#x2714;</font> | <font color="green">&#x2714;</font> | <font color="green">&#x2714;</font> |
| **List** | | | | |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objects | <font color="green">&#x2714;</font> | <font color="green">&#x2714;</font> | <font color="green">&#x2714;</font> | <font color="green">&#x2714;</font> |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; buckets | <font color="green">&#x2714;</font>  | <font color="green">&#x2714;</font>  | <font color="green">&#x2714;</font> | <font color="green">&#x2714;</font>  |
| **Download** | | | | |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objects | <font color="green">&#x2714;</font>&#8226; | <font color="green">&#x2714;</font> | <font color="green">&#x2714;</font> | <font color="green">&#x2714;</font> |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; buckets | | | <font color="green">&#x2714;</font> | <font color="green">&#x2714;</font> |
| **Remove** | | | | |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objects | <font color="green">&#x2714;</font> | <font color="green">&#x2714;</font> | <font color="green">&#x2714;</font> | <font color="green">&#x2714;</font> |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; buckets | <font color="green">&#x2714;</font>&#8226;&#8226; | <font color="green">&#x2714;</font>&#8226;&#8226; | <font color="green">&#x2714;</font> | <font color="green">&#x2714;</font>&#8226;&#8226; |
| **Managing access rights** | | | | |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; public/private | <font color="green">&#x2714;</font> | <font color="green">&#x2714;</font> | <font color="green">&#x2714;</font> | <font color="green">&#x2714;</font> |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; read/write access</br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; to another project | | | <font color="green">&#x2714;</font> | <font color="green">&#x2714;</font> |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; temp URLs | | | <font color="green">&#x2714;</font> | <font color="green">&#x2714;</font> |
| **Move objects** | | | <font color="green">&#x2714;</font> | <font color="green">&#x2714;</font> |
| **Edit metadata** | | | <font color="green">&#x2714;</font> | <font color="green">&#x2714;</font> |
| **Upload large files** (over 5 GB) | | <font color="green">&#x2714;</font> | <font color="green">&#x2714;</font> | <font color="green">&#x2714;</font> |
| **Download whole project** | | | <font color="green">&#x2714;</font> | |
| **Remove whole project** | | | <font color="green">&#x2714;</font> | |



<div align="right">&#8226; Only one object at a time</div>
<div align="right">&#8226;&#8226; Only empty buckets</div>


## System Characteristics

In Allas, objects are stored in buckets. A bucket is a data object container. Buckets should not be confused with _dockers_ or other computing containers. A bucket functions similarly to a file system directory, except that there can only be one level, i.e. buckets cannot contain other buckets.

![Allas projects and buckets](img/allas_projects_and_buckets.PNG)
**Figure** Data structure in Allas

## Naming buckets

Each bucket has a name that must be unique across all Allas users. If another user has a bucket called "_test_", another bucket called "_test_" cannot be created. All bucket names are public, so please do <u>not</u> include any confidential information in the bucket name. You may, for example, use your project ID, e.g. _2000620-raw-data_.

Object URLs can be in the DNS format, e.g. _https://object.pouta.csc.fi/bucketname/objectname_. Please use a valid DNS name (RFC 1035). We recommend not using upper case or non-ASCII (&auml;, &ouml; etc.) characters.

It is <u>not</u> possible to rename a bucket. 

The data is spread across various servers, which protects against disk and server failures. **Please note:** This does not protect the data from e.g. accidental deletion. Please make regular backups of important data.