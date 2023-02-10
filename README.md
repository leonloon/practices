# Code Standard

## Naming Convention

`camelCase` for variables

```js
/* Dos */
// camelCase
let firstName = "John";
let lastName = "Doe";

/* Don'ts */
// snake_case should not be allowed
let first_name = "John";
let last_name = "Doe";

// lowercase should not be allowed
let firstname = "John";
let lastname = "Doe";

// ChangeSettlement.js
let nonmbbcustomerenquiryresponse = {};
let consolidateaccount = {};
```

`UPPERCASE` for global constant

```js
// Environment
const ENV = "development";

// Color
const GREY_BACKGROUND = "#E4E4E9";
```

`UPPERCASE` for constant such as flags

```js
// Inventory flags
const OUT_OF_STOCK = 0;
const IN_STOCK = 1;

// Status flags
const APPROVED = "1";
const PENDING = "2";
const REJECTED = "3";
const DELETED = "4";
const CHECK_APPROVED = "5";
const CHECK_REJECTED = "6";
```

## Make use of NAME to indicate its usage or value

```js
// AccountSelection.js
if (
  this.props.navigation_flow == "OtherBankRegister" ||
  this.props.navigation_flow === "UpdateOutlet" ||
  this.props.navigation_flow === "EDIT_OUTLET" ||
  this.props.user?.meService?.data?.bank_code !== "588734"
) {
  // do something
}
```

If I am a new developer who just got onboarded onto this project and been assigned to build new feature or fix defect in `AccountSelection.js`, how am I going to understand the conditions here clearly?

What does `bank_code !== "588734"` mean?
What does `"588734"` represent?

Humans are generally bad at reading code with bunch of conditions stacked together such as multiple lines of operator (`&&`,`||`) which consist of (`===`,`!==`,`> 0`, `>= 1`, `< 50`).

Better way of doing it:

```js
if (isOtherBanks) {
  // do something
}

function isOtherBanks() {
  // check conditions
  return bool;
}
```

```js
// AddProduct.js
if (
  (this.props.navigation_flow != "viewProduct" &&
    this.props.current.image_path === "") ||
  this.props.current.product.name === "" ||
  this.props.current.product.desc === "" ||
  this.props.current.product.amount === "" ||
  this.props.current.product.amount === "0.00" ||
  this.props.current.product.weight === "" ||
  this.props.current.product.weight === "0.0" ||
  this.props.current.product.category_name === "" ||
  this.props.current.product.size1 === "" ||
  this.props.current.product.size2 === "" ||
  this.props.current.product.size3 === ""
) {
  // do something
}
```

```js
let product = {
  name: "iPhone",
  price: 0,
};

if (isValidated(product)) {
  // do something
}

function isValidated(product) {
  if (product?.name === "") {
    // name cannot be empty
    return false;
  }

  if (product?.price <= 0) {
    // price cannot be less than or equal to 0
    return false;
  }

  return true;
}
```

Do you know what does `mkp_status` stand for? What are all these integer representing here? To be honest, I don't.

```js
// ProductAdded.js
if (this.state.mkp_status === 0) {
  this.props.navigation.push("SSLSetupBasicInfo"); //TODO:: Navigate to Orders page
} else if (this.state.mkp_status === 1) {
  this.goToProducts();
} else if (this.state.mkp_status === 2) {
  const resetAction = StackActions.reset({
    index: 0,
    actions: [NavigationActions.navigate({ routeName: "Calculator" })],
  });
  this.props.navigation.dispatch(resetAction);
} else if (this.state.mkp_status === 3 || this.state.mkp_status === 4) {
  this.sendEmail();
} else if (this.state.mkp_status === "Default") {
  this.props.getCategoryList(this.props.user.access_token);
  if (this.props.ssl.productUpdated.data.status === 2) {
    showMessage({
      message:
        "Successfully updated. Head to draft to re-submit the product(s) for approval.",
      type: "success",
    });
    this.props.navigation.navigate("PendingProductList");
  } else {
    this.goToProducts();
  }
  let result = this.getUnderReviewProducts();
  let underReviewProducts = result?.categoryProducts;
  let productsLength = result?.reviewedProducts?.length;
  this.props.navigation.navigate("PendingProductList", {
    underReviewProducts: underReviewProducts,
    productsLength: productsLength,
    isOnboarding: this.props.isOnboarding,
  });
} else {
  this.goToProducts();
}
```

