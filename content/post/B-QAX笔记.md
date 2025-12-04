---
title: "QAX笔记"           # 文章标题
author: "烂柯"              # 文章作者
description: "QAX笔记" # 文章描述信息
date: 2025-09-10                   # 文章编写日期
lastmod: 2025-12-04                # 文章修改日期

tags: [                    # 文章所属标签
    "work book"
]
categories: [              # 文章所属分类
    "work book"
]
keywords: [                # 文章关键词
    "work book"
]
---

# 	笔记

常看笔记
https://www.bookstack.cn/books/effective-java-3rd-chinese



## 一、后台服务

#### 1.打包部署

```
在gitlub中的Tags中new一个tag，命名     v1.0-alpha.16  ------->  v1.0-alpha.17
1、cd /data/s/services/dayu_config/dayu-confs/configs/
2、vim mods/服务名/artifact/alpha.ini
3、cd /data/s/services/dayu_config/dayu-confs/configs/ && rg -e single _secret-ukey
4、kubectl get pod  -n dev | grep secret-ukey
kubectl get pod | grep secret-ukey
5、查看日志：kubectl logs -f --tail=100 data-transmission-66b8bf7558-dxnsn -n dev

重启服务：（直接删pod）
kubectl delete pod 服务名
```

#### 2.打一键升级包

```
参考文档：
https://cooperate-sop-doc-situation-situation-sop-8fb60afe02c747db4e8e5.doc-biz.qianxin-inc.cn/develop/overall/dev/upgrade-template.html

账号密码：
situation-af
hcK%R*UJXeYesYsw2023

(1)添加物料
1.在images.txt中加入镜像
2.设置compressTarGz.sh中PROJECT_NAME(命名规则为 态势大版本号加上项目简称，例如：R3.10.6_LNWX)
3./pre_other：发布服务之前需要执行的内容，通过脚本执行。
    /init_db：存放需要额外执行的sql，sql文件名字与数据库名保持一致
    /dict：需要导入到玄机字典文件，xls文件
    /asset_field：资产扩展字段文件，json文件
  /mods：本次需要升级的mods文件
  /post_other：发布服务之后需要执行的内容，通过脚本执行
  /zeus_file：zeus权限文件
  
(2)将文件拉到后台服务中

(3)赋予执行权限
chmod +x compressTarGz.sh

(4)执行脚本
./compressTarGz.sh

(5)现场执行
1.tar -zxvf R3.10.6_BJGD_update_20250822132538.tar.gz
2.cd R3.10.6_BJGD_update_20250822132538/
3.bash upgrade.sh
4.查看服务是否起来
```

#### 3.命令

```
ll/ls
cat beta.ini
cat alpha.ini > beta.ini

docker pull situation-docker-alpha.af-biz.qianxin-inc.cn/secret-ukey:6134.0.18.fjwx-alpha

1.查看用x86还是arm：
uname -m
查看服务ip：
hostname -i

2.查看cm：（config-map）
kubectl describe cm gateway

3.查看服务：
kubectl get pod （默认进去命名空间的default）
kubectl get cn（查看命名空间，包含default）
kubectl get pod -n uae-flink-single（进去其他命名空间）

4.查看服务版本号：（镜像服务名：situation...）
kubectl describe pod 服务名  | grep Image

5.查看服务资源：
kubectl get svc | grep hakkero

6.修改服务资源：
kubectl edit svc hakkero

7.改状态机：
kubectl edit svc hakkero
type: ClusterIP 改为 NodePort

8.查看文件：
cat 文件名

9.在目录下创建文件夹：
mkdir 目录名
touch 文件名.txt

10.进入文件：
vim 文件路径

11.删除文件
（1）进到目录下
（2）rm -rf ./文件名

12.复制文件：
cp ./add_incident.yaml add_incident.yaml111（本目录下）
cp ./add_incident_1001.yaml  /data/s/services/ts_config/ts-zeus/（新目录下）

13.运行可执行文件：
（1）到目录下    /data/s/services/ts_config/ts-zeus
（2）执行      ./zeus-permission-sdk -path add_weave_plan.yaml

14.查看user-info信息：
kubectl logs -f gateway-6856965d9f-7ckww | grep user

15.网关pod：
kubectl get pod | grep gate

16.后台服务查数据库：
export PGPASSWORD='A7GaKXT3JEY3_Qrh' && /data/s/services/pgsql/server/bin/psql -U postgres -p 15432 -d postgres(数据库名)

17.连接数据库ip加白名单
iptables -I INPUT 1 -s  10.43.185.114 -p tcp -j ACCEPT
iptables -I INPUT -s 10.43.185.114 -j ACCEPT
iptables -t mangle -I PREROUTING -s 10.43.185.114 -j ACCEPT

18.进入k8s容器：
kubectl exec -it framework-message-center-5b5f676c6d-mj9l7 -- bash

19.查看分布在哪个节点：
kubectl get pod -o wide | grep app
查看系统hosts文件内容(包含节点ip):
cat /etc/hosts
查看服务的端口号信息
kubectl get svc | grep analysis-search
修改配置文件暴露服务端口  将该配置属性改type: ClusterIP修为NodePort
kubectl edit svc analysis-search

查看k8s集群节点信息：
kubectl get nodes

20.查看日志：
kubectl logs framework-message-center-6bb87f68cf-6q8r4 | grep -C 20 'GENIF202509227'
```

