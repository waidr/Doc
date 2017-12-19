pili-diagnostics

## API
- [新增流级别排障job POST /v1/diag/jobs](#1)
- [查询排障job列表 GET /v1/diag/jobs](#2)
- [查询指定排障job信息 GET /v1/diag/jobs/{jobId}](#3)
- [删除排障job DELETE /v1/diag/jobs/{jobId}](#4)
- [获取流统计信息和相关节点信息 GET /v1/diag/jobs/{jobId}/statistics](#5)
- [获取指定流统计信息 GET /v1/diag/jobs/{jobId}/statistics/{streamId}](#6)
- [获取指定流质量timeline GET /v1/diag/jobs/{jobId}/qos/{streamId}](#7)
- [获取指定排障job全局转发树 GET /v1/diag/jobs/{jobId}/tree](#8)
- [新增hub级别排障job POST /v1/diag/hub/jobs](#9)
- [参数取值列表](#100)

> *参数中加粗的是必传参数*

<h4 id="1">新增流级别排障job</h4>

请求包
```markdown
POST /v1/diag/jobs
Host: portal.qiniu.io
{
  "**submitter**" :<who>",
  "**streamID**": "<z1.hub.streamKey>",
  "**start**": <UnixSecond>,
  "**end**": <UnixSecond>,
  "**cover**": <Int,详情见参数取值列表>,
  "ips": "<播放sdk ips；多个ip以半角逗号隔开;当任务包含播流时此参数为必填;最大ip数: 10>"
}
```

返回包
```
200 {
    "job":"<jobID>"
}

400{}
```

<h4 id="9">新增hub级别排障job</h4>

请求包
```markdown
POST /v1/diag/hub/jobs
Host: portal.qiniu.io
{
  "**submitter**": "<who>",
  "**hub**": "<hub>",
  "**start**": <UnixSecond>,
  "**end**": <UnixSecond>,
  "**cover**": <Int,详情见参数取值列表>,
  "**mode**": <Int,详情见参数取值列表>,
  "isp": "<运营商>",
  "province": "<省份>",
  "idcId": "<idcId>",
  "node": "<节点>"
}
```

返回包
```
200 {
"job":<jobID>
}

400{}
```

<h4 id="2">查询排障job列表</h4>

请求包
```markdown
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
        "level":"<任务级别，1：流级别；2：hub级别>"
        "status":"<status>",
        "error":"<jobException>",
        "submitter":"<submitter>",
        "submitAt":<UnixSecond>,
        "doneAt":<UnixSecond>,
        "start":<UnixSecond>,
        "end":<UnixSecond>,
        "cover":<Int>,
        ----流级别包含字段----
        "streamID":"<streamID>",
        "ips": "<播放sdk ips；多个ip以半角逗号隔开>"
        ----hub级别包含字段----
        "hub": "<hub>",
        "hubDimensions": {
            "isp": "<isp>",
            "province": "<province>",
            "idcId": "<idcId>",
            "node": "<node>"
                         },
        "mode": "<mode>"
    }
    ...
]
}

400{}
```

<h4 id="3">查询指定排障job信息</h4>

请求包
```markdown
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
        "cover":<Int>,
        "ips": <播放sdk ips；多个ip以半角逗号隔开;当任务包含播流时此参数为必填;最大ip数: 10>
    }
}

400{}
```


<h4 id="4">删除排障job</h4>

请求包
```markdown
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
```markdown
GET /v1/diag/jobs/<jobid>/statistics
Host: portal.qiniu.io
{
    "**method**":"<Int: 1/2/4>",
    "node":"<nodeID>",
    "isp":"<isp>",
    "province":"<province>",
    "limit":<Int>,
    "onlyNodes":<Int: 0/1>,
    "mode":<Int:1/2/..>
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
            "method":"<Int: 1/2/4>",
            "idcID":"<idcID>
        }
        ...
    ],
    ----流级别任务返回数据结构，后续迭代会以hub级别数据结构代替--------
    "streams":[
        {
            "parent":"<remote nodeId | root | - >",
            "node":"<node>",
            "method":"<Int: 1/2/4>",
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
    ----hub级别任务返回数据结构--------
    "statistics":[
        {
            "streamID":"<streamID>",
            "node":"<node>",
            "remote":"<remote>",
            "isp":"<isp>",
            "province":"<province>",
            "reqID":"<reqID>",
            "parent":"<remote nodeId | root | - >",
            "method":"<Int: 1/2/4>",
            "local":"<local>",
            "idcID":"<idcID>",
            "metrics":
                {
                "avgaudiofps":<int>,
                "avgvideofps":<int>,
                "avgbps":<int>,
                "varaudiofps":<double>,
                "varvideofps":<double>,
                "varbps":<double>,
                "duration":<int>,
                "audiozeroframeratio":<Double>,
                "videozeroframeratio":<Double>,
                "score":<double>,
                "quality":<int: 1/2/3>,
                "lagRatio":<double,lag卡顿率>,
                "reqCount":<int,推/播流链接数>,
                }
        }
        ...
    ]
}

400{}
```


<h4 id="6">获取指定流统计信息</h4>

请求包
```markdown
GET /v1/diag/jobs/<jobid>/statistics/<streamid>
Host: portal.qiniu.io
{
    "**method**":"<Int: 1/2/4>",
    "node":"<nodeIdRegex>",
    "isp":"<isp>",
    "province":"<province>",
    "mode":<Int:1/2/..>,
    "limit":<Int>
}
```

返回包
```
200
{
[
        {
            "parent":"<remote nodeId | root | - >",
            "node":"<node>",
            "method":"<Int: 1/2/4>",
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

<h4 id="7">获取指定流质量timeline</h4>

请求包
```markdown
GET /v1/diag/jobs/<JobID>/qos/<streamID>
Host: portal.qiniu.io
{
    "**method**":"<Int: 1/2/4>",
    "node":"<nodeIdRegex>",
    "isp":"<isp>",
    "province":"<province>",
    "mode":"<Int: 1/2/..>",
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
    "method":"<Int: 1/2/4>",
    "parent":"<remote nodeId | root | - >"
}
...
]
}

400{}
```

<h4 id="8">获取指定排障job全局转发树</h4>

请求包
```markdown
GET /v1/diag/jobs/{jobId}/tree
Host: portal.qiniu.io
```

返回包
```
200
{
[
    {
    "node":"<node>",
    "remotes":[
        {
        "node":<parent node | root | - >,
        "reqID":<reqID>,
        "start":<UnixSecond>,
        "end":<UnixSecond>
        }
        ...
        ]
    }
...
]
}

400{}
```

<h3 id="100">参数取值列表</h3>

----
cover取值，任务覆盖流行为类型（推流、内部转发流、播流）表

|cover | publish(001) | internal source(010) | play(100) |
| :--- | :-----: | :------: | :------: |
| 1(001) | yes | - | -
| 2(010) | - | yes | -
| 4(100) | - | - | yes
| 3(011) | yes | yes | -
| 5(101) | yes | - | yes
| 6(110) | - | yes | yes
| 7(111) | yes | yes | yes

method取值表

| method | description |
| :--- | :---- |
| 1(001) | publish(推流)
| 2(010) | internal source(内部转发)
| 4(100) | play(播流)

mode取值,排序方式表

| key（排序项） |  asc(升序) | desc（降序）| hsc(hub statistics contribution) | description
| :--- | :-----: | :-----: | :-----: |:---
| 平均码率 | 1 | 101=1+100 | 1001/1101 |平均码率升序mode=1;平均码率降序mode=101;hsc=mode+1000
| 平均视频帧率 | 2 | 102 | 1002/1102 |
| 秒级码率标准差 | 3 | 103 | - |
| 秒级视频帧率标准差 | 4 | 104 | - |
| 单次连接持续时间 | 5 | 105 | - |
| 视频帧卡顿率 | 6 | 106 | 1006/1106 |
| 视频lag卡顿率 | 7 | 107 | 1007/1107 |
| 峰值带宽 | 8 | 108 | - |
| 丢帧率 | 9 | 109 | 1009/1109 |