Better way of doing it:

```js
const UNREGISTERED = 0;
const APPROVED = 1;
const APPLIED = 2;
const REJECTED = 3;
const REJECTED_SPECIAL = 4;
const ACTIVE = 8;

if (mkpStatus === UNREGISTERED) {
  return; // do something

if (mkpStatus === APPROVED) {
  return; // do something
}

if (mkpStatus === APPLIED) {
  return; // do something
}

if (mkpStatus === REJECTED) {
  return; // do something
}

if (mkpStatus === REJECTED_SPECIAL) {
  return; // do something
}

if (mkpStatus === ACTIVE) {
  return; // do something
}
```

Bad example of comparing `string` value in condition check:

```js
user.userType === "cashier";
user.userType === "merchant";
user.userType === "corporate";

// Mistyped
if (user.userType === "cahsier") {
  // do something
}

// Extra "s"
if (user.userType === "merchants") {
  // do something
}

// Uppercase lowercase typo
if (user.userType === "Corporate") {
  // do something
}
```

This is very prone to typo error, and it will escape the checking from text editor since every `string` value is a legit value. What if there is a `userType` called `entrepreneur` or `restaurateur`, how likely you think someone will mistype it in one of their condition checks?

To prevent this we should utilize `const`:

```js
const CASHIER = "cashier";
const MERCHANT = "merchant";
const CORPORATE = "corporate";

if (user.userType === CASHIER) {
  // do something
}
```

## Strictly No Inline Styling

Look at how many lines of code to achieve a simple cancel button?

Answer: <b>25 lines !!</b>

```jsx
// ChangeAccount.js
<View
  style={{
    position: "absolute",
    bottom: 0,
    width: "100%",
    zIndex: 2,
    backgroundColor: "#FFFFFF",
  }}
>
  <View
    style={{
      width: "100%",
      height: 40,
      backgroundColor: "#FFFFFF",
      flexDirection: "row",
      borderColor: "lightgray",
      borderWidth: 1,
    }}
  >
    <View style={{ flex: 1, alignContent: "flex-start" }}>
      <TouchableWithoutFeedback onPress={this.togglePicker}>
        <Text style={{ textAlign: "left", padding: 10 }}>Cancel</Text>
      </TouchableWithoutFeedback>
    </View>
  </View>
</View> // total 25 lines of code
```

Ask yourselves how I can achieve the same UI result with minimum lines of code.

```jsx
<View style={styles.container}>
  <View style={styles.bottomContainer}>
    <View style={styles.buttonContainer}>
      <TouchableWithoutFeedback onPress={this.togglePicker}>
        <Text style={styles.cancelText}>Cancel</Text>
      </TouchableWithoutFeedback>
    </View>
  </View>
</View> // total 9 lines of code
```

Or even better if you can figure out how to combine the styling of `bottomContainer` and `buttonContainer` together, which is in most cases highly achievable.

```jsx
<View style={styles.container}>
  <View style={styles.buttonContainer}>
    <TouchableWithoutFeedback onPress={this.togglePicker}>
      <Text style={styles.cancelText}>Cancel</Text>
    </TouchableWithoutFeedback>
  </View>
</View> // total 7 lines of code
```

## Do not commit COMMENTED code

A maintainable code base has no place for commented code. The code has to be either useful in the codebase which shouldn't be commented out, or useless (which shouldn't be committed to the repo at all)

