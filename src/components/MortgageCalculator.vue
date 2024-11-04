<template>
    <div class="credit-calculator">
      <div class="form-section">
        <div class="credit-goal">
          <label>Цель кредита:</label>
          <select>
            <option>Квартира на вторичном рынке</option>
            <option>Квартира на первичном рынке</option>
            <option>Загородная недвижимость</option>
          </select>
        </div>
  
        <div class="rate-options">
          <button v-for="interestRate in interestRates" :key="interestRate.name" :class="{ active: selectedRate === interestRate.rate }" @click="selectedRate = interestRate.rate">
            {{interestRate.name}} от {{interestRate.rate}}%
          </button>
        </div>
  
        <div class="checkbox-switch">
          <label>Получаю зарплату на Сбер</label>
          <div class="switch-container">
            <span class="rate-indicator" :class="{ active: isBankCustomer }">-0,5%</span>
            <input type="checkbox" v-model="isBankCustomer">
          </div>
        </div>
  
        <number-input v-model="propertyValue" label="Стоимость недвижимости" :min="334000" :max="100000000" />
        
        <number-input v-model="initialPayment" label="Первоначальный взнос" :min="propertyValue * 0.101" :max="propertyValue" />
  
        <div class="checkbox-switch">
          <label>Использовать материнский капитал</label>
          <input type="checkbox" v-model="useMaternityCapital">
        </div>

        <number-input v-model="loanTerm" label="Срок кредита" :min="1" :max="30" />
  
      </div>
  
      <div class="summary">
            <div class="summary-item">
                <p class="summary-title">Процентная ставка</p>
                <p class="summary-value">{{ interestRate }}%</p>
            </div>
            <div class="summary-item">
                <p class="summary-title">Ежемесячный платеж</p>
                <p class="summary-value">{{ formatNumber(monthlyPayment) }} руб.</p>
            </div>
            <div class="summary-item">
                <p class="summary-title">Сумма кредита</p>
                <p class="summary-value">{{ formatNumber(loanAmount) }} руб.</p>
            </div>
            <button @click="getApproval">Получить одобрение</button>
       </div>
    </div>
</template>
  
  
<script>
import NumberInput from './NumberInput.vue'

  const interestRates = [
    { name: 'Базовая ставка', rate: 4.9, },
    { name: 'Военная ипотека', rate: 0.4, },
    { name: 'Без первоначального взноса', rate: 5.7, },
  ]

  export default {
    data() {
      return {
        interestRates,
        selectedRate: interestRates[0].rate,
        isBankCustomer: false,
        propertyValue: 10000000,
        initialPayment: 1010000,
        useMaternityCapital: false,
        loanTerm: 10,
      };
    },

    computed: {
      interestRate() {
        return Math.max(0.1, this.selectedRate - (this.isBankCustomer ? 0.5 : 0))
      },
      loanAmount() {
        return Math.max(0, this.propertyValue - this.initialPayment - (this.useMaternityCapital ? 500000 : 0))
      },
      monthlyPayment() {
        if (this.loanAmount <= 0 || this.loanTerm <= 0) {
          return 0;
        }
        const monthlyRate = this.interestRate / 12 / 100;
        return parseFloat(
          (this.loanAmount * (monthlyRate / (1 - Math.pow(1 + monthlyRate, -this.loanTerm * 12)))).toFixed(2)
        );
      }
    },
    
    methods: {
      formatNumber(value) {
        return value.toLocaleString("ru-RU"); 
      },
      getApproval() {
        console.log({
          interest_rate: this.interestRate,
          loan_amount: this.loanAmount,
          initial_payment: this.useMaternityCapital ? undefined : this.initialPayment,
          bank_customer: this.isBankCustomer,
          loan_term: this.loanTerm,
          maternity_capital: this.useMaternityCapital ? 500000 : undefined,
          personal_funds: this.useMaternityCapital ? this.initialPayment : undefined,
        });
      },
    },

    components: {
      NumberInput,
    }
  };
  </script>
  
