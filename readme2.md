Authy OneTouch iOS SDK
=========

## Project Structure

The project is structured as follows:

    ./
	  |-Documentation
	  |-Examples
   	  |---AuthyOneTouchExample
   	  |-Library

1. **AuthyOneTouchExample**: Provides a sample of Authy OneTouch. If you want to get started with the Authy OneTouch the best way is by taking a look at the sample project.
2. **Library**: Contains the actual Authy OneTouch library code.

## Installation Instructions with library
1. AuthyOneTouch requires access to the camera, so you have to make sure you allow it through your cellphone Settings, otherwise you will not be able to scan QR codes.
2. To add the library to your project, copy the folder 'include' and the file 'libAuthyOneTouchLibrary.a' into your project folder directly from XCode. This files can be found in the 'Library' folder.
3. Ensure that the attribute 'Header Search Paths' in 'Build Settings' will be referenced to 'include' folder of Authy OneTouchLibrary
4. Ensure that the attribute 'Library Search Paths' in 'Build Settings' will be referenced to folder of 'libAuthyOneTouchLibrary.a'
5. Required frameworks and libraries. Add the following frameworks and libraries in 'Linked Frameworks and Libraries' under the 'General' tab of your project
    - libAuthyOneTouchLibrary.a (this library should have already been included automatically from the previous step)
    - AVFoundation.framework (provides essential services for working with time-based audiovisual media)
    - libstdc++.dylib
    - SystemConfiguration.framework (provides powerful, flexible support for establishing and maintaining access to configurable network and system resources.)
    - libz.dylib
    - MobileCoreServices.framework (provides the foundation for Apple's Unfirform Type Identifiers mechanism, a system for specifying and identifying data types)

## How import Authy OneTouch Classes
With library use #import "AuthyOneTouch/Class.h"

## Getting Started
This is a short tutorial that will help you getting started with `AuthyOneTouch`. There is one sample project provided that you can checkout to get you started with AuthyOneTouch. Follow the next steps in order to sign `AOApprovalRequest`s.

1. Configure the Scanner.
2. Configure the `AuthyOneTouch` instance.
3. Fetch all accounts
4. Fetch a list of requests.
5. Approve/deny a request.

## Configure the QR Code Scanner
AuthyOneTouch requires that you can QR codes.

**Remember** that in the class definition your controller should import `AOScannerController.h` and adopt the protocol `AOScannerDelegate`

**Notice** In the web app http://authy-demos-sandbox.herokuapp.com/one-touch-qr-code will be a QR Code example

1. To create QR code scanner simply present the AOScannerController as follows:

    ```objectiveC
    - (void)showScannerController {
        AOScannerController *scanner = [[AOScannerController alloc] initWithDelegate:self];
        [self presentViewController:scanner animated:NO completion:nil];
    }
    ```

2. When the QR code has been scanned, the following method is called. This is a sample implementation

    ```objectiveC
    // Authy OneTouch Scanner Delegate
    -(void)scannerController:(AOScannerController *)scanner didScanCustomerAccount:(AOCustomerAccount *) customerAccount{
        if(customerAccount) {
          // You have successfully scanned a QR Code
          // The QR code has been scanned and created a customer account
          // TODO: Register the customer account in the Authy OneTouch server
        } else {
          // The scanner did not recognize as customer account of Authy OneTouch
        }

    [scanner dismissViewControllerAnimated:NO completion:nil];
    }

    -(void)scannerControllerDidCancel:(AOScannerController *)scanner {
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

2. Setup the api sdk key(the SDK_API_KEY of web app demo, http://authy-demos-sandbox.herokuapp.com/one-touch-qr-code, is "NITLFDAS21tUW87YVAd5D7nQ8D3pA-ACulaDLL2MKDg" )

   ```objectiveC
    authyOneTouchDefault.apiKey = @"SDK_API_KEY";
    ```

3. Load the `AuthyOneTouch` instance. Before you can actually use `AuthyOneTouch` you will have to *load* the instance. Loading is as simple as:

    ```objectiveC
    [authyOneTouchDefault registerCustomer:customerAccount handler:^(AOError *error){
    if(!error) {
        // The account has been successfully registered into the AuthyOneTouch server.
        // After this step, you can use this account to get pending requests and approve/deny it
    }else {
        //An error has occurred, prompt the user to scan the token again.
    }
    }];
    ```

**Notice** Please take into account that this method generates the public and private key therefore it will take a few seconds to complete

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
                      handler:^(AOError *error){
  if(!error){
    //The request has been approved/denied.
  } else {
    //An error has occurred, prompt the error.
  }
}];
```
