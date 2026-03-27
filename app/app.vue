<template>
  <div class="min-h-screen bg-gradient-to-b from-stone-950 via-neutral-900 to-stone-950 px-4 py-8 text-stone-100">
    <div class="mx-auto max-w-6xl">
      <div class="mb-6 text-center">
        <p class="text-[11px] uppercase tracking-[0.35em] text-amber-400/80">Cipher Machine</p>
        <h1 class="mt-2 text-3xl font-semibold tracking-[0.2em] text-stone-100">
          ENIGMA
        </h1>
        <p class="mt-3 text-sm text-stone-400">
          Choose rotors in settings. The drums below only change the visible start position.
        </p>
      </div>

      <div class="grid gap-6 lg:grid-cols-[1.2fr_0.8fr]">
        <!-- MACHINE -->
        <div class="overflow-hidden rounded-[2rem] border border-stone-700/70 bg-gradient-to-b from-stone-800 to-stone-950 shadow-[0_30px_80px_rgba(0,0,0,0.45)]">
          <div class="border-b border-stone-700/70 bg-gradient-to-r from-stone-800 via-stone-700 to-stone-800 px-6 py-4">
            <div class="flex flex-wrap items-center justify-between gap-3">
              <div>
                <p class="text-xs uppercase tracking-[0.35em] text-stone-400">Control Panel</p>
                <p class="mt-1 text-lg font-medium text-stone-100">Rotor Windows</p>
              </div>

              <div class="flex items-center gap-2 rounded-full border border-amber-500/30 bg-amber-500/10 px-3 py-1.5 text-xs text-amber-300">
                <span class="inline-block size-2 rounded-full bg-amber-400 shadow-[0_0_10px_rgba(251,191,36,0.9)]" />
                READY
              </div>
            </div>
          </div>

          <div class="space-y-6 p-5 md:p-6">
            <!-- ROTOR WINDOWS -->
            <div class="rounded-[1.75rem] border border-stone-700 bg-gradient-to-b from-stone-700/70 to-stone-900/80 p-4 shadow-inner">
              <div class="mb-4 flex items-center justify-between">
                <p class="text-xs uppercase tracking-[0.3em] text-stone-400">Rotor Windows</p>
                <p class="text-xs text-stone-500">Step only</p>
              </div>

              <div class="grid gap-4 md:grid-cols-3">
                <div
                    v-for="(rotorKey, index) in selectedRotors"
                    :key="`rotor-${index}`"
                    class="rounded-[1.5rem] border border-stone-600 bg-gradient-to-b from-stone-800 to-stone-950 p-4 shadow-[inset_0_1px_0_rgba(255,255,255,0.06)]"
                >
                  <div class="mb-3 flex items-center justify-between">
                    <div>
                      <p class="text-[11px] uppercase tracking-[0.3em] text-stone-500">
                        {{ rotorLabels[index] }}
                      </p>
                      <p class="mt-1 text-sm font-medium text-stone-200">
                        Rotor {{ rotorKey }}
                      </p>
                    </div>

                    <div class="rounded-full border border-stone-600 bg-stone-900 px-2.5 py-1 text-[11px] text-stone-300">
                      Notch {{ activeNotches[index] }}
                    </div>
                  </div>

                  <div class="relative mx-auto mb-4 flex h-36 w-24 items-center justify-center rounded-[1.5rem] border border-stone-600 bg-gradient-to-b from-stone-500 via-stone-700 to-stone-950 shadow-[inset_0_12px_24px_rgba(255,255,255,0.08),inset_0_-12px_24px_rgba(0,0,0,0.45),0_10px_30px_rgba(0,0,0,0.35)]">
                    <div class="absolute inset-y-3 left-1/2 w-[2px] -translate-x-1/2 bg-stone-900/40" />
                    <div class="absolute inset-x-2 top-2 h-3 rounded-full bg-white/10 blur-sm" />
                    <div class="absolute inset-x-2 bottom-2 h-3 rounded-full bg-black/30 blur-sm" />
                    <div class="absolute left-1/2 top-3 h-2 w-8 -translate-x-1/2 rounded-full bg-stone-900" />
                    <div class="absolute left-1/2 bottom-3 h-2 w-8 -translate-x-1/2 rounded-full bg-stone-900" />
                    <div class="absolute inset-y-0 left-0 w-4 rounded-l-[1.5rem] bg-gradient-to-r from-black/35 to-transparent" />
                    <div class="absolute inset-y-0 right-0 w-4 rounded-r-[1.5rem] bg-gradient-to-l from-black/35 to-transparent" />

                    <div class="relative z-10 flex h-20 w-16 flex-col items-center justify-center overflow-hidden rounded-2xl border border-stone-500 bg-gradient-to-b from-stone-950 via-black to-stone-950 shadow-[inset_0_8px_16px_rgba(255,255,255,0.04),inset_0_-8px_16px_rgba(0,0,0,0.6)]">
                      <div class="absolute inset-x-0 top-1/2 h-9 -translate-y-1/2 border-y border-amber-400/25 bg-amber-400/10" />
                      <span class="text-sm tracking-[0.3em] text-stone-500">
                        {{ prevLetter(index) }}
                      </span>
                      <span class="relative flex h-9 w-full items-center justify-center text-3xl font-semibold text-amber-300 drop-shadow-[0_0_12px_rgba(252,211,77,0.35)]">
                        {{ currentLetter(index) }}
                      </span>
                      <span class="text-sm tracking-[0.3em] text-stone-500">
                        {{ nextLetter(index) }}
                      </span>
                    </div>
                  </div>

                  <div class="grid grid-cols-2 gap-2">
                    <button
                        type="button"
                        class="rounded-xl border border-stone-600 bg-stone-800 px-3 py-2 text-sm text-stone-200 transition hover:border-amber-400/40 hover:bg-stone-700"
                        @click="cycleStart(index, -1)"
                    >
                      Step -
                    </button>

                    <button
                        type="button"
                        class="rounded-xl border border-amber-500/25 bg-amber-500/10 px-3 py-2 text-sm text-amber-200 transition hover:bg-amber-500/15"
                        @click="cycleStart(index, 1)"
                    >
                      Step +
                    </button>
                  </div>
                </div>
              </div>
            </div>

            <!-- SETTINGS -->
            <div class="grid gap-4 lg:grid-cols-2">
              <div class="rounded-[1.5rem] border border-stone-700 bg-stone-900/70 p-4">
                <p class="mb-3 text-xs uppercase tracking-[0.3em] text-stone-400">Rotor Order</p>

                <div class="grid grid-cols-3 gap-3">
                  <div v-for="(_, index) in 3" :key="`rotor-select-${index}`">
                    <label class="mb-1 block text-xs text-stone-500">{{ rotorLabels[index] }}</label>
                    <select
                        :value="selectedRotors[index]"
                        @change="setRotor(index, $event.target.value)"
                        class="w-full rounded-xl border border-stone-700 bg-stone-950 px-3 py-2.5 text-sm text-stone-100 outline-none transition focus:border-amber-400/50"
                    >
                      <option
                          v-for="key in availableRotorOptions(index)"
                          :key="`rotor-option-${index}-${key}`"
                          :value="key"
                      >
                        {{ key }}
                      </option>
                    </select>
                  </div>
                </div>

                <p class="mt-3 text-xs text-stone-500">
                  Each rotor can be used only once.
                </p>
              </div>

              <div class="rounded-[1.5rem] border border-stone-700 bg-stone-900/70 p-4">
                <p class="mb-3 text-xs uppercase tracking-[0.3em] text-stone-400">Ring Setting</p>

                <div class="grid grid-cols-3 gap-3">
                  <div v-for="(_, index) in 3" :key="`ring-${index}`">
                    <label class="mb-1 block text-xs text-stone-500">{{ rotorLabels[index] }}</label>
                    <select
                        v-model="ringLetters[index]"
                        class="w-full rounded-xl border border-stone-700 bg-stone-950 px-3 py-2.5 text-sm text-stone-100 outline-none transition focus:border-amber-400/50"
                    >
                      <option v-for="letter in letters" :key="`rg-${index}-${letter}`" :value="letter">
                        {{ letter }}
                      </option>
                    </select>
                  </div>
                </div>
              </div>
            </div>

            <div class="grid gap-4 lg:grid-cols-[1fr_auto]">
              <div class="rounded-[1.5rem] border border-stone-700 bg-stone-900/70 p-4">
                <label class="mb-2 block text-xs uppercase tracking-[0.3em] text-stone-400">
                  Reflector
                </label>

                <select
                    v-model="selectedReflector"
                    class="w-full rounded-xl border border-stone-700 bg-stone-950 px-3 py-2.5 text-sm text-stone-100 outline-none transition focus:border-amber-400/50"
                >
                  <option v-for="key in reflectorKeys" :key="key" :value="key">
                    {{ key }}
                  </option>
                </select>
              </div>

              <button
                  type="button"
                  @click="randomizeSettings"
                  class="rounded-[1.25rem] border border-amber-500/30 bg-gradient-to-b from-amber-400/15 to-amber-500/10 px-5 py-3 text-sm font-medium text-amber-200 transition hover:border-amber-400/50 hover:from-amber-400/20 hover:to-amber-500/15"
              >
                Randomize
              </button>
            </div>

            <!-- INPUT -->
            <div class="rounded-[1.75rem] border border-stone-700 bg-gradient-to-b from-stone-900 to-black p-4">
              <label class="mb-2 block text-xs uppercase tracking-[0.3em] text-stone-400">Plain Text</label>
              <input
                  v-model="input"
                  @keydown.enter="encode"
                  rows="5"
                  class="w-full resize-none rounded-[1.25rem] border border-stone-700 bg-stone-950/80 px-4 py-3 text-sm uppercase text-stone-100 outline-none transition placeholder:text-stone-500 focus:border-amber-400/50"
                  placeholder="TYPE YOUR MESSAGE..."
              />

              <div class="mt-4 flex flex-wrap gap-3">
                <button
                    type="button"
                    @click="encode"
                    class="rounded-[1.25rem] border border-amber-500/30 bg-gradient-to-b from-amber-300 to-amber-500 px-5 py-3 text-sm font-semibold text-stone-950 shadow-[0_10px_25px_rgba(245,158,11,0.3)] transition hover:translate-y-[-1px]"
                >
                  Encode
                </button>

                <button
                    type="button"
                    @click="clearAll"
                    class="rounded-[1.25rem] border border-stone-700 bg-stone-800 px-5 py-3 text-sm font-medium text-stone-200 transition hover:bg-stone-700"
                >
                  Clear
                </button>
              </div>
            </div>
          </div>
        </div>

        <!-- STATUS / OUTPUT -->
        <div class="space-y-6">
          <div class="rounded-[2rem] border border-stone-700/70 bg-gradient-to-b from-stone-800 to-stone-950 p-5 shadow-[0_25px_60px_rgba(0,0,0,0.35)]">
            <p class="text-xs uppercase tracking-[0.35em] text-stone-400">Status</p>

            <div class="mt-4 space-y-3">
              <div class="rounded-2xl border border-stone-700 bg-stone-900/80 p-3">
                <p class="text-[11px] uppercase tracking-[0.25em] text-stone-500">Rotors</p>
                <p class="mt-1 text-sm text-stone-100">{{ selectedRotors.join(' / ') }}</p>
              </div>

              <div class="rounded-2xl border border-stone-700 bg-stone-900/80 p-3">
                <p class="text-[11px] uppercase tracking-[0.25em] text-stone-500">Start Positions</p>
                <p class="mt-1 text-sm text-stone-100">{{ startLetters.join(' / ') }}</p>
              </div>

              <div class="rounded-2xl border border-stone-700 bg-stone-900/80 p-3">
                <p class="text-[11px] uppercase tracking-[0.25em] text-stone-500">Current Windows</p>
                <p class="mt-1 text-sm text-stone-100">{{ positionLetters }}</p>
              </div>

              <div class="rounded-2xl border border-stone-700 bg-stone-900/80 p-3">
                <p class="text-[11px] uppercase tracking-[0.25em] text-stone-500">Rings</p>
                <p class="mt-1 text-sm text-stone-100">{{ ringLetters.join(' / ') }}</p>
              </div>

              <div class="rounded-2xl border border-stone-700 bg-stone-900/80 p-3">
                <p class="text-[11px] uppercase tracking-[0.25em] text-stone-500">Reflector</p>
                <p class="mt-1 text-sm text-stone-100">{{ selectedReflector }}</p>
              </div>

              <div class="rounded-2xl border border-stone-700 bg-stone-900/80 p-3">
                <p class="text-[11px] uppercase tracking-[0.25em] text-stone-500">Notches</p>
                <p class="mt-1 text-sm text-stone-100">{{ activeNotches.join(' / ') }}</p>
              </div>
            </div>
          </div>

          <div class="rounded-[2rem] border border-stone-700/70 bg-gradient-to-b from-stone-800 to-stone-950 p-5 shadow-[0_25px_60px_rgba(0,0,0,0.35)]">
            <p class="text-xs uppercase tracking-[0.35em] text-stone-400">Output</p>

            <div class="mt-4 min-h-[220px] rounded-[1.5rem] border border-amber-500/15 bg-black/40 p-4 shadow-inner">
              <p class="text-sm leading-7 tracking-[0.35em] text-amber-200 break-words">
                {{ output || '—' }}
              </p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed, ref, watch } from 'vue'

