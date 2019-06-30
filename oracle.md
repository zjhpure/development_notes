- Oracle更新时间
  Oracle好像没有MySQL那种自动生成时间和自动更新时间的字段功能，像如下这种：
  `create_time` timestamp default current_timestamp comment '创建时间',
  `update_time` timestamp default current_timestamp on update current_timestamp comment '更新时间',
  MySQL里不需再去考虑update_time更新的问题，每次更新此某行数据时update_time会自动更新，
  Oracle里建表常用的方式如下，更新数据时sql里显式的更新update_time字段
  create_time          TIMESTAMP    default systimestamp,
  update_time          TIMESTAMP,
  服务器写入数据时同时插入更新时间。


- Oracle年份为条件查询
  时间列为date格式，要以年份为条件查询数据用where to_char(tm,'yyyy') = '2009'。


- Oracle年龄分组查询
  按年龄范围分组查询人数(按出生日期算)：

  select case 
  when (to_char(sysdate, 'yyyy') - to_char(t.csrq, 'yyyy')) < 20 then '20-' 
  when (to_char(sysdate, 'yyyy') - to_char(t.csrq, 'yyyy')) BETWEEN 21 AND 30 then '21-30' 
  when (to_char(sysdate, 'yyyy') - to_char(t.csrq, 'yyyy')) BETWEEN 31 AND 40 then '31-40' 
  when (to_char(sysdate, 'yyyy') - to_char(t.csrq, 'yyyy')) BETWEEN 41 AND 50 then '41-50' 
  when (to_char(sysdate, 'yyyy') - to_char(t.csrq, 'yyyy')) BETWEEN 51 AND 60 then '51-60' 
  when (to_char(sysdate, 'yyyy') - to_char(t.csrq, 'yyyy')) > 60 then '60+' end as age_range, count(*) from LY_HR_XXFW_JBXX t 
  group by case 
  when (to_char(sysdate, 'yyyy') - to_char(t.csrq, 'yyyy')) < 20 then '20-' 
  when (to_char(sysdate, 'yyyy') - to_char(t.csrq, 'yyyy')) BETWEEN 21 AND 30 then '21-30' 
  when (to_char(sysdate, 'yyyy') - to_char(t.csrq, 'yyyy')) BETWEEN 31 AND 40 then '31-40' 
  when (to_char(sysdate, 'yyyy') - to_char(t.csrq, 'yyyy')) BETWEEN 41 AND 50 then '41-50' 
  when (to_char(sysdate, 'yyyy') - to_char(t.csrq, 'yyyy')) BETWEEN 51 AND 60 then '51-60' 
  when (to_char(sysdate, 'yyyy') - to_char(t.csrq, 'yyyy')) > 60 then '60+' end
  order by age_range;

  注意between and的边界值，不同数据库边界值不同，Oracle是闭区间是全包含的。

  可参考：https://blog.csdn.net/chenXiaosCode/article/details/52354417。


- Oracle处理sum函数返回NULL
  sum函数返回NULL的解决办法，多数情况下，我们希望如果没有符合条件记录的情况下，我们希望它返回0，而不是NULL，那么我们可以使用例如下面的方法来处理：
  SELECT COALESCE(SUM(name), 0) FROM TABLE；
  当sum结果为null时就会返回0。