#### 4.后台账号

`situation-configs/platform/env_single/cluster.ini`

#### 5.后台配置文件

```
1.后台服务配置文件：（mods/服务名/sys-res/sys_res-reuse.yml）

needle:
  audit:
    topic: dayu_audit
    kafka: {{ .Values.config.envKafkaBrokers | join "," }}
    
2.给网关加白：（mods/gateway/values.yaml）
uaa:
  enable: true
  ignore-url:
    - /dayu/v1/asset/defenceSystem/synData
```



## 二、接口

#### 1.接口注解注意事项

```
getMapping请求参数为 `对象` 时不能用@RequestBody，用@SpringQueryMap

getMapping请求参数为 `单个值` 是用@RequestParam
注：@RequestParam(value = "org_no", required = false) String orgNo

getMapping请求参数在路径上用@PathVariable
```



#### 2.API接口注解

```
对象参数
@Schema(description = "组织机构编码")

controller层上
@Tag(name = "MT安全机构")

接口上
@Operation(summary = "组织名称为公司的列表接口")

传参
@Parameter(description = "组织名称", required = true)
eg：
public List<OrgCompanyResultDto> orgNameList(@Parameter(description = "组织名称", required = true) @RequestParam(value = "org_name", required = false) String orgName){}
```



#### 3.实体类注解

updateById（T entity）和removeById(T entity)

实体类主键id要加**@Table**注解

```java
@TableId(value = "org_no", type = IdType.NONE)

type类型（默认AUTO）
AUTO            数据库自增，可以手动赋值
NONE            无策略，手动赋值
INPUT           必须手动赋值
ASSIGN_ID       雪花算法生成Long类型，可以手动赋值
ASSIGN_UUID     生成UUID字符串
```

```java
//  @Builder、@NoArgsConstructor、@AllArgsConstructor最好一块使用
//  单独使用@Builder赋值时可能因为没有无参构造方法报错
@Data
@TableName("app_business")
@Builder
@NoArgsConstructor
@AllArgsConstructor
```

```java
//需要给数据库赋null值，实体类是加 @TableField 注解
@TableField(updateStrategy = FieldStrategy.IGNORED)

IGNORED         已弃用的策略，表示忽略字段的默认行为。
ALWAYS          总是包含该字段，无论其值如何，都会参与数据库操作。
NOT_NULL        当字段值不为 null 时，才包含该字段参与数据库操作。
NOT_EMPTY       当字段值不为空（通常指不为 null 且不为空字符串）时，才包含该字段参与数据库操作。
DEFAULT         使用默认策略，通常根据字段的默认值或其他默认规则来决定是否包含该字段。
NEVER           从不包含该字段，即该字段不会参与任何数据库操作。
```





## 三、git

#### 1.配置dayu/v1路径    (提测需要加上)

```
前端加dayu/v1配置
dockyard服务端部署配置找git（开发分支）

1.mods/服务名
（1）如果提测后，/values.yaml中alpha改为beta
image:
  repository: situation-docker-alpha.af-biz.qianxin-inc.cn
中的alpha改为beta

2.mods/gateway/values.yaml

3.mods/dayu_mods.rgl
（1）在mod taishi下flow _all加上服务名
（2）flow _服务名 : _flow_begin,${C1},${M1} : vega_flow._helm {
      pkg = "服务名"; SERVICE_PREFIX  = "/服务名" ;
   }
```