```js
// EditOutlet.js
const onOutletDelete = () => {
  console.log("hukjbjhjhghhfghgh");

  // let parameters = {
  //     merchant_id: navigation.state.params.merchant_id,
  //     outlet_id: navigation.state.params.merchant_id,
  // };

  // Alert.alert(
  //     'Are you sure you want to delete this outlet?',
  //     'You can’t undo this action. If you’re sure, tap on ‘Delete’ to proceed.',
  //     [
  //         {
  //             text: 'CANCEL',
  //             onPress: () => console.log('Cancel Pressed'),
  //             style: 'default',
  //         },
  //         {
  //             text: 'DELETE',
  //             onPress: () => dispatch(removeOutletCorporate(parameters, access_token)),
  //             style: 'destructive',
  //         },
  //     ],
  // );
};
```

Exception would be the code is there for upcoming release.

## No color hex code

```js
// ConfirmCreate.js
container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#F5FCFF',
    width: '100%',
}

// SetupOutlet.js
autogenerateText: {
    height: 40,
    fontFamily: 'Lato-Bold',
    paddingVertical: 10,
    paddingLeft: 20,
    color: '#4C8BFB',
    fontSize: 16,
    width: 140,
}
```

Do you know what are the colors of `#F5FCFF` and `#4C8BFB`? Sorry I don't. I am hex code color blind!

Typing out character by charater for the hex code is prone to typo error.

What if UIUX design team came out with a new design and wanted to change to a darker hue for background color from `#F5FCFF` to `#D4E3EA`?

Are you going to change search files by files to make changes, or are you confident enough to do a global search and text replace without any consequences?

Therefore, please utlize global color constant in `color.js`, text editor is powerful enough to give you suggestion and perform auto-complete.

```js
const BLACK = '#000000';
const DARK_BLUE = '#222A3A';
const DARK_GREY = '#7D7D7D';
const GREY = '#ABABB6';
const LIGHT_GREY = '#E4E4E4';
const WHITE = '#FFFFFF';
const BLUE = '#4C8BFB';
const YELLOW = '#FFC83D';
const PINK = '#FFD0CA';
const REDDISH_RED = '#D72C15';

// index.js
container {
    backgroundColor: LIGHT_GREY,
}
```

## Optional Chaining

I would recommend you guys to abuse the usage of optional chaining.

Look at the case study below:

```jsx
{
  merchant.outlets.orders.options.price && (
    <View>
      <Text>Price: {merchant.outlets.orders.options.price}</Text>
    </View>
  );
}
```

What if `merchant` has no outlet and returns an empty array?
What if `outlet` just started operating without having its first order hence empty `orders` array?
What if `orders` has no `option`?
What if `price` is `null`?

All these unforseen scenarios would have resulted with app crash!

As a frontend developer, we have been trained to assume that <b>API reliability cannot be trusted</b>. Hence, we need to create a safety net for ourselves to at least prevent app crash from happening.

```jsx
{
  // Use double !! instead, because if merchant is null, your app will still crash!
  !!merchant?.outlets?.orders?.options?.price && (
    <View>
      <Text>Price: {merchant?.outlets?.orders?.options?.price}</Text>
    </View>
  );
}

// Price: undefined
```

Price will still be displayed as `undefined` but at least developer and tester would be able to pinpoint quickly that faulty API response is the root cause of this, or certain logics are not being handled by frontend developer.

Without optional chaining, tester may need to retest few more times and possibly need to perform screen capture in order to convince developers about their findings. Not efficient!

## View rendering structure

Try not to nest view more than 3 layers for better readability. If it grows more than 3 layers it means you need to find ways to break down to smaller blocks of UI code to make it readable and maintainable.

```js
<View>
  <View>
    <View>
      <Text>Maximum 3 layers</Text>
    </View>
  </View>
</View>
```

## Use `PropTypes`

