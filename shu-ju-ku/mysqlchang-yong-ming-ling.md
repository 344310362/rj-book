### where when

根据不同条件set不同值

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