#### 2.查询代码行数

```
git log --author="L-zhangliming" --format='%aN' | sort -u | while read name; do
    echo -en "$name\t"; 
    git log --author="$name" --pretty=tformat: --since=2025-09-05 --until=2025-09-11 --numstat | awk '{ add += $1; subs += $2; loc += $1 - $2 } END { printf "added lines: %s, removed lines: %s, total lines: %s\n", add, subs, loc }';
done

```



## 四、Dockyard

### 1.新增服务

```
模块管理 -> 微服务管理 -> 新增服务

微服务所属产品： 态势感知-第一事业部

微服务名称： 服务名(中文)

微服务标识： 服务名+后缀名   （项目管理 -> 全部项目 -> 后缀名    后缀名一般C开头）

微服务创建方式： 关联已存在的Git仓库

微服务类型： situation-program

Gitlab Group: situation/situation-java-program/situation-program

选择已存在的 Git 仓库: 输入服务名会自动出来
```



### 2.迭代分支

```
微服务右上角按钮 -> 迭代版本 -> 创建迭代版本

迭代类型: Custom

基准类型: 基准版本
```



## 五、数据库

#### 1.连接数据库ip加白名单
```
iptables -I INPUT 1 -s  10.43.185.114 -p tcp -j ACCEPT

iptables -I INPUT -s 10.111.14.70 -j ACCEPT

iptables -t mangle -I PREROUTING -s 10.111.14.70 -j ACCEPT
```





#### 2.pg数据库id自增

```
-- 创建序列
CREATE SEQUENCE t_traceability_ip_id_seq;

-- 修改字段默认值（让字段具备自增能力）
ALTER TABLE t_traceability_ip
    ALTER COLUMN id SET DEFAULT nextval('t_traceability_ip_id_seq');
    
-- 修改自增序号
SELECT setval('t_traceability_ip_id_seq', (SELECT MAX(id) FROM t_traceability_ip) + 1);
```



#### 3.跨库表连接

```
1.在写sql的数据库执行  SELECT * FROM pg_extension WHERE extname = 'dblink';

2.未安装则执行  CREATE EXTENSION IF NOT EXISTS dblink;

3.写sql，在 bi 中 user=postgres 不允许写
SELECT 
    a.team_type,
    a.problem_type,s
    a.score,
    b.name
FROM attack_defense_info a
JOIN (
    SELECT id, name
    FROM dblink('dbname=governance user=postgres password=A7GaKXT3JEY3_Qrh host=10.57.146.31 port=15432',
                'SELECT id, name FROM organization')
    AS remote_table(id int8, name VARCHAR)
) b ON a.org::BIGINT = b.id
```



#### 4.后台服务查数据库

```
export PGPASSWORD='A7GaKXT3JEY3_Qrh' && /data/s/services/pgsql/server/bin/psql -U postgres -p 15432 -d postgres(数据库名)

PGPASSWORD pg数据库密码
&& 表示前一个命令执行成功后继续执行
/data/s/services/pgsql/server/bin/psql：PostgreSQL 的命令行客户端工具
-U postgres：以用户 postgres 身份登录（PostgreSQL 的默认超级用户）
-p 15432：连接数据库的端口号为 15432
-d postgres：连接的数据库名称为 postgres（默认系统数据库）
```



#### 5.COALESCE（合并）

原理：从左到右依次查找不为null的参数

```
select COALESCE(null,'a','b')...        返回a
select COALESCE(user_name,'abc')...     user_name不为null，返回它的值，为null，返回abc

eg：
WITH
        tddr AS (
        SELECT task_id, COUNT(*) AS disposal_frequency
        FROM t_ddos_disposal_result where api_type = '5.5.3'
        GROUP BY task_id
        )
        SELECT DISTINCT
        tdai.task_id,
        tdai.task_name,
        tdai.user_id,
        tdai.ddos_task_type,
        tdai.task_duration,
        tdai.task_level,
        tdai.task_status,
        tdai.create_time,
        tdai.disposal_start_time,
        tdai.disposal_end_time,
        tdai.user_examine,
        tdai.ip_number,
        COALESCE(tddr.disposal_frequency, 0) AS disposal_frequency
        FROM
        t_ddos_attack_info tdai
        LEFT JOIN tddr ON tdai.task_id = tddr.task_id
        left join t_ddos_ip tdi on tdai.task_id = tdi.task_id
```