```js
Product.propTypes = {
  id: PropTypes.string, // this prop should be string
  name: PropTypes.string, // this prop should be string
  imagePath: PropTypes.string, // this prop should be string
  price: PropTypes.string, // this prop should be string
  optionGroups: PropTypes.array // this prop should be array
  itemCount: PropTypes.number // this prop should be number
}

// It can be added with isRequired
Product.propTypes = {
  id: PropTypes.string.isRequired,
  name: PropTypes.string.isRequired,
  imagePath: PropTypes.string,
  price: PropTypes.string.isRequired,
  optionGroups: PropTypes.array
  itemCount: PropTypes.number.isRequired,
}

```

By looking at the `propTypes`, now you know `imagePath` is optional where product image is not mandatory, and you know `price` is being handled as `string` as opposed to `integer`. Without this information, you might overlook and think that `price` is stored as `integer` and handle it like an `integer`, it may cause error.

## No Nested IF-ELSE Statements

```js
// App.js
if (remoteMessage.data.hasOwnProperty("order_id")) {
  if (token) {
    NavigationService.navigate("OrderDetails", remoteMessage.data.order_id);
  }
} else {
  if (remoteMessage.data.hasOwnProperty("type")) {
    if (remoteMessage.data.type == "s2l") {
    } else if (remoteMessage.data.type == "SSL") {
      if (remoteMessage.data.status == 1) {
        if (token) {
          NavigationService.navigate("S2LApplicationApprovedAll");
        }
      } else if (remoteMessage.data.status == 2) {
        if (token) {
          NavigationService.navigate("S2LApplicationApproved");
        }
      } else if (remoteMessage.data.status == 3) {
        if (token) {
          NavigationService.navigate("S2LApplicationRejected");
        }
      } else if (remoteMessage.data.status == 4) {
        if (token) {
          NavigationService.navigate("S2LApplicationRejectedAll");
        }
      } else {
        //calculator
      }
    } else if (remoteMessage.data.type == "Profile") {
      if (remoteMessage.data.status == 1) {
        if (token) {
          NavigationService.navigate("S2LChangeRequestApproved");
        }
      } else if (remoteMessage.data.status == 2) {
        if (token) {
          NavigationService.navigate("S2LChangeRequestRejected");
        }
      } else {
        this.nav();
      }
    } else if (remoteMessage.data.type == "Product") {
      if (remoteMessage.data.status == 1) {
        if (token) {
          NavigationService.navigate("S2LProductListApproved");
        }
      } else if (remoteMessage.data.status == 2) {
        if (token) {
          NavigationService.navigate("S2LProductListRejected");
        }
      } else {
        this.nav();
      }
    } else if (
      remoteMessage.data.type == "delivery" ||
      remoteMessage.data.type == "tnc" ||
      remoteMessage.data.type == "delivery_tnc"
    ) {
      if (token) {
        if (remoteMessage.data.type == "tnc") {
          if (remoteMessage.data.status == 7) {
            NavigationService.navigate("S2LChangeRequestApproved");
          } else {
            NavigationService.navigate(
              "S2LChangeRequestRejected",
              remoteMessage.data.type
            );
          }
        } else if (remoteMessage.data.type == "delivery") {
          if (remoteMessage.data.status == 5) {
            NavigationService.navigate("S2LChangeRequestApproved");
          } else {
            NavigationService.navigate(
              "S2LChangeRequestRejected",
              remoteMessage.data.type
            );
          }
        } else {
          if (remoteMessage.data.status == 9) {
            NavigationService.navigate("S2LChangeRequestApproved");
          } else {
            NavigationService.navigate(
              "S2LChangeRequestRejected",
              remoteMessage.data.type
            );
          }
        }
      }
      //navigates to sama settings review
    }
  } else {
    // calculator
    if (
      notificationStatus == SSTStatus.APPROVED ||
      notificationStatus == SSTStatus.REJECTED ||
      notificationStatus == SSTStatus.ENABLED ||
      notificationStatus == SSTStatus.DISABLED
    ) {
      store.dispatch(notificationSSTStatusStoreSucess(notificationStatus));
      NavigationService.navigate("SSTChargesApprovedInfo");
    } else {
      if (token) {
        NavigationService.navigate("TransactionList");
      }
    }
  }
}
```

