<template>
  <div>
    <div>
      <input type="file" ref="fileInput" @change="onFileSelected">
      <button @click="selectFile">选择文件</button>
    </div>
    <div>
      <button v-for="button in buttons" :key="button" @click="setA(button)">{{ button }}</button>
    </div>
    <div ref="graphContainer"></div>
  </div>
</template>
<script>
// import exampleImage from './../校园网络拓扑.jfif';
import * as d3force3d from 'd3-force-3d';
import ForceGraph3D from '3d-force-graph';
import * as d3 from 'd3';
import * as THREE from 'three';
import * as d3force from "https://cdn.skypack.dev/d3-force@3";

function getNodeSize(node) {
  if (node.level === "1") {
    return 10;
  } else if (node.level === "2") {
    return 5;
  } else if (node.level === "3") {
    return 1;
  } else {
    return 0.1;
  }
}

function forceAtlas2Improve(nodes, links) {
  const repulsiveForceStrength = -20; // 斥力系数
  const attractiveForceStrength = 0.2; // 引力系数
  const distanceMax = 50; // 最大节点间距离

  function force(alpha) {
    // 计算节点之间的斥力
    for (let j = 0; j < nodes.length; j++) {
      const node1 = nodes[j];
      for (let k = j + 1; k < nodes.length; k++) {
        const node2 = nodes[k];
        const dx = node2.x - node1.x;
        const dy = node2.y - node1.y;
        const dz = node2.z - node1.z;
        const distance = Math.sqrt(dx * dx + dy * dy + dz * dz);
        if (distance > 0 && distance < distanceMax) {
          const force = repulsiveForceStrength / (distance * distance);
          const fx = force * dx / distance;
          const fy = force * dy / distance;
          const fz = force * dz / distance;
          node1.vx -= fx;
          node1.vy -= fy;
          node1.vz -= fz;
          node2.vx += fx;
          node2.vy += fy;
          node2.vz += fz;
        }
      }
    }

    // 计算连线之间的引力
    links.forEach(link => {
      const node1 = link.source;
      const node2 = link.target;
      const dx = node2.x - node1.x;
      const dy= node2.y - node1.y;
      const dz = node2.z - node1.z;
      const distance = Math.sqrt(dx * dx + dy * dy + dz * dz);
      if (distance > 0 && distance < distanceMax) {
      const force = attractiveForceStrength * (distance * distance) / repulsiveForceStrength;
      const fx = force * dx / distance;
      const fy = force * dy / distance;
      const fz = force * dz / distance;
      node1.vx += fx;
      node1.vy += fy;
      node1.vz += fz;
      node2.vx -= fx;
      node2.vy -= fy;
      node2.vz -= fz;
      }
      });

    // 更新节点位置
    nodes.forEach(node => {
      node.x += node.vx * alpha;
      node.y += node.vy * alpha;
      node.z += node.vz * alpha;
    });
  }
  return force;
}

function forceAtlas2(nodes, links) {
  const repulsiveForceStrength = -30; // 斥力系数
  const attractiveForceStrength = 0.2; // 引力系数
  const distanceMax = 100; // 最大节点间距离

  function force(alpha) {
    // 计算节点之间的斥力
    for (let j = 0; j < nodes.length; j++) {
      const node1 = nodes[j];
      for (let k = j + 1; k < nodes.length; k++) {
        const node2 = nodes[k];
        const dx = node2.x - node1.x;
        const dy = node2.y - node1.y;
        const dz = node2.z - node1.z;
        const distance = Math.sqrt(dx * dx + dy * dy + dz * dz);
        if (distance > 0 && distance < distanceMax) {
          const force = repulsiveForceStrength / (distance * distance);
          const fx = force * dx / distance;
          const fy = force * dy / distance;
          const fz = force * dz / distance;
          node1.vx -= fx;
          node1.vy -= fy;
          node1.vz -= fz;
          node2.vx += fx;
          node2.vy += fy;
          node2.vz += fz;
        }
      }
    }

    // 计算连线之间的引力
    links.forEach(link => {
      const node1 = link.source;
      const node2 = link.target;
      const dx = node2.x - node1.x;
      const dy= node2.y - node1.y;
      const dz = node2.z - node1.z;
      const distance = Math.sqrt(dx * dx + dy * dy + dz * dz);
      if (distance > 0 && distance < distanceMax) {
      const force = attractiveForceStrength * (distance * distance) / repulsiveForceStrength;
      const fx = force * dx / distance;
      const fy = force * dy / distance;
      const fz = force * dz / distance;
      node1.vx += fx;
      node1.vy += fy;
      node1.vz += fz;
      node2.vx -= fx;
      node2.vy -= fy;
      node2.vz -= fz;
      }
      });

    // 更新节点位置
    nodes.forEach(node => {
      node.x += node.vx * alpha;
      node.y += node.vy * alpha;
      node.z += node.vz * alpha;
    });
  }
  return force;
}

