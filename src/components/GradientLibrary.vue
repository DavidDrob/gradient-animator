
<script setup>
import CSSModal from "./CSSModal.vue";

const g1Css =
  "background: radial-gradient(100% 100% at var(--g1-1-x-position) var(--g1-1-y-position),#3cc2dd 0%,transparent),radial-gradient(100% 100% at var(--g1-2-x-position) var(--g1-2-y-position),#93f179 0%,transparent),#33405b;";
const g1Properties = [
  '@property --g1-1-x-position { syntax: "<percentage>"; inherits: false; initial-value: 20%; }',
  '@property --g1-1-y-position { syntax: "<percentage>"; inherits: false; initial-value: 80%; }',
  '@property --g1-2-x-position { syntax: "<percentage>"; inherits: false; initial-value: 80%; }',
  '@property --g1-2-y-position { syntax: "<percentage>"; inherits: false; initial-value: 20%; }',
];
const g1Root = [
  { key: "--g1-1-x-position", value: "20%" },
  { key: "--g1-1-y-position", value: "80%" },
  { key: "--g1-2-x-position", value: "80%" },
  { key: "--g1-2-y-position", value: "20%" },
];
const g1Animation =
  "@keyframes g-1 { 25% { --g1-1-x-position: 15%; --g1-1-y-position: 15%; --g1-2-x-position: 85%; --g1-2-y-position: 80%; } 50% { --g1-1-x-position: 80%; --g1-1-y-position: 15%; --g1-2-x-position: 15%; --g1-2-y-position: 85%; } }";

const g2Css =
  "background: radial-gradient(100% 100% at 4% 3%, #ffffff 0%, transparent), radial-gradient( 100% 100% at var(--g2-1-x-position) var(--g2-1-y-position), #1d1ba7 -32%, transparent ), radial-gradient( 100% 100% at var(--g2-2-x-position) var(--g2-2-y-position), #235fd7 0%, transparent ), #ffffff;";
const g2Properties = [
  '@property --g2-1-x-position { syntax: "<percentage>"; inherits: false; initial-value: 87.96875%; } ',
  '@property --g2-1-y-position { syntax: "<percentage>"; inherits: false; initial-value: 91.1328125%; } ',
  '@property --g2-2-x-position { syntax: "<percentage>"; inherits: false; initial-value: 13.398437500000002%; }',
  '@property --g2-2-y-position { syntax: "<percentage>"; inherits: false; initial-value: 82.734375%; }',
];
const g2Root = [
  { key: "--g2-1-x-position", value: "87.96875%" },
  { key: "--g2-1-y-position", value: "91.1328125%" },
  { key: "--g2-2-x-position", value: "13.398437500000002%" },
  { key: "--g2-2-y-position", value: "82.734375%" },
];
const g2Animation =
  "@keyframes g-2 { 50% { --g2-1-x-position: 15%; --g2-1-y-position: 15%; --g2-2-x-position: 61.21093749999999%; --g2-2-y-position: 13.750000000000002%; } }";

const g3Css =
  "background: radial-gradient( 100% 100% at var(--g3-3-x-position) var(--g3-3-y-position), #d77423 -80%, transparent ), radial-gradient( 100% 100% at var(--g3-1-x-position) var(--g3-1-y-position), #7556f0 -71%, transparent ), radial-gradient( 100% 100% at var(--g3-2-x-position) var(--g3-2-y-position), #987af1 -52%, transparent ), #21213b;";
