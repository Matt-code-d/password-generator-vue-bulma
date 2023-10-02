<template>
  <div class="container">
    <div class="section">
      <div v-if="copySuccess" class="notification is-success">
        Password Copiata!
      </div>
      
      <div class="columns">
        <div class="column is-two-thirds">
          <div class="box">
            <label for="password-length" class="label">Password:</label>
            <div @click="copyToClipboard" v-if="generatedPassword" class="box password-box has-background-black">
              {{ generatedPassword }}
            </div>
            <div v-if="generatedPassword" class="password-strength">
              <p class="has-text-centered"><strong>LIVELLO SICUREZZA:</strong></p>
              <progress class="progress" :class="passwordStrengthColor" :value="passwordStrengthPercentage" max="100"></progress>
              <p class="has-text-centered is-size-5"><strong>{{ passwordStrengthCategory }}</strong></p>
            </div>
            <button class="button is-primary is-fullwidth mt-4" @click="generatePassword">
              <span class="icon">
                <i class="fas fa-redo"></i>
              </span>
              <span><strong>RIGENERA</strong></span>
            </button>
          </div>
        </div>
        <div class="column">
          <div class="box">
              <div class="field mt-4">
                <label for="password-length" class="label">Lunghezza:</label>
                <div class="control">
                  <input
                    id="sliderWithValue"
                    class="slider has-output is-fullwidth"
                    step="1"
                    min="4"
                    max="64"
                    v-model="passwordLength"
                    type="range"
                  />
                  <output for="sliderWithValue">{{ passwordLength }}</output>
                </div>
            </div>
            <div class="columns">
  <div class="column is-half"><input
                    id="includeUppercase"
                    type="checkbox"
                    v-model="includeUppercase"
                    class="switch is-rounded is-outlined is-primary"
                  />
                  <label for="includeUppercase">Maiuscole</label></div>
  <div class="column"><input
                    id="includeLowercase"
                    type="checkbox"
                    v-model="includeLowercase"
                    class="switch is-rounded is-outlined is-primary"
                  />
                  <label for="includeLowercase">Minuscole</label></div>
</div>
<div class="columns">
  <div class="column is-half"> <input
                    id="includeSpecial"
                    type="checkbox"
                    v-model="includeSpecial"
                    class="switch is-rounded is-outlined is-primary"
                  />
                  <label for="includeSpecial">Simboli</label></div>
  <div class="column"> <input
                    id="includeNumbers"
                    type="checkbox"
                    v-model="includeNumbers"
                    class="switch is-rounded is-outlined is-primary"
                  />
                  <label for="includeNumbers">Numeri</label></div>
</div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      passwordLength: 4,
      includeUppercase: true,
      includeLowercase: true,
      includeSpecial: true,
      includeNumbers: true,
      generatedPassword: "",
      passwordStrength: 0,
      copySuccess: false,
    };
  },
  computed: {
    passwordStrengthPercentage() {
      return Math.min(100, this.passwordStrength);
    },
    passwordStrengthCategory() {
      const percentage = this.passwordStrengthPercentage;
      if (percentage <= 25) {
        return "Debole";
      } else if (percentage <= 50) {
        return "Media";
      } else if (percentage <= 75) {
        return "Forte";
      } else {
        return "Massima";
      }
    },
    passwordStrengthColor() {
      const percentage = this.passwordStrengthPercentage;
      if (percentage <= 25) {
        return "is-danger";
      } else if (percentage <= 50) {
        return "is-warning";
      } else if (percentage <= 75) {
        return "is-info";
      } else {
        return "is-primary";
      }
    },
  },
  watch: {
    passwordLength: "generatePassword",
    includeUppercase: "generatePassword",
    includeLowercase: "generatePassword",
    includeSpecial: "generatePassword",
    includeNumbers: "generatePassword",
  },
  methods: {
    calculatePasswordStrength() {
      let score = 0;

      // Lunghezza della password
      const minLength = 8;
      const maxLength = 16;
      if (this.passwordLength < minLength) {
        score += (this.passwordLength / minLength) * 15; // Riduci il punteggio di base per password più corte
      } else if (this.passwordLength <= maxLength) {
        score += 15 + ((this.passwordLength - minLength) / (maxLength - minLength)) * 85;
      } else {
        score += 100;
      }

      // Diversità dei caratteri
      const categories = [
        this.includeUppercase,
        this.includeLowercase,
        this.includeSpecial,
        this.includeNumbers,
      ];
      const numCategories = categories.filter((category) => category).length;
      const diversityScore = (numCategories / categories.length) * 60; // Aumenta il punteggio per la diversità dei caratteri
      score += diversityScore;

      // Complessità della password (aggiungi criteri personalizzati qui)
      const hasUppercase = /[A-Z]/.test(this.generatedPassword);
      const hasLowercase = /[a-z]/.test(this.generatedPassword);
      const hasSpecial = /[!@#$%^&*()_+[\]{}|;:,.<>?]/.test(this.generatedPassword);
      const hasNumbers = /\d/.test(this.generatedPassword);

      if (hasUppercase && hasLowercase && hasSpecial && hasNumbers) {
        score += 50;
      }

      // Riduci il punteggio massimo possibile per password corte
      if (this.passwordLength < 12) {
        score = score * (this.passwordLength / 12);
      }

      this.passwordStrength = Math.min(100, score);
    },

    generatePassword() {
      if (!this.includeUppercase && !this.includeLowercase && !this.includeSpecial && !this.includeNumbers) {
        this.generatedPassword = ""; // Se nessuna opzione è selezionata, imposta la password vuota
        this.passwordStrength = 0; // Imposta la forza della password a 0
        return;
      }

      let charset = "";
      if (this.includeUppercase) charset += "ABCDEFGHIJKLMNOPQRSTUVWXYZ";
      if (this.includeLowercase) charset += "abcdefghijklmnopqrstuvwxyz";
      if (this.includeSpecial) charset += "!@#$%^&*()_+[]{}|;:,.<>?";
      if (this.includeNumbers) charset += "0123456789";

      let password = "";
      for (let i = 0; i < this.passwordLength; i++) {
        const randomIndex = Math.floor(Math.random() * charset.length);
        password += charset[randomIndex];
      }

      // Assicurati che la password non superi mai i 64 caratteri
      this.generatedPassword = password.slice(0, 64);
      this.calculatePasswordStrength();
    },

    copyToClipboard() {
      const passwordElement = document.createElement("textarea");
      passwordElement.value = this.generatedPassword;
      document.body.appendChild(passwordElement);
      passwordElement.select();
      document.execCommand("copy");
      document.body.removeChild(passwordElement);

      this.copySuccess = true;

      setTimeout(() => {
        this.copySuccess = false;
      }, 2000);
    },
  },
  created() {
    // Genera la password iniziale
    this.generatePassword();
  },
};
</script>

<style scoped>
.password-box {
  cursor: pointer;
  color: #ffffff;
  font-weight: 600;
  padding: 14px;
}

.password-strength {
  padding: 10px;
  border-radius: 5px;
  margin-top: 10px;
}

.slider.has-output::-webkit-slider-runnable-track {
  background-color: #ddfffa;
}

.slider.has-output::-moz-range-track {
  background-color: #ddfffa;
}

.slider.has-output::-ms-fill-lower {
  background-color: #ddfffa;
}
</style>