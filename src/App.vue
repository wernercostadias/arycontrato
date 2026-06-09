<script setup>
import confetti from 'canvas-confetti'
import { computed, onBeforeUnmount, onMounted, ref } from 'vue'

const modalOpen = ref(false)
const modalStage = ref('confirm')
const contractAccepted = ref(false)
const storageKey = 'ary-axolote-contract-accepted'
const checklistStorageKey = 'ary-axolote-contract-checklist'
const pageDropDistance = ref('100vh')

const obligationsBoyfriend = [
  'Enviar pelo menos 3 mensagens fofinhas durante o dia.',
  'Utilizar emojis de coracao sem reclamar.',
  'Defender Ary em qualquer discussao envolvendo qual personagem de anime e melhor.',
  'Responder "sim, amor" pelo menos uma vez, mesmo sem entender o contexto.'
]

const obligationsGirlfriend = [
  'Nao mutar Axolote sem motivo justo.',
  'Permitir pelo menos uma cantada ruim durante a vigencia do contrato.',
  'Fingir que as piadas dele sao engracadas em pelo menos 30% dos casos.',
  'Nao trocar Axolote por um Nitro presenteado por terceiros.'
]

const discordActivities = [
  'Ficar em call por mais de 30 minutos.',
  'Assistir algo juntos pelo compartilhamento de tela.',
  'Jogar qualquer coisa e culpar o lag pela derrota.',
  'Trocar memes duvidosos.',
  'Ficar em silencio na call e mesmo assim dizer que foi divertido.'
]
const checkedActivities = ref([])

const penalties = [
  'Perda temporaria do titulo de "amor".',
  'Aplicacao de apelidos vergonhosos.',
  'Envio obrigatorio de um meme de qualidade duvidosa.',
  'Obrigacao de ouvir um audio de 3 minutos explicando por que estava errado.'
]

const floatingHearts = ['💖', '💕', '💗', '💘', '✨', '🎮', '🌸', '💌']
const floatingDecorations = createFloatingDecorations()

const buttonLabel = computed(() =>
  contractAccepted.value ? '💞 Ver Contrato Assinado 💞' : '💖 Assinar Contrato 💖'
)

const modalContent = computed(() => {
  if (modalStage.value === 'accepted') {
    return {
      className: 'modal-card success-mode',
      kicker: '💖 Assinatura Confirmada',
      title: 'Agora ficou oficial, fofo e completamente canon.',
      text: 'O contrato foi assinado com sucesso e o namoro temporario de Dia dos Namorados acaba de ser ativado com muito amor, call, carinho e energia de casal perfeito 💕'
    }
  }

  if (modalStage.value === 'thinking') {
    return {
      className: 'modal-card sad-mode',
      kicker: '🥺 Resposta Recebida',
      title: 'O coracao do Axolote ficou tristinho.',
      text: 'Sem assinatura por enquanto... agora so restou olhar para a tela, abracar um travesseiro e esperar o universo romantico ter piedade.'
    }
  }

  return {
    className: 'modal-card',
    kicker: '💌 Confirmar Assinatura',
    title: 'Ary, voce aceita ser a namorada temporaria de Axolote por 24 horas?',
    text: 'Esse e um compromisso serio, fofo e totalmente valido no universo romantico gamer.'
  }
})

function openModal() {
  modalStage.value = contractAccepted.value ? 'accepted' : 'confirm'
  modalOpen.value = true
}

function closeModal() {
  modalOpen.value = false
}

function acceptContract() {
  contractAccepted.value = true
  modalStage.value = 'accepted'
  persistAcceptance()
  launchConfetti()
}

function thinkAboutIt() {
  modalStage.value = 'thinking'
}

function persistAcceptance() {
  if (typeof window === 'undefined') {
    return
  }

  window.localStorage.setItem(storageKey, 'accepted')
}

function toggleActivity(activity) {
  const alreadyChecked = checkedActivities.value.includes(activity)

  checkedActivities.value = alreadyChecked
    ? checkedActivities.value.filter((item) => item !== activity)
    : [...checkedActivities.value, activity]

  persistChecklist()
}