<b>Readability is bad</b>, you are likely to lose track of the conditions after a couple of IF-ELSE Statements!

```js
// CreateOutlet.js
if (outletdetails.outlet_name != "") {
  if (
    outletdetails.outlet_name?.length >= 6 &&
    outletdetails.outlet_name.length <= 25
  ) {
    if (outletdetails.outlet_contactno != "") {
      if (
        outletdetails.outlet_contactno?.length >= 9 &&
        outletdetails.outlet_contactno?.length <= 10
      ) {
        if (outletdetails?.outlet_contact_person_name != "") {
          if (outletdetails.outlet_email_address != "") {
            let regexp = /^[a-zA-Z0-9-.]+@[a-zA-Z0-9]+\.[A-Za-z]+$/;
            if (regexp.test(outletdetails?.outlet_email_address)) {
              if (
                outletdetails.outlet_desc !== "" &&
                outletdetails.outlet_desc !== null
              ) {
                if (
                  store.getState().user.meService?.data?.settlement_mode ==
                    false &&
                  (accountselection.account_no == "" ||
                    accountselection.account_no == null)
                ) {
                  showMessage({
                    message: ErrorJSON.error_codes.QR090[language].replace(
                      "<field name>",
                      "Account No."
                    ),
                    type: "danger",
                    titleStyle: { textAlign: "center" },
                  });
                } else {
                  let parametersdata = {};
                  parametersdata = {
                    user_name: userName,
                  };
                  dispatch(preRequestTacAPICALL(parametersdata));
                  dispatch(navigationFlowChanged("EDIT_OUTLET"));
                }
              } else {
                showMessage({
                  message:
                    LanguageJSON.Create_account.businessDesc_error2[language],
                  type: "danger",
                  titleStyle: { textAlign: "center" },
                });
              }
            } else {
              showMessage({
                message: LanguageJSON.Manage_Profile.invalidemail[language],
                type: "danger",
                titleStyle: { textAlign: "center" },
              });
            }
          } else {
            showMessage({
              message: ErrorJSON.error_codes.QR090[language].replace(
                "<field name>",
                LanguageJSON.details_view.your_business_email_address[language]
              ),
              type: "danger",
              titleStyle: { textAlign: "center" },
            });
          }
        } else {
          showMessage({
            message: ErrorJSON.error_codes.QR090[language].replace(
              "<field name>",
              LanguageJSON.enter_title_contactperson_name[language]
            ),
            type: "danger",
            titleStyle: { textAlign: "center" },
          });
        }
      } else {
        showMessage({
          message: LanguageJSON.register_otherBank.error_MobileNo[language],
          type: "danger",
          titleStyle: { textAlign: "center" },
        });
      }
    } else {
      showMessage({
        message: ErrorJSON.error_codes.QR090[language].replace(
          "<field name>",
          LanguageJSON.details_view.your_business_phone_no_small[language]
        ),
        type: "danger",
        titleStyle: { textAlign: "center" },
      });
    }
  } else {
    showMessage({
      message: LanguageJSON.Create_account.businessName_error[language],
      type: "danger",
      titleStyle: { textAlign: "center" },
    });
  }
} else {
  showMessage({
    message: ErrorJSON.error_codes.QR090[language].replace(
      "<field name>",
      LanguageJSON.outlet_name[language]
    ),
    type: "danger",
    titleStyle: { textAlign: "center" },
  });
}
```

## Code Reusability aka DRY (Don't Repeat Yourself) Concept

What problem do you see in the code below?

