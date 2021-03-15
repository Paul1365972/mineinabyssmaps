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
      let locs = results.filter(item => {
        return item.Name;
      }).map(item => {
        return { x: item.nx, y: item.ny, z: item.nz, name: item.Name, cons: item.Connections, layer: item.Layer };
      });
      let locMap = {};
      locs.forEach(item => {
        locMap[item.name] = item;
      });
      locs.forEach(loc => {
        this.data.push({
          x: [loc.x],
          y: [loc.z],
          z: [loc.y],
          text: loc.name,
          name: loc.name,
          mode: "markers+text",
          type: "scatter3d"
        });
        loc.cons.split(", ").forEach(name => {
          let con = locMap[name];
          if (!con) {
            console.log(loc)
            console.log(name)
            console.log(con)
            return;
          }
          this.data.push({
            x: [loc.x, con.x],
            y: [loc.z, con.z],
            z: [loc.y, con.y],
            mode: "line",
            type: "scatter3d"
          });
        })
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
        },
        showlegend: false,
        marker: {
          tick0: 0,
          dtick: 250
        },
        scene: {
          xaxis: {
            autotick: false,
            tick0: 0,
            dtick: 500,
          },
          yaxis: {
            autotick: false,
            tick0: 0,
            dtick: 500,
          },
          zaxis: {
            autotick: false,
            tick0: 0,
            dtick: 500,
          }
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