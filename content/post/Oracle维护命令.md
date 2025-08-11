---
title: "Oracle维护命令"
author: "烂柯"
description: "Oracle维护命令：资源监控"
date: 2023-10-15
lastmod: 2023-10-20

tags: [
    "Oracle监控",
    "会话管理",
    "性能调优",
    "连接分析",
    "数据库维护"
]
categories: [
    "数据库管理",
    "性能优化",
    "系统监控"
]
keywords: [
    "v$session",
    "会话统计",
    "连接分析",
    "会话终止",
    "Oracle性能",
    "DBA命令"
]
---

# 性能监控
```sql
-- 实时监控会话总数
SELECT COUNT(*) AS active_sessions FROM v$session;

-- 按用户查看连接数（识别异常连接）
SELECT username, COUNT(*) AS conn_count 
FROM v$session 
GROUP BY username
ORDER BY conn_count DESC;
```

# 安全审计
```sql
-- 检查特定机器的连接
SELECT username, machine, program, logon_time
FROM v$session
WHERE machine = 'MDKJSERVER';
```

# 资源释放
```sql
-- 生成终止非活动会话脚本
SELECT 'ALTER SYSTEM KILL SESSION '''||sid||','||serial#||''' IMMEDIATE;' AS kill_cmd
FROM v$session 
WHERE status = 'INACTIVE' AND last_call_et > 1800; -- 30分钟无活动
```


# 连接问题排查
```sql
-- 查看特定OS用户的连接
SELECT sid, serial#, username, status, sql_id
FROM v$session
WHERE osuser = 'MDKJ-W';
```