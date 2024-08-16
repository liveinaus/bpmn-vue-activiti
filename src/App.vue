<template>
  <div v-if="bpmnXml" class="app-containers">
    <Modeler :key="bpmnXml" :bpmn-xml="bpmnXml" @elementChanged="xmlChanged" />
    <Panel />
    <BpmnActions />
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import defaultBpmnXml from './bpmn/defaultBpmnXml';
import BpmnActions from './components/bpmn-actions';
import Modeler from './components/modeler';
import Panel from './components/panel';

export default defineComponent({
  name: 'App',
  components: {
    Modeler,
    Panel,
    BpmnActions,
  },
  data: () => ({
    bpmnXml: '',
  }),
  mounted() {
    if (!this.bpmnXml) {
      // set this.bpmnXml to the value from defaultBpmnXml.ts
      this.loadXml(defaultBpmnXml('default', 'Default'));
    }

    window.addEventListener('message', (event) => {
      const vscodeData = event.data;
      if (vscodeData.command === 'loadXml') {
        if (vscodeData?.data) {
          this.loadXml(vscodeData?.data);
        }
      }
    });
  },
  methods: {
    xmlChanged(xml: string) {
      this.updateXml(xml);
    },
    loadXml(xml: string) {
      this.bpmnXml = xml;
      console.log('loadXml', xml);
    },
    updateXml(xml: string) {
      if (vscode) {
        (vscode as any).postMessage({
          message: 'updateXml',
          xml: xml,
        });
      }
    },
  },
});
</script>

<style>
.centered-screen {
  width: 100vw;
  height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}

.text-larger {
  font-size: x-large;
  margin-bottom: 1rem;
}

.text-large {
  font-size: large;
  margin-bottom: 1rem;
}
</style>
