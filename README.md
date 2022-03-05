# Currency-Converter-

INDEX.HTML FILE

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Currency Converter</title>
  <link rel="stylesheet" href="https://pro.fontawesome.com/releases/v5.10.0/css/all.css" integrity="sha384-AYmEC3Yw5cVb3ZcuHtOA93w35dYTsvhLPVnYs9eStHfGJvOvKxVfELGroGkvsg+p" crossorigin="anonymous"/>
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <div class="container">
    <div class="header">
      <h1>Currency Converter</h1>
    </div>
    <div class="date"></div>
    <ul class="currencies"></ul>
    <button class="add-currency-btn"><i class="fas fa-long-arrow-alt-left"></i>Add Currency</button>
    <ul class="add-currency-list"></ul>
  </div>
    
  <script src="script.js"></script>
</body>
</html>


<!-- <li class="currency base-currency" id="USD">
        <img src="https://www.geoname.org/flags/1/us.gif" class="flag">
        <div class="info">
          <p class="input"><span class="currency-symbol">$<input placeholder="0.0000"></span></p>
          <p class="currency-name">USD - US Dollar</p>
          <p class="base-rate">1 USD = 1 USD</p>
        </div>
        <span class="close">&times;</span>
      </li>
        <li class="currency" id="JPY">
        <img src="https://www.geoname.org/flags/1/jp.gif" class="flag">
        <div class="info">
          <p class="input"><span class="currency-symbol">&#165;<input placeholder="0.0000"></span></p>
          <p class="currency-name">JPY - Japanese Yen</p>
          <p class="base-rate">1 USD = 111.0401 JPY</p>
        </div>
        <span class="close">&times;</span>
        </li>
        <li class="currency" id="INR">
        <img src="https://www.geoname.org/flags/1/in.gif" class="flag">
        <div class="info">
          <p class="input"><span class="currency-symbol">&#8377;<input placeholder="0.0000"></span></p>
          <p class="currency-name">INR - Indian Rupee</p>
          <p class="base-rate">1 USD = 71.1777 INR</p>
        </div>
        <span class="close">&times;</span>
        </li> -->

  <!-- (API WEBSITE) https://exchangerate.host/#/ -->

      <!-- <li data-currency="USD"class="disabled"><img src="https://www.geoname.org/flags/1/us.gif" class="flag"><span>USD - US Dollar</span></li>
      <li data-currency="JPY"><img src="https://www.geoname.org/flags/1/jp.gif" class="flag"><span>JPY - Japanese Yen</span></li>
      <li data-currency="INR"><img src="https://www.geoname.org/flags/1/in.gif" class="flag"><span>INR - Indian Rupee</span></li> -->
     
SCRIPT.JS FILE

const addCurrencyBtn = document.querySelector(".add-currency-btn");
const addCurrencyList = document.querySelector(".add-currency-list");
const currenciesList = document.querySelector(".currencies");

const dataURL = "https://api.exchangerate.host/latest";

const initiallyDisplayedCurrencies = ["USD", "EUR", "JPY"];
let baseCurrency;
let baseCurrencyAmount;

let currencies = [
  {
    name: "US Dollar",
    abbreviation: "USD",
    symbol: "\u0024",
    flagURL: "http://www.geonames.org/flags/l/us.gif"

  },
  {
    name: "Japanese Yen",
    abbreviation: "JPY",
    symbol: "\u00A5",
    flagURL: "http://www.geonames.org/flags/l/jp.gif"
  },
  {
    name: "Indian Rupee",
    abbreviation: "INR",
    symbol: "\u20B9",
    flagURL: "http://www.geonames.org/flags/l/in.gif"
  },
];

// Event Listeners

addCurrencyBtn.addEventListener("click", addCurrencyBtnClick);

function addCurrencyBtnClick(event) {
  addCurrencyBtn.classList.toggle("open");
}

addCurrencyList.addEventListener("click", addCurrencyListClick);

function addCurrencyListClick(event) {
  const clickedListItem = event.target.closest("li");
  if(!clickedListItem.classList.contains("disabled")) {
    const newCurrency = currencies.find(c => c.abbreviation===clickedListItem.getAttribute("data-currency"));
    if(newCurrency) newCurrenciesListItem(newCurrency);
  }
}

currenciesList.addEventListener("click", currenciesListClick);

function currenciesListClick(event) {
  if(event.target.classList.contains("close")) {
    const parentNode = event.target.parentNode;
    parentNode.remove();
    addCurrencyList.querySelector(`[data-currency=${parentNode.id}]`).classList.remove("disabled");
    if(parentNode.classList.contains("base-currency")) {
      const newBaseCurrencyLI = currenciesList.querySelector(".currency");
      if(newBaseCurrencyLI) {
        setNewBaseCurrency(newBaseCurrencyLI);
        baseCurrencyAmount = Number(newBaseCurrencyLI.querySelector(".input input").value);
      }
    }
  }
}

