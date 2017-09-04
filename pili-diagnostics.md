pili-diagnostics

## API
- [新增排障job POST /v1/diag/jobs](#1)
- [查询排障job列表 GET /v1/diag/jobs](#2)
- [查询指定排障job信息 GET /v1/diag/jobs/{jobid}](#3)
- [删除排障job DELETE /v1/diag/jobs/{jobid}](#4)
- [获取流统计信息和相关节点信息 GET /v1/diag/jobs/{jobid}/statistics](#5)
- [获取指定流统计信息 GET /v1/diag/jobs/{jobid}/statistics/{streamid}](#6)
- [获取指定流线性数据 GET /v1/diag/jobs/{JobID}/qos/{streamID}](#7)
- [获取流线性数据 GET /v1/diag/jobs/{JobID}/qos](#8)


<h4 id="1">新增排障job</h4>

请求包
```
POST /v1/diag/jobs
Host: portal.qiniu.io
{
  "submitter": "who",
  "streamID": "z1.hub.streamKey",
  "start": <UnixSecond>,
  "end": <UnixSecond>,
  "cover": <Int>
}
```
cover取值，与排障流行为类型（推流、转流、播流）的任务分析对应表

|cover | publish(001) | internal source(010) | play(100) |
| :--- | :-----: | :------: | :------: |
| 1(001) | <center>yes<center/> | <center>-<center/> | <center>-<center/>
| 2(010) | <center>-<center/> | <center>yes<center/> | <center>-<center/>
| 4(100) | <center>-<center/> | <center>-<center/> | <center>yes<center/>
| 3(011) | <center>yes<center/> | <center>yes<center/> | <center>-<center/>
| 6(110) | <center>-<center/> | <center>yes<center/> | <center>yes<center/>
| 7(111) | <center>yes<center/> | <center>yes<center/> | <center>yes<center/>

返回包
```
200 {
"job":<jobID>
}

400{}
```

<h4 id="2">查询排障job列表</h4>

请求包
```
GET /v1/diag/jobs
Host: portal.qiniu.io
{
  "prevSubmitTime": <UnixSecond>,
  "limit": <Int>
}
```

返回包
```
200 {
[
    {
        "jobID":"<jobId>",
        "status":"<status>",
        "error":"<jobException>",
        "submitter":"<submitter>",
        "submitAt":<UnixSecond>,
        "doneAt":<UnixSecond>,
        "streamID":"<streamID>",
        "start":<UnixSecond>,
        "end":<UnixSecond>,
        "cover":<Int>
    }
    ...
]
}

400{}
```

<h4 id="3">查询指定排障job信息</h4>

请求包
```
GET /v1/diag/jobs/{jobid}
Host: portal.qiniu.io
{}
```

返回包
```
200 {
    {
        "jobID":"<jobId>",
        "status":"<status>",
        "error":"<jobException>",
        "submitter":"<submitter>",
        "submitAt":<UnixSecond>,
        "doneAt":<UnixSecond>,
        "streamID":"<streamID>",
        "start":<UnixSecond>,
        "end":<UnixSecond>,
        "cover":<Int>
    }
}

400{}
```


<h4 id="4">删除排障job</h4>

请求包
```
DELETE /v1/diag/jobs/{jobid}
Host: portal.qiniu.io
{}
```

返回包
```
200 {}

400{}
```


<h4 id="5">获取流统计信息和相关节点信息</h4>

请求包
```
GET /v1/diag/jobs/<jobid>/statistics
Host: portal.qiniu.io
{
    "streamID":"<streamID>",
    "node":"<nodeID>",
    "isp":"<isp>",
    "province":"<province>",
    "limit":<Int>,
    "method":"<method>",
    "onlyNodes":<Int: 0/1>
}

```

返回包
```
200
{
    "nodes":[
        {
            "node":"<node>",
            "remote":"<remote>",
            "isp":"<isp>",
            "province":"<province>",
            "start":<UnixSecond>,
            "end":<UnixSecond>,
            "method":"<method>"
        }
        ...
    ],
    "stream":[
        {
            "parent":"<parent>",
            "node":"<node>",
            "method":"<method>",
            "remote":"<remote>",
            "local":"<local>",
            "isp":"<isp>",
            "province":"<province>",
            "streamID":"<streamID>",
            "reqID":"<reqID>",
            "avgAudioFPS":<Int>,
            "avgVideoFPS":<Int>,
            "avgBPS":<Int>,
            "varAudioFPS":<Double>,
            "varVideoFPS":<Double>,
            "varBPS":<Double>,
            "duration":<Int>,
            "audioZeroFrameRatio":<Double>,
            "videoZeroFrameRatio":<Double>,
            "score":<Double>,
            "quality":<Int: 1/2/3>,
        }
        ...
    ]
}

400{}
```


<h4 id="6">获取指定流统计信息</h4>

请求包
```
GET /v1/diag/jobs/<jobid>/statistics/<streamid>
Host: portal.qiniu.io
{
    "streamID":"<streamID>",
    "node":"<nodeID>",
    "isp":"<isp>",
    "province":"<province>",
    "method":"<method>",
}
```

返回包
```
200
{
[
        {
            "parent":"<parent>",
            "node":"<node>",
            "method":"<method>",
            "remote":"<remote>",
            "local":"<local>",
            "isp":"<isp>",
            "province":"<province>",
            "streamID":"<streamID>",
            "reqID":"<reqID>",
            "avgAudioFPS":<Int>,
            "avgVideoFPS":<Int>,
            "avgBPS":<Int>,
            "varAudioFPS":<Double>,
            "varVideoFPS":<Double>,
            "varBPS":<Double>,
            "duration":<Int>,
            "audioZeroFrameRatio":<Double>,
            "videoZeroFrameRatio":<Double>,
            "score":<Double>,
            "quality":<Int: 1/2/3>,
        }
        ...
]
}

400{}
```

<h4 id="7">获取指定流线性数据</h4>

请求包
```
GET /v1/diag/jobs/<JobID>/qos/<streamID>
Host: portal.qiniu.io
{
    "node":"<nodeID>",
    "isp":"<isp>",
    "province":"<province>",
    "method":"<method>",
}
```

返回包
```
200
{
[ {
    "node":"<node>",
    "remote":"<remote>",
    "isp":"<isp>",
    "province":"<province>",
    "streamID":"<streamID>",
    "points":[
    {
    "time":<UnixSecond>,
    "values":{
    	"audioFPS":<Int>,
    	"videoFPS":<Int>,
    	"bps":<Int>
    	}
    }
    ...
    ]
    "reqID":"<reqID>",
    "method":"<method>"
}
...
]
}

400{}
```

<h4 id="8">获取流线性数据</h4>

请求包
```
GET /v1/diag/jobs/<JobID>/qos
Host: portal.qiniu.io
{
	"streamID":"<streamID>"
    "node":"<nodeID>",
    "isp":"<isp>",
    "province":"<province>",
    "method":"<method>",
    "limit":<Int>
}
```

返回包
```
200
{
[ {
    "node":"<node>",
    "remote":"<remote>",
    "isp":"<isp>",
    "province":"<province>",
    "streamID":"<streamID>",
    "points":[
    {
    "time":<UnixSecond>,
    "values":{
    	"audioFPS":<Int>,
    	"videoFPS":<Int>,
    	"bps":<Int>
    	}
    }
    ...
    ]
    "reqID":"<reqID>",
    "method":"<method>"
}
...
]
}

400{}
```
