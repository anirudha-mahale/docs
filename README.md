
# Setup your Integration
The GrassdoorFramework iOS SDK is compatible with apps supporting iOS 14 and above, and can be installed with CocoaPods as of now.

## Installation
The Grassdoor Framework iOS SDK is compatible with apps supporting iOS 14 and above, and can be installed with CocoaPods or by manually integrating the framework.

Install Manually
+ Download the `GrassdoorFramework.xcframework` from Github
+ Move the framework to the Project directory
+ Next, select your application project in the Project Navigator (blue project icon) to navigate to the target configuration window and select the application target under the "Targets" heading in the sidebar.
+ In the tab bar at the top of that window, open the "General" panel.
+ Click on the + button under the "Frameworks, Libraries and Embedded Content" section.
+ Select the `GrassdoorFramework.xcframework`.
+ If framework is not available than click on "Add Others" -> "Add Files", navigate to framework & select it.
+ Add the below dependencies to your project since those are required by the framework.
    ```bash
    Socket.IO-Client-Swift v15.2.0
    lottie-ios
    Segment-Appboy
    Segment-Firebase
    Rollbar v1.4.2
    Branch
    ```
    
## Initialization
The Methods made available by the GrassdoorFramework exposes features which are available in our Grassdoor App.

You will need to obtain the configure.plist file from the Grassdoor support in order to initialize the framework to add in the project. 

You can modify some of values in the plist file as per your configuration.

To get started, Initialize the GrassdoorManager in AppDelegate.
```swift
import Foundation
import GrassdoorFramework

class AppDelegate: NSObject, UIApplicationDelegate {
  func application(
    _ application: UIApplication,
    didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]? = nil
  ) -> Bool {
      GrassdoorManager.shared.configure(application: UIApplication.shared, launchOptions: [:])
    return true
  }
}
```
## Authentication
+ The class `SUser` is used to verify the user. 
+ User verification is required in order to order any products.
+ The above class exposes 2 methods to verify the user.

Request OTP
```swift
SUser().requestOtp(country:Country,telephone:String,via:Grassdoor.OTPRequestType,onCompletion:((_ onCompletion :Grassdoor.Operation?) -> Void)?)
```

#### Parameters
+ `country: Country` is an enum with available countries
+ `telephone: String` is your mobile number
+ `via: Grassdoor.OTPRequestType` is enum with available ways to validate the user, which are over text or call.

#### Response
+ response is a optional closure of type `((_ onCompletion :Grassdoor.Operation?) -> Void)?`
+ `Grassdoor.Operation` is an struct with several properties.

Varify OTP
```swift
SUser().verifyUser(country:Country,telephone:String,otp:String,onCompletion:((_ user :GrassdoorUserData?) -> Void)?)
```

#### Parameters
+ `country: Country` is an enum with available countries
+ `telephone: String` is your mobile number
+ `otp: String` is the otp that you received over your preferred way

#### Response
+ response is a optional closure of type `((_ user :GrassdoorUserData?) -> Void)?`
+ `GrassdoorUserData` is an struct with several user related properties.

## Set Delivery Details and Delivery Type
```swift
GrassdoorManager.shared.setUserDeliveryDetails(address: Grassdoor.Address)
```

#### Parameters
+ `Grassdoor.Address` holds the user delivery address.
  
  `init(zipcode: String, address: String, placeId: String, addressId: Int, latitude: Double, longitude: Double, state: String, city: String, country: String, zoneName: String)`

#### Note: 
+ To receive precise formatted address, either pass latitude & longitude or place_id(placeId from Google places API).
+ If both the params are passed then place_id will have higher priority to get formatted address
+ If only zipcode is passed, the function will only store the zipcode in localStorage & return default address

```swift
SShopData.setDelivery(type: Grassdoor.DeliveryType, onCompletion: ((Grassdoor.Operation?) -> Void)?)
```

