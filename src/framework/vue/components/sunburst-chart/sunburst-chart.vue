<template>
  <div>
    <div class="chart-title">{{ chartTitle }}</div>
    <div ref="chart"></div>
  </div>
</template>

<script>
import * as UWT from "ui-widget-toolkit";

export default {
  name: "uwtSunburstChart",
  props: {
    chartTitle: {
      type: String,
      default: ""
    },
    chartDef: {
      type: Object,
      default: undefined
    },
    renderOptions: {
      type: Object,
      default: () => {
        return {};
      }
    },
    colorManager: {
      type: Object,
      default: () => {
        return new UWT.ColorManager();
      }
    },
    onRender: {
      type: Function,
      default: undefined
    }
  },
  mounted: function() {
    if (!this.renderer) {
      this.renderer = new UWT.D3Renderer("", this.colorManager);
      this.renderer.setDiv(this.$refs["chart"]);
      this.renderer.setOnRenderCallback(this.onRender);
    }
    if (this.renderer && this.chartDef) {
      this.renderer.invalidate(this.chartDef, this.renderOptions);
    }
  },
  watch: {
    chartDef: function(newValue, oldValue) {
      this.renderer.clearDiv(this.$refs["chart"]);
      if (this.renderer && this.chartDef) {
        if (oldValue) {
          this.renderer.destroy(oldValue);
        }
        this.renderer.setDiv(this.$refs["chart"]);
        this.renderer.invalidate(this.chartDef, this.renderOptions);
      }
    },
    renderOptions: function() {
      if (this.renderer && this.chartDef) {
        this.renderer.setDiv(this.$refs["chart"]);
        this.renderer.invalidate(this.chartDef, this.renderOptions);
      }
    },
    colorManager: function() {
      if (this.renderer) {
        this.renderer.setColorManager(this.colorManager);
      }
    }
  }
};
</script>

<style scoped>
.legendCells .cell .label {
  font-size: var(--uwt-legend-text-size, 12px);
  font-family: Arial;
}

.chart {
  margin: auto;
  width: inherit;
}

.no-pointer-events {
  pointer-events: none;
}
</style>