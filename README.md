# USVLink
无人船系统组网/通信库

## Overview

## Getting Started
### Java
### C++
### Python

## Proto Introduction
- USVLinkMessage.proto：USVLink消息抽象类
- msg_enum.proto：枚举值
- msg_xxx.proto：消息载荷

## Enums
### MsgSrc - 消息来源
| Name           | Value | Description |
|----------------|-------|-------------|
| MSG_SRC_USV    | 0     | 无人船发送       |
| MSG_SRC_UGC    | 1     | 地面站发送       |
| MSG_SRC_SERVER | 2     | 服务器发送       |

### MsgLink - 通讯链路
| Name               | Value | Description |
|--------------------|-------|-------------|
| MSG_LINK_NETWORK   | 0     | 网络          |
| MSG_LINK_RADIO     | 1     | 电台          |
| MSG_LINK_SATELLITE | 2     | 卫星          |

### MsgChannel - 通讯通道
| Name                    | Value | Description |
|-------------------------|-------|-------------|
| MSG_CHANNEL_UDP         | 0     | UDP传输       |
| MSG_CHANNEL_WS          | 1     | Websocket传输 |
| MSG_CHANNEL_MQTT        | 2     | MQTT传输      |
| MSG_CHANNEL_SERIAL_PORT | 3     | 串口传输        |

### MsgId - 消息ID
| Name                       | Value | Description |
|----------------------------|-------|-------------|
| MSG_ID_REGISTER_REQUEST    | 0     | 注册请求        |
| MSG_ID_REGISTER_RESPONSE   | 1     | 注册响应        |
| MSG_ID_DEREGISTER_REQUEST  | 2     | 注销请求        |
| MSG_ID_DEREGISTER_RESPONSE | 3     | 注销响应        |
| MSG_ID_CONNECT_REQUEST     | 4     | 连接请求        |
| MSG_ID_CONNECT_RESPONSE    | 5     | 连接响应        |
| MSG_ID_DISCONNECT_REQUEST  | 6     | 断连请求        |
| MSG_ID_DISCONNECT_RESPONSE | 7     | 断连响应        |
| MSG_ID_SYSTEM_INFORMATION  | 8     | 系统信息        |

## Messages
### MessagePacket
| Field               | Type   | Required | Values     | Description |
|---------------------|--------|----------|------------|-------------|
| system_id           | int32  | Y        |            | 发送方系统ID     |
| component_id        | int32  | Y        |            | 发送方组件ID     |
| target_system_id    | int32  | N        |            | 接收方系统ID     |
| target_component_id | int32  | N        |            | 接收方组件ID     |
| msg_id              | int32  | Y        | MsgId      | 消息ID        |
| time_ms             | int64  | N        |            | 时间戳(ms)     |
| msg_src             | int32  | N        | MsgSrc     | 消息来源        |
| msg_link            | int32  | N        | MsgLink    | 通讯链路        |
| msg_channel         | int32  | N        | MsgChannel | 通讯通道        |
| msg_payload         | Nested | Y        |            | 消息载荷        |
### RegisterRequest (0)
### RegisterResponse (1)
### DeregisterRequest (2)
### DeregisterResponse (3)
### ConnectRequest (4)
### ConnectResponse (5)
### DisconnectRequest (6)
### DisconnectResponse (7)
### SystemInformation (8)