<template>
  <div class="p-6 max-w-md mx-auto space-y-4">
    <div class="space-y-2">
      <p class="font-semibold">SETTINGS</p>

      <div class="grid grid-cols-3 gap-2">
        <div>
          <label class="block text-sm mb-1">Left Rotor</label>
          <select v-model="selectedRotors[0]" class="w-full p-2 border rounded">
            <option v-for="key in rotorKeys" :key="`left-${key}`" :value="key">
              {{ key }}
            </option>
          </select>
        </div>

        <div>
          <label class="block text-sm mb-1">Center Rotor</label>
          <select v-model="selectedRotors[1]" class="w-full p-2 border rounded">
            <option v-for="key in rotorKeys" :key="`center-${key}`" :value="key">
              {{ key }}
            </option>
          </select>
        </div>

        <div>
          <label class="block text-sm mb-1">Right Rotor</label>
          <select v-model="selectedRotors[2]" class="w-full p-2 border rounded">
            <option v-for="key in rotorKeys" :key="`right-${key}`" :value="key">
              {{ key }}
            </option>
          </select>
        </div>
      </div>

      <div class="grid grid-cols-3 gap-2">
        <div>
          <label class="block text-sm mb-1">Left Position</label>
          <select v-model="startLetters[0]" class="w-full p-2 border rounded">
            <option v-for="letter in letters" :key="`lp-${letter}`" :value="letter">
              {{ letter }}
            </option>
          </select>
        </div>

        <div>
          <label class="block text-sm mb-1">Center Position</label>
          <select v-model="startLetters[1]" class="w-full p-2 border rounded">
            <option v-for="letter in letters" :key="`cp-${letter}`" :value="letter">
              {{ letter }}
            </option>
          </select>
        </div>

        <div>
          <label class="block text-sm mb-1">Right Position</label>
          <select v-model="startLetters[2]" class="w-full p-2 border rounded">
            <option v-for="letter in letters" :key="`rp-${letter}`" :value="letter">
              {{ letter }}
            </option>
          </select>
        </div>
      </div>

      <div class="grid grid-cols-3 gap-2">
        <div>
          <label class="block text-sm mb-1">Left Ring</label>
          <select v-model="ringLetters[0]" class="w-full p-2 border rounded">
            <option v-for="letter in letters" :key="`lr-${letter}`" :value="letter">
              {{ letter }}
            </option>
          </select>
        </div>

        <div>
          <label class="block text-sm mb-1">Center Ring</label>
          <select v-model="ringLetters[1]" class="w-full p-2 border rounded">
            <option v-for="letter in letters" :key="`cr-${letter}`" :value="letter">
              {{ letter }}
            </option>
          </select>
        </div>

        <div>
          <label class="block text-sm mb-1">Right Ring</label>
          <select v-model="ringLetters[2]" class="w-full p-2 border rounded">
            <option v-for="letter in letters" :key="`rr-${letter}`" :value="letter">
              {{ letter }}
            </option>
          </select>
        </div>
      </div>

      <div>
        <label class="block text-sm mb-1">Reflector</label>
        <select v-model="selectedReflector" class="w-full p-2 border rounded">
          <option v-for="key in reflectorKeys" :key="key" :value="key">
            {{ key }}
          </option>
        </select>
      </div>
    </div>

    <div class="space-y-1 text-sm">
      <p>ROTORS: {{ selectedRotors.join(' / ') }}</p>
      <p>POSITIONS: {{ positionLetters }}</p>
      <p>RINGS: {{ ringLetters.join(' / ') }}</p>
      <p>REFLECTOR: {{ selectedReflector }}</p>
      <p>NOTCHES: {{ activeNotches.join(' / ') }}</p>
      <p>INDEXES: {{ position }}</p>
    </div>

    <input
        v-model="input"
        type="text"
        @keydown.enter="encode"
        class="w-full p-2 border rounded uppercase"
        placeholder="TEXT"
    />

    <button @click="encode" class="px-4 py-2 bg-black text-white rounded">
      Encode
    </button>

    <div>{{ output }}</div>
  </div>
</template>

<script setup>
import { computed, ref, watch } from 'vue'

const ABC = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'
const letters = ABC.split('')

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

const activeRotors = computed(() =>
    selectedRotors.value.map(key => ROTOR_MAP[key].wiring)
)

const activeNotches = computed(() =>
    selectedRotors.value.map(key => ROTOR_MAP[key].notch)
)

const activeReflector = computed(() =>
    REFLECTOR_MAP[selectedReflector.value]
)

const ringOffsets = computed(() =>
    ringLetters.value.map(letter => ABC.indexOf(letter))
)

const positionLetters = computed(() =>
    position.value.map(i => ABC[i]).join(' / ')
)

const resetPosition = () => {
  position.value = startLetters.value.map(letter => ABC.indexOf(letter))
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

watch([startLetters, selectedRotors, ringLetters], resetPosition, { deep: true })

resetPosition()
</script>