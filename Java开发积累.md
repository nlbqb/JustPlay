# java后端

## java类型转换

### 1.Date转String

```java
new SimpleDateFormat("yyyy-MM-dd").format(#)  //#:Date类型的变量
```

### 2.String转Date

```java
new SimpleDateFormat("yyyy-MM-dd").parse(#)  //#:String类型的变量
```

### 3.JsonObject转java对象

```java
MsFinishProject msFinishProject = JSON.toJavaObject(formData, MsFinishProject.class);
```

### 4.java对象转JsonObject

```java
JSONObject jsonObject = JSONObject.parseObject(JSON.toJSONString(detail));
JSONObject jsonObject = (JSONObject) JSON.toJSON(detail);
```

### 5.Json字符串转java对象

```java
ObjectMapper objectMapper = new ObjectMapper();
String s = "{\"name\":\"熊大\",\"sex\":\"男\",\"age\":15}";
Map<String, Object> map = objectMapper.readValue(s, new TypeReference<Map<String, Object>>(){});
System.out.println("user = " + map);
-------------------------------------
user = {name=熊大, sex=男, age=15}
```

### 6.java对象转Json字符串

```java
User user = new User()
    .setName("熊大")
    .setSex("男")
    .setAge(15);
ObjectMapper objectMapper = new ObjectMapper(); //ObjectMapper是Jackson中的核心类，他用来完成JSON的读写操作。如果你使用Spring Boot，那么ObjectMapper就已经存在于你的Spring容器中了
String s = objectMapper.writeValueAsString(user);
--------------------------------------
s = {"name":"熊大","sex":"男","age":15}
```



## 集合List

#### 1.列表中，不同数据行之前插入一条相同数据

```java
@Test
public void linkListAddTest() {
    LinkedList<Integer> list = new LinkedList<>();
    list.add(1);
    list.add(1);
    list.add(2);
    list.add(2);
    list.add(3);
    list.add(3);

    int flag = list.get(0); //初始化flag
    //
    for (int i = 0; i < list.size(); i++) {
        if (flag != list.get(i)) {
            flag = list.get(i);
            list.add(i, 99);
        }
        System.out.println("element" + i + " = " + list.get(i));
    }
}

------------------------------------------------------------------
element0 = 1
element1 = 1
element2 = 99
element3 = 2
element4 = 2
element5 = 99
element6 = 3
element7 = 3
```

### 2.初始化指定数量的List

```java
List<BigDecimal> nowYearDataList1 = new ArrayList<>(Collections.nCopies(12, BigDecimal.ZERO)); //初始化12个类型为BigDecimal的List，且初始值为0
```

### 3.排序

```java
Collections.reverse(detailList); //单纯的倒置元素
```

### 4.ArrayList扩容

- 默认创建后，添加第一个元素开始扩容成10
- 之后1.5倍扩容，10--->15--->22--->33

## 异步执行代码

```java
public class AsyncExample {

    public static void main(String[] args) {
        // 创建一个CompletableFuture对象
        CompletableFuture.supplyAsync(() -> {
            // 异步执行的代码逻辑
            // 这里可以是长时间运行的任务，或者是需要等待的操作
            Map<String, Object> data = FileUtil.getDataByFile("CrossRecords7Day.txt");
            Map<String, Object> data1 = FileUtil.getDataByFile("CrossRecords7Day.txt");
            Map<String, Object> data2 = FileUtil.getDataByFile("CrossRecords7Day.txt");
            Map<String, Object> data3 = FileUtil.getDataByFile("CrossRecords7Day.txt");
            Map<String, Object> data4 = FileUtil.getDataByFile("CrossRecords7Day.txt");
            log.info("data:" + data4);
            // 返回结果
            return "Hello, world!";
        }).thenAcceptAsync(result -> {
            // 结果处理的代码逻辑
            // 这里是在异步任务结束之后执行的代码
            System.out.println("Result: " + result);
            // 可以继续写后续的处理逻辑
        });
        // 可以在这里编写其他的代码逻辑，不需要等待异步任务的执行结果
		//比如说，先返回结果，然后后端慢慢执行代码
    	return Result.OK();
    }

}
```

## 多线程使用

