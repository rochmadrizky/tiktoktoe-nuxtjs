<template>
  <div class="max-w-3xl mx-auto">
    <div class="flex items-center justify-center">
      <div v-if="!mulaiSoal">
        <div class="flex my-4">
          <label for="nama">Nama:</label>
          <input
            v-model="nama"
            type="text"
            id="nama"
            class="border-2 rounded ml-2 px-2 py-1"
            placeholder="Isi nama disini yaa..."
          />
        </div>

        <div class="flex mb-4">
          <label for="kelas">Kelas:</label>
          <input
            v-model="kelas"
            type="number"
            id="kelas"
            :min="1"
            :max="12"
            class="border-2 rounded ml-2 px-2 py-1"
            placeholder="1"
          />
        </div>

        <div class="flex mb-4">
          <label>Jenis Kelamin:</label>

          <div class="ml-2">
            <input
              type="radio"
              id="laki-laki"
              v-model="jenisKelamin"
              value="laki-laki"
            />
            <label for="laki-laki" class="ml-1">Laki-laki</label>
          </div>

          <div class="ml-4">
            <input
              type="radio"
              id="perempuan"
              v-model="jenisKelamin"
              value="perempuan"
            />
            <label for="perempuan" class="ml-1">Perempuan</label>
          </div>
        </div>
        <button
          @click="kerjakanSoal"
          class="bg-blue-500 text-white px-4 py-2 rounded"
          :disabled="!inputValid"
        >
          Mulai
        </button>
      </div>
    </div>

    <div class="flex items-center justify-center">
      <div v-if="mulaiSoal">
        <h1 class="text-2xl font-bold mb-4">Ujian Online</h1>

        <div
          v-for="(question, key) in soalYangDilihat"
          :key="question.id"
          class="mb-4"
        >
          <p>{{ question.pertanyaan }}</p>
          <div v-for="answer in question.answers" :key="answer.content">
            <input
              type="radio"
              :name="key"
              :id="answer.content"
              :value="answer.content"
              v-model="question.jawabanYangDipilih"
              :disabled="tampilkanHasil"
            />
            <label :for="answer.content" class="ml-2">{{
              answer.content
            }}</label>
          </div>
        </div>

        <div class="mt-4">
          <button
            @click="kembaliPertanyaan"
            :disabled="indeksPertanyaan === 0"
            class="bg-gray-300 text-gray-600 px-4 py-2 rounded mr-2"
          >
            Sebelumnya
          </button>
          <button
            @click="nextPertanyaan"
            :disabled="indeksPertanyaan === soal.length - 1"
            class="bg-gray-300 text-gray-600 px-4 py-2 rounded"
          >
            Selanjutnya
          </button>
        </div>

        <button
          @click="kirimJawaban"
          class="bg-blue-500 text-white px-4 py-2 mt-4 rounded"
        >
          Submit
        </button>

        <div v-if="tampilkanHasil" class="mt-4">
          <h2 class="text-xl font-bold mb-2">Hasil Ujian</h2>
          <p>Nilai Anda: {{ skor }}</p>

          <div v-for="question in soal" :key="question.id" class="mb-4">
            <p
              :class="{
                'text-green-500':
                  question.jawabanYangDipilih === question.jawabanBenar,
                'text-red-500':
                  question.jawabanYangDipilih !== question.jawabanBenar,
              }"
            >
              Jawaban Anda: {{ question.jawabanYangDipilih }}
            </p>
            <p>Jawaban yang Benar: {{ question.jawabanBenar }}</p>
          </div>

          <p>Nama: {{ nama }}</p>
          <p>Kelas: {{ kelas }}</p>
          <p>Jenis Kelamin: {{ jenisKelamin }}</p>

          <button
            @click="resetSemua"
            class="bg-red-500 text-white px-4 py-2 rounded"
          >
            Keluar
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { soal } from "@/utils/soal";

const nama = ref("");
const kelas = ref("");
const jenisKelamin = ref("");
const mulaiSoal = ref(false);
const indeksPertanyaan = ref(0);

const soalYangDilihat = computed(() => {
  return [soal[indeksPertanyaan.value]];
});

const nextPertanyaan = () => {
  if (indeksPertanyaan.value < soal.length - 1) {
    indeksPertanyaan.value++;
  }
};

const kembaliPertanyaan = () => {
  if (indeksPertanyaan.value > 0) {
    indeksPertanyaan.value--;
  }
};

const tampilkanHasil = ref(false);

const kirimJawaban = () => {
  console.log("coba fungsi");
  tampilkanHasil.value = true;
};

const resetSemua = () => {
  mulaiSoal.value = false;
  tampilkanHasil.value = false;
  nama.value = "";
  kelas.value = "";
  jenisKelamin.value = "";
  soal.forEach((question) => {
    question.jawabanYangDipilih = null;
  });
};

const skor = computed(() => {
  const jawabanBenar = soal.filter(
    (question) => question.jawabanYangDipilih === question.jawabanBenar
  ).length;

  const totalSoal = soal.length;

  return Math.floor((100 / totalSoal) * jawabanBenar);
});

const kerjakanSoal = () => {
  mulaiSoal.value = true;
};

const inputValid = computed(() => {
  const namaValid = nama.value.trim() !== "";
  const isKelasValid =
    Number.isInteger(Number(kelas.value)) &&
    kelas.value >= 1 &&
    kelas.value <= 12;
  const isJenisKelaminValid = jenisKelamin.value !== "";
  return namaValid && isKelasValid && isJenisKelaminValid;
});
</script>
