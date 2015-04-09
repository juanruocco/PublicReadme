Authy OneTouch iOS SDK
=========

## Project Structure

The project is structured as follows:

    ./
	  |-Examples
   	  |---AuthyOneTouchExample
   	  |-Library

1. **AuthyOneTouchExample**: Provides a sample of Authy OneTouch. If you want to get started with the Authy OneTouch the best way is by taking a look at the sample project.
2. **Library**: Contains the actual Authy OneTouch library code.

## Installation Instructions with library
1. AuthyOneTouch requires access to the camera, so you have to make sure you allow it through your cellphone Settings, otherwise you will not be able to scan QR codes.
2. To add the library to your project, copy the folder 'include' and the file 'libAuthyOneTouchLibrary.a' into your project folder directly from XCode. This files can be found in the 'Library' folder.
3. Required frameworks and libraries. Add the following frameworks and libraries in 'Linked Frameworks and Libraries' under the 'General' tab of your project
    - libAuthyOneTouchLibrary.a (this library should have already been included automatically from the previous step)
    - AVFoundation.framework
    - libstdc++.dylib
    - SystemConfiguration.framework
    - libz.dylib
    - MobileCoreServices.framework

## How import Authy OneTouch Classes
With library use #import "AuthyOneTouch/Class.h"

## Getting Started
This is a short tutorial that will help you getting started with `AuthyOneTouch`. There is one sample project provided that you can checkout to get you started with AuthyOneTouch. Follow the next steps in order to sign `APApprovalRequest`s.

1. Configure the Scanner.
2. Configure the `AuthyOneTouch` instance.
3. Fetch all accounts
4. Fetch a list of requests.
5. Approve/deny a request.

## Configure the QR Code Scanner
AuthyOneTouch requires that you can QR codes.

**Remember** that in the class definition your controller should import `AOScannerController.h` and adopt the protocol `AOScannerDelegate`

1. To create QR code scanner simply present the AOScannerController as follows:

    ```objectiveC
    - (void)showScannerController {
        AOScannerController *scanner = [[AOScanner alloc] initWithDelegate:self];
        [self presentViewController:scanner animated:NO completion:nil];
    }
    ```

2. When the QR code has been scanned, the following method is called. This is a sample implementation

    ```objectiveC
    // Authy OneTouch Scanner Delegate
    -(void)scannerController:(AOScanner *)scanner didScanAccount:(AOCustomerAccount *) custoAccountmer{
        if(!customerAccount) {
            // The scanner don't recognize as Authy OneTouch account
        } else {
            // You have successfully scanned a QR Code
            // The QR code has been scanned and is ready to be used using the AOAccount account
            // param in the loadWithAccount method to register the account.
            // TODO: Load the AuthyOneTouch instance with AOAccount
    }

    [scanner dismissViewControllerAnimated:YES completion:nil];
    }

    -(void)scannerControllerDidCancel:(AOScanner *)scanner {
        // The scanner has been stopped by user
        [scanner dismissViewControllerAnimated:NO completion:nil];
    }
    ```

### Configure the AuthyOneTouch instance
Now, you will need an instance of `AuthyOneTouch`, this is the main class you will be using. The `AuthyOneTouch` class provides methods for fetching the list of pending `AOApprovalRequest`s as well as methods to aprove/deny `Approval Request`s.

**Remember** that in the class definition your controller should import `AuthyOneTouch.h`

1. Create an instance of `AuthyOneTouch`

    ```objectiveC
    AuthyOneTouch *authyOneTouchDefault = [AuthyOneTouch authyOneTouchDefault];
    ```

  The authyOneTouchDefault provides a thread-safe singleton instance of AuthyOneTouch, so several calls to `[AuthyOneTouch authyOneTouchDefault]` will return the same instance.

2. Setup the api sdk key

   ```objectiveC
    authyOneTouchDefault.apiKey = @"SDK_API_KEY";
    ```

3. Load the `AuthyOneTouch` instance. Before you can actually use `AuthyOneTouch` you will have to *load* the instance. Loading is as simple as:

    ```objectiveC
    [authyOneTouchDefault registerCustomer:customerAccount handler:^(AOError *error){
    if(!error) {
        // the account has been successfully loaded into the AuthyOneTouch object.
        // After this step, you can now use the other methods in the AuthyOneTouch class
    }else {
        //An error has occurred, prompt the user to scan the token again.
    }
    }];
    ```

**Warning** bear in mind that before calling methods from an `AuthyOneTouch` instance such as the ones below you will first need to load the instance. XCode will not warn you nor an exception will be thrown if the `AuthyOneTouch` instance has not been loaded.

### Fetch all accounts
You can obtain a list of all your accounts by simply calling the `getCustomerAccounts` method as follows:

```objectiveC
NSArray *accounts = [authyOneTouchDefault getCustomerAccounts];
```
The array obtained contains `AOCustomerAccount` objects. You can see its properties directly from the `AOCustomerAccount.h` file included inside the 'include' folder added earlier.

### Fetch a list of requests
You can fetch a list of approval request per customer account.

To obtain the list of all requests (pending, accepted, denied or all) simply call the `getApprovalRequests: ofCustomer: handler:` method as follows:

  ```objectiveC
  [authyOneTouchDefault getApprovalRequests:(AOApprovalRequestStatus)status ofCustomer:(AOCustomerAccount *)customerAccount handler:^(NSArray *array, AOError *error){
    if(!error){
      //Requests have been successfully fetched.
    } else {
      //An error has occurred, prompt the error.
    }
  }];
  ```

  To obtain the list of all, pending, accepted or denied requests, simply call this method with the respectively status: AOApprovalRequestStatusAll, AOApprovalRequestStatusPending, AOApprovalRequestStatusApprove or AOApprovalRequestStatusDeny.


**Remember** the array of requests contains `AOApprovalRequest` objects. You can see its properties directly from the `AOApprovalRequest.h` file included inside the 'include' folder added earlier.

### Approve/deny a request
To approve or deny a request simply call the `updateApprovalRequest: status: handler` methods as follow

```objectiveC
[authyOneTouchDefault updateApprovalRequest:(AOApprovalRequest *)approvalRequest
                       status:(AOApprovalRequestStatus)newStatus
                      handler::^(AOError *error){
  if(error){
    //An error has occurred, prompt the error.
  } else {
    //The request has been approved.
  }
}];
```