```java
/**
 * 成员变量创建线程池
*/
private static final ExecutorService executor = new ThreadPoolExecutor(5, 10, 60L, TimeUnit.SECONDS, new ArrayBlockingQueue<>(10));

//3.多线程处理数据
//3.1 处理库存统计数据
CompletableFuture<Void> task1 = CompletableFuture.runAsync(() ->
	dealStoreStatData(ylMonthList, yData1, yData2, storeStatDataList), executor);
//3.2 处理违章事件统计数据
CompletableFuture<Void> task2 = CompletableFuture.runAsync(() ->
    dealViolateStatData(ylMonthList, yData3, violateStatDataList), executor);
//3.3 处理出勤统计数据
CompletableFuture<Void> task3 = CompletableFuture.runAsync(() ->
    dealAttendStatData(ylMonthList, yData4, yData5, attendStatDataList), executor);
//等待完成
try {
	CompletableFuture.allOf(task1).get();
	CompletableFuture.allOf(task2).get();
	CompletableFuture.allOf(task3).get();
} catch (InterruptedException | ExecutionException e) {
	e.printStackTrace();
}
```



## 类的复制

#### 情况1

如果两个类的字段完全相同，你可以使用Apache Commons Lang库中的`BeanUtils.copyProperties()`方法来复制一个对象的属性到另一个对象

```java
public static void main(String[] args) throws Exception {
    SourceClass source = new SourceClass();
    source.setField1("value1");
    source.setField2(123);

    TargetClass target = new TargetClass();
    BeanUtils.copyProperties(target, source);

    System.out.println(target.getField1()); // 输出 value1
    System.out.println(target.getField2()); // 输出 123
}
```

### 深clone-序列化



## stream流

#### 1.排序

```java
//正序
list.stream().sorted(Comparator.comparing(o -> (BigDecimal) o.get("efficientRate"))).collect(Collectors.toList())

//倒序1
list.stream().sorted(Comparator.comparing(o -> (BigDecimal) o.get("efficientRate")).reversed()).collect(Collectors.toList())   
//倒序2
list.stream().sorted((map1, map2) -> ((BigDecimal) map2.get("efficientRate")).compareTo((BigDecimal) map1.get("efficientRate"))).collect(Collectors.toList())
```

#### 2.手动分页

```java
List<User> list = new ArrayList<>();

List<User> subList = list.stream().skip((pageNo-1)*pageSize).limit(pageSize).
									collect(Collectors.toList());
```





## 框架demo

### web项目

#### Controller

##### 框架

```java
@Api(tags="供应商关系管理")
@RestController
@RequestMapping("/purchase/relation/ylSupplierRelationManage")
@Slf4j
public class YlSupplierRelationManageController{
    @Resource
	private IYlSupplierRelationManageService ylSupplierRelationManageService;
    
    @ApiOperation(value="供应商关系管理-分页列表查询", notes="供应商关系管理-分页列表查询")
	@GetMapping(value = "/list")
	public Result<?> queryPageList() {
		return ylSupplierRelationManageService.queryPageList();
	}
}
```

##### 方法demo

```java
/**
	  * 分页列表查询
	  * @param pageNo 页码
	  * @param pageSize 页数
	  * @param supplier 供应商
	  * @param inspectDate 考察时间
	  * @return Result<?> 分页列表查询数据结果
	  */
	@AutoLog(value = "供应商关系管理-分页列表查询")
	@ApiOperation(value="供应商关系管理-分页列表查询", notes="供应商关系管理-分页列表查询")
	@GetMapping(value = "/list")
	public Result<?> queryPageList(@ApiParam("页码") @RequestParam(name="pageNo", defaultValue="1") Integer pageNo,
								   @ApiParam("页数") @RequestParam(name="pageSize", defaultValue="10") Integer pageSize,
								   @ApiParam("供应商") @RequestParam(name="supplier", required = false) String supplier,
								   @ApiParam("考察时间") @RequestParam(name="inspectDate", required = false) Integer inspectDate) {
		Map<String, Object> paramsMap = new HashMap<>();
		paramsMap.put("supplier", supplier);
		paramsMap.put("inspectDate", inspectDate);
		return ylSupplierRelationManageService.queryPageList(pageNo, pageSize, paramsMap);
	}
-----------------------------------------------------------------
    @ApiOperation(value="供应商关系管理-添加", notes="供应商关系管理-添加")
	@PostMapping(value = "/add")
	public Result<?> add(@RequestBody YlSupplierRelationManage ylSupplierRelationManage) {
		ylSupplierRelationManageService.save(ylSupplierRelationManage);
		return Result.OK("添加成功！");
	}
```

