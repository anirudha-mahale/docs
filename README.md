
# Setup your Integration

The GrassdoorFramework iOS SDK is compatible with apps supporting iOS 14 and above, and can be installed with CocoaPods as of now.



## Installation
The Grassdoor Framework iOS SDK is compatible with apps supporting iOS 14 and above, and can be installed with CocoaPods or by manually integrating the framework.

Install with Cocoapods

```bash
  pod 'GrassdoorFramework', '~> 2.0'
```

Install Manually
+ Download the `GrassdoorFramework.xcframework` from Github
+ Move the framework to the Project directory
+ Next, select your application project in the Project Navigator (blue project icon) to navigate to the target configuration window and select the application target under the "Targets" heading in the sidebar.
+ In the tab bar at the top of that window, open the "General" panel.
+ Click on the + button under the "Frameworks, Libraries and Embedded Content" section.
+ Select the `GrassdoorFramework.xcframework`.
+ If framework is not available than click on "Add Others" -> "Add Files", navigate to framework & select it.
+ And that's it!

    
## Initialization
The Methods made available by the GrassdoorFramework exposes features which are available in our Grassdoor App.

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
## Show the Cart​
* Simply use this class to display the cart.
* The view has inbuild functionality for addition/deletion of products to/from cart.

```swift
SCartView
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
* This class is used to display Checkout View.
`SCheckoutView`
