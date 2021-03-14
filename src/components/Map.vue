<template>
  <div id="map">
    <Plotly id="plotlymap" :data="data" :layout="layout" :display-mode-bar="true"></Plotly>
  </div>
</template>

<script>
import { Plotly } from 'vue-plotly';
import GSheetReader from 'g-sheets-api';

export default {
  name: "Map",
  components: {
    Plotly
  },
  created() {
    const options = {
      sheetId: '1WjpjmloQ646TqG50efYajeAB1dxtBA-k2tOM5lFYHnY',
      sheetNumber: 1,
      returnAllResults: true,
    }
    GSheetReader(options, results => {
      let stops = results.filter(item => {
        return item.Name;
      }).map(item => {
        return { x: item.nx, y: item.ny, z: item.nz, name: item.Name, con: item.Connections, layer: item.Layer };
      });
      stops.forEach(item => {
        this.data.push({
          x: [item.x],
          y: [item.z],
          z: [item.y],
          text: item.name,
          mode: "markers+text",
          type: "scatter3d"
        });
      })
    });
  },
  data: function () {
    return {
      data:[],
      layout:{
        title: "Mine In Abyss Railway System Graph",
        autosize: true,
        margin: {
          t: 50,
          l: 50,
          r: 50,
          b: 50
        }
      }
    }
  }
}
</script>

<style scoped>

#map, #plotlymap {
  width:  100%;
  height: 100%;
  margin: 0;
}

</style>