```jsx
// SSTChargesApprovedInfo.js
{
  status == SSTStatus.APPROVED && (
    <TouchableOpacity
      onPress={() => {
        NavigationService.navigate("BusinessInfo", {
          isPushNotification: true,
        });
      }}
      style={{
        ...styles.buttonContainer,
        position: "absolute",
        bottom: 0,
        // marginTop: Platform.OS == 'android' ? height * 0.05 : height * 0.2,
      }}
    >
      <Text style={styles.button}>
        {
          LanguageJSON.SST_Charges_application_btn_text[
            store.getState().language.language
          ]
        }
      </Text>
    </TouchableOpacity>
  );
}

{
  status == SSTStatus.REJECTED && (
    <TouchableOpacity
      onPress={() => {
        NavigationService.navigate("BusinessInfo", {
          isPushNotification: true,
        });
      }}
      style={{
        ...styles.buttonContainer,
        position: "absolute",
        bottom: 0,
        // marginTop:
        //     Platform.OS == 'android' ? height * 0.23 : height * 0.34,
      }}
    >
      <Text style={styles.button}>
        {
          LanguageJSON.SST_Charges_application_rejected_btn_text[
            store.getState().language.language
          ]
        }
      </Text>
    </TouchableOpacity>
  );
}

{
  status == SSTStatus.ENABLED && (
    <TouchableOpacity
      onPress={() => {
        NavigationService.navigate("BusinessInfo", {
          isPushNotification: true,
        });
      }}
      style={{
        ...styles.buttonContainer,
        position: "absolute",
        bottom: 0,
      }}
    >
      <Text style={styles.button}>
        {
          LanguageJSON.SST_Charges_application_enabled_btn_text[
            store.getState().language.language
          ]
        }
      </Text>
    </TouchableOpacity>
  );
}

{
  status == SSTStatus.DISABLED && (
    <TouchableOpacity
      onPress={() => {
        NavigationService.navigate("BusinessInfo", {
          isPushNotification: true,
        });
      }}
      style={{
        ...styles.buttonContainer,
        position: "absolute",
        bottom: 0,
        // marginTop: Platform.OS == 'android' ? height * 0.34 : height * 0.42,
      }}
    >
      <Text style={styles.button}>
        {
          LanguageJSON.SST_Charges_application_disabled_btn_text[
            store.getState().language.language
          ]
        }
      </Text>
    </TouchableOpacity>
  );
}
```

There are total 100+ lines of code just to display exactly the same button with different set of text.

What if business team or UIUX design team came out with different ideas of styling the button or structure, are you gonna spend your precious hours to change these components <b>FOUR</b> times here? <b>HELL NO! Your precious time deserves to do better things, not this.</b>

How can you do it better?

```jsx
function showBtnText(status) {
  switch (status) {
    case SSTStatus.APPROVED:
      return LanguageJSON.SST_Charges_application_btn_text[
        store.getState().language.language
      ];
    case SSTStatus.REJECTED:
      return LanguageJSON.SST_Charges_application_rejected_btn_text[
        store.getState().language.language
      ];
    case SSTStatus.ENABLED:
      return LanguageJSON.SST_Charges_application_enabled_btn_text[
        store.getState().language.language
      ];
    case SSTStatus.DISABLED:
      return LanguageJSON.SST_Charges_application_disabled_btn_text[
        store.getState().language.language
      ];
    default:
      break;
  }
}

{
  !!status && (
    <TouchableOpacity
      onPress={() => {
        NavigationService.navigate("BusinessInfo", {
          isPushNotification: true,
        });
      }}
      style={styles.button}
    >
      <Text style={styles.buttonText}>{showBtnText(status)}</Text>
    </TouchableOpacity>
  );
}
```

You can cut it down to less than 40 lines of code in a more maintainable way.

Even better if you can transform it into a `json` like below:

```jsx
let json = {
  [SSTStatus.APPROVED]: LanguageJSON.SST_Charges_application_btn_text,
  [SSTStatus.REJECTED]: LanguageJSON.SST_Charges_application_rejected_btn_text,
  [SSTStatus.ENABLED]: LanguageJSON.SST_Charges_application_enabled_btn_text,
  [SSTStatus.DISABLED]: LanguageJSON.SST_Charges_application_disabled_btn_text,
};

{
  !!status && (
    <TouchableOpacity
      onPress={() => {
        NavigationService.navigate("BusinessInfo", {
          isPushNotification: true,
        });
      }}
      style={styles.button}
    >
      <Text style={styles.buttonText}>{json.status}</Text>
    </TouchableOpacity>
  );
}
```

From 100 lines of code down to merely 20 lines of code.

## Make use of mock API data to test or debug

In some cases, bugs are resulted with a specific set of data value as input. But to get those data from an API might need to perform a set of long and time consuming end-to-end flow.

### Case #1

Some bugs only happen when a user freshly registered a new account. However, the process of registering a new account may need to insert 20 input fields. In order to test repetitively without wasting too much time on registering account, developer should obtain the valid json data from initial signup and store it as a mock API data somewhere in the code base. By changing the values in json you can essentially cover lots of test scenarios without the need to register a new account again and again.

Imagine if new user end-to-end signup process may have up to 20 scenarios to cover, without mock API data, you may need to register new accounts at least 20 times!

Therefore, a better way to handle such cases is to make use of mock API to perform a repetitive test in order to cover more edge case scenarios rather than just focusing on happy path.

## Do not commit `console.log()`

Do not commit `console.log()` at all time unless you are confident that your log is wonderfully crafted and it provides more peace than pain for your developers down the road.

```
time now: 1675837678592
OrderList.js:464 ONSCREEN
OrderList.js:156 time now: 1675837679612
OrderList.js:464 ONSCREEN
OrderList.js:156 time now: 1675837680627
OrderList.js:464 ONSCREEN
OrderList.js:156 time now: 1675837681645
OrderList.js:464 ONSCREEN
OrderList.js:156 time now: 1675837682644
OrderList.js:464 ONSCREEN
OrderList.js:156 time now: 1675837683656
OrderList.js:464 ONSCREEN
OrderList.js:156 time now: 1675837684674
OrderList.js:464 ONSCREEN
OrderList.js:156 time now: 1675837685693
userReducer.js:35 in user reducer SELECTED_MENUITEM
loginReducer.js:4 LOGIN REDUCER : 10
LoaderReducer.js:8 LOADER REDUCER : SELECTED_MENUITEM
cashierReducer.js:21 CASHIER ACTION TYPE =>  SELECTED_MENUITEM
cashierMultipleLoginsReducer.js:9 CASHIER ACTION TYPE =>  SELECTED_MENUITEM
userLoginsReducer.js:8 CASHIER ACTION TYPE =>  SELECTED_MENUITEM
LoaderReducer.js:8 LOADER REDUCER : SELECTED_MENUITEM
settingsInfoReducer.js:10 in settingsInfoReducer reducer SELECTED_MENUITEM
forgotPinReducer.js:9 in Forgot pin reducer SELECTED_MENUITEM
SSLStatusReducer.js:9 SSL Status Reducer : =>  SELECTED_MENUITEM
promotionReducer.js:13 PROMOTION ACTION TYPE =>  SELECTED_MENUITEM
promotionReviewReducer.js:24 PROMOTION ACTION TYPE =>  SELECTED_MENUITEM
CorporateAccountReducer.js:9 in CorporateAccountReducer SELECTED_MENUITEM
SideMenu.js:807 Current State DATA:  {language: {…}, categories: {…}, user: {…}, login: {…}, loader: {…}, …}
OrderList.js:1210 TRANSACTION STATE:  {language: {…}, categories: {…}, user: {…}, login: {…}, loader: {…}, …}
userReducer.js:35 in user reducer CHANGE_STOREMANAGEMENT_SIDEMENU_INDICATOR
loginReducer.js:4 LOGIN REDUCER : false
LoaderReducer.js:8 LOADER REDUCER : CHANGE_STOREMANAGEMENT_SIDEMENU_INDICATOR
cashierReducer.js:21 CASHIER ACTION TYPE =>  CHANGE_STOREMANAGEMENT_SIDEMENU_INDICATOR
cashierMultipleLoginsReducer.js:9 CASHIER ACTION TYPE =>  CHANGE_STOREMANAGEMENT_SIDEMENU_INDICATOR
userLoginsReducer.js:8 CASHIER ACTION TYPE =>  CHANGE_STOREMANAGEMENT_SIDEMENU_INDICATOR
LoaderReducer.js:8 LOADER REDUCER : CHANGE_STOREMANAGEMENT_SIDEMENU_INDICATOR
settingsInfoReducer.js:10 in settingsInfoReducer reducer CHANGE_STOREMANAGEMENT_SIDEMENU_INDICATOR
forgotPinReducer.js:9 in Forgot pin reducer CHANGE_STOREMANAGEMENT_SIDEMENU_INDICATOR
SSLStatusReducer.js:9 SSL Status Reducer : =>  CHANGE_STOREMANAGEMENT_SIDEMENU_INDICATOR
promotionReducer.js:13 PROMOTION ACTION TYPE =>  CHANGE_STOREMANAGEMENT_SIDEMENU_INDICATOR
promotionReviewReducer.js:24 PROMOTION ACTION TYPE =>  CHANGE_STOREMANAGEMENT_SIDEMENU_INDICATOR
CorporateAccountReducer.js:9 in CorporateAccountReducer CHANGE_STOREMANAGEMENT_SIDEMENU_INDICATOR
SideMenu.js:807 Current State DATA:  {language: {…}, categories: {…}, user: {…}, login: {…}, loader: {…}, …}
OrderList.js:1210 TRANSACTION STATE:  {language: {…}, categories: {…}, user: {…}, login: {…}, loader: {…}, …}
CategoryList.js:393 CURRENT USER: merchant false
```

