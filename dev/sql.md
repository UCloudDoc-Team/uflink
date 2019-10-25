

# UFlink  SQL 开发指南

UFlink SQL 是 UCloud 为简化计算模型，降低用户使用实时计算的门槛而设计的一套符合标准 SQL 语义的开发套件。

接下来，开发者可以根据如下内容，逐渐熟悉并使用 UFlink SQL 组件所提供的便捷功能。

## 1  表中受支持的数据类型

| 数据类型  | 说明                  | 映射的Java类型      |
| --------- | --------------------- | ------------------- |
| BOOLEAN   | 逻辑值，true 或 false | Boolean.class       |
| INTEGER   | 整形，4 字节整数      | Integer.class       |
| INT       | 整型，4 字节整数      | Integer.class       |
| BIGINT    | 长整型，8 字节整数    | Long.class          |
| TINYINT   | 微整型，1 字节整数    | Byte.class          |
| SMALLINT  | 短整型，2 字节整数    | Short.class         |
| VARCHAR   | 变长字符串            | String.class        |
| FLOAT     | 4 字节浮点数          | Float.class         |
| DOUBLE    | 8 字节浮点数          | Double.class        |
| DATE      | 日期类型：            | Date.class          |
| TIMESTAMP | 时间戳类型            | Timestamp.class     |
| DECIMAL   | 小数类型              | BigDecimal.class    |
| MAP       | 映射类型              | LinkedHashMap.class |
| ARRAY     | List 列表类型         | LinkedList.class    |
| LIST      | List 列表类型         | LinkedList          |

## 2  DDL 语句

UFlink 实时计算框架本身不带有存储数据的功能，因此，创建的表均为外部表，是对外部数据的映射和引用，在程序执行时，数据会动态加载到 UFlink 中。

### 2.1  创建源表 

在 UFlink SQL 中，创建 **数据源表** 遵循如下规律：

```SQL
CREATE TABLE table_name(
	columnName dataType,
	[, columnName dataType ]*
) WITH (
    type = 'xxxx',
    xxxx = 'xxxx'
);
```

#### 1. Kafka 数源表

#### （1）示例

​	以 Kafka 作为数据源表，建表语句示例如下：

```SQL
CREATE TABLE ut_kafka_source(
    user_id VARCHAR,
    pay_cash FLOAT,
    pay_time VARCHAR
 )WITH(
    type = 'kafka11',
    bootstrapServers = 'linux01:9092,linux02:9092,linux03:9092',
    topic = 'flink-test01',
    groupId = 'group1',
    parallelism = '1'
 );
```

上述代码用于在 UFlink 中创建一张名为 ut\_kafka\_source 的表，并声明表中字段信息（属性名及属性类型）。

随后，必须在 WITH 代码块中指定数据来源，以及数据源所使用的框架的必要参数。

#### （2）相关参数

​	可配置参数如下表所示：

| 参数名           | 必填 | 默认值 | 含义                  | 举例                                                     |
| ---------------- | ---- | ------ | --------------------- | -------------------------------------------------------- |
| type             | 是   | 无     | 数据源类型            | type = 'kafka11'                                         |
| bootstrapServers | 是   | 无     | Kafka Broker地址      | bootstrapServers = 'linux01:9092,linux02:9092'           |
| topic            | 是   | 无     | 将要消费的 Kafka 主题 | topic = 'flink-test01,flink-test02'                      |
| groupId          | 是   | 无     | 消费者组ID            | groupId = 'group01'                                      |
| offsetReset      | 否   | latest | 启动时从何处消费      | offsetReset = 'earliest'(或：latest、{"0": 12, "1": 32}) |
| parallelism      | 否   | 1      | 读取数据并行度        | parallelism = '1'                                        |

### 2.2  创建结果表

在 UFlink SQL 中，创建 **结果表** 遵循如下规律：

```SQL
CREATE TABLE table_name(
	columnName dataType,
	[, columnName dataType ]*
) WITH (
    type = 'xxxx',
    xxxx = 'xxxx'
);
```

#### 1.  Kafka 结果表

