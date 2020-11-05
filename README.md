# Mysql-to-SqlServer
mysql语法对比sqlserver



MySql 库表视图及存储转为 Sql Server
																	2020-09-06  刘勇

一、	函数部分：  

      函数	
                               Mysql	                                                Sql Server
      时间累加	                date_add	                                              dateadd
      获取当前时间(返回 date类型)	current_day()	                                          convert(date,getdate(),23)
                                                                                       23：时间格式代码，具体其他格式可查看时间代码对照表
                                                                                        
      列转行函数	                group_concat ()	                                        STUFF((SELECT ',' + 参数from 表 as t0 where t0.字段= t.字段for xml                                                                                                  path('')),1,1,'')Set 赋值	支持多个变量set赋值	单个变量
      分页	                    Limit	                                                  set statistics time on;
                                                                                      -- 分页查询（通用型）
                                                                                      select top pageSize * 
                                                                                      from (select row_number() 
                                                                                      over(order by sno asc) as rownumber,* 
                                                                                      from student) temp_row
                                                                                      where rownumber>((pageIndex-1)*pageSize);
                                                                                      set statistics time on;
                                                                                      -- 分页查询第2页，每页有10条记录
                                                                                      select top 10 * 
                                                                                      from (select row_number() 
                                                                                      over(order by sno asc) as rownumber,* 
                                                                                      from student) temp_row
                                                                                      where rownumber>10;

      统计总记录数	              found_rows()	                                        @@rowcount
      计算时间差	                 timestampdiff()	                                     Datediff()
      判空	                     ifnull()	                                               isnull()
      临时表创建	                 create temporary table 表  
                                drop table if exists 表	create table 表


      drop table 表
      自增	Auto_increment	Identity(1,1) 
      字段 int, bigint
      赋值	Mysql支持into	Sql server 不支持into

      用 = 进行赋值
      例如：@pageSize = 1

变量（针对于存储过程）	无需加@	需要加@
检查是否存在	例drop procedure if exists usp_mop_backup_month;	MS sql 直接drop/create
IF EXISTS (SELECT 1 FROM sysobjects WHERE name = 'usp_mop_fltj_dept' AND type = 'P')
	DROP PROC usp_mop_fltj_dept
GO
Text字段	不允许有默认值	允许有默认值
每句结束	需要 ；	无
UUID/GUID	UUID()	GUID()
参数类型设置	在参数名前	参数名称后
语句执行	Execute	Exec

获取指定时间（时间减法）
		date_sub(curdate(),INTERVAL WEEKDAY(curdate())  DAY)	CONVERT(date,dateadd(day,-(DATEPART(weekday,dateadd(day,-1,getdate()))-1),getdate()),112)
获取当天是本周第几天	weekday(curdate())	DATEPART(weekday,dateadd(day,-1,getdate()))-1
数据插入	INSERT INTO 表(name) SELECT 字段 FROM dual WHERE NOT EXISTS (SELECT 字段 FROM 表 WHERE 字段 = 参数)	IF NOT EXISTS (SELECT字段FROM 表WHERE i字段= 参数)  INSERT INTO 表(name) SELECT字段
数据插入主键冲突处理（有则更新，无则插入）	


on duplicate	BEGIN TRANSACTION
UPDATE  表  WITH(HOLDLOCK)
SET   字段= 参数
WHERE   ID =参数
IF @@ROWCOUNT<1
BEGIN
INSERT  表
( id, state )
VALUES  (字段, 参数 );
END
COMMIT
		
		

