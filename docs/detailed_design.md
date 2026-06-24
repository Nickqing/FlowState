```markdown
# 基于用户行为感知的动态专注管理系统 (FlowState) 详细设计说明书

## 1. 核心数据结构与存储字段
LocalStorage 内的单一键名 `"flowstate_tasks"` 映射为一个结构体数组。每一个数据项（Task Object）的逻辑定义如下：

| 属性名 (Property) | 数据类型 (Type) | 约束/合法值 (Constraints) | 说明 |
| :--- | :--- | :--- | :--- |
| `id` | String | 唯一时间戳字符串 | 主键 |
| `title` | String | $\le 100$ 字符 | 任务描述文本 |
| `energy_level` | String | `'HIGH' \| 'MEDIUM' \| 'LOW'` | 精力消耗评级 |
| `duration_minutes`| Integer | $5 \le \text{Value} \le 120$ | 专注时长（分钟） |
| `source` | String | `'COLD_START' \| 'INSPIRATION_POOL'`| 任务来源标签 |
| `status` | String | `'PENDING' \| 'COMPLETED'` | 状态机判定标识 |

## 2. 有限状态机 (FSM) 转移详细设计
倒计时与视觉反馈模块严格受控于四个法定状态。其状态转移矩阵与触发条件如下：

```mermaid
stateDiagram-v2
    [*] --> COLD_START : 检测不到本地存储数据
    COLD_START --> IDLE : 提交职业与兴趣画像
    [*] --> IDLE : 检测到本地存在数据
    IDLE --> FOCUSING : 点击开始专注 (成功匹配任务)
    FOCUSING --> MOTIVATING : 倒计时减至 0 秒
    MOTIVATING --> IDLE : 动画播放完毕/点击确认