const g3Properties = [
  '@property --g3-1-x-position { syntax: "<percentage>"; inherits: false; initial-value: 16.09375%; }',
  '@property --g3-1-y-position { syntax: "<percentage>"; inherits: false; initial-value: 33.12500000000001%; } ',
  '@property --g3-2-x-position { syntax: "<percentage>"; inherits: false; initial-value: 48.55468750000001%; }',
  '@property --g3-2-y-position { syntax: "<percentage>"; inherits: false; initial-value: 9.648437500000002%; } ',
  '@property --g3-3-x-position { syntax: "<percentage>"; inherits: false; initial-value: 78.6328125%; } ',
  '@property --g3-3-y-position { syntax: "<percentage>"; inherits: false; initial-value: 79.8046875%; }',
];
const g3Root = [
  { key: "--g3-1-x-position", value: "16.09375%" },
  { key: "--g3-1-y-position", value: "33.12500000000001%" },
  { key: "--g3-2-x-position", value: "48.55468750000001%" },
  { key: "--g3-2-y-position", value: "9.648437500000002%" },
  { key: "--g3-3-x-position", value: "78.6328125%" },
  { key: "--g3-3-y-position", value: "79.8046875%" },
];
const g3Animation =
  "@keyframes g-3 { 50% { --g3-1-x-position: 42.69531250000001%; --g3-1-y-position: 91.5625%; --g3-2-x-position: 91.7578125%; --g3-2-y-position: 33.046875%; --g3-3-x-position: 5.742187500000001%; --g3-3-y-position: 5.546875000000001%; } }";

const g4Css =
  "background: radial-gradient( 100% 100% at var(--g4-1-x-position) var(--g4-1-y-position), #d7c823 -100%, transparent ), radial-gradient( 100% 100% at var(--g4-2-x-position) var(--g4-2-y-position), #e228c0 -100%, transparent ), radial-gradient( 100% 100% at var(--g4-3-x-position) var(--g4-3-y-position), #00ffe1 -100%, transparent ), radial-gradient( 100% 100% at var(--g4-4-x-position) var(--g4-4-y-position), #1481f5 -100%, transparent ), #ffffff;";
const g4Properties = [
  '@property --g4-1-x-position { syntax: "<percentage>"; inherits: false; initial-value: 87.23379629629622%; } ',
  '@property --g4-1-y-position { syntax: "<percentage>"; inherits: false; initial-value: 83.08008213552361%; } ',
  '@property --g4-2-x-position { syntax: "<percentage>"; inherits: false; initial-value: 18.900462962962923%; } ',
  '@property --g4-2-y-position { syntax: "<percentage>"; inherits: false; initial-value: 78.15195071868584%; } ',
  '@property --g4-3-x-position { syntax: "<percentage>"; inherits: false; initial-value: 88.62268518518512%; } ',
  '@property --g4-3-y-position { syntax: "<percentage>"; inherits: false; initial-value: 15.071868583162217%; } ',
  '@property --g4-4-x-position { syntax: "<percentage>"; inherits: false; initial-value: 11.712962962962967%; } ',
  '@property --g4-4-y-position { syntax: "<percentage>"; inherits: false; initial-value: 16.550308008213555%; }',
];
const g4Root = [
  { key: "--g4-1-x-position", value: "87.23379629629622%" },
  { key: "--g4-1-y-position", value: "83.08008213552361%" },
  { key: "--g4-2-x-position", value: "18.900462962962923%" },
  { key: "--g4-2-y-position", value: "78.15195071868584%" },
  { key: "--g4-3-x-position", value: "88.62268518518512%" },
  { key: "--g4-3-y-position", value: "15.071868583162217%" },
  { key: "--g4-4-x-position", value: "11.712962962962967%" },
  { key: "--g4-4-y-position", value: "16.550308008213555%" },
];
const g4Animation =
  "@keyframes g-4 { 25% { --g4-1-x-position: 88.11342592592592%; --g4-1-y-position: 17.74127310061603%; --g4-2-x-position: 86.99074074074056%; --g4-2-y-position: 80.29774127310063%; --g4-3-x-position: 11.157407407407389%; --g4-3-y-position: 18.80903490759754%; --g4-4-x-position: 15.543981481481403%; --g4-4-y-position: 78.31622176591377%; } 50% { --g4-1-x-position: 13.888888888888834%; --g4-1-y-position: 18.891170431211524%; --g4-2-x-position: 85.20833333333329%; --g4-2-y-position: 17.874743326488705%; --g4-3-x-position: 15%; --g4-3-y-position: 85%; --g4-4-x-position: 100%; --g4-4-y-position: 100%; } 75% { --g4-1-x-position: 17.071759259259224%; --g4-1-y-position: 75.56468172484597%; --g4-2-x-position: 13.136574074074%; --g4-2-y-position: 20.739219712525706%; --g4-3-x-position: 84.25925925925932%; --g4-3-y-position: 74.94866529774129%; --g4-4-x-position: 81.82870370370377%; --g4-4-y-position: 16.837782340862468%; } }";

