<template>
  <openwb-base-card
    subtype="success"
    :collapsible="true"
    :collapsed="true"
    class="pb-0"
  >
    <template #header>
      <font-awesome-icon
        fixed-width
        :icon="['fas', 'solar-panel']"
      />
      {{ inverter.name }}
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
        <div class="col text-right">Leistung</div>
        <div class="col text-right">Zählerstand</div>
      </div>
      <div class="row">
        <div class="col text-right text-monospace">
          {{ formatNumberTopic(baseTopic + "/get/power", 3, 3, 0.001) }}&nbsp;kW
        </div>
        <div class="col text-right text-monospace">
          {{ formatNumberTopic(baseTopic + "/get/exported", 3, 3, 0.001) }}&nbsp;kWh
        </div>
      </div>
    </openwb-base-card>
    <openwb-base-card
      title="Erträge"
      subtype="white"
      body-bg="white"
      class="py-1 mb-2"
    >
      <div class="row">
        <div class="col text-right">Heute</div>
        <div class="col text-right">Monat</div>
        <div class="col text-right">Jahr</div>
      </div>
      <div class="row">
        <div class="col text-right text-monospace">
          {{ formatNumberTopic(baseTopic + "/get/daily_exported", 3, 3, 0.001) }}&nbsp;kWh
        </div>
        <div class="col text-right text-monospace">
          {{ formatNumberTopic(baseTopic + "/get/monthly_exported", 1, 1, 0.001) }}&nbsp;kWh
        </div>
        <div class="col text-right text-monospace">
          {{ formatNumberTopic(baseTopic + "/get/yearly_exported", 0, 0, 0.001) }}&nbsp;kWh
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
            <div class="text-right">ID: {{ inverter.id }}</div>
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
  faSolarPanel as fasSolarPanel,
} from "@fortawesome/free-solid-svg-icons";
import { FontAwesomeIcon } from "@fortawesome/vue-fontawesome";

library.add(fasCheckCircle, fasExclamationTriangle, fasTimesCircle, fasSolarPanel);

export default {
  name: "InverterCard",
  components: {
    FontAwesomeIcon,
  },
  mixins: [ComponentState],
  props: {
    inverter: { type: Object, required: true },
  },
  computed: {
    baseTopic: {
      get() {
        return "openWB/pv/" + this.inverter.id;
      },
    },
  },
};
</script>