#### （1）示例

​	以 Kafka 作为结果表，建表语句示例如下：

```SQL
CREATE TABLE ut_kafka_result(
	uid VARCHAR,
    pay_cash FLOAT,
	sex VARCHAR,
	age VARCHAR
 )WITH(
    type = 'kafka11',
    bootstrapServers = 'linux01:9092,linux02:9092,linux03:9092',
    topic = 'flink-test02',
    parallelism = '1'
 );
```

上述代码用于在 UFlink 中创建一张名为 ut\_kafka\_result 的表，并声明表中字段信息（属性名及属性类型）。

随后，必须在 WITH 代码块中指定数据输出时的相关参数，以及数据源所使用的框架的必要参数。


#### （2）相关参数

​	可配置参数如下表所示：

| 参数名           | 必填 | 默认值 | 含义                  | 举例                                           |
| ---------------- | ---- | ------ | --------------------- | ---------------------------------------------- |
| type             | 是   | 无     | 数据源类型            | type = 'kafka11'                               |
| bootstrapServers | 是   | 无     | Kafka Broker地址      | bootstrapServers = 'linux01:9092,linux02:9092' |
| topic            | 是   | 无     | 将要消费的 Kafka 主题 | topic = 'flink-test01,flink-test02'            |
| parallelism      | 否   | 1      | 读取数据并行度        | parallelism = '1'                              |

#### 2. MySQL 结果表

#### （1）示例

​	以 MySQL 作为结果表，建表语句示例如下：

```SQL
CREATE TABLE ut_mysql_result(
    uid VARCHAR,
    pay_cash FLOAT,
    sex VARCHAR,
    age VARCHAR
)WITH(
    type = 'mysql',
    url = 'jdbc:mysql://linux01:3306/db_flink_test?useUnicode=true&characterEncoding=utf-8',
    userName = 'root',
    password = '123456',
    tableName = 'ut_mysql_result',
    parallelism = '1'
);
```

#### （2）相关参数

​	可配置参数如下表所示：

| 参数名      | 必填 | 默认值 | 含义                | 举例                                                         |
| ----------- | ---- | ------ | ------------------- | ------------------------------------------------------------ |
| type        | 是   | 无     | 数据源类型          | type = 'mysql'                                               |
| url         | 是   | 无     | MySQL Database 地址 | url = 'jdbc:mysql:*/*/linux01:3306/db\_flink\_test?useUnicode=true&characterEncoding=utf-8' |
| username    | 是   | 无     | 用户名              | userName ='root'                                             |
| password    | 是   | 无     | 密码                | password ='123456'                                           |
| tablename   | 是   | 无     | MySQL 中的表名      | tableName ='ut\_mysql\_result'                                 |
| parallelism | 否   | 1      | 读取数据并行度      | parallelism = '1'                                            |

#### 3. PostgreSQL 结果表

#### （1）示例

​	以 PostgreSQL 作为结果表，建表语句示例如下：

```SQL
CREATE TABLE ut_postgre_result(
    uid VARCHAR,
    pay_cash FLOAT,
    sex VARCHAR,
    age VARCHAR
)WITH(
    type = 'postgre',
    url = 'jdbc:postgresql://10.9.11.122:5432/db_flink_test?useUnicode=true&characterEncoding=utf-8',
    userName = 'root',
    password = '123456',
    tableName = 'ut_postgre_result',
    parallelism = '1'
);
```

上述代码展示了如何使用 UFlink SQL 模块，并通过 PostgreSQL 与 UDW(UCloud 数据湖) 产品交互，实现数据输出到 UDW。

#### （2）相关参数

​	可配置参数如下表所示：

| 参数名      | 必填 | 默认值 | 含义                     | 举例                                                         |
| ----------- | ---- | ------ | ------------------------ | ------------------------------------------------------------ |
| type        | 是   | 无     | 数据源类型               | type = 'postgre'                                             |
| url         | 是   | 无     | PostgreSQL Database 地址 | url = 'jdbc:postgresql:*/*/10.9.11.122:5432/db\_flink\_test?useUnicode=true&characterEncoding=utf-8' |
| username    | 是   | 无     | 用户名                   | userName ='root'                                             |
| password    | 是   | 无     | 密码                     | password ='123456'                                           |
| tablename   | 是   | 无     | PostgreSQL 中的表名      | tableName ='ut\_postgre\_result'                               |
| parallelism | 否   | 1      | 读取数据并行度           | parallelism = '1'                                            |

