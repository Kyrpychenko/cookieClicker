<template>
  <h1>Cookie Clicker</h1>
  <h3 class="d-flex justify-content-center align-items-center">{{ Math.ceil(player.cookies) }} Cookies</h3>

  <div
    class="cookie"
    role="button"
    @click="
      biggerCookie = 'biggerCookie';
      cookieClick();
    "
    :class="biggerCookie"
  ></div>

  <div>
    <img src="/cookie.png" alt="golden cookie" width="75" class="goldenCookie" v-if="displayGoldenCookie" @click="frenzy" />
  </div>

  <div class="stats">
    <span class="d-flex justify-content-center h2">Stats</span>
    <h5 class="d-flex justify-content-center align-items-center text-decoration-underline">
      {{ frenzyActive ? player.clickValue * 7 : player.clickValue }} Cookies per Click
    </h5>
    <h5 class="d-flex justify-content-center align-items-center text-decoration-underline">{{ player.passiveClicks }} passive Cookies per Second</h5>
    <h5 class="d-flex justify-content-center align-items-center text-decoration-underline">{{ player.cookiesInTotal }} in total collected Cookies</h5>
  </div>
  <div class="upgrades">
    <span class="d-flex justify-content-center h2">Upgrades</span>
    <div class="d-flex justify-content-around text-decoration-underline" style="height: 60px" role="button" @click="clickUpgrade()">
      <img src="/cursor.png" alt="cursor" />
      <span class="d-flex justify-content-center align-items-center fs-4">Cursor</span>
      <span class="d-flex justify-content-center align-items-center fs-4">{{ clickUpgradeCost() }}</span>
    </div>
    <div class="d-flex justify-content-around text-decoration-underline" style="height: 60px" role="button" @click="grandmaUpgrade()">
      <img src="/grandma.jpg" alt="grandma" />
      <span class="d-flex justify-content-center align-items-center fs-4">Grandma</span>
      <span class="d-flex justify-content-center align-items-center fs-4">{{ grandmaUpgradeCost() }}</span>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';
const biggerCookie = ref('');
const displayGoldenCookie = ref(false);
const frenzyActive = ref(false);
const player = ref({
  cookiesInTotal: 0,
  cookies: 0,
  clickValue: 1,
  passiveClicks: 0,
});
const upgrades = ref({
  clickUpgradeLevel: 1,
  grandmaUpgradeLevel: 1,
});

const TICKS_PER_SECOND = 1;

setInterval(() => (passiveIncome(), spawnGoldenCookie()), 1000 / TICKS_PER_SECOND);

function passiveIncome() {
  getCookies(player.value.passiveClicks);
}

function clickUpgradeCost() {
  return 100 * (0.5 * upgrades.value.clickUpgradeLevel);
}

function grandmaUpgradeCost() {
  return 100 * upgrades.value.grandmaUpgradeLevel;
}

function getCookies(cookieSum: number) {
  if (frenzyActive.value) cookieSum *= 7;
  player.value.cookies += cookieSum;
  player.value.cookiesInTotal += cookieSum;
}
function cookieClick() {
  getCookies(player.value.clickValue);
  setTimeout(() => (biggerCookie.value = ''), 100);
}
function clickUpgrade() {
  if (player.value.cookies >= clickUpgradeCost()) {
    player.value.clickValue++;
    player.value.cookies -= clickUpgradeCost();
    upgrades.value.clickUpgradeLevel++;
  }
}

function grandmaUpgrade() {
  if (player.value.cookies >= grandmaUpgradeCost()) {
    player.value.passiveClicks++;
    player.value.cookies -= grandmaUpgradeCost();
    upgrades.value.grandmaUpgradeLevel++;
  }
}

function spawnGoldenCookie() {
  const random = Math.random() * 100;
  console.log(random);
  if (random <= 1) {
    displayGoldenCookie.value = true;
    setTimeout(() => (displayGoldenCookie.value = false), 10000);
  }
}

function frenzy() {
  displayGoldenCookie.value = false;
  frenzyActive.value = true;
  setTimeout(() => (frenzyActive.value = false), 7000);
}
</script>

<style scoped lang="scss">
h1,
h3 {
  display: flex;
  align-items: center;
  justify-content: center;
  color: white;
  position: relative;
  z-index: 1;
}
.cookie {
  background-image: url(/cookie.png);
  background-repeat: no-repeat;
  width: 360px;
  height: 360px;
  transition: 0.2s;
}

.biggerCookie {
  transform: scale(1.2);
  transition: 0.2s;
}

.goldenCookie {
  filter: hue-rotate(21deg) saturate(12);
  position: absolute;
}

.upgrades {
  height: 100vh;
  width: 50vh;
  background: repeating-linear-gradient(to right, #99582a, #99582a 30px, #5a2b09 30px, #5a2b09 60px);
  position: absolute;
  right: 0;
  top: 0;
}

.stats {
  height: 100vh;
  width: 50vh;
  position: absolute;
  left: 0;
  top: 0;
  background: repeating-linear-gradient(to right, #99582a, #99582a 30px, #5a2b09 30px, #5a2b09 60px);
}
</style>
