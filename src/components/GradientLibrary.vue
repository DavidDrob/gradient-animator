
<script setup>
</script>

<template>
  <div class="flex flex-wrap justify-items-start -m-8">
    <div class="w-64 h-64 m-8 rounded-xl" id="g-1"></div>
    <div class="w-64 h-64 m-8 rounded-xl" id="g-2"></div>
    <div class="w-64 h-64 m-8 rounded-xl" id="g-3"></div>
    <div class="w-64 h-64 m-8 rounded-xl" id="g-4"></div>
    <div class="w-64 h-64 m-8 rounded-xl" id="g-5"></div>
    <div class="w-64 h-64 m-8 rounded-xl" id="g-6"></div>
  </div>
</template>

<style scoped>
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

/* 2nd */
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

@property --16-x-position {
  syntax: "<percentage>";
  inherits: false;
  initial-value: 52.85156250000001%;
}

@property --16-y-position {
  syntax: "<percentage>";
  inherits: false;
  initial-value: 47.38281250000001%;
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