function persistChecklist() {
  if (typeof window === 'undefined') {
    return
  }

  window.localStorage.setItem(checklistStorageKey, JSON.stringify(checkedActivities.value))
}

onMounted(() => {
  if (typeof window === 'undefined') {
    return
  }

  contractAccepted.value = window.localStorage.getItem(storageKey) === 'accepted'
  checkedActivities.value = readChecklistFromStorage()
  updatePageDropDistance()
  window.addEventListener('resize', updatePageDropDistance)
})

onBeforeUnmount(() => {
  if (typeof window === 'undefined') {
    return
  }

  window.removeEventListener('resize', updatePageDropDistance)
})

function launchConfetti() {
  const defaults = {
    spread: 70,
    startVelocity: 30,
    ticks: 180,
    zIndex: 9999,
    colors: ['#FF6FAE', '#FFD6E7', '#DCC6FF', '#FFC857', '#FFFFFF']
  }

  confetti({
    ...defaults,
    particleCount: 90,
    origin: { x: 0.3, y: 0.35 }
  })

  confetti({
    ...defaults,
    particleCount: 110,
    origin: { x: 0.7, y: 0.35 }
  })
}

function createFloatingDecorations() {
  return Array.from({ length: 18 }, (_, index) => {
    const symbol = floatingHearts[index % floatingHearts.length]

    return {
      id: `${symbol}-${index}`,
      symbol,
      left: `${Math.random() * 100}%`,
      duration: `${16 + Math.random() * 10}s`,
      delay: `-${Math.random() * 18}s`,
      scale: 0.8 + Math.random() * 0.9,
      opacity: 0.4 + Math.random() * 0.35,
      drift: `${-30 + Math.random() * 60}px`
    }
  })
}

function updatePageDropDistance() {
  const documentHeight = Math.max(
    document.body.scrollHeight,
    document.documentElement.scrollHeight,
    document.body.offsetHeight,
    document.documentElement.offsetHeight,
    window.innerHeight
  )

  pageDropDistance.value = `${documentHeight + 160}px`
}

function readChecklistFromStorage() {
  if (typeof window === 'undefined') {
    return []
  }

  const storedValue = window.localStorage.getItem(checklistStorageKey)

  if (!storedValue) {
    return []
  }

  try {
    const parsedValue = JSON.parse(storedValue)
    return Array.isArray(parsedValue)
      ? parsedValue.filter((item) => discordActivities.includes(item))
      : []
  } catch {
    return []
  }
}
</script>

