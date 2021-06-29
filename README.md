# Using Truecaller SDK with your ReactNative App

## Getting started

### ReactNative//TruecallerSDK Integration guide

**STEP 1: Adding dependency**

[Add the following dependency in your app level build.gradle file]

- implementation "com.truecaller.android.sdk:truecaller-sdk:2.6.0"



**STEP 2: Setting up the key**

 - Please refer to the documentation here(https://docs.truecaller.com/truecaller-sdk/android/generating-app-key) for generating the keys. Once done please refer here(https://docs.truecaller.com/truecaller-sdk/android/integrating-with-your-app/app-key-configuration) to configure the key with your app. 



**STEP 3: Creating TruecallerAuthModule.java**

In your   “Project-Folder/Android/……../java”   folder create a new java file “TruecallerAuthModule.java” (File mentioned below).



**STEP 4: Registering the TruecallerAuthModule (TruecallerAuthPackage.java)**

Now to register the module, in the same folder, create another java file namely TruecallerAuthPackage.java (File mentioned below)
[PS: If a module is not registered it will not be available from JavaScript]


**STEP 5: Adding your package in MainApplication.java**    

The package needs to be provided in the getPackages method of the MainApplication.java file, like this: 

      -  packages.add(new TruecallerAuthPackage());


**STEP 6: Invoking TruecallerAuthentication**

Once through with the steps above, go to your JS file where you wish to invoke the truecallerSDK verification dialog and import NativeModules from react-native. 

       - import { NativeModules } from ‘react-native’;

Now create an object - TruecallerAuthModule - of Native Module. 

       - const {TruecallerAuthModule} = NativeModules; 

Now to invoke the verification dialog, simply call the authenticate() method

       - TruecallerAuthModule.authenticate();


*Note : Please refer to the documentation here(https://docs.truecaller.com/truecaller-sdk/android/integrating-with-your-app) to study the flow of the SDK once invoked. 

*Note : In order to clear the resources taken up by the SDK, you can use the method TruecallerSDK.clear(); 
        Please refer to the documentation here for more info (https://docs.truecaller.com/truecaller-sdk/android/integrating-with-your-app/clearing-sdk-instance). 




[File1] -  TruecallerAuthModule.java

    packages com.example	

    import android.app.Activity;
    import android.widget.Toast;
    import android.util.Log;
    import androidx.annotation.NonNull;
    import androidx.annotation.Nullable;
    import androidx.fragment.app.FragmentActivity;
    import android.content.Intent;
    import com.facebook.react.bridge.Arguments;
    import com.facebook.react.bridge.Promise;
    import com.facebook.react.bridge.ReactApplicationContext;
    import com.facebook.react.bridge.ReactContext;
    import com.facebook.react.bridge.ReactContextBaseJavaModule;
    import com.facebook.react.bridge.BaseActivityEventListener;
    import com.facebook.react.bridge.ActivityEventListener;
    import com.facebook.react.bridge.ReactMethod;
    import com.facebook.react.bridge.WritableMap;
    import com.facebook.react.modules.core.DeviceEventManagerModule;
    import com.truecaller.android.sdk.ITrueCallback;
    import com.truecaller.android.sdk.TrueError;
    import com.truecaller.android.sdk.TrueException;
    import com.truecaller.android.sdk.TrueProfile;
    import com.truecaller.android.sdk.TruecallerSDK;
    import com.truecaller.android.sdk.TruecallerSdkScope;
    import com.truecaller.android.sdk.clients.VerificationCallback;
    import com.truecaller.android.sdk.clients.VerificationDataBundle;
    import static com.truecaller.android.sdk.clients.VerificationDataBundle.KEY_OTP;

    public class TruecallerAuthModule extends ReactContextBaseJavaModule {

    private Promise promise = null;

    private final ITrueCallback sdkCallback = new ITrueCallback() {
      @Override
      public void onSuccessProfileShared(@NonNull final TrueProfile trueProfile) {
   
    if (promise != null) {
        WritableMap map = Arguments.createMap();
        map.putBoolean("successful", true);
        map.putString("firstName", trueProfile.firstName);
        map.putString("lastName", trueProfile.lastName);
        map.putString("phoneNumber", trueProfile.phoneNumber);
        map.putString("gender", trueProfile.gender);
        map.putString("street", trueProfile.street);
        map.putString("city", trueProfile.city);
        map.putString("zipcode", trueProfile.zipcode);
        map.putString("countryCode", trueProfile.countryCode);
        map.putString("facebookId", trueProfile.facebookId);
        map.putString("twitterId", trueProfile.twitterId);
        map.putString("email", trueProfile.email);
        map.putString("url", trueProfile.url);
        map.putString("avatarUrl", trueProfile.avatarUrl);
        map.putBoolean("isVerified", trueProfile.isTrueName);
        map.putBoolean("isAmbassador", trueProfile.isAmbassador);
        map.putString("companyName", trueProfile.companyName);
        map.putString("jobTitle", trueProfile.jobTitle);
        map.putString("payload", trueProfile.payload);
        map.putString("signature", trueProfile.signature);
        map.putString("signatureAlgorithm", trueProfile.signatureAlgorithm);
        map.putString("requestNonce", trueProfile.requestNonce);
        map.putBoolean("isBusiness",trueProfile.isBusiness);
        promise.resolve(map);
          }
    }
    @Override
    public void onFailureProfileShared(@NonNull final TrueError trueError) {
      Log.d("TruecallerAuthModule", Integer.toString(trueError.getErrorType()));
      if (promise != null) {
        String errorReason = null;
        switch (trueError.getErrorType()) {
          case TrueError.ERROR_TYPE_INTERNAL:
            errorReason = "ERROR_TYPE_INTERNAL";
            break;
          case TrueError.ERROR_TYPE_NETWORK:
            errorReason = "ERROR_TYPE_NETWORK";
            break;
          case TrueError.ERROR_TYPE_USER_DENIED:
            errorReason = "ERROR_TYPE_USER_DENIED";
            break;
          case TrueError.ERROR_PROFILE_NOT_FOUND:
            errorReason = "ERROR_TYPE_UNAUTHORIZED_PARTNER";
            break;
          case TrueError.ERROR_TYPE_UNAUTHORIZED_USER:
            errorReason = "ERROR_TYPE_UNAUTHORIZED_USER";
            break;
          case TrueError.ERROR_TYPE_TRUECALLER_CLOSED_UNEXPECTEDLY:
            errorReason = "ERROR_TYPE_TRUECALLER_CLOSED_UNEXPECTEDLY";
            break;
          case TrueError.ERROR_TYPE_TRUESDK_TOO_OLD:
            errorReason = "ERROR_TYPE_TRUESDK_TOO_OLD";
            break;
          case TrueError.ERROR_TYPE_POSSIBLE_REQ_CODE_COLLISION:
            errorReason = "ERROR_TYPE_POSSIBLE_REQ_CODE_COLLISION";
            break;
          case TrueError.ERROR_TYPE_RESPONSE_SIGNATURE_MISMATCH:
            errorReason = "ERROR_TYPE_RESPONSE_SIGNATURE_MISSMATCH";
            break;
          case TrueError.ERROR_TYPE_REQUEST_NONCE_MISMATCH:
            errorReason = "ERROR_TYPE_REQUEST_NONCE_MISSMATCH";
            break;
          case TrueError.ERROR_TYPE_INVALID_ACCOUNT_STATE:
            errorReason = "ERROR_TYPE_INVALID_ACCOUNT_STATE";
            break;
          case TrueError.ERROR_TYPE_TC_NOT_INSTALLED:
            errorReason = "ERROR_TYPE_TC_NOT_INSTALLED";
            break;
          case TrueError.ERROR_TYPE_ACTIVITY_NOT_FOUND:
            errorReason = "ERROR_TYPE_ACTIVITY_NOT_FOUND";
            break;
        }
        WritableMap map = Arguments.createMap();
        map.putString("error", errorReason != null ? errorReason : "ERROR_TYPE_NULL");
        promise.resolve(map);
      }
    }
    @Override
    public void onVerificationRequired(TrueError trueError) {
    //The statement below can be ignored incase of one-tap flow integration
      TruecallerSDK.getInstance().requestVerification("IN", "PHONE-NUMBER-STRING", apiCallback,(FragmentActivity) getCurrentActivity());
      }
    };
  

    //Callback below can be ignored incase of one-tap only integration 

    final VerificationCallback apiCallback = new VerificationCallback() {
        @Override
    public void onRequestSuccess(int requestCode, @Nullable VerificationDataBundle extras) {
    if (requestCode == VerificationCallback.TYPE_MISSED_CALL_INITIATED) {
    
    //Retrieving the TTL for missedcall 
        if(extras != null){
             extras.getString(VerificationDataBundle.KEY_TTL); 
        }
    }
    if (requestCode == VerificationCallback.TYPE_MISSED_CALL_RECEIVED) {
                  
        TrueProfile profile = new TrueProfile.Builder("USER-FIRST-NAME","USER-LAST-NAME").build();
        TruecallerSDK.getInstance().verifyMissedCall(profile, apiCallback);
      }
    if (requestCode == VerificationCallback.TYPE_OTP_INITIATED) {
    
    //Retrieving the TTL for otp 
        if(extras != null){
             extras.getString(VerificationDataBundle.KEY_TTL); 
         }  
      }
    if (requestCode == VerificationCallback.TYPE_OTP_RECEIVED) {
        TrueProfile profile = new TrueProfile.Builder("USER-FIRST-NAME","USER-LAST-NAME").build();
        TruecallerSDK.getInstance().verifyOtp(profile, "OTP-ENTERED-BY-THE-USER", apiCallback);
      }
      if (requestCode == VerificationCallback.TYPE_VERIFICATION_COMPLETE) {
     }
      if (requestCode == VerificationCallback.TYPE_PROFILE_VERIFIED_BEFORE) {
      }
    }
    @Override
    public void onRequestFailure(final int requestCode, @NonNull final TrueException e) {
      //Write the Exception Part
        }  
    };

    TruecallerAuthModule(ReactApplicationContext reactContext) {
       super(reactContext);
      TruecallerSdkScope trueScope = new TruecallerSdkScope.Builder(reactContext, sdkCallback)
         .consentMode(TruecallerSdkScope.CONSENT_MODE_BOTTOMSHEET)
        .buttonColor(Color.parseColor("COLOUR-STRING/HEX-CODE"))
        .buttonTextColor(Color.parseColor("COLOUR-STRING/HEX-CODE"))
        .loginTextPrefix(TruecallerSdkScope.LOGIN_TEXT_PREFIX_TO_GET_STARTED)
        .loginTextSuffix(TruecallerSdkScope.LOGIN_TEXT_SUFFIX_PLEASE_VERIFY_MOBILE_NO)
        .ctaTextPrefix(TruecallerSdkScope.CTA_TEXT_PREFIX_USE)
        .buttonShapeOptions(TruecallerSdkScope.BUTTON_SHAPE_ROUNDED)
        .privacyPolicyUrl("<YOUR-PRIVACY-POLICY-URL>")
        .termsOfServiceUrl("<YOUR-TERMS-OF-SERVICE-URL>")
        .footerType(TruecallerSdkScope.FOOTER_TYPE_NONE)
        .consentTitleOption(TruecallerSdkScope.SDK_CONSENT_TITLE_LOG_IN)
        .sdkOptions(TruecallerSdkScope.SDK_OPTION_WITHOUT_OTP)
        .build();
      TruecallerSDK.init(trueScope);
     reactContext.addActivityEventListener(mActivityEventListener);  
    }

    @Override
    public String getName() {
        return "TruecallerAuthModule";
    }

    @ReactMethod
    public void showToast(Promise promise) {
         Toast.makeText(getReactApplicationContext(), "hello", Toast.LENGTH_LONG).show();
         promise.resolve(null);
    }

    @ReactMethod
    public void authenticate(Promise promise) {
        try {
             this.promise = promise;
               if (TruecallerSDK.getInstance() != null) {
                    if(TruecallerSDK.getInstance().isUsable()){
                    TruecallerSDK.getInstance().getUserProfile((FragmentActivity) getCurrentActivity());
                    }
      //For One-Tap implementation : The isUsable method would return true incase where the truecaller app is installed and logged in else it will return false. 
      //For Full-Stack implementation : The isUsable method would always return true as now the SDK can be used to verify both truecaller and non-truecaller users
                    
               } else {
                      WritableMap map = Arguments.createMap();
                      map.putString("error", "ERROR_TYPE_NOT_SUPPORTED");
                      this.promise.resolve(map);
              }
          } catch (Exception e) {
                  this.promise.reject(e);
               }
           }
           
      @ReactMethod 
      public void SDKClear(){
      TruecallerSDK.clear();
      }

     private final ActivityEventListener mActivityEventListener = new BaseActivityEventListener() {
        @Override
        public void onActivityResult(Activity activity, int requestCode, int resultCode, Intent intent) {
            super.onActivityResult(activity, requestCode, resultCode, intent);
        
        if (requestCode == TruecallerSDK.SHARE_PROFILE_REQUEST_CODE) {
             TruecallerSDK.getInstance().onActivityResultObtained((FragmentActivity)activity, requestCode, resultCode, intent);
            }
          }
        };   
      }




[File2]  - TruecallerAuthPackage.java


     package com.example;

    import com.facebook.react.ReactPackage;
    import com.facebook.react.bridge.NativeModule;
    import com.facebook.react.bridge.ReactApplicationContext;
    import com.facebook.react.uimanager.ViewManager;
    import java.util.ArrayList;
    import java.util.Collections;
    import java.util.List;
    
    public class TruecallerAuthPackage implements ReactPackage {
      @Override
      public List<ViewManager> createViewManagers(ReactApplicationContext reactContext) {
          return Collections.emptyList();
      }
      @Override
      public List<NativeModule> createNativeModules(ReactApplicationContext reactContext) {
          List<NativeModule> nativeModules = new ArrayList<>();
          nativeModules.add(new TruecallerAuthModule(reactContext));
          return nativeModules;
        }
      }