function forceAtlas2New(nodes, links, width, height) {
  const distanceMax = Math.min(width, height) / 2;
  const repulsiveForceStrength = -Math.pow(distanceMax, 2) / nodes.length;
  const attractiveForceStrength = Math.pow(distanceMax, 2) * 0.01 * nodes.length / links.length;

  function force(alpha) {
    // 计算节点之间的斥力
    for (let j = 0; j < nodes.length; j++) {
      const node1 = nodes[j];
      for (let k = j + 1; k < nodes.length; k++) {
        const node2 = nodes[k];
        const dx = node2.x - node1.x;
        const dy = node2.y - node1.y;
        const dz = node2.z - node1.z;
        const distance = Math.sqrt(dx * dx + dy * dy + dz * dz);
        if (distance > 0 && distance < distanceMax) {
          const force = repulsiveForceStrength / (distance * distance);
          const fx = force * dx / distance;
          const fy = force * dy / distance;
          const fz = force * dz / distance;
          node1.vx -= fx;
          node1.vy -= fy;
          node1.vz -= fz;
          node2.vx += fx;
          node2.vy += fy;
          node2.vz += fz;
        }
      }
    }

    // 计算连线之间的引力
    links.forEach(link => {
      const node1 = link.source;
      const node2 = link.target;
      const dx = node2.x - node1.x;
      const dy= node2.y - node1.y;
      const dz = node2.z - node1.z;
      const distance = Math.sqrt(dx * dx + dy * dy + dz * dz);
      if (distance > 0 && distance < distanceMax) {
      const force = attractiveForceStrength * (distance * distance) / repulsiveForceStrength;
      const fx = force * dx / distance;
      const fy = force * dy / distance;
      const fz = force * dz / distance;
      node1.vx += fx;
      node1.vy += fy;
      node1.vz += fz;
      node2.vx -= fx;
      node2.vy -= fy;
      node2.vz -= fz;
      }
      });

    // 更新节点位置
    nodes.forEach(node => {
      node.x += node.vx * alpha;
      node.y += node.vy * alpha;
      node.z += node.vz * alpha;
    });
  }
  return force;
}