const ABC = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'
const letters = ABC.split('')
const rotorLabels = ['Left', 'Center', 'Right']

const ROTOR_MAP = {
  I: {
    wiring: 'EKMFLGDQVZNTOWYHXUSPAIBRCJ',
    notch: 'Q'
  },
  II: {
    wiring: 'AJDKSIRUXBLHWTMCQGZNPYFVOE',
    notch: 'E'
  },
  III: {
    wiring: 'BDFHJLCPRTXVZNYEIWGAKMUSQO',
    notch: 'V'
  }
}

const REFLECTOR_MAP = {
  B: 'YRUHQSLDPXNGOKMIEBFZCWVJAT',
  C: 'FVPJIAOYEDRZXWGCTKUQSBNMHL'
}

const rotorKeys = Object.keys(ROTOR_MAP)
const reflectorKeys = Object.keys(REFLECTOR_MAP)

const selectedRotors = ref(['I', 'II', 'III'])
const startLetters = ref(['M', 'C', 'K'])
const ringLetters = ref(['A', 'A', 'A'])
const selectedReflector = ref('B')

const input = ref('')
const output = ref('')
const position = ref([])

const availableRotorOptions = (currentIndex) => {
  return rotorKeys.filter((key) => {
    return !selectedRotors.value.includes(key) || selectedRotors.value[currentIndex] === key
  })
}

