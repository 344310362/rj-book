

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

