### MySQL创建utf8字符集的数据库
  - 执行命令create database 数据库名称 default character set utf8 collate utf8_general_ci;


### MySQL转义字符
  - 当MySQL中字段内容中含有转义字符时，如：家轿「老字号」出奇招\\b！福美来F5要给你点颜色看看？，若要根据此字段内容去查询，需要写上转义查询，如：select * from table where title = '家轿「老字号」出奇招\\\\b！福美来F5要给你点颜色看看？';，要在\\前面添加上两个\\。


### MySQL5.7运行GROUP BY语句出错
  - MySQL5.7运行GROUP BY语句出现ERROR(1055):42000, which is not functionally dependent on columns in GROUP BY clause; this is incompatible with sql_mode=only_full_group_by，
  - 这是因为MySQL5.7默认不准group by查询，需要手动开启，
  - 执行命令mysql> SELECT @@sql_mode;，
  - 会返回：ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION，
  - 把这一串最前面的ONLY_FULL_GROUP_BY去掉，后面的内容复制，然后找到my.cnf文件，粘贴写到sql_mode =的后面：

        [mysqld] sql_mode 
        =STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION


### MySQL嵌套子查询
  - MySQL中不能通过嵌套子查询来直接删除或者修改记录，也就是说update或delete的where语句中不能子查询，需要通过别名来指定嵌套子查询作为一个临时表，解决办法：给嵌套子查询的结果取一个别名，然后从这个表中再次查询出记录，然后再做删除或者修改操作。


### MySQL的UUID
  - MySQL生成uuid方法，直接使用uuid()，执行select uuid();可以生成随机32位字符串，和Java的UUID.randomUUID().toString()方法一样的效果，如果是要执行insert语句在某个字段处生成uuid，则只需uuid()即可，例如：insert into `admin`(uid, account, password) values (uuid(), 'admin', '21232f297a57a5a743894a0e4a801fc3');


### MySQL的timestamp
  - mysql的timestamp类型默认为not null，其它类型都是默认null的，timestamp若要允许为null，则需要指定null，例如：

        `create_time` timestamp default current_timestamp comment '创建时间',
        `update_time` timestamp default current_timestamp on update current_timestamp comment '更新时间',
        `delete_time` timestamp null comment '删除时间',


### 解决MySQL Workbench闲置一段时间就卡住的问题
  - 可以Google搜索MySQL Workbench keep alive，
  - 解决方法1：(好像不是太行)
  - 点击Edit -> Preferences -> Others -> SSH KeepAlive，把原来的0改为10。

  - 解决方法2：
  - 修改/etc/mysql/mysql.conf.d/mysqld.cnf文件，在[mysqld]下面添加以下项：

        explicit_defaults_for_timestamp = TRUE
        connect_timeout = 1000000
        net_write_timeout = 1000000
        wait_timeout = 1000000
        max_allowed_packet = 1024M
        interactive_timeout = 1000000
        net_buffer_length = 200M
        net_read_timeout = 1000000
  - 注意，要看以上项配置文件中是否本来已存在，若已存在直接修改值即可，
  - 然后执行service mysql restart重启(注意，必须重启才生效)，
  - 还有注意，是修改服务端的mysql配置，不是修改客户端的mysql配置。


### MySQL主键问题
  - MySQL的主键不能用varchar，即使要用varchar也要用char，uuid用char(36)类型，因为uuid是32个字符，但是加上中间的4个-，所以是36个字符，
  - 例如：select * from uuid where uuid = '00000233-f46a-437b-8b19-a9495762053b';(这里uuid是主键)
  - 这个语句，若uuid用的是varchar，查询时间会很长，6千万条数据，这个语句大约要1分钟多才能执行完，
  - 若是若uuid用的是char(36)，查询时间会很短，6千万条数据，这个语句大约只要0.02秒就能执行完，
  - 但是这个select count(uuid) from uuid;(这里uuid是主键)sql语句，无论uuid用的是varchar还是char(36)，执行时间都很长，6千万条数据都是大约1分钟多，
  - 总的来说主键要用自增id，不用uuid，可以这样：主键用自增id，添加一个uuid字段但是设置唯一性索引，对外展示uuid，因为展示自增id会暴露出是第几条，查询时以uuid为条件查询，因为uuid有唯一性索引，就是有了索引，所以查询速度会很快。
  - 自增字段可以用bigint(20)，int(10)坐自增字段毕竟只是42亿左右，可能不够，用bigint就不会不够用了。
  - 回滚不会恢复自增id，例如有一条记录插入，但是回滚了，如果插入前最后的自增id是1775，然后再有一条记录插入，会发现现在的自增id是1777，跨了一个数。


### MySQL的information_schema数据库
  - MySQL的information_schema数据库，在该库中有一个tables表，这个表主要字段分别是：
  - TABLE_SCHEMA：数据库名
  - TABLE_NAME：表名
  - ENGINE：所使用的存储引擎
  - TABLES_ROWS：记录数
  - DATA_LENGTH：数据大小
  - INDEX_LENGTH：索引大小

  - MySQL中查询表的条数：
  - use information_schema;
  - select TABLE_ROWS from tables where table_schema = '数据库名' and table_name = '表名';
   
  - MySQL中查询表的占用空间大小：
  - use information_schema;
  - select DATA_LENGTH + INDEX_LENGTH from tables where table_schema = '数据库名' and table_name = '表名';
  - 得到的结果是以字节为单位，除1024为K，一个表占用空间的大小，那就相当于是数据大小 + 索引大小。


### MySQL的大小写
  - 注意MySQL的大小写区分，如下：

        drop database if exists `decrypt`;
        create database `decrypt` default character set utf8 collate utf8_bin;
        use `decrypt`;
        drop table if exists `decrypt`;
        create table `decrypt` (
            `id` int(10) not null auto_increment comment '解密自增id',
            `plaintext` varchar(255) not null comment '明文',
            `ciphertext` varchar(300) not null comment '密文',
            `type` varchar(100) not null comment '加密类型(直接写名字，如：md5，sha256)',
            `create_time` timestamp default current_timestamp comment '创建时间',
            `update_time` timestamp default current_timestamp on update current_timestamp comment '更新时间',
        primary key (`id`),
        unique key `id` (`id`),
        unique key `plaintext` (`plaintext`),
        index ciphertext (ciphertext(300)) 
        ) engine=InnoDB auto_increment=1 default charset=utf8 collate utf8_bin comment='解密表';

  - utf8_bin区分大小写，utf8_general_ci不区分大小写，collate utf8_bin这句若不写就是默认是utf8_general_ci，就会默认不区分大小写，
  - 若一个表设置了collate utf8_bin，那此表的所有字符类型字段的collate都会设置为utf8_bin，否则也默认是utf8_general_ci，
  - 也可以单独设置一个字段的collate为utf8_bin，加上binary即可(binary要紧跟varchar或char)，例如：

        `plaintext` varchar(255) binary not null comment '明文',

  - select * from some_table where str='abc'和select * from some_table where str='ABC'得到的结果是一样的，是不区分大小写的；但若在where后加上binary就会区分大小写，select * from some_table where binary str='abc'和select * from some_table where binary str='ABC'得到的结果是不一样的。
















