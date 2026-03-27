<template>
  <div class="p-6 max-w-md mx-auto space-y-2">
    <ul>
      ROTORS:
      <li v-for="(item, index) in ROTORS" :key="index">
        {{ item }}
      </li>
    </ul>

    <p>REFLECTOR: {{ REFLECTOR }}</p>
    <p>NOTCHES: {{ NOTCHES.join(' / ') }}</p>
    <p>POSITION: {{ position.map(i => ABC[i]).join(' / ') }}</p>
    <p>INDEXES: {{ position }}</p>

    <input
        v-model="input"
        type="text"
        @keydown.enter="encode"
        class="w-full p-2 border rounded mb-3 uppercase"
        placeholder="TEXT"
    />

    <button @click="encode" class="px-4 py-2 bg-black text-white rounded">
      Encode
    </button>

    <div class="mt-3">{{ output }}</div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const ABC = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ'

const ROTORS = [
  'EKMFLGDQVZNTOWYHXUSPAIBRCJ',
  'AJDKSIRUXBLHWTMCQGZNPYFVOE',
  'BDFHJLCPRTXVZNYEIWGAKMUSQO'
]

const REFLECTOR = 'ABCDEFGDIJKGMKMIEBFTCVVJAT'

const NOTCHES = ['Q', 'E', 'V']

const input = ref('')
const output = ref('')
const position = ref([])

const resetPosition = () => {
  position.value = [
    ABC.indexOf('M'),
    ABC.indexOf('C'),
    ABC.indexOf('K')
  ]
}

const isAtNotch = (rotorIndex) => {
  const notchLetter = NOTCHES[rotorIndex] //v
  return ABC[position.value[rotorIndex]] === notchLetter //true false
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

const forward = (rotor, c, p) => {
  const i = (ABC.indexOf(c) + p) % 26
  const x = rotor[i]
  return ABC[(ABC.indexOf(x) - p + 26) % 26]
}

const backward = (rotor, c, p) => {
  const i = (ABC.indexOf(c) + p) % 26
  const x = ABC[i]
  const j = rotor.indexOf(x)
  return ABC[(j - p + 26) % 26]
}

const reflect = (c) => {
  const i = ABC.indexOf(c)
  const pair = REFLECTOR[i] //j

  const first = REFLECTOR.indexOf(pair) //9
  const last = REFLECTOR.lastIndexOf(pair) //23
  const reflectedIndex = i === first ? last : first

  return ABC[reflectedIndex]
}

const encodeChar = (c) => {
  let x = c

  x = forward(ROTORS[2], x, position.value[2])
  x = forward(ROTORS[1], x, position.value[1])
  x = forward(ROTORS[0], x, position.value[0])

  x = reflect(x)

  x = backward(ROTORS[0], x, position.value[0])
  x = backward(ROTORS[1], x, position.value[1])
  x = backward(ROTORS[2], x, position.value[2])

  return x
}

const encode = () => {
  output.value = ''
  resetPosition()

  for (const c of input.value.toUpperCase()) {
    if (!ABC.includes(c)) continue //valid a-z

    step()
    output.value += encodeChar(c)
  }
}

resetPosition()
</script>