const g5Css =
  "background: radial-gradient( 100% 100% at var(--g5-1-x-position) var(--g5-1-y-position), #fbff00 -52%, transparent ), radial-gradient( 100% 100% at var(--g5-2-x-position) var(--g5-2-y-position), #95e916 0%, transparent ), radial-gradient( 100% 100% at var(--g5-3-x-position) var(--g5-3-y-position), #28f0cf 0%, transparent ), #ffffff;";
const g5Properties = [
  '@property --g5-1-x-position { syntax: "<percentage>"; inherits: false; initial-value: 1.0937500000000024%; } ',
  '@property --g5-1-y-position { syntax: "<percentage>"; inherits: false; initial-value: 0%; } ',
  '@property --g5-2-x-position { syntax: "<percentage>"; inherits: false; initial-value: 15.312500000000002%; } ',
  '@property --g5-2-y-position { syntax: "<percentage>"; inherits: false; initial-value: 85.46875%; } ',
  '@property --g5-3-x-position { syntax: "<percentage>"; inherits: false; initial-value: 97.1875%; } ',
  '@property --g5-3-y-position { syntax: "<percentage>"; inherits: false; initial-value: 48.515625%; }',
];
const g5Root = [
  { key: "--g5-1-x-position", value: "1.0937500000000024%" },
  { key: "--g5-1-y-position", value: "0%" },
  { key: "--g5-2-x-position", value: "15.312500000000002%" },
  { key: "--g5-2-y-position", value: "85.46875%" },
  { key: "--g5-3-x-position", value: "97.1875%" },
  { key: "--g5-3-y-position", value: "48.515625%" },
];
const g5Animation =
  "@keyframes g-5 { 50% { --g5-1-x-position: 99.0234375%; --g5-1-y-position: 99.0234375%; --g5-2-x-position: 84.1796875%; --g5-2-y-position: 13.671875%; --g5-3-x-position: 2.8906249999999996%; --g5-3-y-position: 46.1328125%; } }";

const g6Css =
  "background: radial-gradient( 100% 100% at var(--g6-1-x-position) var(--g6-1-y-position), #fff700 -34%, transparent ), radial-gradient( 100% 100% at var(--g6-2-x-position) var(--g6-2-y-position), #f179e7 -28%, transparent ), radial-gradient(100% 100% at 52% 47%, #00fbff 13%, transparent), #ffffff;";
const g6Properties = [
  '@property --g6-1-x-position { syntax: "<percentage>"; inherits: false; initial-value: 89.7265625%; } ',
  '@property --g6-1-y-position { syntax: "<percentage>"; inherits: false; initial-value: 10.468750000000004%; }',
  '@property --g6-2-x-position { syntax: "<percentage>"; inherits: false; initial-value: 15.546875000000004%; }',
  '@property --g6-2-y-position { syntax: "<percentage>"; inherits: false; initial-value: 85.234375%; }',
];
const g6Root = [
  { key: "--g6-1-x-position", value: "89.7265625%" },
  { key: "--g6-1-y-position", value: "10.468750000000004%" },
  { key: "--g6-2-x-position", value: "15.546875000000004%" },
  { key: "--g6-2-y-position", value: "85.234375%" },
];
const g6Animation =
  "@keyframes g-6 { 50% { --g6-1-x-position: 16.718750000000004%; --g6-1-y-position: 83.1640625%; --g6-2-x-position: 86.2890625%; --g6-2-y-position: 14.882812499999998%; } }";
