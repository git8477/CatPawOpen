# 猫爪源接口协议文档
> 非官方

## /config
```json
{
  "video": {
    "sites": [
      {
        "key": "nodejs_douban",
        "name": "🐵豆瓣‍|首页🐵",
        "type": 3,
        "indexs": 1,
        "api": "/spider/douban/3"
      },
    ],
    "danmuSearchUrl": ""
  }
}
```

| 字段说明 | 用途 |
|-------|:-----:|
| type | 3是普通站源，4是推送 |
| indexs | 通常不传，为1代表点击vod后跳搜索，类似豆瓣这种 |
| api | 站源的接口前缀 |
| danmuSearchUrl | 弹幕搜索页面地址 |

## 站源接口

### /init
> 站源初始化，通常在站源首次加载、搜索时调用

参数：无

返回值：无

### /home
> 获取分类列表、分类的二级筛选

参数：无

返回值示例
```json
{
    "class": [
        {
            "type_id": "hot_gaia",
            "type_name": "热门内容",
            "type_flag": "1"
        },
        {
            "type_id": "tv_hot",
            "type_name": "热门电视"
        },
    ],
    "filters": {
        "hot_gaia": [
            {
                "key": "sort",
                "name": "排序",
                "init": "recommend",
                "value": [
                    {
                        "n": "热度",
                        "v": "recommend"
                    },
                    {
                        "n": "最新",
                        "v": "time"
                    },
                    {
                        "n": "评分",
                        "v": "rank"
                    }
                ]
            },
        ]
    }
}
```
| 字段说明 | 用途 |
|-------|:-----:|
| type_flag | 通常不传，为“1”代表是目录模式，可用于实现“我的网盘”、“AList”等功能 |

### /category
> 获取分类数据，也可以在这个接口返回当前分类的filter

| 参数 | 用途 |
|-------|:-----:|
| id | `home`接口返的分类ID |
| page | 页数，从1开始 |
| filter | `Boolean` 是否开启筛选 |
| filters | 对象格式，key/value对应`home`接口的filters |

返回值示例
```json
{
    "page": 1,
    "pagecount": 2,
    "list": [
        {
            "vod_id": "113791",
            "vod_name": "斯巴达克斯：亚述家族",
            "vod_pic": "https://image.baidu.com/search/down?url=https://img1.doubanio.com/view/photo/s_ratio_poster/public/p2925299160.jpg",
            "vod_remarks": "更新至02集"
        },
    ],
    "filter": [
        {
            "key": "type",
            "name": "类型",
            "init": "all",
            "value": [
                {
                    "n": "全部",
                    "v": "all"
                },
                {
                    "n": "喜剧",
                    "v": "喜剧"
                }
            ]
        }
  ]
}
```

### /detail
> 获取vod详情，含线路、选集信息

| 参数 | 用途 |
|-------|:-----:|
| id | vodId，可以直接传ID，也可以传数组 |

返回值示例
```json
{
    "list": [
        {
            "vod_name": "天书黎明",
            "vod_year": "2025",
            "vod_actor": "演员",
            "vod_director": "导演",
            "vod_content": "介绍",
            "vod_play_from": "夸克-22cfee7efbbb$$$夸克-77ede2577454",
            "vod_play_url": "第一集$id11#第二集$id12$$$第一集$id21#第二集$id22"
        }
    ]
}
```
- `vod_play_from`: 以`$$$`分隔为线路
- `vod_play_url`: 以`$$$`分隔为所有选集信息，所有选集信息以`#`分隔为某一集的信息，某一集的信息以`$`分隔为选集名和选集ID


### /play
> 获取播放链接

| 参数 | 用途 |
|-------|:-----:|
| flag | 线路 |
| id | 选集ID |

返回值示例
```json
{
    "url": [
        "原画",
        "http://127.0.0.1:9988/spider/duoduo/3/proxy/quark/src/down/a8a313f9fd1e/o%2FK%2FTV2sqZ6rix2WU3iAcDbHD28TjVp1WD4r%2BCVQlvs%3D*e94c887388584cedabcfb9345cfe0829*d9bd312212ad65e844bf8b8132092e66/.bin",
        "超清",
        "http://127.0.0.1:9988/spider/duoduo/3/proxy/quark/trans/4k/a8a313f9fd1e/o%2FK%2FTV2sqZ6rix2WU3iAcDbHD28TjVp1WD4r%2BCVQlvs%3D*e94c887388584cedabcfb9345cfe0829*d9bd312212ad65e844bf8b8132092e66/.mp4"
    ],
    "header": {
        "Cookie": "",
        "User-Agent": "",
    },
    "extra": {
        "danmaku": "弹幕链接",
        "audio": "外挂音轨链接"
    }
}
```
播放地址支持`push://`协议，例如返回`push://http://test.com/video.mp4`，那么会使用链接推送功能跳到播放页并推送`http://test.com/video.mp4`


### /search
> 搜索

| 参数 | 用途 |
|-------|:-----:|
| wd | 搜索关键字 |
| page | 页码 |

返回值示例
```json
{
    "page": 1,
    "pagecount": 1,
    "list": [
        {
            "vod_id": "/index.php/vod/detail/id/28.html",
            "vod_name": "仙逆",
            "vod_pic": "https://hhmage.com/cover/896b690b9566f53a875e03a3a324c091.jpg",
            "vod_remarks": "更新至118集"
        },
        {
            "vod_id": "/index.php/vod/detail/id/8311.html",
            "vod_name": "仙逆剧场版 神临之战",
            "vod_pic": "https://hhmage.com/cover/841e0cb6a6dd09486f7be20d9b476e17.jpg",
            "vod_remarks": "已完结"
        }
    ]
}
```

## Action列表
> 壳子提供给源调用的方法，因壳而异

### 1. Toast提示
```json
{
    "action": "toast",
    "opt": {
      "message": "提示内容",
      "duration": 2
    }
}
```

### 2. 弹幕推送
> 弹幕数据格式跟安卓源保持一致，示例：https://api.bilibili.com/x/v1/dm/list.so?oid=30777215354
```json
{
    "action": "danmuPush",
    "opt": {
      "url": "弹幕下载地址"
    }
}
```

### 3. App内部打开Webview
```json
{
    "action": "openInternalWebview",
    "opt": {
      "url": "https://www.baidu.com"
    }
}
```

### 4. App外部打开Webview
```json
{
    "action": "openExternalWebview",
    "opt": {
      "url": "https://www.baidu.com"
    }
}
```

### 5. 保存用户配置
> 由壳子实现跨设备同步
```json
{
    "action": "saveProfile",
    "opt": {
      "cookie1": "value1",
      "cookie2": "value2"
    }
}
```

### 6. 查询用户配置
> 由壳子实现跨设备同步
```json
{
    "action": "queryProfile",
    "opt": {}
}
```

### 7. 链接推送
> 壳子跳转至播放页并带上播放信息，功能同链接推送一致
```json
{
    "action": "urlPush",
    "opt": {
      "url": "",
      "name": "非必填，不填就展示url"
    }
}
```

### 8. 获取播放信息
> 通常用于弹幕自动推送时获取剧集信息、emby获取播放进度实现记录回传等场景

**请求**
```json
{
    "action": "getPlayInfo",
    "opt": {}
}
```

**返回**
```json
{
    "url": "播放地址",
    "speed": "倍速",
    "title": "剧名",
    "flag": "线路",
    "episodeName": "选集名称",
    "cover": "封面",
    "duration": "总时长，单位秒",
    "position": "当前播放进度，单位秒"
}
```
