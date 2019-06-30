#### springboot项目以测服启动
  - 若是idea，在Run Configurations处的Application的Program arguments项设置：--spring.profiles.active=test
  - 若是命令行，找到用maven打包好的jar文件(一般是60m以上的，内嵌有web容器)，执行命令

        java -jar xxx.jar --spring.profiles.active=test


#### Swagger的bug
  - 访问Swagger-UI主页面有时会弹出一个框，提示Unable to infer base url. This is common when using dynamic servlet registration...，这应该是swagger的一个bug，解决方法：清理浏览器缓存就可以了


#### Swagger
  - swagger用2.4.0版本，不要用2.2.2版本，2.2.2版本有bug，部署到服务器swagger api的页面的右下角会有error的错误(本地服务器不会有)，2.4.0版本没有此bug，2.8版以后UI反人类


#### Swagger默认值
  - swagger的默认值有bug，若int类型时设置默认值为0，则swagger-ui.html网页无法显示出，如下：

        @ApiOperation(value = "分页查询xx")
        @ApiImplicitParams({
            @ApiImplicitParam(paramType = "query", name = "type", dataType = "string", required = true, value = "查询类型", defaultValue = "0"),
            @ApiImplicitParam(paramType = "query", name = "pageNum", dataType = "int", required = true, value = "当前页码", defaultValue = "1"),
            @ApiImplicitParam(paramType = "query", name = "pageSize", dataType = "int", required = true, value = "每页条数", defaultValue = "15")
        })
  - 第一个ApiImplicitParam因为dataType设置类型是string了，后面的defaultValue = "0"才会显示出0，若dataType设置类型是int是不会显示出0的


#### Swagger的文件传输
  - swagger在Java生成excel的导出时，在swagger下访问接口去下载excel会导致文件损坏而无法打开，直接用浏览器去测试会正常，可能是swagger的bug


#### springboot回滚
  - 删除操作时有些要做回滚，有可能会出现以下错误做法：
  - 先删除了，然后再从表中去取数据，发现取不出数据，因为都已经删除了所以取不到数据，又因为回滚了，所以没有被删，所以会奇怪怎么删除又失败了
  - 这里需要先取数据，再删除数据；而不是先删除数据，再取数据


#### mybatis最常见的错误
  - mybatis注意sql语句最后不要加上分号;，会运行错误的，提示“ORA-00911: 无效字符”，99%是是因为多加了分号


#### Map
  - LinkedHashMap保证与添加顺序一样，HashMap不保证与添加顺序一样


#### 命令行启动springboot项目
  - 可以将命令行位置跳转到当前项目的根目录下，输入“mvn spring-boot:run”命令


#### mybatis尽量用别名
  - 例如：

        <!-- xx视图类 -->
        <resultMap id="teachWorkloadVO" type="com.ly.cloud.teacherInfo.vo.TeachWorkloadVO">
            <result column="YEAR" property="year" jdbcType="VARCHAR"/>
            <result column="TEACHWORKLOAD" property="count" jdbcType="INTEGER"/>
        </resultMap>

        <!-- 统计xx -->
        <select id="countTeachWorkload" resultMap="teachWorkloadVO">
            SELECT TO_CHAR(T.ND, 'YYYY') AS YEAR, COALESCE(SUM(T.JXGZL), 0) AS TEACHWORKLOAD FROM XXFW_JXGZL T
        <where>
            <if test="teacherNum != null and teacherNum != ''">
                AND T.JGH = #{teacherNum}
            </if>
        </where>
        GROUP BY TO_CHAR(T.ND, 'YYYY')
        </select>

  - 注意：<result column="TEACHWORKLOAD" property="count" jdbcType="INTEGER"/>，这里的column的值不能用COALESCE(SUM(T.JXGZL), 0)，要用别名TEACHWORKLOAD，mybatis不能识别到这种列名，mybatis尽量用别名


#### 时间格式
  - 有些时间格式通过hh和HH的大小写区分12小时制和24小时制
  - yyyy-MM-dd HH:mm:ss表示24小时制
  - yyyy-MM-dd hh:mm:ss表示12小时制，比如现在是下午2点19分，只会显示2019-05-13 02:06:56，而不会显示2019-05-13 14:06:56

















