### 2.3  创建维表

在 UFlink SQL 中，创建 **维度表** 遵循如下规律：

```SQL
CREATE TABLE table_name(
	columnName dataType,
	[, columnName dataType ]*
) WITH (
    type = 'xxxx',
    xxxx = 'xxxx',
    SIDE DIMENSION SIGN
);
```

其中，维表中最后一个字段，必须为SIDE DIMENSION SIGN，以此来标识这是一张维度表。

####  1. MySQL 维表

#### （1）示例

​	以 MySQL 作为维度表，建表语句示例如下：

```SQL
CREATE TABLE ut_mysql_side(
    uid VARCHAR,
    sex VARCHAR,
    age VARCHAR,
    PRIMARY KEY(uid),
    SIDE DIMENSION SIGN
 )WITH(
    type ='mysql',
    url ='jdbc:mysql://linux01:3306/db_flink_test?useUnicode=true&characterEncoding=utf-8',
    userName ='root',
    password ='123456',
    tableName ='ut_mysql_side',
    cache ='LRU',
    cacheSize ='2',
    cacheTTLMs ='2001',
    parallelism ='1',
    partitionedJoin='false'
 );
```

上述代码用于在 UFlink 中创建一张名为 ut\_mysql\_side 的表，并声明表中字段信息（属性名及属性类型），并可以指定主键对应的列，并且，涉及到 JOIN 操作的列，必须通过PRIMARY KEY 关键字进行声明，其中可以指定多个列（逗号分隔）。

随后，必须在 WITH 代码块中指定数据输出时的相关参数，以及数据源所使用的框架的必要参数。

#### （2）相关参数

可配置参数如下表所示：

| 参数名          | 必填 | 默认值 | 含义                                                         | 举例                                                         |
| --------------- | ---- | ------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| type            | 是   | 无     | 数据源类型                                                   | type = 'postgre'                                             |
| url             | 是   | 无     | MySQL Database 地址                                          | url = 'jdbc:mysql:*/*/linux01:3306/db\_flink\_test?useUnicode=true&characterEncoding=utf-8' |
| username        | 是   | 无     | 用户名                                                       | userName ='root'                                             |
| password        | 是   | 无     | 密码                                                         | password ='123456'                                           |
| tablename       | 是   | 无     | MySQL 中的表名                                               | tableName ='ut\_postgre\_result'                               |
| cache           | 否   | NONE   | 维表数据缓存方式                                             | cache = 'LRU'  cache = 'NONE'                            |
| cacheSize       | 否   | 无     | 缓存行数，cache属性为LRU时生效                               | cacheSize = '100'                                            |
| cacheTTLMs      | 否   | 无     | 缓存过期时间，cache属性为LRU时生效，单位：毫秒               | cacheTTLMs ='60000'                                          |
| partitionedJoin | 否   | 无     | 是否在 JOIN 数据之前，将维表数据按照 PRIMARY KEY指定的列进行reduceByKey操作，从而减少维表缓存数据量。 | partitionedJoin = 'true'                                     |
| parallelism     | 否   | 1      | 读取数据并行度                                               | parallelism = '1'                                            |

#### 2. PostgreSQL 维表

#### （1）示例

​	以 MySQL 作为维度表，建表语句示例如下：

```SQL
CREATE TABLE ut_postgre_side(
    uid VARCHAR,
    sex VARCHAR,
    age VARCHAR,
    PRIMARY KEY(uid),
    SIDE DIMENSION SIGN
 )WITH(
    type ='postgre',
    url = 'jdbc:postgresql://10.9.116.184:5432/db_flink_test?useUnicode=true&characterEncoding=utf-8',
    userName ='root',
    password ='birthDAY+-0230,',
    tableName ='ut_postgre_side',
    parallelism ='1',
    cache ='LRU',
    cacheSize ='2',
    cacheTTLMs ='2001',
    partitionedJoin='false'
 );
```