#### IService

##### 框架

```java
public interface IYlSupplierRelationManageService {

    /**
     * 分页列表查询
     * @param pageNo 页码
     * @param pageSize 页数
     * @param paramsMap 查询参数
     * @return Result<?> 分页列表
     */
    Result<?> queryPageList(Integer pageNo, Integer pageSize, Map<String, Object> paramsMap);
}
```

##### 方法demo

```java
/**
     * 分页列表查询
     * @param pageNo 页码
     * @param pageSize 页数
     * @param paramsMap 查询参数
     * @return Result<?> 分页列表
     */
    Result<?> queryPageList(Integer pageNo, Integer pageSize, Map<String, Object> paramsMap);
```

#### serviceImpl

##### 框架

```java
@Service
public class YlSupplierRelationManageServiceImpl implements IYlSupplierRelationManageService {

    @Resource
    private YlSupplierRelationManageMapper ylSupplierRelationManageMapper;
    
    @Override
    public Result<?> queryPageList() {
        ...
    }
}
```

##### 方法demo

```java
@Override
    public Result<?> queryPageList(Integer pageNo, Integer pageSize, Map<String, Object> paramsMap) {
        Page<Map<String, Object>> page = new Page<>(pageNo, pageSize);
        IPage<Map<String, Object>> pageList = ylSupplierRelationManageMapper.queryPageList(page, paramsMap);
        return Result.OK(pageList);
    }
```

#### IMapper

##### 框架

```java
public interface YlSupplierRelationManageMapper {

    /**
     * 分页列表查询
     * @param page 分页参数
     * @param paramsMap 查询参数
     * @return IPage<Map<String, Object>> 分页列表
     */
    IPage<Map<String, Object>> queryPageList(Page<Map<String, Object>> page, @Param("paramsMap") Map<String, Object> paramsMap);
}
```

##### 方法demo

```java
/**
     * 分页列表查询
     * @param page 分页参数
     * @param paramsMap 查询参数
     * @return IPage<Map<String, Object>> 分页列表
     */
    IPage<Map<String, Object>> queryPageList(Page<Map<String, Object>> page, @Param("paramsMap") Map<String, Object> paramsMap);
```

#### mapper.xml

##### 框架

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="org.jeecg.youlian.YlSupplierRelationManageMapper">

