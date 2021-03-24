---
title: dagre-compound APIs 文档
author: simplejason
date: 2021-03-01 00:00:00 +0800
categories: [Graph, dagre]
tags: [dagre, algorithm, graph]
---

# 布局方法
## buildGraph(data, option?, setting?)
> data: 必须
> option & setting: 可选

初始化的图数据，是一个包括 nodes 数组和 edges 数组的对象，option 包含布局方向和需要展开的节点 id

## 参数
- data: HierarchyGraphDef
- option: HierarchyGraphOption
- setting: DeepPartial\<LayoutConfig\>，正常无需配置

## 返回值
- 返回值类型：HierarchyGraphNodeInfo
- 包含所有节点、边、组的位置和大小等布局信息，数据结构参考接口定义

## 用法
```typescript
import { buildGraph, HierarchyGraphNodeInfo } from 'dagre-compound';

const data = {
    nodes: [
      { id: '0-1'},
      { id: '0-2'},
      { id: '1-1'},
      { id: '1-2'},
    ],
    edges: [
      { v: '0-1', w: '0-2' },
      { v: '0-2', w: '1-1' },
      { v: '1-1', w: '1-2' }
    ],
    compound: {
      GROUP0: ['0-1', '0-2']
    }
}
const renderInfo: HierarchyGraphNodeInfo = buildGraph(data); // 获得布局结构数据
```

# 接口定义
## HierarchyGraphDef
_初始化的图数据，是一个包括 nodes 数组和 edges 数组和分组对象的对象集合_

| 属性 | 类型 | 说明 |
| -- | -- | -- |
| nodes | HierarchyGraphNodeDef[] | 节点 |
| edges | HierarchyGraphEdgeDef[] | 边 |
| compound | HierarchyGraphCompoundDef | 分组(optional) |

## HierarchyGraphNodeDef
_节点的属性_

| 属性 | 类型 | 说明 |
| -- | -- | -- |
| id | any | 节点 id |
| width | number | 节点宽(optional) |
| height | number | 节点高(optional) |
| [key: string] | any | 其他附加信息 |

## HierarchyGraphEdgeDef
_边的属性_

| 属性 | 类型 | 说明 |
| -- | -- | -- |
| v | any | 起始节点 id |
| w | any | 终止节点 id |
| [key: string] | any | 其他附加信息 |

## HierarchyGraphCompoundDef
_分组对象_

| 属性 | 类型 | 说明 |
| -- | -- | -- |
| [parent: string] | any[] | 分组信息，键为组名，值为所属子节点的 id 数组 |

## HierarchyGraphOption
_初始化选项，包含布局方向和需展开的节点_

| 属性 | 类型 | 说明 |
| -- | -- | -- |
| rankDirection | `TB` \| `BT` \| `LR` \| `RL` | 布局方向 |
| expanded | string[] | 需展开的组节点 id |

## HierarchyGraphNodeInfo
_生成的布局结构数据，extends from HierarchyBaseNodeInfo_

| 属性 | 类型 | 说明 |
| -- | -- | -- |
| expanded | boolean | 是否已展开 |
| nodes | Array\<<br>`HierarchyBaseNodeInfo` \| `HierarchyGraphNodeInfo`\> | 该分组下的子节点 |
| edges | HierarchyBaseEdgeInfo[] | 该分组下的边 |

## HierarchyBaseNodeInfo
_布局后的节点对象，包含了经过布局的位置和大小等信息_

| 属性 | 类型 | 说明 |
| -- | -- | -- |
| name | any | 节点名称，等同于节点 id(待优化) |
| type | `0` \| `1` \| `2` | 节点类型<br>0: 组节点<br>1: 常规节点<br>2：虚拟节点(参与布局单不需要参与渲染) |
| attr | [key: string]: any; | 用户原始节点数据 |
| parentNodeName | string \| null | 父节点 id |
| coreBox | { width: number; height: number; } | coreBox，不含 padding |
| x | number | x 轴偏移值(相对父节点) |
| y | number | y 轴偏移值(相对父节点) |
| width | number | 宽，包含 padding |
| height | number | 高，包含 padding |
| labelHeight | number | label 高度 |
| labelOffset | number | label 距离坐标轴的位置 |
| radius | number | - |
| paddingTop | number | 上边距 |
| paddingBottom | number | 下边距 |
| paddingLeft | number | 左边距 |
| paddingRight | number | 右边距 |

## HierarchyBaseEdgeInfo

| 属性 | 类型 | 说明 |
| -- | -- | -- |
| v | any | 起始节点 id |
| w | any | 终止节点 id |
| inbound | boolean \| null | 是否为入边 |
| points | Array\<{ x: number; y: number }\> | points |
| adjoiningEdge | { v: string; w: string; } \| null | 相邻边 |
| baseEdgeList | Array\<{ v: string; w: string; }\> | 原始的 Edge 对象 |

## LayoutConfig
_布局配置信息，默认不需要更改，有强烈 dagre 定制使用者可参考_