- Oracle分页问题
  有时会发现加了order by排序条件后的分页和不分页，整体结果是不同的，这是因为排序条件有出现重复的，
  比如经常用创建时间排序，order by createTime，但是在开发调试时有可能自己插入的数据的创建时间时一致(可能是因为直接在数据库插入造成的，建议不要这样)，就会导致出现排序条件有相同的，
  解决方法：
  1) 可以保证创建时间不同；
  2) 也可以order by后面加上唯一性字段（类似主键id），比如：order by createTime, id；
  3) 还可以提取rownum到外部，例如：SELECT * FROM (SELECT T.*, ROWNUM AS rowno FROM (SELECT * FROM TBLLE ORDER BY LIST_ORDER) T ) WHERE rowno >= #startRow# AND ROWNUM < #endRow#，
  但是mybatisplus的框架默认rownum到外部了，如下：
  SELECT * FROM (SELECT TMP.*, ROWNUM ROW_ID FROM (SELECT * FROM (LY_JZ_ZPFW_GWYPXX G LEFT JOIN ZPFW_GWJH Z ON G.GWJHID = Z.GWJHID) LEFT JOIN ZPFW_JBXX Y ON Y.XM = G.YPRYXM ORDER BY G.YPSJ DESC) TMP WHERE ROWNUM <=5) WHERE ROW_ID > 0
  所以还是2方法会更好。


- Oracle表添加字段
  例如：ALTER TABLE 表名 ADD 列名 VARCHAR2(60);
  

- Oracle表添加字段以及注释：
  ALTER TABLE RSFW_HTXX ADD SYKSSJ DATE;
  COMMENT ON COLUMN RSFW_HTXX.SYKSSJ IS '注释1'; 

  ALTER TABLE RSFW_HTXX ADD SYQGZ VARCHAR2(60);
  COMMENT ON COLUMN RSFW_HTXX.SYQGZ IS '注释2'; 


- Oracle处理时间差
  获取两时间的相差天数：
  select ceil((To_date('2008-05-02 00:00:00' , 'yyyy-mm-dd hh24-mi-ss') - To_date('2008-04-30 23:59:59' , 'yyyy-mm-dd hh24-mi-ss'))) 相差天数 FROM DUAL;

  获取两时间月份差：
  select (EXTRACT(year FROM to_date('2009-05-01','yyyy-mm-dd')) - EXTRACT(year FROM to_date('2008-04-30','yyyy-mm-dd'))) * 12 + EXTRACT(month FROM to_date('2008-05-01','yyyy-mm-dd')) - EXTRACT(month FROM to_date('2008-04-30','yyyy-mm-dd')) months from dual;

  获取两时间年份差：
  select EXTRACT(year FROM to_date('2009-05-01','yyyy-mm-dd')) - EXTRACT(year FROM to_date('2008-04-30','yyyy-mm-dd')) years from dual;


- 查找最近一周、一个月、三个月的数据
  select * from A where worker_date between ADD_MONTHs(sysdate,-2) and sysdate;


- 查询距离结束时间还有n个月的数据
  SELECT T.JSSJ FROM RSFW_HTXX T WHERE T.JSSJ BETWEEN SYSDATE AND ADD_MONTHS(SYSDATE, 12);


- Oracle时间格式问题
  使用Oracle数据库格式到时分秒的时候，时间格式统一用24小时制的，yyyy-MM-dd HH24:mm:ss。


- Oracle不同数据库之间的连接，可以用dblink的方式连接
  例如：
  create database link JCFW_DBLINK connect to jcfw identified by jcfw using '(DESCRIPTION =(ADDRESS_LIST =(ADDRESS =(PROTOCOL = TCP)(HOST = 192.168.2.28)(PORT = 1521)))(CONNECT_DATA =(SERVICE_NAME = orcl)))';
  这里在测试dblink连接时，可能会提示密码错误，是因为Oracle版本的问题会自动将密码转化为大写，
  解决办法，密码用双引号引起来，把密码jcfw用双引号引起，如下：
  create database link JCFW_DBLINK connect to jcfw identified by "jcfw" using '(DESCRIPTION =(ADDRESS_LIST =(ADDRESS =(PROTOCOL = TCP)(HOST = 192.168.2.28)(PORT = 1521)))(CONNECT_DATA =(SERVICE_NAME = orcl)))';
  例如测试：
  select T.JGH, T.XM from XXFW_JBXX@JCFW_DBLINK.REGRESS.RDBMS.DEV.US.ORACLE.COM T;，执行正常就是测试成功。


























  