function generateForceAtlas2LayoutData(nodes, links) {
  const data = {
    nodes: [],
    links: []
  };
  nodes.forEach(node => {
    data.nodes.push({ id: node.id });
  });
  links.forEach(link => {
    data.links.push({ source: link.source, target: link.target });
  });
  
  const width = 600;
    const height = 400;
  const simulation = d3force.forceSimulation(data.nodes)
    .force("link", d3force.forceLink(data.links).id(d => d.id))
    .force("charge", d3force.forceManyBody())
    .force("center", d3force.forceCenter(width / 2, height / 2))
    .force("atlas", d3force.forceAtlas2().size([width, height]).iterations(10));

// simulation.on("tick", () => {
//     // 更新节点和线条位置的代码
// });

  // const simulation = d3.forceSimulation(data.nodes)
  //   .force('link', d3.forceLink(data.links).id(d => d.id))
  //   .force('charge', d3.forceManyBody())
  //   .force('center', d3.forceCenter())
  //   .force('forceAtlas2', d3Force3d.forceAtlas2())
  //   .stop();
  for (let i = 0; i < 300; i++) {
    simulation.tick();
  }
  data.nodes.forEach((node, i) => {
    node.x = data.nodes[i].x;
    node.y = data.nodes[i].y;
    node.z = data.nodes[i].z;
  });
  return data;
  console.log(data)
}
function generateData(numNodes) {
  const nodes = [];
  const links = [];

  // 生成节点数据
  for (let i = 1; i <= numNodes; i++) {
    const node = {
      id: `node${i}`,
      name: `Node ${i}`,
      group: Math.ceil(Math.random() * 3),
      x: Math.random() * 100,
      y: Math.random() * 100,
      z: Math.random() * 100
    };
    nodes.push(node);
  }

  // 生成链接数据
  for (let i = 1; i <= numNodes; i++) {
    const source = `node${i}`;
    const numLinks = Math.floor(Math.random() * numNodes / 2);
    for (let j = 0; j < numLinks; j++) {
      const target = `node${Math.ceil(Math.random() * numNodes)}`;
      links.push({ source, target, value: Math.random() });
    }
  }

  return { nodes, links };
}

function generateNodeData(numNodes, numGroups) {
  const data = { nodes: [], links: [] };
  const groupSizes = Array.from({ length: numGroups }, () => 0);

  for (let i = 0; i < numNodes; i++) {
    const node = {
      id: `node${i + 1}`,
      name: `Node ${i + 1}`,
      group: Math.floor(Math.random() * numGroups) + 1
    };
    data.nodes.push(node);
    groupSizes[node.group - 1]++;
  }

  for (let i = 0; i < numNodes - 1; i++) {
    const source = data.nodes[i].id;
    const targetIndex = i + 1 + Math.floor(Math.random() * (numNodes - i - 1));
    const target = data.nodes[targetIndex].id;
    const value = Math.floor(Math.random() * 5) + 1;
    data.links.push({ source, target, value });
  }

  return data;
}

function generateSpringLayoutData(nodes, links, distance) {
  const graph = new Graph();
  // 添加节点
  nodes.forEach(node => {
    graph.addNode(node.id, node);
  });
  // 添加边
  links.forEach(link => {
    graph.addEdge(link.source, link.target);
  });
  // 计算Spring布局
  const layout = new Layout.ForceDirected(graph, distance);
  layout.run();
  // 获取节点的布局坐标
  const nodePositions = layout.getPositions();
  // 转换为3d坐标
  const data = {
    nodes: nodes.map(node => ({
      id: node.id,
      x: nodePositions[node.id].x,
      y: nodePositions[node.id].y,
      z: nodePositions[node.id].z
    })),
    links: links
  };
  return data;
}

function generateGridLayoutData(rows, columns, distance) {
  const data = {
    nodes: [],
    links: []
  };
  let id = 0;
  for (let i = 0; i < rows; i++) {
    for (let j = 0; j < columns; j++) {
      const node = {
        id: `node${id}`,
        x: i * distance,
        y: j * distance,
        z: 0
      };
      data.nodes.push(node);
      if (i > 0) {
        const link = {
          source: `node${id}`,
          target: `node${id - columns}`
        };
        data.links.push(link);
      }
      if (j > 0) {
        const link = {
          source: `node${id}`,
          target: `node${id - 1}`
        };
        data.links.push(link);
      }
      id++;
    }
  }
  return data;
}

