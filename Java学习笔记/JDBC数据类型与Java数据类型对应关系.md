title: JDBC数据类型与Java数据类型对应关系
layout: post
date: 7/3/2016 3:30:38 PM   
comments: true
categories: 
- Java
tags: 
- Java
- JDBC

---

|SQL|JDBC/JAVA|setter|getter|
|:--|:--|:--|:--|
|VARCHAR|java.lang.String|setString|getString|
|CHAR|java.lang.String|setString|getString|
|LONGVARCHAR|java.lang.String|setString|getString|
|BIT|boolean|setBoolean|getBoolean|
|NUMERIC|BigDecimal|setBigDecimal|getBigDecimal|
|TINYINT|byte|setByte|getByte|
|SMALLINT|short|setShort|getShort|
|INTEGER|int|setInt|getInt|
|BIGINT|long|setLong|getLong|
|REAL|float|setFloat|getFloat|
|FLOAT|float|setFloat|getFloat|
|DOUBLE|double|setDouble|getDouble|
|VARBINARY|byte[]|setBytes|getBytes|
|BINARY|byte[]|setBytes|getBytes|
|DATE|java.sql.Date|setDate|getDate|
|TIME|java.sql.Time|setTime|getTime|
|TIMESTAMP|java.sql.Timestamp|setTimestamp|getTimestamp|
|CLOB|java.sql.Clob|setClob|getClob|
|BLOB|java.sql.Blob|setBlob|getBlob|
|ARRAY|	java.sql.Array|setARRAY|getARRAY|
|REF|java.sql.Ref|SetRef|getRef|
|STRUCT|java.sql.Struct|SetStruct|getStruct|