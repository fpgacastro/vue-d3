<template>
    <div class="grid-item">
      <div class="graph-container" ref="graphContainer"></div>
    </div>
  </template>
  
  <script>
  import ForceGraph3D from '3d-force-graph';
  
  export default {
    mounted() {
      const data = {
        nodes: [
          { id: 'node1' },
          { id: 'node2' },
          { id: 'node3' },
          { id: 'node4' },
          { id: 'node5' },
        ],
        links: [
          { source: 'node1', target: 'node2' },
          { source: 'node1', target: 'node3' },
          { source: 'node2', target: 'node4' },
          { source: 'node2', target: 'node5' },
        ],
      };
  
      const graph = ForceGraph3D()(
        this.$refs.graphContainer,
        {
          graphData: data,
          nodeAutoColorBy: 'id',
          linkWidth: 2,
          linkColor: '#999',
          onNodeClick: node => console.log(node),
        }
      );
      
      window.addEventListener('resize', () => graph.onResize());
    },
  };
  </script>
  
  <style scoped>
  .graph-container {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    grid-template-rows: repeat(3, 1fr);
    gap: 10px;
    width: 100%;
    height: 100%;
  }
  </style>
  