# USVLink
无人船系统组网/通信库

## Overview

## Getting Started
### Java
### C++
### Python

## Proto Introduction
- link_protocol.proto：消息抽象类
- msg_enum.proto：枚举值
- msg_xxx.proto：消息载荷

## Messages
### MessagePacket
| Field               | Type   | Required | Values     | Description |
|---------------------|--------|----------|------------|-------------|
| system_id           | int32  | Y        |            | 发送方系统ID     |
| component_id        | int32  | N        |            | 发送方组件ID     |
| target_system_id    | int32  | N        |            | 接收方系统ID     |
| target_component_id | int32  | N        |            | 接收方组件ID     |
| msg_id              | int32  | Y        | MsgId      | 消息ID        |
| time_ms             | int64  | Y        |            | 时间戳(ms)     |
| msg_src             | int32  | Y        | MsgSrc     | 消息来源        |
| msg_link            | int32  | Y        | MsgLink    | 通讯链路        |
| msg_payload         | Nested | Y        |            | 消息载荷        |

### Heartbeat - 心跳 (0)
GCS端不用设置latitude、longitude、anchor_remaining。

| Field                    | Type   | Required | Example     | Description             |
|--------------------------|--------|----------|-------------|-------------------------|
| name                     | string | Y        | GCS-01      | 系统名称, 推荐全局唯一            |
| latitude                 | float  | N        | 34.1040000  | 纬度（WGS84），保留小数点后7位      |
| longitude                | float  | N        | 108.3524000 | 经度（WGS84），保留小数点后7位      |
| anchor_remaining         | float  | N        | 11.1        | 铰锚余量（m），保留小数点后1位        |
| connect_status           | bool   | Y        | true        | 连接状态，true-已连接，false-未连接 |
| connect_target_system_id | int32  | Y        | 2           | 连接目标系统ID                |

### ConnectRequest - 连接请求 (1)
空载荷

### ConnectResponse - 连接响应 (2)
| Field   | Type   | Required | Example | Description   |
|---------|--------|----------|---------|---------------|
| ack     | int32  | Y        | 1       | 0-连接失败,1-连接成功 |
| err_msg | string | N        | success | 连接失败信息        |

### DisconnectRequest - 断开请求 (3)
空载荷

### DisconnectResponse - 断开响应 (4)
| Field   | Type   | Required | Example | Description   |
|---------|--------|----------|---------|---------------|
| ack     | int32  | Y        | 1       | 0-断开失败,1-断开成功 |
| err_msg | string | N        | success | 断开失败信息        |

### ParamItem - 参数项 (5)
| Field       | Type   | Required | Example      | Description |
|-------------|--------|----------|--------------|-------------|
| param_id    | string | Y        | max-velocity | 参数名称        |
| param_value | float  | Y        | 1            | 参数值         |
| param_index | int32  | Y        | 0            | 参数索引        |

### ParamReadRequest (6)
如果param_index=-1表示读取所有参数

| Field       | Type   | Required | Example      | Description |
|-------------|--------|----------|--------------|-------------|
| param_id    | string | Y        | max-velocity | 参数名称        |
| param_index | int32  | Y        | 1            | 参数索引        |

### ParamReadResponse (7)
| Field       | Type         | Required | Example | Description   |
|-------------|--------------|----------|---------|---------------|
| ack         | int32        | Y        | 1       | 0-读取失败,1-读取成功 |
| err_msg     | string       | N        | success | 读取失败信息        |
| param_items | ParamItem[N] | Y        | -       | 参数项           |

### ParamWriteRequest (8)
| Field       | Type         | Required | Example | Description |
|-------------|--------------|----------|---------|-------------|
| param_items | ParamItem[N] | Y        | -       | 参数项         |

### ParamWriteResponse (9)
| Field       | Type            | Required | Example | Description   |
|-------------|-----------------|----------|---------|---------------|
| ack         | int32           | Y        | 1       | 0-读取失败,1-读取成功 |
| err_msg     | string          | N        | success | 读取失败信息        |

### MissionItem (10)
| Field         | Type    | Required | Example | Description   |
|---------------|---------|----------|---------|---------------|
| seq           | int32   | Y        | 1       | 0-读取失败,1-读取成功 |
| frame         | Frame   | N        | success | 读取失败信息        |
| current       | bool    | N        | success | 读取失败信息        |
| auto_continue | bool    | N        | success | 读取失败信息        |
| x             | float   | N        | success | 读取失败信息        |
| y             | float   | N        | success | 读取失败信息        |
| z             | float   | N        | success | 读取失败信息        |
| command       | Command | N        | success | 读取失败信息        |

### MissionDownloadRequest (11)
空载荷

### MissionDownloadResponse (12)
opaque_id由船端生成。

| Field         | Type           | Required | Example | Description   |
|---------------|----------------|----------|---------|---------------|
| ack           | int32          | Y        | 1       | 0-下载失败,1-下载成功 |
| err_msg       | string         | N        | success | 下载失败信息        |
| opaque_id     | int32          | N        | 1001001 | 任务ID          |
| mission_items | MissionItem[N] | Y        | -       | 任务项           |

### MissionUploadRequest (13)
| Field         | Type           | Required | Example | Description   |
|---------------|----------------|----------|---------|---------------|
| mission_items | MissionItem[N] | Y        | -       | 任务项           |

### MissionUploadResponse (14)
opaque_id由船端生成。

| Field         | Type           | Required | Example | Description   |
|---------------|----------------|----------|---------|---------------|
| ack           | int32          | Y        | 1       | 0-上传失败,1-上传成功 |
| err_msg       | string         | N        | success | 上传失败信息        |
| opaque_id     | int32          | N        | 1001001 | 任务ID          |

### MissionCurrent (15)
| Field         | Type         | Required | Example              | Description |
|---------------|--------------|----------|----------------------|-------------|
| mission_state | MissionState | Y        | MISSION_STATE_ACTIVE | 任务状态        |
| mission_id    | int32        | Y        | 1001001              | 任务ID        |
| mission_item  | MissionItem  | Y        | -                    | 当前任务项       |

### MissionSetCurrent (16)
| Field | Type  | Required | Example | Description            |
|-------|-------|----------|---------|------------------------|
| seq   | int32 | Y        | 0       | 任务项序号,-1表示重新执行当前任务项    |
| reset | bool  | Y        | 0       | 1-重新执行当前任务,0-不重新执行当前任务 |

### MissionClearRequest (17)
空载荷

### MissionClearResponse (18)
| Field         | Type           | Required | Example | Description   |
|---------------|----------------|----------|---------|---------------|
| ack           | int32          | Y        | 1       | 0-清理失败,1-清理成功 |
| err_msg       | string         | N        | success | 清理失败信息        |

### BatteryStatus (19)
| Field              | Type  | Required | Example | Description    |
|--------------------|-------|----------|---------|----------------|
| id                 | int32 | Y        | 0       | 电池ID           |
| temperature        | int32 | Y        | 60      | 温度（℃）          |
| voltage            | float | Y        | 60      | 电压（V）          |
| capacity_consumed  | float | Y        | 1       | 消耗电量（Ah）       |
| capacity_remaining | float | Y        | 1       | 剩余电量（Ah）       |
| percent_remaining  | float | Y        | 50      | 剩余百分比（%）       |
| status_flags       | int32 | Y        | 1       | 电池状态,1-正常,0-故障 |


## Command (20)