</script>

<template>
  <div class="flex flex-wrap justify-center md:justify-start -m-8">
    <div class="w-64 h-64 m-8 rounded-xl group" id="g-1">
      <div class="invisible group-hover:visible grid place-items-center h-full">
        <CSSModal
          :css-string="g1Css"
          :animations-enabled="true"
          animation-name="g-1"
          :properties="g1Properties"
          :root-variables="g1Root"
          :animation-css-string="g1Animation"
          animation-duration="3s"
          animation-easing="ease-in"
        />
      </div>
    </div>

    <div class="w-64 h-64 m-8 rounded-xl group" id="g-2">
      <div class="invisible group-hover:visible grid place-items-center h-full">
        <CSSModal
          :css-string="g2Css"
          :animations-enabled="true"
          animation-name="g-2"
          :properties="g2Properties"
          :root-variables="g2Root"
          :animation-css-string="g2Animation"
          animation-duration="10s"
          animation-easing="ease-in-out"
        />
      </div>
    </div>

    <div class="w-64 h-64 m-8 rounded-xl group" id="g-3">
      <div class="invisible group-hover:visible grid place-items-center h-full">
        <CSSModal
          :css-string="g3Css"
          :animations-enabled="true"
          animation-name="g-3"
          :properties="g3Properties"
          :root-variables="g3Root"
          :animation-css-string="g3Animation"
          animation-duration="6s"
          animation-easing="ease-in"
        />
      </div>
    </div>

    <div class="w-64 h-64 m-8 rounded-xl group" id="g-4">
      <div class="invisible group-hover:visible grid place-items-center h-full">
        <CSSModal
          :css-string="g4Css"
          :animations-enabled="true"
          animation-name="g-4"
          :properties="g4Properties"
          :root-variables="g4Root"
          :animation-css-string="g4Animation"
          animation-duration="7s"
          animation-easing="ease-in-out"
        />
      </div>
    </div>

    <div class="w-64 h-64 m-8 rounded-xl group" id="g-5">
      <div class="invisible group-hover:visible grid place-items-center h-full">
        <CSSModal
          :css-string="g5Css"
          :animations-enabled="true"
          animation-name="g-5"
          :properties="g5Properties"
          :root-variables="g5Root"
          :animation-css-string="g5Animation"
          animation-duration="6s"
          animation-easing="ease-in"
        />
      </div>
    </div>

    <div class="w-64 h-64 m-8 rounded-xl group" id="g-6">
      <div class="invisible group-hover:visible grid place-items-center h-full">
        <CSSModal
          :css-string="g6Css"
          :animations-enabled="true"
          animation-name="g-6"
          :properties="g6Properties"
          :root-variables="g6Root"
          :animation-css-string="g6Animation"
          animation-duration="4s"
          animation-easing="ease-in"
        />
      </div>
    </div>
  </div>
</template>