上述代码用于在 UFlink 中创建一张名为 ut\_mysql\_side 的表，并声明表中字段信息（属性名及属性类型），并可以指定主键对应的列，并且，涉及到 JOIN 操作的列，必须通过PRIMARY KEY 关键字进行声明，其中可以指定多个列（逗号分隔）。

随后，必须在 WITH 代码块中指定数据输出时的相关参数，以及数据源所使用的框架的必要参数。

#### （2）相关参数

可配置参数如下表所示：

| 参数名          | 必填 | 默认值 | 含义                                                         | 举例                                                         |
| --------------- | ---- | ------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| type            | 是   | 无     | 数据源类型                                                   | type = 'postgre'                                             |
| url             | 是   | 无     | PostgreSQL Database 地址                                     | url = 'jdbc:postgresql:*/*/10.9.11.122:5432/db\_flink\_test?useUnicode=true&characterEncoding=utf-8' |
| username        | 是   | 无     | 用户名                                                       | userName ='root'                                             |
| password        | 是   | 无     | 密码                                                         | password ='123456'                                           |
| tablename       | 是   | 无     | PostgreSQL 中的表名                                          | tableName ='ut\_postgre\_result'                               |
| cache           | 否   | NONE   | 维表数据缓存方式                                             | cache = 'LRU'  cache = 'NONE'                            |
| cacheSize       | 否   | 无     | 缓存行数，cache属性为LRU时生效                               | cacheSize = '100'                                            |
| cacheTTLMs      | 否   | 无     | 缓存过期时间，cache属性为LRU时生效，单位：毫秒               | cacheTTLMs ='60000'                                          |
| partitionedJoin | 否   | 无     | 是否在 JOIN 数据之前，将维表数据按照 PRIMARY KEY指定的列进行reduceByKey操作，从而减少维表缓存数据量。 | partitionedJoin = 'true'                                     |
| parallelism     | 否   | 1      | 读取数据并行度                                               | parallelism = '1'                                            |

### 2.4  创建视图

​	在 UFlink 中创建视图遵循如下逻辑：

```SQL
CREATE VIEW view_name AS SELECT columnName, [, columnName]* FROM table_name[view_name];
```

在 Flink 中通过创建临时视图，可以降低复杂 SQL 的理解成本，从而更好的管理 SQL 逻辑。

## 3  DML 语句

### 3.1  INSERT  INTO 语句

在 UFlink 中使用 INSERT INTO 语句遵循如下逻辑：

```SQL
INSERT INTO 
	table_name
	SELECT 
      [ (columnName[ , columnName]*) ]
   	queryStatement;
```

例如如下 SQL 操作：

```SQL
INSERT INTO
    ut_kafka_result
    SELECT
        t3.uid,
        t3.pay_cash,
        t3.sex,
        t3.age
    FROM
        (SELECT
            t1.*,
            t2.uid,
            t2.sex,
            t2.age
        FROM
            ut_kafka_source t1
        JOIN
            ut_mysql_side t2
        ON
        	t1.user_id = t2.uid
        WHERE
            t2.sex = '男'
        AND
        	t1.pay_cash > 100) AS t3
```

对于 INSERT INTO 操作，需遵循如下约束：

* 数据源表：只能 FROM，不能 INSERT
* 数据维表：只能 JOIN，不能 INSERT
* 数据结果表：只能 INSERT
* 视图：只能 FROM，不能 INSERT

## 4  QUERY 语句

UFlink 中允许直接对 SELECT 中涉及的字段应用 UDXF函数，从实现更复杂的字段到列的映射关系。

例如下面的数据：

```json
{"grade_data":[{"class1":{"小明":"98","老王":"100"}},{"class2":{"凯特琳":"88","凯南":"99"}}]}
```

通过如下 SQL 语句进行 **行转列**  操作：

