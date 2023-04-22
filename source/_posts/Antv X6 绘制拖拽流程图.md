---
title: Antv X6 绘制拖拽流程图
date: 2021-10-20 17:21:06
---
#  使用antv x6 绘制拖拽流程图

## 安装

```
# npm
$ npm install @antv/x6 --save

# yarn
$ yarn add @antv/x6
复制代码
```

## 使用

**创建容器**

```
<!-- 画布 -->
<div class="graph" ref="graph"></div>
<!-- 工具栏 -->
<div class="stencil" ref="stencil"></div>
复制代码
```

**创建实例画布**

```
// 引入
import { Graph, Addon, Shape } from '@antv/x6';
// 创建实例
const graph = new Graph({
    container: this.$refs.graph, // 画布的容器
    // grid: true, // 网格
    width: 770, 
    height: 590,
    mousewheel: {
       enabled: true,
       zoomAtMousePosition: true,
       modifiers: 'ctrl',
       minScale: 0.5,
       maxScale: 3,
    }, // 鼠标滚轮缩放
    resizing: true, // 缩放节点
    selecting: {
        enabled: true,
        rubberband: true,
        showNodeSelectionBox: true,
    }, // 点选/框选
    connecting: {
        router: {
            name: 'manhattan',
            args: {
                padding: 1,
            },
        },
        connector: {
            name: 'rounded',
            args: {
                radius: 8,
            },
        },
        anchor: 'center',
        connectionPoint: 'anchor',
        allowBlank: false,
        snap: {
            radius: 20,
        },
        createEdge() {
            return new Shape.Edge({
                attrs: {
                    line: {
                        stroke: '#9ED4FF',
                        strokeWidth: 2,
                        targetMarker: {
                            name: 'block',
                            width: 12,
                            height: 8,
                        },
                    },
                },
                zIndex: 0,
            });
        },
        validateConnection({ targetMagnet }) {
            return !!targetMagnet;
        },
    }, // 连线选项
    highlighting: {
        magnetAdsorbed: {
            name: 'stroke',
            args: {
                attrs: {
                    fill: '#5F95FF',
                    stroke: '#5F95FF',
                },
            },
        },
    }, // 高亮选项。
    snapline: true, // 对齐线
    keyboard: true, // 键盘快捷键
    clipboard: true, // 剪切板
});
复制代码
```

**创建工具栏实例**

```
// 工具栏
const stencil = new Addon.Stencil({
    title: '流程图', // 标题
    target: graph, // 目标画布
    stencilGraphWidth: 600,
    stencilGraphHeight: 500,
    collapsable: false, // 分组是否可折叠
    groups: [
        {
        title: '基础流程图',
        name: 'group1',
        },
    ], // 提供的分组
    layoutOptions: { 
        columns: 1,
        columnWidth: 80,
        rowHeight: 50,
    }, // 来对节点进行自动布局
});
// 将     this.$refs.stencil.appendChild(stencil.container);
复制代码
```

**连接点配置**

![微信图片_20220328104037.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/dd208184e6674fb5877f7023a777170d~tplv-k3u1fbpfcp-zoom-1.image)

```
// 连接点 上下左右四个连接点
const ports = {
    groups: {
        top: {
            position: 'top',
            attrs: {
                circle: {
                    r: 4,
                    magnet: true,
                    stroke: '#5F95FF',
                    strokeWidth: 1,
                    fill: '#fff',
                    style: {
                        visibility: 'hidden',
                    },
                },
            },
        },
        right: {
            position: 'right',
            attrs: {
                circle: {
                    r: 4,
                    magnet: true,
                    stroke: '#5F95FF',
                    strokeWidth: 1,
                    fill: '#fff',
                    style: {
                        visibility: 'hidden',
                    },
                },
            },
        },
        bottom: {
            position: 'bottom',
            attrs: {
                circle: {
                    r: 4,
                    magnet: true,
                    stroke: '#5F95FF',
                    strokeWidth: 1,
                    fill: '#fff',
                    style: {
                        visibility: 'hidden',
                    },
                },
            },
        },
        left: {
            position: 'left',
            attrs: {
                circle: {
                    r: 4,
                    magnet: true,
                    stroke: '#5F95FF',
                    strokeWidth: 1,
                    fill: '#fff',
                    style: {
                        visibility: 'hidden',
                    },
                },
            },
        },
    },
    items: [
        {
            group: 'top',
        },
        {
            group: 'right',
        },
        {
            group: 'bottom',
        },
        {
            group: 'left',
        },
    ],
};
复制代码
```