const activeRotors = computed(() =>
    selectedRotors.value.map((key) => ROTOR_MAP[key].wiring)
)

const activeNotches = computed(() =>
    selectedRotors.value.map((key) => ROTOR_MAP[key].notch)
)

const activeReflector = computed(() =>
    REFLECTOR_MAP[selectedReflector.value]
)

const ringOffsets = computed(() =>
    ringLetters.value.map((letter) => ABC.indexOf(letter))
)

const positionLetters = computed(() =>
    position.value.map((i) => ABC[i]).join(' / ')
)

const resetPosition = () => {
  position.value = startLetters.value.map((letter) => ABC.indexOf(letter))
}

const currentLetter = (index) => {
  return ABC[position.value[index] ?? 0]
}

const prevLetter = (index) => {
  const currentIndex = position.value[index] ?? 0
  return ABC[(currentIndex - 1 + 26) % 26]
}

const nextLetter = (index) => {
  const currentIndex = position.value[index] ?? 0
  return ABC[(currentIndex + 1) % 26]
}

const cycleStart = (index, direction = 1) => {
  const currentIndex = ABC.indexOf(startLetters.value[index])
  const nextIndex = (currentIndex + direction + 26) % 26
  startLetters.value[index] = ABC[nextIndex]
}

const setRotor = (index, value) => {
  const next = [...selectedRotors.value]
  const existingIndex = next.findIndex((item, i) => item === value && i !== index)

  if (existingIndex !== -1) {
    ;[next[index], next[existingIndex]] = [next[existingIndex], next[index]]
  } else {
    next[index] = value
  }

  selectedRotors.value = next
}