```SQL
CREATE TABLE FUNCTION ParseArrayUDTF WITH cn.ucloud.sql.ParseArrayUDTF;

INSERT INTO
    tb_class_grade_result
    SELECT
        class,
        student,
        grade
    FROM
        tb_class_grade, LATERAL TABLE(ParseArrayUDTF(grade_data)) as T(class, student, grade)
```

执行完毕后，输出效果为：

| class  | student | grade |
| ------ | ------- | ----- |
| class1 | 小明    | 98    |
| class1 | 老王    | 100   |
| class2 | 凯特琳  | 88    |
| class2 | 凯南    | 99    |

其中自定义函数的具体实现可参考第 5 小节中的内容。

同时也可以使用 WHERE 和 JOIN 语法，例如如下 SQL 语句：

```SQL
SELECT
    t3.uid,
    t3.pay_cash,
    t3.sex,
    t3.age
FROM
    (SELECT
        t1.*,
        t2.uid,
        t2.sex,
        t2.age
    FROM
        ut_kafka_source t1
    JOIN
        ut_mysql_side t2
    ON
    	t1.user_id = t2.uid
    WHERE
        t2.sex = '男'
    AND
    	t1.pay_cash > 100) AS t3
```

另外同时支持 Flink 原生所有的 SQL 语法。

## 5  UDXF 自定义函数

在 Flink 中对某列数据进行复杂处理时，如果想要实现更有针对性的逻辑操作，可以通过实现 UDXF 函数来实现。

### 5.1  UDF 自定义标量函数

该类型函数主要特点为“一进一出”，即，依次对某单列数据进行处理，并输出当前列的处理结果（依然为单列）。

#### 1.  实现函数

#### （1） 规则

编写 UFlink 的 UDF 函数代码遵循如下规范：

```JAVA
public class XXX extends ScalarFunction{
    public OUT eval(IN columnValue){
        ...
    }
}
```

#### （2） 示例

以对某列数据进行大写转换为例，通过继承 ScalarFunction 类实现 UDF 函数，代码如下：

```JAVA
package cn.ucloud.sql;

public class TransCaseUDF extends ScalarFunction {
    private static final Logger LOG = LoggerFactory.getLogger(TransCaseUDF.class);
    public String eval(Object data){
        return new String(data.toString().toLowerCase().getBytes(), StandardCharsets.UTF_8);
    }
}
```

其中 eval 为固定方法名。

#### 2.  注册函数

#### （1） 规则

在 UFlink SQL 中，注册标量函数（Scalar）方式如下：

```SQL
CREATE SCALAR FUNCTION [UDFName] WITH [UDF Class Name];
```

#### （2） 示例

注册示例如下所示：

```SQL
CREATE SCALAR FUNCTION TransCaseUDF WITH cn.ucloud.sql.TransCaseUDF;
```

#### 3.  使用函数

结合上述示例，使用该函数方法如下：

```SQL
INSERT INTO
    tb_name
    SELECT
        TransCaseUDF(name) as upper_name,
    FROM
        tb_user_info;
```

### 5.2  UDAF 自定义聚合函数

该类型函数主要特点为 “多进一出”，即，对某几列多行数据进行聚合，并输出聚合结果。

#### 1.  实现函数

#### （1） 规则

编写 UFlink 的 UDAF 函数代码遵循如下规范：

* 定义累加器

```JAVA
public static class XXXAccum {...)
```

* 定义类继承自AggregateFunction

```JAVA
public class XXXUDAF extends AggregateFunction<T, XXXAccum> {
    public XXXAccum createAccumulator(){...} //创建累加器
    public T getValue(){...} //获取聚合结果
    public void accumulate(){...} //累加
    public void merge(){...} //合并多个分区的累加器
    public void resetAccumulator(){...} //重置累加器
}
```

#### （2） 示例

以求某列的平均数为例，实现方法如下：

