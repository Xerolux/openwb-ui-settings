<template>
  <openwb-base-card
    subtype="danger"
    :collapsible="true"
    :collapsed="true"
    class="pb-0"
  >
    <template #header>
      <font-awesome-icon
        fixed-width
        :icon="['fas', 'gauge-high']"
      />
      {{ counter.name }}
    </template>
    <template #actions>
      <div
        v-if="getFaultStateSubtype(baseTopic) == 'success'"
        class="text-right"
      >
        {{ formatNumberTopic(baseTopic + "/get/power", 3, 3, 0.001) }}&nbsp;kW
      </div>
      <span
        v-else
        :class="'subheader pill bg-' + getFaultStateSubtype(baseTopic)"
      >
        <div v-if="getFaultStateSubtype(baseTopic) == 'warning'">Warnung</div>
        <div v-else>Fehler</div>
      </span>
    </template>
    <openwb-base-card
      title="Aktuelle Werte"
      subtype="white"
      body-bg="white"
      class="py-1 mb-2"
    >
      <div class="row">
        <div class="col-6 text-right">Leistung</div>
        <div class="col-6 text-right">Netzfrequenz</div>
      </div>
      <div class="row">
        <div class="col text-right text-monospace pl-0">
          {{ formatNumberTopic(baseTopic + "/get/power", 3, 3, 0.001) + "&nbsp;kW" }}
        </div>
        <div class="col text-right text-monospace pl-0">
          {{ formatNumberTopic(baseTopic + "/get/frequency", 3) + "&nbsp;Hz" }}
        </div>
      </div>
    </openwb-base-card>
    <openwb-base-card
      title="Zählerstände"
      subtype="white"
      body-bg="white"
      class="py-1 mb-2"
    >
      <div class="row">
        <div class="col-6 text-right">Export</div>
        <div class="col-6 text-right">Import</div>
      </div>
      <div class="row">
        <div class="col text-right text-monospace pl-0">
          {{ formatNumberTopic(baseTopic + "/get/exported", 3, 3, 0.001) + "&nbsp;kWh" }}
        </div>
        <div class="col text-right text-monospace pl-0">
          {{ formatNumberTopic(baseTopic + "/get/imported", 3, 3, 0.001) + "&nbsp;kWh" }}
        </div>
      </div>
    </openwb-base-card>
    <openwb-base-card
      title="Werte pro Phase"
      subtype="white"
      body-bg="white"
      class="py-1 mb-2"
    >
      <div class="row">
        <div class="col-md-4 pr-0 text-center text-md-right">Spannung [V]</div>
        <div class="col">
          <div class="row">
            <div class="col text-right text-monospace pl-0">
              {{ formatPhaseArrayNumberTopic(baseTopic + "/get/voltages", 1).split(" / ")[0] }}
            </div>
            <div class="col text-right text-monospace pl-0">
              {{ formatPhaseArrayNumberTopic(baseTopic + "/get/voltages", 1).split(" / ")[1] }}
            </div>
            <div class="col text-right text-monospace pl-0">
              {{ formatPhaseArrayNumberTopic(baseTopic + "/get/voltages", 1).split(" / ")[2] }}
            </div>
          </div>
        </div>
      </div>
      <div class="row">
        <div class="col-md-4 pr-0 text-center text-md-right">Strom [A]</div>
        <div class="col">
          <div class="row">
            <div class="col text-right text-monospace pl-0">
              {{ formatPhaseArrayNumberTopic(baseTopic + "/get/currents", 2).split(" / ")[0] }}
            </div>
            <div class="col text-right text-monospace pl-0">
              {{ formatPhaseArrayNumberTopic(baseTopic + "/get/currents", 2).split(" / ")[1] }}
            </div>
            <div class="col text-right text-monospace pl-0">
              {{ formatPhaseArrayNumberTopic(baseTopic + "/get/currents", 2).split(" / ")[2] }}
            </div>
          </div>
        </div>
      </div>
      <div class="row">
        <div class="col-md-4 pr-0 text-center text-md-right">Wirkleistung [kW]</div>
        <div class="col">
          <div class="row">
            <div class="col text-right text-monospace pl-0">
              {{ formatPhaseArrayNumberTopic(baseTopic + "/get/powers", 3, 3, 0.001).split(" / ")[0] }}
            </div>
            <div class="col text-right text-monospace pl-0">
              {{ formatPhaseArrayNumberTopic(baseTopic + "/get/powers", 3, 3, 0.001).split(" / ")[1] }}
            </div>
            <div class="col text-right text-monospace pl-0">
              {{ formatPhaseArrayNumberTopic(baseTopic + "/get/powers", 3, 3, 0.001).split(" / ")[2] }}
            </div>
          </div>
        </div>
      </div>
      <div class="row">
        <div class="col-md-4 pr-0 text-center text-md-right">Leistungsfaktor</div>
        <div class="col">
          <div class="row">
            <div class="col text-right text-monospace pl-0">
              {{ formatPhaseArrayNumberTopic(baseTopic + "/get/power_factors", 2).split(" / ")[0] }}
            </div>
            <div class="col text-right text-monospace pl-0">
              {{ formatPhaseArrayNumberTopic(baseTopic + "/get/power_factors", 2).split(" / ")[1] }}
            </div>
            <div class="col text-right text-monospace pl-0">
              {{ formatPhaseArrayNumberTopic(baseTopic + "/get/power_factors", 2).split(" / ")[2] }}
            </div>
          </div>
        </div>
      </div>
    </openwb-base-card>
    <template #footer>
      <div class="container">
        <div class="row">
          <div class="col px-0">
            <openwb-base-alert :subtype="getFaultStateSubtype(baseTopic)">
              <font-awesome-icon
                fixed-width
                :icon="stateIcon"
              />
              Modulmeldung:
              <span style="white-space: pre-wrap">{{ $store.state.mqtt[baseTopic + "/get/fault_str"] }}</span>
            </openwb-base-alert>
          </div>
          <div class="col col-auto pr-0">
            <div class="text-right">ID: {{ counter.id }}</div>
          </div>
        </div>
      </div>
    </template>
  </openwb-base-card>
</template>

<script>
import ComponentState from "../mixins/ComponentState.vue";

import { library } from "@fortawesome/fontawesome-svg-core";
import {
  faCheckCircle as fasCheckCircle,
  faExclamationTriangle as fasExclamationTriangle,
  faTimesCircle as fasTimesCircle,
  faGaugeHigh as fasGaugeHigh,
} from "@fortawesome/free-solid-svg-icons";
import { FontAwesomeIcon } from "@fortawesome/vue-fontawesome";

library.add(fasCheckCircle, fasExclamationTriangle, fasTimesCircle, fasGaugeHigh);

export default {
  name: "CounterCard",
  components: {
    FontAwesomeIcon,
  },
  mixins: [ComponentState],
  props: {
    counter: { type: Object, required: true },
  },
  computed: {
    baseTopic: {
      get() {
        return "openWB/counter/" + this.counter.id;
      },
    },
  },
};
</script>