#### Parameters
+ `Grassdoor.DeliveryType` is an enum representing the delivery types available which are as below.
  
    1 .asap - The product is available in your area & will be delivered within 40 minutes

    2 .scheduled - You can select the time slots 
## Show the Cart
* Simply use this class to display the cart.
* The view has inbuild functionality for addition/deletion of products to/from cart.

```swiftUI
SCartView
```

* For UIKit simply wrap the above class in `UIHostingController`
```swiftUI
UIHostingController(rootView: SCartView())
```


## Add to Cart
Product or Bundle should be filtered from product list API to obtain the product with {type: 'bundle' or 'product'} and mappingId :product id from product list API

```swift
SCart.shared.add(product: Product, onCompletion: ((Grassdoor.Operation?) -> Void)?)
```

#### Parameter
+ `Product` holds the product data
  
  `init(name: String, slug: String)`

## Remove from Cart
Product or Bundle should be filtered from product list API to obtain the product with {type: 'bundle' or 'product'} and mappingId :product id from product list API

```swift
SCart.shared.remove(product: Product, onCompletion: ((Grassdoor.Operation?) -> Void)?)
```

#### Parameter
+ `Product` holds the product data
  
  `init(name: String, slug: String)`
## Check User's login status
This will check if User has logged in or not.

`GrassdoorManager.shared.checkLoggedInUser()`

#### Response
```
true //if user has logged in

false //if user has not logged in
```
## Save User's info
This will save user's info to the disk which can be used autofill the details in checkout.
```swift
GrassdoorManager.shared.setCurrentUser(data:GrassdoorUserData,
                                       onCompletion:((_ operation : GrassdoorUserData?) -> Void)?)
```

## Get User's Order History
Take user to order history place only when the user is logged in, there is another API to check the same `Check Users login status`

This API will return a promise which will contain list of User's order history.

Order history API includes all order by a user, covering the ongoing orders, and user needs to be routed the user to `https://<domain>/confirmation/<order-id>` to see full order details 


```swift
GrassdoorManager.shared.getUsersOrderHistory(offset: Int, items: Int = 5, onComplete: @escaping (_: Result<AllOrders, APIError>) -> Void)
```

#### Parameters
+ `offset: Int` Default offset is 0
+ `items: Int` Number of items per page

#### Response
+ response is a escaping closure of type `(_: Result<AllOrders, APIError>) -> Void)`
+ `AllOrders` is an struct with orders & count of all orders.


## Category/Product Listing
+ The class `SShopData` enables you to get the category/products list & details of single product. 

Category/Product Listing
```swift
SShopData.fetchMenu(deliveryType: DeliveryType, filters: [FilterList]?, onComplete: @escaping (_: Result<[Category], APIError> ) -> Void)
```

#### Parameters
+ `deliveryType: DeliveryType` is an enum with 2 values either asap, scheduled
+ `filters: [FilterList]?` is an optional, you can use filter to sort and filter the result.

#### Response
+ response is a escaping closure of type `(_: Result<[Category], APIError> ) -> Void`
+ `Category` is an struct with several properties which also contains the list of products.

```
Note: You have to sent the user delivery location before using the category/product listing.
```

Product Details

+ There are 2 methods to get product details.

```swift
SShopData.getProductDetail(slug: String, type: ProductType, success: @escaping (_: Product) -> Void)
SShopData.getProductDetail(id: String, type: ProductType, success: @escaping (_: Product) -> Void)
```

#### Parameters
+ `slug` 
+ `id` id of the product you want to get details of
+ `type: ProductType` the type can be single product or bundle.

#### Response
+ response is a escaping closure of type `(_: Product) -> Void)`
+ `Product` is an struct with several properties which also contains the list of similar products.

## Get Product Count Based on ID
```swift
SCart.shared.getProductCount(id: Int, type: Grassdoor.ProductType) -> Int
```


#### Parameters

