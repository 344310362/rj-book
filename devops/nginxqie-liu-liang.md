## hosts
```
[ecp-portal-bak-new]
113.219.149.124
113.219.149.125

[ecp-portal-pro-new]
121.226.244.173
121.226.244.174

[nginx_switch_new]
121.226.244.173	ansible_user=root  
121.226.244.174	ansible_user=root
113.219.149.124 ansible_user=root 
113.219.149.125	ansible_user=root
```



## nginx_switch_new.yaml
```
---
- hosts: [nginx_switch_new]
  remote_user: root
  tasks:
   - name: switch to bak
     shell: sh /root/nginx_switch.sh 1 && service nginx reload
     tags:
     - switch_bak
   - name: switch to pro
     shell: sh /root/nginx_switch.sh 2 && service nginx reload
     tags:
     - switch_pro

```



## nginx_switch.sh
```
#!/bin/bash

vmp_list=`ls /usr/local/nginx/conf/vhost/ecp-portal-*`

for i in $vmp_list
do
    if [ $1 -eq 1 ];then
        sed -i 's/server 121.226.244.173/#server 121.226.244.173/' $i
	sed -i 's/server 121.226.244.174/#server 121.226.244.174/' $i
	sed -i 's/#*server 113.219.149.124/server 113.219.149.124/' $i
	sed -i 's/#*server 113.219.149.125/server 113.219.149.125/' $i
    elif [ $1 -eq 2 ];then
        sed -i 's/#*server 121.226.244.173/server 121.226.244.173/' $i
	sed -i 's/#*server 121.226.244.174/server 121.226.244.174/' $i
	sed -i 's/server 113.219.149.124/#server 113.219.149.124/' $i
	sed -i 's/server 113.219.149.125/#server 113.219.149.125/' $i
    elif [[ $1 -ne 1 ]] && [[ $1 -ne 2 ]];then
        echo "wrong params!"
	exit 1
    fi
done

service nginx configtest

```

## 执行命令


```
# 流量切为备的
/allcommon/python27/bin/ansible-playbook -i /home/ansible/hosts /home/ansible/nginx_switch_new.yaml --tag="switch_bak"
 
# 流量切为主的
/allcommon/python27/bin/ansible-playbook -i /home/ansible/hosts /home/ansible/nginx_switch_new.yaml --tag="switch_pro"
 
```



