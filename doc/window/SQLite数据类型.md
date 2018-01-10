## SQlite数据类型

#### SQLite 存储类【存储类型】
###### TEXT
值为文本字符串，使用数据库编码存储
###### BLOB
值是一个blob数据，完全根据他的输入存储
###### INTEGER
值是一个带符号的整数，根据值得大小存储在1，2，4，6，8字节中
###### REAL
值是一个浮点数，存储为8字节IEEE浮点数字
###### NULL
值是一个NULL值

#### SQLite亲和类型，当数据插入时，会首先采用亲和类型作为该值的存储方式

###### TEXT（如果类型字符串包括CHAR,CLOB,TEXT,那么字段的亲缘类型为TEXT）
* CHARACTER(20)
* VARCHAR(255)
* VARYING CHARACTER(255)
* NCHAR(55)
* NATIVE CHARACTER(70)
* NVARCHAR(100)
* TEXT
* CLOB
###### INTEGER（如果类型字符串包含INT，那么字段的亲缘类型为INTEGER）
* INT
* INTEGER, 32位
* TINYINT
* SMALLINT, 16位
* MEDIUMINT
* BIGINT
* UNSIGNED BIG INT
* INT2
* INT8
###### NUMBERIC（其他的亲缘类型都为NUMBERIC）
* NUMBERIC
* DECIMAL(p.s) 精确值p是指全部由几个数的大小值，s是指小数点后有几位数。默认p=5，s=0
* BOOLEAN
* DATE
* DATETIME, 必须写成'2010-08-05'不能写为'2010-8-5'
###### NONE（如果类型字符串包含BLOB，那么字段的亲缘类型是NONE）
* BLOB
* no datatype specified
###### REAL（如果类型字符串中包括DOUB,FLOA,REAL，那么字段亲缘类型是REAL）
* REAL
* DOUBLE
* DOUBLE PRECOSION
* FLOAT