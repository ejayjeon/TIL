 date: 2022.12.28.Wed.

 # :memo: 2. Google Pay 유동 가격 데모

[GooglePay](https://developers.google.com/pay/api/web/guides/resources/demos?hl=ko)

```html
<head>
  <script async src="https://pay.google.com/gp/p/js/pay.js" onload="onGooglePayLoaded()"></script>
</head>
<body>
  <div id="container"></div>
</body>
<script>
  const baseRequest = {
    apiVersion: 2,
    apiVersionMinor: 0
  };

  const allowedCardNetworks = ["AMEX", "DISCOVER", "JCB", "MASTERCARD", "MIR", "VISA"];

  const allowedCardAuthMethods = ["PAN_ONLY", "CRYPTOGRAM_3DS"];

  const tokenizationSpecification = {
    type: 'PAYMENT_GATEWAY',
    parameters: {
      'gateway': 'example',
      'gatewayMerchantId': 'exampleGatewayMerchantId'
    }
  };

  const baseCardPaymentMethod = {
    type: 'CARD',
    parameters: {
      allowedAuthMethods: allowedCardAuthMethods,
      allowedCardNetworks: allowedCardNetworks
    }
  };

  const cardPaymentMethod = Object.assign(
    {},
    baseCardPaymentMethod,
    {
      tokenizationSpecification: tokenizationSpecification
    }
  );

  let paymentsClient = null;

  function getGoogleIsReadyToPayRequest() {
    return Object.assign(
      {},
      baseRequest,
      {
        allowedPaymentMethods: [baseCardPaymentMethod]
      }
    );
  }

  function getGooglePaymentDataRequest() {
    const paymentDataRequest = Object.assign({}, baseRequest);
    paymentDataRequest.allowedPaymentMethods = [cardPaymentMethod];
    paymentDataRequest.transactionInfo = getGoogleTransactionInfo();
    paymentDataRequest.merchantInfo = {
      merchantName: 'Example Merchant'
    };

    paymentDataRequest.callbackIntents = ["SHIPPING_ADDRESS", "SHIPPING_OPTION", "PAYMENT_AUTHORIZATION"];
    paymentDataRequest.shippingAddressRequired = true;
    paymentDataRequest.shippingAddressParameters = getGoogleShippingAddressParameters(); // 배송지 주소 파라미터
    paymentDataRequest.shippingOptionRequired = true;

    return paymentDataRequest;
  }

  // 구매 주체 설정 : 실제는 이곳에 정보 대입
  function getGooglePaymentsClient() {
    if (paymentsClient === null) {
      paymentsClient = new google.payments.api.PaymentsClient({
        environment: "TEST",
        merchantInfo: {
          merchantName: "Example Merchant",
          merchantId: "01234567890123456789"
        },
        paymentDataCallbacks: {
          onPaymentAuthorized: onPaymentAuthorized,
          onPaymentDataChanged: onPaymentDataChanged
        }
      });
    }
    return paymentsClient;
  }

  function onPaymentAuthorized(paymentData) {
    return new Promise(function (resolve, reject) {

      processPayment(paymentData)
        .then(function () {
          resolve({ transactionState: 'SUCCESS' });
        })
        .catch(function () {
          resolve({
            transactionState: 'ERROR',
            error: {
              intent: 'PAYMENT_AUTHORIZATION',
              message: 'Insufficient funds',
              reason: 'PAYMENT_DATA_INVALID'
            }
          });
        });

    });
  }

  // 가격 정보 변경
  function onPaymentDataChanged(intermediatePaymentData) {
    return new Promise(function (resolve, reject) {

      let shippingAddress = intermediatePaymentData.shippingAddress;
      let shippingOptionData = intermediatePaymentData.shippingOptionData;
      let paymentDataRequestUpdate = {};

      if (intermediatePaymentData.callbackTrigger == "INITIALIZE" || intermediatePaymentData.callbackTrigger == "SHIPPING_ADDRESS") {
        if (shippingAddress.administrativeArea == "NJ") {
          paymentDataRequestUpdate.error = getGoogleUnserviceableAddressError();
        }
        else {
          paymentDataRequestUpdate.newShippingOptionParameters = getGoogleDefaultShippingOptions(); // 배송비 추가 옵션
          let selectedShippingOptionId = paymentDataRequestUpdate.newShippingOptionParameters.defaultSelectedOptionId;
          paymentDataRequestUpdate.newTransactionInfo = calculateNewTransactionInfo(selectedShippingOptionId);
        }
      }
      else if (intermediatePaymentData.callbackTrigger == "SHIPPING_OPTION") {
        paymentDataRequestUpdate.newTransactionInfo = calculateNewTransactionInfo(shippingOptionData.id);
      }

      resolve(paymentDataRequestUpdate);
    });
  }

  // 최종 트랜잭션 계산 : 아이템 가격 + 배송비
  function calculateNewTransactionInfo(shippingOptionId) {
    let newTransactionInfo = getGoogleTransactionInfo();

    let shippingCost = getShippingCosts()[shippingOptionId];
    newTransactionInfo.displayItems.push({
      type: "LINE_ITEM",
      label: "Shipping cost",
      price: shippingCost,
      status: "FINAL"
    });

    let totalPrice = 0;
    newTransactionInfo.displayItems.forEach(displayItem => totalPrice += parseFloat(displayItem.price));
    newTransactionInfo.totalPrice = totalPrice.toString();

    return newTransactionInfo;
  }

  function onGooglePayLoaded() {
    const paymentsClient = getGooglePaymentsClient();
    paymentsClient.isReadyToPay(getGoogleIsReadyToPayRequest())
      .then(function (response) {
        if (response.result) {
          addGooglePayButton();
        }
      })
      .catch(function (err) {
        console.error(err);
      });
  }

  // 구매 주체가 있을 때 버튼 생성
  function addGooglePayButton() {
    const paymentsClient = getGooglePaymentsClient();
    const button =
      paymentsClient.createButton({
        onClick: onGooglePaymentButtonClicked,
        allowedPaymentMethods: [baseCardPaymentMethod]
      });
    document.getElementById('container').appendChild(button);
  }

  // 아이템 트랜잭션 인포
  function getGoogleTransactionInfo() {
    return {
      displayItems: [
        {
          label: "Subtotal",
          type: "SUBTOTAL",
          price: "39000",
        },
        {
          label: "Tax",
          type: "TAX",
          price: "1000",
        }
      ],
      countryCode: 'KR',
      currencyCode: "KRW",
      totalPriceStatus: "FINAL",
      totalPrice: "40000",
      totalPriceLabel: "Total"
    };
  }

  // 배송비 옵션
  function getShippingCosts() {
    return {
      "shipping-001": "0",
      "shipping-002": "3000",
      "shipping-003": "10000"
    }
  }

  // 배송지역 옵션
  function getGoogleShippingAddressParameters() {
    return {
      allowedCountryCodes: ['KR'],
      phoneNumberRequired: false
    };
  }

  // 배송비 추가 옵션
  function getGoogleDefaultShippingOptions() {
    return {
      defaultSelectedOptionId: "shipping-001",
      shippingOptions: [
        {
          "id": "shipping-001",
          "label": "후순위 배송(무료)",
          "description": "Free Shipping delivered in 5 business days."
        },
        {
          "id": "shipping-002",
          "label": "일반 배송(+3,000)",
          "description": "Standard shipping delivered in 3 business days."
        },
        {
          "id": "shipping-003",
          "label": "특급 배송(+10,000)",
          "description": "Express shipping delivered in 1 business day."
        },
      ]
    };
  }

  // 배송지 오류
  function getGoogleUnserviceableAddressError() {
    return {
      reason: "SHIPPING_ADDRESS_UNSERVICEABLE",
      message: "Cannot ship to the selected address",
      intent: "SHIPPING_ADDRESS"
    };
  }

  function prefetchGooglePaymentData() {
    const paymentDataRequest = getGooglePaymentDataRequest();
    paymentDataRequest.transactionInfo = {
      totalPriceStatus: 'NOT_CURRENTLY_KNOWN',
      currencyCode: 'KRW'
    };
    const paymentsClient = getGooglePaymentsClient();
    paymentsClient.prefetchPaymentData(paymentDataRequest);
  }

  // 구글페이 버튼 클릭
  function onGooglePaymentButtonClicked() {
    const paymentDataRequest = getGooglePaymentDataRequest();
    paymentDataRequest.transactionInfo = getGoogleTransactionInfo();

    const paymentsClient = getGooglePaymentsClient();
    paymentsClient.loadPaymentData(paymentDataRequest);
  }

  function processPayment(paymentData) {
    return new Promise(function (resolve, reject) {
      setTimeout(function () {
        console.log(paymentData);
        paymentToken = paymentData.paymentMethodData.tokenizationData.token;

        resolve({});
      }, 3000);
    });
  }
</script>
```

* 주소 입력시 국가 코드가 `US`로 되어 있는 것을 `KR`으로 변경하여 적용.
* 배송비 옵션을 변경하면 그에 따라 가격이 유동적으로 변동되도록 적용됨.