function setNewBaseCurrency(newBaseCurrencyLI) {
  newBaseCurrencyLI.classList.add("base-currency");
  baseCurrency = newBaseCurrencyLI.id;
  const baseCurrencyRate = currencies.find(currency => currency.abbreviation===baseCurrency).rate;
  currenciesList.querySelectorAll(".currency").forEach(currencyLI => {
    const currencyRate = currencies.find(currency => currency.abbreviation===currencyLI.id).rate;
    const exchangeRate = currencyLI.id===baseCurrency ? 1 : (currencyRate/baseCurrencyRate).toFixed(4);
    currencyLI.querySelector(".base-currency-rate").textContent = `1 ${baseCurrency} = ${exchangeRate} ${currencyLI.id}`;
  });
}

currenciesList.addEventListener("input", currenciesListInputChange);

function currenciesListInputChange(event) {
  const isNewBaseCurrency = event.target.closest("li").id!==baseCurrency;
  if(isNewBaseCurrency) {
    currenciesList.querySelector(`#${baseCurrency}`).classList.remove("base-currency");
    setNewBaseCurrency(event.target.closest("li"));
  }
  const newBaseCurrencyAmount = isNaN(event.target.value) ? 0 : Number(event.target.value);
  if(baseCurrencyAmount!==newBaseCurrencyAmount || isNewBaseCurrency) {
    baseCurrencyAmount = newBaseCurrencyAmount;
    const baseCurrencyRate = currencies.find(currency => currency.abbreviation===baseCurrency).rate;
    currenciesList.querySelectorAll(".currency").forEach(currencyLI => {
      if(currencyLI.id!==baseCurrency) {
        const currencyRate = currencies.find(currency => currency.abbreviation===currencyLI.id).rate;
        const exchangeRate = currencyLI.id===baseCurrency ? 1 : (currencyRate/baseCurrencyRate).toFixed(4);
        currencyLI.querySelector(".input input").value = exchangeRate*baseCurrencyAmount!==0 ? (exchangeRate*baseCurrencyAmount).toFixed(4) : "";
      }
    });
  }
}

currenciesList.addEventListener("focusout", currenciesListFocusOut);

function currenciesListFocusOut(event) {
  const inputValue = event.target.value;
  if(isNaN(inputValue) || Number(inputValue)===0) event.target.value="";
  else event.target.value = Number(inputValue).toFixed(4);
}

currenciesList.addEventListener("keydown", currenciesListKeyDown);

function currenciesListKeyDown(event) {
  if(event.key==="Enter") event.target.blur();
}

currenciesList.addEventListener("keydown", currenciesListKeyDown);

function currenciesListKeyDown(event) {
  if(event.key==="Enter") event.target.blur();
}

// Auxiliary Functions

function populateAddCyrrencyList() {
  for(let i=0; i<currencies.length; i++) {
    addCurrencyList.insertAdjacentHTML(
      "beforeend", 
      `<li data-currency=${currencies[i].abbreviation}>
        <img src=${currencies[i].flagURL} class="flag"><span>${currencies[i].abbreviation} - ${currencies[i].name}</span>
      </li>`
    );
  }
}

function populateCurrenciesList() {
  for(let i=0; i<initiallyDisplayedCurrencies.length; i++) {
    const currency = currencies.find(c => c.abbreviation===initiallyDisplayedCurrencies[i]);
    if(currency) newCurrenciesListItem(currency);
  }
}

function newCurrenciesListItem(currency) {
  if(currenciesList.childElementCount===0) {
    baseCurrency = currency.abbreviation;
    baseCurrencyAmount = 0;
  }
  addCurrencyList.querySelector(`[data-currency=${currency.abbreviation}]`).classList.add("disabled");
  const baseCurrencyRate = currencies.find(c => c.abbreviation===baseCurrency).rate;
  const exchangeRate = currency.abbreviation===baseCurrency ? 1 : (currency.rate/baseCurrencyRate).toFixed(4);
  const inputValue = baseCurrencyAmount ? (baseCurrencyAmount*exchangeRate).toFixed(4) : "";

  currenciesList.insertAdjacentHTML(
    "beforeend",
    `<li class="currency ${currency.abbreviation===baseCurrency ? "base-currency" : ""}" id=${currency.abbreviation}>
      <img src=${currency.flagURL} class="flag">
      <div class="info">
        <p class="input"><span class="currency-symbol">${currency.symbol}</span><input placeholder="0.0000" value=${inputValue}></p>
        <p class="currency-name">${currency.abbreviation} - ${currency.name}</p>
        <p class="base-currency-rate">1 ${baseCurrency} = ${exchangeRate} ${currency.abbreviation}</p>
      </div>
      <span class="close">&times;</span>
    </li>`
  );
}

fetch(dataURL)
  .then(res => res.json())
  .then(data => {
    document.querySelector(".date").textContent = data.date;
    data.rates["EUR"] = 1;
    currencies = currencies.filter(currency => data.rates[currency.abbreviation]);
    currencies.forEach(currency => currency.rate = data.rates[currency.abbreviation]);
    populateAddCyrrencyList();
    populateCurrenciesList();
  })
  .catch(err => console.log(err));
  