## 六、常用

#### 1.代码规范

`Shift+Ctrl+Alt+L`

`Ctrl+Alt+L`



#### 2.值为空抛异常

```
第一种：
1.在实体类上加注解@NotBlank
@NotBlank(message = "任务名称不能为空")
2.在controller层加注解@Valid
eg：public Boolean createTraceability(@Valid @RequestBody TraceabilityInfoDto traceabilityInfoDto){}

第二种：
if (traceabilityInfoDto.getUserExamine() == null || traceabilityInfoDto.getUserExamine().isEmpty()) {
            throw new BaseException(HttpStatus.INTERNAL, "user_examine", "任务审核人不能为空");
        }
```



#### 3.long类型序列化
`@JsonSerialize(using = ToStringSerializer.class)`



#### 4.远程调用

```
启动类
@EnableFeignClients

@FeignClient(url = "${feign.governance:http://governance:8080}", value = "${feign.governance:http://governance:8080}")
public interface GovernanceClient {
    @PostMapping("governance/organization/list")
    List<JSONObject> list(@RequestBody OrgListRequest request);
}
```

```yaml
# feign相关配置
feign:
    httpclient:
        enabled: false
        disable-ssl-validation: true
    okhttp:
        enabled: true
    unit: http://governance:8080
    
    
# feign相关配置(本地)
feign:
    unit: http://10.52.120.33:31323  # 31323在服务中用 kubectl get svc | grep ... 找
    hystrix:
        enabled: true  
```



#### 5.isBlank和isEmpty区别

```
a = null;
b = "";
c = "   ";

isBlank(a)、isBlank(b)、isBlank(c)为true
isEmpty(a)、isEmpty(b)为true，isEmpty(c)为false
```



#### 6.常用工具类

```
StrUtil工具类    String
StrUtil.isNotEmpty()
StrUtil.isNotBlank()

CollUtil工具类    集合
CollUtil.isNotEmpty
CollUtil.newArrayList()
CollUtil.emptyIfNull(list)  list为null，工具类将null转为空集合，避免空指针异常

Collections工具类
Collections.emptyList();  返回空list集合，不能进行任何操作
```



#### 7.Tag初始化

1.git操作：在部署分支上的init_scripts目录下的3-sql，将tag.sql文件放进去

2.idea操作：配置ymal文件，config.java文件要与flyway中的配置对应



#### 8.添加Tag标签

```sql
#数据库运行语句

INSERT INTO public.tag_group(name, description, created_time, updated_time, deleted_time, app, is_preset, is_default,
                             is_accessed, classification, sort, can_edit, can_delete, can_add_child, can_add_partner,
                             can_add_tag, path, parent_id, app_name)
VALUES ('预置分组', '', null, null, null, 'attack_defense_report', true, true, false, '报告类型', 2, false, false, true, true, true,
        null,
        null, '攻防综合管理');


INSERT INTO "public"."tag"("name", "description", "created_time", "updated_time", "deleted_time", "is_preset", "app",
                           "group_id", "is_read_only", "color_code", "can_edit", "can_delete", "is_disabled",
                           "properties", "user_code")
VALUES ('工具依赖风险', NULL, '2025-04-24 00:00:00.000', '2025-04-24 00:00:00.000', NULL, 't', 'attack_defense',
        (SELECT id from tag_group WHERE NAME = '问题类型' AND app = 'attack_defense'), 'f', '#4C9ADF', 't', 'f', 'f',
        '', 'sw')
    ON conflict (name,app) DO NOTHING;
```



#### 9.Flyway+id自增序列（bigserial）

```xml
<dependency>
    <groupId>org.flywaydb</groupId>
    <artifactId>flyway-core</artifactId>
</dependency>
```

