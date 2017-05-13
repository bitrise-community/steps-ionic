# [Bitrise] Ionic Step

With this step you can build ionic apps easily.
Select your environment things and start building.

On our GitHub page you can see complete Bitrise examples for iOS and Android.

If you have questions feel free to ask us in english and german language.
We will help you as soon as possible ;-)

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