STYLE.CSS FILE

@import url('https://fonts.googleapis.com/css?family=Montserrat');

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
html {
  font-size: 16px;
}
body {
  font-family: 'Montserrat' , sans-serif;
  background-color: rgb(155, 155, 155);
  color: white;
}
.container {
  width: 500px;
  margin: 20px auto;
  user-select: none;
  overflow-x: hidden;
  position: relative;
}
.header {
  background-color: rgb(48, 48, 48);
  text-align: center;
  padding: 1.75rem;
}
.header h1 {
  font-size: 2.25rem;
}
.date {
  background-color: rgb(77, 77, 77);
  text-align: right;
  font-size: 0.75rem;
  padding: 0.75rem 2rem 0.75rem 0;
}
ul.currencies {
  height: calc(100vh - 40px - 100px - 40px - 58px);
  background-color: rgb(77, 77, 77);
  padding: 0 1.5rem 1rem 1.5rem;
  overflow-y: auto;
}
ul.currencies li {
  background-color: rgb(33, 33, 34); 
  border-radius: 5px;
  list-style: none;
  padding: 1rem 1rem 0.75rem 1rem;
  margin-bottom: 1rem;
  position: relative;
}
ul.currencies li:last-child {
  margin-bottom: 0;
}
ul.currencies li.base-currency {
  background-color: #264d73;
}
.flag {
  width: 60px;
  height: 40px;
  border: 1px solid white;
  vertical-align: top;
}
.info {
  display: inline-block;
  width: 78%;
}
.info .input span {
  font-size: 1.5rem;
  display: inline-block;
  width: 4rem;
  text-align: center;
}
.info .input input {
  font-size: 1.5rem;
  width: 78%;
  background-color: transparent;
  border: 2px solid white;
  border-radius: 5px;
  color: white;
  padding: 0.3rem;
  margin-bottom: 0.75rem;
}
.info .currency-name {
  font-size: 1rem;
  font-weight: bold;
  margin-bottom: 0.5rem;
  margin-left: 4rem;
}
.info .base-currency-rate {
  font-size: 0.8rem;
  margin-left: 4rem;
}
ul.currencies li .close {
  position: absolute;
  top: 0;
  right: 0;
  padding: 0 0.5rem;
  font-size: 1.5rem;
  color: #666;
  cursor: pointer;
}
ul.currencies li .close:hover {
  color: white;
}

ul.currencies::-webkit-scrollbar {
  width: 5px;
}
ul.currencies::-webkit-scrollbar-thumb {
  background-color: black;
  border-bottom: 1rem solid grey;
}

ul.add-currency-list {
  position: absolute;
  bottom: 54px;
  left: 105%;
  background-color: white;
  color: grey;
  width: 100%;
  height: calc(100vh - 40px - 100px - 55px);
  overflow-y: auto;
  transition: all 0.25s;
}
ul.add-currency-list li {
  list-style: none;
  padding: 0.75rem;
  border-bottom: 1px solid #ddd;
}
ul.add-currency-list li.disabled {
  opacity: 0.7;
  cursor: not-allowed;
}
ul.add-currency-list li .flag {
  width: 3rem;
  height: 2rem;
  vertical-align: middle;
}
ul.add-currency-list li span {
  margin-left: 1rem;
  font-weight: bold;
}

.add-currency-btn {
  background-color: rgb(255, 113, 47);
  color: white;
  padding: 1rem;
  font-size: 1.2rem;
  font-weight: bold;
  border: none;
  border-top: 3px solid black;
  outline: none;
  width: 100%;
  cursor: pointer;
  transition: background-color 0.25s;
  position: relative;
}
.add-currency-btn i {
  position: absolute;
  top: 0.6rem;
  left: 30%;
  font-size: 2rem;
  opacity: 0;
  transition: all 0.25s;
}
.add-currency-btn.open {
  background-color: red;
}
.add-currency-btn.open i {
  opacity: 1;
  left: 1.25rem;
}
.add-currency-btn.open +  ul.add-currency-list {
  left: 0;
}
.add-currency-btn.open +  ul.add-currency-list li:hover {
  background-color: #ddd;
}

@media (max-width: 600px) {
  html { font-size: 14px; }
  .container { width: 100%; margin: 0 auto; }
  ul.currencies { height: calc(100vh - 83px -34px -51px); }
  .header h1 { font-size: 2rem; }
  .flag { width: 3rem; height: 2rem; }
  .info .input span { font-size: 1.25rem; width: 3.5rem; }
  .info .input input { font-size: 1.25rem; width: 76%;}
  .info .currency-name { margin-left: 3.5rem; }
  .info .base-currency-rate { margin-left: 3.5rem; }
  ul.add-currency-list { bottom: 48px; height: calc(100vh - 80px - 51px); }
  .add-currency-btn i { left: 25%; top:0.65rem; }
}

