<template>

    <div class="number-page">
       <div>
           <h1>تبدیل اعداد به حروف <i>(آلمانی)</i></h1>
       </div>

        <div class="container">
            <input type="text" inputmode="number" placeholder="عدد رو وارد کنید" v-model="inputNumber"/>
        </div>
    </div>

    <div class="container">
            <p v-if="inputNumber" class="result">{{ germanWords }}</p>
    </div>


</template>

<script setup>

import {computed, ref} from "vue";

const inputNumber = ref(null);
const germanWords = computed(() => {
    return numberToGermanWords(inputNumber.value);
})

function numberToGermanWords(num) {
    const units = ["", "eins", "zwei", "drei", "vier", "fünf", "sechs", "sieben", "acht", "neun"];
    const teens = ["zehn", "elf", "zwölf", "dreizehn", "vierzehn", "fünfzehn", "sechzehn", "siebzehn", "achtzehn", "neunzehn"];
    const tens = ["", "", "zwanzig", "dreißig", "vierzig", "fünfzig", "sechzig", "siebzig", "achtzig", "neunzig"];
    const scales = ["", "tausend", "Millionen", "Milliarde", "Billionen", "Billiarde", "Trillionen", "Trilliarde"];

    if (num === 0) return "null";

    function getHundreds(n) {
        let word = "";
        let hundred = Math.floor(n / 100);
        let rest = n % 100;

        if (hundred > 0) {
            word += units[hundred] + "hundert";
        }

        if (rest > 0) {
            if (rest < 10) {
                word += units[rest];
            } else if (rest < 20) {
                word += teens[rest - 10];
            } else {
                let ten = Math.floor(rest / 10);
                let unit = rest % 10;
                if (unit > 0) {
                    word += units[unit] + "und" + tens[ten];
                } else {
                    word += tens[ten];
                }
            }
        }

        return word;
    }

    let words = "";
    let scaleIndex = 0;

    while (num > 0) {
        let chunk = num % 1000;
        if (chunk > 0) {
            let chunkWord = getHundreds(chunk);
            if (scaleIndex > 0) {
                if (scaleIndex === 1) {
                    if (chunk === 1) {
                        chunkWord = "eintausend";
                    } else {
                        chunkWord += scales[scaleIndex];
                    }
                } else {
                    chunkWord += " " + scales[scaleIndex];
                }
            }
            words = chunkWord + " " + words;
        }
        num = Math.floor(num / 1000);
        scaleIndex++;
    }

    // Handle "eins" at the beginning
    words = words.trim();
    if (words.startsWith("eins ")) {
        words = words.replace("eins ", "eine ");
    }

    return words.trim();
}

</script>


<style>
.dark-theme {
    background-color: #121212;
    color: #ffffff;
    font-family: Arial, sans-serif;
}

.number-page{
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    flex-wrap: wrap;
    padding: 50px 10px;
}

h1 {
    font-size: 1.5rem;
}

i {
    font-size: 1rem;
}

.container{
    display: block;
}

input {
    padding: 10px;
    margin-bottom: 20px;
    border: none;
    border-radius: 4px;
    font-size: 1.1em;
    background-color: #333;
    direction: rtl;
    color: #fff;
}

input::placeholder {
    color: #888;
}

p {
    font-size: 1rem;
    font-weight: 300;
    background: #151515;
    padding: 30px;
    border-radius: 10px;
}
</style>
