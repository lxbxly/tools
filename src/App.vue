<template>
  <div class="app">
    <h1>Vue 3 + mxgraph 图形关系展示</h1>
    <div class="button-container">
      <button @click="addRandomEdge" class="add-edge-btn">添加随机连线</button>
      &nbsp;
      <button @click="addEdgeWithCreate" class="add-edge-btn">使用 createEdge 添加连线</button>

      &nbsp;
      <button @click="addGeometry" class="add-edge-btn">添加图形</button>
    </div>
    <div ref="graphContainer" class="graph-container"></div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'

// 在导入 mxgraph 之前定义全局变量，避免 mxLoadResources is not defined 错误
if (typeof window.mxLoadResources === 'undefined') {
  window.mxLoadResources = false
}
if (typeof window.mxLoadStylesheets === 'undefined') {
  window.mxLoadStylesheets = false
}
if (typeof window.mxForceIncludes === 'undefined') {
  window.mxForceIncludes = false
}
if (typeof window.mxResourceExtension === 'undefined') {
  window.mxResourceExtension = '.txt'
}

import mxgraph from 'mxgraph'

const graphContainer = ref(null)
const graphRef = ref(null)
const vertices = ref([])

const addRandomEdge = () => {
  if (vertices.value.length < 2) {
    alert('至少需要两个节点才能添加连线')
    return
  }

  const graph = graphRef.value
  if (!graph) {
    alert('图形实例未初始化')
    return
  }

  // const parent = graph.getDefaultParent()

  // graph.getModel().beginUpdate()
  try {
    const randomIndex1 = Math.floor(Math.random() * vertices.value.length)
    let randomIndex2 = Math.floor(Math.random() * vertices.value.length)

    while (randomIndex2 === randomIndex1) {
      randomIndex2 = Math.floor(Math.random() * vertices.value.length)
    }

    const edgeLabel = `关系 ${vertices.value.length + 1}`
    const style = 'strokeColor=#ddd;strokeWidth=3;endArrow=classic';
    const edge = graph.insertEdge(null, null, edgeLabel, vertices.value[randomIndex1], vertices.value[randomIndex2], style)

    // graph.getModel().endUpdate()

    graph.refresh()

    return edge
  } catch (error) {
    console.error('添加连线失败:', error)
    graph.getModel().endUpdate()
    return null
  }
}

const addEdgeWithCreate = () => {
  if (vertices.value.length < 2) {
    alert('至少需要两个节点才能添加连线')
    return
  }

  const graph = graphRef.value
  if (!graph) {
    alert('图形实例未初始化')
    return
  }

  // const parent = graph.getDefaultParent()

  try {
    const randomIndex1 = Math.floor(Math.random() * vertices.value.length)
    let randomIndex2 = Math.floor(Math.random() * vertices.value.length)

    while (randomIndex2 === randomIndex1) {
      randomIndex2 = Math.floor(Math.random() * vertices.value.length)
    }

    const edgeLabel = `CreateEdge ${vertices.value.length + 1}`

    // 使用 createEdge 创建边对象
    const style = 'strokeColor=red;strokeWidth=3;endArrow=classic';
    const edge = graph.createEdge(null, null, edgeLabel, vertices.value[randomIndex1], vertices.value[randomIndex2], style)

    // 使用 add 方法添加到模型
    graph.addEdge(edge, null, vertices.value[randomIndex1], vertices.value[randomIndex2])

    graph.refresh()

    return edge
  } catch (error) {
    console.error('使用 createEdge 和 add 方法添加连线失败:', error)
    return null
  }
}

const addGeometry = () => {
  const graph = graphRef.value
  if (!graph) {
    alert('图形实例未初始化')
    return
  }

  // const parent = graph.getDefaultParent()

  try {
    const x = Math.random() * 400 + 50
    const y = Math.random() * 300 + 50
    const width = Math.random() * 60 + 40
    const height = Math.random() * 40 + 20

    const vertexLabel = `节点 ${vertices.value.length + 1}`
    const style = 'fillColor=#e1d5e7;strokeColor=#9673a6;rounded=1'

    const vertex = graph.insertVertex(null, null, vertexLabel, x, y, width, height, style)

    vertices.value.push(vertex)

    // graph.refresh()

    return vertex
  } catch (error) {
    console.error('添加图形失败:', error)
    return null
  }
}

onMounted(() => {
  // 初始化 mxgraph
  const { mxGraph, mxGraphModel, mxGeometry, mxVertexHandler, mxEdgeHandler, mxRubberband } = mxgraph()

  // 创建图形实例
  const graph = new mxGraph(graphContainer.value)
  graphRef.value = graph

  // 启用拖放功能
  new mxRubberband(graph)

  // 获取默认父元素
  const parent = graph.getDefaultParent()

  console.log(graph)

  // 开始事务
  // graph.getModel().beginUpdate()

  try {
    // 创建节点
    const v1 = graph.insertVertex(parent, null, '节点 1', 20, 20, 80, 30)
    const v2 = graph.insertVertex(parent, null, '节点 2', 200, 150, 80, 30)
    const v3 = graph.insertVertex(parent, null, '节点 3', 100, 250, 80, 30)
    const v4 = graph.insertVertex(parent, null, '节点 3', 180, 150, 10, 50)
    const v5 = graph.insertVertex(parent, null, '节点 3', 500, 149, 50, 100)

    vertices.value = [v1, v2, v3, v4, v5]

    // // 创建边
    graph.insertEdge(parent, null, '关系 1', v1, v2)
    // graph.insertEdge(parent, null, '关系 2', v2, v3)
    // graph.insertEdge(parent, null, '关系 3', v3, v1)
  } finally {
    // 结束事务
    // graph.getModel().endUpdate()
  }
})
</script>

<style scoped>
.app {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
}

h1 {
  text-align: center;
  color: #333;
  margin-bottom: 30px;
}

.button-container {
  text-align: center;
  margin-bottom: 20px;
}

.add-edge-btn {
  padding: 10px 20px;
  background-color: #4CAF50;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 14px;
  transition: background-color 0.3s;
}

.add-edge-btn:hover {
  background-color: #45a049;
}

.add-edge-btn:active {
  background-color: #3d8b40;
}

.graph-container {
  width: 100%;
  height: 400px;
  border: 1px solid #ddd;
  background-color: #f9f9f9;
}
</style>