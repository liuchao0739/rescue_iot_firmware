# rescue_iot_firmware

智能应急救援平台（Rescue Platform）**物联网警示设备固件**仓库，面向美国市场的 LTE-M / NB-IoT 联网警示设备。

| 属性 | 说明 |
|------|------|
| 平台 | [rescue_platform](https://github.com/liuchao0739) |
| 通信协议 | MQTT over TLS |
| 主网络 | LTE-M（4G 蜂窝物联网） |
| 辅助网络 | NB-IoT |

## 设备能力（MVP P0）

| 模块 | 功能 |
|------|------|
| SOS | 物理 SOS 按钮，长按 / 确认防误触 |
| 警示 | 高亮道路警示灯 |
| 定位 | GPS 经纬度上报 |
| 网络 | LTE-M 优先，NB-IoT 回退 |
| 协议 | MQTT 通信（`rescue/iot/` Topic 前缀） |
| 健康 | 心跳、电量、信号状态 |
| 固件 | OTA 远程升级 |

## 规划目录结构

```
rescue_iot_firmware/
├── src/
│   ├── main/
│   ├── sos/
│   ├── gps/
│   ├── network/        # LTE-M / NB-IoT
│   ├── mqtt/
│   └── ota/
├── docs/
│   ├── rescue_platform_mvp_plan.md
│   └── mqtt_protocol.md   # 待与 platform_api 对齐
├── tools/              # 烧录、日志采集
└── tests/
```

## MQTT 上报（Uplink）

| Topic | 说明 | 优先级 |
|-------|------|--------|
| `rescue/iot/{device_id}/sos` | SOS 紧急事件 | **最高** |
| `rescue/iot/{device_id}/gps` | GPS 位置 | 普通 |
| `rescue/iot/{device_id}/heartbeat` | 心跳 | 普通 |
| `rescue/iot/{device_id}/battery` | 电量 | 普通 |
| `rescue/iot/{device_id}/signal` | 信号 | 普通 |
| `rescue/iot/{device_id}/ota/status` | OTA 状态 | 高 |

## 平台下行（Downlink）

- `rescue/iot/{device_id}/cmd/light_test` — 远程灯光测试  
- `rescue/iot/{device_id}/cmd/ota` — OTA 指令  
- `rescue/iot/{device_id}/cmd/config` — 配置更新  

## 弱网策略

- SOS 消息优先发送  
- 失败后重试、本地缓存并补发  
- 上报字段含：`network_type`、`signal_strength`、`carrier`、`cell_id`

## 与平台联调

1. 设备连接 `rescue_platform_emqx`（见 infra 仓库）  
2. SOS 消息由 `rescue_mqtt_worker` 消费  
3. 运营平台通过 `rescue_ops_web` 展示设备状态  

## 相关仓库

| 仓库 | 说明 |
|------|------|
| [rescue_platform_api](https://github.com/liuchao0739/rescue_platform_api) | MQTT Worker、设备服务 |
| [rescue_platform_infra](https://github.com/liuchao0739/rescue_platform_infra) | EMQX 部署 |

## 文档

- [MVP 产品规划与技术方案](docs/rescue_platform_mvp_plan.md)

## License

Proprietary — Rescue Platform MVP