```yaml
spring:
    flyway:
        #    flyway 的 clean 命令会删除指定 schema 下的所有 table, 杀伤力太大了, 应该禁掉.
        clean-disabled: false
        #    禁用/启用flyway
        enabled: true
        #    设定 SQL 脚本的目录,多个路径使用逗号分隔, 比如取值为 classpath:db/migration,filesystem:/sql-migrations
        locations: classpath:db/migration/postgres
        #    如果指定 schema 包含了其他表,但没有 flyway schema history 表的话, 在执行 flyway migrate 命令之前, 必须先执行 flyway baseline 命令.
        baseline-on-migrate: true
        #    指定 baseline 的版本号,缺省值为 1, 低于该版本号的 SQL 文件, migrate 的时候被忽略.
        baseline-version: 1.0.101
        #    sql文件编码
        encoding: UTF-8
        #    开发环境最好开启 outOfOrder, 生产环境关闭 outOfOrder .
        out-of-order: false
        baseline-description: baseline init
        table: flyway_third_party_integration_history
```

```
-- 创建序列
CREATE SEQUENCE t_traceability_ip_id_seq;

-- 修改字段默认值（让字段具备自增能力）
ALTER TABLE t_traceability_ip
    ALTER COLUMN id SET DEFAULT nextval('t_traceability_ip_id_seq');
    
-- 修改自增序号
SELECT setval('t_traceability_ip_id_seq', (SELECT MAX(id) FROM t_traceability_ip) + 1);
```

```java
// primary key 主键
create table if not exists public.send_mdm_org(
    org_no                    varchar(32) NOT NULL primary key,
    org_name                  varchar(100) NOT NULL,
   ...
);

// bigserial 序列
create table if not exists public.send_mdm_org(
    org_no                    bigserial,
    org_name                  varchar(100) NOT NULL,
   ...
);

注：
tag_name     varchar(255) COLLATE pg_catalog."default" NOT NULL    √
tag_name     varchar(255) COLLATE NOT NULL                         ×
    
COLLATE pg_catalog."default" collate排序，要加后面的pg_catalog."default"
```



#### 10.导入导出

```xml
<!-- pom.xml -->
<!--Excel数据导出 -->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>easyexcel</artifactId>
    <version>3.0.5</version>
    <exclusions>
        <exclusion>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-compress</artifactId>
        </exclusion>
    </exclusions>
</dependency>
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-compress</artifactId>
    <version>1.21</version>
</dependency>
```

```java
//controller

@Operation(description = "导出数据")
@PostMapping("/file/export")
public void export(@Parameter(description = "导出", required = true)
                   HttpServletResponse response) {
    appBindingPersonService.export(response);
}

@Operation(description = "导入数据")
@PostMapping("/file/import")
public String importData(@Parameter(description = "导入", required = true)
                         @RequestParam("file") MultipartFile file) {
    return appBindingPersonService.importData(file);
}
```



##### （1）导出

```java
//dto

package com.qax.situation.mt.imaotai.app.infra.common.excel;

import lombok.Data;

import java.util.List;

@Data
public class ExcelMultiSheetDto {
    /**
     * sheet名称
     */
    private String sheetName;

    /**
     * 数据类型（用于生成表头）
     */
    private Class<?> dataClass;

    /**
     * 导出的数据列表
     */
    private List<?> dataList;
}
```



```java
//Util

package com.qax.situation.mt.imaotai.app.infra.common.excel;

import com.alibaba.excel.EasyExcel;
import com.alibaba.excel.write.builder.ExcelWriterBuilder;

import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.net.URLEncoder;
import java.util.List;

/**
 * Excel导出工具类
 */
public class ExcelExportUtil {

    public static <T> void exportExcel(HttpServletResponse response, String fileName,
                                       List<ExcelMultiSheetDto> sheetList) throws IOException {
        // 设置响应头
        response.setContentType("application/vnd.openxmlformats-officedocument.spreadsheetml.sheet");
        response.setCharacterEncoding("utf-8");
        // 防止中文乱码
        fileName = URLEncoder.encode(fileName, "UTF-8").replaceAll("\\+", "%20");
        response.setHeader("Content-disposition", "attachment;filename*=utf-8''" + fileName + ".xlsx");
        // 写出数据
        ExcelWriterBuilder writerBuilder = EasyExcel.write(response.getOutputStream());
        for (ExcelMultiSheetDto sheetDTO : sheetList) {
            writerBuilder.head(sheetDTO.getDataClass())
                    .sheet(sheetDTO.getSheetName())
                    .doWrite(sheetDTO.getDataList());
        }
    }
}

```