These are some funny `console.log()` out there in the codebase.

```js
// ChangeSettlement.js
console.log("**************************************");
console.log("FLOWWWWWWWWWWWWWWW " + this.props.navigation_flow);

// PromotionList.js
console.log("view more promo...........");

// EditOutlet.js
const onOutletDelete = () => {
  console.log("hukjbjhjhghhfghgh");
};

// SSLOrderSearch.js
console.log("DID FOCUSED=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-");

// OrderList.js
console.log("+_+_+_+_+_CALLING SWITCH");
var flag = this.props.transaction?.storeSwitch;

// AccountSelection.js
if (this.props.current.profile_image_response.status == "QR000") {
  console.log("CRASHEDDDDD 1");
  if (this.props.registration.photoupload.logo) {
    console.log("CRASHEDDDDD 2");
  }
}

// OutletReview.js
console.log("111");
this.props.showloader(true);

// OutletReview.js
console.log("222");
console.log("outlet changed");

// PinScreen.js
console.log("input PIN: " + inputtedPin);
let user_name = this.props.login.userName;
let user_type = this.props.user.userType;
let key = "";
console.log("user typeeeeeeeeeee " + user_type);
let user_pin = inputtedPin;
let navigationFrom = this.props.navigation.state.params;
console.log("user typeeeeeeeeeee page name " + navigationFrom);
```

`console.log()` is very powerful if it throws useful errors especially calculation heavy functions.

I agree `console.log()` is helpful when you are developing code and hitting strange errors. You may have 1000 lines of `console.log()` inside your own stash, I don't really care much as long as you do not commit those code when raising a PR.

## Check your code carefully before committing to repo

I have seen some developers they will just stage all the changes and commit it to repo and regard it as job done. <b>NO! Don't be lazy! You shouldn't just stage ALL and commit.</b> You need to read carefully file by file and line by line before staging it to ensure that those are the code you gonna commit.

If you do not have good GUI client tool to do that, I would suggest <u>GitKraken</u> or <u>Fork</u>.
