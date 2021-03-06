# Time attributes {#concept_swd_nmd_5fb .concept}

This topic describes the time attributes Event Time and Processing Time supported by Flink SQL.

Flink SQL supports the following two time attributes:

-   Event Time: the event time that you provide in the table schema, which is generally the original creation time of data.
-   Processing Time: the local system time at which the system processes an event.

The following figure shows the positions of different time attributes in the workflow of Realtime Compute.

![](images/40675_en-US.svg)

As shown in the preceding figure, Ingestion Time and Processing Time are time attributes that are automatically generated by the system for a data record. You do not have control over them. Event Time is a time attribute that comes with a data record. Because of data out-of-order, network jitter, or other reasons, a data record whose Event Time is t1 \(corresponding to partition 1\) may be processed by Flink later than another data record whose Event Time is t2 \(corresponding to partition 2\). t2 is later than t1.

## Event Time {#section_jf3_mhf_5fb .section}

Event Time is also known as rowtime. The Event Time attribute must be declared in the source table DDL. You can declare a certain field in the source table as an Event Time \(rowtime\) field. Currently, you can only declare a field of the TIMESTAMP type as a rowtime field. The LONG type will be supported in the future. If you do not have a TIMESTAMP column available, you need to use a [computed column](intl.en-US/Flink SQL Development Guide/Flink SQL/Basic concepts/Computed column.md#) to build a TIMESTAMP column based on an existing column.

Because of data out-of-order, network jitter, or other reasons, the order in which data records are received may be different from the order in which they are processed. Therefore, to define a rowtime field, you need to explicitly define a [watermark](intl.en-US/Flink SQL Development Guide/Flink SQL/Basic concepts/Watermark.md#) computation method.

The following is an example of Event Time-based window aggregation:

```language-sql
CREATE TABLE tt_stream(
  a VARCHAR,
  b VARCHAR,
  c TIMESTAMP,
  WATERMARK wk1 FOR c as withOffset(c, 1000)  --The watermark computation method.
) WITH (
  type = 'sls',
  topic = 'yourTopicName',
  accessId = 'yourAccessId',
  accessKey = 'yourAccessSecret'
);

CREATE TABLE rds_output(
  id VARCHAR,
  c TIMESTAMP, 
  f TIMESTAMP,
  cnt BIGINT
) WITH (
  type = 'rds',
  url = 'jdbc:mysql://****3306/test',
  tableName = 'yourTableName',
  userName = 'yourUserName',
  password = 'yourPassword'
);

INSERT INTO rds_output
SELECT a AS id, 
     SESSION_START(c, INTERVAL '1' SECOND) AS c, 
     CAST(SESSION_END(c, INTERVAL '1' SECOND) AS TIMESTAMP) AS f, 
     COUNT(a) AS cnt
FROM tt_stream
GROUP BY SESSION(c, INTERVAL '1' SECOND), a
```

## Processing Time {#section_lv4_5kf_5fb .section}

Processing Time is generated by the system and is not included in your raw data. Therefore, you need to explicitly define a Processing Time column in the declaration of the source table.

```language-sql
filedName as PROCTIME()
```

The following is an example of Processing Time-based window aggregation:

```language-sql
CREATE TABLE mq_stream (
  a VARCHAR,
  b VARCHAR,
  c BIGINT,
  d AS PROCTIME()   -- Explicitly define a Processing Time column in the declaration of the source table.) WITH (
  type = 'mq',
  topic = 'yourTopic',
  accessId = 'yourAccessId',
  accessKey = 'yourAccessSecret'
);

CREATE TABLE rds_output (
  id VARCHAR,
  c TIMESTAMP, 
  f TIMESTAMP,
  cnt BIGINT) with (
  type = 'rds',
  url = 'yourDatebaseURL',
  tableName = 'yourDatabasTableName',
  userName = 'yourUserName',
  password = 'yourPassword'
);

INSERT INTO rds_output
SELECT a AS id, 
     SESSION_START(d, INTERVAL '1' SECOND) AS c, 
     SESSION_END(d, INTERVAL '1' SECOND) AS f, 
     COUNT(a) AS cnt
FROM mq_stream
GROUP BY SESSION(d, INTERVAL '1' SECOND), a
```