</mapper>
```

##### 方法demo

```java
<select id="queryPageList" resultType="java.util.Map">
        SELECT
            supplier_name AS supplierName,
            create_by AS createBy,
            create_time AS createTime
        FROM yl_supplier_relation_manage
        <where>
            <if test="paramsMap.supplier != null and paramsMap.supplier !=''">
                AND supplier_name LIKE CONCAT('%',#{paramsMap.supplier},'%')
            </if>
            <if test="paramsMap.inspectDate != null and paramsMap.inspectDate !=''">
                AND (YEAR(inspect_start_date) = #{paramsMap.inspectDate} OR YEAR(inspect_end_date) = #{paramsMap.inspectDate})
            </if>
        </where>
    </select>
```

#### entity

##### demo

```java
@Data
@TableName("yl_bad_behavior")
@Accessors(chain = true)
@EqualsAndHashCode(callSuper = false)
@ApiModel(value="yl_bad_behavior对象", description="不良行为及等级调整")
public class YlBadBehavior implements Serializable {
    private static final long serialVersionUID = 1L;

	/**主键*/
	@TableId(type = IdType.ASSIGN_ID)
    @ApiModelProperty(value = "主键")
    private String id;
	/**采购员*/
	@Excel(name = "采购员", width = 15, dictTable = "sys_user", dicText = "realname", dicCode = "realname")
	@Dict(dictTable = "sys_user", dicText = "realname", dicCode = "realname")
    @ApiModelProperty(value = "采购员")
    private String purchaser;
	/**反馈部门*/
	@Excel(name = "反馈部门", width = 15, dictTable = "sys_depart", dicText = "depart_name", dicCode = "depart_name")
	@Dict(dictTable = "sys_depart", dicText = "depart_name", dicCode = "depart_name")
    @ApiModelProperty(value = "反馈部门")
    private String feedbackDepartment;
	/**年度评为D*/
	@Excel(name = "年度评为D", width = 15, dicCode = "yn")
	@Dict(dicCode = "yn")
    @ApiModelProperty(value = "年度评为D")
    private Integer yearDFlag;
	/**暂停中选资格/月*/
	@Excel(name = "暂停中选资格/月", width = 15)
    @ApiModelProperty(value = "暂停中选资格/月")
    private Integer stopQualificationMonth;
	/**最终处理结果*/
	@Excel(name = "最终处理结果", width = 15)
    @ApiModelProperty(value = "最终处理结果")
    private String finalDealResult;
	/**赔款金额*/
	@Excel(name = "赔款金额", width = 15)
    @ApiModelProperty(value = "赔款金额")
    private BigDecimal payment;
	/**整改类型*/
	@Excel(name = "整改类型", width = 15, dicCode = "rectify_type")
	@Dict(dicCode = "rectify_type")
    @ApiModelProperty(value = "整改类型")
    private Integer rectifyType;
	/**处理/整改完成时间*/
	@Excel(name = "处理/整改完成时间", width = 15, format = "yyyy-MM-dd")
	@JsonFormat(timezone = "GMT+8",pattern = "yyyy-MM-dd")
    @DateTimeFormat(pattern="yyyy-MM-dd")
    @ApiModelProperty(value = "处理/整改完成时间")
    private Date rectifyEndDate;
	/**制单人*/
	@Dict(dictTable = "sys_user", dicText = "realname", dicCode = "username")
    @ApiModelProperty(value = "制单人")
    private String createBy;
	/**制单日期*/
	@JsonFormat(timezone = "GMT+8",pattern = "yyyy-MM-dd")
    @DateTimeFormat(pattern="yyyy-MM-dd")
    @ApiModelProperty(value = "制单日期")
    private Date createTime;
}
```

#### vo

```java
@Data
@EqualsAndHashCode(callSuper = false)
public class YlSupplierInfoVo extends YlSupplierInfo {
    @ApiModelProperty(value = "供应商信息-证书")
    private List<YlSupplierInfoCertificate> supplierInfoCertificateList;

    @ApiModelProperty(value = "供应商信息-供应品类")
    private List<YlSupplierInfoMaterial> supplierInfoMaterialList;

    @ApiModelProperty(value = "供应商信息-代理/经销")
    private List<YlSupplierInfoAgency> supplierInfoAgencyList;

    @ApiModelProperty(value = "供应商信息-联系人")
    private List<YlSupplierInfoContact> supplierInfoContactList;
}
```

#### vo保存

```java
@Override
@Transactional
public Result<?> addOrEditVo(YlSupplierContractVo supplierContractVo) {
		//获得合同台账明细列表
		List<YlSupplierContractDetail> supplierContractDetailList = supplierContractVo.getSupplierContractDetailList();
		//获得合同台账主表信息
		YlSupplierContract supplierContract = new YlSupplierContract();
		BeanUtils.copyProperties(supplierContractVo, supplierContract);

		try {
			//保存或编辑主表
			this.saveOrUpdate(supplierContract);
			//2.保存子表
			//2.1 赋值外键
			if(!CollectionUtils.isEmpty(supplierContractDetailList)){
				supplierContractDetailList.stream()
						.filter(item -> StringUtils.isEmpty(item.getMainCode()))
						.forEach(item -> item.setMainCode(supplierContract.getId()));
			}
			//2.2 保存或编辑子表
			ylSupplierContractDetailService.saveOrUpdateBatch(supplierContractDetailList);

		}catch (Exception e){
			throw new RuntimeException(e.getMessage());
		}
		return Result.OK("操作成功");
	}
```

## 导入框架代码

```java
@Override
    @Transactional(rollbackFor = Exception.class)
    public Result<?> importExcel(HttpServletRequest request, HttpServletResponse response) {
        MultipartHttpServletRequest multipartRequest = (MultipartHttpServletRequest) request;
        Map<String, MultipartFile> fileMap = multipartRequest.getFileMap();
        for (Map.Entry<String, MultipartFile> entity : fileMap.entrySet()) {
            MultipartFile file = entity.getValue();// 获取上传文件对象
            try {
                Workbook workbook = WorkbookFactory.create(file.getInputStream());
                //获取第一个sheet
                Sheet sheet = workbook.getSheetAt(0);
                Row row; //当前行
                
                
                return Result.OK("文件导入成功");
            } catch (Exception e) {
                log.error(e.getMessage(), e);
                return Result.error("文件导入失败:" + e.getMessage());
            } finally {
                try {
                    file.getInputStream().close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
        return Result.error("文件导入失败！");
    }
```

## 数组处理

### 1.数组合并

```java
方法：System.arraycopy(Object src, int srcPos, Object dest, int destPos, int length);
参数：
src – 源数组。
srcPos – 源数组中的起始位置。
dest – 目标数组。
destPos – 目标数据中的起始位置。
length – 要复制的数组元素的数量

int[] c = new int[a.length + b.length];
System.arraycopy(a,0,c,0,a.length); //将数组 a 中的元素复制到数组 c 的前半部分
System.arraycopy(b,0,c,a.length,b.length); //将数组 b 中的元素复制到数组 c 的后半部分。
```

## 定时任务

```java
//每x分钟同步近1h的数据 10:03 ->[10:00-11:00]
Calendar calendar = Calendar.getInstance();
String startTime = DateUtil.formatDate(calendar.getTime(), "yyyy-MM-dd'T'hh:00:00.000+08:00"); //开始时间
calendar.add(Calendar.HOUR_OF_DAY, 1);
String endTime = DateUtil.formatDate(calendar.getTime(), "yyyy-MM-dd'T'hh:00:00.000+08:00"); //结束时间
System.out.println("startTime = " + startTime);
System.out.println("endTime = " + endTime);
```



# Linux

## Linux架构

![img](https://img-blog.csdnimg.cn/4c458d36fd4848a48dc811da4652089a.png)

## 部署

### Linux部署Jar包

#### 1.前台部署

```
java -jar xxx.jar
```

#### 2.后台部署

```
nohup java -jar -Xms1024m -Xmx1024m -XX:+UseConcMarkSweepGC xxx.jar &
```

```
nohup java -jar /appdata/dtsum/aoya/aoya-3.2.4-7776.jar 2>&1 &

后台模式启动时2>&1 &的含义

“2”：代表标准错误输出（stderr）
“1”：代表标准输出（stdout）
“>&”：代表合并输出文件 （参考1、参考2）
最后的"&"：代表把命令放到后台执行
2>&1含义：将标准错误输出重定向到标准输出
```

### Linux部署Java

https://www.cnblogs.com/antLaddie/p/17599359.html



### 数据库定时备份

**1.新建shell文件(脚本)**

目录结构：liufeng ---- backup ---- sql

​													 ---- backup.sh

 							    ---- web

​							     ---- liufeng-8801-1.04.jar

```
1、新建shell文件
[root@iZm5e8z49es2m3u7u1uxo5Z backup]# vim backup.sh
#...进入文件编辑界面，增加以下内容：
{
#!/bin/sh
mysqldump -uroot -pdtsum123456 liufengerp | gzip > /home/liufeng/backup/sql/liufeng_`date +%Y%m%d%H%M%S`.sql.gz
find /home/liufeng/backup/sql/ -mtime +10 -type f | xargs rm -f 
}
```

**2.crontab开启定时任务，执行shell脚本**

```
#查看是否开启了crontab
[root@iZm5e8z49es2m3u7u1uxo5Z backup]# crontab -l
no crontab for root

#创建一个crontab，每分钟执行
[root@iZm5e8z49es2m3u7u1uxo5Z backup]# crontab -e
#...进入文件编辑界面，增加以下内容：
{
#定期执行数据库和文件的备份工作
0 1 * * * sh /home/liufeng/backup/backup.sh    //0 1 * * *表示每天凌晨1点执行一次
}
```

**3.查看crontab服务是否启动，启动crontab服务**

```
[root@ecs-d532-1228628 backup]# service crond status    //查看crontab服务状态
[root@ecs-d532-1228628 backup]# service crond restart   //重启crontab服务
```



## 常用命令

### 1.cd命令

[cd命令详解](https://blog.csdn.net/bk_hyj/article/details/94629845)

### 2.删除命令

```
rm sql      //删除文件
rm -f sql   //强制删除文件

rm -r backup      //删除文件夹，及其子目录和文件
rm -rf backup     //强制删除文件夹，及其子目录和文件
```

### 3.创建文件夹

```
mkdir backup    //创建一个文件夹

mkdir -p backup/sql     //创建文件夹及其子文件夹
```

### 4.编辑文件

```
vi/vim backup.sh    //编辑一个文件
```

#### 4.1编辑完后的操作

```
按Esc退出编辑模式

:q    //退出
:q!   //强制退出
:wq   //保存并退出

```

### 5.查看文件大小

```
ls -lh
```

### 6.查看磁盘大小

```
lsblk
df -h
du -h --max-depth=1
```



# 数据库

## mysql

### 表SQL

```mysql
ALTER TABLE jimu_report_data_source ADD `type` varchar(10) CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci NULL DEFAULT NULL COMMENT '类型(report:报表;drag:仪表盘)';
//给数据表添加一个字段
```



### insert

#### 1.insert(普通)

```mysql
//一行
INSERT INTO user (id,name,age,email) 
VALUES(1,'Jone',18,'test1@baomidou.com');

//多行
INSERT INTO user (id,name,age,email) 
VALUES(1,'Jone',18,'test1@baomidou.com'),
	  (,,,);
```

#### 2.insert(整行)

```mysql
INSERT INTO user 
select 1,'Jone',18,'test1@baomidou.com'

union

select 2,'Jo',19,'sest1@basmidou.com'
```

#### 3.insert(批量)

```mysql
<insertid="batchInsert"parameterType="java.util.List">    //传的是list
    insert into USER (id,name)
    values 
        <foreachcollection="list" item="model" index="index" separator=","> 
        (#{model.id},#{model.name}) 
        </foreach> 
</insert>
```

#### 4.insert(若与**唯一** unique_code索引一样，则按unique_code更新)

```mysql
<insert id="saveOrUpdateBatchByUk">
        insert into yl_equipment(id, create_time, equipment_code, equipment_name, equipment_spec, equipment_type, equipment_workshop,
        charge_person, contact_way, manufacturer)
        values
        <foreach collection="list" index="index" item="item" separator=",">
            (#{item.id}, NOW(), #{item.equipmentCode}, #{item.equipmentName}, #{item.equipmentSpec}, #{item.equipmentType}, #{item.equipmentWorkshop},
            #{item.chargePerson}, #{item.contactWay}, #{item.manufacturer})
        </foreach>
        on duplicate key update //此例中唯一索引是equipment_code
        update_time = NOW(),
        equipment_name = values(equipment_name),
        equipment_spec = values(equipment_spec),
        equipment_type = values(equipment_type),
        equipment_workshop = values(equipment_workshop),
        charge_person = values(charge_person),
        contact_way = values(contact_way),
        manufacturer = values(manufacturer)
    </insert>
```

#### 技巧

1.可用case语句+分组聚合，将行列数据调换

### update

#### 1.update(普通)

```mysql
UPDATE yl_drawing_error_rate_summary
SET had_distribute_num = had_distribute_num + 1,
    a1_num = a1_num + 1
WHERE ship_name = '文克胜利'
	AND major = '船舾专业'
```

### delete

#### 1.delete(普通)

```mysql
DELETE FROM ms_model
WHERE DATE_FORMAT(create_time,'%Y-%m-%d %H') = '2024-03-04 18'
```

### 日期

#### 1.日期类型格式化

```mysql
DATE_FORMAT(create_time, '%Y-%m-%d %H:%i:%s')  //2024-03-07 23:14:01
```

### 函数

#### 1.sum()

自动判断数字或数字字符串进行转换求和，返回的是Bigdecimal



### 数据类型

![](https://img-blog.csdnimg.cn/img_convert/bab7db2696cd0f2e6e7310c629cc9bfa.png)

- int(1) 和int(10)存储的数据都是4个字节的范围，1和10
- ZEROFILL只在整数类型上有效，对于其他数据类型（如浮点数）不适用。
- 在mysql8.0 中，只有设置了ZEROFILL，才能看到int(),否则都是int

### sql优化

```mysql
/*下图为小表A驱动大表B*/
SELECT 
	*
FROM tableA A /*100条*/
LEFT JOIN tableB B ON B.id = A.id; /*1000000条*/

/*等价于*/
for(int i=100;.......)
{
     for(int j=1000000;......)
     {}
}
```

**索引类型**：1.普通索引、2.唯一索引、3.主键索引、4.复合索引(组合索引)

**SQL在执行的过程**中会有不同的临时中间表，一般是按照如下顺序：

1. from
2. on
3. join
4. where
5. goup by
6. having + 聚合函数
7. select
8. order by
9. limit

# Redis

## 基础命令

### 0.应用场景

![img](https://img2018.cnblogs.com/blog/798174/201903/798174-20190321203330649-499029266.png)

### 1.客户端登录

```
redis-cli -h 127.0.0.1 -p 6379
```

### 2.查询所有的keys

```
keys *
```

# GIT

## 1.新建项目(远程主动)

1.1 GitHub上新建仓库、分支

1.2 运行git clone https://github.com/xxx/xxx.git，将远程的项目同步到本地电脑

1.3 本地开发

## 2.新建项目(本地主动)

1.1 在idea本地开发项目

1.2 git init将本地开发项目由git托管

1.2 idea-setting-github中绑定自己的github账号

1.3 在github新建仓库

1.4 在idea中manage remotes中添加远程分支，例如：远程分支https://github.com/LuoWenSa/Documents.git

1.5 fetch后直接把代码push到远程空仓库

## 3.常用命令

```xml-dtd
git init: 使文件夹被git管理起来

git branch 查看本地分支
git branch -r 查看远程分支
git branch -a 查看本地和远程所有分支

git checkout [java] 切换分支

git add [文件名] 将新文件添加到暂存区

git add . 添加当前目录下的所有文件

git commit -m "commit的备注" 将缓存区内容添加到本地仓库中

git status 命令可以用来查看相关文件的状态

git remote 命令可以查看当前有哪些远程仓库

git push [仓库名](origin) [远程分支] 命令用于推送你的新分支与数据到某个远端仓库命令

git pull [仓库名](origin) [远程分支] 命令用于从另一个存储库或本地分支获取并集成(整合)
```

# 虚拟机

## 安装VMware

https://www.ddooo.com/softdown/177981.htm

## 安装Linux系统

https://blog.csdn.net/2401_83621331/article/details/138237169

## 配置虚拟机网络

https://blog.csdn.net/hualinger/article/details/131188141

# 基础算法

## 1.冒泡排序

```java
@Test
public void popSortTest() {
    int[] a = new int[]{2, 3, 1, 4, 5, 7, 6};
    int temp;
    for (int i = 0; i < a.length - 1; i++) { //每一趟确定一个元素的位置，只需要n-1趟
        for(int j = 0; j < a.length - 1 - i; j++){
            if(a[j] > a[j+1]){
                temp = a[j];
                a[j] = a[j+1];
                a[j+1] = temp;
            }
        }
    }

    for (int i : a) {
        System.out.print(i + " ");
    }
}
----------------------------------------------------------------
//优化：
@Test
public void popSortTest() {
    int[] a = new int[]{2, 3, 1, 4, 5, 7, 6};
    int temp;
    int flag = 0; //哨兵
    for (int i = 0; i < a.length - 1; i++) { //每一趟确定一个元素的位置，只需要n-1趟
        for(int j = 0; j < a.length - 1 - i; j++){
            if(a[j] > a[j+1]){
                temp = a[j];
                a[j] = a[j+1];
                a[j+1] = temp;
                flag = 1; //哨兵
            }
        }

        if (flag == 1) { //哨兵
            flag = 0;
        } else {
            break;
        }
    }

    for (int i : a) {
        System.out.print(i + " ");
    }
}
```

## 2.java队列

```java
//Java 标准库提供了 java.util.Queue 接口来表示队列，它是一个包含多个方法的接口。

//add(E element)：将指定元素添加到队列的尾部。如果队列已满，则抛出异常。
// offer(E element)：将指定元素添加到队列的尾部。如果队列已满，则返回 false。
//remove()：从队列的头部删除并返回一个元素。如果队列为空，则抛出异常。
// poll()：从队列的头部删除并返回一个元素。如果队列为空，则返回 null。
//element()：获取队列的头部元素，但不删除它。如果队列为空，则抛出异常。
// peek()：获取队列的头部元素，但不删除它。如果队列为空，则返回 null。

Queue<T> queue = new LinkedList<>();
```