<template>
  <div class="page-shell">
    <div class="floating-decor" aria-hidden="true" :style="{ '--page-drop-distance': pageDropDistance }">
      <span
        v-for="item in floatingDecorations"
        :key="item.id"
        class="floating-item"
        :style="{
          left: item.left,
          animationDuration: item.duration,
          animationDelay: item.delay,
          opacity: item.opacity,
          '--item-scale': item.scale,
          '--drift-x': item.drift
        }"
      >
        {{ item.symbol }}
      </span>
    </div>

    <main class="contract-card page-reveal">
      <section class="hero section-reveal" style="--reveal-delay: 0.08s">
        <img
          class="hero-image"
          src="/diadosnamorados.png"
          alt="Ilustracao romantica de Dia dos Namorados"
        />
        <p class="eyebrow">Especial Dia dos Namorados 💕</p>
        <h1>Contrato Oficial de Namoro Temporario</h1>
        <div class="couple-highlight">
          <span class="couple-name">Axolote 🦎</span>
          <span class="couple-divider">e</span>
          <span class="couple-name">Ary 🌸</span>
        </div>
        <p class="duration-badge">Valido das 00:00 ate as 23:59 do Dia dos Namorados</p>
        <p class="intro">
          Um acordo romantico, divertido e oficialmente fofo para 24 horas de carinho,
          memes, call e energia de casal gamer.
        </p>
      </section>

      <section class="clause section-reveal" style="--reveal-delay: 0.16s">
        <h2>Clausula 1 - Do Objeto</h2>
        <p>
          O presente contrato tem como objetivo oficializar o namoro entre Axolote e Ary
          durante exatamente 24 horas, podendo ser encerrado automaticamente ao final do
          periodo estabelecido.
        </p>
      </section>

      <section class="clause section-reveal" style="--reveal-delay: 0.24s">
        <h2>Clausula 2 - Das Obrigacoes do Namorado</h2>
        <ul class="pretty-list">
          <li v-for="item in obligationsBoyfriend" :key="item">{{ item }}</li>
        </ul>
      </section>

      <section class="clause section-reveal" style="--reveal-delay: 0.32s">
        <h2>Clausula 3 - Das Obrigacoes da Namorada</h2>
        <ul class="pretty-list">
          <li v-for="item in obligationsGirlfriend" :key="item">{{ item }}</li>
        </ul>
      </section>

      <section class="clause activities section-reveal" style="--reveal-delay: 0.4s">
        <h2>Clausula 4 - Das Atividades Obrigatorias no Discord</h2>
        <div class="check-grid">
          <label v-for="item in discordActivities" :key="item" class="check-card">
            <input
              :checked="checkedActivities.includes(item)"
              type="checkbox"
              @change="toggleActivity(item)"
            />
            <span class="checkmark"></span>
            <span class="check-text">{{ item }}</span>
          </label>
        </div>
      </section>

      <section class="clause section-reveal" style="--reveal-delay: 0.48s">
        <h2>Clausula 5 - Das Penalidades</h2>
        <ul class="pretty-list">
          <li v-for="item in penalties" :key="item">{{ item }}</li>
        </ul>
      </section>

      <section class="clause final-clause section-reveal" style="--reveal-delay: 0.56s">
        <h2>Clausula Final</h2>
        <p>
          Ambas as partes declaram estar entrando neste relacionamento de livre e
          espontanea vontade, sem ameacas, sem chantagens e sem promessas de Nitro.
        </p>
      </section>

      <section class="signature-section section-reveal" style="--reveal-delay: 0.64s">
        <div class="signature-line">
          <span class="signer-name">Axolote 🦎</span>
          <span class="signature-block">
            <span class="fake-signature">Axolote</span>
            <span class="line"></span>
          </span>
        </div>
        <div class="signature-line">
          <span class="signer-name">Ary 🌸</span>
          <span class="signature-block">
            <span class="fake-signature alt">Ary</span>
            <span class="line"></span>
          </span>
        </div>
        <p class="date">Data: 12/06/2026</p>
      </section>

      <blockquote class="closing-phrase section-reveal" style="--reveal-delay: 0.72s">
        "Que o ping esteja baixo e o amor esteja alto."
      </blockquote>

      <div class="cta-panel section-reveal" style="--reveal-delay: 0.8s">
        <button class="primary-button" type="button" @click="openModal">
          {{ buttonLabel }}
        </button>
      </div>
    </main>

    <transition name="modal-fade">
      <div v-if="modalOpen" class="modal-overlay" @click.self="closeModal">
        <div :class="modalContent.className" role="dialog" aria-modal="true" aria-labelledby="modal-title">
          <button class="close-button" type="button" aria-label="Fechar modal" @click="closeModal">
            ×
          </button>
          <p class="modal-kicker">{{ modalContent.kicker }}</p>
          <h2 id="modal-title">{{ modalContent.title }}</h2>
          <p class="modal-message">{{ modalContent.text }}</p>
          <div v-if="modalStage === 'confirm'" class="modal-actions">
            <button class="primary-button" type="button" @click="acceptContract">Aceito 💖</button>
            <button class="secondary-button" type="button" @click="thinkAboutIt">
              Vou pensar 🤔
            </button>
          </div>
          <div v-else class="modal-actions">
            <button class="primary-button" type="button" @click="closeModal">Fechar</button>
          </div>
        </div>
      </div>
    </transition>
  </div>