```JAVA
package cn.ucloud.sql;
import org.apache.flink.table.functions.AggregateFunction;
import java.util.Iterator;

public class AverageUDAF extends AggregateFunction<Float, AverageUDAF.AverageAccum> {
    public static class AverageAccum {
        public float sum, count = 0F;
    }

    @Override
    public AverageAccum createAccumulator() {
        return new AverageAccum();
    }

    @Override
    public Float getValue(AverageAccum accumulator) {
        if(accumulator.count == 0F) return null;
        else return accumulator.sum / accumulator.count;
    }

    public void accumulate(AverageAccum accumulator, float value) {
        accumulator.sum += value;
        accumulator.count += 1F;
    }

    public void merge(AverageAccum accumulator, Iterable<AverageAccum> it) {
        Iterator<AverageAccum> iter = it.iterator();

        while(iter.hasNext()) {
            AverageAccum acc = iter.next();
            accumulator.sum += acc.sum;
            accumulator.count += acc.count;
        }
    }

    public void resetAccumulator(AverageAccum accumulator) {
        accumulator.sum = 0F;
        accumulator.count = 0F;
    }
}
```

#### 2.  注册函数

#### （1） 规则

在 UFlink SQL 中，注册聚合函数（Aggregate）方式如下：

```SQL
CREATE AGGREGATE FUNCTION [UDAFName] WITH [UDAF Class Name];
```

#### （2） 示例

结合上述示例，使用该函数方法如下：

```SQL
CREATE AGGREGATE FUNCTION AverageUDAF WITH cn.ucloud.sql.AverageUDAF;
```

#### 3.  使用函数

结合上述示例，使用该函数方法如下：

```SQL
CREATE AGGREGATE FUNCTION AverageUDAF WITH cn.ucloud.sql.AverageUDAF;

CREATE TABLE ut_kafka_source(
    sex VARCHAR,
    age FLOAT
 )WITH(
    type ='kafka11',
    bootstrapServers ='linux01:9092,linux02:9092,linux03:9092',
    topic ='flink-test01',
    groupId = 'group1',
    parallelism ='1'
 );

CREATE TABLE ut_mysql_age_result(
	sex VARCHAR,
    average_age FLOAT
 )WITH(
    type ='mysql',
    url = 'jdbc:mysql://linux01:3306/db_flink_test?useUnicode=true&characterEncoding=utf-8',
    userName ='root',
    password ='123456',
    tableName ='ut_mysql_age_result',
    parallelism ='1'
 );

INSERT INTO
    ut_mysql_age_result
    SELECT
        sex,
        AverageUDAF(age) AS average_age
    FROM
        ut_kafka_source
    GROUP BY
        sex;
```

### 5.3  UDTF  自定义表值函数

该类型函数主要特点为 “一进多出”，即，对某一行一列数据进行拆分，并输出结果到某列多行。

#### 1.  实现函数

#### （1） 规则

编写 UFlink 的 UDTF 函数代码遵循如下规范：

```JAVA
public class ParseArrayUDTF extends TableFunction<Row> {
    public void eval(T obj) {
       ...
    }

    @Override
    public TypeInformation<Row> getResultType() {
        return Types.ROW(A.class, B.class, C.class);
    }
}

```

#### （2） 示例

以拆分如下数据为例：

```json
{"grade_data":[{"class1":{"小明":"98","老王":"100"}},{"class2":{"凯特琳":"88","凯南":"99"}}]}
```

将其拆分为如下表：

| class  | student | grade |
| ------ | ------- | ----- |
| class1 | 小明    | 98    |
| class1 | 老王    | 100   |
| class2 | 凯特琳  | 88    |
| class2 | 凯南    | 99    |

此时，UDTF 函数实现如下：