<style>
:root {
  --g1-1-x-position: 20%;
  --g1-1-y-position: 80%;
  --g1-2-x-position: 80%;
  --g1-2-y-position: 20%;

  --g2-1-x-position: 87.96875%;
  --g2-1-y-position: 91.1328125%;
  --g2-2-x-position: 13.398437500000002%;
  --g2-2-y-position: 82.734375%;

  --g3-1-x-position: 16.09375%;
  --g3-1-y-position: 33.12500000000001%;
  --g3-2-x-position: 48.55468750000001%;
  --g3-2-y-position: 9.648437500000002%;
  --g3-3-x-position: 78.6328125%;
  --g3-3-y-position: 79.8046875%;

  --g4-1-x-position: 87.23379629629622%;
  --g4-1-y-position: 83.08008213552361%;
  --g4-2-x-position: 18.900462962962923%;
  --g4-2-y-position: 78.15195071868584%;
  --g4-3-x-position: 88.62268518518512%;
  --g4-3-y-position: 15.071868583162217%;
  --g4-4-x-position: 11.712962962962967%;
  --g4-4-y-position: 16.550308008213555%;

  --g5-1-x-position: 1.0937500000000024%;
  --g5-1-y-position: 0%;
  --g5-2-x-position: 15.312500000000002%;
  --g5-2-y-position: 85.46875%;
  --g5-3-x-position: 97.1875%;
  --g5-3-y-position: 48.515625%;

  --g6-1-x-position: 89.7265625%;
  --g6-1-y-position: 10.468750000000004%;
  --g6-2-x-position: 15.546875000000004%;
  --g6-2-y-position: 85.234375%;
}

#g-1 {
  background: radial-gradient(
      100% 100% at var(--g1-1-x-position) var(--g1-1-y-position),
      #3cc2dd 0%,
      transparent
    ),
    radial-gradient(
      100% 100% at var(--g1-2-x-position) var(--g1-2-y-position),
      #93f179 0%,
      transparent
    ),
    #33405b;
  animation-name: g-1;
  animation-iteration-count: infinite;
  animation-duration: 3s;
  transition-timing-function: ease-in;
}

@property --g1-1-x-position {
  syntax: "<percentage>";
  inherits: false;
  initial-value: 20%;
}

@property --g1-1-y-position {
  syntax: "<percentage>";
  inherits: false;
  initial-value: 80%;
}

@property --g1-2-x-position {
  syntax: "<percentage>";
  inherits: false;
  initial-value: 80%;
}

@property --g1-2-y-position {
  syntax: "<percentage>";
  inherits: false;
  initial-value: 20%;
}

@keyframes g-1 {
  25% {
    --g1-1-x-position: 15%;
    --g1-1-y-position: 15%;
    --g1-2-x-position: 85%;
    --g1-2-y-position: 80%;
  }
  50% {
    --g1-1-x-position: 80%;
    --g1-1-y-position: 15%;
    --g1-2-x-position: 15%;
    --g1-2-y-position: 85%;
  }
}

#g-2 {
  background: radial-gradient(100% 100% at 4% 3%, #ffffff 0%, transparent),
    radial-gradient(
      100% 100% at var(--g2-1-x-position) var(--g2-1-y-position),
      #1d1ba7 -32%,
      transparent
    ),
    radial-gradient(
      100% 100% at var(--g2-2-x-position) var(--g2-2-y-position),
      #235fd7 0%,
      transparent
    ),
    #ffffff;
  animation-name: g-2;
  animation-iteration-count: infinite;
  animation-duration: 10s;
  transition-timing-function: ease-in-out;
}

@property --g2-1-x-position {
  syntax: "<percentage>";
  inherits: false;
  initial-value: 87.96875%;
}
@property --g2-1-y-position {
  syntax: "<percentage>";
  inherits: false;
  initial-value: 91.1328125%;
}
@property --g2-2-x-position {
  syntax: "<percentage>";
  inherits: false;
  initial-value: 13.398437500000002%;
}
@property --g2-2-y-position {
  syntax: "<percentage>";
  inherits: false;
  initial-value: 82.734375%;
}

@keyframes g-2 {
  50% {
    --g2-1-x-position: 15%;
    --g2-1-y-position: 15%;
    --g2-2-x-position: 61.21093749999999%;
    --g2-2-y-position: 13.750000000000002%;
  }
}

#g-3 {
  background: radial-gradient(
      100% 100% at var(--g3-3-x-position) var(--g3-3-y-position),
      #d77423 -80%,
      transparent
    ),
    radial-gradient(
      100% 100% at var(--g3-1-x-position) var(--g3-1-y-position),
      #7556f0 -71%,
      transparent
    ),
    radial-gradient(
      100% 100% at var(--g3-2-x-position) var(--g3-2-y-position),
      #987af1 -52%,
      transparent
    ),
    #21213b;
  animation-name: g-3;
  animation-iteration-count: infinite;
  animation-duration: 6s;
  transition-timing-function: ease-in;
}

