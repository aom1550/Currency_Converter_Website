<template>
    <div class="app-container">
      <!-- หน้าเริ่มต้น -->
      <div v-if="!appStarted" class="welcome-screen">
        <div class="welcome-content">
          <h1>ยินดีต้อนรับสู่แอพแลกเปลี่ยนเงินตรา</h1>
          <p>แอพแลกเปลี่ยนเงินตราที่ช่วยให้คุณคำนวณอัตราแลกเปลี่ยนได้อย่างรวดเร็วและแม่นยำ</p>
          <div class="features">
            <div class="feature-item">
              <div class="feature-icon">💱</div>
              <h3>อัตราแลกเปลี่ยนแบบเรียลไทม์</h3>
              <p>ข้อมูลปัจจุบันจากแหล่งข้อมูลที่เชื่อถือได้</p>
            </div>
            <div class="feature-item">
              <div class="feature-icon">🔄</div>
              <h3>แปลงได้หลายสกุลเงิน</h3>
              <p>รองรับมากกว่า 150 สกุลเงินจากทั่วโลก</p>
            </div>
            <div class="feature-item">
              <div class="feature-icon">📱</div>
              <h3>รองรับทุกอุปกรณ์</h3>
              <p>ใช้งานได้ทั้งบนคอมพิวเตอร์ แท็บเล็ต และสมาร์ทโฟน</p>
            </div>
          </div>
          <button @click="startApp" class="start-btn">เริ่มต้นใช้งาน</button>
        </div>
      </div>
  
      <!-- หน้าแอพแลกเปลี่ยนเงินตรา (ปรับปรุงใหม่) -->
      <div v-else class="converter-screen">
        <div class="converter-content">
          <h1>Currency Converter</h1>
          
          <div class="currency-form">
            <div class="form-row">
              <div class="form-group">
                <label for="from">จากสกุลเงิน:</label>
                <select id="from" v-model="fromCurrency" @change="calculateResults">
                  <option v-for="currency in currencies" :key="currency" :value="currency">
                    {{ currency }}
                  </option>
                </select>
              </div>
              
              <div class="swap-container">
                <button @click="swapCurrencies" class="swap-btn circular-btn">
                  <span class="swap-icon">⇄</span>
                </button>
              </div>
              
              <div class="form-group">
                <label for="to">เป็นสกุลเงิน:</label>
                <select id="to" v-model="toCurrency" @change="calculateResults">
                  <option v-for="currency in currencies" :key="currency" :value="currency">
                    {{ currency }}
                  </option>
                </select>
              </div>
            </div>
          </div>
          
          <div v-if="loading" class="loading">
            <div class="loader"></div>
            <p>กำลังโหลดข้อมูลอัตราแลกเปลี่ยน...</p>
          </div>
          <div v-else-if="error" class="error">{{ error }}</div>
          <div v-else class="amount-container">
            <div class="rate-card">
              <h3>อัตราแลกเปลี่ยนปัจจุบัน</h3>
              <div class="rate-display">
                <span class="currency-pill">1 {{ fromCurrency }}</span>
                <span class="equals-sign">=</span>
                <span class="currency-pill highlight-pill">{{ rate }} {{ toCurrency }}</span>
              </div>
              <p class="last-updated">อัพเดทล่าสุด: {{ lastUpdated }}</p>
            </div>
  
            <h2 class="amount-heading">จำนวนเงินที่ต้องการแลกเปลี่ยน</h2>
            
            <div v-for="(item, index) in amounts" :key="index" class="amount-row">
              <div class="amount-input">
                <input
                  type="number"
                  v-model.number="item.value"
                  min="0"
                  @input="calculateResults"
                  :placeholder="'จำนวนเงิน ' + (index + 1)"
                />
                <button @click="calculate(index)" class="calc-btn">คำนวณ</button>
                <button @click="removeAmount(index)" class="remove-btn" v-if="amounts.length > 1">
                  <span class="remove-icon">×</span>
                </button>
              </div>
              
              <div v-if="item.result" class="result">
                <div class="result-box">
                  <div class="result-from">{{ item.value.toLocaleString() }} {{ fromCurrency }}</div>
                  <div class="result-arrow">➜</div>
                  <div class="result-to">{{ item.result.toLocaleString() }} {{ toCurrency }}</div>
                </div>
              </div>
            </div>
            
            <button @click="addAmount" class="add-btn">
              <span class="add-icon">+</span> เพิ่มจำนวนเงิน
            </button>
          </div>
          
          <button @click="backToWelcome" class="back-btn">กลับไปหน้าหลัก</button>
        </div>
      </div>
    </div>
  </template>
    
  <script>
  export default {
    data() {
      return {
        appStarted: false,
        amounts: [{ value: 1000, result: null }],
        fromCurrency: 'USD',
        toCurrency: 'THB',
        currencies: [],
        rates: {},
        rate: null,
        lastUpdated: null,
        loading: true,
        error: null,
        API_KEY: 'c8d5ac4e5948e5f7c17b6bcb'
      }
    },
    
    mounted() {
      this.fetchCurrencies();
    },
    
    methods: {
      startApp() {
        this.appStarted = true;
      },
  
      backToWelcome() {
        this.appStarted = false;
      },
  
      fetchCurrencies() {
        this.loading = true;
        this.error = null;
        
        fetch(`https://v6.exchangerate-api.com/v6/${this.API_KEY}/latest/USD`)
          .then(response => {
            if (!response.ok) {
              throw new Error('ไม่สามารถเชื่อมต่อกับ API ได้');
            }
            return response.json();
          })
          .then(data => {
            if (data.result === 'success') {
              this.rates = data.conversion_rates;
              this.currencies = Object.keys(data.conversion_rates);
              this.lastUpdated = new Date(data.time_last_update_unix * 1000).toLocaleString('th-TH');
              this.calculateResults();
            } else {
              throw new Error('ไม่สามารถดึงข้อมูลอัตราแลกเปลี่ยนได้');
            }
          })
          .catch(error => {
            this.error = error.message;
          })
          .finally(() => {
            this.loading = false;
          });
      },
      
      calculateResults() {
        if (!this.rates || Object.keys(this.rates).length === 0) {
          return;
        }
        
        // คำนวณอัตราแลกเปลี่ยน
        if (this.fromCurrency === 'USD') {
          this.rate = this.rates[this.toCurrency].toFixed(6);
        } else if (this.toCurrency === 'USD') {
          this.rate = (1 / this.rates[this.fromCurrency]).toFixed(6);
        } else {
          // แปลงผ่าน USD
          const fromRateToUSD = 1 / this.rates[this.fromCurrency];
          const toRateFromUSD = this.rates[this.toCurrency];
          this.rate = (fromRateToUSD * toRateFromUSD).toFixed(6);
        }
        
        // คำนวณผลลัพธ์สำหรับทุกจำนวนเงิน
        this.amounts.forEach(item => {
          if (item.value && !isNaN(item.value)) {
            item.result = parseFloat((item.value * this.rate).toFixed(2));
          } else {
            item.result = null;
          }
        });
      },
      
      calculate(index) {
        if (this.amounts[index].value && !isNaN(this.amounts[index].value)) {
          this.amounts[index].result = parseFloat((this.amounts[index].value * this.rate).toFixed(2));
        }
      },
      
      swapCurrencies() {
        const temp = this.fromCurrency;
        this.fromCurrency = this.toCurrency;
        this.toCurrency = temp;
        this.calculateResults();
      },
      
      addAmount() {
        this.amounts.push({ value: '', result: null });
      },
      
      removeAmount(index) {
        if (this.amounts.length > 1) {
          this.amounts.splice(index, 1);
        }
      }
    }
  }
  </script>
    
  <style scoped>
  /* ===== รีเซ็ตสไตล์พื้นฐาน ===== */
  * {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
  }
  
  /* ===== สไตล์หลักของแอพ (เต็มจอ) ===== */
  .app-container {
    min-height: 100vh;
    width: 100vw;
    font-family: 'Kanit', sans-serif;
    overflow-x: hidden;
  }
  
  /* ===== หน้าเริ่มต้น (เต็มจอ) ===== */
  .welcome-screen {
    min-height: 100vh;
    width: 100vw;
    display: flex;
    align-items: center;
    justify-content: center;
    background: linear-gradient(145deg, #3498db, #1abc9c);
    color: white;
    text-align: center;
    padding: 0;
  }
  
  .welcome-content {
    max-width: 1100px;
    width: 90%;
    padding: 50px 30px;
    background-color: rgba(255, 255, 255, 0.1);
    border-radius: 20px;
    backdrop-filter: blur(10px);
    box-shadow: 0 15px 35px rgba(0, 0, 0, 0.2);
  }
  
  .welcome-content h1 {
    font-size: 3rem;
    margin-bottom: 25px;
    text-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
    background: linear-gradient(45deg, #ffffff, #e0e0e0);
    -webkit-background-clip: text;
    background-clip: text;
    color: transparent;
    font-weight: 800;
  }
  
  .welcome-content p {
    font-size: 1.3rem;
    margin-bottom: 40px;
    color: rgba(255, 255, 255, 0.95);
    line-height: 1.6;
  }
  
  .features {
    display: flex;
    justify-content: center;
    flex-wrap: wrap;
    gap: 30px;
    margin: 40px 0;
  }
  
  .feature-item {
    flex: 1;
    min-width: 250px;
    max-width: 320px;
    padding: 30px 25px;
    background-color: rgba(255, 255, 255, 0.2);
    border-radius: 15px;
    transition: transform 0.3s, box-shadow 0.3s;
    border: 1px solid rgba(255, 255, 255, 0.1);
  }
  
  .feature-item:hover {
    transform: translateY(-7px);
    box-shadow: 0 15px 30px rgba(0, 0, 0, 0.15);
    background-color: rgba(255, 255, 255, 0.25);
  }
  
  .feature-icon {
    font-size: 3rem;
    margin-bottom: 15px;
    display: inline-block;
    text-shadow: 0 2px 10px rgba(0, 0, 0, 0.2);
  }
  
  .feature-item h3 {
    font-size: 1.5rem;
    margin-bottom: 15px;
    color: #ffffff;
  }
  
  .feature-item p {
    font-size: 1.1rem;
    color: rgba(255, 255, 255, 0.9);
    margin-bottom: 0;
  }
  
  .start-btn {
    margin-top: 50px;
    padding: 18px 70px;
    font-size: 1.3rem;
    font-weight: bold;
    background: linear-gradient(45deg, #e74c3c, #c0392b);
    color: white;
    border: none;
    border-radius: 50px;
    cursor: pointer;
    transition: all 0.3s;
    box-shadow: 0 10px 20px rgba(0, 0, 0, 0.2);
    position: relative;
    overflow: hidden;
  }
  
  .start-btn:hover {
    transform: scale(1.05) translateY(-3px);
    box-shadow: 0 15px 30px rgba(0, 0, 0, 0.25);
  }
  
  .start-btn:active {
    transform: scale(0.98);
  }
  
  .start-btn::after {
    content: '';
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(255, 255, 255, 0.1);
    border-radius: 50px;
    transform: scaleX(0);
    transform-origin: left;
    transition: transform 0.5s;
  }
  
  .start-btn:hover::after {
    transform: scaleX(1);
  }
  
  /* ===== หน้าอัตราแลกเปลี่ยน (เต็มจอ ใช้สไตล์เดียวกับหน้าเริ่มต้น) ===== */
  .converter-screen {
    min-height: 100vh;
    width: 100vw;
    display: flex;
    align-items: center;
    justify-content: center;
    background: linear-gradient(145deg, #3498db, #1abc9c);
    color: white;
    padding: 40px 0;
  }
  
  .converter-content {
    max-width: 1000px;
    width: 90%;
    padding: 40px 30px;
    background-color: rgba(255, 255, 255, 0.1);
    border-radius: 20px;
    backdrop-filter: blur(10px);
    box-shadow: 0 15px 35px rgba(0, 0, 0, 0.2);
    color: #fff;
  }
  
  .converter-content h1 {
    text-align: center;
    font-size: 2.5rem;
    margin-bottom: 30px;
    text-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
    background: linear-gradient(45deg, #ffffff, #e0e0e0);
    -webkit-background-clip: text;
    background-clip: text;
    color: transparent;
    font-weight: 800;
  }
  
  .currency-form {
    margin-bottom: 30px;
  }
  
  .form-row {
    display: flex;
    justify-content: space-between;
    align-items: center;
    flex-wrap: wrap;
    gap: 15px;
  }
  
  .form-group {
    flex: 1;
    min-width: 200px;
  }
  
  .swap-container {
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 10px;
  }
  
  .swap-icon {
    font-size: 1.5rem;
    display: inline-block;
  }
  
  label {
    display: block;
    margin-bottom: 8px;
    font-weight: 500;
    color: rgba(255, 255, 255, 0.9);
    font-size: 1.1rem;
  }
  
  select {
    width: 100%;
    padding: 15px;
    border: 1px solid rgba(255, 255, 255, 0.2);
    border-radius: 10px;
    font-size: 16px;
    color: #fff;
    background-color: rgba(255, 255, 255, 0.1);
    backdrop-filter: blur(5px);
    appearance: none;
    cursor: pointer;
    transition: all 0.3s;
  }
  
  select:hover {
    background-color: rgba(255, 255, 255, 0.15);
  }
  
  select option {
    background-color: #34495e;
    color: white;
  }
  
  .amount-container {
    margin-top: 30px;
  }
  
  .rate-card {
    padding: 20px;
    background-color: rgba(255, 255, 255, 0.15);
    border-radius: 15px;
    margin-bottom: 30px;
    text-align: center;
    border: 1px solid rgba(255, 255, 255, 0.1);
  }
  
  .rate-card h3 {
    font-size: 1.2rem;
    margin-bottom: 15px;
    color: rgba(255, 255, 255, 0.9);
  }
  
  .rate-display {
    display: flex;
    justify-content: center;
    align-items: center;
    gap: 10px;
    margin-bottom: 15px;
    flex-wrap: wrap;
  }
  
  .currency-pill {
    padding: 10px 20px;
    background-color: rgba(255, 255, 255, 0.1);
    border-radius: 50px;
    font-weight: bold;
  }
  
  .highlight-pill {
    background: linear-gradient(45deg, #1abc9c, #3498db);
    color: white;
    box-shadow: 0 5px 15px rgba(0, 0, 0, 0.1);
  }
  
  .equals-sign {
    font-size: 1.5rem;
    color: rgba(255, 255, 255, 0.7);
  }
  
  .last-updated {
    font-size: 0.9rem;
    color: rgba(255, 255, 255, 0.7);
  }
  
  .amount-heading {
    margin: 30px 0 20px;
    color: rgba(255, 255, 255, 0.9);
    font-size: 1.5rem;
    text-align: center;
  }
  
  .amount-row {
    margin-bottom: 25px;
    padding-bottom: 20px;
    border-bottom: 1px solid rgba(255, 255, 255, 0.1);
  }
  
  .amount-input {
    display: flex;
    gap: 10px;
    flex-wrap: wrap;
  }
  
  .amount-input input {
    flex: 1;
    min-width: 150px;
    padding: 15px;
    border: 1px solid rgba(255, 255, 255, 0.2);
    border-radius: 10px;
    font-size: 16px;
    color: #fff;
    background-color: rgba(255, 255, 255, 0.1);
  }
  
  .amount-input input:focus {
    outline: none;
    border-color: rgba(255, 255, 255, 0.4);
    background-color: rgba(255, 255, 255, 0.15);
  }
  
  .circular-btn {
    width: 50px;
    height: 50px;
    display: flex;
    justify-content: center;
    align-items: center;
    border-radius: 50%;
    padding: 0;
  }
  
  .result {
    margin-top: 15px;
  }
  
  .result-box {
    display: flex;
    justify-content: center;
    align-items: center;
    gap: 15px;
    padding: 15px;
    background-color: rgba(255, 255, 255, 0.15);
    border-radius: 10px;
    flex-wrap: wrap;
  }
  
  .result-from, .result-to {
    padding: 10px 15px;
    border-radius: 8px;
  }
  
  .result-from {
    background-color: rgba(52, 152, 219, 0.3);
    color: white;
  }
  
  .result-to {
    background-color: rgba(46, 204, 113, 0.3);
    color: white;
    font-weight: bold;
  }
  
  .result-arrow {
    color: rgba(255, 255, 255, 0.7);
    font-size: 1.5rem;
  }
  
  .calc-btn, .swap-btn, .add-btn, .back-btn, .remove-btn {
    padding: 15px;
    color: white;
    border: none;
    border-radius: 10px;
    cursor: pointer;
    font-size: 16px;
    transition: all 0.3s;
    font-weight: 500;
  }
  
  .calc-btn {
    background: linear-gradient(45deg, #f39c12, #e67e22);
    flex-shrink: 0;
  }
  
  .swap-btn {
    background: linear-gradient(45deg, #3498db, #2980b9);
  }
  
  .add-btn {
    width: 100%;
    margin-top: 20px;
    background: linear-gradient(45deg, #27ae60, #2ecc71);
    display: flex;
    justify-content: center;
    align-items: center;
    gap: 10px;
  }
  
  .add-icon {
    font-size: 1.2rem;
  }
  
  .back-btn {
    width: 100%;
    margin-top: 30px;
    background: rgba(255, 255, 255, 0.2);
    border: 1px solid rgba(255, 255, 255, 0.1);
  }
  
  .remove-btn {
    background: linear-gradient(45deg, #e74c3c, #c0392b);
    width: 40px;
    height: 40px;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 0;
    border-radius: 50%;
  }
  
  .remove-icon {
    font-size: 1.2rem;
  }
  
  .calc-btn:hover, .swap-btn:hover, .add-btn:hover, .remove-btn:hover {
    transform: translateY(-3px);
    box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2);
  }
  
  .back-btn:hover {
    background: rgba(255, 255, 255, 0.25);
  }
  
  .loading {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    padding: 40px 0;
  }
  
  .loader {
    width: 50px;
    height: 50px;
    border: 5px solid rgba(255, 255, 255, 0.3);
    border-radius: 50%;
    border-top-color: white;
    animation: spin 1s ease-in-out infinite;
    margin-bottom: 20px;
  }
  
  @keyframes spin {
    to { transform: rotate(360deg); }
  }
  
  .loading p {
    color: rgba(255, 255, 255, 0.8);
  }
  
  .error {
    padding: 20px;
    background-color: rgba(231, 76, 60, 0.3);
    border-left: 4px solid #e74c3c;
    color: white;
    border-radius: 10px;
    font-weight: 500;
    margin: 20px 0;
  }
  
  /* ===== ส่วนของ Responsive Design ===== */
  @media (max-width: 992px) {
    .welcome-content h1 {
      font-size: 2.5rem;
    }
    
    .converter-content h1 {
      font-size: 2.2rem;
    }
  }
  
  @media (max-width: 768px) {
    .welcome-content h1 {
      font-size: 2rem;
    }
    
    .welcome-content p {
      font-size: 1.1rem;
    }
    
    .feature-item {
      min-width: 100%;
      margin-bottom: 15px;
    }
    
    .converter-content {
      width: 95%;
      padding: 25px 20px;
    }
    
    .converter-content h1 {
      font-size: 1.8rem;
    }
    
    .amount-input {
      flex-direction: column;
    }
    
    .amount-input .calc-btn {
      width: 100%;
      margin-top: 10px;
    }
    
    .form-row {
      flex-direction: column;
    }
    
    .swap-container {
      margin: 10px 0;
    }
    
    .form-group {
      width: 100%;
    }
    
    .result-box {
      flex-direction: column;
    }
    
    .result-from, .result-to {
      width: 100%;
      text-align: center;
    }
  }
  
  @media (max-width: 480px) {
    .welcome-content {
      padding: 30px 20px;
    }
  
    .welcome-content h1 {
      font-size: 1.7rem;
    }
    
    .start-btn {
      padding: 15px 40px;
      font-size: 1.1rem;
    }
    
    .converter-content h1 {
      font-size: 1.5rem;
    }
    
    .amount-heading {
      font-size: 1.2rem;
    }
    
    .rate-card {
      padding: 15px;
    }
    
    .rate-display {
      flex-direction: column;
    }
    
    .equals-sign {
      transform: rotate(90deg);
    }
  }
  </style>