function generateKKLayoutData(nodes, links) {
  const data = {
    nodes: [],
    links: []
  };
  // 将输入数据转换为3d-force-graph所需的格式
  nodes.forEach(node => {
    data.nodes.push({ id: node.id });
  });
  links.forEach(link => {
    data.links.push({ source: link.source, target: link.target });
  });
  // 使用d3-force-3d库中的力导向算法生成KK布局
  const simulation = d3.forceSimulation(data.nodes)
    .force('link', d3.forceLink(data.links).id(d => d.id))
    .force('charge', d3.forceManyBody())
    .force('center', d3.forceCenter())
    .force('x', d3.forceX())
    .force('y', d3.forceY())
    .force('z', d3.forceZ())
    .stop();
  for (let i = 0; i < 300; i++) {
    simulation.tick();
  }
  // 将节点位置信息添加到3d-force-graph所需的格式中
  data.nodes.forEach((node, i) => {
    node.x = data.nodes[i].x;
    node.y = data.nodes[i].y;
    node.z = data.nodes[i].z;
  });
  return data;
}

// function generateForceAtlas2LayoutData(nodes, links) {
//   const data = {
//     nodes: [],
//     links: []
//   };
//   // 将输入数据转换为3d-force-graph所需的格式
//   nodes.forEach(node => {
//     data.nodes.push({ id: node.id });
//   });
//   links.forEach(link => {
//     data.links.push({ source: link.source, target: link.target });
//   });
//   // 使用d3-force-3d库中的力导向算法生成ForceAtlas2布局
//   const simulation = d3.forceSimulation(data.nodes)
//     .force('link', d3.forceLink(data.links).id(d => d.id))
//     .force('charge', d3.forceManyBody())
//     .force('center', d3.forceCenter())
//     // .force('forceAtlas2', d3.forceAtlas2())
//     .stop();
//   for (let i = 0; i < 300; i++) {
//     simulation.tick();
//   }
//   // 将节点位置信息添加到3d-force-graph所需的格式中
//   data.nodes.forEach((node, i) => {
//     node.x = data.nodes[i].x;
//     node.y = data.nodes[i].y;
//     node.z = data.nodes[i].z;
//   });
//   return data;
// }

function generateStarLayoutData(numNodes, radius) {
  const data = {
    nodes: [],
    links: []
  };
  const centerNode = {
    id: 'center',
    x: 0,
    y: 0,
    z: 0
  };
  data.nodes.push(centerNode);
  for (let i = 0; i < numNodes; i++) {
    const angle = (2 * Math.PI * i) / numNodes;
    const x = radius * Math.cos(angle);
    const y = radius * Math.sin(angle);
    const node = {
      id: `node${i}`,
      x,
      y,
      z: 0
    };
    data.nodes.push(node);
    const link = {
      source: `node${i}`,
      target: 'center'
    };
    data.links.push(link);
  }
  return data;
}

function generateCircularLayoutData(numNodes, radius) {
  const data = {
    nodes: [],
    links: []
  };
  const angle = (2 * Math.PI) / numNodes;
  for (let i = 0; i < numNodes; i++) {
    const x = radius * Math.cos(i * angle);
    const y = radius * Math.sin(i * angle);
    const node = {
      id: `node${i}`,
      x,
      y,
      z: 0
    };
    data.nodes.push(node);
    if (i > 0) {
      const link = {
        source: `node${i}`,
        target: `node${i - 1}`
      };
      data.links.push(link);
    }
  }
  const link = {
    source: `node0`,
    target: `node${numNodes - 1}`
  };
  data.links.push(link);
  return data;
}