+ id Int - Mapping ID
+ type Grassdoor.ProductType - Product type
#### Response
```swift
5 //current quantity of the product in the cart
```
## Get Total Cart Count
This will return total product count in the cart.
```swift
SCart.shared.getCartCount() -> Int
```

#### Response
```
5 //Total product count in the cart
```
## Buy Now
+ Since checkout process involves complex logic and handling of data flow we provide you out of the box User Interface.
* Use `SCheckoutView` class to show the checkout UI
* For UIKit use `UIHostingController(rootView: SCheckoutView())`
## Set your custom Fonts

To match with your application style we provide you an interface to set fonts.

If you don't provide with font, Default we use is `ProximaNova`

Installation
+ Drag & Drop fonts in project structure and add it to your target.
+ Use the below method to set the fonts with respect to their font weights

```swift
GrassdoorManager.shared.setCustomFonts(regular: String?, semiBold: String?, bold: String?, extraBold: String?, italic: String?)
```

#### Parameters
+ `regular: String?` filename of regular font
+ `semiBold: String?` filename of semiBold font
+ `bold: String?` filename of bold font
+ `extraBold: String?` filename of extraBold font
+ `italic: String?` filename of italic font

## Configuration Variables

To Initialize the framework you will need to add the following Configuration.plist file to your project structure

Configuration file

The Grassdoor SDK supports a number of feature which allow for customizing certain UI aspects and behavior.

 + API_BASE_URL: Base url of Grassdoor backend
 + API_VERSION: Route version for the Grassdoor backend
 + API_KEY: Secret key to validate the Grassdoor backend access
 + APPLICATION_NAME: Your application name
 + APPSTORE_ID: The app is on the Apple store
 + APP_BACKGROUND_COLOR: Background color of the app (Not being used)
 + APP_DEFAULT_COLOR: Specify the hex code of color to be used as theme and primary color
 + APP_ENV:
 + APP_ENV_NAME: Environment name like Production, Staging & etch
 + APP_SHORT_NAME:
 + BRANCH_KEY: The branch key is required to initialise the branch sdk
 + BRANCH_LINK_DOMAIN: Redirect link for the branch sdk
 + BRANCH_LINK_DOMAIN_ALTERNATE: Alternate redirect link for the branch sdk
 + BRAZE_API_KEY: Required by AppBoy framework
 + BRAZE_END_POINT: Required by AppBoy framework
 + CONTACT_EMAIL: Contact email of the organisation
 + FIREBASE_PLIST_FILENAME: Filename of Google plist file
 + HIDE_FILTER_AND_SORT: Flag indicating to toggle visibility filter & sort functionality
 + HIDE_PRODUCT_PROPERTIES: Flag indicating to toggle visibility Product properties
 + HIDE_PRODUCT_WEIGHT: Flag indicating to toggle visibility the Product weight 
 + HIDE_TOP_BRAND_SECTION: Flag indicating to toggle visibility the Popular Brands on Home
 + HIDE_TOP_BRAND_TAB: Flag indicating to toggle visibility Top Brand in UITabBar
 + HOST_URL: Frontend url of the organisation
 + IS_GRASSDOOR: Flag indicating if the application is grassdoor or not
 + LAUNCHER_NAME: 
 + MIXPANEL_ID: Mixpanel token to initialise the Mixpanel
 + ONESIGNAL_APP_ID: OneSignal token to initialise the OneSignal
 + ROLLBAR_KEY: Rollbar token to initialise the Rollbar Tracker
 + SEGMENT_API_KEY: Segment token to initialise the Sigment Analytics
 + WEBSITE_URL: Organisation frontend url
 + ZENDESK_ACCOUNT_KEY: Zendesk Account key
 + ZENDESK_APP_ID: Zendesk App Id
 + ZENDESK_CLIENT_ID: Zendesk Client Id
 + ZENDESK_URL: Zendesk frontend url

