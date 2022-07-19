# How-Tos
A random collection of How-To's.

## Kali - Change User-Agent for web-based scripting tools

### Apt Package Manager

```
vi /etc/apt/apt.conf.d/99user-agent
```

```
Acquire
{
  http::User-Agent "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/70.0.3538.110 Safari/537.36";
};
```
### Curl

```
vi ~/.curlrc
```

```
user-agent "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/70.0.3538.110 Safari/537.36"
```

### Wget

```
vi ~/.wgetrc
```

```
header = "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/70.0.3538.110 Safari/537.36"
```

## Kali - export NFS for vsphere

```
mkdir /mnt/share
echo '/mnt/share *(rw,sync)' > /etc/export
service restart rpcbind
systemctl restart nfs-kernel-server
```

In vSphere add /mnt/share and server IP

## Powershell manage USB devices

```
#Get the Generic USB Device whre the status is OK
$((Get-PnpDevice).where({ $_.FriendlyName -match  'Generic USB' -AND $($_.Status -eq 'OK') })
```

## Compiling Spring Framework modules

#install java 17 and maven. Gradle is required, but comes packaged w/the Spring Framework
#I also added Maven for Java plug-in for VS Code

#Clone the spring-framework. The main branch will have the patched code. 
```
git clone https://github.com/spring-projects/spring-framework.git 
```

#Change into the local repo just downloaded

```
cd spring-framework
```

#Remove the patched version of file 

```
mv src/main/java/org/springframework/beans/CachedIntrospectionResults.java .
```
Replace that file with the vulnerable version

```
wget https://raw.githubusercontent.com/spring-projects/spring-framework/d4192b9d355a2d4b0be959e076c255d8b5f01bcf/spring-beans/src/main/java/org/springframework/beans/CachedIntrospectionResults.java
```

Replace the patched source code file w/ the vulnerable version (hash is d4192b9d355a2d4b0be959e076c255d8b5f01bcf)

```
mv CachedIntrospectionResults.java src/main/java/org/springframework/beans/CachedIntrospectionResults.java
```
Compile Spring Framework. Remove the tests will expedite the process, but not done here

```
.\gradlew build
```

Note: This will take a while. Wasn't able to compile just one module because of 
dependencies. Not sure if there's a way to force build the dependencies as 
well?

Publish them to your local maven repository. This will be located in 
you user home/profile. So ~/.m2 in Linux %userprofile%\.m2 in Windows

```
.\gradlew publishToMavenLocal 
```
You can do the above or directly install just the bean module through maven.

In maven project Remove Test file in \test\java\com\example\demo

Next, we want maven to auto-load everything excpet the patched beans module and 
load our vulnerable version instead. In VS Code, open the POM manifest 
Under <description> node under the top-level <Project> node, there should be a 
<properties> node. Add a child node add, the below:

```
<spring-beans.version>XXX</spring-beans.version>
```

Replacing XXX with the version of the spring-beans module you compiled (In my case: 5.3.15)    
This forces maven to grab the version of beans we compiled and published to our local .m2 repository
in the step above.
