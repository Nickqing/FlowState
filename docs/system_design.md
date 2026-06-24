# 基于用户行为感知的动态专注管理系统 (FlowState) 概要设计说明书

## 1. 总体架构设计
本系统采用基于 MVVM (Model-View-ViewModel) 模式的单端解耦流式架构。由于摒弃了传统物理服务器，系统的“数据层”下沉至浏览器硬件沙盒，“控制层”与“业务层”完全由 Vue 3 运行时状态机托管。

### 1.1 系统拓扑结构图
```mermaid
graph TD
    subgraph 视图层 (View - HTML5/Tailwind)
        A[App主界面] --> B[冷启动遮罩层]
        A --> C[任务控制面板]
        A --> D[全屏倒计时视觉层]
    end
    subgraph 业务控制层 (ViewModel - Vue 3 Reactivity)
        E[状态管理器] --> F[任务匹配路由引擎]
        E --> G[倒计时线性插值器]
    end
    subgraph 数据存储层 (Model - LocalStorage)
        H[(LocalStorage 沙盒)]
    end
    A <-->|双向数据绑定| E
    F <-->|JSON 序列化/反序列化| H
