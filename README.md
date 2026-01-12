# FoodRecomendation

## 项目介绍
THINK PLAN MOVE是一个基于 Android 的AI智能导航应用。用户可以输入口味偏好或选择快捷标签，应用会结合位置与偏好，从高德地图周边搜索接口获取信息，并通过本地规则与 DeepSeek 分析结果进行筛选排序，提供卡片式推荐与导航能力。

## 需求分析
- 用户产生需求或想法，但是不知道该去哪，或者说选择困难症
- 位置来源需具备多种兜底方案（设备定位、网络/IP 定位、手动输入）
- 支持输入偏好与预算，并能自动解析/补全推荐标签
- 推荐结果要展示距离、评分、与图片等关键信息
- 支持“喜欢/不喜欢”交互，快速切换推荐
- 提供地图路线与系统导航入口
- 位置信息与偏好应本地保存，避免重复输入

## 关键技术
- Android (Java 11)，Material Components，Navigation，RecyclerView，ViewBinding
- 高德地图 SDK：地图展示、定位、路线规划
- 高德 Web API：IP 定位、地理编码/逆地理、周边搜索
- DeepSeek API：将自然语言偏好解析为标签、预算与规则权重
- HttpURLConnection + JSON 解析
- Glide 图片加载
- SharedPreferences 本地数据存储

## 项目总体设计
- 架构：MainActivity + NavHost 承载两个核心 Fragment（偏好设置与推荐展示），MapRouteActivity 负责路线页面
- 数据流：
  1) FirstFragment 收集位置与偏好并写入 SharedPreferences
  2) SecondFragment 读取位置与偏好，调用 DeepSeek 生成推荐标签
  3) 使用 AMap 周边搜索获取地理数据，执行本地过滤/排序
  4) 结果以卡片形式展示，支持地图与导航能力
- 并发：网络请求放在 ExecutorService 中，UI 更新回到主线程
- 容错：定位失败自动切换到 IP 定位/手动定位；偏好为空时使用随机推荐

## 功能模块详细设计
- 位置获取模块
  - 设备定位：AMapLocationClient 获取当前位置
  - 网络/IP 定位：高德 IP 定位接口获取大致位置
  - 手动定位：地理编码接口解析用户输入地址
  - 位置结果写入 SharedPreferences，供推荐与路线使用

- 偏好分析模块
  - 用户输入偏好文本并做分词/去重
  - 若有 DeepSeek Key，调用 DeepSeek 解析偏好标签、预算与过滤条件
  - 无模型返回时采用本地规则构建默认偏好

- 推荐获取与排序模块
  - 调用高德“周边搜索”接口获取列表
  - 过滤：距离、评分、排除关键词等规则
  - 排序：按匹配度、距离与评分组合排序
  - 无偏好时采用随机顺序输出

- 推荐展示与交互模块
  - 卡片式滑动交互：喜欢/不喜欢切换
  - 展示内容：名称、匹配度、距离、评分、图片、标签
  - 标签可点击切换筛选方向

- 地图与导航模块
  - MapRouteActivity 显示路线与目的地标记
  - 支持系统导航与内置路线展示

- 数据存储与配置模块
  - SharedPreferences 存储位置、偏好与来源
  - local.properties 配置 Key：AMAP_API_KEY、AMAP_WEB_KEY、DEEPSEEK_API_KEY

## 配置说明
在 `local.properties` 中添加：
- `AMAP_API_KEY`：高德地图 Android SDK Key
- `AMAP_WEB_KEY`：高德 Web API Key
- `DEEPSEEK_API_KEY`：DeepSeek API Key