```java
//impl

@Override
@Transactional
public void export(HttpServletResponse response) {
    QueryWrapper<AppBindingPersonPo> queryWrapper = new QueryWrapper<>();
    queryWrapper.and(wrapper -> wrapper.isNull("app_account").or().eq("app_account", ""));
    List<AppBindingPersonPo> personList = appBindingPersonRepository.list(queryWrapper);
    List<FileDto> list = FileDto.convert(personList);
    final List<ExcelMultiSheetDto> sheetList = new ArrayList<>();
    ExcelMultiSheetDto sheet = new ExcelMultiSheetDto();
    sheet.setSheetName("用户列表");
    sheet.setDataClass(FileDto.class);
    sheet.setDataList(list);
    sheetList.add(sheet);
    try {
        //填充数据
        ExcelExportUtil.exportExcel(response, "未绑定人员数据导出", sheetList);
    } catch (IOException e) {
        log.error("导出异常：", e);
    }

}
```

##### （2）导入

```java
//util 监听器

package com.qax.situation.mt.imaotai.app.infra.common.excel;

import cn.hutool.core.collection.CollUtil;
import cn.hutool.core.util.ObjectUtil;
import com.alibaba.excel.context.AnalysisContext;
import com.alibaba.excel.event.AnalysisEventListener;
import com.baomidou.mybatisplus.core.conditions.query.QueryWrapper;
import com.qax.situation.mt.imaotai.app.domain.repository.AppBindingPersonRepository;
import com.qax.situation.mt.imaotai.app.infra.persistence.entity.AppBindingPersonPo;
import com.qax.situation.mt.imaotai.app.application.dto.*;
import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Component;

import javax.annotation.Resource;
import java.util.ArrayList;
import java.util.List;

/**
 * Excel导入监听器
 *
 * @param <T>
 */
@Slf4j
@Component
public class ImportListener<T> extends AnalysisEventListener<T> {

    @Resource
    private AppBindingPersonRepository appBindingPersonRepository;

    List<T> list = new ArrayList<>();

    @Override
    public void invoke(T t, AnalysisContext analysisContext) {
        if (t != null) {
            list.add(t);
        }
    }

    @Override
    public void doAfterAllAnalysed(AnalysisContext analysisContext) {
        List<AppBindingPersonPo> updateList = new ArrayList<>();
        for (T t : list) {
            FileDto dto = (FileDto) t;
            QueryWrapper<AppBindingPersonPo> queryWrapper = new QueryWrapper<>();
            queryWrapper.eq("relation_account", dto.getRelationAccount());
            AppBindingPersonPo po = appBindingPersonRepository.getOne(queryWrapper);

            if (ObjectUtil.isNotEmpty(po)) {
                po.setAppAccount(dto.getAppAccount());
                updateList.add(po);
            }
        }
        if (CollUtil.isNotEmpty(updateList)) {
            appBindingPersonRepository.updateBatchById(updateList);
        }
        list.clear();
    }
}

```

```java
//impl

@Override
@Transactional
public String importData(MultipartFile file) {
    if (Objects.isNull(file)) {
        throw new BaseException(HttpStatus.INTERNAL, "MultipartFile", "请选择文件");
    }
    String suffix = FileUtil.getSuffix(file.getOriginalFilename());
    if (!"xlsx".equals(suffix) && !"xls".equals(suffix)) {
        throw new BaseException(HttpStatus.INTERNAL, "MultipartFile", "请选择Excel文件");
    }

    ImportListener importListener = applicationContext.getBean(ImportListener.class);
    try {
        EasyExcel.read(
            file.getInputStream(),
            FileDto.class,
            importListener
        ).sheet()
            .doRead();
        return "导入成功";
    } catch (IOException e) {
        log.error("导入异常：", e);
        return "导入失败";
    }
}
```



#### 11.获取Bean实例

```
// 方式1: 按类型获取（当前使用的方式）
ImportListener listener = applicationContext.getBean(ImportListener.class);

// 方式2: 按名称获取
ImportListener listener = applicationContext.getBean("importListener",
ImportListener.class);

// 方式3: 按名称获取（返回Object类型，需要强转）
ImportListener listener = (ImportListener) applicationContext.getBean("importListener");

```