export default {
  data() {
    return {
      SpringData: {
      nodes: [
        { id: 'node0' },
        { id: 'node1' },
        { id: 'node2' },
        { id: 'node3' },
        { id: 'node4' },
        { id: 'node5' }
      ],
      links: [
        { source: 'node0', target: 'node1' },
        { source: 'node1', target: 'node2' },
        { source: 'node2', target: 'node3' },
        { source: 'node3', target: 'node4' },
        { source: 'node4', target: 'node5' },
        { source: 'node5', target: 'node0' }
      ],
      distance: 50
    },
      graphData: {
        nodes: [
          { id: 'A' },
          { id: 'B' },
          { id: 'C' },
          { id: 'D' },
          { id: 'E' }
        ],
        links: [
          { source: 'A', target: 'B' },
          { source: 'B', target: 'C' },
          { source: 'C', target: 'D' },
          { source: 'D', target: 'E' },
          { source: 'E', target: 'A' }
        ],
      },
      isLayoutRunning: false,
      A: null,
      newdata: null,
      buttons: ['loop', 'star', 'fruchterman', 'test'],
      // buttons: ['orient', 'loop', 'star', 'forceatlas2', 'fruchterman', 'test'],
    //   treeData : {
    //   "nodes": [
    //     {"id": 1, "name": "Level 1 Node 1", "color": "#00ffff", "level": "1", "size": "30"},
    //     {"id": 2, "name": "Level 1 Node 2", "color": "#00ffff", "level": "1", "size": "30"},

    //     {"id": 3, "name": "Level 2 Node 3", "color": "#ff0000", "level": "2", "Size": "15"},
    //     {"id": 4, "name": "Level 2 Node 4", "color": "#ff0000", "level": "2", "Size": "15"},
    //     {"id": 5, "name": "Level 2 Node 5", "color": "#ff0000", "level": "2", "Size": "15"},
    //     {"id": 6, "name": "Level 2 Node 6", "color": "#ff0000", "level": "2", "Size": "15"},
    //     {"id": 7, "name": "Level 2 Node 7", "color": "#ff0000", "level": "2", "Size": "15"},
    //     {"id": 8, "name": "Level 2 Node 8", "color": "#ff0000", "level": "2", "Size": "15"},
    //     {"id": 9, "name": "Level 2 Node 9", "color": "#ff0000", "level": "2", "Size": "15"},

    //     {"id": 10, "name": "Level 3 Node 10", "color": "#0000ff", "level": "3", "size": "10"},
    //     {"id": 11, "name": "Level 3 Node 11", "color": "#0000ff", "level": "3", "size": "10"},
    //     {"id": 12, "name": "Level 3 Node 12", "color": "#0000ff", "level": "3", "size": "10"},
    //     {"id": 13, "name": "Level 3 Node 13", "color": "#0000ff", "level": "3", "size": "10"},
    //     {"id": 14, "name": "Level 3 Node 14", "color": "#0000ff", "level": "3", "size": "10"},
    //     {"id": 15, "name": "Level 3 Node 15", "color": "#0000ff", "level": "3", "size": "10"},
    //     {"id": 16, "name": "Level 3 Node 16", "color": "#0000ff", "level": "3", "size": "10"},
    //     {"id": 17, "name": "Level 3 Node 17", "color": "#0000ff", "level": "3", "size": "10"},
    //     {"id": 18, "name": "Level 3 Node 18", "color": "#0000ff", "level": "3", "size": "10"},
    //     {"id": 19, "name": "Level 3 Node 19", "color": "#0000ff", "level": "3", "size": "10"},
    //     {"id": 20, "name": "Level 3 Node 20", "color": "#0000ff", "level": "3", "size": "10"},
    //     {"id": 21, "name": "Level 3 Node 21", "color": "#0000ff", "level": "3", "size": "10"},
    //     {"id": 22, "name": "Level 3 Node 22", "color": "#0000ff", "level": "3", "size": "10"},
    //     {"id": 23, "name": "Level 3 Node 23", "color": "#0000ff", "level": "3", "size": "10"},
    //     {"id": 24, "name": "Level 3 Node 24", "color": "#0000ff", "level": "3", "size": "10"},
    //     {"id": 25, "name": "Level 3 Node 25", "color": "#0000ff", "level": "3", "size": "10"},
    //     {"id": 26, "name": "Level 3 Node 26", "color": "#0000ff", "level": "3", "size": "10"},
    //     {"id": 27, "name": "Level 3 Node 27", "color": "#0000ff", "level": "3", "size": "10"},
    //     {"id": 28, "name": "Level 3 Node 28", "color": "#0000ff", "level": "3", "size": "10"},
    //     {"id": 29, "name": "Level 3 Node 29", "color": "#0000ff", "level": "3", "size": "10"},
    //     {"id": 30, "name": "Level 3 Node 30", "color": "#0000ff", "level": "3", "size": "10"},
    //     {"id": 31, "name": "Level 3 Node 31", "color": "#0000ff", "level": "3", "size": "10"}
        
    //   ],
    //   "links": [
    //     {"source": 1, "target": 3},
    //     {"source": 1, "target": 4},
    //     {"source": 1, "target": 5},
    //     {"source": 1, "target": 6},
    //     {"source": 1, "target": 7},
    //     {"source": 1, "target": 8},
    //     {"source": 1, "target": 9},
    //     {"source": 2, "target": 3},
    //     {"source": 2, "target": 4},
    //     {"source": 2, "target": 5},
    //     {"source": 2, "target": 6},
    //     {"source": 2, "target": 7},
    //     {"source": 2, "target": 8},
    //     {"source": 2, "target": 9},
    //     {"source": 3, "target": 10},
    //     {"source": 3, "target": 11},
    //     {"source": 3, "target": 12},
    //     {"source": 3, "target": 13},
    //     {"source": 4, "target": 14},
    //     {"source": 4, "target": 15},
    //     {"source": 4, "target": 16},
    //     {"source": 4, "target": 17},
    //     {"source": 5, "target": 18},
    //     {"source": 5, "target": 19},
    //     {"source": 5, "target": 20},
    //     {"source": 5, "target": 21},
    //     {"source": 6, "target": 22},
    //     {"source": 6, "target": 23},
    //     {"source": 6, "target": 24},
    //     {"source": 6, "target": 25},
    //     {"source": 7, "target": 26},
    //     {"source": 7, "target": 27},
    //     {"source": 7, "target": 28},
    //     {"source": 7, "target": 29},
    //     {"source": 8, "target": 30},
    //     {"source": 9, "target": 31},
    //   ]
    // },

      d3data : {
      nodes: [
        { id: 'node1', name: 'Node 1' },
        { id: 'node2', name: 'Node 2' },
        { id: 'node3', name: 'Node 3' },
        { id: 'node4', name: 'Node 4' },
        { id: 'node5', name: 'Node 5' },
        { id: 'node6', name: 'Node 6' },
        { id: 'node7', name: 'Node 7' },
        { id: 'node8', name: 'Node 8' },
        { id: 'node9', name: 'Node 9' },
      ],
      links: [
        { source: 'node1', target: 'node2' },
        { source: 'node1', target: 'node3' },
        { source: 'node2', target: 'node4' },
        { source: 'node2', target: 'node5' },
        { source: 'node3', target: 'node6' },
        { source: 'node3', target: 'node7' },
        { source: 'node5', target: 'node8' },
        { source: 'node5', target: 'node9' },
      ]
    }
    };
  },
  mounted() { 
    
    this.graph = ForceGraph3D()(this.$refs.graphContainer)
                .linkWidth(3);
                // .nodeRelSize(options.nodeRelSize)
                // .nodeVal(options.nodeVal);
                // .nodeLabel('name');
 
    this.$nextTick(() => {
    const container = this.$refs.graphContainer;
    container.style.width = '10%';
    container.style.height = '50%';
    // container.style.margin = 'auto';
  });
},
  watch: {  
    
    A(newValue) {
      this.graph
      .nodeVal(node => getNodeSize(node)) // 设置节点大小
      .graphData(newValue)
      .nodeThreeObjectExtend(true)
      .nodeLabel('name')
      // .nodeLabel('id'); // 将节点的名字作为标签展示
      // .nodeLabel(node => node.name);
    }
  },
  methods: {
    
    selectFile() {
      this.$refs.fileInput.click(); // 点击按钮时，触发input的点击事件
    },
    onFileSelected(event) {
      const file = event.target.files[0]; // 获取选择的文件
      const reader = new FileReader(); // 创建FileReader对象
      reader.readAsText(file); // 读取文件内容
      reader.onload = () => {
        const data = JSON.parse(reader.result); // 将文件内容解析为JSON对象
        this.treeData = data; // 将解析后的JSON对象赋给变量
      };
    },
    setA(buttonValue) {
    if (buttonValue === 'orient') {
        this.A = this.treeData;
      } else if (buttonValue === 'loop') {
        const numNodes = 30; // 节点数量
        const radius = 100; // 半径
        this.A = generateCircularLayoutData(numNodes, radius); // 生成数据
        
        // const newdata = this.treeData;
        // const simulation = d3.forceSimulation(newdata.nodes)
        //   // .force("link", d3.forceLink(newdata.links).id(d => d.id))
        //   // .force("charge", d3.forceManyBody().strength(-300))
        //   .force("atlas", forceAtlas2Improve(newdata.nodes, newdata.links))
        //   // .force("center", d3.forceCenter());
        //   simulation.on("tick", () => {
        //   this.A = newdata;
        // });

      } else if (buttonValue === 'star') {
        const numNodes = 30; // 节点数量
        const radius = 100; // 半径
        this.A = generateStarLayoutData(numNodes, radius); // 生成数据
        
        // const width = this.$refs.graphContainer.clientWidth;
        // const height = this.$refs.graphContainer.clientHeight;

        // const newdata = this.treeData;
        // const simulation = d3force3d.forceSimulation(newdata.nodes)
        //   // .force("link", d3.forceLink(newdata.links).id(d => d.id))
        //   .force("charge", d3force3d.forceManyBody().strength(-300))
        //   .force("center", d3force3d.forceCenter())
        //   .force("atlas", forceAtlas2New(newdata.nodes, newdata.links, width, height));
        //   simulation.on("tick", () => {
        //   newdata.nodes.forEach((node, index) => {
        //     node.x = newdata.nodes[index].x;
        //     node.y = newdata.nodes[index].y;
        //     node.z = newdata.nodes[index].z;
        //   });
        //   this.A = newdata;
        // });

      } else if (buttonValue === 'fruchterman') {
      // } else if (buttonValue === 'forceatlas2') {
        const newdata = this.treeData;
        this.A = null;
        const simulation = d3force3d.forceSimulation(newdata.nodes)
          // .force("link", d3.forceLink(newdata.links).id(d => d.id))
          // .force("charge", d3.forceManyBody().strength(-300))
          .force("atlas", forceAtlas2(newdata.nodes, newdata.links))
          .force("center", d3force3d.forceCenter());
          
          simulation.on("tick", () => {
          newdata.nodes.forEach((node, index) => {
            node.x = newdata.nodes[index].x;
            node.y = newdata.nodes[index].y;
            node.z = newdata.nodes[index].z;
          });
          this.A = newdata;
        });
      } else if (buttonValue === 'test') {
      // } else if (buttonValue === 'fruchterman') {
        const newdata = this.treeData;
        const simulation = d3force3d.forceSimulation()
          .force('link', d3force3d.forceLink().id(d => d.id))
          .force('charge', d3force3d.forceManyBody().strength(-30))
          .force('center', d3force3d.forceCenter())
          .force('fruchterman', d3force3d.forceManyBody().strength(-30).distanceMin(20).distanceMax(200));

        simulation.nodes(newdata.nodes);
        simulation.force('link').links(newdata.links);

        simulation.on('tick', () => {
          this.graph.graphData(newdata);
          simulation.stop();
        });
      } else if (buttonValue === 'forceatlas2') {
      // } else if (buttonValue === 'test') {
        const rows = 6; // 行数
        const columns = 6; // 列数
        const distance = 50; // 节点间距
        this.A = generateNodeData(100, 5);
      }
    }
  }
}
</script>

<style>
.title {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100px;
}

.title h1 {
  font-size: 56px;
  text-align: center;
}

</style>