<style scoped>
.credit-calculator {
  display: grid;
  grid-template-columns: 1fr 420px;
  gap: 20px;
  color: #15203D;
  max-width: 1520px;
  margin: 10px;
  font-family: "SB Sans Text Medium";
}

.form-section {
  display: flex;
  flex-direction: column;
  gap: 20px; 
}

.summary-container {
  display: flex;
  flex-direction: column;
  align-items: flex-end; 
}
  
.credit-goal label,
.rate-options button,
.checkbox-switch label {
    font-size: 15px;
    color: #15203D;
}

.credit-goal select {
    background-color: #F3F6FF;
    border: 1px solid #E0E0E0;
    border-radius: 5px;
    padding: 17px;
    width: calc(100% - 34px); 
    color: #15203D;
    outline: none;
    font-family: "SB Sans Text Medium"; 
    font-size: 15px; 
    line-height: 1.5; 
}

.credit-goal {
    width: 102%;
}

.credit-goal select:hover {
    background-color: #dae3ff;
}

.rate-options button {
    background-color: #F0F4F8;
    color: #15203D;
    border: none;
    border-radius: 8px;
    padding: 10px 20px;
    font-size: 14px;
    cursor: pointer;
    transition: background-color 0.3s ease;
    font-family: "SB Sans Text Medium";
    margin-right: 14px;
}

.rate-options button.active {
    background-color: #24ADED;
    color: white;
}

.rate-options button:hover:not(.active) {
    background-color: #108ec9;
}

.checkbox-switch input[type="checkbox"] {
    width: 40px;
    height: 20px;
    appearance: none;
    background-color: #D3D3D3;
    border-radius: 20px;
    position: relative;
    outline: none;
    cursor: pointer;
    transition: background-color 0.3s ease;
}

.checkbox-switch input[type="checkbox"]:checked {
    background-color: #24ADED;
}

.checkbox-switch input[type="checkbox"]::after {
    content: "";
    width: 16px;
    height: 16px;
    background-color: #FFFFFF;
    border-radius: 50%;
    position: absolute;
    top: 2px;
    left: 2px;
    transition: transform 0.3s ease;
}

.checkbox-switch input[type="checkbox"]:checked::after {
    transform: translateX(20px);
}

.summary p {
    font-size: 16px;
    color: #15203D;
    margin-top: 5px;
}

.summary button {
    padding: 12px 20px;
    background-color: #24ADED;
    color: white;
    font-size: 16px;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    transition: background-color 0.3s ease;
    font-family: "SB Sans Text Medium";
}

.summary button:hover {
    background-color: #108ec9;
}

.checkbox-switch {
    display: flex;
    align-items: center; 
    justify-content: space-between; 
    padding: 10px 0;
}

.switch-container {
display: flex;
align-items: center; 
}

.rate-indicator {
    color: rgba(0, 0, 0, 0.5); 
    transition: color 0.3s ease; 
    margin-right: 10px;
}

.rate-indicator.active {
  color: #24ADED;
}

.summary {
    display: grid;
    grid-template-columns: 1fr 1fr; 
    grid-template-rows: auto auto auto; 
    gap: 20px;
    padding: 20px;
    border-radius: 5px;
    height: 250px;
}

.summary-item:nth-child(1) {
    grid-column: 1;
}

.summary-item:nth-child(2) {
    grid-column: 2; 
}

.summary-item:nth-child(3) {
    grid-column: 1 / span 2;
}

.summary-title {
    font-size: 14px;
    color: rgba(0, 0, 0, 0.5);
    margin: 0;
}

.summary-value {
    font-size: 24px;
    color: #15203D;
    font-weight: bold;
    margin: 0;
}

.summary button {
    grid-column: 1 / span 2; 
    padding: 15px;
    background-color: #24ADED;
    color: white;
    font-size: 16px;
    border: none;
    border-radius: 8px;
    cursor: pointer;
    transition: background-color 0.3s ease;
    font-family: "SB Sans Text Medium";
    height: 60px;
}

.summary button:hover {
    background-color: #108ec9;
}
</style>
  