const shuffleArray = (arr) => {
  const copy = [...arr]

  for (let i = copy.length - 1; i > 0; i -= 1) {
    const j = Math.floor(Math.random() * (i + 1))
    ;[copy[i], copy[j]] = [copy[j], copy[i]]
  }

  return copy
}

const randomLetter = () => {
  return letters[Math.floor(Math.random() * letters.length)]
}

const isAtNotch = (rotorIndex) => {
  return ABC[position.value[rotorIndex]] === activeNotches.value[rotorIndex]
}

const step = () => {
  const leftWillStep = isAtNotch(1)
  const middleWillStep = isAtNotch(2) || isAtNotch(1)

  position.value[2] = (position.value[2] + 1) % 26

  if (middleWillStep) {
    position.value[1] = (position.value[1] + 1) % 26
  }

  if (leftWillStep) {
    position.value[0] = (position.value[0] + 1) % 26
  }
}

const forward = (rotor, c, p, ring) => {
  const inputIndex = ABC.indexOf(c)
  const shiftedIndex = (inputIndex + p - ring + 26) % 26
  const wiredChar = rotor[shiftedIndex]
  const wiredIndex = ABC.indexOf(wiredChar)
  const outputIndex = (wiredIndex - p + ring + 26) % 26

  return ABC[outputIndex]
}