</template>

<style>
:root {
  color-scheme: light;
  --pink-main: #ff6fae;
  --pink-soft: #ffd6e7;
  --lilac-soft: #dcc6ff;
  --lavender-light: #f3eeff;
  --background: #fff9fc;
  --text-main: #4a3b57;
  --gold: #ffc857;
  --white: #ffffff;
  --shadow: 0 24px 60px rgba(137, 100, 159, 0.18);
  --shadow-soft: 0 12px 30px rgba(255, 111, 174, 0.14);
  --border: rgba(255, 111, 174, 0.18);
  font-family: "Trebuchet MS", "Segoe UI", "Helvetica Neue", sans-serif;
  line-height: 1.5;
  font-weight: 400;
  color: var(--text-main);
  background:
    radial-gradient(circle at top left, rgba(255, 111, 174, 0.2), transparent 28%),
    radial-gradient(circle at top right, rgba(220, 198, 255, 0.45), transparent 26%),
    linear-gradient(180deg, #fff7fb 0%, #fff9fc 45%, #f9f5ff 100%);
  font-synthesis: none;
  text-rendering: optimizeLegibility;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

* {
  box-sizing: border-box;
}

body {
  margin: 0;
  min-width: 320px;
  min-height: 100vh;
}

button,
input {
  font: inherit;
}

#app {
  min-height: 100vh;
}

.page-shell {
  position: relative;
  min-height: 100vh;
  overflow: hidden;
  padding: 32px 18px 56px;
}

.floating-decor {
  pointer-events: none;
  position: absolute;
  inset: 0;
  z-index: 3;
  overflow: hidden;
}

.floating-item {
  position: absolute;
  top: -10%;
  font-size: clamp(1.1rem, 2vw, 1.8rem);
  animation: drift linear infinite;
  will-change: transform;
}

.contract-card {
  position: relative;
  z-index: 2;
  width: min(920px, 100%);
  margin: 0 auto;
  padding: 28px;
  border: 1px solid rgba(255, 255, 255, 0.75);
  border-radius: 30px;
  background:
    linear-gradient(160deg, rgba(255, 255, 255, 0.95), rgba(255, 248, 252, 0.92)),
    var(--white);
  box-shadow: var(--shadow);
  backdrop-filter: blur(8px);
}

.page-reveal {
  animation: pageReveal 0.9s ease-out both;
}

.section-reveal {
  opacity: 0;
  animation: sectionReveal 0.8s cubic-bezier(0.2, 0.8, 0.2, 1) forwards;
  animation-delay: var(--reveal-delay, 0s);
}

.hero {
  text-align: center;
  margin-bottom: 28px;
}

.hero-image {
  display: block;
  width: min(700px, calc(100% + 40px));
  height: auto;
  margin: -10px auto 10px;
  filter: drop-shadow(0 22px 34px rgba(190, 98, 140, 0.18));
}

.eyebrow,
.modal-kicker {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  padding: 7px 12px;
  border-radius: 999px;
  background: rgba(255, 111, 174, 0.12);
  color: var(--pink-main);
  font-weight: 700;
  letter-spacing: 0.02em;
  font-size: 0.95rem;
}

.hero h1,
.modal-card h2 {
  margin: 16px 0 12px;
  line-height: 1.1;
}

.hero h1 {
  font-size: clamp(1.95rem, 4vw, 3.25rem);
  max-width: 700px;
  margin-left: auto;
  margin-right: auto;
}

.intro {
  max-width: 620px;
  margin: 0 auto;
  font-size: 0.98rem;
  color: rgba(74, 59, 87, 0.82);
}

.couple-highlight {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 14px;
  flex-wrap: wrap;
  margin: 10px 0 14px;
}

.couple-name {
  font-size: clamp(1.7rem, 3.2vw, 2.35rem);
  font-weight: 800;
  color: var(--text-main);
}

.couple-divider {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  width: 42px;
  height: 42px;
  border-radius: 50%;
  background: linear-gradient(135deg, rgba(255, 111, 174, 0.18), rgba(220, 198, 255, 0.45));
  color: var(--pink-main);
  font-weight: 900;
  text-transform: uppercase;
}

.duration-badge {
  display: inline-flex;
  margin: 0 0 16px;
  padding: 9px 16px;
  border-radius: 999px;
  background: rgba(255, 214, 231, 0.62);
  color: rgba(74, 59, 87, 0.82);
  font-size: 0.9rem;
  font-weight: 700;
}

.clause,
.modal-card {
  border: 1px solid var(--border);
  box-shadow: var(--shadow-soft);
}

.clause {
  margin-bottom: 18px;
  padding: 22px;
  border-radius: 24px;
  background: rgba(255, 255, 255, 0.74);
}

.clause h2 {
  margin: 0 0 12px;
  font-size: 1.18rem;
}

.clause p,
.pretty-list,
.date,
.closing-phrase {
  margin: 0;
}

.pretty-list {
  padding-left: 0;
  list-style: none;
}

.pretty-list li {
  position: relative;
  padding-left: 30px;
}

.pretty-list li + li {
  margin-top: 10px;
}

.pretty-list li::before {
  content: '❤';
  position: absolute;
  left: 0;
  top: 0;
  color: var(--pink-main);
}

.check-grid {
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));
  gap: 14px;
}

