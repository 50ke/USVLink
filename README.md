# USVLink
无人船系统组网/通信库

## Proto Introduction
- link_protocol.proto：消息抽象类
- msg_enum.proto：枚举值
- msg_xxx.proto：消息载荷

## 消息定义
### 定义普通消息
- 第一步：创建msg_xxx.proto
- 第二步：在[msg_enum.proto](proto%2Fmsg_enum.proto)添加MsgId
### 定义控制消息
- 第一步：在[msg_enum.proto](proto%2Fmsg_enum.proto)添加CmdId
- 第二步：在README声明控制消息的参数设置，参考：CMD_ID_NAV_WAYPOINT、CMD_ID_DO_WEIGH_ANCHOR、CMD_ID_DO_DROP_ANCHOR
- 说明1：CMD_ID_NAV_XXX - 针对导航或和移动控制，例如：指点航行、指向航行等
- 说明2：CMD_ID_DO_XXX - 针对立即动作控制，如改变速度、激活伺服、起锚抛锚等
- 说明3：MAV_CMD_CONDITION_XXX - 根据条件改变任务的执行,例如：起航之前起锚

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

### ParamReadRequest - 参数读取请求 (6)
如果param_index=-1表示读取所有参数

| Field       | Type   | Required | Example      | Description |
|-------------|--------|----------|--------------|-------------|
| param_id    | string | Y        | max-velocity | 参数名称        |
| param_index | int32  | Y        | 1            | 参数索引        |

### ParamReadResponse - 参数读取响应 (7)
| Field       | Type         | Required | Example | Description   |
|-------------|--------------|----------|---------|---------------|
| ack         | int32        | Y        | 1       | 0-读取失败,1-读取成功 |
| err_msg     | string       | N        | success | 读取失败信息        |
| param_items | ParamItem[N] | Y        | -       | 参数项           |

### ParamWriteRequest - 参数设置请求 (8)
| Field       | Type         | Required | Example | Description |
|-------------|--------------|----------|---------|-------------|
| param_items | ParamItem[N] | Y        | -       | 参数项         |

### ParamWriteResponse - 参数设置响应 (9)
| Field       | Type            | Required | Example | Description   |
|-------------|-----------------|----------|---------|---------------|
| ack         | int32           | Y        | 1       | 0-读取失败,1-读取成功 |
| err_msg     | string          | N        | success | 读取失败信息        |

### MissionItem - 任务项 (10)
| Field         | Type    | Required | Example             | Description           |
|---------------|---------|----------|---------------------|-----------------------|
| seq           | int32   | Y        | 0                   | 任务项序号(从0开始)           |
| frame         | Frame   | Y        | FRAME_GLOBAL        | 航点坐标系（推荐全局坐标系）        |
| current       | bool    | Y        | true                | 是否为当前任务项              |
| auto_continue | bool    | Y        | true                | 当前任务项完成时，是否自动执行下一个任务项 |
| x             | float   | Y        | 34.1040000          | latitude              |
| y             | float   | Y        | 108.4350000         | longitude             |
| z             | float   | Y        | 0.00000000          | altitude              |
| command       | Command | Y        | CMD_ID_NAV_WAYPOINT | 航点动作                  |

### MissionDownloadRequest - 任务下载请求 (11)
空载荷

### MissionDownloadResponse - 任务下载响应 (12)
opaque_id由船端生成。

| Field         | Type           | Required | Example | Description   |
|---------------|----------------|----------|---------|---------------|
| ack           | int32          | Y        | 1       | 0-下载失败,1-下载成功 |
| err_msg       | string         | N        | success | 下载失败信息        |
| opaque_id     | int32          | N        | 1001001 | 任务ID          |
| mission_items | MissionItem[N] | Y        | -       | 任务项           |

### MissionUploadRequest - 任务上传请求 (13)
| Field         | Type           | Required | Example | Description   |
|---------------|----------------|----------|---------|---------------|
| mission_items | MissionItem[N] | Y        | -       | 任务项           |

### MissionUploadResponse - 任务上传响应 (14)
opaque_id由船端生成。

| Field         | Type           | Required | Example | Description   |
|---------------|----------------|----------|---------|---------------|
| ack           | int32          | Y        | 1       | 0-上传失败,1-上传成功 |
| err_msg       | string         | N        | success | 上传失败信息        |
| opaque_id     | int32          | N        | 1001001 | 任务ID          |

