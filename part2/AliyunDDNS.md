# 阿里云DDNS更新 Python3



```python
pip install aliyun-python-sdk-core
pip install aliyun-python-sdk-domain
```



更新前需要先注册域名，然后随便添加一条解析记录



```python
from aliyunsdkcore.client import AcsClient
from aliyunsdkcore.request import CommonRequest
import json
import requests
import re

# 初始化
client = AcsClient('<AccessKey>', '<AccessKeySrc>', 'cn-hangzhou')
request = CommonRequest()
request.set_domain('alidns.aliyuncs.com')
request.set_version('2015-01-09')
domain = "域名"
prefix = "前缀"

# 获取公网IP地址
html_text = requests.get("http://www.3322.org/dyndns/getip").text
ip_text = re.findall(r'(?<![\.\d])(?:25[0-5]\.|2[0-4]\d\.|[01]?\d\d?\.){3}(?:25[0-5]|2[0-4]\d|[01]?\d\d?)(?![\.\d])', html_text)
ip = ip_text[0]
print("公网IP地址："+ ip)
print("域名："+ prefix + "." + domain)

# 获取二级域名的RecordId
request.set_action_name('DescribeDomainRecords')
request.add_query_param('DomainName', domain)
response = client.do_action_with_exception(request)
jsonObj = json.loads(response.decode("UTF-8"))
records = jsonObj["DomainRecords"]["Record"]
record = None
for rec in records:
    if rec["RR"] == prefix:
        record = rec
        break
if record == None:
    print("未找到二级域名记录")
    exit()
elif record['Value'] == ip:
    print("现有IP记录已为最新")
    exit()

# 更新IP记录
request.set_action_name('UpdateDomainRecord')
request.add_query_param('RecordId', record['RecordId'])
request.add_query_param('RR', prefix)
request.add_query_param('Type', 'A')
request.add_query_param('Value', ip)
response = client.do_action_with_exception(request)

print("DDNS更新完成\n")

```





