## Requests库

### 一个简单的`GET`请求

```python
import requests


def simple_get(url):
    header = {
        'User-Agent': 'curl/7.29.0',
        'Accept': '*/*',
        'Accept-Encoding': 'gzip, deflate',
        'Accept-Language': 'zh-CN,zh;q=0.9,ja;q=0.8,en;q=0.7'
    }
    response = requests.get(url, headers=header)
    print '>>>response code:'
    print response.status_code
    print '>>>response reason:'
    print response.reason
    print '>>>response headers:'
    print response.headers
    print '>>>response text:'
    print response.text


huize_url = "https://www.huize.com/product/detail-101074.html?DProtectPlanId=102434"
simple_get(huize_url)
```