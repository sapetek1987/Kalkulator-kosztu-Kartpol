<!DOCTYPE html>
<html lang="pl">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">

  <title>Kalkulator kosztu - Kartpol</title>

  <style>
    * {
      box-sizing: border-box;
    }

    body {
      margin: 0;
      font-family: Arial, sans-serif;
      background: #f4f6f8;
      color: #1f2937;
    }

    .container {
      max-width: 720px;
      margin: 0 auto;
      padding: 25px 16px 40px;
    }

    .card {
      background: white;
      border-radius: 16px;
      padding: 25px;
      box-shadow: 0 6px 25px rgba(0, 0, 0, 0.08);
    }

    h1 {
      margin-top: 0;
      margin-bottom: 8px;
      font-size: 30px;
    }

    .subtitle {
      color: #6b7280;
      margin-bottom: 25px;
    }

    label {
      display: block;
      font-weight: bold;
      margin: 16px 0 8px;
    }

    input,
    select {
      width: 100%;
      padding: 13px;
      border: 1px solid #d1d5db;
      border-radius: 10px;
      font-size: 16px;
      background: white;
    }

    input:focus,
    select:focus {
      outline: 2px solid #2563eb;
      border-color: #2563eb;
    }

    .row {
      display: grid;
      grid-template-columns: 1fr 1fr;
      gap: 15px;
    }

    button {
      width: 100%;
      margin-top: 24px;
      padding: 15px;
      border: none;
      border-radius: 10px;
      background: #2563eb;
      color: white;
      font-size: 17px;
      font-weight: bold;
      cursor: pointer;
    }

    button:hover {
      background: #1d4ed8;
    }

    .result {
      margin-top: 25px;
      padding: 20px;
      border-radius: 12px;
      background: #eff6ff;
      border: 1px solid #bfdbfe;
    }

    .result-title {
      font-size: 16px;
      color: #4b5563;
    }

    .total {
      display: block;
      margin-top: 5px;
      font-size: 32px;
      font-weight: bold;
      color: #111827;
    }

    .details {
      margin-top: 10px;
      color: #6b7280;
      font-size: 14px;
    }

    .error {
      margin-top: 12px;
      color: #b91c1c;
      font-weight: bold;
    }

    .footer {
      text-align: center;
      margin-top: 20px;
      color: #9ca3af;
      font-size: 13px;
    }

    @media (max-width: 560px) {
      .row {
        grid-template-columns: 1fr;
      }

      .card {
        padding: 18px;
      }

      h1 {
        font-size: 26px;
      }
    }
  </style>
</head>

<body>

  <main class="container">

    <section class="card">

      <h1>Kalkulator kosztu - Kartpol</h1>

      <p class="subtitle">
        Oblicz orientacyjny koszt na podstawie ilości, ceny i kosztów dodatkowych.
      </p>

      <!-- ILOŚĆ I JEDNOSTKA -->

      <div class="row">

        <div>
          <label for="quantity">
            Ilość
          </label>

          <input
            id="quantity"
            type="number"
            min="0"
            step="0.01"
            value="1"
          >
        </div>

        <div>
          <label for="unit">
            Jednostka
          </label>

          <select id="unit">
            <option value="szt.">szt.</option>
            <option value="m">m</option>
            <option value="m²">m²</option>
            <option value="m³">m³</option>
            <option value="kg">kg</option>
          </select>
        </div>

      </div>


      <!-- CENA -->

      <label for="price">
        Cena jednostkowa (zł)
      </label>

      <input
        id="price"
        type="number"
        min="0"
        step="0.01"
        value="0"
        placeholder="np. 100"
      >


      <!-- KOSZTY DODATKOWE -->

      <label for="additional">
        Koszty dodatkowe (zł)
      </label>

      <input
        id="additional"
        type="number"
        min="0"
        step="0.01"
        value="0"
        placeholder="np. 50"
      >


      <!-- RABAT -->

      <label for="discount">
        Rabat (%)
      </label>

      <input
        id="discount"
        type="number"
        min="0"
        max="100"
        step="0.01"
        value="0"
        placeholder="np. 10"
      >


      <!-- PRZYCISK -->

      <button id="calculate" type="button">
        Oblicz koszt
      </button>


      <!-- KOMUNIKAT BŁĘDU -->

      <div
        id="error"
        class="error"
        aria-live="polite"
      ></div>


      <!-- WYNIK -->

      <div
        class="result"
        aria-live="polite"
      >

        <div class="result-title">
          Koszt końcowy:
        </div>

        <span
          id="total"
          class="total"
        >
          0,00 zł
        </span>

        <div
          id="details"
          class="details"
        >
          Wprowadź dane i kliknij „Oblicz koszt”.
        </div>

      </div>

    </section>


    <div class="footer">
      Kalkulator kosztu - Kartpol
    </div>

  </main>


  <script>

    // Pobieranie elementów formularza

    const quantity =
      document.getElementById("quantity");

    const price =
      document.getElementById("price");

    const additional =
      document.getElementById("additional");

    const discount =
      document.getElementById("discount");

    const unit =
      document.getElementById("unit");

    const total =
      document.getElementById("total");

    const details =
      document.getElementById("details");

    const error =
      document.getElementById("error");

    const calculateButton =
      document.getElementById("calculate");


    // Formatowanie kwoty

    function formatMoney(value) {

      return value.toLocaleString(
        "pl-PL",
        {
          minimumFractionDigits: 2,
          maximumFractionDigits: 2
        }
      ) + " zł";

    }


    // Obliczanie kosztu

    function calculate() {

      error.textContent = "";


      const q = Number(quantity.value);

      const p = Number(price.value);

      const extra = Number(additional.value);

      const disc = Number(discount.value);


      // Sprawdzenie poprawności danych

      if (
        !Number.isFinite(q) ||
        !Number.isFinite(p) ||
        !Number.isFinite(extra) ||
        !Number.isFinite(disc)
      ) {

        error.textContent =
          "Wprowadź poprawne wartości.";

        return;

      }


      if (
        q < 0 ||
        p < 0 ||
        extra < 0 ||
        disc < 0 ||
        disc > 100
      ) {

        error.textContent =
          "Sprawdź wprowadzone wartości.";

        return;

      }


      // Cena podstawowa

      const baseCost =
        q * p;


      // Koszt przed rabatem

      const costBeforeDiscount =
        baseCost + extra;


      // Wartość rabatu

      const discountValue =
        costBeforeDiscount * (disc / 100);


      // Cena końcowa

      const finalCost =
        costBeforeDiscount - discountValue;


      // Wyświetlenie wyniku

      total.textContent =
        formatMoney(finalCost);


      // Szczegóły

      details.textContent =
        q +
        " " +
        unit.value +
        " × " +
        formatMoney(p) +
        " + " +
        formatMoney(extra) +
        " kosztów dodatkowych" +
        (disc > 0
          ? ", rabat " + disc + "%"
          : "");

    }


    // Kliknięcie przycisku

    calculateButton.addEventListener(
      "click",
      calculate
    );


    // Automatyczne przeliczanie po zmianie danych

    [
      quantity,
      price,
      additional,
      discount
    ].forEach(function(element) {

      element.addEventListener(
        "input",
        calculate
      );

    });


    unit.addEventListener(
      "change",
      calculate
    );


    // Pierwsze obliczenie

    calculate();

  </script>

</body>
</html>
