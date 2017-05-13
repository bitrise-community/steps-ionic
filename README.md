# [Bitrise] Ionic Step

With this step you can build ionic apps easily.
Select your environment things and start building.

If you have questions feel free to ask us in english and german language.
We will help you as soon as possible ;-)

## Build the ionic conference app from scratch

Here you will be informed how you can build the ionic conference app from scratch.
I mean you fork the app and you register on Bitrise and then you can complete the build easily.
You can test the other examples, too.

### Android

#### Prepare

For Android we need a keystore.

    $ keytool -genkeypair -dname "cn=Ionic Conference App, ou=Sample, o=Sample, c=DE" -alias KEYSTORE_ALIAS -keypass PRIVATE_KEY_PASSWORD -keystore android.keystore -storepass KEYSTORE_PASSWORD -validity 9999

This keystore you upload to the Bitrise `Code Signing` tab as `Android Keystore file`.
As `Keystore password` we used `KEYSTORE_PASSWORD` in the command.
The `Alias` is `KEYSTORE_ALIAS`.
And as `Private key password` we used `PRIVATE_KEY_PASSWORD`.

#### Build

Copy the bitrise.yml in the workflow editor in bitrise.yml tab.

**Dont forget to change the trigger from ios to android!**

Then you can start your build and if everything is set up correct you will get a successfully signed APK which is ready for the Google Play Store :-)

### iOS

#### Prepare

For iOS we need a bit more time. Currently our team is working on a platform where you can generate everything really quick and set up the following things in few minutes.

##### 1. iOS Certificate

https://developer.apple.com/account/ios/certificate/

We need a certificate for `iOS App Development` or if we want to publish to App Store we need a `App Store and Ad Hoc` certificate.
You will only need one certificate for you or for your organization. So if you already have a certificate you can use it in the second step.

At the end you have 1 x `iOS App Development` per person and 1 x `App Store and Ad Hoc` for your whole organization.

    $ openssl req -out ios_development.csr -new -newkey rsa:2048 -nodes -keyout ios_development.key

You will be asked for some things.
**Example:**

    Country Name (2 letter code) [AU]: DE
    State or Province Name (full name) [Some-State]: Niedersachsen
    Locality Name (eg, city) []: Hannover
    Organization Name (eg, company) [Internet Widgits Pty Ltd]: Test Org
    Organizational Unit Name (eg, section) []:
    Common Name (e.g. server FQDN or YOUR name) []: My name or my organization
    Email Address []: info@example.org

    Please enter the following 'extra' attributes
    to be sent with your certificate request
    A challenge password []:
    An optional company name []:

Then you will receive a `ios_development.cer` which you must download.

Convert it to `pem` format because we will need it later.

    $ openssl x509 -inform der -in ios_development.cer -out ios_development.pem

Then we need the subject to set a correct alias for the Bitrise certificate tool.

    $ openssl x509 -noout -subject -in ios_development.pem

This will give you a result like this.

    subject= /UID=12AB3456C7/CN=iPhone Developer: Patrick (A12B345C6D)/OU=1A234BCD56/O=Your company or your name if exist/C=DE

You need only the words after `CN=` until `/` in the above example it will be: `iPhone Developer: Patrick (A12B345C6D)`

This is the last step for later use on Bitrise. Convert it to `p12` format and set a password.

    $ openssl pkcs12 -export -in ios_development.pem -inkey ios_development.key -out ios_development.p12 -name "iPhone Developer: Patrick (A12B345C6D)"

For demo reasons I'm using `my-ios-development-p12-password` as password.

This `ios_development.*` files you can copy to your personal folder or to your business folder.
You should use this file(s) for all your apps. There are some limits so you are not able to generate unlimited certificates.

##### 2. iOS App ID

https://developer.apple.com/account/ios/identifier/bundle

This steps you need for each app you will develop. (except you use wildcards ;-))

I will use `Ionic Conference App` as `App ID Description` and `io.ionic.conferenceapp` as `Explicit App ID`. (its not valid but good enough for example :-D real id: de.ionic.conferenceapp)
And for later example of push notification certificate generation I will add the `Push Notifications` feature at the bottom of the page.
Please check all checkboxes you need. Most popular for example is the `Push Notifications` or `Associated Domains` feature.

**Please ensure that you use the `id` of your `config.xml` as `Explicit App ID`!**

No panic. You can delete the app id later. But please don't add this example to `iTunes Connect`. Because there you can't delete them.

##### 3. iOS Provisioning Profile

https://developer.apple.com/account/ios/profile/

We need a provisioning profile.
Here you can choose for example between `iOS App Development`, `App Store` and `Ad Hoc`.

I will use `iOS App Development` in this example because I want to use the app on the iPhone of my and my friends ;-)

In the second step you select the App ID you created in step 2.
And in step three you select the certificate you created in step 1. (normally you have only one certificate but maybe you created some via XCode automatic signing or with some other tools)

Then I must select the devices for this provisioning profile. If you selected something else you are finished now.

The name of the provisioning profile in my example will be `bitrise test`.

You should save the downloaded `bitrise_test.mobileprovision` in your local project folder. But please add it to gitignore!

##### 4. Upload files to Bitrise

You upload the in step 1. generated `p12` file and the `mobileprovision` file of step 3. to the Bitrise `Code Signing`.
The `p12` is your `Code Signing Identity` and the `mobileprovision` is your `Provisioning profile`.

On the `Code Signing Identity` you should enter the password which you set in the first step.

#### Build

Copy the [bitrise.yml](https://github.com/bussmann-it/ionic-conference-app/raw/master/bitrise.yml) to the workflow editor in bitrise.yml tab.

Then fill in your personal variables.
You should open the `mobileprovision` file with `nano` or text editor.

- `BITRISE_SCHEME` should be the name of your `config.xml`
- `force_team_id` should be your `TeamIdentifier` of the `mobileprovision` file
- `force_code_sign_identity` should be `iPhone Developer` if you selected `iOS Development` in the first step or `iPhone Distribution` if you selected `iOS Distribution`
- `force_provisioning_profile` should be your `UUID` of the `mobileprovision` file

Then you can start your build and if everything is set up correct you will get a successfully signed IPA which is ready for the App Store and your (test) devices ;-)

## How to use this Step

Reference it in your `bitrise.yml`:

```
- git::https://github.com/bussmann-it/steps-ionic.git@master:
    title: Ionic Build
    inputs:
    - build_for_platform: "browser"
    - cordova_version: "7"
    - ionic_version: "3"
    - ios_provisioning_style: "Manual"
    - build_parameters: "--release --prod"
```

## How to contribute to this Step

1. Fork this repository
2. `git clone` it
3. Create a branch you'll work on
4. To use/test the step just follow the **How to use this Step** section
5. Do the changes you want to
6. Run/test the step before sending your contribution
  * You can also test the step in your `bitrise` project, either on your Mac or on [bitrise.io](https://www.bitrise.io)
  * You just have to replace the step ID in your project's `bitrise.yml` with either a relative path, or with a git URL format
  * (relative) path format: instead of `- original-step-id:` use `- path::./relative/path/of/script/on/your/Mac:`
  * direct git URL format: instead of `- original-step-id:` use `- git::https://github.com/user/step.git@branch:`
  * You can find more example of alternative step referencing at: https://github.com/bitrise-io/bitrise/blob/master/_examples/tutorials/steps-and-workflows/bitrise.yml
7. Once you're done just commit your changes & create a Pull Request
