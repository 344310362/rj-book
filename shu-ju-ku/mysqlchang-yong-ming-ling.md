### where when

> 根据不同条件set不同值

    UPDATE `ops_role_account`
    SET production_id = CASE component
    WHEN 'vmp' THEN '1'
    WHEN 'pontus' THEN '2'
    WHEN 'poseidon' THEN '3'
    WHEN 'aipaas' THEN '4'
    END,
    production_name = CASE component
    WHEN 'vmp' THEN '边缘云主机'
    WHEN 'pontus' THEN '边缘云容器'
    WHEN 'poseidon' THEN '边缘云网络'
    WHEN 'aipaas' THEN '边缘云智能'
    END;

### group\_concat

* group 后根据某个字段合并，多个值间用逗号隔开。

```
SELECT group_concat( town )
FROM players
结果：
group_concat(town)
长沙,北京,北京,
```

* group\_concat 里面支持去重

```
group_contact(DISTINCT TOWN)
```

* 排序

```
GROUP_CONCAT(field ORDER BY  coder desc)  'msg'
```



