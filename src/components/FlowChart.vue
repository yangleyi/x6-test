<template>
  <div class="graph-container">
    <div id="container" class="container"></div>
    <div
      v-show="menu.visible"
      class="remove-menu"
      :style="{left: menu.x + 'px', top: menu.y + 'px'}"
      @click="onRemoveEdge"
    >
      删除
    </div>
  </div>
</template>

<script>
import { Graph, Addon } from '@antv/x6'
import {
  graphOptionFn,
  stencilOptionfn,
  bindKeysFn,
  registNodes,
  createNodeShapes,
  createImageShapes,
  createGraphDom
} from './flowOptions'
export default {
  name: 'FlowChart',
  data() {
    return {
      graph: null,
      menu: {
        x: 0,
        y: 0,
        visible: false
      },
      cellId: null
    }
  },
  mounted() {
    this.init()
  },
  methods: {
    onRemoveEdge() {
      this.graph.removeEdge(this.cellId)
      this.menu.visible = false
      this.cell = null
    },
    init() {
      createGraphDom()
      // #region 初始化画布
      const graph = new Graph(graphOptionFn(document.getElementById('graph-container')))
      // #endregion

      // #region 初始化 stencil
      const stencil = new Addon.Stencil(stencilOptionfn(graph))
      document.getElementById('stencil').appendChild(stencil.container)
      // #endregion

      bindKeysFn(graph)

      // 双击边
      graph.on('edge:dblclick', ({ cell, e }) => {
        cell.addTools({
          name: 'edge-editor',
          args: {
            event: e,
          },
        })
        this.menu.visible = false
      })

      // 单击边
      graph.on('edge:click', ({cell, x, y}) => {
        this.cellId = cell.id
        this.menu.x = x + 200
        this.menu.y = y
        this.menu.visible = true
      })

      // 空白点击
      graph.on('blank:click', () => {
        this.menu.visible = false
        this.cellId = null
      })

      // 双击节点
      graph.on('node:dblclick', (e) => {
        console.log(`双击了节点：${e.node.label}`)
        // console.log(e)
      })

      // 控制连接桩显示/隐藏
      const showPorts = (ports, show) => {
        for (let i = 0, len = ports.length; i < len; i = i + 1) {
          ports[i].style.visibility = show ? 'visible' : 'hidden'
        }
      }
      graph.on('node:mouseenter', () => {
        const container = document.getElementById('graph-container')
        const ports = container.querySelectorAll(
          '.x6-port-body',
        )
        showPorts(ports, true)
      })
      graph.on('node:mouseleave', () => {
        const container = document.getElementById('graph-container')
        const ports = container.querySelectorAll(
          '.x6-port-body',
        )
        showPorts(ports, false)
      })
      // #endregion

      registNodes(Graph)

      stencil.load([...createNodeShapes(graph)], 'group1')

      stencil.load(createImageShapes(graph), 'group2')
      this.graph = graph
    },
  }
}
</script>

<style scoped>
.graph-container {
  position: relative;
}
.remove-menu {
  position: absolute;
  left: 500px;
  top: 100px;
  z-index: 2;
  background: #FFFFFF;
  color: #dd0000;
  cursor: pointer;
  width: 180px;
  padding: 5px 10px;
  box-sizing: border-box;
  box-shadow: 1px 1px 5px 1px #ccc;
}
.container {
  height: 100vh;
}
</style>