const backward = (rotor, c, p, ring) => {
  const inputIndex = ABC.indexOf(c)
  const shiftedIndex = (inputIndex + p - ring + 26) % 26
  const shiftedChar = ABC[shiftedIndex]
  const wiredIndex = rotor.indexOf(shiftedChar)
  const outputIndex = (wiredIndex - p + ring + 26) % 26

  return ABC[outputIndex]
}

const reflect = (c) => {
  return activeReflector.value[ABC.indexOf(c)]
}

const encodeChar = (c) => {
  let x = c

  x = forward(activeRotors.value[2], x, position.value[2], ringOffsets.value[2])
  x = forward(activeRotors.value[1], x, position.value[1], ringOffsets.value[1])
  x = forward(activeRotors.value[0], x, position.value[0], ringOffsets.value[0])

  x = reflect(x)

  x = backward(activeRotors.value[0], x, position.value[0], ringOffsets.value[0])
  x = backward(activeRotors.value[1], x, position.value[1], ringOffsets.value[1])
  x = backward(activeRotors.value[2], x, position.value[2], ringOffsets.value[2])

  return x
}

const encode = () => {
  output.value = ''
  resetPosition()

  for (const c of input.value.toUpperCase()) {
    if (!ABC.includes(c)) continue

    step()
    output.value += encodeChar(c)
  }
}

const clearAll = () => {
  input.value = ''
  output.value = ''
  resetPosition()
}

const randomizeSettings = () => {
  selectedRotors.value = shuffleArray(rotorKeys).slice(0, 3)
  startLetters.value = [randomLetter(), randomLetter(), randomLetter()]
  ringLetters.value = [randomLetter(), randomLetter(), randomLetter()]
  selectedReflector.value = reflectorKeys[Math.floor(Math.random() * reflectorKeys.length)]
  output.value = ''
  resetPosition()
}

watch([startLetters, ringLetters], () => {
  output.value = ''
  resetPosition()
}, { deep: true })

resetPosition()
</script>