@property --g3-1-x-position {
  syntax: "<percentage>";
  inherits: false;
  initial-value: 16.09375%;
}

@property --g3-1-y-position {
  syntax: "<percentage>";
  inherits: false;
  initial-value: 33.12500000000001%;
}

@property --g3-2-x-position {
  syntax: "<percentage>";
  inherits: false;
  initial-value: 48.55468750000001%;
}

@property --g3-2-y-position {
  syntax: "<percentage>";
  inherits: false;
  initial-value: 9.648437500000002%;
}

@property --g3-3-x-position {
  syntax: "<percentage>";
  inherits: false;
  initial-value: 78.6328125%;
}

@property --g3-3-y-position {
  syntax: "<percentage>";
  inherits: false;
  initial-value: 79.8046875%;
}

@keyframes g-3 {
  50% {
    --g3-1-x-position: 42.69531250000001%;
    --g3-1-y-position: 91.5625%;
    --g3-2-x-position: 91.7578125%;
    --g3-2-y-position: 33.046875%;
    --g3-3-x-position: 5.742187500000001%;
    --g3-3-y-position: 5.546875000000001%;
  }
}

#g-4 {
  background: radial-gradient(
      100% 100% at var(--g4-1-x-position) var(--g4-1-y-position),
      #d7c823 -100%,
      transparent
    ),
    radial-gradient(
      100% 100% at var(--g4-2-x-position) var(--g4-2-y-position),
      #e228c0 -100%,
      transparent
    ),
    radial-gradient(
      100% 100% at var(--g4-3-x-position) var(--g4-3-y-position),
      #00ffe1 -100%,
      transparent
    ),
    radial-gradient(
      100% 100% at var(--g4-4-x-position) var(--g4-4-y-position),
      #1481f5 -100%,
      transparent
    ),
    #ffffff;
  animation-name: g-4;
  animation-iteration-count: infinite;
  animation-duration: 7s;
  transition-timing-function: ease-in-out;
}

@property --g4-1-x-position {
  syntax: "<percentage>";
  inherits: false;
  initial-value: 87.23379629629622%;
}

@property --g4-1-y-position {
  syntax: "<percentage>";
  inherits: false;
  initial-value: 83.08008213552361%;
}

@property --g4-2-x-position {
  syntax: "<percentage>";
  inherits: false;
  initial-value: 18.900462962962923%;
}

@property --g4-2-y-position {
  syntax: "<percentage>";
  inherits: false;
  initial-value: 78.15195071868584%;
}

@property --g4-3-x-position {
  syntax: "<percentage>";
  inherits: false;
  initial-value: 88.62268518518512%;
}

@property --g4-3-y-position {
  syntax: "<percentage>";
  inherits: false;
  initial-value: 15.071868583162217%;
}

@property --g4-4-x-position {
  syntax: "<percentage>";
  inherits: false;
  initial-value: 11.712962962962967%;
}

@property --g4-4-y-position {
  syntax: "<percentage>";
  inherits: false;
  initial-value: 16.550308008213555%;
}

