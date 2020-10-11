## 解析多层json的方法

json过滤器无法对json格式进行解析（真可笑），要先将json格式的内容转为字符串，再对这个字符串进行解析

例如我要解析下面的嵌套json

```
{
	"date": "2020-10-11 11:32:06.897",
	"thread": "http-nio-2000-exec-1",
	"level": "DEBUG",
	"class": "com.hozuo.xdw.common.filter.Order0XdwTokenFilter",
	"msg": {
		"method": "GET",
		"ip": "192.168.3.189",
		"port": 44070,
		"userAgent": "Mozilla/5.0 (Linux; Android 8.1.0; 16th) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/86.0.4240.75 Mobile Safari/537.36",
		"host": "192.168.3.10:2000",
		"requestURI": "/",
		"spendTime": 94
	}
}
```

解析为如下的结果

```
{
     "userAgent" => "Mozilla/5.0 (Linux; Android 8.1.0; 16th) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/86.0.4240.75 Mobile Safari/537.36",
          "host" => "192.168.3.10:2000",
        "method" => "GET",
    "@timestamp" => 2020-10-11T10:01:06.050Z,
            "ip" => "192.168.3.189",
     "spendTime" => 94,
         "class" => "com.hozuo.xdw.common.filter.Order0XdwTokenFilter",
      "@version" => "1",
        "thread" => "http-nio-2000-exec-1",
         "level" => "DEBUG",
          "port" => 44070,
          "date" => "2020-10-11 11:32:06.897",
    "requestURI" => "/"
}
```

配置管道如下

```
input {	
    stdin{}
}

## Add your filters / logstash plugins configuration here

filter {
    json {
        source => "message"
        remove_field => [ "message" ]
    }
    mutate {
        add_field => {"@msg" => "%{msg}"}
    }
    json {
        source => "@msg"
        remove_field => [ "msg","@msg" ]
    }
}

output {
    stdout { 
        codec => rubydebug
    }
}
```

