---
title: DAG 可视化布局库 - dagre-compound
author: simplejason
date: 2021-03-01 00:00:00 +0800
categories: [Dagre, Graph]
tags: [dagre, algorithm, graph]
---

# 关于 dagre-compound

[![dagre-compound](https://img.shields.io/badge/npm-dagre--compound-orange?style=flat-square){: .left}](https://www.npmjs.com/package/dagre-compound)
<br>
<br>


> 如果你了解或使用过 dagre 或者 dagre-d3 等可视化布局库，或者有图可视化布局相关知识，会对理解和使用该布局库有很大的帮助。

- 针对具有层级结构的 DAG 图布局
- 支持跨层级节点相连，保持其层级关联关系
- 优化跨层级节点相连导致的边交叉问题

![demo](https://gblobscdn.gitbook.com/assets%2F-MW2-2DdByqaneMxSyZr%2F-MW3XGbReAcGuDIUsjKv%2F-MW3YIAZVn_muLvf7Pq5%2Fg6-dc-demo.jpg?alt=media&token=ebde33a3-cb7e-4277-a6cc-ad10cc58aeab)

# 快速上手

stackblitz 在线演示：[React Demo](https://stackblitz.com/edit/react-ts-igpdyg?file=Graph.tsx)、[Angular Demo](https://stackblitz.com/edit/angular-ivy-ivy67e?file=src/app/app.component.ts)

API 文档请参考：[API 文档](https://docs.simplejason.com/dc-api)

## 安装

```console
$ npm i dagre-compound --save
```

## 使用

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
const renderInfo: HierarchyGraphNodeInfo = buildGraph(data);
```

# 更多问题

- [x] 获取绝对定位

默认 dagre-compound 生成具有层级结构的布局数据，边与节点的位置均为相对组节点的位置。
如果你希望使用绝对定位来渲染 svg 元素，可参考如下方法：
```typescript
// ROOT 节点  
ROOT_NAME = '◬ROOT◬';
/**
 * 计算节点的绝对位置，用于 g 元素定位
 * parent 节点为 ROOT 节点时无需执行此方法
 */
const calcAbsolutePos = (node: HierarchyGraphNodeInfo | HierarchyBaseNodeInfo, parent: HierarchyGraphNodeInfo): void => {
  node.x = parent.x - parent.coreBox.width / 2 + node.x;
  node.y = parent.y - (parent.coreBox.height / 2 - node.y) + parent.labelHeight;
}

/**
 * 计算边 point 的绝对位置
 */
const calcAbsoluteEdgePos = (edges: HierarchyBaseEdgeInfo[], node: HierarchyGraphNodeInfo) => {
  const offsetX = node.name === ROOT_NAME ? 0 : node.x - node.coreBox.width / 2;
  const offsetY = node.name === ROOT_NAME ? 0 : node.y - node.coreBox.height / 2 + node.paddingTop;
  edges.forEach(e => {
    // 同步 points 为绝对定位
    e.points = e.points.map(ne => {
      const x = ne.x + offsetX;
      const y = ne.y + offsetY;
      return {
        ...ne,
        x,
        y
      };
    });
  });
};
```