@keyframes g-4 {
  25% {
    --g4-1-x-position: 88.11342592592592%;
    --g4-1-y-position: 17.74127310061603%;
    --g4-2-x-position: 86.99074074074056%;
    --g4-2-y-position: 80.29774127310063%;
    --g4-3-x-position: 11.157407407407389%;
    --g4-3-y-position: 18.80903490759754%;
    --g4-4-x-position: 15.543981481481403%;
    --g4-4-y-position: 78.31622176591377%;
  }
  50% {
    --g4-1-x-position: 13.888888888888834%;
    --g4-1-y-position: 18.891170431211524%;
    --g4-2-x-position: 85.20833333333329%;
    --g4-2-y-position: 17.874743326488705%;
    --g4-3-x-position: 15%;
    --g4-3-y-position: 85%;
    --g4-4-x-position: 100%;
    --g4-4-y-position: 100%;
  }
  75% {
    --g4-1-x-position: 17.071759259259224%;
    --g4-1-y-position: 75.56468172484597%;
    --g4-2-x-position: 13.136574074074%;
    --g4-2-y-position: 20.739219712525706%;
    --g4-3-x-position: 84.25925925925932%;
    --g4-3-y-position: 74.94866529774129%;
    --g4-4-x-position: 81.82870370370377%;
    --g4-4-y-position: 16.837782340862468%;
  }
}

#g-5 {
  background: radial-gradient(
      100% 100% at var(--g5-1-x-position) var(--g5-1-y-position),
      #fbff00 -52%,
      transparent
    ),
    radial-gradient(
      100% 100% at var(--g5-2-x-position) var(--g5-2-y-position),
      #95e916 0%,
      transparent
    ),
    radial-gradient(
      100% 100% at var(--g5-3-x-position) var(--g5-3-y-position),
      #28f0cf 0%,
      transparent
    ),
    #ffffff;
  animation-name: g-5;
  animation-iteration-count: infinite;
  animation-duration: 6s;
  transition-timing-function: ease-in;
}

@property --g5-1-x-position {
  syntax: "<percentage>";
  inherits: false;
  initial-value: 1.0937500000000024%;
}

@property --g5-1-y-position {
  syntax: "<percentage>";
  inherits: false;
  initial-value: 0%;
}

@property --g5-2-x-position {
  syntax: "<percentage>";
  inherits: false;
  initial-value: 15.312500000000002%;
}

@property --g5-2-y-position {
  syntax: "<percentage>";
  inherits: false;
  initial-value: 85.46875%;
}

@property --g5-3-x-position {
  syntax: "<percentage>";
  inherits: false;
  initial-value: 97.1875%;
}

@property --g5-3-y-position {
  syntax: "<percentage>";
  inherits: false;
  initial-value: 48.515625%;
}

@keyframes g-5 {
  50% {
    --g5-1-x-position: 99.0234375%;
    --g5-1-y-position: 99.0234375%;
    --g5-2-x-position: 84.1796875%;
    --g5-2-y-position: 13.671875%;
    --g5-3-x-position: 2.8906249999999996%;
    --g5-3-y-position: 46.1328125%;
  }
}

#g-6 {
  background: radial-gradient(
      100% 100% at var(--g6-1-x-position) var(--g6-1-y-position),
      #fff700 -34%,
      transparent
    ),
    radial-gradient(
      100% 100% at var(--g6-2-x-position) var(--g6-2-y-position),
      #f179e7 -28%,
      transparent
    ),
    radial-gradient(100% 100% at 52% 47%, #00fbff 13%, transparent), #ffffff;
  animation-name: g-6;
  animation-iteration-count: infinite;
  animation-duration: 4s;
  transition-timing-function: ease-in;
}

@property --g6-1-x-position {
  syntax: "<percentage>";
  inherits: false;
  initial-value: 89.7265625%;
}

@property --g6-1-y-position {
  syntax: "<percentage>";
  inherits: false;
  initial-value: 10.468750000000004%;
}

@property --g6-2-x-position {
  syntax: "<percentage>";
  inherits: false;
  initial-value: 15.546875000000004%;
}

@property --g6-2-y-position {
  syntax: "<percentage>";
  inherits: false;
  initial-value: 85.234375%;
}

@keyframes g-6 {
  50% {
    --g6-1-x-position: 16.718750000000004%;
    --g6-1-y-position: 83.1640625%;
    --g6-2-x-position: 86.2890625%;
    --g6-2-y-position: 14.882812499999998%;
  }
}
</style>