.check-card {
  display: flex;
  gap: 12px;
  align-items: flex-start;
  padding: 16px;
  border-radius: 20px;
  background: linear-gradient(145deg, rgba(255, 214, 231, 0.45), rgba(220, 198, 255, 0.42));
  cursor: pointer;
}

.check-card input {
  position: absolute;
  opacity: 0;
  pointer-events: none;
}

.checkmark {
  flex: 0 0 24px;
  width: 24px;
  height: 24px;
  margin-top: 2px;
  border: 2px solid rgba(255, 111, 174, 0.6);
  border-radius: 8px;
  background: var(--white);
  transition: 0.2s ease;
  position: relative;
}

.check-card input:checked + .checkmark {
  background: linear-gradient(180deg, var(--pink-main), #ff93c0);
  border-color: transparent;
}

.check-card input:checked + .checkmark::after {
  content: '✓';
  position: absolute;
  inset: 0;
  display: grid;
  place-items: center;
  color: var(--white);
  font-weight: 700;
}

.check-text {
  flex: 1;
}

.signature-section {
  margin: 28px 0 18px;
  padding: 22px;
  border-radius: 24px;
  background: linear-gradient(180deg, rgba(243, 238, 255, 0.95), rgba(255, 255, 255, 0.92));
}

.signature-line {
  display: flex;
  align-items: center;
  gap: 14px;
}

.signer-name {
  min-width: 108px;
  font-weight: 700;
}

.signature-line + .signature-line {
  margin-top: 14px;
}

.signature-block {
  position: relative;
  flex: 1;
  min-height: 56px;
}

.fake-signature {
  position: absolute;
  left: 10px;
  bottom: 10px;
  z-index: 1;
  color: rgba(255, 111, 174, 0.88);
  font-family: "Brush Script MT", "Segoe Script", "Lucida Handwriting", cursive;
  font-size: clamp(1.8rem, 4vw, 2.4rem);
  line-height: 1;
  transform: rotate(-5deg);
  text-shadow: 0 6px 14px rgba(255, 111, 174, 0.14);
}

.fake-signature.alt {
  color: rgba(127, 95, 177, 0.9);
  transform: rotate(-3deg);
}

.line {
  display: block;
  border-bottom: 2px dashed rgba(74, 59, 87, 0.4);
  min-height: 54px;
}

.date {
  margin-top: 18px;
  font-weight: 700;
}

.closing-phrase {
  padding: 16px 18px;
  border-left: 5px solid var(--gold);
  border-radius: 18px;
  background: rgba(255, 200, 87, 0.12);
  font-style: italic;
}

.cta-panel {
  margin-top: 24px;
  text-align: center;
}

.primary-button,
.secondary-button,
.close-button {
  border: 0;
  cursor: pointer;
}

.primary-button,
.secondary-button {
  padding: 14px 22px;
  border-radius: 999px;
  font-weight: 700;
  transition:
    transform 0.2s ease,
    box-shadow 0.2s ease,
    filter 0.2s ease;
}

.primary-button {
  color: var(--white);
  background: linear-gradient(135deg, var(--pink-main), #ff8abc);
  box-shadow: 0 12px 24px rgba(255, 111, 174, 0.28);
}

.secondary-button {
  color: var(--text-main);
  background: linear-gradient(135deg, var(--lavender-light), var(--pink-soft));
}

.primary-button:hover,
.secondary-button:hover,
.close-button:hover {
  transform: translateY(-2px);
  filter: brightness(1.02);
}

.modal-overlay {
  position: fixed;
  inset: 0;
  z-index: 20;
  display: grid;
  place-items: center;
  padding: 18px;
  background: rgba(74, 59, 87, 0.35);
  backdrop-filter: blur(8px);
}

.modal-card {
  position: relative;
  width: min(520px, 100%);
  padding: 28px;
  border-radius: 28px;
  background: linear-gradient(180deg, rgba(255, 255, 255, 0.98), rgba(255, 245, 250, 0.96));
  text-align: center;
}

.success-mode {
  background:
    radial-gradient(circle at top center, rgba(255, 200, 87, 0.24), transparent 34%),
    linear-gradient(180deg, rgba(255, 255, 255, 0.98), rgba(255, 241, 248, 0.98));
}

.sad-mode {
  background:
    radial-gradient(circle at top center, rgba(220, 198, 255, 0.26), transparent 34%),
    linear-gradient(180deg, rgba(255, 255, 255, 0.98), rgba(245, 241, 255, 0.98));
}

.modal-message {
  margin: 0;
  color: rgba(74, 59, 87, 0.84);
  font-size: 1rem;
}

.modal-actions {
  display: flex;
  justify-content: center;
  gap: 12px;
  flex-wrap: wrap;
  margin-top: 22px;
}

.close-button {
  position: absolute;
  top: 12px;
  right: 12px;
  width: 40px;
  height: 40px;
  border-radius: 50%;
  background: rgba(255, 111, 174, 0.14);
  color: var(--text-main);
  font-size: 1.5rem;
  line-height: 1;
}

.modal-fade-enter-active,
.modal-fade-leave-active {
  transition: all 0.25s ease;
}

.modal-fade-enter-from,
.modal-fade-leave-to {
  opacity: 0;
}

.modal-fade-enter-from .modal-card,
.modal-fade-leave-to .modal-card {
  transform: scale(0.96) translateY(10px);
}

@keyframes drift {
  from {
    transform: translate3d(0, -120px, 0) rotate(0deg) scale(var(--item-scale, 1));
  }

  to {
    transform: translate3d(
      var(--drift-x, 0px),
      var(--page-drop-distance, 1400px),
      0
    ) rotate(18deg) scale(var(--item-scale, 1));
  }
}

@keyframes pageReveal {
  from {
    opacity: 0;
    transform: translateY(18px) scale(0.985);
  }

  to {
    opacity: 1;
    transform: translateY(0) scale(1);
  }
}

@keyframes sectionReveal {
  from {
    opacity: 0;
    transform: translateY(18px);
  }

  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@media (max-width: 760px) {
  .page-shell {
    padding: 22px 14px 40px;
  }

  .contract-card {
    padding: 20px;
    border-radius: 24px;
  }

  .check-grid {
    grid-template-columns: 1fr;
  }

  .clause,
  .signature-section,
  .duration-badge {
    padding: 18px;
  }

  .hero h1 {
    font-size: 2rem;
  }

  .hero-image {
    width: min(100%, 560px);
    margin-top: -4px;
  }

  .modal-card {
    padding: 22px 18px;
  }
}
</style>
