# Datasource Log Format
SOFATracer tracks the standard JDBC data source and outputs the chain data of SQL statement execution, in the default `JSON` format.

### DataSource digest log (datasource-client-digest.log)

The data is output in JSON format. Each key meaning is as follows:

Key | Meaning
--------- | -------------
 Time | log printing time
 Local.app | Current application name
 traceId | TraceId
 spanId | SpanId
 Database.name | Database name
 Sql | SQL execution statement
 Result.code | SQL execution status code
 Total.time | SQL statement execution total time
 Connection.establish.span | SQL execution connection establishment time
 Db.execute.cost | SQL execution time
 Database.type | Database type
 Database.endpoint | Database url
 Current.thread.name | Current thread name
 Baggage | Transparently transmitted baggage data

Example:

```json
{"time":"2018-09-28 01:11:56.715","local.app":"SOFATracerDataSource","traceId":"1e1bcab91538068316462100111113","spanId":"0.1.2","database.name":"test","sql":"CREATE TABLE TEST(ID INT PRIMARY KEY%2C NAME VARCHAR(255));","result.code":"success","total.time":"228ms","connection.establish.span":"220ms","db.execute.cost":"3ms","database.type":"h2","database.endpoint":"jdbc:h2:~/test:-1","current.thread.name":"http-nio-8080-exec-1","baggage":""}
```

### DataSource statistical Log (datasource-client-stat.log)

`stat.key` is the set of statistical keywords in this period, which uniquely determines a set of statistical data, including local.app, database.name, and SQL field.

<table>
   <tr>
      <td colspan="2">Key</td>
      <td>Meaning</td>
   </tr>
   <tr>
      <td colspan="2">time</td>
      <td>Log printing time</td>
   </tr>
   <tr>
      <td rowspan="3">stat.key</td>
      <td>local.app</td>
      <td>Current application name</td>
   </tr>
   <tr>
      <td>database.name</td>
      <td>Database name</td>
   </tr>
   <tr>
      <td>sql</td>
      <td>SQL execution statement</td>
   </tr>
   <tr>
      <td colspan="2">count</td>
      <td>SQL execution count in this period</td>
   </tr>
   <tr>
      <td colspan="2">total.cost.milliseconds</td>
      <td>Total duration (ms) for SQL execution in this period</td>
   </tr>
   <tr>
      <td colspan="2">success</td>
      <td>Request result: Y for success; N for failure</td>
   </tr>
   <tr>
      <td colspan="2">load.test</td>
      <td>Pressure mark: T for pressure test; F for non-pressure test</td>
   </tr>
</table>

Example:

```json
{"time":"2018-09-28 01:12:43.647","stat.key":{"local.app":"SOFATracerDataSource","database.name":"test", "sql":"CREATE TABLE TEST(ID INT PRIMARY KEY%2C NAME VARCHAR(255));"},"count":1,"total.cost.milliseconds":228,"success":"true","load.test":"F"}
```