**自定义节点**

```
// 自定义节点
Graph.registerNode(
    'custom-rect',
    {
        inherit: 'rect',
        width: 66,
        height: 36,
        attrs: {
            body: {
                strokeWidth: 1,
                stroke: '#5F95FF',
                fill: '#EFF4FF',
            },
            text: {
                fontSize: 12,
                fill: '#262626',
            },
        },
        ports: { ...ports },
    },
    true
);

Graph.registerNode(
    'custom-polygon',
    {
        inherit: 'polygon',
        width: 66,
        height: 36,
        attrs: {
            body: {
                strokeWidth: 1,
                stroke: '#5F95FF',
                fill: '#EFF4FF',
            },
            text: {
                fontSize: 12,
                fill: '#262626',
            },
        },
        ports: {
            ...ports,
            items: [
                {
                    group: 'top',
                },
                {
                    group: 'bottom',
                },
            ],
        },
    },
    true
);

Graph.registerNode(
    'custom-circle',
    {
        inherit: 'circle',
        width: 45,
        height: 45,
        attrs: {
            body: {
                strokeWidth: 1,
                stroke: '#5F95FF',
                fill: '#EFF4FF',
            },
            text: {
                fontSize: 12,
                fill: '#262626',
            },
        },
        ports: { ...ports },
    },
    true
);

const r1 = graph.createNode({
    shape: 'custom-rect',
    attrs: {
        text: {
            fontSize: 18,
        },
        body: {
            rx: 40,
            ry: 26,
        },
    },
});
const r2 = graph.createNode({
    shape: 'custom-rect',
    attrs: {
        text: {
            fontSize: 18,
        },
    },
});
const r3 = graph.createNode({
    shape: 'custom-rect',
    attrs: {
        text: {
            fontSize: 18,
        },
        body: {
            rx: 6,
            ry: 6,
        },
    },
});
const r4 = graph.createNode({
    shape: 'custom-polygon',
    attrs: {
        body: {
            refPoints: '0,10 10,0 20,10 10,20',
        },
        text: {
            fontSize: 18,
        },
    },
});
stencil.load([r1, r2, r3, r4], 'group1');
复制代码
```

**快捷键和事件触发**

```
// 快捷键与事件
// 复制粘贴
graph.bindKey(['meta+c', 'ctrl+c'], () => {
    const cells = graph.getSelectedCells();
    if (cells.length) {
    	graph.copy(cells);
    }
	return false;
});
graph.bindKey(['meta+x', 'ctrl+x'], () => {
    const cells = graph.getSelectedCells();
    if (cells.length) {
    	graph.cut(cells);
	}
	return false;
});
graph.bindKey(['meta+v', 'ctrl+v'], () => {
    if (!graph.isClipboardEmpty()) {
        const cells = graph.paste({ offset: 32 });
        graph.cleanSelection();
        graph.select(cells);
    }
    return false;
});
// 控制连接桩显示/隐藏
var showPorts = function (ports, show) {
    for (var i = 0, len = ports.length; i < len; i = i + 1) {
    	ports[i].style.visibility = show ? 'visible' : 'hidden';
    }
};
// 触摸
graph.on('node:mouseenter', ({ node }) => {
    // 显示节点删除按钮
    node.addTools({
        name: 'button-remove',
        args: {
            x: '100%',
            y: 0,
            offset: { x: -10, y: 10 },
        },
	});
    var container = this.$refs.graph;
    var ports = container.querySelectorAll('.x6-port-body');
    showPorts(ports, true);
});
graph.on('node:mouseleave', ({ node }) => {
    // 移除节点删除按钮
    node.removeTools();
    var container = this.$refs.graph;
    var ports = container.querySelectorAll('.x6-port-body');
    showPorts(ports, false);
});
// 文字输入
graph.on('cell:dblclick', ({ cell, e }) => {
    const isNode = cell.isNode();
    const name = cell.isNode() ? 'node-editor' : 'edge-editor';
    cell.removeTool(name);
    cell.addTools({
        name,
        args: {
            event: e,
            attrs: {
                backgroundColor: isNode ? '#EFF4FF' : '#FFF',
                fontSize: 18,
            },
        },
    });
});   
复制代码
```

![image-20220328103743007.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e610c6d1942b4941ae6f1b68b0996e53~tplv-k3u1fbpfcp-zoom-1.image)

  