```JAVA
package cn.ucloud.sql;

import org.apache.flink.api.common.typeinfo.TypeInformation;
import org.apache.flink.table.api.Types;
import org.apache.flink.table.functions.TableFunction;
import org.apache.flink.types.Row;

import java.util.List;
import java.util.Map;

public class ParseArrayUDTF extends TableFunction<Row> {
    //list 数据为 [{"class1":{"小明":"98","老王":"100"}},{"class2":{"凯特琳":"88","凯南":"99"}}]
    public void eval(List<Map<String, Map<String, String>>> list) {
        for (Map<String, Map<String, String>> element : list) {
            for (String className : element.keySet()) {
                Map<String, String> userMap = element.get(className);
                for(String userName: userMap.keySet()){
                    Row row = new Row(3);
                    row.setField(0, className);
                    row.setField(1, userName);
                    row.setField(2, userMap.get(userName));
                    collect(row);
                }

            }
        }
    }

    @Override
    public TypeInformation<Row> getResultType() {
        return Types.ROW(Types.STRING(), Types.STRING(), Types.STRING());
    }
}

```

#### 2.  注册函数

#### （1） 规则

在 UFlink SQL 中，注册聚合函数（Aggregate）方式如下：

```SQL
CREATE TABLE FUNCTION [UDTFName] WITH [UDTF Class Name];
```

#### （2） 示例

结合上述示例，使用该函数方法如下：

```SQL
CREATE TABLE FUNCTION ParseArrayUDTF WITH cn.ucloud.sql.ParseArrayUDTF;
```

#### 3.  使用函数

结合上述示例，使用该函数方法如下：

```SQL
CREATE TABLE FUNCTION ParseArrayUDTF WITH cn.ucloud.sql.ParseArrayUDTF;

CREATE TABLE tb_class_grade(
    grade_data ARRAY
 )WITH(
    type ='kafka11',
    bootstrapServers ='linux01:9092,linux02:9092,linux03:9092',
    zookeeperQuorum ='linux01:2181/kafka',
    offsetReset ='latest',
    topic ='flink-sql-test02',
    groupId = 'group1',
    parallelism ='1'
 );


CREATE TABLE tb_class_grade_result(
    class VARCHAR,
    student VARCHAR,
    grade VARCHAR
)WITH(
    type ='mysql',
    url = 'jdbc:mysql://linux01:3306/db_flink_test?useUnicode=true&characterEncoding=utf-8',
    userName ='root',
    password ='123456',
    tableName ='tb_class_grade_result',
    parallelism ='1'
);

INSERT INTO
    tb_class_grade_result
    SELECT
        class,
        student,
        grade
    FROM
        tb_class_grade, LATERAL TABLE(ParseArrayUDTF(grade_data)) as T(class, student, grade)
```

## 6  Watermark 的使用

### 6.1  Watermark 的使用规则

在创建表时，在属性字段的最后一行，为时间列创建Watermark，语法如下：

```SQL
WATERMARK FOR columnName AS WITHOFFSET(columnName, delayTime(ms))	
```

其中 columnName 必须为 LONG ，或BIGINT，或 TimeStamp类型的事件时间。

随后，Flink 将为该表生成 ROWTIME 字段，用于记录并表示事件时间。

### 6.2  Watermark 的使用示例

举个例子来说明 Watermark 的相关用法，示例 SQL 内容如下：

```SQL
CREATE TABLE ut_kafka_source(
    user_id VARCHAR,
    pay_cash FLOAT,
    pay_time LONG,
    WATERMARK FOR pay_time AS WITHOFFSET(pay_time, 2000)
 )WITH(
    type ='kafka11',
    bootstrapServers ='linux01:9092,linux02:9092,linux03:9092',
    topic ='flink-test01',
    groupId = 'group1',
    parallelism ='1'
 );

CREATE TABLE ut_kafka_result(
    user_id VARCHAR,
    pay_cash FLOAT
 )WITH(
    type ='kafka11',
    bootstrapServers ='linux01:9092,linux02:9092,linux03:9092',
    topic ='flink-test02',
    parallelism ='1'
 );

INSERT INTO
    ut_kafka_result
    SELECT
        user_id,
        SUM(pay_cash) as pay_cash
    FROM
        ut_kafka_source
    GROUP BY
        TUMBLE (
            ROWTIME,
            INTERVAL '2' SECOND
        ),
        user_id
```

其中，TUMBLE 为生成 滚动窗口 的函数，ROWTIME 为当前表的事件时间。
提示：上述操作需要在提交 SQL 任务时添加指定参数：time.characteristic: EventTime