### MissionCurrent - 当前任务项 (15)
| Field         | Type         | Required | Example              | Description |
|---------------|--------------|----------|----------------------|-------------|
| mission_state | MissionState | Y        | MISSION_STATE_ACTIVE | 任务状态        |
| mission_id    | int32        | Y        | 1001001              | 任务ID        |
| mission_item  | MissionItem  | Y        | -                    | 当前任务项       |

### MissionSetCurrent - 设置当前任务项 (16)
| Field | Type  | Required | Example | Description            |
|-------|-------|----------|---------|------------------------|
| seq   | int32 | Y        | 0       | 任务项序号,-1表示重新执行当前任务项    |
| reset | bool  | Y        | 0       | 1-重新执行当前任务,0-不重新执行当前任务 |

### MissionClearRequest - 任务清理请求 (17)
空载荷

### MissionClearResponse - 任务请求响应 (18)
| Field         | Type           | Required | Example | Description   |
|---------------|----------------|----------|---------|---------------|
| ack           | int32          | Y        | 1       | 0-清理失败,1-清理成功 |
| err_msg       | string         | N        | success | 清理失败信息        |

### BatteryStatus - 电池状态 (19)
| Field              | Type  | Required | Example | Description    |
|--------------------|-------|----------|---------|----------------|
| id                 | int32 | Y        | 0       | 电池ID           |
| temperature        | int32 | Y        | 60      | 温度（℃）          |
| voltage            | float | Y        | 60      | 电压（V）          |
| capacity_consumed  | float | Y        | 1       | 消耗电量（Ah）       |
| capacity_remaining | float | Y        | 1       | 剩余电量（Ah）       |
| percent_remaining  | float | Y        | 50      | 剩余百分比（%）       |
| status_flags       | int32 | Y        | 1       | 电池状态,1-正常,0-故障 |

## Command - 控制指令 (20)
| Field  | Type  | Required | Example             | Description |
|--------|-------|----------|---------------------|-------------|
| cmd_id | CmdId | Y        | CMD_ID_NAV_WAYPOINT | 控制指令ID      |
| param1 | float | N        | -                   | 指令参数        |
| param2 | float | N        | -                   | 指令参数        |
| param3 | float | N        | -                   | 指令参数        |
| param4 | float | N        | -                   | 指令参数        |
| param5 | float | N        | -                   | 指令参数        |
| param6 | float | N        | -                   | 指令参数        |
| param7 | float | N        | -                   | 指令参数        |

### CMD_ID_NAV_WAYPOINT - 导航至航点
| Field  | Type  | Required | Example             | Description |
|--------|-------|----------|---------------------|-------------|
| cmd_id | CmdId | Y        | CMD_ID_NAV_WAYPOINT | 控制指令ID      |
| param1 | float | N        | 3                   | 航点停留时间（s）   |
| param2 | float | N        | 1                   | 到达半径（m）     |
| param3 | float | N        | 1                   | 通过半径（m）     |
| param4 | float | N        | 15                  | 偏航角（°）      |
| param5 | float | N        | 34.1230000          | 纬度          |
| param6 | float | N        | 108.1110000         | 经度          |
| param7 | float | N        | 0                   | 高程（m）       |

### CMD_ID_DO_WEIGH_ANCHOR - 起锚
| Field  | Type  | Required | Example                | Description         |
|--------|-------|----------|------------------------|---------------------|
| cmd_id | CmdId | Y        | CMD_ID_DO_WEIGH_ANCHOR | 控制指令ID              |
| param1 | float | N        | -1                     | 起锚长度（m） ，-1表示收回全部锚链 |
| param2 | float | N        | -                      | EMPTY               |
| param3 | float | N        | -                      | EMPTY               |
| param4 | float | N        | -                      | EMPTY               |
| param5 | float | N        | -                      | EMPTY               |
| param6 | float | N        | -                      | EMPTY               |
| param7 | float | N        | -                      | EMPTY               |

### CMD_ID_DO_DROP_ANCHOR - 抛锚
| Field  | Type  | Required | Example               | Description         |
|--------|-------|----------|-----------------------|---------------------|
| cmd_id | CmdId | Y        | CMD_ID_DO_DROP_ANCHOR | 控制指令ID              |
| param1 | float | N        | -1                    | 抛锚长度（m） ，-1表示下放全部锚链 |
| param2 | float | N        | -                     | EMPTY               |
| param3 | float | N        | -                     | EMPTY               |
| param4 | float | N        | -                     | EMPTY               |
| param5 | float | N        | -                     | EMPTY               |
| param6 | float | N        | -                     | EMPTY               |
| param7 | float | N        | -                     | EMPTY               |
