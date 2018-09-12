# myShell
(1)
---------------------------------------------------------
## #启动weblogic，及memcached
#!/bin/sh
if [ ! -d "/data/log" ];then
mkdir /data/log
fi
chown -R weblogic:web /data

startM=/home/weblogic/startMemcached.log
startW=/home/weblogic/startWeblogic.log
if [ ! -f "$startM" ];then
touch "$startM"
fi
if [ ! -f "$startW" ];then
echo "creat startW"
echo "-"
touch "$startW"
fi

chown -R weblogic:web $startW

filename=startMemcached_`date +%Y%m%d%H%M%S`
touch /data/log/"$filename".log
cat /home/weblogic/startMemcached.log >> /data/log/"$filename".log

chown -R weblogic:web /data

echo "" > /home/weblogic/startMemcached.log

echo "start to startMemcached"
nohup /opt/memcached-1.5.2/memcached/bin/memcached -d -vvv -u nobody -P /tmp/memcached.pid >> /home/weblogic/startMemcached.log &
echo "success to startMemcached"

su - weblogic -c "/share/sh/sub/./startWeblogicSub.sh"
exit

## startWeblogicSub.sh
#!/bin/sh
echo "you are logining into weblogic user"

filename=startWeblogic_`date +%Y%m%d%H%M%S`
touch /data/log/"$filename".log
cat /home/weblogic/startWeblogic.log >> /data/log/"$filename".log

echo "" > startWeblogic.log

echo "start to startWeblogic"
nohup /opt/weblogic/app/herysoft_projects/domains/domain1/bin/startWebLogic.sh >> startWeblogic.log &
echo "success to startWeblogic"


(2)
---------------------------------------------------------
## #关闭weblogic，及memcached
#!/bin/sh
if [ ! -d "/data/log" ];then
mkdir /data/log
fi
chown -R weblogic:web /data

stopM=/home/weblogic/stopMemcached.log
stopW=/home/weblogic/stopWeblogic.log
if [ ! -f "$stopM" ];then
touch "$stopM"
fi
if [ ! -f "$stopW" ];then
echo "creat stopW"
echo "-"
touch "$stopW"
fi

chown -R weblogic:web $stopW

filename=stopMemcached_`date +%Y%m%d%H%M%S`
touch /data/log/"$filename".log
cat /home/weblogic/stopMemcached.log >> /data/log/"$filename".log

chown -R weblogic:web /data

echo "" > /home/weblogic/startMemcached.log

echo "start to stopMemcached"
nohup kill `cat /tmp/memcached.pid`>> /home/weblogic/stopMemcached.log
echo "success to stopMemcached"

su - weblogic -c "/share/sh/sub/./stopWeblogicSub.sh"

kill -9 `ps -ef|grep weblogic|awk '{print $2}'`

exit

## stopWeblogicSub.sh
#!/bin/sh
echo "you are logining into weblogic user"

filename=stopWeblogic_`date +%Y%m%d%H%M%S`
touch /data/log/"$filename".log
cat /home/weblogic/stopWeblogic.log >> /data/log/"$filename".log

echo "" > stopWeblogic.log

echo "start to stopWeblogic"
nohup /opt/weblogic/app/herysoft_projects/domains/domain1/bin/stopWebLogic.sh >> stopWeblogic.log
echo "success to stopWeblogic"


